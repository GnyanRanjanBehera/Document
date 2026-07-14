# Java ArrayList — Interview Cheat Sheet

`ArrayList<E>` is a resizable array implementation of the `List` interface. Allows duplicates, maintains insertion order, allows multiple `null` values.

```java
import java.util.*;
```

---

## 1. List Hierarchy (Interview favorite question)

```
Collection (interface)
    └── List (interface)
          ├── ArrayList     (resizable array, fast random access)
          ├── LinkedList    (doubly linked list, fast insert/delete)
          └── Vector        (legacy, synchronized)
                └── Stack   (LIFO, extends Vector)
```

| Type | Backed by | Access | Insert/Delete (middle) | Thread-safe |
|---|---|---|---|---|
| `ArrayList` | Dynamic array | O(1) | O(n) | No |
| `LinkedList` | Doubly linked list | O(n) | O(1) | No |
| `Vector` | Dynamic array | O(1) | O(n) | Yes |

---

## 2. All ArrayList Methods

### `add(E e)` — Append element
```java
List<String> list = new ArrayList<>();
list.add("Apple");
list.add("Banana");
```

### `add(int index, E element)` — Insert at specific position
```java
list.add(1, "Mango");   // [Apple, Mango, Banana]
```

### `addAll(Collection<? extends E> c)` — Add all elements
```java
list.addAll(Arrays.asList("Cherry", "Grapes"));
```

### `addAll(int index, Collection<? extends E> c)` — Insert collection at index
```java
list.addAll(1, Arrays.asList("X", "Y"));
```

### `get(int index)` — Retrieve element
```java
String fruit = list.get(0);   // Apple
```

### `set(int index, E element)` — Replace element at index
```java
list.set(0, "Kiwi");
```

### `remove(int index)` — Remove by index
```java
list.remove(0);
```

### `remove(Object o)` — Remove by value (first occurrence)
```java
list.remove("Banana");
```

### `removeIf(Predicate<? super E> filter)` — Conditional removal
```java
list.removeIf(s -> s.startsWith("M"));
```

### `removeAll(Collection<?> c)` — Remove all matching elements
```java
list.removeAll(Arrays.asList("X", "Y"));
```

### `retainAll(Collection<?> c)` — Keep only matching elements (intersection)
```java
list.retainAll(Arrays.asList("Apple", "Cherry"));
```

### `clear()` — Remove all elements
```java
list.clear();
```

### `contains(Object o)` — Check existence
```java
boolean has = list.contains("Apple");
```

### `containsAll(Collection<?> c)` — Check if all elements present
```java
boolean hasAll = list.containsAll(Arrays.asList("Apple", "Banana"));
```

### `indexOf(Object o)` — First index of element (-1 if not found)
```java
int idx = list.indexOf("Banana");
```

### `lastIndexOf(Object o)` — Last index of element
```java
int idx = list.lastIndexOf("Banana");
```

### `size()` — Number of elements
```java
int n = list.size();
```

### `isEmpty()`
```java
boolean empty = list.isEmpty();
```

### `iterator()` — Forward iterator
```java
Iterator<String> it = list.iterator();
while (it.hasNext()) System.out.println(it.next());
```

### `listIterator()` — Bidirectional iterator (List-specific)
```java
ListIterator<String> lit = list.listIterator();
while (lit.hasNext()) System.out.println(lit.next());
while (lit.hasPrevious()) System.out.println(lit.previous());
```

### `forEach(Consumer<? super E> action)`
```java
list.forEach(System.out::println);
```

### `stream()` / `parallelStream()` — Java 8 Stream API
```java
list.stream().filter(s -> s.length() > 5).forEach(System.out::println);
```

### `sort(Comparator<? super E> c)` — Sort in place
```java
list.sort(Comparator.naturalOrder());
list.sort(Comparator.reverseOrder());
list.sort((a, b) -> a.length() - b.length());
```

### `replaceAll(UnaryOperator<E> operator)` — Transform all elements in place
```java
list.replaceAll(String::toUpperCase);
```

### `subList(int fromIndex, int toIndex)` — View of portion of list
```java
List<String> sub = list.subList(1, 3);   // [index1, index2]
```

### `toArray()` — Convert to array
```java
Object[] arr = list.toArray();
String[] strArr = list.toArray(new String[0]);
```

### `equals(Object o)` — Compare lists (order matters, unlike Set)
```java
List<String> l1 = List.of("A", "B");
List<String> l2 = List.of("A", "B");
System.out.println(l1.equals(l2));  // true
```

### `hashCode()`
```java
int h = list.hashCode();
```

### `clone()` — Shallow copy (ArrayList-specific, not in List interface)
```java
ArrayList<String> original = new ArrayList<>(List.of("A", "B"));
ArrayList<String> copy = (ArrayList<String>) original.clone();
```

### `ensureCapacity(int minCapacity)` — Pre-allocate internal array size (performance tuning)
```java
ArrayList<Integer> nums = new ArrayList<>();
nums.ensureCapacity(1000);   // avoid repeated resizing
```

### `trimToSize()` — Shrink internal array to current size (free memory)
```java
nums.trimToSize();
```

---

## 3. Creating Lists — Common Patterns

```java
// Immutable list (Java 9+)
List<String> immutable = List.of("A", "B", "C");

// From existing collection
List<Integer> fromSet = new ArrayList<>(Set.of(1, 2, 3));

// Fixed-size list backed by array
List<String> fixed = Arrays.asList("A", "B", "C");   // can set(), can't add/remove

// Thread-safe list
List<String> syncList = Collections.synchronizedList(new ArrayList<>());

// With initial capacity (performance optimization)
List<Integer> withCap = new ArrayList<>(100);
```

---

## 4. Interview Q&A Quick Hits

**Q: How does ArrayList grow internally?**
A: Backed by an array. When it's full, a new array of **1.5x** the current capacity is created (default initial capacity is 10), and old elements are copied over — an O(n) operation, though it's amortized O(1) per add.

**Q: ArrayList vs LinkedList — when to use which?**
- `ArrayList`: fast random access (`get`), use when reads > writes, or index-based access needed.
- `LinkedList`: fast insertion/deletion at ends or middle (via iterator), use when frequent insert/delete, implements `Deque` too (can be used as stack/queue).

**Q: ArrayList vs Vector?**
A: Functionally similar (both array-backed), but `Vector` is synchronized (thread-safe, slower), `ArrayList` is not. `Vector` grows by 100% (doubles), `ArrayList` grows by 50%.

**Q: Can ArrayList contain null?**
A: Yes, multiple `null` values allowed.

**Q: Is ArrayList thread-safe? How to make it thread-safe?**
A: Not thread-safe by default. Use `Collections.synchronizedList()` or `CopyOnWriteArrayList` (from `java.util.concurrent`, best for read-heavy scenarios).

**Q: What's the difference between `Arrays.asList()` and `new ArrayList<>()`?**
A: `Arrays.asList()` returns a fixed-size list backed by the array — you can `set()` elements but `add()`/`remove()` throws `UnsupportedOperationException`.
```java
List<String> fixed = Arrays.asList("A", "B");
fixed.set(0, "Z");     // OK
fixed.add("C");        // throws UnsupportedOperationException
```

**Q: How to avoid `ConcurrentModificationException` while removing elements during iteration?**
A: Use `Iterator.remove()`, not `list.remove()` directly, or use `removeIf()`.
```java
Iterator<Integer> it = list.iterator();
while (it.hasNext()) {
    if (it.next() % 2 == 0) it.remove();   // safe removal
}
```
Wrong way (throws exception):
```java
for (Integer i : list) {
    if (i % 2 == 0) list.remove(i);   // ConcurrentModificationException
}
```

**Q: Time complexity summary**
| Operation | Complexity |
|---|---|
| `get(index)` | O(1) |
| `add(element)` (at end) | O(1) amortized |
| `add(index, element)` (middle) | O(n) |
| `remove(index)` | O(n) |
| `contains`, `indexOf` | O(n) |
| `sort` | O(n log n) |

**Q: How to remove duplicates from an ArrayList?**
```java
List<Integer> withDup = Arrays.asList(1, 2, 2, 3, 3, 3);
List<Integer> unique = new ArrayList<>(new LinkedHashSet<>(withDup));
System.out.println(unique);   // [1, 2, 3]
```

**Q: How to reverse an ArrayList?**
```java
Collections.reverse(list);
```

**Q: How to find max/min in an ArrayList?**
```java
Integer max = Collections.max(numList);
Integer min = Collections.min(numList);
```

**Q: How to sort a list of custom objects?**
```java
class Employee {
    String name; int salary;
    Employee(String name, int salary) { this.name = name; this.salary = salary; }
}

List<Employee> employees = new ArrayList<>();
// sort by salary ascending
employees.sort(Comparator.comparingInt(e -> e.salary));
// sort by salary descending, then name ascending
employees.sort(Comparator.comparingInt((Employee e) -> e.salary).reversed()
                          .thenComparing(e -> e.name));
```

---

## 5. Full Working Example (Interview-style demo)

```java
import java.util.*;
import java.util.stream.Collectors;

public class ArrayListDemo {
    public static void main(String[] args) {
        List<Integer> nums = new ArrayList<>(Arrays.asList(5, 3, 8, 3, 1, 9, 5));

        System.out.println("Original: " + nums);

        // Remove duplicates, preserve order
        List<Integer> unique = new ArrayList<>(new LinkedHashSet<>(nums));
        System.out.println("Unique: " + unique);

        // Sort ascending
        Collections.sort(unique);
        System.out.println("Sorted: " + unique);

        // Reverse
        Collections.reverse(unique);
        System.out.println("Reversed: " + unique);

        // Max / Min
        System.out.println("Max: " + Collections.max(unique) + ", Min: " + Collections.min(unique));

        // Filter using streams
        List<Integer> evens = unique.stream()
                                     .filter(n -> n % 2 == 0)
                                     .collect(Collectors.toList());
        System.out.println("Evens: " + evens);

        // Safe removal during iteration
        Iterator<Integer> it = unique.iterator();
        while (it.hasNext()) {
            if (it.next() < 3) it.remove();
        }
        System.out.println("After removing < 3: " + unique);

        // subList view
        List<Integer> sub = unique.subList(0, Math.min(2, unique.size()));
        System.out.println("Sublist: " + sub);
    }
}
```

**Output (example — depends on random values above but deterministic here):**
```
Original: [5, 3, 8, 3, 1, 9, 5]
Unique: [5, 3, 8, 1, 9]
Sorted: [1, 3, 5, 8, 9]
Reversed: [9, 8, 5, 3, 1]
Max: 9, Min: 1
Evens: [8]
After removing < 3: [9, 8, 5, 3]
Sublist: [9, 8]
```

---

## 6. Quick Reference Table (memorize for interviews)

| Method | Category | Purpose |
|---|---|---|
| `add`, `add(index,e)`, `addAll` | Modify | Insert elements |
| `remove(index)`, `remove(obj)`, `removeIf`, `removeAll` | Modify | Delete elements |
| `retainAll` | Modify | Keep intersection |
| `set` | Modify | Replace element |
| `get`, `indexOf`, `lastIndexOf` | Query | Access/search |
| `contains`, `containsAll` | Query | Check existence |
| `size`, `isEmpty` | Query | Size info |
| `clear` | Modify | Empty list |
| `iterator`, `listIterator`, `forEach`, `stream` | Traverse | Iterate |
| `sort`, `replaceAll` | Transform | Sort/transform elements |
| `subList` | View | Portion of list |
| `toArray` | Convert | To array |
| `ensureCapacity`, `trimToSize` | Performance | Memory tuning |

> **Interview tip:** Be ready to explain **amortized O(1) add** — most `add()` calls are O(1), but occasionally the internal array must resize and copy (O(n)), which averages out to O(1) over many operations.
