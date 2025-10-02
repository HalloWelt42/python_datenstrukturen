---
tags: [python, datenstrukturen, sets, frozenset, immutable]
created: 2025-10-01
---

← [[03_Mappings_und_Sets/02_Sets/02_Set Operationen|Set Operationen]] | [[INDEX|📑 Index]] | [[03_Mappings_und_Sets/02_Sets/04_Set Comprehensions|Set Comprehensions]] →

# Frozenset

> [!tip] Lernziel
> Nach dieser Seite verstehst du frozenset als unveränderbare Set-Variante und kannst es für hashbare Mengen und Dict-Keys einsetzen.

## Was ist ein Frozenset?

Ein **frozenset** ist die **unveränderbare** (immutable) Version eines Sets. Es hat alle Eigenschaften eines Sets, kann aber nach Erstellung nicht mehr geändert werden.

**Kernmerkmale:**
- **Immutable**: Kann nicht verändert werden
- **Hashable**: Kann als Dict-Key oder Set-Element verwendet werden
- **Ungeordnet**: Keine definierte Reihenfolge
- **Eindeutig**: Keine Duplikate
- **Set-Operationen**: Alle Lese-Operationen verfügbar
- **Keine Modifikationen**: add(), remove(), etc. nicht verfügbar

**Unterschiede zu set:**
| Feature | set | frozenset |
|---------|-----|-----------|
| Mutable | ✅ | ❌ |
| Hashable | ❌ | ✅ |
| Als Dict-Key | ❌ | ✅ |
| In Set speichern | ❌ | ✅ |
| add()/remove() | ✅ | ❌ |
| Set-Operationen | ✅ | ✅ |

---

## Erstellung und Grundlagen

### Frozenset erstellen

```python
# Mit frozenset() Konstruktor
zahlen = frozenset([1, 2, 3, 4, 5])
print(zahlen)  # frozenset({1, 2, 3, 4, 5})
print(type(zahlen))  # <class 'frozenset'>

# Aus String
buchstaben = frozenset("hello")
print(buchstaben)  # frozenset({'h', 'e', 'l', 'o'})

# Aus Set
normal_set = {1, 2, 3}
frozen = frozenset(normal_set)
print(frozen)  # frozenset({1, 2, 3})

# Leeres Frozenset
leer = frozenset()
print(leer)  # frozenset()
print(len(leer))  # 0

# Aus beliebigem Iterable
tupel = (1, 2, 2, 3, 3, 3)
frozen = frozenset(tupel)
print(frozen)  # frozenset({1, 2, 3}) - Duplikate entfernt
```

### ⚠️ Keine geschweiften Klammern!

```python
# ❌ FALSCH - Gibt es nicht für frozenset!
# frozen = frozenset{1, 2, 3}  # SyntaxError!

# ✅ RICHTIG - Nur mit Konstruktor
frozen = frozenset([1, 2, 3])
```

---

## Immutability - Unveränderbarkeit

### Keine Modifikations-Methoden

```python
frozen = frozenset([1, 2, 3])

# ❌ Keine add() Methode
try:
    frozen.add(4)
except AttributeError as e:
    print(f"Fehler: {e}")
    # 'frozenset' object has no attribute 'add'

# ❌ Keine remove() Methode
try:
    frozen.remove(2)
except AttributeError as e:
    print(f"Fehler: {e}")

# ❌ Keine discard() Methode
try:
    frozen.discard(2)
except AttributeError as e:
    print(f"Fehler: {e}")

# ❌ Keine clear() Methode
try:
    frozen.clear()
except AttributeError as e:
    print(f"Fehler: {e}")

# ❌ Keine pop() Methode
try:
    frozen.pop()
except AttributeError as e:
    print(f"Fehler: {e}")

# ❌ Keine update-ähnlichen Methoden
# update(), intersection_update(), etc. existieren nicht!
```

### Set-Operationen geben neue frozensets

```python
a = frozenset([1, 2, 3])
b = frozenset([3, 4, 5])

# Alle Operationen geben neue frozensets zurück
union = a | b
print(union)  # frozenset({1, 2, 3, 4, 5})
print(type(union))  # <class 'frozenset'>

intersection = a & b
print(intersection)  # frozenset({3})

difference = a - b
print(difference)  # frozenset({1, 2})

# Original bleibt unverändert
print(a)  # frozenset({1, 2, 3})
```

---

## Hashable - Verwendung als Key

### Als Dictionary-Key

```python
# ❌ Set als Dict-Key geht NICHT
try:
    d = {{1, 2, 3}: "value"}
except TypeError as e:
    print(f"Set: {e}")
    # unhashable type: 'set'

# ✅ Frozenset als Dict-Key geht!
d = {
    frozenset([1, 2, 3]): "Gruppe A",
    frozenset([4, 5, 6]): "Gruppe B"
}

key = frozenset([1, 2, 3])
print(d[key])  # Gruppe A

# Praktisch: Koordinaten-Sets als Keys
koordinaten = {
    frozenset([(0, 0), (0, 1), (1, 0)]): "L-Form",
    frozenset([(0, 0), (1, 0), (2, 0)]): "Linie"
}

form = frozenset([(0, 0), (0, 1), (1, 0)])
print(koordinaten[form])  # L-Form
```

### In Sets speichern

```python
# ❌ Set in Set geht NICHT
try:
    s = {{1, 2}, {3, 4}}
except TypeError as e:
    print(f"Set in Set: {e}")

# ✅ Frozenset in Set geht!
gruppen = {
    frozenset([1, 2, 3]),
    frozenset([4, 5, 6]),
    frozenset([7, 8, 9])
}

print(gruppen)
# {frozenset({1, 2, 3}), frozenset({4, 5, 6}), frozenset({7, 8, 9})}

# Membership-Test
gruppe = frozenset([1, 2, 3])
if gruppe in gruppen:
    print("Gruppe gefunden")

# Neue Gruppe hinzufügen
gruppen.add(frozenset([10, 11]))
print(len(gruppen))  # 4
```

---

## Set-Operationen mit frozenset

### Alle Lese-Operationen verfügbar

```python
a = frozenset([1, 2, 3, 4, 5])
b = frozenset([4, 5, 6, 7, 8])

# Union
print(a | b)  # frozenset({1, 2, 3, 4, 5, 6, 7, 8})
print(a.union(b))  # Gleich

# Intersection
print(a & b)  # frozenset({4, 5})
print(a.intersection(b))

# Difference
print(a - b)  # frozenset({1, 2, 3})
print(a.difference(b))

# Symmetric Difference
print(a ^ b)  # frozenset({1, 2, 3, 6, 7, 8})
print(a.symmetric_difference(b))

# Subset/Superset
c = frozenset([1, 2])
print(c <= a)  # True
print(c.issubset(a))  # True

print(a >= c)  # True
print(a.issuperset(c))  # True

# Disjoint
d = frozenset([10, 11])
print(a.isdisjoint(d))  # True
```

### Keine In-Place Operationen

```python
a = frozenset([1, 2, 3])
b = frozenset([3, 4, 5])

# ❌ Keine |= Operator
try:
    a |= b
except TypeError as e:
    print("Keine In-Place Updates bei frozenset")

# ❌ Keine Methoden wie update(), intersection_update(), etc.
# Diese existieren nicht für frozenset!

# ✅ Stattdessen: Neue frozensets erstellen
a = a | b  # Neues frozenset zuweisen
print(a)  # frozenset({1, 2, 3, 4, 5})
```

---

## Praktische Anwendungen

### 1. Konstante Mengen

```python
# Unveränderbare Konstanten
WOCHENTAGE = frozenset([
    "Montag", "Dienstag", "Mittwoch", "Donnerstag",
    "Freitag", "Samstag", "Sonntag"
])

ARBEITSTAGE = frozenset([
    "Montag", "Dienstag", "Mittwoch", "Donnerstag", "Freitag"
])

WOCHENENDE = WOCHENTAGE - ARBEITSTAGE

def ist_arbeitstag(tag):
    return tag in ARBEITSTAGE

print(ist_arbeitstag("Montag"))  # True
print(ist_arbeitstag("Samstag"))  # False

# Niemand kann die Konstanten ändern
# WOCHENTAGE.add("Neuer Tag")  # AttributeError!
```

### 2. Cache mit Set-Keys

```python
# Cache für Mengen-basierte Berechnungen
berechnung_cache = {}

def berechne_schnittmenge(gruppe_a, gruppe_b):
    """Berechnet Schnittmenge mit Caching"""
    # Konvertiere zu frozensets für Hashability
    key = (frozenset(gruppe_a), frozenset(gruppe_b))
    
    if key in berechnung_cache:
        print("Aus Cache geladen")
        return berechnung_cache[key]
    
    # Teure Berechnung (simuliert)
    result = set(gruppe_a) & set(gruppe_b)
    
    # Cache speichern
    berechnung_cache[key] = result
    return result

# Verwendung
gruppe1 = [1, 2, 3, 4, 5]
gruppe2 = [4, 5, 6, 7, 8]

result1 = berechne_schnittmenge(gruppe1, gruppe2)
print(result1)  # {4, 5}

# Zweiter Aufruf nutzt Cache
result2 = berechne_schnittmenge(gruppe1, gruppe2)  # Aus Cache geladen
```

### 3. Graph-Repräsentation

```python
# Kanten in einem ungerichteten Graphen
# Jede Kante ist ein frozenset von zwei Knoten

class Graph:
    def __init__(self):
        self.kanten = set()
        self.knoten = set()
    
    def add_kante(self, knoten_a, knoten_b):
        """Fügt ungerichtete Kante hinzu"""
        kante = frozenset([knoten_a, knoten_b])
        self.kanten.add(kante)
        self.knoten.add(knoten_a)
        self.knoten.add(knoten_b)
    
    def hat_kante(self, knoten_a, knoten_b):
        """Prüft ob Kante existiert"""
        kante = frozenset([knoten_a, knoten_b])
        return kante in self.kanten
    
    def nachbarn(self, knoten):
        """Findet alle Nachbarn eines Knotens"""
        nachbarn = set()
        for kante in self.kanten:
            if knoten in kante:
                # Anderer Knoten in der Kante
                nachbarn |= kante - {knoten}
        return nachbarn

# Verwendung
g = Graph()
g.add_kante("A", "B")
g.add_kante("B", "C")
g.add_kante("A", "C")

print(g.hat_kante("A", "B"))  # True
print(g.hat_kante("B", "A"))  # True (ungerichtet!)
print(g.hat_kante("A", "D"))  # False

print(g.nachbarn("B"))  # {'A', 'C'}
```

### 4. Berechtigungen und Rollen

```python
# Unveränderbare Berechtigungssätze
class Permission:
    # Vordefinierte Berechtigungssets
    ADMIN = frozenset(["read", "write", "delete", "manage_users", "manage_roles"])
    EDITOR = frozenset(["read", "write", "delete"])
    VIEWER = frozenset(["read"])
    GUEST = frozenset()
    
    @staticmethod
    def has_permission(user_perms, required_perms):
        """Prüft ob User alle erforderlichen Rechte hat"""
        return required_perms <= user_perms
    
    @staticmethod
    def get_role_name(perms):
        """Findet Rollenname basierend auf Permissions"""
        if perms == Permission.ADMIN:
            return "Admin"
        elif perms == Permission.EDITOR:
            return "Editor"
        elif perms == Permission.VIEWER:
            return "Viewer"
        elif perms == Permission.GUEST:
            return "Guest"
        else:
            return "Custom"

# Verwendung
user_perms = Permission.EDITOR

if Permission.has_permission(user_perms, frozenset(["read", "write"])):
    print("User kann lesen und schreiben")

role = Permission.get_role_name(user_perms)
print(f"User-Rolle: {role}")  # Editor

# Als Dict-Keys
permissions_db = {
    Permission.ADMIN: ["alice", "bob"],
    Permission.EDITOR: ["charlie", "diana"],
    Permission.VIEWER: ["eve", "frank"]
}

# User mit bestimmten Permissions finden
for perms, users in permissions_db.items():
    if "write" in perms:
        print(f"User mit Schreibrechten: {users}")
```

### 5. Mengen von Mengen

```python
# Sammlung von Gruppen
teams = {
    frozenset(["Alice", "Bob", "Charlie"]),
    frozenset(["Diana", "Eve"]),
    frozenset(["Frank", "Grace", "Henry", "Iris"])
}

# Team finden, das Person enthält
def find_team(person, teams):
    for team in teams:
        if person in team:
            return team
    return None

alice_team = find_team("Alice", teams)
print(f"Alice's Team: {alice_team}")

# Teams nach Größe gruppieren
from collections import defaultdict
teams_by_size = defaultdict(set)

for team in teams:
    size = len(team)
    teams_by_size[size].add(team)

print(f"Teams mit 3 Mitgliedern: {len(teams_by_size[3])}")

# Neue Teams hinzufügen
teams.add(frozenset(["John", "Kate"]))

# Überschneidende Teams finden
def find_overlapping_teams(teams):
    overlaps = []
    teams_list = list(teams)
    for i, team1 in enumerate(teams_list):
        for team2 in teams_list[i+1:]:
            if team1 & team2:  # Schnittmenge nicht leer
                overlaps.append((team1, team2, team1 & team2))
    return overlaps

# Keine Überschneidungen in unserem Beispiel
overlaps = find_overlapping_teams(teams)
if not overlaps:
    print("Alle Teams sind disjoint")
```

### 6. Konfigurations-Sets

```python
# Feature-Flags als frozensets
class FeatureConfig:
    BASIC = frozenset(["login", "profile", "search"])
    PREMIUM = frozenset(["login", "profile", "search", "export", "advanced_search"])
    ENTERPRISE = frozenset(["login", "profile", "search", "export", 
                            "advanced_search", "api_access", "analytics", "admin"])
    
    # Zusätzliche Feature-Gruppen
    SEARCH_FEATURES = frozenset(["search", "advanced_search"])
    DATA_FEATURES = frozenset(["export", "api_access", "analytics"])

# Feature-Matrix als Dict
feature_matrix = {
    "basic": FeatureConfig.BASIC,
    "premium": FeatureConfig.PREMIUM,
    "enterprise": FeatureConfig.ENTERPRISE
}

def upgrade_path(current, target):
    """Zeigt welche Features beim Upgrade hinzukommen"""
    current_features = feature_matrix[current]
    target_features = feature_matrix[target]
    
    new_features = target_features - current_features
    return new_features

# Upgrade von Basic zu Premium
new_in_premium = upgrade_path("basic", "premium")
print(f"Neu in Premium: {new_in_premium}")
# frozenset({'export', 'advanced_search'})

# Welche Pläne haben bestimmtes Feature?
def plans_with_feature(feature):
    plans = []
    for plan, features in feature_matrix.items():
        if feature in features:
            plans.append(plan)
    return plans

print(plans_with_feature("export"))  # ['premium', 'enterprise']
```

---

## Performance und Memory

### Memory-Vergleich

```python
import sys

# Set vs. Frozenset Speicherverbrauch
data = range(1000)

normal_set = set(data)
frozen_set = frozenset(data)

print(f"Set:       {sys.getsizeof(normal_set):,} bytes")
print(f"Frozenset: {sys.getsizeof(frozen_set):,} bytes")

# Frozenset ist minimal kleiner (keine Modifikations-Overhead)
```

### Hash-Performance

```python
import timeit

# Frozenset kann gehasht werden
frozen = frozenset(range(100))

# Hash-Berechnung
hash_time = timeit.timeit("hash(frozen)", globals=globals(), number=100000)
print(f"Hash-Zeit: {hash_time:.4f}s")

# Als Dict-Key nutzen
d = {frozen: "value"}
lookup_time = timeit.timeit("d[frozen]", globals=globals(), number=100000)
print(f"Dict-Lookup: {lookup_time:.4f}s")

# Sehr schnell wegen Caching des Hash-Werts
```

---

## Konvertierung

### Set ↔ Frozenset

```python
# Set → Frozenset
normal = {1, 2, 3, 4, 5}
frozen = frozenset(normal)
print(frozen)  # frozenset({1, 2, 3, 4, 5})

# Frozenset → Set
frozen = frozenset([1, 2, 3])
normal = set(frozen)
print(normal)  # {1, 2, 3}

# Jetzt kann man modifizieren
normal.add(4)
print(normal)  # {1, 2, 3, 4}

# Original frozen bleibt unverändert
print(frozen)  # frozenset({1, 2, 3})
```

### Mit anderen Typen

```python
# Liste → Frozenset
liste = [1, 2, 2, 3, 3, 3]
frozen = frozenset(liste)
print(frozen)  # frozenset({1, 2, 3})

# Frozenset → Liste
frozen = frozenset([3, 1, 4, 1, 5])
liste = list(frozen)
print(liste)  # [1, 3, 4, 5] (sortiert variiert)

# Frozenset → Tuple
tuple_version = tuple(frozen)
print(tuple_version)

# String → Frozenset
text = "hello"
chars = frozenset(text)
print(chars)  # frozenset({'h', 'e', 'l', 'o'})
```

---

## Häufige Fallstricke

### ⚠️ Fallstrick 1: Versuch zu modifizieren

```python
frozen = frozenset([1, 2, 3])

# ❌ ALLE Modifikations-Versuche schlagen fehl
methods_that_dont_exist = [
    "add", "remove", "discard", "pop", "clear",
    "update", "intersection_update", "difference_update",
    "symmetric_difference_update"
]

for method in methods_that_dont_exist:
    if not hasattr(frozen, method):
        print(f"✗ {method}() existiert nicht")
```

### ⚠️ Fallstrick 2: Erwarten von Geschwindigkeit

```python
# Frozenset ist NICHT schneller als set!
# Es ist nur immutable und hashable

import timeit

data = list(range(1000))

# Erstellung
set_time = timeit.timeit("set(data)", globals=globals(), number=10000)
frozen_time = timeit.timeit("frozenset(data)", globals=globals(), number=10000)

print(f"Set:       {set_time:.4f}s")
print(f"Frozenset: {frozen_time:.4f}s")

# Ähnliche Performance - frozenset ist nicht "schneller"
# Vorteil ist Immutability, nicht Speed!
```

### ⚠️ Fallstrick 3: Nested Mutables

```python
# ❌ FALSCH - Nested mutable Objekte
liste = [1, 2, 3]
try:
    frozen = frozenset([liste])  # Liste ist mutable!
except TypeError as e:
    print(f"Fehler: {e}")
    # unhashable type: 'list'

# ✅ RICHTIG - Nur Immutables
tuple_data = (1, 2, 3)
frozen = frozenset([tuple_data])
print(frozen)  # frozenset({(1, 2, 3)})

# Frozenset von frozensets ist OK
nested = frozenset([
    frozenset([1, 2]),
    frozenset([3, 4])
])
print(nested)
# frozenset({frozenset({1, 2}), frozenset({3, 4})})
```

---

## Best Practices

### ✅ Verwende frozenset wenn:

```python
# 1. Als Dict-Key
koordinaten_daten = {
    frozenset([(0, 0), (0, 1)]): "Linie vertikal",
    frozenset([(0, 0), (1, 0)]): "Linie horizontal"
}

# 2. In Sets speichern
gruppen_sammlung = {
    frozenset(["A", "B", "C"]),
    frozenset(["D", "E"])
}

# 3. Konstanten definieren
VALID_COUNTRIES = frozenset(["DE", "AT", "CH", "IT", "FR"])

# 4. Unveränderbare Datenstrukturen
def get_prime_numbers():
    return frozenset([2, 3, 5, 7, 11, 13, 17, 19, 23, 29])

# 5. Cache-Keys
def cached_function(items_set):
    cache_key = frozenset(items_set)
    # Nutze cache_key als Dict-Key
```

### ❌ Verwende NICHT frozenset wenn:

```python
# 1. Du Elemente hinzufügen/entfernen musst
# → Verwende set

# 2. Performance kritisch ist bei Erstellung
# → set und frozenset haben ähnliche Performance

# 3. Du keine Hashability brauchst
# → set ist flexibler

# 4. Du oft konvertieren musst
# → Extra Konvertierungen kosten Zeit
```

---

## Zusammenfassung: set vs. frozenset

```python
# DECISION FLOWCHART

"""
Brauchst du ein Set als Dict-Key oder in anderem Set?
│
├─ JA → frozenset
│
└─ NEIN
    │
    Wird das Set nach Erstellung modifiziert?
    │
    ├─ JA → set
    │
    └─ NEIN
        │
        Soll es als Konstante dienen?
        │
        ├─ JA → frozenset
        │
        └─ NEIN → set (default)
"""

# In 90% der Fälle: Verwende set
# frozenset nur für spezielle Use-Cases
```

---

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [frozenset](https://docs.python.org/3/library/stdtypes.html#frozenset) - Vollständige frozenset Referenz
- [Set Types — set, frozenset](https://docs.python.org/3/library/stdtypes.html#set-types-set-frozenset) - Beide Set-Typen
- [Built-in Functions: frozenset()](https://docs.python.org/3/library/functions.html#func-frozenset) - Konstruktor-Doku

---

## Verwandte Themen

- [[03_Mappings_und_Sets/02_Sets/01_Set Basics|Set Basics]] - Mutable Set-Variante
- [[03_Mappings_und_Sets/02_Sets/02_Set Operationen|Set Operationen]] - Gelten auch für frozenset
- [[01_Grundlagen/01_Einführung/02_Mutability vs Immutability|Mutability vs Immutability]] - Konzepte erklärt
- [[02_Sequenzen/02_Tupel/01_Tuple Basics|Tuple Basics]] - Andere immutable Collection

---

← [[03_Mappings_und_Sets/02_Sets/02_Set Operationen|Set Operationen]] | [[INDEX|📑 Index]] | [[03_Mappings_und_Sets/02_Sets/04_Set Comprehensions|Set Comprehensions]] →