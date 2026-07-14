# Java Set — Interview Cheat Sheet

`Set<E>` is a collection that stores **unique elements**, no duplicates allowed. It extends `Collection` interface.

```java
import java.util.*;
```

**Main implementations:** `HashSet`, `LinkedHashSet`, `TreeSet`

---

## 1. Set Hierarchy (Interview favorite question)

```
Collection (interface)
    └── Set (interface)
          ├── HashSet
          │     └── LinkedHashSet
          └── SortedSet (interface)
                └── NavigableSet (interface)
                      └── TreeSet
```

| Type | Order | Null allowed | Backed by | Time complexity |
|---|---|---|---|---|
| `HashSet` | No order | 1 null | HashMap | O(1) avg |
| `LinkedHashSet` | Insertion order | 1 null | LinkedHashMap | O(1) avg |
| `TreeSet` | Sorted order | No null (throws NPE) | TreeMap (Red-Black tree) | O(log n) |

---

## 2. All Set Methods (common to all implementations)

### `add(E e)` — Add element, returns boolean
```java
Set<String> set = new HashSet<>();
boolean added = set.add("Apple");   // true
boolean dup = set.add("Apple");     // false, duplicate ignored
```

### `addAll(Collection<? extends E> c)` — Add all elements from another collection
```java
Set<String> set2 = new HashSet<>(Arrays.asList("Banana", "Cherry"));
set.addAll(set2);
```

### `remove(Object o)` — Remove element
```java
set.remove("Apple");   // true if removed
```

### `removeAll(Collection<?> c)` — Remove all matching elements
```java
set.removeAll(set2);   // removes Banana, Cherry
```

### `removeIf(Predicate<? super E> filter)` — Remove based on condition
```java
set.removeIf(s -> s.startsWith("A"));
```

### `retainAll(Collection<?> c)` — Keep only elements present in given collection (intersection)
```java
set.retainAll(set2);  // set now only contains common elements
```

### `contains(Object o)` — Check if element exists
```java
boolean has = set.contains("Cherry");
```

### `containsAll(Collection<?> c)` — Check if all elements exist
```java
boolean hasAll = set.containsAll(set2);
```

### `size()` — Number of elements
```java
int n = set.size();
```

### `isEmpty()`
```java
boolean empty = set.isEmpty();
```

### `clear()` — Remove all elements
```java
set.clear();
```

### `iterator()` — Get iterator to traverse
```java
Iterator<String> it = set.iterator();
while (it.hasNext()) {
    System.out.println(it.next());
}
```

### `forEach(Consumer<? super E> action)`
```java
set.forEach(System.out::println);
```

### `stream()` — Convert to Stream (Java 8+)
```java
set.stream().filter(s -> s.length() > 3).forEach(System.out::println);
```

### `toArray()` — Convert to array
```java
Object[] arr = set.toArray();
String[] strArr = set.toArray(new String[0]);
```

### `equals(Object o)` — Compare two sets (order-independent for equality)
```java
Set<String> a = new HashSet<>(List.of("X", "Y"));
Set<String> b = new HashSet<>(List.of("Y", "X"));
System.out.println(a.equals(b));  // true
```

### `hashCode()`
```java
int h = set.hashCode();
```

---

## 3. TreeSet-specific methods (SortedSet / NavigableSet) — Very common in interviews

```java
TreeSet<Integer> ts = new TreeSet<>(Arrays.asList(50, 10, 40, 20, 30));
```

| Method | Purpose | Example |
|---|---|---|
| `first()` | Smallest element | `ts.first()` → 10 |
| `last()` | Largest element | `ts.last()` → 50 |
| `higher(e)` | Smallest element **strictly greater** than e | `ts.higher(20)` → 30 |
| `lower(e)` | Largest element **strictly less** than e | `ts.lower(20)` → 10 |
| `ceiling(e)` | Smallest element **>= e** | `ts.ceiling(25)` → 30 |
| `floor(e)` | Largest element **<= e** | `ts.floor(25)` → 20 |
| `pollFirst()` | Retrieve & remove smallest | `ts.pollFirst()` → 10 |
| `pollLast()` | Retrieve & remove largest | `ts.pollLast()` → 50 |
| `headSet(e)` | Elements strictly less than e | `ts.headSet(30)` → [10,20] |
| `tailSet(e)` | Elements >= e | `ts.tailSet(30)` → [30,40,50] |
| `subSet(from, to)` | Elements in range [from, to) | `ts.subSet(20,40)` → [20,30] |
| `descendingSet()` | Reverse-ordered view | `ts.descendingSet()` |
| `descendingIterator()` | Iterator in descending order | |

```java
System.out.println(ts.descendingSet());  // [50, 40, 30, 20, 10]
```

---

## 4. Creating Sets — Common patterns

```java
// Immutable set (Java 9+)
Set<String> immutable = Set.of("A", "B", "C");

// From existing collection
Set<Integer> fromList = new HashSet<>(List.of(1, 2, 3));

// Sorted with custom comparator
TreeSet<String> byLength = new TreeSet<>(Comparator.comparingInt(String::length));

// Thread-safe set
Set<String> syncSet = Collections.synchronizedSet(new HashSet<>());

// Concurrent (from java.util.concurrent)
Set<String> concurrentSet = ConcurrentHashMap.newKeySet();
```

---

## 5. Interview Q&A Quick Hits

**Q: How does HashSet ensure no duplicates?**
A: Internally backed by a `HashMap`; each element is stored as a key with a constant dummy value. Duplicates are rejected because map keys must be unique (uses `hashCode()` + `equals()`).

**Q: HashSet vs LinkedHashSet vs TreeSet — when to use which?**
- `HashSet`: fastest, use when order doesn't matter.
- `LinkedHashSet`: need predictable iteration (insertion) order.
- `TreeSet`: need sorted order or range queries.

**Q: Can HashSet contain null?**
A: Yes, one `null` element. `TreeSet` cannot (throws `NullPointerException` when natural ordering is used).

**Q: Is Set thread-safe?**
A: No, by default. Use `Collections.synchronizedSet()` or `ConcurrentHashMap.newKeySet()`.

**Q: How to remove duplicates from a List using Set?**
```java
List<Integer> list = Arrays.asList(1, 2, 2, 3, 3, 3);
Set<Integer> unique = new LinkedHashSet<>(list);  // preserves order too
List<Integer> result = new ArrayList<>(unique);
System.out.println(result);  // [1, 2, 3]
```

**Q: What's the time complexity of add/remove/contains in each?**
- `HashSet`: O(1) average, O(n) worst case (hash collisions)
- `LinkedHashSet`: O(1) average
- `TreeSet`: O(log n) always (Red-Black tree)

**Q: How does TreeSet sort custom objects?**
```java
class Person implements Comparable<Person> {
    String name; int age;
    Person(String name, int age) { this.name = name; this.age = age; }
    public int compareTo(Person p) { return this.age - p.age; }
}

TreeSet<Person> people = new TreeSet<>();
people.add(new Person("Alice", 30));
people.add(new Person("Bob", 25));
// sorted by age: Bob(25), Alice(30)
```
Or with a Comparator (no need to implement Comparable):
```java
TreeSet<Person> byName = new TreeSet<>(Comparator.comparing(p -> p.name));
```

**Q: Set operations — union, intersection, difference**
```java
Set<Integer> a = new HashSet<>(List.of(1, 2, 3, 4));
Set<Integer> b = new HashSet<>(List.of(3, 4, 5, 6));

// Union
Set<Integer> union = new HashSet<>(a);
union.addAll(b);            // [1,2,3,4,5,6]

// Intersection
Set<Integer> intersection = new HashSet<>(a);
intersection.retainAll(b);  // [3,4]

// Difference (a - b)
Set<Integer> difference = new HashSet<>(a);
difference.removeAll(b);    // [1,2]
```

---

## 6. Full Working Example (Interview-style demo)

```java
import java.util.*;

public class SetDemo {
    public static void main(String[] args) {
        Set<Integer> hashSet = new HashSet<>();
        hashSet.add(30); hashSet.add(10); hashSet.add(20); hashSet.add(10); // dup ignored

        Set<Integer> linkedSet = new LinkedHashSet<>(hashSet);
        Set<Integer> treeSet = new TreeSet<>(hashSet);

        System.out.println("HashSet (no order guarantee): " + hashSet);
        System.out.println("LinkedHashSet (insertion order): " + linkedSet);
        System.out.println("TreeSet (sorted): " + treeSet);

        TreeSet<Integer> ts = (TreeSet<Integer>) treeSet;
        System.out.println("First: " + ts.first() + ", Last: " + ts.last());
        System.out.println("Ceiling(15): " + ts.ceiling(15));
        System.out.println("Floor(15): " + ts.floor(15));

        // Remove duplicates from list example
        List<String> withDup = Arrays.asList("A", "B", "A", "C", "B");
        Set<String> noDup = new LinkedHashSet<>(withDup);
        System.out.println("Deduplicated: " + noDup);
    }
}
```

**Output:**
```
HashSet (no order guarantee): [20, 10, 30]
LinkedHashSet (insertion order): [20, 10, 30]
TreeSet (sorted): [10, 20, 30]
First: 10, Last: 30
Ceiling(15): 20
Floor(15): 10
Deduplicated: [A, B, C]
```

---

## 7. Quick Reference Table (memorize for interviews)

| Method | Category | Purpose |
|---|---|---|
| `add`, `addAll` | Modify | Insert elements |
| `remove`, `removeAll`, `removeIf` | Modify | Delete elements |
| `retainAll` | Modify | Keep intersection |
| `contains`, `containsAll` | Query | Check existence |
| `size`, `isEmpty` | Query | Size info |
| `clear` | Modify | Empty set |
| `iterator`, `forEach`, `stream` | Traverse | Iterate |
| `toArray` | Convert | To array |
| `first`, `last`, `higher`, `lower`, `ceiling`, `floor` | TreeSet only | Navigation |
| `pollFirst`, `pollLast` | TreeSet only | Remove smallest/largest |
| `headSet`, `tailSet`, `subSet` | TreeSet only | Range views |

> **Interview tip:** Be ready to explain **why HashSet lookup is O(1)** — it uses the element's `hashCode()` to find the bucket, then `equals()` to confirm the match within that bucket.
