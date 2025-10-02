---
tags: [python, datenstrukturen, enums, fortgeschritten]
created: 2025-10-02
---

← [[04_Fortgeschritten/05_Enums/01_Enum Basics|Enum Basics]] | [[INDEX|📑 Index]] | [[04_Fortgeschritten/05_Enums/03_Übungen Enums|Übungen Enums]] →

# Enum Typen

> [!tip] Lernziel
> Du kennst die verschiedenen spezialisierten Enum-Typen (IntEnum, Flag, IntFlag, StrEnum) und weißt, wann du welchen verwendest.

## Was sind spezialisierte Enum-Typen?

Python bietet mehrere spezialisierte Enum-Typen für verschiedene Anwendungsfälle:
- **IntEnum** - Enums die sich wie Integer verhalten
- **StrEnum** - Enums die sich wie Strings verhalten (ab Python 3.11)
- **Flag** - Enums für Bit-Flags (kombinierbar mit OR)
- **IntFlag** - Integer-Flags (kombinierbar + Integer-Verhalten)

Jeder Typ hat spezielle Eigenschaften und Anwendungsfälle.

## IntEnum - Integer Enumerations

### Grundlagen

```python
from enum import IntEnum

class Priority(IntEnum):
    LOW = 1
    MEDIUM = 2
    HIGH = 3
    CRITICAL = 4

# IntEnum verhält sich wie int
priority = Priority.HIGH
print(priority == 3)        # True (im Gegensatz zu normalem Enum!)
print(priority > Priority.LOW)   # True
print(priority + 1)         # 4
print(priority * 2)         # 6

# Sortierung funktioniert
priorities = [Priority.CRITICAL, Priority.LOW, Priority.MEDIUM]
print(sorted(priorities))
# [<Priority.LOW: 1>, <Priority.MEDIUM: 2>, <Priority.CRITICAL: 3>]
```

### Wann IntEnum verwenden

```python
from enum import IntEnum

class HttpStatusCode(IntEnum):
    OK = 200
    CREATED = 201
    BAD_REQUEST = 400
    NOT_FOUND = 404
    SERVER_ERROR = 500

def handle_status(code: int):
    """Funktion erwartet int, kann aber IntEnum nehmen"""
    if code >= 500:
        print("Server-Fehler")
    elif code >= 400:
        print("Client-Fehler")
    elif code >= 200:
        print("Erfolg")

# IntEnum kann überall verwendet werden, wo int erwartet wird
handle_status(HttpStatusCode.OK)           # Erfolg
handle_status(HttpStatusCode.SERVER_ERROR) # Server-Fehler

# Vergleiche mit normalen ints
response_code = 404
if response_code == HttpStatusCode.NOT_FOUND:
    print("Seite nicht gefunden")  # Wird ausgeführt
```

### IntEnum vs. Enum

```python
from enum import Enum, IntEnum

class NormalPriority(Enum):
    LOW = 1
    HIGH = 2

class IntPriority(IntEnum):
    LOW = 1
    HIGH = 2

# Normales Enum - strenge Typsicherheit
normal = NormalPriority.HIGH
print(normal == 2)              # False
print(normal.value == 2)        # True
# print(normal > NormalPriority.LOW)  # TypeError!

# IntEnum - verhält sich wie int
int_prio = IntPriority.HIGH
print(int_prio == 2)            # True
print(int_prio > IntPriority.LOW)  # True
print(int_prio + 1)             # 3
```

## StrEnum - String Enumerations (Python 3.11+)

### Grundlagen

```python
from enum import StrEnum

class Color(StrEnum):
    RED = "red"
    GREEN = "green"
    BLUE = "blue"

# StrEnum verhält sich wie str
color = Color.RED
print(color == "red")           # True
print(color.upper())            # "RED"
print(color + "_light")         # "red_light"
print(f"Color: {color}")        # "Color: red"

# String-Operationen funktionieren
colors = [Color.BLUE, Color.RED, Color.GREEN]
print(sorted(colors))           # Alphabetische Sortierung
```

### Automatische String-Werte

```python
from enum import StrEnum

class LogLevel(StrEnum):
    DEBUG = "debug"
    INFO = "info"
    WARNING = "warning"
    ERROR = "error"

# Oder mit auto() für automatische lowercase-Namen
from enum import auto

class Status(StrEnum):
    PENDING = auto()      # "pending"
    COMPLETED = auto()    # "completed"
    FAILED = auto()       # "failed"

print(Status.PENDING)     # Status.PENDING
print(Status.PENDING.value)  # "pending"
```

### Praktisches Beispiel

```python
from enum import StrEnum

class Environment(StrEnum):
    DEV = "development"
    STAGE = "staging"
    PROD = "production"

def load_config(env: str) -> dict:
    """Funktion erwartet str, kann aber StrEnum nehmen"""
    configs = {
        "development": {"debug": True, "db": "localhost"},
        "staging": {"debug": False, "db": "stage-db"},
        "production": {"debug": False, "db": "prod-db"}
    }
    return configs.get(env, {})

# StrEnum funktioniert überall wo str erwartet wird
config = load_config(Environment.DEV)
print(config)  # {"debug": True, "db": "localhost"}

# String-Vergleiche funktionieren
current_env = "production"
if current_env == Environment.PROD:
    print("Production-Modus aktiv")  # Wird ausgeführt
```

## Flag - Kombinierbare Bit-Flags

### Grundlagen

```python
from enum import Flag, auto

class Permission(Flag):
    READ = auto()      # 1 (binär: 001)
    WRITE = auto()     # 2 (binär: 010)
    EXECUTE = auto()   # 4 (binär: 100)

# Flags können kombiniert werden mit OR (|)
user_perms = Permission.READ | Permission.WRITE
print(user_perms)  # Permission.READ|WRITE

# Prüfung mit IN
print(Permission.READ in user_perms)    # True
print(Permission.EXECUTE in user_perms) # False

# Entfernen mit XOR (^) oder AND NOT (&~)
new_perms = user_perms & ~Permission.READ
print(new_perms)  # Permission.WRITE

# Alle Flags setzen
all_perms = Permission.READ | Permission.WRITE | Permission.EXECUTE
print(all_perms)  # Permission.READ|WRITE|EXECUTE
```

### Flag-Operationen

```python
from enum import Flag, auto

class FileMode(Flag):
    READ = auto()
    WRITE = auto()
    APPEND = auto()
    BINARY = auto()

# OR (|) - Flags kombinieren
mode = FileMode.READ | FileMode.WRITE
print(mode)  # FileMode.READ|WRITE

# AND (&) - Gemeinsame Flags
mode1 = FileMode.READ | FileMode.WRITE
mode2 = FileMode.WRITE | FileMode.APPEND
common = mode1 & mode2
print(common)  # FileMode.WRITE

# XOR (^) - Unterschiedliche Flags
diff = mode1 ^ mode2
print(diff)  # FileMode.READ|APPEND

# NOT (~) - Invertieren
not_mode = ~FileMode.READ
print(not_mode)  # FileMode.WRITE|APPEND|BINARY

# Prüfen ob Flag gesetzt ist
if FileMode.READ in mode:
    print("Lesen erlaubt")
```

### Praktisches Beispiel: Dateiberechtigungen

```python
from enum import Flag, auto

class FilePermission(Flag):
    OWNER_READ = auto()
    OWNER_WRITE = auto()
    OWNER_EXECUTE = auto()
    GROUP_READ = auto()
    GROUP_WRITE = auto()
    GROUP_EXECUTE = auto()
    OTHER_READ = auto()
    OTHER_WRITE = auto()
    OTHER_EXECUTE = auto()

class File:
    def __init__(self, name: str, permissions: FilePermission):
        self.name = name
        self.permissions = permissions
    
    def has_permission(self, perm: FilePermission) -> bool:
        """Prüft ob Berechtigung gesetzt ist"""
        return perm in self.permissions
    
    def add_permission(self, perm: FilePermission):
        """Fügt Berechtigung hinzu"""
        self.permissions |= perm
    
    def remove_permission(self, perm: FilePermission):
        """Entfernt Berechtigung"""
        self.permissions &= ~perm
    
    def __repr__(self):
        return f"File('{self.name}', {self.permissions})"

# Verwendung
file = File(
    "document.txt",
    FilePermission.OWNER_READ | 
    FilePermission.OWNER_WRITE |
    FilePermission.GROUP_READ
)

print(file.has_permission(FilePermission.OWNER_WRITE))  # True
print(file.has_permission(FilePermission.OTHER_READ))   # False

file.add_permission(FilePermission.OTHER_READ)
print(file.has_permission(FilePermission.OTHER_READ))   # True
```

### Vordefinierte Flag-Kombinationen

```python
from enum import Flag, auto

class Color(Flag):
    RED = auto()
    GREEN = auto()
    BLUE = auto()
    
    # Vordefinierte Kombinationen
    YELLOW = RED | GREEN
    CYAN = GREEN | BLUE
    MAGENTA = RED | BLUE
    WHITE = RED | GREEN | BLUE

print(Color.YELLOW)  # Color.YELLOW
print(Color.WHITE)   # Color.WHITE

# Prüfung funktioniert
print(Color.RED in Color.YELLOW)   # True
print(Color.BLUE in Color.YELLOW)  # False
```

## IntFlag - Integer-Flags

### Grundlagen

```python
from enum import IntFlag, auto

class Permission(IntFlag):
    NONE = 0
    READ = auto()      # 1
    WRITE = auto()     # 2
    EXECUTE = auto()   # 4

# IntFlag kombiniert Flag-Verhalten mit int-Arithmetik
perms = Permission.READ | Permission.WRITE
print(perms)           # Permission.READ|WRITE
print(perms == 3)      # True (1 + 2 = 3)
print(perms > Permission.READ)  # True

# Kann mit normalen ints kombiniert werden
perms = 5  # READ (1) + EXECUTE (4)
perm_flag = Permission(perms)
print(perm_flag)  # Permission.READ|EXECUTE
```

### Unix-Dateirechte Beispiel

```python
from enum import IntFlag

class UnixPermission(IntFlag):
    """Unix-Style Dateirechte (chmod)"""
    NONE = 0
    
    # Other (0-2)
    OTHER_EXECUTE = 1
    OTHER_WRITE = 2
    OTHER_READ = 4
    
    # Group (3-5)
    GROUP_EXECUTE = 8
    GROUP_WRITE = 16
    GROUP_READ = 32
    
    # Owner (6-8)
    OWNER_EXECUTE = 64
    OWNER_WRITE = 128
    OWNER_READ = 256
    
    # Kombinationen
    OTHER_ALL = OTHER_READ | OTHER_WRITE | OTHER_EXECUTE
    GROUP_ALL = GROUP_READ | GROUP_WRITE | GROUP_EXECUTE
    OWNER_ALL = OWNER_READ | OWNER_WRITE | OWNER_EXECUTE
    
    ALL = OWNER_ALL | GROUP_ALL | OTHER_ALL

def chmod(permissions: int) -> UnixPermission:
    """Konvertiert Unix-Permission-Zahl zu IntFlag"""
    return UnixPermission(permissions)

# chmod 755 = Owner: rwx, Group: rx, Other: rx
perm_755 = (UnixPermission.OWNER_ALL | 
            UnixPermission.GROUP_READ | UnixPermission.GROUP_EXECUTE |
            UnixPermission.OTHER_READ | UnixPermission.OTHER_EXECUTE)

print(perm_755.value)  # 493
print(perm_755)        # Zeigt alle gesetzten Flags

# chmod 644 = Owner: rw, Group: r, Other: r
perm_644 = (UnixPermission.OWNER_READ | UnixPermission.OWNER_WRITE |
            UnixPermission.GROUP_READ | UnixPermission.OTHER_READ)

print(perm_644.value)  # 420
```

## Details und Interna

### Flag-Werte müssen Potenzen von 2 sein

```python
from enum import Flag

# RICHTIG - Potenzen von 2
class GoodFlag(Flag):
    A = 1   # 2^0
    B = 2   # 2^1
    C = 4   # 2^2
    D = 8   # 2^3

# FALSCH - Keine Potenzen von 2
class BadFlag(Flag):
    A = 1
    B = 3  # Nicht 2^n, sondern 1+2
    C = 5  # Nicht 2^n, sondern 1+4

# auto() generiert automatisch korrekte Werte
from enum import auto

class AutoFlag(Flag):
    A = auto()  # 1
    B = auto()  # 2
    C = auto()  # 4
    D = auto()  # 8
```

### Iteration über Flags

```python
from enum import Flag, auto

class Status(Flag):
    READY = auto()
    WAITING = auto()
    RUNNING = auto()
    DONE = auto()

# Iteration über Enum-Definition
for status in Status:
    print(f"{status.name}: {status.value}")
# Output:
# READY: 1
# WAITING: 2
# RUNNING: 4
# DONE: 8

# Iteration über gesetzte Flags in Kombination
combined = Status.READY | Status.DONE
for status in Status:
    if status in combined:
        print(f"{status.name} ist gesetzt")
# Output:
# READY ist gesetzt
# DONE ist gesetzt
```

### Typ-Hierarchie

```python
from enum import Enum, IntEnum, Flag, IntFlag
import numbers

class MyEnum(Enum):
    A = 1

class MyIntEnum(IntEnum):
    A = 1

class MyFlag(Flag):
    A = 1

class MyIntFlag(IntFlag):
    A = 1

# Typ-Prüfungen
print(isinstance(MyEnum.A, int))        # False
print(isinstance(MyIntEnum.A, int))     # True
print(isinstance(MyFlag.A, int))        # False
print(isinstance(MyIntFlag.A, int))     # True

# Alle sind Enums
print(isinstance(MyEnum.A, Enum))       # True
print(isinstance(MyIntEnum.A, Enum))    # True
print(isinstance(MyFlag.A, Enum))       # True
print(isinstance(MyIntFlag.A, Enum))    # True
```

## Häufige Fallstricke

### ❌ Fallstrick 1: Falsche Flag-Werte

```python
from enum import Flag

# FALSCH - Werte sind keine Potenzen von 2
class BadPermission(Flag):
    READ = 3    # 3 ist nicht 2^n
    WRITE = 5   # 5 ist nicht 2^n
    EXECUTE = 7 # 7 ist nicht 2^n

# Kombinationen funktionieren nicht richtig
perms = BadPermission.READ | BadPermission.WRITE
print(perms)  # Unerwartetes Ergebnis
```

✅ **Richtig:**
```python
from enum import Flag, auto

class GoodPermission(Flag):
    READ = auto()      # 1
    WRITE = auto()     # 2
    EXECUTE = auto()   # 4

perms = GoodPermission.READ | GoodPermission.WRITE
print(perms)  # GoodPermission.READ|WRITE
```

### ❌ Fallstrick 2: Flag-Prüfung mit ==

```python
from enum import Flag, auto

class Status(Flag):
    ACTIVE = auto()
    PENDING = auto()
    COMPLETED = auto()

status = Status.ACTIVE | Status.PENDING

# FALSCH - prüft auf exakte Gleichheit
if status == Status.ACTIVE:
    print("Nur ACTIVE")  # Wird nicht ausgeführt

# FALSCH - funktioniert nicht mit Kombinationen
if Status.ACTIVE and Status.PENDING in status:
    print("Beides")  # Falsche Logik
```

✅ **Richtig:**
```python
from enum import Flag, auto

class Status(Flag):
    ACTIVE = auto()
    PENDING = auto()
    COMPLETED = auto()

status = Status.ACTIVE | Status.PENDING

# Richtig - prüft ob Flag gesetzt ist
if Status.ACTIVE in status:
    print("ACTIVE ist gesetzt")  # ✓

if Status.PENDING in status:
    print("PENDING ist gesetzt")  # ✓

# Prüfen ob mehrere Flags gesetzt sind
if Status.ACTIVE in status and Status.PENDING in status:
    print("Beide sind gesetzt")  # ✓
```

### ❌ Fallstrick 3: IntEnum wenn Enum ausreicht

```python
from enum import IntEnum

# NICHT IDEAL - IntEnum ohne guten Grund
class Color(IntEnum):
    RED = 1
    GREEN = 2
    BLUE = 3

# Verliert Typsicherheit
color = Color.RED
print(color == 1)  # True - kann versehentlich mit int verwechselt werden

def set_color(c: Color):
    print(f"Color: {c}")

set_color(1)  # Funktioniert, aber nicht typsicher!
```

✅ **Richtig:**
```python
from enum import Enum

# Besser - normales Enum für Typsicherheit
class Color(Enum):
    RED = 1
    GREEN = 2
    BLUE = 3

color = Color.RED
print(color == 1)  # False - typsicher

def set_color(c: Color):
    print(f"Color: {c}")

# set_color(1)  # Typ-Checker warnt!
set_color(Color.RED)  # Korrekt
```

### ❌ Fallstrick 4: Flag mit 0-Wert vergessen

```python
from enum import Flag, auto

# Fehlt: NONE = 0
class Status(Flag):
    ACTIVE = auto()
    PENDING = auto()
    DONE = auto()

# Problem: Kein Weg, "keine Flags" zu repräsentieren
status = ???  # Wie setzt man auf "leer"?
```

✅ **Richtig:**
```python
from enum import Flag, auto

class Status(Flag):
    NONE = 0        # Explizit definieren!
    ACTIVE = auto()
    PENDING = auto()
    DONE = auto()

# Jetzt können wir "leer" darstellen
status = Status.NONE
print(status)  # Status.NONE
print(status.value)  # 0

# Und leere Prüfung
if status == Status.NONE:
    print("Keine Status-Flags gesetzt")
```

## Performance-Hinweise

### Zeitkomplexität

| Operation | Flag/IntFlag | IntEnum | StrEnum |
|-----------|--------------|---------|---------|
| Zugriff | O(1) | O(1) | O(1) |
| Vergleich | O(1) | O(1) | O(1) |
| Kombination (OR) | O(1) | N/A | N/A |
| In-Test | O(1) | N/A | N/A |
| Iteration | O(n) | O(n) | O(n) |

### Best Practices

**IntEnum verwenden wenn:**
- ✅ Kompatibilität mit bestehendem int-Code nötig
- ✅ Sortierung/Vergleiche wichtig sind
- ✅ Mathematische Operationen benötigt werden

**StrEnum verwenden wenn:**
- ✅ String-Kompatibilität nötig (JSON, APIs)
- ✅ Lesbare Serialisierung wichtig ist
- ✅ String-Operationen benötigt werden

**Flag verwenden wenn:**
- ✅ Mehrere Optionen gleichzeitig gesetzt sein können
- ✅ Bitwise-Operationen nötig sind
- ✅ Keine int-Kompatibilität benötigt wird

**IntFlag verwenden wenn:**
- ✅ Flag-Funktionalität + int-Kompatibilität nötig
- ✅ Legacy-Code mit int-Flags existiert
- ✅ Unix-Style Permissions o.ä. implementiert werden

## Übungsaufgaben

### Übung 1: HTTP-Methoden mit IntEnum (Leicht)

Erstelle ein `HttpMethod` IntEnum mit Werten GET=1, POST=2, PUT=3, DELETE=4. Implementiere eine Funktion `is_safe(method: int) -> bool`, die nur GET als sicher einstuft.

**Erwarteter Output:**
```python
print(is_safe(HttpMethod.GET))     # True
print(is_safe(HttpMethod.POST))    # False
print(is_safe(1))                  # True (IntEnum-Kompatibilität)
```

### Übung 2: Log-Level mit StrEnum (Leicht)

Erstelle ein `LogLevel` StrEnum (DEBUG, INFO, WARNING, ERROR). Implementiere eine Funktion `parse_log_line(line: str)` die Zeilen wie "ERROR: Connection failed" parst.

**Erwarteter Output:**
```python
level, message = parse_log_line("ERROR: Connection failed")
print(level == LogLevel.ERROR)     # True
print(message)                     # "Connection failed"
```

### Übung 3: Feature Flags (Mittel)

Erstelle ein `Feature` Flag mit DARK_MODE, NOTIFICATIONS, ANALYTICS, BETA_UI. Implementiere eine `UserSettings`-Klasse mit Methoden `enable()`, `disable()`, `has_feature()`.

**Erwarteter Output:**
```python
settings = UserSettings()
settings.enable(Feature.DARK_MODE | Feature.NOTIFICATIONS)
print(settings.has_feature(Feature.DARK_MODE))      # True
print(settings.has_feature(Feature.ANALYTICS))      # False
settings.disable(Feature.DARK_MODE)
print(settings.has_feature(Feature.DARK_MODE))      # False
```

### Übung 4: Dateimodus mit IntFlag (Mittel)

Erstelle ein `FileMode` IntFlag für Unix-Dateimodi: OWNER_R=256, OWNER_W=128, OWNER_X=64, GROUP_R=32, etc. Implementiere `chmod(mode: int) -> FileMode` und `can_read(mode: FileMode, user_type: str) -> bool`.

**Erwarteter Output:**
```python
mode = chmod(0o755)  # rwxr-xr-x
print(can_read(mode, "owner"))     # True
print(can_read(mode, "group"))     # True
print(can_write(mode, "other"))    # False
```

### Übung 5: Kombinierte Enum-System (Schwer)

Erstelle ein System mit:
1. `TaskPriority` (IntEnum): LOW=1, MEDIUM=2, HIGH=3
2. `TaskStatus` (StrEnum): TODO, IN_PROGRESS, DONE
3. `TaskFlag` (Flag): URGENT, RECURRING, BLOCKED

Implementiere `Task`-Klasse die alle drei verwendet mit Methode `should_work_on_now()` die True zurückgibt wenn: Priority >= MEDIUM ODER URGENT Flag gesetzt ODER Status == IN_PROGRESS.

**Erwarteter Output:**
```python
task1 = Task("Bug", TaskPriority.HIGH, TaskStatus.TODO, TaskFlag.URGENT)
print(task1.should_work_on_now())  # True (HIGH priority + URGENT)

task2 = Task("Refactor", TaskPriority.LOW, TaskStatus.TODO, TaskFlag(0))
print(task2.should_work_on_now())  # False

task3 = Task("Feature", TaskPriority.LOW, TaskStatus.IN_PROGRESS, TaskFlag(0))
print(task3.should_work_on_now())  # True (IN_PROGRESS)
```

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [enum - IntEnum](https://docs.python.org/3/library/enum.html#enum.IntEnum) - Integer Enumerations
- [enum - Flag](https://docs.python.org/3/library/enum.html#enum.Flag) - Flag und IntFlag für Bit-Operationen
- [enum - StrEnum](https://docs.python.org/3/library/enum.html#enum.StrEnum) - String Enumerations (3.11+)
- [Enum How-to - Specialized Enum Types](https://docs.python.org/3/howto/enum.html#specialized-enum-types) - Detaillierte Beispiele

## Verwandte Themen

- [[04_Fortgeschritten/05_Enums/01_Enum Basics|Enum Basics]] - Grundlagen von Enumerations
- [[04_Fortgeschritten/05_Enums/03_Übungen Enums|Übungen Enums]] - Mehr Praxisübungen
- [[01_Grundlagen/02_Numerische_Typen/01_Integer (int)|Integer (int)]] - Integer-Grundlagen
- [[01_Grundlagen/04_Strings/01_String Basics|String Basics]] - String-Grundlagen

---
← [[04_Fortgeschritten/05_Enums/01_Enum Basics|Enum Basics]] | [[INDEX|📑 Index]] | [[04_Fortgeschritten/05_Enums/03_Übungen Enums|Übungen Enums]] →