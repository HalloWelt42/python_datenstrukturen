---
tags: [python, datenstrukturen, memoryview, buffer, zero-copy]
created: 2025-10-01
---

← [[01_Bytes und Bytearray|Bytes und Bytearray]] | [[INDEX|📑 Index]] | [[03_Übungen Bytes|Übungen Bytes]] →

# Memoryview

> [!tip] Lernziel
> Du verstehst memoryview als Zero-Copy Buffer-Interface und kennst seine Vorteile für Performance-kritische Operationen

## Was ist Memoryview?

**Memoryview** ermöglicht den direkten Zugriff auf die Speicherrepräsentation von Objekten ohne Kopien zu erstellen (Zero-Copy).

```python
# Ohne memoryview: Kopie wird erstellt
data = bytearray(b"Hello World")
slice1 = data[0:5]  # Kopie!
print(type(slice1))  # <class 'bytearray'>

# Mit memoryview: Keine Kopie, nur Referenz
data = bytearray(b"Hello World")
view = memoryview(data)
slice2 = view[0:5]   # Keine Kopie!
print(type(slice2))  # <class 'memoryview'>

# Änderungen im Original sichtbar
data[0] = ord('J')
print(bytes(view[0:5]))  # b'Jello' (sieht Änderung!)
```

## Memoryview erstellen

### Von bytes/bytearray

```python
# Von bytearray (mutable)
arr = bytearray(b"Python")
view = memoryview(arr)
print(view)  # <memory at 0x...>

# Von bytes (immutable)
data = b"Python"
view = memoryview(data)
print(view)

# Von array
import array
arr = array.array('i', [1, 2, 3, 4, 5])
view = memoryview(arr)
print(view)
```

### Eigenschaften abfragen

```python
arr = bytearray(b"Hello")
view = memoryview(arr)

# Format (wie in struct)
print(view.format)      # 'B' (unsigned byte)

# Item-Größe
print(view.itemsize)    # 1 (1 Byte pro Item)

# Anzahl Dimensionen
print(view.ndim)        # 1 (eindimensional)

# Shape (wie in NumPy)
print(view.shape)       # (5,) - 5 Elemente

# Strides (Schrittweite)
print(view.strides)     # (1,) - 1 Byte Schrittweite

# Größe
print(len(view))        # 5

# Read-only?
print(view.readonly)    # False (bytearray ist mutable)

# Von bytes: read-only
view2 = memoryview(b"Test")
print(view2.readonly)   # True
```

## Zero-Copy Slicing

### Das Problem mit Kopien

```python
import time
import sys

# Große Daten
size = 10_000_000
data = bytearray(b"X" * size)

# ❌ Mit Kopie (langsam)
start = time.perf_counter()
slice1 = data[100:1000]  # Kopiert 900 Bytes
t1 = time.perf_counter() - start

# ✅ Ohne Kopie (schnell)
view = memoryview(data)
start = time.perf_counter()
slice2 = view[100:1000]  # Keine Kopie!
t2 = time.perf_counter() - start

print(f"Mit Kopie:   {t1*1000:.4f}ms")
print(f"Ohne Kopie:  {t2*1000:.4f}ms")
print(f"Speedup:     {t1/t2:.1f}x")

# Memory-Verbrauch
print(f"\nSlice size:  {sys.getsizeof(slice1)} bytes")
print(f"View size:   {sys.getsizeof(slice2)} bytes")
# View ist deutlich kleiner!
```

### Slicing mit memoryview

```python
arr = bytearray(b"Python Programming")
view = memoryview(arr)

# Slicing
sub_view = view[0:6]
print(bytes(sub_view))  # b'Python'

# Verschachtelte Slices
sub_sub = sub_view[0:3]
print(bytes(sub_sub))   # b'Pyt'

# Alle zeigen auf gleichen Speicher!
arr[0] = ord('J')
print(bytes(view))      # b'Jython Programming'
print(bytes(sub_view))  # b'Jython' (sieht Änderung!)
```

## Zugriff und Modifikation

### Lesen

```python
arr = bytearray(b"Hello")
view = memoryview(arr)

# Index-Zugriff
print(view[0])          # 72 (ASCII 'H')
print(chr(view[0]))     # 'H'

# Iteration
for byte_val in view:
    print(byte_val, end=' ')  # 72 101 108 108 111
print()

# Zu bytes konvertieren
print(bytes(view))      # b'Hello'

# Zu Liste
print(list(view))       # [72, 101, 108, 108, 111]
```

### Schreiben (bei mutable objects)

```python
arr = bytearray(b"Hello")
view = memoryview(arr)

# ✅ Einzelne Werte ändern
view[0] = ord('J')
print(bytes(view))      # b'Jello'
print(arr)              # bytearray(b'Jello')

# Slice-Assignment
view[1:3] = b"aw"
print(bytes(view))      # b'Jawlo'

# ⚠️ Länge muss passen!
try:
    view[1:3] = b"xxx"  # 3 bytes statt 2
except ValueError as e:
    print(f"Fehler: {e}")  # memoryview assignment: lvalue and rvalue have different structures
```

### Read-only Views

```python
# Von bytes: read-only
data = b"Hello"
view = memoryview(data)

print(view.readonly)    # True

# ❌ Schreiben nicht möglich
try:
    view[0] = ord('J')
except TypeError as e:
    print(f"Fehler: {e}")  # cannot modify read-only memory
```

## Casting und Reinterpretation

### cast() - Daten uminterpretieren

```python
import array

# Integer-Array
arr = array.array('i', [1, 2, 3, 4])  # 4 signed ints (4 bytes each)
view = memoryview(arr)

print(f"Original: format={view.format}, itemsize={view.itemsize}")
# format='i', itemsize=4

# Cast zu bytes (unsigned bytes)
byte_view = view.cast('B')  # 'B' = unsigned byte
print(f"Bytes: format={byte_view.format}, itemsize={byte_view.itemsize}")
# format='B', itemsize=1

print(f"Original length: {len(view)}")      # 4 items
print(f"Bytes length: {len(byte_view)}")    # 16 bytes (4*4)

# Bytes anzeigen (Little Endian)
print(list(byte_view))  # [1, 0, 0, 0, 2, 0, 0, 0, 3, 0, 0, 0, 4, 0, 0, 0]
```

### Mehrdimensionale Casts

```python
# Eindimensional zu zweidimensional
data = bytearray(range(12))  # 0-11
view = memoryview(data)

# Cast zu 2D (3 Zeilen, 4 Spalten)
view_2d = view.cast('B', shape=[3, 4])

print(f"Shape: {view_2d.shape}")      # (3, 4)
print(f"Dimensions: {view_2d.ndim}")  # 2

# Zugriff mit [row, col]
print(view_2d[0, 0])    # 0
print(view_2d[1, 2])    # 6
print(view_2d[2, 3])    # 11

# Slice in 2D
row = view_2d[1]        # Zweite Zeile
print(list(row))        # [4, 5, 6, 7]
```

## Praktische Anwendungen

### Anwendung 1: Effizientes File-Reading

```python
def process_large_file(filename, chunk_size=4096):
    """
    Liest große Datei in Chunks ohne zusätzliche Kopien
    """
    with open(filename, 'rb') as f:
        # Lese Datei in großen Buffer
        buffer = bytearray(chunk_size)
        view = memoryview(buffer)
        
        while True:
            bytes_read = f.readinto(buffer)
            if bytes_read == 0:
                break
            
            # Verarbeite nur gelesenen Teil (ohne Kopie!)
            chunk_view = view[:bytes_read]
            
            # Verarbeitung hier (z.B. Suche, Parsing)
            process_chunk(chunk_view)

def process_chunk(view):
    """Verarbeitet Chunk ohne Kopie"""
    # Beispiel: Zähle 'e'
    count = 0
    for byte in view:
        if byte == ord('e'):
            count += 1
    return count
```

### Anwendung 2: Netzwerk-Buffer

```python
class NetworkBuffer:
    """Effizienter Netzwerk-Buffer mit memoryview"""
    
    def __init__(self, size=4096):
        self.buffer = bytearray(size)
        self.view = memoryview(self.buffer)
        self.write_pos = 0
        self.read_pos = 0
    
    def write(self, data):
        """Schreibt Daten in Buffer"""
        data_len = len(data)
        if self.write_pos + data_len > len(self.buffer):
            raise BufferError("Buffer voll")
        
        # Zero-copy write
        self.view[self.write_pos:self.write_pos + data_len] = data
        self.write_pos += data_len
    
    def read(self, size):
        """Liest Daten aus Buffer (ohne Kopie)"""
        if self.read_pos + size > self.write_pos:
            size = self.write_pos - self.read_pos
        
        # Zero-copy read (gibt memoryview zurück)
        data_view = self.view[self.read_pos:self.read_pos + size]
        self.read_pos += size
        return data_view
    
    def available(self):
        """Verfügbare Bytes"""
        return self.write_pos - self.read_pos

# Verwendung
buffer = NetworkBuffer()
buffer.write(b"Hello ")
buffer.write(b"World")

chunk1 = buffer.read(5)
print(bytes(chunk1))  # b'Hello'

chunk2 = buffer.read(6)
print(bytes(chunk2))  # b' World'
```

### Anwendung 3: Image Processing (Pixel-Zugriff)

```python
class SimpleImage:
    """Einfaches Image mit memoryview für Pixel-Zugriff"""
    
    def __init__(self, width, height):
        self.width = width
        self.height = height
        # RGB: 3 bytes pro Pixel
        self.data = bytearray(width * height * 3)
        self.view = memoryview(self.data)
        
        # Cast zu 3D: [height, width, 3]
        self.pixels = self.view.cast('B', shape=[height, width, 3])
    
    def set_pixel(self, x, y, r, g, b):
        """Setzt Pixel (ohne Kopie)"""
        self.pixels[y, x, 0] = r
        self.pixels[y, x, 1] = g
        self.pixels[y, x, 2] = b
    
    def get_pixel(self, x, y):
        """Holt Pixel (ohne Kopie)"""
        return (
            self.pixels[y, x, 0],
            self.pixels[y, x, 1],
            self.pixels[y, x, 2]
        )
    
    def get_row(self, y):
        """Gibt Zeile zurück (ohne Kopie)"""
        return self.pixels[y]

# Verwendung
img = SimpleImage(100, 100)

# Setze roten Pixel
img.set_pixel(50, 50, 255, 0, 0)

# Hole Pixel
r, g, b = img.get_pixel(50, 50)
print(f"RGB: ({r}, {g}, {b})")  # (255, 0, 0)

# Zeile verarbeiten (ohne Kopie)
row = img.get_row(50)
print(f"Row shape: {row.shape}")  # (100, 3)
```

### Anwendung 4: Binary Protocol Parsing

```python
import struct

def parse_message(data):
    """
    Parsed Binär-Message effizient mit memoryview
    Format: [type:1byte][length:4bytes][data:n bytes]
    """
    view = memoryview(data)
    
    # Type (1 byte)
    msg_type = view[0]
    
    # Length (4 bytes, Little Endian)
    length_bytes = bytes(view[1:5])
    msg_length = struct.unpack('<I', length_bytes)[0]
    
    # Data (ohne Kopie!)
    msg_data = view[5:5+msg_length]
    
    return {
        'type': msg_type,
        'length': msg_length,
        'data': msg_data  # memoryview, keine Kopie!
    }

# Beispiel
message = bytearray([1])  # Type
message.extend(struct.pack('<I', 5))  # Length
message.extend(b"Hello")  # Data

parsed = parse_message(message)
print(f"Type: {parsed['type']}")
print(f"Data: {bytes(parsed['data'])}")
```

### Anwendung 5: Zero-Copy Slicing für Analyse

```python
def analyze_log_line(line_view):
    """Analysiert Log-Zeile ohne Kopien"""
    # Suche Timestamp (erste 19 Zeichen)
    timestamp = line_view[0:19]
    
    # Suche Level (nach '] ')
    line_bytes = bytes(line_view)
    level_start = line_bytes.find(b'] ') + 2
    level_end = line_bytes.find(b' ', level_start)
    level = line_view[level_start:level_end]
    
    # Rest ist Message
    message = line_view[level_end+1:]
    
    return {
        'timestamp': bytes(timestamp),
        'level': bytes(level),
        'message': bytes(message)
    }

# Große Log-Datei
log_data = bytearray(b"[2025-10-01 14:30] INFO Server started\n" * 10000)
view = memoryview(log_data)

# Parse Zeilen ohne Kopien
lines = bytes(view).split(b'\n')
for line_bytes in lines[:3]:
    if line_bytes:
        line_view = memoryview(line_bytes)
        parsed = analyze_log_line(line_view)
        print(parsed)
```

## Häufige Fallstricke

### Fallstrick 1: Original-Object löschen

```python
def create_view():
    data = bytearray(b"Hello")
    return memoryview(data)

# ⚠️ Gefährlich: data wird gelöscht!
view = create_view()
# view zeigt auf gelöschten Speicher (undefined behavior!)

# ✅ Besser: Original behalten
def create_view_safe():
    data = bytearray(b"Hello")
    view = memoryview(data)
    return view, data  # Beide zurückgeben

view, data = create_view_safe()  # data bleibt im Scope
```

### Fallstrick 2: Slice-Length muss passen

```python
arr = bytearray(b"Hello")
view = memoryview(arr)

# ❌ Verschiedene Längen
try:
    view[1:3] = b"xxx"  # 3 bytes in 2-byte Slice
except ValueError as e:
    print(f"Fehler: {e}")

# ✅ Gleiche Länge
view[1:3] = b"ab"
print(bytes(view))  # b'Hablo'
```

### Fallstrick 3: Read-only Views

```python
# bytes ist immutable
data = b"Hello"
view = memoryview(data)

# ❌ Kann nicht ändern
try:
    view[0] = ord('J')
except TypeError as e:
    print(f"Fehler: {e}")

# ✅ Erst zu bytearray konvertieren
arr = bytearray(data)
view = memoryview(arr)
view[0] = ord('J')  # OK
```

### Fallstrick 4: Release vergessen

```python
# memoryview hält Referenz zum Original
arr = bytearray(b"Hello")
view = memoryview(arr)

# Manche Operationen brauchen exklusiven Zugriff
try:
    arr.resize(10)  # Geht nicht während view existiert!
except BufferError as e:
    print(f"Fehler: {e}")

# ✅ View erst releasen
view.release()
arr.resize(10)  # Jetzt OK
```

### Fallstrick 5: Cast mit falscher Größe

```python
arr = bytearray(range(10))  # 10 bytes
view = memoryview(arr)

# ❌ Shape passt nicht
try:
    view_2d = view.cast('B', shape=[3, 4])  # 3*4=12 bytes, haben aber nur 10!
except TypeError as e:
    print(f"Fehler: {e}")

# ✅ Shape muss zur Größe passen
view_2d = view.cast('B', shape=[2, 5])  # 2*5=10 bytes - OK
print(view_2d.shape)  # (2, 5)
```

## Performance-Vergleich

### Slicing Performance

```python
import time

size = 10_000_000
iterations = 1000

# Setup
data = bytearray(b"X" * size)

# Test 1: Mit Kopie
start = time.perf_counter()
for _ in range(iterations):
    slice_copy = data[100:200]
t1 = time.perf_counter() - start

# Test 2: Ohne Kopie (memoryview)
view = memoryview(data)
start = time.perf_counter()
for _ in range(iterations):
    slice_view = view[100:200]
t2 = time.perf_counter() - start

print(f"Mit Kopie:        {t1:.4f}s")
print(f"Ohne Kopie (mv):  {t2:.4f}s")
print(f"Speedup:          {t1/t2:.1f}x")
```

### Memory-Vergleich

```python
import sys

data = bytearray(b"X" * 1_000_000)

# Kopie
slice1 = data[0:500_000]
print(f"Slice (copy):    {sys.getsizeof(slice1):,} bytes")

# memoryview
view = memoryview(data)
slice2 = view[0:500_000]
print(f"Slice (view):    {sys.getsizeof(slice2):,} bytes")

# memoryview ist deutlich kleiner!
```

## Übungsaufgaben

### Übung 1: Buffer Manager (Leicht)

Erstelle einen effizienten Buffer-Manager mit memoryview.

**Erwarteter Output:**
```python
mgr = BufferManager(1024)
mgr.write(b"Hello ")
mgr.write(b"World")
data = mgr.read(11)
print(bytes(data))  # b'Hello World'
```

### Übung 2: Image Flipper (Mittel)

Flippe Bild horizontal/vertikal ohne Kopien.

**Erwarteter Output:**
```python
img = SimpleImage(100, 100)
# ... setze Pixel ...
img.flip_horizontal()  # In-place, ohne Kopie
img.flip_vertical()    # In-place, ohne Kopie
```

### Übung 3: Binary Stream Parser (Mittel)

Parse Binary Stream effizient mit memoryview.

**Erwarteter Output:**
```python
stream = BinaryStream(data)
msg_type = stream.read_byte()
msg_len = stream.read_uint32()
msg_data = stream.read(msg_len)  # memoryview, keine Kopie
```

### Übung 4: Zero-Copy CSV (Schwer)

Parse CSV aus bytearray ohne Kopien.

**Erwarteter Output:**
```python
csv_data = bytearray(b"a,b,c\n1,2,3\n4,5,6")
parser = ZeroCopyCSV(csv_data)
for row in parser.rows():
    # row ist memoryview zu Original
    print([bytes(cell) for cell in row])
```

### Übung 5: Memory Pool (Schwer)

Implementiere Memory Pool mit memoryview für Allocation.

**Erwarteter Output:**
```python
pool = MemoryPool(10240)  # 10KB Pool
view1 = pool.allocate(100)  # Allokiert 100 bytes
view2 = pool.allocate(200)  # Allokiert 200 bytes
pool.free(view1)            # Gibt frei
view3 = pool.allocate(50)   # Reused space
```

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [memoryview](https://docs.python.org/3/library/stdtypes.html#memoryview) - memoryview Dokumentation
- [Buffer Protocol](https://docs.python.org/3/c-api/buffer.html) - Buffer Protocol (C-API)
- [struct Module](https://docs.python.org/3/library/struct.html) - Binary Data Handling

## Zusammenfassung

### Wichtigste Punkte

1. **Zero-Copy:**
   - memoryview vermeidet Kopien
   - Direkter Zugriff auf Speicher
   - Ideal für große Daten

2. **Operationen:**
   - Slicing ohne Kopie
   - Indexing gibt Werte
   - Cast für Reinterpretation

3. **Mutable vs. Immutable:**
   - bytearray → mutable view
   - bytes → read-only view
   - `readonly` Property prüfen

4. **Mehrdimensional:**
   - cast() für Reshape
   - NumPy-ähnlicher Zugriff
   - shape, strides, ndim

5. **Use Cases:**
   - Große Dateien
   - Netzwerk-Buffer
   - Binary Parsing
   - Image Processing
   - Überall wo Kopien teuer sind

### Wann memoryview nutzen?

✅ **JA:**
- Große Datenmengen
- Viele Slices
- Performance-kritisch
- Binary Data Handling

❌ **NEIN:**
- Kleine Daten (<1KB)
- Einfache One-Off Operationen
- Wenn Kopie gewünscht

## Verwandte Themen

- [[01_Bytes und Bytearray|Bytes und Bytearray]] - Grundlagen
- [[03_Übungen Bytes|Übungen]] - Mehr Praxis
- [[02_Sequenzen/01_Listen/01_List Basics|Listen]] - Andere Sequenzen

---

← [[01_Bytes und Bytearray|Bytes und Bytearray]] | [[INDEX|📑 Index]] | [[03_Übungen Bytes|Übungen Bytes]] →