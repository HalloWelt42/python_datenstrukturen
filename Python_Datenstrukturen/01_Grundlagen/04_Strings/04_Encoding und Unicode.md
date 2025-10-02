---
tags: [python, datenstrukturen, strings, encoding, unicode, utf8]
created: 2025-10-01
---

← [[03_Formatierung (f-strings)|Formatierung (f-strings)]] | [[INDEX|📑 Index]] | [[05_String Performance|String Performance]] →

# Encoding und Unicode

> [!tip] Lernziel
> Du verstehst Unicode, verschiedene Encodings, und wie Python mit Text-Encoding umgeht

## Unicode Grundlagen

**Unicode** ist ein Standard, der jedem Zeichen eine eindeutige Nummer (Code Point) zuweist. Python 3 Strings sind **Unicode-Strings**.

```python
# Python 3 Strings sind Unicode
text = "Hello 世界 🌍"
print(text)  # Funktioniert mit allen Unicode-Zeichen

# Jedes Zeichen hat einen Code Point
print(ord('A'))        # 65
print(ord('€'))        # 8364
print(ord('🐍'))       # 128013

# Von Code Point zu Zeichen
print(chr(65))         # A
print(chr(8364))       # €
print(chr(128013))     # 🐍

# Unicode Name
import unicodedata
print(unicodedata.name('€'))  # EURO SIGN
print(unicodedata.name('🐍'))  # SNAKE
```

## Encoding vs. Decoding

**Encoding** = Unicode String → Bytes
**Decoding** = Bytes → Unicode String

```python
# String ist Unicode
text = "Hallo Welt"
print(type(text))  # <class 'str'>

# Encode zu Bytes
bytes_utf8 = text.encode('utf-8')
print(bytes_utf8)       # b'Hallo Welt'
print(type(bytes_utf8)) # <class 'bytes'>

# Decode zurück zu String
text_wieder = bytes_utf8.decode('utf-8')
print(text_wieder)      # Hallo Welt
print(type(text_wieder)) # <class 'str'>
```

## UTF-8 Encoding

**UTF-8** ist das Standard-Encoding in Python 3 und im Web.

```python
text = "Café ☕"

# UTF-8 Encoding (variable Länge: 1-4 Bytes pro Zeichen)
utf8_bytes = text.encode('utf-8')
print(utf8_bytes)  # b'Caf\xc3\xa9 \xe2\x98\x95'
print(len(text))       # 6 (6 Unicode-Zeichen)
print(len(utf8_bytes)) # 9 (9 Bytes)

# ASCII: 1 Byte
# é: 2 Bytes (C3 A9)
# ☕: 3 Bytes (E2 98 95)

# UTF-8 kann alle Unicode-Zeichen darstellen
emoji_text = "Python 🐍 ist toll! 🎉"
print(emoji_text.encode('utf-8'))
```

## Andere Encodings

### ASCII

```python
# ASCII (7-bit, nur 0-127)
text = "Hello"
ascii_bytes = text.encode('ascii')
print(ascii_bytes)  # b'Hello'

# Nicht-ASCII Zeichen funktionieren nicht
text_german = "Grüße"
try:
    ascii_bytes = text_german.encode('ascii')
except UnicodeEncodeError as e:
    print(f"Fehler: {e}")
    # 'ascii' codec can't encode character '\xfc'

# Mit Fehlerbehandlung
ascii_bytes = text_german.encode('ascii', errors='ignore')
print(ascii_bytes)  # b'Gre' (ü weggelassen)

ascii_bytes = text_german.encode('ascii', errors='replace')
print(ascii_bytes)  # b'Gr??e' (ü durch ? ersetzt)

ascii_bytes = text_german.encode('ascii', errors='xmlcharrefreplace')
print(ascii_bytes)  # b'Gr&#252;&#223;e' (XML entities)
```

### Latin-1 (ISO-8859-1)

```python
# Latin-1 (8-bit, nur westeuropäische Zeichen)
text = "Café"
latin1_bytes = text.encode('latin-1')
print(latin1_bytes)  # b'Caf\xe9'
print(len(latin1_bytes))  # 4 (jedes Zeichen = 1 Byte)

# Aber keine Emojis oder asiatische Zeichen
emoji_text = "☕"
try:
    emoji_text.encode('latin-1')
except UnicodeEncodeError:
    print("Emoji nicht in Latin-1!")
```

### UTF-16 und UTF-32

```python
text = "Hello 🌍"

# UTF-16 (2 oder 4 Bytes pro Zeichen)
utf16_bytes = text.encode('utf-16')
print(utf16_bytes)
print(len(utf16_bytes))  # Größer als UTF-8

# UTF-32 (immer 4 Bytes pro Zeichen)
utf32_bytes = text.encode('utf-32')
print(utf32_bytes)
print(len(utf32_bytes))  # Am größten

# Vergleich
print(f"UTF-8:  {len(text.encode('utf-8'))} bytes")
print(f"UTF-16: {len(text.encode('utf-16'))} bytes")
print(f"UTF-32: {len(text.encode('utf-32'))} bytes")
```

### Windows-1252 (CP1252)

```python
# Windows Standard-Encoding (Westeuropa)
text = "Café"
windows_bytes = text.encode('cp1252')
print(windows_bytes)  # b'Caf\xe9'

# Ähnlich wie Latin-1 aber mit mehr Zeichen
# Enthält z.B. €, aber keine Emojis
```

## Fehlerbehandlung beim Encoding/Decoding

### Encoding Fehler

```python
text = "Python 🐍"

# errors='strict' (default) - wirft Exception
try:
    text.encode('ascii')
except UnicodeEncodeError as e:
    print(f"Strict: {e}")

# errors='ignore' - überspringt problematische Zeichen
print(text.encode('ascii', errors='ignore'))  # b'Python '

# errors='replace' - ersetzt mit ?
print(text.encode('ascii', errors='replace'))  # b'Python ?'

# errors='xmlcharrefreplace' - XML entities
print(text.encode('ascii', errors='xmlcharrefreplace'))
# b'Python &#128013;'

# errors='backslashreplace' - escape sequences
print(text.encode('ascii', errors='backslashreplace'))
# b'Python \\U0001f40d'

# errors='namereplace' - Unicode Namen
print(text.encode('ascii', errors='namereplace'))
# b'Python \\N{SNAKE}'
```

### Decoding Fehler

```python
# Falsche Bytes
falsche_bytes = b'\xff\xfe'

# errors='strict' (default) - wirft Exception
try:
    falsche_bytes.decode('utf-8')
except UnicodeDecodeError as e:
    print(f"Strict: {e}")

# errors='ignore' - überspringt invalide Bytes
print(falsche_bytes.decode('utf-8', errors='ignore'))  # ''

# errors='replace' - ersetzt mit �
print(falsche_bytes.decode('utf-8', errors='replace'))  # ��

# errors='backslashreplace'
print(falsche_bytes.decode('utf-8', errors='backslashreplace'))
# \\xff\\xfe
```

## Dateien und Encoding

### Dateien lesen/schreiben

```python
# Schreiben mit UTF-8 (empfohlen!)
text = "Café ☕ Python 🐍"
with open('test.txt', 'w', encoding='utf-8') as f:
    f.write(text)

# Lesen mit UTF-8
with open('test.txt', 'r', encoding='utf-8') as f:
    content = f.read()
    print(content)  # Café ☕ Python 🐍

# ⚠️ Ohne encoding (plattformabhängig!)
# Windows: cp1252, Linux/Mac: utf-8
# with open('test.txt', 'w') as f:  # Gefährlich!
#     f.write(text)

# Explizit anderes Encoding
with open('test_latin.txt', 'w', encoding='latin-1') as f:
    f.write("Café")  # Funktioniert (é ist in Latin-1)
```

### Encoding-Erkennung

```python
# Encoding einer Datei erkennen mit chardet
try:
    import chardet
    
    with open('unknown.txt', 'rb') as f:
        raw_data = f.read()
        result = chardet.detect(raw_data)
        encoding = result['encoding']
        confidence = result['confidence']
        
        print(f"Erkanntes Encoding: {encoding} ({confidence*100:.1f}% sicher)")
        
        # Mit erkanntem Encoding lesen
        text = raw_data.decode(encoding)
except ImportError:
    print("chardet nicht installiert: pip install chardet")
```

## BOM (Byte Order Mark)

```python
# BOM in UTF-8 (optional, oft bei Windows)
text = "Hello"

# UTF-8 ohne BOM (standard)
utf8 = text.encode('utf-8')
print(utf8)  # b'Hello'

# UTF-8 mit BOM (utf-8-sig)
utf8_bom = text.encode('utf-8-sig')
print(utf8_bom)  # b'\xef\xbb\xbfHello'

# BOM entfernen beim Lesen
with open('file_with_bom.txt', 'r', encoding='utf-8-sig') as f:
    content = f.read()  # BOM wird automatisch entfernt

# UTF-16 hat immer BOM
utf16 = text.encode('utf-16')
print(utf16)  # b'\xff\xfeH\x00e\x00l\x00l\x00o\x00' (LE)
```

## Unicode Normalisierung

```python
import unicodedata

# Verschiedene Darstellungen des gleichen Zeichens
# Composed (é als ein Zeichen)
text1 = "café"
# Decomposed (e + combining accent)
text2 = "cafe\u0301"

print(text1)  # café
print(text2)  # café (sieht gleich aus!)
print(text1 == text2)  # False (verschiedene Bytes!)

print(len(text1))  # 4
print(len(text2))  # 5 (e + accent = 2 Zeichen)

# Normalisierung
# NFC (Canonical Composition) - kombiniert
nfc1 = unicodedata.normalize('NFC', text1)
nfc2 = unicodedata.normalize('NFC', text2)
print(nfc1 == nfc2)  # True

# NFD (Canonical Decomposition) - trennt
nfd1 = unicodedata.normalize('NFD', text1)
nfd2 = unicodedata.normalize('NFD', text2)
print(nfd1 == nfd2)  # True

# Für Vergleiche: Immer normalisieren!
def unicode_vergleich(s1, s2):
    return unicodedata.normalize('NFC', s1) == unicodedata.normalize('NFC', s2)

print(unicode_vergleich("café", "cafe\u0301"))  # True
```

## Praktische Anwendungen

### Anwendung 1: Slugify für URLs

```python
import unicodedata
import re

def slugify(text):
    """Erstellt URL-sicheren Slug"""
    # Unicode zu ASCII (ähnliche Zeichen)
    text = unicodedata.normalize('NFKD', text)
    text = text.encode('ascii', 'ignore').decode('ascii')
    
    # Lowercase
    text = text.lower()
    
    # Nur alphanumerisch und Spaces
    text = re.sub(r'[^a-z0-9\s-]', '', text)
    
    # Spaces zu Bindestrichen
    text = re.sub(r'[\s]+', '-', text)
    
    # Mehrfache Bindestriche zu einem
    text = re.sub(r'-+', '-', text)
    
    # Trim Bindestriche
    text = text.strip('-')
    
    return text

# Tests
print(slugify("Café Paris"))           # cafe-paris
print(slugify("Über uns & Kontakt"))   # uber-uns-kontakt
print(slugify("São Paulo, Brasil!"))   # sao-paulo-brasil
print(slugify("日本語"))               # '' (keine ASCII-Äquivalente)
```

### Anwendung 2: Text-Bereinigung

```python
def bereinige_text(text):
    """Entfernt unsichtbare und problematische Zeichen"""
    # Verschiedene Whitespace-Zeichen zu normalem Space
    text = ' '.join(text.split())
    
    # Zero-width Zeichen entfernen
    zero_width = [
        '\u200b',  # Zero width space
        '\u200c',  # Zero width non-joiner
        '\u200d',  # Zero width joiner
        '\ufeff',  # Zero width no-break space
    ]
    for char in zero_width:
        text = text.replace(char, '')
    
    # Steuerzeichen entfernen (außer newline/tab)
    text = ''.join(
        char for char in text
        if not unicodedata.category(char).startswith('C')
        or char in '\n\t'
    )
    
    return text

dirty = "Test\u200b\u200c  mit   seltsamen\ufeff   Zeichen"
clean = bereinige_text(dirty)
print(f"'{clean}'")  # 'Test mit seltsamen Zeichen'
```

### Anwendung 3: Email-Validierung (Unicode)

```python
def ist_gültige_email(email):
    """Validiert Email mit Unicode-Support"""
    # Normalisieren
    import unicodedata
    email = unicodedata.normalize('NFC', email)
    
    # Einfache Prüfung
    if '@' not in email:
        return False
    
    local, domain = email.rsplit('@', 1)
    
    # Local part kann Unicode enthalten
    if not local:
        return False
    
    # Domain sollte Punycode sein oder ASCII
    try:
        domain.encode('ascii')
    except UnicodeEncodeError:
        # Internationalized Domain Names (IDN)
        try:
            domain.encode('idna')
        except:
            return False
    
    return True

# Tests
print(ist_gültige_email("user@example.com"))      # True
print(ist_gültige_email("user@münchen.de"))       # True (IDN)
print(ist_gültige_email("用户@例え.jp"))           # True
```

### Anwendung 4: Punycode (IDN)

```python
# Internationalized Domain Names
domain = "münchen.de"

# Zu Punycode (ASCII-safe)
punycode = domain.encode('idna').decode('ascii')
print(punycode)  # xn--mnchen-3ya.de

# Zurück
original = punycode.encode('ascii').decode('idna')
print(original)  # münchen.de

# Vollständige URL
url = "https://www.café.com/über-uns"
# Domain-Teil zu Punycode
parts = url.split('/')
domain_part = parts[2]
punycode_domain = domain_part.encode('idna').decode('ascii')
parts[2] = punycode_domain
safe_url = '/'.join(parts)
print(safe_url)  # https://www.xn--caf-dma.com/über-uns
```

### Anwendung 5: CSV mit verschiedenen Encodings

```python
import csv

def lese_csv_auto_encoding(filename):
    """Liest CSV mit automatischer Encoding-Erkennung"""
    # Probiere verschiedene Encodings
    encodings = ['utf-8', 'utf-8-sig', 'latin-1', 'cp1252']
    
    for encoding in encodings:
        try:
            with open(filename, 'r', encoding=encoding) as f:
                reader = csv.reader(f)
                data = list(reader)
                print(f"Erfolgreich mit {encoding}")
                return data
        except (UnicodeDecodeError, UnicodeError):
            continue
    
    raise ValueError(f"Konnte {filename} mit keinem Encoding lesen")

# Schreibe CSV mit UTF-8 (empfohlen!)
def schreibe_csv_utf8(filename, data):
    """Schreibt CSV mit UTF-8 und BOM für Excel-Kompatibilität"""
    with open(filename, 'w', encoding='utf-8-sig', newline='') as f:
        writer = csv.writer(f)
        writer.writerows(data)
```

## Häufige Fallstricke

### Fallstrick 1: Bytes vs. String verwechseln

```python
# ❌ Bytes und Strings mischen
text = "Hallo"
bytes_data = b"Welt"

try:
    result = text + bytes_data  # TypeError!
except TypeError as e:
    print(f"Fehler: {e}")

# ✅ Erst dekodieren
result = text + bytes_data.decode('utf-8')
print(result)  # HalloWelt
```

### Fallstrick 2: Encoding vergessen bei Dateien

```python
# ❌ Ohne encoding (plattformabhängig!)
# Windows: cp1252, Linux: utf-8
with open('test.txt', 'w') as f:
    f.write("Café")  # Kann auf Windows anders aussehen!

# ✅ Immer encoding angeben
with open('test.txt', 'w', encoding='utf-8') as f:
    f.write("Café")
```

### Fallstrick 3: String-Länge vs. Byte-Länge

```python
text = "🐍"
print(len(text))                    # 1 (ein Unicode-Zeichen)
print(len(text.encode('utf-8')))    # 4 (4 Bytes in UTF-8)

# Für Datenbank CHAR(10) - welche Länge?
text = "Hello🐍🐍🐍"
print(len(text))                    # 8 Zeichen
print(len(text.encode('utf-8')))    # 17 Bytes!
```

### Fallstrick 4: Encoding-Mismatch

```python
# Schreiben mit einem Encoding, Lesen mit anderem
text = "Café"

# Schreiben mit UTF-8
with open('test.txt', 'wb') as f:
    f.write(text.encode('utf-8'))

# ❌ Lesen mit Latin-1
with open('test.txt', 'rb') as f:
    bytes_data = f.read()
    falsch = bytes_data.decode('latin-1')
    print(falsch)  # CafÃ© (Mojibake!)

# ✅ Mit richtigem Encoding
with open('test.txt', 'rb') as f:
    bytes_data = f.read()
    richtig = bytes_data.decode('utf-8')
    print(richtig)  # Café
```

### Fallstrick 5: Unicode Normalisierung vergessen

```python
# Verschiedene Darstellungen
text1 = "Café"        # é als ein Zeichen (U+00E9)
text2 = "Cafe\u0301"  # e + accent (U+0065 + U+0301)

# ❌ Direkter Vergleich schlägt fehl
print(text1 == text2)  # False!

# ✅ Nach Normalisierung
import unicodedata
nfc1 = unicodedata.normalize('NFC', text1)
nfc2 = unicodedata.normalize('NFC', text2)
print(nfc1 == nfc2)  # True
```

## Performance-Hinweise

### Encoding/Decoding Performance

```python
import timeit

text = "Hello World" * 1000

# UTF-8 (am schnellsten für ASCII)
t1 = timeit.timeit(lambda: text.encode('utf-8'), number=10000)
print(f"UTF-8:  {t1:.4f}s")

# Latin-1 (auch schnell)
t2 = timeit.timeit(lambda: text.encode('latin-1'), number=10000)
print(f"Latin1: {t2:.4f}s")

# UTF-16 (langsamer)
t3 = timeit.timeit(lambda: text.encode('utf-16'), number=10000)
print(f"UTF-16: {t3:.4f}s")

# UTF-8 ist meist am schnellsten und flexibelsten
```

### Caching bei wiederholtem Encoding

```python
# ❌ Wiederholtes Encoding
def langsam(text, n):
    for _ in range(n):
        bytes_data = text.encode('utf-8')  # Jedes Mal neu!
        # ... verarbeite bytes_data

# ✅ Einmal encodieren
def schnell(text, n):
    bytes_data = text.encode('utf-8')  # Nur einmal!
    for _ in range(n):
        # ... verarbeite bytes_data
        pass
```

## Übungsaufgaben

### Übung 1: Encoding-Detektor (Leicht)

Erkenne das Encoding einer Datei durch Probieren.

**Erwarteter Output:**
```python
encoding = erkenne_encoding('datei.txt')
print(encoding)  # 'utf-8' oder 'latin-1' etc.
```

### Übung 2: Unicode-Info (Mittel)

Zeige Informationen über Unicode-Zeichen.

**Erwarteter Output:**
```python
info = unicode_info('é')
# {
#   'character': 'é',
#   'codepoint': 'U+00E9',
#   'name': 'LATIN SMALL LETTER E WITH ACUTE',
#   'category': 'Lowercase Letter',
#   'utf8': 'C3 A9'
# }
```

### Übung 3: Encoding-Konverter (Mittel)

Konvertiere Datei von einem Encoding zu anderem.

**Erwarteter Output:**
```python
konvertiere_encoding('input.txt', 'latin-1', 'utf-8', 'output.txt')
# Datei erfolgreich konvertiert
```

### Übung 4: Text-Sanitizer (Schwer)

Bereinige Text von problematischen Unicode-Zeichen.

**Erwarteter Output:**
```python
text = "Test\u200b mit\ufeff Zero-Width"
clean = sanitize_text(text)
# "Test mit Zero-Width"
```

### Übung 5: Multilingual Slug (Schwer)

Erstelle URL-Slugs für verschiedene Sprachen.

**Erwarteter Output:**
```python
print(multilingual_slug("Привет мир"))  # privet-mir (Transliteration)
print(multilingual_slug("こんにちは"))    # konnichiwa (Romanisierung)
print(multilingual_slug("Café Paris"))  # cafe-paris
```

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [Unicode HOWTO](https://docs.python.org/3/howto/unicode.html) - Umfassender Unicode-Guide
- [codecs Module](https://docs.python.org/3/library/codecs.html) - Encoding/Decoding
- [unicodedata Module](https://docs.python.org/3/library/unicodedata.html) - Unicode-Datenbank
- [String encode/decode](https://docs.python.org/3/library/stdtypes.html#str.encode) - Encoding-Methoden

## Zusammenfassung

### Wichtigste Punkte

1. **Unicode Basics:**
   - Python 3 Strings sind Unicode
   - Jedes Zeichen hat Code Point
   - `ord()` und `chr()` für Konvertierung

2. **Encoding:**
   - String → Bytes: `encode()`
   - Bytes → String: `decode()`
   - UTF-8 ist Standard (empfohlen!)

3. **Wichtige Encodings:**
   - UTF-8: Universal, variable Länge
   - ASCII: Nur 0-127
   - Latin-1: Westeuropäisch, 1 Byte
   - UTF-16/32: Mehr Speicher

4. **Fehlerbehandlung:**
   - `errors='strict'`: Exception (default)
   - `errors='ignore'`: Überspringen
   - `errors='replace'`: Ersetzen mit ?/�

5. **Best Practices:**
   - ✅ Immer UTF-8 für Dateien
   - ✅ Encoding explizit angeben
   - ✅ Normalisieren für Vergleiche
   - ✅ Bytes und Strings nicht mischen
   - ⚠️ Länge: Zeichen ≠ Bytes!

## Verwandte Themen

- [[01_String Basics|String Basics]] - String-Grundlagen
- [[02_String Methoden|String Methoden]] - String-Operationen
- [[03_Formatierung (f-strings)|Formatierung]] - String-Formatierung
- [[05_String Performance|Performance]] - Optimierungen
- [[06_Übungen Strings|Übungen]] - Mehr Praxis

---

← [[03_Formatierung (f-strings)|Formatierung (f-strings)]] | [[INDEX|📑 Index]] | [[05_String Performance|String Performance]] →