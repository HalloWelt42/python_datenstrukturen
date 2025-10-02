---
tags: [python, datenstrukturen, mappings, dictionaries, ordereddict, collections]
created: 2025-10-01
---

← [[03_Mappings_und_Sets/01_Dictionaries/05_Dict Views|Dict Views]] | [[INDEX|📑 Index]] | [[03_Mappings_und_Sets/01_Dictionaries/07_Dict Performance|Dict Performance]] →

# OrderedDict vs. Dict

> [!tip] Lernziel
> Nach dieser Seite verstehst du den historischen Kontext von OrderedDict, die Änderungen in Python 3.7+, und wann du heute noch OrderedDict brauchst.

## Historischer Kontext

### Vor Python 3.7

```python
# Python 3.6 und früher: Dict hatte KEINE garantierte Reihenfolge!

# Das Verhalten war "implementation detail" und unvorhersehbar
d = {}
d["z"] = 1
d["a"] = 2
d["m"] = 3

# Reihenfolge war zufällig/undefiniert
print(d)  # Könnte sein: {'a': 2, 'm': 3, 'z': 1} oder irgendwas

# Deshalb wurde OrderedDict eingeführt (Python 2.7 / 3.1)
from collections import OrderedDict

od = OrderedDict()
od["z"] = 1
od["a"] = 2
od["m"] = 3

print(od)  # GARANTIERT: OrderedDict([('z', 1), ('a', 2), ('m', 3)])
```

### Seit Python 3.7+

```python
# Python 3.7+: Dict behält Insertion-Order bei!

d = {}
d["z"] = 1
d["a"] = 2
d["m"] = 3

print(d)  # GARANTIERT: {'z': 1, 'a': 2, 'm': 3}

# Dies ist jetzt Teil der Sprachspezifikation, nicht nur
# ein Implementation-Detail!
```

> [!note] Wichtige Änderung
> Seit Python 3.7 ist die **Insertion-Order** von `dict` Teil der offiziellen Sprachspezifikation. In Python 3.6 war es bereits implementiert, aber nur als "implementation detail" der CPython-Version.

---

## OrderedDict Grundlagen

### Import und Erstellung

```python
from collections import OrderedDict

# Verschiedene Erstellungsmethoden
od1 = OrderedDict()
od1["a"] = 1
od1["b"] = 2

od2 = OrderedDict([("a", 1), ("b", 2), ("c", 3)])

od3 = OrderedDict(a=1, b=2, c=3)
# ⚠️ Vorsicht: Keyword args Reihenfolge ist seit 3.7+ garantiert

# Aus Dict erstellen
regular_dict = {"x": 1, "y": 2}
od4 = OrderedDict(regular_dict)

print(od1)  # OrderedDict([('a', 1), ('b', 2)])
print(od2)  # OrderedDict([('a', 1), ('b', 2), ('c', 3)])
```

### String-Repräsentation

```python
from collections import OrderedDict

# OrderedDict zeigt anders an
od = OrderedDict([("a", 1), ("b", 2)])
d = {"a": 1, "b": 2}

print(od)  # OrderedDict([('a', 1), ('b', 2)])
print(d)   # {'a': 1, 'b': 2}

# Beide haben gleiche Funktionalität für Zugriff
print(od["a"])  # 1
print(d["a"])   # 1
```

---

## Unterschiede zwischen dict und OrderedDict

### 1. Gleichheit und Vergleich

```python
from collections import OrderedDict

# dict: Reihenfolge spielt KEINE Rolle beim Vergleich
d1 = {"a": 1, "b": 2}
d2 = {"b": 2, "a": 1}

print(d1 == d2)  # True - Nur Keys und Values zählen

# OrderedDict: Reihenfolge spielt eine Rolle!
od1 = OrderedDict([("a", 1), ("b", 2)])
od2 = OrderedDict([("b", 2), ("a", 1)])

print(od1 == od2)  # False - Unterschiedliche Reihenfolge!

# OrderedDict mit dict vergleichen
od = OrderedDict([("a", 1), ("b", 2)])
d = {"a": 1, "b": 2}

print(od == d)  # True - Reihenfolge wird ignoriert bei Vergleich mit dict!
```

### 2. move_to_end() Methode

```python
from collections import OrderedDict

# Nur OrderedDict hat move_to_end()!
od = OrderedDict([("a", 1), ("b", 2), ("c", 3)])

# Element ans Ende verschieben
od.move_to_end("a")
print(od)  # OrderedDict([('b', 2), ('c', 3), ('a', 1)])

# Element an den Anfang verschieben
od.move_to_end("c", last=False)
print(od)  # OrderedDict([('c', 3), ('b', 2), ('a', 1)])

# Bei normalem dict: Nicht verfügbar!
d = {"a": 1, "b": 2, "c": 3}
try:
    d.move_to_end("a")
except AttributeError:
    print("dict hat keine move_to_end() Methode!")
```

### 3. Reihenfolge bei popitem()

```python
from collections import OrderedDict

# dict: Entfernt LETZTES Element (LIFO)
d = {"a": 1, "b": 2, "c": 3}
print(d.popitem())  # ('c', 3)
print(d)            # {'a': 1, 'b': 2}

# OrderedDict: Kann beide Richtungen!
od = OrderedDict([("a", 1), ("b", 2), ("c", 3)])

# LIFO (Standard, wie dict)
print(od.popitem())  # ('c', 3)
print(od)            # OrderedDict([('a', 1), ('b', 2)])

# FIFO (nur OrderedDict!)
od = OrderedDict([("a", 1), ("b", 2), ("c", 3)])
print(od.popitem(last=False))  # ('a', 1) - Erstes Element!
print(od)                       # OrderedDict([('b', 2), ('c', 3)])
```

### 4. Memory-Overhead

```python
import sys
from collections import OrderedDict

# OrderedDict braucht mehr Speicher
d = {i: i for i in range(1000)}
od = OrderedDict((i, i) for i in range(1000))

print(f"dict:        {sys.getsizeof(d):,} bytes")
print(f"OrderedDict: {sys.getsizeof(od):,} bytes")

# OrderedDict: ~20-30% mehr Speicher
# Grund: Zusätzliche verkettete Liste für Reihenfolge
```

### 5. Performance

```python
import timeit
from collections import OrderedDict

# Setup
setup = """
from collections import OrderedDict
d = {i: i for i in range(1000)}
od = OrderedDict((i, i) for i in range(1000))
"""

# Zugriff
print("Zugriff:")
print("  dict:", timeit.timeit("d[500]", setup=setup, number=100000))
print("  OrderedDict:", timeit.timeit("od[500]", setup=setup, number=100000))

# Insertion
print("\nInsertion:")
print("  dict:", timeit.timeit("d[1001] = 1001", setup=setup, number=100000))
print("  OrderedDict:", timeit.timeit("od[1001] = 1001", setup=setup, number=100000))

# dict ist in allen Operationen schneller!
```

---

## Wann welchen verwenden?

### Verwende dict (Standard-Fall)

```python
# ✅ In den meisten Fällen ist dict die richtige Wahl:

# 1. Normale Key-Value Speicherung
user = {"name": "Max", "age": 30}

# 2. Konfigurationen
config = {
    "debug": True,
    "host": "localhost",
    "port": 8000
}

# 3. Caching
cache = {}

# 4. Zählen
counter = {}
for word in words:
    counter[word] = counter.get(word, 0) + 1

# Seit Python 3.7+ behält dict die Reihenfolge automatisch!
```

### Verwende OrderedDict wenn:

#### 1. Reihenfolge in Gleichheit wichtig ist

```python
from collections import OrderedDict

# Bei Tests wo Reihenfolge relevant ist
def test_api_response():
    expected = OrderedDict([
        ("status", "ok"),
        ("data", {...}),
        ("timestamp", "...")
    ])
    
    actual = api_call()
    
    # Vergleicht auch Reihenfolge!
    assert expected == actual

# Mit dict würde Reihenfolge ignoriert werden
```

#### 2. move_to_end() benötigt wird

```python
from collections import OrderedDict

# LRU Cache Implementation (vereinfacht)
class LRUCache:
    def __init__(self, capacity):
        self.cache = OrderedDict()
        self.capacity = capacity
    
    def get(self, key):
        if key not in self.cache:
            return None
        
        # Element ans Ende (als "recently used")
        self.cache.move_to_end(key)
        return self.cache[key]
    
    def put(self, key, value):
        if key in self.cache:
            # Aktualisieren und ans Ende
            self.cache.move_to_end(key)
        
        self.cache[key] = value
        
        # Ältestes entfernen wenn zu voll
        if len(self.cache) > self.capacity:
            self.cache.popitem(last=False)  # FIFO

# Verwendung
cache = LRUCache(3)
cache.put("a", 1)
cache.put("b", 2)
cache.put("c", 3)
print(cache.cache)  # OrderedDict([('a', 1), ('b', 2), ('c', 3)])

cache.get("a")  # Zugriff auf 'a'
print(cache.cache)  # OrderedDict([('b', 2), ('c', 3), ('a', 1)])

cache.put("d", 4)  # 'b' wird entfernt (älteste)
print(cache.cache)  # OrderedDict([('c', 3), ('a', 1), ('d', 4)])
```

#### 3. FIFO popitem() benötigt wird

```python
from collections import OrderedDict

# Queue-ähnliches Verhalten
tasks = OrderedDict()
tasks["task1"] = "E-Mail senden"
tasks["task2"] = "Bericht schreiben"
tasks["task3"] = "Meeting vorbereiten"

# Aufgaben in Reihenfolge abarbeiten (FIFO)
while tasks:
    task_id, beschreibung = tasks.popitem(last=False)
    print(f"Erledige: {task_id} - {beschreibung}")

# Mit dict: Nur LIFO möglich (letztes Element zuerst)
```

#### 4. Abwärtskompatibilität mit Python < 3.7

```python
from collections import OrderedDict

# Wenn Code auf Python 3.6 oder älter laufen muss
# und Reihenfolge wichtig ist:
od = OrderedDict([("first", 1), ("second", 2)])

# Mit dict in Python 3.6: Reihenfolge nicht garantiert!
```

#### 5. Explizite Dokumentation der Intention

```python
from collections import OrderedDict

# Macht explizit klar, dass Reihenfolge wichtig ist
class ConfigReader:
    def __init__(self):
        # Reihenfolge ist Teil der Semantik
        self.sections = OrderedDict()
    
    def add_section(self, name, config):
        self.sections[name] = config
    
    def write_config(self):
        # Sections werden in Reihenfolge geschrieben
        for name, config in self.sections.items():
            write_section(name, config)

# Lesbarkeit: OrderedDict signalisiert "Reihenfolge ist wichtig"
```

---

## Praktische Beispiele

### Beispiel 1: JSON mit garantierter Reihenfolge

```python
import json
from collections import OrderedDict

# JSON parsen mit Reihenfolge
json_string = '{"name": "Max", "age": 30, "city": "Berlin"}'

# Standard dict (Reihenfolge seit 3.7+ erhalten)
data = json.loads(json_string)
print(data)  # {'name': 'Max', 'age': 30, 'city': 'Berlin'}

# Explizit OrderedDict (für ältere Python-Versionen)
data_ordered = json.loads(json_string, object_pairs_hook=OrderedDict)
print(data_ordered)
# OrderedDict([('name', 'Max'), ('age', 30), ('city', 'Berlin')])

# JSON schreiben
output = json.dumps(data_ordered, indent=2)
print(output)
# {
#   "name": "Max",
#   "age": 30,
#   "city": "Berlin"
# }
```

### Beispiel 2: Config-File mit Sections-Reihenfolge

```python
from collections import OrderedDict

class INIConfig:
    """INI-Config mit Sections in definierter Reihenfolge"""
    
    def __init__(self):
        self.sections = OrderedDict()
    
    def add_section(self, name):
        if name not in self.sections:
            self.sections[name] = OrderedDict()
    
    def set(self, section, key, value):
        if section not in self.sections:
            self.add_section(section)
        self.sections[section][key] = value
    
    def write(self):
        lines = []
        for section, keys in self.sections.items():
            lines.append(f"[{section}]")
            for key, value in keys.items():
                lines.append(f"{key} = {value}")
            lines.append("")  # Leerzeile
        return "\n".join(lines)

# Verwendung
config = INIConfig()
config.set("Database", "host", "localhost")
config.set("Database", "port", "5432")
config.set("Server", "host", "0.0.0.0")
config.set("Server", "port", "8000")

print(config.write())
# [Database]
# host = localhost
# port = 5432
#
# [Server]
# host = 0.0.0.0
# port = 8000
```

### Beispiel 3: Sortiertes Dictionary

```python
from collections import OrderedDict

def sorted_dict(d, by_key=True, reverse=False):
    """Erstellt sortiertes OrderedDict"""
    if by_key:
        sorted_items = sorted(d.items(), key=lambda x: x[0], reverse=reverse)
    else:
        sorted_items = sorted(d.items(), key=lambda x: x[1], reverse=reverse)
    
    return OrderedDict(sorted_items)

# Beispiel
scores = {"Tom": 85, "Max": 92, "Anna": 78, "Lisa": 95}

# Nach Namen sortiert
by_name = sorted_dict(scores, by_key=True)
print(by_name)
# OrderedDict([('Anna', 78), ('Lisa', 95), ('Max', 92), ('Tom', 85)])

# Nach Score sortiert (absteigend)
by_score = sorted_dict(scores, by_key=False, reverse=True)
print(by_score)
# OrderedDict([('Lisa', 95), ('Max', 92), ('Tom', 85), ('Anna', 78)])

# Iteration behält Reihenfolge
for name, score in by_score.items():
    print(f"{name}: {score}")
```

### Beispiel 4: Reordering von Elementen

```python
from collections import OrderedDict

# Menu-Items die umsortiert werden können
menu = OrderedDict([
    ("home", "Startseite"),
    ("about", "Über uns"),
    ("services", "Dienstleistungen"),
    ("contact", "Kontakt")
])

print("Original:", list(menu.keys()))

# "services" nach oben
menu.move_to_end("services", last=False)
print("Nach oben:", list(menu.keys()))

# "about" nach unten
menu.move_to_end("about", last=True)
print("Nach unten:", list(menu.keys()))

# Ausgabe:
# Original: ['home', 'about', 'services', 'contact']
# Nach oben: ['services', 'home', 'about', 'contact']
# Nach unten: ['services', 'home', 'contact', 'about']
```

### Beispiel 5: Priority Queue mit OrderedDict

```python
from collections import OrderedDict

class PriorityDict:
    """Dictionary mit Priority-basierten Operations"""
    
    def __init__(self):
        self.items = OrderedDict()
        self.priorities = {}
    
    def add(self, key, value, priority=0):
        self.items[key] = value
        self.priorities[key] = priority
        self._reorder()
    
    def _reorder(self):
        """Sortiert nach Priority"""
        sorted_keys = sorted(
            self.items.keys(),
            key=lambda k: self.priorities[k],
            reverse=True
        )
        new_items = OrderedDict()
        for key in sorted_keys:
            new_items[key] = self.items[key]
        self.items = new_items
    
    def pop_highest(self):
        """Entfernt und gibt Item mit höchster Priority zurück"""
        if not self.items:
            return None
        key, value = self.items.popitem(last=False)
        del self.priorities[key]
        return key, value

# Verwendung
pq = PriorityDict()
pq.add("task1", "E-Mail", priority=2)
pq.add("task2", "Meeting", priority=5)
pq.add("task3", "Bericht", priority=3)

print("Items nach Priority:")
for key, value in pq.items.items():
    print(f"{key}: {value} (Priority: {pq.priorities[key]})")

# Items nach Priority:
# task2: Meeting (Priority: 5)
# task3: Bericht (Priority: 3)
# task1: E-Mail (Priority: 2)

# Höchste Priority holen
key, value = pq.pop_highest()
print(f"\nEntfernt: {key} - {value}")
```

---

## Konvertierung zwischen dict und OrderedDict

```python
from collections import OrderedDict

# dict → OrderedDict
d = {"a": 1, "b": 2, "c": 3}
od = OrderedDict(d)
print(od)  # OrderedDict([('a', 1), ('b', 2), ('c', 3)])

# OrderedDict → dict
od = OrderedDict([("x", 10), ("y", 20)])
d = dict(od)
print(d)  # {'x': 10, 'y': 20}

# Oder einfach:
d = {**od}  # Dict unpacking
print(d)  # {'x': 10, 'y': 20}

# Beide Richtungen behalten Reihenfolge in Python 3.7+
```

---

## Performance-Vergleich

```python
import timeit
from collections import OrderedDict

# Setup für verschiedene Größen
sizes = [100, 1000, 10000]

for size in sizes:
    print(f"\n=== Size: {size} ===")
    
    # Creation
    dict_time = timeit.timeit(
        f"d = {{i: i for i in range({size})}}",
        number=1000
    )
    od_time = timeit.timeit(
        f"od = OrderedDict((i, i) for i in range({size}))",
        setup="from collections import OrderedDict",
        number=1000
    )
    
    print(f"Creation:")
    print(f"  dict: {dict_time:.4f}s")
    print(f"  OrderedDict: {od_time:.4f}s ({od_time/dict_time:.1f}x)")
    
    # Access
    setup = f"""
from collections import OrderedDict
d = {{i: i for i in range({size})}}
od = OrderedDict((i, i) for i in range({size}))
"""
    
    dict_time = timeit.timeit(
        f"d[{size//2}]",
        setup=setup,
        number=100000
    )
    od_time = timeit.timeit(
        f"od[{size//2}]",
        setup=setup,
        number=100000
    )
    
    print(f"Access:")
    print(f"  dict: {dict_time:.4f}s")
    print(f"  OrderedDict: {od_time:.4f}s ({od_time/dict_time:.1f}x)")

# dict ist durchgehend schneller!
```

---

## Zusammenfassung: Entscheidungshilfe

```python
# Flowchart zur Entscheidung:

"""
Brauchst du move_to_end() oder popitem(last=False)?
│
├─ JA → Verwende OrderedDict
│
└─ NEIN
    │
    Ist Reihenfolge Teil der Gleichheit?
    │
    ├─ JA → Verwende OrderedDict
    │
    └─ NEIN
        │
        Python Version < 3.7?
        │
        ├─ JA → Verwende OrderedDict (wenn Reihenfolge wichtig)
        │
        └─ NEIN → Verwende dict
"""

# In 95% der Fälle: Verwende dict
# OrderedDict nur für spezielle Use-Cases
```

---

## Häufige Fallstricke

### ⚠️ Fallstrick 1: Unnötige Verwendung von OrderedDict

```python
from collections import OrderedDict

# ❌ UNNÖTIG in Python 3.7+
od = OrderedDict([("a", 1), ("b", 2)])
for key in od:
    print(key)  # Reihenfolge garantiert

# ✅ BESSER - dict reicht!
d = {"a": 1, "b": 2}
for key in d:
    print(key)  # Reihenfolge seit 3.7+ auch garantiert!

# OrderedDict nur wenn spezielle Features benötigt
```

### ⚠️ Fallstrick 2: Performance ignorieren

```python
from collections import OrderedDict

# ❌ SCHLECHT - Unnötiger Overhead
# Wenn nur Reihenfolge wichtig, aber keine OrderedDict-Features
od = OrderedDict()
for i in range(1000000):
    od[i] = i

# ✅ BESSER
d = {}
for i in range(1000000):
    d[i] = i

# dict ist schneller und braucht weniger Speicher!
```

### ⚠️ Fallstrick 3: Gleichheit missverstehen

```python
from collections import OrderedDict

# OrderedDict mit OrderedDict: Reihenfolge wichtig
od1 = OrderedDict([("a", 1), ("b", 2)])
od2 = OrderedDict([("b", 2), ("a", 1)])
print(od1 == od2)  # False

# OrderedDict mit dict: Reihenfolge egal!
od = OrderedDict([("a", 1), ("b", 2)])
d = {"b": 2, "a": 1}
print(od == d)  # True (!!)

# Verwirrend: Asymmetrisches Verhalten
```

---

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [collections.OrderedDict](https://docs.python.org/3/library/collections.html#collections.OrderedDict) - Vollständige OrderedDict Referenz
- [PEP 468 – Preserving Keyword Argument Order](https://peps.python.org/pep-0468/) - Warum Reihenfolge wichtig wurde
- [What's New In Python 3.7](https://docs.python.org/3/whatsnew/3.7.html) - Dict Order Guarantee
- [collections module](https://docs.python.org/3/library/collections.html) - Alle Collection-Typen

---

## Verwandte Themen

- [[03_Mappings_und_Sets/01_Dictionaries/01_Dict Basics|Dict Basics]] - Grundlagen von Dictionaries
- [[03_Mappings_und_Sets/01_Dictionaries/02_Dict Methoden|Dict Methoden]] - Methoden die beide teilen
- [[03_Mappings_und_Sets/03_Collections_Module/02_DefaultDict|DefaultDict]] - Anderer spezialisierter Dict-Typ
- [[03_Mappings_und_Sets/01_Dictionaries/07_Dict Performance|Dict Performance]] - Performance-Details

---

← [[03_Mappings_und_Sets/01_Dictionaries/05_Dict Views|Dict Views]] | [[INDEX|📑 Index]] | [[03_Mappings_und_Sets/01_Dictionaries/07_Dict Performance|Dict Performance]] →