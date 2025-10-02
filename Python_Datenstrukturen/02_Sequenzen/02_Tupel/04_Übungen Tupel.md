---
tags: [python, datenstrukturen, tuple, übungen, namedtuple]
created: 2025-10-01
---

← [[02_Sequenzen/02_Tupel/03_Named Tuples|Named Tuples]] | [[INDEX|📑 Index]] | [[02_Sequenzen/03_Slicing/01_Slicing Grundlagen|Slicing Grundlagen]] →

# Übungen: Tupel

> [!tip] Lernziel
> Diese Übungen festigen dein Verständnis von Tupeln, Tuple Unpacking, Named Tuples und deren praktischen Anwendungen. Die Übungen decken alle Aspekte ab: Basics, Unveränderlichkeit, Unpacking und Named Tuples.

## 📝 Übungsaufgaben

### Übung 1: Tupel Basics - Koordinaten (Leicht)

Erstelle Funktionen zur Arbeit mit 2D-Koordinaten als Tupel.

**Anforderungen:**
- `create_point(x, y)` - Erstellt Punkt-Tupel
- `distance_from_origin(point)` - Berechnet Distanz vom Ursprung
- `midpoint(p1, p2)` - Berechnet Mittelpunkt zwischen zwei Punkten
- `translate(point, dx, dy)` - Verschiebt Punkt

**Beispiel:**
```python
import math

def create_point(x, y):
    # Dein Code hier
    pass

def distance_from_origin(point):
    # Dein Code hier
    pass

def midpoint(p1, p2):
    # Dein Code hier
    pass

def translate(point, dx, dy):
    # Dein Code hier
    pass

# Test
p1 = create_point(3, 4)
print(f"Punkt: {p1}")
print(f"Distanz: {distance_from_origin(p1)}")

p2 = create_point(9, 12)
mid = midpoint(p1, p2)
print(f"Mittelpunkt: {mid}")

p3 = translate(p1, 10, 20)
print(f"Verschoben: {p3}")
```

**Erwarteter Output:**
```
Punkt: (3, 4)
Distanz: 5.0
Mittelpunkt: (6.0, 8.0)
Verschoben: (13, 24)
```

<details>
<summary>💡 Hinweis</summary>

- Tupel erstellen: `return (x, y)`
- Distanz: `math.sqrt(x**2 + y**2)`
- Mittelpunkt: `((x1+x2)/2, (y1+y2)/2)`
- Translate: `(x+dx, y+dy)` - neues Tupel erstellen
</details>

---

### Übung 2: Tuple Unpacking - Swap und Rotation (Leicht-Mittel)

Implementiere verschiedene Swap- und Rotations-Funktionen mit Tuple Unpacking.

**Anforderungen:**
- `swap(a, b)` - Tauscht zwei Werte
- `rotate_three(a, b, c)` - Rotiert drei Werte (a→b, b→c, c→a)
- `swap_first_last(sequence)` - Tauscht erstes und letztes Element
- `extract_min_max(numbers)` - Gibt (min, max, rest) zurück

**Beispiel:**
```python
def swap(a, b):
    # Dein Code hier
    pass

def rotate_three(a, b, c):
    # Dein Code hier
    pass

def swap_first_last(sequence):
    # Dein Code hier
    pass

def extract_min_max(numbers):
    # Dein Code hier
    pass

# Test
a, b = 5, 10
a, b = swap(a, b)
print(f"Getauscht: a={a}, b={b}")

x, y, z = 1, 2, 3
x, y, z = rotate_three(x, y, z)
print(f"Rotiert: x={x}, y={y}, z={z}")

data = [1, 2, 3, 4, 5]
result = swap_first_last(data)
print(f"First-Last Swap: {result}")

numbers = [3, 1, 4, 1, 5, 9, 2, 6]
min_val, max_val, rest = extract_min_max(numbers)
print(f"Min: {min_val}, Max: {max_val}, Rest: {len(rest)} Elemente")
```

**Erwarteter Output:**
```
Getauscht: a=10, b=5
Rotiert: x=2, y=3, z=1
First-Last Swap: [5, 2, 3, 4, 1]
Min: 1, Max: 9, Rest: 6 Elemente
```

<details>
<summary>💡 Hinweis</summary>

- Swap: `return b, a`
- Rotate: `return b, c, a`
- First-Last: List kopieren und indizes tauschen
- Extract: `min()`, `max()` verwenden, rest mit List Comprehension
</details>

---

### Übung 3: Unveränderlichkeit - Immutable Operations (Mittel)

Arbeite mit Tupeln unter Beachtung der Unveränderlichkeit.

**Anforderungen:**
- `add_element(tpl, element)` - Fügt Element hinzu (neues Tupel)
- `remove_element(tpl, index)` - Entfernt Element an Index
- `replace_element(tpl, index, value)` - Ersetzt Element
- `merge_tuples(*tuples)` - Verbindet mehrere Tupel

**Beispiel:**
```python
def add_element(tpl, element):
    # Dein Code hier
    pass

def remove_element(tpl, index):
    # Dein Code hier
    pass

def replace_element(tpl, index, value):
    # Dein Code hier
    pass

def merge_tuples(*tuples):
    # Dein Code hier
    pass

# Test
original = (1, 2, 3, 4, 5)
print(f"Original: {original}")

added = add_element(original, 6)
print(f"Mit 6: {added}")

removed = remove_element(original, 2)
print(f"Index 2 entfernt: {removed}")

replaced = replace_element(original, 1, 99)
print(f"Index 1 ersetzt: {replaced}")

merged = merge_tuples((1, 2), (3, 4), (5, 6))
print(f"Merged: {merged}")

# Original bleibt unverändert!
print(f"Original unverändert: {original}")
```

**Erwarteter Output:**
```
Original: (1, 2, 3, 4, 5)
Mit 6: (1, 2, 3, 4, 5, 6)
Index 2 entfernt: (1, 2, 4, 5)
Index 1 ersetzt: (1, 99, 3, 4, 5)
Merged: (1, 2, 3, 4, 5, 6)
Original unverändert: (1, 2, 3, 4, 5)
```

<details>
<summary>💡 Hinweis</summary>

- Add: `tpl + (element,)` - Komma wichtig!
- Remove: `tpl[:index] + tpl[index+1:]`
- Replace: `tpl[:index] + (value,) + tpl[index+1:]`
- Merge: Verwende `sum(tuples, ())` oder Konkatenation
</details>

---

### Übung 4: Nested Tuples - Matrix Operations (Mittel)

Arbeite mit verschachtelten Tupeln als unveränderliche Matrizen.

**Anforderungen:**
- `create_matrix(rows, cols, value=0)` - Erstellt Matrix als Tupel
- `get_element(matrix, row, col)` - Holt Element
- `set_element(matrix, row, col, value)` - Setzt Element (neue Matrix!)
- `transpose_matrix(matrix)` - Transponiert Matrix

**Beispiel:**
```python
def create_matrix(rows, cols, value=0):
    # Dein Code hier
    pass

def get_element(matrix, row, col):
    # Dein Code hier
    pass

def set_element(matrix, row, col, value):
    # Dein Code hier
    pass

def transpose_matrix(matrix):
    # Dein Code hier
    pass

# Test
matrix = create_matrix(3, 3, 0)
print("Leere Matrix:")
for row in matrix:
    print(row)

matrix = set_element(matrix, 1, 1, 5)
print(f"\nNach Set (1,1)=5:")
for row in matrix:
    print(row)

print(f"\nElement (1,1): {get_element(matrix, 1, 1)}")

matrix = ((1, 2, 3), (4, 5, 6))
transposed = transpose_matrix(matrix)
print("\nOriginal:")
for row in matrix:
    print(row)
print("Transponiert:")
for row in transposed:
    print(row)
```

**Erwarteter Output:**
```
Leere Matrix:
(0, 0, 0)
(0, 0, 0)
(0, 0, 0)

Nach Set (1,1)=5:
(0, 0, 0)
(0, 5, 0)
(0, 0, 0)

Element (1,1): 5

Original:
(1, 2, 3)
(4, 5, 6)
Transponiert:
(1, 4)
(2, 5)
(3, 6)
```

<details>
<summary>💡 Hinweis</summary>

- Create: `tuple(tuple(value for _ in range(cols)) for _ in range(rows))`
- Get: `matrix[row][col]`
- Set: Neue Zeile erstellen, dann neue Matrix
- Transpose: `tuple(zip(*matrix))`
</details>

---

### Übung 5: Named Tuples - Person Database (Mittel-Schwer)

Erstelle ein einfaches Personen-Datenbanksystem mit Named Tuples.

**Anforderungen:**
- `Person` Named Tuple mit name, age, city
- `add_person()` - Fügt Person hinzu
- `find_by_name()` - Sucht Person nach Name
- `filter_by_city()` - Filtert nach Stadt
- `oldest_person()` - Findet älteste Person
- `average_age()` - Berechnet Durchschnittsalter

**Beispiel:**
```python
from typing import NamedTuple, List, Optional

class Person(NamedTuple):
    # Dein Code hier
    pass

def add_person(database: List[Person], name: str, age: int, city: str) -> List[Person]:
    # Dein Code hier
    pass

def find_by_name(database: List[Person], name: str) -> Optional[Person]:
    # Dein Code hier
    pass

def filter_by_city(database: List[Person], city: str) -> List[Person]:
    # Dein Code hier
    pass

def oldest_person(database: List[Person]) -> Optional[Person]:
    # Dein Code hier
    pass

def average_age(database: List[Person]) -> float:
    # Dein Code hier
    pass

# Test
db = []
db = add_person(db, "Anna", 25, "Berlin")
db = add_person(db, "Bob", 30, "München")
db = add_person(db, "Charlie", 25, "Berlin")
db = add_person(db, "David", 35, "Hamburg")

print(f"Anzahl Personen: {len(db)}")

person = find_by_name(db, "Bob")
print(f"Gefunden: {person}")

berlin_people = filter_by_city(db, "Berlin")
print(f"In Berlin: {[p.name for p in berlin_people]}")

oldest = oldest_person(db)
print(f"Älteste Person: {oldest.name} ({oldest.age})")

avg = average_age(db)
print(f"Durchschnittsalter: {avg:.1f}")
```

**Erwarteter Output:**
```
Anzahl Personen: 4
Gefunden: Person(name='Bob', age=30, city='München')
In Berlin: ['Anna', 'Charlie']
Älteste Person: David (35)
Durchschnittsalter: 28.8
```

<details>
<summary>💡 Hinweis</summary>

- Named Tuple: `class Person(NamedTuple): name: str; age: int; city: str`
- Add: Liste ist veränderlich, einfach append
- Find: `next((p for p in db if p.name == name), None)`
- Filter: List Comprehension
- Oldest: `max(db, key=lambda p: p.age)`
- Average: `sum(p.age for p in db) / len(db)`
</details>

---

### Übung 6: Named Tuples mit Methoden - Geometry (Schwer)

Erstelle geometrische Formen mit Named Tuples und Methoden.

**Anforderungen:**
- `Point` mit distance_to() Methode
- `Circle` mit area(), circumference(), contains() Methoden
- `Rectangle` mit area(), perimeter(), contains() Methoden
- Alle Formen sollen prüfen können ob Punkt enthalten ist

**Beispiel:**
```python
from typing import NamedTuple
import math

class Point(NamedTuple):
    x: float
    y: float
    
    def distance_to(self, other: 'Point') -> float:
        # Dein Code hier
        pass

class Circle(NamedTuple):
    center: Point
    radius: float
    
    def area(self) -> float:
        # Dein Code hier
        pass
    
    def circumference(self) -> float:
        # Dein Code hier
        pass
    
    def contains(self, point: Point) -> bool:
        # Dein Code hier
        pass

class Rectangle(NamedTuple):
    bottom_left: Point
    width: float
    height: float
    
    def area(self) -> float:
        # Dein Code hier
        pass
    
    def perimeter(self) -> float:
        # Dein Code hier
        pass
    
    def contains(self, point: Point) -> bool:
        # Dein Code hier
        pass

# Test
p1 = Point(0, 0)
p2 = Point(3, 4)
print(f"Distanz: {p1.distance_to(p2):.2f}")

circle = Circle(center=Point(0, 0), radius=5)
print(f"Kreis Fläche: {circle.area():.2f}")
print(f"Kreis enthält (3,4)? {circle.contains(p2)}")
print(f"Kreis enthält (10,10)? {circle.contains(Point(10, 10))}")

rect = Rectangle(bottom_left=Point(0, 0), width=10, height=5)
print(f"Rechteck Fläche: {rect.area():.2f}")
print(f"Rechteck Umfang: {rect.perimeter():.2f}")
print(f"Rechteck enthält (5,2)? {rect.contains(Point(5, 2))}")
print(f"Rechteck enthält (15,2)? {rect.contains(Point(15, 2))}")
```

**Erwarteter Output:**
```
Distanz: 5.00
Kreis Fläche: 78.54
Kreis enthält (3,4)? True
Kreis enthält (10,10)? False
Rechteck Fläche: 50.00
Rechteck Umfang: 30.00
Rechteck enthält (5,2)? True
Rechteck enthält (15,2)? False
```

<details>
<summary>💡 Hinweis</summary>

- Distance: `math.sqrt((x2-x1)**2 + (y2-y1)**2)`
- Circle area: `math.pi * radius**2`
- Circle contains: `distance_to(center) <= radius`
- Rectangle area: `width * height`
- Rectangle contains: Prüfe x und y Grenzen
</details>

---

### Übung 7: Extended Unpacking - Data Processing (Schwer)

Verwende Extended Unpacking für Datenverarbeitung.

**Anforderungen:**
- `process_scores(name, *scores)` - Erster Wert ist Name, Rest sind Scores
- `split_header_data(lines)` - Trennt erste Zeile von Rest
- `extract_first_last(*values)` - Gibt (first, last, middle_count) zurück
- `group_by_first(*items)` - Gruppiert Items nach erstem Buchstaben

**Beispiel:**
```python
def process_scores(name, *scores):
    """Berechnet Statistiken für Scores"""
    # Dein Code hier
    pass

def split_header_data(lines):
    """Trennt Header von Daten"""
    # Dein Code hier
    pass

def extract_first_last(*values):
    """Extrahiert erstes, letztes und Anzahl mittlerer Werte"""
    # Dein Code hier
    pass

def group_by_first(*items):
    """Gruppiert Items nach erstem Buchstaben"""
    # Dein Code hier
    pass

# Test
result = process_scores("Anna", 85, 90, 78, 92, 88)
print(f"Ergebnis: {result}")

csv_data = [
    "Name,Age,City",
    "Anna,25,Berlin",
    "Bob,30,München"
]
header, data = split_header_data(csv_data)
print(f"Header: {header}")
print(f"Daten: {data}")

first, last, count = extract_first_last(1, 2, 3, 4, 5)
print(f"Erstes: {first}, Letztes: {last}, Mittlere: {count}")

groups = group_by_first("Apple", "Banana", "Apricot", "Berry", "Cherry")
print(f"Gruppiert: {groups}")
```

**Erwarteter Output:**
```
Ergebnis: {'name': 'Anna', 'min': 78, 'max': 92, 'avg': 86.6}
Header: Name,Age,City
Daten: ['Anna,25,Berlin', 'Bob,30,München']
Erstes: 1, Letztes: 5, Mittlere: 3
Gruppiert: {'A': ['Apple', 'Apricot'], 'B': ['Banana', 'Berry'], 'C': ['Cherry']}
```

<details>
<summary>💡 Hinweis</summary>

- Process: `*scores` sammelt alle Argumente nach name
- Split: `header, *data = lines`
- Extract: `first, *middle, last = values; return (first, last, len(middle))`
- Group: Dictionary mit ersten Buchstaben als Keys
</details>

---

### Übung 8: Tuple als Dictionary Key - Caching (Schwer)

Implementiere einen Funktions-Cache mit Tupeln als Keys.

**Anforderungen:**
- `@cache_result` Decorator der Funktionsergebnisse cached
- Cache-Key soll aus allen Argumenten bestehen
- Zeige Cache-Statistiken (Hits/Misses)
- Fibonacci und Fakultät als Beispiele

**Beispiel:**
```python
def cache_result(func):
    """Decorator für Function Caching"""
    cache = {}
    stats = {"hits": 0, "misses": 0}
    
    def wrapper(*args):
        # Dein Code hier
        pass
    
    def get_stats():
        return stats.copy()
    
    wrapper.get_stats = get_stats
    wrapper.clear_cache = lambda: cache.clear()
    
    return wrapper

@cache_result
def fibonacci(n):
    if n <= 1:
        return n
    return fibonacci(n-1) + fibonacci(n-2)

@cache_result
def factorial(n):
    if n <= 1:
        return 1
    return n * factorial(n-1)

# Test
print(f"Fibonacci(10): {fibonacci(10)}")
print(f"Stats: {fibonacci.get_stats()}")

print(f"Fibonacci(10) nochmal: {fibonacci(10)}")
print(f"Stats: {fibonacci.get_stats()}")

print(f"\nFactorial(5): {factorial(5)}")
print(f"Stats: {factorial.get_stats()}")

fibonacci.clear_cache()
print(f"\nNach Clear:")
print(f"Fibonacci(5): {fibonacci(5)}")
print(f"Stats: {fibonacci.get_stats()}")
```

**Erwarteter Output:**
```
Fibonacci(10): 55
Stats: {'hits': 8, 'misses': 11}
Fibonacci(10) nochmal: 55
Stats: {'hits': 9, 'misses': 11}

Factorial(5): 120
Stats: {'hits': 4, 'misses': 5}

Nach Clear:
Fibonacci(5): 5
Stats: {'hits': 3, 'misses': 6}
```

<details>
<summary>💡 Hinweis</summary>

- Cache-Key: `args` ist bereits ein Tupel!
- Check: `if args in cache: stats["hits"] += 1; return cache[args]`
- Sonst: `stats["misses"] += 1; result = func(*args); cache[args] = result`
- Tupel sind hashbar und können als Dict-Keys verwendet werden
</details>

---

### Übung 9: Complex Data Structures - Event System (Schwer)

Erstelle ein Event-System mit Named Tuples für Events.

**Anforderungen:**
- `Event` Named Tuple mit type, timestamp, data
- `EventLog` zum Speichern von Events
- Methoden: add_event, get_events_by_type, get_recent_events
- Statistiken: event_count_by_type, time_range

**Beispiel:**
```python
from typing import NamedTuple, List, Dict, Optional
from datetime import datetime, timedelta

class Event(NamedTuple):
    type: str
    timestamp: datetime
    data: Dict
    
    def age_seconds(self) -> float:
        """Alter des Events in Sekunden"""
        # Dein Code hier
        pass

class EventLog:
    def __init__(self):
        # Dein Code hier
        pass
    
    def add_event(self, event_type: str, data: Dict) -> Event:
        # Dein Code hier
        pass
    
    def get_events_by_type(self, event_type: str) -> List[Event]:
        # Dein Code hier
        pass
    
    def get_recent_events(self, seconds: int) -> List[Event]:
        # Dein Code hier
        pass
    
    def event_count_by_type(self) -> Dict[str, int]:
        # Dein Code hier
        pass

# Test
log = EventLog()

# Events hinzufügen
log.add_event("login", {"user": "anna"})
log.add_event("purchase", {"item": "laptop", "price": 999})
log.add_event("login", {"user": "bob"})
log.add_event("logout", {"user": "anna"})

# Abfragen
logins = log.get_events_by_type("login")
print(f"Login Events: {len(logins)}")
for event in logins:
    print(f"  {event.data['user']} at {event.timestamp}")

stats = log.event_count_by_type()
print(f"\nEvent Statistik: {stats}")

# Simuliere Verzögerung
import time
time.sleep(1)
log.add_event("error", {"message": "Test"})

recent = log.get_recent_events(seconds=2)
print(f"\nRecent Events (2s): {len(recent)}")
```

**Erwarteter Output:** (Timestamps variieren)
```
Login Events: 2
  anna at 2025-10-01 14:30:15.123456
  bob at 2025-10-01 14:30:15.234567

Event Statistik: {'login': 2, 'purchase': 1, 'logout': 1, 'error': 1}

Recent Events (2s): 5
```

<details>
<summary>💡 Hinweis</summary>

- Event age: `(datetime.now() - self.timestamp).total_seconds()`
- EventLog: Liste von Events speichern
- Add: Erstelle Event mit `datetime.now()`, füge zu Liste hinzu
- Get by type: Filter mit List Comprehension
- Recent: Filter nach `event.age_seconds() < seconds`
- Count: Verwende `Counter` oder Dictionary
</details>

---

## 🎯 Bonus-Challenge: Immutable Graph mit Tupeln

**Aufgabe:** Implementiere einen unveränderlichen gerichteten Graphen mit Tupeln.

**Anforderungen:**
- Knoten und Kanten als Tupel
- `add_edge(graph, from_node, to_node)` - Fügt Kante hinzu (neuer Graph)
- `get_neighbors(graph, node)` - Gibt Nachbarn zurück
- `has_path(graph, start, end)` - Prüft ob Pfad existiert
- `shortest_path(graph, start, end)` - Findet kürzesten Pfad

```python
from typing import NamedTuple, Set, List, Optional, Tuple

# Graph = Tupel von (from, to) Kanten
Graph = Tuple[Tuple[str, str], ...]

def add_edge(graph: Graph, from_node: str, to_node: str) -> Graph:
    """Fügt Kante hinzu"""
    # Dein Code hier
    pass

def get_neighbors(graph: Graph, node: str) -> Set[str]:
    """Gibt alle Nachbarn eines Knotens zurück"""
    # Dein Code hier
    pass

def has_path(graph: Graph, start: str, end: str) -> bool:
    """Prüft ob Pfad von start zu end existiert (DFS/BFS)"""
    # Dein Code hier
    pass

def shortest_path(graph: Graph, start: str, end: str) -> Optional[List[str]]:
    """Findet kürzesten Pfad (BFS)"""
    # Dein Code hier
    pass

# Test
graph = ()  # Leerer Graph
graph = add_edge(graph, "A", "B")
graph = add_edge(graph, "B", "C")
graph = add_edge(graph, "A", "C")
graph = add_edge(graph, "C", "D")

print(f"Nachbarn von A: {get_neighbors(graph, 'A')}")
print(f"Pfad A→D existiert? {has_path(graph, 'A', 'D')}")
print(f"Kürzester Pfad A→D: {shortest_path(graph, 'A', 'D')}")
```

---

## 📊 Lösungshinweise

> [!note] Selbsttest
> Versuche alle Übungen selbst zu lösen, bevor du die Lösungen ansiehst. Die Lösungen findest du in [[06_Praxis/04_Lösungen/Lösungen Teil 2|Lösungen Teil 2]].

**Bewertung deiner Lösungen:**
- ✅ Tupel bleiben unveränderlich (neue Tupel bei Änderungen)
- ✅ Tuple Unpacking wo möglich verwendet
- ✅ Named Tuples für strukturierte Daten
- ✅ Type Hints bei Named Tuples
- ✅ Methoden bei Named Tuples wo sinnvoll

---

## 🔗 Weiterführende Themen

Nach diesen Übungen solltest du bereit sein für:
- [[02_Sequenzen/03_Slicing/01_Slicing Grundlagen|Slicing Grundlagen]] - Erweiterte Slicing-Techniken
- [[02_Sequenzen/04_Comprehensions/01_List Comprehensions|List Comprehensions]] - Elegante Datenverarbeitung
- [[04_Fortgeschritten/04_Dataclasses/01_Dataclass Basics|Dataclasses]] - Veränderliche Alternative zu Named Tuples

---

## 📚 Python-Dokumentation

**Offizielle Ressourcen:**
- [Tuple Tutorial](https://docs.python.org/3/tutorial/datastructures.html#tuples-and-sequences) - Komplettes Tutorial
- [collections.namedtuple](https://docs.python.org/3/library/collections.html#collections.namedtuple) - Named Tuples Klassisch
- [typing.NamedTuple](https://docs.python.org/3/library/typing.html#typing.NamedTuple) - Named Tuples Modern
- [Unpacking](https://docs.python.org/3/tutorial/datastructures.html#tuples-and-sequences) - Unpacking Syntax

---

← [[02_Sequenzen/02_Tupel/03_Named Tuples|Named Tuples]] | [[INDEX|📑 Index]] | [[02_Sequenzen/03_Slicing/01_Slicing Grundlagen|Slicing Grundlagen]] →