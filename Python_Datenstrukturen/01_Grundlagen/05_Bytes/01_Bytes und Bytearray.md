---
tags: [python, datenstrukturen, bytes, bytearray, binär]
created: 2025-10-01
---

← [[06_Übungen Strings|Übungen Strings]] | [[INDEX|📑 Index]] | [[02_Memoryview|Memoryview]] →

# Bytes und Bytearray

> [!tip] Lernziel
> Du verstehst den Unterschied zwischen Strings und Bytes, kennst bytes und bytearray, und weißt wann du sie einsetzt

## Bytes vs. Strings

**String** = Sequenz von Unicode-Zeichen (Text)
**Bytes** = Sequenz von Bytes (Binärdaten, 0-255)

```python
# String (Unicode-Text)
text = "Café"
print(type(text))      # <class 'str'>
print(len(text))       # 4 (4 Unicode-Zeichen)

# Bytes (Binärdaten)
data = b"Caf\xc3\xa9"
print(type(data))      # <class 'bytes'>
print(len(data))       # 5 (5 Bytes)

# String zu Bytes
text_bytes = text.encode('utf-8')
print(text_bytes)      # b'Caf\xc3\xa9'

# Bytes zu String
text_wieder = text_bytes.decode('utf-8')
print(text_wieder)     # Café
```

## Bytes (Immutable)

### Bytes erstellen

```python
# 1. Bytes-Literal (b-Prefix)
data1 = b"Hello"
print(data1)           # b'Hello'

# 2. bytes() Konstruktor - aus Iterable
data2 = bytes([72, 101, 108, 108, 111])
print(data2)           # b'Hello'

# 3. bytes() - mit Länge (nullgefüllt)
data3 = bytes(5)
print(data3)           # b'\x00\x00\x00\x00\x00'

# 4. Aus String encodieren
data4 = "Hello".encode('utf-8')
print(data4)           # b'Hello'

# 5. Mit Hex-Werten
data5 = b"\x48\x65\x6c\x6c\x6f"
print(data5)           # b'Hello'

# 6. bytes.fromhex()
data6 = bytes.fromhex('48656c6c6f')
print(data6)           # b'Hello'
```

### Bytes sind immutable

```python
data = b"Hello"

# ❌ Änderung nicht möglich
# data[0] = 74  # TypeError: 'bytes' object does not support item assignment

# ✅ Neues Bytes-Objekt erstellen
neue_data = b"J" + data[1:]
print(neue_data)  # b'Jello'

# Alte ID vs. neue ID
print(id(data))       # z.B. 140234567890
print(id(neue_data))  # Andere ID (neues Objekt)
```

### Bytes-Operationen

```python
data = b"Python"

# Indexierung
print(data[0])         # 80 (ASCII-Wert von 'P')
print(chr(data[0]))    # 'P'

# Slicing
print(data[0:3])       # b'Pyt'
print(data[::-1])      # b'nohtyP' (umgekehrt)

# Länge
print(len(data))       # 6

# Iteration
for byte in data:
    print(byte, end=' ')  # 80 121 116 104 111 110

# Konkatenation
data2 = b" Programming"
result = data + data2
print(result)          # b'Python Programming'

# Wiederholung
print(b"Hi" * 3)       # b'HiHiHi'

# Membership
print(b"Py" in data)   # True
print(80 in data)      # True (einzelnes Byte)
```

### Bytes-Methoden

```python
data = b"Hello World"

# String-ähnliche Methoden
print(data.upper())           # b'HELLO WORLD'
print(data.lower())           # b'hello world'
print(data.replace(b"World", b"Python"))  # b'Hello Python'

# Split und Join
parts = data.split(b" ")
print(parts)                  # [b'Hello', b'World']
print(b"-".join(parts))       # b'Hello-World'

# Strip
data2 = b"  Hello  "
print(data2.strip())          # b'Hello'

# Find
print(data.find(b"World"))    # 6
print(data.startswith(b"Hel")) # True
print(data.endswith(b"ld"))   # True

# Count
print(data.count(b"l"))       # 3
```

### Bytes zu/von Hex

```python
data = b"Python"

# Zu Hexadezimal
hex_str = data.hex()
print(hex_str)         # '507974686f6e'

# Mit Separator (Python 3.5+)
hex_str = data.hex(' ')
print(hex_str)         # '50 79 74 68 6f 6e'

# Von Hexadezimal
data_wieder = bytes.fromhex(hex_str)
print(data_wieder)     # b'Python'

# Praktisch für Debugging
def print_hex(data):
    """Zeigt Bytes in Hex-Format"""
    hex_pairs = [f"{b:02x}" for b in data]
    return " ".join(hex_pairs)

print(print_hex(b"ABC"))  # 41 42 43
```

## Bytearray (Mutable)

### Bytearray erstellen

```python
# 1. bytearray-Literal (geht nicht, nur Konstruktor!)
# arr = ba"Hello"  # SyntaxError!

# 2. bytearray() Konstruktor
arr1 = bytearray(b"Hello")
print(arr1)            # bytearray(b'Hello')

# 3. Aus Iterable
arr2 = bytearray([72, 101, 108, 108, 111])
print(arr2)            # bytearray(b'Hello')

# 4. Mit Länge (nullgefüllt)
arr3 = bytearray(5)
print(arr3)            # bytearray(b'\x00\x00\x00\x00\x00')

# 5. Aus String
arr4 = bytearray("Hello", 'utf-8')
print(arr4)            # bytearray(b'Hello')
```

### Bytearray ist mutable

```python
arr = bytearray(b"Hello")

# ✅ In-Place Änderung möglich
arr[0] = 74            # 'J'
print(arr)             # bytearray(b'Jello')

# Gleiche Objekt-ID
alte_id = id(arr)
arr[1] = 65            # 'A'
neue_id = id(arr)
print(alte_id == neue_id)  # True (gleiches Objekt)

# Slice-Assignment
arr[2:5] = b"zzz"
print(arr)             # bytearray(b'Jazzz')

# Mit unterschiedlicher Länge
arr[2:5] = b"y"
print(arr)             # bytearray(b'Jay')
```

### Bytearray-Methoden

```python
arr = bytearray(b"Hello")

# List-ähnliche Methoden
arr.append(33)         # Füge '!' hinzu
print(arr)             # bytearray(b'Hello!')

# Extend
arr.extend(b" World")
print(arr)             # bytearray(b'Hello! World')

# Insert
arr.insert(0, 62)      # '>' am Anfang
print(arr)             # bytearray(b'>Hello! World')

# Remove (erstes Vorkommen)
arr.remove(62)
print(arr)             # bytearray(b'Hello! World')

# Pop
last_byte = arr.pop()
print(chr(last_byte))  # 'd'
print(arr)             # bytearray(b'Hello! Worl')

# Clear
arr2 = bytearray(b"Test")
arr2.clear()
print(arr2)            # bytearray(b'')

# Reverse
arr3 = bytearray(b"Python")
arr3.reverse()
print(arr3)            # bytearray(b'nohtyP')
```

### Bytearray Performance

```python
import time

# bytearray ist effizienter für häufige Änderungen
n = 10000

# ❌ bytes: Viele neue Objekte
start = time.perf_counter()
data = b""
for i in range(n):
    data = data + bytes([i % 256])  # Jedes Mal neues bytes
t1 = time.perf_counter() - start

# ✅ bytearray: In-Place Modifikation
start = time.perf_counter()
arr = bytearray()
for i in range(n):
    arr.append(i % 256)  # Modifiziert gleiches Objekt
t2 = time.perf_counter() - start

print(f"bytes:     {t1:.4f}s")
print(f"bytearray: {t2:.4f}s ({t1/t2:.1f}x faster)")
# bytearray ist ~100x schneller für viele Änderungen!
```

## Bytes vs. Bytearray Vergleich

| Feature | bytes | bytearray |
|---------|-------|-----------|
| Mutability | Immutable | Mutable |
| Literal | `b"text"` | Kein Literal |
| Änderungen | Erstellt neue Objekte | In-Place |
| Performance | Schnell für Read | Schnell für Write |
| Memory | Effizienter bei vielen Kopien | Mehr Overhead |
| Use Case | Konstante Daten | Häufige Änderungen |
| Hashable | Ja (kann Dict-Key sein) | Nein |

```python
# bytes ist hashable
dict_mit_bytes = {b"key": "value"}
print(dict_mit_bytes[b"key"])  # value

# bytearray ist nicht hashable
try:
    dict_mit_bytearray = {bytearray(b"key"): "value"}
except TypeError as e:
    print(f"Fehler: {e}")  # unhashable type: 'bytearray'

# bytearray zu bytes konvertieren
arr = bytearray(b"Hello")
data = bytes(arr)
print(type(data))  # <class 'bytes'>
```

## Praktische Anwendungen

### Anwendung 1: Binärdateien lesen/schreiben

```python
# Binärdatei schreiben
data = bytes([0xFF, 0x00, 0xAB, 0xCD])
with open('binary.dat', 'wb') as f:
    f.write(data)

# Binärdatei lesen
with open('binary.dat', 'rb') as f:
    data_gelesen = f.read()
    print(data_gelesen.hex())  # ff00abcd

# Große Dateien in Chunks
def lese_in_chunks(filename, chunk_size=1024):
    """Liest Datei in Chunks"""
    with open(filename, 'rb') as f:
        while True:
            chunk = f.read(chunk_size)
            if not chunk:
                break
            yield chunk

# Verwendung
for chunk in lese_in_chunks('large_file.bin', 4096):
    # Verarbeite Chunk
    print(f"Chunk: {len(chunk)} bytes")
```

### Anwendung 2: Netzwerk-Protokolle

```python
def erstelle_http_request(host, path="/"):
    """Erstellt HTTP GET Request als Bytes"""
    request = (
        f"GET {path} HTTP/1.1\r\n"
        f"Host: {host}\r\n"
        f"Connection: close\r\n"
        f"\r\n"
    )
    return request.encode('ascii')

# Verwendung
request_bytes = erstelle_http_request("example.com")
print(request_bytes)
# b'GET / HTTP/1.1\r\nHost: example.com\r\nConnection: close\r\n\r\n'

def parse_http_response(response_bytes):
    """Parsed HTTP Response"""
    # Trennung Header/Body
    header_end = response_bytes.find(b'\r\n\r\n')
    if header_end == -1:
        return None
    
    header_bytes = response_bytes[:header_end]
    body_bytes = response_bytes[header_end + 4:]
    
    # Header zu String
    header_str = header_bytes.decode('ascii')
    
    return {
        'header': header_str,
        'body': body_bytes
    }
```

### Anwendung 3: Checksums und Hashing

```python
import hashlib

def berechne_checksumme(data):
    """Berechnet verschiedene Checksummen"""
    if isinstance(data, str):
        data = data.encode('utf-8')
    
    return {
        'md5': hashlib.md5(data).hexdigest(),
        'sha1': hashlib.sha1(data).hexdigest(),
        'sha256': hashlib.sha256(data).hexdigest()
    }

# Verwendung
text = "Hello World"
checksums = berechne_checksumme(text)
print(f"MD5:    {checksums['md5']}")
print(f"SHA1:   {checksums['sha1']}")
print(f"SHA256: {checksums['sha256']}")

# Datei-Checksumme
def datei_checksum(filename):
    """Berechnet SHA256 einer Datei"""
    sha256 = hashlib.sha256()
    with open(filename, 'rb') as f:
        while True:
            chunk = f.read(4096)
            if not chunk:
                break
            sha256.update(chunk)
    return sha256.hexdigest()
```

### Anwendung 4: Binäre Datenstrukturen

```python
import struct

def pack_data(id, timestamp, value):
    """Packt Daten in Binärformat"""
    # Format: unsigned int, unsigned long long, float
    # < = Little Endian
    # I = unsigned int (4 bytes)
    # Q = unsigned long long (8 bytes)
    # f = float (4 bytes)
    return struct.pack('<IQf', id, timestamp, value)

def unpack_data(data):
    """Entpackt Binärdaten"""
    return struct.unpack('<IQf', data)

# Verwendung
packed = pack_data(42, 1234567890, 3.14)
print(f"Packed: {packed.hex()}")  # Binärdaten als Hex
print(f"Size: {len(packed)} bytes")  # 16 bytes

unpacked = unpack_data(packed)
print(f"Unpacked: ID={unpacked[0]}, Time={unpacked[1]}, Value={unpacked[2]}")
```

### Anwendung 5: Bild-Manipulation (einfach)

```python
def erstelle_bmp_header(width, height):
    """Erstellt BMP-Header (vereinfacht)"""
    # BMP Header ist 54 bytes
    file_size = 54 + (width * height * 3)  # 3 bytes pro Pixel (RGB)
    
    header = bytearray(54)
    
    # Signature "BM"
    header[0:2] = b'BM'
    
    # File size
    header[2:6] = file_size.to_bytes(4, 'little')
    
    # Pixel data offset
    header[10:14] = (54).to_bytes(4, 'little')
    
    # DIB header size
    header[14:18] = (40).to_bytes(4, 'little')
    
    # Width and Height
    header[18:22] = width.to_bytes(4, 'little')
    header[22:26] = height.to_bytes(4, 'little')
    
    # Planes and bits per pixel
    header[26:28] = (1).to_bytes(2, 'little')
    header[28:30] = (24).to_bytes(2, 'little')  # 24-bit RGB
    
    return bytes(header)

# Erstelle 2x2 rotes Bild
header = erstelle_bmp_header(2, 2)
pixels = b'\x00\x00\xff' * 4  # 4 rote Pixel (BGR-Format!)

with open('red.bmp', 'wb') as f:
    f.write(header)
    f.write(pixels)
```

## Häufige Fallstricke

### Fallstrick 1: Bytes vs. String verwechseln

```python
# ❌ Bytes und Strings mischen
text = "Hello"
data = b"World"

try:
    result = text + data
except TypeError as e:
    print(f"Fehler: {e}")  # can only concatenate str to str

# ✅ Erst konvertieren
result = text + data.decode('utf-8')
# oder
result = text.encode('utf-8') + data
```

### Fallstrick 2: Bytes[index] gibt Integer

```python
data = b"Hello"

# ⚠️ Einzelnes Byte ist Integer!
print(data[0])         # 72 (nicht b'H')
print(type(data[0]))   # <class 'int'>

# Für einzelnes Byte als bytes:
print(data[0:1])       # b'H'
print(bytes([data[0]])) # b'H'

# ASCII-Zeichen
print(chr(data[0]))    # H
```

### Fallstrick 3: Encoding vergessen

```python
text = "Café"

# ❌ Direkt zu bytes geht nicht
# data = bytes(text)  # TypeError!

# ✅ Erst encodieren
data = text.encode('utf-8')
print(data)  # b'Caf\xc3\xa9'

# ❌ Bytes mit nicht-ASCII Zeichen im Literal
# data = b"Café"  # SyntaxError: bytes can only contain ASCII

# ✅ Mit Escape-Sequenzen
data = b"Caf\xc3\xa9"  # UTF-8 codiert
```

### Fallstrick 4: Bytearray und Hashable

```python
# bytes ist hashable
data = b"Hello"
set_mit_bytes = {data}  # OK
dict_key = {data: "value"}  # OK

# bytearray ist nicht hashable
arr = bytearray(b"Hello")
try:
    set_mit_arr = {arr}
except TypeError as e:
    print(f"Fehler: {e}")  # unhashable type

# ✅ Konvertiere zu bytes wenn nötig
set_mit_arr = {bytes(arr)}  # OK
```

### Fallstrick 5: Bytearray-Größe ändert sich

```python
arr = bytearray(b"Hello")

# Größe kann sich ändern
print(len(arr))  # 5

arr.extend(b" World")
print(len(arr))  # 11

# Bei bytes: Immer neues Objekt
data = b"Hello"
data = data + b" World"  # Neues bytes-Objekt
```

## Performance-Hinweise

### Zeitkomplexität

| Operation | bytes | bytearray |
|-----------|-------|-----------|
| Indexing | O(1) | O(1) |
| Slicing | O(k) | O(k) |
| Concat (+) | O(n+m) | O(n+m) |
| append() | - | O(1) amortized |
| extend() | - | O(k) |
| Iteration | O(n) | O(n) |

### Memory und Performance

```python
import sys

# bytes vs. bytearray Memory
data_bytes = b"X" * 1000
data_array = bytearray(b"X" * 1000)

print(f"bytes:     {sys.getsizeof(data_bytes)} bytes")
print(f"bytearray: {sys.getsizeof(data_array)} bytes")
# bytearray hat mehr Overhead

# Wann was nutzen?
# bytes: Daten ändern sich nicht, viele Kopien
# bytearray: Häufige Modifikationen

# ✅ Effizient: bytearray für Buffer
buffer = bytearray()
for i in range(1000):
    buffer.append(i % 256)

# ❌ Ineffizient: bytes-Konkatenation
data = b""
for i in range(1000):
    data = data + bytes([i % 256])  # Langsam!
```

## Übungsaufgaben

### Übung 1: Hex Dump (Leicht)

Zeige Bytes als Hex-Dump wie in Hex-Editoren.

**Erwarteter Output:**
```python
data = b"Hello World!"
hex_dump(data)
# 00000000: 48 65 6c 6c 6f 20 57 6f  72 6c 64 21              Hello World!
```

### Übung 2: Byte-String Konverter (Mittel)

Konvertiere zwischen verschiedenen Byte-Darstellungen.

**Erwarteter Output:**
```python
converter = ByteConverter(b"ABC")
print(converter.to_hex())        # "414243"
print(converter.to_binary())     # "010000010100001001000011"
print(converter.to_decimal())    # [65, 66, 67]
```

### Übung 3: Simple Encryption (Mittel)

Implementiere XOR-Verschlüsselung.

**Erwarteter Output:**
```python
key = b"secret"
plaintext = b"Hello World"
encrypted = xor_encrypt(plaintext, key)
decrypted = xor_encrypt(encrypted, key)
assert decrypted == plaintext
```

### Übung 4: Binary File Parser (Schwer)

Parse binäre Dateiformate mit struct.

**Erwarteter Output:**
```python
# Gegeben: Binärdatei mit Header
parser = BinaryFileParser('data.bin')
header = parser.read_header()  # Struktur parsen
data = parser.read_records()    # Records lesen
```

### Übung 5: Network Protocol (Schwer)

Implementiere einfaches binäres Netzwerkprotokoll.

**Erwarteter Output:**
```python
# Message Format: [type:1byte][length:2bytes][data:n bytes]
msg = Message(type=1, data=b"Hello")
packed = msg.pack()
msg_unpacked = Message.unpack(packed)
assert msg.data == msg_unpacked.data
```

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [bytes](https://docs.python.org/3/library/stdtypes.html#bytes) - bytes Typ
- [bytearray](https://docs.python.org/3/library/stdtypes.html#bytearray) - bytearray Typ
- [struct Module](https://docs.python.org/3/library/struct.html) - Binär-Packing
- [Binary Sequence Types](https://docs.python.org/3/library/stdtypes.html#binaryseq) - Übersicht

## Zusammenfassung

### Wichtigste Punkte

1. **bytes vs. String:**
   - String = Unicode-Text
   - bytes = Binärdaten (0-255)
   - Konvertierung mit encode()/decode()

2. **bytes (immutable):**
   - Literal: `b"text"`
   - Unveränderbar wie Strings
   - Hashable (Dict-Keys möglich)
   - Effizient für Read-Only

3. **bytearray (mutable):**
   - Kein Literal, nur Konstruktor
   - Veränderbar wie Listen
   - Nicht hashable
   - Effizient für häufige Änderungen

4. **Operationen:**
   - Viele String-Methoden verfügbar
   - Index gibt int (nicht bytes!)
   - Slicing gibt bytes/bytearray

5. **Use Cases:**
   - Binärdateien I/O
   - Netzwerk-Protokolle
   - Hashing/Checksums
   - Binäre Datenstrukturen

### Entscheidungsbaum

```
Brauche ich Binärdaten?
├─ Nein → Nutze str (String)
└─ Ja
   ├─ Änderungen nötig?
   │  ├─ Ja → bytearray
   │  └─ Nein → bytes
   └─ Als Dict-Key?
      └─ Ja → bytes (hashable)
```

## Verwandte Themen

- [[02_Memoryview|Memoryview]] - Zero-Copy Buffer
- [[03_Übungen Bytes|Übungen]] - Mehr Praxis
- [[04_Encoding und Unicode|Encoding]] - String/Bytes-Konvertierung
- [[01_String Basics|Strings]] - Text-Datentyp

---

← [[06_Übungen Strings|Übungen Strings]] | [[INDEX|📑 Index]] | [[02_Memoryview|Memoryview]] →