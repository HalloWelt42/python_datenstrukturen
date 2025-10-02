---
tags: [python, datenstrukturen, slicing, slice, objects]
created: 2025-10-01
---

← [[02_Sequenzen/03_Slicing/02_Erweiterte Slicing Techniken|Erweiterte Slicing Techniken]] | [[INDEX|📑 Index]] | [[02_Sequenzen/03_Slicing/04_Übungen Slicing|Übungen Slicing]] →

# Slice Objekte - Die slice() Funktion

> [!tip] Lernziel
> Nach dieser Seite verstehst du Slice-Objekte als First-Class Citizens, kannst sie mit der `slice()` Funktion erstellen, benennen, wiederverwenden und für dynamisches Slicing einsetzen.

## Was sind Slice-Objekte?

**Slice-Objekte** sind Python-Objekte, die Slicing-Parameter kapseln. Sie werden normalerweise implizit durch die Slice-Notation `[start:stop:step]` erstellt, können aber auch explizit mit der `slice()` Funktion erzeugt werden.

**Vorteile:**
- ✅ **Wiederverwendbar** - Benenne und speichere Slices
- ✅ **Dynamisch** - Berechne Slice-Parameter zur Laufzeit
- ✅ **Lesbar** - Semantische Namen statt magischer Zahlen
- ✅ **Testbar** - Slices können getestet werden

```python
# Implizit: Slice-Notation
numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
subset = numbers[2:7]  # Python erstellt slice(2, 7, None)

# Explizit: slice() Funktion
my_slice = slice(2, 7)
subset = numbers[my_slice]  # Äquivalent!
print(subset)  # [2, 3, 4, 5, 6]

# Slice-Objekt untersuchen
print(type(my_slice))    # <class 'slice'>
print(my_slice)          # slice(2, 7, None)
print(my_slice.start)    # 2
print(my_slice.stop)     # 7
print(my_slice.step)     # None
```

---

## Die slice() Funktion

### Syntax und Parameter

```python
slice(stop)
slice(start, stop)
slice(start, stop, step)
```

- **stop**: Endindex (exklusiv)
- **start**: Startindex (optional, default=None)
- **step**: Schrittweite (optional, default=None)

```python
# Ein Parameter: nur stop
s1 = slice(5)
print(s1)  # slice(None, 5, None)
numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
print(numbers[s1])  # [0, 1, 2, 3, 4] - gleich wie [:5]

# Zwei Parameter: start und stop
s2 = slice(2, 7)
print(s2)  # slice(2, 7, None)
print(numbers[s2])  # [2, 3, 4, 5, 6] - gleich wie [2:7]

# Drei Parameter: start, stop, step
s3 = slice(1, 9, 2)
print(s3)  # slice(1, 9, 2)
print(numbers[s3])  # [1, 3, 5, 7] - gleich wie [1:9:2]

# Mit negativen Werten
s4 = slice(-5, -1)
print(numbers[s4])  # [5, 6, 7, 8] - gleich wie [-5:-1]

# Mit negativem Step
s5 = slice(None, None, -1)
print(numbers[s5])  # [9, 8, 7, 6, 5, 4, 3, 2, 1, 0] - gleich wie [::-1]
```

### Slice-Attribute

```python
s = slice(2, 10, 3)

# Attribute (read-only)
print(s.start)  # 2
print(s.stop)   # 10
print(s.step)   # 3

# None bedeutet "default"
s_default = slice(5)
print(s_default.start)  # None (= 0)
print(s_default.stop)   # 5
print(s_default.step)   # None (= 1)

# Slice-Objekte sind unveränderlich
try:
    s.start = 5  # AttributeError
except AttributeError as e:
    print(f"Fehler: {e}")  # readonly attribute
```

---

## Benannte Slices

### Slice-Konstanten für Lesbarkeit

```python
# ❌ Magische Zahlen - Was bedeuten sie?
data = "2025-10-01 14:30:15"
year = data[0:4]
month = data[5:7]
day = data[8:10]

# ✅ Benannte Slices - Selbstdokumentierend!
YEAR = slice(0, 4)
MONTH = slice(5, 7)
DAY = slice(8, 10)
HOUR = slice(11, 13)
MINUTE = slice(14, 16)
SECOND = slice(17, 19)

year = data[YEAR]
month = data[MONTH]
day = data[DAY]

print(f"Datum: {year}-{month}-{day}")  # Datum: 2025-10-01
```

### Praktisches Beispiel: CSV-Parser

```python
# CSV mit fester Spaltenbreite
# Name: 0-20, Alter: 20-25, Stadt: 25-45

# Slice-Definitionen
NAME = slice(0, 20)
AGE = slice(20, 25)
CITY = slice(25, 45)

def parse_csv_line(line):
    """Parsed Zeile mit festen Spaltenbreiten"""
    return {
        'name': line[NAME].strip(),
        'age': int(line[AGE].strip()),
        'city': line[CITY].strip()
    }

# Testdaten
csv_line = "Anna Schmidt        25   Berlin                    "
person = parse_csv_line(csv_line)
print(person)  # {'name': 'Anna Schmidt', 'age': 25, 'city': 'Berlin'}

# Mehrere Zeilen
csv_data = [
    "Bob Müller         30   München                   ",
    "Charlie Wagner     28   Hamburg                   "
]

people = [parse_csv_line(line) for line in csv_data]
for person in people:
    print(f"{person['name']} ({person['age']}) aus {person['city']}")
```

### Beispiel: Protokoll-Parser

```python
# Binäres Protokoll mit Feldern fester Größe
# Header: 4 Bytes, Type: 1 Byte, Length: 2 Bytes, Data: Variable

HEADER = slice(0, 4)
TYPE = slice(4, 5)
LENGTH = slice(5, 7)
DATA = slice(7, None)  # Rest

def parse_packet(packet):
    """Parsed Protokoll-Paket"""
    return {
        'header': packet[HEADER],
        'type': packet[TYPE],
        'length': packet[LENGTH],
        'data': packet[DATA]
    }

# Simuliertes Paket
packet = bytes([0xFF, 0xFF, 0xFF, 0xFF,  # Header
                0x01,                      # Type
                0x00, 0x05,                # Length (5)
                0x48, 0x65, 0x6C, 0x6C, 0x6F])  # "Hello"

parsed = parse_packet(packet)
print(f"Header: {parsed['header'].hex()}")
print(f"Type: {parsed['type'][0]}")
print(f"Data: {parsed['data'].decode('ascii')}")
```

---

## Die indices() Methode

### Normalisierung von Slice-Parametern

Die `indices()` Methode konvertiert Slice-Parameter relativ zu einer gegebenen Länge in absolute Indizes. Sie behandelt negative Werte und None-Werte.

```python
# Signatur: slice.indices(length) -> (start, stop, stride)

s = slice(2, 8, 2)
start, stop, step = s.indices(10)
print(f"start={start}, stop={stop}, step={step}")  # start=2, stop=8, step=2

# Mit None-Werten
s = slice(None, None, None)
start, stop, step = s.indices(10)
print(f"start={start}, stop={stop}, step={step}")  # start=0, stop=10, step=1

# Mit negativen Werten
s = slice(-5, -1, 1)
start, stop, step = s.indices(10)
print(f"start={start}, stop={stop}, step={step}")  # start=5, stop=9, step=1

# Mit negativem Step
s = slice(None, None, -1)
start, stop, step = s.indices(10)
print(f"start={start}, stop={stop}, step={step}")  # start=9, stop=-1, step=-1

# Out-of-bounds wird angepasst
s = slice(5, 100, 1)
start, stop, step = s.indices(10)
print(f"start={start}, stop={stop}, step={step}")  # start=5, stop=10, step=1
```

### Praktische Anwendung: Manual Slicing

```python
def manual_slice(sequence, slice_obj):
    """Implementiert Slicing manuell"""
    start, stop, step = slice_obj.indices(len(sequence))
    
    result = []
    if step > 0:
        i = start
        while i < stop:
            result.append(sequence[i])
            i += step
    else:  # step < 0
        i = start
        while i > stop:
            result.append(sequence[i])
            i += step
    
    return result

numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

# Test verschiedene Slices
s1 = slice(2, 7)
print(manual_slice(numbers, s1))  # [2, 3, 4, 5, 6]

s2 = slice(None, None, 2)
print(manual_slice(numbers, s2))  # [0, 2, 4, 6, 8]

s3 = slice(None, None, -1)
print(manual_slice(numbers, s3))  # [9, 8, 7, 6, 5, 4, 3, 2, 1, 0]
```

---

## Dynamisches Slicing

### Slice zur Laufzeit berechnen

```python
def get_slice_for_page(page, items_per_page):
    """Berechnet Slice für Pagination"""
    start = (page - 1) * items_per_page
    stop = start + items_per_page
    return slice(start, stop)

# Daten paginieren
data = list(range(100))
page = 3
items_per_page = 10

page_slice = get_slice_for_page(page, items_per_page)
page_data = data[page_slice]
print(f"Seite {page}: {page_data}")  # Seite 3: [20, 21, 22, ..., 29]

# Alle Seiten durchgehen
def paginate(data, items_per_page):
    """Generator für Seiten"""
    total_pages = (len(data) + items_per_page - 1) // items_per_page
    
    for page in range(1, total_pages + 1):
        page_slice = get_slice_for_page(page, items_per_page)
        yield page, data[page_slice]

for page_num, page_data in paginate(list(range(25)), 10):
    print(f"Seite {page_num}: {len(page_data)} Items")
```

### Konfigurierbare Extraktion

```python
class DataExtractor:
    """Extrahiert Daten mit konfigurierbaren Slices"""
    
    def __init__(self):
        self.slices = {}
    
    def define_field(self, name, start, stop=None, step=None):
        """Definiert benanntes Feld"""
        if stop is None:
            self.slices[name] = slice(start)
        elif step is None:
            self.slices[name] = slice(start, stop)
        else:
            self.slices[name] = slice(start, stop, step)
    
    def extract(self, data, field_name):
        """Extrahiert Feld aus Daten"""
        if field_name not in self.slices:
            raise ValueError(f"Unbekanntes Feld: {field_name}")
        return data[self.slices[field_name]]
    
    def extract_all(self, data):
        """Extrahiert alle Felder"""
        return {name: data[s] for name, s in self.slices.items()}

# Verwendung
extractor = DataExtractor()

# Für Log-Zeilen
extractor.define_field('timestamp', 0, 19)
extractor.define_field('level', 21, 26)
extractor.define_field('message', 28, None)

log_line = "2025-10-01 14:30:15 INFO: Server started"
fields = extractor.extract_all(log_line)
print(fields)
# {'timestamp': '2025-10-01 14:30:1', 'level': 'INFO:', 'message': 'Server started'}

# Einzelnes Feld
timestamp = extractor.extract(log_line, 'timestamp')
print(f"Timestamp: {timestamp}")
```

---

## Slices in benutzerdefinierten Klassen

### __getitem__ mit Slice-Objekten

```python
class TimeSeries:
    """Zeitreihen-Datenstruktur mit Slice-Support"""
    
    def __init__(self, data):
        self._data = list(data)
    
    def __getitem__(self, key):
        """Unterstützt Index und Slice"""
        if isinstance(key, slice):
            # Slice-Objekt empfangen
            print(f"Slice empfangen: {key}")
            return TimeSeries(self._data[key])
        elif isinstance(key, int):
            # Einzelner Index
            return self._data[key]
        else:
            raise TypeError(f"Ungültiger Index-Typ: {type(key)}")
    
    def __len__(self):
        return len(self._data)
    
    def __repr__(self):
        return f"TimeSeries({self._data})"

# Verwendung
ts = TimeSeries([1, 2, 3, 4, 5, 6, 7, 8, 9, 10])

# Slice-Notation erstellt slice-Objekt
subset = ts[2:7]
print(subset)  # TimeSeries([3, 4, 5, 6, 7])

# Slice-Objekt explizit übergeben
my_slice = slice(1, 9, 2)
subset = ts[my_slice]
print(subset)  # TimeSeries([2, 4, 6, 8])
```

### Erweiterte Slice-Verarbeitung

```python
class DataFrame:
    """Vereinfachte DataFrame-Klasse mit benannten Spalten"""
    
    def __init__(self, data, columns):
        self._data = data  # Liste von Listen
        self._columns = columns
    
    def __getitem__(self, key):
        """Unterstützt Zeilen-Slicing und Spalten-Namen"""
        if isinstance(key, slice):
            # Zeilen slicen
            return DataFrame(self._data[key], self._columns)
        elif isinstance(key, str):
            # Spalte nach Name
            col_idx = self._columns.index(key)
            return [row[col_idx] for row in self._data]
        elif isinstance(key, tuple):
            # (Zeilen-Slice, Spalten)
            row_selector, col_selector = key
            
            # Zeilen filtern
            rows = self._data[row_selector] if isinstance(row_selector, slice) else [self._data[row_selector]]
            
            # Spalten filtern
            if isinstance(col_selector, str):
                col_idx = self._columns.index(col_selector)
                return [row[col_idx] for row in rows]
            elif isinstance(col_selector, slice):
                return DataFrame([row[col_selector] for row in rows], 
                               self._columns[col_selector])
        
        raise TypeError(f"Ungültiger Selektor: {key}")
    
    def __repr__(self):
        header = " | ".join(self._columns)
        rows = "\n".join(" | ".join(map(str, row)) for row in self._data)
        return f"{header}\n{'-' * len(header)}\n{rows}"

# Verwendung
data = [
    ["Anna", 25, "Berlin"],
    ["Bob", 30, "München"],
    ["Charlie", 28, "Hamburg"],
    ["David", 35, "Köln"]
]
columns = ["Name", "Alter", "Stadt"]

df = DataFrame(data, columns)

# Spalte nach Name
print(df["Name"])  # ['Anna', 'Bob', 'Charlie', 'David']

# Zeilen slicen
print(df[1:3])
# Name | Alter | Stadt
# --------------------
# Bob | 30 | München
# Charlie | 28 | Hamburg

# Kombination
print(df[1:3, "Alter"])  # [30, 28]
```

---

## Vergleich: Notation vs slice()

### Wann was verwenden?

```python
numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

# ✅ Notation: Direkt und einfach
subset = numbers[2:7]

# ✅ slice(): Wiederverwendbar und dynamisch
MIDDLE = slice(2, 7)
subset = numbers[MIDDLE]

# ✅ slice(): Berechnete Parameter
def get_window(center, width):
    start = center - width // 2
    stop = center + width // 2 + 1
    return slice(start, stop)

window = get_window(5, 3)  # Fenster um Index 5, Breite 3
print(numbers[window])  # [4, 5, 6]

# ✅ slice(): Benannte Konstanten
HEADER = slice(0, 10)
BODY = slice(10, None)

# ❌ Notation: Bei zu komplexen Ausdrücken
# Unleserlich:
result = data[(start if condition else alt_start):(stop if other else alt_stop)]

# Besser mit slice():
s = slice(start if condition else alt_start, 
          stop if other else alt_stop)
result = data[s]
```

---

## Häufige Anwendungsfälle

### Anwendungsfall 1: Konfigurierbare Datenformate

```python
# Verschiedene Datumsformate
DATE_FORMATS = {
    'iso': {
        'year': slice(0, 4),
        'month': slice(5, 7),
        'day': slice(8, 10)
    },
    'us': {
        'month': slice(0, 2),
        'day': slice(3, 5),
        'year': slice(6, 10)
    },
    'eu': {
        'day': slice(0, 2),
        'month': slice(3, 5),
        'year': slice(6, 10)
    }
}

def parse_date(date_string, format_name):
    """Parsed Datum nach Format"""
    format_def = DATE_FORMATS[format_name]
    return {
        'year': int(date_string[format_def['year']]),
        'month': int(date_string[format_def['month']]),
        'day': int(date_string[format_def['day']])
    }

# Tests
iso_date = "2025-10-01"
us_date = "10/01/2025"
eu_date = "01.10.2025"

print(parse_date(iso_date, 'iso'))  # {'year': 2025, 'month': 10, 'day': 1}
print(parse_date(us_date, 'us'))    # {'year': 2025, 'month': 10, 'day': 1}
print(parse_date(eu_date, 'eu'))    # {'year': 2025, 'month': 10, 'day': 1}
```

### Anwendungsfall 2: Batch Processing

```python
def process_in_batches(data, batch_size):
    """Verarbeitet Daten in Batches mit Slices"""
    num_batches = (len(data) + batch_size - 1) // batch_size
    
    for i in range(num_batches):
        batch_slice = slice(i * batch_size, (i + 1) * batch_size)
        batch = data[batch_slice]
        
        # Verarbeite Batch
        print(f"Batch {i+1}: {len(batch)} Items")
        yield batch

# Verwendung
data = list(range(27))
for batch in process_in_batches(data, 10):
    print(f"  Verarbeite: {batch[:3]}... bis ...{batch[-3:]}")
```

### Anwendungsfall 3: Rolling Window

```python
class RollingWindow:
    """Rolling Window mit konfigurierbarer Größe"""
    
    def __init__(self, size):
        self.size = size
    
    def apply(self, data, func):
        """Wendet Funktion auf alle Fenster an"""
        results = []
        for i in range(len(data) - self.size + 1):
            window_slice = slice(i, i + self.size)
            window = data[window_slice]
            results.append(func(window))
        return results

# Verwendung
prices = [100, 102, 98, 105, 110, 108, 112, 115, 113, 120]

# 3-Tage gleitender Durchschnitt
window = RollingWindow(3)
moving_avg = window.apply(prices, lambda w: sum(w) / len(w))
print("Gleitender Durchschnitt:", [f"{x:.2f}" for x in moving_avg])

# Maximum im Fenster
rolling_max = window.apply(prices, max)
print("Rolling Maximum:", rolling_max)
```

---

## Performance-Hinweise

### slice() vs Notation

```python
import time

data = list(range(1000000))

# Notation
start = time.perf_counter()
for _ in range(10000):
    subset = data[100:200]
time1 = time.perf_counter() - start

# slice()-Objekt (einmal erstellt)
s = slice(100, 200)
start = time.perf_counter()
for _ in range(10000):
    subset = data[s]
time2 = time.perf_counter() - start

print(f"Notation: {time1:.4f}s")
print(f"slice():  {time2:.4f}s")
print(f"Unterschied: {abs(time1-time2)/min(time1, time2)*100:.1f}%")

# Performance ist praktisch identisch!
# Vorteil von slice(): Wiederverwendbarkeit und Lesbarkeit
```

---

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [slice() Built-in](https://docs.python.org/3/library/functions.html#slice) - slice() Funktion
- [Slice Objects](https://docs.python.org/3/c-api/slice.html) - C-API Dokumentation
- [Data Model - Slicing](https://docs.python.org/3/reference/datamodel.html#object.__getitem__) - `__getitem__` mit Slices

---

## Verwandte Themen

- [[02_Sequenzen/03_Slicing/01_Slicing Grundlagen|Slicing Grundlagen]] - Basis-Syntax
- [[02_Sequenzen/03_Slicing/02_Erweiterte Slicing Techniken|Erweiterte Slicing Techniken]] - Slice Assignment
- [[02_Sequenzen/01_Listen/01_List Basics|List Basics]] - Listen und Slicing
- [[06_Praxis/01_Best_Practices/01_Idiomatisches Python|Idiomatisches Python]] - Pythonic Patterns

---

← [[02_Sequenzen/03_Slicing/02_Erweiterte Slicing Techniken|Erweiterte Slicing Techniken]] | [[INDEX|📑 Index]] | [[02_Sequenzen/03_Slicing/04_Übungen Slicing|Übungen Slicing]] →