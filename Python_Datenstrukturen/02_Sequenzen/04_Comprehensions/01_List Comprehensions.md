---
tags: [python, datenstrukturen, comprehensions, list, pythonic]
created: 2025-10-01
---

← [[02_Sequenzen/03_Slicing/04_Übungen Slicing|Übungen Slicing]] | [[INDEX|📑 Index]] | [[02_Sequenzen/04_Comprehensions/02_Generator Expressions|Generator Expressions]] →

# List Comprehensions - Elegante Listen-Erstellung

> [!tip] Lernziel
> Nach dieser Seite beherrschst du List Comprehensions - eine der pythonischsten und mächtigsten Techniken. Du weißt, wann sie gegenüber Loops vorzuziehen sind und kennst alle Varianten: einfach, mit Filter, verschachtelt.

## Was sind List Comprehensions?

**List Comprehensions** sind eine deklarative, lesbare Syntax zur Erstellung von Listen aus anderen Iterables. Sie sind oft schneller und pythonischer als äquivalente `for`-Loops.

**Hauptmerkmale:**
- ✅ **Kompakt** - Eine Zeile statt mehrerer
- ✅ **Lesbar** - Deklarativ statt imperativ
- ✅ **Schnell** - Optimiert in C implementiert
- ✅ **Pythonisch** - Idiomatischer Python-Stil
- ✅ **Funktional** - Map + Filter in einem

```python
# Traditioneller Loop
squares = []
for x in range(10):
    squares.append(x ** 2)

# List Comprehension - pythonisch!
squares = [x ** 2 for x in range(10)]
print(squares)  # [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
```

---

## Grundlegende Syntax

### Einfache List Comprehension

```python
[expression for item in iterable]
```

- **expression**: Was mit jedem Element gemacht wird
- **item**: Variable für aktuelles Element
- **iterable**: Quelle der Daten

```python
# Beispiele
numbers = [x for x in range(10)]
print(numbers)  # [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

# Mit Transformation
squares = [x ** 2 for x in range(10)]
print(squares)  # [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]

# Mit Strings
words = ["hello", "world", "python"]
uppercase = [word.upper() for word in words]
print(uppercase)  # ['HELLO', 'WORLD', 'PYTHON']

# Mit Tupeln
points = [(1, 2), (3, 4), (5, 6)]
x_coords = [x for x, y in points]
print(x_coords)  # [1, 3, 5]
```

### Äquivalenter for-Loop

```python
# List Comprehension
result = [x * 2 for x in range(5)]

# Äquivalenter Loop
result = []
for x in range(5):
    result.append(x * 2)

# Beide ergeben: [0, 2, 4, 6, 8]
```

---

## Mit Bedingungen (Filter)

### If-Bedingung (Filter)

```python
[expression for item in iterable if condition]
```

```python
# Nur gerade Zahlen
evens = [x for x in range(10) if x % 2 == 0]
print(evens)  # [0, 2, 4, 6, 8]

# Nur positive Zahlen
numbers = [-2, -1, 0, 1, 2, 3]
positives = [x for x in numbers if x > 0]
print(positives)  # [1, 2, 3]

# Strings mit bestimmter Länge
words = ["a", "ab", "abc", "abcd", "abcde"]
long_words = [word for word in words if len(word) > 2]
print(long_words)  # ['abc', 'abcd', 'abcde']

# Mehrere Bedingungen mit and
numbers = range(20)
result = [x for x in numbers if x % 2 == 0 if x % 3 == 0]
print(result)  # [0, 6, 12, 18]

# Äquivalent zu:
result = [x for x in numbers if x % 2 == 0 and x % 3 == 0]
```

### If-Else in Expression

```python
[expression_if_true if condition else expression_if_false for item in iterable]
```

```python
# Quadrate für gerade, Original für ungerade
numbers = range(10)
result = [x ** 2 if x % 2 == 0 else x for x in numbers]
print(result)  # [0, 1, 4, 3, 16, 5, 36, 7, 64, 9]

# Kategorisierung
values = [15, 25, 35, 45, 55]
categories = ["Low" if x < 30 else "High" for x in values]
print(categories)  # ['Low', 'Low', 'High', 'High', 'High']

# Mit komplexerer Logik
def categorize(score):
    if score >= 90:
        return "A"
    elif score >= 80:
        return "B"
    elif score >= 70:
        return "C"
    else:
        return "F"

scores = [95, 87, 72, 65]
grades = [categorize(s) for s in scores]
print(grades)  # ['A', 'B', 'C', 'F']
```

---

## Transformationen

### Einfache Transformationen

```python
# Mathematische Operationen
numbers = [1, 2, 3, 4, 5]
doubled = [x * 2 for x in numbers]
print(doubled)  # [2, 4, 6, 8, 10]

cubed = [x ** 3 for x in numbers]
print(cubed)  # [1, 8, 27, 64, 125]

# String-Operationen
names = ["alice", "bob", "charlie"]
capitalized = [name.capitalize() for name in names]
print(capitalized)  # ['Alice', 'Bob', 'Charlie']

# String-Methoden verketten
words = ["  hello  ", "  world  "]
cleaned = [word.strip().upper() for word in words]
print(cleaned)  # ['HELLO', 'WORLD']
```

### Mit Funktionen

```python
# Eingebaute Funktionen
numbers = [-2, -1, 0, 1, 2]
absolutes = [abs(x) for x in numbers]
print(absolutes)  # [2, 1, 0, 1, 2]

strings = ["123", "456", "789"]
integers = [int(s) for s in strings]
print(integers)  # [123, 456, 789]

# Eigene Funktionen
def square_plus_one(x):
    return x ** 2 + 1

result = [square_plus_one(x) for x in range(5)]
print(result)  # [1, 2, 5, 10, 17]

# Lambda-Funktionen (inline)
double_and_increment = lambda x: x * 2 + 1
result = [double_and_increment(x) for x in range(5)]
print(result)  # [1, 3, 5, 7, 9]
```

### Komplexe Transformationen

```python
# Dictionary aus Liste erstellen
words = ["apple", "banana", "cherry"]
word_lengths = [{"word": w, "length": len(w)} for w in words]
print(word_lengths)
# [{'word': 'apple', 'length': 5}, 
#  {'word': 'banana', 'length': 6}, 
#  {'word': 'cherry', 'length': 6}]

# Tuple Unpacking mit Transformation
points = [(1, 2), (3, 4), (5, 6)]
distances = [((x ** 2 + y ** 2) ** 0.5) for x, y in points]
print([f"{d:.2f}" for d in distances])  # ['2.24', '5.00', '7.81']

# Verschachtelte Strukturen
data = [
    {"name": "Alice", "scores": [85, 90, 92]},
    {"name": "Bob", "scores": [78, 82, 88]}
]
averages = [{"name": d["name"], "avg": sum(d["scores"]) / len(d["scores"])} 
            for d in data]
print(averages)
# [{'name': 'Alice', 'avg': 89.0}, {'name': 'Bob', 'avg': 82.67}]
```

---

## Mehrere Iterables

### Mit zip()

```python
# Zwei Listen parallel verarbeiten
names = ["Alice", "Bob", "Charlie"]
scores = [85, 92, 78]

results = [f"{name}: {score}" for name, score in zip(names, scores)]
print(results)  # ['Alice: 85', 'Bob: 92', 'Charlie: 78']

# Drei Listen
first = ["Alice", "Bob"]
middle = ["M.", "J."]
last = ["Smith", "Jones"]

full_names = [f"{f} {m} {l}" for f, m, l in zip(first, middle, last)]
print(full_names)  # ['Alice M. Smith', 'Bob J. Jones']

# Mit enumerate
words = ["apple", "banana", "cherry"]
indexed = [f"{i}: {word}" for i, word in enumerate(words, 1)]
print(indexed)  # ['1: apple', '2: banana', '3: cherry']
```

### Kartesisches Produkt (Nested Loops)

```python
# Zwei for-Klauseln
colors = ["red", "green"]
sizes = ["S", "M", "L"]

products = [f"{color}-{size}" for color in colors for size in sizes]
print(products)
# ['red-S', 'red-M', 'red-L', 'green-S', 'green-M', 'green-L']

# Äquivalenter nested loop
products = []
for color in colors:
    for size in sizes:
        products.append(f"{color}-{size}")

# Koordinaten-Grid
coordinates = [(x, y) for x in range(3) for y in range(3)]
print(coordinates)
# [(0, 0), (0, 1), (0, 2), (1, 0), (1, 1), (1, 2), (2, 0), (2, 1), (2, 2)]

# Mit Filter
pairs = [(x, y) for x in range(5) for y in range(5) if x < y]
print(pairs)
# [(0, 1), (0, 2), (0, 3), (0, 4), (1, 2), (1, 3), (1, 4), (2, 3), (2, 4), (3, 4)]
```

---

## Verschachtelte List Comprehensions

### 2D-Listen (Matrizen)

```python
# Matrix erstellen
matrix = [[i * j for j in range(5)] for i in range(5)]
print("Multiplikationstabelle:")
for row in matrix:
    print(row)
# [0, 0, 0, 0, 0]
# [0, 1, 2, 3, 4]
# [0, 2, 4, 6, 8]
# [0, 3, 6, 9, 12]
# [0, 4, 8, 12, 16]

# Identitätsmatrix
n = 4
identity = [[1 if i == j else 0 for j in range(n)] for i in range(n)]
for row in identity:
    print(row)
# [1, 0, 0, 0]
# [0, 1, 0, 0]
# [0, 0, 1, 0]
# [0, 0, 0, 1]
```

### Matrix-Transformationen

```python
# Matrix transponieren
matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]

# Methode 1: Mit zip
transposed = [list(row) for row in zip(*matrix)]

# Methode 2: Mit verschachtelter Comprehension
transposed = [[matrix[i][j] for i in range(len(matrix))] 
              for j in range(len(matrix[0]))]

print(transposed)
# [[1, 4, 7], [2, 5, 8], [3, 6, 9]]

# Flatten (verschachtelte Liste auflösen)
matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
flat = [item for row in matrix for item in row]
print(flat)  # [1, 2, 3, 4, 5, 6, 7, 8, 9]

# Äquivalent zu:
flat = []
for row in matrix:
    for item in row:
        flat.append(item)
```

---

## Praktische Anwendungen

### Anwendungsfall 1: Datenbereinigung

```python
# CSV-Daten bereinigen
csv_rows = [
    "  Alice,25,Berlin  ",
    "  Bob,30,München  ",
    "  Charlie,28,Hamburg  "
]

# Bereinigen und splitten
cleaned = [row.strip().split(',') for row in csv_rows]
print(cleaned)
# [['Alice', '25', 'Berlin'], ['Bob', '30', 'München'], ['Charlie', '28', 'Hamburg']]

# Als Dictionaries
headers = ['name', 'age', 'city']
people = [{k: v.strip() for k, v in zip(headers, row.strip().split(','))} 
          for row in csv_rows]
print(people)
# [{'name': 'Alice', 'age': '25', 'city': 'Berlin'}, ...]
```

### Anwendungsfall 2: Datenfilterung

```python
# Produkt-Liste filtern und transformieren
products = [
    {"name": "Laptop", "price": 999, "in_stock": True},
    {"name": "Mouse", "price": 25, "in_stock": True},
    {"name": "Keyboard", "price": 75, "in_stock": False},
    {"name": "Monitor", "price": 299, "in_stock": True}
]

# Nur verfügbare Produkte
available = [p for p in products if p["in_stock"]]

# Nur Namen der teuren Produkte
expensive_names = [p["name"] for p in products if p["price"] > 100]
print(expensive_names)  # ['Laptop', 'Monitor']

# Preise mit Rabatt
discounted = [{"name": p["name"], "price": p["price"] * 0.9} 
              for p in products if p["in_stock"]]
print(discounted)
# [{'name': 'Laptop', 'price': 899.1}, {'name': 'Mouse', 'price': 22.5}, ...]
```

### Anwendungsfall 3: String-Verarbeitung

```python
# Wörter aus Text extrahieren
text = "Python is awesome! List comprehensions are powerful."
words = text.split()

# Wörter ohne Satzzeichen, lowercase
import string
cleaned_words = [word.strip(string.punctuation).lower() for word in words]
print(cleaned_words)
# ['python', 'is', 'awesome', 'list', 'comprehensions', 'are', 'powerful']

# Nur lange Wörter
long_words = [word for word in cleaned_words if len(word) > 5]
print(long_words)  # ['python', 'awesome', 'comprehensions', 'powerful']

# Wort-Häufigkeiten (mit Counter wäre besser, aber als Beispiel)
word_counts = [{word: cleaned_words.count(word)} for word in set(cleaned_words)]
```

### Anwendungsfall 4: Mathematische Operationen

```python
# Vektor-Operationen
v1 = [1, 2, 3]
v2 = [4, 5, 6]

# Skalarprodukt
dot_product = sum([a * b for a, b in zip(v1, v2)])
print(dot_product)  # 32 (1*4 + 2*5 + 3*6)

# Vektor-Addition
v_sum = [a + b for a, b in zip(v1, v2)]
print(v_sum)  # [5, 7, 9]

# Skalar-Multiplikation
scalar = 3
v_scaled = [scalar * x for x in v1]
print(v_scaled)  # [3, 6, 9]

# Primzahlen finden (Sieb des Eratosthenes)
n = 30
primes = [x for x in range(2, n) 
          if all(x % d != 0 for d in range(2, int(x ** 0.5) + 1))]
print(primes)  # [2, 3, 5, 7, 11, 13, 17, 19, 23, 29]
```

### Anwendungsfall 5: Datenaggregation

```python
# Gruppierte Daten aggregieren
sales = [
    {"product": "A", "region": "North", "amount": 100},
    {"product": "B", "region": "North", "amount": 150},
    {"product": "A", "region": "South", "amount": 120},
    {"product": "B", "region": "South", "amount": 180}
]

# Summe pro Region
regions = list(set(s["region"] for s in sales))
region_totals = [{"region": r, 
                  "total": sum(s["amount"] for s in sales if s["region"] == r)}
                 for r in regions]
print(region_totals)
# [{'region': 'North', 'total': 250}, {'region': 'South', 'total': 300}]

# Summe pro Produkt
products = list(set(s["product"] for s in sales))
product_totals = [{p: sum(s["amount"] for s in sales if s["product"] == p)}
                  for p in products]
print(product_totals)
# [{'A': 220}, {'B': 330}]
```

---

## List Comprehensions vs Alternativen

### vs for-Loop

```python
# for-Loop: Mehr Code, imperativ
result = []
for x in range(10):
    if x % 2 == 0:
        result.append(x ** 2)

# List Comprehension: Kompakt, deklarativ
result = [x ** 2 for x in range(10) if x % 2 == 0]

# Beide: [0, 4, 16, 36, 64]
```

### vs map() und filter()

```python
numbers = range(10)

# Mit map() und filter()
evens = filter(lambda x: x % 2 == 0, numbers)
squares = list(map(lambda x: x ** 2, evens))

# Mit List Comprehension
squares = [x ** 2 for x in numbers if x % 2 == 0]

# List Comprehension ist oft lesbarer!
```

### Performance-Vergleich

```python
import time

n = 1000000

# for-Loop
start = time.perf_counter()
result = []
for x in range(n):
    result.append(x * 2)
time_loop = time.perf_counter() - start

# List Comprehension
start = time.perf_counter()
result = [x * 2 for x in range(n)]
time_comp = time.perf_counter() - start

# map()
start = time.perf_counter()
result = list(map(lambda x: x * 2, range(n)))
time_map = time.perf_counter() - start

print(f"for-Loop:      {time_loop:.4f}s")
print(f"Comprehension: {time_comp:.4f}s ({time_loop/time_comp:.2f}x)")
print(f"map():         {time_map:.4f}s ({time_loop/time_map:.2f}x)")

# Comprehension ist oft 10-30% schneller als Loop!
```

---

## Häufige Fallstricke

### ⚠️ Fallstrick 1: Zu komplexe Comprehensions

```python
# ❌ Schwer lesbar
result = [x * 2 if x % 2 == 0 else x * 3 if x % 3 == 0 else x 
          for x in range(20) if x > 5 if x < 15]

# ✅ Besser: for-Loop oder Funktion
def transform(x):
    if x % 2 == 0:
        return x * 2
    elif x % 3 == 0:
        return x * 3
    else:
        return x

result = [transform(x) for x in range(20) if 5 < x < 15]

# Faustregel: Maximal 2 Zeilen, sonst for-Loop
```

### ⚠️ Fallstrick 2: Nebenwirkungen (Side Effects)

```python
# ❌ Schlecht: List Comprehension mit Nebenwirkungen
output = []
[output.append(x * 2) for x in range(5)]  # Missbrauch!

# ✅ Richtig: for-Loop für Nebenwirkungen
output = []
for x in range(5):
    output.append(x * 2)

# Oder direkt mit Comprehension
output = [x * 2 for x in range(5)]
```

### ⚠️ Fallstrick 3: Variable Leaking (Python 2 Problem, gelöst in Python 3)

```python
# Python 3: Variable bleibt lokal in Comprehension
x = "outer"
result = [x for x in range(5)]
print(x)  # "outer" - nicht überschrieben! ✅

# In Python 2 wäre x jetzt 4 gewesen (Leak)
```

### ⚠️ Fallstrick 4: If-Else Position

```python
# if-else in Expression (vor for)
result = [x * 2 if x % 2 == 0 else x for x in range(10)]

# if als Filter (nach for)
result = [x * 2 for x in range(10) if x % 2 == 0]

# ❌ Syntaxfehler: else nach for
try:
    result = [x * 2 for x in range(10) if x % 2 == 0 else x]
except SyntaxError:
    print("else nicht nach if-Filter erlaubt!")
```

---

## Best Practices

### ✅ DO - Wann List Comprehensions verwenden

```python
# 1. Einfache Transformationen
squares = [x ** 2 for x in range(10)]

# 2. Filtern und Transformieren
evens_squared = [x ** 2 for x in range(10) if x % 2 == 0]

# 3. Flatten von verschachtelten Listen
flat = [item for sublist in nested for item in sublist]

# 4. Einfache Kartesische Produkte
pairs = [(x, y) for x in range(3) for y in range(3)]

# 5. Map + Filter kombiniert
result = [transform(x) for x in data if condition(x)]
```

### ❌ DON'T - Wann for-Loop besser ist

```python
# 1. Komplexe Logik
# ❌ Unleserlich
result = [complex_transform(x, y, z) if condition1(x) else 
          other_transform(x) if condition2(x) else x 
          for x, y, z in zip(a, b, c) if x > 0 if y < 10]

# ✅ Besser mit Loop
result = []
for x, y, z in zip(a, b, c):
    if x > 0 and y < 10:
        if condition1(x):
            result.append(complex_transform(x, y, z))
        elif condition2(x):
            result.append(other_transform(x))
        else:
            result.append(x)

# 2. Nebenwirkungen (Printing, Logging, DB-Writes)
# ❌ for x in data: print(x)]  # Falsch!
# ✅ for x in data: print(x)   # Richtig!

# 3. Mehr als 2-3 Zeilen Logik
# Verwende regulären Loop
```

---

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [List Comprehensions](https://docs.python.org/3/tutorial/datastructures.html#list-comprehensions) - Offizielles Tutorial
- [Data Structures](https://docs.python.org/3/tutorial/datastructures.html) - Comprehensions und mehr
- [PEP 202](https://www.python.org/dev/peps/pep-0202/) - List Comprehensions

---

## Verwandte Themen

- [[02_Sequenzen/04_Comprehensions/02_Generator Expressions|Generator Expressions]] - Lazy Evaluation
- [[02_Sequenzen/04_Comprehensions/03_Verschachtelte Comprehensions|Verschachtelte Comprehensions]] - Detaillierte Behandlung
- [[03_Mappings_und_Sets/01_Dictionaries/03_Dict Comprehensions|Dict Comprehensions]] - Dictionary-Variante
- [[03_Mappings_und_Sets/02_Sets/04_Set Comprehensions|Set Comprehensions]] - Set-Variante

---

← [[02_Sequenzen/03_Slicing/04_Übungen Slicing|Übungen Slicing]] | [[INDEX|📑 Index]] | [[02_Sequenzen/04_Comprehensions/02_Generator Expressions|Generator Expressions]] →