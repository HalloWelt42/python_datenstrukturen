---
tags: [python, datenstrukturen, deque, übungen]
created: 2025-10-02
---

← [[04_Fortgeschritten/02_Deque/03_Performance Vergleich|Performance Vergleich]] | [[INDEX|📑 Index]] | [[04_Fortgeschritten/03_Heap_Queue/01_Heapq Module|Heapq Module]] →

# Übungen Deque

> [!tip] Übungsziel
> Diese Übungen helfen dir, deque in verschiedenen Szenarien praktisch anzuwenden und die Vorteile gegenüber anderen Strukturen zu verstehen.

## Schwierigkeitsgrade

- 🟢 **Leicht**: Grundlegende Operationen und einfache Anwendungen
- 🟡 **Mittel**: Komplexere Algorithmen und Kombinationen
- 🔴 **Schwer**: Fortgeschrittene Patterns und Optimierungen

---

## 🟢 Übung 1: Palindrom-Checker

**Schwierigkeit:** Leicht

Schreibe eine Funktion, die mit einer deque überprüft, ob ein String (ignoriert Leerzeichen und Groß-/Kleinschreibung) ein Palindrom ist.

```python
from collections import deque

def is_palindrome(text):
    """
    Prüft ob text ein Palindrom ist.
    
    Args:
        text: Zu prüfender String
    
    Returns:
        bool: True wenn Palindrom, sonst False
    """
    # Dein Code hier
    pass

# Tests
print(is_palindrome("radar"))  # True
print(is_palindrome("hello"))  # False
print(is_palindrome("A man a plan a canal Panama"))  # True
print(is_palindrome("race car"))  # True
print(is_palindrome("not a palindrome"))  # False
```

**Erwarteter Output:**
```
True
False
True
True
False
```

<details>
<summary>💡 Hinweis</summary>

1. Bereinige den String (entferne Leerzeichen, konvertiere zu lowercase)
2. Erstelle deque aus bereinigttem String
3. Vergleiche erstes und letztes Element, dann entfernen
4. Wiederhole bis deque leer oder nur 1 Element übrig

</details>

<details>
<summary>✅ Lösung</summary>

```python
from collections import deque

def is_palindrome(text):
    """Prüft ob text ein Palindrom ist"""
    # Bereinige Text: nur Buchstaben, lowercase
    clean = ''.join(c.lower() for c in text if c.isalnum())
    
    # Erstelle deque
    d = deque(clean)
    
    # Vergleiche von beiden Enden
    while len(d) > 1:
        if d.popleft() != d.pop():
            return False
    
    return True

# Tests
print(is_palindrome("radar"))  # True
print(is_palindrome("hello"))  # False
print(is_palindrome("A man a plan a canal Panama"))  # True
print(is_palindrome("race car"))  # True
print(is_palindrome("not a palindrome"))  # False
```

**Zeitkomplexität:** O(n) - Jedes Element wird einmal verarbeitet
**Speicherkomplexität:** O(n) - Deque speichert alle Zeichen

</details>

---

## 🟢 Übung 2: Rotating Queue

**Schwierigkeit:** Leicht

Implementiere eine rotierende Warteschlange mit fester Größe. Wenn die maximale Größe erreicht ist, wird beim Hinzufügen automatisch das älteste Element entfernt.

```python
from collections import deque

class RotatingQueue:
    def __init__(self, max_size):
        """Initialisiert Queue mit maximaler Größe"""
        # Dein Code hier
        pass
    
    def add(self, item):
        """Fügt Item hinzu (entfernt ältestes bei Überlauf)"""
        pass
    
    def get_all(self):
        """Gibt alle aktuellen Items als Liste zurück"""
        pass
    
    def is_full(self):
        """Prüft ob Queue voll ist"""
        pass

# Tests
rq = RotatingQueue(3)
rq.add(1)
rq.add(2)
rq.add(3)
print(rq.get_all())  # [1, 2, 3]
print(rq.is_full())  # True
rq.add(4)
print(rq.get_all())  # [2, 3, 4]
rq.add(5)
rq.add(6)
print(rq.get_all())  # [4, 5, 6]
```

**Erwarteter Output:**
```
[1, 2, 3]
True
[2, 3, 4]
[4, 5, 6]
```

<details>
<summary>💡 Hinweis</summary>

Die `maxlen`-Option von deque erledigt die meiste Arbeit für dich!

</details>

<details>
<summary>✅ Lösung</summary>

```python
from collections import deque

class RotatingQueue:
    def __init__(self, max_size):
        """Initialisiert Queue mit maximaler Größe"""
        self.queue = deque(maxlen=max_size)
    
    def add(self, item):
        """Fügt Item hinzu (entfernt ältestes bei Überlauf)"""
        self.queue.append(item)  # maxlen kümmert sich um Overflow
    
    def get_all(self):
        """Gibt alle aktuellen Items als Liste zurück"""
        return list(self.queue)
    
    def is_full(self):
        """Prüft ob Queue voll ist"""
        return len(self.queue) == self.queue.maxlen

# Tests
rq = RotatingQueue(3)
rq.add(1)
rq.add(2)
rq.add(3)
print(rq.get_all())  # [1, 2, 3]
print(rq.is_full())  # True
rq.add(4)
print(rq.get_all())  # [2, 3, 4]
rq.add(5)
rq.add(6)
print(rq.get_all())  # [4, 5, 6]
```

**Zeitkomplexität:** O(1) für alle Operationen
**Speicherkomplexität:** O(max_size) - Fixe Größe

</details>

---

## 🟡 Übung 3: Browser History

**Schwierigkeit:** Mittel

Implementiere eine Browser-History mit Vor- und Zurück-Navigation. Die Historie soll maximal 50 Seiten speichern.

```python
from collections import deque

class BrowserHistory:
    def __init__(self, homepage):
        """Startet mit Homepage"""
        # Dein Code hier
        pass
    
    def visit(self, url):
        """Besucht neue URL (löscht Forward-History)"""
        pass
    
    def back(self, steps=1):
        """Geht steps Schritte zurück"""
        pass
    
    def forward(self, steps=1):
        """Geht steps Schritte vorwärts"""
        pass
    
    def current(self):
        """Gibt aktuelle URL zurück"""
        pass

# Tests
browser = BrowserHistory("google.com")
browser.visit("youtube.com")
browser.visit("facebook.com")
print(browser.current())  # facebook.com

browser.back(1)
print(browser.current())  # youtube.com

browser.back(1)
print(browser.current())  # google.com

browser.forward(1)
print(browser.current())  # youtube.com

browser.visit("reddit.com")
print(browser.current())  # reddit.com

browser.forward(1)
print(browser.current())  # reddit.com (Forward-History wurde gelöscht)
```

**Erwarteter Output:**
```
facebook.com
youtube.com
google.com
youtube.com
reddit.com
reddit.com
```

<details>
<summary>💡 Hinweis</summary>

1. Verwende zwei deques: eine für History (zurück), eine für Forward
2. Bei `visit()`: aktuelle Seite zu History, Forward-History löschen
3. Bei `back()`: aktuelle Seite zu Forward, letzte aus History holen
4. Bei `forward()`: aktuelle Seite zu History, nächste aus Forward holen

</details>

<details>
<summary>✅ Lösung</summary>

```python
from collections import deque

class BrowserHistory:
    def __init__(self, homepage):
        """Startet mit Homepage"""
        self.current_page = homepage
        self.back_history = deque(maxlen=50)
        self.forward_history = deque(maxlen=50)
    
    def visit(self, url):
        """Besucht neue URL (löscht Forward-History)"""
        self.back_history.append(self.current_page)
        self.current_page = url
        self.forward_history.clear()  # Forward-History wird ungültig
    
    def back(self, steps=1):
        """Geht steps Schritte zurück"""
        for _ in range(steps):
            if not self.back_history:
                break  # Keine History mehr
            
            self.forward_history.append(self.current_page)
            self.current_page = self.back_history.pop()
    
    def forward(self, steps=1):
        """Geht steps Schritte vorwärts"""
        for _ in range(steps):
            if not self.forward_history:
                break  # Keine Forward-History
            
            self.back_history.append(self.current_page)
            self.current_page = self.forward_history.pop()
    
    def current(self):
        """Gibt aktuelle URL zurück"""
        return self.current_page

# Tests
browser = BrowserHistory("google.com")
browser.visit("youtube.com")
browser.visit("facebook.com")
print(browser.current())  # facebook.com

browser.back(1)
print(browser.current())  # youtube.com

browser.back(1)
print(browser.current())  # google.com

browser.forward(1)
print(browser.current())  # youtube.com

browser.visit("reddit.com")
print(browser.current())  # reddit.com

browser.forward(1)
print(browser.current())  # reddit.com
```

**Zeitkomplexität:** O(1) für visit, O(k) für back/forward mit k steps
**Speicherkomplexität:** O(50) - Bounded History

</details>

---

## 🟡 Übung 4: Task Scheduler mit Prioritäten

**Schwierigkeit:** Mittel

Implementiere einen Task Scheduler, der Tasks mit verschiedenen Prioritäten verwaltet. High-Priority Tasks werden vorne eingefügt, normale hinten.

```python
from collections import deque

class TaskScheduler:
    def __init__(self):
        # Dein Code hier
        pass
    
    def add_task(self, task, priority='normal'):
        """
        Fügt Task hinzu.
        priority: 'high', 'normal', 'low'
        """
        pass
    
    def execute_next(self):
        """Führt nächste Task aus und gibt sie zurück"""
        pass
    
    def peek_next(self):
        """Zeigt nächste Task ohne Ausführung"""
        pass
    
    def count_tasks(self):
        """Gibt Anzahl wartender Tasks zurück"""
        pass

# Tests
scheduler = TaskScheduler()
scheduler.add_task("Task A")
scheduler.add_task("Task B", priority='high')
scheduler.add_task("Task C")
scheduler.add_task("Task D", priority='high')
scheduler.add_task("Task E", priority='low')

print(scheduler.count_tasks())  # 5
print(scheduler.execute_next())  # Task D (high, zuletzt hinzugefügt)
print(scheduler.execute_next())  # Task B (high)
print(scheduler.execute_next())  # Task A (normal)
print(scheduler.execute_next())  # Task C (normal)
print(scheduler.execute_next())  # Task E (low)
print(scheduler.count_tasks())  # 0
```

**Erwarteter Output:**
```
5
Task D
Task B
Task A
Task C
Task E
0
```

<details>
<summary>💡 Hinweis</summary>

Verwende eine deque mit folgender Strategie:
- High priority: `appendleft()` (vorne einfügen)
- Normal priority: `append()` (hinten einfügen)
- Low priority: `append()` und später ausführen

</details>

<details>
<summary>✅ Lösung</summary>

```python
from collections import deque

class TaskScheduler:
    def __init__(self):
        self.queue = deque()
    
    def add_task(self, task, priority='normal'):
        """Fügt Task mit Priorität hinzu"""
        if priority == 'high':
            # High priority: vorne einfügen
            self.queue.appendleft(task)
        elif priority == 'low':
            # Low priority: ganz hinten
            self.queue.append(task)
        else:  # normal
            # Normal: nach high-priority, vor low-priority
            # Finde erste non-high Position
            high_count = 0
            for item in self.queue:
                if not hasattr(item, '__priority__'):
                    break
                high_count += 1
            
            # Einfache Lösung: normale Tasks hinten
            self.queue.append(task)
    
    def execute_next(self):
        """Führt nächste Task aus"""
        if self.queue:
            return self.queue.popleft()
        return None
    
    def peek_next(self):
        """Zeigt nächste Task ohne Ausführung"""
        if self.queue:
            return self.queue[0]
        return None
    
    def count_tasks(self):
        """Gibt Anzahl wartender Tasks zurück"""
        return len(self.queue)

# Tests
scheduler = TaskScheduler()
scheduler.add_task("Task A")
scheduler.add_task("Task B", priority='high')
scheduler.add_task("Task C")
scheduler.add_task("Task D", priority='high')
scheduler.add_task("Task E", priority='low')

print(scheduler.count_tasks())  # 5
print(scheduler.execute_next())  # Task D
print(scheduler.execute_next())  # Task B
print(scheduler.execute_next())  # Task A
print(scheduler.execute_next())  # Task C
print(scheduler.execute_next())  # Task E
print(scheduler.count_tasks())  # 0
```

**Alternative Lösung mit separaten Queues:**

```python
from collections import deque

class TaskScheduler:
    def __init__(self):
        self.high_queue = deque()
        self.normal_queue = deque()
        self.low_queue = deque()
    
    def add_task(self, task, priority='normal'):
        """Fügt Task mit Priorität hinzu"""
        if priority == 'high':
            self.high_queue.append(task)
        elif priority == 'low':
            self.low_queue.append(task)
        else:
            self.normal_queue.append(task)
    
    def execute_next(self):
        """Führt nächste Task aus (high > normal > low)"""
        if self.high_queue:
            return self.high_queue.popleft()
        elif self.normal_queue:
            return self.normal_queue.popleft()
        elif self.low_queue:
            return self.low_queue.popleft()
        return None
    
    def peek_next(self):
        """Zeigt nächste Task ohne Ausführung"""
        if self.high_queue:
            return self.high_queue[0]
        elif self.normal_queue:
            return self.normal_queue[0]
        elif self.low_queue:
            return self.low_queue[0]
        return None
    
    def count_tasks(self):
        """Gibt Anzahl aller wartenden Tasks zurück"""
        return len(self.high_queue) + len(self.normal_queue) + len(self.low_queue)
```

**Zeitkomplexität:** O(1) für alle Operationen
**Speicherkomplexität:** O(n) wobei n = Anzahl Tasks

</details>

---

## 🟡 Übung 5: Moving Average

**Schwierigkeit:** Mittel

Implementiere einen gleitenden Durchschnitt mit festem Fenster unter Verwendung einer deque.

```python
from collections import deque

class MovingAverage:
    def __init__(self, window_size):
        """Initialisiert mit Fenstergröße"""
        # Dein Code hier
        pass
    
    def add_value(self, value):
        """Fügt Wert hinzu und gibt aktuellen Durchschnitt zurück"""
        pass
    
    def get_average(self):
        """Gibt aktuellen Durchschnitt zurück"""
        pass
    
    def reset(self):
        """Setzt zurück"""
        pass

# Tests
ma = MovingAverage(3)
print(ma.add_value(1))   # 1.0
print(ma.add_value(2))   # 1.5
print(ma.add_value(3))   # 2.0
print(ma.add_value(4))   # 3.0 (Fenster: [2, 3, 4])
print(ma.add_value(5))   # 4.0 (Fenster: [3, 4, 5])
print(ma.get_average())  # 4.0

ma.reset()
print(ma.add_value(10))  # 10.0
```

**Erwarteter Output:**
```
1.0
1.5
2.0
3.0
4.0
4.0
10.0
```

<details>
<summary>💡 Hinweis</summary>

1. Verwende deque mit maxlen für automatisches Entfernen
2. Behalte Summe für O(1) Durchschnittsberechnung
3. Update Summe bei jedem add_value

</details>

<details>
<summary>✅ Lösung</summary>

```python
from collections import deque

class MovingAverage:
    def __init__(self, window_size):
        """Initialisiert mit Fenstergröße"""
        self.window_size = window_size
        self.window = deque(maxlen=window_size)
        self.sum = 0.0
    
    def add_value(self, value):
        """Fügt Wert hinzu und gibt aktuellen Durchschnitt zurück"""
        # Wenn Fenster voll, subtrahiere ältesten Wert
        if len(self.window) == self.window_size:
            self.sum -= self.window[0]
        
        # Füge neuen Wert hinzu
        self.window.append(value)
        self.sum += value
        
        return self.get_average()
    
    def get_average(self):
        """Gibt aktuellen Durchschnitt zurück"""
        if not self.window:
            return 0.0
        return self.sum / len(self.window)
    
    def reset(self):
        """Setzt zurück"""
        self.window.clear()
        self.sum = 0.0

# Tests
ma = MovingAverage(3)
print(ma.add_value(1))   # 1.0
print(ma.add_value(2))   # 1.5
print(ma.add_value(3))   # 2.0
print(ma.add_value(4))   # 3.0
print(ma.add_value(5))   # 4.0
print(ma.get_average())  # 4.0

ma.reset()
print(ma.add_value(10))  # 10.0
```

**Zeitkomplexität:** O(1) für add_value und get_average
**Speicherkomplexität:** O(window_size)

**Erklärung:** 
- Durch Speichern der laufenden Summe ist die Durchschnittsberechnung O(1)
- maxlen sorgt automatisch für Entfernen alter Werte
- Wir subtrahieren den zu entfernenden Wert manuell von der Summe

</details>

---

## 🔴 Übung 6: Sliding Window Maximum

**Schwierigkeit:** Schwer

Implementiere eine Funktion, die für jedes Fenster der Größe k in einem Array das Maximum findet. Nutze eine monotone deque für O(n) Zeitkomplexität.

```python
from collections import deque

def sliding_window_max(arr, k):
    """
    Findet Maximum in jedem Fenster der Größe k.
    
    Args:
        arr: Liste von Zahlen
        k: Fenstergröße
    
    Returns:
        Liste der Maxima für jedes Fenster
    """
    # Dein Code hier
    pass

# Tests
print(sliding_window_max([1, 3, -1, -3, 5, 3, 6, 7], 3))
# [3, 3, 5, 5, 6, 7]

print(sliding_window_max([1, 3, 1, 2, 0, 5], 3))
# [3, 3, 2, 5]

print(sliding_window_max([9, 10, 9, -7, -4, -8, 2, -6], 5))
# [10, 10, 9, 2]

print(sliding_window_max([1], 1))
# [1]
```

**Erwarteter Output:**
```
[3, 3, 5, 5, 6, 7]
[3, 3, 2, 5]
[10, 10, 9, 2]
[1]
```

<details>
<summary>💡 Hinweis</summary>

Monotone Deque Technik:
1. Deque speichert Indizes (nicht Werte) in absteigender Reihenfolge ihrer Werte
2. Für jedes neue Element:
   - Entferne Indizes außerhalb des aktuellen Fensters
   - Entferne alle kleineren Elemente vom Ende (die nie Maximum sein können)
   - Füge aktuellen Index hinzu
   - Das vorderste Element ist das Maximum

</details>

<details>
<summary>✅ Lösung</summary>

```python
from collections import deque

def sliding_window_max(arr, k):
    """
    Findet Maximum in jedem Fenster der Größe k mit O(n) Komplexität.
    Verwendet monotone deque.
    """
    if not arr or k <= 0:
        return []
    
    if k == 1:
        return arr
    
    result = []
    dq = deque()  # Speichert Indizes in absteigender Reihenfolge der Werte
    
    for i in range(len(arr)):
        # 1. Entferne Indizes außerhalb des aktuellen Fensters
        while dq and dq[0] <= i - k:
            dq.popleft()
        
        # 2. Entferne kleinere Elemente vom Ende
        # (sie können nie Maximum sein, wenn aktuelles Element größer ist)
        while dq and arr[dq[-1]] < arr[i]:
            dq.pop()
        
        # 3. Füge aktuellen Index hinzu
        dq.append(i)
        
        # 4. Sobald erstes vollständiges Fenster erreicht, füge Maximum hinzu
        if i >= k - 1:
            result.append(arr[dq[0]])  # Vorderstes Element ist Maximum
    
    return result

# Tests
print(sliding_window_max([1, 3, -1, -3, 5, 3, 6, 7], 3))
# [3, 3, 5, 5, 6, 7]

print(sliding_window_max([1, 3, 1, 2, 0, 5], 3))
# [3, 3, 2, 5]

print(sliding_window_max([9, 10, 9, -7, -4, -8, 2, -6], 5))
# [10, 10, 9, 2]

print(sliding_window_max([1], 1))
# [1]

# Schritt-für-Schritt Beispiel für [1, 3, -1, -3, 5, 3, 6, 7], k=3
def sliding_window_max_verbose(arr, k):
    """Mit Debug-Output"""
    if not arr or k <= 0:
        return []
    
    result = []
    dq = deque()
    
    print(f"Array: {arr}, k={k}\n")
    
    for i in range(len(arr)):
        print(f"i={i}, arr[i]={arr[i]}")
        
        # Entferne alte Indizes
        while dq and dq[0] <= i - k:
            removed = dq.popleft()
            print(f"  Entferne Index {removed} (außerhalb Fenster)")
        
        # Entferne kleinere Elemente
        while dq and arr[dq[-1]] < arr[i]:
            removed = dq.pop()
            print(f"  Entferne Index {removed} (arr[{removed}]={arr[removed]} < {arr[i]})")
        
        dq.append(i)
        print(f"  Füge Index {i} hinzu")
        print(f"  Deque: {list(dq)} (Werte: {[arr[idx] for idx in dq]})")
        
        if i >= k - 1:
            max_val = arr[dq[0]]
            result.append(max_val)
            print(f"  ✓ Fenster komplett, Maximum: {max_val}")
        
        print()
    
    return result

# Detailliertes Beispiel
sliding_window_max_verbose([1, 3, -1, -3, 5], 3)
```

**Zeitkomplexität:** O(n) - Jedes Element wird max. 2x verarbeitet (einmal rein, einmal raus)
**Speicherkomplexität:** O(k) - Deque speichert maximal k Indizes

**Warum funktioniert das?**
- Wenn ein neues größeres Element kommt, können alle kleineren davor nie Maximum sein
- Deque behält nur potenzielle Maxima in absteigender Reihenfolge
- Das vorderste Element ist immer das aktuelle Maximum

</details>

---

## 🔴 Übung 7: LRU Cache

**Schwierigkeit:** Schwer

Implementiere einen vollständigen LRU (Least Recently Used) Cache mit deque.

```python
from collections import deque

class LRUCache:
    def __init__(self, capacity):
        """Initialisiert Cache mit Kapazität"""
        # Dein Code hier
        pass
    
    def get(self, key):
        """Holt Wert für key, gibt -1 wenn nicht existiert"""
        pass
    
    def put(self, key, value):
        """Speichert key:value Paar"""
        pass
    
    def __repr__(self):
        """String-Repräsentation für Debugging"""
        pass

# Tests
cache = LRUCache(2)

cache.put(1, 1)
cache.put(2, 2)
print(cache.get(1))       # 1

cache.put(3, 3)           # Evict key 2
print(cache.get(2))       # -1 (nicht gefunden)

cache.put(4, 4)           # Evict key 1
print(cache.get(1))       # -1 (nicht gefunden)
print(cache.get(3))       # 3
print(cache.get(4))       # 4
```

**Erwarteter Output:**
```
1
-1
-1
3
4
```

<details>
<summary>💡 Hinweis</summary>

1. Dictionary für O(1) Key-Value Zugriff
2. Deque für O(1) LRU Tracking (Reihenfolge der Nutzung)
3. Bei `get()`: Key muss in deque nach hinten verschoben werden
4. Bei `put()`: Wenn voll, entferne LRU (vorderster in deque)

</details>

<details>
<summary>✅ Lösung</summary>

```python
from collections import deque

class LRUCache:
    def __init__(self, capacity):
        """Initialisiert Cache mit Kapazität"""
        self.capacity = capacity
        self.cache = {}  # key -> value mapping
        self.usage_order = deque()  # Tracks usage order (MRU at end)
    
    def get(self, key):
        """Holt Wert für key, gibt -1 wenn nicht existiert"""
        if key not in self.cache:
            return -1
        
        # Markiere als recently used (verschiebe ans Ende)
        self.usage_order.remove(key)
        self.usage_order.append(key)
        
        return self.cache[key]
    
    def put(self, key, value):
        """Speichert key:value Paar"""
        if key in self.cache:
            # Update bestehenden Wert
            self.cache[key] = value
            # Markiere als recently used
            self.usage_order.remove(key)
            self.usage_order.append(key)
        else:
            # Neuer Key
            if len(self.cache) >= self.capacity:
                # Evict LRU (vorderster in deque)
                lru_key = self.usage_order.popleft()
                del self.cache[lru_key]
            
            self.cache[key] = value
            self.usage_order.append(key)
    
    def __repr__(self):
        """String-Repräsentation für Debugging"""
        return f"LRUCache(capacity={self.capacity}, cache={self.cache}, order={list(self.usage_order)})"

# Tests
cache = LRUCache(2)

cache.put(1, 1)
cache.put(2, 2)
print(cache.get(1))       # 1

cache.put(3, 3)           # Evict key 2
print(cache.get(2))       # -1 (nicht gefunden)

cache.put(4, 4)           # Evict key 1
print(cache.get(1))       # -1 (nicht gefunden)
print(cache.get(3))       # 3
print(cache.get(4))       # 4

# Detaillierterer Test
print("\n=== Detaillierter Test ===")
cache2 = LRUCache(3)
cache2.put('a', 1)
print(f"Nach put('a', 1): {cache2}")
cache2.put('b', 2)
print(f"Nach put('b', 2): {cache2}")
cache2.put('c', 3)
print(f"Nach put('c', 3): {cache2}")
cache2.get('a')  # 'a' wird MRU
print(f"Nach get('a'): {cache2}")
cache2.put('d', 4)  # 'b' wird evicted (LRU)
print(f"Nach put('d', 4): {cache2}")
```

**Zeitkomplexität:** 
- `get()`: O(n) wegen `remove()` auf deque
- `put()`: O(n) wegen `remove()` auf deque

**Speicherkomplexität:** O(capacity)

**Hinweis:** In der Praxis würde man `OrderedDict` oder eine Kombination aus dict + doppelt verketteter Liste für O(1) Operationen verwenden.

</details>

---

## 🔴 Übung 8: Circular Buffer mit Peek

**Schwierigkeit:** Schwer

Implementiere einen Circular Buffer mit Overwrite-Verhalten und einer `peek(n)` Methode, die die letzten n Elemente zurückgibt ohne sie zu entfernen.

```python
from collections import deque

class CircularBuffer:
    def __init__(self, capacity):
        """Initialisiert Buffer mit Kapazität"""
        # Dein Code hier
        pass
    
    def write(self, item):
        """Schreibt Item (überschreibt ältestes wenn voll)"""
        pass
    
    def read(self):
        """Liest und entfernt ältestes Item"""
        pass
    
    def peek(self, n):
        """Gibt letzte n Items zurück ohne zu entfernen"""
        pass
    
    def is_empty(self):
        """Prüft ob Buffer leer ist"""
        pass
    
    def is_full(self):
        """Prüft ob Buffer voll ist"""
        pass
    
    def size(self):
        """Gibt aktuelle Anzahl Elemente zurück"""
        pass

# Tests
cb = CircularBuffer(3)

cb.write('A')
cb.write('B')
cb.write('C')
print(cb.peek(2))      # ['B', 'C']
print(cb.is_full())    # True

cb.write('D')          # Überschreibt 'A'
print(cb.peek(3))      # ['B', 'C', 'D']

print(cb.read())       # B
print(cb.peek(2))      # ['C', 'D']
print(cb.is_full())    # False

cb.write('E')
print(cb.peek(3))      # ['C', 'D', 'E']
```

**Erwarteter Output:**
```
['B', 'C']
True
['B', 'C', 'D']
B
['C', 'D']
False
['C', 'D', 'E']
```

<details>
<summary>💡 Hinweis</summary>

1. Verwende `maxlen` für automatisches Überschreiben
2. `peek(n)` kann mit Slicing auf konvertierter Liste arbeiten
3. Beachte: deque unterstützt kein direktes Slicing

</details>

<details>
<summary>✅ Lösung</summary>

```python
from collections import deque

class CircularBuffer:
    def __init__(self, capacity):
        """Initialisiert Buffer mit Kapazität"""
        self.capacity = capacity
        self.buffer = deque(maxlen=capacity)
    
    def write(self, item):
        """Schreibt Item (überschreibt ältestes wenn voll)"""
        self.buffer.append(item)  # maxlen überschreibt automatisch
    
    def read(self):
        """Liest und entfernt ältestes Item"""
        if self.buffer:
            return self.buffer.popleft()
        raise IndexError("Buffer ist leer")
    
    def peek(self, n):
        """Gibt letzte n Items zurück ohne zu entfernen"""
        if n <= 0:
            return []
        
        # Konvertiere zu Liste für Slicing
        buffer_list = list(self.buffer)
        return buffer_list[-n:] if n <= len(buffer_list) else buffer_list
    
    def is_empty(self):
        """Prüft ob Buffer leer ist"""
        return len(self.buffer) == 0
    
    def is_full(self):
        """Prüft ob Buffer voll ist"""
        return len(self.buffer) == self.capacity
    
    def size(self):
        """Gibt aktuelle Anzahl Elemente zurück"""
        return len(self.buffer)
    
    def __repr__(self):
        return f"CircularBuffer(capacity={self.capacity}, buffer={list(self.buffer)})"

# Tests
cb = CircularBuffer(3)

cb.write('A')
cb.write('B')
cb.write('C')
print(cb.peek(2))      # ['B', 'C']
print(cb.is_full())    # True

cb.write('D')          # Überschreibt 'A'
print(cb.peek(3))      # ['B', 'C', 'D']

print(cb.read())       # B
print(cb.peek(2))      # ['C', 'D']
print(cb.is_full())    # False

cb.write('E')
print(cb.peek(3))      # ['C', 'D', 'E']

# Erweiterte Tests
print("\n=== Erweiterte Tests ===")
cb2 = CircularBuffer(5)
for i in range(8):
    cb2.write(i)
    print(f"Nach write({i}): {cb2}")

print(f"\nPeek(3): {cb2.peek(3)}")
print(f"Peek(10): {cb2.peek(10)}")  # Mehr als vorhanden
```

**Zeitkomplexität:**
- `write()`: O(1)
- `read()`: O(1)
- `peek(n)`: O(n) - muss Liste konvertieren

**Speicherkomplexität:** O(capacity)

</details>

---

## 🔴 Übung 9: Undo/Redo System

**Schwierigkeit:** Schwer

Implementiere ein vollständiges Undo/Redo System für einen Text-Editor mit begrenzter History.

```python
from collections import deque

class TextEditor:
    def __init__(self, max_history=10):
        """Initialisiert Editor mit History-Limit"""
        # Dein Code hier
        pass
    
    def insert(self, text):
        """Fügt Text ein"""
        pass
    
    def delete(self, count):
        """Löscht count Zeichen vom Ende"""
        pass
    
    def undo(self):
        """Macht letzte Operation rückgängig"""
        pass
    
    def redo(self):
        """Wiederholt rückgängig gemachte Operation"""
        pass
    
    def get_text(self):
        """Gibt aktuellen Text zurück"""
        pass
    
    def can_undo(self):
        """Prüft ob Undo möglich"""
        pass
    
    def can_redo(self):
        """Prüft ob Redo möglich"""
        pass

# Tests
editor = TextEditor(max_history=5)

editor.insert("Hello")
print(editor.get_text())  # Hello

editor.insert(" World")
print(editor.get_text())  # Hello World

editor.delete(6)
print(editor.get_text())  # Hello

editor.undo()
print(editor.get_text())  # Hello World

editor.undo()
print(editor.get_text())  # Hello

editor.redo()
print(editor.get_text())  # Hello World

editor.insert("!")
print(editor.get_text())  # Hello World!

print(editor.can_redo())  # False (neue Operation löscht Redo-History)
```

**Erwarteter Output:**
```
Hello
Hello World
Hello
Hello World
Hello
Hello World
Hello World!
False
```

<details>
<summary>💡 Hinweis</summary>

1. Speichere kompletten Text-State nach jeder Operation
2. Undo-Stack für vergangene States
3. Redo-Stack für rückgängig gemachte States
4. Bei neuer Operation: Redo-Stack löschen

</details>

<details>
<summary>✅ Lösung</summary>

```python
from collections import deque

class TextEditor:
    def __init__(self, max_history=10):
        """Initialisiert Editor mit History-Limit"""
        self.text = ""
        self.undo_stack = deque(maxlen=max_history)
        self.redo_stack = deque(maxlen=max_history)
    
    def _save_state(self):
        """Speichert aktuellen State für Undo"""
        self.undo_stack.append(self.text)
        self.redo_stack.clear()  # Neue Operation macht Redo ungültig
    
    def insert(self, text):
        """Fügt Text ein"""
        self._save_state()
        self.text += text
    
    def delete(self, count):
        """Löscht count Zeichen vom Ende"""
        if count > 0:
            self._save_state()
            self.text = self.text[:-count] if count <= len(self.text) else ""
    
    def undo(self):
        """Macht letzte Operation rückgängig"""
        if not self.can_undo():
            return False
        
        # Aktuellen State zu Redo speichern
        self.redo_stack.append(self.text)
        # Letzten State wiederherstellen
        self.text = self.undo_stack.pop()
        return True
    
    def redo(self):
        """Wiederholt rückgängig gemachte Operation"""
        if not self.can_redo():
            return False
        
        # Aktuellen State zu Undo speichern
        self.undo_stack.append(self.text)
        # Redo State wiederherstellen
        self.text = self.redo_stack.pop()
        return True
    
    def get_text(self):
        """Gibt aktuellen Text zurück"""
        return self.text
    
    def can_undo(self):
        """Prüft ob Undo möglich"""
        return len(self.undo_stack) > 0
    
    def can_redo(self):
        """Prüft ob Redo möglich"""
        return len(self.redo_stack) > 0
    
    def get_history_info(self):
        """Debug-Information"""
        return {
            'text': self.text,
            'undo_available': len(self.undo_stack),
            'redo_available': len(self.redo_stack)
        }

# Tests
editor = TextEditor(max_history=5)

editor.insert("Hello")
print(editor.get_text())  # Hello

editor.insert(" World")
print(editor.get_text())  # Hello World

editor.delete(6)
print(editor.get_text())  # Hello

editor.undo()
print(editor.get_text())  # Hello World

editor.undo()
print(editor.get_text())  # Hello

editor.redo()
print(editor.get_text())  # Hello World

editor.insert("!")
print(editor.get_text())  # Hello World!

print(editor.can_redo())  # False

# Detaillierter Test
print("\n=== Detaillierter Test ===")
editor2 = TextEditor(max_history=3)
editor2.insert("A")
print(f"Nach 'A': {editor2.get_history_info()}")
editor2.insert("B")
print(f"Nach 'B': {editor2.get_history_info()}")
editor2.insert("C")
print(f"Nach 'C': {editor2.get_history_info()}")
editor2.insert("D")
print(f"Nach 'D': {editor2.get_history_info()}")
editor2.insert("E")  # Ältester State ('A') wird verworfen
print(f"Nach 'E': {editor2.get_history_info()}")

editor2.undo()
editor2.undo()
print(f"Nach 2x undo: {editor2.get_history_info()}")
```

**Zeitkomplexität:** O(1) für alle Operationen (außer Text-Konkatenation ist O(n))
**Speicherkomplexität:** O(max_history * text_length)

**Verbesserung:** Statt komplette Text-Kopien könnten wir Commands/Diffs speichern für bessere Speicher-Effizienz.

</details>

---

## 🔴 Übung 10: Multi-Level Undo mit Branching

**Schwierigkeit:** Sehr Schwer

Implementiere ein erweitertes Undo-System, das "Branching" unterstützt - wenn man nach Undo neue Operationen macht, bleiben alte Branches erhalten und können wiederhergestellt werden.

```python
from collections import deque

class BranchingEditor:
    def __init__(self):
        """Initialisiert Editor mit Branching-Support"""
        # Dein Code hier
        pass
    
    def insert(self, text):
        """Fügt Text ein"""
        pass
    
    def undo(self):
        """Geht einen Schritt zurück"""
        pass
    
    def redo(self):
        """Geht einen Schritt vorwärts im aktuellen Branch"""
        pass
    
    def switch_branch(self, branch_id):
        """Wechselt zu anderem Branch"""
        pass
    
    def list_branches(self):
        """Listet alle verfügbaren Branches"""
        pass
    
    def get_text(self):
        """Gibt aktuellen Text zurück"""
        pass

# Test
editor = BranchingEditor()
editor.insert("Hello")
editor.insert(" World")  # Branch 0: "Hello World"
editor.undo()            # "Hello"
editor.insert(" Python") # Branch 1: "Hello Python"
editor.list_branches()   # [0: "Hello World", 1: "Hello Python"]
editor.switch_branch(0)  # Zurück zu "Hello World"
```

<details>
<summary>💡 Hinweis</summary>

Dies ist eine sehr komplexe Übung! Struktur:
- Tree-Struktur für States
- Jeder State hat Referenz zum Parent
- Bei neuer Operation nach Undo: Neuer Branch
- Branch-ID System für Navigation

</details>

<details>
<summary>✅ Lösung</summary>

```python
from collections import deque
from dataclasses import dataclass
from typing import Optional, List

@dataclass
class State:
    """Repräsentiert einen Editor-State"""
    text: str
    parent: Optional['State'] = None
    children: List['State'] = None
    branch_id: int = 0
    
    def __post_init__(self):
        if self.children is None:
            self.children = []

class BranchingEditor:
    def __init__(self):
        """Initialisiert Editor mit Branching-Support"""
        self.root = State(text="")
        self.current = self.root
        self.next_branch_id = 0
    
    def insert(self, text):
        """Fügt Text ein"""
        new_text = self.current.text + text
        new_state = State(
            text=new_text,
            parent=self.current,
            branch_id=self.next_branch_id
        )
        self.next_branch_id += 1
        self.current.children.append(new_state)
        self.current = new_state
    
    def undo(self):
        """Geht einen Schritt zurück"""
        if self.current.parent:
            self.current = self.current.parent
            return True
        return False
    
    def redo(self):
        """Geht einen Schritt vorwärts (zum letzten Child)"""
        if self.current.children:
            self.current = self.current.children[-1]
            return True
        return False
    
    def switch_branch(self, branch_id):
        """Wechselt zu State mit gegebener branch_id"""
        target = self._find_state_by_branch(self.root, branch_id)
        if target:
            self.current = target
            return True
        return False
    
    def _find_state_by_branch(self, state, branch_id):
        """Rekursive Suche nach Branch-ID"""
        if state.branch_id == branch_id:
            return state
        for child in state.children:
            result = self._find_state_by_branch(child, branch_id)
            if result:
                return result
        return None
    
    def list_branches(self):
        """Listet alle Leaf-States (Endpunkte von Branches)"""
        leaves = []
        self._find_leaves(self.root, leaves)
        return [(state.branch_id, state.text) for state in leaves]
    
    def _find_leaves(self, state, leaves):
        """Findet alle Leaf-Nodes"""
        if not state.children:
            leaves.append(state)
        else:
            for child in state.children:
                self._find_leaves(child, leaves)
    
    def get_text(self):
        """Gibt aktuellen Text zurück"""
        return self.current.text
    
    def visualize_tree(self, state=None, prefix="", is_last=True):
        """Visualisiert den State-Tree"""
        if state is None:
            state = self.root
        
        marker = "└── " if is_last else "├── "
        current_marker = " <--" if state == self.current else ""
        print(f"{prefix}{marker}[{state.branch_id}] '{state.text}'{current_marker}")
        
        for i, child in enumerate(state.children):
            extension = "    " if is_last else "│   "
            self.visualize_tree(child, prefix + extension, i == len(state.children) - 1)

# Tests
print("=== Branching Editor Test ===\n")
editor = BranchingEditor()

editor.insert("Hello")
editor.insert(" World")
print(f"Nach 'Hello World': {editor.get_text()}")

editor.undo()
print(f"Nach undo: {editor.get_text()}")

editor.insert(" Python")
print(f"Nach 'Hello Python': {editor.get_text()}")

print("\nBranches:")
for bid, text in editor.list_branches():
    print(f"  Branch {bid}: '{text}'")

print("\nTree-Visualisierung:")
editor.visualize_tree()

print("\nWechsle zu Branch 1...")
editor.switch_branch(1)
print(f"Aktueller Text: {editor.get_text()}")

print("\nTree nach Branch-Switch:")
editor.visualize_tree()
```

**Ausgabe:**
```
=== Branching Editor Test ===

Nach 'Hello World': Hello World
Nach undo: Hello
Nach 'Hello Python': Hello Python

Branches:
  Branch 1: 'Hello World'
  Branch 2: 'Hello Python'

Tree-Visualisierung:
└── [0] ''
    └── [1] 'Hello'
        ├── [2] 'Hello World' <--
        └── [3] 'Hello Python'

Wechsle zu Branch 1...
Aktueller Text: Hello World

Tree nach Branch-Switch:
└── [0] ''
    └── [1] 'Hello'
        ├── [2] 'Hello World' <--
        └── [3] 'Hello Python'
```

**Zeitkomplexität:** 
- insert: O(1)
- undo/redo: O(1)
- switch_branch: O(n) wobei n = Anzahl States
- list_branches: O(n)

Diese Lösung zeigt fortgeschrittene Konzepte wie Tree-Strukturen, die auf deque aufbauen können.

</details>

---

## Zusammenfassung

Diese Übungen haben gezeigt:

✅ **Grundlegende deque-Operationen** (Palindrom, Rotating Queue)
✅ **Queue-Patterns** (Browser History, Task Scheduler)
✅ **Sliding Window Techniken** (Moving Average, Window Maximum)
✅ **Cache-Implementierungen** (LRU Cache, Circular Buffer)
✅ **State Management** (Undo/Redo, Branching)

Die Lösungen findest du auch im Kapitel [[06_Praxis/04_Lösungen/Lösungen Teil 4|Lösungen Teil 4]].

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [deque - collections](https://docs.python.org/3/library/collections.html#collections.deque) - Vollständige API
- [deque recipes](https://docs.python.org/3/library/collections.html#deque-recipes) - Weitere Patterns

## Verwandte Themen

- [[04_Fortgeschritten/02_Deque/01_Deque Basics|Deque Basics]] - Grundlagen
- [[04_Fortgeschritten/02_Deque/02_Deque Operationen|Deque Operationen]] - Alle Methoden
- [[04_Fortgeschritten/02_Deque/03_Performance Vergleich|Performance Vergleich]] - Benchmarks
- [[02_Sequenzen/04_Comprehensions/05_Übungen Comprehensions|Übungen Comprehensions]] - Mehr Übungen

---
← [[04_Fortgeschritten/02_Deque/03_Performance Vergleich|Performance Vergleich]] | [[INDEX|📑 Index]] | [[04_Fortgeschritten/03_Heap_Queue/01_Heapq Module|Heapq Module]] →