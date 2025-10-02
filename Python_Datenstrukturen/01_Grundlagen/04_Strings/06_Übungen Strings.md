---
tags: [python, datenstrukturen, strings, übungen, praxis]
created: 2025-10-01
---

← [[05_String Performance|String Performance]] | [[INDEX|📑 Index]] | [[01_Grundlagen/05_Bytes/01_Bytes und Bytearray|Bytes und Bytearray]] →

# Übungen Strings

> [!tip] Lernziel
> Du festigst dein Wissen über Strings durch praktische Übungen zu allen Aspekten

## Übungsstruktur

Jede Übung hat:
- 📝 **Aufgabe** - Was zu tun ist
- 💡 **Hinweise** - Tipps zur Lösung
- ✅ **Erwarteter Output** - So sollte es aussehen
- 🎯 **Schwierigkeit** - Leicht / Mittel / Schwer

Die Lösungen findest du in [[06_Praxis/04_Lösungen/Lösungen Teil 1|Lösungen Teil 1]].

## String Basics

### Übung 1: Palindrom-Checker 🎯 Leicht

**Aufgabe:**
Prüfe ob ein String ein Palindrom ist (vorwärts = rückwärts). Ignoriere Leerzeichen und Groß-/Kleinschreibung.

**Erwarteter Output:**
```python
print(ist_palindrom("Anna"))                    # True
print(ist_palindrom("A man a plan a canal Panama"))  # True
print(ist_palindrom("Python"))                  # False
print(ist_palindrom("racecar"))                 # True
```

💡 **Hinweise:**
- Entferne Leerzeichen mit `replace()` oder `split()`/`join()`
- Nutze `lower()` für Case-Insensitive
- Vergleiche String mit `[::-1]`

---

### Übung 2: Wörter zählen 🎯 Leicht

**Aufgabe:**
Zähle Wörter, Zeichen, Sätze und durchschnittliche Wortlänge.

**Erwarteter Output:**
```python
text = "Python ist toll. Es ist einfach!"
stats = text_statistik(text)
print(stats)
# {
#   'wörter': 5,
#   'zeichen': 34,
#   'sätze': 2,
#   'durchschnittliche_wortlänge': 4.0
# }
```

💡 **Hinweise:**
- `split()` für Wörter
- `count('.')` für Sätze (vereinfacht)
- `len()` für Zeichen

---

### Übung 3: ROT13 Cipher 🎯 Leicht

**Aufgabe:**
Implementiere ROT13 Verschlüsselung (Caesar Cipher mit Shift 13).

**Erwarteter Output:**
```python
print(rot13("Hello World"))  # Uryyb Jbeyq
print(rot13("Uryyb Jbeyq"))  # Hello World (wieder entschlüsselt)
print(rot13("Python"))       # Clguba
```

💡 **Hinweise:**
- `ord()` und `chr()` für Zeichen-Konvertierung
- ROT13: A→N, B→O, ..., N→A, O→B, ...
- Nur Buchstaben verschlüsseln, Rest beibehalten

---

### Übung 4: Anagramm-Check 🎯 Mittel

**Aufgabe:**
Prüfe ob zwei Strings Anagramme sind (gleiche Buchstaben, andere Reihenfolge).

**Erwarteter Output:**
```python
print(sind_anagramme("listen", "silent"))  # True
print(sind_anagramme("Python", "Java"))    # False
print(sind_anagramme("Astronomer", "Moon starer"))  # True (ignoriere Spaces)
```

💡 **Hinweise:**
- Sortiere Buchstaben mit `sorted()`
- Ignoriere Leerzeichen und Case
- Vergleiche sortierte Listen

---

### Übung 5: String-Kompression 🎯 Mittel

**Aufgabe:**
Komprimiere Strings mit Run-Length Encoding (RLE).

**Erwarteter Output:**
```python
print(komprimiere("aaabbcccc"))      # a3b2c4
print(komprimiere("abcdef"))         # abcdef (keine Kompression)
print(dekomprimiere("a3b2c4"))       # aaabbcccc
print(dekomprimiere("a1b1c1"))       # abc
```

💡 **Hinweise:**
- Iteriere durch String, zähle gleiche Zeichen
- Nur komprimieren wenn sinnvoll (a1 → a)
- Dekompression: Parse Zahl nach Buchstabe

---

## String-Methoden

### Übung 6: Title Case Smart 🎯 Mittel

**Aufgabe:**
Implementiere intelligentes Title Case (kleine Wörter wie "of", "the", "a" bleiben klein).

**Erwarteter Output:**
```python
print(title_case_smart("the lord of the rings"))
# "The Lord of the Rings"

print(title_case_smart("a tale of two cities"))
# "A Tale of Two Cities"
```

💡 **Hinweise:**
- Liste kleiner Wörter: `["of", "the", "a", "an", "and", "or", "but"]`
- Erstes und letztes Wort immer groß
- `split()` und `join()`

---

### Übung 7: Zensur-Funktion 🎯 Mittel

**Aufgabe:**
Ersetze Wörter aus einer Blacklist mit Sternchen (gleiche Länge).

**Erwarteter Output:**
```python
text = "Das ist ein böser Test"
blacklist = ["böser", "Test"]
print(zensiere(text, blacklist))
# "Das ist ein ***** ****"

# Case-insensitive
print(zensiere("BÖSER Test", blacklist, case_sensitive=False))
# "***** ****"
```

💡 **Hinweise:**
- Wort-Grenzen beachten (nicht Teilstrings ersetzen)
- `len(wort) * "*"` für Sterne
- Optional: `lower()` für Case-Insensitive

---

### Übung 8: String-Diff 🎯 Schwer

**Aufgabe:**
Zeige Unterschiede zwischen zwei Strings an.

**Erwarteter Output:**
```python
diff("Hello", "Hallo")
# Position 1: 'e' → 'a'
# Position 4: 'o' → deleted
# Position 4: Added 'o'

diff("Python", "Python")
# Keine Unterschiede
```

💡 **Hinweise:**
- Iteriere parallel durch beide Strings
- Nutze `enumerate()` und `zip_longest()`
- Behandle unterschiedliche Längen

---

### Übung 9: CSV Parser 🎯 Schwer

**Aufgabe:**
Parse einfache CSV (ohne Library) mit Quote-Handling.

**Erwarteter Output:**
```python
csv = 'Name,Age,City\nAlice,30,"New York"\nBob,25,"Los Angeles, CA"'
data = parse_csv(csv)
print(data)
# [
#   ['Name', 'Age', 'City'],
#   ['Alice', '30', 'New York'],
#   ['Bob', '25', 'Los Angeles, CA']
# ]
```

💡 **Hinweise:**
- `splitlines()` für Zeilen
- Behandle Quotes (Komma in Quotes ignorieren)
- State-Machine oder Zeichen-für-Zeichen-Parsing

---

### Übung 10: Markdown zu HTML 🎯 Schwer

**Aufgabe:**
Konvertiere einfaches Markdown zu HTML.

**Erwarteter Output:**
```python
markdown = """
# Überschrift
Dies ist **fett** und *kursiv*.
- Punkt 1
- Punkt 2
"""

html = markdown_to_html(markdown)
# <h1>Überschrift</h1>
# <p>Dies ist <strong>fett</strong> und <em>kursiv</em>.</p>
# <ul>
# <li>Punkt 1</li>
# <li>Punkt 2</li>
# </ul>
```

💡 **Hinweise:**
- Zeile für Zeile verarbeiten
- Regex oder String-Methoden
- Behandle: #, **, *, -, Links

---

## Formatierung

### Übung 11: Tabellenformatierer 🎯 Mittel

**Aufgabe:**
Formatiere Daten als ASCII-Tabelle mit automatischer Spaltenbreite.

**Erwarteter Output:**
```python
data = [
    ["Name", "Alter", "Stadt"],
    ["Alice", "30", "Berlin"],
    ["Bob", "25", "Hamburg"]
]

print(formatiere_tabelle(data))
# +-------+-------+----------+
# | Name  | Alter | Stadt    |
# +-------+-------+----------+
# | Alice | 30    | Berlin   |
# | Bob   | 25    | Hamburg  |
# +-------+-------+----------+
```

💡 **Hinweise:**
- Berechne max. Breite jeder Spalte
- Nutze f-strings mit Alignment
- Trennlinien mit `+` und `-`

---

### Übung 12: Geld-Formatierer 🎯 Mittel

**Aufgabe:**
Formatiere Geldbeträge für verschiedene Länder.

**Erwarteter Output:**
```python
print(format_geld(1234567.89, "DE"))  # 1.234.567,89 €
print(format_geld(1234567.89, "US"))  # $1,234,567.89
print(format_geld(1234567.89, "JP"))  # ¥1,234,568 (keine Dezimalen)
```

💡 **Hinweise:**
- Dictionary für Formate pro Land
- Tausender-Trennzeichen unterschiedlich
- Runden für JP (keine Cents)

---

### Übung 13: Log-Formatter 🎯 Schwer

**Aufgabe:**
Formatiere strukturierte Logs mit Farben und Timestamp.

**Erwarteter Output:**
```python
logger = Logger()
logger.info("Server started", port=8080)
logger.error("Connection failed", host="db.example.com", error="Timeout")

# [2025-10-01 14:30:45] INFO  | Server started | port=8080
# [2025-10-01 14:30:46] ERROR | Connection failed | host=db.example.com error=Timeout
```

💡 **Hinweise:**
- `datetime` für Timestamp
- ANSI-Codes für Farben
- **kwargs für extra Felder
- Format: `{farbe}[{timestamp}] {level:5}{reset} | {message}`

---

## Encoding & Unicode

### Übung 14: Unicode-Analyzer 🎯 Leicht

**Aufgabe:**
Analysiere Unicode-Zeichen in einem String.

**Erwarteter Output:**
```python
analyze_unicode("Café 🐍")
# Character: C | U+0043 | Category: Lu | Name: LATIN CAPITAL LETTER C
# Character: a | U+0061 | Category: Ll | Name: LATIN SMALL LETTER A
# Character: f | U+0066 | Category: Ll | Name: LATIN SMALL LETTER F
# Character: é | U+00E9 | Category: Ll | Name: LATIN SMALL LETTER E WITH ACUTE
# Character:   | U+0020 | Category: Zs | Name: SPACE
# Character: 🐍 | U+1F40D | Category: So | Name: SNAKE
```

💡 **Hinweise:**
- `unicodedata.name()`, `unicodedata.category()`
- `ord()` für Code Point
- Format: `U+{ord:04X}`

---

### Übung 15: Encoding-Konverter 🎯 Mittel

**Aufgabe:**
Konvertiere Datei von einem Encoding zu einem anderen.

**Erwarteter Output:**
```python
konvertiere_encoding(
    input_file='latin1.txt',
    output_file='utf8.txt',
    from_encoding='latin-1',
    to_encoding='utf-8'
)
# Datei erfolgreich konvertiert: 1234 Zeichen
```

💡 **Hinweise:**
- Lese mit `encoding=from_encoding`
- Schreibe mit `encoding=to_encoding`
- Error-Handling für Encoding-Fehler

---

### Übung 16: Slugify International 🎯 Schwer

**Aufgabe:**
Erstelle URL-Slugs mit Transliteration für verschiedene Sprachen.

**Erwarteter Output:**
```python
print(slugify("Café Paris"))           # cafe-paris
print(slugify("Москва"))               # moskva (Transliteration)
print(slugify("東京"))                 # dong-jing (Transliteration)
print(slugify("São Paulo"))            # sao-paulo
```

💡 **Hinweise:**
- `unicodedata.normalize('NFKD', text)`
- `unidecode` Library für Transliteration (oder eigene Mapping)
- ASCII-safe machen

---

## Performance

### Übung 17: String-Builder 🎯 Mittel

**Aufgabe:**
Implementiere effiziente StringBuilder-Klasse.

**Erwarteter Output:**
```python
builder = StringBuilder()
for i in range(10000):
    builder.append(str(i))
    builder.append(",")

result = str(builder)  # Nur einmal join()
# Sollte deutlich schneller sein als += in Loop
```

💡 **Hinweise:**
- Liste intern für Teile
- `append()` fügt zu Liste hinzu
- `__str__()` joined Liste

---

### Übung 18: Performance-Benchmark 🎯 Schwer

**Aufgabe:**
Erstelle Benchmark-Suite für String-Operationen.

**Erwarteter Output:**
```python
benchmarks = StringBenchmarks()
results = benchmarks.run_all()

# Operation              Time        Speedup
# ========================================
# concat_plus           0.1234s     1.0x
# concat_join           0.0123s     10.0x
# format_percent        0.0456s     2.7x
# format_format         0.0389s     3.2x
# format_fstring        0.0234s     5.3x
```

💡 **Hinweise:**
- `timeit` für Zeitmessungen
- Verschiedene Operationen testen
- Relative Performance berechnen

---

### Übung 19: Memory-Efficient Text Processor 🎯 Schwer

**Aufgabe:**
Verarbeite große Textdateien Zeile für Zeile ohne alles in RAM zu laden.

**Erwarteter Output:**
```python
processor = TextProcessor('large_file.txt')

for line in processor.process_lines(
    strip_whitespace=True,
    lowercase=True,
    remove_empty=True
):
    print(line)

# Memory Usage: < 10 MB (auch bei GB-Dateien)
```

💡 **Hinweise:**
- Generator-Funktion
- `yield` für Zeile
- Nie komplette Datei in Speicher laden

---

## Kombinierte Übungen

### Übung 20: Text-Analyzer 🎯 Schwer

**Aufgabe:**
Umfassender Text-Analyzer mit vielen Statistiken.

**Erwarteter Output:**
```python
text = "Python ist eine großartige Programmiersprache. Python ist einfach!"

stats = analyze_text(text)
# {
#   'zeichen': 65,
#   'zeichen_ohne_leerzeichen': 54,
#   'wörter': 7,
#   'eindeutige_wörter': 6,
#   'sätze': 2,
#   'durchschnittliche_wortlänge': 7.71,
#   'längster_satz': 43,
#   'häufigste_wörter': [('python', 2), ('ist', 2), ...],
#   'großbuchstaben': 2,
#   'kleinbuchstaben': 52
# }
```

💡 **Hinweise:**
- Kombiniere verschiedene String-Methoden
- Collections Counter für Häufigkeit
- Sätze mit `.!?` trennen

---

### Übung 21: Template Engine 🎯 Schwer

**Aufgabe:**
Einfache Template Engine mit Variablen, Schleifen und Bedingungen.

**Erwarteter Output:**
```python
template = """
<h1>{{ title }}</h1>
{% for item in items %}
  <p>{{ item.name }}: {{ item.price }}€</p>
{% endfor %}
{% if show_total %}
  <p>Total: {{ total }}€</p>
{% endif %}
"""

data = {
    'title': 'Shop',
    'items': [
        {'name': 'A', 'price': 10},
        {'name': 'B', 'price': 20}
    ],
    'show_total': True,
    'total': 30
}

html = render_template(template, data)
```

💡 **Hinweise:**
- Parse Zeile für Zeile
- {{ var }} für Variablen
- {% for %} und {% if %} für Logik
- Stack für verschachtelte Blöcke

---

### Übung 22: SQL Query Builder 🎯 Schwer

**Aufgabe:**
Fluent Interface für SQL Query Building.

**Erwarteter Output:**
```python
query = (Query('users')
    .select('name', 'email')
    .where('age', '>', 18)
    .where('active', '=', True)
    .order_by('name')
    .limit(10))

print(query.to_sql())
# SELECT name, email FROM users
# WHERE age > 18 AND active = true
# ORDER BY name
# LIMIT 10
```

💡 **Hinweise:**
- Methoden returnen `self` für Chaining
- Sammle Bedingungen in Listen
- `to_sql()` baut finalen String

---

### Übung 23: Regex-Tester 🎯 Schwer

**Aufgabe:**
Interaktiver Regex-Tester mit Highlighting.

**Erwarteter Output:**
```python
tester = RegexTester(r'\d+')
result = tester.test("Ich habe 42 Äpfel und 17 Birnen")

print(result.matches)  # ['42', '17']
print(result.highlighted)
# "Ich habe [42] Äpfel und [17] Birnen"

print(result.groups)  # Wenn capturing groups
```

💡 **Hinweise:**
- `re.finditer()` für Matches
- Spans für Positionen
- Highlighting mit Markers einfügen

---

### Übung 24: Code Formatter 🎯 Schwer

**Aufgabe:**
Einfacher Python Code Formatter (Basis-Funktionalität).

**Erwarteter Output:**
```python
code = "def foo(x,y):return x+y"

formatted = format_code(code)
# def foo(x, y):
#     return x + y
```

💡 **Hinweise:**
- Tokenize mit einfachem Parsing
- Füge Spaces nach Kommas ein
- Indentation bei Blöcken
- Sehr vereinfacht, kein vollständiger Formatter!

---

### Übung 25: Spell Checker 🎯 Schwer

**Aufgabe:**
Einfacher Spell Checker mit Edit Distance.

**Erwarteter Output:**
```python
checker = SpellChecker(['python', 'java', 'javascript'])

print(checker.correct('pyton'))      # python (1 Edit)
print(checker.correct('jva'))        # java (1 Edit)
print(checker.correct('xyz'))        # xyz (kein Match)

print(checker.suggestions('pythn'))  # ['python']
```

💡 **Hinweise:**
- Levenshtein Distance berechnen
- Edit Distance: Insertions, Deletions, Substitutions
- Sortiere nach Distance

---

## Bonus-Herausforderungen 🔥

### Challenge 1: Brainfuck Interpreter

Interpretiere Brainfuck-Programme (esoterische Programmiersprache).

```python
code = "++++++++[>++++[>++>+++>+++>+<<<<-]>+>+>->>+[<]<-]>>.>---.+++++++..+++.>>.<-.<.+++.------.--------.>>+.>++."
interpret_brainfuck(code)  # Hello World!
```

### Challenge 2: Markov-Chain Text Generator

Generiere Text basierend auf Markov Chains.

```python
generator = MarkovGenerator(text, order=2)
generated = generator.generate(words=50)
# Erzeugt Text der dem Eingabe-Stil ähnelt
```

### Challenge 3: Diff Algorithm

Implementiere Longest Common Subsequence (LCS) für Text-Diff.

```python
diff = text_diff(
    "The quick brown fox",
    "The slow brown dog"
)
# - quick
# + slow
# - fox
# + dog
```

## Tipps für alle Übungen

### Testing

```python
def test_palindrom():
    assert ist_palindrom("anna") == True
    assert ist_palindrom("python") == False
    assert ist_palindrom("A man a plan a canal Panama") == True
    print("✅ Alle Tests bestanden!")

test_palindrom()
```

### Debugging

```python
def debug_string_operation(text):
    print(f"Input: '{text}'")
    print(f"Länge: {len(text)}")
    print(f"Repr: {repr(text)}")
    print(f"Bytes: {text.encode('utf-8')}")
```

### Performance Messen

```python
import time

def measure_performance(func, *args, **kwargs):
    start = time.perf_counter()
    result = func(*args, **kwargs)
    duration = time.perf_counter() - start
    print(f"{func.__name__}: {duration:.4f}s")
    return result
```

## Weiterführende Ideen

Nach diesen Übungen kannst du:

1. **Text-Editor** mit Syntax-Highlighting
2. **Markdown-Renderer** vollständig
3. **JSON-Parser** von Grund auf
4. **Chat-Bot** mit Pattern-Matching
5. **Code-Analyzer** für Python
6. **Templating-System** wie Jinja2
7. **CSV/XML-Parser** robust
8. **Text-Kompressor** verschiedene Algorithmen

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [String Methods](https://docs.python.org/3/library/stdtypes.html#string-methods)
- [Text Processing Services](https://docs.python.org/3/library/text.html)
- [re Module](https://docs.python.org/3/library/re.html)
- [unicodedata Module](https://docs.python.org/3/library/unicodedata.html)

## Verwandte Themen

- [[01_String Basics|String Basics]] - String-Grundlagen
- [[02_String Methoden|String Methoden]] - String-Operationen
- [[03_Formatierung (f-strings)|Formatierung]] - String-Formatierung
- [[04_Encoding und Unicode|Encoding]] - Unicode-Handling
- [[05_String Performance|Performance]] - Optimierungen
- [[06_Praxis/04_Lösungen/Lösungen Teil 1|Lösungen]] - Musterlösungen

---

← [[05_String Performance|String Performance]] | [[INDEX|📑 Index]] | [[01_Grundlagen/05_Bytes/01_Bytes und Bytearray|Bytes und Bytearray]] →