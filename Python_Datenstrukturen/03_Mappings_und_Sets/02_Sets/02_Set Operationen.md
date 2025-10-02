---
tags: [python, datenstrukturen, sets, mengen, operationen]
created: 2025-10-01
---

← [[03_Mappings_und_Sets/02_Sets/01_Set Basics|Set Basics]] | [[INDEX|📑 Index]] | [[03_Mappings_und_Sets/02_Sets/03_Frozenset|Frozenset]] →

# Set Operationen

> [!tip] Lernziel
> Nach dieser Seite beherrschst du alle mathematischen Mengenoperationen und kannst Sets elegant für komplexe Datenverarbeitung einsetzen.

## Überblick: Alle Set-Operationen

Python Sets unterstützen alle klassischen mathematischen Mengenoperationen:

| Operation | Operator | Methode | Beschreibung |
|-----------|----------|---------|--------------|
| **Union** | `\|` | `union()` | Alle Elemente aus beiden |
| **Intersection** | `&` | `intersection()` | Nur gemeinsame Elemente |
| **Difference** | `-` | `difference()` | In A, aber nicht in B |
| **Symmetric Difference** | `^` | `symmetric_difference()` | In A oder B, aber nicht beide |
| **Subset** | `<=` | `issubset()` | Alle Elemente von A in B |
| **Proper Subset** | `<` | | Subset, aber nicht gleich |
| **Superset** | `>=` | `issuperset()` | Alle Elemente von B in A |
| **Proper Superset** | `>` | | Superset, aber nicht gleich |
| **Disjoint** | | `isdisjoint()` | Keine gemeinsamen Elemente |

---

## Union (Vereinigung) - |

Alle Elemente aus beiden Sets, ohne Duplikate.

### Syntax

```python
# Mit | Operator
a = {1, 2, 3}
b = {3, 4, 5}
c = a | b
print(c)  # {1, 2, 3, 4, 5}

# Mit union() Methode
c = a.union(b)
print(c)  # {1, 2, 3, 4, 5}

# Mehrere Sets
d = {5, 6, 7}
alle = a | b | d
print(alle)  # {1, 2, 3, 4, 5, 6, 7}

# union() mit mehreren Sets
alle = a.union(b, d)
print(alle)  # {1, 2, 3, 4, 5, 6, 7}

# union() akzeptiert auch Iterables (nicht nur Sets)
zahlen = {1, 2, 3}
mit_liste = zahlen.union([4, 5, 6])
print(mit_liste)  # {1, 2, 3, 4, 5, 6}
```

### In-Place: update() / |=

```python
a = {1, 2, 3}
b = {3, 4, 5}

# Mit |= Operator
a |= b
print(a)  # {1, 2, 3, 4, 5}

# Mit update() Methode
a = {1, 2, 3}
a.update(b)
print(a)  # {1, 2, 3, 4, 5}

# update() mit mehreren Iterables
a = {1, 2}
a.update([3, 4], {5, 6}, (7, 8))
print(a)  # {1, 2, 3, 4, 5, 6, 7, 8}
```

### Praktische Anwendung

```python
# Alle User aus verschiedenen Gruppen
admins = {"alice", "bob"}
moderators = {"bob", "charlie", "diana"}
editors = {"diana", "eve"}

# Alle User mit Rechten
alle_berechtigte = admins | moderators | editors
print(alle_berechtigte)
# {'alice', 'bob', 'charlie', 'diana', 'eve'}

# Alle aktiven IPs aus mehreren Logs
log1_ips = {"192.168.1.1", "192.168.1.5"}
log2_ips = {"192.168.1.5", "192.168.1.10"}
log3_ips = {"192.168.1.15"}

alle_ips = log1_ips | log2_ips | log3_ips
print(f"Gesamt {len(alle_ips)} unique IPs")
```

---

## Intersection (Schnittmenge) - &

Nur Elemente, die in beiden Sets vorhanden sind.

### Syntax

```python
# Mit & Operator
a = {1, 2, 3, 4, 5}
b = {4, 5, 6, 7, 8}
c = a & b
print(c)  # {4, 5}

# Mit intersection() Methode
c = a.intersection(b)
print(c)  # {4, 5}

# Mehrere Sets
d = {4, 9, 10}
gemeinsam = a & b & d
print(gemeinsam)  # {4}

# intersection() mit mehreren Sets
gemeinsam = a.intersection(b, d)
print(gemeinsam)  # {4}
```

### In-Place: intersection_update() / &=

```python
a = {1, 2, 3, 4, 5}
b = {4, 5, 6, 7, 8}

# Mit &= Operator
a &= b
print(a)  # {4, 5}

# Mit intersection_update() Methode
a = {1, 2, 3, 4, 5}
a.intersection_update(b)
print(a)  # {4, 5}
```

### Praktische Anwendung

```python
# Gemeinsame Interessen finden
alice_hobbies = {"lesen", "sport", "musik", "reisen"}
bob_hobbies = {"sport", "gaming", "musik", "kochen"}

gemeinsame = alice_hobbies & bob_hobbies
print(f"Gemeinsame Hobbies: {gemeinsame}")
# {'sport', 'musik'}

# User in mehreren Gruppen
gruppe_a = {"user1", "user2", "user3", "user4"}
gruppe_b = {"user3", "user4", "user5", "user6"}
gruppe_c = {"user4", "user7", "user8"}

# In allen drei Gruppen
in_allen = gruppe_a & gruppe_b & gruppe_c
print(f"In allen Gruppen: {in_allen}")  # {'user4'}

# Skills die alle Team-Mitglieder haben
team_skills = [
    {"python", "git", "sql", "docker"},
    {"python", "git", "javascript"},
    {"python", "git", "sql"}
]

gemeinsame_skills = set.intersection(*team_skills)
print(f"Alle können: {gemeinsame_skills}")  # {'python', 'git'}
```

---

## Difference (Differenz) - -

Elemente in A, aber nicht in B.

### Syntax

```python
# Mit - Operator
a = {1, 2, 3, 4, 5}
b = {4, 5, 6, 7, 8}
c = a - b
print(c)  # {1, 2, 3}

# Umgekehrt
c = b - a
print(c)  # {6, 7, 8}

# Mit difference() Methode
c = a.difference(b)
print(c)  # {1, 2, 3}

# Mehrere Sets
d = {2, 3, 9}
nur_a = a - b - d
print(nur_a)  # {1}
```

### In-Place: difference_update() / -=

```python
a = {1, 2, 3, 4, 5}
b = {4, 5, 6, 7, 8}

# Mit -= Operator
a -= b
print(a)  # {1, 2, 3}

# Mit difference_update() Methode
a = {1, 2, 3, 4, 5}
a.difference_update(b)
print(a)  # {1, 2, 3}
```

### Praktische Anwendung

```python
# User die gekündigt haben
alle_user = {"alice", "bob", "charlie", "diana", "eve"}
aktive_user = {"alice", "charlie", "eve"}

gekuendigt = alle_user - aktive_user
print(f"Gekündigt: {gekuendigt}")  # {'bob', 'diana'}

# Features die entfernt wurden
alte_features = {"login", "search", "export", "import", "admin"}
neue_features = {"login", "search", "export", "dashboard"}

entfernt = alte_features - neue_features
hinzugefuegt = neue_features - alte_features

print(f"Entfernt: {entfernt}")      # {'import', 'admin'}
print(f"Hinzugefügt: {hinzugefuegt}")  # {'dashboard'}

# Duplikate zwischen zwei Datensätzen
dataset1_ids = {1, 2, 3, 4, 5}
dataset2_ids = {4, 5, 6, 7, 8}

nur_in_1 = dataset1_ids - dataset2_ids
nur_in_2 = dataset2_ids - dataset1_ids

print(f"Nur in Dataset 1: {nur_in_1}")  # {1, 2, 3}
print(f"Nur in Dataset 2: {nur_in_2}")  # {6, 7, 8}
```

---

## Symmetric Difference (Symmetrische Differenz) - ^

Elemente die in A oder B sind, aber nicht in beiden.

### Syntax

```python
# Mit ^ Operator
a = {1, 2, 3, 4, 5}
b = {4, 5, 6, 7, 8}
c = a ^ b
print(c)  # {1, 2, 3, 6, 7, 8}

# Entspricht: (a - b) | (b - a)
c = (a - b) | (b - a)
print(c)  # {1, 2, 3, 6, 7, 8}

# Mit symmetric_difference() Methode
c = a.symmetric_difference(b)
print(c)  # {1, 2, 3, 6, 7, 8}
```

### In-Place: symmetric_difference_update() / ^=

```python
a = {1, 2, 3, 4, 5}
b = {4, 5, 6, 7, 8}

# Mit ^= Operator
a ^= b
print(a)  # {1, 2, 3, 6, 7, 8}

# Mit symmetric_difference_update() Methode
a = {1, 2, 3, 4, 5}
a.symmetric_difference_update(b)
print(a)  # {1, 2, 3, 6, 7, 8}
```

### Praktische Anwendung

```python
# Features die sich geändert haben (hinzu oder weg)
version_1_features = {"login", "search", "export", "profile"}
version_2_features = {"login", "search", "import", "dashboard"}

geaendert = version_1_features ^ version_2_features
print(f"Geänderte Features: {geaendert}")
# {'export', 'profile', 'import', 'dashboard'}

# User die nur in einer von zwei Listen sind
liste_a = {"alice", "bob", "charlie"}
liste_b = {"bob", "diana", "eve"}

unterschiedlich = liste_a ^ liste_b
print(f"Nicht in beiden: {unterschiedlich}")
# {'alice', 'charlie', 'diana', 'eve'}

# Toggle-Logik (XOR für Sets)
aktive_flags = {"debug", "verbose"}
toggle = {"verbose", "trace"}

# Flags die getoggelt werden
aktive_flags ^= toggle
print(aktive_flags)  # {'debug', 'trace'}
```

---

## Subset und Superset Tests

### issubset() / <=

Prüft ob alle Elemente von A in B enthalten sind.

```python
a = {1, 2, 3}
b = {1, 2, 3, 4, 5}

# Mit <= Operator
print(a <= b)  # True - A ist Subset von B

# Mit issubset() Methode
print(a.issubset(b))  # True

# Subset von sich selbst
print(a <= a)  # True

# Proper Subset (echte Teilmenge) - <
print(a < b)   # True - A ist echtes Subset
print(a < a)   # False - nicht echt
```

### issuperset() / >=

Prüft ob B alle Elemente von A enthält.

```python
a = {1, 2, 3, 4, 5}
b = {1, 2, 3}

# Mit >= Operator
print(a >= b)  # True - A ist Superset von B

# Mit issuperset() Methode
print(a.issuperset(b))  # True

# Proper Superset - >
print(a > b)   # True - A ist echtes Superset
print(a > a)   # False - nicht echt
```

### Praktische Anwendung

```python
# Permissions Check
erforderliche_rechte = {"read", "write"}
user_rechte = {"read", "write", "delete", "admin"}

if erforderliche_rechte <= user_rechte:
    print("User hat alle erforderlichen Rechte")

# Feature-Check
minimale_features = {"login", "profile"}
app_features = {"login", "profile", "search", "export"}

if minimale_features <= app_features:
    print("App erfüllt Mindestanforderungen")

# Skills-Anforderung
job_anforderung = {"python", "sql", "git"}
bewerber_skills = {"python", "sql", "git", "docker", "aws"}

if job_anforderung <= bewerber_skills:
    print("Bewerber erfüllt alle Anforderungen")
else:
    fehlend = job_anforderung - bewerber_skills
    print(f"Fehlende Skills: {fehlend}")
```

---

## isdisjoint() - Keine Überschneidung

Prüft ob zwei Sets keine gemeinsamen Elemente haben.

```python
a = {1, 2, 3}
b = {4, 5, 6}
c = {3, 4, 5}

# Keine gemeinsamen Elemente
print(a.isdisjoint(b))  # True

# Haben gemeinsame Elemente
print(a.isdisjoint(c))  # False (3 ist gemeinsam)

# Äquivalent zu:
print(len(a & c) == 0)  # False
```

### Praktische Anwendung

```python
# Zeitslots ohne Überschneidung
meeting_a_zeiten = {10, 11, 12}  # 10-13 Uhr
meeting_b_zeiten = {14, 15, 16}  # 14-17 Uhr
meeting_c_zeiten = {12, 13, 14}  # 12-15 Uhr

if meeting_a_zeiten.isdisjoint(meeting_b_zeiten):
    print("Meeting A und B überschneiden sich nicht")

if not meeting_a_zeiten.isdisjoint(meeting_c_zeiten):
    print("Meeting A und C überschneiden sich!")
    konflikt = meeting_a_zeiten & meeting_c_zeiten
    print(f"Konflikt bei Stunden: {konflikt}")

# User-Gruppen sollten disjoint sein
gruppe_admins = {"alice", "bob"}
gruppe_guests = {"charlie", "diana"}

if gruppe_admins.isdisjoint(gruppe_guests):
    print("Keine User in beiden Gruppen")
else:
    overlap = gruppe_admins & gruppe_guests
    print(f"User in beiden Gruppen: {overlap}")
```

---

## Komplexe Operationen kombinieren

### Mehrere Operationen verketten

```python
# Drei User-Gruppen
admins = {"alice", "bob"}
moderators = {"bob", "charlie"}
users = {"charlie", "diana", "eve"}

# Nur Admins (nicht Moderator oder User)
nur_admins = admins - moderators - users
print(f"Nur Admins: {nur_admins}")  # {'alice'}

# In mindestens zwei Gruppen
in_zwei = ((admins & moderators) | 
           (admins & users) | 
           (moderators & users))
print(f"In zwei Gruppen: {in_zwei}")  # {'bob', 'charlie'}

# Alle mit Rechten, aber nicht normale User
privilegiert = (admins | moderators) - users
print(f"Privilegiert: {privilegiert}")  # {'alice', 'bob'}
```

### Set-Operationen mit Conditions

```python
# Produkte nach Eigenschaften
alle_produkte = {
    "laptop", "maus", "tastatur", "monitor", 
    "drucker", "scanner", "headset"
}

# Kategorien
elektronik = {"laptop", "monitor", "headset", "drucker"}
zubehoer = {"maus", "tastatur", "headset"}
buero = {"drucker", "scanner", "monitor"}

# Produkte die sowohl Elektronik als auch Zubehör sind
elektronik_zubehoer = elektronik & zubehoer
print(f"Elektronik & Zubehör: {elektronik_zubehoer}")

# Produkte die NICHT Büro sind
nicht_buero = alle_produkte - buero
print(f"Nicht Büro: {nicht_buero}")

# Produkte in genau einer Kategorie
eindeutig = (
    (elektronik - zubehoer - buero) |
    (zubehoer - elektronik - buero) |
    (buero - elektronik - zubehoer)
)
print(f"Eindeutig kategorisiert: {eindeutig}")
```

---

## Performance der Operationen

### Zeitkomplexität

```python
"""
Operation                  | Durchschnitt | Worst Case
---------------------------|--------------|------------
a | b (Union)             | O(len(a)+len(b)) | O(len(a)*len(b))
a & b (Intersection)      | O(min(len(a),len(b))) | O(len(a)*len(b))
a - b (Difference)        | O(len(a))    | O(len(a)*len(b))
a ^ b (Sym. Difference)   | O(len(a)+len(b)) | O(len(a)*len(b))
a <= b (Subset)           | O(len(a))    | O(len(a))
a.isdisjoint(b)           | O(min(len(a),len(b))) | O(len(a)*len(b))

Worst Case tritt bei vielen Hash-Kollisionen auf (sehr selten)
"""
```

### Benchmark

```python
import timeit

# Setup
a = set(range(10000))
b = set(range(5000, 15000))

# Union
union_time = timeit.timeit("a | b", globals=globals(), number=1000)
print(f"Union:        {union_time:.4f}s")

# Intersection
inter_time = timeit.timeit("a & b", globals=globals(), number=1000)
print(f"Intersection: {inter_time:.4f}s")

# Difference
diff_time = timeit.timeit("a - b", globals=globals(), number=1000)
print(f"Difference:   {diff_time:.4f}s")

# Intersection ist oft am schnellsten
# (beendet früh wenn kleines Set leer durchsucht)
```

---

## Operatoren vs. Methoden

### Wann welche verwenden?

```python
# OPERATOREN (|, &, -, ^)
# ✅ Kurz und lesbar
# ✅ Pythonisch
# ❌ Nur mit Sets, nicht mit anderen Iterables
# ❌ Erstellen immer neue Sets

a = {1, 2, 3}
b = {3, 4, 5}
c = a | b  # Nur wenn b ein Set ist

# METHODEN (.union(), .intersection(), etc.)
# ✅ Funktionieren mit beliebigen Iterables
# ✅ Klarere Intention
# ❌ Etwas verbose
# ✅ In-place Varianten verfügbar

a = {1, 2, 3}
b = [3, 4, 5]  # Liste!
c = a.union(b)  # Funktioniert!

# BEISPIELE

# ❌ FALSCH - Liste mit |
try:
    result = {1, 2} | [3, 4]
except TypeError:
    print("| funktioniert nicht mit Liste")

# ✅ RICHTIG - Methode verwenden
result = {1, 2}.union([3, 4])
print(result)  # {1, 2, 3, 4}

# In-place wenn möglich
a = {1, 2, 3}
a.update([4, 5])  # Effizienter als a = a | {4, 5}
```

---

## Praktische Beispiele

### Beispiel 1: Zugriffskontrolle

```python
class AccessControl:
    def __init__(self):
        self.user_permissions = {}
        self.role_permissions = {
            "admin": {"read", "write", "delete", "manage_users"},
            "editor": {"read", "write", "delete"},
            "viewer": {"read"}
        }
    
    def add_user(self, username, role):
        self.user_permissions[username] = self.role_permissions[role].copy()
    
    def add_permission(self, username, permission):
        if username in self.user_permissions:
            self.user_permissions[username].add(permission)
    
    def remove_permission(self, username, permission):
        if username in self.user_permissions:
            self.user_permissions[username].discard(permission)
    
    def has_permission(self, username, permission):
        return permission in self.user_permissions.get(username, set())
    
    def has_all_permissions(self, username, required):
        user_perms = self.user_permissions.get(username, set())
        return required <= user_perms
    
    def get_common_permissions(self, users):
        if not users:
            return set()
        perms = [self.user_permissions.get(u, set()) for u in users]
        return set.intersection(*perms)

# Verwendung
ac = AccessControl()
ac.add_user("alice", "admin")
ac.add_user("bob", "editor")
ac.add_user("charlie", "viewer")

print(ac.has_permission("alice", "delete"))  # True
print(ac.has_permission("charlie", "delete"))  # False

# Gemeinsame Permissions
common = ac.get_common_permissions(["alice", "bob", "charlie"])
print(f"Alle können: {common}")  # {'read'}
```

### Beispiel 2: Tag-basierte Suche

```python
articles = [
    {"id": 1, "title": "Python Intro", "tags": {"python", "tutorial", "beginner"}},
    {"id": 2, "title": "Advanced Python", "tags": {"python", "advanced", "oop"}},
    {"id": 3, "title": "Web Dev", "tags": {"javascript", "web", "tutorial"}},
    {"id": 4, "title": "Python Web", "tags": {"python", "web", "flask"}},
]

def search_articles(must_have=None, nice_to_have=None, exclude=None):
    """Sucht Artikel basierend auf Tags"""
    must_have = must_have or set()
    nice_to_have = nice_to_have or set()
    exclude = exclude or set()
    
    results = []
    for article in articles:
        tags = article["tags"]
        
        # Muss alle must_have Tags haben
        if not must_have <= tags:
            continue
        
        # Darf keine exclude Tags haben
        if tags & exclude:
            continue
        
        # Score basierend auf nice_to_have
        score = len(tags & nice_to_have)
        results.append((score, article))
    
    # Sortiert nach Score (absteigend)
    results.sort(reverse=True, key=lambda x: x[0])
    return [article for score, article in results]

# Suche: Python UND Tutorial
results = search_articles(must_have={"python", "tutorial"})
print("Python Tutorials:", [a["title"] for a in results])

# Suche: Python, gerne Web, aber nicht Advanced
results = search_articles(
    must_have={"python"},
    nice_to_have={"web"},
    exclude={"advanced"}
)
print("Python (nicht advanced):", [a["title"] for a in results])
```

### Beispiel 3: Dependency Resolution

```python
class DependencyResolver:
    def __init__(self):
        self.dependencies = {}
    
    def add_package(self, package, deps):
        """Fügt Package mit Dependencies hinzu"""
        self.dependencies[package] = set(deps)
    
    def resolve(self, package):
        """Löst alle Dependencies rekursiv auf"""
        if package not in self.dependencies:
            return {package}
        
        resolved = {package}
        for dep in self.dependencies[package]:
            resolved |= self.resolve(dep)
        
        return resolved
    
    def check_conflict(self, packages):
        """Prüft ob Packages konfliktfrei sind"""
        all_deps = set()
        for pkg in packages:
            pkg_deps = self.resolve(pkg)
            # Prüfe auf Konflikte (hier vereinfacht)
            all_deps |= pkg_deps
        return all_deps
    
    def find_minimal_set(self, required):
        """Findet minimales Set an Packages für Requirements"""
        needed = set()
        for req in required:
            needed |= self.resolve(req)
        return needed

# Beispiel
resolver = DependencyResolver()
resolver.add_package("flask", ["werkzeug", "jinja2"])
resolver.add_package("werkzeug", [])
resolver.add_package("jinja2", ["markupsafe"])
resolver.add_package("markupsafe", [])
resolver.add_package("requests", ["urllib3", "certifi"])
resolver.add_package("urllib3", [])
resolver.add_package("certifi", [])

# Alle Dependencies für Flask
flask_deps = resolver.resolve("flask")
print(f"Flask benötigt: {flask_deps}")
# {'flask', 'werkzeug', 'jinja2', 'markupsafe'}

# Minimales Set für mehrere Packages
needed = resolver.find_minimal_set(["flask", "requests"])
print(f"Für Flask + Requests nötig: {needed}")
```

---

## Häufige Patterns

### Pattern 1: Mehrere Bedingungen prüfen

```python
# User muss ALLE erforderlichen Rechte haben
erforderlich = {"read", "write"}
user_rechte = {"read", "write", "delete"}

if erforderlich <= user_rechte:
    print("Zugriff erlaubt")

# Äquivalent zu:
if all(recht in user_rechte for recht in erforderlich):
    print("Zugriff erlaubt")

# Set-Version ist eleganter und schneller!
```

### Pattern 2: Änderungen tracken

```python
# Vor und Nach Vergleich
vor = {"feature_a", "feature_b", "feature_c"}
nach = {"feature_b", "feature_c", "feature_d"}

hinzugefuegt = nach - vor
entfernt = vor - nach
unveraendert = vor & nach

print(f"+ {hinzugefuegt}")     # {'feature_d'}
print(f"- {entfernt}")         # {'feature_a'}
print(f"= {unveraendert}")     # {'feature_b', 'feature_c'}
```

### Pattern 3: Kategorisierung

```python
items = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10}

gerade = {x for x in items if x % 2 == 0}
ungerade = items - gerade  # Rest

prime = {2, 3, 5, 7}
composite = items - prime - {1}

print(f"Gerade: {gerade}")
print(f"Ungerade: {ungerade}")
print(f"Primzahlen: {prime}")
print(f"Zusammengesetzt: {composite}")
```

---

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [Set Types — set, frozenset](https://docs.python.org/3/library/stdtypes.html#set-types-set-frozenset) - Set Operationen Referenz
- [Set Objects](https://docs.python.org/3/library/stdtypes.html#set) - Detaillierte Dokumentation
- [Mathematical Set Operations](https://docs.python.org/3/library/stdtypes.html#set-types-set-frozenset) - Alle Operationen erklärt

---

## Verwandte Themen

- [[03_Mappings_und_Sets/02_Sets/01_Set Basics|Set Basics]] - Grundlagen wiederholen
- [[03_Mappings_und_Sets/02_Sets/03_Frozenset|Frozenset]] - Unveränderbare Sets
- [[03_Mappings_und_Sets/02_Sets/04_Set Comprehensions|Set Comprehensions]] - Elegante Set-Erzeugung
- [[03_Mappings_und_Sets/01_Dictionaries/05_Dict Views|Dict Views]] - Ähnliche Set-Operationen

---

← [[03_Mappings_und_Sets/02_Sets/01_Set Basics|Set Basics]] | [[INDEX|📑 Index]] | [[03_Mappings_und_Sets/02_Sets/03_Frozenset|Frozenset]] →