---
tags: [python, datenstrukturen, pattern-matching, python312]
created: 2025-10-02
---

← [[04_Fortgeschritten/05_Enums/03_Übungen Enums|Übungen Enums]] | [[INDEX|📑 Index]] | [[05_Pattern_Matching/01_Grundlagen/02_Pattern Syntax|Pattern Syntax]] →

# Match Statement

> [!tip] Lernziel
> Du verstehst das match-Statement (strukturelles Pattern Matching) aus Python 3.10+ und kannst es für eleganten, lesbaren Code verwenden.

## Was ist Pattern Matching?

**Pattern Matching** ist ein mächtiges Feature aus Python 3.10+, das es ermöglicht, Werte gegen Muster zu prüfen und dabei gleichzeitig zu destrukturieren. Es ist viel mehr als ein verbessertes `if-elif-else` - es kann Strukturen analysieren und Werte extrahieren.

**Traditioneller Ansatz (vor Python 3.10):**
```python
def handle_command(command):
    if isinstance(command, tuple) and len(command) == 2:
        if command[0] == "move":
            x, y = command[1], command[1]
            print(f"Move to {x}, {y}")
        elif command[0] == "jump":
            height = command[1]
            print(f"Jump {height}m")
    elif command == "stop":
        print("Stop")
    else:
        print("Unknown command")
```

**Mit Pattern Matching (Python 3.10+):**
```python
def handle_command(command):
    match command:
        case ("move", x, y):
            print(f"Move to {x}, {y}")
        case ("jump", height):
            print(f"Jump {height}m")
        case "stop":
            print("Stop")
        case _:
            print("Unknown command")
```

## Syntax und Grundlagen

### Basis-Syntax

```python
# Grundstruktur
match value:
    case pattern1:
        # Code wenn pattern1 matcht
    case pattern2:
        # Code wenn pattern2 matcht
    case _:
        # Default case (wie else)

# Einfaches Beispiel
def describe_number(n):
    match n:
        case 0:
            return "Zero"
        case 1:
            return "One"
        case 2:
            return "Two"
        case _:
            return "Many"

print(describe_number(0))   # "Zero"
print(describe_number(5))   # "Many"
```

### Literal Patterns

```python
def handle_status(status):
    match status:
        case 200:
            print("OK")
        case 404:
            print("Not Found")
        case 500:
            print("Server Error")
        case _:
            print(f"Status: {status}")

handle_status(200)  # OK
handle_status(403)  # Status: 403

# String Literals
def parse_command(cmd):
    match cmd:
        case "start":
            print("Starting...")
        case "stop":
            print("Stopping...")
        case "restart":
            print("Restarting...")
        case _:
            print("Unknown command")
```

### Capture Patterns (Variablen binden)

```python
def describe_point(point):
    match point:
        case (0, 0):
            print("Origin")
        case (0, y):  # y wird gebunden
            print(f"On Y-axis at {y}")
        case (x, 0):  # x wird gebunden
            print(f"On X-axis at {x}")
        case (x, y):  # beide werden gebunden
            print(f"Point at ({x}, {y})")

describe_point((0, 0))    # Origin
describe_point((0, 5))    # On Y-axis at 5
describe_point((3, 0))    # On X-axis at 3
describe_point((2, 4))    # Point at (2, 4)
```

### Wildcard Pattern (_)

```python
def categorize_tuple(t):
    match t:
        case (1, _):  # Erstes Element ist 1, zweites egal
            print("Starts with 1")
        case (_, 2):  # Zweites Element ist 2, erstes egal
            print("Ends with 2")
        case (_, _, _):  # Genau 3 Elemente
            print("Triple")
        case _:  # Alles andere (wie else)
            print("Other")

categorize_tuple((1, 99))     # Starts with 1
categorize_tuple((99, 2))     # Ends with 2
categorize_tuple((1, 2, 3))   # Triple
categorize_tuple((1, 2, 3, 4))  # Other
```

## Details und Interna

### Sequence Patterns

```python
# Listen und Tupel matchen
def analyze_list(items):
    match items:
        case []:
            print("Empty list")
        case [x]:
            print(f"Single item: {x}")
        case [x, y]:
            print(f"Two items: {x}, {y}")
        case [first, *rest]:  # * für "rest" wie bei unpacking
            print(f"First: {first}, Rest: {rest}")

analyze_list([])           # Empty list
analyze_list([1])          # Single item: 1
analyze_list([1, 2])       # Two items: 1, 2
analyze_list([1, 2, 3, 4]) # First: 1, Rest: [2, 3, 4]

# Spezifische Werte + Capture
def process_command(cmd):
    match cmd:
        case ["load", filename]:
            print(f"Loading {filename}")
        case ["save", filename]:
            print(f"Saving {filename}")
        case ["delete", *files]:  # Mehrere Dateien löschen
            print(f"Deleting {len(files)} files")
        case _:
            print("Invalid command")
```

### Mapping Patterns (Dictionaries)

```python
def handle_event(event):
    match event:
        case {"type": "click", "x": x, "y": y}:
            print(f"Click at ({x}, {y})")
        case {"type": "keypress", "key": key}:
            print(f"Key pressed: {key}")
        case {"type": "scroll", "delta": delta}:
            print(f"Scrolled by {delta}")
        case _:
            print("Unknown event")

handle_event({"type": "click", "x": 10, "y": 20})
# Click at (10, 20)

handle_event({"type": "keypress", "key": "Enter"})
# Key pressed: Enter

# Zusätzliche Keys werden ignoriert
handle_event({"type": "click", "x": 5, "y": 8, "button": "left"})
# Click at (5, 8)
```

### OR Patterns (|)

```python
def describe_http_status(code):
    match code:
        case 200 | 201 | 204:  # Mehrere Werte
            print("Success")
        case 400 | 401 | 403 | 404:
            print("Client Error")
        case 500 | 502 | 503:
            print("Server Error")
        case _:
            print("Other status")

describe_http_status(200)  # Success
describe_http_status(404)  # Client Error
describe_http_status(502)  # Server Error

# Mit Capture
def process_value(val):
    match val:
        case 0 | 1 | 2 as small_num:  # Bindet zu small_num
            print(f"Small number: {small_num}")
        case _:
            print("Larger number")
```

### Guards (if-Bedingung)

```python
def categorize_number(n):
    match n:
        case x if x < 0:
            print("Negative")
        case x if x == 0:
            print("Zero")
        case x if x < 10:
            print("Small positive")
        case x:
            print("Large positive")

categorize_number(-5)   # Negative
categorize_number(0)    # Zero
categorize_number(5)    # Small positive
categorize_number(100)  # Large positive

# Guard mit Pattern
def validate_point(point):
    match point:
        case (x, y) if x >= 0 and y >= 0:
            print(f"Point in first quadrant: ({x}, {y})")
        case (x, y) if x < 0 and y >= 0:
            print(f"Point in second quadrant: ({x}, {y})")
        case (x, y):
            print(f"Point in other quadrant: ({x}, {y})")

validate_point((5, 3))    # First quadrant
validate_point((-2, 4))   # Second quadrant
```

### Class Patterns

```python
from dataclasses import dataclass

@dataclass
class Point:
    x: int
    y: int

@dataclass
class Circle:
    center: Point
    radius: int

@dataclass
class Rectangle:
    top_left: Point
    width: int
    height: int

def describe_shape(shape):
    match shape:
        case Point(x=0, y=0):
            print("Origin point")
        case Point(x=0, y=y):
            print(f"Point on Y-axis at {y}")
        case Point(x=x, y=0):
            print(f"Point on X-axis at {x}")
        case Point(x=x, y=y):
            print(f"Point at ({x}, {y})")
        case Circle(center=Point(x=0, y=0), radius=r):
            print(f"Circle at origin with radius {r}")
        case Circle(center=c, radius=r):
            print(f"Circle at {c} with radius {r}")
        case Rectangle(width=w, height=h) if w == h:
            print(f"Square with side {w}")
        case Rectangle(width=w, height=h):
            print(f"Rectangle {w}x{h}")

describe_shape(Point(0, 0))           # Origin point
describe_shape(Circle(Point(0, 0), 5)) # Circle at origin with radius 5
describe_shape(Rectangle(Point(0, 0), 10, 10))  # Square with side 10
```

## Praktische Anwendungen

### Anwendungsfall 1: JSON-API-Response-Handler

```python
def handle_api_response(response):
    """Verarbeitet verschiedene API-Response-Formate"""
    match response:
        case {"status": "success", "data": data}:
            print(f"✅ Success: {data}")
            return data
        
        case {"status": "error", "message": msg, "code": code}:
            print(f"❌ Error {code}: {msg}")
            return None
        
        case {"status": "error", "message": msg}:
            print(f"❌ Error: {msg}")
            return None
        
        case {"status": "loading", "progress": progress}:
            print(f"⏳ Loading... {progress}%")
            return None
        
        case {"status": status}:
            print(f"⚠️ Unknown status: {status}")
            return None
        
        case _:
            print("⚠️ Invalid response format")
            return None

# Verwendung
handle_api_response({"status": "success", "data": {"id": 1, "name": "Test"}})
# ✅ Success: {'id': 1, 'name': 'Test'}

handle_api_response({"status": "error", "message": "Not found", "code": 404})
# ❌ Error 404: Not found

handle_api_response({"status": "loading", "progress": 75})
# ⏳ Loading... 75%
```

### Anwendungsfall 2: Command Pattern Parser

```python
def execute_command(command):
    """Führt verschiedene Befehle aus"""
    match command.split():
        case ["quit" | "exit"]:
            print("Exiting...")
            return "exit"
        
        case ["help"]:
            print("Available commands: help, quit, add, list, delete")
            return "help"
        
        case ["add", *items]:
            print(f"Adding items: {', '.join(items)}")
            return "add"
        
        case ["delete", item]:
            print(f"Deleting: {item}")
            return "delete"
        
        case ["list"]:
            print("Listing all items...")
            return "list"
        
        case ["config", "set", key, value]:
            print(f"Setting {key} = {value}")
            return "config_set"
        
        case ["config", "get", key]:
            print(f"Getting {key}")
            return "config_get"
        
        case _:
            print("Unknown command. Type 'help' for available commands.")
            return "unknown"

# Verwendung
execute_command("add item1 item2 item3")  # Adding items: item1, item2, item3
execute_command("config set timeout 30")  # Setting timeout = 30
execute_command("quit")                   # Exiting...
```

### Anwendungsfall 3: Expression Evaluator

```python
from dataclasses import dataclass
from typing import Union

@dataclass
class Num:
    value: float

@dataclass
class Add:
    left: 'Expr'
    right: 'Expr'

@dataclass
class Mul:
    left: 'Expr'
    right: 'Expr'

@dataclass
class Neg:
    expr: 'Expr'

Expr = Union[Num, Add, Mul, Neg]

def evaluate(expr: Expr) -> float:
    """Evaluiert mathematische Ausdrücke"""
    match expr:
        case Num(value=v):
            return v
        
        case Add(left=l, right=r):
            return evaluate(l) + evaluate(r)
        
        case Mul(left=l, right=r):
            return evaluate(l) * evaluate(r)
        
        case Neg(expr=e):
            return -evaluate(e)
        
        case _:
            raise ValueError(f"Invalid expression: {expr}")

# Verwendung: (2 + 3) * 4
expr = Mul(
    left=Add(left=Num(2), right=Num(3)),
    right=Num(4)
)
result = evaluate(expr)
print(result)  # 20.0

# Verwendung: -(5 + 3)
expr2 = Neg(expr=Add(left=Num(5), right=Num(3)))
result2 = evaluate(expr2)
print(result2)  # -8.0
```

### Anwendungsfall 4: State Machine mit Pattern Matching

```python
from enum import Enum, auto
from dataclasses import dataclass

class State(Enum):
    IDLE = auto()
    RUNNING = auto()
    PAUSED = auto()
    FINISHED = auto()

@dataclass
class Event:
    type: str
    data: dict = None

class StateMachine:
    def __init__(self):
        self.state = State.IDLE
    
    def handle_event(self, event: Event):
        """Verarbeitet Events basierend auf aktuellem State"""
        match (self.state, event.type):
            case (State.IDLE, "start"):
                print("Starting...")
                self.state = State.RUNNING
            
            case (State.RUNNING, "pause"):
                print("Pausing...")
                self.state = State.PAUSED
            
            case (State.PAUSED, "resume"):
                print("Resuming...")
                self.state = State.RUNNING
            
            case (State.RUNNING, "finish"):
                print("Finishing...")
                self.state = State.FINISHED
            
            case (State.FINISHED, "reset"):
                print("Resetting...")
                self.state = State.IDLE
            
            case (state, event_type):
                print(f"⚠️ Invalid transition: {state} + {event_type}")

# Verwendung
sm = StateMachine()
sm.handle_event(Event("start"))   # Starting...
sm.handle_event(Event("pause"))   # Pausing...
sm.handle_event(Event("resume"))  # Resuming...
sm.handle_event(Event("finish"))  # Finishing...
sm.handle_event(Event("start"))   # ⚠️ Invalid transition
```

## Häufige Fallstricke

### ❌ Fallstrick 1: Reihenfolge der Cases wichtig

```python
# FALSCH - Spezifischerer Case nach allgemeinerem
def bad_categorize(value):
    match value:
        case x:  # Matcht ALLES
            print("Any value")
        case 0:  # Wird NIE erreicht!
            print("Zero")

bad_categorize(0)  # "Any value" - NICHT "Zero"!
```

✅ **Richtig:**
```python
def good_categorize(value):
    match value:
        case 0:  # Spezifisch zuerst
            print("Zero")
        case x:  # Allgemein zuletzt
            print("Any value")

good_categorize(0)  # "Zero"
```

### ❌ Fallstrick 2: OR-Pattern mit Capture

```python
# FALSCH - Kann nicht beide binden
def bad_or_pattern(value):
    match value:
        case 1 | 2 as x | 3 as y:  # SyntaxError!
            print(f"{x} or {y}")
```

✅ **Richtig:**
```python
def good_or_pattern(value):
    match value:
        case 1 | 2 | 3 as num:  # Alle zu num binden
            print(f"Number: {num}")

good_or_pattern(2)  # "Number: 2"
```

### ❌ Fallstrick 3: Mutable Default-Pattern vergessen

```python
# FALSCH - Kein Default-Case
def bad_match(value):
    match value:
        case 1:
            print("One")
        case 2:
            print("Two")
    # Falls value = 3: Nichts passiert!

bad_match(3)  # Keine Ausgabe
```

✅ **Richtig:**
```python
def good_match(value):
    match value:
        case 1:
            print("One")
        case 2:
            print("Two")
        case _:  # Default-Case
            print("Other")

good_match(3)  # "Other"
```

### ❌ Fallstrick 4: Dictionary-Pattern erwartet exakte Keys

```python
# Missverständnis: Dictionary muss nur die gematchten Keys haben
def process_user(user):
    match user:
        case {"name": name}:
            print(f"User: {name}")

# Funktioniert - zusätzliche Keys werden ignoriert
process_user({"name": "Max", "age": 30, "city": "Berlin"})
# Output: User: Max

# Das ist richtig so! Pattern prüft nur auf vorhandene Keys.
```

### ❌ Fallstrick 5: Class Pattern ohne Keyword-Arguments

```python
from dataclasses import dataclass

@dataclass
class Point:
    x: int
    y: int

# FALSCH - Positional patterns bei dataclass
def bad_match(p):
    match p:
        case Point(0, 0):  # SyntaxError bei dataclass!
            print("Origin")
```

✅ **Richtig:**
```python
from dataclasses import dataclass

@dataclass
class Point:
    x: int
    y: int

def good_match(p):
    match p:
        case Point(x=0, y=0):  # Keyword-Arguments verwenden
            print("Origin")
        case Point(x=x, y=y):
            print(f"Point at ({x}, {y})")

good_match(Point(0, 0))  # Origin
```

## Performance-Hinweise

### Zeitkomplexität

```python
# Match-Statement ist O(1) für jeden Case
# Aber: Reihenfolge zählt!

# Ineffizient bei vielen Cases
def inefficient(value):
    match value:
        case 1: return "one"
        case 2: return "two"
        # ... 1000 weitere Cases
        case 1000: return "thousand"  # O(n) worst case

# Besser: Dictionary für viele einfache Mappings
def efficient(value):
    mapping = {i: f"number_{i}" for i in range(1, 1001)}
    return mapping.get(value, "unknown")  # O(1) average
```

### Wann Pattern Matching verwenden

**✅ Gut geeignet für:**
- Strukturiertes Datenmatching (JSON, AST)
- Wenige Cases (<10-20)
- Komplexe Bedingungen mit Destrukturierung
- State Machines
- Command/Event-Handler

**❌ Nicht optimal für:**
- Einfache if-elif mit nur Wert-Vergleichen
- Sehr viele Cases (>50) - Dictionary besser
- Performance-kritischer Code mit nur einer Bedingung
- Wenn normale if-else lesbarer ist

## Übungsaufgaben

### Übung 1: Fibonacci mit Pattern Matching (Leicht)

Implementiere die Fibonacci-Funktion mit match-Statement.

**Erwarteter Output:**
```python
print(fib(0))   # 0
print(fib(1))   # 1
print(fib(5))   # 5
print(fib(10))  # 55
```

### Übung 2: Calculator Parser (Mittel)

Erstelle einen einfachen Calculator, der Strings wie "5 + 3", "10 * 2", "8 - 4" parst und auswertet.

**Erwarteter Output:**
```python
print(calculate("5 + 3"))    # 8
print(calculate("10 * 2"))   # 20
print(calculate("8 - 4"))    # 4
print(calculate("invalid"))  # Error: Invalid expression
```

### Übung 3: JSON Validator (Mittel)

Schreibe eine Funktion die JSON-Objekte validiert:
- User muss "name" (str) und "age" (int) haben
- Optional: "email" (str), "active" (bool)

**Erwarteter Output:**
```python
validate_user({"name": "Max", "age": 30})  # Valid
validate_user({"name": "Anna", "age": 25, "email": "a@b.com"})  # Valid
validate_user({"name": "Bob"})  # Invalid: missing age
validate_user({"age": 30})  # Invalid: missing name
```

### Übung 4: Tree Traversal (Schwer)

Implementiere Pre-Order Traversal für einen Binärbaum mit Pattern Matching.

**Erwarteter Output:**
```python
tree = Node(1, Node(2, Leaf(4), Leaf(5)), Node(3))
print(traverse(tree))  # [1, 2, 4, 5, 3]
```

### Übung 5: Chess Move Validator (Schwer)

Validiere Schachzüge basierend auf Figurentyp mit Pattern Matching.

**Erwarteter Output:**
```python
is_valid_move("pawn", (1,1), (1,2))    # True (1 forward)
is_valid_move("pawn", (1,1), (1,3))    # False (too far)
is_valid_move("knight", (0,0), (1,2))  # True (L-shape)
is_valid_move("bishop", (0,0), (2,2))  # True (diagonal)
```

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [PEP 634 - Structural Pattern Matching](https://peps.python.org/pep-0634/) - Spezifikation
- [PEP 636 - Pattern Matching Tutorial](https://peps.python.org/pep-0636/) - Offizielles Tutorial
- [match Statement Documentation](https://docs.python.org/3/reference/compound_stmts.html#the-match-statement) - Referenz

## Verwandte Themen

- [[05_Pattern_Matching/01_Grundlagen/02_Pattern Syntax|Pattern Syntax]] - Detaillierte Pattern-Arten
- [[05_Pattern_Matching/01_Grundlagen/03_Übungen Pattern Matching|Übungen Pattern Matching]] - Mehr Übungen
- [[04_Fortgeschritten/05_Enums/01_Enum Basics|Enum Basics]] - Enums mit Pattern Matching
- [[04_Fortgeschritten/04_Dataclasses/01_Dataclass Basics|Dataclass Basics]] - Dataclasses matchen

---
← [[04_Fortgeschritten/05_Enums/03_Übungen Enums|Übungen Enums]] | [[INDEX|📑 Index]] | [[05_Pattern_Matching/01_Grundlagen/02_Pattern Syntax|Pattern Syntax]] →