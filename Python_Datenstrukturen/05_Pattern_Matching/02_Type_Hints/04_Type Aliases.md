---
tags: [python, datenstrukturen, type-hints, aliases]
created: 2025-10-02
---

← [[05_Pattern_Matching/02_Type_Hints/03_Union Types|Union Types]] | [[INDEX|📑 Index]] | [[05_Pattern_Matching/02_Type_Hints/05_Übungen Type Hints|Übungen Type Hints]] →

# Type Aliases

> [!tip] Lernziel
> Du verstehst Type Aliases, kannst komplexe Typen benennen und wiederverwendbar machen.

## Was sind Type Aliases?

**Type Aliases** sind benannte Referenzen auf komplexe Typen. Sie verbessern die Lesbarkeit, vermeiden Wiederholungen und dokumentieren die Bedeutung von Typen.

**Ohne Type Alias:**
```python
def process_user_data(
    users: dict[int, tuple[str, str, int, bool]]
) -> list[tuple[str, int]]:
    # Schwer lesbar - was bedeuten die Typen?
    result = []
    for user_id, (name, email, age, active) in users.items():
        if active:
            result.append((name, age))
    return result
```

**Mit Type Alias:**
```python
# Klar und dokumentiert
UserId = int
UserData = tuple[str, str, int, bool]  # (name, email, age, active)
UserDatabase = dict[UserId, UserData]
UserSummary = tuple[str, int]  # (name, age)

def process_user_data(users: UserDatabase) -> list[UserSummary]:
    result = []
    for user_id, (name, email, age, active) in users.items():
        if active:
            result.append((name, age))
    return result
```

## Syntax und Grundlagen

### Einfache Type Aliases

```python
# Basis-Typen umbenennen
UserId = int
Username = str
EmailAddress = str
Age = int

def create_user(
    user_id: UserId,
    name: Username,
    email: EmailAddress,
    age: Age
) -> dict[str, UserId | Username | EmailAddress | Age]:
    return {
        "id": user_id,
        "name": name,
        "email": email,
        "age": age
    }

# Verwendung
user = create_user(1, "Max", "max@example.com", 30)
print(user)
```

### Collection Type Aliases

```python
# Listen, Dicts, Sets
Numbers = list[int]
Coordinates = tuple[float, float]
UserMap = dict[int, str]
Tags = set[str]

def calculate_sum(numbers: Numbers) -> int:
    return sum(numbers)

def distance_from_origin(point: Coordinates) -> float:
    x, y = point
    return (x**2 + y**2)**0.5

def get_username(users: UserMap, user_id: int) -> str | None:
    return users.get(user_id)

# Verwendung
print(calculate_sum([1, 2, 3, 4, 5]))       # 15
print(distance_from_origin((3.0, 4.0)))     # 5.0
print(get_username({1: "Max", 2: "Anna"}, 1))  # "Max"
```

### Verschachtelte Aliases

```python
# Komplexe verschachtelte Strukturen
Address = dict[str, str]  # {"street": ..., "city": ..., "zip": ...}
Person = dict[str, str | int | Address]
Company = dict[str, str | list[Person]]

def get_company_data() -> Company:
    return {
        "name": "Tech Corp",
        "location": "Berlin",
        "employees": [
            {
                "name": "Max",
                "age": 30,
                "address": {
                    "street": "Main St",
                    "city": "Berlin",
                    "zip": "10115"
                }
            }
        ]
    }
```

### Union Type Aliases

```python
# Union-Typen benennen
JsonValue = str | int | float | bool | None
JsonDict = dict[str, JsonValue]
JsonList = list[JsonValue]
Json = JsonDict | JsonList | JsonValue

def parse_json(data: str) -> Json:
    import json
    return json.loads(data)

def stringify_json(data: Json) -> str:
    import json
    return json.dumps(data)

# ID kann String oder Int sein
Id = int | str

def get_item(item_id: Id) -> dict[str, any]:
    return {"id": item_id, "name": f"Item {item_id}"}

print(get_item(123))      # {"id": 123, ...}
print(get_item("abc"))    # {"id": "abc", ...}
```

## Details und Interna

### TypeAlias Annotation (Python 3.10+)

```python
from typing import TypeAlias

# Explizite Type Alias Deklaration
UserId: TypeAlias = int
Username: TypeAlias = str
UserData: TypeAlias = dict[str, str | int]

# Hilft Type-Checker zu verstehen, dass es ein Alias ist
# (nicht eine Variable mit diesem Typ)

# Vorher wurde manchmal verwechselt:
Name = str  # Ist das ein Alias oder Variable vom Typ type[str]?

# Mit TypeAlias ist es klar:
Name: TypeAlias = str  # Definitiv ein Type Alias
```

### Generic Type Aliases

```python
from typing import TypeVar, TypeAlias

T = TypeVar('T')

# Generischer Alias
Stack: TypeAlias = list[T]
Queue: TypeAlias = list[T]
Matrix: TypeAlias = list[list[T]]

# Verwendung
int_stack: Stack[int] = [1, 2, 3]
str_queue: Queue[str] = ["a", "b", "c"]
float_matrix: Matrix[float] = [[1.0, 2.0], [3.0, 4.0]]

# Callback-Typen
Callback: TypeAlias = callable[[T], None]
Predicate: TypeAlias = callable[[T], bool]
Transformer: TypeAlias = callable[[T], T]

def process_items(
    items: list[T],
    callback: Callback[T]
) -> None:
    for item in items:
        callback(item)
```

### Recursive Type Aliases

```python
from typing import TypeAlias

# Rekursive Typen mit String Forward Reference
JsonValue: TypeAlias = (
    None | bool | int | float | str |
    list['JsonValue'] | dict[str, 'JsonValue']
)

def pretty_print(value: JsonValue, indent: int = 0) -> None:
    """Gibt JSON formatiert aus"""
    prefix = "  " * indent
    
    if isinstance(value, dict):
        print(f"{prefix}{{")
        for key, val in value.items():
            print(f"{prefix}  {key}:", end=" ")
            if isinstance(val, (dict, list)):
                print()
                pretty_print(val, indent + 2)
            else:
                print(val)
        print(f"{prefix}}}")
    
    elif isinstance(value, list):
        print(f"{prefix}[")
        for item in value:
            pretty_print(item, indent + 1)
        print(f"{prefix}]")
    
    else:
        print(f"{prefix}{value}")

# Tree-Struktur
TreeNode: TypeAlias = int | tuple[int, 'TreeNode', 'TreeNode']

def tree_sum(node: TreeNode) -> int:
    """Summiert alle Werte im Baum"""
    if isinstance(node, int):
        return node
    value, left, right = node
    return value + tree_sum(left) + tree_sum(right)

# Binary Tree: (5, (3, 1, 2), 7)
tree: TreeNode = (5, (3, 1, 2), 7)
print(tree_sum(tree))  # 18
```

### Callable Type Aliases

```python
from typing import TypeAlias, Callable

# Function Signatures als Aliases
IntBinaryOp: TypeAlias = Callable[[int, int], int]
StringProcessor: TypeAlias = Callable[[str], str]
Validator: TypeAlias = Callable[[any], bool]
AsyncHandler: TypeAlias = Callable[[str], None]

def apply_operation(a: int, b: int, op: IntBinaryOp) -> int:
    return op(a, b)

def process_text(text: str, processor: StringProcessor) -> str:
    return processor(text)

# Verwendung
def add(x: int, y: int) -> int:
    return x + y

def multiply(x: int, y: int) -> int:
    return x * y

def uppercase(s: str) -> str:
    return s.upper()

print(apply_operation(5, 3, add))         # 8
print(apply_operation(5, 3, multiply))    # 15
print(process_text("hello", uppercase))   # "HELLO"
```

## Praktische Anwendungen

### Anwendungsfall 1: Datenbank-Modelle

```python
from typing import TypeAlias
from datetime import datetime

# ID Types
UserId: TypeAlias = int
PostId: TypeAlias = int
CommentId: TypeAlias = int

# Timestamps
Timestamp: TypeAlias = datetime

# Models
User: TypeAlias = dict[str, UserId | str | Timestamp]
Post: TypeAlias = dict[str, PostId | UserId | str | Timestamp]
Comment: TypeAlias = dict[str, CommentId | PostId | UserId | str | Timestamp]

# Queries
UserList: TypeAlias = list[User]
PostWithComments: TypeAlias = tuple[Post, list[Comment]]

def create_user(name: str, email: str) -> User:
    return {
        "id": 0,  # Auto-increment in real DB
        "name": name,
        "email": email,
        "created_at": datetime.now()
    }

def get_post_with_comments(post_id: PostId) -> PostWithComments | None:
    # Simuliert Datenbankabfrage
    post: Post = {
        "id": post_id,
        "user_id": 1,
        "title": "Test Post",
        "content": "Content here",
        "created_at": datetime.now()
    }
    
    comments: list[Comment] = [
        {
            "id": 1,
            "post_id": post_id,
            "user_id": 2,
            "text": "Nice post!",
            "created_at": datetime.now()
        }
    ]
    
    return (post, comments)
```

### Anwendungsfall 2: API-Typen

```python
from typing import TypeAlias, Literal

# HTTP
HttpMethod: TypeAlias = Literal["GET", "POST", "PUT", "DELETE", "PATCH"]
StatusCode: TypeAlias = int
Headers: TypeAlias = dict[str, str]

# Request/Response
RequestBody: TypeAlias = dict[str, any] | str | bytes | None
ResponseBody: TypeAlias = dict[str, any] | str | list[any]

Request: TypeAlias = dict[str, HttpMethod | str | Headers | RequestBody]
Response: TypeAlias = dict[str, StatusCode | Headers | ResponseBody]

# API Handler
ApiHandler: TypeAlias = Callable[[Request], Response]

def create_request(
    method: HttpMethod,
    url: str,
    body: RequestBody = None
) -> Request:
    return {
        "method": method,
        "url": url,
        "headers": {"Content-Type": "application/json"},
        "body": body
    }

def create_response(
    status: StatusCode,
    body: ResponseBody
) -> Response:
    return {
        "status": status,
        "headers": {"Content-Type": "application/json"},
        "body": body
    }

# Handler-Funktion
def user_handler(request: Request) -> Response:
    method = request["method"]
    
    if method == "GET":
        return create_response(200, {"users": []})
    elif method == "POST":
        return create_response(201, {"id": 1, "created": True})
    
    return create_response(405, {"error": "Method not allowed"})
```

### Anwendungsfall 3: Configuration System

```python
from typing import TypeAlias, Literal

# Environment
Environment: TypeAlias = Literal["development", "staging", "production"]

# Log Levels
LogLevel: TypeAlias = Literal["DEBUG", "INFO", "WARNING", "ERROR", "CRITICAL"]

# Database Config
DatabaseConfig: TypeAlias = dict[str, str | int | bool]

# Server Config
ServerConfig: TypeAlias = dict[str, str | int | bool | DatabaseConfig]

# Complete Config
AppConfig: TypeAlias = dict[str, Environment | LogLevel | bool | ServerConfig]

def create_config(env: Environment) -> AppConfig:
    """Erstellt Konfiguration basierend auf Environment"""
    
    base_config: AppConfig = {
        "environment": env,
        "debug": env == "development",
        "log_level": "DEBUG" if env == "development" else "INFO",
        "server": {
            "host": "localhost" if env == "development" else "0.0.0.0",
            "port": 8000,
            "workers": 1 if env == "development" else 4,
            "database": {
                "host": "localhost",
                "port": 5432,
                "name": f"app_{env}",
                "ssl": env == "production"
            }
        }
    }
    
    return base_config

# Verwendung
dev_config = create_config("development")
prod_config = create_config("production")

print(dev_config["debug"])           # True
print(prod_config["debug"])          # False
```

### Anwendungsfall 4: Event System

```python
from typing import TypeAlias, Literal
from dataclasses import dataclass
from datetime import datetime

# Event Types
EventType: TypeAlias = Literal[
    "user.created",
    "user.updated",
    "user.deleted",
    "post.created",
    "post.published",
    "comment.added"
]

# Event Data
EventData: TypeAlias = dict[str, any]

# Event Metadata
EventMetadata: TypeAlias = dict[str, str | datetime]

# Complete Event
@dataclass
class Event:
    type: EventType
    data: EventData
    metadata: EventMetadata

# Event Handler
EventHandler: TypeAlias = Callable[[Event], None]

# Event Handlers Map
EventHandlers: TypeAlias = dict[EventType, list[EventHandler]]

class EventBus:
    """Simple Event Bus"""
    
    def __init__(self) -> None:
        self._handlers: EventHandlers = {}
    
    def subscribe(self, event_type: EventType, handler: EventHandler) -> None:
        """Registriert Event Handler"""
        if event_type not in self._handlers:
            self._handlers[event_type] = []
        self._handlers[event_type].append(handler)
    
    def publish(self, event: Event) -> None:
        """Veröffentlicht Event"""
        handlers = self._handlers.get(event.type, [])
        for handler in handlers:
            handler(event)

# Verwendung
bus = EventBus()

def log_user_created(event: Event) -> None:
    print(f"User created: {event.data['name']}")

def send_welcome_email(event: Event) -> None:
    print(f"Sending welcome email to {event.data['email']}")

bus.subscribe("user.created", log_user_created)
bus.subscribe("user.created", send_welcome_email)

# Event erstellen und publishen
user_event = Event(
    type="user.created",
    data={"name": "Max", "email": "max@example.com"},
    metadata={"timestamp": datetime.now(), "source": "api"}
)

bus.publish(user_event)
# Output:
# User created: Max
# Sending welcome email to max@example.com
```

## Häufige Fallstricke

### ❌ Fallstrick 1: Alias vs. NewType verwechseln

```python
# Type Alias - nur für Type Checker
UserId = int

# Zur Laufzeit ist es dasselbe
user_id: UserId = 123
normal_int: int = 123
print(user_id == normal_int)  # True

# NewType - erstellt neuen Typ
from typing import NewType

UserId2 = NewType('UserId2', int)

user_id2 = UserId2(123)
# normal_int2: UserId2 = 123  # Type-Checker Error!
```

✅ **Richtig - wann was verwenden:**
```python
# Type Alias - für Dokumentation und Lesbarkeit
EmailAddress = str  # Nur Bedeutung, keine neue Type

# NewType - für echte Type Safety
from typing import NewType

UserId = NewType('UserId', int)
PostId = NewType('PostId', int)

def get_user(user_id: UserId) -> str:
    return f"User {user_id}"

# get_user(123)  # Type Error - braucht UserId!
get_user(UserId(123))  # OK
```

### ❌ Fallstrick 2: Zu komplexe Aliases

```python
# SCHLECHT - zu komplex, schwer zu verstehen
ComplexType = dict[str, list[tuple[int, str, dict[str, list[int] | str]]]]

def process(data: ComplexType) -> None:
    pass  # Was ist data eigentlich?
```

✅ **Richtig - in kleine Teile aufteilen:**
```python
ItemData = dict[str, list[int] | str]
ItemTuple = tuple[int, str, ItemData]
ItemList = list[ItemTuple]
DataStructure = dict[str, ItemList]

def process(data: DataStructure) -> None:
    pass  # Viel klarer!
```

### ❌ Fallstrick 3: Forward Reference vergessen

```python
# FALSCH bei rekursiven Typen
# TreeNode = int | tuple[int, TreeNode, TreeNode]  # NameError!
```

✅ **Richtig:**
```python
from typing import TypeAlias

# String Quote für Forward Reference
TreeNode: TypeAlias = int | tuple[int, 'TreeNode', 'TreeNode']

# Oder: from __future__ import annotations
from __future__ import annotations

TreeNode: TypeAlias = int | tuple[int, TreeNode, TreeNode]
```

### ❌ Fallstrick 4: TypeAlias weglassen bei komplexen Typen

```python
# Unklar ob Variable oder Type Alias
Container = list[int] | dict[str, int]
```

✅ **Richtig:**
```python
from typing import TypeAlias

# Explizit als Type Alias markieren
Container: TypeAlias = list[int] | dict[str, int]
```

## Performance-Hinweise

### Type Aliases sind kostenlos

```python
# Type Aliases existieren nur für Type Checker
# Zur Laufzeit gibt es keinen Overhead

UserId = int
Username = str

def create_user(user_id: UserId, name: Username) -> dict:
    return {"id": user_id, "name": name}

# Identisch zu:
def create_user_no_alias(user_id: int, name: str) -> dict:
    return {"id": user_id, "name": name}

# Beide sind gleich schnell!
```

### Best Practices

**✅ Verwende Type Aliases für:**
- Komplexe, wiederverwendete Typen
- Domain-spezifische Typen (UserId, EmailAddress)
- API-Signaturen
- Konfigurationstypen
- Callback/Handler-Typen

**❌ Vermeide Type Aliases für:**
- Sehr einfache Typen die nur einmal verwendet werden
- Offensichtliche Typen (kein Alias für `list[int]` wenn klar)
- Zu viele Ebenen (max 2-3 verschachtelte Aliases)

## Übungsaufgaben

### Übung 1: Shop-System (Leicht)

Erstelle Type Aliases für einen Online-Shop:
- `ProductId`, `CategoryId`, `CustomerId`
- `Price` (float), `Quantity` (int)
- `Product`, `Order`, `Customer`

### Übung 2: File System (Mittel)

Definiere Typen für ein Dateisystem:
- `FileName`, `FilePath`, `FileSize`
- `FileMetadata`, `DirectoryContent`
- Recursive: `FileTree`

**Erwarteter Output:**
```python
tree: FileTree = {
    "name": "root",
    "children": [
        {"name": "file1.txt", "size": 100},
        {
            "name": "subdir",
            "children": [
                {"name": "file2.txt", "size": 200}
            ]
        }
    ]
}
```

### Übung 3: Game State (Mittel)

Definiere Type Aliases für ein Spiel:
- `Position`, `Health`, `Score`
- `Player`, `Enemy`, `Item`
- `GameState`, `GameAction`

### Übung 4: Workflow System (Schwer)

Erstelle ein Workflow-Type-System:
- `TaskId`, `TaskStatus`, `TaskPriority`
- `Task`, `Workflow`, `WorkflowState`
- Recursive workflow mit Steps

### Übung 5: Expression Tree (Schwer)

Definiere rekursiven Type für mathematische Ausdrücke:
```python
Expr: TypeAlias = ...
# Support: numbers, (+, -, *, /), variables
```

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [typing - Type Aliases](https://docs.python.org/3/library/typing.html#type-aliases) - Type Alias Dokumentation
- [PEP 613 - TypeAlias](https://peps.python.org/pep-0613/) - Explicit Type Aliases
- [typing - NewType](https://docs.python.org/3/library/typing.html#newtype) - NewType vs Alias

## Verwandte Themen

- [[05_Pattern_Matching/02_Type_Hints/01_Type Annotations Basics|Type Annotations Basics]] - Grundlagen
- [[05_Pattern_Matching/02_Type_Hints/02_Generics|Generics]] - Generische Type Aliases
- [[05_Pattern_Matching/02_Type_Hints/03_Union Types|Union Types]] - Union in Aliases
- [[04_Fortgeschritten/04_Dataclasses/01_Dataclass Basics|Dataclass Basics]] - Dataclasses als Types

---
← [[05_Pattern_Matching/02_Type_Hints/03_Union Types|Union Types]] | [[INDEX|📑 Index]] | [[05_Pattern_Matching/02_Type_Hints/05_Übungen Type Hints|Übungen Type Hints]] →