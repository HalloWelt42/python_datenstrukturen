---
tags: [python, datenstrukturen, sets, mengen]
created: 2025-10-01
---

← [[03_Mappings_und_Sets/01_Dictionaries/08_Übungen Dictionaries|Übungen Dictionaries]] | [[INDEX|📑 Index]] | [[03_Mappings_und_Sets/02_Sets/02_Set Operationen|Set Operationen]] →

# Set Basics (set)

> [!tip] Lernziel
> Nach dieser Seite verstehst du Sets, ihre mathematischen Eigenschaften und kannst sie für Mengenoperationen und Duplikat-Entfernung einsetzen.

## Was ist ein Set?

Ein **Set** ist eine **ungeordnete** Collection von **eindeutigen** Elementen. Es ist die Python-Implementation der mathematischen Menge.

**Kernmerkmale:**
- **Ungeordnet**: Keine definierte Reihenfolge (keine Indizes)
- **Eindeutig**: Jedes Element nur einmal vorhanden
- **Mutable**: Kann verändert werden (Elemente hinzufügen/entfernen)
- **Nur hashbare Elemente**: Wie bei Dict-Keys
- **O(1) Membership**: Extrem schnelle Suche
- **Set-Operationen**: Union, Intersection, Difference, etc.

**Wofür verwendet:**
- Duplikate entfernen
- Membership-Tests (x in set)
- Mathematische Mengenoperationen
- Schnelle Lookups ohne Values
- Unique Collections

---

## Syntax und Grundlagen

### Set erstellen

```python
# Mit geschweiften Klammern (aber nicht leer!)
zahlen = {1, 2, 3, 4, 5}
print(zahlen)  # {1, 2, 3, 4, 5}
print(type(zahlen))  # <class 'set'>

# Mit set() Konstruktor
buchstaben = set("hello")
print(buchstaben)  # {'h', 'e', 'l', 'o'} - Duplikate entfernt!

# Aus Liste/Tuple
liste = [1, 2, 2, 3, 3, 3]
unique = set(liste)
print(unique)  # {1, 2, 3}

# Leeres Set (ACHTUNG: {} erstellt Dict!)
leer = set()  # ✅ Richtig
nicht_leer = {}  # ❌ Dies ist ein Dict!
print(type(leer))  # <class 'set'>
print(type(nicht_leer))  # <class 'dict'>

# Mit set() und Iterable
woerter = set(["apfel", "banane", "apfel", "kirsche"])
print(woerter)  # {'kirsche', 'apfel', 'banane'}

# Range zu Set
zahlen_range = set(range(5))
print(zahlen_range)  # {0, 1, 2, 3, 4}
```

### Eigenschaften eines Sets

```python
s = {3, 1, 4, 1, 5, 9, 2, 6}

# Automatische Duplikat-Entfernung
print(s)  # {1, 2, 3, 4, 5, 6, 9} - Keine doppelte 1!

# Ungeordnet - Reihenfolge kann variieren
# (seit Python 3.7+ ist Iteration-Order deterministisch,
#  aber nicht Teil der Spezifikation wie bei dict)

# Keine Indizes
try:
    print(s[0])  # TypeError!
except TypeError as e:
    print(f"Fehler: {e}")

# Länge
print(len(s))  # 7
```

### Elemente hinzufügen

```python
farben = {"rot", "blau", "grün"}

# Einzelnes Element mit add()
farben.add("gelb")
print(farben)  # {'rot', 'blau', 'grün', 'gelb'}

# Bestehendes Element hinzufügen (keine Wirkung)
farben.add("rot")
print(farben)  # Unverändert

# Mehrere Elemente mit update()
farben.update(["orange", "lila", "rosa"])
print(farben)
# {'rot', 'blau', 'grün', 'gelb', 'orange', 'lila', 'rosa'}

# update() mit mehreren Iterables
farben.update(["weiß"], ("schwarz", "grau"))
print(farben)
```

### Elemente entfernen

```python
zahlen = {1, 2, 3, 4, 5}

# Mit remove() - KeyError wenn nicht vorhanden
zahlen.remove(3)
print(zahlen)  # {1, 2, 4, 5}

try:
    zahlen.remove(10)  # KeyError!
except KeyError:
    print("Element nicht gefunden")

# Mit discard() - Kein Fehler wenn nicht vorhanden
zahlen.discard(4)
print(zahlen)  # {1, 2, 5}

zahlen.discard(100)  # Kein Fehler!
print(zahlen)  # Unverändert

# Mit pop() - Entfernt arbiträres Element
zahlen = {1, 2, 3, 4, 5}
element = zahlen.pop()
print(f"Entfernt: {element}")
print(zahlen)

# Bei leerem Set: KeyError
try:
    set().pop()
except KeyError:
    print("Set ist leer!")

# Alle Elemente löschen
zahlen.clear()
print(zahlen)  # set()
```

---

## Details und Interna

### Hash-Table Implementation

```python
"""
Sets sind intern wie Dictionaries implementiert:
- Hash-Table für O(1) Lookups
- Nur Keys, keine Values
- Deshalb: Nur hashbare Elemente erlaubt

Set = Dict ohne Values
"""

# Hashbare Typen (können in Set sein)
hashables = {
    42,                    # int
    3.14,                  # float
    "text",                # str
    (1, 2, 3),            # tuple
    frozenset([1, 2]),    # frozenset
    True,                  # bool
    None                   # None
}

print(hashables)

# Unhashbare Typen (NICHT in Set erlaubt)
try:
    s = {[1, 2, 3]}  # list
except TypeError as e:
    print(f"Liste: {e}")

try:
    s = {{1, 2, 3}}  # set
except TypeError as e:
    print(f"Set: {e}")

try:
    s = {{"key": "value"}}  # dict
except TypeError as e:
    print(f"Dict: {e}")
```

### Memory Layout

```python
import sys

# Sets sind speichereffizient
small_set = {1, 2, 3}
large_set = set(range(1000))

print(f"3 Elemente:    {sys.getsizeof(small_set)} bytes")
print(f"1000 Elemente: {sys.getsizeof(large_set)} bytes")

# Vergleich mit Liste
small_list = [1, 2, 3]
large_list = list(range(1000))

print(f"\n3 Elemente (Liste):    {sys.getsizeof(small_list)} bytes")
print(f"1000 Elemente (Liste): {sys.getsizeof(large_list)} bytes")

# Set braucht mehr Speicher als Liste
# Aber: O(1) Lookup statt O(n)!
```

### Performance-Charakteristiken

```python
# Zeitkomplexität

"""
Operation          | Average | Worst Case
-------------------|---------|------------
x in s             | O(1)    | O(n)
s.add(x)           | O(1)    | O(n)
s.remove(x)        | O(1)    | O(n)
s.discard(x)       | O(1)    | O(n)
s.pop()            | O(1)    | O(1)
len(s)             | O(1)    | O(1)
s.clear()          | O(1)    | O(1)
s.copy()           | O(n)    | O(n)
for x in s         | O(n)    | O(n)

Worst Case nur bei vielen Hash-Kollisionen (selten)
"""

# Membership-Test: Set vs. Liste
import timeit

data = list(range(10000))
s = set(data)
l = list(data)

# Set: O(1)
def set_test():
    return 9999 in s

# Liste: O(n)
def list_test():
    return 9999 in l

set_time = timeit.timeit(set_test, number=100000)
list_time = timeit.timeit(list_test, number=100000)

print(f"Set:   {set_time:.4f}s")
print(f"Liste: {list_time:.4f}s")
print(f"Set ist {list_time/set_time:.0f}x schneller!")
```

---

## Praktische Anwendungen

### 1. Duplikate entfernen

```python
# Duplikate aus Liste entfernen
zahlen = [1, 2, 2, 3, 3, 3, 4, 4, 4, 4]
unique = list(set(zahlen))
print(unique)  # [1, 2, 3, 4]

# ⚠️ Reihenfolge geht verloren!
# Wenn Reihenfolge wichtig: dict.fromkeys()
zahlen = [3, 1, 2, 1, 3, 2]
unique_ordered = list(dict.fromkeys(zahlen))
print(unique_ordered)  # [3, 1, 2]

# Duplikate in String
text = "mississippi"
unique_chars = set(text)
print(unique_chars)  # {'m', 'i', 's', 'p'}
print(''.join(sorted(unique_chars)))  # imps
```

### 2. Membership-Tests

```python
# Schnelle Lookup-Table
aktive_user_ids = {123, 456, 789, 1011, 1213}

# O(1) Check
user_id = 456
if user_id in aktive_user_ids:
    print("User ist aktiv")

# Viel schneller als Liste bei vielen IDs!
# Liste wäre O(n), Set ist O(1)

# Blacklist Check
verbotene_woerter = {
    "spam", "scam", "fake", "fraud"
}

text = "This is not spam"
woerter = text.lower().split()

if any(wort in verbotene_woerter for wort in woerter):
    print("Verbotenes Wort gefunden!")
```

### 3. Unique Werte sammeln

```python
# Alle unique Werte in einer Datenstruktur
daten = [
    {"name": "Max", "stadt": "Berlin"},
    {"name": "Anna", "stadt": "München"},
    {"name": "Tom", "stadt": "Berlin"},
    {"name": "Lisa", "stadt": "Hamburg"}
]

# Alle Städte (unique)
staedte = {person["stadt"] for person in daten}
print(staedte)  # {'Berlin', 'München', 'Hamburg'}

# Anzahl unique Städte
print(f"Anzahl Städte: {len(staedte)}")  # 3
```

### 4. Schnelle Datenvalidierung

```python
# Erlaubte Werte prüfen
ERLAUBTE_STATUS = {"pending", "active", "inactive", "suspended"}

def validiere_status(status):
    if status not in ERLAUBTE_STATUS:
        raise ValueError(f"Ungültiger Status: {status}")
    return True

# Schnelle Validierung
try:
    validiere_status("active")    # ✅ OK
    validiere_status("deleted")   # ❌ Error
except ValueError as e:
    print(e)

# Mehrere Werte validieren
benutzer_rollen = {"admin", "user", "moderator"}
erlaubte_rollen = {"admin", "user", "guest", "moderator"}

if benutzer_rollen <= erlaubte_rollen:  # Subset-Check
    print("Alle Rollen sind erlaubt")
else:
    ungueltig = benutzer_rollen - erlaubte_rollen
    print(f"Ungültige Rollen: {ungueltig}")
```

### 5. Daten-Bereinigung

```python
# Doppelte Einträge in Datenbank finden
alle_emails = [
    "max@example.com",
    "anna@example.com",
    "max@example.com",  # Duplikat!
    "tom@example.com",
    "anna@example.com"  # Duplikat!
]

# Duplikate finden
gesehen = set()
duplikate = set()

for email in alle_emails:
    if email in gesehen:
        duplikate.add(email)
    else:
        gesehen.add(email)

print(f"Duplikate: {duplikate}")
# {'max@example.com', 'anna@example.com'}

# Alternative mit Counter
from collections import Counter
zaehler = Counter(alle_emails)
duplikate = {email for email, count in zaehler.items() if count > 1}
print(f"Duplikate: {duplikate}")
```

### 6. Tags und Kategorien

```python
# Artikel-Tags
artikel1_tags = {"python", "tutorial", "programming", "beginner"}
artikel2_tags = {"python", "advanced", "programming", "expert"}
artikel3_tags = {"javascript", "tutorial", "web", "beginner"}

# Alle verwendeten Tags
alle_tags = artikel1_tags | artikel2_tags | artikel3_tags
print(f"Alle Tags: {alle_tags}")

# Gemeinsame Tags
gemeinsam_1_2 = artikel1_tags & artikel2_tags
print(f"Gemeinsam 1&2: {gemeinsam_1_2}")  # {'python', 'programming'}

# Ähnliche Artikel finden (basierend auf gemeinsamen Tags)
def aehnlichkeit(tags1, tags2):
    gemeinsam = len(tags1 & tags2)
    gesamt = len(tags1 | tags2)
    return gemeinsam / gesamt if gesamt > 0 else 0

print(f"Ähnlichkeit 1-2: {aehnlichkeit(artikel1_tags, artikel2_tags):.2%}")
print(f"Ähnlichkeit 1-3: {aehnlichkeit(artikel1_tags, artikel3_tags):.2%}")
```

---

## Iteration über Sets

### Grundlegende Iteration

```python
farben = {"rot", "grün", "blau", "gelb"}

# Direkt über Set
for farbe in farben:
    print(farbe)

# Mit enumerate() für Index
for i, farbe in enumerate(farben):
    print(f"{i}: {farbe}")

# ⚠️ Reihenfolge ist nicht garantiert!
# (auch wenn deterministisch seit Python 3.7+)
```

### Sortierte Iteration

```python
zahlen = {5, 2, 8, 1, 9, 3}

# Sortiert iterieren
for zahl in sorted(zahlen):
    print(zahl)  # 1, 2, 3, 5, 8, 9

# Rückwärts sortiert
for zahl in sorted(zahlen, reverse=True):
    print(zahl)  # 9, 8, 5, 3, 2, 1

# Strings sortieren
woerter = {"Zebra", "Apfel", "Banane"}
for wort in sorted(woerter):
    print(wort)  # Apfel, Banane, Zebra
```

### Filtern während Iteration

```python
zahlen = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10}

# Nur gerade Zahlen
gerade = {x for x in zahlen if x % 2 == 0}
print(gerade)  # {2, 4, 6, 8, 10}

# Mit Bedingung und Transformation
quadrate = {x**2 for x in zahlen if x > 5}
print(quadrate)  # {36, 49, 64, 81, 100}
```

---

## Häufige Fallstricke

### ⚠️ Fallstrick 1: {} erstellt Dict, nicht Set!

```python
# ❌ FALSCH - Dies ist ein Dict!
leer = {}
print(type(leer))  # <class 'dict'>

# ✅ RICHTIG - Leeres Set
leer = set()
print(type(leer))  # <class 'set'>

# Mit Elementen ist {} ein Set
nicht_leer = {1, 2, 3}
print(type(nicht_leer))  # <class 'set'>
```

### ⚠️ Fallstrick 2: Unhashbare Elemente

```python
# ❌ FALSCH - Liste ist nicht hashbar
try:
    s = {[1, 2, 3]}
except TypeError as e:
    print(f"Fehler: {e}")

# ✅ RICHTIG - Tuple stattdessen
s = {(1, 2, 3)}
print(s)  # {(1, 2, 3)}

# Koordinaten als Set
koordinaten = {(0, 0), (1, 0), (0, 1), (1, 1)}
print(koordinaten)
```

### ⚠️ Fallstrick 3: Set während Iteration ändern

```python
zahlen = {1, 2, 3, 4, 5}

# ❌ FALSCH - RuntimeError!
try:
    for zahl in zahlen:
        if zahl % 2 == 0:
            zahlen.remove(zahl)
except RuntimeError as e:
    print("Set changed size during iteration")

# ✅ RICHTIG - Kopie erstellen
zahlen = {1, 2, 3, 4, 5}
for zahl in zahlen.copy():  # Oder list(zahlen)
    if zahl % 2 == 0:
        zahlen.remove(zahl)

print(zahlen)  # {1, 3, 5}

# ODER: Set Comprehension
zahlen = {1, 2, 3, 4, 5}
ungerade = {x for x in zahlen if x % 2 != 0}
print(ungerade)  # {1, 3, 5}
```

### ⚠️ Fallstrick 4: Reihenfolge ist nicht garantiert

```python
# Sets haben keine definierte Reihenfolge
s = {3, 1, 4, 1, 5, 9, 2, 6}

# Iteration-Reihenfolge kann sich ändern
# (auch wenn deterministisch in CPython 3.7+)
print(list(s))  # Könnte sein: [1, 2, 3, 4, 5, 6, 9]

# ❌ FALSCH - Auf Reihenfolge verlassen
# erste = list(s)[0]  # Nicht verlässlich!

# ✅ RICHTIG - Wenn Reihenfolge wichtig: Liste verwenden
# Oder sortieren:
erste = min(s)  # Kleinste Element
letzte = max(s)  # Größte Element
```

---

## Set vs. Liste vs. Tuple

### Wann welche Datenstruktur?

```python
# LIST: Geordnet, veränderbar, Duplikate erlaubt
einkaufsliste = ["Apfel", "Brot", "Apfel", "Milch"]
# - Reihenfolge wichtig
# - Duplikate gewollt
# - Index-Zugriff nötig

# SET: Ungeordnet, veränderbar, keine Duplikate
unique_produkte = {"Apfel", "Brot", "Milch"}
# - Nur Membership wichtig
# - Keine Duplikate
# - Schnelle Lookups

# TUPLE: Geordnet, unveränderbar, Duplikate erlaubt
koordinaten = (10, 20)
# - Immutable
# - Als Dict-Key verwendbar
# - Fixed Struktur

# Konvertierung
liste = [1, 2, 2, 3, 3, 3]
als_set = set(liste)       # {1, 2, 3}
als_tuple = tuple(als_set)  # (1, 2, 3)
zurueck = list(als_tuple)   # [1, 2, 3]
```

### Performance-Vergleich

```python
import timeit

data = list(range(1000))

# Erstellung
print("Erstellung (1000 Elemente):")
print("  List:", timeit.timeit("list(range(1000))", number=10000))
print("  Set: ", timeit.timeit("set(range(1000))", number=10000))

# Membership
s = set(data)
l = data

print("\nMembership (999 in ...):")
print("  List:", timeit.timeit("999 in l", globals=globals(), number=100000))
print("  Set: ", timeit.timeit("999 in s", globals=globals(), number=100000))

# Set ist bei Membership deutlich schneller!
```

---

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [Set Types — set, frozenset](https://docs.python.org/3/library/stdtypes.html#set-types-set-frozenset) - Vollständige Set-Referenz
- [Set Objects](https://docs.python.org/3/library/stdtypes.html#set) - Detaillierte Dokumentation
- [Built-in Types](https://docs.python.org/3/library/stdtypes.html) - Alle eingebauten Typen

---

## Verwandte Themen

- [[03_Mappings_und_Sets/02_Sets/02_Set Operationen|Set Operationen]] - Union, Intersection, Difference
- [[03_Mappings_und_Sets/02_Sets/03_Frozenset|Frozenset]] - Unveränderbare Sets
- [[03_Mappings_und_Sets/01_Dictionaries/01_Dict Basics|Dict Basics]] - Verwandte Datenstruktur
- [[03_Mappings_und_Sets/02_Sets/04_Set Comprehensions|Set Comprehensions]] - Elegante Set-Erzeugung

---

← [[03_Mappings_und_Sets/01_Dictionaries/08_Übungen Dictionaries|Übungen Dictionaries]] | [[INDEX|📑 Index]] | [[03_Mappings_und_Sets/02_Sets/02_Set Operationen|Set Operationen]] →