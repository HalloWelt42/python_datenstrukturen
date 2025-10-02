---
tags: [python, datenstrukturen, tuple, sequenzen, immutable]
created: 2025-10-01
---

← [[02_Sequenzen/01_Listen/06_Übungen Listen|Übungen Listen]] | [[INDEX|📑 Index]] | [[02_Sequenzen/02_Tupel/02_Tuple Unpacking|Tuple Unpacking]] →

# Tuple Basics - Unveränderliche Sequenzen

> [!tip] Lernziel
> Nach dieser Seite verstehst du, was Tupel sind, wie sie sich von Listen unterscheiden, warum Unveränderlichkeit wichtig ist und wann du Tupel statt Listen verwenden solltest.

## Was sind Tupel?

**Tupel** (englisch: Tuples) sind **geordnete**, **unveränderliche** (immutable) Sequenzen. Sie sind wie Listen, können aber nach ihrer Erstellung nicht mehr verändert werden.

**Hauptmerkmale:**
- ✅ **Geordnet** - Elemente behalten ihre Position
- ✅ **Unveränderlich** - Können nicht modifiziert werden
- ✅ **Heterogen** - Können verschiedene Datentypen mischen
- ✅ **Indexierbar** - Zugriff über Position (0-basiert)
- ✅ **Iterierbar** - Können in Schleifen durchlaufen werden
- ✅ **Hashbar** - Können als Dictionary-Keys verwendet werden

```python
# Ein einfaches Tupel
coordinates = (3, 4)
rgb_color = (255, 128, 0)
person = ("Anna", 25, "Berlin")

# Tupel sind unveränderlich
try:
    coordinates[0] = 5  # TypeError!
except TypeError as e:
    print(f"Fehler: {e}")  # 'tuple' object does not support item assignment
```

---

## Tuple vs Liste - Der Hauptunterschied

### Vergleich

| Eigenschaft | Liste | Tupel |
|-------------|-------|-------|
| Veränderlich | ✅ Ja (mutable) | ❌ Nein (immutable) |
| Syntax | `[1, 2, 3]` | `(1, 2, 3)` |
| Performance | Langsamer | Schneller |
| Speicher | Mehr | Weniger |
| Methoden | 11 Methoden | 2 Methoden |
| Hashbar | ❌ Nein | ✅ Ja (wenn Elemente hashbar) |
| Als Dict-Key | ❌ Nein | ✅ Ja |
| Verwendung | Homogene Daten | Heterogene Daten |

### Praktischer Vergleich

```python
# Liste - veränderlich
my_list = [1, 2, 3]
my_list[0] = 99       # ✅ Funktioniert
my_list.append(4)     # ✅ Funktioniert
print(my_list)        # [99, 2, 3, 4]

# Tupel - unveränderlich
my_tuple = (1, 2, 3)
# my_tuple[0] = 99    # ❌ TypeError
# my_tuple.append(4)  # ❌ AttributeError
print(my_tuple)       # (1, 2, 3)

# Tupel als Dictionary-Key
locations = {
    (0, 0): "Ursprung",
    (10, 20): "Punkt A",
    (30, 40): "Punkt B"
}
print(locations[(10, 20)])  # "Punkt A"

# Liste kann nicht als Key verwendet werden
try:
    bad_dict = {[1, 2]: "value"}  # TypeError!
except TypeError as e:
    print(f"Fehler: {e}")  # unhashable type: 'list'
```

---

## Syntax und Erstellung

### Literal-Syntax

```python
# Mit Klammern (empfohlen)
coordinates = (3, 4)
rgb = (255, 128, 0)

# Ohne Klammern (funktioniert auch)
point = 10, 20
values = 1, 2, 3, 4, 5

# Ein-Element Tupel: Komma ist ZWINGEND!
single = (42,)    # ✅ Tupel mit einem Element
not_tuple = (42)  # ❌ Nur Integer 42, KEIN Tupel!

print(type(single))    # <class 'tuple'>
print(type(not_tuple)) # <class 'int'>

# Leeres Tupel
empty = ()
also_empty = tuple()
```

**⚠️ WICHTIG: Ein-Element Tupel**
```python
# Ein Komma macht den Unterschied!
single_tuple = (42,)      # Tupel
just_int = (42)           # Integer (Klammern für Reihenfolge)

print(type(single_tuple)) # <class 'tuple'>
print(type(just_int))     # <class 'int'>

# Auch ohne Klammern
also_tuple = 42,
print(type(also_tuple))   # <class 'tuple'>

# Praktisches Beispiel
def return_single_value():
    return (42,)  # Gibt Tupel zurück

def return_just_int():
    return (42)   # Gibt Integer zurück

result1 = return_single_value()
result2 = return_just_int()

print(f"result1: {result1}, type: {type(result1)}")  # (42,), tuple
print(f"result2: {result2}, type: {type(result2)}")  # 42, int
```

### Tuple Constructor

```python
# Aus Iterable erstellen
tuple_from_list = tuple([1, 2, 3])
print(tuple_from_list)  # (1, 2, 3)

tuple_from_string = tuple("Python")
print(tuple_from_string)  # ('P', 'y', 't', 'h', 'o', 'n')

tuple_from_range = tuple(range(5))
print(tuple_from_range)  # (0, 1, 2, 3, 4)

# Aus Generator
tuple_from_gen = tuple(x * 2 for x in range(5))
print(tuple_from_gen)  # (0, 2, 4, 6, 8)

# Leeres Tupel
empty = tuple()
print(empty)  # ()
```

### Verschachtelte Tupel

```python
# Tupel können andere Tupel enthalten
nested = ((1, 2), (3, 4), (5, 6))
print(nested[0])     # (1, 2)
print(nested[0][1])  # 2

# Gemischte Verschachtelung
mixed = (1, [2, 3], (4, 5), "text")
print(mixed)  # (1, [2, 3], (4, 5), 'text')

# ⚠️ ACHTUNG: Innere veränderbare Objekte können modifiziert werden!
mixed_mutable = (1, [2, 3], 4)
mixed_mutable[1].append(99)  # ✅ Funktioniert! Liste ist veränderbar
print(mixed_mutable)  # (1, [2, 3, 99], 4)

# Das Tupel selbst ist unveränderlich, aber die Liste darin nicht!
```

---

## Zugriff auf Elemente

### Indexierung

```python
fruits = ("Apfel", "Banane", "Orange", "Mango", "Kiwi")

# Positiver Index (von vorne)
print(fruits[0])   # 'Apfel'
print(fruits[2])   # 'Orange'

# Negativer Index (von hinten)
print(fruits[-1])  # 'Kiwi' (letztes Element)
print(fruits[-2])  # 'Mango' (vorletztes Element)

# IndexError bei ungültigem Index
try:
    print(fruits[10])  # IndexError
except IndexError as e:
    print(f"Fehler: {e}")
```

### Slicing

```python
numbers = (0, 1, 2, 3, 4, 5, 6, 7, 8, 9)

# Grundlegendes Slicing: [start:stop:step]
print(numbers[2:7])      # (2, 3, 4, 5, 6)
print(numbers[:5])       # (0, 1, 2, 3, 4)
print(numbers[5:])       # (5, 6, 7, 8, 9)
print(numbers[::2])      # (0, 2, 4, 6, 8)
print(numbers[::-1])     # (9, 8, 7, 6, 5, 4, 3, 2, 1, 0)

# Slicing gibt NEUES Tupel zurück
subset = numbers[2:5]
print(type(subset))      # <class 'tuple'>
print(id(numbers) == id(subset))  # False - verschiedene Objekte
```

---

## Tuple-Operationen

### Konkatenation

```python
# Mit + Operator
tuple1 = (1, 2, 3)
tuple2 = (4, 5, 6)
combined = tuple1 + tuple2
print(combined)  # (1, 2, 3, 4, 5, 6)

# Original-Tupel bleiben unverändert
print(tuple1)  # (1, 2, 3)
print(tuple2)  # (4, 5, 6)

# Mehrere Tupel verbinden
result = (1, 2) + (3, 4) + (5, 6)
print(result)  # (1, 2, 3, 4, 5, 6)
```

### Wiederholung

```python
# Mit * Operator
pattern = ("X", "O")
repeated = pattern * 3
print(repeated)  # ('X', 'O', 'X', 'O', 'X', 'O')

# Mehrfach verschachteln
nested = ((1, 2),) * 3
print(nested)  # ((1, 2), (1, 2), (1, 2))

# ⚠️ VORSICHT: Referenzen werden wiederholt!
inner = ([1, 2],)
repeated = inner * 3
print(id(repeated[0]) == id(repeated[1]))  # True - selbe Referenz!

repeated[0][0].append(99)
print(repeated)  # ([1, 2, 99], [1, 2, 99], [1, 2, 99])
```

### Membership-Tests

```python
colors = ("rot", "grün", "blau")

# in und not in
print("rot" in colors)      # True
print("gelb" in colors)     # False
print("gelb" not in colors) # True

# Funktioniert mit allen Typen
numbers = (1, 2, 3, 4, 5)
print(3 in numbers)   # True
print(10 in numbers)  # False

# Teilsequenzen werden NICHT gefunden
text = ("Hallo", "Welt")
print("Hal" in text)  # False - "Hal" ist kein Element
```

---

## Tuple-Methoden

Tupel haben nur **2 Methoden** (im Vergleich zu 11 bei Listen):

### `count()` - Vorkommen zählen

```python
# Signatur: tuple.count(value) -> int

numbers = (1, 2, 3, 2, 4, 2, 5)
count = numbers.count(2)
print(count)  # 3

# Element nicht vorhanden
count = numbers.count(99)
print(count)  # 0

# Funktioniert mit allen Typen
mixed = (1, "text", 1, "text", 1)
print(mixed.count(1))       # 3
print(mixed.count("text"))  # 2

# Zeitkomplexität: O(n)
```

### `index()` - Index finden

```python
# Signatur: tuple.index(value[, start[, stop]]) -> int
# Raises: ValueError wenn nicht gefunden

fruits = ("Apfel", "Banane", "Orange", "Banane", "Mango")

# Einfache Suche
idx = fruits.index("Orange")
print(idx)  # 2

# Mit Start-Position (erstes Vorkommen ab Index 2)
idx = fruits.index("Banane", 2)
print(idx)  # 3

# Mit Start und Stop
idx = fruits.index("Banane", 0, 3)
print(idx)  # 1

# ValueError bei nicht gefundenem Element
try:
    fruits.index("Kiwi")
except ValueError as e:
    print(f"Fehler: {e}")  # 'Kiwi' is not in tuple

# Zeitkomplexität: O(n)
```

**Sichere Verwendung:**
```python
def safe_index(tpl, value, default=-1):
    """Gibt Index zurück oder default"""
    try:
        return tpl.index(value)
    except ValueError:
        return default

fruits = ("Apfel", "Banane", "Orange")
print(safe_index(fruits, "Banane"))  # 1
print(safe_index(fruits, "Kiwi"))    # -1
```

---

## Built-in Functions für Tupel

```python
numbers = (3, 1, 4, 1, 5, 9, 2, 6)

# len() - Länge
print(len(numbers))  # 8

# min() - Kleinstes Element
print(min(numbers))  # 1

# max() - Größtes Element
print(max(numbers))  # 9

# sum() - Summe (nur für numerische Tupel)
print(sum(numbers))  # 31

# sorted() - Sortierte LISTE (nicht Tupel!)
sorted_result = sorted(numbers)
print(sorted_result)       # [1, 1, 2, 3, 4, 5, 6, 9]
print(type(sorted_result)) # <class 'list'>

# Zurück zu Tupel konvertieren
sorted_tuple = tuple(sorted(numbers))
print(sorted_tuple)  # (1, 1, 2, 3, 4, 5, 6, 9)

# any() - Mindestens ein True?
bools = (False, False, True, False)
print(any(bools))  # True

# all() - Alle True?
print(all(bools))  # False

# enumerate() - Index-Wert Paare
for i, value in enumerate(numbers):
    print(f"{i}: {value}", end=" ")
# 0: 3 1: 1 2: 4 3: 1 4: 5 5: 9 6: 2 7: 6
```

---

## Unveränderlichkeit verstehen

### Was bedeutet "immutable"?

```python
# Tupel selbst kann nicht geändert werden
coords = (10, 20)

# ❌ Alle diese Operationen sind verboten:
# coords[0] = 15          # TypeError
# coords.append(30)       # AttributeError
# coords.remove(10)       # AttributeError
# coords.clear()          # AttributeError

# ✅ Aber du kannst neue Tupel erstellen:
new_coords = (15, 20)
print(new_coords)  # (15, 20)

# Oder durch Konkatenation
extended = coords + (30,)
print(extended)  # (10, 20, 30)
```

### Veränderbare Elemente in Tupeln

```python
# Tupel mit veränderbaren Elementen
data = (1, [2, 3], 4)

# Das Tupel selbst ist unveränderlich
# data[0] = 99  # TypeError

# Aber die Liste DARIN ist veränderbar!
data[1].append(99)  # ✅ Funktioniert
print(data)  # (1, [2, 3, 99], 4)

# Das Tupel-Objekt ändert sich nicht, aber sein Inhalt schon
# Dies ist ein wichtiger Unterschied!

# Visualisierung:
# data ──→ Tupel(unveränderlich)
#          ├─ 1 (unveränderlich)
#          ├─ Liste(veränderlich) ──→ [2, 3, 99]
#          └─ 4 (unveränderlich)
```

### Hashbarkeit

```python
# Tupel mit nur unveränderlichen Elementen ist hashbar
hashable_tuple = (1, 2, "text")
print(hash(hashable_tuple))  # z.B. 4138606995446258907

# Kann als Dictionary-Key verwendet werden
cache = {
    (1, 2): "Ergebnis A",
    (3, 4): "Ergebnis B"
}
print(cache[(1, 2)])  # "Ergebnis A"

# Tupel mit veränderbaren Elementen ist NICHT hashbar
unhashable_tuple = (1, [2, 3], 4)
try:
    hash(unhashable_tuple)  # TypeError
except TypeError as e:
    print(f"Fehler: {e}")  # unhashable type: 'list'

# Kann NICHT als Dictionary-Key verwendet werden
try:
    bad_cache = {(1, [2, 3]): "value"}  # TypeError
except TypeError as e:
    print(f"Fehler: {e}")
```

---

## Praktische Anwendungen

### Anwendungsfall 1: Funktions-Rückgabewerte

```python
def get_min_max(numbers):
    """Gibt Minimum und Maximum zurück"""
    return min(numbers), max(numbers)  # Automatisch Tupel!

result = get_min_max([3, 1, 4, 1, 5, 9])
print(result)  # (1, 9)
print(type(result))  # <class 'tuple'>

# Tuple Unpacking (nächstes Kapitel)
min_val, max_val = get_min_max([3, 1, 4, 1, 5, 9])
print(f"Min: {min_val}, Max: {max_val}")  # Min: 1, Max: 9

# Mehrere Werte zurückgeben
def get_stats(numbers):
    return min(numbers), max(numbers), sum(numbers), len(numbers)

min_val, max_val, total, count = get_stats([1, 2, 3, 4, 5])
average = total / count
print(f"Durchschnitt: {average}")  # Durchschnitt: 3.0
```

### Anwendungsfall 2: Koordinaten und Punkte

```python
# 2D-Koordinaten
point = (10, 20)
x, y = point
print(f"x={x}, y={y}")  # x=10, y=20

# 3D-Koordinaten
point_3d = (10, 20, 30)
x, y, z = point_3d

# RGB-Farben
red = (255, 0, 0)
green = (0, 255, 0)
blue = (0, 0, 255)

def blend_colors(color1, color2):
    """Mischt zwei RGB-Farben"""
    return tuple((c1 + c2) // 2 for c1, c2 in zip(color1, color2))

purple = blend_colors(red, blue)
print(purple)  # (127, 0, 127)
```

### Anwendungsfall 3: Dictionary-Keys

```python
# Koordinaten als Keys
grid = {
    (0, 0): "Start",
    (0, 1): "Weg",
    (1, 0): "Weg",
    (1, 1): "Ziel"
}

# Zugriff
print(grid[(0, 0)])  # "Start"

# Iteration
for coords, value in grid.items():
    x, y = coords
    print(f"Position ({x}, {y}): {value}")

# Praktisches Beispiel: Schachbrett
def init_chess_board():
    """Initialisiert Schachbrett mit Koordinaten"""
    board = {}
    
    # Weiße Figuren
    board[(0, 0)] = "Turm_w"
    board[(0, 1)] = "Springer_w"
    # ... weitere Figuren
    
    # Schwarze Figuren
    board[(7, 0)] = "Turm_s"
    board[(7, 1)] = "Springer_s"
    # ... weitere Figuren
    
    return board

# Caching von Funktionsergebnissen
def fibonacci_cached():
    cache = {}
    
    def fib(n):
        if n in cache:
            return cache[n]
        
        if n <= 1:
            return n
        
        result = fib(n-1) + fib(n-2)
        cache[n] = result  # n ist hashbar (int)
        return result
    
    return fib

fib = fibonacci_cached()
print(fib(10))  # 55
```

### Anwendungsfall 4: Konstante Daten

```python
# Tupel für unveränderliche Konfiguration
DAYS_OF_WEEK = ("Montag", "Dienstag", "Mittwoch", "Donnerstag", 
                "Freitag", "Samstag", "Sonntag")

MONTHS = ("Januar", "Februar", "März", "April", "Mai", "Juni",
          "Juli", "August", "September", "Oktober", "November", "Dezember")

# RGB-Farben-Palette
COLORS = {
    "RED": (255, 0, 0),
    "GREEN": (0, 255, 0),
    "BLUE": (0, 0, 255),
    "YELLOW": (255, 255, 0),
    "CYAN": (0, 255, 255),
    "MAGENTA": (255, 0, 255)
}

# HTTP-Statuscodes
HTTP_STATUS = {
    200: ("OK", "Die Anfrage war erfolgreich"),
    404: ("Not Found", "Die Ressource wurde nicht gefunden"),
    500: ("Internal Server Error", "Interner Serverfehler")
}

status_code = 404
message, description = HTTP_STATUS[status_code]
print(f"{status_code} {message}: {description}")
```

---

## Warum Tupel verwenden?

### Performance-Vorteile

```python
import sys
import time

# Speicherverbrauch
list_obj = [1, 2, 3, 4, 5]
tuple_obj = (1, 2, 3, 4, 5)

print(f"Liste: {sys.getsizeof(list_obj)} Bytes")  # ~104 Bytes
print(f"Tupel: {sys.getsizeof(tuple_obj)} Bytes") # ~80 Bytes

# Tupel brauchen ~23% weniger Speicher!

# Erstellungs-Performance
def benchmark_creation(n=1000000):
    # Liste
    start = time.perf_counter()
    for _ in range(n):
        _ = [1, 2, 3, 4, 5]
    time_list = time.perf_counter() - start
    
    # Tupel
    start = time.perf_counter()
    for _ in range(n):
        _ = (1, 2, 3, 4, 5)
    time_tuple = time.perf_counter() - start
    
    print(f"\nErstellung ({n}x):")
    print(f"Liste: {time_list:.4f}s")
    print(f"Tupel: {time_tuple:.4f}s")
    print(f"Tupel ist {time_list/time_tuple:.2f}x schneller")

benchmark_creation()
```

### Vorteile zusammengefasst

**✅ Verwende Tupel wenn:**
- Daten unveränderlich sein sollen (Schutz vor Änderungen)
- Du Dictionary-Keys brauchst
- Du mehrere Werte zurückgeben willst
- Performance wichtig ist (schneller, weniger Speicher)
- Daten semantisch zusammengehören (z.B. Koordinaten, RGB-Werte)

**✅ Verwende Listen wenn:**
- Daten veränderlich sein müssen
- Du häufig Elemente hinzufügst/entfernst
- Du die vielen List-Methoden brauchst
- Reihenfolge sich ändern kann (sortieren, etc.)

---

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [Tuple - Built-in Types](https://docs.python.org/3/library/stdtypes.html#tuple) - Komplette Referenz
- [Data Structures Tutorial](https://docs.python.org/3/tutorial/datastructures.html#tuples-and-sequences) - Tupel im Tutorial
- [Sequence Types](https://docs.python.org/3/library/stdtypes.html#sequence-types-list-tuple-range) - Vergleich aller Sequenztypen

---

## Verwandte Themen

- [[02_Sequenzen/02_Tupel/02_Tuple Unpacking|Tuple Unpacking]] - Elegantes Entpacken von Tupeln
- [[02_Sequenzen/02_Tupel/03_Named Tuples|Named Tuples]] - Tupel mit benannten Feldern
- [[02_Sequenzen/01_Listen/01_List Basics|List Basics]] - Vergleich mit Listen
- [[01_Grundlagen/01_Einführung/02_Mutability vs Immutability|Mutability]] - Konzept der Unveränderlichkeit

---

← [[02_Sequenzen/01_Listen/06_Übungen Listen|Übungen Listen]] | [[INDEX|📑 Index]] | [[02_Sequenzen/02_Tupel/02_Tuple Unpacking|Tuple Unpacking]] →