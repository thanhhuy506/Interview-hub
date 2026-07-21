# Java Collections Interview Handbook

For senior Java backend engineers with around 10 years of experience.

---

## 1. Big Picture

Java Collections Framework is a unified architecture for storing, accessing, searching, sorting, and manipulating groups of objects.

It provides:

- Interfaces: `Collection`, `List`, `Set`, `Queue`, `Deque`, `Map`
- Implementations: `ArrayList`, `LinkedList`, `HashSet`, `TreeSet`, `HashMap`, `ConcurrentHashMap`, etc.
- Algorithms: sorting, searching, copying, shuffling through `Collections`
- Utilities: unmodifiable, synchronized, checked collections

### Interview Answer

**English**

Java Collections Framework is a set of interfaces, implementations, and algorithms that provide reusable data structures. As a senior engineer, I do not choose a collection randomly. I choose it based on access pattern, insertion/deletion frequency, ordering requirement, uniqueness requirement, null handling, thread-safety requirement, and Big-O complexity.

**Vietnamese**

Java Collections Framework là tập hợp các interface, implementation và algorithm để cung cấp các cấu trúc dữ liệu tái sử dụng. Với kinh nghiệm senior, em sẽ không chọn collection một cách ngẫu nhiên. Em chọn dựa trên access pattern, tần suất insert/delete, yêu cầu ordering, uniqueness, null handling, thread-safety và độ phức tạp Big-O.

---

## 2. Core Hierarchy

```text
Iterable
 └── Collection
      ├── List
      │    ├── ArrayList
      │    ├── LinkedList
      │    └── Vector / Stack legacy
      │
      ├── Set
      │    ├── HashSet
      │    ├── LinkedHashSet
      │    └── SortedSet / NavigableSet
      │         └── TreeSet
      │
      └── Queue
           ├── PriorityQueue
           └── Deque
                ├── ArrayDeque
                └── LinkedList

Map is separate from Collection
 ├── HashMap
 ├── LinkedHashMap
 ├── TreeMap
 ├── Hashtable legacy
 ├── ConcurrentHashMap
 └── WeakHashMap / IdentityHashMap / EnumMap
```

`Map` is not a subtype of `Collection` because it stores key-value pairs, not individual elements.

---

## 3. List

A `List` is an ordered collection that allows duplicate elements and index-based access.

### 3.1 ArrayList

`ArrayList` is backed by a resizable array.

Main characteristics:

- Maintains insertion order
- Allows duplicates
- Allows null values
- Fast random access: `O(1)`
- Append is amortized `O(1)`
- Insert/delete in the middle is `O(n)` because elements need shifting
- Not thread-safe

### Internal Working

Internally, `ArrayList` uses an `Object[]` array.

When capacity is not enough, it grows automatically, usually by around 1.5x.

```java
List<String> names = new ArrayList<>();
names.add("Alice");
names.add("Bob");
```

When adding many elements, define initial capacity to reduce resizing cost:

```java
List<Order> orders = new ArrayList<>(10_000);
```

### Senior Interview Answer

**English**

I use `ArrayList` when I need fast read by index, iteration, and append operations. It is cache-friendly because elements are stored in a continuous array. However, insertion or deletion in the middle is expensive because it requires shifting elements. For large lists, I usually provide an initial capacity if the expected size is known to avoid repeated resizing.

**Vietnamese**

Em dùng `ArrayList` khi cần đọc nhanh theo index, iterate nhanh và append nhiều. Nó cache-friendly vì dữ liệu nằm trong array liên tục. Tuy nhiên insert hoặc delete ở giữa sẽ tốn chi phí vì phải shift element. Với list lớn, nếu biết trước size, em thường set initial capacity để tránh resize nhiều lần.

---

### 3.2 LinkedList

`LinkedList` is a doubly linked list.

Main characteristics:

- Maintains insertion order
- Allows duplicates
- Allows null values
- Implements both `List` and `Deque`
- Access by index is `O(n)`
- Insert/delete is `O(1)` only when node reference is already known
- More memory overhead because each node stores previous and next pointers

### Internal Working

Each element is wrapped in a node:

```text
prev <- Node(item) -> next
```

### Senior Interview Answer

**English**

Many candidates think `LinkedList` is always better for insertion and deletion, but that is not always true. If I insert by index, Java still needs to traverse the list first, which is `O(n)`. In real-world backend systems, `ArrayList` is often faster because of better CPU cache locality. I use `LinkedList` mainly when I need deque behavior, but even then `ArrayDeque` is usually a better choice.

**Vietnamese**

Nhiều người nghĩ `LinkedList` luôn tốt hơn cho insert/delete, nhưng không phải lúc nào cũng đúng. Nếu insert theo index, Java vẫn phải traverse list trước, chi phí là `O(n)`. Trong backend thực tế, `ArrayList` thường nhanh hơn do CPU cache locality tốt hơn. Em chỉ dùng `LinkedList` khi cần behavior dạng deque, nhưng trong nhiều case `ArrayDeque` vẫn tốt hơn.

---

### 3.3 ArrayList vs LinkedList

| Feature | ArrayList | LinkedList |
|---|---|---|
| Internal structure | Dynamic array | Doubly linked list |
| Random access | Fast `O(1)` | Slow `O(n)` |
| Append | Amortized `O(1)` | `O(1)` |
| Insert/delete middle | `O(n)` shifting | `O(n)` traversal + relink |
| Memory overhead | Lower | Higher |
| Cache locality | Better | Worse |
| Common backend usage | Very common | Less common |

### Interview Conclusion

For most backend use cases, prefer `ArrayList` unless there is a very specific reason to use `LinkedList`.

---

## 4. Set

A `Set` is a collection that does not allow duplicate elements.

### 4.1 HashSet

`HashSet` is backed by `HashMap`.

Main characteristics:

- No duplicate elements
- No guaranteed order
- Allows one null element
- Add/search/remove average `O(1)`
- Depends on correct `equals()` and `hashCode()` implementation

### Internal Working

Internally:

```java
HashSet<E> internally uses HashMap<E, Object>
```

The set element becomes the key in the internal map. A dummy object is used as the value.

```java
Set<String> emails = new HashSet<>();
emails.add("a@test.com");
emails.add("a@test.com"); // duplicate, ignored
```

### Senior Interview Answer

**English**

`HashSet` uses `HashMap` internally. The element is stored as the key, and a constant dummy object is stored as the value. Duplicate detection depends on `hashCode()` and `equals()`. If these methods are not implemented correctly, the set may allow logically duplicate objects or fail to find existing objects.

**Vietnamese**

`HashSet` dùng `HashMap` bên trong. Element được lưu như key, còn value là một dummy object cố định. Việc detect duplicate phụ thuộc vào `hashCode()` và `equals()`. Nếu implement sai hai method này, set có thể chứa object duplicate về mặt business hoặc không tìm thấy object đã tồn tại.

---

### 4.2 LinkedHashSet

`LinkedHashSet` maintains insertion order.

It is backed by `LinkedHashMap`.

Use it when:

- You need uniqueness
- You need predictable iteration order
- You want insertion order preserved

```java
Set<String> set = new LinkedHashSet<>();
set.add("B");
set.add("A");
set.add("C");
// iteration order: B, A, C
```

---

### 4.3 TreeSet

`TreeSet` is based on a Red-Black Tree.

Main characteristics:

- Sorted order
- No duplicates
- Add/search/remove `O(log n)`
- Does not allow null in natural ordering
- Requires elements to be comparable or a comparator must be provided

```java
Set<Integer> numbers = new TreeSet<>();
numbers.add(3);
numbers.add(1);
numbers.add(2);
// iteration order: 1, 2, 3
```

### Senior Interview Answer

**English**

I use `TreeSet` when I need sorted unique elements or range queries. It is slower than `HashSet` for basic lookup because operations are `O(log n)`, but it provides ordering and navigation operations such as `higher`, `lower`, `ceiling`, and `floor`.

**Vietnamese**

Em dùng `TreeSet` khi cần unique element có sorted order hoặc cần range query. Nó chậm hơn `HashSet` cho lookup cơ bản vì operation là `O(log n)`, nhưng đổi lại nó hỗ trợ ordering và navigation như `higher`, `lower`, `ceiling`, `floor`.

---

## 5. Map

A `Map` stores key-value pairs.

Keys must be unique. Values can be duplicated.

---

## 6. HashMap

`HashMap` is one of the most important Java collections for interviews.

Main characteristics:

- Stores key-value pairs
- Allows one null key and multiple null values
- No guaranteed order
- Average get/put/remove: `O(1)`
- Worst case: `O(n)`, improved to `O(log n)` when bucket is treeified
- Not thread-safe

---

## 7. HashMap Internal Working

### 7.1 Basic Structure

`HashMap` uses an array of buckets.

```text
table[index] -> Node<K,V> -> Node<K,V> -> Node<K,V>
```

Each node contains:

```java
static class Node<K,V> {
    final int hash;
    final K key;
    V value;
    Node<K,V> next;
}
```

### 7.2 Put Operation

When calling:

```java
map.put(key, value);
```

Java does roughly:

1. Calculate hash from key
2. Convert hash to bucket index
3. If bucket is empty, insert node
4. If bucket has nodes, compare hash and key using `equals()`
5. If same key exists, replace value
6. If different key, append node or insert into tree bucket
7. Resize if size exceeds threshold

### 7.3 Index Calculation

HashMap capacity is usually power of two.

Index is calculated like:

```java
index = (capacity - 1) & hash;
```

This is faster than modulo.

### 7.4 Collision

Collision happens when different keys map to the same bucket.

Before Java 8, collision bucket used linked list only.

Since Java 8, if a bucket becomes too large, it can be converted into a Red-Black Tree.

Important thresholds:

- `TREEIFY_THRESHOLD = 8`
- `UNTREEIFY_THRESHOLD = 6`
- `MIN_TREEIFY_CAPACITY = 64`

### 7.5 Resize

Default settings:

```java
initial capacity = 16
load factor = 0.75
threshold = capacity * load factor
```

When size exceeds threshold, HashMap resizes, usually doubles capacity.

Resize is expensive because buckets need to be redistributed.

### Senior Interview Answer

**English**

`HashMap` stores entries in an internal array of buckets. It calculates the hash of the key and maps it to an index using `(n - 1) & hash`. If multiple keys fall into the same bucket, it handles collision using a linked list, and since Java 8, when the bucket becomes large enough and the table capacity is at least 64, the bucket can be treeified into a Red-Black Tree. This improves worst-case lookup from `O(n)` to `O(log n)`. Correct implementation of `equals()` and `hashCode()` is critical.

**Vietnamese**

`HashMap` lưu entry trong một array bucket. Nó tính hash của key rồi map sang index bằng `(n - 1) & hash`. Nếu nhiều key rơi vào cùng bucket thì xảy ra collision, HashMap xử lý bằng linked list, và từ Java 8 nếu bucket đủ lớn và capacity ít nhất 64 thì bucket có thể được treeify thành Red-Black Tree. Điều này cải thiện worst-case lookup từ `O(n)` xuống `O(log n)`. Việc implement đúng `equals()` và `hashCode()` là cực kỳ quan trọng.

---

## 8. equals() and hashCode()

### Contract

If two objects are equal by `equals()`, they must have the same `hashCode()`.

```java
if a.equals(b) == true
then a.hashCode() == b.hashCode()
```

But the reverse is not always true.

```java
if a.hashCode() == b.hashCode()
a.equals(b) may be true or false
```

### Bad Example

```java
class User {
    private Long id;
    private String email;
}
```

Without overriding `equals()` and `hashCode()`, two users with the same id may be treated as different keys.

### Good Example

```java
class User {
    private Long id;
    private String email;

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof User user)) return false;
        return Objects.equals(id, user.id);
    }

    @Override
    public int hashCode() {
        return Objects.hash(id);
    }
}
```

### Senior Advice

Be careful when using mutable fields in `equals()` and `hashCode()`.

Bad case:

```java
Map<User, String> map = new HashMap<>();
User user = new User(1L, "a@test.com");
map.put(user, "value");

user.setId(2L); // dangerous if id is used in hashCode

map.get(user); // may return null
```

### Interview Answer

**English**

For hash-based collections such as `HashMap`, `HashSet`, and `ConcurrentHashMap`, `equals()` and `hashCode()` must be stable and consistent. I avoid using mutable fields as keys because if the field used in `hashCode()` changes after insertion, the object may be stored in one bucket but searched in another bucket.

**Vietnamese**

Với hash-based collection như `HashMap`, `HashSet`, `ConcurrentHashMap`, `equals()` và `hashCode()` phải ổn định và consistent. Em tránh dùng mutable field làm key vì nếu field dùng trong `hashCode()` bị thay đổi sau khi insert, object có thể đang nằm ở một bucket nhưng khi search lại bị tìm ở bucket khác.

---

## 9. LinkedHashMap

`LinkedHashMap` extends `HashMap` and maintains a doubly linked list across entries.

It can maintain:

- Insertion order
- Access order

### Use Cases

- Predictable iteration order
- Simple LRU cache

```java
Map<String, String> map = new LinkedHashMap<>();
map.put("B", "2");
map.put("A", "1");
map.put("C", "3");
// iteration order: B, A, C
```

### LRU Cache Example

```java
class LruCache<K, V> extends LinkedHashMap<K, V> {
    private final int maxSize;

    LruCache(int maxSize) {
        super(16, 0.75f, true); // accessOrder = true
        this.maxSize = maxSize;
    }

    @Override
    protected boolean removeEldestEntry(Map.Entry<K, V> eldest) {
        return size() > maxSize;
    }
}
```

---

## 10. TreeMap

`TreeMap` is based on a Red-Black Tree.

Main characteristics:

- Sorted by natural ordering or comparator
- get/put/remove: `O(log n)`
- Does not allow null key with natural ordering
- Supports range queries

```java
NavigableMap<Integer, String> map = new TreeMap<>();
map.put(10, "A");
map.put(20, "B");
map.put(30, "C");

map.floorKey(25); // 20
map.ceilingKey(25); // 30
```

### Use Cases

- Sorted map
- Range query
- Leaderboard
- Time-window lookup
- Prefix/ranking-like logic

---

## 11. HashMap vs Hashtable vs ConcurrentHashMap

| Feature | HashMap | Hashtable | ConcurrentHashMap |
|---|---|---|---|
| Thread-safe | No | Yes | Yes |
| Synchronization | None | Whole method synchronized | Fine-grained / CAS / synchronized bin |
| Null key/value | Allows one null key and many null values | Not allowed | Not allowed |
| Performance | Fast single-thread | Slow | High concurrent performance |
| Legacy | No | Yes | No |
| Recommended | Single-thread/non-shared | Avoid | Concurrent use |

### Senior Interview Answer

**English**

`Hashtable` is legacy and synchronizes almost every public method, which causes poor scalability. `ConcurrentHashMap` is designed for concurrent access with much better performance. In modern Java, I avoid `Hashtable`; I use `HashMap` for non-shared data and `ConcurrentHashMap` for shared concurrent access.

**Vietnamese**

`Hashtable` là legacy và synchronize gần như toàn bộ public method, nên scalability kém. `ConcurrentHashMap` được thiết kế cho concurrent access với performance tốt hơn nhiều. Trong Java hiện đại, em tránh dùng `Hashtable`; em dùng `HashMap` cho data không share giữa thread và `ConcurrentHashMap` cho shared concurrent access.

---

## 12. ConcurrentHashMap

`ConcurrentHashMap` is a thread-safe map optimized for concurrent access.

Main characteristics:

- Does not allow null key or null value
- Thread-safe
- Better scalability than `Collections.synchronizedMap()`
- Retrieval operations are generally non-blocking
- Updates lock only affected bin or use CAS internally

### Why null is not allowed?

Because in concurrent context, `null` creates ambiguity.

```java
map.get(key) == null
```

This could mean:

- Key does not exist
- Key exists but value is null

In concurrent scenarios, this ambiguity is dangerous.

### Common Atomic Methods

```java
map.putIfAbsent(key, value);
map.computeIfAbsent(key, k -> loadValue(k));
map.compute(key, (k, oldValue) -> newValue);
map.merge(key, 1, Integer::sum);
```

### Correct Usage

```java
ConcurrentHashMap<String, LongAdder> counters = new ConcurrentHashMap<>();

counters.computeIfAbsent("login", k -> new LongAdder()).increment();
```

### Senior Interview Answer

**English**

`ConcurrentHashMap` is not simply a synchronized `HashMap`. It is designed for high concurrency. Reads are usually non-blocking, and writes only synchronize at a smaller scope, such as a bin. It also provides atomic methods like `computeIfAbsent`, `putIfAbsent`, and `merge`, which help avoid race conditions that would happen with separate `get` and `put` operations.

**Vietnamese**

`ConcurrentHashMap` không đơn giản là một `HashMap` được synchronized. Nó được thiết kế cho high concurrency. Read thường non-blocking, còn write chỉ synchronize ở phạm vi nhỏ hơn như một bin. Nó cũng cung cấp atomic method như `computeIfAbsent`, `putIfAbsent`, `merge`, giúp tránh race condition khi dùng tách riêng `get` rồi `put`.

---

## 13. Queue and Deque

### 13.1 Queue

A `Queue` is usually FIFO.

Common methods:

| Method | Throws exception | Returns special value |
|---|---|---|
| Insert | `add()` | `offer()` |
| Remove | `remove()` | `poll()` |
| Examine | `element()` | `peek()` |

Prefer `offer`, `poll`, and `peek` in most application code because they avoid exceptions for normal control flow.

---

### 13.2 PriorityQueue

`PriorityQueue` orders elements by priority, not insertion order.

Internally it is based on a heap.

```java
PriorityQueue<Integer> pq = new PriorityQueue<>();
pq.offer(3);
pq.offer(1);
pq.offer(2);

pq.poll(); // 1
```

Main characteristics:

- Head is the smallest element by default
- `offer` and `poll`: `O(log n)`
- `peek`: `O(1)`
- Not thread-safe

Use cases:

- Top K problems
- Scheduling
- Ranking
- Dijkstra-like algorithms

---

### 13.3 Deque

`Deque` means double-ended queue.

It supports insertion/removal from both ends.

Common implementation:

- `ArrayDeque`
- `LinkedList`

### ArrayDeque

`ArrayDeque` is usually preferred over `Stack` and often preferred over `LinkedList` for queue/stack behavior.

```java
Deque<String> stack = new ArrayDeque<>();
stack.push("A");
stack.push("B");
stack.pop(); // B

Deque<String> queue = new ArrayDeque<>();
queue.offer("A");
queue.offer("B");
queue.poll(); // A
```

### Interview Answer

**English**

For stack behavior, I prefer `ArrayDeque` instead of legacy `Stack`. `Stack` extends `Vector`, so it has unnecessary synchronization and legacy design. `ArrayDeque` is faster and cleaner for single-threaded stack or queue usage.

**Vietnamese**

Với behavior dạng stack, em ưu tiên dùng `ArrayDeque` thay vì `Stack` legacy. `Stack` extends `Vector`, nên có synchronization không cần thiết và design cũ. `ArrayDeque` nhanh hơn và clean hơn cho stack hoặc queue trong single-threaded context.

---

## 14. Comparable vs Comparator

### Comparable

Used when the class has natural ordering.

```java
class Employee implements Comparable<Employee> {
    private int age;

    @Override
    public int compareTo(Employee other) {
        return Integer.compare(this.age, other.age);
    }
}
```

### Comparator

Used when ordering is external or multiple sorting strategies are needed.

```java
employees.sort(Comparator.comparing(Employee::getAge));

employees.sort(
    Comparator.comparing(Employee::getDepartment)
              .thenComparing(Employee::getAge)
              .thenComparing(Employee::getName)
);
```

### Senior Interview Answer

**English**

I use `Comparable` when the object has one clear natural ordering. I use `Comparator` when sorting logic is external, dynamic, or when I need multiple sorting strategies. In enterprise applications, `Comparator` is often more flexible because business sorting rules can vary by use case.

**Vietnamese**

Em dùng `Comparable` khi object có một natural ordering rõ ràng. Em dùng `Comparator` khi logic sort nằm bên ngoài, dynamic, hoặc cần nhiều strategy sort khác nhau. Trong enterprise application, `Comparator` thường linh hoạt hơn vì business sorting rule có thể thay đổi theo từng use case.

---

## 15. Fail-Fast vs Fail-Safe Iterator

### Fail-Fast

Collections like `ArrayList`, `HashMap`, `HashSet` are fail-fast.

If structurally modified while iterating, they may throw `ConcurrentModificationException`.

```java
List<String> list = new ArrayList<>(List.of("A", "B", "C"));

for (String item : list) {
    if (item.equals("B")) {
        list.remove(item); // may throw ConcurrentModificationException
    }
}
```

Correct way:

```java
Iterator<String> iterator = list.iterator();
while (iterator.hasNext()) {
    String item = iterator.next();
    if (item.equals("B")) {
        iterator.remove();
    }
}
```

Or:

```java
list.removeIf(item -> item.equals("B"));
```

### Fail-Safe / Weakly Consistent

Concurrent collections such as `ConcurrentHashMap` provide weakly consistent iterators.

They do not throw `ConcurrentModificationException`, but may or may not reflect updates during iteration.

### Interview Answer

**English**

Fail-fast iterators detect structural modification using a modification count and throw `ConcurrentModificationException` on a best-effort basis. It is not a thread-safety mechanism. Concurrent collections usually provide weakly consistent iterators, meaning they do not fail when the collection changes, but they may not reflect all latest updates during iteration.

**Vietnamese**

Fail-fast iterator detect structural modification bằng modification count và throw `ConcurrentModificationException` theo cơ chế best-effort. Nó không phải là cơ chế thread-safe. Concurrent collection thường cung cấp weakly consistent iterator, nghĩa là khi collection thay đổi trong lúc iterate thì không bị fail, nhưng iterator có thể không thấy toàn bộ update mới nhất.

---

## 16. Collections Utility Class

`Collections` provides utility methods.

Examples:

```java
Collections.sort(list);
Collections.reverse(list);
Collections.shuffle(list);
Collections.unmodifiableList(list);
Collections.synchronizedList(list);
Collections.emptyList();
Collections.singletonList("A");
```

### Unmodifiable View vs Immutable Collection

```java
List<String> mutable = new ArrayList<>();
List<String> view = Collections.unmodifiableList(mutable);

mutable.add("A");
System.out.println(view); // [A]
```

`Collections.unmodifiableList()` returns a read-only view of the original list. If the original list changes, the view reflects the change.

`List.of()` creates an immutable list.

```java
List<String> immutable = List.of("A", "B");
immutable.add("C"); // UnsupportedOperationException
```

### Interview Answer

**English**

`Collections.unmodifiableList()` is not the same as a truly immutable copy. It creates an unmodifiable view backed by the original list. If the original list changes, the view changes too. If I need immutability, I usually create a defensive copy using `List.copyOf()` or use `List.of()` when values are known.

**Vietnamese**

`Collections.unmodifiableList()` không giống immutable copy thật sự. Nó tạo ra một unmodifiable view được backed bởi list gốc. Nếu list gốc thay đổi, view cũng thay đổi. Nếu cần immutability, em thường tạo defensive copy bằng `List.copyOf()` hoặc dùng `List.of()` khi data đã biết trước.

---

## 17. Immutable Collections

Since Java 9:

```java
List<String> list = List.of("A", "B");
Set<String> set = Set.of("A", "B");
Map<String, Integer> map = Map.of("A", 1, "B", 2);
```

Characteristics:

- Immutable
- Do not allow null
- Throw `UnsupportedOperationException` on modification
- Useful for constants and defensive programming

For larger maps:

```java
Map<String, Integer> map = Map.ofEntries(
    Map.entry("A", 1),
    Map.entry("B", 2),
    Map.entry("C", 3)
);
```

---

## 18. CopyOnWriteArrayList

`CopyOnWriteArrayList` is a thread-safe list where every write creates a new copy of the underlying array.

Good for:

- Many reads
- Very few writes
- Listener lists
- Configuration snapshots

Bad for:

- Frequent writes
- Large lists

```java
List<String> listeners = new CopyOnWriteArrayList<>();
```

### Interview Answer

**English**

`CopyOnWriteArrayList` is useful when reads are extremely frequent and writes are rare. Iteration is safe without locking because it works on a snapshot. However, every write copies the underlying array, so it is very expensive for write-heavy workloads.

**Vietnamese**

`CopyOnWriteArrayList` hữu ích khi read rất nhiều và write rất ít. Việc iterate an toàn mà không cần lock vì iterator làm việc trên snapshot. Tuy nhiên mỗi lần write sẽ copy toàn bộ array bên dưới, nên rất tốn chi phí với workload ghi nhiều.

---

## 19. BlockingQueue

`BlockingQueue` is useful for producer-consumer problems.

Common implementations:

- `ArrayBlockingQueue`
- `LinkedBlockingQueue`
- `PriorityBlockingQueue`
- `DelayQueue`
- `SynchronousQueue`

Example:

```java
BlockingQueue<Task> queue = new LinkedBlockingQueue<>(1000);

// Producer
queue.put(task);

// Consumer
Task task = queue.take();
```

### Senior Interview Answer

**English**

I use `BlockingQueue` for producer-consumer patterns. It provides built-in blocking behavior, so producers can wait when the queue is full, and consumers can wait when the queue is empty. In backend systems, it is useful for async task processing, but we must define capacity carefully to avoid unbounded memory growth.

**Vietnamese**

Em dùng `BlockingQueue` cho producer-consumer pattern. Nó cung cấp blocking behavior sẵn, producer có thể chờ khi queue full, consumer có thể chờ khi queue empty. Trong backend system, nó hữu ích cho async task processing, nhưng phải define capacity cẩn thận để tránh memory tăng không giới hạn.

---

## 20. Complexity Cheat Sheet

| Collection | Get | Add | Remove | Contains | Ordered | Sorted | Thread-safe |
|---|---:|---:|---:|---:|---|---|---|
| ArrayList | `O(1)` | Amortized `O(1)` | `O(n)` | `O(n)` | Yes | No | No |
| LinkedList | `O(n)` | `O(1)` | `O(n)` | `O(n)` | Yes | No | No |
| HashSet | N/A | `O(1)` avg | `O(1)` avg | `O(1)` avg | No | No | No |
| LinkedHashSet | N/A | `O(1)` avg | `O(1)` avg | `O(1)` avg | Yes | No | No |
| TreeSet | N/A | `O(log n)` | `O(log n)` | `O(log n)` | Yes | Yes | No |
| HashMap | `O(1)` avg | `O(1)` avg | `O(1)` avg | key `O(1)` avg | No | No | No |
| LinkedHashMap | `O(1)` avg | `O(1)` avg | `O(1)` avg | key `O(1)` avg | Yes | No | No |
| TreeMap | `O(log n)` | `O(log n)` | `O(log n)` | key `O(log n)` | Yes | Yes | No |
| ConcurrentHashMap | `O(1)` avg | `O(1)` avg | `O(1)` avg | key `O(1)` avg | No | No | Yes |
| PriorityQueue | peek `O(1)` | `O(log n)` | poll `O(log n)` | `O(n)` | Priority order | Partial | No |
| ArrayDeque | N/A | `O(1)` amortized | `O(1)` amortized | `O(n)` | Yes | No | No |

---

## 21. Choosing the Right Collection

### Need indexed access?

Use `ArrayList`.

### Need uniqueness only?

Use `HashSet`.

### Need uniqueness and insertion order?

Use `LinkedHashSet`.

### Need uniqueness and sorted order?

Use `TreeSet`.

### Need key-value lookup?

Use `HashMap`.

### Need key-value lookup with insertion order?

Use `LinkedHashMap`.

### Need sorted key-value map?

Use `TreeMap`.

### Need concurrent key-value map?

Use `ConcurrentHashMap`.

### Need stack or queue behavior?

Use `ArrayDeque`.

### Need priority-based processing?

Use `PriorityQueue`.

### Need producer-consumer with blocking?

Use `BlockingQueue`.

---

## 22. Common Interview Questions and Answers

### Q1. Difference between ArrayList and LinkedList?

**English**

`ArrayList` is backed by a dynamic array, so random access is `O(1)` and iteration is usually faster due to cache locality. Insert/delete in the middle is `O(n)` because elements need shifting. `LinkedList` is a doubly linked list, so random access is `O(n)`. Insert/delete is only `O(1)` if we already have the node reference, but if we insert by index, traversal is still `O(n)`. In most backend systems, I prefer `ArrayList` unless I specifically need deque behavior.

**Vietnamese**

`ArrayList` được backed bởi dynamic array, nên random access là `O(1)` và iterate thường nhanh hơn nhờ cache locality. Insert/delete ở giữa là `O(n)` vì phải shift element. `LinkedList` là doubly linked list, random access là `O(n)`. Insert/delete chỉ `O(1)` nếu đã có node reference, nhưng nếu insert theo index thì vẫn phải traverse `O(n)`. Trong hầu hết backend system, em ưu tiên `ArrayList` trừ khi cần behavior dạng deque.

---

### Q2. How does HashMap work internally?

**English**

`HashMap` uses an internal array of buckets. When we put a key-value pair, it calculates the hash of the key, maps it to a bucket index using bit operation, and stores the entry there. If multiple keys map to the same bucket, collision is handled by linked list or, since Java 8, by treeifying the bucket into a Red-Black Tree when the bucket is large enough and capacity is at least 64. Average complexity is `O(1)`, but in bad collision scenarios it can be `O(log n)` after treeification.

**Vietnamese**

`HashMap` dùng một array bucket bên trong. Khi put key-value, nó tính hash của key, map hash đó sang bucket index bằng bit operation, rồi lưu entry vào bucket đó. Nếu nhiều key rơi vào cùng bucket thì collision được xử lý bằng linked list hoặc từ Java 8 có thể treeify bucket thành Red-Black Tree khi bucket đủ lớn và capacity ít nhất 64. Average complexity là `O(1)`, nhưng trong collision xấu thì sau treeification có thể là `O(log n)`.

---

### Q3. Why should HashMap key be immutable?

**English**

A HashMap key should be immutable because HashMap uses the key's hash code to decide the bucket location. If the fields used in `hashCode()` change after insertion, the key may no longer be found because HashMap will search in a different bucket. This causes very hard-to-debug issues.

**Vietnamese**

Key của HashMap nên immutable vì HashMap dùng hash code của key để xác định bucket. Nếu field dùng trong `hashCode()` thay đổi sau khi insert, key có thể không tìm được nữa vì HashMap sẽ search ở bucket khác. Đây là lỗi rất khó debug.

---

### Q4. Difference between HashMap and ConcurrentHashMap?

**English**

`HashMap` is not thread-safe. If multiple threads modify it concurrently, it can cause data inconsistency. `ConcurrentHashMap` is thread-safe and optimized for concurrent access. It does not lock the entire map for normal operations. Reads are generally non-blocking, and writes lock only a smaller scope or use CAS. Also, `ConcurrentHashMap` does not allow null keys or values.

**Vietnamese**

`HashMap` không thread-safe. Nếu nhiều thread cùng modify, có thể gây data inconsistency. `ConcurrentHashMap` thread-safe và được optimize cho concurrent access. Nó không lock toàn bộ map cho operation thông thường. Read thường non-blocking, còn write chỉ lock phạm vi nhỏ hơn hoặc dùng CAS. Ngoài ra `ConcurrentHashMap` không cho phép null key hoặc null value.

---

### Q5. Difference between HashSet and TreeSet?

**English**

`HashSet` is backed by `HashMap` and provides average `O(1)` add, remove, and contains, but it does not guarantee order. `TreeSet` is backed by a Red-Black Tree and keeps elements sorted, but operations are `O(log n)`. I use `HashSet` for fast uniqueness checks and `TreeSet` when sorted order or range navigation is required.

**Vietnamese**

`HashSet` được backed bởi `HashMap`, add/remove/contains average `O(1)`, nhưng không đảm bảo order. `TreeSet` được backed bởi Red-Black Tree và giữ element sorted, nhưng operation là `O(log n)`. Em dùng `HashSet` để check uniqueness nhanh, còn `TreeSet` khi cần sorted order hoặc range navigation.

---

### Q6. Difference between fail-fast and fail-safe iterator?

**English**

Fail-fast iterators throw `ConcurrentModificationException` if the collection is structurally modified during iteration, except through the iterator itself. This is common in `ArrayList`, `HashMap`, and `HashSet`. Concurrent collections such as `ConcurrentHashMap` have weakly consistent iterators. They do not throw the exception, but they may not reflect all updates during iteration.

**Vietnamese**

Fail-fast iterator throw `ConcurrentModificationException` nếu collection bị structural modification trong lúc iterate, ngoại trừ modify thông qua chính iterator. Cơ chế này thường thấy ở `ArrayList`, `HashMap`, `HashSet`. Concurrent collection như `ConcurrentHashMap` có weakly consistent iterator. Nó không throw exception, nhưng có thể không reflect toàn bộ update trong lúc iterate.

---

### Q7. Why is ConcurrentHashMap not allowing null?

**English**

`ConcurrentHashMap` does not allow null because null creates ambiguity in concurrent access. If `get(key)` returns null, we cannot safely know whether the key does not exist or the key exists with a null value. In a concurrent environment, this ambiguity can lead to race conditions or incorrect logic.

**Vietnamese**

`ConcurrentHashMap` không cho phép null vì null tạo ra ambiguity trong concurrent access. Nếu `get(key)` trả về null, ta không biết chắc key không tồn tại hay key tồn tại với value null. Trong môi trường concurrent, ambiguity này có thể gây race condition hoặc logic sai.

---

### Q8. What is load factor in HashMap?

**English**

Load factor controls when HashMap should resize. Default load factor is `0.75`. With default capacity `16`, threshold is `16 * 0.75 = 12`. When size exceeds 12, HashMap resizes. A lower load factor reduces collisions but uses more memory. A higher load factor saves memory but may increase collisions.

**Vietnamese**

Load factor kiểm soát khi nào HashMap cần resize. Default load factor là `0.75`. Với capacity mặc định `16`, threshold là `16 * 0.75 = 12`. Khi size vượt quá 12, HashMap sẽ resize. Load factor thấp giảm collision nhưng tốn memory hơn. Load factor cao tiết kiệm memory nhưng có thể tăng collision.

---

### Q9. What is the difference between Collections.synchronizedMap and ConcurrentHashMap?

**English**

`Collections.synchronizedMap()` wraps a map and synchronizes access using a single lock, so scalability is limited. Iteration still requires external synchronization. `ConcurrentHashMap` is designed for concurrency with better scalability, usually allowing concurrent reads and segmented/bin-level update control. For high-concurrency backend services, `ConcurrentHashMap` is usually preferred.

**Vietnamese**

`Collections.synchronizedMap()` wrap một map và synchronize access bằng một lock duy nhất, nên scalability bị giới hạn. Khi iterate vẫn cần external synchronization. `ConcurrentHashMap` được thiết kế cho concurrency với scalability tốt hơn, thường cho phép concurrent read và kiểm soát update ở mức segment/bin. Với backend service có concurrency cao, `ConcurrentHashMap` thường được ưu tiên.

---

### Q10. When should you use LinkedHashMap?

**English**

I use `LinkedHashMap` when I need HashMap-like lookup performance but also need predictable iteration order. It maintains a doubly linked list across entries. It can preserve insertion order or access order. Access order is useful for implementing simple LRU cache by overriding `removeEldestEntry()`.

**Vietnamese**

Em dùng `LinkedHashMap` khi cần lookup performance gần giống HashMap nhưng vẫn cần iteration order predictable. Nó maintain một doubly linked list giữa các entry. Nó có thể giữ insertion order hoặc access order. Access order hữu ích để implement LRU cache đơn giản bằng cách override `removeEldestEntry()`.

---

## 23. Senior-Level Best Practices

### 23.1 Program to Interface

Prefer:

```java
List<String> names = new ArrayList<>();
Map<String, User> users = new HashMap<>();
```

Avoid:

```java
ArrayList<String> names = new ArrayList<>();
HashMap<String, User> users = new HashMap<>();
```

Reason: easier to change implementation later.

---

### 23.2 Use Initial Capacity for Large Collections

```java
Map<String, User> users = new HashMap<>(expectedSize * 4 / 3 + 1);
```

Reason: avoid repeated resizing.

---

### 23.3 Avoid Exposing Mutable Collections

Bad:

```java
public List<Item> getItems() {
    return items;
}
```

Better:

```java
public List<Item> getItems() {
    return List.copyOf(items);
}
```

---

### 23.4 Be Careful with remove while Iterating

Prefer:

```java
list.removeIf(item -> item.isExpired());
```

---

### 23.5 Use EnumMap for Enum Keys

```java
Map<OrderStatus, Integer> countByStatus = new EnumMap<>(OrderStatus.class);
```

`EnumMap` is faster and more memory-efficient than `HashMap` for enum keys.

---

### 23.6 Use EnumSet for Enum Sets

```java
Set<Role> roles = EnumSet.of(Role.ADMIN, Role.USER);
```

Very efficient because internally it uses bit vectors.

---

### 23.7 Avoid Using List.contains for Large Lookup

Bad:

```java
if (userIds.contains(id)) {
    // O(n)
}
```

Better:

```java
Set<Long> userIdSet = new HashSet<>(userIds);
if (userIdSet.contains(id)) {
    // O(1) average
}
```

---

### 23.8 Do Not Use Parallel Stream Blindly

Collections can be used with streams, but parallel stream is not always faster.

Consider:

- Data size
- CPU-bound or IO-bound
- Thread pool impact
- Shared mutable state
- Ordering requirement

---

## 24. Real Backend Examples

### Example 1: Remove Duplicate IDs while Preserving Order

```java
List<Long> ids = List.of(3L, 1L, 3L, 2L, 1L);
Set<Long> unique = new LinkedHashSet<>(ids);
List<Long> result = new ArrayList<>(unique);
// [3, 1, 2]
```

Use `LinkedHashSet` because we need uniqueness and insertion order.

---

### Example 2: Group Orders by Status

```java
Map<OrderStatus, List<Order>> ordersByStatus = orders.stream()
    .collect(Collectors.groupingBy(Order::getStatus));
```

For enum key optimization:

```java
Map<OrderStatus, List<Order>> ordersByStatus = orders.stream()
    .collect(Collectors.groupingBy(
        Order::getStatus,
        () -> new EnumMap<>(OrderStatus.class),
        Collectors.toList()
    ));
```

---

### Example 3: Count Events Concurrently

```java
ConcurrentHashMap<String, LongAdder> counters = new ConcurrentHashMap<>();

public void increment(String eventName) {
    counters.computeIfAbsent(eventName, key -> new LongAdder()).increment();
}
```

This is better than using `AtomicLong` in very high contention cases.

---

### Example 4: Prevent External Mutation

```java
public class OrderResponse {
    private final List<OrderItem> items;

    public OrderResponse(List<OrderItem> items) {
        this.items = List.copyOf(items);
    }

    public List<OrderItem> getItems() {
        return items;
    }
}
```

---

## 25. Common Mistakes

1. Using `LinkedList` assuming it is always faster for insert/delete
2. Using mutable objects as `HashMap` keys
3. Forgetting to override `equals()` and `hashCode()`
4. Using `List.contains()` for large lookup loops
5. Returning internal mutable collections from APIs
6. Using `Hashtable` or `Vector` in modern code without reason
7. Using `Collections.unmodifiableList()` and thinking it is immutable copy
8. Modifying collection while iterating without iterator/removeIf
9. Using `parallelStream()` without understanding cost
10. Using unbounded queues in backend services

---

## 26. Final Interview Summary

**English**

Java Collections are not just about knowing class names. In real backend systems, choosing the right collection affects performance, memory usage, thread safety, and correctness. For read-heavy indexed data, I usually use `ArrayList`. For uniqueness, I use `HashSet`, `LinkedHashSet`, or `TreeSet` depending on ordering needs. For key-value lookup, I use `HashMap`, `LinkedHashMap`, `TreeMap`, or `ConcurrentHashMap` depending on ordering and concurrency requirements. I also pay close attention to `equals()` and `hashCode()`, immutability of keys, resizing cost, fail-fast behavior, and exposing mutable collections safely.

**Vietnamese**

Java Collections không chỉ là nhớ tên class. Trong backend system thực tế, chọn đúng collection ảnh hưởng trực tiếp đến performance, memory, thread safety và correctness. Với data cần read theo index nhiều, em thường dùng `ArrayList`. Với uniqueness, em dùng `HashSet`, `LinkedHashSet`, hoặc `TreeSet` tùy yêu cầu ordering. Với key-value lookup, em dùng `HashMap`, `LinkedHashMap`, `TreeMap`, hoặc `ConcurrentHashMap` tùy yêu cầu ordering và concurrency. Em cũng rất chú ý đến `equals()` và `hashCode()`, immutability của key, resize cost, fail-fast behavior và cách expose mutable collection an toàn.

---

## 27. Quick Revision Checklist

Before interview, make sure you can explain:

- Java Collections hierarchy
- `ArrayList` internal array and resizing
- `LinkedList` real performance trade-off
- `HashMap` internal working
- Hash collision and treeification
- Load factor and resizing
- `equals()` and `hashCode()` contract
- Why keys should be immutable
- `HashSet` backed by `HashMap`
- `TreeMap` and `TreeSet` Red-Black Tree
- `ConcurrentHashMap` vs `HashMap`
- `ConcurrentHashMap` null restriction
- Fail-fast vs weakly consistent iterator
- `ArrayDeque` vs `Stack`
- `Comparable` vs `Comparator`
- Immutable vs unmodifiable collections
- When to use `EnumMap` and `EnumSet`
- Producer-consumer with `BlockingQueue`
- Big-O complexity table

