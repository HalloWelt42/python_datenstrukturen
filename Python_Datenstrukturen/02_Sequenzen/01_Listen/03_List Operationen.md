---
tags: [python, datenstrukturen, list, operationen]
created: 2025-10-01
---

← [[02_Sequenzen/01_Listen/02_List Methoden|List Methoden]] | [[INDEX|📑 Index]] | [[02_Sequenzen/01_Listen/04_Nested Lists|Nested Lists]] →

# List Operationen - Operatoren und Built-in Functions

> [!tip] Lernziel
> Nach dieser Seite kennst du alle Operatoren (+, *, in, etc.) und wichtigen Built-in-Funktionen (len, sum, min, max, etc.) für Listen. Du weißt, wie sie intern funktionieren und wann du sie einsetzen solltest.

## Übersicht der Operationen

### Operatoren

| Operator | Beschreibung | Zeitkomplexität |
|----------|--------------|-----------------|
| `+` | Konkatenation (verbinden) | O(n+m) |
| `*` | Wiederholung | O(n*k) |
| `in` | Membership Test | O(n) |
| `not in` | Negierter Membership | O(n) |
| `==` | Gleichheit | O(n) |
| `!=` | Ungleichheit | O(n) |
| `<`, `>`, `<=`, `>=` | Lexikographischer Vergleich | O(n) |
| `[i]` | Index-Zugriff | O(1) |
| `[i:j:k]` | Slicing | O(k) |

### Built-in Functions

| Funktion | Beschreibung | Zeitkomplexität |
|----------|--------------|-----------------|
| `len(list)` | Anzahl Elemente | O(1) |
| `sum(list)` | Summe der Elemente | O(n) |
| `min(list)` | Kleinstes Element | O(n) |
| `max(list)` | Größtes Element | O(n) |
| `all(list)` | Alle True? | O(n) |
| `any(list)` | Mindestens ein True? | O(n) |
| `sorted(list)` | Sortierte Kopie | O(n log n) |
| `reversed(list)` | Umgekehrter Iterator | O(1) |
| `enumerate(list)` | Index-Wert Paare | O(1) |
| `zip(lists)` | Listen kombinieren | O(1) |

---

## Arithmetische Operatoren

### `+` - Konkatenation (Verbinden)

```python
# Zwei Listen verbinden
list1 = [1, 2, 3]
list2 = [4, 5, 6]
result = list1 + list2
print(result)  # [1, 2, 3, 4, 5, 6]

# Original-Listen bleiben unverändert
print(list1)  # [1, 2, 3]
print(list2)  # [4, 5, 6]

# Mehrere Listen verbinden
list3 = [7, 8]
result = list1 + list2 + list3
print(result)  # [1, 2, 3, 4, 5, 6, 7, 8]

# Zeitkomplexität: O(n+m), wobei n, m = Längen der Listen
# Muss neue Liste mit allen Elementen erstellen
```

**+ vs extend():**
```python
# + erstellt NEUE Liste
list1 = [1, 2, 3]
list2 = [4, 5, 6]
list3 = list1 + list2  # Neue Liste
print(id(list1), id(list3))  # Verschiedene IDs

# extend() modifiziert bestehende Liste
list1 = [1, 2, 3]
list2 = [4, 5, 6]
list1.extend(list2)  # Selbe Liste, erweitert
print(list1)  # [1, 2, 3, 4, 5, 6]

# += ist wie extend() (modifiziert Original)
list1 = [1, 2, 3]
original_id = id(list1)
list1 += [4, 5, 6]
print(id(list1) == original_id)  # True - selbes Objekt!
```

**Performance-Vergleich:**
```python
import time

# Ineffizient: Wiederholtes +
def concat_with_plus(n):
    result = []
    for i in range(n):
        result = result + [i]  # Erstellt jedes Mal NEUE Liste! O(n²)
    return result

# Effizient: append()
def concat_with_append(n):
    result = []
    for i in range(n):
        result.append(i)  # O(n)
    return result

n = 5000
start = time.perf_counter()
concat_with_plus(n)
time1 = time.perf_counter() - start

start = time.perf_counter()
concat_with_append(n)
time2 = time.perf_counter() - start

print(f"Mit +:      {time1:.4f}s")
print(f"Mit append: {time2:.4f}s")
print(f"Faktor:     {time1/time2:.1f}x langsamer")
```

---

### `*` - Wiederholung

```python
# Liste wiederholen
zeros = [0] * 5
print(zeros)  # [0, 0, 0, 0, 0]

pattern = ["X", "O"] * 3
print(pattern)  # ['X', 'O', 'X', 'O', 'X', 'O']

# Mit 0 oder negativer Zahl
empty = [1, 2, 3] * 0
print(empty)  # []

negative = [1, 2] * -5
print(negative)  # []

# Zeitkomplexität: O(n*k), wobei n = Listenlänge, k = Wiederholungen
```

**⚠️ WICHTIGE WARNUNG bei verschachtelten Listen:**
```python
# ❌ FALSCH: Alle Zeilen sind DIESELBE Liste!
matrix = [[0] * 3] * 3
matrix[0][0] = 1
print(matrix)
# [[1, 0, 0], [1, 0, 0], [1, 0, 0]]
# Alle Zeilen ändern sich! Sie sind identisch!

# Beweis:
matrix = [[0] * 3] * 3
print(id(matrix[0]) == id(matrix[1]))  # True - selbes Objekt!

# ✅ RICHTIG: Jede Zeile ist eigene Liste
matrix = [[0] * 3 for _ in range(3)]
matrix[0][0] = 1
print(matrix)
# [[1, 0, 0], [0, 0, 0], [0, 0, 0]]
# Nur erste Zeile ändert sich!

# Beweis:
matrix = [[0] * 3 for _ in range(3)]
print(id(matrix[0]) == id(matrix[1]))  # False - verschiedene Objekte
```

**Praktische Anwendungen:**
```python
# Initialisierung mit Standardwerten
default_scores = [0.0] * 10
print(default_scores)  # [0.0, 0.0, 0.0, ...]

# Padding für Ausrichtung
text = "Hallo"
padded = [" "] * 10 + list(text) + [" "] * 10
print(''.join(padded))  # "          Hallo          "

# Separator einfügen
words = ["Python", "ist", "toll"]
with_sep = []
for word in words:
    with_sep.append(word)
    with_sep.extend(["-"] * 3)
with_sep = with_sep[:-3]  # Letzten Separator entfernen
print(with_sep)  # ['Python', '-', '-', '-', 'ist', '-', '-', '-', 'toll']
```

---

## Vergleichsoperatoren

### `==` und `!=` - Gleichheit prüfen

```python
# Gleichheit prüft Inhalt, nicht Identität
list1 = [1, 2, 3]
list2 = [1, 2, 3]
list3 = list1

print(list1 == list2)  # True - gleicher Inhalt
print(list1 is list2)  # False - verschiedene Objekte
print(list1 is list3)  # True - selbes Objekt

# Element-weiser Vergleich
print([1, 2, 3] == [1, 2, 3])      # True
print([1, 2, 3] == [1, 2, 3, 4])   # False
print([1, 2, 3] == [3, 2, 1])      # False - Reihenfolge zählt!

# Verschiedene Typen
print([1, 2] == (1, 2))  # False - Liste vs Tupel
print([1, 2] != (1, 2))  # True

# Zeitkomplexität: O(n)
```

### Lexikographischer Vergleich (`<`, `>`, `<=`, `>=`)

```python
# Vergleicht Element für Element von links nach rechts
print([1, 2, 3] < [1, 2, 4])   # True - drittes Element entscheidet
print([1, 2, 3] < [1, 3])      # True - zweites Element entscheidet
print([1, 2, 3] < [1, 2, 3])   # False - identisch
print([1, 2, 3] <= [1, 2, 3])  # True - identisch ist <=

# Kürzere Liste ist "kleiner" wenn Präfix gleich
print([1, 2] < [1, 2, 3])      # True
print([1, 2, 3] > [1, 2])      # True

# Strings lexikographisch
print(["a", "b"] < ["a", "c"]) # True
print(["abc"] < ["b"])         # True - "abc" < "b"

# ⚠️ Typen müssen vergleichbar sein
try:
    result = [1, 2] < ["a", "b"]  # TypeError
except TypeError as e:
    print(f"Fehler: {e}")
```

**Anwendungen:**
```python
# Sortierung von Listen
pairs = [[3, 1], [1, 2], [1, 1], [2, 4]]
pairs.sort()
print(pairs)  # [[1, 1], [1, 2], [2, 4], [3, 1]]

# Version-Vergleich
version1 = [1, 2, 3]
version2 = [1, 2, 10]
if version1 < version2:
    print("Version 1 ist älter")  # Version 1 ist älter

# Tupel-ähnlicher Vergleich
students = [
    ["Anna", 85],
    ["Bob", 92],
    ["Anna", 90]
]
students.sort()  # Sortiert nach Name, dann Note
print(students)  # [['Anna', 85], ['Anna', 90], ['Bob', 92]]
```

---

## Membership-Operatoren

### `in` und `not in` - Enthält Element?

```python
# Prüft ob Element vorhanden ist
fruits = ["Apfel", "Banane", "Orange"]

print("Apfel" in fruits)    # True
print("Kiwi" in fruits)     # False
print("Kiwi" not in fruits) # True

# Funktioniert mit allen Typen
numbers = [1, 2, 3, 4, 5]
print(3 in numbers)   # True
print(10 in numbers)  # False

# Teilsequenzen werden NICHT gefunden!
text = ["Hallo", "Welt"]
print("Hal" in text)  # False - "Hal" ist kein Element, nur Teil von "Hallo"

# Zeitkomplexität: O(n) - muss potentiell alle Elemente durchsuchen
```

**Performance-Überlegungen:**
```python
import time

# Ineffizient: Wiederholte Suche in Liste
def check_in_list(n):
    data = list(range(n))
    count = 0
    for i in range(1000):
        if i in data:  # O(n) bei jedem Check!
            count += 1
    return count

# Effizient: Set verwenden
def check_in_set(n):
    data = set(range(n))
    count = 0
    for i in range(1000):
        if i in data:  # O(1) bei jedem Check!
            count += 1
    return count

n = 10000
start = time.perf_counter()
check_in_list(n)
time1 = time.perf_counter() - start

start = time.perf_counter()
check_in_set(n)
time2 = time.perf_counter() - start

print(f"Liste: {time1:.4f}s")
print(f"Set:   {time2:.4f}s")
print(f"Speedup: {time1/time2:.1f}x")
```

**Sichere Patterns:**
```python
# Bedingung mit membership
fruits = ["Apfel", "Banane", "Orange"]
if "Apfel" in fruits:
    print("Apfel gefunden!")

# Filter mit membership
allowed = ["Python", "Java", "C++"]
languages = ["Python", "JavaScript", "C++", "Ruby"]
filtered = [lang for lang in languages if lang in allowed]
print(filtered)  # ['Python', 'C++']

# Negation
unwanted = ["foo", "bar"]
data = ["hello", "foo", "world", "bar"]
cleaned = [item for item in data if item not in unwanted]
print(cleaned)  # ['hello', 'world']
```

---

## Built-in Functions

### `len()` - Länge ermitteln

```python
# Anzahl Elemente
numbers = [1, 2, 3, 4, 5]
print(len(numbers))  # 5

empty = []
print(len(empty))  # 0

# Funktioniert mit verschachtelten Listen
nested = [[1, 2], [3, 4, 5], [6]]
print(len(nested))  # 3 (äußere Liste hat 3 Elemente)

# Zeitkomplexität: O(1) - Länge ist gespeichert
```

**Häufige Anwendungen:**
```python
# Leere Liste prüfen
data = []
if len(data) == 0:
    print("Leer")

# Pythonischer: Truthiness nutzen
if not data:  # Leere Liste ist falsy
    print("Leer")

# Letztes Element sicher zugreifen
if len(data) > 0:
    last = data[-1]

# Iteration mit Index
for i in range(len(data)):
    print(f"Index {i}: {data[i]}")

# Besser: enumerate() verwenden
for i, value in enumerate(data):
    print(f"Index {i}: {value}")
```

---

### `sum()` - Summe berechnen

```python
# Signatur: sum(iterable, start=0)

# Einfache Summe
numbers = [1, 2, 3, 4, 5]
total = sum(numbers)
print(total)  # 15

# Mit Startwert
total = sum(numbers, 100)
print(total)  # 115 (100 + 15)

# Funktioniert mit float
prices = [9.99, 19.99, 5.50]
total = sum(prices)
print(total)  # 35.48

# Zeitkomplexität: O(n)
```

**Erweiterte Anwendungen:**
```python
# Durchschnitt berechnen
numbers = [1, 2, 3, 4, 5]
average = sum(numbers) / len(numbers)
print(average)  # 3.0

# Bedingten Summe
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
even_sum = sum(n for n in numbers if n % 2 == 0)
print(even_sum)  # 30 (2 + 4 + 6 + 8 + 10)

# Verschachtelte Listen summieren
matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
total = sum(sum(row) for row in matrix)
print(total)  # 45

# ⚠️ NICHT für Strings verwenden!
try:
    result = sum(["a", "b", "c"], "")  # TypeError
except TypeError as e:
    print(f"Fehler: {e}")

# ✅ Für Strings: ''.join() verwenden
result = ''.join(["a", "b", "c"])
print(result)  # "abc"
```

---

### `min()` und `max()` - Extremwerte

```python
# Signatur: min(iterable, *, key=None, default=None)

# Kleinstes Element
numbers = [3, 1, 4, 1, 5, 9, 2, 6]
print(min(numbers))  # 1
print(max(numbers))  # 9

# Mit Strings (lexikographisch)
words = ["Zebra", "Apfel", "Banane"]
print(min(words))  # 'Apfel'
print(max(words))  # 'Zebra'

# Leere Liste: ValueError
try:
    min([])
except ValueError as e:
    print(f"Fehler: {e}")  # min() arg is an empty sequence

# Mit default (Python 3.4+)
print(min([], default=0))  # 0

# Zeitkomplexität: O(n)
```

**Mit Key-Funktion:**
```python
# Nach Länge
words = ["Python", "ist", "großartig"]
shortest = min(words, key=len)
longest = max(words, key=len)
print(shortest)  # 'ist'
print(longest)   # 'großartig'

# Nach absolutem Wert
numbers = [-5, 3, -8, 1, 7]
closest_to_zero = min(numbers, key=abs)
print(closest_to_zero)  # 1

# Komplexe Objekte
students = [
    {"name": "Anna", "grade": 85},
    {"name": "Bob", "grade": 92},
    {"name": "Charlie", "grade": 78}
]

best = max(students, key=lambda s: s["grade"])
worst = min(students, key=lambda s: s["grade"])

print(f"Beste Note: {best['name']} ({best['grade']})")
print(f"Schlechteste Note: {worst['name']} ({worst['grade']})")
```

---

### `all()` und `any()` - Boolesche Tests

```python
# all() - Sind ALLE Elemente True?
# any() - Ist MINDESTENS EIN Element True?

# Alle True?
print(all([True, True, True]))    # True
print(all([True, False, True]))   # False
print(all([]))                    # True - leere Sequenz!

# Mindestens eins True?
print(any([False, False, True]))  # True
print(any([False, False, False])) # False
print(any([]))                    # False - leere Sequenz!

# Zeitkomplexität: O(n) worst case, aber short-circuit evaluation
```

**Praktische Anwendungen:**
```python
# Alle Zahlen positiv?
numbers = [1, 2, 3, 4, 5]
all_positive = all(n > 0 for n in numbers)
print(all_positive)  # True

# Irgendeine Zahl negativ?
numbers = [1, -2, 3, 4, 5]
has_negative = any(n < 0 for n in numbers)
print(has_negative)  # True

# Alle Strings nicht leer?
strings = ["hello", "world", ""]
all_non_empty = all(s for s in strings)  # "" ist falsy
print(all_non_empty)  # False

# Validierung
def validate_user_data(data):
    required_fields = ["name", "email", "age"]
    has_all_fields = all(field in data for field in required_fields)
    
    if has_all_fields:
        valid_age = data["age"] > 0
        valid_email = "@" in data["email"]
        return all([valid_age, valid_email])
    return False

user1 = {"name": "Anna", "email": "anna@example.com", "age": 25}
user2 = {"name": "Bob", "email": "invalid", "age": -5}

print(validate_user_data(user1))  # True
print(validate_user_data(user2))  # False
```

---

### `sorted()` - Sortierte Kopie erstellen

```python
# Signatur: sorted(iterable, *, key=None, reverse=False)

# Erstellt NEUE sortierte Liste (Original bleibt unverändert)
numbers = [3, 1, 4, 1, 5, 9, 2, 6]
sorted_numbers = sorted(numbers)

print(numbers)         # [3, 1, 4, 1, 5, 9, 2, 6] - unverändert
print(sorted_numbers)  # [1, 1, 2, 3, 4, 5, 6, 9]

# Funktioniert mit ALLEN Iterables (nicht nur Listen)
sorted_from_set = sorted({3, 1, 4, 1, 5})
print(sorted_from_set)  # [1, 3, 4, 5]

sorted_from_string = sorted("Python")
print(sorted_from_string)  # ['P', 'h', 'n', 'o', 't', 'y']

# Zeitkomplexität: O(n log n)
```

**sorted() vs list.sort():**
```python
# sorted() - gibt neue Liste zurück, funktioniert mit allen Iterables
original = (3, 1, 4)  # Tupel
result = sorted(original)
print(result)  # [1, 3, 4] - Liste!
print(original)  # (3, 1, 4) - unverändert

# list.sort() - modifiziert Original, nur für Listen
lst = [3, 1, 4]
lst.sort()
print(lst)  # [1, 3, 4]

# Wann was verwenden?
# - sorted(): Wenn Original behalten, mit anderen Iterables, funktionaler Stil
# - list.sort(): Wenn nur Liste und Speicher sparen wichtig ist
```

---

### `reversed()` - Umgekehrter Iterator

```python
# Signatur: reversed(sequence) -> iterator

# Gibt Iterator zurück (lazy evaluation)
numbers = [1, 2, 3, 4, 5]
rev = reversed(numbers)

print(rev)  # <list_reverseiterator object>
print(list(rev))  # [5, 4, 3, 2, 1]

# Original unverändert
print(numbers)  # [1, 2, 3, 4, 5]

# Zeitkomplexität: O(1) für Erstellung, O(n) für Iteration
```

**Verschiedene Methoden zum Umkehren:**
```python
numbers = [1, 2, 3, 4, 5]

# Methode 1: reversed() - Iterator, lazy
for n in reversed(numbers):
    print(n, end=" ")  # 5 4 3 2 1
print()

# Methode 2: [::-1] - neue Liste, sofort
reversed_copy = numbers[::-1]
print(reversed_copy)  # [5, 4, 3, 2, 1]

# Methode 3: list.reverse() - modifiziert Original
numbers_copy = numbers.copy()
numbers_copy.reverse()
print(numbers_copy)  # [5, 4, 3, 2, 1]

# Wann was?
# reversed() - für Iteration, speichereffizient
# [::-1] - wenn neue Liste gebraucht wird
# .reverse() - wenn Original ändern ok ist
```

---

### `enumerate()` - Index-Wert Paare

```python
# Signatur: enumerate(iterable, start=0) -> iterator

fruits = ["Apfel", "Banane", "Orange"]

# Standard (start=0)
for i, fruit in enumerate(fruits):
    print(f"{i}: {fruit}")
# 0: Apfel
# 1: Banane
# 2: Orange

# Mit anderem Startwert
for i, fruit in enumerate(fruits, start=1):
    print(f"{i}. {fruit}")
# 1. Apfel
# 2. Banane
# 3. Orange

# Als Liste
pairs = list(enumerate(fruits))
print(pairs)  # [(0, 'Apfel'), (1, 'Banane'), (2, 'Orange')]

# Zeitkomplexität: O(1) für Erstellung, O(n) für Iteration
```

**Warum enumerate() statt range(len()):**
```python
fruits = ["Apfel", "Banane", "Orange"]

# ❌ Nicht pythonisch
for i in range(len(fruits)):
    print(f"{i}: {fruits[i]}")

# ✅ Pythonisch mit enumerate()
for i, fruit in enumerate(fruits):
    print(f"{i}: {fruit}")

# Vorteile:
# - Lesbarer und idiomatischer
# - Funktioniert mit allen Iterables (nicht nur Listen)
# - Weniger fehleranfällig
```

**Praktische Anwendungen:**
```python
# Nummerierte Ausgabe
tasks = ["Einkaufen", "Sport", "Lernen"]
for i, task in enumerate(tasks, start=1):
    print(f"Aufgabe {i}: {task}")

# Index beim Filtern merken
numbers = [10, 25, 30, 15, 40, 5]
large_indices = [i for i, n in enumerate(numbers) if n > 20]
print(large_indices)  # [1, 2, 4]

# Mehrere Listen parallel mit Index
names = ["Anna", "Bob", "Charlie"]
scores = [85, 92, 78]

for i, (name, score) in enumerate(zip(names, scores), start=1):
    print(f"{i}. {name}: {score} Punkte")
```

---

### `zip()` - Listen kombinieren

```python
# Signatur: zip(*iterables, strict=False) -> iterator

# Zwei Listen paaren
names = ["Anna", "Bob", "Charlie"]
ages = [25, 30, 28]

pairs = list(zip(names, ages))
print(pairs)  # [('Anna', 25), ('Bob', 30), ('Charlie', 28)]

# Iteration
for name, age in zip(names, ages):
    print(f"{name} ist {age} Jahre alt")

# Mehrere Listen
names = ["Anna", "Bob"]
ages = [25, 30]
cities = ["Berlin", "München"]

combined = list(zip(names, ages, cities))
print(combined)  # [('Anna', 25, 'Berlin'), ('Bob', 30, 'München')]

# Zeitkomplexität: O(1) für Erstellung, O(min(n,m,...)) für Iteration
```

**Verschiedene Längen:**
```python
# Stoppt bei kürzester Liste
short = [1, 2]
long = [10, 20, 30, 40]

print(list(zip(short, long)))  # [(1, 10), (2, 20)]

# Python 3.10+: strict=True für Fehler bei unterschiedlichen Längen
try:
    result = list(zip(short, long, strict=True))
except ValueError as e:
    print(f"Fehler: {e}")  # zip() argument 2 is longer than argument 1
```

**Unzip - Listen trennen:**
```python
# zip kann sich selbst "umkehren"
pairs = [(1, 'a'), (2, 'b'), (3, 'c')]

numbers, letters = zip(*pairs)  # * unpacks die Liste
print(numbers)  # (1, 2, 3)
print(letters)  # ('a', 'b', 'c')

# Als Listen (zip gibt Tupel zurück)
numbers = list(numbers)
letters = list(letters)
```

**Praktische Anwendungen:**
```python
# Dictionary aus zwei Listen
keys = ["name", "age", "city"]
values = ["Anna", 25, "Berlin"]
person = dict(zip(keys, values))
print(person)  # {'name': 'Anna', 'age': 25, 'city': 'Berlin'}

# Parallele Iteration
questions = ["Wie heißt du?", "Wie alt bist du?"]
answers = ["Anna", "25"]

for q, a in zip(questions, answers):
    print(f"F: {q}")
    print(f"A: {a}\n")

# Matrix-Transposition
matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]

transposed = list(zip(*matrix))
for row in transposed:
    print(list(row))
# [1, 4, 7]
# [2, 5, 8]
# [3, 6, 9]
```

---

## Zusammenfassung: Best Practices

```python
# ✅ DO: Effiziente Operationen verwenden
result = []
for item in items:
    result.append(item)  # O(1) pro append

# ❌ DON'T: Ineffiziente Konkatenation
result = []
for item in items:
    result = result + [item]  # O(n) pro Operation!

# ✅ DO: Set für Membership Tests bei vielen Checks
allowed = set(allowed_list)
filtered = [x for x in data if x in allowed]  # O(1) per check

# ❌ DON'T: Liste für viele Membership Tests
allowed = allowed_list  # Liste!
filtered = [x for x in data if x in allowed]  # O(n) per check!

# ✅ DO: enumerate() für Index-Zugriff
for i, item in enumerate(items):
    print(f"{i}: {item}")

# ❌ DON'T: range(len())
for i in range(len(items)):
    print(f"{i}: {items[i]}")

# ✅ DO: sorted() für temporäre Sortierung
sorted_copy = sorted(original)

# ✅ DO: list.sort() für permanente Sortierung
items.sort()

# ✅ DO: zip() für parallele Iteration
for name, score in zip(names, scores):
    print(f"{name}: {score}")

# ❌ DON'T: Manuelle Index-Iteration
for i in range(len(names)):
    print(f"{names[i]}: {scores[i]}")
```

---

## Performance-Cheat-Sheet

**Schnelle Operationen (O(1)):**
- `list[i]` - Index-Zugriff
- `list[i] = x` - Index-Zuweisung
- `len(list)` - Länge
- `list.append(x)` - Am Ende hinzufügen
- `list.pop()` - Vom Ende entfernen

**Lineare Operationen (O(n)):**
- `x in list` - Membership Test
- `list.count(x)` - Zählen
- `list.index(x)` - Suchen
- `list.remove(x)` - Nach Wert entfernen
- `min(list)`, `max(list)`, `sum(list)` - Aggregation
- `list.reverse()` - Umkehren

**Langsame Operationen:**
- `list.sort()` - O(n log n) Sortieren
- `list.insert(0, x)` - O(n) Am Anfang einfügen
- `list.pop(0)` - O(n) Vom Anfang entfernen
- `list + list` - O(n+m) Konkatenation

**Wann Alternativen verwenden:**
- Viele Membership Tests → `set` statt `list`
- Viele Operationen am Anfang → `collections.deque` statt `list`
- Nur numerische Daten → `array.array` oder NumPy
- Sortierung erhalten → `bisect` Modul

---

## Häufige Idiome

### Dictionary aus Parallelen Listen erstellen

```python
keys = ["name", "age", "city"]
values = ["Anna", 25, "Berlin"]

# Mit zip() und dict()
person = dict(zip(keys, values))
print(person)  # {'name': 'Anna', 'age': 25, 'city': 'Berlin'}

# Mit Dictionary Comprehension
person = {k: v for k, v in zip(keys, values)}
```

### Liste deduplizieren (Duplikate entfernen)

```python
# Reihenfolge wird nicht erhalten
numbers = [1, 2, 2, 3, 3, 3, 4]
unique = list(set(numbers))
print(unique)  # [1, 2, 3, 4] - aber Reihenfolge kann anders sein!

# Reihenfolge erhalten (Python 3.7+)
from collections import OrderedDict
unique = list(OrderedDict.fromkeys(numbers))
print(unique)  # [1, 2, 3, 4] - Reihenfolge garantiert

# Oder einfacher (Python 3.7+, dict ist geordnet):
unique = list(dict.fromkeys(numbers))
print(unique)  # [1, 2, 3, 4]
```

### Liste in Chunks aufteilen

```python
def chunks(lst, n):
    """Teilt Liste in Chunks der Größe n"""
    for i in range(0, len(lst), n):
        yield lst[i:i + n]

data = [1, 2, 3, 4, 5, 6, 7, 8, 9]
for chunk in chunks(data, 3):
    print(chunk)
# [1, 2, 3]
# [4, 5, 6]
# [7, 8, 9]
```

### Flatten (verschachtelte Liste auflösen)

```python
# Eine Ebene flatten
nested = [[1, 2], [3, 4], [5, 6]]
flat = [item for sublist in nested for item in sublist]
print(flat)  # [1, 2, 3, 4, 5, 6]

# Oder mit sum() (clever aber ungewöhnlich)
flat = sum(nested, [])
print(flat)  # [1, 2, 3, 4, 5, 6]

# Rekursiv für beliebige Tiefe
def flatten(lst):
    result = []
    for item in lst:
        if isinstance(item, list):
            result.extend(flatten(item))
        else:
            result.append(item)
    return result

deep_nested = [1, [2, [3, [4, 5]], 6], 7]
print(flatten(deep_nested))  # [1, 2, 3, 4, 5, 6, 7]
```

### Paarweise Iteration

```python
# Aufeinanderfolgende Paare
numbers = [1, 2, 3, 4, 5]
pairs = zip(numbers, numbers[1:])
print(list(pairs))  # [(1, 2), (2, 3), (3, 4), (4, 5)]

# Anwendung: Differenzen berechnen
differences = [b - a for a, b in zip(numbers, numbers[1:])]
print(differences)  # [1, 1, 1, 1]

# Sliding Window (Fenster der Größe n)
def sliding_window(lst, n):
    for i in range(len(lst) - n + 1):
        yield lst[i:i + n]

numbers = [1, 2, 3, 4, 5]
for window in sliding_window(numbers, 3):
    print(window)
# [1, 2, 3]
# [2, 3, 4]
# [3, 4, 5]
```

---

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [Sequence Types](https://docs.python.org/3/library/stdtypes.html#sequence-types-list-tuple-range) - Alle Operatoren für Sequenzen
- [Built-in Functions](https://docs.python.org/3/library/functions.html) - Komplette Liste aller Built-ins
- [itertools](https://docs.python.org/3/library/itertools.html) - Erweiterte Iterator-Tools
- [operator Module](https://docs.python.org/3/library/operator.html) - Funktionale Versionen der Operatoren

---

## Verwandte Themen

- [[02_Sequenzen/01_Listen/02_List Methoden|List Methoden]] - Alle List-spezifischen Methoden
- [[02_Sequenzen/01_Listen/04_Nested Lists|Nested Lists]] - Arbeiten mit verschachtelten Listen
- [[02_Sequenzen/03_Slicing/01_Slicing Grundlagen|Slicing Grundlagen]] - Erweiterte Slice-Operationen
- [[02_Sequenzen/04_Comprehensions/01_List Comprehensions|List Comprehensions]] - Elegante Listen-Erstellung

---

← [[02_Sequenzen/01_Listen/02_List Methoden|List Methoden]] | [[INDEX|📑 Index]] | [[02_Sequenzen/01_Listen/04_Nested Lists|Nested Lists]] →