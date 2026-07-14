# Java HashMap — All Methods with Examples

`HashMap<K, V>` stores key-value pairs, allows one `null` key and multiple `null` values, and does **not** guarantee order.

```java
import java.util.HashMap;
import java.util.Map;

HashMap<String, Integer> map = new HashMap<>();
```

---

## 1. `put(K key, V value)` — Add/Update entry
```java
map.put("Apple", 10);
map.put("Banana", 20);
map.put("Apple", 15);  // overwrites value to 15
```

## 2. `putIfAbsent(K key, V value)` — Add only if key is absent
```java
map.putIfAbsent("Apple", 100);  // no change, key exists
map.putIfAbsent("Cherry", 30);  // added
```

## 3. `get(Object key)` — Retrieve value
```java
int val = map.get("Apple");   // 15
```

## 4. `getOrDefault(Object key, V defaultValue)`
```java
int val = map.getOrDefault("Mango", 0);  // 0, key not present
```

## 5. `remove(Object key)` — Remove entry
```java
map.remove("Banana");
```

## 6. `remove(Object key, Object value)` — Remove only if key maps to given value
```java
map.remove("Apple", 999);  // false, value doesn't match — not removed
map.remove("Apple", 15);   // true, removed
```

## 7. `containsKey(Object key)`
```java
boolean hasKey = map.containsKey("Cherry");  // true
```

## 8. `containsValue(Object value)`
```java
boolean hasValue = map.containsValue(30);  // true
```

## 9. `size()` — Number of entries
```java
int n = map.size();
```

## 10. `isEmpty()`
```java
boolean empty = map.isEmpty();
```

## 11. `clear()` — Remove all entries
```java
map.clear();
```

## 12. `keySet()` — Set of all keys
```java
map.put("A", 1); map.put("B", 2);
for (String key : map.keySet()) {
    System.out.println(key);
}
```

## 13. `values()` — Collection of all values
```java
for (int val : map.values()) {
    System.out.println(val);
}
```

## 14. `entrySet()` — Set of key-value pairs
```java
for (Map.Entry<String, Integer> entry : map.entrySet()) {
    System.out.println(entry.getKey() + " = " + entry.getValue());
}
```

## 15. `replace(K key, V value)` — Replace value if key exists
```java
map.replace("A", 100);
```

## 16. `replace(K key, V oldValue, V newValue)` — Replace only if current value matches
```java
map.replace("A", 100, 200);  // replaces only if current value is 100
```

## 17. `replaceAll(BiFunction<K, V, V> function)` — Transform all values
```java
map.replaceAll((k, v) -> v * 10);
```

## 18. `merge(K key, V value, BiFunction<V,V,V> remap)` — Combine values
```java
map.put("A", 5);
map.merge("A", 10, Integer::sum);   // A -> 15
map.merge("Z", 10, Integer::sum);   // Z -> 10 (new key, uses value directly)
```

## 19. `compute(K key, BiFunction<K,V,V> remap)` — Compute new value from key+current value
```java
map.compute("A", (k, v) -> (v == null) ? 1 : v + 1);
```

## 20. `computeIfAbsent(K key, Function<K,V> mappingFunction)` — Compute only if key missing
```java
map.computeIfAbsent("NewKey", k -> 50);
```

## 21. `computeIfPresent(K key, BiFunction<K,V,V> remap)` — Compute only if key exists
```java
map.computeIfPresent("A", (k, v) -> v + 100);
```

## 22. `forEach(BiConsumer<K, V> action)` — Iterate with lambda
```java
map.forEach((k, v) -> System.out.println(k + " -> " + v));
```

## 23. `putAll(Map<? extends K, ? extends V> otherMap)` — Copy all entries from another map
```java
HashMap<String, Integer> map2 = new HashMap<>();
map2.put("X", 1);
map.putAll(map2);
```

## 24. `clone()` — Shallow copy of the map
```java
HashMap<String, Integer> copy = (HashMap<String, Integer>) map.clone();
```

## 25. `equals(Object o)` — Compare two maps for equal key-value mappings
```java
HashMap<String, Integer> m1 = new HashMap<>();
HashMap<String, Integer> m2 = new HashMap<>();
m1.put("A", 1); m2.put("A", 1);
System.out.println(m1.equals(m2));  // true
```

## 26. `hashCode()` — Hash code based on entries
```java
int hash = map.hashCode();
```

## 27. `toString()` — String representation
```java
System.out.println(map.toString());  // {A=15, B=2, ...}
```

---

## Full Working Example
```java
import java.util.HashMap;
import java.util.Map;

public class HashMapDemo {
    public static void main(String[] args) {
        HashMap<String, Integer> stock = new HashMap<>();

        stock.put("Apple", 50);
        stock.put("Banana", 30);
        stock.put("Cherry", 20);

        System.out.println("Initial map: " + stock);

        stock.putIfAbsent("Apple", 999);      // ignored, key exists
        stock.computeIfAbsent("Mango", k -> 15); // added

        stock.merge("Apple", 10, Integer::sum);  // Apple -> 60
        stock.compute("Banana", (k, v) -> v - 5); // Banana -> 25

        System.out.println("Contains 'Cherry'? " + stock.containsKey("Cherry"));
        System.out.println("Value of Mango: " + stock.getOrDefault("Mango", 0));

        stock.remove("Cherry");

        System.out.println("\nIterating entries:");
        for (Map.Entry<String, Integer> entry : stock.entrySet()) {
            System.out.println(entry.getKey() + " : " + entry.getValue());
        }

        System.out.println("\nSize: " + stock.size());
        System.out.println("Final map: " + stock);
    }
}
```

**Output:**
```
Initial map: {Banana=30, Apple=50, Cherry=20}
Contains 'Cherry'? true
Value of Mango: 15

Iterating entries:
Banana : 25
Apple : 60
Mango : 15

Size: 3
Final map: {Banana=25, Apple=60, Mango=15}
```

---

## Quick Reference Table

| Method | Purpose |
|---|---|
| `put` | Insert/update entry |
| `putIfAbsent` | Insert only if key absent |
| `get` | Fetch value |
| `getOrDefault` | Fetch value with fallback |
| `remove` | Delete entry |
| `containsKey` / `containsValue` | Check existence |
| `size` / `isEmpty` | Map info |
| `clear` | Empty the map |
| `keySet` / `values` / `entrySet` | Iteration views |
| `replace` / `replaceAll` | Update values |
| `merge` | Combine old & new value |
| `compute` / `computeIfAbsent` / `computeIfPresent` | Conditional computation |
| `forEach` | Lambda iteration |
| `putAll` | Merge another map |
| `clone` | Shallow copy |
| `equals` / `hashCode` / `toString` | Object methods |

> **Note:** HashMap is **not thread-safe**. For concurrent use, prefer `ConcurrentHashMap` or `Collections.synchronizedMap()`.
