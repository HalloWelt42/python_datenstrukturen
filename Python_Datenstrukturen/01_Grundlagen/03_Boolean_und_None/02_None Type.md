---
tags: [python, datenstrukturen, boolean, bool, logik, grundlagen]
created: 2025-10-01
---

← [[05_Übungen Numerische Typen|Übungen Numerische Typen]] | [[INDEX|📑 Index]] | [[02_None Type|None Type]] →

# Boolean (bool)

> [!tip] Lernziel
> Du verstehst den Boolean-Typ, seine Besonderheiten als Integer-Subtyp und Wahrheitswerte in Python

## Was ist ein Boolean?

Ein **Boolean** (Wahrheitswert) kann nur zwei Werte annehmen:
- `True` (Wahr)
- `False` (Falsch)

```python
# Boolean-Werte
ist_aktiv = True
ist_fertig = False

print(type(True))   # <class 'bool'>
print(type(False))  # <class 'bool'>

# Vererbung prüfen
print(isinstance(True, bool))  # True
print(isinstance(True, int))   # True! bool erbt von int
```

## Boolean ist ein Integer-Subtyp

**Überraschung:** In Python ist `bool` eine Subklasse von `int`!

```python
# True = 1, False = 0
print(True == 1)    # True
print(False == 0)   # True

# Arithmetik mit Booleans
print(True + True)   # 2
print(True + False)  # 1
print(True * 10)     # 10
print(False * 100)   # 0

# Als Index verwenden
liste = ['erstes', 'zweites']
print(liste[False])  # 'erstes' (Index 0)
print(liste[True])   # 'zweites' (Index 1)

# Summe von Booleans = Anzahl True
werte = [True, False, True, True, False]
print(sum(werte))    # 3 (drei True-Werte)
```

## Boolean erstellen

### Direkte Zuweisung

```python
# Literale
ist_wahr = True
ist_falsch = False

# Vergleichsoperatoren
ist_größer = 5 > 3        # True
ist_gleich = 10 == 10     # True
ist_ungleich = 5 != 3     # True
```

### Mit bool() Konstruktor

```python
# Konvertierung zu Boolean
print(bool(1))      # True
print(bool(0))      # False
print(bool(42))     # True
print(bool(-1))     # True

print(bool("Text")) # True
print(bool(""))     # False

print(bool([1,2]))  # True
print(bool([]))     # False

print(bool(None))   # False
```

## Vergleichsoperatoren

```python
x = 10
y = 20

# Gleichheit
print(x == y)    # False
print(x != y)    # True

# Größer/Kleiner
print(x < y)     # True
print(x <= y)    # True
print(x > y)     # False
print(x >= y)    # False

# Verkettete Vergleiche
z = 15
print(x < z < y)  # True (10 < 15 < 20)
print(x < z > 10) # True (10 < 15 UND 15 > 10)

# Identität
a = [1, 2, 3]
b = [1, 2, 3]
c = a

print(a == b)    # True (gleicher Inhalt)
print(a is b)    # False (verschiedene Objekte)
print(a is c)    # True (gleiche Objekte)

# Membership
liste = [1, 2, 3, 4, 5]
print(3 in liste)     # True
print(10 not in liste) # True
```

## Logische Operatoren

### and, or, not

```python
# AND - beide müssen True sein
print(True and True)    # True
print(True and False)   # False
print(False and False)  # False

# OR - mindestens einer muss True sein
print(True or True)     # True
print(True or False)    # True
print(False or False)   # False

# NOT - invertiert
print(not True)         # False
print(not False)        # True

# Kombinationen
alter = 25
hat_führerschein = True

darf_fahren = alter >= 18 and hat_führerschein
print(darf_fahren)      # True

# Kurzschluss-Auswertung (Short-circuit evaluation)
def teuer():
    print("teuer() wurde aufgerufen")
    return True

# Bei False and X wird X nicht ausgewertet!
print(False and teuer())  # False (teuer() wird NICHT aufgerufen)

# Bei True or X wird X nicht ausgewertet!
print(True or teuer())    # True (teuer() wird NICHT aufgerufen)
```

### Wahrheitstabellen

```python
# AND Wahrheitstabelle
print(f"True  and True  = {True and True}")    # True
print(f"True  and False = {True and False}")   # False
print(f"False and True  = {False and True}")   # False
print(f"False and False = {False and False}")  # False

# OR Wahrheitstabelle  
print(f"True  or True  = {True or True}")      # True
print(f"True  or False = {True or False}")     # True
print(f"False or True  = {False or True}")     # True
print(f"False or False = {False or False}")    # False

# NOT Wahrheitstabelle
print(f"not True  = {not True}")               # False
print(f"not False = {not False}")              # True
```

## Truthy und Falsy Werte

**Nicht nur Booleans haben Wahrheitswerte!** Fast alle Objekte in Python sind entweder "truthy" oder "falsy".

### Falsy-Werte (gelten als False)

```python
# Diese Werte gelten als False:
print(bool(False))      # False
print(bool(None))       # False
print(bool(0))          # False
print(bool(0.0))        # False
print(bool(0j))         # False (komplexe Null)
print(bool(""))         # False (leerer String)
print(bool([]))         # False (leere Liste)
print(bool(()))         # False (leeres Tuple)
print(bool({}))         # False (leeres Dict)
print(bool(set()))      # False (leeres Set)
print(bool(range(0)))   # False (leere Range)

# Alle anderen Werte sind Truthy!
```

### Truthy-Werte (gelten als True)

```python
# Alle nicht-leeren Werte sind truthy:
print(bool(True))       # True
print(bool(1))          # True
print(bool(-1))         # True
print(bool(3.14))       # True
print(bool("Text"))     # True
print(bool([1]))        # True
print(bool([False]))    # True! (Liste ist nicht leer)
print(bool((0,)))       # True! (Tuple ist nicht leer)

# Objekte sind truthy (außer sie definieren __bool__)
class MeinObjekt:
    pass

obj = MeinObjekt()
print(bool(obj))        # True
```

### Praktische Anwendung

```python
# ✅ Idiomatischer Python-Code
name = input("Name: ")
if name:  # Prüft ob String nicht leer
    print(f"Hallo {name}!")
else:
    print("Kein Name eingegeben")

# ❌ Umständlich (nicht idiomatisch)
if name != "":
    print(f"Hallo {name}!")

# ✅ Liste prüfen
zahlen = [1, 2, 3]
if zahlen:  # Prüft ob Liste nicht leer
    print(f"Erste Zahl: {zahlen[0]}")

# ✅ None prüfen
ergebnis = None
if not ergebnis:  # Prüft auf None
    print("Kein Ergebnis")
```

## Boolean in Bedingungen

### if-elif-else

```python
alter = 25

if alter < 18:
    print("Minderjährig")
elif alter < 65:
    print("Erwachsen")
else:
    print("Senior")

# Mehrere Bedingungen
temperatur = 22
ist_sonnig = True

if temperatur > 20 and ist_sonnig:
    print("Perfektes Wetter!")
elif temperatur > 20:
    print("Warm aber bewölkt")
else:
    print("Zu kalt")
```

### Ternärer Operator

```python
# Kurzform für if-else
alter = 20
status = "Volljährig" if alter >= 18 else "Minderjährig"
print(status)  # Volljährig

# Mit Berechnungen
x = 10
y = 20
maximum = x if x > y else y
print(maximum)  # 20

# Verschachtelt (nicht empfohlen!)
note = 85
bewertung = "Sehr gut" if note >= 90 else "Gut" if note >= 80 else "Befriedigend"
print(bewertung)  # Gut
```

### while-Schleife

```python
# Mit Boolean-Variable
läuft = True
zähler = 0

while läuft:
    print(zähler)
    zähler += 1
    if zähler >= 5:
        läuft = False

# Mit Bedingung direkt
x = 0
while x < 5:
    print(x)
    x += 1

# Unendliche Schleife mit break
while True:
    antwort = input("Weiter? (j/n): ")
    if antwort.lower() == 'n':
        break
```

## Boolean-Methoden für Strings

```python
text = "Python"

# Überprüfungen
print(text.isalpha())      # True (nur Buchstaben)
print(text.isdigit())      # False
print(text.isalnum())      # True (alphanumerisch)
print(text.isupper())      # False
print(text.islower())      # False
print(text.istitle())      # True (Erster Buchstabe groß)

# Nützliche Checks
email = "user@example.com"
print("@" in email)        # True

zahl_str = "123"
print(zahl_str.isdigit())  # True

# Whitespace
leer = "   "
print(leer.isspace())      # True
print(leer.strip() == "")  # True
```

## Typische Anwendungsfälle

### Anwendung 1: Validierung

```python
def validiere_email(email):
    """Einfache Email-Validierung"""
    hat_at = '@' in email
    hat_punkt = '.' in email
    nicht_leer = len(email) > 0
    
    ist_gültig = hat_at and hat_punkt and nicht_leer
    
    return ist_gültig

print(validiere_email("user@example.com"))  # True
print(validiere_email("ungültig"))          # False

def validiere_passwort(passwort):
    """Passwort muss mindestens 8 Zeichen, 1 Zahl, 1 Großbuchstaben haben"""
    lang_genug = len(passwort) >= 8
    hat_zahl = any(c.isdigit() for c in passwort)
    hat_großbuchstabe = any(c.isupper() for c in passwort)
    
    return lang_genug and hat_zahl and hat_großbuchstabe

print(validiere_passwort("Test1234"))    # True
print(validiere_passwort("test"))        # False
```

### Anwendung 2: Flags und Zustandsverwaltung

```python
class Benutzer:
    def __init__(self, name):
        self.name = name
        self.ist_eingeloggt = False
        self.ist_admin = False
        self.hat_premium = False
    
    def login(self):
        self.ist_eingeloggt = True
    
    def logout(self):
        self.ist_eingeloggt = False
    
    def kann_admin_bereich_sehen(self):
        return self.ist_eingeloggt and self.ist_admin
    
    def kann_premium_content_sehen(self):
        return self.ist_eingeloggt and (self.ist_admin or self.hat_premium)

# Verwendung
user = Benutzer("Alice")
user.login()
user.ist_admin = True

print(user.kann_admin_bereich_sehen())        # True
print(user.kann_premium_content_sehen())      # True
```

### Anwendung 3: Filterung und Suche

```python
# Filtere Liste nach Bedingung
zahlen = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

gerade = [x for x in zahlen if x % 2 == 0]
print(gerade)  # [2, 4, 6, 8, 10]

# Mit filter()
def ist_gerade(n):
    return n % 2 == 0

gerade2 = list(filter(ist_gerade, zahlen))
print(gerade2)  # [2, 4, 6, 8, 10]

# Suche erstes Element
def finde_erstes(liste, bedingung):
    for element in liste:
        if bedingung(element):
            return element
    return None

erste_gerade = finde_erstes(zahlen, lambda x: x % 2 == 0)
print(erste_gerade)  # 2
```

### Anwendung 4: All/Any

```python
# all() - alle müssen True sein
noten = [80, 85, 90, 95]
alle_bestanden = all(note >= 60 for note in noten)
print(alle_bestanden)  # True

# any() - mindestens einer muss True sein
zahlen = [1, 3, 5, 7, 8]
hat_gerade = any(x % 2 == 0 for x in zahlen)
print(hat_gerade)  # True

# Praktisches Beispiel
def sind_alle_positiv(zahlen):
    return all(x > 0 for x in zahlen)

print(sind_alle_positiv([1, 2, 3]))     # True
print(sind_alle_positiv([1, -2, 3]))    # False

def enthält_negativzahl(zahlen):
    return any(x < 0 for x in zahlen)

print(enthält_negativzahl([1, 2, 3]))   # False
print(enthält_negativzahl([1, -2, 3]))  # True
```

### Anwendung 5: Kurzschluss-Auswertung nutzen

```python
# Default-Werte mit or
name = ""
anzeige_name = name or "Unbekannt"
print(anzeige_name)  # Unbekannt

# Verkettung
benutzername = input("Name: ") or "Gast"
print(f"Willkommen {benutzername}!")

# Cache-Pattern mit and
cache = {}
schlüssel = "daten"

# Nur wenn schlüssel in cache, dann zugreifen
ergebnis = schlüssel in cache and cache[schlüssel]
print(ergebnis)  # False (schlüssel nicht in cache)

# Sichere Attributzugriffe
obj = None
# wert = obj.attribut  # Würde AttributeError werfen
wert = obj and obj.attribut  # False (obj ist None)
print(wert)  # False
```

## Häufige Fallstricke

### Fallstrick 1: == vs is

```python
# == prüft Wert, is prüft Identität

# ✅ Für True/False: is ist OK
if wert is True:
    print("Explizit True")

# ⚠️ Aber oft unnötig!
if wert:  # Besser!
    print("Truthy")

# ❌ Nie is für andere Werte
x = 1000
y = 1000
print(x == y)  # True
print(x is y)  # False (verschiedene Objekte)

# ⚠️ Kleine Integers werden gecacht
a = 5
b = 5
print(a is b)  # True (gleiches Objekt)
```

### Fallstrick 2: Negation von Booleans

```python
# ❌ Umständlich
ist_aktiv = True
if ist_aktiv == False:
    print("Nicht aktiv")

# ✅ Besser
if not ist_aktiv:
    print("Nicht aktiv")

# ❌ Doppelte Negation
if not (not wert):
    pass

# ✅ Einfach
if wert:
    pass
```

### Fallstrick 3: Leere Collections

```python
# ⚠️ Liste mit False ist truthy!
liste = [False]
if liste:
    print("Liste ist nicht leer!")  # Wird ausgegeben!

# Richtig prüfen
if liste and liste[0]:
    print("Liste hat truthy Element")

# ✅ Länge prüfen wenn nötig
if len(liste) > 0:
    print("Liste nicht leer")
```

### Fallstrick 4: None prüfen

```python
wert = 0

# ❌ FALSCH: 0 ist falsy!
if not wert:
    print("Wert ist None")  # Wird fälschlich ausgegeben!

# ✅ RICHTIG: Explizit auf None prüfen
if wert is None:
    print("Wert ist None")  # Wird nicht ausgegeben

# ✅ Oder
if wert == None:  # Funktioniert, aber is ist besser
    print("Wert ist None")
```

### Fallstrick 5: Boolean aus String

```python
# ⚠️ Alle nicht-leeren Strings sind truthy!
wert = "False"
if wert:
    print("Wahr!")  # Wird ausgegeben!

# ✅ String zu Boolean konvertieren
def str_zu_bool(s):
    return s.lower() in ('true', '1', 'yes', 'ja')

print(str_zu_bool("True"))   # True
print(str_zu_bool("False"))  # False
print(str_zu_bool("1"))      # True
```

## Performance-Hinweise

### Kurzschluss-Auswertung nutzen

```python
# ✅ Schnelle Bedingung zuerst
def ist_gültig_und_teuer(obj):
    return obj is not None and obj.teure_berechnung()

# obj is not None ist schnell
# teure_berechnung() wird nur bei not None aufgerufen

# ❌ Langsam: Teure Operation zuerst
def schlecht(obj):
    return obj.teure_berechnung() and obj is not None
# teure_berechnung() wird immer aufgerufen!
```

### Boolean vs. Vergleich

```python
# ✅ Direkt Boolean nutzen
ist_aktiv = True
if ist_aktiv:
    pass

# ❌ Unnötiger Vergleich
if ist_aktiv == True:
    pass

# ⚡ Performance-Unterschied minimal, aber Code lesbarer!
```

### any() und all() sind lazy

```python
# any() stoppt bei erstem True
def check(x):
    print(f"Prüfe {x}")
    return x > 5

zahlen = [1, 2, 6, 8, 10]
ergebnis = any(check(x) for x in zahlen)
# Ausgabe:
# Prüfe 1
# Prüfe 2
# Prüfe 6
# Stoppt hier! (6 > 5 ist True)

# all() stoppt bei erstem False
```

## Übungsaufgaben

### Übung 1: Validierungs-Suite (Leicht)

Erstelle Funktionen zur Validierung von Benutzereingaben.

**Erwarteter Output:**
```python
print(ist_email_gültig("test@example.com"))  # True
print(ist_email_gültig("ungültig"))          # False
print(ist_passwort_sicher("Test1234"))       # True
print(ist_alter_gültig(25))                  # True
```

### Übung 2: Wahrheitstabellen-Generator (Mittel)

Erstelle alle Wahrheitstabellen für logische Operatoren.

**Erwarteter Output:**
```python
generiere_wahrheitstabelle('and')
# A     | B     | A and B
# True  | True  | True
# True  | False | False
# False | True  | False
# False | False | False
```

### Übung 3: Konfiguration mit Flags (Mittel)

Implementiere ein Konfigurationssystem mit Boolean-Flags.

**Erwarteter Output:**
```python
config = Config()
config.set_flag('dark_mode', True)
config.set_flag('notifications', True)
config.set_flag('auto_save', False)

print(config.ist_aktiv('dark_mode'))  # True
print(config.alle_aktiv())            # False
print(config.mindestens_eine_aktiv()) # True
```

### Übung 4: Smart Filter (Schwer)

Erstelle ein flexibles Filter-System mit kombinierbaren Bedingungen.

**Erwarteter Output:**
```python
produkte = [
    {'name': 'Laptop', 'preis': 999, 'lager': 5},
    {'name': 'Maus', 'preis': 25, 'lager': 0},
]

filter = SmartFilter(produkte)
ergebnis = filter.wo('preis', '<', 1000).und('lager', '>', 0).finde()
print(ergebnis)  # [{'name': 'Laptop', ...}]
```

### Übung 5: Logik-Evaluator (Schwer)

Parse und evaluiere logische Ausdrücke als Strings.

**Erwarteter Output:**
```python
evaluator = LogikEvaluator()
print(evaluator.eval("True and False"))           # False
print(evaluator.eval("(True or False) and True")) # True
print(evaluator.eval("not (False and True)"))     # True
```

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [Boolean Operations](https://docs.python.org/3/library/stdtypes.html#boolean-operations-and-or-not) - and, or, not
- [Truth Value Testing](https://docs.python.org/3/library/stdtypes.html#truth-value-testing) - Truthy/Falsy Werte
- [Comparisons](https://docs.python.org/3/library/stdtypes.html#comparisons) - Vergleichsoperatoren
- [Boolean Type](https://docs.python.org/3/library/functions.html#bool) - bool() Funktion
- [Built-in Functions - all/any](https://docs.python.org/3/library/functions.html#all) - all() und any()

## Zusammenfassung

### Wichtigste Punkte

1. **Boolean-Basics:**
   - Nur zwei Werte: `True`, `False`
   - Erbt von `int` (True=1, False=0)
   - Kann in Arithmetik verwendet werden

2. **Truthy/Falsy:**
   - Falsy: `False`, `None`, `0`, `""`, `[]`, `{}`, `()`
   - Alles andere ist truthy
   - Idiomatisch: `if liste:` statt `if len(liste) > 0:`

3. **Operatoren:**
   - Vergleich: `==`, `!=`, `<`, `>`, `<=`, `>=`
   - Logik: `and`, `or`, `not`
   - Identität: `is`, `is not`
   - Membership: `in`, `not in`

4. **Kurzschluss-Auswertung:**
   - `False and X` → X wird nicht ausgewertet
   - `True or X` → X wird nicht ausgewertet
   - Nutze für Performance und Sicherheit

5. **Best Practices:**
   - ✅ `if wert:` statt `if wert == True:`
   - ✅ `if not wert:` statt `if wert == False:`
   - ✅ `is None` statt `== None`
   - ✅ `all()`, `any()` für Listen
   - ❌ Verwechsle nicht `==` und `is`

## Verwandte Themen

- [[02_None Type|None Type]] - Der None-Typ
- [[03_Wahrheitswerte|Wahrheitswerte]] - Truthy/Falsy im Detail
- [[04_Übungen Bool und None|Übungen]] - Praktische Aufgaben
- [[01_Python Typ-System|Typ-System]] - bool als int-Subtyp
- [[05_Pattern_Matching/01_Grundlagen/01_Match Statement|Pattern Matching]] - Moderne Bedingungen

---

← [[05_Übungen Numerische Typen|Übungen Numerische Typen]] | [[INDEX|📑 Index]] | [[02_None Type|None Type]] →
