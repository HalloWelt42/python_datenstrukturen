---
tags: [python, datenstrukturen, collections, counter]
created: 2025-10-01
---

← [[03_Mappings_und_Sets/02_Sets/06_Übungen Sets|Übungen Sets]] | [[INDEX|📑 Index]] | [[03_Mappings_und_Sets/03_Collections_Module/02_DefaultDict|DefaultDict]] →

# Counter

> [!tip] Lernziel
> Nach dieser Seite kannst du Counter effektiv zum Zählen, Analysieren von Häufigkeiten und für statistische Auswertungen einsetzen.

## Was ist Counter?

**Counter** ist eine `dict`-Subklasse, die speziell zum Zählen von hashbaren Objekten entwickelt wurde. Es ist Teil des `collections` Moduls.

**Kernmerkmale:**
- **Spezialisiertes Dictionary**: Keys = Elemente, Values = Anzahl
- **Automatische Initialisierung**: Fehlende Keys haben Count 0
- **Arithmetische Operationen**: Addition, Subtraktion, etc.
- **Nützliche Methoden**: most_common(), elements(), etc.
- **Negative Counts erlaubt**: Für Differenzen

**Wofür verwendet:**
- Elemente zählen (Wörter, Buchstaben, etc.)
- Häufigkeitsanalysen
- Histogramme erstellen
- Multisets implementieren
- Statistische Auswertungen

---

## Import und Erstellung

### Counter erstellen

```python
from collections import Counter

# Leerer Counter
c = Counter()
print(c)  # Counter()

# Aus Iterable
woerter = ['apfel', 'banane', 'apfel', 'kirsche', 'banane', 'apfel']
c = Counter(woerter)
print(c)
# Counter({'apfel': 3, 'banane': 2, 'kirsche': 1})

# Aus String (zählt Zeichen)
text = "hello world"
c = Counter(text)
print(c)
# Counter({'l': 3, 'o': 2, 'h': 1, 'e': 1, ' ': 1, 'w': 1, 'r': 1, 'd': 1})

# Aus Dict
c = Counter({'apfel': 3, 'banane': 2})
print(c)  # Counter({'apfel': 3, 'banane': 2})

# Mit Keyword Arguments
c = Counter(apfel=3, banane=2, kirsche=1)
print(c)  # Counter({'apfel': 3, 'banane': 2, 'kirsche': 1})

# Aus anderen Mappings
d = {'a': 5, 'b': 3}
c = Counter(d)
print(c)  # Counter({'a': 5, 'b': 3})
```

---

## Grundlegende Operationen

### Zugriff auf Counts

```python
from collections import Counter

c = Counter(['a', 'b', 'c', 'a', 'b', 'a'])
print(c)  # Counter({'a': 3, 'b': 2, 'c': 1})

# Wie bei Dict - aber fehlende Keys geben 0 zurück!
print(c['a'])  # 3
print(c['z'])  # 0 (kein KeyError!)

# Mit get() (wie bei dict)
print(c.get('a'))  # 3
print(c.get('z'))  # None
print(c.get('z', 0))  # 0
```

### Counts setzen und ändern

```python
from collections import Counter

c = Counter(['a', 'b', 'c'])

# Count erhöhen
c['a'] += 1
print(c)  # Counter({'a': 2, 'b': 1, 'c': 1})

# Neues Element hinzufügen
c['d'] = 5
print(c)  # Counter({'d': 5, 'a': 2, 'b': 1, 'c': 1})

# Count verringern
c['d'] -= 2
print(c)  # Counter({'d': 3, 'a': 2, 'b': 1, 'c': 1})

# Auf 0 setzen (Element bleibt im Counter!)
c['b'] = 0
print(c)  # Counter({'d': 3, 'a': 2, 'c': 1, 'b': 0})

# Negative Counts sind erlaubt
c['e'] = -5
print(c)  # Counter({'d': 3, 'a': 2, 'c': 1, 'b': 0, 'e': -5})
```

### Elemente hinzufügen mit update()

```python
from collections import Counter

c = Counter(['a', 'b', 'c'])
print(c)  # Counter({'a': 1, 'b': 1, 'c': 1})

# Mit Iterable
c.update(['a', 'b', 'd'])
print(c)  # Counter({'a': 2, 'b': 2, 'c': 1, 'd': 1})

# Mit anderem Counter
c2 = Counter(['a', 'a', 'e'])
c.update(c2)
print(c)  # Counter({'a': 4, 'b': 2, 'c': 1, 'd': 1, 'e': 1})

# Mit Dict
c.update({'f': 3})
print(c)  # Counter({'a': 4, 'f': 3, 'b': 2, 'c': 1, 'd': 1, 'e': 1})

# Mit Keyword Args
c.update(g=2, h=1)
print(c)
```

### Elemente entfernen mit subtract()

```python
from collections import Counter

c = Counter(['a', 'a', 'a', 'b', 'b', 'c'])
print(c)  # Counter({'a': 3, 'b': 2, 'c': 1})

# subtract() - subtrahiert Counts
c.subtract(['a', 'b', 'b'])
print(c)  # Counter({'a': 2, 'c': 1, 'b': 0})

# Kann negative Counts erzeugen
c.subtract(['a', 'a', 'a'])
print(c)  # Counter({'c': 1, 'b': 0, 'a': -1})

# Mit anderem Counter
c2 = Counter({'a': 2, 'c': 1})
c = Counter({'a': 5, 'b': 3, 'c': 2})
c.subtract(c2)
print(c)  # Counter({'a': 3, 'b': 3, 'c': 1})
```

---

## Wichtige Counter-Methoden

### most_common() - Die häufigsten Elemente

```python
from collections import Counter

# Wörter zählen
text = "die katze sitzt auf der matte die katze mag die matte"
woerter = text.split()
c = Counter(woerter)

# Alle Elemente nach Häufigkeit sortiert
print(c.most_common())
# [('die', 3), ('katze', 2), ('matte', 2), ('sitzt', 1), 
#  ('auf', 1), ('der', 1), ('mag', 1)]

# Top N Elemente
print(c.most_common(3))
# [('die', 3), ('katze', 2), ('matte', 2)]

# Ohne Argument: Alle sortiert
top_alle = c.most_common()
print(top_alle)

# Bottom N (mit Slice von hinten)
bottom_3 = c.most_common()[:-4:-1]  # Letzte 3, umgekehrt
print(bottom_3)
```

### elements() - Alle Elemente expandieren

```python
from collections import Counter

c = Counter({'a': 3, 'b': 2, 'c': 1})

# elements() gibt Iterator zurück
# Jedes Element so oft wie Count
elemente = list(c.elements())
print(sorted(elemente))  # ['a', 'a', 'a', 'b', 'b', 'c']

# Praktisch: Original-Liste rekonstruieren
c = Counter(['x', 'y', 'x', 'z', 'y', 'x'])
original = list(c.elements())
print(sorted(original))  # ['x', 'x', 'x', 'y', 'y', 'z']

# ⚠️ Negative und 0 Counts werden ignoriert!
c = Counter({'a': 2, 'b': 0, 'c': -1})
print(list(c.elements()))  # ['a', 'a'] - nur positive!
```

### total() - Summe aller Counts (Python 3.10+)

```python
from collections import Counter

c = Counter(['a', 'b', 'c', 'a', 'b', 'a'])

# Gesamtanzahl aller Elemente
total = c.total()  # Python 3.10+
print(total)  # 6

# Vor Python 3.10: sum(c.values())
total = sum(c.values())
print(total)  # 6

# Mit negativen Counts
c = Counter({'a': 5, 'b': -2, 'c': 3})
print(sum(c.values()))  # 6 (5 - 2 + 3)
```

---

## Arithmetische Operationen

### Addition und Subtraktion

```python
from collections import Counter

c1 = Counter(['a', 'a', 'b', 'c'])
c2 = Counter(['a', 'b', 'b', 'd'])

# Addition (+): Addiert Counts
summe = c1 + c2
print(summe)
# Counter({'a': 3, 'b': 3, 'c': 1, 'd': 1})

# Subtraktion (-): Subtrahiert, behält nur positive
differenz = c1 - c2
print(differenz)
# Counter({'a': 1, 'c': 1}) - 'b' und 'd' weg (würden 0 oder negativ)

# Umgekehrt
differenz2 = c2 - c1
print(differenz2)
# Counter({'b': 1, 'd': 1})

# ⚠️ Wichtig: Nur positive Counts bleiben!
c1 = Counter({'a': 5, 'b': 3})
c2 = Counter({'a': 2, 'b': 4})
print(c1 - c2)  # Counter({'a': 3}) - 'b' würde -1, wird entfernt
```

### Union und Intersection

```python
from collections import Counter

c1 = Counter(['a', 'a', 'b', 'c'])
c2 = Counter(['a', 'b', 'b', 'd'])

# Union (|): Maximum jedes Counts
union = c1 | c2
print(union)
# Counter({'a': 2, 'b': 2, 'c': 1, 'd': 1})
# 'a': max(2, 1) = 2, 'b': max(1, 2) = 2

# Intersection (&): Minimum jedes Counts
intersection = c1 & c2
print(intersection)
# Counter({'a': 1, 'b': 1})
# 'a': min(2, 1) = 1, 'b': min(1, 2) = 1
# 'c' und 'd' nicht in beiden

# Praktisch für Multiset-Operationen
einkauf1 = Counter(['apfel', 'apfel', 'banane'])
einkauf2 = Counter(['apfel', 'banane', 'banane'])

# Was wurde mindestens in beiden gekauft?
gemeinsam = einkauf1 & einkauf2
print(gemeinsam)  # Counter({'apfel': 1, 'banane': 1})
```

### Unary Plus und Minus

```python
from collections import Counter

c = Counter({'a': 5, 'b': -3, 'c': 0, 'd': 2})

# Unary + : Behält nur positive Counts
positiv = +c
print(positiv)  # Counter({'a': 5, 'd': 2})

# Unary - : Negiert Counts und behält nur positive
negativ = -c
print(negativ)  # Counter({'b': 3})

# Original unverändert
print(c)  # Counter({'a': 5, 'd': 2, 'c': 0, 'b': -3})
```

---

## Praktische Anwendungen

### 1. Wort-Häufigkeit in Texten

```python
from collections import Counter
import re

text = """
Python ist eine tolle Programmiersprache. Python macht Spaß
und Python ist einfach zu lernen. Mit Python kann man viel machen.
"""

# Text bereinigen und Wörter extrahieren
woerter = re.findall(r'\b\w+\b', text.lower())
wort_counts = Counter(woerter)

# Top 5 häufigste Wörter
print("Top 5 Wörter:")
for wort, count in wort_counts.most_common(5):
    print(f"  {wort}: {count}x")

# Ausgabe:
# Top 5 Wörter:
#   python: 4x
#   ist: 2x
#   eine: 1x
#   tolle: 1x
#   programmiersprache: 1x
```

### 2. Buchstaben-Analyse

```python
from collections import Counter

text = "Hello World"

# Alle Buchstaben zählen (case-insensitive)
buchstaben = Counter(text.lower())

# Leerzeichen entfernen
del buchstaben[' ']

print("Buchstabenhäufigkeit:")
for buchstabe, count in sorted(buchstaben.items()):
    print(f"  {buchstabe}: {count}")

# Häufigster Buchstabe
haeufigster = buchstaben.most_common(1)[0]
print(f"\nHäufigster: '{haeufigster[0]}' ({haeufigster[1]}x)")
```

### 3. Umfragen auswerten

```python
from collections import Counter

# Umfrage-Ergebnisse
antworten = [
    "ja", "nein", "ja", "vielleicht", "ja", 
    "nein", "ja", "ja", "vielleicht", "ja"
]

ergebnis = Counter(antworten)

print("Umfrage-Ergebnis:")
total = sum(ergebnis.values())
for antwort, anzahl in ergebnis.most_common():
    prozent = (anzahl / total) * 100
    print(f"  {antwort}: {anzahl} ({prozent:.1f}%)")

# Ausgabe:
# Umfrage-Ergebnis:
#   ja: 6 (60.0%)
#   nein: 2 (20.0%)
#   vielleicht: 2 (20.0%)
```

### 4. Anagramm-Detektion

```python
from collections import Counter

def sind_anagramme(wort1, wort2):
    """Prüft ob zwei Wörter Anagramme sind"""
    return Counter(wort1.lower()) == Counter(wort2.lower())

# Tests
print(sind_anagramme("listen", "silent"))  # True
print(sind_anagramme("hello", "world"))    # False
print(sind_anagramme("Astronomer", "Moon starer"))  # True (mit Leerzeichen)

# Anagramm-Gruppen finden
woerter = ["eat", "tea", "tan", "ate", "nat", "bat"]

anagramm_gruppen = {}
for wort in woerter:
    # Sortierte Buchstaben als Key
    key = tuple(sorted(wort))
    anagramm_gruppen.setdefault(key, []).append(wort)

print("\nAnagramm-Gruppen:")
for gruppe in anagramm_gruppen.values():
    if len(gruppe) > 1:
        print(f"  {gruppe}")
```

### 5. Inventar-Verwaltung

```python
from collections import Counter

class Lager:
    def __init__(self):
        self.bestand = Counter()
    
    def einlagern(self, artikel, menge):
        self.bestand[artikel] += menge
    
    def auslagern(self, artikel, menge):
        if self.bestand[artikel] >= menge:
            self.bestand[artikel] -= menge
            return True
        return False
    
    def bestand_pruefen(self, artikel):
        return self.bestand[artikel]
    
    def inventur(self):
        print("\n=== Inventur ===")
        for artikel, menge in self.bestand.most_common():
            if menge > 0:
                print(f"  {artikel}: {menge} Stück")

# Verwendung
lager = Lager()
lager.einlagern("Schrauben", 100)
lager.einlagern("Nägel", 50)
lager.einlagern("Schrauben", 25)

print(f"Schrauben: {lager.bestand_pruefen('Schrauben')}")  # 125

lager.auslagern("Schrauben", 30)
print(f"Schrauben nach Entnahme: {lager.bestand_pruefen('Schrauben')}")  # 95

lager.inventur()
```

### 6. Log-Analyse

```python
from collections import Counter
from datetime import datetime

# Simulierte Log-Einträge
logs = [
    "2025-01-01 10:00:00 ERROR Database connection failed",
    "2025-01-01 10:05:00 INFO User login successful",
    "2025-01-01 10:10:00 ERROR Database connection failed",
    "2025-01-01 10:15:00 WARNING Low memory",
    "2025-01-01 10:20:00 ERROR Database connection failed",
    "2025-01-01 10:25:00 INFO User logout",
]

# Log-Level zählen
log_levels = Counter()
for log in logs:
    parts = log.split()
    if len(parts) >= 3:
        level = parts[2]
        log_levels[level] += 1

print("Log-Level Verteilung:")
for level, count in log_levels.most_common():
    print(f"  {level}: {count}")

# Häufigste Fehler finden
error_messages = []
for log in logs:
    if "ERROR" in log:
        # Nachricht nach Level extrahieren
        message = ' '.join(log.split()[3:])
        error_messages.append(message)

fehler_counts = Counter(error_messages)
print("\nHäufigste Fehler:")
for fehler, count in fehler_counts.most_common(3):
    print(f"  {count}x: {fehler}")
```

---

## Counter vs. Dictionary

### Unterschiede

```python
from collections import Counter

# Standard Dict
d = {}
# Fehlender Key → KeyError
try:
    print(d['missing'])
except KeyError:
    print("Dict: KeyError bei fehlendem Key")

# Counter
c = Counter()
# Fehlender Key → 0
print(c['missing'])  # 0 (kein Error!)

# Update-Verhalten
d = {'a': 1}
d.update({'a': 2})  # Überschreibt
print(d)  # {'a': 2}

c = Counter({'a': 1})
c.update({'a': 2})  # Addiert!
print(c)  # Counter({'a': 3})

# Arithmetische Operationen
try:
    result = {'a': 1} + {'b': 2}
except TypeError:
    print("Dict: Keine Arithmetik")

c1 = Counter({'a': 1})
c2 = Counter({'b': 2})
print(c1 + c2)  # Counter({'a': 1, 'b': 2}) - Funktioniert!
```

### Wann welches verwenden?

```python
# ✅ Counter: Für Zählungen
buchstaben_count = Counter("hello world")

# ✅ Dict: Für allgemeine Key-Value Mappings
user_data = {"name": "Max", "age": 30}

# ✅ Counter: Für Häufigkeitsanalysen
wort_freq = Counter(text.split())

# ✅ Dict: Wenn keine automatische 0-Initialisierung gewünscht
config = {"debug": True}  # Fehlende Keys sollen Error werfen
```

---

## Performance

### Counter Performance

```python
import timeit
from collections import Counter

data = ['a'] * 1000 + ['b'] * 500 + ['c'] * 250

# Mit Counter
def mit_counter():
    return Counter(data)

# Mit Dict und get()
def mit_dict():
    d = {}
    for item in data:
        d[item] = d.get(item, 0) + 1
    return d

# Mit defaultdict
from collections import defaultdict
def mit_defaultdict():
    d = defaultdict(int)
    for item in data:
        d[item] += 1
    return d

# Benchmark
counter_time = timeit.timeit(mit_counter, number=10000)
dict_time = timeit.timeit(mit_dict, number=10000)
defaultdict_time = timeit.timeit(mit_defaultdict, number=10000)

print(f"Counter:     {counter_time:.3f}s")
print(f"Dict:        {dict_time:.3f}s")
print(f"defaultdict: {defaultdict_time:.3f}s")

# Counter ist optimiert für Zählungen!
```

---

## Häufige Fallstricke

### ⚠️ Fallstrick 1: update() addiert, überschreibt nicht

```python
from collections import Counter

c = Counter({'a': 5, 'b': 3})

# ❌ Erwartung: Überschreiben wie bei dict
c.update({'a': 2})  # Erwartet: {'a': 2, 'b': 3}
print(c)  # Counter({'a': 7, 'b': 3}) - Addiert!

# ✅ Zum Überschreiben: Direkte Zuweisung
c['a'] = 2
print(c)  # Counter({'a': 2, 'b': 3})
```

### ⚠️ Fallstrick 2: Negative Counts bei Subtraktion

```python
from collections import Counter

c1 = Counter({'a': 2, 'b': 1})
c2 = Counter({'a': 3, 'b': 1})

# - Operator: Entfernt negative Counts
print(c1 - c2)  # Counter() - leer, 'a' würde -1

# subtract(): Behält negative Counts
c1_copy = c1.copy()
c1_copy.subtract(c2)
print(c1_copy)  # Counter({'b': 0, 'a': -1})
```

### ⚠️ Fallstrick 3: elements() ignoriert 0 und negative

```python
from collections import Counter

c = Counter({'a': 2, 'b': 0, 'c': -1})

# elements() gibt nur positive Counts
elemente = list(c.elements())
print(elemente)  # ['a', 'a'] - 'b' und 'c' fehlen!

# ✅ Alle einschließen:
alle = []
for item, count in c.items():
    alle.extend([item] * max(0, count))
```

---

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [collections.Counter](https://docs.python.org/3/library/collections.html#collections.Counter) - Vollständige Counter Referenz
- [Counter Recipes](https://docs.python.org/3/library/collections.html#counter-objects) - Praktische Beispiele
- [collections module](https://docs.python.org/3/library/collections.html) - Alle Collections

---

## Verwandte Themen

- [[03_Mappings_und_Sets/03_Collections_Module/02_DefaultDict|DefaultDict]] - Ähnliche Spezial-Dict
- [[03_Mappings_und_Sets/01_Dictionaries/01_Dict Basics|Dict Basics]] - Counter basiert auf dict
- [[03_Mappings_und_Sets/02_Sets/01_Set Basics|Set Basics]] - Für Multisets
- [[03_Mappings_und_Sets/01_Dictionaries/02_Dict Methoden|Dict Methoden]] - Grundlegende Methoden

---

← [[03_Mappings_und_Sets/02_Sets/06_Übungen Sets|Übungen Sets]] | [[INDEX|📑 Index]] | [[03_Mappings_und_Sets/03_Collections_Module/02_DefaultDict|DefaultDict]] →