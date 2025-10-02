---
tags: [python, datenstrukturen, collections, übungen]
created: 2025-10-01
---

← [[03_Mappings_und_Sets/03_Collections_Module/03_ChainMap|ChainMap]] | [[INDEX|📑 Index]] | [[04_Fortgeschritten/01_Arrays/01_Array Module|Array Module]] →

# Übungen: Collections Module

> [!tip] Lernziel
> Diese Übungen helfen dir, Counter, defaultdict und ChainMap effektiv für komplexe Datenverarbeitungsaufgaben einzusetzen.

## 📝 Übung 1: Top N Wörter (Leicht)

Erstelle eine Funktion `top_woerter(text, n)`, die die N häufigsten Wörter in einem Text findet.

**Anforderungen:**
- Verwende Counter
- Case-insensitive
- Entferne Satzzeichen
- Gibt Liste von (wort, count) Tupeln zurück

```python
from collections import Counter

def top_woerter(text, n):
    # Dein Code hier
    pass

# Test
text = """
Python ist toll. Python macht Spaß. 
Ich liebe Python und Python ist einfach!
"""

top_3 = top_woerter(text, 3)
for wort, count in top_3:
    print(f"{wort}: {count}x")
```

**Erwarteter Output:**
```
python: 4x
ist: 2x
toll: 1x
```

---

## 📝 Übung 2: Inventar-Differenz (Leicht-Mittel)

Schreibe eine Funktion `inventar_diff(soll, ist)`, die Differenzen zwischen Soll- und Ist-Bestand berechnet.

**Anforderungen:**
- Beide sind Counter
- Gibt drei Counter zurück: fehlend, überschuss, korrekt
- Nutze Counter-Arithmetik

```python
from collections import Counter

def inventar_diff(soll, ist):
    # Dein Code hier
    # Rückgabe: (fehlend, überschuss, korrekt)
    pass

# Test
soll = Counter({'Schrauben': 100, 'Nägel': 50, 'Muttern': 75})
ist = Counter({'Schrauben': 95, 'Nägel': 60, 'Muttern': 75, 'Bolzen': 10})

fehlend, überschuss, korrekt = inventar_diff(soll, ist)
print(f"Fehlend: {dict(fehlend)}")
print(f"Überschuss: {dict(überschuss)}")
print(f"Korrekt: {dict(korrekt)}")
```

**Erwarteter Output:**
```
Fehlend: {'Schrauben': 5}
Überschuss: {'Nägel': 10, 'Bolzen': 10}
Korrekt: {'Muttern': 75}
```

---

## 📝 Übung 3: Gruppiere nach Länge (Mittel)

Erstelle eine Funktion `gruppiere_nach_laenge(woerter)`, die Wörter nach ihrer Länge gruppiert.

**Anforderungen:**
- Verwende defaultdict
- Keys sind Längen, Values sind Listen von Wörtern
- Sortiere die Wörter pro Gruppe

```python
from collections import defaultdict

def gruppiere_nach_laenge(woerter):
    # Dein Code hier
    pass

# Test
woerter = ["hi", "hallo", "python", "code", "programmierung", "ja", "nein"]

gruppen = gruppiere_nach_laenge(woerter)
for laenge in sorted(gruppen.keys()):
    print(f"{laenge}: {gruppen[laenge]}")
```

**Erwarteter Output:**
```
2: ['hi', 'ja']
4: ['code', 'nein']
5: ['hallo']
6: ['python']
15: ['programmierung']
```

---

## 📝 Übung 4: Config-System (Mittel)

Implementiere ein Konfigurations-System mit ChainMap.

**Anforderungen:**
- Klasse `ConfigManager` mit ChainMap
- Methoden: set_default, set_user, set_temp, get, reset_temp
- Priorität: temp > user > default

```python
from collections import ChainMap

class ConfigManager:
    def __init__(self):
        # Dein Code hier
        pass
    
    def set_default(self, key, value):
        # Setzt Default-Wert
        pass
    
    def set_user(self, key, value):
        # Setzt User-Präferenz
        pass
    
    def set_temp(self, key, value):
        # Setzt temporären Override
        pass
    
    def get(self, key, default=None):
        # Holt Wert mit Fallback
        pass
    
    def reset_temp(self):
        # Löscht alle temporären Overrides
        pass

# Test
cm = ConfigManager()
cm.set_default('theme', 'light')
cm.set_default('font_size', 12)

cm.set_user('theme', 'dark')

print(cm.get('theme'))      # dark (user override)
print(cm.get('font_size'))  # 12 (default)

cm.set_temp('theme', 'contrast')
print(cm.get('theme'))      # contrast (temp override)

cm.reset_temp()
print(cm.get('theme'))      # dark (zurück zu user)
```

**Erwarteter Output:**
```
dark
12
contrast
dark
```

---

## 📝 Übung 5: Anagramm-Gruppen (Mittel)

Finde alle Anagramm-Gruppen in einer Liste von Wörtern.

**Anforderungen:**
- Verwende defaultdict mit tuple als Key
- Key ist sortierte Buchstaben-Reihenfolge
- Gibt Liste von Anagramm-Gruppen zurück

```python
from collections import defaultdict

def finde_anagramm_gruppen(woerter):
    # Dein Code hier
    pass

# Test
woerter = [
    "listen", "silent", "hello", "world",
    "enlist", "banana", "anaban"
]

gruppen = finde_anagramm_gruppen(woerter)
for gruppe in gruppen:
    if len(gruppe) > 1:
        print(sorted(gruppe))
```

**Erwarteter Output:**
```
['enlist', 'listen', 'silent']
['anaban', 'banana']
```

---

## 📝 Übung 6: Most Common Elements Across Lists (Mittel-Schwer)

Finde die N häufigsten Elemente, die in mehreren Listen vorkommen.

**Anforderungen:**
- Verwende Counter
- Kombiniere mehrere Listen
- Element muss in mindestens min_lists Listen vorkommen

```python
from collections import Counter

def most_common_across(lists, n=5, min_lists=2):
    # Dein Code hier
    # Rückgabe: Liste von (element, count) für Top N
    pass

# Test
liste1 = ['a', 'b', 'c', 'a', 'b']
liste2 = ['b', 'c', 'd', 'b']
liste3 = ['a', 'c', 'e', 'c']

# Top 3 Elemente die in mind. 2 Listen vorkommen
top = most_common_across([liste1, liste2, liste3], n=3, min_lists=2)
for element, count in top:
    print(f"{element}: {count}x")
```

**Erwarteter Output:**
```
b: 4x
c: 4x
a: 3x
```

---

## 📝 Übung 7: Nested Counter (Schwer)

Erstelle einen verschachtelten Counter für hierarchische Zählungen.

**Anforderungen:**
- Funktion `nested_counter(daten, *schluessel)`
- Zählt nach mehreren Kategorien hierarchisch
- Nutzt defaultdict mit Counter

```python
from collections import defaultdict, Counter

def nested_counter(daten, *schluessel):
    # Dein Code hier
    # Erstellt verschachtelte Struktur basierend auf Schlüsseln
    pass

# Test
verkauf = [
    {'kategorie': 'Elektronik', 'produkt': 'Laptop', 'menge': 5},
    {'kategorie': 'Elektronik', 'produkt': 'Maus', 'menge': 10},
    {'kategorie': 'Bücher', 'produkt': 'Python Buch', 'menge': 3},
    {'kategorie': 'Elektronik', 'produkt': 'Laptop', 'menge': 2},
    {'kategorie': 'Bücher', 'produkt': 'Python Buch', 'menge': 5},
]

# Nach Kategorie > Produkt zählen
result = nested_counter(verkauf, 'kategorie', 'produkt')

print("Verkauf nach Kategorie:")
for kategorie, produkte in result.items():
    print(f"\n{kategorie}:")
    for produkt, menge in produkte.items():
        print(f"  {produkt}: {menge}")
```

**Erwarteter Output:**
```
Verkauf nach Kategorie:

Elektronik:
  Laptop: 7
  Maus: 10

Bücher:
  Python Buch: 8
```

---

## 📝 Übung 8: Scope Manager (Schwer)

Implementiere einen Scope Manager für Variablen mit ChainMap.

**Anforderungen:**
- Klasse `ScopeManager`
- Methoden: enter_scope, exit_scope, set_var, get_var, del_var
- Unterstützt verschachtelte Scopes
- Variable Shadowing

```python
from collections import ChainMap

class ScopeManager:
    def __init__(self):
        # Dein Code hier
        pass
    
    def enter_scope(self):
        # Neuen Scope betreten
        pass
    
    def exit_scope(self):
        # Aktuellen Scope verlassen
        pass
    
    def set_var(self, name, value):
        # Variable im aktuellen Scope setzen
        pass
    
    def get_var(self, name):
        # Variable nachschlagen (mit Fallback)
        pass
    
    def del_var(self, name):
        # Variable aus aktuellem Scope löschen
        pass
    
    def current_scope_vars(self):
        # Alle Variablen im aktuellen Scope
        pass

# Test
sm = ScopeManager()

# Global scope
sm.set_var('x', 100)
sm.set_var('y', 200)

print(f"Global x: {sm.get_var('x')}")  # 100

# Function scope
sm.enter_scope()
sm.set_var('x', 10)  # Shadows global x
sm.set_var('z', 30)

print(f"Local x: {sm.get_var('x')}")   # 10
print(f"Local y: {sm.get_var('y')}")   # 200 (from global)
print(f"Local z: {sm.get_var('z')}")   # 30

# Nested scope
sm.enter_scope()
sm.set_var('x', 1)  # Shadows local x

print(f"Nested x: {sm.get_var('x')}")  # 1
print(f"Nested z: {sm.get_var('z')}")  # 30 (from parent)

# Back to local
sm.exit_scope()
print(f"Back to local x: {sm.get_var('x')}")  # 10

# Back to global
sm.exit_scope()
print(f"Back to global x: {sm.get_var('x')}")  # 100
```

**Erwarteter Output:**
```
Global x: 100
Local x: 10
Local y: 200
Local z: 30
Nested x: 1
Nested z: 30
Back to local x: 10
Back to global x: 100
```

---

## 📝 Übung 9: Multi-Level Cache (Schwer)

Implementiere ein Multi-Level Caching-System.

**Anforderungen:**
- Klasse `MultiLevelCache` mit ChainMap
- Mehrere Cache-Level (L1, L2, L3)
- get, set, invalidate Methoden
- LRU-ähnliches Verhalten (optional)

```python
from collections import ChainMap, OrderedDict

class MultiLevelCache:
    def __init__(self, l1_size=5, l2_size=10):
        # Dein Code hier
        # L1: Schnellster, kleinster Cache
        # L2: Mittel
        # L3: Persistent/Langsam (simuliert mit dict)
        pass
    
    def get(self, key):
        # Sucht in L1 > L2 > L3
        # Promoted zu L1 bei Hit
        pass
    
    def set(self, key, value):
        # Setzt in L1
        pass
    
    def invalidate(self, key):
        # Entfernt aus allen Levels
        pass
    
    def stats(self):
        # Gibt Cache-Statistiken zurück
        pass

# Test
cache = MultiLevelCache(l1_size=2, l2_size=3)

# Items hinzufügen
cache.set('a', 1)
cache.set('b', 2)
cache.set('c', 3)

print(cache.get('a'))  # 1, in L1

cache.set('d', 4)
cache.set('e', 5)

# 'a' sollte noch in L2 sein
print(cache.get('a'))  # 1, promoted von L2 zu L1

print(cache.stats())
```

**Erwarteter Output:**
```
1
1
{'l1_size': 2, 'l2_size': 3, 'l3_size': 3, 'total_items': 5}
```

---

## 📝 Übung 10: Text Analysis Suite (Experte)

Erstelle eine umfassende Text-Analyse-Suite.

**Anforderungen:**
- Klasse `TextAnalyzer`
- Verwendet Counter, defaultdict für verschiedene Analysen
- Methoden: word_frequency, char_frequency, ngrams, most_common_pattern
- Statistical summaries

```python
from collections import Counter, defaultdict
import re

class TextAnalyzer:
    def __init__(self, text):
        # Dein Code hier
        # Bereitet Text auf und initialisiert Counter
        pass
    
    def word_frequency(self, top_n=10):
        # Top N häufigste Wörter
        pass
    
    def char_frequency(self, exclude_spaces=True):
        # Buchstabenhäufigkeit
        pass
    
    def ngrams(self, n=2):
        # N-Gramme (Wortpaare, Tripel, etc.)
        pass
    
    def sentence_stats(self):
        # Durchschnittliche Satzlänge, etc.
        pass
    
    def most_common_pattern(self):
        # Häufigstes Wort-Pattern (z.B. Adjektiv+Nomen)
        pass

# Test
text = """
Python ist eine tolle Programmiersprache. Python macht Spaß
und Python ist einfach zu lernen. Viele Entwickler lieben Python
wegen seiner Einfachheit. Python, Python, Python!
"""

analyzer = TextAnalyzer(text)

print("Top 5 Wörter:")
for wort, count in analyzer.word_frequency(5):
    print(f"  {wort}: {count}x")

print("\nTop 5 Buchstaben:")
for char, count in analyzer.char_frequency()[:5]:
    print(f"  {char}: {count}x")

print("\nTop 3 Wortpaare:")
for ngram, count in analyzer.ngrams(2)[:3]:
    print(f"  {' '.join(ngram)}: {count}x")

stats = analyzer.sentence_stats()
print(f"\nSatz-Statistiken:")
print(f"  Durchschnittliche Länge: {stats['avg_length']:.1f} Wörter")
print(f"  Anzahl Sätze: {stats['sentence_count']}")
```

**Erwarteter Output:**
```
Top 5 Wörter:
  python: 6x
  ist: 2x
  eine: 1x
  tolle: 1x
  programmiersprache: 1x

Top 5 Buchstaben:
  e: 20x
  n: 18x
  i: 15x
  t: 12x
  h: 10x

Top 3 Wortpaare:
  python ist: 2x
  ist eine: 1x
  macht spaß: 1x

Satz-Statistiken:
  Durchschnittliche Länge: 6.8 Wörter
  Anzahl Sätze: 4
```

---

## 💡 Lösungsstrategien

### Für Übung 1-3 (Grundlagen):
- Counter.most_common() für Top-N
- Counter-Arithmetik (+, -, &, |)
- defaultdict(list) für Gruppierung

### Für Übung 4-6 (Fortgeschritten):
- ChainMap für Prioritäts-Hierarchien
- tuple(sorted()) als Dict-Key für Anagramme
- Kombiniere mehrere Counter

### Für Übung 7-9 (Profi):
- Verschachtelte defaultdicts
- ChainMap.new_child() für Scopes
- OrderedDict für LRU-Verhalten

### Für Übung 10 (Experte):
- Kombiniere alle Collections-Typen
- Regex für Text-Parsing
- Statistische Aggregationen
- Sliding Window für N-Gramme

---

## 🔍 Selbst-Check

Nach diesen Übungen solltest du:

✅ Counter für Häufigkeitsanalysen nutzen können
✅ defaultdict für automatische Initialisierung einsetzen
✅ ChainMap für hierarchische Mappings verstehen
✅ Counter-Arithmetik beherrschen
✅ Verschachtelte Collections erstellen können
✅ Scope-Management mit ChainMap implementieren

---

## 🎯 Bonus-Herausforderungen

### Challenge 1: Log Analyzer

Erstelle einen Log-Analyzer:
- Parsed verschiedene Log-Formate
- Counter für Error-Types, Levels
- defaultdict für Zeitbasierte Gruppierung
- ChainMap für Multi-Source Logs

### Challenge 2: Configuration Manager

Erweitere ConfigManager:
- Persistenz (Save/Load)
- Validation mit Schemas
- Nested Configurations
- Change Notifications

### Challenge 3: Memory-Efficient Word Index

Implementiere einen Wort-Index:
- Invertierter Index mit defaultdict
- Counter für Wort-Häufigkeiten
- N-Gramm Suche
- Fuzzy Matching

---

## 📚 Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [collections module](https://docs.python.org/3/library/collections.html) - Alle Collections
- [Counter](https://docs.python.org/3/library/collections.html#collections.Counter) - Counter Referenz
- [defaultdict](https://docs.python.org/3/library/collections.html#collections.defaultdict) - defaultdict Referenz
- [ChainMap](https://docs.python.org/3/library/collections.html#collections.ChainMap) - ChainMap Referenz

---

## 🎓 Verwandte Themen

- [[03_Mappings_und_Sets/03_Collections_Module/01_Counter|Counter]] - Counter im Detail
- [[03_Mappings_und_Sets/03_Collections_Module/02_DefaultDict|DefaultDict]] - defaultdict im Detail
- [[03_Mappings_und_Sets/03_Collections_Module/03_ChainMap|ChainMap]] - ChainMap im Detail
- [[06_Praxis/04_Lösungen/Lösungen Teil 3|Lösungen Teil 3]] - Musterlösungen

---

← [[03_Mappings_und_Sets/03_Collections_Module/03_ChainMap|ChainMap]] | [[INDEX|📑 Index]] | [[04_Fortgeschritten/01_Arrays/01_Array Module|Array Module]] →