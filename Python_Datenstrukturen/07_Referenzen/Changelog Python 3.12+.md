---
tags: [python, datenstrukturen, referenz, changelog]
created: 2025-10-02
---

← [[07_Referenzen/Python Docs Links|Python Docs Links]] | [[INDEX|📑 Index]] →

# Changelog Python 3.12+

> [!tip] Übersicht
> Wichtige Änderungen und neue Features in Python 3.12 und 3.13, die Datenstrukturen betreffen

## 🎯 Python 3.12 (Oktober 2023)

### Highlights

**⚡ Performance-Verbesserungen**
- Durchschnittlich 5% schneller als Python 3.11
- Verbesserte Comprehension-Performance
- Optimierte dict und set Operationen

**🔍 Verbesserte Error Messages**
- Noch präzisere Fehlermeldungen
- Bessere Suggestions bei NameError
- Detailliertere Traceback-Informationen

### Neue Features

#### 1. Per-Interpreter GIL (PEP 684)
```python
# Experimentell: Sub-Interpreters mit eigenem GIL
# Bessere Parallelität für CPU-intensive Tasks
import _xxsubinterpreters as subinterpreters

# Noch nicht production-ready, aber vielversprechend
```

#### 2. Type Parameter Syntax (PEP 695)
```python
# Neu: Einfachere Generic-Syntax
def max[T](args: list[T]) -> T:
    ...

class Stack[T]:
    def __init__(self):
        self._items: list[T] = []
    
    def push(self, item: T) -> None:
        self._items.append(item)

# Alt (funktioniert weiterhin):
from typing import TypeVar, Generic
T = TypeVar('T')
def max(args: list[T]) -> T: ...
```

#### 3. Override Decorator (PEP 698)
```python
from typing import override

class Base:
    def method(self) -> None:
        pass

class Derived(Base):
    @override
    def method(self) -> None:  # Type checker validiert
        pass
    
    @override
    def methdo(self) -> None:  # Type error: Nicht in Base!
        pass
```

#### 4. f-string Verbesserungen
```python
# Neu: Wiederverwendung von Quotes
greeting = f"{'Hello'}"  # Früher nicht erlaubt!

# Mehrzeilige Expressions
value = f"{
    some_long_function_name(
        arg1, arg2, arg3
    )
}"

# Backslashes in f-strings
print(f"{'\n'.join(items)}")  # Funktioniert jetzt!
```

#### 5. Buffer Protocol Verbesserungen
```python
# Bessere memoryview und buffer Operationen
# Schnellerer Zugriff auf binäre Daten
data = bytearray(1000)
view = memoryview(data)
# Verbesserte Performance bei Slicing
```

### Änderungen an Datenstrukturen

#### Dict & Set Optimierungen
```python
# Schnellere dict.copy()
original = {i: i**2 for i in range(1000)}
copy = original.copy()  # Bis zu 2x schneller

# Optimierte set Operationen
s1 = set(range(1000))
s2 = set(range(500, 1500))
result = s1 & s2  # Schneller
```

#### List Comprehensions
```python
# Verbesserte Performance bei verschachtelten Comprehensions
matrix = [[i*j for j in range(100)] for i in range(100)]
# Schnellere Ausführung durch Optimierungen
```

### Deprecated & Entfernt

**Entfernt:**
- `asyncore` Modul (verwende `asyncio`)
- `asynchat` Modul (verwende `asyncio`)
- `smtpd` Modul

**Deprecated:**
- `datetime.utcnow()` → verwende `datetime.now(timezone.utc)`
- `datetime.utcfromtimestamp()` → verwende `datetime.fromtimestamp(timezone.utc)`

## 🚀 Python 3.13 (Oktober 2024)

### Highlights

**⚡ Massive Performance-Verbesserungen**
- JIT-Compiler (experimentell)
- Verbesserte Garbage Collection
- Optimierte String-Operationen

**🔓 Free-Threaded Python (PEP 703)**
- Optional: Python ohne GIL
- Bessere Multi-Threading Performance
- Compile mit `--disable-gil`

### Neue Features

#### 1. Improved Error Messages
```python
# Noch bessere Suggestions
name = "Alice"
print(nmae)
# NameError: name 'nmae' is not defined. Did you mean: 'name'?

# Bessere Fehlermeldungen bei Type Errors
def greet(name: str) -> str:
    return f"Hello, {name}"

greet(123)
# TypeError: greet() argument 'name' must be str, not int
```

#### 2. Neue REPL-Features
```python
# Syntax-Highlighting im REPL
# Bessere Tab-Completion
# Multiline-Editing verbessert
```

#### 3. Optimized Startup Time
```python
# Python startet schneller
# Besseres Caching von Bytecode
# Reduzierte Import-Zeit
```

#### 4. iOS und Android Support (PEP 730)
```python
# Offizieller Support für Mobile Platforms
# Python kann nun native Mobile-Apps entwickeln
```

### Datenstruktur-Verbesserungen

#### 1. Dict Performance
```python
# Verbesserte dict-Iteration
large_dict = {i: i**2 for i in range(100000)}
for key, value in large_dict.items():
    pass  # Schnellere Iteration
```

#### 2. List Slicing Optimierungen
```python
# Schnelleres Slicing bei großen Listen
big_list = list(range(1000000))
subset = big_list[::2]  # Optimiert
```

#### 3. Set Operations
```python
# Optimierte set Operationen
s1 = set(range(100000))
s2 = set(range(50000, 150000))
union = s1 | s2  # Schneller
intersection = s1 & s2  # Schneller
```

### Type System Verbesserungen

#### TypeIs (PEP 742)
```python
from typing import TypeIs

def is_str_list(val: list[object]) -> TypeIs[list[str]]:
    """Type guard für string Listen"""
    return all(isinstance(x, str) for x in val)

items: list[object] = ["a", "b", "c"]
if is_str_list(items):
    # items ist hier list[str]
    print(items[0].upper())  # Type-safe!
```

#### ReadOnly Collections (PEP 705)
```python
from typing import ReadOnly

def process(data: ReadOnly[dict[str, int]]) -> None:
    """Kann data nicht modifizieren"""
    value = data["key"]  # OK
    # data["key"] = 10  # Type error!
```

## 📊 Performance-Vergleich

### Benchmark: Dict Operations (relativ zu Python 3.11)

| Operation | Python 3.12 | Python 3.13 |
|-----------|-------------|-------------|
| dict.copy() | 1.8x | 2.1x |
| dict iteration | 1.1x | 1.3x |
| dict.get() | 1.0x | 1.2x |
| dict comprehension | 1.2x | 1.5x |

### Benchmark: List Operations

| Operation | Python 3.12 | Python 3.13 |
|-----------|-------------|-------------|
| list.append() | 1.0x | 1.1x |
| list comprehension | 1.3x | 1.6x |
| list slicing | 1.1x | 1.4x |
| list.sort() | 1.0x | 1.1x |

### Benchmark: String Operations

| Operation | Python 3.12 | Python 3.13 |
|-----------|-------------|-------------|
| str.join() | 1.2x | 1.5x |
| f-strings | 1.1x | 1.3x |
| str.replace() | 1.1x | 1.2x |
| str methods | 1.0x | 1.2x |

*Werte sind Durchschnitte und variieren je nach Workload*

## 🔮 Zukünftige Entwicklungen

### Python 3.14 (geplant Oktober 2025)

**Erwartete Features:**
- Weitere JIT-Optimierungen
- Verbesserte free-threaded Performance
- Neue Pattern Matching Features
- Optimierungen für AI/ML Workloads

**Mögliche Änderungen:**
- `typing` Module Vereinfachungen
- Neue Collections in stdlib
- Performance-Verbesserungen für async

## 📝 Migration Guide

### Von Python 3.11 zu 3.12

**✅ Empfohlene Änderungen:**
```python
# Alte Syntax (funktioniert weiterhin)
from typing import TypeVar, Generic
T = TypeVar('T')
class Stack(Generic[T]):
    pass

# Neue Syntax (empfohlen)
class Stack[T]:
    pass
```

**⚠️ Beachten:**
```python
# datetime Updates
from datetime import datetime, timezone

# Alt (deprecated)
now = datetime.utcnow()

# Neu (empfohlen)
now = datetime.now(timezone.utc)
```

### Von Python 3.12 zu 3.13

**Keine Breaking Changes** für normale Datenstrukturen-Nutzung.

**Optional: Free-Threading aktivieren**
```bash
# Kompiliere Python mit --disable-gil
./configure --disable-gil
make
```

## 🎯 Best Practices für Python 3.12+

### 1. Nutze neue Type Syntax
```python
# ✅ Modern
def process[T](items: list[T]) -> T:
    return items[0]

# ❌ Alt (aber OK)
from typing import TypeVar
T = TypeVar('T')
def process(items: list[T]) -> T:
    return items[0]
```

### 2. Verwende override Decorator
```python
from typing import override

class MyList(list):
    @override
    def append(self, item):
        print(f"Adding {item}")
        super().append(item)
```

### 3. Moderne datetime Nutzung
```python
from datetime import datetime, timezone

# ✅ Timezone-aware
now = datetime.now(timezone.utc)

# ❌ Deprecated
now = datetime.utcnow()
```

### 4. Nutze Performance-Verbesserungen
```python
# Dict-Copies sind schneller
large_dict = {i: i**2 for i in range(10000)}
copy = large_dict.copy()  # Automatisch optimiert

# Comprehensions profitieren von Optimierungen
result = [x**2 for x in range(10000) if x % 2 == 0]
```

## 🔍 Testing auf neuer Version

```python
import sys

if sys.version_info >= (3, 12):
    # Nutze Python 3.12+ Features
    class Stack[T]:
        pass
else:
    # Fallback für ältere Versionen
    from typing import TypeVar, Generic
    T = TypeVar('T')
    class Stack(Generic[T]):
        pass
```

## 📚 Weitere Ressourcen

**Offizielle Dokumentation:**
- Python 3.12 What's New: https://docs.python.org/3.12/whatsnew/3.12.html
- Python 3.13 What's New: https://docs.python.org/3.13/whatsnew/3.13.html

**PEPs:**
- PEP 695 (Type Parameter Syntax): https://peps.python.org/pep-0695/
- PEP 698 (Override Decorator): https://peps.python.org/pep-0698/
- PEP 703 (No-GIL Python): https://peps.python.org/pep-0703/
- PEP 742 (TypeIs): https://peps.python.org/pep-0742/

**Performance:**
- Python Speed Center: https://speed.python.org/

## 🎓 Zusammenfassung

**Python 3.12:**
- ✅ Neue Generic-Syntax
- ✅ Override Decorator
- ✅ Verbesserte f-strings
- ✅ 5% schneller

**Python 3.13:**
- ✅ Free-threaded Python (optional)
- ✅ JIT-Compiler (experimentell)
- ✅ Mobile Platform Support
- ✅ Massive Performance-Verbesserungen

**Empfehlung:**
- Nutze Python 3.12+ für neue Projekte
- Migriere bestehende Projekte schrittweise
- Teste Performance-kritische Code auf neueren Versionen
- Bleibe auf dem Laufenden mit PEPs

## Verwandte Themen

- [[07_Referenzen/Glossar|Glossar]]
- [[07_Referenzen/Ressourcen|Ressourcen]]
- [[07_Referenzen/Python Docs Links|Python Docs Links]]

---
← [[07_Referenzen/Python Docs Links|Python Docs Links]] | [[INDEX|📑 Index]] →

---

**🎉 Ende des Buches**

Vielen Dank, dass du dieses Buch durchgearbeitet hast! Du hast jetzt ein solides Verständnis von Python-Datenstrukturen. 

**Nächste Schritte:**
1. Baue eigene Projekte
2. Trage zu Open Source bei
3. Teile dein Wissen
4. Lerne weiter!

**Viel Erfolg auf deiner Python-Reise! 🐍**