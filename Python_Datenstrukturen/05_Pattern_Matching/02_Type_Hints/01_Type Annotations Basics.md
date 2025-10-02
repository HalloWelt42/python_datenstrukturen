---
tags: [python, datenstrukturen, type-hints, typing]
created: 2025-10-02
---

← [[05_Pattern_Matching/01_Grundlagen/03_Übungen Pattern Matching|Übungen Pattern Matching]] | [[INDEX|📑 Index]] | [[05_Pattern_Matching/02_Type_Hints/02_Generics|Generics]] →

# Type Annotations Basics

> [!tip] Lernziel
> Du verstehst Type Hints in Python, kannst sie korrekt verwenden und weißt, wie sie zur Code-Qualität beitragen.

## Was sind Type Annotations?

**Type Annotations** (Type Hints) sind optionale Typ-Deklarationen in Python, die seit Version 3.5 verfügbar sind. Sie dokumentieren, welche Typen erwartet werden, werden aber zur Laufzeit **nicht** erzwungen.

**Ohne Type Hints:**
```python
def greet(name):
    return f"Hello, {name}!"

result = greet("Max")     # OK
result = greet(123)       # Auch OK - aber macht keinen Sinn
result = greet(None)      # Auch OK - könnte crashen
```

**Mit Type Hints:**
```python
def greet(name: str) -> str:
    return f"Hello, {name}!"

result = greet("Max")     # OK
result = greet(123)       # Type-Checker warnt!
result = greet(None)      # Type-Checker warnt!
```

> [!note] Wichtig
> Type Hints sind **optional** und werden zur Laufzeit **nicht** geprüft. Sie dienen der Dokumentation und statischen Analyse durch Tools wie mypy, pyright oder pylance.

## Syntax und Grundlagen

### Basis-Typen

```python
# Einfache Typen
def add(a: int, b: int) -> int:
    return a + b

def get_name() -> str:
    return "Max"

def calculate_average(numbers: list) -> float:
    return sum(numbers) / len(numbers)

def is_valid(flag: bool) -> bool:
    return not flag

def do_nothing() -> None:  # None für Funktionen ohne Rückgabewert
    print("Nothing returned")
```

### Variable Annotationen

```python
# Variablen können auch annotiert werden
name: str = "Max"
age: int = 30
price: float = 19.99
is_active: bool = True
data: list = [1, 2, 3]
config: dict = {"debug": True}

# Annotation ohne Initialisierung
counter: int
counter = 0  # Wird später zugewiesen

# Mehrere Variablen
x: int
y: int
x, y = 10, 20
```

### Built-in Collection Types

```python
from typing import List, Dict, Set, Tuple

# Liste mit spezifischem Typ
def process_numbers(numbers: List[int]) -> int:
    return sum(numbers)

# Dictionary mit Key- und Value-Typen
def get_user_age(users: Dict[str, int], name: str) -> int:
    return users.get(name, 0)

# Set
def unique_names(names: Set[str]) -> int:
    return len(names)

# Tuple mit festen Typen
def get_coordinates() -> Tuple[float, float]:
    return (52.5200, 13.4050)  # Berlin

# Tuple variabler Länge (alle gleicher Typ)
def get_numbers() -> Tuple[int, ...]:
    return (1, 2, 3, 4, 5)

# Beispiele
print(process_numbers([1, 2, 3]))           # 6
print(get_user_age({"Max": 30}, "Max"))     # 30
print(get_coordinates())                     # (52.52, 13.405)
```

### Python 3.9+ - Vereinfachte Syntax

```python
# Ab Python 3.9 können built-in types direkt verwendet werden
def process_numbers(numbers: list[int]) -> int:
    return sum(numbers)

def get_user_age(users: dict[str, int], name: str) -> int:
    return users.get(name, 0)

def unique_names(names: set[str]) -> int:
    return len(names)

def get_coordinates() -> tuple[float, float]:
    return (52.5200, 13.4050)

# Vorteil: Keine Imports aus typing nötig für einfache Fälle
```

## Details und Interna

### Optional und None

```python
from typing import Optional

# Optional[T] ist Kurzform für T | None
def find_user(user_id: int) -> Optional[str]:
    """Gibt Username zurück oder None"""
    users = {1: "Max", 2: "Anna"}
    return users.get(user_id)

# Ab Python 3.10: Vereinfachte Syntax mit |
def find_user_new(user_id: int) -> str | None:
    users = {1: "Max", 2: "Anna"}
    return users.get(user_id)

# Verwendung
username = find_user(1)
if username is not None:
    print(f"Found: {username}")
else:
    print("User not found")
```

### Union Types

```python
from typing import Union

# Union für mehrere mögliche Typen
def process_id(id_value: Union[int, str]) -> str:
    """Akzeptiert int oder str"""
    return str(id_value)

# Ab Python 3.10: | Operator
def process_id_new(id_value: int | str) -> str:
    return str(id_value)

# Union mit mehreren Typen
def parse_config(value: int | float | str | bool) -> str:
    return str(value)

# Beispiele
print(process_id(123))        # "123"
print(process_id("ABC"))      # "ABC"
print(parse_config(True))     # "True"
print(parse_config(3.14))     # "3.14"
```

### Any Type

```python
from typing import Any

# Any erlaubt jeden Typ (wie kein Type Hint)
def log_value(value: Any) -> None:
    print(f"Value: {value}")

# Nützlich für generische Funktionen
def safe_get(dictionary: dict[str, Any], key: str, default: Any = None) -> Any:
    return dictionary.get(key, default)

# Beispiele
log_value(42)           # OK
log_value("text")       # OK
log_value([1, 2, 3])    # OK

config = {"debug": True, "port": 8080, "name": "Server"}
print(safe_get(config, "port"))      # 8080
print(safe_get(config, "missing"))   # None
```

### Callable (Funktionen als Parameter)

```python
from typing import Callable

# Callable[[Param-Typen], Return-Typ]
def apply_twice(func: Callable[[int], int], value: int) -> int:
    """Wendet Funktion zweimal an"""
    return func(func(value))

def double(x: int) -> int:
    return x * 2

def square(x: int) -> int:
    return x * x

print(apply_twice(double, 5))   # double(double(5)) = 20
print(apply_twice(square, 3))   # square(square(3)) = 81

# Callback-Funktion
def process_data(
    data: list[int],
    callback: Callable[[int], None]
) -> None:
    for item in data:
        callback(item)

def print_value(x: int) -> None:
    print(f"Value: {x}")

process_data([1, 2, 3], print_value)
# Output: Value: 1, Value: 2, Value: 3
```

### Type Aliases

```python
from typing import List, Dict, Tuple

# Type Alias für bessere Lesbarkeit
UserId = int
Username = str
UserData = Dict[UserId, Username]
Coordinates = Tuple[float, float]
Point = Tuple[int, int]

def get_user(users: UserData, user_id: UserId) -> Username | None:
    return users.get(user_id)

def distance(p1: Point, p2: Point) -> float:
    x1, y1 = p1
    x2, y2 = p2
    return ((x2 - x1)**2 + (y2 - y1)**2)**0.5

# Verwendung
users: UserData = {1: "Max", 2: "Anna"}
print(get_user(users, 1))        # "Max"
print(distance((0, 0), (3, 4)))  # 5.0

# Komplexere Aliases
JsonDict = Dict[str, Any]
Matrix = List[List[float]]

def parse_json(data: str) -> JsonDict:
    import json
    return json.loads(data)

def add_matrices(m1: Matrix, m2: Matrix) -> Matrix:
    return [[a + b for a, b in zip(row1, row2)] 
            for row1, row2 in zip(m1, m2)]
```

### Literal Types

```python
from typing import Literal

# Literal für exakte Werte
def set_mode(mode: Literal["debug", "production", "test"]) -> None:
    print(f"Mode: {mode}")

# Type-Checker prüft nur erlaubte Werte
set_mode("debug")       # OK
set_mode("production")  # OK
# set_mode("invalid")   # Type-Checker Fehler!

# Mit Zahlen
def roll_dice() -> Literal[1, 2, 3, 4, 5, 6]:
    import random
    return random.randint(1, 6)  # type: ignore

# Boolean Literal
def get_flag() -> Literal[True]:
    """Gibt immer True zurück"""
    return True
```

## Praktische Anwendungen

### Anwendungsfall 1: API-Funktion mit klaren Typen

```python
from typing import Dict, List, Optional

def fetch_user_data(
    user_id: int,
    include_posts: bool = False
) -> Dict[str, Any]:
    """
    Lädt Benutzerdaten von API.
    
    Args:
        user_id: ID des Benutzers
        include_posts: Ob Posts inkludiert werden sollen
        
    Returns:
        Dictionary mit Benutzerdaten
    """
    user = {
        "id": user_id,
        "name": f"User{user_id}",
        "email": f"user{user_id}@example.com"
    }
    
    if include_posts:
        user["posts"] = [
            {"id": 1, "title": "First Post"},
            {"id": 2, "title": "Second Post"}
        ]
    
    return user

# Verwendung mit IDE-Support
data = fetch_user_data(123, include_posts=True)
print(data["name"])  # IDE kann Keys vorschlagen
```

### Anwendungsfall 2: Datenverarbeitung

```python
from typing import List, Tuple, Callable

def filter_and_transform(
    data: List[int],
    predicate: Callable[[int], bool],
    transformer: Callable[[int], str]
) -> List[str]:
    """
    Filtert und transformiert Daten.
    
    Args:
        data: Liste von Zahlen
        predicate: Filterfunktion (True = behalten)
        transformer: Transformationsfunktion
        
    Returns:
        Liste transformierter Werte
    """
    return [transformer(x) for x in data if predicate(x)]

# Verwendung
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

# Gerade Zahlen zu Strings
result = filter_and_transform(
    numbers,
    lambda x: x % 2 == 0,
    lambda x: f"Number: {x}"
)
print(result)
# ['Number: 2', 'Number: 4', 'Number: 6', 'Number: 8', 'Number: 10']
```

### Anwendungsfall 3: Konfiguration

```python
from typing import TypedDict, NotRequired

# TypedDict für strukturierte Dictionaries (Python 3.8+)
class ServerConfig(TypedDict):
    host: str
    port: int
    debug: bool
    ssl: NotRequired[bool]  # Optional field (Python 3.11+)

def start_server(config: ServerConfig) -> None:
    """Startet Server mit gegebener Konfiguration"""
    print(f"Starting server on {config['host']}:{config['port']}")
    if config.get('debug'):
        print("Debug mode enabled")
    if config.get('ssl'):
        print("SSL enabled")

# Verwendung - IDE prüft Struktur
config: ServerConfig = {
    "host": "localhost",
    "port": 8080,
    "debug": True,
    "ssl": True
}

start_server(config)
# config["invalid_key"] = "test"  # Type-Checker Fehler!
```

### Anwendungsfall 4: Class mit Type Hints

```python
from typing import List, Optional, ClassVar

class User:
    """Benutzer-Klasse mit Type Hints"""
    
    # Class Variable
    total_users: ClassVar[int] = 0
    
    def __init__(
        self,
        username: str,
        email: str,
        age: Optional[int] = None
    ) -> None:
        self.username: str = username
        self.email: str = email
        self.age: Optional[int] = age
        self.posts: List[str] = []
        User.total_users += 1
    
    def add_post(self, content: str) -> None:
        """Fügt Post hinzu"""
        self.posts.append(content)
    
    def get_posts(self) -> List[str]:
        """Gibt alle Posts zurück"""
        return self.posts
    
    def get_age_status(self) -> str:
        """Gibt Alters-Status zurück"""
        if self.age is None:
            return "Age unknown"
        elif self.age < 18:
            return "Minor"
        else:
            return "Adult"
    
    @classmethod
    def get_user_count(cls) -> int:
        """Gibt Anzahl aller User zurück"""
        return cls.total_users

# Verwendung
user = User("max", "max@example.com", 30)
user.add_post("Hello World")
user.add_post("Second post")

print(user.get_posts())         # ['Hello World', 'Second post']
print(user.get_age_status())    # "Adult"
print(User.get_user_count())    # 1
```

## Häufige Fallstricke

### ❌ Fallstrick 1: List vs list verwechseln

```python
from typing import List

# Vor Python 3.9
def process_old(items: List[int]) -> int:  # List aus typing
    return sum(items)

# Ab Python 3.9
def process_new(items: list[int]) -> int:  # built-in list
    return sum(items)

# NICHT mischen!
# def process_mixed(items: list[int]) -> List[int]:  # Inkonsistent
```

✅ **Richtig:**
```python
# Konsistent bleiben - entweder typing oder built-in
# Python 3.9+: Bevorzuge built-in
def process_items(items: list[int]) -> list[int]:
    return [x * 2 for x in items]
```

### ❌ Fallstrick 2: Mutable Default Arguments

```python
# FALSCH - Mutable default wird geteilt!
def add_item(item: str, items: list[str] = []) -> list[str]:
    items.append(item)
    return items

list1 = add_item("a")  # ['a']
list2 = add_item("b")  # ['a', 'b'] - Ups!
```

✅ **Richtig:**
```python
def add_item(item: str, items: list[str] | None = None) -> list[str]:
    if items is None:
        items = []
    items.append(item)
    return items

list1 = add_item("a")  # ['a']
list2 = add_item("b")  # ['b'] - Korrekt!
```

### ❌ Fallstrick 3: Type Hints zur Laufzeit prüfen

```python
# Type Hints werden NICHT zur Laufzeit geprüft
def add(a: int, b: int) -> int:
    return a + b

result = add("hello", "world")  # Kein Runtime-Error!
print(result)  # "helloworld"
```

✅ **Richtig:**
```python
# Manuelle Prüfung wenn nötig
def add(a: int, b: int) -> int:
    if not isinstance(a, int) or not isinstance(b, int):
        raise TypeError("Both arguments must be integers")
    return a + b

# Oder: Type-Checker verwenden (mypy, pyright)
# $ mypy script.py
# error: Argument 1 has incompatible type "str"; expected "int"
```

### ❌ Fallstrick 4: Self-Reference ohne Quotes

```python
# FALSCH - Klasse noch nicht definiert
class Node:
    def __init__(self, value: int, next: Node = None):  # NameError!
        self.value = value
        self.next = next
```

✅ **Richtig:**
```python
from __future__ import annotations  # Python 3.7+

# Option 1: String annotation (vor Python 3.10)
class Node:
    def __init__(self, value: int, next: 'Node | None' = None):
        self.value = value
        self.next = next

# Option 2: from __future__ import annotations (Python 3.7+)
from __future__ import annotations

class Node:
    def __init__(self, value: int, next: Node | None = None):
        self.value = value
        self.next = next
```

## Performance-Hinweise

### Type Hints haben (fast) keinen Performance-Impact

```python
# Type Hints werden zur Laufzeit ignoriert
def add_no_hints(a, b):
    return a + b

def add_with_hints(a: int, b: int) -> int:
    return a + b

# Beide sind gleich schnell!
# Type Hints werden nur von statischen Analysatoren genutzt
```

### Wann Type Hints verwenden

**✅ Empfohlen für:**
- Öffentliche APIs und Bibliotheken
- Komplexe Funktionen mit vielen Parametern
- Code der im Team entwickelt wird
- Langlebige Projekte
- Wenn IDE-Unterstützung wichtig ist

**❌ Optional für:**
- Sehr einfache Funktionen
- Temporäre Skripte
- Offensichtliche Typen
- Private Helper-Funktionen

## Übungsaufgaben

### Übung 1: Funktionen annotieren (Leicht)

Füge Type Hints zu diesen Funktionen hinzu:

```python
def multiply(a, b):
    return a * b

def get_full_name(first, last):
    return f"{first} {last}"

def count_words(text):
    return len(text.split())

def is_even(number):
    return number % 2 == 0
```

### Übung 2: List Processing (Leicht)

Schreibe eine Funktion mit Type Hints:
- Name: `filter_positive`
- Parameter: Liste von Zahlen (int oder float)
- Return: Liste mit nur positiven Zahlen

**Erwarteter Output:**
```python
print(filter_positive([1, -2, 3, -4, 5]))  # [1, 3, 5]
print(filter_positive([1.5, -2.3, 0, 3.7])) # [1.5, 3.7]
```

### Übung 3: User Management (Mittel)

Erstelle ein User-Management-System mit Type Hints:
- `User` Klasse mit: id (int), name (str), email (str), active (bool)
- `UserDatabase` Klasse mit Methoden:
  - `add_user(user: User) -> None`
  - `get_user(user_id: int) -> User | None`
  - `get_active_users() -> list[User]`

### Übung 4: Generic Function (Mittel)

Schreibe eine Funktion `safe_divide` mit Type Hints:
- Dividiert zwei Zahlen
- Gibt Ergebnis oder Fehlermeldung zurück
- Return-Type: `float | str`

**Erwarteter Output:**
```python
print(safe_divide(10, 2))   # 5.0
print(safe_divide(10, 0))   # "Error: Division by zero"
```

### Übung 5: TypedDict Config (Schwer)

Erstelle ein Konfigurations-System mit TypedDict:
- `DatabaseConfig` mit: host, port, username, password
- `ServerConfig` mit: debug, log_level, database (DatabaseConfig)
- Funktion `validate_config` die prüft ob alle Felder vorhanden

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [typing - Type Hints](https://docs.python.org/3/library/typing.html) - Vollständige typing-Modul Dokumentation
- [PEP 484 - Type Hints](https://peps.python.org/pep-0484/) - Original Type Hints Proposal
- [PEP 585 - Type Hinting Generics](https://peps.python.org/pep-0585/) - Built-in Generic Types
- [mypy Documentation](http://mypy-lang.org/) - Type Checker Tool

## Verwandte Themen

- [[05_Pattern_Matching/02_Type_Hints/02_Generics|Generics]] - Generische Typen
- [[05_Pattern_Matching/02_Type_Hints/03_Union Types|Union Types]] - Union und Optional
- [[05_Pattern_Matching/02_Type_Hints/04_Type Aliases|Type Aliases]] - Custom Type Definitions
- [[04_Fortgeschritten/04_Dataclasses/01_Dataclass Basics|Dataclass Basics]] - Dataclasses mit Type Hints

---
← [[05_Pattern_Matching/01_Grundlagen/03_Übungen Pattern Matching|Übungen Pattern Matching]] | [[INDEX|📑 Index]] | [[05_Pattern_Matching/02_Type_Hints/02_Generics|Generics]] →