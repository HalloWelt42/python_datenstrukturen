---
tags: [python, datenstrukturen, lösungen]
created: 2025-10-02
---

← [[06_Praxis/04_Lösungen/Lösungen Teil 4|Lösungen Teil 4]] | [[INDEX|📑 Index]] | [[06_Praxis/04_Lösungen/Lösungen Projekte|Lösungen Projekte]] →

# Lösungen Teil 5: Pattern Matching & Type Hints

> [!tip] Hinweis
> Diese Lösungen zeigen moderne Python-Features (3.10+) mit Pattern Matching und Type Hints

## 05_Pattern_Matching/01_Grundlagen

### Match Statement - Übungen

**Übung 1: Basic Pattern Matching**
```python
def describe_value(value):
    """Beschreibt Wert mit Pattern Matching"""
    match value:
        case 0:
            return "Zero"
        case 1:
            return "One"
        case int(x) if x > 0:
            return f"Positive integer: {x}"
        case int(x) if x < 0:
            return f"Negative integer: {x}"
        case float(x):
            return f"Float: {x}"
        case str(s):
            return f"String: {s}"
        case list():
            return "Empty list"
        case list(items):
            return f"List with {len(items)} items"
        case _:
            return "Unknown type"

# Tests
print(describe_value(0))        # Zero
print(describe_value(42))       # Positive integer: 42
print(describe_value(-5))       # Negative integer: -5
print(describe_value(3.14))     # Float: 3.14
print(describe_value("hello"))  # String: hello
print(describe_value([]))       # Empty list
print(describe_value([1,2,3]))  # List with 3 items
```

**Übung 2: Sequence Patterns**
```python
def analyze_point(point):
    """Analysiert Koordinaten-Punkt"""
    match point:
        case [0, 0]:
            return "Origin"
        case [0, y]:
            return f"Y-axis at y={y}"
        case [x, 0]:
            return f"X-axis at x={x}"
        case [x, y]:
            return f"Point at ({x}, {y})"
        case [x, y, z]:
            return f"3D Point at ({x}, {y}, {z})"
        case _:
            return "Invalid point"

# Tests
print(analyze_point([0, 0]))     # Origin
print(analyze_point([0, 5]))     # Y-axis at y=5
print(analyze_point([3, 0]))     # X-axis at x=3
print(analyze_point([2, 3]))     # Point at (2, 3)
print(analyze_point([1, 2, 3]))  # 3D Point at (1, 2, 3)

def process_list(items):
    """Verarbeitet Listen unterschiedlicher Größe"""
    match items:
        case []:
            return "Empty"
        case [x]:
            return f"Single: {x}"
        case [x, y]:
            return f"Pair: {x}, {y}"
        case [first, *middle, last]:
            return f"First: {first}, Middle: {middle}, Last: {last}"

print(process_list([]))           # Empty
print(process_list([1]))          # Single: 1
print(process_list([1, 2]))       # Pair: 1, 2
print(process_list([1, 2, 3, 4])) # First: 1, Middle: [2, 3], Last: 4
```

**Übung 3: Mapping Patterns**
```python
def handle_request(request):
    """Verarbeitet Request-Dictionary"""
    match request:
        case {"method": "GET", "path": path}:
            return f"GET request to {path}"
        
        case {"method": "POST", "path": path, "data": data}:
            return f"POST request to {path} with data: {data}"
        
        case {"method": "DELETE", "path": path}:
            return f"DELETE request to {path}"
        
        case {"method": method, "path": path}:
            return f"Unsupported method {method} for {path}"
        
        case _:
            return "Invalid request"

# Tests
print(handle_request({"method": "GET", "path": "/users"}))
# GET request to /users

print(handle_request({"method": "POST", "path": "/users", "data": {"name": "Alice"}}))
# POST request to /users with data: {'name': 'Alice'}

print(handle_request({"method": "DELETE", "path": "/users/1"}))
# DELETE request to /users/1

def extract_user_info(user):
    """Extrahiert User-Informationen"""
    match user:
        case {"name": name, "age": age, "email": email}:
            return f"{name} ({age}) - {email}"
        
        case {"name": name, "age": age}:
            return f"{name} ({age}) - No email"
        
        case {"name": name}:
            return f"{name} - Minimal info"
        
        case _:
            return "Invalid user data"

print(extract_user_info({"name": "Alice", "age": 30, "email": "alice@example.com"}))
# Alice (30) - alice@example.com
```

**Übung 4: Class Patterns**
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

@dataclass
class Rectangle:
    top_left: Point
    width: float
    height: float

def describe_shape(shape):
    """Beschreibt geometrische Form"""
    match shape:
        case Point(x=0, y=0):
            return "Point at origin"
        
        case Point(x=x, y=y):
            return f"Point at ({x}, {y})"
        
        case Circle(center=Point(x=0, y=0), radius=r):
            return f"Circle at origin with radius {r}"
        
        case Circle(center=Point(x=x, y=y), radius=r):
            return f"Circle at ({x}, {y}) with radius {r}"
        
        case Rectangle(top_left=Point(x=x, y=y), width=w, height=h):
            return f"Rectangle at ({x}, {y}) with size {w}x{h}"
        
        case _:
            return "Unknown shape"

# Tests
print(describe_shape(Point(0, 0)))
# Point at origin

print(describe_shape(Circle(Point(5, 5), 10)))
# Circle at (5, 5) with radius 10

print(describe_shape(Rectangle(Point(0, 0), 100, 50)))
# Rectangle at (0, 0) with size 100x50
```

**Übung 5: Guards und OR Patterns**
```python
def classify_number(n):
    """Klassifiziert Zahl mit Guards"""
    match n:
        case int(x) if x == 0:
            return "Zero"
        
        case int(x) if x > 0 and x % 2 == 0:
            return "Positive even"
        
        case int(x) if x > 0 and x % 2 == 1:
            return "Positive odd"
        
        case int(x) if x < 0:
            return "Negative"
        
        case _:
            return "Not an integer"

print(classify_number(0))   # Zero
print(classify_number(4))   # Positive even
print(classify_number(5))   # Positive odd
print(classify_number(-3))  # Negative
print(classify_number(3.14)) # Not an integer

def check_status(status_code):
    """Prüft HTTP Status mit OR-Pattern"""
    match status_code:
        case 200 | 201 | 204:
            return "Success"
        
        case 400 | 401 | 403 | 404:
            return "Client Error"
        
        case 500 | 502 | 503:
            return "Server Error"
        
        case code if 200 <= code < 300:
            return "Other success"
        
        case _:
            return "Unknown status"

print(check_status(200))  # Success
print(check_status(404))  # Client Error
print(check_status(500))  # Server Error
```

## 05_Pattern_Matching/02_Type_Hints

### Type Annotations - Übungen

**Übung 1: Basic Type Hints**
```python
def greet(name: str) -> str:
    """Begrüßt Person"""
    return f"Hello, {name}!"

def add(a: int, b: int) -> int:
    """Addiert zwei Zahlen"""
    return a + b

def calculate_average(numbers: list[float]) -> float:
    """Berechnet Durchschnitt"""
    if not numbers:
        return 0.0
    return sum(numbers) / len(numbers)

# Verwendung
print(greet("Alice"))
print(add(5, 3))
print(calculate_average([1.5, 2.5, 3.5]))

# Type Checker würde Fehler finden:
# greet(123)  # Error: Expected str, got int
# add("5", "3")  # Error: Expected int, got str
```

**Übung 2: Optional und Union**
```python
from typing import Optional, Union

def find_user(user_id: int) -> Optional[dict]:
    """Findet User, gibt None wenn nicht gefunden"""
    users = {
        1: {"name": "Alice", "age": 30},
        2: {"name": "Bob", "age": 25},
    }
    return users.get(user_id)

def process_value(value: Union[int, float, str]) -> str:
    """Verarbeitet verschiedene Typen"""
    if isinstance(value, (int, float)):
        return f"Number: {value}"
    return f"String: {value}"

# Python 3.10+ Syntax (einfacher)
def process_value_v2(value: int | float | str) -> str:
    """Verarbeitet verschiedene Typen (moderne Syntax)"""
    if isinstance(value, (int, float)):
        return f"Number: {value}"
    return f"String: {value}"

# Tests
user = find_user(1)
if user is not None:
    print(user["name"])

print(process_value(42))
print(process_value(3.14))
print(process_value("hello"))
```

**Übung 3: Generics**
```python
from typing import TypeVar, Generic, List

T = TypeVar('T')

class Stack(Generic[T]):
    """Typsicherer Stack"""
    
    def __init__(self) -> None:
        self._items: List[T] = []
    
    def push(self, item: T) -> None:
        self._items.append(item)
    
    def pop(self) -> T:
        if not self._items:
            raise IndexError("Pop from empty stack")
        return self._items.pop()
    
    def peek(self) -> T:
        if not self._items:
            raise IndexError("Peek from empty stack")
        return self._items[-1]
    
    def is_empty(self) -> bool:
        return len(self._items) == 0

# Verwendung
int_stack: Stack[int] = Stack()
int_stack.push(1)
int_stack.push(2)
print(int_stack.pop())  # 2

str_stack: Stack[str] = Stack()
str_stack.push("hello")
str_stack.push("world")
print(str_stack.pop())  # world

# Type Checker würde Fehler finden:
# int_stack.push("string")  # Error: Expected int
```

**Übung 4: Callable und Protocol**
```python
from typing import Callable, Protocol

# Callable Type Hints
def apply_operation(x: int, y: int, operation: Callable[[int, int], int]) -> int:
    """Wendet Operation auf zwei Zahlen an"""
    return operation(x, y)

def add(a: int, b: int) -> int:
    return a + b

def multiply(a: int, b: int) -> int:
    return a * b

print(apply_operation(5, 3, add))       # 8
print(apply_operation(5, 3, multiply))  # 15

# Protocol für Duck Typing
class Drawable(Protocol):
    """Protocol für zeichenbare Objekte"""
    def draw(self) -> str:
        ...

class Circle:
    def draw(self) -> str:
        return "Drawing circle"

class Square:
    def draw(self) -> str:
        return "Drawing square"

def render(shape: Drawable) -> None:
    """Rendert beliebiges Drawable-Objekt"""
    print(shape.draw())

# Beide Klassen sind Drawable (Duck Typing)
render(Circle())  # Drawing circle
render(Square())  # Drawing square
```

**Übung 5: TypedDict und Literal**
```python
from typing import TypedDict, Literal

# TypedDict für strukturierte Dicts
class UserDict(TypedDict):
    """User als TypedDict"""
    name: str
    age: int
    email: str
    active: bool

def create_user(name: str, age: int, email: str) -> UserDict:
    """Erstellt User-Dict mit Typ-Sicherheit"""
    return {
        "name": name,
        "age": age,
        "email": email,
        "active": True
    }

user: UserDict = create_user("Alice", 30, "alice@example.com")
print(user["name"])

# Literal für exakte Werte
OrderStatus = Literal["pending", "shipped", "delivered", "cancelled"]

def update_order_status(order_id: int, status: OrderStatus) -> None:
    """Updated Order-Status mit eingeschränkten Werten"""
    print(f"Order {order_id} -> {status}")

update_order_status(123, "shipped")  # OK
# update_order_status(123, "invalid")  # Type Error

# Kombination
class OrderDict(TypedDict):
    order_id: int
    status: OrderStatus
    items: list[str]

order: OrderDict = {
    "order_id": 123,
    "status": "pending",
    "items": ["item1", "item2"]
}
```

**Übung 6: NewType und Type Aliases**
```python
from typing import NewType

# Type Alias (einfache Umbenennung)
UserId = int
Username = str

def get_user(user_id: UserId) -> Username:
    """Holt Username nach ID"""
    return f"User{user_id}"

# NewType für echte Typ-Unterscheidung
UserId = NewType('UserId', int)
ProductId = NewType('ProductId', int)

def get_user_v2(user_id: UserId) -> str:
    """Holt User - akzeptiert nur UserId"""
    return f"User {user_id}"

def get_product(product_id: ProductId) -> str:
    """Holt Product - akzeptiert nur ProductId"""
    return f"Product {product_id}"

# Erstellen
user_id = UserId(123)
product_id = ProductId(456)

print(get_user_v2(user_id))        # OK
# print(get_user_v2(product_id))   # Type Error!
# print(get_user_v2(123))          # Type Error!

# Complex Type Aliases
from typing import Union, List, Dict

JsonValue = Union[None, bool, int, float, str, List['JsonValue'], Dict[str, 'JsonValue']]

def process_json(data: JsonValue) -> None:
    """Verarbeitet JSON-Daten"""
    match data:
        case None | bool() | int() | float() | str():
            print(f"Primitive: {data}")
        case list():
            print(f"List with {len(data)} items")
        case dict():
            print(f"Dict with {len(data)} keys")
```

**Übung 7: Praktisches Beispiel mit Type Hints**
```python
from typing import TypedDict, Optional, Literal
from dataclasses import dataclass
from datetime import datetime

# Types definieren
TransactionType = Literal["deposit", "withdrawal", "transfer"]

class TransactionDict(TypedDict):
    id: str
    type: TransactionType
    amount: float
    timestamp: datetime
    description: Optional[str]

@dataclass
class BankAccount:
    """Bankkonto mit Type Hints"""
    account_number: str
    owner: str
    balance: float = 0.0
    transactions: list[TransactionDict] = None
    
    def __post_init__(self) -> None:
        if self.transactions is None:
            self.transactions = []
    
    def deposit(self, amount: float, description: Optional[str] = None) -> None:
        """Einzahlung"""
        if amount <= 0:
            raise ValueError("Amount must be positive")
        
        self.balance += amount
        self._add_transaction("deposit", amount, description)
    
    def withdraw(self, amount: float, description: Optional[str] = None) -> bool:
        """Auszahlung"""
        if amount <= 0:
            raise ValueError("Amount must be positive")
        
        if amount > self.balance:
            return False
        
        self.balance -= amount
        self._add_transaction("withdrawal", amount, description)
        return True
    
    def _add_transaction(
        self, 
        trans_type: TransactionType, 
        amount: float,
        description: Optional[str]
    ) -> None:
        """Fügt Transaktion hinzu"""
        transaction: TransactionDict = {
            "id": f"TXN{len(self.transactions) + 1:04d}",
            "type": trans_type,
            "amount": amount,
            "timestamp": datetime.now(),
            "description": description
        }
        self.transactions.append(transaction)
    
    def get_transaction_history(self) -> list[TransactionDict]:
        """Gibt Transaktions-Historie zurück"""
        return self.transactions.copy()
    
    def get_balance(self) -> float:
        """Gibt aktuellen Kontostand zurück"""
        return self.balance

# Verwendung
account = BankAccount("DE12345", "Alice")

account.deposit(1000.0, "Initial deposit")
account.deposit(500.0, "Salary")
account.withdraw(200.0, "Groceries")

print(f"Balance: {account.get_balance():.2f}€")
print(f"\nTransactions:")
for txn in account.get_transaction_history():
    print(f"  {txn['id']}: {txn['type']} {txn['amount']:.2f}€ - {txn['description']}")
```

## Zusammenfassung

**Pattern Matching (Python 3.10+):**
- ✅ Match-Statement für eleganten Verzweigungscode
- ✅ Sequence-, Mapping- und Class-Patterns
- ✅ Guards für zusätzliche Bedingungen
- ✅ OR-Patterns für mehrere Optionen

**Type Hints:**
- ✅ Basic Types: `int`, `str`, `float`, `bool`
- ✅ Collections: `list[T]`, `dict[K, V]`, `set[T]`
- ✅ Optional: `Optional[T]` oder `T | None`
- ✅ Union: `Union[T1, T2]` oder `T1 | T2`
- ✅ Generics: `Generic[T]`, `TypeVar`
- ✅ Callable: `Callable[[Args], Return]`
- ✅ Protocol: Duck Typing mit Struktur
- ✅ TypedDict: Strukturierte Dicts
- ✅ Literal: Exakte Werte
- ✅ NewType: Distinkte Typen

**Vorteile von Type Hints:**
- 🔍 Frühe Fehler-Erkennung mit Tools wie mypy
- 📚 Bessere Dokumentation
- 🛠️ IDE-Support (Autocomplete, Refactoring)
- 🐛 Einfacheres Debugging
- 🤝 Team-Kommunikation

## Python Dokumentation

📚 **Offizielle Ressourcen:**
- [Pattern Matching](https://docs.python.org/3/whatsnew/3.10.html#pep-634-structural-pattern-matching) - PEP 634
- [typing Module](https://docs.python.org/3/library/typing.html) - Type Hints
- [mypy Documentation](http://mypy-lang.org/) - Static Type Checker
- [PEP 484](https://peps.python.org/pep-0484/) - Type Hints
- [PEP 585](https://peps.python.org/pep-0585/) - Type Hinting Generics

## Verwandte Themen

- [[05_Pattern_Matching/01_Grundlagen/01_Match Statement|Match Statement]]
- [[05_Pattern_Matching/02_Type_Hints/01_Type Annotations Basics|Type Annotations]]
- [[04_Fortgeschritten/04_Dataclasses/01_Dataclass Basics|Dataclasses]]
- [[04_Fortgeschritten/05_Enums/01_Enum Basics|Enums]]

---
← [[06_Praxis/04_Lösungen/Lösungen Teil 4|Lösungen Teil 4]] | [[INDEX|📑 Index]] | [[06_Praxis/04_Lösungen/Lösungen Projekte|Lösungen Projekte]] →