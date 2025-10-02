---
tags: [python, datenstrukturen, performance, optimization]
created: 2025-10-02
---

← [[06_Praxis/02_Performance/02_Memory Profiling|Memory Profiling]] | [[INDEX|📑 Index]] | [[06_Praxis/03_Projektübungen/01_Projekt - Datenverarbeitung|Projekt - Datenverarbeitung]] →

# Optimization Strategien

> [!tip] Lernziel
> Du kennst bewährte Optimierungs-Strategien und kannst sie gezielt einsetzen.

## Die Goldenen Regeln

1. **Measure First** - Erst messen, dann optimieren
2. **Pareto Principle** - 80% der Zeit in 20% des Codes
3. **Algorithmus > Micro-Optimizations** - O(n) → O(log n) schlägt alles
4. **Readability Counts** - Nur optimieren wenn nötig

> "Premature optimization is the root of all evil" - Donald Knuth

## Algorithmus-Optimierung

### 1. Bessere Datenstruktur wählen

```python
# ❌ O(n²) - Liste für Membership
def find_duplicates_slow(items):
    """Langsam: O(n²)"""
    seen = []
    duplicates = []
    for item in items:
        if item in seen:  # O(n) Lookup!
            duplicates.append(item)
        else:
            seen.append(item)
    return duplicates

# ✅ O(n) - Set für Membership
def find_duplicates_fast(items):
    """Schnell: O(n)"""
    seen = set()
    duplicates = []
    for item in items:
        if item in seen:  # O(1) Lookup!
            duplicates.append(item)
        else:
            seen.add(item)
    return duplicates

# Benchmark
import timeit

data = list(range(10000)) * 2  # Duplikate

t1 = timeit.timeit(lambda: find_duplicates_slow(data), number=10)
t2 = timeit.timeit(lambda: find_duplicates_fast(data), number=10)

print(f"Slow: {t1:.4f}s")
print(f"Fast: {t2:.4f}s")
print(f"Speedup: {t1/t2:.0f}x")
# Speedup: ~500x!
```

### 2. Vermeiden von unnötigen Operationen

```python
# ❌ Unnötige Berechnungen in Loop
def calculate_total_slow(items, tax_rate=0.19):
    """Berechnet tax_rate in jedem Durchlauf neu"""
    total = 0
    for item in items:
        total += item * (1 + tax_rate)  # Multiplikation jedes Mal
    return total

# ✅ Pre-calculate
def calculate_total_fast(items, tax_rate=0.19):
    """Berechnet Faktor einmal"""
    factor = 1 + tax_rate  # Einmal berechnen
    return sum(item * factor for item in items)

# Oder noch besser: keine Extra-Multiplikation
def calculate_total_faster(items, tax_rate=0.19):
    """Mathematisch optimiert"""
    subtotal = sum(items)
    return subtotal * (1 + tax_rate)
```

### 3. Early Exit

```python
# ❌ Prüft alle Elemente
def has_negative_slow(numbers):
    """Prüft alle, auch wenn erstes negativ"""
    negatives = [n for n in numbers if n < 0]
    return len(negatives) > 0

# ✅ Stoppt bei erstem Fund
def has_negative_fast(numbers):
    """Early exit"""
    return any(n < 0 for n in numbers)

# Oder explizit:
def has_negative_explicit(numbers):
    for n in numbers:
        if n < 0:
            return True  # Sofort beenden!
    return False
```

## Caching & Memoization

### 1. functools.lru_cache

```python
from functools import lru_cache

# ❌ Ohne Cache - jedes Mal neu berechnen
def fibonacci_slow(n):
    if n < 2:
        return n
    return fibonacci_slow(n-1) + fibonacci_slow(n-2)

# ✅ Mit Cache
@lru_cache(maxsize=128)
def fibonacci_fast(n):
    if n < 2:
        return n
    return fibonacci_fast(n-1) + fibonacci_fast(n-2)

# Benchmark
import timeit

print("Ohne Cache:")
t1 = timeit.timeit(lambda: fibonacci_slow(30), number=1)
print(f"Zeit: {t1:.4f}s")

print("\nMit Cache:")
t2 = timeit.timeit(lambda: fibonacci_fast(30), number=1)
print(f"Zeit: {t2:.6f}s")
print(f"Speedup: {t1/t2:.0f}x")

# Cache-Statistiken
print(f"\nCache Info: {fibonacci_fast.cache_info()}")
```

### 2. Manueller Cache

```python
# Eigener Cache für komplexere Szenarien
class DataCache:
    def __init__(self, max_size=100):
        self.cache = {}
        self.max_size = max_size
        self.hits = 0
        self.misses = 0
    
    def get(self, key, compute_func):
        """Gibt Wert aus Cache oder berechnet neu"""
        if key in self.cache:
            self.hits += 1
            return self.cache[key]
        
        self.misses += 1
        value = compute_func(key)
        
        # Einfaches LRU: ältesten entfernen wenn voll
        if len(self.cache) >= self.max_size:
            self.cache.pop(next(iter(self.cache)))
        
        self.cache[key] = value
        return value
    
    def stats(self):
        total = self.hits + self.misses
        hit_rate = self.hits / total if total > 0 else 0
        return f"Hits: {self.hits}, Misses: {self.misses}, Rate: {hit_rate:.2%}"

# Verwendung
def expensive_computation(n):
    import time
    time.sleep(0.1)  # Simuliert langsame Berechnung
    return n ** 2

cache = DataCache()

# Erste Aufrufe - Cache Miss
result1 = cache.get(5, expensive_computation)  # Slow
result2 = cache.get(10, expensive_computation) # Slow

# Wiederholte Aufrufe - Cache Hit
result3 = cache.get(5, expensive_computation)  # Fast!
result4 = cache.get(10, expensive_computation) # Fast!

print(cache.stats())
```

### 3. Lazy Evaluation

```python
class LazyProperty:
    """Lazy-loaded Property mit Caching"""
    def __init__(self, func):
        self.func = func
        self.attr_name = f"_lazy_{func.__name__}"
    
    def __get__(self, obj, objtype=None):
        if obj is None:
            return self
        
        # Bereits berechnet?
        if not hasattr(obj, self.attr_name):
            value = self.func(obj)
            setattr(obj, self.attr_name, value)
        
        return getattr(obj, self.attr_name)

class DataProcessor:
    def __init__(self, data):
        self.data = data
    
    @LazyProperty
    def expensive_calculation(self):
        """Wird nur bei erstem Zugriff berechnet"""
        print("Computing...")
        return sum(x**2 for x in self.data)
    
    @LazyProperty
    def statistics(self):
        """Lazy Statistics"""
        print("Computing statistics...")
        return {
            "mean": sum(self.data) / len(self.data),
            "max": max(self.data),
            "min": min(self.data)
        }

# Verwendung
processor = DataProcessor(range(1000000))

# Nichts passiert bis zum Zugriff
print("Created processor")

# Erst hier wird berechnet
result = processor.expensive_calculation  # Computing...
# Zweiter Zugriff: aus Cache
result2 = processor.expensive_calculation  # Kein Output
```

## Built-in Optimierungen nutzen

### 1. Built-in Functions

```python
# ❌ Manuelle Implementierung
def sum_slow(numbers):
    total = 0
    for n in numbers:
        total += n
    return total

# ✅ Built-in (in C implementiert)
def sum_fast(numbers):
    return sum(numbers)

# ❌ Manuelle Min/Max-Suche
def find_min_max_slow(numbers):
    minimum = numbers[0]
    maximum = numbers[0]
    for n in numbers[1:]:
        if n < minimum:
            minimum = n
        if n > maximum:
            maximum = n
    return minimum, maximum

# ✅ Built-ins
def find_min_max_fast(numbers):
    return min(numbers), max(numbers)
```

### 2. List Comprehensions > Loops

```python
import timeit

numbers = range(10000)

# Loop
def with_loop():
    result = []
    for n in numbers:
        result.append(n * 2)
    return result

# List Comprehension
def with_comp():
    return [n * 2 for n in numbers]

# Map
def with_map():
    return list(map(lambda n: n * 2, numbers))

t1 = timeit.timeit(with_loop, number=1000)
t2 = timeit.timeit(with_comp, number=1000)
t3 = timeit.timeit(with_map, number=1000)

print(f"Loop:        {t1:.4f}s")
print(f"Comprehension: {t2:.4f}s (speedup: {t1/t2:.2f}x)")
print(f"Map:         {t3:.4f}s (speedup: {t1/t3:.2f}x)")
```

### 3. String Operations

```python
import timeit

words = ["word"] * 1000

# ❌ String Concatenation
def concat_slow():
    result = ""
    for word in words:
        result += word
    return result

# ✅ join()
def concat_fast():
    return "".join(words)

t1 = timeit.timeit(concat_slow, number=100)
t2 = timeit.timeit(concat_fast, number=100)

print(f"Concatenation: {t1:.4f}s")
print(f"join():        {t2:.4f}s")
print(f"Speedup:       {t1/t2:.0f}x")
```

## Generator-Optimierungen

### 1. Generator statt Liste

```python
import sys

# ❌ Liste - alles im Memory
def read_large_file_list(filename):
    with open(filename) as f:
        return [line.strip() for line in f]

# ✅ Generator - lazy
def read_large_file_gen(filename):
    with open(filename) as f:
        for line in f:
            yield line.strip()

# Memory-Vergleich
import tracemalloc

# Simuliere große Datei
lines = ["line " * 100 + "\n" for _ in range(100000)]

tracemalloc.start()
list_result = [line.strip() for line in lines]
mem1, _ = tracemalloc.get_traced_memory()

tracemalloc.clear_traces()
gen_result = (line.strip() for line in lines)
mem2, _ = tracemalloc.get_traced_memory()

tracemalloc.stop()

print(f"List:      {mem1 / 1024 / 1024:.2f} MB")
print(f"Generator: {mem2 / 1024 / 1024:.6f} MB")
```

### 2. Generator-Pipelines

```python
# Generator-Pipeline für Datentransformation
def read_lines(filename):
    """Generator: liest Zeilen"""
    with open(filename) as f:
        for line in f:
            yield line

def filter_comments(lines):
    """Generator: filtert Kommentare"""
    for line in lines:
        if not line.strip().startswith("#"):
            yield line

def parse_data(lines):
    """Generator: parst Daten"""
    for line in lines:
        yield line.strip().split(",")

def process_numbers(rows):
    """Generator: verarbeitet Zahlen"""
    for row in rows:
        yield [int(x) for x in row if x.isdigit()]

# Pipeline - alles lazy!
pipeline = process_numbers(
    parse_data(
        filter_comments(
            read_lines("data.csv")
        )
    )
)

# Nur bei Iteration wird verarbeitet
for row in pipeline:
    process(row)  # Eine Zeile nach der anderen
```

## Batch-Processing

```python
def process_items_naive(items):
    """Verarbeitet einzeln - ineffizient bei I/O"""
    results = []
    for item in items:
        result = expensive_operation(item)  # Z.B. DB-Query
        results.append(result)
    return results

def process_items_batched(items, batch_size=100):
    """Verarbeitet in Batches - effizienter"""
    results = []
    for i in range(0, len(items), batch_size):
        batch = items[i:i + batch_size]
        batch_results = expensive_batch_operation(batch)  # Bulk-Query
        results.extend(batch_results)
    return results

# Beispiel: Datenbank-Queries
def fetch_users_naive(user_ids):
    """Eine Query pro User - langsam"""
    users = []
    for user_id in user_ids:
        user = db.query(f"SELECT * FROM users WHERE id = {user_id}")
        users.append(user)
    return users

def fetch_users_batch(user_ids, batch_size=100):
    """Batch-Queries - viel schneller"""
    users = []
    for i in range(0, len(user_ids), batch_size):
        batch = user_ids[i:i + batch_size]
        batch_users = db.query(
            f"SELECT * FROM users WHERE id IN ({','.join(map(str, batch))})"
        )
        users.extend(batch_users)
    return users

# Speedup: 10-100x je nach Netzwerk-Latenz!
```

## Local Variable Optimization

```python
import timeit

# ❌ Global Lookup in Loop
import math

def calculate_slow():
    result = []
    for i in range(10000):
        result.append(math.sqrt(i))  # Global lookup jedes Mal
    return result

# ✅ Local Reference
def calculate_fast():
    sqrt = math.sqrt  # Local variable
    result = []
    for i in range(10000):
        result.append(sqrt(i))  # Local lookup
    return result

t1 = timeit.timeit(calculate_slow, number=100)
t2 = timeit.timeit(calculate_fast, number=100)

print(f"Global: {t1:.4f}s")
print(f"Local:  {t2:.4f}s")
print(f"Speedup: {t1/t2:.2f}x")
```

## Numpy für numerische Operationen

```python
import timeit
import numpy as np

# ❌ Pure Python
def sum_squares_python(n):
    return sum(i**2 for i in range(n))

# ✅ NumPy (vectorized)
def sum_squares_numpy(n):
    arr = np.arange(n)
    return np.sum(arr ** 2)

n = 1000000

t1 = timeit.timeit(lambda: sum_squares_python(n), number=10)
t2 = timeit.timeit(lambda: sum_squares_numpy(n), number=10)

print(f"Python: {t1:.4f}s")
print(f"NumPy:  {t2:.4f}s")
print(f"Speedup: {t1/t2:.0f}x")
# Speedup: ~50-100x!
```

## Profiling-gesteuertes Optimieren

```python
import cProfile
import pstats
from io import StringIO

def optimize_workflow():
    """Systematisch optimieren mit Profiling"""
    
    # 1. Original Code profilen
    profiler = cProfile.Profile()
    profiler.enable()
    
    result = original_function()
    
    profiler.disable()
    
    # 2. Bottlenecks identifizieren
    s = StringIO()
    stats = pstats.Stats(profiler, stream=s)
    stats.sort_stats('cumulative')
    stats.print_stats(10)
    
    print("=== Original ===")
    print(s.getvalue())
    
    # 3. Bottleneck optimieren
    # (basierend auf Profiling-Output)
    
    # 4. Optimierte Version profilen
    profiler2 = cProfile.Profile()
    profiler2.enable()
    
    result2 = optimized_function()
    
    profiler2.disable()
    
    s2 = StringIO()
    stats2 = pstats.Stats(profiler2, stream=s2)
    stats2.sort_stats('cumulative')
    stats2.print_stats(10)
    
    print("\n=== Optimized ===")
    print(s2.getvalue())
```

## Optimization Checklist

### Algorithmus-Ebene
- ✓ Richtige Datenstruktur? (set statt list für Membership)
- ✓ Besserer Algorithmus möglich? (O(n²) → O(n log n))
- ✓ Unnötige Operationen vermieden? (Loop-invariant code motion)
- ✓ Early exit möglich?

### Code-Ebene
- ✓ Built-ins verwendet? (sum, min, max, any, all)
- ✓ List comprehensions statt Loops?
- ✓ Generator statt Liste wo möglich?
- ✓ String join() statt Concatenation?

### Memory-Ebene
- ✓ Unnötige Kopien vermieden?
- ✓ Generator für große Datenmengen?
- ✓ __slots__ für viele Objekte?
- ✓ Weak references für Caches?

### I/O-Ebene
- ✓ Batch-Processing für DB/Network?
- ✓ Caching implementiert?
- ✓ Lazy loading wo möglich?

## Zusammenfassung: Optimization Priority

```
1. Algorithmus (O-Notation)     ████████████████████  Größter Impact
2. Datenstruktur                ███████████████       Großer Impact
3. Caching/Memoization         ████████████          Mittlerer Impact
4. Built-ins nutzen            ███████               Kleiner Impact
5. Micro-Optimierungen         ██                    Minimaler Impact
```

**Workflow:**
1. **Profilen** - Wo ist der Bottleneck?
2. **Algorithmus** - Bessere Big-O möglich?
3. **Datenstruktur** - Richtige Wahl?
4. **Caching** - Wiederholte Berechnungen?
5. **Built-ins** - Python-Optimierungen nutzen?
6. **Messen** - Hat es geholfen?

**Golden Rule:**
> Optimiere nur was messbar langsam ist und häufig ausgeführt wird.

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [functools](https://docs.python.org/3/library/functools.html) - lru_cache und mehr
- [timeit](https://docs.python.org/3/library/timeit.html) - Performance-Messung
- [cProfile](https://docs.python.org/3/library/profile.html) - Profiling
- [Python Performance Tips](https://wiki.python.org/moin/PythonSpeed/PerformanceTips)

## Verwandte Themen

- [[06_Praxis/02_Performance/01_Performance Messung|Performance Messung]] - Profiling
- [[06_Praxis/02_Performance/02_Memory Profiling|Memory Profiling]] - Memory-Optimierung
- [[06_Praxis/01_Best_Practices/03_Datenstruktur Auswahl|Datenstruktur Auswahl]] - Richtige Struktur
- [[02_Sequenzen/04_Comprehensions/04_Performance Vergleich|Performance Vergleich]] - Comprehensions

---
← [[06_Praxis/02_Performance/02_Memory Profiling|Memory Profiling]] | [[INDEX|📑 Index]] | [[06_Praxis/03_Projektübungen/01_Projekt - Datenverarbeitung|Projekt - Datenverarbeitung]] →