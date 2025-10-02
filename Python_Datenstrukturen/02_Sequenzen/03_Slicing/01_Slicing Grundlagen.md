---
tags: [python, datenstrukturen, slicing, sequenzen]
created: 2025-10-01
---

← [[02_Sequenzen/02_Tupel/04_Übungen Tupel|Übungen Tupel]] | [[INDEX|📑 Index]] | [[02_Sequenzen/03_Slicing/02_Erweiterte Slicing Techniken|Erweiterte Slicing Techniken]] →

# Slicing Grundlagen - Teilsequenzen extrahieren

> [!tip] Lernziel
> Nach dieser Seite verstehst du die Slicing-Syntax vollständig, kannst Teilsequenzen effizient extrahieren und kennst alle Parameter (start, stop, step). Slicing ist eine der pythonischsten und mächtigsten Techniken.

## Was ist Slicing?

**Slicing** ist eine Technik zum Extrahieren von Teilsequenzen aus Sequenzen (Listen, Tupeln, Strings, etc.). Es verwendet die Syntax `[start:stop:step]` und ist extrem vielseitig und effizient.

**Hauptmerkmale:**
- ✅ **Leistbar** - Funktioniert mit allen Sequenztypen
- ✅ **Effizient** - Optimiert in C implementiert
- ✅ **Lesbar** - Klare und pythonische Syntax
- ✅ **Flexibel** - Positive, negative Indizes, verschiedene Schritte
- ✅ **Sicher** - IndexError wird vermieden

```python
# Einfaches Beispiel
numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

# Slice von Index 2 bis 5 (exklusiv)
subset = numbers[2:5]
print(subset)  # [2, 3, 4]

# Mit Strings funktioniert es genauso
text = "Python"
print(text[0:4])  # "Pyth"
```

---

## Die Slicing-Syntax

### Grundform: [start:stop]

```python
sequence[start:stop]
```

- **start**: Startindex (inklusiv)
- **stop**: Endindex (exklusiv)
- **Ergebnis**: Elemente von start bis (stop - 1)

```python
numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

# Index:    0  1  2  3  4  5  6  7  8  9
# Werte:   [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

print(numbers[2:5])    # [2, 3, 4] - Index 2, 3, 4
print(numbers[0:3])    # [0, 1, 2] - Index 0, 1, 2
print(numbers[5:8])    # [5, 6, 7] - Index 5, 6, 7

# Mit Strings
text = "Python"
print(text[0:4])  # "Pyth"
print(text[2:6])  # "thon"
```

**Wichtig: Stop ist exklusiv!**
```python
# Warum ist stop exklusiv?
numbers = [0, 1, 2, 3, 4, 5]

# Diese Slices ergänzen sich perfekt:
first_half = numbers[0:3]   # [0, 1, 2]
second_half = numbers[3:6]  # [3, 4, 5]

# Zusammen ergeben sie das Original
print(first_half + second_half)  # [0, 1, 2, 3, 4, 5]

# Länge des Slices ist stop - start
slice_result = numbers[2:7]
print(len(slice_result))  # 5 = 7 - 2
```

---

## Weggelassene Parameter

### Start weglassen - Vom Anfang

```python
numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

# [:stop] = Vom Anfang bis stop
print(numbers[:5])     # [0, 1, 2, 3, 4] - gleich wie [0:5]
print(numbers[:3])     # [0, 1, 2] - gleich wie [0:3]
print(numbers[:0])     # [] - leere Liste

# Mit Strings
text = "Python"
print(text[:4])  # "Pyth"
```

### Stop weglassen - Bis zum Ende

```python
numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

# [start:] = Von start bis Ende
print(numbers[5:])     # [5, 6, 7, 8, 9]
print(numbers[3:])     # [3, 4, 5, 6, 7, 8, 9]
print(numbers[0:])     # [0, 1, 2, 3, 4, 5, 6, 7, 8, 9] - komplette Kopie

# Mit Strings
text = "Python"
print(text[2:])  # "thon"
```

### Beide weglassen - Vollständige Kopie

```python
numbers = [0, 1, 2, 3, 4, 5]

# [:] = Komplette Kopie
copy = numbers[:]
print(copy)  # [0, 1, 2, 3, 4, 5]

# Es ist eine KOPIE, nicht das Original
copy[0] = 999
print(numbers)  # [0, 1, 2, 3, 4, 5] - unverändert!
print(copy)     # [999, 1, 2, 3, 4, 5]

# Praktische Anwendung: Kopie für Iteration
for item in numbers[:]:  # Iteriere über Kopie
    if item % 2 == 0:
        numbers.remove(item)  # Ändere Original
print(numbers)  # [1, 3, 5]
```

---

## Negative Indizes

### Negative Start/Stop Werte

```python
numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

# Positive Indizes:  0  1  2  3  4  5  6  7  8  9
# Negative Indizes: -10 -9 -8 -7 -6 -5 -4 -3 -2 -1

# Negativ = Von hinten zählen
print(numbers[-3:])      # [7, 8, 9] - letzte 3 Elemente
print(numbers[-5:-2])    # [5, 6, 7] - von -5 bis -2
print(numbers[:-3])      # [0, 1, 2, 3, 4, 5, 6] - bis -3 (exklusiv)

# Letzte n Elemente
n = 4
last_n = numbers[-n:]
print(last_n)  # [6, 7, 8, 9]

# Alle außer letzte n Elemente
all_but_last_n = numbers[:-n]
print(all_but_last_n)  # [0, 1, 2, 3, 4, 5]
```

### Kombinationen positiv/negativ

```python
numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

# Positive start, negative stop
print(numbers[2:-2])     # [2, 3, 4, 5, 6, 7]

# Negative start, positive stop
print(numbers[-5:8])     # [5, 6, 7]

# Beide negativ
print(numbers[-8:-3])    # [2, 3, 4, 5, 6]

# Praktisches Beispiel: Mittleren Teil extrahieren
text = "***Hello***"
middle = text[3:-3]
print(middle)  # "Hello"
```

---

## Der Step-Parameter

### Vollständige Syntax: [start:stop:step]

```python
sequence[start:stop:step]
```

- **step**: Schrittweite (default = 1)
- Positiv: Vorwärts durch Sequenz
- Negativ: Rückwärts durch Sequenz

```python
numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

# step = 2: Jedes zweite Element
print(numbers[::2])      # [0, 2, 4, 6, 8] - gerade Indizes
print(numbers[1::2])     # [1, 3, 5, 7, 9] - ungerade Indizes

# step = 3: Jedes dritte Element
print(numbers[::3])      # [0, 3, 6, 9]

# Mit start und stop
print(numbers[1:8:2])    # [1, 3, 5, 7] - von 1 bis 8, Schritt 2

# Mit negativem Step
print(numbers[::1])      # [0, 1, 2, 3, 4, 5, 6, 7, 8, 9] - normal
print(numbers[::-1])     # [9, 8, 7, 6, 5, 4, 3, 2, 1, 0] - umgekehrt!
```

### Negativer Step - Rückwärts

```python
numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

# step = -1: Umkehren
reversed_nums = numbers[::-1]
print(reversed_nums)  # [9, 8, 7, 6, 5, 4, 3, 2, 1, 0]

# step = -2: Jedes zweite von hinten
print(numbers[::-2])  # [9, 7, 5, 3, 1]

# Mit start und stop (bei negativem step)
# WICHTIG: start muss größer als stop sein!
print(numbers[8:2:-1])   # [8, 7, 6, 5, 4, 3] - rückwärts von 8 bis 3

# Strings umkehren - klassisches Python-Idiom
text = "Python"
reversed_text = text[::-1]
print(reversed_text)  # "nohtyP"

# Palindrom-Check
word = "radar"
is_palindrome = word == word[::-1]
print(is_palindrome)  # True
```

---

## Out-of-Bounds Verhalten

### Slicing ist fehlerverzeihend

```python
numbers = [0, 1, 2, 3, 4, 5]

# Regulärer Index: IndexError
try:
    print(numbers[10])  # IndexError
except IndexError as e:
    print(f"Fehler: {e}")

# Slicing: KEIN Error!
print(numbers[10:20])   # [] - leere Liste
print(numbers[3:100])   # [3, 4, 5] - bis Ende
print(numbers[-100:3])  # [0, 1, 2] - vom Anfang bis 3

# Praktisch für sichere Extraktion
def safe_first_n(lst, n):
    """Gibt erste n Elemente zurück, auch wenn Liste kürzer"""
    return lst[:n]

print(safe_first_n([1, 2, 3], 5))  # [1, 2, 3] - kein Error!
print(safe_first_n([1, 2, 3], 2))  # [1, 2]
```

### Leere Slices

```python
numbers = [0, 1, 2, 3, 4, 5]

# Verschiedene Wege zu leeren Slices
print(numbers[5:5])    # [] - start == stop
print(numbers[5:2])    # [] - start > stop (positiver step)
print(numbers[10:20])  # [] - beide out of bounds
print(numbers[2:2:5])  # [] - start == stop

# Mit negativem Index
print(numbers[-1:-1])  # []
```

---

## Slicing mit verschiedenen Typen

### Listen

```python
numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

# Slicing erstellt NEUE Liste
subset = numbers[2:7]
print(id(numbers) == id(subset))  # False - verschiedene Objekte

# Original bleibt unverändert
subset[0] = 999
print(numbers)  # [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
print(subset)   # [999, 3, 4, 5, 6]
```

### Tupel

```python
numbers = (0, 1, 2, 3, 4, 5, 6, 7, 8, 9)

# Slicing gibt NEUES Tupel zurück
subset = numbers[2:7]
print(type(subset))  # <class 'tuple'>
print(subset)        # (2, 3, 4, 5, 6)

# Auch bei vollständiger Kopie
copy = numbers[:]
print(id(numbers) == id(copy))  # False - neues Tupel
```

### Strings

```python
text = "Python Programming"

# Slicing gibt NEUEN String zurück
print(text[0:6])      # "Python"
print(text[7:])       # "Programming"
print(text[::2])      # "Pto rgamn"
print(text[::-1])     # "gnimmargorP nohtyP"

# Strings sind unveränderlich - Slicing erstellt immer neu
word = "Hello"
# word[0] = "h"  # TypeError - geht nicht
new_word = "h" + word[1:]  # Aber Slicing + Konkatenation geht
print(new_word)  # "hello"
```

### Range Objekte

```python
# Range unterstützt Slicing (ab Python 3.6+)
r = range(10)

# Slicing gibt NEUES Range-Objekt zurück
subset = r[2:7]
print(subset)       # range(2, 7)
print(list(subset)) # [2, 3, 4, 5, 6]

# Mit Step
every_second = r[::2]
print(list(every_second))  # [0, 2, 4, 6, 8]

# Umgekehrt
reversed_range = r[::-1]
print(list(reversed_range))  # [9, 8, 7, 6, 5, 4, 3, 2, 1, 0]
```

---

## Praktische Anwendungen

### Anwendungsfall 1: String-Manipulation

```python
# Präfix/Suffix entfernen
filename = "document.txt"
name = filename[:-4]      # "document"
extension = filename[-4:]  # ".txt"

# Padding entfernen
text = "   Hello World   "
# text.strip() ist besser, aber als Beispiel:
start = 0
while text[start] == ' ':
    start += 1
end = len(text)
while text[end-1] == ' ':
    end -= 1
trimmed = text[start:end]
print(f"'{trimmed}'")  # 'Hello World'

# Oder einfacher mit strip():
print(f"'{text.strip()}'")  # 'Hello World'

# Wörter extrahieren
sentence = "Python is awesome"
words = []
start = 0
for i, char in enumerate(sentence + ' '):
    if char == ' ':
        words.append(sentence[start:i])
        start = i + 1
print(words)  # ['Python', 'is', 'awesome']

# Oder mit split():
print(sentence.split())  # ['Python', 'is', 'awesome']
```

### Anwendungsfall 2: Listen-Operationen

```python
# Erstes und letztes Element entfernen
numbers = [1, 2, 3, 4, 5]
inner = numbers[1:-1]
print(inner)  # [2, 3, 4]

# Liste in Chunks teilen
data = list(range(20))
chunk_size = 5

chunks = []
for i in range(0, len(data), chunk_size):
    chunk = data[i:i + chunk_size]
    chunks.append(chunk)

print(chunks)
# [[0, 1, 2, 3, 4], [5, 6, 7, 8, 9], [10, 11, 12, 13, 14], [15, 16, 17, 18, 19]]

# Sliding Window
def sliding_window(lst, window_size):
    """Generiert alle Fenster der Größe window_size"""
    for i in range(len(lst) - window_size + 1):
        yield lst[i:i + window_size]

numbers = [1, 2, 3, 4, 5, 6]
for window in sliding_window(numbers, 3):
    print(window)
# [1, 2, 3]
# [2, 3, 4]
# [3, 4, 5]
# [4, 5, 6]
```

### Anwendungsfall 3: Daten-Extraktion

```python
# CSV-ähnliche Daten
csv_line = "Name,Age,City,Country"
fields = csv_line.split(',')
print(fields)  # ['Name', 'Age', 'City', 'Country']

# Erste n Spalten
first_two = fields[:2]
print(first_two)  # ['Name', 'Age']

# Letzte n Spalten
last_two = fields[-2:]
print(last_two)  # ['City', 'Country']

# Ohne erste und letzte Spalte
middle = fields[1:-1]
print(middle)  # ['Age', 'City']

# Log-Zeilen verarbeiten
log_line = "[2025-10-01 14:30:15] INFO: User logged in"
timestamp = log_line[1:20]      # "2025-10-01 14:30:15"
level = log_line[22:26]         # "INFO"
message = log_line[28:]         # "User logged in"

print(f"Zeit: {timestamp}")
print(f"Level: {level}")
print(f"Nachricht: {message}")
```

### Anwendungsfall 4: Sequenz-Umkehrung

```python
# String umkehren
text = "Hello"
reversed_text = text[::-1]
print(reversed_text)  # "olleH"

# Liste umkehren (Kopie)
numbers = [1, 2, 3, 4, 5]
reversed_nums = numbers[::-1]
print(reversed_nums)  # [5, 4, 3, 2, 1]
print(numbers)        # [1, 2, 3, 4, 5] - Original unverändert

# In-place umkehren
numbers.reverse()
print(numbers)  # [5, 4, 3, 2, 1]

# Palindrom-Check
def is_palindrome(text):
    # Groß-/Kleinschreibung ignorieren, Leerzeichen entfernen
    clean = ''.join(text.lower().split())
    return clean == clean[::-1]

print(is_palindrome("A man a plan a canal Panama"))  # True
print(is_palindrome("Hello"))  # False
```

### Anwendungsfall 5: Muster extrahieren

```python
# Jeden n-ten Wert
numbers = list(range(20))

# Jedes 2. Element
evens = numbers[::2]
odds = numbers[1::2]
print(f"Gerade Indizes: {evens}")  # [0, 2, 4, 6, 8, 10, 12, 14, 16, 18]
print(f"Ungerade Indizes: {odds}")  # [1, 3, 5, 7, 9, 11, 13, 15, 17, 19]

# Jedes 3. Element
every_third = numbers[::3]
print(f"Jedes 3.: {every_third}")  # [0, 3, 6, 9, 12, 15, 18]

# RGB-Pixel extrahieren
pixels = [255, 0, 0, 0, 255, 0, 0, 0, 255, 128, 128, 128]
# Format: [R, G, B, R, G, B, ...]

red_values = pixels[0::3]
green_values = pixels[1::3]
blue_values = pixels[2::3]

print(f"Rot: {red_values}")      # [255, 0, 0, 128]
print(f"Grün: {green_values}")   # [0, 255, 0, 128]
print(f"Blau: {blue_values}")    # [0, 0, 255, 128]
```

---

## Häufige Fallstricke

### ⚠️ Fallstrick 1: Stop ist exklusiv

```python
numbers = [0, 1, 2, 3, 4, 5]

# ❌ Missverständnis: "Bis Index 3"
# Denkt man an Element bei Index 3, aber:
print(numbers[0:3])  # [0, 1, 2] - Index 3 ist NICHT enthalten!

# ✅ Richtig: "Bis vor Index 3"
# Um Element bei Index 3 zu bekommen:
print(numbers[0:4])  # [0, 1, 2, 3]
```

### ⚠️ Fallstrick 2: Negativer Step mit Start/Stop

```python
numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

# ❌ Start < Stop mit negativem Step → leere Liste
print(numbers[2:8:-1])  # [] - funktioniert nicht!

# ✅ Start > Stop mit negativem Step
print(numbers[8:2:-1])  # [8, 7, 6, 5, 4, 3]

# Merkhilfe: Richtung des Steps beachten!
# Positiver Step: start → stop (vorwärts)
# Negativer Step: start → stop (rückwärts)
```

### ⚠️ Fallstrick 3: Modifikation der Original-Sequenz

```python
# Listen: Slice ist KOPIE
numbers = [1, 2, 3, 4, 5]
subset = numbers[1:4]
subset[0] = 999

print(numbers)  # [1, 2, 3, 4, 5] - unverändert ✅
print(subset)   # [999, 3, 4] - geändert

# ABER: Bei verschachtelten Listen
matrix = [[1, 2], [3, 4], [5, 6]]
subset = matrix[0:2]  # [[1, 2], [3, 4]]

# Äußere Liste ist Kopie, innere Listen sind Referenzen!
subset[0][0] = 999

print(matrix)  # [[999, 2], [3, 4], [5, 6]] - auch geändert! ⚠️
print(subset)  # [[999, 2], [3, 4]]

# Lösung: Deep Copy
import copy
subset = copy.deepcopy(matrix[0:2])
```

### ⚠️ Fallstrick 4: Leere Slices verstehen

```python
numbers = [0, 1, 2, 3, 4, 5]

# Verschiedene Wege zu leeren Listen
print(numbers[3:3])   # []
print(numbers[5:2])   # [] - start > stop
print(numbers[10:20]) # [] - out of bounds

# Kann verwirrend sein bei Berechnungen
n = len(numbers)
print(numbers[n:n+5]) # [] - nicht [n, n+1, ...]!
```

---

## Performance-Hinweise

### Slicing ist effizient

```python
import time

# Große Liste
big_list = list(range(1000000))

# Slicing ist schnell
start = time.perf_counter()
subset = big_list[::2]  # Jedes 2. Element = 500k Elemente
time_slice = time.perf_counter() - start

# List Comprehension zum Vergleich
start = time.perf_counter()
subset = [big_list[i] for i in range(0, len(big_list), 2)]
time_comp = time.perf_counter() - start

print(f"Slicing:       {time_slice:.4f}s")
print(f"Comprehension: {time_comp:.4f}s")
print(f"Faktor: {time_comp/time_slice:.1f}x")

# Slicing ist oft 2-3x schneller!
```

### Wann Slicing verwenden?

**✅ Slicing ist gut für:**
- Teilsequenzen extrahieren
- Kopien erstellen
- Umkehren (`::-1`)
- Muster extrahieren (Step-Parameter)
- Out-of-Bounds tolerantes Zugreifen

**❌ Vermeiden bei:**
- Komplexen Bedingungen → List Comprehension
- Modifikation während Iteration → Kopie mit `[:]`
- Verschachtelten Strukturen → `copy.deepcopy()`

---

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [Sequence Types](https://docs.python.org/3/library/stdtypes.html#sequence-types-list-tuple-range) - Slicing für alle Sequenzen
- [Slicing Tutorial](https://docs.python.org/3/tutorial/introduction.html#strings) - Grundlagen
- [Data Model - Slicing](https://docs.python.org/3/reference/datamodel.html#the-standard-type-hierarchy) - Technische Details

---

## Verwandte Themen

- [[02_Sequenzen/03_Slicing/02_Erweiterte Slicing Techniken|Erweiterte Slicing Techniken]] - Slice-Objekte, Assignment
- [[02_Sequenzen/03_Slicing/03_Slice Objekte|Slice Objekte]] - `slice()` Funktion
- [[02_Sequenzen/01_Listen/01_List Basics|List Basics]] - Listen und Slicing
- [[02_Sequenzen/02_Tupel/01_Tuple Basics|Tuple Basics]] - Tupel und Slicing

---

← [[02_Sequenzen/02_Tupel/04_Übungen Tupel|Übungen Tupel]] | [[INDEX|📑 Index]] | [[02_Sequenzen/03_Slicing/02_Erweiterte Slicing Techniken|Erweiterte Slicing Techniken]] →