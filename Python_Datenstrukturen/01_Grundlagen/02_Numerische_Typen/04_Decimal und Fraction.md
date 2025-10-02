---
tags: [python, datenstrukturen, decimal, fraction, numerisch, präzision]
created: 2025-10-01
---

← [[03_Complex|Complex]] | [[INDEX|📑 Index]] | [[05_Übungen Numerische Typen|Übungen Numerische Typen]] →

# Decimal und Fraction

> [!tip] Lernziel
> Du verstehst, wann und wie du Decimal für exakte Dezimalzahlen und Fraction für Brüche nutzt

## Warum Decimal und Fraction?

**Problem mit Float:**
```python
# Float hat Rundungsfehler
print(0.1 + 0.1 + 0.1)  # 0.30000000000000004
print(0.1 + 0.2)        # 0.30000000000000004
```

**Lösung 1: Decimal** - Exakte Dezimalzahlen
**Lösung 2: Fraction** - Exakte Brüche

## Decimal - Exakte Dezimalzahlen

### Basics

```python
from decimal import Decimal

# Erstellen aus String (empfohlen!)
d1 = Decimal('0.1')
d2 = Decimal('0.2')
d3 = Decimal('0.3')

print(d1 + d2)        # 0.3 (exakt!)
print(d1 + d2 == d3)  # True (funktioniert!)

# ⚠️ Aus Float erstellen behält Rundungsfehler!
d_falsch = Decimal(0.1)
print(d_falsch)  # 0.1000000000000000055511151231257827021181583404541015625

# ✅ Immer aus String oder Integer erstellen
d_richtig = Decimal('0.1')
print(d_richtig)  # 0.1
```

### Decimal erstellen

```python
from decimal import Decimal

# Aus String (beste Methode)
d1 = Decimal('3.14')
d2 = Decimal('0.1')
d3 = Decimal('-5.67')

# Aus Integer
d4 = Decimal(42)
d5 = Decimal(-17)

# Aus Tuple (selten genutzt)
# (sign, digits, exponent)
# sign: 0 = positiv, 1 = negativ
# digits: Ziffern als Tuple
# exponent: 10er-Potenz
d6 = Decimal((0, (3, 1, 4), -2))  # 3.14
print(d6)  # 3.14

# Aus anderen Decimals
d7 = Decimal(d1)

# Spezielle Werte
inf = Decimal('Infinity')
neg_inf = Decimal('-Infinity')
nan = Decimal('NaN')
```

### Arithmetische Operationen

```python
from decimal import Decimal

a = Decimal('10.5')
b = Decimal('3.2')

# Alle Grundoperationen
print(a + b)   # 13.7
print(a - b)   # 7.3
print(a * b)   # 33.60
print(a / b)   # 3.28125
print(a // b)  # 3
print(a % b)   # 1.1
print(a ** 2)  # 110.25

# Negation und Absolutwert
print(-a)      # -10.5
print(abs(-a)) # 10.5

# Vergleiche funktionieren perfekt
x = Decimal('0.1') + Decimal('0.2')
y = Decimal('0.3')
print(x == y)  # True!
```

### Präzision und Kontext

```python
from decimal import Decimal, getcontext, setcontext, Context

# Aktueller Kontext
ctx = getcontext()
print(ctx.prec)  # 28 (Standard-Präzision: 28 Stellen)

# Präzision ändern
getcontext().prec = 50
d = Decimal(1) / Decimal(3)
print(d)  # 0.33333333333333333333333333333333333333333333333333

# Zurück zu Standard
getcontext().prec = 28

# Temporären Kontext nutzen
with Context(prec=10):
    d = Decimal(1) / Decimal(3)
    print(d)  # 0.3333333333 (10 Stellen)

# Außerhalb des with-Blocks wieder Standard
d = Decimal(1) / Decimal(3)
print(d)  # 0.3333333333333333333333333333 (28 Stellen)
```

### Rundung

```python
from decimal import Decimal, ROUND_HALF_UP, ROUND_DOWN, ROUND_UP, ROUND_CEILING, ROUND_FLOOR

d = Decimal('3.14159')

# Verschiedene Rundungsmodi
print(d.quantize(Decimal('0.01'), rounding=ROUND_HALF_UP))   # 3.14
print(d.quantize(Decimal('0.001'), rounding=ROUND_HALF_UP))  # 3.142

# Auf ganze Zahl runden
print(d.quantize(Decimal('1')))  # 3

# Rundungsmodi
d_pos = Decimal('2.5')
d_neg = Decimal('-2.5')

print(d_pos.quantize(Decimal('1'), ROUND_HALF_UP))    # 3 (aufrunden bei .5)
print(d_pos.quantize(Decimal('1'), ROUND_DOWN))       # 2 (Richtung Null)
print(d_pos.quantize(Decimal('1'), ROUND_UP))         # 3 (Weg von Null)
print(d_pos.quantize(Decimal('1'), ROUND_CEILING))    # 3 (Aufrunden)
print(d_pos.quantize(Decimal('1'), ROUND_FLOOR))      # 2 (Abrunden)

# Bei negativen Zahlen
print(d_neg.quantize(Decimal('1'), ROUND_CEILING))    # -2 (zur höheren Zahl)
print(d_neg.quantize(Decimal('1'), ROUND_FLOOR))      # -3 (zur niedrigeren Zahl)
```

### Decimal-Methoden

```python
from decimal import Decimal
import math

d = Decimal('123.456')

# Mathematische Operationen
print(d.sqrt())        # 11.11107555549866648462149404
print(d.exp())         # Exponentialfunktion
print(d.ln())          # Natürlicher Logarithmus
print(d.log10())       # Logarithmus zur Basis 10

# Informationen
print(d.as_tuple())    # DecimalTuple(sign=0, digits=(1,2,3,4,5,6), exponent=-3)
print(d.is_finite())   # True
print(d.is_infinite()) # False
print(d.is_nan())      # False
print(d.is_signed())   # False (positiv)
print(d.is_zero())     # False

# Zu anderen Typen
print(float(d))        # 123.456 (als Float)
print(int(d))          # 123 (als Integer)

# Normalisierung (entfernt trailing zeros)
d2 = Decimal('3.1400')
print(d2.normalize())  # 3.14
```

## Fraction - Exakte Brüche

### Basics

```python
from fractions import Fraction

# Brüche erstellen
f1 = Fraction(1, 2)        # 1/2
f2 = Fraction(3, 4)        # 3/4
f3 = Fraction(2, 8)        # 2/8 = 1/4 (automatisch gekürzt)

print(f1)  # 1/2
print(f2)  # 3/4
print(f3)  # 1/4 (gekürzt!)

# Aus String
f4 = Fraction('1/3')
f5 = Fraction('0.5')       # Aus Dezimalzahl
f6 = Fraction('3.14')

print(f4)  # 1/3
print(f5)  # 1/2
print(f6)  # 157/50

# Aus Float (⚠️ kann überraschend sein!)
f7 = Fraction(0.5)         # OK
f8 = Fraction(0.1)         # 3602879701896397/36028797018963968 (Rundungsfehler!)
print(f8)

# ✅ Besser: aus String
f9 = Fraction('0.1')       # 1/10 (korrekt)
print(f9)
```

### Arithmetische Operationen

```python
from fractions import Fraction

a = Fraction(1, 2)   # 1/2
b = Fraction(1, 3)   # 1/3

# Addition
print(a + b)         # 5/6

# Subtraktion
print(a - b)         # 1/6

# Multiplikation
print(a * b)         # 1/6

# Division
print(a / b)         # 3/2

# Potenz
print(a ** 2)        # 1/4

# Negation
print(-a)            # -1/2

# Absolutwert
print(abs(Fraction(-3, 4)))  # 3/4

# Vergleiche
print(a == b)        # False
print(a > b)         # True
print(a < Fraction(1))  # True
```

### Fraction-Eigenschaften

```python
from fractions import Fraction

f = Fraction(6, 8)  # Wird automatisch zu 3/4 gekürzt

# Zähler und Nenner
print(f.numerator)    # 3
print(f.denominator)  # 4

# Zu anderen Typen
print(float(f))       # 0.75
print(int(f))         # 0 (Floor-Division)

# Gemischte Zahl darstellen
def als_gemischte_zahl(f):
    ganzzahl = int(f)
    rest = f - ganzzahl
    if rest == 0:
        return str(ganzzahl)
    return f"{ganzzahl} {rest}" if ganzzahl != 0 else str(rest)

print(als_gemischte_zahl(Fraction(7, 4)))   # 1 3/4
print(als_gemischte_zahl(Fraction(8, 4)))   # 2
print(als_gemischte_zahl(Fraction(3, 4)))   # 3/4
```

### Fraction mit limit_denominator

```python
from fractions import Fraction
import math

# Pi als Bruch approximieren
pi = Fraction(str(math.pi))
print(pi)  # Sehr komplizierter Bruch

# Vereinfachen mit maximaler Nenner-Größe
pi_approx = pi.limit_denominator(100)
print(pi_approx)  # 22/7 (klassische Näherung)

pi_approx = pi.limit_denominator(1000)
print(pi_approx)  # 355/113 (bessere Näherung)

# Float zu einfachem Bruch
f = 0.333333
bruch = Fraction(f).limit_denominator(10)
print(bruch)  # 1/3

# Goldener Schnitt
phi = (1 + math.sqrt(5)) / 2
phi_bruch = Fraction(str(phi)).limit_denominator(1000)
print(phi_bruch)  # 987/610
```

## Decimal vs. Fraction vs. Float

### Vergleich

```python
from decimal import Decimal
from fractions import Fraction

# Gleiche Berechnung mit allen drei
wert = 0.1 + 0.2

# Float
f_result = wert
print(f"Float:    {f_result}")  # 0.30000000000000004

# Decimal
d_result = Decimal('0.1') + Decimal('0.2')
print(f"Decimal:  {d_result}")  # 0.3

# Fraction
fr_result = Fraction(1, 10) + Fraction(2, 10)
print(f"Fraction: {fr_result}")  # 3/10

# Zu Float konvertieren
print(f"Fraction als Float: {float(fr_result)}")  # 0.3
```

### Wann was nutzen?

| Typ | Wann nutzen | Vorteile | Nachteile |
|-----|-------------|----------|-----------|
| **Float** | Wissenschaftliche Berechnungen, Performance | Schnell, kompakt | Rundungsfehler |
| **Decimal** | Finanzen, exakte Dezimalzahlen | Exakt, kontrollierbare Präzision | Langsamer, mehr Speicher |
| **Fraction** | Mathematik, exakte Verhältnisse | Exakt, automatisch gekürzt | Langsam, Nenner kann groß werden |

## Typische Anwendungsfälle

### Anwendung 1: Finanzen (Decimal)

```python
from decimal import Decimal, ROUND_HALF_UP

def berechne_preis_mit_mwst(netto, mwst_prozent=19):
    """Berechnet Bruttopreis mit MwSt"""
    netto = Decimal(str(netto))
    mwst_prozent = Decimal(str(mwst_prozent))
    
    mwst_betrag = netto * (mwst_prozent / 100)
    brutto = netto + mwst_betrag
    
    # Auf Cent runden
    return brutto.quantize(Decimal('0.01'), rounding=ROUND_HALF_UP)

# Beispiel
netto = 99.99
brutto = berechne_preis_mit_mwst(netto)
print(f"Netto: {netto}€, Brutto: {brutto}€")  # Brutto: 118.99€

def teile_rechnung(betrag, anzahl_personen):
    """Teilt Rechnung fair auf"""
    betrag = Decimal(str(betrag))
    anzahl = Decimal(str(anzahl_personen))
    
    pro_person = (betrag / anzahl).quantize(Decimal('0.01'), rounding=ROUND_HALF_UP)
    rest = betrag - (pro_person * anzahl)
    
    return {
        'pro_person': pro_person,
        'anzahl': int(anzahl),
        'rest': rest
    }

# 100€ auf 3 Personen
aufteilung = teile_rechnung(100, 3)
print(aufteilung)  # {'pro_person': 33.33, 'anzahl': 3, 'rest': 0.01}
```

### Anwendung 2: Musik-Intervalle (Fraction)

```python
from fractions import Fraction

# Musikalische Intervalle als Frequenzverhältnisse
INTERVALLE = {
    'Prim': Fraction(1, 1),
    'Kleine Sekunde': Fraction(16, 15),
    'Große Sekunde': Fraction(9, 8),
    'Kleine Terz': Fraction(6, 5),
    'Große Terz': Fraction(5, 4),
    'Quarte': Fraction(4, 3),
    'Tritonus': Fraction(45, 32),
    'Quinte': Fraction(3, 2),
    'Kleine Sexte': Fraction(8, 5),
    'Große Sexte': Fraction(5, 3),
    'Kleine Septime': Fraction(9, 5),
    'Große Septime': Fraction(15, 8),
    'Oktave': Fraction(2, 1)
}

def berechne_frequenz(grundton, intervall):
    """Berechnet Frequenz eines Intervalls"""
    return float(grundton * INTERVALLE[intervall])

# A4 = 440 Hz
grundton = 440
print(f"Grundton A4: {grundton} Hz")
print(f"Quinte E5: {berechne_frequenz(grundton, 'Quinte'):.2f} Hz")
print(f"Oktave A5: {berechne_frequenz(grundton, 'Oktave'):.2f} Hz")
```

### Anwendung 3: Rezept-Skalierung (Fraction)

```python
from fractions import Fraction

class Rezept:
    def __init__(self, name, portionen):
        self.name = name
        self.portionen = portionen
        self.zutaten = {}
    
    def füge_zutat_hinzu(self, name, menge, einheit):
        """Fügt Zutat hinzu (Menge als Bruch)"""
        if isinstance(menge, (int, float)):
            menge = Fraction(str(menge))
        self.zutaten[name] = {'menge': menge, 'einheit': einheit}
    
    def skaliere(self, neue_portionen):
        """Skaliert Rezept auf neue Portionenzahl"""
        faktor = Fraction(neue_portionen, self.portionen)
        skaliertes = Rezept(self.name, neue_portionen)
        
        for name, zutat in self.zutaten.items():
            neue_menge = zutat['menge'] * faktor
            skaliertes.füge_zutat_hinzu(name, neue_menge, zutat['einheit'])
        
        return skaliertes
    
    def zeige_zutaten(self):
        """Zeigt Zutaten an"""
        print(f"\n{self.name} für {self.portionen} Portionen:")
        for name, zutat in self.zutaten.items():
            menge = zutat['menge']
            # Vereinfache Darstellung
            if menge.denominator == 1:
                menge_str = str(menge.numerator)
            else:
                menge_str = str(menge)
            print(f"  {menge_str} {zutat['einheit']} {name}")

# Beispiel: Pfannkuchen-Rezept
pfannkuchen = Rezept("Pfannkuchen", 4)
pfannkuchen.füge_zutat_hinzu("Mehl", Fraction(250), "g")
pfannkuchen.füge_zutat_hinzu("Milch", Fraction(500), "ml")
pfannkuchen.füge_zutat_hinzu("Eier", Fraction(3), "Stück")
pfannkuchen.füge_zutat_hinzu("Salz", Fraction(1, 2), "TL")

pfannkuchen.zeige_zutaten()

# Für 6 Personen skalieren
pfannkuchen_6 = pfannkuchen.skaliere(6)
pfannkuchen_6.zeige_zutaten()
```

### Anwendung 4: Wissenschaftliche Konstanten (Decimal)

```python
from decimal import Decimal, getcontext

# Hohe Präzision für Berechnungen
getcontext().prec = 50

# Physikalische Konstanten mit hoher Genauigkeit
c = Decimal('299792458')  # Lichtgeschwindigkeit m/s
h = Decimal('6.62607015e-34')  # Planck-Konstante
k_B = Decimal('1.380649e-23')  # Boltzmann-Konstante

def energie_photon(wellenlänge_nm):
    """
    Berechnet Energie eines Photons
    E = h * c / λ
    """
    wellenlänge_m = Decimal(str(wellenlänge_nm)) * Decimal('1e-9')
    energie = (h * c) / wellenlänge_m
    return energie

# Energie eines roten Lichtphotons (650 nm)
E = energie_photon(650)
print(f"Photonenenergie: {E:.10e} J")
```

## Häufige Fallstricke

### Fallstrick 1: Decimal aus Float

```python
from decimal import Decimal

# ❌ FEHLER: Float hat schon Rundungsfehler!
d = Decimal(0.1)
print(d)  # 0.1000000000000000055511151231257827021181583404541015625

# ✅ RICHTIG: Aus String
d = Decimal('0.1')
print(d)  # 0.1
```

### Fallstrick 2: Fraction aus Float

```python
from fractions import Fraction

# ❌ Float-Rundungsfehler wird übernommen
f = Fraction(0.1)
print(f)  # 3602879701896397/36028797018963968

# ✅ RICHTIG: Aus String
f = Fraction('0.1')
print(f)  # 1/10

# ✅ ODER: limit_denominator nutzen
f = Fraction(0.1).limit_denominator(100)
print(f)  # 1/10
```

### Fallstrick 3: Division gibt manchmal Float zurück

```python
from decimal import Decimal
from fractions import Fraction

# Decimal bleibt Decimal
d = Decimal('10') / Decimal('3')
print(type(d))  # <class 'decimal.Decimal'>

# Fraction bleibt Fraction
f = Fraction(10, 3)
print(type(f))  # <class 'fractions.Fraction'>

# ⚠️ Aber: Mischen mit Float gibt Float!
d2 = Decimal('10') / 3.0  # Division mit Float
print(type(d2))  # <class 'float'>
```

### Fallstrick 4: Performance

```python
import time
from decimal import Decimal
from fractions import Fraction

n = 100000

# Float: Sehr schnell
start = time.perf_counter()
result = 0.0
for i in range(n):
    result += 0.1
print(f"Float: {time.perf_counter() - start:.4f}s")

# Decimal: Langsamer
start = time.perf_counter()
result = Decimal('0')
d = Decimal('0.1')
for i in range(n):
    result += d
print(f"Decimal: {time.perf_counter() - start:.4f}s")

# Fraction: Am langsamsten
start = time.perf_counter()
result = Fraction(0)
f = Fraction(1, 10)
for i in range(n):
    result += f
print(f"Fraction: {time.perf_counter() - start:.4f}s")

# Float ist ~10x schneller als Decimal
# Decimal ist ~10x schneller als Fraction
```

### Fallstrick 5: Große Nenner bei Fraction

```python
from fractions import Fraction

# ❌ Nenner können sehr groß werden
f1 = Fraction(1, 3)
f2 = Fraction(1, 7)

# Viele Operationen
result = f1
for _ in range(10):
    result = result * f2 + f1

print(result)  # Sehr großer Nenner!
print(f"Nenner: {result.denominator}")  # Kann riesig werden

# ✅ Periodisch vereinfachen
result_simplified = result.limit_denominator(1000)
print(result_simplified)
```

## Performance-Hinweise

### Speicher und Geschwindigkeit

```python
import sys
from decimal import Decimal
from fractions import Fraction

# Speicherverbrauch
f = 3.14
d = Decimal('3.14')
fr = Fraction(314, 100)

print(f"Float:    {sys.getsizeof(f)} Bytes")      # 24 Bytes
print(f"Decimal:  {sys.getsizeof(d)} Bytes")      # 104 Bytes
print(f"Fraction: {sys.getsizeof(fr)} Bytes")     # 32 Bytes

# Decimal und Fraction brauchen deutlich mehr Speicher!
```

### Wann was nutzen - Performance

```python
# ✅ Float für Performance-kritische Berechnungen
# ✅ Decimal für Finanzberechnungen (Genauigkeit wichtiger als Speed)
# ✅ Fraction für exakte Bruchrechnung (kleine Datenmengen)

# ❌ Nicht Decimal in Tight Loops mit Millionen Iterationen
# ❌ Nicht Fraction für große Datenmengen
```

## Übungsaufgaben

### Übung 1: Geldrechner (Leicht)

Implementiere einen Währungsrechner mit Decimal, der mehrere Währungen umrechnet.

**Erwarteter Output:**
```python
rechner = Währungsrechner()
rechner.setze_kurs('USD', Decimal('1.08'))
rechner.setze_kurs('GBP', Decimal('0.87'))

print(rechner.konvertiere(Decimal('100'), 'EUR', 'USD'))  # 108.00 USD
print(rechner.konvertiere(Decimal('100'), 'EUR', 'GBP'))  # 87.00 GBP
```

### Übung 2: Bruch-Rechner (Mittel)

Erstelle einen Taschenrechner der mit Brüchen rechnet und das Ergebnis gekürzt ausgibt.

**Erwarteter Output:**
```python
print(bruch_rechnen("1/2 + 1/3"))  # 5/6
print(bruch_rechnen("3/4 * 2/5"))  # 3/10
print(bruch_rechnen("1/2 / 1/4"))  # 2
```

### Übung 3: Zinseszins exakt (Mittel)

Berechne Zinseszins mit Decimal und vergleiche mit Float-Ergebnis.

**Erwarteter Output:**
```python
kapital = 10000
zinssatz = 5.5
jahre = 10

float_ergebnis = zinseszins_float(kapital, zinssatz, jahre)
decimal_ergebnis = zinseszins_decimal(kapital, zinssatz, jahre)

print(f"Float:   {float_ergebnis}")
print(f"Decimal: {decimal_ergebnis}")
print(f"Differenz: {abs(float_ergebnis - float(decimal_ergebnis))}")
```

### Übung 4: Kettenbruch (Schwer)

Konvertiere eine Dezimalzahl in einen Kettenbruch und zurück.

**Erwarteter Output:**
```python
# Pi als Kettenbruch
kettenbruch = zu_kettenbruch(math.pi, max_tiefe=10)
print(kettenbruch)  # [3, 7, 15, 1, 292, ...]

# Zurück zu Dezimalzahl
dezimal = von_kettenbruch(kettenbruch)
print(f"Pi ≈ {dezimal}")
```

### Übung 5: Steuer-Berechnung (Schwer)

Implementiere ein vollständiges Steuersystem mit Decimal (progressiver Steuersatz, Freibeträge, etc.).

**Erwarteter Output:**
```python
steuer = SteuerRechner()
einkommen = Decimal('50000')

ergebnis = steuer.berechne(einkommen)
print(ergebnis)
# {
#     'einkommen': 50000.00,
#     'steuer': 12345.67,
#     'netto': 37654.33,
#     'steuersatz_effektiv': 24.69%
# }
```

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [decimal Module](https://docs.python.org/3/library/decimal.html) - Decimal Arithmetik
- [fractions Module](https://docs.python.org/3/library/fractions.html) - Rationale Zahlen
- [Decimal FAQ](https://docs.python.org/3/library/decimal.html#decimal-faq) - Häufige Fragen zu Decimal
- [Floating Point Arithmetic Issues](https://docs.python.org/3/tutorial/floatingpoint.html) - Warum Float problematisch ist

## Zusammenfassung

### Wichtigste Punkte

1. **Decimal:**
   - Exakte Dezimalzahlen
   - Immer aus String erstellen!
   - Kontrollierbare Präzision
   - Perfekt für Finanzen

2. **Fraction:**
   - Exakte Brüche
   - Automatisch gekürzt
   - Gut für Mathematik
   - `limit_denominator()` für Vereinfachung

3. **Vergleich:**
   - Float: Schnell, ungenau
   - Decimal: Langsam, exakt, mehr Speicher
   - Fraction: Am langsamsten, exakt

4. **Best Practices:**
   - ✅ Decimal aus String: `Decimal('0.1')`
   - ✅ Fraction aus String: `Fraction('1/10')`
   - ❌ Nie aus Float erstellen!
   - ✅ Decimal für Geld
   - ✅ Fraction für Verhältnisse

5. **Performance:**
   - Float: 1x
   - Decimal: ~10x langsamer
   - Fraction: ~100x langsamer
   - Decimal: ~4x mehr Speicher als Float

## Verwandte Themen

- [[01_Integer (int)|Integer]] - Ganzzahlen
- [[02_Float|Float]] - Gleitkommazahlen mit Rundungsfehlern
- [[03_Complex|Complex]] - Komplexe Zahlen
- [[05_Übungen Numerische Typen|Übungen]] - Praktische Aufgaben
- [[06_Praxis/02_Performance/01_Performance Messung|Performance]] - Optimierung

---

← [[03_Complex|Complex]] | [[INDEX|📑 Index]] | [[05_Übungen Numerische Typen|Übungen Numerische Typen]] →