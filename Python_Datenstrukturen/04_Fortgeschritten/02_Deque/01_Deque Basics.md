---
tags: [python, datenstrukturen, deque, collections, fortgeschritten]
created: 2025-10-01
---

← [[04_Fortgeschritten/01_Arrays/03_Übungen Arrays|Übungen Arrays]] | [[INDEX|📑 Index]] | [[04_Fortgeschritten/02_Deque/02_Deque Operationen|Deque Operationen]] →

# Deque Basics

> [!tip] Lernziel
> Nach dieser Seite verstehst du deque als doppelseitige Warteschlange und kannst es für effiziente append/pop Operationen an beiden Enden einsetzen.

## Was ist eine Deque?

**Deque** (ausgesprochen "deck") steht für **"double-ended queue"** - eine Warteschlange mit Zugriff an beiden Enden. Es ist Teil des `collections` Moduls.

**Kernmerkmale:**
- **Beidseitiger Zugriff**: O(1) append/pop an beiden Enden
- **Thread-safe**: Atomare append/pop Operationen
- **Memory-effizient**: Keine Reallocation wie bei Listen
- **Bounded**: Optional mit maximaler Größe
- **Rotation**: Effizientes Rotieren der Elemente
- **Implementiert als doubly-linked list** von Blöcken

**Wofür verwendet:**
- Warteschlangen (Queues)
- Stacks
- Sliding Window Probleme
- Undo/Redo Funktionalität
- Breadth-First Search (BFS)
- LRU Cache Implementation

---

## Import und Erstellung

### Deque erstellen

```python
from collections import deque

# Leere Deque
d = deque()
print(d)  # deque([])

# Aus Iterable
d = deque([1, 2, 3, 4, 5])
print(d)  # deque([1, 2, 3, 4, 5])

# Aus String
d = deque('hello')
print(d)  # deque(['h', 'e', 'l', 'l', 'o'])

# Aus Range
d = deque(range(5))
print(d)  # deque([0, 1, 2, 3, 4])

# Mit maxlen (begrenzte Größe)
d = deque([1, 2, 3], maxlen=5)
print(d)  # deque([1, 2, 3], maxlen=5)
print(d.maxlen)  # 5

# maxlen = None für unbegrenzt (default)
d = deque([1, 2, 3])
print(d.maxlen)  # None
```

---

## Grundlegende Operationen

### Elemente an beiden Enden hinzufügen

```python
from collections import deque

d = deque([2, 3, 4])
print(d)  # deque([2, 3, 4])

# append() - Am rechten Ende
d.append(5)
print(d)  # deque([2, 3, 4, 5])

# appendleft() - Am linken Ende
d.appendleft(1)
print(d)  # deque([1, 2, 3, 4, 5])

# extend() - Mehrere rechts
d.extend([6, 7])
print(d)  # deque([1, 2, 3, 4, 5, 6, 7])

# extendleft() - Mehrere links (umgekehrte Reihenfolge!)
d.extendleft([0, -1])
print(d)  # deque([-1, 0, 1, 2, 3, 4, 5, 6, 7])
# ⚠️ Beachte: [0, -1] wird zu [-1, 0]
```

### Elemente an beiden Enden entfernen

```python
from collections import deque

d = deque([1, 2, 3, 4, 5])

# pop() - Vom rechten Ende
right = d.pop()
print(f"Pop right: {right}")  # 5
print(d)  # deque([1, 2, 3, 4])

# popleft() - Vom linken Ende
left = d.popleft()
print(f"Pop left: {left}")  # 1
print(d)  # deque([2, 3, 4])

# Bei leerer Deque: IndexError
empty = deque()
try:
    empty.pop()
except IndexError:
    print("Deque ist leer!")
```

### Zugriff und Modifikation

```python
from collections import deque

d = deque([10, 20, 30, 40, 50])

# Index-Zugriff (wie Liste)
print(d[0])   # 10 (erstes Element)
print(d[-1])  # 50 (letztes Element)

# Slicing wird NICHT unterstützt!
try:
    print(d[1:3])  # TypeError!
except TypeError:
    print("Slicing nicht unterstützt bei deque")

# Modifikation
d[0] = 100
print(d)  # deque([100, 20, 30, 40, 50])

# Länge
print(len(d))  # 5

# Membership
print(30 in d)  # True
```

---

## Bounded Deque (maxlen)

### Automatisches Überschreiben

```python
from collections import deque

# Deque mit maxlen=3
d = deque(maxlen=3)

# Elemente hinzufügen
d.append(1)
d.append(2)
d.append(3)
print(d)  # deque([1, 2, 3], maxlen=3)

# Weiteres append: Ältestes wird entfernt!
d.append(4)
print(d)  # deque([2, 3, 4], maxlen=3)

d.append(5)
print(d)  # deque([3, 4, 5], maxlen=3)

# Auch von links
d.appendleft(2)
print(d)  # deque([2, 3, 4], maxlen=3) - 5 wurde entfernt

# Praktisch für Sliding Window
recent_values = deque(maxlen=5)
for i in range(10):
    recent_values.append(i)
    print(f"Recent 5: {list(recent_values)}")
```

### Use Case: Last N Items

```python
from collections import deque

class RecentLogger:
    """Speichert nur die letzten N Log-Einträge"""
    def __init__(self, max_entries=100):
        self.logs = deque(maxlen=max_entries)
    
    def log(self, message):
        self.logs.append(message)
    
    def get_recent(self, n=10):
        # Letzte n Einträge
        return list(self.logs)[-n:]
    
    def clear(self):
        self.logs.clear()

# Verwendung
logger = RecentLogger(max_entries=5)
for i in range(10):
    logger.log(f"Message {i}")

print("Letzte 5:", logger.get_recent(5))
# Nur 5-9 gespeichert, 0-4 wurden überschrieben
```

---

## Rotation

### rotate() - Elemente verschieben

```python
from collections import deque

d = deque([1, 2, 3, 4, 5])
print(f"Original: {d}")

# Nach rechts rotieren (positive n)
d.rotate(1)
print(f"Rotate 1:  {d}")  # deque([5, 1, 2, 3, 4])

d.rotate(2)
print(f"Rotate 2:  {d}")  # deque([3, 4, 5, 1, 2])

# Nach links rotieren (negative n)
d = deque([1, 2, 3, 4, 5])
d.rotate(-1)
print(f"Rotate -1: {d}")  # deque([2, 3, 4, 5, 1])

d.rotate(-2)
print(f"Rotate -2: {d}")  # deque([4, 5, 1, 2, 3])

# Rotation um mehr als Länge
d = deque([1, 2, 3])
d.rotate(5)  # Äquivalent zu rotate(5 % 3) = rotate(2)
print(f"Rotate 5:  {d}")  # deque([2, 3, 1])
```

### Praktische Anwendung: Rundgang

```python
from collections import deque

# Spieler-Reihenfolge
players = deque(['Alice', 'Bob', 'Charlie', 'Diana'])

print("Runde 1:")
for _ in range(len(players)):
    current = players[0]
    print(f"{current}'s Zug")
    players.rotate(-1)  # Nächster Spieler

print(f"\nSpieler-Reihenfolge: {list(players)}")
# Reihenfolge bleibt erhalten nach vollem Durchlauf
```

---

## Weitere Methoden

### clear(), copy(), count(), index()

```python
from collections import deque

d = deque([1, 2, 3, 2, 4, 2, 5])

# count() - Häufigkeit
print(d.count(2))  # 3

# index() - Position
print(d.index(2))  # 1 (erstes Vorkommen)

# index() mit Start/Stop
print(d.index(2, 2))  # 3 (ab Index 2 suchen)

# clear() - Alles löschen
d_copy = d.copy()
d.clear()
print(d)  # deque([])
print(d_copy)  # deque([1, 2, 3, 2, 4, 2, 5]) - Original Kopie

# copy() - Shallow Copy
d1 = deque([1, 2, 3])
d2 = d1.copy()
d2.append(4)
print(d1)  # deque([1, 2, 3]) - Unverändert
print(d2)  # deque([1, 2, 3, 4])
```

### insert(), remove(), reverse()

```python
from collections import deque

d = deque([1, 2, 3, 4, 5])

# insert() - An Position einfügen (O(n)!)
d.insert(2, 99)
print(d)  # deque([1, 2, 99, 3, 4, 5])

# remove() - Erstes Vorkommen entfernen (O(n)!)
d.remove(99)
print(d)  # deque([1, 2, 3, 4, 5])

# reverse() - In-place umkehren
d.reverse()
print(d)  # deque([5, 4, 3, 2, 1])

# ⚠️ insert() und remove() sind O(n) - nicht optimal!
# Besser: append/appendleft und pop/popleft nutzen
```

---

## Praktische Anwendungen

### 1. Queue (FIFO - First In, First Out)

```python
from collections import deque

class Queue:
    """FIFO Queue mit deque"""
    def __init__(self):
        self.items = deque()
    
    def enqueue(self, item):
        """Fügt am Ende hinzu"""
        self.items.append(item)
    
    def dequeue(self):
        """Entfernt vom Anfang"""
        if self.is_empty():
            raise IndexError("Queue ist leer")
        return self.items.popleft()
    
    def peek(self):
        """Schaut erstes Element an"""
        if self.is_empty():
            return None
        return self.items[0]
    
    def is_empty(self):
        return len(self.items) == 0
    
    def size(self):
        return len(self.items)

# Verwendung
q = Queue()
q.enqueue('First')
q.enqueue('Second')
q.enqueue('Third')

print(q.dequeue())  # First
print(q.dequeue())  # Second
print(q.peek())     # Third
print(q.size())     # 1
```

### 2. Stack (LIFO - Last In, First Out)

```python
from collections import deque

class Stack:
    """LIFO Stack mit deque"""
    def __init__(self):
        self.items = deque()
    
    def push(self, item):
        """Fügt oben hinzu"""
        self.items.append(item)
    
    def pop(self):
        """Entfernt von oben"""
        if self.is_empty():
            raise IndexError("Stack ist leer")
        return self.items.pop()
    
    def peek(self):
        """Schaut oberstes Element an"""
        if self.is_empty():
            return None
        return self.items[-1]
    
    def is_empty(self):
        return len(self.items) == 0

# Verwendung
s = Stack()
s.push(1)
s.push(2)
s.push(3)

print(s.pop())   # 3
print(s.pop())   # 2
print(s.peek())  # 1
```

### 3. Sliding Window

```python
from collections import deque

def sliding_window_max(nums, k):
    """Findet Maximum in jedem Window der Größe k"""
    if not nums or k == 0:
        return []
    
    result = []
    window = deque()
    
    for i, num in enumerate(nums):
        # Entferne Elemente außerhalb des Windows
        if window and window[0] <= i - k:
            window.popleft()
        
        # Entferne kleinere Elemente (nicht mehr relevant)
        while window and nums[window[-1]] < num:
            window.pop()
        
        window.append(i)
        
        # Ab Window-Größe: Maximum hinzufügen
        if i >= k - 1:
            result.append(nums[window[0]])
    
    return result

# Test
nums = [1, 3, -1, -3, 5, 3, 6, 7]
k = 3
print(sliding_window_max(nums, k))
# [3, 3, 5, 5, 6, 7]
```

### 4. Undo/Redo System

```python
from collections import deque

class UndoRedo:
    """Einfaches Undo/Redo System"""
    def __init__(self, max_history=10):
        self.history = deque(maxlen=max_history)
        self.redo_stack = deque()
        self.current_state = None
    
    def do_action(self, state):
        """Führt Aktion aus und speichert State"""
        if self.current_state is not None:
            self.history.append(self.current_state)
        self.current_state = state
        self.redo_stack.clear()  # Redo-History ungültig
    
    def undo(self):
        """Macht letzte Aktion rückgängig"""
        if not self.history:
            return None
        
        self.redo_stack.append(self.current_state)
        self.current_state = self.history.pop()
        return self.current_state
    
    def redo(self):
        """Wiederholt rückgängig gemachte Aktion"""
        if not self.redo_stack:
            return None
        
        self.history.append(self.current_state)
        self.current_state = self.redo_stack.pop()
        return self.current_state

# Verwendung
editor = UndoRedo()

editor.do_action("Hello")
editor.do_action("Hello World")
editor.do_action("Hello World!")

print(f"Current: {editor.current_state}")  # Hello World!

print(f"Undo: {editor.undo()}")  # Hello World
print(f"Undo: {editor.undo()}")  # Hello

print(f"Redo: {editor.redo()}")  # Hello World
```

### 5. Breadth-First Search (BFS)

```python
from collections import deque

def bfs(graph, start):
    """BFS Traversierung eines Graphen"""
    visited = set()
    queue = deque([start])
    visited.add(start)
    
    result = []
    
    while queue:
        vertex = queue.popleft()
        result.append(vertex)
        
        # Alle Nachbarn
        for neighbor in graph[vertex]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)
    
    return result

# Test
graph = {
    'A': ['B', 'C'],
    'B': ['A', 'D', 'E'],
    'C': ['A', 'F'],
    'D': ['B'],
    'E': ['B', 'F'],
    'F': ['C', 'E']
}

print(bfs(graph, 'A'))
# ['A', 'B', 'C', 'D', 'E', 'F']
```

### 6. Recent History Tracker

```python
from collections import deque

class BrowserHistory:
    """Browser History mit deque"""
    def __init__(self, max_history=50):
        self.history = deque(maxlen=max_history)
        self.current_index = -1
    
    def visit(self, url):
        """Besucht neue URL"""
        # Entferne Forward-History
        while len(self.history) > self.current_index + 1:
            self.history.pop()
        
        self.history.append(url)
        self.current_index = len(self.history) - 1
    
    def back(self):
        """Zurück"""
        if self.current_index > 0:
            self.current_index -= 1
            return self.history[self.current_index]
        return None
    
    def forward(self):
        """Vorwärts"""
        if self.current_index < len(self.history) - 1:
            self.current_index += 1
            return self.history[self.current_index]
        return None
    
    def current(self):
        """Aktuelle URL"""
        if self.current_index >= 0:
            return self.history[self.current_index]
        return None

# Verwendung
browser = BrowserHistory()

browser.visit("google.com")
browser.visit("github.com")
browser.visit("stackoverflow.com")

print(f"Current: {browser.current()}")      # stackoverflow.com
print(f"Back: {browser.back()}")            # github.com
print(f"Back: {browser.back()}")            # google.com
print(f"Forward: {browser.forward()}")      # github.com

browser.visit("python.org")
print(f"Current: {browser.current()}")      # python.org
print(f"Forward: {browser.forward()}")      # None (Forward-History gelöscht)
```

---

## Deque vs. Liste

### Performance-Vergleich

```python
from collections import deque
import timeit

size = 100000

# append() Performance
def list_append():
    l = []
    for i in range(size):
        l.append(i)

def deque_append():
    d = deque()
    for i in range(size):
        d.append(i)

list_time = timeit.timeit(list_append, number=10)
deque_time = timeit.timeit(deque_append, number=10)

print(f"append() - {size} Operationen:")
print(f"  Liste: {list_time:.3f}s")
print(f"  Deque: {deque_time:.3f}s")

# appendleft() / insert(0) Performance
def list_appendleft():
    l = []
    for i in range(1000):  # Kleiner wegen O(n)
        l.insert(0, i)

def deque_appendleft():
    d = deque()
    for i in range(1000):
        d.appendleft(i)

list_time = timeit.timeit(list_appendleft, number=10)
deque_time = timeit.timeit(deque_appendleft, number=10)

print(f"\nappendleft/insert(0) - 1000 Operationen:")
print(f"  Liste: {list_time:.3f}s")
print(f"  Deque: {deque_time:.3f}s")
print(f"  Deque ist {list_time/deque_time:.0f}x schneller!")
```

---

## Thread Safety

```python
from collections import deque
import threading

# deque append/pop Operationen sind thread-safe
shared_queue = deque()

def producer():
    for i in range(1000):
        shared_queue.append(i)

def consumer():
    while True:
        try:
            item = shared_queue.popleft()
        except IndexError:
            break

# Threads starten
threads = []
threads.append(threading.Thread(target=producer))
threads.append(threading.Thread(target=consumer))

for t in threads:
    t.start()

for t in threads:
    t.join()

# Keine Race Conditions bei append/popleft!
```

---

## Häufige Fallstricke

### ⚠️ Fallstrick 1: Kein Slicing

```python
from collections import deque

d = deque([1, 2, 3, 4, 5])

# ❌ Slicing geht nicht
try:
    print(d[1:3])
except TypeError:
    print("Slicing nicht unterstützt!")

# ✅ Zu Liste konvertieren
print(list(d)[1:3])  # [2, 3]
```

### ⚠️ Fallstrick 2: extendleft() umgekehrte Reihenfolge

```python
from collections import deque

d = deque([3, 4, 5])

# ❌ Erwartung: [1, 2, 3, 4, 5]
d.extendleft([1, 2])
print(d)  # deque([2, 1, 3, 4, 5]) - Umgekehrt!

# ✅ Richtig: Items einzeln oder reversed
d = deque([3, 4, 5])
d.extendleft(reversed([1, 2]))
print(d)  # deque([1, 2, 3, 4, 5])
```

### ⚠️ Fallstrick 3: insert/remove sind O(n)

```python
from collections import deque

d = deque([1, 2, 3, 4, 5])

# ❌ insert() ist O(n) - nicht optimal!
d.insert(2, 99)  # Langsam

# ✅ Verwende append/appendleft wann möglich
d.append(99)      # O(1)
d.appendleft(99)  # O(1)
```

---

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [collections.deque](https://docs.python.org/3/library/collections.html#collections.deque) - Vollständige deque Referenz
- [deque Recipes](https://docs.python.org/3/library/collections.html#deque-recipes) - Praktische Beispiele
- [collections module](https://docs.python.org/3/library/collections.html) - Alle Collections

---

## Verwandte Themen

- [[04_Fortgeschritten/02_Deque/02_Deque Operationen|Deque Operationen]] - Weitere Operationen im Detail
- [[04_Fortgeschritten/02_Deque/03_Performance Vergleich|Performance Vergleich]] - Detaillierter Vergleich
- [[02_Sequenzen/01_Listen/01_List Basics|List Basics]] - Alternative Datenstruktur
- [[04_Fortgeschritten/02_Deque/04_Übungen Deque|Übungen Deque]] - Praktische Übungen

---

← [[04_Fortgeschritten/01_Arrays/03_Übungen Arrays|Übungen Arrays]] | [[INDEX|📑 Index]] | [[04_Fortgeschritten/02_Deque/02_Deque Operationen|Deque Operationen]] →