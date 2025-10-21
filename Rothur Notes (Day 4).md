---
aliases: []
tags:
created: 2025-10-17
modified: 2025-10-20
status:
  - inactive
type:
  - instant_note
---

## Motivations / Purposes
- [[Interview planning]]

## Program, Process, Thread

| **Level**   | **Concept**                        | **Definition**                                                                                                          | **Key Characteristics**                                                                                                   | **Java Context**                                                                       |
| ----------- | ---------------------------------- | ----------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------- |
| **Program** | Static Code                        | A **passive** set of instructions stored on disk (e.g., `.class`, `.jar`, `.exe`)                                       | - Inactive- Immutable- Includes code, constants, metadata                                                                 | Java `.class` files are **bytecode**, loaded dynamically by **ClassLoader**.           |
| **Process** | Execution Routine of a Program     | A **running instance** of a program managed by the OS. Owns **private memory space** (heap, stack, PC, etc.).           | - Has its own address space- OS-managed- Can host multiple threads                                                        | The **JVM** itself is a _process_ (e.g., `java.exe` / `java` command).                 |
| **Thread**  | Unit of Execution within a Process | The **smallest schedulable unit** in the CPU, sharing the process’s memory but with its own **stack + PC + registers**. | - Lightweight- Shares heap with others- Can communicate via shared memory. Use `volatile` for sharing with other threads. | Java `Thread` objects map to **native OS threads** (or **virtual threads** in JDK 21). |


When you run a Java program (e.g., executing a `.class` file or a `.jar` file), you initiate a **Java Process** managed by the operating system, but the execution itself is handled by the **Java Virtual Machine (JVM)**.
### 1. Operating System (OS) Process Creation
The OS performs the initial setup to run the program:
- **Creates a Process:** The OS allocates a distinct, **isolated memory space (virtual address space)** for the new Java process. This space holds all the resources the program will use.
- **Loads the JVM:** The OS loads the JVM's executable code (usually `java.exe` or `java` command) into the allocated memory. The JVM itself is now the running process.
- **Creates the Main Thread:** The OS initiates the **main thread of execution** within this process, which immediately begins executing the JVM's startup routines.
### 2. Java Virtual Machine (JVM) Initialization
The JVM takes over and sets up the Java runtime environment within the process's memory space:
- **Loads Core Classes:** The JVM loads necessary core Java classes, including the bootstrap classes (like `java.lang.Object` and the `String` class).
- **Allocates Runtime Data Areas:** The JVM partitions the allocated OS memory into its own runtime data areas, which include:
    - **Method Area:** Stores **class metadata** (bytecode, field information, method data, etc.) and the **String Constant Pool**.
    - **Heap:** The shared memory area where **all Java objects** and arrays are allocated dynamically at runtime.1 This area is managed by the **Garbage Collector (GC)**.
    - **JVM Stacks:** Creates a **private stack** for every thread, which is used to store local variables, method parameters, and return addresses (known as **Stack Frames**).
### 3. Program Execution
Execution begins when the JVM's main thread finds and executes the program's entry point.
- **Locates `main()`:** The JVM looks for the public static `main(String[] args)` method in the specified class.
- **Execution Start:** The main thread starts executing the instructions in the `main()` method.2
- **Runtime Operations:**
    - When the program calls a new method, a new **stack frame** is pushed onto the thread's stack.
    - When a method creates a new object (using `new`), the object is placed in the **Heap**.
- **JIT Compilation:** The **Just-In-Time (JIT) Compiler** monitors frequently executed code. Instead of interpreting this bytecode every time, the JIT compiles it into highly optimized **native machine code**, significantly accelerating the program's performance.
## 4. Termination
The process terminates when the last non-daemon thread finishes execution (usually the main thread, unless other non-daemon threads were explicitly started).
- **JVM Shutdown:** The JVM initiates its shutdown sequence.3 The **Garbage Collector** cleans up any unreachable objects in the Heap.
- **OS Cleanup:** The OS reclaims the entire memory space and all resources that were owned by the Java Process.

## Summary Table

| **Feature**             | **Program**              | **Process**                                                     | **Thread**                                                                |
| ----------------------- | ------------------------ | --------------------------------------------------------------- | ------------------------------------------------------------------------- |
| **Nature**              | Passive                  | Active                                                          | Active (lightweight)                                                      |
| **Resource Ownership**  | None (on disk)           | Owns dedicated, isolated resources (Memory, Files, etc.)        | Shares resources with other threads in the same process.                  |
| **Switching Overhead**  | High (loading from disk) | High (Context switch requires saving/loading entire memory map) | Low (Context switch only requires saving/loading CPU registers and stack) |
| **Minimum Requirement** | N/A                      | Requires at least one thread.                                   | Must belong to a process.                                                 |
| **Analogy**             | The Recipe Book          | The Running Kitchen (Memory, Resources)                         | The Chef's Hands (Sequence of Execution)                                  |

## Thread Lifecycles
| **State**             | **Description**                                                           | **Triggered By**                                                       |
| --------------------- | ------------------------------------------------------------------------- | ---------------------------------------------------------------------- |
| **NEW**               | Thread object created but not yet started.                                | `new Thread(runnable)`                                                 |
| **RUNNABLE**          | Ready to run or currently executing. (The OS scheduler decides.)          | `start()` called → enters thread scheduler queue                       |
| **BLOCKED**           | Waiting to acquire a **lock/monitor**.                                    | Entering a synchronized block while another thread holds the lock      |
| **WAITING**           | Waiting **indefinitely** for another thread to perform a specific action. | `Object.wait()`, `Thread.join()` (no timeout), or `LockSupport.park()` |
| **TIMED_WAITING**     | Waiting for a **specified time**.                                         | `sleep(ms)`, `wait(timeout)`, `join(timeout)`, `parkNanos()`           |
| **TERMINATED (DEAD)** | Execution finished (run() completed or exception thrown).                 | run() method exits                                                     |

### Run v.s. Start

| **run()**               | **start()**                                                 |                                                            |
| ----------------------- | ----------------------------------------------------------- | ---------------------------------------------------------- |
| **What it does**        | Executes the code in the current thread.                    | Creates a new OS-level thread and calls `run()` inside it. |
| **Creates new thread?** | ❌ No                                                        | ✅ Yes                                                      |
| **Lifecycle state**     | Thread never leaves `NEW` (just acts like a normal method). | Moves from `NEW` → `RUNNABLE` → runs in parallel.          |
| **Usage example**       | `t.run(); // just a normal method call`                     | `t.start(); // real multithreading`                        |

## JDK7 to JDK 21
| **Year / Version**                        | **Major Highlights**                                                                                                                               | **Notes & Ecosystem Context**                                                                                                                                                                                                                                                                              |
| ----------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **JDK 7 (2011)**                          | `try-with-resources`, diamond operator (`<>`), NIO.2 (New I/O), `ForkJoinPool`                                                                     | Focused on syntactic cleanup and I/O improvements.Introduced **multi-core parallelism foundations** with `ForkJoinPool`, paving the way for future `parallelStream()` in JDK 8.First major Oracle-led release after Sun acquisition.                                                                       |
| ==**JDK 8 (2014)**==                      | ==**Lambdas**, **Streams API**, `Optional`, default & static methods in interfaces, `java.time` (JSR-310), Nashorn JS engine==                     | ==A **revolutionary release** — brought **functional programming** to Java.Enabled **declarative data processing** with Streams.JSR-310 replaced legacy `Date`/`Calendar`.JDK 8 became **the de facto LTS** for nearly a decade.Foundation for **Spring 5** and the **microservices explosion** (2016+).== |
| **JDK 9 (2017)**                          | **Java Platform Module System (JPMS)** aka Project Jigsaw                                                                                          | Introduced true modularization (`module-info.java`) to reduce JAR hell.However, adoption was slow due to migration complexity; many enterprise apps stayed on JDK 8.                                                                                                                                       |
| **JDK 10 (2018)**                         | **`var` keyword** for local variable type inference                                                                                                | Small but impactful — improved code brevity and readability.Signaled start of **6-month release cadence** for Java.                                                                                                                                                                                        |
| ==**JDK 11 (2018, LTS)**==                | ==**Flight Recorder (JFR)**, **Z Garbage Collector (ZGC)**, **HTTP Client API** (standardized from incubator)==                                    | ==Flight Recorder — originally commercial — became **free**, enabling advanced **profiling & observability**.HTTP client replaced clunky `HttpURLConnection`.First **LTS** under 6-month release model — became the new enterprise baseline.Spring Boot 2.x matured around this era.==                     |
| ==**Spring Framework Boom (2018–2020)**== | ==Spring Boot 2.x + Spring Cloud + Docker/Kubernetes ecosystem==                                                                                   | ==Standardization of **microservice architecture**.Integration with **Actuator**, **Micrometer**, and **Cloud Config**.DevOps-friendly Spring apps dominated the Java world.Common tech stack: JDK 8/11 + Spring Boot + Gradle/Maven + Docker.==                                                           |
| **JDK 14–16 (2020–2021)**                 | **Records (preview → final)**, **Pattern Matching (instanceof)**, **Text Blocks**, **Switch Expressions**                                          | Java syntax became more expressive and modern.**Records** simplified immutable DTOs.Bridged gap between Java verbosity and Kotlin/Scala ergonomics.                                                                                                                                                        |
| ==**JDK 17 (2021, LTS)**==                | ==**Sealed Classes**, **Pattern Matching**, **Records finalized**, **Enhanced pseudo-random generators**==                                         | ==Official **baseline for Spring Framework 6** and **Spring Boot 3.0**.Modern syntax features now LTS-stable.Marked **transition to JPMS compatibility** and stronger encapsulation of JDK internals.==                                                                                                    |
| **JDK 18 (2022)**                         | Simple web server (`jwebserver`), UTF-8 as default charset                                                                                         | Smaller QoL release.Helpful for lightweight demos and containerized Java runtimes.                                                                                                                                                                                                                         |
| **JDK 19 (2022)**                         | **Virtual Threads (Preview)** – **Project Loom**, Structured Concurrency (preview)                                                                 | Run with:`java --enable-preview -jar app.jar`.Introduced **Go-routine–like concurrency** — each virtual thread maps to a lightweight scheduler-managed continuation.Massively reduces overhead of thread-per-request servers.                                                                              |
| **JDK 20 (2023)**                         | Refined **Loom APIs** and pattern matching for switch                                                                                              | Continuation of experimental features — preparing for LTS stability.                                                                                                                                                                                                                                       |
| ==**JDK 21 (2023, LTS)**==                | ==**Virtual Threads finalized**, **Sequenced Collections**, **Pattern Matching for Switch**, **Record Patterns**, **String Templates (preview)**== | ==Virtual threads become **first-class citizens** — no longer experimental.Ushered a new era of **structured concurrency**, massively simplifying async code.Perfect for **Spring Boot 3.2+**, **reactive replacements**, and **high-throughput APIs**.Now default choice for new Java backend systems.==  |

## How to use threads in Java
| **#** | **Approach / API**                                               | **Introduced In**         | **How It Works**                                                                           | **Highlights / Use Cases**                                                                 | **Limitations / Notes**                                                      |
| ----- | ---------------------------------------------------------------- | ------------------------- | ------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------- |
| **1** | **`Thread` class**                                               | JDK 1.0                   | Extend `Thread` and override `run()`; call `start()` to create a new OS thread             | Simple, direct control over lifecycle; good for learning and demos                         | Tight coupling between task and thread; cannot return values; not scalable   |
| **2** | **`Runnable` interface**                                         | JDK 1.0                   | Implement `Runnable.run()` and pass to `new Thread(runnable)`                              | Separates **task logic** from **thread management**; reusable                              | No return value; no checked exception handling                               |
| **3** | **`Callable<V>` + `Future<V>`**                                  | JDK 5                     | Task returns a result (`V`) and may throw exceptions; submitted to `ExecutorService`       | Enables result retrieval (`Future.get()`); integrates with thread pools                    | `Future.get()` blocks; no built-in chaining or composition                   |
| **4** | **Thread Pools (`Executor`, `ExecutorService`, `ForkJoinPool`)** | JDK 5                     | Manages a pool of reusable worker threads to run submitted tasks                           | Efficient thread reuse, load management, async execution (`submit`, `invokeAll`)           | Manual tuning required; limited by fixed thread count; potential deadlocks   |
| **5** | **`CompletableFuture`**                                          | JDK 8                     | Asynchronous, non-blocking pipeline API using callbacks (`thenApply`, `thenCompose`, etc.) | Supports functional composition, combination (`allOf`, `anyOf`), and chaining              | Uses `ForkJoinPool.commonPool()` by default; can overwhelm it if misused     |
| **6** | **Virtual Threads (`Project Loom`)**                             | JDK 19 → **JDK 21 (LTS)** | Lightweight threads managed by JVM scheduler, not OS; `Thread.ofVirtual().start(...)`      | Millions of concurrent tasks; natural blocking code; ideal for high-throughput I/O servers | CPU-bound tasks still constrained by core count; debugging tools catching up |

## Thread Pool Logistics

### **Pool Initialization**

When you construct a `ThreadPoolExecutor`, it’s configured with:
- **corePoolSize** → minimum threads always kept alive
- **maximumPoolSize** → upper bound of threads
- **keepAliveTime, unit** → how long idle threads survive
- **workQueue** → where pending tasks wait
- **threadFactory** → how threads are created (naming, daemon, etc.)
- **handler** → what happens if the pool is saturated

### Execution  order
Interface relations:
```
Executor → ExecutorService → ThreadPoolExecutor
                ↑
             Executors (factory methods)
```

| Step  | Condition                           | Action                                                          |
| ----- | ----------------------------------- | --------------------------------------------------------------- |
| **①** | If current threads < `corePoolSize` | Create a **new worker thread**, run the task immediately        |
| **②** | Else if queue is not full           | Enqueue the task into **workQueue** (waiting for a free worker) |
| **③** | Else if threads < `maximumPoolSize` | Create a **new thread (beyond core)** to handle the overload    |
| **④** | Else (queue full, max reached)      | **Reject** task → call `RejectedExecutionHandler`               |

|Queue Type|Behavior|
|---|---|
|`SynchronousQueue`|No buffer — tasks handed directly to threads. Pool grows fast under load.|
|`LinkedBlockingQueue`|Potentially unbounded. Pool won’t grow past `corePoolSize`; queue grows instead.|
|`ArrayBlockingQueue`|Bounded, fixed-capacity. Pool grows and rejects properly under pressure.|
|`PriorityBlockingQueue`|Ordered by priority, not FIFO.|
|`DelayQueue`|For delayed/scheduled tasks.|

### **Rejection Handling**

When the pool and queue are full, one of four policies applies:

- `AbortPolicy` → throws `RejectedExecutionException` (default)
- `CallerRunsPolicy` → caller executes task (throttles producers)
- `DiscardPolicy` → silently drops task
- `DiscardOldestPolicy` → drops oldest queued task

--- 
## Code from class
```java
package org.example.rothur;  
  
import java.util.concurrent.Callable;  
import java.util.concurrent.ExecutorService;  
import java.util.concurrent.Executors;  
import java.util.concurrent.locks.LockSupport;  
  
public class Day5 {  
    public static void main(String[] args){  
        new MyThread().start();  
//        new MyThread().run();  
        System.out.println("main thread");  
  
        ExecutorService executorService = Executors.newCachedThreadPool();  
    }  
}  
  
  
class MyThread extends Thread {  
    public void run() {  
        System.out.println("my thread");  
    }  
}  
  
class MyRunnable implements Runnable {  
    public void run() {  
        System.out.println("my thread");  
    }  
}  
  
// class MyCallable implements Callable {  
//  
// }  
  
/**  
 * *                        jdk7 *  *                        jdk8 -> 2014 (buggy) -> 2018 -> Spring framework booming *  *                          jdk11 -> release "flight recorder" -> free feature *  *                        jdk17 -> Spring6/SpringBoot3.0 -> *  *                          jdk19 -> virtual thread -> Loom project (java -jar --experimental main.jar) -> GO/GO routine *  *                        jdk21 -> virtual thread (normal object) * * * *  static codes                                execution routine of a process *  program,            process,                thread -> volatile *                      execution of a program * *  Thread lifecycle -> Six State *  run() vs start() * *  Thread -> Creation -> 1, Thread class  
 *                        2, Runnable interface *                        3, Callable interface *                        4, Thread Pool -> (the only) -> (jdk21) * *                        CompletableFuture * * *      Executor -> ExecutorService -> ThreadPoolExecutor *                    ^            corePoolSize *                    |            maximumPoolSize *                    |            keepAliveTime                                            | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | *                    |            unit                                                     | queue            |                  |                     |                 |                   | *                Executors        workQueue                               |--------------->|                  |                  |                     |                 |                   | *                                 threadFactory                           |                |                  |                  |                     |                 |                   | *                                 handler                                 |                | | | | | | | | | || | | | | | | | | || | | | | | | | | | | || | | | | | | |  | | | | | | | | | | | *                                                                         | *                                                                         2                                 | | | | | | | | | | | | | | | | | | | | | | | | | | | *                                                                         |                                 |                 maximumPool                       | *                             | | | | | | | | | | |            | | | | | | | | | | |                        |                                                   | *                             | main thread       |            | execute()         |                        |                                                   | *                             |                   |            |                   |                        |            | | | | | | | | | | |                  | *                             | Runnable.run()    |----------->|                   |                        |            | corePool          |                  | *                             |                   |            |                   |------1-----------------|----------->|                   |                  | *                             |                   |            |                   |                        |            | ||||||||||||||||  |                  | *                             | | | | | | | | | | |            | | | | | | | | | | |------3------------|    |            | | thread 0     |  |                  | *                               ^                                        |                             |    |            | |              |  |                  | *                               |                                        |                             |    |            | ||||||||||||||||  |                  | *                               |                                        4                             |    |            |                   |                  | *                               |                                        |                             |    |            | ||||||||||||||||  |                  | *                               |                                        V                             |    |            | | thread 1     |  |                  | *                               |               | | | | | | | | | | | | | | | | | | | | | | | | | | |  |    |            | |              |  |                  | *                               |               |                                                   |  |    |            | ||||||||||||||||  |                  | *                               |               |   | | | | | | | | | |  | | | | | | | | | |        |  |    |            | | | | | | | | | | |                  | *                               |               |   | AbortPolicy     |  | DiscardPolicy   |        |  |    |                                                   | *                               |---------------|   | | | | | | | | | |  | | | | | | | | | |        |  |    |              ||||||||||||||||                     | *                                               |                                                   |  |----|------------->| thread 2     |                     | *                                               |   | | | | | | | | | |  | | | | | | | | | | | |    |       |              |              |                     | *                                               |   | CallerRunsPolicy|  | DiscardOldestPolicy |    |       |              ||||||||||||||||                     | *                                               |   | | | | | | | | | |  | | | | | | | | | | | |    |       |                                                   | *                                               |                                                   |       |              ||||||||||||||||                     | *                                               |                                                   |       |              | thread 3     |                     | *                                               | | | | | | | | | | | | | | | | | | | | | | | | | | |       |              |              |                     | *                                                                                                           |              ||||||||||||||||                     | *                                                                                                           | | | | | | | | | | | | | | | | | | | | | | | | | | | * * * * * *                    1 *  Web app 1   -------> Web app 2 *                     2 *              <-------- * *  CompletableFuture.supplyAsync(TASK!).thenApply(task2).thenAccept(task3); *   *  CompletableFuture.supplyAsync(TASK!).thenApply(task2).thenAccept(task3); *    *  CompletableFuture.supplyAsync(TASK!).thenApply(task2).thenAccept(task3); *     *  CompletableFuture.supplyAsync(TASK!).thenApply(task2).thenAccept(task3); *      *  CompletableFuture.supplyAsync(TASK!).thenApply(task2).thenAccept(task3); *       *  CompletableFuture.supplyAsync(TASK!).thenApply(task2).thenAccept(task3); *        *  CompletableFuture.supplyAsync(TASK!).thenApply(task2).thenAccept(task3); * *      result1 = CallaableTask1.get() *  ->  result2 = CallableTask2(result1).get() *  -> result3=CallablaeTask3(result2).get() * * *   *      result1 = CallaableTask1.get() *  *  ->  result2 = CallableTask2(result1).get() *  *  -> result3=CallablaeTask3(result2).get() * * *   *      result1 = CallaableTask1.get() *  *  ->  result2 = CallableTask2(result1).get() *  *  -> result3=CallablaeTask3(result2).get() * * *   *      result1 = CallaableTask1.get() *  *  ->  result2 = CallableTask2(result1).get() *  *  -> result3=CallablaeTask3(result2).get() * *   *      result1 = CallaableTask1.get() *  *  ->  result2 = CallableTask2(result1).get() *  *  -> result3=CallablaeTask3(result2).get() * *   *      result1 = CallaableTask1.get() *  *  ->  result2 = CallableTask2(result1).get() *  *  -> result3=CallablaeTask3(result2).get() * *  * 100 * *  task1 ->              task2 ->             task3 *  CompletableFuture   CompletableFuture          CompletableFuture * *   *  task1 ->              task2 ->             task3 *  *  CompletableFuture   CompletableFuture          CompletableFuture * *   *  task1 ->              task2 ->             task3 *  *  CompletableFuture   CompletableFuture          CompletableFuture * *   *  task1 ->              task2 ->             task3 *  *  CompletableFuture   CompletableFuture          CompletableFuture * * * * *   | | | | | | | | | | |                  | | | | | | | | | | | *   |  .CLASS File      |                  |   Class Loader    | *   |                   | ---------------->|                   | *   |                   |                  |                   | *   | | | | | | | | | | |                  | | | | | | | | | | | *                                              |         ^ *                                              |         | *                                              |         | *                                              V         | *  | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | *  |                                                                                     | *  |                               Memory Area (RAM)                                     | *  |                                                                                     | *  |      | | | | | | | | | | |     | | | | | | | | | | |     | | | | | | | | | | |      | *  |      |  Method Area      |     |  VM Stack         |     | Native Method     |      | *  |      |                   |     |                   |     |    stack          |      |                       | | | | | | | | | | | *  |      |                   |     |                   |     |                   |      |                       | Garbage Collector | *  |      |                   |     |                   |     |                   |      |                       |                   | *  |      |                   |     |                   |     |                   |      | ------------------->  |                   | *  |      | | | | | | | | | | |     | | | | | | | | | | |     | | | | | | | | | | |      |                       |                   | *  |                                                                                     |                       |                   | *  |      | | | | | | | | | | |      | | | | | | | | | | |                               |                       | | | | | | | | | | | *  |      |  Heap             |      |  Program Counter  |                               | *  |      |                   |      |     Register      |                               | *  |      |                   |      |                   |                               | *  |      |                   |      |                   |                               | *  |      |                   |      |                   |                               | *  |      | | | | | | | | | | |      | | | | | | | | | | |                               | *  |                         RunTime Data Memory Area                                    | *  | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | *            |         ^                       |         ^ *            |         |                       |         | *            |         |                       |         | *            V         |                       V         | *      | | | | | | | | | | |             | | | | | | | | | | |                    | | | | | | | | | | | *      | Execution Engine  |             |     native        |                    | Local Library     | *      |                   |             |    Interface      |                    |                   | *      |                   |             |                   | -----------------> |                   | *      |                   |             |                   |                    |                   | *      |                   |             |                   |                    |                   | *      | | | | | | | | | | |             | | | | | | | | | | |                    | | | | | | | | | | | * */  
  
/**  
 * A thread state.  A thread can be in one of the following states: * <ul>  
 * <li>{@link #NEW}<br>  
 *     A thread that has not yet started is in this state.  
 *     </li>  
 * <li>{@link #RUNNABLE}<br>  
 *     A thread executing in the Java virtual machine is in this state.  
 *     </li>  
 * <li>{@link #BLOCKED}<br>  
 *     A thread that is blocked waiting for a monitor lock  
 *     is in this state. *     </li>  
 * <li>{@link #WAITING}<br>  
 *     A thread that is waiting indefinitely for another thread to  
 *     perform a particular action is in this state. *     </li>  
 * <li>{@link #TIMED_WAITING}<br>  
 *     A thread that is waiting for another thread to perform an action  
 *     for up to a specified waiting time is in this state. *     </li>  
 * <li>{@link #TERMINATED}<br>  
 *     A thread that has exited is in this state.  
 *     </li>  
 * </ul>  
 *  
 * <p>  
 * A thread can be in only one state at a given point in time.  
 * These states are virtual machine states which do not reflect * any operating system thread states. * * @since   1.5  
 * @see #getState  
 */enum State {  
    /**  
     * Thread state for a thread which has not yet started.     */    NEW,  
  
    /**  
     * Thread state for a runnable thread.  A thread in the runnable     * state is executing in the Java virtual machine but it may     * be waiting for other resources from the operating system     * such as processor.     */    RUNNABLE,  
  
    /**  
     * Thread state for a thread blocked waiting for a monitor lock.     * A thread in the blocked state is waiting for a monitor lock     * to enter a synchronized block/method or     * reenter a synchronized block/method after calling     * {@link Object#wait() Object.wait}.  
     */    BLOCKED,  
  
    /**  
     * Thread state for a waiting thread.     * A thread is in the waiting state due to calling one of the     * following methods:     * <ul>  
     *   <li>{@link Object#wait() Object.wait} with no timeout</li>  
     *   <li>{@link #join() Thread.join} with no timeout</li>  
     *   <li>{@link LockSupport#park() LockSupport.park}</li>  
     * </ul>  
     *  
     * <p>A thread in the waiting state is waiting for another thread to  
     * perform a particular action.     *     * For example, a thread that has called {@code Object.wait()}  
     * on an object is waiting for another thread to call     * {@code Object.notify()} or {@code Object.notifyAll()} on  
     * that object. A thread that has called {@code Thread.join()}  
     * is waiting for a specified thread to terminate.     */    WAITING,  
  
    /**  
     * Thread state for a waiting thread with a specified waiting time.     * A thread is in the timed waiting state due to calling one of     * the following methods with a specified positive waiting time:     * <ul>  
     *   <li>{@link #sleep Thread.sleep}</li>  
     *   <li>{@link Object#wait(long) Object.wait} with timeout</li>  
     *   <li>{@link #join(long) Thread.join} with timeout</li>  
     *   <li>{@link LockSupport#parkNanos LockSupport.parkNanos}</li>  
     *   <li>{@link LockSupport#parkUntil LockSupport.parkUntil}</li>  
     * </ul>  
     */  
    TIMED_WAITING,  
  
    /**  
     * Thread state for a terminated thread.     * The thread has completed execution.     */    TERMINATED;  
}
```