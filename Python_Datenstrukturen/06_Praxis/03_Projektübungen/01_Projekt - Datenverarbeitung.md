---
tags: [python, datenstrukturen, praxis, projekt]
created: 2025-10-02
---

← [[06_Praxis/02_Performance/03_Optimization Strategien|Optimization Strategien]] | [[INDEX|📑 Index]] | [[06_Praxis/03_Projektübungen/02_Projekt - Cache Implementation|Projekt - Cache Implementation]] →

# Projekt: Datenverarbeitung

> [!tip] Projektziel
> Implementiere ein vollständiges System zur Verarbeitung und Analyse von CSV-Daten mit mehreren zusammenwirkenden Datenstrukturen

## Projektübersicht

In diesem Projekt entwickelst du ein **Data Processing Pipeline System**, das:
- CSV-Dateien einliest und validiert
- Daten aggregiert und transformiert
- Statistische Analysen durchführt
- Ergebnisse in verschiedenen Formaten ausgibt

**Geschätzte Bearbeitungszeit:** 2-3 Stunden

## Anforderungen

### Funktionale Anforderungen

1. **CSV-Parser**
   - Einlesen von CSV-Dateien
   - Automatische Typ-Erkennung
   - Fehlerbehandlung bei ungültigen Daten

2. **Datenverarbeitung**
   - Filtern nach Kriterien
   - Gruppierung nach Spalten
   - Aggregationen (Summe, Durchschnitt, Min, Max)
   - Sortierung

3. **Ausgabe**
   - Tabellarische Anzeige
   - JSON-Export
   - Zusammenfassende Statistiken

### Technische Anforderungen

- Verwende **keine externen Libraries** (kein pandas!)
- Nutze verschiedene Datenstrukturen optimal
- Implementiere Type Hints
- Schreibe docstrings
- Fehlerbehandlung mit Custom Exceptions

## Projektstruktur

```python
from typing import List, Dict, Any, Callable, Optional
from collections import defaultdict, Counter
from dataclasses import dataclass, field
from decimal import Decimal
import csv
from pathlib import Path
```

## Aufgabe 1: Datenmodell (Leicht)

Erstelle die grundlegenden Datenstrukturen.

```python
from dataclasses import dataclass, field
from typing import Any, List, Dict
from decimal import Decimal

@dataclass
class Column:
    """Repräsentiert eine Spalte mit Metadaten"""
    name: str
    dtype: type  # int, float, Decimal, str
    nullable: bool = True
    
    def convert(self, value: str) -> Any:
        """Konvertiert String-Wert in den richtigen Typ"""
        # TODO: Implementieren
        pass

@dataclass
class DataRow:
    """Repräsentiert eine Datenzeile"""
    data: Dict[str, Any]
    row_number: int
    
    def __getitem__(self, key: str) -> Any:
        return self.data[key]
    
    def __setitem__(self, key: str, value: Any) -> None:
        self.data[key] = value

@dataclass
class Dataset:
    """Repräsentiert das gesamte Dataset"""
    columns: List[Column]
    rows: List[DataRow] = field(default_factory=list)
    
    def __len__(self) -> int:
        return len(self.rows)
    
    def __iter__(self):
        return iter(self.rows)
```

**Erwartete Implementierung:**
- `Column.convert()` soll String in den Zieltyp konvertieren
- Fehlerbehandlung bei ungültigen Werten
- None-Handling wenn nullable=True

## Aufgabe 2: CSV-Parser (Mittel)

Implementiere einen robusten CSV-Parser.

```python
class CSVParseError(Exception):
    """Custom Exception für CSV-Parsing Fehler"""
    pass

class CSVParser:
    """Parser für CSV-Dateien"""
    
    def __init__(self, delimiter: str = ',', 
                 decimal_separator: str = '.'):
        self.delimiter = delimiter
        self.decimal_separator = decimal_separator
    
    def infer_type(self, values: List[str]) -> type:
        """
        Erkennt automatisch den Datentyp einer Spalte
        anhand der Werte (ohne None/leere Strings)
        
        Reihenfolge: int -> Decimal -> float -> str
        """
        # TODO: Implementieren
        pass
    
    def parse_file(self, filepath: Path, 
                   auto_detect_types: bool = True) -> Dataset:
        """
        Liest CSV-Datei und erstellt Dataset
        
        Args:
            filepath: Pfad zur CSV-Datei
            auto_detect_types: Wenn True, erkenne Typen automatisch
            
        Returns:
            Dataset mit gelesenen Daten
            
        Raises:
            CSVParseError: Bei Parsing-Fehlern
            FileNotFoundError: Wenn Datei nicht existiert
        """
        # TODO: Implementieren
        pass
```

**Tipps:**
- Verwende `csv.DictReader` als Basis
- Bei Typ-Erkennung: Teste zuerst int, dann Decimal, dann float
- Sammle Fehler und gebe detaillierte Fehlermeldungen

**Test-CSV erstellen:**
```python
# Erstelle test_data.csv zum Testen
test_csv = """name,age,salary,department
Alice,30,75000.50,Engineering
Bob,25,65000.00,Sales
Charlie,35,85000.75,Engineering
Diana,28,70000.00,Marketing
Eve,32,80000.25,Engineering"""

with open('test_data.csv', 'w') as f:
    f.write(test_csv)
```

## Aufgabe 3: Datenverarbeitung (Schwer)

Implementiere die Analyse-Funktionen.

```python
from typing import Callable, List, Dict, Any
from collections import defaultdict
from decimal import Decimal

class DataProcessor:
    """Verarbeitet und analysiert Datasets"""
    
    def __init__(self, dataset: Dataset):
        self.dataset = dataset
    
    def filter(self, predicate: Callable[[DataRow], bool]) -> 'DataProcessor':
        """
        Filtert Zeilen nach Bedingung
        
        Example:
            processor.filter(lambda row: row['age'] > 30)
        """
        filtered_rows = [row for row in self.dataset if predicate(row)]
        new_dataset = Dataset(
            columns=self.dataset.columns,
            rows=filtered_rows
        )
        return DataProcessor(new_dataset)
    
    def group_by(self, column: str) -> Dict[Any, List[DataRow]]:
        """
        Gruppiert Zeilen nach Spaltenwert
        
        Returns:
            Dictionary {Gruppenwert: [Zeilen]}
        """
        # TODO: Implementieren mit defaultdict
        pass
    
    def aggregate(self, column: str, 
                  func: Callable[[List[Any]], Any]) -> Any:
        """
        Aggregiert Spaltenwerte mit gegebener Funktion
        
        Example:
            processor.aggregate('salary', sum)
            processor.aggregate('age', lambda vals: sum(vals)/len(vals))
        """
        # TODO: Implementieren
        pass
    
    def group_aggregate(self, group_col: str, 
                       agg_col: str,
                       agg_func: Callable[[List[Any]], Any]) -> Dict[Any, Any]:
        """
        Gruppiert und aggregiert in einem Schritt
        
        Example:
            # Durchschnittsgehalt pro Department
            processor.group_aggregate('department', 'salary', 
                                     lambda x: sum(x)/len(x))
        """
        # TODO: Implementieren
        pass
    
    def sort_by(self, column: str, 
                reverse: bool = False) -> 'DataProcessor':
        """Sortiert Dataset nach Spalte"""
        # TODO: Implementieren
        pass
```

**Erwartete Outputs:**
```python
# Beispiel-Verwendung
parser = CSVParser()
dataset = parser.parse_file(Path('test_data.csv'))
processor = DataProcessor(dataset)

# Filter: Nur Engineering
eng_processor = processor.filter(
    lambda row: row['department'] == 'Engineering'
)
print(f"Engineering: {len(eng_processor.dataset)} Mitarbeiter")
# Output: Engineering: 3 Mitarbeiter

# Durchschnittsgehalt pro Department
avg_salaries = processor.group_aggregate(
    'department', 
    'salary',
    lambda vals: sum(vals) / len(vals)
)
for dept, avg in sorted(avg_salaries.items()):
    print(f"{dept}: {avg:.2f}€")
# Output:
# Engineering: 80000.50€
# Marketing: 70000.00€
# Sales: 65000.00€
```

## Aufgabe 4: Statistik-Modul (Mittel)

Erstelle Funktionen für statistische Analysen.

```python
from typing import List, Dict, Any
from collections import Counter
from decimal import Decimal

class Statistics:
    """Statistische Funktionen"""
    
    @staticmethod
    def mean(values: List[float]) -> float:
        """Durchschnitt"""
        return sum(values) / len(values)
    
    @staticmethod
    def median(values: List[float]) -> float:
        """Median"""
        # TODO: Implementieren
        pass
    
    @staticmethod
    def mode(values: List[Any]) -> Any:
        """Häufigster Wert"""
        # TODO: Implementieren mit Counter
        pass
    
    @staticmethod
    def std_dev(values: List[float]) -> float:
        """Standardabweichung"""
        # TODO: Implementieren
        # σ = sqrt(sum((x - mean)²) / n)
        pass
    
    @staticmethod
    def percentile(values: List[float], p: float) -> float:
        """
        Berechnet p-tes Perzentil (p zwischen 0 und 100)
        
        Example:
            percentile([1,2,3,4,5], 50)  # Median
        """
        # TODO: Implementieren
        pass
    
    @staticmethod
    def summary(values: List[float]) -> Dict[str, float]:
        """
        Gibt vollständige Statistik-Übersicht
        
        Returns:
            Dict mit min, max, mean, median, std_dev, q25, q75
        """
        # TODO: Implementieren
        pass
```

**Erwarteter Output:**
```python
salaries = [75000.50, 65000.00, 85000.75, 70000.00, 80000.25]
stats = Statistics.summary(salaries)

# Output:
# {
#     'count': 5,
#     'min': 65000.0,
#     'max': 85000.75,
#     'mean': 75000.3,
#     'median': 75000.5,
#     'std_dev': 7211.10,
#     'q25': 70000.0,
#     'q75': 80000.25
# }
```

## Aufgabe 5: Output-Formate (Leicht-Mittel)

Implementiere verschiedene Ausgabeformate.

```python
import json
from typing import Dict, Any, List

class OutputFormatter:
    """Formatiert Dataset-Ausgaben"""
    
    @staticmethod
    def to_table(dataset: Dataset, max_rows: int = 10) -> str:
        """
        Erstellt tabellarische ASCII-Ausgabe
        
        Example Output:
        ┌──────────┬─────┬──────────┬─────────────┐
        │ name     │ age │ salary   │ department  │
        ├──────────┼─────┼──────────┼─────────────┤
        │ Alice    │  30 │ 75000.50 │ Engineering │
        │ Bob      │  25 │ 65000.00 │ Sales       │
        └──────────┴─────┴──────────┴─────────────┘
        """
        # TODO: Implementieren
        pass
    
    @staticmethod
    def to_json(dataset: Dataset, indent: int = 2) -> str:
        """Exportiert als JSON"""
        # TODO: Implementieren
        pass
    
    @staticmethod
    def to_csv(dataset: Dataset, filepath: Path) -> None:
        """Schreibt als CSV-Datei"""
        # TODO: Implementieren
        pass
    
    @staticmethod
    def summary_report(dataset: Dataset, 
                       numeric_columns: List[str]) -> str:
        """
        Erstellt Zusammenfassungs-Report
        
        Zeigt für numerische Spalten Statistiken
        Zeigt für kategorische Spalten Häufigkeiten
        """
        # TODO: Implementieren
        pass
```

## Aufgabe 6: Vollständiges Beispiel (Schwer)

Kombiniere alle Komponenten zu einem funktionierenden Programm.

```python
def main():
    """Hauptprogramm"""
    
    # 1. Daten laden
    print("Lade Daten...")
    parser = CSVParser()
    dataset = parser.parse_file(Path('test_data.csv'))
    print(f"✓ {len(dataset)} Zeilen geladen")
    
    # 2. Übersicht anzeigen
    print("\n" + "="*60)
    print("DATENSATZ ÜBERSICHT")
    print("="*60)
    formatter = OutputFormatter()
    print(formatter.to_table(dataset))
    
    # 3. Analyse: Durchschnittsgehalt pro Department
    print("\n" + "="*60)
    print("DURCHSCHNITTSGEHALT PRO DEPARTMENT")
    print("="*60)
    processor = DataProcessor(dataset)
    avg_by_dept = processor.group_aggregate(
        'department', 'salary',
        lambda vals: sum(vals) / len(vals)
    )
    for dept, avg in sorted(avg_by_dept.items(), 
                           key=lambda x: x[1], reverse=True):
        print(f"  {dept:20s}: {avg:>10.2f}€")
    
    # 4. Top-Verdiener finden
    print("\n" + "="*60)
    print("TOP 3 VERDIENER")
    print("="*60)
    top_earners = processor.sort_by('salary', reverse=True)
    print(formatter.to_table(
        Dataset(dataset.columns, top_earners.dataset.rows[:3])
    ))
    
    # 5. Statistik für Gehälter
    print("\n" + "="*60)
    print("GEHALTS-STATISTIK")
    print("="*60)
    salaries = [row['salary'] for row in dataset]
    stats = Statistics.summary(salaries)
    for key, value in stats.items():
        print(f"  {key:15s}: {value:>10.2f}€")
    
    # 6. Export
    print("\n" + "="*60)
    print("EXPORT")
    print("="*60)
    
    # JSON Export
    json_output = formatter.to_json(dataset)
    Path('output.json').write_text(json_output)
    print("✓ Daten exportiert nach output.json")
    
    # Filtered Export
    eng_only = processor.filter(
        lambda row: row['department'] == 'Engineering'
    )
    formatter.to_csv(eng_only.dataset, Path('engineering.csv'))
    print("✓ Engineering-Daten exportiert nach engineering.csv")

if __name__ == '__main__':
    main()
```

**Erwarteter Output:**
```
Lade Daten...
✓ 5 Zeilen geladen

============================================================
DATENSATZ ÜBERSICHT
============================================================
┌──────────┬─────┬──────────┬─────────────┐
│ name     │ age │ salary   │ department  │
├──────────┼─────┼──────────┼─────────────┤
│ Alice    │  30 │ 75000.50 │ Engineering │
│ Bob      │  25 │ 65000.00 │ Sales       │
│ Charlie  │  35 │ 85000.75 │ Engineering │
│ Diana    │  28 │ 70000.00 │ Marketing   │
│ Eve      │  32 │ 80000.25 │ Engineering │
└──────────┴─────┴──────────┴─────────────┘

============================================================
DURCHSCHNITTSGEHALT PRO DEPARTMENT
============================================================
  Engineering         :   80000.50€
  Marketing           :   70000.00€
  Sales               :   65000.00€

============================================================
TOP 3 VERDIENER
============================================================
┌──────────┬─────┬──────────┬─────────────┐
│ Charlie  │  35 │ 85000.75 │ Engineering │
│ Eve      │  32 │ 80000.25 │ Engineering │
│ Alice    │  30 │ 75000.50 │ Engineering │
└──────────┴─────┴──────────┴─────────────┘

============================================================
GEHALTS-STATISTIK
============================================================
  count          :       5.00€
  min            :   65000.00€
  max            :   85000.75€
  mean           :   75000.30€
  median         :   75000.50€
  std_dev        :    7211.10€
  q25            :   70000.00€
  q75            :   80000.25€

============================================================
EXPORT
============================================================
✓ Daten exportiert nach output.json
✓ Engineering-Daten exportiert nach engineering.csv
```

## Bonus-Aufgaben (Optional)

### Bonus 1: Pipeline-Architektur
Implementiere Method-Chaining für Datenverarbeitung:

```python
result = (DataProcessor(dataset)
    .filter(lambda row: row['age'] > 25)
    .sort_by('salary', reverse=True)
    .limit(3)
    .select(['name', 'salary'])
)
```

### Bonus 2: Caching
Füge Memoization für teure Berechnungen hinzu.

### Bonus 3: Multi-File Processing
Erweitere den Parser für mehrere CSV-Dateien mit JOIN-Operationen.

### Bonus 4: Query Language
Erstelle eine einfache Query-Sprache:
```python
processor.query("SELECT name, salary WHERE department = 'Engineering' ORDER BY salary DESC")
```

## Lernziele & verwendete Konzepte

**Datenstrukturen:**
- ✅ Dataclasses für Datenmodelle
- ✅ Dict für Mappings und Caching
- ✅ List für sequentielle Daten
- ✅ defaultdict für Gruppierungen
- ✅ Counter für Häufigkeiten
- ✅ Decimal für präzise Berechnungen

**Konzepte:**
- Type Hints & Type Safety
- Generator Expressions für Memory-Effizienz
- Lambda Functions & Higher-Order Functions
- Custom Exceptions
- Method Chaining
- Decorator für Memoization (Bonus)

**Performance:**
- O(1) für Row-Access via Dict
- O(n) für Filterung und Aggregation
- O(n log n) für Sortierung
- Lazy Evaluation wo möglich

## Bewertungskriterien

### Funktionalität (50%)
- [ ] Alle Grundfunktionen implementiert
- [ ] Fehlerbehandlung vorhanden
- [ ] Edge Cases berücksichtigt

### Code-Qualität (30%)
- [ ] Type Hints verwendet
- [ ] Docstrings vorhanden
- [ ] PEP 8 konform
- [ ] Keine Code-Duplikation

### Datenstrukturen (20%)
- [ ] Richtige Strukturen gewählt
- [ ] Effiziente Implementierung
- [ ] Memory-bewusst programmiert

## Nächste Schritte

Nach Abschluss dieses Projekts:
1. Vergleiche deine Lösung mit anderen
2. Führe Performance-Messungen durch
3. Erweitere mit Bonus-Features
4. Weiter zu [[06_Praxis/03_Projektübungen/02_Projekt - Cache Implementation|Projekt 2: Cache Implementation]]

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [csv module](https://docs.python.org/3/library/csv.html) - CSV-Datei Verarbeitung
- [dataclasses](https://docs.python.org/3/library/dataclasses.html) - Datenklassen
- [typing](https://docs.python.org/3/library/typing.html) - Type Hints
- [collections](https://docs.python.org/3/library/collections.html) - defaultdict, Counter
- [pathlib](https://docs.python.org/3/library/pathlib.html) - Dateipfad-Handling

## Verwandte Themen

- [[04_Fortgeschritten/04_Dataclasses/01_Dataclass Basics|Dataclasses]]
- [[03_Mappings_und_Sets/03_Collections_Module/01_Counter|Counter]]
- [[03_Mappings_und_Sets/03_Collections_Module/02_DefaultDict|DefaultDict]]
- [[06_Praxis/02_Performance/01_Performance Messung|Performance Messung]]

---
← [[06_Praxis/02_Performance/03_Optimization Strategien|Optimization Strategien]] | [[INDEX|📑 Index]] | [[06_Praxis/03_Projektübungen/02_Projekt - Cache Implementation|Projekt - Cache Implementation]] →