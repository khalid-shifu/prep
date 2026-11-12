# Java Notes

## Index

1. [JVM Internals](#1-jvm-internals)
   - 1.1 [javac](#11-javac)
   - 1.2 [Bytecode](#12-bytecode)
   - 1.3 [Class loading](#13-class-loading)
   - 1.4 [Verification](#14-verification)
   - 1.5 [JIT compiler](#15-jit-compiler)
   - 1.6 [Class loaders and parent delegation](#16-class-loaders-and-parent-delegation)
   - 1.7 [Runtime memory areas](#17-runtime-memory-areas)
     - 1.7.1 [Heap](#171-heap)
     - 1.7.2 [Stack](#172-stack)
     - 1.7.3 [Metaspace](#173-metaspace)
     - 1.7.4 [PC register](#174-pc-register)
     - 1.7.5 [Native method stack](#175-native-method-stack)
   - 1.8 [Object layout in memory](#18-object-layout-in-memory)
   - 1.9 [Escape analysis](#19-escape-analysis)
2. [Garbage Collection](#2-garbage-collection)
   - 2.1 [What is garbage collection](#21-what-is-garbage-collection)
   - 2.2 [GC roots and reachability](#22-gc-roots-and-reachability)
   - 2.3 [Generational heap](#23-generational-heap)
   - 2.4 [Minor GC vs Major GC vs Full GC](#24-minor-gc-vs-major-gc-vs-full-gc)
   - 2.5 [Collectors](#25-collectors)
   - 2.6 [Reference types](#26-reference-types)
   - 2.7 [Memory leaks](#27-memory-leaks)
   - 2.8 [GC tuning and logs](#28-gc-tuning-and-logs)
3. [Java Memory Model and Concurrency](#3-java-memory-model-and-concurrency)
   - 3.1 [Threads and thread lifecycle](#31-threads-and-thread-lifecycle)
   - 3.2 [Race conditions](#32-race-conditions)
   - 3.3 [synchronized](#33-synchronized)
   - 3.4 [volatile](#34-volatile)
   - 3.5 [Java Memory Model and happens-before](#35-java-memory-model-and-happens-before)
   - 3.6 [Locks](#36-locks)
   - 3.7 [Atomics and CAS](#37-atomics-and-cas)
   - 3.8 [Thread pools](#38-thread-pools)
   - 3.9 [CompletableFuture](#39-completablefuture)
   - 3.10 [Concurrent utilities](#310-concurrent-utilities)
   - 3.11 [ConcurrentHashMap internals](#311-concurrenthashmap-internals)
   - 3.12 [Deadlock, livelock and starvation](#312-deadlock-livelock-and-starvation)
   - 3.13 [ThreadLocal](#313-threadlocal)
   - 3.14 [Virtual threads](#314-virtual-threads)
7. [Generics](#7-generics)
   - 7.1 [Generic class and method](#71-generic-class-and-method)
   - 7.2 [Bounded types](#72-bounded-types)
   - 7.3 [Wildcards and PECS](#73-wildcards-and-pecs)
   - 7.4 [Type erasure](#74-type-erasure)
   - 7.5 [Limitations](#75-limitations)
9. [Java 8+ Features](#9-java-8-features)
   - 9.1 [Lambdas](#91-lambdas)
   - 9.2 [Functional interfaces](#92-functional-interfaces)
   - 9.3 [Method references](#93-method-references)
   - 9.4 [Streams](#94-streams)
   - 9.5 [Optional](#95-optional)
   - 9.6 [Default and static methods in interfaces](#96-default-and-static-methods-in-interfaces)
   - 9.7 [java.time](#97-javatime)
   - 9.8 [Other additions](#98-other-additions)
10. [Modern Java (9 to 25)](#10-modern-java-9-to-25)
   - 10.1 [Version overview](#101-version-overview)
   - 10.2 [var](#102-var)
   - 10.3 [Immutable collection factories](#103-immutable-collection-factories)
   - 10.4 [String methods and text blocks](#104-string-methods-and-text-blocks)
   - 10.5 [Records](#105-records)
   - 10.6 [Switch expressions](#106-switch-expressions)
   - 10.7 [Pattern matching for instanceof](#107-pattern-matching-for-instanceof)
   - 10.8 [Sealed classes](#108-sealed-classes)
   - 10.9 [Pattern matching for switch and record patterns](#109-pattern-matching-for-switch-and-record-patterns)
   - 10.10 [Sequenced collections](#1010-sequenced-collections)
   - 10.11 [Other useful additions](#1011-other-useful-additions)

---

## 1. JVM Internals

### 1.1 javac

`javac` is the Java compiler. It converts `.java` source code into `.class` files containing bytecode.

- It does not produce machine code. It produces bytecode.
- It catches syntax and type errors before the program runs.

```
javac Hello.java    // creates Hello.class
java Hello          // the JVM loads and runs it
```

### 1.2 Bytecode

Bytecode is the instruction set that the JVM understands, stored in `.class` files.

- It is the same on every OS, so a `.class` file runs on any machine that has a JVM.
- The JVM runs it using a stack: instructions push values onto it and pop values off it.
- `javap -c ClassName` shows the bytecode.

```java
static int add(int a, int b) {
    return a + b;
}
```

```
iload_0     // push a
iload_1     // push b
iadd        // pop both, add them, push the result
ireturn     // return the result
```

### 1.3 Class loading

Class loading is how the JVM brings a `.class` file into memory so it can be used.

- A class is loaded the first time it is needed, not at startup.
- A class is loaded only once.

The steps:

```
1. LOAD        find the .class file, read the bytes into memory
2. LINK
     verify    check the bytecode is valid and safe
     prepare   allocate static fields, set defaults (0, null, false)
     resolve   turn symbolic references into real references
3. INITIALIZE  run static initializers and static blocks
```

```java
public class Main {
    public static void main(String[] args) {
        System.out.println("main started");
        new Dog();
        new Dog();
    }
}

class Dog {
    static { System.out.println("Dog loaded"); }
    Dog() { System.out.println("Dog created"); }
}
```

```
main started
Dog loaded      <- loaded at the first new Dog(), once only
Dog created
Dog created
```

Prepare vs initialize:
- Prepare sets the default value.
- Initialize runs your code.

```java
static int timeout = 30;
// after prepare:    timeout = 0
// after initialize: timeout = 30
```

Static fields and `static { }` blocks run in the order they appear in the file.

### 1.4 Verification

Verification is the step where the JVM checks that bytecode is valid and safe before running it.

- It checks things like correct types and valid jumps.
- Bad bytecode is rejected with a `VerifyError`.
- It is why a broken or tampered `.class` file cannot crash the JVM.

### 1.5 JIT compiler

The JIT (Just-In-Time) compiler converts frequently used bytecode into native machine code while the program runs.

- The JVM first interprets bytecode, which is slow.
- Methods that run many times are called hot, and the JIT compiles them to machine code, which is fast.
- This is why a Java program is slower at the start (warm-up) and faster later.
- There are two compilers, C1 (quick, light optimization) and C2 (slower, heavy optimization).

```
java -XX:+PrintCompilation Hot     // prints methods as they get compiled
```

### 1.6 Class loaders and parent delegation

A class loader finds a class by name and loads it into the JVM.

There are three built-in loaders:

| Loader | Loads |
|--------|-------|
| Bootstrap | Core JDK classes (`java.lang.String`, `java.util.*`) |
| Platform | Other JDK modules (`java.sql`, etc.) |
| Application | Your own classes |

Parent Delegation Model: A class-loading mechanism where a ClassLoader delegates the request to its parent ClassLoader before attempting to load the class itself.
- When: During the class-loading phase, when a class needs to be loaded.
- Why: To prioritize trusted Java classes, prevent applications from replacing core Java classes, and maintain consistent class loading.

Remember: Parent first, child if the parent cannot load it.

Example: if you create your own `java.lang.String`, it is never loaded, because Bootstrap (the top parent) returns the real one first.

```java
System.out.println(String.class.getClassLoader());     // null (Bootstrap)
System.out.println(Main.class.getClassLoader());       // Application loader
```

### 1.7 Runtime memory areas

These are the areas of memory the JVM uses while a program runs.

| Area | Shared or per thread |
|------|----------------------|
| Heap | Shared |
| Metaspace | Shared |
| Stack | Per thread |
| PC register | Per thread |
| Native method stack | Per thread |

#### 1.7.1 Heap

The Java Heap is a runtime memory area allocated from the memory available to the JVM. It is where objects and arrays are allocated. It is shared among all application threads and managed by the Garbage Collector, which reclaims memory from objects that are no longer reachable.

- Heap stores objects and arrays.
- Heap is shared among threads.
- Garbage Collector manages Heap memory.

```java
Dog d = new Dog();    // the Dog object is created on the heap
```

- Too much memory use causes `OutOfMemoryError: Java heap space`.
- Size is set with `-Xms` (starting size) and `-Xmx` (maximum size).

#### 1.7.2 Stack

The Stack is the memory area where each thread keeps its method calls and local variables.

- Each thread has its own stack.
- Every method call adds a frame to the stack, and the frame is removed when the method returns.
- A frame holds the method's local variables, such as primitives and references.
- A reference variable is on the stack, but the object it points to is on the heap.
- Too many nested calls cause `StackOverflowError`.

```java
void run() {
    int count = 5;            // stack
    Dog d = new Dog();        // d (the reference) on stack, Dog object on heap
}

static void recurse() { recurse(); }    // StackOverflowError
```

#### 1.7.3 Metaspace

Metaspace is the memory area that stores information about loaded classes.

- It holds class details such as method code, field names and static fields' metadata.
- It is shared among all threads.
- It uses native memory, not the heap. Before Java 8 this area was called PermGen.
- Loading too many classes causes `OutOfMemoryError: Metaspace`.

#### 1.7.4 PC register

The PC (program counter) register stores which instruction a thread is currently running.

- Each thread has its own.
- It lets the JVM continue from the right place when it switches between threads.

#### 1.7.5 Native method stack

The native method stack is the memory area used when Java code calls native code written in C or C++.

- Each thread has its own.
- It is used for native methods (JNI).

### 1.8 Object layout in memory

Object layout is how an object is stored in the heap.

An object has three parts:

```
| header | fields | padding |
```

- Header: extra information the JVM keeps about the object, about 12 bytes. It includes a pointer to the object's class, the hash code, lock info and GC info.
- Fields: the actual data, such as your `int x` and `String name`.
- Padding: extra bytes added so the total size is a multiple of 8.

```java
class Point {
    int x;    // 4 bytes
    int y;    // 4 bytes
}
// 12 (header) + 8 (fields) = 20, padded to 24 bytes
```

- Each object has this overhead, so many small objects use a lot of memory.
- An `Integer` holds 4 bytes of data but takes about 16 bytes.
- The JOL tool can print the real layout of a class.

### 1.9 Escape analysis

Escape analysis is a JIT optimization that checks whether an object is used only inside the method that creates it.

- If the object never leaves the method, it does not escape.
- A non-escaping object does not need to be created on the heap. The JIT can use just its fields as plain local variables.
- This means less heap use and less garbage collection work.
- It only happens in JIT-compiled (hot) code.

```java
static int distance() {
    Point p = new Point(3, 4);       // only used here, does not escape
    return p.x * p.x + p.y * p.y;
}

static Point create() {
    return new Point(3, 4);          // returned, so it escapes: created on the heap
}
```

---

## 2. Garbage Collection

### 2.1 What is garbage collection

Garbage Collection (GC) is the JVM's automatic process of freeing heap memory used by objects that are no longer needed.

- You create objects with `new`, but you never free them yourself.
- The Garbage Collector finds objects that nothing uses anymore and reclaims their memory.
- While it runs, it may pause your application for a short time. This pause is called a stop-the-world pause.

```java
Dog d = new Dog();    // object created on the heap
d = null;             // nothing points to the Dog now, so it can be collected
```

- `System.gc()` is only a request. The JVM may ignore it, so do not rely on it.

### 2.2 GC roots and reachability

An object is garbage when it is unreachable, meaning no chain of references leads to it from a GC root.

GC roots are the starting points the GC checks:
- Local variables on thread stacks
- Static fields
- Active threads
- References held by native code (JNI)

How it works:
- The GC starts from the roots and follows every reference. This is called marking.
- Every object it reaches is alive.
- Every object it does not reach is garbage and gets removed.

```java
Dog a = new Dog();
Dog b = a;         // two references to the same Dog
a = null;          // still reachable through b, not garbage
b = null;          // now unreachable, can be collected
```

Objects that only point to each other (a cycle) are still collected if nothing reachable points to them.

### 2.3 Generational heap

Most objects die young, so the heap is split by object age and the young part is cleaned often.

```
Heap
├── Young generation
│     ├── Eden          new objects start here
│     ├── Survivor 0    objects that survived a GC
│     └── Survivor 1
└── Old generation      long-lived objects
```

How an object moves:
- A new object is created in Eden.
- When Eden fills up, a GC runs and the live objects are copied to a Survivor space.
- Each time an object survives a GC, its age goes up.
- When the age reaches a limit, the object is promoted to the Old generation.

Why this design:
- Cleaning the small Young generation is fast.
- The Old generation is bigger and cleaned less often.

### 2.4 Minor GC vs Major GC vs Full GC

| Type | Cleans | Speed |
|------|--------|-------|
| Minor GC | Young generation | Fast, frequent |
| Major GC | Old generation | Slower, less frequent |
| Full GC | Whole heap (and metaspace) | Slowest, longest pause |

- A Minor GC runs when Eden is full.
- A Full GC is usually a sign of trouble, such as the Old generation filling up.
- Many Full GCs in a row often point to a memory leak or a heap that is too small.

### 2.5 Collectors

A collector is the algorithm the JVM uses to do garbage collection. You pick one with a JVM flag.

| Collector | Flag | Notes |
|-----------|------|-------|
| Serial | `-XX:+UseSerialGC` | Single thread. For small apps. |
| Parallel | `-XX:+UseParallelGC` | Many threads. Focus on total throughput. |
| G1 | `-XX:+UseG1GC` | Default since Java 9. Splits heap into regions. Aims for short, predictable pauses. |
| ZGC | `-XX:+UseZGC` | Very short pauses, even for huge heaps. |
| Shenandoah | `-XX:+UseShenandoahGC` | Short pauses, runs most work alongside the app. |

How to choose:
- Default (G1) is fine for most applications.
- Need the lowest pauses: ZGC or Shenandoah.
- Batch jobs where only total speed matters: Parallel.

Basic steps most collectors follow:
- Mark: find live objects.
- Sweep: remove dead objects.
- Compact: move live objects together so free memory is in one block.

### 2.6 Reference types

A reference type controls how strongly a reference keeps an object alive.

| Type | Collected when | Used for |
|------|----------------|----------|
| Strong | Never, while reachable | Normal references |
| Soft | JVM is low on memory | Caches |
| Weak | Next GC | `WeakHashMap` |
| Phantom | After it is finalized | Cleanup tasks |

```java
Dog strong = new Dog();                                   // normal
WeakReference<Dog> weak = new WeakReference<>(new Dog());
weak.get();    // the Dog, or null if the GC has collected it
```

- Normal code uses only strong references. The other types are for caches and cleanup.

### 2.7 Memory leaks

A memory leak in Java is when objects are no longer needed but are still referenced, so the GC cannot remove them.

Common causes:
- Static collections that keep growing (a static `List` or `Map` that is never cleared)
- Unclosed resources (streams, connections)
- `ThreadLocal` values never removed in thread pools
- Listeners and callbacks never unregistered
- Mutable objects used as `HashMap` keys and then changed
- Inner (non-static) classes holding a reference to the outer class

```java
class Cache {
    static List<byte[]> data = new ArrayList<>();    // never cleared, so it grows forever
    static void add() { data.add(new byte[1024 * 1024]); }
}
```

Symptoms:
- Memory keeps rising and does not drop after GC
- Frequent Full GCs
- Eventually `OutOfMemoryError`

How to find one:
- Take a heap dump and open it in a tool such as Eclipse MAT or VisualVM.
- Look for the objects taking the most memory and see what is holding them.

### 2.8 GC tuning and logs

Tuning means setting JVM flags so GC behaves well for your application. Start with the defaults and tune only when there is a measured problem.

Common flags:

| Flag | Meaning |
|------|---------|
| `-Xms` / `-Xmx` | Initial / maximum heap size |
| `-XX:+UseG1GC` | Choose the collector |
| `-XX:MaxGCPauseMillis=200` | Target maximum pause time (G1) |
| `-Xlog:gc` | Print GC logs |
| `-XX:+HeapDumpOnOutOfMemoryError` | Save a heap dump when memory runs out |

```
java -Xmx512m -Xlog:gc -XX:+HeapDumpOnOutOfMemoryError MyApp
```

What to look at in GC logs:
- How often GCs happen
- How long each pause is
- How much memory is freed after each GC
- Whether Full GCs are happening

---

## 3. Java Memory Model and Concurrency

### 3.1 Threads and thread lifecycle

A thread is an independent path of execution inside a program. All threads of a program share the same heap.

```java
Thread t = new Thread(() -> System.out.println("running"));
t.start();    // starts a new thread
```

- `start()` creates a new thread and runs the code in it.
- `run()` called directly just runs the code in the current thread. No new thread is created.
- `Runnable` returns nothing. `Callable` returns a value and can throw checked exceptions.

Thread states:

| State | Meaning |
|-------|---------|
| NEW | Created, not started |
| RUNNABLE | Running or ready to run |
| BLOCKED | Waiting to get a `synchronized` lock |
| WAITING | Waiting for another thread, no time limit (`wait()`, `join()`) |
| TIMED_WAITING | Waiting with a time limit (`sleep(ms)`) |
| TERMINATED | Finished |

### 3.2 Race conditions

A race condition is a bug where the result depends on the timing of threads.

```java
class Counter {
    int count = 0;
    void increment() { count++; }
}
```

- `count++` looks like one step, but it is three: read the value, add 1, write it back.
- Two threads can both read `5` and both write `6`, so one increment is lost.
- The fix is to make the operation safe with `synchronized`, a lock or an atomic class.

### 3.3 synchronized

`synchronized` allows only one thread at a time to run a block of code that uses the same lock.

```java
class Counter {
    int count = 0;

    synchronized void increment() { count++; }    // lock is "this"

    void add() {
        synchronized (this) { count++; }          // same thing, as a block
    }
}
```

- Every object has a lock (monitor).
- On an instance method the lock is `this`. On a static method the lock is the `Class` object.
- It gives two things: only one thread inside at a time (mutual exclusion) and changes made inside are visible to the next thread that takes the lock (visibility).
- It is reentrant: a thread that holds a lock can enter another `synchronized` block on the same lock.
- Keep synchronized blocks small. Long blocks slow everything down.

### 3.4 volatile

`volatile` makes sure every thread sees the latest value of a variable.

```java
volatile boolean running = true;

void stop() { running = false; }                 // called from another thread

void work() { while (running) { /* ... */ } }    // sees the change
```

- Without `volatile`, a thread may keep using an old cached copy of the variable.
- It guarantees visibility and prevents instruction reordering around the variable.
- It does not make operations atomic.

```java
volatile int count = 0;
count++;    // still a race condition: read, add, write are three steps
```

- Use `volatile` for simple flags. Use `synchronized` or atomics for `count++`.

### 3.5 Java Memory Model and happens-before

The Java Memory Model (JMM) defines when a change made by one thread is guaranteed to be visible to another thread.

Why it is needed:
- Each CPU core has its own cache, and the compiler and CPU may reorder instructions.
- So one thread's write may not be seen by another thread right away.

Happens-before: if action A happens-before action B, then B is guaranteed to see everything A did.

The main happens-before rules:
- Unlocking a monitor happens-before the next lock of the same monitor.
- A write to a `volatile` variable happens-before every later read of it.
- `Thread.start()` happens-before everything in the started thread.
- Everything in a thread happens-before another thread returns from `join()` on it.

If no happens-before relationship exists between two threads, there is no guarantee about what one sees from the other.

### 3.6 Locks

The `java.util.concurrent.locks` package gives more control than `synchronized`.

```java
ReentrantLock lock = new ReentrantLock();

lock.lock();
try {
    // critical section
} finally {
    lock.unlock();    // always unlock in finally
}
```

`ReentrantLock` can do things `synchronized` cannot:
- `tryLock()` tries to get the lock without waiting forever
- `lockInterruptibly()` can be interrupted while waiting
- Fair mode: the longest-waiting thread goes first
- Multiple `Condition` objects (like several `wait/notify` queues)

Other locks:
- `ReadWriteLock`: many readers at the same time, or one writer alone. Good when reads are much more common than writes.
- `StampedLock`: like a read/write lock, with an optimistic read mode that avoids locking when no writer interferes.

### 3.7 Atomics and CAS

Atomic classes (`AtomicInteger`, `AtomicLong`, `AtomicReference`) do thread-safe operations without locks.

```java
AtomicInteger count = new AtomicInteger(0);
count.incrementAndGet();    // safe from many threads
```

How it works, called CAS (Compare-And-Swap):
- The CPU checks that the variable still holds the expected value.
- If yes, it sets the new value in one step.
- If not, another thread changed it, so the operation retries.

- No locks means no blocking and no deadlock.
- Under heavy contention many retries happen. `LongAdder` is faster for counters with many threads.

### 3.8 Thread pools

A thread pool is a group of reusable threads that run submitted tasks. Creating a thread for every task is expensive.

```java
ExecutorService pool = Executors.newFixedThreadPool(4);

Future<Integer> f = pool.submit(() -> 42);    // Callable
int result = f.get();                         // waits for the result

pool.shutdown();                              // always shut down
```

Common pools:

| Method | Behavior |
|--------|----------|
| `newFixedThreadPool(n)` | n threads, unbounded queue |
| `newCachedThreadPool()` | Creates threads as needed, reuses idle ones |
| `newSingleThreadExecutor()` | One thread, tasks run in order |
| `newScheduledThreadPool(n)` | Delayed or repeated tasks |
| `newVirtualThreadPerTaskExecutor()` | One virtual thread per task (Java 21) |

`ThreadPoolExecutor` settings:
- Core pool size: threads always kept
- Maximum pool size: upper limit
- Queue: where tasks wait
- Rejection policy: what to do when the pool and queue are full

How a new task is handled:
1. Fewer threads than core size: start a new thread.
2. Otherwise: put the task in the queue.
3. Queue full: start extra threads up to the maximum.
4. Still full: apply the rejection policy.

- In production, create `ThreadPoolExecutor` directly with a bounded queue. An unbounded queue can use up all memory.
- `execute()` takes a `Runnable` and returns nothing. `submit()` returns a `Future`, and exceptions show up on `get()`.

### 3.9 CompletableFuture

`CompletableFuture` runs tasks asynchronously and lets you chain steps without blocking.

```java
CompletableFuture<String> result =
    CompletableFuture.supplyAsync(() -> getUser())       // runs in another thread
        .thenApply(user -> user.getName())               // transform the result
        .exceptionally(e -> "unknown");                  // handle errors

String name = result.get();
```

Common methods:

| Method | Use |
|--------|-----|
| `supplyAsync` / `runAsync` | Start an async task |
| `thenApply` | Transform the result |
| `thenCompose` | Chain another async call that returns a `CompletableFuture` |
| `thenCombine` | Combine two independent futures |
| `allOf` / `anyOf` | Wait for all / any of several futures |
| `exceptionally` / `handle` | Handle errors |

- By default tasks run on the common `ForkJoinPool`. For blocking work, pass your own executor as an extra argument.

### 3.10 Concurrent utilities

| Utility | Use |
|---------|-----|
| `CountDownLatch` | Wait until N events happen. One-time use. |
| `CyclicBarrier` | N threads wait for each other at a point. Reusable. |
| `Semaphore` | Limit how many threads can use a resource at once. |
| `BlockingQueue` | Threads hand work to each other (producer-consumer). |

```java
CountDownLatch latch = new CountDownLatch(3);
// each of 3 worker threads calls latch.countDown() when done
latch.await();    // main thread waits until the count reaches 0
```

```java
BlockingQueue<String> queue = new ArrayBlockingQueue<>(10);
queue.put("job");           // producer: waits if the queue is full
String job = queue.take();  // consumer: waits if the queue is empty
```

### 3.11 ConcurrentHashMap internals

`ConcurrentHashMap` is a thread-safe `HashMap` that allows many threads to work at the same time.

How it works (Java 8 and later):
- It uses an array of buckets like `HashMap`.
- Adding to an empty bucket uses CAS, with no lock.
- Adding to a bucket that already has entries locks only that bucket (`synchronized` on its first node).
- Reads do not lock.
- So different threads working on different buckets do not block each other.

Compare:
- `Hashtable` and `Collections.synchronizedMap` lock the whole map for every operation.
- Before Java 8 it used segment locks (16 segments).

Rules:
- Null keys and null values are not allowed. A `null` from `get()` would be unclear: missing key or null value?
- Use its atomic methods for check-then-act logic:

```java
map.putIfAbsent("a", 1);
map.computeIfAbsent("a", k -> load(k));
map.merge("a", 1, Integer::sum);    // counting
```

```java
// not safe even on a ConcurrentHashMap: two separate steps
if (!map.containsKey("a")) {
    map.put("a", 1);
}
```

- Iterators never throw `ConcurrentModificationException`. They may or may not show changes made during iteration.

### 3.12 Deadlock, livelock and starvation

Deadlock: two or more threads wait for each other forever.

```java
// Thread 1                       // Thread 2
synchronized (A) {                synchronized (B) {
    synchronized (B) { }              synchronized (A) { }
}                                 }
```

- Thread 1 holds A and waits for B. Thread 2 holds B and waits for A. Neither can continue.

Deadlock needs all four of these:
1. Mutual exclusion (a lock is held by one thread)
2. Hold and wait (a thread holds one lock while waiting for another)
3. No preemption (locks cannot be taken away)
4. Circular wait (a cycle of threads waiting on each other)

How to prevent it:
- Always take locks in the same order.
- Use `tryLock()` with a timeout.
- Hold locks for as short a time as possible.
- To find one, take a thread dump (`jstack <pid>`). It reports detected deadlocks.

Livelock: threads keep running and reacting to each other, but make no progress (like two people stepping aside in a hallway at the same time).

Starvation: a thread never gets the resource it needs because other threads always get it first. Fair locks can help.

### 3.13 ThreadLocal

`ThreadLocal` gives each thread its own copy of a variable.

```java
ThreadLocal<SimpleDateFormat> format =
    ThreadLocal.withInitial(() -> new SimpleDateFormat("yyyy-MM-dd"));

format.get();    // each thread gets its own SimpleDateFormat
```

- Common uses: per-request data such as user ID or transaction context.
- Thread pools reuse threads, so a value left behind can leak into the next task or never be freed.
- Always clean up:

```java
try {
    context.set(value);
    // work
} finally {
    context.remove();
}
```

### 3.14 Virtual threads

Virtual threads (Java 21) are very lightweight threads managed by the JVM instead of the operating system.

```java
Thread.startVirtualThread(() -> System.out.println("hi"));

try (var executor = Executors.newVirtualThreadPerTaskExecutor()) {
    executor.submit(() -> callSlowService());
}
```

- A normal (platform) thread maps to one OS thread, which is expensive, so you can have only thousands.
- Virtual threads are cheap, so you can have millions.
- When a virtual thread blocks (waiting on I/O), the JVM frees the OS thread to run another virtual thread.
- Good for: many tasks that mostly wait, such as web requests and database calls.
- Not good for: heavy CPU work, because there is no gain over normal threads.
- Do not pool them. Create one per task.
- Long `synchronized` blocks around blocking calls can pin the virtual thread to its OS thread. Prefer `ReentrantLock` there (this was largely fixed in Java 24).

---

## 7. Generics

Generics let a class or method work with different types while the compiler checks the types for you.

### 7.1 Generic class and method

```java
class Box<T> {
    private T value;
    Box(T value) { this.value = value; }
    T get() { return value; }
}

Box<String> b = new Box<>("hi");
String s = b.get();                  // no cast needed
// Box<String> bad = new Box<>(5);   // compile error
```

```java
static <T> T firstOf(List<T> list) { return list.get(0); }

String s = firstOf(List.of("a", "b"));
int n = firstOf(List.of(1, 2));
```

```java
class Pair<K, V> {
    K key; V value;
    Pair(K key, V value) { this.key = key; this.value = value; }
}
var p = new Pair<>("age", 30);       // Pair<String, Integer>
```

### 7.2 Bounded types

`extends` limits what types are allowed.

```java
static <T extends Number> double sum(List<T> list) {
    double total = 0;
    for (T n : list) total += n.doubleValue();
    return total;
}

static <T extends Comparable<T>> T max(T a, T b) {
    return a.compareTo(b) >= 0 ? a : b;
}

max(3, 7);          // 7
max("a", "b");      // "b"
// max(new Object(), new Object());   // compile error: not Comparable
```

```java
// multiple bounds: class first, then interfaces
static <T extends Number & Comparable<T>> T largest(List<T> list) { /* ... */ }
```

### 7.3 Wildcards and PECS

`?` means "some unknown type". Use it when you only read from or only write to a collection.

```java
List<Integer> ints = new ArrayList<>();
// List<Number> nums = ints;    // compile error: List<Integer> is NOT a List<Number>
```

```java
// ? extends: you can READ (as Number), you cannot add
static double sumAll(List<? extends Number> nums) {
    double total = 0;
    for (Number n : nums) total += n.doubleValue();
    // nums.add(1);             // compile error
    return total;
}
sumAll(List.of(1, 2, 3));       // List<Integer> works
sumAll(List.of(1.5, 2.5));      // List<Double> works
```

```java
// ? super: you can WRITE Integer, you read only as Object
static void fill(List<? super Integer> sink) {
    sink.add(1);
    sink.add(2);
    Object o = sink.get(0);
}
fill(new ArrayList<Integer>());
fill(new ArrayList<Number>());
fill(new ArrayList<Object>());
```

```java
// ? alone: unknown type, read as Object
static void printAll(List<?> list) {
    for (Object o : list) System.out.println(o);
}
```

PECS: Producer Extends, Consumer Super.

```java
// src produces T (extends), dest consumes T (super)
static <T> void copy(List<? super T> dest, List<? extends T> src) {
    for (T item : src) dest.add(item);
}
```

### 7.4 Type erasure

Generic types exist only at compile time. The compiler removes them and adds casts.

```java
List<String> a = new ArrayList<>();
List<Integer> b = new ArrayList<>();
System.out.println(a.getClass() == b.getClass());   // true, both are ArrayList
```

```java
// what you write
List<String> list = new ArrayList<>();
list.add("x");
String s = list.get(0);

// what the JVM sees
List list = new ArrayList();
list.add("x");
String s = (String) list.get(0);
```

`T` is replaced by its bound, or by `Object` if there is none.

### 7.5 Limitations

```java
class Demo<T> {
    // T x = new T();                  // not allowed: T is unknown at runtime
    // T[] arr = new T[10];            // not allowed
    // static T shared;                // not allowed
    // boolean b = obj instanceof T;   // not allowed
}

// if (obj instanceof List<String>) {}     // not allowed
if (obj instanceof List<?>) {}             // allowed

// List<int> bad;                          // primitives not allowed
List<Integer> ok;

// both have the same signature after erasure:
// void f(List<String> l) {}
// void f(List<Integer> l) {}              // compile error
```

Workaround for `new T()`: pass a factory.

```java
static <T> T create(Supplier<T> factory) { return factory.get(); }

List<String> list = create(ArrayList::new);
```

---

## 9. Java 8+ Features

### 9.1 Lambdas

A lambda is a short anonymous function, used wherever a functional interface is expected.

Shapes of a lambda:

```java
(a, b) -> a + b                  // two params
x -> x * 2                       // one param, no parentheses needed
() -> System.out.println("hi")   // no params
```

Expression body and block body:

```java
// expression body: the value is returned automatically
Function<String, Integer> lengthShort = s -> s.length();

// block body: use braces and return
Function<String, Integer> lengthBlock = s -> {
    int length = s.length();
    return length;
};
```

Runnable:

```java
Runnable r = () -> {
    System.out.println("run");
};

r.run();
```

Comparator:

```java
Comparator<String> byLength = (a, b) -> {
    return a.length() - b.length();
};

List<String> names = new ArrayList<>(List.of("Charlie", "Al", "Bob"));

names.sort(byLength);
// [Al, Bob, Charlie]

// same thing, short form
names.sort((a, b) -> a.length() - b.length());
```

Lambdas can only use local variables that are effectively final.

```java
int base = 10;

Function<Integer, Integer> addBase = x -> {
    return x + base;
};

// base++;   // compile error: base is no longer effectively final
```

Inside a lambda, `this` is the enclosing class. Inside an anonymous class, `this` is the anonymous object.

### 9.2 Functional interfaces

A functional interface has exactly one abstract method. Lambdas and method references implement it.

| Interface | Takes | Returns | Method | Use it for |
|-----------|-------|---------|--------|------------|
| `Predicate<T>` | one value of any type `T` | `boolean` (true or false) | `test(T)` | checking a condition, filtering |
| `Function<T,R>` | one value of type `T` | a value of type `R` (can be a different type from `T`) | `apply(T)` | converting or transforming a value |
| `Consumer<T>` | one value of type `T` | nothing (`void`) | `accept(T)` | doing something with a value: print, save, send |
| `Supplier<T>` | nothing | a value of type `T` | `get()` | creating or providing a value |
| `BiFunction<T,U,R>` | two values, of types `T` and `U` | a value of type `R` | `apply(T,U)` | converting two values into one result |
| `BiConsumer<T,U>` | two values, of types `T` and `U` | nothing (`void`) | `accept(T,U)` | doing something with two values |
| `BiPredicate<T,U>` | two values, of types `T` and `U` | `boolean` (true or false) | `test(T,U)` | checking a condition on two values |
| `UnaryOperator<T>` | one value of type `T` | a value of the same type `T` | `apply(T)` | changing a value without changing its type |
| `BinaryOperator<T>` | two values, both of type `T` | a value of the same type `T` | `apply(T,T)` | combining two values of one type into one |

In short:
- Predicate answers yes or no.
- Function converts.
- Consumer uses a value and returns nothing.
- Supplier gives a value and takes nothing.
- The `Bi` versions take two inputs.
- The Operators are Functions where every type is the same.

Predicate:

```java
Predicate<String> isEmpty = s -> {
    return s.isEmpty();
};

boolean a = isEmpty.test("");
// true

Predicate<String> notEmpty = isEmpty.negate();

Predicate<String> isLong = s -> {
    return s.length() > 5;
};

boolean b = isEmpty
    .or(isLong)
    .test("abcdefg");
// true

boolean c = notEmpty
    .and(isLong)
    .test("abc");
// false
```

Function:

```java
Function<String, Integer> length = s -> {
    return s.length();
};

int n = length.apply("abc");
// 3

Function<Integer, Integer> doubleIt = x -> x * 2;
Function<Integer, Integer> plusOne = x -> x + 1;

int r1 = doubleIt
    .andThen(plusOne)
    .apply(5);
// 11  (double first, then +1)

int r2 = doubleIt
    .compose(plusOne)
    .apply(5);
// 12  (+1 first, then double)

String same = Function.<String>identity().apply("same");
// "same"
```

Consumer:

```java
Consumer<String> print = s -> {
    System.out.println(s);
};

print.accept("hi");

Consumer<String> printLength = s -> {
    System.out.println(s.length());
};

print
    .andThen(printLength)
    .accept("hello");
// hello
// 5
```

Supplier:

```java
Supplier<List<String>> newList = () -> {
    return new ArrayList<>();
};

List<String> list = newList.get();

Supplier<Double> random = () -> Math.random();
```

BiFunction, BiConsumer, BiPredicate:

```java
BiFunction<Integer, Integer, Integer> add = (a, b) -> {
    return a + b;
};

int sum = add.apply(2, 3);
// 5

BiConsumer<String, Integer> show = (key, value) -> {
    System.out.println(key + "=" + value);
};

show.accept("age", 30);
// age=30

BiPredicate<String, Integer> hasLength = (s, n) -> {
    return s.length() == n;
};

boolean ok = hasLength.test("abc", 3);
// true
```

UnaryOperator and BinaryOperator:

```java
UnaryOperator<String> upper = s -> {
    return s.toUpperCase();
};

String u = upper.apply("hi");
// "HI"

BinaryOperator<Integer> max = (a, b) -> {
    return a > b ? a : b;
};

int m = max.apply(4, 9);
// 9
```

Primitive versions avoid boxing: `IntPredicate`, `IntFunction`, `ToIntFunction`, `IntUnaryOperator`, `IntBinaryOperator`, `IntSupplier`, and the same for `Long` and `Double`.

```java
IntPredicate even = n -> {
    return n % 2 == 0;
};

boolean isEven = even.test(4);
// true

ToIntFunction<String> length = s -> {
    return s.length();
};

int len = length.applyAsInt("abc");
// 3

IntBinaryOperator multiply = (a, b) -> {
    return a * b;
};

int product = multiply.applyAsInt(3, 4);
// 12
```

Existing interfaces that are also functional: `Runnable`, `Callable`, `Comparator`.

Your own functional interface:

```java
@FunctionalInterface
interface Validator<T> {
    boolean validate(T value);
}

Validator<String> notBlank = s -> {
    return !s.isBlank();
};

boolean valid = notBlank.validate("hi");
// true
```

### 9.3 Method references

A method reference is a shorter lambda that just calls an existing method. The syntax is `Class::method`.

| Kind | Syntax | Same as lambda |
|------|--------|----------------|
| Static method | `Integer::parseInt` | `s -> Integer.parseInt(s)` |
| Method of a specific object | `System.out::println` | `x -> System.out.println(x)` |
| Method of any object of a type | `String::length` | `s -> s.length()` |
| Constructor | `ArrayList::new` | `() -> new ArrayList<>()` |

1. Static method:

```java
Function<String, Integer> parse = Integer::parseInt;

int n = parse.apply("42");
// 42
```

2. Method of a specific object (bound):

```java
String prefix = "Hi, ";

Function<String, String> greet = prefix::concat;

String result = greet.apply("Ana");
// "Hi, Ana"

Consumer<String> print = System.out::println;
```

3. Method of any object of a type (unbound). The first argument becomes the object:

```java
Function<String, Integer> length = String::length;

BiFunction<String, String, Boolean> same = String::equalsIgnoreCase;

boolean equal = same.apply("Java", "java");
// true
```

4. Constructor:

```java
Supplier<ArrayList<String>> newList = ArrayList::new;

Function<String, StringBuilder> newBuilder = StringBuilder::new;

Function<Integer, int[]> newArray = int[]::new;
```

In streams:

```java
List<String> names = List.of("ana", "ben", "cara");

List<String> upper = names.stream()
    .map(String::toUpperCase)
    .toList();
// [ANA, BEN, CARA]

names.forEach(System.out::println);

List<Integer> numbers = Stream.of("1", "2", "3")
    .map(Integer::parseInt)
    .toList();
// [1, 2, 3]

List<StringBuilder> builders = names.stream()
    .map(StringBuilder::new)
    .toList();
```

### 9.4 Streams

A stream is a pipeline that processes a collection step by step without changing the original.

The pipeline is: source, then zero or more intermediate operations, then one terminal operation.

```java
// Sample data used below (record needs Java 16+)
record Employee(String name, String dept, double salary, int age) {}

List<Employee> emps = List.of(
    new Employee("Ana",  "IT",    90000, 30),
    new Employee("Ben",  "IT",    70000, 25),
    new Employee("Cara", "HR",    60000, 40),
    new Employee("Dan",  "HR",    65000, 35),
    new Employee("Eve",  "Sales", 50000, 28)
);

List<Integer> nums = List.of(5, 3, 8, 1, 9, 3, 2);
```

#### Creating streams

```java
list.stream();

Arrays.stream(new int[]{1, 2, 3});

Stream.of("a", "b", "c");

IntStream.range(0, 5);          // 0,1,2,3,4
IntStream.rangeClosed(1, 5);    // 1,2,3,4,5

Stream.iterate(1, x -> x * 2)
    .limit(5);
// 1,2,4,8,16

Stream.generate(() -> "x")
    .limit(3);
// x,x,x

"hello".chars();                // IntStream of characters

Files.lines(Path.of("data.txt"));   // lines of a file (close it)
```

#### Intermediate operations (lazy, return a stream)

filter: keep matching elements.

```java
List<Integer> result = nums.stream()
    .filter(n -> n > 2)
    .toList();
// [5, 3, 8, 9, 3]
```

map: transform each element.

```java
List<Integer> times10 = nums.stream()
    .map(n -> n * 10)
    .toList();
// [50, 30, 80, 10, 90, 30, 20]

List<String> names = emps.stream()
    .map(Employee::name)
    .toList();
// [Ana, Ben, Cara, Dan, Eve]
```

flatMap: flatten nested collections into one stream.

```java
List<List<Integer>> nested = List.of(
    List.of(1, 2),
    List.of(3),
    List.of(4, 5)
);

List<Integer> flat = nested.stream()
    .flatMap(List::stream)
    .toList();
// [1, 2, 3, 4, 5]

List<String> words = Stream.of("hello world", "java stream")
    .flatMap(s -> Arrays.stream(s.split(" ")))
    .toList();
// [hello, world, java, stream]
```

distinct: remove duplicates.

```java
List<Integer> unique = nums.stream()
    .distinct()
    .toList();
// [5, 3, 8, 1, 9, 2]
```

sorted:

```java
List<Integer> asc = nums.stream()
    .sorted()
    .toList();
// [1, 2, 3, 3, 5, 8, 9]

List<Integer> desc = nums.stream()
    .sorted(Comparator.reverseOrder())
    .toList();
// [9, 8, 5, 3, 3, 2, 1]

List<String> bySalary = emps.stream()
    .sorted(Comparator.comparing(Employee::salary).reversed())
    .map(Employee::name)
    .toList();
// [Ana, Ben, Dan, Cara, Eve]

List<String> byDeptThenName = emps.stream()
    .sorted(Comparator.comparing(Employee::dept)
        .thenComparing(Employee::name))
    .map(Employee::name)
    .toList();
// [Cara, Dan, Ana, Ben, Eve]
```

limit and skip:

```java
List<Integer> page = nums.stream()
    .skip(2)
    .limit(3)
    .toList();
// [8, 1, 9]
```

takeWhile and dropWhile (Java 9):

```java
List<Integer> taken = Stream.of(1, 2, 3, 10, 4)
    .takeWhile(n -> n < 5)
    .toList();
// [1, 2, 3]

List<Integer> dropped = Stream.of(1, 2, 3, 10, 4)
    .dropWhile(n -> n < 5)
    .toList();
// [10, 4]
```

peek: look at elements while debugging.

```java
long count = nums.stream()
    .peek(n -> System.out.println("saw " + n))
    .count();
```

mapToInt and boxed: switch between object streams and primitive streams.

```java
int totalAge = emps.stream()
    .mapToInt(Employee::age)
    .sum();
// 158

List<Integer> boxed = IntStream.range(0, 3)
    .boxed()
    .toList();
// [0, 1, 2]
```

#### Terminal operations (start the pipeline)

```java
long count = nums.stream().count();
// 7

nums.stream()
    .forEach(System.out::println);

List<Integer> list = nums.stream()
    .toList();                      // immutable list (Java 16)

Object[] objects = nums.stream()
    .toArray();

int[] ints = nums.stream()
    .mapToInt(Integer::intValue)
    .toArray();
```

reduce: combine all elements into one.

```java
int sum = nums.stream()
    .reduce(0, Integer::sum);
// 31

Optional<Integer> biggest = nums.stream()
    .reduce(Integer::max);
// Optional[9]
```

min and max:

```java
Optional<Integer> max = nums.stream()
    .max(Integer::compare);
// Optional[9]

String lowestPaid = emps.stream()
    .min(Comparator.comparingDouble(Employee::salary))
    .get()
    .name();
// Eve
```

Matching:

```java
boolean any = nums.stream()
    .anyMatch(n -> n > 8);
// true

boolean all = nums.stream()
    .allMatch(n -> n > 0);
// true

boolean none = nums.stream()
    .noneMatch(n -> n > 100);
// true
```

Finding:

```java
Optional<Integer> first = nums.stream()
    .filter(n -> n > 5)
    .findFirst();
// Optional[8]

Optional<Integer> any = nums.stream()
    .findAny();                     // any element (useful in parallel)
```

#### Primitive streams

```java
int sum = IntStream.rangeClosed(1, 5).sum();
// 15

OptionalDouble average = IntStream.rangeClosed(1, 5).average();
// OptionalDouble[3.0]

OptionalInt max = IntStream.of(4, 9, 2).max();
// OptionalInt[9]

IntSummaryStatistics stats = nums.stream()
    .mapToInt(n -> n)
    .summaryStatistics();

stats.getMin();       // 1
stats.getMax();       // 9
stats.getAverage();   // 4.428...
stats.getSum();       // 31
```

#### Collectors

toSet:

```java
Set<String> depts = emps.stream()
    .map(Employee::dept)
    .collect(Collectors.toSet());
// [IT, HR, Sales]   (a Set has no fixed order, so the real order can differ)
```

toMap:

```java
Map<String, Double> salaryByName = emps.stream()
    .collect(Collectors.toMap(
        Employee::name,
        Employee::salary
    ));
```

toMap with a merge function when keys repeat:

```java
Map<String, Double> totalByDept = emps.stream()
    .collect(Collectors.toMap(
        Employee::dept,
        Employee::salary,
        Double::sum
    ));
// {IT=160000.0, HR=125000.0, Sales=50000.0}
```

joining:

```java
String joined = emps.stream()
    .map(Employee::name)
    .collect(Collectors.joining(", ", "[", "]"));
// [Ana, Ben, Cara, Dan, Eve]
```

groupingBy:

```java
Map<String, List<Employee>> byDept = emps.stream()
    .collect(Collectors.groupingBy(Employee::dept));
```

groupingBy + counting:

```java
Map<String, Long> countByDept = emps.stream()
    .collect(Collectors.groupingBy(
        Employee::dept,
        Collectors.counting()
    ));
// {IT=2, HR=2, Sales=1}
```

groupingBy + averaging:

```java
Map<String, Double> averageByDept = emps.stream()
    .collect(Collectors.groupingBy(
        Employee::dept,
        Collectors.averagingDouble(Employee::salary)
    ));
// {IT=80000.0, HR=62500.0, Sales=50000.0}
```

groupingBy + mapping: group, then transform each group.

```java
Map<String, List<String>> namesByDept = emps.stream()
    .collect(Collectors.groupingBy(
        Employee::dept,
        Collectors.mapping(Employee::name, Collectors.toList())
    ));
// {IT=[Ana, Ben], HR=[Cara, Dan], Sales=[Eve]}
```

groupingBy + maxBy: highest paid per department.

```java
Map<String, Optional<Employee>> topByDept = emps.stream()
    .collect(Collectors.groupingBy(
        Employee::dept,
        Collectors.maxBy(Comparator.comparingDouble(Employee::salary))
    ));
```

groupingBy into a sorted map:

```java
Map<String, List<Employee>> sortedByDept = emps.stream()
    .collect(Collectors.groupingBy(
        Employee::dept,
        TreeMap::new,
        Collectors.toList()
    ));
```

partitioningBy: split into true and false.

```java
Map<Boolean, List<Employee>> split = emps.stream()
    .collect(Collectors.partitioningBy(e -> e.salary() > 60000));

split.get(true);     // Ana, Ben, Dan
split.get(false);    // Cara, Eve
```

summarizing:

```java
DoubleSummaryStatistics salaryStats = emps.stream()
    .collect(Collectors.summarizingDouble(Employee::salary));

salaryStats.getAverage();     // 67000.0
```

collectingAndThen:

```java
List<String> fixed = emps.stream()
    .map(Employee::name)
    .collect(Collectors.collectingAndThen(
        Collectors.toList(),
        Collections::unmodifiableList
    ));
```

The order of a `HashMap` result can differ from what is shown above.

#### Streams are lazy

Nothing runs until a terminal operation is called, and each element moves through the whole pipeline one at a time.

```java
Stream.of("a", "b", "c")
    .filter(s -> {
        System.out.println("filter " + s);
        return true;
    })
    .map(s -> {
        System.out.println("map " + s);
        return s.toUpperCase();
    });
// prints nothing: there is no terminal operation
```

```java
Stream.of("a", "b", "c")
    .filter(s -> {
        System.out.println("filter " + s);
        return true;
    })
    .map(s -> {
        System.out.println("map " + s);
        return s.toUpperCase();
    })
    .findFirst();
// filter a
// map a          <- stops after the first element: findFirst short-circuits
```

#### A stream can be used only once

```java
Stream<String> s = Stream.of("a", "b");

s.count();
s.count();     // IllegalStateException: stream has already been operated upon or closed
```

#### Parallel streams

```java
long sum = LongStream.range(0, 1_000_000)
    .parallel()
    .sum();
// 499999500000  (an int sum would overflow)

List<Integer> doubled = nums.parallelStream()
    .map(n -> n * 2)
    .toList();
```

- They split the work across the common `ForkJoinPool`.
- Use them only for large, CPU-heavy work.
- Avoid them for small lists, I/O or code that changes shared state.

```java
// wrong: shared mutable state in a parallel stream
List<Integer> out = new ArrayList<>();

nums.parallelStream()
    .forEach(out::add);     // not thread-safe
```

#### Common interview problems

Word frequency:

```java
String text = "to be or not to be";

Map<String, Long> freq = Arrays.stream(text.split(" "))
    .collect(Collectors.groupingBy(
        Function.identity(),
        Collectors.counting()
    ));
// {to=2, be=2, or=1, not=1}   (a HashMap has no fixed order, so the real order can differ)
```

Sort a map by value, highest first:

```java
List<Map.Entry<String, Long>> sorted = freq.entrySet().stream()
    .sorted(Map.Entry.<String, Long>comparingByValue().reversed())
    .toList();
```

Second highest salary:

```java
Optional<Double> second = emps.stream()
    .map(Employee::salary)
    .distinct()
    .sorted(Comparator.reverseOrder())
    .skip(1)
    .findFirst();
// Optional[70000.0]
```

Highest paid employee per department:

```java
Map<String, Employee> top = emps.stream()
    .collect(Collectors.toMap(
        Employee::dept,
        e -> e,
        BinaryOperator.maxBy(Comparator.comparingDouble(Employee::salary))
    ));
```

First non-repeating character:

```java
String word = "swiss";

Optional<Character> first = word.chars()
    .mapToObj(c -> (char) c)
    .collect(Collectors.groupingBy(
        Function.identity(),
        LinkedHashMap::new,
        Collectors.counting()
    ))
    .entrySet().stream()
    .filter(e -> e.getValue() == 1)
    .map(Map.Entry::getKey)
    .findFirst();
// Optional[w]
```

Sum of squares of even numbers:

```java
int sum = nums.stream()
    .filter(n -> n % 2 == 0)
    .mapToInt(n -> n * n)
    .sum();
// 68
```

Palindrome check:

```java
String p = "level";

boolean isPalindrome = IntStream.range(0, p.length() / 2)
    .allMatch(i -> p.charAt(i) == p.charAt(p.length() - 1 - i));
// true
```

Names of employees older than 28, uppercase, sorted:

```java
List<String> result = emps.stream()
    .filter(e -> e.age() > 28)
    .map(Employee::name)
    .map(String::toUpperCase)
    .sorted()
    .toList();
// [ANA, CARA, DAN]
```

### 9.5 Optional

`Optional<T>` is a container that holds a value or is empty. It is meant for return types, so callers deal with "no result" explicitly instead of a `null`.

```java
Optional<String> a = Optional.of("x");               // NullPointerException if the value is null
Optional<String> b = Optional.ofNullable(maybeNull); // empty if null
Optional<String> c = Optional.empty();
```

```java
Optional<String> name = Optional.of("Ana");

name.isPresent();                                    // true
name.isEmpty();                                      // false (Java 11)

name.ifPresent(n -> System.out.println(n));
name.ifPresentOrElse(                                // Java 9
    n -> System.out.println("Hi " + n),
    () -> System.out.println("no name"));
```

```java
// getting the value
Optional<String> empty = Optional.empty();

empty.orElse("default");                             // "default"
empty.orElseGet(() -> loadDefault());                // computed only when empty
empty.orElseThrow(() -> new IllegalStateException("missing"));
empty.orElseThrow();                                 // NoSuchElementException (Java 10)
// empty.get();                                      // NoSuchElementException: avoid
```

```java
// map, flatMap, filter
Optional<String> upper = name.map(String::toUpperCase);             // Optional[ANA]
Optional<String> longName = name.filter(n -> n.length() > 5);       // Optional.empty

// flatMap: when the function itself returns an Optional
Optional<Address> addr = user.flatMap(User::getAddress);

// a safe chain instead of nested null checks
String city = user.flatMap(User::getAddress)
                  .map(Address::getCity)
                  .orElse("Unknown");

name.or(() -> Optional.of("fallback"));              // Java 9
name.stream();                                       // Java 9: 0 or 1 element stream
```

`orElse` vs `orElseGet`:

```java
opt.orElse(expensiveCall());              // expensiveCall() ALWAYS runs
opt.orElseGet(() -> expensiveCall());     // runs only if opt is empty
```

Rules:
- Use it as a return type. Do not use it for fields, method parameters or collections.
- Do not call `get()` without checking first. Prefer `orElse`, `orElseGet` or `orElseThrow`.
- For primitives use `OptionalInt`, `OptionalLong`, `OptionalDouble`.

### 9.6 Default and static methods in interfaces

Interfaces can have methods with a body, so new methods can be added without breaking existing implementations.

```java
interface Greeter {
    String name();

    default String greet() { return "Hello, " + name(); }     // inherited by implementers

    static Greeter of(String n) { return () -> n; }           // called as Greeter.of("Ana")

    private String helper() { return "x"; }                   // Java 9, only for use inside the interface
}

Greeter g = Greeter.of("Ana");
g.greet();                                                    // "Hello, Ana"
```

If two interfaces have the same default method, the class must choose:

```java
interface A { default void hello() { System.out.println("A"); } }
interface B { default void hello() { System.out.println("B"); } }

class C implements A, B {
    public void hello() { A.super.hello(); }                  // pick one explicitly
}
```

### 9.7 java.time

The new date and time API. It is immutable and thread-safe, unlike `Date` and `SimpleDateFormat`.

```java
LocalDate today = LocalDate.now();
LocalDate d = LocalDate.of(2026, 11, 6);
d.plusDays(10);                                      // 2026-11-16
d.minusMonths(1);                                    // 2026-10-06
d.getDayOfWeek();                                    // FRIDAY
d.isBefore(today);

LocalTime t = LocalTime.of(14, 30);
LocalDateTime dt = LocalDateTime.of(2026, 11, 6, 14, 30);
ZonedDateTime dhaka = ZonedDateTime.now(ZoneId.of("Asia/Dhaka"));
Instant now = Instant.now();                         // a point on the UTC timeline
```

```java
// differences
ChronoUnit.DAYS.between(LocalDate.of(2026, 1, 1), LocalDate.of(2026, 3, 1));   // 59
Period.between(LocalDate.of(2025, 1, 15), LocalDate.of(2026, 3, 20));          // P1Y2M5D
Duration.between(LocalTime.of(9, 0), LocalTime.of(17, 30)).toMinutes();        // 510
```

```java
// formatting and parsing
DateTimeFormatter f = DateTimeFormatter.ofPattern("dd-MM-yyyy");
d.format(f);                                         // "06-11-2026"
LocalDate.parse("06-11-2026", f);
```

### 9.8 Other additions

New methods on collections and maps:

```java
List<String> list = new ArrayList<>(List.of("a", "bb", "ccc"));

list.forEach(System.out::println);
list.removeIf(s -> s.length() > 2);                  // [a, bb]
list.replaceAll(String::toUpperCase);                // [A, BB]
list.sort(Comparator.naturalOrder());
```

```java
Map<String, Integer> map = new HashMap<>();

map.getOrDefault("k", 0);                            // 0
map.putIfAbsent("k", 1);

map.merge("apple", 1, Integer::sum);                 // counting: add 1, or sum with the existing value
map.merge("apple", 1, Integer::sum);                 // apple = 2

Map<String, List<String>> groups = new HashMap<>();
groups.computeIfAbsent("fruits", k -> new ArrayList<>()).add("apple");   // create the list if missing

map.forEach((k, v) -> System.out.println(k + "=" + v));
map.entrySet().removeIf(e -> e.getValue() < 2);
```

Comparators:

```java
people.sort(Comparator.comparing(Person::lastName)
                      .thenComparing(Person::firstName)
                      .thenComparingInt(Person::age)
                      .reversed());

list.sort(Comparator.nullsFirst(Comparator.naturalOrder()));
```

Joining strings:

```java
String.join(", ", List.of("a", "b", "c"));           // "a, b, c"

StringJoiner sj = new StringJoiner(", ", "[", "]");
sj.add("a").add("b");                                // [a, b]
```

`CompletableFuture` is covered in section 3.9.

---

## 10. Modern Java (9 to 25)

### 10.1 Version overview

| Version | Main additions |
|---------|----------------|
| 9 | `List.of` / `Map.of`, private interface methods, `Optional.or`, modules |
| 10 | `var` |
| 11 (LTS) | `String` methods (`isBlank`, `strip`, `lines`, `repeat`), `HttpClient`, run a single file with `java Hello.java` |
| 12 | `Collectors.teeing` |
| 14 | Switch expressions, helpful `NullPointerException` messages |
| 15 | Text blocks |
| 16 | Records, pattern matching for `instanceof`, `Stream.toList()` |
| 17 (LTS) | Sealed classes |
| 21 (LTS) | Virtual threads, pattern matching for `switch`, record patterns, sequenced collections |
| 22 | Unnamed variables `_` |
| 24 | Stream gatherers |
| 25 (LTS) | Compact source files and instance `main`, flexible constructor bodies, scoped values |

Check feature status against the JDK you use. Some features were previews in earlier versions.

### 10.2 var

`var` lets the compiler work out the type of a local variable. The variable is still statically typed.

```java
var list = new ArrayList<String>();      // ArrayList<String>
var count = 10;                          // int
var name = "Ana";                        // String

for (var entry : map.entrySet()) { }

// var x;                                // error: cannot infer the type
// var y = null;                         // error
// var f = () -> 1;                      // error
// only for local variables: not for fields, parameters or return types
```

### 10.3 Immutable collection factories

```java
List<Integer> list = List.of(1, 2, 3);
Set<String> set = Set.of("a", "b");
Map<String, Integer> map = Map.of("a", 1, "b", 2);
Map<String, Integer> bigMap = Map.ofEntries(Map.entry("a", 1), Map.entry("b", 2));

// list.add(4);                          // UnsupportedOperationException
// List.of(1, null);                     // NullPointerException: nulls not allowed
// Set.of("a", "a");                     // IllegalArgumentException: duplicates not allowed

List<Integer> copy = List.copyOf(existingList);          // immutable copy
```

### 10.4 String methods and text blocks

```java
"  ".isBlank();                          // true (Java 11)
"  hi  ".strip();                        // "hi"
"  hi  ".stripLeading();                 // "hi  "
"ab".repeat(3);                          // "ababab"
"a\nb\nc".lines().count();               // 3
"Hello %s, you are %d".formatted("Ana", 30);    // Java 15
```

```java
String json = """
    {
      "name": "Ana",
      "age": 30
    }
    """;
// indentation is trimmed based on the closing quotes; no \n escapes needed
```

### 10.5 Records

A record is a short way to write an immutable data class.

```java
record Point(int x, int y) {}

var p = new Point(3, 4);
p.x();                                   // 3 (accessor, no "get" prefix)
p.toString();                            // Point[x=3, y=4]
p.equals(new Point(3, 4));               // true
```

The compiler generates the constructor, accessors, `equals`, `hashCode` and `toString`.

```java
record Range(int low, int high) {
    Range {                                          // compact constructor for validation
        if (low > high) throw new IllegalArgumentException("low > high");
    }

    int length() { return high - low; }              // extra methods are allowed
    static Range of(int a, int b) { return new Range(a, b); }
}
```

- Fields are final and there are no setters.
- A record cannot extend a class, but it can implement interfaces.

### 10.6 Switch expressions

`switch` can return a value, uses `->`, and has no fall-through.

```java
String type = switch (day) {
    case SATURDAY, SUNDAY -> "weekend";
    default -> "weekday";
};

int length = switch (name) {
    case "Ana" -> 3;
    case "Cara" -> 4;
    default -> {
        System.out.println("unknown name");
        yield name.length();                         // yield returns a value from a block
    }
};
```

The old form needed `break` and was easy to get wrong:

```java
switch (day) {
    case SATURDAY:
    case SUNDAY:
        type = "weekend";
        break;
    default:
        type = "weekday";
}
```

### 10.7 Pattern matching for instanceof

```java
// before
if (obj instanceof String) {
    String s = (String) obj;
    System.out.println(s.length());
}

// now: test and cast in one step
if (obj instanceof String s) {
    System.out.println(s.length());
}

if (obj instanceof String s && s.length() > 3) {
    System.out.println(s.toUpperCase());
}

if (!(obj instanceof String s)) return;
System.out.println(s.length());                      // s is in scope here
```

### 10.8 Sealed classes

A sealed class or interface lists exactly which classes may extend it.

```java
sealed interface Shape permits Circle, Rectangle, Square {}

record Circle(double radius) implements Shape {}
record Rectangle(double width, double height) implements Shape {}
record Square(double side) implements Shape {}

// class Triangle implements Shape {}                // compile error: not in the permits list
```

Each permitted subclass must be `final`, `sealed` or `non-sealed`. Records are implicitly final.

### 10.9 Pattern matching for switch and record patterns

With a sealed type the compiler knows all cases, so `default` is not needed.

```java
double area(Shape shape) {
    return switch (shape) {
        case Circle c -> Math.PI * c.radius() * c.radius();
        case Rectangle r -> r.width() * r.height();
        case Square s -> s.side() * s.side();
    };                                               // if a new Shape is added, this stops compiling
}
```

```java
// record patterns take the record apart directly
double area2(Shape shape) {
    return switch (shape) {
        case Circle(double r) -> Math.PI * r * r;
        case Rectangle(double w, double h) -> w * h;
        case Square(double side) -> side * side;
    };
}

if (obj instanceof Rectangle(double w, double h)) {
    System.out.println(w * h);
}
```

```java
// guards with "when", and null handling
static String describe(Object o) {
    return switch (o) {
        case null -> "null";
        case Integer i when i > 10 -> "big int";
        case Integer i -> "int";
        case String s -> "string of length " + s.length();
        default -> "something else";
    };
}
```

Put a guarded case before the same type without a guard.

### 10.10 Sequenced collections

New methods for collections that have a defined order: first, last and reversed.

```java
List<Integer> list = new ArrayList<>(List.of(1, 2, 3));

list.getFirst();                                     // 1
list.getLast();                                      // 3
list.addFirst(0);                                    // [0, 1, 2, 3]
list.removeLast();                                   // [0, 1, 2]
list.reversed();                                     // [2, 1, 0] (a view)

LinkedHashSet<String> set = new LinkedHashSet<>(List.of("a", "b", "c"));
set.getFirst();                                      // "a"

LinkedHashMap<String, Integer> map = new LinkedHashMap<>();
map.put("a", 1);
map.put("b", 2);
map.firstEntry();                                    // a=1
map.lastEntry();                                     // b=2
```

Before Java 21, getting the last element of a list was `list.get(list.size() - 1)`.

### 10.11 Other useful additions

```java
// Stream.toList() (16): short form of collect(Collectors.toList()), returns an immutable list
List<String> names = emps.stream().map(Employee::name).toList();

// Collectors.teeing (12): run two collectors on the same stream, then combine the results
var average = Stream.of(1, 2, 3, 4).collect(Collectors.teeing(
    Collectors.counting(),
    Collectors.summingInt(x -> x),
    (count, sum) -> sum * 1.0 / count));             // 2.5
```

```java
// Helpful NullPointerException messages (14)
String s = null;
s.length();
// Cannot invoke "String.length()" because "s" is null
```

```java
// Unnamed variables (22): use _ when a variable is required but not used
for (var _ : list) count++;
try { risky(); } catch (Exception _) { }
String kind = switch (shape) { case Circle _ -> "circle"; default -> "other"; };
```

```java
// HttpClient (11)
HttpClient client = HttpClient.newHttpClient();
HttpRequest request = HttpRequest.newBuilder(URI.create("https://example.com")).build();
HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
response.statusCode();
```

```
// Run a single source file without javac (11)
java Hello.java
```

```java
// Stream gatherers (24)
Stream.of(1, 2, 3, 4, 5).gather(Gatherers.windowFixed(2)).toList();   // [[1, 2], [3, 4], [5]]
```

```java
// Java 25: compact source file with instance main, no class or static needed
void main() {
    System.out.println("Hello");
}
```

```java
// Java 25: flexible constructor bodies, code allowed before super()
class Child extends Parent {
    Child(int x) {
        if (x < 0) throw new IllegalArgumentException("x < 0");
        super(x);
    }
}
```

Virtual threads (21) are covered in section 3.14.
