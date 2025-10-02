---
tags: [python, datenstrukturen, lösungen]
created: 2025-10-02
---

← [[06_Praxis/03_Projektübungen/04_Projekt - Datenbankmodell|Projekt - Datenbankmodell]] | [[INDEX|📑 Index]] | [[06_Praxis/04_Lösungen/Lösungen Teil 2|Lösungen Teil 2]] →

# Lösungen Teil 1: Grundlagen & Einfache Datentypen

> [!tip] Hinweis
> Dies sind Musterlösungen. Deine Lösung kann anders aussehen und trotzdem korrekt sein!

## 01_Grundlagen/02_Numerische_Typen

### Integer (int) - Übungen

**Übung 1: Große Zahlen**
```python
# Berechne 2^1000
result = 2 ** 1000
print(f"2^1000 hat {len(str(result))} Stellen")
print(f"Erste 50 Ziffern: {str(result)[:50]}...")

# Ausgabe:
# 2^1000 hat 302 Stellen
# Erste 50 Ziffern: 10715086071862673209484250490600018105614048...
```

**Übung 2: Zahlensysteme**
```python
decimal = 255

# Binär
binary = bin(decimal)
print(f"Binär: {binary}")  # 0b11111111

# Hexadezimal
hex_val = hex(decimal)
print(f"Hexadezimal: {hex_val}")  # 0xff

# Oktal
oct_val = oct(decimal)
print(f"Oktal: {oct_val}")  # 0o377

# Zurück zu Dezimal
from_bin = int('11111111', 2)
from_hex = int('ff', 16)
from_oct = int('377', 8)

print(f"Von Binär: {from_bin}")  # 255
print(f"Von Hex: {from_hex}")    # 255
print(f"Von Oktal: {from_oct}")  # 255
```

**Übung 3: Bitwise Operations**
```python
def check_bit(number: int, position: int) -> bool:
    """Prüft ob Bit an Position gesetzt ist"""
    return (number >> position) & 1 == 1

def set_bit(number: int, position: int) -> int:
    """Setzt Bit an Position"""
    return number | (1 << position)

def clear_bit(number: int, position: int) -> int:
    """Löscht Bit an Position"""
    return number & ~(1 << position)

def toggle_bit(number: int, position: int) -> int:
    """Togglet Bit an Position"""
    return number ^ (1 << position)

# Tests
num = 0b1010  # 10

print(f"Bit 1 gesetzt? {check_bit(num, 1)}")  # True
print(f"Bit 2 gesetzt? {check_bit(num, 2)}")  # False

print(f"Setze Bit 0: {bin(set_bit(num, 0))}")      # 0b1011
print(f"Lösche Bit 1: {bin(clear_bit(num, 1))}")   # 0b1000
print(f"Toggle Bit 3: {bin(toggle_bit(num, 3))}")  # 0b0010
```

**Übung 4: Primzahl-Check**
```python
def is_prime(n: int) -> bool:
    """
    Prüft ob Zahl prim ist
    
    Optimierung: Nur bis sqrt(n) prüfen
    """
    if n < 2:
        return False
    if n == 2:
        return True
    if n % 2 == 0:
        return False
    
    # Prüfe nur ungerade Teiler bis sqrt(n)
    import math
    for i in range(3, int(math.sqrt(n)) + 1, 2):
        if n % i == 0:
            return False
    
    return True

# Finde erste 20 Primzahlen
primes = []
num = 2
while len(primes) < 20:
    if is_prime(num):
        primes.append(num)
    num += 1

print(f"Erste 20 Primzahlen: {primes}")
# [2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47, 53, 59, 61, 67, 71]
```

**Übung 5: Fibonacci**
```python
def fibonacci(n: int) -> int:
    """
    Berechnet n-te Fibonacci-Zahl iterativ
    
    Effizient: O(n) Zeit, O(1) Speicher
    """
    if n <= 1:
        return n
    
    a, b = 0, 1
    for _ in range(2, n + 1):
        a, b = b, a + b
    
    return b

# Erste 15 Fibonacci-Zahlen
fibs = [fibonacci(i) for i in range(15)]
print(f"Fibonacci-Folge: {fibs}")
# [0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144, 233, 377]

# 100. Fibonacci-Zahl (sehr groß!)
print(f"100. Fibonacci: {fibonacci(100)}")
# 354224848179261915075
```

### Float - Übungen

**Übung 1: Float-Präzision**
```python
# Klassisches Problem
result = 0.1 + 0.2
print(f"0.1 + 0.2 = {result}")  # 0.30000000000000004
print(f"Ist 0.3? {result == 0.3}")  # False

# Richtige Vergleiche
import math

def almost_equal(a: float, b: float, epsilon: float = 1e-9) -> bool:
    """Vergleicht Floats mit Toleranz"""
    return abs(a - b) < epsilon

print(f"Fast gleich? {almost_equal(result, 0.3)}")  # True

# Oder: math.isclose (ab Python 3.5)
print(f"math.isclose? {math.isclose(result, 0.3)}")  # True
```

**Übung 2: Runden**
```python
value = 3.14159265359

# Verschiedene Rundungsmethoden
print(f"round(2): {round(value, 2)}")  # 3.14
print(f"round(4): {round(value, 4)}")  # 3.1416

# Floor, Ceil, Trunc
import math

print(f"floor: {math.floor(value)}")   # 3
print(f"ceil: {math.ceil(value)}")     # 4
print(f"trunc: {math.trunc(value)}")   # 3

# Negative Zahlen
neg = -3.7
print(f"floor(-3.7): {math.floor(neg)}")  # -4
print(f"ceil(-3.7): {math.ceil(neg)}")    # -3
print(f"trunc(-3.7): {math.trunc(neg)}")  # -3
```

**Übung 3: Spezialwerte**
```python
import math

# Infinity
inf = float('inf')
neg_inf = float('-inf')

print(f"inf + 1 = {inf + 1}")  # inf
print(f"inf * 2 = {inf * 2}")  # inf
print(f"inf > 1000000 = {inf > 1000000}")  # True

# NaN
nan = float('nan')

print(f"nan == nan? {nan == nan}")  # False!
print(f"math.isnan(nan)? {math.isnan(nan)}")  # True

# Checks
def categorize_float(x: float) -> str:
    """Kategorisiert Float-Wert"""
    if math.isnan(x):
        return "NaN"
    elif math.isinf(x):
        return "Infinity" if x > 0 else "-Infinity"
    elif x == 0.0:
        return "Zero"
    else:
        return "Normal"

print(categorize_float(3.14))    # Normal
print(categorize_float(inf))     # Infinity
print(categorize_float(nan))     # NaN
print(categorize_float(0.0))     # Zero
```

**Übung 4: Wissenschaftliche Notation**
```python
# Erstellen
small = 1.5e-10  # 0.00000000015
large = 3.2e15   # 3200000000000000

print(f"Klein: {small}")
print(f"Groß: {large}")

# Formatierung
avogadro = 6.02214076e23

print(f"Standard: {avogadro}")
print(f"e-Notation: {avogadro:e}")
print(f"E-Notation: {avogadro:E}")
print(f"Mit Präzision: {avogadro:.2e}")

# Ausgabe:
# Standard: 6.02214076e+23
# e-Notation: 6.022141e+23
# E-Notation: 6.022141E+23
# Mit Präzision: 6.02e+23
```

**Übung 5: Mathematische Funktionen**
```python
import math

def berechne_kreis(radius: float) -> dict:
    """Berechnet Kreiseigenschaften"""
    return {
        'radius': radius,
        'umfang': 2 * math.pi * radius,
        'flaeche': math.pi * radius ** 2,
        'durchmesser': 2 * radius
    }

def pythagoras(a: float, b: float) -> float:
    """Berechnet Hypotenuse"""
    return math.sqrt(a**2 + b**2)

# Tests
kreis = berechne_kreis(5.0)
print(f"Kreis r=5:")
for key, value in kreis.items():
    print(f"  {key}: {value:.2f}")

hyp = pythagoras(3, 4)
print(f"Hypotenuse(3,4): {hyp}")  # 5.0

# Trigonometrie
winkel_grad = 45
winkel_rad = math.radians(winkel_grad)

print(f"\n45° in Radiant: {winkel_rad:.4f}")
print(f"sin(45°): {math.sin(winkel_rad):.4f}")
print(f"cos(45°): {math.cos(winkel_rad):.4f}")
print(f"tan(45°): {math.tan(winkel_rad):.4f}")
```

### Decimal und Fraction - Übungen

**Übung 1: Genaue Berechnungen mit Decimal**
```python
from decimal import Decimal, getcontext

# Setze Präzision
getcontext().prec = 50

# Genaue Addition
d1 = Decimal('0.1')
d2 = Decimal('0.2')
result = d1 + d2

print(f"Decimal: {result}")  # 0.3 (exakt!)
print(f"Ist 0.3? {result == Decimal('0.3')}")  # True

# Float-Problem gelöst
f_result = 0.1 + 0.2
print(f"Float: {f_result}")  # 0.30000000000000004

# Finanzberechnung
preis = Decimal('19.99')
menge = Decimal('3')
mwst = Decimal('0.19')

subtotal = preis * menge
steuern = subtotal * mwst
total = subtotal + steuern

print(f"\nPreis: {preis}€")
print(f"Menge: {menge}")
print(f"Subtotal: {subtotal:.2f}€")
print(f"MwSt (19%): {steuern:.2f}€")
print(f"Total: {total:.2f}€")
```

**Übung 2: Bruchrechnung mit Fraction**
```python
from fractions import Fraction

# Erstellen
f1 = Fraction(1, 3)  # 1/3
f2 = Fraction(1, 6)  # 1/6

# Rechnen
summe = f1 + f2
print(f"{f1} + {f2} = {summe}")  # 1/3 + 1/6 = 1/2

differenz = f1 - f2
print(f"{f1} - {f2} = {differenz}")  # 1/3 - 1/6 = 1/6

produkt = f1 * f2
print(f"{f1} * {f2} = {produkt}")  # 1/3 * 1/6 = 1/18

quotient = f1 / f2
print(f"{f1} / {f2} = {quotient}")  # 1/3 / 1/6 = 2/1

# Von Float
f_from_float = Fraction(0.5)
print(f"Von 0.5: {f_from_float}")  # 1/2

# Problematisch mit unpräzisen Floats
f_unpraezise = Fraction(0.1)
print(f"Von 0.1: {f_unpraezise}")  
# 3602879701896397/36028797018963968 (nicht 1/10!)

# Besser: Von String
f_praezise = Fraction('0.1')
print(f"Von '0.1': {f_praezise}")  # 1/10
```

**Übung 3: Rezept-Umrechnung**
```python
from fractions import Fraction

class Rezept:
    """Rezept mit Bruch-Mengen"""
    
    def __init__(self, name: str, portionen: int):
        self.name = name
        self.portionen = portionen
        self.zutaten = {}
    
    def zutat_hinzufuegen(self, name: str, menge: Fraction, einheit: str):
        """Fügt Zutat hinzu"""
        self.zutaten[name] = {'menge': menge, 'einheit': einheit}
    
    def skalieren(self, neue_portionen: int) -> 'Rezept':
        """Skaliert Rezept für andere Portionenzahl"""
        faktor = Fraction(neue_portionen, self.portionen)
        
        neues_rezept = Rezept(self.name, neue_portionen)
        for name, data in self.zutaten.items():
            neue_menge = data['menge'] * faktor
            neues_rezept.zutat_hinzufuegen(name, neue_menge, data['einheit'])
        
        return neues_rezept
    
    def __str__(self) -> str:
        lines = [f"{self.name} ({self.portionen} Portionen):"]
        for name, data in self.zutaten.items():
            menge = data['menge']
            einheit = data['einheit']
            lines.append(f"  - {menge} {einheit} {name}")
        return '\n'.join(lines)

# Originalrezept für 4 Portionen
kuchen = Rezept("Marmorkuchen", 4)
kuchen.zutat_hinzufuegen("Mehl", Fraction(1, 2), "kg")
kuchen.zutat_hinzufuegen("Zucker", Fraction(1, 4), "kg")
kuchen.zutat_hinzufuegen("Butter", Fraction(1, 8), "kg")
kuchen.zutat_hinzufuegen("Eier", Fraction(3, 1), "Stück")

print(kuchen)
print()

# Skaliere auf 6 Portionen
kuchen_6 = kuchen.skalieren(6)
print(kuchen_6)

# Ausgabe:
# Marmorkuchen (4 Portionen):
#   - 1/2 kg Mehl
#   - 1/4 kg Zucker
#   - 1/8 kg Butter
#   - 3 Stück Eier
#
# Marmorkuchen (6 Portionen):
#   - 3/4 kg Mehl
#   - 3/8 kg Zucker
#   - 3/16 kg Butter
#   - 9/2 Stück Eier
```

## 01_Grundlagen/03_Boolean_und_None

### Boolean - Übungen

**Übung 1: Wahrheitswerte**
```python
# Verschiedene Typen testen
values = [
    0, 1, -1,
    "", "text",
    [], [1, 2],
    {}, {"a": 1},
    None,
    True, False
]

for val in values:
    print(f"{str(val):15s} -> {bool(val)}")

# Ausgabe:
# 0               -> False
# 1               -> True
# -1              -> True
# ...
```

**Übung 2: Logische Operatoren**
```python
def xor(a: bool, b: bool) -> bool:
    """XOR: True wenn genau einer True ist"""
    return (a or b) and not (a and b)

def nand(a: bool, b: bool) -> bool:
    """NAND: Not AND"""
    return not (a and b)

def nor(a: bool, b: bool) -> bool:
    """NOR: Not OR"""
    return not (a or b)

# Wahrheitstabelle
print("A     B     | XOR   NAND  NOR")
print("-" * 35)
for a in [False, True]:
    for b in [False, True]:
        print(f"{a!s:5s} {b!s:5s} | {xor(a,b)!s:5s} {nand(a,b)!s:5s} {nor(a,b)!s:5s}")
```

**Übung 3: Short-Circuit Evaluation**
```python
def expensive_check():
    """Simuliert teure Berechnung"""
    print("Teure Berechnung läuft...")
    return True

# AND: Zweiter Teil wird nicht ausgeführt wenn erster False
print("Test 1:")
result = False and expensive_check()
print(f"Result: {result}\n")  # Keine Ausgabe von expensive_check

# OR: Zweiter Teil wird nicht ausgeführt wenn erster True
print("Test 2:")
result = True or expensive_check()
print(f"Result: {result}\n")  # Keine Ausgabe von expensive_check

# Praktisches Beispiel: Sichere Division
def safe_divide(a: int, b: int) -> float:
    """Division mit Nullcheck"""
    if b != 0 and a / b > 0:  # Short-circuit verhindert Division durch 0
        return a / b
    return 0.0

print(safe_divide(10, 2))  # 5.0
print(safe_divide(10, 0))  # 0.0 (kein Error!)
```

**Übung 4: All und Any**
```python
# all(): Alle True?
numbers = [2, 4, 6, 8, 10]
alle_gerade = all(n % 2 == 0 for n in numbers)
print(f"Alle gerade? {alle_gerade}")  # True

# any(): Mindestens einer True?
zahlen = [1, 3, 4, 7, 9]
mindestens_eine_gerade = any(n % 2 == 0 for n in zahlen)
print(f"Mindestens eine gerade? {mindestens_eine_gerade}")  # True

# Praktisches Beispiel: Passwort-Validierung
def ist_starkes_passwort(pw: str) -> bool:
    """Prüft Passwort-Stärke"""
    checks = [
        len(pw) >= 8,
        any(c.isupper() for c in pw),
        any(c.islower() for c in pw),
        any(c.isdigit() for c in pw),
        any(c in '!@#$%^&*' for c in pw)
    ]
    return all(checks)

print(ist_starkes_passwort("Passw0rd!"))  # True
print(ist_starkes_passwort("password"))   # False
```

### None - Übungen

**Übung 1: None vs. False vs. 0**
```python
values = [None, False, 0, "", [], {}]

print("Wert      | is None | == False | bool()")
print("-" * 45)
for val in values:
    is_none = val is None
    eq_false = val == False
    as_bool = bool(val)
    print(f"{str(val):10s}| {is_none!s:7s} | {eq_false!s:8s} | {as_bool!s}")

# Wichtig: is None vs. == None
x = None
print(f"\nx is None: {x is None}")  # True (richtig!)
print(f"x == None: {x == None}")    # True (funktioniert, aber nicht idiomatisch)
```

**Übung 2: Default-Werte**
```python
def greet(name: str = None) -> str:
    """Begrüßung mit optionalem Namen"""
    if name is None:
        name = "Fremder"
    return f"Hallo, {name}!"

print(greet("Alice"))  # Hallo, Alice!
print(greet())         # Hallo, Fremder!

# Mit Walrus-Operator (Python 3.8+)
def process_data(data: list = None) -> list:
    """Verarbeitet Daten mit Default"""
    if (d := data) is None:
        d = []
    return d

# Idiomatischer: or-Operator
def get_value(val = None):
    return val or "default"

print(get_value("test"))  # test
print(get_value(None))    # default
print(get_value(0))       # default (Achtung: 0 ist falsy!)
```

**Übung 3: Optional Type Hints**
```python
from typing import Optional

def find_user(user_id: int) -> Optional[dict]:
    """
    Sucht User, gibt None zurück wenn nicht gefunden
    
    Optional[dict] ist equivalent zu Union[dict, None]
    """
    users = {
        1: {"name": "Alice", "email": "alice@example.com"},
        2: {"name": "Bob", "email": "bob@example.com"},
    }
    return users.get(user_id)

# Verwendung
user = find_user(1)
if user is not None:
    print(f"Gefunden: {user['name']}")
else:
    print("User nicht gefunden")

# Mit Walrus
if (user := find_user(3)) is not None:
    print(f"User: {user['name']}")
else:
    print("Nicht gefunden")
```

## 01_Grundlagen/04_Strings

### String Basics - Übungen

**Übung 1: String-Slicing**
```python
text = "Python Programming"

# Verschiedene Slices
print(text[0:6])      # Python
print(text[7:])       # Programming
print(text[:6])       # Python
print(text[-11:])     # Programming
print(text[::2])      # Pto rgamn
print(text[::-1])     # gnimmargorP nohtyP

# Palindrom-Check
def ist_palindrom(s: str) -> bool:
    """Prüft ob String ein Palindrom ist"""
    s = s.lower().replace(" ", "")
    return s == s[::-1]

print(ist_palindrom("Anna"))              # True
print(ist_palindrom("Lagerregal"))        # True
print(ist_palindrom("Ein Esel lese nie")) # True
print(ist_palindrom("Python"))            # False
```

**Übung 2: String-Methoden**
```python
text = "  Hello, World!  "

# Trimming
print(f"strip: '{text.strip()}'")    # 'Hello, World!'
print(f"lstrip: '{text.lstrip()}'")  # 'Hello, World!  '
print(f"rstrip: '{text.rstrip()}'")  # '  Hello, World!'

# Case
print(text.upper())       # HELLO, WORLD!
print(text.lower())       # hello, world!
print(text.title())       # Hello, World!
print(text.capitalize())  # Hello, world!

# Suchen
print(text.find("World"))     # 9
print(text.index("World"))    # 9
print(text.count("o"))        # 2
print(text.startswith("  H")) # True
print(text.endswith("!  "))   # True

# Ersetzen
print(text.replace("World", "Python"))  # Hello, Python!
```

**Übung 3: String-Formatierung**
```python
name = "Alice"
age = 30
salary = 75000.50

# f-strings (modern, empfohlen)
print(f"Name: {name}, Alter: {age}")
print(f"Gehalt: {salary:,.2f}€")
print(f"Hex: {age:#x}")

# format()
print("Name: {}, Alter: {}".format(name, age))
print("Gehalt: {:.2f}€".format(salary))

# %-Formatting (alt, aber noch verwendet)
print("Name: %s, Alter: %d" % (name, age))

# Alignment und Padding
print(f"{name:>10}")    # Rechtsbündig, Breite 10
print(f"{name:<10}")    # Linksbündig
print(f"{name:^10}")    # Zentriert
print(f"{age:05d}")     # Padding mit Nullen: 00030
```

**Übung 4: String-Parsing**
```python
csv_line = "Alice,30,alice@example.com,Engineering"

# Split
parts = csv_line.split(",")
print(parts)  # ['Alice', '30', 'alice@example.com', 'Engineering']

# Join
new_csv = " | ".join(parts)
print(new_csv)  # Alice | 30 | alice@example.com | Engineering

# Partition
email = "alice@example.com"
user, sep, domain = email.partition("@")
print(f"User: {user}, Domain: {domain}")

# Splitlines
multiline = """Line 1
Line 2
Line 3"""
lines = multiline.splitlines()
print(lines)  # ['Line 1', 'Line 2', 'Line 3']
```

**Übung 5: Text-Validierung**
```python
def validate_input(text: str) -> dict:
    """Validiert verschiedene String-Eigenschaften"""
    return {
        'is_alpha': text.isalpha(),
        'is_digit': text.isdigit(),
        'is_alnum': text.isalnum(),
        'is_upper': text.isupper(),
        'is_lower': text.islower(),
        'is_space': text.isspace(),
        'is_title': text.istitle(),
    }

tests = ["Hello", "123", "Hello123", "HELLO", "hello", "   ", "Hello World"]

for test in tests:
    result = validate_input(test)
    print(f"\n'{test}':")
    for key, value in result.items():
        if value:
            print(f"  ✓ {key}")
```

## Verwandte Themen

- [[01_Grundlagen/02_Numerische_Typen/01_Integer (int)|Integer]]
- [[01_Grundlagen/02_Numerische_Typen/02_Float|Float]]
- [[01_Grundlagen/03_Boolean_und_None/01_Boolean (bool)|Boolean]]
- [[01_Grundlagen/04_Strings/01_String Basics|Strings]]

---
← [[06_Praxis/03_Projektübungen/04_Projekt - Datenbankmodell|Projekt - Datenbankmodell]] | [[INDEX|📑 Index]] | [[06_Praxis/04_Lösungen/Lösungen Teil 2|Lösungen Teil 2]] →