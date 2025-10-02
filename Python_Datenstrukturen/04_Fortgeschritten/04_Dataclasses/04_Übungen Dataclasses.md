---
tags: [python, datenstrukturen, dataclasses, übungen]
created: 2025-10-02
---

← [[04_Fortgeschritten/04_Dataclasses/03_Field Options|Field Options]] | [[INDEX|📑 Index]] | [[04_Fortgeschritten/05_Enums/01_Enum Basics|Enum Basics]] →

# Übungen Dataclasses

> [!tip] Übungsziel
> Diese Übungen helfen dir, Dataclasses in verschiedenen realen Szenarien anzuwenden und fortgeschrittene Features zu meistern.

## Schwierigkeitsgrade

- 🟢 **Leicht**: Grundlegende Dataclass-Erstellung
- 🟡 **Mittel**: Mit field(), Validierung, Methoden
- 🔴 **Schwer**: Komplexe Patterns, Vererbung, Serialisierung

---

## 🟢 Übung 1: Einfache Book Dataclass

Erstelle eine `Book` Dataclass mit title, author, year und pages. Teste Erstellung, Vergleich und String-Repräsentation.

<details>
<summary>✅ Lösung</summary>

```python
from dataclasses import dataclass

@dataclass
class Book:
    title: str
    author: str
    year: int
    pages: int

# Tests
book1 = Book("1984", "George Orwell", 1949, 328)
book2 = Book("1984", "George Orwell", 1949, 328)
print(book1 == book2)  # True
```

</details>

---

## 🟢 Übung 2: BlogPost mit Defaults

Erstelle eine `BlogPost` Dataclass mit title, content, author und optionalen Feldern published (bool, default=False) und tags (list, default leer).

<details>
<summary>✅ Lösung</summary>

```python
from dataclasses import dataclass, field
from typing import List

@dataclass
class BlogPost:
    title: str
    content: str
    author: str
    published: bool = False
    tags: List[str] = field(default_factory=list)
```

**Wichtig:** `default_factory=list` statt `default=[]` um Sharing zu vermeiden!

</details>

---

## 🟡 Übung 3: Temperature Converter

Erstelle eine `Temperature` Dataclass mit Properties für celsius, fahrenheit und kelvin mit automatischer Konversion.

<details>
<summary>✅ Lösung</summary>

```python
from dataclasses import dataclass

@dataclass
class Temperature:
    celsius: float = 0.0
    
    @property
    def fahrenheit(self) -> float:
        return self.celsius * 9/5 + 32
    
    @fahrenheit.setter
    def fahrenheit(self, value: float):
        self.celsius = (value - 32) * 5/9
    
    @property
    def kelvin(self) -> float:
        return self.celsius + 273.15
    
    @kelvin.setter
    def kelvin(self, value: float):
        self.celsius = value - 273.15
```

</details>

---

## 🟡 Übung 4: Person mit Validierung

Erstelle eine `Person` Dataclass mit name und age. Validiere in `__post_init__()`, dass name nicht leer und age zwischen 0 und 150 ist.

<details>
<summary>✅ Lösung</summary>

```python
from dataclasses import dataclass

@dataclass
class Person:
    name: str
    age: int
    
    def __post_init__(self):
        if not self.name or not self.name.strip():
            raise ValueError("Name darf nicht leer sein")
        if not (0 <= self.age <= 150):
            raise ValueError("Alter muss zwischen 0 und 150 liegen")
        self.name = self.name.strip()
```

</details>

---

## 🟡 Übung 5: Shopping Cart

Erstelle eine `ShoppingCart` Dataclass mit Methoden zum Hinzufügen/Entfernen von Items und Preisberechnung.

<details>
<summary>✅ Lösung</summary>

```python
from dataclasses import dataclass, field
from typing import List, Dict

@dataclass
class ShoppingCart:
    items: List[Dict] = field(default_factory=list)
    
    def add_item(self, name: str, price: float, quantity: int = 1):
        self.items.append({'name': name, 'price': price, 'quantity': quantity})
    
    def remove_item(self, name: str):
        self.items = [item for item in self.items if item['name'] != name]
    
    def get_total(self) -> float:
        return sum(item['price'] * item['quantity'] for item in self.items)
    
    def get_item_count(self) -> int:
        return len(self.items)
```

</details>

---

## 🟡 Übung 6: Vector2D mit Operatoren

Erstelle eine `Vector2D` Dataclass mit arithmetischen Operatoren (+, -, *, /).

<details>
<summary>✅ Lösung</summary>

```python
from dataclasses import dataclass
import math

@dataclass
class Vector2D:
    x: float
    y: float
    
    def __add__(self, other: 'Vector2D') -> 'Vector2D':
        return Vector2D(self.x + other.x, self.y + other.y)
    
    def __sub__(self, other: 'Vector2D') -> 'Vector2D':
        return Vector2D(self.x - other.x, self.y - other.y)
    
    def __mul__(self, scalar: float) -> 'Vector2D':
        return Vector2D(self.x * scalar, self.y * scalar)
    
    def __abs__(self) -> float:
        return math.sqrt(self.x ** 2 + self.y ** 2)
```

</details>

---

## 🔴 Übung 7: Invoice mit berechneten Feldern

Erstelle eine `Invoice` Dataclass mit Items-Liste und automatisch berechneten Feldern für subtotal, tax und total.

<details>
<summary>✅ Lösung</summary>

```python
from dataclasses import dataclass, field
from typing import List, Dict

@dataclass
class Invoice:
    items: List[Dict[str, any]]
    tax_rate: float = 0.19
    subtotal: float = field(init=False)
    tax: float = field(init=False)
    total: float = field(init=False)
    
    def __post_init__(self):
        self.subtotal = sum(item['price'] * item['qty'] for item in self.items)
        self.tax = self.subtotal * self.tax_rate
        self.total = self.subtotal + self.tax
```

</details>

---

## 🔴 Übung 8: Frozen Coordinate als Dictionary Key

Erstelle eine frozen `Coordinate` Dataclass die als Dictionary-Key verwendet werden kann.

<details>
<summary>✅ Lösung</summary>

```python
from dataclasses import dataclass

@dataclass(frozen=True)
class Coordinate:
    x: float
    y: float
    
    def distance_to(self, other: 'Coordinate') -> float:
        return ((self.x - other.x)**2 + (self.y - other.y)**2) ** 0.5
```

</details>

---

## 🔴 Übung 9: Vererbung - Employee Hierarchy

Erstelle eine Employee-Hierarchie mit Base-Klasse `Employee` und abgeleiteten Klassen `Developer` und `Manager`.

<details>
<summary>✅ Lösung</summary>

```python
from dataclasses import dataclass, field
from typing import List

@dataclass
class Employee:
    name: str
    salary: float
    
    def get_info(self) -> str:
        return f"{self.name}: ${self.salary:,.2f}"

@dataclass
class Developer(Employee):
    language: str = "Python"
    
    def get_info(self) -> str:
        return f"{self.name} (Developer): {self.language}"

@dataclass
class Manager(Employee):
    team: List[str] = field(default_factory=list)
    
    def get_info(self) -> str:
        return f"{self.name} (Manager): {len(self.team)} team members"
```

</details>

---

## 🔴 Übung 10: JSON Serialization mit Metadata

Erstelle eine Dataclass mit Metadaten-gesteuerter JSON-Serialisierung.

<details>
<summary>✅ Lösung</summary>

```python
from dataclasses import dataclass, field, fields
from datetime import datetime
import json

@dataclass
class User:
    user_id: int = field(metadata={"json_name": "userId"})
    username: str = field(metadata={"json_name": "userName"})
    email: str = field(metadata={"json_name": "emailAddress"})
    created_at: datetime = field(metadata={"json_name": "createdAt"})
    is_active: bool = field(default=True, metadata={"json_name": "isActive"})
    
    def to_dict(self) -> dict:
        result = {}
        for f in fields(self):
            value = getattr(self, f.name)
            json_name = f.metadata.get("json_name", f.name)
            if isinstance(value, datetime):
                result[json_name] = value.isoformat()
            else:
                result[json_name] = value
        return result
    
    def to_json(self, **kwargs) -> str:
        return json.dumps(self.to_dict(), **kwargs)
    
    @classmethod
    def from_dict(cls, data: dict):
        field_map = {f.metadata.get("json_name", f.name): f.name for f in fields(cls)}
        kwargs = {}
        for json_name, value in data.items():
            if json_name in field_map:
                field_name = field_map[json_name]
                field_obj = next(f for f in fields(cls) if f.name == field_name)
                if field_obj.type == datetime:
                    value = datetime.fromisoformat(value)
                kwargs[field_name] = value
        return cls(**kwargs)
    
    @classmethod
    def from_json(cls, json_str: str):
        return cls.from_dict(json.loads(json_str))
```

</details>

---

## Zusammenfassung

Diese Übungen haben gezeigt:

✅ Grundlegende Dataclasses (Book, BlogPost)
✅ Properties (Temperature Converter)
✅ Validierung (Person mit __post_init__)
✅ Geschäftslogik (ShoppingCart)
✅ Arithmetische Operationen (Vector2D)
✅ Berechnete Felder (Invoice)
✅ Frozen Dataclasses (Coordinate als Dict-Key)
✅ Vererbung (Employee-Hierarchie)
✅ Fortgeschrittene Serialisierung (JSON mit Metadata)

Die Lösungen findest du auch im Kapitel [[06_Praxis/04_Lösungen/Lösungen Teil 4|Lösungen Teil 4]].

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [dataclasses - Data Classes](https://docs.python.org/3/library/dataclasses.html) - Vollständige Dokumentation
- [PEP 557 - Data Classes](https://peps.python.org/pep-0557/) - Original Proposal

## Verwandte Themen

- [[04_Fortgeschritten/04_Dataclasses/01_Dataclass Basics|Dataclass Basics]] - Grundlagen
- [[04_Fortgeschritten/04_Dataclasses/02_Dataclass Features|Dataclass Features]] - Erweiterte Features
- [[04_Fortgeschritten/04_Dataclasses/03_Field Options|Field Options]] - field() Details

---
← [[04_Fortgeschritten/04_Dataclasses/03_Field Options|Field Options]] | [[INDEX|📑 Index]] | [[04_Fortgeschritten/05_Enums/01_Enum Basics|Enum Basics]] →