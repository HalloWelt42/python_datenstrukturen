---
tags: [python, datenstrukturen, dataclasses, advanced]
created: 2025-10-02
---

← [[04_Fortgeschritten/04_Dataclasses/01_Dataclass Basics|Dataclass Basics]] | [[INDEX|📑 Index]] | [[04_Fortgeschritten/04_Dataclasses/03_Field Options|Field Options]] →

# Dataclass Features

> [!tip] Lernziel
> Nach dieser Seite kennst du erweiterte Dataclass-Features wie `__post_init__`, Vererbung, Properties, berechnete Felder, Validierung und fortgeschrittene Patterns.

## `__post_init__()` - Post-Initialization

Die `__post_init__()` Methode wird **nach** `__init__()` automatisch aufgerufen. Ideal für:
- Validierung
- Berechnete Felder
- Transformation von Eingaben
- Zusätzliche Initialisierung

### Grundlegendes Beispiel

```python
from dataclasses import dataclass
from datetime import datetime

@dataclass
class LogEntry:
    message: str
    level: str
    timestamp: datetime = None
    
    def __post_init__(self):
        """Setze Timestamp wenn nicht gegeben"""
        if self.timestamp is None:
            self.timestamp = datetime.now()

# Verwendung
log1 = LogEntry("Server started", "INFO")
print(log1)
# LogEntry(message='Server started', level='INFO', timestamp=datetime.datetime(...))

log2 = LogEntry("Error occurred", "ERROR", datetime(2024, 1, 1))
print(log2.timestamp)  # 2024-01-01 00:00:00
```

### Validierung in `__post_init__()`

```python
from dataclasses import dataclass

@dataclass
class Person:
    name: str
    age: int
    
    def __post_init__(self):
        """Validiere Eingaben"""
        if not self.name:
            raise ValueError("Name darf nicht leer sein")
        
        if self.age < 0:
            raise ValueError("Alter muss positiv sein")
        
        if self.age > 150:
            raise ValueError("Alter unrealistisch hoch")
        
        # Normalisiere Name
        self.name = self.name.strip().title()

# Test
person1 = Person("alice smith", 30)
print(person1)  # Person(name='Alice Smith', age=30)

try:
    person2 = Person("", 25)
except ValueError as e:
    print(f"Fehler: {e}")  # Fehler: Name darf nicht leer sein

try:
    person3 = Person("Bob", -5)
except ValueError as e:
    print(f"Fehler: {e}")  # Fehler: Alter muss positiv sein
```

### Berechnete Felder

```python
from dataclasses import dataclass, field
from typing import Optional

@dataclass
class Rectangle:
    width: float
    height: float
    area: Optional[float] = field(init=False)  # Nicht im __init__
    perimeter: Optional[float] = field(init=False)
    
    def __post_init__(self):
        """Berechne abgeleitete Werte"""
        self.area = self.width * self.height
        self.perimeter = 2 * (self.width + self.height)

# Verwendung
rect = Rectangle(10, 5)
print(f"Fläche: {rect.area}")  # 50
print(f"Umfang: {rect.perimeter}")  # 30

# area ist nicht im __init__
# rect2 = Rectangle(10, 5, area=50)  # TypeError!
```

### InitVar - Nur für Initialisierung

`InitVar` Felder werden an `__post_init__()` übergeben, aber nicht als Instanz-Attribute gespeichert.

```python
from dataclasses import dataclass, field, InitVar
from typing import Optional

@dataclass
class DatabaseConnection:
    host: str
    port: int
    database: str
    
    # InitVar: Nur für __post_init__, nicht gespeichert
    password: InitVar[str] = None
    
    connection_string: str = field(init=False)
    
    def __post_init__(self, password: Optional[str]):
        """Erstelle connection string, speichere Passwort nicht"""
        if password:
            self.connection_string = f"postgresql://{self.host}:{self.port}/{self.database}?password=***"
        else:
            self.connection_string = f"postgresql://{self.host}:{self.port}/{self.database}"

# Verwendung
db = DatabaseConnection("localhost", 5432, "mydb", password="secret123")
print(db.connection_string)
# postgresql://localhost:5432/mydb?password=***

# Passwort ist NICHT als Attribut gespeichert
print(hasattr(db, 'password'))  # False
```

💡 **Sicherheit:** Verwende InitVar für sensitive Daten, die nicht gespeichert werden sollen.

## Vererbung mit Dataclasses

### Einfache Vererbung

```python
from dataclasses import dataclass

@dataclass
class Animal:
    name: str
    age: int

@dataclass
class Dog(Animal):
    breed: str
    
    def bark(self):
        return f"{self.name} says: Woof!"

# Verwendung
dog = Dog("Buddy", 5, "Golden Retriever")
print(dog)  # Dog(name='Buddy', age=5, breed='Golden Retriever')
print(dog.bark())  # Buddy says: Woof!
```

### Vererbungsreihenfolge bei Defaults

⚠️ **Problem:** Felder ohne Default können nicht nach Feldern mit Default kommen!

```python
from dataclasses import dataclass

@dataclass
class Base:
    name: str
    active: bool = True

# ❌ FEHLER
@dataclass
class Derived(Base):
    id: int  # TypeError: non-default argument 'id' follows default argument 'active'
```

✅ **Lösungen:**

**Lösung 1:** Defaults auch in abgeleiteter Klasse

```python
from dataclasses import dataclass

@dataclass
class Base:
    name: str
    active: bool = True

@dataclass
class Derived(Base):
    id: int = 0  # Mit Default
```

**Lösung 2:** Reihenfolge ändern

```python
from dataclasses import dataclass

@dataclass
class Base:
    name: str
    # Kein Default bei active

@dataclass
class Derived(Base):
    id: int
    active: bool = True  # Default in abgeleiteter Klasse
```

**Lösung 3:** field() mit default_factory

```python
from dataclasses import dataclass, field

@dataclass
class Base:
    name: str
    active: bool = True

@dataclass
class Derived(Base):
    id: int = field(default=0)
```

### Mehrfachvererbung

```python
from dataclasses import dataclass

@dataclass
class TimestampMixin:
    created_at: float = 0.0
    updated_at: float = 0.0

@dataclass
class UserMixin:
    username: str = ""
    email: str = ""

@dataclass
class BlogPost(TimestampMixin, UserMixin):
    title: str
    content: str
    
    def __post_init__(self):
        """Setze Timestamps wenn nicht gegeben"""
        import time
        if self.created_at == 0.0:
            self.created_at = time.time()
        if self.updated_at == 0.0:
            self.updated_at = time.time()

# Verwendung
post = BlogPost(
    title="Hello World",
    content="First post",
    username="alice",
    email="alice@example.com"
)
print(post)
```

## Properties mit Dataclasses

Dataclasses können normale Properties verwenden:

```python
from dataclasses import dataclass

@dataclass
class Circle:
    radius: float
    
    @property
    def diameter(self) -> float:
        """Berechnet Durchmesser"""
        return self.radius * 2
    
    @property
    def area(self) -> float:
        """Berechnet Fläche"""
        import math
        return math.pi * self.radius ** 2
    
    @property
    def circumference(self) -> float:
        """Berechnet Umfang"""
        import math
        return 2 * math.pi * self.radius

# Verwendung
circle = Circle(5.0)
print(f"Radius: {circle.radius}")
print(f"Durchmesser: {circle.diameter}")
print(f"Fläche: {circle.area:.2f}")
print(f"Umfang: {circle.circumference:.2f}")

# Properties sind read-only
# circle.area = 100  # AttributeError
```

### Setter für Properties

```python
from dataclasses import dataclass

@dataclass
class Temperature:
    _celsius: float = 0.0
    
    @property
    def celsius(self) -> float:
        return self._celsius
    
    @celsius.setter
    def celsius(self, value: float):
        if value < -273.15:
            raise ValueError("Temperatur unter absolutem Nullpunkt!")
        self._celsius = value
    
    @property
    def fahrenheit(self) -> float:
        return self._celsius * 9/5 + 32
    
    @fahrenheit.setter
    def fahrenheit(self, value: float):
        self.celsius = (value - 32) * 5/9
    
    @property
    def kelvin(self) -> float:
        return self._celsius + 273.15
    
    @kelvin.setter
    def kelvin(self, value: float):
        self.celsius = value - 273.15

# Verwendung
temp = Temperature()
temp.celsius = 25
print(f"{temp.celsius}°C = {temp.fahrenheit}°F = {temp.kelvin}K")
# 25.0°C = 77.0°F = 298.15K

temp.fahrenheit = 32
print(f"{temp.celsius}°C")  # 0.0°C

try:
    temp.kelvin = 100  # -173.15°C, gültig
    temp.celsius = -300  # Fehler!
except ValueError as e:
    print(f"Fehler: {e}")
```

## Methoden in Dataclasses

Dataclasses können beliebige Methoden haben:

```python
from dataclasses import dataclass
from typing import List
import json

@dataclass
class ShoppingCart:
    items: List[dict] = None
    
    def __post_init__(self):
        if self.items is None:
            self.items = []
    
    def add_item(self, name: str, price: float, quantity: int = 1):
        """Fügt Item hinzu"""
        self.items.append({
            'name': name,
            'price': price,
            'quantity': quantity
        })
    
    def remove_item(self, name: str):
        """Entfernt Item nach Name"""
        self.items = [item for item in self.items if item['name'] != name]
    
    def get_total(self) -> float:
        """Berechnet Gesamtpreis"""
        return sum(item['price'] * item['quantity'] for item in self.items)
    
    def get_item_count(self) -> int:
        """Anzahl verschiedener Items"""
        return len(self.items)
    
    def get_total_quantity(self) -> int:
        """Gesamtanzahl aller Items"""
        return sum(item['quantity'] for item in self.items)
    
    def to_json(self) -> str:
        """Exportiert als JSON"""
        return json.dumps({
            'items': self.items,
            'total': self.get_total()
        }, indent=2)
    
    def clear(self):
        """Leert Warenkorb"""
        self.items.clear()

# Verwendung
cart = ShoppingCart()
cart.add_item("Laptop", 999.99, 1)
cart.add_item("Mouse", 29.99, 2)
cart.add_item("Keyboard", 79.99, 1)

print(f"Items: {cart.get_item_count()}")  # 3
print(f"Total Quantity: {cart.get_total_quantity()}")  # 4
print(f"Total: €{cart.get_total():.2f}")  # €1139.96

print("\nJSON:")
print(cart.to_json())
```

## Klassmethoden und Staticmethods

```python
from dataclasses import dataclass
from datetime import datetime
from typing import Optional

@dataclass
class User:
    username: str
    email: str
    created_at: datetime
    last_login: Optional[datetime] = None
    
    @classmethod
    def create_new(cls, username: str, email: str):
        """Factory Method für neue User"""
        return cls(
            username=username,
            email=email,
            created_at=datetime.now()
        )
    
    @classmethod
    def from_dict(cls, data: dict):
        """Erstellt User aus Dictionary"""
        return cls(
            username=data['username'],
            email=data['email'],
            created_at=datetime.fromisoformat(data['created_at']),
            last_login=datetime.fromisoformat(data['last_login']) if data.get('last_login') else None
        )
    
    @staticmethod
    def validate_email(email: str) -> bool:
        """Validiert Email-Format"""
        return '@' in email and '.' in email.split('@')[1]
    
    def to_dict(self) -> dict:
        """Konvertiert zu Dictionary"""
        return {
            'username': self.username,
            'email': self.email,
            'created_at': self.created_at.isoformat(),
            'last_login': self.last_login.isoformat() if self.last_login else None
        }

# Verwendung
# Factory Method
user1 = User.create_new("alice", "alice@example.com")
print(user1)

# Aus Dictionary
user_data = {
    'username': 'bob',
    'email': 'bob@example.com',
    'created_at': '2024-01-01T12:00:00',
    'last_login': None
}
user2 = User.from_dict(user_data)
print(user2)

# Static Method
print(User.validate_email("test@example.com"))  # True
print(User.validate_email("invalid"))  # False

# Serialisierung
print(user1.to_dict())
```

## Spezielle Methoden (Dunder Methods)

Dataclasses können beliebige Special Methods definieren:

```python
from dataclasses import dataclass
from typing import Iterator

@dataclass
class Range:
    start: int
    end: int
    step: int = 1
    
    def __iter__(self) -> Iterator[int]:
        """Macht Range iterierbar"""
        current = self.start
        while current < self.end:
            yield current
            current += self.step
    
    def __len__(self) -> int:
        """Anzahl Elemente"""
        return max(0, (self.end - self.start + self.step - 1) // self.step)
    
    def __contains__(self, value: int) -> bool:
        """Membership Test"""
        if self.step > 0:
            return self.start <= value < self.end and (value - self.start) % self.step == 0
        return False
    
    def __getitem__(self, index: int) -> int:
        """Index-Zugriff"""
        if index < 0:
            index = len(self) + index
        
        if index < 0 or index >= len(self):
            raise IndexError("Index out of range")
        
        return self.start + index * self.step

# Verwendung
r = Range(0, 10, 2)

# Iteration
print(list(r))  # [0, 2, 4, 6, 8]

# Length
print(len(r))  # 5

# Membership
print(4 in r)  # True
print(5 in r)  # False

# Index access
print(r[0])  # 0
print(r[2])  # 4
print(r[-1])  # 8
```

### Arithmetische Operationen

```python
from dataclasses import dataclass

@dataclass
class Vector2D:
    x: float
    y: float
    
    def __add__(self, other: 'Vector2D') -> 'Vector2D':
        """Vector Addition"""
        return Vector2D(self.x + other.x, self.y + other.y)
    
    def __sub__(self, other: 'Vector2D') -> 'Vector2D':
        """Vector Subtraction"""
        return Vector2D(self.x - other.x, self.y - other.y)
    
    def __mul__(self, scalar: float) -> 'Vector2D':
        """Scalar Multiplication"""
        return Vector2D(self.x * scalar, self.y * scalar)
    
    def __truediv__(self, scalar: float) -> 'Vector2D':
        """Scalar Division"""
        return Vector2D(self.x / scalar, self.y / scalar)
    
    def __abs__(self) -> float:
        """Magnitude"""
        return (self.x ** 2 + self.y ** 2) ** 0.5
    
    def __neg__(self) -> 'Vector2D':
        """Negation"""
        return Vector2D(-self.x, -self.y)
    
    def dot(self, other: 'Vector2D') -> float:
        """Dot Product"""
        return self.x * other.x + self.y * other.y

# Verwendung
v1 = Vector2D(3, 4)
v2 = Vector2D(1, 2)

print(v1 + v2)  # Vector2D(x=4, y=6)
print(v1 - v2)  # Vector2D(x=2, y=2)
print(v1 * 2)   # Vector2D(x=6, y=8)
print(v1 / 2)   # Vector2D(x=1.5, y=2.0)
print(abs(v1))  # 5.0
print(-v1)      # Vector2D(x=-3, y=-4)
print(v1.dot(v2))  # 11.0
```

## Serialisierung und Deserialisierung

### JSON Serialisierung

```python
from dataclasses import dataclass, asdict, astuple
from typing import List
import json

@dataclass
class Address:
    street: str
    city: str
    zip: str

@dataclass
class Person:
    name: str
    age: int
    address: Address
    hobbies: List[str]

# Erstelle Instanz
person = Person(
    name="Alice",
    age=30,
    address=Address("Main St", "Berlin", "10115"),
    hobbies=["Reading", "Coding"]
)

# Als Dictionary
person_dict = asdict(person)
print(person_dict)
# {'name': 'Alice', 'age': 30, 
#  'address': {'street': 'Main St', 'city': 'Berlin', 'zip': '10115'},
#  'hobbies': ['Reading', 'Coding']}

# Als Tuple
person_tuple = astuple(person)
print(person_tuple)
# ('Alice', 30, ('Main St', 'Berlin', '10115'), ['Reading', 'Coding'])

# JSON
json_str = json.dumps(asdict(person), indent=2)
print(json_str)

# Von JSON zurück
data = json.loads(json_str)
person2 = Person(
    name=data['name'],
    age=data['age'],
    address=Address(**data['address']),
    hobbies=data['hobbies']
)
print(person2 == person)  # True
```

### Custom Serialization

```python
from dataclasses import dataclass, field
from datetime import datetime
from typing import Any
import json

@dataclass
class SerializableMixin:
    """Mixin für JSON-Serialisierung"""
    
    def to_dict(self) -> dict:
        """Konvertiert zu Dictionary mit Custom Logic"""
        result = {}
        for key, value in self.__dict__.items():
            if isinstance(value, datetime):
                result[key] = value.isoformat()
            elif isinstance(value, SerializableMixin):
                result[key] = value.to_dict()
            elif isinstance(value, list):
                result[key] = [
                    item.to_dict() if isinstance(item, SerializableMixin) else item
                    for item in value
                ]
            else:
                result[key] = value
        return result
    
    def to_json(self, **kwargs) -> str:
        """Exportiert als JSON"""
        return json.dumps(self.to_dict(), **kwargs)

@dataclass
class Task(SerializableMixin):
    title: str
    completed: bool
    created_at: datetime
    
    @classmethod
    def from_dict(cls, data: dict) -> 'Task':
        return cls(
            title=data['title'],
            completed=data['completed'],
            created_at=datetime.fromisoformat(data['created_at'])
        )

@dataclass
class TodoList(SerializableMixin):
    name: str
    tasks: List[Task] = field(default_factory=list)
    
    @classmethod
    def from_dict(cls, data: dict) -> 'TodoList':
        return cls(
            name=data['name'],
            tasks=[Task.from_dict(t) for t in data['tasks']]
        )

# Verwendung
todo = TodoList(
    name="Work",
    tasks=[
        Task("Review PR", False, datetime.now()),
        Task("Fix bug", True, datetime.now())
    ]
)

# Serialisierung
json_output = todo.to_json(indent=2)
print(json_output)

# Deserialisierung
data = json.loads(json_output)
todo2 = TodoList.from_dict(data)
print(todo2)
```

## Praktische Patterns

### Builder Pattern

```python
from dataclasses import dataclass, field
from typing import Optional, List

@dataclass
class EmailBuilder:
    """Builder für Email-Objekte"""
    _to: List[str] = field(default_factory=list)
    _cc: List[str] = field(default_factory=list)
    _bcc: List[str] = field(default_factory=list)
    _subject: str = ""
    _body: str = ""
    _attachments: List[str] = field(default_factory=list)
    
    def to(self, *recipients: str) -> 'EmailBuilder':
        """Fügt Empfänger hinzu"""
        self._to.extend(recipients)
        return self
    
    def cc(self, *recipients: str) -> 'EmailBuilder':
        """Fügt CC hinzu"""
        self._cc.extend(recipients)
        return self
    
    def subject(self, text: str) -> 'EmailBuilder':
        """Setzt Betreff"""
        self._subject = text
        return self
    
    def body(self, text: str) -> 'EmailBuilder':
        """Setzt Body"""
        self._body = text
        return self
    
    def attach(self, *files: str) -> 'EmailBuilder':
        """Fügt Anhänge hinzu"""
        self._attachments.extend(files)
        return self
    
    def build(self) -> 'Email':
        """Erstellt Email-Objekt"""
        if not self._to:
            raise ValueError("Mindestens ein Empfänger erforderlich")
        if not self._subject:
            raise ValueError("Betreff erforderlich")
        
        return Email(
            to=self._to.copy(),
            cc=self._cc.copy(),
            bcc=self._bcc.copy(),
            subject=self._subject,
            body=self._body,
            attachments=self._attachments.copy()
        )

@dataclass(frozen=True)
class Email:
    """Immutable Email"""
    to: List[str]
    cc: List[str]
    bcc: List[str]
    subject: str
    body: str
    attachments: List[str]

# Verwendung
email = (EmailBuilder()
         .to("alice@example.com", "bob@example.com")
         .cc("manager@example.com")
         .subject("Project Update")
         .body("Here's the latest update...")
         .attach("report.pdf", "chart.png")
         .build())

print(email)
```

### State Machine Pattern

```python
from dataclasses import dataclass
from enum import Enum, auto
from typing import Optional

class OrderState(Enum):
    PENDING = auto()
    PAID = auto()
    SHIPPED = auto()
    DELIVERED = auto()
    CANCELLED = auto()

@dataclass
class Order:
    order_id: str
    amount: float
    state: OrderState = OrderState.PENDING
    tracking_number: Optional[str] = None
    
    def pay(self):
        """Markiert als bezahlt"""
        if self.state == OrderState.PENDING:
            self.state = OrderState.PAID
            return True
        raise ValueError(f"Kann nicht bezahlen im State {self.state}")
    
    def ship(self, tracking: str):
        """Versendet Bestellung"""
        if self.state == OrderState.PAID:
            self.state = OrderState.SHIPPED
            self.tracking_number = tracking
            return True
        raise ValueError(f"Kann nicht versenden im State {self.state}")
    
    def deliver(self):
        """Markiert als zugestellt"""
        if self.state == OrderState.SHIPPED:
            self.state = OrderState.DELIVERED
            return True
        raise ValueError(f"Kann nicht zustellen im State {self.state}")
    
    def cancel(self):
        """Storniert Bestellung"""
        if self.state in (OrderState.PENDING, OrderState.PAID):
            self.state = OrderState.CANCELLED
            return True
        raise ValueError(f"Kann nicht stornieren im State {self.state}")
    
    def can_cancel(self) -> bool:
        """Prüft ob stornierbar"""
        return self.state in (OrderState.PENDING, OrderState.PAID)

# Verwendung
order = Order("ORD-123", 99.99)
print(f"State: {order.state}")  # PENDING

order.pay()
print(f"State: {order.state}")  # PAID

order.ship("TRACK-456")
print(f"State: {order.state}, Tracking: {order.tracking_number}")

try:
    order.cancel()  # Fehler: schon versendet
except ValueError as e:
    print(f"Fehler: {e}")

order.deliver()
print(f"Final State: {order.state}")  # DELIVERED
```

## Übungsaufgaben

Siehe [[04_Fortgeschritten/04_Dataclasses/04_Übungen Dataclasses|Übungen Dataclasses]] für praktische Aufgaben.

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [dataclasses - Post-init processing](https://docs.python.org/3/library/dataclasses.html#post-init-processing) - __post_init__ Details
- [dataclasses - Inheritance](https://docs.python.org/3/library/dataclasses.html#inheritance) - Vererbung
- [dataclasses - Module-level functions](https://docs.python.org/3/library/dataclasses.html#module-level-decorators-classes-and-functions) - asdict, astuple

## Verwandte Themen

- [[04_Fortgeschritten/04_Dataclasses/01_Dataclass Basics|Dataclass Basics]] - Grundlagen
- [[04_Fortgeschritten/04_Dataclasses/03_Field Options|Field Options]] - field() im Detail
- [[04_Fortgeschritten/04_Dataclasses/04_Übungen Dataclasses|Übungen Dataclasses]] - Praktische Übungen
- [[05_Pattern_Matching/02_Type_Hints/01_Type Annotations Basics|Type Annotations]] - Type Hints verstehen

---
← [[04_Fortgeschritten/04_Dataclasses/01_Dataclass Basics|Dataclass Basics]] | [[INDEX|📑 Index]] | [[04_Fortgeschritten/04_Dataclasses/03_Field Options|Field Options]] →