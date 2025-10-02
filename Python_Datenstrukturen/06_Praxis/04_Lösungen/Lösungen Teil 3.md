---
tags: [python, datenstrukturen, lösungen]
created: 2025-10-02
---

← [[06_Praxis/04_Lösungen/Lösungen Teil 2|Lösungen Teil 2]] | [[INDEX|📑 Index]] | [[06_Praxis/04_Lösungen/Lösungen Teil 4|Lösungen Teil 4]] →

# Lösungen Teil 3: Mappings und Sets

> [!tip] Hinweis
> Achte besonders auf die idiomatischen Dict- und Set-Operationen!

## 03_Mappings_und_Sets/01_Dictionaries

### Dict Basics - Übungen

**Übung 1: Dict-Operationen**
```python
# Erstellen
person = {
    "name": "Alice",
    "age": 30,
    "city": "Berlin"
}

# Zugriff
print(person["name"])        # Alice
print(person.get("age"))     # 30
print(person.get("email"))   # None
print(person.get("email", "keine@email.com"))  # keine@email.com

# Hinzufügen/Ändern
person["email"] = "alice@example.com"
person["age"] = 31

# Löschen
del person["city"]
email = person.pop("email")
print(f"Gelöschte Email: {email}")

# Keys, Values, Items
print(f"Keys: {list(person.keys())}")
print(f"Values: {list(person.values())}")
print(f"Items: {list(person.items())}")

# Iteration
for key, value in person.items():
    print(f"{key}: {value}")
```

**Übung 2: Dict Comprehension**
```python
# Quadrate
squares = {x: x**2 for x in range(10)}
print(squares)  # {0: 0, 1: 1, 2: 4, 3: 9, ...}

# String zu Länge
words = ["apple", "banana", "cherry"]
lengths = {word: len(word) for word in words}
print(lengths)  # {'apple': 5, 'banana': 6, 'cherry': 6}

# Filtern
data = {"a": 1, "b": 2, "c": 3, "d": 4}
filtered = {k: v for k, v in data.items() if v % 2 == 0}
print(filtered)  # {'b': 2, 'd': 4}

# Transformation
prices = {"apple": 1.20, "banana": 0.50, "cherry": 2.80}
euro_prices = {fruit: f"{price:.2f}€" for fruit, price in prices.items()}
print(euro_prices)  # {'apple': '1.20€', 'banana': '0.50€', ...}

# Von zwei Listen
keys = ["name", "age", "city"]
values = ["Alice", 30, "Berlin"]
person = {k: v for k, v in zip(keys, values)}
print(person)  # {'name': 'Alice', 'age': 30, 'city': 'Berlin'}
```

**Übung 3: Verschachtelte Dicts**
```python
# User-Datenbank
users = {
    "alice": {
        "name": "Alice Smith",
        "age": 30,
        "email": "alice@example.com",
        "roles": ["admin", "user"]
    },
    "bob": {
        "name": "Bob Jones",
        "age": 25,
        "email": "bob@example.com",
        "roles": ["user"]
    }
}

# Zugriff
print(users["alice"]["name"])  # Alice Smith
print(users["bob"]["roles"][0])  # user

# Sicherer Zugriff
email = users.get("charlie", {}).get("email", "Nicht gefunden")
print(email)  # Nicht gefunden

# Alle Admins finden
admins = [user["name"] for user in users.values() 
          if "admin" in user["roles"]]
print(f"Admins: {admins}")  # ['Alice Smith']

# Durchschnittsalter
avg_age = sum(user["age"] for user in users.values()) / len(users)
print(f"Durchschnittsalter: {avg_age}")  # 27.5
```

**Übung 4: setdefault und update**
```python
# setdefault - fügt Key hinzu wenn nicht vorhanden
stats = {}
stats.setdefault("views", 0)
stats["views"] += 1
stats["views"] += 1
print(stats)  # {'views': 2}

# Praktisch: Gruppierung
words = ["apple", "banana", "apricot", "cherry", "avocado"]
by_first_letter = {}

for word in words:
    first = word[0]
    by_first_letter.setdefault(first, []).append(word)

print(by_first_letter)
# {'a': ['apple', 'apricot', 'avocado'], 'b': ['banana'], 'c': ['cherry']}

# update - merged Dicts
default_config = {"debug": False, "timeout": 30}
user_config = {"debug": True, "port": 8080}

config = default_config.copy()
config.update(user_config)
print(config)  # {'debug': True, 'timeout': 30, 'port': 8080}

# Python 3.9+: Merge-Operator
merged = default_config | user_config
print(merged)  # Gleich wie oben
```

**Übung 5: Wort-Frequenz zählen**
```python
def count_words(text: str) -> dict:
    """Zählt Wort-Häufigkeiten"""
    # Methode 1: Manuell
    counts = {}
    words = text.lower().split()
    
    for word in words:
        # Entferne Satzzeichen
        word = word.strip('.,!?;:')
        if word:
            counts[word] = counts.get(word, 0) + 1
    
    return counts

text = "Python is great. Python is powerful. Is Python easy?"
word_counts = count_words(text)
print(word_counts)
# {'python': 3, 'is': 3, 'great': 1, 'powerful': 1, 'easy': 1}

# Top 3 häufigste Wörter
top_words = sorted(word_counts.items(), key=lambda x: x[1], reverse=True)[:3]
print(f"Top 3: {top_words}")

# Methode 2: Mit Counter (eleganter)
from collections import Counter

def count_words_v2(text: str) -> Counter:
    words = text.lower().split()
    words = [w.strip('.,!?;:') for w in words if w.strip('.,!?;:')]
    return Counter(words)

counts = count_words_v2(text)
print(f"Top 3: {counts.most_common(3)}")
```

## 03_Mappings_und_Sets/02_Sets

### Set Basics - Übungen

**Übung 1: Set-Operationen**
```python
# Erstellen
numbers = {1, 2, 3, 4, 5}
even = {2, 4, 6, 8}

# Union (Vereinigung)
print(numbers | even)           # {1, 2, 3, 4, 5, 6, 8}
print(numbers.union(even))      # Gleich

# Intersection (Schnittmenge)
print(numbers & even)           # {2, 4}
print(numbers.intersection(even))  # Gleich

# Difference (Differenz)
print(numbers - even)           # {1, 3, 5}
print(numbers.difference(even))  # Gleich

# Symmetric Difference (Symmetrische Differenz)
print(numbers ^ even)           # {1, 3, 5, 6, 8}
print(numbers.symmetric_difference(even))  # Gleich

# Subset/Superset
small = {1, 2}
print(small <= numbers)  # True (Subset)
print(numbers >= small)  # True (Superset)
print(small < numbers)   # True (echte Teilmenge)
```

**Übung 2: Set-Modifikation**
```python
fruits = {"apple", "banana", "cherry"}

# Hinzufügen
fruits.add("date")
print(fruits)  # {'apple', 'banana', 'cherry', 'date'}

# Mehrere hinzufügen
fruits.update(["elderberry", "fig"])
print(fruits)

# Entfernen
fruits.remove("banana")  # KeyError wenn nicht vorhanden
fruits.discard("grape")  # Kein Error wenn nicht vorhanden

# Pop (zufälliges Element)
item = fruits.pop()
print(f"Removed: {item}")

# Clear
fruits.clear()
print(fruits)  # set()
```

**Übung 3: Duplikate entfernen**
```python
def remove_duplicates(lst: list) -> list:
    """Entfernt Duplikate, behält Reihenfolge (Python 3.7+)"""
    return list(dict.fromkeys(lst))

# Oder mit Set (keine Reihenfolge garantiert vor 3.7)
def remove_duplicates_v2(lst: list) -> list:
    """Entfernt Duplikate"""
    return list(set(lst))

numbers = [1, 2, 2, 3, 4, 3, 5, 1]
print(remove_duplicates(numbers))     # [1, 2, 3, 4, 5]
print(remove_duplicates_v2(numbers))  # Reihenfolge variiert

# Praktisch: Unique Werte aus mehreren Listen
list1 = [1, 2, 3]
list2 = [3, 4, 5]
list3 = [5, 6, 7]

all_unique = set(list1) | set(list2) | set(list3)
print(sorted(all_unique))  # [1, 2, 3, 4, 5, 6, 7]

# Gemeinsame Elemente
common = set(list1) & set(list2) & set(list3)
print(common)  # set() (keine gemeinsamen)

common_12 = set(list1) & set(list2)
print(common_12)  # {3}
```

**Übung 4: Set Comprehension**
```python
# Unique Quadrate
numbers = [1, 2, 2, 3, 3, 3]
unique_squares = {x**2 for x in numbers}
print(unique_squares)  # {1, 4, 9}

# Vokale extrahieren
text = "Hello World"
vowels = {c.lower() for c in text if c.lower() in 'aeiou'}
print(vowels)  # {'e', 'o'}

# Primlängen von Wörtern
words = ["a", "ab", "abc", "abcd", "abcde"]

def is_prime(n):
    if n < 2:
        return False
    for i in range(2, int(n**0.5) + 1):
        if n % i == 0:
            return False
    return True

prime_lengths = {len(w) for w in words if is_prime(len(w))}
print(prime_lengths)  # {2, 3, 5}
```

**Übung 5: Frozenset**
```python
# Immutable Set
frozen = frozenset([1, 2, 3])
print(frozen)  # frozenset({1, 2, 3})

# Kann als Dict-Key verwendet werden
cache = {
    frozenset([1, 2]): "result_a",
    frozenset([3, 4]): "result_b"
}
print(cache[frozenset([1, 2])])  # result_a

# Frozenset-Operationen
fs1 = frozenset([1, 2, 3])
fs2 = frozenset([2, 3, 4])

print(fs1 | fs2)  # frozenset({1, 2, 3, 4})
print(fs1 & fs2)  # frozenset({2, 3})

# Praktisch: Unveränderliche Gruppen
ADMIN_PERMS = frozenset(["read", "write", "delete"])
USER_PERMS = frozenset(["read"])

def has_permission(user_perms: frozenset, required: str) -> bool:
    return required in user_perms

print(has_permission(ADMIN_PERMS, "delete"))  # True
print(has_permission(USER_PERMS, "delete"))   # False
```

## 03_Mappings_und_Sets/03_Collections_Module

### Counter - Übungen

**Übung 1: Counter Basics**
```python
from collections import Counter

# Von Liste
numbers = [1, 2, 2, 3, 3, 3, 4, 4, 4, 4]
counts = Counter(numbers)
print(counts)  # Counter({4: 4, 3: 3, 2: 2, 1: 1})

# Von String
text = "hello world"
char_counts = Counter(text)
print(char_counts)  # Counter({'l': 3, 'o': 2, ...})

# Von Wörtern
words = "the quick brown fox jumps over the lazy dog".split()
word_counts = Counter(words)
print(word_counts)  # Counter({'the': 2, ...})

# most_common()
print(f"Top 3: {counts.most_common(3)}")  # [(4, 4), (3, 3), (2, 2)]

# Zugriff wie Dict
print(counts[3])   # 3
print(counts[99])  # 0 (kein KeyError!)
```

**Übung 2: Counter-Arithmetik**
```python
from collections import Counter

# Addition
c1 = Counter(['a', 'b', 'c', 'a'])
c2 = Counter(['a', 'b', 'd'])

print(c1 + c2)  # Counter({'a': 3, 'b': 2, 'c': 1, 'd': 1})

# Subtraktion
print(c1 - c2)  # Counter({'a': 1, 'c': 1})

# Intersection (minimum)
print(c1 & c2)  # Counter({'a': 1, 'b': 1})

# Union (maximum)
print(c1 | c2)  # Counter({'a': 2, 'b': 1, 'c': 1, 'd': 1})
```

**Übung 3: Häufigste Elemente finden**
```python
from collections import Counter

def analyze_text(text: str):
    """Analysiert Text-Häufigkeiten"""
    # Wörter
    words = text.lower().split()
    word_counter = Counter(words)
    
    # Zeichen (ohne Leerzeichen)
    chars = [c for c in text.lower() if c.isalpha()]
    char_counter = Counter(chars)
    
    print(f"Total Wörter: {sum(word_counter.values())}")
    print(f"Unique Wörter: {len(word_counter)}")
    print(f"\nTop 5 Wörter:")
    for word, count in word_counter.most_common(5):
        print(f"  {word}: {count}")
    
    print(f"\nTop 5 Buchstaben:")
    for char, count in char_counter.most_common(5):
        print(f"  {char}: {count}")

text = """
Python is an interpreted high-level programming language.
Python's design philosophy emphasizes code readability.
"""

analyze_text(text)
```

**Übung 4: Vote Counting**
```python
from collections import Counter

def count_votes(votes: list) -> dict:
    """Zählt Stimmen und ermittelt Gewinner"""
    counter = Counter(votes)
    
    total = sum(counter.values())
    winner = counter.most_common(1)[0][0]
    
    results = {
        "total_votes": total,
        "winner": winner,
        "results": dict(counter),
        "percentages": {
            candidate: (count / total * 100)
            for candidate, count in counter.items()
        }
    }
    
    return results

votes = ["Alice", "Bob", "Alice", "Charlie", "Alice", "Bob", "Alice"]
results = count_votes(votes)

print(f"Total: {results['total_votes']}")
print(f"Gewinner: {results['winner']}")
print("\nErgebnisse:")
for candidate, count in results['results'].items():
    percentage = results['percentages'][candidate]
    print(f"  {candidate}: {count} ({percentage:.1f}%)")

# Output:
# Total: 7
# Gewinner: Alice
# Ergebnisse:
#   Alice: 4 (57.1%)
#   Bob: 2 (28.6%)
#   Charlie: 1 (14.3%)
```

### DefaultDict - Übungen

**Übung 1: DefaultDict Basics**
```python
from collections import defaultdict

# Mit List als Default
groups = defaultdict(list)

groups['fruits'].append('apple')
groups['fruits'].append('banana')
groups['vegetables'].append('carrot')

print(dict(groups))
# {'fruits': ['apple', 'banana'], 'vegetables': ['carrot']}

# Mit Int als Default (Counter-ähnlich)
counts = defaultdict(int)

for char in "hello":
    counts[char] += 1

print(dict(counts))  # {'h': 1, 'e': 1, 'l': 2, 'o': 1}

# Mit Set als Default
tags = defaultdict(set)

tags['python'].add('programming')
tags['python'].add('language')
tags['java'].add('programming')

print(dict(tags))
# {'python': {'programming', 'language'}, 'java': {'programming'}}
```

**Übung 2: Gruppierung mit DefaultDict**
```python
from collections import defaultdict

# Gruppiere Studenten nach Note
students = [
    {"name": "Alice", "grade": "A"},
    {"name": "Bob", "grade": "B"},
    {"name": "Charlie", "grade": "A"},
    {"name": "Diana", "grade": "C"},
    {"name": "Eve", "grade": "B"},
]

by_grade = defaultdict(list)
for student in students:
    by_grade[student['grade']].append(student['name'])

print(dict(by_grade))
# {'A': ['Alice', 'Charlie'], 'B': ['Bob', 'Eve'], 'C': ['Diana']}

# Gruppiere Wörter nach Länge
words = ["apple", "pie", "banana", "cat", "cherry", "dog"]

by_length = defaultdict(list)
for word in words:
    by_length[len(word)].append(word)

print(dict(by_length))
# {5: ['apple', 'berry'], 3: ['pie', 'cat', 'dog'], 6: ['banana', 'cherry']}
```

**Übung 3: Graph mit DefaultDict**
```python
from collections import defaultdict

class Graph:
    """Einfacher gerichteter Graph"""
    
    def __init__(self):
        self.graph = defaultdict(list)
    
    def add_edge(self, from_node, to_node):
        """Fügt Kante hinzu"""
        self.graph[from_node].append(to_node)
    
    def neighbors(self, node):
        """Gibt Nachbarn zurück"""
        return self.graph[node]
    
    def has_edge(self, from_node, to_node):
        """Prüft ob Kante existiert"""
        return to_node in self.graph[from_node]
    
    def __repr__(self):
        return f"Graph({dict(self.graph)})"

# Erstelle Graph
g = Graph()
g.add_edge('A', 'B')
g.add_edge('A', 'C')
g.add_edge('B', 'C')
g.add_edge('C', 'D')

print(g)
print(f"Nachbarn von A: {g.neighbors('A')}")  # ['B', 'C']
print(f"A -> B existiert? {g.has_edge('A', 'B')}")  # True
print(f"B -> A existiert? {g.has_edge('B', 'A')}")  # False
```

**Übung 4: Verschachteltes DefaultDict**
```python
from collections import defaultdict

# DefaultDict von DefaultDicts
nested = defaultdict(lambda: defaultdict(int))

# Tracking User-Actions
nested['alice']['login'] += 1
nested['alice']['click'] += 5
nested['bob']['login'] += 2
nested['bob']['purchase'] += 1

print(dict(nested))
# {'alice': {'login': 1, 'click': 5}, 'bob': {'login': 2, 'purchase': 1}}

# Praktisch: Multi-Level Gruppierung
data = [
    {"country": "DE", "city": "Berlin", "population": 3_600_000},
    {"country": "DE", "city": "Munich", "population": 1_500_000},
    {"country": "FR", "city": "Paris", "population": 2_200_000},
    {"country": "FR", "city": "Lyon", "population": 500_000},
]

by_country = defaultdict(lambda: defaultdict(int))

for item in data:
    country = item['country']
    city = item['city']
    pop = item['population']
    by_country[country][city] = pop

print(dict(by_country))
# {'DE': {'Berlin': 3600000, 'Munich': 1500000}, 
#  'FR': {'Paris': 2200000, 'Lyon': 500000}}
```

### ChainMap - Übungen

**Übung 1: ChainMap Basics**
```python
from collections import ChainMap

# Mehrere Dicts kombinieren
defaults = {"color": "red", "size": "M"}
user_prefs = {"color": "blue"}
session = {"size": "L"}

# ChainMap sucht in Reihenfolge
config = ChainMap(session, user_prefs, defaults)

print(config['color'])  # blue (aus user_prefs)
print(config['size'])   # L (aus session)

# Änderungen gehen ins erste Dict
config['theme'] = 'dark'
print(session)  # {'size': 'L', 'theme': 'dark'}
```

**Übung 2: Configuration Layers**
```python
from collections import ChainMap

class Config:
    """Konfiguration mit mehreren Layern"""
    
    def __init__(self):
        # Layer-Hierarchie: CLI > User > Defaults
        self.defaults = {
            "debug": False,
            "timeout": 30,
            "port": 8080,
        }
        self.user_config = {}
        self.cli_args = {}
        
        self.config = ChainMap(
            self.cli_args,
            self.user_config,
            self.defaults
        )
    
    def set_user(self, **kwargs):
        """Setzt User-Konfiguration"""
        self.user_config.update(kwargs)
    
    def set_cli(self, **kwargs):
        """Setzt CLI-Argumente"""
        self.cli_args.update(kwargs)
    
    def get(self, key, default=None):
        """Holt Konfigurations-Wert"""
        return self.config.get(key, default)
    
    def __repr__(self):
        return f"Config({dict(self.config)})"

# Verwendung
cfg = Config()
print(cfg.get('port'))  # 8080 (default)

cfg.set_user(port=9000, debug=True)
print(cfg.get('port'))  # 9000 (user override)

cfg.set_cli(port=9999)
print(cfg.get('port'))  # 9999 (CLI override)

print(cfg)
```

**Übung 3: Scope Chain (wie JavaScript)**
```python
from collections import ChainMap

class Scope:
    """Variable Scoping"""
    
    def __init__(self):
        self.global_scope = {}
        self.scopes = ChainMap(self.global_scope)
    
    def push_scope(self):
        """Neuer Scope (z.B. Funktions-Block)"""
        self.scopes = self.scopes.new_child()
    
    def pop_scope(self):
        """Verlasse Scope"""
        self.scopes = self.scopes.parents
    
    def set(self, name, value):
        """Setze Variable im aktuellen Scope"""
        self.scopes[name] = value
    
    def get(self, name):
        """Hole Variable (sucht in allen Scopes)"""
        return self.scopes.get(name)

# Test
scope = Scope()
scope.set('x', 10)  # Global

scope.push_scope()  # Neuer Scope
scope.set('x', 20)  # Lokales x
scope.set('y', 30)  # Lokales y
print(scope.get('x'))  # 20 (lokal)
print(scope.get('y'))  # 30

scope.pop_scope()  # Zurück zu global
print(scope.get('x'))  # 10 (global wieder sichtbar)
print(scope.get('y'))  # None (war lokal)
```

## Verwandte Themen

- [[03_Mappings_und_Sets/01_Dictionaries/01_Dict Basics|Dictionaries]]
- [[03_Mappings_und_Sets/02_Sets/01_Set Basics|Sets]]
- [[03_Mappings_und_Sets/03_Collections_Module/01_Counter|Counter]]
- [[03_Mappings_und_Sets/03_Collections_Module/02_DefaultDict|DefaultDict]]

---
← [[06_Praxis/04_Lösungen/Lösungen Teil 2|Lösungen Teil 2]] | [[INDEX|📑 Index]] | [[06_Praxis/04_Lösungen/Lösungen Teil 4|Lösungen Teil 4]] →