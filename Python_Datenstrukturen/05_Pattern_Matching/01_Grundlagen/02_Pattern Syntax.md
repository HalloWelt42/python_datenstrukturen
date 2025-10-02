---
tags: [python, datenstrukturen, pattern-matching, python312]
created: 2025-10-02
---

← [[05_Pattern_Matching/01_Grundlagen/01_Match Statement|Match Statement]] | [[INDEX|📑 Index]] | [[05_Pattern_Matching/01_Grundlagen/03_Übungen Pattern Matching|Übungen Pattern Matching]] →

# Pattern Syntax

> [!tip] Lernziel
> Du kennst alle Pattern-Arten im Detail und kannst komplexe Pattern-Kombinationen erstellen.

## Übersicht der Pattern-Typen

Python's Pattern Matching unterstützt verschiedene Pattern-Typen:

| Pattern-Typ | Syntax | Beschreibung |
|-------------|--------|--------------|
| Literal | `42`, `"text"` | Exakter Wert-Vergleich |
| Capture | `x`, `name` | Bindet Wert an Variable |
| Wildcard | `_` | Matcht alles, bindet nicht |
| Sequence | `[x, y, z]` | Listen/Tupel-Struktur |
| Mapping | `{"key": value}` | Dictionary-Struktur |
| Class | `Point(x=1, y=2)` | Objekt-Matching |
| OR | `1 \| 2 \| 3` | Alternative Patterns |
| AS | `pattern as name` | Pattern + Binding |
| Guard | `case x if x > 0:` | Pattern + Bedingung |

## Literal Patterns

### Zahlen und Strings

```python
def check_value(val):
    match val:
        case 0:
            return "zero"
        case 1:
            return "one"
        case 3.14:
            return "pi"
        case "hello":
            return "greeting"
        case True:  # Achtung: True == 1!
            return "boolean true"
        case False:
            return "boolean false"
        case None:
            return "nothing"
        case _:
            return "other"

print(check_value(0))       # "zero"
print(check_value(3.14))    # "pi"
print(check_value("hello")) # "greeting"
print(check_value(None))    # "nothing"
```

### Konstanten aus Modulen

```python
import math

def check_constant(val):
    match val:
        case math.pi:  # Konstante aus Modul
            return "Pi"
        case math.e:
            return "Euler's number"
        case _:
            return "other"

# Enums als Literal
from enum import Enum

class Status(Enum):
    ACTIVE = 1
    INACTIVE = 2

def check_status(status):
    match status:
        case Status.ACTIVE:  # Enum-Wert als Literal
            return "Active"
        case Status.INACTIVE:
            return "Inactive"
        case _:
            return "Unknown"

print(check_status(Status.ACTIVE))  # "Active"
```

## Capture Patterns

### Einfache Captures

```python
def describe(value):
    match value:
        case x:  # Bindet value an x
            return f"Got value: {x}"

print(describe(42))      # "Got value: 42"
print(describe("hello")) # "Got value: hello"

# Mehrfache Captures
def process_pair(pair):
    match pair:
        case (x, y):  # Bindet beide Werte
            return f"First: {x}, Second: {y}"

print(process_pair((1, 2)))  # "First: 1, Second: 2"
```

### Captures mit Constraints

```python
def categorize_pair(pair):
    match pair:
        case (0, y):  # Erstes ist 0, zweites gebunden
            return f"Y-axis at {y}"
        case (x, 0):  # Zweites ist 0, erstes gebunden
            return f"X-axis at {x}"
        case (x, y) if x == y:  # Beide gleich
            return f"Diagonal at {x}"
        case (x, y):  # Beide gebunden
            return f"Point at ({x}, {y})"

print(categorize_pair((0, 5)))  # "Y-axis at 5"
print(categorize_pair((3, 0)))  # "X-axis at 3"
print(categorize_pair((4, 4)))  # "Diagonal at 4"
print(categorize_pair((2, 7)))  # "Point at (2, 7)"
```

## Wildcard Pattern (_)

### Ignorieren von Werten

```python
def parse_command(cmd):
    match cmd:
        case ["load", filename, _]:  # Dritter Parameter ignoriert
            return f"Load {filename}"
        case ["save", _, format]:  # Zweiter Parameter ignoriert
            return f"Save as {format}"
        case [_, _, _]:  # Genau 3 Elemente, alle ignoriert
            return "Three-element command"
        case _:  # Default
            return "Unknown"

print(parse_command(["load", "file.txt", "readonly"]))  # "Load file.txt"
print(parse_command(["save", "data", "json"]))          # "Save as json"
```

### Wildcard vs. Capture

```python
# Wildcard - bindet NICHT
match value:
    case (_, y):
        # _ ist nicht verfügbar, nur y
        print(y)

# Capture - bindet
match value:
    case (x, y):
        # x und y sind verfügbar
        print(x, y)
```

## Sequence Patterns

### Listen und Tupel

```python
def analyze_sequence(seq):
    match seq:
        case []:
            return "Empty"
        case [x]:
            return f"Single: {x}"
        case [x, y]:
            return f"Pair: {x}, {y}"
        case [x, y, z]:
            return f"Triple: {x}, {y}, {z}"
        case _:
            return f"Long sequence with {len(seq)} items"

print(analyze_sequence([]))        # "Empty"
print(analyze_sequence([1]))       # "Single: 1"
print(analyze_sequence([1, 2]))    # "Pair: 1, 2"
print(analyze_sequence([1,2,3,4])) # "Long sequence with 4 items"
```

### Star Patterns (*)

```python
def process_list(items):
    match items:
        case []:
            return "Empty"
        case [first, *rest]:  # Rest der Liste
            return f"First: {first}, Rest: {rest}"

print(process_list([]))           # "Empty"
print(process_list([1]))          # "First: 1, Rest: []"
print(process_list([1, 2, 3, 4])) # "First: 1, Rest: [2, 3, 4]"

# Star in der Mitte
def extract_middle(items):
    match items:
        case [first, *middle, last]:
            return f"First: {first}, Middle: {middle}, Last: {last}"
        case _:
            return "Need at least 2 items"

print(extract_middle([1, 2, 3, 4, 5]))  # "First: 1, Middle: [2, 3, 4], Last: 5"
print(extract_middle([1, 2]))           # "First: 1, Middle: [], Last: 2"

# Star am Ende
def split_head_tail(items):
    match items:
        case [head, *tail]:
            return f"Head: {head}, Tail: {tail}"

# Star am Anfang
def split_init_last(items):
    match items:
        case [*init, last]:
            return f"Init: {init}, Last: {last}"

print(split_init_last([1, 2, 3]))  # "Init: [1, 2], Last: 3"
```

### Verschachtelte Sequences

```python
def analyze_nested(data):
    match data:
        case [[x, y], [z, w]]:  # 2x2 Matrix
            return f"2x2 matrix: {x},{y} / {z},{w}"
        case [[x], [y], [z]]:  # 3x1 Vektor
            return f"3x1 vector: {x}, {y}, {z}"
        case [first, *rest] if all(isinstance(x, list) for x in rest):
            return f"List of lists, first: {first}"
        case _:
            return "Other structure"

print(analyze_nested([[1, 2], [3, 4]]))     # "2x2 matrix: 1,2 / 3,4"
print(analyze_nested([[1], [2], [3]]))      # "3x1 vector: 1, 2, 3"
print(analyze_nested([[1], [2, 3], [4]]))   # "List of lists, first: [1]"
```

## Mapping Patterns (Dictionaries)

### Basis Dictionary Matching

```python
def process_config(config):
    match config:
        case {"mode": "debug"}:
            return "Debug mode enabled"
        case {"mode": "production", "port": port}:
            return f"Production on port {port}"
        case {"host": host, "port": port}:
            return f"Server at {host}:{port}"
        case _:
            return "Invalid config"

print(process_config({"mode": "debug"}))
# "Debug mode enabled"

print(process_config({"mode": "production", "port": 8080}))
# "Production on port 8080"

# Zusätzliche Keys werden ignoriert
print(process_config({"host": "localhost", "port": 3000, "ssl": True}))
# "Server at localhost:3000"
```

### Rest Pattern (**rest)

```python
def extract_user_data(data):
    match data:
        case {"name": name, "age": age, **rest}:
            return f"User {name} ({age}), Extra: {rest}"
        case _:
            return "Invalid data"

print(extract_user_data({
    "name": "Max",
    "age": 30,
    "city": "Berlin",
    "email": "max@example.com"
}))
# "User Max (30), Extra: {'city': 'Berlin', 'email': 'max@example.com'}"

# Ohne rest werden Extra-Keys ignoriert
def simple_match(data):
    match data:
        case {"name": name}:  # Nur name extrahiert
            return name

print(simple_match({"name": "Anna", "age": 25}))  # "Anna"
```

### Verschachtelte Mappings

```python
def process_nested_config(config):
    match config:
        case {"database": {"host": host, "port": port}}:
            return f"DB at {host}:{port}"
        case {"server": {"ssl": True, "port": port}}:
            return f"Secure server on {port}"
        case {"api": {"version": version, "endpoints": endpoints}}:
            return f"API v{version} with {len(endpoints)} endpoints"
        case _:
            return "Unknown config"

print(process_nested_config({
    "database": {"host": "localhost", "port": 5432}
}))
# "DB at localhost:5432"

print(process_nested_config({
    "api": {
        "version": "2.0",
        "endpoints": ["/users", "/posts", "/comments"]
    }
}))
# "API v2.0 with 3 endpoints"
```

## Class Patterns

### Dataclass Matching

```python
from dataclasses import dataclass

@dataclass
class Point:
    x: float
    y: float

@dataclass
class Circle:
    center: Point
    radius: float

def describe_shape(shape):
    match shape:
        case Point(x=0, y=0):
            return "Origin"
        case Point(x=0, y=y):
            return f"Y-axis at {y}"
        case Point(x=x, y=0):
            return f"X-axis at {x}"
        case Point(x=x, y=y) if x == y:
            return f"Diagonal at {x}"
        case Point(x=x, y=y):
            return f"Point ({x}, {y})"
        case Circle(center=Point(x=0, y=0), radius=r):
            return f"Circle at origin, r={r}"
        case Circle(center=c, radius=r):
            return f"Circle at {c}, r={r}"
        case _:
            return "Unknown shape"

print(describe_shape(Point(0, 0)))              # "Origin"
print(describe_shape(Point(3, 3)))              # "Diagonal at 3"
print(describe_shape(Circle(Point(0, 0), 5)))   # "Circle at origin, r=5"
print(describe_shape(Circle(Point(2, 3), 10)))  # "Circle at Point(x=2, y=3), r=10"
```

### Named Tuple Matching

```python
from typing import NamedTuple

class Person(NamedTuple):
    name: str
    age: int
    city: str

def describe_person(person):
    match person:
        case Person(name=name, age=age) if age < 18:
            return f"{name} is a minor"
        case Person(name=name, age=age, city="Berlin"):
            return f"{name} ({age}) lives in Berlin"
        case Person(name=name, age=age, city=city):
            return f"{name} ({age}) from {city}"

print(describe_person(Person("Max", 15, "Munich")))
# "Max is a minor"

print(describe_person(Person("Anna", 30, "Berlin")))
# "Anna (30) lives in Berlin"
```

### Beliebige Klassen

```python
class User:
    def __init__(self, name, role):
        self.name = name
        self.role = role
    
    # __match_args__ definiert welche Attribute gematcht werden können
    __match_args__ = ("name", "role")

def check_permission(user, action):
    match (user, action):
        case (User(role="admin"), _):
            return "Admin can do anything"
        case (User(role="editor"), "read" | "write"):
            return "Editor can read and write"
        case (User(role="viewer"), "read"):
            return "Viewer can read"
        case _:
            return "Permission denied"

admin = User("Alice", "admin")
editor = User("Bob", "editor")
viewer = User("Charlie", "viewer")

print(check_permission(admin, "delete"))    # "Admin can do anything"
print(check_permission(editor, "write"))    # "Editor can read and write"
print(check_permission(viewer, "write"))    # "Permission denied"
```

## OR Patterns (|)

### Mehrere Literale

```python
def check_status_code(code):
    match code:
        case 200 | 201 | 204:
            return "Success"
        case 400 | 401 | 403 | 404:
            return "Client Error"
        case 500 | 502 | 503:
            return "Server Error"
        case _:
            return "Unknown"

print(check_status_code(200))  # "Success"
print(check_status_code(404))  # "Client Error"
```

### OR mit Captures

```python
def categorize_value(val):
    match val:
        case 0 | 1 | 2 as small:  # Bindet zu 'small'
            return f"Small: {small}"
        case str() | bytes() as text_like:  # Bindet zu 'text_like'
            return f"Text-like: {text_like}"
        case list() | tuple() as seq:  # Bindet zu 'seq'
            return f"Sequence: {seq}"
        case _:
            return "Other"

print(categorize_value(1))        # "Small: 1"
print(categorize_value("hello"))  # "Text-like: hello"
print(categorize_value([1, 2]))   # "Sequence: [1, 2]"
```

### OR mit Patterns

```python
def analyze_data(data):
    match data:
        case [] | None:  # Leer oder None
            return "Empty"
        case [x] | (x,):  # Einzel-Element Liste oder Tuple
            return f"Single: {x}"
        case {"status": "ok"} | {"success": True}:
            return "Success response"
        case _:
            return "Other"

print(analyze_data([]))                    # "Empty"
print(analyze_data(None))                  # "Empty"
print(analyze_data([42]))                  # "Single: 42"
print(analyze_data({"status": "ok"}))      # "Success response"
print(analyze_data({"success": True}))     # "Success response"
```

## AS Patterns

### Pattern Binden und Extrahieren

```python
def process_nested(data):
    match data:
        case [x, y] as pair:
            # 'pair' ist die gesamte Liste, x und y sind Elemente
            return f"Pair {pair}: first={x}, second={y}"
        case {"name": name, "data": data} as full_dict:
            # 'full_dict' ist das gesamte Dict
            return f"Dict {full_dict}: name={name}"
        case _:
            return "Other"

print(process_nested([1, 2]))
# "Pair [1, 2]: first=1, second=2"

print(process_nested({"name": "test", "data": [1, 2, 3]}))
# "Dict {'name': 'test', 'data': [1, 2, 3]}: name=test"
```

### AS mit OR

```python
def handle_input(val):
    match val:
        case int() | float() as number:
            return f"Numeric: {number}, squared: {number**2}"
        case str() as text:
            return f"Text: {text}, length: {len(text)}"
        case list() | tuple() as sequence:
            return f"Sequence: {len(sequence)} items"
        case _:
            return "Unknown type"

print(handle_input(5))          # "Numeric: 5, squared: 25"
print(handle_input(3.14))       # "Numeric: 3.14, squared: 9.8596"
print(handle_input("hello"))    # "Text: hello, length: 5"
print(handle_input([1, 2, 3]))  # "Sequence: 3 items"
```

## Guard Patterns (if)

### Einfache Guards

```python
def categorize_number(n):
    match n:
        case x if x < 0:
            return "Negative"
        case 0:
            return "Zero"
        case x if x < 10:
            return "Small positive"
        case x if x < 100:
            return "Medium positive"
        case x:
            return "Large positive"

print(categorize_number(-5))   # "Negative"
print(categorize_number(0))    # "Zero"
print(categorize_number(5))    # "Small positive"
print(categorize_number(50))   # "Medium positive"
print(categorize_number(500))  # "Large positive"
```

### Guards mit Destrukturierung

```python
def validate_triangle(sides):
    match sides:
        case (a, b, c) if a + b > c and b + c > a and a + c > b:
            # Gültiges Dreieck
            if a == b == c:
                return "Equilateral triangle"
            elif a == b or b == c or a == c:
                return "Isosceles triangle"
            else:
                return "Scalene triangle"
        case (a, b, c):
            return "Invalid triangle (side lengths don't satisfy triangle inequality)"
        case _:
            return "Need exactly 3 sides"

print(validate_triangle((3, 3, 3)))   # "Equilateral triangle"
print(validate_triangle((3, 3, 4)))   # "Isosceles triangle"
print(validate_triangle((3, 4, 5)))   # "Scalene triangle"
print(validate_triangle((1, 2, 10)))  # "Invalid triangle..."
```

### Guards mit Dictionary-Patterns

```python
def validate_user(user):
    match user:
        case {"name": name, "age": age} if age < 0:
            return "Invalid: age cannot be negative"
        case {"name": name, "age": age} if age < 18:
            return f"Minor: {name} ({age})"
        case {"name": name, "age": age} if len(name) < 2:
            return "Invalid: name too short"
        case {"name": name, "age": age, "email": email} if "@" not in email:
            return "Invalid: email format"
        case {"name": name, "age": age}:
            return f"Valid user: {name} ({age})"
        case _:
            return "Invalid user data"

print(validate_user({"name": "Max", "age": 30}))
# "Valid user: Max (30)"

print(validate_user({"name": "Anna", "age": 15}))
# "Minor: Anna (15)"

print(validate_user({"name": "M", "age": 25}))
# "Invalid: name too short"
```

## Kombinierte Patterns

### Komplexe Verschachtelung

```python
from dataclasses import dataclass
from typing import Optional

@dataclass
class Address:
    street: str
    city: str
    country: str

@dataclass
class Person:
    name: str
    age: int
    address: Optional[Address] = None

def describe_person(person):
    match person:
        case Person(name=name, age=age, address=None):
            return f"{name} ({age}), no address"
        
        case Person(
            name=name,
            age=age,
            address=Address(city="Berlin", country="Germany")
        ):
            return f"{name} ({age}) from Berlin, Germany"
        
        case Person(
            name=name,
            age=age,
            address=Address(city=city, country="Germany")
        ):
            return f"{name} ({age}) from {city}, Germany"
        
        case Person(
            name=name,
            address=Address(city=city, country=country)
        ) if age >= 18:
            return f"Adult {name} from {city}, {country}"
        
        case Person(name=name, age=age, address=addr):
            return f"{name} ({age}) at {addr}"

p1 = Person("Max", 30, Address("Main St", "Berlin", "Germany"))
print(describe_person(p1))
# "Max (30) from Berlin, Germany"

p2 = Person("Anna", 25, Address("Oak Ave", "Munich", "Germany"))
print(describe_person(p2))
# "Anna (25) from Munich, Germany"

p3 = Person("Bob", 15)
print(describe_person(p3))
# "Bob (15), no address"
```

### Pattern mit Multiple Guards

```python
def analyze_transaction(transaction):
    match transaction:
        case {
            "type": "payment",
            "amount": amount,
            "currency": "EUR"
        } if amount > 1000:
            return f"Large EUR payment: €{amount}"
        
        case {
            "type": "payment",
            "amount": amount,
            "currency": currency
        } if amount > 0:
            return f"Payment: {amount} {currency}"
        
        case {
            "type": "refund",
            "amount": amount,
            "original_id": original_id
        } if amount > 0:
            return f"Refund of {amount} for transaction {original_id}"
        
        case {"type": "transfer", "from": sender, "to": receiver, "amount": amt}:
            return f"Transfer: {sender} → {receiver} ({amt})"
        
        case _:
            return "Invalid transaction"

print(analyze_transaction({
    "type": "payment",
    "amount": 1500,
    "currency": "EUR"
}))
# "Large EUR payment: €1500"

print(analyze_transaction({
    "type": "transfer",
    "from": "Alice",
    "to": "Bob",
    "amount": 100
}))
# "Transfer: Alice → Bob (100)"
```

## Performance-Hinweise

### Pattern-Reihenfolge optimieren

```python
# Ineffizient - allgemeines Pattern zuerst
def bad_match(val):
    match val:
        case x if x > 0:  # Wird für alle positiven Zahlen geprüft
            ...
        case 1:  # Wird nie erreicht!
            ...

# Besser - spezifisch zu allgemein
def good_match(val):
    match val:
        case 1:  # Spezifisch
            ...
        case x if x > 0:  # Allgemein
            ...
```

### Vermeiden bei vielen Fällen

```python
# Schlecht für viele Cases
def bad_lookup(code):
    match code:
        case 1: return "one"
        case 2: return "two"
        # ... 100 weitere Cases
        case 100: return "hundred"

# Besser: Dictionary
def good_lookup(code):
    lookup = {i: f"number_{i}" for i in range(1, 101)}
    return lookup.get(code, "unknown")
```

## Übungsaufgaben

### Übung 1: List Analyzer (Leicht)

Schreibe eine Funktion die Listen analysiert:
- Leer → "Empty"
- Ein Element → "Single: X"
- Zwei gleiche Elemente → "Double X"
- Palindrom → "Palindrome"
- Sonst → "List of N items"

**Erwarteter Output:**
```python
analyze_list([])           # "Empty"
analyze_list([5])          # "Single: 5"
analyze_list([3, 3])       # "Double 3"
analyze_list([1,2,3,2,1])  # "Palindrome"
analyze_list([1,2,3])      # "List of 3 items"
```

### Übung 2: JSON Router (Mittel)

Erstelle einen API-Router der Requests verarbeitet:
```python
{"method": "GET", "path": "/users/123"}
{"method": "POST", "path": "/users", "body": {...}}
{"method": "DELETE", "path": "/users/123"}
```

**Erwarteter Output:**
```python
route({"method": "GET", "path": "/users/123"})
# "GET user 123"

route({"method": "POST", "path": "/users", "body": {"name": "Max"}})
# "Create user: Max"
```

### Übung 3: Binary Tree Operations (Schwer)

Implementiere Tree-Operationen mit Pattern Matching:
```python
@dataclass
class Leaf:
    value: int

@dataclass
class Node:
    left: 'Tree'
    right: 'Tree'

Tree = Leaf | Node
```

Funktionen: `height()`, `sum_values()`, `find_max()`

**Erwarteter Output:**
```python
tree = Node(Leaf(1), Node(Leaf(2), Leaf(3)))
print(height(tree))      # 3
print(sum_values(tree))  # 6
print(find_max(tree))    # 3
```

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [Pattern Syntax Reference](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-python-grammar-patterns) - Vollständige Syntax
- [PEP 634 - Pattern Specification](https://peps.python.org/pep-0634/) - Detaillierte Spezifikation
- [PEP 636 - Tutorial](https://peps.python.org/pep-0636/) - Lern-Tutorial

## Verwandte Themen

- [[05_Pattern_Matching/01_Grundlagen/01_Match Statement|Match Statement]] - Grundlagen
- [[05_Pattern_Matching/01_Grundlagen/03_Übungen Pattern Matching|Übungen Pattern Matching]] - Praxis
- [[04_Fortgeschritten/04_Dataclasses/01_Dataclass Basics|Dataclass Basics]] - Class Patterns
- [[02_Sequenzen/01_Listen/03_List Operationen|List Operationen]] - Sequence Patterns

---
← [[05_Pattern_Matching/01_Grundlagen/01_Match Statement|Match Statement]] | [[INDEX|📑 Index]] | [[05_Pattern_Matching/01_Grundlagen/03_Übungen Pattern Matching|Übungen Pattern Matching]] →