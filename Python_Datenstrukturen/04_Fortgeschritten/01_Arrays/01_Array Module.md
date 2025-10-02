---
tags: [python, datenstrukturen, arrays, fortgeschritten]
created: 2025-10-01
---

← [[03_Mappings_und_Sets/03_Collections_Module/04_Übungen Collections|Übungen Collections]] | [[INDEX|📑 Index]] | [[04_Fortgeschritten/01_Arrays/02_Array vs List|Array vs List]] →

# Array Module

> [!tip] Lernziel
> Nach dieser Seite verstehst du das array Modul für speichereffiziente numerische Sequenzen und kannst es für performante Datenverarbeitung einsetzen.

## Was ist das Array Module?

Das **array** Modul bietet speichereffiziente Arrays für **homogene** (gleichartige) Daten. Anders als Listen können Arrays nur Elemente eines bestimmten Typs speichern.

**Kernmerkmale:**
- **Typisiert**: Alle Elemente müssen denselben Typ haben
- **Speichereffizient**: Weniger Overhead als Listen
- **C-Arrays**: Intern als C-Arrays implementiert
- **Mutable**: Kann verändert werden
- **Sequenz-Interface**: Wie Listen verwendbar
- **Binary I/O**: Effizientes Lesen/Schreiben

**Wofür verwendet:**
- Numerische Datenverarbeitung
- Binary Data (Bytes, Integers)
- Audio/Video-Daten (Samples)
- Sensor-Daten sammeln
- Memory-effiziente große Zahlen-Sequenzen
- Interface zu C-Code

---

## Import und Type Codes

### Array erstellen

```python
import array

# Array von Integers (signed int)
arr = array.array('i', [1, 2, 3, 4, 5])
print(arr)  # array('i', [1, 2, 3, 4, 5])
print(type(arr))  # <class 'array.array'>

# Leeres Array
empty = array.array('i')
print(empty)  # array('i')

# Aus anderem Iterable
from_range = array.array('i', range(10))
print(from_range)  # array('i', [0, 1, 2, 3, 4, 5, 6, 7, 8, 9])

# Aus String (für Bytes)
byte_arr = array.array('b', b'hello')
print(byte_arr)  # array('b', [104, 101, 108, 108, 111])
```

### Type Codes

| Type Code | C Type | Python Type | Bytes | Range |
|-----------|--------|-------------|-------|-------|
| `'b'` | signed char | int | 1 | -128 to 127 |
| `'B'` | unsigned char | int | 1 | 0 to 255 |
| `'u'` | wchar_t | Unicode | 2/4 | Unicode |
| `'h'` | signed short | int | 2 | -32,768 to 32,767 |
| `'H'` | unsigned short | int | 2 | 0 to 65,535 |
| `'i'` | signed int | int | 2/4 | -2^31 to 2^31-1 |
| `'I'` | unsigned int | int | 2/4 | 0 to 2^32-1 |
| `'l'` | signed long | int | 4/8 | Platform dependent |
| `'L'` | unsigned long | int | 4/8 | Platform dependent |
| `'q'` | signed long long | int | 8 | -2^63 to 2^63-1 |
| `'Q'` | unsigned long long | int | 8 | 0 to 2^64-1 |
| `'f'` | float | float | 4 | ±3.4e38 |
| `'d'` | double | float | 8 | ±1.7e308 |

```python
import array

# Verschiedene Typen
integers = array.array('i', [1, 2, 3])
floats = array.array('f', [1.1, 2.2, 3.3])
doubles = array.array('d', [1.1, 2.2, 3.3])
bytes_arr = array.array('B', [0, 255, 128])

print(f"Int array: {integers}")
print(f"Float array: {floats}")
print(f"Double array: {doubles}")
print(f"Bytes array: {bytes_arr}")

# Größe pro Element
print(f"\nBytes pro Element:")
print(f"  'i': {integers.itemsize} bytes")
print(f"  'f': {floats.itemsize} bytes")
print(f"  'd': {doubles.itemsize} bytes")
```

---

## Grundlegende Operationen

### Zugriff und Modifikation

```python
import array

arr = array.array('i', [10, 20, 30, 40, 50])

# Index-Zugriff (wie Liste)
print(arr[0])   # 10
print(arr[-1])  # 50

# Slicing
print(arr[1:4])  # array('i', [20, 30, 40])
print(arr[::2])  # array('i', [10, 30, 50])

# Modifikation
arr[0] = 100
print(arr)  # array('i', [100, 20, 30, 40, 50])

# Slice-Zuweisung
arr[1:3] = array.array('i', [200, 300])
print(arr)  # array('i', [100, 200, 300, 40, 50])

# ⚠️ Typ muss passen!
try:
    arr[0] = 1.5  # Float in int array
    print(arr[0])  # 1 - Wird zu int konvertiert!
except:
    pass

try:
    arr[0] = "text"  # String geht nicht!
except TypeError as e:
    print(f"Fehler: {e}")
```

### Elemente hinzufügen

```python
import array

arr = array.array('i', [1, 2, 3])

# append() - Am Ende hinzufügen
arr.append(4)
print(arr)  # array('i', [1, 2, 3, 4])

# extend() - Mehrere hinzufügen
arr.extend([5, 6, 7])
print(arr)  # array('i', [1, 2, 3, 4, 5, 6, 7])

# insert() - An Position einfügen
arr.insert(0, 0)
print(arr)  # array('i', [0, 1, 2, 3, 4, 5, 6, 7])

# fromlist() - Aus Liste hinzufügen
arr.fromlist([8, 9])
print(arr)  # array('i', [0, 1, 2, 3, 4, 5, 6, 7, 8, 9])

# + Operator
arr2 = array.array('i', [10, 11])
combined = arr + arr2
print(combined[-2:])  # array('i', [10, 11])
```

### Elemente entfernen

```python
import array

arr = array.array('i', [1, 2, 3, 2, 4, 5])

# remove() - Erstes Vorkommen
arr.remove(2)
print(arr)  # array('i', [1, 3, 2, 4, 5])

# pop() - Nach Index
value = arr.pop(2)
print(f"Popped: {value}")  # 2
print(arr)  # array('i', [1, 3, 4, 5])

# pop() ohne Argument - Letztes Element
last = arr.pop()
print(f"Last: {last}")  # 5
print(arr)  # array('i', [1, 3, 4])

# del - Nach Index oder Slice
del arr[0]
print(arr)  # array('i', [3, 4])
```

---

## Array-spezifische Methoden

### buffer_info()

```python
import array

arr = array.array('i', [1, 2, 3, 4, 5])

# Buffer Info: (address, length)
info = arr.buffer_info()
print(f"Memory address: {info[0]}")
print(f"Length: {info[1]}")
print(f"Bytes pro Element: {arr.itemsize}")
print(f"Total bytes: {info[1] * arr.itemsize}")
```

### byteswap()

```python
import array

# Für Big/Little Endian Konvertierung
arr = array.array('i', [1, 256, 65536])
print(f"Original: {arr}")

# Bytes umkehren (für Endianness)
arr.byteswap()
print(f"Nach byteswap: {arr}")

# Wieder zurück
arr.byteswap()
print(f"Zurück: {arr}")
```

### tolist() und fromlist()

```python
import array

# Array zu Liste
arr = array.array('i', [1, 2, 3, 4, 5])
liste = arr.tolist()
print(liste)  # [1, 2, 3, 4, 5]
print(type(liste))  # <class 'list'>

# Liste zu Array
arr2 = array.array('i')
arr2.fromlist([10, 20, 30])
print(arr2)  # array('i', [10, 20, 30])

# ⚠️ fromlist() modifiziert bestehendes Array
arr2.fromlist([40, 50])
print(arr2)  # array('i', [10, 20, 30, 40, 50])
```

### tobytes() und frombytes()

```python
import array

# Array zu Bytes
arr = array.array('i', [1, 2, 3])
byte_data = arr.tobytes()
print(byte_data)  # b'\x01\x00\x00\x00\x02\x00\x00\x00\x03\x00\x00\x00'
print(len(byte_data))  # 12 (3 integers * 4 bytes each)

# Bytes zu Array
arr2 = array.array('i')
arr2.frombytes(byte_data)
print(arr2)  # array('i', [1, 2, 3])

# Nützlich für Binary I/O
with open('data.bin', 'wb') as f:
    arr.tofile(f)

# Zurücklesen
arr3 = array.array('i')
with open('data.bin', 'rb') as f:
    arr3.fromfile(f, 3)  # Lese 3 integers
print(arr3)  # array('i', [1, 2, 3])

# Cleanup
import os
os.remove('data.bin')
```

---

## Praktische Anwendungen

### 1. Audio-Samples verarbeiten

```python
import array
import math

# Audio-Sample generieren (Sinuswelle)
def generate_sine_wave(frequency, duration, sample_rate=44100):
    """Generiert Sinuswelle als 16-bit signed integers"""
    samples = array.array('h')  # signed short
    
    num_samples = int(duration * sample_rate)
    for i in range(num_samples):
        t = i / sample_rate
        # Sinuswelle: -32767 bis 32767
        value = int(32767 * math.sin(2 * math.pi * frequency * t))
        samples.append(value)
    
    return samples

# 440 Hz Ton (A4) für 1 Sekunde
tone = generate_sine_wave(440, 1.0)
print(f"Generiert {len(tone)} samples")
print(f"Erste 10 samples: {tone[:10]}")
print(f"Speicher: {len(tone) * tone.itemsize} bytes")

# Als WAV speichern (vereinfacht)
def save_wav(filename, samples, sample_rate=44100):
    """Speichert samples als WAV-Datei"""
    with open(filename, 'wb') as f:
        # WAV Header schreiben (vereinfacht)
        import struct
        # "RIFF" chunk
        f.write(b'RIFF')
        f.write(struct.pack('<I', 36 + len(samples) * 2))
        f.write(b'WAVE')
        # "fmt " chunk
        f.write(b'fmt ')
        f.write(struct.pack('<IHHIIHH', 16, 1, 1, sample_rate, 
                           sample_rate * 2, 2, 16))
        # "data" chunk
        f.write(b'data')
        f.write(struct.pack('<I', len(samples) * 2))
        samples.tofile(f)

# Speichern
save_wav('tone.wav', tone)
print("WAV-Datei gespeichert")

# Cleanup
import os
os.remove('tone.wav')
```

### 2. Sensor-Daten sammeln

```python
import array
import random
import time

class SensorLogger:
    def __init__(self, max_readings=1000):
        # Temperaturen als floats
        self.temperatures = array.array('f')
        # Timestamps als integers (Unix timestamp)
        self.timestamps = array.array('i')
        self.max_readings = max_readings
    
    def add_reading(self, temperature):
        """Fügt Sensor-Reading hinzu"""
        if len(self.temperatures) >= self.max_readings:
            # Älteste entfernen (Ring-Buffer)
            self.temperatures.pop(0)
            self.timestamps.pop(0)
        
        self.temperatures.append(temperature)
        self.timestamps.append(int(time.time()))
    
    def average(self):
        """Durchschnittstemperatur"""
        if not self.temperatures:
            return 0.0
        return sum(self.temperatures) / len(self.temperatures)
    
    def min_max(self):
        """Min und Max Temperatur"""
        if not self.temperatures:
            return None, None
        return min(self.temperatures), max(self.temperatures)
    
    def save_to_file(self, filename):
        """Speichert Daten binär"""
        with open(filename, 'wb') as f:
            # Anzahl Readings
            count = array.array('i', [len(self.temperatures)])
            count.tofile(f)
            # Daten
            self.timestamps.tofile(f)
            self.temperatures.tofile(f)
    
    def load_from_file(self, filename):
        """Lädt Daten aus Datei"""
        with open(filename, 'rb') as f:
            # Anzahl lesen
            count = array.array('i')
            count.fromfile(f, 1)
            n = count[0]
            # Daten lesen
            self.timestamps = array.array('i')
            self.timestamps.fromfile(f, n)
            self.temperatures = array.array('f')
            self.temperatures.fromfile(f, n)

# Verwendung
logger = SensorLogger(max_readings=100)

# Simulierte Sensor-Daten
for _ in range(50):
    temp = 20.0 + random.uniform(-5, 5)
    logger.add_reading(temp)

print(f"Readings: {len(logger.temperatures)}")
print(f"Durchschnitt: {logger.average():.2f}°C")
min_t, max_t = logger.min_max()
print(f"Min/Max: {min_t:.2f}°C / {max_t:.2f}°C")

# Speichern und laden
logger.save_to_file('sensor_data.bin')
print(f"Gespeichert ({logger.temperatures.itemsize * len(logger.temperatures)} bytes)")

# Cleanup
import os
os.remove('sensor_data.bin')
```

### 3. Bildverarbeitung (Grayscale)

```python
import array

class GrayscaleImage:
    def __init__(self, width, height, pixels=None):
        self.width = width
        self.height = height
        
        if pixels:
            self.pixels = pixels
        else:
            # Schwarzes Bild (0 = schwarz, 255 = weiß)
            self.pixels = array.array('B', [0] * (width * height))
    
    def get_pixel(self, x, y):
        """Holt Pixel-Wert an (x, y)"""
        index = y * self.width + x
        return self.pixels[index]
    
    def set_pixel(self, x, y, value):
        """Setzt Pixel-Wert an (x, y)"""
        index = y * self.width + x
        self.pixels[index] = max(0, min(255, value))  # Clamp 0-255
    
    def invert(self):
        """Invertiert Bild"""
        for i in range(len(self.pixels)):
            self.pixels[i] = 255 - self.pixels[i]
    
    def brightness(self, factor):
        """Ändert Helligkeit"""
        for i in range(len(self.pixels)):
            new_value = int(self.pixels[i] * factor)
            self.pixels[i] = max(0, min(255, new_value))
    
    def histogram(self):
        """Gibt Histogramm zurück (0-255)"""
        hist = [0] * 256
        for pixel in self.pixels:
            hist[pixel] += 1
        return hist
    
    def save_pgm(self, filename):
        """Speichert als PGM (Portable GrayMap)"""
        with open(filename, 'wb') as f:
            # PGM Header
            header = f"P5\n{self.width} {self.height}\n255\n"
            f.write(header.encode())
            # Pixel-Daten
            self.pixels.tofile(f)

# Verwendung
# Erstelle 10x10 Gradientenbild
img = GrayscaleImage(10, 10)
for y in range(10):
    for x in range(10):
        # Gradient von schwarz zu weiß
        value = int((x + y) / 18 * 255)
        img.set_pixel(x, y, value)

print(f"Bild: {img.width}x{img.height}")
print(f"Speicher: {len(img.pixels) * img.pixels.itemsize} bytes")

# Histogramm
hist = img.histogram()
non_zero = sum(1 for count in hist if count > 0)
print(f"Verwendete Graustufen: {non_zero}")

# Invertieren
img.invert()
print("Bild invertiert")
```

### 4. Numerische Berechnungen

```python
import array
import math

def vector_operations():
    """Vektor-Operationen mit arrays"""
    # Zwei Vektoren
    v1 = array.array('d', [1.0, 2.0, 3.0])
    v2 = array.array('d', [4.0, 5.0, 6.0])
    
    # Skalarprodukt
    dot_product = sum(a * b for a, b in zip(v1, v2))
    print(f"Skalarprodukt: {dot_product}")  # 32.0
    
    # Vektor-Addition
    v_add = array.array('d', (a + b for a, b in zip(v1, v2)))
    print(f"Addition: {v_add}")  # array('d', [5.0, 7.0, 9.0])
    
    # Länge (Magnitude)
    length = math.sqrt(sum(x**2 for x in v1))
    print(f"Länge v1: {length:.2f}")  # 3.74
    
    # Normalisieren
    v_norm = array.array('d', (x / length for x in v1))
    print(f"Normalisiert: {[f'{x:.3f}' for x in v_norm]}")
    
    return dot_product, v_add, length

vector_operations()

# Matrix-Multiplikation (vereinfacht)
def matrix_multiply(a, b, size):
    """Multipliziert zwei quadratische Matrizen (als flache arrays)"""
    result = array.array('d', [0.0] * (size * size))
    
    for i in range(size):
        for j in range(size):
            total = 0.0
            for k in range(size):
                total += a[i * size + k] * b[k * size + j]
            result[i * size + j] = total
    
    return result

# 2x2 Matrizen
m1 = array.array('d', [1, 2, 3, 4])  # [[1,2], [3,4]]
m2 = array.array('d', [5, 6, 7, 8])  # [[5,6], [7,8]]

result = matrix_multiply(m1, m2, 2)
print(f"\nMatrix-Multiplikation: {result}")
# array('d', [19.0, 22.0, 43.0, 50.0]) = [[19,22], [43,50]]
```

---

## Speicher-Effizienz

### Vergleich: Array vs. Liste

```python
import array
import sys

size = 1000000

# Liste von Integers
liste = list(range(size))
liste_size = sys.getsizeof(liste)

# Array von Integers
arr = array.array('i', range(size))
arr_size = sys.getsizeof(arr)

print(f"1 Million Integers:")
print(f"  Liste: {liste_size:,} bytes ({liste_size/1024/1024:.2f} MB)")
print(f"  Array: {arr_size:,} bytes ({arr_size/1024/1024:.2f} MB)")
print(f"  Ersparnis: {(1 - arr_size/liste_size)*100:.1f}%")

# Array ist deutlich speicher-effizienter!
# Liste: ~8 MB (jedes Element ist Python-Objekt)
# Array: ~4 MB (roh C integers)
```

---

## Einschränkungen und Fallstricke

### ⚠️ Fallstrick 1: Homogenität

```python
import array

# ❌ Nur ein Typ erlaubt
arr = array.array('i', [1, 2, 3])
try:
    arr.append('text')  # Geht nicht!
except TypeError as e:
    print(f"Fehler: {e}")

# Float wird konvertiert (Verlust!)
arr.append(3.7)
print(arr[-1])  # 3 (nicht 3.7!)

# ✅ Für gemischte Typen: Liste verwenden
mixed = [1, 'text', 3.14]  # OK mit Liste
```

### ⚠️ Fallstrick 2: Range-Limits

```python
import array

# signed char: -128 bis 127
small = array.array('b')
small.append(127)   # OK
print(small)

# Overflow!
small.append(128)   # Wird zu -128!
print(small[-1])    # -128 (Wrap-around)

# ✅ Richtigen Typ wählen
large = array.array('i')  # signed int
large.append(128)
print(large[-1])  # 128 - OK
```

### ⚠️ Fallstrick 3: Performance bei kleinen Daten

```python
import array
import timeit

# Bei kleinen Datenmengen kann Liste schneller sein
small_data = range(10)

def with_list():
    return list(small_data)

def with_array():
    return array.array('i', small_data)

list_time = timeit.timeit(with_list, number=100000)
array_time = timeit.timeit(with_array, number=100000)

print(f"Kleine Daten (10 Elemente):")
print(f"  Liste: {list_time:.3f}s")
print(f"  Array: {array_time:.3f}s")

# Array hat Overhead beim Erstellen!
# Vorteil wird erst bei größeren Datenmengen sichtbar
```

---

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [array module](https://docs.python.org/3/library/array.html) - Vollständige Array Referenz
- [Array Type Codes](https://docs.python.org/3/library/array.html#module-array) - Alle Typen
- [struct module](https://docs.python.org/3/library/struct.html) - Verwandtes Modul für Binary Data

---

## Verwandte Themen

- [[04_Fortgeschritten/01_Arrays/02_Array vs List|Array vs List]] - Detaillierter Vergleich
- [[04_Fortgeschritten/01_Arrays/03_Übungen Arrays|Übungen Arrays]] - Praktische Übungen
- [[02_Sequenzen/01_Listen/01_List Basics|List Basics]] - Alternative Datenstruktur
- [[01_Grundlagen/05_Bytes/01_Bytes und Bytearray|Bytes und Bytearray]] - Für Byte-Daten

---

← [[03_Mappings_und_Sets/03_Collections_Module/04_Übungen Collections|Übungen Collections]] | [[INDEX|📑 Index]] | [[04_Fortgeschritten/01_Arrays/02_Array vs List|Array vs List]] →