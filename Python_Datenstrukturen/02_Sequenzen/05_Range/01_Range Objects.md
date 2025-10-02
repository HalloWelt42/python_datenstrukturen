---
tags: [python, datenstrukturen, range, sequences, lazy]
created: 2025-10-01
---

← [[02_Sequenzen/04_Comprehensions/05_Übungen Comprehensions|Übungen Comprehensions]] | [[INDEX|📑 Index]] | [[02_Sequenzen/05_Range/02_Übungen Range|Übungen Range]] →

# Range Objects - Lazy Integer Sequences

> [!tip] Lernziel
> Nach dieser Seite verstehst du Range-Objekte als speichereffiziente, unveränderliche Sequenzen von Zahlen. Du kennst alle Parameter, Anwendungsfälle und Performance-Vorteile gegenüber Listen.

## Was sind Range-Objekte?

**Range** ist ein eingebauter Typ, der eine unveränderliche Sequenz von Zahlen repräsentiert. Im Gegensatz zu Listen werden die Werte **nicht gespeichert**, sondern **on-demand berechnet** (Lazy Evaluation).

**Hauptmerkmale:**
- ✅ **Speichereffizient** - Konstanter Speicher, unabhängig von Größe
- ✅ **Lazy** - Werte werden bei Bedarf berechnet
- ✅ **Unveränderlich** - Kann nicht modifiziert werden
- ✅ **Schnell** - Optimiert für Iteration
- ✅ **Indexierbar** - Unterstützt Index-Zugriff und Slicing
- ✅ **Hashbar** - Kann in Sets verwendet werden

```python
# Range erstellen
numbers = range(10)
print(numbers)  # range(0, 10)
print(type(numbers))  # <class 'range'>

# Kein Speicher für Werte!
import sys
print(f"Liste [0..999999]: {sys.getsizeof(list(range(1000000))) / 1024 / 1024:.2f} MB")
print(f"Range(1000000):    {sys.getsizeof(range(1000000))} Bytes")
# Liste: ~8 MB, Range: ~48 Bytes!
```

---

## Syntax und Parameter

### Die drei Formen

```python
range(stop)
range(start, stop)
range(start, stop, step)
```

**Parameter:**
- **stop**: Endwert (exklusiv) - PFLICHT
- **start**: Startwert (inklusiv, default=0) - optional
- **step**: Schrittweite (default=1) - optional

```python
# Ein Parameter: nur stop
r = range(10)
print(list(r))  # [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

# Zwei Parameter: start und stop
r = range(5, 10)
print(list(r))  # [5, 6, 7, 8, 9]

# Drei Parameter: start, stop, step
r = range(0, 20, 3)
print(list(r))  # [0, 3, 6, 9, 12, 15, 18]

# Negativer Step - rückwärts
r = range(10, 0, -1)
print(list(r))  # [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]

# Negativer start und stop
r = range(-5, 5)
print(list(r))  # [-5, -4, -3, -2, -1, 0, 1, 2, 3, 4]
```

### Leere Ranges

```python
# Stop <= Start mit positivem Step
r = range(5, 5)
print(list(r))  # []

r = range(5, 3)
print(list(r))  # []

# Stop >= Start mit negativem Step
r = range(5, 10, -1)
print(list(r))  # []

# Funktioniert mit 0
r = range(0)
print(list(r))  # []
```

---

## Zugriff und Operationen

### Index-Zugriff

```python
r = range(10, 20)

# Positiver Index
print(r[0])   # 10
print(r[5])   # 15
print(r[9])   # 19

# Negativer Index
print(r[-1])  # 19
print(r[-3])  # 17

# IndexError bei ungültigem Index
try:
    print(r[100])
except IndexError as e:
    print(f"Fehler: {e}")
```

### Slicing

```python
r = range(0, 20, 2)  # [0, 2, 4, 6, 8, 10, 12, 14, 16, 18]

# Slicing gibt neues Range zurück (Python 3.6+)
subset = r[2:7]
print(subset)       # range(4, 14, 2)
print(list(subset)) # [4, 6, 8, 10, 12]

# Mit Step
every_second = r[::2]
print(list(every_second))  # [0, 4, 8, 12, 16]

# Rückwärts
reversed_r = r[::-1]
print(list(reversed_r))  # [18, 16, 14, 12, 10, 8, 6, 4, 2, 0]
```

### Länge und Membership

```python
r = range(10, 100, 5)

# Länge - O(1)
print(len(r))  # 18

# Membership - O(1) bei Range!
print(50 in r)  # True
print(51 in r)  # False
print(15 in r)  # True

# min() und max()
print(min(r))  # 10
print(max(r))  # 95

# Effizient ohne Iteration!
```

---

## Range vs Liste

### Speicherverbrauch

```python
import sys

# Verschiedene Größen testen
sizes = [100, 1000, 10000, 100000, 1000000]

print(f"{'Größe':>10} {'Liste (KB)':>12} {'Range (Bytes)':>15} {'Faktor'}")
print("-" * 55)

for n in sizes:
    list_size = sys.getsizeof(list(range(n)))
    range_size = sys.getsizeof(range(n))
    factor = list_size / range_size
    
    print(f"{n:>10,} {list_size/1024:>11.2f} {range_size:>15} {factor:>10.0f}x")
```

**Typische Ausgabe:**
```
     Größe   Liste (KB)   Range (Bytes)     Faktor
-------------------------------------------------------
       100         0.90              48         19x
     1,000         8.69              48        188x
    10,000        87.63              48      1,896x
   100,000       859.54              48     18,590x
 1,000,000     8,589.93              48    185,899x
```

### Performance beim Iterieren

```python
import time

n = 10000000

# Mit Liste - muss erst erstellt werden
start = time.perf_counter()
numbers = list(range(n))
for x in numbers:
    pass
time_list = time.perf_counter() - start

# Mit Range - direkte Iteration
start = time.perf_counter()
for x in range(n):
    pass
time_range = time.perf_counter() - start

print(f"Liste:  {time_list:.4f}s")
print(f"Range:  {time_range:.4f}s")
print(f"Faktor: {time_list/time_range:.2f}x")
```

**Typische Ausgabe:**
```
Liste:  0.8234s
Range:  0.3567s
Faktor: 2.31x  ← Range ist ~2x schneller!
```

---

## Praktische Anwendungen

### Anwendungsfall 1: Traditionelle Loops

```python
# for-Loop mit Index
for i in range(10):
    print(f"Iteration {i}")

# Bestimmter Bereich
for i in range(5, 15):
    print(f"Zahl: {i}")

# Mit Schrittweite
for i in range(0, 100, 10):
    print(f"Vielfaches von 10: {i}")

# Countdown
for i in range(10, 0, -1):
    print(f"Countdown: {i}")
print("Start!")
```

### Anwendungsfall 2: Index-basierte Iteration

```python
# Über Liste mit Index iterieren
words = ["Python", "is", "awesome"]

# Traditionell
for i in range(len(words)):
    print(f"{i}: {words[i]}")

# Pythonischer: enumerate()
for i, word in enumerate(words):
    print(f"{i}: {word}")

# Rückwärts iterieren
for i in range(len(words) - 1, -1, -1):
    print(f"{i}: {words[i]}")

# Pythonischer: reversed()
for i, word in enumerate(reversed(words)):
    print(f"{len(words)-1-i}: {word}")
```

### Anwendungsfall 3: Numerische Sequenzen

```python
# Multiplikationstabelle
for i in range(1, 11):
    for j in range(1, 11):
        print(f"{i*j:4}", end="")
    print()

# Summation
total = sum(range(101))  # 0 + 1 + 2 + ... + 100
print(f"Summe 1-100: {total}")  # 5050

# Fakultät
def factorial(n):
    result = 1
    for i in range(1, n + 1):
        result *= i
    return result

print(f"10! = {factorial(10)}")  # 3628800

# Fibonacci
def fibonacci(n):
    a, b = 0, 1
    for _ in range(n):
        yield a
        a, b = b, a + b

fib_10 = list(fibonacci(10))
print(f"Fibonacci(10): {fib_10}")
```

### Anwendungsfall 4: Koordinaten und Grids

```python
# 2D-Grid
width, height = 5, 3
for y in range(height):
    for x in range(width):
        print(f"({x},{y})", end=" ")
    print()

# Mit List Comprehension
grid = [(x, y) for y in range(height) for x in range(width)]
print(grid)

# Schachbrett-Muster
for row in range(8):
    for col in range(8):
        if (row + col) % 2 == 0:
            print("⬜", end="")
        else:
            print("⬛", end="")
    print()
```

### Anwendungsfall 5: Pagination

```python
def paginate(items, page_size):
    """Teilt items in Seiten"""
    total = len(items)
    num_pages = (total + page_size - 1) // page_size
    
    for page in range(num_pages):
        start = page * page_size
        end = min(start + page_size, total)
        yield page + 1, items[start:end]

# Verwendung
data = list(range(27))
for page_num, page_items in paginate(data, 10):
    print(f"Seite {page_num}: {page_items}")
```

---

## Range als Sequenz

### Sequenz-Operationen

```python
r = range(5, 15)

# Länge
print(len(r))  # 10

# Min und Max
print(min(r))  # 5
print(max(r))  # 14

# Index-Zugriff
print(r[0])    # 5
print(r[-1])   # 14

# Slicing
print(r[2:7])  # range(7, 12)

# Iteration
for x in r:
    print(x, end=" ")
# 5 6 7 8 9 10 11 12 13 14

# In Liste konvertieren
lst = list(r)
print(lst)  # [5, 6, 7, 8, 9, 10, 11, 12, 13, 14]
```

### Vergleich und Hashing

```python
# Gleichheit
r1 = range(5)
r2 = range(5)
r3 = range(0, 5, 1)
r4 = range(3)

print(r1 == r2)  # True
print(r1 == r3)  # True
print(r1 == r4)  # False

# Range ist hashbar
range_set = {range(5), range(10), range(5, 10)}
print(range_set)  # {range(0, 5), range(0, 10), range(5, 10)}

# Als Dictionary-Key
range_dict = {
    range(5): "kleine Zahlen",
    range(10, 20): "mittlere Zahlen"
}
print(range_dict[range(5)])  # "kleine Zahlen"
```

---

## Fortgeschrittene Techniken

### Range mit negativen Zahlen

```python
# Negative Bereiche
negative = range(-10, -1)
print(list(negative))  # [-10, -9, -8, -7, -6, -5, -4, -3, -2]

# Von negativ zu positiv
crossing = range(-5, 5)
print(list(crossing))  # [-5, -4, -3, -2, -1, 0, 1, 2, 3, 4]

# Rückwärts mit Negativen
backward = range(-1, -11, -1)
print(list(backward))  # [-1, -2, -3, -4, -5, -6, -7, -8, -9, -10]
```

### Range Arithmetik

```python
# Start, Stop, Step extrahieren
r = range(10, 100, 5)
print(f"Start: {r.start}")  # 10
print(f"Stop:  {r.stop}")   # 100
print(f"Step:  {r.step}")   # 5

# Eigenschaften prüfen
def range_info(r):
    return {
        'start': r.start,
        'stop': r.stop,
        'step': r.step,
        'length': len(r),
        'first': r[0] if len(r) > 0 else None,
        'last': r[-1] if len(r) > 0 else None
    }

print(range_info(range(5, 50, 7)))
```

### Range mit enumerate und zip

```python
# enumerate mit range
for i, val in enumerate(range(10, 20, 2)):
    print(f"Index {i}: Value {val}")
# Index 0: Value 10
# Index 1: Value 12
# ...

# zip mit range
letters = ['a', 'b', 'c', 'd', 'e']
numbers = range(1, 6)

for letter, number in zip(letters, numbers):
    print(f"{letter}: {number}")
# a: 1, b: 2, c: 3, d: 4, e: 5

# Mehrere ranges kombinieren
for x, y, z in zip(range(3), range(10, 13), range(20, 23)):
    print(f"({x}, {y}, {z})")
```

---

## Häufige Fallstricke

### ⚠️ Fallstrick 1: Range ist kein Generator

```python
# Range ist NICHT einmalig
r = range(5)

# Kann mehrfach iteriert werden
print(list(r))  # [0, 1, 2, 3, 4]
print(list(r))  # [0, 1, 2, 3, 4] - funktioniert!

# Im Gegensatz zu Generator
gen = (x for x in range(5))
print(list(gen))  # [0, 1, 2, 3, 4]
print(list(gen))  # [] - leer!
```

### ⚠️ Fallstrick 2: Float-Werte nicht unterstützt

```python
# ❌ Range akzeptiert nur Integer
try:
    r = range(0.5, 10.5)
except TypeError as e:
    print(f"Fehler: {e}")  # 'float' object cannot be interpreted as an integer

# ✅ Für Float: NumPy verwenden
import numpy as np
float_range = np.arange(0.5, 10.5, 0.5)
print(float_range)

# ✅ Oder eigene Funktion
def frange(start, stop, step):
    """Float-Range"""
    current = start
    while current < stop:
        yield current
        current += step

print(list(frange(0.5, 5.5, 0.5)))
# [0.5, 1.0, 1.5, 2.0, 2.5, 3.0, 3.5, 4.0, 4.5, 5.0]
```

### ⚠️ Fallstrick 3: Modifikation nicht möglich

```python
r = range(10)

# ❌ Kann nicht modifiziert werden
try:
    r[0] = 5
except TypeError as e:
    print(f"Fehler: {e}")  # 'range' object does not support item assignment

# ✅ Zu Liste konvertieren wenn Modifikation nötig
lst = list(r)
lst[0] = 5
print(lst)  # [5, 1, 2, 3, 4, 5, 6, 7, 8, 9]
```

---

## Best Practices

### ✅ DO - Wann Range verwenden

```python
# 1. for-Loops mit Zähler
for i in range(10):
    process(i)

# 2. Index-basierte Iteration (wenn wirklich nötig)
for i in range(len(items)):
    print(f"{i}: {items[i]}")

# 3. Numerische Sequenzen
squares = [x ** 2 for x in range(10)]

# 4. Mit enumerate/zip für parallele Iteration
for i, (x, y) in enumerate(zip(range(5), range(10, 15))):
    print(f"{i}: ({x}, {y})")

# 5. Große Sequenzen wo Speicher wichtig ist
for i in range(10000000):
    if condition(i):
        break  # Speichereffizient!
```

### ❌ DON'T - Wann Listen besser sind

```python
# 1. Wenn Liste modifiziert werden muss
# ❌ Range kann nicht modifiziert werden
# ✅ Nutze Liste

# 2. Wenn mehrfache Zugriffe mit verschiedenen Indizes
# ❌ Ineffizient mit range
r = range(1000)
values = [r[i] for i in [5, 10, 100, 500]]

# ✅ Liste ist besser
lst = list(range(1000))
values = [lst[i] for i in [5, 10, 100, 500]]

# 3. Wenn enumerate() möglich ist
# ❌ Nicht pythonisch
for i in range(len(items)):
    print(items[i])

# ✅ Pythonisch
for item in items:
    print(item)

# ✅ Mit Index
for i, item in enumerate(items):
    print(f"{i}: {item}")
```

---

## Performance-Tipps

### Wann range() optimal ist

```python
import time

n = 100000000

# Test 1: Einfache Iteration
start = time.perf_counter()
for _ in range(n):
    pass
time_range = time.perf_counter() - start

# Sehr schnell da keine Liste erstellt wird
print(f"Range iteration: {time_range:.4f}s")

# Test 2: Mit Bedingung (Early Exit)
def find_first_divisible(n, divisor):
    for i in range(n):
        if i % divisor == 0 and i > 100:
            return i
    return None

start = time.perf_counter()
result = find_first_divisible(n, 13)
time_find = time.perf_counter() - start

print(f"Early exit: {time_find:.6f}s für Ergebnis {result}")
# Extrem schnell da sofort stoppt
```

---

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [range()](https://docs.python.org/3/library/stdtypes.html#range) - Built-in Function
- [Ranges](https://docs.python.org/3/library/stdtypes.html#ranges) - Range Type
- [Sequence Types](https://docs.python.org/3/library/stdtypes.html#sequence-types-list-tuple-range) - Alle Sequenz-Typen

---

## Verwandte Themen

- [[02_Sequenzen/04_Comprehensions/02_Generator Expressions|Generator Expressions]] - Ähnliche Lazy Evaluation
- [[02_Sequenzen/01_Listen/01_List Basics|List Basics]] - Alternative wenn Modifikation nötig
- [[02_Sequenzen/04_Comprehensions/01_List Comprehensions|List Comprehensions]] - Range in Comprehensions
- [[06_Praxis/02_Performance/03_Optimization Strategien|Optimization Strategien]] - Speicher-Optimierung

---

← [[02_Sequenzen/04_Comprehensions/05_Übungen Comprehensions|Übungen Comprehensions]] | [[INDEX|📑 Index]] | [[02_Sequenzen/05_Range/02_Übungen Range|Übungen Range]] →