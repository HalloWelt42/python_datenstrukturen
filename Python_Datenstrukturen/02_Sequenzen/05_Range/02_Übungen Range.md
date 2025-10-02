---
tags: [python, datenstrukturen, sequenzen, range, übungen]
created: 2025-10-01
---

← [[02_Sequenzen/05_Range/01_Range Objects|Range Objects]] | [[INDEX|📑 Index]] | [[03_Mappings_und_Sets/01_Dictionaries/01_Dict Basics|Dict Basics]] →

# Übungen: Range Objects

> [!tip] Lernziel
> Diese Übungen helfen dir, Range-Objekte sicher anzuwenden und ihre Vorteile in verschiedenen Szenarien zu nutzen.

## 📝 Übung 1: Gerade Zahlen (Leicht)

Erstelle eine Funktion `gerade_zahlen(start, ende)`, die alle geraden Zahlen zwischen `start` (inklusive) und `ende` (exklusive) als Liste zurückgibt.

**Anforderungen:**
- Verwende ein Range-Objekt mit Step
- Keine if-Bedingungen nötig
- Funktioniert auch mit negativen Zahlen

```python
def gerade_zahlen(start, ende):
    # Dein Code hier
    pass

# Test
print(gerade_zahlen(0, 10))
print(gerade_zahlen(1, 10))
print(gerade_zahlen(-10, 5))
```

**Erwarteter Output:**
```
[0, 2, 4, 6, 8]
[2, 4, 6, 8]
[-10, -8, -6, -4, -2, 0, 2, 4]
```

---

## 📝 Übung 2: Countdown-Generator (Leicht-Mittel)

Erstelle eine Funktion `countdown(von, bis=0)`, die einen Countdown von `von` bis `bis` (inklusive) erzeugt.

**Anforderungen:**
- Verwende einen negativen Step
- Standardwert für `bis` ist 0
- Gib die Zahlen als String zurück, getrennt durch " → "

```python
def countdown(von, bis=0):
    # Dein Code hier
    pass

# Test
print(countdown(10))
print(countdown(5, 1))
print(countdown(100, 90))
```

**Erwarteter Output:**
```
10 → 9 → 8 → 7 → 6 → 5 → 4 → 3 → 2 → 1 → 0
5 → 4 → 3 → 2 → 1
100 → 99 → 98 → 97 → 96 → 95 → 94 → 93 → 92 → 91 → 90
```

---

## 📝 Übung 3: Index-Finder (Mittel)

Schreibe eine Funktion `finde_index(zahl, start, ende, step=1)`, die prüft, ob eine Zahl in einem Range-Objekt enthalten ist und deren Index zurückgibt.

**Anforderungen:**
- Verwende `in` Operator für Membership-Test
- Berechne den Index ohne das Range in eine Liste zu konvertieren
- Gib -1 zurück, wenn die Zahl nicht im Range ist

```python
def finde_index(zahl, start, ende, step=1):
    # Dein Code hier
    pass

# Test
print(finde_index(5, 0, 10))        # 5 ist an Index 5
print(finde_index(8, 0, 20, 2))     # 8 ist an Index 4 (0,2,4,6,8)
print(finde_index(7, 0, 20, 2))     # 7 ist nicht in geraden Zahlen
print(finde_index(15, 20, 10, -1))  # 15 ist an Index 5 (20,19,18,17,16,15)
```

**Erwarteter Output:**
```
5
4
-1
5
```

**💡 Hinweis:** Nutze mathematische Berechnung statt list-Konvertierung!

---

## 📝 Übung 4: Schritte-Rechner (Mittel)

Erstelle eine Funktion `berechne_step(start, ende, anzahl_elemente)`, die den Step-Wert berechnet, um genau `anzahl_elemente` Zahlen zwischen `start` und `ende` zu erhalten.

**Anforderungen:**
- Berechne den korrekten Step-Wert
- Runde sinnvoll (keine Dezimalsteps in Range möglich)
- Prüfe auf gültige Eingaben

```python
def berechne_step(start, ende, anzahl_elemente):
    # Dein Code hier
    pass

# Test
step = berechne_step(0, 100, 11)
print(f"Step: {step}")
print(list(range(0, 101, step)))

step = berechne_step(10, 50, 5)
print(f"Step: {step}")
print(list(range(10, 51, step)))
```

**Erwarteter Output:**
```
Step: 10
[0, 10, 20, 30, 40, 50, 60, 70, 80, 90, 100]
Step: 10
[10, 20, 30, 40, 50]
```

---

## 📝 Übung 5: Memory-Effizienz-Vergleich (Mittel-Schwer)

Schreibe eine Funktion `vergleiche_memory(n)`, die den Speicherverbrauch von Range-Objekten mit äquivalenten Listen vergleicht.

**Anforderungen:**
- Vergleiche `range(n)` mit `list(range(n))`
- Nutze `sys.getsizeof()` für Größenmessung
- Berechne den Faktor der Speicherersparnis
- Formatiere die Ausgabe lesbar

```python
import sys

def vergleiche_memory(n):
    # Dein Code hier
    pass

# Test
vergleiche_memory(100)
vergleiche_memory(1_000)
vergleiche_memory(1_000_000)
```

**Erwarteter Output (ungefähr):**
```
Vergleich für n=100:
  Range:  48 Bytes
  List:   920 Bytes
  Ersparnis: 19.2x

Vergleich für n=1000:
  Range:  48 Bytes
  List:   9016 Bytes
  Ersparnis: 187.8x

Vergleich für n=1000000:
  Range:  48 Bytes
  List:   8000056 Bytes
  Ersparnis: 166667.8x
```

---

## 📝 Übung 6: Spezial-Sequenzen (Schwer)

Erstelle eine Funktion `erstelle_sequenz(typ, start, ende)`, die verschiedene spezielle Zahlenfolgen generiert.

**Typen:**
- `"ungerade"`: Alle ungeraden Zahlen
- `"vielfache"`: Alle Vielfachen von 5
- `"fibonacci_indices"`: Fibonacci-Indizes (0, 1, 1, 2, 3, 5, 8, ...)
- `"primzahl_indizes"`: Nur Indizes wo Primzahlen stehen würden

```python
def erstelle_sequenz(typ, start, ende):
    # Dein Code hier
    pass

# Test
print(erstelle_sequenz("ungerade", 1, 20))
print(erstelle_sequenz("vielfache", 0, 50))
print(erstelle_sequenz("fibonacci_indices", 0, 100))
```

**Erwarteter Output:**
```
[1, 3, 5, 7, 9, 11, 13, 15, 17, 19]
[0, 5, 10, 15, 20, 25, 30, 35, 40, 45]
[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89]
```

---

## 📝 Übung 7: Range-Merger (Schwer)

Schreibe eine Funktion `merge_ranges(*ranges)`, die mehrere Range-Objekte zu einer sortierten Liste eindeutiger Werte kombiniert.

**Anforderungen:**
- Akzeptiere beliebig viele Range-Objekte
- Entferne Duplikate
- Sortiere das Ergebnis
- Verwende Set-Operationen für Effizienz

```python
def merge_ranges(*ranges):
    # Dein Code hier
    pass

# Test
r1 = range(0, 10, 2)      # 0, 2, 4, 6, 8
r2 = range(5, 15)         # 5, 6, 7, 8, 9, 10, 11, 12, 13, 14
r3 = range(10, 0, -2)     # 10, 8, 6, 4, 2

print(merge_ranges(r1, r2))
print(merge_ranges(r1, r2, r3))
```

**Erwarteter Output:**
```
[0, 2, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14]
[0, 2, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14]
```

---

## 🎯 Bonus-Übung: Range-Slicer (Experte)

Erstelle eine Klasse `SmartRange`, die ein Range-Objekt erweitert und zusätzliche Methoden bietet.

**Anforderungen:**
- Methode `slice(start, stop)`: Gibt Teilbereich zurück
- Methode `reverse()`: Kehrt die Reihenfolge um
- Methode `intersect(other)`: Schnittmenge mit anderem Range
- Methode `statistics()`: Min, Max, Anzahl, Summe

```python
class SmartRange:
    def __init__(self, *args):
        # Dein Code hier
        pass
    
    def slice(self, start, stop):
        pass
    
    def reverse(self):
        pass
    
    def intersect(self, other):
        pass
    
    def statistics(self):
        pass

# Test
sr = SmartRange(0, 100, 5)
print(sr.slice(10, 50))
print(sr.reverse())
print(sr.statistics())

sr2 = SmartRange(40, 80, 5)
print(sr.intersect(sr2))
```

**Erwarteter Output:**
```
[10, 15, 20, 25, 30, 35, 40, 45]
[95, 90, 85, 80, 75, 70, 65, 60, 55, 50, 45, 40, 35, 30, 25, 20, 15, 10, 5, 0]
{'min': 0, 'max': 95, 'count': 20, 'sum': 950}
[40, 45, 50, 55, 60, 65, 70, 75]
```

---

## 💡 Lösungsstrategien

### Für Übung 1-3 (Grundlagen):
- Nutze die Step-Parameter von Range kreativ
- Teste Edge Cases (negative Zahlen, leere Ranges)
- Vermeide unnötige Listen-Konvertierungen

### Für Übung 4-5 (Fortgeschritten):
- Mathematische Berechnung statt Trial-and-Error
- Denke an Speichereffizienz
- Nutze eingebaute Module (`sys`, `math`)

### Für Übung 6-7 (Profi):
- Kombiniere Range mit anderen Datenstrukturen
- Set-Operationen für Duplikate-Entfernung
- Generator-Expressions für große Datenmengen

### Für Bonus-Übung (Experte):
- Objektorientierte Prinzipien anwenden
- Range-Eigenschaften kapseln
- Fehlerbehandlung nicht vergessen

---

## 🔍 Selbst-Check

Nach diesen Übungen solltest du:

✅ Range-Objekte mit verschiedenen Step-Werten erstellen können
✅ Die Speichereffizienz von Range verstehen
✅ Range in Kombination mit anderen Sequenzen nutzen können
✅ Mathematische Berechnungen mit Range-Grenzen durchführen
✅ Range-Objekte in eigenen Funktionen/Klassen einsetzen können

---

## 📚 Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [Range Objects](https://docs.python.org/3/library/stdtypes.html#ranges) - Vollständige Referenz mit allen Details
- [Built-in Functions: range()](https://docs.python.org/3/library/functions.html#func-range) - Funktionsdokumentation
- [sys.getsizeof()](https://docs.python.org/3/library/sys.html#sys.getsizeof) - Für Memory-Messungen

---

## 🎓 Verwandte Themen

- [[02_Sequenzen/05_Range/01_Range Objects|Range Objects]] - Theorie nochmal wiederholen
- [[02_Sequenzen/04_Comprehensions/01_List Comprehensions|List Comprehensions]] - Alternative Sequenzerzeugung
- [[03_Mappings_und_Sets/01_Dictionaries/01_Dict Basics|Dict Basics]] - Weiter zum nächsten großen Thema
- [[06_Praxis/04_Lösungen/Lösungen Teil 2|Lösungen Teil 2]] - Musterlösungen zu diesen Übungen

---

← [[02_Sequenzen/05_Range/01_Range Objects|Range Objects]] | [[INDEX|📑 Index]] | [[03_Mappings_und_Sets/01_Dictionaries/01_Dict Basics|Dict Basics]] →