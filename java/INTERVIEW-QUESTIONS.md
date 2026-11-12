# Java Interview Questions and Answers

Target: senior backend roles (3+ years, Spring Boot, microservices, Kafka, Docker/K8s, AWS).

Each question has a short answer, a simple explanation, code or a diagram, and follow-up questions with brief answers.
Version-specific facts are marked with the Java version. Code examples with a stated result were compiled and run on JDK 17. Examples that need Java 18 or later (pattern switch, record patterns, sequenced collections, virtual threads) were checked by reading only.

Question IDs: `Q1` to `Q41` are topics 1 to 4, `L` = lambdas/streams/Optional, `M` = modern Java, `G` = generics, `D` = design patterns.

---

## Index

- [1. JVM and Garbage Collection](#1-jvm-internals-and-garbage-collection)
  - [Basic](#11-basic): Q1 JDK/JRE/JVM and how code runs, Q2 Memory areas, Q3 What is GC
  - [Intermediate](#12-intermediate): Q4 Class loading, Q5 Generational heap and GC types, Q6 JIT, Q7 G1 vs ZGC vs Parallel, Q8 Memory leaks
  - [Advanced](#13-advanced): Q9 Debugging OutOfMemoryError, Q10 JVM flags and containers, Q11 Object layout and escape analysis
- [2. Concurrency and Java Memory Model](#2-concurrency-and-java-memory-model)
  - [Basic](#21-basic): Q12 Thread vs Runnable, Q13 synchronized, Q14 volatile, Q15 Race condition
  - [Intermediate](#22-intermediate): Q16 Happens-before, Q17 Atomics and CAS, Q18 ThreadPoolExecutor, Q19 CompletableFuture, Q20 Deadlock, Q21 ThreadLocal
  - [Advanced](#23-advanced): Q22 ConcurrentHashMap internals, Q23 ReentrantLock vs synchronized, Q24 Virtual threads, Q25 Double-checked locking
- [3. Collections](#3-collections)
  - [Basic](#31-basic): Q26 ArrayList vs LinkedList, Q27 Comparable vs Comparator
  - [Intermediate](#32-intermediate): Q28 HashMap internals, Q29 equals/hashCode, Q30 Fail-fast iterators, Q31 HashSet/TreeMap/LinkedHashMap
  - [Advanced](#33-advanced): Q32 HashMap resize, treeify, thread safety, Q33 LRU cache
- [4. OOP, Strings, Exceptions, Immutability](#4-oop-strings-exceptions-immutability)
  - [Basic](#41-basic): Q34 Abstract class vs interface, Q35 Overloading vs overriding, Q36 String pool and StringBuilder
  - [Intermediate](#42-intermediate): Q37 Pass-by-value and Integer cache, Q38 Checked vs unchecked, Q39 try-with-resources and finally, Q40 SOLID
  - [Advanced](#43-advanced): Q41 Immutable class design
- [5. Lambdas, Functional Interfaces, Method References, Streams, Optional](#5-lambdas-functional-interfaces-method-references-streams-optional)
  - [Basic](#lambdas-and-streams-basic): L1 to L8
  - [Intermediate](#lambdas-and-streams-intermediate): L9 to L17
  - [Advanced](#lambdas-and-streams-advanced): L18 to L24
- [6. Modern Java: records, sealed, enums, switch, patterns, var, versions](#6-modern-java-records-sealed-enums-switch-patterns-var-versions)
  - [Basic](#modern-java-basic): M1 to M4
  - [Intermediate](#modern-java-intermediate): M5 to M8
  - [Advanced](#modern-java-advanced): M9 to M10
- [7. Generics](#7-generics)
  - [Basic](#generics-basic): G1
  - [Intermediate](#generics-intermediate): G2
  - [Advanced](#generics-advanced): G3 to G4
- [8. Design Patterns](#8-design-patterns)
  - [Basic](#design-patterns-basic): D1 to D4
  - [Intermediate](#design-patterns-intermediate): D5 to D6
  - [Advanced](#design-patterns-advanced): D7

---

# 1. JVM internals and Garbage Collection

## 1.1 Basic

### Q1. What is the difference between JDK, JRE and JVM, and how does Java code run?

**Short answer:** The JVM runs bytecode. The JRE is the JVM plus the standard libraries. The JDK is the JRE plus developer tools such as `javac`.

**Explanation:**
- `javac` compiles `.java` source into `.class` files. These contain **bytecode**, a platform-neutral instruction set.
- The JVM loads the bytecode and runs it. It first interprets it, then compiles hot (often used) code to machine code with the JIT compiler.
- This is why the same `.class` file runs on Linux, Windows and macOS: only the JVM is platform specific.
- Since Java 9 there is no separate JRE download in most distributions. You ship a JDK or a custom runtime built with `jlink`.

```text
Hello.java --javac--> Hello.class (bytecode) --> JVM
                                                   |
                       +---------------------------+---------------------+
                       | Class loader -> Memory areas -> Execution engine |
                       |                               (interpreter + JIT)|
                       +--------------------------------------------------+
                                                   |
                                             Machine code / OS
```

You can inspect bytecode:

```text
javap -c Hello.class
```

**Follow-up questions:**
- **What is bytecode?** A compact instruction set (like `iload`, `invokevirtual`) for a stack-based virtual machine. It is not machine code.
- **Is Java compiled or interpreted?** Both. Compiled to bytecode first, then interpreted and JIT-compiled at run time.
- **What does "write once, run anywhere" depend on?** A JVM existing for the target platform.

### Q2. What are the JVM memory areas: heap, stack, metaspace?

**Short answer:** The heap holds objects and is shared by all threads. Each thread has its own stack for method calls and local variables. Metaspace holds class metadata and lives in native memory.

**Explanation:**

| Area | What it stores | Shared between threads? | Error when full |
|---|---|---|---|
| Heap | All objects and arrays | Yes | `OutOfMemoryError: Java heap space` |
| Thread stack | One frame per method call: local variables, parameters, return address | No (one per thread) | `StackOverflowError` |
| Metaspace | Class metadata: method code, field info, constant pool (Java 8+) | Yes | `OutOfMemoryError: Metaspace` |
| Code cache | Machine code produced by the JIT | Yes | JIT stops compiling |
| PC register | Address of the current instruction | No | - |
| Direct (off-heap) memory | NIO direct buffers, used by Netty, Kafka clients | Yes | `OutOfMemoryError: Direct buffer memory` |

- Local primitives live in the stack frame. Object references are in the frame, but the object itself is on the heap.
- Before Java 8, class metadata was in PermGen, a fixed-size heap area. Java 8 replaced it with Metaspace, which grows in native memory by default.

```java
void demo() {
    int count = 5;                       // count: stack
    Order order = new Order();           // order (reference): stack, Order object: heap
}
```

```text
Thread 1 stack      Thread 2 stack          Heap (shared)
+-------------+     +-------------+     +----------------------+
| demo()      |     | run()       |     |  Order object        |
|  order  ----+-----+--> ---------+---->|  fields ...          |
|  count = 5  |     |             |     +----------------------+
+-------------+     +-------------+
```

**Follow-up questions:**
- **What causes StackOverflowError?** Too-deep or infinite recursion. Each call adds a frame. The stack size is set with `-Xss`.
- **Is the String pool in PermGen?** Not since Java 7. It is on the heap.
- **Why was PermGen removed?** It had a fixed size, which caused `OutOfMemoryError: PermGen space` in apps that load many classes.

### Q3. What is garbage collection and why is it needed?

**Short answer:** Garbage collection (GC) automatically frees memory of objects that are no longer reachable. It removes the need for manual `free`, and avoids leaks from forgotten frees and crashes from double frees.

**Explanation:**
- An object is **reachable** if some chain of references leads to it from a **GC root**.
- GC roots include: local variables on thread stacks, static fields, active threads, and JNI references.
- Unreachable objects are garbage. GC reclaims their memory.
- Setting a reference to `null` only helps if that was the last reference.

```text
GC roots            Heap
[stack var] ---> A ---> B
[static] ------> C         D ---> E     <- D and E: no path from a root = garbage
```

**Follow-up questions:**
- **Can you force GC?** `System.gc()` is only a hint. Avoid it in production code.
- **What is a stop-the-world (STW) pause?** A moment when GC pauses all application threads to do safe work. GC tuning mostly tries to make these short.
- **Does GC prevent all memory leaks?** No. See Q8.

---

## 1.2 Intermediate

### Q4. How does class loading work? What is parent delegation?

**Short answer:** A class loader finds and loads `.class` files. With parent delegation, a loader first asks its parent to load the class and only loads it itself if the parent cannot.

**Explanation:**

Class loading steps:
1. **Loading:** read the bytecode and create a `Class` object.
2. **Linking:** *verify* the bytecode is valid, *prepare* static fields with default values, *resolve* symbolic references.
3. **Initialization:** run static initializers and assign real static values. This happens on first use of the class.

Built-in loaders (Java 9+ names):

| Loader | What it loads |
|---|---|
| Bootstrap | Core classes such as `java.lang.*` (written in native code, shows as `null`) |
| Platform (called Extension before Java 9) | Platform modules, for example `java.sql` |
| Application (System) | Classes from your classpath or module path |

```text
Application loader
   asks parent -> Platform loader
                     asks parent -> Bootstrap loader
                                       found? -> use it
                                    not found -> Platform tries
                  not found -> Application loader tries itself
```

Why delegation:
- **Security:** you cannot replace `java.lang.String` with your own class. Bootstrap always wins.
- **No duplicates:** a class loaded once by a parent is shared.

```java
public class LoaderDemo {

    public static void main(String[] args) {
        System.out.println(String.class.getClassLoader());
        // null (Bootstrap loader is shown as null)

        System.out.println(LoaderDemo.class.getClassLoader());
        // prints the application class loader
    }
}
```

**Follow-up questions:**
- **When do you see `ClassNotFoundException` vs `NoClassDefFoundError`?** `ClassNotFoundException` is thrown when code asks for a class by name (for example `Class.forName`) and it is not found. `NoClassDefFoundError` is thrown when a class existed at compile time but is missing at run time, or its static initializer failed earlier.
- **Where do custom class loaders appear?** Application servers, plugin systems, Spring Boot fat-jar loading, hot reload tools. They often break parent delegation so each app has its own library versions.
- **Can the same class be loaded twice?** Yes, by two different loaders. They are then different types, which causes odd `ClassCastException` errors.

### Q5. What is the generational heap? Explain minor, major and full GC.

**Short answer:** The heap is split by object age. Most objects die young, so GC cleans the small young area often and cheaply (minor GC), and the large old area rarely (major or full GC).

**Explanation:**
- **Young generation:** new objects go to **Eden**. Survivors are copied between two **Survivor** spaces (S0, S1).
- Each time an object survives a minor GC, its **age** increases. After a threshold it is **promoted** (moved) to the old generation.
- **Old generation (Tenured):** long-lived objects.
- **Minor GC:** cleans only the young generation. Fast. Still a stop-the-world pause.
- **Major GC:** cleans the old generation. The term is used loosely.
- **Full GC:** cleans the whole heap (young + old) and often metaspace. The slowest. Many full GCs in a row is a red flag.

```text
Heap
+------------------------------+---------------------------+
|        Young generation      |     Old generation        |
|  +--------+----+----+        |   long-lived objects      |
|  |  Eden  | S0 | S1 |        |                           |
|  +--------+----+----+        |                           |
+------------------------------+---------------------------+
 new objects -> Eden -> survive -> S0/S1 (age++) -> promote to Old
```

- Note: G1 and ZGC use a region-based heap. The idea of young and old is still there, but they are sets of regions, not fixed blocks.

**Follow-up questions:**
- **What is premature promotion?** Short-lived objects reach the old generation because the young area is too small or survivors overflow. This fills old gen and causes full GCs.
- **What triggers a full GC?** Old gen is full, promotion failure, metaspace exhaustion, explicit `System.gc()`, or a failed concurrent cycle in G1 (the GC log shows "to-space exhausted"; the old CMS collector called it "concurrent mode failure").
- **Why is young GC fast?** It copies only live objects, and few objects survive. Cost depends on live data, not on garbage.

### Q6. What is the JIT compiler?

**Short answer:** The Just-In-Time (JIT) compiler turns frequently executed bytecode into optimized machine code while the app runs, so hot code runs near native speed.

**Explanation:**
- The JVM starts by **interpreting** bytecode. It counts how often methods and loops run.
- Hot methods are compiled. HotSpot uses **tiered compilation**: C1 compiles quickly with light optimization, C2 compiles hot code with heavy optimization using profile data.
- Common optimizations: **inlining** (copy a small method into its caller), loop unrolling, dead code removal, escape analysis.
- If an assumption turns out wrong (for example a new subclass is loaded), the JVM **deoptimizes** and falls back to the interpreter.
- This is why Java apps are slow at the start and get faster ("warm-up"). It matters for benchmarks and for short-lived containers.

```text
bytecode --> interpreter --(hot)--> C1 (fast compile) --(very hot)--> C2 (best code)
                  ^                                                      |
                  +-------------- deoptimize if assumption breaks -------+
```

**Follow-up questions:**
- **How do you benchmark Java properly?** Use JMH. It handles warm-up and prevents the JIT from removing your code as dead.
- **What helps cold start in containers?** Class Data Sharing (CDS), GraalVM native image, and CRaC or similar checkpoint approaches. These are trade-offs, not free.
- **Where is JIT output stored?** In the code cache. You can see it with `-XX:+PrintCompilation`.

### Q7. G1 vs ZGC vs Parallel GC: what is the difference and when to use each?

**Short answer:** Parallel GC maximizes throughput with longer pauses. G1 balances throughput and pause time and is the default since Java 9. ZGC targets very low pauses (sub-millisecond in recent versions) even for huge heaps.

**Explanation:**

| Collector | What it does | Best for |
|---|---|---|
| Serial (`-XX:+UseSerialGC`) | Single-threaded, stop-the-world | Tiny heaps, small containers |
| Parallel (`-XX:+UseParallelGC`) | Many GC threads, stop-the-world | Batch jobs where throughput matters more than pauses |
| G1 (`-XX:+UseG1GC`) | Heap split into equal regions. Collects regions with the most garbage first. Mostly concurrent marking. Pause target set by `-XX:MaxGCPauseMillis` (default 200 ms) | General services. Default |
| ZGC (`-XX:+UseZGC`) | Almost all work is concurrent. Pause time does not grow with heap size | Latency-sensitive services, very large heaps |

```text
G1 heap: many equal regions, each one plays a role
+---+---+---+---+---+---+---+---+
| E | E | S | O | O | H | E | O |    E=Eden  S=Survivor  O=Old  H=Humongous
+---+---+---+---+---+---+---+---+
G1 picks the regions with most garbage ("garbage first") within the pause budget.
```

- **Humongous objects** in G1 are objects larger than half a region. They get special regions and can cause trouble if you allocate many big arrays.
- ZGC became production-ready in Java 15. Generational ZGC arrived in Java 21 (enabled with `-XX:+UseZGC -XX:+ZGenerational`). In later releases generational mode became the default and the old non-generational mode was removed. Check the release you use.
- Parallel GC and G1 are the main choices for typical Spring Boot services. Switch to ZGC only when GC logs show pauses hurting your latency targets.

**Follow-up questions:**
- **What is the cost of ZGC?** It uses some extra CPU and memory for concurrent work. Throughput can be a bit lower than Parallel.
- **What does `MaxGCPauseMillis` do?** It is a goal, not a guarantee. G1 adjusts young size and how many regions it collects to try to meet it.
- **Which GC for a container with 1 CPU and 512 MB?** The JVM may choose Serial automatically on small machines (fewer than 2 CPUs or under about 1792 MB memory).

### Q8. Can Java have memory leaks? Give examples.

**Short answer:** Yes. A leak in Java is an object that is still referenced but never used again, so GC cannot free it. The heap grows until `OutOfMemoryError`.

**Explanation:** Common causes:
- **Static collections** that only grow (a static `Map` used as a cache with no eviction).
- **Listeners or callbacks** registered but never removed.
- **ThreadLocal** values in pooled threads never removed (see Q21).
- **Unclosed resources:** connections, streams, `HttpClient` responses.
- **Bad `equals`/`hashCode` on map keys:** the key never matches, so you keep adding new entries.
- **Inner class or lambda holding the outer object** longer than expected.
- **ClassLoader leaks** on redeploy: classes can never unload (Metaspace grows).

```java
public class Leaky {

    private static final Map<String, byte[]> CACHE = new HashMap<>();

    public static void load(String id) {
        // entries are added but never evicted: memory grows forever
        CACHE.put(id, new byte[1_000_000]);
    }
}
```

Fix: bounded cache with eviction (for example Caffeine or a `LinkedHashMap` LRU, see Q33), use `WeakHashMap` carefully, close resources with try-with-resources.

Typical symptom in GC logs: after each full GC the used heap is a bit higher than the last time (the "sawtooth" trends upward).

**Follow-up questions:**
- **How do you find the leak?** Take heap dumps over time and compare, look at the dominator tree in Eclipse MAT. See Q9.
- **What is a WeakReference?** A reference that does not keep the object alive. GC can free the object when only weak references remain.
- **Is a high heap usage always a leak?** No. The JVM is lazy about collecting. Look at the usage after a full GC.

---

## 1.3 Advanced

### Q9. How do you debug an OutOfMemoryError in production?

**Short answer:** First find which OOM it is from the message. Then use a heap dump (taken automatically on OOM) and GC logs to see what fills the memory. Analyze the dump in Eclipse MAT or similar.

**Explanation:**

OOM messages and what they mean:

| Message | Meaning |
|---|---|
| `Java heap space` | Heap full: leak or heap too small |
| `GC overhead limit exceeded` | JVM spends almost all time in GC and frees almost nothing |
| `Metaspace` | Too many classes loaded: class loader leak or dynamic class generation |
| `unable to create native thread` | OS limit on threads or native memory reached. Too many threads |
| `Direct buffer memory` | Off-heap NIO buffers exceeded `-XX:MaxDirectMemorySize` |
| `Requested array size exceeds VM limit` | Trying to allocate a huge array |

Step by step:
1. Enable heap dump on failure (before the incident):

```text
-XX:+HeapDumpOnOutOfMemoryError
-XX:HeapDumpPath=/dumps/
```

2. Enable GC logs (Java 9+ unified logging):

```text
-Xlog:gc*:file=/logs/gc.log:time,uptime,level,tags:filecount=5,filesize=20m
```

3. Take a dump from a running process if needed:

```text
jcmd <pid> GC.heap_dump /tmp/heap.hprof
jcmd <pid> GC.heap_info
jstat -gcutil <pid> 1000
jstack <pid>
```

4. Open the `.hprof` in **Eclipse MAT** or VisualVM. Look at:
   - **Dominator tree:** which objects keep the most memory alive.
   - **Retained size:** memory that would be freed if the object were removed.
   - **Path to GC roots:** why an object is still reachable.
5. Read GC logs: does heap after GC keep rising (leak)? Are full GCs frequent? Is there a promotion failure?
6. Use **Java Flight Recorder (JFR)** for allocation profiling with low overhead: `jcmd <pid> JFR.start duration=60s filename=rec.jfr`.

In Kubernetes: if the pod is killed with exit code 137 and reason `OOMKilled`, that is the container memory limit being hit (the Linux OOM killer), not a Java `OutOfMemoryError`. The heap is only part of JVM memory (see Q10). Make sure the dump path is on a volume that survives the restart.

**Follow-up questions:**
- **Heap dump is huge and the pod is limited. What now?** Mount a volume, or use `jcmd GC.class_histogram` for a cheap count of objects per class first. Taking a dump pauses the app.
- **`unable to create native thread` but heap is fine?** Count threads (`jstack` or `ps`). Cap pool sizes, check `ulimit -u` and container pid limits, or reduce `-Xss`.
- **Which GC log signs show a leak?** Old gen occupancy after full GC grows over time; full GCs become more frequent.

### Q10. Which JVM flags matter most, and how does the JVM behave in containers?

**Short answer:** Know the heap size flags (`-Xms`, `-Xmx`, `MaxRAMPercentage`), the GC choice flag, GC logging, and the heap dump flags. In containers, size the heap as a percentage of the container limit and leave room for non-heap memory.

**Explanation:**

| Flag | What it does |
|---|---|
| `-Xms` / `-Xmx` | Initial and maximum heap size. Setting them equal avoids resize pauses |
| `-XX:MaxRAMPercentage=75.0` | Max heap as a percent of container or machine memory |
| `-XX:InitialRAMPercentage` | Initial heap as a percent of memory |
| `-Xss` | Stack size per thread |
| `-XX:MaxMetaspaceSize` | Cap on metaspace (unlimited by default) |
| `-XX:MaxDirectMemorySize` | Cap on off-heap direct buffers |
| `-XX:+UseG1GC` / `-XX:+UseZGC` | Choose collector |
| `-XX:MaxGCPauseMillis` | Pause goal for G1 |
| `-XX:+HeapDumpOnOutOfMemoryError` | Write a heap dump on OOM |
| `-XX:+ExitOnOutOfMemoryError` | Exit so Kubernetes restarts the pod instead of leaving a broken JVM |
| `-Xlog:gc*` | GC logging (Java 9+) |

- Since Java 10 the JVM reads container limits (cgroups), so `-Xmx` is not required. The default max heap is 25% of container memory, which is often too low, so teams set `MaxRAMPercentage`.
- **Total JVM memory is more than heap:**

```text
container limit
= heap + metaspace + thread stacks + code cache + direct buffers + GC structures + native libs
```

- Setting heap to 100% of the container limit leads to `OOMKilled`. A common starting point is 60 to 75%.

```text
java -XX:MaxRAMPercentage=70.0 \
     -XX:+UseG1GC \
     -XX:+HeapDumpOnOutOfMemoryError \
     -XX:HeapDumpPath=/dumps \
     -Xlog:gc*:file=/logs/gc.log \
     -jar app.jar
```

**Follow-up questions:**
- **Why set `-Xms` equal to `-Xmx`?** The heap does not need to grow, which avoids resize work and gives stable behavior. The cost is the memory is reserved up front.
- **What is Native Memory Tracking?** `-XX:NativeMemoryTracking=summary` then `jcmd <pid> VM.native_memory` shows non-heap usage.
- **CPU limits in K8s and GC?** The JVM sizes GC and JIT threads from the CPU count it sees. Very low CPU limits slow GC and cause throttling.

### Q11. What is the object layout in memory, and what is escape analysis?

**Short answer:** An object is a header plus its fields plus padding. Escape analysis is a JIT optimization that checks whether an object can be seen outside its method. If not, the JIT can avoid allocating it on the heap.

**Explanation:**
- **Object header:** a mark word (hash code, GC age, lock state) and a class pointer. On 64-bit HotSpot with compressed oops (default for heaps under about 32 GB) the header is typically 12 bytes. Objects are aligned to 8 bytes.
- So `new Object()` is about 16 bytes, and a small object with one `int` is also about 16 bytes.
- **Compressed oops** make references 4 bytes instead of 8, saving memory. This is a reason not to set heaps just above 32 GB.
- **Escape analysis:** if an object is created and only used inside one method, it does not "escape".
  - **Scalar replacement:** the JIT replaces the object with its fields held in registers. No heap allocation, no GC work.
  - **Lock elision:** locks on non-escaping objects are removed.
- It is an optimization the JIT may or may not apply. Do not write code that depends on it.

```java
double distance(double x, double y) {
    Point p = new Point(x, y);
    // p never leaves this method: JIT may avoid allocating it
    return Math.sqrt(p.x() * p.x() + p.y() * p.y());
}
```

**Follow-up questions:**
- **Can Java allocate objects on the stack?** Not directly. Scalar replacement gives a similar effect for non-escaping objects.
- **Why does the 32 GB boundary matter?** Above it the JVM cannot use compressed oops, so references take 8 bytes and the same data uses more memory. A 40 GB heap may hold less than a 31 GB one.
- **How can you measure object size?** The JOL (Java Object Layout) tool prints the layout.

---

# 2. Concurrency and Java Memory Model

## 2.1 Basic

### Q12. How do you create a thread? Thread vs Runnable?

**Short answer:** Implement `Runnable` (or `Callable`) and pass it to a `Thread` or an `ExecutorService`. Do not extend `Thread` in normal code. In production you should rarely create raw threads at all. Use an executor.

**Explanation:**
- `Runnable`: a task, no return value, cannot throw checked exceptions.
- `Callable<V>`: a task that returns a value and can throw checked exceptions. Used with `ExecutorService.submit`, which returns a `Future`.
- `start()` creates a new thread that runs `run()`. Calling `run()` directly runs it in the caller thread. This is a common mistake.
- Thread states: NEW, RUNNABLE, BLOCKED, WAITING, TIMED_WAITING, TERMINATED.

```java
Runnable task = () -> {
    System.out.println("running in " + Thread.currentThread().getName());
};

Thread thread = new Thread(task);
thread.start();
// starts a new thread

ExecutorService executor = Executors.newFixedThreadPool(2);
Future<Integer> future = executor.submit(() -> 21 * 2);
// future.get() returns 42, blocking until the task finishes
executor.shutdown();
```

**Follow-up questions:**
- **What happens if you call `start()` twice?** `IllegalThreadStateException`.
- **`wait()` vs `sleep()`?** `sleep` pauses the thread and keeps its locks. `wait` releases the monitor lock and waits for `notify`. `wait` must be called inside `synchronized`.
- **What is a daemon thread?** A background thread that does not stop the JVM from exiting.

### Q13. What does `synchronized` do?

**Short answer:** It lets only one thread at a time run a block or method for a given lock object. It also makes changes made inside it visible to the next thread that takes the same lock.

**Explanation:**
- Every Java object has a **monitor** (an intrinsic lock).
- `synchronized` instance method locks `this`. `synchronized` static method locks the `Class` object.
- A synchronized block locks the object you name. Prefer a private final lock object.
- It is **reentrant**: a thread holding the lock can enter another synchronized block on the same lock.
- Two guarantees: **mutual exclusion** (one thread at a time) and **visibility** (unlock publishes writes, lock sees them).

```java
public class Counter {

    private int count;
    private final Object lock = new Object();

    public void increment() {
        synchronized (lock) {
            count++;
        }
    }

    public int get() {
        synchronized (lock) {
            return count;
        }
    }
}
```

**Follow-up questions:**
- **Do threads on different instances block each other?** Not with instance-level locks, since each instance has its own monitor. A static synchronized method locks the class, so all instances share it.
- **Why not `synchronized(this)` or a String literal as lock?** Outside code can lock the same object and interfere. String literals are shared through the pool.
- **Cost of synchronized?** Cheap when not contended in modern JVMs. Cost comes from contention.

### Q14. What does `volatile` do?

**Short answer:** `volatile` guarantees that a write to the variable is visible to all threads at once, and prevents reordering around it. It does not make compound actions like `count++` atomic.

**Explanation:**
- Without `volatile`, a thread may keep the value in a CPU cache or register and never see another thread's update.
- A volatile write happens-before every later read of that same variable (see Q16).
- Good use: a status flag, or publishing an immutable object reference.
- Bad use: counters. `count++` is three steps (read, add, write). Use `AtomicInteger` or a lock.

```java
public class Worker implements Runnable {

    private volatile boolean running = true;

    public void stop() {
        running = false;
    }

    @Override
    public void run() {
        while (running) {
            // do work; without volatile this loop might never see running = false
        }
    }
}
```

**Follow-up questions:**
- **volatile vs synchronized?** volatile gives visibility and ordering for one variable. synchronized also gives atomicity for a whole block, and can lock.
- **Does volatile on an array make elements volatile?** No. Only the reference is volatile.
- **Is `long` or `double` read atomically?** Non-volatile 64-bit reads and writes may be split in the Java spec. In practice modern 64-bit JVMs do it atomically, but `volatile` guarantees it.

### Q15. What is a race condition? How do you prevent it?

**Short answer:** A race condition is when the result depends on the timing of threads, usually because several threads read and change shared data without coordination.

**Explanation:**
- Classic case: **check-then-act** (`if (!map.containsKey(k)) map.put(k, v)`) and **read-modify-write** (`count++`).
- Two threads can both read 5 and both write 6, losing one update.

```text
Thread A: read count (5)
Thread B: read count (5)
Thread A: write 6
Thread B: write 6      <- expected 7, got 6
```

Fixes:
- `synchronized` or `Lock` around the critical section.
- Atomic classes (`AtomicInteger`, `LongAdder`).
- Concurrent collections with atomic methods (`putIfAbsent`, `computeIfAbsent`, `merge`).
- Avoid shared mutable state: immutable objects, thread confinement, message passing.
- In databases, use optimistic locking (version column) or `SELECT ... FOR UPDATE`. The same race happens across service instances, where JVM locks do not help.

```java
Map<String, Integer> counts = new ConcurrentHashMap<>();

counts.merge("orders", 1, Integer::sum);
// atomic read-modify-write on one key
```

**Follow-up questions:**
- **Data race vs race condition?** A data race is unsynchronized access to the same variable where at least one is a write. A race condition is any timing-dependent bug. You can have one without the other.
- **Is `ConcurrentHashMap` enough to make `if (!contains) put` safe?** No. Two separate calls are not atomic together. Use `putIfAbsent` or `computeIfAbsent`.
- **Does `synchronized` fix races between two microservice instances?** No. It works inside one JVM. Use DB constraints, optimistic locking, or a distributed lock.

---

## 2.2 Intermediate

### Q16. What is the Java Memory Model and the happens-before relationship?

**Short answer:** The Java Memory Model (JMM) defines when one thread is guaranteed to see another thread's writes. "A happens-before B" means everything A did is visible to B and is ordered before it.

**Explanation:**
- CPUs and the compiler reorder instructions and cache values for speed. Without rules, threads may see stale or out-of-order data.
- The JMM gives specific happens-before rules:

| Rule | Meaning |
|---|---|
| Program order | Within one thread, each action happens-before the next one in code order |
| Monitor lock | An unlock on a monitor happens-before every later lock of the same monitor |
| Volatile | A write to a volatile variable happens-before every later read of it |
| Thread start | `thread.start()` happens-before any action in that thread |
| Thread join | All actions in a thread happen-before `join()` returns in another thread |
| Final fields | Properly constructed objects publish their final fields safely |
| Transitivity | If A hb B and B hb C, then A hb C |

```java
class Publisher {

    private int data;
    private volatile boolean ready;

    void writer() {
        data = 42;          // 1
        ready = true;       // 2 volatile write
    }

    void reader() {
        if (ready) {        // 3 volatile read
            // 1 happens-before 2, 2 happens-before 3, so data is guaranteed to be 42 here
            System.out.println(data);
        }
    }
}
```

**Follow-up questions:**
- **What if `ready` were not volatile?** The reader could see `ready == true` but `data == 0`, because of reordering or caching. Nothing guarantees order.
- **Which `java.util.concurrent` actions create happens-before?** Putting into a concurrent queue happens-before taking from it. Submitting a task to an executor happens-before the task runs. `Future.get()` sees the task's results.
- **What are visibility, atomicity, ordering?** Visibility: seeing others' writes. Atomicity: an action is not split. Ordering: actions appear in a defined order. Concurrency bugs break one of these three.

### Q17. What are atomic classes and CAS?

**Short answer:** Atomic classes (`AtomicInteger`, `AtomicReference`, and others) do thread-safe updates without locks using Compare-And-Swap (CAS), a single CPU instruction that updates a value only if it still has the expected value.

**Explanation:**
- CAS(variable, expected, new): if the variable equals `expected`, set it to `new` and return true. Otherwise return false.
- The caller retries in a loop on failure. This is **lock-free**: no thread is blocked, but threads may retry under contention.

```java
public int incrementAndGet(AtomicInteger value) {
    while (true) {
        int current = value.get();
        int next = current + 1;
        if (value.compareAndSet(current, next)) {
            return next;
        }
        // another thread changed it: retry
    }
}
```

- **ABA problem:** a value changes A to B and back to A. A CAS sees A and succeeds even though it changed. `AtomicStampedReference` adds a version number to detect it.
- **LongAdder** spreads updates across several cells, so it is faster than `AtomicLong` under heavy contention. It is good for counters and metrics, not when you need an exact value for each update.

**Follow-up questions:**
- **When is CAS worse than a lock?** Under very high contention many threads spin and retry, wasting CPU.
- **Why is `AtomicInteger` safe but `volatile int` with `++` not?** CAS makes read-compare-write one atomic step. `volatile` only gives visibility.
- **Where is CAS used inside the JDK?** `ConcurrentHashMap`, `ConcurrentLinkedQueue`, `AtomicXxx`, `ReentrantLock` internals (AQS).

### Q18. Explain the ThreadPoolExecutor parameters and how tasks flow.

**Short answer:** A pool has core threads, a queue, and extra threads up to a max. A new task goes to a core thread first, then the queue, then extra threads, and finally the rejection handler.

**Explanation:**

| Parameter | What it does |
|---|---|
| `corePoolSize` | Number of threads kept alive even when idle |
| `maximumPoolSize` | Upper limit on threads |
| `keepAliveTime` | How long threads above core stay idle before being removed |
| `workQueue` | Holds tasks waiting for a thread (for example `ArrayBlockingQueue`) |
| `threadFactory` | Creates threads. Use it to give names like `order-worker-1` |
| `handler` (RejectedExecutionHandler) | What to do when the pool and queue are both full |

```text
new task
   |
   v
threads < core?  --yes--> start new thread
   | no
   v
queue has room?  --yes--> enqueue
   | no
   v
threads < max?   --yes--> start new thread
   | no
   v
reject (handler)
```

Important: extra threads beyond core are created only after the **queue is full**. With an unbounded queue, the pool never grows past core.

Rejection policies:
- `AbortPolicy` (default): throws `RejectedExecutionException`.
- `CallerRunsPolicy`: the submitting thread runs the task. This slows the producer and gives natural backpressure.
- `DiscardPolicy`: silently drops the task.
- `DiscardOldestPolicy`: drops the oldest queued task and retries.

```java
ThreadPoolExecutor executor = new ThreadPoolExecutor(
    4,                                        // core
    8,                                        // max
    60, TimeUnit.SECONDS,                     // keep alive for extra threads
    new ArrayBlockingQueue<>(100),            // bounded queue
    new ThreadPoolExecutor.CallerRunsPolicy() // when full
);
```

Why `Executors.newFixedThreadPool` is risky: it uses an unbounded `LinkedBlockingQueue`, so a slow consumer can fill memory. `newCachedThreadPool` allows an unbounded number of threads. Prefer building `ThreadPoolExecutor` yourself with a bounded queue.

Sizing rule of thumb:
- CPU-bound work: about number of cores (+1).
- IO-bound work: more, roughly `cores * (1 + wait time / compute time)`. Measure to confirm.

**Follow-up questions:**
- **Shutdown vs shutdownNow?** `shutdown` stops accepting tasks and finishes queued ones. `shutdownNow` interrupts running tasks and returns the queued ones.
- **What happens to exceptions in `submit` vs `execute`?** `submit` stores the exception in the `Future`, so it is silent unless you call `get`. `execute` lets it reach the thread's uncaught exception handler.
- **How do you monitor a pool?** Queue size, active count, completed count, rejected count. Spring's `ThreadPoolTaskExecutor` exposes these, and Micrometer can publish them.

### Q19. How does CompletableFuture work?

**Short answer:** `CompletableFuture` represents a result that will arrive later and lets you chain steps without blocking. It is the standard way to compose async calls in Java 8+.

**Explanation:**
- `supplyAsync` / `runAsync` start async work.
- `thenApply` transforms the result (like `map`). `thenCompose` chains another future (like `flatMap`). `thenCombine` joins two independent futures. `allOf` / `anyOf` wait on many.
- `exceptionally` and `handle` deal with errors.
- Default executor is the shared `ForkJoinPool.commonPool()`. For blocking IO calls pass your own executor, or you can starve the common pool.

```java
ExecutorService ioPool = Executors.newFixedThreadPool(10);

CompletableFuture<User> userFuture = CompletableFuture.supplyAsync(
    () -> userClient.fetch(userId),
    ioPool
);

CompletableFuture<List<Order>> ordersFuture = CompletableFuture.supplyAsync(
    () -> orderClient.fetch(userId),
    ioPool
);

CompletableFuture<Profile> profileFuture = userFuture
    .thenCombine(ordersFuture, (user, orders) -> new Profile(user, orders))
    .exceptionally(ex -> Profile.empty());
// both calls run in parallel, then combine
```

**Follow-up questions:**
- **`thenApply` vs `thenCompose`?** `thenApply` takes a function returning a value. `thenCompose` takes a function returning a `CompletableFuture`, and flattens it so you do not get a nested future.
- **`thenApply` vs `thenApplyAsync`?** The non-async version runs in the thread that completed the previous stage (or the caller if already done). The async version submits to an executor.
- **How to add a timeout?** `orTimeout(2, SECONDS)` or `completeOnTimeout(default, 2, SECONDS)` (Java 9+).
- **Why can `join()` or `get()` be a problem?** They block the thread. Blocking inside a stage on the common pool can cause starvation.

### Q20. What is a deadlock? How do you detect and prevent it?

**Short answer:** A deadlock is when two or more threads wait for each other's locks forever, so none can continue.

**Explanation:**

All four conditions must hold:
1. Mutual exclusion: a lock is held by one thread at a time.
2. Hold and wait: a thread holds one lock while waiting for another.
3. No preemption: locks cannot be taken away.
4. Circular wait: A waits for B, and B waits for A.

```java
// Thread 1                          // Thread 2
synchronized (lockA) {               synchronized (lockB) {
    synchronized (lockB) {               synchronized (lockA) {
        // work                              // work
    }                                    }
}                                    }
```

```text
Thread 1 holds A, wants B
Thread 2 holds B, wants A   -> both wait forever
```

Prevention:
- **Lock ordering:** always take locks in the same global order (for example by account id).
- **tryLock with timeout** (`ReentrantLock.tryLock(1, SECONDS)`), then release and retry.
- Hold locks for the shortest time, avoid calling unknown code (callbacks) while holding a lock.
- Prefer higher-level tools: concurrent collections, single-writer designs.

Detection: take a thread dump with `jstack <pid>` or `jcmd <pid> Thread.print`. The JVM reports "Found one Java-level deadlock" and shows the cycle. `ThreadMXBean.findDeadlockedThreads()` does it from code.

**Follow-up questions:**
- **Deadlock vs livelock vs starvation?** Livelock: threads keep reacting to each other and make no progress. Starvation: a thread never gets the lock or CPU because others always win.
- **Can DB deadlocks happen too?** Yes. Two transactions update rows in opposite order. The DB aborts one. Fix with consistent update order and retry logic.
- **How to find a hung app's cause?** Take several thread dumps a few seconds apart, and look for threads stuck at the same place.

### Q21. What is ThreadLocal and what are its pitfalls?

**Short answer:** `ThreadLocal` gives each thread its own copy of a variable. It is used to carry per-request data (user, trace id, transaction) without passing it through every method.

**Explanation:**
- Each `Thread` holds a map of its ThreadLocal values.
- Spring uses it for `SecurityContextHolder`, transaction synchronization and `RequestContextHolder`. Logging MDC works the same way.
- **Pitfall with thread pools:** pool threads live a long time. If you do not call `remove()`, the old value stays, so the next request on that thread may see stale data (a security issue) and the value cannot be garbage collected (a leak).
- **Async pitfall:** the value does not move to another thread, so context is lost in `CompletableFuture` or `@Async` unless you copy it.

```java
public class RequestContext {

    private static final ThreadLocal<String> USER = new ThreadLocal<>();

    public static void set(String user) {
        USER.set(user);
    }

    public static String get() {
        return USER.get();
    }

    public static void clear() {
        USER.remove();
    }
}

// in a filter
try {
    RequestContext.set(userId);
    chain.doFilter(request, response);
} finally {
    RequestContext.clear();
}
```

**Follow-up questions:**
- **How do you pass context to child threads?** `InheritableThreadLocal` copies at thread creation, but is unreliable with pools. Wrap tasks to copy the context manually, or use a task decorator.
- **ThreadLocal with virtual threads?** It works, but with millions of virtual threads each holds its own copy, which is memory heavy. `ScopedValue` is the newer alternative (preview in Java 21, final in Java 25).
- **Why can ThreadLocal leak even though keys are weak?** The map holds the key weakly, but the value strongly. If the thread lives on, the value stays until `remove()`.

---

## 2.3 Advanced

### Q22. How does ConcurrentHashMap work internally?

**Short answer:** In Java 8+ it uses an array of bins. It inserts into an empty bin with CAS (no lock), and otherwise locks only the first node of that bin with `synchronized`. Reads do not lock.

**Explanation:**
- **Java 7 and earlier:** it used 16 segments, each its own lock. Java 8 replaced this.
- **Put in Java 8+:**
  1. Compute the hash and bin index.
  2. If the bin is empty, insert with CAS.
  3. Else `synchronized` on the bin's head node, then insert in the list or tree.
  4. If the bin gets long, convert to a tree (same rules as HashMap, see Q32).
- **Get:** lock-free. The table and node values are `volatile`, so reads see recent writes.
- **Resize:** multiple threads can help move bins in parallel.
- **Size:** kept in a base counter plus counter cells (like `LongAdder`), so counting does not become a bottleneck. `size()` is approximate while others update.
- **No null keys or values.** With concurrency, `get` returning null could not tell "absent" from "mapped to null".
- Iterators are **weakly consistent**: they do not throw `ConcurrentModificationException` and may or may not show updates made during iteration.

```text
table
 [0] -> null          <- put: CAS to insert, no lock
 [1] -> A -> B        <- put: synchronized(A), then append
 [2] -> TreeBin       <- long bin converted to red-black tree
 [3] -> null
get(): no locks
```

```java
ConcurrentHashMap<String, Long> hits = new ConcurrentHashMap<>();

hits.merge("home", 1L, Long::sum);
// atomic update per key

hits.computeIfAbsent("cart", key -> loadFromDb(key));
// the function runs at most once per absent key; keep it short and do not modify the map inside it
```

**Follow-up questions:**
- **ConcurrentHashMap vs Hashtable vs `Collections.synchronizedMap`?** The last two use one lock for the entire map, so threads queue up. ConcurrentHashMap locks per bin and reads freely.
- **Is a compound call like `get` then `put` safe?** No. Use `compute`, `merge`, `putIfAbsent`.
- **Why can `computeIfAbsent` be dangerous?** A long or blocking function holds the bin lock. Modifying the same map inside it can deadlock or throw (`IllegalStateException: Recursive update` in newer versions).

### Q23. ReentrantLock vs synchronized: when to use which?

**Short answer:** Use `synchronized` by default because it is simple and releases automatically. Use `ReentrantLock` when you need features such as timed or interruptible lock attempts, fairness, or multiple conditions.

**Explanation:**

| Feature | `synchronized` | `ReentrantLock` |
|---|---|---|
| Release | Automatic at block end | Manual, must call `unlock()` in `finally` |
| Try without waiting | No | `tryLock()` |
| Timeout | No | `tryLock(time, unit)` |
| Interruptible wait | No | `lockInterruptibly()` |
| Fair ordering option | No | `new ReentrantLock(true)` |
| Multiple wait conditions | One per monitor | Many `Condition` objects |
| Virtual thread pinning (Java 21) | Can pin the carrier thread when blocking inside | Does not pin |

```java
private final ReentrantLock lock = new ReentrantLock();

public boolean transfer() throws InterruptedException {
    if (!lock.tryLock(1, TimeUnit.SECONDS)) {
        return false;
        // could not get the lock in time
    }
    try {
        // critical section
        return true;
    } finally {
        lock.unlock();
    }
}
```

Related: `ReadWriteLock` allows many readers or one writer. `StampedLock` adds optimistic reads. These help on read-heavy data, but the added complexity is rarely worth it over a concurrent map.

**Follow-up questions:**
- **What does fair mean?** Waiting threads get the lock in arrival order. It prevents starvation but lowers throughput.
- **What happens if you forget `unlock()`?** The lock is never released and other threads block forever. That is why `finally` is mandatory.
- **What is `Condition`?** The Lock equivalent of `wait/notify`: `await()` and `signal()` for a specific condition (for example "queue not empty" and "queue not full").

### Q24. What are virtual threads (Java 21)?

**Short answer:** Virtual threads are lightweight threads managed by the JVM, not the OS. You can run millions of them. They make blocking code scalable, which suits IO-heavy services. They are final in Java 21.

**Explanation:**
- A normal (platform) thread maps 1:1 to an OS thread. It is expensive (around 1 MB of stack reserved by default), so pools hold hundreds, not millions.
- A virtual thread is scheduled on a small pool of **carrier threads**. When it blocks on IO (socket, sleep, lock in `java.util.concurrent`), it is **unmounted** from the carrier, which then runs another virtual thread.
- This allows the simple "one thread per request" style with throughput close to reactive code.

```java
try (ExecutorService executor = Executors.newVirtualThreadPerTaskExecutor()) {

    for (int i = 0; i < 10_000; i++) {
        executor.submit(() -> {
            Thread.sleep(Duration.ofSeconds(1));
            // blocks cheaply; the carrier thread is released
            return fetchFromRemote();
        });
    }
}
// close() waits for the tasks
```

```text
Virtual threads: V1 V2 V3 V4 V5 V6 ...  (thousands/millions)
                  \  |  /      |
Carrier threads:   C1  C2  C3 C4         (about number of CPU cores)
                    |   |   |  |
OS threads:        OS  OS  OS OS
```

Rules and limits:
- Good for **IO-bound** work. They do not make CPU-bound code faster.
- **Do not pool them.** Create one per task. Limit concurrency with a `Semaphore` if a downstream resource needs protection (for example a DB connection pool).
- **Pinning (Java 21):** a virtual thread blocking inside a `synchronized` block, or in a native call, keeps its carrier thread blocked. Prefer `ReentrantLock` there. Java 24 removed the `synchronized` pinning case (JEP 491).
- Spring Boot 3.2+ enables them with `spring.threads.virtual.enabled=true`.
- Beware of `ThreadLocal` memory use and of assuming thread count limits request count.

**Follow-up questions:**
- **Virtual threads vs reactive (WebFlux)?** Virtual threads let you keep imperative, readable code and normal stack traces. Reactive still has value for streaming and backpressure pipelines.
- **Does the DB pool still limit you?** Yes. 10,000 virtual threads still share, for example, 20 JDBC connections. The pool becomes the bottleneck, so size it deliberately.
- **What is a carrier thread?** The platform thread that actually executes a virtual thread's code at a given moment.

### Q25. Why does double-checked locking need `volatile`? What is safe publication?

**Short answer:** Without `volatile`, another thread can see a non-null reference to a singleton that is not fully constructed, because of instruction reordering. `volatile` forbids that reordering.

**Explanation:**
- `instance = new Singleton()` is roughly: allocate memory, run constructor, assign reference. The JIT or CPU may reorder it to assign the reference **before** the constructor finishes.
- A second thread doing the unsynchronized first check may see a non-null reference to a half-built object.

```java
public class Singleton {

    private static volatile Singleton instance;

    private Singleton() {
    }

    public static Singleton getInstance() {
        if (instance == null) {                    // first check, no lock
            synchronized (Singleton.class) {
                if (instance == null) {            // second check, with lock
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

Simpler alternatives:
- **Holder idiom:** a static inner class loads lazily and class initialization is thread safe.
- **Enum singleton.**
- In Spring, beans are singletons by default, so you rarely write this yourself.

**Safe publication** means making an object visible to other threads in a safe way: through a `volatile` field, `final` fields of an immutable object, a lock, or a concurrent collection.

**Follow-up questions:**
- **Why is the holder idiom safe?** The JVM guarantees class initialization runs once and is visible to all threads.
- **How can the singleton pattern be broken?** Reflection, serialization, multiple class loaders. An enum singleton resists reflection and serialization.
- **Are `final` fields special in the JMM?** Yes. After the constructor ends, other threads see the correct final field values, provided `this` did not escape during construction.

---

# 3. Collections

## 3.1 Basic

### Q26. ArrayList vs LinkedList

**Short answer:** `ArrayList` is backed by an array and is faster for nearly everything in practice. `LinkedList` is a doubly linked list and is only good for frequent add/remove at the ends or via an iterator position.

**Explanation:**

| Operation | ArrayList | LinkedList |
|---|---|---|
| `get(i)` | O(1) | O(n) |
| `add(e)` at end | O(1) amortized | O(1) |
| `add(0, e)` at front | O(n) (shifts elements) | O(1) |
| Insert in the middle by index | O(n) | O(n) to find the spot, then O(1) to link |
| Memory per element | Just the reference | Reference plus two node pointers and an object header |
| CPU cache behavior | Good (contiguous) | Poor (scattered nodes) |

- `ArrayList` grows when full: new array of about 1.5 times the size, copying the old one. The array is created lazily on the first add (default capacity 10).
- If you know the size, pass it: `new ArrayList<>(10_000)` avoids repeated growth.
- `ArrayDeque` is usually a better queue or stack than `LinkedList`.

```java
List<String> names = new ArrayList<>(1000);
// pre-sized, avoids resize copying

names.add("a");
String first = names.get(0);
// "a"
```

**Follow-up questions:**
- **Why is LinkedList often slower even for insertion?** Finding the position is O(n), and each node is a separate object with poor cache locality.
- **How does `ArrayList.remove(index)` work?** It shifts all later elements left. O(n).
- **Is ArrayList thread-safe?** No. Use `CopyOnWriteArrayList` for read-heavy shared lists, or `Collections.synchronizedList`.

### Q27. Comparable vs Comparator

**Short answer:** `Comparable` defines the natural order inside the class itself (`compareTo`). `Comparator` defines an order outside the class, so you can have many orders (`compare`).

**Explanation:**
- Use `Comparable` when there is one obvious order (numbers, dates, IDs).
- Use `Comparator` for custom or multiple orders, or when you cannot change the class.
- Both return a negative number, zero, or a positive number for less, equal, greater.
- Avoid `a - b` in comparisons because it can overflow. Use `Integer.compare(a, b)`.

```java
public record Employee(String name, int age, double salary) {
}

List<Employee> employees = new ArrayList<>(); // filled elsewhere

employees.sort(
    Comparator.comparing(Employee::salary)
        .reversed()
        .thenComparing(Employee::name)
);
// highest salary first, ties by name
```

```java
public class Version implements Comparable<Version> {

    private final int major;

    public Version(int major) {
        this.major = major;
    }

    @Override
    public int compareTo(Version other) {
        return Integer.compare(this.major, other.major);
    }
}
```

**Follow-up questions:**
- **What does "consistent with equals" mean?** `compareTo` returns 0 exactly when `equals` returns true. If not, `TreeSet` and `TreeMap` may treat equal-by-equals objects as duplicates or vice versa, since they use the comparison only.
- **How to handle null values?** `Comparator.nullsFirst(...)` or `nullsLast(...)`.
- **Is `Collections.sort` stable?** Yes. It uses TimSort, which keeps equal elements in their original order.

---

## 3.2 Intermediate

### Q28. How does HashMap work internally?

**Short answer:** `HashMap` stores entries in an array of buckets. It hashes the key to pick a bucket, then uses `equals` to find the entry inside the bucket. Average get and put are O(1).

**Explanation:**

Step by step for `put(key, value)`:
1. Call `key.hashCode()`.
2. Spread the bits: `hash = h ^ (h >>> 16)`. This mixes high bits into low bits so poor hash codes still spread well.
3. Find the bucket index: `index = (n - 1) & hash`, where `n` is the table size (always a power of 2).
4. If the bucket is empty, store a new node.
5. If not empty, walk the bucket. If a node has the same hash and an `equals` key, replace its value. Otherwise add a new node at the end.
6. If size exceeds `threshold` (capacity times load factor), **resize** (double the table).

Key facts:
- Default capacity 16, default load factor 0.75, so first resize happens when the 13th entry is added.
- A **collision** is when different keys land in the same bucket. They form a linked list.
- **Java 8+:** if a bucket has 8 or more nodes and table capacity is at least 64, the list becomes a red-black tree. Lookup in that bucket is then O(log n) instead of O(n). It goes back to a list when it shrinks to 6 or fewer. If the table is smaller than 64, HashMap resizes instead.
- One `null` key is allowed (stored in bucket 0). Null values are allowed.
- Not thread-safe. Iteration order is not guaranteed.

```text
table (size 16)
 [0]  -> null
 [1]  -> (k1,v1) -> (k17,v17)         <- collision: same bucket, linked list
 [2]  -> null
 [3]  -> (k3,v3)
 ...
 [9]  -> TreeNode root ...            <- 8+ nodes and table >= 64: tree
 [15] -> null

index = (n - 1) & (h ^ (h >>> 16))
```

```java
Map<String, Integer> stock = new HashMap<>();

stock.put("apple", 10);
stock.put("pear", 5);

Integer apples = stock.get("apple");
// 10

stock.getOrDefault("kiwi", 0);
// 0
```

Why capacity is a power of 2: `(n - 1) & hash` is a fast replacement for modulo and works only for powers of 2.

**Follow-up questions:**
- **What if two different keys have the same hashCode?** They go to the same bucket. `equals` tells them apart.
- **What is the time complexity?** O(1) average. Worst case O(n) with a list bucket (before Java 8), O(log n) with treeified buckets (Java 8+).
- **Why is load factor 0.75?** A trade-off: lower means more memory and fewer collisions, higher means less memory and more collisions.
- **How do you size a HashMap for N entries to avoid resizing?** Capacity of about `N / 0.75 + 1`. Java 19 added `HashMap.newHashMap(N)` which does this for you.
- **Can you keep null as a key?** Yes for HashMap, one null key. `ConcurrentHashMap` and `Hashtable` do not allow null keys.

### Q29. What is the equals and hashCode contract?

**Short answer:** If two objects are equal by `equals`, they must return the same `hashCode`. Different objects may share a hash code. If you override one, override the other.

**Explanation:**

Contract:
- `equals` must be reflexive, symmetric, transitive, consistent, and `x.equals(null)` is false.
- Equal objects must have equal hash codes.
- Unequal objects may have equal hash codes (but good hashing minimizes it).
- Hash code must stay the same while the object is in a hash collection, so use immutable fields.

What breaks if you override only `equals`: two equal objects get different hash codes, so they go to different buckets. `map.get(key)` returns null and `HashSet` stores duplicates.

```java
public final class OrderId {

    private final String value;

    public OrderId(String value) {
        this.value = value;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) {
            return true;
        }
        if (!(o instanceof OrderId other)) {
            return false;
        }
        return value.equals(other.value);
    }

    @Override
    public int hashCode() {
        return value.hashCode();
    }
}
```

- Records (Java 16+) generate `equals`, `hashCode` and `toString` from their components.
- With JPA entities, be careful using generated ids in `equals`/`hashCode`, because the id is null before the entity is saved.

**Follow-up questions:**
- **What if the key is mutable and you change it after inserting?** Its hash changes, but the entry is still in the old bucket. You can no longer find it, which is effectively a memory leak.
- **What does the default `Object.equals` do?** Compares references (`==`). Default `hashCode` is derived from the object identity.
- **Why is `equals(Object)` a common bug with overload `equals(MyType)`?** That overloads, it does not override. Collections call `equals(Object)`. Use `@Override` to catch it.

### Q30. What is a fail-fast iterator?

**Short answer:** A fail-fast iterator throws `ConcurrentModificationException` if the collection is structurally modified during iteration, except through the iterator itself.

**Explanation:**
- `ArrayList` and `HashMap` keep a `modCount` counter that increases on each structural change (add, remove).
- The iterator remembers `modCount` when created and checks it on each `next()`.
- It is **best effort**, not a guarantee, so do not use it for program logic.
- It can happen in a single thread too (the classic bug of removing inside a for-each loop).

```java
List<Integer> numbers = new ArrayList<>(List.of(1, 2, 3, 4));

for (Integer n : numbers) {
    if (n == 2) {
        numbers.remove(n);
        // throws ConcurrentModificationException on a later iteration step
    }
}
```

Safe ways:

```java
numbers.removeIf(n -> n % 2 == 0);
// removes even numbers safely

Iterator<Integer> it = numbers.iterator();
while (it.hasNext()) {
    if (it.next() > 2) {
        it.remove();
        // iterator's own remove is allowed
    }
}
```

- **Fail-safe / weakly consistent** collections (`CopyOnWriteArrayList`, `ConcurrentHashMap`) do not throw. `CopyOnWriteArrayList` iterates over a snapshot copy.

**Follow-up questions:**
- **Does fail-fast make a collection thread-safe?** No. It only detects some misuse.
- **Cost of CopyOnWriteArrayList?** Every write copies the whole array. Use it only for read-heavy lists with rare writes.
- **`List.of` and `Collections.unmodifiableList`?** `List.of` is truly immutable (throws `UnsupportedOperationException` on change). `unmodifiableList` is a read-only view over a list that can still change underneath.

### Q31. HashSet, TreeMap, LinkedHashMap: how do they differ?

**Short answer:** `HashSet` is a set backed by a `HashMap`. `LinkedHashMap` is a `HashMap` that also keeps order. `TreeMap` keeps keys sorted using a red-black tree.

**Explanation:**

| Class | What it is | Order | get/put cost | Null key |
|---|---|---|---|---|
| `HashMap` | Hash table | None | O(1) average | One allowed |
| `LinkedHashMap` | Hash table plus doubly linked list of entries | Insertion order, or access order if configured | O(1) | One allowed |
| `TreeMap` | Red-black tree | Sorted by key (`Comparable` or `Comparator`) | O(log n) | Not with natural ordering |
| `HashSet` | Wrapper over a `HashMap` using the elements as keys and a dummy value | None | O(1) | One allowed |
| `LinkedHashSet` | Set over `LinkedHashMap` | Insertion order | O(1) | One allowed |
| `TreeSet` | Set over `TreeMap` | Sorted | O(log n) | Not with natural ordering |

- `TreeMap` gives range operations: `firstKey`, `floorKey`, `ceilingKey`, `headMap`, `tailMap`. Useful for things like finding the nearest price tier or time window.
- `HashSet.add` returns false if the element already exists (decided by `hashCode` + `equals`).
- `TreeSet` decides duplicates using `compareTo`/`compare`, not `equals`.

```java
TreeMap<Integer, String> tiers = new TreeMap<>();

tiers.put(0, "bronze");
tiers.put(1000, "silver");
tiers.put(5000, "gold");

String tier = tiers.floorEntry(2500).getValue();
// "silver" (greatest key less than or equal to 2500)
```

**Follow-up questions:**
- **Which to pick for a sorted report?** `TreeMap`, or sort a list at the end if you do not need sorted access during writes.
- **Is `HashMap` iteration order stable?** Not guaranteed. It can change after a resize or between Java versions.
- **How does a `PriorityQueue` differ?** It is a binary heap. Only the head (smallest) is guaranteed ordered. Iteration order is not sorted.

---

## 3.3 Advanced

### Q32. What happens during HashMap resize and treeification? Why is HashMap unsafe with threads?

**Short answer:** On resize, the table doubles and entries are redistributed. Each entry either stays at the same index or moves by exactly the old capacity. Used from many threads without synchronization, HashMap can lose updates and, in Java 7, could loop forever.

**Explanation:**

Resize:
- When `size > threshold`, a new table of double size is created and entries are moved.
- Because capacity is a power of 2, one extra hash bit decides the new position: the entry stays at index `i` or goes to `i + oldCapacity`. Java 8 splits each bucket into a "low" and a "high" list without recomputing hashes and keeps order.
- Resize is O(n). It is a good reason to presize large maps.

```text
old capacity 16, entry hash index 5
new capacity 32:
  if the new extra bit of hash is 0 -> index 5
  if the new extra bit of hash is 1 -> index 5 + 16 = 21
```

Treeification (Java 8+):
- A bucket with 8 or more nodes becomes a red-black tree only if the table has at least 64 buckets. Otherwise the map resizes.
- This defends against many keys with colliding hashes (including hash flooding attacks), keeping lookups at O(log n).
- Keys that implement `Comparable` order better inside the tree.

Thread safety problems:
- Two concurrent `put` calls on the same bucket can overwrite each other (lost update).
- A resize during a concurrent write can lose entries.
- In Java 7 concurrent resize could create a cycle in a bucket list, causing an infinite loop in `get`. Java 8 changed the transfer logic, but the map is still not safe.
- Visibility: another thread may not see writes at all without synchronization.

What to use instead:
- `ConcurrentHashMap` for shared mutable maps (see Q22).
- Immutable maps (`Map.of`, `Map.copyOf`) for shared read-only data.
- Confine a `HashMap` to one thread.

**Follow-up questions:**
- **Why is the initial table created lazily?** The table is allocated on the first `put`. Empty maps stay cheap.
- **Can an attacker exploit hash collisions?** Yes (hash flooding denial of service). Treeification limits the damage, and keys implementing `Comparable` help.
- **What does `Map.of` guarantee?** It is immutable, null-free, and its iteration order is deliberately unspecified and can differ between runs.

### Q33. How do you implement an LRU cache with LinkedHashMap?

**Short answer:** Create a `LinkedHashMap` with `accessOrder = true` and override `removeEldestEntry` to remove the oldest entry once size passes a limit.

**Explanation:**
- With `accessOrder = true`, each `get` or `put` moves that entry to the end of the internal linked list. The eldest entry (least recently used) is at the head.
- After each insertion, `removeEldestEntry` is called. Returning true removes the eldest.
- All operations are O(1).
- This class is **not thread-safe**. Wrap it with `Collections.synchronizedMap`, or use a library like Caffeine for real production caches (it also provides time-based expiry, statistics and better concurrency).

```java
public class LruCache<K, V> extends LinkedHashMap<K, V> {

    private final int maxEntries;

    public LruCache(int maxEntries) {
        super(16, 0.75f, true);
        // true = access order
        this.maxEntries = maxEntries;
    }

    @Override
    protected boolean removeEldestEntry(Map.Entry<K, V> eldest) {
        return size() > maxEntries;
    }
}
```

```java
LruCache<String, String> cache = new LruCache<>(2);

cache.put("a", "1");
cache.put("b", "2");
cache.get("a");
// "a" is now most recently used
cache.put("c", "3");
// "b" was the least recently used, so it is evicted
```

Interview variant: build it from scratch with a `HashMap<K, Node>` plus a doubly linked list. The map gives O(1) lookup, the list gives O(1) move-to-front and removal.

**Follow-up questions:**
- **Why not just a HashMap with timestamps?** Finding the oldest entry would be O(n).
- **How would you make it thread-safe and fast?** Use Caffeine, or lock striping, rather than one global lock.
- **Where do you cache in a Spring Boot microservice?** `@Cacheable` with Caffeine for local data, Redis for shared data across instances. Local caches can differ between instances, so choose TTLs carefully.

---

# 4. OOP, Strings, Exceptions, Immutability

## 4.1 Basic

### Q34. Abstract class vs interface

**Short answer:** An interface defines a contract and a class can implement many. An abstract class is a partial base class with state and constructors, and a class can extend only one.

**Explanation:**

| Point | Abstract class | Interface |
|---|---|---|
| Inheritance | A class extends only one | A class implements many |
| State (instance fields) | Yes | No, only `public static final` constants |
| Constructors | Yes | No |
| Method bodies | Yes | `default`, `static` (Java 8), `private` (Java 9) |
| Access modifiers | Any | Methods are public (private allowed since Java 9) |
| Use for | Shared code and state between closely related classes | A capability or contract that unrelated classes can have |

- Default methods let interfaces evolve without breaking existing implementers. If two interfaces give the same default method, the class must override it to resolve the conflict.
- Since Java 17, `sealed` interfaces and classes restrict which types can implement or extend them.
- Prefer interfaces for dependencies (testing, Spring injection, mocking). Use an abstract class when subclasses share real state or a template method flow.

```java
public interface PaymentGateway {

    PaymentResult pay(Order order);

    default boolean supports(String currency) {
        return true;
    }
}

public abstract class BaseReport {

    protected final String title;

    protected BaseReport(String title) {
        this.title = title;
    }

    public final String render() {
        // template method: fixed steps, subclass fills in the body
        return title + "\n" + body();
    }

    protected abstract String body();
}
```

**Follow-up questions:**
- **Can an interface have a constructor?** No.
- **Diamond problem in Java?** Classes have single inheritance, so state conflicts do not occur. For default methods, the compiler forces you to override and may call `A.super.method()`.
- **What is a functional interface?** An interface with exactly one abstract method, usable with lambdas (`@FunctionalInterface`).

### Q35. Overloading vs overriding

**Short answer:** Overloading is the same method name with different parameters in one class, resolved at compile time. Overriding is a subclass replacing a parent method with the same signature, resolved at run time.

**Explanation:**

| | Overloading | Overriding |
|---|---|---|
| Where | Same class (or subclass) | Subclass |
| Signature | Different parameters | Same parameters |
| Return type | Can differ | Same or a subtype (covariant) |
| Resolved | Compile time (by declared types) | Run time (by actual object type) |
| Access | Any | Cannot be more restrictive |
| Checked exceptions | Any | Cannot throw broader checked exceptions |

- `static`, `private` and `final` methods cannot be overridden. A static method with the same signature in a subclass **hides** the parent one.
- Fields are not polymorphic. Field access uses the declared type.
- Always use `@Override`.

```java
class Animal {

    String sound() {
        return "...";
    }
}

class Dog extends Animal {

    @Override
    String sound() {
        return "woof";
    }
}

Animal pet = new Dog();
pet.sound();
// "woof" - decided at run time from the actual object (Dog)
```

```java
class Printer {

    void print(Object o) {
        System.out.println("object");
    }

    void print(String s) {
        System.out.println("string");
    }
}

Object text = "hi";
new Printer().print(text);
// prints "object": overload chosen at compile time from the declared type Object
```

**Follow-up questions:**
- **What is runtime polymorphism?** A parent-typed reference calling the child's overriding method.
- **Can you override a constructor?** No. Constructors are not inherited.
- **What does `super` do?** Calls the parent's constructor or method. A call to `super(...)` must be the first statement of a constructor (relaxed only in newer preview features).

### Q36. String pool, immutability and StringBuilder

**Short answer:** `String` is immutable: its content cannot change after creation. Literals are stored in a shared String pool so equal literals share one object. Use `StringBuilder` for building strings in loops.

**Explanation:**
- **Why immutable:** safe to share between threads, safe as `HashMap` keys (the hash code is cached), enables the String pool, and is more secure (a class name or file path cannot change after validation).
- **String pool:** a table of unique strings, on the heap (Java 7+). Literals go there. `intern()` adds or returns the pooled copy.
- `==` compares references. `equals` compares content. Always use `equals` for strings.
- Concatenating with `+` in a loop creates many temporary strings. Use `StringBuilder`. A single `a + b + c` expression is fine (Java 9+ compiles it with `invokedynamic`).
- `StringBuffer` is the older synchronized version. `StringBuilder` is unsynchronized and faster. In most cases use `StringBuilder`.

```java
String a = "java";
String b = "java";
String c = new String("java");

System.out.println(a == b);
// true  (same pooled object)

System.out.println(a == c);
// false (c is a new object on the heap)

System.out.println(a.equals(c));
// true

System.out.println(a == c.intern());
// true  (intern returns the pooled one)
```

```java
StringBuilder sb = new StringBuilder();

for (String part : parts) {
    sb.append(part).append(',');
}
String result = sb.toString();
```

```text
String pool (heap)
 "java" <---- a
      ^------- b
Heap
 new String("java")  <---- c   (separate object, same content)
```

**Follow-up questions:**
- **How many objects does `new String("java")` create?** One on the heap each call, plus the literal `"java"` in the pool if it is not already there.
- **Why char[] over String for passwords?** A `String` stays in memory until GC and may stay in the pool, while a `char[]` can be overwritten after use.
- **Are text blocks (Java 15) different strings?** They are normal strings written over several lines with `"""`.

---

## 4.2 Intermediate

### Q37. Is Java pass-by-value or pass-by-reference? What is the Integer cache?

**Short answer:** Java is always pass-by-value. For objects, the value copied is the reference, so the method can change the object's content but cannot change which object the caller's variable points to. The Integer cache means `Integer` values from -128 to 127 are shared objects, so `==` works there by accident and fails outside that range.

**Explanation:**

```java
static void change(StringBuilder sb, int number) {
    sb.append(" world");
    // changes the shared object
    sb = new StringBuilder("new");
    // only changes the local copy of the reference
    number = 99;
    // only changes the local copy
}

StringBuilder text = new StringBuilder("hello");
int n = 1;
change(text, n);

System.out.println(text);
// hello world
System.out.println(n);
// 1
```

```text
caller:  text ----+
                  +---> [StringBuilder "hello world"]
method:  sb   ----+     (sb was then re-pointed to a different object; caller unaffected)
```

Integer cache:
- Autoboxing `Integer x = 127` calls `Integer.valueOf`, which returns cached objects for -128 to 127. The upper bound can be raised with `-XX:AutoBoxCacheMax`.
- Outside the range, each boxing creates a new object.

```java
Integer a = 127;
Integer b = 127;
System.out.println(a == b);
// true  (cached)

Integer c = 128;
Integer d = 128;
System.out.println(c == d);
// false (different objects with the default cache)

System.out.println(c.equals(d));
// true
```

- Unboxing a null `Integer` throws `NullPointerException`: `int x = map.get("missing");`.
- Boxed types in hot loops create garbage. Prefer primitives.

**Follow-up questions:**
- **Which wrapper types have caches?** `Byte`, `Short`, `Integer`, `Long` (-128 to 127), `Character` (0 to 127), and `Boolean`.
- **`new Integer(5)`?** Deprecated for removal. Use `Integer.valueOf(5)`.
- **Does the cache affect `equals`?** No. Always compare boxed values with `equals`.

### Q38. Checked vs unchecked exceptions

**Short answer:** Checked exceptions (subclasses of `Exception` but not `RuntimeException`) must be caught or declared. Unchecked exceptions (`RuntimeException` and `Error`) need not be.

**Explanation:**

```text
Throwable
 |-- Error                    (JVM problems: OutOfMemoryError, StackOverflowError) - do not catch
 `-- Exception
      |-- IOException, SQLException ...  CHECKED   (compiler forces handling)
      `-- RuntimeException               UNCHECKED
           |-- NullPointerException, IllegalArgumentException, IllegalStateException ...
```

- **Checked:** for recoverable conditions the caller can reasonably handle (file missing, network issue).
- **Unchecked:** for programming errors (null, bad argument) or failures the caller cannot fix.
- Modern frameworks (Spring) prefer unchecked exceptions. Spring translates `SQLException` into unchecked `DataAccessException`.
- **Spring `@Transactional` rolls back on unchecked exceptions and errors by default, but not on checked exceptions.** Use `rollbackFor = Exception.class` to change it. This is a frequent interview and production gotcha.
- Do not swallow exceptions silently. Log with context or rethrow. Keep the original as the cause.

```java
public Order load(long id) {
    try {
        return repository.find(id);
    } catch (SQLException e) {
        throw new OrderLoadException("Could not load order " + id, e);
        // wrap and keep the cause
    }
}
```

**Follow-up questions:**
- **Should you catch `Exception` or `Throwable`?** Rarely. Catch the narrowest type you can handle. Catching `Throwable` also catches `Error`.
- **What is exception chaining?** Passing the original exception as the `cause` so the stack trace keeps the whole story.
- **How do you handle exceptions globally in Spring?** `@RestControllerAdvice` with `@ExceptionHandler` methods that map exceptions to HTTP responses.

### Q39. How does try-with-resources work? What are the pitfalls of `finally`?

**Short answer:** try-with-resources automatically closes any `AutoCloseable` resource at the end of the block, even on exception. `finally` always runs, but returning or throwing inside it can hide the original result or exception.

**Explanation:**
- Resources are closed in **reverse order** of declaration.
- If the body throws and `close()` also throws, the body exception is the main one and the close exception is attached as **suppressed** (`getSuppressed()`). With a manual `finally`, the close exception would replace the original.

```java
try (Connection con = dataSource.getConnection();
     PreparedStatement ps = con.prepareStatement("select 1")) {

    ps.execute();
}
// ps is closed first, then con
```

`finally` pitfalls:
- A `return` in `finally` overrides the `try` return and discards any thrown exception.
- It does not run if the JVM exits (`System.exit`), crashes, or the thread is killed.
- An exception thrown in `finally` replaces the original exception.

```java
static int test() {
    try {
        return 1;
    } finally {
        return 2;
        // overrides: the method returns 2
    }
}
```

```java
static int test2() {
    int x = 1;
    try {
        return x;
        // value 1 is saved before finally runs
    } finally {
        x = 2;
        // does not change the returned value for a primitive
    }
}
// returns 1
```

**Follow-up questions:**
- **What must a resource implement?** `AutoCloseable` (its `close()` may throw `Exception`). `Closeable` (for IO) narrows to `IOException`.
- **Can you use an existing variable?** Yes, since Java 9, if it is final or effectively final: `try (existingResource) { ... }`.
- **Multi-catch?** `catch (IOException | SQLException e)` handles several types in one block.

### Q40. What is SOLID? (brief)

**Short answer:** Five design principles that keep object-oriented code easy to change and test.

**Explanation:**

| Letter | Principle | Meaning in practice |
|---|---|---|
| S | Single Responsibility | A class has one reason to change. Keep controller, service and repository roles separate |
| O | Open/Closed | Add behavior by adding new classes, not by editing tested code (for example a new `PaymentStrategy` implementation) |
| L | Liskov Substitution | A subclass must work wherever its parent is expected, without surprising behavior |
| I | Interface Segregation | Many small interfaces, not one large one that forces empty methods |
| D | Dependency Inversion | Depend on abstractions, and let the container inject implementations (Spring constructor injection) |

```java
public interface NotificationSender {

    void send(String to, String message);
}

public class OrderService {

    private final NotificationSender sender;

    public OrderService(NotificationSender sender) {
        this.sender = sender;
        // depends on the interface, easy to swap or mock
    }
}
```

**Follow-up questions:**
- **Example of a Liskov violation?** `Square extends Rectangle` where setting width also changes height, which breaks code that expects independent width and height. Another: a subclass that throws `UnsupportedOperationException` for an inherited method.
- **Dependency Inversion vs Dependency Injection?** Inversion is the principle (depend on abstractions). Injection is the technique (a container or caller supplies the dependency).
- **Composition vs inheritance?** Prefer composition: it is more flexible and avoids fragile base class problems.

---

## 4.3 Advanced

### Q41. How do you design an immutable class? Why does it matter?

**Short answer:** Make the class final, fields private and final, no setters, and copy any mutable inputs and outputs. Immutable objects are thread-safe without locks and are safe as map keys.

**Explanation:**

Rules:
1. Declare the class `final` (or use a private constructor), so it cannot be subclassed and made mutable.
2. All fields `private final`.
3. No setters or methods that change state.
4. **Defensive copies:** copy mutable objects (lists, dates, arrays) when receiving them in the constructor and when returning them.
5. Do not let `this` escape during construction.

```java
public final class Order {

    private final String id;
    private final List<String> items;

    public Order(String id, List<String> items) {
        this.id = id;
        this.items = List.copyOf(items);
        // defensive copy, immutable
    }

    public String id() {
        return id;
    }

    public List<String> items() {
        return items;
        // already immutable, safe to return
    }

    public Order withItem(String item) {
        List<String> updated = new ArrayList<>(items);
        updated.add(item);
        return new Order(id, updated);
        // "change" means create a new object
    }
}
```

Shortcut (Java 16+): a `record` gives you a final class with final fields, `equals`, `hashCode`, `toString`. Records are shallowly immutable, so you still need defensive copies for mutable components:

```java
public record OrderRecord(String id, List<String> items) {

    public OrderRecord {
        items = List.copyOf(items);
        // compact constructor: copy on creation
    }
}
```

Benefits: thread safety, easy reasoning, safe caching and hashing, no defensive copying by callers. Cost: more object creation for frequent changes.

**Follow-up questions:**
- **Is `final` on a field enough?** No. `final List<String> items` stops reassigning the reference, but the list can still change. You need an immutable list.
- **Is `String` really immutable internally?** Yes by design. The class is final, its content cannot be changed through the public API, and the hash is cached.
- **Immutability and JPA/Jackson?** JPA entities need a default constructor and mutable state, so records are better as DTOs. Jackson supports records (Jackson 2.12+).
- **What does `Collections.unmodifiableList` give you?** A read-only view, not a copy. If the original list changes, the view changes too. `List.copyOf` makes an independent immutable copy.

---

# 5. Lambdas, Functional Interfaces, Method References, Streams, Optional

Sample data used in most examples below (a record is a short class for plain data, explained in M1):

```java
record Employee(String name, String dept, double salary) {
}

List<Employee> employees = List.of(
    new Employee("Asha", "IT", 90000),
    new Employee("Ben", "IT", 70000),
    new Employee("Chen", "HR", 60000),
    new Employee("Dina", "HR", 60000),
    new Employee("Eli", "Sales", 50000)
);
```

If your own `Employee` is a normal class with getters, replace `Employee::salary` with `Employee::getSalary`, and so on.

## Lambdas and Streams: Basic

#### L1. [Basic] What is a lambda expression?

**Short answer:** A lambda is a short way to write an anonymous function. You pass it where a functional interface (an interface with one abstract method) is expected. Added in Java 8.

**Explanation:**
- Syntax: `(parameters) -> body`.
- The compiler works out which interface it implements from the context. This is called target typing.
- A lambda replaces a short anonymous inner class.

```java
// Before Java 8
Comparator<String> oldWay = new Comparator<String>() {
    @Override
    public int compare(String a, String b) {
        return Integer.compare(a.length(), b.length());
    }
};

// Java 8 and later
Comparator<String> byLength = (a, b) -> Integer.compare(a.length(), b.length());

// Block body: use braces and return
Comparator<String> byLengthBlock = (a, b) -> {
    int result = Integer.compare(a.length(), b.length());
    return result;
};

Runnable task = () -> System.out.println("running");
```

**Follow-up questions**
- *Can a lambda be used anywhere?* No. Only where the target type is a functional interface.
- *Is a lambda the same as an anonymous class?* No. Differences: `this` inside a lambda is the enclosing object, and a lambda does not create its own class file at compile time (see L19).
- *Can a lambda throw a checked exception?* Only if the functional interface method declares it. `Callable.call()` can throw `Exception`. `Runnable.run()` and `Function.apply()` cannot.

---

#### L2. [Basic] What is a functional interface?

**Short answer:** An interface with exactly one abstract method. Lambdas and method references can be assigned to it. `@FunctionalInterface` is an optional annotation that makes the compiler check this rule.

**Explanation:**
- `default` methods, `static` methods and methods that match `Object` (like `equals`) do not count as abstract.
- Examples in the JDK: `Runnable`, `Comparator`, `Callable`, and everything in `java.util.function`.

```java
@FunctionalInterface
interface Validator<T> {

    boolean validate(T value);                 // the single abstract method

    default Validator<T> negate() {            // allowed: default method
        return value -> !validate(value);
    }
}

Validator<String> notEmpty = s -> !s.isEmpty();
boolean ok = notEmpty.validate("hi");
// ok = true

boolean bad = notEmpty.negate().validate("hi");
// bad = false
```

**Follow-up questions**
- *What if I add a second abstract method with `@FunctionalInterface`?* Compile error.
- *Does `Comparator` count? It has `equals` declared.* Yes, it is functional. `equals` matches `Object.equals`, so it is not counted.
- *Can I use a lambda for an abstract class with one abstract method?* No. Lambdas work only with interfaces.

---

#### L3. [Basic] Which built-in functional interfaces should I know?

**Short answer:** `Predicate`, `Function`, `Consumer`, `Supplier`, their `Bi` versions, and `UnaryOperator` / `BinaryOperator`. All are in `java.util.function`.

| Interface | Method | Takes | Returns | Typical use |
|---|---|---|---|---|
| `Predicate<T>` | `test(T)` | one `T` | `boolean` | filter |
| `Function<T,R>` | `apply(T)` | one `T` | `R` | map |
| `Consumer<T>` | `accept(T)` | one `T` | nothing (`void`) | forEach |
| `Supplier<T>` | `get()` | nothing | `T` | lazy value, factory |
| `BiPredicate<T,U>` | `test(T,U)` | two values | `boolean` | two-input check |
| `BiFunction<T,U,R>` | `apply(T,U)` | two values | `R` | combine two inputs |
| `BiConsumer<T,U>` | `accept(T,U)` | two values | nothing | `Map.forEach` |
| `UnaryOperator<T>` | `apply(T)` | one `T` | `T` (same type) | `List.replaceAll` |
| `BinaryOperator<T>` | `apply(T,T)` | two `T` | `T` | `reduce` |
| `Runnable` | `run()` | nothing | nothing | threads |
| `Callable<V>` | `call()` | nothing | `V`, may throw `Exception` | executors |

Primitive versions avoid boxing (wrapping `int` into `Integer`): `IntPredicate`, `IntFunction<R>`, `ToIntFunction<T>`, `IntUnaryOperator`, `IntBinaryOperator`, and so on.

```java
Predicate<String> isLong = s -> s.length() > 5;
Function<String, Integer> length = String::length;
Consumer<String> printer = s -> System.out.println(s);
Supplier<List<String>> listFactory = ArrayList::new;
BinaryOperator<Integer> add = (a, b) -> a + b;

// Composition
Predicate<String> isShort = isLong.negate();
Predicate<String> longAndStartsWithA = isLong.and(s -> s.startsWith("A"));
Function<Integer, Integer> doubleIt = x -> x * 2;
Function<Integer, Integer> plusOne = x -> x + 1;

int r1 = doubleIt.andThen(plusOne).apply(5);
// r1 = 11   (double first: 10, then plus one: 11)

int r2 = doubleIt.compose(plusOne).apply(5);
// r2 = 12   (plus one first: 6, then double: 12)
```

**Follow-up questions**
- *Difference between `Function` and `UnaryOperator`?* `UnaryOperator<T>` is a `Function<T,T>`: input and output are the same type.
- *Why primitive versions like `IntPredicate`?* They avoid boxing cost in hot loops.
- *Difference between `andThen` and `compose`?* `f.andThen(g)` runs `f` then `g`. `f.compose(g)` runs `g` then `f`.

---

#### L4. [Basic] What does "effectively final" mean for lambdas?

**Short answer:** A local variable used inside a lambda must not be changed after it is assigned. It can be declared `final` or just never reassigned (effectively final).

**Explanation:**
- A lambda copies the value of a captured local variable. If the variable could change later, the lambda and the method would see different values. Java forbids this.
- Local variables live on the thread's stack. The lambda may run later on another thread, so sharing a changing stack variable would be unsafe.
- Fields and array elements are not copied, they live on the heap. A lambda can change them, but that is risky in multi-threaded code.

```java
int base = 10;
Function<Integer, Integer> addBase = x -> x + base;   // OK: base never changes

int counter = 0;
list.forEach(item -> counter++);
// compile error: local variables referenced from a lambda must be final or effectively final

// Workaround for a counter: use an atomic holder, or better, use a stream operation
AtomicInteger atomicCounter = new AtomicInteger();
list.forEach(item -> atomicCounter.incrementAndGet());

long count = list.stream().count();   // best: no mutation needed
```

**Follow-up questions**
- *Does the same rule apply to anonymous classes?* Yes, since Java 8 (before that they needed explicit `final`).
- *Can a lambda change an instance field?* Yes, because it uses `this.field`, not a copied local.

---

#### L5. [Basic] What are method references and what kinds exist?

**Short answer:** A method reference (`Class::method`) is a shorter lambda that just calls an existing method. There are four kinds.

| Kind | Syntax | Same as lambda | Meaning |
|---|---|---|---|
| Static method | `Integer::parseInt` | `s -> Integer.parseInt(s)` | calls a static method |
| Bound instance | `System.out::println` | `x -> System.out.println(x)` | calls a method on one specific, already existing object |
| Unbound instance | `String::length` | `s -> s.length()` | first parameter becomes the object the method is called on |
| Constructor | `ArrayList::new` | `() -> new ArrayList<>()` | creates a new object |

```java
List<String> words = List.of("apple", "kiwi");

List<Integer> lengths = words.stream()
    .map(String::length)            // unbound instance
    .toList();
// lengths = [5, 4]

words.forEach(System.out::println); // bound instance
// prints: apple, then kiwi

List<Integer> numbers = Stream.of("1", "2")
    .map(Integer::parseInt)         // static
    .toList();
// numbers = [1, 2]

Supplier<List<String>> factory = ArrayList::new;   // constructor
```

**Follow-up questions**
- *When should I prefer a lambda?* When extra logic is needed, or the reference makes the code less clear.
- *Why is `String::length` "unbound"?* No object is fixed in advance. The stream passes each element as the receiver.

---

#### L6. [Basic] What is a Stream and what is a stream pipeline?

**Short answer:** A Stream is a sequence of elements that you process with a chain of operations. It does not store data and does not change the source. A pipeline has three parts: a source, zero or more intermediate operations, and one terminal operation.

```text
source           intermediate operations              terminal operation
employees  -->  filter  -->  map  -->  sorted   -->   collect / forEach / count
(list)          (keep some)  (convert) (order)         (produces the result)
```

```java
List<String> result = employees.stream()
    .filter(e -> e.salary() >= 60000)
    .map(Employee::name)
    .map(String::toUpperCase)
    .toList();
// result = [ASHA, BEN, CHEN, DINA]
```

- A stream is not a collection. It does not hold elements. It reads them from the source.
- It does not modify the source list.
- `IntStream`, `LongStream`, `DoubleStream` are streams of primitives. They have `sum()`, `average()`, `summaryStatistics()`.

```java
double total = employees.stream()
    .mapToDouble(Employee::salary)
    .sum();
// total = 330000.0
```

**Follow-up questions**
- *Stream vs Collection?* A collection stores data and can be read many times. A stream describes a computation and can be used once.
- *How do you create a stream?* `list.stream()`, `Stream.of(...)`, `Arrays.stream(array)`, `IntStream.range(0, 5)`, `Stream.iterate(...)`, `Stream.generate(...)`.
- *Why `mapToDouble` and not `map`?* It gives a `DoubleStream` with no boxing and has `sum()`.

---

#### L7. [Basic] What is the difference between intermediate and terminal operations? What is lazy evaluation?

**Short answer:** Intermediate operations (`filter`, `map`, `sorted`...) return a new stream and do nothing yet. Terminal operations (`collect`, `forEach`, `count`...) start the work. This "do nothing until needed" behaviour is lazy evaluation.

| Type | Examples | Returns |
|---|---|---|
| Intermediate | `filter`, `map`, `flatMap`, `distinct`, `sorted`, `limit`, `skip`, `peek` | another `Stream` |
| Terminal | `collect`, `toList`, `forEach`, `reduce`, `count`, `min`, `max`, `findFirst`, `anyMatch` | a value, a collection or `void` |

Elements go through the whole pipeline one at a time, not operation by operation. Short-circuit operations (`findFirst`, `limit`, `anyMatch`) stop early.

```java
Optional<String> first = Stream.of("a", "bb", "ccc", "dd")
    .filter(s -> {
        System.out.println("filter " + s);
        return s.length() > 1;
    })
    .map(s -> {
        System.out.println("map " + s);
        return s.toUpperCase();
    })
    .findFirst();
// prints:
// filter a
// filter bb
// map bb
// first = Optional[BB]
// "ccc" and "dd" are never touched

Stream<String> noTerminal = Stream.of("x").peek(System.out::println);
// prints nothing: there is no terminal operation
```

**Follow-up questions**
- *Why is lazy evaluation useful?* It avoids unneeded work and allows infinite streams like `Stream.iterate(1, x -> x + 1).limit(5)`.
- *Which intermediate operations are "stateful"?* `sorted` and `distinct` must see elements before they can emit them. `sorted` needs all elements first.
- *What does `peek` do?* Runs an action on each element as it passes. Meant for debugging only.

---

#### L8. [Basic] What is Optional and why was it introduced?

**Short answer:** `Optional<T>` is a container that holds either one value or nothing. It makes "might be missing" visible in the return type and reduces `NullPointerException`.

```java
Optional<Employee> found = employees.stream()
    .filter(e -> e.name().equals("Ben"))
    .findFirst();

Optional<String> empty = Optional.empty();
Optional<String> present = Optional.of("hi");             // value must not be null
Optional<String> maybe = Optional.ofNullable(null);       // null becomes empty

String name = found
    .map(Employee::name)
    .orElse("unknown");
// name = "Ben"

found.ifPresent(e -> System.out.println(e.name()));
// prints: Ben

found.ifPresentOrElse(                                     // Java 9
    e -> System.out.println("found " + e.name()),
    () -> System.out.println("not found")
);
```

Useful methods:

| Method | What it does |
|---|---|
| `of(v)` | wraps a non-null value, throws `NullPointerException` if null |
| `ofNullable(v)` | wraps the value, or empty if null |
| `map(f)` | applies `f` to the value if present, returns a new Optional |
| `flatMap(f)` | like `map`, but `f` itself returns an Optional (avoids `Optional<Optional<T>>`) |
| `filter(p)` | keeps the value only if `p` is true |
| `orElse(x)` | value, or `x` if empty |
| `orElseGet(s)` | value, or the result of supplier `s` if empty |
| `orElseThrow(s)` | value, or throws the exception from `s` |
| `isPresent()` / `isEmpty()` | presence check (`isEmpty` since Java 11) |

**Follow-up questions**
- *What does `get()` do on an empty Optional?* Throws `NoSuchElementException`. Prefer `orElseThrow()` (no-arg version since Java 10), which says clearly what it does.
- *Is Optional serializable?* No. That is one reason not to use it as a field.

---

## Lambdas and Streams: Intermediate

#### L9. [Intermediate] What is the difference between `map` and `flatMap`?

**Short answer:** `map` converts each element into exactly one new element. `flatMap` converts each element into a stream of elements and then joins all those streams into one flat stream.

```text
map:      [ [1,2], [3], [4,5] ]  --map(List::size)-->      [2, 1, 2]
flatMap:  [ [1,2], [3], [4,5] ]  --flatMap(List::stream)--> [1, 2, 3, 4, 5]
```

```java
List<List<Integer>> nested = List.of(
    List.of(1, 2),
    List.of(3),
    List.of(4, 5)
);

List<Integer> flat = nested.stream()
    .flatMap(List::stream)
    .toList();
// flat = [1, 2, 3, 4, 5]

List<String> sentences = List.of("hello world", "java stream");

List<String> words = sentences.stream()
    .flatMap(s -> Arrays.stream(s.split(" ")))
    .toList();
// words = [hello, world, java, stream]

// With map you would get a Stream<String[]> / List<String[]>, which is not what you want.
```

**Follow-up questions**
- *What is `flatMap` on Optional?* If the mapping function already returns an Optional, `flatMap` returns it as is. `map` would give `Optional<Optional<T>>`.
- *What is `mapMulti`?* A Java 16 alternative to `flatMap` that pushes zero or more elements into a consumer. Used when creating a small stream per element is too costly.

---

#### L10. [Intermediate] How does `reduce` work?

**Short answer:** `reduce` combines all elements into one value by repeatedly applying a function. You give it a starting value (identity) and a combining function that takes two values and returns one.

```java
int sum = Stream.of(1, 2, 3, 4, 5)
    .reduce(0, Integer::sum);
// sum = 15
// steps: 0+1=1, 1+2=3, 3+3=6, 6+4=10, 10+5=15

Optional<Integer> max = Stream.of(1, 2, 3, 4, 5)
    .reduce(Integer::max);
// max = Optional[5]    (no identity, so the result is an Optional: the stream could be empty)

double totalSalary = employees.stream()
    .map(Employee::salary)
    .reduce(0.0, Double::sum);
// totalSalary = 330000.0
```

Three forms:
- `reduce(identity, accumulator)` returns `T`.
- `reduce(accumulator)` returns `Optional<T>`.
- `reduce(identity, accumulator, combiner)` is used when the result type differs from the element type, and for parallel streams.

**Follow-up questions**
- *What rules must the function follow?* It must be associative (grouping does not matter) and stateless, and the identity must not change the other value (`0` for addition, `1` for multiplication). Otherwise parallel results are wrong.
- *`reduce` or `sum()`?* For primitives prefer `mapToInt(...).sum()`. It is clearer and avoids boxing.

---

#### L11. [Intermediate] How do `groupingBy`, `partitioningBy`, `counting` and `joining` work?

**Short answer:** `Collectors` turn a stream into a result. `groupingBy` makes a `Map<key, group>`. `partitioningBy` splits into exactly two groups (`true` and `false`). `joining` concatenates strings.

| Collector | What it does |
|---|---|
| `groupingBy(classifier)` | groups elements by the key from the classifier, values are lists |
| `groupingBy(classifier, downstream)` | same, but applies the downstream collector to each group |
| `partitioningBy(predicate)` | `Map<Boolean, List<T>>` with both `true` and `false` keys always present |
| `counting()` | number of elements, as `Long` |
| `mapping(f, downstream)` | converts each element with `f` before the downstream collector |
| `averagingDouble(f)` / `summingDouble(f)` | average or sum of a double value |
| `joining(sep, prefix, suffix)` | joins `CharSequence` elements into one `String` |

```java
Map<String, List<Employee>> byDept = employees.stream()
    .collect(Collectors.groupingBy(Employee::dept));
// {IT=[Asha, Ben], HR=[Chen, Dina], Sales=[Eli]}   (as Employee objects; HashMap order is not guaranteed)

Map<String, Long> countByDept = employees.stream()
    .collect(Collectors.groupingBy(
        Employee::dept,
        Collectors.counting()
    ));
// {IT=2, HR=2, Sales=1}   (order may differ)

Map<String, Double> avgByDept = employees.stream()
    .collect(Collectors.groupingBy(
        Employee::dept,
        Collectors.averagingDouble(Employee::salary)
    ));
// {IT=80000.0, HR=60000.0, Sales=50000.0}   (order may differ)

Map<String, List<String>> namesByDept = employees.stream()
    .collect(Collectors.groupingBy(
        Employee::dept,
        Collectors.mapping(Employee::name, Collectors.toList())
    ));
// {IT=[Asha, Ben], HR=[Chen, Dina], Sales=[Eli]}   (order may differ)

Map<Boolean, List<String>> highLow = employees.stream()
    .collect(Collectors.partitioningBy(
        e -> e.salary() >= 60000,
        Collectors.mapping(Employee::name, Collectors.toList())
    ));
// {false=[Eli], true=[Asha, Ben, Chen, Dina]}

String joined = employees.stream()
    .map(Employee::name)
    .collect(Collectors.joining(", ", "[", "]"));
// joined = "[Asha, Ben, Chen, Dina, Eli]"
```

**Follow-up questions**
- *`groupingBy` vs `partitioningBy`?* `partitioningBy` always has two keys, `true` and `false`, even if one group is empty. `groupingBy` only has keys that appear.
- *How do I keep the group order?* Pass a map factory: `groupingBy(Employee::dept, LinkedHashMap::new, Collectors.toList())` or `TreeMap::new` for sorted keys.
- *What is a downstream collector?* The second collector that processes the elements inside each group.

---

#### L12. [Intermediate] How does `Collectors.toMap` work and what is the merge function?

**Short answer:** `toMap(keyFn, valueFn)` builds a map. If two elements give the same key, it throws `IllegalStateException` (duplicate key). A third argument, the merge function, tells it how to combine the two values.

```java
Map<String, Double> totalByDept = employees.stream()
    .collect(Collectors.toMap(
        Employee::dept,
        Employee::salary,
        Double::sum
    ));
// {IT=160000.0, HR=120000.0, Sales=50000.0}   (order may differ)

Map<String, Double> broken = employees.stream()
    .collect(Collectors.toMap(
        Employee::dept,
        Employee::salary
    ));
// throws IllegalStateException: Duplicate key IT (...)  - IT appears twice
// (the exact key named in the message depends on which duplicate is hit first)

Map<String, Double> ordered = employees.stream()
    .collect(Collectors.toMap(
        Employee::name,
        Employee::salary,
        (oldValue, newValue) -> oldValue,
        LinkedHashMap::new
    ));
// {Asha=90000.0, Ben=70000.0, Chen=60000.0, Dina=60000.0, Eli=50000.0}
```

**Follow-up questions**
- *What happens if a value is null?* `toMap` throws `NullPointerException` (it uses `HashMap.merge`, which rejects null values).
- *The fourth argument?* A supplier for the map type, for example `LinkedHashMap::new`.
- *`toMap` vs `groupingBy`?* Use `toMap` for one value per key. Use `groupingBy` when many elements share a key.

---

#### L13. [Intermediate] Can a stream be reused?

**Short answer:** No. A stream can be consumed once. After a terminal operation, using it again throws `IllegalStateException`.

```java
Stream<String> stream = Stream.of("a", "b", "c");

long count = stream.count();
// count = 3

stream.forEach(System.out::println);
// throws IllegalStateException: stream has already been operated upon or closed

// Fix: create a new stream each time, or use a Supplier
Supplier<Stream<String>> supplier = () -> Stream.of("a", "b", "c");
long c1 = supplier.get().count();
List<String> l1 = supplier.get().toList();
```

**Follow-up questions**
- *Why this design?* A stream may read from a one-time source like a file or a network. It does not store elements, so it cannot replay them.
- *Does the same apply to Optional?* No. An Optional is a plain value and can be used many times.

---

#### L14. [Intermediate] What is the difference between `orElse` and `orElseGet`?

**Short answer:** `orElse(x)` always evaluates its argument `x`, even when the Optional has a value. `orElseGet(supplier)` runs the supplier only if the Optional is empty.

```java
static String defaultName() {
    System.out.println("defaultName called");
    return "N/A";
}

Optional<String> opt = Optional.of("Asha");

String a = opt.orElse(defaultName());
// prints "defaultName called", a = "Asha"   (the method ran for nothing)

String b = opt.orElseGet(() -> defaultName());
// prints nothing, b = "Asha"

String c = opt.orElseThrow(() -> new IllegalStateException("missing"));
// c = "Asha"; on an empty Optional this would throw IllegalStateException
```

Use `orElse` for cheap constants (`orElse("N/A")`). Use `orElseGet` when the default is costly or has side effects (database call, creating an object, logging).

**Follow-up questions**
- *Can this cause real bugs?* Yes. `orElse(repository.save(new User()))` would save a user every time, even when one was found.
- *Other fallbacks?* `or(() -> otherOptional)` (Java 9) returns another Optional.

---

#### L15. [Intermediate] What is the right and wrong way to use Optional?

**Short answer:** Use it as a method return type when a result may be absent. Do not use it for fields, method parameters, collections, or with `isPresent()` + `get()`.

Right:
```java
Optional<User> findByEmail(String email);      // return type

String city = findByEmail("a@b.com")
    .map(User::getAddress)
    .map(Address::getCity)
    .orElse("unknown");
```

Wrong:
```java
class User {
    private Optional<String> nickname;              // wrong: field (not serializable, adds object cost)
}

void process(Optional<String> input) { }            // wrong: parameter, use overloads or null check

Optional<User> u = findByEmail("a@b.com");
if (u.isPresent()) {                                // wrong: same as a null check
    use(u.get());
}

Optional<List<String>> names;                       // wrong: return an empty list instead

Optional<String> bad = Optional.of(null);           // throws NullPointerException; use ofNullable
```

**Follow-up questions**
- *Should a repository method return `null` or `Optional`?* `Optional` for a single result. Spring Data supports `Optional<T>` return types.
- *What should a method return when there are no items?* An empty collection, never `null` and never `Optional<List>`.
- *Why not `Optional` as a parameter?* The caller can still pass `null` or an empty Optional, so it adds noise and does not remove checks.

---

#### L16. [Intermediate] How do you sort with streams and `Comparator`?

**Short answer:** Use `sorted(Comparator)`. Build comparators with `Comparator.comparing...` and chain with `thenComparing` and `reversed`.

```java
List<String> sorted = employees.stream()
    .sorted(Comparator
        .comparingDouble(Employee::salary)
        .reversed()
        .thenComparing(Employee::name))
    .map(Employee::name)
    .toList();
// sorted = [Asha, Ben, Chen, Dina, Eli]
// salary high to low: Asha 90000, Ben 70000, Chen 60000, Dina 60000 (tie broken by name), Eli 50000

List<Employee> top2 = employees.stream()
    .sorted(Comparator.comparingDouble(Employee::salary).reversed())
    .limit(2)
    .toList();
// top2 = [Asha, Ben]  (as Employee objects)
```

**Follow-up questions**
- *`sorted()` with no argument?* Uses natural order. Elements must implement `Comparable`, otherwise `ClassCastException`.
- *Is `sorted` stable?* For ordered streams, yes: equal elements keep their original relative order.
- *How to put `null` values last?* `Comparator.nullsLast(...)`.

---

#### L17. [Intermediate] What is the difference between `Collectors.toList()`, `Stream.toList()` and `toUnmodifiableList()`?

**Short answer:** `Collectors.toList()` returns a normal modifiable list (the exact type is not promised). `Stream.toList()` (Java 16) and `Collectors.toUnmodifiableList()` (Java 10) return lists that cannot be changed.

```java
List<Integer> a = Stream.of(1, 2, 3).collect(Collectors.toList());
a.add(4);
// works

List<Integer> b = Stream.of(1, 2, 3).toList();
b.add(4);
// throws UnsupportedOperationException
```

**Follow-up questions**
- *Any difference between `Stream.toList()` and `toUnmodifiableList()`?* `Stream.toList()` allows `null` elements. `toUnmodifiableList()` throws `NullPointerException` on `null`.
- *Which is better by default?* An unmodifiable result is safer when you return data from a method.

---

## Lambdas and Streams: Advanced

#### L18. [Advanced] What are the pitfalls of parallel streams?

**Short answer:** `parallel()` splits work across threads, but it is not always faster and can give wrong results if the code has shared mutable state or depends on order.

```text
list.parallelStream()
   |
   +--> split into chunks --> ForkJoinPool.commonPool() threads --> combine results
```

Pitfalls:
- **Shared mutable state:** writing to a non-thread-safe collection from the lambda is a race condition.
- **Common pool:** all parallel streams in the JVM share `ForkJoinPool.commonPool()`. One slow or blocking task (database or HTTP call) can starve the others. In a web server this affects other requests.
- **Small data or cheap work:** splitting and merging costs more than the work itself.
- **Order:** `forEach` has no order guarantee in parallel. Use `forEachOrdered` if order matters (this reduces the speed gain).
- **Non-associative or stateful functions in `reduce`:** wrong result.
- **Bad sources:** `LinkedList` and `Stream.iterate` split poorly. `ArrayList`, arrays and `IntStream.range` split well.

```java
List<Integer> result = new ArrayList<>();
IntStream.range(0, 1000)
    .parallel()
    .forEach(result::add);
// UNSAFE: ArrayList is not thread-safe. Can lose elements, or throw ArrayIndexOutOfBoundsException

List<Integer> safe = IntStream.range(0, 1000)
    .parallel()
    .boxed()
    .collect(Collectors.toList());
// safe: collect manages the threads and merges results correctly

int wrong = IntStream.rangeClosed(1, 100)
    .parallel()
    .reduce(0, (a, b) -> a - b);
// subtraction is not associative: the result is not reliable
```

**Follow-up questions**
- *When is a parallel stream worth it?* Large data (many thousands of elements or more), CPU-heavy work per element, no shared state, and a good source. Measure first.
- *How do you use a different pool?* Run the stream inside your own `ForkJoinPool` with `submit`. This works in practice but is not an officially documented guarantee. For I/O work, prefer an `ExecutorService`, `CompletableFuture` or virtual threads (Java 21).
- *What is a good alternative for I/O-bound parallel work?* `CompletableFuture` with a dedicated executor.

---

#### L19. [Advanced] How are lambdas implemented internally? How are they different from anonymous classes?

**Short answer:** The compiler turns the lambda body into a private method. At runtime, the first time the lambda runs, the JVM uses `invokedynamic` and `LambdaMetafactory` to create the object that implements the interface. An anonymous class is compiled to a real `.class` file at compile time.

| Point | Lambda | Anonymous class |
|---|---|---|
| Class file at compile time | No (generated at runtime) | Yes, `Outer$1.class` |
| `this` | the enclosing object | the anonymous object itself |
| Can implement | only a functional interface | any interface or class, many methods |
| Can have own fields/state | No | Yes |
| Variable shadowing | cannot redeclare an enclosing local name | can shadow |

```java
class Demo {

    String name = "demo";

    void run() {
        Runnable lambda = () -> System.out.println(this.name);
        // this = Demo object, prints: demo

        Runnable anon = new Runnable() {
            String name = "anon";

            @Override
            public void run() {
                System.out.println(this.name);
                // this = the anonymous Runnable, prints: anon
            }
        };
    }
}
```

A lambda that captures no variables is typically created once and reused. A capturing lambda creates a new object per evaluation. Do not depend on this, it is a JVM implementation detail.

**Follow-up questions**
- *What does `javap -c -p` show for a lambda?* An `invokedynamic` instruction and a synthetic method like `lambda$run$0`.
- *Why `invokedynamic` and not inner classes?* It lets the JVM choose the best creation strategy later, with no class file per lambda and a smaller jar.

---

#### L20. [Advanced] Coding: Find the second highest salary.

**Short answer:** Take salaries, remove duplicates, sort descending, skip the first, take the next.

```java
Optional<Double> secondHighest = employees.stream()
    .map(Employee::salary)
    .distinct()
    .sorted(Comparator.reverseOrder())
    .skip(1)
    .findFirst();
// salaries: 90000, 70000, 60000, 60000, 50000
// after distinct and sort desc: 90000, 70000, 60000, 50000
// skip 1 -> first is 70000
// secondHighest = Optional[70000.0]
```

Employee with the second highest salary:

```java
Optional<Employee> employee = employees.stream()
    .filter(e -> e.salary() == 70000.0)
    .findFirst();
// to avoid hard-coding, first compute the salary as above, then filter on it
```

Per department (second highest in each):

```java
Map<String, Optional<Double>> secondByDept = employees.stream()
    .collect(Collectors.groupingBy(
        Employee::dept,
        Collectors.collectingAndThen(
            Collectors.mapping(Employee::salary, Collectors.toList()),
            salaries -> salaries.stream()
                .distinct()
                .sorted(Comparator.reverseOrder())
                .skip(1)
                .findFirst()
        )
    ));
// IT -> Optional[70000.0]
// HR -> Optional.empty   (only one distinct salary: 60000)
// Sales -> Optional.empty
```

**Follow-up questions**
- *Why `distinct()`?* Without it, with ties at the top, the "second" would equal the first.
- *Why `Optional`?* There may be fewer than two distinct salaries.
- *Faster approach?* Sorting is O(n log n). A single pass with two variables (`max`, `secondMax`) is O(n).
- *SQL version?* `SELECT DISTINCT salary FROM employee ORDER BY salary DESC LIMIT 1 OFFSET 1`.

---

#### L21. [Advanced] Coding: Count the frequency of each word.

**Short answer:** Split the text into words and use `groupingBy` with `counting`.

```java
String text = "to be or not to be";

Map<String, Long> frequency = Arrays.stream(text.split(" "))
    .collect(Collectors.groupingBy(
        Function.identity(),
        LinkedHashMap::new,
        Collectors.counting()
    ));
// frequency = {to=2, be=2, or=1, not=1}
// LinkedHashMap keeps the first-seen order. With the default HashMap the order is not guaranteed.
```

Real text with mixed case and punctuation:

```java
Map<String, Long> cleaned = Arrays.stream(text.toLowerCase().split("\\W+"))
    .filter(w -> !w.isEmpty())
    .collect(Collectors.groupingBy(
        Function.identity(),
        Collectors.counting()
    ));
```

Most frequent word:

```java
Optional<Map.Entry<String, Long>> top = frequency.entrySet().stream()
    .max(Map.Entry.comparingByValue());
// top = one of to=2 or be=2 (a tie; either may be returned, do not rely on which)
```

**Follow-up questions**
- *Without streams?* `map.merge(word, 1L, Long::sum)` inside a loop.
- *Why `Function.identity()`?* It is `x -> x`. The word itself is the key.
- *Sort by count descending?* `frequency.entrySet().stream().sorted(Map.Entry.<String, Long>comparingByValue().reversed())`.

---

#### L22. [Advanced] Coding: Find the first non-repeating character in a string.

**Short answer:** Count each character in insertion order (`LinkedHashMap`), then take the first with count 1.

```java
String input = "swiss";

Optional<Character> firstUnique = input.chars()
    .mapToObj(c -> (char) c)
    .collect(Collectors.groupingBy(
        Function.identity(),
        LinkedHashMap::new,
        Collectors.counting()
    ))
    .entrySet()
    .stream()
    .filter(entry -> entry.getValue() == 1L)
    .map(Map.Entry::getKey)
    .findFirst();
// counts in order: s=3, w=1, i=1
// firstUnique = Optional[w]
```

`input.chars()` returns an `IntStream` of character codes. `mapToObj(c -> (char) c)` turns each code into a `Character` so it can be a map key.

Without streams (simple loop version):

```java
static char firstUnique(String s) {
    Map<Character, Integer> counts = new LinkedHashMap<>();
    for (char c : s.toCharArray()) {
        counts.merge(c, 1, Integer::sum);
    }
    for (Map.Entry<Character, Integer> entry : counts.entrySet()) {
        if (entry.getValue() == 1) {
            return entry.getKey();
        }
    }
    throw new NoSuchElementException("no unique character");
}
// firstUnique("swiss") returns 'w'
```

**Follow-up questions**
- *Why `LinkedHashMap`?* A `HashMap` does not keep order, so "first" would be wrong.
- *Complexity?* O(n) time, O(k) space where k is the number of distinct characters.
- *Why not `filter(...).findFirst()` on the characters directly?* You would need counts first, so two passes are needed anyway.

---

#### L23. [Advanced] Coding: Highest-paid employee per department, and department statistics.

**Short answer:** Use `groupingBy` with a downstream collector such as `maxBy`, wrapped in `collectingAndThen`, or `summarizingDouble` for statistics.

```java
Map<String, String> topNameByDept = employees.stream()
    .collect(Collectors.groupingBy(
        Employee::dept,
        Collectors.collectingAndThen(
            Collectors.maxBy(Comparator.comparingDouble(Employee::salary)),
            best -> best.get().name()
        )
    ));
// {IT=Asha, HR=Chen, Sales=Eli}   (order may differ)
// HR has a tie (Chen and Dina, both 60000). maxBy returns one of them; for ties
// the documented behaviour is not something to depend on.
// best.get() is safe here: every group has at least one element.

Map<String, DoubleSummaryStatistics> stats = employees.stream()
    .collect(Collectors.groupingBy(
        Employee::dept,
        Collectors.summarizingDouble(Employee::salary)
    ));
// stats.get("IT").getMax()     -> 90000.0
// stats.get("IT").getAverage() -> 80000.0
// stats.get("IT").getCount()   -> 2
```

**Follow-up questions**
- *Why is `maxBy` result an Optional?* A stream can be empty. Inside `groupingBy` groups are never empty, so `get()` is safe there.
- *Top 3 overall?* `sorted(Comparator.comparingDouble(Employee::salary).reversed()).limit(3)`.
- *Is there an easier way for max?* `Collectors.toMap(dept, e -> e, BinaryOperator.maxBy(comparator))` when you want the full object.

---

#### L24. [Advanced] What are common stream mistakes (side effects, `peek`, stateful lambdas)?

**Short answer:** Lambdas in a stream should be stateless and free of side effects. Changing outside state inside `map` or `filter` gives wrong or unpredictable results, especially in parallel.

```java
// Bad: building a list as a side effect
List<String> names = new ArrayList<>();
employees.stream()
    .filter(e -> e.salary() > 60000)
    .forEach(e -> names.add(e.name()));

// Good: let the stream collect
List<String> better = employees.stream()
    .filter(e -> e.salary() > 60000)
    .map(Employee::name)
    .toList();
// better = [Asha, Ben]

// Bad: relying on peek for logic. peek may be skipped by optimisations
// (for example, count() on a sized source can skip the pipeline since Java 9)
long n = List.of(1, 2, 3).stream()
    .peek(x -> System.out.println("seen " + x))
    .count();
// n = 3; the "seen" lines are not printed in Java 9 and later, because count() is computed from the source size

// Bad: modifying the source while streaming
List<Integer> source = new ArrayList<>(List.of(1, 2, 3));
source.stream().forEach(x -> source.add(x));
// throws ConcurrentModificationException (stream sources must not change during the pipeline)
```

Other mistakes: using a stream for a simple loop that is clearer as a `for`, using `Optional.get()` without a check, boxing in hot paths (use `mapToInt`), and calling `.parallel()` without measuring.

**Follow-up questions**
- *Is `forEach` bad?* No. It is fine for the final action (print, send). It is bad when used to build results.
- *What does "non-interfering" mean?* A pipeline must not change its source while running.
- *How do you handle checked exceptions in a lambda?* Wrap them in a helper method or catch inside the lambda and rethrow as an unchecked exception. The functional interfaces in `java.util.function` do not declare checked exceptions.

---

# 6. Modern Java: records, sealed, enums, switch, patterns, var, versions

## Modern Java: Basic

#### M1. [Basic] What is a record?

**Short answer:** A record is a short, immutable data class. You declare only the components, and the compiler generates the constructor, accessors, `equals`, `hashCode` and `toString`. Final in Java 16.

```java
record Point(int x, int y) {
}

Point p = new Point(1, 2);
int x = p.x();
// x = 1   (accessor is x(), not getX())

String text = p.toString();
// text = "Point[x=1, y=2]"

boolean same = p.equals(new Point(1, 2));
// same = true   (compared by values)
```

Validation with a compact constructor (no parameter list, runs before fields are set):

```java
record Money(long amount, String currency) {

    Money {
        if (amount < 0) {
            throw new IllegalArgumentException("amount must be >= 0");
        }
        currency = currency.toUpperCase();     // you can normalise the parameter
    }
}
```

Rules: a record is implicitly `final`, cannot extend another class (it extends `java.lang.Record`), can implement interfaces, can have static fields and extra methods, but no extra instance fields.

**Follow-up questions**
- *Is a record fully immutable?* It is shallowly immutable. Fields cannot be reassigned, but a `List` component can still be modified. Copy it: `items = List.copyOf(items);` in the compact constructor.
- *Can a record be a JPA entity?* No. JPA needs a no-argument constructor and mutable fields. Records are good for DTOs and query projections. Jackson (2.12 and later) supports them.
- *Record vs Lombok `@Value`?* A record is a language feature with no library. `@Value` is generated by an annotation processor.

---

#### M2. [Basic] What are enums in Java? Can they have fields and methods?

**Short answer:** An enum is a fixed set of named constants. Each constant is a singleton object of the enum class. Enums can have fields, constructors, methods, implement interfaces, and even constant-specific method bodies.

```java
enum Status {
    ACTIVE("A"),
    INACTIVE("I");

    private final String code;

    Status(String code) {            // constructor is always private
        this.code = code;
    }

    public String code() {
        return code;
    }
}

Status s = Status.valueOf("ACTIVE");
// s = ACTIVE   (invalid name throws IllegalArgumentException)

Status[] all = Status.values();
// all = [ACTIVE, INACTIVE]

String code = s.code();
// code = "A"
```

Constant-specific behaviour with an abstract method:

```java
enum Operation {

    ADD("+") {
        @Override
        int apply(int a, int b) {
            return a + b;
        }
    },
    MULTIPLY("*") {
        @Override
        int apply(int a, int b) {
            return a * b;
        }
    };

    private final String symbol;

    Operation(String symbol) {
        this.symbol = symbol;
    }

    abstract int apply(int a, int b);
}

int r = Operation.MULTIPLY.apply(3, 4);
// r = 12
```

**Follow-up questions**
- *Can an enum extend a class?* No, it already extends `java.lang.Enum`. It can implement interfaces.
- *`==` or `equals` for enums?* `==` is fine and null-safe on the left side. There is exactly one instance per constant.
- *Why avoid `ordinal()`?* It is the position in the declaration. Reordering constants changes it. Never store it in a database. Use `EnumType.STRING` in JPA.

---

#### M3. [Basic] What are `var`, text blocks and the other small syntax features?

**Short answer:** `var` (Java 10) lets the compiler infer a local variable's type. Text blocks (Java 15) are multi-line string literals written with `"""`.

```java
var names = new ArrayList<String>();
// type is ArrayList<String>; the type is still fixed at compile time (static typing)

var count = 10;
// int

String json = """
    {
      "name": "Asha",
      "dept": "IT"
    }
    """;
// multi-line string; the indentation shared by all lines is removed
```

Where `var` is allowed and not allowed:

| Allowed | Not allowed |
|---|---|
| local variables with an initialiser | fields |
| `for` loop variables | method parameters and return types |
| lambda parameters (Java 11) | `var x;` without a value, `var x = null;` |

**Follow-up questions**
- *Is `var` dynamic typing?* No. The type is inferred once at compile time and does not change.
- *When to avoid `var`?* When the type is not obvious from the right side, for example `var result = service.process();`.
- *Other small features?* `Stream.toList()` (16), `String.isBlank/strip/lines/repeat` (11), `List.of/Map.of` (9), unnamed variable `_` (final in 22).

---

#### M4. [Basic] What changed in each Java version that interviewers ask about?

**Short answer:** Know the LTS (long-term support) releases: 8, 11, 17, 21 and 25.

| Version | Notable features |
|---|---|
| 8 (LTS, 2014) | lambdas, streams, `Optional`, default methods, `java.time` |
| 9 | modules, `List.of` / `Map.of`, `Optional.ifPresentOrElse` |
| 10 | `var` |
| 11 (LTS) | `HttpClient`, new `String` methods, `var` in lambda parameters |
| 14 | switch expressions |
| 15 | text blocks |
| 16 | records, pattern matching for `instanceof`, `Stream.toList()` |
| 17 (LTS) | sealed classes and interfaces |
| 21 (LTS) | virtual threads, pattern matching for `switch`, record patterns, sequenced collections |
| 22 | unnamed variables and patterns (`_`) |
| 25 (LTS, Sept 2025) | scoped values, flexible constructor bodies, compact source files, module import declarations (all final; check the release notes for the exact list) |

**Follow-up questions**
- *What is a preview feature?* A feature shipped in a release but not final. It must be enabled with `--enable-preview` and may change.
- *Which version should a project use?* The newest LTS the frameworks and your platform support (Spring Boot 3 needs Java 17 or higher).

---

## Modern Java: Intermediate

#### M5. [Intermediate] What are sealed classes and interfaces?

**Short answer:** A sealed type lists which classes may extend or implement it, using `permits`. This gives a closed hierarchy that the compiler knows completely. Final in Java 17.

```java
sealed interface Shape permits Circle, Rectangle {
}

record Circle(double radius) implements Shape {
}

record Rectangle(double width, double height) implements Shape {
}
```

Each permitted subclass must be exactly one of:
- `final` (cannot be extended, records are implicitly final)
- `sealed` (and list its own permitted subclasses)
- `non-sealed` (open again for anyone to extend)

```text
        Shape (sealed)
       /              \
   Circle (record)  Rectangle (record)
```

**Follow-up questions**
- *Why use sealed?* To model a fixed set of options (for example payment types, or API results `Success` / `Failure`). The compiler can check a `switch` over them is complete (see M7).
- *Where must permitted classes be?* In the same module, or the same package if the module system is not used. If they are in the same file, `permits` can be omitted.
- *Sealed vs enum?* Enum constants are singletons without per-instance data. Sealed subtypes are classes, each can carry different fields.

---

#### M6. [Intermediate] What is a switch expression?

**Short answer:** A `switch` that returns a value, uses `->` arrows, has no fall-through, and must cover all cases. Final in Java 14.

```java
enum Day { MONDAY, TUESDAY, SATURDAY, SUNDAY }

String type = switch (day) {
    case SATURDAY, SUNDAY -> "weekend";
    case MONDAY, TUESDAY -> "weekday";
};
// no default needed: all enum constants are covered
// the compiler rejects a missing constant

int length = switch (word) {
    case "a", "b" -> 1;
    default -> {
        int n = word.length();
        yield n * 2;          // yield returns a value from a block
    }
};
```

Old switch statement problems that this fixes: forgetting `break` runs the next case (fall-through), and the old form is a statement, not a value.

**Follow-up questions**
- *`yield` vs `return`?* `yield` gives the value of the switch expression. `return` would leave the method.
- *Does it work on `String`, `enum`, `int`?* Yes. Since Java 21 also on any object with patterns (M7).
- *What if no case matches?* For an enum without `default`, the compiler requires all constants. Missing ones fail compilation. At runtime, an unexpected new constant throws `MatchException` (Java 21; earlier `IncompatibleClassChangeError`).

---

#### M7. [Intermediate] What is pattern matching for `instanceof` and `switch`? What are record patterns?

**Short answer:** Pattern matching tests a type and binds a variable in one step, with no manual cast. `instanceof` patterns are final in Java 16. Switch patterns and record patterns are final in Java 21.

```java
// Old
if (obj instanceof String) {
    String s = (String) obj;
    System.out.println(s.length());
}

// Pattern matching (Java 16)
if (obj instanceof String s && s.length() > 3) {
    System.out.println(s.length());
}

// Switch patterns (Java 21)
static String describe(Object o) {
    return switch (o) {
        case null -> "null";
        case Integer i when i > 10 -> "big int " + i;     // 'when' is a guard condition
        case Integer i -> "int " + i;
        case String s -> "string of length " + s.length();
        default -> "other";
    };
}
// describe(42)  -> "big int 42"
// describe(5)   -> "int 5"
// describe("hi") -> "string of length 2"

// Record patterns (Java 21): take the record apart directly
record Point(int x, int y) {
}

if (obj instanceof Point(int x, int y)) {
    System.out.println(x + y);
}
```

**Follow-up questions**
- *Why does the order of `case` labels matter?* The first match wins. A general case before a specific case makes the specific one unreachable and the compiler reports an error.
- *Can `null` be handled?* Yes, with `case null` (Java 21). Without it, a `null` selector throws `NullPointerException`.
- *Are record patterns nestable?* Yes: `case Line(Point(var x1, var y1), Point p2) -> ...`.

---

#### M8. [Intermediate] What are Sequenced Collections (Java 21)?

**Short answer:** New interfaces (`SequencedCollection`, `SequencedSet`, `SequencedMap`) for collections with a defined encounter order. They add the same methods to get the first and last element and to reverse.

| Method | What it does |
|---|---|
| `getFirst()` / `getLast()` | returns the first or last element |
| `addFirst(e)` / `addLast(e)` | inserts at the start or end |
| `removeFirst()` / `removeLast()` | removes and returns the first or last element |
| `reversed()` | returns a reverse-order view (not a copy) |
| `SequencedMap`: `firstEntry()`, `lastEntry()`, `pollFirstEntry()`, `putFirst(k, v)` | same idea for maps |

```java
List<String> list = new ArrayList<>(List.of("a", "b", "c"));

String first = list.getFirst();
// first = "a"   (before Java 21: list.get(0))

String last = list.getLast();
// last = "c"    (before Java 21: list.get(list.size() - 1))

List<String> backwards = list.reversed();
// backwards = [c, b, a]   (a view: changes show in the original list)
```

Implemented by `List`, `Deque`, `LinkedHashSet`, `LinkedHashMap`, `SortedSet`, `SortedMap`. `HashSet` and `HashMap` have no order, so they are not sequenced.

**Follow-up questions**
- *What did `getLast()` on a `List` need before?* `list.get(list.size() - 1)`, which fails on an empty list with `IndexOutOfBoundsException`.
- *What does `getFirst()` do on an empty list?* Throws `NoSuchElementException`.

---

## Modern Java: Advanced

#### M9. [Advanced] How do you use EnumMap, EnumSet and an enum singleton? How do enums work in a switch?

**Short answer:** `EnumMap` and `EnumSet` are fast, compact collections for enum keys. An enum with one constant is the safest singleton in Java.

```java
// EnumMap: backed by an array indexed by ordinal; keeps keys in declaration order
Map<Day, String> plan = new EnumMap<>(Day.class);
plan.put(Day.SUNDAY, "rest");
plan.put(Day.MONDAY, "work");
// iteration order: MONDAY, then SUNDAY (declaration order, not insertion order)

EnumSet<Day> weekend = EnumSet.of(Day.SATURDAY, Day.SUNDAY);
// stored internally as bits

// Enum singleton
enum AppConfig {

    INSTANCE;

    private final Properties props = new Properties();

    public String get(String key) {
        return props.getProperty(key);
    }
}

String value = AppConfig.INSTANCE.get("db.url");
```

Why the enum singleton is the safest:
- The JVM guarantees one instance per constant, created once when the class loads (thread-safe).
- Serialization cannot create a second copy.
- Reflection cannot create a new instance (`Constructor.newInstance` throws for enums).

Limit: it cannot extend a class and it is not lazy in the way the holder idiom is (D1).

**Follow-up questions**
- *Why `EnumMap` instead of `HashMap`?* Faster, uses less memory, and has a predictable order. It needs no hashing.
- *Is the enum constructor ever public?* No. It is always private, so you cannot create new constants.
- *Can enums be used as Spring `@Value` or JSON values?* Yes. Jackson reads them by name by default.

---

#### M10. [Advanced] How do sealed types, records and pattern switch work together?

**Short answer:** A sealed interface of records gives a closed set of data types. A pattern `switch` over it is checked by the compiler for completeness, so no `default` is needed. Adding a new subtype later causes compile errors in every switch that must handle it.

```java
sealed interface PaymentResult permits Success, Declined, Error {
}

record Success(String transactionId) implements PaymentResult {
}

record Declined(String reason) implements PaymentResult {
}

record Error(Exception cause) implements PaymentResult {
}

static String message(PaymentResult result) {
    return switch (result) {
        case Success(String id) -> "Paid, id " + id;
        case Declined(String reason) -> "Declined: " + reason;
        case Error(Exception cause) -> "Error: " + cause.getMessage();
    };
}
// no default: the compiler knows only three subtypes exist
// message(new Success("T1")) -> "Paid, id T1"
```

This is an "algebraic data type" style: it replaces the Visitor pattern and long `instanceof` chains.

**Follow-up questions**
- *What if I add a `Pending` record to the sealed interface?* Every switch without a `default` stops compiling until it handles `Pending`. That is the benefit.
- *Should I add `default` to such a switch?* No. It would hide missing cases and turn compile errors into silent bugs.
- *Which version is needed?* Java 21 for record patterns and switch patterns. Sealed alone needs 17.

---

# 7. Generics

## Generics: Basic

#### G1. [Basic] What are generics and why do we use them?

**Short answer:** Generics let a class or method work with a type that is given later as a parameter (`List<String>`). The compiler then checks types at compile time and no casts are needed.

```java
// Without generics (before Java 5)
List raw = new ArrayList();
raw.add("a");
raw.add(1);
String s = (String) raw.get(1);
// ClassCastException at runtime

// With generics
List<String> list = new ArrayList<>();
list.add("a");
list.add(1);
// compile error: caught early

String first = list.get(0);
// no cast needed
```

Your own generic class and method:

```java
class Box<T> {                          // T is a type parameter

    private T value;

    public void set(T value) {
        this.value = value;
    }

    public T get() {
        return value;
    }
}

static <T> T firstOf(List<T> items) {   // generic method: <T> comes before the return type
    return items.get(0);
}

Box<String> box = new Box<>();          // <> is the diamond: the compiler infers the type
```

Naming: `T` type, `E` element, `K` key, `V` value, `R` result.

**Follow-up questions**
- *What is a raw type?* A generic class used without a type argument (`List`). It disables type checks. Avoid it.
- *Generic class vs generic method?* A generic method has its own type parameter and can be inside a non-generic class.

---

## Generics: Intermediate

#### G2. [Intermediate] What are bounded types, wildcards and PECS?

**Short answer:** A bound limits which types are allowed. A wildcard (`?`) means "some unknown type". PECS: Producer Extends, Consumer Super.

```java
// Upper bound: T must be Number or a subclass
static <T extends Number> double sum(List<T> list) {
    double total = 0;
    for (T n : list) {
        total += n.doubleValue();
    }
    return total;
}

// Multiple bounds
static <T extends Number & Comparable<T>> T max(T a, T b) {
    return a.compareTo(b) >= 0 ? a : b;
}
```

| Syntax | Meaning | Can read as | Can add |
|---|---|---|---|
| `List<?>` | list of unknown type | `Object` | only `null` |
| `List<? extends Number>` | list of `Number` or a subclass (upper bound) | `Number` | only `null` |
| `List<? super Integer>` | list of `Integer` or a superclass (lower bound) | `Object` | `Integer` and its subclasses |

PECS rule:
- If a collection **produces** (you only read from it), use `? extends T`.
- If a collection **consumes** (you only write into it), use `? super T`.

```java
static <T> void copy(List<? super T> destination, List<? extends T> source) {
    for (T item : source) {          // source produces
        destination.add(item);       // destination consumes
    }
}

List<Integer> ints = List.of(1, 2);
List<Number> numbers = new ArrayList<>();
copy(numbers, ints);
// numbers = [1, 2]
```

**Follow-up questions**
- *Why can't I add to `List<? extends Number>`?* The real type could be `List<Integer>`. Adding a `Double` would break it, so the compiler blocks all adds except `null`.
- *Where does the JDK use PECS?* `Collections.copy`, `Collections.sort(List<T>, Comparator<? super T>)`, `Stream.map(Function<? super T, ? extends R>)`.

---

## Generics: Advanced

#### G3. [Advanced] What is type erasure and what are the limitations of generics?

**Short answer:** The compiler removes generic type information after checking it. At runtime `List<String>` and `List<Integer>` are both just `List`. This keeps compatibility with old code but creates limits.

```java
List<String> a = new ArrayList<>();
List<Integer> b = new ArrayList<>();

boolean same = a.getClass() == b.getClass();
// same = true   (both are ArrayList at runtime)
```

How the compiler rewrites code:
- `T` becomes `Object` (or its bound, for `T extends Number` it becomes `Number`).
- It inserts casts where needed.
- It generates "bridge methods" so overriding still works.

Limitations (all caused by erasure):

| You cannot | Reason |
|---|---|
| `new T()` or `new T[10]` | `T` is unknown at runtime |
| `obj instanceof List<String>` | `List<String>` does not exist at runtime (`instanceof List<?>` is allowed) |
| use primitives as type arguments (`List<int>`) | type arguments must be reference types, use `List<Integer>` |
| `static T field` | `T` belongs to an instance, not the class |
| `catch (T e)` or `class MyEx<T> extends Exception` | exceptions cannot be generic |
| overload `void f(List<String>)` and `void f(List<Integer>)` | same signature after erasure |

Workaround to know the type at runtime: pass a `Class<T>` argument (`Class<T> type`) or a supplier (`Supplier<T>`). Jackson uses `TypeReference` for the same reason.

**Follow-up questions**
- *What is heap pollution?* Mixing raw and generic types so a list of the wrong type is used. It causes `ClassCastException` later, far from the real mistake.
- *Why did Java choose erasure?* So generic code could still work with older, non-generic libraries (Java 5 backward compatibility).
- *What is `@SafeVarargs`?* A promise that a generic varargs method does not misuse its array. It silences an unchecked warning.

---

#### G4. [Advanced] Why is `List<Integer>` not a `List<Number>`?

**Short answer:** Generics are invariant: `List<Integer>` is not a subtype of `List<Number>`, even though `Integer` is a subtype of `Number`. If it were, you could add a `Double` into a list of `Integer`.

```java
List<Integer> ints = new ArrayList<>();
List<Number> nums = ints;
// compile error

// If it were allowed:
// nums.add(3.14);          // a Double inside a List<Integer>
// Integer x = ints.get(0); // ClassCastException

List<? extends Number> readOnly = ints;
// OK: read as Number, no adds
```

Arrays are different: they are covariant (`Integer[]` is a `Number[]`), so the same mistake compiles and fails at runtime.

```java
Number[] array = new Integer[1];
array[0] = 3.14;
// throws ArrayStoreException at runtime
```

Generics move this error to compile time.

**Follow-up questions**
- *How can a method accept `List<Integer>` and `List<Double>`?* Use `List<? extends Number>` or a generic method `<T extends Number>`.
- *Are arrays or generic collections safer?* Generic collections: the error shows at compile time.
- *Why can't you create a generic array `new List<String>[10]`?* Arrays check element types at runtime, but erasure removes the type, so Java forbids it.

---

# 8. Design Patterns

## Design Patterns: Basic

#### D1. [Basic] Singleton: how do you implement it (enum, double-checked locking, holder)?

**Short answer:** A singleton has exactly one instance and a global access point. The safest forms are the enum singleton and the initialization-on-demand holder.

**Double-checked locking:**

```java
public class Singleton {

    private static volatile Singleton instance;

    private Singleton() {
    }

    public static Singleton getInstance() {
        if (instance == null) {                    // first check, no lock (fast path)
            synchronized (Singleton.class) {
                if (instance == null) {            // second check, inside the lock
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

`volatile` is required. Creating an object has several steps (allocate memory, run constructor, assign reference). Without `volatile`, the CPU or compiler may reorder them, and another thread could see a non-null but half-built object.

**Holder idiom (lazy and thread-safe, no locks):**

```java
public class Singleton {

    private Singleton() {
    }

    private static class Holder {
        static final Singleton INSTANCE = new Singleton();
    }

    public static Singleton getInstance() {
        return Holder.INSTANCE;      // Holder loads, and INSTANCE is created, on first call
    }
}
```

The JVM guarantees class initialization is thread-safe, and `Holder` loads only when `getInstance()` is first called.

**Enum singleton:** see M9. Safe against reflection and serialization.

**Follow-up questions**
- *How can a singleton be broken?* Reflection (calling the private constructor), serialization (a new object on deserialize, fix with `readResolve`), cloning, and multiple class loaders.
- *Is a Spring singleton bean the same thing?* No. It is one instance per Spring container, not per JVM. Multiple containers or multiple application instances each have their own.
- *Why are singletons criticised?* They hide dependencies and are hard to test. Dependency injection is usually better.

---

#### D2. [Basic] Factory pattern

**Short answer:** A factory creates objects so the caller does not use `new` with a concrete class. The caller asks for a type and gets an object that matches an interface.

```java
interface Notification {
    void send(String message);
}

class EmailNotification implements Notification {
    @Override
    public void send(String message) {
        System.out.println("Email: " + message);
    }
}

class SmsNotification implements Notification {
    @Override
    public void send(String message) {
        System.out.println("SMS: " + message);
    }
}

class NotificationFactory {

    static Notification create(String type) {
        return switch (type) {
            case "EMAIL" -> new EmailNotification();
            case "SMS" -> new SmsNotification();
            default -> throw new IllegalArgumentException("Unknown type: " + type);
        };
    }
}

Notification n = NotificationFactory.create("SMS");
n.send("hello");
// prints: SMS: hello
```

**Follow-up questions**
- *Factory Method vs Abstract Factory?* Factory Method: one method creates one product, subclasses decide which. Abstract Factory: a factory of related families of objects.
- *Where does Spring use it?* `BeanFactory` / `ApplicationContext` create beans. `@Bean` methods are factory methods.
- *What if I add a new type?* You edit the factory (open for change). In Spring, inject `Map<String, Notification>` to avoid editing the factory.

---

#### D3. [Basic] Builder pattern

**Short answer:** A builder creates a complex object step by step with readable, named steps. It avoids constructors with many parameters and allows immutable objects.

```java
public class User {

    private final String name;
    private final String email;
    private final int age;

    private User(Builder builder) {
        this.name = builder.name;
        this.email = builder.email;
        this.age = builder.age;
    }

    public static Builder builder() {
        return new Builder();
    }

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
            if (name == null) {
                throw new IllegalStateException("name is required");
            }
            return new User(this);
        }
    }
}

User user = User.builder()
    .name("Asha")
    .email("asha@example.com")
    .age(30)
    .build();
```

**Follow-up questions**
- *Where is it in the JDK?* `StringBuilder`, `Stream.Builder`, `HttpRequest.newBuilder()`.
- *Easier way?* Lombok `@Builder` generates the code.
- *Builder vs telescoping constructors?* Telescoping constructors (many overloads) are hard to read, and you cannot tell which argument is which.

---

#### D4. [Basic] Strategy pattern

**Short answer:** Strategy puts interchangeable algorithms behind one interface so the algorithm can be chosen at runtime, without long `if/else` chains.

```java
interface DiscountStrategy {
    double apply(double price);
}

class NoDiscount implements DiscountStrategy {
    @Override
    public double apply(double price) {
        return price;
    }
}

class TenPercentDiscount implements DiscountStrategy {
    @Override
    public double apply(double price) {
        return price * 0.9;
    }
}

class Checkout {

    private final DiscountStrategy strategy;

    Checkout(DiscountStrategy strategy) {
        this.strategy = strategy;
    }

    double total(double price) {
        return strategy.apply(price);
    }
}

double total = new Checkout(new TenPercentDiscount()).total(200);
// total = 180.0

// With lambdas, a functional interface is a strategy
DiscountStrategy flat50 = price -> price - 50;
```

**Follow-up questions**
- *Where in Java?* `Comparator` passed to `sort` is a strategy. In Spring, inject `Map<String, DiscountStrategy>` and pick by bean name.
- *Strategy vs State?* Strategy: the caller picks the algorithm. State: the object changes its own behaviour as its state changes.

---

## Design Patterns: Intermediate

#### D5. [Intermediate] Observer pattern

**Short answer:** One object (the subject) keeps a list of listeners (observers) and notifies all of them when something happens. The subject does not know the details of the observers.

```java
interface OrderListener {
    void onOrderPlaced(String orderId);
}

class OrderService {

    private final List<OrderListener> listeners = new ArrayList<>();

    void register(OrderListener listener) {
        listeners.add(listener);
    }

    void placeOrder(String orderId) {
        // ... save the order
        for (OrderListener listener : listeners) {
            listener.onOrderPlaced(orderId);
        }
    }
}

OrderService service = new OrderService();
service.register(id -> System.out.println("Email sent for " + id));
service.register(id -> System.out.println("Stock updated for " + id));
service.placeOrder("A1");
// prints:
// Email sent for A1
// Stock updated for A1
```

**Follow-up questions**
- *Where do you see it?* Spring `ApplicationEvent` and `@EventListener`, Swing listeners, and Kafka/message brokers (publish-subscribe, but across services and asynchronous).
- *Problems?* A slow listener blocks the subject (when called synchronously), exceptions in one listener can stop the others, and forgotten listeners cause memory leaks (unregister them).
- *`java.util.Observable`?* Deprecated since Java 9. Do not use.

---

#### D6. [Intermediate] Decorator pattern

**Short answer:** A decorator wraps an object with the same interface and adds behaviour before or after calling it. You can stack several decorators.

```java
interface Coffee {
    double cost();
}

class SimpleCoffee implements Coffee {
    @Override
    public double cost() {
        return 2.0;
    }
}

class MilkDecorator implements Coffee {

    private final Coffee inner;

    MilkDecorator(Coffee inner) {
        this.inner = inner;
    }

    @Override
    public double cost() {
        return inner.cost() + 0.5;
    }
}

Coffee order = new MilkDecorator(new MilkDecorator(new SimpleCoffee()));
double price = order.cost();
// price = 3.0   (2.0 + 0.5 + 0.5)
```

```text
MilkDecorator -> MilkDecorator -> SimpleCoffee
   (+0.5)           (+0.5)          (2.0)
```

**Follow-up questions**
- *Where in the JDK?* `new BufferedReader(new InputStreamReader(System.in))`, `Collections.unmodifiableList(list)`.
- *Decorator vs inheritance?* Decorators are combined at runtime. Inheritance fixes the behaviour at compile time and can lead to many subclasses.
- *Decorator vs Proxy?* Decorator adds behaviour the caller wants. Proxy controls access to the same object (D7). The structure is similar, the intent differs.

---

## Design Patterns: Advanced

#### D7. [Advanced] Proxy pattern (and how Spring uses it)

**Short answer:** A proxy is a stand-in object with the same interface that controls access to the real object. It can add things like logging, caching, security or transactions.

```java
interface Report {
    String generate();
}

class RealReport implements Report {
    @Override
    public String generate() {
        return "report data";
    }
}

class LoggingReportProxy implements Report {

    private final Report target;

    LoggingReportProxy(Report target) {
        this.target = target;
    }

    @Override
    public String generate() {
        System.out.println("before generate");
        String result = target.generate();
        System.out.println("after generate");
        return result;
    }
}
```

Dynamic proxy (created at runtime, no hand-written class):

```java
Report real = new RealReport();

Report proxy = (Report) Proxy.newProxyInstance(
    Report.class.getClassLoader(),
    new Class<?>[] { Report.class },
    (proxyObject, method, args) -> {
        System.out.println("calling " + method.getName());
        return method.invoke(real, args);
    }
);

String data = proxy.generate();
// prints: calling generate
// data = "report data"
```

Spring uses two kinds of proxies for `@Transactional`, `@Async`, `@Cacheable` and AOP:

| Kind | Used when | How |
|---|---|---|
| JDK dynamic proxy | the bean implements an interface | `java.lang.reflect.Proxy` |
| CGLIB proxy | the bean has no interface (Spring Boot default) | generates a subclass at runtime |

**Follow-up questions**
- *Why does `@Transactional` not work when I call another method of the same class?* The call goes `this.method()`, not through the proxy, so the transaction logic is skipped. Move it to another bean or inject the proxy.
- *Why can't CGLIB proxy a `final` class or `final` method?* It works by subclassing and overriding, which `final` forbids.
- *Types of proxy?* Virtual proxy (lazy loading, like Hibernate lazy entities), protection proxy (access control), remote proxy (calls over the network), logging/caching proxy.
