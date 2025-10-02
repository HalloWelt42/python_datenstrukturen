---
tags: [python, datenstrukturen, best-practices, pythonic]
created: 2025-10-02
---

← [[05_Pattern_Matching/02_Type_Hints/05_Übungen Type Hints|Übungen Type Hints]] | [[INDEX|📑 Index]] | [[06_Praxis/01_Best_Practices/02_Code Smells vermeiden|Code Smells vermeiden]] →

# Idiomatisches Python

> [!tip] Lernziel
> Du verstehst "Pythonic Code" und kannst Python-typische Idiome effektiv einsetzen.

## Was ist "Pythonic"?

**Pythonic Code** folgt den Konventionen und Best Practices der Python-Community. Er ist lesbar, elegant und nutzt Python-Features optimal. Das Zen of Python (`import this`) fasst die Philosophie zusammen.

```python
import this
# The Zen of Python, by Tim Peters
#
# Beautiful is better than ugly.
# Explicit is better than implicit.
# Simple is better than complex.
# Readability counts.
# ...
```

## List Comprehensions statt Loops

### ❌ Nicht-Pythonic

```python
# Unnötig kompliziert
squares = []
for i in range(10):
    squares.append(i ** 2)

# Klassischer Loop für Filterung
evens = []
for num in numbers:
    if num % 2 == 0:
        evens.append(num)
```

### ✅ Pythonic

```python
# List Comprehension
squares = [i ** 2 for i in range(10)]

# Filterung mit Comprehension
evens = [num for num in numbers if num % 2 == 0]

# Transformation + Filterung
doubled_evens = [num * 2 for num in numbers if num % 2 == 0]

# Nested Comprehensions für flache Listen
matrix = [[1, 2, 3], [4, 5, 6]]
flat = [num for row in matrix for num in row]
# [1, 2, 3, 4, 5, 6]
```

## Enumerate statt Range mit Index

### ❌ Nicht-Pythonic

```python
# Index mit range(len())
items = ["a", "b", "c"]
for i in range(len(items)):
    print(f"{i}: {items[i]}")
```

### ✅ Pythonic

```python
# enumerate für Index + Element
items = ["a", "b", "c"]
for i, item in enumerate(items):
    print(f"{i}: {item}")

# Mit Start-Index
for i, item in enumerate(items, start=1):
    print(f"{i}: {item}")
# 1: a, 2: b, 3: c
```

## Zip für parallele Iteration

### ❌ Nicht-Pythonic

```python
# Parallele Listen mit Index
names = ["Alice", "Bob", "Charlie"]
ages = [25, 30, 35]

for i in range(len(names)):
    print(f"{names[i]} is {ages[i]} years old")
```

### ✅ Pythonic

```python
# zip für parallele Iteration
names = ["Alice", "Bob", "Charlie"]
ages = [25, 30, 35]

for name, age in zip(names, ages):
    print(f"{name} is {age} years old")

# zip mit mehr als 2 Listen
cities = ["Berlin", "London", "Paris"]
for name, age, city in zip(names, ages, cities):
    print(f"{name}, {age}, from {city}")

# zip_longest für unterschiedliche Längen
from itertools import zip_longest

for name, age in zip_longest(names, ages, fillvalue="Unknown"):
    print(f"{name}: {age}")
```

## Unpacking und Extended Unpacking

### ❌ Nicht-Pythonic

```python
# Index-Zugriff
point = (10, 20)
x = point[0]
y = point[1]

# Erstes und letztes Element
numbers = [1, 2, 3, 4, 5]
first = numbers[0]
last = numbers[-1]
```

### ✅ Pythonic

```python
# Tuple Unpacking
point = (10, 20)
x, y = point

# Multiple Assignment
a, b, c = 1, 2, 3

# Extended Unpacking (*)
numbers = [1, 2, 3, 4, 5]
first, *middle, last = numbers
print(first)   # 1
print(middle)  # [2, 3, 4]
print(last)    # 5

# Nur erstes und Rest
head, *tail = numbers
print(head)  # 1
print(tail)  # [2, 3, 4, 5]

# Nur letztes und Rest
*init, last = numbers
print(init)  # [1, 2, 3, 4]
print(last)  # 5

# Ignorieren mit _
first, _, third = (1, 2, 3)
print(first, third)  # 1 3
```

## Dictionary Idioms

### ❌ Nicht-Pythonic

```python
# if-else für Default-Werte
if key in dictionary:
    value = dictionary[key]
else:
    value = default_value

# Doppelter Lookup
if key in dictionary:
    dictionary[key] += 1
else:
    dictionary[key] = 1
```

### ✅ Pythonic

```python
# get() mit Default
value = dictionary.get(key, default_value)

# setdefault für Default + Assignment
dictionary.setdefault(key, []).append(item)

# defaultdict für automatische Defaults
from collections import defaultdict

counter = defaultdict(int)
counter["a"] += 1  # Kein KeyError

groups = defaultdict(list)
groups["team1"].append("player1")

# dict.get() mit Callback
value = dictionary.get(key) or compute_default()

# Dictionary Comprehension
squares = {x: x**2 for x in range(5)}
# {0: 0, 1: 1, 2: 4, 3: 9, 4: 16}

# Dictionary merge (Python 3.9+)
dict1 = {"a": 1, "b": 2}
dict2 = {"b": 3, "c": 4}
merged = dict1 | dict2
# {"a": 1, "b": 3, "c": 4}
```

## String Operations

### ❌ Nicht-Pythonic

```python
# String Concatenation in Loop
result = ""
for word in words:
    result = result + word + " "

# Manuelle Checks
if s[0] == "H" and s[1] == "e" and s[2] == "l":
    print("Starts with Hel")
```

### ✅ Pythonic

```python
# join() für String-Listen
words = ["Hello", "World", "Python"]
result = " ".join(words)
# "Hello World Python"

# startswith/endswith
if s.startswith("Hel"):
    print("Starts with Hel")

if filename.endswith((".txt", ".md")):
    print("Text file")

# f-strings für Formatierung
name = "Max"
age = 30
message = f"{name} is {age} years old"

# String multiplication
separator = "=" * 40

# in operator
if "Python" in text:
    print("Contains Python")
```

## Context Managers (with)

### ❌ Nicht-Pythonic

```python
# Manuelles File-Handling
f = open("file.txt")
try:
    data = f.read()
    # Verarbeitung
finally:
    f.close()

# Mehrere try-finally
lock.acquire()
try:
    # Critical section
    pass
finally:
    lock.release()
```

### ✅ Pythonic

```python
# with für automatisches Cleanup
with open("file.txt") as f:
    data = f.read()
    # File wird automatisch geschlossen

# Mehrere Context Manager
with open("input.txt") as infile, open("output.txt", "w") as outfile:
    outfile.write(infile.read())

# Eigener Context Manager
from contextlib import contextmanager

@contextmanager
def timer(name):
    import time
    start = time.time()
    yield
    end = time.time()
    print(f"{name} took {end - start:.2f}s")

with timer("Operation"):
    # Code hier
    pass
```

## Truthiness und Falsiness

### ❌ Nicht-Pythonic

```python
# Explizite Vergleiche mit True/False
if flag == True:
    pass

if len(items) > 0:
    pass

if items != []:
    pass

if value != None:
    pass
```

### ✅ Pythonic

```python
# Direkte Wahrheitswert-Prüfung
if flag:  # if flag == True
    pass

if items:  # if len(items) > 0
    pass

if not items:  # if items == []
    pass

if value is not None:  # Explizit None prüfen
    pass

# Falsy values in Python:
# False, None, 0, 0.0, "", [], {}, set()
# Alles andere ist truthy
```

## EAFP vs LBYL

**EAFP**: "Easier to Ask for Forgiveness than Permission"  
**LBYL**: "Look Before You Leap"

### ❌ LBYL (nicht pythonic)

```python
# Prüfen vor Zugriff
if key in dictionary:
    value = dictionary[key]
else:
    value = None

if os.path.exists(filename):
    with open(filename) as f:
        data = f.read()
```

### ✅ EAFP (pythonic)

```python
# Exception Handling
try:
    value = dictionary[key]
except KeyError:
    value = None

try:
    with open(filename) as f:
        data = f.read()
except FileNotFoundError:
    data = None

# Warum? Race conditions vermeiden + klarer Code
```

## Generator Expressions

### ❌ Nicht-Pythonic

```python
# Liste im Memory für einfache Summe
total = sum([x**2 for x in range(1000000)])

# Unnötige Liste
all_uppercase = [s.upper() for s in strings]
for s in all_uppercase:
    print(s)
```

### ✅ Pythonic

```python
# Generator Expression - memory-efficient
total = sum(x**2 for x in range(1000000))

# Generator für Iteration
for s in (s.upper() for s in strings):
    print(s)

# Generator-Funktion
def fibonacci():
    a, b = 0, 1
    while True:
        yield a
        a, b = b, a + b

# Verwendung
for i, fib in enumerate(fibonacci()):
    if i >= 10:
        break
    print(fib)
```

## Chaining Comparisons

### ❌ Nicht-Pythonic

```python
# Mehrere Vergleiche
if x > 0 and x < 10:
    pass

if a < b and b < c:
    pass
```

### ✅ Pythonic

```python
# Chained Comparisons
if 0 < x < 10:
    pass

if a < b < c:
    pass

if 1 <= value <= 100:
    print("In range")

# Auch mit Strings
if "a" <= char <= "z":
    print("Lowercase letter")
```

## Any und All

### ❌ Nicht-Pythonic

```python
# Manuelle Prüfung ob irgendein Element True
has_even = False
for num in numbers:
    if num % 2 == 0:
        has_even = True
        break

# Manuelle Prüfung ob alle True
all_positive = True
for num in numbers:
    if num <= 0:
        all_positive = False
        break
```

### ✅ Pythonic

```python
# any() - mindestens ein Element True
has_even = any(num % 2 == 0 for num in numbers)

# all() - alle Elemente True
all_positive = all(num > 0 for num in numbers)

# Praktische Beispiele
if any(word in text for word in keywords):
    print("Found keyword")

if all(isinstance(x, int) for x in values):
    print("All integers")

# Leere Iterables
print(any([]))  # False
print(all([]))  # True (!)
```

## Praktische Idiome

### Swap Variables

```python
# ❌ Mit temp variable
temp = a
a = b
b = temp

# ✅ Pythonic
a, b = b, a
```

### Default Arguments

```python
# ❌ Mutable Default
def add_item(item, items=[]):  # Gefährlich!
    items.append(item)
    return items

# ✅ None als Default
def add_item(item, items=None):
    if items is None:
        items = []
    items.append(item)
    return items
```

### Iteration mit Index

```python
# ❌ Manual index
index = 0
for item in items:
    print(f"{index}: {item}")
    index += 1

# ✅ enumerate
for index, item in enumerate(items):
    print(f"{index}: {item}")
```

### Dictionary aus Zwei Listen

```python
keys = ["a", "b", "c"]
values = [1, 2, 3]

# ❌ Loop
result = {}
for i in range(len(keys)):
    result[keys[i]] = values[i]

# ✅ zip + dict
result = dict(zip(keys, values))
# {"a": 1, "b": 2, "c": 3}
```

### List Flattening

```python
nested = [[1, 2], [3, 4], [5, 6]]

# ❌ Nested loops
flat = []
for sublist in nested:
    for item in sublist:
        flat.append(item)

# ✅ List comprehension
flat = [item for sublist in nested for item in sublist]

# ✅ itertools.chain
from itertools import chain
flat = list(chain.from_iterable(nested))
```

### Conditional Expression (Ternary)

```python
# ❌ if-else Block
if condition:
    value = x
else:
    value = y

# ✅ Ternary expression
value = x if condition else y

# Beispiel
status = "active" if user.is_active else "inactive"
```

### Set für Membership Tests

```python
valid_colors = ["red", "green", "blue", "yellow"]

# ❌ Liste für häufige Lookups
if color in valid_colors:  # O(n)
    pass

# ✅ Set für O(1) Lookup
valid_colors = {"red", "green", "blue", "yellow"}
if color in valid_colors:  # O(1)
    pass
```

## Anti-Patterns zu vermeiden

### 1. Unnötige Else nach Return

```python
# ❌ Unnötiges else
def check(value):
    if value > 0:
        return "positive"
    else:
        return "non-positive"

# ✅ Ohne else
def check(value):
    if value > 0:
        return "positive"
    return "non-positive"
```

### 2. Unnötige Lambda

```python
# ❌ Lambda für einfache Operationen
squares = map(lambda x: x**2, numbers)
uppercased = map(lambda s: s.upper(), strings)

# ✅ List comprehension oder direkte Referenz
squares = [x**2 for x in numbers]
uppercased = [s.upper() for s in strings]

# ❌ Lambda wrapper
sorted_items = sorted(items, key=lambda x: x.value)

# ✅ operator.attrgetter
from operator import attrgetter
sorted_items = sorted(items, key=attrgetter("value"))
```

### 3. Überkomplizierte Comprehensions

```python
# ❌ Zu komplex
result = [
    process(
        transform(x) if condition(x) else default(x)
    )
    for x in items
    if validate(x) and check(x)
]

# ✅ In Funktion auslagern
def process_item(x):
    if not (validate(x) and check(x)):
        return None
    value = transform(x) if condition(x) else default(x)
    return process(value)

result = [r for r in map(process_item, items) if r is not None]
```

## Performance-Hinweise

```python
# Set statt Liste für Membership
large_list = list(range(10000))
value in large_list  # O(n) - langsam

large_set = set(range(10000))
value in large_set  # O(1) - schnell

# Generator statt Liste wenn möglich
sum([x**2 for x in range(1000000)])  # Viel Memory
sum(x**2 for x in range(1000000))    # Wenig Memory

# String join statt Concatenation
# Langsam
result = ""
for s in strings:
    result += s

# Schnell
result = "".join(strings)

# Local variable statt global lookup
# Langsam in Loop
for i in range(1000):
    result = math.sqrt(i)

# Schneller
sqrt = math.sqrt  # Local reference
for i in range(1000):
    result = sqrt(i)
```

## Zusammenfassung: Pythonic Principles

1. **Explizit ist besser als implizit** - Klare Intention
2. **Lesbarkeit zählt** - Code wird öfter gelesen als geschrieben
3. **Einfach ist besser als komplex** - KISS-Prinzip
4. **Flach ist besser als verschachtelt** - Vermeide tiefe Verschachtelung
5. **Es sollte einen - und vorzugsweise nur einen - offensichtlichen Weg geben** - Ein pythonischer Weg

## Übungsaufgaben

### Übung 1: Refactoring (Leicht)

Mache diesen Code pythonic:

```python
squares = []
for i in range(10):
    if i % 2 == 0:
        squares.append(i * i)

index = 0
for item in items:
    print(str(index) + ": " + item)
    index = index + 1
```

### Übung 2: Dictionary Operations (Mittel)

Implementiere pythonic:
- Gruppiere Wörter nach Länge
- Zähle Buchstaben-Häufigkeit
- Merge mehrere Dicts

### Übung 3: List Processing (Mittel)

Pythonic implementation:
- Flatten nested list
- Remove duplicates (preserve order)
- Chunk list in groups of n

### Übung 4: Generator (Schwer)

Erstelle Generator für:
- Fibonacci-Zahlen
- Prime-Zahlen
- Sliding window über Sequence

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [PEP 8 - Style Guide](https://peps.python.org/pep-0008/) - Python Code Style
- [The Zen of Python](https://peps.python.org/pep-0020/) - Python Philosophie
- [Python Idioms](https://docs.python-guide.org/writing/style/) - Best Practices Guide

## Verwandte Themen

- [[06_Praxis/01_Best_Practices/02_Code Smells vermeiden|Code Smells vermeiden]] - Anti-Patterns
- [[06_Praxis/01_Best_Practices/03_Datenstruktur Auswahl|Datenstruktur Auswahl]] - Richtige Struktur wählen
- [[02_Sequenzen/04_Comprehensions/01_List Comprehensions|List Comprehensions]] - Details
- [[03_Mappings_und_Sets/01_Dictionaries/01_Dict Basics|Dict Basics]] - Dictionary Idiome

---
← [[05_Pattern_Matching/02_Type_Hints/05_Übungen Type Hints|Übungen Type Hints]] | [[INDEX|📑 Index]] | [[06_Praxis/01_Best_Practices/02_Code Smells vermeiden|Code Smells vermeiden]] →