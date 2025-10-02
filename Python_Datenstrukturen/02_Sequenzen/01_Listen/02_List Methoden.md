---
tags: [python, datenstrukturen, list, methoden]
created: 2025-10-01
---

← [[02_Sequenzen/01_Listen/01_List Basics|List Basics]] | [[INDEX|📑 Index]] | [[02_Sequenzen/01_Listen/03_List Operationen|List Operationen]] →

# List Methoden - Vollständige Referenz

> [!tip] Lernziel
> Nach dieser Seite kennst du alle verfügbaren List-Methoden, ihre Parameter, Rückgabewerte und typischen Anwendungsfälle. Diese Seite dient als Nachschlagewerk.

## Übersicht aller List-Methoden

Python Listen bieten 11 eingebaute Methoden. Hier eine komplette Übersicht:

| Methode | Beschreibung | Modifiziert Liste | Rückgabe |
|---------|--------------|-------------------|----------|
| `append(x)` | Fügt Element am Ende hinzu | ✅ | None |
| `extend(iterable)` | Fügt alle Elemente hinzu | ✅ | None |
| `insert(i, x)` | Fügt Element an Position ein | ✅ | None |
| `remove(x)` | Entfernt erstes Vorkommen | ✅ | None |
| `pop([i])` | Entfernt und gibt Element zurück | ✅ | Element |
| `clear()` | Entfernt alle Elemente | ✅ | None |
| `index(x[, start[, end]])` | Gibt Index des Elements | ❌ | int |
| `count(x)` | Zählt Vorkommen | ❌ | int |
| `sort(...)` | Sortiert die Liste | ✅ | None |
| `reverse()` | Kehrt Reihenfolge um | ✅ | None |
| `copy()` | Erstellt flache Kopie | ❌ | list |

---

## Elemente hinzufügen

### `append(x)` - Element am Ende hinzufügen

```python
# Signatur: list.append(x) -> None

fruits = ["Apfel", "Banane"]
fruits.append("Orange")
print(fruits)  # ['Apfel', 'Banane', 'Orange']

# Fügt das GESAMTE Argument als EIN Element hinzu
fruits.append(["Mango", "Kiwi"])
print(fruits)  # ['Apfel', 'Banane', 'Orange', ['Mango', 'Kiwi']]

# Zeitkomplexität: O(1) amortisiert
# Speicher: Erweitert bei Bedarf die Kapazität
```

**Typische Anwendung:**
```python
# Liste schrittweise aufbauen
numbers = []
for i in range(5):
    numbers.append(i * 2)
print(numbers)  # [0, 2, 4, 6, 8]

# In Funktion sammeln
def collect_even_numbers(n):
    result = []
    for i in range(n):
        if i % 2 == 0:
            result.append(i)
    return result

print(collect_even_numbers(10))  # [0, 2, 4, 6, 8]
```

---

### `extend(iterable)` - Mehrere Elemente hinzufügen

```python
# Signatur: list.extend(iterable) -> None

numbers = [1, 2, 3]
numbers.extend([4, 5, 6])
print(numbers)  # [1, 2, 3, 4, 5, 6]

# Funktioniert mit jedem Iterable
numbers.extend(range(7, 10))
print(numbers)  # [1, 2, 3, 4, 5, 6, 7, 8, 9]

numbers.extend("ABC")  # String ist iterable!
print(numbers)  # [1, 2, 3, 4, 5, 6, 7, 8, 9, 'A', 'B', 'C']

# Zeitkomplexität: O(k), wobei k = Länge des Iterables
```

**append() vs extend():**
```python
list1 = [1, 2, 3]
list2 = [1, 2, 3]

# append fügt als EIN Element hinzu
list1.append([4, 5])
print(list1)  # [1, 2, 3, [4, 5]] - verschachtelt!

# extend fügt JEDES Element einzeln hinzu
list2.extend([4, 5])
print(list2)  # [1, 2, 3, 4, 5] - flach!
```

**Äquivalente Operationen:**
```python
# Diese drei sind äquivalent:
list1 = [1, 2, 3]
list1.extend([4, 5])

list2 = [1, 2, 3]
list2 += [4, 5]

list3 = [1, 2, 3]
list3 = list3 + [4, 5]  # Erstellt NEUE Liste!

print(list1 == list2 == list3)  # True
```

---

### `insert(i, x)` - Element an Position einfügen

```python
# Signatur: list.insert(i, x) -> None

fruits = ["Apfel", "Orange", "Mango"]
fruits.insert(1, "Banane")  # An Index 1 einfügen
print(fruits)  # ['Apfel', 'Banane', 'Orange', 'Mango']

# Index 0: Am Anfang einfügen
fruits.insert(0, "Erdbeere")
print(fruits)  # ['Erdbeere', 'Apfel', 'Banane', 'Orange', 'Mango']

# Großer Index: Am Ende einfügen
fruits.insert(999, "Kiwi")
print(fruits)  # ['Erdbeere', 'Apfel', 'Banane', 'Orange', 'Mango', 'Kiwi']

# Negativer Index: Von hinten zählen
fruits.insert(-1, "Traube")  # Vor dem letzten Element
print(fruits)  # ['Erdbeere', 'Apfel', 'Banane', 'Orange', 'Mango', 'Traube', 'Kiwi']

# Zeitkomplexität: O(n) - alle nachfolgenden Elemente müssen verschoben werden!
```

**Performance-Vergleich:**
```python
import time

# Ineffizient: Am Anfang einfügen
def insert_at_start(n):
    lst = []
    for i in range(n):
        lst.insert(0, i)  # O(n) bei jedem Aufruf = O(n²) gesamt!
    return lst

# Effizient: Am Ende einfügen
def append_at_end(n):
    lst = []
    for i in range(n):
        lst.append(i)  # O(1) amortisiert = O(n) gesamt
    return lst

# Teste Performance
n = 10000
start = time.perf_counter()
insert_at_start(n)
time1 = time.perf_counter() - start

start = time.perf_counter()
append_at_end(n)
time2 = time.perf_counter() - start

print(f"insert(0): {time1:.4f}s")
print(f"append():  {time2:.4f}s")
print(f"Faktor:    {time1/time2:.1f}x langsamer")
```

---

## Elemente entfernen

### `remove(x)` - Erstes Vorkommen entfernen

```python
# Signatur: list.remove(x) -> None
# Raises: ValueError wenn Element nicht gefunden

numbers = [1, 2, 3, 2, 4, 2, 5]
numbers.remove(2)  # Entfernt ERSTES Vorkommen
print(numbers)  # [1, 3, 2, 4, 2, 5]

# ValueError bei nicht vorhandenem Element
try:
    numbers.remove(999)
except ValueError as e:
    print(f"Fehler: {e}")  # list.remove(x): x not in list

# Zeitkomplexität: O(n) - muss suchen und verschieben
```

**Sichere Verwendung:**
```python
# ✅ Prüfen vor Entfernen
numbers = [1, 2, 3, 4, 5]
if 3 in numbers:
    numbers.remove(3)

# ✅ Fehlerbehandlung
try:
    numbers.remove(999)
except ValueError:
    print("Element nicht gefunden")

# ✅ Alle Vorkommen entfernen
def remove_all(lst, value):
    while value in lst:
        lst.remove(value)

numbers = [1, 2, 3, 2, 4, 2, 5]
remove_all(numbers, 2)
print(numbers)  # [1, 3, 4, 5]

# ✅ Besser: List Comprehension
numbers = [1, 2, 3, 2, 4, 2, 5]
numbers = [x for x in numbers if x != 2]
print(numbers)  # [1, 3, 4, 5]
```

---

### `pop([i])` - Element entfernen und zurückgeben

```python
# Signatur: list.pop([i]) -> element
# Raises: IndexError wenn Liste leer oder Index ungültig

# Ohne Argument: Letztes Element
fruits = ["Apfel", "Banane", "Orange"]
last = fruits.pop()
print(last)    # 'Orange'
print(fruits)  # ['Apfel', 'Banane']

# Mit Index: Spezifisches Element
numbers = [10, 20, 30, 40, 50]
middle = numbers.pop(2)
print(middle)   # 30
print(numbers)  # [10, 20, 40, 50]

# Negativer Index
first = numbers.pop(-len(numbers))  # Erstes Element
print(first)    # 10
print(numbers)  # [20, 40, 50]

# Zeitkomplexität:
# - pop() oder pop(-1): O(1) - vom Ende
# - pop(0): O(n) - vom Anfang, alle verschieben
# - pop(i): O(n) - aus der Mitte
```

**Stack-Operationen:**
```python
# Liste als Stack (LIFO - Last In, First Out)
stack = []

# Push
stack.append(1)
stack.append(2)
stack.append(3)
print(f"Stack: {stack}")  # [1, 2, 3]

# Pop
while stack:
    item = stack.pop()
    print(f"Pop: {item}, Rest: {stack}")

# Output:
# Pop: 3, Rest: [1, 2]
# Pop: 2, Rest: [1]
# Pop: 1, Rest: []
```

**Queue-Operationen (nicht empfohlen!):**
```python
# ❌ Liste als Queue ist ineffizient
queue = []
queue.append(1)  # Enqueue
queue.append(2)
queue.append(3)

item = queue.pop(0)  # Dequeue - O(n)! Langsam!
print(item)  # 1

# ✅ Stattdessen: collections.deque verwenden
from collections import deque
queue = deque([1, 2, 3])
item = queue.popleft()  # O(1) - Schnell!
print(item)  # 1
```

---

### `clear()` - Alle Elemente entfernen

```python
# Signatur: list.clear() -> None

numbers = [1, 2, 3, 4, 5]
numbers.clear()
print(numbers)  # []
print(len(numbers))  # 0

# Äquivalent zu:
numbers = [1, 2, 3, 4, 5]
del numbers[:]
print(numbers)  # []

# Oder:
numbers = [1, 2, 3, 4, 5]
numbers[:] = []
print(numbers)  # []

# Zeitkomplexität: O(n)
```

**Unterschied zu Neuzuweisung:**
```python
# Mit clear(): Selbes Objekt, nur leer
original = [1, 2, 3]
reference = original  # Zweite Referenz
original.clear()
print(original)   # []
print(reference)  # [] - auch leer!

# Mit Neuzuweisung: Neues Objekt
original = [1, 2, 3]
reference = original
original = []  # original zeigt jetzt auf NEUE Liste
print(original)   # []
print(reference)  # [1, 2, 3] - unverändert!
```

---

## Suchen und Zählen

### `index(x[, start[, end]])` - Index eines Elements finden

```python
# Signatur: list.index(x[, start[, end]]) -> int
# Raises: ValueError wenn Element nicht gefunden

fruits = ["Apfel", "Banane", "Orange", "Banane", "Mango"]

# Einfache Suche
idx = fruits.index("Orange")
print(idx)  # 2

# Mit Start-Position
idx = fruits.index("Banane", 2)  # Suche ab Index 2
print(idx)  # 3 (zweites Vorkommen)

# Mit Start und End
fruits_ext = ["A", "B", "C", "B", "D", "B", "E"]
idx = fruits_ext.index("B", 2, 5)  # Suche zwischen Index 2 und 5
print(idx)  # 3

# ValueError bei nicht gefunden
try:
    fruits.index("Kiwi")
except ValueError as e:
    print(f"Fehler: {e}")  # 'Kiwi' is not in list

# Zeitkomplexität: O(n)
```

**Sichere Verwendung:**
```python
def safe_index(lst, value, default=-1):
    """Gibt Index zurück oder default wenn nicht gefunden"""
    try:
        return lst.index(value)
    except ValueError:
        return default

fruits = ["Apfel", "Banane", "Orange"]
print(safe_index(fruits, "Banane"))  # 1
print(safe_index(fruits, "Kiwi"))    # -1

# Oder mit membership test
if "Banane" in fruits:
    idx = fruits.index("Banane")
    print(f"Gefunden an Index {idx}")
```

**Alle Indizes finden:**
```python
def find_all_indices(lst, value):
    """Findet alle Indizes eines Wertes"""
    indices = []
    start = 0
    while True:
        try:
            idx = lst.index(value, start)
            indices.append(idx)
            start = idx + 1
        except ValueError:
            break
    return indices

numbers = [1, 2, 3, 2, 4, 2, 5]
print(find_all_indices(numbers, 2))  # [1, 3, 5]

# Oder mit enumerate (pythonischer):
def find_all_indices_v2(lst, value):
    return [i for i, x in enumerate(lst) if x == value]

print(find_all_indices_v2(numbers, 2))  # [1, 3, 5]
```

---

### `count(x)` - Vorkommen zählen

```python
# Signatur: list.count(x) -> int

numbers = [1, 2, 3, 2, 4, 2, 5, 2]
count = numbers.count(2)
print(count)  # 4

# Zählt nur exakte Übereinstimmungen
mixed = [1, "1", 1.0, True]
print(mixed.count(1))    # 3 (1, 1.0, und True zählen als 1!)
print(mixed.count("1"))  # 1
print(mixed.count(True)) # 3 (1, 1.0, und True sind äquivalent)

# Element nicht vorhanden: gibt 0 zurück
fruits = ["Apfel", "Banane"]
print(fruits.count("Kiwi"))  # 0

# Zeitkomplexität: O(n)
```

**Häufigkeitsanalyse:**
```python
# Häufigkeit aller einzigartigen Elemente
numbers = [1, 2, 2, 3, 3, 3, 4, 4, 4, 4]

# Methode 1: Mit count (ineffizient für viele unique values)
unique = list(set(numbers))
frequencies = {num: numbers.count(num) for num in unique}
print(frequencies)  # {1: 1, 2: 2, 3: 3, 4: 4}

# Methode 2: Collections.Counter (besser!)
from collections import Counter
frequencies = Counter(numbers)
print(frequencies)  # Counter({4: 4, 3: 3, 2: 2, 1: 1})

# Most common elements
print(frequencies.most_common(2))  # [(4, 4), (3, 3)]
```

---

## Sortieren und Umkehren

### `sort(*, key=None, reverse=False)` - Liste in-place sortieren

```python
# Signatur: list.sort(*, key=None, reverse=False) -> None

# Einfaches Sortieren
numbers = [3, 1, 4, 1, 5, 9, 2, 6]
numbers.sort()
print(numbers)  # [1, 1, 2, 3, 4, 5, 6, 9]

# Absteigend sortieren
numbers.sort(reverse=True)
print(numbers)  # [9, 6, 5, 4, 3, 2, 1, 1]

# Strings sortieren (lexikographisch)
fruits = ["Banane", "Apfel", "Orange", "Mango"]
fruits.sort()
print(fruits)  # ['Apfel', 'Banane', 'Mango', 'Orange']

# Case-insensitive sortieren
words = ["Zebra", "apfel", "Banane", "orange"]
words.sort(key=str.lower)
print(words)  # ['apfel', 'Banane', 'orange', 'Zebra']

# Zeitkomplexität: O(n log n) - Timsort Algorithmus
```

**Sortieren mit Key-Funktion:**
```python
# Nach Länge sortieren
words = ["Python", "ist", "großartig", "!"]
words.sort(key=len)
print(words)  # ['!', 'ist', 'Python', 'großartig']

# Nach mehreren Kriterien (Tupel-Sortierung)
words.sort(key=lambda x: (len(x), x))  # Erst Länge, dann alphabetisch
print(words)  # ['!', 'ist', 'Python', 'großartig']

# Komplexe Objekte sortieren
students = [
    {"name": "Anna", "grade": 85},
    {"name": "Bob", "grade": 92},
    {"name": "Charlie", "grade": 78}
]

# Nach Note sortieren
students.sort(key=lambda s: s["grade"], reverse=True)
for s in students:
    print(f"{s['name']}: {s['grade']}")

# Output:
# Bob: 92
# Anna: 85
# Charlie: 78
```

**sort() vs sorted():**
```python
# sort() - modifiziert Original, gibt None zurück
numbers1 = [3, 1, 4, 1, 5]
result = numbers1.sort()
print(numbers1)  # [1, 1, 3, 4, 5] - sortiert
print(result)    # None

# sorted() - gibt NEUE sortierte Liste zurück
numbers2 = [3, 1, 4, 1, 5]
result = sorted(numbers2)
print(numbers2)  # [3, 1, 4, 1, 5] - unverändert
print(result)    # [1, 1, 3, 4, 5] - sortiert

# Wann was verwenden?
# - sort(): Wenn du Original modifizieren willst, spart Speicher
# - sorted(): Wenn du Original behalten willst, flexibler (funktioniert mit allen Iterables)
```

**Stabile Sortierung:**
```python
# Python's sort ist STABIL: gleiche Elemente behalten relative Reihenfolge

data = [
    ("Alice", 25),
    ("Bob", 30),
    ("Charlie", 25),
    ("David", 30)
]

# Sortiere nach Alter
data.sort(key=lambda x: x[1])
print(data)
# [('Alice', 25), ('Charlie', 25), ('Bob', 30), ('David', 30)]
# Alice kommt vor Charlie (beide 25), Bob vor David (beide 30) - Reihenfolge erhalten!
```

---

### `reverse()` - Reihenfolge umkehren

```python
# Signatur: list.reverse() -> None

numbers = [1, 2, 3, 4, 5]
numbers.reverse()
print(numbers)  # [5, 4, 3, 2, 1]

# Funktioniert mit allen Typen
fruits = ["Apfel", "Banane", "Orange"]
fruits.reverse()
print(fruits)  # ['Orange', 'Banane', 'Apfel']

# Zeitkomplexität: O(n)
```

**reverse() vs slicing:**
```python
# reverse() - modifiziert Original
numbers1 = [1, 2, 3, 4, 5]
numbers1.reverse()
print(numbers1)  # [5, 4, 3, 2, 1]

# [::-1] - erstellt NEUE umgekehrte Liste
numbers2 = [1, 2, 3, 4, 5]
reversed_copy = numbers2[::-1]
print(numbers2)       # [1, 2, 3, 4, 5] - unverändert
print(reversed_copy)  # [5, 4, 3, 2, 1]

# reversed() - gibt Iterator zurück (lazy)
numbers3 = [1, 2, 3, 4, 5]
rev_iter = reversed(numbers3)
print(list(rev_iter))  # [5, 4, 3, 2, 1]
print(numbers3)        # [1, 2, 3, 4, 5] - unverändert
```

---

## Kopieren

### `copy()` - Flache Kopie erstellen

```python
# Signatur: list.copy() -> list

# Erstellt flache (shallow) Kopie
original = [1, 2, 3]
copied = original.copy()

# Änderungen an Kopie betreffen Original nicht
copied.append(4)
print(original)  # [1, 2, 3]
print(copied)    # [1, 2, 3, 4]

# Zeitkomplexität: O(n)
```

**Verschiedene Kopiermethoden:**
```python
original = [1, 2, 3]

# Alle erstellen flache Kopien:
copy1 = original.copy()
copy2 = original[:]
copy3 = list(original)
copy4 = [x for x in original]

print(copy1 == copy2 == copy3 == copy4)  # True
print(copy1 is original)  # False - verschiedene Objekte
```

**Flache vs Tiefe Kopie:**
```python
# Flache Kopie: Nur äußere Liste wird kopiert
original = [[1, 2], [3, 4]]
shallow = original.copy()

# Änderung in verschachtelter Liste betrifft beide!
shallow[0].append(999)
print(original)  # [[1, 2, 999], [3, 4]] - auch geändert!
print(shallow)   # [[1, 2, 999], [3, 4]]

# Tiefe Kopie: Alle Ebenen werden kopiert
import copy
original = [[1, 2], [3, 4]]
deep = copy.deepcopy(original)

deep[0].append(999)
print(original)  # [[1, 2], [3, 4]] - unverändert
print(deep)      # [[1, 2, 999], [3, 4]]
```

---

## Zusammenfassung: Wann welche Methode?

```python
# HINZUFÜGEN
list.append(x)        # Ein Element am Ende - O(1)
list.extend(iter)     # Mehrere Elemente am Ende - O(k)
list.insert(i, x)     # An Position i - O(n), langsam!

# ENTFERNEN
list.remove(x)        # Erstes Vorkommen von x - O(n)
list.pop([i])         # An Position i (default: Ende) - O(1) oder O(n)
list.clear()          # Alle Elemente - O(n)

# SUCHEN
list.index(x)         # Index von x - O(n)
list.count(x)         # Anzahl von x - O(n)
x in list             # Membership test - O(n)

# ORDNUNG
list.sort()           # In-place sortieren - O(n log n)
list.reverse()        # In-place umkehren - O(n)

# KOPIEREN
list.copy()           # Flache Kopie - O(n)
```

---

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [List Methods](https://docs.python.org/3/tutorial/datastructures.html#more-on-lists) - Tutorial mit allen Methoden
- [Mutable Sequence Types](https://docs.python.org/3/library/stdtypes.html#mutable-sequence-types) - Technische Referenz
- [Sorting HOW TO](https://docs.python.org/3/howto/sorting.html) - Detaillierter Guide zum Sortieren
- [copy Module](https://docs.python.org/3/library/copy.html) - Shallow und Deep Copy

---

## Verwandte Themen

- [[02_Sequenzen/01_Listen/01_List Basics|List Basics]] - Grundlagen zu Listen
- [[02_Sequenzen/01_Listen/03_List Operationen|List Operationen]] - Operatoren und Built-ins
- [[02_Sequenzen/04_Comprehensions/01_List Comprehensions|List Comprehensions]] - Elegante Listen-Erstellung
- [[06_Praxis/01_Best_Practices/01_Idiomatisches Python|Idiomatisches Python]] - Best Practices

---

← [[02_Sequenzen/01_Listen/01_List Basics|List Basics]] | [[INDEX|📑 Index]] | [[02_Sequenzen/01_Listen/03_List Operationen|List Operationen]] →