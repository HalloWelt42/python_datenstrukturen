---
tags: [python, datenstrukturen, dataclasses, oop]
created: 2025-10-02
---

← [[04_Fortgeschritten/03_Heap_Queue/03_Übungen Heapq|Übungen Heapq]] | [[INDEX|📑 Index]] | [[04_Fortgeschritten/04_Dataclasses/02_Dataclass Features|Dataclass Features]] →

# Dataclass Basics

> [!tip] Lernziel
> Nach dieser Seite verstehst du, was Dataclasses sind, wie sie funktionieren, kennst ihre Vorteile gegenüber normalen Klassen und kannst einfache Dataclasses erstellen und verwenden.

## Was sind Dataclasses?

**Dataclasses** (ab Python 3.7) sind eine spezielle Art von Klassen, die hauptsächlich zum **Speichern von Daten** dienen. Sie generieren automatisch viele Standard-Methoden wie `__init__()`, `__repr__()`, `__eq__()` und mehr.

### Problem: Normale Klassen sind verbose

```python
# Normale Klasse: Viel Boilerplate-Code
class Person:
    def __init__(self, name, age, email):
        self.name = name
        self.age = age
        self.email = email
    
    def __repr__(self):
        return f"Person(name={self.name!r}, age={self.age!r}, email={self.email!r})"
    
    def __eq__(self, other):
        if not isinstance(other, Person):
            return NotImplemented
        return (self.name, self.age, self.email) == (other.name, other.age, other.email)
    
    # Evtl. noch __hash__, __lt__, etc...
```

### Lösung: Dataclasses

```python
from dataclasses import dataclass

# Dataclass: Minimal und klar
@dataclass
class Person:
    name: str
    age: int
    email: str

# Automatisch generiert:
# - __init__()
# - __repr__()
# - __eq__()
# - und mehr!

# Verwendung
person = Person("Alice", 30, "alice@example.com")
print(person)  # Person(name='Alice', age=30, email='alice@example.com')
```

💡 **Vorteil:** Weniger Code, mehr Klarheit, weniger Fehler!

## Erste Dataclass erstellen

### Einfachstes Beispiel

```python
from dataclasses import dataclass

@dataclass
class Point:
    x: float
    y: float

# Erstellen
p1 = Point(1.0, 2.0)
p2 = Point(3.0, 4.0)

print(p1)  # Point(x=1.0, y=2.0)
print(p1.x)  # 1.0

# Vergleich funktioniert automatisch
print(p1 == Point(1.0, 2.0))  # True
print(p1 == p2)  # False
```

### Mit Default-Werten

```python
from dataclasses import dataclass

@dataclass
class User:
    username: str
    email: str
    active: bool = True  # Default-Wert
    admin: bool = False

# Mit Defaults
user1 = User("alice", "alice@example.com")
print(user1)  # User(username='alice', email='alice@example.com', active=True, admin=False)

# Defaults überschreiben
user2 = User("bob", "bob@example.com", active=False, admin=True)
print(user2)  # User(username='bob', email='bob@example.com', active=False, admin=True)
```

⚠️ **Wichtig:** Parameter ohne Default müssen vor Parametern mit Default stehen!

```python
# ❌ FEHLER
@dataclass
class Invalid:
    name: str = "Default"
    age: int  # TypeError: non-default argument follows default argument

# ✅ RICHTIG
@dataclass
class Valid:
    age: int
    name: str = "Default"
```

## Type Hints in Dataclasses

Type Hints sind in Dataclasses **erforderlich** (auch wenn Python sie zur Laufzeit nicht erzwingt).

```python
from dataclasses import dataclass
from typing import List, Optional

@dataclass
class Book:
    title: str
    author: str
    year: int
    pages: int
    isbn: Optional[str] = None  # Kann None sein
    tags: List[str] = None  # ⚠️ Problem! (siehe unten)

# Type Hints helfen Tools wie mypy:
book = Book("1984", "George Orwell", 1949, 328)
# book.pages = "viele"  # mypy würde warnen (zur Laufzeit aber erlaubt)
```

### Wichtig: Mutable Defaults

❌ **Falsch:** Mutable Objekte als Default

```python
from dataclasses import dataclass
from typing import List

@dataclass
class BadClass:
    items: List[str] = []  # ⚠️ Gefährlich!

# Problem: Alle Instanzen teilen dieselbe Liste!
obj1 = BadClass()
obj1.items.append("A")

obj2 = BadClass()
print(obj2.items)  # ['A'] - Unerwartetes Verhalten!
```

✅ **Richtig:** `default_factory` verwenden

```python
from dataclasses import dataclass, field
from typing import List

@dataclass
class GoodClass:
    items: List[str] = field(default_factory=list)

# Jede Instanz bekommt eigene Liste
obj1 = GoodClass()
obj1.items.append("A")

obj2 = GoodClass()
print(obj2.items)  # [] - Korrekt!
```

## Automatisch generierte Methoden

### `__init__()`

```python
from dataclasses import dataclass

@dataclass
class Rectangle:
    width: float
    height: float

# Äquivalent zu:
class Rectangle_Manual:
    def __init__(self, width: float, height: float):
        self.width = width
        self.height = height

rect = Rectangle(10, 20)
print(rect.width, rect.height)  # 10 20
```

### `__repr__()`

```python
from dataclasses import dataclass

@dataclass
class Product:
    name: str
    price: float

p = Product("Laptop", 999.99)
print(p)  # Product(name='Laptop', price=999.99)
print(repr(p))  # Product(name='Laptop', price=999.99)

# Nützlich für Debugging!
```

### `__eq__()`

```python
from dataclasses import dataclass

@dataclass
class Color:
    r: int
    g: int
    b: int

red1 = Color(255, 0, 0)
red2 = Color(255, 0, 0)
blue = Color(0, 0, 255)

print(red1 == red2)  # True (Werte-Vergleich)
print(red1 == blue)  # False
print(red1 is red2)  # False (verschiedene Objekte)
```

## Dataclass Optionen

Der `@dataclass` Decorator akzeptiert verschiedene Parameter:

```python
from dataclasses import dataclass

@dataclass(
    init=True,      # __init__() generieren (Default: True)
    repr=True,      # __repr__() generieren (Default: True)
    eq=True,        # __eq__() generieren (Default: True)
    order=False,    # __lt__, __le__, __gt__, __ge__ generieren (Default: False)
    unsafe_hash=False,  # __hash__() generieren (Default: False)
    frozen=False    # Instanzen immutable machen (Default: False)
)
class Example:
    value: int
```

### `order=True` - Sortierbare Dataclasses

```python
from dataclasses import dataclass

@dataclass(order=True)
class Student:
    name: str
    grade: float

students = [
    Student("Bob", 85.5),
    Student("Alice", 92.0),
    Student("Charlie", 78.0)
]

# Sortierung funktioniert!
students.sort()
for s in students:
    print(s)

# Ausgabe (sortiert nach name):
# Student(name='Alice', grade=92.0)
# Student(name='Bob', grade=85.5)
# Student(name='Charlie', grade=78.0)
```

💡 **Sortierung:** Erfolgt über alle Felder in Reihenfolge der Definition (wie Tupel-Vergleich).

### `frozen=True` - Immutable Dataclasses

```python
from dataclasses import dataclass

@dataclass(frozen=True)
class ImmutablePoint:
    x: float
    y: float

point = ImmutablePoint(1.0, 2.0)
print(point.x)  # 1.0

# Änderung nicht möglich!
try:
    point.x = 5.0
except AttributeError as e:
    print(f"Fehler: {e}")  # Fehler: cannot assign to field 'x'

# Frozen Dataclasses sind hashbar
print(hash(point))  # Funktioniert!

# Können als Dict-Keys verwendet werden
coords = {point: "Origin"}
```

## Vergleich: Normale Klasse vs Dataclass

### Beispiel: User-Klasse

```python
# === Normale Klasse ===
class UserNormal:
    def __init__(self, username: str, email: str, age: int, active: bool = True):
        self.username = username
        self.email = email
        self.age = age
        self.active = active
    
    def __repr__(self):
        return (f"UserNormal(username={self.username!r}, email={self.email!r}, "
                f"age={self.age!r}, active={self.active!r})")
    
    def __eq__(self, other):
        if not isinstance(other, UserNormal):
            return NotImplemented
        return (self.username, self.email, self.age, self.active) == \
               (other.username, other.email, other.age, other.active)

# === Dataclass ===
from dataclasses import dataclass

@dataclass
class UserDataclass:
    username: str
    email: str
    age: int
    active: bool = True

# Gleiche Funktionalität, viel weniger Code!

# Verwendung identisch
user1 = UserNormal("alice", "alice@example.com", 30)
user2 = UserDataclass("alice", "alice@example.com", 30)

print(user1)
print(user2)
# Beide: User...(username='alice', email='alice@example.com', age=30, active=True)
```

**Code-Reduktion:** ~70% weniger Code mit Dataclass!

## Praktische Anwendungsbeispiele

### Beispiel 1: Konfigurationsobjekt

```python
from dataclasses import dataclass
from typing import Optional

@dataclass
class DatabaseConfig:
    host: str
    port: int
    database: str
    username: str
    password: str
    ssl_enabled: bool = True
    timeout: int = 30
    pool_size: int = 10

# Übersichtliche Konfiguration
config = DatabaseConfig(
    host="localhost",
    port=5432,
    database="myapp",
    username="admin",
    password="secret",
    timeout=60
)

print(config)
# DatabaseConfig(host='localhost', port=5432, database='myapp', 
#                username='admin', password='secret', ssl_enabled=True, 
#                timeout=60, pool_size=10)
```

### Beispiel 2: API Response

```python
from dataclasses import dataclass
from typing import List, Optional
from datetime import datetime

@dataclass
class User:
    id: int
    username: str
    email: str
    created_at: datetime

@dataclass
class ApiResponse:
    success: bool
    data: Optional[User] = None
    error: Optional[str] = None
    timestamp: datetime = None
    
    def __post_init__(self):
        """Wird nach __init__ automatisch aufgerufen"""
        if self.timestamp is None:
            self.timestamp = datetime.now()

# Verwendung
def get_user(user_id: int) -> ApiResponse:
    if user_id > 0:
        user = User(
            id=user_id,
            username="alice",
            email="alice@example.com",
            created_at=datetime.now()
        )
        return ApiResponse(success=True, data=user)
    else:
        return ApiResponse(success=False, error="Invalid user ID")

# Test
response = get_user(1)
print(response)
print(f"Success: {response.success}")
if response.data:
    print(f"User: {response.data.username}")
```

### Beispiel 3: Geometrie-Klassen

```python
from dataclasses import dataclass
from math import sqrt

@dataclass
class Point:
    x: float
    y: float
    
    def distance_to(self, other: 'Point') -> float:
        """Berechnet Distanz zu anderem Punkt"""
        return sqrt((self.x - other.x)**2 + (self.y - other.y)**2)
    
    def __add__(self, other: 'Point') -> 'Point':
        """Vector addition"""
        return Point(self.x + other.x, self.y + other.y)

@dataclass
class Rectangle:
    top_left: Point
    width: float
    height: float
    
    @property
    def area(self) -> float:
        """Berechnet Fläche"""
        return self.width * self.height
    
    @property
    def bottom_right(self) -> Point:
        """Berechnet untere rechte Ecke"""
        return Point(
            self.top_left.x + self.width,
            self.top_left.y - self.height
        )

# Verwendung
p1 = Point(0, 0)
p2 = Point(3, 4)

print(f"Distanz: {p1.distance_to(p2)}")  # 5.0

p3 = p1 + p2
print(p3)  # Point(x=3, y=4)

rect = Rectangle(Point(0, 10), 5, 3)
print(f"Fläche: {rect.area}")  # 15.0
print(f"Untere rechte Ecke: {rect.bottom_right}")  # Point(x=5, y=7)
```

### Beispiel 4: Geschachtelte Dataclasses

```python
from dataclasses import dataclass, field
from typing import List

@dataclass
class Address:
    street: str
    city: str
    zip_code: str
    country: str = "Germany"

@dataclass
class Contact:
    email: str
    phone: str

@dataclass
class Person:
    name: str
    age: int
    address: Address
    contacts: List[Contact] = field(default_factory=list)
    
    def add_contact(self, email: str, phone: str):
        """Fügt Kontaktinformation hinzu"""
        self.contacts.append(Contact(email, phone))

# Verwendung
person = Person(
    name="Alice Smith",
    age=30,
    address=Address(
        street="Hauptstraße 123",
        city="Berlin",
        zip_code="10115"
    )
)

person.add_contact("alice@work.com", "+49 30 12345678")
person.add_contact("alice@private.com", "+49 170 9876543")

print(person)
# Person(name='Alice Smith', age=30, 
#        address=Address(street='Hauptstraße 123', city='Berlin', 
#                       zip_code='10115', country='Germany'),
#        contacts=[Contact(email='alice@work.com', phone='+49 30 12345678'),
#                 Contact(email='alice@private.com', phone='+49 170 9876543')])
```

## Häufige Fallstricke

### ❌ Fallstrick 1: Mutable Defaults ohne field()

```python
from dataclasses import dataclass

@dataclass
class TodoList:
    items: list = []  # ⚠️ Alle Instanzen teilen diese Liste!

todo1 = TodoList()
todo1.items.append("Task 1")

todo2 = TodoList()
print(todo2.items)  # ['Task 1'] - Falsch!
```

✅ **Richtig:**
```python
from dataclasses import dataclass, field

@dataclass
class TodoList:
    items: list = field(default_factory=list)

todo1 = TodoList()
todo1.items.append("Task 1")

todo2 = TodoList()
print(todo2.items)  # [] - Korrekt!
```

### ❌ Fallstrick 2: Order ohne vergleichbare Typen

```python
from dataclasses import dataclass

@dataclass(order=True)
class Mixed:
    value: object  # Kann alles sein

m1 = Mixed(5)
m2 = Mixed("hello")

try:
    print(m1 < m2)  # TypeError: '<' not supported
except TypeError as e:
    print(f"Fehler: {e}")
```

✅ **Richtig:** Nur vergleichbare Typen verwenden oder eigene Vergleichslogik implementieren.

### ❌ Fallstrick 3: frozen=True mit mutable Feldern

```python
from dataclasses import dataclass, field

@dataclass(frozen=True)
class Container:
    items: list = field(default_factory=list)

c = Container()
# Kann items nicht neu zuweisen:
# c.items = [1, 2, 3]  # AttributeError

# Aber Liste selbst ist mutable!
c.items.append(1)  # Funktioniert! 😱
print(c.items)  # [1]
```

💡 **Lösung:** Verwende immutable Typen (tuple statt list) bei frozen Dataclasses.

## Performance-Hinweise

Dataclasses sind **nicht** langsamer als normale Klassen:

```python
import timeit
from dataclasses import dataclass

# Normale Klasse
class NormalPerson:
    def __init__(self, name, age):
        self.name = name
        self.age = age

# Dataclass
@dataclass
class DataPerson:
    name: str
    age: int

# Benchmark
normal_time = timeit.timeit(
    'NormalPerson("Alice", 30)',
    globals=globals(),
    number=1000000
)

data_time = timeit.timeit(
    'DataPerson("Alice", 30)',
    globals=globals(),
    number=1000000
)

print(f"Normale Klasse: {normal_time:.4f}s")
print(f"Dataclass:      {data_time:.4f}s")
# Beide praktisch gleich schnell!
```

## Wann Dataclasses verwenden?

### ✅ Verwende Dataclasses wenn:

- Klasse hauptsächlich Daten speichert
- Wenig bis keine Logik in der Klasse
- Standard-Methoden (`__init__`, `__repr__`, `__eq__`) benötigt werden
- Lesbarkeit und Wartbarkeit wichtig sind
- Type Hints sowieso verwendet werden

### ❌ Verwende normale Klassen wenn:

- Komplexe Initialisierung benötigt wird
- Viel Geschäftslogik in der Klasse
- Feinere Kontrolle über `__init__` nötig
- Performance kritisch und jedes Detail zählt (sehr selten relevant)

## Übungsaufgaben

### Übung 1: Einfache Dataclass (Leicht)

Erstelle eine `Book` Dataclass mit title, author, year und pages. Teste Instanzerstellung und Vergleich.

```python
from dataclasses import dataclass

# Dein Code hier

# Test
book1 = Book("1984", "George Orwell", 1949, 328)
book2 = Book("1984", "George Orwell", 1949, 328)
print(book1)
print(book1 == book2)  # Should be True
```

### Übung 2: Mit Defaults (Leicht)

Erstelle eine `BlogPost` Dataclass mit title, content, author, und optionalen Feldern published (bool, default=False) und tags (list, default leer).

```python
from dataclasses import dataclass, field

# Dein Code hier

# Test
post = BlogPost("Hello World", "My first post", "Alice")
print(post.published)  # False
print(post.tags)  # []
```

### Übung 3: Frozen und Hashable (Mittel)

Erstelle eine frozen `Coordinate` Dataclass und verwende sie als Dictionary-Key.

```python
from dataclasses import dataclass

# Dein Code hier

# Test
c1 = Coordinate(1.0, 2.0)
c2 = Coordinate(1.0, 2.0)
locations = {c1: "Home", c2: "Office"}
print(len(locations))  # Should be 1 (same coordinates)
```

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [dataclasses - Data Classes](https://docs.python.org/3/library/dataclasses.html) - Vollständige Dokumentation
- [PEP 557 - Data Classes](https://peps.python.org/pep-0557/) - Original Proposal
- [dataclasses Source](https://github.com/python/cpython/blob/main/Lib/dataclasses.py) - Implementation

## Verwandte Themen

- [[04_Fortgeschritten/04_Dataclasses/02_Dataclass Features|Dataclass Features]] - Erweiterte Features
- [[04_Fortgeschritten/04_Dataclasses/03_Field Options|Field Options]] - field() im Detail
- [[05_Pattern_Matching/02_Type_Hints/01_Type Annotations Basics|Type Annotations]] - Type Hints verstehen
- [[02_Sequenzen/02_Tupel/03_Named Tuples|Named Tuples]] - Alternative zu Dataclasses

---
← [[04_Fortgeschritten/03_Heap_Queue/03_Übungen Heapq|Übungen Heapq]] | [[INDEX|📑 Index]] | [[04_Fortgeschritten/04_Dataclasses/02_Dataclass Features|Dataclass Features]] →