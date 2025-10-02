---
tags: [python, datenstrukturen, comprehensions, performance, benchmarks]
created: 2025-10-01
---

← [[02_Sequenzen/04_Comprehensions/03_Verschachtelte Comprehensions|Verschachtelte Comprehensions]] | [[INDEX|📑 Index]] | [[02_Sequenzen/04_Comprehensions/05_Übungen Comprehensions|Übungen Comprehensions]] →

# Performance Vergleich - Benchmarks und Optimierung

> [!tip] Lernziel
> Nach dieser Seite kennst du die Performance-Charakteristiken von Comprehensions, verstehst Benchmarking-Techniken und weißt, welche Methode wann am effizientesten ist.

## Übersicht der Methoden

Wir vergleichen verschiedene Ansätze zur Listen-Erstellung und -Transformation:

| Methode | Beschreibung | Typische Performance |
|---------|--------------|---------------------|
| **for-Loop** | Traditionell, append() | Baseline (1.0x) |
| **List Comprehension** | `[expr for x in iter]` | 1.1-1.3x schneller |
| **map()** | `list(map(fn, iter))` | 1.0-1.2x schneller |
| **filter()** | `list(filter(fn, iter))` | 1.0-1.2x schneller |
| **Generator Expression** | `(expr for x in iter)` | Memory: 1000x+ effizienter |
| **NumPy** | Vektorisiert | 10-100x schneller* |

*Für numerische Operationen auf großen Arrays

---

## Benchmark 1: Einfache Transformation

### Setup

```python
import time
import sys

def benchmark(func, *args, iterations=1000):
    """Hilfsfunktion für Benchmarking"""
    start = time.perf_counter()
    for _ in range(iterations):
        result = func(*args)
    elapsed = time.perf_counter() - start
    return elapsed / iterations

n = 10000  # Datengröße
```

### Methoden

```python
# 1. for-Loop mit append()
def method_loop(n):
    result = []
    for x in range(n):
        result.append(x * 2)
    return result

# 2. List Comprehension
def method_comp(n):
    return [x * 2 for x in range(n)]

# 3. map() mit lambda
def method_map_lambda(n):
    return list(map(lambda x: x * 2, range(n)))

# 4. map() mit Funktion
def double(x):
    return x * 2

def method_map_func(n):
    return list(map(double, range(n)))

# 5. Generator Expression
def method_gen(n):
    return list(x * 2 for x in range(n))
```

### Ergebnisse

```python
# Benchmarks durchführen
methods = {
    "for-Loop": method_loop,
    "List Comp": method_comp,
    "map(lambda)": method_map_lambda,
    "map(func)": method_map_func,
    "Generator": method_gen
}

print(f"Datengröße: n = {n:,}\n")
results = {}

for name, func in methods.items():
    time_taken = benchmark(func, n)
    results[name] = time_taken
    print(f"{name:15} {time_taken*1000:.4f}ms")

# Relative Performance
baseline = results["for-Loop"]
print(f"\nRelativ zu for-Loop:")
for name, time_taken in results.items():
    speedup = baseline / time_taken
    print(f"{name:15} {speedup:.2f}x")
```

**Typische Ausgabe:**
```
Datengröße: n = 10,000

for-Loop        0.8234ms
List Comp       0.6123ms
map(lambda)     0.7456ms
map(func)       0.5891ms
Generator       0.6234ms

Relativ zu for-Loop:
for-Loop        1.00x
List Comp       1.34x  ← ~34% schneller
map(lambda)     1.10x
map(func)       1.40x  ← Am schnellsten!
Generator       1.32x
```

---

## Benchmark 2: Mit Filter

### Setup

```python
# Nur gerade Zahlen
n = 100000

def method_loop_filter(n):
    result = []
    for x in range(n):
        if x % 2 == 0:
            result.append(x * 2)
    return result

def method_comp_filter(n):
    return [x * 2 for x in range(n) if x % 2 == 0]

def method_map_filter(n):
    return list(map(lambda x: x * 2, filter(lambda x: x % 2 == 0, range(n))))

def method_gen_filter(n):
    return list(x * 2 for x in range(n) if x % 2 == 0)
```

### Ergebnisse

```python
methods = {
    "for-Loop": method_loop_filter,
    "List Comp": method_comp_filter,
    "map+filter": method_map_filter,
    "Generator": method_gen_filter
}

for name, func in methods.items():
    time_taken = benchmark(func, n, iterations=100)
    print(f"{name:15} {time_taken*1000:.4f}ms")
```

**Typische Ausgabe:**
```
for-Loop        12.34ms
List Comp       9.56ms   ← 1.29x schneller
map+filter      11.23ms
Generator       9.78ms
```

**Erkenntnis:** List Comprehension ist bei Filter+Transform kombiniert am effizientesten.

---

## Benchmark 3: Verschachtelte Loops

### 2D-Matrix Erstellung

```python
rows, cols = 100, 100

def method_loop_nested(rows, cols):
    result = []
    for i in range(rows):
        row = []
        for j in range(cols):
            row.append(i * cols + j)
        result.append(row)
    return result

def method_comp_nested(rows, cols):
    return [[i * cols + j for j in range(cols)] for i in range(rows)]

# Benchmark
time_loop = benchmark(method_loop_nested, rows, cols, iterations=1000)
time_comp = benchmark(method_comp_nested, rows, cols, iterations=1000)

print(f"Nested Loop: {time_loop*1000:.4f}ms")
print(f"Nested Comp: {time_comp*1000:.4f}ms")
print(f"Speedup:     {time_loop/time_comp:.2f}x")
```

**Typische Ausgabe:**
```
Nested Loop: 3.45ms
Nested Comp: 2.67ms
Speedup:     1.29x
```

---

## Benchmark 4: Memory-Verbrauch

### List Comprehension vs Generator

```python
import sys
import tracemalloc

def measure_memory(func, *args):
    """Misst Speicherverbrauch"""
    tracemalloc.start()
    result = func(*args)
    current, peak = tracemalloc.get_traced_memory()
    tracemalloc.stop()
    return peak / 1024 / 1024  # MB

n = 1000000

# List Comprehension - alle im Speicher
def create_list(n):
    return [x * 2 for x in range(n)]

# Generator Expression - lazy
def create_gen(n):
    return (x * 2 for x in range(n))

# Generator mit Verarbeitung
def process_gen(n):
    gen = (x * 2 for x in range(n))
    return sum(gen)  # Verarbeitet ohne gesamte Liste

# Messungen
mem_list = measure_memory(create_list, n)
mem_gen = measure_memory(create_gen, n)
mem_process = measure_memory(process_gen, n)

print(f"Liste erstellen:     {mem_list:.2f} MB")
print(f"Generator erstellen: {mem_gen:.4f} MB")
print(f"Generator process:   {mem_process:.4f} MB")
print(f"\nFaktor: {mem_list/mem_gen:.0f}x weniger Speicher mit Generator")
```

**Typische Ausgabe:**
```
Liste erstellen:     38.15 MB
Generator erstellen: 0.0001 MB
Generator process:   0.0001 MB

Faktor: 381,500x weniger Speicher mit Generator
```

---

## Benchmark 5: Große Datenmengen

### Skalierung testen

```python
import time

def benchmark_scaling(method_func, sizes):
    """Testet Skalierung über verschiedene Größen"""
    results = []
    for n in sizes:
        start = time.perf_counter()
        method_func(n)
        elapsed = time.perf_counter() - start
        results.append((n, elapsed))
    return results

sizes = [1000, 10000, 100000, 1000000]

# for-Loop
def loop_method(n):
    result = []
    for x in range(n):
        result.append(x * 2)
    return result

# List Comprehension
def comp_method(n):
    return [x * 2 for x in range(n)]

print("Skalierung:")
print(f"{'n':>10} {'Loop':>12} {'Comp':>12} {'Speedup'}")
print("-" * 50)

for n in sizes:
    time_loop = benchmark(loop_method, n, iterations=10)
    time_comp = benchmark(comp_method, n, iterations=10)
    speedup = time_loop / time_comp
    
    print(f"{n:>10,} {time_loop*1000:>10.2f}ms {time_comp*1000:>10.2f}ms {speedup:>7.2f}x")
```

**Typische Ausgabe:**
```
Skalierung:
         n         Loop         Comp  Speedup
--------------------------------------------------
     1,000        0.08ms       0.06ms    1.33x
    10,000        0.82ms       0.61ms    1.34x
   100,000        8.23ms       6.12ms    1.34x
 1,000,000       82.34ms      61.23ms    1.34x
```

**Erkenntnis:** Speedup bleibt konstant - O(n) Komplexität für beide.

---

## Benchmark 6: Komplexe Transformationen

### Mit teurer Operation

```python
import math

n = 10000

def expensive_operation(x):
    """Simuliert teure Berechnung"""
    return math.sqrt(x) * math.sin(x) * math.cos(x)

# for-Loop
def loop_expensive(n):
    result = []
    for x in range(n):
        result.append(expensive_operation(x))
    return result

# List Comprehension
def comp_expensive(n):
    return [expensive_operation(x) for x in range(n)]

# map()
def map_expensive(n):
    return list(map(expensive_operation, range(n)))

# NumPy (vektorisiert)
import numpy as np

def numpy_expensive(n):
    x = np.arange(n)
    return np.sqrt(x) * np.sin(x) * np.cos(x)

# Benchmark
methods = {
    "for-Loop": loop_expensive,
    "List Comp": comp_expensive,
    "map()": map_expensive,
    "NumPy": numpy_expensive
}

print("Komplexe Operation:")
for name, func in methods.items():
    time_taken = benchmark(func, n, iterations=100)
    print(f"{name:15} {time_taken*1000:.4f}ms")
```

**Typische Ausgabe:**
```
Komplexe Operation:
for-Loop        15.67ms
List Comp       14.89ms
map()           14.23ms
NumPy           0.45ms  ← 35x schneller!
```

**Erkenntnis:** Bei mathematischen Operationen ist NumPy deutlich überlegen.

---

## Benchmark 7: Early Exit Szenarien

### Ersten Treffer finden

```python
n = 10000000  # Sehr große Datenmenge

# List Comprehension - verarbeitet ALLES
def comp_all(n, target=5000):
    result = [x for x in range(n) if x > target and x % 7 == 0]
    return result[0] if result else None

# Generator Expression - stoppt beim ersten
def gen_first(n, target=5000):
    gen = (x for x in range(n) if x > target and x % 7 == 0)
    return next(gen, None)

# Benchmark
time_comp = benchmark(comp_all, n, iterations=10)
time_gen = benchmark(gen_first, n, iterations=10)

print(f"List Comp (alle):  {time_comp*1000:.2f}ms")
print(f"Generator (erst):  {time_gen*1000:.4f}ms")
print(f"Speedup:           {time_comp/time_gen:.0f}x")
```

**Typische Ausgabe:**
```
List Comp (alle):  1250.45ms
Generator (erst):  0.05ms
Speedup:           25,009x  ← Generator ist MASSIV schneller!
```

---

## Performance-Tipps

### 1. Wähle die richtige Methode

```python
# ✅ List Comprehension: Wenn Liste benötigt
squares = [x ** 2 for x in range(1000)]

# ✅ Generator: Bei einmaliger Iteration
total = sum(x ** 2 for x in range(1000000))

# ✅ map(): Bei existierender Funktion
def process(x):
    return complex_calculation(x)
results = list(map(process, data))

# ✅ NumPy: Bei numerischen Arrays
import numpy as np
arr = np.arange(1000000)
result = arr ** 2 + 2 * arr + 1  # Vektorisiert!
```

### 2. Vermeide verschachtelte Comprehensions bei Komplexität

```python
# ❌ Zu komplex für Comprehension
result = [[transform(i, j) for j in range(cols) if condition(j)] 
          for i in range(rows) if other_condition(i)]

# ✅ Nutze Funktion + Comprehension
def process_row(i):
    return [transform(i, j) for j in range(cols) if condition(j)]

result = [process_row(i) for i in range(rows) if other_condition(i)]

# ✅ Oder regulären Loop bei sehr komplexer Logik
```

### 3. Pre-Allocation bringt nichts

```python
# ❌ Unnötig - kein Performance-Gewinn
result = [None] * n
for i in range(n):
    result[i] = i * 2

# ✅ Einfach und schneller
result = [i * 2 for i in range(n)]
```

### 4. Nutze Built-in-Funktionen

```python
# ❌ Langsamer
total = sum([x for x in range(1000000)])

# ✅ Schneller - Generator direkt an sum()
total = sum(x for x in range(1000000))

# ✅ Noch besser - Built-in nutzen
total = sum(range(1000000))
```

---

## Wann welche Methode?

### Entscheidungsbaum

```
Brauche ich...

├─ Eine Liste für mehrfache Verwendung?
│  ├─ Ja, einfache Transformation
│  │  └─ → List Comprehension
│  ├─ Ja, mit existierender Funktion
│  │  └─ → map()
│  └─ Ja, sehr komplex
│     └─ → for-Loop
│
├─ Nur einmalige Iteration?
│  ├─ Große Datenmenge
│  │  └─ → Generator Expression
│  ├─ Early exit möglich
│  │  └─ → Generator Expression
│  └─ Aggregation (sum, max, etc.)
│     └─ → Generator Expression
│
├─ Numerische Berechnungen?
│  ├─ Große Arrays
│  │  └─ → NumPy
│  └─ Kleine Daten
│     └─ → List Comprehension
│
└─ Komplexe Logik mit Nebenwirkungen?
   └─ → for-Loop
```

---

## Best Practices Zusammenfassung

### Performance-Optimierung

```python
# 1. Generator für große Datenmengen
# ✅ Speichereffizient
data = (process(x) for x in huge_dataset)
result = aggregate(data)

# 2. List Comprehension für kleine/mittlere Daten
# ✅ Schnell und lesbar
squares = [x ** 2 for x in range(1000)]

# 3. NumPy für numerische Arrays
# ✅ 10-100x schneller
import numpy as np
arr = np.array([1, 2, 3, 4, 5])
result = arr ** 2 + 2 * arr  # Vektorisiert

# 4. Built-ins wo möglich
# ✅ Optimiert in C
total = sum(range(1000))  # Besser als sum([x for x in range(1000)])
maximum = max(data)       # Besser als max([x for x in data])

# 5. Vermeide doppelte Iteration
# ❌ Langsam
squares = [x ** 2 for x in data]
cubes = [x ** 3 for x in data]

# ✅ Schneller
squares_and_cubes = [(x ** 2, x ** 3) for x in data]
squares, cubes = zip(*squares_and_cubes)
```

---

## Profiling-Tools

### timeit für präzise Messungen

```python
import timeit

# Setup-Code
setup = """
data = list(range(10000))
def process(x):
    return x * 2
"""

# Verschiedene Methoden testen
methods = {
    "Comprehension": "[process(x) for x in data]",
    "map()": "list(map(process, data))",
    "for-Loop": """
result = []
for x in data:
    result.append(process(x))
"""
}

for name, code in methods.items():
    time_taken = timeit.timeit(code, setup, number=1000)
    print(f"{name:15} {time_taken:.4f}s")
```

### cProfile für detaillierte Analyse

```python
import cProfile
import pstats

def complex_operation():
    result = []
    for i in range(10000):
        result.append([j * i for j in range(100)])
    return result

# Profile
profiler = cProfile.Profile()
profiler.enable()

complex_operation()

profiler.disable()
stats = pstats.Stats(profiler)
stats.sort_stats('cumulative')
stats.print_stats(10)  # Top 10
```

---

## Zusammenfassung

### Performance-Ranking (typisch)

**Geschwindigkeit (einmalige Iteration):**
1. NumPy (numerisch) - 10-100x schneller
2. map() mit Funktion - 1.2-1.4x schneller
3. List Comprehension - 1.1-1.3x schneller
4. Generator Expression - 1.1-1.3x schneller
5. for-Loop - Baseline
6. map() mit lambda - ähnlich wie Loop

**Speichereffizienz:**
1. Generator Expression - 1000x+ effizienter
2. Itertools - sehr effizient
3. NumPy - effizient für numerische Daten
4. List Comprehension - gleich wie Loop
5. for-Loop - Baseline

**Lesbarkeit:**
1. List Comprehension - sehr pythonisch
2. Generator Expression - pythonisch
3. for-Loop - klar und explizit
4. map()/filter() - funktional aber weniger pythonisch
5. Verschachtelte Comprehensions - kann unleserlich werden

---

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [timeit](https://docs.python.org/3/library/timeit.html) - Performance-Messungen
- [cProfile](https://docs.python.org/3/library/profile.html) - Code-Profiling
- [Performance Tips](https://wiki.python.org/moin/PythonSpeed/PerformanceTips) - Offizielle Tipps
- [Time Complexity](https://wiki.python.org/moin/TimeComplexity) - Komplexitäts-Referenz

---

## Verwandte Themen

- [[02_Sequenzen/04_Comprehensions/01_List Comprehensions|List Comprehensions]] - Grundlagen
- [[02_Sequenzen/04_Comprehensions/02_Generator Expressions|Generator Expressions]] - Speichereffizienz
- [[06_Praxis/02_Performance/01_Performance Messung|Performance Messung]] - Profiling-Techniken
- [[06_Praxis/02_Performance/03_Optimization Strategien|Optimization Strategien]] - Allgemeine Optimierung

---

← [[02_Sequenzen/04_Comprehensions/03_Verschachtelte Comprehensions|Verschachtelte Comprehensions]] | [[INDEX|📑 Index]] | [[02_Sequenzen/04_Comprehensions/05_Übungen Comprehensions|Übungen Comprehensions]] →