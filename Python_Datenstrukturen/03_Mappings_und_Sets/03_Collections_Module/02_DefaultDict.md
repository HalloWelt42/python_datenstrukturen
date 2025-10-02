---
tags: [python, datenstrukturen, collections, defaultdict]
created: 2025-10-01
---

← [[03_Mappings_und_Sets/03_Collections_Module/01_Counter|Counter]] | [[INDEX|📑 Index]] | [[03_Mappings_und_Sets/03_Collections_Module/03_ChainMap|ChainMap]] →

# DefaultDict

> [!tip] Lernziel
> Nach dieser Seite kannst du defaultdict effektiv nutzen um fehlende Keys elegant zu behandeln und Code zu vereinfachen.

## Was ist defaultdict?

**defaultdict** ist eine `dict`-Subklasse, die einen Default-Wert für fehlende Keys bereitstellt. Es ist Teil des `collections` Moduls.

**Kernmerkmale:**
- **Automatische Initialisierung**: Fehlende Keys werden automatisch erstellt
- **Factory-Funktion**: Definiert wie Default-Werte erstellt werden
- **Verhindert KeyError**: Kein KeyError bei fehlendem Zugriff
- **Dict-kompatibel**: Alle dict-Methoden verfügbar
- **Eleganter Code**: Weniger if-checks nötig

**Wofür verwendet:**
- Gruppierung von Daten
- Nested Dictionaries aufbauen
- Zähler ohne manuelle Initialisierung
- Listen von Values sammeln
- Komplexe Datenstrukturen vereinfachen

---

## Import und Erstellung

### DefaultDict erstellen

```python
from collections import defaultdict

# Mit list als Default-Factory
d = defaultdict(list)
print(d)  # defaultdict(<class 'list'>, {})

# Fehlender Key wird automatisch mit [] initialisiert
d['fruits'].append('apple')
print(d)  # defaultdict(<class 'list'>, {'fruits': ['apple']})

# Mit int als Default-Factory (für Zähler)
counter = defaultdict(int)
counter['a'] += 1  # Kein KeyError! Startet bei 0
print(counter)  # defaultdict(<class 'int'>, {'a': 1})

# Mit set als Default-Factory
tags = defaultdict(set)
tags['article1'].add('python')
print(tags)  # defaultdict(<class 'set'>, {'article1': {'python'}})

# Mit dict als Default-Factory (für nested dicts)
nested = defaultdict(dict)
nested['user1']['name'] = 'Alice'
print(nested)  # defaultdict(<class 'dict'>, {'user1': {'name': 'Alice'}})

# Mit lambda für komplexere Defaults
d = defaultdict(lambda: 'N/A')
print(d['missing'])  # 'N/A'

# Mit eigener Funktion
def default_value():
    return {'count': 0, 'items': []}

d = defaultdict(default_value)
print(d['key'])  # {'count': 0, 'items': []}
```

### Aus bestehendem Dict

```python
from collections import defaultdict

# Normales Dict
normal = {'a': 1, 'b': 2}

# Zu defaultdict konvertieren
d = defaultdict(int, normal)
print(d)  # defaultdict(<class 'int'>, {'a': 1, 'b': 2})

# Bestehende Keys bleiben erhalten
print(d['a'])  # 1

# Neue Keys werden mit Default initialisiert
print(d['c'])  # 0
print(d)  # defaultdict(<class 'int'>, {'a': 1, 'b': 2, 'c': 0})
```

---

## Default-Factory Funktionen

### Eingebaute Typen als Factory

```python
from collections import defaultdict

# list - Für Listen von Values
d = defaultdict(list)
d['key'].append(1)

# int - Für Zähler (startet bei 0)
d = defaultdict(int)
d['key'] += 1

# set - Für eindeutige Sammlungen
d = defaultdict(set)
d['key'].add(1)

# dict - Für nested Dictionaries
d = defaultdict(dict)
d['key']['subkey'] = 'value'

# str - Für String-Defaults (leer)
d = defaultdict(str)
print(d['key'])  # ''

# float - Für numerische Werte
d = defaultdict(float)
print(d['key'])  # 0.0

# tuple - ACHTUNG: Immer leeres Tuple!
d = defaultdict(tuple)
print(d['key'])  # ()
```

### Lambda-Ausdrücke

```python
from collections import defaultdict

# Konstanter Wert
d = defaultdict(lambda: 'Unknown')
print(d['missing'])  # 'Unknown'

# Liste mit Default-Werten
d = defaultdict(lambda: [0, 0, 0])
d['key'][0] = 1
print(d['key'])  # [1, 0, 0]

# Dict mit Struktur
d = defaultdict(lambda: {'name': '', 'age': 0, 'active': False})
print(d['user1'])  # {'name': '', 'age': 0, 'active': False}

# Set mit Default-Werten
d = defaultdict(lambda: {'admin', 'user'})
d['person1'].add('moderator')
print(d['person1'])  # {'admin', 'user', 'moderator'}

# Counter-ähnlich mit Custom Start
d = defaultdict(lambda: 100)  # Startet bei 100
d['item'] -= 5
print(d['item'])  # 95
```

### Eigene Funktionen

```python
from collections import defaultdict

# Funktion für komplexe Defaults
def create_user():
    return {
        'name': 'Guest',
        'permissions': set(),
        'login_count': 0,
        'last_seen': None
    }

users = defaultdict(create_user)
users['user1']['name'] = 'Alice'
users['user1']['permissions'].add('read')

print(users['user1'])
# {'name': 'Alice', 'permissions': {'read'}, 'login_count': 0, 'last_seen': None}

print(users['user2'])  # Neuer User mit Defaults
# {'name': 'Guest', 'permissions': set(), 'login_count': 0, 'last_seen': None}

# Factory mit Parametern (Closure)
def create_counter(start=0):
    def factory():
        return start
    return factory

d = defaultdict(create_counter(100))
d['item'] += 5
print(d['item'])  # 105
```

---

## Praktische Anwendungen

### 1. Gruppierung nach Kriterium

```python
from collections import defaultdict

# Studenten nach Klassen gruppieren
studenten = [
    {'name': 'Max', 'klasse': '10a'},
    {'name': 'Anna', 'klasse': '10b'},
    {'name': 'Tom', 'klasse': '10a'},
    {'name': 'Lisa', 'klasse': '10b'},
]

# Mit normalem Dict (umständlich)
klassen_normal = {}
for student in studenten:
    klasse = student['klasse']
    if klasse not in klassen_normal:
        klassen_normal[klasse] = []
    klassen_normal[klasse].append(student['name'])

# Mit defaultdict (elegant!)
klassen = defaultdict(list)
for student in studenten:
    klassen[student['klasse']].append(student['name'])

print(klassen)
# defaultdict(<class 'list'>, {'10a': ['Max', 'Tom'], '10b': ['Anna', 'Lisa']})

# Als normales Dict ausgeben
print(dict(klassen))
# {'10a': ['Max', 'Tom'], '10b': ['Anna', 'Lisa']}
```

### 2. Graph als Adjazenzliste

```python
from collections import defaultdict

# Graph mit defaultdict(set) für Kanten
graph = defaultdict(set)

# Kanten hinzufügen
edges = [
    ('A', 'B'),
    ('A', 'C'),
    ('B', 'D'),
    ('C', 'D'),
    ('D', 'E')
]

for start, end in edges:
    graph[start].add(end)
    graph[end].add(start)  # Ungerichteter Graph

print("Graph:")
for node, neighbors in sorted(graph.items()):
    print(f"  {node}: {sorted(neighbors)}")

# Nachbarn eines Knotens (auch wenn nicht im Graph)
print(f"Nachbarn von F: {graph['F']}")  # set() - leer, kein Error!

# Grad jedes Knotens
for node in graph:
    print(f"{node}: Grad {len(graph[node])}")
```

### 3. Nested Dictionary aufbauen

```python
from collections import defaultdict

# Verkaufsdaten nach Jahr > Monat > Produkt
verkauf = defaultdict(lambda: defaultdict(lambda: defaultdict(int)))

# Daten hinzufügen
verkauf[2024]['Januar']['Laptop'] += 5
verkauf[2024]['Januar']['Maus'] += 20
verkauf[2024]['Februar']['Laptop'] += 3
verkauf[2025]['Januar']['Laptop'] += 7

# Zugriff ohne KeyError
print(verkauf[2024]['Januar']['Laptop'])  # 5
print(verkauf[2024]['März']['Keyboard'])  # 0 (automatisch erstellt!)

# Als normale Dict-Struktur
print(dict(verkauf[2024]))

# Gesamtverkauf eines Produkts über alle Monate
def total_verkauf(daten, jahr, produkt):
    total = 0
    for monat in daten[jahr].values():
        total += monat[produkt]
    return total

print(f"Laptop 2024 gesamt: {total_verkauf(verkauf, 2024, 'Laptop')}")  # 8
```

### 4. Invertierter Index (Suchmaschine)

```python
from collections import defaultdict

# Dokumente
dokumente = {
    'doc1': 'Python ist eine tolle Programmiersprache',
    'doc2': 'Python macht Spaß und ist einfach',
    'doc3': 'JavaScript ist auch toll',
}

# Invertierter Index: Wort -> Liste von Dokumenten
index = defaultdict(set)

for doc_id, text in dokumente.items():
    for wort in text.lower().split():
        index[wort].add(doc_id)

# Suche nach Wort
def suche(wort):
    return index[wort.lower()]

print(f"'Python' kommt vor in: {suche('Python')}")
# {'doc1', 'doc2'}

print(f"'ist' kommt vor in: {suche('ist')}")
# {'doc1', 'doc2', 'doc3'}

# UND-Suche (beide Wörter müssen vorkommen)
def suche_und(wort1, wort2):
    return index[wort1.lower()] & index[wort2.lower()]

print(f"'Python' UND 'Spaß': {suche_und('Python', 'Spaß')}")
# {'doc2'}
```

### 5. Transaktions-Log

```python
from collections import defaultdict
from datetime import datetime

# Transaktionen nach User gruppieren
transaktionen = defaultdict(list)

def add_transaktion(user, betrag, beschreibung):
    transaktionen[user].append({
        'betrag': betrag,
        'beschreibung': beschreibung,
        'timestamp': datetime.now()
    })

# Transaktionen hinzufügen
add_transaktion('alice', -50, 'Einkauf')
add_transaktion('alice', -20, 'Restaurant')
add_transaktion('bob', -100, 'Miete')
add_transaktion('alice', 1000, 'Gehalt')

# Kontostand berechnen
def kontostand(user):
    return sum(t['betrag'] for t in transaktionen[user])

print(f"Alice Kontostand: {kontostand('alice')} EUR")  # 930
print(f"Bob Kontostand: {kontostand('bob')} EUR")      # -100

# Auch für neuen User ohne Error
print(f"Charlie Kontostand: {kontostand('charlie')} EUR")  # 0

# Transaktions-Historie
print(f"\nAlice Transaktionen ({len(transaktionen['alice'])} Stück):")
for t in transaktionen['alice']:
    print(f"  {t['betrag']:>6} EUR - {t['beschreibung']}")
```

### 6. Zählen mit Kategorien

```python
from collections import defaultdict

# Log-Einträge nach Level zählen
logs = [
    'ERROR: Connection failed',
    'INFO: User logged in',
    'ERROR: Timeout',
    'WARNING: Low memory',
    'ERROR: Connection failed',
    'INFO: User logged out',
]

# Zähler pro Level
level_counts = defaultdict(int)
level_messages = defaultdict(list)

for log in logs:
    level = log.split(':')[0]
    level_counts[level] += 1
    level_messages[level].append(log)

print("Log-Level Statistik:")
for level in ['ERROR', 'WARNING', 'INFO']:
    count = level_counts[level]
    print(f"  {level}: {count}")

# Häufigste ERROR-Meldungen
from collections import Counter
error_msgs = [msg.split(': ', 1)[1] for msg in level_messages['ERROR']]
print(f"\nHäufigste Fehler:")
for msg, count in Counter(error_msgs).most_common(3):
    print(f"  {count}x: {msg}")
```

---

## defaultdict vs. dict.get() / dict.setdefault()

### Vergleich

```python
from collections import defaultdict

# 1. Mit normalem Dict und get()
d = {}
for item in ['a', 'b', 'a', 'c', 'b', 'a']:
    d[item] = d.get(item, 0) + 1
print(d)  # {'a': 3, 'b': 2, 'c': 1}

# 2. Mit normalem Dict und setdefault()
d = {}
for item in ['a', 'b', 'a', 'c', 'b', 'a']:
    d.setdefault(item, 0)
    d[item] += 1
print(d)  # {'a': 3, 'b': 2, 'c': 1}

# 3. Mit defaultdict (am elegantesten!)
d = defaultdict(int)
for item in ['a', 'b', 'a', 'c', 'b', 'a']:
    d[item] += 1
print(dict(d))  # {'a': 3, 'b': 2, 'c': 1}

# Gruppierung: Noch deutlicher
# Dict mit setdefault
d = {}
for key, value in [('a', 1), ('b', 2), ('a', 3)]:
    d.setdefault(key, []).append(value)

# defaultdict (viel klarer!)
d = defaultdict(list)
for key, value in [('a', 1), ('b', 2), ('a', 3)]:
    d[key].append(value)
```

### Wann welches verwenden?

```python
# ✅ defaultdict: Wenn oft auf fehlende Keys zugegriffen wird
gruppierung = defaultdict(list)
for item in items:
    gruppierung[item.category].append(item)

# ✅ dict.get(): Für einzelne Default-Werte
config = {}
debug = config.get('debug', False)

# ✅ dict.setdefault(): Wenn Default gesetzt werden soll
d = {}
d.setdefault('key', []).append('value')

# ❌ Nicht defaultdict: Wenn KeyError gewünscht ist
strict_config = {}  # Fehlende Keys sollen Error werfen
# strict_config['typo']  # KeyError ist gut hier!
```

---

## Besonderheiten und Fallstricke

### ⚠️ Fallstrick 1: Default-Factory wird bei jedem Zugriff aufgerufen

```python
from collections import defaultdict

# ❌ Mutable Default - wird geteilt!
def get_list():
    return ['default']  # Neue Liste jedes Mal - OK

d = defaultdict(get_list)
d['a'].append('item')
d['b'].append('item')

print(d['a'])  # ['default', 'item'] - OK
print(d['b'])  # ['default', 'item'] - OK, separate Listen

# ✅ Aber: Vorsicht bei Shared State
counter = {'calls': 0}

def tracked_factory():
    counter['calls'] += 1
    return []

d = defaultdict(tracked_factory)
_ = d['a']
_ = d['b']
print(f"Factory aufgerufen: {counter['calls']}x")  # 2x
```

### ⚠️ Fallstrick 2: Lesezugriff erstellt Keys

```python
from collections import defaultdict

d = defaultdict(int)

# Einfacher Lesezugriff erstellt Key!
value = d['missing']
print(value)  # 0
print('missing' in d)  # True - Key wurde erstellt!

# Vermeiden mit regulärem Dict
d_normal = {}
value = d_normal.get('missing', 0)
print('missing' in d_normal)  # False - Key nicht erstellt

# Oder: defaultdict nur für Schreiboperationen nutzen
d = defaultdict(int)
if 'key' in d:  # Check zuerst
    value = d['key']
```

### ⚠️ Fallstrick 3: Serialisierung (JSON, pickle)

```python
from collections import defaultdict
import json

d = defaultdict(list)
d['a'].append(1)
d['b'].append(2)

# ❌ JSON serialisiert defaultdict als normales dict
json_str = json.dumps(d)
loaded = json.loads(json_str)
print(type(loaded))  # <class 'dict'> - kein defaultdict mehr!

# ✅ Lösung: Explizit zurück konvertieren
loaded_dd = defaultdict(list, loaded)
print(type(loaded_dd))  # <class 'collections.defaultdict'>

# Oder: Als normales Dict behandeln wenn OK
loaded_dict = dict(d)  # Explizite Konvertierung
```

### ⚠️ Fallstrick 4: default_factory = None

```python
from collections import defaultdict

# defaultdict ohne Factory
d = defaultdict()  # Kein Argument
print(d.default_factory)  # None

# Verhält sich wie normales dict
try:
    value = d['missing']
except KeyError:
    print("KeyError! Verhält sich wie dict")

# Factory kann auch später gesetzt werden
d.default_factory = list
d['key'].append(1)
print(d)  # defaultdict(<class 'list'>, {'key': [1]})

# Oder deaktiviert werden
d.default_factory = None
try:
    d['new_key'].append(2)
except KeyError:
    print("Factory deaktiviert - KeyError!")
```

---

## Performance

### defaultdict vs. dict Operationen

```python
import timeit
from collections import defaultdict

data = [('a', 1), ('b', 2), ('a', 3), ('c', 4)] * 100

# Mit dict und setdefault
def mit_setdefault():
    d = {}
    for key, value in data:
        d.setdefault(key, []).append(value)
    return d

# Mit dict und get
def mit_get():
    d = {}
    for key, value in data:
        if key not in d:
            d[key] = []
        d[key].append(value)
    return d

# Mit defaultdict
def mit_defaultdict():
    d = defaultdict(list)
    for key, value in data:
        d[key].append(value)
    return d

# Benchmark
setdefault_time = timeit.timeit(mit_setdefault, number=10000)
get_time = timeit.timeit(mit_get, number=10000)
defaultdict_time = timeit.timeit(mit_defaultdict, number=10000)

print(f"setdefault(): {setdefault_time:.3f}s")
print(f"if/get:       {get_time:.3f}s")
print(f"defaultdict:  {defaultdict_time:.3f}s")

# defaultdict ist am schnellsten!
```

---

## Zu normalem Dict konvertieren

```python
from collections import defaultdict

# defaultdict erstellen
dd = defaultdict(list)
dd['a'].append(1)
dd['b'].append(2)

# Methode 1: dict() Konstruktor
normal1 = dict(dd)
print(type(normal1))  # <class 'dict'>

# Methode 2: Dict Comprehension
normal2 = {k: v for k, v in dd.items()}

# Methode 3: copy() gibt auch normales dict
normal3 = dd.copy()
print(type(normal3))  # <class 'dict'> in Python 3.9+

# Nested defaultdicts rekursiv konvertieren
def to_dict(d):
    if isinstance(d, defaultdict):
        d = {k: to_dict(v) for k, v in d.items()}
    return d

nested = defaultdict(lambda: defaultdict(list))
nested['a']['b'].append(1)

normal_nested = to_dict(nested)
print(type(normal_nested))  # <class 'dict'>
print(type(normal_nested['a']))  # <class 'dict'>
```

---

## Erweiterte Patterns

### Mehrfach verschachtelte defaultdicts

```python
from collections import defaultdict

# Funktion für beliebige Verschachtelungstiefe
def nested_defaultdict():
    return defaultdict(nested_defaultdict)

# 3-fach verschachtelt
data = nested_defaultdict()
data['level1']['level2']['level3'] = 'value'

print(data['level1']['level2']['level3'])  # 'value'
print(data['new']['path']['here'])  # defaultdict - automatisch erstellt!

# Mit Typ am Ende
def tree():
    return defaultdict(tree)

# Oder mit Counter am Ende
from collections import Counter
def counted_tree():
    return defaultdict(lambda: defaultdict(Counter))

stats = counted_tree()
stats['2024']['Januar']['Laptop'] += 5
stats['2024']['Januar']['Maus'] += 10

print(dict(stats['2024']['Januar']))
# {'Laptop': 5, 'Maus': 10}
```

---

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [collections.defaultdict](https://docs.python.org/3/library/collections.html#collections.defaultdict) - Vollständige Referenz
- [defaultdict Examples](https://docs.python.org/3/library/collections.html#defaultdict-examples) - Praktische Beispiele
- [collections module](https://docs.python.org/3/library/collections.html) - Alle Collections

---

## Verwandte Themen

- [[03_Mappings_und_Sets/03_Collections_Module/01_Counter|Counter]] - Andere dict-Spezialisierung
- [[03_Mappings_und_Sets/01_Dictionaries/01_Dict Basics|Dict Basics]] - Basis-Funktionalität
- [[03_Mappings_und_Sets/01_Dictionaries/02_Dict Methoden|Dict Methoden]] - setdefault() Alternative
- [[03_Mappings_und_Sets/03_Collections_Module/03_ChainMap|ChainMap]] - Weitere Collection

---

← [[03_Mappings_und_Sets/03_Collections_Module/01_Counter|Counter]] | [[INDEX|📑 Index]] | [[03_Mappings_und_Sets/03_Collections_Module/03_ChainMap|ChainMap]] →