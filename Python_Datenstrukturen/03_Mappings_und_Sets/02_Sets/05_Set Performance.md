---
tags: [python, datenstrukturen, sets, performance, optimization]
created: 2025-10-01
---

← [[03_Mappings_und_Sets/02_Sets/04_Set Comprehensions|Set Comprehensions]] | [[INDEX|📑 Index]] | [[03_Mappings_und_Sets/02_Sets/06_Übungen Sets|Übungen Sets]] →

# Set Performance

> [!tip] Lernziel
> Nach dieser Seite verstehst du die Performance-Charakteristiken von Sets im Detail und kannst sie optimal für schnelle Lookups und Mengenoperationen nutzen.

## Zeit-Komplexität: Big-O-Notation

### Grundlegende Operationen

| Operation | Average Case | Worst Case | Bemerkung |
|-----------|--------------|------------|-----------|
| `x in s` | O(1) | O(n) | Membership-Test |
| `s.add(x)` | O(1) | O(n) | Element hinzufügen |
| `s.remove(x)` | O(1) | O(n) | Element entfernen |
| `s.discard(x)` | O(1) | O(n) | Sicheres Entfernen |
| `s.pop()` | O(1) | O(1) | Arbiträres Element |
| `len(s)` | O(1) | O(1) | Größe |
| `s.clear()` | O(1) | O(1) | Alle löschen |
| `for x in s` | O(n) | O(n) | Iteration |
| `s.copy()` | O(n) | O(n) | Shallow Copy |

**Worst Case** nur bei extremen Hash-Kollisionen (sehr selten).

### Set-Operationen

| Operation | Average Case | Worst Case | Bemerkung |
|-----------|--------------|------------|-----------|
| `s \| t` (Union) | O(len(s)+len(t)) | O(len(s)*len(t)) | Alle Elemente |
| `s & t` (Intersection) | O(min(len(s),len(t))) | O(len(s)*len(t)) | Gemeinsame |
| `s - t` (Difference) | O(len(s)) | O(len(s)*len(t)) | In s, nicht in t |
| `s ^ t` (Sym. Diff.) | O(len(s)+len(t)) | O(len(s)*len(t)) | XOR |
| `s <= t` (Subset) | O(len(s)) | O(len(s)) | Teilmenge |
| `s.isdisjoint(t)` | O(min(len(s),len(t))) | O(len(s)*len(t)) | Keine Überschneidung |

```python
import timeit

# O(1) Membership - unabhängig von Set-Größe
def test_membership(size):
    s = set(range(size))
    
    def lookup():
        return size // 2 in s
    
    time = timeit.timeit(lookup, number=100000)
    return time

# Tests mit verschiedenen Größen
for size in [100, 1000, 10000, 100000]:
    time = test_membership(size)
    print(f"Size {size:6d}: {time:.4f}s")

# Ausgabe zeigt: Zeit ist nahezu konstant!
# Size    100: 0.0045s
# Size   1000: 0.0046s
# Size  10000: 0.0047s
# Size 100000: 0.0048s
```

---

## Set vs. andere Datenstrukturen

### Set vs. List (Membership-Test)

```python
import timeit

# Setup: Große Datenmengen
sizes = [100, 1000, 10000]

for size in sizes:
    data = list(range(size))
    s = set(data)
    l = data.copy()
    
    # Suche nach letztem Element (Worst Case für Liste)
    target = size - 1
    
    # Set: O(1)
    set_time = timeit.timeit(
        lambda: target in s,
        number=10000
    )
    
    # List: O(n)
    list_time = timeit.timeit(
        lambda: target in l,
        number=10000
    )
    
    print(f"\nSize: {size}")
    print(f"  Set:  {set_time:.4f}s (O(1))")
    print(f"  List: {list_time:.4f}s (O(n))")
    print(f"  Set ist {list_time/set_time:.0f}x schneller!")

# Bei size=10000: Set ist ~1000x schneller!
```

### Set vs. Dict (Nur Membership)

```python
import timeit

size = 10000
data = range(size)

s = set(data)
d = {x: None for x in data}  # Dict mit dummy values

# Membership-Test
def set_test():
    return 5000 in s

def dict_test():
    return 5000 in d

set_time = timeit.timeit(set_test, number=100000)
dict_time = timeit.timeit(dict_test, number=100000)

print(f"Set:  {set_time:.4f}s")
print(f"Dict: {dict_time:.4f}s")
print(f"Unterschied: {abs(set_time-dict_time)/min(set_time,dict_time)*100:.1f}%")

# Nahezu identisch - beide verwenden Hash-Tables
# Aber: Set braucht weniger Speicher (keine Values)
```

### Speicher-Vergleich

```python
import sys

size = 10000
data = list(range(size))

# Verschiedene Datenstrukturen
s = set(data)
l = data.copy()
d = {x: None for x in data}
t = tuple(data)

print("Speicherverbrauch für 10,000 Elemente:")
print(f"  Set:   {sys.getsizeof(s):,} bytes")
print(f"  List:  {sys.getsizeof(l):,} bytes")
print(f"  Dict:  {sys.getsizeof(d):,} bytes")
print(f"  Tuple: {sys.getsizeof(t):,} bytes")

# Ungefähre Ausgabe:
# Set:   524,528 bytes
# List:   87,624 bytes
# Dict:  524,528 bytes
# Tuple:  80,056 bytes

# Set braucht mehr als Liste/Tuple (Hash-Table)
# Aber: O(1) Lookup statt O(n)!
```

---

## Hash-Table Implementation Details

### Load Factor und Resizing

```python
"""
PYTHON SET INTERNALS:

1. Initial Size: 8 Slots
2. Load Factor: ~2/3 (0.67)
3. Resize: Wenn zu voll
4. Growth: 4x für kleine, 2x für große Sets

Resize-Schwellen (ungefähr):
- 6 Elemente → Resize auf 32
- 22 Elemente → Resize auf 64
- 43 Elemente → Resize auf 128
"""

import sys

s = set()
prev_size = sys.getsizeof(s)

print(f"{'Elemente':<10} | {'Bytes':<10} | {'Änderung'}")
print("-" * 40)

for i in range(30):
    s.add(i)
    current_size = sys.getsizeof(s)
    change = "RESIZE!" if current_size > prev_size else ""
    print(f"{len(s):<10} | {current_size:<10} | {change}")
    prev_size = current_size

# Zeigt Resize-Punkte:
# 0 → 224 bytes (initial)
# 6 → 736 bytes (resize)
# 11 → 2272 bytes (resize)
# 22 → 8416 bytes (resize)
```

### Hash-Kollisionen vermeiden

```python
# Gute Hash-Verteilung ist wichtig für Performance

# ✅ GUTE Hash-Verteilung
class GoodHash:
    def __init__(self, value):
        self.value = value
    
    def __hash__(self):
        # Python's eingebauter Hash ist gut
        return hash(self.value)
    
    def __eq__(self, other):
        return self.value == other.value

# ❌ SCHLECHTE Hash-Verteilung
class BadHash:
    def __init__(self, value):
        self.value = value
    
    def __hash__(self):
        # Alle Objekte haben denselben Hash - viele Kollisionen!
        return 42
    
    def __eq__(self, other):
        return self.value == other.value

# Performance-Test
import timeit

def test_hash_class(hash_class, size):
    s = {hash_class(i) for i in range(size)}
    target = hash_class(size // 2)
    
    def lookup():
        return target in s
    
    return timeit.timeit(lookup, number=10000)

size = 1000
good_time = test_hash_class(GoodHash, size)
bad_time = test_hash_class(BadHash, size)

print(f"Gute Hash-Verteilung: {good_time:.4f}s")
print(f"Schlechte Hash-Verteilung: {bad_time:.4f}s")
print(f"Schlechte ist {bad_time/good_time:.1f}x langsamer!")
```

---

## Performance-Optimierungen

### 1. Set Comprehension vs. Loop

```python
import timeit

data = range(10000)

# Mit Loop
def mit_loop():
    result = set()
    for x in data:
        if x % 2 == 0:
            result.add(x)
    return result

# Mit Comprehension
def mit_comprehension():
    return {x for x in data if x % 2 == 0}

# Benchmark
loop_time = timeit.timeit(mit_loop, number=1000)
comp_time = timeit.timeit(mit_comprehension, number=1000)

print(f"Loop:         {loop_time:.3f}s")
print(f"Comprehension: {comp_time:.3f}s")
print(f"Speedup:      {loop_time/comp_time:.2f}x")

# Comprehension ist ~20-30% schneller!
```

### 2. Richtige Set-Operation wählen

```python
import timeit

# Zwei Sets
a = set(range(10000))
b = set(range(5000, 15000))

# Intersection ist schneller als manueller Check
def manuell():
    result = set()
    for x in a:
        if x in b:
            result.add(x)
    return result

def mit_operator():
    return a & b

# Benchmark
manual_time = timeit.timeit(manuell, number=1000)
operator_time = timeit.timeit(mit_operator, number=1000)

print(f"Manuell:      {manual_time:.3f}s")
print(f"& Operator:   {operator_time:.3f}s")
print(f"Speedup:      {manual_time/operator_time:.2f}x")

# Operator ist deutlich schneller - optimiert in C!
```

### 3. Duplikat-Entfernung: Set vs. andere Methoden

```python
import timeit

# Daten mit vielen Duplikaten
data = [i % 100 for i in range(10000)]

# Methode 1: Set
def mit_set():
    return list(set(data))

# Methode 2: Dict.fromkeys (behält Reihenfolge)
def mit_dict():
    return list(dict.fromkeys(data))

# Methode 3: Manuell mit gesehen-Set
def manuell():
    gesehen = set()
    result = []
    for x in data:
        if x not in gesehen:
            gesehen.add(x)
            result.append(x)
    return result

# Benchmark
set_time = timeit.timeit(mit_set, number=1000)
dict_time = timeit.timeit(mit_dict, number=1000)
manual_time = timeit.timeit(manuell, number=1000)

print(f"set():         {set_time:.3f}s")
print(f"dict.fromkeys(): {dict_time:.3f}s")
print(f"Manuell:       {manual_time:.3f}s")

# set() ist am schnellsten!
# dict.fromkeys() wenn Reihenfolge wichtig
```

### 4. Pre-allocation

```python
import timeit

data = range(10000)

# Ohne Pre-allocation
def ohne_prealloc():
    s = set()
    for x in data:
        s.add(x)
    return s

# Mit Pre-allocation (wenn möglich)
def mit_prealloc():
    return set(data)  # Bulk-Operation

# Benchmark
ohne_time = timeit.timeit(ohne_prealloc, number=1000)
mit_time = timeit.timeit(mit_prealloc, number=1000)

print(f"Ohne Pre-alloc: {ohne_time:.3f}s")
print(f"Mit Pre-alloc:  {mit_time:.3f}s")
print(f"Speedup:        {ohne_time/mit_time:.2f}x")

# Bulk-Operationen sind schneller!
```

### 5. update() vs. |=

```python
import timeit

a = set(range(5000))
b = set(range(5000, 10000))

# Mit update()
def mit_update():
    s = a.copy()
    s.update(b)
    return s

# Mit |= Operator
def mit_operator():
    s = a.copy()
    s |= b
    return s

# Mit | und Neuzuweisung
def mit_new():
    return a | b

# Benchmark
update_time = timeit.timeit(mit_update, number=10000)
operator_time = timeit.timeit(mit_operator, number=10000)
new_time = timeit.timeit(mit_new, number=10000)

print(f"update():  {update_time:.3f}s")
print(f"|=:        {operator_time:.3f}s")
print(f"| (neu):   {new_time:.3f}s")

# update() und |= sind ähnlich schnell
# | erstellt neues Set (langsamer wenn nicht nötig)
```

---

## Best Practices für Performance

### ✅ DO: Verwende Sets für Membership-Tests

```python
# ❌ LANGSAM - Liste für Membership
erlaubte_ids = [1, 2, 3, 4, 5, ...., 1000]  # O(n) Lookup

if user_id in erlaubte_ids:  # Langsam bei großen Listen!
    allow_access()

# ✅ SCHNELL - Set für Membership
erlaubte_ids = {1, 2, 3, 4, 5, ...., 1000}  # O(1) Lookup

if user_id in erlaubte_ids:  # Immer schnell!
    allow_access()
```

### ✅ DO: Nutze Set-Operationen statt Loops

```python
a = {1, 2, 3, 4, 5}
b = {4, 5, 6, 7, 8}

# ❌ LANGSAM - Manueller Loop
gemeinsam = set()
for x in a:
    if x in b:
        gemeinsam.add(x)

# ✅ SCHNELL - Set-Operation
gemeinsam = a & b  # Viel schneller!
```

### ✅ DO: Bulk-Operationen verwenden

```python
# ❌ LANGSAM - Element für Element
s = set()
for item in large_list:
    s.add(item)

# ✅ SCHNELL - Bulk-Operation
s = set(large_list)

# ODER: update() für mehrere
s = set()
s.update(list1, list2, list3)  # Effizienter als viele add()
```

### ✅ DO: Richtige Datenstruktur wählen

```python
# Für Membership ohne Reihenfolge: Set
active_users = {123, 456, 789}

# Für Membership mit Values: Dict
user_data = {123: "Alice", 456: "Bob"}

# Für Reihenfolge wichtig: List
ordered_items = [1, 2, 3, 4, 5]

# Für Immutability: frozenset
CONSTANTS = frozenset(['a', 'b', 'c'])
```

### ❌ DON'T: Sets für kleine Datenmengen

```python
# Bei sehr kleinen Mengen (< 5 Elemente) kann Liste schneller sein
# wegen Overhead der Hash-Table

import timeit

# Sehr kleine Menge
small_data = [1, 2, 3]

# Set
s = set(small_data)
set_time = timeit.timeit(lambda: 2 in s, number=1000000)

# List
l = small_data
list_time = timeit.timeit(lambda: 2 in l, number=1000000)

print(f"Set:  {set_time:.4f}s")
print(f"List: {list_time:.4f}s")

# Bei < 5 Elementen ist Liste oft schneller!
# Ab ~10 Elementen wird Set besser
```

### ❌ DON'T: Unnötige Konvertierungen

```python
# ❌ INEFFIZIENT - Mehrfache Konvertierung
data = [1, 2, 3, 4, 5]
s = set(data)      # List → Set
l = list(s)        # Set → List
s2 = set(l)        # List → Set wieder
# Jede Konvertierung kostet Zeit!

# ✅ EFFIZIENTER - Direkt verwenden
data = [1, 2, 3, 4, 5]
unique_data = set(data)  # Nur eine Konvertierung
```

---

## Profiling von Set-Code

### Mit timeit

```python
import timeit

# Code zu testen
code = """
s = set()
for i in range(1000):
    s.add(i)
    if i % 2 == 0:
        s.discard(i)
"""

time = timeit.timeit(code, number=1000)
print(f"Zeit: {time:.3f}s")
```

### Mit cProfile

```python
import cProfile
import pstats

def set_operations():
    # Verschiedene Set-Operationen
    a = set(range(10000))
    b = set(range(5000, 15000))
    
    # Union
    c = a | b
    
    # Intersection
    d = a & b
    
    # Viele Lookups
    for i in range(1000):
        _ = i in a
    
    return c, d

# Profile
profiler = cProfile.Profile()
profiler.enable()
result = set_operations()
profiler.disable()

# Statistiken
stats = pstats.Stats(profiler)
stats.sort_stats('cumulative')
stats.print_stats(10)
```

### Memory Profiling

```python
import sys

# Verschiedene Set-Größen
sizes = [10, 100, 1000, 10000]

print(f"{'Elemente':<10} | {'Bytes':<12} | {'Bytes/Element'}")
print("-" * 45)

for size in sizes:
    s = set(range(size))
    total = sys.getsizeof(s)
    per_item = total / size if size > 0 else 0
    
    print(f"{size:<10} | {total:<12,} | {per_item:.1f}")

# Zeigt: Bytes/Element sinkt mit größeren Sets
# (bessere Ausnutzung der Hash-Table)
```

---

## Vergleich: Verschiedene Mengenoperationen

### Performance-Matrix

```python
import timeit

# Setup
size_a = 10000
size_b = 10000
a = set(range(size_a))
b = set(range(size_a // 2, size_a + size_a // 2))

operations = {
    "Union (|)": lambda: a | b,
    "Intersection (&)": lambda: a & b,
    "Difference (-)": lambda: a - b,
    "Sym. Diff. (^)": lambda: a ^ b,
    "Subset (<=)": lambda: {1, 2} <= a,
    "Disjoint": lambda: a.isdisjoint({-1, -2})
}

print(f"{'Operation':<20} | {'Zeit (ms)'}")
print("-" * 40)

for name, op in operations.items():
    time = timeit.timeit(op, number=1000) * 1000
    print(f"{name:<20} | {time:>8.2f}")

# Intersection ist oft am schnellsten (früher Abbruch möglich)
```

---

## Spezielle Performance-Szenarien

### 1. Sehr große Sets

```python
import timeit

# Bei sehr großen Sets: Achte auf Memory
def test_large_set(size):
    start_mem = 0  # Vereinfacht
    
    s = set(range(size))
    
    # Membership bleibt O(1)
    lookup_time = timeit.timeit(
        lambda: size // 2 in s,
        number=10000
    )
    
    return lookup_time

# Auch bei Millionen Elementen: O(1)
for size in [10_000, 100_000, 1_000_000]:
    time = test_large_set(size)
    print(f"{size:,} Elemente: {time:.4f}s")
```

### 2. Viele kleine Operationen

```python
import timeit

# Viele add() vs. eine update()
items = list(range(1000))

def viele_adds():
    s = set()
    for item in items:
        s.add(item)
    return s

def eine_update():
    s = set()
    s.update(items)
    return s

add_time = timeit.timeit(viele_adds, number=1000)
update_time = timeit.timeit(eine_update, number=1000)

print(f"Viele add():  {add_time:.3f}s")
print(f"Eine update(): {update_time:.3f}s")
print(f"Speedup:      {add_time/update_time:.2f}x")
```

### 3. Set-Operationen mit unterschiedlichen Größen

```python
import timeit

# Intersection: Kleineres Set zuerst iterieren
small = set(range(100))
large = set(range(10000))

# Python optimiert automatisch
time1 = timeit.timeit(lambda: small & large, number=10000)
time2 = timeit.timeit(lambda: large & small, number=10000)

print(f"small & large: {time1:.4f}s")
print(f"large & small: {time2:.4f}s")
print(f"Unterschied: {abs(time1-time2)/min(time1,time2)*100:.1f}%")

# Python wählt automatisch den optimalen Weg!
```

---

## Anti-Patterns vermeiden

### Anti-Pattern 1: Liste wenn Set besser

```python
# ❌ SCHLECHT - Liste für Lookups
blacklist = [user1, user2, user3, ..., user1000]

for request in requests:
    if request.user in blacklist:  # O(n) - langsam!
        block()

# ✅ GUT - Set für Lookups
blacklist = {user1, user2, user3, ..., user1000}

for request in requests:
    if request.user in blacklist:  # O(1) - schnell!
        block()
```

### Anti-Pattern 2: Set wenn Reihenfolge wichtig

```python
# ❌ SCHLECHT - Set wenn Reihenfolge wichtig
tasks = {"task3", "task1", "task2"}
for task in tasks:
    process(task)  # Reihenfolge nicht garantiert!

# ✅ GUT - Liste wenn Reihenfolge wichtig
tasks = ["task1", "task2", "task3"]
for task in tasks:
    process(task)  # Reihenfolge erhalten
```

### Anti-Pattern 3: Unnötige Set-Konvertierung in Loop

```python
# ❌ SCHLECHT - Set-Konvertierung in Loop
for item in items:
    if item in set(allowed_items):  # Set-Erstellung jedes Mal!
        process(item)

# ✅ GUT - Set einmal erstellen
allowed_set = set(allowed_items)
for item in items:
    if item in allowed_set:
        process(item)
```

---

## Zusammenfassung: Performance-Checkliste

```python
"""
✅ SET PERFORMANCE CHECKLISTE

1. WANN SET VERWENDEN
   □ Membership-Tests (x in set) benötigt
   □ Duplikat-Entfernung erforderlich
   □ Mengenoperationen (Union, Intersection, etc.)
   □ Mehr als ~10 Elemente

2. OPTIMIERUNGEN
   □ Set Comprehensions statt Loops
   □ Built-in Operatoren (&, |, -, ^) nutzen
   □ Bulk-Operationen (update, set()) bevorzugen
   □ Richtige Operation wählen (intersection früher Abbruch)

3. VERMEIDEN
   □ Sets für kleine Datenmengen (< 5 Elemente)
   □ Sets wenn Reihenfolge wichtig
   □ Unnötige Konvertierungen
   □ Set-Erstellung in Loops

4. ALTERNATIVEN
   □ Liste: Wenn Reihenfolge wichtig
   □ Dict: Wenn Values benötigt
   □ frozenset: Wenn Immutability nötig

5. PROFILING
   □ timeit für Benchmarks
   □ cProfile für Funktionsprofile
   □ sys.getsizeof() für Memory
"""
```

---

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [Time Complexity](https://wiki.python.org/moin/TimeComplexity) - Komplexität aller Operationen
- [Set Types](https://docs.python.org/3/library/stdtypes.html#set-types-set-frozenset) - Set Referenz
- [Performance Tips](https://wiki.python.org/moin/PythonSpeed/PerformanceTips) - Offizielle Tips
- [timeit module](https://docs.python.org/3/library/timeit.html) - Für Benchmarking

---

## Verwandte Themen

- [[03_Mappings_und_Sets/02_Sets/01_Set Basics|Set Basics]] - Grundlagen wiederholen
- [[03_Mappings_und_Sets/02_Sets/02_Set Operationen|Set Operationen]] - Operations-Performance
- [[03_Mappings_und_Sets/01_Dictionaries/07_Dict Performance|Dict Performance]] - Ähnliche Datenstruktur
- [[06_Praxis/02_Performance/01_Performance Messung|Performance Messung]] - Allgemeine Techniken

---

← [[03_Mappings_und_Sets/02_Sets/04_Set Comprehensions|Set Comprehensions]] | [[INDEX|📑 Index]] | [[03_Mappings_und_Sets/02_Sets/06_Übungen Sets|Übungen Sets]] →