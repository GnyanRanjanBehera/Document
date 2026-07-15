# Java LinkedList — Interview Cheat Sheet

`LinkedList<E>` is a **doubly linked list** implementation that implements both `List` and `Deque` interfaces. Allows duplicates, maintains insertion order, allows multiple `null` values. Can be used as a **List**, **Stack**, **Queue**, or **Deque**.

```java
import java.util.*;
```

---

## 1. Hierarchy (Interview favorite question)

```
Collection (interface)
    ├── List (interface)
    │     └── LinkedList
    └── Queue (interface)
          └── Deque (interface)
                └── LinkedList
```

`LinkedList` is unique — it's the **only** class that implements both `List` and `Deque`.

| Type | Backed by | Access | Insert/Delete (ends) | Insert/Delete (middle) |
|---|---|---|---|---|
| `ArrayList` | Dynamic array | O(1) | O(1) amortized (end only) | O(n) |
| `LinkedList` | Doubly linked list (nodes) | O(n) | O(1) | O(n) to find + O(1) to link |

> **Key interview line:** *"LinkedList trades fast random access for fast insertion/deletion, because each element is a node with prev/next pointers instead of a contiguous array."*

---

## 2. All LinkedList Methods

### List interface methods

#### `add(E e)` — Append element
```java
LinkedList<String> list = new LinkedList<>();
list.add("Apple");
list.add("Banana");
```

#### `add(int index, E element)` — Insert at position
```java
list.add(1, "Mango");   // [Apple, Mango, Banana]
```

#### `addAll(Collection<? extends E> c)` / `addAll(int index, Collection<? extends E> c)`
```java
list.addAll(Arrays.asList("Cherry", "Grapes"));
```

#### `get(int index)` — Retrieve element (traverses from nearest end)
```java
String fruit = list.get(0);
```

#### `set(int index, E element)` — Replace element at index
```java
list.set(0, "Kiwi");
```

#### `remove(int index)` — Remove by index
```java
list.remove(0);
```

#### `remove(Object o)` — Remove by value (first occurrence)
```java
list.remove("Banana");
```

#### `indexOf(Object o)` / `lastIndexOf(Object o)`
```java
int idx = list.indexOf("Mango");
```

#### `contains`, `containsAll`, `size`, `isEmpty`, `clear`, `removeIf`, `removeAll`, `retainAll`, `sort`, `replaceAll`, `subList`, `toArray`, `forEach`, `stream`, `iterator`, `listIterator`
Same behavior as `ArrayList` — see ArrayList cheat sheet for examples.

---

### Deque / Queue interface methods (the important part for interviews!)

#### `addFirst(E e)` — Insert at head
```java
list.addFirst("Start");
```

#### `addLast(E e)` — Insert at tail (same as `add`)
```java
list.addLast("End");
```

#### `offerFirst(E e)` / `offerLast(E e)` — Insert at head/tail, returns boolean (Deque style, never throws)
```java
list.offerFirst("Head");
list.offerLast("Tail");
```

#### `getFirst()` / `getLast()` — Retrieve without removing (throws exception if empty)
```java
String first = list.getFirst();
String last = list.getLast();
```

#### `peekFirst()` / `peekLast()` — Retrieve without removing (returns null if empty, safer)
```java
String first = list.peekFirst();
String last = list.peekLast();
```

#### `removeFirst()` / `removeLast()` — Remove & return (throws exception if empty)
```java
String removed = list.removeFirst();
```

#### `pollFirst()` / `pollLast()` — Remove & return (returns null if empty, safer)
```java
String polled = list.pollFirst();
```

#### `removeFirstOccurrence(Object o)` / `removeLastOccurrence(Object o)`
```java
list.removeFirstOccurrence("Apple");
```

---

### Queue-specific methods (FIFO usage)

```java
Queue<Integer> queue = new LinkedList<>();
```

| Method | Behavior on empty queue | Purpose |
|---|---|---|
| `offer(e)` | Returns false (never throws) | Add to tail |
| `add(e)` | Throws if capacity-restricted | Add to tail |
| `poll()` | Returns null | Remove & return head |
| `remove()` | Throws `NoSuchElementException` | Remove & return head |
| `peek()` | Returns null | Return head without removing |
| `element()` | Throws exception | Return head without removing |

```java
queue.offer(1); queue.offer(2); queue.offer(3);
System.out.println(queue.poll());    // 1 (FIFO)
System.out.println(queue.peek());    // 2
```

---

### Stack usage (LIFO, via Deque methods)

```java
Deque<Integer> stack = new LinkedList<>();
stack.push(1);   // same as addFirst
stack.push(2);
stack.push(3);
System.out.println(stack.pop());    // 3 (LIFO) — same as removeFirst
System.out.println(stack.peek());   // 2 — same as peekFirst
```

| Method | Equivalent |
|---|---|
| `push(e)` | `addFirst(e)` |
| `pop()` | `removeFirst()` |
| `peek()` | `peekFirst()` |

---

### Other LinkedList-specific / inherited methods

#### `descendingIterator()` — Iterate in reverse
```java
Iterator<String> it = list.descendingIterator();
while (it.hasNext()) System.out.println(it.next());
```

#### `clone()` — Shallow copy
```java
LinkedList<String> copy = (LinkedList<String>) list.clone();
```

#### `push(E e)` / `pop()` — Deque-inherited stack ops (shown above)

---

## 3. Creating LinkedLists — Common Patterns

```java
// Empty
LinkedList<String> l1 = new LinkedList<>();

// From existing collection
LinkedList<String> l2 = new LinkedList<>(List.of("A", "B", "C"));

// As a Queue
Queue<Integer> queue = new LinkedList<>();

// As a Deque
Deque<Integer> deque = new LinkedList<>();

// As a Stack (via Deque interface — preferred over java.util.Stack)
Deque<Integer> stack = new LinkedList<>();
```

---

## 4. Interview Q&A Quick Hits

**Q: LinkedList vs ArrayList — the classic question**
| Aspect | ArrayList | LinkedList |
|---|---|---|
| Internal structure | Dynamic array | Doubly linked list (nodes) |
| Random access (`get(i)`) | O(1) | O(n) |
| Insert/delete at ends | O(1) amortized | O(1) |
| Insert/delete in middle | O(n) (shifting) | O(n) to traverse, O(1) to link |
| Memory overhead | Lower (just array) | Higher (each node stores prev+next pointers) |
| Implements | `List` | `List` **and** `Deque` |

**Q: When would you actually choose LinkedList over ArrayList?**
A: When you do frequent insertions/deletions at the **beginning** or via an iterator in the **middle**, and rarely need random access by index. In practice, `ArrayDeque` often outperforms `LinkedList` even for queue/stack use cases due to better cache locality — so LinkedList is chosen less often than people expect.

**Q: Why can LinkedList implement both List and Deque?**
A: Internally it's just a chain of nodes with `prev`/`next` references and head/tail pointers — this structure naturally supports both index-based list operations (by traversing) and constant-time operations at both ends (Deque).

**Q: getFirst()/removeFirst() vs peekFirst()/pollFirst() — what's the difference?**
A: The `get`/`remove` versions **throw `NoSuchElementException`** if the list is empty. The `peek`/`poll` versions **return `null`** instead — safer for concurrent or uncertain-state code.

**Q: How would you implement a Stack using LinkedList instead of java.util.Stack?**
A: Use `Deque<T> stack = new LinkedList<>();` with `push()`/`pop()`/`peek()`. This is the **modern recommended approach** — the JDK docs themselves suggest `Deque` over the legacy `Stack` class.

**Q: How would you implement a Queue using LinkedList?**
```java
Queue<Integer> queue = new LinkedList<>();
queue.offer(1);
queue.offer(2);
System.out.println(queue.poll());  // 1, FIFO order
```

**Q: Is LinkedList thread-safe?**
A: No. Use `Collections.synchronizedList()` or `ConcurrentLinkedQueue`/`ConcurrentLinkedDeque` (from `java.util.concurrent`) for concurrent use.

**Q: Time complexity summary**
| Operation | Complexity |
|---|---|
| `addFirst`/`addLast`/`removeFirst`/`removeLast` | O(1) |
| `get(index)` | O(n) |
| `add(index, e)` / `remove(index)` | O(n) (traversal) |
| `contains`, `indexOf` | O(n) |
| `push`/`pop`/`peek` (as stack) | O(1) |
| `offer`/`poll` (as queue) | O(1) |

---

## 5. Full Working Example (Interview-style demo)

```java
import java.util.*;

public class LinkedListDemo {
    public static void main(String[] args) {
        LinkedList<Integer> list = new LinkedList<>(Arrays.asList(2, 4, 6));

        // As a List
        list.add(1, 3);
        System.out.println("As List: " + list);

        // As a Deque
        list.addFirst(0);
        list.addLast(8);
        System.out.println("After addFirst/addLast: " + list);
        System.out.println("First: " + list.getFirst() + ", Last: " + list.getLast());

        // As a Queue (FIFO)
        Queue<Integer> queue = new LinkedList<>(list);
        System.out.println("Queue poll (FIFO): " + queue.poll());
        System.out.println("Queue after poll: " + queue);

        // As a Stack (LIFO)
        Deque<Integer> stack = new LinkedList<>(list);
        System.out.println("Stack pop (LIFO): " + stack.pop());
        System.out.println("Stack after pop: " + stack);

        // Descending iteration
        System.out.print("Descending: ");
        Iterator<Integer> desc = list.descendingIterator();
        while (desc.hasNext()) System.out.print(desc.next() + " ");
        System.out.println();
    }
}
```

**Output:**
```
As List: [2, 3, 4, 6]
After addFirst/addLast: [0, 2, 3, 4, 6, 8]
First: 0, Last: 8
Queue poll (FIFO): 0
Queue after poll: [2, 3, 4, 6, 8]
Stack pop (LIFO): 0
Stack after pop: [2, 3, 4, 6, 8]
Descending: 8 6 4 3 2 0 
```

---

## 6. Quick Reference Table (memorize for interviews)

| Method | Category | Purpose |
|---|---|---|
| `add`, `add(index,e)`, `addAll` | List | Insert elements |
| `get`, `set`, `indexOf`, `lastIndexOf` | List | Access/search by index |
| `remove(index)`, `remove(obj)`, `removeIf` | List | Delete elements |
| `addFirst`, `addLast`, `offerFirst`, `offerLast` | Deque | Insert at ends |
| `getFirst`, `getLast`, `peekFirst`, `peekLast` | Deque | Peek at ends (no removal) |
| `removeFirst`, `removeLast`, `pollFirst`, `pollLast` | Deque | Remove from ends |
| `push`, `pop`, `peek` | Stack (via Deque) | LIFO ops |
| `offer`, `poll`, `peek`, `element` | Queue | FIFO ops |
| `descendingIterator` | Traverse | Reverse iteration |
| `size`, `isEmpty`, `clear`, `contains` | Query | Basic info |

> **Interview tip:** If asked to "implement a stack or queue in Java," always mention **`Deque` (via `ArrayDeque` or `LinkedList`) is the modern recommended choice** over the legacy `Stack`/direct `LinkedList`-as-Queue pattern — but knowing LinkedList's dual nature (List + Deque) is exactly what interviewers are testing for.

---

## 7. Custom Implementations from Scratch (DSA Interview Questions)

`java.util.LinkedList` is already a doubly linked list under the hood, but interviewers frequently ask you to **implement these structures manually** using a `Node` class — this section covers that.

### 7.1 Singly Linked List (baseline, for comparison)

```java
class Node {
    int data;
    Node next;
    Node(int data) { this.data = data; }
}

class SinglyLinkedList {
    Node head;

    void insertAtEnd(int data) {
        Node newNode = new Node(data);
        if (head == null) { head = newNode; return; }
        Node curr = head;
        while (curr.next != null) curr = curr.next;
        curr.next = newNode;
    }

    void insertAtStart(int data) {
        Node newNode = new Node(data);
        newNode.next = head;
        head = newNode;
    }

    void deleteValue(int value) {
        if (head == null) return;
        if (head.data == value) { head = head.next; return; }
        Node curr = head;
        while (curr.next != null && curr.next.data != value) curr = curr.next;
        if (curr.next != null) curr.next = curr.next.next;
    }

    void display() {
        Node curr = head;
        while (curr != null) {
            System.out.print(curr.data + " -> ");
            curr = curr.next;
        }
        System.out.println("null");
    }
}
```

---

### 7.2 Doubly Linked List (DLL) — custom implementation

Each node has **both** `next` and `prev` pointers, allowing traversal in both directions.

```java
class DNode {
    int data;
    DNode prev, next;
    DNode(int data) { this.data = data; }
}

class DoublyLinkedList {
    DNode head, tail;

    void insertAtEnd(int data) {
        DNode newNode = new DNode(data);
        if (head == null) { head = tail = newNode; return; }
        tail.next = newNode;
        newNode.prev = tail;
        tail = newNode;
    }

    void insertAtStart(int data) {
        DNode newNode = new DNode(data);
        if (head == null) { head = tail = newNode; return; }
        newNode.next = head;
        head.prev = newNode;
        head = newNode;
    }

    void deleteValue(int value) {
        DNode curr = head;
        while (curr != null && curr.data != value) curr = curr.next;
        if (curr == null) return;                     // not found

        if (curr.prev != null) curr.prev.next = curr.next;
        else head = curr.next;                          // deleting head

        if (curr.next != null) curr.next.prev = curr.prev;
        else tail = curr.prev;                           // deleting tail
    }

    void displayForward() {
        DNode curr = head;
        while (curr != null) {
            System.out.print(curr.data + " <-> ");
            curr = curr.next;
        }
        System.out.println("null");
    }

    void displayBackward() {
        DNode curr = tail;
        while (curr != null) {
            System.out.print(curr.data + " <-> ");
            curr = curr.prev;
        }
        System.out.println("null");
    }
}
```

**Usage:**
```java
DoublyLinkedList dll = new DoublyLinkedList();
dll.insertAtEnd(10);
dll.insertAtEnd(20);
dll.insertAtStart(5);
dll.displayForward();   // 5 <-> 10 <-> 20 <-> null
dll.displayBackward();  // 20 <-> 10 <-> 5 <-> null
dll.deleteValue(10);
dll.displayForward();   // 5 <-> 20 <-> null
```

**DLL vs Singly Linked List:**
| Aspect | Singly | Doubly |
|---|---|---|
| Pointers per node | 1 (`next`) | 2 (`next`, `prev`) |
| Backward traversal | Not possible | Possible |
| Memory per node | Lower | Higher (extra pointer) |
| Delete given only a node reference | O(n) (need prev) | O(1) (prev is known) |

---

### 7.3 Circular Linked List (CLL) — singly, circular

Same as a singly linked list, but the **last node points back to the head** instead of `null`.

```java
class CNode {
    int data;
    CNode next;
    CNode(int data) { this.data = data; }
}

class CircularLinkedList {
    CNode head, tail;

    void insertAtEnd(int data) {
        CNode newNode = new CNode(data);
        if (head == null) {
            head = tail = newNode;
            tail.next = head;              // points to itself
            return;
        }
        tail.next = newNode;
        tail = newNode;
        tail.next = head;                  // close the circle
    }

    void insertAtStart(int data) {
        CNode newNode = new CNode(data);
        if (head == null) {
            head = tail = newNode;
            tail.next = head;
            return;
        }
        newNode.next = head;
        head = newNode;
        tail.next = head;                  // re-close the circle
    }

    void deleteValue(int value) {
        if (head == null) return;

        if (head == tail && head.data == value) { head = tail = null; return; }

        if (head.data == value) {
            head = head.next;
            tail.next = head;               // re-close the circle
            return;
        }

        CNode curr = head;
        while (curr.next != head && curr.next.data != value) curr = curr.next;
        if (curr.next.data == value) {
            if (curr.next == tail) tail = curr;
            curr.next = curr.next.next;
        }
    }

    void display() {
        if (head == null) { System.out.println("Empty list"); return; }
        CNode curr = head;
        do {
            System.out.print(curr.data + " -> ");
            curr = curr.next;
        } while (curr != head);
        System.out.println("(back to head)");
    }
}
```

**Usage:**
```java
CircularLinkedList cll = new CircularLinkedList();
cll.insertAtEnd(1);
cll.insertAtEnd(2);
cll.insertAtEnd(3);
cll.display();          // 1 -> 2 -> 3 -> (back to head)
cll.deleteValue(2);
cll.display();          // 1 -> 3 -> (back to head)
```

> **Common interview use case:** Circular linked lists model the **Josephus problem** and round-robin scheduling (e.g., CPU task scheduling, multiplayer turn order).

**Detecting if a list is circular (Floyd's Cycle Detection / "tortoise and hare"):**
```java
boolean hasCycle(CNode head) {
    if (head == null) return false;
    CNode slow = head, fast = head;
    while (fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;
        if (slow == fast) return true;   // cycle detected
    }
    return false;
}
```

---

### 7.4 Circular Doubly Linked List (CDLL) — combines both

Each node has `next`/`prev`, and the list wraps around: `tail.next == head` and `head.prev == tail`.

```java
class CDNode {
    int data;
    CDNode prev, next;
    CDNode(int data) { this.data = data; }
}

class CircularDoublyLinkedList {
    CDNode head, tail;

    void insertAtEnd(int data) {
        CDNode newNode = new CDNode(data);
        if (head == null) {
            head = tail = newNode;
            head.next = head;
            head.prev = head;
            return;
        }
        newNode.prev = tail;
        newNode.next = head;
        tail.next = newNode;
        head.prev = newNode;
        tail = newNode;
    }

    void insertAtStart(int data) {
        CDNode newNode = new CDNode(data);
        if (head == null) { insertAtEnd(data); return; }
        newNode.next = head;
        newNode.prev = tail;
        head.prev = newNode;
        tail.next = newNode;
        head = newNode;
    }

    void deleteValue(int value) {
        if (head == null) return;

        if (head == tail && head.data == value) { head = tail = null; return; }

        CDNode curr = head;
        do {
            if (curr.data == value) {
                curr.prev.next = curr.next;
                curr.next.prev = curr.prev;
                if (curr == head) head = curr.next;
                if (curr == tail) tail = curr.prev;
                return;
            }
            curr = curr.next;
        } while (curr != head);
    }

    void displayForward() {
        if (head == null) { System.out.println("Empty list"); return; }
        CDNode curr = head;
        do {
            System.out.print(curr.data + " <-> ");
            curr = curr.next;
        } while (curr != head);
        System.out.println("(back to head)");
    }

    void displayBackward() {
        if (tail == null) { System.out.println("Empty list"); return; }
        CDNode curr = tail;
        do {
            System.out.print(curr.data + " <-> ");
            curr = curr.prev;
        } while (curr != tail);
        System.out.println("(back to tail)");
    }
}
```

**Usage:**
```java
CircularDoublyLinkedList cdll = new CircularDoublyLinkedList();
cdll.insertAtEnd(10);
cdll.insertAtEnd(20);
cdll.insertAtEnd(30);
cdll.displayForward();    // 10 <-> 20 <-> 30 <-> (back to head)
cdll.displayBackward();   // 30 <-> 20 <-> 10 <-> (back to tail)
cdll.deleteValue(20);
cdll.displayForward();    // 10 <-> 30 <-> (back to head)
```

> **Real-world use case:** CDLL is used to implement things like **music playlist "next/previous" with looping**, browser tab cycling (Ctrl+Tab), and the `LinkedHashMap`-based LRU cache structures (conceptually similar).

---

### 7.5 Comparison Table — All Four Variants

| Type | Traversal | Last node points to | First node's prev points to | Use case |
|---|---|---|---|---|
| Singly Linked List | Forward only | `null` | N/A | Simple sequences, stacks |
| Doubly Linked List | Both directions | `null` | `null` | Browser history (back/forward), `java.util.LinkedList` |
| Circular Linked List | Forward only, wraps | `head` | N/A | Round-robin scheduling, Josephus problem |
| Circular Doubly Linked List | Both directions, wraps | `head` | `tail` | Playlist looping, tab cycling |

### 7.6 Interview Q&A — DSA-specific

**Q: How do you detect a cycle in a linked list?**
A: Floyd's Cycle Detection Algorithm ("tortoise and hare") — two pointers moving at speeds 1 and 2; if they ever meet, there's a cycle. O(n) time, O(1) space. (Code shown in section 7.3.)

**Q: How is a Circular Linked List different from detecting a cycle bug in a normal list?**
A: A Circular Linked List is **intentionally** designed so the tail links back to the head. A "cycle" in a normal linked list is usually a **bug** (accidentally created loop) that breaks traversal (`while (curr != null)` becomes infinite) — detected and fixed using the same tortoise-and-hare technique.

**Q: How do you reverse a Doubly Linked List?**
```java
void reverse(DoublyLinkedList list) {
    DNode curr = list.head;
    DNode temp = null;
    while (curr != null) {
        temp = curr.prev;
        curr.prev = curr.next;
        curr.next = temp;
        curr = curr.prev;             // move to next (which is now prev)
    }
    if (temp != null) list.head = temp.prev;
    DNode oldHead = list.head;
    list.head = list.tail;
    list.tail = oldHead;
}
```

**Q: Why does `java.util.LinkedList` not need a "circular" variant built in?**
A: Because `Deque` already provides O(1) access to both ends (`getFirst`/`getLast`), which covers most use cases a circular structure would solve — circularity is typically only needed for custom round-robin logic, which you implement yourself using the modulo operator or a manual circular structure like above.

---
