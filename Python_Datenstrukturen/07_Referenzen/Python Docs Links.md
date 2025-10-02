---
tags: [python, datenstrukturen, referenz, dokumentation]
created: 2025-10-02
---

← [[07_Referenzen/Ressourcen|Ressourcen]] | [[INDEX|📑 Index]] | [[07_Referenzen/Changelog Python 3.12+|Changelog Python 3.12+]] →

# Python Docs Links

> [!tip] Übersicht
> Direkte Links zur offiziellen Python-Dokumentation, organisiert nach Themen aus diesem Buch

## 🏠 Hauptdokumentation

**Python 3.12 Dokumentation**
- https://docs.python.org/3.12/

**Python 3.13 Dokumentation** (Latest)
- https://docs.python.org/3/

**Tutorial (Deutsch)**
- https://docs.python.org/de/3/tutorial/

## 📊 Built-in Types

### Numerische Typen
**int, float, complex**
- https://docs.python.org/3/library/stdtypes.html#numeric-types-int-float-complex
- Beschreibt alle numerischen Operationen und Methoden

**decimal - Dezimal-Arithmetik**
- https://docs.python.org/3/library/decimal.html
- Präzise Dezimalzahlen für Finanzberechnungen

**fractions - Rationale Zahlen**
- https://docs.python.org/3/library/fractions.html
- Bruchrechnung ohne Präzisionsverlust

**math - Mathematische Funktionen**
- https://docs.python.org/3/library/math.html
- Trigonometrie, Logarithmen, Konstanten

### Boolean & None
**bool - Boolesche Werte**
- https://docs.python.org/3/library/stdtypes.html#boolean-values
- True, False und Wahrheitswerte

**Truth Value Testing**
- https://docs.python.org/3/library/stdtypes.html#truth-value-testing
- Was ist truthy/falsy in Python

**None Type**
- https://docs.python.org/3/library/constants.html#None
- Der Null-Wert in Python

### Strings
**str - Text Sequence Type**
- https://docs.python.org/3/library/stdtypes.html#text-sequence-type-str
- Alle String-Methoden und Operationen

**String Methods**
- https://docs.python.org/3/library/stdtypes.html#string-methods
- Vollständige Referenz aller String-Methoden

**f-strings (Formatted String Literals)**
- https://docs.python.org/3/reference/lexical_analysis.html#f-strings
- Moderne String-Formatierung

**Format String Syntax**
- https://docs.python.org/3/library/string.html#format-string-syntax
- .format() und Format-Spezifikationen

**Unicode HOWTO**
- https://docs.python.org/3/howto/unicode.html
- Unicode in Python verstehen

### Bytes
**bytes - Immutable Bytes**
- https://docs.python.org/3/library/stdtypes.html#bytes-objects
- Binäre Daten, unveränderlich

**bytearray - Mutable Bytes**
- https://docs.python.org/3/library/stdtypes.html#bytearray-objects
- Veränderbare binäre Daten

**memoryview - Memory Views**
- https://docs.python.org/3/library/stdtypes.html#memory-views
- Effizienter Zugriff auf binäre Daten

## 📚 Sequence Types

### Listen
**list - Lists**
- https://docs.python.org/3/library/stdtypes.html#lists
- Vollständige List-Referenz

**List Methods**
- https://docs.python.org/3/tutorial/datastructures.html#more-on-lists
- Detaillierte Erklärung aller Methoden

**Sorting HOW TO**
- https://docs.python.org/3/howto/sorting.html
- Alles über Sortierung in Python

### Tupel
**tuple - Tuples**
- https://docs.python.org/3/library/stdtypes.html#tuples
- Unveränderliche Sequenzen

**Tuple Packing/Unpacking**
- https://docs.python.org/3/tutorial/datastructures.html#tuples-and-sequences
- Arbeiten mit Tupeln

### Range
**range - Range Objects**
- https://docs.python.org/3/library/stdtypes.html#ranges
- Immutable Sequence von Zahlen

## 🗂️ Mapping Types

### Dictionaries
**dict - Dictionaries**
- https://docs.python.org/3/library/stdtypes.html#mapping-types-dict
- Vollständige Dict-Referenz

**Dictionary View Objects**
- https://docs.python.org/3/library/stdtypes.html#dictionary-view-objects
- keys(), values(), items()

**PEP 584 - Dict Union Operators**
- https://peps.python.org/pep-0584/
- Dict Merge mit | Operator (Python 3.9+)

## 🎯 Set Types

**set - Set Objects**
- https://docs.python.org/3/library/stdtypes.html#set-types-set-frozenset
- Sets und Frozensets

**Set Operations**
- https://docs.python.org/3/library/stdtypes.html#set
- Union, Intersection, Difference, etc.

## 📦 Collections Module

**collections - Container Datatypes**
- https://docs.python.org/3/library/collections.html
- Spezielle Container-Typen

**collections.OrderedDict**
- https://docs.python.org/3/library/collections.html#collections.OrderedDict
- Dict mit garantierter Ordnung

**collections.defaultdict**
- https://docs.python.org/3/library/collections.html#collections.defaultdict
- Dict mit Default-Factory

**collections.Counter**
- https://docs.python.org/3/library/collections.html#collections.Counter
- Zählen von Elementen

**collections.deque**
- https://docs.python.org/3/library/collections.html#collections.deque
- Double-ended Queue

**collections.ChainMap**
- https://docs.python.org/3/library/collections.html#collections.ChainMap
- Mehrere Dicts kombinieren

**collections.namedtuple**
- https://docs.python.org/3/library/collections.html#collections.namedtuple
- Tuple mit benannten Feldern

## 🔧 Advanced Structures

### Array Module
**array - Efficient Arrays**
- https://docs.python.org/3/library/array.html
- Typisierte Arrays für numerische Daten

### Heapq
**heapq - Heap Queue Algorithm**
- https://docs.python.org/3/library/heapq.html
- Priority Queue Implementation

### Bisect
**bisect - Array Bisection Algorithm**
- https://docs.python.org/3/library/bisect.html
- Binäre Suche in sortierten Listen

### Queue
**queue - Synchronized Queue Classes**
- https://docs.python.org/3/library/queue.html
- Thread-safe Queues

## 🎨 Dataclasses & Enums

**dataclasses - Data Classes**
- https://docs.python.org/3/library/dataclasses.html
- Automatische Generierung von Special Methods

**PEP 557 - Data Classes**
- https://peps.python.org/pep-0557/
- Original Proposal

**enum - Enumerations**
- https://docs.python.org/3/library/enum.html
- Aufzählungstypen

**PEP 435 - Adding an Enum type**
- https://peps.python.org/pep-0435/
- Enum-Design

## 🎯 Pattern Matching

**Match Statements**
- https://docs.python.org/3/reference/compound_stmts.html#match
- Pattern Matching Syntax

**PEP 634 - Structural Pattern Matching**
- https://peps.python.org/pep-0634/
- Specification

**PEP 635 - Structural Pattern Matching: Motivation**
- https://peps.python.org/pep-0635/
- Hintergrund und Motivation

**PEP 636 - Structural Pattern Matching: Tutorial**
- https://peps.python.org/pep-0636/
- Tutorial mit Beispielen

## 🏷️ Type Hints

**typing - Type Hints**
- https://docs.python.org/3/library/typing.html
- Vollständige typing-Modul Referenz

**PEP 484 - Type Hints**
- https://peps.python.org/pep-0484/
- Original Type Hints Proposal

**PEP 585 - Type Hinting Generics In Standard Collections**
- https://peps.python.org/pep-0585/
- list[int] statt List[int] (Python 3.9+)

**PEP 604 - Union Operators**
- https://peps.python.org/pep-0604/
- int | str statt Union[int, str] (Python 3.10+)

**mypy - Static Type Checker**
- https://mypy.readthedocs.io/
- Externe Dokumentation für mypy

**typing.Protocol**
- https://docs.python.org/3/library/typing.html#typing.Protocol
- Structural Subtyping

**typing.TypedDict**
- https://docs.python.org/3/library/typing.html#typing.TypedDict
- Typed Dictionaries

## 🔄 Iterators & Generators

**Iterator Types**
- https://docs.python.org/3/library/stdtypes.html#iterator-types
- Iterator Protocol

**Generator Functions**
- https://docs.python.org/3/reference/expressions.html#generator-expressions
- yield und Generator Expressions

**itertools - Iterator Functions**
- https://docs.python.org/3/library/itertools.html
- Funktionale Iterator-Tools

**functools - Higher-order Functions**
- https://docs.python.org/3/library/functools.html
- reduce, partial, cache, etc.

## ⚡ Performance

**Time Complexity**
- https://wiki.python.org/moin/TimeComplexity
- Wiki mit Komplexitäten aller Operationen

**timeit - Measure Execution Time**
- https://docs.python.org/3/library/timeit.html
- Performance-Messung

**profile & cProfile**
- https://docs.python.org/3/library/profile.html
- Profiling von Python-Code

**tracemalloc - Trace Memory Allocations**
- https://docs.python.org/3/library/tracemalloc.html
- Memory-Profiling

## 🛠️ Built-in Functions

**Built-in Functions**
- https://docs.python.org/3/library/functions.html
- Alle eingebauten Funktionen

Wichtige Funktionen für Datenstrukturen:
- `len()`, `sum()`, `min()`, `max()`
- `sorted()`, `reversed()`, `enumerate()`, `zip()`
- `all()`, `any()`
- `map()`, `filter()`
- `range()`, `iter()`, `next()`

## 📖 Language Reference

**Data Model**
- https://docs.python.org/3/reference/datamodel.html
- Wie Python auf Objektebene funktioniert

**Special Method Names**
- https://docs.python.org/3/reference/datamodel.html#special-method-names
- __init__, __str__, __repr__, __len__, etc.

**Expressions**
- https://docs.python.org/3/reference/expressions.html
- List/Dict/Set Comprehensions, Generator Expressions

**Compound Statements**
- https://docs.python.org/3/reference/compound_stmts.html
- if, for, while, match, etc.

## 🎓 Tutorials & HOWTOs

**Data Structures Tutorial**
- https://docs.python.org/3/tutorial/datastructures.html
- Offizielles Tutorial

**Sorting HOW TO**
- https://docs.python.org/3/howto/sorting.html
- Sortierung meistern

**Functional Programming HOWTO**
- https://docs.python.org/3/howto/functional.html
- Iteratoren, Generatoren, etc.

**Descriptor HowTo Guide**
- https://docs.python.org/3/howto/descriptor.html
- Fortgeschrittenes Thema

## 🔍 Suchtipps

### Innerhalb der Python-Docs suchen
```
site:docs.python.org/3/ "list comprehension"
site:docs.python.org/3/ collections Counter
```

### Spezifische Python-Version
```
site:docs.python.org/3.12/ dataclasses
site:docs.python.org/3.13/ pattern matching
```

### Nach PEPs suchen
```
site:peps.python.org "type hints"
```

## 📱 Mobile & Offline

**Dash (macOS/iOS)**
- Offline-Dokumentation
- https://kapeli.com/dash

**Zeal (Windows/Linux)**
- Dash-Alternative
- https://zealdocs.org/

**DevDocs**
- Web-basiert, auch offline
- https://devdocs.io/python/

## 🌍 Mehrsprachig

**Deutsche Dokumentation**
- https://docs.python.org/de/3/
- Teilweise übersetzt

**Weitere Sprachen**
- Französisch: https://docs.python.org/fr/3/
- Japanisch: https://docs.python.org/ja/3/
- Spanisch: https://docs.python.org/es/3/
- Und weitere...

## 🆕 What's New

**What's New in Python 3.12**
- https://docs.python.org/3/whatsnew/3.12.html

**What's New in Python 3.13**
- https://docs.python.org/3/whatsnew/3.13.html

**Complete Changelog**
- https://docs.python.org/3/whatsnew/changelog.html

## 📧 Python Newsletter

**Python Insider**
- Offizieller Python-Blog
- https://blog.python.org/

**Python.org News**
- https://www.python.org/blogs/

## 🔗 Schnellzugriff nach Thema

### Listen
→ https://docs.python.org/3/library/stdtypes.html#lists

### Dictionaries
→ https://docs.python.org/3/library/stdtypes.html#mapping-types-dict

### Sets
→ https://docs.python.org/3/library/stdtypes.html#set-types-set-frozenset

### Strings
→ https://docs.python.org/3/library/stdtypes.html#text-sequence-type-str

### Collections
→ https://docs.python.org/3/library/collections.html

### Dataclasses
→ https://docs.python.org/3/library/dataclasses.html

### Type Hints
→ https://docs.python.org/3/library/typing.html

### Pattern Matching
→ https://docs.python.org/3/reference/compound_stmts.html#match

## 💡 Tipp

Setze ein Lesezeichen auf:
```
https://docs.python.org/3/
```

Dies zeigt immer die neueste stabile Python-Version!

## Verwandte Themen

- [[07_Referenzen/Glossar|Glossar]]
- [[07_Referenzen/Ressourcen|Ressourcen]]
- [[07_Referenzen/Changelog Python 3.12+|Changelog Python 3.12+]]

---
← [[07_Referenzen/Ressourcen|Ressourcen]] | [[INDEX|📑 Index]] | [[07_Referenzen/Changelog Python 3.12+|Changelog Python 3.12+]] →