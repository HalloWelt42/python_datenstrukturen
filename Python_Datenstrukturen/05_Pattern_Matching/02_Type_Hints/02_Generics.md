---
tags: [python, datenstrukturen, type-hints, generics]
created: 2025-10-02
---

← [[05_Pattern_Matching/02_Type_Hints/01_Type Annotations Basics|Type Annotations Basics]] | [[INDEX|📑 Index]] | [[05_Pattern_Matching/02_Type_Hints/03_Union Types|Union Types]] →

# Generics

> [!tip] Lernziel
> Du verstehst generische Typen (Generics), kannst sie verwenden und eigene generische Funktionen/Klassen erstellen.

## Was sind Generics?

**Generics** erlauben es, Typen zu parametrisieren - d.h. Funktionen und Klassen können mit verschiedenen Typen arbeiten, während die Typsicherheit erhalten bleibt.

**Problem ohne Generics:**
```python
# Ohne Generics - verliert Typ-Information
def first_element(items: list) -> Any:
    return items[0] if items else None

numbers = [1, 2, 3]
result = first_element(numbers)  # Type: Any (nicht int!)
# result.bit_length()  # IDE weiß nicht, dass es int ist
```

**Mit Generics:**
```python
from typing import TypeVar, List

T = TypeVar('T')  # Generischer Typ-Parameter

def first_element(items: List[T]) -> T | None:
    return items[0] if items else None

numbers = [1, 2, 3]
result = first_element(numbers)  # Type: int | None ✓
if result is not None:
    print(result.bit_length())  # IDE weiß: result ist int
```

## Syntax und Grundlagen

### TypeVar - Generische Typ-Variablen

```python
from typing import TypeVar, List

# Einfacher TypeVar
T = TypeVar('T')  # Kann beliebiger Typ sein

def identity(x: T) -> T:
    """Gibt Eingabe unverändert zurück"""
    return x

# Type bleibt erhalten
num = identity(42)        # Type: int
text = identity("hello")  # Type: str
items = identity([1, 2])  # Type: list[int]

# Mehrere TypeVars
T = TypeVar('T')
U = TypeVar('U')

def pair(first: T, second: U) -> tuple[T, U]:
    """Erstellt Tupel aus zwei Werten"""
    return (first, second)

result = pair(42, "hello")  # Type: tuple[int, str]
print(result)  # (42, 'hello')
```

### TypeVar mit Constraints

```python
from typing import TypeVar

# Eingeschränkt auf bestimmte Typen
NumberType = TypeVar('NumberType', int, float)

def add_numbers(a: NumberType, b: NumberType) -> NumberType:
    """Addiert zwei Zahlen (int oder float)"""
    return a + b

print(add_numbers(5, 3))        # 8 (int)
print(add_numbers(2.5, 3.7))    # 6.2 (float)
# add_numbers("a", "b")  # Type-Checker Error!

# Mit bound - muss Subtyp sein
from numbers import Number

NumericType = TypeVar('NumericType', bound=Number)

def multiply(a: NumericType, b: NumericType) -> NumericType:
    return a * b
```

### Generic Collections

```python
from typing import TypeVar, Generic

T = TypeVar('T')

class Stack(Generic[T]):
    """Generischer Stack für beliebige Typen"""
    
    def __init__(self) -> None:
        self._items: list[T] = []
    
    def push(self, item: T) -> None:
        """Fügt Element hinzu"""
        self._items.append(item)
    
    def pop(self) -> T | None:
        """Entfernt und gibt letztes Element zurück"""
        return self._items.pop() if self._items else None
    
    def peek(self) -> T | None:
        """Schaut oberstes Element an"""
        return self._items[-1] if self._items else None
    
    def is_empty(self) -> bool:
        """Prüft ob Stack leer ist"""
        return len(self._items) == 0
    
    def size(self) -> int:
        """Gibt Anzahl Elemente zurück"""
        return len(self._items)

# Verwendung mit verschiedenen Typen
int_stack: Stack[int] = Stack()
int_stack.push(1)
int_stack.push(2)
print(int_stack.pop())  # 2 (Type: int | None)

str_stack: Stack[str] = Stack()
str_stack.push("hello")
str_stack.push("world")
print(str_stack.pop())  # "world" (Type: str | None)
```

### Generic Functions

```python
from typing import TypeVar, Sequence

T = TypeVar('T')

def first(seq: Sequence[T]) -> T | None:
    """Gibt erstes Element oder None zurück"""
    return seq[0] if seq else None

def last(seq: Sequence[T]) -> T | None:
    """Gibt letztes Element oder None zurück"""
    return seq[-1] if seq else None

def swap_pairs(items: list[T]) -> list[T]:
    """Vertauscht Paare: [1,2,3,4] -> [2,1,4,3]"""
    result: list[T] = []
    for i in range(0, len(items) - 1, 2):
        result.append(items[i + 1])
        result.append(items[i])
    if len(items) % 2 == 1:
        result.append(items[-1])
    return result

# Verwendung
print(first([1, 2, 3]))        # 1 (Type: int | None)
print(last(["a", "b", "c"]))   # "c" (Type: str | None)
print(swap_pairs([1, 2, 3, 4]))  # [2, 1, 4, 3]
```

## Details und Interna

### Mehrere Type Parameters

```python
from typing import TypeVar, Generic

K = TypeVar('K')  # Key type
V = TypeVar('V')  # Value type

class Cache(Generic[K, V]):
    """Generischer Cache mit Key-Value-Paaren"""
    
    def __init__(self) -> None:
        self._data: dict[K, V] = {}
    
    def set(self, key: K, value: V) -> None:
        """Speichert Wert"""
        self._data[key] = value
    
    def get(self, key: K) -> V | None:
        """Lädt Wert"""
        return self._data.get(key)
    
    def delete(self, key: K) -> bool:
        """Löscht Wert, gibt True bei Erfolg"""
        if key in self._data:
            del self._data[key]
            return True
        return False
    
    def clear(self) -> None:
        """Leert Cache"""
        self._data.clear()

# Int-Keys, String-Values
cache: Cache[int, str] = Cache()
cache.set(1, "one")
cache.set(2, "two")
print(cache.get(1))  # "one" (Type: str | None)

# String-Keys, List-Values
list_cache: Cache[str, list[int]] = Cache()
list_cache.set("numbers", [1, 2, 3])
print(list_cache.get("numbers"))  # [1, 2, 3]
```

### Protocol - Structural Subtyping

```python
from typing import Protocol, TypeVar

class Comparable(Protocol):
    """Protocol für vergleichbare Typen"""
    def __lt__(self, other: 'Comparable') -> bool: ...
    def __gt__(self, other: 'Comparable') -> bool: ...

T = TypeVar('T', bound=Comparable)

def find_max(items: list[T]) -> T | None:
    """Findet Maximum in Liste"""
    if not items:
        return None
    
    max_item = items[0]
    for item in items[1:]:
        if item > max_item:
            max_item = item
    return max_item

# Funktioniert mit allen vergleichbaren Typen
print(find_max([1, 5, 3, 9, 2]))      # 9
print(find_max(["apple", "zebra"]))   # "zebra"
print(find_max([3.14, 2.71, 1.41]))   # 3.14
```

### Generic mit Vererbung

```python
from typing import TypeVar, Generic

T = TypeVar('T')

class Container(Generic[T]):
    """Basis-Container"""
    
    def __init__(self) -> None:
        self._items: list[T] = []
    
    def add(self, item: T) -> None:
        self._items.append(item)
    
    def get_all(self) -> list[T]:
        return self._items.copy()

class SortedContainer(Container[T]):
    """Container der Elemente sortiert hält"""
    
    def add(self, item: T) -> None:
        """Fügt Element sortiert ein"""
        self._items.append(item)
        self._items.sort()  # type: ignore
    
    def get_min(self) -> T | None:
        return self._items[0] if self._items else None
    
    def get_max(self) -> T | None:
        return self._items[-1] if self._items else None

# Verwendung
sorted_nums = SortedContainer[int]()
sorted_nums.add(5)
sorted_nums.add(2)
sorted_nums.add(8)
sorted_nums.add(1)
print(sorted_nums.get_all())  # [1, 2, 5, 8]
print(sorted_nums.get_min())  # 1
print(sorted_nums.get_max())  # 8
```

### Callable Generics

```python
from typing import TypeVar, Callable

T = TypeVar('T')
R = TypeVar('R')

def map_list(items: list[T], func: Callable[[T], R]) -> list[R]:
    """Wendet Funktion auf jedes Element an"""
    return [func(item) for item in items]

# int -> str
numbers = [1, 2, 3, 4, 5]
strings = map_list(numbers, str)
print(strings)  # ['1', '2', '3', '4', '5']

# str -> int
words = ["1", "2", "3"]
nums = map_list(words, int)
print(nums)  # [1, 2, 3]

# str -> bool
names = ["Alice", "Bob", "Charlie"]
long_names = map_list(names, lambda x: len(x) > 4)
print(long_names)  # [True, False, True]
```

## Praktische Anwendungen

### Anwendungsfall 1: Repository Pattern

```python
from typing import TypeVar, Generic, Protocol
from dataclasses import dataclass

class HasId(Protocol):
    """Protocol für Entities mit ID"""
    id: int

T = TypeVar('T', bound=HasId)

class Repository(Generic[T]):
    """Generisches Repository für Datenzugriff"""
    
    def __init__(self) -> None:
        self._storage: dict[int, T] = {}
        self._next_id = 1
    
    def add(self, entity: T) -> T:
        """Fügt Entity hinzu"""
        if entity.id == 0:
            entity.id = self._next_id
            self._next_id += 1
        self._storage[entity.id] = entity
        return entity
    
    def get(self, entity_id: int) -> T | None:
        """Lädt Entity by ID"""
        return self._storage.get(entity_id)
    
    def get_all(self) -> list[T]:
        """Lädt alle Entities"""
        return list(self._storage.values())
    
    def delete(self, entity_id: int) -> bool:
        """Löscht Entity"""
        if entity_id in self._storage:
            del self._storage[entity_id]
            return True
        return False
    
    def update(self, entity: T) -> bool:
        """Aktualisiert Entity"""
        if entity.id in self._storage:
            self._storage[entity.id] = entity
            return True
        return False

@dataclass
class User:
    id: int
    name: str
    email: str

@dataclass
class Product:
    id: int
    name: str
    price: float

# Verwendung
user_repo: Repository[User] = Repository()
user1 = user_repo.add(User(0, "Max", "max@example.com"))
user2 = user_repo.add(User(0, "Anna", "anna@example.com"))

product_repo: Repository[Product] = Repository()
product1 = product_repo.add(Product(0, "Laptop", 999.99))

print(user_repo.get(1))      # User(id=1, name='Max', ...)
print(len(user_repo.get_all()))  # 2
```

### Anwendungsfall 2: Result Type

```python
from typing import TypeVar, Generic
from dataclasses import dataclass

T = TypeVar('T')
E = TypeVar('E')

@dataclass
class Ok(Generic[T]):
    """Erfolgreicher Result"""
    value: T
    
    def is_ok(self) -> bool:
        return True
    
    def is_err(self) -> bool:
        return False
    
    def unwrap(self) -> T:
        return self.value

@dataclass
class Err(Generic[E]):
    """Fehlgeschlagener Result"""
    error: E
    
    def is_ok(self) -> bool:
        return False
    
    def is_err(self) -> bool:
        return True
    
    def unwrap(self) -> None:
        raise ValueError(f"Called unwrap on Err: {self.error}")

Result = Ok[T] | Err[E]

def divide(a: float, b: float) -> Result[float, str]:
    """Division mit Result-Type"""
    if b == 0:
        return Err("Division by zero")
    return Ok(a / b)

def parse_int(s: str) -> Result[int, str]:
    """Parse String zu Int"""
    try:
        return Ok(int(s))
    except ValueError:
        return Err(f"Cannot parse '{s}' as integer")

# Verwendung
result1 = divide(10, 2)
if result1.is_ok():
    print(f"Result: {result1.unwrap()}")  # Result: 5.0

result2 = divide(10, 0)
if result2.is_err():
    print(f"Error: {result2.error}")  # Error: Division by zero

result3 = parse_int("42")
print(result3.is_ok())  # True
print(result3.unwrap())  # 42

result4 = parse_int("abc")
print(result4.is_err())  # True
```

### Anwendungsfall 3: Tree Structure

```python
from typing import TypeVar, Generic
from dataclasses import dataclass

T = TypeVar('T')

@dataclass
class TreeNode(Generic[T]):
    """Generischer Baum-Knoten"""
    value: T
    children: list['TreeNode[T]']
    
    def __init__(self, value: T):
        self.value = value
        self.children = []
    
    def add_child(self, child: 'TreeNode[T]') -> None:
        """Fügt Kind hinzu"""
        self.children.append(child)
    
    def traverse(self) -> list[T]:
        """Traversiert Baum und sammelt alle Werte"""
        result = [self.value]
        for child in self.children:
            result.extend(child.traverse())
        return result
    
    def find(self, value: T) -> 'TreeNode[T] | None':
        """Findet Knoten mit Wert"""
        if self.value == value:
            return self
        for child in self.children:
            found = child.find(value)
            if found is not None:
                return found
        return None
    
    def height(self) -> int:
        """Berechnet Höhe des Baums"""
        if not self.children:
            return 1
        return 1 + max(child.height() for child in self.children)

# String-Tree
root: TreeNode[str] = TreeNode("root")
child1 = TreeNode("child1")
child2 = TreeNode("child2")
grandchild1 = TreeNode("grandchild1")

root.add_child(child1)
root.add_child(child2)
child1.add_child(grandchild1)

print(root.traverse())  # ['root', 'child1', 'grandchild1', 'child2']
print(root.height())    # 3

# Int-Tree
num_tree: TreeNode[int] = TreeNode(1)
num_tree.add_child(TreeNode(2))
num_tree.add_child(TreeNode(3))
print(num_tree.traverse())  # [1, 2, 3]
```

### Anwendungsfall 4: Builder Pattern

```python
from typing import TypeVar, Generic
from dataclasses import dataclass, field

T = TypeVar('T')

class Builder(Generic[T]):
    """Generischer Builder"""
    
    def __init__(self, constructor: type[T]):
        self._constructor = constructor
        self._data: dict[str, any] = {}
    
    def set(self, key: str, value: any) -> 'Builder[T]':
        """Setzt Attribut"""
        self._data[key] = value
        return self  # Für Chaining
    
    def build(self) -> T:
        """Erstellt Objekt"""
        return self._constructor(**self._data)

@dataclass
class User:
    name: str
    email: str
    age: int = 0
    active: bool = True

@dataclass
class Product:
    name: str
    price: float
    description: str = ""
    stock: int = 0

# Verwendung
user = (Builder(User)
    .set("name", "Max")
    .set("email", "max@example.com")
    .set("age", 30)
    .build())

print(user)  # User(name='Max', email='max@example.com', age=30, active=True)

product = (Builder(Product)
    .set("name", "Laptop")
    .set("price", 999.99)
    .set("stock", 5)
    .build())

print(product)  # Product(name='Laptop', price=999.99, ...)
```

## Häufige Fallstricke

### ❌ Fallstrick 1: TypeVar ohne Verwendung

```python
from typing import TypeVar

T = TypeVar('T')

# FALSCH - T wird nicht verwendet
def bad_function(items: list) -> list[T]:  # T nicht im Input!
    return items
```

✅ **Richtig:**
```python
from typing import TypeVar

T = TypeVar('T')

def good_function(items: list[T]) -> list[T]:
    return items
```

### ❌ Fallstrick 2: Covariance/Contravariance verwechseln

```python
from typing import TypeVar, Generic

T = TypeVar('T')

class Container(Generic[T]):
    def __init__(self, value: T):
        self.value = value

# NICHT automatisch kompatibel!
int_container: Container[int] = Container(42)
# num_container: Container[float] = int_container  # Type Error!
```

✅ **Richtig - Covariant TypeVar:**
```python
from typing import TypeVar, Generic

T_co = TypeVar('T_co', covariant=True)

class Container(Generic[T_co]):
    def __init__(self, value: T_co):
        self._value = value
    
    def get(self) -> T_co:  # Nur lesend
        return self._value
```

### ❌ Fallstrick 3: Generic zur Laufzeit prüfen

```python
from typing import TypeVar, Generic

T = TypeVar('T')

class Box(Generic[T]):
    def __init__(self, value: T):
        self.value = value

# FALSCH - Generics sind zur Laufzeit gelöscht
box = Box[int](42)  # SyntaxError!
```

✅ **Richtig:**
```python
from typing import TypeVar, Generic

T = TypeVar('T')

class Box(Generic[T]):
    def __init__(self, value: T):
        self.value = value

box: Box[int] = Box(42)  # Type annotation
```

## Performance-Hinweise

### Generics haben keinen Runtime-Overhead

```python
# Generics werden zur Laufzeit gelöscht (Type Erasure)
# Kein Performance-Unterschied zwischen:

def generic_function(items: list[int]) -> list[int]:
    return items

def non_generic_function(items: list) -> list:
    return items

# Beide sind gleich schnell!
```

### Wann Generics verwenden

**✅ Verwende Generics für:**
- Container-Klassen (Stack, Queue, Cache)
- Wiederverwendbare Algorithmen
- Repository/DAO-Patterns
- Builder/Factory-Patterns
- Wenn Typ-Information erhalten bleiben soll

**❌ Vermeide Generics für:**
- Sehr einfache Funktionen
- Wenn `Any` ausreicht
- Single-Use Code
- Wenn Komplexität nicht gerechtfertigt ist

## Übungsaufgaben

### Übung 1: Generic Queue (Leicht)

Implementiere eine generische Queue mit:
- `enqueue(item: T)` - Fügt hinzu
- `dequeue() -> T | None` - Entfernt erstes
- `peek() -> T | None` - Schaut erstes an
- `is_empty() -> bool`

**Erwarteter Output:**
```python
q: Queue[int] = Queue()
q.enqueue(1)
q.enqueue(2)
print(q.dequeue())  # 1
print(q.peek())     # 2
```

### Übung 2: Pair Type (Leicht)

Erstelle eine generische `Pair[T, U]` Klasse mit:
- `first: T` und `second: U`
- `swap() -> Pair[U, T]` - Vertauscht

**Erwarteter Output:**
```python
p = Pair(42, "hello")
print(p.first, p.second)  # 42 hello
swapped = p.swap()
print(swapped.first, swapped.second)  # hello 42
```

### Übung 3: Generic Filter (Mittel)

Schreibe `filter_by_predicate`:
- Parameter: `list[T]`, `Callable[[T], bool]`
- Return: `list[T]`

**Erwarteter Output:**
```python
nums = [1, 2, 3, 4, 5]
evens = filter_by_predicate(nums, lambda x: x % 2 == 0)
print(evens)  # [2, 4]
```

### Übung 4: Generic Tree Sum (Schwer)

Erstelle `sum_tree` für `TreeNode[T]`:
- Summiert alle Werte im Baum
- T muss addierbar sein

### Übung 5: Option Type (Schwer)

Implementiere `Option[T]` ähnlich Rust:
- `Some(value: T)` - Hat Wert
- `Nothing` - Kein Wert
- Methoden: `is_some()`, `is_none()`, `unwrap()`, `map()`

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [typing - Generics](https://docs.python.org/3/library/typing.html#generics) - Generic Types
- [PEP 484 - Type Hints](https://peps.python.org/pep-0484/) - Type Hints
- [PEP 544 - Protocols](https://peps.python.org/pep-0544/) - Structural Subtyping

## Verwandte Themen

- [[05_Pattern_Matching/02_Type_Hints/01_Type Annotations Basics|Type Annotations Basics]] - Grundlagen
- [[05_Pattern_Matching/02_Type_Hints/03_Union Types|Union Types]] - Union und Optional
- [[05_Pattern_Matching/02_Type_Hints/04_Type Aliases|Type Aliases]] - Type Aliases
- [[04_Fortgeschritten/04_Dataclasses/01_Dataclass Basics|Dataclass Basics]] - Dataclasses mit Generics

---
← [[05_Pattern_Matching/02_Type_Hints/01_Type Annotations Basics|Type Annotations Basics]] | [[INDEX|📑 Index]] | [[05_Pattern_Matching/02_Type_Hints/03_Union Types|Union Types]]