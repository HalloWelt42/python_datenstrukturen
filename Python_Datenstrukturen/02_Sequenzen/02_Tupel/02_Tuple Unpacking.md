---
tags: [python, datenstrukturen, tuple, unpacking, destructuring]
created: 2025-10-01
---

← [[02_Sequenzen/02_Tupel/01_Tuple Basics|Tuple Basics]] | [[INDEX|📑 Index]] | [[02_Sequenzen/02_Tupel/03_Named Tuples|Named Tuples]] →

# Tuple Unpacking - Elegantes Entpacken

> [!tip] Lernziel
> Nach dieser Seite beherrschst du Tuple Unpacking (auch Sequence Unpacking genannt), eine der pythonischsten Techniken zum Arbeiten mit Tupeln und anderen Sequenzen. Du kennst alle Varianten vom einfachen bis zum erweiterten Unpacking.

## Was ist Tuple Unpacking?

**Tuple Unpacking** (auch **Destructuring** genannt) ist eine Technik, bei der Werte aus einer Sequenz in einzelne Variablen extrahiert werden. Es funktioniert nicht nur mit Tupeln, sondern mit allen Sequenzen (Listen, Strings, etc.).

```python
# Statt einzeln zuzugreifen:
point = (10, 20)
x = point[0]
y = point[1]

# Mit Unpacking - elegant und pythonisch:
x, y = (10, 20)
print(f"x={x}, y={y}")  # x=10, y=20

# Funktioniert auch ohne Klammern
x, y = 10, 20
print(f"x={x}, y={y}")  # x=10, y=20
```

---

## Grundlegendes Unpacking

### Einfaches Unpacking

```python
# Mit Tupel
coordinates = (3, 4)
x, y = coordinates
print(f"x={x}, y={y}")  # x=3, y=4

# Mit Liste (funktioniert auch!)
rgb = [255, 128, 0]
r, g, b = rgb
print(f"RGB: {r}, {g}, {b}")  # RGB: 255, 128, 0

# Mit String
word = "Hi"
first, second = word
print(f"{first} - {second}")  # H - i

# Mit Range
a, b, c = range(3)
print(f"{a}, {b}, {c}")  # 0, 1, 2
```

### Anzahl muss übereinstimmen

```python
# ✅ Richtig: Gleiche Anzahl
point = (10, 20)
x, y = point  # OK

# ❌ Zu wenige Variablen
try:
    x = (10, 20)  # Nur eine Variable
    print(x)  # (10, 20) - kein Unpacking!
    
    x, = (10, 20)  # Versuche auf eine Variable zu unpacken
except ValueError as e:
    print(f"Fehler: {e}")  # too many values to unpack (expected 1)

# ❌ Zu viele Variablen
try:
    x, y, z = (10, 20)
except ValueError as e:
    print(f"Fehler: {e}")  # not enough values to unpack (expected 3, got 2)

# ✅ Ein-Element Tupel unpacken
single = (42,)
value, = single  # Komma ist wichtig!
print(value)  # 42
```

---

## Erweiterte Unpacking-Techniken

### Nested Unpacking (Verschachtelt)

```python
# Verschachtelte Tupel
nested = ((1, 2), (3, 4))
(a, b), (c, d) = nested
print(f"a={a}, b={b}, c={c}, d={d}")  # a=1, b=2, c=3, d=4

# Tiefere Verschachtelung
deep = (1, (2, (3, 4)))
a, (b, (c, d)) = deep
print(f"a={a}, b={b}, c={c}, d={d}")  # a=1, b=2, c=3, d=4

# Praktisches Beispiel: Koordinatensystem
points = [(10, 20), (30, 40), (50, 60)]
for x, y in points:
    print(f"Punkt: ({x}, {y})")
# Punkt: (10, 20)
# Punkt: (30, 40)
# Punkt: (50, 60)

# Matrix durchlaufen
matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]

for row in matrix:
    a, b, c = row
    print(f"{a} + {b} + {c} = {a+b+c}")
```

### Extended Unpacking mit `*` (Python 3+)

```python
# * sammelt mehrere Werte
first, *rest = [1, 2, 3, 4, 5]
print(first)  # 1
print(rest)   # [2, 3, 4, 5]

# Am Ende
*initial, last = [1, 2, 3, 4, 5]
print(initial)  # [1, 2, 3, 4]
print(last)     # 5

# In der Mitte
first, *middle, last = [1, 2, 3, 4, 5]
print(first)   # 1
print(middle)  # [2, 3, 4]
print(last)    # 5

# Nur ein * erlaubt!
try:
    *a, *b = [1, 2, 3]  # SyntaxError
except SyntaxError:
    print("Nur ein * pro Unpacking!")
```

**Praktische Anwendungen von Extended Unpacking:**

```python
# Erstes und letztes Element, Rest ignorieren
first, *_, last = [1, 2, 3, 4, 5, 6, 7, 8, 9]
print(f"Erstes: {first}, Letztes: {last}")  # Erstes: 1, Letztes: 9

# Head und Tail Pattern
def process_list(items):
    if not items:
        return
    
    head, *tail = items
    print(f"Verarbeite: {head}")
    if tail:
        process_list(tail)

process_list([1, 2, 3, 4, 5])
# Verarbeite: 1
# Verarbeite: 2
# Verarbeite: 3
# Verarbeite: 4
# Verarbeite: 5

# CSV-Header und Daten trennen
csv_data = [
    ["Name", "Alter", "Stadt"],
    ["Anna", "25", "Berlin"],
    ["Bob", "30", "München"],
    ["Charlie", "28", "Hamburg"]
]

header, *rows = csv_data
print(f"Header: {header}")
print(f"Anzahl Datensätze: {len(rows)}")

for row in rows:
    name, age, city = row
    print(f"{name} ({age}) aus {city}")
```

### Ignorieren von Werten mit `_`

```python
# _ als Konvention für ungenutzte Werte
point_3d = (10, 20, 30)
x, y, _ = point_3d  # z wird nicht gebraucht
print(f"x={x}, y={y}")  # x=10, y=20

# Mehrere Werte ignorieren
data = (1, 2, 3, 4, 5)
first, *_, last = data
print(f"Erstes: {first}, Letztes: {last}")  # Erstes: 1, Letztes: 5

# In Loops
coordinates = [(10, 20, 0), (30, 40, 0), (50, 60, 0)]
for x, y, _ in coordinates:  # z-Koordinate ignorieren
    print(f"({x}, {y})")

# Funktion mit mehreren Rückgabewerten, nur manche nutzen
def get_stats(numbers):
    return min(numbers), max(numbers), sum(numbers), len(numbers)

min_val, max_val, _, _ = get_stats([1, 2, 3, 4, 5])
print(f"Min: {min_val}, Max: {max_val}")
```

---

## Unpacking in der Praxis

### Funktions-Rückgabewerte

```python
def calculate_circle(radius):
    """Berechnet Umfang und Fläche eines Kreises"""
    import math
    circumference = 2 * math.pi * radius
    area = math.pi * radius ** 2
    return circumference, area  # Gibt Tupel zurück

# Beide Werte nutzen
circ, area = calculate_circle(5)
print(f"Umfang: {circ:.2f}, Fläche: {area:.2f}")

# Nur einen Wert nutzen
_, area = calculate_circle(5)
print(f"Fläche: {area:.2f}")

# Mehrere Statistiken
def analyze_data(numbers):
    return {
        "min": min(numbers),
        "max": max(numbers),
        "avg": sum(numbers) / len(numbers)
    }

# Als Tupel Items
data = [1, 2, 3, 4, 5]
stats = analyze_data(data)

# Dictionary Unpacking in Loop
for key, value in stats.items():
    print(f"{key}: {value}")
```

### Variable Tauschen (Swap)

```python
# Klassischer Weg (andere Sprachen)
a = 1
b = 2
temp = a
a = b
b = temp
print(f"a={a}, b={b}")  # a=2, b=1

# Python-Weg mit Tuple Unpacking - elegant!
a = 1
b = 2
a, b = b, a
print(f"a={a}, b={b}")  # a=2, b=1

# Mehrere Variablen gleichzeitig tauschen
x, y, z = 1, 2, 3
x, y, z = z, y, x
print(f"x={x}, y={y}, z={z}")  # x=3, y=2, z=1

# Zirkulär rotieren
a, b, c = 1, 2, 3
a, b, c = b, c, a
print(f"a={a}, b={b}, c={c}")  # a=2, b=3, c=1
```

### Mehrfache Zuweisungen

```python
# Mehrere Variablen gleicher Wert
a = b = c = 0
print(f"a={a}, b={b}, c={c}")  # a=0, b=0, c=0

# Mehrere Variablen verschiedene Werte
a, b, c = 1, 2, 3
print(f"a={a}, b={b}, c={c}")  # a=1, b=2, c=3

# Mit Berechnung
x, y = y + 1, x + 1  # Rechte Seite wird zuerst ausgewertet!

# Fibonacci-Sequenz elegant
a, b = 0, 1
for _ in range(10):
    print(a, end=" ")
    a, b = b, a + b
# 0 1 1 2 3 5 8 13 21 34
```

### Enumeration mit Unpacking

```python
# enumerate() gibt (index, value) Tupel zurück
fruits = ["Apfel", "Banane", "Orange"]

for i, fruit in enumerate(fruits):
    print(f"{i}: {fruit}")
# 0: Apfel
# 1: Banane
# 2: Orange

# Mit Start-Index
for i, fruit in enumerate(fruits, start=1):
    print(f"{i}. {fruit}")
# 1. Apfel
# 2. Banane
# 3. Orange

# Nur Index oder nur Wert
for i, _ in enumerate(fruits):
    print(f"Index: {i}")

for _, fruit in enumerate(fruits):
    print(f"Frucht: {fruit}")
```

### Dictionary Items Unpacking

```python
# items() gibt (key, value) Tupel zurück
person = {
    "name": "Anna",
    "age": 25,
    "city": "Berlin"
}

# Unpacking in Loop
for key, value in person.items():
    print(f"{key}: {value}")
# name: Anna
# age: 25
# city: Berlin

# Mehrere Dictionaries kombinieren
dict1 = {"a": 1, "b": 2}
dict2 = {"c": 3, "d": 4}

combined = {}
for key, value in dict1.items():
    combined[key] = value
for key, value in dict2.items():
    combined[key] = value

print(combined)  # {'a': 1, 'b': 2, 'c': 3, 'd': 4}

# Oder einfacher mit ** Unpacking (Kapitel Dictionaries)
combined = {**dict1, **dict2}
```

### Zip mit Unpacking

```python
# zip() erstellt Tupel aus parallelen Listen
names = ["Anna", "Bob", "Charlie"]
ages = [25, 30, 28]
cities = ["Berlin", "München", "Hamburg"]

# Unpacking beim Iterieren
for name, age, city in zip(names, ages, cities):
    print(f"{name} ({age}) aus {city}")
# Anna (25) aus Berlin
# Bob (30) aus München
# Charlie (28) aus Hamburg

# Zip zum "Transponieren"
matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]

# Spalten extrahieren
col1, col2, col3 = zip(*matrix)
print(col1)  # (1, 4, 7)
print(col2)  # (2, 5, 8)
print(col3)  # (3, 6, 9)

# Zurück zu Listen
columns = [list(col) for col in zip(*matrix)]
print(columns)  # [[1, 4, 7], [2, 5, 8], [3, 6, 9]]
```

---

## Erweiterte Patterns

### Unpacking in Funktionsparametern

```python
def print_point(x, y):
    print(f"Punkt: ({x}, {y})")

# Tupel als einzelne Argumente übergeben
point = (10, 20)
print_point(*point)  # Unpacking mit *

# Funktioniert mit allen Sequenzen
coords = [30, 40]
print_point(*coords)

# Mehrere Argumente
def draw_rectangle(x, y, width, height):
    print(f"Rechteck bei ({x}, {y}) mit {width}×{height}")

rect = (10, 20, 50, 30)
draw_rectangle(*rect)
```

### Unpacking in List/Dict Comprehensions

```python
# Liste von Tupeln
points = [(1, 2), (3, 4), (5, 6)]

# Unpacking in Comprehension
x_coords = [x for x, y in points]
print(x_coords)  # [1, 3, 5]

y_coords = [y for x, y in points]
print(y_coords)  # [2, 4, 6]

# Mit Bedingung
positive_x = [x for x, y in points if x > 2]
print(positive_x)  # [3, 5]

# Dictionary aus Tupel-Liste
pairs = [("a", 1), ("b", 2), ("c", 3)]
d = {key: value for key, value in pairs}
print(d)  # {'a': 1, 'b': 2, 'c': 3}

# Komplexeres Beispiel
data = [
    ("Anna", 25, "Berlin"),
    ("Bob", 30, "München"),
    ("Charlie", 28, "Hamburg")
]

# Dictionary mit Name als Key
people = {name: {"age": age, "city": city} 
          for name, age, city in data}
print(people)
# {'Anna': {'age': 25, 'city': 'Berlin'}, ...}
```

### Unpacking mit Defaults

```python
# Extended Unpacking mit Defaults
def get_config():
    # Gibt 2-4 Werte zurück
    return ("localhost", 8080)  # Nur Host und Port

# Mit Defaults für optionale Werte
def parse_config(config):
    if len(config) == 2:
        host, port = config
        user, password = "admin", "secret"
    elif len(config) == 4:
        host, port, user, password = config
    else:
        raise ValueError("Invalid config")
    
    return host, port, user, password

config1 = ("localhost", 8080)
config2 = ("server.com", 443, "user", "pass123")

print(parse_config(config1))
print(parse_config(config2))

# Oder mit Extended Unpacking
def flexible_parse(config):
    host, port, *credentials = config
    if credentials:
        user, password = credentials
    else:
        user, password = "admin", "secret"
    
    return host, port, user, password
```

---

## Häufige Fallstricke

### ⚠️ Fallstrick 1: Vergessenes Komma bei Ein-Element Tupel

```python
# ❌ Kein Tupel!
single = (42)
print(type(single))  # <class 'int'>

try:
    value, = single  # Versuche zu unpacken
except TypeError as e:
    print(f"Fehler: {e}")  # cannot unpack non-iterable int object

# ✅ Richtiges Ein-Element Tupel
single = (42,)  # Komma!
value, = single
print(value)  # 42
```

### ⚠️ Fallstrick 2: Falsche Anzahl Variablen

```python
# ❌ Zu wenige Variablen
try:
    x, y = (1, 2, 3)
except ValueError as e:
    print(f"Fehler: {e}")  # too many values to unpack

# ✅ Extended Unpacking verwenden
x, y, *rest = (1, 2, 3)
print(f"x={x}, y={y}, rest={rest}")  # x=1, y=2, rest=[3]

# Oder explizit ignorieren
x, y, _ = (1, 2, 3)
```

### ⚠️ Fallstrick 3: Reihenfolge bei Extended Unpacking

```python
# * sammelt in eine LISTE, nicht Tupel!
first, *rest = (1, 2, 3)
print(type(rest))  # <class 'list'>  ← Liste!

# Wenn du Tupel brauchst:
first, *rest = (1, 2, 3)
rest = tuple(rest)
print(type(rest))  # <class 'tuple'>

# Oder direkt als Tupel:
first, rest = (1, 2, 3)[0], (1, 2, 3)[1:]
```

### ⚠️ Fallstrick 4: Nested Unpacking Tiefe

```python
# ❌ Struktur muss exakt passen
nested = ((1, 2), 3, 4)
try:
    (a, b), (c, d) = nested
except ValueError as e:
    print(f"Fehler: {e}")  # too many values to unpack

# ✅ Struktur anpassen
(a, b), c, d = nested
print(f"a={a}, b={b}, c={c}, d={d}")
```

---

## Best Practices

### ✅ DO - Gute Patterns

```python
# 1. Tuple Unpacking für mehrere Rückgabewerte
def get_dimensions():
    return 1920, 1080

width, height = get_dimensions()

# 2. Variable Swap ohne temp
a, b = b, a

# 3. Extended Unpacking für flexible Funktionen
def print_scores(name, *scores):
    avg = sum(scores) / len(scores) if scores else 0
    print(f"{name}: {avg:.1f}")

print_scores("Anna", 85, 90, 78)

# 4. Enumerate mit Unpacking
for i, item in enumerate(items, start=1):
    print(f"{i}. {item}")

# 5. Dictionary Items
for key, value in my_dict.items():
    process(key, value)

# 6. Ignorieren mit _
_, y, _ = get_3d_point()  # Nur y-Koordinate

# 7. List-of-tuples Comprehension
coords = [(x, y) for x in range(3) for y in range(3)]
```

### ❌ DON'T - Anti-Patterns

```python
# 1. Vermeiden: Einzelzugriff wenn Unpacking möglich
# ❌ Umständlich
point = (10, 20)
x = point[0]
y = point[1]

# ✅ Besser
x, y = point

# 2. Vermeiden: Temp-Variable für Swap
# ❌ Unnötig
temp = a
a = b
b = temp

# ✅ Pythonisch
a, b = b, a

# 3. Vermeiden: Index statt Unpacking in Loops
# ❌ Nicht pythonisch
for i in range(len(points)):
    x = points[i][0]
    y = points[i][1]
    process(x, y)

# ✅ Pythonisch
for x, y in points:
    process(x, y)

# 4. Vermeiden: Zu viele Ebenen
# ❌ Schwer lesbar
((a, (b, c)), (d, (e, f))) = complex_nested

# ✅ Schrittweise unpacken
outer1, outer2 = complex_nested
a, inner1 = outer1
b, c = inner1
# ...
```

---

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [Unpacking Assignments](https://docs.python.org/3/tutorial/datastructures.html#tuples-and-sequences) - Tutorial zu Unpacking
- [PEP 3132](https://www.python.org/dev/peps/pep-3132/) - Extended Iterable Unpacking
- [Assignment Statements](https://docs.python.org/3/reference/simple_stmts.html#assignment-statements) - Formale Spezifikation

---

## Verwandte Themen

- [[02_Sequenzen/02_Tupel/01_Tuple Basics|Tuple Basics]] - Grundlagen zu Tupeln
- [[02_Sequenzen/02_Tupel/03_Named Tuples|Named Tuples]] - Tupel mit benannten Feldern
- [[03_Mappings_und_Sets/01_Dictionaries/01_Dict Basics|Dict Basics]] - Dictionary Unpacking
- [[02_Sequenzen/04_Comprehensions/01_List Comprehensions|List Comprehensions]] - Unpacking in Comprehensions

---

← [[02_Sequenzen/02_Tupel/01_Tuple Basics|Tuple Basics]] | [[INDEX|📑 Index]] | [[02_Sequenzen/02_Tupel/03_Named Tuples|Named Tuples]] →