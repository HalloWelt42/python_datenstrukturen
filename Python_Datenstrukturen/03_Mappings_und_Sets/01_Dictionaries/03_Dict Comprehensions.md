---
tags: [python, datenstrukturen, mappings, dictionaries, comprehensions]
created: 2025-10-01
---

← [[03_Mappings_und_Sets/01_Dictionaries/02_Dict Methoden|Dict Methoden]] | [[INDEX|📑 Index]] | [[03_Mappings_und_Sets/01_Dictionaries/04_Nested Dicts|Nested Dicts]] →

# Dictionary Comprehensions

> [!tip] Lernziel
> Nach dieser Seite kannst du Dictionaries elegant und pythonisch mit Comprehensions erstellen, transformieren und filtern.

## Was sind Dictionary Comprehensions?

**Dictionary Comprehensions** sind eine kompakte Syntax zur Erstellung von Dictionaries aus Iterables. Sie sind das Dictionary-Äquivalent zu List Comprehensions.

**Syntax:**
```python
{key_expr: value_expr for item in iterable}
{key_expr: value_expr for item in iterable if condition}
```

**Vorteile:**
- Kompakter und lesbarer als Loops
- Oft schneller als äquivalente Loops
- Pythonischer Stil (PEP 274)
- Eine Zeile statt mehrerer

**Wann verwenden:**
- Dictionary aus Iterable erstellen
- Dictionaries transformieren
- Dictionaries filtern
- Keys und Values vertauschen

---

## Grundlegende Syntax

### Einfache Comprehension

```python
# Traditioneller Ansatz mit Loop
quadrate = {}
for x in range(5):
    quadrate[x] = x ** 2

print(quadrate)  # {0: 0, 1: 1, 2: 4, 3: 9, 4: 16}

# Mit Dictionary Comprehension
quadrate = {x: x**2 for x in range(5)}
print(quadrate)  # {0: 0, 1: 1, 2: 4, 3: 9, 4: 16}

# Aus String
buchstaben = {c: ord(c) for c in "ABC"}
print(buchstaben)  # {'A': 65, 'B': 66, 'C': 67}

# Aus Liste
namen = ["Max", "Anna", "Tom"]
laengen = {name: len(name) for name in namen}
print(laengen)  # {'Max': 3, 'Anna': 4, 'Tom': 3}
```

### Mit Bedingung (Filter)

```python
# Nur gerade Zahlen
gerade = {x: x**2 for x in range(10) if x % 2 == 0}
print(gerade)  # {0: 0, 2: 4, 4: 16, 6: 36, 8: 64}

# Nur lange Namen
namen = ["Max", "Anna", "Tom", "Alexander"]
lange_namen = {name: len(name) for name in namen if len(name) > 3}
print(lange_namen)  # {'Anna': 4, 'Alexander': 9}

# Mit mehreren Bedingungen
zahlen = {x: x**2 for x in range(20) if x % 2 == 0 if x > 5}
print(zahlen)  # {6: 36, 8: 64, 10: 100, 12: 144, 14: 196, 16: 256, 18: 324}
```

### Mit if-else (Transformation)

```python
# Conditional Expression im Value
zahlen = {x: "gerade" if x % 2 == 0 else "ungerade" for x in range(5)}
print(zahlen)
# {0: 'gerade', 1: 'ungerade', 2: 'gerade', 3: 'ungerade', 4: 'gerade'}

# Conditional Expression im Key
umgedreht = {"positiv_" + str(x) if x >= 0 else "negativ_" + str(abs(x)): x 
             for x in range(-2, 3)}
print(umgedreht)
# {'negativ_2': -2, 'negativ_1': -1, 'positiv_0': 0, 
#  'positiv_1': 1, 'positiv_2': 2}

# Beide: Filter UND Transform
werte = {x: x**2 if x % 2 == 0 else x**3 
         for x in range(10) if x > 0}
print(werte)
# {1: 1, 2: 4, 3: 27, 4: 16, 5: 125, 6: 36, 7: 343, 8: 64, 9: 729}
```

---

## Aus verschiedenen Datenquellen

### Aus zwei Listen mit zip()

```python
# Keys und Values getrennt
keys = ["name", "alter", "stadt"]
values = ["Max", 30, "Berlin"]

person = {k: v for k, v in zip(keys, values)}
print(person)  # {'name': 'Max', 'alter': 30, 'stadt': 'Berlin'}

# Mit Transformation
zahlen1 = [1, 2, 3, 4]
zahlen2 = [10, 20, 30, 40]

produkte = {f"item_{a}": a * b for a, b in zip(zahlen1, zahlen2)}
print(produkte)
# {'item_1': 10, 'item_2': 40, 'item_3': 90, 'item_4': 160}

# Mit Filter
scores1 = [85, 92, 78, 95]
scores2 = [88, 79, 91, 87]
namen = ["Max", "Anna", "Tom", "Lisa"]

# Nur wenn beide Scores >= 80
gut = {name: (s1 + s2) / 2 
       for name, s1, s2 in zip(namen, scores1, scores2)
       if s1 >= 80 and s2 >= 80}
print(gut)  # {'Max': 86.5, 'Anna': 85.5, 'Lisa': 91.0}
```

### Aus Liste von Tupeln

```python
# Liste von (key, value) Paaren
paare = [("a", 1), ("b", 2), ("c", 3)]
d = {k: v for k, v in paare}
print(d)  # {'a': 1, 'b': 2, 'c': 3}

# Mit Transformation
paare = [("apfel", 2.50), ("banane", 1.80), ("kirsche", 3.00)]
preise_cent = {produkt: int(preis * 100) for produkt, preis in paare}
print(preise_cent)  # {'apfel': 250, 'banane': 180, 'kirsche': 300}
```

### Aus anderem Dictionary

```python
# Dictionary kopieren und transformieren
original = {"a": 1, "b": 2, "c": 3}

# Values verdoppeln
verdoppelt = {k: v * 2 for k, v in original.items()}
print(verdoppelt)  # {'a': 2, 'b': 4, 'c': 6}

# Keys in Großbuchstaben
gross = {k.upper(): v for k, v in original.items()}
print(gross)  # {'A': 1, 'B': 2, 'C': 3}

# Keys und Values vertauschen
umgekehrt = {v: k for k, v in original.items()}
print(umgekehrt)  # {1: 'a', 2: 'b', 3: 'c'}

# Mit Filter
gefiltert = {k: v for k, v in original.items() if v > 1}
print(gefiltert)  # {'b': 2, 'c': 3}
```

### Aus enumerate()

```python
# Index als Key
woerter = ["Python", "ist", "toll"]
indexed = {i: wort for i, wort in enumerate(woerter)}
print(indexed)  # {0: 'Python', 1: 'ist', 2: 'toll'}

# Mit Start-Index
indexed = {i: wort for i, wort in enumerate(woerter, start=1)}
print(indexed)  # {1: 'Python', 2: 'ist', 3: 'toll'}

# Wort als Key, Index als Value
umgekehrt = {wort: i for i, wort in enumerate(woerter)}
print(umgekehrt)  # {'Python': 0, 'ist': 1, 'toll': 2}
```

---

## Praktische Anwendungen

### 1. Daten-Transformation

```python
# Temperaturen von Celsius in Fahrenheit
celsius = {"Berlin": 20, "München": 18, "Hamburg": 15}

fahrenheit = {stadt: (temp * 9/5) + 32 
              for stadt, temp in celsius.items()}
print(fahrenheit)
# {'Berlin': 68.0, 'München': 64.4, 'Hamburg': 59.0}

# Preise mit Mehrwertsteuer
nettopreise = {"laptop": 800, "maus": 25, "tastatur": 60}
bruttopreise = {produkt: preis * 1.19 
                for produkt, preis in nettopreise.items()}
print(bruttopreise)
# {'laptop': 952.0, 'maus': 29.75, 'tastatur': 71.4}
```

### 2. Gruppierung und Aggregation

```python
# Wörter nach erstem Buchstaben gruppieren
woerter = ["Apfel", "Banane", "Ananas", "Birne", "Aprikose"]

# Anzahl pro Anfangsbuchstabe
counts = {}
for wort in woerter:
    erste = wort[0]
    counts[erste] = counts.get(erste, 0) + 1

# Eleganter mit Comprehension (nach Vorbereitung)
from collections import Counter
erste_buchstaben = [w[0] for w in woerter]
count_dict = dict(Counter(erste_buchstaben))
print(count_dict)  # {'A': 3, 'B': 2}

# Gruppierung: Buchstabe -> Liste von Wörtern
gruppiert = {}
for wort in woerter:
    erste = wort[0]
    gruppiert.setdefault(erste, []).append(wort)

print(gruppiert)
# {'A': ['Apfel', 'Ananas', 'Aprikose'], 'B': ['Banane', 'Birne']}
```

### 3. Daten-Bereinigung

```python
# Rohdaten mit None und leeren Strings
rohdaten = {
    "name": "Max",
    "alter": 30,
    "email": "",
    "telefon": None,
    "stadt": "Berlin",
    "land": ""
}

# Nur nicht-leere Werte behalten
bereinigt = {k: v for k, v in rohdaten.items() 
             if v and v != ""}
print(bereinigt)
# {'name': 'Max', 'alter': 30, 'stadt': 'Berlin'}

# Whitespace entfernen (nur Strings)
rohdaten2 = {
    "name": "  Max  ",
    "alter": 30,
    "stadt": "Berlin  "
}

bereinigt2 = {
    k: v.strip() if isinstance(v, str) else v 
    for k, v in rohdaten2.items()
}
print(bereinigt2)
# {'name': 'Max', 'alter': 30, 'stadt': 'Berlin'}
```

### 4. Lookup-Tables erstellen

```python
# Monatsnamen -> Nummer
monate = ["Januar", "Februar", "März", "April", "Mai", "Juni",
          "Juli", "August", "September", "Oktober", "November", "Dezember"]

monat_zu_nummer = {monat.lower(): i+1 for i, monat in enumerate(monate)}
print(monat_zu_nummer["märz"])  # 3

# Umgekehrte Lookup
nummer_zu_monat = {i+1: monat for i, monat in enumerate(monate)}
print(nummer_zu_monat[12])  # Dezember

# HTTP Status Codes
status_codes = [
    (200, "OK"),
    (201, "Created"),
    (400, "Bad Request"),
    (404, "Not Found"),
    (500, "Internal Server Error")
]

status_dict = {code: message for code, message in status_codes}
print(status_dict[404])  # Not Found
```

### 5. Feature Flags / Konfiguration

```python
# Features basierend auf Umgebung aktivieren
features = ["api", "admin", "analytics", "debug", "testing"]
environment = "production"

# In Production: debug und testing deaktiviert
flags = {
    feature: environment != "production" 
    for feature in features 
    if feature in ["debug", "testing"]
}
flags.update({
    feature: True 
    for feature in features 
    if feature not in ["debug", "testing"]
})

print(flags)
# {'debug': False, 'testing': False, 'api': True, 
#  'admin': True, 'analytics': True}
```

### 6. Invertierung / Umkehrung

```python
# User ID -> Username
users = {
    1: "max123",
    2: "anna_m",
    3: "tom_dev"
}

# Username -> User ID
username_to_id = {username: uid for uid, username in users.items()}
print(username_to_id["anna_m"])  # 2

# Vorsicht bei nicht-eindeutigen Values!
scores = {"Max": 85, "Anna": 92, "Tom": 85}  # Doppelte 85!
inverted = {score: name for name, score in scores.items()}
print(inverted)  # {85: 'Tom', 92: 'Anna'} - Max wurde überschrieben!

# Besser: Liste von Namen pro Score
from collections import defaultdict
score_to_names = defaultdict(list)
for name, score in scores.items():
    score_to_names[score].append(name)

print(dict(score_to_names))
# {85: ['Max', 'Tom'], 92: ['Anna']}
```

---

## Verschachtelte Comprehensions

### Dictionary of Dictionaries

```python
# Multiplikationstabelle
tabelle = {
    i: {j: i * j for j in range(1, 6)}
    for i in range(1, 6)
}

print(tabelle[3])  # {1: 3, 2: 6, 3: 9, 4: 12, 5: 15}
print(tabelle[3][4])  # 12

# Formatiert ausgeben
for i, zeile in tabelle.items():
    print(f"{i}: {zeile}")

# 1: {1: 1, 2: 2, 3: 3, 4: 4, 5: 5}
# 2: {1: 2, 2: 4, 3: 6, 4: 8, 5: 10}
# ...
```

### Dictionary of Lists

```python
# Zahlen nach Eigenschaft gruppieren
zahlen = range(1, 21)

gruppen = {
    "gerade": [x for x in zahlen if x % 2 == 0],
    "ungerade": [x for x in zahlen if x % 2 != 0],
    "durch_3": [x for x in zahlen if x % 3 == 0],
    "durch_5": [x for x in zahlen if x % 5 == 0]
}

print(gruppen["durch_3"])  # [3, 6, 9, 12, 15, 18]

# Oder mit Comprehension
eigenschaften = ["gerade", "ungerade", "durch_3", "durch_5"]
bedingungen = [
    lambda x: x % 2 == 0,
    lambda x: x % 2 != 0,
    lambda x: x % 3 == 0,
    lambda x: x % 5 == 0
]

# Komplex, aber möglich
gruppen2 = {
    name: [x for x in zahlen if cond(x)]
    for name, cond in zip(eigenschaften, bedingungen)
}
```

### Mehrere Dimensionen

```python
# 3D-Koordinatensystem mit Werten
koordinaten = {
    (x, y): x + y
    for x in range(3)
    for y in range(3)
}

print(koordinaten)
# {(0, 0): 0, (0, 1): 1, (0, 2): 2, (1, 0): 1, (1, 1): 2, 
#  (1, 2): 3, (2, 0): 2, (2, 1): 3, (2, 2): 4}

print(koordinaten[(1, 2)])  # 3

# Schachbrett
schachbrett = {
    (zeile, spalte): "weiß" if (zeile + spalte) % 2 == 0 else "schwarz"
    for zeile in range(8)
    for spalte in range(8)
}

print(schachbrett[(0, 0)])  # weiß
print(schachbrett[(0, 1)])  # schwarz
```

---

## Häufige Fallstricke

### ⚠️ Fallstrick 1: Doppelte Keys

```python
# Letzter Wert gewinnt!
woerter = ["apfel", "banane", "apfel", "kirsche"]
laengen = {wort: len(wort) for wort in woerter}
print(laengen)
# {'apfel': 5, 'banane': 6, 'kirsche': 7}
# Nur ein 'apfel' Entry!

# Wenn du alle behalten willst: Liste als Value
laengen_liste = {}
for wort in woerter:
    laengen_liste.setdefault(wort, []).append(len(wort))

print(laengen_liste)
# {'apfel': [5, 5], 'banane': [6], 'kirsche': [7]}
```

### ⚠️ Fallstrick 2: Unhashable Keys

```python
# ❌ FALSCH - List als Key
try:
    falsch = {[1, 2]: "value" for _ in range(1)}
except TypeError as e:
    print(f"Fehler: {e}")

# ✅ RICHTIG - Tuple stattdessen
richtig = {(1, 2): "value"}
print(richtig)  # {(1, 2): 'value'}

# Bei verschachtelten Strukturen aufpassen
daten = [[1, 2], [3, 4], [5, 6]]
# d = {item: sum(item) for item in daten}  # TypeError!

# Besser: Tuple verwenden
d = {tuple(item): sum(item) for item in daten}
print(d)  # {(1, 2): 3, (3, 4): 7, (5, 6): 11}
```

### ⚠️ Fallstrick 3: Variable Scope

```python
# Vorsicht bei Closures in Comprehensions
n = 5
quadrate = {x: x**2 for x in range(n)}

# x ist nach Comprehension nicht verfügbar (eigener Scope)
try:
    print(x)  # NameError in Python 3
except NameError:
    print("x ist nicht definiert!")

# Das ist gut - verhindert Namespace-Verschmutzung
```

### ⚠️ Fallstrick 4: Komplexität

```python
# ❌ SCHLECHT - Zu komplex, unleserlich
kompliziert = {
    k: {
        kk: [vv * 2 for vv in v if vv > 0]
        for kk, v in vv.items() if len(v) > 2
    }
    for k, vv in data.items() if sum(len(x) for x in vv.values()) > 10
}

# ✅ BESSER - In mehrere Schritte aufteilen
# 1. Filtern
gefiltert = {k: v for k, v in data.items() 
             if sum(len(x) for x in v.values()) > 10}

# 2. Transformieren
result = {}
for k, vv in gefiltert.items():
    result[k] = {
        kk: [vv * 2 for vv in v if vv > 0]
        for kk, v in vv.items() if len(v) > 2
    }

# Faustregel: Max 2 for-Klauseln pro Comprehension
```

---

## Performance-Überlegungen

### Comprehensions vs. Loops

```python
import timeit

# Comprehension
def mit_comprehension():
    return {x: x**2 for x in range(1000)}

# Loop
def mit_loop():
    result = {}
    for x in range(1000):
        result[x] = x**2
    return result

# Comprehensions sind meist schneller
print("Comprehension:", timeit.timeit(mit_comprehension, number=10000))
print("Loop:", timeit.timeit(mit_loop, number=10000))

# Comprehension: ~0.3s
# Loop: ~0.4s (ca. 30% langsamer)
```

### Wann NICHT Comprehensions verwenden

```python
# ❌ Bei komplexer Logik - Loop ist klarer
def verarbeite_daten(daten):
    result = {}
    for key, value in daten.items():
        if value > 0:
            if key.startswith("temp_"):
                result[key] = value * 2
            else:
                result[key] = value + 10
        else:
            result[key] = 0
    return result

# Comprehension wäre hier unleserlich!

# ❌ Bei Side Effects
# Comprehensions sollten KEINE Side Effects haben
counter = 0
# d = {x: (counter := counter + 1) for x in range(10)}  # Nicht idiomatisch!

# ✅ Loop für Side Effects
counter = 0
result = {}
for x in range(10):
    counter += 1
    result[x] = counter
```

---

## Best Practices

### ✅ Do's

```python
# 1. Kurz und lesbar halten
quadrate = {x: x**2 for x in range(10)}

# 2. Sinnvolle Namen verwenden
user_scores = {user.name: user.score for user in users}

# 3. Filter klar formulieren
aktive = {k: v for k, v in users.items() if v.is_active}

# 4. Für einfache Transformationen
upper = {k: v.upper() for k, v in strings.items()}
```

### ❌ Don'ts

```python
# 1. Keine Side Effects
# d = {k: print(k) or k for k in range(10)}  # Nein!

# 2. Nicht zu verschachtelt
# kompliziert = {x: {y: {z: ... for z in ...} for y in ...} for x in ...}

# 3. Keine Exception-Handling
# falsch = {k: v / divisor for k, v in data.items()}  # Kann ZeroDivisionError!

# Besser mit try-except außerhalb oder Loop verwenden

# 4. Nicht für große Datenmengen ohne Filter
# gross = {x: x**2 for x in range(10_000_000)}  # Viel RAM!
```

---

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [PEP 274 – Dict Comprehensions](https://peps.python.org/pep-0274/) - Original Proposal
- [Dictionary Comprehensions](https://docs.python.org/3/tutorial/datastructures.html#dictionaries) - Tutorial
- [Expressions - Comprehensions](https://docs.python.org/3/reference/expressions.html#displays-for-lists-sets-and-dictionaries) - Sprachspezifikation

---

## Verwandte Themen

- [[03_Mappings_und_Sets/01_Dictionaries/02_Dict Methoden|Dict Methoden]] - Methoden für Dict-Operationen
- [[02_Sequenzen/04_Comprehensions/01_List Comprehensions|List Comprehensions]] - Ähnliche Syntax für Listen
- [[03_Mappings_und_Sets/02_Sets/04_Set Comprehensions|Set Comprehensions]] - Comprehensions für Sets
- [[03_Mappings_und_Sets/01_Dictionaries/04_Nested Dicts|Nested Dicts]] - Verschachtelte Dictionaries

---

← [[03_Mappings_und_Sets/01_Dictionaries/02_Dict Methoden|Dict Methoden]] | [[INDEX|📑 Index]] | [[03_Mappings_und_Sets/01_Dictionaries/04_Nested Dicts|Nested Dicts]] →