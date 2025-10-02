---
tags: [python, datenstrukturen, float, numerisch, gleitkomma]
created: 2025-10-01
---

← [[01_Integer (int)|Integer (int)]] | [[INDEX|📑 Index]] | [[03_Complex|Complex]] →

# Float

> [!tip] Lernziel
> Du verstehst Gleitkommazahlen, ihre Präzisionsgrenzen, Rundungsprobleme und wie du damit sicher arbeitest

## Was ist ein Float?

Ein **Float** (Gleitkommazahl) repräsentiert reelle Zahlen mit Dezimalstellen. Python nutzt **64-Bit Double Precision** (IEEE 754 Standard).

```python
# Float Literale
x = 3.14
y = -0.001
z = 2.0

# Wissenschaftliche Notation
große_zahl = 1.5e10    # 1.5 * 10^10 = 15000000000
kleine_zahl = 3.2e-5   # 3.2 * 10^-5 = 0.000032

print(große_zahl)      # 15000000000.0
print(kleine_zahl)     # 3.2e-05

# Integer zu Float
ganzzahl = 42
gleitkomma = float(ganzzahl)
print(gleitkomma)      # 42.0
print(type(gleitkomma)) # <class 'float'>
```

## Syntax und Grundlagen

### Float Literale

```python
# Verschiedene Schreibweisen
a = 3.14159
b = 3.
c = .5
d = 0.0

# Wissenschaftliche Notation (E-Notation)
million = 1e6          # 1000000.0
mikro = 1e-6           # 0.000001
avogadro = 6.022e23    # 602200000000000000000000.0

# Hexadezimale Float-Literale (selten genutzt)
hex_float = 0x1.8p3    # 1.5 * 2^3 = 12.0
print(hex_float)       # 12.0

# Type Checking
print(type(3.14))      # <class 'float'>
print(isinstance(3.14, float))  # True
```

### Konvertierung

```python
# String zu Float
x = float("3.14")
y = float("1.5e10")
z = float("inf")       # Unendlich
w = float("-inf")      # Minus Unendlich
n = float("nan")       # Not a Number

print(x, y, z, w, n)   # 3.14 15000000000.0 inf -inf nan

# Integer zu Float
i = 42
f = float(i)
print(f)               # 42.0

# Float zu Integer (schneidet Nachkommastellen ab!)
f = 3.99
i = int(f)
print(i)               # 3 (nicht 4!)

# Runden beim Konvertieren
print(round(3.99))     # 4
print(round(3.14))     # 3
```

## Grundlegende Operationen

### Arithmetische Operationen

```python
a = 10.5
b = 3.2

# Grundrechenarten
print(a + b)    # 13.7
print(a - b)    # 7.3
print(a * b)    # 33.6
print(a / b)    # 3.28125
print(a // b)   # 3.0 (Ganzzahl-Division, aber float!)
print(a % b)    # 0.8999999999999995 (Modulo)
print(a ** b)   # 1452.9002606985523 (Potenz)

# Negation
print(-a)       # -10.5

# Absolutwert
print(abs(-3.14))  # 3.14
```

### Vergleichsoperationen

```python
x = 3.14
y = 3.14
z = 3.15

print(x == y)   # True
print(x != z)   # True
print(x < z)    # True
print(x <= y)   # True
print(x > z)    # False
print(x >= y)   # True

# ⚠️ Vorsicht bei Float-Vergleichen wegen Rundungsfehlern!
a = 0.1 + 0.1 + 0.1
b = 0.3
print(a == b)   # False! (Rundungsfehler)
print(a)        # 0.30000000000000004
```

## Das Rundungsproblem

### Warum Floats ungenau sind

Floats nutzen **binäre Darstellung**. Viele Dezimalzahlen können nicht exakt dargestellt werden:

```python
# 0.1 kann nicht exakt in binär dargestellt werden
x = 0.1
print(f"{x:.50f}")  # 0.10000000000000000555111512312578270211815834045410

# Das führt zu Überraschungen
print(0.1 + 0.1 + 0.1)  # 0.30000000000000004
print(0.1 + 0.2)        # 0.30000000000000004
print(0.1 * 3)          # 0.30000000000000004

# Vergleiche schlagen fehl
print((0.1 + 0.1 + 0.1) == 0.3)  # False!

# Aber manchmal klappt es
print(0.5 + 0.5)  # 1.0 (exakt!)
print((0.5 + 0.5) == 1.0)  # True
# Grund: 0.5 = 1/2 ist exakt in binär darstellbar
```

### Sichere Float-Vergleiche

```python
import math

# ❌ Direkte Gleichheit ist gefährlich
a = 0.1 + 0.1 + 0.1
b = 0.3
print(a == b)  # False

# ✅ Vergleich mit Toleranz (Epsilon)
def fast_gleich(a, b, toleranz=1e-9):
    return abs(a - b) < toleranz

print(fast_gleich(a, b))  # True

# ✅ Nutze math.isclose() (Python 3.5+)
print(math.isclose(a, b))  # True
print(math.isclose(a, b, rel_tol=1e-9))  # True

# Verschiedene Toleranzen
print(math.isclose(1.0, 1.0001, rel_tol=0.001))   # True
print(math.isclose(1.0, 1.0001, rel_tol=0.00001)) # False
```

### Rundungsfunktionen

```python
import math

x = 3.7
y = 3.2
z = -2.7

# round() - Kaufmännisches Runden
print(round(x))      # 4
print(round(y))      # 3
print(round(z))      # -3

# Mit Nachkommastellen
print(round(3.14159, 2))   # 3.14
print(round(3.14159, 4))   # 3.1416

# math.floor() - Abrunden
print(math.floor(x))   # 3
print(math.floor(z))   # -3

# math.ceil() - Aufrunden  
print(math.ceil(x))    # 4
print(math.ceil(y))    # 4
print(math.ceil(z))    # -2

# math.trunc() - Richtung Null
print(math.trunc(x))   # 3
print(math.trunc(z))   # -2

# Unterschied bei negativen Zahlen:
print(f"floor(-2.7) = {math.floor(-2.7)}")  # -3
print(f"trunc(-2.7) = {math.trunc(-2.7)}")  # -2
```

## Details und Interna

### IEEE 754 Darstellung

Python Floats nutzen 64 Bit (Double Precision):
- **1 Bit** Vorzeichen
- **11 Bits** Exponent
- **52 Bits** Mantisse

```python
import sys

# Float Info
print(sys.float_info)
# sys.float_info(
#     max=1.7976931348623157e+308,    # Größter Float
#     max_exp=1024,                    # Max Exponent
#     max_10_exp=308,                  # Max Dezimal-Exponent
#     min=2.2250738585072014e-308,    # Kleinster positiver Float
#     min_exp=-1021,                   # Min Exponent
#     min_10_exp=-307,                 # Min Dezimal-Exponent
#     dig=15,                          # Signifikante Stellen
#     mant_dig=53,                     # Mantissen-Bits
#     epsilon=2.220446049250313e-16,  # Kleinster Unterschied zu 1.0
#     radix=2,                         # Basis (binär)
#     rounds=1                         # Rundungs-Modus
# )

# Größter darstellbarer Float
print(sys.float_info.max)  # ~1.8e308

# Kleinster positiver Float
print(sys.float_info.min)  # ~2.2e-308

# Precision (signifikante Dezimalstellen)
print(sys.float_info.dig)  # 15
```

### Spezielle Float-Werte

```python
import math

# Unendlich
inf = float('inf')
neg_inf = float('-inf')

print(inf)           # inf
print(neg_inf)       # -inf
print(inf > 1e308)   # True
print(-inf < -1e308) # True

# Operationen mit Unendlich
print(inf + 1)       # inf
print(inf * 2)       # inf
print(1 / inf)       # 0.0
print(inf / inf)     # nan

# NaN (Not a Number)
nan = float('nan')
print(nan)           # nan

# NaN-Eigenschaften
print(nan == nan)    # False! NaN ist nie gleich NaN
print(nan != nan)    # True
print(nan < 5)       # False
print(nan > 5)       # False

# Prüfen auf spezielle Werte
print(math.isnan(nan))      # True
print(math.isinf(inf))      # True
print(math.isfinite(3.14))  # True
print(math.isfinite(inf))   # False
```

### Memory Layout

```python
import sys

# Floats sind fix 24 Bytes groß (auf 64-bit System)
print(sys.getsizeof(0.0))        # 24 Bytes
print(sys.getsizeof(3.14))       # 24 Bytes
print(sys.getsizeof(1e308))      # 24 Bytes
print(sys.getsizeof(float('inf'))) # 24 Bytes

# Im Vergleich zu Integers (variable Größe)
print(sys.getsizeof(0))          # 24 Bytes
print(sys.getsizeof(10**100))    # 60 Bytes
```

## Math Module

```python
import math

# Trigonometrische Funktionen
print(math.sin(math.pi / 2))   # 1.0
print(math.cos(0))             # 1.0
print(math.tan(math.pi / 4))   # 0.9999999999999999 (≈1)

# Umkehrfunktionen
print(math.asin(1))            # π/2
print(math.acos(0))            # π/2
print(math.atan(1))            # π/4

# Hyperbolische Funktionen
print(math.sinh(1))            # 1.1752011936438014
print(math.cosh(0))            # 1.0
print(math.tanh(0))            # 0.0

# Logarithmen
print(math.log(math.e))        # 1.0 (natürlicher Logarithmus)
print(math.log10(100))         # 2.0 (Logarithmus zur Basis 10)
print(math.log2(8))            # 3.0 (Logarithmus zur Basis 2)
print(math.log(8, 2))          # 3.0 (Logarithmus zu beliebiger Basis)

# Exponentialfunktion
print(math.exp(1))             # 2.718281828459045 (e^1)
print(math.exp(0))             # 1.0

# Wurzeln
print(math.sqrt(16))           # 4.0
print(math.pow(8, 1/3))        # 2.0 (Kubikwurzel)

# Konstanten
print(math.pi)                 # 3.141592653589793
print(math.e)                  # 2.718281828459045
print(math.tau)                # 6.283185307179586 (2π)
print(math.inf)                # inf
print(math.nan)                # nan

# Weitere nützliche Funktionen
print(math.factorial(5))       # 120
print(math.gcd(48, 18))        # 6 (größter gemeinsamer Teiler)
print(math.lcm(12, 18))        # 36 (kleinstes gemeinsames Vielfaches)
print(math.degrees(math.pi))   # 180.0 (Bogenmaß zu Grad)
print(math.radians(180))       # 3.141592653589793 (Grad zu Bogenmaß)
```

## Typische Anwendungsfälle

### Anwendung 1: Wissenschaftliche Berechnungen

```python
import math

def kreis_berechnen(radius):
    """Berechnet Umfang und Fläche eines Kreises"""
    umfang = 2 * math.pi * radius
    fläche = math.pi * radius ** 2
    return {
        "radius": radius,
        "umfang": round(umfang, 2),
        "fläche": round(fläche, 2)
    }

print(kreis_berechnen(5))
# {'radius': 5, 'umfang': 31.42, 'fläche': 78.54}

def quadratische_gleichung(a, b, c):
    """Löst ax² + bx + c = 0"""
    diskriminante = b**2 - 4*a*c
    
    if diskriminante < 0:
        return None  # Keine reellen Lösungen
    elif diskriminante == 0:
        return [-b / (2*a)]  # Eine Lösung
    else:
        x1 = (-b + math.sqrt(diskriminante)) / (2*a)
        x2 = (-b - math.sqrt(diskriminante)) / (2*a)
        return [x1, x2]

print(quadratische_gleichung(1, -3, 2))  # [2.0, 1.0]
```

### Anwendung 2: Finanzberechnungen

```python
def zinseszins(kapital, zinssatz, jahre):
    """
    Berechnet Endkapital mit Zinseszins
    K_n = K_0 * (1 + p/100)^n
    """
    return kapital * (1 + zinssatz/100) ** jahre

# 10000€ bei 5% für 10 Jahre
endkapital = zinseszins(10000, 5, 10)
print(f"Endkapital: {endkapital:.2f}€")  # 16288.95€

def monatliche_rate(kredit, zinssatz_jahr, laufzeit_monate):
    """Berechnet monatliche Rate für Annuitätendarlehen"""
    zinssatz_monat = zinssatz_jahr / 12 / 100
    
    if zinssatz_monat == 0:
        return kredit / laufzeit_monate
    
    rate = kredit * (zinssatz_monat * (1 + zinssatz_monat) ** laufzeit_monate) / \
           ((1 + zinssatz_monat) ** laufzeit_monate - 1)
    
    return rate

# 200.000€ Kredit, 3% p.a., 20 Jahre
rate = monatliche_rate(200000, 3, 240)
print(f"Monatliche Rate: {rate:.2f}€")  # 1109.19€
```

### Anwendung 3: Physikalische Berechnungen

```python
import math

def freier_fall(höhe, g=9.81):
    """
    Berechnet Fallzeit und Endgeschwindigkeit
    h = 1/2 * g * t²
    v = g * t
    """
    zeit = math.sqrt(2 * höhe / g)
    geschwindigkeit = g * zeit
    
    return {
        "fallzeit": round(zeit, 2),
        "geschwindigkeit": round(geschwindigkeit, 2),
        "geschwindigkeit_kmh": round(geschwindigkeit * 3.6, 2)
    }

# Fall aus 100m Höhe
print(freier_fall(100))
# {'fallzeit': 4.52, 'geschwindigkeit': 44.29, 'geschwindigkeit_kmh': 159.43}

def wellengeschwindigkeit(frequenz, wellenlänge):
    """v = f * λ"""
    return frequenz * wellenlänge

# Lichtgeschwindigkeit (Beispiel)
c = wellengeschwindigkeit(5e14, 600e-9)  # Sichtbares Licht
print(f"Lichtgeschwindigkeit: {c:.2e} m/s")  # 3.00e+08 m/s
```

### Anwendung 4: Statistik

```python
import math

def mittelwert(zahlen):
    """Arithmetisches Mittel"""
    return sum(zahlen) / len(zahlen)

def standardabweichung(zahlen):
    """Stichproben-Standardabweichung"""
    n = len(zahlen)
    if n < 2:
        return 0
    
    mittel = mittelwert(zahlen)
    varianz = sum((x - mittel) ** 2 for x in zahlen) / (n - 1)
    return math.sqrt(varianz)

def statistik(zahlen):
    """Umfassende Statistik"""
    sortiert = sorted(zahlen)
    n = len(sortiert)
    
    # Median
    if n % 2 == 0:
        median = (sortiert[n//2 - 1] + sortiert[n//2]) / 2
    else:
        median = sortiert[n//2]
    
    return {
        "anzahl": n,
        "mittelwert": round(mittelwert(zahlen), 2),
        "median": round(median, 2),
        "std_abweichung": round(standardabweichung(zahlen), 2),
        "minimum": min(zahlen),
        "maximum": max(zahlen),
        "spannweite": max(zahlen) - min(zahlen)
    }

daten = [12.5, 15.3, 18.7, 14.2, 16.8, 13.9, 17.1]
print(statistik(daten))
# {
#     'anzahl': 7,
#     'mittelwert': 15.5,
#     'median': 15.3,
#     'std_abweichung': 2.19,
#     'minimum': 12.5,
#     'maximum': 18.7,
#     'spannweite': 6.2
# }
```

### Anwendung 5: Distanzberechnungen

```python
import math

def distanz_2d(x1, y1, x2, y2):
    """Euklidische Distanz in 2D"""
    return math.sqrt((x2 - x1)**2 + (y2 - y1)**2)

print(distanz_2d(0, 0, 3, 4))  # 5.0 (Pythagoras)

def haversine(lat1, lon1, lat2, lon2):
    """
    Berechnet Luftlinie zwischen zwei GPS-Koordinaten
    Haversine-Formel für Großkreis-Distanz
    """
    R = 6371  # Erdradius in km
    
    # Grad zu Bogenmaß
    lat1, lon1, lat2, lon2 = map(math.radians, [lat1, lon1, lat2, lon2])
    
    # Haversine-Formel
    dlat = lat2 - lat1
    dlon = lon2 - lon1
    a = math.sin(dlat/2)**2 + math.cos(lat1) * math.cos(lat2) * math.sin(dlon/2)**2
    c = 2 * math.asin(math.sqrt(a))
    
    return R * c

# Berlin nach München
distanz = haversine(52.5200, 13.4050, 48.1351, 11.5820)
print(f"Berlin - München: {distanz:.2f} km")  # ~504 km
```

## Häufige Fallstricke

### Fallstrick 1: Float-Gleichheit

```python
# ❌ FEHLER: Direkte Gleichheit
a = 0.1 + 0.2
b = 0.3
if a == b:
    print("Gleich")
else:
    print("Nicht gleich!")  # Das wird ausgegeben!

# ✅ RICHTIG: Mit Toleranz vergleichen
import math
if math.isclose(a, b):
    print("Fast gleich")  # Das ist richtig
```

### Fallstrick 2: Akkumulation von Rundungsfehlern

```python
# ❌ Rundungsfehler akkumulieren
summe = 0.0
for i in range(1000):
    summe += 0.1
print(summe)  # 100.00000000000007 (nicht 100.0!)

# ✅ BESSER: Nutze Decimal für exakte Berechnungen
from decimal import Decimal
summe = Decimal('0')
for i in range(1000):
    summe += Decimal('0.1')
print(summe)  # 100.0 (exakt!)

# ✅ ODER: Arbeite mit Integers und teile am Ende
summe = 0
for i in range(1000):
    summe += 1  # Integer-Addition
print(summe / 10)  # 100.0
```

### Fallstrick 3: Division durch Null

```python
# ❌ Division durch Null
try:
    x = 5 / 0
except ZeroDivisionError as e:
    print(f"Fehler: {e}")  # division by zero

# ✅ Prüfe vorher
divisor = 0
if divisor != 0:
    result = 5 / divisor
else:
    result = float('inf')  # Oder handle es anders
print(result)  # inf
```

### Fallstrick 4: Modulo mit Floats

```python
# Modulo mit Floats kann überraschend sein
print(10 % 3)      # 1 (Integer)
print(10.0 % 3.0)  # 1.0 (Float)
print(10.5 % 3.0)  # 1.5

# ⚠️ Rundungsfehler möglich
print(0.3 % 0.1)   # 0.09999999999999998 (nicht 0.0!)

# ✅ Für exakte Modulo-Berechnungen: Decimal
from decimal import Decimal
print(Decimal('0.3') % Decimal('0.1'))  # 0.0
```

### Fallstrick 5: Sehr große und sehr kleine Zahlen

```python
# Überlauf zu inf
x = 1e308
y = x * 10
print(y)  # inf (zu groß!)

# Unterlauf zu 0.0
x = 1e-308
y = x / 1e10
print(y)  # 0.0 (zu klein!)

# Präzisionsverlust bei großen Unterschieden
groß = 1e16
klein = 1.0
print(groß + klein == groß)  # True! (klein wird "verschluckt")

# ✅ Nutze math.fsum() für akkurate Summen
import math
zahlen = [1e16, 1.0, -1e16]
print(sum(zahlen))       # 0.0 (falsch!)
print(math.fsum(zahlen)) # 1.0 (korrekt!)
```

## Performance-Hinweise

### Zeitkomplexität

| Operation | Zeitkomplexität |
|-----------|----------------|
| `+`, `-`, `*`, `/` | O(1) |
| `**` | O(1) |
| `math` Funktionen | O(1) |
| Vergleiche | O(1) |

Alle Float-Operationen sind O(1) weil Floats feste Größe haben (64 Bit).

### Performance-Tipps

```python
import time
import math

# ✅ math.sqrt() ist schneller als ** 0.5
start = time.perf_counter()
for _ in range(1000000):
    x = 16 ** 0.5
print(f"** 0.5: {time.perf_counter() - start:.4f}s")

start = time.perf_counter()
for _ in range(1000000):
    x = math.sqrt(16)
print(f"math.sqrt(): {time.perf_counter() - start:.4f}s")
# math.sqrt() ist ~2x schneller!

# ✅ Vermeide unnötige Float-Konvertierungen
# Langsam
result = float(int(3.7) + int(2.3))

# Schneller
result = 3.7 + 2.3

# ✅ Nutze math.fsum() für genaue Summen (aber langsamer)
zahlen = [0.1] * 10
print(sum(zahlen))        # Schnell aber ungenau
print(math.fsum(zahlen))  # Langsamer aber genau
```

## Übungsaufgaben

### Übung 1: Sichere Vergleichsfunktion (Leicht)

Schreibe eine Funktion `vergleiche_floats(a, b, toleranz=1e-9)`, die zwei Floats vergleicht und "gleich", "größer" oder "kleiner" zurückgibt.

**Erwarteter Output:**
```python
print(vergleiche_floats(0.1 + 0.2, 0.3))  # "gleich"
print(vergleiche_floats(3.14, 3.13))      # "größer"
print(vergleiche_floats(2.5, 2.6))        # "kleiner"
```

### Übung 2: Rundungsfehler demonstrieren (Mittel)

Schreibe eine Funktion `zeige_rundungsfehler(operationen)`, die aufzeigt wie sich Rundungsfehler akkumulieren.

**Erwarteter Output:**
```python
# Addiere 0.1 tausendmal
zeige_rundungsfehler(1000)
# Erwartet: 100.0
# Tatsächlich: 100.00000000000007
# Fehler: 7.105427357601002e-14
```

### Übung 3: Temperatur-Konverter (Mittel)

Implementiere Funktionen für Temperaturumrechnung zwischen Celsius, Fahrenheit und Kelvin mit korrekter Rundung.

**Erwarteter Output:**
```python
print(celsius_zu_fahrenheit(0))    # 32.0
print(fahrenheit_zu_celsius(32))   # 0.0
print(celsius_zu_kelvin(0))        # 273.15
print(kelvin_zu_celsius(273.15))   # 0.0
```

### Übung 4: Numerische Integration (Schwer)

Implementiere die Trapezregel zur numerischen Integration einer Funktion.

**Erwarteter Output:**
```python
def f(x):
    return x**2

# Integral von x² von 0 bis 1 sollte 1/3 ≈ 0.333 sein
integral = trapez_integration(f, 0, 1, schritte=1000)
print(f"Integral: {integral:.6f}")  # ~0.333333
```

### Übung 5: Float-Arithmetik Analyzer (Schwer)

Schreibe eine Klasse `FloatAnalyzer`, die Float-Berechnungen durchführt und dabei Rundungsfehler tracked.

**Erwarteter Output:**
```python
analyzer = FloatAnalyzer()
result = analyzer.add(0.1, 0.2)
result = analyzer.subtract(result, 0.3)

print(analyzer.fehler())        # Akkumulierter Rundungsfehler
print(analyzer.operationen())   # Liste aller Operationen
print(analyzer.ist_genau())     # False (wegen Rundungsfehlern)
```

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [Floating Point Arithmetic](https://docs.python.org/3/tutorial/floatingpoint.html) - Erklärung von Float-Rundung
- [math Module](https://docs.python.org/3/library/math.html) - Mathematische Funktionen
- [sys.float_info](https://docs.python.org/3/library/sys.html#sys.float_info) - Float-Implementierungsdetails
- [IEEE 754](https://en.wikipedia.org/wiki/IEEE_754) - Float-Standard
- [Decimal Module](https://docs.python.org/3/library/decimal.html) - Exakte Dezimalarithmetik

## Zusammenfassung

### Wichtigste Punkte

1. **Floats sind ungenau:**
   - Nutzen binäre Darstellung
   - Viele Dezimalzahlen nicht exakt darstellbar
   - Rundungsfehler akkumulieren

2. **Sichere Vergleiche:**
   - Nie `==` direkt nutzen
   - `math.isclose()` verwenden
   - Mit Toleranz arbeiten

3. **Spezielle Werte:**
   - `inf` / `-inf` für Unendlich
   - `nan` für ungültige Operationen
   - Prüfung mit `math.isnan()`, `math.isinf()`

4. **Wann Float nutzen?**
   - ✅ Wissenschaftliche Berechnungen
   - ✅ Physik, Geometrie
   - ✅ Statistik
   - ❌ Nicht für Geld! (nutze Decimal)
   - ❌ Nicht für exakte Gleichheit

5. **Best Practices:**
   - `math` Module für Funktionen
   - `math.fsum()` für akkurate Summen
   - `Decimal` für exakte Werte
   - Rundungsfehler einplanen

## Verwandte Themen

- [[01_Integer (int)|Integer]] - Ganzzahlen
- [[03_Complex|Complex]] - Komplexe Zahlen
- [[04_Decimal und Fraction|Decimal und Fraction]] - Exakte Arithmetik
- [[05_Übungen Numerische Typen|Übungen]] - Mehr Praxis
- [[06_Praxis/02_Performance/01_Performance Messung|Performance]] - Optimierung

---

← [[01_Integer (int)|Integer (int)]] | [[INDEX|📑 Index]] | [[03_Complex|Complex]] →