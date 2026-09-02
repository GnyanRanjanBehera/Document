# Java Heap (PriorityQueue) Cheat Sheet

Java doesn't have a built-in `Heap` class — you use **`PriorityQueue`**, which is a heap under the hood (binary min-heap by default).

---

## 1. Declaration

```java
import java.util.PriorityQueue;
import java.util.Collections;

// Min-Heap (default) — smallest element at top
PriorityQueue<Integer> minHeap = new PriorityQueue<>();

// Max-Heap — largest element at top
PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());

// Max-Heap (alternative)
PriorityQueue<Integer> maxHeap2 = new PriorityQueue<>((a, b) -> b - a);

// With initial capacity
PriorityQueue<Integer> pq = new PriorityQueue<>(20);

// From existing collection
PriorityQueue<Integer> pq2 = new PriorityQueue<>(list);
```

---

## 2. Core Operations

| Method | Description | Time Complexity |
|---|---|---|
| `add(e)` / `offer(e)` | Insert element | O(log n) |
| `peek()` | View top element (null if empty) | O(1) |
| `poll()` | Remove & return top (null if empty) | O(log n) |
| `remove(e)` | Remove specific element | O(n) |
| `remove()` | Remove top (throws if empty) | O(log n) |
| `size()` | Number of elements | O(1) |
| `isEmpty()` | Check if empty | O(1) |
| `contains(e)` | Check if element exists | O(n) |
| `clear()` | Remove all elements | O(n) |

**`add` vs `offer`**: functionally same for `PriorityQueue` (unbounded), but `offer` is preferred by convention for queues.
**`remove()` vs `poll()`**: `remove()` throws `NoSuchElementException` on empty queue; `poll()` returns `null`.

---

## 3. Custom Objects — Comparator

```java
class Point {
    int x, y;
    Point(int x, int y) { this.x = x; this.y = y; }
}

// Min-heap by x
PriorityQueue<Point> pq = new PriorityQueue<>((a, b) -> a.x - b.x);

// Multi-key sort: by x, then by y
PriorityQueue<Point> pq2 = new PriorityQueue<>(
    (a, b) -> a.x != b.x ? a.x - b.x : a.y - b.y
);

// Using Comparator.comparingInt (avoids overflow issues)
PriorityQueue<Point> pq3 = new PriorityQueue<>(
    Comparator.comparingInt((Point p) -> p.x).thenComparingInt(p -> p.y)
);
```

⚠️ Avoid `a.x - b.x` for large/negative ints — risk of integer overflow. Prefer `Integer.compare(a.x, b.x)` or `Comparator.comparingInt`.

---

## 4. Iterating (order NOT guaranteed)

```java
for (int val : pq) {
    System.out.println(val); // arbitrary heap order, not sorted!
}
```

To get sorted order, you must `poll()` repeatedly:
```java
while (!pq.isEmpty()) {
    System.out.println(pq.poll());
}
```

---

## 5. Common Patterns

### Kth Largest Element (min-heap of size k)
```java
PriorityQueue<Integer> minHeap = new PriorityQueue<>();
for (int num : nums) {
    minHeap.offer(num);
    if (minHeap.size() > k) minHeap.poll();
}
return minHeap.peek(); // kth largest
```

### Top K Frequent Elements
```java
PriorityQueue<Map.Entry<Integer,Integer>> pq =
    new PriorityQueue<>((a, b) -> a.getValue() - b.getValue());
for (var entry : freqMap.entrySet()) {
    pq.offer(entry);
    if (pq.size() > k) pq.poll();
}
```

### Merge K Sorted Lists
```java
PriorityQueue<ListNode> pq = new PriorityQueue<>((a, b) -> a.val - b.val);
for (ListNode node : lists) if (node != null) pq.offer(node);

ListNode dummy = new ListNode(-1), curr = dummy;
while (!pq.isEmpty()) {
    ListNode min = pq.poll();
    curr.next = min;
    curr = curr.next;
    if (min.next != null) pq.offer(min.next);
}
```

### Two Heaps — Running Median
```java
PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder()); // lower half
PriorityQueue<Integer> minHeap = new PriorityQueue<>(); // upper half

void addNum(int num) {
    maxHeap.offer(num);
    minHeap.offer(maxHeap.poll());
    if (minHeap.size() > maxHeap.size()) maxHeap.offer(minHeap.poll());
}

double findMedian() {
    if (maxHeap.size() > minHeap.size()) return maxHeap.peek();
    return (maxHeap.peek() + minHeap.peek()) / 2.0;
}
```

### Dijkstra's Algorithm (shortest path)
```java
PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> a[1] - b[1]); // {node, dist}
pq.offer(new int[]{start, 0});
while (!pq.isEmpty()) {
    int[] curr = pq.poll();
    int node = curr[0], dist = curr[1];
    if (dist > distArr[node]) continue; // stale entry
    for (int[] edge : graph.get(node)) {
        int next = edge[0], weight = edge[1];
        if (dist + weight < distArr[next]) {
            distArr[next] = dist + weight;
            pq.offer(new int[]{next, dist + weight});
        }
    }
}
```

---

## 6. Building a Heap From an Array — Heapify

```java
int[] arr = {5, 3, 8, 1, 9};
PriorityQueue<Integer> pq = new PriorityQueue<>();
for (int x : arr) pq.offer(x);
// Bulk-construction from a Collection is O(n), one-by-one add is O(n log n)

// Faster: pass a List directly
List<Integer> list = Arrays.asList(5, 3, 8, 1, 9);
PriorityQueue<Integer> pq2 = new PriorityQueue<>(list); // O(n) heapify
```

---

## 7. Complexity Summary

| Operation | Time |
|---|---|
| Insert | O(log n) |
| Delete top | O(log n) |
| Peek top | O(1) |
| Search arbitrary element | O(n) |
| Build heap from array | O(n) |
| Heap sort (n polls) | O(n log n) |

---

## 8. Gotchas / Notes

- `PriorityQueue` is **not thread-safe** — use `PriorityBlockingQueue` for concurrent access.
- It allows **duplicates** and **null is not allowed** (throws `NullPointerException`).
- Default capacity is 11, grows automatically.
- It does **not** implement `List`, so no indexed access (`pq.get(i)` doesn't exist).
- For a **max-heap of primitives**, remember `Collections.reverseOrder()` only works with the no-arg constructor on `Comparable` types (like `Integer`).
- Iteration order is **not** sorted — only `poll()` guarantees sorted retrieval.
