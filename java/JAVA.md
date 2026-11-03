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
