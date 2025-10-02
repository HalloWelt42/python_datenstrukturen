---
tags: [python, datenstrukturen, enums, fortgeschritten]
created: 2025-10-02
---

← [[04_Fortgeschritten/04_Dataclasses/04_Übungen Dataclasses|Übungen Dataclasses]] | [[INDEX|📑 Index]] | [[04_Fortgeschritten/05_Enums/02_Enum Typen|Enum Typen]] →

# Enum Basics

> [!tip] Lernziel
> Du verstehst, was Enumerationen sind, warum sie besser als String-Konstanten sind, und kannst eigene Enums definieren und verwenden.

## Was sind Enums?

**Enumerations (Enums)** sind eine Möglichkeit, einen Satz benannter Konstanten zu definieren. Sie lösen das Problem von "magischen Strings" oder "magischen Zahlen" im Code und bieten Typsicherheit.

**Ohne Enums (problematisch):**
```python
# Magische Strings - anfällig für Tippfehler
status = "pending"
if status == "pendingg":  # Tippfehler wird nicht erkannt!
    print("Warten...")
```

**Mit Enums (typsicher):**
```python
from enum import Enum

class Status(Enum):
    PENDING = "pending"
    COMPLETED = "completed"
    FAILED = "failed"

status = Status.PENDING
if status == Status.PENDING:  # Typsicher und autovervollständigbar!
    print("Warten...")
```

## Syntax und Grundlagen

### Einfache Enum-Definition

```python
from enum import Enum

# Grundlegende Enum-Definition
class Color(Enum):
    RED = 1
    GREEN = 2
    BLUE = 3

# Zugriff auf Enum-Mitglieder
print(Color.RED)           # Color.RED
print(Color.RED.name)      # 'RED'
print(Color.RED.value)     # 1

# Enum-Mitglieder sind Singleton
print(Color.RED is Color.RED)  # True

# Vergleiche
print(Color.RED == Color.RED)   # True
print(Color.RED == Color.BLUE)  # False
print(Color.RED == 1)           # False (keine automatische Wert-Gleichheit!)
```

### Automatische Werte

```python
from enum import Enum, auto

class Direction(Enum):
    NORTH = auto()  # 1
    SOUTH = auto()  # 2
    EAST = auto()   # 3
    WEST = auto()   # 4

print(Direction.NORTH.value)  # 1
print(Direction.SOUTH.value)  # 2

# auto() startet bei 1 und zählt hoch
```

### Verschiedene Wert-Typen

```python
from enum import Enum

# String-Werte
class HttpMethod(Enum):
    GET = "GET"
    POST = "POST"
    PUT = "PUT"
    DELETE = "DELETE"

# Tuple-Werte für mehrere Attribute
class Planet(Enum):
    MERCURY = (3.303e+23, 2.4397e6)
    VENUS = (4.869e+24, 6.0518e6)
    EARTH = (5.976e+24, 6.37814e6)
    
    def __init__(self, mass, radius):
        self.mass = mass
        self.radius = radius
    
    @property
    def surface_gravity(self):
        G = 6.67430e-11
        return G * self.mass / (self.radius ** 2)

print(Planet.EARTH.mass)              # 5.976e+24
print(Planet.EARTH.surface_gravity)   # 9.802652743337129
```

## Details und Interna

### Enum-Mitglieder sind Instanzen

```python
from enum import Enum

class Status(Enum):
    ACTIVE = 1
    INACTIVE = 2

# Jedes Mitglied ist eine Instanz der Enum-Klasse
print(type(Status.ACTIVE))           # <enum 'Status'>
print(isinstance(Status.ACTIVE, Status))  # True

# Mitglieder sind Singleton - nur eine Instanz existiert
s1 = Status.ACTIVE
s2 = Status.ACTIVE
print(s1 is s2)  # True
```

### Iteration über Enums

```python
from enum import Enum

class Priority(Enum):
    LOW = 1
    MEDIUM = 2
    HIGH = 3
    CRITICAL = 4

# Über alle Mitglieder iterieren
for priority in Priority:
    print(f"{priority.name}: {priority.value}")
# Output:
# LOW: 1
# MEDIUM: 2
# HIGH: 3
# CRITICAL: 4

# Als Liste
priorities = list(Priority)
print(priorities)  # [<Priority.LOW: 1>, <Priority.MEDIUM: 2>, ...]

# Anzahl der Mitglieder
print(len(Priority))  # 4
```

### Zugriff auf Enum-Mitglieder

```python
from enum import Enum

class Status(Enum):
    PENDING = "pending"
    COMPLETED = "completed"
    FAILED = "failed"

# Verschiedene Zugriffsmethoden
status1 = Status.PENDING
status2 = Status["PENDING"]
status3 = Status("pending")

print(status1 == status2 == status3)  # True

# Prüfen ob Wert existiert
try:
    status = Status("unknown")
except ValueError as e:
    print(f"Fehler: {e}")
# Output: Fehler: 'unknown' is not a valid Status

# Sicherer Zugriff
def get_status(value):
    try:
        return Status(value)
    except ValueError:
        return None

print(get_status("pending"))   # Status.PENDING
print(get_status("unknown"))   # None
```

### Enum-Eigenschaften

```python
from enum import Enum

class Color(Enum):
    RED = 1
    GREEN = 2
    BLUE = 3

color = Color.RED

# Wichtige Eigenschaften
print(color.name)       # 'RED' (String)
print(color.value)      # 1 (beliebiger Typ)
print(repr(color))      # <Color.RED: 1>
print(str(color))       # Color.RED

# Dictionary aller Mitglieder
print(Color.__members__)
# {'RED': <Color.RED: 1>, 'GREEN': <Color.GREEN: 2>, 'BLUE': <Color.BLUE: 3>}
```

## Praktische Anwendungen

### Anwendungsfall 1: HTTP-Status-Codes

```python
from enum import Enum

class HttpStatus(Enum):
    # 2xx Success
    OK = 200
    CREATED = 201
    NO_CONTENT = 204
    
    # 4xx Client Errors
    BAD_REQUEST = 400
    UNAUTHORIZED = 401
    FORBIDDEN = 403
    NOT_FOUND = 404
    
    # 5xx Server Errors
    INTERNAL_ERROR = 500
    BAD_GATEWAY = 502
    
    def is_success(self):
        """Prüft ob Status-Code erfolgreich ist"""
        return 200 <= self.value < 300
    
    def is_client_error(self):
        """Prüft ob Client-Fehler"""
        return 400 <= self.value < 500
    
    def is_server_error(self):
        """Prüft ob Server-Fehler"""
        return 500 <= self.value < 600

# Verwendung
def handle_response(status: HttpStatus):
    if status.is_success():
        print(f"✅ Erfolg: {status.name}")
    elif status.is_client_error():
        print(f"⚠️ Client-Fehler: {status.name}")
    elif status.is_server_error():
        print(f"❌ Server-Fehler: {status.name}")

handle_response(HttpStatus.OK)            # ✅ Erfolg: OK
handle_response(HttpStatus.NOT_FOUND)     # ⚠️ Client-Fehler: NOT_FOUND
handle_response(HttpStatus.INTERNAL_ERROR) # ❌ Server-Fehler: INTERNAL_ERROR
```

### Anwendungsfall 2: Spiel-Zustände

```python
from enum import Enum, auto

class GameState(Enum):
    MENU = auto()
    PLAYING = auto()
    PAUSED = auto()
    GAME_OVER = auto()
    
    def can_pause(self):
        """Nur im PLAYING-State kann pausiert werden"""
        return self == GameState.PLAYING
    
    def can_resume(self):
        """Nur im PAUSED-State kann fortgesetzt werden"""
        return self == GameState.PAUSED

class Game:
    def __init__(self):
        self.state = GameState.MENU
        self.score = 0
    
    def start(self):
        if self.state == GameState.MENU:
            self.state = GameState.PLAYING
            self.score = 0
            print("🎮 Spiel gestartet!")
        else:
            print("⚠️ Spiel läuft bereits!")
    
    def pause(self):
        if self.state.can_pause():
            self.state = GameState.PAUSED
            print("⏸️ Pausiert")
        else:
            print("⚠️ Kann nicht pausieren!")
    
    def resume(self):
        if self.state.can_resume():
            self.state = GameState.PLAYING
            print("▶️ Fortgesetzt")
        else:
            print("⚠️ Kann nicht fortsetzen!")
    
    def game_over(self):
        if self.state == GameState.PLAYING:
            self.state = GameState.GAME_OVER
            print(f"💀 Game Over! Score: {self.score}")

# Verwendung
game = Game()
game.start()    # 🎮 Spiel gestartet!
game.pause()    # ⏸️ Pausiert
game.resume()   # ▶️ Fortgesetzt
game.game_over() # 💀 Game Over! Score: 0
```

### Anwendungsfall 3: Konfigurations-Umgebungen

```python
from enum import Enum

class Environment(Enum):
    DEVELOPMENT = "dev"
    STAGING = "staging"
    PRODUCTION = "prod"
    
    @property
    def database_url(self):
        """Gibt die Datenbank-URL für die Umgebung zurück"""
        urls = {
            Environment.DEVELOPMENT: "localhost:5432/dev_db",
            Environment.STAGING: "staging-db.company.com:5432/staging_db",
            Environment.PRODUCTION: "prod-db.company.com:5432/prod_db"
        }
        return urls[self]
    
    @property
    def debug_enabled(self):
        """Debug nur in Development"""
        return self == Environment.DEVELOPMENT
    
    @property
    def log_level(self):
        """Log-Level je nach Umgebung"""
        levels = {
            Environment.DEVELOPMENT: "DEBUG",
            Environment.STAGING: "INFO",
            Environment.PRODUCTION: "WARNING"
        }
        return levels[self]

class Config:
    def __init__(self, env: Environment):
        self.env = env
        self.database_url = env.database_url
        self.debug = env.debug_enabled
        self.log_level = env.log_level
    
    def __repr__(self):
        return (f"Config(env={self.env.name}, "
                f"db={self.database_url}, "
                f"debug={self.debug})")

# Verwendung
dev_config = Config(Environment.DEVELOPMENT)
print(dev_config)
# Config(env=DEVELOPMENT, db=localhost:5432/dev_db, debug=True)

prod_config = Config(Environment.PRODUCTION)
print(prod_config)
# Config(env=PRODUCTION, db=prod-db.company.com:5432/prod_db, debug=False)
```

## Häufige Fallstricke

### ❌ Fallstrick 1: Vergleich mit Werten statt Enum-Mitgliedern

```python
from enum import Enum

class Status(Enum):
    ACTIVE = 1
    INACTIVE = 0

# FALSCH - vergleicht mit Wert
status = Status.ACTIVE
if status == 1:  # False! Enum-Mitglieder sind nicht ihre Werte
    print("Aktiv")

# FALSCH - vergleicht mit String
if status == "ACTIVE":  # False!
    print("Aktiv")
```

✅ **Richtig:**
```python
from enum import Enum

class Status(Enum):
    ACTIVE = 1
    INACTIVE = 0

status = Status.ACTIVE

# Vergleiche mit Enum-Mitglied
if status == Status.ACTIVE:
    print("Aktiv")

# Oder prüfe den Wert explizit
if status.value == 1:
    print("Aktiv")

# Oder prüfe den Namen
if status.name == "ACTIVE":
    print("Aktiv")
```

### ❌ Fallstrick 2: Doppelte Werte ohne @unique

```python
from enum import Enum

class Color(Enum):
    RED = 1
    CRIMSON = 1  # Erlaubt! CRIMSON ist Alias für RED
    BLUE = 2

# CRIMSON ist nur ein Alias
print(Color.RED is Color.CRIMSON)  # True
print(list(Color))  # [<Color.RED: 1>, <Color.BLUE: 2>] - CRIMSON fehlt!
```

✅ **Richtig - Eindeutige Werte erzwingen:**
```python
from enum import Enum, unique

@unique
class Color(Enum):
    RED = 1
    CRIMSON = 1  # ValueError: duplicate values found in <enum 'Color'>: CRIMSON -> RED
    BLUE = 2
```

### ❌ Fallstrick 3: Enum-Mitglieder ändern

```python
from enum import Enum

class Status(Enum):
    ACTIVE = 1
    INACTIVE = 0

# FALSCH - Enums sind unveränderlich
try:
    Status.ACTIVE = 2
except AttributeError as e:
    print(f"Fehler: {e}")
# Output: Fehler: Cannot reassign member 'ACTIVE'.

# FALSCH - Werte sind auch unveränderlich
try:
    Status.ACTIVE.value = 2
except AttributeError as e:
    print(f"Fehler: {e}")
# Output: Fehler: can't set attribute
```

✅ **Richtig:**
```python
# Enums sind Konstanten und sollten nicht geändert werden
# Wenn du veränderbare Werte brauchst, sind Enums das falsche Werkzeug

# Alternative: Normale Klasse mit Klassenattributen
class Status:
    ACTIVE = 1
    INACTIVE = 0

# Diese kann man ändern (aber sollte man auch nicht in Production!)
Status.ACTIVE = 2
```

### ❌ Fallstrick 4: JSON-Serialisierung

```python
from enum import Enum
import json

class Priority(Enum):
    LOW = 1
    HIGH = 2

data = {"priority": Priority.HIGH}

# FALSCH - Enums sind nicht direkt JSON-serialisierbar
try:
    json.dumps(data)
except TypeError as e:
    print(f"Fehler: {e}")
# Output: Fehler: Object of type Priority is not JSON serializable
```

✅ **Richtig:**
```python
from enum import Enum
import json

class Priority(Enum):
    LOW = 1
    HIGH = 2
    
    def to_json(self):
        """Konvertiert Enum zu JSON-serialisierbarem Format"""
        return self.value

# Option 1: Manuell konvertieren
data = {"priority": Priority.HIGH.value}
print(json.dumps(data))  # {"priority": 2}

# Option 2: Custom JSON Encoder
class EnumEncoder(json.JSONEncoder):
    def default(self, obj):
        if isinstance(obj, Enum):
            return obj.value
        return super().default(obj)

data = {"priority": Priority.HIGH}
print(json.dumps(data, cls=EnumEncoder))  # {"priority": 2}
```

## Performance-Hinweise

### Zeitkomplexität

| Operation | Komplexität | Anmerkung |
|-----------|-------------|-----------|
| Zugriff per Name | O(1) | `Status.ACTIVE` |
| Zugriff per Wert | O(n) | `Status(1)` - durchsucht alle Mitglieder |
| Vergleich | O(1) | Singleton-Vergleich |
| Iteration | O(n) | `for s in Status` |
| Membership-Test | O(1) | `if "ACTIVE" in Status.__members__` |

### Speicherverbrauch

```python
from enum import Enum
import sys

class Status(Enum):
    ACTIVE = 1
    INACTIVE = 2

# Enum-Instanzen sind sehr leichtgewichtig
print(sys.getsizeof(Status.ACTIVE))  # ~56 Bytes (Singleton)

# Alle Mitglieder teilen sich die Enum-Klasse
s1 = Status.ACTIVE
s2 = Status.ACTIVE
print(s1 is s2)  # True - gleiche Instanz!
```

### Best Practices

**Wann Enums verwenden:**
- ✅ Fester Satz von Konstanten (Status, Farben, Richtungen)
- ✅ Typsicherheit ist wichtig
- ✅ Autovervollständigung gewünscht
- ✅ Vermeidung von "magischen Werten"

**Wann KEINE Enums verwenden:**
- ❌ Dynamische Werte (aus Datenbank, Config-Datei)
- ❌ Sehr große Mengen von Werten (>100)
- ❌ Werte müssen zur Laufzeit geändert werden
- ❌ Einfache Boolean-Flags (besser: bool)

## Übungsaufgaben

### Übung 1: Wochentage (Leicht)

Erstelle ein Enum `Weekday` mit allen Wochentagen. Füge eine Methode `is_weekend()` hinzu, die `True` für Samstag und Sonntag zurückgibt.

**Erwarteter Output:**
```python
print(Weekday.MONDAY.is_weekend())     # False
print(Weekday.SATURDAY.is_weekend())   # True
print(list(Weekday))                   # Alle 7 Tage
```

### Übung 2: Spielkarten (Mittel)

Erstelle zwei Enums:
1. `Suit` (Farbe) mit HEARTS, DIAMONDS, CLUBS, SPADES
2. `Rank` (Wert) mit TWO bis TEN, JACK, QUEEN, KING, ACE

Erstelle eine `Card`-Klasse, die beide Enums verwendet. Füge eine Methode `value()` hinzu, die den Punktewert zurückgibt (Zahlen = Wert, Bube=11, Dame=12, König=13, Ass=14).

**Erwarteter Output:**
```python
card = Card(Suit.HEARTS, Rank.ACE)
print(card)                  # ACE of HEARTS
print(card.value())          # 14

card2 = Card(Suit.CLUBS, Rank.SEVEN)
print(card2.value())         # 7
```

### Übung 3: Log-Level mit Filterung (Mittel)

Erstelle ein `LogLevel` Enum (DEBUG=10, INFO=20, WARNING=30, ERROR=40, CRITICAL=50). Implementiere eine `Logger`-Klasse, die:
- Ein Minimum-Level hat
- Nur Nachrichten >= diesem Level ausgibt
- Methoden `log(level, message)` hat

**Erwarteter Output:**
```python
logger = Logger(LogLevel.WARNING)
logger.log(LogLevel.INFO, "Info message")      # Nichts (zu niedrig)
logger.log(LogLevel.ERROR, "Error!")           # ERROR: Error!
logger.log(LogLevel.CRITICAL, "Critical!")     # CRITICAL: Critical!
```

### Übung 4: Berechtigungen mit Flags (Schwer)

Erstelle ein `Permission` Enum für Dateiberechtigungen (READ=4, WRITE=2, EXECUTE=1). Implementiere eine Funktion `has_permission(user_perms, required_perm)`, die prüft, ob ein Benutzer die erforderlichen Rechte hat.

Hinweis: Verwende Bitwise-Operationen (später mehr dazu in IntFlag).

**Erwarteter Output:**
```python
user_perms = 7  # READ + WRITE + EXECUTE
print(has_permission(user_perms, Permission.READ))     # True
print(has_permission(user_perms, Permission.WRITE))    # True

user_perms = 4  # nur READ
print(has_permission(user_perms, Permission.WRITE))    # False
```

### Übung 5: State Machine (Schwer)

Implementiere eine einfache State Machine für einen Aufzug:
- States: GROUND_FLOOR, FIRST_FLOOR, SECOND_FLOOR, MOVING_UP, MOVING_DOWN
- Jeder State kennt seine erlaubten Übergänge
- Methode `can_transition_to(new_state)` prüft Gültigkeit

**Erwarteter Output:**
```python
elevator = Elevator()
print(elevator.state)                              # GROUND_FLOOR
print(elevator.can_move_to(ElevatorState.FIRST_FLOOR))  # True
elevator.move_to(ElevatorState.FIRST_FLOOR)       # Elevator moving to FIRST_FLOOR
print(elevator.state)                              # FIRST_FLOOR
print(elevator.can_move_to(ElevatorState.MOVING_DOWN)) # False (invalid transition)
```

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [enum - Unterstützung für Enumerationen](https://docs.python.org/3/library/enum.html) - Vollständige Dokumentation mit allen Enum-Typen
- [PEP 435 - Adding an Enum type to the Python standard library](https://peps.python.org/pep-0435/) - Original-Proposal für Enums
- [enum How-to Guide](https://docs.python.org/3/howto/enum.html) - Praktischer Leitfaden mit vielen Beispielen

## Verwandte Themen

- [[04_Fortgeschritten/05_Enums/02_Enum Typen|Enum Typen]] - IntEnum, Flag, IntFlag, StrEnum
- [[05_Pattern_Matching/01_Grundlagen/01_Match Statement|Match Statement]] - Pattern Matching mit Enums
- [[05_Pattern_Matching/02_Type_Hints/01_Type Annotations Basics|Type Annotations]] - Enums in Type Hints
- [[04_Fortgeschritten/04_Dataclasses/01_Dataclass Basics|Dataclass Basics]] - Kombination mit Dataclasses

---
← [[04_Fortgeschritten/04_Dataclasses/04_Übungen Dataclasses|Übungen Dataclasses]] | [[INDEX|📑 Index]] | [[04_Fortgeschritten/05_Enums/02_Enum Typen|Enum Typen]] →