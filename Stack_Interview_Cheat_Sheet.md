# Java Stack — Interview Cheat Sheet

A **Stack** is a LIFO (**Last In, First Out**) data structure. In Java, `java.util.Stack` is a legacy class, but modern code typically uses `Deque` (via `ArrayDeque`) instead. This sheet covers both the built-in classes **and** custom from-scratch implementations (common in DSA interviews).

```java
import java.util.*;
```

---

## 1. Hierarchy (Interview favorite question)

```
Collection (interface)
    ├── List (interface)
    │     └── Vector
    │           └── Stack             (legacy, extends Vector)
    └── Queue (interface)
          └── Deque (interface)
                ├── ArrayDeque        (modern, preferred for stack use)
                └── LinkedList
```

| Implementation | Thread-safe | Backed by | Recommended? |
|---|---|---|---|
| `java.util.Stack` | Yes (synchronized, inherited from Vector) | Dynamic array | No — legacy |
| `ArrayDeque` (as stack) | No | Resizable array | **Yes** — modern standard |
| `LinkedList` (as stack) | No | Doubly linked list | Yes, when node-based semantics help |

> **Key interview line:** *"`java.util.Stack` extends `Vector`, which means it inherits List methods like `get(index)` and `add(index, e)` — letting you break LIFO order by accident. The JDK docs themselves recommend `Deque` (via `ArrayDeque`) as a faster, non-synchronized, purpose-built alternative."*

---

## 2. `java.util.Stack` — All Methods

### `push(E item)` — Add to top
```java
Stack<Integer> stack = new Stack<>();
stack.push(10);
stack.push(20);
stack.push(30);
```

### `pop()` — Remove & return top element (throws `EmptyStackException` if empty)
```java
int top = stack.pop();   // 30
```

### `peek()` — View top element without removing (throws exception if empty)
```java
int top = stack.peek();  // 20
```

### `empty()` — Check if stack is empty
```java
boolean isEmpty = stack.empty();
```

### `search(Object o)` — Returns **1-based position from the top**, or -1 if not found
```java
stack.push(10); stack.push(20); stack.push(30);
int pos = stack.search(20);   // 2 (20 is 2nd from top)
int notFound = stack.search(99);  // -1
```

### Inherited from Vector/List (use with caution — breaks pure LIFO semantics)
```java
stack.get(0);            // access by index, bypasses LIFO
stack.add(1, 99);         // insert in middle, bypasses LIFO
stack.size();
stack.isEmpty();
stack.contains(20);
stack.remove(Integer.valueOf(20));   // removes by value, not just top
stack.clear();
stack.iterator();
stack.forEach(System.out::println);
```

---

## 3. `Deque` as Stack — Modern Recommended Approach

```java
Deque<Integer> stack = new ArrayDeque<>();
```

| Stack operation | Deque method used | Behavior on empty |
|---|---|---|
| Push | `push(e)` → `addFirst(e)` | N/A |
| Pop | `pop()` → `removeFirst()` | Throws `NoSuchElementException` |
| Peek | `peek()` → `peekFirst()` | Returns `null` |

```java
stack.push(10);
stack.push(20);
stack.push(30);

System.out.println(stack.pop());   // 30 (LIFO)
System.out.println(stack.peek());  // 20
System.out.println(stack.isEmpty()); // false
```

> **Why `ArrayDeque` is preferred:** No synchronization overhead (faster than `Stack`), no legacy List methods to accidentally misuse, and better cache locality than `LinkedList`.

---

## 4. Custom Stack Implementation from Scratch (DSA Interview Staple)

### 4.1 Array-based Stack

```java
class ArrayStack {
    private int[] arr;
    private int top;
    private int capacity;

    ArrayStack(int size) {
        arr = new int[size];
        capacity = size;
        top = -1;
    }

    void push(int x) {
        if (top == capacity - 1) {
            throw new RuntimeException("Stack Overflow");
        }
        arr[++top] = x;
    }

    int pop() {
        if (isEmpty()) throw new RuntimeException("Stack Underflow");
        return arr[top--];
    }

    int peek() {
        if (isEmpty()) throw new RuntimeException("Stack is empty");
        return arr[top];
    }

    boolean isEmpty() {
        return top == -1;
    }

    int size() {
        return top + 1;
    }
}
```

**Usage:**
```java
ArrayStack stack = new ArrayStack(5);
stack.push(10);
stack.push(20);
stack.push(30);
System.out.println(stack.pop());   // 30
System.out.println(stack.peek());  // 20
System.out.println(stack.size());  // 2
```

### 4.2 Linked List-based Stack (dynamic size, no overflow limit)

```java
class LinkedStack {
    class Node {
        int data;
        Node next;
        Node(int data) { this.data = data; }
    }

    private Node top;
    private int size;

    void push(int x) {
        Node newNode = new Node(x);
        newNode.next = top;
        top = newNode;
        size++;
    }

    int pop() {
        if (isEmpty()) throw new RuntimeException("Stack Underflow");
        int val = top.data;
        top = top.next;
        size--;
        return val;
    }

    int peek() {
        if (isEmpty()) throw new RuntimeException("Stack is empty");
        return top.data;
    }

    boolean isEmpty() {
        return top == null;
    }

    int size() {
        return size;
    }
}
```

**Array-based vs Linked List-based Stack:**
| Aspect | Array-based | Linked List-based |
|---|---|---|
| Size | Fixed (or requires resizing) | Dynamic, grows as needed |
| Memory | Contiguous, less overhead | Extra pointer per node |
| Overflow | Possible if fixed size | Never (limited only by heap memory) |
| Cache performance | Better (contiguous memory) | Worse (scattered nodes) |

---

## 5. Interview Q&A Quick Hits

**Q: Why is `java.util.Stack` considered bad design?**
A: It extends `Vector`, inheriting index-based methods (`get`, `add(index,e)`, `remove(index)`) that let you violate LIFO order — a stack should only expose `push`/`pop`/`peek`. It's also synchronized, adding unnecessary overhead for single-threaded use.

**Q: What do you use instead of `java.util.Stack` today?**
A: `Deque<T> stack = new ArrayDeque<>();` — faster, not synchronized, and doesn't expose List-breaking methods.

**Q: Time complexity of stack operations?**
| Operation | Array-based | Linked-based | ArrayDeque |
|---|---|---|---|
| push | O(1) amortized | O(1) | O(1) amortized |
| pop | O(1) | O(1) | O(1) |
| peek | O(1) | O(1) | O(1) |
| search | O(n) | O(n) | O(n) |

**Q: Classic Stack-based interview problems (be ready to code these):**

**a) Valid Parentheses / Balanced Brackets**
```java
boolean isValid(String s) {
    Deque<Character> stack = new ArrayDeque<>();
    Map<Character, Character> pairs = Map.of(')', '(', ']', '[', '}', '{');
    for (char c : s.toCharArray()) {
        if (pairs.containsValue(c)) {
            stack.push(c);
        } else if (pairs.containsKey(c)) {
            if (stack.isEmpty() || stack.pop() != pairs.get(c)) return false;
        }
    }
    return stack.isEmpty();
}
// isValid("({[]})") -> true
// isValid("([)]")   -> false
```

**b) Reverse a String using Stack**
```java
String reverse(String s) {
    Deque<Character> stack = new ArrayDeque<>();
    for (char c : s.toCharArray()) stack.push(c);
    StringBuilder sb = new StringBuilder();
    while (!stack.isEmpty()) sb.append(stack.pop());
    return sb.toString();
}
```

**c) Next Greater Element (monotonic stack pattern)**
```java
int[] nextGreaterElement(int[] nums) {
    int[] result = new int[nums.length];
    Arrays.fill(result, -1);
    Deque<Integer> stack = new ArrayDeque<>();  // stores indices

    for (int i = 0; i < nums.length; i++) {
        while (!stack.isEmpty() && nums[stack.peek()] < nums[i]) {
            result[stack.pop()] = nums[i];
        }
        stack.push(i);
    }
    return result;
}
// [4,5,2,10] -> [5,10,10,-1]
```

**d) Evaluate Postfix Expression**
```java
int evalPostfix(String expr) {
    Deque<Integer> stack = new ArrayDeque<>();
    for (String token : expr.split(" ")) {
        if (token.matches("-?\\d+")) {
            stack.push(Integer.parseInt(token));
        } else {
            int b = stack.pop();
            int a = stack.pop();
            switch (token) {
                case "+": stack.push(a + b); break;
                case "-": stack.push(a - b); break;
                case "*": stack.push(a * b); break;
                case "/": stack.push(a / b); break;
            }
        }
    }
    return stack.pop();
}
// evalPostfix("2 3 + 4 *") -> 20
```

**e) Implement a Queue using two Stacks (very common)**
```java
class QueueUsingStacks {
    Deque<Integer> inStack = new ArrayDeque<>();
    Deque<Integer> outStack = new ArrayDeque<>();

    void enqueue(int x) {
        inStack.push(x);
    }

    int dequeue() {
        if (outStack.isEmpty()) {
            while (!inStack.isEmpty()) outStack.push(inStack.pop());
        }
        return outStack.pop();
    }
}
```

**Q: Min Stack — design a stack that supports `getMin()` in O(1)**
```java
class MinStack {
    Deque<Integer> stack = new ArrayDeque<>();
    Deque<Integer> minStack = new ArrayDeque<>();

    void push(int x) {
        stack.push(x);
        if (minStack.isEmpty() || x <= minStack.peek()) minStack.push(x);
        else minStack.push(minStack.peek());
    }

    int pop() {
        minStack.pop();
        return stack.pop();
    }

    int getMin() {
        return minStack.peek();
    }
}
```

---

## 6. Full Working Example (Interview-style demo)

```java
import java.util.*;

public class StackDemo {
    public static void main(String[] args) {
        // Legacy Stack
        Stack<Integer> legacyStack = new Stack<>();
        legacyStack.push(1); legacyStack.push(2); legacyStack.push(3);
        System.out.println("Legacy Stack: " + legacyStack);
        System.out.println("Pop: " + legacyStack.pop());
        System.out.println("Search for 1: " + legacyStack.search(1));  // position from top

        // Modern Deque as Stack
        Deque<Integer> deque = new ArrayDeque<>();
        deque.push(10); deque.push(20); deque.push(30);
        System.out.println("\nDeque as Stack: " + deque);
        System.out.println("Pop: " + deque.pop());
        System.out.println("Peek: " + deque.peek());

        // Valid Parentheses check
        String expr = "({[]})";
        System.out.println("\nIs '" + expr + "' valid? " + isValid(expr));

        // Custom array-based stack
        ArrayStack customStack = new ArrayStack(5);
        customStack.push(100); customStack.push(200);
        System.out.println("\nCustom Stack pop: " + customStack.pop());
    }

    static boolean isValid(String s) {
        Deque<Character> stack = new ArrayDeque<>();
        Map<Character, Character> pairs = Map.of(')', '(', ']', '[', '}', '{');
        for (char c : s.toCharArray()) {
            if (pairs.containsValue(c)) stack.push(c);
            else if (pairs.containsKey(c)) {
                if (stack.isEmpty() || stack.pop() != pairs.get(c)) return false;
            }
        }
        return stack.isEmpty();
    }

    static class ArrayStack {
        int[] arr; int top; int capacity;
        ArrayStack(int size) { arr = new int[size]; capacity = size; top = -1; }
        void push(int x) { arr[++top] = x; }
        int pop() { return arr[top--]; }
    }
}
```

**Output:**
```
Legacy Stack: [1, 2, 3]
Pop: 3
Search for 1: 2

Deque as Stack: [30, 20, 10]
Pop: 30
Peek: 20

Is '({[]})' valid? true

Custom Stack pop: 200
```

---

## 7. Quick Reference Table (memorize for interviews)

| Method | Class | Purpose |
|---|---|---|
| `push` | Stack / Deque | Add to top |
| `pop` | Stack / Deque | Remove & return top |
| `peek` | Stack / Deque | View top without removing |
| `empty` / `isEmpty` | Stack / Deque | Check if empty |
| `search` | Stack only | 1-based position from top |
| `addFirst` / `removeFirst` / `peekFirst` | Deque | Underlying methods `push`/`pop`/`peek` map to |
| `get(index)`, `add(index,e)` | Stack (inherited, avoid) | List-style access — breaks LIFO |

> **Interview tip:** Always mention the **Deque-over-Stack** recommendation first, then be ready to code at least one classic problem: **balanced parentheses**, **next greater element**, or **min stack** — these three cover 90% of stack-based interview questions.
