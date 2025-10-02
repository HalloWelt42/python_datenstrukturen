---
tags: [python, datenstrukturen, generators, comprehensions, lazy-evaluation]
created: 2025-10-01
---

← [[02_Sequenzen/04_Comprehensions/01_List Comprehensions|List Comprehensions]] | [[INDEX|📑 Index]] | [[02_Sequenzen/04_Comprehensions/03_Verschachtelte Comprehensions|Verschachtelte Comprehensions]] →

# Generator Expressions - Lazy Evaluation

> [!tip] Lernziel
> Nach dieser Seite verstehst du Generator Expressions und ihre Vorteile gegenüber List Comprehensions. Du weißt, wann Lazy Evaluation sinnvoll ist und wie Generatoren für speichereffiziente Verarbeitung großer Datenmengen eingesetzt werden.

## Was sind Generator Expressions?

**Generator Expressions** sind wie List Comprehensions, erzeugen aber **Generatoren** statt Listen. Sie verwenden **Lazy Evaluation** - Werte werden erst bei Bedarf berechnet.

**Hauptmerkmale:**
- ✅ **Speichereffizient** - Nur ein Element zur Zeit im Speicher
- ✅ **Lazy** - Berechnung on-demand
- ✅ **Iterierbar** - Können nur einmal durchlaufen werden
- ✅ **Schnell** - Kein Overhead durch Listen-Erstellung
- ✅ **Pipeline-fähig** - Können verkettet werden

```python
# List Comprehension - alle Werte sofort im Speicher
list_comp = [x ** 2 for x in range(1000000)]
print(type(list_comp))  # <class 'list'>

# Generator Expression - berechnet on-demand
gen_exp = (x ** 2 for x in range(1000000))
print(type(gen_exp))  # <class 'generator'>

# Vergleich Speicherverbrauch
import sys
print(f"Liste: {sys.getsizeof(list_comp) / 1024 / 1024:.2f} MB")
print(f"Generator: {sys.getsizeof(gen_exp)} Bytes")
# Liste: ~8 MB, Generator: ~200 Bytes!
```

---

## Syntax

### Grundform

```python
# List Comprehension mit []
list_comp = [expression for item in iterable]

# Generator Expression mit ()
gen_exp = (expression for item in iterable)
```

**Einziger Unterschied:** Eckige vs runde Klammern!

```python
# List Comprehension
squares_list = [x ** 2 for x in range(10)]
print(squares_list)  # [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]

# Generator Expression
squares_gen = (x ** 2 for x in range(10))
print(squares_gen)  # <generator object <genexpr> at 0x...>

# Generator muss iteriert werden
print(list(squares_gen))  # [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
```

### Iteration über Generator

```python
# Generator erstellen
gen = (x * 2 for x in range(5))

# Mit for-Loop
for value in gen:
    print(value, end=" ")  # 0 2 4 6 8
print()

# Generator ist jetzt erschöpft!
for value in gen:
    print(value)  # Keine Ausgabe - Generator leer!

# Neuen Generator erstellen für erneute Iteration
gen = (x * 2 for x in range(5))
print(list(gen))  # [0, 2, 4, 6, 8]
```

---

## Generator vs List Comprehension

### Speicherverbrauch

```python
import sys

# Große Datenmenge
n = 1000000

# List Comprehension - alles im Speicher
list_comp = [x ** 2 for x in range(n)]
list_size = sys.getsizeof(list_comp)

# Generator Expression - nur Generator-Objekt
gen_exp = (x ** 2 for x in range(n))
gen_size = sys.getsizeof(gen_exp)

print(f"Liste: {list_size:,} Bytes ({list_size / 1024 / 1024:.2f} MB)")
print(f"Generator: {gen_size:,} Bytes")
print(f"Faktor: {list_size / gen_size:.0f}x kleiner")

# Typische Ausgabe:
# Liste: 8,448,728 Bytes (8.06 MB)
# Generator: 200 Bytes
# Faktor: 42,244x kleiner
```

### Performance bei einmaliger Iteration

```python
import time

n = 10000000

# List Comprehension
start = time.perf_counter()
result = sum([x for x in range(n)])
time_list = time.perf_counter() - start

# Generator Expression
start = time.perf_counter()
result = sum(x for x in range(n))  # sum() akzeptiert Generator direkt!
time_gen = time.perf_counter() - start

print(f"Liste:     {time_list:.4f}s")
print(f"Generator: {time_gen:.4f}s")
print(f"Speedup:   {time_list/time_gen:.2f}x")

# Generator ist oft 10-30% schneller bei einmaliger Iteration!
```

### Wann was verwenden?

```python
# ✅ List Comprehension verwenden wenn:
# - Mehrfache Iteration benötigt
# - Länge benötigt (len())
# - Index-Zugriff benötigt
# - Slicing benötigt
# - Liste modifiziert werden muss

numbers = [x ** 2 for x in range(10)]
print(len(numbers))    # OK
print(numbers[5])      # OK
print(numbers[2:5])    # OK

# ✅ Generator Expression verwenden wenn:
# - Nur einmalige Iteration
# - Große Datenmengen
# - Pipeline-Verarbeitung
# - Speichereffizienz wichtig

total = sum(x ** 2 for x in range(10000000))  # Effizient!
```

---

## Lazy Evaluation verstehen

### On-Demand Berechnung

```python
def expensive_operation(x):
    """Simuliert teure Berechnung"""
    print(f"Berechne {x}...")
    return x ** 2

# Mit List Comprehension - alle sofort berechnet
print("List Comprehension:")
squares_list = [expensive_operation(x) for x in range(5)]
print("Liste erstellt\n")
# Output:
# Berechne 0...
# Berechne 1...
# Berechne 2...
# Berechne 3...
# Berechne 4...
# Liste erstellt

# Mit Generator Expression - nichts berechnet!
print("Generator Expression:")
squares_gen = (expensive_operation(x) for x in range(5))
print("Generator erstellt (noch keine Berechnung!)\n")
# Output:
# Generator erstellt (noch keine Berechnung!)

# Erst bei Iteration wird berechnet
print("Erste Iteration:")
print(next(squares_gen))  # Berechne 0... → 0
print("\nZweite Iteration:")
print(next(squares_gen))  # Berechne 1... → 1
```

### Early Exit Vorteil

```python
# Suche ersten Treffer in großer Datenmenge
data = range(10000000)

# List Comprehension - verarbeitet ALLE Elemente
matching_list = [x for x in data if x > 5000 and x % 7 == 0]
first = matching_list[0]  # Alle Elemente berechnet, nur erstes verwendet!

# Generator Expression - stoppt beim ersten Treffer
matching_gen = (x for x in data if x > 5000 and x % 7 == 0)
first = next(matching_gen)  # Nur bis zum ersten Treffer berechnet!

# Bei großen Datenmengen kann das MASSIV schneller sein
```

---

## Praktische Anwendungen

### Anwendungsfall 1: Große Dateien verarbeiten

```python
# ❌ Schlecht: Lädt gesamte Datei in Speicher
def process_file_bad(filename):
    lines = [line.strip() for line in open(filename)]
    long_lines = [line for line in lines if len(line) > 80]
    return long_lines

# ✅ Gut: Verarbeitet Zeile für Zeile
def process_file_good(filename):
    with open(filename) as f:
        long_lines = (line.strip() for line in f if len(line.strip()) > 80)
        return list(long_lines)  # Nur wenn Liste wirklich benötigt

# ✅ Besser: Generator zurückgeben
def process_file_best(filename):
    """Gibt Generator zurück - Caller entscheidet über Materialisierung"""
    with open(filename) as f:
        for line in f:
            line = line.strip()
            if len(line) > 80:
                yield line  # Generator function

# Verwendung
for line in process_file_best('large_file.txt'):
    print(line)  # Verarbeitet eine Zeile zur Zeit
```

### Anwendungsfall 2: Pipeline-Verarbeitung

```python
# Daten-Pipeline mit Generatoren
def read_numbers(filename):
    """Generator: Liest Zahlen aus Datei"""
    with open(filename) as f:
        for line in f:
            yield int(line.strip())

def filter_positive(numbers):
    """Generator: Filtert positive Zahlen"""
    return (n for n in numbers if n > 0)

def square(numbers):
    """Generator: Quadriert Zahlen"""
    return (n ** 2 for n in numbers)

def take(n, iterable):
    """Generator: Nimmt erste n Elemente"""
    for i, item in enumerate(iterable):
        if i >= n:
            break
        yield item

# Pipeline aufbauen (noch nichts berechnet!)
numbers = read_numbers('numbers.txt')
positive = filter_positive(numbers)
squared = square(positive)
first_10 = take(10, squared)

# Erst hier wird berechnet - nur 10 Elemente!
result = list(first_10)
```

### Anwendungsfall 3: Unendliche Sequenzen

```python
# Generator für unendliche Sequenz
def infinite_counter(start=0):
    """Unendlicher Zähler"""
    while True:
        yield start
        start += 1

# Fibonacci-Generator (unendlich)
def fibonacci():
    """Unendliche Fibonacci-Sequenz"""
    a, b = 0, 1
    while True:
        yield a
        a, b = b, a + b

# Generator Expression für unendliche Sequenz
all_squares = (x ** 2 for x in infinite_counter())

# Erste 10 Quadratzahlen
import itertools
first_10_squares = list(itertools.islice(all_squares, 10))
print(first_10_squares)  # [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]

# Erste 10 Fibonacci-Zahlen
first_10_fib = list(itertools.islice(fibonacci(), 10))
print(first_10_fib)  # [0, 1, 1, 2, 3, 5, 8, 13, 21, 34]
```

### Anwendungsfall 4: Speichereffiziente Aggregation

```python
# Statistiken über große Datenmenge
def compute_stats(data):
    """Berechnet Statistiken speichereffizient"""
    # Generator für Quadrate
    squares = (x ** 2 for x in data)
    
    # Summen berechnen (iteriert nur einmal)
    total = sum(data)
    total_squares = sum(x ** 2 for x in data)  # Neuer Generator!
    count = sum(1 for _ in data)  # Neuer Generator!
    
    mean = total / count
    variance = (total_squares / count) - (mean ** 2)
    
    return {
        'mean': mean,
        'variance': variance,
        'std': variance ** 0.5
    }

# Funktioniert mit beliebig großen Datenmengen
data = range(10000000)
stats = compute_stats(data)
print(stats)
```

### Anwendungsfall 5: Lazy Data Loading

```python
class DataLoader:
    """Lädt Daten lazy aus verschiedenen Quellen"""
    
    def __init__(self, filenames):
        self.filenames = filenames
    
    def load_all(self):
        """Generator: Lädt alle Dateien nacheinander"""
        for filename in self.filenames:
            yield from self._load_file(filename)
    
    def _load_file(self, filename):
        """Generator: Lädt einzelne Datei"""
        with open(filename) as f:
            for line in f:
                yield line.strip()
    
    def filter_and_transform(self, predicate, transform):
        """Generator: Filtert und transformiert"""
        data = self.load_all()
        filtered = (item for item in data if predicate(item))
        transformed = (transform(item) for item in filtered)
        return transformed

# Verwendung
loader = DataLoader(['file1.txt', 'file2.txt', 'file3.txt'])

# Pipeline aufbauen (noch keine Datei geöffnet!)
pipeline = loader.filter_and_transform(
    predicate=lambda x: len(x) > 10,
    transform=str.upper
)

# Verarbeite erste 100 Einträge (lädt nur was nötig)
for i, item in enumerate(pipeline):
    if i >= 100:
        break
    process(item)
```

---

## Generator-Funktionen vs Expressions

### Wann Generator Function?

```python
# Generator Expression - für einfache Fälle
squares = (x ** 2 for x in range(10))

# Generator Function - für komplexere Logik
def complex_generator(n):
    """Generator mit komplexer Logik"""
    for i in range(n):
        if i % 2 == 0:
            yield i ** 2
        else:
            yield i * 3
        
        # Zusätzliche Logik möglich
        if i > 5:
            print(f"Warnung: i = {i}")

# Generator Function mit State
def stateful_generator():
    """Generator mit internem Zustand"""
    count = 0
    while True:
        count += 1
        yield count
        if count >= 10:
            print("Erreichte 10!")
            count = 0  # Reset

# Generator Expression ist limitierter
# ❌ Kann nicht: print statements, komplexes control flow, state
# ✅ Kann: einfache Expression, Filter
```

---

## Verkettung von Generatoren

### Generator Chains

```python
# Mehrere Generator-Stufen
numbers = range(1000)

# Stufe 1: Nur gerade
evens = (x for x in numbers if x % 2 == 0)

# Stufe 2: Quadriere
squared = (x ** 2 for x in evens)

# Stufe 3: Nur Zahlen > 1000
large = (x for x in squared if x > 1000)

# Stufe 4: Konvertiere zu String
strings = (str(x) for x in large)

# Erst hier wird berechnet!
result = list(strings)
print(len(result))  # Wie viele erfüllen alle Bedingungen?

# Alles in einer Zeile (aber unleserlich)
result = list(str(x) for x in (x ** 2 for x in (x for x in range(1000) if x % 2 == 0)) if x > 1000)
```

### itertools für Generator-Pipelines

```python
import itertools

# Unendliche Sequenz
naturals = itertools.count(1)  # 1, 2, 3, 4, ...

# Filter
evens = (x for x in naturals if x % 2 == 0)

# Erste 10
first_10 = itertools.islice(evens, 10)
print(list(first_10))  # [2, 4, 6, 8, 10, 12, 14, 16, 18, 20]

# Kombinationen
data1 = range(3)
data2 = range(3, 6)
combined = itertools.chain(data1, data2)
squared = (x ** 2 for x in combined)
print(list(squared))  # [0, 1, 4, 9, 16, 25]

# Gruppen
data = [1, 1, 2, 2, 2, 3, 3]
groups = itertools.groupby(data)
counts = ((key, sum(1 for _ in group)) for key, group in groups)
print(list(counts))  # [(1, 2), (2, 3), (3, 2)]
```

---

## Häufige Fallstricke

### ⚠️ Fallstrick 1: Generator ist einmalig

```python
# Generator erstellen
gen = (x ** 2 for x in range(5))

# Erste Verwendung
print(list(gen))  # [0, 1, 4, 9, 16]

# Zweite Verwendung - leer!
print(list(gen))  # [] - Generator erschöpft!

# ✅ Lösung 1: Neuen Generator erstellen
def make_generator():
    return (x ** 2 for x in range(5))

gen1 = make_generator()
gen2 = make_generator()

# ✅ Lösung 2: Liste wenn mehrfache Iteration nötig
data = list(x ** 2 for x in range(5))
print(list(data))  # [0, 1, 4, 9, 16]
print(list(data))  # [0, 1, 4, 9, 16]
```

### ⚠️ Fallstrick 2: Generator kann nicht indiziert werden

```python
gen = (x ** 2 for x in range(10))

# ❌ Kein Index-Zugriff
try:
    print(gen[5])  # TypeError
except TypeError as e:
    print(f"Fehler: {e}")

# ❌ Keine Länge
try:
    print(len(gen))  # TypeError
except TypeError as e:
    print(f"Fehler: {e}")

# ✅ Zu Liste konvertieren wenn nötig
gen_list = list(gen)
print(gen_list[5])  # 25
print(len(gen_list))  # 10
```

### ⚠️ Fallstrick 3: Variable Binding in Generator

```python
# ❌ Spätes Binding - alle verwenden finales i
generators = []
for i in range(5):
    generators.append((x * i for x in range(3)))

# Alle Generatoren verwenden i = 4!
for gen in generators:
    print(list(gen))
# [0, 4, 8]
# [0, 4, 8]
# [0, 4, 8]
# [0, 4, 8]
# [0, 4, 8]

# ✅ Lösung: Default-Argument verwenden
generators = []
for i in range(5):
    generators.append((lambda i=i: (x * i for x in range(3)))())

for gen in generators:
    print(list(gen))
# [0, 0, 0]
# [0, 1, 2]
# [0, 2, 4]
# [0, 3, 6]
# [0, 4, 8]
```

### ⚠️ Fallstrick 4: Generator mit Nebenwirkungen

```python
# ❌ Nebenwirkungen werden verzögert ausgeführt
def side_effect_gen():
    for i in range(5):
        print(f"Verarbeite {i}")  # Nebenwirkung!
        yield i ** 2

gen = side_effect_gen()
print("Generator erstellt")  # Keine Ausgabe von "Verarbeite"!

# Erst bei Iteration
print(next(gen))  # Jetzt: "Verarbeite 0" → 0
print(next(gen))  # Jetzt: "Verarbeite 1" → 1

# ✅ Für Nebenwirkungen: reguläre Funktion oder for-Loop
def process_with_side_effects():
    for i in range(5):
        print(f"Verarbeite {i}")
        # Weitere Nebenwirkungen...
```

---

## Best Practices

### ✅ DO - Wann Generator Expressions

```python
# 1. Einmalige Iteration über große Datenmengen
total = sum(x ** 2 for x in range(10000000))

# 2. Pipeline-Verarbeitung
data = read_data()
filtered = (x for x in data if condition(x))
transformed = (transform(x) for x in filtered)
result = process(transformed)

# 3. Mit Built-in-Funktionen die Iterables akzeptieren
max_value = max(x for x in data if x > 0)
any_match = any(x > 100 for x in data)
all_valid = all(validate(x) for x in data)

# 4. Early exit scenarios
first_match = next((x for x in large_data if matches(x)), None)

# 5. Speichereffiziente Aggregation
stats = {
    'sum': sum(data),
    'count': sum(1 for _ in data),  # Neuer Generator!
    'positive': sum(1 for x in data if x > 0)
}
```

### ❌ DON'T - Wann List Comprehension besser

```python
# 1. Mehrfache Iteration benötigt
# ❌ Generator wird leer
gen = (x for x in data)
first_pass = list(gen)
second_pass = list(gen)  # Leer!

# ✅ Liste verwenden
lst = [x for x in data]
first_pass = lst
second_pass = lst  # Funktioniert!

# 2. Index-Zugriff oder Slicing benötigt
# ❌ Nicht möglich mit Generator
# ✅ Liste verwenden
lst = [x for x in data]
middle = lst[len(lst)//2]
subset = lst[10:20]

# 3. Länge benötigt ohne Iteration
# ❌
gen = (x for x in data)
# len(gen)  # Fehler!

# ✅
lst = [x for x in data]
length = len(lst)

# 4. Liste wird modifiziert
# ❌ Generator ist read-only
# ✅ Liste kann modifiziert werden
lst = [x for x in data]
lst[5] = new_value
lst.append(another_value)
```

---

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [Generator Expressions](https://docs.python.org/3/reference/expressions.html#generator-expressions) - Offizielle Referenz
- [Generators](https://docs.python.org/3/howto/functional.html#generators) - Functional Programming Guide
- [itertools](https://docs.python.org/3/library/itertools.html) - Tools für Iteratoren
- [PEP 289](https://www.python.org/dev/peps/pep-0289/) - Generator Expressions

---

## Verwandte Themen

- [[02_Sequenzen/04_Comprehensions/01_List Comprehensions|List Comprehensions]] - Listen-Variante
- [[02_Sequenzen/04_Comprehensions/04_Performance Vergleich|Performance Vergleich]] - Detaillierte Benchmarks
- [[03_Mappings_und_Sets/01_Dictionaries/03_Dict Comprehensions|Dict Comprehensions]] - Dictionary-Generatoren
- [[06_Praxis/02_Performance/03_Optimization Strategien|Optimization Strategien]] - Speicher-Optimierung

---

← [[02_Sequenzen/04_Comprehensions/01_List Comprehensions|List Comprehensions]] | [[INDEX|📑 Index]] | [[02_Sequenzen/04_Comprehensions/03_Verschachtelte Comprehensions|Verschachtelte Comprehensions]] →