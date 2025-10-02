---
tags: [python, datenstrukturen, mappings, dictionaries, methoden]
created: 2025-10-01
---

← [[03_Mappings_und_Sets/01_Dictionaries/01_Dict Basics|Dict Basics]] | [[INDEX|📑 Index]] | [[03_Mappings_und_Sets/01_Dictionaries/03_Dict Comprehensions|Dict Comprehensions]] →

# Dictionary Methoden

> [!tip] Lernziel
> Nach dieser Seite kennst du alle wichtigen Dictionary-Methoden, ihre Anwendungsfälle und Unterschiede im Detail.

## Überblick: Alle Dict-Methoden

Python Dictionaries bieten eine reichhaltige API mit vielen nützlichen Methoden:

| Methode | Beschreibung | Return |
|---------|--------------|--------|
| `d.get(key, default)` | Sicherer Zugriff mit Optional | Value oder Default |
| `d.setdefault(key, default)` | Gibt Value oder setzt & gibt Default | Value |
| `d.pop(key, default)` | Entfernt Key und gibt Value zurück | Value |
| `d.popitem()` | Entfernt und gibt letztes Paar zurück | (key, value) |
| `d.update(other)` | Fügt andere Mappings hinzu | None |
| `d.clear()` | Entfernt alle Einträge | None |
| `d.copy()` | Erstellt flache Kopie | dict |
| `d.keys()` | Gibt Keys als View zurück | dict_keys |
| `d.values()` | Gibt Values als View zurück | dict_values |
| `d.items()` | Gibt (key, value) Paare als View zurück | dict_items |
| `d.fromkeys(seq, value)` | Erstellt Dict aus Sequence | dict |

---

## 1. get() - Sicherer Zugriff

Die wichtigste Methode für sicheren Dictionary-Zugriff!

### Syntax

```python
d.get(key)           # Gibt None wenn Key fehlt
d.get(key, default)  # Gibt default wenn Key fehlt
```

### Beispiele

```python
person = {"name": "Max", "alter": 30}

# Standard-Zugriff vs get()
# Mit []: KeyError wenn Key fehlt
try:
    stadt = person["stadt"]
except KeyError:
    print("KeyError!")

# Mit get(): None oder Default
stadt = person.get("stadt")
print(stadt)  # None

stadt = person.get("stadt", "Unbekannt")
print(stadt)  # Unbekannt

# Bestehende Keys
name = person.get("name")
print(name)  # Max

# Default wird nicht evaluiert wenn Key existiert
name = person.get("name", "Teurer Default")  # "Teurer Default" wird nicht berechnet
```

### Praktischer Einsatz

```python
# Konfiguration mit Defaults
config = {"debug": True, "port": 8000}

# Sichere Defaults
debug = config.get("debug", False)
port = config.get("port", 3000)
host = config.get("host", "localhost")  # Fehlt, nimmt Default

print(f"Server läuft auf {host}:{port} (Debug: {debug})")
# Server läuft auf localhost:8000 (Debug: True)

# Zähler mit get()
text = "hallo welt hallo"
counter = {}
for wort in text.split():
    counter[wort] = counter.get(wort, 0) + 1

print(counter)  # {'hallo': 2, 'welt': 1}
```

---

## 2. setdefault() - Setzen wenn fehlt

Gibt Value zurück, **setzt aber Default wenn Key nicht existiert**.

### Syntax

```python
d.setdefault(key, default=None)
```

### Beispiele

```python
person = {"name": "Max"}

# Key existiert: Gibt bestehenden Value zurück
name = person.setdefault("name", "Standard")
print(name)  # Max (bestehender Wert)
print(person)  # {'name': 'Max'} - Unverändert

# Key fehlt: Setzt UND gibt Default zurück
alter = person.setdefault("alter", 25)
print(alter)  # 25
print(person)  # {'name': 'Max', 'alter': 25} - Wurde gesetzt!

# Ohne Default: Setzt None
stadt = person.setdefault("stadt")
print(stadt)  # None
print(person)  # {'name': 'Max', 'alter': 25, 'stadt': None}
```

### Unterschied zu get()

```python
d = {"a": 1}

# get() verändert Dictionary NICHT
value = d.get("b", 2)
print(value)  # 2
print(d)      # {'a': 1} - Unverändert

# setdefault() verändert Dictionary!
d = {"a": 1}
value = d.setdefault("b", 2)
print(value)  # 2
print(d)      # {'a': 1, 'b': 2} - 'b' wurde hinzugefügt!
```

### Praktischer Einsatz: Gruppierung

```python
# Schüler nach Klassen gruppieren
schueler = [
    ("Max", "10a"),
    ("Anna", "10b"),
    ("Tom", "10a"),
    ("Lisa", "10b")
]

# Mit setdefault() elegant lösen
klassen = {}
for name, klasse in schueler:
    klassen.setdefault(klasse, []).append(name)

print(klassen)
# {'10a': ['Max', 'Tom'], '10b': ['Anna', 'Lisa']}

# Ohne setdefault() umständlicher:
klassen2 = {}
for name, klasse in schueler:
    if klasse not in klassen2:
        klassen2[klasse] = []
    klassen2[klasse].append(name)
```

---

## 3. pop() - Entfernen und Zurückgeben

Entfernt einen Key und gibt dessen Value zurück.

### Syntax

```python
d.pop(key)           # KeyError wenn Key fehlt
d.pop(key, default)  # Gibt default wenn Key fehlt
```

### Beispiele

```python
person = {
    "name": "Max",
    "alter": 30,
    "stadt": "Berlin"
}

# Key entfernen und Value bekommen
alter = person.pop("alter")
print(alter)   # 30
print(person)  # {'name': 'Max', 'stadt': 'Berlin'}

# Mit Default (kein KeyError)
gehalt = person.pop("gehalt", 0)
print(gehalt)  # 0
print(person)  # Unverändert, da Key nicht existierte

# Ohne Default: KeyError
try:
    person.pop("beruf")
except KeyError as e:
    print(f"Key nicht gefunden: {e}")
```

### Praktischer Einsatz

```python
# Sensible Daten entfernen vor Logging
user_data = {
    "username": "max123",
    "email": "max@example.com",
    "password": "geheim123",
    "api_key": "secret_key_xyz"
}

# Sensible Felder entfernen
password = user_data.pop("password", None)
api_key = user_data.pop("api_key", None)

# Jetzt sicher zu loggen
print(f"User-Daten: {user_data}")
# User-Daten: {'username': 'max123', 'email': 'max@example.com'}

# Stack-ähnliche Verwendung (LIFO mit popitem besser)
temp_data = {"item1": "wert1", "item2": "wert2"}
while temp_data:
    key = list(temp_data.keys())[0]  # Ersten Key nehmen
    value = temp_data.pop(key)
    print(f"Verarbeite: {key} = {value}")
```

---

## 4. popitem() - Letztes Paar entfernen

Entfernt und gibt das **letzte eingefügte** Key-Value-Paar zurück (seit Python 3.7+).

### Syntax

```python
d.popitem()  # Gibt (key, value) Tuple zurück
             # KeyError wenn Dictionary leer
```

### Beispiele

```python
# LIFO-Verhalten (Last In, First Out)
d = {"a": 1, "b": 2, "c": 3}

paar = d.popitem()
print(paar)  # ('c', 3) - Letztes Element
print(d)     # {'a': 1, 'b': 2}

paar = d.popitem()
print(paar)  # ('b', 2)
print(d)     # {'a': 1}

# Bei leerem Dict: KeyError
d.clear()
try:
    d.popitem()
except KeyError:
    print("Dictionary ist leer!")
```

### Praktischer Einsatz: Stack

```python
# Dictionary als Stack verwenden
aufgaben_stack = {}

# Items hinzufügen (LIFO-Order wichtig)
aufgaben_stack["task_1"] = "E-Mail senden"
aufgaben_stack["task_2"] = "Bericht schreiben"
aufgaben_stack["task_3"] = "Meeting vorbereiten"

# Items in umgekehrter Reihenfolge abarbeiten
while aufgaben_stack:
    task_id, beschreibung = aufgaben_stack.popitem()
    print(f"Erledige: {task_id} - {beschreibung}")

# Ausgabe (LIFO):
# Erledige: task_3 - Meeting vorbereiten
# Erledige: task_2 - Bericht schreiben
# Erledige: task_1 - E-Mail senden
```

### Vor Python 3.7

```python
# In Python < 3.7 war Reihenfolge nicht garantiert!
# popitem() gab ein "zufälliges" Paar zurück
# Heute: Immer das letzte eingefügte Paar
```

---

## 5. update() - Dictionaries zusammenführen

Fügt Einträge aus einem anderen Mapping oder Iterable hinzu.

### Syntax

```python
d.update(other)           # Anderes Dictionary
d.update(**kwargs)        # Keyword Arguments
d.update(iterable)        # Iterable von (key, value) Paaren
d.update(d2, d3, **kw)    # Mehrere auf einmal (seit 3.9+)
```

### Beispiele

```python
# Mit anderem Dictionary
person = {"name": "Max", "alter": 30}
zusatz = {"stadt": "Berlin", "beruf": "Entwickler"}

person.update(zusatz)
print(person)
# {'name': 'Max', 'alter': 30, 'stadt': 'Berlin', 'beruf': 'Entwickler'}

# Bestehende Werte überschreiben
person.update({"alter": 31, "gehalt": 60000})
print(person)
# {'name': 'Max', 'alter': 31, 'stadt': 'Berlin', 
#  'beruf': 'Entwickler', 'gehalt': 60000}

# Mit Keyword Arguments
person.update(hobbys=["Sport", "Lesen"], auto="Tesla")
print(person)

# Mit Iterable von Paaren
paare = [("farbe", "blau"), ("größe", 180)]
person.update(paare)
print(person)

# Mehrere auf einmal (Python 3.9+)
d1 = {"a": 1}
d1.update({"b": 2}, {"c": 3}, d=4)
print(d1)  # {'a': 1, 'b': 2, 'c': 3, 'd': 4}
```

### Praktischer Einsatz: Config Merging

```python
# Default-Konfiguration
default_config = {
    "debug": False,
    "host": "localhost",
    "port": 3000,
    "timeout": 30,
    "max_connections": 100
}

# User-Konfiguration überschreibt Defaults
user_config = {
    "debug": True,
    "port": 8000
}

# Finale Config
config = default_config.copy()  # Kopie der Defaults
config.update(user_config)       # User-Werte überschreiben

print(config)
# {'debug': True, 'host': 'localhost', 'port': 8000, 
#  'timeout': 30, 'max_connections': 100}

# Umgebungsvariablen haben höchste Priorität
env_overrides = {"host": "0.0.0.0"}
config.update(env_overrides)
```

---

## 6. clear() - Alle Einträge löschen

Entfernt alle Key-Value-Paare aus dem Dictionary.

### Syntax

```python
d.clear()  # Gibt None zurück
```

### Beispiele

```python
person = {"name": "Max", "alter": 30, "stadt": "Berlin"}

person.clear()
print(person)  # {}
print(len(person))  # 0

# Unterschied zu d = {}
d1 = {"a": 1, "b": 2}
d2 = d1  # Referenz!

# Mit clear(): Beide leer
d1.clear()
print(d1)  # {}
print(d2)  # {} - Auch leer!

# Mit = {}: Nur d1 neu
d1 = {"a": 1, "b": 2}
d2 = d1
d1 = {}
print(d1)  # {}
print(d2)  # {'a': 1, 'b': 2} - Noch alte Daten!
```

### Praktischer Einsatz

```python
# Cache leeren
cache = {"user_1": "data", "user_2": "data"}

def clear_cache():
    cache.clear()
    print("Cache geleert")

# Temporäre Daten zurücksetzen
temp_storage = {}

def process_batch(items):
    for item in items:
        temp_storage[item.id] = item.data
    # Verarbeite...
    temp_storage.clear()  # Aufräumen nach Verarbeitung
```

---

## 7. copy() - Flache Kopie erstellen

Erstellt eine **flache Kopie** (shallow copy) des Dictionaries.

### Syntax

```python
d.copy()  # Gibt neues Dictionary zurück
```

### Beispiele

```python
original = {"name": "Max", "alter": 30}

# Flache Kopie
kopie = original.copy()

# Sind unterschiedliche Objekte
print(kopie is original)  # False
print(kopie == original)  # True

# Änderungen an Kopie betreffen Original nicht
kopie["alter"] = 31
print(original)  # {'name': 'Max', 'alter': 30} - Unverändert
print(kopie)     # {'name': 'Max', 'alter': 31}
```

### Problem: Verschachtelte Strukturen

```python
original = {
    "name": "Max",
    "hobbys": ["Sport", "Lesen"],
    "adresse": {"stadt": "Berlin", "plz": "10115"}
}

# Flache Kopie
kopie = original.copy()

# Änderung an verschachtelten Objekten betrifft BEIDE!
kopie["hobbys"].append("Gaming")
kopie["adresse"]["stadt"] = "München"

print(original["hobbys"])   # ['Sport', 'Lesen', 'Gaming'] - Auch geändert!
print(original["adresse"])  # {'stadt': 'München', 'plz': '10115'} - Auch geändert!

# Grund: Nur oberste Ebene wird kopiert
print(kopie["hobbys"] is original["hobbys"])  # True - Selbes Objekt!
```

### Lösung: Deep Copy

```python
import copy

original = {
    "name": "Max",
    "hobbys": ["Sport", "Lesen"],
    "adresse": {"stadt": "Berlin", "plz": "10115"}
}

# Tiefe Kopie
kopie = copy.deepcopy(original)

# Änderungen betreffen nur Kopie
kopie["hobbys"].append("Gaming")
kopie["adresse"]["stadt"] = "München"

print(original["hobbys"])   # ['Sport', 'Lesen'] - Unverändert
print(original["adresse"])  # {'stadt': 'Berlin', 'plz': '10115'} - Unverändert
```

---

## 8. keys() - Key-View

Gibt ein **View-Objekt** aller Keys zurück (kein Liste!).

### Syntax

```python
d.keys()  # Gibt dict_keys View zurück
```

### Beispiele

```python
person = {"name": "Max", "alter": 30, "stadt": "Berlin"}

# Keys als View
keys = person.keys()
print(keys)  # dict_keys(['name', 'alter', 'stadt'])
print(type(keys))  # <class 'dict_keys'>

# View ist dynamisch!
person["beruf"] = "Entwickler"
print(keys)  # dict_keys(['name', 'alter', 'stadt', 'beruf'])

# In Liste konvertieren
keys_list = list(keys)
print(keys_list)  # ['name', 'alter', 'stadt', 'beruf']

# Set-Operationen möglich!
keys2 = {"alter", "gehalt", "hobbys"}
gemeinsam = person.keys() & keys2
print(gemeinsam)  # {'alter'}

nur_in_person = person.keys() - keys2
print(nur_in_person)  # {'name', 'stadt', 'beruf'}
```

### Iteration über Keys

```python
person = {"name": "Max", "alter": 30, "stadt": "Berlin"}

# Direkt über Dict iteriert auch über Keys
for key in person:
    print(key)

# Explizit mit .keys() (identisch)
for key in person.keys():
    print(key)

# Mit Zugriff auf Values
for key in person.keys():
    print(f"{key}: {person[key]}")
```

---

## 9. values() - Value-View

Gibt ein **View-Objekt** aller Values zurück.

### Syntax

```python
d.values()  # Gibt dict_values View zurück
```

### Beispiele

```python
person = {"name": "Max", "alter": 30, "stadt": "Berlin"}

# Values als View
values = person.values()
print(values)  # dict_values(['Max', 30, 'Berlin'])
print(type(values))  # <class 'dict_values'>

# View ist dynamisch
person["beruf"] = "Entwickler"
print(values)  # dict_values(['Max', 30, 'Berlin', 'Entwickler'])

# In Liste konvertieren
values_list = list(values)
print(values_list)  # ['Max', 30, 'Berlin', 'Entwickler']

# Summe von numerischen Values
zahlen = {"a": 10, "b": 20, "c": 30}
summe = sum(zahlen.values())
print(summe)  # 60
```

### Praktischer Einsatz

```python
# Alle Values prüfen
scores = {"Max": 85, "Anna": 92, "Tom": 78}

if all(score >= 50 for score in scores.values()):
    print("Alle haben bestanden!")

# Maximum finden
bester_score = max(scores.values())
print(f"Bester Score: {bester_score}")

# Filtern
hohe_scores = [s for s in scores.values() if s >= 85]
print(hohe_scores)  # [85, 92]
```

---

## 10. items() - Key-Value-Paare

Gibt ein **View-Objekt** aller (key, value) Paare zurück.

### Syntax

```python
d.items()  # Gibt dict_items View zurück
```

### Beispiele

```python
person = {"name": "Max", "alter": 30, "stadt": "Berlin"}

# Items als View
items = person.items()
print(items)
# dict_items([('name', 'Max'), ('alter', 30), ('stadt', 'Berlin')])

# In Liste von Tupeln konvertieren
items_list = list(items)
print(items_list)
# [('name', 'Max'), ('alter', 30), ('stadt', 'Berlin')]

# View ist dynamisch
person["beruf"] = "Entwickler"
print(items)
# dict_items([('name', 'Max'), ('alter', 30), 
#             ('stadt', 'Berlin'), ('beruf', 'Entwickler')])
```

### Iteration mit items()

```python
person = {"name": "Max", "alter": 30, "stadt": "Berlin"}

# Tuple Unpacking in Loop
for key, value in person.items():
    print(f"{key}: {value}")

# name: Max
# alter: 30
# stadt: Berlin

# Filtern während Iteration
for key, value in person.items():
    if isinstance(value, int):
        print(f"{key} ist eine Zahl: {value}")
```

### Praktischer Einsatz: Dict umkehren

```python
# Original Dictionary
monate = {
    "januar": 1,
    "februar": 2,
    "märz": 3
}

# Umkehren: Value wird Key, Key wird Value
umgekehrt = {v: k for k, v in monate.items()}
print(umgekehrt)
# {1: 'januar', 2: 'februar', 3: 'märz'}

# Filtern und transformieren
preise = {"apfel": 2.50, "banane": 1.80, "kirsche": 4.00}

# Nur günstige Produkte, mit 10% Rabatt
guenstig = {
    produkt: preis * 0.9 
    for produkt, preis in preise.items() 
    if preis < 3.00
}
print(guenstig)
# {'apfel': 2.25, 'banane': 1.62}
```

---

## 11. fromkeys() - Dict aus Sequence

Erstellt ein neues Dictionary aus einer Sequence von Keys (Klassenmethode).

### Syntax

```python
dict.fromkeys(seq)              # Alle Values sind None
dict.fromkeys(seq, value)       # Alle Values sind value
```

### Beispiele

```python
# Mit None als Default
keys = ["name", "alter", "stadt"]
person = dict.fromkeys(keys)
print(person)
# {'name': None, 'alter': None, 'stadt': None}

# Mit festem Wert
person = dict.fromkeys(keys, "Unbekannt")
print(person)
# {'name': 'Unbekannt', 'alter': 'Unbekannt', 'stadt': 'Unbekannt'}

# Aus String (jedes Zeichen wird Key)
alphabet = dict.fromkeys("abc", 0)
print(alphabet)  # {'a': 0, 'b': 0, 'c': 0}

# Aus Range
zahlen = dict.fromkeys(range(5), [])
print(zahlen)
# {0: [], 1: [], 2: [], 3: [], 4: []}
```

### ⚠️ Vorsicht: Mutable Default Values

```python
# GEFÄHRLICH: Alle Keys teilen sich DIESELBE Liste!
d = dict.fromkeys(["a", "b", "c"], [])

d["a"].append(1)
print(d)
# {'a': [1], 'b': [1], 'c': [1]} - Alle geändert!

print(d["a"] is d["b"])  # True - Selbes Objekt!

# BESSER: Dict Comprehension mit jeweils neuer Liste
d = {key: [] for key in ["a", "b", "c"]}
d["a"].append(1)
print(d)
# {'a': [1], 'b': [], 'c': []} - Nur 'a' geändert
```

### Praktischer Einsatz

```python
# Initialisierung von Countern
produkte = ["apfel", "banane", "kirsche", "apfel", "banane"]

# Alle Produkte mit Counter 0 initialisieren
unique_produkte = set(produkte)
counter = dict.fromkeys(unique_produkte, 0)

for produkt in produkte:
    counter[produkt] += 1

print(counter)
# {'banane': 2, 'apfel': 2, 'kirsche': 1}

# Feature-Flags initialisieren
features = ["dark_mode", "notifications", "auto_save"]
feature_flags = dict.fromkeys(features, False)
print(feature_flags)
# {'dark_mode': False, 'notifications': False, 'auto_save': False}
```

---

## Performance-Vergleich

```python
import timeit

# Zugriff: [] vs get()
def test_bracket():
    d = {"a": 1}
    return d.get("a", 0)

def test_get():
    d = {"a": 1}
    return d.get("a", 0)

# get() ist minimal langsamer, aber sicherer
print("[] Zugriff:", timeit.timeit(test_bracket, number=1000000))
print("get():", timeit.timeit(test_get, number=1000000))

# Update: update() vs loop
def test_update():
    d1 = {str(i): i for i in range(100)}
    d2 = {str(i): i*2 for i in range(100)}
    d1.update(d2)

def test_loop():
    d1 = {str(i): i for i in range(100)}
    d2 = {str(i): i*2 for i in range(100)}
    for k, v in d2.items():
        d1[k] = v

# update() ist deutlich schneller!
print("update():", timeit.timeit(test_update, number=10000))
print("loop:", timeit.timeit(test_loop, number=10000))
```

---

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [Dictionary Methods](https://docs.python.org/3/library/stdtypes.html#dict) - Vollständige Methoden-Referenz
- [Dictionary view objects](https://docs.python.org/3/library/stdtypes.html#dictionary-view-objects) - Details zu keys(), values(), items()
- [copy module](https://docs.python.org/3/library/copy.html) - Für shallow und deep copies

---

## Verwandte Themen

- [[03_Mappings_und_Sets/01_Dictionaries/01_Dict Basics|Dict Basics]] - Grundlagen wiederholen
- [[03_Mappings_und_Sets/01_Dictionaries/03_Dict Comprehensions|Dict Comprehensions]] - Elegante Dict-Erzeugung
- [[03_Mappings_und_Sets/01_Dictionaries/05_Dict Views|Dict Views]] - Views im Detail
- [[03_Mappings_und_Sets/03_Collections_Module/02_DefaultDict|DefaultDict]] - Alternative zu setdefault()

---

← [[03_Mappings_und_Sets/01_Dictionaries/01_Dict Basics|Dict Basics]] | [[INDEX|📑 Index]] | [[03_Mappings_und_Sets/01_Dictionaries/03_Dict Comprehensions|Dict Comprehensions]] →