---
tags: [python, datenstrukturen, mutability, immutability, grundlagen]
created: 2025-10-01
---

← [[01_Python Typ-System|Python Typ-System]] | [[INDEX|📑 Index]] | [[03_Memory Management Basics|Memory Management Basics]] →

# Mutability vs Immutability

> [!tip] Lernziel
> Du verstehst den Unterschied zwischen veränderbaren (mutable) und unveränderbaren (immutable) Objekten und kennst die Auswirkungen auf deinen Code

## Was ist Mutability?

**Mutability** (Veränderbarkeit) beschreibt, ob ein Objekt nach seiner Erstellung modifiziert werden kann:

- **Mutable** (veränderbar): Objekt kann nach Erstellung geändert werden
- **Immutable** (unveränderbar): Objekt kann nach Erstellung NICHT geändert werden

```python
# MUTABLE: Liste kann geändert werden
liste = [1, 2, 3]
liste.append(4)      # Modifiziert die Liste
liste[0] = 99        # Ändert erstes Element
print(liste)         # [99, 2, 3, 4]

# IMMUTABLE: Tuple kann NICHT geändert werden
tupel = (1, 2, 3)
# tupel.append(4)    # ❌ AttributeError: 'tuple' object has no attribute 'append'
# tupel[0] = 99      # ❌ TypeError: 'tuple' object does not support item assignment
```

## Übersicht: Mutable vs Immutable Types

### Immutable Types (Unveränderbar)

| Typ | Beispiel | Warum immutable? |
|-----|----------|------------------|
| `int` | `42` | Zahlen ändern sich nicht |
| `float` | `3.14` | Zahlen ändern sich nicht |
| `complex` | `3+4j` | Zahlen ändern sich nicht |
| `bool` | `True` | Nur zwei Werte möglich |
| `str` | `"Hallo"` | Strings sind immer unveränderbar |
| `tuple` | `(1, 2, 3)` | Feste Sequenz |
| `frozenset` | `frozenset([1, 2])` | Unveränderbare Menge |
| `bytes` | `b"data"` | Unveränderbare Binärdaten |
| `NoneType` | `None` | Singleton-Wert |

### Mutable Types (Veränderbar)

| Typ | Beispiel | Was kann geändert werden? |
|-----|----------|---------------------------|
| `list` | `[1, 2, 3]` | Elemente können hinzugefügt, gelöscht, geändert werden |
| `dict` | `{"a": 1}` | Keys und Values können geändert werden |
| `set` | `{1, 2, 3}` | Elemente können hinzugefügt, gelöscht werden |
| `bytearray` | `bytearray(b"data")` | Bytes können geändert werden |
| eigene Klassen | `MeinObjekt()` | Attribute können geändert werden |

## Immutable Objects im Detail

### Strings sind immutable

```python
# String scheint sich zu ändern - tut er aber nicht!
text = "Hallo"
print(id(text))        # z.B. 140234567890

text = text + " Welt"  # Erstellt NEUES String-Objekt!
print(id(text))        # Andere Adresse! Neues Objekt!

# String-Methoden geben neue Strings zurück
original = "python"
groß = original.upper()  # Neuer String
print(original)          # python (unverändert!)
print(groß)              # PYTHON

# Ersetzen erstellt neuen String
text = "Hallo Welt"
neu = text.replace("Welt", "Python")
print(text)  # Hallo Welt (unverändert!)
print(neu)   # Hallo Python (neues Objekt)
```

### Integers sind immutable

```python
# Integer scheint sich zu ändern - tut er aber nicht!
x = 42
print(id(x))  # z.B. 140234567890

x = x + 1     # Erstellt neues int-Objekt!
print(id(x))  # Andere Adresse!

# += erstellt auch neues Objekt
y = 100
alte_id = id(y)
y += 5
neue_id = id(y)
print(alte_id != neue_id)  # True - verschiedene Objekte!
```

### Tuples sind immutable

```python
# Tuple kann nicht geändert werden
koordinaten = (10, 20)
# koordinaten[0] = 15  # ❌ TypeError!

# Aber: Neues Tuple mit geänderten Werten erstellen
neue_koordinaten = (15, koordinaten[1])
print(neue_koordinaten)  # (15, 20)

# Tuple Unpacking und Neuzuweisung
x, y = koordinaten
koordinaten = (x + 5, y + 5)  # Neues Tuple!
print(koordinaten)  # (15, 25)
```

## Mutable Objects im Detail

### Listen sind mutable

```python
# Liste wird tatsächlich modifiziert
zahlen = [1, 2, 3]
alte_id = id(zahlen)

zahlen.append(4)      # Modifiziert das Objekt
zahlen[0] = 99       # Ändert Element
zahlen.extend([5, 6]) # Fügt Elemente hinzu

neue_id = id(zahlen)
print(alte_id == neue_id)  # True - gleiches Objekt!
print(zahlen)              # [99, 2, 3, 4, 5, 6]
```

### Dictionaries sind mutable

```python
# Dictionary wird modifiziert
person = {"name": "Alice", "alter": 30}
alte_id = id(person)

person["stadt"] = "Berlin"      # Fügt Key hinzu
person["alter"] = 31            # Ändert Wert
del person["name"]              # Löscht Key

neue_id = id(person)
print(alte_id == neue_id)  # True - gleiches Objekt!
print(person)              # {'alter': 31, 'stadt': 'Berlin'}
```

### Sets sind mutable

```python
# Set wird modifiziert
farben = {"rot", "grün"}
alte_id = id(farben)

farben.add("blau")        # Fügt Element hinzu
farben.remove("rot")      # Entfernt Element

neue_id = id(farben)
print(alte_id == neue_id)  # True - gleiches Objekt!
print(farben)              # {'grün', 'blau'}
```

## Der große Unterschied

### Auswirkung auf Variablen-Zuweisung

```python
# IMMUTABLE: Kopie bei Zuweisung
x = "Hallo"
y = x
y = y + " Welt"  # Erstellt neues Objekt
print(x)         # Hallo (unverändert!)
print(y)         # Hallo Welt

# MUTABLE: Referenz bei Zuweisung
liste1 = [1, 2, 3]
liste2 = liste1        # Beide zeigen auf GLEICHES Objekt!
liste2.append(4)       # Modifiziert das Objekt
print(liste1)          # [1, 2, 3, 4] - auch liste1 betroffen!
print(liste2)          # [1, 2, 3, 4]
print(liste1 is liste2)  # True - gleiches Objekt
```

### Kopieren von mutable Objects

```python
# Flache Kopie (shallow copy)
original = [1, 2, 3]
kopie = original.copy()     # oder original[:]
kopie.append(4)

print(original)  # [1, 2, 3] (unverändert!)
print(kopie)     # [1, 2, 3, 4]
print(original is kopie)  # False - verschiedene Objekte

# Problem bei verschachtelten Strukturen!
matrix = [[1, 2], [3, 4]]
flache_kopie = matrix.copy()
flache_kopie[0].append(99)  # Modifiziert innere Liste!

print(matrix)        # [[1, 2, 99], [3, 4]] - auch betroffen!
print(flache_kopie)  # [[1, 2, 99], [3, 4]]

# Tiefe Kopie (deep copy) für verschachtelte Strukturen
import copy
tiefe_kopie = copy.deepcopy(matrix)
tiefe_kopie[0].append(77)

print(matrix)       # [[1, 2, 99], [3, 4]] (unverändert!)
print(tiefe_kopie)  # [[1, 2, 99, 77], [3, 4]]
```

## Häufige Fallstricke

### Fallstrick 1: Default Arguments

```python
# ❌ KLASSISCHER FEHLER: Mutable Default Argument
def füge_element_hinzu(element, liste=[]):
    liste.append(element)
    return liste

# WAS ERWARTET: Jedes Mal neue Liste
# WAS PASSIERT: Gleiche Liste wird wiederverwendet!
print(füge_element_hinzu(1))  # [1]
print(füge_element_hinzu(2))  # [1, 2] - sollte [2] sein!
print(füge_element_hinzu(3))  # [1, 2, 3] - sollte [3] sein!

# ✅ RICHTIG: None als Default, dann neue Liste erstellen
def füge_element_hinzu_richtig(element, liste=None):
    if liste is None:
        liste = []
    liste.append(element)
    return liste

print(füge_element_hinzu_richtig(1))  # [1]
print(füge_element_hinzu_richtig(2))  # [2] - korrekt!
print(füge_element_hinzu_richtig(3))  # [3] - korrekt!
```

### Fallstrick 2: Tuple mit mutable Elementen

```python
# Tuple selbst ist immutable, aber Inhalt kann mutable sein!
liste_in_tuple = ([1, 2, 3], [4, 5, 6])

# Tuple kann nicht geändert werden
# liste_in_tuple[0] = [7, 8, 9]  # ❌ TypeError!

# Aber: Die Listen IM Tuple können geändert werden!
liste_in_tuple[0].append(99)  # ✅ Funktioniert!
print(liste_in_tuple)  # ([1, 2, 3, 99], [4, 5, 6])

# Das Tuple ist immutable, der Inhalt nicht!
```

### Fallstrick 3: Funktionsargumente

```python
# Mutable Objects als Argumente können verändert werden!
def modifiziere_liste(liste):
    liste.append(999)  # Modifiziert die Original-Liste!

meine_liste = [1, 2, 3]
modifiziere_liste(meine_liste)
print(meine_liste)  # [1, 2, 3, 999] - wurde verändert!

# ✅ Besser: Explizit eine Kopie erstellen
def modifiziere_liste_sicher(liste):
    neue_liste = liste.copy()  # Arbeite mit Kopie
    neue_liste.append(999)
    return neue_liste

meine_liste = [1, 2, 3]
ergebnis = modifiziere_liste_sicher(meine_liste)
print(meine_liste)  # [1, 2, 3] (unverändert!)
print(ergebnis)     # [1, 2, 3, 999]
```

### Fallstrick 4: Iteration und Modifikation

```python
# ❌ FEHLER: Liste während Iteration modifizieren
zahlen = [1, 2, 3, 4, 5]
for zahl in zahlen:
    if zahl % 2 == 0:
        zahlen.remove(zahl)  # Verändert Liste während Iteration!
print(zahlen)  # [1, 3, 4, 5] - 4 wurde nicht entfernt!

# ✅ RICHTIG: Über Kopie iterieren
zahlen = [1, 2, 3, 4, 5]
for zahl in zahlen[:]:  # Iteriere über Kopie!
    if zahl % 2 == 0:
        zahlen.remove(zahl)
print(zahlen)  # [1, 3, 5] - korrekt!

# ✅ NOCH BESSER: List Comprehension
zahlen = [1, 2, 3, 4, 5]
zahlen = [z for z in zahlen if z % 2 != 0]
print(zahlen)  # [1, 3, 5]
```

## Performance-Hinweise

### Immutable Objects

**Vorteile:**
- ✅ Thread-safe (können sicher zwischen Threads geteilt werden)
- ✅ Können als Dictionary Keys verwendet werden
- ✅ Können gehasht werden (in Sets verwendbar)
- ✅ Keine unerwarteten Seiteneffekte

**Nachteile:**
- ⚠️ Jede "Änderung" erstellt neues Objekt (Memory-Overhead)
- ⚠️ String-Konkatenation in Loops ist langsam

```python
# ❌ Langsam: Viele String-Objekte werden erstellt
ergebnis = ""
for i in range(10000):
    ergebnis += str(i)  # Erstellt jedes Mal neuen String!

# ✅ Schnell: Liste sammeln, dann joinen
teile = []
for i in range(10000):
    teile.append(str(i))
ergebnis = "".join(teile)  # Ein String am Ende
```

### Mutable Objects

**Vorteile:**
- ✅ Effiziente In-Place Modifikation
- ✅ Kein Memory-Overhead bei Änderungen
- ✅ Schnell für viele Operationen

**Nachteile:**
- ⚠️ Nicht thread-safe
- ⚠️ Können nicht als Dictionary Keys verwendet werden
- ⚠️ Mögliche unerwartete Seiteneffekte

```python
# Listen sind effizient für viele Operationen
zahlen = []
for i in range(10000):
    zahlen.append(i)  # Effiziente In-Place Operation

# Zeitkomplexität:
# append():  O(1) amortisiert
# insert(0): O(n)
# pop():     O(1)
# pop(0):    O(n)
```

## Praktische Anwendungen

### Anwendung 1: Sichere Funktionen schreiben

```python
def verarbeite_daten(daten: list[int], multiplier: int = 2) -> list[int]:
    """
    Verarbeitet Daten OHNE Original zu verändern
    """
    # Arbeite mit Kopie
    ergebnis = daten.copy()
    for i in range(len(ergebnis)):
        ergebnis[i] *= multiplier
    return ergebnis

original = [1, 2, 3, 4, 5]
verarbeitet = verarbeite_daten(original)

print(original)     # [1, 2, 3, 4, 5] (unverändert!)
print(verarbeitet)  # [2, 4, 6, 8, 10]
```

### Anwendung 2: Immutable Configuration

```python
# Konfiguration als Tuple (unveränderbar)
CONFIG = (
    ("host", "localhost"),
    ("port", 8080),
    ("debug", True)
)

# Als Dictionary (veränderbar, aber sollte nicht geändert werden)
# Nutze frozenset für Keys wenn nötig
CONFIG_DICT = dict(CONFIG)

# Sichere Config mit namedtuple (später mehr dazu)
from collections import namedtuple

Config = namedtuple("Config", ["host", "port", "debug"])
config = Config(host="localhost", port=8080, debug=True)

# config.port = 9000  # ❌ AttributeError - immutable!
print(config.host)   # localhost
```

### Anwendung 3: Caching mit immutable Keys

```python
# Dictionary Keys müssen hashable (und damit immutable) sein
cache = {}

# ✅ Immutable als Key
key1 = ("user", 123)  # Tuple als Key
cache[key1] = {"name": "Alice", "score": 100}

# ❌ Mutable als Key geht nicht
# key2 = ["user", 123]  # Liste als Key
# cache[key2] = {...}   # TypeError: unhashable type: 'list'

# Frozenset für Set-Keys
key3 = frozenset(["tag1", "tag2", "tag3"])
cache[key3] = "Daten für diese Tags"

print(cache[key1])  # {'name': 'Alice', 'score': 100}
print(cache[key3])  # Daten für diese Tags
```

### Anwendung 4: Effiziente String-Verarbeitung

```python
# ❌ Ineffizient: String-Konkatenation in Loop
def baue_html_langsam(elemente):
    html = "<ul>"
    for element in elemente:
        html += f"<li>{element}</li>"  # Jedes Mal neuer String!
    html += "</ul>"
    return html

# ✅ Effizient: Liste sammeln, dann joinen
def baue_html_schnell(elemente):
    teile = ["<ul>"]
    for element in elemente:
        teile.append(f"<li>{element}</li>")
    teile.append("</ul>")
    return "".join(teile)

# Performance-Test
import time

daten = ["Element" + str(i) for i in range(1000)]

start = time.perf_counter()
baue_html_langsam(daten)
print(f"Langsam: {time.perf_counter() - start:.4f}s")

start = time.perf_counter()
baue_html_schnell(daten)
print(f"Schnell: {time.perf_counter() - start:.4f}s")
# Schnell ist 10-100x schneller!
```

## Übungsaufgaben

### Übung 1: Typ-Klassifikation (Leicht)

Erstelle eine Funktion `ist_mutable(obj)`, die prüft ob ein Objekt mutable ist.

**Erwarteter Output:**
```python
print(ist_mutable([1, 2, 3]))      # True
print(ist_mutable((1, 2, 3)))      # False
print(ist_mutable("text"))         # False
print(ist_mutable({"a": 1}))       # True
print(ist_mutable({1, 2, 3}))      # True
print(ist_mutable(frozenset([1]))) # False
print(ist_mutable(42))             # False
```

### Übung 2: Sichere Listenmodifikation (Mittel)

Schreibe eine Funktion `verdopple_gerade(zahlen)`, die eine Liste mit allen geraden Zahlen verdoppelt zurückgibt, OHNE die Original-Liste zu verändern.

**Erwarteter Output:**
```python
original = [1, 2, 3, 4, 5, 6]
ergebnis = verdopple_gerade(original)

print(original)  # [1, 2, 3, 4, 5, 6] (unverändert!)
print(ergebnis)  # [2, 4, 6, 8, 10, 12]
```

### Übung 3: Tiefe Kopie testen (Mittel)

Erstelle eine verschachtelte Datenstruktur und demonstriere den Unterschied zwischen flacher und tiefer Kopie.

**Erwarteter Output:**
```python
original = {
    "name": "Alice",
    "scores": [85, 90, 92],
    "metadata": {"level": 5}
}

flache_kopie = ...  # Dein Code
tiefe_kopie = ...   # Dein Code

# Modifiziere die Kopien
flache_kopie["scores"].append(95)
tiefe_kopie["scores"].append(98)

print(original["scores"])      # [85, 90, 92, 95] (von flacher Kopie betroffen!)
print(flache_kopie["scores"])  # [85, 90, 92, 95]
print(tiefe_kopie["scores"])   # [85, 90, 92, 95, 98] (unabhängig)
```

### Übung 4: Immutable Dictionary (Schwer)

Erstelle eine Klasse `ImmutableDict`, die sich wie ein Dictionary verhält, aber nach Erstellung nicht mehr geändert werden kann.

**Erwarteter Output:**
```python
d = ImmutableDict({"a": 1, "b": 2})
print(d["a"])              # 1
print("a" in d)            # True
print(len(d))              # 2

# Alle Änderungs-Versuche sollten Fehler werfen:
# d["c"] = 3               # Error!
# d["a"] = 99              # Error!
# del d["a"]               # Error!
```

### Übung 5: String-Builder (Schwer)

Implementiere eine `StringBuilder` Klasse, die effiziente String-Konkatenation ermöglicht.

**Anforderungen:**
- `append(text)` - Fügt Text hinzu
- `__str__()` - Gibt finalen String zurück
- Intern mit Liste arbeiten, nur bei `__str__()` joinen

**Erwarteter Output:**
```python
sb = StringBuilder()
sb.append("Hallo ")
sb.append("Welt")
sb.append("!")

print(str(sb))  # Hallo Welt!

# Sollte viel schneller sein als String-Konkatenation bei vielen Operationen
```

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [Data Model - Immutable Sequences](https://docs.python.org/3/reference/datamodel.html#the-standard-type-hierarchy) - Details zur Typ-Hierarchie
- [copy Module](https://docs.python.org/3/library/copy.html) - Shallow und Deep Copy
- [Common Gotchas - Mutable Default Arguments](https://docs.python-guide.org/writing/gotchas/) - Häufige Fallstricke
- [Hashable Objects](https://docs.python.org/3/glossary.html#term-hashable) - Was bedeutet hashable?

## Zusammenfassung

### Wichtigste Regeln

1. **Immutable Types:**
   - int, float, str, tuple, frozenset, bytes
   - Können nicht geändert werden
   - Sicher als Dictionary Keys
   - Thread-safe

2. **Mutable Types:**
   - list, dict, set, bytearray
   - Können geändert werden
   - Vorsicht bei Funktionsargumenten!
   - Nicht als Dictionary Keys

3. **Best Practices:**
   - ✅ Nutze `None` statt mutable defaults
   - ✅ Kopiere Listen wenn nötig
   - ✅ Beachte flache vs. tiefe Kopie
   - ✅ Iteriere über Kopie bei Modifikation
   - ✅ Nutze `.copy()` oder `copy.deepcopy()`

4. **Performance:**
   - Immutable: Teuer bei vielen Änderungen
   - Mutable: Effizient für In-Place Ops
   - String-Konkatenation: Nutze `join()`!

## Verwandte Themen

- [[03_Memory Management Basics|Memory Management]] - Wie Python Objekte verwaltet
- [[01_Python Typ-System|Python Typ-System]] - Typ-Hierarchie und Vererbung
- [[02_Sequenzen/01_Listen/01_List Basics|Listen]] - Mutable Sequenzen
- [[02_Sequenzen/02_Tupel/01_Tuple Basics|Tupel]] - Immutable Sequenzen
- [[03_Mappings_und_Sets/01_Dictionaries/01_Dict Basics|Dictionaries]] - Mutable Mappings

---

← [[01_Python Typ-System|Python Typ-System]] | [[INDEX|📑 Index]] | [[03_Memory Management Basics|Memory Management Basics]] →