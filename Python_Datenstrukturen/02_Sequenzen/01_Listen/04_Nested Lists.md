---
tags: [python, datenstrukturen, list, nested, matrix]
created: 2025-10-01
---

← [[02_Sequenzen/01_Listen/03_List Operationen|List Operationen]] | [[INDEX|📑 Index]] | [[02_Sequenzen/01_Listen/05_List Performance|List Performance]] →

# Nested Lists - Mehrdimensionale Datenstrukturen

> [!tip] Lernziel
> Nach dieser Seite verstehst du, wie verschachtelte Listen funktionieren, wie du Matrizen erstellst und manipulierst, und kennst die häufigsten Fallstricke beim Arbeiten mit mehrdimensionalen Strukturen.

## Was sind Nested Lists?

**Nested Lists** (verschachtelte Listen) sind Listen, die andere Listen als Elemente enthalten. Sie ermöglichen mehrdimensionale Datenstrukturen wie Matrizen, Tabellen oder Bäume.

```python
# Einfache verschachtelte Liste
nested = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]

# Verschiedene Verschachtelungstiefen
flat = [1, 2, 3]                    # 1D - flach
nested_2d = [[1, 2], [3, 4]]        # 2D - Matrix
nested_3d = [[[1, 2]], [[3, 4]]]    # 3D - Tensor
irregular = [[1], [2, 3], [4, 5, 6]] # Unregelmäßig (Jagged Array)
```

**Typische Anwendungen:**
- 📊 Matrizen und Tabellen
- 🎮 Spielbretter (Schach, Tic-Tac-Toe)
- 📈 Zeitreihendaten mit mehreren Dimensionen
- 🗂️ Hierarchische Datenstrukturen
- 🖼️ Bildpixel (RGB-Werte)

---

## Erstellung von Nested Lists

### 2D-Listen (Matrizen)

```python
# Methode 1: Literal-Syntax (übersichtlich für kleine Matrizen)
matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]

# Methode 2: List Comprehension (flexibel)
rows, cols = 3, 4
matrix = [[0 for _ in range(cols)] for _ in range(rows)]
print(matrix)
# [[0, 0, 0, 0],
#  [0, 0, 0, 0],
#  [0, 0, 0, 0]]

# Methode 3: Mit Werten initialisieren
matrix = [[row * cols + col for col in range(cols)] for row in range(rows)]
print(matrix)
# [[0, 1, 2, 3],
#  [4, 5, 6, 7],
#  [8, 9, 10, 11]]

# Methode 4: Identitätsmatrix
n = 4
identity = [[1 if i == j else 0 for j in range(n)] for i in range(n)]
print(identity)
# [[1, 0, 0, 0],
#  [0, 1, 0, 0],
#  [0, 0, 1, 0],
#  [0, 0, 0, 1]]
```

### ⚠️ KRITISCHER FALLSTRICK: Der `*` Operator

```python
# ❌ FALSCH: Alle Zeilen sind DIESELBE Liste!
rows, cols = 3, 3
matrix = [[0] * cols] * rows

# Scheinbar funktioniert es:
print(matrix)
# [[0, 0, 0], [0, 0, 0], [0, 0, 0]]

# Aber beim Ändern passiert Chaos:
matrix[0][0] = 1
print(matrix)
# [[1, 0, 0], [1, 0, 0], [1, 0, 0]]  # ALLE Zeilen ändern sich!

# Warum? Beweis:
matrix = [[0] * 3] * 3
print(id(matrix[0]))  # z.B. 140234567890
print(id(matrix[1]))  # 140234567890 - IDENTISCH!
print(id(matrix[2]))  # 140234567890 - IDENTISCH!

# ✅ RICHTIG: Jede Zeile ist eigene Liste
matrix = [[0] * cols for _ in range(rows)]
matrix[0][0] = 1
print(matrix)
# [[1, 0, 0], [0, 0, 0], [0, 0, 0]]  # Nur erste Zeile!

# Beweis:
print(id(matrix[0]) == id(matrix[1]))  # False - verschiedene Objekte!
```

**Warum passiert das?**
```python
# Der * Operator erstellt keine Kopien, sondern Referenzen!
inner = [0, 0, 0]
matrix = [inner] * 3  # Drei Referenzen auf DASSELBE Objekt

# Visualisierung:
# matrix[0] ──┐
#             ├──→ [0, 0, 0]
# matrix[1] ──┤
#             │
# matrix[2] ──┘

# Mit List Comprehension:
matrix = [[0, 0, 0] for _ in range(3)]  # Drei VERSCHIEDENE Objekte

# Visualisierung:
# matrix[0] ──→ [0, 0, 0]
# matrix[1] ──→ [0, 0, 0]
# matrix[2] ──→ [0, 0, 0]
```

### 3D-Listen und höhere Dimensionen

```python
# 3D-Array: [Ebenen][Zeilen][Spalten]
depth, rows, cols = 2, 3, 4
array_3d = [
    [[0 for _ in range(cols)] for _ in range(rows)]
    for _ in range(depth)
]

print(f"Shape: {depth}x{rows}x{cols}")
print(f"Ebene 0: {array_3d[0]}")

# Mit Werten initialisieren (z.B. Koordinaten)
array_3d = [
    [[f"({z},{y},{x})" for x in range(cols)]
     for y in range(rows)]
    for z in range(depth)
]

print(array_3d[0][0][0])  # "(0,0,0)"
print(array_3d[1][2][3])  # "(1,2,3)"
```

---

## Zugriff auf Elemente

### Indexierung in 2D-Listen

```python
matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]

# Einzelnes Element: matrix[row][col]
print(matrix[0][0])  # 1 - erste Zeile, erste Spalte
print(matrix[1][2])  # 6 - zweite Zeile, dritte Spalte
print(matrix[2][1])  # 8 - dritte Zeile, zweite Spalte

# Negativer Index funktioniert auch
print(matrix[-1][-1])  # 9 - letzte Zeile, letzte Spalte

# Ganze Zeile zugreifen
first_row = matrix[0]
print(first_row)  # [1, 2, 3]

# Spalte zugreifen (etwas umständlicher)
first_col = [row[0] for row in matrix]
print(first_col)  # [1, 4, 7]
```

### Slicing in Nested Lists

```python
matrix = [
    [1,  2,  3,  4],
    [5,  6,  7,  8],
    [9,  10, 11, 12],
    [13, 14, 15, 16]
]

# Zeilen-Slicing
first_two_rows = matrix[:2]
print(first_two_rows)
# [[1, 2, 3, 4], [5, 6, 7, 8]]

# Spalten-Slicing (für jede Zeile)
first_two_cols = [row[:2] for row in matrix]
print(first_two_cols)
# [[1, 2], [5, 6], [9, 10], [13, 14]]

# Submatrix extrahieren
submatrix = [row[1:3] for row in matrix[1:3]]
print(submatrix)
# [[6, 7], [10, 11]]

# Einzelne Zeile slicen
row = matrix[1][1:3]
print(row)  # [6, 7]
```

---

## Manipulation von Nested Lists

### Elemente ändern

```python
matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]

# Einzelnes Element ändern
matrix[1][1] = 99
print(matrix)
# [[1, 2, 3], [4, 99, 6], [7, 8, 9]]

# Ganze Zeile ersetzen
matrix[0] = [10, 20, 30]
print(matrix)
# [[10, 20, 30], [4, 99, 6], [7, 8, 9]]

# Spalte ändern (alle Zeilen durchgehen)
for row in matrix:
    row[0] = 0
print(matrix)
# [[0, 20, 30], [0, 99, 6], [0, 8, 9]]
```

### Zeilen und Spalten hinzufügen/entfernen

```python
matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]

# Zeile hinzufügen
matrix.append([10, 11, 12])
print(matrix)
# [[1, 2, 3], [4, 5, 6], [7, 8, 9], [10, 11, 12]]

# Zeile einfügen
matrix.insert(1, [0, 0, 0])
print(matrix)
# [[1, 2, 3], [0, 0, 0], [4, 5, 6], [7, 8, 9], [10, 11, 12]]

# Zeile entfernen
matrix.pop(1)  # Entfernt zweite Zeile
print(matrix)
# [[1, 2, 3], [4, 5, 6], [7, 8, 9], [10, 11, 12]]

# Spalte hinzufügen
for row in matrix:
    row.append(0)
print(matrix)
# [[1, 2, 3, 0], [4, 5, 6, 0], [7, 8, 9, 0], [10, 11, 12, 0]]

# Spalte entfernen (z.B. letzte)
for row in matrix:
    row.pop()
print(matrix)
# [[1, 2, 3], [4, 5, 6], [7, 8, 9], [10, 11, 12]]
```

---

## Iteration über Nested Lists

### Verschiedene Iterationsmuster

```python
matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]

# 1. Über Zeilen iterieren
for row in matrix:
    print(row)
# [1, 2, 3]
# [4, 5, 6]
# [7, 8, 9]

# 2. Über alle Elemente iterieren
for row in matrix:
    for element in row:
        print(element, end=" ")
# 1 2 3 4 5 6 7 8 9

# 3. Mit Indizes (enumerate)
for i, row in enumerate(matrix):
    for j, element in enumerate(row):
        print(f"[{i}][{j}] = {element}")

# 4. Mit Koordinaten (praktischer)
for i, row in enumerate(matrix):
    for j, element in enumerate(row):
        matrix[i][j] = element * 2  # Beispiel: Verdoppeln

print(matrix)
# [[2, 4, 6], [8, 10, 12], [14, 16, 18]]

# 5. Spaltenweise iterieren
rows, cols = len(matrix), len(matrix[0])
for col in range(cols):
    for row in range(rows):
        print(matrix[row][col], end=" ")
    print()
# 2 8 14
# 4 10 16
# 6 12 18
```

### Fortgeschrittene Iteration

```python
matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]

# Diagonale iterieren
size = len(matrix)
main_diagonal = [matrix[i][i] for i in range(size)]
print(main_diagonal)  # [1, 5, 9]

anti_diagonal = [matrix[i][size-1-i] for i in range(size)]
print(anti_diagonal)  # [3, 5, 7]

# Spiralförmig iterieren (außen nach innen)
def spiral_order(matrix):
    result = []
    while matrix:
        # Erste Zeile
        result.extend(matrix.pop(0))
        
        # Letzte Spalte (wenn noch Zeilen da sind)
        if matrix and matrix[0]:
            for row in matrix:
                result.append(row.pop())
        
        # Letzte Zeile (umgekehrt)
        if matrix:
            result.extend(matrix.pop()[::-1])
        
        # Erste Spalte (umgekehrt)
        if matrix and matrix[0]:
            for row in matrix[::-1]:
                result.append(row.pop(0))
    
    return result

matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]
print(spiral_order(matrix))  # [1, 2, 3, 6, 9, 8, 7, 4, 5]
```

---

## Matrix-Operationen

### Transposition

```python
# Matrix transponieren (Zeilen ↔ Spalten)
matrix = [
    [1, 2, 3],
    [4, 5, 6]
]

# Methode 1: zip() (elegant!)
transposed = list(zip(*matrix))
print(transposed)
# [(1, 4), (2, 5), (3, 6)]

# Als verschachtelte Listen
transposed = [list(col) for col in zip(*matrix)]
print(transposed)
# [[1, 4], [2, 5], [3, 6]]

# Methode 2: List Comprehension (expliziter)
rows, cols = len(matrix), len(matrix[0])
transposed = [[matrix[i][j] for i in range(rows)] for j in range(cols)]
print(transposed)
# [[1, 4], [2, 5], [3, 6]]

# Quadratische Matrix in-place transponieren
def transpose_inplace(matrix):
    n = len(matrix)
    for i in range(n):
        for j in range(i + 1, n):
            matrix[i][j], matrix[j][i] = matrix[j][i], matrix[i][j]

square = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]
transpose_inplace(square)
print(square)
# [[1, 4, 7], [2, 5, 8], [3, 6, 9]]
```

### Rotation

```python
# 90° im Uhrzeigersinn rotieren
def rotate_90_clockwise(matrix):
    # Transponieren, dann jede Zeile umkehren
    transposed = list(zip(*matrix))
    return [list(row[::-1]) for row in transposed]

matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]
rotated = rotate_90_clockwise(matrix)
print(rotated)
# [[7, 4, 1],
#  [8, 5, 2],
#  [9, 6, 3]]

# 90° gegen Uhrzeigersinn
def rotate_90_counter_clockwise(matrix):
    # Jede Zeile umkehren, dann transponieren
    reversed_rows = [row[::-1] for row in matrix]
    return [list(col) for col in zip(*reversed_rows)]

# 180° rotieren
def rotate_180(matrix):
    return [row[::-1] for row in matrix[::-1]]

matrix = [[1, 2], [3, 4]]
print(rotate_180(matrix))  # [[4, 3], [2, 1]]
```

### Flatten (Verschachtelung auflösen)

```python
# Eine Ebene flatten
nested = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]

# Methode 1: List Comprehension
flat = [item for row in nested for item in row]
print(flat)  # [1, 2, 3, 4, 5, 6, 7, 8, 9]

# Methode 2: sum() mit leerer Liste als Start
flat = sum(nested, [])
print(flat)  # [1, 2, 3, 4, 5, 6, 7, 8, 9]

# Methode 3: itertools.chain
from itertools import chain
flat = list(chain.from_iterable(nested))
print(flat)  # [1, 2, 3, 4, 5, 6, 7, 8, 9]

# Rekursiv für beliebige Tiefe
def flatten_recursive(nested):
    result = []
    for item in nested:
        if isinstance(item, list):
            result.extend(flatten_recursive(item))
        else:
            result.append(item)
    return result

deep = [1, [2, [3, [4, 5]], 6], 7, [8, 9]]
print(flatten_recursive(deep))  # [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

---

## Praktische Anwendungen

### Anwendungsfall 1: Tic-Tac-Toe Board

```python
class TicTacToe:
    def __init__(self):
        # 3x3 Board, leer ist None
        self.board = [[None for _ in range(3)] for _ in range(3)]
    
    def make_move(self, row, col, player):
        """Macht einen Zug (player ist 'X' oder 'O')"""
        if self.board[row][col] is None:
            self.board[row][col] = player
            return True
        return False
    
    def display(self):
        """Zeigt das Board"""
        for row in self.board:
            print("|".join([cell if cell else " " for cell in row]))
            print("-" * 5)
    
    def check_winner(self):
        """Prüft ob jemand gewonnen hat"""
        # Zeilen prüfen
        for row in self.board:
            if row[0] == row[1] == row[2] and row[0] is not None:
                return row[0]
        
        # Spalten prüfen
        for col in range(3):
            if (self.board[0][col] == self.board[1][col] == 
                self.board[2][col] and self.board[0][col] is not None):
                return self.board[0][col]
        
        # Diagonalen prüfen
        if (self.board[0][0] == self.board[1][1] == 
            self.board[2][2] and self.board[0][0] is not None):
            return self.board[0][0]
        
        if (self.board[0][2] == self.board[1][1] == 
            self.board[2][0] and self.board[0][2] is not None):
            return self.board[0][2]
        
        return None

# Spiel spielen
game = TicTacToe()
game.make_move(0, 0, 'X')
game.make_move(1, 1, 'O')
game.make_move(0, 1, 'X')
game.make_move(1, 0, 'O')
game.make_move(0, 2, 'X')
game.display()
print(f"Gewinner: {game.check_winner()}")  # Gewinner: X
```

### Anwendungsfall 2: Bildverarbeitung (RGB-Pixel)

```python
# Bild als 3D-Array: [Höhe][Breite][RGB]
def create_image(width, height, color=(255, 255, 255)):
    """Erstellt ein einfarbiges Bild"""
    return [[[color[0], color[1], color[2]] 
             for _ in range(width)] 
            for _ in range(height)]

def get_pixel(image, x, y):
    """Gibt RGB-Werte eines Pixels zurück"""
    return tuple(image[y][x])

def set_pixel(image, x, y, color):
    """Setzt Farbe eines Pixels"""
    image[y][x] = list(color)

def draw_rectangle(image, x, y, width, height, color):
    """Zeichnet ein Rechteck"""
    for dy in range(height):
        for dx in range(width):
            if 0 <= y + dy < len(image) and 0 <= x + dx < len(image[0]):
                set_pixel(image, x + dx, y + dy, color)

def grayscale(image):
    """Konvertiert Bild zu Graustufen"""
    for row in image:
        for pixel in row:
            avg = sum(pixel) // 3
            pixel[0] = pixel[1] = pixel[2] = avg

# Beispiel
img = create_image(10, 10, (255, 255, 255))  # Weißes 10x10 Bild
draw_rectangle(img, 2, 2, 5, 5, (255, 0, 0))  # Rotes Rechteck
print(f"Pixel bei (3,3): {get_pixel(img, 3, 3)}")  # (255, 0, 0)
```

### Anwendungsfall 3: Tabellendaten (CSV-ähnlich)

```python
# Tabelle mit Kopfzeile
def create_table(headers, rows):
    """Erstellt Tabelle mit Kopfzeile"""
    return [headers] + rows

def get_column(table, col_name):
    """Extrahiert Spalte nach Name"""
    headers = table[0]
    if col_name not in headers:
        return None
    
    col_index = headers.index(col_name)
    return [row[col_index] for row in table[1:]]

def filter_rows(table, col_name, condition):
    """Filtert Zeilen basierend auf Bedingung"""
    headers = table[0]
    col_index = headers.index(col_name)
    
    filtered = [headers]
    for row in table[1:]:
        if condition(row[col_index]):
            filtered.append(row)
    
    return filtered

def print_table(table):
    """Gibt Tabelle formatiert aus"""
    col_widths = [max(len(str(row[i])) for row in table) 
                  for i in range(len(table[0]))]
    
    for row in table:
        print(" | ".join(str(cell).ljust(width) 
                        for cell, width in zip(row, col_widths)))
        if row == table[0]:
            print("-" * (sum(col_widths) + 3 * (len(col_widths) - 1)))

# Beispiel
table = create_table(
    ["Name", "Alter", "Stadt"],
    [
        ["Anna", 25, "Berlin"],
        ["Bob", 30, "München"],
        ["Charlie", 25, "Berlin"],
        ["David", 35, "Hamburg"]
    ]
)

print("Gesamte Tabelle:")
print_table(table)

print("\nAlter-Spalte:")
print(get_column(table, "Alter"))

print("\nPersonen über 25:")
filtered = filter_rows(table, "Alter", lambda age: age > 25)
print_table(filtered)
```

### Anwendungsfall 4: Pascal's Triangle

```python
def generate_pascals_triangle(n):
    """Generiert die ersten n Zeilen des Pascal-Dreiecks"""
    triangle = [[1]]
    
    for i in range(1, n):
        row = [1]
        for j in range(1, i):
            row.append(triangle[i-1][j-1] + triangle[i-1][j])
        row.append(1)
        triangle.append(row)
    
    return triangle

def print_pascals_triangle(triangle):
    """Gibt Pascal-Dreieck formatiert aus"""
    max_width = len(" ".join(map(str, triangle[-1])))
    
    for row in triangle:
        row_str = " ".join(map(str, row))
        print(row_str.center(max_width))

# Generiere und zeige 10 Zeilen
triangle = generate_pascals_triangle(10)
print_pascals_triangle(triangle)
```

---

## Häufige Fallstricke

### ⚠️ Fallstrick 1: Shallow Copy Problem

```python
# ❌ Shallow Copy kopiert nur äußere Liste
original = [[1, 2], [3, 4]]
shallow = original.copy()  # oder original[:]

shallow[0][0] = 999
print(original)  # [[999, 2], [3, 4]] - auch geändert!
print(shallow)   # [[999, 2], [3, 4]]

# Warum? Die inneren Listen sind immer noch dieselben Objekte!

# ✅ Deep Copy für verschachtelte Strukturen
import copy
original = [[1, 2], [3, 4]]
deep = copy.deepcopy(original)

deep[0][0] = 999
print(original)  # [[1, 2], [3, 4]] - unverändert
print(deep)      # [[999, 2], [3, 4]]
```

### ⚠️ Fallstrick 2: Verschiedene Zeilenlängen

```python
# Unregelmäßige Matrix (Jagged Array)
jagged = [[1, 2], [3, 4, 5], [6]]

# ❌ Annahme: Alle Zeilen haben gleiche Länge
try:
    print(jagged[2][1])  # IndexError!
except IndexError as e:
    print(f"Fehler: {e}")

# ✅ Sichere Zugriffe
def safe_get(matrix, row, col, default=None):
    if row < len(matrix) and col < len(matrix[row]):
        return matrix[row][col]
    return default

print(safe_get(jagged, 2, 1, default=0))  # 0
```

### ⚠️ Fallstrick 3: Modifikation während Iteration

```python
matrix = [[1, 2], [3, 4], [5, 6]]

# ❌ Liste während Iteration ändern
for row in matrix:
    if sum(row) > 5:
        matrix.remove(row)  # GEFÄHRLICH!

# ✅ Über Kopie iterieren
for row in matrix[:]:
    if sum(row) > 5:
        matrix.remove(row)

# ✅ Besser: Neue Liste erstellen
matrix = [row for row in matrix if sum(row) <= 5]
```

---

## Performance-Hinweise

### Speicherverbrauch

```python
import sys

# Verschachtelte Listen verbrauchen viel Speicher
matrix_2d = [[0] * 1000 for _ in range(1000)]
print(f"2D-Matrix: {sys.getsizeof(matrix_2d) / 1024:.2f} KB")

# Für große numerische Matrizen: NumPy verwenden
import numpy as np
array_np = np.zeros((1000, 1000))
print(f"NumPy Array: {array_np.nbytes / 1024:.2f} KB")

# NumPy ist oft 10-100x speichereffizienter!
```

### Zugriffsmuster

```python
# Zeilenweiser Zugriff ist schneller (Cache-freundlich)
matrix = [[j for j in range(1000)] for i in range(1000)]

# ✅ Schnell: Zeilenweise
total = 0
for row in matrix:
    for element in row:
        total += element

# ❌ Langsamer: Spaltenweise
total = 0
for col in range(1000):
    for row in range(1000):
        total += matrix[row][col]
```

**Wann Nested Lists verwenden?**
- ✅ Kleine bis mittlere Datenmengen
- ✅ Heterogene Daten (verschiedene Typen)
- ✅ Unregelmäßige Strukturen
- ❌ Große numerische Matrizen → NumPy
- ❌ Viele mathematische Operationen → NumPy

---

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [Data Structures Tutorial](https://docs.python.org/3/tutorial/datastructures.html#nested-list-comprehensions) - Nested List Comprehensions
- [copy Module](https://docs.python.org/3/library/copy.html) - Shallow und Deep Copy
- [itertools](https://docs.python.org/3/library/itertools.html) - Tools für effiziente Iteration
- [NumPy Documentation](https://numpy.org/doc/stable/) - Für große numerische Arrays

---

## Verwandte Themen

- [[02_Sequenzen/01_Listen/01_List Basics|List Basics]] - Grundlagen zu Listen
- [[02_Sequenzen/01_Listen/05_List Performance|List Performance]] - Performance-Optimierung
- [[02_Sequenzen/04_Comprehensions/03_Verschachtelte Comprehensions|Verschachtelte Comprehensions]] - Elegante Erstellung verschachtelter Listen
- [[04_Fortgeschritten/01_Arrays/01_Array Module|Array Module]] - Typisierte Arrays für numerische Daten

---

← [[02_Sequenzen/01_Listen/03_List Operationen|List Operationen]] | [[INDEX|📑 Index]] | [[02_Sequenzen/01_Listen/05_List Performance|List Performance]] →