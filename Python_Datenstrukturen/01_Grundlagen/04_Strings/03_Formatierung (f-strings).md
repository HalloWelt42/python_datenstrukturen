---
tags: [python, datenstrukturen, strings, formatierung, f-strings]
created: 2025-10-01
---

← [[02_String Methoden|String Methoden]] | [[INDEX|📑 Index]] | [[04_Encoding und Unicode|Encoding und Unicode]] →

# Formatierung (f-strings)

> [!tip] Lernziel
> Du beherrschst alle Methoden der String-Formatierung, besonders moderne f-strings mit ihren erweiterten Features

## String-Formatierung Überblick

Python bietet **drei Hauptmethoden** für String-Formatierung:

```python
name = "Alice"
age = 30

# 1. %-Formatierung (alt, C-Style)
text = "Hallo %s, du bist %d Jahre alt" % (name, age)

# 2. str.format() (mittel)
text = "Hallo {}, du bist {} Jahre alt".format(name, age)

# 3. f-strings (modern, empfohlen!)
text = f"Hallo {name}, du bist {age} Jahre alt"

print(text)  # Hallo Alice, du bist 30 Jahre alt
```

## F-Strings (Python 3.6+)

### Grundlagen

```python
# F-String mit Variablen
name = "Bob"
text = f"Hallo {name}!"
print(text)  # Hallo Bob!

# Ausdrücke in geschweiften Klammern
x = 10
y = 20
print(f"{x} + {y} = {x + y}")  # 10 + 20 = 30

# Funktionsaufrufe
def grüße(name):
    return f"Hallo {name}!"

print(f"{grüße('Charlie')}")  # Hallo Charlie!

# Objekt-Attribute
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

person = Person("Diana", 25)
print(f"{person.name} ist {person.age}")  # Diana ist 25
```

### F-Strings mit Formatspezifikationen

```python
# Zahlen formatieren
zahl = 3.14159

print(f"{zahl:.2f}")      # 3.14 (2 Dezimalstellen)
print(f"{zahl:.4f}")      # 3.1416 (4 Dezimalstellen)
print(f"{zahl:10.2f}")    # "      3.14" (width=10, 2 decimals)

# Integer formatieren
zahl = 42
print(f"{zahl:05d}")      # 00042 (mit Nullen auffüllen)
print(f"{zahl:>10}")      # "        42" (rechtsbündig)
print(f"{zahl:<10}")      # "42        " (linksbündig)
print(f"{zahl:^10}")      # "    42    " (zentriert)

# Mit Füllzeichen
print(f"{zahl:*>10}")     # "********42"
print(f"{zahl:0>10}")     # "0000000042"
```

### Zahlen-Formatierung

```python
# Tausender-Trennzeichen
große_zahl = 1234567890
print(f"{große_zahl:,}")           # 1,234,567,890
print(f"{große_zahl:_}")           # 1_234_567_890 (Python 3.6+)

# Prozent
anteil = 0.847
print(f"{anteil:.2%}")             # 84.70%
print(f"{anteil:.0%}")             # 85%

# Wissenschaftliche Notation
print(f"{große_zahl:e}")           # 1.234568e+09
print(f"{große_zahl:.2e}")         # 1.23e+09

# Binär, Oktal, Hex
zahl = 42
print(f"{zahl:b}")                 # 101010 (binär)
print(f"{zahl:o}")                 # 52 (oktal)
print(f"{zahl:x}")                 # 2a (hex lowercase)
print(f"{zahl:X}")                 # 2A (hex uppercase)
print(f"{zahl:#x}")                # 0x2a (mit Prefix)
print(f"{zahl:#b}")                # 0b101010 (mit Prefix)
```

### Alignment und Padding

```python
text = "Python"

# Linksbündig (default für Strings)
print(f"{text:<20}")               # "Python              "
print(f"{text:<20}")               # "Python              "

# Rechtsbündig
print(f"{text:>20}")               # "              Python"

# Zentriert
print(f"{text:^20}")               # "       Python       "

# Mit Füllzeichen
print(f"{text:*<20}")              # "Python**************"
print(f"{text:->20}")              # "--------------Python"
print(f"{text:.^20}")              # ".......Python......."

# Zahlen und Vorzeichen
zahl = 42
print(f"{zahl:+}")                 # +42 (Vorzeichen immer)
print(f"{-zahl:+}")                # -42
print(f"{zahl: }")                 # " 42" (Leerzeichen für positiv)
```

### Datum und Zeit

```python
from datetime import datetime

jetzt = datetime.now()

# Verschiedene Formate
print(f"{jetzt:%Y-%m-%d}")         # 2025-10-01
print(f"{jetzt:%d.%m.%Y}")         # 01.10.2025
print(f"{jetzt:%H:%M:%S}")         # 14:30:45
print(f"{jetzt:%A, %d. %B %Y}")   # Wednesday, 01. October 2025

# Eigenes Format
print(f"Heute ist {jetzt:%d.%m.%Y} um {jetzt:%H:%M} Uhr")
```

### Selbst-Dokumentierende Ausdrücke (Python 3.8+)

```python
# Mit = Operator
x = 10
y = 20

print(f"{x=}")                     # x=10
print(f"{y=}")                     # y=20
print(f"{x + y=}")                 # x + y=30

# Nützlich für Debugging
def berechne(a, b):
    result = a * b + 10
    print(f"{a=}, {b=}, {result=}")
    return result

berechne(5, 7)  # a=5, b=7, result=45

# Mit Formatierung kombinieren
pi = 3.14159
print(f"{pi=:.2f}")                # pi=3.14
```

### Verschachtelte F-Strings

```python
# Verschachtelte Formatierung
breite = 10
präzision = 2
wert = 3.14159

print(f"{wert:{breite}.{präzision}f}")  # "      3.14"

# Mit Variablen für Format
für_alignment = ">"
print(f"{wert:{für_alignment}{breite}.{präzision}f}")

# Komplexe Verschachtelung
zahlen = [1.2345, 2.3456, 3.4567]
max_breite = max(len(f"{z:.2f}") for z in zahlen)
for z in zahlen:
    print(f"{z:>{max_breite}.2f}")
```

### Neue Features Python 3.12+

```python
# Quotes in f-strings (Python 3.12+)
data = {"name": "Alice", "age": 30}

# Früher: Fehler oder umständlich
# print(f"{data["name"]}")  # SyntaxError vor 3.12

# Jetzt einfach möglich
print(f"{data["name"]} ist {data["age"]}")  # Alice ist 30

# Mehrzeilige f-strings mit Einrückung
name = "Bob"
text = f"""
    Hallo {name},
    dies ist eine
    mehrzeilige Nachricht.
"""
print(text)
```

## str.format() Methode

### Grundlagen

```python
# Positionsargumente
print("Hallo {}, du bist {}".format("Alice", 30))

# Mit Indices
print("{0} {1} {0}".format("Hallo", "Welt"))  # Hallo Welt Hallo

# Named Arguments
print("Hallo {name}, du bist {age}".format(name="Bob", age=25))

# Gemischt
print("{0}, {name}!".format("Hallo", name="Charlie"))
```

### Formatspezifikationen

```python
# Wie f-strings, nur mit .format()
pi = 3.14159

print("{:.2f}".format(pi))         # 3.14
print("{:10.2f}".format(pi))       # "      3.14"
print("{:0>10.2f}".format(pi))     # "0000003.14"

# Mit Namen
print("{val:.2f}".format(val=pi))

# Dictionary unpacking
data = {"name": "Diana", "age": 28}
print("Hallo {name}, Alter: {age}".format(**data))
```

### Attribut- und Index-Zugriff

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

person = Person("Eve", 35)

# Attributzugriff
print("{p.name} ist {p.age}".format(p=person))

# List-Zugriff
zahlen = [1, 2, 3]
print("{0[0]}, {0[1]}, {0[2]}".format(zahlen))

# Dict-Zugriff
data = {"x": 10, "y": 20}
print("{d[x]}, {d[y]}".format(d=data))
```

## %-Formatierung (Alt)

```python
# C-Style Formatierung (veraltet, aber noch in Gebrauch)
name = "Frank"
age = 40

# String (%s) und Integer (%d)
print("Hallo %s, du bist %d" % (name, age))

# Float mit Präzision
pi = 3.14159
print("Pi ist ungefähr %.2f" % pi)  # 3.14

# Mit Breite
print("Zahl: %10.2f" % pi)          # "      3.14"

# Dictionary-Syntax
print("%(name)s ist %(age)d" % {"name": name, "age": age})

# Verschiedene Typen
print("%s %d %f %x" % ("Text", 42, 3.14, 255))
# Text 42 3.140000 ff
```

## Template Strings

```python
from string import Template

# Template mit $-Syntax
template = Template("Hallo $name, du bist $age Jahre alt")

# Mit substitute()
result = template.substitute(name="Grace", age=45)
print(result)

# Mit Dictionary
data = {"name": "Henry", "age": 50}
result = template.substitute(data)
print(result)

# safe_substitute() - keine Fehler bei fehlenden Variablen
template = Template("$name arbeitet bei $firma")
result = template.safe_substitute(name="Iris")
print(result)  # Iris arbeitet bei $firma

# Mit Escaping
template = Template("Preis: $$price")
print(template.substitute(price=10))  # Preis: $10
```

## Praktische Anwendungen

### Anwendung 1: Tabellen formatieren

```python
def print_tabelle(daten, headers):
    """Gibt formatierte Tabelle aus"""
    # Spaltenbreiten berechnen
    breiten = []
    for i, header in enumerate(headers):
        max_breite = len(header)
        for row in daten:
            max_breite = max(max_breite, len(str(row[i])))
        breiten.append(max_breite + 2)
    
    # Header
    header_line = "".join(f"{h:<{breiten[i]}}" for i, h in enumerate(headers))
    print(header_line)
    print("-" * sum(breiten))
    
    # Zeilen
    for row in daten:
        line = "".join(f"{str(row[i]):<{breiten[i]}}" for i in range(len(row)))
        print(line)

# Verwendung
headers = ["Name", "Alter", "Stadt"]
daten = [
    ["Alice", 30, "Berlin"],
    ["Bob", 25, "Hamburg"],
    ["Charlie", 35, "München"]
]
print_tabelle(daten, headers)
```

### Anwendung 2: Fortschrittsanzeige

```python
def zeige_fortschritt(prozent, breite=50):
    """ASCII Fortschrittsbalken"""
    gefüllt = int(breite * prozent / 100)
    bar = "█" * gefüllt + "-" * (breite - gefüllt)
    print(f"\r[{bar}] {prozent:.1f}%", end="", flush=True)

# Simulation
import time
for i in range(101):
    zeige_fortschritt(i)
    time.sleep(0.05)
print()  # Newline am Ende
```

### Anwendung 3: Geld formatieren

```python
def formatiere_geld(betrag, währung="EUR"):
    """Formatiert Geldbetrag"""
    symbole = {
        "EUR": "€",
        "USD": "$",
        "GBP": "£"
    }
    
    symbol = symbole.get(währung, währung)
    
    # Mit Tausender-Trennung und 2 Dezimalstellen
    if währung in ["USD"]:
        return f"{symbol}{betrag:,.2f}"
    else:
        # Europäisches Format
        betrag_str = f"{betrag:,.2f}".replace(",", "X").replace(".", ",").replace("X", ".")
        return f"{betrag_str} {symbol}"

print(formatiere_geld(1234567.89, "EUR"))  # 1.234.567,89 €
print(formatiere_geld(1234567.89, "USD"))  # $1,234,567.89
```

### Anwendung 4: Log-Formatierung

```python
from datetime import datetime

def log_nachricht(level, nachricht, **kwargs):
    """Formatiert Log-Nachricht"""
    timestamp = datetime.now()
    
    # Farben für Levels (ANSI codes)
    farben = {
        "INFO": "\033[94m",    # Blau
        "WARNING": "\033[93m", # Gelb
        "ERROR": "\033[91m",   # Rot
        "RESET": "\033[0m"
    }
    
    farbe = farben.get(level, "")
    reset = farben["RESET"]
    
    # Basis-Format
    log = f"{farbe}[{timestamp:%Y-%m-%d %H:%M:%S}] {level:8}{reset} | {nachricht}"
    
    # Extra-Felder
    if kwargs:
        extras = " | ".join(f"{k}={v}" for k, v in kwargs.items())
        log += f" | {extras}"
    
    return log

# Verwendung
print(log_nachricht("INFO", "Server gestartet", port=8080))
print(log_nachricht("WARNING", "Hohe CPU-Last", cpu=95))
print(log_nachricht("ERROR", "Verbindung fehlgeschlagen", host="db.example.com"))
```

### Anwendung 5: SQL-Query Builder

```python
def baue_select_query(tabelle, felder=None, bedingungen=None):
    """Baut SELECT Query"""
    # Felder
    if felder:
        felder_str = ", ".join(felder)
    else:
        felder_str = "*"
    
    # Basis-Query
    query = f"SELECT {felder_str} FROM {tabelle}"
    
    # WHERE-Clause
    if bedingungen:
        where_parts = [f"{k} = {v!r}" for k, v in bedingungen.items()]
        query += " WHERE " + " AND ".join(where_parts)
    
    return query + ";"

# Verwendung
print(baue_select_query("users"))
# SELECT * FROM users;

print(baue_select_query("users", ["name", "email"], {"active": True, "age": 25}))
# SELECT name, email FROM users WHERE active = True AND age = 25;
```

## Häufige Fallstricke

### Fallstrick 1: Quotes in f-strings (vor 3.12)

```python
# ❌ Fehler vor Python 3.12
data = {"name": "Alice"}
# print(f"{data["name"]}")  # SyntaxError

# ✅ Lösungen für Python < 3.12
# 1. Andere Quotes außen
print(f'{data["name"]}')

# 2. Temporäre Variable
name = data["name"]
print(f"{name}")

# 3. get() Methode
print(f"{data.get('name')}")

# ✅ Python 3.12+: Funktioniert direkt
print(f"{data["name"]}")
```

### Fallstrick 2: Ausdrücke mit Gleichheit

```python
x = 10

# ❌ Verwechslung mit self-documenting
# print(f"{x==10}")  # Könnte als x==10 interpretiert werden

# ✅ Klammern oder Leerzeichen
print(f"{x == 10}")   # True
print(f"{(x==10)}")   # True

# Self-documenting (3.8+)
print(f"{x=}")        # x=10
```

### Fallstrick 3: Format-String Sicherheit

```python
# ⚠️ SICHERHEITSRISIKO: User-Input in Format-String
user_input = "{data.__init__.__globals__}"
data = {"name": "Test"}

# ❌ GEFÄHRLICH - kann sensible Daten leaken!
# result = user_input.format(data=data)

# ✅ SICHER: Nur vordefinierte Formate
template = "Hallo {name}"
safe_input = {"name": user_input}  # User-Input als Wert, nicht Format
result = template.format(**safe_input)
```

### Fallstrick 4: Newlines in f-strings

```python
# ❌ Backslash in f-string Expression geht nicht
# print(f"{'Hallo\nWelt'}")  # SyntaxError

# ✅ Lösungen
# 1. Variable
newline = "\n"
print(f"Hallo{newline}Welt")

# 2. Außerhalb der Expression
print(f"Hallo\nWelt")

# 3. chr() Funktion
print(f"Hallo{chr(10)}Welt")
```

### Fallstrick 5: Leere Expressions

```python
# ❌ Leere geschweifte Klammern gehen nicht
# print(f"Test {}")  # SyntaxError

# Für literale geschweiftes Klammern: doppelt
print(f"Test {{geschweifte Klammern}}")  # Test {geschweifte Klammern}
```

## Performance-Hinweise

### Geschwindigkeitsvergleich

```python
import timeit

name = "Alice"
age = 30

# F-String (am schnellsten)
t1 = timeit.timeit(lambda: f"Hallo {name}, du bist {age}", number=100000)
print(f"f-string: {t1:.4f}s")

# .format()
t2 = timeit.timeit(lambda: "Hallo {}, du bist {}".format(name, age), number=100000)
print(f"format(): {t2:.4f}s")

# % Formatierung
t3 = timeit.timeit(lambda: "Hallo %s, du bist %d" % (name, age), number=100000)
print(f"%%:        {t3:.4f}s")

# f-strings sind ~20% schneller als format(), ~30% schneller als %
```

### Memory-Effizienz

```python
import sys

# Alle Methoden erstellen gleich große String-Objekte
s1 = f"Hallo Alice"
s2 = "Hallo {}".format("Alice")
s3 = "Hallo %s" % "Alice"

print(sys.getsizeof(s1))  # Gleich
print(sys.getsizeof(s2))  # Gleich
print(sys.getsizeof(s3))  # Gleich

# Unterschied nur in Erstellung, nicht im Result
```

## Übungsaufgaben

### Übung 1: Rechnungs-Formatter (Leicht)

Formatiere eine Rechnung mit Artikeln und Preisen.

**Erwarteter Output:**
```python
artikel = [("Apfel", 3, 0.50), ("Banane", 5, 0.30)]
print(formatiere_rechnung(artikel))
# Artikel        Anzahl  Preis
# Apfel               3   0,50 €
# Banane              5   0,30 €
# --------------------------------
# Gesamt:                 3,00 €
```

### Übung 2: Dynamische Tabelle (Mittel)

Erstelle eine Funktion die Daten in Tabellenform ausgibt mit automatischer Spaltenbreite.

**Erwarteter Output:**
```python
daten = [
    {"Name": "Alice", "Alter": 30, "Stadt": "Berlin"},
    {"Name": "Bob", "Alter": 25, "Stadt": "Hamburg"}
]
print_dynamic_table(daten)
```

### Übung 3: Template Engine (Mittel)

Einfache Template-Engine mit Variablen und Schleifen.

**Erwarteter Output:**
```python
template = "{% for item in items %}{{ item.name }}: {{ item.price }}€\n{% endfor %}"
data = {"items": [{"name": "A", "price": 10}, {"name": "B", "price": 20}]}
print(render_template(template, data))
```

### Übung 4: SQL Formatter (Schwer)

Formatiere SQL-Queries schön mit Einrückung.

**Erwarteter Output:**
```python
query = "SELECT name, age FROM users WHERE age > 18 AND active = true ORDER BY name"
print(format_sql(query))
# SELECT
#     name,
#     age
# FROM users
# WHERE
#     age > 18
#     AND active = true
# ORDER BY name
```

### Übung 5: Report Generator (Schwer)

Generiere formatierten Bericht mit Grafiken (ASCII).

**Erwarteter Output:**
```python
daten = {"sales": [100, 150, 200, 180, 220]}
print(generate_report(daten))
# Verkaufsbericht
# ================
# 
# Monat 1: ████████████ 100
# Monat 2: ██████████████████ 150
# ...
```

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [f-strings](https://docs.python.org/3/reference/lexical_analysis.html#f-strings) - F-String Spezifikation
- [Format Specification Mini-Language](https://docs.python.org/3/library/string.html#formatspec) - Format-Syntax
- [str.format()](https://docs.python.org/3/library/stdtypes.html#str.format) - format() Methode
- [printf-style Formatting](https://docs.python.org/3/library/stdtypes.html#printf-style-string-formatting) - % Formatierung
- [string Module](https://docs.python.org/3/library/string.html) - Template und mehr

## Zusammenfassung

### Wichtigste Punkte

1. **F-Strings (empfohlen):**
   - Modern und schnell
   - Syntax: `f"{variable}"`
   - Ausdrücke möglich: `f"{x + y}"`
   - Seit Python 3.6

2. **Formatspezifikationen:**
   - Breite: `{var:10}`
   - Präzision: `{float:.2f}`
   - Alignment: `{text:<10}`, `{text:>10}`, `{text:^10}`
   - Füllzeichen: `{var:0>5}`

3. **Spezielle Formate:**
   - Prozent: `{val:.2%}`
   - Tausender: `{num:,}` oder `{num:_}`
   - Hex/Bin: `{num:x}`, `{num:b}`
   - Datum: `{date:%Y-%m-%d}`

4. **Alternative Methoden:**
   - `.format()`: Flexibel, etwas langsamer
   - `%`: Veraltet, aber noch verwendet
   - `Template`: Für User-Input sicher

5. **Best Practices:**
   - ✅ Nutze f-strings für modernen Code
   - ✅ Self-documenting mit `{var=}` (3.8+)
   - ✅ Format-Specs für Zahlen
   - ⚠️ Vorsicht bei User-Input in Formats

## Verwandte Themen

- [[01_String Basics|String Basics]] - String-Grundlagen
- [[02_String Methoden|String Methoden]] - String-Operationen
- [[04_Encoding und Unicode|Encoding]] - Unicode-Handling
- [[05_String Performance|Performance]] - Optimierungen
- [[06_Übungen Strings|Übungen]] - Mehr Praxis

---

← [[02_String Methoden|String Methoden]] | [[INDEX|📑 Index]] | [[04_Encoding und Unicode|Encoding und Unicode]] →