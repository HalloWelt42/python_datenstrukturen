---
tags: [python, datenstrukturen, deque, performance, benchmarks]
created: 2025-10-02
---

← [[04_Fortgeschritten/02_Deque/02_Deque Operationen|Deque Operationen]] | [[INDEX|📑 Index]] | [[04_Fortgeschritten/02_Deque/04_Übungen Deque|Übungen Deque]] →

# Performance Vergleich

> [!tip] Lernziel
> Nach dieser Seite verstehst du genau, wann du deque statt list verwenden solltest, kennst die Performance-Unterschiede verschiedener Operationen und kannst datengetriebene Entscheidungen bei der Strukturwahl treffen.

## Übersicht: Wann welche Struktur?

| Anwendungsfall | Beste Wahl | Grund |
|----------------|------------|-------|
| **Einfügen/Entfernen am Anfang** | `deque` | O(1) vs O(n) bei list |
| **Einfügen/Entfernen am Ende** | Beide gut | Beide O(1) |
| **Häufiger Index-Zugriff** | `list` | O(1) vs O(n) bei deque |
| **Queue (FIFO)** | `deque` | Optimiert für beide Enden |
| **Stack (LIFO)** | Beide gut | Beide effizient für ein Ende |
| **Sliding Window** | `deque` | Effizientes Hinzufügen/Entfernen |
| **Random Access** | `list` | Direkter Speicherzugriff |
| **Memory Footprint** | `list` | Kompakter Speicher |

## Zeitkomplexität im Detail

### deque vs list Komplexitäten

| Operation | deque | list | Gewinner |
|-----------|-------|------|----------|
| `append(x)` | O(1) | O(1)* | Unentschieden |
| `appendleft(x)` | O(1) | O(n) | **deque** |
| `pop()` | O(1) | O(1) | Unentschieden |
| `popleft()` | O(1) | O(n) | **deque** |
| `insert(i, x)` | O(n) | O(n) | Unentschieden |
| `remove(x)` | O(n) | O(n) | Unentschieden |
| `d[i]` | O(n) | O(1) | **list** |
| `d[i] = x` | O(n) | O(1) | **list** |
| `extend(iterable)` | O(k) | O(k) | Unentschieden |
| `rotate(n)` | O(k) | N/A | **deque** |
| `clear()` | O(n) | O(n) | Unentschieden |
| `count(x)` | O(n) | O(n) | Unentschieden |
| `reverse()` | O(n) | O(n) | Unentschieden |

*O(1) amortisiert (durchschnittlich), O(n) im Worst Case bei Reallocation

## Benchmark-Vergleiche

### Test 1: Einfügen am Anfang

```python
import time
from collections import deque

def benchmark_insert_front(n=100000):
    """Vergleicht Einfügen am Anfang"""
    
    # Test mit list
    start = time.perf_counter()
    l = []
    for i in range(n):
        l.insert(0, i)  # O(n) Operation!
    list_time = time.perf_counter() - start
    
    # Test mit deque
    start = time.perf_counter()
    d = deque()
    for i in range(n):
        d.appendleft(i)  # O(1) Operation
    deque_time = time.perf_counter() - start
    
    print(f"Einfügen von {n:,} Elementen am Anfang:")
    print(f"  list:  {list_time:.4f}s")
    print(f"  deque: {deque_time:.4f}s")
    print(f"  Speedup: {list_time/deque_time:.1f}x schneller mit deque")

benchmark_insert_front()
# Beispiel-Output:
# Einfügen von 100,000 Elementen am Anfang:
#   list:  4.2341s
#   deque: 0.0089s
#   Speedup: 475.7x schneller mit deque
```

💡 **Erkenntnis:** Bei Operationen am Anfang ist deque hunderte Male schneller!

### Test 2: Einfügen am Ende

```python
import time
from collections import deque

def benchmark_insert_back(n=1000000):
    """Vergleicht Einfügen am Ende"""
    
    # Test mit list
    start = time.perf_counter()
    l = []
    for i in range(n):
        l.append(i)
    list_time = time.perf_counter() - start
    
    # Test mit deque
    start = time.perf_counter()
    d = deque()
    for i in range(n):
        d.append(i)
    deque_time = time.perf_counter() - start
    
    print(f"Einfügen von {n:,} Elementen am Ende:")
    print(f"  list:  {list_time:.4f}s")
    print(f"  deque: {deque_time:.4f}s")
    print(f"  Verhältnis: {deque_time/list_time:.2f}x")

benchmark_insert_back()
# Beispiel-Output:
# Einfügen von 1,000,000 Elementen am Ende:
#   list:  0.0523s
#   deque: 0.0687s
#   Verhältnis: 1.31x
```

💡 **Erkenntnis:** Beide sind effizient am Ende, list ist etwas schneller durch bessere Cache-Lokalität.

### Test 3: Index-Zugriff

```python
import time
from collections import deque
import random

def benchmark_index_access(n=10000, accesses=100000):
    """Vergleicht Index-Zugriff"""
    
    # Vorbereitung
    l = list(range(n))
    d = deque(range(n))
    indices = [random.randint(0, n-1) for _ in range(accesses)]
    
    # Test mit list
    start = time.perf_counter()
    for idx in indices:
        _ = l[idx]
    list_time = time.perf_counter() - start
    
    # Test mit deque
    start = time.perf_counter()
    for idx in indices:
        _ = d[idx]
    deque_time = time.perf_counter() - start
    
    print(f"{accesses:,} zufällige Zugriffe auf {n:,} Elemente:")
    print(f"  list:  {list_time:.4f}s")
    print(f"  deque: {deque_time:.4f}s")
    print(f"  Speedup: {deque_time/list_time:.1f}x schneller mit list")

benchmark_index_access()
# Beispiel-Output:
# 100,000 zufällige Zugriffe auf 10,000 Elemente:
#   list:  0.0043s
#   deque: 0.4521s
#   Speedup: 105.1x schneller mit list
```

💡 **Erkenntnis:** Für Index-Zugriff ist list massiv überlegen!

### Test 4: Iteration

```python
import time
from collections import deque

def benchmark_iteration(n=1000000):
    """Vergleicht Iteration über Elemente"""
    
    l = list(range(n))
    d = deque(range(n))
    
    # Test list
    start = time.perf_counter()
    total = sum(l)
    list_time = time.perf_counter() - start
    
    # Test deque
    start = time.perf_counter()
    total = sum(d)
    deque_time = time.perf_counter() - start
    
    print(f"Iteration über {n:,} Elemente:")
    print(f"  list:  {list_time:.4f}s")
    print(f"  deque: {deque_time:.4f}s")
    print(f"  Verhältnis: {deque_time/list_time:.2f}x")

benchmark_iteration()
# Beispiel-Output:
# Iteration über 1,000,000 Elemente:
#   list:  0.0234s
#   deque: 0.0287s
#   Verhältnis: 1.23x
```

💡 **Erkenntnis:** Beide iterieren effizient, list ist etwas schneller.

### Test 5: Queue-Operationen (FIFO)

```python
import time
from collections import deque

def benchmark_queue_operations(n=100000):
    """Vergleicht FIFO Queue-Operationen"""
    
    # Test mit list
    start = time.perf_counter()
    l = []
    for i in range(n):
        l.append(i)          # Hinzufügen am Ende: O(1)
    for i in range(n):
        _ = l.pop(0)         # Entfernen am Anfang: O(n)!
    list_time = time.perf_counter() - start
    
    # Test mit deque
    start = time.perf_counter()
    d = deque()
    for i in range(n):
        d.append(i)          # Hinzufügen am Ende: O(1)
    for i in range(n):
        _ = d.popleft()      # Entfernen am Anfang: O(1)
    deque_time = time.perf_counter() - start
    
    print(f"Queue-Operationen (FIFO) mit {n:,} Elementen:")
    print(f"  list:  {list_time:.4f}s")
    print(f"  deque: {deque_time:.4f}s")
    print(f"  Speedup: {list_time/deque_time:.1f}x schneller mit deque")

benchmark_queue_operations()
# Beispiel-Output:
# Queue-Operationen (FIFO) mit 100,000 Elementen:
#   list:  2.8932s
#   deque: 0.0124s
#   Speedup: 233.3x schneller mit deque
```

💡 **Erkenntnis:** Für Queue-Operationen ist deque die klare Wahl!

### Test 6: Stack-Operationen (LIFO)

```python
import time
from collections import deque

def benchmark_stack_operations(n=1000000):
    """Vergleicht LIFO Stack-Operationen"""
    
    # Test mit list
    start = time.perf_counter()
    l = []
    for i in range(n):
        l.append(i)
    for i in range(n):
        _ = l.pop()
    list_time = time.perf_counter() - start
    
    # Test mit deque
    start = time.perf_counter()
    d = deque()
    for i in range(n):
        d.append(i)
    for i in range(n):
        _ = d.pop()
    deque_time = time.perf_counter() - start
    
    print(f"Stack-Operationen (LIFO) mit {n:,} Elementen:")
    print(f"  list:  {list_time:.4f}s")
    print(f"  deque: {deque_time:.4f}s")
    print(f"  Verhältnis: {deque_time/list_time:.2f}x")

benchmark_stack_operations()
# Beispiel-Output:
# Stack-Operationen (LIFO) mit 1,000,000 Elementen:
#   list:  0.0847s
#   deque: 0.1023s
#   Verhältnis: 1.21x
```

💡 **Erkenntnis:** Für Stacks sind beide gut, list ist minimal schneller.

### Test 7: Rotation

```python
import time
from collections import deque

def benchmark_rotation(n=100000, rotations=1000):
    """Vergleicht Rotation"""
    
    # Test mit list (simuliert)
    start = time.perf_counter()
    l = list(range(n))
    for _ in range(rotations):
        # Rotation simulieren: Element von Ende an Anfang
        l.insert(0, l.pop())  # O(n) jedes Mal!
    list_time = time.perf_counter() - start
    
    # Test mit deque
    start = time.perf_counter()
    d = deque(range(n))
    for _ in range(rotations):
        d.rotate(1)  # O(1) Operation
    deque_time = time.perf_counter() - start
    
    print(f"{rotations:,} Rotationen von {n:,} Elementen:")
    print(f"  list:  {list_time:.4f}s")
    print(f"  deque: {deque_time:.4f}s")
    print(f"  Speedup: {list_time/deque_time:.1f}x schneller mit deque")

benchmark_rotation()
# Beispiel-Output:
# 1,000 Rotationen von 100,000 Elementen:
#   list:  12.3456s
#   deque: 0.0003s
#   Speedup: 41152.0x schneller mit deque
```

💡 **Erkenntnis:** Rotation ist eine Killer-Feature von deque!

## Memory-Overhead Vergleich

### Speicherverbrauch messen

```python
import sys
from collections import deque

def compare_memory(sizes=[100, 1000, 10000, 100000]):
    """Vergleicht Speicherverbrauch"""
    print("Speicherverbrauch (Bytes):")
    print(f"{'Größe':<10} {'list':>15} {'deque':>15} {'Overhead':>10}")
    print("-" * 55)
    
    for size in sizes:
        l = list(range(size))
        d = deque(range(size))
        
        list_size = sys.getsizeof(l)
        deque_size = sys.getsizeof(d)
        overhead = ((deque_size - list_size) / list_size) * 100
        
        print(f"{size:<10,} {list_size:>15,} {deque_size:>15,} {overhead:>9.1f}%")

compare_memory()
# Beispiel-Output:
# Speicherverbrauch (Bytes):
# Größe            list           deque    Overhead
# -------------------------------------------------------
# 100               920           1,104       20.0%
# 1,000           8,856          10,288       16.2%
# 10,000         87,624         103,072       17.6%
# 100,000       835,128         983,696       17.8%
```

💡 **Erkenntnis:** deque hat etwa 15-20% mehr Overhead durch Block-Struktur.

### Speicher-Effizienz bei maxlen

```python
import sys
from collections import deque

def compare_bounded_memory():
    """Vergleicht Speicher bei bounded deque"""
    
    # Unbounded
    d_unbounded = deque(range(10000))
    
    # Bounded
    d_bounded = deque(range(10000), maxlen=10000)
    
    # Liste zum Vergleich
    l = list(range(10000))
    
    print("Speicherverbrauch für 10,000 Elemente:")
    print(f"  list:              {sys.getsizeof(l):>8,} Bytes")
    print(f"  deque (unbounded): {sys.getsizeof(d_unbounded):>8,} Bytes")
    print(f"  deque (bounded):   {sys.getsizeof(d_bounded):>8,} Bytes")

compare_bounded_memory()
# Beispiel-Output:
# Speicherverbrauch für 10,000 Elemente:
#   list:               87,624 Bytes
#   deque (unbounded): 103,072 Bytes
#   deque (bounded):   103,072 Bytes
```

💡 **Erkenntnis:** maxlen spart keinen Speicher, sondern verhindert nur Wachstum.

## Entscheidungsbaum: Welche Struktur wählen?

```python
def choose_data_structure(operations):
    """
    Hilft bei der Strukturwahl basierend auf Operationen.
    
    operations: dict mit Häufigkeiten, z.B.
    {
        'append_left': 1000,
        'append_right': 100,
        'pop_left': 1000,
        'random_access': 10,
        'iterate': 5
    }
    """
    # Gewichtung: Wie schlecht ist Operation für Struktur?
    list_penalties = {
        'append_left': 1000,   # O(n) - sehr schlecht
        'pop_left': 1000,      # O(n) - sehr schlecht
        'append_right': 1,     # O(1) - gut
        'pop_right': 1,        # O(1) - gut
        'random_access': 1,    # O(1) - gut
        'iterate': 1,          # O(n) - gut
    }
    
    deque_penalties = {
        'append_left': 1,      # O(1) - gut
        'pop_left': 1,         # O(1) - gut
        'append_right': 1,     # O(1) - gut
        'pop_right': 1,        # O(1) - gut
        'random_access': 100,  # O(n) - schlecht
        'iterate': 1.2,        # O(n) - okay
    }
    
    list_score = sum(operations.get(op, 0) * penalty 
                     for op, penalty in list_penalties.items())
    deque_score = sum(operations.get(op, 0) * penalty 
                      for op, penalty in deque_penalties.items())
    
    print(f"Penalty Scores (niedriger = besser):")
    print(f"  list:  {list_score:>10,}")
    print(f"  deque: {deque_score:>10,}")
    print()
    
    if list_score < deque_score * 0.8:
        print("Empfehlung: list")
        print("Grund: Viel Random Access, wenig Operationen am Anfang")
    elif deque_score < list_score * 0.8:
        print("Empfehlung: deque")
        print("Grund: Viele Operationen am Anfang")
    else:
        print("Empfehlung: Beide ähnlich gut")

# Beispiel 1: Queue-artige Nutzung
print("=== Szenario 1: Queue ===")
choose_data_structure({
    'append_right': 10000,
    'pop_left': 10000,
})

print("\n=== Szenario 2: Viel Random Access ===")
choose_data_structure({
    'append_right': 1000,
    'random_access': 50000,
    'iterate': 100,
})

print("\n=== Szenario 3: Gemischt ===")
choose_data_structure({
    'append_left': 1000,
    'append_right': 1000,
    'random_access': 1000,
    'iterate': 100,
})
```

## Praktische Anwendungsfälle im Detail

### Anwendungsfall 1: Web Server Request Queue

```python
from collections import deque
import time

class RequestQueue:
    """Effiziente Request-Queue mit deque"""
    
    def __init__(self):
        self.queue = deque()
        self.processed = 0
    
    def add_request(self, request):
        """O(1) - Neue Requests hinzufügen"""
        self.queue.append(request)
    
    def process_next(self):
        """O(1) - Ältesten Request verarbeiten"""
        if self.queue:
            request = self.queue.popleft()
            self.processed += 1
            return request
        return None
    
    def get_stats(self):
        return {
            'pending': len(self.queue),
            'processed': self.processed
        }

# Simulation
queue = RequestQueue()

# 10000 Requests hinzufügen
start = time.perf_counter()
for i in range(10000):
    queue.add_request(f"Request-{i}")
add_time = time.perf_counter() - start

# 10000 Requests verarbeiten
start = time.perf_counter()
while queue.queue:
    queue.process_next()
process_time = time.perf_counter() - start

print(f"Hinzufügen: {add_time:.4f}s")
print(f"Verarbeiten: {process_time:.4f}s")
print(f"Total: {add_time + process_time:.4f}s")
# Mit list würde process_time ~100x länger dauern!
```

### Anwendungsfall 2: Sliding Window Maximum (Optimiert)

```python
from collections import deque
import time

def sliding_max_deque(arr, k):
    """Optimierte Version mit monotoner deque - O(n)"""
    if not arr or k <= 0:
        return []
    
    result = []
    dq = deque()  # Speichert Indizes
    
    for i in range(len(arr)):
        # Entferne alte Indizes
        while dq and dq[0] <= i - k:
            dq.popleft()
        
        # Entferne kleinere Elemente
        while dq and arr[dq[-1]] < arr[i]:
            dq.pop()
        
        dq.append(i)
        
        if i >= k - 1:
            result.append(arr[dq[0]])
    
    return result

def sliding_max_naive(arr, k):
    """Naive Version mit max() - O(nk)"""
    if not arr or k <= 0:
        return []
    
    result = []
    for i in range(len(arr) - k + 1):
        result.append(max(arr[i:i+k]))
    
    return result

# Performance-Vergleich
test_data = list(range(10000, 0, -1))
k = 100

start = time.perf_counter()
result_naive = sliding_max_naive(test_data, k)
naive_time = time.perf_counter() - start

start = time.perf_counter()
result_deque = sliding_max_deque(test_data, k)
deque_time = time.perf_counter() - start

print(f"Naive (O(nk)): {naive_time:.4f}s")
print(f"Deque (O(n)):  {deque_time:.4f}s")
print(f"Speedup: {naive_time/deque_time:.1f}x")
```

## Best Practices Zusammenfassung

### ✅ Verwende deque wenn:

1. **Queue-Operationen (FIFO)**
   ```python
   from collections import deque
   queue = deque()
   queue.append(item)      # Hinzufügen
   item = queue.popleft()  # Entfernen
   ```

2. **Operationen an beiden Enden**
   ```python
   d = deque()
   d.appendleft(item)  # Am Anfang
   d.append(item)      # Am Ende
   ```

3. **Bounded Buffer/Cache**
   ```python
   recent = deque(maxlen=100)  # Automatisches Eviction
   ```

4. **Rotation benötigt**
   ```python
   d = deque([1, 2, 3, 4, 5])
   d.rotate(2)  # Effiziente Rotation
   ```

### ✅ Verwende list wenn:

1. **Häufiger Random Access**
   ```python
   l = [1, 2, 3, 4, 5]
   value = l[2]  # O(1) statt O(n)
   ```

2. **Nur Operationen am Ende**
   ```python
   stack = []
   stack.append(item)  # Push
   item = stack.pop()  # Pop
   ```

3. **Slicing benötigt**
   ```python
   l = [1, 2, 3, 4, 5]
   sub = l[1:4]  # list unterstützt Slicing
   ```

4. **Memory-kritisch**
   ```python
   # list hat ~15% weniger Overhead
   ```

## Übungsaufgaben

Siehe [[04_Fortgeschritten/02_Deque/04_Übungen Deque|Übungen Deque]] für praktische Aufgaben.

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [TimeComplexity Wiki](https://wiki.python.org/moin/TimeComplexity) - Offizielle Komplexitäts-Übersicht
- [deque Performance](https://docs.python.org/3/library/collections.html#deque-objects) - Deque Performance-Notizen
- [timeit - Performance Measurement](https://docs.python.org/3/library/timeit.html) - Tool für Benchmarks

## Verwandte Themen

- [[04_Fortgeschritten/02_Deque/01_Deque Basics|Deque Basics]] - Grundlagen
- [[04_Fortgeschritten/02_Deque/02_Deque Operationen|Deque Operationen]] - Alle Methoden
- [[02_Sequenzen/01_Listen/05_List Performance|List Performance]] - Listen-Performance
- [[06_Praxis/02_Performance/01_Performance Messung|Performance Messung]] - Benchmark-Techniken

---
← [[04_Fortgeschritten/02_Deque/02_Deque Operationen|Deque Operationen]] | [[INDEX|📑 Index]] | [[04_Fortgeschritten/02_Deque/04_Übungen Deque|Übungen Deque]] →