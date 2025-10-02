---
tags: [python, datenstrukturen, list, sequenzen]
created: 2025-10-01
---

← [[01_Grundlagen/05_Bytes/03_Übungen Bytes|Übungen Bytes]] | [[INDEX|📑 Index]] | [[02_Sequenzen/01_Listen/02_List Methoden|List Methoden]] →

# List Basics - Die vielseitigste Datenstruktur

> [!tip] Lernziel
> Nach dieser Seite verstehst du, wie Listen in Python funktionieren, wie sie im Speicher organisiert sind und wann du sie verwenden solltest. Listen sind die am häufigsten verwendete Datenstruktur in Python.

## Was sind Listen?

**Listen** sind **geordnete**, **veränderbare** (mutable) Sequenzen, die beliebige Python-Objekte speichern können. Sie sind extrem vielseitig und die Go-To-Datenstruktur für die meisten Anwendungsfälle.

**Hauptmerkmale:**
- ✅ **Geordnet** - Elemente behalten ihre Position
- ✅ **Mutable** - Können nach Erstellung verändert werden
- ✅ **Heterogen** - Können verschiedene Datentypen mischen
- ✅ **Dynamisch** - Größe wächst/schrumpft automatisch
- ✅ **Indexierbar** - Zugriff über Position (0-basiert)
- ✅ **Iterierbar** - Können in Schleifen durchlaufen werden

```python
# Eine Liste ist vielseitig einsetzbar
zahlen = [1, 2, 3, 4, 5]
gemischt = [42, "Python", 3.14, True, None, [1, 2]]
leer = []
```

---

## Syntax und Erstellung

### Literal-Syntax (meistverwendet)

```python
# Einfache Liste
fruits = ["Apfel", "Banane", "Orange"]

# Liste mit verschiedenen Typen
mixed = [1, "zwei", 3.0, True]

# Verschachtelte Listen (2D-Array)
matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]

# Leere Liste
empty = []
```

### List Constructor

```python
# Aus Iterable erstellen
list_from_string = list("Python")  # ['P', 'y', 't', 'h', 'o', 'n']
list_from_range = list(range(5))    # [0, 1, 2, 3, 4]
list_from_tuple = list((1, 2, 3))   # [1, 2, 3]

# Leere Liste
empty = list()  # gleich wie []
```

### List mit Wiederholungen

```python
# Gleicher Wert mehrfach
zeros = [0] * 5           # [0, 0, 0, 0, 0]
pattern = ["X", "O"] * 3  # ['X', 'O', 'X', 'O', 'X', 'O']

# ⚠️ VORSICHT bei verschachtelten Listen!
wrong = [[]] * 3          # Drei Referenzen auf DIESELBE Liste!
wrong[0].append(1)
print(wrong)              # [[1], [1], [1]] - Überraschung!

# ✅ Richtig: List Comprehension
correct = [[] for _ in range(3)]  # Drei verschiedene Listen
correct[0].append(1)
print(correct)            # [[1], [], []]
```

---

## Zugriff auf Elemente

### Index-Zugriff

```python
fruits = ["Apfel", "Banane", "Orange", "Mango", "Kiwi"]

# Positiver Index (von vorne)
print(fruits[0])     # 'Apfel'
print(fruits[2])     # 'Orange'

# Negativer Index (von hinten)
print(fruits[-1])    # 'Kiwi' (letztes Element)
print(fruits[-2])    # 'Mango' (vorletztes Element)

# Index-Visualisierung:
#   0        1         2        3       4
# ['Apfel', 'Banane', 'Orange', 'Mango', 'Kiwi']
#  -5       -4        -3       -2      -1
```

### Slicing (Teilbereiche)

```python
numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

# Grundlegendes Slicing: [start:stop:step]
print(numbers[2:7])      # [2, 3, 4, 5, 6] - von Index 2 bis 6
print(numbers[:5])       # [0, 1, 2, 3, 4] - von Anfang bis 4
print(numbers[5:])       # [5, 6, 7, 8, 9] - von 5 bis Ende
print(numbers[::2])      # [0, 2, 4, 6, 8] - jedes zweite Element
print(numbers[::-1])     # [9, 8, 7, 6, 5, 4, 3, 2, 1, 0] - umgekehrt

# Slicing Details folgen in eigenem Kapitel
```

### IndexError vermeiden

```python
fruits = ["Apfel", "Banane"]

# ❌ IndexError
try:
    print(fruits[5])  # IndexError: list index out of range
except IndexError as e:
    print(f"Fehler: {e}")

# ✅ Sichere Methoden
if len(fruits) > 5:
    print(fruits[5])
else:
    print("Index außerhalb des Bereichs")

# Oder mit get-ähnlichem Verhalten
def safe_get(lst, index, default=None):
    try:
        return lst[index]
    except IndexError:
        return default

print(safe_get(fruits, 5, "Nicht gefunden"))  # 'Nicht gefunden'
```

---

## Elemente hinzufügen und ändern

### Einzelne Elemente ändern

```python
fruits = ["Apfel", "Banane", "Orange"]

# Element durch Index-Zuweisung ändern
fruits[1] = "Erdbeere"
print(fruits)  # ['Apfel', 'Erdbeere', 'Orange']

# Mehrere Elemente mit Slicing ändern
numbers = [1, 2, 3, 4, 5]
numbers[1:4] = [20, 30, 40]
print(numbers)  # [1, 20, 30, 40, 5]

# Slice kann verschiedene Länge haben!
numbers[1:4] = [100]  # Ersetzt 3 Elemente durch 1
print(numbers)  # [1, 100, 5]
```

### Elemente anfügen

```python
fruits = ["Apfel", "Banane"]

# append() - Ein Element am Ende hinzufügen
fruits.append("Orange")
print(fruits)  # ['Apfel', 'Banane', 'Orange']

# append fügt das gesamte Argument als EIN Element hinzu
fruits.append(["Mango", "Kiwi"])
print(fruits)  # ['Apfel', 'Banane', 'Orange', ['Mango', 'Kiwi']]
```

### Listen zusammenführen

```python
list1 = [1, 2, 3]
list2 = [4, 5, 6]

# extend() - Mehrere Elemente hinzufügen
list1.extend(list2)
print(list1)  # [1, 2, 3, 4, 5, 6]

# + Operator - Erstellt NEUE Liste
list3 = [1, 2, 3]
list4 = list3 + [4, 5, 6]
print(list4)  # [1, 2, 3, 4, 5, 6]
print(list3)  # [1, 2, 3] - unverändert!

# += Operator - Modifiziert bestehende Liste (wie extend)
list3 += [7, 8, 9]
print(list3)  # [1, 2, 3, 7, 8, 9]
```

---

## Elemente entfernen

```python
fruits = ["Apfel", "Banane", "Orange", "Banane", "Mango"]

# remove() - Entfernt erstes Vorkommen nach Wert
fruits.remove("Banane")
print(fruits)  # ['Apfel', 'Orange', 'Banane', 'Mango']

# pop() - Entfernt und gibt Element an Index zurück
last = fruits.pop()      # Letztes Element
print(last)              # 'Mango'
print(fruits)            # ['Apfel', 'Orange', 'Banane']

first = fruits.pop(0)    # Erstes Element
print(first)             # 'Apfel'

# del Statement - Löscht Element(e) per Index/Slice
numbers = [0, 1, 2, 3, 4, 5]
del numbers[2]           # Löscht Index 2
print(numbers)           # [0, 1, 3, 4, 5]

del numbers[1:3]         # Löscht Slice
print(numbers)           # [0, 4, 5]

# clear() - Entfernt alle Elemente
numbers.clear()
print(numbers)           # []
```

---

## Details und Interna

### Memory-Layout

Listen in Python sind **dynamische Arrays** mit einigen cleveren Optimierungen:

```python
import sys

# Liste belegt mehr Speicher als nötig für zukünftiges Wachstum
numbers = []
for i in range(10):
    size_before = sys.getsizeof(numbers)
    numbers.append(i)
    size_after = sys.getsizeof(numbers)
    if size_after != size_before:
        print(f"Länge: {i+1}, Größe: {size_after} Bytes, "
              f"Kapazität geschätzt: ~{(size_after - 56) // 8}")

# Output zeigt: Liste wächst in Sprüngen, nicht bei jedem append
# Typisches Wachstumsmuster: 0, 4, 8, 16, 25, 35, 46, 58, 72, 88...
```

**Interne Struktur:**
1. **Header** (56 Bytes auf 64-bit System): Objekt-Metadaten
2. **Array von Zeigern**: Referenzen auf die tatsächlichen Objekte
3. **Kapazität** > **Länge**: Platz für Wachstum ohne Reallokation

```
Python Liste:
┌─────────────────────┐
│ Header (56 Bytes)   │
├─────────────────────┤
│ Pointer → Obj 1     │  8 Bytes pro Zeiger
│ Pointer → Obj 2     │
│ Pointer → Obj 3     │
│ ...                 │
│ (ungenutzt)         │  Reserve-Kapazität
│ (ungenutzt)         │
└─────────────────────┘
```

### Wachstumsstrategie

```python
# Python verwendet eine Over-allocation Strategie
# Neue Kapazität ≈ alte Kapazität + (alte Kapazität >> 3) + 6

def estimate_growth(current_size):
    """Schätzt die neue Kapazität beim Wachstum"""
    return current_size + (current_size >> 3) + 6

# Beispiel-Wachstum:
for size in [0, 4, 8, 16, 32, 64, 128]:
    new_cap = estimate_growth(size)
    print(f"Größe {size:3d} → neue Kapazität: {new_cap:3d} "
          f"(+{new_cap - size} Plätze)")
```

**Warum Over-allocation?**
- Vermeidet häufige Reallokationen
- Amortisierte O(1) für `append()`
- Trade-off: Etwas mehr Speicher für bessere Performance

---

## Praktische Anwendungen

### Anwendungsfall 1: Stack (LIFO)

```python
# Liste als Stack verwenden
stack = []

# Push (hinzufügen)
stack.append(1)
stack.append(2)
stack.append(3)
print(f"Stack: {stack}")  # [1, 2, 3]

# Pop (entfernen)
top = stack.pop()
print(f"Entnommen: {top}")  # 3
print(f"Stack: {stack}")     # [1, 2]

# Peek (ansehen ohne entfernen)
if stack:
    print(f"Oben: {stack[-1]}")  # 2
```

### Anwendungsfall 2: To-Do Liste

```python
class TodoList:
    def __init__(self):
        self.tasks = []
    
    def add_task(self, task):
        """Fügt neue Aufgabe hinzu"""
        self.tasks.append({"task": task, "done": False})
    
    def complete_task(self, index):
        """Markiert Aufgabe als erledigt"""
        if 0 <= index < len(self.tasks):
            self.tasks[index]["done"] = True
    
    def remove_task(self, index):
        """Entfernt Aufgabe"""
        if 0 <= index < len(self.tasks):
            self.tasks.pop(index)
    
    def show_tasks(self):
        """Zeigt alle Aufgaben"""
        for i, task in enumerate(self.tasks):
            status = "✅" if task["done"] else "❌"
            print(f"{i}. {status} {task['task']}")

# Verwendung
todo = TodoList()
todo.add_task("Python lernen")
todo.add_task("Einkaufen")
todo.add_task("Sport machen")
todo.complete_task(0)
todo.show_tasks()
```

### Anwendungsfall 3: Batch-Verarbeitung

```python
# Daten in Batches verarbeiten
def process_in_batches(data, batch_size=3):
    """Verarbeitet Daten in Gruppen"""
    for i in range(0, len(data), batch_size):
        batch = data[i:i + batch_size]
        print(f"Verarbeite Batch {i // batch_size + 1}: {batch}")
        # Hier würde die eigentliche Verarbeitung stattfinden
        yield batch  # Generator für speichereffiziente Verarbeitung

# Beispiel
numbers = list(range(1, 11))
for batch in process_in_batches(numbers, batch_size=3):
    total = sum(batch)
    print(f"  → Summe: {total}\n")
```

### Anwendungsfall 4: Daten filtern und transformieren

```python
# Original-Daten
sales = [
    {"product": "Laptop", "price": 999, "quantity": 5},
    {"product": "Maus", "price": 25, "quantity": 50},
    {"product": "Tastatur", "price": 75, "quantity": 30},
    {"product": "Monitor", "price": 299, "quantity": 15}
]

# Filtern: Nur teure Produkte
expensive = [item for item in sales if item["price"] > 100]
print("Teure Produkte:", expensive)

# Transformieren: Gesamtwert berechnen
totals = [
    {
        "product": item["product"],
        "total_value": item["price"] * item["quantity"]
    }
    for item in sales
]
print("Gesamtwerte:", totals)

# Sortieren nach Gesamtwert
totals.sort(key=lambda x: x["total_value"], reverse=True)
print("Sortiert:", totals)
```

---

## Häufige Fallstricke

### ⚠️ Fallstrick 1: Listen sind Referenzen

```python
# ❌ Flache Kopie bei Zuweisung
list1 = [1, 2, 3]
list2 = list1         # Beide zeigen auf DIESELBE Liste!
list2.append(4)
print(list1)          # [1, 2, 3, 4] - überraschend?

# ✅ Richtige Kopie erstellen
list1 = [1, 2, 3]
list2 = list1.copy()  # oder list1[:] oder list(list1)
list2.append(4)
print(list1)          # [1, 2, 3] - unverändert
print(list2)          # [1, 2, 3, 4]
```

### ⚠️ Fallstrick 2: Modifikation während Iteration

```python
# ❌ Liste während Iteration ändern
numbers = [1, 2, 3, 4, 5]
for num in numbers:
    if num % 2 == 0:
        numbers.remove(num)  # PROBLEM: Überspringt Elemente!
print(numbers)  # [1, 3, 5] - aber unvorhersehbares Verhalten

# ✅ Über Kopie iterieren
numbers = [1, 2, 3, 4, 5]
for num in numbers[:]:  # Iteriere über Kopie
    if num % 2 == 0:
        numbers.remove(num)
print(numbers)  # [1, 3, 5]

# ✅ Besser: List Comprehension
numbers = [1, 2, 3, 4, 5]
numbers = [num for num in numbers if num % 2 != 0]
print(numbers)  # [1, 3, 5]
```

### ⚠️ Fallstrick 3: Verschachtelte Listen mit *

```python
# ❌ Alle zeigen auf dieselbe Liste
matrix = [[0] * 3] * 3
matrix[0][0] = 1
print(matrix)  # [[1, 0, 0], [1, 0, 0], [1, 0, 0]] - alle Zeilen ändern sich!

# ✅ Jede Zeile ist eigene Liste
matrix = [[0] * 3 for _ in range(3)]
matrix[0][0] = 1
print(matrix)  # [[1, 0, 0], [0, 0, 0], [0, 0, 0]] - nur erste Zeile ändert sich
```

### ⚠️ Fallstrick 4: Default Argument ist mutable

```python
# ❌ Mutable Default Argument
def add_to_list(item, target=[]):  # GEFÄHRLICH!
    target.append(item)
    return target

print(add_to_list(1))  # [1]
print(add_to_list(2))  # [1, 2] - überraschend!

# ✅ None als Default, dann neue Liste erstellen
def add_to_list(item, target=None):
    if target is None:
        target = []
    target.append(item)
    return target

print(add_to_list(1))  # [1]
print(add_to_list(2))  # [2] - wie erwartet
```

---

## Performance-Hinweise

### Zeitkomplexität wichtiger Operationen

| Operation | Zeitkomplexität | Erklärung |
|-----------|-----------------|-----------|
| `list[i]` | O(1) | Direkter Zugriff per Index |
| `list[i] = x` | O(1) | Direktes Setzen |
| `len(list)` | O(1) | Größe ist gespeichert |
| `list.append(x)` | O(1)* | Amortisiert konstant |
| `list.pop()` | O(1) | Vom Ende |
| `list.pop(0)` | O(n) | Muss alle verschieben |
| `list.insert(0, x)` | O(n) | Muss alle verschieben |
| `x in list` | O(n) | Muss durchsuchen |
| `list.remove(x)` | O(n) | Suchen + Verschieben |
| `list.sort()` | O(n log n) | Timsort Algorithmus |
| `list.reverse()` | O(n) | Alle umkehren |

**\* Amortisierte Komplexität:** Im Durchschnitt O(1), auch wenn gelegentlich O(n) durch Reallokation.

### Wann Listen verwenden?

**✅ Gut für:**
- Sequentielle Daten mit häufigem Zugriff per Index
- Stack-Operationen (append/pop am Ende)
- Wenn Reihenfolge wichtig ist
- Heterogene Datensammlungen
- Kleine bis mittlere Datenmengen

**❌ Nicht optimal für:**
- Häufiges Einfügen/Löschen am Anfang → nutze `collections.deque`
- Nur numerische Daten → nutze `array.array` oder NumPy
- Membership-Tests (`x in list`) bei vielen Elementen → nutze `set`
- Eindeutige Werte → nutze `set`
- Key-Value Paare → nutze `dict`

---

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [List - Built-in Types](https://docs.python.org/3/library/stdtypes.html#list) - Komplette Referenz zu List-Methoden und Operationen
- [More on Lists](https://docs.python.org/3/tutorial/datastructures.html#more-on-lists) - Tutorial mit Beispielen
- [Time Complexity](https://wiki.python.org/moin/TimeComplexity) - Performance-Charakteristiken aller Operationen
- [Sorting HOW TO](https://docs.python.org/3/howto/sorting.html) - Detaillierter Guide zum Sortieren

---

## Verwandte Themen

- [[02_Sequenzen/01_Listen/02_List Methoden|List Methoden]] - Alle verfügbaren Methoden im Detail
- [[02_Sequenzen/02_Tupel/01_Tuple Basics|Tuple Basics]] - Unveränderliche Alternative
- [[02_Sequenzen/03_Slicing/01_Slicing Grundlagen|Slicing]] - Fortgeschrittene Techniken
- [[04_Fortgeschritten/02_Deque/01_Deque Basics|Deque]] - Effizient für beide Enden

---

← [[01_Grundlagen/05_Bytes/03_Übungen Bytes|Übungen Bytes]] | [[INDEX|📑 Index]] | [[02_Sequenzen/01_Listen/02_List Methoden|List Methoden]] →