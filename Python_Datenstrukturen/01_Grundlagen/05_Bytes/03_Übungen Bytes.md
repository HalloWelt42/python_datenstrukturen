---
tags: [python, datenstrukturen, bytes, übungen]
created: 2025-10-01
---

← [[01_Grundlagen/05_Bytes/02_Memoryview|Memoryview]] | [[INDEX|📑 Index]] | [[02_Sequenzen/01_Listen/01_List Basics|List Basics]] →

# Übungen: Bytes und Bytearray

> [!tip] Lernziel
> Diese Übungen festigen dein Verständnis von Bytes, Bytearray und Memoryview. Du übst Encoding, Manipulation von Binärdaten und effizienten Speicherzugriff.

## 📝 Übungsaufgaben

### Übung 1: Text-Encoding (Leicht)

Schreibe eine Funktion `analyse_encoding()`, die einen Text in verschiedenen Encodings kodiert und die Länge der resultierenden Bytes zurückgibt.

**Anforderungen:**
- Kodiere den Text in UTF-8, UTF-16 und UTF-32
- Gib die Länge jeder Kodierung aus
- Vergleiche die Ergebnisse

**Beispiel:**
```python
def analyse_encoding(text: str) -> dict:
    # Dein Code hier
    pass

# Test
text = "Python 🐍"
ergebnis = analyse_encoding(text)
print(ergebnis)
```

**Erwarteter Output:**
```
{
    'utf-8': 10,
    'utf-16': 16,
    'utf-32': 32
}
```

<details>
<summary>💡 Hinweis</summary>

- Verwende die `.encode()` Methode mit verschiedenen Encodings
- Nutze `len()` um die Byte-Länge zu ermitteln
- UTF-8 ist variabel, UTF-16 und UTF-32 haben feste Breiten
</details>

---

### Übung 2: Hexadecimal-Konverter (Leicht-Mittel)

Erstelle Funktionen zum Konvertieren zwischen Bytes und Hexadecimal-Strings.

**Anforderungen:**
- `bytes_to_hex()`: Konvertiert bytes zu Hex-String
- `hex_to_bytes()`: Konvertiert Hex-String zu bytes
- Unterstütze beide Formate: "AABBCC" und "AA:BB:CC"

**Beispiel:**
```python
def bytes_to_hex(data: bytes, separator: str = "") -> str:
    # Dein Code hier
    pass

def hex_to_bytes(hex_string: str) -> bytes:
    # Dein Code hier
    pass

# Test
data = b'\xAA\xBB\xCC\xDD'
hex_str = bytes_to_hex(data, separator=":")
print(hex_str)
print(hex_to_bytes(hex_str))
```

**Erwarteter Output:**
```
AA:BB:CC:DD
b'\xaa\xbb\xcc\xdd'
```

<details>
<summary>💡 Hinweis</summary>

- Verwende `.hex()` Methode für bytes zu hex
- Nutze `bytes.fromhex()` für hex zu bytes
- Entferne Separatoren mit `.replace()` vor der Konvertierung
</details>

---

### Übung 3: Bytearray-Manipulation (Mittel)

Implementiere eine Funktion `modify_bytes()`, die ein Bytearray an verschiedenen Positionen modifiziert.

**Anforderungen:**
- Setze Byte an Index auf neuen Wert
- Ersetze einen Bereich von Bytes
- Füge Bytes an Position ein
- Lösche Bytes aus einem Bereich

**Beispiel:**
```python
def modify_bytes(data: bytearray, 
                 set_at: tuple = None,      # (index, wert)
                 replace: tuple = None,     # (start, end, neue_bytes)
                 insert: tuple = None,      # (index, neue_bytes)
                 delete: tuple = None) -> bytearray:  # (start, end)
    # Dein Code hier
    pass

# Test
data = bytearray(b'ABCDEFGH')
data = modify_bytes(data, set_at=(0, ord('Z')))
print(data)

data = modify_bytes(data, replace=(2, 4, b'XY'))
print(data)
```

**Erwarteter Output:**
```
bytearray(b'ZBCDEFGH')
bytearray(b'ZBXYEFGH')
```

<details>
<summary>💡 Hinweis</summary>

- Verwende Index-Zuweisung für `set_at`
- Nutze Slicing mit Zuweisung für `replace`
- Verwende `.insert()` oder Slicing für `insert`
- Nutze `del` Statement für `delete`
</details>

---

### Übung 4: Memoryview Optimierung (Mittel)

Schreibe eine Funktion `sum_large_array()`, die große Zahlen-Arrays effizient summiert.

**Anforderungen:**
- Erstelle ein großes bytearray mit Zahlen (0-255)
- Berechne Summe mit direktem Zugriff (langsam)
- Berechne Summe mit memoryview (schnell)
- Vergleiche die Performance

**Beispiel:**
```python
import time

def sum_direct(data: bytearray) -> int:
    # Dein Code hier
    pass

def sum_with_view(data: bytearray) -> int:
    # Dein Code hier
    pass

# Test
data = bytearray(range(256)) * 10000

start = time.perf_counter()
result1 = sum_direct(data)
time1 = time.perf_counter() - start

start = time.perf_counter()
result2 = sum_with_view(data)
time2 = time.perf_counter() - start

print(f"Direkt: {result1}, Zeit: {time1:.4f}s")
print(f"Memoryview: {result2}, Zeit: {time2:.4f}s")
print(f"Speedup: {time1/time2:.2f}x")
```

**Erwarteter Output:** (Ungefähr)
```
Direkt: 327680000, Zeit: 0.0234s
Memoryview: 327680000, Zeit: 0.0156s
Speedup: 1.50x
```

<details>
<summary>💡 Hinweis</summary>

- Bei direktem Zugriff: einfach durch das bytearray iterieren
- Mit memoryview: erst `memoryview()` erstellen, dann iterieren
- Memoryview vermeidet Kopien bei jedem Zugriff
</details>

---

### Übung 5: Binary Protocol Parser (Schwer)

Implementiere einen Parser für ein einfaches binäres Protokoll.

**Protokoll-Format:**
```
Header (4 Bytes):
  - Byte 0: Version (uint8)
  - Byte 1: Message Type (uint8)
  - Bytes 2-3: Payload Length (uint16, big-endian)

Payload (variable Länge):
  - UTF-8 Text der angegebenen Länge
```

**Anforderungen:**
- `create_message()`: Erstellt eine binäre Nachricht
- `parse_message()`: Parst eine binäre Nachricht
- Validiere Version und Length
- Behandle Fehler (zu kurz, falsche Version, etc.)

**Beispiel:**
```python
import struct

def create_message(version: int, msg_type: int, payload: str) -> bytes:
    # Dein Code hier
    pass

def parse_message(data: bytes) -> dict:
    # Dein Code hier
    # Rückgabe: {'version': int, 'type': int, 'payload': str}
    pass

# Test
msg = create_message(1, 10, "Hello World")
print(f"Binär: {msg.hex()}")
print(f"Länge: {len(msg)} Bytes")

parsed = parse_message(msg)
print(f"Parsed: {parsed}")
```

**Erwarteter Output:**
```
Binär: 010a000b48656c6c6f20576f726c64
Länge: 15 Bytes
Parsed: {'version': 1, 'type': 10, 'payload': 'Hello World'}
```

<details>
<summary>💡 Hinweis</summary>

- Verwende `struct.pack()` für create_message
- Format-String: `'>BBH'` (big-endian, 2x uint8, 1x uint16)
- Nutze `struct.unpack()` für parse_message
- Validiere: `len(data) >= 4` und `len(payload) == expected_length`
</details>

---

### Übung 6: Byte-Buffer mit Wrapping (Schwer)

Implementiere einen zirkulären Byte-Buffer (Ring-Buffer).

**Anforderungen:**
- Feste Größe (z.B. 10 Bytes)
- `write()`: Schreibt Bytes (überschreibt alte Daten bei Überlauf)
- `read()`: Liest n Bytes vom Anfang
- `peek()`: Zeigt Daten ohne zu entfernen
- Effiziente Implementierung mit memoryview

**Beispiel:**
```python
class CircularBuffer:
    def __init__(self, size: int):
        # Dein Code hier
        pass
    
    def write(self, data: bytes) -> int:
        """Schreibt Bytes, gibt Anzahl geschriebener Bytes zurück"""
        pass
    
    def read(self, n: int) -> bytes:
        """Liest und entfernt n Bytes"""
        pass
    
    def peek(self, n: int) -> bytes:
        """Zeigt n Bytes ohne zu entfernen"""
        pass
    
    def available(self) -> int:
        """Gibt Anzahl verfügbarer Bytes zurück"""
        pass

# Test
buffer = CircularBuffer(10)
buffer.write(b'ABCDEFGH')
print(f"Verfügbar: {buffer.available()}")

data = buffer.read(3)
print(f"Gelesen: {data}")
print(f"Verfügbar: {buffer.available()}")

buffer.write(b'12345')
print(f"Verfügbar: {buffer.available()}")
print(f"Peek: {buffer.peek(5)}")
```

**Erwarteter Output:**
```
Verfügbar: 8
Gelesen: b'ABC'
Verfügbar: 5
Verfügbar: 10
Peek: b'DEFGH'
```

<details>
<summary>💡 Hinweis</summary>

- Verwende ein bytearray fester Größe
- Tracke `read_pos` und `write_pos` Indizes
- Bei Wrapping: `pos % size` für zirkuläres Verhalten
- Memoryview für effiziente Slicing-Operationen
</details>

---

## 🎯 Bonus-Challenge: Image Header Parser

**Aufgabe:** Schreibe einen Parser, der die Header von PNG und JPEG Bildern erkennt und grundlegende Informationen extrahiert.

**PNG-Header:**
- Bytes 0-7: Signatur `89 50 4E 47 0D 0A 1A 0A`
- Bytes 16-19: Width (uint32, big-endian)
- Bytes 20-23: Height (uint32, big-endian)

**JPEG-Header:**
- Bytes 0-1: Signatur `FF D8`
- Letzten 2 Bytes: `FF D9`

```python
def identify_image(data: bytes) -> dict:
    """
    Erkennt Bildformat und extrahiert Infos
    Rückgabe: {'format': str, 'width': int, 'height': int, 'valid': bool}
    """
    # Dein Code hier
    pass

# Test mit echten Bild-Bytes oder Mock-Daten
png_mock = bytes([0x89, 0x50, 0x4E, 0x47, 0x0D, 0x0A, 0x1A, 0x0A]) + \
           b'\x00' * 8 + \
           b'\x00\x00\x04\x00' + \  # Width: 1024
           b'\x00\x00\x03\x00'      # Height: 768

info = identify_image(png_mock)
print(info)
```

---

## 📊 Lösungshinweise

> [!note] Selbsttest
> Versuche alle Übungen selbst zu lösen, bevor du die Lösungen ansiehst. Die Lösungen findest du in [[06_Praxis/04_Lösungen/Lösungen Teil 1|Lösungen Teil 1]].

**Bewertung deiner Lösungen:**
- ✅ Funktioniert mit Test-Daten
- ✅ Behandelt Edge-Cases (leere Eingabe, zu groß, etc.)
- ✅ Nutzt passende Datentypen (bytes vs bytearray)
- ✅ Effizienter Code (memoryview wo sinnvoll)
- ✅ Gute Fehlerbehandlung

---

## 🔗 Weiterführende Themen

Nach diesen Übungen solltest du bereit sein für:
- [[02_Sequenzen/01_Listen/01_List Basics|Listen]] - Die wichtigste sequenzielle Datenstruktur
- [[02_Sequenzen/02_Tupel/01_Tuple Basics|Tupel]] - Unveränderliche Sequenzen
- [[04_Fortgeschritten/01_Arrays/01_Array Module|Arrays]] - Typisierte numerische Arrays

---

## 📚 Python-Dokumentation

**Offizielle Ressourcen:**
- [Bytes Objects](https://docs.python.org/3/library/stdtypes.html#bytes-objects) - Bytes-Objekte im Detail
- [Bytearray Objects](https://docs.python.org/3/library/stdtypes.html#bytearray-objects) - Mutable Byte-Arrays
- [Memory Views](https://docs.python.org/3/library/stdtypes.html#memory-views) - Effizienter Speicherzugriff
- [struct Module](https://docs.python.org/3/library/struct.html) - Binary Data Parsing
- [codecs Module](https://docs.python.org/3/library/codecs.html) - Codec Registry

---

← [[01_Grundlagen/05_Bytes/02_Memoryview|Memoryview]] | [[INDEX|📑 Index]] | [[02_Sequenzen/01_Listen/01_List Basics|List Basics]] →