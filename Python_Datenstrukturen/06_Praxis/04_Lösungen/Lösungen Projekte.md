---
tags: [python, datenstrukturen, lösungen, projekte]
created: 2025-10-02
---

← [[06_Praxis/04_Lösungen/Lösungen Teil 5|Lösungen Teil 5]] | [[INDEX|📑 Index]] | [[07_Referenzen/Glossar|Glossar]] →

# Lösungen: Projektübungen

> [!tip] Hinweis
> Dies sind Musterlösungen für die großen Projektaufgaben. Deine Implementierung kann abweichen und trotzdem korrekt sein!

## Projekt 1: Datenverarbeitung

### Vollständige Lösung der Kernkomponenten

**Column.convert() Implementierung:**
```python
def convert(self, value: str) -> Any:
    """Konvertiert String-Wert in den richtigen Typ"""
    if value == '' or value is None:
        if self.nullable:
            return None
        raise ValueError(f"Column '{self.name}' cannot be NULL")
    
    try:
        if self.dtype == int:
            return int(value)
        elif self.dtype == float:
            return float(value)
        elif self.dtype == Decimal:
            return Decimal(value)
        elif self.dtype == str:
            return str(value)
        else:
            return value
    except (ValueError, TypeError) as e:
        raise ValueError(f"Cannot convert '{value}' to {self.dtype.__name__}: {e}")
```

**CSVParser.infer_type() Implementierung:**
```python
def infer_type(self, values: List[str]) -> type:
    """Erkennt automatisch den Datentyp einer Spalte"""
    # Filtere leere Werte
    non_empty = [v for v in values if v and v.strip()]
    
    if not non_empty:
        return str
    
    # Teste int
    try:
        for v in non_empty:
            int(v)
        return int
    except ValueError:
        pass
    
    # Teste Decimal (für Geldbeträge)
    try:
        for v in non_empty:
            Decimal(v)
        # Prüfe ob Dezimalstellen
        if any('.' in v for v in non_empty):
            return Decimal
        return int
    except:
        pass
    
    # Teste float
    try:
        for v in non_empty:
            float(v)
        return float
    except ValueError:
        pass
    
    # Default: str
    return str
```

**DataProcessor - Fehlende Methoden:**
```python
def group_by(self, column: str) -> Dict[Any, List[DataRow]]:
    """Gruppiert Zeilen nach Spaltenwert"""
    groups = defaultdict(list)
    for row in self.dataset:
        key = row[column]
        groups[key].append(row)
    return dict(groups)

def aggregate(self, column: str, func: Callable[[List[Any]], Any]) -> Any:
    """Aggregiert Spaltenwerte mit gegebener Funktion"""
    values = [row[column] for row in self.dataset if row[column] is not None]
    if not values:
        return None
    return func(values)

def group_aggregate(self, group_col: str, agg_col: str,
                   agg_func: Callable[[List[Any]], Any]) -> Dict[Any, Any]:
    """Gruppiert und aggregiert in einem Schritt"""
    groups = self.group_by(group_col)
    result = {}
    for key, rows in groups.items():
        values = [row[agg_col] for row in rows if row[agg_col] is not None]
        if values:
            result[key] = agg_func(values)
    return result

def sort_by(self, column: str, reverse: bool = False) -> 'DataProcessor':
    """Sortiert Dataset nach Spalte"""
    sorted_rows = sorted(self.dataset.rows, 
                        key=lambda row: row[column], 
                        reverse=reverse)
    new_dataset = Dataset(
        columns=self.dataset.columns,
        rows=sorted_rows
    )
    return DataProcessor(new_dataset)
```

**Statistics - Fehlende Methoden:**
```python
@staticmethod
def median(values: List[float]) -> float:
    """Median"""
    sorted_values = sorted(values)
    n = len(sorted_values)
    mid = n // 2
    
    if n % 2 == 0:
        return (sorted_values[mid - 1] + sorted_values[mid]) / 2
    return sorted_values[mid]

@staticmethod
def mode(values: List[Any]) -> Any:
    """Häufigster Wert"""
    from collections import Counter
    counter = Counter(values)
    return counter.most_common(1)[0][0]

@staticmethod
def std_dev(values: List[float]) -> float:
    """Standardabweichung"""
    mean = sum(values) / len(values)
    variance = sum((x - mean) ** 2 for x in values) / len(values)
    return variance ** 0.5

@staticmethod
def percentile(values: List[float], p: float) -> float:
    """Berechnet p-tes Perzentil"""
    sorted_values = sorted(values)
    k = (len(sorted_values) - 1) * (p / 100)
    f = int(k)
    c = k - f
    
    if f + 1 < len(sorted_values):
        return sorted_values[f] + c * (sorted_values[f + 1] - sorted_values[f])
    return sorted_values[f]

@staticmethod
def summary(values: List[float]) -> Dict[str, float]:
    """Gibt vollständige Statistik-Übersicht"""
    return {
        'count': len(values),
        'min': min(values),
        'max': max(values),
        'mean': Statistics.mean(values),
        'median': Statistics.median(values),
        'std_dev': Statistics.std_dev(values),
        'q25': Statistics.percentile(values, 25),
        'q75': Statistics.percentile(values, 75)
    }
```

## Projekt 2: Cache Implementation

### Vollständige LRU Cache Lösung

```python
class LRUCache(BaseCache):
    """Least Recently Used Cache - Vollständige Implementierung"""
    
    def __init__(self, max_size: int = 100):
        super().__init__(max_size)
        self._cache: OrderedDict = OrderedDict()
    
    def _evict(self) -> None:
        """Entfernt ältesten Eintrag"""
        if not self._cache:
            return
        evicted_key, _ = self._cache.popitem(last=False)
        self.stats.evictions += 1
    
    def _on_access(self, key: Any) -> None:
        """Verschiebt Key ans Ende"""
        self._cache.move_to_end(key)
    
    def _on_set(self, key: Any, value: Any) -> None:
        """Stellt sicher, dass neue Keys am Ende stehen"""
        if key in self._cache:
            self._cache.move_to_end(key)
```

### Vollständige LFU Cache Lösung

```python
class LFUCache(BaseCache):
    """Least Frequently Used Cache - Vollständige Implementierung"""
    
    def __init__(self, max_size: int = 100):
        super().__init__(max_size)
        self._cache: OrderedDict = OrderedDict()
        self._frequency: Counter = Counter()
    
    def _evict(self) -> None:
        """Entfernt Eintrag mit niedrigster Frequenz"""
        if not self._cache:
            return
        
        # Finde Key mit minimaler Frequenz
        # Bei Gleichstand: Ältester (erster in OrderedDict)
        min_freq = min(self._frequency.values())
        
        for key in self._cache:
            if self._frequency[key] == min_freq:
                del self._cache[key]
                del self._frequency[key]
                self.stats.evictions += 1
                return
```

### Cache Decorator - Vollständig

```python
def cached(cache: BaseCache) -> Callable:
    """Decorator für Function Memoization - Vollständige Implementierung"""
    def decorator(func: Callable) -> Callable:
        @wraps(func)
        def wrapper(*args, **kwargs):
            # Erstelle Cache-Key
            key_parts = [str(args)]
            if kwargs:
                key_parts.append(str(sorted(kwargs.items())))
            cache_key = '|'.join(key_parts)
            
            # Prüfe Cache
            result = cache.get(cache_key)
            if result is not None:
                return result
            
            # Berechne und cache
            result = func(*args, **kwargs)
            cache.set(cache_key, result)
            return result
        
        wrapper.cache = cache
        wrapper.cache_clear = lambda: cache.clear()
        wrapper.cache_info = lambda: cache.stats
        
        return wrapper
    return decorator
```

### TTL Cache - Vollständig

```python
def set(self, key: Any, value: Any, ttl: Optional[float] = None) -> None:
    """Speichert Wert mit TTL"""
    if key not in self._cache and len(self._cache) >= self.max_size:
        self._evict()
    
    expiry_time = time() + (ttl if ttl is not None else self.ttl)
    self._cache[key] = (value, expiry_time)
    self.stats.sets += 1

def _evict(self) -> None:
    """Entfernt abgelaufene Einträge, dann ältesten"""
    current_time = time()
    
    # Entferne alle abgelaufenen
    expired = [
        k for k, (_, expiry) in self._cache.items()
        if current_time > expiry
    ]
    
    for key in expired:
        del self._cache[key]
        self.stats.evictions += 1
    
    # Wenn immer noch voll: Entferne ältesten
    if len(self._cache) >= self.max_size and self._cache:
        oldest_key = min(
            self._cache.keys(),
            key=lambda k: self._cache[k][1]  # Nach expiry_time
        )
        del self._cache[oldest_key]
        self.stats.evictions += 1

def cleanup_expired(self) -> int:
    """Entfernt alle abgelaufenen Einträge"""
    current_time = time()
    expired = [
        k for k, (_, expiry) in self._cache.items()
        if current_time > expiry
    ]
    
    for key in expired:
        del self._cache[key]
        self.stats.evictions += 1
    
    return len(expired)
```

## Projekt 3: JSON Parser

### Lexer - Unicode Escape

```python
def read_string(self) -> str:
    """Liest String mit Unicode-Support"""
    result = []
    self.advance()  # Öffnendes "
    
    while self.current_char() is not None and self.current_char() != '"':
        char = self.current_char()
        
        if char == '\\':
            self.advance()
            escaped = self.current_char()
            
            if escaped is None:
                raise JSONLexerError(f"Unterminated string")
            
            escape_map = {
                '"': '"', '\\': '\\', '/': '/',
                'b': '\b', 'f': '\f', 'n': '\n',
                'r': '\r', 't': '\t',
            }
            
            if escaped in escape_map:
                result.append(escape_map[escaped])
            elif escaped == 'u':
                # Unicode: \uXXXX
                self.advance()
                hex_chars = ''
                for _ in range(4):
                    if self.current_char() and self.current_char() in '0123456789abcdefABCDEF':
                        hex_chars += self.current_char()
                        self.advance()
                    else:
                        raise JSONLexerError(f"Invalid unicode escape")
                
                # Zurück ein Zeichen (wird am Ende advanced)
                self.pos -= 1
                self.column -= 1
                
                code_point = int(hex_chars, 16)
                result.append(chr(code_point))
            else:
                raise JSONLexerError(f"Invalid escape sequence")
        else:
            result.append(char)
        
        self.advance()
    
    if self.current_char() != '"':
        raise JSONLexerError(f"Unterminated string")
    
    self.advance()
    return ''.join(result)
```

### Parser - Object Parsing

```python
def parse_object(self) -> Dict[str, JSONValue]:
    """Parst JSON-Objekt - Vollständig"""
    self.expect(TokenType.LEFT_BRACE)
    
    obj = {}
    
    # Leeres Objekt
    if self.current_token().type == TokenType.RIGHT_BRACE:
        self.advance()
        return obj
    
    # Erstes Pair
    key_token = self.expect(TokenType.STRING)
    key = key_token.value
    
    self.expect(TokenType.COLON)
    
    value = self.parse_value()
    obj[key] = value
    
    # Weitere Pairs
    while self.current_token().type == TokenType.COMMA:
        self.advance()
        
        # Trailing comma check
        if self.current_token().type == TokenType.RIGHT_BRACE:
            token = self.current_token()
            raise JSONParseError(f"Trailing comma not allowed")
        
        key_token = self.expect(TokenType.STRING)
        key = key_token.value
        
        self.expect(TokenType.COLON)
        
        value = self.parse_value()
        obj[key] = value
    
    self.expect(TokenType.RIGHT_BRACE)
    return obj
```

### Encoder - Object Encoding

```python
def _encode_object(self, obj: Dict[str, Any]) -> str:
    """Konvertiert Dict zu JSON-Objekt"""
    if not obj:
        return '{}'
    
    # Prüfe dass alle Keys Strings sind
    for key in obj.keys():
        if not isinstance(key, str):
            raise TypeError(f"Dict keys must be strings, got {type(key)}")
    
    if self.indent is None:
        # Kompakt
        items = [
            f'{self._encode_string(k)}: {self._encode_value(v)}'
            for k, v in obj.items()
        ]
        return '{' + ', '.join(items) + '}'
    else:
        # Pretty-Print
        self._current_indent += self.indent
        items = []
        
        for key, value in obj.items():
            indent_str = ' ' * self._current_indent
            encoded_key = self._encode_string(key)
            encoded_value = self._encode_value(value)
            items.append(f'{indent_str}{encoded_key}: {encoded_value}')
        
        self._current_indent -= self.indent
        
        return '{\n' + ',\n'.join(items) + '\n' + ' ' * self._current_indent + '}'
```

## Projekt 4: Datenbankmodell

### Query.execute() - Vollständig

```python
def execute(self) -> List[Row]:
    """Führt Query aus - Vollständige Implementierung"""
    # 1. Hole alle Zeilen
    results = list(self.table.rows.values())
    
    # 2. WHERE-Bedingungen anwenden
    for condition in self._conditions:
        results = [row for row in results if condition.evaluate(row)]
    
    # 3. ORDER BY anwenden
    if self._order_by:
        column, descending = self._order_by
        results.sort(
            key=lambda row: row.get(column),
            reverse=descending
        )
    
    # 4. OFFSET/LIMIT anwenden
    start = self._offset
    end = start + self._limit if self._limit else None
    results = results[start:end]
    
    # 5. Spalten projizieren
    if self._columns:
        projected = []
        for row in results:
            new_data = {col: row.data[col] for col in self._columns}
            projected.append(Row(new_data, row.rowid))
        return projected
    
    return results
```

### Table - Update und Delete

```python
def update(self, data: Dict[str, Any],
           where: Optional[Callable[[Row], bool]] = None) -> int:
    """Aktualisiert Zeilen"""
    # Validiere Spalten
    for col_name in data.keys():
        if self.schema.get_column(col_name) is None:
            raise DatabaseError(f"Unknown column: {col_name}")
    
    # Finde Zeilen
    to_update = []
    for row in self.rows.values():
        if where is None or where(row):
            to_update.append(row)
    
    # Update
    for row in to_update:
        old_data = row.data.copy()
        
        # Update Werte
        for col_name, new_value in data.items():
            col = self.schema.get_column(col_name)
            
            # Validiere
            valid, error = col.validate(new_value)
            if not valid:
                raise ConstraintViolation(error)
            
            # Prüfe UNIQUE
            if col.unique and new_value != old_data.get(col_name):
                for other_row in self.rows.values():
                    if other_row.rowid != row.rowid:
                        if other_row.get(col_name) == new_value:
                            raise ConstraintViolation(
                                f"Duplicate value for UNIQUE column '{col_name}'"
                            )
            
            row.data[col_name] = new_value
        
        # Update Indizes
        self._update_indexes_update(row.rowid, old_data, row.data)
    
    return len(to_update)

def delete(self, where: Optional[Callable[[Row], bool]] = None) -> int:
    """Löscht Zeilen"""
    to_delete = []
    
    for rowid, row in self.rows.items():
        if where is None or where(row):
            to_delete.append(rowid)
    
    # Lösche
    for rowid in to_delete:
        row = self.rows[rowid]
        del self.rows[rowid]
        
        # Update Indizes
        self._update_indexes_delete(rowid, row.data)
    
    return len(to_delete)

def _update_indexes_update(self, rowid: int, 
                          old_data: Dict[str, Any],
                          new_data: Dict[str, Any]) -> None:
    """Aktualisiert Indizes nach Update"""
    for col_name, index in self._indexes.items():
        old_value = old_data.get(col_name)
        new_value = new_data.get(col_name)
        
        if old_value != new_value:
            # Entferne alten Wert
            if old_value is not None and old_value in index:
                index[old_value].discard(rowid)
                if not index[old_value]:
                    del index[old_value]
            
            # Füge neuen Wert hinzu
            if new_value is not None:
                if new_value not in index:
                    index[new_value] = set()
                index[new_value].add(rowid)

def _update_indexes_delete(self, rowid: int, data: Dict[str, Any]) -> None:
    """Aktualisiert Indizes nach Delete"""
    for col_name, index in self._indexes.items():
        value = data.get(col_name)
        if value is not None and value in index:
            index[value].discard(rowid)
            if not index[value]:
                del index[value]
```

## Performance-Tipps für Projekte

### Projekt 1: Datenverarbeitung
```python
# ✅ Gut: Nutze Generator für große Dateien
def read_large_csv(filepath):
    with open(filepath) as f:
        reader = csv.DictReader(f)
        for row in reader:
            yield process_row(row)

# ❌ Schlecht: Alles in Memory laden
def read_large_csv_bad(filepath):
    with open(filepath) as f:
        return list(csv.DictReader(f))
```

### Projekt 2: Cache
```python
# ✅ Gut: OrderedDict für LRU (O(1))
from collections import OrderedDict
cache = OrderedDict()

# ❌ Schlecht: Liste für LRU (O(n))
cache = []
```

### Projekt 3: JSON Parser
```python
# ✅ Gut: Liste für Tokens (append ist O(1))
tokens = []
tokens.append(token)

# ❌ Schlecht: String concatenation (O(n²))
result = ""
for char in text:
    result += char  # Erstellt neue Strings!
```

### Projekt 4: Datenbank
```python
# ✅ Gut: Index für häufige Lookups
table.find_by_index("id", 123)  # O(1)

# ❌ Schlecht: Sequential scan
[row for row in table.rows.values() if row['id'] == 123]  # O(n)
```

## Verwandte Themen

- [[06_Praxis/03_Projektübungen/01_Projekt - Datenverarbeitung|Projekt 1]]
- [[06_Praxis/03_Projektübungen/02_Projekt - Cache Implementation|Projekt 2]]
- [[06_Praxis/03_Projektübungen/03_Projekt - JSON Parser|Projekt 3]]
- [[06_Praxis/03_Projektübungen/04_Projekt - Datenbankmodell|Projekt 4]]

---
← [[06_Praxis/04_Lösungen/Lösungen Teil 5|Lösungen Teil 5]] | [[INDEX|📑 Index]] | [[07_Referenzen/Glossar|Glossar]] →