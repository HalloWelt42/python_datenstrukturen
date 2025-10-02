---
tags: [python, datenstrukturen, deque, collections, operationen]
created: 2025-10-02
---

← [[04_Fortgeschritten/02_Deque/01_Deque Basics|Deque Basics]] | [[INDEX|📑 Index]] | [[04_Fortgeschritten/02_Deque/03_Performance Vergleich|Performance Vergleich]] →

# Deque Operationen

> [!tip] Lernziel
> Nach dieser Seite kennst du alle wichtigen deque-Methoden und -Operationen, verstehst ihre Zeitkomplexität und kannst sie effektiv in verschiedenen Szenarien einsetzen.

## Übersicht der Deque-Methoden

Deque bietet eine umfangreiche API für Operationen an beiden Enden sowie einige allgemeine Sequenz-Operationen:

| Kategorie | Methoden |
|-----------|----------|
| **Hinzufügen** | `append()`, `appendleft()`, `extend()`, `extendleft()`, `insert()` |
| **Entfernen** | `pop()`, `popleft()`, `remove()`, `clear()` |
| **Zugriff** | `[index]`, `count()`, `index()` |
| **Rotation** | `rotate()`, `reverse()` |
| **Kopieren** | `copy()` |

## Hinzufügen von Elementen

### append() und appendleft()

**Zeitkomplexität:** O(1)

```python
from collections import deque

# append() - Fügt am Ende hinzu
d = deque([1, 2, 3])
d.append(4)
print(d)  # deque([1, 2, 3, 4])

# appendleft() - Fügt am Anfang hinzu
d.appendleft(0)
print(d)  # deque([0, 1, 2, 3, 4])

# Bei maxlen wird automatisch das andere Ende entfernt
bounded = deque([1, 2, 3], maxlen=3)
bounded.append(4)
print(bounded)  # deque([2, 3, 4], maxlen=3) - 1 wurde entfernt
```

### extend() und extendleft()

**Zeitkomplexität:** O(k) wobei k = Anzahl der hinzugefügten Elemente

```python
from collections import deque

# extend() - Fügt mehrere Elemente am Ende hinzu
d = deque([1, 2, 3])
d.extend([4, 5, 6])
print(d)  # deque([1, 2, 3, 4, 5, 6])

# extendleft() - Fügt mehrere Elemente am Anfang hinzu
# ACHTUNG: Reihenfolge wird umgekehrt!
d = deque([4, 5, 6])
d.extendleft([1, 2, 3])
print(d)  # deque([3, 2, 1, 4, 5, 6])

# Warum die Umkehrung?
# Weil jedes Element einzeln links eingefügt wird:
d = deque([4])
d.appendleft(3)  # deque([3, 4])
d.appendleft(2)  # deque([2, 3, 4])
d.appendleft(1)  # deque([1, 2, 3, 4])
```

💡 **Praktischer Tipp:** Wenn du die Reihenfolge bei `extendleft()` beibehalten willst, kehre die Sequenz vorher um:

```python
d = deque([4, 5, 6])
d.extendleft(reversed([1, 2, 3]))
print(d)  # deque([1, 2, 3, 4, 5, 6])
```

### insert()

**Zeitkomplexität:** O(n)

```python
from collections import deque

# insert(index, element) - Fügt an beliebiger Stelle ein
d = deque([1, 2, 4, 5])
d.insert(2, 3)  # Fügt 3 an Index 2 ein
print(d)  # deque([1, 2, 3, 4, 5])

# Negative Indizes funktionieren auch
d.insert(-1, 4.5)
print(d)  # deque([1, 2, 3, 4, 4.5, 5])
```

⚠️ **Achtung:** `insert()` ist O(n) und damit langsam! Verwende deque nur für Operationen an den Enden. Für häufiges Einfügen in der Mitte ist eine Liste besser.

## Entfernen von Elementen

### pop() und popleft()

**Zeitkomplexität:** O(1)

```python
from collections import deque

d = deque([1, 2, 3, 4, 5])

# pop() - Entfernt und gibt letztes Element zurück
last = d.pop()
print(last)  # 5
print(d)     # deque([1, 2, 3, 4])

# popleft() - Entfernt und gibt erstes Element zurück
first = d.popleft()
print(first)  # 1
print(d)      # deque([2, 3, 4])

# IndexError bei leerer deque
empty = deque()
try:
    empty.pop()
except IndexError as e:
    print(f"Fehler: {e}")  # Fehler: pop from an empty deque
```

### remove()

**Zeitkomplexität:** O(n)

```python
from collections import deque

# remove(value) - Entfernt das erste Vorkommen
d = deque([1, 2, 3, 2, 4])
d.remove(2)  # Entfernt das erste 2
print(d)     # deque([1, 3, 2, 4])

# ValueError wenn Element nicht existiert
try:
    d.remove(99)
except ValueError as e:
    print(f"Fehler: {e}")  # Fehler: deque.remove(x): x not in deque
```

### clear()

**Zeitkomplexität:** O(n)

```python
from collections import deque

# clear() - Entfernt alle Elemente
d = deque([1, 2, 3, 4, 5])
d.clear()
print(d)  # deque([])
print(len(d))  # 0
```

## Rotation

### rotate()

**Zeitkomplexität:** O(k) wobei k = Anzahl der Rotationen

```python
from collections import deque

# rotate(n) - Rotiert n Schritte nach rechts
d = deque([1, 2, 3, 4, 5])
d.rotate(2)
print(d)  # deque([4, 5, 1, 2, 3])

# Negative Werte rotieren nach links
d = deque([1, 2, 3, 4, 5])
d.rotate(-2)
print(d)  # deque([3, 4, 5, 1, 2])

# rotate(1) entspricht: d.appendleft(d.pop())
# rotate(-1) entspricht: d.append(d.popleft())

# Praktisches Beispiel: Zyklische Iteration
def cycle_through(items, steps):
    """Zeige Elemente in rotierender Reihenfolge"""
    d = deque(items)
    for _ in range(steps):
        print(list(d))
        d.rotate(1)

cycle_through(['A', 'B', 'C'], 5)
# ['A', 'B', 'C']
# ['C', 'A', 'B']
# ['B', 'C', 'A']
# ['A', 'B', 'C']
# ['C', 'A', 'B']
```

## Zugriff und Suche

### Index-Zugriff

**Zeitkomplexität:** O(n) für Zugriff, O(n) für Zuweisung

```python
from collections import deque

d = deque([10, 20, 30, 40, 50])

# Lesen per Index
print(d[0])   # 10 (erstes Element)
print(d[-1])  # 50 (letztes Element)
print(d[2])   # 30 (drittes Element)

# Schreiben per Index
d[2] = 35
print(d)  # deque([10, 20, 35, 40, 50])

# Negative Indizes
d[-1] = 55
print(d)  # deque([10, 20, 35, 40, 55])

# IndexError bei ungültigem Index
try:
    print(d[10])
except IndexError as e:
    print(f"Fehler: {e}")
```

⚠️ **Performance-Warnung:** Index-Zugriff ist bei deque O(n)! Für häufigen Zugriff mittlerer Elemente ist eine Liste besser.

### count() und index()

**Zeitkomplexität:** O(n)

```python
from collections import deque

d = deque([1, 2, 3, 2, 4, 2, 5])

# count(value) - Zählt Vorkommen
print(d.count(2))  # 3
print(d.count(9))  # 0

# index(value, [start, [stop]]) - Findet ersten Index
print(d.index(2))        # 1 (erstes Vorkommen)
print(d.index(2, 2))     # 3 (ab Index 2 suchen)
print(d.index(2, 2, 5))  # 3 (zwischen Index 2 und 5)

# ValueError wenn nicht gefunden
try:
    d.index(99)
except ValueError as e:
    print(f"Fehler: {e}")  # Fehler: 99 is not in deque
```

## Weitere Operationen

### reverse()

**Zeitkomplexität:** O(n)

```python
from collections import deque

# reverse() - Kehrt Reihenfolge um (in-place)
d = deque([1, 2, 3, 4, 5])
d.reverse()
print(d)  # deque([5, 4, 3, 2, 1])

# Alternativ: reversed() für Iterator
d = deque([1, 2, 3, 4, 5])
for item in reversed(d):
    print(item, end=' ')  # 5 4 3 2 1
```

### copy()

**Zeitkomplexität:** O(n)

```python
from collections import deque

# copy() - Erstellt flache Kopie
original = deque([1, 2, 3])
kopie = original.copy()

kopie.append(4)
print(original)  # deque([1, 2, 3])
print(kopie)     # deque([1, 2, 3, 4])

# Achtung bei verschachtelten Objekten (flache Kopie!)
nested = deque([[1, 2], [3, 4]])
flat_copy = nested.copy()
flat_copy[0].append(5)  # Ändert auch Original!
print(nested)     # deque([[1, 2, 5], [3, 4]])
print(flat_copy)  # deque([[1, 2, 5], [3, 4]])

# Für tiefe Kopie: copy.deepcopy()
import copy
deep_copy = copy.deepcopy(nested)
deep_copy[0].append(6)
print(nested)     # deque([[1, 2, 5], [3, 4]])
print(deep_copy)  # deque([[1, 2, 5, 6], [3, 4]])
```

## Praktische Anwendungsbeispiele

### Beispiel 1: Sliding Window Maximum

```python
from collections import deque

def sliding_window_max(arr, k):
    """
    Findet Maximum in jedem Fenster der Größe k.
    Verwendet monotone deque für O(n) Zeitkomplexität.
    """
    if not arr or k <= 0:
        return []
    
    result = []
    dq = deque()  # Speichert Indizes in absteigender Reihenfolge der Werte
    
    for i in range(len(arr)):
        # Entferne Indizes außerhalb des aktuellen Fensters
        while dq and dq[0] <= i - k:
            dq.popleft()
        
        # Entferne kleinere Elemente (werden nie Maximum sein)
        while dq and arr[dq[-1]] < arr[i]:
            dq.pop()
        
        dq.append(i)
        
        # Ab Fenster voll: Maximum hinzufügen
        if i >= k - 1:
            result.append(arr[dq[0]])
    
    return result

# Test
numbers = [1, 3, -1, -3, 5, 3, 6, 7]
k = 3
print(sliding_window_max(numbers, k))
# [3, 3, 5, 5, 6, 7]
```

### Beispiel 2: LRU Cache (vereinfacht)

```python
from collections import deque

class SimpleLRUCache:
    """Einfacher LRU Cache mit deque"""
    
    def __init__(self, capacity):
        self.capacity = capacity
        self.cache = {}
        self.usage_order = deque()
    
    def get(self, key):
        """Holt Wert und markiert als recently used"""
        if key not in self.cache:
            return None
        
        # Markiere als zuletzt verwendet
        self.usage_order.remove(key)
        self.usage_order.append(key)
        return self.cache[key]
    
    def put(self, key, value):
        """Speichert Wert mit LRU Eviction"""
        if key in self.cache:
            # Update bestehenden Wert
            self.usage_order.remove(key)
        elif len(self.cache) >= self.capacity:
            # Entferne least recently used
            lru_key = self.usage_order.popleft()
            del self.cache[lru_key]
        
        self.cache[key] = value
        self.usage_order.append(key)
    
    def __repr__(self):
        return f"Cache: {self.cache}, Order: {list(self.usage_order)}"

# Test
cache = SimpleLRUCache(3)
cache.put('a', 1)
cache.put('b', 2)
cache.put('c', 3)
print(cache)  # Cache: {'a': 1, 'b': 2, 'c': 3}, Order: ['a', 'b', 'c']

cache.get('a')  # 'a' wird recently used
print(cache)  # Cache: {'a': 1, 'b': 2, 'c': 3}, Order: ['b', 'c', 'a']

cache.put('d', 4)  # 'b' wird evicted (LRU)
print(cache)  # Cache: {'a': 1, 'c': 3, 'd': 4}, Order: ['c', 'a', 'd']
```

### Beispiel 3: Undo/Redo System

```python
from collections import deque

class UndoRedoSystem:
    """Undo/Redo mit begrenztem History"""
    
    def __init__(self, max_history=10):
        self.undo_stack = deque(maxlen=max_history)
        self.redo_stack = deque(maxlen=max_history)
        self.current_state = None
    
    def do(self, action, state):
        """Führt Aktion aus und speichert State"""
        if self.current_state is not None:
            self.undo_stack.append(self.current_state)
        self.current_state = state
        self.redo_stack.clear()  # Redo history wird ungültig
        return f"Ausgeführt: {action}"
    
    def undo(self):
        """Macht letzte Aktion rückgängig"""
        if not self.undo_stack:
            return "Nichts zum Rückgängigmachen"
        
        self.redo_stack.append(self.current_state)
        self.current_state = self.undo_stack.pop()
        return f"Rückgängig gemacht. State: {self.current_state}"
    
    def redo(self):
        """Wiederholt rückgängig gemachte Aktion"""
        if not self.redo_stack:
            return "Nichts zum Wiederholen"
        
        self.undo_stack.append(self.current_state)
        self.current_state = self.redo_stack.pop()
        return f"Wiederholt. State: {self.current_state}"

# Test
editor = UndoRedoSystem(max_history=3)
print(editor.do("Typ 'H'", "H"))
print(editor.do("Typ 'e'", "He"))
print(editor.do("Typ 'l'", "Hel"))
print(editor.do("Typ 'l'", "Hell"))
print(editor.do("Typ 'o'", "Hello"))

print(editor.undo())  # Hello -> Hell
print(editor.undo())  # Hell -> Hel
print(editor.redo())  # Hel -> Hell
print(editor.do("Typ '!'", "Hell!"))  # Redo history wird gelöscht
```

## Häufige Fallstricke

### ❌ Fallstrick 1: Index-Zugriff in Schleifen

```python
from collections import deque

d = deque(range(1000))

# INEFFIZIENT: O(n²) Gesamtkomplexität
for i in range(len(d)):
    print(d[i])  # Jeder Zugriff ist O(n)!
```

✅ **Richtig:**
```python
# EFFIZIENT: O(n) durch direkte Iteration
for item in d:
    print(item)
```

### ❌ Fallstrick 2: extendleft() Reihenfolge vergessen

```python
from collections import deque

d = deque([4, 5, 6])
d.extendleft([1, 2, 3])
print(d)  # deque([3, 2, 1, 4, 5, 6]) - Nicht was erwartet!
```

✅ **Richtig:**
```python
d = deque([4, 5, 6])
d.extendleft(reversed([1, 2, 3]))
print(d)  # deque([1, 2, 3, 4, 5, 6])
```

### ❌ Fallstrick 3: Slicing versuchen

```python
from collections import deque

d = deque([1, 2, 3, 4, 5])
try:
    sub = d[1:3]  # TypeError!
except TypeError as e:
    print(f"Fehler: {e}")
```

✅ **Richtig:**
```python
# Konvertiere zu Liste für Slicing
sub = list(d)[1:3]
print(sub)  # [2, 3]

# Oder verwende itertools
from itertools import islice
sub = list(islice(d, 1, 3))
print(sub)  # [2, 3]
```

## Performance-Cheatsheet

| Operation | Zeitkomplexität | Anmerkung |
|-----------|----------------|-----------|
| `append(x)` | O(1) | Optimal |
| `appendleft(x)` | O(1) | Optimal |
| `pop()` | O(1) | Optimal |
| `popleft()` | O(1) | Optimal |
| `extend(iterable)` | O(k) | k = Länge iterable |
| `extendleft(iterable)` | O(k) | k = Länge iterable |
| `rotate(n)` | O(k) | k = abs(n) |
| `d[i]` | O(n) | Vermeide häufigen Zugriff |
| `d[i] = x` | O(n) | Vermeide häufige Zuweisung |
| `insert(i, x)` | O(n) | Verwende nur an Enden |
| `remove(x)` | O(n) | Linear search |
| `count(x)` | O(n) | Muss alle durchsuchen |
| `index(x)` | O(n) | Linear search |
| `reverse()` | O(n) | In-place reversal |
| `copy()` | O(n) | Shallow copy |

## Übungsaufgaben

### Übung 1: Palindrom-Checker (Leicht)

Schreibe eine Funktion, die mit einer deque prüft, ob ein String ein Palindrom ist.

```python
def is_palindrome(text):
    """Prüft ob text ein Palindrom ist (ignoriert Leerzeichen/Groß-Klein)"""
    # Dein Code hier
    pass

# Tests
print(is_palindrome("radar"))  # ?
print(is_palindrome("hello"))  # ?
print(is_palindrome("A man a plan a canal Panama"))  # ?
```

**Erwarteter Output:**
```
True
False
True
```

### Übung 2: Rotating Queue (Mittel)

Implementiere eine rotierende Warteschlange, die nach n Elementen automatisch das älteste Element entfernt.

```python
class RotatingQueue:
    def __init__(self, max_size):
        """Initialisiert Queue mit maximaler Größe"""
        # Dein Code hier
        pass
    
    def add(self, item):
        """Fügt Item hinzu (entfernt ältestes bei Überlauf)"""
        pass
    
    def get_all(self):
        """Gibt alle aktuellen Items zurück"""
        pass

# Test
rq = RotatingQueue(3)
rq.add(1)
rq.add(2)
rq.add(3)
print(rq.get_all())  # ?
rq.add(4)
print(rq.get_all())  # ?
```

**Erwarteter Output:**
```
[1, 2, 3]
[2, 3, 4]
```

### Übung 3: Task Scheduler mit Prioritäten (Mittel)

Implementiere einen Task Scheduler, der Tasks mit hoher Priorität vorne einfügt und normale Tasks hinten.

```python
class TaskScheduler:
    def __init__(self):
        # Dein Code hier
        pass
    
    def add_task(self, task, priority='normal'):
        """Fügt Task hinzu (priority: 'high' oder 'normal')"""
        pass
    
    def execute_next(self):
        """Führt nächste Task aus und gibt sie zurück"""
        pass

# Test
scheduler = TaskScheduler()
scheduler.add_task("Task A")
scheduler.add_task("Task B", priority='high')
scheduler.add_task("Task C")
scheduler.add_task("Task D", priority='high')

print(scheduler.execute_next())  # ?
print(scheduler.execute_next())  # ?
print(scheduler.execute_next())  # ?
```

**Erwarteter Output:**
```
Task D
Task B
Task A
```

### Übung 4: Moving Average (Mittel-Schwer)

Implementiere einen gleitenden Durchschnitt mit festem Fenster unter Verwendung einer deque.

```python
class MovingAverage:
    def __init__(self, window_size):
        """Initialisiert mit Fenstergröße"""
        # Dein Code hier
        pass
    
    def add_value(self, value):
        """Fügt Wert hinzu und gibt aktuellen Durchschnitt zurück"""
        pass

# Test
ma = MovingAverage(3)
print(ma.add_value(1))   # ?
print(ma.add_value(2))   # ?
print(ma.add_value(3))   # ?
print(ma.add_value(4))   # ?
print(ma.add_value(5))   # ?
```

**Erwarteter Output:**
```
1.0
1.5
2.0
3.0
4.0
```

### Übung 5: Circular Buffer mit Overwrite (Schwer)

Implementiere einen Circular Buffer, der überschreibt wenn voll, und eine `peek(n)` Methode hat, die die letzten n Elemente ohne Entfernen zurückgibt.

```python
class CircularBuffer:
    def __init__(self, capacity):
        # Dein Code hier
        pass
    
    def write(self, item):
        """Schreibt Item (überschreibt ältestes wenn voll)"""
        pass
    
    def read(self):
        """Liest und entfernt ältestes Item"""
        pass
    
    def peek(self, n):
        """Gibt letzte n Items zurück ohne zu entfernen"""
        pass

# Test
cb = CircularBuffer(3)
cb.write('A')
cb.write('B')
cb.write('C')
cb.write('D')  # Überschreibt 'A'
print(cb.peek(2))    # ?
print(cb.read())     # ?
print(cb.peek(2))    # ?
```

**Erwarteter Output:**
```
['C', 'D']
B
['C', 'D']
```

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [deque - collections](https://docs.python.org/3/library/collections.html#collections.deque) - Vollständige API-Referenz mit allen Methoden
- [deque recipes](https://docs.python.org/3/library/collections.html#deque-recipes) - Praktische Rezepte und Patterns
- [Time Complexity](https://wiki.python.org/moin/TimeComplexity) - Performance verschiedener Operationen

## Verwandte Themen

- [[04_Fortgeschritten/02_Deque/01_Deque Basics|Deque Basics]] - Grundlagen und Erstellung
- [[04_Fortgeschritten/02_Deque/03_Performance Vergleich|Performance Vergleich]] - deque vs list vs andere
- [[02_Sequenzen/01_Listen/02_List Methoden|List Methoden]] - Vergleich mit Listen-Methoden
- [[03_Mappings_und_Sets/03_Collections_Module/02_DefaultDict|DefaultDict]] - Weiteres aus collections

---
← [[04_Fortgeschritten/02_Deque/01_Deque Basics|Deque Basics]] | [[INDEX|📑 Index]] | [[04_Fortgeschritten/02_Deque/03_Performance Vergleich|Performance Vergleich]] →