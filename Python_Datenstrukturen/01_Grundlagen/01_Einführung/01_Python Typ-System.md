---
tags: [python, datenstrukturen, typsystem, grundlagen]
created: 2025-10-01
---

← [[Über Python 3.12+|Über Python 3.12+]] | [[INDEX|📑 Index]] | [[02_Mutability vs Immutability|Mutability vs Immutability]] →

# Python Typ-System

> [!tip] Lernziel
> Du verstehst, wie Pythons dynamisches Typ-System funktioniert und kannst zwischen Typen unterscheiden

## Was ist ein Typ-System?

Ein **Typ-System** definiert, welche Arten von Werten (Typen) eine Programmiersprache hat und wie sie sich verhalten. Python hat ein **dynamisches Typ-System** - das bedeutet:

- Variablen haben **keinen festen Typ**
- Der Typ wird zur **Laufzeit** bestimmt
- **Derselbe Variablenname** kann unterschiedliche Typen speichern

```python
# Dynamische Typisierung in Aktion
x = 42          # x ist jetzt ein int
print(type(x))  # <class 'int'>

x = "Hallo"     # x ist jetzt ein str - kein Problem!
print(type(x))  # <class 'str'>

x = [1, 2, 3]   # x ist jetzt eine list
print(type(x))  # <class 'list'>
```

## Statisch vs. Dynamisch

### Statische Typisierung (z.B. Java, C++)

```java
// Java: Typ muss vorher deklariert werden
int x = 42;
x = "Hallo";  // ❌ FEHLER: String kann nicht int zugewiesen werden
```

### Dynamische Typisierung (Python)

```python
# Python: Typ wird automatisch ermittelt
x = 42
x = "Hallo"  # ✅ Kein Problem!
```

**Vorteile der dynamischen Typisierung:**
- ✅ Flexibler Code
- ✅ Weniger Boilerplate
- ✅ Schnellere Entwicklung

**Nachteile:**
- ⚠️ Typfehler erst zur Laufzeit
- ⚠️ Schwieriger zu refactoren
- ⚠️ Ohne Type Hints: weniger IDE-Support

## Python's Typ-Hierarchie

Python organisiert Typen in einer Hierarchie. Alles erbt von `object`:

```
object (Basisklasse für alles)
├── int
├── float
├── str
├── bool (erbt von int!)
├── list
├── tuple
├── dict
├── set
├── frozenset
└── ... viele mehr
```

### Vererbung prüfen

```python
# bool erbt von int - überraschend aber wahr!
print(isinstance(True, int))   # True
print(isinstance(True, bool))  # True

# True verhält sich wie 1
print(True + True)  # 2
print(True * 10)    # 10

# False verhält sich wie 0
print(False + 5)  # 5
```

## Kategorien von Datentypen

Python teilt Typen in verschiedene Kategorien:

### 1. Numerische Typen

```python
# Integer (Ganzzahlen)
alter = 42
große_zahl = 999_999_999_999_999_999  # Beliebig groß!

# Float (Gleitkommazahlen)
pi = 3.14159
temperatur = -273.15

# Complex (Komplexe Zahlen)
z = 3 + 4j
print(z.real)  # 3.0
print(z.imag)  # 4.0

# Boolean (Wahrheitswerte)
ist_wahr = True
ist_falsch = False
```

### 2. Sequenzen (geordnete Collections)

```python
# String (Text)
text = "Hallo Welt"

# List (veränderbare Liste)
zahlen = [1, 2, 3, 4, 5]

# Tuple (unveränderbare Liste)
koordinaten = (10, 20)

# Range (Zahlenbereich)
bereich = range(0, 10)

# Bytes (Binärdaten)
daten = b"Hello"
```

### 3. Mappings (Key-Value Paare)

```python
# Dictionary
person = {
    "name": "Alice",
    "alter": 30,
    "stadt": "Berlin"
}
```

### 4. Sets (Mengen)

```python
# Set (veränderbare Menge)
farben = {"rot", "grün", "blau"}

# Frozenset (unveränderbare Menge)
primzahlen = frozenset([2, 3, 5, 7, 11])
```

### 5. None Type

```python
# None (repräsentiert "kein Wert")
ergebnis = None
```

## Built-in Types (Eingebaute Typen)

Python hat viele eingebaute Typen, die sofort verfügbar sind:

```python
# Typ prüfen mit type()
print(type(42))              # <class 'int'>
print(type(3.14))            # <class 'float'>
print(type("text"))          # <class 'str'>
print(type([1, 2]))          # <class 'list'>
print(type({"a": 1}))        # <class 'dict'>
print(type({1, 2}))          # <class 'set'>
print(type((1, 2)))          # <class 'tuple'>
print(type(True))            # <class 'bool'>
print(type(None))            # <class 'NoneType'>
```

### isinstance() vs type()

```python
# type() prüft den exakten Typ
x = 42
print(type(x) == int)  # True
print(type(x) == bool) # False (obwohl bool von int erbt)

# isinstance() berücksichtigt Vererbung (empfohlen!)
print(isinstance(x, int))   # True
print(isinstance(True, int)) # True (bool erbt von int)

# isinstance() mit mehreren Typen
print(isinstance(x, (int, float)))  # True (int ODER float)
```

> [!tip] Best Practice
> Nutze `isinstance()` statt `type() ==` für Typprüfungen!

## Type Hints (Optional)

Seit Python 3.5 kannst du **Type Hints** (Typ-Annotationen) hinzufügen. Diese ändern **nichts** am Verhalten, helfen aber:

- 🔍 IDEs beim Auto-Complete
- 🐛 Tools wie `mypy` finden Typfehler
- 📖 Code-Dokumentation

```python
# Ohne Type Hints
def addiere(a, b):
    return a + b

# Mit Type Hints
def addiere(a: int, b: int) -> int:
    return a + b

# Type Hints für Collections (Python 3.12+)
def verarbeite_liste(zahlen: list[int]) -> dict[str, int]:
    return {
        "summe": sum(zahlen),
        "anzahl": len(zahlen)
    }

# Union Types mit | (Python 3.10+)
def verarbeite(wert: int | str) -> str:
    return str(wert)
```

> [!note] Wichtig
> Type Hints sind **optional** und werden zur Laufzeit **nicht geprüft**! Sie sind reine Dokumentation.

## Duck Typing

Python folgt dem **Duck Typing** Prinzip:

> "Wenn es läuft wie eine Ente und quakt wie eine Ente, dann ist es eine Ente."

Das bedeutet: Der Typ ist weniger wichtig als **welche Operationen** möglich sind.

```python
# Duck Typing in Aktion
def verdopple(container):
    """Funktioniert mit allem, was + und * unterstützt"""
    return container + container

# Funktioniert mit verschiedenen Typen!
print(verdopple("Hallo"))      # HalloHallo
print(verdopple([1, 2]))       # [1, 2, 1, 2]
print(verdopple((1, 2)))       # (1, 2, 1, 2)

# Länge ermitteln - funktioniert mit vielen Typen
def zeige_länge(obj):
    """Funktioniert mit allem, was __len__ hat"""
    print(f"Länge: {len(obj)}")

zeige_länge("Python")      # Länge: 6
zeige_länge([1, 2, 3])     # Länge: 3
zeige_länge({"a": 1})      # Länge: 1
```

### Protokolle und Interfaces

Python nutzt **Protokolle** statt formaler Interfaces:

```python
# Ein Objekt ist "iterable", wenn es __iter__ hat
class MeineZahlen:
    def __init__(self):
        self.zahlen = [1, 2, 3]
    
    def __iter__(self):
        return iter(self.zahlen)

# Kann in for-Loop verwendet werden!
obj = MeineZahlen()
for zahl in obj:
    print(zahl)  # 1, 2, 3
```

## Typ-Konvertierung (Casting)

Du kannst Werte in andere Typen umwandeln:

```python
# String zu Integer
zahl = int("42")
print(zahl + 10)  # 52

# Integer zu String
text = str(42)
print(text + " ist die Antwort")  # 42 ist die Antwort

# String zu Float
preis = float("19.99")
print(preis * 1.19)  # 23.7881

# Liste zu Tuple
liste = [1, 2, 3]
tupel = tuple(liste)
print(tupel)  # (1, 2, 3)

# String zu Liste
text = "Hallo"
buchstaben = list(text)
print(buchstaben)  # ['H', 'a', 'l', 'l', 'o']

# Liste zu Set (entfernt Duplikate!)
zahlen = [1, 2, 2, 3, 3, 3]
eindeutig = set(zahlen)
print(eindeutig)  # {1, 2, 3}
```

### Fehler bei Konvertierung

```python
# ❌ Nicht alle Konvertierungen sind möglich
try:
    zahl = int("Hallo")  # ValueError!
except ValueError as e:
    print(f"Fehler: {e}")  # invalid literal for int() with base 10: 'Hallo'

# ✅ Prüfe vor Konvertierung
text = "42"
if text.isdigit():
    zahl = int(text)
    print(f"Erfolgreich: {zahl}")
```

## Memory Model Grundlagen

Python speichert **Objekte** im Heap und **Referenzen** in Variablen:

```python
# Objekte und Referenzen
a = [1, 2, 3]  # Erstellt list-Objekt im Heap
b = a          # b zeigt auf dasselbe Objekt!

b.append(4)    # Modifiziert das Objekt
print(a)       # [1, 2, 3, 4] - auch a ist betroffen!

# id() zeigt die Speicheradresse
print(id(a))   # z.B. 140234567890
print(id(b))   # Gleiche Adresse!
print(a is b)  # True - gleiche Objekte

# Neue Liste erstellen
c = [1, 2, 3]
print(a == c)  # True - gleicher Inhalt
print(a is c)  # False - verschiedene Objekte
```

### == vs is

```python
# == prüft Wert-Gleichheit
# is prüft Identität (gleiche Speicheradresse)

liste1 = [1, 2, 3]
liste2 = [1, 2, 3]
liste3 = liste1

print(liste1 == liste2)  # True (gleicher Wert)
print(liste1 is liste2)  # False (verschiedene Objekte)
print(liste1 is liste3)  # True (gleiches Objekt)

# Ausnahme: Kleine Integers und Strings werden gecacht!
a = 42
b = 42
print(a is b)  # True (Python cached kleine Integers)

x = 1000
y = 1000
print(x is y)  # False (große Zahlen werden nicht gecacht)
```

## Häufige Fallstricke

### Fallstrick 1: Typ-Annahmen

```python
# ❌ Gefährlich: Annahme über Typ
def addiere_zehn(x):
    return x + 10

# Funktioniert nicht mit Strings!
# print(addiere_zehn("5"))  # TypeError: can only concatenate str to str

# ✅ Besser: Typ prüfen oder Type Hints
def addiere_zehn(x: int) -> int:
    if not isinstance(x, int):
        raise TypeError(f"Erwartet int, bekam {type(x).__name__}")
    return x + 10

print(addiere_zehn(5))   # 15
# print(addiere_zehn("5"))  # TypeError mit klarer Meldung