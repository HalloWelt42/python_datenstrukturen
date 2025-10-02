---
tags: [python, datenstrukturen, referenz, glossar]
created: 2025-10-02
---

← [[06_Praxis/04_Lösungen/Lösungen Projekte|Lösungen Projekte]] | [[INDEX|📑 Index]] | [[07_Referenzen/Ressourcen|Ressourcen]] →

# Glossar

> [!tip] Navigation
> Alphabetisch sortierte Begriffserklärungen zu Python-Datenstrukturen

## A

**ABC (Abstract Base Class)**
Abstrakte Basisklasse, die nicht direkt instantiiert werden kann. Definiert Interface für Subklassen.
```python
from abc import ABC, abstractmethod
class MyABC(ABC):
    @abstractmethod
    def my_method(self):
        pass
```

**Aggregation**
Kombination mehrerer Werte zu einem Ergebnis (z.B. sum, count, average).

**Amortisierte Zeitkomplexität**
Durchschnittliche Performance über viele Operationen. Beispiel: `list.append()` ist amortisiert O(1).

**Annotation**
Type Hint für Variablen und Funktionen.
```python
name: str = "Alice"
def greet(name: str) -> str: ...
```

## B

**Big-O Notation**
Beschreibt Wachstumsrate von Algorithmen:
- O(1): Konstant
- O(log n): Logarithmisch
- O(n): Linear
- O(n log n): Linearithmisch
- O(n²): Quadratisch

**Bytecode**
Python kompiliert Code zu Bytecode (.pyc), der von der VM ausgeführt wird.

## C

**Cache**
Temporärer Speicher für schnellen Zugriff auf häufig benötigte Daten.

**Callable**
Objekt, das aufgerufen werden kann (Funktionen, Methoden, Klassen mit `__call__`).

**ChainMap**
Kombiniert mehrere Dicts zu einer View ohne Kopie.
```python
from collections import ChainMap
combined = ChainMap(dict1, dict2, dict3)
```

**Comprehension**
Kompakte Syntax zum Erstellen von Collections.
```python
squares = [x**2 for x in range(10)]
```

**Counter**
Dict-Subklasse für Häufigkeitszählung.
```python
from collections import Counter
counts = Counter(['a', 'b', 'a', 'c'])
```

## D

**Dataclass**
Decorator für automatische `__init__`, `__repr__`, etc.
```python
from dataclasses import dataclass
@dataclass
class Point:
    x: int
    y: int
```

**Decorator**
Funktion, die Funktion/Klasse modifiziert.
```python
@decorator
def func(): ...
```

**DefaultDict**
Dict mit Default-Wert für fehlende Keys.
```python
from collections import defaultdict
d = defaultdict(list)
```

**Deque**
Double-ended Queue mit O(1) Operationen an beiden Enden.
```python
from collections import deque
dq = deque([1, 2, 3])
```

**Descriptor**
Objekt mit `__get__`, `__set__`, `__delete__` für Attribut-Zugriff.

**Duck Typing**
"If it walks like a duck and quacks like a duck, it's a duck" - Typen werden durch Verhalten definiert, nicht durch Vererbung.

## E

**Enum**
Aufzählungstyp für benannte Konstanten.
```python
from enum import Enum
class Color(Enum):
    RED = 1
    GREEN = 2
```

**Eviction**
Entfernen von Einträgen aus Cache (z.B. bei LRU, LFU).

## F

**FIFO (First In First Out)**
Queue-Prinzip: Erstes Element wird zuerst verarbeitet.

**Frozenset**
Unveränderliche Version von Set.
```python
fs = frozenset([1, 2, 3])
```

## G

**GC (Garbage Collection)**
Automatisches Speichermanagement in Python (Reference Counting + Cycle Detection).

**Generator**
Funktion mit `yield`, erzeugt Werte lazy.
```python
def gen():
    yield 1
    yield 2
```

**Generator Expression**
Lazy Comprehension mit runden Klammern.
```python
gen = (x**2 for x in range(1000000))
```

**Generic**
Typ-Parameter für flexible Typdefinitionen.
```python
from typing import Generic, TypeVar
T = TypeVar('T')
class Box(Generic[T]): ...
```

## H

**Hash**
Funktion, die Objekte auf Integer abbildet. Benötigt für Dict/Set-Keys.

**Hashable**
Objekt mit `__hash__()` und `__eq__()`. Immutable Types sind meist hashable.

**Heap**
Binärer Baum mit Heap-Eigenschaft (Parent ≤ Children bei Min-Heap).

**Heapq**
Python-Modul für Heap-Queue-Algorithmus (Priority Queue).

## I

**Immutable**
Unveränderliches Objekt (str, tuple, frozenset, int, etc.).

**Index**
Position in Sequence oder Datenstruktur für schnellen Lookup.

**Iterable**
Objekt, das iteriert werden kann (hat `__iter__()`).

**Iterator**
Objekt mit `__next__()`, liefert Elemente nacheinander.

## J

**JSON**
JavaScript Object Notation - Textformat für Datenaustausch.

## K

**Key Function**
Funktion für `sort()`, `sorted()`, `min()`, `max()`.
```python
sorted(words, key=len)
```

## L

**Lambda**
Anonyme Funktion.
```python
square = lambda x: x**2
```

**Lazy Evaluation**
Berechnung erst bei Bedarf (Generatoren, Iteratoren).

**LFU (Least Frequently Used)**
Cache-Strategie: Entfernt am seltensten verwendete Einträge.

**LIFO (Last In First Out)**
Stack-Prinzip: Letztes Element wird zuerst verarbeitet.

**List Comprehension**
Kompakte Liste-Erstellung.
```python
[x**2 for x in range(10) if x % 2 == 0]
```

**Literal**
Type Hint für exakte Werte.
```python
from typing import Literal
Status = Literal["pending", "done"]
```

**LRU (Least Recently Used)**
Cache-Strategie: Entfernt am längsten nicht verwendete Einträge.

## M

**Mapping**
Abstrakte Collection mit Key-Value-Paaren (Dict ist Mapping).

**Memoization**
Caching von Funktionsergebnissen für Performance.

**Method Resolution Order (MRO)**
Reihenfolge der Klassen-Hierarchie bei Mehrfachvererbung.

**Mutable**
Veränderbares Objekt (list, dict, set).

## N

**NamedTuple**
Tuple mit benannten Feldern.
```python
from collections import namedtuple
Point = namedtuple('Point', ['x', 'y'])
```

**NewType**
Erstellt distinkte Type-Aliases.
```python
from typing import NewType
UserId = NewType('UserId', int)
```

## O

**O-Notation**
Siehe Big-O Notation.

**OrderedDict**
Dict mit Einfüge-Reihenfolge (seit Python 3.7 haben normale Dicts dies auch).

## P

**Pattern Matching**
Strukturelles Matching mit `match`/`case` (Python 3.10+).

**PEP**
Python Enhancement Proposal - Offizielles Dokument für Python-Änderungen.

**Protocol**
Struktureller Subtyping (Duck Typing mit Type Hints).
```python
from typing import Protocol
class Drawable(Protocol):
    def draw(self) -> str: ...
```

**Pythonic**
Idiomatischer Python-Code, der Best Practices folgt.

## Q

**Queue**
FIFO-Datenstruktur. Implementiert mit `collections.deque` oder `queue.Queue`.

## R

**Reference Counting**
Python zählt Referenzen auf Objekte für Garbage Collection.

**REPL**
Read-Eval-Print Loop - Interaktive Python-Shell.

## S

**Sequence**
Geordnete Collection (list, tuple, str, range).

**Set**
Ungeordnete Collection einzigartiger Elemente.

**Shallow Copy**
Kopiert nur erste Ebene, innere Objekte werden referenziert.
```python
copy = original.copy()
```

**Deep Copy**
Rekursive Kopie aller Ebenen.
```python
from copy import deepcopy
copy = deepcopy(original)
```

**Slice**
Teil-Sequenz mit `[start:stop:step]`.
```python
numbers[1:5:2]  # Index 1, 3
```

**Stack**
LIFO-Datenstruktur. Implementiert mit `list` oder `collections.deque`.

**String Interning**
Python speichert identische Strings nur einmal im Speicher.

## T

**Truthy/Falsy**
Wahrheitswert in booleschen Kontexten:
- Falsy: `None`, `False`, `0`, `""`, `[]`, `{}`, `set()`
- Truthy: Alles andere

**Tuple**
Unveränderliche Sequence.
```python
point = (10, 20)
```

**Type Alias**
Alternativer Name für Typ.
```python
Vector = list[float]
```

**Type Hint**
Optionale Typ-Annotation für statische Typ-Checks.

**TypedDict**
Dict mit definierten Keys und Typen.
```python
from typing import TypedDict
class User(TypedDict):
    name: str
    age: int
```

**TypeVar**
Type-Variable für Generics.
```python
T = TypeVar('T')
```

## U

**Unhashable**
Objekt ohne `__hash__()` - kann nicht als Dict-Key verwendet werden.

**Union**
Mehrere mögliche Typen.
```python
from typing import Union
def func(x: Union[int, str]): ...
# Python 3.10+: def func(x: int | str): ...
```

**Unpacking**
Extrahiert Werte aus Sequence.
```python
x, y = point
first, *rest = numbers
```

## V

**View**
Live-Ansicht auf Dict-Keys/Values/Items.
```python
keys_view = my_dict.keys()
```

## W

**Walrus Operator**
Assignment Expression `:=` (Python 3.8+).
```python
if (n := len(items)) > 10:
    print(f"Too many: {n}")
```

## Z

**Zen of Python**
Python-Philosophie, abrufbar mit `import this`:
- Beautiful is better than ugly
- Explicit is better than implicit
- Simple is better than complex
- Readability counts

## Symbole

**`*args`**
Variable Positionsargumente.
```python
def func(*args):
    print(args)  # Tuple
```

**`**kwargs`**
Variable Keyword-Argumente.
```python
def func(**kwargs):
    print(kwargs)  # Dict
```

**`_` (Underscore)**
- Konvention für "unwichtige" Variable
- Prefix für interne/private Attribute
- In REPL: Letztes Ergebnis

**`__dunder__`**
Special Methods (Magic Methods).
```python
__init__, __str__, __repr__, __len__, etc.
```

## Siehe auch

📚 **Weiterführende Ressourcen:**
- [[07_Referenzen/Ressourcen|Ressourcen]]
- [[07_Referenzen/Python Docs Links|Python Docs Links]]
- [[07_Referenzen/Changelog Python 3.12+|Changelog Python 3.12+]]

---
← [[06_Praxis/04_Lösungen/Lösungen Projekte|Lösungen Projekte]] | [[INDEX|📑 Index]] | [[07_Referenzen/Ressourcen|Ressourcen]] →