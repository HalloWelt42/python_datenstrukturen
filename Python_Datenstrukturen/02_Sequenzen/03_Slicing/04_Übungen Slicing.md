---
tags: [python, datenstrukturen, slicing, übungen]
created: 2025-10-01
---

← [[02_Sequenzen/03_Slicing/03_Slice Objekte|Slice Objekte]] | [[INDEX|📑 Index]] | [[02_Sequenzen/04_Comprehensions/01_List Comprehensions|List Comprehensions]] →

# Übungen: Slicing

> [!tip] Lernziel
> Diese Übungen festigen dein Verständnis von Slicing in allen Facetten: Grundlagen, erweiterte Techniken, Slice Assignment, Slice-Objekte und praktische Anwendungen.

## 📝 Übungsaufgaben

### Übung 1: Slicing Basics - String Manipulation (Leicht)

Implementiere verschiedene String-Operationen mit Slicing.

**Anforderungen:**
- `get_extension(filename)` - Extrahiert Dateiendung
- `remove_extension(filename)` - Entfernt Dateiendung
- `get_first_n_chars(text, n)` - Erste n Zeichen
- `get_last_n_chars(text, n)` - Letzte n Zeichen
- `reverse_string(text)` - Kehrt String um

**Beispiel:**
```python
def get_extension(filename):
    # Dein Code hier
    pass

def remove_extension(filename):
    # Dein Code hier
    pass

def get_first_n_chars(text, n):
    # Dein Code hier
    pass

def get_last_n_chars(text, n):
    # Dein Code hier
    pass

def reverse_string(text):
    # Dein Code hier
    pass

# Test
filename = "document.pdf"
print(f"Extension: {get_extension(filename)}")
print(f"Ohne Extension: {remove_extension(filename)}")

text = "Python"
print(f"Erste 3: {get_first_n_chars(text, 3)}")
print(f"Letzte 3: {get_last_n_chars(text, 3)}")
print(f"Umgekehrt: {reverse_string(text)}")
```

**Erwarteter Output:**
```
Extension: .pdf
Ohne Extension: document
Erste 3: Pyt
Letzte 3: hon
Umgekehrt: nohtyP
```

<details>
<summary>💡 Hinweis</summary>

- Extension: Suche letzten Punkt mit `.rfind()`, dann slice ab dieser Position
- Remove: Slice bis zum letzten Punkt
- First n: `text[:n]`
- Last n: `text[-n:]`
- Reverse: `text[::-1]`
</details>

---

### Übung 2: List Slicing - Chunking (Leicht-Mittel)

Teile Listen in Chunks (Blöcke) fester Größe.

**Anforderungen:**
- `chunk_list(lst, size)` - Teilt Liste in Chunks
- Letzter Chunk kann kleiner sein
- Gibt Liste von Chunks zurück

**Beispiel:**
```python
def chunk_list(lst, size):
    # Dein Code hier
    pass

# Test
numbers = list(range(17))
chunks = chunk_list(numbers, 5)
print(f"Anzahl Chunks: {len(chunks)}")
for i, chunk in enumerate(chunks, 1):
    print(f"Chunk {i}: {chunk}")
```

**Erwarteter Output:**
```
Anzahl Chunks: 4
Chunk 1: [0, 1, 2, 3, 4]
Chunk 2: [5, 6, 7, 8, 9]
Chunk 3: [10, 11, 12, 13, 14]
Chunk 4: [15, 16]
```

<details>
<summary>💡 Hinweis</summary>

- Iteriere mit `range(0, len(lst), size)`
- Für jeden Start-Index: `lst[i:i+size]`
- Slicing behandelt automatisch Ende der Liste
</details>

---

### Übung 3: Slice Assignment - Matrix Operations (Mittel)

Implementiere Matrix-Operationen mit Slice Assignment.

**Anforderungen:**
- `set_row(matrix, row_index, values)` - Setzt Zeile
- `set_column(matrix, col_index, values)` - Setzt Spalte
- `set_diagonal(matrix, values)` - Setzt Hauptdiagonale
- Alle Operationen in-place

**Beispiel:**
```python
def set_row(matrix, row_index, values):
    # Dein Code hier
    pass

def set_column(matrix, col_index, values):
    # Dein Code hier
    pass

def set_diagonal(matrix, values):
    # Dein Code hier
    pass

# Test
matrix = [
    [0, 0, 0, 0],
    [0, 0, 0, 0],
    [0, 0, 0, 0],
    [0, 0, 0, 0]
]

set_row(matrix, 1, [1, 2, 3, 4])
print("Nach set_row(1):")
for row in matrix:
    print(row)

set_column(matrix, 2, [10, 20, 30, 40])
print("\nNach set_column(2):")
for row in matrix:
    print(row)

set_diagonal(matrix, [100, 200, 300, 400])
print("\nNach set_diagonal:")
for row in matrix:
    print(row)
```

**Erwarteter Output:**
```
Nach set_row(1):
[0, 0, 0, 0]
[1, 2, 3, 4]
[0, 0, 0, 0]
[0, 0, 0, 0]

Nach set_column(2):
[0, 0, 10, 0]
[1, 2, 30, 4]
[0, 0, 30, 0]
[0, 0, 40, 0]

Nach set_diagonal:
[100, 0, 10, 0]
[1, 200, 30, 4]
[0, 0, 300, 0]
[0, 0, 40, 400]
```

<details>
<summary>💡 Hinweis</summary>

- Row: `matrix[row_index][:] = values` (Slice Assignment!)
- Column: Schleife über Zeilen, setze `matrix[i][col_index]`
- Diagonal: Schleife, setze `matrix[i][i]`
</details>

---

### Übung 4: Negative Step - Palindrome und Patterns (Mittel)

Arbeite mit negativem Step für verschiedene Muster.

**Anforderungen:**
- `is_palindrome(text)` - Prüft Palindrom (ignoriert Leerzeichen/Groß-Klein)
- `every_second_reversed(lst)` - Jedes zweite Element rückwärts
- `interleave_forward_backward(lst)` - Verzahnt vorwärts/rückwärts

**Beispiel:**
```python
def is_palindrome(text):
    # Dein Code hier
    pass

def every_second_reversed(lst):
    # Dein Code hier
    pass

def interleave_forward_backward(lst):
    # Dein Code hier
    pass

# Test
print(is_palindrome("A man a plan a canal Panama"))  # True
print(is_palindrome("Hello"))  # False

numbers = [1, 2, 3, 4, 5, 6, 7, 8]
print(f"Jedes 2. rückwärts: {every_second_reversed(numbers)}")

numbers = [1, 2, 3, 4, 5]
print(f"Verzahnt: {interleave_forward_backward(numbers)}")
```

**Erwarteter Output:**
```
True
False
Jedes 2. rückwärts: [8, 6, 4, 2]
Verzahnt: [1, 5, 2, 4, 3]
```

<details>
<summary>💡 Hinweis</summary>

- Palindrome: `clean = ''.join(text.lower().split())`; dann `clean == clean[::-1]`
- Every second reversed: `lst[::-2]` oder `lst[-1::-2]`
- Interleave: Kombiniere `lst[::2]` und `lst[-1::-2]` mit zip
</details>

---

### Übung 5: Benannte Slices - Log Parser (Mittel-Schwer)

Erstelle einen Log-Parser mit benannten Slice-Objekten.

**Anforderungen:**
- Definiere Slices für Log-Format: `[YYYY-MM-DD HH:MM:SS] LEVEL: Message`
- `parse_log_line(line)` - Gibt Dictionary zurück
- `filter_by_level(logs, level)` - Filtert nach Level
- `get_messages_in_timerange(logs, start_time, end_time)` - Filtert nach Zeit

**Beispiel:**
```python
# Definiere Slice-Konstanten
TIMESTAMP = slice(1, 20)
LEVEL = slice(22, 27)
MESSAGE = slice(29, None)

def parse_log_line(line):
    # Dein Code hier
    pass

def filter_by_level(logs, level):
    # Dein Code hier
    pass

def get_messages_in_timerange(logs, start_time, end_time):
    # Dein Code hier
    pass

# Test
logs = [
    "[2025-10-01 10:30:15] INFO : Server started",
    "[2025-10-01 10:30:20] DEBUG: Loading config",
    "[2025-10-01 10:30:25] ERROR: Connection failed",
    "[2025-10-01 10:30:30] INFO : Request received"
]

# Parse alle
for log in logs:
    parsed = parse_log_line(log)
    print(parsed)

# Filter
errors = filter_by_level(logs, "ERROR")
print(f"\nErrors: {len(errors)}")

# Zeitbereich
messages = get_messages_in_timerange(logs, "10:30:20", "10:30:30")
print(f"Messages im Zeitraum: {messages}")
```

**Erwarteter Output:**
```
{'timestamp': '2025-10-01 10:30:15', 'level': 'INFO ', 'message': 'Server started'}
{'timestamp': '2025-10-01 10:30:20', 'level': 'DEBUG', 'message': 'Loading config'}
{'timestamp': '2025-10-01 10:30:25', 'level': 'ERROR', 'message': 'Connection failed'}
{'timestamp': '2025-10-01 10:30:30', 'level': 'INFO ', 'message': 'Request received'}

Errors: 1
Messages im Zeitraum: ['Loading config', 'Connection failed']
```

<details>
<summary>💡 Hinweis</summary>

- Parse: Verwende definierte Slices, `.strip()` für saubere Werte
- Filter: List comprehension mit `parse_log_line` und Level-Check
- Timerange: Extrahiere Zeit aus Timestamp (z.B. `[11:19]`), vergleiche
</details>

---

### Übung 6: Slice Assignment - In-Place Transformationen (Schwer)

Implementiere verschiedene In-Place-Transformationen mit Slice Assignment.

**Anforderungen:**
- `rotate_inplace(lst, n)` - Rotiert Liste in-place
- `reverse_segments(lst, segment_size)` - Kehrt Segmente um
- `merge_sorted_inplace(lst, mid)` - Merged zwei sortierte Hälften

**Beispiel:**
```python
def rotate_inplace(lst, n):
    """Rotiert Liste n Positionen nach rechts (in-place)"""
    # Dein Code hier
    pass

def reverse_segments(lst, segment_size):
    """Kehrt jedes Segment der Größe segment_size um (in-place)"""
    # Dein Code hier
    pass

def merge_sorted_inplace(lst, mid):
    """Merged zwei sortierte Hälften [0:mid] und [mid:] (in-place)"""
    # Dein Code hier
    pass

# Test Rotate
numbers = [1, 2, 3, 4, 5]
rotate_inplace(numbers, 2)
print(f"Rotiert: {numbers}")

# Test Reverse Segments
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9]
reverse_segments(numbers, 3)
print(f"Segments umgekehrt: {numbers}")

# Test Merge
numbers = [1, 3, 5, 7, 2, 4, 6, 8]  # Zwei sortierte Hälften
merge_sorted_inplace(numbers, 4)
print(f"Gemerged: {numbers}")
```

**Erwarteter Output:**
```
Rotiert: [4, 5, 1, 2, 3]
Segments umgekehrt: [3, 2, 1, 6, 5, 4, 9, 8, 7]
Gemerged: [1, 2, 3, 4, 5, 6, 7, 8]
```

<details>
<summary>💡 Hinweis</summary>

- Rotate: `n = n % len(lst)`, dann `lst[:] = lst[-n:] + lst[:-n]`
- Reverse Segments: Loop über Segmente, `lst[i:i+size][::-1]` zuweisen
- Merge: Verwende zwei Pointer, baue merged list, dann `lst[:] = merged`
</details>

---

### Übung 7: Dynamisches Slicing - Window Operations (Schwer)

Implementiere verschiedene Window-basierte Operationen.

**Anforderungen:**
- `sliding_window_max(lst, window_size)` - Maximum pro Fenster
- `sliding_window_avg(lst, window_size)` - Durchschnitt pro Fenster
- `detect_peaks(lst, window_size)` - Findet lokale Maxima
- Alle Operationen mit Slicing

**Beispiel:**
```python
def sliding_window_max(lst, window_size):
    # Dein Code hier
    pass

def sliding_window_avg(lst, window_size):
    # Dein Code hier
    pass

def detect_peaks(lst, window_size):
    """Findet Indizes wo Wert > alle im Fenster darum"""
    # Dein Code hier
    pass

# Test
prices = [10, 12, 9, 15, 8, 11, 14, 7, 16, 13]

maxima = sliding_window_max(prices, 3)
print(f"Sliding Max: {maxima}")

averages = sliding_window_avg(prices, 3)
print(f"Sliding Avg: {[f'{x:.1f}' for x in averages]}")

peaks = detect_peaks(prices, 2)
print(f"Peaks bei Indizes: {peaks}")
print(f"Peak-Werte: {[prices[i] for i in peaks]}")
```

**Erwarteter Output:**
```
Sliding Max: [12, 15, 15, 15, 14, 14, 16, 16]
Sliding Avg: ['10.3', '12.0', '10.7', '11.3', '11.0', '10.7', '12.3', '12.0']
Peaks bei Indizes: [1, 3, 6, 8]
Peak-Werte: [12, 15, 14, 16]
```

<details>
<summary>💡 Hinweis</summary>

- Sliding max: Loop über Indizes, `max(lst[i:i+window_size])`
- Sliding avg: Ähnlich, aber `sum(window) / len(window)`
- Peaks: Für jeden Index, prüfe ob größer als Nachbarn im Fenster
</details>

---

### Übung 8: Slice Objects - Konfigurierbare Extraktion (Schwer)

Erstelle einen flexiblen Daten-Extraktor mit Slice-Objekten.

**Anforderungen:**
- `FieldExtractor` Klasse
- `add_field(name, start, stop=None, step=None)` - Definiert Feld
- `extract(data)` - Extrahiert alle Felder
- `extract_field(data, field_name)` - Extrahiert einzelnes Feld
- Unterstützt verschiedene Datenformate

**Beispiel:**
```python
class FieldExtractor:
    def __init__(self):
        # Dein Code hier
        pass
    
    def add_field(self, name, start, stop=None, step=None):
        # Dein Code hier
        pass
    
    def extract(self, data):
        # Dein Code hier
        pass
    
    def extract_field(self, data, field_name):
        # Dein Code hier
        pass

# Test mit CSV fester Breite
extractor = FieldExtractor()
extractor.add_field('name', 0, 20)
extractor.add_field('age', 20, 25)
extractor.add_field('city', 25, 45)

csv_line = "Anna Schmidt        25   Berlin                    "
fields = extractor.extract(csv_line)
print(fields)

name = extractor.extract_field(csv_line, 'name')
print(f"Name: '{name.strip()}'")

# Test mit anderem Format (Binärdaten)
binary_extractor = FieldExtractor()
binary_extractor.add_field('header', 0, 4)
binary_extractor.add_field('type', 4, 5)
binary_extractor.add_field('data', 5, None)

packet = bytes([0xFF, 0xFF, 0xFF, 0xFF, 0x01, 0x48, 0x65, 0x6C, 0x6C, 0x6F])
fields = binary_extractor.extract(packet)
print(f"Header: {fields['header'].hex()}")
print(f"Type: {fields['type'][0]}")
print(f"Data: {fields['data'].decode('ascii')}")
```

**Erwarteter Output:**
```
{'name': 'Anna Schmidt        ', 'age': '25   ', 'city': 'Berlin                    '}
Name: 'Anna Schmidt'
Header: ffffffff
Type: 1
Data: Hello
```

<details>
<summary>💡 Hinweis</summary>

- Speichere Slices in Dictionary: `self.fields = {}`
- add_field: Erstelle `slice(start, stop, step)` und speichere
- extract: `{name: data[s] for name, s in self.fields.items()}`
- extract_field: Hole Slice aus Dictionary, wende auf data an
</details>

---

### Übung 9: Advanced Pattern - Two Pointers mit Slicing (Schwer)

Implementiere Two-Pointers Algorithmen mit Slicing.

**Anforderungen:**
- `remove_duplicates_sorted(lst)` - Entfernt Duplikate aus sortierter Liste
- `merge_sorted_arrays(arr1, arr2)` - Merged zwei sortierte Arrays
- `find_pair_with_sum(lst, target)` - Findet Paar mit Summe (sortierte Liste)

**Beispiel:**
```python
def remove_duplicates_sorted(lst):
    """Entfernt Duplikate aus sortierter Liste (in-place)"""
    # Dein Code hier
    pass

def merge_sorted_arrays(arr1, arr2):
    """Merged zwei sortierte Arrays"""
    # Dein Code hier
    pass

def find_pair_with_sum(lst, target):
    """Findet Paar (i, j) mit lst[i] + lst[j] == target"""
    # Dein Code hier
    pass

# Test
numbers = [1, 1, 2, 2, 2, 3, 4, 4, 5]
remove_duplicates_sorted(numbers)
print(f"Ohne Duplikate: {numbers}")

arr1 = [1, 3, 5, 7]
arr2 = [2, 4, 6, 8]
merged = merge_sorted_arrays(arr1, arr2)
print(f"Gemerged: {merged}")

numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9]
pair = find_pair_with_sum(numbers, 10)
print(f"Paar mit Summe 10: {pair}")
if pair:
    i, j = pair
    print(f"  {numbers[i]} + {numbers[j]} = {numbers[i] + numbers[j]}")
```

**Erwarteter Output:**
```
Ohne Duplikate: [1, 2, 3, 4, 5]
Gemerged: [1, 2, 3, 4, 5, 6, 7, 8]
Paar mit Summe 10: (0, 8)
  1 + 9 = 10
```

<details>
<summary>💡 Hinweis</summary>

- Remove duplicates: Verwende write-pointer, nur bei Änderung kopieren, dann `lst[:] = lst[:write_pos]`
- Merge: Two pointers in arr1 und arr2, vergleiche und füge hinzu
- Find pair: Two pointers von beiden Enden, bewege basierend auf Summe
</details>

---

## 🎯 Bonus-Challenge: Matrix Rotation mit Slicing

**Aufgabe:** Rotiere eine n×n Matrix um 90° im Uhrzeigersinn mit nur Slicing (kein Transpose + Reverse Trick).

**Anforderungen:**
- In-place Rotation (ohne extra Matrix)
- Verwende nur Slicing und Swaps
- Funktioniert für beliebige n×n Matrizen

```python
def rotate_matrix_90(matrix):
    """Rotiert n×n Matrix 90° im Uhrzeigersinn (in-place)"""
    n = len(matrix)
    
    # Dein Code hier
    # Tipp: Rotiere in Ringen von außen nach innen
    # Jeder Ring kann mit Slicing und 4-Wege-Swap rotiert werden
    
    pass

# Test
matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]

print("Original:")
for row in matrix:
    print(row)

rotate_matrix_90(matrix)

print("\nNach Rotation:")
for row in matrix:
    print(row)

# Erwartetes Ergebnis:
# [[7, 4, 1],
#  [8, 5, 2],
#  [9, 6, 3]]
```

---

## 📊 Lösungshinweise

> [!note] Selbsttest
> Versuche alle Übungen selbst zu lösen, bevor du die Lösungen ansiehst. Die Lösungen findest du in [[06_Praxis/04_Lösungen/Lösungen Teil 2|Lösungen Teil 2]].

**Bewertung deiner Lösungen:**
- ✅ Verwendet Slicing wo möglich statt Loops
- ✅ Slice Assignment für In-Place-Operationen
- ✅ Benannte Slices für Lesbarkeit
- ✅ Behandelt Edge-Cases (leere Listen, einzelnes Element)
- ✅ Effizient (vermeidet unnötige Kopien)

---

## 🔗 Weiterführende Themen

Nach diesen Übungen solltest du bereit sein für:
- [[02_Sequenzen/04_Comprehensions/01_List Comprehensions|List Comprehensions]] - Elegante Alternative zu Slicing + Loops
- [[02_Sequenzen/05_Range/01_Range Objects|Range Objects]] - Lazy Sequences
- [[04_Fortgeschritten/02_Deque/01_Deque Basics|Deque]] - Effiziente beidseitige Operationen

---

## 📚 Python-Dokumentation

**Offizielle Ressourcen:**
- [Slicing Tutorial](https://docs.python.org/3/tutorial/introduction.html#strings) - Grundlagen
- [Sequence Types](https://docs.python.org/3/library/stdtypes.html#sequence-types-list-tuple-range) - Alle Slicing-Operationen
- [slice() Built-in](https://docs.python.org/3/library/functions.html#slice) - slice() Funktion
- [Data Model](https://docs.python.org/3/reference/datamodel.html#object.__getitem__) - Slicing Internals

---

← [[02_Sequenzen/03_Slicing/03_Slice Objekte|Slice Objekte]] | [[INDEX|📑 Index]] | [[02_Sequenzen/04_Comprehensions/01_List Comprehensions|List Comprehensions]] →