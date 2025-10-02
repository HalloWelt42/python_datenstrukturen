---
tags: [python, datenstrukturen, memory, garbage-collection, grundlagen]
created: 2025-10-01
---

← [[02_Mutability vs Immutability|Mutability vs Immutability]] | [[INDEX|📑 Index]] | [[01_Grundlagen/02_Numerische_Typen/01_Integer (int)|Integer (int)]] →

# Memory Management Basics

> [!tip] Lernziel
> Du verstehst, wie Python Objekte im Speicher verwaltet, was Referenzen sind und wie Garbage Collection funktioniert

## Wie Python Objekte speichert

Python nutzt ein **Objekt-basiertes Memory Model**:

- **Alle Werte sind Objekte** im Heap
- **Variablen sind Referenzen** zu diesen Objekten
- **Garbage Collector** räumt ungenutzte Objekte auf

```python
# Was wirklich passiert:
x = 42

# Schritt 1: Python erstellt int-Objekt mit Wert 42 im Heap
# Schritt 2: Variable 'x' zeigt auf dieses Objekt (Referenz)
# Schritt 3: Reference Count des Objekts wird erhöht

print(id(x))  # Speicheradresse des Objekts (z.B. 140234567890)
```

## Objekte und Referenzen

### Was ist eine Referenz?

```python
# Eine Referenz ist wie eine Adresse
a = [1, 2, 3]  # Erstellt list-Objekt
b = a          # b bekommt die gleiche Referenz!

# Beide Variablen zeigen auf das GLEICHE Objekt
print(id(a))  # 140234567890
print(id(b))  # 140234567890 (gleiche Adresse!)
print(a is b) # True

# Änderung betrifft beide
b.append(4)
print(a)  # [1, 2, 3, 4]
print(b)  # [1, 2, 3, 4]
```

### Visualisierung

```
Memory (Heap):
┌─────────────────┐
│ List-Objekt     │
│ [1, 2, 3, 4]    │ ← Beide Variablen zeigen hierauf
└─────────────────┘
        ↑    ↑
        │    │
        a    b    (Referenzen)
```

### Neue Objekte vs. Referenzen

```python
# Neue Referenz erstellen (zeigt auf gleiches Objekt)
liste1 = [1, 2, 3]
liste2 = liste1  # Referenz kopiert, nicht das Objekt!

# Neues Objekt erstellen
liste3 = [1, 2, 3]  # Komplett neues Objekt!

print(liste1 is liste2)  # True (gleiche Referenz)
print(liste1 is liste3)  # False (verschiedene Objekte)
print(liste1 == liste3)  # True (gleicher Inhalt)
```

## Reference Counting

Python nutzt **Reference Counting** um zu wissen, wann ein Objekt gelöscht werden kann.

### Wie es funktioniert

```python
import sys

# Erstelle Objekt
x = [1, 2, 3]
print(sys.getrefcount(x))  # 2 (x + getrefcount selbst)

# Neue Referenz erstellen
y = x
print(sys.getrefcount(x))  # 3 (x, y, + getrefcount)

# Referenz löschen
del y
print(sys.getrefcount(x))  # 2 (zurück zu vorher)

# Letzte Referenz löschen
del x
# Objekt wird jetzt vom Garbage Collector gelöscht
```

### Reference Count visualisiert

```python
# Objekt mit refcount = 1
a = [1, 2, 3]

# refcount = 2
b = a

# refcount = 3
c = a

# refcount = 4
d = [a, "other"]  # Liste enthält Referenz zu a

# refcount = 3
del b

# refcount = 2
del c

# refcount = 1
del d

# refcount = 0 → Objekt wird gelöscht
del a
```

## Garbage Collection

Python's Garbage Collector hat zwei Mechanismen:

### 1. Reference Counting (Haupt-Mechanismus)

```python
# Objekt wird sofort gelöscht wenn refcount = 0
def test():
    x = [1, 2, 3]  # refcount = 1
    # Funktion endet, x wird gelöscht, refcount = 0
    # Objekt wird SOFORT freigegeben

test()
# Liste ist jetzt weg
```

### 2. Generational Garbage Collection (für zyklische Referenzen)

```python
# Zyklische Referenzen können nicht durch Reference Counting gelöst werden
class Node:
    def __init__(self, value):
        self.value = value
        self.next = None

# Erstelle Zyklus
a = Node(1)
b = Node(2)
a.next = b
b.next = a  # Zyklus!

# Auch wenn wir a und b löschen, zeigen sie aufeinander
del a
del b
# Objekte existieren noch (zyklische Referenz)
# Garbage Collector erkennt das und räumt auf

# Garbage Collector manuell starten
import gc
print(gc.collect())  # Gibt Anzahl gelöschter Objekte zurück
```

### Generationen

Python teilt Objekte in drei Generationen:

- **Generation 0**: Neue Objekte (häufig gecheckt)
- **Generation 1**: Objekte die Gen 0 überlebt haben
- **Generation 2**: Langlebige Objekte (selten gecheckt)

```python
import gc

# Garbage Collection Status
print(gc.get_count())  # (threshold0, threshold1, threshold2)
# z.B. (700, 10, 10)

# Thresholds zeigen, wann GC läuft
thresholds = gc.get_threshold()
print(thresholds)  # (700, 10, 10)
# Gen 0: alle 700 Allokationen
# Gen 1: alle 10 Gen 0 Collections
# Gen 2: alle 10 Gen 1 Collections

# Statistiken
print(gc.get_stats())  # Detaillierte GC-Statistiken
```

## Objekt-Identität mit id()

```python
# id() gibt die Speicheradresse zurück
x = [1, 2, 3]
print(id(x))  # z.B. 140234567890

# Gleiche id = gleiches Objekt
y = x
print(id(y))  # 140234567890 (gleiche Adresse)

# Verschiedene id = verschiedene Objekte
z = [1, 2, 3]
print(id(z))  # z.B. 140234567999 (andere Adresse)
```

### Integer und String Interning

Python optimiert kleine Integers und kurze Strings:

```python
# Kleine Integers (-5 bis 256) werden gecacht
a = 42
b = 42
print(a is b)  # True (gleiche Objekte!)

# Große Integers nicht
x = 1000
y = 1000
print(x is y)  # False (verschiedene Objekte)

# Strings werden "interned" (gecacht) wenn sie aussehen wie Identifier
s1 = "hello"
s2 = "hello"
print(s1 is s2)  # True (gleiche Objekte!)

# Strings mit Sonderzeichen nicht unbedingt
s3 = "hello world!"
s4 = "hello world!"
print(s3 is s4)  # Möglicherweise False (nicht immer interned)

# Explizit internen
import sys
s5 = sys.intern("hello world!")
s6 = sys.intern("hello world!")
print(s5 is s6)  # True (jetzt gecacht)
```

## Speicher-Layout von Objekten

### Basis-Objekt Struktur

Jedes Python-Objekt hat:
- **Reference Count** (wie viele Referenzen zeigen darauf)
- **Type-Pointer** (welcher Typ ist das Objekt)
- **Daten** (der eigentliche Wert)

```python
import sys

# Größe verschiedener Objekte
print(sys.getsizeof(42))          # 28 Bytes
print(sys.getsizeof(3.14))        # 24 Bytes
print(sys.getsizeof(""))          # 49 Bytes
print(sys.getsizeof("a"))         # 50 Bytes
print(sys.getsizeof([]))          # 56 Bytes
print(sys.getsizeof([1, 2, 3]))   # 80 Bytes
print(sys.getsizeof({}))          # 64 Bytes
print(sys.getsizeof({1: 1}))      # 232 Bytes (hash table!)
```

### Listen im Detail

```python
# Liste speichert Array von Referenzen
liste = [1, 2, 3]

# Memory Layout (vereinfacht):
# Liste-Objekt: [refcount, type, size, capacity, pointer_to_array]
#                                                         ↓
# Array:        [ref_zu_1, ref_zu_2, ref_zu_3, None, None, ...]

# Deshalb: Liste mit 3 Elementen ist größer als leere Liste
print(sys.getsizeof([]))           # 56 Bytes
print(sys.getsizeof([1]))          # 64 Bytes (+8)
print(sys.getsizeof([1, 2]))       # 72 Bytes (+8)
print(sys.getsizeof([1, 2, 3]))    # 80 Bytes (+8)
```

### Dictionaries im Detail

```python
# Dictionary nutzt Hash Table
d = {"a": 1}

# Memory Layout (vereinfacht):
# Dict-Objekt: [refcount, type, size, hash_table_pointer]
#                                              ↓
# Hash Table:  [größeres Array mit entries]

# Deshalb: Dictionaries brauchen mehr Speicher
print(sys.getsizeof({}))              # 64 Bytes (leer)
print(sys.getsizeof({"a": 1}))        # 232 Bytes (erste entry)
print(sys.getsizeof({"a": 1, "b": 2}))  # 232 Bytes (noch Platz)

# Hash Table wird größer wenn nötig (Resize bei ~2/3 voll)
```

## Shallow Copy vs Deep Copy

```python
import copy

# Original mit verschachtelten Listen
original = [[1, 2], [3, 4]]

# Shallow Copy - kopiert nur äußere Struktur
shallow = copy.copy(original)
# oder: shallow = original.copy()
# oder: shallow = original[:]

# Deep Copy - kopiert alles rekursiv
deep = copy.deepcopy(original)

# Modifikation testen
original[0].append(99)

print(original)  # [[1, 2, 99], [3, 4]]
print(shallow)   # [[1, 2, 99], [3, 4]] - auch betroffen!
print(deep)      # [[1, 2], [3, 4]] - nicht betroffen

# Visualisierung:
# SHALLOW COPY:
# original → [[1,2,99], [3,4]] ← shallow zeigt auf gleiche innere Listen!
#
# DEEP COPY:
# original → [[1,2,99], [3,4]]
# deep     → [[1,2], [3,4]]     (komplett neue Objekte)
```

## Memory Leaks vermeiden

### Problem 1: Globale Variablen

```python
# ❌ Globale Variablen werden nie gelöscht
global_liste = []

def füge_daten_hinzu():
    global_liste.append([0] * 1000000)  # 1 Million Nullen
    # Liste wird NIE freigegeben!

# ✅ Besser: Lokale Variablen oder explizites Löschen
def verarbeite_daten():
    lokale_liste = []
    lokale_liste.append([0] * 1000000)
    # Wird automatisch gelöscht wenn Funktion endet
    return result
```

### Problem 2: Zyklische Referenzen

```python
# ❌ Zyklische Referenzen können Speicher blockieren
class Node:
    def __init__(self):
        self.ref = None

a = Node()
b = Node()
a.ref = b
b.ref = a  # Zyklus!

# Auch nach del existieren Objekte noch
del a, b
# Garbage Collector räuft auf, aber später

# ✅ Besser: Weak References verwenden
import weakref

class NodeSicher:
    def __init__(self):
        self.ref = None
    
    def set_ref(self, other):
        self.ref = weakref.ref(other)  # Schwache Referenz!

a = NodeSicher()
b = NodeSicher()
a.set_ref(b)
b.set_ref(a)

# Kein Problem mehr mit Garbage Collection
```

### Problem 3: Closures mit großen Objekten

```python
# ❌ Closure hält Referenz auf große Objekte
def create_handler():
    large_data = [0] * 1000000  # Große Daten
    
    def handler():
        print("Handler called")
        # large_data wird im Closure gespeichert!
    
    return handler

h = create_handler()
# large_data ist immer noch im Speicher!

# ✅ Besser: Nur relevante Daten im Closure
def create_handler_besser():
    large_data = [0] * 1000000
    summary = len(large_data)  # Nur das Wichtigste
    
    def handler():
        print(f"Handler called, data size was: {summary}")
        # large_data ist NICHT im Closure!
    
    return handler
```

## Performance-Hinweise

### Object Pooling für häufig erstellte Objekte

```python
# Python macht das automatisch für kleine Integers und Strings
# Für eigene Klassen kann man das implementieren:

class ObjectPool:
    def __init__(self, size=10):
        self.pool = [self._create_object() for _ in range(size)]
    
    def _create_object(self):
        return []  # Oder eigene Klasse
    
    def acquire(self):
        if self.pool:
            return self.pool.pop()
        return self._create_object()
    
    def release(self, obj):
        obj.clear()  # Reset
        self.pool.append(obj)

# Nutzung
pool = ObjectPool()
liste = pool.acquire()
# ... nutze liste ...
pool.release(liste)  # Statt zu löschen
```

### Memory Profiling

```python
# Speicherverbrauch überwachen
import tracemalloc

# Start tracking
tracemalloc.start()

# Dein Code hier
große_liste = [0] * 1000000

# Aktuellen Speicher
current, peak = tracemalloc.get_traced_memory()
print(f"Current: {current / 1024 / 1024:.2f} MB")
print(f"Peak: {peak / 1024 / 1024:.2f} MB")

# Stop tracking
tracemalloc.stop()
```

## Häufige Fallstricke

### Fallstrick 1: Unerwartete Referenzen

```python
# ❌ Unerwartet: Beide Listen zeigen auf gleiches Objekt
original = [1, 2, 3]
kopie = original  # Nur Referenz kopiert!

kopie.append(4)
print(original)  # [1, 2, 3, 4] - auch verändert!

# ✅ Richtig: Explizite Kopie
kopie = original.copy()
kopie.append(5)
print(original)  # [1, 2, 3, 4] - unverändert
```

### Fallstrick 2: Dictionaries in Schleifen

```python
# ❌ Ineffizient: Neue Dicts werden erstellt aber nie gelöscht
def verarbeite_daten_schlecht():
    for i in range(1000):
        d = {"index": i, "daten": [0] * 1000}
        # Dict wird bei jedem Durchlauf neu erstellt
        # Aber erst am Ende alle gelöscht!

# ✅ Besser: Wiederverwendung
def verarbeite_daten_gut():
    d = {}
    for i in range(1000):
        d.clear()
        d["index"] = i
        d["daten"] = [0] * 1000
        # Verwende d weiter...
```

### Fallstrick 3: Generator vs. Liste

```python
import sys

# Liste: Alle Elemente im Speicher
liste = [x**2 for x in range(1000000)]
print(sys.getsizeof(liste))  # ~8 MB

# Generator: Nur aktuelles Element
generator = (x**2 for x in range(1000000))
print(sys.getsizeof(generator))  # ~200 Bytes!

# ✅ Nutze Generatoren für große Datenmengen
```

## Praktische Anwendungen

### Anwendung 1: Speichereffiziente Datenverarbeitung

```python
def verarbeite_große_datei(dateiname):
    """
    Verarbeitet große Datei Zeile für Zeile
    Speichert nicht die ganze Datei im RAM!
    """
    with open(dateiname) as f:
        for zeile in f:  # Generator!
            # Verarbeite Zeile
            yield zeile.strip().upper()
    # Dateiobjekt wird automatisch geschlossen
    # Nur eine Zeile zur Zeit im Speicher!

# Nutzung
for verarbeitete_zeile in verarbeite_große_datei("große_datei.txt"):
    print(verarbeitete_zeile)
```

### Anwendung 2: Cache mit automatischer Bereinigung

```python
import weakref

class Cache:
    def __init__(self):
        # Weak Value Dictionary: Werte werden gelöscht wenn nicht mehr referenziert
        self._cache = weakref.WeakValueDictionary()
    
    def get(self, key):
        return self._cache.get(key)
    
    def set(self, key, value):
        self._cache[key] = value

# Nutzung
cache = Cache()

obj = [1, 2, 3]
cache.set("data", obj)
print(cache.get("data"))  # [1, 2, 3]

del obj  # Objekt wird gelöscht
print(cache.get("data"))  # None - automatisch aus Cache entfernt!
```

### Anwendung 3: Kontextbasierte Speicherverwaltung

```python
class MemoryContext:
    def __init__(self):
        self.objekte = []
    
    def __enter__(self):
        return self
    
    def __exit__(self, *args):
        # Räume auf beim Verlassen des Contexts
        self.objekte.clear()
        import gc
        gc.collect()  # Explizite Garbage Collection
    
    def erstelle_objekt(self, größe):
        obj = [0] * größe
        self.objekte.append(obj)
        return obj

# Nutzung
with MemoryContext() as ctx:
    große_liste = ctx.erstelle_objekt(1000000)
    # Nutze große_liste...
# Beim Verlassen: Alles wird automatisch aufgeräumt!
```

## Übungsaufgaben

### Übung 1: Referenzen zählen (Leicht)

Schreibe Code, der demonstriert wie sich der Reference Count ändert wenn:
- Eine Variable erstellt wird
- Eine zweite Variable auf das gleiche Objekt zeigt  
- Eine Variable gelöscht wird
- Das Objekt in einer Liste gespeichert wird

**Erwarteter Output:**
```python
# Nutze sys.getrefcount() und zeige Reference Count nach jedem Schritt
```

### Übung 2: Memory-Größe vergleichen (Mittel)

Erstelle eine Funktion `vergleiche_größe(obj1, obj2)`, die die Speichergröße zweier Objekte vergleicht und einen Bericht ausgibt.

**Erwarteter Output:**
```python
vergleiche_größe([1, 2, 3], (1, 2, 3))
# Liste: 80 Bytes
# Tuple: 64 Bytes
# Unterschied: 16 Bytes (20.0%)
# Tuple ist 20.0% kleiner
```

### Übung 3: Shallow vs Deep Copy Test (Mittel)

Erstelle eine verschachtelte Struktur und demonstriere den Unterschied zwischen shallow und deep copy mit konkreten Beispielen.

**Erwarteter Output:**
```python
# Zeige, dass shallow copy innere Objekte teilt
# Zeige, dass deep copy alles kopiert
# Nutze id() um zu beweisen, dass Objekte gleich/verschieden sind
```

### Übung 4: Memory Leak Simulator (Schwer)

Schreibe zwei Versionen einer Funktion:
1. Eine mit Memory Leak (speichert Daten in globaler Variable)
2. Eine ohne Memory Leak (säubert korrekt auf)

Nutze `tracemalloc` um den Unterschied zu messen.

**Erwarteter Output:**
```python
# Version mit Leak: Speicher steigt kontinuierlich
# Version ohne Leak: Speicher bleibt konstant
```

### Übung 5: Weak Reference Cache (Schwer)

Implementiere einen Cache der Weak References nutzt, sodass Objekte automatisch aus dem Cache entfernt werden wenn sie nicht mehr gebraucht werden.

**Anforderungen:**
- `set(key, value)` - Speichert Wert
- `get(key)` - Holt Wert oder None
- Werte werden automatisch entfernt wenn keine starken Referenzen mehr existieren

**Erwarteter Output:**
```python
cache = WeakCache()
obj = [1, 2, 3]
cache.set("data", obj)
print(cache.get("data"))  # [1, 2, 3]

del obj
print(cache.get("data"))  # None (automatisch entfernt!)
```

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [Memory Management](https://docs.python.org/3/c-api/memory.html) - CPython Memory Management
- [gc Module](https://docs.python.org/3/library/gc.html) - Garbage Collection Interface
- [sys.getsizeof()](https://docs.python.org/3/library/sys.html#sys.getsizeof) - Objektgröße ermitteln
- [weakref Module](https://docs.python.org/3/library/weakref.html) - Weak References
- [tracemalloc Module](https://docs.python.org/3/library/tracemalloc.html) - Memory Profiling
- [copy Module](https://docs.python.org/3/library/copy.html) - Shallow und Deep Copy

## Zusammenfassung

### Wichtigste Konzepte

1. **Objekte und Referenzen:**
   - Variablen sind Referenzen, keine Werte
   - Mehrere Variablen können auf gleiches Objekt zeigen
   - `id()` gibt Speicheradresse zurück
   - `is` prüft Objekt-Identität

2. **Reference Counting:**
   - Jedes Objekt zählt seine Referenzen
   - Bei refcount = 0 wird Objekt gelöscht
   - `sys.getrefcount()` zeigt Reference Count

3. **Garbage Collection:**
   - Automatische Speicherverwaltung
   - Löst zyklische Referenzen auf
   - Generational GC für Effizienz
   - `gc` Modul für manuelle Kontrolle

4. **Memory Leaks vermeiden:**
   - ✅ Keine unnötigen globalen Variablen
   - ✅ Weak References für Caches
   - ✅ Closures mit Bedacht nutzen
   - ✅ Generatoren für große Daten

5. **Performance:**
   - Shallow Copy: schnell, aber teilt innere Objekte
   - Deep Copy: langsam, aber komplett unabhängig
   - Object Pooling für häufige Objekte
   - Memory Profiling mit `tracemalloc`

### Best Practices

```python
# ✅ Nutze lokale Variablen
def verarbeite():
    daten = []  # Wird automatisch gelöscht
    return ergebnis

# ✅ Explizite Kopien wenn nötig
kopie = original.copy()

# ✅ Weak References für Caches
cache = weakref.WeakValueDictionary()

# ✅ Generatoren für große Datenmengen
def daten_generator():
    for item in große_quelle:
        yield verarbeite(item)

# ✅ Context Manager für Cleanup
with MemoryContext() as ctx:
    # Nutze Ressourcen
    pass  # Automatisches Cleanup
```

## Verwandte Themen

- [[02_Mutability vs Immutability|Mutability]] - Veränderbare vs. unveränderbare Objekte
- [[01_Python Typ-System|Typ-System]] - Wie Typen organisiert sind
- [[02_Sequenzen/01_Listen/01_List Basics|Listen]] - Mutable Sequenzen im Detail
- [[02_Sequenzen/04_Comprehensions/02_Generator Expressions|Generatoren]] - Speichereffiziente Iteration
- [[06_Praxis/02_Performance/02_Memory Profiling|Memory Profiling]] - Fortgeschrittene Techniken

---

← [[02_Mutability vs Immutability|Mutability vs Immutability]] | [[INDEX|📑 Index]] | [[01_Grundlagen/02_Numerische_Typen/01_Integer (int)|Integer (int)]] →