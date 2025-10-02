---
tags: [python, datenstrukturen, heapq, priority-queue, heap]
created: 2025-10-02
---

← [[04_Fortgeschritten/02_Deque/04_Übungen Deque|Übungen Deque]] | [[INDEX|📑 Index]] | [[04_Fortgeschritten/03_Heap_Queue/02_Priority Queues|Priority Queues]] →

# Heapq Module

> [!tip] Lernziel
> Nach dieser Seite verstehst du, was ein Heap ist, wie das heapq-Modul funktioniert, kennst alle wichtigen Heap-Operationen und kannst Heaps für effiziente Priority Queues und Sortierung einsetzen.

## Was ist ein Heap?

Ein **Heap** ist eine spezielle Baum-basierte Datenstruktur, die die **Heap-Eigenschaft** erfüllt:

**Min-Heap:** Jeder Elternknoten ist kleiner oder gleich seinen Kindern
**Max-Heap:** Jeder Elternknoten ist größer oder gleich seinen Kindern

Python's `heapq` implementiert einen **Min-Heap**.

### Visuelle Darstellung

```
Min-Heap Beispiel:
       1
      / \
     3   2
    / \ / \
   5  4 6  7

Array-Repräsentation: [1, 3, 2, 5, 4, 6, 7]

Index-Beziehungen:
- Parent von Index i: (i-1) // 2
- Linkes Kind von i: 2*i + 1
- Rechtes Kind von i: 2*i + 2
```

### Warum Heaps?

Heaps sind ideal für Szenarien, wo man:
- ✅ Schnell das **Minimum** (oder Maximum) finden muss - **O(1)**
- ✅ Effizient **einfügen** will - **O(log n)**
- ✅ Das Minimum **entfernen** will - **O(log n)**
- ✅ Eine **Priority Queue** implementieren will
- ✅ Die **n kleinsten/größten** Elemente finden will

❌ **Nicht geeignet** für:
- Suchen beliebiger Elemente (O(n))
- Zugriff auf mittlere Elemente
- Sortierte Iteration (außer mit `heappop`)

## Das heapq-Modul

Das `heapq`-Modul bietet Heap-Operationen für **normale Python-Listen**. Es modifiziert Listen in-place, sodass sie die Heap-Eigenschaft erfüllen.

```python
import heapq

# heapq arbeitet mit normalen Listen!
heap = []  # Leerer Heap
```

> [!warning] Wichtig
> `heapq` erstellt **keinen** speziellen Heap-Typ, sondern nutzt normale Listen. Die Heap-Eigenschaft wird durch die Funktionen garantiert.

## Grundlegende Heap-Operationen

### heappush() - Element hinzufügen

**Zeitkomplexität:** O(log n)

```python
import heapq

heap = []

# Elemente hinzufügen
heapq.heappush(heap, 5)
heapq.heappush(heap, 3)
heapq.heappush(heap, 7)
heapq.heappush(heap, 1)

print(heap)  # [1, 3, 7, 5]
# Heap-Eigenschaft erfüllt, aber nicht vollständig sortiert!
```

💡 **Hinweis:** Die Liste ist **nicht sortiert**, aber die Heap-Eigenschaft ist erfüllt (kleinstes Element an Index 0).

### heappop() - Kleinstes Element entfernen

**Zeitkomplexität:** O(log n)

```python
import heapq

heap = [1, 3, 7, 5]

# Kleinstes Element entfernen und zurückgeben
smallest = heapq.heappop(heap)
print(smallest)  # 1
print(heap)      # [3, 5, 7]

# Weiteres pop
smallest = heapq.heappop(heap)
print(smallest)  # 3
print(heap)      # [5, 7]
```

### heappushpop() - Push und Pop kombiniert

**Zeitkomplexität:** O(log n)

```python
import heapq

heap = [1, 3, 7, 5]

# Effizienter als separate push() + pop()
# Fügt hinzu und gibt kleinstes Element zurück
result = heapq.heappushpop(heap, 2)
print(result)  # 1 (war kleinstes)
print(heap)    # [2, 3, 7, 5]

# Noch ein Beispiel: größeres Element hinzufügen
result = heapq.heappushpop(heap, 10)
print(result)  # 2 (immer noch kleinstes)
print(heap)    # [3, 5, 7, 10]
```

💡 **Vorteil:** Effizienter als `heappush()` gefolgt von `heappop()`.

### heapreplace() - Pop dann Push

**Zeitkomplexität:** O(log n)

```python
import heapq

heap = [1, 3, 7, 5]

# Entfernt kleinstes, dann fügt neues hinzu
result = heapq.heapreplace(heap, 2)
print(result)  # 1 (entferntes Element)
print(heap)    # [2, 3, 7, 5]

# Unterschied zu heappushpop:
heap = [1, 3, 7, 5]

# heapreplace: Pop DANN Push
result1 = heapq.heapreplace(heap, 0)
print(result1, heap)  # 1 [0, 3, 7, 5]

# heappushpop: Push DANN Pop
heap = [1, 3, 7, 5]
result2 = heapq.heappushpop(heap, 0)
print(result2, heap)  # 0 [1, 3, 7, 5]
```

⚠️ **Achtung:** 
- `heapreplace()` kann Element zurückgeben, das größer ist als das hinzugefügte
- `heappushpop()` gibt immer das global kleinste zurück

## Heap aus existierender Liste erstellen

### heapify() - Liste in Heap umwandeln

**Zeitkomplexität:** O(n)

```python
import heapq

# Unsortierte Liste
numbers = [5, 7, 9, 1, 3, 2, 8]

# In Heap umwandeln (in-place)
heapq.heapify(numbers)
print(numbers)  # [1, 3, 2, 7, 5, 9, 8]
# Heap-Eigenschaft erfüllt!

# Jetzt kann man heappop() verwenden
print(heapq.heappop(numbers))  # 1
print(heapq.heappop(numbers))  # 2
print(heapq.heappop(numbers))  # 3
```

💡 **Effizient:** `heapify()` ist O(n), während n einzelne `heappush()` Operationen O(n log n) wären!

## Nützliche Heap-Funktionen

### nsmallest() und nlargest()

**Zeitkomplexität:** O(n log k) wobei k = Anzahl gewünschter Elemente

```python
import heapq

numbers = [5, 2, 9, 1, 7, 6, 3, 8, 4]

# 3 kleinste Elemente (sortiert)
smallest = heapq.nsmallest(3, numbers)
print(smallest)  # [1, 2, 3]

# 3 größte Elemente (sortiert, absteigend)
largest = heapq.nlargest(3, numbers)
print(largest)  # [9, 8, 7]

# Original-Liste bleibt unverändert
print(numbers)  # [5, 2, 9, 1, 7, 6, 3, 8, 4]
```

#### Mit key-Funktion

```python
import heapq

users = [
    {'name': 'Alice', 'age': 30},
    {'name': 'Bob', 'age': 25},
    {'name': 'Charlie', 'age': 35},
    {'name': 'David', 'age': 28}
]

# 2 jüngste User
youngest = heapq.nsmallest(2, users, key=lambda u: u['age'])
print(youngest)
# [{'name': 'Bob', 'age': 25}, {'name': 'David', 'age': 28}]

# 2 älteste User
oldest = heapq.nlargest(2, users, key=lambda u: u['age'])
print(oldest)
# [{'name': 'Charlie', 'age': 35}, {'name': 'Alice', 'age': 30}]
```

### Performance-Hinweis: Wann was verwenden?

```python
import heapq

# Für k kleine relativ zu n: nsmallest/nlargest
numbers = list(range(1000000))
top_10 = heapq.nlargest(10, numbers)  # Effizient!

# Für k ≈ n: Besser sortieren
numbers = list(range(100))
top_90 = sorted(numbers, reverse=True)[:90]  # Effizienter als nlargest(90)

# Faustregel:
# k < n/10  → nsmallest/nlargest
# k > n/2   → sorted()
```

## Heap-Sortierung (Heapsort)

```python
import heapq

def heapsort(iterable):
    """Sortiert mit Heap - O(n log n)"""
    heap = []
    for item in iterable:
        heapq.heappush(heap, item)
    
    return [heapq.heappop(heap) for _ in range(len(heap))]

# Test
numbers = [5, 2, 9, 1, 7, 6, 3]
sorted_numbers = heapsort(numbers)
print(sorted_numbers)  # [1, 2, 3, 5, 6, 7, 9]

# Effizienter mit heapify:
def heapsort_optimized(iterable):
    """Optimierter Heapsort"""
    heap = list(iterable)
    heapq.heapify(heap)  # O(n) statt O(n log n)
    return [heapq.heappop(heap) for _ in range(len(heap))]
```

⚠️ **Hinweis:** In der Praxis ist Python's `sorted()` (Timsort) meist schneller. Heapsort ist interessant für:
- Streaming-Daten (Element für Element sortieren)
- Memory-beschränkte Umgebungen
- Online-Algorithmen

## Interna: Wie funktioniert ein Heap?

### Heap als Array/Liste

```python
# Heap als Baum:
#        1
#       / \
#      3   2
#     / \
#    5   4

# Als Liste: [1, 3, 2, 5, 4]
# Index:      0  1  2  3  4

def parent(i):
    """Index des Parents"""
    return (i - 1) // 2

def left_child(i):
    """Index des linken Kindes"""
    return 2 * i + 1

def right_child(i):
    """Index des rechten Kindes"""
    return 2 * i + 2

# Beispiel
heap = [1, 3, 2, 5, 4]
print(f"Parent von Index 3: {parent(3)} → Wert: {heap[parent(3)]}")  # 1
print(f"Linkes Kind von Index 1: {left_child(1)} → Wert: {heap[left_child(1)]}")  # 5
print(f"Rechtes Kind von Index 1: {right_child(1)} → Wert: {heap[right_child(1)]}")  # 4
```

### Heap-Operationen visualisiert

```python
import heapq

def visualize_heap(heap, indent=0, index=0):
    """Einfache Heap-Visualisierung"""
    if index >= len(heap):
        return
    
    # Rechtes Kind (zuerst, für bessere Visualisierung)
    right = 2 * index + 2
    if right < len(heap):
        visualize_heap(heap, indent + 4, right)
    
    # Aktueller Knoten
    print(' ' * indent + str(heap[index]))
    
    # Linkes Kind
    left = 2 * index + 1
    if left < len(heap):
        visualize_heap(heap, indent + 4, left)

# Demonstration
heap = []
for value in [5, 3, 7, 1, 9, 2]:
    heapq.heappush(heap, value)
    print(f"\nNach heappush({value}):")
    print(f"Liste: {heap}")
    print("Baum:")
    visualize_heap(heap)
```

## Praktische Anwendungsbeispiele

### Beispiel 1: K kleinste Elemente aus Stream

```python
import heapq

def find_k_smallest_streaming(stream, k):
    """
    Findet k kleinste Elemente aus einem Stream,
    ohne alle Elemente im Speicher zu halten.
    """
    # Verwende Max-Heap (negierte Werte für Python's Min-Heap)
    heap = []
    
    for item in stream:
        if len(heap) < k:
            # Heap noch nicht voll
            heapq.heappush(heap, -item)  # Negiert für Max-Heap
        elif item < -heap[0]:  # item ist kleiner als größtes im Heap
            heapq.heapreplace(heap, -item)
    
    # Zurück konvertieren und sortieren
    return sorted([-x for x in heap])

# Test mit Generator (simuliert Stream)
def number_stream():
    """Simuliert Daten-Stream"""
    import random
    for _ in range(1000000):
        yield random.randint(1, 10000)

k_smallest = find_k_smallest_streaming(number_stream(), 10)
print(f"10 kleinste aus 1M Elementen: {k_smallest}")
```

💡 **Vorteil:** Verwendet nur O(k) Speicher statt O(n)!

### Beispiel 2: Merge k sortierte Listen

```python
import heapq

def merge_sorted_lists(lists):
    """
    Merged k sortierte Listen effizient.
    Zeitkomplexität: O(N log k) wobei N = Gesamtzahl Elemente, k = Anzahl Listen
    """
    # Heap enthält: (wert, listen_index, element_index)
    heap = []
    
    # Initialisiere Heap mit erstem Element jeder Liste
    for i, lst in enumerate(lists):
        if lst:
            heapq.heappush(heap, (lst[0], i, 0))
    
    result = []
    
    while heap:
        val, list_idx, element_idx = heapq.heappop(heap)
        result.append(val)
        
        # Füge nächstes Element aus derselben Liste hinzu
        if element_idx + 1 < len(lists[list_idx]):
            next_val = lists[list_idx][element_idx + 1]
            heapq.heappush(heap, (next_val, list_idx, element_idx + 1))
    
    return result

# Test
lists = [
    [1, 4, 7, 10],
    [2, 5, 8, 11],
    [3, 6, 9, 12]
]

merged = merge_sorted_lists(lists)
print(merged)  # [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12]
```

### Beispiel 3: Median aus Stream

```python
import heapq

class MedianFinder:
    """
    Findet laufenden Median aus Daten-Stream.
    Verwendet zwei Heaps: Max-Heap für untere Hälfte, Min-Heap für obere Hälfte.
    """
    
    def __init__(self):
        # Max-Heap für untere Hälfte (negierte Werte)
        self.lower = []
        # Min-Heap für obere Hälfte
        self.upper = []
    
    def add_number(self, num):
        """Fügt Zahl zum Stream hinzu - O(log n)"""
        # Füge zu lower hinzu (als Max-Heap via Negation)
        heapq.heappush(self.lower, -num)
        
        # Balanciere: größtes von lower muss kleiner sein als kleinstes von upper
        if self.lower and self.upper and -self.lower[0] > self.upper[0]:
            val = -heapq.heappop(self.lower)
            heapq.heappush(self.upper, val)
        
        # Größen-Balance: Differenz maximal 1
        if len(self.lower) > len(self.upper) + 1:
            val = -heapq.heappop(self.lower)
            heapq.heappush(self.upper, val)
        elif len(self.upper) > len(self.lower) + 1:
            val = heapq.heappop(self.upper)
            heapq.heappush(self.lower, -val)
    
    def find_median(self):
        """Gibt aktuellen Median zurück - O(1)"""
        if len(self.lower) > len(self.upper):
            return -self.lower[0]
        elif len(self.upper) > len(self.lower):
            return self.upper[0]
        else:
            return (-self.lower[0] + self.upper[0]) / 2

# Test
mf = MedianFinder()
numbers = [5, 15, 1, 3, 8, 7, 9, 10, 20, 12]

for num in numbers:
    mf.add_number(num)
    print(f"Nach Hinzufügen von {num:2d}: Median = {mf.find_median()}")

# Ausgabe:
# Nach Hinzufügen von  5: Median = 5
# Nach Hinzufügen von 15: Median = 10.0
# Nach Hinzufügen von  1: Median = 5
# Nach Hinzufügen von  3: Median = 4.0
# Nach Hinzufügen von  8: Median = 5
# ...
```

## Häufige Fallstricke

### ❌ Fallstrick 1: Heap-Eigenschaft nach manueller Änderung

```python
import heapq

heap = [1, 3, 2, 5, 4]
print(heap)  # [1, 3, 2, 5, 4] - Heap-Eigenschaft erfüllt

# Manuelle Änderung
heap[0] = 10  # Bricht Heap-Eigenschaft!
print(heap)  # [10, 3, 2, 5, 4] - KEIN gültiger Heap mehr!

# heappop würde falsches Ergebnis liefern
# heapq.heappop(heap)  # Gibt 10 zurück, aber 2 ist kleiner!
```

✅ **Richtig:**
```python
# Nach manueller Änderung: Heap neu erstellen
heapq.heapify(heap)
print(heap)  # [2, 3, 10, 5, 4] - Heap-Eigenschaft wiederhergestellt
```

### ❌ Fallstrick 2: Max-Heap mit Min-Heap verwechseln

```python
import heapq

# heapq ist immer Min-Heap!
heap = [5, 3, 7, 1]
heapq.heapify(heap)
print(heapq.heappop(heap))  # 1 (kleinstes, nicht größtes!)
```

✅ **Richtig für Max-Heap:**
```python
# Negiere Werte für Max-Heap-Verhalten
heap = [5, 3, 7, 1]
max_heap = [-x for x in heap]
heapq.heapify(max_heap)
print(-heapq.heappop(max_heap))  # 7 (größtes)
```

### ❌ Fallstrick 3: Slicing oder Indexing des Heaps

```python
import heapq

heap = [1, 3, 2, 5, 4, 6, 7]

# Nicht sortiert!
print(heap[1:4])  # [3, 2, 5] - keine sortierte Teil-Liste
```

✅ **Richtig:**
```python
# Für sortierte Teil-Liste: nsmallest verwenden
result = heapq.nsmallest(3, heap)
print(result)  # [1, 2, 3]
```

## Performance-Zusammenfassung

| Operation | Zeitkomplexität | Anmerkung |
|-----------|----------------|-----------|
| `heappush(heap, item)` | O(log n) | Element hinzufügen |
| `heappop(heap)` | O(log n) | Kleinstes entfernen |
| `heappushpop(heap, item)` | O(log n) | Push + Pop kombiniert |
| `heapreplace(heap, item)` | O(log n) | Pop + Push kombiniert |
| `heapify(list)` | O(n) | Liste zu Heap |
| `nsmallest(k, iterable)` | O(n log k) | k kleinste Elemente |
| `nlargest(k, iterable)` | O(n log k) | k größte Elemente |
| `heap[0]` | O(1) | Kleinstes Element ansehen |

**Speicherkomplexität:** O(n)

## Übungsaufgaben

### Übung 1: Top K Häufige Elemente (Mittel)

Finde die k häufigsten Elemente in einer Liste mit heapq.

```python
import heapq
from collections import Counter

def top_k_frequent(nums, k):
    """Findet k häufigste Elemente"""
    # Dein Code hier
    pass

# Test
print(top_k_frequent([1, 1, 1, 2, 2, 3], k=2))  # [1, 2]
print(top_k_frequent([1], k=1))  # [1]
```

**Erwarteter Output:**
```
[1, 2]
[1]
```

### Übung 2: Kth Largest Element (Mittel)

Finde das k-t größte Element in einem Array.

```python
import heapq

def find_kth_largest(nums, k):
    """Findet k-t größtes Element"""
    # Dein Code hier
    pass

# Test
print(find_kth_largest([3, 2, 1, 5, 6, 4], k=2))  # 5
print(find_kth_largest([3, 2, 3, 1, 2, 4, 5, 5, 6], k=4))  # 4
```

**Erwarteter Output:**
```
5
4
```

### Übung 3: Merge Intervals (Schwer)

Merge überlappende Intervalle mit Hilfe eines Heaps.

```python
import heapq

def merge_intervals(intervals):
    """Merged überlappende Intervalle"""
    # Dein Code hier
    pass

# Test
print(merge_intervals([[1, 3], [2, 6], [8, 10], [15, 18]]))
# [[1, 6], [8, 10], [15, 18]]
```

**Erwarteter Output:**
```
[[1, 6], [8, 10], [15, 18]]
```

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [heapq - Heap queue algorithm](https://docs.python.org/3/library/heapq.html) - Vollständige API-Dokumentation
- [heapq Source Code](https://github.com/python/cpython/blob/main/Lib/heapq.py) - Implementierung verstehen
- [Priority Queue recipes](https://docs.python.org/3/library/heapq.html#priority-queue-implementation-notes) - Praktische Patterns

## Verwandte Themen

- [[04_Fortgeschritten/03_Heap_Queue/02_Priority Queues|Priority Queues]] - Anwendung von Heaps
- [[04_Fortgeschritten/03_Heap_Queue/03_Übungen Heapq|Übungen Heapq]] - Mehr Praxis
- [[02_Sequenzen/01_Listen/05_List Performance|List Performance]] - Performance-Vergleich
- [[04_Fortgeschritten/02_Deque/01_Deque Basics|Deque Basics]] - Alternative für Queues

---
← [[04_Fortgeschritten/02_Deque/04_Übungen Deque|Übungen Deque]] | [[INDEX|📑 Index]] | [[04_Fortgeschritten/03_Heap_Queue/02_Priority Queues|Priority Queues]]