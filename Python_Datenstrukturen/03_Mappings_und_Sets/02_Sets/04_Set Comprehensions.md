---
tags: [python, datenstrukturen, sets, comprehensions]
created: 2025-10-01
---

← [[03_Mappings_und_Sets/02_Sets/03_Frozenset|Frozenset]] | [[INDEX|📑 Index]] | [[03_Mappings_und_Sets/02_Sets/05_Set Performance|Set Performance]] →

# Set Comprehensions

> [!tip] Lernziel
> Nach dieser Seite kannst du Sets elegant mit Comprehensions erstellen, transformieren und filtern - pythonischer Code für Mengenoperationen.

## Was sind Set Comprehensions?

**Set Comprehensions** sind eine kompakte Syntax zur Erstellung von Sets aus Iterables. Sie sind das Set-Äquivalent zu List Comprehensions.

**Syntax:**
```python
{expression for item in iterable}
{expression for item in iterable if condition}
```

**Vorteile:**
- Kompakter als Loops
- Automatische Duplikat-Entfernung
- Pythonischer Stil
- Oft performanter als äquivalente Loops
- Eine Zeile statt mehrerer

**Unterschiede zu anderen Comprehensions:**
- List: `[x for x in ...]` → Liste mit Duplikaten
- Dict: `{k: v for ...}` → Key-Value Paare
- Set: `{x for x in ...}` → Eindeutige Werte
- Generator: `(x for x in ...)` → Lazy Evaluation

---

## Grundlegende Syntax

### Einfache Comprehension

```python
# Traditioneller Ansatz mit Loop
zahlen_set = set()
for x in range(10):
    zahlen_set.add(x ** 2)

print(zahlen_set)  # {0, 1, 4, 9, 16, 25, 36, 49, 64, 81}

# Mit Set Comprehension
quadrate = {x ** 2 for x in range(10)}
print(quadrate)  # {0, 1, 4, 9, 16, 25, 36, 49, 64, 81}

# Aus String - Duplikate automatisch entfernt
buchstaben = {char for char in "hello"}
print(buchstaben)  # {'h', 'e', 'l', 'o'}

# Aus Liste
woerter = ["apfel", "banane", "apfel", "kirsche"]
unique_woerter = {wort for wort in woerter}
print(unique_woerter)  # {'apfel', 'banane', 'kirsche'}
```

### Mit Bedingung (Filter)

```python
# Nur gerade Zahlen
gerade = {x for x in range(20) if x % 2 == 0}
print(gerade)  # {0, 2, 4, 6, 8, 10, 12, 14, 16, 18}

# Nur positive Zahlen
zahlen = [-5, -3, -1, 0, 1, 3, 5]
positiv = {x for x in zahlen if x > 0}
print(positiv)  # {1, 3, 5}

# Nur lange Wörter
woerter = ["hi", "hallo", "python", "code", "programmierung"]
lang = {wort for wort in woerter if len(wort) > 5}
print(lang)  # {'python', 'programmierung'}

# Mehrere Bedingungen
zahlen = {x for x in range(30) if x % 2 == 0 if x > 10}
print(zahlen)  # {12, 14, 16, 18, 20, 22, 24, 26, 28}
```

### Mit Transformation

```python
# Großbuchstaben
woerter = ["apfel", "banane", "kirsche"]
gross = {wort.upper() for wort in woerter}
print(gross)  # {'APFEL', 'BANANE', 'KIRSCHE'}

# Quadratwurzeln
import math
wurzeln = {math.sqrt(x) for x in [1, 4, 9, 16, 25]}
print(wurzeln)  # {1.0, 2.0, 3.0, 4.0, 5.0}

# Längen von Strings
woerter = ["hi", "hallo", "python"]
laengen = {len(wort) for wort in woerter}
print(laengen)  # {2, 5, 6}

# Conditional Expression
zahlen = {x if x > 0 else -x for x in range(-5, 6)}
print(zahlen)  # {0, 1, 2, 3, 4, 5} - Alle zu Betrag
```

---

## Automatische Duplikat-Entfernung

### Hauptvorteil von Set Comprehensions

```python
# Liste mit vielen Duplikaten
zahlen = [1, 2, 2, 3, 3, 3, 4, 4, 4, 4, 5, 5, 5, 5, 5]

# List Comprehension - Behält Duplikate
quadrate_liste = [x ** 2 for x in zahlen]
print(len(quadrate_liste))  # 15 Elemente
print(quadrate_liste[:5])   # [1, 4, 4, 9, 9, ...]

# Set Comprehension - Entfernt automatisch Duplikate
quadrate_set = {x ** 2 for x in zahlen}
print(len(quadrate_set))    # 5 Elemente
print(quadrate_set)         # {1, 4, 9, 16, 25}

# Praktisch: Erste Buchstaben
woerter = ["Apfel", "Ananas", "Banane", "Birne", "Aprikose"]
erste_buchstaben = {wort[0] for wort in woerter}
print(erste_buchstaben)  # {'A', 'B'} - Nur unique
```

### Duplikate während Transformation

```python
# Modulo-Operation erzeugt Duplikate
zahlen = range(20)
modulo = {x % 5 for x in zahlen}
print(modulo)  # {0, 1, 2, 3, 4} - Statt 20 Werten nur 5

# IDs zu Kategorien
user_ids = [101, 102, 103, 201, 202, 301]
kategorien = {uid // 100 for uid in user_ids}
print(kategorien)  # {1, 2, 3} - Nur die Kategorien

# Rundung führt zu Duplikaten
preise = [9.99, 10.01, 19.95, 20.05, 29.99]
gerundet = {round(preis) for preis in preise}
print(gerundet)  # {10, 20, 30}
```

---

## Verschachtelte Comprehensions

### Flaches Set aus verschachtelten Strukturen

```python
# Liste von Listen zu flachem Set
matrix = [[1, 2, 3], [3, 4, 5], [5, 6, 7]]
alle_zahlen = {zahl for zeile in matrix for zahl in zeile}
print(alle_zahlen)  # {1, 2, 3, 4, 5, 6, 7}

# Äquivalent zu:
alle_zahlen = set()
for zeile in matrix:
    for zahl in zeile:
        alle_zahlen.add(zahl)

# Liste von Strings zu allen Buchstaben
woerter = ["hello", "world", "python"]
alle_chars = {char for wort in woerter for char in wort}
print(alle_chars)  # {'h', 'e', 'l', 'o', 'w', 'r', 'd', 'p', 'y', 't', 'n'}

# Tags von mehreren Artikeln
artikel = [
    {"titel": "Python Intro", "tags": ["python", "tutorial"]},
    {"titel": "Web Dev", "tags": ["web", "javascript", "tutorial"]},
    {"titel": "Data Science", "tags": ["python", "data", "ml"]}
]

alle_tags = {tag for art in artikel for tag in art["tags"]}
print(alle_tags)
# {'python', 'tutorial', 'web', 'javascript', 'data', 'ml'}
```

### Mit Bedingungen in beiden Loops

```python
# Nur bestimmte Zahlen aus bestimmten Zeilen
matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]

# Nur gerade Zahlen aus geraden Zeilen (Index)
gerade = {zahl 
          for i, zeile in enumerate(matrix) if i % 2 == 0
          for zahl in zeile if zahl % 2 == 0}
print(gerade)  # {2, 8}

# Alle Vokale aus langen Wörtern
woerter = ["hi", "hallo", "python", "programmierung"]
vokale = {'a', 'e', 'i', 'o', 'u'}

vokale_aus_langen = {char.lower() 
                     for wort in woerter if len(wort) > 5
                     for char in wort if char.lower() in vokale}
print(vokale_aus_langen)  # {'o', 'a', 'i', 'e', 'u'}
```

---

## Praktische Anwendungen

### 1. Duplikat-Erkennung in Daten

```python
# E-Mail-Adressen normalisieren und deduplizieren
emails = [
    "Max@Example.com",
    "anna@test.de",
    "max@example.com",  # Duplikat (andere Schreibweise)
    "Anna@Test.DE",     # Duplikat
    "tom@company.org"
]

# Normalisierte unique E-Mails
unique_emails = {email.lower() for email in emails}
print(f"Unique E-Mails: {len(unique_emails)}")
# 3 statt 5

# Duplikate finden
gesehen = set()
duplikate = {email.lower() for email in emails 
             if email.lower() in gesehen or gesehen.add(email.lower())}
# Trick: add() gibt None zurück, also immer False in or
```

### 2. Daten-Extraktion

```python
# User-IDs aus verschiedenen Quellen sammeln
logs = [
    {"user": 123, "action": "login"},
    {"user": 456, "action": "view"},
    {"user": 123, "action": "logout"},
    {"user": 789, "action": "login"}
]

# Alle unique User-IDs
user_ids = {log["user"] for log in logs}
print(f"Aktive User: {user_ids}")  # {123, 456, 789}

# Nur User die bestimmte Action gemacht haben
logged_out = {log["user"] for log in logs if log["action"] == "logout"}
print(f"Logout User: {logged_out}")  # {123}
```

### 3. String-Verarbeitung

```python
# Alle Wortanfänge
text = "Python ist eine tolle Programmiersprache für Anfänger"
woerter = text.split()
anfaenge = {wort[0].lower() for wort in woerter}
print(f"Wörter beginnen mit: {sorted(anfaenge)}")
# ['a', 'e', 'f', 'i', 'p', 't']

# Unique Wortlängen
laengen = {len(wort) for wort in woerter}
print(f"Wortlängen: {sorted(laengen)}")
# [3, 4, 5, 6, 8, 17]

# Palindrom-Filter
woerter = ["anna", "otto", "python", "level", "code", "racecar"]
palindrome = {wort for wort in woerter if wort == wort[::-1]}
print(f"Palindrome: {palindrome}")
# {'anna', 'otto', 'level', 'racecar'}
```

### 4. Mathematische Mengen

```python
# Primzahlen bis 100 (vereinfacht)
def ist_prim(n):
    if n < 2:
        return False
    for i in range(2, int(n ** 0.5) + 1):
        if n % i == 0:
            return False
    return True

primzahlen = {n for n in range(2, 101) if ist_prim(n)}
print(f"Anzahl Primzahlen 2-100: {len(primzahlen)}")

# Perfekte Quadrate
quadrate = {n * n for n in range(1, 11)}
print(f"Quadrate 1-100: {quadrate}")
# {1, 4, 9, 16, 25, 36, 49, 64, 81, 100}

# Fibonacci-Zahlen bis 1000
a, b = 0, 1
fibonacci = {1}
while b < 1000:
    fibonacci.add(b)
    a, b = b, a + b
print(f"Fibonacci < 1000: {sorted(fibonacci)}")
```

### 5. Daten-Validierung

```python
# Erlaubte Werte definieren
ERLAUBTE_STATUS = {"pending", "active", "inactive", "suspended"}
ERLAUBTE_ROLLEN = {"admin", "user", "guest", "moderator"}

# Benutzer-Daten validieren
users = [
    {"name": "Alice", "status": "active", "role": "admin"},
    {"name": "Bob", "status": "deleted", "role": "user"},  # Ungültiger Status
    {"name": "Charlie", "status": "active", "role": "superuser"}  # Ungültige Rolle
]

# Alle ungültigen Status finden
status_werte = {user["status"] for user in users}
ungueltige_status = status_werte - ERLAUBTE_STATUS
print(f"Ungültige Status: {ungueltige_status}")  # {'deleted'}

# User mit ungültigen Rollen
ungueltige_user = {user["name"] for user in users 
                   if user["role"] not in ERLAUBTE_ROLLEN}
print(f"User mit ungültiger Rolle: {ungueltige_user}")  # {'Charlie'}

# Alle verwendeten aber nicht erlaubten Werte
alle_rollen = {user["role"] for user in users}
neue_rollen = alle_rollen - ERLAUBTE_ROLLEN
if neue_rollen:
    print(f"Neue Rollen erkannt: {neue_rollen}")
```

### 6. Graph und Beziehungen

```python
# Freundschaften (ungerichteter Graph)
freundschaften = [
    ("Alice", "Bob"),
    ("Bob", "Charlie"),
    ("Alice", "Charlie"),
    ("Diana", "Eve"),
    ("Charlie", "Diana")
]

# Alle Personen
alle_personen = {person for paar in freundschaften 
                 for person in paar}
print(f"Personen: {alle_personen}")
# {'Alice', 'Bob', 'Charlie', 'Diana', 'Eve'}

# Freunde von Alice
alice_freunde = {person for paar in freundschaften 
                 for person in paar 
                 if "Alice" in paar and person != "Alice"}
print(f"Alice's Freunde: {alice_freunde}")
# {'Bob', 'Charlie'}

# Personen mit mindestens 2 Freunden
from collections import Counter
alle_beziehungen = [person for paar in freundschaften for person in paar]
freunde_count = Counter(alle_beziehungen)

beliebte = {person for person, count in freunde_count.items() if count >= 2}
print(f"Beliebte Personen (≥2 Freunde): {beliebte}")
# {'Charlie', 'Bob', 'Alice'}
```

---

## Performance-Überlegungen

### Comprehensions vs. Loops

```python
import timeit

# Setup
data = range(10000)

# Mit Loop
def mit_loop():
    result = set()
    for x in data:
        if x % 2 == 0:
            result.add(x ** 2)
    return result

# Mit Comprehension
def mit_comprehension():
    return {x ** 2 for x in data if x % 2 == 0}

# Benchmark
loop_time = timeit.timeit(mit_loop, number=1000)
comp_time = timeit.timeit(mit_comprehension, number=1000)

print(f"Loop:         {loop_time:.3f}s")
print(f"Comprehension: {comp_time:.3f}s")
print(f"Speedup:      {loop_time/comp_time:.2f}x")

# Comprehension ist ~20-30% schneller!
```

### Set vs. List Comprehension für Duplikate

```python
import timeit

# Daten mit vielen Duplikaten
data = [i % 100 for i in range(10000)]

# List Comprehension + set()
def list_dann_set():
    return set([x for x in data])

# Set Comprehension direkt
def set_direkt():
    return {x for x in data}

# Benchmark
list_time = timeit.timeit(list_dann_set, number=1000)
set_time = timeit.timeit(set_direkt, number=1000)

print(f"List dann set(): {list_time:.3f}s")
print(f"Set Comprehension: {set_time:.3f}s")
print(f"Speedup:         {list_time/set_time:.2f}x")

# Set Comprehension ist schneller - keine Zwischenliste!
```

---

## Häufige Fallstricke

### ⚠️ Fallstrick 1: Leeres Set vs. Dict

```python
# ❌ FALSCH - Dies ist ein leeres Dict!
leer = {}
print(type(leer))  # <class 'dict'>

# ✅ RICHTIG - Leeres Set
leer = set()
print(type(leer))  # <class 'set'>

# Mit Comprehension: Immer Set (wenn nicht leer)
nicht_leer = {x for x in range(0)}  # Leeres Set!
print(type(nicht_leer))  # <class 'set'>

# Aber wenn leer, ist es trotzdem ein set:
leer_comp = {x for x in [] if False}
print(type(leer_comp))  # <class 'set'>
print(leer_comp)  # set()
```

### ⚠️ Fallstrick 2: Unhashable Elemente

```python
# ❌ FALSCH - Listen sind nicht hashbar
daten = [[1, 2], [3, 4], [5, 6]]
try:
    s = {item for item in daten}
except TypeError as e:
    print(f"Fehler: {e}")
    # unhashable type: 'list'

# ✅ RICHTIG - Zu Tuple konvertieren
s = {tuple(item) for item in daten}
print(s)  # {(1, 2), (3, 4), (5, 6)}

# Oder nur hashbare Teile extrahieren
daten = [[1, 2, 3], [4, 5], [6]]
laengen = {len(item) for item in daten}
print(laengen)  # {1, 2, 3}
```

### ⚠️ Fallstrick 3: Zu komplexe Comprehensions

```python
# ❌ UNLESERLICH - Zu komplex
kompliziert = {
    (x, y, z) 
    for x in range(10) if x % 2 == 0
    for y in range(x) if y % 3 == 0
    for z in range(y) if z % 2 == 1
}

# ✅ BESSER - In mehrere Schritte aufteilen
gerade = {x for x in range(10) if x % 2 == 0}
ergebnis = set()
for x in gerade:
    for y in range(x):
        if y % 3 == 0:
            for z in range(y):
                if z % 2 == 1:
                    ergebnis.add((x, y, z))

# Oder Hilfsfunktion verwenden
def ist_gueltig(x, y, z):
    return x % 2 == 0 and y % 3 == 0 and z % 2 == 1

ergebnis = {
    (x, y, z)
    for x in range(10)
    for y in range(x)
    for z in range(y)
    if ist_gueltig(x, y, z)
}
```

### ⚠️ Fallstrick 4: Side Effects in Comprehensions

```python
# ❌ SCHLECHT - Side Effects vermeiden
counter = [0]  # List um Mutation zu erlauben

def increment_and_return(x):
    counter[0] += 1
    return x

# Comprehension sollte keine Side Effects haben!
zahlen = {increment_and_return(x) for x in range(10)}
print(f"Counter: {counter[0]}")  # Unvorhersehbar!

# ✅ BESSER - Pure Functions
zahlen = {x for x in range(10)}
counter = len(zahlen)
```

---

## Best Practices

### ✅ Do's

```python
# 1. Kurz und lesbar halten
quadrate = {x**2 for x in range(10)}

# 2. Für Duplikat-Entfernung nutzen
unique = {item.lower() for item in items}

# 3. Mit Filtern kombinieren
gerade = {x for x in zahlen if x % 2 == 0}

# 4. Aus Nested Structures extrahieren
tags = {tag for artikel in artikel_liste for tag in artikel['tags']}
```

### ❌ Don'ts

```python
# 1. Keine Side Effects
# s = {print(x) or x for x in range(10)}  # Nein!

# 2. Nicht zu verschachtelt
# s = {x for y in ... for z in ... for x in ... if ...}  # Zu komplex!

# 3. Keine exception handling
# s = {int(x) for x in data}  # Kann ValueError werfen!
# Besser: Mit try-except außerhalb oder Validierung

# 4. Nicht für große Datenmengen ohne Filter
# s = {x for x in range(100_000_000)}  # Viel RAM!
# Besser: Generator Expression wenn möglich
```

---

## Set Comprehension vs. Generator Expression

```python
# Set Comprehension - Erstellt Set sofort
set_comp = {x**2 for x in range(1000)}
print(type(set_comp))  # <class 'set'>
print(len(set_comp))   # 1000

# Generator Expression - Lazy Evaluation
gen_expr = (x**2 for x in range(1000))
print(type(gen_expr))  # <class 'generator'>

# Zu Set konvertieren wenn benötigt
result = set(gen_expr)
print(len(result))  # 1000

# Wann was verwenden?
# Set Comprehension: Wenn Set sofort benötigt
# Generator + set(): Wenn Filterung/Transformation vor Set-Erstellung
```

---

## Frozenset Comprehensions

```python
# ❌ Keine direkte Syntax für frozenset Comprehensions
# Aber: Set Comprehension + frozenset()

# Set Comprehension
normal = {x**2 for x in range(5)}
print(normal)  # {0, 1, 4, 9, 16}

# Zu frozenset konvertieren
frozen = frozenset(x**2 for x in range(5))
print(frozen)  # frozenset({0, 1, 4, 9, 16})

# Oder:
frozen = frozenset({x**2 for x in range(5)})

# Verschachtelte frozensets
gruppen = {
    frozenset({x, x+1, x+2}) 
    for x in range(0, 10, 3)
}
print(gruppen)
# {frozenset({0, 1, 2}), frozenset({3, 4, 5}), ...}
```

---

## Kombination mit Set-Operationen

```python
# Comprehensions mit Set-Operationen kombinieren
a = {1, 2, 3, 4, 5}
b = {4, 5, 6, 7, 8}

# Nur Elemente aus a, quadriert, die nicht in b
result = {x**2 for x in (a - b)}
print(result)  # {1, 4, 9}

# Gemeinsame Elemente, transformiert
gemeinsam = {x * 10 for x in (a & b)}
print(gemeinsam)  # {40, 50}

# Union mit Transformation
alle = {x % 3 for x in (a | b)}
print(alle)  # {0, 1, 2}

# Komplexeres Beispiel: Tags aus mehreren Quellen
quelle1_tags = {"python", "programming", "tutorial"}
quelle2_tags = {"python", "web", "javascript"}
quelle3_tags = {"data", "ml", "python"}

# Nur Tags die in mindestens 2 Quellen vorkommen
alle_quellen = [quelle1_tags, quelle2_tags, quelle3_tags]
haeufige_tags = {
    tag 
    for tag in (quelle1_tags | quelle2_tags | quelle3_tags)
    if sum(1 for quelle in alle_quellen if tag in quelle) >= 2
}
print(haeufige_tags)  # {'python'}
```

---

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [Set Comprehensions](https://docs.python.org/3/tutorial/datastructures.html#sets) - Tutorial
- [Comprehensions](https://docs.python.org/3/reference/expressions.html#displays-for-lists-sets-and-dictionaries) - Sprachspezifikation
- [PEP 274 – Dict Comprehensions](https://peps.python.org/pep-0274/) - Ähnliche Syntax

---

## Verwandte Themen

- [[03_Mappings_und_Sets/02_Sets/01_Set Basics|Set Basics]] - Grundlagen von Sets
- [[03_Mappings_und_Sets/02_Sets/02_Set Operationen|Set Operationen]] - Für Kombination mit Comprehensions
- [[02_Sequenzen/04_Comprehensions/01_List Comprehensions|List Comprehensions]] - Ähnliche Syntax
- [[03_Mappings_und_Sets/01_Dictionaries/03_Dict Comprehensions|Dict Comprehensions]] - Verwandte Comprehensions
- [[02_Sequenzen/04_Comprehensions/02_Generator Expressions|Generator Expressions]] - Alternative

---

← [[03_Mappings_und_Sets/02_Sets/03_Frozenset|Frozenset]] | [[INDEX|📑 Index]] | [[03_Mappings_und_Sets/02_Sets/05_Set Performance|Set Performance]] →