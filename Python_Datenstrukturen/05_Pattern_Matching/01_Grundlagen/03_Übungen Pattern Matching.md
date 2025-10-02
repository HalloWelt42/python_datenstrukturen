---
tags: [python, datenstrukturen, pattern-matching, übungen]
created: 2025-10-02
---

← [[05_Pattern_Matching/01_Grundlagen/02_Pattern Syntax|Pattern Syntax]] | [[INDEX|📑 Index]] | [[05_Pattern_Matching/02_Type_Hints/01_Type Annotations Basics|Type Annotations Basics]] →

# Übungen Pattern Matching

> [!tip] Lernziel
> Praktische Anwendung von Pattern Matching durch umfassende Übungsaufgaben.

## Übung 1: Rechner (Leicht)

Implementiere einen einfachen Rechner der arithmetische Ausdrücke als Tupel verarbeitet:
- `("add", a, b)` → a + b
- `("sub", a, b)` → a - b
- `("mul", a, b)` → a * b
- `("div", a, b)` → a / b (mit Division-by-Zero Prüfung)

**Erwarteter Output:**
```python
print(calculate(("add", 5, 3)))      # 8
print(calculate(("mul", 4, 7)))      # 28
print(calculate(("div", 10, 2)))     # 5.0
print(calculate(("div", 10, 0)))     # "Error: Division by zero"
print(calculate(("unknown", 1, 2)))  # "Error: Unknown operation"
```

---

## Übung 2: FizzBuzz mit Pattern Matching (Leicht)

Implementiere FizzBuzz mit match-Statement:
- Vielfache von 3 und 5 → "FizzBuzz"
- Vielfache von 3 → "Fizz"
- Vielfache von 5 → "Buzz"
- Sonst → Zahl selbst

**Erwarteter Output:**
```python
for i in range(1, 16):
    print(fizzbuzz(i))
# 1, 2, Fizz, 4, Buzz, Fizz, 7, 8, Fizz, Buzz, 11, Fizz, 13, 14, FizzBuzz
```

---

## Übung 3: Liste Zerteilen (Leicht)

Erstelle Funktionen mit Pattern Matching:
- `get_first(lst)` - erstes Element oder None
- `get_last(lst)` - letztes Element oder None
- `get_middle(lst)` - mittlere Elemente ohne erstes/letztes
- `split_at(lst, n)` - teilt Liste an Position n

**Erwarteter Output:**
```python
print(get_first([1, 2, 3]))       # 1
print(get_first([]))              # None
print(get_last([1, 2, 3]))        # 3
print(get_middle([1, 2, 3, 4]))   # [2, 3]
print(split_at([1,2,3,4,5], 2))   # ([1, 2], [3, 4, 5])
```

---

## Übung 4: HTTP Request Parser (Mittel)

Parse HTTP-Request-Dictionaries:
```python
{"method": "GET", "path": "/users"}
{"method": "GET", "path": "/users/123"}
{"method": "POST", "path": "/users", "body": {"name": "Max"}}
{"method": "PUT", "path": "/users/123", "body": {...}}
{"method": "DELETE", "path": "/users/123"}
```

Erstelle eine Funktion die den Request-Typ zurückgibt.

**Erwarteter Output:**
```python
parse({"method": "GET", "path": "/users"})
# "List all users"

parse({"method": "GET", "path": "/users/123"})
# "Get user 123"

parse({"method": "POST", "path": "/users", "body": {"name": "Max"}})
# "Create user: Max"

parse({"method": "DELETE", "path": "/users/123"})
# "Delete user 123"
```

---

## Übung 5: Koordinaten Klassifizierung (Mittel)

Klassifiziere 2D-Punkte:
- Ursprung (0, 0)
- Auf X-Achse (x, 0)
- Auf Y-Achse (0, y)
- Im ersten Quadranten (x>0, y>0)
- Im zweiten Quadranten (x<0, y>0)
- Im dritten Quadranten (x<0, y<0)
- Im vierten Quadranten (x>0, y<0)

**Erwarteter Output:**
```python
print(classify_point((0, 0)))      # "Origin"
print(classify_point((5, 0)))      # "X-axis (positive)"
print(classify_point((3, 4)))      # "Quadrant I"
print(classify_point((-2, 5)))     # "Quadrant II"
print(classify_point((-3, -4)))    # "Quadrant III"
print(classify_point((2, -3)))     # "Quadrant IV"
```

---

## Übung 6: JSON Validator (Mittel)

Validiere JSON-Strukturen für verschiedene Entitäten:

**User:** `{"type": "user", "name": str, "email": str}`
**Product:** `{"type": "product", "id": int, "price": float}`
**Order:** `{"type": "order", "items": list, "total": float}`

**Erwarteter Output:**
```python
validate({"type": "user", "name": "Max", "email": "max@example.com"})
# "Valid user"

validate({"type": "product", "id": 123, "price": 29.99})
# "Valid product"

validate({"type": "user", "name": "Max"})
# "Invalid: missing email"

validate({"type": "unknown"})
# "Invalid: unknown type"
```

---

## Übung 7: Command Parser (Mittel)

Parse Shell-artige Befehle aus Listen:
```python
["cd", "directory"]
["ls"]
["ls", "-l"]
["mkdir", "folder1", "folder2", ...]
["rm", "-rf", "file"]
["help"]
```

**Erwarteter Output:**
```python
parse_command(["cd", "documents"])
# "Change directory to: documents"

parse_command(["ls", "-l"])
# "List files (long format)"

parse_command(["mkdir", "dir1", "dir2", "dir3"])
# "Create directories: dir1, dir2, dir3"

parse_command(["rm", "-rf", "file.txt"])
# "Remove (force, recursive): file.txt"
```

---

## Übung 8: Binary Tree Operations (Schwer)

Implementiere verschiedene Operationen auf Binärbäumen:

```python
from dataclasses import dataclass

@dataclass
class Empty:
    pass

@dataclass
class Leaf:
    value: int

@dataclass
class Node:
    value: int
    left: 'Tree'
    right: 'Tree'

Tree = Empty | Leaf | Node
```

Implementiere:
- `tree_size(tree)` - Anzahl Knoten
- `tree_height(tree)` - Höhe des Baums
- `tree_sum(tree)` - Summe aller Werte
- `tree_contains(tree, value)` - Prüft ob Wert vorhanden
- `tree_max(tree)` - Maximum-Wert

**Erwarteter Output:**
```python
tree = Node(
    value=5,
    left=Node(value=3, left=Leaf(1), right=Leaf(4)),
    right=Leaf(7)
)

print(tree_size(tree))           # 5
print(tree_height(tree))         # 3
print(tree_sum(tree))            # 20
print(tree_contains(tree, 4))    # True
print(tree_contains(tree, 10))   # False
print(tree_max(tree))            # 7
```

---

## Übung 9: Expression Evaluator (Schwer)

Implementiere einen Ausdrucks-Evaluator:

```python
@dataclass
class Num:
    value: float

@dataclass
class BinOp:
    op: str  # "+", "-", "*", "/"
    left: 'Expr'
    right: 'Expr'

@dataclass
class UnOp:
    op: str  # "-", "abs"
    expr: 'Expr'

Expr = Num | BinOp | UnOp
```

Implementiere `evaluate(expr)` mit Pattern Matching.

**Erwarteter Output:**
```python
# (2 + 3) * 4
expr1 = BinOp("*", BinOp("+", Num(2), Num(3)), Num(4))
print(evaluate(expr1))  # 20.0

# -(5 - 3)
expr2 = UnOp("-", BinOp("-", Num(5), Num(3)))
print(evaluate(expr2))  # -2.0

# abs(-10 + 3)
expr3 = UnOp("abs", BinOp("+", Num(-10), Num(3)))
print(evaluate(expr3))  # 7.0
```

---

## Übung 10: State Machine (Schwer)

Implementiere eine State Machine für einen Aufzug:

```python
from enum import Enum, auto

class Floor(Enum):
    GROUND = 0
    FIRST = 1
    SECOND = 2
    THIRD = 3

class State(Enum):
    IDLE = auto()
    MOVING_UP = auto()
    MOVING_DOWN = auto()
    DOOR_OPEN = auto()

@dataclass
class ElevatorState:
    current_floor: Floor
    state: State
    target_floor: Floor | None = None
```

Implementiere `handle_event(elevator, event)` mit Events:
- `("call", floor)` - Ruft Aufzug zu Floor
- `("select", floor)` - Wählt Ziel-Floor
- `("door_open")` - Öffnet Tür
- `("door_close")` - Schließt Tür

**Erwarteter Output:**
```python
elevator = ElevatorState(Floor.GROUND, State.IDLE)

elevator = handle_event(elevator, ("select", Floor.SECOND))
# ElevatorState(Floor.GROUND, State.MOVING_UP, Floor.SECOND)

elevator = handle_event(elevator, ("arrived",))
# ElevatorState(Floor.SECOND, State.DOOR_OPEN, None)

elevator = handle_event(elevator, ("door_close",))
# ElevatorState(Floor.SECOND, State.IDLE, None)
```

---

## Übung 11: Chess Move Validator (Schwer)

Validiere Schachzüge mit Pattern Matching:

```python
from enum import Enum

class Piece(Enum):
    PAWN = "pawn"
    KNIGHT = "knight"
    BISHOP = "bishop"
    ROOK = "rook"
    QUEEN = "queen"
    KING = "king"

def is_valid_move(piece: Piece, from_pos: tuple, to_pos: tuple) -> bool:
    # Implementiere mit Pattern Matching
    pass
```

**Erwarteter Output:**
```python
# Bauer: 1 oder 2 Felder vorwärts von Startposition
print(is_valid_move(Piece.PAWN, (1, 1), (1, 2)))     # True
print(is_valid_move(Piece.PAWN, (1, 1), (1, 3)))     # True (Startpos)
print(is_valid_move(Piece.PAWN, (1, 2), (1, 4)))     # False

# Springer: L-Form
print(is_valid_move(Piece.KNIGHT, (0, 0), (1, 2)))   # True
print(is_valid_move(Piece.KNIGHT, (0, 0), (2, 1)))   # True
print(is_valid_move(Piece.KNIGHT, (0, 0), (2, 2)))   # False

# Läufer: Diagonal
print(is_valid_move(Piece.BISHOP, (0, 0), (3, 3)))   # True
print(is_valid_move(Piece.BISHOP, (0, 0), (3, 2)))   # False

# Turm: Gerade
print(is_valid_move(Piece.ROOK, (0, 0), (0, 5)))     # True
print(is_valid_move(Piece.ROOK, (0, 0), (3, 0)))     # True
print(is_valid_move(Piece.ROOK, (0, 0), (1, 1)))     # False
```

---

## Übung 12: Graph Traversal (Sehr Schwer)

Implementiere Graph-Traversal mit Pattern Matching:

```python
@dataclass
class Node:
    id: int
    neighbors: list[int]

Graph = dict[int, Node]

def bfs(graph: Graph, start: int, target: int) -> list[int] | None:
    """Findet kürzesten Pfad mit BFS"""
    pass

def dfs(graph: Graph, start: int, target: int) -> list[int] | None:
    """Findet Pfad mit DFS"""
    pass
```

**Erwarteter Output:**
```python
graph = {
    1: Node(1, [2, 3]),
    2: Node(2, [1, 4]),
    3: Node(3, [1, 4]),
    4: Node(4, [2, 3, 5]),
    5: Node(5, [4])
}

print(bfs(graph, 1, 5))  # [1, 2, 4, 5] oder [1, 3, 4, 5]
print(dfs(graph, 1, 5))  # Ein gültiger Pfad
print(bfs(graph, 1, 99)) # None (nicht gefunden)
```

---

## Übung 13: Lisp Interpreter (Sehr Schwer)

Implementiere einen Mini-Lisp-Interpreter:

```python
@dataclass
class Symbol:
    name: str

@dataclass
class Number:
    value: float

@dataclass
class List:
    elements: list['Expr']

Expr = Symbol | Number | List

def evaluate(expr: Expr, env: dict) -> float:
    """Evaluiert Lisp-Ausdrück"""
    pass
```

Unterstütze: `+`, `-`, `*`, `/`, `define`, `let`

**Erwarteter Output:**
```python
env = {}

# (+ 1 2 3)
expr1 = List([Symbol("+"), Number(1), Number(2), Number(3)])
print(evaluate(expr1, env))  # 6.0

# (define x 10)
expr2 = List([Symbol("define"), Symbol("x"), Number(10)])
evaluate(expr2, env)

# (* x 5)
expr3 = List([Symbol("*"), Symbol("x"), Number(5)])
print(evaluate(expr3, env))  # 50.0
```

---

## Übung 14: SQL Query Builder (Sehr Schwer)

Erstelle einen SQL Query Builder mit Pattern Matching:

```python
@dataclass
class Select:
    columns: list[str]
    table: str
    where: 'Condition' | None = None

@dataclass
class Eq:
    field: str
    value: any

@dataclass
class And:
    left: 'Condition'
    right: 'Condition'

@dataclass
class Or:
    left: 'Condition'
    right: 'Condition'

Condition = Eq | And | Or

def build_query(query: Select) -> str:
    """Baut SQL-String"""
    pass
```

**Erwarteter Output:**
```python
query1 = Select(["name", "age"], "users", Eq("active", True))
print(build_query(query1))
# "SELECT name, age FROM users WHERE active = True"

query2 = Select(
    ["*"],
    "products",
    And(Eq("price", ">10"), Eq("stock", ">0"))
)
print(build_query(query2))
# "SELECT * FROM products WHERE price > 10 AND stock > 0"
```

---

## Übung 15: Regex-Matcher (Experte)

Implementiere einen vereinfachten Regex-Matcher:
- `.` - beliebiges Zeichen
- `*` - 0 oder mehr Wiederholungen
- `+` - 1 oder mehr Wiederholungen
- `?` - 0 oder 1 Vorkommen
- `|` - Oder

```python
def regex_match(pattern: str, text: str) -> bool:
    """Pattern Matching mit Regex"""
    pass
```

**Erwarteter Output:**
```python
print(regex_match("a.c", "abc"))      # True
print(regex_match("a.c", "ac"))       # False
print(regex_match("ab*c", "ac"))      # True (b* = 0 Mal)
print(regex_match("ab*c", "abbc"))    # True
print(regex_match("ab+c", "ac"))      # False (b+ = min 1)
print(regex_match("ab?c", "abc"))     # True
print(regex_match("ab?c", "ac"))      # True
print(regex_match("a|b", "a"))        # True
print(regex_match("a|b", "b"))        # True
```

---

## Bonusaufgabe: Mini-Compiler (Meister)

Implementiere einen Mini-Compiler der einfache Ausdrücke zu Assembly übersetzt:

```python
@dataclass
class Assign:
    var: str
    expr: 'Expr'

@dataclass
class Return:
    expr: 'Expr'

Statement = Assign | Return

def compile_to_asm(statements: list[Statement]) -> list[str]:
    """Compiliert zu Assembly-Code"""
    pass
```

**Erwarteter Output:**
```python
program = [
    Assign("x", BinOp("+", Num(5), Num(3))),
    Assign("y", BinOp("*", Symbol("x"), Num(2))),
    Return(Symbol("y"))
]

asm = compile_to_asm(program)
print("\n".join(asm))
# MOV R0, 5
# ADD R0, 3
# MOV [x], R0
# MOV R0, [x]
# MUL R0, 2
# MOV [y], R0
# MOV R0, [y]
# RET
```

---

## Tipps für alle Übungen

### Pattern Matching Best Practices:
1. **Spezifisch zu allgemein** - Ordne Cases vom spezifischsten zum allgemeinsten
2. **Guards nutzen** - Verwende `if` für komplexe Bedingungen
3. **Destructuring** - Extrahiere Werte direkt im Pattern
4. **Default-Case** - Immer `case _:` für unerwartete Fälle
5. **AS-Pattern** - Binde gesamtes Pattern wenn nötig

### Debugging:
```python
def debug_match(value):
    match value:
        case pattern:
            print(f"Matched: {pattern}")
            print(f"Type: {type(value)}")
            print(f"Value: {value}")
```

### Testing:
```python
def test_pattern_matching():
    test_cases = [
        (input1, expected_output1),
        (input2, expected_output2),
        # ...
    ]
    
    for input_val, expected in test_cases:
        result = your_function(input_val)
        assert result == expected, f"Failed for {input_val}"
```

---

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [PEP 636 - Tutorial](https://peps.python.org/pep-0636/) - Strukturiertes Pattern Matching Tutorial
- [PEP 634 - Specification](https://peps.python.org/pep-0634/) - Vollständige Spezifikation
- [match Statement](https://docs.python.org/3/reference/compound_stmts.html#match) - Referenz

## Verwandte Themen

- [[05_Pattern_Matching/01_Grundlagen/01_Match Statement|Match Statement]] - Grundlagen
- [[05_Pattern_Matching/01_Grundlagen/02_Pattern Syntax|Pattern Syntax]] - Alle Pattern-Typen
- [[06_Praxis/04_Lösungen/Lösungen Teil 5|Lösungen Teil 5]] - Musterlösungen
- [[04_Fortgeschritten/04_Dataclasses/01_Dataclass Basics|Dataclass Basics]] - Für Class Patterns

---
← [[05_Pattern_Matching/01_Grundlagen/02_Pattern Syntax|Pattern Syntax]] | [[INDEX|📑 Index]] | [[05_Pattern_Matching/02_Type_Hints/01_Type Annotations Basics|Type Annotations Basics]] →