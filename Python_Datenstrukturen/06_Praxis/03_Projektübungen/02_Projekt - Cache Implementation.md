---
tags: [python, datenstrukturen, praxis, projekt, cache]
created: 2025-10-02
---

← [[06_Praxis/03_Projektübungen/01_Projekt - Datenverarbeitung|Projekt - Datenverarbeitung]] | [[INDEX|📑 Index]] | [[06_Praxis/03_Projektübungen/03_Projekt - JSON Parser|Projekt - JSON Parser]] →

# Projekt: Cache Implementation

> [!tip] Projektziel
> Implementiere ein vollständiges, produktionsreifes Caching-System mit verschiedenen Eviction-Strategien und Performance-Monitoring

## Projektübersicht

In diesem Projekt entwickelst du ein **Multi-Strategy Caching System**, das:
- Verschiedene Cache-Strategien unterstützt (LRU, LFU, FIFO, TTL)
- Thread-safe ist (optional)
- Performance-Metriken sammelt
- Decorator-Interface bietet
- Serialisierung unterstützt

**Geschätzte Bearbeitungszeit:** 3-4 Stunden

## Anforderungen

### Funktionale Anforderungen

1. **Cache-Strategien**
   - LRU (Least Recently Used)
   - LFU (Least Frequently Used)
   - FIFO (First In First Out)
   - TTL (Time To Live)

2. **Funktionalität**
   - Get/Set/Delete Operationen
   - Cache-Größenlimit
   - Automatische Eviction
   - Cache-Statistiken

3. **Extras**
   - Decorator für Function Caching
   - Persistierung (Save/Load)
   - Cache-Invalidierung

### Technische Anforderungen

- Verwende OrderedDict, deque, Counter
- Implementiere alle Strategien als Klassen
- Type Hints und Docstrings
- Umfassende Tests

## Projektstruktur

```python
from typing import Any, Optional, Callable, Dict, Tuple
from collections import OrderedDict, deque, Counter, defaultdict
from dataclasses import dataclass, field
from functools import wraps
from time import time
import pickle
from pathlib import Path
```

## Aufgabe 1: Cache-Statistiken (Leicht)

Erstelle ein System zur Erfassung von Cache-Metriken.

```python
from dataclasses import dataclass, field
from typing import Dict, Any
from time import time

@dataclass
class CacheStats:
    """Sammelt Cache-Statistiken"""
    hits: int = 0
    misses: int = 0
    evictions: int = 0
    sets: int = 0
    deletes: int = 0
    start_time: float = field(default_factory=time)
    
    @property
    def hit_rate(self) -> float:
        """Berechnet Hit-Rate in Prozent"""
        total = self.hits + self.misses
        return (self.hits / total * 100) if total > 0 else 0.0
    
    @property
    def total_operations(self) -> int:
        """Gesamtzahl aller Operationen"""
        return self.hits + self.misses + self.sets + self.deletes
    
    @property
    def uptime(self) -> float:
        """Cache-Laufzeit in Sekunden"""
        return time() - self.start_time
    
    def reset(self) -> None:
        """Setzt alle Statistiken zurück"""
        # TODO: Implementieren
        pass
    
    def __repr__(self) -> str:
        """Formatierte Ausgabe der Statistiken"""
        return (
            f"CacheStats(\n"
            f"  Hits: {self.hits}, Misses: {self.misses}, "
            f"Hit Rate: {self.hit_rate:.2f}%\n"
            f"  Evictions: {self.evictions}, Sets: {self.sets}, "
            f"Deletes: {self.deletes}\n"
            f"  Total Operations: {self.total_operations}, "
            f"Uptime: {self.uptime:.2f}s\n"
            f")"
        )
```

## Aufgabe 2: Base Cache Class (Mittel)

Implementiere die Basis-Klasse für alle Cache-Strategien.

```python
from typing import Any, Optional, Dict
from abc import ABC, abstractmethod

class CacheException(Exception):
    """Base Exception für Cache-Fehler"""
    pass

class CacheFullException(CacheException):
    """Cache ist voll und kann nicht erweitert werden"""
    pass

class BaseCache(ABC):
    """Abstrakte Basis-Klasse für alle Cache-Implementierungen"""
    
    def __init__(self, max_size: int = 100):
        """
        Args:
            max_size: Maximale Anzahl Einträge im Cache
        """
        if max_size <= 0:
            raise ValueError("max_size muss > 0 sein")
        
        self.max_size = max_size
        self._cache: Dict[Any, Any] = {}
        self.stats = CacheStats()
    
    def get(self, key: Any, default: Any = None) -> Any:
        """
        Holt Wert aus Cache
        
        Args:
            key: Cache-Key
            default: Rückgabewert wenn Key nicht existiert
            
        Returns:
            Gecachter Wert oder default
        """
        if key in self._cache:
            self.stats.hits += 1
            value = self._cache[key]
            self._on_access(key)  # Hook für Subklassen
            return value
        else:
            self.stats.misses += 1
            return default
    
    def set(self, key: Any, value: Any) -> None:
        """
        Speichert Wert im Cache
        
        Args:
            key: Cache-Key
            value: Zu speichernder Wert
        """
        # Wenn Cache voll und Key neu, evicte
        if key not in self._cache and len(self._cache) >= self.max_size:
            self._evict()
        
        self._cache[key] = value
        self.stats.sets += 1
        self._on_set(key, value)  # Hook für Subklassen
    
    def delete(self, key: Any) -> bool:
        """
        Entfernt Eintrag aus Cache
        
        Returns:
            True wenn Key existierte, sonst False
        """
        if key in self._cache:
            del self._cache[key]
            self.stats.deletes += 1
            self._on_delete(key)  # Hook für Subklassen
            return True
        return False
    
    def clear(self) -> None:
        """Leert den kompletten Cache"""
        self._cache.clear()
        self._on_clear()  # Hook für Subklassen
    
    def __contains__(self, key: Any) -> bool:
        """Ermöglicht 'key in cache' Syntax"""
        return key in self._cache
    
    def __len__(self) -> int:
        """Gibt Anzahl der Einträge zurück"""
        return len(self._cache)
    
    def __repr__(self) -> str:
        return (f"{self.__class__.__name__}"
                f"(size={len(self)}/{self.max_size})")
    
    # Abstrakte Methoden - müssen in Subklassen implementiert werden
    
    @abstractmethod
    def _evict(self) -> None:
        """Entfernt einen Eintrag nach Cache-Strategie"""
        pass
    
    # Hooks - können in Subklassen überschrieben werden
    
    def _on_access(self, key: Any) -> None:
        """Wird bei jedem Cache-Hit aufgerufen"""
        pass
    
    def _on_set(self, key: Any, value: Any) -> None:
        """Wird nach jedem Set aufgerufen"""
        pass
    
    def _on_delete(self, key: Any) -> None:
        """Wird nach jedem Delete aufgerufen"""
        pass
    
    def _on_clear(self) -> None:
        """Wird nach Clear aufgerufen"""
        pass
```

## Aufgabe 3: LRU Cache (Mittel)

Implementiere Least Recently Used Cache mit OrderedDict.

```python
from collections import OrderedDict

class LRUCache(BaseCache):
    """
    Least Recently Used Cache
    
    Entfernt den am längsten nicht verwendeten Eintrag.
    Verwendet OrderedDict für O(1) Zugriff und Reordering.
    """
    
    def __init__(self, max_size: int = 100):
        super().__init__(max_size)
        # Überschreibe _cache mit OrderedDict
        self._cache: OrderedDict = OrderedDict()
    
    def _evict(self) -> None:
        """Entfernt ältesten (am wenigsten kürzlich verwendeten) Eintrag"""
        if not self._cache:
            return
        
        # LIFO=False bedeutet FIFO: erstes Element entfernen
        evicted_key, _ = self._cache.popitem(last=False)
        self.stats.evictions += 1
    
    def _on_access(self, key: Any) -> None:
        """Verschiebt Key ans Ende (markiert als kürzlich verwendet)"""
        # TODO: Implementieren
        # Tipp: move_to_end() Methode von OrderedDict
        pass
    
    def _on_set(self, key: Any, value: Any) -> None:
        """Stellt sicher, dass neue Keys am Ende stehen"""
        # TODO: Implementieren
        pass
```

**Erwartetes Verhalten:**
```python
cache = LRUCache(max_size=3)

cache.set('a', 1)
cache.set('b', 2)
cache.set('c', 3)
print(list(cache._cache.keys()))  # ['a', 'b', 'c']

cache.get('a')  # Greift auf 'a' zu
print(list(cache._cache.keys()))  # ['b', 'c', 'a']

cache.set('d', 4)  # Cache voll, 'b' wird evicted
print(list(cache._cache.keys()))  # ['c', 'a', 'd']
print(cache.stats.evictions)       # 1
```

## Aufgabe 4: LFU Cache (Schwer)

Implementiere Least Frequently Used Cache mit Counter.

```python
from collections import Counter, OrderedDict

class LFUCache(BaseCache):
    """
    Least Frequently Used Cache
    
    Entfernt den am seltensten verwendeten Eintrag.
    Bei Gleichstand: Ältester Eintrag wird entfernt.
    """
    
    def __init__(self, max_size: int = 100):
        super().__init__(max_size)
        self._cache: OrderedDict = OrderedDict()
        self._frequency: Counter = Counter()
    
    def _evict(self) -> None:
        """Entfernt Eintrag mit niedrigster Frequenz"""
        # TODO: Implementieren
        # Tipp: min() mit key=lambda nutzen
        # Bei Gleichstand: Nutze OrderedDict-Reihenfolge
        pass
    
    def _on_access(self, key: Any) -> None:
        """Erhöht Zugriffs-Frequenz"""
        self._frequency[key] += 1
    
    def _on_set(self, key: Any, value: Any) -> None:
        """Initialisiert Frequenz für neue Keys"""
        if key not in self._frequency:
            self._frequency[key] = 1
    
    def _on_delete(self, key: Any) -> None:
        """Entfernt Frequenz-Eintrag"""
        if key in self._frequency:
            del self._frequency[key]
    
    def _on_clear(self) -> None:
        """Setzt Frequenzen zurück"""
        self._frequency.clear()
    
    def get_frequency(self, key: Any) -> int:
        """Gibt Zugriffs-Frequenz für Key zurück"""
        return self._frequency.get(key, 0)
```

**Erwartetes Verhalten:**
```python
cache = LFUCache(max_size=3)

cache.set('a', 1)
cache.set('b', 2)
cache.set('c', 3)

cache.get('a')  # freq(a) = 2
cache.get('a')  # freq(a) = 3
cache.get('b')  # freq(b) = 2

cache.set('d', 4)  # 'c' wird evicted (freq=1)
print('c' in cache)  # False
print('a' in cache)  # True (freq=3)
```

## Aufgabe 5: FIFO Cache (Leicht)

Implementiere First In First Out Cache mit deque.

```python
from collections import deque

class FIFOCache(BaseCache):
    """
    First In First Out Cache
    
    Entfernt den ältesten Eintrag (Reihenfolge des Einfügens).
    """
    
    def __init__(self, max_size: int = 100):
        super().__init__(max_size)
        self._order: deque = deque()
    
    def _evict(self) -> None:
        """Entfernt ältesten Eintrag"""
        # TODO: Implementieren
        # Tipp: popleft() von deque
        pass
    
    def _on_set(self, key: Any, value: Any) -> None:
        """Fügt Key zur Queue hinzu wenn neu"""
        # TODO: Implementieren
        # Achtung: Nur bei neuen Keys hinzufügen!
        pass
    
    def _on_delete(self, key: Any) -> None:
        """Entfernt Key aus Queue"""
        if key in self._order:
            self._order.remove(key)
    
    def _on_clear(self) -> None:
        """Leert Queue"""
        self._order.clear()
```

## Aufgabe 6: TTL Cache (Schwer)

Implementiere Time To Live Cache.

```python
from time import time
from typing import Tuple

class TTLCache(BaseCache):
    """
    Time To Live Cache
    
    Einträge verfallen nach bestimmter Zeit automatisch.
    """
    
    def __init__(self, max_size: int = 100, ttl: float = 60.0):
        """
        Args:
            max_size: Maximale Cache-Größe
            ttl: Time to live in Sekunden (default: 60s)
        """
        super().__init__(max_size)
        self.ttl = ttl
        # Speichert (value, expiry_time) Tupel
        self._cache: Dict[Any, Tuple[Any, float]] = {}
    
    def get(self, key: Any, default: Any = None) -> Any:
        """Holt Wert, prüft aber zuerst Ablaufzeit"""
        if key not in self._cache:
            self.stats.misses += 1
            return default
        
        value, expiry = self._cache[key]
        
        # Prüfe ob abgelaufen
        if time() > expiry:
            # Eintrag ist abgelaufen
            del self._cache[key]
            self.stats.misses += 1
            self.stats.evictions += 1
            return default
        
        self.stats.hits += 1
        self._on_access(key)
        return value
    
    def set(self, key: Any, value: Any, ttl: Optional[float] = None) -> None:
        """
        Speichert Wert mit TTL
        
        Args:
            key: Cache-Key
            value: Wert
            ttl: Optionale individuelle TTL (überschreibt Standard)
        """
        # TODO: Implementieren
        # Speichere (value, expiry_time) wo expiry_time = time() + ttl
        pass
    
    def _evict(self) -> None:
        """Entfernt abgelaufene Einträge, dann ältesten"""
        # Zuerst: Entferne alle abgelaufenen
        current_time = time()
        expired = [
            k for k, (_, expiry) in self._cache.items()
            if current_time > expiry
        ]
        
        for key in expired:
            del self._cache[key]
            self.stats.evictions += 1
        
        # Wenn immer noch voll: Entferne ältesten
        if len(self._cache) >= self.max_size:
            # TODO: Implementieren
            # Tipp: min() nach expiry_time
            pass
    
    def cleanup_expired(self) -> int:
        """
        Entfernt alle abgelaufenen Einträge
        
        Returns:
            Anzahl entfernter Einträge
        """
        # TODO: Implementieren
        pass
```

**Erwartetes Verhalten:**
```python
cache = TTLCache(max_size=10, ttl=2.0)  # 2 Sekunden TTL

cache.set('temp', 'data')
print(cache.get('temp'))  # 'data'

time.sleep(3)
print(cache.get('temp'))  # None (abgelaufen)

# Individuelle TTL
cache.set('short', 'data', ttl=1.0)
cache.set('long', 'data', ttl=10.0)
```

## Aufgabe 7: Cache Decorator (Mittel)

Erstelle einen Decorator für Function Memoization.

```python
from functools import wraps
from typing import Callable, Any

def cached(cache: BaseCache) -> Callable:
    """
    Decorator für automatisches Function Caching
    
    Example:
        cache = LRUCache(max_size=100)
        
        @cached(cache)
        def expensive_function(n):
            time.sleep(1)
            return n * n
    """
    def decorator(func: Callable) -> Callable:
        @wraps(func)
        def wrapper(*args, **kwargs):
            # TODO: Implementieren
            # Erstelle Cache-Key aus args und kwargs
            # Tipp: Nutze str(args) + str(sorted(kwargs.items()))
            # oder verwende hash() für hashbare Argumente
            pass
        
        # Füge Hilfsfunktionen hinzu
        wrapper.cache = cache
        wrapper.cache_clear = lambda: cache.clear()
        wrapper.cache_info = lambda: cache.stats
        
        return wrapper
    return decorator
```

**Erwartete Verwendung:**
```python
import time

lru = LRUCache(max_size=3)

@cached(lru)
def fibonacci(n: int) -> int:
    """Rekursive Fibonacci mit Caching"""
    if n < 2:
        return n
    return fibonacci(n-1) + fibonacci(n-2)

start = time.time()
result = fibonacci(35)
duration = time.time() - start

print(f"Ergebnis: {result}")
print(f"Dauer: {duration:.4f}s")
print(f"Cache: {lru.stats}")

# Zweiter Aufruf sollte viel schneller sein
start = time.time()
result = fibonacci(35)
duration = time.time() - start
print(f"Dauer (gecached): {duration:.6f}s")
```

## Aufgabe 8: Cache Persistence (Mittel)

Implementiere Speichern und Laden des Cache-Inhalts.

```python
import pickle
from pathlib import Path
from typing import Any

class PersistentCache(BaseCache):
    """Cache mit Persistierung"""
    
    def __init__(self, max_size: int = 100, 
                 filepath: Optional[Path] = None):
        super().__init__(max_size)
        self.filepath = filepath or Path('cache.pkl')
    
    def save(self, filepath: Optional[Path] = None) -> None:
        """
        Speichert Cache-Inhalt in Datei
        
        Args:
            filepath: Optionaler anderer Pfad
        """
        path = filepath or self.filepath
        data = {
            'cache': self._cache,
            'stats': self.stats,
            'max_size': self.max_size
        }
        # TODO: Implementieren mit pickle
        pass
    
    def load(self, filepath: Optional[Path] = None) -> bool:
        """
        Lädt Cache-Inhalt aus Datei
        
        Returns:
            True wenn erfolgreich, False bei Fehler
        """
        path = filepath or self.filepath
        if not path.exists():
            return False
        
        try:
            # TODO: Implementieren
            pass
        except Exception as e:
            print(f"Fehler beim Laden: {e}")
            return False
    
    def __enter__(self):
        """Context Manager: Lädt beim Start"""
        self.load()
        return self
    
    def __exit__(self, exc_type, exc_val, exc_tb):
        """Context Manager: Speichert beim Ende"""
        self.save()
```

**Erwartete Verwendung:**
```python
# Cache verwenden und automatisch speichern
with PersistentCache(max_size=100) as cache:
    cache.set('key', 'value')
    # ... weitere Operationen
# Cache wird automatisch gespeichert

# Beim nächsten Mal: Daten sind noch da
with PersistentCache(max_size=100) as cache:
    print(cache.get('key'))  # 'value'
```

## Aufgabe 9: Vollständiges System (Schwer)

Kombiniere alle Komponenten zu einem Cache-Manager.

```python
from typing import Dict, Type, Optional
from enum import Enum

class CacheStrategy(Enum):
    """Verfügbare Cache-Strategien"""
    LRU = "lru"
    LFU = "lfu"
    FIFO = "fifo"
    TTL = "ttl"

class CacheManager:
    """Verwaltet mehrere Cache-Instanzen"""
    
    def __init__(self):
        self._caches: Dict[str, BaseCache] = {}
        self._strategy_map: Dict[CacheStrategy, Type[BaseCache]] = {
            CacheStrategy.LRU: LRUCache,
            CacheStrategy.LFU: LFUCache,
            CacheStrategy.FIFO: FIFOCache,
            CacheStrategy.TTL: TTLCache,
        }
    
    def create_cache(self, name: str, 
                     strategy: CacheStrategy,
                     max_size: int = 100,
                     **kwargs) -> BaseCache:
        """
        Erstellt und registriert einen neuen Cache
        
        Args:
            name: Eindeutiger Cache-Name
            strategy: Cache-Strategie
            max_size: Maximale Größe
            **kwargs: Zusätzliche Strategie-spezifische Parameter
            
        Returns:
            Erstellte Cache-Instanz
        """
        if name in self._caches:
            raise ValueError(f"Cache '{name}' existiert bereits")
        
        cache_class = self._strategy_map[strategy]
        cache = cache_class(max_size=max_size, **kwargs)
        self._caches[name] = cache
        return cache
    
    def get_cache(self, name: str) -> Optional[BaseCache]:
        """Holt Cache-Instanz nach Name"""
        return self._caches.get(name)
    
    def remove_cache(self, name: str) -> bool:
        """Entfernt Cache"""
        if name in self._caches:
            del self._caches[name]
            return True
        return False
    
    def get_stats(self) -> Dict[str, CacheStats]:
        """Gibt Statistiken für alle Caches zurück"""
        return {
            name: cache.stats 
            for name, cache in self._caches.items()
        }
    
    def print_summary(self) -> None:
        """Gibt formatierte Zusammenfassung aller Caches aus"""
        print("\n" + "="*70)
        print("CACHE MANAGER SUMMARY")
        print("="*70)
        
        for name, cache in self._caches.items():
            print(f"\n{name} ({cache.__class__.__name__}):")
            print(f"  Size: {len(cache)}/{cache.max_size}")
            print(f"  Hit Rate: {cache.stats.hit_rate:.2f}%")
            print(f"  Operations: {cache.stats.total_operations}")
```

**Vollständiges Beispiel:**
```python
def main():
    """Demo-Programm"""
    
    manager = CacheManager()
    
    # Erstelle verschiedene Caches
    user_cache = manager.create_cache(
        'users', 
        CacheStrategy.LRU, 
        max_size=1000
    )
    
    session_cache = manager.create_cache(
        'sessions',
        CacheStrategy.TTL,
        max_size=500,
        ttl=300.0  # 5 Minuten
    )
    
    stats_cache = manager.create_cache(
        'statistics',
        CacheStrategy.LFU,
        max_size=100
    )
    
    # Verwende Caches
    user_cache.set('user:123', {'name': 'Alice', 'email': 'alice@example.com'})
    session_cache.set('session:abc', {'user_id': 123, 'ip': '192.168.1.1'})
    stats_cache.set('page_views', 1000)
    
    # Simuliere Zugriffe
    for i in range(100):
        user_cache.get('user:123')
        stats_cache.get('page_views')
    
    # Zeige Zusammenfassung
    manager.print_summary()

if __name__ == '__main__':
    main()
```

**Erwarteter Output:**
```
======================================================================
CACHE MANAGER SUMMARY
======================================================================

users (LRUCache):
  Size: 1/1000
  Hit Rate: 99.01%
  Operations: 101

sessions (TTLCache):
  Size: 1/500
  Hit Rate: 0.00%
  Operations: 1

statistics (LFUCache):
  Size: 1/100
  Hit Rate: 99.01%
  Operations: 101
```

## Performance-Tests

Erstelle Performance-Vergleiche:

```python
import time
from typing import List

def benchmark_cache(cache: BaseCache, 
                   operations: int = 10000) -> Dict[str, float]:
    """
    Misst Cache-Performance
    
    Returns:
        Dict mit Timing-Informationen
    """
    results = {}
    
    # Write Performance
    start = time.perf_counter()
    for i in range(operations):
        cache.set(f'key_{i}', i)
    results['write_time'] = time.perf_counter() - start
    
    # Read Performance (Hits)
    start = time.perf_counter()
    for i in range(operations):
        cache.get(f'key_{i % cache.max_size}')
    results['read_time'] = time.perf_counter() - start
    
    # Read Performance (Misses)
    start = time.perf_counter()
    for i in range(operations):
        cache.get(f'missing_{i}')
    results['miss_time'] = time.perf_counter() - start
    
    return results

# Vergleiche alle Strategien
strategies = {
    'LRU': LRUCache(1000),
    'LFU': LFUCache(1000),
    'FIFO': FIFOCache(1000),
    'TTL': TTLCache(1000, ttl=60.0)
}

print("\nPerformance Comparison (10000 operations):")
print("="*60)
for name, cache in strategies.items():
    results = benchmark_cache(cache)
    print(f"\n{name}:")
    print(f"  Write: {results['write_time']:.4f}s")
    print(f"  Read:  {results['read_time']:.4f}s")
    print(f"  Miss:  {results['miss_time']:.4f}s")
```

## Lernziele & verwendete Konzepte

**Datenstrukturen:**
- ✅ OrderedDict für LRU
- ✅ Counter für LFU
- ✅ deque für FIFO
- ✅ Dict für TTL
- ✅ Enum für Strategien

**Design Patterns:**
- Abstract Base Class (ABC)
- Strategy Pattern
- Decorator Pattern
- Context Manager
- Hook Methods

**Performance:**
- O(1) für get/set bei allen Strategien
- O(1) für eviction bei LRU/FIFO
- O(n) für eviction bei LFU
- Memory-effiziente Implementierungen

## Bewertungskriterien

### Funktionalität (40%)
- [ ] Alle Strategien korrekt implementiert
- [ ] Eviction funktioniert wie erwartet
- [ ] Stats werden korrekt erfasst

### Code-Qualität (30%)
- [ ] ABC richtig verwendet
- [ ] Type Hints vollständig
- [ ] Keine Code-Duplikation

### Performance (20%)
- [ ] Richtige Datenstrukturen gewählt
- [ ] O(1) Operationen wo möglich
- [ ] Memory-effizient

### Extras (10%)
- [ ] Decorator implementiert
- [ ] Persistence funktioniert
- [ ] CacheManager vollständig

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [collections.OrderedDict](https://docs.python.org/3/library/collections.html#collections.OrderedDict)
- [collections.Counter](https://docs.python.org/3/library/collections.html#collections.Counter)
- [collections.deque](https://docs.python.org/3/library/collections.html#collections.deque)
- [functools.wraps](https://docs.python.org/3/library/functools.html#functools.wraps)
- [pickle](https://docs.python.org/3/library/pickle.html)

## Verwandte Themen

- [[03_Mappings_und_Sets/01_Dictionaries/06_OrderedDict vs Dict|OrderedDict]]
- [[03_Mappings_und_Sets/03_Collections_Module/01_Counter|Counter]]
- [[04_Fortgeschritten/02_Deque/01_Deque Basics|Deque]]
- [[06_Praxis/02_Performance/01_Performance Messung|Performance Messung]]

---
← [[06_Praxis/03_Projektübungen/01_Projekt - Datenverarbeitung|Projekt - Datenverarbeitung]] | [[INDEX|📑 Index]] | [[06_Praxis/03_Projektübungen/03_Projekt - JSON Parser|Projekt - JSON Parser]] →