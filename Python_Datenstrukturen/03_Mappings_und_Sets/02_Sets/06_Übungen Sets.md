---
tags: [python, datenstrukturen, sets, übungen]
created: 2025-10-01
---

← [[03_Mappings_und_Sets/02_Sets/05_Set Performance|Set Performance]] | [[INDEX|📑 Index]] | [[03_Mappings_und_Sets/03_Collections_Module/01_Counter|Counter]] →

# Übungen: Sets

> [!tip] Lernziel
> Diese Übungen helfen dir, Sets sicher für Mengenoperationen, Duplikat-Entfernung und schnelle Lookups einzusetzen.

## 📝 Übung 1: Unique Characters (Leicht)

Erstelle eine Funktion `unique_chars(text)`, die alle eindeutigen Zeichen in einem Text zurückgibt (case-insensitive).

**Anforderungen:**
- Ignoriere Leerzeichen
- Case-insensitive
- Gib Set zurück

```python
def unique_chars(text):
    # Dein Code hier
    pass

# Test
text = "Hello World"
chars = unique_chars(text)
print(sorted(chars))
```

**Erwarteter Output:**
```
['d', 'e', 'h', 'l', 'o', 'r', 'w']
```

---

## 📝 Übung 2: Gemeinsame Elemente (Leicht-Mittel)

Schreibe eine Funktion `finde_gemeinsame(*listen)`, die gemeinsame Elemente in mehreren Listen findet.

**Anforderungen:**
- Akzeptiert beliebig viele Listen
- Gibt Set der gemeinsamen Elemente zurück
- Funktioniert mit leeren Listen

```python
def finde_gemeinsame(*listen):
    # Dein Code hier
    pass

# Test
liste1 = [1, 2, 3, 4, 5]
liste2 = [3, 4, 5, 6, 7]
liste3 = [4, 5, 6, 7, 8]

gemeinsam = finde_gemeinsame(liste1, liste2, liste3)
print(sorted(gemeinsam))

# Mit nur einer Liste
einzeln = finde_gemeinsame([1, 2, 3])
print(sorted(einzeln))
```

**Erwarteter Output:**
```
[4, 5]
[1, 2, 3]
```

---

## 📝 Übung 3: Duplikat-Detektor (Mittel)

Erstelle eine Funktion `finde_duplikate(items)`, die alle Elemente findet, die mehr als einmal vorkommen.

**Anforderungen:**
- Gibt Set der duplizierten Elemente zurück
- Funktioniert mit verschiedenen Typen
- Effizient implementiert

```python
def finde_duplikate(items):
    # Dein Code hier
    pass

# Test
zahlen = [1, 2, 3, 2, 4, 5, 1, 6, 7, 1]
dups = finde_duplikate(zahlen)
print(sorted(dups))

woerter = ["apfel", "banane", "apfel", "kirsche", "banane"]
dups2 = finde_duplikate(woerter)
print(sorted(dups2))
```

**Erwarteter Output:**
```
[1, 2]
['apfel', 'banane']
```

---

## 📝 Übung 4: Set-Statistiken (Mittel)

Schreibe eine Funktion `set_statistiken(sets)`, die Statistiken über eine Liste von Sets berechnet.

**Anforderungen:**
- Gibt Dict mit Statistiken zurück
- Berechnet: Total unique, gemeinsam in allen, nur in einem
- Findet größtes und kleinstes Set

```python
def set_statistiken(sets):
    # Dein Code hier
    # Rückgabe: Dict mit Statistiken
    pass

# Test
sets = [
    {1, 2, 3, 4},
    {3, 4, 5, 6},
    {4, 5, 6, 7, 8}
]

stats = set_statistiken(sets)
print(stats)
```

**Erwarteter Output:**
```python
{
    'total_unique': 8,
    'gemeinsam_alle': {4},
    'nur_in_einem': {1, 2, 7, 8},
    'groesstes_set': {4, 5, 6, 7, 8},
    'kleinstes_set': {1, 2, 3, 4},
    'durchschnitt_groesse': 4.33
}
```

---

## 📝 Übung 5: Tag-Filter (Mittel)

Implementiere ein Tag-Filtering-System für Artikel.

**Anforderungen:**
- Funktion `filter_artikel(artikel, must_have, exclude)`
- must_have: Artikel müssen alle diese Tags haben
- exclude: Artikel dürfen keine dieser Tags haben
- Gibt gefilterte Liste zurück

```python
def filter_artikel(artikel, must_have=None, exclude=None):
    # Dein Code hier
    pass

# Test
artikel = [
    {"id": 1, "titel": "Python Intro", "tags": {"python", "tutorial", "beginner"}},
    {"id": 2, "titel": "Advanced Python", "tags": {"python", "advanced", "oop"}},
    {"id": 3, "titel": "Web Dev", "tags": {"javascript", "web", "tutorial"}},
    {"id": 4, "titel": "Python Web", "tags": {"python", "web", "flask"}},
]

# Nur Python, keine Advanced
result = filter_artikel(
    artikel, 
    must_have={"python"}, 
    exclude={"advanced"}
)
print([a["id"] for a in result])

# Tutorial, kein Python
result2 = filter_artikel(
    artikel,
    must_have={"tutorial"},
    exclude={"python"}
)
print([a["id"] for a in result2])
```

**Erwarteter Output:**
```
[1, 4]
[3]
```

---

## 📝 Übung 6: Permissions Checker (Mittel-Schwer)

Erstelle ein Berechtigungssystem mit Sets.

**Anforderungen:**
- Klasse `PermissionManager` mit User-Permissions
- Methoden: add_user, grant, revoke, has_permission, has_all
- Verwende frozensets für Rollen-Templates

```python
class PermissionManager:
    def __init__(self):
        # Dein Code hier
        pass
    
    def add_user(self, username, permissions=None):
        # Fügt User mit Permissions hinzu
        pass
    
    def grant(self, username, permission):
        # Gibt Permission
        pass
    
    def revoke(self, username, permission):
        # Entzieht Permission
        pass
    
    def has_permission(self, username, permission):
        # Prüft einzelne Permission
        pass
    
    def has_all(self, username, required):
        # Prüft ob User alle required Permissions hat
        pass

# Test
pm = PermissionManager()
pm.add_user("alice", {"read", "write"})
pm.add_user("bob", {"read"})

print(pm.has_permission("alice", "write"))  # True
print(pm.has_permission("bob", "write"))    # False

pm.grant("bob", "write")
print(pm.has_permission("bob", "write"))    # True

print(pm.has_all("alice", {"read", "write"}))  # True
print(pm.has_all("bob", {"read", "write", "delete"}))  # False
```

**Erwarteter Output:**
```
True
False
True
True
False
```

---

## 📝 Übung 7: Graph Connectivity (Schwer)

Implementiere Funktionen zur Graph-Analyse mit Sets.

**Anforderungen:**
- Funktion `sind_verbunden(graph, start, ziel)` - BFS/DFS
- Funktion `finde_komponenten(graph)` - Alle zusammenhängenden Komponenten
- Graph als Dict von Sets (Adjazenzliste)

```python
def sind_verbunden(graph, start, ziel):
    # Prüft ob Pfad zwischen start und ziel existiert
    pass

def finde_komponenten(graph):
    # Findet alle zusammenhängenden Komponenten
    # Gibt Liste von Sets zurück
    pass

# Test
graph = {
    'A': {'B', 'C'},
    'B': {'A', 'D'},
    'C': {'A'},
    'D': {'B'},
    'E': {'F'},
    'F': {'E'}
}

print(sind_verbunden(graph, 'A', 'D'))  # True
print(sind_verbunden(graph, 'A', 'E'))  # False

komponenten = finde_komponenten(graph)
print(sorted([sorted(k) for k in komponenten]))
```

**Erwarteter Output:**
```
True
False
[['A', 'B', 'C', 'D'], ['E', 'F']]
```

---

## 📝 Übung 8: Word Chain (Schwer)

Erstelle ein Wort-Ketten-Spiel: Finde Wörter die sich nur um einen Buchstaben unterscheiden.

**Anforderungen:**
- Funktion `finde_nachbarn(wort, woerterbuch)` 
- Funktion `wort_kette(start, ziel, woerterbuch)`
- Verwendet Sets für effiziente Suche

```python
def finde_nachbarn(wort, woerterbuch):
    # Findet alle Wörter die sich um 1 Buchstaben unterscheiden
    pass

def wort_kette(start, ziel, woerterbuch):
    # Findet kürzesten Pfad von start zu ziel
    # Gibt Liste von Wörtern zurück
    pass

# Test
woerter = {"cat", "bat", "hat", "hot", "dot", "dog", "lot", "log", "cog"}

nachbarn = finde_nachbarn("cat", woerter)
print(sorted(nachbarn))

kette = wort_kette("cat", "dog", woerter)
if kette:
    print(" -> ".join(kette))
```

**Erwarteter Output:**
```
['bat', 'hat']
cat -> bat -> hat -> hot -> dot -> dog
```

---

## 📝 Übung 9: Set Algebra System (Schwer)

Implementiere ein System für erweiterte Mengenalgebra.

**Anforderungen:**
- Klasse `SetExpression` für Mengenausdrücke
- Unterstützt: union, intersection, difference, complement
- Method Chaining
- Evaluation zu finalem Set

```python
class SetExpression:
    def __init__(self, initial_set):
        # Dein Code hier
        pass
    
    def union(self, other):
        # Vereinigung
        return self
    
    def intersection(self, other):
        # Schnittmenge
        return self
    
    def difference(self, other):
        # Differenz
        return self
    
    def complement(self, universal):
        # Komplement bezüglich Universum
        return self
    
    def evaluate(self):
        # Gibt finales Set zurück
        pass

# Test
universal = set(range(20))
a = SetExpression({1, 2, 3, 4, 5})
b = SetExpression({4, 5, 6, 7, 8})

# (A ∪ B) - {6, 7}
result = a.union(b.evaluate()).difference({6, 7}).evaluate()
print(sorted(result))

# A ∩ (B ∪ {10, 11})
result2 = a.intersection(
    SetExpression(b.evaluate()).union({10, 11}).evaluate()
).evaluate()
print(sorted(result2))
```

**Erwarteter Output:**
```
[1, 2, 3, 4, 5, 8]
[4, 5]
```

---

## 📝 Übung 10: Social Network Analysis (Experte)

Implementiere ein Analyse-System für soziale Netzwerke mit Sets.

**Anforderungen:**
- Klasse `SocialNetwork` für Freundschafts-Graph
- Methoden: add_friendship, remove_friendship, common_friends
- find_cliques, suggest_friends, degrees_of_separation
- Alles mit Set-Operationen optimiert

```python
class SocialNetwork:
    def __init__(self):
        # Dein Code hier
        pass
    
    def add_friendship(self, person1, person2):
        # Fügt bidirektionale Freundschaft hinzu
        pass
    
    def common_friends(self, person1, person2):
        # Findet gemeinsame Freunde
        pass
    
    def suggest_friends(self, person):
        # Schlägt Freunde vor (Freunde von Freunden)
        pass
    
    def find_cliques(self, min_size=3):
        # Findet Gruppen wo alle miteinander befreundet sind
        pass
    
    def degrees_of_separation(self, person1, person2):
        # Kürzeste Distanz zwischen zwei Personen
        pass

# Test
sn = SocialNetwork()
sn.add_friendship("Alice", "Bob")
sn.add_friendship("Alice", "Charlie")
sn.add_friendship("Bob", "Charlie")
sn.add_friendship("Bob", "Diana")
sn.add_friendship("Charlie", "Diana")

common = sn.common_friends("Alice", "Diana")
print(sorted(common))

suggestions = sn.suggest_friends("Alice")
print(sorted(suggestions))

distance = sn.degrees_of_separation("Alice", "Diana")
print(f"Distanz: {distance}")
```

**Erwarteter Output:**
```
['Bob', 'Charlie']
['Diana']
Distanz: 2
```

---

## 💡 Lösungsstrategien

### Für Übung 1-3 (Grundlagen):
- Nutze Set Comprehensions
- `set()` für Duplikat-Entfernung
- Intersection (`&`) für gemeinsame Elemente

### Für Übung 4-6 (Fortgeschritten):
- Set-Operationen kombinieren (`&`, `|`, `-`)
- frozenset für unveränderbare Sets
- Subset-Tests (`<=`, `>=`)

### Für Übung 7-8 (Profi):
- BFS/DFS mit Sets für besuchte Knoten
- Graph als Dict von Sets
- Queue/Stack mit Set für schnelle Lookups

### Für Übung 9-10 (Experte):
- Method Chaining mit `return self`
- Komplexe Set-Operationen kombinieren
- Rekursive oder iterative Graph-Algorithmen
- Performance mit Sets optimieren

---

## 🔍 Selbst-Check

Nach diesen Übungen solltest du:

✅ Sets für Duplikat-Entfernung nutzen können
✅ Set-Operationen (Union, Intersection, etc.) beherrschen
✅ Sets für schnelle Membership-Tests einsetzen
✅ frozensets als Dict-Keys verwenden können
✅ Graph-Algorithmen mit Sets implementieren
✅ Performance-Vorteile von Sets verstehen

---

## 🎯 Bonus-Herausforderungen

### Challenge 1: Sudoku Validator

Validiere Sudoku-Felder mit Sets:
- Prüfe Zeilen, Spalten, 3x3-Boxen
- Jede Menge muss {1,2,3,4,5,6,7,8,9} sein
- Nutze Set-Operationen für Validation

### Challenge 2: Spell Checker

Implementiere einen einfachen Spell Checker:
- Dictionary als Set von korrekten Wörtern
- Edit Distance 1 (ein Buchstabe anders)
- Vorschläge für falsch geschriebene Wörter
- Effiziente Suche mit Sets

### Challenge 3: Dependency Resolver

Erstelle einen Package Dependency Resolver:
- Packages mit Dependencies als Sets
- Topological Sort für Install-Reihenfolge
- Erkennung zirkulärer Dependencies
- Minimales Set benötigter Packages

---

## 📚 Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [Set Types](https://docs.python.org/3/library/stdtypes.html#set-types-set-frozenset) - Set Referenz
- [Built-in Types](https://docs.python.org/3/library/stdtypes.html) - Alle Typen
- [Collections](https://docs.python.org/3/library/collections.html) - Erweiterte Collections
- [itertools](https://docs.python.org/3/library/itertools.html) - Für Kombinationen

---

## 🎓 Verwandte Themen

- [[03_Mappings_und_Sets/02_Sets/01_Set Basics|Set Basics]] - Grundlagen wiederholen
- [[03_Mappings_und_Sets/02_Sets/02_Set Operationen|Set Operationen]] - Alle Operationen
- [[03_Mappings_und_Sets/02_Sets/03_Frozenset|Frozenset]] - Unveränderbare Sets
- [[03_Mappings_und_Sets/02_Sets/04_Set Comprehensions|Set Comprehensions]] - Elegante Syntax
- [[03_Mappings_und_Sets/02_Sets/05_Set Performance|Set Performance]] - Performance-Tips
- [[06_Praxis/04_Lösungen/Lösungen Teil 3|Lösungen Teil 3]] - Musterlösungen

---

← [[03_Mappings_und_Sets/02_Sets/05_Set Performance|Set Performance]] | [[INDEX|📑 Index]] | [[03_Mappings_und_Sets/03_Collections_Module/01_Counter|Counter]] →