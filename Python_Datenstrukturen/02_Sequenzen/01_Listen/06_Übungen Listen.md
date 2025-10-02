---
tags: [python, datenstrukturen, list, übungen]
created: 2025-10-01
---

← [[02_Sequenzen/01_Listen/05_List Performance|List Performance]] | [[INDEX|📑 Index]] | [[02_Sequenzen/02_Tupel/01_Tuple Basics|Tuple Basics]] →

# Übungen: Listen

> [!tip] Lernziel
> Diese Übungen festigen dein Verständnis von Listen, ihren Methoden, Performance-Charakteristiken und praktischen Anwendungen. Die Übungen decken alle Aspekte ab: Basics, Manipulation, verschachtelte Listen und Performance.

## 📝 Übungsaufgaben

### Übung 1: List Basics - Einkaufsliste (Leicht)

Erstelle ein einfaches Einkaufslisten-Programm mit folgenden Funktionen:

**Anforderungen:**
- `add_item(liste, item)` - Fügt Artikel hinzu
- `remove_item(liste, item)` - Entfernt ersten Artikel
- `has_item(liste, item)` - Prüft ob Artikel vorhanden
- `count_items(liste)` - Gibt Anzahl zurück
- `clear_list(liste)` - Leert Liste

**Beispiel:**
```python
def add_item(liste, item):
    # Dein Code hier
    pass

def remove_item(liste, item):
    # Dein Code hier
    pass

def has_item(liste, item):
    # Dein Code hier
    pass

def count_items(liste):
    # Dein Code hier
    pass

def clear_list(liste):
    # Dein Code hier
    pass

# Test
shopping = []
add_item(shopping, "Milch")
add_item(shopping, "Brot")
add_item(shopping, "Eier")
print(shopping)
print(f"Anzahl: {count_items(shopping)}")
print(f"Hat Milch: {has_item(shopping, 'Milch')}")
remove_item(shopping, "Brot")
print(shopping)
```

**Erwarteter Output:**
```
['Milch', 'Brot', 'Eier']
Anzahl: 3
Hat Milch: True
['Milch', 'Eier']
```

<details>
<summary>💡 Hinweis</summary>

- Verwende `append()` zum Hinzufügen
- Verwende `remove()` zum Entfernen (mit Fehlerbehandlung!)
- Verwende `in` Operator für Membership Test
- Verwende `len()` für Anzahl
- Verwende `clear()` zum Leeren
</details>

---

### Übung 2: List Manipulation - Rotation (Leicht-Mittel)

Implementiere Funktionen zum Rotieren einer Liste.

**Anforderungen:**
- `rotate_right(liste, n)` - Rotiert n Positionen nach rechts
- `rotate_left(liste, n)` - Rotiert n Positionen nach links
- Funktioniert mit n > len(liste)
- Modifiziert Liste in-place

**Beispiel:**
```python
def rotate_right(liste, n):
    # Dein Code hier
    pass

def rotate_left(liste, n):
    # Dein Code hier
    pass

# Test
numbers = [1, 2, 3, 4, 5]
rotate_right(numbers, 2)
print(numbers)  # [4, 5, 1, 2, 3]

numbers = [1, 2, 3, 4, 5]
rotate_left(numbers, 2)
print(numbers)  # [3, 4, 5, 1, 2]

numbers = [1, 2, 3]
rotate_right(numbers, 5)  # 5 % 3 = 2
print(numbers)  # [2, 3, 1]
```

**Erwarteter Output:**
```
[4, 5, 1, 2, 3]
[3, 4, 5, 1, 2]
[2, 3, 1]
```

<details>
<summary>💡 Hinweis</summary>

- Verwende Slicing: `liste[:] = liste[-n:] + liste[:-n]`
- Nutze Modulo für große n: `n = n % len(liste)`
- Rechts-Rotation: Nimm letzte n und setze sie vorne
- Links-Rotation: Nimm erste n und setze sie hinten
</details>

---

### Übung 3: List Sorting - Custom Sortierung (Mittel)

Sortiere eine Liste von Dictionaries nach verschiedenen Kriterien.

**Anforderungen:**
- Sortiere Personen nach Name (alphabetisch)
- Sortiere nach Alter (absteigend)
- Sortiere nach Stadt, dann nach Alter
- Finde älteste und jüngste Person

**Beispiel:**
```python
def sort_by_name(people):
    # Dein Code hier
    pass

def sort_by_age_desc(people):
    # Dein Code hier
    pass

def sort_by_city_and_age(people):
    # Dein Code hier
    pass

def find_oldest(people):
    # Dein Code hier
    pass

def find_youngest(people):
    # Dein Code hier
    pass

# Test
people = [
    {"name": "Anna", "age": 25, "city": "Berlin"},
    {"name": "Bob", "age": 30, "city": "München"},
    {"name": "Charlie", "age": 25, "city": "Berlin"},
    {"name": "David", "age": 35, "city": "Hamburg"}
]

print("Nach Name:")
sort_by_name(people)
print([p["name"] for p in people])

print("\nNach Alter (absteigend):")
sort_by_age_desc(people)
print([(p["name"], p["age"]) for p in people])

print(f"\nÄlteste: {find_oldest(people)['name']}")
print(f"Jüngste: {find_youngest(people)['name']}")
```

**Erwarteter Output:**
```
Nach Name:
['Anna', 'Bob', 'Charlie', 'David']

Nach Alter (absteigend):
[('David', 35), ('Bob', 30), ('Anna', 25), ('Charlie', 25)]

Älteste: David
Jüngste: Anna
```

<details>
<summary>💡 Hinweis</summary>

- Verwende `list.sort(key=lambda x: x["field"])`
- Für mehrere Kriterien: `key=lambda x: (x["city"], x["age"])`
- Verwende `reverse=True` für absteigende Sortierung
- Nutze `max()` und `min()` mit Key-Funktion
</details>

---

### Übung 4: Matrix Operations (Mittel)

Implementiere grundlegende Matrix-Operationen.

**Anforderungen:**
- `create_matrix(rows, cols, value=0)` - Erstellt Matrix
- `transpose(matrix)` - Transponiert Matrix
- `get_diagonal(matrix)` - Gibt Hauptdiagonale zurück
- `sum_matrix(matrix)` - Summiert alle Elemente

**Beispiel:**
```python
def create_matrix(rows, cols, value=0):
    # Dein Code hier
    pass

def transpose(matrix):
    # Dein Code hier
    pass

def get_diagonal(matrix):
    # Dein Code hier
    pass

def sum_matrix(matrix):
    # Dein Code hier
    pass

# Test
matrix = create_matrix(3, 3, 0)
print("Leere Matrix:")
for row in matrix:
    print(row)

matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]

print("\nTransponiert:")
trans = transpose(matrix)
for row in trans:
    print(row)

print(f"\nDiagonale: {get_diagonal(matrix)}")
print(f"Summe: {sum_matrix(matrix)}")
```

**Erwarteter Output:**
```
Leere Matrix:
[0, 0, 0]
[0, 0, 0]
[0, 0, 0]

Transponiert:
[1, 4, 7]
[2, 5, 8]
[3, 6, 9]

Diagonale: [1, 5, 9]
Summe: 45
```

<details>
<summary>💡 Hinweis</summary>

- Matrix erstellen: `[[value] * cols for _ in range(rows)]`
- Transponieren: `list(zip(*matrix))` oder List Comprehension
- Diagonale: `[matrix[i][i] for i in range(len(matrix))]`
- Summe: `sum(sum(row) for row in matrix)`
</details>

---

### Übung 5: List Performance - Deduplizierung (Mittel-Schwer)

Implementiere und vergleiche verschiedene Methoden zur Entfernung von Duplikaten.

**Anforderungen:**
- `remove_duplicates_set()` - Mit Set (schnell, Reihenfolge nicht garantiert)
- `remove_duplicates_dict()` - Mit Dict (schnell, Reihenfolge erhalten)
- `remove_duplicates_loop()` - Mit Loop (langsam, Reihenfolge erhalten)
- Vergleiche Performance mit `time` Module

**Beispiel:**
```python
import time

def remove_duplicates_set(liste):
    # Dein Code hier
    pass

def remove_duplicates_dict(liste):
    # Dein Code hier
    pass

def remove_duplicates_loop(liste):
    # Dein Code hier
    pass

def benchmark_methods(data):
    # Dein Code hier: Miss Zeit für jede Methode
    pass

# Test
numbers = [1, 2, 2, 3, 3, 3, 4, 4, 4, 4, 5]

print("Set-Methode:")
print(remove_duplicates_set(numbers))

print("\nDict-Methode:")
print(remove_duplicates_dict(numbers))

print("\nLoop-Methode:")
print(remove_duplicates_loop(numbers))

# Performance-Test
large_data = [i % 100 for i in range(10000)]
print("\nPerformance-Vergleich:")
benchmark_methods(large_data)
```

**Erwarteter Output:**
```
Set-Methode:
[1, 2, 3, 4, 5]  # Reihenfolge kann variieren

Dict-Methode:
[1, 2, 3, 4, 5]  # Reihenfolge erhalten

Loop-Methode:
[1, 2, 3, 4, 5]  # Reihenfolge erhalten

Performance-Vergleich:
Set:  0.0012s
Dict: 0.0015s
Loop: 0.0234s (15.6x langsamer)
```

<details>
<summary>💡 Hinweis</summary>

- Set: `list(set(liste))`
- Dict (Python 3.7+): `list(dict.fromkeys(liste))`
- Loop: Iteriere und füge nur neue Elemente hinzu
- Nutze `time.perf_counter()` für Zeitmessung
</details>

---

### Übung 6: Nested Lists - Flattening (Schwer)

Implementiere verschiedene Flatten-Methoden für verschachtelte Listen.

**Anforderungen:**
- `flatten_one_level()` - Eine Ebene auflösen
- `flatten_recursive()` - Beliebig tiefe Verschachtelung
- `flatten_iterative()` - Iterativ mit Stack
- Behandle verschiedene Datentypen korrekt

**Beispiel:**
```python
def flatten_one_level(nested):
    # Dein Code hier
    pass

def flatten_recursive(nested):
    # Dein Code hier
    pass

def flatten_iterative(nested):
    # Dein Code hier
    pass

# Test
nested_1 = [[1, 2], [3, 4], [5, 6]]
print("Eine Ebene:")
print(flatten_one_level(nested_1))

nested_2 = [1, [2, [3, [4, 5]], 6], 7, [8, 9]]
print("\nRekursiv:")
print(flatten_recursive(nested_2))

print("\nIterativ:")
print(flatten_iterative(nested_2))

# Mit verschiedenen Typen
mixed = [1, "text", [2, "more", [3, "deep"]], 4]
print("\nGemischt:")
print(flatten_recursive(mixed))
```

**Erwarteter Output:**
```
Eine Ebene:
[1, 2, 3, 4, 5, 6]

Rekursiv:
[1, 2, 3, 4, 5, 6, 7, 8, 9]

Iterativ:
[1, 2, 3, 4, 5, 6, 7, 8, 9]

Gemischt:
[1, 'text', 2, 'more', 3, 'deep', 4]
```

<details>
<summary>💡 Hinweis</summary>

- Eine Ebene: List Comprehension `[item for sublist in nested for item in sublist]`
- Rekursiv: Prüfe mit `isinstance(item, list)` und rufe rekursiv auf
- Iterativ: Verwende Stack (Liste) und `pop()` / `extend()`
- Achte darauf, dass Strings nicht als Listen behandelt werden!
</details>

---

### Übung 7: List Algorithms - Sliding Window (Schwer)

Implementiere Sliding Window Algorithmen.

**Anforderungen:**
- `max_sum_subarray(arr, k)` - Maximale Summe von k aufeinanderfolgenden Elementen
- `min_window_substring(s, t)` - Kleinstes Fenster das alle Zeichen enthält
- `find_anagrams(s, p)` - Alle Start-Indizes von Anagrammen

**Beispiel:**
```python
def max_sum_subarray(arr, k):
    """Findet maximale Summe von k aufeinanderfolgenden Elementen"""
    # Dein Code hier
    pass

def moving_average(arr, k):
    """Berechnet gleitenden Durchschnitt mit Fenster k"""
    # Dein Code hier
    pass

# Test
numbers = [1, 3, 2, 6, -1, 4, 1, 8, 2]
k = 3

max_sum = max_sum_subarray(numbers, k)
print(f"Maximale Summe ({k} Elemente): {max_sum}")

averages = moving_average(numbers, k)
print(f"Gleitender Durchschnitt: {averages}")

# Zusatz: Finde das Fenster selbst
def find_max_window(arr, k):
    """Gibt das Fenster mit maximaler Summe zurück"""
    # Dein Code hier
    pass

window = find_max_window(numbers, k)
print(f"Fenster mit max Summe: {window}")
```

**Erwarteter Output:**
```
Maximale Summe (3 Elemente): 13
Gleitender Durchschnitt: [2.0, 3.67, 2.33, 3.0, 1.33, 4.33, 3.67]
Fenster mit max Summe: [4, 1, 8]
```

<details>
<summary>💡 Hinweis</summary>

- Sliding Window Pattern: Schiebe Fenster um eins, aktualisiere Summe
- Erste Summe: `sum(arr[:k])`
- Dann: `current_sum = current_sum - arr[i-k] + arr[i]`
- Tracke Maximum und Index während des Schiebens
- Zeitkomplexität sollte O(n) sein, nicht O(n*k)!
</details>

---

### Übung 8: Custom Data Structure - Circular Buffer (Schwer)

Implementiere einen zirkulären Buffer (Ring-Buffer) mit Listen.

**Anforderungen:**
- Feste Größe
- `write(item)` - Schreibt Element (überschreibt ältestes bei Überlauf)
- `read()` - Liest ältestes Element
- `peek(n)` - Zeigt n neueste Elemente
- `is_full()`, `is_empty()`, `size()`

**Beispiel:**
```python
class CircularBuffer:
    def __init__(self, capacity):
        # Dein Code hier
        pass
    
    def write(self, item):
        """Schreibt Element in Buffer"""
        # Dein Code hier
        pass
    
    def read(self):
        """Liest und entfernt ältestes Element"""
        # Dein Code hier
        pass
    
    def peek(self, n=1):
        """Zeigt n neueste Elemente ohne zu entfernen"""
        # Dein Code hier
        pass
    
    def is_full(self):
        # Dein Code hier
        pass
    
    def is_empty(self):
        # Dein Code hier
        pass
    
    def size(self):
        # Dein Code hier
        pass

# Test
buffer = CircularBuffer(5)

# Schreibe Elemente
for i in range(3):
    buffer.write(i)
    print(f"Geschrieben: {i}, Größe: {buffer.size()}")

print(f"Peek(2): {buffer.peek(2)}")

# Lese Elemente
print(f"Gelesen: {buffer.read()}")
print(f"Größe nach read: {buffer.size()}")

# Fülle Buffer komplett
for i in range(10, 15):
    buffer.write(i)

print(f"Voll? {buffer.is_full()}")
print(f"Peek(3): {buffer.peek(3)}")

# Überlauf testen
buffer.write(99)  # Überschreibt ältestes
print(f"Nach Überlauf, Peek(5): {buffer.peek(5)}")
```

**Erwarteter Output:**
```
Geschrieben: 0, Größe: 1
Geschrieben: 1, Größe: 2
Geschrieben: 2, Größe: 3
Peek(2): [2, 1]
Gelesen: 0
Größe nach read: 2
Voll? True
Peek(3): [14, 13, 12]
Nach Überlauf, Peek(5): [99, 14, 13, 12, 11]
```

<details>
<summary>💡 Hinweis</summary>

- Verwende interne Liste mit fester Größe
- Tracke `read_pos` und `write_pos` Indizes
- Verwende Modulo für Wrapping: `pos % capacity`
- Tracke Anzahl der Elemente separat
- Bei Überlauf: Überschreibe ältestes Element
</details>

---

### Übung 9: List Comprehension Challenge (Schwer)

Löse komplexe Probleme mit List Comprehensions.

**Anforderungen:**
- Erzeuge alle Primzahlen bis n
- Erstelle Multiplikationstabelle
- Finde alle Paare mit gegebener Summe
- Generiere Pascal's Triangle
- Transpose einer Matrix

**Beispiel:**
```python
def primes_up_to(n):
    """Alle Primzahlen bis n mit List Comprehension"""
    # Dein Code hier (Sieve of Eratosthenes)
    pass

def multiplication_table(n):
    """n×n Multiplikationstabelle"""
    # Dein Code hier
    pass

def pairs_with_sum(arr, target):
    """Alle Paare die target ergeben"""
    # Dein Code hier
    pass

def pascals_triangle(n):
    """Erste n Zeilen von Pascal's Triangle"""
    # Dein Code hier
    pass

# Test
print("Primzahlen bis 30:")
print(primes_up_to(30))

print("\n5×5 Multiplikationstabelle:")
table = multiplication_table(5)
for row in table:
    print(row)

print("\nPaare mit Summe 10:")
print(pairs_with_sum([1, 2, 3, 4, 5, 6, 7, 8, 9], 10))

print("\nPascal's Triangle (6 Zeilen):")
triangle = pascals_triangle(6)
for row in triangle:
    print(row)
```

**Erwarteter Output:**
```
Primzahlen bis 30:
[2, 3, 5, 7, 11, 13, 17, 19, 23, 29]

5×5 Multiplikationstabelle:
[1, 2, 3, 4, 5]
[2, 4, 6, 8, 10]
[3, 6, 9, 12, 15]
[4, 8, 12, 16, 20]
[5, 10, 15, 20, 25]

Paare mit Summe 10:
[(1, 9), (2, 8), (3, 7), (4, 6)]

Pascal's Triangle (6 Zeilen):
[1]
[1, 1]
[1, 2, 1]
[1, 3, 3, 1]
[1, 4, 6, 4, 1]
[1, 5, 10, 10, 5, 1]
```

<details>
<summary>💡 Hinweis</summary>

- Primzahlen: `[x for x in range(2, n+1) if all(x % d != 0 for d in range(2, int(x**0.5)+1))]`
- Multiplikation: `[[i*j for j in range(1, n+1)] for i in range(1, n+1)]`
- Paare: Verwende verschachtelte Comprehension mit Bedingung
- Pascal: Jede Zeile basiert auf vorheriger Zeile
</details>

---

## 🎯 Bonus-Challenge: Sortier-Algorithmen

**Aufgabe:** Implementiere verschiedene Sortier-Algorithmen und vergleiche ihre Performance.

**Anforderungen:**
- Bubble Sort
- Selection Sort
- Insertion Sort
- Merge Sort
- Quick Sort
- Vergleiche mit eingebautem `sort()`

```python
def bubble_sort(arr):
    """Bubble Sort - O(n²)"""
    # Dein Code hier
    pass

def selection_sort(arr):
    """Selection Sort - O(n²)"""
    # Dein Code hier
    pass

def insertion_sort(arr):
    """Insertion Sort - O(n²)"""
    # Dein Code hier
    pass

def merge_sort(arr):
    """Merge Sort - O(n log n)"""
    # Dein Code hier
    pass

def quick_sort(arr):
    """Quick Sort - O(n log n) average"""
    # Dein Code hier
    pass

def benchmark_sorts(size=1000):
    """Vergleicht alle Sortier-Algorithmen"""
    import time
    import random
    
    data = [random.randint(0, 1000) for _ in range(size)]
    
    algorithms = {
        "Bubble Sort": bubble_sort,
        "Selection Sort": selection_sort,
        "Insertion Sort": insertion_sort,
        "Merge Sort": merge_sort,
        "Quick Sort": quick_sort,
        "Built-in sort()": lambda x: sorted(x)
    }
    
    for name, func in algorithms.items():
        test_data = data.copy()
        start = time.perf_counter()
        func(test_data)
        elapsed = time.perf_counter() - start
        print(f"{name:20} {elapsed:.4f}s")

# Test
test = [64, 34, 25, 12, 22, 11, 90]
print("Original:", test)
print("Sortiert:", bubble_sort(test.copy()))

print("\nPerformance-Vergleich (n=1000):")
benchmark_sorts(1000)
```

---

## 📊 Lösungshinweise

> [!note] Selbsttest
> Versuche alle Übungen selbst zu lösen, bevor du die Lösungen ansiehst. Die Lösungen findest du in [[06_Praxis/04_Lösungen/Lösungen Teil 2|Lösungen Teil 2]].

**Bewertung deiner Lösungen:**
- ✅ Funktioniert mit Test-Daten
- ✅ Behandelt Edge-Cases (leere Liste, einzelnes Element, etc.)
- ✅ Effizient (nutzt richtige Operationen)
- ✅ Pythonisch (List Comprehensions wo sinnvoll)
- ✅ Gute Fehlerbehandlung

---

## 🔗 Weiterführende Themen

Nach diesen Übungen solltest du bereit sein für:
- [[02_Sequenzen/02_Tupel/01_Tuple Basics|Tupel]] - Unveränderliche Sequenzen
- [[02_Sequenzen/03_Slicing/01_Slicing Grundlagen|Slicing]] - Fortgeschrittene Techniken
- [[02_Sequenzen/04_Comprehensions/01_List Comprehensions|List Comprehensions]] - Detaillierte Behandlung

---

## 📚 Python-Dokumentation

**Offizielle Ressourcen:**
- [List Tutorial](https://docs.python.org/3/tutorial/datastructures.html) - Komplettes Tutorial
- [List Methods](https://docs.python.org/3/library/stdtypes.html#mutable-sequence-types) - Alle Methoden
- [Sorting HOW TO](https://docs.python.org/3/howto/sorting.html) - Sortier-Guide
- [Time Complexity](https://wiki.python.org/moin/TimeComplexity) - Performance-Referenz

---

← [[02_Sequenzen/01_Listen/05_List Performance|List Performance]] | [[INDEX|📑 Index]] | [[02_Sequenzen/02_Tupel/01_Tuple Basics|Tuple Basics]] →