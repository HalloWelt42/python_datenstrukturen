---
tags: [python, datenstrukturen, performance, profiling]
created: 2025-10-02
---

← [[06_Praxis/01_Best_Practices/03_Datenstruktur Auswahl|Datenstruktur Auswahl]] | [[INDEX|📑 Index]] | [[06_Praxis/02_Performance/02_Memory Profiling|Memory Profiling]] →

# Performance Messung

> [!tip] Lernziel
> Du kannst Performance-Probleme identifizieren, Code-Performance messen und Bottlenecks finden.

## Warum Performance messen?

> "Premature optimization is the root of all evil" - Donald Knuth

Aber: Messen ist wichtig um zu wissen **wo** optimiert werden muss.

**Goldene Regeln:**
1. **Erst messen, dann optimieren** - Vermutungen sind oft falsch
2. **Bottlenecks finden** - 80/20 Regel (80% Zeit in 20% Code)
3. **Vorher/Nachher vergleichen** - Objektivität

## timeit - Einfache Zeitmessung

### Basis-Verwendung

```python
import timeit

# Einfacher String
code = """
result = []
for i in range(1000):
    result.append(i * 2)
"""

time = timeit.timeit(code, number=1000)
print(f"Zeit: {time:.4f} Sekunden")

# Vergleich: List Comprehension
code2 = "[i * 2 for i in range(1000)]"
time2 = timeit.timeit(code2, number=1000)
print(f"Zeit: {time2:.4f} Sekunden")
print(f"Speedup: {time / time2:.2f}x")
```

### Mit Setup

```python
import timeit

# Setup-Code (wird nur einmal ausgeführt)
setup = """
data = list(range(10000))
"""

# Test-Code
code = "sum(data)"

time = timeit.timeit(code, setup=setup, number=1000)
print(f"Zeit: {time:.6f}s")
```

### Funktionen testen

```python
import timeit

def list_append():
    result = []
    for i in range(1000):
        result.append(i)
    return result

def list_comprehension():
    return [i for i in range(1000)]

# Methode 1: Mit globals
time1 = timeit.timeit("list_append()", globals=globals(), number=1000)
time2 = timeit.timeit("list_comprehension()", globals=globals(), number=1000)

print(f"Append:        {time1:.4f}s")
print(f"Comprehension: {time2:.4f}s")
print(f"Speedup:       {time1/time2:.2f}x")

# Methode 2: Mit lambda
time1 = timeit.timeit(lambda: list_append(), number=1000)
time2 = timeit.timeit(lambda: list_comprehension(), number=1000)
```

### Praktisches Beispiel: Datenstruktur-Vergleich

```python
import timeit

def benchmark_membership():
    """Vergleicht Membership-Tests verschiedener Strukturen"""
    
    sizes = [100, 1000, 10000]
    
    for size in sizes:
        print(f"\n=== Size: {size} ===")
        
        # Setup
        setup = f"""
data_list = list(range({size}))
data_set = set(range({size}))
data_dict = {{i: i for i in range({size})}}
target = {size - 1}  # Letztes Element
"""
        
        # Tests
        list_time = timeit.timeit(
            "target in data_list",
            setup=setup,
            number=10000
        )
        
        set_time = timeit.timeit(
            "target in data_set",
            setup=setup,
            number=10000
        )
        
        dict_time = timeit.timeit(
            "target in data_dict",
            setup=setup,
            number=10000
        )
        
        print(f"List: {list_time:.6f}s")
        print(f"Set:  {set_time:.6f}s (speedup: {list_time/set_time:.0f}x)")
        print(f"Dict: {dict_time:.6f}s (speedup: {list_time/dict_time:.0f}x)")

benchmark_membership()
```

## time - Einfache Zeit-Tracker

```python
import time

# Einfache Zeitmessung
start = time.time()

# Code ausführen
result = sum(range(1000000))

end = time.time()
print(f"Dauer: {end - start:.4f}s")

# Context Manager für wiederverwendbare Messung
from contextlib import contextmanager

@contextmanager
def timer(name="Operation"):
    """Context Manager für Zeitmessung"""
    start = time.perf_counter()
    yield
    end = time.perf_counter()
    print(f"{name}: {end - start:.4f}s")

# Verwendung
with timer("Sum calculation"):
    result = sum(range(1000000))

with timer("List comprehension"):
    squares = [i**2 for i in range(100000)]
```

## cProfile - Detailliertes Profiling

### Basis-Profiling

```python
import cProfile
import pstats

def slow_function():
    """Funktion mit Performance-Problem"""
    total = 0
    for i in range(1000):
        for j in range(1000):
            total += i * j
    return total

# Profiling
cProfile.run("slow_function()")

# Output zeigt:
# - ncalls: Anzahl Aufrufe
# - tottime: Zeit in dieser Funktion
# - cumtime: Zeit inkl. Subfunktionen
# - filename:lineno: Wo die Funktion ist
```

### Sortiertes Profiling

```python
import cProfile
import pstats
from io import StringIO

def profile_function(func, *args, **kwargs):
    """Profiled eine Funktion und zeigt Top-Funktionen"""
    profiler = cProfile.Profile()
    profiler.enable()
    
    result = func(*args, **kwargs)
    
    profiler.disable()
    
    # Stats in String-Buffer
    s = StringIO()
    stats = pstats.Stats(profiler, stream=s)
    stats.sort_stats("cumulative")
    stats.print_stats(10)  # Top 10
    
    print(s.getvalue())
    return result

# Verwendung
def complex_operation():
    data = [i**2 for i in range(10000)]
    filtered = [x for x in data if x % 2 == 0]
    return sum(filtered)

profile_function(complex_operation)
```

### Praktisches Beispiel: Bottleneck finden

```python
import cProfile
import pstats

def process_data(data):
    """Verarbeitet Daten - wo ist der Bottleneck?"""
    # Step 1: Parse
    parsed = parse_items(data)
    
    # Step 2: Filter
    filtered = filter_items(parsed)
    
    # Step 3: Transform
    transformed = transform_items(filtered)
    
    # Step 4: Aggregate
    result = aggregate_items(transformed)
    
    return result

def parse_items(data):
    time.sleep(0.1)  # Simuliert langsame Operation
    return [int(x) for x in data]

def filter_items(items):
    return [x for x in items if x > 0]

def transform_items(items):
    time.sleep(0.5)  # Simuliert sehr langsame Operation
    return [x * 2 for x in items]

def aggregate_items(items):
    return sum(items)

# Profiling
profiler = cProfile.Profile()
profiler.enable()

data = ["1", "2", "3", "4", "5"] * 100
result = process_data(data)

profiler.disable()

# Analyse
stats = pstats.Stats(profiler)
stats.sort_stats("cumulative")
stats.print_stats()

# Output zeigt: transform_items ist der Bottleneck!
```

## line_profiler - Zeile-für-Zeile

```python
# Installation: pip install line_profiler

# Verwendung als Decorator
from line_profiler import LineProfiler

def slow_function():
    total = 0
    for i in range(1000):        # Zeile X
        temp = i ** 2            # Zeile Y
        for j in range(100):     # Zeile Z
            total += temp + j    # Zeile W
    return total

# Profiling
profiler = LineProfiler()
profiler.add_function(slow_function)
profiler.enable()

slow_function()

profiler.disable()
profiler.print_stats()

# Output zeigt Zeit pro Zeile:
# Line    Hits    Time    Per Hit    % Time  Line Contents
# ====    ====    ====    =======    ======  =============
#  123    1       0.5     0.5        0.1     total = 0
#  124    1000    50.2    0.05       10.0    for i in range(1000):
#  125    1000    100.4   0.10       20.0        temp = i ** 2
#  ...
```

## Praktische Performance-Tests

### String Concatenation

```python
import timeit

# Test 1: += Operator
def concat_plus():
    result = ""
    for i in range(1000):
        result += str(i)
    return result

# Test 2: join()
def concat_join():
    return "".join(str(i) for i in range(1000))

# Test 3: Liste + join()
def concat_list_join():
    parts = []
    for i in range(1000):
        parts.append(str(i))
    return "".join(parts)

# Benchmark
n = 1000

t1 = timeit.timeit(concat_plus, number=n)
t2 = timeit.timeit(concat_join, number=n)
t3 = timeit.timeit(concat_list_join, number=n)

print(f"Concatenation +=:     {t1:.4f}s")
print(f"Generator + join():   {t2:.4f}s (speedup: {t1/t2:.1f}x)")
print(f"List + join():        {t3:.4f}s (speedup: {t1/t3:.1f}x)")

# Ergebnis: join() ist viel schneller!
```

### List vs. Generator

```python
import timeit
import sys

# Memory-Vergleich
list_comp = [i**2 for i in range(100000)]
gen_expr = (i**2 for i in range(100000))

print(f"List size:      {sys.getsizeof(list_comp):,} bytes")
print(f"Generator size: {sys.getsizeof(gen_expr):,} bytes")

# Zeit-Vergleich für sum()
setup = "data = range(100000)"

list_time = timeit.timeit(
    "sum([i**2 for i in data])",
    setup=setup,
    number=100
)

gen_time = timeit.timeit(
    "sum(i**2 for i in data)",
    setup=setup,
    number=100
)

print(f"\nList comp: {list_time:.4f}s")
print(f"Generator: {gen_time:.4f}s")
print(f"Speedup:   {list_time/gen_time:.2f}x")
```

### Dictionary Lookup vs. If-Elif

```python
import timeit

# Option 1: if-elif chain
def process_if_elif(value):
    if value == 1:
        return "one"
    elif value == 2:
        return "two"
    elif value == 3:
        return "three"
    elif value == 4:
        return "four"
    elif value == 5:
        return "five"
    else:
        return "unknown"

# Option 2: Dictionary lookup
LOOKUP = {
    1: "one",
    2: "two",
    3: "three",
    4: "four",
    5: "five"
}

def process_dict(value):
    return LOOKUP.get(value, "unknown")

# Benchmark
setup = "value = 5"  # Letzter Wert - worst case für if-elif

t1 = timeit.timeit("process_if_elif(value)", setup=setup, globals=globals(), number=100000)
t2 = timeit.timeit("process_dict(value)", setup=setup, globals=globals(), number=100000)

print(f"if-elif: {t1:.6f}s")
print(f"dict:    {t2:.6f}s")
print(f"Speedup: {t1/t2:.2f}x")
```

## Decorator für automatisches Timing

```python
import time
from functools import wraps

def timing_decorator(func):
    """Decorator der Ausführungszeit misst"""
    @wraps(func)
    def wrapper(*args, **kwargs):
        start = time.perf_counter()
        result = func(*args, **kwargs)
        end = time.perf_counter()
        print(f"{func.__name__}: {end - start:.4f}s")
        return result
    return wrapper

# Verwendung
@timing_decorator
def slow_calculation(n):
    total = 0
    for i in range(n):
        total += i ** 2
    return total

result = slow_calculation(1000000)
# Output: slow_calculation: 0.1234s

# Erweiterte Version mit Statistiken
import statistics

class TimingStats:
    def __init__(self):
        self.times = {}
    
    def __call__(self, func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            start = time.perf_counter()
            result = func(*args, **kwargs)
            end = time.perf_counter()
            
            elapsed = end - start
            if func.__name__ not in self.times:
                self.times[func.__name__] = []
            self.times[func.__name__].append(elapsed)
            
            return result
        return wrapper
    
    def report(self):
        for name, times in self.times.items():
            avg = statistics.mean(times)
            median = statistics.median(times)
            count = len(times)
            total = sum(times)
            print(f"{name}:")
            print(f"  Calls: {count}")
            print(f"  Total: {total:.4f}s")
            print(f"  Avg:   {avg:.6f}s")
            print(f"  Median: {median:.6f}s")

# Verwendung
timer = TimingStats()

@timer
def function_a():
    time.sleep(0.01)

@timer
def function_b():
    time.sleep(0.02)

# Mehrmals aufrufen
for _ in range(10):
    function_a()
    function_b()

timer.report()
```

## Benchmark-Framework

```python
import timeit
import statistics
from typing import Callable, Dict, List

class Benchmark:
    """Framework für Benchmarks"""
    
    def __init__(self, name: str, number: int = 1000):
        self.name = name
        self.number = number
        self.results: Dict[str, List[float]] = {}
    
    def add(self, name: str, func: Callable, *args, **kwargs):
        """Fügt Benchmark hinzu"""
        times = []
        
        # Mehrere Durchläufe für Statistik
        for _ in range(5):
            time = timeit.timeit(
                lambda: func(*args, **kwargs),
                number=self.number
            )
            times.append(time)
        
        self.results[name] = times
    
    def report(self):
        """Zeigt Ergebnisse"""
        print(f"\n=== {self.name} ===")
        print(f"Number of runs: {self.number}\n")
        
        # Finde schnellste als Baseline
        fastest_avg = min(statistics.mean(times) for times in self.results.values())
        
        for name, times in self.results.items():
            avg = statistics.mean(times)
            median = statistics.median(times)
            stdev = statistics.stdev(times) if len(times) > 1 else 0
            speedup = avg / fastest_avg
            
            print(f"{name}:")
            print(f"  Avg:    {avg:.6f}s")
            print(f"  Median: {median:.6f}s")
            print(f"  Stdev:  {stdev:.6f}s")
            print(f"  Speedup: {speedup:.2f}x")
            print()

# Verwendung
bench = Benchmark("List Creation Methods", number=10000)

bench.add("For loop", lambda: [i for i in range(1000)])
bench.add("List comp", lambda: list(range(1000)))
bench.add("Generator", lambda: list(i for i in range(1000)))

bench.report()
```

## Best Practices

### 1. Aussagekräftige Benchmarks

```python
# ❌ Schlecht - zu kurz
timeit.timeit("sum(range(10))", number=10)

# ✅ Gut - genug Wiederholungen
timeit.timeit("sum(range(10))", number=100000)

# ❌ Schlecht - nicht isoliert
timeit.timeit("results.append(i)", number=1000)

# ✅ Gut - mit Setup
setup = "results = []"
timeit.timeit("results.append(1)", setup=setup, number=1000)
```

### 2. Warmup berücksichtigen

```python
import timeit

# Mit Warmup
setup = """
def function():
    return sum(range(1000))

# Warmup
for _ in range(100):
    function()
"""

time = timeit.timeit("function()", setup=setup, number=1000)
```

### 3. Caching beachten

```python
# ❌ Kann durch Caching verfälscht werden
def test():
    return sum(range(1000))

# Mehrmals gemessen - wird schneller durch CPU-Cache
for i in range(3):
    t = timeit.timeit(test, number=10000)
    print(f"Run {i+1}: {t:.4f}s")

# ✅ Besser: Neue Daten pro Run
def test_with_new_data(n):
    return sum(range(n))

for i in range(3):
    t = timeit.timeit(lambda: test_with_new_data(1000), number=10000)
    print(f"Run {i+1}: {t:.4f}s")
```

## Zusammenfassung

**Tool-Auswahl:**

| Tool | Verwendung | Granularität |
|------|-----------|--------------|
| `timeit` | Mikro-Benchmarks | Funktion/Code-Snippet |
| `time.perf_counter()` | Einfache Messungen | Code-Block |
| `cProfile` | Gesamt-Profiling | Funktionsebene |
| `line_profiler` | Detailliertes Profiling | Zeile-für-Zeile |
| Custom Decorator | Produktions-Monitoring | Funktion |

**Workflow:**
1. `timeit` für schnelle Vergleiche
2. `cProfile` um Bottlenecks zu finden
3. `line_profiler` für Details
4. Optimieren und erneut messen

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [timeit](https://docs.python.org/3/library/timeit.html) - Performance-Messung
- [profile/cProfile](https://docs.python.org/3/library/profile.html) - Profiling
- [time](https://docs.python.org/3/library/time.html) - Zeit-Funktionen
- [line_profiler](https://github.com/pyutils/line_profiler) - Line-by-line Profiling

## Verwandte Themen

- [[06_Praxis/02_Performance/02_Memory Profiling|Memory Profiling]] - Speicher-Analyse
- [[06_Praxis/02_Performance/03_Optimization Strategien|Optimization Strategien]] - Optimierungs-Techniken
- [[06_Praxis/01_Best_Practices/03_Datenstruktur Auswahl|Datenstruktur Auswahl]] - Performance-optimale Strukturen

---
← [[06_Praxis/01_Best_Practices/03_Datenstruktur Auswahl|Datenstruktur Auswahl]] | [[INDEX|📑 Index]] | [[06_Praxis/02_Performance/02_Memory Profiling|Memory Profiling]] →