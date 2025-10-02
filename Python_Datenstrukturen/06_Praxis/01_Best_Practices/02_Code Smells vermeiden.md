---
tags: [python, datenstrukturen, best-practices, code-quality]
created: 2025-10-02
---

← [[06_Praxis/01_Best_Practices/01_Idiomatisches Python|Idiomatisches Python]] | [[INDEX|📑 Index]] | [[06_Praxis/01_Best_Practices/03_Datenstruktur Auswahl|Datenstruktur Auswahl]] →

# Code Smells vermeiden

> [!tip] Lernziel
> Du erkennst problematische Code-Patterns (Code Smells) und kannst sie refactoren.

## Was sind Code Smells?

**Code Smells** sind Anzeichen für potenzielle Probleme im Code. Sie sind keine Bugs, aber deuten auf schlechtes Design oder Wartungsprobleme hin.

## Mutable Default Arguments

### ❌ Problem

```python
def add_to_list(item, items=[]):  # GEFÄHRLICH!
    items.append(item)
    return items

# Überraschung!
list1 = add_to_list(1)  # [1]
list2 = add_to_list(2)  # [1, 2] - Nicht [2]!
list3 = add_to_list(3)  # [1, 2, 3] - Die gleiche Liste!

print(list1)  # [1, 2, 3]
print(list2)  # [1, 2, 3]
print(list3)  # [1, 2, 3]
```

**Warum?** Default-Argumente werden nur einmal bei Funktionsdefinition evaluiert, nicht bei jedem Aufruf.

### ✅ Lösung

```python
def add_to_list(item, items=None):
    if items is None:
        items = []
    items.append(item)
    return items

list1 = add_to_list(1)  # [1]
list2 = add_to_list(2)  # [2] ✓
list3 = add_to_list(3)  # [3] ✓

# Oder: Factory-Funktion verwenden
from typing import Callable

def add_to_list_v2(item, items_factory: Callable[[], list] = list):
    items = items_factory()
    items.append(item)
    return items
```

## Catching Too Broad Exceptions

### ❌ Problem

```python
def load_config():
    try:
        with open("config.json") as f:
            config = json.load(f)
        return config
    except:  # Fängt ALLES - auch Ctrl+C!
        return {}

# Oder kaum besser:
def process_data(data):
    try:
        result = complex_operation(data)
        return result
    except Exception:  # Zu breit
        return None
```

**Warum problematisch?**
- Versteckt echte Bugs
- Schwer zu debuggen
- Fängt unerwartete Exceptions (KeyboardInterrupt, SystemExit)

### ✅ Lösung

```python
def load_config():
    try:
        with open("config.json") as f:
            config = json.load(f)
        return config
    except FileNotFoundError:
        print("Config file not found, using defaults")
        return {}
    except json.JSONDecodeError as e:
        print(f"Invalid JSON: {e}")
        return {}

# Spezifische Exceptions fangen
def parse_number(s: str) -> int | None:
    try:
        return int(s)
    except ValueError:  # Nur ValueError
        return None
```

## Not Using Context Managers

### ❌ Problem

```python
# Ressourcen manuell verwalten
def process_file(filename):
    f = open(filename)
    try:
        data = f.read()
        # Verarbeitung
        return process(data)
    finally:
        f.close()  # Kann vergessen werden!

# Mehrere Ressourcen
lock.acquire()
try:
    # Critical section
    pass
finally:
    lock.release()
```

### ✅ Lösung

```python
# Context Manager - automatisches Cleanup
def process_file(filename):
    with open(filename) as f:
        data = f.read()
        return process(data)

# Mehrere Context Manager
with open("in.txt") as inf, open("out.txt", "w") as outf:
    outf.write(inf.read())

# Eigener Context Manager
from contextlib import contextmanager

@contextmanager
def database_transaction(db):
    db.begin()
    try:
        yield db
        db.commit()
    except Exception:
        db.rollback()
        raise

with database_transaction(db) as transaction:
    transaction.execute("INSERT ...")
```

## Magic Numbers

### ❌ Problem

```python
def calculate_price(quantity):
    if quantity > 100:
        return quantity * 9.99 * 0.9  # Was bedeutet 0.9?
    return quantity * 9.99

def validate_password(password):
    if len(password) < 8:  # Warum 8?
        return False
    return True

# Später im Code
if status == 2:  # Was ist 2?
    handle_error()
```

### ✅ Lösung

```python
# Konstanten definieren
PRICE_PER_UNIT = 9.99
BULK_DISCOUNT = 0.1  # 10% Rabatt
BULK_THRESHOLD = 100
MIN_PASSWORD_LENGTH = 8

def calculate_price(quantity):
    price = quantity * PRICE_PER_UNIT
    if quantity > BULK_THRESHOLD:
        price *= (1 - BULK_DISCOUNT)
    return price

def validate_password(password):
    return len(password) >= MIN_PASSWORD_LENGTH

# Status als Enum
from enum import IntEnum

class Status(IntEnum):
    SUCCESS = 0
    WARNING = 1
    ERROR = 2

if status == Status.ERROR:
    handle_error()
```

## Long Functions

### ❌ Problem

```python
def process_order(order):
    # 100+ Zeilen Code
    # Validierung
    if not order.items:
        raise ValueError("Empty order")
    if order.total < 0:
        raise ValueError("Negative total")
    
    # Inventory check
    for item in order.items:
        if not check_stock(item):
            notify_out_of_stock(item)
            return False
    
    # Payment
    payment_result = process_payment(order.payment_info)
    if not payment_result.success:
        log_payment_failure(payment_result)
        return False
    
    # Shipping
    shipping_label = create_shipping_label(order)
    send_to_warehouse(shipping_label)
    
    # Notification
    send_confirmation_email(order.customer)
    update_inventory(order.items)
    
    # ... noch mehr Code
    return True
```

**Probleme:**
- Schwer zu verstehen
- Schwer zu testen
- Viele Verantwortlichkeiten

### ✅ Lösung

```python
def process_order(order):
    """Verarbeitet Bestellung - Hauptablauf"""
    validate_order(order)
    
    if not check_inventory(order):
        return False
    
    if not process_payment(order):
        return False
    
    ship_order(order)
    notify_customer(order)
    return True

def validate_order(order):
    """Validiert Bestellung"""
    if not order.items:
        raise ValueError("Empty order")
    if order.total < 0:
        raise ValueError("Negative total")

def check_inventory(order) -> bool:
    """Prüft Lagerbestand"""
    for item in order.items:
        if not check_stock(item):
            notify_out_of_stock(item)
            return False
    return True

# Jede Funktion hat eine klare Verantwortung
# Besser testbar, lesbarer, wartbarer
```

## God Objects

### ❌ Problem

```python
class User:
    """Macht ALLES mit Usern"""
    def __init__(self, name):
        self.name = name
        self.orders = []
        self.connection = Database()
    
    def save_to_database(self):
        self.connection.execute(...)
    
    def send_email(self, subject, body):
        smtp = SMTP(...)
        # Email-Logik
    
    def calculate_loyalty_points(self):
        # Berechnung
        pass
    
    def generate_invoice(self, order):
        # PDF-Generierung
        pass
    
    def validate_credit_card(self, card):
        # Validierung
        pass
    
    # ... 20 weitere Methoden
```

**Problem:** Eine Klasse macht zu viel (violates Single Responsibility Principle)

### ✅ Lösung

```python
# Aufteilen in kleinere, fokussierte Klassen
class User:
    """Nur User-Daten"""
    def __init__(self, name, email):
        self.name = name
        self.email = email

class UserRepository:
    """Datenbankzugriff"""
    def __init__(self, connection):
        self.connection = connection
    
    def save(self, user):
        self.connection.execute(...)
    
    def find(self, user_id):
        ...

class EmailService:
    """Email-Versand"""
    def send(self, to, subject, body):
        ...

class LoyaltyService:
    """Treuepunkte"""
    def calculate_points(self, user, orders):
        ...

class InvoiceGenerator:
    """Rechnungserstellung"""
    def generate(self, order):
        ...

# Jede Klasse hat eine klare Verantwortung
```

## Nested Conditionals

### ❌ Problem

```python
def process_request(request):
    if request:
        if request.is_valid():
            if request.user:
                if request.user.is_authenticated():
                    if request.user.has_permission():
                        # Eigentliche Logik hier
                        return handle_request(request)
                    else:
                        return "No permission"
                else:
                    return "Not authenticated"
            else:
                return "No user"
        else:
            return "Invalid request"
    else:
        return "No request"
```

**Problem:** Arrow Anti-Pattern (auch "Pyramid of Doom")

### ✅ Lösung

```python
def process_request(request):
    """Guard Clauses - früh returnen"""
    if not request:
        return "No request"
    
    if not request.is_valid():
        return "Invalid request"
    
    if not request.user:
        return "No user"
    
    if not request.user.is_authenticated():
        return "Not authenticated"
    
    if not request.user.has_permission():
        return "No permission"
    
    # Hauptlogik - nicht eingerückt!
    return handle_request(request)

# Oder mit Exceptions
def process_request_v2(request):
    validate_request(request)
    validate_user(request.user)
    return handle_request(request)

def validate_request(request):
    if not request or not request.is_valid():
        raise ValueError("Invalid request")

def validate_user(user):
    if not user or not user.is_authenticated():
        raise AuthenticationError()
    if not user.has_permission():
        raise PermissionError()
```

## Duplicate Code

### ❌ Problem

```python
def calculate_discount_a(price):
    if price > 100:
        return price * 0.1
    return 0

def calculate_discount_b(amount):
    if amount > 100:
        return amount * 0.1
    return 0

def calculate_discount_c(cost):
    if cost > 100:
        return cost * 0.1
    return 0

# Gleiche Logik, dreimal wiederholt!
```

### ✅ Lösung

```python
# DRY: Don't Repeat Yourself
DISCOUNT_THRESHOLD = 100
DISCOUNT_RATE = 0.1

def calculate_discount(price: float) -> float:
    """Berechnet Rabatt basierend auf Preis"""
    if price > DISCOUNT_THRESHOLD:
        return price * DISCOUNT_RATE
    return 0

# Einmal definieren, überall nutzen
discount_a = calculate_discount(price_a)
discount_b = calculate_discount(price_b)
discount_c = calculate_discount(price_c)

# Oder: Klasse für komplexere Logik
class DiscountCalculator:
    def __init__(self, threshold: float, rate: float):
        self.threshold = threshold
        self.rate = rate
    
    def calculate(self, price: float) -> float:
        if price > self.threshold:
            return price * self.rate
        return 0

standard_discount = DiscountCalculator(100, 0.1)
premium_discount = DiscountCalculator(50, 0.15)
```

## Boolean Parameters

### ❌ Problem

```python
def send_message(message, encrypted, compressed, logged):
    if encrypted:
        message = encrypt(message)
    if compressed:
        message = compress(message)
    if logged:
        log(message)
    # ...

# Aufruf - was bedeuten die Booleans?
send_message("Hello", True, False, True)
send_message("Hi", False, True, False)  # Unklar!
```

### ✅ Lösung

```python
# Option 1: Named Arguments
def send_message(message, *, encrypted=False, compressed=False, logged=False):
    if encrypted:
        message = encrypt(message)
    if compressed:
        message = compress(message)
    if logged:
        log(message)

# Aufruf ist klar
send_message("Hello", encrypted=True, logged=True)

# Option 2: Enum/Flags
from enum import Flag, auto

class MessageOptions(Flag):
    ENCRYPTED = auto()
    COMPRESSED = auto()
    LOGGED = auto()

def send_message_v2(message, options: MessageOptions = MessageOptions(0)):
    if MessageOptions.ENCRYPTED in options:
        message = encrypt(message)
    if MessageOptions.COMPRESSED in options:
        message = compress(message)
    if MessageOptions.LOGGED in options:
        log(message)

# Klar und kombinierbar
send_message_v2("Hello", MessageOptions.ENCRYPTED | MessageOptions.LOGGED)

# Option 3: Builder Pattern
class MessageBuilder:
    def __init__(self, message):
        self.message = message
        self._encrypted = False
        self._compressed = False
        self._logged = False
    
    def encrypted(self):
        self._encrypted = True
        return self
    
    def compressed(self):
        self._compressed = True
        return self
    
    def logged(self):
        self._logged = True
        return self
    
    def send(self):
        # Sende mit Optionen
        pass

# Fluent API
MessageBuilder("Hello").encrypted().logged().send()
```

## Primitive Obsession

### ❌ Problem

```python
def create_user(name: str, email: str, age: int):
    # Was wenn email ungültig?
    # Was wenn age negativ?
    # Keine Validierung!
    pass

def process_order(order_id: str, amount: float, currency: str):
    # Keine Typ-Sicherheit
    # "EUR" vs "eur" vs "Euro"?
    pass

# Primitive Typen überall
users = [("Max", "max@example.com", 30), ("Anna", "anna@example.com", 25)]
```

### ✅ Lösung

```python
# Value Objects / Domain Objects
from dataclasses import dataclass
from typing import NewType

@dataclass
class Email:
    value: str
    
    def __post_init__(self):
        if "@" not in self.value:
            raise ValueError(f"Invalid email: {self.value}")

@dataclass
class Age:
    value: int
    
    def __post_init__(self):
        if not 0 <= self.value <= 150:
            raise ValueError(f"Invalid age: {self.value}")

@dataclass
class User:
    name: str
    email: Email
    age: Age

# Verwendung - Typ-sicher und validiert
user = User("Max", Email("max@example.com"), Age(30))

# NewType für IDs
UserId = NewType("UserId", int)
OrderId = NewType("OrderId", str)

def get_user(user_id: UserId) -> User:
    ...

# Enum für begrenzte Werte
from enum import Enum

class Currency(Enum):
    EUR = "EUR"
    USD = "USD"
    GBP = "GBP"

@dataclass
class Money:
    amount: float
    currency: Currency

price = Money(99.99, Currency.EUR)
```

## Long Parameter Lists

### ❌ Problem

```python
def create_report(
    title, subtitle, author, date, format,
    include_charts, include_tables, page_numbers,
    header, footer, font_size, line_spacing,
    color_scheme, template_name
):
    # Zu viele Parameter!
    pass

# Aufruf ist unleserlich
create_report(
    "Report", "Subtitle", "Max", "2025-10-02", "PDF",
    True, True, True, "Header", "Footer", 12, 1.5,
    "blue", "default"
)
```

### ✅ Lösung

```python
# Option 1: Parameter Object
@dataclass
class ReportConfig:
    title: str
    subtitle: str
    author: str
    date: str
    format: str = "PDF"
    include_charts: bool = True
    include_tables: bool = True
    page_numbers: bool = True
    header: str = ""
    footer: str = ""
    font_size: int = 12
    line_spacing: float = 1.5
    color_scheme: str = "blue"
    template: str = "default"

def create_report(config: ReportConfig):
    # Klar und strukturiert
    pass

# Aufruf ist lesbar
config = ReportConfig(
    title="Report",
    subtitle="Subtitle",
    author="Max",
    date="2025-10-02"
)
create_report(config)

# Option 2: Builder Pattern (wie vorher gezeigt)
# Option 3: Factory Functions
def create_simple_report(title, author):
    return create_report(ReportConfig(
        title=title,
        author=author,
        include_charts=False
    ))
```

## Comments Everywhere

### ❌ Problem

```python
def calculate_total(items):
    # Initialize total
    total = 0
    # Loop through items
    for item in items:
        # Get price
        price = item.price
        # Get quantity
        quantity = item.quantity
        # Calculate item total
        item_total = price * quantity
        # Add to total
        total += item_total
    # Apply tax
    tax = total * 0.19  # 19% tax
    # Add tax to total
    total += tax
    # Return total
    return total
```

**Problem:** Zu viele offensichtliche Kommentare - Code sollte selbsterklärend sein

### ✅ Lösung

```python
# Selbsterklärender Code
TAX_RATE = 0.19

def calculate_total_with_tax(items: list[Item]) -> float:
    """Berechnet Gesamtsumme inkl. Steuer"""
    subtotal = sum(item.price * item.quantity for item in items)
    tax = subtotal * TAX_RATE
    return subtotal + tax

# Kommentare nur für "Warum", nicht "Was"
def apply_discount(price: float, user: User) -> float:
    # Langzeitkunden erhalten 10% Rabatt als Dankeschön
    # für ihre Treue (Marketing-Entscheidung vom 2025-01)
    if user.is_long_term_customer():
        return price * 0.9
    return price
```

## Performance Anti-Patterns

### ❌ Problem

```python
# 1. Loop mit häufigem Lookup
def process_users(users, valid_ids):
    result = []
    for user in users:
        if user.id in valid_ids:  # O(n) wenn valid_ids eine Liste!
            result.append(user)
    return result

# 2. Konkatenation in Loop
def join_strings(strings):
    result = ""
    for s in strings:
        result += s  # Jedes Mal neue String-Allokation!
    return result

# 3. Unnötige Kopien
def process_large_list(items):
    sorted_items = sorted(items)  # Neue Liste
    filtered = [x for x in sorted_items if x > 0]  # Noch eine Liste
    result = [x * 2 for x in filtered]  # Noch eine!
    return result
```

### ✅ Lösung

```python
# 1. Set für O(1) Lookup
def process_users(users, valid_ids):
    valid_set = set(valid_ids)  # Einmalig konvertieren
    return [user for user in users if user.id in valid_set]

# 2. join() verwenden
def join_strings(strings):
    return "".join(strings)

# 3. Generator-Pipeline
def process_large_list(items):
    return [
        x * 2
        for x in sorted(items)
        if x > 0
    ]
    # Oder mit Generator für sehr große Listen:
    # return (x * 2 for x in sorted(items) if x > 0)
```

## Zusammenfassung: Code Smells Checklist

**Daten & Typen:**
- ✓ Mutable defaults vermeiden (None statt [])
- ✓ Value Objects statt Primitives
- ✓ Type Hints verwenden
- ✓ Enums für begrenzte Werte

**Funktionen:**
- ✓ Funktionen kurz halten (<20 Zeilen)
- ✓ Eine Verantwortung pro Funktion
- ✓ Guard Clauses statt Nesting
- ✓ Sprechende Namen

**Fehlerbehandlung:**
- ✓ Spezifische Exceptions
- ✓ Context Manager für Ressourcen
- ✓ EAFP statt LBYL

**Code-Organisation:**
- ✓ DRY (Don't Repeat Yourself)
- ✓ Single Responsibility
- ✓ Konstanten statt Magic Numbers
- ✓ Kleine, fokussierte Klassen

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [PEP 8 - Style Guide](https://peps.python.org/pep-0008/) - Code Style
- [Refactoring Guru - Code Smells](https://refactoring.guru/refactoring/smells) - Patterns
- [Clean Code in Python](https://github.com/zedr/clean-code-python) - Best Practices

## Verwandte Themen

- [[06_Praxis/01_Best_Practices/01_Idiomatisches Python|Idiomatisches Python]] - Pythonic Code
- [[06_Praxis/01_Best_Practices/03_Datenstruktur Auswahl|Datenstruktur Auswahl]] - Richtige Struktur
- [[06_Praxis/02_Performance/01_Performance Messung|Performance Messung]] - Optimierung
- [[04_Fortgeschritten/04_Dataclasses/01_Dataclass Basics|Dataclass Basics]] - Value Objects

---
← [[06_Praxis/01_Best_Practices/01_Idiomatisches Python|Idiomatisches Python]] | [[INDEX|📑 Index]] | [[06_Praxis/01_Best_Practices/03_Datenstruktur Auswahl|Datenstruktur Auswahl]]