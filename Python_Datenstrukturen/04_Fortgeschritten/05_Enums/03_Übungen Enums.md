---
tags: [python, datenstrukturen, enums, übungen]
created: 2025-10-02
---

← [[04_Fortgeschritten/05_Enums/02_Enum Typen|Enum Typen]] | [[INDEX|📑 Index]] | [[05_Pattern_Matching/01_Grundlagen/01_Match Statement|Match Statement]] →

# Übungen Enums

> [!tip] Lernziel
> Praktische Anwendung aller Enum-Konzepte durch umfassende Übungsaufgaben.

## Übung 1: Schachfiguren (Leicht)

Erstelle ein System für Schachfiguren:
- Enum `PieceType`: PAWN, KNIGHT, BISHOP, ROOK, QUEEN, KING
- Enum `Color`: WHITE, BLACK
- Klasse `ChessPiece` mit Typ und Farbe
- Methode `get_value()` die Materialwert zurückgibt (Bauer=1, Springer/Läufer=3, Turm=5, Dame=9, König=unendlich)

**Erwarteter Output:**
```python
piece1 = ChessPiece(PieceType.QUEEN, Color.WHITE)
print(piece1.get_value())      # 9
print(piece1)                  # "White Queen"

piece2 = ChessPiece(PieceType.PAWN, Color.BLACK)
print(piece2.get_value())      # 1
print(piece2.is_major_piece()) # False (nur Dame, Turm, König)
```

---

## Übung 2: Bestellstatus-System (Leicht)

Erstelle ein Bestellstatus-System:
- Enum `OrderStatus`: PENDING, CONFIRMED, SHIPPED, DELIVERED, CANCELLED
- Jeder Status kennt seine erlaubten Folge-Status
- Methode `can_transition_to(new_status)` prüft Gültigkeit
- Methode `is_final()` prüft ob Status final ist

**Erwarteter Output:**
```python
status = OrderStatus.PENDING
print(status.can_transition_to(OrderStatus.CONFIRMED))  # True
print(status.can_transition_to(OrderStatus.DELIVERED))  # False

print(OrderStatus.DELIVERED.is_final())    # True
print(OrderStatus.SHIPPED.is_final())      # False
```

---

## Übung 3: Währungen mit IntEnum (Mittel)

Erstelle ein `Currency` IntEnum für verschiedene Währungen mit ihren ISO-Codes als Werten:
- EUR = 978
- USD = 840
- GBP = 826
- JPY = 392

Implementiere Klasse `Money` mit:
- Betrag und Währung
- Methode `convert_to(target_currency, rates)` für Umrechnung
- Methode `format()` für formatierte Ausgabe mit Währungssymbol

**Erwarteter Output:**
```python
money = Money(100, Currency.EUR)
print(money.format())  # "100.00 €"

rates = {
    (Currency.EUR, Currency.USD): 1.10,
    (Currency.USD, Currency.EUR): 0.91
}
money_usd = money.convert_to(Currency.USD, rates)
print(money_usd.format())  # "110.00 $"
```

---

## Übung 4: Content-Type mit StrEnum (Mittel)

Erstelle ein `ContentType` StrEnum für HTTP Content-Types:
- JSON = "application/json"
- XML = "application/xml"
- HTML = "text/html"
- PLAIN = "text/plain"
- PDF = "application/pdf"

Implementiere:
- Methode `is_text()` - prüft ob text/*
- Methode `get_extension()` - gibt Dateiendung zurück
- Funktion `parse_content_type(header: str)` die Content-Type Header parst

**Erwarteter Output:**
```python
ct = ContentType.JSON
print(ct.is_text())           # False
print(ct.get_extension())     # ".json"

ct2 = ContentType.HTML
print(ct2.is_text())          # True

parsed = parse_content_type("application/json; charset=utf-8")
print(parsed == ContentType.JSON)  # True
```

---

## Übung 5: Dateisystem-Berechtigungen mit Flag (Mittel)

Erstelle ein vollständiges Dateisystem-Berechtigungs-System:
- Flag `Permission`: READ, WRITE, EXECUTE
- Flag `UserType`: OWNER, GROUP, OTHER
- Klasse `FilePermissions` die alle Kombinationen verwaltet
- Methode `check(user_type, permission)` prüft Berechtigung
- Methode `chmod(mode_string)` parst "rwxr-xr--" Format

**Erwarteter Output:**
```python
perms = FilePermissions.from_string("rwxr-xr--")
print(perms.check(UserType.OWNER, Permission.WRITE))   # True
print(perms.check(UserType.OTHER, Permission.WRITE))   # False
print(perms.to_octal())                                # "754"
```

---

## Übung 6: Feature-Toggle-System (Mittel)

Erstelle ein Feature-Toggle-System mit:
- Flag `Feature`: SEARCH, COMMENTS, ANALYTICS, DARK_MODE, BETA_FEATURES
- Klasse `FeatureManager` mit Methoden:
  - `enable(features)` - aktiviert Features
  - `disable(features)` - deaktiviert Features
  - `is_enabled(feature)` - prüft einzelnes Feature
  - `get_enabled()` - gibt Liste aller aktiven Features
  - `load_from_config(config_dict)` - lädt aus Dictionary

**Erwarteter Output:**
```python
fm = FeatureManager()
fm.enable(Feature.SEARCH | Feature.COMMENTS)
print(fm.is_enabled(Feature.SEARCH))      # True
print(fm.is_enabled(Feature.DARK_MODE))   # False

fm.disable(Feature.SEARCH)
print(list(fm.get_enabled()))             # [Feature.COMMENTS]

config = {"dark_mode": True, "analytics": True}
fm.load_from_config(config)
print(fm.is_enabled(Feature.DARK_MODE))   # True
```

---

## Übung 7: Poker-Hand-Evaluator (Schwer)

Erstelle ein Poker-System:
- Enum `Suit`: HEARTS, DIAMONDS, CLUBS, SPADES
- IntEnum `Rank`: TWO=2, THREE=3, ..., ACE=14
- Enum `HandType`: HIGH_CARD, PAIR, TWO_PAIR, THREE_KIND, STRAIGHT, FLUSH, FULL_HOUSE, FOUR_KIND, STRAIGHT_FLUSH
- Klasse `Card` mit Rank und Suit
- Klasse `PokerHand` mit 5 Karten
- Methode `evaluate()` die HandType bestimmt

**Erwarteter Output:**
```python
hand = PokerHand([
    Card(Rank.ACE, Suit.HEARTS),
    Card(Rank.KING, Suit.HEARTS),
    Card(Rank.QUEEN, Suit.HEARTS),
    Card(Rank.JACK, Suit.HEARTS),
    Card(Rank.TEN, Suit.HEARTS)
])
print(hand.evaluate())  # HandType.STRAIGHT_FLUSH

hand2 = PokerHand([
    Card(Rank.ACE, Suit.HEARTS),
    Card(Rank.ACE, Suit.DIAMONDS),
    Card(Rank.KING, Suit.CLUBS),
    Card(Rank.QUEEN, Suit.SPADES),
    Card(Rank.JACK, Suit.HEARTS)
])
print(hand2.evaluate())  # HandType.PAIR
```

---

## Übung 8: Task-Management-System (Schwer)

Erstelle ein vollständiges Task-Management-System:
- IntEnum `Priority`: LOWEST=1, LOW=2, MEDIUM=3, HIGH=4, CRITICAL=5
- StrEnum `Status`: TODO="todo", IN_PROGRESS="in_progress", REVIEW="review", DONE="done"
- Flag `Label`: BUG, FEATURE, DOCS, REFACTOR, TEST
- Klasse `Task` mit allen Attributen
- Methode `should_escalate()` - True wenn überfällig oder CRITICAL
- Methode `to_dict()` - serialisiert zu Dictionary
- Funktion `filter_tasks(tasks, **criteria)` für flexible Filterung

**Erwarteter Output:**
```python
task = Task(
    title="Fix login bug",
    priority=Priority.CRITICAL,
    status=Status.IN_PROGRESS,
    labels=Label.BUG | Label.URGENT,
    due_date="2025-10-05"
)

print(task.should_escalate())  # True (CRITICAL)
print(task.has_label(Label.BUG))  # True

tasks = [task1, task2, task3, task4]
critical = filter_tasks(tasks, priority=Priority.CRITICAL)
bugs = filter_tasks(tasks, label=Label.BUG)
in_progress = filter_tasks(tasks, status=Status.IN_PROGRESS)
```

---

## Übung 9: State Machine für Workflow (Schwer)

Erstelle eine State Machine für einen Genehmigungsprozess:
- Enum `State`: DRAFT, SUBMITTED, REVIEWING, APPROVED, REJECTED, REVISION_NEEDED
- Jeder State definiert erlaubte Transitionen
- Klasse `Workflow` mit State-Management
- Methode `transition(new_state, user_role)` prüft Berechtigung
- Event-Log für alle Übergänge
- Methode `can_approve(user_role)` - nur MANAGER können genehmigen

**Erwarteter Output:**
```python
workflow = Workflow(document_id="DOC-123")
print(workflow.current_state)  # State.DRAFT

workflow.transition(State.SUBMITTED, UserRole.EMPLOYEE)  # OK
workflow.transition(State.APPROVED, UserRole.EMPLOYEE)   # Error: Keine Berechtigung

workflow.transition(State.REVIEWING, UserRole.MANAGER)   # OK
workflow.transition(State.APPROVED, UserRole.MANAGER)    # OK

print(workflow.get_history())  # Alle Transitionen mit Timestamps
```

---

## Übung 10: Multi-Language Support mit StrEnum (Schwer)

Erstelle ein mehrsprachiges System:
- StrEnum `Language`: DE="de", EN="en", FR="fr", ES="es"
- StrEnum `TranslationKey`: WELCOME, GOODBYE, ERROR, ...
- Klasse `Translator` die Übersetzungen verwaltet
- Methode `t(key, lang, **kwargs)` übersetzt mit Platzhaltern
- Methode `load_from_json(filepath)` lädt Übersetzungen
- Fallback auf Englisch wenn Übersetzung fehlt

**Erwarteter Output:**
```python
translator = Translator()
translator.load_translations({
    Language.DE: {
        TranslationKey.WELCOME: "Willkommen, {name}!",
        TranslationKey.ERROR: "Fehler: {message}"
    },
    Language.EN: {
        TranslationKey.WELCOME: "Welcome, {name}!",
        TranslationKey.ERROR: "Error: {message}"
    }
})

print(translator.t(TranslationKey.WELCOME, Language.DE, name="Max"))
# "Willkommen, Max!"

print(translator.t(TranslationKey.WELCOME, Language.EN, name="John"))
# "Welcome, John!"

# Nicht übersetzter Key fällt auf Englisch zurück
print(translator.t(TranslationKey.MISSING, Language.DE))
# "[EN] Missing translation key"
```

---

## Übung 11: Netzwerk-Protokoll mit IntFlag (Sehr Schwer)

Erstelle ein TCP-Flags-System:
- IntFlag `TCPFlag`: FIN=1, SYN=2, RST=4, PSH=8, ACK=16, URG=32
- Klasse `TCPPacket` mit Flags
- Methode `is_connection_start()` - SYN ohne ACK
- Methode `is_connection_end()` - FIN mit ACK
- Methode `is_acknowledgment()` - ACK gesetzt
- Funktion `parse_flags(binary_string)` parst "100100" zu Flags

**Erwarteter Output:**
```python
# SYN Packet (Connection Start)
syn = TCPPacket(flags=TCPFlag.SYN)
print(syn.is_connection_start())  # True

# SYN-ACK Packet
syn_ack = TCPPacket(flags=TCPFlag.SYN | TCPFlag.ACK)
print(syn_ack.is_connection_start())  # False
print(syn_ack.is_acknowledgment())    # True

# FIN-ACK Packet (Connection End)
fin_ack = TCPPacket(flags=TCPFlag.FIN | TCPFlag.ACK)
print(fin_ack.is_connection_end())  # True

# Aus Binär-String parsen
flags = parse_flags("010010")  # Bit 1 und 4 gesetzt
print(TCPFlag.SYN in flags)    # True
print(TCPFlag.ACK in flags)    # True
```

---

## Übung 12: Game State Machine (Sehr Schwer)

Erstelle ein komplettes Spiel-State-System für ein RPG:
- Enum `GameState`: MENU, LOADING, PLAYING, PAUSED, INVENTORY, DIALOGUE, COMBAT, GAME_OVER
- Enum `CombatState`: PLAYER_TURN, ENEMY_TURN, ANIMATION, VICTORY, DEFEAT
- Flag `GameFlag`: TUTORIAL_DONE, BOSS_DEFEATED, SECRET_FOUND, HARD_MODE
- Klasse `GameStateMachine` mit verschachtelten States
- Sub-State-Machine für Combat
- State-History und Undo-Funktion
- Event-System für State-Änderungen

**Erwarteter Output:**
```python
game = GameStateMachine()
game.start()  # MENU -> LOADING -> PLAYING

game.open_inventory()  # PLAYING -> INVENTORY
game.close_inventory() # INVENTORY -> PLAYING

game.enter_combat()    # PLAYING -> COMBAT (PLAYER_TURN)
game.combat.player_attack()  # PLAYER_TURN -> ANIMATION -> ENEMY_TURN
game.combat.enemy_defeated()  # ENEMY_TURN -> VICTORY -> PLAYING

print(game.has_flag(GameFlag.BOSS_DEFEATED))  # False
game.defeat_boss()
print(game.has_flag(GameFlag.BOSS_DEFEATED))  # True

print(game.get_state_history())  # Alle State-Änderungen
game.undo()  # Zurück zum vorherigen State
```

---

## Bonusaufgabe: API-Rate-Limiting mit IntFlag (Experte)

Erstelle ein API-Rate-Limiting-System:
- IntFlag `RateLimit`: PER_SECOND=1, PER_MINUTE=2, PER_HOUR=4, PER_DAY=8
- Enum `APITier`: FREE, BASIC, PREMIUM, ENTERPRISE
- Jeder Tier hat verschiedene Limits pro Zeiteinheit
- Klasse `RateLimiter` die Requests trackt
- Methode `can_make_request(tier, limit_type)` prüft Verfügbarkeit
- Methode `make_request(tier)` inkrementiert Counter
- Methode `reset(limit_type)` für Cleanup
- Automatisches Zurücksetzen nach Zeitablauf

**Erwarteter Output:**
```python
limiter = RateLimiter()
limiter.set_limits(APITier.FREE, {
    RateLimit.PER_SECOND: 1,
    RateLimit.PER_MINUTE: 10,
    RateLimit.PER_HOUR: 100
})

# Erste Requests OK
print(limiter.can_make_request(APITier.FREE))  # True
limiter.make_request(APITier.FREE)

# Zu viele Requests
for _ in range(10):
    limiter.make_request(APITier.FREE)

print(limiter.can_make_request(APITier.FREE))  # False (Limit erreicht)
print(limiter.time_until_reset(RateLimit.PER_MINUTE))  # Sekunden bis Reset

# Nach 60 Sekunden
limiter.reset(RateLimit.PER_MINUTE)
print(limiter.can_make_request(APITier.FREE))  # True
```

---

## Tipps für alle Übungen

### Allgemeine Hinweise:
1. **Type Hints verwenden** - Macht Code klarer und ermöglicht IDE-Support
2. **Docstrings schreiben** - Dokumentiere was jede Methode macht
3. **Assertions testen** - Validiere Eingaben wo sinnvoll
4. **repr() implementieren** - Für besseres Debugging

### Enum-Spezifische Tipps:
- Verwende `@unique` wenn Duplikate nicht erlaubt sind
- Bei Flags immer Potenzen von 2 verwenden oder `auto()`
- IntEnum nur wenn wirklich int-Kompatibilität nötig
- StrEnum für JSON/API-Schnittstellen
- Methoden direkt im Enum für Logik die zum Enum gehört

### Testing:
```python
# Beispiel Test-Setup
def test_enum_behavior():
    # Teste Singleton-Verhalten
    assert Status.ACTIVE is Status.ACTIVE
    
    # Teste Vergleiche
    assert Status.ACTIVE != Status.INACTIVE
    
    # Teste Iteration
    assert len(list(Status)) == expected_count
    
    # Teste Flag-Operationen
    flags = Flag.A | Flag.B
    assert Flag.A in flags
    assert Flag.C not in flags
```

## Lösungsstrategien

### Für State Machines:
1. Definiere alle States als Enum
2. Erstelle Transition-Dictionary: `{State: [erlaubte_nächste_States]}`
3. Validiere Transition vor Ausführung
4. Logge alle State-Änderungen für Debugging

### Für Flag-Systeme:
1. Beginne mit `NONE = 0`
2. Verwende `auto()` für automatische Werte
3. Prüfe Flags mit `flag in combined_flags`
4. Kombiniere mit `|`, entferne mit `& ~flag`

### Für komplexe Systeme:
1. Teile in kleinere Enums auf (Single Responsibility)
2. Verwende Komposition statt Vererbung
3. Implementiere `to_dict()` für Serialisierung
4. Schreibe Factory-Methoden wie `from_string()`

---

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [enum - Vollständige Dokumentation](https://docs.python.org/3/library/enum.html)
- [Enum How-to Guide](https://docs.python.org/3/howto/enum.html) - Praktische Beispiele
- [PEP 435](https://peps.python.org/pep-0435/) - Original Enum Proposal
- [typing - Type Hints](https://docs.python.org/3/library/typing.html) - Für Type Annotations

## Verwandte Themen

- [[04_Fortgeschritten/05_Enums/01_Enum Basics|Enum Basics]] - Grundlagen wiederholen
- [[04_Fortgeschritten/05_Enums/02_Enum Typen|Enum Typen]] - Spezialisierte Typen
- [[05_Pattern_Matching/01_Grundlagen/01_Match Statement|Match Statement]] - Pattern Matching mit Enums
- [[06_Praxis/04_Lösungen/Lösungen Teil 4|Lösungen Teil 4]] - Musterlösungen

---
← [[04_Fortgeschritten/05_Enums/02_Enum Typen|Enum Typen]] | [[INDEX|📑 Index]] | [[05_Pattern_Matching/01_Grundlagen/01_Match Statement|Match Statement]] →