---
tags: [python, datenstrukturen, mappings, dictionaries, übungen]
created: 2025-10-01
---

← [[03_Mappings_und_Sets/01_Dictionaries/07_Dict Performance|Dict Performance]] | [[INDEX|📑 Index]] | [[03_Mappings_und_Sets/02_Sets/01_Set Basics|Set Basics]] →

# Übungen: Dictionaries

> [!tip] Lernziel
> Diese Übungen helfen dir, Dictionaries in verschiedenen Szenarien sicher anzuwenden und ihre volle Leistungsfähigkeit zu nutzen.

## 📝 Übung 1: Wörter zählen (Leicht)

Erstelle eine Funktion `zaehle_woerter(text)`, die alle Wörter in einem Text zählt (case-insensitive).

**Anforderungen:**
- Ignoriere Groß-/Kleinschreibung
- Entferne Satzzeichen
- Gib Dictionary mit Wort -> Anzahl zurück

```python
def zaehle_woerter(text):
    # Dein Code hier
    pass

# Test
text = "Python ist toll! Python macht Spaß. Python, Python, Python!"
ergebnis = zaehle_woerter(text)
print(ergebnis)
```

**Erwarteter Output:**
```
{'python': 5, 'ist': 1, 'toll': 1, 'macht': 1, 'spaß': 1}
```

---

## 📝 Übung 2: Dictionary invertieren (Leicht-Mittel)

Schreibe eine Funktion `invertiere_dict(d)`, die Keys und Values vertauscht.

**Anforderungen:**
- Bei doppelten Values: Erstelle Liste von Keys
- Behandle nur hashbare Values
- Ignoriere unhashbare Values

```python
def invertiere_dict(d):
    # Dein Code hier
    pass

# Test
original = {"a": 1, "b": 2, "c": 1, "d": 3}
invertiert = invertiere_dict(original)
print(invertiert)

original2 = {"Max": 85, "Anna": 92, "Tom": 85, "Lisa": 78}
invertiert2 = invertiere_dict(original2)
print(invertiert2)
```

**Erwarteter Output:**
```
{1: ['a', 'c'], 2: ['b'], 3: ['d']}
{85: ['Max', 'Tom'], 92: ['Anna'], 78: ['Lisa']}
```

---

## 📝 Übung 3: Nested Dict Navigation (Mittel)

Erstelle eine Funktion `hole_wert(d, pfad, default=None)`, die sicher auf verschachtelte Dictionaries zugreift.

**Anforderungen:**
- Pfad ist String mit Punkten: "user.address.city"
- Gibt default zurück wenn Pfad nicht existiert
- Behandelt fehlende Keys elegant

```python
def hole_wert(d, pfad, default=None):
    # Dein Code hier
    pass

# Test
data = {
    "user": {
        "name": "Max",
        "address": {
            "city": "Berlin",
            "zip": "10115"
        },
        "settings": {
            "theme": "dark"
        }
    }
}

print(hole_wert(data, "user.name"))              # Max
print(hole_wert(data, "user.address.city"))      # Berlin
print(hole_wert(data, "user.email", "N/A"))      # N/A
print(hole_wert(data, "user.address.country"))   # None
```

**Erwarteter Output:**
```
Max
Berlin
N/A
None
```

---

## 📝 Übung 4: Dict Merge mit Regeln (Mittel)

Schreibe eine Funktion `merge_configs(default, user, rules)`, die zwei Config-Dicts merged.

**Anforderungen:**
- default: Default-Konfiguration
- user: User-Overrides
- rules: Dict mit Merge-Strategien pro Key
  - "override": User-Wert gewinnt
  - "combine": Werte zusammenführen (für Listen/Sets)
  - "max"/"min": Numerischer Vergleich

```python
def merge_configs(default, user, rules):
    # Dein Code hier
    pass

# Test
default = {
    "timeout": 30,
    "retries": 3,
    "features": ["api", "cache"],
    "max_size": 1000
}

user = {
    "timeout": 60,
    "retries": 5,
    "features": ["api", "admin"],
    "max_size": 500
}

rules = {
    "timeout": "max",
    "retries": "override",
    "features": "combine",
    "max_size": "min"
}

result = merge_configs(default, user, rules)
print(result)
```

**Erwarteter Output:**
```
{
    'timeout': 60,
    'retries': 5,
    'features': ['api', 'cache', 'admin'],
    'max_size': 500
}
```

---

## 📝 Übung 5: Gruppierung (Mittel)

Erstelle eine Funktion `gruppiere_nach(items, key_func)`, die Items nach einem Kriterium gruppiert.

**Anforderungen:**
- items: Liste von Dictionaries
- key_func: Funktion die Key aus Item extrahiert
- Gibt Dict mit Key -> Liste von Items zurück

```python
def gruppiere_nach(items, key_func):
    # Dein Code hier
    pass

# Test
studenten = [
    {"name": "Max", "klasse": "10a", "note": 2},
    {"name": "Anna", "klasse": "10b", "note": 1},
    {"name": "Tom", "klasse": "10a", "note": 3},
    {"name": "Lisa", "klasse": "10b", "note": 2},
    {"name": "Paul", "klasse": "10a", "note": 1}
]

# Nach Klasse gruppieren
nach_klasse = gruppiere_nach(studenten, lambda s: s["klasse"])
print(nach_klasse)

# Nach Note gruppieren
nach_note = gruppiere_nach(studenten, lambda s: s["note"])
print(nach_note)
```

**Erwarteter Output:**
```
{
    '10a': [
        {'name': 'Max', 'klasse': '10a', 'note': 2},
        {'name': 'Tom', 'klasse': '10a', 'note': 3},
        {'name': 'Paul', 'klasse': '10a', 'note': 1}
    ],
    '10b': [
        {'name': 'Anna', 'klasse': '10b', 'note': 1},
        {'name': 'Lisa', 'klasse': '10b', 'note': 2}
    ]
}
{1: [{'name': 'Anna', ...}, {'name': 'Paul', ...}], 2: [...], 3: [...]}
```

---

## 📝 Übung 6: Dict Diff (Mittel-Schwer)

Schreibe eine Funktion `dict_diff(d1, d2)`, die detaillierte Unterschiede zwischen zwei Dicts findet.

**Anforderungen:**
- Findet hinzugefügte Keys
- Findet entfernte Keys
- Findet geänderte Values
- Findet unveränderte Keys
- Gibt strukturiertes Dict zurück

```python
def dict_diff(d1, d2):
    # Dein Code hier
    pass

# Test
alt = {
    "name": "Max",
    "alter": 30,
    "stadt": "Berlin",
    "gehalt": 50000
}

neu = {
    "name": "Max",
    "alter": 31,
    "stadt": "München",
    "beruf": "Entwickler"
}

diff = dict_diff(alt, neu)
print(diff)
```

**Erwarteter Output:**
```
{
    'hinzugefuegt': {'beruf': 'Entwickler'},
    'entfernt': {'gehalt': 50000},
    'geaendert': {
        'alter': {'alt': 30, 'neu': 31},
        'stadt': {'alt': 'Berlin', 'neu': 'München'}
    },
    'unveraendert': {'name': 'Max'}
}
```

---

## 📝 Übung 7: LRU Cache (Schwer)

Implementiere einen einfachen LRU (Least Recently Used) Cache mit Dictionaries.

**Anforderungen:**
- Maximale Kapazität
- get(key): Gibt Value zurück, markiert als "recently used"
- put(key, value): Fügt ein, entfernt ältestes bei Überlauf
- Verwende OrderedDict oder simuliere mit dict

```python
class LRUCache:
    def __init__(self, capacity):
        # Dein Code hier
        pass
    
    def get(self, key):
        # Dein Code hier
        pass
    
    def put(self, key, value):
        # Dein Code hier
        pass
    
    def __repr__(self):
        # Für Debug
        pass

# Test
cache = LRUCache(3)

cache.put("a", 1)
cache.put("b", 2)
cache.put("c", 3)
print(cache)  # {a: 1, b: 2, c: 3}

cache.get("a")  # 'a' wird "recent"
print(cache)  # {b: 2, c: 3, a: 1}

cache.put("d", 4)  # 'b' wird entfernt (oldest)
print(cache)  # {c: 3, a: 1, d: 4}

cache.put("e", 5)  # 'c' wird entfernt
print(cache)  # {a: 1, d: 4, e: 5}
```

**Erwarteter Output:**
```
LRUCache(capacity=3): {a: 1, b: 2, c: 3}
LRUCache(capacity=3): {b: 2, c: 3, a: 1}
LRUCache(capacity=3): {c: 3, a: 1, d: 4}
LRUCache(capacity=3): {a: 1, d: 4, e: 5}
```

---

## 📝 Übung 8: JSON Flattener (Schwer)

Erstelle eine Funktion `flatten_json(obj, prefix="")`, die verschachteltes JSON flach macht.

**Anforderungen:**
- Verschachtelte Dicts werden zu Dot-Notation
- Listen werden mit Index: "items[0].name"
- Behandelt beliebig tiefe Verschachtelung

```python
def flatten_json(obj, prefix=""):
    # Dein Code hier
    pass

# Test
nested = {
    "user": {
        "name": "Max",
        "age": 30,
        "address": {
            "city": "Berlin",
            "zip": "10115"
        },
        "hobbies": ["Sport", "Lesen"]
    },
    "active": True
}

flat = flatten_json(nested)
for key, value in sorted(flat.items()):
    print(f"{key}: {value}")
```

**Erwarteter Output:**
```
active: True
user.address.city: Berlin
user.address.zip: 10115
user.age: 30
user.hobbies[0]: Sport
user.hobbies[1]: Lesen
user.name: Max
```

---

## 📝 Übung 9: Dict Query System (Schwer)

Implementiere ein einfaches Query-System für eine Liste von Dictionaries.

**Anforderungen:**
- where(key, operator, value): Filtert Items
- Operatoren: "==", "!=", ">", "<", ">=", "<=", "in", "contains"
- order_by(key, reverse=False): Sortiert Ergebnisse
- Methoden-Chaining möglich

```python
class DictQuery:
    def __init__(self, data):
        # Dein Code hier
        pass
    
    def where(self, key, operator, value):
        # Dein Code hier
        pass
    
    def order_by(self, key, reverse=False):
        # Dein Code hier
        pass
    
    def get(self):
        # Gibt gefilterte/sortierte Daten zurück
        pass

# Test
products = [
    {"name": "Laptop", "price": 800, "category": "Electronics"},
    {"name": "Mouse", "price": 25, "category": "Electronics"},
    {"name": "Desk", "price": 300, "category": "Furniture"},
    {"name": "Chair", "price": 150, "category": "Furniture"},
    {"name": "Keyboard", "price": 60, "category": "Electronics"}
]

# Alle Electronics unter 100€, sortiert nach Preis
query = DictQuery(products)
result = (query
    .where("category", "==", "Electronics")
    .where("price", "<", 100)
    .order_by("price")
    .get())

for item in result:
    print(f"{item['name']}: {item['price']}€")
```

**Erwarteter Output:**
```
Mouse: 25€
Keyboard: 60€
```

---

## 📝 Übung 10: Dictionary Validator (Experte)

Erstelle eine Klasse `DictValidator`, die Dictionaries gegen ein Schema validiert.

**Anforderungen:**
- Schema definiert erforderliche Keys und Typen
- Unterstützt verschachtelte Validierung
- Optionale Keys mit Defaults
- Custom Validators
- Gibt detaillierte Fehler zurück

```python
class DictValidator:
    def __init__(self, schema):
        # schema: Dict mit Validierungs-Regeln
        pass
    
    def validate(self, data):
        # Gibt (valid: bool, errors: list) zurück
        pass

# Test
schema = {
    "name": {"type": str, "required": True},
    "age": {"type": int, "required": True, "min": 0, "max": 150},
    "email": {"type": str, "required": True, "pattern": r".*@.*\..*"},
    "address": {
        "type": dict,
        "required": False,
        "schema": {
            "city": {"type": str, "required": True},
            "zip": {"type": str, "required": True}
        }
    },
    "hobbies": {"type": list, "required": False}
}

validator = DictValidator(schema)

# Valide Daten
valid_data = {
    "name": "Max",
    "age": 30,
    "email": "max@example.com",
    "address": {
        "city": "Berlin",
        "zip": "10115"
    }
}

valid, errors = validator.validate(valid_data)
print(f"Valid: {valid}")
print(f"Errors: {errors}")

# Invalide Daten
invalid_data = {
    "name": "Anna",
    "age": 200,  # Zu alt
    "email": "invalid-email"  # Kein @
}

valid, errors = validator.validate(invalid_data)
print(f"\nValid: {valid}")
print(f"Errors: {errors}")
```

**Erwarteter Output:**
```
Valid: True
Errors: []

Valid: False
Errors: ['age: value 200 exceeds max 150', 'email: pattern mismatch']
```

---

## 💡 Lösungsstrategien

### Für Übung 1-3 (Grundlagen):
- Nutze dict.get() für sichere Zugriffe
- String-Methoden für Text-Processing
- Iterate mit .items() für Key-Value Paare

### Für Übung 4-6 (Fortgeschritten):
- Verwende setdefault() oder defaultdict
- Set-Operationen für Key-Vergleiche (keys() & keys())
- Dict Comprehensions für Transformationen

### Für Übung 7-9 (Profi):
- OrderedDict für LRU Cache
- Rekursion für verschachtelte Strukturen
- Method Chaining mit `return self`

### Für Übung 10 (Experte):
- Rekursive Validierung für nested schemas
- Regular Expressions für Pattern-Matching
- Akkumuliere Fehler statt frühes Abbrechen

---

## 🔍 Selbst-Check

Nach diesen Übungen solltest du:

✅ Dictionaries für Zählungen und Gruppierungen nutzen können
✅ Sicher mit verschachtelten Dictionaries arbeiten
✅ Dict Comprehensions und Transformationen beherrschen
✅ Set-Operationen mit dict.keys() anwenden können
✅ Performance-bewusste Dict-Operationen schreiben
✅ Eigene Dict-basierte Datenstrukturen implementieren können

---

## 🎯 Bonus-Herausforderungen

### Challenge 1: In-Memory Datenbank

Implementiere eine einfache In-Memory Datenbank mit Dictionaries:
- Tabellen mit Rows (Dicts)
- SELECT mit WHERE-Bedingungen
- INSERT, UPDATE, DELETE Operationen
- JOIN zwischen Tabellen
- Indizes für schnellen Lookup

### Challenge 2: Config Manager

Erstelle einen hierarchischen Config Manager:
- Mehrere Config-Ebenen (default, user, environment)
- Automatisches Merging mit Prioritäten
- Dot-Notation Zugriff ("database.host")
- Type-Safe Getters
- Config-Validation

### Challenge 3: Event System

Implementiere ein Event-System:
- Event-Handler registrieren
- Events mit Payloads dispatchen
- Priorisierung von Handlern
- Event-History
- Async Event-Processing (optional)

---

## 📚 Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [Mapping Types — dict](https://docs.python.org/3/library/stdtypes.html#mapping-types-dict) - Dictionary Referenz
- [collections module](https://docs.python.org/3/library/collections.html) - OrderedDict, defaultdict, Counter
- [operator module](https://docs.python.org/3/library/operator.html) - itemgetter für Sortierung
- [re module](https://docs.python.org/3/library/re.html) - Regular Expressions für Validierung

---

## 🎓 Verwandte Themen

- [[03_Mappings_und_Sets/01_Dictionaries/01_Dict Basics|Dict Basics]] - Grundlagen wiederholen
- [[03_Mappings_und_Sets/01_Dictionaries/02_Dict Methoden|Dict Methoden]] - Methoden-Referenz
- [[03_Mappings_und_Sets/01_Dictionaries/03_Dict Comprehensions|Dict Comprehensions]] - Comprehension-Patterns
- [[03_Mappings_und_Sets/01_Dictionaries/04_Nested Dicts|Nested Dicts]] - Verschachtelte Strukturen
- [[03_Mappings_und_Sets/03_Collections_Module/02_DefaultDict|DefaultDict]] - Spezielle Dict-Variante
- [[06_Praxis/04_Lösungen/Lösungen Teil 3|Lösungen Teil 3]] - Musterlösungen zu diesen Übungen

---

← [[03_Mappings_und_Sets/01_Dictionaries/07_Dict Performance|Dict Performance]] | [[INDEX|📑 Index]] | [[03_Mappings_und_Sets/02_Sets/01_Set Basics|Set Basics]] →