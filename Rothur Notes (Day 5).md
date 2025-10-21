---
aliases: []
tags:
created: 2025-10-20
modified: 2025-10-20
status:
  - inactive
type:
  - instant_note
---

## Motivations / Purposes
- [[Job Seeking Thread]]

## Thread Safety
Multithread major concerns:
- Hardware resource limitations
- Thread safety

### **Definition**

> **Thread safety** means that a piece of code or an object behaves **correctly when accessed by multiple threads simultaneously**, _without producing race conditions, inconsistent states, or corrupted data_.

In other words:

> A class is **thread-safe** if multiple threads can use it _at the same time_ and its internal state remains valid.

---

## ⚙️ **Why It Matters**

When multiple threads run in parallel:
- They **share the same memory (heap)**.
- They may **read and write** the same variables or objects.

If these accesses are not properly synchronized, they can **interleave** in unpredictable ways, leading to **race conditions**, **visibility issues**, or **data corruption**.

---

### **Key Concepts Behind Thread Safety**

| Concept             | Description                                                                                                    | Example                                                                    |
| ------------------- | -------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------- |
| **Race Condition**  | Two or more threads access shared data and try to change it at the same time → final result depends on timing. | Two threads both do `counter++` — only one increment is actually recorded. |
| **Atomicity**       | An operation completes _entirely or not at all_.                                                               | `x++` is **not** atomic (it’s read + increment + write).                   |
| **Visibility**      | Changes made by one thread must be visible to others.                                                          | Without `volatile` or locks, another thread may see an **old value**.      |
| **Synchronization** | Mechanism to coordinate access (locks, monitors).                                                              | `synchronized`, `ReentrantLock`, `ConcurrentHashMap`, etc.                 |

---
## **Ways to Achieve Thread Safety**

| Mechanism                                            | How It Helps                                                 | Example                                               |
| ---------------------------------------------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| **Synchronization (`synchronized`)**                 | Ensures only one thread enters a critical section at a time. | `synchronized void update() { ... }`                  |
| **Locks (`ReentrantLock`)**                          | Fine-grained control over locking/unlocking.                 | `lock.lock(); try { ... } finally { lock.unlock(); }` |
| **Atomic Variables (`java.util.concurrent.atomic`)** | Hardware-level atomic operations (CAS).                      | `AtomicInteger.incrementAndGet()`                     |
| **Immutable Objects**                                | If state never changes, multiple threads can safely read it. | `String`, `LocalDate`, custom immutable DTOs.         |
| **Thread confinement**                               | Each thread uses its own data (no sharing).                  | Local variables, `ThreadLocal<T>`.                    |
| **Concurrent Collections**                           | Thread-safe versions of collections.                         | `ConcurrentHashMap`, `CopyOnWriteArrayList`.          |

---

## 🧩 **Examples**

### ❌ Not Thread-Safe

```java
class Counter {
    private int count = 0;
    public void increment() {
        count++; // not atomic!
    }
    public int get() { return count; }
}
```

Two threads incrementing can interleave:

```
T1: read count (0)
T2: read count (0)
T1: write 1
T2: write 1   → lost update
```

---

### ✅ Thread-Safe with Synchronization

```java
class SafeCounter {
    private int count = 0;
    public synchronized void increment() {
        count++;
    }
    public synchronized int get() { return count; }
}
```

Only one thread at a time can enter the method — no race condition.

---

### ✅ Thread-Safe with Atomic

```java
class AtomicCounter {
    private final AtomicInteger count = new AtomicInteger(0);
    public void increment() {
        count.incrementAndGet();
    }
    public int get() { return count.get(); }
}
```

Non-blocking, lock-free, still thread-safe.

---

### ✅ Thread-Safe by Design (Immutable)

```java
record Point(int x, int y) {} // Java 16+
```

Once created, can’t change → inherently thread-safe.

---

## **Thread Safety ≠ Performance**

- Thread safety ensures **correctness**, not necessarily **speed**.
- Over-synchronization can reduce performance by forcing threads to wait unnecessarily.
- Modern Java concurrency (`ConcurrentHashMap`, `StampedLock`, etc.) helps balance both.

---

## Locks
### Pessimistic Lock v.s. Optimistic Lock

| Feature         | **Pessimistic Concurrency Control**                                   | **Optimistic Concurrency Control**                                        |
| --------------- | --------------------------------------------------------------------- | ------------------------------------------------------------------------- |
| **Idea**        | Prevent conflicts _before_ they occur by locking resources.           | Allow concurrent access; detect and resolve conflicts _after_ they occur. |
| **Mechanism**   | Uses **locks** (`synchronized`, `ReentrantLock`, database row locks). | Uses **version checks**, **timestamps**, or **CAS (Compare-And-Swap)**.   |
| **Performance** | Good for **high contention** (many writes).                           | Better for **low contention** (mostly reads).                             |
| **Overhead**    | Lock management, thread blocking.                                     | Occasional retries on conflict.                                           |
| **Risk**        | Threads block → potential deadlocks.                                  | Threads retry → wasted work, but no blocking.                             |

### Different locks in Java

| **Feature**                 | **`synchronized`**           | **`ReentrantLock`**                               | **`ReentrantReadWriteLock`**   | **`StampedLock` (Java 8+)**                  |
| --------------------------- | ---------------------------- | ------------------------------------------------- | ------------------------------ | -------------------------------------------- |
| **Type**                    | Built-in (intrinsic monitor) | Explicit mutual-exclusion lock                    | Separate read & write locks    | Optimistic + read/write locks                |
| **Reentrant**               | ✅ Yes                        | ✅ Yes                                             | ✅ Yes                          | ❌ No                                         |
| **Fairness Option**         | ❌ No                         | ✅ Yes (constructor flag)                          | ✅ Yes (constructor flag)       | ❌ No                                         |
| **Lock / Unlock Control**   | Automatic (on block/method)  | Manual (`lock()` / `unlock()`)                    | Manual                         | Manual (`unlock(stamp)`)                     |
| **Interruptible / Timeout** | ❌ No                         | ✅ Yes (`tryLock()`, `lockInterruptibly()`)        | ✅ Yes                          | ✅ Yes                                        |
| **Performance**             | Simple, stable               | Better under high contention                      | Great for read-heavy workloads | Best for mostly-read workloads               |
| **Read / Write Separation** | ❌ No                         | ❌ No                                              | ✅ Yes                          | ✅ Yes (plus optimistic read)                 |
| **Use Case**                | Simple thread safety         | Fine control, advanced coordination               | Many readers, few writers      | Many readers, rare writes (high performance) |
| **Code Example**            | `synchronized(obj){...}`     | `lock.lock(); try {...} finally {lock.unlock();}` | `readLock.lock(); ...`         | `long s = lock.tryOptimisticRead(); ...`     |

## Optimistic Lock
### **What is Compare and Swap (CAS)?**

> **CAS** is a low-level atomic operation that checks whether a variable still holds an expected value,  
> and **only if it does**, updates it to a new value — all in one indivisible CPU instruction.
### **Signature**

Conceptually:

`boolean compareAndSwap(expectedValue, newValue)`

It performs this atomically:

```java
if (memoryValue == expectedValue) {
    memoryValue = newValue;     
    return true; 
} 
else {return false;}
```

If another thread changed the value in between, CAS fails — the update is **not applied**, and you can **retry**.

```java
class CASCounter {
    private final AtomicInteger count = new AtomicInteger(0);

    void increment() {
        int oldValue, newValue;
        do {
            oldValue = count.get();         // read current value
            newValue = oldValue + 1;        // compute new value
        } while (!count.compareAndSet(oldValue, newValue)); // retry if failed
    }

    int get() { return count.get(); }
}

```

### The ABA Problem

With CAS (Compare-And-Set), you only check that a memory location still holds value **A** before swapping it to **C**. If another thread changed it **A → B → A** in between, your CAS still succeeds—yet the world _did_ change. In lock-free structures (stacks/queues/free-lists), that can corrupt invariants or “lose” nodes.

GC note (Java): GC prevents the classic “pointer reused after free” bug, but ABA still matters as a **logical state** change (e.g., a node temporarily removed and reinserted).

# Minimal demo (shows the issue)

```java
import java.util.concurrent.atomic.AtomicInteger;

public class ABA {
  static AtomicInteger x = new AtomicInteger(100);

  public static void main(String[] args) throws Exception {
    Thread t1 = new Thread(() -> {
      int v = x.get();                // sees 100 (A)
      sleep(200);                     // let t2 do A->B->A
      boolean ok = x.compareAndSet(v, 200); // succeeds (still A)
      System.out.println("t1 CAS ok? " + ok + ", x=" + x.get());
    });

    Thread t2 = new Thread(() -> {
      x.compareAndSet(100, 123);      // A -> B
      x.compareAndSet(123, 100);      // B -> A
      System.out.println("t2 done, x=" + x.get());
    });

    t1.start(); t2.start(); t1.join(); t2.join();
  }

  static void sleep(long ms){ try { Thread.sleep(ms); } catch(Exception ignored){} }
}
```

`t1` “thinks” nothing changed because it only compared the value.

### 1) Pair the value with a version (stamp)

Use `AtomicStampedReference<T>`: CAS succeeds _only if both_ the reference and its stamp match.

Key points:
- Increment stamp on **every** successful structural change.
- Readers must fetch both `(ref, stamp)` atomically via `get(stampHolder)`.

### 2) Mark a bit for logical deletion

If you only need a boolean “tombstone” alongside a reference, use `AtomicMarkableReference<T>` (common for lock-free lists to avoid helping the wrong node).

```java
import java.util.concurrent.atomic.AtomicMarkableReference;
AtomicMarkableReference<Node> next = new AtomicMarkableReference<>(null, false);
// CAS on both reference and mark
```

### 3) Bundle fields and CAS them together

Create an immutable holder `(value, version)` and CAS the **holder** via `AtomicReference` or `VarHandle.compareAndSet`:

```java
record State(int value, int version) {}
AtomicReference<State> state = new AtomicReference<>(new State(100, 0));

boolean update(int expectedVal, int newVal) {
  while (true) {
    State s = state.get();
    if (s.value() != expectedVal) return false;
    State n = new State(newVal, s.version() + 1);
    if (state.compareAndSet(s, n)) return true;
  }
}
```

### 4) Use well-tested JDK structures

`ConcurrentLinkedQueue`, `ConcurrentHashMap`, `Atomic*` classes, `StampedLock`-based APIs, etc., already handle these edge cases internally. Prefer them over hand-rolled lock-free algorithms unless you truly need custom behavior.

### When can you ignore ABA?

- Pure counters with `AtomicLong/Integer` where **only the numeric value matters** (no hidden state transitions).
- Code not doing lock-free structural changes (simple synchronized blocks or `ReentrantLock` avoid ABA by design).

### Quick checklist (Java)

- Lock-free + shared pointer/state? → **Use stamp/version or mark.**
- Removing/inserting nodes? → Consider `AtomicStampedReference` or an existing JDK concurrent collection.
- Need a flag + ref? → `AtomicMarkableReference`.
- Avoid mutating shared nodes in place; prefer **immutable nodes** and CAS the container link.


### Monitoring and Observability

**Monitoring** is the process of collecting, analyzing, and using predefined data points (metrics) to track the **known health and performance** of your system. It is a reactive practice that tells you **when** something is wrong.

### Key Steps for Monitoring:

- **Define Key Metrics:** Establish what's important to track. A common set is the **Golden Signals**:
    - **Latency:** Time taken to service a request (e.g., average response time).
    - **Traffic:** How much demand is being placed on the system (e.g., requests per second).
    - **Errors:** The rate of requests that fail (e.g., HTTP 5xx responses).
    - **Saturation:** How "full" your service is (e.g., CPU utilization, memory usage).
- **Instrument and Collect Data:** Use agents or built-in libraries (**telemetry**) to collect data from your infrastructure (servers, network) and applications.
- **Visualize:** Use **dashboards** to display key metrics in real-time, providing a high-level view of system health.
- **Alert:** Set **alerts** on predefined thresholds. For example, an alert is triggered if **error rate > 5%** or **CPU usage > 80%**. This is your notification that something is wrong.

---

## 2. Observability: The "Why" and "How"

**Observability** is a property of a system that allows you to infer its **internal state** (even for new or unknown failure modes) by analyzing the data it produces. It's a proactive, investigative practice that helps you answer **why** something went wrong and **how** to fix it.

### The Three Pillars of Observability

Implementing observability requires collecting and correlating three types of telemetry data:

1. Metrics (What is happening)
    - **Definition:** Quantitative measurements collected over time. They are ideal for dashboards, alerting, and trend analysis.
    - **Example:** CPU utilization, number of requests, memory consumption.
    - **Role in Observability:** Provide the high-level signal that an issue exists (e.g., a "Service A" dashboard shows a spike in error rate).
2. Logs (Where and when it happened)
    - **Definition:** Discrete, timestamped text records of events that occurred within a system or application.
    - **Example:** An application log recording an error, "User failed to authenticate at 2025-10-20T19:40:00Z."
    - **Role in Observability:** Provide granular context and detail for specific events that help pinpoint the exact location and time of a problem. Use **structured logging** (e.g., JSON format) for easy searching and aggregation.
3. Traces (How a request moved through the system)
    - **Definition:** The record of a single request's complete journey as it moves through all the services, databases, and components in a **distributed system** (like microservices). Each step in the journey is a **span**.
    - **Example:** A user clicks a button, which calls Service A, which calls Service B, which queries the database. The trace captures the time spent in each step.
    - **Role in Observability:** Essential for diagnosing performance bottlenecks and failures in complex architectures by identifying which service or step in the call chain is the root cause.
        

### Best Practices for Observability:

- **Instrumentation with Open Standards:** Use vendor-neutral tools like **OpenTelemetry (OTel)** to instrument your code for all three pillars. This prevents vendor lock-in and standardizes data formats.
- **Correlation is Key:** Use an observability platform that can link logs, metrics, and traces together. When you see an error metric spike, you should be able to jump directly to the relevant logs and traces for that time period.
- **Shift Left:** Integrate observability practices into your development lifecycle, not just production. Generate traces and metrics during local development and testing to catch issues early.
- **Monitor Business Outcomes:** Track metrics that tie directly to your business goals, such as "Conversion Rate" or "Revenue per Minute," not just technical health, to understand the real-world impact of system issues.

## In Class Code
```java
package com.example.rothurdemo;  
  
/**  
 * multiple threads -> running jvm * * major concerns? * hardware resources limitations * thread safety -> concurrency issue -> read/write issue -> one thread reading *                                                           while the other thread writing *                                                           on the same resource * access the same resource -> not an issue if ???? -> * * * *                          Memory Area (RAM) * * | | | | | | | | | | |     | | | | | | | | | | |     | | | | | | | | | | | * |  Method Area      |     |  VM Stack         |     | Native Method     | * |                   |     |  r1 e1            |     |    stack          | * |  source code      |     |  | | | | | | | |  |     |                   | * |  of MyRunnableTask|     |  r2 e1            |     |                   | * |   Employee        |     |                   |     |                   | * | | | | | | | | | | |     | | | | | | | | | | |     | | | | | | | | | | | * * | | | | | | | | | | |      | | | | | | | | | | | * |  Heap             |      |  Program Counter  | * |  MyRunnableTask() |      |     Register      | * |  Employee("David")|      |                   | * |                   |      |    PC1            | * |                   |      |                   | * | | | | | | | | | | |      | | | | | | | | | | | *                    RunTime Data Memory Area * * * Lock -> Pessimistic vs Optimistic * * synchronized -> scope(where???): method, block * essential -> Reentrantlock (Pessimisitc lock) * * // thread safety -> (great things) -> trade off -> performance * public synchronized String myMethod() { *     // logics before *     // critical section (10 lines) *     //logics after * } * * Lock lock = new Reentrantlock(); --> Condition ---> Thread -> Six state -> before start() NEW *                                                                            after start() Runnable * *  Lock + Condition -> thread1 execute first, then thread2 execute second, then come back to thread1 again * lock.lock() * try { *    // critical section * } catch { *   // critical section * } finally { *   // critical section *   lock.unlock(); * } * * *  * jvm -> stack, heap, method area, PC, native method stack *  * *  * class MyRunnableTask implements Runnable { *  *     public void run() { *  *         Employee e1 = new Employee("David"); *  *     } *  * } *  * *  * public static void main(String[] args) { *  * *  *     MyRunnableTask task = new MyRunnableTask();    -> .java -> .class -> (i) creation of object *  *                                                                          (ii) find a space in heap *  *                                                                          (iii) assign reference to object *  *     MyRunnableTask task1 = new MyRunnableTask(); *         task1.start(); *         task1.priority(10); *          MyRunnableTask task2 = new MyRunnableTask(); *          task2.start(); * *          11 (10 -> task1 *              1-> task2) * *          // set priority (1 ~ 10) *          // CPU -> 3.2 GHz *          1 / 3.2G second *          CPU time slot -> *  * *  * } * *   Pessimistic Lock ??? -> when to use *   Optimistic Lock -> not locking before modification *                      + *                      Versioning (CAS -> Compare And Swap) (non-locking thread safety) * *   value1 -> version1  -> thread1 -> execution logics -> value1Updated2 * * *   value1Updated -> version2 -> thread2 -> update(very quick) -> return * *   valueUpdate * *   Java -> AtomicInteger/AtomicReference<T> AtomicReference<Employee>  
 *                         AtomicReference<MYController>  
 *                          AtomicTimeStampedReference  
 *   ABA -> * *  pessimistic *  major(rw ww) vs major(rr) minor(rw ww) *  minor(rr)       optimistic * *  monitoring * *  80% read / 20% write  -> common pattern *  -----------------------------> 1 year *  insurance system -> GEICO -> * * * * * * * * * * * * * * * * * * * * * *  | | | | | | | | | | |                  | | | | | | | | | | | * |  .CLASS File      |                  |   Class Loader    | * |                   | ---------------->|                   | * |                   |                  |                   | * | | | | | | | | | | |                  | | | | | | | | | | | *                                            |         ^ *                                            |         | *                                            |         | *                                            V         | *  | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | *                                                                                      | *                                Memory Area (RAM)                                     | *                                                                                      | *       | | | | | | | | | | |     | | | | | | | | | | |     | | | | | | | | | | |      | *       |  Method Area      |     |  VM Stack         |     | Native Method     |      | *       |                   |     |                   |     |    stack          |      |                       | | | | | | | | | | | *       |                   |     |                   |     |                   |      |                       | Garbage Collector | *       |                   |     |                   |     |                   |      |                       |                   | *       |                   |     |                   |     |                   |      | ------------------->  |                   | *       | | | | | | | | | | |     | | | | | | | | | | |     | | | | | | | | | | |      |                       |                   | *                                                                                      |                       |                   | *       | | | | | | | | | | |      | | | | | | | | | | |                               |                       | | | | | | | | | | | *       |  Heap             |      |  Program Counter  |                               | *       |                   |      |     Register      |                               | *       |                   |      |                   |                               | *       |                   |      |                   |                               | *       |                   |      |                   |                               | *       | | | | | | | | | | |      | | | | | | | | | | |                               | *                          RunTime Data Memory Area                                    | *  | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | *          |         ^                       |         ^ *          |         |                       |         | *          |         |                       |         | *          V         |                       V         | *    | | | | | | | | | | |             | | | | | | | | | | |                    | | | | | | | | | | | *    | Execution Engine  |             |     native        |                    | Local Library     | *    |                   |             |    Interface      |                    |                   | *    |                   |             |                   | -----------------> |                   | *    |                   |             |                   |                    |                   | *    |                   |             |                   |                    |                   | *    | | | | | | | | | | |             | | | | | | | | | | |                    | | | | | | | | | | | * * * * * * * * */  
  
public class Day5 {  
  
  
  
  
  
}
```