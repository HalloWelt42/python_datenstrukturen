---
tags: [python, datenstrukturen, type-hints, union]
created: 2025-10-02
---

← [[05_Pattern_Matching/02_Type_Hints/02_Generics|Generics]] | [[INDEX|📑 Index]] | [[05_Pattern_Matching/02_Type_Hints/04_Type Aliases|Type Aliases]] →

# Union Types

> [!tip] Lernziel
> Du verstehst Union Types, Optional, und kannst mit mehreren möglichen Typen elegant arbeiten.

## Was sind Union Types?

**Union Types** ermöglichen es, anzugeben, dass ein Wert einen von mehreren Typen haben kann. Dies ist nützlich, wenn Funktionen flexibel verschiedene Input-Typen akzeptieren oder verschiedene Output-Typen zurückgeben können.

**Problem ohne Union:**
```python
# Schlecht - verliert Typ-Information
def process(value: Any) -> Any:
    if isinstance(value, int):
        return value * 2
    elif isinstance(value, str):
        return value.upper()
    return value
```

**Mit Union:**
```python
def process(value: int | str) -> int | str:
    if isinstance(value, int):
        return value * 2
    elif isinstance(value, str):
        return value.upper()
    return value  # Type-Checker warnt hier!
```

## Syntax und Grundlagen

### Union mit | (Python 3.10+)

```python
# Moderne Syntax mit | Operator
def divide(a: int | float, b: int | float) -> float | str:
    """Dividiert zwei Zahlen oder gibt Fehler zurück"""
    if b == 0:
        return "Error: Division by zero"
    return a / b

print(divide(10, 2))    # 5.0
print(divide(10, 0))    # "Error: Division by zero"
print(divide(7.5, 2.5)) # 3.0

# Mehrere Typen
def format_value(value: int | float | str | bool) -> str:
    return f"Value: {value}"

print(format_value(42))       # "Value: 42"
print(format_value(3.14))     # "Value: 3.14"
print(format_value("text"))   # "Value: text"
print(format_value(True))     # "Value: True"
```

### Union aus typing (Python 3.5+)

```python
from typing import Union

# Alte Syntax (vor Python 3.10)
def divide_old(a: Union[int, float], b: Union[int, float]) -> Union[float, str]:
    if b == 0:
        return "Error: Division by zero"
    return a / b

# Äquivalent zur | Syntax
# Union[int, float] == int | float
```

### Optional - Union mit None

```python
# Optional[T] ist Kurzform für T | None
from typing import Optional

def find_user(user_id: int) -> Optional[str]:
    """Gibt Username oder None zurück"""
    users = {1: "Max", 2: "Anna", 3: "Bob"}
    return users.get(user_id)

# Moderne Syntax (Python 3.10+)
def find_user_new(user_id: int) -> str | None:
    users = {1: "Max", 2: "Anna", 3: "Bob"}
    return users.get(user_id)

# Verwendung
username = find_user(1)
if username is not None:
    print(f"Found: {username}")
else:
    print("User not found")
```

### Type Narrowing mit isinstance

```python
def process_value(value: int | str | list[int]) -> str:
    """Type Narrowing mit isinstance"""
    
    if isinstance(value, int):
        # Hier weiß Type-Checker: value ist int
        return f"Number: {value}, Squared: {value ** 2}"
    
    elif isinstance(value, str):
        # Hier weiß Type-Checker: value ist str
        return f"Text: {value.upper()}"
    
    else:
        # Hier weiß Type-Checker: value ist list[int]
        return f"List sum: {sum(value)}"

print(process_value(5))           # "Number: 5, Squared: 25"
print(process_value("hello"))     # "Text: HELLO"
print(process_value([1, 2, 3]))   # "List sum: 6"
```

## Details und Interna

### Union Order Matters (für Menschen)

```python
# Reihenfolge ist für Type-Checker egal, aber für Lesbarkeit wichtig
def parse_input(value: str | int | float) -> float:
    """Konvertiert zu float"""
    return float(value)

# Besser: Häufigste/Einfachste Typen zuerst
def parse_input_better(value: int | float | str) -> float:
    return float(value)

# Type-Checker behandelt beide gleich:
# int | float | str == str | int | float
```

### Union vs. Any

```python
from typing import Any

# Any erlaubt ALLES - keine Typ-Prüfung
def process_any(value: Any) -> Any:
    return value.upper()  # Kein Fehler, auch wenn value int ist!

# Union ist spezifisch - Type-Checker hilft
def process_union(value: int | str) -> str:
    # return value.upper()  # Type-Checker Error bei int!
    if isinstance(value, str):
        return value.upper()
    return str(value)

# Union ist fast immer besser als Any!
```

### Union mit Collections

```python
# Union in Collections
def process_items(items: list[int | str]) -> list[str]:
    """Liste kann ints oder strings enthalten"""
    return [str(item) for item in items]

print(process_items([1, "two", 3, "four"]))  # ['1', 'two', '3', 'four']

# Dictionary mit verschiedenen Value-Types
def get_config() -> dict[str, int | str | bool]:
    return {
        "port": 8080,
        "host": "localhost",
        "debug": True
    }

config = get_config()
print(config["port"])   # 8080 (Type: int | str | bool)
```

### Type Guards

```python
from typing import TypeGuard

def is_string_list(value: list[int | str]) -> TypeGuard[list[str]]:
    """Type Guard: prüft ob alle Elemente strings sind"""
    return all(isinstance(item, str) for item in value)

def process_list(items: list[int | str]) -> None:
    if is_string_list(items):
        # Type-Checker weiß: items ist list[str]
        for item in items:
            print(item.upper())  # OK - item ist str
    else:
        print("Mixed or int list")

process_list(["a", "b", "c"])  # A B C
process_list([1, 2, 3])        # Mixed or int list
process_list([1, "a"])         # Mixed or int list
```

### Literal Union

```python
from typing import Literal

# Union von spezifischen Werten
Status = Literal["pending", "approved", "rejected"]

def update_status(status: Status) -> str:
    """Nur diese 3 Werte erlaubt"""
    return f"Status: {status}"

update_status("pending")    # OK
update_status("approved")   # OK
# update_status("invalid") # Type-Checker Error!

# Kombiniert mit Union
def process_response(response: int | Literal["timeout", "error"]) -> str:
    if isinstance(response, int):
        return f"Status code: {response}"
    return f"Error: {response}"

print(process_response(200))        # "Status code: 200"
print(process_response("timeout"))  # "Error: timeout"
```

## Praktische Anwendungen

### Anwendungsfall 1: API Response Handler

```python
from dataclasses import dataclass
from typing import Literal

@dataclass
class SuccessResponse:
    status: Literal["success"]
    data: dict[str, any]

@dataclass
class ErrorResponse:
    status: Literal["error"]
    message: str
    code: int

Response = SuccessResponse | ErrorResponse

def handle_api_call(url: str) -> Response:
    """Simuliert API-Call"""
    # Simulation
    if "invalid" in url:
        return ErrorResponse("error", "Invalid URL", 400)
    return SuccessResponse("success", {"id": 1, "name": "Test"})

def process_response(response: Response) -> None:
    """Verarbeitet Response mit Type Narrowing"""
    
    if isinstance(response, SuccessResponse):
        # Type-Checker weiß: response ist SuccessResponse
        print(f"✅ Success: {response.data}")
    
    elif isinstance(response, ErrorResponse):
        # Type-Checker weiß: response ist ErrorResponse
        print(f"❌ Error {response.code}: {response.message}")

# Verwendung
response1 = handle_api_call("https://api.example.com/users")
process_response(response1)  # ✅ Success: {'id': 1, 'name': 'Test'}

response2 = handle_api_call("https://invalid")
process_response(response2)  # ❌ Error 400: Invalid URL
```

### Anwendungsfall 2: Flexible Parser

```python
from datetime import datetime
from typing import overload

def parse_value(value: str) -> str | int | float | bool | None:
    """Parst String zu passendem Typ"""
    
    # None
    if value.lower() == "none" or value == "":
        return None
    
    # Boolean
    if value.lower() in ("true", "false"):
        return value.lower() == "true"
    
    # Integer
    try:
        return int(value)
    except ValueError:
        pass
    
    # Float
    try:
        return float(value)
    except ValueError:
        pass
    
    # String
    return value

# Verwendung
print(parse_value("42"))        # 42 (int)
print(parse_value("3.14"))      # 3.14 (float)
print(parse_value("true"))      # True (bool)
print(parse_value("hello"))     # "hello" (str)
print(parse_value("none"))      # None

# Mit Type Narrowing
result = parse_value("123")
if isinstance(result, int):
    print(f"Integer: {result * 2}")
elif isinstance(result, str):
    print(f"String: {result.upper()}")
```

### Anwendungsfall 3: Result Type mit Union

```python
from dataclasses import dataclass
from typing import Generic, TypeVar

T = TypeVar('T')
E = TypeVar('E')

@dataclass
class Ok(Generic[T]):
    value: T

@dataclass
class Err(Generic[E]):
    error: E

Result = Ok[T] | Err[E]

def safe_divide(a: float, b: float) -> Result[float, str]:
    """Division mit Fehlerbehandlung"""
    if b == 0:
        return Err("Division by zero")
    return Ok(a / b)

def safe_parse_int(s: str) -> Result[int, str]:
    """String zu Int mit Fehlerbehandlung"""
    try:
        return Ok(int(s))
    except ValueError:
        return Err(f"Cannot parse '{s}' as integer")

# Verwendung mit Pattern Matching
def handle_result(result: Result[float, str]) -> None:
    match result:
        case Ok(value):
            print(f"✅ Success: {value}")
        case Err(error):
            print(f"❌ Error: {error}")

handle_result(safe_divide(10, 2))   # ✅ Success: 5.0
handle_result(safe_divide(10, 0))   # ❌ Error: Division by zero

# Chaining
result1 = safe_parse_int("42")
if isinstance(result1, Ok):
    result2 = safe_divide(result1.value, 2)
    handle_result(result2)  # ✅ Success: 21.0
```

### Anwendungsfall 4: Event System

```python
from dataclasses import dataclass
from typing import Literal

@dataclass
class ClickEvent:
    type: Literal["click"]
    x: int
    y: int
    button: str

@dataclass
class KeyEvent:
    type: Literal["keypress"]
    key: str
    modifiers: list[str]

@dataclass
class ScrollEvent:
    type: Literal["scroll"]
    delta: int
    direction: Literal["up", "down"]

Event = ClickEvent | KeyEvent | ScrollEvent

def handle_event(event: Event) -> None:
    """Event-Handler mit Type Narrowing"""
    
    match event:
        case ClickEvent(x=x, y=y, button=button):
            print(f"Click at ({x}, {y}) with {button}")
        
        case KeyEvent(key=key, modifiers=mods):
            mods_str = "+".join(mods) if mods else ""
            print(f"Key: {mods_str}{key}")
        
        case ScrollEvent(delta=delta, direction=direction):
            print(f"Scroll {direction} by {delta}")

# Verwendung
handle_event(ClickEvent("click", 100, 200, "left"))
# Click at (100, 200) with left

handle_event(KeyEvent("keypress", "A", ["ctrl", "shift"]))
# Key: ctrl+shift+A

handle_event(ScrollEvent("scroll", 3, "down"))
# Scroll down by 3
```

## Häufige Fallstricke

### ❌ Fallstrick 1: None vergessen

```python
# FALSCH - vergisst None als möglichen Rückgabewert
def find_item(items: list[int], target: int) -> int:
    for item in items:
        if item == target:
            return item
    # Was wenn nicht gefunden? Implizites None!
```

✅ **Richtig:**
```python
def find_item(items: list[int], target: int) -> int | None:
    for item in items:
        if item == target:
            return item
    return None

# Oder explizit prüfen
result = find_item([1, 2, 3], 5)
if result is not None:
    print(f"Found: {result}")
```

### ❌ Fallstrick 2: Type Narrowing vergessen

```python
def process(value: int | str) -> int:
    # FALSCH - Type-Checker weiß nicht ob int oder str
    # return value * 2  # Error wenn str!
    pass
```

✅ **Richtig:**
```python
def process(value: int | str) -> int:
    if isinstance(value, int):
        return value * 2
    else:  # value ist jetzt str
        return len(value)
```

### ❌ Fallstrick 3: bool und int verwechseln

```python
# PROBLEM: bool ist Subtyp von int!
def check_value(value: int | bool) -> str:
    if isinstance(value, int):
        # bool ist auch int - wird hier matched!
        return "integer"
    return "boolean"

print(check_value(True))   # "integer" - Überraschung!
```

✅ **Richtig:**
```python
def check_value(value: int | bool) -> str:
    # bool ZUERST prüfen
    if isinstance(value, bool):
        return "boolean"
    elif isinstance(value, int):
        return "integer"
    return "unknown"

print(check_value(True))   # "boolean" ✓
print(check_value(42))     # "integer" ✓
```

### ❌ Fallstrick 4: Union Order bei Overloads

```python
from typing import overload

# FALSCH - zu allgemeine Signatur zuerst
@overload
def process(value: int | str) -> str: ...
@overload
def process(value: int) -> int: ...  # Wird nie matched!

def process(value: int | str) -> int | str:
    if isinstance(value, int):
        return value * 2
    return value.upper()
```

✅ **Richtig:**
```python
from typing import overload

# Spezifisch zu allgemein
@overload
def process(value: int) -> int: ...
@overload
def process(value: str) -> str: ...

def process(value: int | str) -> int | str:
    if isinstance(value, int):
        return value * 2
    return value.upper()
```

## Performance-Hinweise

### Type Checking ist statisch

```python
# Union Types haben KEINEN Runtime-Overhead
def process(value: int | str) -> str:
    return str(value)

# Ist genauso schnell wie:
def process_untyped(value):
    return str(value)

# Type Hints werden zur Laufzeit ignoriert
```

### Wann Union verwenden

**✅ Verwende Union für:**
- Funktionen die mehrere Input-Typen akzeptieren
- Rückgabewerte die verschiedene Typen haben können
- Optional/None Werte
- Fehlerbehandlung ohne Exceptions
- API-Responses mit verschiedenen Strukturen

**❌ Vermeide Union für:**
- Zu viele Typen (>5) - evtl. Refactoring nötig
- Wenn `Any` wirklich nötig ist
- Wenn gemeinsames Interface möglich ist
- Zu komplexe Type Narrowing Logik

## Übungsaufgaben

### Übung 1: Safe Math Operations (Leicht)

Schreibe Funktionen mit Union Returns:
- `safe_sqrt(x: float) -> float | str` - Fehler bei negativen Zahlen
- `safe_log(x: float) -> float | str` - Fehler bei x <= 0
- `safe_factorial(n: int) -> int | str` - Fehler bei n < 0

**Erwarteter Output:**
```python
print(safe_sqrt(16))    # 4.0
print(safe_sqrt(-1))    # "Error: negative input"
print(safe_factorial(5)) # 120
```

### Übung 2: Type Validator (Leicht)

Schreibe `validate_input(value: str) -> int | float | bool | str`:
- Versuche zu int zu parsen
- Dann zu float
- Dann zu bool ("true"/"false")
- Sonst string zurückgeben

**Erwarteter Output:**
```python
print(validate_input("42"))      # 42 (int)
print(validate_input("3.14"))    # 3.14 (float)
print(validate_input("true"))    # True (bool)
print(validate_input("hello"))   # "hello" (str)
```

### Übung 3: Command Parser (Mittel)

Parse Commands als Union Types:
```python
@dataclass
class Move:
    x: int
    y: int

@dataclass
class Attack:
    target: str

@dataclass
class Heal:
    amount: int

Command = Move | Attack | Heal
```

Schreibe `parse_command(cmd: str) -> Command | None`

**Erwarteter Output:**
```python
parse_command("move 10 20")     # Move(10, 20)
parse_command("attack enemy")   # Attack("enemy")
parse_command("heal 50")        # Heal(50)
parse_command("invalid")        # None
```

### Übung 4: JSON Schema Validator (Schwer)

Implementiere Validator für JSON-Schema:
- `validate_json(data: dict, schema: dict) -> dict | list[str]`
- Return: data wenn valid, sonst Liste von Fehlern

### Übung 5: Expression Evaluator (Schwer)

Erstelle Expression-Evaluator mit Union:
```python
Expr = int | float | tuple[str, Expr, Expr]
```

Unterstütze: `("+", a, b)`, `("-", a, b)`, `("*", a, b)`, `("/", a, b)`

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [typing - Union](https://docs.python.org/3/library/typing.html#typing.Union) - Union Type
- [typing - Optional](https://docs.python.org/3/library/typing.html#typing.Optional) - Optional Type
- [PEP 604 - Union Operator](https://peps.python.org/pep-0604/) - | Operator für Union
- [PEP 647 - TypeGuard](https://peps.python.org/pep-0647/) - User-Defined Type Guards

## Verwandte Themen

- [[05_Pattern_Matching/02_Type_Hints/01_Type Annotations Basics|Type Annotations Basics]] - Grundlagen
- [[05_Pattern_Matching/02_Type_Hints/02_Generics|Generics]] - Generische Typen
- [[05_Pattern_Matching/02_Type_Hints/04_Type Aliases|Type Aliases]] - Type Aliases
- [[05_Pattern_Matching/01_Grundlagen/01_Match Statement|Match Statement]] - Pattern Matching mit Union

---
← [[05_Pattern_Matching/02_Type_Hints/02_Generics|Generics]] | [[INDEX|📑 Index]] | [[05_Pattern_Matching/02_Type_Hints/04_Type Aliases|Type Aliases]] →