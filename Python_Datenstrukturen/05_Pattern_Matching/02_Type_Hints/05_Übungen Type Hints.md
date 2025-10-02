---
tags: [python, datenstrukturen, type-hints, übungen]
created: 2025-10-02
---

← [[05_Pattern_Matching/02_Type_Hints/04_Type Aliases|Type Aliases]] | [[INDEX|📑 Index]] | [[06_Praxis/01_Best_Practices/01_Idiomatisches Python|Idiomatisches Python]] →

# Übungen Type Hints

> [!tip] Lernziel
> Praktische Anwendung aller Type Hint Konzepte durch umfassende Übungsaufgaben.

## Übung 1: Grundlegende Annotationen (Leicht)

Füge Type Hints zu allen Funktionen hinzu:

```python
def add(a, b):
    return a + b

def greet(name, greeting="Hello"):
    return f"{greeting}, {name}!"

def filter_even(numbers):
    return [n for n in numbers if n % 2 == 0]

def get_first_or_none(items):
    return items[0] if items else None

def count_words(text):
    return len(text.split())
```

**Erwartetes Ergebnis:**
- Alle Funktionen haben korrekte Type Hints
- Parameter und Return-Types sind annotiert
- Optional-Typen wo nötig

---

## Übung 2: Dictionary Typen (Leicht)

Erstelle Type Hints für diese Funktionen:

```python
def create_user(name, age, email):
    return {
        "name": name,
        "age": age,
        "email": email,
        "active": True
    }

def get_user_info(user, key):
    return user.get(key)

def merge_configs(config1, config2):
    return {**config1, **config2}

def group_by_age(users):
    groups = {}
    for user in users:
        age = user["age"]
        if age not in groups:
            groups[age] = []
        groups[age].append(user)
    return groups
```

**Erwartetes Ergebnis:**
- Dict-Typen mit Key/Value-Types
- TypeAlias für wiederkehrende Strukturen

---

## Übung 3: Generic Stack (Mittel)

Implementiere einen generischen Stack mit vollständigen Type Hints:

```python
class Stack:
    def __init__(self):
        self._items = []
    
    def push(self, item):
        self._items.append(item)
    
    def pop(self):
        return self._items.pop() if self._items else None
    
    def peek(self):
        return self._items[-1] if self._items else None
    
    def is_empty(self):
        return len(self._items) == 0
    
    def size(self):
        return len(self._items)
    
    def clear(self):
        self._items.clear()
```

**Erwarteter Output:**
```python
int_stack: Stack[int] = Stack()
int_stack.push(1)
int_stack.push(2)
print(int_stack.pop())  # 2 (Type: int | None)

str_stack: Stack[str] = Stack()
str_stack.push("hello")
print(str_stack.peek())  # "hello" (Type: str | None)
```

---

## Übung 4: Result Type (Mittel)

Implementiere ein Result-Type-System mit Type Hints:

```python
@dataclass
class Ok:
    value: ...  # Generic type

@dataclass
class Err:
    error: ...  # Generic type

# Result = Ok[T] | Err[E]

def safe_divide(a, b):
    # Return Ok with result or Err with error message
    pass

def safe_parse_int(s):
    # Return Ok with int or Err with error message
    pass

def chain_results(result1, result2):
    # If both Ok, combine values
    # If any Err, return first error
    pass
```

**Erwarteter Output:**
```python
result1 = safe_divide(10, 2)
if isinstance(result1, Ok):
    print(result1.value)  # 5.0

result2 = safe_divide(10, 0)
if isinstance(result2, Err):
    print(result2.error)  # "Division by zero"
```

---

## Übung 5: Tree Operations (Mittel)

Erstelle Type Hints für einen Binärbaum:

```python
@dataclass
class Node:
    value: ...
    left: ...
    right: ...

def tree_height(node):
    """Berechnet Höhe des Baums"""
    pass

def tree_sum(node):
    """Summiert alle Werte"""
    pass

def tree_map(node, func):
    """Wendet Funktion auf alle Werte an"""
    pass

def tree_find(node, value):
    """Findet Knoten mit Wert"""
    pass
```

**Erwarteter Output:**
```python
tree: Node[int] = Node(5, Node(3, None, None), Node(7, None, None))
print(tree_height(tree))  # 2
print(tree_sum(tree))     # 15
```

---

## Übung 6: Cache System (Mittel)

Implementiere ein Cache-System mit Type Hints:

```python
class Cache:
    def __init__(self, max_size):
        self._cache = {}
        self._max_size = max_size
    
    def get(self, key):
        return self._cache.get(key)
    
    def set(self, key, value):
        if len(self._cache) >= self._max_size:
            # Remove oldest entry
            oldest = next(iter(self._cache))
            del self._cache[oldest]
        self._cache[key] = value
    
    def delete(self, key):
        if key in self._cache:
            del self._cache[key]
            return True
        return False
    
    def clear(self):
        self._cache.clear()
    
    def size(self):
        return len(self._cache)
```

**Erwarteter Output:**
```python
cache: Cache[str, int] = Cache(max_size=2)
cache.set("a", 1)
cache.set("b", 2)
print(cache.get("a"))  # 1 (Type: int | None)
```

---

## Übung 7: Event System (Schwer)

Erstelle ein typsicheres Event-System:

```python
# Event Types als Literal
EventType = ...

# Event Data
@dataclass
class Event:
    type: ...
    data: ...
    timestamp: ...

# Event Handler
EventHandler = ...

class EventBus:
    def subscribe(self, event_type, handler):
        pass
    
    def unsubscribe(self, event_type, handler):
        pass
    
    def publish(self, event):
        pass
    
    def clear(self):
        pass
```

**Erwarteter Output:**
```python
bus: EventBus = EventBus()

def on_user_created(event: Event) -> None:
    print(f"User created: {event.data['name']}")

bus.subscribe("user.created", on_user_created)

bus.publish(Event(
    type="user.created",
    data={"name": "Max"},
    timestamp=datetime.now()
))
```

---

## Übung 8: API Client (Schwer)

Implementiere einen typsicheren API-Client:

```python
# HTTP Methods
HttpMethod = ...

# Request/Response
@dataclass
class Request:
    method: ...
    url: ...
    headers: ...
    body: ...

@dataclass
class Response:
    status: ...
    headers: ...
    body: ...

class ApiClient:
    def __init__(self, base_url):
        self._base_url = base_url
    
    def request(self, method, path, body=None):
        # Create request, send, return response
        pass
    
    def get(self, path):
        pass
    
    def post(self, path, body):
        pass
    
    def put(self, path, body):
        pass
    
    def delete(self, path):
        pass
```

**Erwarteter Output:**
```python
client: ApiClient = ApiClient("https://api.example.com")
response: Response = client.get("/users/1")
if response.status == 200:
    user_data = response.body  # Type: dict[str, any]
```

---

## Übung 9: State Machine (Schwer)

Erstelle eine typsichere State Machine:

```python
# States als Enum oder Literal
State = ...

# Transitions
Transition = ...

@dataclass
class StateMachine:
    current_state: ...
    transitions: ...
    
    def can_transition(self, target_state):
        """Prüft ob Transition erlaubt"""
        pass
    
    def transition(self, target_state):
        """Führt Transition aus"""
        pass
    
    def add_transition(self, from_state, to_state):
        """Fügt erlaubte Transition hinzu"""
        pass
    
    def get_possible_transitions(self):
        """Gibt alle möglichen Transitions zurück"""
        pass
```

**Erwarteter Output:**
```python
sm: StateMachine = StateMachine(
    current_state="idle",
    transitions={
        "idle": ["running"],
        "running": ["paused", "stopped"],
        "paused": ["running", "stopped"]
    }
)

print(sm.can_transition("running"))  # True
sm.transition("running")
print(sm.current_state)  # "running"
```

---

## Übung 10: Query Builder (Schwer)

Implementiere einen typsicheren SQL Query Builder:

```python
# Operators
ComparisonOp = ...
LogicalOp = ...

@dataclass
class Condition:
    field: ...
    operator: ...
    value: ...

@dataclass
class Query:
    table: ...
    columns: ...
    conditions: ...
    limit: ...
    offset: ...
    
    def where(self, field, operator, value):
        """Fügt WHERE-Bedingung hinzu"""
        pass
    
    def and_(self, field, operator, value):
        """Fügt AND-Bedingung hinzu"""
        pass
    
    def or_(self, field, operator, value):
        """Fügt OR-Bedingung hinzu"""
        pass
    
    def limit(self, n):
        """Setzt LIMIT"""
        pass
    
    def offset(self, n):
        """Setzt OFFSET"""
        pass
    
    def build(self):
        """Baut SQL-String"""
        pass
```

**Erwarteter Output:**
```python
query = (Query("users", ["name", "email"])
    .where("active", "=", True)
    .and_("age", ">", 18)
    .limit(10)
    .build())

print(query)
# "SELECT name, email FROM users WHERE active = True AND age > 18 LIMIT 10"
```

---

## Übung 11: Repository Pattern (Sehr Schwer)

Erstelle ein generisches Repository mit Type Hints:

```python
# Entity Protocol
class HasId(Protocol):
    id: int

# Repository
class Repository:
    def __init__(self):
        self._storage = {}
        self._next_id = 1
    
    def add(self, entity):
        pass
    
    def get(self, entity_id):
        pass
    
    def get_all(self):
        pass
    
    def update(self, entity):
        pass
    
    def delete(self, entity_id):
        pass
    
    def find(self, predicate):
        pass
    
    def count(self):
        pass
```

**Erwarteter Output:**
```python
@dataclass
class User:
    id: int
    name: str
    email: str

repo: Repository[User] = Repository()
user = repo.add(User(0, "Max", "max@example.com"))
print(user.id)  # Auto-assigned ID

found = repo.get(user.id)  # Type: User | None
all_users = repo.get_all()  # Type: list[User]
```

---

## Übung 12: Async Task Queue (Sehr Schwer)

Implementiere eine typsichere async Task Queue:

```python
# Task
@dataclass
class Task:
    id: ...
    func: ...
    args: ...
    kwargs: ...
    priority: ...
    status: ...

# Task Queue
class TaskQueue:
    def __init__(self, max_workers):
        self._tasks = []
        self._max_workers = max_workers
        self._workers = []
    
    async def add_task(self, func, *args, priority=0, **kwargs):
        """Fügt Task hinzu"""
        pass
    
    async def run(self):
        """Führt Tasks aus"""
        pass
    
    def get_pending_tasks(self):
        """Gibt wartende Tasks zurück"""
        pass
    
    def get_completed_tasks(self):
        """Gibt abgeschlossene Tasks zurück"""
        pass
    
    async def wait_all(self):
        """Wartet auf alle Tasks"""
        pass
```

**Erwarteter Output:**
```python
queue: TaskQueue = TaskQueue(max_workers=3)

async def process_data(x: int) -> int:
    await asyncio.sleep(1)
    return x * 2

await queue.add_task(process_data, 5)
await queue.add_task(process_data, 10)
await queue.run()
```

---

## Übung 13: Expression Evaluator mit Types (Sehr Schwer)

Erstelle einen typsicheren Expression Evaluator:

```python
# Expression Types
@dataclass
class Num:
    value: ...

@dataclass
class Var:
    name: ...

@dataclass
class BinOp:
    op: ...
    left: ...
    right: ...

@dataclass
class UnOp:
    op: ...
    expr: ...

@dataclass
class Let:
    var: ...
    value: ...
    body: ...

Expr = ...  # Union of all expression types

# Environment
Env = ...

def evaluate(expr, env):
    """Evaluiert Expression"""
    pass

def optimize(expr):
    """Optimiert Expression (konstante Faltung)"""
    pass

def compile_to_bytecode(expr):
    """Compiliert zu Bytecode"""
    pass
```

**Erwarteter Output:**
```python
# (let x = 5 in x + 3)
expr = Let(
    var="x",
    value=Num(5),
    body=BinOp("+", Var("x"), Num(3))
)

result = evaluate(expr, {})  # 8
optimized = optimize(expr)   # Num(8)
```

---

## Übung 14: GraphQL-like Schema (Meister)

Implementiere ein typsicheres GraphQL-ähnliches Schema:

```python
# Schema Types
@dataclass
class Field:
    name: ...
    type: ...
    resolver: ...
    args: ...

@dataclass
class ObjectType:
    name: ...
    fields: ...

@dataclass
class Schema:
    types: ...
    query: ...
    mutation: ...
    
    def add_type(self, obj_type):
        pass
    
    def set_query(self, obj_type):
        pass
    
    def set_mutation(self, obj_type):
        pass
    
    def execute(self, query_string, variables=None):
        pass
```

**Erwarteter Output:**
```python
schema = Schema()

user_type = ObjectType(
    name="User",
    fields={
        "id": Field("id", int, lambda obj: obj["id"]),
        "name": Field("name", str, lambda obj: obj["name"])
    }
)

schema.add_type(user_type)
result = schema.execute("{ user(id: 1) { name } }")
```

---

## Übung 15: Type-Safe ORM (Meister)

Erstelle ein einfaches typsicheres ORM:

```python
# Column Types
@dataclass
class Column:
    name: ...
    type: ...
    primary_key: ...
    nullable: ...
    default: ...

# Model
class Model:
    __table__: ...
    __columns__: ...
    
    @classmethod
    def create_table(cls):
        pass
    
    @classmethod
    def select(cls):
        pass
    
    @classmethod
    def insert(cls, **kwargs):
        pass
    
    @classmethod
    def update(cls, **kwargs):
        pass
    
    @classmethod
    def delete(cls, **kwargs):
        pass

# Query Builder
class QueryBuilder:
    def where(self, **conditions):
        pass
    
    def order_by(self, *fields):
        pass
    
    def limit(self, n):
        pass
    
    def all(self):
        pass
    
    def first(self):
        pass
    
    def count(self):
        pass
```

**Erwarteter Output:**
```python
class User(Model):
    __table__ = "users"
    __columns__ = {
        "id": Column("id", int, primary_key=True),
        "name": Column("name", str),
        "email": Column("email", str)
    }

users = User.select().where(active=True).limit(10).all()
# Type: list[User]
```

---

## Tipps für alle Übungen

### Type Hints Best Practices:
1. **Von einfach zu komplex** - Beginne mit einfachen Typen
2. **Type Aliases** - Verwende Aliases für Wiederverwendung
3. **Generic Types** - Nutze TypeVar für Flexibilität
4. **Union Types** - Verwende Union für mehrere mögliche Typen
5. **Type Narrowing** - Nutze isinstance für Type Guards

### Testing mit mypy:
```bash
# Prüfe Type Hints mit mypy
mypy your_file.py

# Strict mode
mypy --strict your_file.py

# Ignore missing imports
mypy --ignore-missing-imports your_file.py
```

### IDE Support:
- VSCode mit Pylance
- PyCharm hat built-in Type Checking
- Andere: pyright, mypy daemon

---

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [typing - Type Hints](https://docs.python.org/3/library/typing.html) - Vollständige typing Dokumentation
- [mypy Documentation](http://mypy-lang.org/) - Type Checker
- [PEP 484 - Type Hints](https://peps.python.org/pep-0484/) - Type Hints Spezifikation

## Verwandte Themen

- [[05_Pattern_Matching/02_Type_Hints/01_Type Annotations Basics|Type Annotations Basics]] - Grundlagen
- [[05_Pattern_Matching/02_Type_Hints/02_Generics|Generics]] - Generische Typen
- [[05_Pattern_Matching/02_Type_Hints/03_Union Types|Union Types]] - Union und Optional
- [[05_Pattern_Matching/02_Type_Hints/04_Type Aliases|Type Aliases]] - Type Aliases
- [[06_Praxis/04_Lösungen/Lösungen Teil 5|Lösungen Teil 5]] - Musterlösungen

---
← [[05_Pattern_Matching/02_Type_Hints/04_Type Aliases|Type Aliases]] | [[INDEX|📑 Index]] | [[06_Praxis/01_Best_Practices/01_Idiomatisches Python|Idiomatisches Python]] →