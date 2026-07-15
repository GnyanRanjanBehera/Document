# Java Queue — Interview Cheat Sheet

A **Queue** is a FIFO (**First In, First Out**) data structure. In Java, `Queue` is an interface implemented by several classes, each suited for different scenarios (simple FIFO, double-ended, priority-based, blocking/concurrent).

```java
import java.util.*;
import java.util.concurrent.*;
```

---

## 1. Hierarchy (Interview favorite question)

```
Collection (interface)
    └── Queue (interface)
          ├── LinkedList              (FIFO queue + Deque + List)
          ├── PriorityQueue           (min-heap / max-heap ordering)
          └── Deque (interface)
                ├── ArrayDeque        (double-ended queue, also used as stack)
                └── LinkedList

java.util.concurrent
    ├── BlockingQueue (interface)
    │     ├── ArrayBlockingQueue
    │     ├── LinkedBlockingQueue
    │     └── PriorityBlockingQueue
    └── ConcurrentLinkedQueue
```

| Implementation | Ordering | Null allowed | Thread-safe | Bounded |
|---|---|---|---|---|
| `LinkedList` | FIFO (insertion) | Yes | No | No |
| `ArrayDeque` | FIFO or LIFO | No | No | No |
| `PriorityQueue` | Priority (natural/comparator) | No | No | No |
| `ArrayBlockingQueue` | FIFO | No | Yes | Yes (fixed) |
| `LinkedBlockingQueue` | FIFO | No | Yes | Optional |
| `ConcurrentLinkedQueue` | FIFO | No | Yes (lock-free) | No |

> **Key interview line:** *"`ArrayDeque` is now the recommended general-purpose queue implementation — it's faster than `LinkedList` for queue operations because of better cache locality, and unlike `LinkedList` it doesn't implement `List`, so it can't be misused with index-based access."*

---

## 2. Core `Queue` Interface Methods

| Method | Throws on failure? | Purpose |
|---|---|---|
| `add(e)` | Yes (`IllegalStateException` if capacity-restricted) | Insert at tail |
| `offer(e)` | No (returns `false`) | Insert at tail — **preferred** |
| `remove()` | Yes (`NoSuchElementException`) | Remove & return head |
| `poll()` | No (returns `null`) | Remove & return head — **preferred** |
| `element()` | Yes (`NoSuchElementException`) | Return head without removing |
| `peek()` | No (returns `null`) | Return head without removing — **preferred** |

```java
Queue<Integer> queue = new LinkedList<>();
queue.offer(1);
queue.offer(2);
queue.offer(3);

System.out.println(queue.poll());   // 1 (FIFO)
System.out.println(queue.peek());   // 2
System.out.println(queue);          // [2, 3]
```

> **Interview tip:** Always prefer `offer`/`poll`/`peek` over `add`/`remove`/`element` — the former return special values (`false`/`null`) instead of throwing exceptions, which is safer in most code.

---

## 3. `Deque` — Double-Ended Queue Methods

`Deque` (Double Ended Queue) supports insertion/removal at **both** ends — can be used as a Queue, Stack, or both.

```java
Deque<Integer> deque = new ArrayDeque<>();
```

| Method | End | Throws? |
|---|---|---|
| `addFirst(e)` / `offerFirst(e)` | Head | throws / no |
| `addLast(e)` / `offerLast(e)` | Tail | throws / no |
| `removeFirst()` / `pollFirst()` | Head | throws / no |
| `removeLast()` / `pollLast()` | Tail | throws / no |
| `getFirst()` / `peekFirst()` | Head | throws / no |
| `getLast()` / `peekLast()` | Tail | throws / no |

```java
deque.offerFirst(1);   // [1]
deque.offerLast(2);    // [1, 2]
deque.offerFirst(0);   // [0, 1, 2]
System.out.println(deque.pollFirst());  // 0
System.out.println(deque.pollLast());   // 2
System.out.println(deque);              // [1]
```

**As a Queue (FIFO):** use `offer`/`poll`/`peek` (maps to `offerLast`/`pollFirst`/`peekFirst`)
**As a Stack (LIFO):** use `push`/`pop`/`peek` (maps to `addFirst`/`removeFirst`/`peekFirst`)

```java
Deque<Integer> asQueue = new ArrayDeque<>();
asQueue.offer(1); asQueue.offer(2); asQueue.offer(3);
System.out.println(asQueue.poll());   // 1 (FIFO)

Deque<Integer> asStack = new ArrayDeque<>();
asStack.push(1); asStack.push(2); asStack.push(3);
System.out.println(asStack.pop());    // 3 (LIFO)
```

### Other Deque methods
```java
deque.contains(1);
deque.size();
deque.isEmpty();
deque.iterator();               // front-to-back
deque.descendingIterator();     // back-to-front
deque.removeFirstOccurrence(1);
deque.removeLastOccurrence(1);
deque.clear();
```

---

## 4. `PriorityQueue` — Heap-based Priority Ordering

Elements are ordered by **natural ordering** or a **custom Comparator** — NOT insertion order. Internally a **binary heap** (min-heap by default).

```java
PriorityQueue<Integer> pq = new PriorityQueue<>();  // min-heap by default
pq.offer(30); pq.offer(10); pq.offer(20);

System.out.println(pq.poll());  // 10 (smallest first)
System.out.println(pq.poll());  // 20
```

### Max-heap using Comparator
```java
PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
maxHeap.offer(30); maxHeap.offer(10); maxHeap.offer(20);
System.out.println(maxHeap.poll());  // 30 (largest first)
```

### Custom objects with Comparator
```java
class Task {
    String name; int priority;
    Task(String name, int priority) { this.name = name; this.priority = priority; }
}

PriorityQueue<Task> taskQueue = new PriorityQueue<>(
    Comparator.comparingInt(t -> t.priority)
);
taskQueue.offer(new Task("Low", 3));
taskQueue.offer(new Task("High", 1));
taskQueue.offer(new Task("Medium", 2));
System.out.println(taskQueue.poll().name);  // "High" (priority 1 first)
```

### PriorityQueue-specific behavior
- `peek()` — returns the **highest priority** (smallest, by default) element, O(1)
- `poll()` — removes and returns highest priority element, O(log n)
- `offer()` — inserts maintaining heap property, O(log n)
- Iterating with `iterator()` does **NOT** guarantee sorted order (only `poll()` repeatedly does)

```java
PriorityQueue<Integer> pq2 = new PriorityQueue<>(List.of(5, 1, 3));
System.out.println(pq2);        // internal array order, NOT sorted! e.g. [1, 5, 3]
while (!pq2.isEmpty()) {
    System.out.print(pq2.poll() + " ");   // 1 3 5 -- correctly sorted via poll
}
```

---

## 5. Blocking Queues (`java.util.concurrent`) — for multi-threaded producer-consumer

```java
BlockingQueue<Integer> bq = new ArrayBlockingQueue<>(10);  // bounded, capacity 10
```

| Method | Behavior when full/empty |
|---|---|
| `put(e)` | **Blocks** until space available (producer waits) |
| `take()` | **Blocks** until element available (consumer waits) |
| `offer(e, timeout, unit)` | Waits up to timeout, then returns false |
| `poll(timeout, unit)` | Waits up to timeout, then returns null |

```java
BlockingQueue<Integer> queue = new LinkedBlockingQueue<>();

// Producer thread
new Thread(() -> {
    try { queue.put(1); } catch (InterruptedException e) {}
}).start();

// Consumer thread
new Thread(() -> {
    try { System.out.println(queue.take()); } catch (InterruptedException e) {}
}).start();
```

**Common types:**
- `ArrayBlockingQueue`: fixed capacity, array-backed
- `LinkedBlockingQueue`: optionally bounded, linked-node backed
- `PriorityBlockingQueue`: unbounded, priority-ordered, thread-safe
- `SynchronousQueue`: zero capacity — every `put` must wait for a matching `take`

---

## 6. Creating Queues — Common Patterns

```java
// FIFO queue
Queue<Integer> fifo = new LinkedList<>();
Queue<Integer> fifoFast = new ArrayDeque<>();   // preferred, faster

// Priority queue (min-heap)
Queue<Integer> minHeap = new PriorityQueue<>();

// Priority queue (max-heap)
Queue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());

// Deque (double-ended)
Deque<Integer> deque = new ArrayDeque<>();

// Thread-safe queue
Queue<Integer> concurrentQ = new ConcurrentLinkedQueue<>();

// Blocking queue (producer-consumer)
BlockingQueue<Integer> blockingQ = new LinkedBlockingQueue<>();
```

---

## 7. Interview Q&A Quick Hits

**Q: Queue vs Deque vs PriorityQueue?**
| Type | Order | Access points |
|---|---|---|
| `Queue` | FIFO | One end (head in, tail out) |
| `Deque` | FIFO or LIFO | Both ends |
| `PriorityQueue` | By priority (heap) | Only smallest/largest accessible |

**Q: Why prefer `ArrayDeque` over `LinkedList` for queue operations?**
A: `ArrayDeque` has better cache locality (contiguous array) and doesn't carry the overhead of node objects with two pointers each. The JDK docs recommend it over `LinkedList` and `Stack` for both stack and queue use cases.

**Q: Why can't we do `add(index, e)` on a `PriorityQueue`?**
A: Because it's not a `List` — it's a `Queue` backed by a binary heap where element position is determined by heap ordering, not insertion order. There's no meaningful "index."

**Q: How does PriorityQueue achieve O(log n) insert/remove?**
A: It's backed by a **binary heap** stored in an array. Insertion appends then "bubbles up" (sift-up) to restore heap property; removal takes the root, replaces it with the last element, then "bubbles down" (sift-down) — both O(log n).

**Q: What's the difference between `poll()` and `remove()`?**
A: Both remove the head, but `poll()` returns `null` on empty queue while `remove()` throws `NoSuchElementException`. Same distinction applies to `offer()`/`add()` and `peek()`/`element()`.

**Q: How do you implement a Stack using two Queues?**
```java
class StackUsingQueues {
    Queue<Integer> q1 = new LinkedList<>();
    Queue<Integer> q2 = new LinkedList<>();

    void push(int x) {
        q2.offer(x);
        while (!q1.isEmpty()) q2.offer(q1.poll());
        Queue<Integer> temp = q1; q1 = q2; q2 = temp;
    }

    int pop() {
        return q1.poll();
    }
}
```

**Q: Circular Queue — what problem does it solve?**
A: A normal array-based queue wastes space at the front after repeated dequeues (front index just keeps moving right). A **circular queue** wraps the index around using modulo (`(rear + 1) % capacity`), reusing freed slots.

```java
class CircularQueue {
    int[] arr; int front, rear, size, capacity;

    CircularQueue(int capacity) {
        this.capacity = capacity;
        arr = new int[capacity];
        front = 0; rear = -1; size = 0;
    }

    void enqueue(int x) {
        if (size == capacity) throw new RuntimeException("Queue is full");
        rear = (rear + 1) % capacity;
        arr[rear] = x;
        size++;
    }

    int dequeue() {
        if (size == 0) throw new RuntimeException("Queue is empty");
        int val = arr[front];
        front = (front + 1) % capacity;
        size--;
        return val;
    }
}
```

**Q: Sliding Window Maximum — classic Deque problem**
```java
int[] maxSlidingWindow(int[] nums, int k) {
    Deque<Integer> deque = new ArrayDeque<>();  // stores indices
    int[] result = new int[nums.length - k + 1];
    for (int i = 0; i < nums.length; i++) {
        while (!deque.isEmpty() && deque.peekFirst() <= i - k) deque.pollFirst();
        while (!deque.isEmpty() && nums[deque.peekLast()] < nums[i]) deque.pollLast();
        deque.offerLast(i);
        if (i >= k - 1) result[i - k + 1] = nums[deque.peekFirst()];
    }
    return result;
}
// nums=[1,3,-1,-3,5,3,6,7], k=3 -> [3,3,5,5,6,7]
```

**Q: Time complexity summary**
| Operation | LinkedList/ArrayDeque | PriorityQueue |
|---|---|---|
| offer/add | O(1) | O(log n) |
| poll/remove | O(1) | O(log n) |
| peek/element | O(1) | O(1) |
| contains | O(n) | O(n) |

---

## 8. Full Working Example (Interview-style demo)

```java
import java.util.*;

public class QueueDemo {
    public static void main(String[] args) {
        // FIFO Queue
        Queue<Integer> queue = new ArrayDeque<>();
        queue.offer(1); queue.offer(2); queue.offer(3);
        System.out.println("Queue: " + queue);
        System.out.println("Poll: " + queue.poll());
        System.out.println("Peek: " + queue.peek());

        // Deque as both ends
        Deque<Integer> deque = new ArrayDeque<>();
        deque.offerFirst(10);
        deque.offerLast(20);
        System.out.println("\nDeque: " + deque);

        // PriorityQueue (min-heap)
        PriorityQueue<Integer> minHeap = new PriorityQueue<>(List.of(5, 1, 8, 3));
        System.out.print("\nMin-heap poll order: ");
        while (!minHeap.isEmpty()) System.out.print(minHeap.poll() + " ");
        System.out.println();

        // PriorityQueue (max-heap)
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        maxHeap.addAll(List.of(5, 1, 8, 3));
        System.out.print("Max-heap poll order: ");
        while (!maxHeap.isEmpty()) System.out.print(maxHeap.poll() + " ");
        System.out.println();

        // Circular Queue
        CircularQueue cq = new CircularQueue(3);
        cq.enqueue(1); cq.enqueue(2); cq.enqueue(3);
        System.out.println("\nCircular dequeue: " + cq.dequeue());
        cq.enqueue(4);   // reuses freed slot
        System.out.println("Circular dequeue: " + cq.dequeue());
    }

    static class CircularQueue {
        int[] arr; int front, rear, size, capacity;
        CircularQueue(int capacity) {
            this.capacity = capacity;
            arr = new int[capacity];
            front = 0; rear = -1; size = 0;
        }
        void enqueue(int x) {
            rear = (rear + 1) % capacity;
            arr[rear] = x;
            size++;
        }
        int dequeue() {
            int val = arr[front];
            front = (front + 1) % capacity;
            size--;
            return val;
        }
    }
}
```

**Output:**
```
Queue: [1, 2, 3]
Poll: 1
Peek: 2

Deque: [10, 20]

Min-heap poll order: 1 3 5 8 
Max-heap poll order: 8 5 3 1 

Circular dequeue: 1
Circular dequeue: 2
```

---

## 9. Quick Reference Table (memorize for interviews)

| Method | Class | Purpose |
|---|---|---|
| `offer` / `add` | Queue | Insert at tail |
| `poll` / `remove` | Queue | Remove & return head |
| `peek` / `element` | Queue | View head without removing |
| `offerFirst`/`offerLast`/`pollFirst`/`pollLast`/`peekFirst`/`peekLast` | Deque | Insert/remove/view at either end |
| `push` / `pop` | Deque (as Stack) | LIFO ops (maps to First variants) |
| `put` / `take` | BlockingQueue | Blocking insert/remove (threads wait) |
| Natural order / Comparator | PriorityQueue | Determines poll order (heap-based) |

> **Interview tip:** Always be ready to state the **ArrayDeque > LinkedList > Stack** recommendation for stack/queue use, explain **PriorityQueue's heap internals (O(log n))**, and code at least one of: **circular queue**, **sliding window maximum**, or **stack-using-queues** — these are the most repeated queue-based interview questions.
