---
tags: [python, datenstrukturen, list, performance, optimization]
created: 2025-10-01
---

← [[02_Sequenzen/01_Listen/04_Nested Lists|Nested Lists]] | [[INDEX|📑 Index]] | [[02_Sequenzen/01_Listen/06_Übungen Listen|Übungen Listen]] →

# List Performance - Optimierung und Best Practices

> [!tip] Lernziel
> Nach dieser Seite verstehst du die Performance-Charakteristiken von Listen im Detail, kannst Engpässe identifizieren und weißt, wie du Listen-Operationen optimal einsetzt.

## Performance-Übersicht

### Zeitkomplexität aller Operationen

| Operation | Average Case | Worst Case | Beschreibung |
|-----------|--------------|------------|--------------|
| **Zugriff** |
| `list[i]` | O(1) | O(1) | Index-Zugriff |
| `list[i] = x` | O(1) | O(1) | Index-Zuweisung |
| **Suche** |
| `x in list` | O(n) | O(n) | Lineares Suchen |
| `list.index(x)` | O(n) | O(n) | Index finden |
| `list.count(x)` | O(n) | O(n) | Vorkommen zählen |
| **Hinzufügen** |
| `list.append(x)` | O(1) | O(n) | Am Ende anfügen |
| `list.extend(iter)` | O(k) | O(k) | k Elemente anfügen |
| `list.insert(0, x)` | O(n) | O(n) | Am Anfang einfügen |
| `list.insert(i, x)` | O(n) | O(n) | An Position einfügen |
| **Entfernen** |
| `list.pop()` | O(1) | O(1) | Letztes Element |
| `list.pop(0)` | O(n) | O(n) | Erstes Element |
| `list.pop(i)` | O(n) | O(n) | An Position |
| `list.remove(x)` | O(n) | O(n) | Nach Wert |
| `del list[i]` | O(n) | O(n) | An Position |
| `list.clear()` | O(n) | O(n) | Alle entfernen |
| **Sonstiges** |
| `len(list)` | O(1) | O(1) | Länge |
| `list.copy()` | O(n) | O(n) | Flache Kopie |
| `list.sort()` | O(n log n) | O(n log n) | Timsort |
| `list.reverse()` | O(n) | O(n) | Umkehren |
| `list * k` | O(k*n) | O(k*n) | Wiederholen |
| `list1 + list2` | O(n+m) | O(n+m) | Konkatenation |

---

## Memory-Layout und Over-Allocation

### Wie Listen im Speicher wachsen

```python
import sys

def analyze_growth():
    """Analysiert wie Liste wächst"""
    lst = []
    prev_size = sys.getsizeof(lst)
    
    print(f"{'Länge':<8} {'Bytes':<12} {'Kapazität':<12} {'Wachstum'}")
    print("-" * 50)
    
    for i in range(25):
        lst.append(i)
        size = sys.getsizeof(lst)
        
        if size != prev_size:
            # Kapazität = (size - header) / pointer_size
            capacity = (size - 56) // 8  # 56 Bytes Header, 8 Bytes pro Zeiger
            growth = size - prev_size
            print(f"{len(lst):<8} {size:<12} {capacity:<12} +{growth}")
            prev_size = size

analyze_growth()
```

**Erwartete Ausgabe (ungefähr):**
```
Länge    Bytes        Kapazität    Wachstum
--------------------------------------------------
1        88           4            +32
5        120          8            +32
9        184          16           +64
17       248          24           +64
25       312          32           +64
```

**Wachstumsstrategie:**
- Python allokiert **mehr Speicher als nötig** (over-allocation)
- Neue Kapazität ≈ `alte_kapazität + (alte_kapazität >> 3) + 6`
- Das entspricht etwa **12,5% Wachstum** plus Konstante
- Vermeidet häufige Reallokationen → amortisiert O(1) für `append()`

```python
def estimate_new_capacity(current):
    """Schätzt neue Kapazität beim Wachstum"""
    return current + (current >> 3) + 6

# Typisches Wachstumsmuster
capacity = 0
print("Kapazität:  ", end="")
for _ in range(10):
    capacity = estimate_new_capacity(capacity)
    print(capacity, end=" ")
# Kapazität: 6 13 21 30 40 51 63 77 93 111
```

### Speicherverbrauch minimieren

```python
import sys

# Liste mit Over-Allocation
large_list = []
for i in range(1000):
    large_list.append(i)

print(f"Mit Over-Allocation: {sys.getsizeof(large_list)} Bytes")
# ~8000-9000 Bytes

# Liste ohne Over-Allocation (falls finalisiert)
compact_list = list(range(1000))
print(f"Ohne Over-Allocation: {sys.getsizeof(compact_list)} Bytes")
# ~8000 Bytes

# Speicher zurückgeben mit Slicing
large_list = large_list[:]  # Erstellt neue Liste mit exakter Größe
print(f"Nach Kompaktierung: {sys.getsizeof(large_list)} Bytes")
```

---

## Performance-Benchmarks

### Append vs Insert vs Concatenation

```python
import time

def benchmark_append(n):
    """Benchmark: append() am Ende"""
    lst = []
    start = time.perf_counter()
    for i in range(n):
        lst.append(i)
    return time.perf_counter() - start

def benchmark_insert_start(n):
    """Benchmark: insert() am Anfang"""
    lst = []
    start = time.perf_counter()
    for i in range(n):
        lst.insert(0, i)
    return time.perf_counter() - start

def benchmark_concatenation(n):
    """Benchmark: += Operator"""
    lst = []
    start = time.perf_counter()
    for i in range(n):
        lst += [i]
    return time.perf_counter() - start

def benchmark_plus_operator(n):
    """Benchmark: + Operator (ineffizient!)"""
    lst = []
    start = time.perf_counter()
    for i in range(n):
        lst = lst + [i]  # Erstellt jedes Mal neue Liste!
    return time.perf_counter() - start

# Tests mit verschiedenen Größen
sizes = [1000, 5000, 10000]

for n in sizes:
    print(f"\n=== n = {n} ===")
    
    time_append = benchmark_append(n)
    print(f"append():          {time_append:.4f}s")
    
    time_concat = benchmark_concatenation(n)
    print(f"+= operator:       {time_concat:.4f}s ({time_concat/time_append:.1f}x)")
    
    if n <= 5000:  # Nur für kleine n, sonst zu langsam
        time_insert = benchmark_insert_start(n)
        print(f"insert(0):         {time_insert:.4f}s ({time_insert/time_append:.1f}x)")
    
    if n <= 1000:  # Nur für sehr kleine n
        time_plus = benchmark_plus_operator(n)
        print(f"+ operator:        {time_plus:.4f}s ({time_plus/time_append:.1f}x)")
```

**Typische Ergebnisse:**
```
=== n = 1000 ===
append():          0.0001s
+= operator:       0.0001s (1.2x)
insert(0):         0.0156s (156.0x)  ← SEHR langsam!
+ operator:        0.0234s (234.0x)  ← EXTREM langsam!
```

**Wichtigste Erkenntnisse:**
- ✅ `append()` ist die schnellste Methode
- ✅ `+=` ist fast so schnell wie `append()`
- ❌ `insert(0, x)` ist 100-1000x langsamer
- ❌ `lst = lst + [x]` ist 100-1000x langsamer

---

### List vs Set für Membership Tests

```python
import time
import random

def benchmark_membership(n, lookups=1000):
    """Vergleicht Membership Tests: Liste vs Set"""
    data = list(range(n))
    data_set = set(data)
    
    # Zufällige Werte zum Suchen
    search_values = [random.randint(0, n*2) for _ in range(lookups)]
    
    # Liste
    start = time.perf_counter()
    for val in search_values:
        _ = val in data
    time_list = time.perf_counter() - start
    
    # Set
    start = time.perf_counter()
    for val in search_values:
        _ = val in data_set
    time_set = time.perf_counter() - start
    
    print(f"n={n:>6}, lookups={lookups}")
    print(f"  Liste: {time_list:.4f}s")
    print(f"  Set:   {time_set:.4f}s")
    print(f"  Speedup: {time_list/time_set:.1f}x\n")

# Tests
for n in [100, 1000, 10000, 100000]:
    benchmark_membership(n)
```

**Typische Ergebnisse:**
```
n=   100, lookups=1000
  Liste: 0.0012s
  Set:   0.0001s
  Speedup: 12.0x

n=  1000, lookups=1000
  Liste: 0.0089s
  Set:   0.0001s
  Speedup: 89.0x

n= 10000, lookups=1000
  Liste: 0.0823s
  Set:   0.0001s
  Speedup: 823.0x  ← Set ist 800x schneller!
```

**Faustregel:**
- **< 10 Elemente:** Liste ist ok
- **10-100 Elemente:** Set ist besser
- **> 100 Elemente:** Set ist VIEL besser

---

### List Comprehension vs Loop

```python
import time

def benchmark_methods(n):
    """Vergleicht verschiedene Methoden zur Listen-Erstellung"""
    
    # 1. Traditionelle For-Schleife
    start = time.perf_counter()
    result = []
    for i in range(n):
        result.append(i * 2)
    time_loop = time.perf_counter() - start
    
    # 2. List Comprehension
    start = time.perf_counter()
    result = [i * 2 for i in range(n)]
    time_comp = time.perf_counter() - start
    
    # 3. map() mit lambda
    start = time.perf_counter()
    result = list(map(lambda i: i * 2, range(n)))
    time_map = time.perf_counter() - start
    
    # 4. map() mit Funktion
    def double(x):
        return x * 2
    
    start = time.perf_counter()
    result = list(map(double, range(n)))
    time_map_func = time.perf_counter() - start
    
    print(f"\n=== n = {n} ===")
    print(f"For-Loop:              {time_loop:.4f}s (1.0x)")
    print(f"List Comprehension:    {time_comp:.4f}s ({time_loop/time_comp:.1f}x)")
    print(f"map() mit lambda:      {time_map:.4f}s ({time_loop/time_map:.1f}x)")
    print(f"map() mit Funktion:    {time_map_func:.4f}s ({time_loop/time_map_func:.1f}x)")

for n in [10000, 100000, 1000000]:
    benchmark_methods(n)
```

**Typische Ergebnisse:**
```
=== n = 100000 ===
For-Loop:              0.0089s (1.0x)
List Comprehension:    0.0052s (1.7x)  ← ~70% schneller
map() mit lambda:      0.0078s (1.1x)
map() mit Funktion:    0.0045s (2.0x)  ← ~100% schneller
```

**Wichtigste Erkenntnisse:**
- ✅ **List Comprehension** ist schneller und pythonischer als Loops
- ✅ **map()** mit benannter Funktion ist am schnellsten
- ❌ **map()** mit lambda ist langsamer (Lambda-Overhead)

---

## Optimierungsstrategien

### 1. Richtige Datenstruktur wählen

```python
# ❌ LANGSAM: Liste für viele Membership Tests
def filter_allowed_slow(items, allowed_list):
    return [item for item in items if item in allowed_list]

# ✅ SCHNELL: Set für Membership Tests
def filter_allowed_fast(items, allowed_list):
    allowed_set = set(allowed_list)
    return [item for item in items if item in allowed_set]

# Benchmark
import time
items = list(range(10000))
allowed = list(range(5000))

start = time.perf_counter()
result = filter_allowed_slow(items, allowed)
time_slow = time.perf_counter() - start

start = time.perf_counter()
result = filter_allowed_fast(items, allowed)
time_fast = time.perf_counter() - start

print(f"Liste: {time_slow:.4f}s")
print(f"Set:   {time_fast:.4f}s")
print(f"Speedup: {time_slow/time_fast:.1f}x")
```

### 2. Operationen am richtigen Ende

```python
import time

def operations_at_end(n):
    """Operationen am Ende - SCHNELL"""
    lst = []
    for i in range(n):
        lst.append(i)  # O(1)
    
    for _ in range(n):
        lst.pop()      # O(1)

def operations_at_start(n):
    """Operationen am Anfang - LANGSAM"""
    lst = []
    for i in range(n):
        lst.insert(0, i)  # O(n)
    
    for _ in range(n):
        lst.pop(0)        # O(n)

n = 5000
start = time.perf_counter()
operations_at_end(n)
time_end = time.perf_counter() - start

start = time.perf_counter()
operations_at_start(n)
time_start = time.perf_counter() - start

print(f"Am Ende:   {time_end:.4f}s")
print(f"Am Anfang: {time_start:.4f}s")
print(f"Faktor:    {time_start/time_end:.1f}x langsamer")

# Lösung für Operationen am Anfang: collections.deque
from collections import deque

def operations_with_deque(n):
    """Operationen am Anfang mit deque - SCHNELL"""
    dq = deque()
    for i in range(n):
        dq.appendleft(i)  # O(1)
    
    for _ in range(n):
        dq.popleft()      # O(1)

start = time.perf_counter()
operations_with_deque(n)
time_deque = time.perf_counter() - start

print(f"Deque:     {time_deque:.4f}s")
print(f"Speedup vs Liste: {time_start/time_deque:.1f}x")
```

### 3. Pre-Allocation vermeiden (meistens unnötig)

```python
import time

def without_preallocation(n):
    """Ohne Pre-Allocation - üblicher Weg"""
    lst = []
    for i in range(n):
        lst.append(i)
    return lst

def with_preallocation(n):
    """Mit Pre-Allocation - meistens NICHT besser"""
    lst = [None] * n
    for i in range(n):
        lst[i] = i
    return lst

n = 1000000

start = time.perf_counter()
result1 = without_preallocation(n)
time1 = time.perf_counter() - start

start = time.perf_counter()
result2 = with_preallocation(n)
time2 = time.perf_counter() - start

print(f"Ohne Pre-Allocation: {time1:.4f}s")
print(f"Mit Pre-Allocation:  {time2:.4f}s")
print(f"Unterschied: {abs(time1-time2)/min(time1, time2)*100:.1f}%")

# Fazit: Kaum Unterschied! append() mit Over-Allocation ist sehr effizient
# Pre-Allocation lohnt sich nur in speziellen Fällen
```

### 4. List Comprehension statt Filter + Map

```python
import time

def traditional_approach(data):
    """Traditioneller Ansatz mit filter() und map()"""
    filtered = filter(lambda x: x % 2 == 0, data)
    result = list(map(lambda x: x * 2, filtered))
    return result

def comprehension_approach(data):
    """List Comprehension - pythonischer und schneller"""
    return [x * 2 for x in data if x % 2 == 0]

# Benchmark
data = list(range(100000))

start = time.perf_counter()
result1 = traditional_approach(data)
time1 = time.perf_counter() - start

start = time.perf_counter()
result2 = comprehension_approach(data)
time2 = time.perf_counter() - start

print(f"filter() + map():    {time1:.4f}s")
print(f"List Comprehension:  {time2:.4f}s")
print(f"Speedup: {time1/time2:.1f}x")
```

### 5. Generator Expressions für große Daten

```python
import sys

# Liste - alle Elemente im Speicher
list_comp = [x * 2 for x in range(1000000)]
print(f"Liste: {sys.getsizeof(list_comp) / 1024 / 1024:.2f} MB")

# Generator - lazy evaluation, nur eines zur Zeit
gen_exp = (x * 2 for x in range(1000000))
print(f"Generator: {sys.getsizeof(gen_exp)} Bytes")

# Wenn du nur einmal iterierst: Generator ist besser!
def process_with_list(n):
    data = [x * 2 for x in range(n)]
    total = sum(data)
    return total

def process_with_generator(n):
    data = (x * 2 for x in range(n))
    total = sum(data)
    return total

import time
n = 10000000

start = time.perf_counter()
result1 = process_with_list(n)
time1 = time.perf_counter() - start
print(f"\nMit Liste:     {time1:.4f}s")

start = time.perf_counter()
result2 = process_with_generator(n)
time2 = time.perf_counter() - start
print(f"Mit Generator: {time2:.4f}s")
print(f"Speedup: {time1/time2:.1f}x")
```

---

## Profiling-Techniken

### Einfaches Timing

```python
import time

def simple_timing():
    """Einfaches Performance-Measurement"""
    data = list(range(100000))
    
    # Methode 1: time.perf_counter()
    start = time.perf_counter()
    result = [x * 2 for x in data if x % 2 == 0]
    elapsed = time.perf_counter() - start
    
    print(f"Zeit: {elapsed:.4f} Sekunden")
    print(f"Elemente: {len(result)}")

simple_timing()
```

### timeit Module für präzise Messungen

```python
import timeit

# Setup-Code (wird nur einmal ausgeführt)
setup = """
data = list(range(10000))
"""

# Zu testender Code
code = """
result = [x * 2 for x in data if x % 2 == 0]
"""

# Führe Code 1000x aus und miss Zeit
time_taken = timeit.timeit(code, setup, number=1000)
print(f"Durchschnitt: {time_taken/1000:.6f}s")

# Vergleich mehrerer Ansätze
def compare_approaches():
    """Vergleicht verschiedene Ansätze"""
    setup = "data = list(range(10000))"
    
    approaches = {
        "List Comp": "[x * 2 for x in data if x % 2 == 0]",
        "Filter+Map": "list(map(lambda x: x*2, filter(lambda x: x%2==0, data)))",
        "For Loop": """
result = []
for x in data:
    if x % 2 == 0:
        result.append(x * 2)
"""
    }
    
    for name, code in approaches.items():
        time_taken = timeit.timeit(code, setup, number=100)
        print(f"{name:15} {time_taken:.4f}s")

compare_approaches()
```

### Memory Profiling

```python
import sys
import tracemalloc

def memory_profile():
    """Misst Speicherverbrauch"""
    
    # Starte Memory Tracking
    tracemalloc.start()
    
    # Erstelle große Liste
    data = [i for i in range(1000000)]
    
    # Hole aktuelle und Peak-Nutzung
    current, peak = tracemalloc.get_traced_memory()
    
    print(f"Aktuell: {current / 1024 / 1024:.2f} MB")
    print(f"Peak:    {peak / 1024 / 1024:.2f} MB")
    
    tracemalloc.stop()

memory_profile()

# Vergleich: Liste vs Generator
def compare_memory():
    tracemalloc.start()
    
    # Mit Liste
    snapshot1 = tracemalloc.take_snapshot()
    list_data = [x * 2 for x in range(1000000)]
    snapshot2 = tracemalloc.take_snapshot()
    
    stats = snapshot2.compare_to(snapshot1, 'lineno')
    for stat in stats[:3]:
        print(stat)
    
    tracemalloc.stop()

compare_memory()
```

---

## Best Practices Zusammenfassung

### ✅ DO - Effiziente Patterns

```python
# 1. append() für schrittweisen Aufbau
result = []
for item in data:
    result.append(process(item))

# 2. List Comprehension für Transformationen
result = [process(item) for item in data]

# 3. Set für Membership Tests
allowed = set(allowed_list)
filtered = [x for x in data if x in allowed]

# 4. Generator Expressions für einmalige Iteration
total = sum(x * 2 for x in large_data)

# 5. Operationen am Ende der Liste
stack = []
stack.append(item)  # Push
item = stack.pop()  # Pop

# 6. slice für Kopien und Subsets
copy = original[:]
subset = original[10:20]

# 7. enumerate() statt range(len())
for i, item in enumerate(items):
    print(f"{i}: {item}")

# 8. zip() für parallele Iteration
for name, age in zip(names, ages):
    print(f"{name}: {age}")
```

### ❌ DON'T - Ineffiziente Patterns

```python
# 1. Vermeiden: lst = lst + [item]
# Erstellt jedes Mal neue Liste - O(n²)
for item in data:
    result = result + [item]  # LANGSAM!

# 2. Vermeiden: insert(0, x) oder pop(0)
# Verschiebt alle Elemente - O(n)
for item in data:
    result.insert(0, item)  # LANGSAM!

# 3. Vermeiden: Liste für viele Membership Tests
# O(n) pro Test
for item in data:
    if item in large_list:  # LANGSAM wenn large_list groß ist!
        process(item)

# 4. Vermeiden: Verschachtelte Loops bei Membership
# O(n*m) - exponentiell!
for item1 in list1:
    for item2 in list2:
        if item1 == item2:  # SEHR LANGSAM!
            result.append(item1)

# 5. Vermeiden: Liste während Iteration modifizieren
for item in lst:
    if condition(item):
        lst.remove(item)  # GEFÄHRLICH und LANGSAM!

# 6. Vermeiden: range(len()) statt enumerate()
for i in range(len(items)):  # Nicht pythonisch
    print(f"{i}: {items[i]}")

# 7. Vermeiden: Mehrfache Konkatenation in Loop
result = []
for chunk in chunks:
    result = result + chunk  # LANGSAM! Nutze extend()
```

---

## Wann Alternative Datenstrukturen?

### Liste vs Alternativen

```python
# Liste ist gut für:
# - Sequentielle Daten mit Index-Zugriff
# - Stack-Operationen (append/pop am Ende)
# - Kleine bis mittlere Datenmengen
# - Heterogene Daten

# Alternativen:

# 1. collections.deque - für Queue und Double-Ended Operations
from collections import deque
queue = deque()
queue.append(1)      # O(1)
queue.appendleft(0)  # O(1) - Liste wäre O(n)!
queue.popleft()      # O(1) - Liste wäre O(n)!

# 2. set - für Membership Tests und Eindeutigkeit
unique_items = set(items)  # Duplikate entfernen
if item in unique_items:   # O(1) statt O(n)!
    process(item)

# 3. dict - für Key-Value Mappings
lookup = {item: value for item, value in pairs}
value = lookup[key]  # O(1) statt O(n)!

# 4. array.array - für numerische Daten
from array import array
numbers = array('i', range(1000))  # Typisiert, weniger Speicher

# 5. NumPy - für große numerische Arrays
import numpy as np
data = np.array(range(1000000))  # Viel effizienter für Mathematik
```

### Entscheidungsbaum

```
Brauche ich...

├─ Reihenfolge wichtig?
│  ├─ Ja → Liste oder deque
│  └─ Nein → set oder dict
│
├─ Häufige Suchen/Membership Tests?
│  ├─ Ja → set oder dict
│  └─ Nein → Liste ok
│
├─ Operationen am Anfang UND Ende?
│  ├─ Ja → deque
│  └─ Nein → Liste ok
│
├─ Nur numerische Daten?
│  ├─ Ja, große Mengen → NumPy
│  ├─ Ja, kleine Mengen → array.array
│  └─ Nein → Liste ok
│
└─ Eindeutige Werte?
   ├─ Ja → set
   └─ Nein → Liste ok
```

---

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [Time Complexity](https://wiki.python.org/moin/TimeComplexity) - Komplette Performance-Tabelle
- [timeit Module](https://docs.python.org/3/library/timeit.html) - Präzise Performance-Messungen
- [tracemalloc](https://docs.python.org/3/library/tracemalloc.html) - Memory Profiling
- [Performance Tips](https://wiki.python.org/moin/PythonSpeed/PerformanceTips) - Offizielle Optimierungstipps

---

## Verwandte Themen

- [[02_Sequenzen/01_Listen/02_List Methoden|List Methoden]] - Alle Methoden im Detail
- [[04_Fortgeschritten/02_Deque/03_Performance Vergleich|Deque Performance]] - Alternative für beide Enden
- [[06_Praxis/02_Performance/01_Performance Messung|Performance Messung]] - Profiling-Techniken
- [[06_Praxis/02_Performance/03_Optimization Strategien|Optimization Strategien]] - Allgemeine Optimierung

---

← [[02_Sequenzen/01_Listen/04_Nested Lists|Nested Lists]] | [[INDEX|📑 Index]] | [[02_Sequenzen/01_Listen/06_Übungen Listen|Übungen Listen]] →