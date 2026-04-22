# Core Java Syllabus for Backend Developers
## 2026 Industry-Focused Curriculum

---

## Overview

This syllabus covers Core Java concepts that directly impact backend development at scale. It prioritizes:
- **Production relevance** — what you actually use in Spring Boot/microservices
- **Interview frequency** — concepts asked in 80%+ of backend interviews
- **Depth over breadth** — understanding internals, not just API usage
- **Real-world patterns** — how to write performant, reliable code

**Target:** Junior/Associate Java Developer roles in India (TCS, Infosys, Cognizant, Wipro, startups)

**Time Allocation:** 60–80 hours focused study + 40 hours hands-on coding

**Outcome:** You should be able to write production-grade backend code, debug concurrency issues, and explain Java internals confidently in interviews.

---

## Module 1: Java Fundamentals (8 hours)

### 1.1 Data Types & Variables
- **Primitive types:** byte, short, int, long, float, double, boolean, char
  - Memory allocation (1, 2, 4, 8 bytes)
  - Overflow behavior (int max = 2,147,483,647)
  - When to use each type (long for large numbers, float for precision concerns)
- **Reference types:** Objects, arrays, strings
- **Type casting:** Implicit vs explicit, widening vs narrowing
- **Variable scope:** Local, instance, class (static), block scope

**Interview Questions:**
- What is the default value of an int variable? (0)
- Why use long instead of int for IDs in databases? (int overflows at 2.1 billion; long handles 9+ quintillion)
- What happens when you add 1 to Integer.MAX_VALUE? (Wraps to Integer.MIN_VALUE)

**Hands-On:**
```java
// Demonstrate type casting and overflow
long userId = 1_000_000_000_000L; // Use long for large numbers
int overflowTest = Integer.MAX_VALUE + 1; // Wraps to negative
```

---

### 1.2 Operators & Control Flow
- **Arithmetic:** +, -, *, /, % (modulo), ++ (pre vs post increment)
- **Comparison:** ==, !=, <, >, <=, >=
- **Logical:** &&, ||, ! (short-circuit evaluation matters)
- **Bitwise:** &, |, ^, ~, <<, >> (rarely used but good to know)
- **Ternary:** condition ? trueValue : falseValue
- **Control structures:** if-else, switch (traditional + switch expressions in Java 14+), loops (for, while, do-while, enhanced for)

**Key Concepts:**
- Short-circuit evaluation: `if (obj != null && obj.getValue() > 10)` — second condition only evaluated if first is true
- Switch expressions (Java 14+): Return values, no break statements needed

**Interview Questions:**
- Difference between == and .equals()? (== checks reference equality; .equals() checks value equality)
- What is short-circuit evaluation? (Logical operators stop evaluating once result is determined)
- When would you use a switch over if-else? (Fixed discrete values; cleaner syntax)

**Hands-On:**
```java
// Short-circuit evaluation
String name = null;
if (name != null && name.length() > 5) { // Safe; doesn't throw NPE
  System.out.println("Long name");
}

// Switch expression (Java 14+)
int days = switch (month) {
  case "Jan", "Mar", "May" -> 31;
  case "Feb" -> 28;
  default -> 30;
};
```

---

### 1.3 Strings & String Handling
- **String immutability:** Why strings are final, implications for memory
- **String literals vs String constructor:** Interning, string pool
- **String methods:** length(), charAt(), substring(), indexOf(), contains(), split(), trim(), toLowerCase(), toUpperCase()
- **String concatenation:** + operator vs StringBuilder vs String.join()
- **StringBuilder vs StringBuffer:** Mutability, thread-safety trade-offs
- **String comparison:** equals(), equalsIgnoreCase(), compareTo()

**Key Concepts:**
- Strings are immutable → every concatenation creates a new object (performance issue in loops)
- String pool: identical string literals point to same memory location
- StringBuilder is mutable, faster for multiple concatenations
- StringBuffer is thread-safe version of StringBuilder (rarely used now)

**Interview Questions:**
- Why is String immutable in Java? (Security, thread-safety, string pool optimization)
- What's wrong with concatenating strings in a loop? (Creates new String object each iteration; O(n²) performance)
- When should you use StringBuilder instead of +? (>3 concatenations or in loops)

**Hands-On:**
```java
// String pool behavior
String s1 = "hello";
String s2 = "hello";
System.out.println(s1 == s2); // true (same reference in string pool)

String s3 = new String("hello");
System.out.println(s1 == s3); // false (different objects)
System.out.println(s1.equals(s3)); // true (same value)

// StringBuilder for performance
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 1000; i++) {
  sb.append(i).append(", ");
}
String result = sb.toString(); // Much faster than + in loop
```

---

## Module 2: Object-Oriented Programming (12 hours)

### 2.1 Classes & Objects
- **Class anatomy:** Fields, constructors, methods, static members
- **Access modifiers:** public, private, protected, package-private (default)
- **this and super keywords:** Reference to current object, parent class
- **Constructor overloading:** Multiple constructors with different signatures
- **Static members:** Class-level variables and methods, initialization blocks

**Key Concepts:**
- Every object gets its own copy of instance variables; static variables are shared
- Private fields → encapsulation → can change internal implementation without breaking API
- Constructor chaining: `this(...)` and `super(...)` for code reuse

**Interview Questions:**
- What is the difference between instance and static variables? (Instance: per-object; static: per-class, shared)
- Why use private fields with public getters/setters? (Encapsulation; allows validation, lazy initialization, change implementation later)
- Can you override a static method? (No; static methods are resolved at compile time, not runtime)

**Hands-On:**
```java
public class User {
  private String email; // Private field
  private static int totalUsers = 0; // Shared across all instances
  
  public User(String email) {
    this.email = email;
    totalUsers++;
  }
  
  public String getEmail() {
    return email;
  }
  
  public static int getTotalUsers() {
    return totalUsers; // Can only access static members
  }
}

User u1 = new User("user@example.com");
User u2 = new User("another@example.com");
System.out.println(User.getTotalUsers()); // 2
```

---

### 2.2 Inheritance & Polymorphism
- **Inheritance:** extends keyword, inheritance hierarchy, IS-A relationship
- **Method overriding:** Subclass provides specific implementation of parent method
- **Polymorphism:** Runtime method resolution (late binding)
- **Abstract classes:** Cannot instantiate; partial implementation; define contracts
- **Interfaces:** Multiple inheritance, contracts without implementation (Java 8+ default methods)
- **super keyword:** Call parent class methods/constructors
- **Type casting:** Upcasting (automatic), downcasting (requires explicit cast, risk of ClassCastException)

**Key Concepts:**
- Override ≠ Overload. Override: subclass changes parent method. Overload: same method name, different signature.
- Polymorphism enables loosely coupled code: `List<Animal> animals = new ArrayList<>(); animals.add(new Dog()); animals.add(new Cat());`
- Abstract classes have constructors (for initialization); interfaces are pure contracts
- Interface default methods (Java 8+): Provide implementation without breaking existing implementations

**Interview Questions:**
- What is polymorphism and why is it useful? (Subclasses can be treated as parent type; enables extensibility without changing existing code)
- Can an interface extend another interface? (Yes; can have interface inheritance)
- What is the difference between abstract classes and interfaces? (Abstract: state + behavior; interface: contract. A class can extend one abstract class, implement many interfaces)
- What happens if you downcast to wrong type? (ClassCastException at runtime)

**Hands-On:**
```java
// Inheritance & Polymorphism
abstract class Animal {
  abstract void makeSound();
  
  void sleep() {
    System.out.println("Zzz...");
  }
}

class Dog extends Animal {
  @Override
  void makeSound() {
    System.out.println("Woof!");
  }
}

// Polymorphism in action
List<Animal> animals = Arrays.asList(new Dog(), new Dog());
for (Animal animal : animals) {
  animal.makeSound(); // Calls Dog's makeSound(), not Animal's
}

// Interface with default method (Java 8+)
interface Swimmer {
  void swim();
  
  default void rest() {
    System.out.println("Resting after swim");
  }
}

class Duck extends Animal implements Swimmer {
  @Override
  void makeSound() { System.out.println("Quack!"); }
  
  @Override
  public void swim() { System.out.println("Swimming..."); }
}
```

---

### 2.3 Encapsulation & Design Principles
- **Encapsulation:** Hide internal details, expose only necessary interface
- **Tight vs loose coupling:** Prefer loose coupling (depends on abstractions, not concrete classes)
- **High cohesion:** Related functionality in same class, unrelated functionality separated
- **SOLID principles introduction:**
  - **S**ingle Responsibility: One class, one reason to change
  - **O**pen/Closed: Open for extension, closed for modification
  - **L**iskov Substitution: Subtypes must be substitutable for base type
  - **I**nterface Segregation: Don't force clients to implement unnecessary methods
  - **D**ependency Inversion: Depend on abstractions, not concretions

**Interview Questions:**
- What is encapsulation? (Bundling data + methods; hiding internals; exposing interface)
- Why prefer loose coupling? (Changes to one component don't ripple; easier testing, reusability)
- Explain Single Responsibility Principle. (One class should have one reason to change; easier to test, maintain, reuse)

**Hands-On:**
```java
// Tight coupling (BAD)
class OrderProcessor {
  private MySQLDatabase db = new MySQLDatabase();
  
  void saveOrder(Order order) {
    db.insert(order);
  }
}

// Loose coupling (GOOD)
interface OrderRepository {
  void save(Order order);
}

class OrderProcessor {
  private OrderRepository repo;
  
  public OrderProcessor(OrderRepository repo) {
    this.repo = repo;
  }
  
  void processOrder(Order order) {
    repo.save(order);
  }
}

// Can now swap implementations
class PostgresOrderRepository implements OrderRepository { ... }
class MongoOrderRepository implements OrderRepository { ... }
```

---

## Module 3: Collections Framework (14 hours)

### 3.1 Collection Hierarchy & Interfaces
- **Collection (root interface):** add, remove, contains, size, iterator, stream
- **List:** Ordered, allows duplicates, index-based access
  - **ArrayList:** Dynamic array, fast random access (O(1)), slow insertion (O(n))
  - **LinkedList:** Doubly-linked list, slow random access (O(n)), fast insertion at head/tail (O(1))
  - **CopyOnWriteArrayList:** Thread-safe variant of ArrayList
- **Set:** Unique elements, unordered
  - **HashSet:** Fast add/remove/contains (O(1) average), unordered, null-safe
  - **TreeSet:** Sorted, slower (O(log n)), no nulls, backed by balanced BST
  - **LinkedHashSet:** Maintains insertion order, O(1) operations
- **Queue:** FIFO structure
  - **LinkedList:** Can be used as queue
  - **PriorityQueue:** Min-heap by default, O(log n) add/remove, useful for sorting
  - **Deque:** Double-ended queue
- **Map:** Key-value pairs, no duplicates in keys
  - **HashMap:** Fast (O(1)), unordered, null-safe, not thread-safe
  - **TreeMap:** Sorted by key (O(log n)), no nulls
  - **LinkedHashMap:** Insertion order, O(1) operations
  - **ConcurrentHashMap:** Thread-safe, highly performant (segment-based locking)
  - **Hashtable:** Legacy, fully synchronized (slower)

**Key Concepts:**
- Choose collection based on access patterns:
  - Frequent random access → ArrayList
  - Frequent insertion/deletion from middle → LinkedList
  - Need uniqueness → Set
  - Need ordering → TreeSet/TreeMap
  - Need thread-safe → CopyOnWriteArrayList, ConcurrentHashMap
- HashMap uses hash codes and equality: two objects with same hashCode() must have equals() == true
- Iterating over HashMap: Unordered; if you need order, use LinkedHashMap
- ConcurrentHashMap is thread-safe without locking entire map; HashMap is NOT thread-safe

**Interview Questions:**
- When would you use ArrayList vs LinkedList? (ArrayList for random access; LinkedList for frequent insertions at head/tail)
- What is the difference between HashMap and Hashtable? (HashMap: faster, not thread-safe; Hashtable: thread-safe but slower)
- Why do you need to override both hashCode() and equals()? (HashMap uses both to find objects; if only equals() is overridden, hash-based lookups fail)
- What is the time complexity of HashMap operations? (O(1) average, O(n) worst case if many hash collisions)

**Hands-On:**
```java
// ArrayList vs LinkedList
List<Integer> list1 = new ArrayList<>();
List<Integer> list2 = new LinkedList<>();

// ArrayList: O(1) access, O(n) insertion at head
list1.get(500000); // Fast
list1.add(0, 99); // Slow (shifts all elements)

// LinkedList: O(n) access, O(1) insertion at head
list2.get(500000); // Slow (traverses from start)
list2.add(0, 99); // Fast

// HashMap with custom key
class UserId {
  int id;
  
  @Override
  public int hashCode() {
    return Integer.hashCode(id);
  }
  
  @Override
  public boolean equals(Object obj) {
    if (this == obj) return true;
    if (!(obj instanceof UserId)) return false;
    return id == ((UserId) obj).id;
  }
}

Map<UserId, String> userNames = new HashMap<>();
userNames.put(new UserId(1), "Alice");
userNames.get(new UserId(1)); // Must override equals() + hashCode() for lookup to work

// ConcurrentHashMap for thread-safety
Map<String, Integer> map = new ConcurrentHashMap<>();
map.put("key", 1);
map.putIfAbsent("key", 2); // Atomic operation
```

---

### 3.2 Iteration & Streams (Java 8+)
- **Iterator pattern:** Accessing elements sequentially without exposing structure
  - `Iterator<T> iterator()`, `hasNext()`, `next()`, `remove()`
  - Fail-fast behavior: ConcurrentModificationException if collection modified during iteration
- **Enhanced for loop:** Syntactic sugar for Iterator
- **forEach method:** `collection.forEach(e -> System.out.println(e))`
- **Streams (Java 8+):** Functional approach, lazy evaluation
  - **Intermediate operations:** filter, map, flatMap, sorted, distinct, limit, skip (lazy)
  - **Terminal operations:** collect, forEach, reduce, findFirst, count, anyMatch, allMatch (eager)
  - **Collectors:** Grouping, partitioning, reducing to custom structures

**Key Concepts:**
- Streams are not collections; they are pipelines for processing sequences
- Intermediate operations are lazy: nothing happens until terminal operation
- Parallel streams: `.parallelStream()` for multi-threaded processing (be careful with side effects)
- Streams are one-time use: can't iterate a stream twice

**Interview Questions:**
- What is a stream? (Pipeline for processing sequences functionally; not a data structure)
- Difference between intermediate and terminal operations? (Intermediate: lazy, return stream; terminal: eager, trigger execution)
- When should you use streams vs loops? (Streams for data transformations, readability; loops for complex control flow)
- What is the risk with parallel streams? (Unpredictable ordering, thread-safety issues if operations have side effects)

**Hands-On:**
```java
// Streams with intermediate + terminal operations
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

// Lazy evaluation: nothing happens until terminal operation
int result = numbers.stream()
  .filter(n -> n % 2 == 0) // Intermediate (lazy)
  .map(n -> n * n) // Intermediate (lazy)
  .reduce(0, Integer::sum); // Terminal (eager)

System.out.println(result); // 220

// Grouping with Collectors
Map<String, List<User>> usersByRole = users.stream()
  .collect(Collectors.groupingBy(User::getRole));

// Partitioning
Map<Boolean, List<User>> activeInactive = users.stream()
  .collect(Collectors.partitioningBy(User::isActive));

// Parallel stream (use cautiously)
long count = numbers.parallelStream()
  .filter(n -> n > 5)
  .count();
```

---

### 3.3 Sorting & Comparators
- **Comparable interface:** `compareTo()`, defines natural ordering
- **Comparator interface:** Defines custom ordering, multiple comparators possible
- **Sorting methods:** Collections.sort(), Arrays.sort(), streams sorted()
- **Custom comparators:** Lambda expressions, method references, multi-level sorting

**Key Concepts:**
- Comparable: Single ordering per class (implement in class itself)
- Comparator: Flexible, multiple orderings without modifying class
- compareTo() returns: negative (this < other), 0 (equal), positive (this > other)
- TreeSet and TreeMap require Comparable or Comparator (no equals/hashCode issues)

**Interview Questions:**
- Difference between Comparable and Comparator? (Comparable: single natural order in class; Comparator: external, flexible)
- How would you sort by multiple fields? (Chained comparators or custom Comparator)

**Hands-On:**
```java
// Comparable: natural ordering
class User implements Comparable<User> {
  String name;
  int age;
  
  @Override
  public int compareTo(User other) {
    return Integer.compare(this.age, other.age); // Sort by age
  }
}

Collections.sort(users); // Uses compareTo()

// Comparator: custom orderings
Comparator<User> byName = Comparator.comparing(User::getName);
Comparator<User> byAge = Comparator.comparingInt(User::getAge);

// Multi-level sorting: by role, then by name
Comparator<User> complex = Comparator
  .comparing(User::getRole)
  .thenComparing(User::getName);

users.sort(complex);

// Streams with sorting
users.stream()
  .sorted(Comparator.comparingInt(User::getAge).reversed())
  .collect(Collectors.toList());
```

---

## Module 4: Exception Handling (8 hours)

### 4.1 Exception Hierarchy & Types
- **Throwable (root):**
  - **Exception:** Recoverable errors
    - **Checked exceptions:** Compiler requires you to handle (e.g., IOException, SQLException)
    - **Unchecked exceptions (RuntimeException):** Compiler doesn't require handling (e.g., NullPointerException, IllegalArgumentException)
  - **Error:** JVM errors, not meant to catch (OutOfMemoryError, StackOverflowError)

**Key Concepts:**
- Checked exceptions: Must catch or declare in method signature
- Unchecked exceptions: Don't propagate obligation to caller; best for programming errors
- Rule: Use checked exceptions for recoverable conditions (file not found); unchecked for programmer errors (null dereference)

**Interview Questions:**
- Difference between checked and unchecked exceptions? (Checked: compiler enforces handling; unchecked: not enforced)
- When should you create custom exceptions? (When error is specific to your domain and needs special handling)
- What is the difference between throw and throws? (throw: actually raise exception; throws: declare that method may throw)

---

### 4.2 Try-Catch-Finally & Try-with-Resources
- **try-catch-finally:**
  - try: Code that may throw exception
  - catch: Handle specific exceptions (can chain multiple catch blocks)
  - finally: Cleanup code that always runs (even if catch rethrows)
- **Multiple catch blocks:** Catch specific exceptions first, then general (Java 7+ multi-catch: `catch (IOException | SQLException e)`)
- **Try-with-resources (Java 7+):** Automatic resource management, calls close() on AutoCloseable objects
- **Suppressed exceptions:** Secondary exceptions during cleanup in try-with-resources

**Key Concepts:**
- finally always runs unless JVM exits or infinite loop in try
- Avoid catching Exception; be specific (catch IOException, etc.)
- Try-with-resources eliminates null checks and explicit close() calls
- Multiple resources in try-with-resources: declared with semicolons

**Interview Questions:**
- When would you use try-finally without catch? (For cleanup even if exception escapes)
- What is try-with-resources? (Automatic resource closing; resource must implement AutoCloseable)
- If both try and finally throw exceptions, which one propagates? (finally's exception; try's exception is suppressed)

**Hands-On:**
```java
// Traditional try-catch-finally
try {
  FileReader reader = new FileReader("file.txt");
  // ... read file
  reader.close(); // Must remember to close
} catch (FileNotFoundException e) {
  // Handle specific exception
} finally {
  // Cleanup
}

// Try-with-resources (automatic close)
try (FileReader reader = new FileReader("file.txt")) {
  // ... read file
  // reader.close() called automatically
} catch (FileNotFoundException e) {
  // Handle
}

// Multiple exceptions
try {
  // Code that may throw different exceptions
} catch (IOException | SQLException e) {
  // Handle both in same block
  System.err.println("Error: " + e.getMessage());
}
```

---

### 4.3 Exception Handling Best Practices
- **Specific exceptions:** Catch specific exceptions, not Exception or Throwable
- **Logging:** Log exception details (stack trace) for debugging
- **Exception chaining:** `throw new CustomException("message", cause)` preserves original exception
- **Don't swallow exceptions:** Never catch without logging or handling
- **Custom exceptions:** Extend Exception or RuntimeException for domain-specific errors
- **Rethrowing:** `catch (Exception e) { ... throw e; }` or `throw new CustomException(..., e);`

**Hands-On:**
```java
// Good: specific exception, logging
try {
  parseInteger("not a number");
} catch (NumberFormatException e) {
  logger.error("Failed to parse integer", e); // Log with stack trace
  throw new ValidationException("Invalid input", e); // Chain exception
}

// BAD: swallows exception silently
try {
  loadData();
} catch (IOException e) {
  // Silent failure; bug propagates
}

// Custom exception
public class InvalidUserException extends RuntimeException {
  public InvalidUserException(String message) {
    super(message);
  }
  
  public InvalidUserException(String message, Throwable cause) {
    super(message, cause);
  }
}
```

---

## Module 5: Multithreading & Concurrency (18 hours)

### 5.1 Thread Basics
- **Thread creation:** Extend Thread, implement Runnable
- **Thread lifecycle:** New, Runnable, Running, Waiting, Terminated
- **Starting threads:** thread.start() (not run())
- **Thread methods:** join(), sleep(), yield(), interrupt()
- **Daemon threads:** Background threads; JVM exits when only daemon threads remain

**Key Concepts:**
- Always implement Runnable, not extend Thread (single inheritance limitation)
- start() creates new thread; run() executes in current thread
- Thread.sleep() is blocking; doesn't yield to other threads immediately
- join() blocks caller until thread finishes

**Interview Questions:**
- How do you create a thread? (Implement Runnable, create Thread, call start())
- Difference between start() and run()? (start(): creates new thread; run(): executes in current thread)
- What is a daemon thread? (Background thread; doesn't prevent JVM from exiting)

**Hands-On:**
```java
// Thread creation with Runnable
Thread t = new Thread(() -> {
  System.out.println("Running in thread: " + Thread.currentThread().getName());
});
t.start(); // Creates new thread
// t.run(); // DON'T do this; runs in current thread

// Thread.join() to wait for completion
Thread t1 = new Thread(() -> {
  try {
    Thread.sleep(2000); // Simulate work
  } catch (InterruptedException e) {
    Thread.currentThread().interrupt();
  }
});
t1.start();
t1.join(); // Main thread waits for t1 to finish
System.out.println("t1 finished");

// Daemon thread
Thread daemon = new Thread(() -> {
  while (true) {
    System.out.println("Daemon running...");
  }
});
daemon.setDaemon(true); // Set before starting
daemon.start();
// JVM exits after main thread finishes, even though daemon is running
```

---

### 5.2 Synchronization & Locks
- **Race conditions:** Multiple threads accessing shared state simultaneously, causing unpredictable results
- **synchronized keyword:** Method-level or block-level synchronization using intrinsic locks (monitors)
- **volatile keyword:** Ensures visibility of field changes across threads; prevents optimization by compiler
- **Intrinsic locks (monitors):** Every object has a lock; synchronized acquires it
- **ReentrantLock:** Explicit lock object, more flexible than synchronized
- **Mutual exclusion:** Only one thread can execute synchronized block at a time
- **Happens-before relationship:** Memory visibility guarantees in multithreading

**Key Concepts:**
- Race conditions occur with non-atomic compound operations (read-modify-write)
- synchronized: Simple, built-in; ReentrantLock: More features (tryLock, fairness, conditions)
- volatile: Not a substitute for synchronization; only ensures visibility of single field
- Intrinsic locks can cause deadlocks if acquired in inconsistent order

**Interview Questions:**
- What is a race condition? (Multiple threads modify shared state without coordination; result depends on timing)
- Difference between synchronized and ReentrantLock? (synchronized: simpler, implicit; ReentrantLock: explicit, flexible)
- What does volatile do? (Ensures visibility of field changes; prevents compiler optimizations)
- Can you have a race condition with immutable objects? (No; immutability eliminates race conditions)

**Hands-On:**
```java
// Race condition example
class Counter {
  private int count = 0;
  
  // NOT thread-safe
  public void increment() {
    count++; // Compound operation: read, increment, write
  }
  
  // Thread-safe with synchronized
  public synchronized void incrementSafe() {
    count++;
  }
  
  public synchronized int getCount() {
    return count;
  }
}

// Intrinsic lock on object
public synchronized void criticalSection() {
  // Only one thread can execute this at a time
}

// Synchronized block with specific lock object
private final Object lock = new Object();
synchronized (lock) {
  // Acquire lock on 'lock' object
  // Multiple threads on same object will block
}

// ReentrantLock
ReentrantLock lock = new ReentrantLock();
lock.lock();
try {
  // Critical section
} finally {
  lock.unlock(); // Always unlock
}

// tryLock for non-blocking attempt
if (lock.tryLock()) {
  try {
    // Critical section
  } finally {
    lock.unlock();
  }
} else {
  // Lock was held by another thread
}

// volatile for visibility (NOT atomicity)
class Flag {
  private volatile boolean stopped = false;
  
  public void stop() {
    stopped = true; // All threads see this change immediately
  }
  
  public void run() {
    while (!stopped) {
      // ...
    }
  }
}
```

---

### 5.3 Thread Synchronization Patterns
- **Producer-Consumer pattern:** One thread produces data, another consumes; coordination via queue/buffer
- **Wait-Notify mechanism:** Threads wait for condition, notified when condition changes
  - `object.wait()`: Release lock, wait for notification
  - `object.notify()`: Wake one waiting thread
  - `object.notifyAll()`: Wake all waiting threads
- **Deadlock:** Circular wait for locks; prevention strategies
- **Liveness issues:** Deadlock, livelock, starvation
- **Condition variables (with ReentrantLock):** More flexible than wait/notify
- **Semaphore:** Permits-based synchronization; control number of threads accessing resource
- **CountDownLatch:** Wait for N events to occur
- **CyclicBarrier:** Wait for N threads to reach barrier

**Key Concepts:**
- wait() must be in synchronized block; releases lock while waiting
- Spurious wakeups: wait() can wake without notify(); always check condition in loop
- Condition objects with ReentrantLock: `condition.await()`, `condition.signal()`
- Semaphore: `acquire()` decrements permits, `release()` increments
- CountDownLatch: One-time use; threads wait for count to reach zero

**Interview Questions:**
- Explain Producer-Consumer pattern. (Producer puts items in queue; consumer takes items; both coordinate via queue)
- What is wait-notify? (Thread waits for condition, another thread notifies when condition changes)
- How do you prevent deadlock? (Acquire locks in consistent order, use timeouts, avoid nested locks)
- Difference between CountDownLatch and CyclicBarrier? (CountDownLatch: one-time; CyclicBarrier: reusable, all wait for all)

**Hands-On:**
```java
// Producer-Consumer with wait-notify
class ProducerConsumer {
  private Queue<Integer> queue = new LinkedList<>();
  private final int MAX = 10;
  
  public synchronized void produce(int item) throws InterruptedException {
    while (queue.size() == MAX) {
      wait(); // Wait if queue is full
    }
    queue.add(item);
    notifyAll(); // Wake consumer
  }
  
  public synchronized int consume() throws InterruptedException {
    while (queue.isEmpty()) {
      wait(); // Wait if queue is empty
    }
    int item = queue.poll();
    notifyAll(); // Wake producer
    return item;
  }
}

// CountDownLatch: wait for N tasks to complete
CountDownLatch latch = new CountDownLatch(3);
for (int i = 0; i < 3; i++) {
  new Thread(() -> {
    try {
      // Do work
      System.out.println("Task " + Thread.currentThread().getName() + " done");
    } finally {
      latch.countDown(); // Decrement count
    }
  }).start();
}
latch.await(); // Main thread waits for all 3 to complete
System.out.println("All tasks completed");

// Semaphore: limit concurrent access
Semaphore semaphore = new Semaphore(3); // Max 3 concurrent threads
for (int i = 0; i < 10; i++) {
  new Thread(() -> {
    try {
      semaphore.acquire();
      System.out.println(Thread.currentThread().getName() + " using resource");
      Thread.sleep(1000);
    } catch (InterruptedException e) {
      Thread.currentThread().interrupt();
    } finally {
      semaphore.release();
    }
  }).start();
}
```

---

### 5.4 Thread-Safe Collections
- **Legacy thread-safe collections:** Vector, Hashtable, Stack (synchronized, slow)
- **Collections wrapper:** `Collections.synchronizedList()`, `synchronizedMap()`, etc.
- **Concurrent collections:**
  - **ConcurrentHashMap:** Segment-based locking, better concurrency than Hashtable
  - **CopyOnWriteArrayList:** Thread-safe for reads; expensive writes (copy-on-write)
  - **BlockingQueue:** Thread-safe queue with blocking put/take operations
    - LinkedBlockingQueue, ArrayBlockingQueue, PriorityBlockingQueue
  - **ConcurrentLinkedQueue:** Lock-free queue, high concurrency
  - **ConcurrentSkipListMap:** Concurrent sorted map
  - **ConcurrentSkipListSet:** Concurrent sorted set

**Key Concepts:**
- ConcurrentHashMap: Multiple locks (one per segment), threads can modify different segments simultaneously
- CopyOnWriteArrayList: Efficient reads, expensive writes; good for read-heavy workloads
- BlockingQueue: Built-in coordination for producer-consumer; blocks if full/empty
- Iterators on concurrent collections may show intermediate state (snapshot semantics)

**Interview Questions:**
- When should you use ConcurrentHashMap vs Hashtable? (ConcurrentHashMap: better concurrency; Hashtable: legacy)
- What is copy-on-write? (Creates new array copy on modification; reads see previous version; expensive but thread-safe)
- Why is BlockingQueue useful? (Built-in waiting for produce-consume; no need for wait/notify)

**Hands-On:**
```java
// ConcurrentHashMap: segment-based locking
Map<String, Integer> map = new ConcurrentHashMap<>();
map.put("user1", 1);
map.putIfAbsent("user1", 2); // Atomic operation

// BlockingQueue for producer-consumer
BlockingQueue<String> queue = new LinkedBlockingQueue<>(10);

// Producer thread
new Thread(() -> {
  try {
    queue.put("item1");
    queue.put("item2");
  } catch (InterruptedException e) {
    Thread.currentThread().interrupt();
  }
}).start();

// Consumer thread
new Thread(() -> {
  try {
    String item = queue.take(); // Blocks if empty
    System.out.println("Consumed: " + item);
  } catch (InterruptedException e) {
    Thread.currentThread().interrupt();
  }
}).start();

// CopyOnWriteArrayList
List<String> list = new CopyOnWriteArrayList<>();
list.add("a");
list.add("b");
// Safe for concurrent iteration even during modifications
for (String s : list) {
  System.out.println(s);
}
```

---

### 5.5 ExecutorService & Thread Pools
- **Thread pools:** Reuse threads, avoid creation overhead
- **ExecutorService:** Manager for thread pool, lifecycle management
  - `Executors.newFixedThreadPool(n)`: Fixed number of threads
  - `Executors.newCachedThreadPool()`: Dynamic threads, reuse idle
  - `Executors.newSingleThreadExecutor()`: Single thread, serialized tasks
  - `Executors.newScheduledThreadPool(n)`: Schedule periodic tasks
- **Submit tasks:** execute() (fire-forget), submit() (returns Future)
- **Future:** Get result, wait for completion, cancel task
- **Callable vs Runnable:** Callable returns result; Runnable returns void

**Key Concepts:**
- Thread pools improve efficiency; avoid creating new threads each time
- Fixed thread pool: Consistent resource usage; if tasks queue, no more threads created
- Cached thread pool: Dynamic; creates threads as needed, reuses idle threads
- shutdown() vs shutdownNow(): shutdown() waits for tasks; shutdownNow() cancels pending tasks

**Interview Questions:**
- Why use thread pools? (Reuse threads, control resource usage, manage lifecycle)
- Difference between execute() and submit()? (execute(): void, fire-forget; submit(): returns Future)
- What happens if you submit 100 tasks to a fixed pool of 5 threads? (95 tasks queue; executed as threads become available)

**Hands-On:**
```java
// Fixed thread pool
ExecutorService executor = Executors.newFixedThreadPool(3);
for (int i = 0; i < 10; i++) {
  executor.submit(() -> {
    System.out.println(Thread.currentThread().getName());
  });
}
executor.shutdown(); // Prevent new submissions
executor.awaitTermination(10, TimeUnit.SECONDS); // Wait for completion

// Submit task and get Future
Future<Integer> future = executor.submit(() -> {
  return 42;
});
try {
  int result = future.get(5, TimeUnit.SECONDS); // Wait with timeout
  System.out.println("Result: " + result);
} catch (TimeoutException e) {
  future.cancel(true); // Cancel if takes too long
}

// Scheduled executor
ScheduledExecutorService scheduler = Executors.newScheduledThreadPool(1);
scheduler.scheduleAtFixedRate(() -> {
  System.out.println("Periodic task");
}, 0, 1, TimeUnit.SECONDS); // Initial delay 0, repeat every 1 second

// Shutdown
scheduler.shutdown();
```

---

## Module 6: Memory Management & Garbage Collection (8 hours)

### 6.1 Java Memory Model
- **Heap:** Object allocation, shared among all threads
- **Stack:** Local variables, method calls; each thread has own stack
- **String pool:** String literals interned
- **Method area (PermGen/Metaspace):** Class definitions, constant pool
- **Memory diagram:** Understanding stack vs heap allocation

**Key Concepts:**
- Primitives on stack; objects on heap (reference on stack, object on heap)
- Objects referenced multiple times share same heap allocation
- Stack memory is faster (pointer increment); heap requires garbage collection

**Interview Questions:**
- Stack vs heap? (Stack: local variables, faster; heap: objects, GC-managed)
- What is the string pool? (Interning; identical literal strings point to same object)

---

### 6.2 Garbage Collection Basics
- **GC algorithm:** Mark-sweep, generational (Young/Old generations)
- **Generational hypothesis:** Young objects die more quickly; separate young/old spaces
- **Young generation:** Eden, Survivor (S0, S1) spaces; frequent GC
- **Old generation:** Older objects; less frequent GC
- **GC triggers:** Memory pressure, explicit System.gc() (not recommended)
- **GC impact:** Stop-the-world pauses; application threads pause during GC

**Key Concepts:**
- Generational GC: Most objects die young; don't scan old generation every time
- Young GC (Minor GC): Fast, frequent; copies live objects to survivor space
- Full GC (Major GC): Slow, infrequent; scans entire heap
- GC tuning: Heap sizes (-Xmx), generation sizes, GC algorithm selection

**Interview Questions:**
- How does generational GC work? (Young objects die quickly; separate young/old spaces; frequent young GC, infrequent full GC)
- What triggers garbage collection? (Memory pressure; never rely on System.gc())
- What is stop-the-world? (All application threads pause during GC; can impact latency)

---

### 6.3 Memory Leaks & Object Lifecycle
- **Memory leaks:** Objects no longer needed but not garbage collected
- **Common causes:** Static collections accumulating objects, listeners not unregistered, circular references
- **Weak references:** WeakReference, SoftReference for GC-able references
- **Object finalization:** finalize() method (deprecated); use try-with-resources instead

**Key Concepts:**
- GC reachability: Object is collectable only if no strong references exist
- WeakReference: Eligible for GC even if referenced
- SoftReference: Cleared by GC if memory is low
- Avoid finalize(); use AutoCloseable + try-with-resources

**Interview Questions:**
- What is a memory leak? (Object no longer used but not GC'd due to lingering references)
- How to detect memory leaks? (Memory profiler; objects that should be GC'd remain)
- What are weak references? (Objects referenced by WeakReference can be GC'd; useful for caches)

**Hands-On:**
```java
// Memory leak: static list accumulating objects
class BadCache {
  private static List<String> cache = new ArrayList<>();
  
  public void addToCache(String item) {
    cache.add(item); // Never removed; memory leak
  }
}

// Better: use WeakReference or bounded cache
class BetterCache {
  private final Map<String, WeakReference<String>> cache = new HashMap<>();
  
  public void put(String key, String value) {
    cache.put(key, new WeakReference<>(value));
  }
  
  public String get(String key) {
    WeakReference<String> ref = cache.get(key);
    return ref != null ? ref.get() : null; // May be null if GC'd
  }
}
```

---

## Module 7: I/O & Networking (10 hours)

### 7.1 Streams & File I/O
- **Byte streams:** InputStream, OutputStream (low-level)
  - FileInputStream, FileOutputStream
  - BufferedInputStream, BufferedOutputStream
- **Character streams:** Reader, Writer (for text)
  - FileReader, FileWriter
  - BufferedReader, BufferedWriter
- **Buffering:** Improves performance by reducing I/O operations
- **File class:** File operations, metadata
- **NIO (java.nio):** Non-blocking I/O, channels, buffers (newer, more efficient)
  - FileChannel, ByteBuffer, Selector

**Key Concepts:**
- Byte streams for binary data; character streams for text
- Buffering: Buffer data in memory, write in chunks (reduces disk I/O)
- NIO: Selector-based multiplexing, useful for high-concurrency scenarios
- Try-with-resources: Automatic resource cleanup

**Interview Questions:**
- Byte vs character streams? (Byte: binary, single byte; character: text, handles encoding)
- When should you use buffering? (When reading/writing large files; improves throughput)
- What is NIO? (Non-blocking I/O; channels, buffers, selectors; useful for high concurrency)

**Hands-On:**
```java
// Try-with-resources for automatic cleanup
try (BufferedReader reader = new BufferedReader(new FileReader("file.txt"))) {
  String line;
  while ((line = reader.readLine()) != null) {
    System.out.println(line);
  }
} catch (IOException e) {
  e.printStackTrace();
}

// NIO: reading file with channels and buffers
try (RandomAccessFile file = new RandomAccessFile("file.txt", "r")) {
  FileChannel channel = file.getChannel();
  ByteBuffer buffer = ByteBuffer.allocate(1024);
  
  while (channel.read(buffer) > 0) {
    buffer.flip(); // Prepare for reading
    while (buffer.hasRemaining()) {
      System.out.print((char) buffer.get());
    }
    buffer.clear(); // Reset for next read
  }
} catch (IOException e) {
  e.printStackTrace();
}
```

---

### 7.2 Serialization & Deserialization
- **Serialization:** Converting object to byte stream for storage/transmission
- **Serializable interface:** Mark class as serializable; affects JVM behavior
- **serialVersionUID:** Version control for serialized classes
- **Transient keyword:** Mark fields to exclude from serialization
- **Externalizable interface:** Custom serialization logic

**Key Concepts:**
- Serialization is not magic; affected by class hierarchy, transient fields, version compatibility
- serialVersionUID: Used to detect version mismatch; change when class structure changes
- Transient fields are not serialized; useful for sensitive data or computed fields
- Serialization can have security implications; be cautious with untrusted data

**Interview Questions:**
- What is serialization? (Converting object to bytes for storage/transmission)
- Why is serialVersionUID needed? (Version control; detect incompatibility between serialized and current class)
- When should you mark a field transient? (Sensitive data, computed fields, non-serializable objects)

**Hands-On:**
```java
public class User implements Serializable {
  private static final long serialVersionUID = 1L;
  
  private String name;
  private transient String password; // Not serialized
  private int age;
  
  // Getters, setters
}

// Serialize
User user = new User("Alice", "secret", 30);
ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("user.dat"));
oos.writeObject(user);
oos.close();

// Deserialize
ObjectInputStream ois = new ObjectInputStream(new FileInputStream("user.dat"));
User deserializedUser = (User) ois.readObject();
ois.close();
System.out.println(deserializedUser.getPassword()); // null (transient)
```

---

## Module 8: Java 8+ Features (12 hours)

### 8.1 Lambda Expressions & Functional Interfaces
- **Functional interface:** Single abstract method; can be implemented with lambda
- **Lambda syntax:** `(parameters) -> expression` or `(parameters) -> { statements; }`
- **Method references:** Shorthand for lambda calling single method
  - Static method: `Class::staticMethod`
  - Instance method: `object::instanceMethod` or `Class::instanceMethod`
  - Constructor: `ClassName::new`
- **Built-in functional interfaces:** Predicate, Consumer, Function, Supplier, etc.

**Key Concepts:**
- Lambda: Cleaner syntax for anonymous inner classes for functional interfaces
- Type inference: Compiler infers parameter types from context
- Method references: Even more concise when lambda just calls a method
- Functional interfaces enable functional programming style in Java

**Interview Questions:**
- What is a functional interface? (Single abstract method; can be implemented with lambda)
- What is a lambda expression? (Anonymous function; cleaner syntax for single-method interfaces)
- When would you use method references? (When lambda just calls a method; more concise)

**Hands-On:**
```java
// Functional interface
@FunctionalInterface
interface Validator {
  boolean validate(String input);
}

// Lambda implementation
Validator emailValidator = (String email) -> email.contains("@");
System.out.println(emailValidator.validate("user@example.com")); // true

// Method reference
Function<String, Integer> stringToInt = Integer::parseInt;
int num = stringToInt.apply("42");

// Built-in functional interfaces
Predicate<Integer> isPositive = n -> n > 0;
Consumer<String> printer = System.out::println;
Function<Integer, Integer> square = n -> n * n;
Supplier<UUID> uuidSupplier = UUID::randomUUID;

// Using with streams
List<Integer> numbers = Arrays.asList(1, -2, 3, -4, 5);
numbers.stream()
  .filter(isPositive) // Predicate
  .map(square) // Function
  .forEach(printer); // Consumer
```

---

### 8.2 Optional (Null-Safety)
- **Optional:** Container for value that may or may not be present
- **Creating Optional:** Optional.of(), Optional.ofNullable(), Optional.empty()
- **Operations:** get(), orElse(), orElseThrow(), ifPresent(), ifPresentOrElse(), map(), flatMap(), filter()
- **Chaining:** Fluent API for optional operations

**Key Concepts:**
- Optional prevents NullPointerException; makes null handling explicit
- Never do Optional.get() without checking; defeats purpose
- Use map(), flatMap() for chaining operations on optional values
- Avoid Optional<List>; use empty list instead

**Interview Questions:**
- What is Optional? (Container for optional values; prevents NullPointerException)
- When should you use Optional? (When method may return null; API design)
- What is the difference between map() and flatMap()? (map(): applies function; flatMap(): flattens nested optionals)

**Hands-On:**
```java
// Optional basics
Optional<String> name = Optional.of("Alice");
System.out.println(name.get()); // "Alice"

Optional<String> empty = Optional.empty();
System.out.println(empty.orElse("Default")); // "Default"

// Chaining with map
Optional<Integer> maybeAge = Optional.of(30);
maybeAge
  .filter(age -> age >= 18)
  .map(age -> age + 5)
  .ifPresent(System.out::println); // 35

// flatMap for nested optionals
Optional<User> user = getUserById(1);
Optional<String> email = user
  .flatMap(u -> getEmailForUser(u)) // flatMap flattens Optional<Optional<String>> to Optional<String>
  .filter(e -> e.contains("@"));
```

---

### 8.3 Date & Time API (java.time)
- **LocalDate:** Date without time (2025-04-22)
- **LocalTime:** Time without date (10:30:45)
- **LocalDateTime:** Date and time (2025-04-22T10:30:45)
- **ZonedDateTime:** Date, time, and timezone
- **Instant:** Moment in UTC
- **Duration:** Time interval
- **Period:** Date interval
- **Formatting:** DateTimeFormatter for parsing and formatting

**Key Concepts:**
- java.time is immutable; all operations return new instances
- LocalDateTime is default for server-side logic; use ZonedDateTime when timezone matters
- Instant is for storing in database/transmission (UTC-based)
- DateTimeFormatter for custom date formatting

**Interview Questions:**
- Why is java.time better than Date/Calendar? (Immutable, thread-safe, cleaner API)
- Difference between LocalDateTime and Instant? (LocalDateTime: no timezone; Instant: UTC-based)
- How do you handle timezones? (Use ZonedDateTime or convert Instant to local time)

**Hands-On:**
```java
// Date/time creation and operations
LocalDate today = LocalDate.now();
LocalDate birthday = LocalDate.of(1990, 5, 15);
LocalDateTime appointmentTime = LocalDateTime.of(today, LocalTime.of(14, 30));

// Arithmetic
LocalDate nextWeek = today.plusWeeks(1);
LocalDate lastMonth = today.minusMonths(1);

// Duration and Period
Duration between = Duration.between(
  LocalDateTime.of(2025, 4, 22, 10, 0),
  LocalDateTime.of(2025, 4, 22, 15, 30)
);
System.out.println(between.getMinutes()); // 330

// Timezone handling
ZonedDateTime nowInIST = ZonedDateTime.now(ZoneId.of("Asia/Kolkata"));
ZonedDateTime nowInUTC = nowInIST.withZoneSameInstant(ZoneId.of("UTC"));

// Formatting
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("dd/MM/yyyy HH:mm:ss");
String formatted = appointmentTime.format(formatter); // "22/04/2025 14:30:00"
```

---

## Module 9: Advanced Topics (10 hours)

### 9.1 Reflection API
- **Class object:** Metadata about class
- **Methods, fields, constructors:** Introspection
- **Creating instances dynamically:** Constructor.newInstance()
- **Invoking methods:** Method.invoke()
- **Use cases:** Frameworks (Spring, Hibernate), testing, annotations

**Key Concepts:**
- Reflection allows runtime introspection; used by frameworks
- Performance cost: reflection is slower than direct calls
- Security implications: can bypass access modifiers

**Hands-On:**
```java
// Getting class object
Class<?> clazz = User.class;
// OR
Class<?> clazz = Class.forName("com.example.User");

// Getting methods, fields, constructors
Method[] methods = clazz.getDeclaredMethods();
Field[] fields = clazz.getDeclaredFields();
Constructor<?>[] constructors = clazz.getDeclaredConstructors();

// Creating instance dynamically
Constructor<?> constructor = clazz.getConstructor(String.class);
Object instance = constructor.newInstance("Alice");

// Invoking method
Method setAge = clazz.getMethod("setAge", int.class);
setAge.invoke(instance, 30);
```

---

### 9.2 Annotations
- **Built-in annotations:** @Override, @Deprecated, @SuppressWarnings, @FunctionalInterface
- **Meta-annotations:** @Target, @Retention, @Documented, @Inherited
- **Custom annotations:** Define your own for domain-specific use
- **Annotation processing:** Runtime vs compile-time processing

**Hands-On:**
```java
// Custom annotation
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface LogExecution {
  String value() default "Executing method";
}

// Using annotation
class UserService {
  @LogExecution("Getting user by ID")
  public User getUserById(int id) {
    return new User(id, "Alice");
  }
}

// Processing at runtime
Method method = UserService.class.getMethod("getUserById", int.class);
if (method.isAnnotationPresent(LogExecution.class)) {
  LogExecution log = method.getAnnotation(LogExecution.class);
  System.out.println(log.value());
}
```

---

### 9.3 Generics
- **Generic types:** Type-safe collections and classes
- **Type parameters:** `<T>`, `<E>`, `<K, V>`
- **Bounded types:** `<T extends Number>`, `<? extends Animal>`
- **Type erasure:** Generics are erased at runtime
- **Wildcard types:** `<?>`, `<? extends Type>`, `<? super Type>`

**Key Concepts:**
- Generics provide compile-time type safety; remove casting
- Type erasure: Generic info removed at runtime; can't do `instanceof List<String>`
- Bounded wildcards: `<? extends Type>` for read-only; `<? super Type>` for write-only

**Hands-On:**
```java
// Generic class
class Container<T> {
  private T value;
  
  public void set(T value) {
    this.value = value;
  }
  
  public T get() {
    return value;
  }
}

Container<String> stringContainer = new Container<>();
stringContainer.set("hello");
String value = stringContainer.get(); // No casting needed

// Bounded generic
<T extends Comparable<T>> T max(List<T> list) {
  // T must implement Comparable
}

// Wildcard types
void printList(List<?> list) {
  for (Object obj : list) {
    System.out.println(obj);
  }
}

void addNumbers(List<? super Number> list) {
  list.add(1);
  list.add(2.5);
}
```

---

## Module 10: Production Patterns & Best Practices (8 hours)

### 10.1 Design Patterns for Backend
- **Singleton:** One instance; lazy initialization, thread-safe
- **Factory:** Create objects without specifying exact classes
- **Builder:** Complex object construction with optional parameters
- **Strategy:** Encapsulate algorithms; choose at runtime
- **Observer:** Event-driven communication between objects
- **Adapter:** Make incompatible interfaces compatible
- **Decorator:** Dynamically add behavior to objects

**Interview Questions:**
- When would you use Singleton? (Shared resource; logging, configuration)
- Difference between Factory and Builder? (Factory: simple creation; Builder: complex, optional parameters)
- What is the Observer pattern? (Event-driven; observers notified of state changes)

**Hands-On:**
```java
// Singleton
public class Logger {
  private static Logger instance;
  
  private Logger() {}
  
  public static synchronized Logger getInstance() {
    if (instance == null) {
      instance = new Logger();
    }
    return instance;
  }
}

// Builder
class User {
  private String name;
  private String email;
  private int age;
  
  public static class Builder {
    private String name;
    private String email;
    private int age;
    
    public Builder name(String name) {
      this.name = name;
      return this;
    }
    
    public Builder email(String email) {
      this.email = email;
      return this;
    }
    
    public Builder age(int age) {
      this.age = age;
      return this;
    }
    
    public User build() {
      User user = new User();
      user.name = this.name;
      user.email = this.email;
      user.age = this.age;
      return user;
    }
  }
}

// Usage
User user = new User.Builder()
  .name("Alice")
  .email("alice@example.com")
  .age(30)
  .build();

// Strategy
interface PaymentStrategy {
  void pay(double amount);
}

class CreditCardPayment implements PaymentStrategy {
  @Override
  public void pay(double amount) {
    System.out.println("Paid " + amount + " via credit card");
  }
}

class PaymentProcessor {
  private PaymentStrategy strategy;
  
  public PaymentProcessor(PaymentStrategy strategy) {
    this.strategy = strategy;
  }
  
  public void processPayment(double amount) {
    strategy.pay(amount);
  }
}
```

---

### 10.2 Performance & Optimization
- **Big-O complexity:** Understanding algorithm efficiency
- **Micro-optimizations:** When to optimize (profile first)
- **Caching:** Memoization, caching frequent queries
- **Connection pooling:** Database connection management
- **Lazy initialization:** Create expensive objects on-demand
- **Batch processing:** Reduce network/DB round trips

**Key Concepts:**
- Profile before optimizing; don't guess bottlenecks
- Algorithmic complexity matters more than micro-optimizations
- Caching: Trade memory for speed; invalidation is hard
- Connection pooling: Avoid creating new DB connections each request

---

### 10.3 Testing
- **Unit testing:** JUnit for testing individual methods
- **Mocking:** Mockito for mocking dependencies
- **Integration testing:** Testing components together
- **Test coverage:** Metrics for how much code is tested
- **TDD:** Write tests before implementation

**Hands-On:**
```java
// Unit test with JUnit 5 and Mockito
@ExtendWith(MockitoExtension.class)
class UserServiceTest {
  @InjectMocks
  UserService userService;
  
  @Mock
  UserRepository userRepository;
  
  @Test
  void testGetUserById() {
    // Arrange
    User mockUser = new User(1, "Alice");
    when(userRepository.findById(1)).thenReturn(Optional.of(mockUser));
    
    // Act
    User result = userService.getUserById(1);
    
    // Assert
    assertEquals("Alice", result.getName());
    verify(userRepository, times(1)).findById(1);
  }
}
```

---

## Study Plan (60–80 hours)

### Week 1–2: Fundamentals (16 hours)
- Module 1: Java Fundamentals (8 hours)
- Module 2: OOP (8 hours)
- **Hands-on:** Write 5–10 small programs; run them, understand output

### Week 3: Collections (14 hours)
- Module 3: Collections Framework (14 hours)
- **Hands-on:** Implement a simple task manager using HashMap + ArrayList

### Week 4: Exception Handling & Intro Concurrency (14 hours)
- Module 4: Exception Handling (8 hours)
- Module 5.1–5.2: Thread basics, synchronization (6 hours)
- **Hands-on:** Create multi-threaded program with shared counter; ensure thread-safety

### Week 5–6: Advanced Concurrency (18 hours)
- Module 5.3–5.5: Synchronization patterns, thread-safe collections, thread pools (12 hours)
- Module 6: Memory Management (6 hours)
- **Hands-on:** Implement producer-consumer with BlockingQueue

### Week 7: I/O, Serialization, Java 8+ (16 hours)
- Module 7: I/O & Networking (10 hours)
- Module 8: Java 8+ Features (6 hours)
- **Hands-on:** File reading/writing; stream operations; lambda expressions

### Week 8: Advanced Topics (10 hours)
- Module 9: Reflection, Annotations, Generics (10 hours)
- **Hands-on:** Create custom annotation and process it at runtime

### Week 9+: Patterns & Integration (variable)
- Module 10: Patterns, Performance, Testing (8 hours)
- **Integration:** Build a small project combining multiple concepts

---

## Interview Preparation (Key Topics)

**Be ready to answer these with confidence:**

1. **Collections:** HashMap internals, ArrayList vs LinkedList, ConcurrentHashMap
2. **Concurrency:** Race conditions, synchronized, volatile, ReentrantLock, thread pools, BlockingQueue
3. **Memory:** Stack vs heap, garbage collection, memory leaks
4. **Exception handling:** Checked vs unchecked, try-with-resources
5. **Streams:** Intermediate vs terminal operations, lazy evaluation
6. **OOP:** Inheritance, polymorphism, abstract classes vs interfaces
7. **Java 8+:** Lambda, Optional, date/time API
8. **Design patterns:** Singleton, Builder, Factory, Strategy
9. **Performance:** Big-O, caching, optimization strategies
10. **Testing:** Unit tests, mocking, TDD

---

## Resources

**Books:**
- "Effective Java" (3rd edition) by Joshua Bloch — essential for production practices
- "Java Concurrency in Practice" by Goetz et al. — deep dive into multithreading

**Online:**
- Oracle Java documentation: docs.oracle.com
- Baeldung: baeldung.com (tutorials, in-depth articles)
- Stack Overflow: search for specific problems

**Videos:**
- YouTube channels: Telusko, Java Brains, TechGurukulInside (don't watch passively; code along)

---

## Progress Checklist

After each module, verify:

- [ ] Understand all core concepts
- [ ] Can explain in 2–3 sentences
- [ ] Can code examples without looking at notes
- [ ] Can answer typical interview questions
- [ ] Can debug code using these concepts

---

## Final Notes

This syllabus prioritizes **depth over breadth**. You won't master everything in 80 hours; the goal is solid fundamentals and production awareness.

**Focus on:**
- Understanding WHY, not just HOW
- Real-world relevance; avoid abstract examples
- Hands-on coding; don't just read
- Interview-readiness; be confident explaining concepts

**Avoid:**
- Passive learning (watching without coding)
- Memorizing API; focus on principles
- Over-optimization before profiling
- Ignoring multithreading and concurrency (critical for backend)

---

## Next Steps

1. **This week:** Start Module 1 (Fundamentals); code along
2. **Track progress:** Spend 6–8 hours per week on Core Java
3. **Parallel to microservices project:** Use Core Java concepts you learn in actual Spring Boot code
4. **Mock interviews:** Answer questions on collected concepts after Weeks 4–6

You now have a roadmap. The output is production-ready backend code and interview confidence.