---
tags: [python, datenstrukturen, mappings, dictionaries, performance, optimization]
created: 2025-10-01
---

← [[03_Mappings_und_Sets/01_Dictionaries/06_OrderedDict vs Dict|OrderedDict vs Dict]] | [[INDEX|📑 Index]] | [[03_Mappings_und_Sets/01_Dictionaries/08_Übungen Dictionaries|Übungen Dictionaries]] →

# Dictionary Performance

> [!tip] Lernziel
> Nach dieser Seite verstehst du die Performance-Charakteristiken von Dictionaries im Detail und kannst sie optimal nutzen und optimieren.

## Zeit-Komplexität: Big-O-Notation

### Grundlegende Operationen

| Operation | Average Case | Worst Case | Amortized | Bemerkung |
|-----------|--------------|------------|-----------|-----------|
| `d[key]` | O(1) | O(n) | O(1) | Hash-Lookup |
| `d[key] = value` | O(1) | O(n) | O(1) | Setzen/Update |
| `del d[key]` | O(1) | O(n) | O(1) | Löschen |
| `key in d` | O(1) | O(n) | O(1) | Membership |
| `len(d)` | O(1) | O(1) | O(1) | Größe |
| `d.copy()` | O(n) | O(n) | O(n) | Shallow Copy |
| `d.clear()` | O(1) | O(1) | O(1) | Alle löschen |
| `d.get(key)` | O(1) | O(n) | O(1) | Sicherer Zugriff |
| `d.pop(key)` | O(1) | O(n) | O(1) | Entfernen |
| `d.items()` | O(1) | O(1) | O(1) | View erstellen |
| `for k in d` | O(n) | O(n) | O(n) | Iteration |

**Worst Case** tritt nur bei extremen Hash-Kollisionen auf (sehr selten in der Praxis).

### Code-Beispiele

```python
import timeit

# O(1) Zugriff - unabhängig von Dict-Größe
def test_access(size):
    d = {i: i for i in range(size)}
    
    # Zugriff ist konstant, egal wie groß das Dict ist
    def access():
        return d[size // 2]
    
    time = timeit.timeit(access, number=100000)
    return time

# Tests mit verschiedenen Größen
for size in [100, 1000, 10000, 100000]:
    time = test_access(size)
    print(f"Size {size:6d}: {time:.4f}s")

# Ausgabe zeigt: Zeit ist nahezu konstant!
# Size    100: 0.0050s
# Size   1000: 0.0051s
# Size  10000: 0.0052s
# Size 100000: 0.0053s
```

---

## Hash-Table Implementation

### Wie Dictionaries intern funktionieren

```python
# Vereinfachte Darstellung der internen Struktur

"""
Dictionary = Hash Table + Kompakte Speicherung (seit 3.6+)

1. HASH-BERECHNUNG:
   hash_value = hash(key)
   index = hash_value % table_size

2. SPARSE ARRAY (Indices-Array):
   [hash_index -> entry_index]
   Speichert nur Indizes, nicht die Daten selbst

3. COMPACT ARRAY (Entries-Array):
   [(hash, key, value), (hash, key, value), ...]
   Speichert die eigentlichen Daten kompakt

Vorteile seit Python 3.6+:
- 20-25% weniger Speicher
- Insertion-Order erhalten
- Bessere Cache-Locality
"""

# Hash-Kollisionen
def zeige_kollisionen():
    # Verschiedene Keys können denselben Hash haben (mod table_size)
    keys = ["ab", "ba", "cd"]
    
    for key in keys:
        h = hash(key)
        # Index in einer Table mit Größe 8
        index = h % 8
        print(f"{key}: hash={h}, index={index}")
    
    # Bei Kollision: Open Addressing mit Probing

zeige_kollisionen()
```

### Load Factor und Resizing

```python
"""
LOAD FACTOR = filled_slots / total_slots

Python Dictionary:
- Initial size: 8 Slots
- Resize wenn Load Factor > 2/3 (≈0.67)
- Neue Größe: 4x für kleine Dicts, 2x für große

Beispiel:
1. Start: 8 slots, 0 items → Load Factor = 0
2. Füge 5 items hinzu → Load Factor = 5/8 = 0.625
3. Füge 6. item hinzu → Load Factor = 6/8 = 0.75 > 2/3
4. RESIZE auf 32 slots!
5. Rehash alle items in neue Table
"""

import sys

# Speicher-Wachstum beobachten
d = {}
prev_size = sys.getsizeof(d)

print(f"Items | Memory (bytes) | Growth")
print("-" * 40)

for i in range(20):
    d[i] = i
    current_size = sys.getsizeof(d)
    growth = "RESIZE!" if current_size > prev_size else ""
    print(f"{i+1:5d} | {current_size:14d} | {growth}")
    prev_size = current_size

# Ausgabe zeigt Resize bei bestimmten Schwellen:
# 0 -> 64 bytes (initial)
# 6 -> 240 bytes (resize)
# 11 -> 368 bytes (resize)
# 22 -> 648 bytes (resize)
```

---

## Speicher-Verbrauch

### Memory Overhead

```python
import sys

# Leeres Dictionary
empty = {}
print(f"Leeres Dict: {sys.getsizeof(empty)} bytes")  # ~64 bytes

# Mit Elementen
sizes = [10, 100, 1000, 10000]

for size in sizes:
    d = {i: i for i in range(size)}
    total_bytes = sys.getsizeof(d)
    bytes_per_item = total_bytes / size
    
    print(f"{size:5d} items: {total_bytes:8d} bytes "
          f"({bytes_per_item:.1f} bytes/item)")

# Ausgabe zeigt:
#    10 items:      240 bytes (24.0 bytes/item)
#   100 items:     4704 bytes (47.0 bytes/item)
#  1000 items:    36968 bytes (37.0 bytes/item)
# 10000 items:   327896 bytes (32.8 bytes/item)

# Bytes/item sinkt bei größeren Dicts (bessere Ausnutzung)
```

### Dict vs. Liste vs. Tuple

```python
import sys

# Gleiche Daten in verschiedenen Strukturen
size = 1000

# Als Dictionary
d = {i: i*2 for i in range(size)}

# Als Liste von Tupeln
l = [(i, i*2) for i in range(size)]

# Als zwei separate Listen
keys = list(range(size))
values = [i*2 for i in range(size)]

print(f"Dict:             {sys.getsizeof(d):,} bytes")
print(f"Liste von Tupeln: {sys.getsizeof(l):,} bytes")
print(f"Zwei Listen:      {sys.getsizeof(keys) + sys.getsizeof(values):,} bytes")

# Dict braucht mehr Speicher, aber O(1) Lookup!
# Liste: O(n) für Suche
```

### Keys und Values Speicher

```python
import sys

# Verschiedene Key-Typen
d_int = {i: "value" for i in range(100)}
d_str = {f"key_{i}": "value" for i in range(100)}
d_tuple = {(i, i): "value" for i in range(100)}

print("Key-Typ Memory:")
print(f"  Int keys:   {sys.getsizeof(d_int):,} bytes")
print(f"  String keys: {sys.getsizeof(d_str):,} bytes")
print(f"  Tuple keys:  {sys.getsizeof(d_tuple):,} bytes")

# Strings brauchen mehr Speicher als Ints
# Aber: Lookup-Zeit bleibt O(1)!
```

---

## Performance-Optimierungen

### 1. Dictionary Comprehensions statt Loops

```python
import timeit

# Setup
data = list(range(10000))

# Mit Loop
def mit_loop():
    result = {}
    for x in data:
        result[x] = x**2
    return result

# Mit Comprehension
def mit_comprehension():
    return {x: x**2 for x in data}

# Benchmark
loop_time = timeit.timeit(mit_loop, number=1000)
comp_time = timeit.timeit(mit_comprehension, number=1000)

print(f"Loop:         {loop_time:.3f}s")
print(f"Comprehension: {comp_time:.3f}s")
print(f"Speedup:      {loop_time/comp_time:.2f}x")

# Comprehension ist ~20-30% schneller!
```

### 2. get() vs. if-in-else

```python
import timeit

d = {i: i for i in range(1000)}

# Mit if-in
def mit_if_in():
    if 500 in d:
        return d[500]
    else:
        return None

# Mit get()
def mit_get():
    return d.get(500)

# Benchmark
if_time = timeit.timeit(mit_if_in, number=100000)
get_time = timeit.timeit(mit_get, number=100000)

print(f"if-in:  {if_time:.3f}s")
print(f"get():  {get_time:.3f}s")
print(f"Speedup: {if_time/get_time:.2f}x")

# get() ist schneller - nur ein Lookup statt zwei!
```

### 3. setdefault() vs. if-not-in

```python
import timeit

# Setup
data = list(range(1000))

# Mit if-not-in
def mit_if_not_in():
    d = {}
    for x in data:
        if x not in d:
            d[x] = []
        d[x].append(x)
    return d

# Mit setdefault()
def mit_setdefault():
    d = {}
    for x in data:
        d.setdefault(x, []).append(x)
    return d

# Benchmark
if_time = timeit.timeit(mit_if_not_in, number=1000)
set_time = timeit.timeit(mit_setdefault, number=1000)

print(f"if-not-in:    {if_time:.3f}s")
print(f"setdefault(): {set_time:.3f}s")
print(f"Speedup:      {if_time/set_time:.2f}x")

# setdefault() ist schneller!
```

### 4. Dict Merging: update() vs. unpacking

```python
import timeit

d1 = {i: i for i in range(100)}
d2 = {i+100: i for i in range(100)}

# Mit update()
def mit_update():
    result = d1.copy()
    result.update(d2)
    return result

# Mit Unpacking (Python 3.5+)
def mit_unpacking():
    return {**d1, **d2}

# Mit | Operator (Python 3.9+)
def mit_operator():
    return d1 | d2

# Benchmark
update_time = timeit.timeit(mit_update, number=10000)
unpack_time = timeit.timeit(mit_unpacking, number=10000)
operator_time = timeit.timeit(mit_operator, number=10000)

print(f"update():  {update_time:.3f}s")
print(f"unpacking: {unpack_time:.3f}s")
print(f"| operator: {operator_time:.3f}s")

# | operator ist am schnellsten in Python 3.9+!
```

### 5. Pre-sizing Dictionaries

```python
import timeit

data = range(10000)

# Ohne Pre-sizing (viele Resizes)
def ohne_presize():
    d = {}
    for i in data:
        d[i] = i
    return d

# Mit Pre-sizing (weniger Resizes)
def mit_presize():
    d = dict.fromkeys(data)
    for i in data:
        d[i] = i
    return d

# Benchmark
ohne_time = timeit.timeit(ohne_presize, number=1000)
mit_time = timeit.timeit(mit_presize, number=1000)

print(f"Ohne Pre-sizing: {ohne_time:.3f}s")
print(f"Mit Pre-sizing:  {mit_time:.3f}s")
print(f"Speedup:         {ohne_time/mit_time:.2f}x")

# Pre-sizing kann helfen, besonders bei sehr großen Dicts
```

---

## Vergleich: Dict vs. Alternativen

### Dict vs. List (Lookup)

```python
import timeit

size = 10000
target = size - 1  # Worst case - letztes Element

# Dict
d = {i: i for i in range(size)}

def dict_lookup():
    return target in d

# Liste
l = list(range(size))

def list_lookup():
    return target in l

# Benchmark
dict_time = timeit.timeit(dict_lookup, number=100000)
list_time = timeit.timeit(list_lookup, number=100000)

print(f"Dict lookup:  {dict_time:.3f}s (O(1))")
print(f"List lookup:  {list_time:.3f}s (O(n))")
print(f"Dict ist {list_time/dict_time:.0f}x schneller!")

# Bei size=10000: Dict ist ~1000x schneller!
```

### Dict vs. Set (Membership)

```python
import timeit

size = 10000

d = {i: i for i in range(size)}
s = set(range(size))

# Dict membership
def dict_test():
    return 5000 in d

# Set membership
def set_test():
    return 5000 in s

dict_time = timeit.timeit(dict_test, number=100000)
set_time = timeit.timeit(set_test, number=100000)

print(f"Dict: {dict_time:.3f}s")
print(f"Set:  {set_time:.3f}s")

# Beide O(1), aber Set minimal schneller
# Verwende Set wenn nur Membership, Dict wenn auch Values
```

### Dict vs. namedtuple vs. dataclass

```python
import timeit
from collections import namedtuple
from dataclasses import dataclass

# Dict
def dict_create():
    return {"name": "Max", "age": 30, "city": "Berlin"}

# namedtuple
Person = namedtuple("Person", ["name", "age", "city"])
def namedtuple_create():
    return Person("Max", 30, "Berlin")

# dataclass
@dataclass
class PersonDC:
    name: str
    age: int
    city: str

def dataclass_create():
    return PersonDC("Max", 30, "Berlin")

# Benchmark Creation
print("Erstellung:")
print(f"  Dict:       {timeit.timeit(dict_create, number=100000):.3f}s")
print(f"  namedtuple: {timeit.timeit(namedtuple_create, number=100000):.3f}s")
print(f"  dataclass:  {timeit.timeit(dataclass_create, number=100000):.3f}s")

# Dict ist am schnellsten zu erstellen
# namedtuple/dataclass haben Type-Safety und Attribute-Access
```

---

## Best Practices für Performance

### ✅ DO: Verwende die richtige Datenstruktur

```python
# Für Lookups: Dict
user_cache = {
    123: {"name": "Max", "email": "max@example.com"},
    456: {"name": "Anna", "email": "anna@example.com"}
}
user = user_cache[123]  # O(1)

# Für Membership ohne Values: Set
active_users = {123, 456, 789}
is_active = 123 in active_users  # O(1)

# Für geordnete Sequenzen: List
log_entries = ["entry1", "entry2", "entry3"]
```

### ✅ DO: Batch-Operations verwenden

```python
# ❌ SCHLECHT - Einzelne Updates
d = {}
for key, value in items:
    d[key] = value

# ✅ GUT - Batch Update
d = {}
d.update(items)

# ODER: Comprehension
d = {key: value for key, value in items}
```

### ✅ DO: Views statt Listen

```python
d = {i: i for i in range(10000)}

# ❌ SCHLECHT - Liste erstellen
keys = list(d.keys())
for key in keys:
    process(key)

# ✅ GUT - View verwenden
for key in d.keys():  # Oder einfach: for key in d
    process(key)

# Views: Kein Memory-Overhead, immer aktuell
```

### ✅ DO: Membership-Tests optimieren

```python
# ❌ SCHLECHT - In Values suchen (O(n))
if "Max" in user_dict.values():
    # Langsam bei vielen Usern
    pass

# ✅ GUT - Invertierten Index erstellen
name_to_id = {user["name"]: uid for uid, user in user_dict.items()}
if "Max" in name_to_id:  # O(1)
    user_id = name_to_id["Max"]
```

### ❌ DON'T: Dictionaries in Loops wachsen lassen

```python
# ❌ SCHLECHT - Viele Resizes
d = {}
for i in range(100000):
    d[i] = i  # Mehrere Resizes während Loop

# ✅ BESSER - Pre-allocate wenn Größe bekannt
d = dict.fromkeys(range(100000))
for i in range(100000):
    d[i] = i

# ODER: Comprehension (automatisch optimiert)
d = {i: i for i in range(100000)}
```

### ❌ DON'T: Doppelte Lookups

```python
# ❌ SCHLECHT - Zwei Lookups
if key in d:
    value = d[key]
else:
    value = default

# ✅ GUT - Ein Lookup
value = d.get(key, default)

# ODER: try-except bei häufigen Hits
try:
    value = d[key]
except KeyError:
    value = default
```

---

## Profiling von Dictionary-Code

### Mit timeit

```python
import timeit

code_to_test = """
d = {}
for i in range(1000):
    d[i] = i * 2
"""

time = timeit.timeit(code_to_test, number=1000)
print(f"Zeit: {time:.3f}s")
```

### Mit cProfile

```python
import cProfile
import pstats

def dict_operations():
    d = {}
    for i in range(10000):
        d[i] = i * 2
    
    # Verschiedene Operationen
    for i in range(5000):
        _ = d[i]
    
    for i in range(2500):
        del d[i]
    
    return d

# Profile
profiler = cProfile.Profile()
profiler.enable()
result = dict_operations()
profiler.disable()

# Statistiken
stats = pstats.Stats(profiler)
stats.sort_stats('cumulative')
stats.print_stats(10)  # Top 10 Funktionen
```

### Mit memory_profiler

```python
# Installiere: pip install memory-profiler

from memory_profiler import profile

@profile
def dict_memory_test():
    # Kleine Dicts
    small_dicts = [{i: i for i in range(100)} for _ in range(100)]
    
    # Ein großes Dict
    large_dict = {i: i for i in range(10000)}
    
    # Nested Dicts
    nested = {
        i: {j: j for j in range(10)}
        for i in range(100)
    }
    
    return small_dicts, large_dict, nested

# Ausführen mit: python -m memory_profiler script.py
# Zeigt Line-by-line Memory-Verwendung
```

---

## Anti-Patterns vermeiden

### Anti-Pattern 1: String-Keys statt Constants

```python
# ❌ SCHLECHT - Typos möglich, schwer zu refactoren
user = {
    "name": "Max",
    "age": 30,
    "emial": "max@example.com"  # Typo!
}
email = user.get("email")  # None - Fehler nicht erkannt!

# ✅ BESSER - Constants verwenden
NAME = "name"
AGE = "age"
EMAIL = "email"

user = {
    NAME: "Max",
    AGE: 30,
    EMAIL: "max@example.com"
}
email = user.get(EMAIL)

# NOCH BESSER: dataclass oder TypedDict für Type-Safety
```

### Anti-Pattern 2: Dict als "Objekt-Ersatz"

```python
# ❌ SCHLECHT - Dict für strukturierte Daten
user = {
    "name": "Max",
    "age": 30,
    "address": {
        "street": "Hauptstr. 1",
        "city": "Berlin"
    }
}

# Zugriff ist fehleranfällig
city = user["address"]["city"]  # KeyError wenn fehlt

# ✅ BESSER - dataclass verwenden
from dataclasses import dataclass

@dataclass
class Address:
    street: str
    city: str

@dataclass
class User:
    name: str
    age: int
    address: Address

user = User("Max", 30, Address("Hauptstr. 1", "Berlin"))
city = user.address.city  # Type-safe, Auto-complete
```

### Anti-Pattern 3: Unnötige Kopien

```python
# ❌ SCHLECHT - Unnötige Kopien
def process_data(data):
    result = data.copy()  # Unnötig wenn nur Lesen
    for key, value in result.items():
        print(f"{key}: {value}")
    return result

# ✅ BESSER - Direkt verwenden
def process_data(data):
    for key, value in data.items():
        print(f"{key}: {value}")
    # Nur kopieren wenn wirklich modifiziert wird
```

### Anti-Pattern 4: Dict für Counter-Logik

```python
from collections import Counter

# ❌ UMSTÄNDLICH
word_count = {}
for word in words:
    if word in word_count:
        word_count[word] += 1
    else:
        word_count[word] = 1

# ✅ BESSER - get()
word_count = {}
for word in words:
    word_count[word] = word_count.get(word, 0) + 1

# ✅ AM BESTEN - Counter
word_count = Counter(words)
```

---

## Praktische Benchmarks

### Verschiedene Dict-Größen

```python
import timeit
import sys

def benchmark_operations(size):
    setup = f"d = {{i: i for i in range({size})}}"
    
    # Access
    access_time = timeit.timeit(
        f"d[{size//2}]",
        setup=setup,
        number=100000
    )
    
    # Insertion
    insert_time = timeit.timeit(
        f"d[{size+1}] = {size+1}",
        setup=setup,
        number=100000
    )
    
    # Deletion
    delete_time = timeit.timeit(
        f"d.pop({size//2}, None)",
        setup=setup,
        number=100000
    )
    
    # Memory
    d = {i: i for i in range(size)}
    memory = sys.getsizeof(d)
    
    return {
        "size": size,
        "access": access_time,
        "insert": insert_time,
        "delete": delete_time,
        "memory": memory
    }

# Benchmark verschiedene Größen
sizes = [10, 100, 1000, 10000, 100000]

print(f"{'Size':>8} | {'Access':>8} | {'Insert':>8} | {'Delete':>8} | {'Memory':>10}")
print("-" * 60)

for size in sizes:
    result = benchmark_operations(size)
    print(f"{result['size']:8d} | "
          f"{result['access']:8.4f} | "
          f"{result['insert']:8.4f} | "
          f"{result['delete']:8.4f} | "
          f"{result['memory']:10,}")
```

---

## Zusammenfassung: Performance-Checkliste

```python
"""
✅ PERFORMANCE CHECKLISTE

1. DATENSTRUKTUR-WAHL
   □ Dict für Key-Value mit O(1) Lookup
   □ Set für Membership ohne Values
   □ List nur wenn Reihenfolge + Index wichtig

2. ERSTELLUNG
   □ Comprehensions statt Loops
   □ Batch-Operations (update, fromkeys)
   □ Pre-sizing bei bekannter Größe

3. ZUGRIFF
   □ get() statt if-in + []
   □ setdefault() für Initialisierung
   □ try-except bei häufigen Hits

4. ITERATION
   □ Views statt Listen
   □ Direkt über Dict statt .keys()
   □ items() für Key+Value Zugriff

5. MEMORY
   □ Keine unnötigen Kopien
   □ Values() nur wenn nötig
   □ Aufräumen von ungenutzten Entries

6. PROFILING
   □ timeit für Micro-Benchmarks
   □ cProfile für Funktionsprofile
   □ memory_profiler für Memory
"""
```

---

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [Time Complexity](https://wiki.python.org/moin/TimeComplexity) - Komplexität aller Operationen
- [Performance Tips](https://wiki.python.org/moin/PythonSpeed/PerformanceTips) - Offizielle Performance-Tipps
- [timeit module](https://docs.python.org/3/library/timeit.html) - Für Benchmarking
- [sys.getsizeof()](https://docs.python.org/3/library/sys.html#sys.getsizeof) - Memory-Messung

---

## Verwandte Themen

- [[03_Mappings_und_Sets/01_Dictionaries/01_Dict Basics|Dict Basics]] - Grundlagen wiederholen
- [[03_Mappings_und_Sets/01_Dictionaries/02_Dict Methoden|Dict Methoden]] - Methoden-Performance
- [[03_Mappings_und_Sets/02_Sets/05_Set Performance|Set Performance]] - Vergleichbare Datenstruktur
- [[06_Praxis/02_Performance/01_Performance Messung|Performance Messung]] - Allgemeine Performance-Techniken

---

← [[03_Mappings_und_Sets/01_Dictionaries/06_OrderedDict vs Dict|OrderedDict vs Dict]] | [[INDEX|📑 Index]] | [[03_Mappings_und_Sets/01_Dictionaries/08_Übungen Dictionaries|Übungen Dictionaries]] →