---
tags: [python, datenstrukturen, lösungen]
created: 2025-10-02
---

← [[06_Praxis/04_Lösungen/Lösungen Teil 1|Lösungen Teil 1]] | [[INDEX|📑 Index]] | [[06_Praxis/04_Lösungen/Lösungen Teil 3|Lösungen Teil 3]] →

# Lösungen Teil 2: Sequenzielle Datenstrukturen

> [!tip] Hinweis
> Diese Lösungen zeigen idiomatisches Python. Achte auf List Comprehensions und moderne Syntax!

## 02_Sequenzen/01_Listen

### List Basics - Übungen

**Übung 1: List-Operationen**
```python
# Erstelle Liste und führe Operationen durch
zahlen = [1, 2, 3, 4, 5]

# Append
zahlen.append(6)
print(f"Nach append: {zahlen}")  # [1, 2, 3, 4, 5, 6]

# Extend
zahlen.extend([7, 8, 9])
print(f"Nach extend: {zahlen}")  # [1, 2, 3, 4, 5, 6, 7, 8, 9]

# Insert
zahlen.insert(0, 0)
print(f"Nach insert: {zahlen}")  # [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

# Remove
zahlen.remove(5)
print(f"Nach remove: {zahlen}")  # [0, 1, 2, 3, 4, 6, 7, 8, 9]

# Pop
last = zahlen.pop()
first = zahlen.pop(0)
print(f"Gepoppt: {first}, {last}")  # 0, 9
print(f"Nach pop: {zahlen}")  # [1, 2, 3, 4, 6, 7, 8]

# Clear
zahlen_copy = zahlen.copy()
zahlen.clear()
print(f"Nach clear: {zahlen}")  # []
print(f"Copy: {zahlen_copy}")   # [1, 2, 3, 4, 6, 7, 8]
```

**Übung 2: Listen-Manipulation**
```python
def remove_duplicates(lst: list) -> list:
    """Entfernt Duplikate, behält Reihenfolge"""
    seen = set()
    result = []
    for item in lst:
        if item not in seen:
            seen.add(item)
            result.append(item)
    return result

# Oder kürzer mit dict.fromkeys() (Python 3.7+)
def remove_duplicates_v2(lst: list) -> list:
    """Nutzt dict für Ordnung"""
    return list(dict.fromkeys(lst))

test = [1, 2, 2, 3, 4, 3, 5, 1]
print(remove_duplicates(test))     # [1, 2, 3, 4, 5]
print(remove_duplicates_v2(test))  # [1, 2, 3, 4, 5]

def flatten(nested: list) -> list:
    """Flacht verschachtelte Liste ab"""
    result = []
    for item in nested:
        if isinstance(item, list):
            result.extend(flatten(item))  # Rekursiv
        else:
            result.append(item)
    return result

nested = [1, [2, 3], [4, [5, 6]], 7]
print(flatten(nested))  # [1, 2, 3, 4, 5, 6, 7]

def chunk_list(lst: list, size: int) -> list:
    """Teilt Liste in Chunks"""
    return [lst[i:i+size] for i in range(0, len(lst), size)]

numbers = list(range(10))
print(chunk_list(numbers, 3))  # [[0, 1, 2], [3, 4, 5], [6, 7, 8], [9]]
```

**Übung 3: Sortieren**
```python
# Einfaches Sortieren
numbers = [5, 2, 8, 1, 9, 3]
numbers.sort()
print(f"Sortiert: {numbers}")  # [1, 2, 3, 5, 8, 9]

# Reverse
numbers.sort(reverse=True)
print(f"Reverse: {numbers}")  # [9, 8, 5, 3, 2, 1]

# Nach Key
words = ["Python", "is", "awesome", "and", "powerful"]
words.sort(key=len)
print(f"Nach Länge: {words}")  # ['is', 'and', 'Python', 'awesome', 'powerful']

# Komplexere Keys
students = [
    {"name": "Alice", "grade": 85},
    {"name": "Bob", "grade": 92},
    {"name": "Charlie", "grade": 78},
]

students.sort(key=lambda s: s["grade"], reverse=True)
for student in students:
    print(f"{student['name']}: {student['grade']}")
# Output:
# Bob: 92
# Alice: 85
# Charlie: 78

# sorted() für neue Liste
original = [3, 1, 4, 1, 5]
sorted_list = sorted(original)
print(f"Original: {original}")      # [3, 1, 4, 1, 5]
print(f"Sortiert: {sorted_list}")   # [1, 1, 3, 4, 5]
```

**Übung 4: List Comprehensions Intro**
```python
# Quadrate
squares = [x**2 for x in range(10)]
print(f"Quadrate: {squares}")  # [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]

# Mit Filter
even_squares = [x**2 for x in range(10) if x % 2 == 0]
print(f"Gerade Quadrate: {even_squares}")  # [0, 4, 16, 36, 64]

# String-Verarbeitung
words = ["hello", "world", "python"]
upper_words = [w.upper() for w in words]
print(f"Uppercase: {upper_words}")  # ['HELLO', 'WORLD', 'PYTHON']

# Verschachtelt
matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
flattened = [num for row in matrix for num in row]
print(f"Flattened: {flattened}")  # [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

**Übung 5: Stack und Queue**
```python
# Stack (LIFO) mit Liste
class Stack:
    """Stack-Implementierung mit Liste"""
    
    def __init__(self):
        self._items = []
    
    def push(self, item):
        """Fügt Element hinzu"""
        self._items.append(item)
    
    def pop(self):
        """Entfernt und gibt letztes Element zurück"""
        if self.is_empty():
            raise IndexError("Pop from empty stack")
        return self._items.pop()
    
    def peek(self):
        """Gibt letztes Element zurück ohne zu entfernen"""
        if self.is_empty():
            raise IndexError("Peek from empty stack")
        return self._items[-1]
    
    def is_empty(self):
        return len(self._items) == 0
    
    def __len__(self):
        return len(self._items)
    
    def __repr__(self):
        return f"Stack({self._items})"

# Test Stack
stack = Stack()
stack.push(1)
stack.push(2)
stack.push(3)
print(stack)           # Stack([1, 2, 3])
print(stack.pop())     # 3
print(stack.peek())    # 2
print(len(stack))      # 2

# Queue (FIFO) mit Liste (ineffizient!)
# Besser: collections.deque (siehe Teil 4)
class SimpleQueue:
    """Einfache Queue (ineffizient)"""
    
    def __init__(self):
        self._items = []
    
    def enqueue(self, item):
        """Fügt am Ende hinzu"""
        self._items.append(item)
    
    def dequeue(self):
        """Entfernt vom Anfang - O(n)!"""
        if self.is_empty():
            raise IndexError("Dequeue from empty queue")
        return self._items.pop(0)  # O(n) - ineffizient!
    
    def is_empty(self):
        return len(self._items) == 0

queue = SimpleQueue()
queue.enqueue("A")
queue.enqueue("B")
queue.enqueue("C")
print(queue.dequeue())  # A
print(queue.dequeue())  # B
```

## 02_Sequenzen/02_Tupel

### Tuple Basics - Übungen

**Übung 1: Tuple Unpacking**
```python
# Einfaches Unpacking
coordinates = (10, 20)
x, y = coordinates
print(f"x={x}, y={y}")  # x=10, y=20

# Mit *
numbers = (1, 2, 3, 4, 5)
first, *middle, last = numbers
print(f"Erste: {first}")      # 1
print(f"Mitte: {middle}")     # [2, 3, 4]
print(f"Letzte: {last}")      # 5

# Funktion mit mehreren Rückgabewerten
def min_max(numbers):
    """Gibt Min und Max zurück"""
    return min(numbers), max(numbers)

minimum, maximum = min_max([3, 1, 4, 1, 5])
print(f"Min: {minimum}, Max: {maximum}")  # Min: 1, Max: 5

# Swap ohne temporäre Variable
a, b = 5, 10
a, b = b, a
print(f"a={a}, b={b}")  # a=10, b=5

# Enumerate mit Unpacking
fruits = ["Apple", "Banana", "Cherry"]
for index, fruit in enumerate(fruits):
    print(f"{index}: {fruit}")
```

**Übung 2: Named Tuples**
```python
from collections import namedtuple

# Definiere Named Tuple
Point = namedtuple('Point', ['x', 'y'])
Person = namedtuple('Person', ['name', 'age', 'city'])

# Erstellen
p = Point(10, 20)
print(f"Point: x={p.x}, y={p.y}")  # Point: x=10, y=20

person = Person("Alice", 30, "Berlin")
print(f"{person.name} ist {person.age} Jahre alt")  # Alice ist 30 Jahre alt

# Immutable
try:
    person.age = 31
except AttributeError as e:
    print(f"Fehler: {e}")  # can't set attribute

# _replace() für "Änderungen"
person2 = person._replace(age=31)
print(f"Original: {person.age}")  # 30
print(f"Neu: {person2.age}")      # 31

# _asdict() für Dict-Konvertierung
person_dict = person._asdict()
print(person_dict)  # {'name': 'Alice', 'age': 30, 'city': 'Berlin'}

# Praktisches Beispiel: Farben
Color = namedtuple('Color', ['red', 'green', 'blue'])

# Vordefinierte Farben
RED = Color(255, 0, 0)
GREEN = Color(0, 255, 0)
BLUE = Color(0, 0, 255)

def blend_colors(c1: Color, c2: Color) -> Color:
    """Mischt zwei Farben"""
    return Color(
        (c1.red + c2.red) // 2,
        (c1.green + c2.green) // 2,
        (c1.blue + c2.blue) // 2
    )

yellow = blend_colors(RED, GREEN)
print(f"Yellow: RGB({yellow.red}, {yellow.green}, {yellow.blue})")
# Yellow: RGB(127, 127, 0)
```

**Übung 3: Tuple als Dict-Keys**
```python
# Koordinaten als Keys
locations = {}

locations[(0, 0)] = "Origin"
locations[(10, 20)] = "Point A"
locations[(30, 40)] = "Point B"

print(locations[(0, 0)])  # Origin

# Grid-System
grid = {}

for x in range(3):
    for y in range(3):
        grid[(x, y)] = f"Cell({x},{y})"

print(grid[(1, 1)])  # Cell(1,1)

# Praktisch: Memoization
def fibonacci_memo():
    """Fibonacci mit Memoization"""
    cache = {}
    
    def fib(n):
        if n in cache:
            return cache[n]
        
        if n <= 1:
            result = n
        else:
            result = fib(n-1) + fib(n-2)
        
        cache[n] = result
        return result
    
    return fib

fib = fibonacci_memo()
print(f"fib(100) = {fib(100)}")  # Sehr schnell durch Cache
```

**Übung 4: Tuple vs. Liste**
```python
import sys

# Memory-Vergleich
tuple_data = (1, 2, 3, 4, 5)
list_data = [1, 2, 3, 4, 5]

print(f"Tuple: {sys.getsizeof(tuple_data)} bytes")  # Kleiner
print(f"List: {sys.getsizeof(list_data)} bytes")    # Größer

# Performance-Vergleich
import timeit

# Tuple-Erstellung
tuple_time = timeit.timeit(
    "t = (1, 2, 3, 4, 5)",
    number=1000000
)

# List-Erstellung
list_time = timeit.timeit(
    "l = [1, 2, 3, 4, 5]",
    number=1000000
)

print(f"\nTuple-Erstellung: {tuple_time:.4f}s")
print(f"List-Erstellung: {list_time:.4f}s")
print(f"Tuple ist {list_time/tuple_time:.2f}x schneller")

# Wann welche verwenden?
# Tuple: Unveränderliche Daten, Dict-Keys, Rückgabewerte
# Liste: Veränderliche Daten, dynamische Größe
```

## 02_Sequenzen/03_Slicing

### Slicing - Übungen

**Übung 1: Erweiterte Slicing-Techniken**
```python
numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

# Standard
print(numbers[2:7])      # [2, 3, 4, 5, 6]
print(numbers[:5])       # [0, 1, 2, 3, 4]
print(numbers[5:])       # [5, 6, 7, 8, 9]

# Mit Step
print(numbers[::2])      # [0, 2, 4, 6, 8] - jede 2.
print(numbers[1::2])     # [1, 3, 5, 7, 9] - ungerade
print(numbers[::3])      # [0, 3, 6, 9]

# Reverse
print(numbers[::-1])     # [9, 8, 7, 6, 5, 4, 3, 2, 1, 0]
print(numbers[8:2:-1])   # [8, 7, 6, 5, 4, 3]

# Negative Indices
print(numbers[-3:])      # [7, 8, 9]
print(numbers[:-3])      # [0, 1, 2, 3, 4, 5, 6]
print(numbers[-5:-2])    # [5, 6, 7]

# Copy
copy = numbers[:]
print(f"Copy: {copy}")
print(f"Ist gleich: {copy == numbers}")
print(f"Ist dasselbe: {copy is numbers}")  # False!
```

**Übung 2: Slicing für Manipulation**
```python
# Ersetzen von Bereichen
lst = [1, 2, 3, 4, 5]
lst[1:3] = [20, 30]
print(lst)  # [1, 20, 30, 4, 5]

# Einfügen
lst = [1, 2, 5]
lst[2:2] = [3, 4]
print(lst)  # [1, 2, 3, 4, 5]

# Löschen
lst = [1, 2, 3, 4, 5]
del lst[1:3]
print(lst)  # [1, 4, 5]

# Reverse in-place
lst = [1, 2, 3, 4, 5]
lst[:] = lst[::-1]
print(lst)  # [5, 4, 3, 2, 1]

# Jedes zweite Element ersetzen
lst = [1, 2, 3, 4, 5, 6]
lst[::2] = [10, 30, 50]
print(lst)  # [10, 2, 30, 4, 50, 6]
```

**Übung 3: String-Slicing**
```python
text = "Python Programming"

# Substring
print(text[0:6])    # Python
print(text[7:])     # Programming

# Jedes 2. Zeichen
print(text[::2])    # Pto rgamn

# Reverse
print(text[::-1])   # gnimmargorP nohtyP

# Praktisch: Palindrom-Check
def is_palindrome(s):
    s = s.lower().replace(" ", "")
    return s == s[::-1]

print(is_palindrome("Anna"))  # True

# Text-Rotation
def rotate_string(s, n):
    """Rotiert String um n Positionen"""
    n = n % len(s)  # Handle n > len
    return s[n:] + s[:n]

print(rotate_string("Python", 2))  # thonPy
print(rotate_string("Hello", -1))  # oHell
```

**Übung 4: 2D-Arrays (Nested Lists)**
```python
# Matrix
matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]

# Zeile
print(matrix[0])      # [1, 2, 3]
print(matrix[-1])     # [7, 8, 9]

# Element
print(matrix[1][1])   # 5

# Spalte (mit List Comprehension)
col = [row[0] for row in matrix]
print(col)  # [1, 4, 7]

# Submatrix
submatrix = [row[1:3] for row in matrix[0:2]]
print(submatrix)  # [[2, 3], [5, 6]]

# Diagonale
diagonal = [matrix[i][i] for i in range(len(matrix))]
print(diagonal)  # [1, 5, 9]

# Transpose
def transpose(matrix):
    """Transponiert Matrix"""
    return [[matrix[j][i] for j in range(len(matrix))]
            for i in range(len(matrix[0]))]

transposed = transpose(matrix)
for row in transposed:
    print(row)
# [1, 4, 7]
# [2, 5, 8]
# [3, 6, 9]
```

## 02_Sequenzen/04_Comprehensions

### List Comprehensions - Übungen

**Übung 1: Grundlegende Comprehensions**
```python
# Quadrate
squares = [x**2 for x in range(10)]
print(squares)  # [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]

# Mit Bedingung
even_squares = [x**2 for x in range(10) if x % 2 == 0]
print(even_squares)  # [0, 4, 16, 36, 64]

# Mit if-else
labels = ["Even" if x % 2 == 0 else "Odd" for x in range(5)]
print(labels)  # ['Even', 'Odd', 'Even', 'Odd', 'Even']

# String-Operationen
words = ["hello", "world", "python"]
uppercase = [w.upper() for w in words]
print(uppercase)  # ['HELLO', 'WORLD', 'PYTHON']

# Filtern und Transformieren
numbers = [1, -2, 3, -4, 5, -6]
positive_doubled = [x * 2 for x in numbers if x > 0]
print(positive_doubled)  # [2, 6, 10]
```

**Übung 2: Verschachtelte Comprehensions**
```python
# Kombinationen
colors = ["red", "blue"]
sizes = ["S", "M", "L"]
products = [(color, size) for color in colors for size in sizes]
print(products)
# [('red', 'S'), ('red', 'M'), ('red', 'L'), 
#  ('blue', 'S'), ('blue', 'M'), ('blue', 'L')]

# Matrix flatten
matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
flat = [num for row in matrix for num in row]
print(flat)  # [1, 2, 3, 4, 5, 6, 7, 8, 9]

# Mit Bedingung
even_from_matrix = [num for row in matrix for num in row if num % 2 == 0]
print(even_from_matrix)  # [2, 4, 6, 8]

# Koordinaten-Grid
grid = [(x, y) for x in range(3) for y in range(3)]
print(grid)
# [(0,0), (0,1), (0,2), (1,0), (1,1), (1,2), (2,0), (2,1), (2,2)]
```

**Übung 3: Dict Comprehensions**
```python
# Squares Dict
squares_dict = {x: x**2 for x in range(6)}
print(squares_dict)  # {0: 0, 1: 1, 2: 4, 3: 9, 4: 16, 5: 25}

# Von Listen zu Dict
keys = ['a', 'b', 'c']
values = [1, 2, 3]
mapping = {k: v for k, v in zip(keys, values)}
print(mapping)  # {'a': 1, 'b': 2, 'c': 3}

# Dict invertieren
original = {'a': 1, 'b': 2, 'c': 3}
inverted = {v: k for k, v in original.items()}
print(inverted)  # {1: 'a', 2: 'b', 3: 'c'}

# Filtern
data = {'a': 1, 'b': 2, 'c': 3, 'd': 4}
filtered = {k: v for k, v in data.items() if v > 2}
print(filtered)  # {'c': 3, 'd': 4}

# String-Längen
words = ["apple", "banana", "cherry"]
lengths = {word: len(word) for word in words}
print(lengths)  # {'apple': 5, 'banana': 6, 'cherry': 6}
```

**Übung 4: Set Comprehensions**
```python
# Unique Squares
numbers = [1, 2, 2, 3, 3, 3, 4, 4, 4, 4]
unique_squares = {x**2 for x in numbers}
print(unique_squares)  # {1, 4, 9, 16}

# Unique Lengths
words = ["apple", "pie", "banana", "cat", "cherry"]
lengths = {len(w) for w in words}
print(lengths)  # {3, 5, 6}

# Zeichen aus String
text = "Hello World"
unique_chars = {c.lower() for c in text if c.isalpha()}
print(unique_chars)  # {'d', 'e', 'h', 'l', 'o', 'r', 'w'}
```

**Übung 5: Generator Expressions**
```python
# Generator statt Liste (Memory-effizient)
squares_gen = (x**2 for x in range(1000000))  # Kein Memory-Verbrauch
print(type(squares_gen))  # <class 'generator'>

# Iterieren
for square in (x**2 for x in range(5)):
    print(square, end=" ")  # 0 1 4 9 16
print()

# sum() mit Generator
total = sum(x**2 for x in range(100))
print(f"Summe: {total}")

# any() und all() mit Generator
numbers = range(100)
has_even = any(x % 2 == 0 for x in numbers)
all_positive = all(x >= 0 for x in numbers)
print(f"Hat gerade: {has_even}")      # True
print(f"Alle positiv: {all_positive}") # True

# Memory-Vergleich
import sys

list_comp = [x**2 for x in range(10000)]
gen_expr = (x**2 for x in range(10000))

print(f"Liste: {sys.getsizeof(list_comp)} bytes")
print(f"Generator: {sys.getsizeof(gen_expr)} bytes")
# Liste ist viel größer!
```

## 02_Sequenzen/05_Range

### Range - Übungen

**Übung 1: Range-Varianten**
```python
# range(stop)
for i in range(5):
    print(i, end=" ")  # 0 1 2 3 4
print()

# range(start, stop)
for i in range(2, 8):
    print(i, end=" ")  # 2 3 4 5 6 7
print()

# range(start, stop, step)
for i in range(0, 10, 2):
    print(i, end=" ")  # 0 2 4 6 8
print()

# Negative Steps
for i in range(10, 0, -1):
    print(i, end=" ")  # 10 9 8 7 6 5 4 3 2 1
print()

# Range zu Liste
numbers = list(range(5))
print(numbers)  # [0, 1, 2, 3, 4]
```

**Übung 2: Range mit Indexing**
```python
r = range(10, 20)

# Indexing
print(r[0])    # 10
print(r[5])    # 15
print(r[-1])   # 19

# Slicing
print(list(r[2:5]))  # [12, 13, 14]

# Length
print(len(r))  # 10

# Membership
print(15 in r)  # True
print(25 in r)  # False

# Index
print(r.index(15))  # 5

# Count
print(r.count(15))  # 1
```

**Übung 3: Praktische Anwendungen**
```python
# Countdown
def countdown(n):
    """Countdown von n bis 1"""
    for i in range(n, 0, -1):
        print(f"{i}...", end=" ")
    print("Go!")

countdown(5)  # 5... 4... 3... 2... 1... Go!

# Fibonacci mit Range
def fibonacci_range(n):
    """Erste n Fibonacci-Zahlen"""
    a, b = 0, 1
    result = []
    for _ in range(n):
        result.append(a)
        a, b = b, a + b
    return result

print(fibonacci_range(10))  # [0, 1, 1, 2, 3, 5, 8, 13, 21, 34]

# Tabelle drucken
def print_table(n):
    """Druckt Multiplikationstabelle"""
    for i in range(1, n+1):
        for j in range(1, n+1):
            print(f"{i*j:4d}", end="")
        print()

print_table(5)
```

**Übung 4: enumerate vs. range**
```python
fruits = ["Apple", "Banana", "Cherry"]

# Mit range (nicht idiomatisch)
for i in range(len(fruits)):
    print(f"{i}: {fruits[i]}")

# Mit enumerate (idiomatisch!)
for i, fruit in enumerate(fruits):
    print(f"{i}: {fruit}")

# enumerate mit Start
for i, fruit in enumerate(fruits, start=1):
    print(f"{i}. {fruit}")

# zip statt range für zwei Listen
names = ["Alice", "Bob", "Charlie"]
ages = [25, 30, 35]

# Nicht idiomatisch
for i in range(len(names)):
    print(f"{names[i]} ist {ages[i]} Jahre alt")

# Idiomatisch
for name, age in zip(names, ages):
    print(f"{name} ist {age} Jahre alt")
```

## Verwandte Themen

- [[02_Sequenzen/01_Listen/01_List Basics|Listen]]
- [[02_Sequenzen/02_Tupel/01_Tuple Basics|Tupel]]
- [[02_Sequenzen/03_Slicing/01_Slicing Grundlagen|Slicing]]
- [[02_Sequenzen/04_Comprehensions/01_List Comprehensions|Comprehensions]]

---
← [[06_Praxis/04_Lösungen/Lösungen Teil 1|Lösungen Teil 1]] | [[INDEX|📑 Index]] | [[06_Praxis/04_Lösungen/Lösungen Teil 3|Lösungen Teil 3]] →