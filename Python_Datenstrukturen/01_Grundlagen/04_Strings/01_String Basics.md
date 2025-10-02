---
tags: [python, datenstrukturen, strings, text, grundlagen]
created: 2025-10-01
---

← [[04_Übungen Bool und None|Übungen Bool und None]] | [[INDEX|📑 Index]] | [[02_String Methoden|String Methoden]] →

# String Basics

> [!tip] Lernziel
> Du verstehst Strings als immutable Sequenzen, ihre Erstellung, grundlegende Operationen und Indexierung

## Was ist ein String?

Ein **String** (str) ist eine **immutable Sequenz von Unicode-Zeichen**. Strings repräsentieren Text in Python.

```python
# Strings erstellen
text1 = "Hallo Welt"
text2 = 'Python'
text3 = """Mehrzeiliger
String"""

print(type(text1))  # <class 'str'>

# Strings sind unveränderbar (immutable)
text = "Python"
# text[0] = "J"  # TypeError: 'str' object does not support item assignment
```

## String-Literale

### Verschiedene Anführungszeichen

```python
# Einfache Anführungszeichen
text1 = 'Hallo'

# Doppelte Anführungszeichen
text2 = "Welt"

# Unterschied nur bei Quotes im String
text3 = "Er sagte 'Hallo'"
text4 = 'Sie sagte "Hallo"'

# Escaping
text5 = 'Er sagte \'Hallo\''
text6 = "Sie sagte \"Hallo\""

print(text3)  # Er sagte 'Hallo'
print(text4)  # Sie sagte "Hallo"
```

### Mehrzeilige Strings

```python
# Triple-Quotes für mehrzeilige Strings
text = """Dies ist ein
mehrzeiliger
String"""

print(text)
# Dies ist ein
# mehrzeilig
# String

# Auch mit einfachen Quotes
text2 = '''Auch so
funktioniert
es'''

# Praktisch für Docstrings
def meine_funktion():
    """
    Dies ist ein Docstring.
    Er kann mehrere Zeilen haben.
    """
    pass
```

### Raw Strings

```python
# Raw Strings (r-Prefix) - Backslashes werden nicht escaped
normal = "C:\neue\daten"
print(normal)   # C:
                # eue\daten (Escape-Sequenzen!)

raw = r"C:\neue\daten"
print(raw)      # C:\neue\daten (literal)

# Besonders nützlich für Regex und Pfade
import re
pattern = r"\d+"  # Regex für Zahlen
pfad = r"C:\Users\name\Documents"
```

### Escape-Sequenzen

```python
# Häufige Escape-Sequenzen
print("Zeile 1\nZeile 2")           # \n = Newline
print("Tab\tSepariert")             # \t = Tab
print("Backslash: \\")              # \\ = Backslash
print("Quote: \"")                  # \" = Double Quote
print("Bell: \a")                   # \a = Bell (piept)

# Unicode-Escape
print("\u2764")                     # ❤ (Unicode)
print("\U0001F600")                 # 😀 (Emoji)
print("\N{SNOWMAN}")                # ☃ (Named Unicode)

# Hex und Oktal
print("\x41")                       # A (Hexadezimal)
print("\101")                       # A (Oktal)
```

## String-Erstellung

### Konstruktor str()

```python
# Aus verschiedenen Typen
zahl = 42
text = str(zahl)
print(text)      # "42"
print(type(text)) # <class 'str'>

# Aus Liste
liste = [1, 2, 3]
print(str(liste))  # "[1, 2, 3]"

# Aus Boolean
print(str(True))   # "True"
print(str(False))  # "False"

# Aus None
print(str(None))   # "None"
```

### String-Konkatenation

```python
# Mit + Operator
vorname = "Max"
nachname = "Mustermann"
vollname = vorname + " " + nachname
print(vollname)  # Max Mustermann

# Mit * Operator (Wiederholung)
print("Ha" * 3)  # HaHaHa
print("-" * 40)  # ----------------------------------------

# Implizite Konkatenation (nur bei Literalen!)
text = "Hallo " "Welt"
print(text)  # Hallo Welt

# Über mehrere Zeilen
long_text = ("Dies ist ein "
             "sehr langer "
             "Text")
print(long_text)  # Dies ist ein sehr langer Text
```

## Indexierung und Slicing

### Positiver Index

```python
text = "Python"
#       012345

# Einzelne Zeichen
print(text[0])   # P
print(text[1])   # y
print(text[5])   # n

# Index out of range
# print(text[6])  # IndexError: string index out of range
```

### Negativer Index

```python
text = "Python"
#      -6-5-4-3-2-1

# Von hinten zählen
print(text[-1])  # n (letztes Zeichen)
print(text[-2])  # o
print(text[-6])  # P (erstes Zeichen)
```

### Slicing

```python
text = "Python Programming"
#       0123456789...

# [start:end] - end ist exklusiv!
print(text[0:6])    # Python
print(text[7:18])   # Programming

# Shortcuts
print(text[:6])     # Python (von Anfang)
print(text[7:])     # Programming (bis Ende)
print(text[:])      # Python Programming (komplette Kopie)

# Negativer Index im Slice
print(text[-11:])   # Programming
print(text[:-12])   # Python

# Mit Step
print(text[::2])    # Pto rgamn (jedes zweite Zeichen)
print(text[1::2])   # yhnPoamig (ab Index 1, jedes zweite)

# Umkehren mit negativem Step
print(text[::-1])   # gnimmargorP nohtyP (rückwärts)
```

### Slicing-Beispiele

```python
text = "Hallo Welt"

# Erstes Wort
print(text[:5])      # Hallo

# Letztes Wort
print(text[6:])      # Welt

# Ohne erstes und letztes Zeichen
print(text[1:-1])    # allo Wel

# Jeden zweiten Buchstaben
print(text[::2])     # HloWl

# Rückwärts
print(text[::-1])    # tleW ollaH

# Mittlere 4 Zeichen
mitte_start = len(text) // 2 - 2
mitte_end = len(text) // 2 + 2
print(text[mitte_start:mitte_end])  # o We
```

## String-Länge und Membership

### len() - Länge

```python
text = "Python"
print(len(text))  # 6

# Leerer String
print(len(""))    # 0

# Mit Umlauten (Unicode-Zeichen = 1)
print(len("Über"))  # 4

# Emoji = 1 Zeichen (aber mehrere Bytes!)
print(len("😀"))    # 1
```

### in / not in - Membership

```python
text = "Python Programming"

# Substring-Test
print("Python" in text)      # True
print("Java" in text)        # False
print("program" in text)     # False (case-sensitive!)
print("Programming" in text) # True

# not in
print("Ruby" not in text)    # True

# Einzelne Zeichen
print("P" in text)           # True
print("x" in text)           # False

# Praktisch für Validierung
email = "user@example.com"
if "@" in email and "." in email:
    print("Könnte eine Email sein")
```

## String-Vergleich

### Gleichheit

```python
# == prüft Wert-Gleichheit
text1 = "Python"
text2 = "Python"
text3 = "python"

print(text1 == text2)  # True
print(text1 == text3)  # False (case-sensitive!)
print(text1 != text3)  # True

# is prüft Identität (gleiche Objekte)
print(text1 is text2)  # True (String Interning!)

# Bei konstruierten Strings oft nicht
text4 = "Py" + "thon"
print(text1 == text4)  # True (gleicher Wert)
print(text1 is text4)  # Möglicherweise False
```

### Alphabetische Ordnung

```python
# Strings können verglichen werden (<, >, <=, >=)
print("A" < "B")      # True
print("a" > "Z")      # True (Kleinbuchstaben > Großbuchstaben in Unicode)
print("Apple" < "Banana")  # True

# Lexikographischer Vergleich
print("10" < "2")     # True (String-Vergleich, nicht numerisch!)
print("abc" < "b")    # True

# Zeichenweise
print("Python" < "Pythonista")  # True

# Case-insensitive Vergleich
text1 = "Python"
text2 = "python"
print(text1.lower() == text2.lower())  # True
```

## String-Iteration

### Zeichenweise

```python
text = "Python"

# Mit for-Loop
for zeichen in text:
    print(zeichen)
# P
# y
# t
# h
# o
# n

# Mit Index
for i in range(len(text)):
    print(f"{i}: {text[i]}")
# 0: P
# 1: y
# ...

# Mit enumerate()
for index, zeichen in enumerate(text):
    print(f"{index}: {zeichen}")
```

### String aufteilen

```python
text = "Python ist toll"

# split() - teilt bei Leerzeichen
wörter = text.split()
print(wörter)  # ['Python', 'ist', 'toll']

# split() mit Separator
datum = "2024-03-15"
teile = datum.split("-")
print(teile)   # ['2024', '03', '15']

# Maximal n Splits
text = "a,b,c,d,e"
print(text.split(",", 2))  # ['a', 'b', 'c,d,e']

# splitlines() - teilt bei Zeilenumbrüchen
mehrzeiler = "Zeile 1\nZeile 2\nZeile 3"
zeilen = mehrzeiler.splitlines()
print(zeilen)  # ['Zeile 1', 'Zeile 2', 'Zeile 3']
```

## String-Immutability

```python
# Strings können NICHT geändert werden!
text = "Python"

# ❌ Geht nicht
# text[0] = "J"  # TypeError

# ✅ Neuen String erstellen
text = "J" + text[1:]
print(text)  # Jython

# String "ändern" = neuen String erstellen
original = "Hallo"
geändert = original.replace("a", "e")
print(original)  # Hallo (unverändert!)
print(geändert)  # Hello (neuer String)

# Jede "Änderung" erstellt ein neues Objekt
text = "Test"
alte_id = id(text)
text = text + "!"
neue_id = id(text)
print(alte_id != neue_id)  # True (verschiedene Objekte)
```

## Praktische Anwendungen

### Anwendung 1: Text bereinigen

```python
def bereinige_text(text):
    """Entfernt Whitespace und macht lowercase"""
    return text.strip().lower()

eingabe = "  Python Programming  "
sauber = bereinige_text(eingabe)
print(f"'{sauber}'")  # 'python programming'

# Mehrere Leerzeichen zu einem
text = "Zu    viele     Leerzeichen"
sauber = " ".join(text.split())
print(sauber)  # Zu viele Leerzeichen
```

### Anwendung 2: Benutzereingabe validieren

```python
def ist_gültige_email(email):
    """Einfache Email-Validierung"""
    if not email:
        return False
    if "@" not in email:
        return False
    if email.count("@") != 1:
        return False
    if not email.split("@")[1]:
        return False
    return True

print(ist_gültige_email("user@example.com"))  # True
print(ist_gültige_email("ungültig"))          # False
print(ist_gültige_email("zwei@@at.com"))      # False
```

### Anwendung 3: Texte vergleichen

```python
def texte_ähnlich(text1, text2, case_sensitive=False):
    """Prüft ob zwei Texte ähnlich sind"""
    if not case_sensitive:
        text1 = text1.lower()
        text2 = text2.lower()
    
    # Whitespace normalisieren
    text1 = " ".join(text1.split())
    text2 = " ".join(text2.split())
    
    return text1 == text2

t1 = "  Python   Programming  "
t2 = "python programming"
print(texte_ähnlich(t1, t2))  # True
```

### Anwendung 4: String-Template

```python
def erstelle_brief(name, firma, position):
    """Erstellt Brief aus Template"""
    template = """Sehr geehrte/r {name},

vielen Dank für Ihre Bewerbung bei {firma}.
Wir freuen uns, Ihnen die Position als {position} 
anbieten zu können.

Mit freundlichen Grüßen
Die Personalabteilung"""
    
    return template.format(name=name, firma=firma, position=position)

brief = erstelle_brief("Max Mustermann", "TechCorp", "Entwickler")
print(brief)
```

### Anwendung 5: Passwort-Prüfung

```python
def prüfe_passwort(passwort):
    """Prüft Passwortstärke"""
    if len(passwort) < 8:
        return "Zu kurz (min. 8 Zeichen)"
    
    hat_großbuchstabe = any(c.isupper() for c in passwort)
    hat_kleinbuchstabe = any(c.islower() for c in passwort)
    hat_ziffer = any(c.isdigit() for c in passwort)
    hat_sonderzeichen = any(not c.isalnum() for c in passwort)
    
    if not hat_großbuchstabe:
        return "Mindestens ein Großbuchstabe nötig"
    if not hat_kleinbuchstabe:
        return "Mindestens ein Kleinbuchstabe nötig"
    if not hat_ziffer:
        return "Mindestens eine Ziffer nötig"
    if not hat_sonderzeichen:
        return "Mindestens ein Sonderzeichen nötig"
    
    return "Passwort ist stark"

print(prüfe_passwort("Test"))         # Zu kurz
print(prüfe_passwort("testtest"))     # Mindestens ein Großbuchstabe nötig
print(prüfe_passwort("Test1234"))     # Mindestens ein Sonderzeichen nötig
print(prüfe_passwort("Test123!"))     # Passwort ist stark
```

## Häufige Fallstricke

### Fallstrick 1: String-Konkatenation in Loops

```python
# ❌ INEFFIZIENT: Viele String-Objekte werden erstellt
result = ""
for i in range(1000):
    result += str(i)  # Erstellt jedes Mal neuen String!

# ✅ EFFIZIENT: Liste sammeln, dann joinen
parts = []
for i in range(1000):
    parts.append(str(i))
result = "".join(parts)
```

### Fallstrick 2: Mutable String-Äquivalent

```python
# Strings sind immutable
text = "Hallo"
# text[0] = "J"  # TypeError

# ✅ Für häufige Änderungen: Liste von Zeichen
chars = list("Hallo")
chars[0] = "J"
text = "".join(chars)
print(text)  # Jallo

# Oder: bytearray für Bytes
ba = bytearray(b"Hallo")
ba[0] = ord('J')
print(ba.decode())  # Jallo
```

### Fallstrick 3: Strip entfernt nicht in der Mitte

```python
text = "  Hallo  Welt  "

# strip() entfernt nur Anfang/Ende
print(text.strip())       # "Hallo  Welt"
print(text.strip("He"))   # "  Hallo  Welt  " (nicht gefunden an Rändern)

# Für Mitte: replace() oder split()/join()
print(text.replace("  ", " "))     # " Hallo Welt "
print(" ".join(text.split()))      # "Hallo Welt"
```

### Fallstrick 4: String ist Sequenz

```python
# String ist iterable - kann überraschend sein
text = "Python"

# Funktioniert
print(list(text))  # ['P', 'y', 't', 'h', 'o', 'n']

# Kann problematisch sein
def zeige_elemente(container):
    for element in container:
        print(element)

zeige_elemente([1, 2, 3])  # OK
zeige_elemente("ABC")      # Zeigt A, B, C einzeln!
```

### Fallstrick 5: Index out of range

```python
text = "Python"

# ❌ Index-Fehler
try:
    print(text[10])
except IndexError:
    print("Index existiert nicht")

# ✅ Sicher prüfen
if len(text) > 10:
    print(text[10])
else:
    print("String zu kurz")

# ✅ Slicing ist sicher (kein Error)
print(text[0:100])  # Python (gibt was möglich ist)
```

## Performance-Hinweise

### String-Operationen Komplexität

| Operation | Zeitkomplexität | Beschreibung |
|-----------|----------------|--------------|
| `len(s)` | O(1) | Länge ist gecacht |
| `s[i]` | O(1) | Index-Zugriff |
| `s[i:j]` | O(j-i) | Slice-Größe |
| `s + t` | O(len(s) + len(t)) | Konkatenation |
| `s * n` | O(n * len(s)) | Wiederholung |
| `c in s` | O(len(s)) | Substring-Suche |
| `s.find(sub)` | O(len(s)) | Substring-Suche |
| `s.replace(old, new)` | O(len(s)) | Ersetzen |
| `s.split()` | O(len(s)) | Splitten |
| `"".join(list)` | O(total_length) | Join |

### Performance-Tipps

```python
import time

# ❌ Langsam: += in Loop
start = time.perf_counter()
result = ""
for i in range(10000):
    result += "x"
print(f"Concat: {time.perf_counter() - start:.4f}s")

# ✅ Schnell: Liste + join
start = time.perf_counter()
parts = []
for i in range(10000):
    parts.append("x")
result = "".join(parts)
print(f"Join: {time.perf_counter() - start:.4f}s")
# Join ist ~100x schneller!

# ✅ Am schnellsten: Multiplikation
start = time.perf_counter()
result = "x" * 10000
print(f"Mult: {time.perf_counter() - start:.4f}s")
```

## Übungsaufgaben

### Übung 1: Palindrom-Check (Leicht)

Prüfe ob ein String ein Palindrom ist (vorwärts = rückwärts).

**Erwarteter Output:**
```python
print(ist_palindrom("anna"))      # True
print(ist_palindrom("python"))    # False
print(ist_palindrom("A man a plan a canal Panama"))  # True (ignoriere Leerzeichen/Case)
```

### Übung 2: Wörter zählen (Leicht)

Zähle Wörter in einem Text.

**Erwarteter Output:**
```python
text = "Python ist eine tolle Programmiersprache"
print(zähle_wörter(text))  # 5
```

### Übung 3: String umkehren (Mittel)

Kehre Wörter in einem Satz um (nicht Buchstaben).

**Erwarteter Output:**
```python
print(kehre_wörter_um("Hallo Welt Python"))
# "Python Welt Hallo"
```

### Übung 4: Zensur-Funktion (Mittel)

Ersetze Wörter aus einer Blacklist mit Sternchen.

**Erwarteter Output:**
```python
text = "Das ist ein böser Test"
blacklist = ["böser", "Test"]
print(zensiere(text, blacklist))
# "Das ist ein ***** ****"
```

### Übung 5: String-Kompression (Schwer)

Komprimiere Strings nach Run-Length Encoding.

**Erwarteter Output:**
```python
print(komprimiere("aaabbcccc"))      # "a3b2c4"
print(dekomprimiere("a3b2c4"))       # "aaabbcccc"
print(komprimiere("abcdef"))         # "abcdef" (keine Kompression sinnvoll)
```

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [Text Sequence Type - str](https://docs.python.org/3/library/stdtypes.html#text-sequence-type-str) - String-Typ Dokumentation
- [String Methods](https://docs.python.org/3/library/stdtypes.html#string-methods) - Alle String-Methoden
- [String Literals](https://docs.python.org/3/reference/lexical_analysis.html#strings) - String-Syntax
- [Common String Operations](https://docs.python.org/3/library/string.html) - String Module

## Zusammenfassung

### Wichtigste Punkte

1. **String-Eigenschaften:**
   - Immutable (unveränderbar)
   - Sequenz von Unicode-Zeichen
   - Verschiedene Quotes möglich

2. **Erstellung:**
   - Literale: `"text"`, `'text'`, `"""text"""`
   - Raw Strings: `r"C:\path"`
   - str() Konstruktor

3. **Operationen:**
   - Konkatenation: `+`
   - Wiederholung: `*`
   - Indexierung: `text[0]`
   - Slicing: `text[start:end:step]`

4. **Wichtige Konzepte:**
   - len() für Länge
   - in/not in für Membership
   - Iteration mit for
   - split() und join()

5. **Performance:**
   - += in Loops vermeiden
   - Liste sammeln + join() nutzen
   - Strings sind immutable = neue Objekte

## Verwandte Themen

- [[02_String Methoden|String Methoden]] - Alle wichtigen Methoden
- [[03_Formatierung (f-strings)|Formatierung]] - String-Formatierung
- [[04_Encoding und Unicode|Encoding]] - Unicode und Encodings
- [[05_String Performance|Performance]] - Optimierungen
- [[06_Übungen Strings|Übungen]] - Mehr Praxis

---

← [[04_Übungen Bool und None|Übungen Bool und None]] | [[INDEX|📑 Index]] | [[02_String Methoden|String Methoden]] →