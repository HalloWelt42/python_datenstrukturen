---
tags: [python, datenstrukturen, heapq, übungen, priority-queue]
created: 2025-10-02
---

← [[04_Fortgeschritten/03_Heap_Queue/02_Priority Queues|Priority Queues]] | [[INDEX|📑 Index]] | [[04_Fortgeschritten/04_Dataclasses/01_Dataclass Basics|Dataclass Basics]] →

# Übungen Heapq

> [!tip] Übungsziel
> Diese Übungen helfen dir, heapq und Priority Queues in verschiedenen Szenarien anzuwenden, von einfachen Sortieraufgaben bis zu komplexen Algorithmen.

## Schwierigkeitsgrade

- 🟢 **Leicht**: Grundlegende heapq-Operationen
- 🟡 **Mittel**: Priority Queues und Algorithmen
- 🔴 **Schwer**: Komplexe Anwendungen und Optimierungen

---

## 🟢 Übung 1: Top K Häufige Elemente

**Schwierigkeit:** Leicht

Finde die k häufigsten Elemente in einer Liste mit `heapq`.

```python
import heapq
from collections import Counter

def top_k_frequent(nums, k):
    """
    Findet k häufigste Elemente.
    
    Args:
        nums: Liste von Zahlen
        k: Anzahl häufigster Elemente
    
    Returns:
        Liste der k häufigsten Elemente
    """
    # Dein Code hier
    pass

# Tests
print(top_k_frequent([1, 1, 1, 2, 2, 3], k=2))  # [1, 2]
print(top_k_frequent([1], k=1))  # [1]
print(top_k_frequent([4, 1, -1, 2, -1, 2, 3], k=2))  # [-1, 2] oder [2, -1]
```

**Erwarteter Output:**
```
[1, 2]
[1]
[-1, 2]
```

<details>
<summary>💡 Hinweis</summary>

1. Verwende `Counter` um Häufigkeiten zu zählen
2. `heapq.nlargest()` kann direkt mit key-Funktion arbeiten
3. Alternativ: Erstelle Heap von (count, element) Tupeln

</details>

<details>
<summary>✅ Lösung</summary>

```python
import heapq
from collections import Counter

def top_k_frequent(nums, k):
    """Findet k häufigste Elemente"""
    # Zähle Häufigkeiten
    count = Counter(nums)
    
    # Verwende nlargest mit key-Funktion
    # Sortiert nach Häufigkeit
    return heapq.nlargest(k, count.keys(), key=count.get)

# Alternative Lösung mit explizitem Heap
def top_k_frequent_v2(nums, k):
    """Alternative mit Heap-Manipulation"""
    count = Counter(nums)
    
    # Erstelle Min-Heap der Größe k
    # Speichere (häufigkeit, element)
    heap = []
    
    for num, freq in count.items():
        heapq.heappush(heap, (freq, num))
        if len(heap) > k:
            heapq.heappop(heap)  # Entferne am wenigsten häufiges
    
    # Extrahiere nur Elemente (nicht Häufigkeiten)
    return [num for freq, num in heap]

# Tests
print(top_k_frequent([1, 1, 1, 2, 2, 3], k=2))  # [1, 2]
print(top_k_frequent([1], k=1))  # [1]
print(top_k_frequent([4, 1, -1, 2, -1, 2, 3], k=2))  # [-1, 2]

print("\nAlternative Lösung:")
print(top_k_frequent_v2([1, 1, 1, 2, 2, 3], k=2))
```

**Zeitkomplexität:** O(n log k) für v2, O(n) für nlargest wenn k klein
**Speicherkomplexität:** O(n) für Counter, O(k) für Heap

</details>

---

## 🟢 Übung 2: Kth Largest Element

**Schwierigkeit:** Leicht

Finde das k-t größte Element in einem Array.

```python
import heapq

def find_kth_largest(nums, k):
    """
    Findet k-t größtes Element.
    
    Args:
        nums: Liste von Zahlen
        k: Position (1 = größtes, 2 = zweitgrößtes, ...)
    
    Returns:
        k-t größtes Element
    """
    # Dein Code hier
    pass

# Tests
print(find_kth_largest([3, 2, 1, 5, 6, 4], k=2))  # 5
print(find_kth_largest([3, 2, 3, 1, 2, 4, 5, 5, 6], k=4))  # 4
print(find_kth_largest([1], k=1))  # 1
```

**Erwarteter Output:**
```
5
4
1
```

<details>
<summary>💡 Hinweis</summary>

1. Methode 1: `nlargest()` - einfach aber O(n log k)
2. Methode 2: Min-Heap der Größe k maintainen
3. Methode 3: `heapify()` und k-mal `heappop()` für negative Werte

</details>

<details>
<summary>✅ Lösung</summary>

```python
import heapq

# Lösung 1: Mit nlargest (einfachste)
def find_kth_largest(nums, k):
    """Einfachste Lösung mit nlargest"""
    return heapq.nlargest(k, nums)[-1]

# Lösung 2: Mit Min-Heap der Größe k (effizienter für großes n, kleines k)
def find_kth_largest_v2(nums, k):
    """Effizienter mit Min-Heap"""
    heap = []
    
    for num in nums:
        heapq.heappush(heap, num)
        if len(heap) > k:
            heapq.heappop(heap)  # Entferne kleinstes
    
    return heap[0]  # Kleinstes im Heap = k-t größtes im Array

# Lösung 3: Mit Max-Heap (negierte Werte)
def find_kth_largest_v3(nums, k):
    """Mit Max-Heap Simulation"""
    # Negiere alle Werte für Max-Heap
    max_heap = [-num for num in nums]
    heapq.heapify(max_heap)
    
    # Entferne k-1 größte Elemente
    for _ in range(k - 1):
        heapq.heappop(max_heap)
    
    # Das nächste ist das k-te größte
    return -max_heap[0]

# Tests
print(find_kth_largest([3, 2, 1, 5, 6, 4], k=2))  # 5
print(find_kth_largest([3, 2, 3, 1, 2, 4, 5, 5, 6], k=4))  # 4
print(find_kth_largest([1], k=1))  # 1

print("\nLösung 2:")
print(find_kth_largest_v2([3, 2, 1, 5, 6, 4], k=2))

print("\nLösung 3:")
print(find_kth_largest_v3([3, 2, 1, 5, 6, 4], k=2))
```

**Zeitkomplexität:**
- Lösung 1: O(n log k)
- Lösung 2: O(n log k)
- Lösung 3: O(n + k log n)

**Beste Wahl:** Lösung 2 wenn k klein, Lösung 1 wenn k ≈ n/2

</details>

---

## 🟡 Übung 3: Merge K Sorted Lists

**Schwierigkeit:** Mittel

Merge k sortierte Listen effizient in eine sortierte Liste.

```python
import heapq

def merge_k_sorted_lists(lists):
    """
    Merged k sortierte Listen.
    
    Args:
        lists: Liste von sortierten Listen
    
    Returns:
        Eine sortierte Liste mit allen Elementen
    """
    # Dein Code hier
    pass

# Tests
lists1 = [[1, 4, 5], [1, 3, 4], [2, 6]]
print(merge_k_sorted_lists(lists1))  # [1, 1, 2, 3, 4, 4, 5, 6]

lists2 = [[]]
print(merge_k_sorted_lists(lists2))  # []

lists3 = [[1], [0]]
print(merge_k_sorted_lists(lists3))  # [0, 1]
```

**Erwarteter Output:**
```
[1, 1, 2, 3, 4, 4, 5, 6]
[]
[0, 1]
```

<details>
<summary>💡 Hinweis</summary>

1. Initialisiere Heap mit erstem Element jeder Liste
2. Heap-Element: (wert, listen_index, element_index)
3. Wenn Element gepoppt wird, füge nächstes aus derselben Liste hinzu
4. Vermeide Vergleich von Listen durch Index-Tupel

</details>

<details>
<summary>✅ Lösung</summary>

```python
import heapq

def merge_k_sorted_lists(lists):
    """Merged k sortierte Listen effizient"""
    if not lists:
        return []
    
    # Entferne leere Listen
    lists = [lst for lst in lists if lst]
    
    if not lists:
        return []
    
    result = []
    heap = []
    
    # Initialisiere Heap mit erstem Element jeder Liste
    # Format: (wert, listen_index, element_index)
    for i, lst in enumerate(lists):
        if lst:
            heapq.heappush(heap, (lst[0], i, 0))
    
    # Merge-Prozess
    while heap:
        val, list_idx, elem_idx = heapq.heappop(heap)
        result.append(val)
        
        # Füge nächstes Element aus derselben Liste hinzu
        if elem_idx + 1 < len(lists[list_idx]):
            next_val = lists[list_idx][elem_idx + 1]
            heapq.heappush(heap, (next_val, list_idx, elem_idx + 1))
    
    return result

# Alternative mit merge (für viele Listen)
from heapq import merge

def merge_k_sorted_lists_v2(lists):
    """Verwendet heapq.merge"""
    return list(heapq.merge(*lists))

# Tests
lists1 = [[1, 4, 5], [1, 3, 4], [2, 6]]
print(merge_k_sorted_lists(lists1))  # [1, 1, 2, 3, 4, 4, 5, 6]

lists2 = [[]]
print(merge_k_sorted_lists(lists2))  # []

lists3 = [[1], [0]]
print(merge_k_sorted_lists(lists3))  # [0, 1]

print("\nMit heapq.merge:")
print(merge_k_sorted_lists_v2(lists1))
```

**Zeitkomplexität:** O(N log k) wobei N = Gesamtzahl Elemente, k = Anzahl Listen
**Speicherkomplexität:** O(k) für Heap

</details>

---

## 🟡 Übung 4: Meeting Rooms II

**Schwierigkeit:** Mittel

Finde die minimale Anzahl an Konferenzräumen, die für gegebene Meeting-Intervalle benötigt werden.

```python
import heapq

def min_meeting_rooms(intervals):
    """
    Findet minimale Anzahl Räume.
    
    Args:
        intervals: Liste von [start, end] Intervallen
    
    Returns:
        Minimale Anzahl gleichzeitig benötigter Räume
    """
    # Dein Code hier
    pass

# Tests
print(min_meeting_rooms([[0, 30], [5, 10], [15, 20]]))  # 2
print(min_meeting_rooms([[7, 10], [2, 4]]))  # 1
print(min_meeting_rooms([[1, 5], [2, 3], [3, 6]]))  # 2
print(min_meeting_rooms([[1, 3], [3, 6]]))  # 1
```

**Erwarteter Output:**
```
2
1
2
1
```

<details>
<summary>💡 Hinweis</summary>

1. Sortiere Meetings nach Startzeit
2. Verwende Min-Heap für Endzeiten der laufenden Meetings
3. Für jedes Meeting: Entferne beendete Meetings aus Heap
4. Füge aktuelles Meeting hinzu
5. Max. Heap-Größe = benötigte Räume

</details>

<details>
<summary>✅ Lösung</summary>

```python
import heapq

def min_meeting_rooms(intervals):
    """Findet minimale Anzahl Räume"""
    if not intervals:
        return 0
    
    # Sortiere nach Startzeit
    intervals.sort(key=lambda x: x[0])
    
    # Min-Heap für Endzeiten
    heap = []
    
    for start, end in intervals:
        # Entferne alle Meetings die vor diesem Start enden
        while heap and heap[0] <= start:
            heapq.heappop(heap)
        
        # Füge aktuelles Meeting hinzu
        heapq.heappush(heap, end)
    
    # Anzahl Elemente im Heap = gleichzeitig laufende Meetings
    return len(heap)

# Alternative mit detailliertem Tracking
def min_meeting_rooms_v2(intervals):
    """Mit Schritt-für-Schritt Tracking"""
    if not intervals:
        return 0
    
    intervals.sort(key=lambda x: x[0])
    heap = []
    max_rooms = 0
    
    print("Meeting Schedule:")
    for start, end in intervals:
        # Räume freigeben
        while heap and heap[0] <= start:
            freed = heapq.heappop(heap)
            print(f"  Raum frei um {freed}")
        
        # Neues Meeting
        heapq.heappush(heap, end)
        print(f"  Meeting {start}-{end}: {len(heap)} Räume benötigt")
        max_rooms = max(max_rooms, len(heap))
    
    return max_rooms

# Tests
print(min_meeting_rooms([[0, 30], [5, 10], [15, 20]]))  # 2
print(min_meeting_rooms([[7, 10], [2, 4]]))  # 1
print(min_meeting_rooms([[1, 5], [2, 3], [3, 6]]))  # 2
print(min_meeting_rooms([[1, 3], [3, 6]]))  # 1

print("\n=== Detailliert ===")
min_meeting_rooms_v2([[0, 30], [5, 10], [15, 20]])
```

**Zeitkomplexität:** O(n log n) - Sortieren + n Heap-Operationen
**Speicherkomplexität:** O(n) - Worst Case alle Meetings überlappen

</details>

---

## 🟡 Übung 5: K Closest Points to Origin

**Schwierigkeit:** Mittel

Finde die k nächsten Punkte zum Ursprung (0, 0) in einem 2D-Raum.

```python
import heapq

def k_closest_points(points, k):
    """
    Findet k nächste Punkte zum Ursprung.
    
    Args:
        points: Liste von [x, y] Koordinaten
        k: Anzahl nächster Punkte
    
    Returns:
        Liste der k nächsten Punkte
    """
    # Dein Code hier
    pass

# Tests
print(k_closest_points([[1, 3], [-2, 2]], k=1))  # [[-2, 2]]
print(k_closest_points([[3, 3], [5, -1], [-2, 4]], k=2))  # [[3, 3], [-2, 4]]
```

**Erwarteter Output:**
```
[[-2, 2]]
[[3, 3], [-2, 4]]
```

<details>
<summary>💡 Hinweis</summary>

1. Distanz zum Ursprung: sqrt(x² + y²)
2. Für Vergleiche reicht x² + y² (kein sqrt nötig)
3. Verwende Max-Heap der Größe k
4. Speichere (-distanz, point) für Max-Heap Verhalten

</details>

<details>
<summary>✅ Lösung</summary>

```python
import heapq

def k_closest_points(points, k):
    """Findet k nächste Punkte zum Ursprung"""
    # Max-Heap der Größe k (negierte Distanzen)
    heap = []
    
    for x, y in points:
        # Berechne quadrierte Distanz (sqrt nicht nötig für Vergleich)
        dist = x**2 + y**2
        
        if len(heap) < k:
            # Heap noch nicht voll, füge hinzu
            # Negiere Distanz für Max-Heap
            heapq.heappush(heap, (-dist, [x, y]))
        elif dist < -heap[0][0]:
            # Punkt ist näher als fernster im Heap
            heapq.heapreplace(heap, (-dist, [x, y]))
    
    # Extrahiere nur Punkte (nicht Distanzen)
    return [point for dist, point in heap]

# Alternative mit nsmallest (einfacher)
def k_closest_points_v2(points, k):
    """Einfachere Lösung mit nsmallest"""
    return heapq.nsmallest(k, points, key=lambda p: p[0]**2 + p[1]**2)

# Tests
print(k_closest_points([[1, 3], [-2, 2]], k=1))  # [[-2, 2]]
print(k_closest_points([[3, 3], [5, -1], [-2, 4]], k=2))  # [[3, 3], [-2, 4]]

print("\nMit nsmallest:")
print(k_closest_points_v2([[1, 3], [-2, 2]], k=1))
print(k_closest_points_v2([[3, 3], [5, -1], [-2, 4]], k=2))
```

**Zeitkomplexität:** O(n log k) - Jeder Punkt wird einmal verarbeitet
**Speicherkomplexität:** O(k) - Heap-Größe

</details>

---

## 🔴 Übung 6: Median aus Daten-Stream

**Schwierigkeit:** Schwer

Implementiere eine Klasse die einen laufenden Median aus einem Daten-Stream berechnet.

```python
import heapq

class MedianFinder:
    def __init__(self):
        """Initialisiert Median Finder"""
        # Dein Code hier
        pass
    
    def add_num(self, num):
        """Fügt Zahl zum Stream hinzu"""
        pass
    
    def find_median(self):
        """Gibt aktuellen Median zurück"""
        pass

# Test
mf = MedianFinder()
mf.add_num(1)
mf.add_num(2)
print(mf.find_median())  # 1.5

mf.add_num(3)
print(mf.find_median())  # 2.0
```

**Erwarteter Output:**
```
1.5
2.0
```

<details>
<summary>💡 Hinweis</summary>

**Zwei-Heap Strategie:**
1. Max-Heap für untere Hälfte der Zahlen
2. Min-Heap für obere Hälfte der Zahlen
3. Balance: Größen-Unterschied maximal 1
4. Median: Mittleres Element oder Durchschnitt der beiden Mitten

</details>

<details>
<summary>✅ Lösung</summary>

```python
import heapq

class MedianFinder:
    """Findet Median aus Daten-Stream mit zwei Heaps"""
    
    def __init__(self):
        # Max-Heap für untere Hälfte (negierte Werte)
        self.lower = []  # Max-Heap via negation
        # Min-Heap für obere Hälfte
        self.upper = []  # Min-Heap
    
    def add_num(self, num):
        """Fügt Zahl hinzu - O(log n)"""
        # Füge immer zuerst zu lower hinzu
        heapq.heappush(self.lower, -num)
        
        # Stelle sicher: max(lower) <= min(upper)
        if self.lower and self.upper and -self.lower[0] > self.upper[0]:
            # Verschiebe größtes aus lower zu upper
            val = -heapq.heappop(self.lower)
            heapq.heappush(self.upper, val)
        
        # Balance Größen: Differenz maximal 1
        if len(self.lower) > len(self.upper) + 1:
            val = -heapq.heappop(self.lower)
            heapq.heappush(self.upper, val)
        elif len(self.upper) > len(self.lower) + 1:
            val = heapq.heappop(self.upper)
            heapq.heappush(self.lower, -val)
    
    def find_median(self):
        """Gibt Median zurück - O(1)"""
        if len(self.lower) > len(self.upper):
            return -self.lower[0]
        elif len(self.upper) > len(self.lower):
            return self.upper[0]
        else:
            # Gleich viele Elemente: Durchschnitt
            return (-self.lower[0] + self.upper[0]) / 2.0
    
    def __repr__(self):
        """Debug-Ausgabe"""
        lower_vals = sorted([-x for x in self.lower], reverse=True)
        upper_vals = sorted(self.upper)
        return f"Lower (Max-Heap): {lower_vals}, Upper (Min-Heap): {upper_vals}"

# Tests
print("=== Median Finder Test ===\n")
mf = MedianFinder()

numbers = [5, 15, 1, 3, 8, 7, 9, 10, 20, 12]
for num in numbers:
    mf.add_num(num)
    print(f"Nach {num:2d}: Median = {mf.find_median():.1f}")
    print(f"  {mf}")
    print()

# Einfacher Test
mf2 = MedianFinder()
mf2.add_num(1)
mf2.add_num(2)
print(f"Median von [1, 2]: {mf2.find_median()}")  # 1.5

mf2.add_num(3)
print(f"Median von [1, 2, 3]: {mf2.find_median()}")  # 2.0
```

**Zeitkomplexität:**
- `add_num()`: O(log n)
- `find_median()`: O(1)

**Speicherkomplexität:** O(n)

**Warum funktioniert das?**
- Lower-Heap enthält kleinere Hälfte (als Max-Heap)
- Upper-Heap enthält größere Hälfte (als Min-Heap)
- Balance stellt sicher: |len(lower) - len(upper)| ≤ 1
- Median ist entweder Top von größerem Heap oder Durchschnitt beider Tops

</details>

---

## 🔴 Übung 7: Skyline Problem

**Schwierigkeit:** Sehr Schwer

Gegeben Gebäude als [left, right, height], finde die Skyline (Umriss).

```python
import heapq

def get_skyline(buildings):
    """
    Berechnet Skyline von Gebäuden.
    
    Args:
        buildings: Liste von [left, right, height]
    
    Returns:
        Liste von [x, height] Key-Points der Skyline
    """
    # Dein Code hier
    pass

# Test
buildings = [[2, 9, 10], [3, 7, 15], [5, 12, 12], [15, 20, 10], [19, 24, 8]]
print(get_skyline(buildings))
# [[2, 10], [3, 15], [7, 12], [12, 0], [15, 10], [20, 8], [24, 0]]
```

**Erwarteter Output:**
```
[[2, 10], [3, 15], [7, 12], [12, 0], [15, 10], [20, 8], [24, 0]]
```

<details>
<summary>💡 Hinweis</summary>

**Sweep-Line Algorithmus:**
1. Erstelle Events: (x, START/END, height)
2. Sortiere Events
3. Verwende Max-Heap für aktive Höhen
4. Bei jedem Event: Update Heap, prüfe ob max. Höhe sich ändert
5. Wenn Höhe ändert: Key-Point hinzufügen

</details>

<details>
<summary>✅ Lösung</summary>

```python
import heapq
from collections import defaultdict

def get_skyline(buildings):
    """Berechnet Skyline mit Sweep-Line + Heap"""
    # Erstelle Events
    events = []
    for left, right, height in buildings:
        # Start-Event: negative Höhe für Sortierung (starts vor ends)
        events.append((left, -height, 's'))
        # End-Event
        events.append((right, height, 'e'))
    
    # Sortiere Events
    # Bei gleicher x-Position: Starts vor Ends
    events.sort()
    
    result = []
    # Max-Heap für aktive Höhen (negiert für Max-Heap)
    heights = [0]  # Ground level immer aktiv
    
    for x, h, event_type in events:
        if event_type == 's':
            # Start eines Gebäudes: Füge Höhe hinzu
            heapq.heappush(heights, h)  # h ist bereits negativ
        else:
            # End eines Gebäudes: Entferne Höhe
            heights.remove(-h)  # Entferne positive Höhe
            heapq.heapify(heights)  # Stelle Heap-Eigenschaft wieder her
        
        # Aktuelle max. Höhe
        max_height = -heights[0]
        
        # Wenn Höhe sich ändert: Key-Point
        if not result or result[-1][1] != max_height:
            result.append([x, max_height])
    
    return result

# Optimierte Version mit Counter für bessere Performance
from collections import Counter

def get_skyline_optimized(buildings):
    """Optimierte Version mit Counter"""
    events = []
    for left, right, height in buildings:
        events.append((left, -height))  # Start (negative für Sortierung)
        events.append((right, height))   # End
    
    events.sort()
    
    result = []
    height_count = Counter([0])  # Höhen-Zähler
    
    prev_max = 0
    
    i = 0
    while i < len(events):
        current_x = events[i][0]
        
        # Verarbeite alle Events an derselben x-Position
        while i < len(events) and events[i][0] == current_x:
            h = events[i][1]
            if h < 0:  # Start
                height_count[-h] += 1
            else:  # End
                height_count[h] -= 1
                if height_count[h] == 0:
                    del height_count[h]
            i += 1
        
        # Max. aktive Höhe
        current_max = max(height_count.keys())
        
        # Höhe hat sich geändert?
        if current_max != prev_max:
            result.append([current_x, current_max])
            prev_max = current_max
    
    return result

# Tests
buildings = [[2, 9, 10], [3, 7, 15], [5, 12, 12], [15, 20, 10], [19, 24, 8]]
print("Skyline:")
print(get_skyline(buildings))

print("\nOptimiert:")
print(get_skyline_optimized(buildings))

# Einfacheres Beispiel
buildings2 = [[0, 2, 3], [2, 5, 3]]
print("\nEinfaches Beispiel:")
print(get_skyline(buildings2))  # [[0, 3], [5, 0]]
```

**Zeitkomplexität:** O(n² log n) für erste Version (remove ist O(n))
                    O(n log n) für optimierte Version
**Speicherkomplexität:** O(n)

**Visualisierung:**
```
Gebäude: [2,9,10], [3,7,15], [5,12,12], [15,20,10], [19,24,8]

Höhe
15 |     ┌──┐
12 |     │  └────┐
10 |  ┌──┘       │           ┌────┐
8  |  │          │           │    └──┐
0  |──┘          └───────────┘       └──
   2  3  5  7  9 12         15   20  24  x-Achse

Key-Points: [2,10], [3,15], [7,12], [12,0], [15,10], [20,8], [24,0]
```

</details>

---

## 🔴 Übung 8: Reorganize String

**Schwierigkeit:** Schwer

Ordne einen String so um, dass keine zwei gleichen Zeichen nebeneinander stehen.

```python
import heapq
from collections import Counter

def reorganize_string(s):
    """
    Ordnet String um, sodass keine gleichen Zeichen nebeneinander sind.
    
    Args:
        s: Input String
    
    Returns:
        Umgeordneter String, oder "" wenn unmöglich
    """
    # Dein Code hier
    pass

# Tests
print(reorganize_string("aab"))  # "aba" oder "baa"
print(reorganize_string("aaab"))  # "" (unmöglich)
print(reorganize_string("aaabbbcc"))  # z.B. "abacabcb"
```

**Erwarteter Output:**
```
aba
""
abacabcb
```

<details>
<summary>💡 Hinweis</summary>

**Greedy mit Heap:**
1. Zähle Buchstaben-Häufigkeiten
2. Max-Heap (häufigstes zuerst)
3. Nimm immer häufigstes verfügbares Zeichen
4. Merke vorheriges Zeichen um Wiederholung zu vermeiden
5. Unmöglich wenn häufigstes Zeichen > (len + 1) / 2

</details>

<details>
<summary>✅ Lösung</summary>

```python
import heapq
from collections import Counter

def reorganize_string(s):
    """Ordnet String um ohne benachbarte Duplikate"""
    # Zähle Häufigkeiten
    count = Counter(s)
    
    # Prüfe ob möglich
    # Wenn ein Zeichen mehr als (n+1)/2 mal vorkommt, unmöglich
    max_freq = max(count.values())
    if max_freq > (len(s) + 1) // 2:
        return ""
    
    # Max-Heap (negierte Häufigkeiten)
    heap = [(-freq, char) for char, freq in count.items()]
    heapq.heapify(heap)
    
    result = []
    prev_freq, prev_char = 0, ''
    
    while heap:
        # Nimm häufigstes verfügbares Zeichen
        freq, char = heapq.heappop(heap)
        result.append(char)
        
        # Füge vorheriges Zeichen zurück (wenn noch Vorkommen übrig)
        if prev_freq < 0:
            heapq.heappush(heap, (prev_freq, prev_char))
        
        # Update vorheriges
        prev_freq = freq + 1  # freq ist negativ, +1 = eine Verwendung
        prev_char = char
    
    return ''.join(result)

# Alternative Lösung: Zwei häufigste abwechselnd
def reorganize_string_v2(s):
    """Alternative mit pair-wise Einfügen"""
    count = Counter(s)
    max_freq = max(count.values())
    
    if max_freq > (len(s) + 1) // 2:
        return ""
    
    # Max-Heap
    heap = [(-freq, char) for char, freq in count.items()]
    heapq.heapify(heap)
    
    result = []
    
    while len(heap) >= 2:
        # Nimm zwei häufigste
        freq1, char1 = heapq.heappop(heap)
        freq2, char2 = heapq.heappop(heap)
        
        # Füge beide hinzu
        result.extend([char1, char2])
        
        # Füge zurück wenn noch Vorkommen übrig
        if freq1 + 1 < 0:
            heapq.heappush(heap, (freq1 + 1, char1))
        if freq2 + 1 < 0:
            heapq.heappush(heap, (freq2 + 1, char2))
    
    # Ein Zeichen übrig?
    if heap:
        freq, char = heapq.heappop(heap)
        if freq + 1 < 0:  # Mehr als eins übrig
            return ""
        result.append(char)
    
    return ''.join(result)

# Tests
print(reorganize_string("aab"))  # "aba"
print(reorganize_string("aaab"))  # ""
print(reorganize_string("aaabbbcc"))  # "abacabcb"
print(reorganize_string("vvvlo"))  # "vovlv"

print("\nAlternative:")
print(reorganize_string_v2("aab"))
print(reorganize_string_v2("aaab"))
print(reorganize_string_v2("aaabbbcc"))
```

**Zeitkomplexität:** O(n log k) wobei k = Anzahl unterschiedlicher Zeichen
**Speicherkomplexität:** O(k)

</details>

---

## 🔴 Übung 9: Task Scheduler mit Cooldown

**Schwierigkeit:** Sehr Schwer

Minimiere die Zeit zum Ausführen aller Tasks mit Cooldown zwischen gleichen Tasks.

```python
import heapq
from collections import Counter

def least_interval(tasks, n):
    """
    Berechnet minimale Zeit für Task-Ausführung mit Cooldown.
    
    Args:
        tasks: Liste von Task-Namen (z.B. ['A', 'A', 'A', 'B', 'B'])
        n: Cooldown-Perioden zwischen gleichen Tasks
    
    Returns:
        Minimale Anzahl Zeiteinheiten
    """
    # Dein Code hier
    pass

# Tests
print(least_interval(['A', 'A', 'A', 'B', 'B', 'B'], n=2))  # 8
# A -> B -> idle -> A -> B -> idle -> A -> B

print(least_interval(['A', 'A', 'A', 'B', 'B', 'B'], n=0))  # 6
# Kein Cooldown: A A A B B B

print(least_interval(['A', 'A', 'A', 'A', 'A', 'A', 'B', 'C', 'D', 'E', 'F', 'G'], n=2))  # 16
```

**Erwarteter Output:**
```
8
6
16
```

<details>
<summary>💡 Hinweis</summary>

**Greedy Strategie:**
1. Zähle Task-Häufigkeiten
2. Max-Heap für verfügbare Tasks
3. Simuliere Zeitschritte
4. Cooldown-Queue für Tasks in Cooldown
5. Oder: Mathematische Formel basierend auf häufigstem Task

</details>

<details>
<summary>✅ Lösung</summary>

```python
import heapq
from collections import Counter, deque

# Lösung 1: Simulation mit Heap
def least_interval(tasks, n):
    """Simuliert Task-Ausführung mit Heap"""
    if n == 0:
        return len(tasks)
    
    # Zähle Häufigkeiten
    count = Counter(tasks)
    
    # Max-Heap (negierte Häufigkeiten)
    heap = [-freq for freq in count.values()]
    heapq.heapify(heap)
    
    time = 0
    
    while heap:
        # Cooldown-Queue: Tasks die gerade ausgeführt wurden
        cooldown = []
        
        # Führe n+1 Tasks aus (ein Cooldown-Zyklus)
        for _ in range(n + 1):
            if heap:
                freq = heapq.heappop(heap)
                if freq + 1 < 0:  # Noch mehr von diesem Task übrig
                    cooldown.append(freq + 1)
                time += 1
            elif cooldown:
                # Heap leer, aber noch Tasks in Cooldown
                time += 1  # Idle time
        
        # Füge Tasks nach Cooldown zurück
        for freq in cooldown:
            heapq.heappush(heap, freq)
    
    return time

# Lösung 2: Mathematische Formel (effizienter)
def least_interval_v2(tasks, n):
    """Optimierte Lösung mit Formel"""
    if n == 0:
        return len(tasks)
    
    count = Counter(tasks)
    max_freq = max(count.values())
    
    # Anzahl Tasks mit maximaler Häufigkeit
    max_count = sum(1 for freq in count.values() if freq == max_freq)
    
    # Minimale Zeit berechnen
    # Partitions: (max_freq - 1) Blöcke mit jeweils (n + 1) Slots
    # Plus letzter Block mit max_count Tasks
    min_time = (max_freq - 1) * (n + 1) + max_count
    
    # Kann nicht weniger als len(tasks) sein
    return max(min_time, len(tasks))

# Lösung 3: Detaillierte Simulation (für Verständnis)
def least_interval_detailed(tasks, n):
    """Mit detaillierter Ausgabe"""
    if n == 0:
        return len(tasks)
    
    count = Counter(tasks)
    heap = [(-freq, task) for task, freq in count.items()]
    heapq.heapify(heap)
    
    result = []
    time = 0
    
    while heap:
        cooldown_queue = []
        
        # Ein Cooldown-Zyklus
        for i in range(n + 1):
            if heap:
                freq, task = heapq.heappop(heap)
                result.append(task)
                if freq + 1 < 0:
                    cooldown_queue.append((freq + 1, task))
                time += 1
            elif cooldown_queue:
                result.append('idle')
                time += 1
        
        # Zurück zum Heap
        for item in cooldown_queue:
            heapq.heappush(heap, item)
    
    print(f"Schedule: {' -> '.join(result)}")
    return time

# Tests
print("Lösung 1 (Simulation):")
print(least_interval(['A', 'A', 'A', 'B', 'B', 'B'], n=2))  # 8
print(least_interval(['A', 'A', 'A', 'B', 'B', 'B'], n=0))  # 6
print(least_interval(['A', 'A', 'A', 'A', 'A', 'A', 'B', 'C', 'D', 'E', 'F', 'G'], n=2))  # 16

print("\nLösung 2 (Formel):")
print(least_interval_v2(['A', 'A', 'A', 'B', 'B', 'B'], n=2))  # 8
print(least_interval_v2(['A', 'A', 'A', 'B', 'B', 'B'], n=0))  # 6
print(least_interval_v2(['A', 'A', 'A', 'A', 'A', 'A', 'B', 'C', 'D', 'E', 'F', 'G'], n=2))  # 16

print("\nDetailliert:")
result = least_interval_detailed(['A', 'A', 'A', 'B', 'B', 'B'], n=2)
print(f"Zeit: {result}")
```

**Zeitkomplexität:** 
- Simulation: O(n log k) wobei k = Anzahl unterschiedlicher Tasks
- Formel: O(n)

**Speicherkomplexität:** O(k)

**Erklärung der Formel:**
- Häufigster Task muss `max_freq` mal ausgeführt werden
- Zwischen Ausführungen: mindestens `n` andere Slots
- Das ergibt `(max_freq - 1) * (n + 1)` Slots
- Plus finale Ausführung aller Tasks mit `max_freq`

</details>

---

## 🔴 Übung 10: Sliding Window Median

**Schwierigkeit:** Sehr Schwer

Finde Median für jedes Sliding Window der Größe k.

```python
import heapq

def median_sliding_window(nums, k):
    """
    Berechnet Median für jedes Window.
    
    Args:
        nums: Liste von Zahlen
        k: Window-Größe
    
    Returns:
        Liste der Mediane
    """
    # Dein Code hier
    pass

# Tests
print(median_sliding_window([1, 3, -1, -3, 5, 3, 6, 7], k=3))
# [1.0, -1.0, -1.0, 3.0, 5.0, 6.0]

print(median_sliding_window([1, 2, 3, 4, 2, 3, 1, 4, 2], k=3))
# [2.0, 3.0, 3.0, 3.0, 2.0, 3.0, 2.0]
```

**Erwarteter Output:**
```
[1.0, -1.0, -1.0, 3.0, 5.0, 6.0]
[2.0, 3.0, 3.0, 3.0, 2.0, 3.0, 2.0]
```

<details>
<summary>💡 Hinweis</summary>

Kombiniere MedianFinder mit Sliding Window:
1. Zwei Heaps für Median (wie in Übung 6)
2. Füge neue Elemente hinzu
3. Entferne alte Elemente (lazy deletion)
4. Rebalanciere nach jedem Schritt

</details>

<details>
<summary>✅ Lösung</summary>

```python
import heapq
from collections import Counter

def median_sliding_window(nums, k):
    """Sliding Window Median mit zwei Heaps"""
    if not nums or k <= 0:
        return []
    
    # Zwei Heaps + Lazy deletion
    lower = []  # Max-Heap (negierte Werte)
    upper = []  # Min-Heap
    
    # Counter für lazy deletion
    to_remove = Counter()
    
    def add_num(num):
        """Fügt Zahl hinzu"""
        if not lower or num <= -lower[0]:
            heapq.heappush(lower, -num)
        else:
            heapq.heappush(upper, num)
    
    def remove_num(num):
        """Markiert Zahl für Entfernung"""
        to_remove[num] += 1
    
    def clean_heap(heap, negate=False):
        """Entfernt lazy deleted Elemente vom Top"""
        while heap:
            val = -heap[0] if negate else heap[0]
            if to_remove[val] > 0:
                to_remove[val] -= 1
                heapq.heappop(heap)
            else:
                break
    
    def balance():
        """Balanciert Heaps"""
        # Bereinige Tops
        clean_heap(lower, negate=True)
        clean_heap(upper, negate=False)
        
        # Größen-Balance
        while len(lower) > len(upper) + 1:
            val = -heapq.heappop(lower)
            heapq.heappush(upper, val)
        
        while len(upper) > len(lower):
            val = heapq.heappop(upper)
            heapq.heappush(lower, -val)
    
    def get_median():
        """Berechnet aktuellen Median"""
        clean_heap(lower, negate=True)
        clean_heap(upper, negate=False)
        
        if k % 2 == 1:
            return float(-lower[0])
        else:
            return (-lower[0] + upper[0]) / 2.0
    
    result = []
    
    # Initialisiere erstes Window
    for i in range(k):
        add_num(nums[i])
    balance()
    result.append(get_median())
    
    # Slide Window
    for i in range(k, len(nums)):
        # Entferne altes Element
        remove_num(nums[i - k])
        
        # Füge neues hinzu
        add_num(nums[i])
        
        # Rebalanciere
        balance()
        
        # Median berechnen
        result.append(get_median())
    
    return result

# Einfachere aber langsamere Lösung für kleine k
def median_sliding_window_simple(nums, k):
    """Einfache Lösung: Window sortieren"""
    result = []
    
    for i in range(len(nums) - k + 1):
        window = sorted(nums[i:i+k])
        
        if k % 2 == 1:
            result.append(float(window[k // 2]))
        else:
            result.append((window[k // 2 - 1] + window[k // 2]) / 2.0)
    
    return result

# Tests
print("Lösung mit Heaps:")
print(median_sliding_window([1, 3, -1, -3, 5, 3, 6, 7], k=3))
print(median_sliding_window([1, 2, 3, 4, 2, 3, 1, 4, 2], k=3))

print("\nEinfache Lösung:")
print(median_sliding_window_simple([1, 3, -1, -3, 5, 3, 6, 7], k=3))
print(median_sliding_window_simple([1, 2, 3, 4, 2, 3, 1, 4, 2], k=3))
```

**Zeitkomplexität:** 
- Heap-Lösung: O(n log k)
- Simple: O(nk log k)

**Speicherkomplexität:** O(k)

</details>

---

## Zusammenfassung

Diese Übungen haben gezeigt:

✅ **Grundlegende Heap-Operationen** (Top K, Kth Largest)
✅ **Priority Queue Patterns** (Meeting Rooms, Closest Points)
✅ **Stream-Algorithmen** (Median Finder)
✅ **Fortgeschrittene Techniken** (Skyline, Task Scheduler)
✅ **Optimierungs-Strategien** (Lazy Deletion, Two-Heap Pattern)

Die Lösungen findest du auch im Kapitel [[06_Praxis/04_Lösungen/Lösungen Teil 4|Lösungen Teil 4]].

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [heapq - Heap queue algorithm](https://docs.python.org/3/library/heapq.html) - Vollständige API
- [heapq Source Code](https://github.com/python/cpython/blob/main/Lib/heapq.py) - Implementation verstehen
- [Algorithm Examples](https://docs.python.org/3/library/heapq.html#basic-examples) - Weitere Beispiele

## Verwandte Themen

- [[04_Fortgeschritten/03_Heap_Queue/01_Heapq Module|Heapq Module]] - Grundlagen
- [[04_Fortgeschritten/03_Heap_Queue/02_Priority Queues|Priority Queues]] - Anwendungen
- [[04_Fortgeschritten/02_Deque/04_Übungen Deque|Übungen Deque]] - Mehr Übungen
- [[02_Sequenzen/04_Comprehensions/05_Übungen Comprehensions|Übungen Comprehensions]] - Weitere Praxis

---
← [[04_Fortgeschritten/03_Heap_Queue/02_Priority Queues|Priority Queues]] | [[INDEX|📑 Index]] | [[04_Fortgeschritten/04_Dataclasses/01_Dataclass Basics|Dataclass Basics]] →