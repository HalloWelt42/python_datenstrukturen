---
tags: [python, datenstrukturen, mappings, dictionaries, dict]
created: 2025-10-01
---

← [[02_Sequenzen/05_Range/02_Übungen Range|Übungen Range]] | [[INDEX|📑 Index]] | [[03_Mappings_und_Sets/01_Dictionaries/02_Dict Methoden|Dict Methoden]] →

# Dictionary Basics (dict)

> [!tip] Lernziel
> Nach dieser Seite verstehst du, was Dictionaries sind, wie sie intern funktionieren, und kannst sie sicher für Key-Value-Mappings einsetzen.

## Was ist ein Dictionary?

Ein **Dictionary** (dict) ist eine **veränderbare, ungeordnete** Collection von **Key-Value-Paaren**. Es ist eine der wichtigsten und am häufigsten verwendeten Datenstrukturen in Python.

**Kernmerkmale:**
- **Mapping-Typ**: Ordnet Keys eindeutig Values zu
- **Mutable**: Kann nach Erstellung verändert werden
- **Keys müssen hashable sein**: Immutable Typen wie str, int, tuple
- **Values können beliebig sein**: Jeder Python-Typ
- **Seit Python 3.7+**: Insertion-Order wird beibehalten
- **O(1) Lookup**: Extrem schneller Zugriff über Keys

**Wofür verwendet:**
- Daten mit benannten Feldern speichern
- Schnelle Lookups (wie eine Datenbank im Speicher)
- Zählungen und Gruppierungen
- Konfigurationen und Settings
- JSON-ähnliche Datenstrukturen
- Caching und Memoization

---

## Syntax und Grundlagen

### Dictionary erstellen

```python
# Leeres Dictionary
leer = {}
auch_leer = dict()

# Mit Literal-Syntax (am häufigsten)
person = {
    "name": "Max",
    "alter": 30,
    "stadt": "Berlin"
}

# Mit dict() Konstruktor
person2 = dict(name="Anna", alter=25, stadt="München")

# Aus Sequenzen von Paaren
paare = [("a", 1), ("b", 2), ("c", 3)]
buchstaben = dict(paare)
print(buchstaben)  # {'a': 1, 'b': 2, 'c': 3}

# Mit dict comprehension (später mehr dazu)
quadrate = {x: x**2 for x in range(5)}
print(quadrate)  # {0: 0, 1: 1, 2: 4, 3: 9, 4: 16}

# Aus zwei Listen mit zip()
keys = ["name", "alter", "beruf"]
values = ["Lisa", 28, "Entwicklerin"]
profil = dict(zip(keys, values))
print(profil)  # {'name': 'Lisa', 'alter': 28, 'beruf': 'Entwicklerin'}
```

### Zugriff auf Werte

```python
person = {"name": "Max", "alter": 30, "stadt": "Berlin"}

# Mit eckigen Klammern (wirft KeyError bei fehlendem Key)
name = person["name"]
print(name)  # Max

# Mit .get() Methode (sicherer, gibt None oder Default)
alter = person.get("alter")
print(alter)  # 30

gehalt = person.get("gehalt")
print(gehalt)  # None

gehalt = person.get("gehalt", 50000)  # Mit Default-Wert
print(gehalt)  # 50000

# KeyError Beispiel
try:
    person["gehalt"]  # Key existiert nicht!
except KeyError as e:
    print(f"Key nicht gefunden: {e}")
```

### Werte hinzufügen und ändern

```python
person = {"name": "Max", "alter": 30}

# Neuen Key hinzufügen
person["stadt"] = "Berlin"
print(person)  # {'name': 'Max', 'alter': 30, 'stadt': 'Berlin'}

# Bestehenden Wert ändern
person["alter"] = 31
print(person)  # {'name': 'Max', 'alter': 31, 'stadt': 'Berlin'}

# Mehrere Keys auf einmal hinzufügen mit update()
person.update({"beruf": "Entwickler", "gehalt": 60000})
print(person)
# {'name': 'Max', 'alter': 31, 'stadt': 'Berlin', 
#  'beruf': 'Entwickler', 'gehalt': 60000}
```

### Werte löschen

```python
person = {
    "name": "Max",
    "alter": 30,
    "stadt": "Berlin",
    "beruf": "Entwickler"
}

# Mit del Statement
del person["beruf"]
print(person)  # beruf wurde entfernt

# Mit .pop() - gibt Wert zurück
alter = person.pop("alter")
print(f"Gelöscht: {alter}")  # Gelöscht: 30
print(person)  # alter wurde entfernt

# Mit .pop() und Default-Wert (kein KeyError)
gehalt = person.pop("gehalt", "nicht vorhanden")
print(gehalt)  # nicht vorhanden

# Alle Einträge löschen
person.clear()
print(person)  # {}
```

---

## Details und Interna

### Hash-Table Implementation

Dictionaries sind intern als **Hash-Tables** implementiert:

```python
# Wie ein Dictionary intern funktioniert (vereinfacht):

# 1. Key wird gehasht
key = "name"
hash_value = hash(key)  # z.B. -8374728374982374

# 2. Hash bestimmt Position im Array
# position = hash_value % array_size

# 3. Value wird an dieser Position gespeichert
# Bei Kollisionen: Open Addressing / Probing

# Deshalb: Keys müssen hashable sein!
print(hash("text"))      # ✅ Funktioniert
print(hash(42))          # ✅ Funktioniert
print(hash((1, 2, 3)))   # ✅ Tuple ist hashable

try:
    print(hash([1, 2, 3]))  # ❌ List ist nicht hashable
except TypeError as e:
    print(f"Fehler: {e}")
```

### Hashable vs. Unhashable

```python
# ✅ HASHABLE (können Dict-Keys sein)
hashable_keys = {
    "string": "str ist immutable",
    42: "int ist immutable",
    3.14: "float ist immutable",
    (1, 2): "tuple ist immutable",
    True: "bool ist immutable",
    None: "None ist immutable",
    frozenset([1, 2]): "frozenset ist immutable"
}

# ❌ UNHASHABLE (können KEINE Dict-Keys sein)
# Listen
try:
    falsch = {[1, 2, 3]: "value"}
except TypeError:
    print("List kann kein Key sein!")

# Sets
try:
    falsch = {{1, 2, 3}: "value"}
except TypeError:
    print("Set kann kein Key sein!")

# Dictionaries
try:
    falsch = {{"key": "value"}: "value"}
except TypeError:
    print("Dict kann kein Key sein!")
```

### Memory Layout (Python 3.6+)

Seit Python 3.6+ nutzen Dictionaries eine **kompaktere, geordnete** Implementierung:

```python
# Alte Implementation (vor 3.6):
# - Hash-Table mit direkter Speicherung
# - Viel verschwendeter Platz bei leeren Slots
# - Keine Insertion-Order

# Neue Implementation (3.6+):
# - Separates Dense Array für Keys/Values
# - Hash-Table zeigt auf Indices
# - 20-25% weniger Speicher
# - Insertion-Order erhalten!

d = {"z": 1, "a": 2, "m": 3}
print(list(d.keys()))  # ['z', 'a', 'm'] - Reihenfolge erhalten!

# Aber: Verlasse dich nicht auf die Reihenfolge
# wenn es wichtig ist → collections.OrderedDict
```

### Performance-Charakteristiken

```python
import sys

# Lookup: O(1) durchschnittlich
d = {i: i*2 for i in range(1000000)}

# Schneller Zugriff, unabhängig von Größe
print(d[999999])  # Instant!

# Speicherverbrauch
d_small = {i: i for i in range(10)}
d_large = {i: i for i in range(1000)}

print(f"10 Einträge: {sys.getsizeof(d_small)} bytes")
print(f"1000 Einträge: {sys.getsizeof(d_large)} bytes")

# Wichtig: Dictionaries brauchen mehr Speicher als Listen
# dafür extrem schneller Lookup!
```

---

## Praktische Anwendungen

### 1. Datenstruktur für Objekte

```python
# Statt mehrere Variablen:
name = "Max"
alter = 30
stadt = "Berlin"

# Besser: Ein Dictionary
person = {
    "name": "Max",
    "alter": 30,
    "stadt": "Berlin",
    "hobbys": ["Programmieren", "Lesen", "Sport"]
}

# Einfacher Zugriff
print(f"{person['name']} ist {person['alter']} Jahre alt")
print(f"Hobbys: {', '.join(person['hobbys'])}")
```

### 2. Zählen von Elementen

```python
# Wörter in Text zählen
text = "Python ist toll Python macht Spaß Python ist super"
woerter = text.lower().split()

# Manuelle Zählung
zaehler = {}
for wort in woerter:
    if wort in zaehler:
        zaehler[wort] += 1
    else:
        zaehler[wort] = 1

print(zaehler)
# {'python': 3, 'ist': 2, 'toll': 1, 'macht': 1, 'spaß': 1, 'super': 1}

# Eleganter mit .get()
zaehler2 = {}
for wort in woerter:
    zaehler2[wort] = zaehler2.get(wort, 0) + 1

# Noch eleganter: collections.Counter (später)
```

### 3. Lookup-Tables

```python
# Monatsnamen zu Nummern
monate = {
    "januar": 1,
    "februar": 2,
    "märz": 3,
    "april": 4,
    "mai": 5,
    "juni": 6,
    "juli": 7,
    "august": 8,
    "september": 9,
    "oktober": 10,
    "november": 11,
    "dezember": 12
}

monat = "märz"
print(f"{monat.title()} ist Monat Nr. {monate[monat.lower()]}")

# Umgekehrte Lookup-Table
monate_umgekehrt = {v: k for k, v in monate.items()}
print(f"Monat 12 ist: {monate_umgekehrt[12]}")  # dezember
```

### 4. Konfigurationen

```python
# App-Konfiguration
config = {
    "debug": True,
    "database": {
        "host": "localhost",
        "port": 5432,
        "name": "myapp"
    },
    "max_connections": 100,
    "timeout": 30,
    "features": ["login", "api", "admin"]
}

# Verschachtelte Zugriffe
db_host = config["database"]["host"]
print(f"Datenbank auf: {db_host}")

# Feature-Check
if "api" in config["features"]:
    print("API ist aktiviert")
```

### 5. Caching / Memoization

```python
# Fibonacci mit Caching
def fibonacci_cached(n, cache={}):
    """Berechnet Fibonacci-Zahl mit Dictionary-Cache"""
    if n in cache:
        return cache[n]
    
    if n <= 1:
        return n
    
    result = fibonacci_cached(n-1, cache) + fibonacci_cached(n-2, cache)
    cache[n] = result
    return result

# Viel schneller als ohne Cache!
print(fibonacci_cached(100))  # Instant!
print(f"Cache enthält {len(fibonacci_cached.__defaults__[0])} Einträge")
```

### 6. Gruppierung von Daten

```python
# Schüler nach Klassen gruppieren
schueler = [
    {"name": "Max", "klasse": "10a"},
    {"name": "Anna", "klasse": "10b"},
    {"name": "Tom", "klasse": "10a"},
    {"name": "Lisa", "klasse": "10b"},
    {"name": "Paul", "klasse": "10a"}
]

# Nach Klasse gruppieren
klassen = {}
for s in schueler:
    klasse = s["klasse"]
    if klasse not in klassen:
        klassen[klasse] = []
    klassen[klasse].append(s["name"])

for klasse, namen in klassen.items():
    print(f"{klasse}: {', '.join(namen)}")

# Ausgabe:
# 10a: Max, Tom, Paul
# 10b: Anna, Lisa
```

---

## Häufige Fallstricke

### ⚠️ Fallstrick 1: Mutable Default Values

```python
# ❌ FALSCH - Gefährliches Muster!
def add_item(item, items={}):
    items[item] = len(items) + 1
    return items

# Jeder Aufruf nutzt DASSELBE Dictionary!
print(add_item("a"))  # {'a': 1}
print(add_item("b"))  # {'a': 1, 'b': 2} - Ups!
print(add_item("c"))  # {'a': 1, 'b': 2, 'c': 3} - Alle zusammen!

# ✅ RICHTIG
def add_item_correct(item, items=None):
    if items is None:
        items = {}
    items[item] = len(items) + 1
    return items

print(add_item_correct("a"))  # {'a': 1}
print(add_item_correct("b"))  # {'b': 1} - Jedes Mal neu!
```

### ⚠️ Fallstrick 2: Key nicht gefunden

```python
person = {"name": "Max", "alter": 30}

# ❌ FALSCH - Wirft KeyError
try:
    stadt = person["stadt"]  # Key existiert nicht!
except KeyError:
    print("Stadt nicht gefunden!")

# ✅ RICHTIG - Mit .get() und Default
stadt = person.get("stadt", "Unbekannt")
print(f"Stadt: {stadt}")  # Stadt: Unbekannt

# ODER: Mit in-Check
if "stadt" in person:
    stadt = person["stadt"]
else:
    stadt = "Unbekannt"
```

### ⚠️ Fallstrick 3: Dictionary während Iteration ändern

```python
preise = {"apfel": 2.50, "banane": 1.80, "kirsche": 3.00}

# ❌ FALSCH - RuntimeError!
try:
    for item, preis in preise.items():
        if preis > 2.00:
            del preise[item]  # Ändert Dict während Iteration!
except RuntimeError as e:
    print(f"Fehler: {e}")

# ✅ RICHTIG - Liste der zu löschenden Keys sammeln
preise = {"apfel": 2.50, "banane": 1.80, "kirsche": 3.00}
zu_loeschen = [item for item, preis in preise.items() if preis > 2.00]

for item in zu_loeschen:
    del preise[item]

print(preise)  # {'banane': 1.80}

# ODER: Neues Dict mit Comprehension
preise = {"apfel": 2.50, "banane": 1.80, "kirsche": 3.00}
guenstig = {k: v for k, v in preise.items() if v <= 2.00}
print(guenstig)  # {'banane': 1.80}
```

### ⚠️ Fallstrick 4: Unhashable Keys

```python
# ❌ FALSCH - List als Key
try:
    falsch = {[1, 2, 3]: "value"}
except TypeError as e:
    print(f"Fehler: {e}")

# ✅ RICHTIG - Tuple stattdessen
richtig = {(1, 2, 3): "value"}
print(richtig)  # {(1, 2, 3): 'value'}

# Praktisch: Koordinaten als Keys
positionen = {
    (0, 0): "Start",
    (10, 5): "Checkpoint 1",
    (20, 15): "Ziel"
}
```

---

## Performance-Hinweise

### Zeitkomplexität

| Operation | Average Case | Worst Case | Bemerkung |
|-----------|--------------|------------|-----------|
| `d[key]` | O(1) | O(n) | Zugriff |
| `d[key] = value` | O(1) | O(n) | Setzen |
| `key in d` | O(1) | O(n) | Membership |
| `del d[key]` | O(1) | O(n) | Löschen |
| `d.get(key)` | O(1) | O(n) | Sicherer Zugriff |
| `d.copy()` | O(n) | O(n) | Shallow Copy |
| `d.clear()` | O(1) | O(1) | Alle löschen |

**Worst Case** tritt nur bei vielen Hash-Kollisionen auf (sehr selten).

### Wann Dictionary verwenden?

**✅ Verwende Dict wenn:**
- Schneller Lookup über Keys nötig ist
- Key-Value-Paare gespeichert werden sollen
- Eindeutige Identifikation wichtig ist
- Häufige Membership-Tests (`in` Operator)
- Reihenfolge seit 3.7+ wichtig ist

**❌ Vermeide Dict wenn:**
- Nur Reihenfolge wichtig ist → Liste
- Keys numerisch und dicht (0, 1, 2, ...) → Liste
- Nur Membership, keine Values → Set
- Speicher kritisch und viele Einträge → Andere Struktur

### Memory-Overhead

```python
import sys

# Dictionary braucht mehr Speicher als Liste
liste = list(range(100))
dict_from_list = {i: i for i in range(100)}

print(f"Liste: {sys.getsizeof(liste)} bytes")
print(f"Dict:  {sys.getsizeof(dict_from_list)} bytes")

# Aber: Lookup in Dict ist O(1) statt O(n)!
# Trade-off: Speicher gegen Geschwindigkeit
```

---

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [Mapping Types — dict](https://docs.python.org/3/library/stdtypes.html#mapping-types-dict) - Vollständige Dict-Referenz
- [Dictionary view objects](https://docs.python.org/3/library/stdtypes.html#dictionary-view-objects) - Views und Iteration
- [Built-in Types](https://docs.python.org/3/library/stdtypes.html) - Alle eingebauten Typen
- [PEP 468 – Preserving the order of **kwargs](https://peps.python.org/pep-0468/) - Warum Reihenfolge wichtig wurde

---

## Verwandte Themen

- [[03_Mappings_und_Sets/01_Dictionaries/02_Dict Methoden|Dict Methoden]] - Alle wichtigen Methoden im Detail
- [[03_Mappings_und_Sets/01_Dictionaries/03_Dict Comprehensions|Dict Comprehensions]] - Elegante Dict-Erzeugung
- [[03_Mappings_und_Sets/03_Collections_Module/02_DefaultDict|DefaultDict]] - Dict mit Default-Values
- [[03_Mappings_und_Sets/02_Sets/01_Set Basics|Set Basics]] - Verwandte Datenstruktur

---

← [[02_Sequenzen/05_Range/02_Übungen Range|Übungen Range]] | [[INDEX|📑 Index]] | [[03_Mappings_und_Sets/01_Dictionaries/02_Dict Methoden|Dict Methoden]] →