---
tags: [python, datenstrukturen, integer, numerisch, grundlagen]
created: 2025-10-01
---

← [[03_Memory Management Basics|Memory Management Basics]] | [[INDEX|📑 Index]] | [[02_Float|Float]] →

# Integer (int)

> [!tip] Lernziel
> Du verstehst Pythons Integer-Typ, seine unbegrenzte Größe, Operationen und wann du ihn optimal einsetzt

## Was ist ein Integer?

Ein **Integer** (Ganzzahl) repräsentiert ganze Zahlen ohne Dezimalstellen. In Python haben Integers eine **unbegrenzte Größe** - sie werden nur durch den verfügbaren Arbeitsspeicher begrenzt!

```python
# Kleine Zahlen
x = 42
y = -17
z = 0

# Große Zahlen - kein Problem!
sehr_groß = 123456789012345678901234567890
noch_größer = 10 ** 100  # 1 mit 100 Nullen (Googol)

print(sehr_groß)
print(noch_größer)
```

## Syntax und Grundlagen

### Integer Literale

```python
# Dezimal (Standard)
dezimal = 42

# Binär (Prefix: 0b)
binär = 0b1010  # = 10 in Dezimal
print(binär)    # 10

# Oktal (Prefix: 0o)
oktal = 0o755   # = 493 in Dezimal
print(oktal)    # 493

# Hexadezimal (Prefix: 0x)
hex = 0xFF      # = 255 in Dezimal
print(hex)      # 255

# Lesbarkeit mit Unterstrichen (Python 3.6+)
große_zahl = 1_000_000  # = 1000000
print(große_zahl)       # 1000000

bincode = 0b_1111_0000  # Gruppierung in Binär
print(bincode)          # 240
```

### Type Checking

```python
x = 42
print(type(x))           # <class 'int'>
print(isinstance(x, int)) # True

# Boolean ist Subtyp von int!
print(isinstance(True, int))  # True
print(True + True)            # 2
print(False * 10)             # 0
```

## Grundlegende Operationen

### Arithmetische Operationen

```python
a = 10
b = 3

# Addition
print(a + b)    # 13

# Subtraktion
print(a - b)    # 7

# Multiplikation
print(a * b)    # 30

# Division (gibt immer float zurück!)
print(a / b)    # 3.3333333333333335
print(type(a / b))  # <class 'float'>

# Ganzzahl-Division (floor division)
print(a // b)   # 3
print(type(a // b))  # <class 'int'>

# Modulo (Rest)
print(a % b)    # 1

# Potenz
print(a ** b)   # 1000
print(2 ** 10)  # 1024

# Negation
print(-a)       # -10
```

### Vergleichsoperationen

```python
x = 10
y = 20

print(x == y)   # False (gleich)
print(x != y)   # True (ungleich)
print(x < y)    # True (kleiner)
print(x <= y)   # True (kleiner oder gleich)
print(x > y)    # False (größer)
print(x >= y)   # False (größer oder gleich)

# Verkettete Vergleiche
z = 15
print(x < z < y)  # True (10 < 15 < 20)
print(10 < z <= 15)  # True
```

### Bitweise Operationen

```python
a = 60  # 0b111100
b = 13  # 0b001101

# AND
print(a & b)    # 12 (0b001100)

# OR
print(a | b)    # 61 (0b111101)

# XOR
print(a ^ b)    # 49 (0b110001)

# NOT (invertiert alle Bits)
print(~a)       # -61

# Left Shift (multipliziert mit 2^n)
print(a << 2)   # 240 (60 * 4)

# Right Shift (dividiert durch 2^n)
print(a >> 2)   # 15 (60 // 4)
```

## Integer-Methoden und Funktionen

### Built-in Funktionen

```python
# abs() - Absolutwert
print(abs(-42))      # 42
print(abs(42))       # 42

# pow() - Potenz mit optionalem Modulo
print(pow(2, 10))           # 1024
print(pow(2, 10, 100))      # 24 (2^10 % 100)

# divmod() - Division und Modulo gleichzeitig
quotient, rest = divmod(17, 5)
print(quotient, rest)  # 3 1

# max() / min()
print(max(1, 5, 3, 9, 2))   # 9
print(min(1, 5, 3, 9, 2))   # 1

# sum()
zahlen = [1, 2, 3, 4, 5]
print(sum(zahlen))          # 15

# round() (bei Integers ändert sich nichts)
print(round(42))            # 42
```

### Integer-spezifische Methoden

```python
x = 42

# bit_length() - Anzahl Bits zur Repräsentation
print(x.bit_length())           # 6 (42 = 0b101010)
print((255).bit_length())       # 8
print((256).bit_length())       # 9

# bit_count() - Anzahl gesetzter Bits (Python 3.10+)
print((15).bit_count())         # 4 (0b1111)
print((255).bit_count())        # 8 (0b11111111)

# to_bytes() - Integer zu Bytes
zahl = 1024
bytes_data = zahl.to_bytes(2, byteorder='big')
print(bytes_data)               # b'\x04\x00'

# from_bytes() - Bytes zu Integer
zurück = int.from_bytes(bytes_data, byteorder='big')
print(zurück)                   # 1024

# as_integer_ratio() - Gibt (numerator, denominator) zurück
print((42).as_integer_ratio())  # (42, 1)
```

## Details und Interna

### Unbegrenzte Größe

Python's Integer hat **keine feste Größe**. Intern nutzt Python ein Array von "Digits":

```python
# Kleine Integers (-5 bis 256) werden gecacht
a = 42
b = 42
print(a is b)  # True (gleiches Objekt!)

# Große Integers werden neu erstellt
x = 1000
y = 1000
print(x is y)  # False (verschiedene Objekte)

# Aber Wert ist gleich
print(x == y)  # True

# Beliebig große Zahlen möglich
googol = 10 ** 100
googolplex = 10 ** googol  # Würde SEHR lange dauern!
```

### Integer Caching

Python cached kleine Integers für Performance:

```python
import sys

# Kleine Integers sind Singletons
a = 5
b = 5
c = 2 + 3
print(a is b is c)  # True - alle zeigen auf gleiches Objekt!

# Check welche Integers gecacht sind
print(sys.intern(5) is 5)  # Funktioniert nicht, nur für Strings
```

### Memory-Layout

```python
import sys

# Kleine Integer
print(sys.getsizeof(0))      # 24 Bytes
print(sys.getsizeof(1))      # 28 Bytes
print(sys.getsizeof(255))    # 28 Bytes

# Größere Integers brauchen mehr Speicher
print(sys.getsizeof(256))    # 28 Bytes
print(sys.getsizeof(65536))  # 32 Bytes
print(sys.getsizeof(2**64))  # 36 Bytes
print(sys.getsizeof(10**100)) # 60 Bytes

# Speicher wächst mit Größe des Werts
```

## Typische Anwendungsfälle

### Anwendung 1: Zähler und Indizes

```python
# Klassischer Zähler
zähler = 0
for item in ["a", "b", "c", "d"]:
    print(f"Item {zähler}: {item}")
    zähler += 1

# Besser mit enumerate()
for index, item in enumerate(["a", "b", "c", "d"]):
    print(f"Item {index}: {item}")

# Array-Indizes
liste = [10, 20, 30, 40, 50]
print(liste[0])   # 10 (erster)
print(liste[-1])  # 50 (letzter)
print(liste[2])   # 30
```

### Anwendung 2: Mathematische Berechnungen

```python
# Fakultät berechnen
def fakultät(n):
    if n <= 1:
        return 1
    return n * fakultät(n - 1)

print(fakultät(5))    # 120
print(fakultät(20))   # 2432902008176640000

# Fibonacci
def fibonacci(n):
    if n <= 1:
        return n
    a, b = 0, 1
    for _ in range(n - 1):
        a, b = b, a + b
    return b

print(fibonacci(10))  # 55
print(fibonacci(100)) # 354224848179261915075 (riesig!)

# Primzahl-Check
def ist_primzahl(n):
    if n < 2:
        return False
    for i in range(2, int(n ** 0.5) + 1):
        if n % i == 0:
            return False
    return True

print(ist_primzahl(17))   # True
print(ist_primzahl(18))   # False
```

### Anwendung 3: Bitmasken und Flags

```python
# Permissions mit Bitmasken
READ = 0b100    # 4
WRITE = 0b010   # 2
EXECUTE = 0b001 # 1

# Kombiniere Permissions
permissions = READ | WRITE  # 0b110 = 6

# Check Permission
def hat_permission(perms, flag):
    return (perms & flag) != 0

print(hat_permission(permissions, READ))    # True
print(hat_permission(permissions, WRITE))   # True
print(hat_permission(permissions, EXECUTE)) # False

# Setze Permission
permissions = permissions | EXECUTE
print(hat_permission(permissions, EXECUTE)) # True

# Entferne Permission
permissions = permissions & ~WRITE
print(hat_permission(permissions, WRITE))   # False
```

### Anwendung 4: Zahlensysteme konvertieren

```python
def konvertiere_zahlensystem(zahl, basis=10):
    """
    Zeigt Zahl in verschiedenen Zahlensystemen
    """
    return {
        "dezimal": str(zahl),
        "binär": bin(zahl),
        "oktal": oct(zahl),
        "hexadezimal": hex(zahl)
    }

ergebnis = konvertiere_zahlensystem(255)
print(ergebnis)
# {
#     'dezimal': '255',
#     'binär': '0b11111111',
#     'oktal': '0o377',
#     'hexadezimal': '0xff'
# }

# Zurück zu Integer
print(int('0b11111111', 2))   # 255
print(int('0o377', 8))        # 255
print(int('0xff', 16))        # 255
print(int('FF', 16))          # 255 (ohne Prefix)
```

### Anwendung 5: Range und Sequenzen

```python
# Einfache Range
for i in range(5):
    print(i)  # 0, 1, 2, 3, 4

# Range mit Start und Ende
for i in range(2, 7):
    print(i)  # 2, 3, 4, 5, 6

# Range mit Schrittweite
for i in range(0, 10, 2):
    print(i)  # 0, 2, 4, 6, 8

# Rückwärts zählen
for i in range(10, 0, -1):
    print(i)  # 10, 9, 8, ..., 1

# Range zu Liste
zahlen = list(range(1, 11))
print(zahlen)  # [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

## Häufige Fallstricke

### Fallstrick 1: Division gibt Float zurück

```python
# ❌ Überraschung: Division gibt immer float!
x = 10 / 2
print(type(x))  # <class 'float'>
print(x)        # 5.0 (nicht 5!)

# ✅ Nutze // für Integer-Division
x = 10 // 2
print(type(x))  # <class 'int'>
print(x)        # 5

# Oder explizit zu int casten
x = int(10 / 2)
print(type(x))  # <class 'int'>
```

### Fallstrick 2: Modulo mit negativen Zahlen

```python
# Modulo mit negativen Zahlen kann überraschend sein
print(10 % 3)    # 1
print(-10 % 3)   # 2 (nicht -1!)
print(10 % -3)   # -2 (nicht 1!)
print(-10 % -3)  # -1

# Python garantiert: a == (a // b) * b + (a % b)
# Deshalb folgt Modulo dem Vorzeichen des Divisors
```

### Fallstrick 3: Integer Overflow gibt es nicht

```python
# In anderen Sprachen (C, Java):
# int hat maximale Größe (z.B. 2^31 - 1)
# Overflow führt zu falschen Ergebnissen

# Python: Kein Overflow!
x = 2 ** 1000
print(x)  # Gigantische Zahl, kein Problem!

# Aber: Performance leidet bei sehr großen Zahlen
import time
start = time.perf_counter()
x = 10 ** 100000  # Sehr groß!
print(f"Zeit: {time.perf_counter() - start:.4f}s")
```

### Fallstrick 4: Vergleich mit Float

```python
# ❌ Float-Genauigkeit kann zu Problemen führen
x = 0.1 + 0.1 + 0.1
print(x == 0.3)  # False! (Rundungsfehler)

# ✅ Vergleiche mit Toleranz
def fast_gleich(a, b, toleranz=1e-9):
    return abs(a - b) < toleranz

print(fast_gleich(x, 0.3))  # True

# Oder nutze Decimal für exakte Berechnungen
from decimal import Decimal
x = Decimal('0.1') + Decimal('0.1') + Decimal('0.1')
print(x == Decimal('0.3'))  # True
```

### Fallstrick 5: Veränderbare vs. Unveränderbare

```python
# Integer sind IMMUTABLE
x = 42
alte_id = id(x)
x += 1
neue_id = id(x)
print(alte_id != neue_id)  # True - neues Objekt!

# Das betrifft auch Funktionsargumente
def erhöhe(zahl):
    zahl += 1  # Erstellt neues Objekt lokal
    return zahl

x = 42
y = erhöhe(x)
print(x)  # 42 (unverändert!)
print(y)  # 43
```

## Performance-Hinweise

### Zeitkomplexität

| Operation | Zeitkomplexität | Beschreibung |
|-----------|----------------|--------------|
| `+`, `-`, `*` | O(n·m) | n, m = Anzahl Digits |
| `/`, `//`, `%` | O(n·m) | Division ist teuer |
| `**` | O(n²·log(m)) | Potenz sehr teuer bei großen Exponenten |
| `<`, `>`, `==` | O(n) | n = Anzahl Digits |
| `&`, `|`, `^` | O(n) | Bitwise Operationen |
| `<<`, `>>` | O(n) | Shift Operationen |

### Performance-Tipps

```python
# ✅ Nutze pow() mit Modulo für große Potenzen
# Viel schneller als (a ** b) % c
print(pow(2, 1000, 100))  # Schnell
# print((2 ** 1000) % 100)  # Langsam (berechnet riesige Zahl erst)

# ✅ Nutze bit_length() statt len(bin())
x = 1024
print(x.bit_length())     # Schnell: O(1)
# print(len(bin(x)) - 2)    # Langsam: O(n)

# ✅ Nutze // statt int(/)
print(10 // 3)  # Schneller
# print(int(10 / 3))  # Langsamer (float-Umwandlung)

# ✅ Caching für wiederholte Berechnungen
from functools import lru_cache

@lru_cache(maxsize=None)
def fibonacci(n):
    if n <= 1:
        return n
    return fibonacci(n-1) + fibonacci(n-2)

print(fibonacci(100))  # Sehr schnell dank Caching
```

### Speicher-Effizienz

```python
import sys

# Kleine Integers sind gespeichert (gecacht)
print(sys.getsizeof(1))      # 28 Bytes
print(sys.getsizeof(10))     # 28 Bytes
print(sys.getsizeof(100))    # 28 Bytes

# Größere brauchen mehr Speicher
print(sys.getsizeof(10**6))  # 32 Bytes
print(sys.getsizeof(10**12)) # 36 Bytes
print(sys.getsizeof(10**18)) # 40 Bytes

# Array von Integers
zahlen = list(range(1000000))
print(f"Speicher: {sys.getsizeof(zahlen) / 1024 / 1024:.2f} MB")

# Besser: array für homogene numerische Daten
import array
arr = array.array('i', range(1000000))  # 'i' = signed int
print(f"Speicher: {sys.getsizeof(arr) / 1024 / 1024:.2f} MB")
# Array ist ~3x speichereffizienter!
```

## Übungsaufgaben

### Übung 1: Zahlensystem-Konverter (Leicht)

Schreibe eine Funktion `zeige_alle_basen(zahl)`, die eine Dezimalzahl in binär, oktal und hexadezimal darstellt.

**Erwarteter Output:**
```python
zeige_alle_basen(42)
# Dezimal:      42
# Binär:        0b101010
# Oktal:        0o52
# Hexadezimal:  0x2a
```

### Übung 2: Bit-Manipulation (Mittel)

Schreibe Funktionen für:
- `setze_bit(zahl, position)` - Setzt Bit an Position
- `lösche_bit(zahl, position)` - Löscht Bit an Position  
- `toggle_bit(zahl, position)` - Invertiert Bit an Position
- `prüfe_bit(zahl, position)` - Prüft ob Bit gesetzt

**Erwarteter Output:**
```python
x = 0b1010  # 10
x = setze_bit(x, 0)    # 0b1011 = 11
x = lösche_bit(x, 3)   # 0b0011 = 3
x = toggle_bit(x, 1)   # 0b0001 = 1
print(prüfe_bit(x, 0)) # True
```

### Übung 3: Große Zahlen Rechner (Mittel)

Implementiere einen Taschenrechner, der mit sehr großen Zahlen (10^100+) rechnen kann und die Ergebnisse formatiert ausgibt.

**Erwarteter Output:**
```python
rechne("10^100 + 5^200")
# 1.000...000 (100 Nullen) + 6.223...
# = 6.223...
```

### Übung 4: Optimierte Potenz (Schwer)

Implementiere `schnelle_potenz(basis, exponent)` mit dem "Exponentiation by Squaring" Algorithmus.

**Hinweis:** a^n = (a^2)^(n/2) wenn n gerade, sonst a * a^(n-1)

**Erwarteter Output:**
```python
print(schnelle_potenz(2, 1000))  # Sehr schnell berechnet
# Sollte schneller sein als 2 ** 1000 für sehr große Exponenten
```

### Übung 5: Primfaktorzerlegung (Schwer)

Schreibe eine Funktion `primfaktoren(n)`, die alle Primfaktoren einer Zahl findet.

**Erwarteter Output:**
```python
print(primfaktoren(60))
# [2, 2, 3, 5]  (60 = 2 * 2 * 3 * 5)

print(primfaktoren(1024))
# [2, 2, 2, 2, 2, 2, 2, 2, 2, 2]  (2^10)

print(primfaktoren(97))
# [97]  (Primzahl)
```

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [Numeric Types - int](https://docs.python.org/3/library/stdtypes.html#numeric-types-int-float-complex) - Integer Typ im Detail
- [Built-in Functions](https://docs.python.org/3/library/functions.html) - abs(), pow(), divmod() etc.
- [Binary Operations](https://docs.python.org/3/reference/expressions.html#binary-bitwise-operations) - Bitwise Operationen
- [Integer String Conversion](https://docs.python.org/3/library/stdtypes.html#int) - Zahlensystem-Konvertierung
- [Arbitrary Precision Integers](https://docs.python.org/3/library/sys.html#sys.int_info) - Informationen zu Pythons Integer-Implementierung

## Verwandte Themen

- [[02_Float|Float]] - Gleitkommazahlen
- [[03_Complex|Complex]] - Komplexe Zahlen
- [[04_Decimal und Fraction|Decimal und Fraction]] - Exakte Dezimalzahlen
- [[05_Übungen Numerische Typen|Übungen]] - Mehr Praxis mit Zahlen
- [[01_Grundlagen/03_Boolean_und_None/01_Boolean (bool)|Boolean]] - Bool erbt von int!

---

← [[03_Memory Management Basics|Memory Management Basics]] | [[INDEX|📑 Index]] | [[02_Float|Float]] →