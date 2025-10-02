---
tags: [python, datenstrukturen, mappings, dictionaries, views]
created: 2025-10-01
---

← [[03_Mappings_und_Sets/01_Dictionaries/04_Nested Dicts|Nested Dicts]] | [[INDEX|📑 Index]] | [[03_Mappings_und_Sets/01_Dictionaries/06_OrderedDict vs Dict|OrderedDict vs Dict]] →

# Dictionary Views

> [!tip] Lernziel
> Nach dieser Seite verstehst du Dictionary View-Objekte, ihre dynamischen Eigenschaften und wie du sie effizient für Set-Operationen nutzt.

## Was sind Dictionary Views?

**Dictionary Views** sind spezielle Objekte, die von den Methoden `keys()`, `values()` und `items()` zurückgegeben werden. Sie bieten eine **dynamische Sicht** auf die Dictionary-Daten.

**Eigenschaften:**
- **Dynamisch**: Änderungen am Dictionary werden sofort reflektiert
- **Memory-effizient**: Keine Kopie der Daten
- **Iterierbar**: Können in Loops verwendet werden
- **Set-ähnlich**: keys() und items() unterstützen Set-Operationen
- **Kein Index-Zugriff**: Nicht subscriptable (kein `view[0]`)

**View-Typen:**
- `dict_keys` - Von `.keys()` zurückgegeben
- `dict_values` - Von `.values()` zurückgegeben  
- `dict_items` - Von `.items()` zurückgegeben

---

## Die drei View-Typen

### dict_keys

```python
person = {"name": "Max", "alter": 30, "stadt": "Berlin"}

# Keys als View
keys = person.keys()
print(keys)  # dict_keys(['name', 'alter', 'stadt'])
print(type(keys))  # <class 'dict_keys'>

# Dynamisch: Änderungen am Dict reflektiert
person["beruf"] = "Entwickler"
print(keys)  # dict_keys(['name', 'alter', 'stadt', 'beruf'])

# Iteration möglich
for key in keys:
    print(key)

# Membership-Test O(1)
print("name" in keys)  # True
print("gehalt" in keys)  # False

# Länge
print(len(keys))  # 4
```

### dict_values

```python
person = {"name": "Max", "alter": 30, "stadt": "Berlin"}

# Values als View
values = person.values()
print(values)  # dict_values(['Max', 30, 'Berlin'])
print(type(values))  # <class 'dict_values'>

# Dynamisch
person["name"] = "Anna"
print(values)  # dict_values(['Anna', 30, 'Berlin'])

# Iteration
for value in values:
    print(value)

# Membership-Test O(n) - muss alle Values durchsuchen!
print("Berlin" in values)  # True
print(30 in values)  # True

# Zählen von Vorkommen
print(list(values).count(30))  # 1

# Mit numerischen Values
zahlen = {"a": 10, "b": 20, "c": 30}
print(sum(zahlen.values()))  # 60
print(max(zahlen.values()))  # 30
print(min(zahlen.values()))  # 10
```

### dict_items

```python
person = {"name": "Max", "alter": 30, "stadt": "Berlin"}

# Items als View - Tupel von (key, value) Paaren
items = person.items()
print(items)
# dict_items([('name', 'Max'), ('alter', 30), ('stadt', 'Berlin')])
print(type(items))  # <class 'dict_items'>

# Dynamisch
person["beruf"] = "Entwickler"
print(items)
# dict_items([('name', 'Max'), ('alter', 30), ('stadt', 'Berlin'), 
#             ('beruf', 'Entwickler')])

# Iteration mit Tuple Unpacking
for key, value in items:
    print(f"{key}: {value}")

# Membership-Test mit Tupeln
print(("name", "Max") in items)  # True
print(("alter", 25) in items)  # False

# Länge
print(len(items))  # 4
```

---

## Dynamische Eigenschaften

### Views reflektieren Änderungen sofort

```python
d = {"a": 1, "b": 2}

# Views erstellen
keys = d.keys()
values = d.values()
items = d.items()

print("Initial:")
print(f"Keys: {keys}")
print(f"Values: {values}")
print(f"Items: {items}")

# Dictionary ändern
d["c"] = 3
d["a"] = 10

print("\nNach Änderungen:")
print(f"Keys: {keys}")    # Neuer Key 'c' sichtbar
print(f"Values: {values}")  # Neuer Value und geänderter Value
print(f"Items: {items}")    # Alle Änderungen

# Key löschen
del d["b"]

print("\nNach Löschen:")
print(f"Keys: {keys}")    # 'b' verschwunden
print(f"Values: {values}")  # 2 verschwunden
print(f"Items: {items}")    # ('b', 2) verschwunden
```

### Views vs. Listen

```python
d = {"a": 1, "b": 2, "c": 3}

# View (dynamisch, kein Speicher-Overhead)
keys_view = d.keys()
print(type(keys_view))  # <class 'dict_keys'>

# Liste (statisch, Kopie der Daten)
keys_list = list(d.keys())
print(type(keys_list))  # <class 'list'>

# Änderung am Dictionary
d["d"] = 4

print(keys_view)  # dict_keys(['a', 'b', 'c', 'd']) - Aktualisiert!
print(keys_list)  # ['a', 'b', 'c'] - Unverändert!

# View hat keinen Index-Zugriff
try:
    print(keys_view[0])  # TypeError!
except TypeError as e:
    print(f"Fehler: {e}")

# Liste hat Index-Zugriff
print(keys_list[0])  # 'a'
```

---

## Set-Operationen mit Views

### keys() und items() Views

Keys und Items Views unterstützen **Set-Operationen**, da Keys eindeutig sind!

```python
dict1 = {"a": 1, "b": 2, "c": 3}
dict2 = {"b": 20, "c": 30, "d": 4}

# Union (|) - Alle Keys
union = dict1.keys() | dict2.keys()
print(union)  # {'a', 'b', 'c', 'd'}

# Intersection (&) - Gemeinsame Keys
intersection = dict1.keys() & dict2.keys()
print(intersection)  # {'b', 'c'}

# Difference (-) - Nur in dict1
difference = dict1.keys() - dict2.keys()
print(difference)  # {'a'}

# Symmetric Difference (^) - Entweder-oder
sym_diff = dict1.keys() ^ dict2.keys()
print(sym_diff)  # {'a', 'd'}

# Subset/Superset Tests
dict3 = {"a": 1, "b": 2}
print(dict3.keys() <= dict1.keys())  # True - Subset
print(dict1.keys() >= dict3.keys())  # True - Superset
print(dict1.keys() < dict3.keys())   # False - Proper Subset
```

### Praktische Anwendung: Dict-Vergleich

```python
# Zwei Config-Dictionaries vergleichen
config_old = {
    "debug": False,
    "host": "localhost",
    "port": 3000,
    "timeout": 30
}

config_new = {
    "debug": True,
    "host": "0.0.0.0",
    "port": 8000,
    "max_connections": 100
}

# Welche Keys wurden hinzugefügt?
added = config_new.keys() - config_old.keys()
print(f"Hinzugefügt: {added}")  # {'max_connections'}

# Welche Keys wurden entfernt?
removed = config_old.keys() - config_new.keys()
print(f"Entfernt: {removed}")  # {'timeout'}

# Welche Keys sind in beiden?
common = config_old.keys() & config_new.keys()
print(f"Gemeinsam: {common}")  # {'debug', 'host', 'port'}

# Welche gemeinsamen Keys haben unterschiedliche Values?
changed = {
    key 
    for key in common 
    if config_old[key] != config_new[key]
}
print(f"Geändert: {changed}")  # {'debug', 'host', 'port'}
```

### Set-Operationen mit items()

```python
dict1 = {"a": 1, "b": 2}
dict2 = {"b": 2, "c": 3}

# Items die in beiden gleich sind
gemeinsam = dict1.items() & dict2.items()
print(gemeinsam)  # {('b', 2)}

# Items die nur in dict1 sind
nur_dict1 = dict1.items() - dict2.items()
print(nur_dict1)  # {('a', 1)}

# Items die in einem von beiden sind (aber nicht beide)
unterschiedlich = dict1.items() ^ dict2.items()
print(unterschiedlich)  # {('a', 1), ('c', 3)}
```

### ⚠️ values() Views unterstützen KEINE Set-Operationen

```python
dict1 = {"a": 1, "b": 2}
dict2 = {"c": 2, "d": 3}

# ❌ FALSCH - TypeError!
try:
    union = dict1.values() | dict2.values()
except TypeError as e:
    print(f"Fehler: {e}")
    # unsupported operand type(s) for |: 'dict_values' and 'dict_values'

# ✅ RICHTIG - In Sets konvertieren
values1 = set(dict1.values())
values2 = set(dict2.values())
union = values1 | values2
print(union)  # {1, 2, 3}

# Grund: Values können Duplikate haben
# Set-Operationen benötigen Eindeutigkeit
```

---

## Konvertierung von Views

### Zu Liste

```python
d = {"a": 1, "b": 2, "c": 3}

# Views zu Listen
keys_list = list(d.keys())
values_list = list(d.values())
items_list = list(d.items())

print(keys_list)    # ['a', 'b', 'c']
print(values_list)  # [1, 2, 3]
print(items_list)   # [('a', 1), ('b', 2), ('c', 3)]

# Jetzt mit Index-Zugriff
print(keys_list[0])   # 'a'
print(values_list[1]) # 2
print(items_list[2])  # ('c', 3)
```

### Zu Set

```python
d = {"a": 1, "b": 2, "c": 3}

# Keys zu Set (oft schon Set-ähnlich)
keys_set = set(d.keys())
print(keys_set)  # {'a', 'b', 'c'}

# Values zu Set (entfernt Duplikate!)
d2 = {"a": 1, "b": 2, "c": 1, "d": 2}
values_set = set(d2.values())
print(values_set)  # {1, 2}

# Items zu Set von Tupeln
items_set = set(d.items())
print(items_set)  # {('a', 1), ('b', 2), ('c', 3)}
```

### Zu Tuple

```python
d = {"a": 1, "b": 2}

keys_tuple = tuple(d.keys())
values_tuple = tuple(d.values())
items_tuple = tuple(d.items())

print(keys_tuple)    # ('a', 'b')
print(values_tuple)  # (1, 2)
print(items_tuple)   # (('a', 1), ('b', 2))
```

---

## Iteration über Views

### Direkt iterieren

```python
person = {"name": "Max", "alter": 30, "stadt": "Berlin"}

# Keys (meist unnötig, da for key in dict: auch funktioniert)
for key in person.keys():
    print(key)

# Values
for value in person.values():
    print(value)

# Items mit Tuple Unpacking
for key, value in person.items():
    print(f"{key}: {value}")
```

### Mit enumerate()

```python
person = {"name": "Max", "alter": 30, "stadt": "Berlin"}

# Keys mit Index
for i, key in enumerate(person.keys()):
    print(f"{i}: {key}")

# Items mit Index
for i, (key, value) in enumerate(person.items()):
    print(f"{i}. {key} = {value}")

# 0. name = Max
# 1. alter = 30
# 2. stadt = Berlin
```

### Mit zip() kombinieren

```python
dict1 = {"a": 1, "b": 2, "c": 3}
dict2 = {"a": 10, "b": 20, "c": 30}

# Parallel über zwei Dicts iterieren
for k1, k2 in zip(dict1.keys(), dict2.keys()):
    print(f"{k1} == {k2}: {k1 == k2}")

# Values vergleichen
for v1, v2 in zip(dict1.values(), dict2.values()):
    print(f"{v1} vs {v2}")

# Items parallel
for (k1, v1), (k2, v2) in zip(dict1.items(), dict2.items()):
    if k1 == k2:
        print(f"{k1}: {v1} → {v2}")
```

---

## Praktische Anwendungen

### 1. Dict Differenzen finden

```python
def dict_diff(d1, d2):
    """Findet Unterschiede zwischen zwei Dictionaries"""
    # Nur in d1
    nur_d1 = d1.keys() - d2.keys()
    
    # Nur in d2
    nur_d2 = d2.keys() - d1.keys()
    
    # In beiden, aber unterschiedliche Values
    gemeinsam = d1.keys() & d2.keys()
    geaendert = {
        key: (d1[key], d2[key])
        for key in gemeinsam
        if d1[key] != d2[key]
    }
    
    return {
        "nur_d1": set(nur_d1),
        "nur_d2": set(nur_d2),
        "geaendert": geaendert
    }

# Beispiel
alt = {"name": "Max", "alter": 30, "stadt": "Berlin"}
neu = {"name": "Max", "alter": 31, "land": "Deutschland"}

diff = dict_diff(alt, neu)
print(f"Nur in alt: {diff['nur_d1']}")        # {'stadt'}
print(f"Nur in neu: {diff['nur_d2']}")        # {'land'}
print(f"Geändert: {diff['geaendert']}")       # {'alter': (30, 31)}
```

### 2. Dict Merge mit Konflikt-Behandlung

```python
def merge_dicts(d1, d2, conflict_resolver=None):
    """Merged zwei Dicts mit optionaler Konflikt-Behandlung"""
    result = d1.copy()
    
    # Gemeinsame Keys finden
    conflicts = d1.keys() & d2.keys()
    
    for key in d2:
        if key in conflicts:
            if conflict_resolver:
                result[key] = conflict_resolver(d1[key], d2[key])
            else:
                result[key] = d2[key]  # d2 gewinnt
        else:
            result[key] = d2[key]
    
    return result

# Beispiel
config1 = {"timeout": 30, "retries": 3, "debug": False}
config2 = {"timeout": 60, "max_size": 1000, "debug": True}

# d2 gewinnt bei Konflikten
merged1 = merge_dicts(config1, config2)
print(merged1)
# {'timeout': 60, 'retries': 3, 'debug': True, 'max_size': 1000}

# Custom Resolver: Höherer Wert gewinnt
merged2 = merge_dicts(config1, config2, lambda v1, v2: max(v1, v2))
print(merged2)
# {'timeout': 60, 'retries': 3, 'debug': True, 'max_size': 1000}
```

### 3. Duplikate in Values finden

```python
def find_duplicate_values(d):
    """Findet Keys mit duplizierten Values"""
    value_to_keys = {}
    
    # Gruppiere Keys nach Values
    for key, value in d.items():
        # Value muss hashable sein
        try:
            if value not in value_to_keys:
                value_to_keys[value] = []
            value_to_keys[value].append(key)
        except TypeError:
            continue  # Unhashable value (z.B. list)
    
    # Nur Values mit mehreren Keys
    duplicates = {
        value: keys
        for value, keys in value_to_keys.items()
        if len(keys) > 1
    }
    
    return duplicates

# Beispiel
scores = {
    "Max": 85,
    "Anna": 92,
    "Tom": 85,
    "Lisa": 78,
    "Paul": 92
}

dups = find_duplicate_values(scores)
print(dups)
# {85: ['Max', 'Tom'], 92: ['Anna', 'Paul']}

for score, namen in dups.items():
    print(f"Score {score}: {', '.join(namen)}")
```

### 4. Dict Intersection - Gemeinsame Key-Value-Paare

```python
def dict_intersection(d1, d2):
    """Gibt Dict mit gemeinsamen Key-Value-Paaren zurück"""
    # Items die in beiden gleich sind
    common_items = d1.items() & d2.items()
    return dict(common_items)

# Beispiel
user1_prefs = {
    "theme": "dark",
    "language": "de",
    "notifications": True,
    "auto_save": True
}

user2_prefs = {
    "theme": "dark",
    "language": "en",
    "notifications": True,
    "font_size": 14
}

gemeinsam = dict_intersection(user1_prefs, user2_prefs)
print(gemeinsam)
# {'theme': 'dark', 'notifications': True}
```

### 5. Keys filtern basierend auf Values

```python
def filter_dict_by_values(d, predicate):
    """Filtert Dict basierend auf Value-Bedingung"""
    # Filtere items View
    filtered_items = (
        (k, v) for k, v in d.items() 
        if predicate(v)
    )
    return dict(filtered_items)

# Beispiel
preise = {
    "laptop": 800,
    "maus": 25,
    "tastatur": 60,
    "monitor": 300,
    "kabel": 10
}

# Nur teure Produkte (> 100)
teuer = filter_dict_by_values(preise, lambda v: v > 100)
print(teuer)  # {'laptop': 800, 'monitor': 300}

# Nur günstige Produkte
guenstig = filter_dict_by_values(preise, lambda v: v < 50)
print(guenstig)  # {'maus': 25, 'kabel': 10}
```

### 6. Dict-Statistiken aus Values

```python
def dict_value_stats(d):
    """Berechnet Statistiken über numerische Values"""
    values = list(d.values())
    
    if not values:
        return None
    
    # Nur numerische Values
    numeric_values = [v for v in values if isinstance(v, (int, float))]
    
    if not numeric_values:
        return None
    
    return {
        "count": len(numeric_values),
        "sum": sum(numeric_values),
        "min": min(numeric_values),
        "max": max(numeric_values),
        "avg": sum(numeric_values) / len(numeric_values)
    }

# Beispiel
sales = {
    "jan": 50000,
    "feb": 55000,
    "mar": 60000,
    "apr": 58000
}

stats = dict_value_stats(sales)
print(f"Durchschnitt: {stats['avg']:,.0f} €")
print(f"Min: {stats['min']:,} €")
print(f"Max: {stats['max']:,} €")
print(f"Gesamt: {stats['sum']:,} €")
```

---

## Performance-Überlegungen

### Views vs. Listen

```python
import timeit

d = {i: i for i in range(10000)}

# View erstellen: O(1)
def create_view():
    return d.keys()

# Liste erstellen: O(n)
def create_list():
    return list(d.keys())

print("View erstellen:", timeit.timeit(create_view, number=100000))
print("Liste erstellen:", timeit.timeit(create_list, number=100000))

# View: ~0.001s
# Liste: ~0.5s (500x langsamer!)

# Memory: View ist konstant, Liste wächst mit Dict-Größe
import sys
print(f"View Größe: {sys.getsizeof(d.keys())} bytes")
print(f"List Größe: {sys.getsizeof(list(d.keys()))} bytes")
```

### Membership-Tests

```python
import timeit

d = {i: i for i in range(10000)}

# Keys: O(1) durch Hash-Table
def test_key():
    return 5000 in d.keys()

# Values: O(n) muss durchsuchen
def test_value():
    return 5000 in d.values()

print("Key membership:", timeit.timeit(test_key, number=100000))
print("Value membership:", timeit.timeit(test_value, number=100000))

# Key: ~0.01s
# Value: ~1.5s (150x langsamer!)
```

---

## Häufige Fallstricke

### ⚠️ Fallstrick 1: Kein Index-Zugriff

```python
d = {"a": 1, "b": 2, "c": 3}
keys = d.keys()

# ❌ FALSCH - TypeError!
try:
    first_key = keys[0]
except TypeError as e:
    print(f"Fehler: {e}")

# ✅ RICHTIG - Zu Liste konvertieren
keys_list = list(keys)
first_key = keys_list[0]
print(first_key)  # 'a'

# ODER: next() mit iter()
first_key = next(iter(keys))
print(first_key)  # 'a'
```

### ⚠️ Fallstrick 2: Dict während View-Iteration ändern

```python
d = {"a": 1, "b": 2, "c": 3}

# ❌ FALSCH - RuntimeError!
try:
    for key in d.keys():
        if key == "b":
            del d[key]
except RuntimeError as e:
    print(f"Fehler: dictionary changed size during iteration")

# ✅ RICHTIG - Liste erstellen
d = {"a": 1, "b": 2, "c": 3}
for key in list(d.keys()):  # Liste!
    if key == "b":
        del d[key]

print(d)  # {'a': 1, 'c': 3}
```

### ⚠️ Fallstrick 3: values() hat keine Set-Operationen

```python
d1 = {"a": 1, "b": 2}
d2 = {"c": 2, "d": 3}

# ❌ FALSCH
try:
    union = d1.values() | d2.values()
except TypeError:
    print("values() unterstützt keine Set-Operationen!")

# ✅ RICHTIG - Zu Sets konvertieren
union = set(d1.values()) | set(d2.values())
print(union)  # {1, 2, 3}
```

---

## Best Practices

### ✅ Do's

```python
# 1. Views für Memory-Effizienz verwenden
for key in d.keys():  # Kein Speicher-Overhead
    process(key)

# 2. Set-Operationen mit keys()/items() nutzen
gemeinsam = dict1.keys() & dict2.keys()

# 3. Views für dynamische Sichten
keys = d.keys()  # Reflektiert Änderungen

# 4. Direkt über Dict iterieren wenn nur Keys benötigt
for key in d:  # Statt for key in d.keys()
    print(key)
```

### ❌ Don'ts

```python
# 1. Nicht zu Listen konvertieren wenn unnötig
keys_list = list(d.keys())  # Nur wenn Index-Zugriff nötig

# 2. Nicht während View-Iteration ändern
# for key in d.keys():
#     del d[key]  # RuntimeError!

# 3. Keine Set-Ops mit values()
# union = d1.values() | d2.values()  # TypeError!

# 4. Kein Index-Zugriff auf Views
# first = d.keys()[0]  # TypeError!
```

---

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [Dictionary view objects](https://docs.python.org/3/library/stdtypes.html#dictionary-view-objects) - Vollständige View-Referenz
- [Mapping Types](https://docs.python.org/3/library/stdtypes.html#mapping-types-dict) - Dictionary Operationen
- [Set Types](https://docs.python.org/3/library/stdtypes.html#set-types-set-frozenset) - Set-Operationen Referenz

---

## Verwandte Themen

- [[03_Mappings_und_Sets/01_Dictionaries/02_Dict Methoden|Dict Methoden]] - keys(), values(), items() Methoden
- [[03_Mappings_und_Sets/02_Sets/01_Set Basics|Set Basics]] - Set-Operationen im Detail
- [[03_Mappings_und_Sets/01_Dictionaries/01_Dict Basics|Dict Basics]] - Dictionary Grundlagen
- [[02_Sequenzen/04_Comprehensions/01_List Comprehensions|List Comprehensions]] - Iteration Patterns

---

← [[03_Mappings_und_Sets/01_Dictionaries/04_Nested Dicts|Nested Dicts]] | [[INDEX|📑 Index]] | [[03_Mappings_und_Sets/01_Dictionaries/06_OrderedDict vs Dict|OrderedDict vs Dict]] →