---
tags: [python, datenstrukturen, wahrheitswerte, truthy, falsy, grundlagen]
created: 2025-10-01
---

← [[02_None Type|None Type]] | [[INDEX|📑 Index]] | [[04_Übungen Bool und None|Übungen Bool und None]] →

# Wahrheitswerte

> [!tip] Lernziel
> Du verstehst das Konzept von Truthy und Falsy, wie Python Objekte in Boolean-Kontext evaluiert und wie du eigene Wahrheitswerte definierst

## Truthy und Falsy

In Python kann **jedes Objekt** in einem Boolean-Kontext (z.B. `if`, `while`) verwendet werden. Objekte sind entweder **truthy** (wahr) oder **falsy** (falsch).

```python
# Boolean-Kontext: if-Statement
if "Hallo":  # String ist truthy
    print("Wird ausgegeben")

if []:  # Leere Liste ist falsy
    print("Wird NICHT ausgegeben")

# Explizite Konvertierung zu bool
print(bool("Hallo"))  # True
print(bool([]))       # False
```

## Falsy-Werte

Nur wenige Werte sind **falsy** in Python:

```python
# Alle falsy-Werte:
print(bool(False))          # False - Boolean False
print(bool(None))           # False - None
print(bool(0))              # False - Integer Null
print(bool(0.0))            # False - Float Null
print(bool(0j))             # False - Complex Null
print(bool(Decimal('0')))   # False - Decimal Null
print(bool(Fraction(0, 1))) # False - Fraction Null

# Leere Sequenzen
print(bool(""))             # False - Leerer String
print(bool([]))             # False - Leere Liste
print(bool(()))             # False - Leeres Tuple
print(bool(range(0)))       # False - Leere Range
print(bool(b""))            # False - Leere Bytes

# Leere Collections
print(bool({}))             # False - Leeres Dict
print(bool(set()))          # False - Leeres Set
print(bool(frozenset()))    # False - Leeres Frozenset

# ALLE anderen Objekte sind truthy!
```

## Truthy-Werte

**Alle anderen Werte** sind truthy:

```python
# Zahlen (außer 0)
print(bool(1))              # True
print(bool(-1))             # True
print(bool(3.14))           # True
print(bool(1+2j))           # True

# Nicht-leere Sequenzen
print(bool("a"))            # True
print(bool([1]))            # True
print(bool([0]))            # True! (Liste nicht leer)
print(bool([False]))        # True! (Liste nicht leer)
print(bool((None,)))        # True! (Tuple nicht leer)

# Nicht-leere Collections
print(bool({1}))            # True
print(bool({"a": None}))    # True

# Objekte
class MeinObjekt:
    pass

obj = MeinObjekt()
print(bool(obj))            # True (Standard)
```

## Wahrheitswert-Tests

### In Bedingungen

```python
# Idiomatischer Python-Code
name = input("Name: ")

# ✅ Pythonic
if name:
    print(f"Hallo {name}!")

# ❌ Nicht idiomatisch
if name != "":
    print(f"Hallo {name}!")

# ✅ Liste prüfen
zahlen = [1, 2, 3]
if zahlen:
    print(f"Erste Zahl: {zahlen[0]}")

# ❌ Umständlich
if len(zahlen) > 0:
    print(f"Erste Zahl: {zahlen[0]}")
```

### Mit bool()

```python
# Explizite Konvertierung
wert = "Text"
ist_truthy = bool(wert)
print(ist_truthy)  # True

# In Ausdrücken
hat_daten = bool([1, 2, 3])
print(hat_daten)  # True

# Listen-Comprehension mit bool
werte = [0, 1, "", "text", [], [1], None, False, True]
truthy_werte = [x for x in werte if bool(x)]
print(truthy_werte)  # [1, 'text', [1], True]
```

## Logische Operatoren und Rückgabewerte

**Wichtig:** `and` und `or` geben **nicht immer Boolean** zurück, sondern einen der Operanden!

### and-Operator

```python
# and gibt den ersten falsy-Wert zurück, sonst den letzten Wert
print(True and True)        # True
print(True and False)       # False
print(False and True)       # False

# Mit anderen Werten
print(5 and 10)             # 10 (beide truthy → letzter Wert)
print(0 and 10)             # 0 (erster ist falsy)
print(10 and 0)             # 0 (zweiter ist falsy)
print("" and "text")        # "" (erster ist falsy)
print("text" and "")        # "" (zweiter ist falsy)
print("a" and "b" and "c")  # "c" (alle truthy → letzter)

# Praktisches Beispiel
user = {"name": "Alice"}
name = user and user.get("name")  # "Alice"
print(name)

user = None
name = user and user.get("name")  # None (nicht AttributeError!)
print(name)
```

### or-Operator

```python
# or gibt den ersten truthy-Wert zurück, sonst den letzten Wert
print(True or False)        # True
print(False or True)        # True
print(False or False)       # False

# Mit anderen Werten
print(5 or 10)              # 5 (erster ist truthy)
print(0 or 10)              # 10 (erster ist falsy)
print("" or "text")         # "text" (erster ist falsy)
print("text" or "")         # "text" (erster ist truthy)
print(None or 0 or "")      # "" (alle falsy → letzter)
print(None or 0 or "text")  # "text" (erster truthy)

# Default-Werte
name = input("Name: ") or "Gast"
print(f"Willkommen {name}!")

# Verkettung für erstes truthy
ergebnis = None or "" or 0 or "Gefunden!" or "Ende"
print(ergebnis)  # "Gefunden!"
```

### Kurzschluss-Auswertung

```python
# and stoppt bei erstem falsy
def teuer1():
    print("teuer1() aufgerufen")
    return True

def teuer2():
    print("teuer2() aufgerufen")
    return True

# False and X → X wird NICHT ausgewertet
print(False and teuer1())  # False (teuer1 wird nicht aufgerufen)

# True and X → X wird ausgewertet
print(True and teuer1())   # teuer1 wird aufgerufen, dann True

# or stoppt bei erstem truthy
print(True or teuer2())    # True (teuer2 wird nicht aufgerufen)
print(False or teuer2())   # teuer2 wird aufgerufen, dann True

# Praktisch für sichere Zugriffe
data = None
# wert = data.get("key")  # AttributeError!
wert = data and data.get("key")  # None (kein Error)
```

## Eigene Wahrheitswerte definieren

### Mit __bool__()

```python
class Punktestand:
    def __init__(self, punkte):
        self.punkte = punkte
    
    def __bool__(self):
        """Truthy wenn Punkte > 0"""
        return self.punkte > 0

# Verwendung
score1 = Punktestand(100)
score2 = Punktestand(0)
score3 = Punktestand(-10)

print(bool(score1))  # True
print(bool(score2))  # False
print(bool(score3))  # False

if score1:
    print("Hat Punkte!")  # Wird ausgegeben

if not score2:
    print("Keine Punkte!")  # Wird ausgegeben
```

### Mit __len__()

```python
class MeineContainer:
    def __init__(self, elemente):
        self.elemente = elemente
    
    def __len__(self):
        """Länge zurückgeben"""
        return len(self.elemente)
    
    # Kein __bool__() definiert
    # Python nutzt automatisch len() > 0 als Wahrheitswert!

container1 = MeineContainer([1, 2, 3])
container2 = MeineContainer([])

print(bool(container1))  # True (len > 0)
print(bool(container2))  # False (len == 0)

if container1:
    print("Container nicht leer!")
```

### __bool__() vs. __len__()

```python
class SmartContainer:
    def __init__(self, elemente):
        self.elemente = elemente
    
    def __len__(self):
        return len(self.elemente)
    
    def __bool__(self):
        # __bool__() hat Vorrang vor __len__()
        # Custom-Logik: truthy wenn mindestens 2 Elemente
        return len(self.elemente) >= 2

container1 = SmartContainer([1])
container2 = SmartContainer([1, 2])

print(len(container1))   # 1
print(bool(container1))  # False (< 2 Elemente)

print(len(container2))   # 2
print(bool(container2))  # True (>= 2 Elemente)
```

## Praktische Anwendungen

### Anwendung 1: Default-Werte

```python
def begrüße(name=None, titel=None):
    # Nutze or für Defaults
    name = name or "Gast"
    titel = titel or "Herr/Frau"
    
    return f"{titel} {name}"

print(begrüße())                    # Herr/Frau Gast
print(begrüße("Alice"))             # Herr/Frau Alice
print(begrüße("Bob", "Dr."))        # Dr. Bob

# Vorsicht bei 0 oder False als gültige Werte!
def setze_port(port=None):
    # ❌ FALSCH: 0 ist gültiger Port!
    port = port or 8080
    return port

print(setze_port(0))     # 8080 (falsch!)
print(setze_port(3000))  # 3000

# ✅ RICHTIG: Explizit auf None prüfen
def setze_port_richtig(port=None):
    if port is None:
        port = 8080
    return port

print(setze_port_richtig(0))     # 0 (korrekt!)
print(setze_port_richtig(3000))  # 3000
```

### Anwendung 2: Sichere Navigation

```python
# Sichere Navigation durch verschachtelte Strukturen
data = {
    'user': {
        'profile': {
            'name': 'Alice'
        }
    }
}

# ❌ Gefährlich
# name = data['user']['settings']['theme']  # KeyError!

# ✅ Mit get() und and
name = data.get('user') and \
       data['user'].get('profile') and \
       data['user']['profile'].get('name')
print(name)  # Alice

# Nicht vorhanden
theme = data.get('user') and \
        data['user'].get('settings') and \
        data['user']['settings'].get('theme')
print(theme)  # None

# ✅ Moderne Lösung: try/except oder Optional Chaining (mit Library)
```

### Anwendung 3: Validierungs-Ketten

```python
def validiere_benutzer(username, password, email):
    """Gibt erste Fehlermeldung oder True zurück"""
    # Alle Bedingungen prüfen, erste falsy ist Fehler
    return (
        username and len(username) >= 3 or "Username zu kurz" and
        password and len(password) >= 8 or "Passwort zu kurz" and
        email and "@" in email or "Email ungültig" and
        True
    )

# Tests
print(validiere_benutzer("Al", "12345678", "al@test.com"))
# Username zu kurz

print(validiere_benutzer("Alice", "123", "alice@test.com"))
# Passwort zu kurz

print(validiere_benutzer("Alice", "12345678", "alice"))
# Email ungültig

print(validiere_benutzer("Alice", "12345678", "alice@test.com"))
# True
```

### Anwendung 4: Filter und Bereinigung

```python
# Entferne alle falsy-Werte aus Liste
werte = [0, 1, False, True, "", "text", None, [], [1], {}, {"a": 1}]

# Mit filter()
truthy = list(filter(None, werte))
print(truthy)  # [1, True, 'text', [1], {'a': 1}]

# Mit List Comprehension
truthy2 = [x for x in werte if x]
print(truthy2)  # [1, True, 'text', [1], {'a': 1}]

# Nur bestimmte falsy-Werte entfernen
def nicht_none(x):
    return x is not None

ohne_none = list(filter(nicht_none, werte))
print(ohne_none)  # [0, 1, False, True, '', 'text', [], [1], {}, {'a': 1}]
```

### Anwendung 5: Lazy Evaluation

```python
class LazyValue:
    def __init__(self, compute_func):
        self.compute_func = compute_func
        self._value = None
        self._computed = False
    
    def __bool__(self):
        """Nur truthy wenn Wert berechnet UND truthy"""
        if not self._computed:
            self._value = self.compute_func()
            self._computed = True
        return bool(self._value)
    
    def get(self):
        if not self._computed:
            self._value = self.compute_func()
            self._computed = True
        return self._value

# Teure Berechnung
def teure_berechnung():
    print("Berechne...")
    return [1, 2, 3]

lazy = LazyValue(teure_berechnung)

# Noch nicht berechnet
print("Vor if")

# Jetzt wird berechnet
if lazy:
    print("Hat Wert!")  # Ausgabe: Berechne..., dann Hat Wert!

# Nicht nochmal berechnet
if lazy:
    print("Immer noch Wert!")  # Ausgabe: Immer noch Wert!
```

## All() und Any()

### all() - Alle müssen truthy sein

```python
# all() gibt True wenn ALLE Elemente truthy sind
print(all([True, True, True]))      # True
print(all([True, False, True]))     # False
print(all([1, 2, 3]))               # True (alle truthy)
print(all([1, 0, 3]))               # False (0 ist falsy)
print(all([]))                      # True! (leere Sequenz)

# Mit Generator Expression
zahlen = [2, 4, 6, 8]
alle_gerade = all(x % 2 == 0 for x in zahlen)
print(alle_gerade)  # True

# Praktisches Beispiel
def alle_felder_ausgefüllt(formular):
    return all([
        formular.get('name'),
        formular.get('email'),
        formular.get('password')
    ])

formular1 = {'name': 'Alice', 'email': 'a@b.c', 'password': '123'}
formular2 = {'name': 'Bob', 'email': '', 'password': '456'}

print(alle_felder_ausgefüllt(formular1))  # True
print(alle_felder_ausgefüllt(formular2))  # False (email leer)
```

### any() - Mindestens einer muss truthy sein

```python
# any() gibt True wenn MINDESTENS EIN Element truthy ist
print(any([False, False, False]))   # False
print(any([False, True, False]))    # True
print(any([0, 0, 1]))               # True
print(any([0, "", None]))           # False (alle falsy)
print(any([]))                      # False! (leere Sequenz)

# Mit Generator Expression
zahlen = [1, 3, 5, 8]
hat_gerade = any(x % 2 == 0 for x in zahlen)
print(hat_gerade)  # True (8 ist gerade)

# Praktisches Beispiel
def hat_fehler(validierungen):
    return any([
        validierungen.get('username_error'),
        validierungen.get('email_error'),
        validierungen.get('password_error')
    ])

val1 = {'username_error': None, 'email_error': None, 'password_error': None}
val2 = {'username_error': None, 'email_error': 'Ungültig', 'password_error': None}

print(hat_fehler(val1))  # False
print(hat_fehler(val2))  # True
```

### Kombinieren von all() und any()

```python
# Komplexe Validierung
bestellungen = [
    {'produkte': [1, 2], 'bezahlt': True, 'versendet': False},
    {'produkte': [3], 'bezahlt': True, 'versendet': True},
    {'produkte': [4, 5, 6], 'bezahlt': False, 'versendet': False}
]

# Alle Bestellungen haben Produkte
alle_haben_produkte = all(bool(b['produkte']) for b in bestellungen)
print(alle_haben_produkte)  # True

# Mindestens eine Bestellung bezahlt
mindestens_eine_bezahlt = any(b['bezahlt'] for b in bestellungen)
print(mindestens_eine_bezahlt)  # True

# Alle bezahlten Bestellungen versendet
alle_bezahlten_versendet = all(
    b['versendet'] for b in bestellungen if b['bezahlt']
)
print(alle_bezahlten_versendet)  # False
```

## Häufige Fallstricke

### Fallstrick 1: Liste mit False

```python
# ⚠️ Liste mit False-Wert ist truthy!
liste = [False]
if liste:
    print("Liste ist truthy!")  # Wird ausgegeben!

# Richtig prüfen
if liste and liste[0]:
    print("Liste hat truthy Element")
else:
    print("Liste leer oder Element falsy")  # Wird ausgegeben
```

### Fallstrick 2: 0 vs. None

```python
def hole_anzahl():
    return 0  # Gültige Anzahl

anzahl = hole_anzahl()

# ❌ FALSCH
if not anzahl:
    print("Keine Anzahl")  # Wird fälschlich ausgegeben!

# ✅ RICHTIG
if anzahl is None:
    print("Keine Anzahl")
else:
    print(f"Anzahl: {anzahl}")  # Korrekt!
```

### Fallstrick 3: and/or geben nicht Boolean zurück

```python
# and/or geben Operanden zurück, nicht True/False!
x = 5 and 10
print(x)        # 10 (nicht True!)
print(type(x))  # <class 'int'>

y = 0 or "text"
print(y)        # "text" (nicht True!)
print(type(y))  # <class 'str'>

# Wenn du Boolean willst:
x_bool = bool(5 and 10)
print(x_bool)   # True
print(type(x_bool))  # <class 'bool'>
```

### Fallstrick 4: Leere Strings in or

```python
name1 = ""
name2 = "Bob"

# ❌ Problem: Leerer String ist falsy
gewählter_name = name1 or name2
print(gewählter_name)  # Bob

# Was wenn name1 absichtlich leer sein soll?
# ✅ Explizit prüfen
gewählter_name = name1 if name1 is not None else name2
```

### Fallstrick 5: all() mit leerer Liste

```python
# all([]) gibt True zurück!
print(all([]))  # True (per Definition: Alle von Nichts sind wahr)

# any([]) gibt False zurück!
print(any([]))  # False (per Definition: Keins von Nichts ist wahr)

# Kann zu Überraschungen führen
zahlen = []
alle_positiv = all(x > 0 for x in zahlen)
print(alle_positiv)  # True! (keine negativen Zahlen)

# Wenn du leere Liste anders behandeln willst:
if zahlen and all(x > 0 for x in zahlen):
    print("Alle positiv")
else:
    print("Leer oder nicht alle positiv")
```

## Übungsaufgaben

### Übung 1: Truthy/Falsy Tester (Leicht)

Erstelle eine Funktion die Werte testet und kategorisiert.

**Erwarteter Output:**
```python
teste_wert(0)        # "Falsy: Zahl (0)"
teste_wert("")       # "Falsy: String (leer)"
teste_wert([])       # "Falsy: Liste (leer)"
teste_wert([False])  # "Truthy: Liste (nicht leer)"
```

### Übung 2: Smart Default (Mittel)

Implementiere ein System das zwischen None und 0/False unterscheidet.

**Erwarteter Output:**
```python
wert = hole_mit_default(None, default=100)    # 100
wert = hole_mit_default(0, default=100)       # 0 (nicht 100!)
wert = hole_mit_default(False, default=True)  # False (nicht True!)
```

### Übung 3: Validierungs-Chain (Mittel)

Erstelle eine Kette von Validierungen die beim ersten Fehler stoppt.

**Erwarteter Output:**
```python
validator = ValidationChain()
result = (validator
    .check(lambda: len(username) >= 3, "Username zu kurz")
    .check(lambda: "@" in email, "Email ungültig")
    .check(lambda: len(password) >= 8, "Passwort zu kurz")
    .validate())

print(result)  # True oder erste Fehlermeldung
```

### Übung 4: Custom Truthy (Schwer)

Erstelle mehrere Klassen mit verschiedenen Wahrheitswert-Logiken.

**Erwarteter Output:**
```python
# Container: truthy wenn Elemente > Schwellwert
c = ThresholdContainer([1,2,3], threshold=2)
print(bool(c))  # True (3 > 2)

# Zeitfenster: truthy wenn innerhalb des Fensters
t = TimeWindow(start=9, end=17, current=14)
print(bool(t))  # True (14 zwischen 9 und 17)
```

### Übung 5: Lazy All/Any (Schwer)

Implementiere eigene lazy-evaluierte all/any Funktionen mit Logging.

**Erwarteter Output:**
```python
def check(x):
    print(f"Prüfe {x}")
    return x > 5

zahlen = [1, 2, 6, 8, 10]
result = lazy_any(check(x) for x in zahlen)
# Ausgabe:
# Prüfe 1
# Prüfe 2
# Prüfe 6  <- Stoppt hier!
# Result: True
```

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [Truth Value Testing](https://docs.python.org/3/library/stdtypes.html#truth-value-testing) - Offizielle Liste aller falsy-Werte
- [Boolean Operations](https://docs.python.org/3/library/stdtypes.html#boolean-operations-and-or-not) - and, or, not
- [all() and any()](https://docs.python.org/3/library/functions.html#all) - Built-in Funktionen
- [object.__bool__()](https://docs.python.org/3/reference/datamodel.html#object.__bool__) - Eigene Wahrheitswerte definieren

## Zusammenfassung

### Wichtigste Punkte

1. **Falsy-Werte (wenige):**
   - `False`, `None`
   - Zahlen: `0`, `0.0`, `0j`
   - Leere Sequenzen: `""`, `[]`, `()`, `range(0)`
   - Leere Collections: `{}`, `set()`

2. **Truthy-Werte (alles andere):**
   - Alle nicht-leeren Werte
   - Alle Objekte (außer mit __bool__)
   - Sogar `[False]` ist truthy!

3. **and/or Verhalten:**
   - Geben Operanden zurück, nicht True/False
   - Kurzschluss-Auswertung
   - Nützlich für Defaults: `x or default`

4. **all() und any():**
   - `all([])` = True
   - `any([])` = False
   - Lazy evaluation (stoppt früh)

5. **Best Practices:**
   - ✅ `if liste:` statt `if len(liste) > 0:`
   - ✅ `x or default` für Defaults
   - ⚠️ Vorsicht bei 0/False als gültige Werte
   - ✅ Eigene __bool__() für Custom-Logik

## Verwandte Themen

- [[01_Boolean (bool)|Boolean]] - Der bool-Typ
- [[02_None Type|None Type]] - None als falsy-Wert
- [[04_Übungen Bool und None|Übungen]] - Praktische Aufgaben
- [[01_Python Typ-System|Typ-System]] - Typ-Hierarchie

---

← [[02_None Type|None Type]] | [[INDEX|📑 Index]] | [[04_Übungen Bool und None|Übungen Bool und None]] →