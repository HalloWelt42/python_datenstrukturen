---
tags: [python, datenstrukturen, strings, methoden, text-verarbeitung]
created: 2025-10-01
---

← [[01_String Basics|String Basics]] | [[INDEX|📑 Index]] | [[03_Formatierung (f-strings)|Formatierung (f-strings)]] →

# String Methoden

> [!tip] Lernziel
> Du kennst alle wichtigen String-Methoden und kannst sie effektiv für Text-Verarbeitung einsetzen

## Übersicht String-Methoden

Python bietet **über 45 eingebaute String-Methoden**. Hier sind die wichtigsten kategorisiert:

```python
text = "Python Programming"

# Alle String-Methoden anzeigen
print([m for m in dir(text) if not m.startswith('_')])
```

## Case-Änderung (Groß-/Kleinschreibung)

### upper() und lower()

```python
text = "Python Programming"

# Komplett groß
print(text.upper())  # PYTHON PROGRAMMING

# Komplett klein
print(text.lower())  # python programming

# Praktisch für Case-Insensitive Vergleiche
user_input = "PYTHON"
if user_input.lower() == "python":
    print("Match!")
```

### capitalize() und title()

```python
text = "python programming language"

# Erstes Zeichen groß, Rest klein
print(text.capitalize())  # Python programming language

# Jedes Wort groß (Title Case)
print(text.title())       # Python Programming Language

# Vorsicht bei Apostrophen!
text2 = "don't stop"
print(text2.title())      # Don'T Stop (nicht ideal)
```

### swapcase()

```python
text = "Python Programming"

# Groß ↔ Klein tauschen
print(text.swapcase())  # pYTHON pROGRAMMING
```

### casefold()

```python
# Aggressiver als lower() - für Unicode
text1 = "ß"  # Deutsches ß

print(text1.lower())     # ß
print(text1.casefold())  # ss

# Für internationale Vergleiche
german = "Straße"
if german.casefold() == "strasse".casefold():
    print("Match!")  # Wird ausgegeben
```

## Suchen und Prüfen

### find() und rfind()

```python
text = "Python Programming in Python"

# find() - findet ersten Index (oder -1)
print(text.find("Python"))     # 0
print(text.find("Java"))       # -1 (nicht gefunden)
print(text.find("Python", 10)) # 24 (suche ab Index 10)

# rfind() - findet von rechts (letztes Vorkommen)
print(text.rfind("Python"))    # 24

# Mit Start/End Position
print(text.find("in", 10, 20)) # 14 (zwischen Index 10 und 20)
```

### index() und rindex()

```python
text = "Python Programming"

# Wie find(), aber wirft ValueError wenn nicht gefunden
print(text.index("Python"))    # 0

try:
    print(text.index("Java"))
except ValueError:
    print("Nicht gefunden!")

# rindex() - von rechts
print(text.rindex("m"))        # 13 (letztes 'm')
```

### count()

```python
text = "banana"

# Zählt Vorkommen
print(text.count("a"))     # 3
print(text.count("an"))    # 2
print(text.count("x"))     # 0

# Mit Start/End
print(text.count("a", 2))  # 2 (ab Index 2)
```

### startswith() und endswith()

```python
text = "Python Programming"

# Beginnt mit?
print(text.startswith("Python"))     # True
print(text.startswith("Java"))       # False

# Endet mit?
print(text.endswith("ing"))          # True
print(text.endswith("tion"))         # False

# Mit Tuple - prüft mehrere
print(text.startswith(("Python", "Java", "C++")))  # True

# Mit Start/End Position
print(text.startswith("Pro", 7))     # True (ab Index 7)
```

## Whitespace-Handling

### strip(), lstrip(), rstrip()

```python
text = "   Python   "

# Entfernt Whitespace von beiden Seiten
print(f"'{text.strip()}'")   # 'Python'

# Nur links
print(f"'{text.lstrip()}'")  # 'Python   '

# Nur rechts
print(f"'{text.rstrip()}'")  # '   Python'

# Spezifische Zeichen entfernen
text2 = "###Python###"
print(text2.strip("#"))      # Python

# Mehrere Zeichen
text3 = "...!!!Python!!!..."
print(text3.strip(".!"))     # Python
```

### split() und splitlines()

```python
text = "Python ist eine Programmiersprache"

# Bei Whitespace splitten
words = text.split()
print(words)  # ['Python', 'ist', 'eine', 'Programmiersprache']

# Mit Separator
csv = "Name,Alter,Stadt"
fields = csv.split(",")
print(fields)  # ['Name', 'Alter', 'Stadt']

# Maximal n Splits
text2 = "a:b:c:d:e"
print(text2.split(":", 2))  # ['a', 'b', 'c:d:e']

# rsplit() - von rechts
print(text2.rsplit(":", 2))  # ['a:b:c', 'd', 'e']

# splitlines() - bei Zeilenumbrüchen
multiline = "Zeile 1\nZeile 2\nZeile 3"
lines = multiline.splitlines()
print(lines)  # ['Zeile 1', 'Zeile 2', 'Zeile 3']
```

### join()

```python
# join() ist eine STRING-Methode!
words = ["Python", "ist", "toll"]

# Mit Leerzeichen
text = " ".join(words)
print(text)  # Python ist toll

# Mit Komma
csv = ",".join(words)
print(csv)  # Python,ist,toll

# Mit Newline
text = "\n".join(words)
print(text)
# Python
# ist
# toll

# Funktioniert mit jeder Iterable
zahlen = [1, 2, 3, 4]
text = "-".join(str(n) for n in zahlen)
print(text)  # 1-2-3-4
```

### partition() und rpartition()

```python
text = "Python:Programming:Language"

# Teilt bei erstem Separator in 3 Teile
left, sep, right = text.partition(":")
print(left)   # Python
print(sep)    # :
print(right)  # Programming:Language

# rpartition() - bei letztem Separator
left, sep, right = text.rpartition(":")
print(left)   # Python:Programming
print(sep)    # :
print(right)  # Language

# Wenn Separator nicht gefunden
left, sep, right = text.partition("@")
print(left)   # Python:Programming:Language
print(sep)    # '' (leerer String)
print(right)  # '' (leerer String)
```

## Ersetzen und Ändern

### replace()

```python
text = "Python ist toll, Python ist einfach"

# Alle Vorkommen ersetzen
new_text = text.replace("Python", "Java")
print(new_text)  # Java ist toll, Java ist einfach

# Nur n Vorkommen ersetzen
new_text = text.replace("Python", "Java", 1)
print(new_text)  # Java ist toll, Python ist einfach

# Mehrfach verketten
text = "Hallo Welt"
new_text = text.replace("a", "e").replace("o", "u")
print(new_text)  # Hellu Welt
```

### translate() und maketrans()

```python
# translate() für Zeichen-Mapping
text = "Hello World"

# Erstelle Translation Table
trans_table = str.maketrans("aeiou", "12345")
translated = text.translate(trans_table)
print(translated)  # H2ll4 W4rld

# Zeichen entfernen (None als zweiter Wert)
trans_table = str.maketrans("", "", "aeiou")
no_vowels = text.translate(trans_table)
print(no_vowels)  # Hll Wrld

# ROT13 Cipher
import string
rot13 = str.maketrans(
    string.ascii_lowercase + string.ascii_uppercase,
    string.ascii_lowercase[13:] + string.ascii_lowercase[:13] +
    string.ascii_uppercase[13:] + string.ascii_uppercase[:13]
)
encrypted = "Hello".translate(rot13)
print(encrypted)  # Uryyb
```

### expandtabs()

```python
text = "Name\tAlter\tStadt"
print(text)  # Name    Alter   Stadt

# Tab-Stops auf 4 Zeichen
expanded = text.expandtabs(4)
print(expanded)  # Name Alter Stadt

# Tab-Stops auf 10 Zeichen
expanded = text.expandtabs(10)
print(expanded)  # Name      Alter     Stadt
```

## Ausrichten und Auffüllen

### center(), ljust(), rjust()

```python
text = "Python"

# Zentrieren
print(text.center(20))       # "       Python       "
print(text.center(20, "-"))  # "-------Python-------"

# Linksbündig
print(text.ljust(20))        # "Python              "
print(text.ljust(20, "."))   # "Python.............."

# Rechtsbündig
print(text.rjust(20))        # "              Python"
print(text.rjust(20, "*"))   # "**************Python"

# Zu kurzer width - keine Änderung
print(text.center(3))        # "Python" (nicht gekürzt!)
```

### zfill()

```python
# Mit Nullen auffüllen (für Zahlen)
number = "42"
print(number.zfill(5))   # 00042

# Funktioniert mit Vorzeichen
number = "-42"
print(number.zfill(5))   # -0042

number = "+42"
print(number.zfill(5))   # +0042

# Praktisch für Dateinamen
for i in range(1, 15):
    filename = f"file_{str(i).zfill(3)}.txt"
    print(filename)
# file_001.txt
# file_002.txt
# ...
# file_015.txt
```

## Prüfungen (is* Methoden)

### isalpha(), isdigit(), isalnum()

```python
# isalpha() - nur Buchstaben?
print("Python".isalpha())      # True
print("Python3".isalpha())     # False
print("".isalpha())            # False

# isdigit() - nur Ziffern?
print("123".isdigit())         # True
print("12.3".isdigit())        # False
print("12a".isdigit())         # False

# isalnum() - Buchstaben oder Ziffern?
print("Python3".isalnum())     # True
print("Python-3".isalnum())    # False (wegen -)
```

### isspace(), isupper(), islower()

```python
# isspace() - nur Whitespace?
print("   ".isspace())         # True
print(" \t\n".isspace())       # True
print("".isspace())            # False
print(" a ".isspace())         # False

# isupper() - alle Großbuchstaben?
print("PYTHON".isupper())      # True
print("Python".isupper())      # False
print("PYTHON3".isupper())     # True (Zahlen ignoriert)

# islower() - alle Kleinbuchstaben?
print("python".islower())      # True
print("Python".islower())      # False
print("python3".islower())     # True
```

### istitle(), isascii(), isprintable()

```python
# istitle() - Title Case?
print("Python Programming".istitle())  # True
print("Python programming".istitle())  # False
print("PYTHON".istitle())              # False

# isascii() - nur ASCII-Zeichen?
print("Python".isascii())              # True
print("Pythön".isascii())              # False
print("Python 🐍".isascii())           # False

# isprintable() - nur druckbare Zeichen?
print("Python".isprintable())          # True
print("Python\n".isprintable())        # False (newline)
print("Python\t".isprintable())        # False (tab)
```

### isdecimal(), isnumeric()

```python
# isdecimal() - Dezimalziffern (0-9)?
print("123".isdecimal())       # True
print("①②③".isdecimal())       # False (Unicode-Ziffern)

# isnumeric() - alle numerischen Zeichen?
print("123".isnumeric())       # True
print("①②③".isnumeric())       # True (Unicode-Ziffern)
print("½".isnumeric())         # True (Bruch)
print("Ⅳ".isnumeric())         # True (Römisch)

# isdigit() liegt dazwischen
print("²".isdigit())           # True (hochgestellt)
print("²".isdecimal())         # False
print("²".isnumeric())         # True
```

### isidentifier()

```python
# isidentifier() - gültiger Python-Identifier?
print("variable".isidentifier())       # True
print("my_var".isidentifier())         # True
print("class".isidentifier())          # True (aber Keyword!)
print("2fast".isidentifier())          # False (beginnt mit Ziffer)
print("my-var".isidentifier())         # False (Minus)

# Mit Keyword-Check kombinieren
import keyword
name = "class"
if name.isidentifier() and not keyword.iskeyword(name):
    print("Gültiger Variablenname")
else:
    print("Kein gültiger Variablenname")  # Wird ausgegeben
```

## Encoding/Decoding

### encode()

```python
text = "Python"

# Zu Bytes encodieren
bytes_data = text.encode()
print(bytes_data)       # b'Python'
print(type(bytes_data)) # <class 'bytes'>

# Verschiedene Encodings
bytes_utf8 = text.encode('utf-8')
bytes_ascii = text.encode('ascii')
bytes_latin1 = text.encode('latin-1')

# Nicht-ASCII Zeichen
text_german = "Grüße"
bytes_data = text_german.encode('utf-8')
print(bytes_data)  # b'Gr\xc3\xbc\xc3\x9fe'

# Fehlerbehandlung
text_emoji = "Python 🐍"
# bytes_ascii = text_emoji.encode('ascii')  # UnicodeEncodeError!
bytes_ignore = text_emoji.encode('ascii', errors='ignore')
print(bytes_ignore)  # b'Python '
```

## Formatierung (alt)

### format()

```python
# format() für String-Formatierung
text = "Hallo {}, du bist {} Jahre alt"
result = text.format("Alice", 25)
print(result)  # Hallo Alice, du bist 25 Jahre alt

# Mit Indices
text = "{0} ist {1} und {0} mag {2}"
print(text.format("Alice", 25, "Python"))
# Alice ist 25 und Alice mag Python

# Mit Named Parameters
text = "Hallo {name}, du bist {age} Jahre alt"
print(text.format(name="Bob", age=30))

# Formatierungs-Spezifikationen
print("{:.2f}".format(3.14159))  # 3.14
print("{:>10}".format("right"))  # "     right"
print("{:0>5}".format("42"))     # "00042"

# Mehr dazu in [[03_Formatierung (f-strings)]]
```

## Praktische Kombinationen

### Text bereinigen

```python
def bereinige_text(text):
    """Vollständige Text-Bereinigung"""
    # Whitespace entfernen
    text = text.strip()
    # Mehrfache Leerzeichen zu einem
    text = " ".join(text.split())
    # Zu Lowercase
    text = text.lower()
    return text

eingabe = "   PYTHON    PROGRAMMING   "
sauber = bereinige_text(eingabe)
print(sauber)  # "python programming"
```

### Slug erstellen

```python
def erstelle_slug(text):
    """Erstellt URL-freundlichen Slug"""
    # Lowercase
    slug = text.lower()
    # Nur alphanumerisch und Leerzeichen
    slug = "".join(c if c.isalnum() or c.isspace() else "" for c in slug)
    # Leerzeichen zu Minus
    slug = "-".join(slug.split())
    return slug

titel = "Python Programming: Ein Tutorial"
slug = erstelle_slug(titel)
print(slug)  # python-programming-ein-tutorial
```

### Wörter zählen

```python
def wort_statistik(text):
    """Zählt Wörter und Zeichen"""
    # Bereinigen
    text_clean = text.strip()
    
    # Wörter
    wörter = text_clean.split()
    
    # Zeichen (ohne Whitespace)
    zeichen_ohne_ws = len(text_clean.replace(" ", ""))
    
    return {
        "wörter": len(wörter),
        "zeichen_total": len(text_clean),
        "zeichen_ohne_whitespace": zeichen_ohne_ws,
        "durchschnitt": len(text_clean) / len(wörter) if wörter else 0
    }

text = "Python ist eine großartige Sprache"
stats = wort_statistik(text)
print(stats)
# {'wörter': 5, 'zeichen_total': 35, 'zeichen_ohne_whitespace': 31, ...}
```

### Zeilen verarbeiten

```python
def verarbeite_datei_zeilen(text):
    """Verarbeitet mehrzeiligen Text"""
    zeilen = text.splitlines()
    
    # Leere Zeilen entfernen
    zeilen = [z.strip() for z in zeilen if z.strip()]
    
    # Nummerieren
    nummeriert = [f"{i+1}. {zeile}" for i, zeile in enumerate(zeilen)]
    
    return "\n".join(nummeriert)

text = """
Erste Zeile

Zweite Zeile
Dritte Zeile

"""

result = verarbeite_datei_zeilen(text)
print(result)
# 1. Erste Zeile
# 2. Zweite Zeile
# 3. Dritte Zeile
```

### Passwort maskieren

```python
def maskiere_passwort(passwort, zeige_letzte=4):
    """Maskiert Passwort außer letzte n Zeichen"""
    if len(passwort) <= zeige_letzte:
        return "*" * len(passwort)
    
    sichtbar = passwort[-zeige_letzte:]
    maskiert = "*" * (len(passwort) - zeige_letzte)
    return maskiert + sichtbar

pw = "SuperSecret123"
print(maskiere_passwort(pw))  # *********t123
```

## Häufige Fallstricke

### Fallstrick 1: Methoden geben neue Strings zurück

```python
text = "Python"

# ❌ Ändert NICHT den originalen String
text.upper()
print(text)  # Python (unverändert!)

# ✅ Neuen String zuweisen
text = text.upper()
print(text)  # PYTHON
```

### Fallstrick 2: split() ohne Argument vs. mit Argument

```python
text = "  a  b  c  "

# Ohne Argument - split bei jedem Whitespace, entfernt leere
print(text.split())      # ['a', 'b', 'c']

# Mit Argument - split bei diesem Zeichen, behält leere
print(text.split(" "))   # ['', '', 'a', '', 'b', '', 'c', '', '']
```

### Fallstrick 3: strip() entfernt nur Ränder

```python
text = "  Python  Programming  "

# strip() entfernt nur Anfang/Ende
print(text.strip())  # "Python  Programming"

# Für Mitte: split() + join()
print(" ".join(text.split()))  # "Python Programming"
```

### Fallstrick 4: Chaining mit None

```python
text = None

# ❌ AttributeError wenn None
# result = text.upper()

# ✅ Sicher prüfen
result = text.upper() if text else ""

# ✅ Oder mit get_default
result = (text or "").upper()
```

### Fallstrick 5: Unicode und Encoding

```python
text = "Café"

# Verschiedene Encodings, verschiedene Längen
print(len(text))                   # 4 (Unicode-Zeichen)
print(len(text.encode('utf-8')))   # 5 (Bytes)
print(len(text.encode('latin-1'))) # 4 (Bytes)

# Vorsicht bei Slicing nach Encoding
bytes_data = text.encode('utf-8')
# print(bytes_data[:3].decode())  # UnicodeDecodeError! (é ist 2 Bytes)
```

## Performance-Hinweise

### Methoden-Performance

| Methode | Komplexität | Notizen |
|---------|-------------|---------|
| `upper()`, `lower()` | O(n) | Erstellt neuen String |
| `strip()` | O(n) | Worst case |
| `split()` | O(n) | Abhängig von Anzahl Splits |
| `join()` | O(n) | n = totale Länge |
| `replace()` | O(n*m) | n = String-Länge, m = Pattern-Länge |
| `find()` | O(n*m) | Worst case |
| `startswith()` | O(m) | m = Pattern-Länge |
| `count()` | O(n*m) | Worst case |

### Performance-Tipps

```python
# ✅ Nutze comprehension statt Loop + concatenation
# Langsam
result = ""
for word in words:
    result += word.upper() + " "

# Schnell
result = " ".join(word.upper() for word in words)

# ✅ Nutze startswith() mit Tuple
# Statt:
if s.startswith("http://") or s.startswith("https://"):
    pass

# Besser:
if s.startswith(("http://", "https://")):
    pass

# ✅ in-Operator ist schnell
if "substring" in large_string:  # Optimiert in C
    pass
```

## Übungsaufgaben

### Übung 1: Text-Analyse (Leicht)

Analysiere einen Text und gib Statistiken zurück.

**Erwarteter Output:**
```python
stats = analysiere_text("Python ist toll!")
# {
#   'wörter': 3,
#   'zeichen': 16,
#   'großbuchstaben': 1,
#   'kleinbuchstaben': 12,
#   'sonderzeichen': 2
# }
```

### Übung 2: Title Case richtig (Mittel)

Implementiere korrektes Title Case (Artikel klein lassen).

**Erwarteter Output:**
```python
print(title_case_smart("the lord of the rings"))
# "The Lord of the Rings"
```

### Übung 3: String-Diff (Mittel)

Zeige Unterschiede zwischen zwei Strings.

**Erwarteter Output:**
```python
diff("Hello", "Hallo")
# Position 1: 'e' → 'a'
# Position 4: 'o' → Added 'o'
```

### Übung 4: Template Engine (Schwer)

Einfache Template Engine mit Variablen und Bedingungen.

**Erwarteter Output:**
```python
template = "Hallo {{name}}, {% if premium %}Premium{% else %}Normal{% endif %}"
result = render(template, {'name': 'Alice', 'premium': True})
# "Hallo Alice, Premium"
```

### Übung 5: Text-Wrapper (Schwer)

Wickle Text bei bestimmter Breite um (beachte Wörter).

**Erwarteter Output:**
```python
text = "Python ist eine großartige Programmiersprache"
wrapped = wrap_text(text, width=20)
# Python ist eine
# großartige
# Programmiersprache
```

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [String Methods](https://docs.python.org/3/library/stdtypes.html#string-methods) - Komplette Methoden-Liste
- [Text Processing Services](https://docs.python.org/3/library/text.html) - Text-Module
- [re Module](https://docs.python.org/3/library/re.html) - Regular Expressions
- [textwrap Module](https://docs.python.org/3/library/textwrap.html) - Text-Wrapping

## Zusammenfassung

### Wichtigste Methoden

1. **Case:**
   - `upper()`, `lower()`, `capitalize()`, `title()`
   - `swapcase()`, `casefold()`

2. **Suche:**
   - `find()`, `index()`, `count()`
   - `startswith()`, `endswith()`

3. **Whitespace:**
   - `strip()`, `lstrip()`, `rstrip()`
   - `split()`, `join()`, `splitlines()`

4. **Ändern:**
   - `replace()`, `translate()`
   - `center()`, `ljust()`, `rjust()`, `zfill()`

5. **Prüfen:**
   - `isalpha()`, `isdigit()`, `isalnum()`
   - `isupper()`, `islower()`, `istitle()`
   - `isspace()`, `isidentifier()`

### Best Practices

- ✅ Methoden verketten für mehrere Operationen
- ✅ `join()` statt `+=` für Konkatenation
- ✅ `startswith()` mit Tuple für mehrere Optionen
- ✅ `casefold()` für internationale Vergleiche
- ⚠️ Alle Methoden geben neue Strings zurück (immutable!)

## Verwandte Themen

- [[01_String Basics|String Basics]] - String-Grundlagen
- [[03_Formatierung (f-strings)|Formatierung]] - f-strings und format()
- [[04_Encoding und Unicode|Encoding]] - Unicode-Handling
- [[05_String Performance|Performance]] - Optimierungen
- [[06_Übungen Strings|Übungen]] - Mehr Praxis

---

← [[01_String Basics|String Basics]] | [[INDEX|📑 Index]] | [[03_Formatierung (f-strings)|Formatierung (f-strings)]] →