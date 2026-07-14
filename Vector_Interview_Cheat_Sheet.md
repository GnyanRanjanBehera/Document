# Java Vector — Interview Cheat Sheet

`Vector<E>` is a legacy, **synchronized** (thread-safe), resizable array implementation of `List`. Introduced in JDK 1.0 (before the Collections Framework existed), later retrofitted to implement `List`.

```java
import java.util.*;
```

---

## 1. Hierarchy (Interview favorite question)

```
Collection (interface)
    └── List (interface)
          ├── ArrayList
          ├── LinkedList
          └── Vector                (synchronized, legacy)
                └── Stack           (LIFO, extends Vector)
```

| Type | Synchronized | Growth | Legacy? |
|---|---|---|---|
| `Vector` | Yes | Doubles (100%) capacity | Yes (JDK 1.0) |
| `ArrayList` | No | Grows by 50% | No (JDK 1.2+) |
| `Stack` | Yes (extends Vector) | Doubles | Yes |

> **Key interview line:** *"Vector is basically a thread-safe ArrayList, but every method is synchronized individually, which makes it slower — that's why ArrayList is generally preferred unless legacy code or built-in thread-safety is required."*

---

## 2. All Vector Methods

### `add(E e)` — Append element
```java
Vector<String> vec = new Vector<>();
vec.add("Apple");
vec.add("Banana");
```

### `add(int index, E element)` — Insert at position
```java
vec.add(1, "Mango");   // [Apple, Mango, Banana]
```

### `addElement(E obj)` — Legacy method, same as add (pre-Collections API)
```java
vec.addElement("Cherry");
```

### `addAll(Collection<? extends E> c)` — Add all elements
```java
vec.addAll(Arrays.asList("Grapes", "Kiwi"));
```

### `addAll(int index, Collection<? extends E> c)` — Insert collection at index
```java
vec.addAll(1, Arrays.asList("X", "Y"));
```

### `get(int index)` — Retrieve element
```java
String fruit = vec.get(0);
```

### `elementAt(int index)` — Legacy equivalent of `get()`
```java
String fruit2 = vec.elementAt(0);
```

### `firstElement()` — Legacy: get first element
```java
String first = vec.firstElement();
```

### `lastElement()` — Legacy: get last element
```java
String last = vec.lastElement();
```

### `set(int index, E element)` — Replace element at index
```java
vec.set(0, "Kiwi");
```

### `setElementAt(E obj, int index)` — Legacy equivalent of `set()`
```java
vec.setElementAt("Papaya", 0);
```

### `remove(int index)` — Remove by index
```java
vec.remove(0);
```

### `remove(Object o)` — Remove by value (first occurrence)
```java
vec.remove("Banana");
```

### `removeElement(Object obj)` — Legacy equivalent of `remove(Object)`
```java
vec.removeElement("Mango");
```

### `removeElementAt(int index)` — Legacy equivalent of `remove(int)`
```java
vec.removeElementAt(0);
```

### `removeAllElements()` — Legacy equivalent of `clear()`
```java
vec.removeAllElements();
```

### `removeIf(Predicate<? super E> filter)` — Conditional removal
```java
vec.removeIf(s -> s.startsWith("A"));
```

### `removeAll(Collection<?> c)` — Remove all matching elements
```java
vec.removeAll(Arrays.asList("X", "Y"));
```

### `retainAll(Collection<?> c)` — Keep only matching (intersection)
```java
vec.retainAll(Arrays.asList("Apple", "Cherry"));
```

### `clear()` — Remove all elements
```java
vec.clear();
```

### `contains(Object o)` — Check existence
```java
boolean has = vec.contains("Apple");
```

### `containsAll(Collection<?> c)` — Check if all elements present
```java
boolean hasAll = vec.containsAll(Arrays.asList("Apple", "Banana"));
```

### `indexOf(Object o)` — First index of element
```java
int idx = vec.indexOf("Banana");
```

### `lastIndexOf(Object o)` — Last index of element
```java
int idx = vec.lastIndexOf("Banana");
```

### `size()` — Number of elements currently stored
```java
int n = vec.size();
```

### `capacity()` — Current internal array size (Vector-specific, not in ArrayList!)
```java
int cap = vec.capacity();
```

### `isEmpty()`
```java
boolean empty = vec.isEmpty();
```

### `elements()` — Legacy Enumeration (pre-Iterator API)
```java
Enumeration<String> e = vec.elements();
while (e.hasMoreElements()) {
    System.out.println(e.nextElement());
}
```

### `iterator()` — Standard forward iterator
```java
Iterator<String> it = vec.iterator();
while (it.hasNext()) System.out.println(it.next());
```

### `listIterator()` — Bidirectional iterator
```java
ListIterator<String> lit = vec.listIterator();
```

### `forEach(Consumer<? super E> action)`
```java
vec.forEach(System.out::println);
```

### `stream()` — Java 8 Stream API
```java
vec.stream().filter(s -> s.length() > 5).forEach(System.out::println);
```

### `sort(Comparator<? super E> c)` — Sort in place
```java
vec.sort(Comparator.naturalOrder());
```

### `subList(int fromIndex, int toIndex)` — View of portion
```java
List<String> sub = vec.subList(1, 3);
```

### `toArray()` — Convert to array
```java
Object[] arr = vec.toArray();
String[] strArr = vec.toArray(new String[0]);
```

### `clone()` — Shallow copy
```java
Vector<String> copy = (Vector<String>) vec.clone();
```

### `equals(Object o)` / `hashCode()`
```java
Vector<String> v1 = new Vector<>(List.of("A", "B"));
Vector<String> v2 = new Vector<>(List.of("A", "B"));
System.out.println(v1.equals(v2));  // true
```

### `ensureCapacity(int minCapacity)` — Pre-allocate capacity
```java
vec.ensureCapacity(1000);
```

### `trimToSize()` — Shrink internal array to current size
```java
vec.trimToSize();
```

### `setSize(int newSize)` — Force resize (truncates or pads with null)
```java
vec.setSize(5);   // pads with nulls if smaller, truncates if larger
```

### `copyInto(Object[] anArray)` — Legacy: copy elements into given array
```java
String[] arr2 = new String[vec.size()];
vec.copyInto(arr2);
```

---

## 3. Creating Vectors — Common Patterns

```java
// Default (initial capacity 10)
Vector<String> v1 = new Vector<>();

// With initial capacity
Vector<String> v2 = new Vector<>(50);

// With initial capacity + capacity increment (legacy constructor)
Vector<String> v3 = new Vector<>(50, 10);   // grows by 10 (not doubling) when full

// From existing collection
Vector<String> v4 = new Vector<>(List.of("A", "B", "C"));
```

---

## 4. Interview Q&A Quick Hits

**Q: Vector vs ArrayList — the classic question**
| Aspect | Vector | ArrayList |
|---|---|---|
| Thread safety | Synchronized (every method) | Not synchronized |
| Performance | Slower (sync overhead) | Faster |
| Growth rate | Doubles (100%) capacity | Grows by 50% |
| Legacy API | Has `addElement`, `elementAt`, `Enumeration` | No legacy methods |
| Introduced | JDK 1.0 | JDK 1.2 (Collections Framework) |

**Q: Why is Vector considered "legacy" / rarely used today?**
A: Its synchronization locks **every single method call**, even in single-threaded contexts, which adds unnecessary overhead. Modern code prefers `ArrayList` (single-threaded) or `CopyOnWriteArrayList` / `Collections.synchronizedList()` (multi-threaded, more flexible locking).

**Q: Is Vector fully thread-safe for compound operations?**
A: No — individual method calls are synchronized, but compound actions (like "check-then-act", e.g. `if (!vec.contains(x)) vec.add(x)`) are **not atomic** and still need external synchronization:
```java
synchronized (vec) {
    if (!vec.contains("Apple")) {
        vec.add("Apple");
    }
}
```

**Q: What is `Enumeration` and how does it differ from `Iterator`?**
A: `Enumeration` is the legacy traversal interface (`hasMoreElements()`, `nextElement()`) used before `Iterator` existed. Unlike `Iterator`, it has **no `remove()` method** — you can't modify the collection while enumerating.

**Q: Vector vs Stack?**
A: `Stack` **extends** `Vector` and adds LIFO-specific methods: `push()`, `pop()`, `peek()`, `empty()`, `search()`. Since it inherits Vector's list methods too, it's not a "pure" stack — you can technically insert/access elements out of LIFO order, which is considered a design flaw. `Deque` (via `ArrayDeque`) is preferred for stack behavior today.
```java
Stack<Integer> stack = new Stack<>();
stack.push(1); stack.push(2); stack.push(3);
System.out.println(stack.pop());   // 3
System.out.println(stack.peek());  // 2
```

**Q: How does Vector grow internally?**
A: Default capacity is 10. When full, it **doubles** its capacity (`newCapacity = oldCapacity * 2`), unless a `capacityIncrement` was specified in the constructor, in which case it grows by that fixed amount instead. Contrast with `ArrayList`, which grows by 1.5x.

**Q: Time complexity summary**
| Operation | Complexity |
|---|---|
| `get(index)` | O(1) |
| `add(element)` (at end) | O(1) amortized |
| `add(index, element)` (middle) | O(n) |
| `remove(index)` | O(n) |
| `contains`, `indexOf` | O(n) |
| All operations | + synchronization overhead |

**Q: How to make ArrayList behave like a thread-safe Vector without using Vector?**
```java
List<String> syncList = Collections.synchronizedList(new ArrayList<>());
// or for read-heavy concurrent use:
List<String> cowList = new CopyOnWriteArrayList<>();
```

---

## 5. Full Working Example (Interview-style demo)

```java
import java.util.*;

public class VectorDemo {
    public static void main(String[] args) {
        Vector<Integer> vec = new Vector<>(Arrays.asList(5, 3, 8, 3, 1));

        System.out.println("Original: " + vec);
        System.out.println("Capacity: " + vec.capacity());

        vec.addElement(9);           // legacy add
        vec.add(0, 100);             // insert at index 0
        System.out.println("After adds: " + vec);

        System.out.println("First: " + vec.firstElement());
        System.out.println("Last: " + vec.lastElement());

        // Legacy Enumeration traversal
        Enumeration<Integer> en = vec.elements();
        System.out.print("Enumeration: ");
        while (en.hasMoreElements()) {
            System.out.print(en.nextElement() + " ");
        }
        System.out.println();

        Collections.sort(vec);
        System.out.println("Sorted: " + vec);

        vec.removeElementAt(0);
        System.out.println("After removeElementAt(0): " + vec);

        // Stack usage (Vector subclass)
        Stack<String> stack = new Stack<>();
        stack.push("A"); stack.push("B"); stack.push("C");
        System.out.println("Stack pop: " + stack.pop());   // C
        System.out.println("Stack after pop: " + stack);
    }
}
```

**Output:**
```
Original: [5, 3, 8, 3, 1]
Capacity: 5
After adds: [100, 5, 3, 8, 3, 1, 9]
First: 100
Last: 9
Enumeration: 100 5 3 8 3 1 9 
Sorted: [1, 3, 3, 5, 8, 9, 100]
After removeElementAt(0): [3, 3, 5, 8, 9, 100]
Stack pop: C
Stack after pop: [A, B]
```

---

## 6. Quick Reference Table (memorize for interviews)

| Method | Category | Purpose |
|---|---|---|
| `add`, `addElement`, `addAll` | Modify | Insert elements |
| `remove`, `removeElement`, `removeElementAt`, `removeAllElements`, `removeIf` | Modify | Delete elements |
| `set`, `setElementAt` | Modify | Replace element |
| `get`, `elementAt`, `firstElement`, `lastElement` | Query | Access elements |
| `indexOf`, `lastIndexOf`, `contains`, `containsAll` | Query | Search |
| `size`, `capacity`, `isEmpty` | Query | Size/capacity info |
| `clear` | Modify | Empty vector |
| `iterator`, `listIterator`, `elements`, `forEach`, `stream` | Traverse | Iteration (old + new style) |
| `sort` | Transform | Sort elements |
| `subList` | View | Portion of vector |
| `toArray`, `copyInto` | Convert | To array |
| `ensureCapacity`, `trimToSize`, `setSize` | Performance | Capacity tuning |

> **Interview tip:** The #1 question is always **"Vector vs ArrayList"** — lead with *synchronization* and *growth rate* as the two core differences, then mention Vector is rarely used in new code (prefer `ArrayList` + explicit synchronization when needed).
