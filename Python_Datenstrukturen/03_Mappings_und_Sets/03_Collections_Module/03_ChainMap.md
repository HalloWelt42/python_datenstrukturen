---
tags: [python, datenstrukturen, collections, chainmap]
created: 2025-10-01
---

← [[03_Mappings_und_Sets/03_Collections_Module/02_DefaultDict|DefaultDict]] | [[INDEX|📑 Index]] | [[03_Mappings_und_Sets/03_Collections_Module/04_Übungen Collections|Übungen Collections]] →

# ChainMap

> [!tip] Lernziel
> Nach dieser Seite kannst du ChainMap für hierarchische Konfigurationen, Scopes und das Zusammenführen mehrerer Mappings einsetzen.

## Was ist ChainMap?

**ChainMap** gruppiert mehrere Dictionaries oder Mappings zu einer einzigen, aktualisierbaren View. Es durchsucht die Dictionaries in Reihenfolge bei Lookups.

**Kernmerkmale:**
- **Mehrere Mappings**: Gruppiert mehrere Dicts zusammen
- **Prioritäts-Lookup**: Erstes Dict hat höchste Priorität
- **Keine Kopie**: Views auf Original-Dicts, kein Merge
- **Schnell**: Nur Referenzen, keine Daten-Duplikation
- **Dynamisch**: Änderungen an Originals werden reflektiert
- **Scope-Simulation**: Ideal für variable Scopes

**Wofür verwendet:**
- Konfigurations-Hierarchien (User → Default)
- Scope-Chains (Local → Global)
- Mehrere Fallback-Quellen
- Command-line Args → Config File → Defaults
- Template-Contexts zusammenführen

---

## Import und Erstellung

### ChainMap erstellen

```python
from collections import ChainMap

# Aus mehreren Dicts
dict1 = {'a': 1, 'b': 2}
dict2 = {'b': 3, 'c': 4}
dict3 = {'c': 5, 'd': 6}

cm = ChainMap(dict1, dict2, dict3)
print(cm)
# ChainMap({'a': 1, 'b': 2}, {'b': 3, 'c': 4}, {'c': 5, 'd': 6})

# Zugriff: Erstes Dict gewinnt
print(cm['a'])  # 1 (nur in dict1)
print(cm['b'])  # 2 (in dict1 und dict2, dict1 gewinnt)
print(cm['c'])  # 4 (in dict2 und dict3, dict2 gewinnt)
print(cm['d'])  # 6 (nur in dict3)

# Leere ChainMap
empty = ChainMap()
print(empty)  # ChainMap({})

# Mit einem Dict
single = ChainMap({'a': 1})
print(single)  # ChainMap({'a': 1})
```

### Zugriff auf interne Dicts

```python
from collections import ChainMap

dict1 = {'a': 1}
dict2 = {'b': 2}
cm = ChainMap(dict1, dict2)

# Liste der Mappings
print(cm.maps)  # [{'a': 1}, {'b': 2}]
print(type(cm.maps))  # <class 'list'>

# Erstes Mapping (höchste Priorität)
print(cm.maps[0])  # {'a': 1}

# Mappings können geändert werden
cm.maps.append({'c': 3})
print(cm['c'])  # 3

# Oder entfernt
cm.maps.pop()
try:
    print(cm['c'])
except KeyError:
    print("'c' nicht mehr verfügbar")
```

---

## Grundlegende Operationen

### Lookup-Verhalten

```python
from collections import ChainMap

# Priorität: dict1 > dict2 > dict3
dict1 = {'name': 'Alice', 'age': 30}
dict2 = {'age': 25, 'city': 'Berlin'}
dict3 = {'city': 'München', 'country': 'Deutschland'}

cm = ChainMap(dict1, dict2, dict3)

# Erstes Vorkommen gewinnt
print(cm['name'])     # 'Alice' (nur in dict1)
print(cm['age'])      # 30 (dict1 gewinnt über dict2)
print(cm['city'])     # 'Berlin' (dict2 gewinnt über dict3)
print(cm['country'])  # 'Deutschland' (nur in dict3)

# KeyError wenn nirgends gefunden
try:
    print(cm['missing'])
except KeyError:
    print("Key nicht gefunden")

# get() mit Default
print(cm.get('missing', 'N/A'))  # 'N/A'
```

### Updates - nur erstes Dict!

```python
from collections import ChainMap

dict1 = {'a': 1}
dict2 = {'b': 2}
cm = ChainMap(dict1, dict2)

# Setzen: Nur in erstem Dict!
cm['c'] = 3
print(dict1)  # {'a': 1, 'c': 3} - Hinzugefügt
print(dict2)  # {'b': 2} - Unverändert

# Update bestehender Key
cm['a'] = 10
print(dict1)  # {'a': 10, 'c': 3} - Geändert in dict1

# Auch wenn Key in anderem Dict existiert
cm['b'] = 20
print(dict1)  # {'a': 10, 'c': 3, 'b': 20} - Neu in dict1!
print(dict2)  # {'b': 2} - Original unverändert!

# Delete: Nur aus erstem Dict
del cm['c']
print(dict1)  # {'a': 10, 'b': 20}
# Aber: KeyError wenn nicht in erstem Dict!
```

### Dynamische Änderungen

```python
from collections import ChainMap

dict1 = {'a': 1}
dict2 = {'b': 2}
cm = ChainMap(dict1, dict2)

print(cm['a'])  # 1

# Änderungen an Original-Dicts werden reflektiert
dict1['a'] = 100
print(cm['a'])  # 100 - Änderung sichtbar!

# Neuer Key in Original
dict2['c'] = 3
print(cm['c'])  # 3 - Automatisch verfügbar!

# Dict löschen
dict2.clear()
try:
    print(cm['b'])
except KeyError:
    print("'b' wurde aus dict2 gelöscht")
```

---

## ChainMap-spezifische Methoden

### new_child() - Neues Child-Dict

```python
from collections import ChainMap

# Basis-Config
base = {'debug': False, 'timeout': 30}
cm = ChainMap(base)

# Neuen Child-Scope hinzufügen
child = cm.new_child({'debug': True})
print(child['debug'])    # True (aus Child)
print(child['timeout'])  # 30 (aus Parent)

# Original unverändert
print(cm['debug'])  # False

# Child ist eine neue ChainMap
print(child.maps)  # [{'debug': True}, {'debug': False, 'timeout': 30}]

# Mit explizitem Dict
custom_child = cm.new_child({'custom': 'value'})
print(custom_child.maps)
# [{'custom': 'value'}, {'debug': False, 'timeout': 30}]
```

### parents - Parent-Scope

```python
from collections import ChainMap

# Mehrere Ebenen
dict1 = {'a': 1}
dict2 = {'b': 2}
dict3 = {'c': 3}

cm = ChainMap(dict1, dict2, dict3)
print(cm['a'])  # 1

# parents: ChainMap ohne erstes Dict
parent = cm.parents
print(parent.maps)  # [{'b': 2}, {'c': 3}]
print(parent['b'])  # 2
try:
    print(parent['a'])  # KeyError - 'a' war nur in dict1
except KeyError:
    print("'a' nicht in Parents")

# Nützlich für Scope-Hierarchien
# Local scope
local_scope = ChainMap({'x': 10})
# Mit global scope
with_global = local_scope.new_child({'y': 20})
print(with_global['y'])  # 20 (local)
print(with_global['x'])  # 10 (global)

# Zurück zu global
back_to_global = with_global.parents
print(back_to_global['x'])  # 10
```

---

## Praktische Anwendungen

### 1. Konfigurations-Hierarchie

```python
from collections import ChainMap
import os

# Default-Konfiguration
defaults = {
    'debug': False,
    'host': 'localhost',
    'port': 3000,
    'timeout': 30,
    'log_level': 'INFO'
}

# Config-File (simuliert)
config_file = {
    'host': '0.0.0.0',
    'port': 8000,
    'log_level': 'DEBUG'
}

# Umgebungsvariablen (simuliert)
env_vars = {
    'debug': True,  # Von Umgebung
    'timeout': 60
}

# Command-line Arguments (höchste Priorität)
cli_args = {
    'port': 9000
}

# ChainMap: CLI > ENV > Config > Defaults
config = ChainMap(cli_args, env_vars, config_file, defaults)

print("Effektive Konfiguration:")
print(f"  debug: {config['debug']}")        # True (von env)
print(f"  host: {config['host']}")          # 0.0.0.0 (von config_file)
print(f"  port: {config['port']}")          # 9000 (von cli_args)
print(f"  timeout: {config['timeout']}")    # 60 (von env)
print(f"  log_level: {config['log_level']}")  # DEBUG (von config_file)

# Alle Werte anzeigen
print("\nAlle Config-Werte:")
for key, value in config.items():
    print(f"  {key}: {value}")
```

### 2. Variable Scopes simulieren

```python
from collections import ChainMap

class Interpreter:
    def __init__(self):
        # Globaler Scope
        self.globals = {'pi': 3.14159, 'e': 2.71828}
        # Aktueller Scope (startet als global)
        self.scope = ChainMap(self.globals)
    
    def enter_function(self, local_vars=None):
        """Neuen Function-Scope betreten"""
        if local_vars is None:
            local_vars = {}
        self.scope = self.scope.new_child(local_vars)
    
    def exit_function(self):
        """Function-Scope verlassen"""
        self.scope = self.scope.parents
    
    def set_var(self, name, value):
        """Variable im aktuellen Scope setzen"""
        self.scope[name] = value
    
    def get_var(self, name):
        """Variable nachschlagen (mit Fallback zu parent scopes)"""
        return self.scope[name]

# Verwendung
interp = Interpreter()

# Globale Variable setzen
interp.set_var('x', 100)
print(f"Global x: {interp.get_var('x')}")  # 100

# Function scope betreten
interp.enter_function({'x': 10, 'y': 20})
print(f"Local x: {interp.get_var('x')}")   # 10 (local shadowed global)
print(f"Local y: {interp.get_var('y')}")   # 20
print(f"Global pi: {interp.get_var('pi')}")  # 3.14159 (aus global)

# Nested function
interp.enter_function({'x': 1})
print(f"Nested x: {interp.get_var('x')}")  # 1
print(f"Nested y: {interp.get_var('y')}")  # 20 (aus parent function)

# Zurück zu function scope
interp.exit_function()
print(f"Back to function x: {interp.get_var('x')}")  # 10

# Zurück zu global
interp.exit_function()
print(f"Back to global x: {interp.get_var('x')}")  # 100
```

### 3. Template Context

```python
from collections import ChainMap

class TemplateEngine:
    def __init__(self, globals=None):
        self.globals = globals or {}
    
    def render(self, template, context=None):
        """Rendert Template mit Context + Globals"""
        context = context or {}
        # Context hat Priorität über Globals
        full_context = ChainMap(context, self.globals)
        
        # Einfache Variable-Substitution
        result = template
        for key, value in full_context.items():
            result = result.replace(f'{{{key}}}', str(value))
        return result

# Setup
engine = TemplateEngine(globals={
    'site_name': 'My Website',
    'year': 2025
})

# Template 1 mit lokalem Context
template1 = "Welcome to {site_name}! User: {user}"
output1 = engine.render(template1, {'user': 'Alice'})
print(output1)  # Welcome to My Website! User: Alice

# Template 2 überschreibt global
template2 = "{site_name} © {year}"
output2 = engine.render(template2, {'site_name': 'Custom Site'})
print(output2)  # Custom Site © 2025

# Template 3 nur globals
template3 = "© {year} {site_name}"
output3 = engine.render(template3)
print(output3)  # © 2025 My Website
```

### 4. Feature Flags mit Prioritäten

```python
from collections import ChainMap

class FeatureManager:
    def __init__(self):
        # Default Features (alle aus)
        self.defaults = {
            'new_ui': False,
            'beta_feature': False,
            'analytics': True,
            'debug_mode': False
        }
        
        # User-spezifische Overrides
        self.user_features = {}
        
        # Temporäre Overrides (z.B. A/B Testing)
        self.temp_overrides = {}
    
    def get_features(self, user_id=None):
        """Gibt effektive Features für User zurück"""
        user_prefs = self.user_features.get(user_id, {})
        return ChainMap(self.temp_overrides, user_prefs, self.defaults)
    
    def set_user_feature(self, user_id, feature, enabled):
        """Setzt Feature für spezifischen User"""
        if user_id not in self.user_features:
            self.user_features[user_id] = {}
        self.user_features[user_id][feature] = enabled
    
    def set_temp_override(self, feature, enabled):
        """Temporärer Override für alle"""
        self.temp_overrides[feature] = enabled
    
    def is_enabled(self, feature, user_id=None):
        """Prüft ob Feature aktiv ist"""
        features = self.get_features(user_id)
        return features.get(feature, False)

# Verwendung
fm = FeatureManager()

# User 1: Beta Tester
fm.set_user_feature('user1', 'beta_feature', True)
fm.set_user_feature('user1', 'new_ui', True)

# User 2: Normal
# (nutzt nur Defaults)

print(f"User 1 - Beta: {fm.is_enabled('beta_feature', 'user1')}")  # True
print(f"User 2 - Beta: {fm.is_enabled('beta_feature', 'user2')}")  # False

# Temporärer Override für alle
fm.set_temp_override('debug_mode', True)

print(f"User 1 - Debug: {fm.is_enabled('debug_mode', 'user1')}")  # True
print(f"User 2 - Debug: {fm.is_enabled('debug_mode', 'user2')}")  # True

# User 1's effektive Features
features_user1 = fm.get_features('user1')
print(f"\nUser 1 Features: {dict(features_user1)}")
```

### 5. Plugin-System

```python
from collections import ChainMap

class PluginManager:
    def __init__(self):
        # Core commands
        self.core_commands = {
            'help': lambda: "Show help",
            'exit': lambda: "Exit application",
            'version': lambda: "1.0.0"
        }
        
        # Plugin commands
        self.plugins = []
        
        # Effektive Commands (Plugins > Core)
        self.update_commands()
    
    def register_plugin(self, plugin_commands):
        """Registriert Plugin mit Commands"""
        self.plugins.insert(0, plugin_commands)  # Am Anfang = höchste Priorität
        self.update_commands()
    
    def update_commands(self):
        """Aktualisiert Command-Chain"""
        self.commands = ChainMap(*self.plugins, self.core_commands)
    
    def execute(self, command):
        """Führt Command aus"""
        if command in self.commands:
            return self.commands[command]()
        return f"Unknown command: {command}"

# Setup
pm = PluginManager()

print(pm.execute('version'))  # 1.0.0 (von core)

# Plugin 1: Überschreibt version
plugin1 = {
    'version': lambda: "1.0.0-plugin1",
    'status': lambda: "Plugin 1 active"
}
pm.register_plugin(plugin1)

print(pm.execute('version'))  # 1.0.0-plugin1 (überschrieben)
print(pm.execute('status'))   # Plugin 1 active
print(pm.execute('help'))     # Show help (von core)

# Plugin 2: Höchste Priorität
plugin2 = {
    'version': lambda: "2.0.0-beta",
    'deploy': lambda: "Deploying..."
}
pm.register_plugin(plugin2)

print(pm.execute('version'))  # 2.0.0-beta (plugin2 gewinnt)
print(pm.execute('status'))   # Plugin 1 active (plugin1)
print(pm.execute('deploy'))   # Deploying... (plugin2)
print(pm.execute('help'))     # Show help (core)
```

---

## ChainMap vs. Dict Merging

### Unterschiede

```python
from collections import ChainMap

dict1 = {'a': 1, 'b': 2}
dict2 = {'b': 3, 'c': 4}

# 1. Mit ChainMap (keine Kopie)
cm = ChainMap(dict1, dict2)
print(cm['b'])  # 2 (dict1 gewinnt)

# Änderung an Original wird reflektiert
dict1['a'] = 100
print(cm['a'])  # 100 - Sichtbar!

# 2. Mit Dict Merging (Kopie)
merged = {**dict2, **dict1}  # dict1 überschreibt dict2
print(merged['b'])  # 2

# Änderung an Original NICHT sichtbar
dict1['a'] = 200
print(merged['a'])  # 100 - Unverändert!

# 3. Mit update() (Kopie)
merged2 = dict2.copy()
merged2.update(dict1)
print(merged2['b'])  # 2
```

### Performance

```python
import timeit
from collections import ChainMap

# Viele kleine Dicts
dicts = [{'key' + str(i): i} for i in range(100)]

# ChainMap: O(1) Creation
def with_chainmap():
    return ChainMap(*dicts)

# Dict Merge: O(n) alle Keys kopieren
def with_merge():
    result = {}
    for d in reversed(dicts):  # Richtige Priorität
        result.update(d)
    return result

cm_time = timeit.timeit(with_chainmap, number=10000)
merge_time = timeit.timeit(with_merge, number=10000)

print(f"ChainMap: {cm_time:.3f}s")
print(f"Merge:    {merge_time:.3f}s")
print(f"ChainMap ist {merge_time/cm_time:.1f}x schneller!")

# ChainMap ist deutlich schneller beim Erstellen!
# Aber: Lookup kann langsamer sein (muss mehrere Dicts durchsuchen)
```

### Wann welches verwenden?

```python
# ✅ ChainMap: Wenn dynamische Updates wichtig
config = ChainMap(cli_args, env_vars, defaults)
# Änderungen an cli_args sofort sichtbar

# ✅ Dict Merge: Wenn statisches Ergebnis gewünscht
final_config = {**defaults, **env_vars, **cli_args}
# Keine dynamischen Updates

# ✅ ChainMap: Bei vielen Fallback-Quellen
context = ChainMap(local, function, module, builtin)

# ✅ Dict Merge: Wenn Performance bei Lookups kritisch
# (Ein großes Dict ist schneller als mehrere kleine durchsuchen)
```

---

## Häufige Fallstricke

### ⚠️ Fallstrick 1: Updates nur in erstem Dict

```python
from collections import ChainMap

dict1 = {'a': 1}
dict2 = {'b': 2}
cm = ChainMap(dict1, dict2)

# Update betrifft nur dict1!
cm['b'] = 20
print(dict1)  # {'a': 1, 'b': 20} - 'b' hinzugefügt
print(dict2)  # {'b': 2} - Original unverändert!

# cm['b'] shadowed jetzt dict2['b']
print(cm['b'])  # 20 (aus dict1)
```

### ⚠️ Fallstrick 2: Delete nur aus erstem Dict

```python
from collections import ChainMap

dict1 = {'a': 1}
dict2 = {'b': 2}
cm = ChainMap(dict1, dict2)

# Delete funktioniert nur wenn in dict1!
try:
    del cm['b']  # KeyError! 'b' ist nur in dict2
except KeyError as e:
    print(f"Fehler: {e}")

# Funktioniert mit 'a'
del cm['a']
print(dict1)  # {} - Gelöscht
```

### ⚠️ Fallstrick 3: items(), keys(), values() mit Duplikaten

```python
from collections import ChainMap

dict1 = {'a': 1, 'b': 2}
dict2 = {'b': 3, 'c': 4}
cm = ChainMap(dict1, dict2)

# keys() zeigt alle unique Keys
print(list(cm.keys()))  # ['a', 'b', 'c']

# Aber values() kann verwirrend sein
print(list(cm.values()))  # [1, 2, 4] - nur von ersten Vorkommen

# items() auch
print(list(cm.items()))
# [('a', 1), ('b', 2), ('c', 4)] - 'b': 3 fehlt!

# len() zählt nur unique Keys
print(len(cm))  # 3 (nicht 4!)
```

---

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [collections.ChainMap](https://docs.python.org/3/library/collections.html#collections.ChainMap) - Vollständige Referenz
- [ChainMap Examples](https://docs.python.org/3/library/collections.html#chainmap-examples-and-recipes) - Praktische Beispiele
- [collections module](https://docs.python.org/3/library/collections.html) - Alle Collections

---

## Verwandte Themen

- [[03_Mappings_und_Sets/03_Collections_Module/01_Counter|Counter]] - Andere Collection
- [[03_Mappings_und_Sets/03_Collections_Module/02_DefaultDict|DefaultDict]] - Andere dict-Spezialisierung
- [[03_Mappings_und_Sets/01_Dictionaries/01_Dict Basics|Dict Basics]] - Basis-Funktionalität
- [[03_Mappings_und_Sets/01_Dictionaries/02_Dict Methoden|Dict Methoden]] - update() Alternative

---

← [[03_Mappings_und_Sets/03_Collections_Module/02_DefaultDict|DefaultDict]] | [[INDEX|📑 Index]] | [[03_Mappings_und_Sets/03_Collections_Module/04_Übungen Collections|Übungen Collections]] →