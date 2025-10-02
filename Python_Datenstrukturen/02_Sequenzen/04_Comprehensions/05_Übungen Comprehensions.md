---
tags: [python, datenstrukturen, comprehensions, übungen]
created: 2025-10-01
---

← [[02_Sequenzen/04_Comprehensions/04_Performance Vergleich|Performance Vergleich]] | [[INDEX|📑 Index]] | [[02_Sequenzen/05_Range/01_Range Objects|Range Objects]] →

# Übungen: Comprehensions

> [!tip] Lernziel
> Diese Übungen festigen dein Verständnis von List Comprehensions, Generator Expressions und verschachtelten Comprehensions. Sie decken alle Aspekte ab: einfache Transformationen, Filter, verschachtelte Strukturen und Performance-Optimierung.

## 📝 Übungsaufgaben

### Übung 1: List Comprehensions Basics (Leicht)

Implementiere verschiedene Transformationen mit List Comprehensions.

**Anforderungen:**
- `get_squares(n)` - Quadratzahlen von 0 bis n
- `get_evens(n)` - Gerade Zahlen von 0 bis n
- `string_lengths(words)` - Längen aller Wörter
- `uppercase_long_words(words, min_length)` - Lange Wörter in Großbuchstaben

**Beispiel:**
```python
def get_squares(n):
    # Dein Code hier
    pass

def get_evens(n):
    # Dein Code hier
    pass

def string_lengths(words):
    # Dein Code hier
    pass

def uppercase_long_words(words, min_length):
    # Dein Code hier
    pass

# Test
print(get_squares(10))
print(get_evens(20))

words = ["Python", "is", "awesome", "and", "powerful"]
print(string_lengths(words))
print(uppercase_long_words(words, 5))
```

**Erwarteter Output:**
```
[0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
[0, 2, 4, 6, 8, 10, 12, 14, 16, 18]
[6, 2, 7, 3, 8]
['PYTHON', 'AWESOME', 'POWERFUL']
```

<details>
<summary>💡 Hinweis</summary>

- Squares: `[x ** 2 for x in range(n)]`
- Evens: `[x for x in range(n) if x % 2 == 0]`
- Lengths: `[len(word) for word in words]`
- Uppercase: `[word.upper() for word in words if len(word) >= min_length]`
</details>

---

### Übung 2: Generator Expressions - Memory Efficiency (Leicht-Mittel)

Vergleiche List Comprehensions mit Generator Expressions für große Datenmengen.

**Anforderungen:**
- `sum_squares_list(n)` - Mit List Comprehension
- `sum_squares_gen(n)` - Mit Generator Expression
- `measure_memory(func, n)` - Misst Speicherverbrauch
- Vergleiche Speicher und Performance

**Beispiel:**
```python
import sys
import time

def sum_squares_list(n):
    # Dein Code hier
    pass

def sum_squares_gen(n):
    # Dein Code hier
    pass

def measure_time(func, n):
    start = time.perf_counter()
    result = func(n)
    elapsed = time.perf_counter() - start
    return result, elapsed

# Test
n = 1000000

result1, time1 = measure_time(sum_squares_list, n)
result2, time2 = measure_time(sum_squares_gen, n)

print(f"Ergebnis (sollte gleich sein): {result1 == result2}")
print(f"\nList:      {time1:.4f}s")
print(f"Generator: {time2:.4f}s")
print(f"Speedup:   {time1/time2:.2f}x")

# Speicher-Test (für kleineres n)
list_comp = [x ** 2 for x in range(10000)]
gen_exp = (x ** 2 for x in range(10000))

print(f"\nSpeicher:")
print(f"Liste:     {sys.getsizeof(list_comp):,} Bytes")
print(f"Generator: {sys.getsizeof(gen_exp):,} Bytes")
```

**Erwarteter Output:**
```
Ergebnis (sollte gleich sein): True

List:      0.1234s
Generator: 0.0987s
Speedup:   1.25x

Speicher:
Liste:     87,624 Bytes
Generator: 200 Bytes
```

<details>
<summary>💡 Hinweis</summary>

- List: `sum([x ** 2 for x in range(n)])`
- Generator: `sum(x ** 2 for x in range(n))` - Keine eckigen Klammern!
- Generator ist speichereffizienter und oft schneller bei einmaliger Iteration
</details>

---

### Übung 3: Nested Comprehensions - Matrix Operations (Mittel)

Implementiere Matrix-Operationen mit verschachtelten Comprehensions.

**Anforderungen:**
- `create_matrix(rows, cols, value=0)` - Erstellt Matrix
- `transpose(matrix)` - Transponiert Matrix
- `flatten(matrix)` - Flacht Matrix ab
- `matrix_multiply(A, B)` - Multipliziert 2x2 Matrizen

**Beispiel:**
```python
def create_matrix(rows, cols, value=0):
    # Dein Code hier
    pass

def transpose(matrix):
    # Dein Code hier
    pass

def flatten(matrix):
    # Dein Code hier
    pass

def matrix_multiply(A, B):
    """Multipliziert zwei 2x2 Matrizen"""
    # Dein Code hier
    pass

# Test
matrix = create_matrix(3, 4, 0)
print("Matrix:")
for row in matrix:
    print(row)

matrix = [[1, 2, 3], [4, 5, 6]]
print(f"\nTransponiert:")
trans = transpose(matrix)
for row in trans:
    print(row)

print(f"\nFlattened: {flatten(matrix)}")

A = [[1, 2], [3, 4]]
B = [[5, 6], [7, 8]]
C = matrix_multiply(A, B)
print(f"\nA × B:")
for row in C:
    print(row)
```

**Erwarteter Output:**
```
Matrix:
[0, 0, 0, 0]
[0, 0, 0, 0]
[0, 0, 0, 0]

Transponiert:
[1, 4]
[2, 5]
[3, 6]

Flattened: [1, 2, 3, 4, 5, 6]

A × B:
[19, 22]
[43, 50]
```

<details>
<summary>💡 Hinweis</summary>

- Create: `[[value for _ in range(cols)] for _ in range(rows)]`
- Transpose: `[list(col) for col in zip(*matrix)]`
- Flatten: `[item for row in matrix for item in row]`
- Multiply: `[[sum(A[i][k] * B[k][j] for k in range(2)) for j in range(2)] for i in range(2)]`
</details>

---

### Übung 4: Complex Filtering - Data Processing (Mittel)

Verarbeite komplexe Datenstrukturen mit Comprehensions.

**Anforderungen:**
- Filtere und transformiere Liste von Dictionaries
- Mehrere Bedingungen kombinieren
- Gruppiere Ergebnisse

**Beispiel:**
```python
# Daten
products = [
    {"name": "Laptop", "price": 999, "category": "Electronics", "stock": 5},
    {"name": "Mouse", "price": 25, "category": "Electronics", "stock": 50},
    {"name": "Desk", "price": 299, "category": "Furniture", "stock": 10},
    {"name": "Chair", "price": 199, "category": "Furniture", "stock": 15},
    {"name": "Monitor", "price": 399, "category": "Electronics", "stock": 0}
]

def get_available_products(products):
    """Nur Produkte mit Lagerbestand"""
    # Dein Code hier
    pass

def get_expensive_electronics(products, min_price=300):
    """Teure Elektronik-Produkte"""
    # Dein Code hier
    pass

def calculate_inventory_value(products):
    """Gesamtwert pro Kategorie"""
    # Dein Code hier
    pass

def get_product_names_by_category(products):
    """Dictionary: Kategorie -> Liste von Namen"""
    # Dein Code hier
    pass

# Test
available = get_available_products(products)
print(f"Verfügbare Produkte: {[p['name'] for p in available]}")

expensive = get_expensive_electronics(products)
print(f"Teure Elektronik: {[p['name'] for p in expensive]}")

inventory = calculate_inventory_value(products)
print(f"Inventarwert: {inventory}")

by_category = get_product_names_by_category(products)
print(f"Nach Kategorie: {by_category}")
```

**Erwarteter Output:**
```
Verfügbare Produkte: ['Laptop', 'Mouse', 'Desk', 'Chair']
Teure Elektronik: ['Laptop', 'Monitor']
Inventarwert: {'Electronics': 10020, 'Furniture': 5975}
Nach Kategorie: {'Electronics': ['Laptop', 'Mouse', 'Monitor'], 'Furniture': ['Desk', 'Chair']}
```

<details>
<summary>💡 Hinweis</summary>

- Available: `[p for p in products if p['stock'] > 0]`
- Expensive: `[p for p in products if p['category'] == 'Electronics' and p['price'] >= min_price]`
- Inventory: Verwende `set()` für Kategorien, dann Dict Comprehension
- By category: Dict Comprehension mit List Comprehension als Value
</details>

---

### Übung 5: Cartesian Product - Combinations (Mittel-Schwer)

Erzeuge alle Kombinationen mit verschachtelten Comprehensions.

**Anforderungen:**
- `cartesian_product(list1, list2)` - Alle Paare
- `pythagorean_triples(n)` - Pythagoräische Tripel bis n
- `generate_coordinates(x_range, y_range)` - Grid-Koordinaten
- `valid_combinations(options)` - Kombinationen mit Bedingungen

**Beispiel:**
```python
def cartesian_product(list1, list2):
    # Dein Code hier
    pass

def pythagorean_triples(n):
    """Findet alle (a, b, c) mit a² + b² = c²"""
    # Dein Code hier
    pass

def generate_coordinates(x_range, y_range):
    """Alle (x, y) Koordinaten im Bereich"""
    # Dein Code hier
    pass

def valid_combinations(colors, sizes, exclude_combinations):
    """Alle Kombinationen außer ausgeschlossene"""
    # exclude_combinations ist Liste von (color, size) Tupeln
    # Dein Code hier
    pass

# Test
print("Kartesisches Produkt:")
print(cartesian_product([1, 2], ['a', 'b', 'c']))

print("\nPythagoräische Tripel bis 20:")
print(pythagorean_triples(20))

print("\nKoordinaten:")
coords = generate_coordinates(range(3), range(3))
print(coords)

print("\nGültige Kombinationen:")
colors = ["red", "blue"]
sizes = ["S", "M", "L"]
exclude = [("red", "S"), ("blue", "L")]
valid = valid_combinations(colors, sizes, exclude)
print(valid)
```

**Erwarteter Output:**
```
Kartesisches Produkt:
[(1, 'a'), (1, 'b'), (1, 'c'), (2, 'a'), (2, 'b'), (2, 'c')]

Pythagoräische Tripel bis 20:
[(3, 4, 5), (5, 12, 13), (6, 8, 10), (8, 15, 17), (9, 12, 15)]

Koordinaten:
[(0, 0), (0, 1), (0, 2), (1, 0), (1, 1), (1, 2), (2, 0), (2, 1), (2, 2)]

Gültige Kombinationen:
[('red', 'M'), ('red', 'L'), ('blue', 'S'), ('blue', 'M')]
```

<details>
<summary>💡 Hinweis</summary>

- Cartesian: `[(x, y) for x in list1 for y in list2]`
- Triples: Drei verschachtelte Loops mit Bedingung `a**2 + b**2 == c**2`
- Coordinates: `[(x, y) for x in x_range for y in y_range]`
- Valid: Filter mit `not in exclude_combinations`
</details>

---

### Übung 6: String Processing - Text Analysis (Mittel-Schwer)

Analysiere Text mit Comprehensions.

**Anforderungen:**
- `extract_words(text)` - Extrahiert Wörter (ohne Satzzeichen)
- `word_lengths(text)` - Dictionary {Wort: Länge}
- `find_palindromes(words)` - Findet Palindrome
- `create_acronym(phrase)` - Erstellt Akronym aus Anfangsbuchstaben

**Beispiel:**
```python
import string

def extract_words(text):
    """Extrahiert Wörter, lowercase, ohne Satzzeichen"""
    # Dein Code hier
    pass

def word_lengths(text):
    """Dictionary mit Wort -> Länge"""
    # Dein Code hier
    pass

def find_palindromes(words):
    """Findet alle Palindrome"""
    # Dein Code hier
    pass

def create_acronym(phrase):
    """Erstellt Akronym aus Anfangsbuchstaben"""
    # Dein Code hier
    pass

# Test
text = "Python is awesome! List comprehensions are powerful, aren't they?"

words = extract_words(text)
print(f"Wörter: {words}")

lengths = word_lengths(text)
print(f"Längen: {lengths}")

test_words = ["racecar", "hello", "level", "world", "noon"]
palindromes = find_palindromes(test_words)
print(f"Palindrome: {palindromes}")

phrase = "List Comprehensions Are Great"
acronym = create_acronym(phrase)
print(f"Akronym: {acronym}")
```

**Erwarteter Output:**
```
Wörter: ['python', 'is', 'awesome', 'list', 'comprehensions', 'are', 'powerful', 'arent', 'they']
Längen: {'python': 6, 'is': 2, 'awesome': 7, 'list': 4, ...}
Palindrome: ['racecar', 'level', 'noon']
Akronym: LCAG
```

<details>
<summary>💡 Hinweis</summary>

- Extract: Split, dann `word.strip(string.punctuation).lower()` für jedes Wort
- Lengths: Dict Comprehension `{word: len(word) for word in words}`
- Palindromes: `[w for w in words if w == w[::-1]]`
- Acronym: `''.join([word[0].upper() for word in phrase.split()])`
</details>

---

### Übung 7: Performance Challenge - Optimize (Schwer)

Optimiere Code für bessere Performance.

**Anforderungen:**
- Gegeben: Langsame Implementation
- Aufgabe: Optimiere mit Comprehensions/Generators
- Messe Performance-Verbesserung

**Beispiel:**
```python
import time

# ❌ LANGSAM - Optimiere diese Funktionen!

def slow_sum_of_squares(n):
    """Summiert Quadrate von geraden Zahlen"""
    result = []
    for i in range(n):
        if i % 2 == 0:
            result.append(i ** 2)
    return sum(result)

def slow_flatten(matrix):
    """Flacht verschachtelte Liste ab"""
    result = []
    for row in matrix:
        for item in row:
            result.append(item)
    return result

def slow_filter_transform(data, threshold):
    """Filtert und transformiert"""
    filtered = []
    for item in data:
        if item > threshold:
            filtered.append(item)
    
    result = []
    for item in filtered:
        result.append(item ** 2)
    return result

# ✅ SCHNELL - Deine optimierten Versionen

def fast_sum_of_squares(n):
    # Dein Code hier
    pass

def fast_flatten(matrix):
    # Dein Code hier
    pass

def fast_filter_transform(data, threshold):
    # Dein Code hier
    pass

# Benchmark
def benchmark(slow_func, fast_func, *args):
    # Slow
    start = time.perf_counter()
    result1 = slow_func(*args)
    time_slow = time.perf_counter() - start
    
    # Fast
    start = time.perf_counter()
    result2 = fast_func(*args)
    time_fast = time.perf_counter() - start
    
    print(f"Langsam: {time_slow:.4f}s")
    print(f"Schnell: {time_fast:.4f}s")
    print(f"Speedup: {time_slow/time_fast:.2f}x")
    print(f"Korrekt: {result1 == result2}\n")

# Test
print("Sum of Squares:")
benchmark(slow_sum_of_squares, fast_sum_of_squares, 100000)

print("Flatten:")
matrix = [[i * j for j in range(100)] for i in range(100)]
benchmark(slow_flatten, fast_flatten, matrix)

print("Filter & Transform:")
data = list(range(100000))
benchmark(slow_filter_transform, fast_filter_transform, data, 50000)
```

**Erwartetes Ergebnis:**
```
Sum of Squares:
Langsam: 0.0123s
Schnell: 0.0089s
Speedup: 1.38x
Korrekt: True

Flatten:
Langsam: 0.0034s
Schnell: 0.0026s
Speedup: 1.31x
Korrekt: True

Filter & Transform:
Langsam: 0.0156s
Schnell: 0.0112s
Speedup: 1.39x
Korrekt: True
```

<details>
<summary>💡 Hinweis</summary>

- Sum: `sum(i ** 2 for i in range(n) if i % 2 == 0)` - Generator!
- Flatten: `[item for row in matrix for item in row]`
- Filter+Transform: `[item ** 2 for item in data if item > threshold]` - in einem!
</details>

---

### Übung 8: 3D Structures - Voxel Grid (Schwer)

Arbeite mit 3D-Datenstrukturen.

**Anforderungen:**
- `create_3d_grid(x, y, z, value=0)` - Erstellt 3D-Array
- `set_voxel(grid, x, y, z, value)` - Setzt Wert
- `get_slice(grid, axis, index)` - Extrahiert 2D-Slice
- `count_neighbors(grid, x, y, z)` - Zählt nicht-null Nachbarn

**Beispiel:**
```python
def create_3d_grid(x, y, z, value=0):
    # Dein Code hier
    pass

def set_voxel(grid, x, y, z, value):
    """Setzt Voxel-Wert"""
    grid[z][y][x] = value

def get_slice(grid, axis, index):
    """Extrahiert 2D-Slice entlang axis ('x', 'y', oder 'z')"""
    # Dein Code hier
    pass

def count_neighbors(grid, x, y, z):
    """Zählt nicht-null Nachbarn (6-connected)"""
    # Dein Code hier
    pass

# Test
grid = create_3d_grid(5, 5, 5, 0)
print(f"Grid Shape: {len(grid)}x{len(grid[0])}x{len(grid[0][0])}")

# Setze einige Voxel
set_voxel(grid, 2, 2, 2, 1)
set_voxel(grid, 2, 2, 3, 1)
set_voxel(grid, 3, 2, 2, 1)

# Extrahiere Slice
z_slice = get_slice(grid, 'z', 2)
print(f"\nZ-Slice bei Index 2:")
for row in z_slice:
    print(row)

# Zähle Nachbarn
neighbors = count_neighbors(grid, 2, 2, 2)
print(f"\nNachbarn von (2,2,2): {neighbors}")
```

**Erwarteter Output:**
```
Grid Shape: 5x5x5

Z-Slice bei Index 2:
[0, 0, 0, 0, 0]
[0, 0, 0, 0, 0]
[0, 0, 1, 1, 0]
[0, 0, 0, 0, 0]
[0, 0, 0, 0, 0]

Nachbarn von (2,2,2): 2
```

<details>
<summary>💡 Hinweis</summary>

- Create: `[[[value for _ in range(x)] for _ in range(y)] for _ in range(z)]`
- Get slice: Abhängig von axis verschiedene Zugriffe
- Neighbors: Prüfe 6 Positionen: (x±1, y, z), (x, y±1, z), (x, y, z±1)
</details>

---

### Übung 9: Advanced Pattern - Prime Factorization (Schwer)

Implementiere komplexe Algorithmen mit Comprehensions.

**Anforderungen:**
- `prime_factors(n)` - Primfaktorzerlegung
- `all_divisors(n)` - Alle Teiler
- `gcd_list(numbers)` - GCD einer Liste
- `generate_fractions(n)` - Alle gekürzte Brüche mit Nenner ≤ n

**Beispiel:**
```python
import math

def prime_factors(n):
    """Primfaktorzerlegung"""
    # Dein Code hier
    pass

def all_divisors(n):
    """Alle Teiler von n"""
    # Dein Code hier
    pass

def gcd_list(numbers):
    """GCD einer Liste von Zahlen"""
    # Dein Code hier
    pass

def generate_fractions(n):
    """Alle gekürzten Brüche (a, b) mit b ≤ n und 0 < a < b"""
    # Dein Code hier
    pass

# Test
print(f"Primfaktoren von 60: {prime_factors(60)}")
print(f"Teiler von 24: {all_divisors(24)}")
print(f"GCD von [12, 18, 24]: {gcd_list([12, 18, 24])}")

fractions = generate_fractions(5)
print(f"\nBrüche mit Nenner ≤ 5:")
for a, b in fractions:
    print(f"{a}/{b}", end=" ")
```

**Erwarteter Output:**
```
Primfaktoren von 60: [2, 2, 3, 5]
Teiler von 24: [1, 2, 3, 4, 6, 8, 12, 24]
GCD von [12, 18, 24]: 6

Brüche mit Nenner ≤ 5:
1/2 1/3 2/3 1/4 3/4 1/5 2/5 3/5 4/5
```

<details>
<summary>💡 Hinweis</summary>

- Prime factors: While-Loop für Faktoren, dann List Comprehension
- Divisors: `[i for i in range(1, n+1) if n % i == 0]`
- GCD: Verwende `math.gcd`, reduziere mit comprehension/loop
- Fractions: Doppelte Loop mit `math.gcd(a, b) == 1` Bedingung
</details>

---

## 🎯 Bonus-Challenge: Data Pipeline mit Generators

**Aufgabe:** Erstelle eine speichereffiziente Daten-Pipeline mit Generator Expressions.

```python
# Große Datei simulieren
def read_large_file():
    """Simuliert Datei mit Millionen Zeilen"""
    for i in range(10000000):
        yield f"line {i}: value {i * 2}"

def create_pipeline():
    """
    Erstelle Pipeline die:
    1. Zeilen liest
    2. Nur Zeilen mit geraden Werten behält
    3. Wert extrahiert und zu int konvertiert
    4. Quadriert
    5. Summiert erste 1000
    
    Alles mit Generators - maximal speichereffizient!
    """
    # Dein Code hier
    pass

# Test
result = create_pipeline()
print(f"Summe der ersten 1000: {result}")

# Erwartetes Ergebnis: Summe von (0², 2², 4², ..., 1998²)
```

---

## 📊 Lösungshinweise

> [!note] Selbsttest
> Versuche alle Übungen selbst zu lösen, bevor du die Lösungen ansiehst. Die Lösungen findest du in [[06_Praxis/04_Lösungen/Lösungen Teil 2|Lösungen Teil 2]].

**Bewertung deiner Lösungen:**
- ✅ Verwendet Comprehensions wo angemessen
- ✅ Generator Expressions für große Daten/einmalige Iteration
- ✅ Lesbar (nicht zu verschachtelt)
- ✅ Effizient (vermeidet unnötige Zwischenlisten)
- ✅ Korrekt (produziert erwartete Ergebnisse)

---

## 🔗 Weiterführende Themen

Nach diesen Übungen solltest du bereit sein für:
- [[02_Sequenzen/05_Range/01_Range Objects|Range Objects]] - Lazy Integer Sequences
- [[03_Mappings_und_Sets/01_Dictionaries/03_Dict Comprehensions|Dict Comprehensions]] - Dictionary-Variante
- [[03_Mappings_und_Sets/02_Sets/04_Set Comprehensions|Set Comprehensions]] - Set-Variante

---

## 📚 Python-Dokumentation

**Offizielle Ressourcen:**
- [List Comprehensions](https://docs.python.org/3/tutorial/datastructures.html#list-comprehensions) - Tutorial
- [Generator Expressions](https://docs.python.org/3/reference/expressions.html#generator-expressions) - Referenz
- [itertools](https://docs.python.org/3/library/itertools.html) - Iterator-Tools
- [Functional Programming](https://docs.python.org/3/howto/functional.html) - Funktionale Konzepte

---

← [[02_Sequenzen/04_Comprehensions/04_Performance Vergleich|Performance Vergleich]] | [[INDEX|📑 Index]] | [[02_Sequenzen/05_Range/01_Range Objects|Range Objects]] →