---
tags: [python, datenstrukturen, übungen, numerisch, praxis]
created: 2025-10-01
---

← [[04_Decimal und Fraction|Decimal und Fraction]] | [[INDEX|📑 Index]] | [[01_Grundlagen/03_Boolean_und_None/01_Boolean (bool)|Boolean (bool)]] →

# Übungen Numerische Typen

> [!tip] Lernziel
> Du festigst dein Wissen über Integer, Float, Complex, Decimal und Fraction durch praktische Übungen

## Übungsstruktur

Jede Übung hat:
- 📝 **Aufgabe** - Was zu tun ist
- 💡 **Hinweise** - Tipps zur Lösung
- ✅ **Erwarteter Output** - So sollte es aussehen
- 🎯 **Schwierigkeit** - Leicht / Mittel / Schwer

Die Lösungen findest du in [[06_Praxis/04_Lösungen/Lösungen Teil 1|Lösungen Teil 1]].

## Integer Übungen

### Übung 1: Zahlensystem-Konverter 🎯 Leicht

**Aufgabe:**
Schreibe eine Funktion `konvertiere_zahlensysteme(zahl)`, die eine Dezimalzahl in alle Zahlensysteme konvertiert und formatiert ausgibt.

**Erwarteter Output:**
```python
konvertiere_zahlensysteme(42)
# Dezimal:      42
# Binär:        0b101010 (6 Bits)
# Oktal:        0o52
# Hexadezimal:  0x2A
```

💡 **Hinweise:**
- Nutze `bin()`, `oct()`, `hex()`
- Für Bit-Anzahl: `.bit_length()`

---

### Übung 2: Primzahl-Prüfer 🎯 Leicht

**Aufgabe:**
Erstelle eine Funktion `ist_primzahl(n)`, die prüft ob eine Zahl eine Primzahl ist.

**Erwarteter Output:**
```python
print(ist_primzahl(2))    # True
print(ist_primzahl(17))   # True
print(ist_primzahl(18))   # False
print(ist_primzahl(97))   # True
```

💡 **Hinweise:**
- Prüfe nur bis √n
- Nutze `range(2, int(n**0.5) + 1)`
- Sonderfall: 2 ist die einzige gerade Primzahl

---

### Übung 3: Römische Zahlen 🎯 Mittel

**Aufgabe:**
Konvertiere Dezimalzahlen zu römischen Zahlen und zurück.

**Erwarteter Output:**
```python
print(zu_römisch(42))      # XLII
print(zu_römisch(1994))    # MCMXCIV
print(von_römisch("XLII")) # 42
print(von_römisch("MCMXCIV"))  # 1994
```

💡 **Hinweise:**
- Mapping: I=1, V=5, X=10, L=50, C=100, D=500, M=1000
- Subtraktionsregel: IV=4, IX=9, XL=40, XC=90, CD=400, CM=900
- Für zu_römisch: Von groß nach klein durchgehen

---

### Übung 4: Collatz-Vermutung 🎯 Mittel

**Aufgabe:**
Implementiere die Collatz-Sequenz und finde die Anzahl Schritte bis zur 1.

**Regel:**
- Wenn n gerade: n/2
- Wenn n ungerade: 3n+1

**Erwarteter Output:**
```python
schritte, sequenz = collatz(10)
print(f"Schritte: {schritte}")  # 6
print(f"Sequenz: {sequenz}")    # [10, 5, 16, 8, 4, 2, 1]
```

💡 **Hinweise:**
- Nutze eine While-Schleife
- Sammle Werte in Liste
- Zähle Schritte mit

---

### Übung 5: Große Zahlen Fakultät 🎯 Schwer

**Aufgabe:**
Berechne Fakultät sehr großer Zahlen und analysiere das Ergebnis.

**Erwarteter Output:**
```python
ergebnis = fakultät_analysieren(100)
print(ergebnis)
# {
#     'wert': 93326215443944152681699238856266700490715968264381621468592963895217599993229915608941463976156518286253697920827223758251185210916864000000000000000000000000,
#     'anzahl_ziffern': 158,
#     'anzahl_nullen_am_ende': 24,
#     'quersumme': 648
# }
```

💡 **Hinweise:**
- Nutze `math.factorial()` oder eigene Implementierung
- Konvertiere zu String für Analyse
- Nullen am Ende = count('0') vom Ende

---

## Float Übungen

### Übung 6: Sichere Float-Gleichheit 🎯 Leicht

**Aufgabe:**
Implementiere eine Funktion `float_vergleich(a, b, toleranz=1e-9)`, die Floats sicher vergleicht.

**Erwarteter Output:**
```python
print(float_vergleich(0.1 + 0.2, 0.3))           # "gleich"
print(float_vergleich(3.14159, 3.14160, 1e-4))   # "gleich"
print(float_vergleich(1.0, 1.1))                  # "a < b"
```

💡 **Hinweise:**
- Nutze `abs(a - b) < toleranz`
- Drei mögliche Rückgaben: "gleich", "a < b", "a > b"

---

### Übung 7: Rundungsfehler-Demo 🎯 Mittel

**Aufgabe:**
Demonstriere wie Rundungsfehler bei wiederholten Operationen akkumulieren.

**Erwarteter Output:**
```python
demo_rundungsfehler(0.1, 1000, operator='+')
# Operation: 1000x Addition von 0.1
# Erwartet: 100.0
# Tatsächlich: 100.00000000000007
# Fehler: 7.105427357601002e-14
# Relativer Fehler: 7.105427357601002e-16
```

💡 **Hinweise:**
- Führe Operation n-mal durch
- Berechne erwartetes Ergebnis
- Zeige absoluten und relativen Fehler

---

### Übung 8: Geometrie-Rechner 🎯 Mittel

**Aufgabe:**
Erstelle Funktionen für verschiedene geometrische Berechnungen.

**Erwarteter Output:**
```python
print(kreis_umfang(5))        # 31.41592653589793
print(kugel_volumen(3))       # 113.09733552923255
print(dreieck_fläche(3, 4, 5)) # 6.0 (Heron's Formel)
```

💡 **Hinweise:**
- Nutze `math.pi`
- Kugel: V = 4/3 * π * r³
- Heron: s = (a+b+c)/2, A = √(s(s-a)(s-b)(s-c))

---

### Übung 9: Numerische Integration 🎯 Schwer

**Aufgabe:**
Implementiere Trapezregel zur numerischen Integration.

**Erwarteter Output:**
```python
def f(x):
    return x**2

integral = trapez_integration(f, 0, 1, schritte=1000)
print(f"Integral von x² von 0 bis 1: {integral:.6f}")
# Sollte ~0.333333 sein (exakt: 1/3)
```

💡 **Hinweise:**
- Teile Intervall in n Schritte
- Berechne Fläche jedes Trapezes
- Summiere alle Flächen

---

### Übung 10: Physik-Simulator 🎯 Schwer

**Aufgabe:**
Simuliere Wurfparabel mit verschiedenen Anfangsbedingungen.

**Erwarteter Output:**
```python
wurf = wurfparabel_simulieren(v0=20, winkel=45, schrittweite=0.1)
print(wurf['max_höhe'])      # Maximale Höhe
print(wurf['reichweite'])    # Horizontale Reichweite
print(wurf['flugzeit'])      # Gesamte Flugzeit
print(wurf['trajektorie'])   # Liste von (x, y) Punkten
```

💡 **Hinweise:**
- x(t) = v₀ * cos(α) * t
- y(t) = v₀ * sin(α) * t - 0.5 * g * t²
- Simuliere bis y ≤ 0

---

## Complex Übungen

### Übung 11: Komplexe Arithmetik 🎯 Leicht

**Aufgabe:**
Implementiere einen komplexen Taschenrechner mit Polar-Ausgabe.

**Erwarteter Output:**
```python
z1 = 3 + 4j
z2 = 1 + 2j

ergebnis = complex_rechner(z1, z2, '+')
print(ergebnis)
# {
#     'kartesisch': (4+6j),
#     'betrag': 7.211102550927978,
#     'phase': 56.31 (Grad)
# }
```

💡 **Hinweise:**
- Nutze `abs()` für Betrag
- `cmath.phase()` für Phase
- `math.degrees()` für Grad

---

### Übung 12: N-te Wurzeln 🎯 Mittel

**Aufgabe:**
Finde alle n-ten Wurzeln einer komplexen Zahl.

**Erwarteter Output:**
```python
wurzeln = n_te_wurzeln(8+0j, n=3)  # Kubikwurzeln von 8
print(len(wurzeln))  # 3
print(wurzeln[0])    # 2+0j
print(wurzeln[1])    # -1+1.732...j
print(wurzeln[2])    # -1-1.732...j
```

💡 **Hinweise:**
- Konvertiere zu Polarform
- Wurzel: r^(1/n), φ/n
- n Wurzeln: Drehe um 2π/n

---

### Übung 13: Mandelbrot-Checker 🎯 Schwer

**Aufgabe:**
Prüfe ob Punkt in Mandelbrot-Menge liegt und erstelle ASCII-Art.

**Erwarteter Output:**
```python
ist_in_mandelbrot(0+0j)      # True
ist_in_mandelbrot(-0.5+0.5j) # True
ist_in_mandelbrot(1+1j)      # False

mandelbrot_ascii(breite=40, höhe=20)
# Gibt ASCII-Darstellung der Mandelbrot-Menge aus
```

💡 **Hinweise:**
- Iteration: z = z² + c
- Divergiert wenn |z| > 2
- Nutze verschiedene Zeichen für Anzahl Iterationen

---

## Decimal Übungen

### Übung 14: Finanz-Rechner 🎯 Leicht

**Aufgabe:**
Berechne Mehrwertsteuer und Preise exakt mit Decimal.

**Erwarteter Output:**
```python
preis_brutto = berechne_mwst(Decimal('99.99'), Decimal('19'))
print(preis_brutto)  # 118.99

preis_netto = entferne_mwst(Decimal('119.00'), Decimal('19'))
print(preis_netto)   # 100.00
```

💡 **Hinweise:**
- IMMER aus String erstellen
- Nutze `quantize()` für Cent-Rundung
- ROUND_HALF_UP für kaufmännisches Runden

---

### Übung 15: Zinseszins-Vergleich 🎯 Mittel

**Aufgabe:**
Vergleiche Zinseszins-Berechnung mit Float und Decimal.

**Erwarteter Output:**
```python
vergleich = zinseszins_vergleich(10000, 5.5, 30)
print(vergleich)
# {
#     'float': 51584.83,
#     'decimal': 51584.84,
#     'differenz': 0.01,
#     'prozent_abweichung': 0.00019%
# }
```

💡 **Hinweise:**
- Beide Versionen implementieren
- K_n = K_0 * (1 + p/100)^n
- Vergleiche Endergebnisse

---

### Übung 16: Steuer-Berechnung 🎯 Schwer

**Aufgabe:**
Implementiere deutsches progressives Steuersystem.

**Erwarteter Output:**
```python
steuer = progressive_steuer(Decimal('50000'))
print(steuer)
# {
#     'einkommen': 50000.00,
#     'grundfreibetrag': 10908.00,
#     'zu_versteuerndes_einkommen': 39092.00,
#     'steuer': 8694.31,
#     'steuersatz_effektiv': 17.39%
# }
```

💡 **Hinweise:**
- Steuerstufen beachten
- Grundfreibetrag abziehen
- Formeln aus Einkommensteuergesetz

---

## Fraction Übungen

### Übung 17: Bruch-Rechner 🎯 Leicht

**Aufgabe:**
Einfacher Bruch-Taschenrechner der Strings parst.

**Erwarteter Output:**
```python
print(bruch_rechnen("1/2 + 1/3"))  # 5/6
print(bruch_rechnen("3/4 - 1/8"))  # 5/8
print(bruch_rechnen("2/3 * 3/4"))  # 1/2
print(bruch_rechnen("1/2 / 1/4"))  # 2
```

💡 **Hinweise:**
- Parse String mit `.split()`
- Erstelle Fraction-Objekte
- Führe Operation durch

---

### Übung 18: Ägyptische Brüche 🎯 Mittel

**Aufgabe:**
Zerlege Bruch in Summe von Stammbrüchen (1/n).

**Erwarteter Output:**
```python
print(ägyptische_brüche(Fraction(3, 4)))
# [1/2, 1/4]

print(ägyptische_brüche(Fraction(5, 6)))
# [1/2, 1/3]
```

💡 **Hinweise:**
- Greedy-Algorithmus: Immer größter Stammbruch
- Wiederhole bis Rest = 0
- 1/n wobei n = ceil(1/bruch)

---

### Übung 19: Kettenbruch 🎯 Schwer

**Aufgabe:**
Konvertiere Zahl in Kettenbruch-Darstellung und zurück.

**Erwarteter Output:**
```python
kb = zu_kettenbruch(math.pi, tiefe=8)
print(kb)  # [3, 7, 15, 1, 292, 1, 1, 1]

zahl = von_kettenbruch(kb)
print(zahl)  # ~3.141592653589793
```

💡 **Hinweise:**
- Ganzteil = floor(zahl)
- Rest = 1 / (zahl - ganzteil)
- Rekursiv bis gewünschte Tiefe

---

## Gemischte Übungen

### Übung 20: Zahlen-Typ-Konverter 🎯 Mittel

**Aufgabe:**
Universeller Konverter zwischen allen numerischen Typen.

**Erwarteter Output:**
```python
konverter = ZahlenKonverter()
ergebnis = konverter.konvertiere('3.14', zu='fraction')
# Fraction(157, 50)

ergebnis = konverter.konvertiere('0.333...', zu='fraction')
# Fraction(1, 3)
```

💡 **Hinweise:**
- Erkenne Eingabe-Typ automatisch
- Konvertiere zu gewünschtem Typ
- Handle Sonderfälle (z.B. periodische Dezimalzahlen)

---

### Übung 21: Zahlensystem-Explorer 🎯 Schwer

**Aufgabe:**
Interaktiver Explorer für verschiedene Zahlendarstellungen.

**Erwarteter Output:**
```python
explorer = ZahlenExplorer(42)
print(explorer.zeige_alle())
# Integer: 42
# Float: 42.0
# Decimal: 42
# Fraction: 42/1
# Complex: (42+0j)
# Binär: 0b101010
# Oktal: 0o52
# Hex: 0x2a
# Römisch: XLII
```

💡 **Hinweise:**
- Zentrale Klasse für eine Zahl
- Methoden für alle Darstellungen
- Pretty-Print Funktion

---

## Bonus-Herausforderungen 🔥

### Challenge 1: Pi-Berechnung

Berechne Pi auf 1000 Stellen genau mit Decimal.

💡 Nutze Machin's Formel oder Chudnovsky-Algorithmus

### Challenge 2: Perfekte Zahlen

Finde alle perfekten Zahlen bis 10000.
(Perfekt: Summe der Teiler = Zahl selbst, z.B. 6 = 1+2+3)

### Challenge 3: Goldener Schnitt

Berechne goldenen Schnitt mit Fraction auf verschiedene Arten:
- Als Grenzwert der Fibonacci-Verhältnisse
- Mit Kettenbruch [1, 1, 1, 1, ...]
- Direkt: (1 + √5) / 2

## Tipps für alle Übungen

### Debugging
```python
# Nutze Type Hints
def meine_funktion(x: int) -> str:
    pass

# Teste mit assert
assert ist_primzahl(17) == True
assert ist_primzahl(18) == False

# Nutze print für Debugging
print(f"DEBUG: x = {x}, type = {type(x)}")
```

### Testing
```python
# Erstelle Test-Funktionen
def test_primzahl():
    assert ist_primzahl(2) == True
    assert ist_primzahl(17) == True
    assert ist_primzahl(18) == False
    print("✅ Alle Tests bestanden!")

test_primzahl()
```

### Performance Messen
```python
import time

start = time.perf_counter()
ergebnis = meine_funktion()
dauer = time.perf_counter() - start
print(f"Dauer: {dauer:.4f}s")
```

## Weiterführende Ideen

Nach diesen Übungen kannst du versuchen:

1. **Eigene Zahlen-Bibliothek** erstellen
2. **Benchmark-Suite** für verschiedene Typen
3. **Wissenschaftlicher Taschenrechner** mit GUI
4. **Finanz-Dashboard** mit echten Berechnungen
5. **Mathematik-Visualisierung** mit Plots

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [Built-in Numeric Types](https://docs.python.org/3/library/stdtypes.html#numeric-types-int-float-complex)
- [decimal Module](https://docs.python.org/3/library/decimal.html)
- [fractions Module](https://docs.python.org/3/library/fractions.html)
- [math Module](https://docs.python.org/3/library/math.html)
- [cmath Module](https://docs.python.org/3/library/cmath.html)

## Verwandte Themen

- [[01_Integer (int)|Integer]] - Integer-Grundlagen
- [[02_Float|Float]] - Float-Grundlagen
- [[03_Complex|Complex]] - Komplexe Zahlen
- [[04_Decimal und Fraction|Decimal und Fraction]] - Exakte Arithmetik
- [[06_Praxis/04_Lösungen/Lösungen Teil 1|Lösungen]] - Musterlösungen zu allen Übungen

---

← [[04_Decimal und Fraction|Decimal und Fraction]] | [[INDEX|📑 Index]] | [[01_Grundlagen/03_Boolean_und_None/01_Boolean (bool)|Boolean (bool)]] →