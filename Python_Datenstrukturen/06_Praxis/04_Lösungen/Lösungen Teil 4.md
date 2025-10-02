---
tags: [python, datenstrukturen, lösungen]
created: 2025-10-02
---

← [[06_Praxis/04_Lösungen/Lösungen Teil 3|Lösungen Teil 3]] | [[INDEX|📑 Index]] | [[06_Praxis/04_Lösungen/Lösungen Teil 5|Lösungen Teil 5]] →

# Lösungen Teil 4: Fortgeschrittene Strukturen

> [!tip] Hinweis
> Diese Lösungen zeigen fortgeschrittene Techniken mit Dataclasses, Enums und spezialisierten Collections

## 04_Fortgeschritten/02_Deque

### Deque - Übungen

**Übung 1: Deque Basics**
```python
from collections import deque

# Erstellen
dq = deque([1, 2, 3])
print(dq)  # deque([1, 2, 3])

# Append/Pop auf beiden Seiten
dq.append(4)        # Rechts: [1, 2, 3, 4]
dq.appendleft(0)    # Links: [0, 1, 2, 3, 4]

print(dq.pop())     # 4 (von rechts)
print(dq.popleft()) # 0 (von links)
print(dq)           # deque([1, 2, 3])

# Extend
dq.extend([4, 5])       # [1, 2, 3, 4, 5]
dq.extendleft([0, -1])  # [-1, 0, 1, 2, 3, 4, 5]
print(dq)

# Rotate
dq = deque([1, 2, 3, 4, 5])
dq.rotate(2)   # Rechts rotieren
print(dq)      # deque([4, 5, 1, 2, 3])

dq.rotate(-2)  # Links rotieren
print(dq)      # deque([1, 2, 3, 4, 5])
```

**Übung 2: Effiziente Queue**
```python
from collections import deque

class Queue:
    """Effiziente Queue mit deque"""
    
    def __init__(self):
        self._items = deque()
    
    def enqueue(self, item):
        """Fügt am Ende hinzu - O(1)"""
        self._items.append(item)
    
    def dequeue(self):
        """Entfernt vom Anfang - O(1)"""
        if self.is_empty():
            raise IndexError("Dequeue from empty queue")
        return self._items.popleft()
    
    def peek(self):
        """Gibt erstes Element zurück"""
        if self.is_empty():
            raise IndexError("Peek from empty queue")
        return self._items[0]
    
    def is_empty(self):
        return len(self._items) == 0
    
    def __len__(self):
        return len(self._items)
    
    def __repr__(self):
        return f"Queue({list(self._items)})"

# Test
q = Queue()
q.enqueue("A")
q.enqueue("B")
q.enqueue("C")
print(q)  # Queue(['A', 'B', 'C'])

print(q.dequeue())  # A
print(q.dequeue())  # B
print(q.peek())     # C
```

**Übung 3: Sliding Window**
```python
from collections import deque

def sliding_window_max(nums: list, k: int) -> list:
    """
    Findet Maximum in jedem Sliding Window der Größe k
    
    Beispiel: [1,3,-1,-3,5,3,6,7], k=3 -> [3,3,5,5,6,7]
    """
    if not nums or k == 0:
        return []
    
    result = []
    dq = deque()  # Speichert Indices
    
    for i in range(len(nums)):
        # Entferne Elemente außerhalb des Windows
        while dq and dq[0] < i - k + 1:
            dq.popleft()
        
        # Entferne kleinere Elemente (nicht mehr relevant)
        while dq and nums[dq[-1]] < nums[i]:
            dq.pop()
        
        dq.append(i)
        
        # Füge Maximum zum Result hinzu (ab k Elementen)
        if i >= k - 1:
            result.append(nums[dq[0]])
    
    return result

# Test
nums = [1, 3, -1, -3, 5, 3, 6, 7]
k = 3
print(sliding_window_max(nums, k))  # [3, 3, 5, 5, 6, 7]

# Einfachere Version (weniger effizient)
def sliding_window_max_simple(nums: list, k: int) -> list:
    """Einfachere Version mit max()"""
    return [max(nums[i:i+k]) for i in range(len(nums) - k + 1)]

print(sliding_window_max_simple(nums, k))  # [3, 3, 5, 5, 6, 7]
```

**Übung 4: LRU Cache mit Deque**
```python
from collections import deque

class LRUCache:
    """Simple LRU Cache mit Deque"""
    
    def __init__(self, capacity: int):
        self.capacity = capacity
        self.cache = {}  # key -> value
        self.order = deque()  # Speichert Keys in LRU-Reihenfolge
    
    def get(self, key):
        """Holt Wert und markiert als kürzlich verwendet"""
        if key not in self.cache:
            return None
        
        # Bewege Key ans Ende (most recently used)
        self.order.remove(key)
        self.order.append(key)
        
        return self.cache[key]
    
    def put(self, key, value):
        """Fügt Key-Value-Paar ein"""
        if key in self.cache:
            # Update existing
            self.cache[key] = value
            self.order.remove(key)
            self.order.append(key)
        else:
            # Add new
            if len(self.cache) >= self.capacity:
                # Evict LRU (leftmost)
                lru_key = self.order.popleft()
                del self.cache[lru_key]
            
            self.cache[key] = value
            self.order.append(key)
    
    def __repr__(self):
        return f"LRUCache({list(self.order)})"

# Test
cache = LRUCache(3)
cache.put('a', 1)
cache.put('b', 2)
cache.put('c', 3)
print(cache)  # LRUCache(['a', 'b', 'c'])

cache.get('a')  # Macht 'a' zu MRU
print(cache)    # LRUCache(['b', 'c', 'a'])

cache.put('d', 4)  # Evicted 'b' (LRU)
print(cache)       # LRUCache(['c', 'a', 'd'])
```

**Übung 5: Palindrom-Check mit Deque**
```python
from collections import deque

def is_palindrome(s: str) -> bool:
    """Prüft Palindrom mit Deque"""
    # Bereinige String
    s = ''.join(c.lower() for c in s if c.isalnum())
    
    dq = deque(s)
    
    while len(dq) > 1:
        if dq.popleft() != dq.pop():
            return False
    
    return True

# Tests
print(is_palindrome("A man a plan a canal Panama"))  # True
print(is_palindrome("race a car"))  # False
print(is_palindrome("Was it a car or a cat I saw"))  # True
```

## 04_Fortgeschritten/03_Heap_Queue

### Heapq - Übungen

**Übung 1: Heap Basics**
```python
import heapq

# Min-Heap erstellen
heap = []
heapq.heappush(heap, 5)
heapq.heappush(heap, 2)
heapq.heappush(heap, 8)
heapq.heappush(heap, 1)

print(heap)  # [1, 2, 8, 5] (Heap-Struktur, nicht sortiert!)

# Kleinestes Element holen
smallest = heapq.heappop(heap)
print(f"Smallest: {smallest}")  # 1
print(heap)  # [2, 5, 8]

# Von Liste zu Heap
numbers = [5, 2, 8, 1, 9, 3]
heapq.heapify(numbers)
print(numbers)  # [1, 2, 3, 5, 9, 8]

# N kleinste/größte Elemente
data = [5, 2, 8, 1, 9, 3, 7, 4, 6]
print(heapq.nsmallest(3, data))  # [1, 2, 3]
print(heapq.nlargest(3, data))   # [9, 8, 7]
```

**Übung 2: Priority Queue**
```python
import heapq

class PriorityQueue:
    """Priority Queue mit heapq"""
    
    def __init__(self):
        self._queue = []
        self._index = 0  # Für gleiche Prioritäten
    
    def push(self, item, priority):
        """Fügt Item mit Priorität hinzu (kleinere Zahl = höhere Priorität)"""
        # Tuple: (priority, index, item)
        # Index verhindert Vergleich von items
        heapq.heappush(self._queue, (priority, self._index, item))
        self._index += 1
    
    def pop(self):
        """Entfernt und gibt Item mit höchster Priorität zurück"""
        if self.is_empty():
            raise IndexError("Pop from empty queue")
        return heapq.heappop(self._queue)[-1]  # Nur Item zurück
    
    def peek(self):
        """Gibt Item mit höchster Priorität zurück"""
        if self.is_empty():
            raise IndexError("Peek from empty queue")
        return self._queue[0][-1]
    
    def is_empty(self):
        return len(self._queue) == 0
    
    def __len__(self):
        return len(self._queue)

# Test
pq = PriorityQueue()
pq.push("Low priority task", 5)
pq.push("High priority task", 1)
pq.push("Medium priority task", 3)

print(pq.pop())  # High priority task
print(pq.pop())  # Medium priority task
print(pq.pop())  # Low priority task
```

**Übung 3: K größte Elemente im Stream**
```python
import heapq

class KLargest:
    """Tracked die K größten Elemente in einem Stream"""
    
    def __init__(self, k: int):
        self.k = k
        self.heap = []
    
    def add(self, num: int) -> int:
        """
        Fügt Zahl hinzu und gibt k-größtes Element zurück
        
        Verwendet Min-Heap der Größe k
        """
        if len(self.heap) < self.k:
            heapq.heappush(self.heap, num)
        elif num > self.heap[0]:
            heapq.heapreplace(self.heap, num)
        
        return self.heap[0]  # Kleinstes der k größten

# Test
kl = KLargest(3)
print(kl.add(3))  # 3 (nur [3])
print(kl.add(5))  # 3 (nur [3, 5])
print(kl.add(10)) # 3 ([3, 5, 10])
print(kl.add(9))  # 5 ([5, 9, 10])
print(kl.add(4))  # 5 ([5, 9, 10])
```

**Übung 4: Merge Sorted Lists**
```python
import heapq

def merge_sorted_lists(*lists):
    """Merged mehrere sortierte Listen"""
    # Verwende Heap für effizientes Merging
    heap = []
    
    # Initialisiere Heap mit ersten Elementen
    for i, lst in enumerate(lists):
        if lst:
            heapq.heappush(heap, (lst[0], i, 0))  # (wert, listen_idx, element_idx)
    
    result = []
    
    while heap:
        val, list_idx, elem_idx = heapq.heappop(heap)
        result.append(val)
        
        # Füge nächstes Element aus dieser Liste hinzu
        if elem_idx + 1 < len(lists[list_idx]):
            next_val = lists[list_idx][elem_idx + 1]
            heapq.heappush(heap, (next_val, list_idx, elem_idx + 1))
    
    return result

# Test
list1 = [1, 4, 7]
list2 = [2, 5, 8]
list3 = [3, 6, 9]

merged = merge_sorted_lists(list1, list2, list3)
print(merged)  # [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

**Übung 5: Median im Stream**
```python
import heapq

class MedianFinder:
    """Findet Median in einem Stream von Zahlen"""
    
    def __init__(self):
        # Two heaps: max_heap für kleinere Hälfte, min_heap für größere
        self.max_heap = []  # Negative Werte für Max-Heap
        self.min_heap = []
    
    def add_num(self, num: int):
        """Fügt Zahl hinzu"""
        # Füge zu max_heap hinzu (kleinere Hälfte)
        heapq.heappush(self.max_heap, -num)
        
        # Balance: Größtes von max_heap zu min_heap
        heapq.heappush(self.min_heap, -heapq.heappop(self.max_heap))
        
        # Balance Größen
        if len(self.max_heap) < len(self.min_heap):
            heapq.heappush(self.max_heap, -heapq.heappop(self.min_heap))
    
    def find_median(self) -> float:
        """Gibt aktuellen Median zurück"""
        if len(self.max_heap) > len(self.min_heap):
            return -self.max_heap[0]
        return (-self.max_heap[0] + self.min_heap[0]) / 2

# Test
mf = MedianFinder()
mf.add_num(1)
print(f"Median: {mf.find_median()}")  # 1.0

mf.add_num(2)
print(f"Median: {mf.find_median()}")  # 1.5

mf.add_num(3)
print(f"Median: {mf.find_median()}")  # 2.0
```

## 04_Fortgeschritten/04_Dataclasses

### Dataclass - Übungen

**Übung 1: Basic Dataclass**
```python
from dataclasses import dataclass

@dataclass
class Person:
    """Repräsentiert eine Person"""
    name: str
    age: int
    email: str
    
    def is_adult(self) -> bool:
        return self.age >= 18

# Erstellen
person = Person("Alice", 30, "alice@example.com")
print(person)  # Person(name='Alice', age=30, email='alice@example.com')

# Zugriff
print(person.name)  # Alice

# Methode aufrufen
print(person.is_adult())  # True

# Gleichheit
person2 = Person("Alice", 30, "alice@example.com")
print(person == person2)  # True

# Mutability
person.age = 31
print(person)  # Person(name='Alice', age=31, ...)
```

**Übung 2: Default Values und Field**
```python
from dataclasses import dataclass, field
from typing import List

@dataclass
class Student:
    """Student mit Default-Werten"""
    name: str
    age: int
    grades: List[int] = field(default_factory=list)
    active: bool = True
    
    def average_grade(self) -> float:
        if not self.grades:
            return 0.0
        return sum(self.grades) / len(self.grades)
    
    def add_grade(self, grade: int):
        self.grades.append(grade)

# Test
student = Student("Bob", 20)
print(student)  # grades ist []

student.add_grade(85)
student.add_grade(90)
print(f"Average: {student.average_grade()}")  # 87.5

# Wichtig: Niemals mutable defaults direkt!
# FALSCH: grades: List[int] = []  # Shared zwischen Instanzen!
# RICHTIG: grades: List[int] = field(default_factory=list)
```

**Übung 3: Frozen Dataclass**
```python
from dataclasses import dataclass

@dataclass(frozen=True)
class Point:
    """Unveränderlicher Punkt"""
    x: float
    y: float
    
    def distance_to(self, other: 'Point') -> float:
        """Berechnet Distanz zu anderem Punkt"""
        return ((self.x - other.x)**2 + (self.y - other.y)**2)**0.5

# Erstellen
p1 = Point(0, 0)
p2 = Point(3, 4)

print(p1.distance_to(p2))  # 5.0

# Immutable
try:
    p1.x = 10
except Exception as e:
    print(f"Error: {e}")  # cannot assign to field 'x'

# Kann als Dict-Key verwendet werden
points = {
    Point(0, 0): "Origin",
    Point(1, 1): "Diagonal"
}
print(points[Point(0, 0)])  # Origin
```

**Übung 4: Post-Init Processing**
```python
from dataclasses import dataclass, field

@dataclass
class Rectangle:
    """Rechteck mit automatischer Flächen-Berechnung"""
    width: float
    height: float
    area: float = field(init=False)  # Nicht im __init__
    
    def __post_init__(self):
        """Wird nach __init__ aufgerufen"""
        self.area = self.width * self.height

# Test
rect = Rectangle(5, 10)
print(rect)  # Rectangle(width=5, height=10, area=50)

@dataclass
class User:
    """User mit Validierung"""
    username: str
    email: str
    age: int
    
    def __post_init__(self):
        # Validierung
        if self.age < 0:
            raise ValueError("Age must be positive")
        if '@' not in self.email:
            raise ValueError("Invalid email")
        
        # Normalisierung
        self.username = self.username.lower()

user = User("Alice", "alice@example.com", 25)
print(user.username)  # alice (lowercase)
```

**Übung 5: Ordering und Comparison**
```python
from dataclasses import dataclass

@dataclass(order=True)
class Student:
    """Student mit automatischem Ordering"""
    grade: float
    name: str = field(compare=False)  # Nicht für Vergleich
    age: int = field(compare=False)
    
    def __repr__(self):
        return f"{self.name} (Grade: {self.grade})"

# Studenten
students = [
    Student(85, "Alice", 20),
    Student(92, "Bob", 22),
    Student(78, "Charlie", 21),
]

# Sortieren (nach grade)
students.sort()
for student in students:
    print(student)

# Vergleiche
print(students[0] < students[1])  # True (78 < 85)
print(max(students))  # Bob (Grade: 92)
```

## 04_Fortgeschritten/05_Enums

### Enum - Übungen

**Übung 1: Basic Enums**
```python
from enum import Enum, auto

class Color(Enum):
    """Farben"""
    RED = 1
    GREEN = 2
    BLUE = 3

# Zugriff
print(Color.RED)        # Color.RED
print(Color.RED.name)   # RED
print(Color.RED.value)  # 1

# Iteration
for color in Color:
    print(f"{color.name}: {color.value}")

# Vergleich
print(Color.RED == Color.RED)    # True
print(Color.RED is Color.RED)    # True (Singleton!)
print(Color.RED == 1)            # False

# Von Value
print(Color(1))  # Color.RED

# Auto-values
class Status(Enum):
    PENDING = auto()    # 1
    APPROVED = auto()   # 2
    REJECTED = auto()   # 3

print(Status.PENDING.value)  # 1
```

**Übung 2: String Enums**
```python
from enum import Enum

class Environment(Enum):
    """Umgebungen"""
    DEVELOPMENT = "dev"
    STAGING = "stg"
    PRODUCTION = "prod"
    
    def is_production(self) -> bool:
        return self == Environment.PRODUCTION

# Verwendung
env = Environment.PRODUCTION
print(env.value)  # prod
print(env.is_production())  # True

# Von String
env2 = Environment("dev")
print(env2)  # Environment.DEVELOPMENT

# In Config
config = {
    "database": "db.sqlite",
    "env": Environment.DEVELOPMENT
}

if config["env"] == Environment.DEVELOPMENT:
    print("Debug mode active")
```

**Übung 3: IntEnum für Flags**
```python
from enum import IntEnum, IntFlag

class Priority(IntEnum):
    """Prioritäten (können verglichen werden)"""
    LOW = 1
    MEDIUM = 2
    HIGH = 3
    CRITICAL = 4

# Vergleiche möglich
print(Priority.HIGH > Priority.LOW)  # True
print(Priority.CRITICAL >= Priority.HIGH)  # True

# Sortieren
tasks = [
    ("Fix bug", Priority.HIGH),
    ("Add feature", Priority.MEDIUM),
    ("Critical issue", Priority.CRITICAL),
]

tasks.sort(key=lambda x: x[1], reverse=True)
for task, prio in tasks:
    print(f"{task}: {prio.name}")

# IntFlag für Bitwise Operations
class Permission(IntFlag):
    """Permissions als Flags"""
    READ = 1
    WRITE = 2
    EXECUTE = 4
    DELETE = 8

# Kombinieren
user_perms = Permission.READ | Permission.WRITE
print(user_perms)  # Permission.READ|WRITE

# Prüfen
print(Permission.READ in user_perms)  # True
print(Permission.DELETE in user_perms)  # False

admin_perms = Permission.READ | Permission.WRITE | Permission.EXECUTE | Permission.DELETE
print(admin_perms)  # Permission.READ|WRITE|EXECUTE|DELETE
```

**Übung 4: Enum mit Methoden**
```python
from enum import Enum
from datetime import datetime, timedelta

class Weekday(Enum):
    """Wochentage mit Hilfsmethoden"""
    MONDAY = 1
    TUESDAY = 2
    WEDNESDAY = 3
    THURSDAY = 4
    FRIDAY = 5
    SATURDAY = 6
    SUNDAY = 7
    
    def is_weekend(self) -> bool:
        return self in (Weekday.SATURDAY, Weekday.SUNDAY)
    
    def is_workday(self) -> bool:
        return not self.is_weekend()
    
    def next_day(self) -> 'Weekday':
        """Gibt nächsten Tag zurück"""
        next_val = self.value % 7 + 1
        return Weekday(next_val)
    
    @classmethod
    def from_date(cls, date: datetime) -> 'Weekday':
        """Erstellt von Datum"""
        # isoweekday: Monday=1, Sunday=7
        return cls(date.isoweekday())

# Tests
today = Weekday.FRIDAY
print(today.is_workday())  # True
print(today.next_day())    # Weekday.SATURDAY

weekend = Weekday.SATURDAY
print(weekend.is_weekend())  # True

# Von Datum
date = datetime(2025, 10, 2)  # Ein Donnerstag
day = Weekday.from_date(date)
print(f"{date.strftime('%Y-%m-%d')} ist ein {day.name}")
```

**Übung 5: State Machine mit Enums**
```python
from enum import Enum, auto
from typing import Set

class OrderState(Enum):
    """Order States"""
    PENDING = auto()
    CONFIRMED = auto()
    SHIPPED = auto()
    DELIVERED = auto()
    CANCELLED = auto()

class Order:
    """Bestellung mit State Machine"""
    
    # Erlaubte Transitionen
    TRANSITIONS = {
        OrderState.PENDING: {OrderState.CONFIRMED, OrderState.CANCELLED},
        OrderState.CONFIRMED: {OrderState.SHIPPED, OrderState.CANCELLED},
        OrderState.SHIPPED: {OrderState.DELIVERED},
        OrderState.DELIVERED: set(),
        OrderState.CANCELLED: set(),
    }
    
    def __init__(self, order_id: str):
        self.order_id = order_id
        self.state = OrderState.PENDING
    
    def transition_to(self, new_state: OrderState):
        """Wechselt State mit Validierung"""
        if new_state not in self.TRANSITIONS[self.state]:
            raise ValueError(
                f"Invalid transition: {self.state.name} -> {new_state.name}"
            )
        
        print(f"Order {self.order_id}: {self.state.name} -> {new_state.name}")
        self.state = new_state
    
    def confirm(self):
        self.transition_to(OrderState.CONFIRMED)
    
    def ship(self):
        self.transition_to(OrderState.SHIPPED)
    
    def deliver(self):
        self.transition_to(OrderState.DELIVERED)
    
    def cancel(self):
        self.transition_to(OrderState.CANCELLED)

# Test
order = Order("ORD-123")
order.confirm()  # PENDING -> CONFIRMED
order.ship()     # CONFIRMED -> SHIPPED
order.deliver()  # SHIPPED -> DELIVERED

# Invalide Transition
try:
    order.cancel()  # Fehler: Delivered kann nicht cancelled werden
except ValueError as e:
    print(f"Error: {e}")
```

## Verwandte Themen

- [[04_Fortgeschritten/02_Deque/01_Deque Basics|Deque]]
- [[04_Fortgeschritten/03_Heap_Queue/01_Heapq Module|Heapq]]
- [[04_Fortgeschritten/04_Dataclasses/01_Dataclass Basics|Dataclasses]]
- [[04_Fortgeschritten/05_Enums/01_Enum Basics|Enums]]

---
← [[06_Praxis/04_Lösungen/Lösungen Teil 3|Lösungen Teil 3]] | [[INDEX|📑 Index]] | [[06_Praxis/04_Lösungen/Lösungen Teil 5|Lösungen Teil 5]] →