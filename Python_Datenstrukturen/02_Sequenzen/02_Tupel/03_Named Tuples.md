---
tags: [python, datenstrukturen, tuple, namedtuple, collections]
created: 2025-10-01
---

← [[02_Sequenzen/02_Tupel/02_Tuple Unpacking|Tuple Unpacking]] | [[INDEX|📑 Index]] | [[02_Sequenzen/02_Tupel/04_Übungen Tupel|Übungen Tupel]] →

# Named Tuples - Tupel mit Feldnamen

> [!tip] Lernziel
> Nach dieser Seite verstehst du Named Tuples - selbstdokumentierende Tupel mit benannten Feldern. Du kennst beide Varianten (`collections.namedtuple` und `typing.NamedTuple`) und weißt, wann du sie statt einfacher Tupel oder Klassen verwenden solltest.

## Was sind Named Tuples?

**Named Tuples** sind Tupel mit benannten Feldern, die per Attribut-Zugriff erreichbar sind. Sie kombinieren die Effizienz von Tupeln mit der Lesbarkeit von Klassen.

**Vorteile:**
- ✅ **Selbstdokumentierend** - Feldnamen statt Indizes
- ✅ **Unveränderlich** - Wie normale Tupel
- ✅ **Leichtgewichtig** - Weniger Overhead als Klassen
- ✅ **Kompatibel** - Funktionieren wie normale Tupel
- ✅ **Hashbar** - Können als Dict-Keys verwendet werden

```python
from collections import namedtuple

# Normales Tupel - unübersichtlich
person_tuple = ("Anna", 25, "Berlin")
print(person_tuple[0])  # Welches Feld war das nochmal?

# Named Tuple - selbsterklärend!
Person = namedtuple("Person", ["name", "age", "city"])
person = Person("Anna", 25, "Berlin")

print(person.name)  # Klar und lesbar!
print(person.age)   # 25
print(person.city)  # Berlin

# Funktioniert auch wie normales Tupel
print(person[0])    # Anna
```

---

## Collections.namedtuple - Klassische Variante

### Erstellung und Syntax

```python
from collections import namedtuple

# Methode 1: Liste von Feldnamen
Point = namedtuple("Point", ["x", "y"])

# Methode 2: String mit Leerzeichen
Point = namedtuple("Point", "x y")

# Methode 3: String mit Kommas
Point = namedtuple("Point", "x, y")

# Erstellen von Instanzen
p1 = Point(10, 20)
p2 = Point(x=30, y=40)
p3 = Point(50, y=60)

print(p1)  # Point(x=10, y=20)
print(p2)  # Point(x=30, y=40)
print(p3)  # Point(x=50, y=60)

# Zugriff auf Felder
print(p1.x)     # 10 - Per Attribut (empfohlen)
print(p1[0])    # 10 - Per Index (funktioniert auch)
print(p1[-1])   # 20 - Negativer Index

# Unpacking funktioniert
x, y = p1
print(f"x={x}, y={y}")  # x=10, y=20
```

### Parameter und Optionen

```python
from collections import namedtuple

# rename=True: Ersetzt ungültige Feldnamen
# Ungültig: Keywords, Duplikate, mit _ startend
InvalidNames = namedtuple(
    "InvalidNames", 
    ["def", "class", "x", "x"],  # def und class sind Keywords, x doppelt
    rename=True
)
print(InvalidNames._fields)  # ('_0', '_1', 'x', '_3')

# defaults: Default-Werte (Python 3.7+)
Person = namedtuple("Person", ["name", "age", "city"], defaults=["Unknown", 0, ""])
p1 = Person("Anna")
print(p1)  # Person(name='Anna', age=0, city='')

p2 = Person("Bob", 30)
print(p2)  # Person(name='Bob', age=30, city='')

# Defaults werden von rechts angewendet!
Employee = namedtuple("Employee", ["name", "id", "dept"], defaults=["IT"])
emp = Employee("Anna", 123)
print(emp)  # Employee(name='Anna', id=123, dept='IT')
```

### Spezielle Attribute und Methoden

```python
from collections import namedtuple

Point = namedtuple("Point", ["x", "y", "z"])
p = Point(1, 2, 3)

# _fields: Tuple mit Feldnamen
print(Point._fields)  # ('x', 'y', 'z')

# _asdict(): Als Dictionary
print(p._asdict())  # {'x': 1, 'y': 2, 'z': 3}

# _replace(): Neues Named Tuple mit geänderten Werten
p2 = p._replace(x=10)
print(p2)  # Point(x=10, y=2, z=3)
print(p)   # Point(x=1, y=2, z=3) - Original unverändert

# _make(): Aus Iterable erstellen
values = [5, 10, 15]
p3 = Point._make(values)
print(p3)  # Point(x=5, y=10, z=15)

# Auch aus anderen Iterables
p4 = Point._make(range(3))
print(p4)  # Point(x=0, y=1, z=2)
```

---

## Typing.NamedTuple - Moderne Variante (Python 3.6+)

### Syntax mit Type Hints

```python
from typing import NamedTuple

# Klassen-basierte Syntax mit Type Hints
class Point(NamedTuple):
    x: float
    y: float
    z: float = 0.0  # Default-Wert

# Erstellen
p1 = Point(1.0, 2.0)
print(p1)  # Point(x=1.0, y=2.0, z=0.0)

p2 = Point(1.0, 2.0, 3.0)
print(p2)  # Point(x=1.0, y=2.0, z=3.0)

# Mit Keyword-Arguments
p3 = Point(x=5.0, y=10.0)
print(p3)  # Point(x=5.0, y=10.0, z=0.0)
```

### Erweiterte Features

```python
from typing import NamedTuple, Optional, List

# Mit verschiedenen Typen
class Person(NamedTuple):
    name: str
    age: int
    city: str
    hobbies: List[str] = []
    email: Optional[str] = None

# Erstellen
person = Person(
    name="Anna",
    age=25,
    city="Berlin",
    hobbies=["Lesen", "Sport"]
)

print(person.name)     # Anna
print(person.hobbies)  # ['Lesen', 'Sport']
print(person.email)    # None

# Mit Defaults
person2 = Person("Bob", 30, "München")
print(person2)  # Person(name='Bob', age=30, city='München', hobbies=[], email=None)
```

### Methoden hinzufügen

```python
from typing import NamedTuple
import math

# Named Tuple kann Methoden haben!
class Point(NamedTuple):
    x: float
    y: float
    
    def distance_from_origin(self) -> float:
        """Berechnet Distanz vom Ursprung"""
        return math.sqrt(self.x**2 + self.y**2)
    
    def distance_to(self, other: 'Point') -> float:
        """Berechnet Distanz zu anderem Punkt"""
        return math.sqrt((self.x - other.x)**2 + (self.y - other.y)**2)
    
    def __str__(self) -> str:
        """Custom String-Repräsentation"""
        return f"Point({self.x}, {self.y})"

# Verwendung
p1 = Point(3.0, 4.0)
p2 = Point(0.0, 0.0)

print(p1.distance_from_origin())  # 5.0
print(p1.distance_to(p2))         # 5.0
print(p1)                         # Point(3.0, 4.0)
```

### Properties und Class Methods

```python
from typing import NamedTuple

class Rectangle(NamedTuple):
    width: float
    height: float
    
    @property
    def area(self) -> float:
        """Berechnet Fläche"""
        return self.width * self.height
    
    @property
    def perimeter(self) -> float:
        """Berechnet Umfang"""
        return 2 * (self.width + self.height)
    
    @classmethod
    def square(cls, side: float) -> 'Rectangle':
        """Erstellt Quadrat"""
        return cls(side, side)
    
    def scale(self, factor: float) -> 'Rectangle':
        """Skaliert Rechteck"""
        return Rectangle(self.width * factor, self.height * factor)

# Verwendung
rect = Rectangle(10, 20)
print(f"Fläche: {rect.area}")        # Fläche: 200
print(f"Umfang: {rect.perimeter}")   # Umfang: 60

square = Rectangle.square(15)
print(square)  # Rectangle(width=15, height=15)

scaled = rect.scale(2)
print(scaled)  # Rectangle(width=20, height=40)
```

---

## Collections vs Typing - Vergleich

### Unterschiede

| Feature | collections.namedtuple | typing.NamedTuple |
|---------|----------------------|-------------------|
| **Syntax** | Funktionsaufruf | Klassen-Definition |
| **Type Hints** | ❌ Nein | ✅ Ja |
| **Defaults** | Ja (Python 3.7+) | ✅ Ja, per Zuweisung |
| **Methoden** | ❌ Schwierig | ✅ Einfach |
| **Docstrings** | ❌ Eingeschränkt | ✅ Ja |
| **IDE Support** | Weniger | ✅ Besser |
| **Python Version** | 2.6+ | 3.6+ |
| **Performance** | Gleich | Gleich |

```python
from collections import namedtuple
from typing import NamedTuple

# collections.namedtuple
PointOld = namedtuple("Point", ["x", "y"])
p1 = PointOld(1, 2)

# typing.NamedTuple
class PointNew(NamedTuple):
    x: int
    y: int

p2 = PointNew(1, 2)

# Beide sind kompatibel und funktionieren gleich
print(p1 == p2)  # True (wenn Felder gleich)
print(type(p1))  # <class '__main__.Point'>
print(type(p2))  # <class '__main__.PointNew'>
```

---

## Praktische Anwendungen

### Anwendungsfall 1: Koordinaten und Geometrie

```python
from typing import NamedTuple
import math

class Point(NamedTuple):
    x: float
    y: float
    
    def distance_to(self, other: 'Point') -> float:
        return math.sqrt((self.x - other.x)**2 + (self.y - other.y)**2)

class Circle(NamedTuple):
    center: Point
    radius: float
    
    def area(self) -> float:
        return math.pi * self.radius ** 2
    
    def circumference(self) -> float:
        return 2 * math.pi * self.radius
    
    def contains(self, point: Point) -> bool:
        return self.center.distance_to(point) <= self.radius

# Verwendung
p1 = Point(0, 0)
p2 = Point(3, 4)
print(f"Distanz: {p1.distance_to(p2)}")  # Distanz: 5.0

circle = Circle(center=Point(0, 0), radius=5)
print(f"Fläche: {circle.area():.2f}")  # Fläche: 78.54
print(f"Enthält (3,4)? {circle.contains(p2)}")  # True
print(f"Enthält (10,10)? {circle.contains(Point(10, 10))}")  # False
```

### Anwendungsfall 2: Datenbankzeilen

```python
from typing import NamedTuple, Optional
from datetime import datetime

class User(NamedTuple):
    id: int
    username: str
    email: str
    created_at: datetime
    last_login: Optional[datetime] = None
    is_active: bool = True
    
    def to_dict(self) -> dict:
        """Konvertiert zu Dictionary"""
        return self._asdict()
    
    @classmethod
    def from_db_row(cls, row: tuple) -> 'User':
        """Erstellt User aus Datenbankzeile"""
        return cls._make(row)

# Simulierte Datenbankzeile
db_row = (1, "anna", "anna@example.com", datetime.now(), None, True)

# User erstellen
user = User.from_db_row(db_row)
print(user.username)  # anna
print(user.email)     # anna@example.com

# Als Dictionary für JSON
user_dict = user.to_dict()
print(user_dict)
```

### Anwendungsfall 3: Konfiguration

```python
from typing import NamedTuple

class DatabaseConfig(NamedTuple):
    host: str = "localhost"
    port: int = 5432
    database: str = "myapp"
    username: str = "admin"
    password: str = ""
    pool_size: int = 10
    
    def connection_string(self) -> str:
        """Erstellt Connection String"""
        return f"postgresql://{self.username}:{self.password}@{self.host}:{self.port}/{self.database}"

# Verwendung mit Defaults
config = DatabaseConfig(password="secret123")
print(config.connection_string())

# Override specific fields
prod_config = DatabaseConfig(
    host="prod-server.com",
    database="production",
    username="prod_user",
    password="prod_pass",
    pool_size=50
)
print(prod_config.connection_string())
```

### Anwendungsfall 4: API-Responses

```python
from typing import NamedTuple, List, Optional
from datetime import datetime

class APIResponse(NamedTuple):
    status_code: int
    data: dict
    timestamp: datetime
    error: Optional[str] = None
    
    @property
    def is_success(self) -> bool:
        return 200 <= self.status_code < 300
    
    @property
    def is_error(self) -> bool:
        return self.status_code >= 400

class PaginatedResponse(NamedTuple):
    items: List[dict]
    page: int
    per_page: int
    total: int
    
    @property
    def total_pages(self) -> int:
        return (self.total + self.per_page - 1) // self.per_page
    
    @property
    def has_next(self) -> bool:
        return self.page < self.total_pages
    
    @property
    def has_prev(self) -> bool:
        return self.page > 1

# Verwendung
response = APIResponse(
    status_code=200,
    data={"message": "Success"},
    timestamp=datetime.now()
)

if response.is_success:
    print(f"Erfolg: {response.data}")

# Paginierung
paginated = PaginatedResponse(
    items=[{"id": 1}, {"id": 2}],
    page=1,
    per_page=10,
    total=25
)

print(f"Seite {paginated.page} von {paginated.total_pages}")
print(f"Weitere Seiten? {paginated.has_next}")
```

### Anwendungsfall 5: Immutable Data Transfer Objects

```python
from typing import NamedTuple, List
from decimal import Decimal

class Money(NamedTuple):
    amount: Decimal
    currency: str = "EUR"
    
    def __str__(self) -> str:
        return f"{self.amount:.2f} {self.currency}"
    
    def convert(self, rate: Decimal, to_currency: str) -> 'Money':
        return Money(self.amount * rate, to_currency)

class OrderItem(NamedTuple):
    product_id: int
    name: str
    quantity: int
    price: Money
    
    @property
    def total(self) -> Money:
        return Money(self.price.amount * self.quantity, self.price.currency)

class Order(NamedTuple):
    order_id: int
    customer_id: int
    items: List[OrderItem]
    
    @property
    def subtotal(self) -> Money:
        total = Decimal(0)
        currency = self.items[0].price.currency if self.items else "EUR"
        
        for item in self.items:
            total += item.total.amount
        
        return Money(total, currency)
    
    def apply_discount(self, percent: Decimal) -> Money:
        discount_amount = self.subtotal.amount * (percent / 100)
        return Money(self.subtotal.amount - discount_amount, self.subtotal.currency)

# Verwendung
item1 = OrderItem(
    product_id=1,
    name="Laptop",
    quantity=1,
    price=Money(Decimal("999.99"))
)

item2 = OrderItem(
    product_id=2,
    name="Maus",
    quantity=2,
    price=Money(Decimal("25.50"))
)

order = Order(
    order_id=12345,
    customer_id=67890,
    items=[item1, item2]
)

print(f"Zwischensumme: {order.subtotal}")
print(f"Mit 10% Rabatt: {order.apply_discount(Decimal('10'))}")
```

---

## Named Tuple vs Alternativen

### Named Tuple vs Dictionary

```python
from typing import NamedTuple

# Dictionary - flexibel, aber fehleranfällig
person_dict = {
    "name": "Anna",
    "age": 25,
    "city": "Berlin"
}

# Typo möglich!
print(person_dict["nmae"])  # KeyError

# Named Tuple - typsicher
class Person(NamedTuple):
    name: str
    age: int
    city: str

person = Person("Anna", 25, "Berlin")
# print(person.nmae)  # AttributeError - frühe Fehlererkennung!

# Named Tuple ist unveränderlich
# person.age = 26  # AttributeError

# Dictionary ist veränderlich
person_dict["age"] = 26  # OK
```

### Named Tuple vs Dataclass

```python
from typing import NamedTuple
from dataclasses import dataclass

# Named Tuple - unveränderlich
class PointTuple(NamedTuple):
    x: int
    y: int

# Dataclass - veränderlich (default)
@dataclass
class PointClass:
    x: int
    y: int

pt = PointTuple(1, 2)
# pt.x = 5  # AttributeError - unveränderlich

pc = PointClass(1, 2)
pc.x = 5  # OK - veränderlich

# Dataclass kann frozen sein
@dataclass(frozen=True)
class PointFrozen:
    x: int
    y: int

pf = PointFrozen(1, 2)
# pf.x = 5  # FrozenInstanceError

# Wann was?
# - Named Tuple: Unveränderlich, leichtgewichtig, als dict-key
# - Dataclass: Veränderlich, mehr Features (defaults, post_init, etc.)
```

### Named Tuple vs Einfache Klasse

```python
from typing import NamedTuple

# Einfache Klasse - viel Boilerplate
class PersonClass:
    def __init__(self, name: str, age: int, city: str):
        self.name = name
        self.age = age
        self.city = city
    
    def __repr__(self):
        return f"Person(name={self.name!r}, age={self.age!r}, city={self.city!r})"
    
    def __eq__(self, other):
        if not isinstance(other, PersonClass):
            return NotImplemented
        return (self.name, self.age, self.city) == (other.name, other.age, other.city)

# Named Tuple - alles automatisch!
class PersonTuple(NamedTuple):
    name: str
    age: int
    city: str

# Beide funktionieren gleich, aber Named Tuple ist viel kürzer!
p1 = PersonClass("Anna", 25, "Berlin")
p2 = PersonTuple("Anna", 25, "Berlin")

print(p1)  # Person(name='Anna', age=25, city='Berlin')
print(p2)  # PersonTuple(name='Anna', age=25, city='Berlin')
```

---

## Best Practices

### ✅ DO - Gute Patterns

```python
from typing import NamedTuple

# 1. Verwende typing.NamedTuple für neue Code
class Point(NamedTuple):
    x: float
    y: float

# 2. Type Hints für bessere IDE-Unterstützung
class Person(NamedTuple):
    name: str  # IDE weiß: name ist string
    age: int   # IDE weiß: age ist int

# 3. Defaults von rechts nach links
class User(NamedTuple):
    username: str
    email: str
    is_active: bool = True  # Default zuletzt

# 4. Methoden für Logik hinzufügen
class Rectangle(NamedTuple):
    width: float
    height: float
    
    @property
    def area(self) -> float:
        return self.width * self.height

# 5. Als Return-Type für Funktionen
def get_coordinates() -> Point:
    return Point(10, 20)

# 6. Docstrings für Dokumentation
class Config(NamedTuple):
    """Application configuration.
    
    Attributes:
        host: Server hostname
        port: Server port number
    """
    host: str
    port: int
```

### ❌ DON'T - Anti-Patterns

```python
# 1. Vermeiden: Zu viele Felder
# ❌ Schwer zu handhaben
class HugeRecord(NamedTuple):
    field1: str
    field2: int
    # ... 20 weitere Felder
    field22: float

# ✅ Besser: In kleinere Strukturen aufteilen
class Address(NamedTuple):
    street: str
    city: str
    zip_code: str

class Person(NamedTuple):
    name: str
    age: int
    address: Address

# 2. Vermeiden: Veränderbare Defaults
# ❌ Gefährlich!
class Bad(NamedTuple):
    items: list = []  # Shared mutable default!

# ✅ Besser: None als Default
from typing import Optional, List

class Good(NamedTuple):
    items: Optional[List] = None
    
    def get_items(self) -> List:
        return self.items if self.items is not None else []

# 3. Vermeiden: collections.namedtuple ohne Grund
# ❌ Veralteter Stil
from collections import namedtuple
OldStyle = namedtuple("OldStyle", ["x", "y"])

# ✅ Moderner Stil mit Type Hints
class NewStyle(NamedTuple):
    x: int
    y: int
```

---

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [collections.namedtuple](https://docs.python.org/3/library/collections.html#collections.namedtuple) - Klassische Named Tuples
- [typing.NamedTuple](https://docs.python.org/3/library/typing.html#typing.NamedTuple) - Moderne Named Tuples mit Type Hints
- [PEP 484](https://www.python.org/dev/peps/pep-0484/) - Type Hints
- [Data Classes](https://docs.python.org/3/library/dataclasses.html) - Alternative für veränderliche Daten

---

## Verwandte Themen

- [[02_Sequenzen/02_Tupel/01_Tuple Basics|Tuple Basics]] - Grundlagen zu Tupeln
- [[02_Sequenzen/02_Tupel/02_Tuple Unpacking|Tuple Unpacking]] - Unpacking funktioniert auch mit Named Tuples
- [[04_Fortgeschritten/04_Dataclasses/01_Dataclass Basics|Dataclasses]] - Veränderliche Alternative
- [[05_Pattern_Matching/02_Type_Hints/01_Type Annotations Basics|Type Hints]] - Type Annotations im Detail

---

← [[02_Sequenzen/02_Tupel/02_Tuple Unpacking|Tuple Unpacking]] | [[INDEX|📑 Index]] | [[02_Sequenzen/02_Tupel/04_Übungen Tupel|Übungen Tupel]] →