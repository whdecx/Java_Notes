---
aliases: []
tags:
created: 2025-10-17
modified: 2025-10-17
status:
  - inactive
type:
  - instant_note
---

## Motivations / Purposes

## Goals / Non-Goals
- Always remember to achieve your goals
- Don't spend too much time on non-goals or tangent topics
- Aim for a minimal viable product first

## Main Content
### Program, Process, Thread
The concepts of **program**, **process**, and **thread** are hierarchical layers of software execution in an operating system.

## Program (The Passive Entity)

A **program** is a passive entity—it is a set of instructions written in a programming language.
- **Definition:** A program is a file residing on disk, like an executable file (.exe, .app, or a Java .jar file).
- **Role:** It is the code, data, and resources needed to execute a task, but it does not perform any activity until it is loaded into memory.
- **Analogy:** A recipe book 📚. It contains instructions, but no cooking is happening yet.

---

## Process (The Active Entity)

A **process** is an active instance of a program being executed.3 It is the operating system's primary unit of resource allocation.
- **Definition:** A process is a running program. It includes the executable code, its entire dedicated virtual address space, open files, security information, and at least one thread of execution.5
- **Resource Ownership:** Each process is **isolated** and owns its own resources, primarily **memory (heap, stack, code/data segments)**.6 One process cannot directly access the memory of another.
- **Analogy:** The chef actively following the recipe 👨‍🍳. The chef has their own kitchen (memory), ingredients (data), and tools (resources).

---

## Thread (The Unit of Execution)

A **thread** is the smallest unit of execution within a process.7 It is a sequence of instructions that can be executed independently.8

- **Definition:** A thread is a lightweight subprocess. It is the CPU's dispatcher unit, responsible for actual instruction execution.
- **Resource Sharing:** Threads **share** the process's memory space and resources (code, heap memory, open files).9 This allows threads to communicate and exchange data much faster than processes.
- **Thread-Specific Data:** Each thread maintains its own **Program Counter**, its own set of **CPU Registers**, and its own **Stack** (for local variables and method calls).11
- **Analogy:** The chef's individual **hands** 🤚🤚. They share the same kitchen and ingredients but perform different tasks concurrently (one hand chops vegetables while the other stirs the pot).


When you run a Java program (e.g., executing a `.class` file or a `.jar` file), you initiate a **Java Process** managed by the operating system, but the execution itself is handled by the **Java Virtual Machine (JVM)**.

Here is what happens when you run a Java process:

### 1. Operating System (OS) Process Creation

The OS performs the initial setup to run the program:

- **Creates a Process:** The OS allocates a distinct, **isolated memory space (virtual address space)** for the new Java process. This space holds all the resources the program will use.
    
- **Loads the JVM:** The OS loads the JVM's executable code (usually `java.exe` or `java` command) into the allocated memory. The JVM itself is now the running process.
    
- **Creates the Main Thread:** The OS initiates the **main thread of execution** within this process, which immediately begins executing the JVM's startup routines.
    

---

### 2. Java Virtual Machine (JVM) Initialization

The JVM takes over and sets up the Java runtime environment within the process's memory space:

- **Loads Core Classes:** The JVM loads necessary core Java classes, including the bootstrap classes (like `java.lang.Object` and the `String` class).
    
- **Allocates Runtime Data Areas:** The JVM partitions the allocated OS memory into its own runtime data areas, which include:
    
    - **Method Area:** Stores **class metadata** (bytecode, field information, method data, etc.) and the **String Constant Pool**.
        
    - **Heap:** The shared memory area where **all Java objects** and arrays are allocated dynamically at runtime.1 This area is managed by the **Garbage Collector (GC)**.
        
    - **JVM Stacks:** Creates a **private stack** for every thread, which is used to store local variables, method parameters, and return addresses (known as **Stack Frames**).
        

---

### 3. Program Execution

Execution begins when the JVM's main thread finds and executes the program's entry point.

- **Locates `main()`:** The JVM looks for the public static `main(String[] args)` method in the specified class.
- **Execution Start:** The main thread starts executing the instructions in the `main()` method.2
- **Runtime Operations:**
    - When the program calls a new method, a new **stack frame** is pushed onto the thread's stack.
    - When a method creates a new object (using `new`), the object is placed in the **Heap**.
- **JIT Compilation:** The **Just-In-Time (JIT) Compiler** monitors frequently executed code. Instead of interpreting this bytecode every time, the JIT compiles it into highly optimized **native machine code**, significantly accelerating the program's performance.
    

---

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