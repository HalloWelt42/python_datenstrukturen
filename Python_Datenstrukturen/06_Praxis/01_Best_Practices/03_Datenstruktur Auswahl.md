---
tags: [python, datenstrukturen, best-practices, performance]
created: 2025-10-02
---

← [[06_Praxis/01_Best_Practices/02_Code Smells vermeiden|Code Smells vermeiden]] | [[INDEX|📑 Index]] | [[06_Praxis/02_Performance/01_Performance Messung|Performance Messung]] →

# Datenstruktur Auswahl

> [!tip] Lernziel
> Du kannst die richtige Datenstruktur für verschiedene Anwendungsfälle auswählen und kennst die Performance-Charakteristiken.

## Entscheidungsbaum

```
Brauchst du geordnete Elemente?
├─ Ja: Brauchen Elemente Keys/Werte?
│  ├─ Ja: dict (geordnet ab Python 3.7+)
│  └─ Nein: list oder tuple
│     ├─ Veränderbar? → list
│     └─ Unveränderbar? → tuple
│
└─ Nein: Brauchst du eindeutige Elemente?
   ├─ Ja: set oder frozenset
   │  ├─ Veränderbar? → set
   │  └─ Unveränderbar? → frozenset
   └─ Nein: Membership-Tests wichtig?
      ├─ Ja: set (O(1) lookup)
      └─ Nein: list
```

## Listen (list)

### Wann verwenden

**✅ Gut für:**
- Geordnete Sammlung veränderlicher Größe
- Häufiges Anfügen am Ende
- Index-basierter Zugriff
- Iteration über alle Elemente
- Slicing-Operationen

**❌ Nicht gut für:**
- Häufige Membership-Tests (`x in list`)
- Einfügen/Löschen am Anfang
- Eindeutige Elemente garantieren
- Unveränderliche Daten

### Performance

```python
# O(1) - Konstante Zeit
lst = [1, 2, 3, 4, 5]
lst.append(6)          # Am Ende anfügen
lst.pop()              # Letztes entfernen
x = lst[2]             # Index-Zugriff
len(lst)               # Länge

# O(n) - Lineare Zeit
3 in lst               # Membership-Test
lst.insert(0, 0)       # Am Anfang einfügen
lst.pop(0)             # Erstes entfernen
lst.remove(3)          # Element entfernen
lst.index(3)           # Element finden

# Beispiel: Schlechte Verwendung
def has_duplicates_bad(items):
    """O(n²) - sehr langsam!"""
    seen = []
    for item in items:
        if item in seen:  # O(n) Lookup!
            return True
        seen.append(item)
    return False

# Besser: Set verwenden
def has_duplicates_good(items):
    """O(n) - viel schneller"""
    seen = set()
    for item in items:
        if item in seen:  # O(1) Lookup!
            return True
        seen.add(item)
    return False
```

### Anwendungsfälle

```python
# Stack (LIFO) - Liste ist perfekt
stack = []
stack.append(1)  # Push
stack.append(2)
top = stack.pop()  # Pop - 2

# Todo-Liste mit Reihenfolge
todos = ["Task 1", "Task 2", "Task 3"]
todos.append("Task 4")
completed = todos.pop(0)

# Sliding Window
data = [1, 2, 3, 4, 5, 6, 7, 8, 9]
window_size = 3
for i in range(len(data) - window_size + 1):
    window = data[i:i + window_size]
    print(window)
```

## Tupel (tuple)

### Wann verwenden

**✅ Gut für:**
- Unveränderliche Daten
- Heterogene Daten (verschiedene Typen)
- Dictionary Keys
- Funktions-Returns mit mehreren Werten
- Performance (weniger Memory als Liste)

**❌ Nicht gut für:**
- Daten die sich ändern müssen
- Große Datenmengen die wachsen

### Performance

```python
# Tupel sind schneller zu erstellen
import timeit

# Tuple schneller
timeit.timeit("(1, 2, 3, 4, 5)", number=1000000)  # ~0.015s
# Liste langsamer
timeit.timeit("[1, 2, 3, 4, 5]", number=1000000)  # ~0.025s

# Weniger Memory
import sys
sys.getsizeof((1, 2, 3, 4, 5))  # 64 bytes
sys.getsizeof([1, 2, 3, 4, 5])  # 88 bytes
```

### Anwendungsfälle

```python
# Koordinaten (unveränderlich)
point = (10, 20)
x, y = point

# Funktions-Return
def get_user_info():
    return ("Max", 30, "max@example.com")

name, age, email = get_user_info()

# Dictionary Key (Liste geht nicht!)
locations = {
    (52.52, 13.40): "Berlin",
    (51.51, -0.13): "London"
}

# Named Tuple für strukturierte Daten
from typing import NamedTuple

class Point(NamedTuple):
    x: float
    y: float

p = Point(10, 20)
print(p.x, p.y)  # Lesbar!
```

## Sets (set)

### Wann verwenden

**✅ Gut für:**
- Eindeutige Elemente
- Sehr schnelle Membership-Tests (O(1))
- Mengenoperationen (Union, Intersection)
- Duplikate entfernen

**❌ Nicht gut für:**
- Geordnete Daten (keine Garantie)
- Index-basierter Zugriff
- Unhashbare Elemente (Listen, Dicts)

### Performance

```python
# Set vs. Liste für Membership
import timeit

items_list = list(range(10000))
items_set = set(range(10000))

# Liste: O(n)
timeit.timeit("5000 in items_list", globals=globals(), number=10000)
# ~2.5s

# Set: O(1)
timeit.timeit("5000 in items_set", globals=globals(), number=10000)
# ~0.001s - 2500x schneller!
```

### Anwendungsfälle

```python
# Duplikate entfernen
numbers = [1, 2, 2, 3, 4, 4, 5]
unique = list(set(numbers))  # [1, 2, 3, 4, 5]

# Membership-Tests
valid_users = {"alice", "bob", "charlie"}
if username in valid_users:  # O(1)
    grant_access()

# Mengenoperationen
set1 = {1, 2, 3, 4}
set2 = {3, 4, 5, 6}

union = set1 | set2           # {1, 2, 3, 4, 5, 6}
intersection = set1 & set2     # {3, 4}
difference = set1 - set2       # {1, 2}
symmetric_diff = set1 ^ set2   # {1, 2, 5, 6}

# Gemeinsame Elemente finden
users_with_permission_a = {"alice", "bob", "charlie"}
users_with_permission_b = {"bob", "charlie", "david"}

# Benutzer mit beiden Permissions
both = users_with_permission_a & users_with_permission_b
# {"bob", "charlie"}
```

## Dictionaries (dict)

### Wann verwenden

**✅ Gut für:**
- Key-Value Zuordnungen
- Sehr schneller Lookup nach Key (O(1))
- Caching/Memoization
- Zählen/Gruppieren
- Konfigurationen

**❌ Nicht gut für:**
- Wenn Reihenfolge essentiell ist (vor 3.7)
- Memory-kritische Anwendungen
- Unhashbare Keys

### Performance

```python
# Dict Operationen
d = {"a": 1, "b": 2, "c": 3}

# O(1) Operationen
d["a"]              # Zugriff
d["d"] = 4          # Setzen
"a" in d            # Membership
d.pop("b")          # Löschen
len(d)              # Länge

# O(n) Operationen
list(d.keys())      # Alle Keys
list(d.values())    # Alle Values
list(d.items())     # Alle Paare
```

### Anwendungsfälle

```python
# Caching/Memoization
cache = {}

def expensive_function(n):
    if n in cache:
        return cache[n]
    
    # Teure Berechnung
    result = compute(n)
    cache[n] = result
    return result

# Zählen
from collections import Counter

words = ["apple", "banana", "apple", "cherry", "banana"]
counts = Counter(words)
# Counter({"apple": 2, "banana": 2, "cherry": 1})

# Gruppieren
from collections import defaultdict

users = [
    {"name": "Alice", "age": 25},
    {"name": "Bob", "age": 30},
    {"name": "Charlie", "age": 25}
]

by_age = defaultdict(list)
for user in users:
    by_age[user["age"]].append(user["name"])

# {25: ["Alice", "Charlie"], 30: ["Bob"]}

# Lookup-Tabelle
STATUS_MESSAGES = {
    200: "OK",
    404: "Not Found",
    500: "Internal Server Error"
}

message = STATUS_MESSAGES.get(status_code, "Unknown")
```

## Deque (collections.deque)

### Wann verwenden

**✅ Gut für:**
- Queue (FIFO)
- Einfügen/Löschen an beiden Enden
- Sliding Window mit fester Größe
- Undo/Redo mit Limit

**❌ Nicht gut für:**
- Index-Zugriff in der Mitte (O(n))
- Slicing
- Wenn nur Stack gebraucht wird (Liste reicht)

### Performance

```python
from collections import deque

# Liste vs. Deque für Queue-Operationen
import timeit

# Liste - langsam bei insert(0)
lst = list(range(1000))
timeit.timeit("lst.insert(0, 1)", globals=globals(), number=10000)
# ~0.8s - O(n)

# Deque - schnell an beiden Enden
dq = deque(range(1000))
timeit.timeit("dq.appendleft(1)", globals=globals(), number=10000)
# ~0.001s - O(1)
```

### Anwendungsfälle

```python
from collections import deque

# Queue (FIFO)
queue = deque()
queue.append(1)      # Enqueue
queue.append(2)
first = queue.popleft()  # Dequeue - 1

# Sliding Window mit maxlen
window = deque(maxlen=3)
for i in range(10):
    window.append(i)
    print(list(window))
# [0], [0, 1], [0, 1, 2], [1, 2, 3], [2, 3, 4], ...

# Undo/Redo mit Limit
history = deque(maxlen=10)  # Max 10 Actions
history.append("action1")
history.append("action2")
last = history.pop()  # Undo

# Circular Buffer
buffer = deque(maxlen=5)
for i in range(10):
    buffer.append(i)
print(buffer)  # deque([5, 6, 7, 8, 9])
```

## DefaultDict & Counter

### DefaultDict

**Wann verwenden:**
- Automatische Initialisierung von Keys
- Gruppieren ohne explizite Checks
- Counting, Collecting

```python
from collections import defaultdict

# Ohne defaultdict - umständlich
groups = {}
for item in items:
    key = item.category
    if key not in groups:
        groups[key] = []
    groups[key].append(item)

# Mit defaultdict - elegant
groups = defaultdict(list)
for item in items:
    groups[item.category].append(item)

# Verschiedene Default-Typen
counts = defaultdict(int)    # Default: 0
lists = defaultdict(list)    # Default: []
sets = defaultdict(set)      # Default: set()
dicts = defaultdict(dict)    # Default: {}

# Graph als Adjacency List
graph = defaultdict(list)
graph["A"].append("B")
graph["A"].append("C")
graph["B"].append("D")
# {"A": ["B", "C"], "B": ["D"]}
```

### Counter

**Wann verwenden:**
- Zählen von Elementen
- Häufigkeitsanalyse
- Most common Elemente finden

```python
from collections import Counter

# Buchstaben zählen
text = "hello world"
counts = Counter(text)
# Counter({"l": 3, "o": 2, "h": 1, ...})

# Most common
counts.most_common(3)
# [("l", 3), ("o", 2), ("h", 1)]

# Arithmetik mit Counters
c1 = Counter(["a", "b", "c", "a"])
c2 = Counter(["a", "b", "d"])

c1 + c2  # Addition
# Counter({"a": 3, "b": 2, "c": 1, "d": 1})

c1 - c2  # Subtraktion
# Counter({"a": 1, "c": 1})

# Anwendung: Word Frequency
words = text.lower().split()
word_freq = Counter(words)
print(word_freq.most_common(10))
```

## Heap (heapq)

### Wann verwenden

**✅ Gut für:**
- Priority Queue
- K kleinste/größte Elemente finden
- Event Scheduling
- Dijkstra's Algorithm

**❌ Nicht gut für:**
- Lookup von beliebigen Elementen
- Wenn komplette Sortierung nötig

```python
import heapq

# Min-Heap (kleinster Wert oben)
heap = []
heapq.heappush(heap, 5)
heapq.heappush(heap, 3)
heapq.heappush(heap, 7)
heapq.heappush(heap, 1)

smallest = heapq.heappop(heap)  # 1

# K kleinste Elemente - O(n log k)
numbers = [10, 3, 5, 1, 8, 2, 9]
k_smallest = heapq.nsmallest(3, numbers)  # [1, 2, 3]
k_largest = heapq.nlargest(3, numbers)    # [10, 9, 8]

# Priority Queue
from dataclasses import dataclass, field
from typing import Any

@dataclass(order=True)
class Task:
    priority: int
    name: str = field(compare=False)

tasks = []
heapq.heappush(tasks, Task(3, "Low priority"))
heapq.heappush(tasks, Task(1, "High priority"))
heapq.heappush(tasks, Task(2, "Medium priority"))

while tasks:
    task = heapq.heappop(tasks)
    print(f"Processing: {task.name}")
# High priority, Medium priority, Low priority
```

## Entscheidungshilfe nach Use Case

### Use Case: Caching

```python
# Einfaches Caching: dict
cache = {}

# LRU Cache: OrderedDict oder functools.lru_cache
from collections import OrderedDict

class LRUCache:
    def __init__(self, capacity):
        self.cache = OrderedDict()
        self.capacity = capacity
    
    def get(self, key):
        if key not in self.cache:
            return None
        self.cache.move_to_end(key)
        return self.cache[key]
    
    def put(self, key, value):
        if key in self.cache:
            self.cache.move_to_end(key)
        self.cache[key] = value
        if len(self.cache) > self.capacity:
            self.cache.popitem(last=False)

# Oder built-in
from functools import lru_cache

@lru_cache(maxsize=128)
def expensive_function(n):
    return compute(n)
```

### Use Case: Unique Elements behalten

```python
# Set für unsortierte unique
seen = set()
unique = []
for item in items:
    if item not in seen:
        seen.add(item)
        unique.append(item)

# Oder: dict.fromkeys() für geordnet (Python 3.7+)
unique = list(dict.fromkeys(items))
```

### Use Case: Top N Elemente

```python
# Kleine N: heapq.nlargest/nsmallest
top_10 = heapq.nlargest(10, items, key=lambda x: x.score)

# Große N: sortieren
top_1000 = sorted(items, key=lambda x: x.score, reverse=True)[:1000]
```

### Use Case: Counting

```python
# Counter für einfaches Zählen
from collections import Counter
counts = Counter(items)

# defaultdict(int) für manuelles Inkrement
counts = defaultdict(int)
for item in items:
    counts[item] += 1
```

## Performance-Cheatsheet

| Operation | list | tuple | set | dict | deque |
|-----------|------|-------|-----|------|-------|
| Append | O(1) | N/A | O(1) | O(1) | O(1) |
| Insert front | O(n) | N/A | N/A | N/A | O(1) |
| Pop back | O(1) | N/A | O(1) | O(1) | O(1) |
| Pop front | O(n) | N/A | N/A | N/A | O(1) |
| Index access | O(1) | O(1) | N/A | O(1)* | O(n) |
| Membership | O(n) | O(n) | O(1) | O(1) | O(n) |
| Iteration | O(n) | O(n) | O(n) | O(n) | O(n) |
| Sort | O(n log n) | N/A | N/A | N/A | N/A |

*Key-basiert

## Zusammenfassung

**Quick Reference:**

```python
# Geordnet + veränderbar → list
tasks = ["Task 1", "Task 2"]

# Geordnet + unveränderbar → tuple
point = (10, 20)

# Unique + schneller Lookup → set
valid_ids = {1, 2, 3, 4, 5}

# Key-Value Mapping → dict
config = {"host": "localhost", "port": 8080}

# Queue/Stack an beiden Enden → deque
from collections import deque
queue = deque()

# Counting → Counter
from collections import Counter
word_counts = Counter(words)

# Priority Queue → heapq
import heapq
tasks = []
heapq.heappush(tasks, (priority, task))
```

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [Built-in Types](https://docs.python.org/3/library/stdtypes.html) - list, dict, set, tuple
- [collections](https://docs.python.org/3/library/collections.html) - Spezielle Container
- [heapq](https://docs.python.org/3/library/heapq.html) - Heap Queue
- [Time Complexity](https://wiki.python.org/moin/TimeComplexity) - Performance Overview

## Verwandte Themen

- [[06_Praxis/01_Best_Practices/01_Idiomatisches Python|Idiomatisches Python]] - Best Practices
- [[06_Praxis/02_Performance/01_Performance Messung|Performance Messung]] - Profiling
- [[02_Sequenzen/01_Listen/05_List Performance|List Performance]] - Details zu Listen
- [[03_Mappings_und_Sets/01_Dictionaries/07_Dict Performance|Dict Performance]] - Details zu Dicts

---
← [[06_Praxis/01_Best_Practices/02_Code Smells vermeiden|Code Smells vermeiden]] | [[INDEX|📑 Index]] | [[06_Praxis/02_Performance/01_Performance Messung|Performance Messung]] →