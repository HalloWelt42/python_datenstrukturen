---
tags: [python, datenstrukturen, complex, numerisch, imaginär]
created: 2025-10-01
---

← [[02_Float|Float]] | [[INDEX|📑 Index]] | [[04_Decimal und Fraction|Decimal und Fraction]] →

# Complex

> [!tip] Lernziel
> Du verstehst komplexe Zahlen in Python, ihre Darstellung, Operationen und typische Anwendungsfälle

## Was ist eine komplexe Zahl?

Eine **komplexe Zahl** hat einen Realteil und einen Imaginärteil: `z = a + bi`
- **a** = Realteil
- **b** = Imaginärteil
- **i** = imaginäre Einheit (i² = -1)

In Python wird `j` statt `i` verwendet (aus der Elektrotechnik):

```python
# Komplexe Zahlen erstellen
z1 = 3 + 4j
z2 = complex(3, 4)  # Gleich wie oben
z3 = 2j             # Nur Imaginärteil
z4 = 5 + 0j         # Nur Realteil

print(z1)           # (3+4j)
print(type(z1))     # <class 'complex'>
```

## Syntax und Grundlagen

### Komplexe Zahlen erstellen

```python
# Literal-Syntax
z = 3 + 4j
print(z)            # (3+4j)

# Mit complex() Konstruktor
z = complex(3, 4)
print(z)            # (3+4j)

# Aus String
z = complex("3+4j")
print(z)            # (3+4j)

# Nur Realteil
z = complex(5)
print(z)            # (5+0j)

# Nur Imaginärteil
z = complex(0, 3)
print(z)            # 3j

# Negative Werte
z = -2 - 5j
print(z)            # (-2-5j)

# Wissenschaftliche Notation
z = 1.5e2 + 3.2e-1j
print(z)            # (150+0.32j)
```

### Real- und Imaginärteil zugreifen

```python
z = 3 + 4j

# Realteil
print(z.real)       # 3.0 (immer float!)
print(type(z.real)) # <class 'float'>

# Imaginärteil
print(z.imag)       # 4.0 (immer float!)
print(type(z.imag)) # <class 'float'>

# ⚠️ real und imag sind read-only!
# z.real = 5  # AttributeError!
```

## Grundlegende Operationen

### Arithmetische Operationen

```python
z1 = 3 + 4j
z2 = 1 + 2j

# Addition
print(z1 + z2)      # (4+6j)

# Subtraktion
print(z1 - z2)      # (2+2j)

# Multiplikation
# (3+4j)(1+2j) = 3 + 6j + 4j + 8j² = 3 + 10j - 8 = -5 + 10j
print(z1 * z2)      # (-5+10j)

# Division
# (3+4j) / (1+2j) = (3+4j)(1-2j) / (1+4) = (11-2j) / 5
print(z1 / z2)      # (2.2-0.4j)

# Potenz
print(z1 ** 2)      # (-7+24j)

# Negation
print(-z1)          # (-3-4j)
```

### Komplexe Konjugation

```python
z = 3 + 4j

# Konjugierte (Imaginärteil invertiert)
print(z.conjugate())  # (3-4j)

# Nützlich für Division:
# z / w = z * w̄ / |w|²
z = 1 + 2j
w = 3 + 4j
# Division mit Konjugation
numerator = z * w.conjugate()
denominator = (w * w.conjugate()).real
result = complex(numerator.real / denominator, numerator.imag / denominator)
print(result)  # Gleich wie z / w
print(z / w)   # (0.44+0.08j)
```

### Betrag und Phase

```python
import math
import cmath

z = 3 + 4j

# Betrag (Abstand vom Ursprung)
print(abs(z))       # 5.0
# Formel: |z| = √(a² + b²) = √(9 + 16) = 5

# Phase (Winkel zur reellen Achse)
print(cmath.phase(z))  # 0.9272952180016122 (Radiant)
# In Grad
print(math.degrees(cmath.phase(z)))  # 53.13010235415598°

# Polardarstellung
r, phi = cmath.polar(z)
print(f"r={r}, φ={phi}")  # r=5.0, φ=0.9272952180016122

# Zurück zu kartesischer Form
z_neu = cmath.rect(r, phi)
print(z_neu)  # (3.0000000000000004+3.9999999999999996j)
```

## cmath Module

Das `cmath` Module bietet mathematische Funktionen für komplexe Zahlen:

```python
import cmath

z = 1 + 1j

# Exponentialfunktion
print(cmath.exp(z))     # (1.4686939399158851+2.2873552871788423j)

# Logarithmus
print(cmath.log(z))     # (0.34657359027997264+0.7853981633974483j)
print(cmath.log10(z))   # (0.15051499783199057+0.3410940884604603j)

# Wurzel
print(cmath.sqrt(z))    # (1.09868411346781+0.45508986056222733j)
print(cmath.sqrt(-1))   # 1j (imaginäre Einheit!)

# Trigonometrische Funktionen
print(cmath.sin(z))     # (1.2984575814159773+0.6349639147847361j)
print(cmath.cos(z))     # (0.8337300251311491-0.9888977057628651j)
print(cmath.tan(z))     # (0.2717525853195118+1.0839233273386946j)

# Hyperbolische Funktionen
print(cmath.sinh(z))    # (0.6349639147847361+1.2984575814159773j)
print(cmath.cosh(z))    # (0.8337300251311491+0.9888977057628651j)
print(cmath.tanh(z))    # (1.0839233273386946+0.2717525853195118j)

# Konstanten
print(cmath.pi)         # 3.141592653589793
print(cmath.e)          # 2.718281828459045
print(cmath.tau)        # 6.283185307179586

# Polarkoordinaten
print(cmath.polar(z))   # (1.4142135623730951, 0.7853981633974483)
print(cmath.rect(1.414, 0.785))  # (0.9997989761024128+0.9996000426839143j)

# Spezielle Werte
print(cmath.isfinite(z))  # True
print(cmath.isinf(complex(float('inf'), 0)))  # True
print(cmath.isnan(complex(float('nan'), 0)))  # True
```

## Details und Interna

### Memory Layout

```python
import sys

# Complex benötigt Platz für zwei Floats
z = 3 + 4j
print(sys.getsizeof(z))  # 32 Bytes (auf 64-bit System)

# Vergleich:
print(sys.getsizeof(3.0))     # 24 Bytes (ein Float)
print(sys.getsizeof(3))       # 28 Bytes (Integer)
```

### Euler's Formel

Die wichtigste Formel mit komplexen Zahlen: `e^(iφ) = cos(φ) + i·sin(φ)`

```python
import cmath
import math

# Euler's Identität: e^(iπ) + 1 = 0
phi = math.pi
euler = cmath.exp(1j * phi)
print(euler)  # (-1+1.2246467991473532e-16j) ≈ -1

# Aufgrund von Rundungsfehlern nicht exakt -1
print(math.isclose(euler.real, -1))  # True
print(math.isclose(euler.imag, 0))   # True

# Kreisdarstellung
# Punkt auf Einheitskreis bei Winkel φ
def punkt_auf_kreis(phi):
    return cmath.rect(1, phi)

# Verschiedene Winkel
print(punkt_auf_kreis(0))           # (1+0j) - 0°
print(punkt_auf_kreis(math.pi/2))   # 6.123233995736766e-17+1j ≈ i - 90°
print(punkt_auf_kreis(math.pi))     # (-1+1.2246467991473532e-16j) ≈ -1 - 180°
print(punkt_auf_kreis(3*math.pi/2)) # -1.8369701987210297e-16-1j ≈ -i - 270°
```

### Komplexe Ebene (Gaußsche Zahlenebene)

```python
# Komplexe Zahlen als 2D-Punkte/Vektoren
z1 = 3 + 4j  # Punkt (3, 4)
z2 = 1 - 2j  # Punkt (1, -2)

# Addition = Vektoraddition
z3 = z1 + z2  # (4+2j) = Punkt (4, 2)

# Multiplikation mit reeller Zahl = Skalierung
z4 = 2 * z1   # (6+8j) - doppelt so weit vom Ursprung

# Betrag = Distanz vom Ursprung
print(abs(z1))  # 5.0 = √(3² + 4²)

# Distanz zwischen zwei Punkten
distanz = abs(z1 - z2)
print(distanz)  # 6.708203932499369 = √((3-1)² + (4-(-2))²)
```

## Typische Anwendungsfälle

### Anwendung 1: Elektrotechnik (Wechselstromrechnung)

```python
import cmath
import math

def impedanz_rechnen():
    """
    Berechnet Impedanz in Wechselstromkreis
    Z = R + jωL - j/(ωC)
    """
    R = 100        # Widerstand in Ohm
    L = 0.1        # Induktivität in Henry
    C = 1e-6       # Kapazität in Farad
    f = 50         # Frequenz in Hz
    omega = 2 * math.pi * f
    
    # Impedanz
    Z = complex(R, omega * L - 1/(omega * C))
    
    return {
        "impedanz": Z,
        "betrag": abs(Z),
        "phase": math.degrees(cmath.phase(Z)),
        "real": Z.real,
        "imaginär": Z.imag
    }

ergebnis = impedanz_rechnen()
print(f"Impedanz: {ergebnis['impedanz']:.2f} Ω")
print(f"Betrag: {ergebnis['betrag']:.2f} Ω")
print(f"Phase: {ergebnis['phase']:.2f}°")
```

### Anwendung 2: Signalverarbeitung (Fourier-Transformation)

```python
import cmath
import math

def dft_einfach(signal):
    """
    Diskrete Fourier-Transformation (vereinfacht)
    X[k] = Σ x[n] * e^(-2πikn/N)
    """
    N = len(signal)
    spektrum = []
    
    for k in range(N):
        summe = 0
        for n in range(N):
            exponent = -2j * math.pi * k * n / N
            summe += signal[n] * cmath.exp(exponent)
        spektrum.append(summe)
    
    return spektrum

# Beispiel: Sinus-Signal
N = 8
signal = [math.sin(2 * math.pi * n / N) for n in range(N)]

spektrum = dft_einfach(signal)
for k, wert in enumerate(spektrum):
    print(f"Frequenz {k}: {abs(wert):.2f}")
```

### Anwendung 3: Fraktale (Mandelbrot-Menge)

```python
def mandelbrot(c, max_iter=100):
    """
    Prüft ob komplexe Zahl c zur Mandelbrot-Menge gehört
    z_n+1 = z_n² + c, z_0 = 0
    """
    z = 0
    for n in range(max_iter):
        z = z**2 + c
        if abs(z) > 2:
            return n  # Divergiert nach n Iterationen
    return max_iter  # Konvergiert

# Teste einige Punkte
test_punkte = [
    0 + 0j,      # Ursprung (in Menge)
    -1 + 0j,     # Auf reeller Achse (in Menge)
    0.5 + 0.5j,  # Außerhalb
    -0.5 + 0.5j  # Nahe Grenze
]

for c in test_punkte:
    iter_count = mandelbrot(c)
    status = "in Menge" if iter_count == 100 else f"divergiert nach {iter_count}"
    print(f"{c}: {status}")
```

### Anwendung 4: Quantenmechanik (Wellenfunktionen)

```python
import cmath
import math

def wellenpaket(x, t, k0=1, omega=1, sigma=1):
    """
    Gaußsches Wellenpaket
    ψ(x,t) = exp(-(x-vt)²/4σ²) * exp(i(kx-ωt))
    """
    v = omega / k0  # Phasengeschwindigkeit
    
    # Gaußsche Einhüllende (reell)
    envelope = math.exp(-((x - v*t)**2) / (4 * sigma**2))
    
    # Oszillierende Phase (komplex)
    phase = cmath.exp(1j * (k0*x - omega*t))
    
    return envelope * phase

# Wellenpaket zu verschiedenen Zeiten
for t in [0, 1, 2]:
    psi = wellenpaket(x=0, t=t)
    print(f"t={t}: ψ={psi:.4f}, |ψ|²={abs(psi)**2:.4f}")
```

### Anwendung 5: Geometrie (Rotationen)

```python
import cmath
import math

def rotiere_punkt(punkt, winkel):
    """
    Rotiert komplexen Punkt um Winkel (in Radiant)
    z' = z * e^(iφ)
    """
    return punkt * cmath.exp(1j * winkel)

# Punkt (3, 4) im 2D
z = 3 + 4j
print(f"Original: {z}")

# Rotiere um 90° gegen den Uhrzeigersinn
z_rot = rotiere_punkt(z, math.pi/2)
print(f"Nach 90°: {z_rot}")  # Sollte ungefähr (-4+3j) sein

# Rotiere um 45°
z_rot = rotiere_punkt(z, math.pi/4)
print(f"Nach 45°: {z_rot}")

# Mehrere Punkte rotieren (Polygon)
polygon = [1+0j, 0+1j, -1+0j, 0-1j]  # Quadrat
rotiert = [rotiere_punkt(p, math.pi/4) for p in polygon]
print("Rotiertes Quadrat:", rotiert)
```

## Häufige Fallstricke

### Fallstrick 1: j muss an Zahl angehängt sein

```python
# ❌ FEHLER: j ohne Zahl
# z = 3 + j  # NameError: name 'j' is not defined

# ✅ RICHTIG: 1j für imaginäre Einheit
z = 3 + 1j
print(z)  # (3+1j)

# Oder explizit
j = 1j  # Variable definieren
z = 3 + j
print(z)  # (3+1j)
```

### Fallstrick 2: Division durch Null

```python
# Division durch komplexe Null
z = 0 + 0j

try:
    result = 1 / z
except ZeroDivisionError as e:
    print(f"Fehler: {e}")  # complex division by zero
```

### Fallstrick 3: Vergleichsoperatoren

```python
# ❌ < und > funktionieren nicht mit komplexen Zahlen!
z1 = 3 + 4j
z2 = 1 + 2j

# print(z1 < z2)  # TypeError: '<' not supported

# ✅ Vergleiche Beträge
print(abs(z1) < abs(z2))  # False (5.0 < 2.236...)
print(abs(z1) > abs(z2))  # True

# Gleichheit funktioniert
print(z1 == z2)  # False
print(z1 != z2)  # True
print((3+4j) == complex(3, 4))  # True
```

### Fallstrick 4: Rundungsfehler bei Konvertierungen

```python
import math

# Real/Imaginär sind immer floats
z = complex(3, 4)
print(z.real)  # 3.0 (nicht 3!)
print(type(z.real))  # <class 'float'>

# Euler mit Rundungsfehlern
z = cmath.exp(1j * math.pi)
print(z.real)  # -1.0
print(z.imag)  # 1.2246467991473532e-16 (sollte 0 sein!)

# Prüfung mit Toleranz
print(math.isclose(z.imag, 0))  # True
```

### Fallstrick 5: String-Parsing

```python
# String zu Complex kann tricky sein
z = complex("3+4j")
print(z)  # (3+4j)

# ⚠️ Leerzeichen führen zu Fehler
# z = complex("3 + 4j")  # ValueError!

# ⚠️ Vorzeichen wichtig
z = complex("3-4j")  # Minus
print(z)  # (3-4j)

# ✅ Besser: Nutze complex() Konstruktor
z = complex(3, -4)
print(z)  # (3-4j)
```

## Performance-Hinweise

### Zeitkomplexität

| Operation | Zeitkomplexität |
|-----------|----------------|
| `+`, `-` | O(1) |
| `*`, `/` | O(1) |
| `**` | O(1) |
| `abs()` | O(1) |
| `cmath` Funktionen | O(1) |

Alle Operationen sind O(1) da Complex aus zwei Floats besteht.

### Performance-Tipps

```python
import time
import math
import cmath

# abs() ist optimiert
z = 3 + 4j

start = time.perf_counter()
for _ in range(1000000):
    r = abs(z)
print(f"abs(): {time.perf_counter() - start:.4f}s")

# Manuelle Berechnung langsamer
start = time.perf_counter()
for _ in range(1000000):
    r = math.sqrt(z.real**2 + z.imag**2)
print(f"manual: {time.perf_counter() - start:.4f}s")
# abs() ist ~2x schneller!

# ✅ Nutze cmath statt eigene Implementierungen
# cmath Funktionen sind in C implementiert und sehr schnell
```

### Speicher-Effizienz

```python
import sys

# Complex braucht ~32 Bytes
z = 3 + 4j
print(sys.getsizeof(z))  # 32 Bytes

# Alternativ: Tuple von Floats
t = (3.0, 4.0)
print(sys.getsizeof(t))  # 56 Bytes

# Complex ist speichereffizienter!
# Aber: Für große Arrays nutze numpy
```

## Übungsaufgaben

### Übung 1: Komplexer Taschenrechner (Leicht)

Schreibe Funktionen für komplexe Arithmetik, die Ergebnisse in Polarform ausgeben.

**Erwarteter Output:**
```python
z1 = 3 + 4j
z2 = 1 + 2j

ergebnis = complex_addieren(z1, z2)
print(ergebnis)
# {
#   'kartesisch': (4+6j),
#   'polar': {'r': 7.21, 'phi': 56.31°}
# }
```

### Übung 2: Komplexe Wurzeln (Mittel)

Finde alle n-ten Wurzeln einer komplexen Zahl. Eine Zahl hat n verschiedene n-te Wurzeln!

**Erwarteter Output:**
```python
wurzeln = n_te_wurzeln(1+0j, n=3)  # Kubikwurzeln von 1
# [(1+0j), (-0.5+0.866j), (-0.5-0.866j)]
```

### Übung 3: Impedanz-Rechner (Mittel)

Implementiere einen Wechselstromkreis-Rechner für Serien- und Parallelschaltungen.

**Erwarteter Output:**
```python
Z1 = 100 + 50j  # Widerstand + Induktivität
Z2 = 200 - 30j  # Widerstand + Kapazität

Z_serie = serie(Z1, Z2)      # Z1 + Z2
Z_parallel = parallel(Z1, Z2) # 1/(1/Z1 + 1/Z2)
```

### Übung 4: Mandelbrot-Visualisierung (Schwer)

Erstelle eine Funktion die prüft, ob Punkte in der Mandelbrot-Menge sind und speichere als "ASCII-Art".

**Erwarteter Output:**
```python
visualisiere_mandelbrot(breite=40, höhe=20, max_iter=50)
# Gibt ASCII-Art der Mandelbrot-Menge aus
```

### Übung 5: Komplexe Polynome (Schwer)

Implementiere einen Polynomauswertung und Nullstellensuche für komplexe Polynome.

**Erwarteter Output:**
```python
# p(z) = z² + 2z + 2
koeffizienten = [1, 2, 2]  # z² + 2z + 2

print(polynom_auswerten(koeffizienten, 1+1j))
# (4+4j)

nullstellen = finde_nullstellen(koeffizienten)
# [(-1+1j), (-1-1j)]
```

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [Complex Numbers](https://docs.python.org/3/library/stdtypes.html#typesnumeric) - Complex Typ Dokumentation
- [cmath Module](https://docs.python.org/3/library/cmath.html) - Mathematische Funktionen für komplexe Zahlen
- [Complex Number Operations](https://docs.python.org/3/reference/expressions.html#binary-arithmetic-operations) - Operationen mit komplexen Zahlen

## Zusammenfassung

### Wichtigste Punkte

1. **Syntax:**
   - `a + bj` für komplexe Zahlen
   - `complex(a, b)` Konstruktor
   - `.real` und `.imag` für Zugriff

2. **Operationen:**
   - Alle arithmetischen Operationen möglich
   - `abs()` für Betrag
   - `.conjugate()` für Konjugation

3. **cmath Module:**
   - Alle math Funktionen für Complex
   - `polar()` und `rect()` für Umwandlung
   - `phase()` für Winkel

4. **Anwendungen:**
   - Elektrotechnik (Wechselstrom)
   - Signalverarbeitung (Fourier)
   - Quantenmechanik
   - Fraktale
   - 2D Rotationen

5. **Vorsicht:**
   - Keine Vergleichsoperatoren < >
   - Rundungsfehler bei Polar/Kartesisch
   - `j` muss an Zahl hängen

## Verwandte Themen

- [[01_Integer (int)|Integer]] - Ganzzahlen
- [[02_Float|Float]] - Gleitkommazahlen
- [[04_Decimal und Fraction|Decimal]] - Exakte Arithmetik
- [[05_Übungen Numerische Typen|Übungen]] - Mehr Praxis

---

← [[02_Float|Float]] | [[INDEX|📑 Index]] | [[04_Decimal und Fraction|Decimal und Fraction]] →