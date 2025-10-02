---
tags: [python, datenstrukturen, slicing, advanced]
created: 2025-10-01
---

← [[02_Sequenzen/03_Slicing/01_Slicing Grundlagen|Slicing Grundlagen]] | [[INDEX|📑 Index]] | [[02_Sequenzen/03_Slicing/03_Slice Objekte|Slice Objekte]] →

# Erweiterte Slicing Techniken

> [!tip] Lernziel
> Nach dieser Seite beherrschst du fortgeschrittene Slicing-Techniken: Slice Assignment, Deletion, mehrdimensionales Slicing und elegante Patterns für reale Probleme.

## Slice Assignment - Slices zuweisen

### Grundlegendes Slice Assignment

Bei **veränderlichen** Sequenzen (Listen) können Slices nicht nur gelesen, sondern auch **zugewiesen** werden. Dies ist eine mächtige Technik zur In-Place-Modifikation.

```python
# Einfaches Assignment
numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

# Slice ersetzen
numbers[2:5] = [20, 30, 40]
print(numbers)  # [0, 1, 20, 30, 40, 5, 6, 7, 8, 9]

# Slice mit anderer Länge ersetzen
numbers = [0, 1, 2, 3, 4, 5]
numbers[2:4] = [100]  # 2 Elemente durch 1 ersetzen
print(numbers)  # [0, 1, 100, 4, 5]

numbers[1:2] = [10, 20, 30]  # 1 Element durch 3 ersetzen
print(numbers)  # [0, 10, 20, 30, 100, 4, 5]
```

**⚠️ Wichtig:** Rechte Seite muss **iterable** sein!

```python
numbers = [1, 2, 3, 4, 5]

# ❌ Falsch: Einzelner Wert
try:
    numbers[1:3] = 99  # TypeError
except TypeError as e:
    print(f"Fehler: {e}")  # can only assign an iterable

# ✅ Richtig: Als Liste oder Tupel
numbers[1:3] = [99]  # OK
print(numbers)  # [1, 99, 4, 5]

# Auch String funktioniert (ist iterable)
numbers[2:3] = "AB"  # 'A' und 'B' als einzelne Elemente
print(numbers)  # [1, 99, 'A', 'B', 5]
```

### Einfügen mit leerem Slice

```python
numbers = [1, 2, 3, 4, 5]

# An Position einfügen (leerer Slice)
numbers[2:2] = [10, 20, 30]
print(numbers)  # [1, 2, 10, 20, 30, 3, 4, 5]

# Am Anfang einfügen
numbers[0:0] = [0]
print(numbers)  # [0, 1, 2, 10, 20, 30, 3, 4, 5]

# Am Ende einfügen
numbers[len(numbers):] = [100, 200]
print(numbers)  # [0, 1, 2, 10, 20, 30, 3, 4, 5, 100, 200]

# Äquivalent zu append, aber generischer
numbers[len(numbers):len(numbers)] = [300]
print(numbers[-1])  # 300
```

### Löschen mit leerem Assignment

```python
numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

# Slice durch leere Liste ersetzen = Löschen
numbers[2:5] = []
print(numbers)  # [0, 1, 5, 6, 7, 8, 9]

# Erste n Elemente löschen
numbers[:2] = []
print(numbers)  # [5, 6, 7, 8, 9]

# Letzte n Elemente löschen
numbers[-2:] = []
print(numbers)  # [5, 6, 7]

# Äquivalent zu del (siehe unten)
```

---

## Slice Deletion - Slices löschen

### Mit del Statement

```python
numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

# Slice löschen mit del
del numbers[2:5]
print(numbers)  # [0, 1, 5, 6, 7, 8, 9]

# Erste n Elemente
del numbers[:2]
print(numbers)  # [5, 6, 7, 8, 9]

# Letzte n Elemente
del numbers[-2:]
print(numbers)  # [5, 6, 7]

# Jedes zweite Element löschen
numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
del numbers[::2]
print(numbers)  # [1, 3, 5, 7, 9]

# Mit negativem Step
numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
del numbers[::-2]  # Jedes zweite von hinten
print(numbers)  # [0, 2, 4, 6, 8]
```

### del vs Assignment mit []

```python
numbers = [0, 1, 2, 3, 4, 5]

# Beide sind äquivalent
numbers1 = numbers.copy()
numbers2 = numbers.copy()

del numbers1[2:4]      # Methode 1
numbers2[2:4] = []     # Methode 2

print(numbers1)  # [0, 1, 4, 5]
print(numbers2)  # [0, 1, 4, 5]

# del ist expliziter und klarer
```

---

## Slice Assignment mit Step

### Extended Slices mit Step

```python
# WICHTIG: Bei step != 1 muss die Länge übereinstimmen!

numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

# Jedes zweite Element ersetzen
# Slice hat 5 Elemente (0, 2, 4, 6, 8)
# Neue Werte müssen auch 5 sein!
numbers[::2] = [10, 20, 30, 40, 50]
print(numbers)  # [10, 1, 20, 3, 30, 5, 40, 7, 50, 9]

# ❌ Falsche Länge
numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
try:
    numbers[::2] = [100, 200]  # 5 Positionen, aber nur 2 Werte
except ValueError as e:
    print(f"Fehler: {e}")  # attempt to assign sequence of size 2 to extended slice of size 5
```

### Praktische Anwendungen

```python
# Gerade/Ungerade Indizes separat setzen
numbers = [0] * 10

# Gerade Indizes
numbers[::2] = [0, 2, 4, 6, 8]
print(numbers)  # [0, 0, 2, 0, 4, 0, 6, 0, 8, 0]

# Ungerade Indizes
numbers[1::2] = [1, 3, 5, 7, 9]
print(numbers)  # [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

# RGB-Werte in Pixel-Array setzen
pixels = [0] * 12  # 4 Pixel, je 3 Werte (RGB)

# Alle Rot-Werte setzen
pixels[0::3] = [255, 128, 64, 32]
print(pixels)  # [255, 0, 0, 128, 0, 0, 64, 0, 0, 32, 0, 0]

# Alle Grün-Werte setzen
pixels[1::3] = [0, 255, 0, 255]
print(pixels)  # [255, 0, 0, 128, 255, 0, 64, 0, 0, 32, 255, 0]

# Umkehren mit Assignment
numbers = [1, 2, 3, 4, 5]
numbers[:] = numbers[::-1]  # In-place umkehren
print(numbers)  # [5, 4, 3, 2, 1]
```

---

## Mehrdimensionales Slicing

### Slicing von Nested Lists

```python
# 2D-Matrix (Liste von Listen)
matrix = [
    [1, 2, 3, 4],
    [5, 6, 7, 8],
    [9, 10, 11, 12]
]

# Zeilen slicen
first_two_rows = matrix[:2]
print(first_two_rows)  # [[1, 2, 3, 4], [5, 6, 7, 8]]

# Spalten extrahieren (etwas umständlich)
first_col = [row[0] for row in matrix]
print(first_col)  # [1, 5, 9]

# Submatrix
submatrix = [row[1:3] for row in matrix[:2]]
print(submatrix)  # [[2, 3], [6, 7]]

# Oder mit Slicing in zwei Schritten
rows = matrix[:2]        # Erste 2 Zeilen
submatrix = [row[1:3] for row in rows]  # Spalten 1-2
```

### Matrix-Operationen mit Slicing

```python
# Zeile ändern
matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]

matrix[1] = [40, 50, 60]
print(matrix)
# [[1, 2, 3], [40, 50, 60], [7, 8, 9]]

# Spalte ändern (alle Zeilen durchgehen)
for row in matrix:
    row[1] = 0

print(matrix)
# [[1, 0, 3], [40, 0, 60], [7, 0, 9]]

# Zeilen tauschen
matrix[0], matrix[2] = matrix[2], matrix[0]
print(matrix)
# [[7, 0, 9], [40, 0, 60], [1, 0, 3]]

# Diagonale extrahieren
diagonal = [matrix[i][i] for i in range(len(matrix))]
print(diagonal)  # [7, 0, 3]

# Diagonale setzen
for i in range(len(matrix)):
    matrix[i][i] = 1

print(matrix)
# [[1, 0, 9], [40, 1, 60], [1, 0, 1]]
```

---

## Fortgeschrittene Patterns

### Pattern 1: Circular Rotation

```python
def rotate_list(lst, n):
    """Rotiert Liste um n Positionen nach rechts"""
    n = n % len(lst)  # Handle n > len(lst)
    return lst[-n:] + lst[:-n]

numbers = [1, 2, 3, 4, 5]
print(rotate_list(numbers, 2))   # [4, 5, 1, 2, 3]
print(rotate_list(numbers, -2))  # [3, 4, 5, 1, 2]
print(rotate_list(numbers, 7))   # [4, 5, 1, 2, 3] (7 % 5 = 2)

# In-place mit Slice Assignment
def rotate_inplace(lst, n):
    """Rotiert Liste in-place"""
    n = n % len(lst)
    lst[:] = lst[-n:] + lst[:-n]

numbers = [1, 2, 3, 4, 5]
rotate_inplace(numbers, 2)
print(numbers)  # [4, 5, 1, 2, 3]
```

### Pattern 2: Interleaving (Verzahnen)

```python
def interleave(lst1, lst2):
    """Verzahnt zwei Listen"""
    result = []
    # Verwende zip für parallele Iteration
    for a, b in zip(lst1, lst2):
        result.extend([a, b])
    
    # Rest der längeren Liste anhängen
    if len(lst1) > len(lst2):
        result.extend(lst1[len(lst2):])
    else:
        result.extend(lst2[len(lst1):])
    
    return result

a = [1, 2, 3]
b = [10, 20, 30, 40]
print(interleave(a, b))  # [1, 10, 2, 20, 3, 30, 40]

# Mit Slicing und Assignment
def interleave_inplace(lst, other):
    """Verzahnt other in lst (in-place)"""
    for i, val in enumerate(other):
        lst[i*2:i*2] = [val]  # Einfügen bei geraden Positionen

numbers = [1, 2, 3]
interleave_inplace(numbers, [10, 20, 30])
print(numbers)  # [10, 1, 20, 2, 30, 3]
```

### Pattern 3: Sparse Update

```python
def sparse_update(lst, indices, values):
    """Aktualisiert Liste an spezifischen Indizes"""
    for idx, val in zip(indices, values):
        lst[idx] = val

numbers = [0] * 10
sparse_update(numbers, [1, 3, 5, 7], [10, 30, 50, 70])
print(numbers)  # [0, 10, 0, 30, 0, 50, 0, 70, 0, 0]

# Mit Slice für aufeinanderfolgende Indizes
def update_range(lst, start, values):
    """Aktualisiert aufeinanderfolgende Elemente"""
    lst[start:start+len(values)] = values

numbers = [0] * 10
update_range(numbers, 3, [100, 200, 300])
print(numbers)  # [0, 0, 0, 100, 200, 300, 0, 0, 0, 0]
```

### Pattern 4: Window Operations

```python
def apply_window_operation(lst, window_size, operation):
    """Wendet Operation auf gleitende Fenster an"""
    result = []
    for i in range(len(lst) - window_size + 1):
        window = lst[i:i + window_size]
        result.append(operation(window))
    return result

# Gleitender Durchschnitt
numbers = [1, 2, 3, 4, 5, 6, 7, 8]
averages = apply_window_operation(numbers, 3, lambda w: sum(w) / len(w))
print(averages)  # [2.0, 3.0, 4.0, 5.0, 6.0, 7.0]

# Maximum in Fenster
maxima = apply_window_operation(numbers, 3, max)
print(maxima)  # [3, 4, 5, 6, 7, 8]

# Custom Operation
def range_in_window(window):
    return max(window) - min(window)

ranges = apply_window_operation(numbers, 3, range_in_window)
print(ranges)  # [2, 2, 2, 2, 2, 2]
```

### Pattern 5: Partition (Teilen)

```python
def partition(lst, predicate):
    """Teilt Liste in zwei: Elemente die predicate erfüllen und nicht"""
    true_part = [x for x in lst if predicate(x)]
    false_part = [x for x in lst if not predicate(x)]
    return true_part, false_part

numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
evens, odds = partition(numbers, lambda x: x % 2 == 0)
print(f"Gerade: {evens}")  # [2, 4, 6, 8, 10]
print(f"Ungerade: {odds}")  # [1, 3, 5, 7, 9]

# In-place Partition (Dutch National Flag Algorithm)
def partition_inplace(lst, pivot):
    """Partitioniert in-place: Elemente < pivot, == pivot, > pivot"""
    i, j, k = 0, 0, len(lst) - 1
    
    while j <= k:
        if lst[j] < pivot:
            lst[i], lst[j] = lst[j], lst[i]
            i += 1
            j += 1
        elif lst[j] > pivot:
            lst[j], lst[k] = lst[k], lst[j]
            k -= 1
        else:
            j += 1

numbers = [3, 5, 2, 7, 5, 1, 5, 9, 4]
partition_inplace(numbers, 5)
print(numbers)  # [3, 2, 1, 4, 5, 5, 5, 9, 7] (< 5, == 5, > 5)
```

---

## Slicing mit benutzerdefinierten Klassen

### __getitem__ und __setitem__ implementieren

```python
class CustomList:
    """Benutzerdefinierte Liste mit Slicing-Support"""
    
    def __init__(self, data):
        self._data = list(data)
    
    def __getitem__(self, key):
        """Ermöglicht Indexierung und Slicing"""
        if isinstance(key, slice):
            # Slice-Objekt
            return CustomList(self._data[key])
        else:
            # Einzelner Index
            return self._data[key]
    
    def __setitem__(self, key, value):
        """Ermöglicht Assignment"""
        if isinstance(key, slice):
            self._data[key] = value
        else:
            self._data[key] = value
    
    def __delitem__(self, key):
        """Ermöglicht Deletion"""
        del self._data[key]
    
    def __len__(self):
        return len(self._data)
    
    def __repr__(self):
        return f"CustomList({self._data})"

# Verwendung
cl = CustomList([1, 2, 3, 4, 5])

# Slicing funktioniert
print(cl[1:4])      # CustomList([2, 3, 4])

# Assignment funktioniert
cl[1:3] = [20, 30]
print(cl)           # CustomList([1, 20, 30, 4, 5])

# Deletion funktioniert
del cl[2:4]
print(cl)           # CustomList([1, 20, 5])
```

---

## Performance-Überlegungen

### Slice Assignment Performance

```python
import time

# Große Liste
big_list = list(range(1000000))

# Methode 1: Slice Assignment (in-place)
list1 = big_list.copy()
start = time.perf_counter()
list1[::2] = [0] * 500000  # Jedes zweite Element
time1 = time.perf_counter() - start

# Methode 2: List Comprehension (neue Liste)
start = time.perf_counter()
list2 = [0 if i % 2 == 0 else big_list[i] for i in range(len(big_list))]
time2 = time.perf_counter() - start

print(f"Slice Assignment: {time1:.4f}s")
print(f"List Comprehension: {time2:.4f}s")
print(f"Faktor: {time2/time1:.2f}x")

# Slice Assignment ist oft schneller für In-Place-Operationen
```

### Wann was verwenden?

```python
# ✅ Slice Assignment: In-place Modifikation
numbers = [1, 2, 3, 4, 5]
numbers[1:4] = [20, 30, 40]  # Modifiziert Original

# ✅ Slicing: Neue Liste erstellen
numbers = [1, 2, 3, 4, 5]
subset = numbers[1:4]  # Neue Liste, Original unverändert

# ✅ List Comprehension: Komplexe Transformation
numbers = [1, 2, 3, 4, 5]
transformed = [x * 2 if x % 2 == 0 else x for x in numbers]

# ❌ Vermeiden: Slice Assignment für komplexe Logik
# Wird unleserlich und fehleranfällig
```

---

## Häufige Fallstricke

### ⚠️ Fallstrick 1: Extended Slice mit falscher Länge

```python
numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

# ❌ Länge stimmt nicht überein
try:
    numbers[::2] = [0, 1]  # 5 Positionen, aber nur 2 Werte
except ValueError as e:
    print(f"Fehler: {e}")

# ✅ Richtige Länge
numbers[::2] = [0, 10, 20, 30, 40]  # 5 Werte
print(numbers)  # [0, 1, 10, 3, 20, 5, 30, 7, 40, 9]
```

### ⚠️ Fallstrick 2: Shallow Copy bei verschachtelten Listen

```python
# Slice Assignment kopiert nur oberste Ebene
matrix = [[1, 2], [3, 4], [5, 6]]
matrix2 = matrix[:]  # Shallow Copy

# Äußere Liste ist Kopie
matrix2.append([7, 8])
print(len(matrix))   # 3
print(len(matrix2))  # 4

# ABER: Innere Listen sind Referenzen!
matrix2[0][0] = 999
print(matrix)   # [[999, 2], [3, 4], [5, 6]] - auch geändert!
print(matrix2)  # [[999, 2], [3, 4], [5, 6], [7, 8]]

# ✅ Deep Copy verwenden
import copy
matrix3 = copy.deepcopy(matrix[:])
```

### ⚠️ Fallstrick 3: [:] vs [:]=[...]

```python
numbers = [1, 2, 3, 4, 5]
reference = numbers

# Variante 1: Neuzuweisung
numbers = numbers[2:4]  # Erstellt NEUE Liste
print(numbers)    # [3, 4]
print(reference)  # [1, 2, 3, 4, 5] - Original unverändert

# Variante 2: Slice Assignment
numbers = [1, 2, 3, 4, 5]
reference = numbers
numbers[:] = numbers[2:4]  # Ändert SELBE Liste
print(numbers)    # [3, 4]
print(reference)  # [3, 4] - auch geändert!
```

---

## Best Practices

### ✅ DO - Gute Patterns

```python
# 1. [:] für vollständige Kopie
original = [1, 2, 3]
copy = original[:]

# 2. Slice Assignment für In-Place-Modifikation
numbers = [1, 2, 3, 4, 5]
numbers[1:4] = [20, 30, 40]

# 3. del für klare Intention beim Löschen
del numbers[2:4]

# 4. Negative Indizes für Ende der Liste
last_three = numbers[-3:]

# 5. Step für Muster
evens = numbers[::2]
reversed = numbers[::-1]
```

### ❌ DON'T - Anti-Patterns

```python
# 1. Vermeiden: Komplexes Slice Assignment
# ❌ Schwer zu lesen
numbers[::2] = [compute(x) for x in numbers[::2]]

# ✅ Klarer mit Loop
for i in range(0, len(numbers), 2):
    numbers[i] = compute(numbers[i])

# 2. Vermeiden: Zu viele verschachtelte Slices
# ❌ Unleserlich
result = matrix[:2][1][3:5]

# ✅ Schrittweise
rows = matrix[:2]
row = rows[1]
result = row[3:5]

# 3. Vermeiden: Slice Assignment mit Step ohne Grund
# ❌ Unnötig komplex
numbers[1:8:3] = [10, 20, 30]

# ✅ Explizit
numbers[1] = 10
numbers[4] = 20
numbers[7] = 30
```

---

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [Assignment Statements](https://docs.python.org/3/reference/simple_stmts.html#assignment-statements) - Slice Assignment
- [Data Model](https://docs.python.org/3/reference/datamodel.html#object.__getitem__) - `__getitem__`, `__setitem__`
- [Mutable Sequence Types](https://docs.python.org/3/library/stdtypes.html#mutable-sequence-types) - Alle Operationen

---

## Verwandte Themen

- [[02_Sequenzen/03_Slicing/01_Slicing Grundlagen|Slicing Grundlagen]] - Basis-Slicing
- [[02_Sequenzen/03_Slicing/03_Slice Objekte|Slice Objekte]] - `slice()` Funktion
- [[02_Sequenzen/01_Listen/02_List Methoden|List Methoden]] - Alternative zu Slicing
- [[02_Sequenzen/04_Comprehensions/01_List Comprehensions|List Comprehensions]] - Deklarative Alternative

---

← [[02_Sequenzen/03_Slicing/01_Slicing Grundlagen|Slicing Grundlagen]] | [[INDEX|📑 Index]] | [[02_Sequenzen/03_Slicing/03_Slice Objekte|Slice Objekte]] →