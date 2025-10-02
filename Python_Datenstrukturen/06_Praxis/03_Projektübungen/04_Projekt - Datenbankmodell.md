---
tags: [python, datenstrukturen, praxis, projekt, datenbank]
created: 2025-10-02
---

← [[06_Praxis/03_Projektübungen/03_Projekt - JSON Parser|Projekt - JSON Parser]] | [[INDEX|📑 Index]] | [[06_Praxis/04_Lösungen/Lösungen Teil 1|Lösungen Teil 1]] →

# Projekt: In-Memory Datenbankmodell

> [!tip] Projektziel
> Implementiere eine vollständige In-Memory Datenbank mit Tabellen, Queries, Indizes und Transaktionen

## Projektübersicht

In diesem Projekt entwickelst du ein **In-Memory Database System**, das:
- Tabellen mit Schemas erstellt und verwaltet
- SQL-ähnliche Queries ausführt (SELECT, INSERT, UPDATE, DELETE)
- Indizes für schnelle Suche verwendet
- Transaktionen mit Rollback unterstützt
- Daten persistent speichern kann

**Geschätzte Bearbeitungszeit:** 4-5 Stunden

## Anforderungen

### Funktionale Anforderungen

1. **Schema-Definition**
   - Spalten mit Typen und Constraints
   - Primary Keys und Foreign Keys
   - Unique Constraints

2. **CRUD-Operationen**
   - INSERT: Neue Zeilen einfügen
   - SELECT: Daten abfragen mit Filter
   - UPDATE: Zeilen aktualisieren
   - DELETE: Zeilen löschen

3. **Erweiterte Features**
   - Indizes für schnelle Suche
   - JOIN-Operationen
   - Aggregationen (COUNT, SUM, AVG, etc.)
   - Transaktionen mit COMMIT/ROLLBACK

### Technische Anforderungen

- Verwende verschiedene Datenstrukturen optimal
- Type Hints durchgängig
- Comprehensive Error Handling
- Performance-Optimierung durch Indizes

## Projektstruktur

```python
from typing import Any, List, Dict, Set, Optional, Callable, Tuple
from dataclasses import dataclass, field
from collections import defaultdict, OrderedDict
from copy import deepcopy
from enum import Enum, auto
import pickle
from pathlib import Path
```

## Aufgabe 1: Schema-Definition (Mittel)

Erstelle das Datenmodell für Tabellen-Schemas.

```python
from enum import Enum, auto
from dataclasses import dataclass
from typing import Any, Optional, Callable

class DataType(Enum):
    """Unterstützte Datentypen"""
    INTEGER = auto()
    FLOAT = auto()
    STRING = auto()
    BOOLEAN = auto()
    DATE = auto()

class ConstraintType(Enum):
    """Constraint-Typen"""
    NOT_NULL = auto()
    UNIQUE = auto()
    PRIMARY_KEY = auto()
    FOREIGN_KEY = auto()
    CHECK = auto()

@dataclass
class Column:
    """Spalten-Definition"""
    name: str
    dtype: DataType
    nullable: bool = True
    unique: bool = False
    default: Optional[Any] = None
    check: Optional[Callable[[Any], bool]] = None
    
    def validate(self, value: Any) -> Tuple[bool, str]:
        """
        Validiert einen Wert gegen die Spalten-Constraints
        
        Returns:
            (ist_valide, fehler_nachricht)
        """
        # NULL-Check
        if value is None:
            if not self.nullable:
                return False, f"Column '{self.name}' cannot be NULL"
            return True, ""
        
        # Typ-Check
        if not self._check_type(value):
            return False, f"Invalid type for column '{self.name}'"
        
        # Custom Check
        if self.check and not self.check(value):
            return False, f"Check constraint failed for column '{self.name}'"
        
        return True, ""
    
    def _check_type(self, value: Any) -> bool:
        """Prüft ob Wert zum Datentyp passt"""
        type_map = {
            DataType.INTEGER: int,
            DataType.FLOAT: (int, float),
            DataType.STRING: str,
            DataType.BOOLEAN: bool,
        }
        expected = type_map.get(self.dtype)
        return isinstance(value, expected)
    
    def __repr__(self) -> str:
        constraints = []
        if not self.nullable:
            constraints.append("NOT NULL")
        if self.unique:
            constraints.append("UNIQUE")
        if self.default is not None:
            constraints.append(f"DEFAULT {self.default}")
        
        constraint_str = f" {' '.join(constraints)}" if constraints else ""
        return f"{self.name} {self.dtype.name}{constraint_str}"

@dataclass
class ForeignKey:
    """Foreign Key Constraint"""
    column: str
    ref_table: str
    ref_column: str
    
    def __repr__(self) -> str:
        return f"FOREIGN KEY ({self.column}) REFERENCES {self.ref_table}({self.ref_column})"

@dataclass
class TableSchema:
    """Tabellen-Schema"""
    name: str
    columns: List[Column]
    primary_key: Optional[str] = None
    foreign_keys: List[ForeignKey] = field(default_factory=list)
    
    def __post_init__(self):
        """Validiert Schema nach Erstellung"""
        # Prüfe Spalten-Namen sind unique
        names = [col.name for col in self.columns]
        if len(names) != len(set(names)):
            raise ValueError("Column names must be unique")
        
        # Prüfe Primary Key existiert
        if self.primary_key:
            if self.primary_key not in names:
                raise ValueError(f"Primary key '{self.primary_key}' not found in columns")
            # Primary Key muss NOT NULL sein
            pk_col = self.get_column(self.primary_key)
            pk_col.nullable = False
            pk_col.unique = True
    
    def get_column(self, name: str) -> Optional[Column]:
        """Findet Spalte nach Name"""
        for col in self.columns:
            if col.name == name:
                return col
        return None
    
    def get_column_names(self) -> List[str]:
        """Gibt alle Spalten-Namen zurück"""
        return [col.name for col in self.columns]
    
    def __repr__(self) -> str:
        lines = [f"TABLE {self.name} ("]
        for col in self.columns:
            lines.append(f"  {col}")
        if self.primary_key:
            lines.append(f"  PRIMARY KEY ({self.primary_key})")
        for fk in self.foreign_keys:
            lines.append(f"  {fk}")
        lines.append(")")
        return "\n".join(lines)
```

**Erwartete Verwendung:**
```python
# Erstelle User-Tabelle Schema
user_schema = TableSchema(
    name="users",
    columns=[
        Column("id", DataType.INTEGER, nullable=False),
        Column("name", DataType.STRING, nullable=False),
        Column("email", DataType.STRING, unique=True),
        Column("age", DataType.INTEGER, check=lambda x: x >= 18),
        Column("active", DataType.BOOLEAN, default=True),
    ],
    primary_key="id"
)

print(user_schema)
# TABLE users (
#   id INTEGER NOT NULL UNIQUE
#   name STRING NOT NULL
#   email STRING UNIQUE
#   age INTEGER
#   active BOOLEAN DEFAULT True
#   PRIMARY KEY (id)
# )
```

## Aufgabe 2: Row und Table (Mittel)

Implementiere Zeilen und Tabellen.

```python
from typing import Dict, Any, List, Optional
from collections import OrderedDict

class DatabaseError(Exception):
    """Base Exception für Datenbank-Fehler"""
    pass

class ConstraintViolation(DatabaseError):
    """Constraint wurde verletzt"""
    pass

@dataclass
class Row:
    """Repräsentiert eine Tabellenzeile"""
    data: Dict[str, Any]
    rowid: int
    
    def __getitem__(self, key: str) -> Any:
        return self.data[key]
    
    def __setitem__(self, key: str, value: Any) -> None:
        self.data[key] = value
    
    def get(self, key: str, default: Any = None) -> Any:
        return self.data.get(key, default)
    
    def __repr__(self) -> str:
        items = ", ".join(f"{k}={v!r}" for k, v in self.data.items())
        return f"Row({self.rowid}: {items})"

class Table:
    """Repräsentiert eine Datenbank-Tabelle"""
    
    def __init__(self, schema: TableSchema):
        self.schema = schema
        self.rows: OrderedDict[int, Row] = OrderedDict()
        self._next_rowid = 1
        
        # Indizes für schnelle Suche
        self._indexes: Dict[str, Dict[Any, Set[int]]] = {}
        
        # Erstelle Index für Primary Key
        if schema.primary_key:
            self._indexes[schema.primary_key] = {}
        
        # Erstelle Indizes für UNIQUE Spalten
        for col in schema.columns:
            if col.unique and col.name not in self._indexes:
                self._indexes[col.name] = {}
    
    def insert(self, data: Dict[str, Any]) -> int:
        """
        Fügt neue Zeile ein
        
        Args:
            data: Dictionary mit Spaltenwerten
            
        Returns:
            rowid der eingefügten Zeile
            
        Raises:
            ConstraintViolation: Bei Constraint-Verletzung
        """
        # Validiere Spalten-Namen
        for col_name in data.keys():
            if self.schema.get_column(col_name) is None:
                raise DatabaseError(f"Unknown column: {col_name}")
        
        # Füge Default-Werte hinzu
        row_data = {}
        for col in self.schema.columns:
            if col.name in data:
                row_data[col.name] = data[col.name]
            elif col.default is not None:
                row_data[col.name] = col.default
            elif col.nullable:
                row_data[col.name] = None
            else:
                raise ConstraintViolation(
                    f"Column '{col.name}' requires a value (NOT NULL)"
                )
        
        # Validiere Werte
        for col in self.schema.columns:
            valid, error = col.validate(row_data[col.name])
            if not valid:
                raise ConstraintViolation(error)
        
        # Prüfe UNIQUE Constraints
        for col_name, index in self._indexes.items():
            value = row_data[col_name]
            if value is not None and value in index:
                raise ConstraintViolation(
                    f"Duplicate value for UNIQUE column '{col_name}': {value}"
                )
        
        # Füge Zeile ein
        rowid = self._next_rowid
        self._next_rowid += 1
        
        row = Row(row_data, rowid)
        self.rows[rowid] = row
        
        # Update Indizes
        self._update_indexes_insert(rowid, row_data)
        
        return rowid
    
    def _update_indexes_insert(self, rowid: int, data: Dict[str, Any]) -> None:
        """Aktualisiert Indizes nach Insert"""
        for col_name, index in self._indexes.items():
            value = data[col_name]
            if value is not None:
                if value not in index:
                    index[value] = set()
                index[value].add(rowid)
    
    def select(self, columns: Optional[List[str]] = None,
               where: Optional[Callable[[Row], bool]] = None) -> List[Row]:
        """
        Wählt Zeilen aus
        
        Args:
            columns: Gewünschte Spalten (None = alle)
            where: Filter-Funktion
            
        Returns:
            Liste von Rows
        """
        # TODO: Implementieren
        # Filtere Zeilen mit where-Funktion
        # Projiziere auf gewünschte Spalten wenn angegeben
        pass
    
    def update(self, data: Dict[str, Any],
               where: Optional[Callable[[Row], bool]] = None) -> int:
        """
        Aktualisiert Zeilen
        
        Args:
            data: Neue Werte
            where: Filter-Funktion (None = alle Zeilen)
            
        Returns:
            Anzahl aktualisierter Zeilen
        """
        # TODO: Implementieren
        pass
    
    def delete(self, where: Optional[Callable[[Row], bool]] = None) -> int:
        """
        Löscht Zeilen
        
        Args:
            where: Filter-Funktion (None = alle Zeilen)
            
        Returns:
            Anzahl gelöschter Zeilen
        """
        # TODO: Implementieren
        pass
    
    def find_by_index(self, column: str, value: Any) -> List[Row]:
        """
        Nutzt Index für schnelle Suche
        
        Args:
            column: Spalten-Name (muss indexiert sein)
            value: Gesuchter Wert
            
        Returns:
            Liste von Rows
        """
        if column not in self._indexes:
            raise DatabaseError(f"No index on column '{column}'")
        
        index = self._indexes[column]
        rowids = index.get(value, set())
        return [self.rows[rid] for rid in rowids if rid in self.rows]
    
    def __len__(self) -> int:
        return len(self.rows)
    
    def __repr__(self) -> str:
        return f"Table({self.schema.name}, {len(self)} rows)"
```

**Erwartete Verwendung:**
```python
table = Table(user_schema)

# Insert
uid1 = table.insert({"id": 1, "name": "Alice", "email": "alice@example.com", "age": 25})
uid2 = table.insert({"id": 2, "name": "Bob", "email": "bob@example.com", "age": 30})

print(f"Eingefügt: {uid1}, {uid2}")
print(f"Zeilen: {len(table)}")

# Select
rows = table.select()
for row in rows:
    print(row)

# Find by Index
alice = table.find_by_index("id", 1)
print(f"User mit ID 1: {alice[0]}")
```

## Aufgabe 3: Query Builder (Schwer)

Erstelle einen Query Builder für komplexe Abfragen.

```python
from typing import List, Callable, Any, Optional
from enum import Enum, auto

class Operator(Enum):
    """Vergleichs-Operatoren"""
    EQ = auto()      # ==
    NE = auto()      # !=
    LT = auto()      # <
    LE = auto()      # <=
    GT = auto()      # >
    GE = auto()      # >=
    LIKE = auto()    # String contains
    IN = auto()      # In Liste

@dataclass
class Condition:
    """Repräsentiert eine WHERE-Bedingung"""
    column: str
    operator: Operator
    value: Any
    
    def evaluate(self, row: Row) -> bool:
        """Wertet Bedingung für eine Zeile aus"""
        row_value = row.get(self.column)
        
        if self.operator == Operator.EQ:
            return row_value == self.value
        elif self.operator == Operator.NE:
            return row_value != self.value
        elif self.operator == Operator.LT:
            return row_value < self.value
        elif self.operator == Operator.LE:
            return row_value <= self.value
        elif self.operator == Operator.GT:
            return row_value > self.value
        elif self.operator == Operator.GE:
            return row_value >= self.value
        elif self.operator == Operator.LIKE:
            return self.value in str(row_value)
        elif self.operator == Operator.IN:
            return row_value in self.value
        
        return False

class Query:
    """Query Builder für SQL-ähnliche Queries"""
    
    def __init__(self, table: Table):
        self.table = table
        self._columns: Optional[List[str]] = None
        self._conditions: List[Condition] = []
        self._order_by: Optional[Tuple[str, bool]] = None  # (column, reverse)
        self._limit: Optional[int] = None
        self._offset: int = 0
    
    def select(self, *columns: str) -> 'Query':
        """
        Wählt Spalten aus
        
        Example:
            query.select('name', 'email')
        """
        self._columns = list(columns) if columns else None
        return self
    
    def where(self, column: str, operator: Operator, value: Any) -> 'Query':
        """
        Fügt WHERE-Bedingung hinzu
        
        Example:
            query.where('age', Operator.GT, 18)
        """
        self._conditions.append(Condition(column, operator, value))
        return self
    
    def order_by(self, column: str, descending: bool = False) -> 'Query':
        """Sortiert Ergebnisse"""
        self._order_by = (column, descending)
        return self
    
    def limit(self, n: int) -> 'Query':
        """Limitiert Anzahl Ergebnisse"""
        self._limit = n
        return self
    
    def offset(self, n: int) -> 'Query':
        """Überspringt n Ergebnisse"""
        self._offset = n
        return self
    
    def execute(self) -> List[Row]:
        """
        Führt Query aus
        
        Returns:
            Liste von Rows
        """
        # TODO: Implementieren
        # 1. Alle Zeilen holen
        # 2. WHERE-Bedingungen anwenden
        # 3. ORDER BY anwenden
        # 4. OFFSET/LIMIT anwenden
        # 5. Spalten projizieren
        pass
    
    def count(self) -> int:
        """Zählt Ergebnisse"""
        return len(self.execute())
    
    def first(self) -> Optional[Row]:
        """Gibt erste Zeile zurück"""
        results = self.limit(1).execute()
        return results[0] if results else None
```

**Erwartete Verwendung:**
```python
# SELECT name, email FROM users WHERE age > 18 ORDER BY name LIMIT 10
results = (Query(table)
    .select('name', 'email')
    .where('age', Operator.GT, 18)
    .order_by('name')
    .limit(10)
    .execute())

for row in results:
    print(f"{row['name']}: {row['email']}")

# SELECT * FROM users WHERE email LIKE '@example.com'
users = (Query(table)
    .where('email', Operator.LIKE, '@example.com')
    .execute())
```

## Aufgabe 4: Aggregationen (Mittel)

Implementiere Aggregat-Funktionen.

```python
from typing import List, Any, Callable
from collections import Counter

class Aggregator:
    """Aggregat-Funktionen"""
    
    @staticmethod
    def count(rows: List[Row], column: Optional[str] = None) -> int:
        """
        Zählt Zeilen oder nicht-NULL Werte
        
        Args:
            rows: Zeilen
            column: Spalte (None = alle Zeilen)
        """
        if column is None:
            return len(rows)
        return sum(1 for row in rows if row.get(column) is not None)
    
    @staticmethod
    def sum(rows: List[Row], column: str) -> float:
        """Summiert numerische Spalte"""
        total = 0
        for row in rows:
            value = row.get(column)
            if value is not None:
                total += value
        return total
    
    @staticmethod
    def avg(rows: List[Row], column: str) -> Optional[float]:
        """Berechnet Durchschnitt"""
        # TODO: Implementieren
        pass
    
    @staticmethod
    def min(rows: List[Row], column: str) -> Optional[Any]:
        """Findet Minimum"""
        # TODO: Implementieren
        pass
    
    @staticmethod
    def max(rows: List[Row], column: str) -> Optional[Any]:
        """Findet Maximum"""
        # TODO: Implementieren
        pass
    
    @staticmethod
    def group_by(rows: List[Row], column: str) -> Dict[Any, List[Row]]:
        """
        Gruppiert Zeilen nach Spaltenwert
        
        Returns:
            Dict {wert: [zeilen]}
        """
        groups = defaultdict(list)
        for row in rows:
            key = row.get(column)
            groups[key].append(row)
        return dict(groups)
    
    @staticmethod
    def distinct(rows: List[Row], column: str) -> List[Any]:
        """Gibt eindeutige Werte zurück"""
        values = set()
        for row in rows:
            value = row.get(column)
            if value is not None:
                values.add(value)
        return sorted(values)
```

**Erwartete Verwendung:**
```python
all_rows = table.select()

# COUNT
print(f"Total users: {Aggregator.count(all_rows)}")
print(f"Users with email: {Aggregator.count(all_rows, 'email')}")

# AVG
avg_age = Aggregator.avg(all_rows, 'age')
print(f"Average age: {avg_age:.1f}")

# GROUP BY
by_age = Aggregator.group_by(all_rows, 'age')
for age, users in by_age.items():
    print(f"Age {age}: {len(users)} users")
```

## Aufgabe 5: Transaktionen (Schwer)

Implementiere Transaktions-Support mit Rollback.

```python
from copy import deepcopy
from typing import Optional

class Transaction:
    """Repräsentiert eine Datenbank-Transaktion"""
    
    def __init__(self, database: 'Database'):
        self.database = database
        self.snapshot: Optional[Dict[str, Any]] = None
        self.active = False
    
    def begin(self) -> None:
        """Startet Transaktion"""
        if self.active:
            raise DatabaseError("Transaction already active")
        
        # Erstelle Snapshot aller Tabellen
        self.snapshot = {}
        for name, table in self.database.tables.items():
            self.snapshot[name] = {
                'rows': deepcopy(table.rows),
                'next_rowid': table._next_rowid,
                'indexes': deepcopy(table._indexes)
            }
        
        self.active = True
    
    def commit(self) -> None:
        """Commitet Transaktion"""
        if not self.active:
            raise DatabaseError("No active transaction")
        
        # Verwerfe Snapshot
        self.snapshot = None
        self.active = False
    
    def rollback(self) -> None:
        """Macht alle Änderungen rückgängig"""
        if not self.active:
            raise DatabaseError("No active transaction")
        
        # Stelle Snapshot wieder her
        for name, snapshot in self.snapshot.items():
            table = self.database.tables[name]
            table.rows = snapshot['rows']
            table._next_rowid = snapshot['next_rowid']
            table._indexes = snapshot['indexes']
        
        self.snapshot = None
        self.active = False
    
    def __enter__(self):
        """Context Manager: BEGIN"""
        self.begin()
        return self
    
    def __exit__(self, exc_type, exc_val, exc_tb):
        """Context Manager: COMMIT oder ROLLBACK"""
        if exc_type is not None:
            # Exception occurred -> Rollback
            self.rollback()
        else:
            # Success -> Commit
            self.commit()
        return False  # Don't suppress exceptions
```

**Erwartete Verwendung:**
```python
# Erfolgreiche Transaktion
with Transaction(db) as tx:
    table.insert({"id": 10, "name": "Test"})
    table.insert({"id": 11, "name": "Test2"})
    # Automatisch committed

# Fehlgeschlagene Transaktion
try:
    with Transaction(db) as tx:
        table.insert({"id": 12, "name": "Test3"})
        raise Exception("Something went wrong")
except:
    pass

# ID 12 wurde nicht eingefügt (Rollback)
```

## Aufgabe 6: Database Manager (Schwer)

Erstelle den Hauptdatenbank-Manager.

```python
from typing import Dict, List
from pathlib import Path

class Database:
    """Haupt-Datenbank-Klasse"""
    
    def __init__(self, name: str = "mydb"):
        self.name = name
        self.tables: Dict[str, Table] = {}
    
    def create_table(self, schema: TableSchema) -> Table:
        """
        Erstellt neue Tabelle
        
        Raises:
            DatabaseError: Wenn Tabelle bereits existiert
        """
        if schema.name in self.tables:
            raise DatabaseError(f"Table '{schema.name}' already exists")
        
        # Validiere Foreign Keys
        for fk in schema.foreign_keys:
            if fk.ref_table not in self.tables:
                raise DatabaseError(
                    f"Referenced table '{fk.ref_table}' does not exist"
                )
            
            ref_table = self.tables[fk.ref_table]
            if ref_table.schema.get_column(fk.ref_column) is None:
                raise DatabaseError(
                    f"Referenced column '{fk.ref_column}' does not exist"
                )
        
        table = Table(schema)
        self.tables[schema.name] = table
        return table
    
    def get_table(self, name: str) -> Optional[Table]:
        """Gibt Tabelle zurück"""
        return self.tables.get(name)
    
    def drop_table(self, name: str) -> bool:
        """
        Löscht Tabelle
        
        Returns:
            True wenn gelöscht, False wenn nicht gefunden
        """
        if name in self.tables:
            del self.tables[name]
            return True
        return False
    
    def list_tables(self) -> List[str]:
        """Gibt alle Tabellen-Namen zurück"""
        return list(self.tables.keys())
    
    def save(self, filepath: Path) -> None:
        """Speichert Datenbank in Datei"""
        data = {
            'name': self.name,
            'tables': {}
        }
        
        for name, table in self.tables.items():
            data['tables'][name] = {
                'schema': table.schema,
                'rows': table.rows,
                'next_rowid': table._next_rowid
            }
        
        with open(filepath, 'wb') as f:
            pickle.dump(data, f)
    
    def load(self, filepath: Path) -> None:
        """Lädt Datenbank aus Datei"""
        with open(filepath, 'rb') as f:
            data = pickle.load(f)
        
        self.name = data['name']
        self.tables = {}
        
        for name, table_data in data['tables'].items():
            table = Table(table_data['schema'])
            table.rows = table_data['rows']
            table._next_rowid = table_data['next_rowid']
            
            # Rebuild Indizes
            for rowid, row in table.rows.items():
                table._update_indexes_insert(rowid, row.data)
            
            self.tables[name] = table
    
    def __repr__(self) -> str:
        return f"Database({self.name}, {len(self.tables)} tables)"
```

## Aufgabe 7: JOIN-Operationen (Schwer - Optional)

Implementiere JOIN zwischen Tabellen.

```python
class JoinType(Enum):
    INNER = auto()
    LEFT = auto()
    RIGHT = auto()

class Join:
    """JOIN zwischen zwei Tabellen"""
    
    @staticmethod
    def inner_join(left: Table, right: Table,
                   left_key: str, right_key: str) -> List[Dict[str, Any]]:
        """
        INNER JOIN: Nur Zeilen wo Match existiert
        
        Returns:
            Liste von kombinierten Dictionaries
        """
        results = []
        
        # Build Index auf right_key wenn nicht vorhanden
        right_index = defaultdict(list)
        for row in right.rows.values():
            key_value = row.get(right_key)
            right_index[key_value].append(row)
        
        # Join
        for left_row in left.rows.values():
            key_value = left_row.get(left_key)
            matching_rows = right_index.get(key_value, [])
            
            for right_row in matching_rows:
                # Kombiniere beide Zeilen
                combined = dict(left_row.data)
                for k, v in right_row.data.items():
                    # Prefix mit Tabellen-Namen bei Konflikten
                    if k in combined and k != right_key:
                        combined[f"{right.schema.name}.{k}"] = v
                    else:
                        combined[k] = v
                
                results.append(combined)
        
        return results
```

**Erwartete Verwendung:**
```python
# CREATE TABLE orders (
#     id INTEGER PRIMARY KEY,
#     user_id INTEGER,
#     amount FLOAT
# )

# SELECT users.name, orders.amount
# FROM users
# INNER JOIN orders ON users.id = orders.user_id

results = Join.inner_join(users_table, orders_table, 'id', 'user_id')
for row in results:
    print(f"{row['name']} ordered {row['amount']}€")
```

## Aufgabe 8: Vollständiges Demo (Schwer)

Erstelle ein komplettes Anwendungs-Szenario.

```python
def demo_database():
    """Vollständige Datenbank-Demo"""
    
    print("="*70)
    print("IN-MEMORY DATABASE DEMO")
    print("="*70)
    
    # 1. Erstelle Datenbank
    print("\n1. Datenbank erstellen")
    print("-" * 70)
    
    db = Database("company_db")
    print(f"✓ {db}")
    
    # 2. Definiere Schemas
    print("\n2. Tabellen-Schemas definieren")
    print("-" * 70)
    
    # Departments
    dept_schema = TableSchema(
        name="departments",
        columns=[
            Column("id", DataType.INTEGER, nullable=False),
            Column("name", DataType.STRING, nullable=False, unique=True),
            Column("budget", DataType.FLOAT, default=0.0),
        ],
        primary_key="id"
    )
    
    # Employees
    emp_schema = TableSchema(
        name="employees",
        columns=[
            Column("id", DataType.INTEGER, nullable=False),
            Column("name", DataType.STRING, nullable=False),
            Column("email", DataType.STRING, unique=True),
            Column("salary", DataType.FLOAT, check=lambda x: x >= 0),
            Column("dept_id", DataType.INTEGER),
            Column("active", DataType.BOOLEAN, default=True),
        ],
        primary_key="id",
        foreign_keys=[
            ForeignKey("dept_id", "departments", "id")
        ]
    )
    
    # Erstelle Tabellen
    dept_table = db.create_table(dept_schema)
    emp_table = db.create_table(emp_schema)
    
    print(f"✓ Created tables: {db.list_tables()}")
    print("\nDepartments Schema:")
    print(dept_schema)
    print("\nEmployees Schema:")
    print(emp_schema)
    
    # 3. INSERT Daten
    print("\n3. Daten einfügen")
    print("-" * 70)
    
    # Departments
    dept_table.insert({"id": 1, "name": "Engineering", "budget": 500000})
    dept_table.insert({"id": 2, "name": "Sales", "budget": 300000})
    dept_table.insert({"id": 3, "name": "Marketing", "budget": 200000})
    
    print(f"✓ Inserted {len(dept_table)} departments")
    
    # Employees
    employees = [
        {"id": 1, "name": "Alice", "email": "alice@company.com", "salary": 85000, "dept_id": 1},
        {"id": 2, "name": "Bob", "email": "bob@company.com", "salary": 75000, "dept_id": 1},
        {"id": 3, "name": "Charlie", "email": "charlie@company.com", "salary": 65000, "dept_id": 2},
        {"id": 4, "name": "Diana", "email": "diana@company.com", "salary": 70000, "dept_id": 3},
        {"id": 5, "name": "Eve", "email": "eve@company.com", "salary": 90000, "dept_id": 1},
    ]
    
    for emp in employees:
        emp_table.insert(emp)
    
    print(f"✓ Inserted {len(emp_table)} employees")
    
    # 4. SELECT Queries
    print("\n4. SELECT Queries")
    print("-" * 70)
    
    # Alle Mitarbeiter
    print("\nAlle Mitarbeiter:")
    all_emp = emp_table.select()
    for emp in all_emp:
        print(f"  {emp['name']:10s} | {emp['email']:25s} | {emp['salary']:>8.0f}€")
    
    # Mit Filter
    print("\nEngineering Mitarbeiter:")
    eng_emp = emp_table.select(where=lambda row: row['dept_id'] == 1)
    for emp in eng_emp:
        print(f"  {emp['name']:10s} | {emp['salary']:>8.0f}€")
    
    # Index-Suche
    print("\nMitarbeiter mit ID 3 (Index-Suche):")
    result = emp_table.find_by_index("id", 3)
    if result:
        emp = result[0]
        print(f"  {emp['name']} ({emp['email']})")
    
    # 5. Query Builder
    print("\n5. Query Builder")
    print("-" * 70)
    
    # SELECT name, salary FROM employees WHERE salary > 70000 ORDER BY salary DESC
    print("\nGehälter über 70k:")
    high_earners = (Query(emp_table)
        .select('name', 'salary')
        .where('salary', Operator.GT, 70000)
        .order_by('salary', descending=True)
        .execute())
    
    for emp in high_earners:
        print(f"  {emp['name']:10s} | {emp['salary']:>8.0f}€")
    
    # Top 3 Verdiener
    print("\nTop 3 Verdiener:")
    top3 = (Query(emp_table)
        .select('name', 'salary')
        .order_by('salary', descending=True)
        .limit(3)
        .execute())
    
    for i, emp in enumerate(top3, 1):
        print(f"  {i}. {emp['name']:10s} | {emp['salary']:>8.0f}€")
    
    # 6. Aggregationen
    print("\n6. Aggregationen")
    print("-" * 70)
    
    all_rows = emp_table.select()
    
    print(f"Anzahl Mitarbeiter: {Aggregator.count(all_rows)}")
    print(f"Gesamt Gehälter: {Aggregator.sum(all_rows, 'salary'):,.0f}€")
    print(f"Durchschnittsgehalt: {Aggregator.avg(all_rows, 'salary'):,.0f}€")
    print(f"Min Gehalt: {Aggregator.min(all_rows, 'salary'):,.0f}€")
    print(f"Max Gehalt: {Aggregator.max(all_rows, 'salary'):,.0f}€")
    
    # Gruppierung
    print("\nMitarbeiter pro Department:")
    by_dept = Aggregator.group_by(all_rows, 'dept_id')
    for dept_id, emps in sorted(by_dept.items()):
        dept_name = dept_table.find_by_index("id", dept_id)[0]['name']
        avg_salary = Aggregator.avg(emps, 'salary')
        print(f"  {dept_name:15s}: {len(emps)} MA, Ø {avg_salary:,.0f}€")
    
    # 7. UPDATE
    print("\n7. UPDATE Operationen")
    print("-" * 70)
    
    # Gehaltserhöhung für Engineering
    updated = emp_table.update(
        {"salary": 80000},
        where=lambda row: row['dept_id'] == 1 and row['salary'] < 80000
    )
    print(f"✓ {updated} Gehälter erhöht")
    
    # Zeige aktualisierte Gehälter
    eng_emp = emp_table.select(where=lambda row: row['dept_id'] == 1)
    print("Engineering Gehälter nach Update:")
    for emp in eng_emp:
        print(f"  {emp['name']:10s} | {emp['salary']:>8.0f}€")
    
    # 8. Transaktionen
    print("\n8. Transaktionen")
    print("-" * 70)
    
    # Erfolgreiche Transaktion
    print("Starte erfolgreiche Transaktion...")
    with Transaction(db) as tx:
        emp_table.insert({
            "id": 6, 
            "name": "Frank", 
            "email": "frank@company.com",
            "salary": 60000,
            "dept_id": 2
        })
        print(f"  ✓ Frank eingefügt (Transaction aktiv)")
    print(f"  ✓ Transaction committed, Mitarbeiter: {len(emp_table)}")
    
    # Fehlgeschlagene Transaktion
    print("\nStarte fehlgeschlagene Transaktion...")
    count_before = len(emp_table)
    try:
        with Transaction(db) as tx:
            emp_table.insert({
                "id": 7,
                "name": "Grace",
                "email": "grace@company.com",
                "salary": 55000,
                "dept_id": 3
            })
            print(f"  Grace eingefügt (Transaction aktiv)")
            # Simuliere Fehler
            raise Exception("Simulated error")
    except:
        print(f"  ✗ Fehler aufgetreten")
    
    count_after = len(emp_table)
    print(f"  ✓ Transaction rolled back, Mitarbeiter: {count_after}")
    print(f"  ✓ Rollback erfolgreich: {count_before == count_after}")
    
    # 9. Constraint-Verletzungen
    print("\n9. Constraint-Validierung")
    print("-" * 70)
    
    # Doppelte Email
    print("Versuch: Doppelte Email...")
    try:
        emp_table.insert({
            "id": 8,
            "name": "Test",
            "email": "alice@company.com",  # Schon vergeben
            "salary": 50000,
            "dept_id": 1
        })
        print("  ✗ Sollte fehlschlagen!")
    except ConstraintViolation as e:
        print(f"  ✓ Constraint verletzt: {e}")
    
    # Negatives Gehalt
    print("Versuch: Negatives Gehalt...")
    try:
        emp_table.insert({
            "id": 9,
            "name": "Test",
            "email": "test@company.com",
            "salary": -1000,  # Check constraint verletzt
            "dept_id": 1
        })
        print("  ✗ Sollte fehlschlagen!")
    except ConstraintViolation as e:
        print(f"  ✓ Check constraint verletzt: {e}")
    
    # 10. JOIN
    print("\n10. JOIN Operationen")
    print("-" * 70)
    
    print("INNER JOIN: Employees mit Department-Namen")
    joined = Join.inner_join(emp_table, dept_table, 'dept_id', 'id')
    
    for row in joined[:5]:  # Zeige erste 5
        print(f"  {row['name']:10s} | {row['departments.name']:15s} | {row['salary']:>8.0f}€")
    
    # 11. Persistierung
    print("\n11. Persistierung")
    print("-" * 70)
    
    # Speichern
    db_file = Path('company.db')
    db.save(db_file)
    print(f"✓ Datenbank gespeichert: {db_file}")
    print(f"  Dateigröße: {db_file.stat().st_size:,} bytes")
    
    # Laden
    db2 = Database()
    db2.load(db_file)
    print(f"✓ Datenbank geladen: {db2}")
    print(f"  Tabellen: {db2.list_tables()}")
    print(f"  Mitarbeiter: {len(db2.get_table('employees'))}")
    
    # Cleanup
    db_file.unlink()
    
    # 12. DELETE
    print("\n12. DELETE Operationen")
    print("-" * 70)
    
    count_before = len(emp_table)
    deleted = emp_table.delete(where=lambda row: row['id'] == 6)
    print(f"✓ {deleted} Mitarbeiter gelöscht")
    print(f"  Vorher: {count_before}, Nachher: {len(emp_table)}")
    
    # 13. Performance
    print("\n13. Performance-Test")
    print("-" * 70)
    
    # Erstelle große Tabelle
    perf_schema = TableSchema(
        name="performance",
        columns=[
            Column("id", DataType.INTEGER, nullable=False),
            Column("value", DataType.INTEGER),
        ],
        primary_key="id"
    )
    perf_table = Table(perf_schema)
    
    # Bulk Insert
    import time
    n = 10000
    start = time.perf_counter()
    for i in range(n):
        perf_table.insert({"id": i, "value": i % 100})
    insert_time = time.perf_counter() - start
    
    print(f"Bulk Insert ({n:,} Zeilen): {insert_time:.4f}s")
    print(f"  {n/insert_time:.0f} inserts/s")
    
    # Index-Suche vs. Sequential Scan
    start = time.perf_counter()
    result = perf_table.find_by_index("id", 5000)
    index_time = time.perf_counter() - start
    
    start = time.perf_counter()
    result = perf_table.select(where=lambda row: row['id'] == 5000)
    scan_time = time.perf_counter() - start
    
    print(f"\nSuche nach ID 5000:")
    print(f"  Index-Suche: {index_time*1000:.3f}ms")
    print(f"  Sequential:  {scan_time*1000:.3f}ms")
    print(f"  Speedup: {scan_time/index_time:.1f}x")
    
    print("\n" + "="*70)
    print("DEMO ABGESCHLOSSEN!")
    print("="*70)

if __name__ == '__main__':
    demo_database()
```

**Erwarteter Output:**
```
======================================================================
IN-MEMORY DATABASE DEMO
======================================================================

1. Datenbank erstellen
----------------------------------------------------------------------
✓ Database(company_db, 0 tables)

2. Tabellen-Schemas definieren
----------------------------------------------------------------------
✓ Created tables: ['departments', 'employees']

Departments Schema:
TABLE departments (
  id INTEGER NOT NULL UNIQUE
  name STRING NOT NULL UNIQUE
  budget FLOAT DEFAULT 0.0
  PRIMARY KEY (id)
)

...

13. Performance-Test
----------------------------------------------------------------------
Bulk Insert (10,000 Zeilen): 0.1234s
  81,037 inserts/s

Suche nach ID 5000:
  Index-Suche: 0.001ms
  Sequential:  0.823ms
  Speedup: 823.0x

======================================================================
DEMO ABGESCHLOSSEN!
======================================================================
```

## Lernziele & verwendete Konzepte

**Datenstrukturen:**
- ✅ OrderedDict für Zeilen (Insertion-Order)
- ✅ Dict für Indizes und Mappings
- ✅ Set für schnelle Membership-Tests
- ✅ defaultdict für Gruppierungen
- ✅ List für Ergebnisse
- ✅ Dataclass für strukturierte Daten
- ✅ Enum für Type-Safety

**Design Patterns:**
- Builder Pattern (Query Builder)
- Strategy Pattern (Aggregat-Funktionen)
- Context Manager (Transactions)
- Factory Pattern (Schema → Table)

**Algorithmen:**
- B-Tree-ähnliche Indizes (vereinfacht)
- Hash-basierte Lookups
- Sequential Scans mit Filtern
- Sort-Merge für JOINs

**Performance:**
- O(1) für Index-Lookups
- O(n) für Sequential Scans
- O(n log n) für Sortierung
- O(n*m) für Nested Loop JOINs

**Komplexität:**
| Operation | Ohne Index | Mit Index |
|-----------|-----------|-----------|
| INSERT | O(1) | O(1) |
| SELECT (ID) | O(n) | O(1) |
| UPDATE | O(n) | O(k) |
| DELETE | O(n) | O(k) |
| JOIN | O(n*m) | O(n+m) |

## Bewertungskriterien

### Funktionalität (40%)
- [ ] CRUD-Operationen funktionieren
- [ ] Constraints werden validiert
- [ ] Indizes beschleunigen Queries
- [ ] Transaktionen mit Rollback

### Robustheit (25%)
- [ ] Umfassende Fehlerbehandlung
- [ ] Foreign Key Validierung
- [ ] Edge Cases behandelt
- [ ] Keine Data Corruption

### Code-Qualität (20%)
- [ ] Klare Architektur
- [ ] Type Hints durchgängig
- [ ] Docstrings vorhanden
- [ ] DRY-Prinzip beachtet

### Performance (15%)
- [ ] Indizes richtig implementiert
- [ ] Effiziente Datenstrukturen
- [ ] Keine unnötigen Kopien
- [ ] Memory-bewusst

## Bonus-Aufgaben (Optional)

### Bonus 1: Query Optimizer
Analysiere Queries und wähle beste Ausführungsstrategie:
```python
optimizer = QueryOptimizer()
plan = optimizer.analyze(query)
# Entscheidet: Index vs. Scan, Join-Reihenfolge, etc.
```

### Bonus 2: Multi-Column Indizes
Erstelle zusammengesetzte Indizes:
```python
table.create_index(['dept_id', 'salary'])
```

### Bonus 3: Full-Text Search
Implementiere Textsuche:
```python
table.search("python developer")
```

### Bonus 4: Replication
Implementiere Master-Slave Replikation für Hochverfügbarkeit.

### Bonus 5: Query Language Parser
Parse SQL-ähnliche Strings:
```python
db.execute("SELECT name FROM users WHERE age > 18")
```

## Testing

Umfangreiche Tests:

```python
import unittest

class TestDatabase(unittest.TestCase):
    
    def setUp(self):
        self.db = Database("test")
        self.schema = TableSchema(
            name="test_table",
            columns=[
                Column("id", DataType.INTEGER, nullable=False),
                Column("value", DataType.STRING),
            ],
            primary_key="id"
        )
        self.table = self.db.create_table(self.schema)
    
    def test_insert(self):
        rowid = self.table.insert({"id": 1, "value": "test"})
        self.assertEqual(rowid, 1)
        self.assertEqual(len(self.table), 1)
    
    def test_unique_constraint(self):
        self.table.insert({"id": 1, "value": "test"})
        with self.assertRaises(ConstraintViolation):
            self.table.insert({"id": 1, "value": "test2"})
    
    def test_transaction_rollback(self):
        self.table.insert({"id": 1, "value": "test"})
        
        try:
            with Transaction(self.db):
                self.table.insert({"id": 2, "value": "test2"})
                raise Exception("Test")
        except:
            pass
        
        self.assertEqual(len(self.table), 1)
    
    def test_index_lookup(self):
        for i in range(1000):
            self.table.insert({"id": i, "value": f"val{i}"})
        
        result = self.table.find_by_index("id", 500)
        self.assertEqual(len(result), 1)
        self.assertEqual(result[0]['value'], "val500")
```

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [collections](https://docs.python.org/3/library/collections.html) - OrderedDict, defaultdict
- [dataclasses](https://docs.python.org/3/library/dataclasses.html) - Strukturierte Daten
- [copy](https://docs.python.org/3/library/copy.html) - deepcopy für Transaktionen
- [pickle](https://docs.python.org/3/library/pickle.html) - Serialisierung
- [enum](https://docs.python.org/3/library/enum.html) - Type-Safe Enumerations

## Verwandte Themen

- [[03_Mappings_und_Sets/01_Dictionaries/06_OrderedDict vs Dict|OrderedDict]]
- [[03_Mappings_und_Sets/03_Collections_Module/02_DefaultDict|DefaultDict]]
- [[04_Fortgeschritten/04_Dataclasses/01_Dataclass Basics|Dataclasses]]
- [[04_Fortgeschritten/05_Enums/01_Enum Basics|Enums]]
- [[06_Praxis/01_Best_Practices/01_Idiomatisches Python|Best Practices]]

---
← [[06_Praxis/03_Projektübungen/03_Projekt - JSON Parser|Projekt - JSON Parser]] | [[INDEX|📑 Index]] | [[06_Praxis/04_Lösungen/Lösungen Teil 1|Lösungen Teil 1]] →