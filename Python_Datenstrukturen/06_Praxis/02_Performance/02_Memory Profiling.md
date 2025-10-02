---
tags: [python, datenstrukturen, performance, memory]
created: 2025-10-02
---

← [[06_Praxis/02_Performance/01_Performance Messung|Performance Messung]] | [[INDEX|📑 Index]] | [[06_Praxis/02_Performance/03_Optimization Strategien|Optimization Strategien]] →

# Memory Profiling

> [!tip] Lernziel
> Du kannst Speicherverbrauch messen, Memory Leaks identifizieren und Speicher-Optimierungen vornehmen.

## Warum Memory Profiling?

**Häufige Memory-Probleme:**
- Memory Leaks (Speicher wird nicht freigegeben)
- Unnötige Kopien großer Datenstrukturen
- Ineffiziente Datenstrukturen
- Zu viele Objekte im Speicher

## sys.getsizeof() - Objekt-Größe

### Basis-Verwendung

```python
import sys

# Primitive Typen
print(f"int:   {sys.getsizeof(42)} bytes")           # 28 bytes
print(f"float: {sys.getsizeof(3.14)} bytes")         # 24 bytes
print(f"bool:  {sys.getsizeof(True)} bytes")         # 28 bytes
print(f"None:  {sys.getsizeof(None)} bytes")         # 16 bytes

# Strings
print(f"Empty string: {sys.getsizeof('')} bytes")    # 49 bytes
print(f"'Hello':      {sys.getsizeof('Hello')} bytes") # 54 bytes
print(f"'x' * 100:    {sys.getsizeof('x' * 100)} bytes") # 149 bytes

# Collections
print(f"Empty list:   {sys.getsizeof([])} bytes")    # 56 bytes
print(f"[1, 2, 3]:    {sys.getsizeof([1, 2, 3])} bytes") # 88 bytes
print(f"Empty dict:   {sys.getsizeof({})} bytes")    # 64 bytes
print(f"Empty set:    {sys.getsizeof(set())} bytes") # 216 bytes
```

### Tiefe Größe berechnen

```python
import sys
from typing import Any

def get_deep_size(obj: Any, seen: set = None) -> int:
    """Berechnet rekursive Größe inkl. aller referenzierten Objekte"""
    if seen is None:
        seen = set()
    
    obj_id = id(obj)
    if obj_id in seen:
        return 0
    
    seen.add(obj_id)
    size = sys.getsizeof(obj)
    
    # Für Container: rekursiv alle Elemente
    if isinstance(obj, dict):
        size += sum(get_deep_size(k, seen) + get_deep_size(v, seen) 
                    for k, v in obj.items())
    elif isinstance(obj, (list, tuple, set, frozenset)):
        size += sum(get_deep_size(item, seen) for item in obj)
    
    return size

# Beispiel
nested = {
    "list": [1, 2, 3, 4, 5],
    "dict": {"a": 1, "b": 2},
    "string": "Hello World"
}

print(f"Shallow size: {sys.getsizeof(nested)} bytes")
print(f"Deep size:    {get_deep_size(nested)} bytes")
```

### List vs. Generator - Memory

```python
import sys

# List - alle Elemente im Speicher
list_comp = [i**2 for i in range(100000)]
print(f"List:      {sys.getsizeof(list_comp):,} bytes")

# Generator - nur State im Speicher
gen_expr = (i**2 for i in range(100000))
print(f"Generator: {sys.getsizeof(gen_expr):,} bytes")

# Unterschied: ~800KB vs. ~200 bytes!
```

## tracemalloc - Speicher-Tracking

### Basis-Verwendung

```python
import tracemalloc

# Tracking starten
tracemalloc.start()

# Code ausführen
data = [i**2 for i in range(100000)]

# Aktueller Memory-Verbrauch
current, peak = tracemalloc.get_traced_memory()
print(f"Current: {current / 1024 / 1024:.2f} MB")
print(f"Peak:    {peak / 1024 / 1024:.2f} MB")

# Tracking stoppen
tracemalloc.stop()
```

### Top Memory Allocations

```python
import tracemalloc

tracemalloc.start()

# Code mit verschiedenen Allocations
list1 = [i for i in range(100000)]
dict1 = {i: i**2 for i in range(50000)}
string = "x" * 1000000

# Snapshot
snapshot = tracemalloc.take_snapshot()
top_stats = snapshot.statistics('lineno')

print("Top 10 Memory Allocations:")
for stat in top_stats[:10]:
    print(f"{stat.size / 1024:.1f} KB - {stat.traceback}")

tracemalloc.stop()
```

### Memory Leak Detection

```python
import tracemalloc

def find_memory_leaks():
    """Findet Memory Leaks durch Snapshot-Vergleich"""
    tracemalloc.start()
    
    # Snapshot 1
    snapshot1 = tracemalloc.take_snapshot()
    
    # Code der potentiell leaked
    leaky_function()
    
    # Snapshot 2
    snapshot2 = tracemalloc.take_snapshot()
    
    # Vergleich
    top_stats = snapshot2.compare_to(snapshot1, 'lineno')
    
    print("Top 10 Memory Increases:")
    for stat in top_stats[:10]:
        print(f"+{stat.size_diff / 1024:.1f} KB - {stat.traceback}")
    
    tracemalloc.stop()

# Beispiel: Leak durch globale Liste
leaked_data = []

def leaky_function():
    """Leaked Memory durch globale Referenz"""
    for i in range(10000):
        leaked_data.append([i] * 100)  # Niemals freigegeben!

find_memory_leaks()
```

### Context Manager für Tracking

```python
import tracemalloc
from contextlib import contextmanager

@contextmanager
def memory_tracker(description="Operation"):
    """Context Manager für Memory-Tracking"""
    tracemalloc.start()
    
    # Vor Operation
    snapshot_before = tracemalloc.take_snapshot()
    
    yield
    
    # Nach Operation
    snapshot_after = tracemalloc.take_snapshot()
    
    # Vergleich
    top_stats = snapshot_after.compare_to(snapshot_before, 'lineno')
    
    current, peak = tracemalloc.get_traced_memory()
    tracemalloc.stop()
    
    print(f"\n=== {description} ===")
    print(f"Current: {current / 1024:.1f} KB")
    print(f"Peak:    {peak / 1024:.1f} KB")
    
    if top_stats:
        print("\nTop memory allocations:")
        for stat in top_stats[:3]:
            print(f"  +{stat.size_diff / 1024:.1f} KB")

# Verwendung
with memory_tracker("List creation"):
    data = [i**2 for i in range(100000)]

with memory_tracker("Dict creation"):
    data = {i: i**2 for i in range(100000)}
```

## Memory Profiling von Funktionen

### Decorator für Memory-Tracking

```python
import tracemalloc
from functools import wraps

def memory_profile(func):
    """Decorator der Memory-Verbrauch misst"""
    @wraps(func)
    def wrapper(*args, **kwargs):
        tracemalloc.start()
        
        result = func(*args, **kwargs)
        
        current, peak = tracemalloc.get_traced_memory()
        tracemalloc.stop()
        
        print(f"{func.__name__}:")
        print(f"  Current: {current / 1024 / 1024:.2f} MB")
        print(f"  Peak:    {peak / 1024 / 1024:.2f} MB")
        
        return result
    return wrapper

# Verwendung
@memory_profile
def create_large_list():
    return [i**2 for i in range(1000000)]

@memory_profile
def create_large_dict():
    return {i: i**2 for i in range(1000000)}

result1 = create_large_list()
result2 = create_large_dict()
```

## Memory-Optimierungen

### 1. __slots__ für Klassen

```python
import sys

# Ohne __slots__ - dict für Attribute
class PersonNoSlots:
    def __init__(self, name, age):
        self.name = name
        self.age = age

# Mit __slots__ - fixe Attribute
class PersonWithSlots:
    __slots__ = ['name', 'age']
    
    def __init__(self, name, age):
        self.name = name
        self.age = age

# Memory-Vergleich
p1 = PersonNoSlots("Max", 30)
p2 = PersonWithSlots("Max", 30)

print(f"Without __slots__: {sys.getsizeof(p1.__dict__)} bytes")
print(f"With __slots__:    {sys.getsizeof(p2)} bytes")

# Bei 1 Million Objekten:
# Ohne: ~280 MB
# Mit:  ~48 MB
# Ersparnis: ~80%!

# Massenerstellung
import tracemalloc

tracemalloc.start()

# Ohne __slots__
people1 = [PersonNoSlots(f"Person{i}", i) for i in range(100000)]
mem1, _ = tracemalloc.get_traced_memory()

tracemalloc.clear_traces()

# Mit __slots__
people2 = [PersonWithSlots(f"Person{i}", i) for i in range(100000)]
mem2, _ = tracemalloc.get_traced_memory()

tracemalloc.stop()

print(f"\n100k Objekte:")
print(f"Without __slots__: {mem1 / 1024 / 1024:.2f} MB")
print(f"With __slots__:    {mem2 / 1024 / 1024:.2f} MB")
print(f"Savings:           {(1 - mem2/mem1) * 100:.1f}%")
```

### 2. Generator statt Liste

```python
import tracemalloc

def list_approach():
    """Liste - alle im Speicher"""
    data = [i**2 for i in range(1000000)]
    return sum(data)

def generator_approach():
    """Generator - einer nach dem anderen"""
    return sum(i**2 for i in range(1000000))

# Memory-Vergleich
tracemalloc.start()
result1 = list_approach()
mem1, _ = tracemalloc.get_traced_memory()
tracemalloc.clear_traces()

result2 = generator_approach()
mem2, _ = tracemalloc.get_traced_memory()
tracemalloc.stop()

print(f"List:      {mem1 / 1024 / 1024:.2f} MB")
print(f"Generator: {mem2 / 1024 / 1024:.2f} MB")
print(f"Savings:   {(1 - mem2/mem1) * 100:.1f}%")
```

### 3. Array statt Liste für numerische Daten

```python
import sys
import array

# Liste von Integers
list_ints = [i for i in range(100000)]

# Array von Integers
array_ints = array.array('i', range(100000))

print(f"List size:  {sys.getsizeof(list_ints) / 1024:.1f} KB")
print(f"Array size: {sys.getsizeof(array_ints) / 1024:.1f} KB")
print(f"Savings:    {(1 - sys.getsizeof(array_ints)/sys.getsizeof(list_ints)) * 100:.1f}%")

# Array ist ~50% kleiner!
```

### 4. Weakref für Cache

```python
import weakref
import sys

class LargeObject:
    """Großes Objekt"""
    def __init__(self, data):
        self.data = [0] * 100000  # ~800KB

# Normaler Cache - hält Referenzen
normal_cache = {}

def get_object_normal(key):
    if key not in normal_cache:
        normal_cache[key] = LargeObject(key)
    return normal_cache[key]

# WeakValueDictionary - schwache Referenzen
weak_cache = weakref.WeakValueDictionary()

def get_object_weak(key):
    obj = weak_cache.get(key)
    if obj is None:
        obj = LargeObject(key)
        weak_cache[key] = obj
    return obj

# Test
obj1 = get_object_normal("key1")
obj2 = get_object_weak("key2")

print(f"Normal cache entries: {len(normal_cache)}")
print(f"Weak cache entries:   {len(weak_cache)}")

# obj2 löschen - wird aus weak_cache entfernt
del obj2
import gc
gc.collect()

print(f"After del obj2:")
print(f"Normal cache entries: {len(normal_cache)}")  # 1
print(f"Weak cache entries:   {len(weak_cache)}")    # 0 (automatisch entfernt!)
```

## Praktische Beispiele

### Memory Leak finden

```python
import tracemalloc
import gc

class DataProcessor:
    """Klasse mit potentiellem Memory Leak"""
    
    # Globale Registry (LEAK!)
    _registry = []
    
    def __init__(self, data):
        self.data = data
        self._registry.append(self)  # Leak: hält Referenz!
    
    def process(self):
        return sum(self.data)

def test_memory_leak():
    """Testet auf Memory Leaks"""
    tracemalloc.start()
    
    # Snapshot vor Loop
    snapshot1 = tracemalloc.take_snapshot()
    
    # Erstelle viele Objekte
    for i in range(100):
        processor = DataProcessor([j for j in range(10000)])
        result = processor.process()
        # processor geht out of scope - sollte gelöscht werden
    
    # Garbage Collection
    gc.collect()
    
    # Snapshot nach Loop
    snapshot2 = tracemalloc.take_snapshot()
    
    # Vergleich
    top_stats = snapshot2.compare_to(snapshot1, 'lineno')
    
    print("Memory Increase:")
    for stat in top_stats[:5]:
        print(f"+{stat.size_diff / 1024:.1f} KB")
        print(f"  {stat.traceback}")
    
    tracemalloc.stop()
    
    # Prüfe Registry
    print(f"\nObjects in registry: {len(DataProcessor._registry)}")
    # Sollte 0 sein, ist aber 100 - LEAK!

test_memory_leak()

# Lösung: Schwache Referenzen verwenden
class DataProcessorFixed:
    _registry = weakref.WeakSet()  # Schwache Referenzen
    
    def __init__(self, data):
        self.data = data
        self._registry.add(self)
```

### Memory-effiziente Dateiverarbeitung

```python
import tracemalloc

# ❌ Ineffizient - gesamte Datei im Speicher
def process_file_bad(filename):
    with open(filename) as f:
        lines = f.readlines()  # Alle Zeilen laden!
    
    result = []
    for line in lines:
        if "keyword" in line:
            result.append(line.strip())
    return result

# ✅ Effizient - Zeile für Zeile
def process_file_good(filename):
    result = []
    with open(filename) as f:
        for line in f:  # Generator - eine Zeile nach der anderen
            if "keyword" in line:
                result.append(line.strip())
    return result

# ✅ Noch besser - Generator zurückgeben
def process_file_best(filename):
    with open(filename) as f:
        for line in f:
            if "keyword" in line:
                yield line.strip()

# Verwendung
for line in process_file_best("large_file.txt"):
    process(line)  # Nur eine Zeile im Speicher!
```

### Batch-Verarbeitung mit Memory-Limit

```python
import tracemalloc
from typing import Iterator, List

def process_in_batches(
    items: List,
    batch_size: int,
    memory_limit_mb: float
) -> Iterator[List]:
    """Verarbeitet Daten in Batches mit Memory-Limit"""
    
    tracemalloc.start()
    
    batch = []
    for item in items:
        batch.append(item)
        
        # Check Memory
        current, _ = tracemalloc.get_traced_memory()
        current_mb = current / 1024 / 1024
        
        # Batch fertig?
        if len(batch) >= batch_size or current_mb >= memory_limit_mb:
            yield batch
            batch = []
            tracemalloc.clear_traces()
    
    # Letzter Batch
    if batch:
        yield batch
    
    tracemalloc.stop()

# Verwendung
large_dataset = list(range(1000000))

for batch in process_in_batches(large_dataset, batch_size=10000, memory_limit_mb=100):
    result = process_batch(batch)
    save_result(result)
    # Batch wird gelöscht, Memory freigegeben
```

## Memory Profiling Best Practices

### 1. Baseline etablieren

```python
import tracemalloc
import gc

def get_memory_baseline():
    """Misst Memory vor Test"""
    gc.collect()  # Cleanup
    tracemalloc.start()
    baseline = tracemalloc.take_snapshot()
    return baseline

def measure_memory_growth(baseline, description="Operation"):
    """Misst Memory-Wachstum seit Baseline"""
    snapshot = tracemalloc.take_snapshot()
    top_stats = snapshot.compare_to(baseline, 'lineno')
    
    print(f"\n=== {description} ===")
    for stat in top_stats[:5]:
        print(f"+{stat.size_diff / 1024:.1f} KB - {stat.traceback}")
```

### 2. Profile verschiedene Szenarien

```python
def benchmark_memory_scenarios():
    """Vergleicht Memory-Verbrauch verschiedener Ansätze"""
    
    scenarios = {
        "List Comprehension": lambda: [i**2 for i in range(100000)],
        "Generator Expression": lambda: (i**2 for i in range(100000)),
        "Map Function": lambda: map(lambda x: x**2, range(100000))
    }
    
    for name, func in scenarios.items():
        tracemalloc.start()
        result = func()
        current, peak = tracemalloc.get_traced_memory()
        tracemalloc.stop()
        
        print(f"{name}:")
        print(f"  Current: {current / 1024:.1f} KB")
        print(f"  Peak:    {peak / 1024:.1f} KB")
```

### 3. Automatisches Monitoring

```python
import tracemalloc
import atexit

class MemoryMonitor:
    """Überwacht Memory während gesamter Laufzeit"""
    
    def __init__(self, threshold_mb=100):
        self.threshold = threshold_mb * 1024 * 1024
        tracemalloc.start()
        atexit.register(self.report)
    
    def check(self):
        """Prüft Memory-Verbrauch"""
        current, peak = tracemalloc.get_traced_memory()
        if current > self.threshold:
            print(f"⚠️ Memory threshold exceeded: {current / 1024 / 1024:.1f} MB")
            snapshot = tracemalloc.take_snapshot()
            top_stats = snapshot.statistics('lineno')
            for stat in top_stats[:3]:
                print(f"  {stat.size / 1024:.1f} KB - {stat.traceback}")
    
    def report(self):
        """Abschlussbericht"""
        snapshot = tracemalloc.take_snapshot()
        top_stats = snapshot.statistics('lineno')
        
        print("\n=== Memory Report ===")
        current, peak = tracemalloc.get_traced_memory()
        print(f"Current: {current / 1024 / 1024:.2f} MB")
        print(f"Peak:    {peak / 1024 / 1024:.2f} MB")
        print("\nTop allocations:")
        for stat in top_stats[:5]:
            print(f"  {stat.size / 1024:.1f} KB")
        
        tracemalloc.stop()

# Verwendung
monitor = MemoryMonitor(threshold_mb=50)

# Programm läuft...
data = [i for i in range(1000000)]
monitor.check()

# Bei Programmende: automatischer Report
```

## Zusammenfassung

**Tool-Auswahl:**

| Tool | Verwendung | Granularität |
|------|-----------|--------------|
| `sys.getsizeof()` | Objekt-Größe | Einzelnes Objekt |
| `tracemalloc` | Memory-Tracking | Zeilen-genau |
| `__slots__` | Optimierung | Klassen |
| `weakref` | Cache-Management | Objekt-Referenzen |
| Generators | Lazy Evaluation | Sequences |

**Workflow:**
1. Baseline etablieren mit `tracemalloc`
2. Hot Spots identifizieren
3. Optimieren (Generators, __slots__, etc.)
4. Vorher/Nachher vergleichen

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [tracemalloc](https://docs.python.org/3/library/tracemalloc.html) - Memory Tracking
- [sys.getsizeof()](https://docs.python.org/3/library/sys.html#sys.getsizeof) - Object Size
- [weakref](https://docs.python.org/3/library/weakref.html) - Weak References
- [gc](https://docs.python.org/3/library/gc.html) - Garbage Collector

## Verwandte Themen

- [[06_Praxis/02_Performance/01_Performance Messung|Performance Messung]] - Zeit-Profiling
- [[06_Praxis/02_Performance/03_Optimization Strategien|Optimization Strategien]] - Optimierung
- [[06_Praxis/01_Best_Practices/03_Datenstruktur Auswahl|Datenstruktur Auswahl]] - Memory-effiziente Strukturen

---
← [[06_Praxis/02_Performance/01_Performance Messung|Performance Messung]] | [[INDEX|📑 Index]] | [[06_Praxis/02_Performance/03_Optimization Strategien|Optimization Strategien]] →