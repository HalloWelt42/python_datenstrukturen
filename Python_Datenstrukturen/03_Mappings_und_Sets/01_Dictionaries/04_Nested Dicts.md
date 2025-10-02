---
tags: [python, datenstrukturen, mappings, dictionaries, nested, verschachtelt]
created: 2025-10-01
---

← [[03_Mappings_und_Sets/01_Dictionaries/03_Dict Comprehensions|Dict Comprehensions]] | [[INDEX|📑 Index]] | [[03_Mappings_und_Sets/01_Dictionaries/05_Dict Views|Dict Views]] →

# Nested Dictionaries (Verschachtelte Dictionaries)

> [!tip] Lernziel
> Nach dieser Seite kannst du komplexe, verschachtelte Dictionary-Strukturen erstellen, navigieren und sicher manipulieren.

## Was sind Nested Dictionaries?

**Nested Dictionaries** (verschachtelte Dictionaries) sind Dictionaries, die andere Dictionaries, Listen oder komplexe Strukturen als Values enthalten.

**Charakteristiken:**
- Beliebig tiefe Verschachtelung möglich
- Ideal für hierarchische Daten (JSON-ähnlich)
- Flexibles Datenmodell
- Natürliche Repräsentation von Objekten

**Typische Anwendungsfälle:**
- Konfigurationsdateien
- API-Responses (JSON)
- Datenbank-ähnliche Strukturen
- Hierarchische Beziehungen
- Komplexe Objekt-Modelle

---

## Grundlegende Strukturen

### Dictionary in Dictionary

```python
# Einfache Verschachtelung
person = {
    "name": "Max Mustermann",
    "alter": 30,
    "adresse": {
        "strasse": "Hauptstraße 123",
        "plz": "10115",
        "stadt": "Berlin",
        "land": "Deutschland"
    },
    "kontakt": {
        "email": "max@example.com",
        "telefon": "+49 123 456789"
    }
}

# Zugriff auf verschachtelte Werte
print(person["adresse"]["stadt"])  # Berlin
print(person["kontakt"]["email"])  # max@example.com

# Mehrstufiger Zugriff
stadt = person["adresse"]["stadt"]
```

### Dictionary mit Listen

```python
# Dictionaries können Listen enthalten
mitarbeiter = {
    "name": "Anna Schmidt",
    "position": "Entwicklerin",
    "skills": ["Python", "JavaScript", "SQL"],
    "projekte": [
        {"name": "Projekt A", "status": "abgeschlossen"},
        {"name": "Projekt B", "status": "in Arbeit"}
    ],
    "sprachen": {
        "deutsch": "Muttersprache",
        "englisch": "fließend",
        "spanisch": "Grundkenntnisse"
    }
}

# Zugriff auf Listen-Elemente
print(mitarbeiter["skills"][0])  # Python
print(mitarbeiter["projekte"][1]["status"])  # in Arbeit
```

### Liste von Dictionaries

```python
# Sehr häufiges Pattern: Liste von Objekten
users = [
    {
        "id": 1,
        "name": "Max",
        "alter": 30,
        "aktiv": True
    },
    {
        "id": 2,
        "name": "Anna",
        "alter": 25,
        "aktiv": True
    },
    {
        "id": 3,
        "name": "Tom",
        "alter": 35,
        "aktiv": False
    }
]

# Iteration über User
for user in users:
    if user["aktiv"]:
        print(f"{user['name']} ist aktiv")

# Suche nach ID
user = next((u for u in users if u["id"] == 2), None)
print(user["name"])  # Anna
```

---

## Erstellen verschachtelter Strukturen

### Manuell

```python
# Schrittweise aufbauen
firma = {}
firma["name"] = "TechCorp"
firma["gruendung"] = 2010

# Nested Dict hinzufügen
firma["standorte"] = {}
firma["standorte"]["berlin"] = {
    "adresse": "Hauptstraße 1",
    "mitarbeiter": 50
}
firma["standorte"]["muenchen"] = {
    "adresse": "Bahnhofstraße 2",
    "mitarbeiter": 30
}

print(firma)
```

### Mit Literal-Syntax

```python
# Alles auf einmal definieren
firma = {
    "name": "TechCorp",
    "gruendung": 2010,
    "standorte": {
        "berlin": {
            "adresse": "Hauptstraße 1",
            "mitarbeiter": 50,
            "abteilungen": ["IT", "Vertrieb", "HR"]
        },
        "muenchen": {
            "adresse": "Bahnhofstraße 2",
            "mitarbeiter": 30,
            "abteilungen": ["IT", "Marketing"]
        }
    },
    "produkte": [
        {"name": "App A", "preis": 9.99},
        {"name": "App B", "preis": 14.99}
    ]
}
```

### Mit Comprehensions

```python
# Nested Dict mit Comprehension
mitarbeiter_ids = [1, 2, 3, 4, 5]

mitarbeiter_db = {
    uid: {
        "name": f"User_{uid}",
        "email": f"user{uid}@example.com",
        "aktiv": uid % 2 == 0,  # Gerade IDs sind aktiv
        "settings": {
            "theme": "dark",
            "notifications": True
        }
    }
    for uid in mitarbeiter_ids
}

print(mitarbeiter_db[2])
# {'name': 'User_2', 'email': 'user2@example.com', 
#  'aktiv': True, 'settings': {'theme': 'dark', 'notifications': True}}

# Multiplikationstabelle als Nested Dict
tabelle = {
    i: {j: i * j for j in range(1, 6)}
    for i in range(1, 6)
}

print(tabelle[3][4])  # 12
```

---

## Zugriff auf verschachtelte Daten

### Direkter Zugriff

```python
data = {
    "user": {
        "profile": {
            "name": "Max",
            "settings": {
                "theme": "dark",
                "language": "de"
            }
        }
    }
}

# Kettenzugriff
theme = data["user"]["profile"]["settings"]["theme"]
print(theme)  # dark

# Problem: KeyError bei fehlendem Key
try:
    wert = data["user"]["profile"]["missing"]["key"]
except KeyError as e:
    print(f"Key nicht gefunden: {e}")
```

### Sicherer Zugriff mit get()

```python
data = {
    "user": {
        "profile": {
            "name": "Max"
        }
    }
}

# Mit verschachtelten get() Aufrufen
theme = data.get("user", {}).get("profile", {}).get("settings", {}).get("theme")
print(theme)  # None (statt KeyError)

theme = (data.get("user", {})
             .get("profile", {})
             .get("settings", {})
             .get("theme", "light"))
print(theme)  # light (Default)

# Helper-Funktion für tiefen Zugriff
def safe_get(data, *keys, default=None):
    """Sicherer Zugriff auf verschachtelte Dictionaries"""
    for key in keys:
        if isinstance(data, dict):
            data = data.get(key, default)
        else:
            return default
    return data

# Verwendung
theme = safe_get(data, "user", "profile", "settings", "theme", default="light")
print(theme)  # light

name = safe_get(data, "user", "profile", "name")
print(name)  # Max
```

### Mit try-except

```python
data = {
    "level1": {
        "level2": {
            "level3": "Wert"
        }
    }
}

# Robuste Fehlerbehandlung
try:
    wert = data["level1"]["level2"]["level3"]["level4"]
except (KeyError, TypeError) as e:
    print(f"Zugriff fehlgeschlagen: {e}")
    wert = None
```

---

## Manipulation verschachtelter Strukturen

### Werte hinzufügen/ändern

```python
firma = {
    "name": "TechCorp",
    "standorte": {
        "berlin": {"mitarbeiter": 50}
    }
}

# Neuen Standort hinzufügen
firma["standorte"]["hamburg"] = {
    "mitarbeiter": 25,
    "gruendung": 2020
}

# Bestehenden Wert ändern
firma["standorte"]["berlin"]["mitarbeiter"] = 55

# Tief verschachtelten Wert ändern
if "berlin" in firma["standorte"]:
    firma["standorte"]["berlin"]["budget"] = 1_000_000

print(firma)
```

### Sicheres Einfügen mit setdefault()

```python
firma = {"name": "TechCorp"}

# Stelle sicher, dass "standorte" existiert
firma.setdefault("standorte", {})

# Füge Berlin hinzu
firma["standorte"].setdefault("berlin", {})
firma["standorte"]["berlin"]["mitarbeiter"] = 50

# Noch sicherer: Verkettete setdefault
firma.setdefault("standorte", {}).setdefault("muenchen", {})["mitarbeiter"] = 30

print(firma)
```

### Werte löschen

```python
firma = {
    "name": "TechCorp",
    "standorte": {
        "berlin": {"mitarbeiter": 50},
        "muenchen": {"mitarbeiter": 30},
        "hamburg": {"mitarbeiter": 25}
    }
}

# Einzelnen Wert löschen
del firma["standorte"]["hamburg"]

# Mit pop() (gibt Wert zurück)
berlin = firma["standorte"].pop("berlin")
print(f"Gelöscht: {berlin}")

# Sicher löschen (kein KeyError)
koeln = firma["standorte"].pop("koeln", None)
print(f"Koeln: {koeln}")  # None

print(firma)
# {'name': 'TechCorp', 'standorte': {'muenchen': {'mitarbeiter': 30}}}
```

---

## Praktische Anwendungen

### 1. Konfigurationsverwaltung

```python
# App-Konfiguration mit Defaults und Overrides
default_config = {
    "app": {
        "name": "MyApp",
        "version": "1.0.0",
        "debug": False
    },
    "database": {
        "host": "localhost",
        "port": 5432,
        "name": "myapp_db",
        "pool": {
            "min_size": 5,
            "max_size": 20
        }
    },
    "features": {
        "auth": {
            "enabled": True,
            "provider": "oauth",
            "timeout": 3600
        },
        "cache": {
            "enabled": True,
            "ttl": 300
        }
    }
}

# User-Overrides
user_config = {
    "app": {
        "debug": True
    },
    "database": {
        "host": "prod-db.example.com",
        "pool": {
            "max_size": 50
        }
    }
}

# Merge-Funktion für verschachtelte Dicts
def deep_merge(base, override):
    """Rekursives Mergen von Dictionaries"""
    result = base.copy()
    for key, value in override.items():
        if key in result and isinstance(result[key], dict) and isinstance(value, dict):
            result[key] = deep_merge(result[key], value)
        else:
            result[key] = value
    return result

config = deep_merge(default_config, user_config)
print(config["app"]["debug"])  # True
print(config["database"]["pool"]["max_size"])  # 50
print(config["database"]["pool"]["min_size"])  # 5 (von default)
```

### 2. JSON-ähnliche Datenstrukturen

```python
# API Response simulieren
api_response = {
    "status": "success",
    "data": {
        "user": {
            "id": 123,
            "username": "max_dev",
            "profile": {
                "name": "Max Mustermann",
                "email": "max@example.com",
                "avatar": "https://example.com/avatar.jpg"
            },
            "stats": {
                "posts": 42,
                "followers": 1337,
                "following": 256
            }
        },
        "posts": [
            {
                "id": 1,
                "title": "Python Tipps",
                "likes": 120,
                "comments": [
                    {"user": "anna", "text": "Super!"},
                    {"user": "tom", "text": "Hilfreich"}
                ]
            },
            {
                "id": 2,
                "title": "Dict Comprehensions",
                "likes": 95,
                "comments": []
            }
        ]
    },
    "meta": {
        "timestamp": "2025-10-01T12:00:00Z",
        "api_version": "2.0"
    }
}

# Daten extrahieren
username = api_response["data"]["user"]["username"]
follower_count = api_response["data"]["user"]["stats"]["followers"]
first_post_likes = api_response["data"]["posts"][0]["likes"]

print(f"{username} hat {follower_count} Follower")
print(f"Erster Post: {first_post_likes} Likes")
```

### 3. Hierarchische Datenmodellierung

```python
# Organisations-Hierarchie
organisation = {
    "CEO": {
        "name": "Dr. Schmidt",
        "email": "schmidt@example.com",
        "untergebene": {
            "CTO": {
                "name": "Max Müller",
                "email": "mueller@example.com",
                "untergebene": {
                    "Team Lead Backend": {
                        "name": "Anna Weber",
                        "team": ["Dev1", "Dev2", "Dev3"]
                    },
                    "Team Lead Frontend": {
                        "name": "Tom Klein",
                        "team": ["Dev4", "Dev5"]
                    }
                }
            },
            "CFO": {
                "name": "Lisa Braun",
                "email": "braun@example.com",
                "untergebene": {}
            }
        }
    }
}

# Rekursive Funktion zum Zählen aller Mitarbeiter
def zaehle_mitarbeiter(org_unit):
    """Zählt alle Mitarbeiter rekursiv"""
    count = 1  # Die Person selbst
    
    if "untergebene" in org_unit:
        for untergebener in org_unit["untergebene"].values():
            count += zaehle_mitarbeiter(untergebener)
    
    if "team" in org_unit:
        count += len(org_unit["team"])
    
    return count

gesamt = zaehle_mitarbeiter(organisation["CEO"])
print(f"Gesamt-Mitarbeiter: {gesamt}")  # 11
```

### 4. Datenbank-ähnliche Strukturen

```python
# In-Memory "Datenbank"
database = {
    "users": {
        1: {"name": "Max", "email": "max@example.com", "rolle": "admin"},
        2: {"name": "Anna", "email": "anna@example.com", "rolle": "user"},
        3: {"name": "Tom", "email": "tom@example.com", "rolle": "user"}
    },
    "posts": {
        101: {"user_id": 1, "titel": "Python Basics", "likes": 50},
        102: {"user_id": 2, "titel": "Dict Tutorial", "likes": 75},
        103: {"user_id": 1, "titel": "Advanced Topics", "likes": 120}
    },
    "comments": {
        1001: {"post_id": 101, "user_id": 2, "text": "Toll!"},
        1002: {"post_id": 101, "user_id": 3, "text": "Sehr hilfreich"},
        1003: {"post_id": 102, "user_id": 1, "text": "Gut erklärt"}
    }
}

# Query-Funktionen
def get_user_posts(user_id):
    """Alle Posts eines Users"""
    return {
        pid: post 
        for pid, post in database["posts"].items() 
        if post["user_id"] == user_id
    }

def get_post_with_comments(post_id):
    """Post mit allen Kommentaren"""
    post = database["posts"].get(post_id)
    if not post:
        return None
    
    comments = [
        comment 
        for comment in database["comments"].values()
        if comment["post_id"] == post_id
    ]
    
    return {
        "post": post,
        "comments": comments,
        "comment_count": len(comments)
    }

# Verwendung
max_posts = get_user_posts(1)
print(f"Max hat {len(max_posts)} Posts")

post_detail = get_post_with_comments(101)
print(f"Post hat {post_detail['comment_count']} Kommentare")
```

### 5. Spielzustand speichern

```python
# RPG Spiel-State
game_state = {
    "spieler": {
        "name": "Held123",
        "level": 15,
        "stats": {
            "leben": 100,
            "mana": 75,
            "staerke": 20,
            "intelligenz": 18
        },
        "inventar": [
            {"item": "Schwert", "schaden": 25, "seltenheit": "episch"},
            {"item": "Trank", "heilung": 50, "anzahl": 3},
            {"item": "Ruestung", "verteidigung": 30}
        ],
        "position": {"x": 100, "y": 250, "map": "wald"}
    },
    "quests": {
        "aktiv": [
            {
                "id": 1,
                "titel": "Rette das Dorf",
                "fortschritt": {"schritt": 2, "von": 5},
                "belohnung": {"gold": 500, "xp": 1000}
            }
        ],
        "abgeschlossen": [
            {"id": 0, "titel": "Tutorial", "belohnung_erhalten": True}
        ]
    },
    "welt": {
        "zeit": "Tag",
        "wetter": "Sonnig",
        "npcs": {
            "haendler_1": {
                "name": "Gareth",
                "position": {"x": 105, "y": 245},
                "inventar": ["Trank", "Ruestung", "Ring"]
            }
        }
    }
}

# Spieler-Leben aktualisieren
game_state["spieler"]["stats"]["leben"] -= 20
print(f"Leben: {game_state['spieler']['stats']['leben']}")

# Item zum Inventar hinzufügen
neues_item = {"item": "Ring", "bonus": "staerke+5"}
game_state["spieler"]["inventar"].append(neues_item)

# Quest-Fortschritt aktualisieren
game_state["quests"]["aktiv"][0]["fortschritt"]["schritt"] = 3
```

### 6. Statistiken und Metriken

```python
# Verkaufs-Statistiken
sales_data = {
    "2024": {
        "Q1": {
            "januar": {"umsatz": 50000, "kunden": 120, "bestellungen": 450},
            "februar": {"umsatz": 55000, "kunden": 135, "bestellungen": 480},
            "maerz": {"umsatz": 60000, "kunden": 150, "bestellungen": 520}
        },
        "Q2": {
            "april": {"umsatz": 58000, "kunden": 140, "bestellungen": 500},
            "mai": {"umsatz": 62000, "kunden": 155, "bestellungen": 540},
            "juni": {"umsatz": 65000, "kunden": 160, "bestellungen": 560}
        }
    }
}

# Gesamt-Umsatz Q1 berechnen
q1_umsatz = sum(
    monat["umsatz"] 
    for monat in sales_data["2024"]["Q1"].values()
)
print(f"Q1 Umsatz: {q1_umsatz:,} €")  # 165,000 €

# Durchschnittliche Bestellungen pro Monat
alle_bestellungen = [
    monat["bestellungen"]
    for quartal in sales_data["2024"].values()
    for monat in quartal.values()
]
durchschnitt = sum(alle_bestellungen) / len(alle_bestellungen)
print(f"Ø Bestellungen: {durchschnitt:.1f}")  # 508.3
```

---

## Häufige Fallstricke

### ⚠️ Fallstrick 1: Shallow Copy bei Nested Dicts

```python
original = {
    "user": "Max",
    "settings": {"theme": "dark", "lang": "de"}
}

# ❌ Shallow Copy kopiert nur oberste Ebene
kopie = original.copy()
kopie["settings"]["theme"] = "light"

print(original["settings"]["theme"])  # light - Auch geändert!
print(kopie is original)  # False
print(kopie["settings"] is original["settings"])  # True - Selbes Objekt!

# ✅ Deep Copy für vollständige Kopie
import copy

original = {
    "user": "Max",
    "settings": {"theme": "dark", "lang": "de"}
}

kopie = copy.deepcopy(original)
kopie["settings"]["theme"] = "light"

print(original["settings"]["theme"])  # dark - Unverändert
print(kopie["settings"] is original["settings"])  # False
```

### ⚠️ Fallstrick 2: KeyError bei fehlendem Pfad

```python
data = {"user": {"profile": {"name": "Max"}}}

# ❌ FALSCH - KeyError wenn Pfad nicht existiert
try:
    theme = data["user"]["settings"]["theme"]
except KeyError as e:
    print(f"KeyError: {e}")

# ✅ RICHTIG - Sicherer Zugriff
theme = data.get("user", {}).get("settings", {}).get("theme", "light")
print(theme)  # light

# Oder: Helper-Funktion verwenden
def safe_get(d, *keys, default=None):
    for key in keys:
        if isinstance(d, dict):
            d = d.get(key)
            if d is None:
                return default
        else:
            return default
    return d

theme = safe_get(data, "user", "settings", "theme", default="light")
```

### ⚠️ Fallstrick 3: Mutable Default Arguments

```python
# ❌ FALSCH - Defaults werden geteilt!
def create_user(name, settings={}):
    settings["name"] = name
    return settings

user1 = create_user("Max")
user2 = create_user("Anna")

print(user1)  # {'name': 'Anna'} - Ups!
print(user2)  # {'name': 'Anna'}
print(user1 is user2)  # True - Selbes Dict!

# ✅ RICHTIG
def create_user_correct(name, settings=None):
    if settings is None:
        settings = {}
    settings["name"] = name
    return settings

user1 = create_user_correct("Max")
user2 = create_user_correct("Anna")

print(user1)  # {'name': 'Max'}
print(user2)  # {'name': 'Anna'}
print(user1 is user2)  # False
```

### ⚠️ Fallstrick 4: Iteration während Änderung

```python
users = {
    1: {"name": "Max", "aktiv": True},
    2: {"name": "Anna", "aktiv": False},
    3: {"name": "Tom", "aktiv": False}
}

# ❌ FALSCH - RuntimeError
try:
    for uid, user in users.items():
        if not user["aktiv"]:
            del users[uid]
except RuntimeError as e:
    print(f"Fehler: {e}")

# ✅ RICHTIG - Liste der zu löschenden Keys
users = {
    1: {"name": "Max", "aktiv": True},
    2: {"name": "Anna", "aktiv": False},
    3: {"name": "Tom", "aktiv": False}
}

zu_loeschen = [uid for uid, user in users.items() if not user["aktiv"]]
for uid in zu_loeschen:
    del users[uid]

print(users)  # {1: {'name': 'Max', 'aktiv': True}}

# ODER: Neues Dict erstellen
aktive = {
    uid: user 
    for uid, user in users.items() 
    if user["aktiv"]
}
```

---

## Hilfsfunktionen für Nested Dicts

### Rekursives Durchsuchen

```python
def find_key(data, target_key):
    """Findet alle Vorkommen eines Keys in nested Dict"""
    results = []
    
    def search(d, path=""):
        if isinstance(d, dict):
            for key, value in d.items():
                current_path = f"{path}.{key}" if path else key
                if key == target_key:
                    results.append((current_path, value))
                search(value, current_path)
        elif isinstance(d, list):
            for i, item in enumerate(d):
                search(item, f"{path}[{i}]")
    
    search(data)
    return results

# Beispiel
data = {
    "user": {
        "name": "Max",
        "settings": {"name": "Dark Theme"}
    },
    "posts": [
        {"name": "Post 1"},
        {"name": "Post 2"}
    ]
}

# Finde alle "name" Keys
names = find_key(data, "name")
for path, value in names:
    print(f"{path}: {value}")

# user.name: Max
# user.settings.name: Dark Theme
# posts[0].name: Post 1
# posts[1].name: Post 2
```

### Flach machen (Flatten)

```python
def flatten_dict(d, parent_key='', sep='_'):
    """Macht nested Dict flach"""
    items = []
    for k, v in d.items():
        new_key = f"{parent_key}{sep}{k}" if parent_key else k
        if isinstance(v, dict):
            items.extend(flatten_dict(v, new_key, sep=sep).items())
        else:
            items.append((new_key, v))
    return dict(items)

# Beispiel
nested = {
    "user": {
        "name": "Max",
        "profile": {
            "age": 30,
            "city": "Berlin"
        }
    }
}

flat = flatten_dict(nested)
print(flat)
# {'user_name': 'Max', 'user_profile_age': 30, 'user_profile_city': 'Berlin'}
```

### Validierung

```python
def validate_structure(data, schema):
    """Validiert nested Dict gegen Schema"""
    def check(d, s, path=""):
        if not isinstance(s, dict):
            return isinstance(d, s)
        
        for key, expected_type in s.items():
            if key not in d:
                print(f"Fehlt: {path}.{key}")
                return False
            
            current = d[key]
            current_path = f"{path}.{key}" if path else key
            
            if isinstance(expected_type, dict):
                if not check(current, expected_type, current_path):
                    return False
            elif not isinstance(current, expected_type):
                print(f"Typ-Fehler bei {current_path}: "
                      f"erwartet {expected_type}, gefunden {type(current)}")
                return False
        
        return True
    
    return check(data, schema)

# Schema definieren
schema = {
    "user": {
        "name": str,
        "age": int,
        "settings": {
            "theme": str
        }
    }
}

# Gültige Daten
valid_data = {
    "user": {
        "name": "Max",
        "age": 30,
        "settings": {"theme": "dark"}
    }
}

# Ungültige Daten
invalid_data = {
    "user": {
        "name": "Max",
        "age": "30",  # String statt int
        "settings": {"theme": "dark"}
    }
}

print(validate_structure(valid_data, schema))    # True
print(validate_structure(invalid_data, schema))  # False
```

---

## Performance-Überlegungen

```python
import sys

# Flaches vs. Nested Dict
flach = {f"key_{i}": i for i in range(1000)}
nested = {f"level1_{i}": {"level2": i} for i in range(1000)}

print(f"Flach: {sys.getsizeof(flach)} bytes")
print(f"Nested: {sys.getsizeof(nested)} bytes")

# Nested Dicts brauchen mehr Speicher
# Aber: Bessere semantische Struktur

# Zugriff ist bei Nested minimal langsamer
# flach["key_500"] vs. nested["level1_500"]["level2"]
```

---

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [Mapping Types — dict](https://docs.python.org/3/library/stdtypes.html#mapping-types-dict) - Dictionary Referenz
- [copy — Shallow and deep copy](https://docs.python.org/3/library/copy.html) - Für deepcopy bei Nested Structures
- [json module](https://docs.python.org/3/library/json.html) - JSON arbeitet mit nested dicts
- [Data Structures](https://docs.python.org/3/tutorial/datastructures.html) - Tutorial mit Beispielen

---

## Verwandte Themen

- [[03_Mappings_und_Sets/01_Dictionaries/03_Dict Comprehensions|Dict Comprehensions]] - Nested Dicts mit Comprehensions
- [[03_Mappings_und_Sets/01_Dictionaries/02_Dict Methoden|Dict Methoden]] - Methoden für Dict-Operationen
- [[03_Mappings_und_Sets/01_Dictionaries/05_Dict Views|Dict Views]] - Views auf Dict-Inhalte
- [[04_Fortgeschritten/04_Dataclasses/01_Dataclass Basics|Dataclass Basics]] - Alternative zu Nested Dicts

---

← [[03_Mappings_und_Sets/01_Dictionaries/03_Dict Comprehensions|Dict Comprehensions]] | [[INDEX|📑 Index]] | [[03_Mappings_und_Sets/01_Dictionaries/05_Dict Views|Dict Views]] →