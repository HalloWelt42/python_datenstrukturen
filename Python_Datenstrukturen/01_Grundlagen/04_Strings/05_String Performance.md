---
tags: [python, datenstrukturen, strings, performance, optimierung]
created: 2025-10-01
---

← [[04_Encoding und Unicode|Encoding und Unicode]] | [[INDEX|📑 Index]] | [[06_Übungen Strings|Übungen Strings]] →

# String Performance

> [!tip] Lernziel
> Du verstehst Performance-Charakteristiken von String-Operationen und kennst Optimierungsstrategien

## Strings sind Immutable

Die wichtigste Performance-Eigenschaft: **Strings sind unveränderbar**.

```python
# Jede "Änderung" erstellt ein neues String-Objekt
text = "Python"
print(id(text))  # z.B. 140234567890

text = text + " Programming"  # Neues Objekt!
print(id(text))  # Andere ID!

# Das bedeutet: Viele String-Operationen können teuer sein
```

## String-Konkatenation

### Das Problem mit +=

```python
import time

# ❌ INEFFIZIENT: += in Loop
start = time.perf_counter()
result = ""
for i in range(10000):
    result += str(i)  # Erstellt jedes Mal neuen String!
zeit1 = time.perf_counter() - start
print(f"+=: {zeit1:.4f}s")

# ✅ EFFIZIENT: Liste + join()
start = time.perf_counter()
parts = []
for i in range(10000):
    parts.append(str(i))
result = "".join(parts)
zeit2 = time.perf_counter() - start
print(f"join(): {zeit2:.4f}s")

print(f"Speedup: {zeit1/zeit2:.1f}x")
# join() ist ~100x schneller!
```

### Warum ist += langsam?

```python
# Was bei += passiert:
# Iteration 1: "" + "0" = "0" (neuer String, 1 Zeichen)
# Iteration 2: "0" + "1" = "01" (neuer String, 2 Zeichen, kopiert 1 Zeichen)
# Iteration 3: "01" + "2" = "012" (neuer String, 3 Zeichen, kopiert 2 Zeichen)
# ...
# Iteration n: Kopiert (n-1) Zeichen
# Gesamt: O(n²) Operationen!

# join() dagegen:
# Berechnet zuerst Gesamtlänge
# Allokiert einmal Speicher
# Kopiert alle Teile einmal
# Gesamt: O(n) Operationen!
```

### Konkatenation-Methoden im Vergleich

```python
import timeit

n = 1000
zahlen = [str(i) for i in range(n)]

# 1. += (langsam)
def mit_plus():
    result = ""
    for num in zahlen:
        result += num
    return result

# 2. join() (schnell)
def mit_join():
    return "".join(zahlen)

# 3. Formatierung (mittel)
def mit_format():
    return "".join("{}".format(num) for num in zahlen)

# 4. Liste + str() (langsam)
def mit_str():
    return str(zahlen)[1:-1].replace("'", "").replace(", ", "")

# Benchmarks
t1 = timeit.timeit(mit_plus, number=100)
t2 = timeit.timeit(mit_join, number=100)
t3 = timeit.timeit(mit_format, number=100)
t4 = timeit.timeit(mit_str, number=100)

print(f"+=:      {t1:.4f}s (Baseline)")
print(f"join():  {t2:.4f}s ({t1/t2:.1f}x faster)")
print(f"format: {t3:.4f}s ({t1/t3:.1f}x faster)")
print(f"str():   {t4:.4f}s ({t1/t4:.1f}x faster)")
```

## String-Operationen Komplexität

### Zeitkomplexität Übersicht

| Operation | Komplexität | Beschreibung |
|-----------|-------------|--------------|
| `len(s)` | O(1) | Länge ist gecacht |
| `s[i]` | O(1) | Direkter Index-Zugriff |
| `s[i:j]` | O(j-i) | Slice-Größe |
| `s + t` | O(len(s) + len(t)) | Neue Kopie |
| `s * n` | O(n * len(s)) | Wiederholung |
| `s in t` | O(len(s) * len(t)) | Worst case |
| `s.find(sub)` | O(len(s) * len(sub)) | Worst case |
| `s.replace(old, new)` | O(len(s)) | Linear |
| `s.split(sep)` | O(len(s)) | Linear |
| `sep.join(list)` | O(total_length) | Summe aller Längen |
| `s.startswith(pre)` | O(len(pre)) | Nur Präfix |
| `s.upper()`, `s.lower()` | O(len(s)) | Jedes Zeichen |

### Beispiele zur Komplexität

```python
import time

# len() ist O(1) - immer gleich schnell
text_kurz = "Python"
text_lang = "Python" * 100000

start = time.perf_counter()
for _ in range(1000000):
    len(text_kurz)
print(f"len(kurz): {time.perf_counter() - start:.4f}s")

start = time.perf_counter()
for _ in range(1000000):
    len(text_lang)
print(f"len(lang): {time.perf_counter() - start:.4f}s")
# Beide gleich schnell!

# in-Operator ist O(n*m) - worst case
print("\n'in' Operator:")
text = "a" * 10000 + "b"
pattern = "a" * 100 + "b"

start = time.perf_counter()
pattern in text
print(f"Zeit: {time.perf_counter() - start:.6f}s")
```

## Optimierungs-Strategien

### 1. join() statt Konkatenation

```python
# ❌ Schlecht
def build_html_bad(items):
    html = "<ul>"
    for item in items:
        html += f"<li>{item}</li>"  # Langsam!
    html += "</ul>"
    return html

# ✅ Gut
def build_html_good(items):
    parts = ["<ul>"]
    for item in items:
        parts.append(f"<li>{item}</li>")
    parts.append("</ul>")
    return "".join(parts)

# ✅ Noch besser: Generator Expression
def build_html_best(items):
    return "".join([
        "<ul>",
        *[f"<li>{item}</li>" for item in items],
        "</ul>"
    ])
```

### 2. f-strings für Formatierung

```python
import timeit

name = "Alice"
age = 30

# f-string (am schnellsten)
t1 = timeit.timeit(lambda: f"Hallo {name}, du bist {age}", number=100000)

# format()
t2 = timeit.timeit(lambda: "Hallo {}, du bist {}".format(name, age), number=100000)

# % Formatierung
t3 = timeit.timeit(lambda: "Hallo %s, du bist %d" % (name, age), number=100000)

# + Konkatenation
t4 = timeit.timeit(lambda: "Hallo " + name + ", du bist " + str(age), number=100000)

print(f"f-string: {t1:.4f}s")
print(f"format(): {t2:.4f}s ({t2/t1:.2f}x)")
print(f"%:        {t3:.4f}s ({t3/t1:.2f}x)")
print(f"+:        {t4:.4f}s ({t4/t1:.2f}x)")
```

### 3. Vorberechnete Konstanten

```python
# ❌ Wiederholte Berechnungen
def process_lines_bad(lines):
    result = []
    for line in lines:
        if line.startswith("# "):  # String jedes Mal neu erstellt
            continue
        if line.endswith("\n"):    # String jedes Mal neu erstellt
            line = line[:-1]
        result.append(line)
    return result

# ✅ Konstanten vorberechnen
def process_lines_good(lines):
    COMMENT_PREFIX = "# "
    NEWLINE = "\n"
    result = []
    for line in lines:
        if line.startswith(COMMENT_PREFIX):
            continue
        if line.endswith(NEWLINE):
            line = line[:-1]
        result.append(line)
    return result
```

### 4. Richtige Methode wählen

```python
text = "Python Programming"

# startswith() ist schneller als Slicing + Vergleich
# ✅ Schnell
if text.startswith("Python"):
    pass

# ❌ Langsamer
if text[:6] == "Python":
    pass

# in-Operator für einfache Suche
# ✅ Schnell (intern optimiert)
if "Prog" in text:
    pass

# ❌ Langsamer
if text.find("Prog") != -1:
    pass
```

### 5. String Interning nutzen

```python
import sys

# String Interning für häufig verwendete Strings
def process_tokens(tokens):
    # Ohne Interning: Viele gleiche Strings im Speicher
    unique_tokens = {}
    for token in tokens:
        if token not in unique_tokens:
            unique_tokens[token] = []
        unique_tokens[token].append(1)
    
    return unique_tokens

# ✅ Mit Interning: Strings werden wiederverwendet
def process_tokens_interned(tokens):
    unique_tokens = {}
    for token in tokens:
        # sys.intern() speichert nur eine Kopie
        interned = sys.intern(token)
        if interned not in unique_tokens:
            unique_tokens[interned] = []
        unique_tokens[interned].append(1)
    
    return unique_tokens

# Besonders nützlich bei vielen gleichen Strings
tokens = ["hello", "world"] * 10000
# Ohne intern: 20000 String-Objekte
# Mit intern: 2 String-Objekte!
```

### 6. StringBuilder-Pattern

```python
# StringBuilder für häufige Modifikationen
class StringBuilder:
    def __init__(self):
        self._strings = []
    
    def append(self, string):
        self._strings.append(string)
    
    def __str__(self):
        return "".join(self._strings)
    
    def __len__(self):
        return sum(len(s) for s in self._strings)

# Verwendung
builder = StringBuilder()
for i in range(1000):
    builder.append(str(i))
    builder.append(",")

result = str(builder)  # Nur einmal join()
```

## Memory-Optimierung

### String-Größe

```python
import sys

# Leerer String
empty = ""
print(f"Leer: {sys.getsizeof(empty)} bytes")  # 49 bytes (Overhead)

# Strings wachsen in Chunks
for length in [1, 10, 100, 1000, 10000]:
    s = "a" * length
    bytes_per_char = (sys.getsizeof(s) - 49) / length
    print(f"{length:5} Zeichen: {sys.getsizeof(s):6} bytes ({bytes_per_char:.2f} bytes/char)")

# ASCII: ~1 byte/char
# Unicode (nicht-ASCII): mehr bytes/char
```

### Memory-Leaks vermeiden

```python
# ❌ Problem: Strings in Closures
def create_processor(large_text):
    # large_text wird im Closure gespeichert!
    def processor():
        return len(large_text)  # Hält ganzen String im Speicher
    return processor

# ✅ Besser: Nur nötige Info extrahieren
def create_processor_optimized(large_text):
    length = len(large_text)  # Nur Länge speichern
    def processor():
        return length  # Kein String im Closure
    return processor

# ❌ Problem: String-Slices halten Referenz zum Original
large_string = "x" * 1000000
small_slice = large_string[0:10]  # Hält Referenz zu large_string!

# ✅ Besser: Neue Kopie erstellen
small_copy = str(large_string[0:10])  # Oder: "".join(large_string[0:10])
```

## Regex Performance

```python
import re
import time

text = "Python programming is great. Python is powerful."

# ❌ Regex kompilieren bei jeder Suche
def search_no_compile(text, n):
    start = time.perf_counter()
    for _ in range(n):
        re.findall(r"Python", text)
    return time.perf_counter() - start

# ✅ Regex einmal kompilieren
def search_with_compile(text, n):
    pattern = re.compile(r"Python")
    start = time.perf_counter()
    for _ in range(n):
        pattern.findall(text)
    return time.perf_counter() - start

n = 10000
t1 = search_no_compile(text, n)
t2 = search_with_compile(text, n)

print(f"Ohne compile: {t1:.4f}s")
print(f"Mit compile:  {t2:.4f}s ({t1/t2:.1f}x faster)")

# ✅ Noch besser: Einfache Suche ohne Regex
def search_simple(text, n):
    start = time.perf_counter()
    for _ in range(n):
        text.count("Python")
    return time.perf_counter() - start

t3 = search_simple(text, n)
print(f"count():      {t3:.4f}s ({t1/t3:.1f}x faster)")
# count() ist oft schneller als Regex für einfache Suchen!
```

## Caching-Strategien

### 1. Computed Properties Cachen

```python
class Text:
    def __init__(self, content):
        self.content = content
        self._word_count = None
        self._line_count = None
    
    # ❌ Ohne Caching: Jedes Mal neu berechnen
    # @property
    # def word_count(self):
    #     return len(self.content.split())
    
    # ✅ Mit Caching
    @property
    def word_count(self):
        if self._word_count is None:
            self._word_count = len(self.content.split())
        return self._word_count
    
    @property
    def line_count(self):
        if self._line_count is None:
            self._line_count = self.content.count('\n') + 1
        return self._line_count

text = Text("Hallo\nWelt\nPython")
print(text.word_count)  # Berechnet einmal
print(text.word_count)  # Gibt Cache zurück
```

### 2. LRU Cache für Funktionen

```python
from functools import lru_cache

# Teure String-Operation
@lru_cache(maxsize=128)
def normalize_text(text):
    """Normalisiert Text (teuer bei großen Texten)"""
    import unicodedata
    # Normalisieren
    text = unicodedata.normalize('NFKD', text)
    # Lowercase
    text = text.lower()
    # Whitespace normalisieren
    text = ' '.join(text.split())
    return text

# Erste Calls: Langsam (berechnet)
text1 = normalize_text("  HALLO   Welt  ")
text2 = normalize_text("  HALLO   Welt  ")  # Cache hit!

# Cache-Info anzeigen
print(normalize_text.cache_info())
# CacheInfo(hits=1, misses=1, maxsize=128, currsize=1)
```

## Profiling

### Zeit-Profiling

```python
import time
import cProfile
import pstats

def string_operations():
    # Verschiedene Operationen
    result = []
    for i in range(1000):
        text = f"Item {i}"
        text = text.upper()
        text = text.replace("ITEM", "Element")
        result.append(text)
    return "".join(result)

# Profiling
profiler = cProfile.Profile()
profiler.enable()
string_operations()
profiler.disable()

# Ergebnisse anzeigen
stats = pstats.Stats(profiler)
stats.sort_stats('cumulative')
stats.print_stats(10)  # Top 10 Funktionen
```

### Memory-Profiling

```python
# Memory Profiling mit tracemalloc
import tracemalloc

tracemalloc.start()

# String-Operationen
strings = []
for i in range(10000):
    strings.append(f"String {i}")

result = " ".join(strings)

# Memory-Statistiken
current, peak = tracemalloc.get_traced_memory()
print(f"Current: {current / 1024 / 1024:.2f} MB")
print(f"Peak: {peak / 1024 / 1024:.2f} MB")

tracemalloc.stop()
```

## Best Practices Zusammenfassung

```python
# ✅ DO's

# 1. join() für Konkatenation
parts = [str(i) for i in range(100)]
result = "".join(parts)

# 2. f-strings für Formatierung
name = "Alice"
text = f"Hallo {name}"

# 3. Vorberechnete Konstanten
SEPARATOR = ","
parts = ["a", "b", "c"]
result = SEPARATOR.join(parts)

# 4. Richtige Methode wählen
if text.startswith("Py"):  # Nicht: text[:2] == "Py"
    pass

# 5. Regex kompilieren
import re
PATTERN = re.compile(r"\d+")
PATTERN.findall(text)

# 6. Caching nutzen
from functools import lru_cache
@lru_cache(maxsize=128)
def expensive_operation(text):
    pass

# ❌ DON'Ts

# 1. += in Loops
# result = ""
# for item in items:
#     result += item  # Langsam!

# 2. Unnötige Regex
# if re.match(r"Python", text):  # Nutze startswith()
#     pass

# 3. Wiederholte Berechnungen
# for line in lines:
#     if line.endswith("\n"):  # "\n" jedes Mal neu
#         pass

# 4. Slicing in Loops
# for i in range(len(text)):
#     if text[i:i+6] == "Python":  # Langsam, nutze find()
#         pass
```

## Übungsaufgaben

### Übung 1: Optimiere String-Builder (Leicht)

Optimiere eine Funktion die viele Strings zusammenfügt.

**Erwarteter Output:**
```python
# Gegeben: Langsame Implementierung
# Aufgabe: Mache sie 10x schneller
result = build_string_optimized(items)
```

### Übung 2: Benchmark-Suite (Mittel)

Erstelle eine Benchmark-Suite für String-Operationen.

**Erwarteter Output:**
```python
benchmarks = StringBenchmarks()
benchmarks.run_all()
# Operation        Time        Memory
# concatenation   0.0234s     1.2 MB
# join()          0.0023s     0.8 MB
# ...
```

### Übung 3: String-Pool (Mittel)

Implementiere einen String-Pool für häufig verwendete Strings.

**Erwarteter Output:**
```python
pool = StringPool()
s1 = pool.get("hello")
s2 = pool.get("hello")
assert s1 is s2  # Gleiche Objekt-Identität
```

### Übung 4: Performance-Analyzer (Schwer)

Analysiere String-Code und gib Optimierungsvorschläge.

**Erwarteter Output:**
```python
code = '''
result = ""
for item in items:
    result += item
'''
suggestions = analyze_performance(code)
# ["Use join() instead of += in loop", ...]
```

### Übung 5: Memory-Efficient Text Processor (Schwer)

Verarbeite große Textdateien speichereffizient.

**Erwarteter Output:**
```python
# Datei Zeile für Zeile verarbeiten ohne alles in RAM zu laden
processor = TextProcessor('large_file.txt')
for processed_line in processor.process_lines():
    print(processed_line)
# Peak Memory < 100 MB auch bei GB-großen Dateien
```

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [Time Complexity](https://wiki.python.org/moin/TimeComplexity) - Komplexität aller Operationen
- [Performance Tips](https://wiki.python.org/moin/PythonSpeed/PerformanceTips) - Offizielle Performance-Tipps
- [timeit Module](https://docs.python.org/3/library/timeit.html) - Zeitmessung
- [cProfile Module](https://docs.python.org/3/library/profile.html) - Profiling
- [tracemalloc Module](https://docs.python.org/3/library/tracemalloc.html) - Memory-Profiling

## Zusammenfassung

### Wichtigste Punkte

1. **Strings sind Immutable:**
   - Jede "Änderung" = neues Objekt
   - += in Loops ist O(n²)
   - join() ist O(n)

2. **Konkatenation:**
   - ✅ `"".join(list)` - schnell
   - ❌ `result += item` in Loop - langsam
   - ✅ StringBuilder-Pattern für viele Ops

3. **Formatierung:**
   - ✅ f-strings - schnellst
   - ⚠️ .format() - mittel
   - ❌ % - langsam

4. **Such-Operationen:**
   - ✅ `startswith()`, `endswith()` - O(m)
   - ✅ `in` - optimiert
   - ⚠️ Regex nur wenn nötig

5. **Optimierungen:**
   - Konstanten vorberechnen
   - Regex kompilieren
   - Caching nutzen
   - Richtige Datenstruktur wählen
   - Profiling bei Performance-Problemen

### Performance-Checkliste

- [ ] join() statt += für Konkatenation?
- [ ] f-strings für Formatierung?
- [ ] Konstanten vorberechnet?
- [ ] Regex kompiliert?
- [ ] Caching wo sinnvoll?
- [ ] Richtige Methoden gewählt?
- [ ] Profiling durchgeführt?

## Verwandte Themen

- [[01_String Basics|String Basics]] - String-Grundlagen
- [[02_String Methoden|String Methoden]] - String-Operationen
- [[03_Formatierung (f-strings)|Formatierung]] - String-Formatierung
- [[04_Encoding und Unicode|Encoding]] - Unicode-Handling
- [[06_Übungen Strings|Übungen]] - Mehr Praxis

---

← [[04_Encoding und Unicode|Encoding und Unicode]] | [[INDEX|📑 Index]] | [[06_Übungen Strings|Übungen Strings]] →