---
tags: [python, datenstrukturen, dataclasses, field]
created: 2025-10-02
---

← [[04_Fortgeschritten/04_Dataclasses/02_Dataclass Features|Dataclass Features]] | [[INDEX|📑 Index]] | [[04_Fortgeschritten/04_Dataclasses/04_Übungen Dataclasses|Übungen Dataclasses]] →

# Field Options

> [!tip] Lernziel
> Nach dieser Seite verstehst du alle Optionen der `field()`-Funktion, kannst Felder für spezielle Zwecke konfigurieren und kennst fortgeschrittene Patterns für Feld-Management.

## Was ist `field()`?

Die `field()`-Funktion gibt dir **feinere Kontrolle** über einzelne Felder in einer Dataclass. Sie ermöglicht:
- Mutable Default-Werte
- Felder aus `__init__` ausschließen
- Felder aus `__repr__` ausschließen
- Sortierung beeinflussen
- Metadaten speichern

```python
from dataclasses import dataclass, field

@dataclass
class Example:
    # Einfaches Feld
    name: str
    
    # Feld mit field()
    items: list = field(default_factory=list)
```

## field() Parameter

### Vollständige Signatur

```python
field(
    *,
    default=MISSING,           # Default-Wert
    default_factory=MISSING,   # Funktion für Default
    init=True,                 # In __init__ einbeziehen?
    repr=True,                 # In __repr__ einbeziehen?
    hash=None,                 # In __hash__ einbeziehen?
    compare=True,              # In Vergleichen einbeziehen?
    metadata=None,             # Zusätzliche Metadaten
    kw_only=False             # Nur als Keyword-Argument? (Python 3.10+)
)
```

## `default` - Einfacher Default-Wert

Für **immutable** Default-Werte:

```python
from dataclasses import dataclass, field

@dataclass
class Config:
    # Mit field()
    host: str = field(default="localhost")
    port: int = field(default=8080)
    debug: bool = field(default=False)
    
    # Äquivalent ohne field():
    # host: str = "localhost"
    # port: int = 8080
    # debug: bool = False

config = Config()
print(config)
# Config(host='localhost', port=8080, debug=False)
```

💡 **Wann `default` mit `field()` verwenden?**
- Wenn andere field()-Optionen benötigt werden
- Für Konsistenz mit anderen Feldern

## `default_factory` - Mutable Defaults

Für **mutable** Default-Werte (Listen, Dicts, Sets, etc.):

```python
from dataclasses import dataclass, field
from typing import List, Dict

@dataclass
class TodoList:
    name: str
    # RICHTIG: Mit default_factory
    tasks: List[str] = field(default_factory=list)
    tags: List[str] = field(default_factory=list)
    metadata: Dict[str, str] = field(default_factory=dict)

# Jede Instanz bekommt eigene Listen/Dicts
todo1 = TodoList("Work")
todo1.tasks.append("Task 1")

todo2 = TodoList("Home")
print(todo2.tasks)  # [] - Nicht geteilt mit todo1!
```

### Custom Default Factories

```python
from dataclasses import dataclass, field
from datetime import datetime
from typing import List
import uuid

@dataclass
class Document:
    title: str
    # UUID als Default
    id: str = field(default_factory=lambda: str(uuid.uuid4()))
    # Aktueller Timestamp
    created_at: datetime = field(default_factory=datetime.now)
    # Leere Liste
    sections: List[str] = field(default_factory=list)
    # Dict mit Default-Werten
    settings: dict = field(default_factory=lambda: {"draft": True, "public": False})

# Jedes Document bekommt unique ID und Timestamp
doc1 = Document("Report 1")
doc2 = Document("Report 2")

print(f"Doc 1 ID: {doc1.id}")
print(f"Doc 2 ID: {doc2.id}")
print(f"IDs different: {doc1.id != doc2.id}")  # True
```

### Factory mit Parametern

```python
from dataclasses import dataclass, field
from typing import List

def create_default_list(size: int, value: any):
    """Factory-Generator"""
    return lambda: [value] * size

@dataclass
class Grid:
    rows: int
    cols: int
    # Erstelle 2D-Grid mit Defaults
    data: List[List[int]] = field(default_factory=lambda: [])
    
    def __post_init__(self):
        if not self.data:
            self.data = [[0 for _ in range(self.cols)] for _ in range(self.rows)]

grid = Grid(3, 3)
print(grid.data)
# [[0, 0, 0], [0, 0, 0], [0, 0, 0]]
```

## `init=False` - Nicht in `__init__`

Felder mit `init=False` werden **nicht** als Parameter in `__init__()` akzeptiert:

```python
from dataclasses import dataclass, field

@dataclass
class User:
    username: str
    email: str
    
    # Wird automatisch berechnet, nicht im __init__
    full_email: str = field(init=False)
    
    def __post_init__(self):
        self.full_email = f"{self.username} <{self.email}>"

# Verwendung
user = User("alice", "alice@example.com")
# user = User("alice", "alice@example.com", "Alice <...>")  # TypeError!

print(user.full_email)  # alice <alice@example.com>
```

### Berechnete Felder

```python
from dataclasses import dataclass, field
from datetime import datetime

@dataclass
class Order:
    items: list
    subtotal: float = field(init=False)
    tax: float = field(init=False)
    total: float = field(init=False)
    created_at: datetime = field(init=False)
    
    TAX_RATE = 0.19  # 19% MwSt
    
    def __post_init__(self):
        # Berechne Werte
        self.subtotal = sum(item['price'] * item['qty'] for item in self.items)
        self.tax = self.subtotal * self.TAX_RATE
        self.total = self.subtotal + self.tax
        self.created_at = datetime.now()

# Verwendung
order = Order([
    {'name': 'Laptop', 'price': 999, 'qty': 1},
    {'name': 'Mouse', 'price': 29, 'qty': 2}
])

print(f"Subtotal: €{order.subtotal:.2f}")
print(f"Tax: €{order.tax:.2f}")
print(f"Total: €{order.total:.2f}")
```

## `repr=False` - Nicht in `__repr__`

Felder mit `repr=False` erscheinen **nicht** in der String-Repräsentation:

```python
from dataclasses import dataclass, field

@dataclass
class User:
    username: str
    email: str
    # Passwort nicht in repr anzeigen (Sicherheit!)
    password_hash: str = field(repr=False)
    # API-Token ebenfalls verstecken
    api_token: str = field(repr=False)

user = User(
    "alice",
    "alice@example.com",
    "hashed_pw_12345",
    "secret_token_xyz"
)

print(user)
# User(username='alice', email='alice@example.com')
# password_hash und api_token NICHT sichtbar!

# Aber Attribute sind natürlich noch da
print(user.password_hash)  # hashed_pw_12345
```

### Große Daten ausblenden

```python
from dataclasses import dataclass, field
from typing import List

@dataclass
class Dataset:
    name: str
    description: str
    # Große Daten nicht in repr
    data: List[float] = field(repr=False)
    
    def __repr__(self):
        # Custom repr mit Daten-Statistik
        return (f"Dataset(name={self.name!r}, description={self.description!r}, "
                f"data_size={len(self.data)})")

dataset = Dataset(
    "Temperature Readings",
    "Daily temps 2024",
    list(range(1000))  # 1000 Datenpunkte
)

print(dataset)
# Dataset(name='Temperature Readings', description='Daily temps 2024', data_size=1000)
# Nicht: Dataset(..., data=[0, 1, 2, ..., 999])
```

## `compare=False` - Nicht in Vergleichen

Felder mit `compare=False` werden bei `==`, `<`, etc. **ignoriert**:

```python
from dataclasses import dataclass, field
from datetime import datetime

@dataclass(order=True)
class Task:
    priority: int
    title: str
    # Timestamps nicht für Vergleiche verwenden
    created_at: datetime = field(compare=False)
    updated_at: datetime = field(compare=False)

# Zwei Tasks mit gleicher Priorität und Titel sind gleich,
# auch wenn Timestamps unterschiedlich
task1 = Task(1, "Fix bug", datetime(2024, 1, 1), datetime(2024, 1, 2))
task2 = Task(1, "Fix bug", datetime(2024, 2, 1), datetime(2024, 2, 2))

print(task1 == task2)  # True (Timestamps werden ignoriert)

# Sortierung basiert nur auf priority und title
tasks = [
    Task(3, "Low priority", datetime.now(), datetime.now()),
    Task(1, "High priority", datetime.now(), datetime.now()),
    Task(2, "Medium priority", datetime.now(), datetime.now())
]

tasks.sort()
for task in tasks:
    print(f"{task.priority}: {task.title}")
# 1: High priority
# 2: Medium priority
# 3: Low priority
```

### Performance: Vergleiche optimieren

```python
from dataclasses import dataclass, field
from typing import List

@dataclass
class Person:
    # Nur diese für Vergleiche
    id: int
    name: str
    
    # Rest ignorieren (schnellere Vergleiche)
    address: str = field(compare=False)
    phone: str = field(compare=False)
    notes: str = field(compare=False)
    tags: List[str] = field(default_factory=list, compare=False)

# Vergleich ist schnell, da nur id und name verglichen werden
p1 = Person(1, "Alice", "Street 1", "123-456", "Some notes", ["tag1", "tag2"])
p2 = Person(1, "Alice", "Street 999", "999-999", "Different notes", [])

print(p1 == p2)  # True (nur id und name zählen)
```

## `hash=None` - Hash-Verhalten

Kontrolliert, ob Feld in `__hash__()` einbezogen wird:

```python
from dataclasses import dataclass, field

@dataclass(frozen=True)  # frozen für Hashability
class CacheKey:
    # Diese bestimmen den Hash
    user_id: int
    resource_type: str
    
    # Diese NICHT (z.B. volatile Daten)
    timestamp: float = field(hash=False)
    access_count: int = field(hash=False, default=0)

# Zwei Keys mit gleichen IDs haben gleichen Hash
key1 = CacheKey(123, "profile", 1000.0, 5)
key2 = CacheKey(123, "profile", 2000.0, 10)

print(hash(key1) == hash(key2))  # True

# Können als Dict-Keys verwendet werden
cache = {key1: "some data"}
print(cache[key2])  # "some data" - Findet Eintrag trotz anderen Timestamps
```

### Hashable mit mutable Feldern

```python
from dataclasses import dataclass, field
from typing import List

@dataclass(unsafe_hash=True)  # Hash trotz mutable Feldern
class Document:
    # Nur ID für Hash
    id: str
    
    # Mutable, nicht für Hash
    title: str = field(hash=False)
    content: str = field(hash=False)
    tags: List[str] = field(default_factory=list, hash=False)

doc1 = Document("doc-123", "Title", "Content", ["tag1"])
doc2 = Document("doc-123", "Different", "Text", [])

print(hash(doc1) == hash(doc2))  # True (gleiche ID)

# Als Dict-Key verwendbar
docs = {doc1: "version 1"}
docs[doc2] = "version 2"  # Überschreibt, da gleicher Hash!
print(len(docs))  # 1
```

⚠️ **Vorsicht:** `unsafe_hash=True` mit mutable Feldern kann zu Problemen führen wenn Hash nach Dict-Einfügung ändert!

## `metadata` - Zusätzliche Informationen

Speichert beliebige Metadaten zum Feld:

```python
from dataclasses import dataclass, field, fields

@dataclass
class User:
    username: str = field(metadata={"min_length": 3, "max_length": 20})
    email: str = field(metadata={"format": "email", "required": True})
    age: int = field(metadata={"min": 0, "max": 150, "unit": "years"})
    bio: str = field(default="", metadata={"max_length": 500, "nullable": True})

# Metadaten abrufen
for f in fields(User):
    print(f"Field: {f.name}")
    print(f"  Metadata: {f.metadata}")
    print()

# Field: username
#   Metadata: {'min_length': 3, 'max_length': 20}
# ...
```

### Validierung mit Metadaten

```python
from dataclasses import dataclass, field, fields

def validate_dataclass(instance):
    """Validiert Dataclass basierend auf Metadaten"""
    for f in fields(instance):
        value = getattr(instance, f.name)
        metadata = f.metadata
        
        # Min/Max für Zahlen
        if 'min' in metadata and value < metadata['min']:
            raise ValueError(f"{f.name} muss >= {metadata['min']} sein")
        if 'max' in metadata and value > metadata['max']:
            raise ValueError(f"{f.name} muss <= {metadata['max']} sein")
        
        # Length für Strings
        if isinstance(value, str):
            if 'min_length' in metadata and len(value) < metadata['min_length']:
                raise ValueError(f"{f.name} zu kurz (min: {metadata['min_length']})")
            if 'max_length' in metadata and len(value) > metadata['max_length']:
                raise ValueError(f"{f.name} zu lang (max: {metadata['max_length']})")

@dataclass
class Product:
    name: str = field(metadata={"min_length": 1, "max_length": 100})
    price: float = field(metadata={"min": 0.01, "max": 1000000})
    stock: int = field(metadata={"min": 0})
    
    def __post_init__(self):
        validate_dataclass(self)

# Test
try:
    product = Product("X" * 101, 99.99, 10)  # Name zu lang
except ValueError as e:
    print(f"Fehler: {e}")

try:
    product = Product("Phone", -5.00, 10)  # Negativer Preis
except ValueError as e:
    print(f"Fehler: {e}")

product = Product("Phone", 999.99, 50)  # Gültig
print(product)
```

### Metadaten für Serialization

```python
from dataclasses import dataclass, field, fields
import json

@dataclass
class ApiResponse:
    user_id: int = field(metadata={"json_name": "userId"})
    full_name: str = field(metadata={"json_name": "fullName"})
    email_address: str = field(metadata={"json_name": "emailAddress"})
    is_active: bool = field(default=True, metadata={"json_name": "isActive"})

def to_json_dict(instance) -> dict:
    """Konvertiert zu JSON-Dict mit umbenannten Keys"""
    result = {}
    for f in fields(instance):
        value = getattr(instance, f.name)
        json_name = f.metadata.get("json_name", f.name)
        result[json_name] = value
    return result

# Verwendung
response = ApiResponse(123, "Alice Smith", "alice@example.com")
json_dict = to_json_dict(response)
print(json.dumps(json_dict, indent=2))
# {
#   "userId": 123,
#   "fullName": "Alice Smith",
#   "emailAddress": "alice@example.com",
#   "isActive": true
# }
```

## `kw_only=True` - Nur Keyword-Argumente (Python 3.10+)

Ab Python 3.10: Felder als keyword-only markieren:

```python
from dataclasses import dataclass, field

@dataclass
class User:
    username: str
    email: str
    # Nur als Keyword-Argument
    admin: bool = field(default=False, kw_only=True)
    verified: bool = field(default=False, kw_only=True)

# Korrekt
user1 = User("alice", "alice@example.com", admin=True)

# Fehler
# user2 = User("bob", "bob@example.com", True)  # TypeError!
# Muss sein: User("bob", "bob@example.com", admin=True)
```

### Ganze Dataclass als kw_only

```python
from dataclasses import dataclass

@dataclass(kw_only=True)
class Config:
    host: str
    port: int
    database: str
    username: str
    password: str

# Alle Parameter müssen als Keywords übergeben werden
config = Config(
    host="localhost",
    port=5432,
    database="mydb",
    username="admin",
    password="secret"
)

# config = Config("localhost", 5432, ...)  # TypeError!
```

## Kombinierte Optionen

### Vollständiges Beispiel

```python
from dataclasses import dataclass, field, fields
from typing import List, Dict
from datetime import datetime
import json

@dataclass
class BlogPost:
    # Pflichtfelder
    title: str = field(metadata={"min_length": 1, "max_length": 200})
    content: str = field(metadata={"min_length": 10})
    author: str
    
    # Auto-generierte Felder (nicht in __init__)
    post_id: str = field(
        init=False,
        default_factory=lambda: f"post-{datetime.now().timestamp()}"
    )
    created_at: datetime = field(init=False, default_factory=datetime.now)
    updated_at: datetime = field(init=False, default_factory=datetime.now)
    
    # Optionale Felder mit Defaults
    published: bool = field(default=False)
    tags: List[str] = field(default_factory=list)
    metadata: Dict[str, str] = field(default_factory=dict, repr=False)
    
    # Stats (nicht in Vergleichen)
    view_count: int = field(default=0, compare=False)
    like_count: int = field(default=0, compare=False)
    
    def publish(self):
        """Veröffentlicht den Post"""
        self.published = True
        self.updated_at = datetime.now()
    
    def add_view(self):
        """Inkrementiert View-Count"""
        self.view_count += 1
    
    def to_dict(self) -> dict:
        """Serialisiert zu Dictionary"""
        return {
            'post_id': self.post_id,
            'title': self.title,
            'content': self.content,
            'author': self.author,
            'published': self.published,
            'tags': self.tags,
            'view_count': self.view_count,
            'like_count': self.like_count,
            'created_at': self.created_at.isoformat(),
            'updated_at': self.updated_at.isoformat()
        }

# Verwendung
post = BlogPost(
    title="Understanding Dataclasses",
    content="Dataclasses sind eine großartige Ergänzung zu Python...",
    author="Alice",
    tags=["python", "tutorial"]
)

print(post)
# BlogPost(title='Understanding Dataclasses', content='...', 
#          author='Alice', post_id='post-...', created_at=..., 
#          published=False, tags=['python', 'tutorial'], ...)

post.publish()
post.add_view()

print(json.dumps(post.to_dict(), indent=2))
```

## Fortgeschrittene Patterns

### Factory für komplexe Defaults

```python
from dataclasses import dataclass, field
from typing import Dict, List
from datetime import datetime, timedelta

def create_schedule() -> Dict[str, List[str]]:
    """Factory für leeren Wochenplan"""
    days = ["Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday"]
    return {day: [] for day in days}

def create_reminder_times() -> List[datetime]:
    """Factory für Standard-Erinnerungszeiten"""
    now = datetime.now()
    return [
        now + timedelta(hours=1),
        now + timedelta(hours=24),
        now + timedelta(days=7)
    ]

@dataclass
class TaskManager:
    user_id: int
    schedule: Dict[str, List[str]] = field(default_factory=create_schedule)
    reminders: List[datetime] = field(default_factory=create_reminder_times)
    settings: dict = field(default_factory=lambda: {
        "theme": "dark",
        "notifications": True,
        "language": "de"
    })

manager = TaskManager(123)
print(f"Schedule: {list(manager.schedule.keys())}")
print(f"Reminders: {len(manager.reminders)} set")
print(f"Settings: {manager.settings}")
```

### Conditional Fields

```python
from dataclasses import dataclass, field, fields
from typing import Optional

@dataclass
class User:
    username: str
    email: str
    role: str = "user"
    
    # Admin-spezifische Felder
    admin_level: Optional[int] = field(default=None, repr=False)
    permissions: list = field(default_factory=list, repr=False)
    
    def __post_init__(self):
        # Setze Admin-Felder basierend auf Rolle
        if self.role == "admin":
            if self.admin_level is None:
                self.admin_level = 1
            if not self.permissions:
                self.permissions = ["read", "write"]
    
    def __repr__(self):
        # Custom repr basierend auf Rolle
        base = f"User(username={self.username!r}, email={self.email!r}, role={self.role!r}"
        if self.role == "admin":
            base += f", admin_level={self.admin_level}, permissions={self.permissions}"
        return base + ")"

user1 = User("alice", "alice@example.com")
print(user1)

admin = User("bob", "bob@example.com", role="admin")
print(admin)
```

## Performance-Tipps

### `slots=True` für Speicher-Effizienz (Python 3.10+)

```python
from dataclasses import dataclass
import sys

# Normale Dataclass
@dataclass
class PersonNormal:
    name: str
    age: int
    email: str

# Mit __slots__
@dataclass(slots=True)
class PersonSlots:
    name: str
    age: int
    email: str

# Memory-Vergleich
p1 = PersonNormal("Alice", 30, "alice@example.com")
p2 = PersonSlots("Alice", 30, "alice@example.com")

print(f"Normal: {sys.getsizeof(p1)} bytes")
print(f"Slots:  {sys.getsizeof(p2)} bytes")
# Slots ist kleiner und schneller!

# __slots__ verhindert dynamische Attribute
try:
    p2.new_attr = "value"  # AttributeError!
except AttributeError as e:
    print(f"Fehler: {e}")
```

## Übungsaufgaben

### Übung 1: Validierung mit Metadata (Mittel)

Erstelle eine `Product` Dataclass mit Validierung basierend auf Metadaten.

```python
from dataclasses import dataclass, field

# Dein Code hier

# Test
product = Product("Laptop", 999.99, 10)
# product = Product("", 999.99, 10)  # ValueError: name zu kurz
# product = Product("Laptop", -10, 10)  # ValueError: price negativ
```

### Übung 2: Berechnete Felder (Mittel)

Erstelle eine `Invoice` Dataclass mit berechneten Feldern für subtotal, tax und total.

```python
from dataclasses import dataclass, field

# Dein Code hier

# Test
invoice = Invoice([
    {"name": "Item 1", "price": 100, "qty": 2},
    {"name": "Item 2", "price": 50, "qty": 1}
])
print(f"Total: ${invoice.total:.2f}")
```

### Übung 3: Custom Serialization (Schwer)

Erstelle eine Dataclass mit Metadaten-gesteuerter JSON-Serialisierung.

```python
from dataclasses import dataclass, field

# Dein Code hier

# Test
user = User(123, "Alice Smith", "alice@example.com")
json_output = user.to_json()
# Sollte "userId", "fullName", "emailAddress" als Keys haben
```

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [dataclasses.field()](https://docs.python.org/3/library/dataclasses.html#dataclasses.field) - Vollständige field() Dokumentation
- [dataclasses.fields()](https://docs.python.org/3/library/dataclasses.html#dataclasses.fields) - Felder inspizieren
- [Field objects](https://docs.python.org/3/library/dataclasses.html#dataclasses.Field) - Field-Klasse Details

## Verwandte Themen

- [[04_Fortgeschritten/04_Dataclasses/01_Dataclass Basics|Dataclass Basics]] - Grundlagen
- [[04_Fortgeschritten/04_Dataclasses/02_Dataclass Features|Dataclass Features]] - Erweiterte Features
- [[04_Fortgeschritten/04_Dataclasses/04_Übungen Dataclasses|Übungen Dataclasses]] - Praktische Übungen
- [[05_Pattern_Matching/02_Type_Hints/01_Type Annotations Basics|Type Annotations]] - Type Hints

---
← [[04_Fortgeschritten/04_Dataclasses/02_Dataclass Features|Dataclass Features]] | [[INDEX|📑 Index]] | [[04_Fortgeschritten/04_Dataclasses/04_Übungen Dataclasses|Übungen Dataclasses]] →