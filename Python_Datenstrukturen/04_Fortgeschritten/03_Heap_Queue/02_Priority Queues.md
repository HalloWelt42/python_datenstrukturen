---
tags: [python, datenstrukturen, heapq, priority-queue, queue]
created: 2025-10-02
---

← [[04_Fortgeschritten/03_Heap_Queue/01_Heapq Module|Heapq Module]] | [[INDEX|📑 Index]] | [[04_Fortgeschritten/03_Heap_Queue/03_Übungen Heapq|Übungen Heapq]] →

# Priority Queues

> [!tip] Lernziel
> Nach dieser Seite kannst du Priority Queues mit heapq implementieren, verstehst verschiedene Priorisierungsstrategien, kennst fortgeschrittene Patterns wie Entry Wrapper und kannst Priority Queues in realen Szenarien einsetzen.

## Was ist eine Priority Queue?

Eine **Priority Queue** ist eine Datenstruktur, bei der jedes Element eine **Priorität** hat. Elemente mit höherer Priorität werden vor Elementen mit niedrigerer Priorität verarbeitet, unabhängig von ihrer Einfüge-Reihenfolge.

### Unterschied zu normalen Queues

```python
# Normale Queue (FIFO)
queue = []
queue.append('Task A')  # Zuerst eingefügt
queue.append('Task B')
queue.append('Task C')
print(queue.pop(0))  # 'Task A' - FIFO

# Priority Queue
# Task mit höchster Priorität wird zuerst verarbeitet,
# unabhängig von Einfüge-Reihenfolge
```

### Anwendungsfälle

Priority Queues sind ideal für:
- 🎯 **Task Scheduling** - Dringende Tasks zuerst
- 🚑 **Notfall-Systeme** - Kritische Fälle zuerst
- 🗺️ **Pfadfindung** (Dijkstra, A*) - Kürzeste Wege
- 📧 **Email-Verarbeitung** - Wichtige Emails zuerst
- 🎮 **Event-Systeme** - Events nach Timestamp
- 💾 **Ressourcen-Verwaltung** - Prozesse nach Priorität

## Einfache Priority Queue Implementation

### Basis-Implementation mit heapq

```python
import heapq

class SimplePriorityQueue:
    """Einfache Priority Queue (niedrigere Zahl = höhere Priorität)"""
    
    def __init__(self):
        self._queue = []
        self._index = 0  # Für stabile Sortierung bei gleicher Priorität
    
    def push(self, item, priority):
        """Fügt Item mit Priorität hinzu"""
        # Tuple: (priorität, index, item)
        # index sorgt für FIFO bei gleicher Priorität
        heapq.heappush(self._queue, (priority, self._index, item))
        self._index += 1
    
    def pop(self):
        """Entfernt und gibt Item mit höchster Priorität zurück"""
        if self._queue:
            return heapq.heappop(self._queue)[-1]  # Gibt nur item zurück
        raise IndexError("Priority Queue ist leer")
    
    def is_empty(self):
        """Prüft ob Queue leer ist"""
        return len(self._queue) == 0
    
    def size(self):
        """Gibt Anzahl Elemente zurück"""
        return len(self._queue)

# Verwendung
pq = SimplePriorityQueue()

pq.push("Task C", priority=3)
pq.push("Task A", priority=1)  # Höchste Priorität (niedrigste Zahl)
pq.push("Task B", priority=2)

print(pq.pop())  # Task A
print(pq.pop())  # Task B
print(pq.pop())  # Task C
```

💡 **Wichtig:** Der `_index` stellt sicher, dass bei gleicher Priorität die Einfüge-Reihenfolge (FIFO) erhalten bleibt.

### Warum der Index wichtig ist

```python
import heapq

# OHNE Index - Problem bei gleicher Priorität
queue = []
heapq.heappush(queue, (1, "Task A"))
heapq.heappush(queue, (1, "Task B"))  # Gleiche Priorität
# Fehler: TypeError bei Vergleich von Strings wenn Priorität gleich!

# MIT Index - Funktioniert
queue = []
heapq.heappush(queue, (1, 0, "Task A"))
heapq.heappush(queue, (1, 1, "Task B"))  # Index bricht Gleichstand
print(heapq.heappop(queue))  # (1, 0, 'Task A') - FIFO bei gleicher Priorität
print(heapq.heappop(queue))  # (1, 1, 'Task B')
```

## Fortgeschrittene Priority Queue mit Entry Wrapper

### Problem: Nicht-vergleichbare Objekte

```python
import heapq

class Task:
    def __init__(self, name, priority):
        self.name = name
        self.priority = priority

# Problem: Tasks sind nicht direkt vergleichbar
pq = []
try:
    heapq.heappush(pq, (1, Task("A", 1)))
    heapq.heappush(pq, (1, Task("B", 1)))  # TypeError!
except TypeError as e:
    print(f"Fehler: {e}")
```

### Lösung: Entry Wrapper Klasse

```python
import heapq
from dataclasses import dataclass, field
from typing import Any

@dataclass(order=True)
class PrioritizedItem:
    """Wrapper für Priority Queue Items"""
    priority: int
    index: int = field(compare=True)  # Für stabile Sortierung
    item: Any = field(compare=False)  # Nicht vergleichen

class PriorityQueue:
    """Vollständige Priority Queue Implementation"""
    
    def __init__(self):
        self._queue = []
        self._index = 0
    
    def push(self, item, priority):
        """Fügt Item mit Priorität hinzu"""
        entry = PrioritizedItem(priority, self._index, item)
        heapq.heappush(self._queue, entry)
        self._index += 1
    
    def pop(self):
        """Entfernt und gibt Item mit höchster Priorität zurück"""
        if self._queue:
            return heapq.heappop(self._queue).item
        raise IndexError("Priority Queue ist leer")
    
    def peek(self):
        """Zeigt Item mit höchster Priorität ohne Entfernen"""
        if self._queue:
            return self._queue[0].item
        raise IndexError("Priority Queue ist leer")
    
    def is_empty(self):
        return len(self._queue) == 0
    
    def __len__(self):
        return len(self._queue)

# Verwendung mit komplexen Objekten
class Task:
    def __init__(self, name, description):
        self.name = name
        self.description = description
    
    def __repr__(self):
        return f"Task('{self.name}')"

pq = PriorityQueue()

pq.push(Task("Deploy", "Deploy to production"), priority=1)
pq.push(Task("Bug Fix", "Fix critical bug"), priority=0)  # Höchste Priorität
pq.push(Task("Feature", "Add new feature"), priority=2)

print(pq.pop())  # Task('Bug Fix')
print(pq.pop())  # Task('Deploy')
print(pq.pop())  # Task('Feature')
```

## Priority Queue mit dynamischen Prioritäten

### Problem: Priorität ändern

In manchen Szenarien müssen Prioritäten nachträglich geändert werden (z.B. Task wird dringender).

```python
import heapq
from dataclasses import dataclass, field
from typing import Any, Optional

@dataclass(order=True)
class Entry:
    priority: int
    index: int = field(compare=True)
    item: Any = field(compare=False)
    valid: bool = field(default=True, compare=False)

class DynamicPriorityQueue:
    """Priority Queue mit Update-Funktion"""
    
    def __init__(self):
        self._queue = []
        self._entry_finder = {}  # item -> entry mapping
        self._index = 0
    
    def push(self, item, priority):
        """Fügt Item hinzu oder updated Priorität"""
        if item in self._entry_finder:
            self.remove(item)
        
        entry = Entry(priority, self._index, item)
        self._entry_finder[item] = entry
        heapq.heappush(self._queue, entry)
        self._index += 1
    
    def remove(self, item):
        """Entfernt Item (markiert als invalid)"""
        entry = self._entry_finder.pop(item)
        entry.valid = False
    
    def pop(self):
        """Entfernt und gibt Item mit höchster Priorität zurück"""
        while self._queue:
            entry = heapq.heappop(self._queue)
            if entry.valid:
                del self._entry_finder[entry.item]
                return entry.item
        raise IndexError("Priority Queue ist leer")
    
    def update_priority(self, item, new_priority):
        """Aktualisiert Priorität eines Items"""
        if item in self._entry_finder:
            self.remove(item)
        self.push(item, new_priority)
    
    def is_empty(self):
        # Bereinige invalide Einträge
        while self._queue and not self._queue[0].valid:
            heapq.heappop(self._queue)
        return len(self._queue) == 0

# Verwendung
dpq = DynamicPriorityQueue()

dpq.push("Task A", priority=3)
dpq.push("Task B", priority=1)
dpq.push("Task C", priority=2)

print(f"Peek: {dpq._queue[0].item}")  # Task B (priority 1)

# Task A wird dringend!
dpq.update_priority("Task A", priority=0)
print(f"Nach Update: {dpq.pop()}")  # Task A (neue priority 0)
```

💡 **Technik:** Statt tatsächlich zu entfernen, markieren wir Einträge als `invalid` (Lazy Deletion). Das ist O(log n) statt O(n).

## Praktische Anwendungsbeispiele

### Beispiel 1: Task Scheduler

```python
import heapq
import time
from dataclasses import dataclass
from typing import Callable

@dataclass(order=True)
class ScheduledTask:
    """Task mit Ausführungszeit und Priorität"""
    execute_at: float  # Timestamp
    priority: int
    name: str = ""
    action: Callable = None
    
    def __post_init__(self):
        # Für Sortierung: Zeitpunkt wichtiger als Priorität
        # Aber bei gleichem Zeitpunkt gilt Priorität
        pass

class TaskScheduler:
    """Scheduler für zeitbasierte Tasks mit Prioritäten"""
    
    def __init__(self):
        self._queue = []
        self._index = 0
    
    def schedule(self, name, action, delay=0, priority=0):
        """
        Plant Task für Ausführung.
        
        Args:
            name: Task-Name
            action: Auszuführende Funktion
            delay: Verzögerung in Sekunden
            priority: Priorität (bei gleichem Zeitpunkt)
        """
        execute_at = time.time() + delay
        # Tuple: (execute_at, priority, index, name, action)
        entry = (execute_at, priority, self._index, name, action)
        heapq.heappush(self._queue, entry)
        self._index += 1
        print(f"📅 Geplant: {name} in {delay}s (Priorität: {priority})")
    
    def run_pending(self):
        """Führt alle fälligen Tasks aus"""
        now = time.time()
        executed = []
        
        while self._queue and self._queue[0][0] <= now:
            execute_at, priority, idx, name, action = heapq.heappop(self._queue)
            print(f"▶️  Führe aus: {name}")
            
            try:
                action()
                executed.append(name)
            except Exception as e:
                print(f"❌ Fehler bei {name}: {e}")
        
        return executed
    
    def get_next_task_info(self):
        """Gibt Info über nächste Task zurück"""
        if not self._queue:
            return None
        
        execute_at, priority, idx, name, _ = self._queue[0]
        wait_time = max(0, execute_at - time.time())
        return {
            'name': name,
            'priority': priority,
            'wait_seconds': wait_time
        }
    
    def pending_count(self):
        """Anzahl wartender Tasks"""
        return len(self._queue)

# Verwendung
scheduler = TaskScheduler()

def send_email():
    print("  📧 Email gesendet")

def backup_database():
    print("  💾 Datenbank gesichert")

def cleanup():
    print("  🧹 Cleanup durchgeführt")

# Tasks planen
scheduler.schedule("Email", send_email, delay=0, priority=2)
scheduler.schedule("Backup", backup_database, delay=0, priority=1)  # Höhere Priorität
scheduler.schedule("Cleanup", cleanup, delay=1, priority=3)

print(f"\n{scheduler.pending_count()} Tasks geplant\n")

# Sofort fällige Tasks ausführen
scheduler.run_pending()

print("\nWarte 1.5 Sekunden...")
time.sleep(1.5)

# Rest ausführen
scheduler.run_pending()
```

### Beispiel 2: Event-System für Spiel

```python
import heapq
from typing import Callable
from dataclasses import dataclass, field

@dataclass(order=True)
class GameEvent:
    """Event mit Zeitpunkt und Priorität"""
    timestamp: float
    priority: int = field(compare=True)
    event_type: str = field(compare=False)
    data: dict = field(compare=False, default_factory=dict)
    callback: Callable = field(compare=False, default=None)

class GameEventQueue:
    """Event Queue für Spiel-Engine"""
    
    def __init__(self):
        self._queue = []
        self._current_time = 0.0
    
    def schedule_event(self, event_type, delay, priority=0, callback=None, **data):
        """Plant Event für spätere Ausführung"""
        event = GameEvent(
            timestamp=self._current_time + delay,
            priority=priority,
            event_type=event_type,
            data=data,
            callback=callback
        )
        heapq.heappush(self._queue, event)
    
    def update(self, delta_time):
        """Update-Schleife: Zeit vorspulen und Events ausführen"""
        self._current_time += delta_time
        
        executed_events = []
        
        # Führe alle fälligen Events aus
        while self._queue and self._queue[0].timestamp <= self._current_time:
            event = heapq.heappop(self._queue)
            executed_events.append(event)
            
            if event.callback:
                event.callback(event)
        
        return executed_events
    
    def peek_next(self):
        """Zeigt nächstes Event"""
        return self._queue[0] if self._queue else None
    
    def time_until_next(self):
        """Zeit bis zum nächsten Event"""
        if not self._queue:
            return float('inf')
        return max(0, self._queue[0].timestamp - self._current_time)

# Verwendung
event_queue = GameEventQueue()

def on_spawn(event):
    print(f"  🐉 Spawn: {event.data['enemy_type']} bei Position {event.data['position']}")

def on_powerup(event):
    print(f"  ⭐ PowerUp: {event.data['type']}")

def on_boss(event):
    print(f"  👹 BOSS erscheint!")

# Events planen
print("=== Spiel-Simulation ===\n")

event_queue.schedule_event("spawn", delay=1.0, priority=1, 
                           callback=on_spawn,
                           enemy_type="Goblin", position=(10, 20))

event_queue.schedule_event("spawn", delay=1.0, priority=2,
                           callback=on_spawn,
                           enemy_type="Slime", position=(15, 25))

event_queue.schedule_event("powerup", delay=2.0, priority=0,  # Höchste Priorität
                           callback=on_powerup,
                           type="Shield")

event_queue.schedule_event("boss", delay=5.0, priority=0,
                           callback=on_boss)

# Simuliere Spiel-Loop
game_time = 0.0
frame_time = 0.5  # 0.5 Sekunden pro Frame

while game_time < 6.0:
    print(f"Zeit: {game_time:.1f}s")
    events = event_queue.update(frame_time)
    
    if events:
        for event in events:
            pass  # Callback wurde bereits ausgeführt
    else:
        print("  (keine Events)")
    
    game_time += frame_time
    print()
```

### Beispiel 3: Dijkstra's Shortest Path

```python
import heapq
from collections import defaultdict

def dijkstra(graph, start):
    """
    Findet kürzeste Pfade von start zu allen anderen Knoten.
    
    Args:
        graph: Dict von {knoten: [(nachbar, gewicht), ...]}
        start: Start-Knoten
    
    Returns:
        Dict von {knoten: (distanz, pfad)}
    """
    # Priority Queue: (distanz, knoten, pfad)
    pq = [(0, start, [start])]
    distances = {start: 0}
    paths = {start: [start]}
    visited = set()
    
    while pq:
        current_dist, current, path = heapq.heappop(pq)
        
        if current in visited:
            continue
        
        visited.add(current)
        
        # Prüfe alle Nachbarn
        for neighbor, weight in graph.get(current, []):
            distance = current_dist + weight
            
            # Kürzerer Pfad gefunden?
            if neighbor not in distances or distance < distances[neighbor]:
                distances[neighbor] = distance
                new_path = path + [neighbor]
                paths[neighbor] = new_path
                heapq.heappush(pq, (distance, neighbor, new_path))
    
    return distances, paths

# Beispiel-Graph
graph = {
    'A': [('B', 4), ('C', 2)],
    'B': [('C', 1), ('D', 5)],
    'C': [('D', 8), ('E', 10)],
    'D': [('E', 2)],
    'E': []
}

distances, paths = dijkstra(graph, 'A')

print("=== Kürzeste Pfade von A ===\n")
for node in sorted(distances.keys()):
    print(f"{node}: Distanz = {distances[node]}, Pfad = {' → '.join(paths[node])}")

# Ausgabe:
# A: Distanz = 0, Pfad = A
# B: Distanz = 4, Pfad = A → B
# C: Distanz = 2, Pfad = A → C
# D: Distanz = 5, Pfad = A → C → B → D
# E: Distanz = 7, Pfad = A → C → B → D → E
```

### Beispiel 4: Job Queue mit Timeouts

```python
import heapq
import time
from dataclasses import dataclass, field
from typing import Callable, Optional

@dataclass(order=True)
class Job:
    priority: int
    created_at: float = field(compare=True)
    timeout: float = field(compare=False)
    name: str = field(compare=False)
    action: Callable = field(compare=False)
    
    def is_expired(self, current_time):
        """Prüft ob Job abgelaufen ist"""
        return current_time - self.created_at > self.timeout

class JobQueue:
    """Job Queue mit Prioritäten und Timeouts"""
    
    def __init__(self):
        self._queue = []
        self._processed = []
        self._expired = []
    
    def add_job(self, name, action, priority=0, timeout=10.0):
        """Fügt Job hinzu"""
        job = Job(
            priority=priority,
            created_at=time.time(),
            timeout=timeout,
            name=name,
            action=action
        )
        heapq.heappush(self._queue, job)
        print(f"➕ Job '{name}' hinzugefügt (Priorität: {priority}, Timeout: {timeout}s)")
    
    def process_next(self):
        """Verarbeitet nächsten Job"""
        current_time = time.time()
        
        # Überspringe abgelaufene Jobs
        while self._queue:
            job = heapq.heappop(self._queue)
            
            if job.is_expired(current_time):
                print(f"⏱️  Job '{job.name}' abgelaufen")
                self._expired.append(job)
                continue
            
            # Führe Job aus
            print(f"▶️  Verarbeite: {job.name}")
            try:
                job.action()
                self._processed.append(job)
                return job
            except Exception as e:
                print(f"❌ Fehler: {e}")
                return None
        
        return None
    
    def process_all(self, max_jobs=None):
        """Verarbeitet alle Jobs (oder max_jobs)"""
        count = 0
        while self._queue and (max_jobs is None or count < max_jobs):
            if self.process_next():
                count += 1
        return count
    
    def stats(self):
        """Gibt Statistiken zurück"""
        return {
            'pending': len(self._queue),
            'processed': len(self._processed),
            'expired': len(self._expired)
        }

# Verwendung
jq = JobQueue()

jq.add_job("Critical Update", lambda: print("  🔴 Kritisches Update"), priority=0, timeout=5.0)
jq.add_job("Send Report", lambda: print("  📊 Bericht senden"), priority=2, timeout=10.0)
jq.add_job("Cleanup", lambda: print("  🧹 Cleanup"), priority=3, timeout=15.0)
jq.add_job("Quick Task", lambda: print("  ⚡ Schnelle Task"), priority=1, timeout=2.0)

print("\n--- Sofortige Verarbeitung ---")
jq.process_next()

print("\n--- Warte 3 Sekunden ---")
time.sleep(3)

print("\n--- Verarbeite Rest ---")
jq.process_all()

print(f"\n📈 Statistiken: {jq.stats()}")
```

## Performance-Vergleich: Verschiedene Queue-Implementierungen

```python
import heapq
import time
from collections import deque
import random

def benchmark_priority_queues(n=10000):
    """Vergleicht verschiedene PQ-Implementierungen"""
    
    # Test-Daten
    tasks = [(random.randint(0, 100), f"Task-{i}") for i in range(n)]
    
    print(f"Benchmark mit {n:,} Tasks\n")
    
    # 1. heapq (empfohlen)
    start = time.perf_counter()
    pq = []
    for priority, task in tasks:
        heapq.heappush(pq, (priority, task))
    while pq:
        heapq.heappop(pq)
    heapq_time = time.perf_counter() - start
    
    # 2. Sortierte Liste (INEFFIZIENT!)
    start = time.perf_counter()
    pq = []
    for priority, task in tasks:
        pq.append((priority, task))
        pq.sort()  # O(n log n) jedes Mal!
    while pq:
        pq.pop(0)
    sorted_list_time = time.perf_counter() - start
    
    # 3. Sortierte Liste (nur am Ende sortieren)
    start = time.perf_counter()
    pq = []
    for priority, task in tasks:
        pq.append((priority, task))
    pq.sort()
    while pq:
        pq.pop(0)
    sorted_once_time = time.perf_counter() - start
    
    print(f"heapq:                {heapq_time:.4f}s  (Baseline)")
    print(f"Sortierte Liste (immer): {sorted_list_time:.4f}s  ({sorted_list_time/heapq_time:.1f}x langsamer)")
    print(f"Sortierte Liste (einmal): {sorted_once_time:.4f}s  ({sorted_once_time/heapq_time:.1f}x langsamer)")

benchmark_priority_queues()
```

## Best Practices

### ✅ Verwende Priority Queues wenn:

1. **Du das Element mit höchster/niedrigster Priorität brauchst**
   ```python
   # Immer das wichtigste Element zuerst
   ```

2. **Prioritäten sich ändern können**
   ```python
   # Mit DynamicPriorityQueue
   ```

3. **Du Event-Scheduling implementierst**
   ```python
   # Tasks/Events nach Zeitpunkt sortiert
   ```

### ❌ Verwende KEINE Priority Queue wenn:

1. **Du nur FIFO (normale Queue) brauchst**
   ```python
   # Verwende deque stattdessen
   from collections import deque
   q = deque()
   ```

2. **Du alle Elemente gleich behandelst**
   ```python
   # Normale Liste ist einfacher
   ```

3. **Du beliebige Elemente entfernen musst**
   ```python
   # Priority Queue ist nicht für remove(arbitrary_item) optimiert
   ```

## Übungsaufgaben

Siehe [[04_Fortgeschritten/03_Heap_Queue/03_Übungen Heapq|Übungen Heapq]] für praktische Aufgaben mit Priority Queues.

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [heapq - Priority Queue](https://docs.python.org/3/library/heapq.html#priority-queue-implementation-notes) - Priority Queue Patterns
- [queue.PriorityQueue](https://docs.python.org/3/library/queue.html#queue.PriorityQueue) - Thread-safe Alternative
- [Implementing a Priority Queue](https://docs.python.org/3/library/heapq.html#priority-queue-implementation-notes) - Offizielle Implementierungs-Notizen

## Verwandte Themen

- [[04_Fortgeschritten/03_Heap_Queue/01_Heapq Module|Heapq Module]] - Heap-Grundlagen
- [[04_Fortgeschritten/03_Heap_Queue/03_Übungen Heapq|Übungen Heapq]] - Praktische Übungen
- [[04_Fortgeschritten/02_Deque/01_Deque Basics|Deque Basics]] - Alternative für FIFO-Queues
- [[06_Praxis/01_Best_Practices/03_Datenstruktur Auswahl|Datenstruktur Auswahl]] - Wann was verwenden

---
← [[04_Fortgeschritten/03_Heap_Queue/01_Heapq Module|Heapq Module]] | [[INDEX|📑 Index]] | [[04_Fortgeschritten/03_Heap_Queue/03_Übungen Heapq|Übungen Heapq]] →