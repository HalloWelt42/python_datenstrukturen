---
tags: [python, datenstrukturen, comprehensions, nested, matrix]
created: 2025-10-01
---

← [[02_Sequenzen/04_Comprehensions/02_Generator Expressions|Generator Expressions]] | [[INDEX|📑 Index]] | [[02_Sequenzen/04_Comprehensions/04_Performance Vergleich|Performance Vergleich]] →

# Verschachtelte Comprehensions - Mehrdimensionale Strukturen

> [!tip] Lernziel
> Nach dieser Seite beherrschst du verschachtelte Comprehensions für mehrdimensionale Datenstrukturen. Du verstehst die Ausführungsreihenfolge, kannst Matrizen elegant verarbeiten und weißt, wann verschachtelte Comprehensions zu komplex werden.

## Was sind verschachtelte Comprehensions?

**Verschachtelte Comprehensions** sind Comprehensions innerhalb von Comprehensions. Sie ermöglichen die elegante Erstellung und Verarbeitung von mehrdimensionalen Datenstrukturen wie Matrizen, 3D-Arrays oder komplexen verschachtelten Listen.

```python
# Einfache 2D-Matrix erstellen
matrix = [[i * j for j in range(5)] for i in range(5)]

print("5x5 Multiplikationstabelle:")
for row in matrix:
    print(row)
# [0, 0, 0, 0, 0]
# [0, 1, 2, 3, 4]
# [0, 2, 4, 6, 8]
# [0, 3, 6, 9, 12]
# [0, 4, 8, 12, 16]
```

---

## Ausführungsreihenfolge verstehen

### Die Reihenfolge ist wie bei Loops

```python
# Verschachtelte Comprehension
result = [[i * j for j in range(3)] for i in range(3)]

# Äquivalenter verschachtelter Loop
result = []
for i in range(3):              # Äußere Schleife
    row = []
    for j in range(3):          # Innere Schleife
        row.append(i * j)
    result.append(row)

# Beide ergeben:
# [[0, 0, 0], [0, 1, 2], [0, 2, 4]]
```

**Wichtig:** Das **äußere** `for` kommt **rechts**, das **innere** kommt **links**!

```python
# Reihenfolge merken:
[[expression for inner in inner_iterable] for outer in outer_iterable]
#            ↑ innere Schleife           ↑ äußere Schleife

# Lesen von rechts nach links:
# 1. Äußere Schleife: for outer in outer_iterable
# 2. Innere Schleife: for inner in inner_iterable
# 3. Expression: expression
```

---

## 2D-Strukturen erstellen

### Matrizen

```python
# Nullmatrix
rows, cols = 4, 5
zero_matrix = [[0 for _ in range(cols)] for _ in range(rows)]
print(zero_matrix)
# [[0, 0, 0, 0, 0],
#  [0, 0, 0, 0, 0],
#  [0, 0, 0, 0, 0],
#  [0, 0, 0, 0, 0]]

# Matrix mit spezifischen Werten
matrix = [[row * cols + col for col in range(cols)] for row in range(rows)]
print(matrix)
# [[0, 1, 2, 3, 4],
#  [5, 6, 7, 8, 9],
#  [10, 11, 12, 13, 14],
#  [15, 16, 17, 18, 19]]

# Identitätsmatrix
n = 4
identity = [[1 if i == j else 0 for j in range(n)] for i in range(n)]
print(identity)
# [[1, 0, 0, 0],
#  [0, 1, 0, 0],
#  [0, 0, 1, 0],
#  [0, 0, 0, 1]]

# Schachbrett-Muster
size = 8
chess = [[1 if (i + j) % 2 == 0 else 0 for j in range(size)] for i in range(size)]
```

### Mit Bedingungen

```python
# Nur Elemente auf Hauptdiagonale
n = 5
diagonal_only = [[i * j if i == j else 0 for j in range(n)] for i in range(n)]
print(diagonal_only)
# [[0, 0, 0, 0, 0],
#  [0, 1, 0, 0, 0],
#  [0, 0, 4, 0, 0],
#  [0, 0, 0, 9, 0],
#  [0, 0, 0, 0, 16]]

# Obere Dreiecksmatrix
upper_triangle = [[i * j if j >= i else 0 for j in range(n)] for i in range(n)]
print(upper_triangle)
# [[0, 0, 0, 0, 0],
#  [0, 1, 2, 3, 4],
#  [0, 2, 4, 6, 8],
#  [0, 3, 6, 9, 12],
#  [0, 4, 8, 12, 16]]
```

---

## Flattening - Verschachtelung auflösen

### Eine Ebene flatten

```python
# 2D → 1D
matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]

# Mit verschachtelter Comprehension
flat = [item for row in matrix for item in row]
print(flat)  # [1, 2, 3, 4, 5, 6, 7, 8, 9]

# Äquivalenter Loop
flat = []
for row in matrix:
    for item in row:
        flat.append(item)

# Mit sum() (ungewöhnlich aber funktioniert)
flat = sum(matrix, [])
print(flat)  # [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

### Selektives Flattening

```python
# Flatten mit Filter
matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]

# Nur gerade Zahlen
flat_evens = [item for row in matrix for item in row if item % 2 == 0]
print(flat_evens)  # [2, 4, 6, 8]

# Nur aus bestimmten Zeilen
flat_first_two = [item for row in matrix[:2] for item in row]
print(flat_first_two)  # [1, 2, 3, 4, 5, 6]

# Mit Transformation
flat_squared = [item ** 2 for row in matrix for item in row if item > 5]
print(flat_squared)  # [36, 49, 64, 81]
```

---

## Matrix-Operationen

### Transposition

```python
# Matrix transponieren
matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]

# Methode 1: Mit zip() - elegant!
transposed = [list(col) for col in zip(*matrix)]
print(transposed)
# [[1, 4, 7], [2, 5, 8], [3, 6, 9]]

# Methode 2: Mit verschachtelter Comprehension
rows, cols = len(matrix), len(matrix[0])
transposed = [[matrix[i][j] for i in range(rows)] for j in range(cols)]
print(transposed)
# [[1, 4, 7], [2, 5, 8], [3, 6, 9]]
```

### Rotation

```python
# 90° im Uhrzeigersinn
matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]

# Transponieren + Zeilen umkehren
rotated = [list(reversed(col)) for col in zip(*matrix)]
print(rotated)
# [[7, 4, 1],
#  [8, 5, 2],
#  [9, 6, 3]]

# Oder mit Comprehension
n = len(matrix)
rotated = [[matrix[n-1-j][i] for j in range(n)] for i in range(n)]
print(rotated)
# [[7, 4, 1],
#  [8, 5, 2],
#  [9, 6, 3]]
```

### Element-weise Operationen

```python
# Zwei Matrizen addieren
A = [[1, 2], [3, 4]]
B = [[5, 6], [7, 8]]

C = [[A[i][j] + B[i][j] for j in range(len(A[0]))] for i in range(len(A))]
print(C)  # [[6, 8], [10, 12]]

# Skalar-Multiplikation
scalar = 3
scaled = [[scalar * element for element in row] for row in A]
print(scaled)  # [[3, 6], [9, 12]]

# Element-weise Multiplikation (Hadamard Product)
hadamard = [[A[i][j] * B[i][j] for j in range(len(A[0]))] for i in range(len(A))]
print(hadamard)  # [[5, 12], [21, 32]]
```

---

## 3D-Strukturen und höher

### 3D-Arrays

```python
# 3D-Array erstellen: [Tiefe][Zeilen][Spalten]
depth, rows, cols = 2, 3, 4

array_3d = [[[z * rows * cols + y * cols + x 
              for x in range(cols)] 
             for y in range(rows)] 
            for z in range(depth)]

print("Ebene 0:")
for row in array_3d[0]:
    print(row)
# [0, 1, 2, 3]
# [4, 5, 6, 7]
# [8, 9, 10, 11]

print("\nEbene 1:")
for row in array_3d[1]:
    print(row)
# [12, 13, 14, 15]
# [16, 17, 18, 19]
# [20, 21, 22, 23]

# RGB-Bildarray (Höhe x Breite x 3 Kanäle)
height, width = 4, 4
rgb_image = [[[channel for channel in [255, 128, 0]]  # Orange
              for _ in range(width)]
             for _ in range(height)]
```

### 3D Flattening

```python
# 3D → 1D
array_3d = [
    [[1, 2], [3, 4]],
    [[5, 6], [7, 8]]
]

# Dreifach verschachtelt
flat = [item 
        for layer in array_3d 
        for row in layer 
        for item in row]
print(flat)  # [1, 2, 3, 4, 5, 6, 7, 8]

# Äquivalenter Loop
flat = []
for layer in array_3d:
    for row in layer:
        for item in row:
            flat.append(item)
```

---

## Komplexe verschachtelte Strukturen

### Verschachtelte Dictionaries

```python
# Liste von Dictionaries mit Listen
students = ["Alice", "Bob", "Charlie"]
subjects = ["Math", "English", "Science"]

# Für jeden Student ein Dict mit allen Fächern und Noten
grades = {
    student: {
        subject: 0 
        for subject in subjects
    }
    for student in students
}

print(grades)
# {'Alice': {'Math': 0, 'English': 0, 'Science': 0},
#  'Bob': {'Math': 0, 'English': 0, 'Science': 0},
#  'Charlie': {'Math': 0, 'English': 0, 'Science': 0}}

# Mit Zufallswerten
import random
grades = {
    student: {
        subject: random.randint(60, 100)
        for subject in subjects
    }
    for student in students
}
```

### Verschachtelte Listen mit Filter

```python
# Nur bestimmte Elemente aus verschachtelter Struktur
data = [
    [1, 2, 3, 4],
    [5, 6, 7, 8],
    [9, 10, 11, 12]
]

# Nur Elemente > 5 aus jedem Row, als verschachtelte Liste
filtered = [[item for item in row if item > 5] for row in data]
print(filtered)
# [[], [6, 7, 8], [9, 10, 11, 12]]

# Nur nicht-leere Zeilen behalten
filtered_non_empty = [
    [item for item in row if item > 5] 
    for row in data 
    if any(item > 5 for item in row)
]
print(filtered_non_empty)
# [[6, 7, 8], [9, 10, 11, 12]]
```

---

## Kartesisches Produkt

### Alle Kombinationen

```python
# Einfaches kartesisches Produkt
colors = ["red", "green", "blue"]
sizes = ["S", "M", "L"]

products = [[color, size] for color in colors for size in sizes]
print(products)
# [['red', 'S'], ['red', 'M'], ['red', 'L'],
#  ['green', 'S'], ['green', 'M'], ['green', 'L'],
#  ['blue', 'S'], ['blue', 'M'], ['blue', 'L']]

# Als Tupel
products = [(color, size) for color in colors for size in sizes]

# Mit drei Dimensionen
materials = ["Cotton", "Polyester"]
full_products = [
    (color, size, material)
    for color in colors
    for size in sizes
    for material in materials
]
print(f"Anzahl Kombinationen: {len(full_products)}")  # 3 * 3 * 2 = 18
```

### Mit Bedingungen

```python
# Nur bestimmte Kombinationen
numbers = range(10)
pairs = [(x, y) for x in numbers for y in numbers if x < y]
print(pairs[:5])  # [(0, 1), (0, 2), (0, 3), (0, 4), (0, 5)]

# Pythagoräische Tripel bis n
n = 20
triplets = [
    (a, b, c)
    for a in range(1, n)
    for b in range(a, n)
    for c in range(b, n)
    if a**2 + b**2 == c**2
]
print(triplets)  # [(3, 4, 5), (5, 12, 13), (6, 8, 10), (8, 15, 17), (9, 12, 15)]
```

---

## Praktische Anwendungen

### Anwendungsfall 1: Grid-basierte Spiele

```python
# Spielbrett erstellen
size = 8
board = [
    [
        "⬜" if (i + j) % 2 == 0 else "⬛"
        for j in range(size)
    ]
    for i in range(size)
]

print("Schachbrett:")
for row in board:
    print(" ".join(row))

# Tic-Tac-Toe Board mit Initialwerten
board = [[" " for _ in range(3)] for _ in range(3)]

# Einige Züge
board[0][0] = "X"
board[1][1] = "O"
board[0][2] = "X"

print("\nTic-Tac-Toe:")
for row in board:
    print("|".join(row))
```

### Anwendungsfall 2: Pixel-Manipulation

```python
# Einfaches Gradienten-Bild
width, height = 10, 5

# Horizontaler Gradient
gradient = [
    [int(255 * x / width) for x in range(width)]
    for _ in range(height)
]

print("Horizontaler Gradient:")
for row in gradient:
    print(" ".join(f"{val:3d}" for val in row))

# RGB-Bild mit Farbverlauf
rgb_gradient = [
    [
        (int(255 * x / width), 128, int(255 * y / height))
        for x in range(width)
    ]
    for y in range(height)
]
```

### Anwendungsfall 3: Tabellarische Daten

```python
# CSV-ähnliche Datenstruktur
headers = ["Name", "Age", "City"]
rows = [
    ["Alice", 25, "Berlin"],
    ["Bob", 30, "München"],
    ["Charlie", 28, "Hamburg"]
]

# Als Dictionary-Liste
data = [
    {header: value for header, value in zip(headers, row)}
    for row in rows
]
print(data)
# [{'Name': 'Alice', 'Age': 25, 'City': 'Berlin'},
#  {'Name': 'Bob', 'Age': 30, 'City': 'München'},
#  {'Name': 'Charlie', 'Age': 28, 'City': 'Hamburg'}]

# Spalten extrahieren
ages = [row[1] for row in rows]
cities = [row[2] for row in rows]

# Filterung und Transformation
adults_in_berlin = [
    row[0]
    for row in rows
    if row[1] >= 18 and row[2] == "Berlin"
]
```

### Anwendungsfall 4: Matrix-Mathematik

```python
# Matrix-Multiplikation (vereinfacht)
A = [[1, 2], [3, 4]]
B = [[5, 6], [7, 8]]

# C = A × B
n = len(A)
C = [
    [
        sum(A[i][k] * B[k][j] for k in range(n))
        for j in range(n)
    ]
    for i in range(n)
]
print("A × B =")
for row in C:
    print(row)
# [[19, 22], [43, 50]]

# Determinante 2x2
def det_2x2(matrix):
    return matrix[0][0] * matrix[1][1] - matrix[0][1] * matrix[1][0]

print(f"det(A) = {det_2x2(A)}")  # -2
```

---

## Lesbarkeit vs Komplexität

### Wann zu komplex?

```python
# ❌ ZU KOMPLEX - unleserlich!
result = [
    [
        x * y if x % 2 == 0 and y % 2 == 0 else x + y if x > 5 else 0
        for y in range(10) if y != 5
    ]
    for x in range(20) if x % 3 != 0
]

# ✅ BESSER - aufgeteilt
def transform(x, y):
    if x % 2 == 0 and y % 2 == 0:
        return x * y
    elif x > 5:
        return x + y
    else:
        return 0

result = [
    [transform(x, y) for y in range(10) if y != 5]
    for x in range(20) if x % 3 != 0
]

# ✅ ODER - regulärer Loop
result = []
for x in range(20):
    if x % 3 != 0:
        row = []
        for y in range(10):
            if y != 5:
                row.append(transform(x, y))
        result.append(row)
```

### Faustregel für Lesbarkeit

```python
# ✅ Einfach - OK
simple = [[i * j for j in range(5)] for i in range(5)]

# ✅ Mit einfachem Filter - OK
filtered = [[i * j for j in range(5) if j % 2 == 0] for i in range(5)]

# ⚠️ Grenzwertig - überlegen ob Loop besser
complex = [
    [transform(i, j) for j in range(5) if condition(j)]
    for i in range(5) if another_condition(i)
]

# ❌ Zu komplex - Loop verwenden!
too_complex = [
    [
        f(i, j) if c1(i) else g(i, j) if c2(j) else h(i, j)
        for j in data2 if c3(j)
    ]
    for i in data1 if c4(i) and c5(i)
]
```

---

## Häufige Fallstricke

### ⚠️ Fallstrick 1: Reihenfolge der for-Klauseln

```python
# ❌ Verwirrt die Reihenfolge
# Denkt: "Für jedes i, dann für jedes j"
wrong = [[i, j] for i in range(3) for j in range(3)]
print(wrong)
# [[0, 0], [0, 1], [0, 2], [1, 0], [1, 1], [1, 2], [2, 0], [2, 1], [2, 2]]

# Das ist NICHT dasselbe wie:
also_wrong = [[i, j] for j in range(3) for i in range(3)]
print(also_wrong)
# [[0, 0], [1, 0], [2, 0], [0, 1], [1, 1], [2, 1], [0, 2], [1, 2], [2, 2]]

# ✅ Klare Zuordnung
matrix = [[f"({i},{j})" for j in range(3)] for i in range(3)]
print(matrix)
# [['(0,0)', '(0,1)', '(0,2)'],
#  ['(1,0)', '(1,1)', '(1,2)'],
#  ['(2,0)', '(2,1)', '(2,2)']]
```

### ⚠️ Fallstrick 2: Gemeinsame Referenz

```python
# ❌ FALSCH: Alle Zeilen sind dieselbe Liste!
n = 3
wrong = [[0] * n] * n
wrong[0][0] = 1
print(wrong)
# [[1, 0, 0], [1, 0, 0], [1, 0, 0]] - alle geändert!

# ✅ RICHTIG: Jede Zeile ist eigene Liste
correct = [[0 for _ in range(n)] for _ in range(n)]
correct[0][0] = 1
print(correct)
# [[1, 0, 0], [0, 0, 0], [0, 0, 0]] - nur erste!
```

### ⚠️ Fallstrick 3: Filter vs. Expression Position

```python
# Filter NACH for
filtered = [[j for j in range(5) if j % 2 == 0] for i in range(3)]
print(filtered)
# [[0, 2, 4], [0, 2, 4], [0, 2, 4]]

# If-Else IN Expression (VOR for)
conditional = [[j if j % 2 == 0 else -j for j in range(5)] for i in range(3)]
print(conditional)
# [[0, -1, 2, -3, 4], [0, -1, 2, -3, 4], [0, -1, 2, -3, 4]]

# ❌ FEHLER: else nach Filter
try:
    wrong = [[j for j in range(5) if j % 2 == 0 else -j] for i in range(3)]
except SyntaxError:
    print("Syntaxfehler!")
```

---

## Best Practices

### ✅ DO

```python
# 1. Einfache 2D-Strukturen
matrix = [[0 for _ in range(cols)] for _ in range(rows)]

# 2. Matrix-Transposition mit zip()
transposed = [list(col) for col in zip(*matrix)]

# 3. Flattening einer Ebene
flat = [item for row in matrix for item in row]

# 4. Kartesisches Produkt mit Bedingungen
pairs = [(x, y) for x in range(n) for y in range(n) if x < y]

# 5. Klare Namen verwenden
grid = [[f"cell_{i}_{j}" for j in range(width)] for i in range(height)]
```

### ❌ DON'T

```python
# 1. Zu viele Verschachtelungsebenen
# ❌ 4D+ wird unleserlich
hyper = [[[[0 for _ in range(a)] for _ in range(b)] 
          for _ in range(c)] for _ in range(d)]

# 2. Komplexe Logik in Expression
# ❌ Schwer zu debuggen
complex = [[transform(i, j) if condition1(i) and condition2(j) 
            else alternative(i, j) if other_condition(i) else 0
            for j in range(n) if j != 5]
           for i in range(m) if i % 2 == 0]

# 3. Mehr als 2 Zeilen
# ❌ Nutze regulären Loop
```

---

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [List Comprehensions](https://docs.python.org/3/tutorial/datastructures.html#nested-list-comprehensions) - Nested List Comprehensions
- [Data Structures](https://docs.python.org/3/tutorial/datastructures.html) - Tutorial
- [PEP 202](https://www.python.org/dev/peps/pep-0202/) - List Comprehensions Specification

---

## Verwandte Themen

- [[02_Sequenzen/04_Comprehensions/01_List Comprehensions|List Comprehensions]] - Grundlagen
- [[02_Sequenzen/01_Listen/04_Nested Lists|Nested Lists]] - Verschachtelte Listen Details
- [[02_Sequenzen/03_Slicing/01_Slicing Grundlagen|Slicing]] - Matrix-Slicing
- [[06_Praxis/01_Best_Practices/01_Idiomatisches Python|Idiomatisches Python]] - Lesbarkeit

---

← [[02_Sequenzen/04_Comprehensions/02_Generator Expressions|Generator Expressions]] | [[INDEX|📑 Index]] | [[02_Sequenzen/04_Comprehensions/04_Performance Vergleich|Performance Vergleich]] →