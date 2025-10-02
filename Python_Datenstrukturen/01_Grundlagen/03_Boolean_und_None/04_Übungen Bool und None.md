---
tags: [python, datenstrukturen, übungen, boolean, none, praxis]
created: 2025-10-01
---

← [[03_Wahrheitswerte|Wahrheitswerte]] | [[INDEX|📑 Index]] | [[01_Grundlagen/04_Strings/01_String Basics|String Basics]] →

# Übungen Bool und None

> [!tip] Lernziel
> Du festigst dein Wissen über Boolean, None und Wahrheitswerte durch praktische Übungen

## Übungsstruktur

Jede Übung hat:
- 📝 **Aufgabe** - Was zu tun ist
- 💡 **Hinweise** - Tipps zur Lösung
- ✅ **Erwarteter Output** - So sollte es aussehen
- 🎯 **Schwierigkeit** - Leicht / Mittel / Schwer

Die Lösungen findest du in [[06_Praxis/04_Lösungen/Lösungen Teil 1|Lösungen Teil 1]].

## Boolean Grundlagen

### Übung 1: Wahrheitstabellen-Generator 🎯 Leicht

**Aufgabe:**
Erstelle einen Generator für Wahrheitstabellen aller logischen Operatoren.

**Erwarteter Output:**
```python
print_wahrheitstabelle('and')
# A     | B     | A and B
# ------|-------|--------
# True  | True  | True
# True  | False | False
# False | True  | False
# False | False | False

print_wahrheitstabelle('or')
print_wahrheitstabelle('xor')  # Exklusiv-Or
print_wahrheitstabelle('implies')  # Implikation
```

💡 **Hinweise:**
- Iteriere über alle Kombinationen von True/False
- `itertools.product([True, False], repeat=2)` hilft
- XOR: A xor B = (A or B) and not (A and B)
- Implies: A → B = not A or B

---

### Übung 2: Boolean Expression Parser 🎯 Mittel

**Aufgabe:**
Parse und evaluiere Boolean-Ausdrücke als Strings.

**Erwarteter Output:**
```python
evaluiere("True and False")              # False
evaluiere("(True or False) and True")    # True
evaluiere("not (False and True)")        # True
evaluiere("True and (False or True)")    # True
```

💡 **Hinweise:**
- Nutze `eval()` VORSICHTIG (nur für sichere Eingaben!)
- Oder: Eigener Parser mit Tokenization
- Oder: `ast.literal_eval()` für Sicherheit

---

### Übung 3: Logik-Rechner 🎯 Mittel

**Aufgabe:**
Implementiere einen Rechner für logische Operationen mit Variablen.

**Erwarteter Output:**
```python
calc = LogikRechner()
calc.setze_variable('A', True)
calc.setze_variable('B', False)
calc.setze_variable('C', True)

print(calc.berechne("A and B"))        # False
print(calc.berechne("A or B"))         # True
print(calc.berechne("A and (B or C)")) # True
```

💡 **Hinweise:**
- Speichere Variablen in Dictionary
- Ersetze Variablennamen mit Werten
- Nutze eval() oder eigenen Parser

---

### Übung 4: Circuit Simulator 🎯 Schwer

**Aufgabe:**
Simuliere logische Schaltkreise (AND, OR, NOT, XOR Gates).

**Erwarteter Output:**
```python
circuit = Circuit()
a = circuit.input('A')
b = circuit.input('B')
and_gate = circuit.AND(a, b)
or_gate = circuit.OR(a, b)
output = circuit.XOR(and_gate, or_gate)

# Teste verschiedene Inputs
circuit.set_inputs({'A': True, 'B': False})
print(circuit.evaluate(output))  # Ergebnis
```

💡 **Hinweise:**
- Erstelle Gate-Klassen
- Gates haben Inputs und berechnen Output
- Evaluiere Schritt für Schritt

---

### Übung 5: Satisfiability Solver (SAT) 🎯 Schwer

**Aufgabe:**
Prüfe ob eine logische Formel erfüllbar ist (finde Variablenbelegung die True ergibt).

**Erwarteter Output:**
```python
# (A or B) and (not A or C)
formel = "(A or B) and (not A or C)"
lösung = sat_solve(formel)
print(lösung)
# {'A': True, 'B': False, 'C': True} oder None wenn unerfüllbar
```

💡 **Hinweise:**
- Brute-Force: Teste alle Kombinationen
- Nutze itertools.product für Kombinationen
- Optimierung: Backtracking

---

## None Übungen

### Übung 6: None-Safe Dictionary 🎯 Leicht

**Aufgabe:**
Erstelle ein Dictionary das None-Werte anders behandelt.

**Erwarteter Output:**
```python
d = NoneSafeDict()
d['a'] = 1
d['b'] = None
d['c'] = 0

print(d.get('a'))  # 1
print(d.get('b'))  # None
print(d.get('d'))  # None

print(d.has_value('a'))  # True
print(d.has_value('b'))  # False (None ist "kein Wert")
print(d.has_value('d'))  # False
```

💡 **Hinweise:**
- Erbe von dict
- Unterscheide zwischen None als Wert und fehlendem Key
- `has_value()` prüft auf nicht-None

---

### Übung 7: Optional Chain 🎯 Mittel

**Aufgabe:**
Implementiere Optional Chaining wie in JavaScript (?.operator).

**Erwarteter Output:**
```python
data = {
    'user': {
        'profile': {
            'name': 'Alice'
        }
    }
}

chain = OptionalChain(data)
print(chain.get('user.profile.name'))      # Alice
print(chain.get('user.settings.theme'))    # None
print(chain.get('user.profile.age', 25))   # 25 (default)
```

💡 **Hinweise:**
- Split String bei '.'
- Gehe Schritt für Schritt durch
- Return None bei erstem fehlenden Key

---

### Übung 8: Null Object Pattern 🎯 Mittel

**Aufgabe:**
Implementiere das Null Object Pattern für sichere Methodenaufrufe.

**Erwarteter Output:**
```python
user = get_user(123)  # Kann None zurückgeben
# Statt: if user: print(user.name)
print(user.name)  # Funktioniert auch wenn user=NullUser

real_user = RealUser("Alice")
null_user = NullUser()

print(real_user.name)     # Alice
print(null_user.name)     # "" (leerer String)
print(bool(real_user))    # True
print(bool(null_user))    # False
```

💡 **Hinweise:**
- Erstelle NullUser-Klasse
- Implementiere gleiche Methoden wie RealUser
- Gib sichere Default-Werte zurück

---

### Übung 9: Maybe Monad 🎯 Schwer

**Aufgabe:**
Implementiere eine Maybe/Option Monad für sichere Verkettung.

**Erwarteter Output:**
```python
# Ohne Maybe: Viele None-Checks
user = get_user()
if user:
    profile = user.get_profile()
    if profile:
        name = profile.get_name()

# Mit Maybe: Verkettung
name = (Maybe(get_user())
    .map(lambda u: u.get_profile())
    .map(lambda p: p.get_name())
    .get_or_else("Unbekannt"))
```

💡 **Hinweise:**
- Maybe hat zwei Zustände: Just(value) oder Nothing
- map() wendet Funktion nur bei Just an
- Verkettung stoppt bei Nothing

---

### Übung 10: None-Coalescing Operator 🎯 Schwer

**Aufgabe:**
Implementiere den Null-Coalescing-Operator (?? aus anderen Sprachen).

**Erwarteter Output:**
```python
# Gibt ersten nicht-None Wert zurück
print(coalesce(None, None, 0, 5))       # 0 (nicht 5!)
print(coalesce(None, "", "text"))       # "" (nicht "text"!)
print(coalesce(None, False, True))      # False
print(coalesce(None, None, None))       # None
```

💡 **Hinweise:**
- Unterscheide None von anderen falsy-Werten
- Nutze `is not None` nicht `if wert`
- Iteriere durch Argumente

---

## Truthy/Falsy Übungen

### Übung 11: Truthy Analyzer 🎯 Leicht

**Aufgabe:**
Analysiere Werte und gib detaillierte Informationen über Truthy/Falsy zurück.

**Erwarteter Output:**
```python
analyse(0)
# {
#   'wert': 0,
#   'typ': 'int',
#   'truthy': False,
#   'grund': 'Numerischer Nullwert',
#   'kategorie': 'number'
# }

analyse([False])
# {
#   'wert': [False],
#   'typ': 'list',
#   'truthy': True,
#   'grund': 'Nicht-leere Collection',
#   'kategorie': 'collection'
# }
```

💡 **Hinweise:**
- Prüfe Typ mit `type()` oder `isinstance()`
- Kategorisiere: number, string, collection, other
- Erkläre warum truthy/falsy

---

### Übung 12: Smart Filter 🎯 Mittel

**Aufgabe:**
Erstelle einen Filter der zwischen None und anderen falsy-Werten unterscheidet.

**Erwarteter Output:**
```python
werte = [0, 1, None, "", "text", [], [1], False, True]

truthy = smart_filter(werte, mode='truthy')
# [1, "text", [1], True]

not_none = smart_filter(werte, mode='not_none')
# [0, 1, "", "text", [], [1], False, True]

numeric_truthy = smart_filter(werte, mode='numeric_truthy')
# [1, True]  (nur truthy Zahlen)
```

💡 **Hinweise:**
- Verschiedene Filtermodi
- mode='truthy': normale truthy-Filterung
- mode='not_none': nur None entfernen
- mode='numeric_truthy': nur truthy Zahlen

---

### Übung 13: Validation Framework 🎯 Schwer

**Aufgabe:**
Erstelle ein Validierungs-Framework mit verkettbaren Regeln.

**Erwarteter Output:**
```python
validator = Validator(username)
result = (validator
    .not_none("Username erforderlich")
    .not_empty("Username darf nicht leer sein")
    .min_length(3, "Username zu kurz")
    .max_length(20, "Username zu lang")
    .matches(r'^[a-zA-Z0-9_]+$', "Nur alphanumerisch")
    .validate())

if result.is_valid():
    print("Gültig!")
else:
    print(result.errors)  # Liste aller Fehler
```

💡 **Hinweise:**
- Validator-Klasse mit Methoden für Regeln
- Sammle Fehler in Liste
- Jede Methode returned self für Chaining
- validate() gibt Result-Objekt zurück

---

### Übung 14: Default Value Provider 🎯 Schwer

**Aufgabe:**
Implementiere intelligentes Default-System mit Fallback-Kette.

**Erwarteter Output:**
```python
defaults = DefaultProvider()
defaults.register('theme', 'hell')
defaults.register('language', lambda: detect_language())  # Callable
defaults.register('timezone', lambda ctx: ctx.get('user_tz'))  # Mit Context

# Hierarchie: Wert → Default → None
provider = defaults.create_provider({'theme': None})
print(provider.get('theme'))     # hell (nutzt Default)
print(provider.get('language'))  # de (callable ausgeführt)
print(provider.get('timezone'))  # None (kein Context)
```

💡 **Hinweise:**
- Registry für Defaults
- Unterstütze Werte, Callables, Context-Callables
- Hierarchie beachten

---

### Übung 15: Conditional Execution Engine 🎯 Schwer

**Aufgabe:**
Erstelle eine Engine für bedingte Ausführung mit komplexen Regeln.

**Erwarteter Output:**
```python
engine = ConditionalEngine()

engine.add_rule(
    condition=lambda ctx: ctx['age'] >= 18,
    action=lambda ctx: print("Volljährig"),
    name="adult_check"
)

engine.add_rule(
    condition=lambda ctx: ctx.get('premium') or False,
    action=lambda ctx: print("Premium-Features aktiviert"),
    name="premium_check"
)

context = {'age': 25, 'premium': None}
engine.execute(context)
# Ausgabe: Volljährig
# (premium_check wird nicht ausgeführt wegen None/False)
```

💡 **Hinweise:**
- Rule-Klasse mit condition und action
- Sammle Rules in Liste
- execute() prüft alle Conditions
- Führe Actions aus wenn Condition True

---

## Kombinierte Übungen

### Übung 16: Feature Flags System 🎯 Mittel

**Aufgabe:**
Implementiere ein Feature-Flag-System mit verschiedenen Strategien.

**Erwarteter Output:**
```python
flags = FeatureFlags()
flags.set('dark_mode', True)
flags.set('beta_features', False)
flags.set('new_ui', None)  # Nicht entschieden

print(flags.is_enabled('dark_mode'))      # True
print(flags.is_enabled('beta_features'))  # False
print(flags.is_enabled('new_ui'))         # False (None → False)
print(flags.is_enabled('unknown'))        # False (nicht gesetzt)

# Mit Default
print(flags.is_enabled('new_ui', default=True))  # True
```

💡 **Hinweise:**
- Dictionary für Flags
- Unterscheide None, True, False
- Default-Parameter für is_enabled()

---

### Übung 17: Query Builder 🎯 Schwer

**Aufgabe:**
Baue einen SQL-ähnlichen Query Builder mit bedingten WHERE-Clauses.

**Erwarteter Output:**
```python
query = Query('users')
query.where('age', '>', 18)
query.where('active', '=', True)
query.where('email', 'IS NOT', None)  # Explizit auf None prüfen

# Optionale Conditions (None wird ignoriert)
search_term = None
if search_term:  # Wird nicht hinzugefügt
    query.where('name', 'LIKE', search_term)

print(query.to_sql())
# SELECT * FROM users WHERE age > 18 AND active = true AND email IS NOT NULL
```

💡 **Hinweise:**
- WHERE-Bedingungen sammeln
- None-Werte speziell behandeln (IS NULL / IS NOT NULL)
- Optional: Parametrisierte Queries

---

### Übung 18: State Machine 🎯 Schwer

**Aufgabe:**
Implementiere eine State Machine mit Guards (Boolean-Bedingungen für Übergänge).

**Erwarteter Output:**
```python
sm = StateMachine(initial='idle')

sm.add_transition(
    from_state='idle',
    to_state='running',
    guard=lambda ctx: ctx.get('has_permission', False)
)

sm.add_transition(
    from_state='running',
    to_state='completed',
    guard=lambda ctx: ctx.get('progress', 0) >= 100
)

context = {'has_permission': True, 'progress': 50}
sm.trigger('start', context)  # idle → running
print(sm.current_state)  # running

context['progress'] = 100
sm.trigger('finish', context)  # running → completed
print(sm.current_state)  # completed
```

💡 **Hinweise:**
- Zustände und Übergänge speichern
- Guard ist Callable die bool zurückgibt
- Nur übergang wenn Guard True

---

## Bonus-Herausforderungen 🔥

### Challenge 1: Three-Valued Logic

Implementiere Three-Valued Logic (True, False, Unknown) wie in SQL.

```python
# True AND Unknown = Unknown
# True OR Unknown = True
# NOT Unknown = Unknown
```

### Challenge 2: Fuzzy Logic

Implementiere Fuzzy Boolean Logic mit Werten zwischen 0.0 und 1.0.

```python
fuzzy_and(0.7, 0.8)  # 0.7 (minimum)
fuzzy_or(0.7, 0.8)   # 0.8 (maximum)
fuzzy_not(0.7)       # 0.3 (1.0 - x)
```

### Challenge 3: Quantum Boolean

Simuliere Quantum Superposition mit Wahrscheinlichkeiten.

```python
qbit = QuantumBit(prob_true=0.5)
# Ist gleichzeitig True und False bis zur Messung
result = qbit.measure()  # Kollabiert zu True oder False
```

## Tipps für alle Übungen

### Testing

```python
def test_wahrheitstabelle():
    # Teste AND
    assert evaluiere_and(True, True) == True
    assert evaluiere_and(True, False) == False
    assert evaluiere_and(False, True) == False
    assert evaluiere_and(False, False) == False
    print("✅ AND Tests bestanden")

test_wahrheitstabelle()
```

### Debugging mit assert

```python
# Nutze assertions für Annahmen
def sichere_funktion(wert):
    assert wert is not None, "Wert darf nicht None sein"
    assert isinstance(wert, (int, float)), "Wert muss Zahl sein"
    # ... Rest der Funktion
```

### Type Hints nutzen

```python
from typing import Optional, Union

def verarbeite(wert: Optional[int]) -> bool:
    """
    Verarbeitet Wert oder gibt False bei None zurück.
    """
    if wert is None:
        return False
    return wert > 0
```

## Weiterführende Ideen

Nach diesen Übungen kannst du:

1. **Regelbasiertes System** für Business Logic
2. **Workflow Engine** mit bedingten Schritten
3. **A/B Testing Framework** mit Feature Flags
4. **Permissions System** mit komplexen Regeln
5. **Configuration Manager** mit Overrides

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [Boolean Operations](https://docs.python.org/3/library/stdtypes.html#boolean-operations-and-or-not)
- [Truth Value Testing](https://docs.python.org/3/library/stdtypes.html#truth-value-testing)
- [Built-in Functions - all/any](https://docs.python.org/3/library/functions.html#all)
- [None](https://docs.python.org/3/library/constants.html#None)

## Verwandte Themen

- [[01_Boolean (bool)|Boolean]] - Boolean-Grundlagen
- [[02_None Type|None Type]] - None im Detail
- [[03_Wahrheitswerte|Wahrheitswerte]] - Truthy/Falsy
- [[06_Praxis/04_Lösungen/Lösungen Teil 1|Lösungen]] - Musterlösungen zu allen Übungen

---

← [[03_Wahrheitswerte|Wahrheitswerte]] | [[INDEX|📑 Index]] | [[01_Grundlagen/04_Strings/01_String Basics|String Basics]] →