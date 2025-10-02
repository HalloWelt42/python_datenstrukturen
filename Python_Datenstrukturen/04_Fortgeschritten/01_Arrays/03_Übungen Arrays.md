---
tags: [python, datenstrukturen, arrays, übungen]
created: 2025-10-01
---

← [[04_Fortgeschritten/01_Arrays/02_Array vs List|Array vs List]] | [[INDEX|📑 Index]] | [[04_Fortgeschritten/02_Deque/01_Deque Basics|Deque Basics]] →

# Übungen: Arrays

> [!tip] Lernziel
> Diese Übungen helfen dir, Arrays für speichereffiziente numerische Datenverarbeitung und Binary I/O einzusetzen.

## 📝 Übung 1: Array Statistics (Leicht)

Erstelle eine Funktion `array_stats(arr)`, die Statistiken über ein numerisches Array berechnet.

**Anforderungen:**
- Min, Max, Durchschnitt, Summe
- Funktioniert mit verschiedenen Array-Typen
- Gibt Dictionary zurück

```python
import array

def array_stats(arr):
    # Dein Code hier
    pass

# Test
data = array.array('i', [10, 20, 30, 40, 50, 60, 70, 80, 90, 100])
stats = array_stats(data)

print(f"Min: {stats['min']}")
print(f"Max: {stats['max']}")
print(f"Durchschnitt: {stats['avg']:.2f}")
print(f"Summe: {stats['sum']}")
```

**Erwarteter Output:**
```
Min: 10
Max: 100
Durchschnitt: 55.00
Summe: 550
```

---

## 📝 Übung 2: Array Konvertierung (Leicht-Mittel)

Schreibe Funktionen zum sicheren Konvertieren zwischen Array-Typen.

**Anforderungen:**
- `convert_array(arr, target_typecode)`
- Prüft ob Konvertierung möglich (Range)
- Gibt neues Array oder None zurück

```python
import array

def convert_array(arr, target_typecode):
    # Dein Code hier
    # Prüfe ob alle Werte im Ziel-Range passen
    pass

# Test
# signed char → unsigned char (OK wenn alle >= 0)
arr1 = array.array('b', [10, 20, 30])
result1 = convert_array(arr1, 'B')
print(result1)  # array('B', [10, 20, 30])

# signed char mit negativen Werten → unsigned (NICHT OK)
arr2 = array.array('b', [-10, 20, 30])
result2 = convert_array(arr2, 'B')
print(result2)  # None
```

**Erwarteter Output:**
```
array('B', [10, 20, 30])
None
```

---

## 📝 Übung 3: Ringpuffer (Mittel)

Implementiere einen Ringpuffer (Circular Buffer) mit array.

**Anforderungen:**
- Klasse `RingBuffer` mit fester Größe
- Methoden: push, pop, is_full, is_empty
- Überschreibt älteste Werte bei Überlauf

```python
import array

class RingBuffer:
    def __init__(self, capacity, typecode='i'):
        # Dein Code hier
        pass
    
    def push(self, value):
        # Fügt Wert hinzu
        pass
    
    def pop(self):
        # Entfernt und gibt ältesten Wert zurück
        pass
    
    def is_full(self):
        pass
    
    def is_empty(self):
        pass
    
    def __len__(self):
        pass

# Test
rb = RingBuffer(capacity=3)

rb.push(1)
rb.push(2)
rb.push(3)
print(f"Voll: {rb.is_full()}")  # True

rb.push(4)  # Überschreibt 1
print(rb.pop())  # 2
print(rb.pop())  # 3
print(rb.pop())  # 4
print(f"Leer: {rb.is_empty()}")  # True
```

**Erwarteter Output:**
```
Voll: True
2
3
4
Leer: True
```

---

## 📝 Übung 4: Binary File I/O (Mittel)

Erstelle Funktionen zum Speichern und Laden von Arrays in Binärdateien mit Metadaten.

**Anforderungen:**
- `save_array(arr, filename)` - Speichert mit Type-Info
- `load_array(filename)` - Lädt mit korrektem Typ
- Header mit Typecode und Länge

```python
import array
import struct

def save_array(arr, filename):
    # Dein Code hier
    # Format: [typecode (1 byte)][length (4 bytes)][data]
    pass

def load_array(filename):
    # Dein Code hier
    pass

# Test
original = array.array('f', [1.1, 2.2, 3.3, 4.4, 5.5])
save_array(original, 'test.bin')

loaded = load_array('test.bin')
print(f"Typecode: {loaded.typecode}")
print(f"Daten: {loaded}")
print(f"Gleich: {list(original) == list(loaded)}")

# Cleanup
import os
os.remove('test.bin')
```

**Erwarteter Output:**
```
Typecode: f
Daten: array('f', [1.100000023841858, 2.200000047683716, ...])
Gleich: True
```

---

## 📝 Übung 5: Beweglicher Durchschnitt (Mittel)

Implementiere einen Moving Average Calculator mit array.

**Anforderungen:**
- Klasse `MovingAverage` mit Fenstergröße
- Methode `add(value)` - Fügt Wert hinzu
- Methode `average()` - Gibt aktuellen Durchschnitt
- Speichereffizient mit Ringpuffer-Logik

```python
import array

class MovingAverage:
    def __init__(self, window_size):
        # Dein Code hier
        pass
    
    def add(self, value):
        # Fügt Wert hinzu, entfernt ältesten wenn voll
        pass
    
    def average(self):
        # Gibt aktuellen Durchschnitt zurück
        pass

# Test
ma = MovingAverage(window_size=3)

ma.add(10)
print(f"Avg: {ma.average():.2f}")  # 10.00

ma.add(20)
print(f"Avg: {ma.average():.2f}")  # 15.00

ma.add(30)
print(f"Avg: {ma.average():.2f}")  # 20.00

ma.add(40)  # Entfernt 10
print(f"Avg: {ma.average():.2f}")  # 30.00
```

**Erwarteter Output:**
```
Avg: 10.00
Avg: 15.00
Avg: 20.00
Avg: 30.00
```

---

## 📝 Übung 6: Audio Waveform Generator (Mittel-Schwer)

Erstelle einen Generator für verschiedene Audio-Wellenformen.

**Anforderungen:**
- Klasse `WaveformGenerator`
- Methoden: sine, square, triangle, sawtooth
- Gibt array von 16-bit Samples zurück

```python
import array
import math

class WaveformGenerator:
    def __init__(self, sample_rate=44100):
        self.sample_rate = sample_rate
    
    def sine(self, frequency, duration):
        # Sinuswelle generieren
        pass
    
    def square(self, frequency, duration):
        # Rechteckwelle generieren
        pass
    
    def triangle(self, frequency, duration):
        # Dreieckswelle generieren
        pass

# Test
gen = WaveformGenerator()

# 440 Hz Sinuswelle, 0.1 Sekunden
sine = gen.sine(440, 0.1)
print(f"Samples: {len(sine)}")
print(f"Typecode: {sine.typecode}")
print(f"Range: {min(sine)} bis {max(sine)}")

# Rechteckwelle
square = gen.square(440, 0.1)
# Werte sollten nur min/max sein
unique_values = len(set(square))
print(f"Unique values in square: {unique_values}")  # ~2
```

**Erwarteter Output:**
```
Samples: 4410
Typecode: h
Range: -32767 bis 32767
Unique values in square: 2
```

---

## 📝 Übung 7: Image Histogram (Schwer)

Implementiere Histogramm-Berechnung für Grayscale-Bilder.

**Anforderungen:**
- Klasse `ImageHistogram`
- Bild als array('B') - Bytes 0-255
- Methoden: calculate, equalize, stretch
- Histogramm als array

```python
import array

class ImageHistogram:
    def __init__(self, image_data, width, height):
        # image_data: array('B') mit width*height Pixeln
        self.data = image_data
        self.width = width
        self.height = height
    
    def calculate(self):
        # Gibt Histogramm zurück (256 bins)
        pass
    
    def stretch(self):
        # Streckt Kontrast auf volle Range
        pass
    
    def equalize(self):
        # Histogram Equalization (vereinfacht)
        pass

# Test
# Erstelle Testbild (Gradient)
width, height = 100, 100
gradient = array.array('B')
for y in range(height):
    for x in range(width):
        value = int((x + y) / (width + height) * 255)
        gradient.append(value)

hist = ImageHistogram(gradient, width, height)

# Histogramm berechnen
histogram = hist.calculate()
print(f"Histogram bins: {len(histogram)}")
print(f"Min value in image: {min(hist.data)}")
print(f"Max value in image: {max(hist.data)}")

# Kontrast strecken
hist.stretch()
print(f"Nach stretch - Min: {min(hist.data)}, Max: {max(hist.data)}")
```

**Erwarteter Output:**
```
Histogram bins: 256
Min value in image: 0
Max value in image: 127
Nach stretch - Min: 0, Max: 255
```

---

## 📝 Übung 8: Vector Math Library (Schwer)

Erstelle eine Mini-Bibliothek für Vektor-Operationen mit arrays.

**Anforderungen:**
- Funktionen: add, subtract, dot, cross, normalize
- Funktioniert mit array('d')
- Effiziente Implementierung

```python
import array
import math

def vector_add(v1, v2):
    # Element-weise Addition
    pass

def vector_dot(v1, v2):
    # Skalarprodukt
    pass

def vector_length(v):
    # Länge/Magnitude
    pass

def vector_normalize(v):
    # Normalisiert auf Länge 1
    pass

def vector_cross(v1, v2):
    # Kreuzprodukt (nur 3D)
    pass

# Test
v1 = array.array('d', [1.0, 2.0, 3.0])
v2 = array.array('d', [4.0, 5.0, 6.0])

# Addition
v_add = vector_add(v1, v2)
print(f"Add: {list(v_add)}")  # [5.0, 7.0, 9.0]

# Skalarprodukt
dot = vector_dot(v1, v2)
print(f"Dot: {dot}")  # 32.0

# Länge
length = vector_length(v1)
print(f"Length: {length:.3f}")  # 3.742

# Normalisiert
v_norm = vector_normalize(v1)
print(f"Normalized length: {vector_length(v_norm):.3f}")  # 1.000

# Kreuzprodukt
cross = vector_cross(v1, v2)
print(f"Cross: {list(cross)}")  # [-3.0, 6.0, -3.0]
```

**Erwarteter Output:**
```
Add: [5.0, 7.0, 9.0]
Dot: 32.0
Length: 3.742
Normalized length: 1.000
Cross: [-3.0, 6.0, -3.0]
```

---

## 📝 Übung 9: Memory-Efficient Data Logger (Schwer)

Implementiere einen speichereffizienten Data Logger mit Kompression.

**Anforderungen:**
- Klasse `DataLogger` mit max_size
- Speichert Timestamps und Values
- Auto-Downsampling bei Überlauf
- Binary Save/Load

```python
import array
import time

class DataLogger:
    def __init__(self, max_size=1000):
        # Dein Code hier
        # Timestamps als 'I' (unsigned int)
        # Values als 'f' (float)
        pass
    
    def log(self, value, timestamp=None):
        # Fügt Datenpunkt hinzu
        # Downsampling wenn voll
        pass
    
    def downsample(self):
        # Reduziert Datenmenge auf Hälfte (Average Paare)
        pass
    
    def get_data(self):
        # Gibt (timestamps, values) zurück
        pass
    
    def save(self, filename):
        # Speichert binär
        pass
    
    def load(self, filename):
        # Lädt aus Datei
        pass

# Test
logger = DataLogger(max_size=5)

# Daten loggen
for i in range(10):
    logger.log(float(i * 10))
    time.sleep(0.01)

timestamps, values = logger.get_data()
print(f"Data points: {len(values)}")
print(f"Values: {[f'{v:.1f}' for v in values]}")

# Nach Downsampling sollten es ~5 sein
```

**Erwarteter Output:**
```
Data points: 5
Values: ['5.0', '25.0', '45.0', '65.0', '85.0']
```

---

## 📝 Übung 10: Signal Processing Suite (Experte)

Erstelle eine Signal-Processing Bibliothek mit arrays.

**Anforderungen:**
- Klasse `SignalProcessor`
- FFT (vereinfacht), Filter, Convolution
- Window Functions (Hamming, Hann)
- Spektralanalyse

```python
import array
import math

class SignalProcessor:
    def __init__(self, sample_rate=44100):
        self.sample_rate = sample_rate
    
    def apply_window(self, signal, window_type='hann'):
        # Wendet Fensterfunktion an
        pass
    
    def lowpass_filter(self, signal, cutoff_freq):
        # Einfacher Lowpass (Moving Average)
        pass
    
    def detect_peaks(self, signal, threshold):
        # Findet Peaks über Threshold
        pass
    
    def rms(self, signal):
        # Root Mean Square (Lautstärke)
        pass
    
    def zero_crossings(self, signal):
        # Zählt Nulldurchgänge (grobe Frequenz)
        pass

# Test
proc = SignalProcessor()

# Test-Signal: 440Hz Sinus
duration = 0.1
samples = int(duration * proc.sample_rate)
signal = array.array('d')

for i in range(samples):
    t = i / proc.sample_rate
    value = math.sin(2 * math.pi * 440 * t)
    signal.append(value)

# RMS berechnen
rms = proc.rms(signal)
print(f"RMS: {rms:.3f}")  # ~0.707 für Sinuswelle

# Nulldurchgänge
crossings = proc.zero_crossings(signal)
estimated_freq = crossings / (2 * duration)
print(f"Geschätzte Frequenz: {estimated_freq:.0f} Hz")  # ~440

# Window anwenden
windowed = proc.apply_window(signal, 'hann')
print(f"Windowed signal: {len(windowed)} samples")
```

**Erwarteter Output:**
```
RMS: 0.707
Geschätzte Frequenz: 440 Hz
Windowed signal: 4410 samples
```

---

## 💡 Lösungsstrategien

### Für Übung 1-3 (Grundlagen):
- Nutze built-in functions (min, max, sum)
- Type-Checking mit typecode
- Modulo für Ringpuffer-Index

### Für Übung 4-6 (Fortgeschritten):
- struct module für Binary I/O
- Ringpuffer-Logik mit Index
- Math-Funktionen für Waveforms

### Für Übung 7-9 (Profi):
- Array-Manipulation für Bildverarbeitung
- Vektor-Operationen mit zip()
- Downsampling mit Averaging

### Für Übung 10 (Experte):
- Window Functions mathematisch
- Moving Average für Filter
- Array-Operationen optimieren

---

## 🔍 Selbst-Check

Nach diesen Übungen solltest du:

✅ Arrays für numerische Daten nutzen können
✅ Binary I/O mit arrays beherrschen
✅ Ringpuffer implementieren können
✅ Audio/Bild-Daten mit arrays verarbeiten
✅ Vektor-Operationen durchführen
✅ Speichereffiziente Lösungen entwickeln

---

## 🎯 Bonus-Herausforderungen

### Challenge 1: WAV File Library

Erstelle eine vollständige WAV-Bibliothek:
- Lesen und Schreiben von WAV-Dateien
- Unterstützung für Stereo
- Sample-Rate Konvertierung
- Effects (Echo, Reverb)

### Challenge 2: Matrix Library

Implementiere Matrix-Operationen:
- 2D Arrays als flache arrays
- Matrix-Multiplikation
- Transpose, Determinante
- LU-Zerlegung (vereinfacht)

### Challenge 3: Real-time Data Acquisition

Simuliere Echtzeit-Datenerfassung:
- Kontinuierliches Logging
- Rolling Window Statistics
- Trigger Detection
- Export zu verschiedenen Formaten

---

## 📚 Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [array module](https://docs.python.org/3/library/array.html) - Array Referenz
- [struct module](https://docs.python.org/3/library/struct.html) - Binary Data
- [wave module](https://docs.python.org/3/library/wave.html) - WAV Files
- [math module](https://docs.python.org/3/library/math.html) - Mathematische Funktionen

---

## 🎓 Verwandte Themen

- [[04_Fortgeschritten/01_Arrays/01_Array Module|Array Module]] - Array im Detail
- [[04_Fortgeschritten/01_Arrays/02_Array vs List|Array vs List]] - Vergleich
- [[01_Grundlagen/05_Bytes/01_Bytes und Bytearray|Bytes und Bytearray]] - Binary Data
- [[06_Praxis/04_Lösungen/Lösungen Teil 4|Lösungen Teil 4]] - Musterlösungen

---

← [[04_Fortgeschritten/01_Arrays/02_Array vs List|Array vs List]] | [[INDEX|📑 Index]] | [[04_Fortgeschritten/02_Deque/01_Deque Basics|Deque Basics]] →