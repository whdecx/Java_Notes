---
aliases: []
tags:
created: 2025-10-21
modified: 2025-10-21
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

## Synchronized Singleton
```Java
class Singleton {
    private volatile static Singleton instance;
    private Singleton(){}
    public static Singleton getInstance() {
        if (instance == null) {
            synchronized(Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
    }
}
```

### Why do we need `volatile`? 
Declaring the Singleton instance as `volatile` solves this by establishing a **happens-before** relationship, which enforces strict ordering and ensures visibility.

```java
public class Singleton {
    private static volatile Singleton instance; // <-- volatile here
    // ...
}
```

### What can go wrong
Imagine two threads:

|Thread A|Thread B|
|---|---|
|enters `getInstance()`|calls `getInstance()`|
|passes 1st `if (instance == null)`|sees `instance != null` (because of step 3 reordered before step 2)|
|starts constructing Singleton|returns a _partially constructed_ object ❌|

That means `Thread B` could get an **incompletely initialized object**, where fields are still default values (null/0).

1. **Prevents Reordering:** The `volatile` keyword forbids the CPU and compiler from reordering the memory allocation steps (specifically, it ensures that **initialization completes before the reference is published**).
2. **Guarantees Visibility:** It ensures that any write to the `volatile` variable (`instance`) by one thread is immediately visible to any other thread attempting to read it.

By using `volatile`, we guarantee that if a thread sees that `instance` is non-null, the object it points to is **guaranteed to be fully initialized and ready to use**.

### What is `volatile`

You tell the JVM and CPU:
> “This variable is shared — don’t cache it thread-locally. Always read/write it directly from main memory, and establish proper ordering.”

In other words:
- **Visibility guarantee:** A write to a `volatile` variable by one thread is immediately visible to all other threads.
- **Ordering guarantee:** It establishes a **happens-before** relationship:
    - A write to a `volatile` happens-before every subsequent read of that same variable.

## Non-synchronized code in between synchronized instructions

> ❓Question:  
> “Can non-synchronized code (from the same or another thread) execute _in between_ synchronized instructions?”

---

### 1. Within the **same thread**

No — inside one thread, code _cannot_ “jump out” of a synchronized block.

```java
synchronized(lock) {
    doA();
    doB();
}
doC();
```

The JVM emits `monitorenter` before `doA()` and `monitorexit` after `doB()`.  
Until the thread executes `monitorexit`, it **still holds the lock**, and the instructions inside execute as part of the same critical section.  
There’s no way for “outside” code to sneak in between those lines unless you call methods that voluntarily release the lock (e.g., `wait()`).

---
### 2. Between **different threads**

Yes — other threads **can execute non-synchronized code** _at the same time_, as long as they don’t try to acquire that same lock.

Example:

```java
synchronized(lock) {
    a = 1;
}
b = 2;  // not synchronized
```

While one thread holds the lock for `lock`, another thread can still run its own non-synchronized code, or synchronized code using a _different_ lock.

But **no thread** can enter another synchronized block guarded by the _same_ lock object until the first thread exits its block.

---

###  3. Memory visibility angle

`synchronized` creates a _happens-before_ edge only between:
- actions **before unlock** in one thread, and
- actions **after lock** in another thread (on the same monitor).
Unrelated, non-synchronized code isn’t ordered by that — it can interleave freely and even see stale values.

---

### 🧭 4. Summary table

|Scenario|Can non-synchronized code run “in between”?|Explanation|
|---|---|---|
|Inside same thread|❌ No|The monitor is held; no interruption until exit.|
|Other threads using different locks or no lock|✅ Yes|They’re independent.|
|Other threads using same lock|❌ No|Must wait for monitor release.|
|Non-synchronized reads/writes to shared data|✅ Yes (but unsafe)|They can interleave and see stale data.|
