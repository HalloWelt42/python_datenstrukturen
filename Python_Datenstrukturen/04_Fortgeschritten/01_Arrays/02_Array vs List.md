---
tags: [python, datenstrukturen, arrays, listen, vergleich]
created: 2025-10-01
---

← [[04_Fortgeschritten/01_Arrays/01_Array Module|Array Module]] | [[INDEX|📑 Index]] | [[04_Fortgeschritten/01_Arrays/03_Übungen Arrays|Übungen Arrays]] →

# Array vs. List

> [!tip] Lernziel
> Nach dieser Seite kannst du zwischen array und list fundiert wählen und verstehst die Trade-offs bei Performance, Speicher und Flexibilität.

## Grundlegende Unterschiede

| Feature | list | array |
|---------|------|-------|
| **Typ-Einschränkung** | Keine (beliebige Objekte) | Nur ein Typ (homogen) |
| **Speicher-Effizienz** | Weniger effizient | Sehr effizient |
| **Flexibilität** | Sehr flexibel | Eingeschränkt |
| **Performance** | Gut für allgemeine Zwecke | Besser für numerische Daten |
| **Type Safety** | Keine | Typ-geprüft |
| **C-API Integration** | Schwieriger | Einfacher |
| **Built-in** | Ja | Nein (import nötig) |

```python
import array

# Liste: Beliebige Typen
liste = [1, 'text', 3.14, [1, 2], {'key': 'value'}]
print(f"Liste: {liste}")  # Funktioniert!

# Array: Nur ein Typ
arr = array.array('i', [1, 2, 3, 4, 5])
print(f"Array: {arr}")

# ❌ Array mit gemischten Typen geht nicht
try:
    mixed_arr = array.array('i', [1, 'text'])
except TypeError as e:
    print(f"Fehler: {e}")
```

---

## Speicher-Vergleich

### Memory Overhead

```python
import array
import sys

# Verschiedene Größen testen
sizes = [10, 100, 1000, 10000]

print(f"{'Elemente':<10} | {'Liste (bytes)':<15} | {'Array (bytes)':<15} | {'Ersparnis'}")
print("-" * 65)

for size in sizes:
    # Liste von Integers
    liste = list(range(size))
    liste_bytes = sys.getsizeof(liste)
    
    # Array von Integers
    arr = array.array('i', range(size))
    arr_bytes = sys.getsizeof(arr)
    
    ersparnis = (1 - arr_bytes/liste_bytes) * 100
    
    print(f"{size:<10} | {liste_bytes:<15,} | {arr_bytes:<15,} | {ersparnis:.1f}%")

# Ausgabe zeigt:
# Array braucht ~50-75% weniger Speicher!
```

### Warum der Unterschied?

```python
"""
LISTE (list):
- Jedes Element ist ein Python-Objekt
- Speichert Referenzen (Pointer) auf Objekte
- Overhead pro Element: ~8 bytes (Pointer) + Objekt-Overhead

Beispiel für Liste mit 3 Integers:
[PyObject*, PyObject*, PyObject*]
   ↓          ↓          ↓
 Integer(1) Integer(2) Integer(3)
 
Pro Element: 8 bytes (Pointer) + ~28 bytes (Integer-Objekt) = 36 bytes

ARRAY (array):
- Speichert rohe C-Werte direkt
- Keine Python-Objekt-Wrapper
- Keine Pointer-Indirektion

Beispiel für Array 'i' mit 3 Integers:
[int32, int32, int32]
  
Pro Element: 4 bytes (direkter Wert)

=> Array: 90% weniger Speicher!
"""
```

---

## Performance-Vergleich

### Erstellung

```python
import array
import timeit

data = range(100000)

# Liste erstellen
def create_list():
    return list(data)

# Array erstellen
def create_array():
    return array.array('i', data)

list_time = timeit.timeit(create_list, number=100)
array_time = timeit.timeit(create_array, number=100)

print(f"Erstellung (100,000 Elemente):")
print(f"  Liste: {list_time:.3f}s")
print(f"  Array: {array_time:.3f}s")
print(f"  Verhältnis: {list_time/array_time:.2f}x")

# Liste ist meist schneller beim Erstellen
# (kein Type-Checking nötig)
```

### Zugriff

```python
import array
import timeit

size = 100000
liste = list(range(size))
arr = array.array('i', range(size))

# Index-Zugriff
def list_access():
    return liste[50000]

def array_access():
    return arr[50000]

list_time = timeit.timeit(list_access, number=1000000)
array_time = timeit.timeit(array_access, number=1000000)

print(f"\nIndex-Zugriff:")
print(f"  Liste: {list_time:.3f}s")
print(f"  Array: {array_time:.3f}s")
print(f"  Verhältnis: {list_time/array_time:.2f}x")

# Array ist minimal schneller (direkter Zugriff)
```

### Iteration

```python
import array
import timeit

size = 10000
liste = list(range(size))
arr = array.array('i', range(size))

# Iteration und Summe
def list_sum():
    total = 0
    for x in liste:
        total += x
    return total

def array_sum():
    total = 0
    for x in arr:
        total += x
    return total

list_time = timeit.timeit(list_sum, number=1000)
array_time = timeit.timeit(array_sum, number=1000)

print(f"\nIteration und Summe:")
print(f"  Liste: {list_time:.3f}s")
print(f"  Array: {array_time:.3f}s")
print(f"  Verhältnis: {list_time/array_time:.2f}x")

# Ähnliche Performance, Array minimal schneller
```

### Append-Operationen

```python
import array
import timeit

# Viele append()-Operationen
def list_append():
    l = []
    for i in range(10000):
        l.append(i)
    return l

def array_append():
    a = array.array('i')
    for i in range(10000):
        a.append(i)
    return a

list_time = timeit.timeit(list_append, number=100)
array_time = timeit.timeit(array_append, number=100)

print(f"\n10,000 append()-Operationen:")
print(f"  Liste: {list_time:.3f}s")
print(f"  Array: {array_time:.3f}s")
print(f"  Verhältnis: {list_time/array_time:.2f}x")

# Liste ist meist schneller (kein Type-Check)
```

---

## Funktionalitäts-Vergleich

### Gemeinsame Operationen

```python
import array

liste = [1, 2, 3, 4, 5]
arr = array.array('i', [1, 2, 3, 4, 5])

# ✅ Beide unterstützen:
print(liste[0])      # Index-Zugriff
print(arr[0])

print(liste[1:3])    # Slicing
print(arr[1:3])

print(len(liste))    # Länge
print(len(arr))

print(2 in liste)    # Membership
print(2 in arr)

for x in liste:      # Iteration
    pass
for x in arr:
    pass

liste.append(6)      # Append
arr.append(6)

liste.extend([7, 8]) # Extend
arr.extend([7, 8])

liste.insert(0, 0)   # Insert
arr.insert(0, 0)

liste.remove(0)      # Remove
arr.remove(0)

liste.pop()          # Pop
arr.pop()

liste.reverse()      # Reverse
arr.reverse()

# count, index auch verfügbar
```

### Nur Liste hat

```python
# ❌ Array hat NICHT:

liste = [3, 1, 4, 1, 5]

# sort() Methode
liste.sort()
print(liste)  # [1, 1, 3, 4, 5]

# Array muss anders sortiert werden
arr = array.array('i', [3, 1, 4, 1, 5])
sorted_arr = array.array('i', sorted(arr))
print(sorted_arr)  # array('i', [1, 1, 3, 4, 5])

# List Comprehensions direkt
squared = [x**2 for x in liste]

# Array braucht Konvertierung
arr_squared = array.array('i', (x**2 for x in arr))

# clear() Methode (aber del arr[:] funktioniert)
liste.clear()
# arr.clear()  # Gibt es nicht!
arr = array.array('i')  # Neu erstellen

# copy() Methode
liste_copy = liste.copy()
# arr_copy = arr.copy()  # Gibt es nicht!
arr_copy = array.array(arr.typecode, arr)  # Manuell
```

### Nur Array hat

```python
import array

arr = array.array('i', [1, 2, 3])

# ✅ Array-spezifisch:

# typecode - Typ-Information
print(arr.typecode)  # 'i'

# itemsize - Bytes pro Element
print(arr.itemsize)  # 4

# buffer_info() - Memory-Info
print(arr.buffer_info())  # (address, length)

# tobytes() / frombytes()
byte_data = arr.tobytes()
arr2 = array.array('i')
arr2.frombytes(byte_data)

# tofile() / fromfile()
with open('data.bin', 'wb') as f:
    arr.tofile(f)

# tolist() / fromlist()
liste = arr.tolist()
arr3 = array.array('i')
arr3.fromlist(liste)

# byteswap() - Endianness
arr.byteswap()

# Cleanup
import os
os.remove('data.bin')
```

---

## Anwendungsfälle

### Wann Liste verwenden?

```python
# ✅ LISTE für:

# 1. Gemischte Typen
mixed = [1, 'text', 3.14, {'key': 'value'}]

# 2. Kleine Datenmengen (< 1000 Elemente)
small_data = [1, 2, 3, 4, 5]

# 3. Häufige Typ-Änderungen
data = [1, 2, 3]
data[0] = 'now a string'  # OK

# 4. Komplexe Objekte
class Person:
    def __init__(self, name):
        self.name = name

people = [Person('Alice'), Person('Bob')]

# 5. Wenn Flexibilität wichtiger als Performance
general_purpose = [x for x in range(100) if x % 2 == 0]

# 6. Nested Structures
nested = [[1, 2], [3, 4], [5, 6]]

# 7. Als Stack/Queue
stack = []
stack.append(1)  # Push
item = stack.pop()  # Pop

# 8. Mit sorted(), filter(), map()
numbers = [3, 1, 4, 1, 5]
sorted_nums = sorted(numbers)
```

### Wann Array verwenden?

```python
import array

# ✅ ARRAY für:

# 1. Große Mengen numerischer Daten
sensor_data = array.array('f', [0.0] * 1000000)

# 2. Binary I/O
with open('data.bin', 'wb') as f:
    sensor_data.tofile(f)

# 3. Memory-kritische Anwendungen
# Array braucht ~50-75% weniger Speicher

# 4. Interface zu C-Code
# array.array ist kompatibel mit C-Arrays

# 5. Audio/Video Processing
audio_samples = array.array('h')  # 16-bit samples

# 6. Numerische Berechnungen
vector = array.array('d', [1.0, 2.0, 3.0])

# 7. Embedded Systems (RAM-begrenzt)
readings = array.array('i')

# 8. Wenn Type Safety gewünscht
typed_data = array.array('i')  # Nur Integers!

# Cleanup
import os
os.remove('data.bin')
```

---

## Konvertierung

### Liste ↔ Array

```python
import array

# Liste → Array
liste = [1, 2, 3, 4, 5]
arr = array.array('i', liste)
print(arr)  # array('i', [1, 2, 3, 4, 5])

# Array → Liste
arr = array.array('i', [1, 2, 3])
liste = arr.tolist()
print(liste)  # [1, 2, 3]
print(type(liste))  # <class 'list'>

# Oder mit list()
liste2 = list(arr)
print(liste2)  # [1, 2, 3]

# Liste zu Array mit fromlist()
arr2 = array.array('i')
arr2.fromlist([10, 20, 30])
print(arr2)  # array('i', [10, 20, 30])
```

---

## Hybrid-Ansätze

### Liste von Arrays

```python
import array

# Für 2D-Daten: Liste von Arrays
# Jede Zeile ist ein Array (speichereffizient)
matrix = [
    array.array('i', [1, 2, 3]),
    array.array('i', [4, 5, 6]),
    array.array('i', [7, 8, 9])
]

print("Matrix:")
for row in matrix:
    print(list(row))

# Zugriff: row[i][j]
print(f"Element [1][2]: {matrix[1][2]}")  # 6

# Besser als Liste von Listen für große Daten
```

### Array mit Metadata

```python
import array

class TypedArray:
    """Wrapper um array mit Typ-Informationen"""
    
    def __init__(self, typecode, data=None):
        self.typecode = typecode
        self.array = array.array(typecode, data or [])
        self.metadata = {}  # Flexibel wie dict
    
    def append(self, value):
        self.array.append(value)
    
    def __getitem__(self, index):
        return self.array[index]
    
    def __len__(self):
        return len(self.array)
    
    def __repr__(self):
        return f"TypedArray('{self.typecode}', {list(self.array)})"

# Verwendung
data = TypedArray('f', [1.1, 2.2, 3.3])
data.metadata['unit'] = 'meters'
data.metadata['sensor'] = 'A1'

print(data)
print(f"Unit: {data.metadata['unit']}")
```

---

## Best Practices

### Entscheidungs-Flowchart

```python
"""
Brauche ich gemischte Typen?
│
├─ JA → Liste
│
└─ NEIN
    │
    Mehr als 1000 Elemente?
    │
    ├─ NEIN → Liste (einfacher)
    │
    └─ JA
        │
        Ist Speicher kritisch?
        │
        ├─ JA → Array
        │
        └─ NEIN
            │
            Numerische Daten?
            │
            ├─ JA → Array (besser)
            │
            └─ NEIN → Liste

Faustregel: 
- Default: Liste
- Große numerische Daten: Array
- Memory-kritisch: Array
"""
```

### Code-Beispiele

```python
import array

# ✅ GUT: Array für große numerische Daten
def process_sensor_data(readings):
    """Verarbeitet viele Sensor-Readings"""
    data = array.array('f', readings)  # Memory-effizient
    # Verarbeitung...
    return data

# ✅ GUT: Liste für gemischte Daten
def process_records(records):
    """Verarbeitet gemischte Datensätze"""
    data = []
    for record in records:
        data.append({
            'id': record.id,
            'name': record.name,
            'values': list(record.values)
        })
    return data

# ❌ SCHLECHT: Array für kleine Datenmengen
def get_coordinates():
    # Overhead nicht wert für 3 Werte!
    return array.array('d', [x, y, z])

# ✅ BESSER: Tuple oder Liste
def get_coordinates():
    return (x, y, z)  # Oder [x, y, z]

# ❌ SCHLECHT: Liste für Million Integers
def generate_data():
    return [i for i in range(1000000)]  # Viel RAM!

# ✅ BESSER: Array
def generate_data():
    return array.array('i', range(1000000))  # ~50% weniger RAM
```

---

## Zusammenfassung: Quick Reference

```python
import array

"""
LISTE (list)
  ✅ Flexibel, gemischte Typen
  ✅ Einfach zu verwenden
  ✅ Viele Built-in Methoden
  ✅ List Comprehensions
  ❌ Mehr Speicher
  ❌ Kein Type Safety

ARRAY (array.array)
  ✅ Speicher-effizient
  ✅ Schneller für numerische Ops
  ✅ Type Safety
  ✅ Binary I/O
  ❌ Nur ein Typ
  ❌ Weniger Methoden
  ❌ Import nötig

WANN WAS:
  < 1000 Elemente           → Liste
  Gemischte Typen           → Liste
  > 10000 numerische Werte  → Array
  Binary I/O                → Array
  Memory kritisch           → Array
  General Purpose           → Liste
"""
```

---

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [array module](https://docs.python.org/3/library/array.html) - Array Referenz
- [list](https://docs.python.org/3/library/stdtypes.html#list) - Liste Referenz
- [Performance Tips](https://wiki.python.org/moin/PythonSpeed/PerformanceTips) - Allgemeine Tips

---

## Verwandte Themen

- [[04_Fortgeschritten/01_Arrays/01_Array Module|Array Module]] - Array im Detail
- [[02_Sequenzen/01_Listen/01_List Basics|List Basics]] - Listen im Detail
- [[04_Fortgeschritten/01_Arrays/03_Übungen Arrays|Übungen Arrays]] - Praktische Übungen
- [[01_Grundlagen/05_Bytes/01_Bytes und Bytearray|Bytes und Bytearray]] - Alternative für Byte-Daten

---

← [[04_Fortgeschritten/01_Arrays/01_Array Module|Array Module]] | [[INDEX|📑 Index]] | [[04_Fortgeschritten/01_Arrays/03_Übungen Arrays|Übungen Arrays]] →