---
aliases: []
tags:
created: 2025-10-15
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


## Comparator v.s. Comparable

| **Feature**           | **Comparable**                                                                                                                                             | **Comparator**                                                                         |
| --------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------- |
| ==**Purpose**==       | ==Defines the **single, natural ordering** for a class that the class can compare object with itself implement by implement this interface on the class.== | ==Defines **multiple, custom orderings** for a class through a lambda expression.==    |
| **Package**           | `java.lang`                                                                                                                                                | `java.util`                                                                            |
| **Method Name**       | `compareTo(Object obj)`                                                                                                                                    | `compare(Object obj1, Object obj2)`                                                    |
| **Implementation**    | Implemented **inside the class** itself.                                                                                                                   | Implemented in a **separate class** or as a lambda.                                    |
| **# of Sort Orders**  | Provides only **one** way to sort.                                                                                                                         | Provides **many** different ways to sort.                                              |
| **Code Modification** | Requires you to be able to **modify the class** you are sorting.                                                                                           | **Does not** require modifying the class; can sort third-party classes.                |
| **Primary Use Case**  | Used by `Collections.sort(list)` and `Arrays.sort(array)`.                                                                                                 | Passed as an argument to `sort()` methods, e.g., `Collections.sort(list, comparator)`. |

## Collection
![image](Attachments/c06899a3fcca6041984c77eeeac70649_MD5.png)

### List
A list let you manage a dynamically-sized **collection of elements** with a certain order allowing random accessing.

| **Operation**                          | **ArrayList (Dynamic Array)**                                                                              | **LinkedList (Doubly Linked List)**                                                                              |
| -------------------------------------- | ---------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| **Random Access (`get(index)`)**       | **$O(1)$** (Direct calculation of memory address by index).                                                | ==$O(n)$ (Must traverse from the head or tail to reach the $n$-th element).==                                    |
| **Insertion/Deletion at End**          | **$O(1)$ amortized** (Fast, but occasionally $O(n)$ when the underlying array must be resized and copied). | $O(1)$ (Fast, as it maintains a direct reference to the tail node).                                              |
| **Insertion/Deletion in Middle/Start** | ==$O(n)$ (Requires **shifting** all subsequent elements).==                                                | **$O(1)$** (==Once the node is found==, only a few pointers need to be updated). Finding a element takes $O(n)$. |
| **Iteration (Sequential Scan)**        | **Fast** ($O(n)$) (Excellent cache locality).                                                              | Slower ($O(n)$) (Poor cache locality due to random memory access).                                               |
### Queue
A collection of elements that supports add and remove operations. Traditionally, it follows FIFO logic. However, one can also define custom add/remove order by defining a priority for each item.

| **Feature**                     | **ArrayDeque (Resizable Circular Array)**                                                    | **LinkedList (Doubly Linked List)**                                                                                    |     |
| ------------------------------- | -------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------- | --- |
| **Data Storage**                | ==Elements are stored in a **contiguous block of memory** (an array).==                      | ==Elements are stored in **separate nodes** scattered across memory, each with two pointers (`next` and `previous`).== |     |
| **Insertion/Removal at Ends**   | **$O(1)$ Amortized** (Fast, but occasionally $O(n)$ if the underlying array needs resizing). | **$O(1)$ Worst-Case** (Fast, only requires changing two pointers).                                                     |     |
| **Insertion/Removal in Middle** | $O(n)$ (Requires **shifting** all subsequent elements).                                      | $O(n)$ (Requires **traversing** to find the node, then $O(1)$ for the pointer change).                                 |     |
| **Random Access (`get(i)`)**    | **$O(1)$** (Direct calculation of memory address by index).                                  | $O(n)$ (Must traverse from the head or tail to the $n$-th element).                                                    |     |
| **Memory Overhead**             | **Low** (Stores only data).                                                                  | **High** (Each element requires memory for two extra pointers).                                                        |     |
| **Cache Locality**              | ==**Excellent** (Contiguous data utilizes CPU cache effectively, making iteration faster).==     | ==**Poor** (Scattered nodes cause frequent **cache misses** or "pointer chasing").==                                       |     |
| **`null` Elements**             | **Not allowed**.                                                                             | **Allowed**.                                                                                                           |     |
| **Interface**                   | Implements **`Deque`**. Does **NOT** implement the `List` interface.                         | Implements **`Deque` AND `List`** (allows random access, though inefficiently).                                        |     |

### Set
 A collection that contains no duplicate elements.
#### Comparison among `Set` Implementations

| Feature                | `HashSet`                                | `LinkedHashSet`                              | `TreeSet`                                     |
| ---------------------- | ---------------------------------------- | -------------------------------------------- | --------------------------------------------- |
| **Ordering**           | **Unordered** (no guarantee)             | **Insertion Order** (predictable)            | **Sorted Order** (natural or by `Comparator`) |
| **Performance**        | **O(1)** for `add`, `remove`, `contains` | **O(1)** for `add`, `remove`, `contains`     | **O(log n)** for `add`, `remove`, `contains`  |
| **Internal Structure** | Hash Table                               | Hash Table + Doubly-Linked List              | Red-Black Tree                                |
| **`null` values?**     | Allows one `null`                        | Allows one `null`                            | Does not allow `null` (by default)            |
| **Best For**           | Maximum speed when order doesn't matter. | Uniqueness with predictable insertion order. | Uniqueness with automatic sorting.            |

## Map
The **`Map` interface** in Java is a core part of the Collections Framework that represents an object designed to store data as **key-value pairs**, modeling the mathematical **function abstraction**.

| **Class**           | **Underlying Structure**            | **Order Guarantee**                                                       | **Performance**                              | **Use Case**                                                                           |
| ------------------- | ----------------------------------- | ------------------------------------------------------------------------- | -------------------------------------------- | -------------------------------------------------------------------------------------- |
| **`HashMap`**       | Hash Table                          | **None** (Arbitrary/Unpredictable order).                                 | **$O(1)$** (Fastest for general operations). | Default choice when **speed** is prioritized and order is irrelevant.                  |
| **`LinkedHashMap`** | Hash Table + Doubly Linked List     | **Insertion Order** (Elements are iterated in the order they were added). | **$O(1)$** (Slight overhead vs. `HashMap`).  | When you need the performance of a Hash Map, but must **preserve the order** of entry. |
| **`TreeMap`**       | Red-Black Tree (Self-balancing BST) | **Sorted Order** (Keys are sorted naturally or by a `Comparator`).        | **$O(\log n)$** (Slower than Hash Maps).     | When you need the keys to be **automatically sorted** (e.g., for range queries).       |

### Hashtable v.s. ConcurrentHashMap
| **Feature**                 | **Hashtable**                                                                                                     | **ConcurrentHashMap (Recommended)**                                                                                                                                                             |
| --------------------------- | ----------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Thread Safety Mechanism** | **Coarse-Grained Locking:** Achieved by synchronizing **every public method** (`put()`, `get()`, `remove()`).     | **Fine-Grained Locking/Lock-Free:** Achieved using techniques like **bucket/segment locking** (pre-Java 8) or **CAS (Compare-and-Swap)** and synchronized blocks on individual nodes (Java 8+). |
| **Concurrency**             | **Low.** Only **one thread** can access the entire table at a time (read or write).                               | **High.** Allows **multiple threads to read concurrently** (no lock) and **multiple threads to write concurrently** (by locking only the bucket being modified).                                |

## About `hashcode` and `equals`
For data structures like `Map` and `Set`, we want to **quickly locate an object in a hash table** and **check if two objects are equal.**

| **Method**       | **Role (Why We Override)**                                                                                                                                                                                                                                                   | **Consequence of NOT Overriding**                                                                                                                                                                                         |
| ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`equals()`**   | **Defines Logical Equality.** Overriding tells the program how to determine if two custom objects (e.g., two different `Person` objects) are **logically the same** based on their content (e.g., same ID and name), rather than just being the exact same object in memory. | Two objects that _should_ be equal (same content) will be treated as separate entries, leading to **duplicate data** in `Set`s and failures in finding values in `Map`s.                                                  |
| **`hashCode()`** | **Ensures Performance and Correctness.** It provides a quick integer value used by hash-based collections to determine which **bucket** in memory an object should be stored or retrieved from.                                                                              | **Bugs and Performance Loss.** If two objects are equal but have different hash codes, the collection will look in the wrong bucket and **fail to find the object**, violating the core **`hashCode`/`equals` contract**. |

## About Exceptions
### Exception Types
The Java exception system is rooted in the **`Throwable`** class, which has two direct descendants: `Error` and `Exception`.

### `Throwable`
The **`Throwable`** class is the superclass of all errors and exceptions in Java. Only objects that are instances of this class (or one of its subclasses) can be thrown or caught.

---

## `Error` vs. `Exception`

The division between `Error` and `Exception` is based on how severe the problem is:

|**Class**|**Purpose**|**Source of Problem**|**Handling**|
|---|---|---|---|
|**`Error`**|Represents serious, **unrecoverable** problems that typically occur in the Java Virtual Machine (JVM).|JVM environment (e.g., memory exhaustion, linking failure).|**You should not catch or handle Errors.** They indicate a catastrophic failure.|
|**`Exception`**|Represents problems that an application _should_ try to catch and recover from.|Application logic, I/O, network issues, etc.|**Must be handled** (checked) or may be handled (unchecked).|

---

### Runtime vs. Compile-Time Exceptions
| **Feature**              | **Runtime Exception (Unchecked)**                                                                                                     | **Compile-Time Exception (Checked)**                                                                                              |
| ------------------------ | ------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| **Parent Class**         | Extends **`RuntimeException`**                                                                                                        | Extends **`Exception`** (but not `RuntimeException`)                                                                              |
| **Handling Requirement** | **Optional.** The compiler does **not** force you to catch or declare it.                                                             | **Mandatory.** The compiler **forces** you to either handle it (`try-catch`) or declare it (`throws`).                            |
| **When Detected**        | **Runtime.** Occurs during program execution, often due to logical flaws or invalid operations.                                       | **Compile Time.** The potential for the exception is recognized by the compiler.                                                  |
| **Typical Cause**        | Programming errors or improper use of an API (e.g., trying to access an array element outside its bounds, passing a `null` argument). | External factors that are usually beyond programmer control but are potentially recoverable (e.g., I/O failures, network errors). |
| **Common Examples**      | `NullPointerException`, `IndexOutOfBoundsException`, `IllegalArgumentException`.                                                      | `IOException`, `SQLException`, `FileNotFoundException`.                                                                           |

### Custom Exception Creation
You can customize exceptions in Java by creating your own **custom exception classes**. This is done by extending one of the two main parent exception classes: `Exception` or `RuntimeException`.

Custom exceptions are valuable because they provide **specific, meaningful context** to an error, allowing other parts of your code to handle different problems precisely.

| **Your Class Should...**   | **When to Use**                                                                                                                                                           |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `extends RuntimeException` | To create an **Unchecked Exception**. Use this for logic errors that should be fixed by the programmer (e.g., **`InvalidUsernameException`**).                            |
| `extends Exception`        | To create a **Checked Exception**. Use this for external, environmental problems that the compiler should force callers to handle (e.g., **`PrinterOutOfInkException`**). |

## About String and Integer
### String
The Java **`String`** class is one of the most critical and highly optimized classes in the language. Its unique behavior is defined by its **immutability** and the JVM's use of the **String Constant Pool**.

---

## 1. Immutability (The Core Design)

A Java `String` is **immutable**, meaning once a `String` object is created, its value (the sequence of characters) cannot be changed.

- **Modification Illusion:** Methods like `concat()`, `replace()`, or `substring()` don't change the original object; instead, they return a **new `String` object** containing the result.
- **Internal Representation:** Internally, a `String` is backed by an array (a `char[]` in older versions or a `byte[]` in Java 9+ for memory efficiency). This array is essentially made final, preventing external manipulation.

### Why Immutability is Essential
Immutability enables several core Java features and optimizations:
- **Thread Safety:** Since the state of a `String` never changes, it's inherently thread-safe and can be safely shared across multiple threads without needing external synchronization (locks).
- **Security:** `String`s are often used for security-sensitive data (like file names, passwords, and network connections). Immutability guarantees that a variable checked for security (e.g., file permissions) cannot be altered after the check is performed.
- **Hashing:** `String` objects are commonly used as keys in hash-based collections (`HashMap`, `HashSet`). Immutability ensures the hash code, which is calculated once and **cached**, remains consistent throughout the object's lifetime.

---

## 2. String Constant Pool (Memory Optimization)

The **String Constant Pool (SCP)** is a special memory area within the Heap managed by the JVM to store unique string literals. This is a crucial memory optimization technique known as **interning**.

### Creation Behavior

The way you create a `String` determines whether it goes into the SCP or the regular heap:

|**Creation Method**|**Location**|**Behavior**|
|---|---|---|
|**Literal** (`String s = "hello";`)|**SCP.**|The JVM checks the pool. If "hello" exists, the existing reference is reused (no new object is created). This saves memory.|
|**Constructor** (`String s = new String("hello");`)|**Heap.**|**Always** creates a new object in the regular heap memory, regardless of whether "hello" already exists in the SCP.|
|**`intern()` Method**|**SCP.**|Calling `s.intern()` forces the JVM to check the SCP. If the string is already present, it returns the pooled reference; otherwise, it adds the string to the pool.|

### Memory Allocation Example

```
String s1 = "Java";    // Creates "Java" in SCP
String s2 = "Java";    // s2 points to the SAME object in SCP as s1 (s1 == s2 is true)

String s3 = new String("Java"); // Creates a NEW object in the HEAP

// s1 == s3 is FALSE because s1 points to the pool and s3 points to the heap
```

---

## 3. String Concatenation Performance
Using the standard **`+`** operator for string concatenation is inefficient because immutability forces the creation of a **new `String` object** at every step.

For dynamic string building or concatenation inside loops, Java automatically or explicitly provides **mutable** alternatives:
- **`StringBuilder`:** The non-synchronized (faster) class used when concatenation is done in a single thread (recommended for local use).
- **`StringBuffer`:** The synchronized (slower) class used when concatenation is performed across multiple threads (thread-safe).


### Integer
The **`Integer`** class in Java is the wrapper class for the primitive type `int`. Its details and optimizations, like those for `String`, are built upon its nature as an **immutable** object.

## 1. Immutability

Like all wrapper classes (`Byte`, `Short`, `Long`, etc.), an `Integer` object's value cannot be changed once it's created. If you perform an arithmetic operation on an `Integer`, a brand new `Integer` object is produced.

- **Benefit:** Immutability makes `Integer` objects inherently **thread-safe** and reliable for use in hash-based collections (`HashMap` keys, `HashSet` elements) because their hash code will never change.
    

---

## 2. Autoboxing and Caching (The Integer Cache)

The key performance optimization for `Integer` is achieved through **caching** for a specific range of values during the process of **autoboxing** (the automatic conversion of an `int` primitive to an `Integer` object).

### How Caching Works

1. **Cache Range:** The JVM maintains a static internal cache (the `Integer.IntegerCache`) for all `Integer` objects with values between **$-128$ and $127$**.
    
2. **Reuse:** When the JVM needs an `Integer` object within this range (typically during autoboxing, e.g., `Integer x = 5;`), it checks the cache and returns a reference to the **existing, single instance** of that number.
    
3. **Heap Allocation:** For numbers **outside** this range (e.g., $128$ or $-129$), a new `Integer` object is created in the regular heap memory.
    

### Impact on == Operator

This caching mechanism leads to a peculiar behavior when using the equality operator ==

|**Comparison**|**Result**|**Reason**|
|---|---|---|
|`Integer a = 100; Integer b = 100;`|`a == b` is **true**|Both variables reference the **same cached object** (within the $-128$ to $127$ range).|
|`Integer x = 200; Integer y = 200;`|`x == y` is **false**|Both variables refer to **different objects** created in the heap (outside the cache range).|
|`Integer.valueOf(200)`|**Same as above.** The `valueOf()` method uses the cache; if the value is outside the cache, it behaves like `new Integer(200)`.||

**Crucial Rule:** Always use the **`.equals()`** method to compare the logical values of `Integer` objects to avoid unexpected behavior due to caching.

## 3. Other Wrapper Caches
Similar, fixed caching is applied to other wrapper classes:
- **`Boolean`:** Always returns the same two instances (`Boolean.TRUE` or `Boolean.FALSE`).
- **`Byte`, `Short`, `Long`:** Caches values in the guaranteed range of **$-128$ to $127$**.
- **`Character`:** Caches values from **$0$ to $127$**.

### OOP
#### Inheritance
- Interface
- Abstract class
- final

#### Encapsulation
- Access modifier

#### Polymorphism
- Override vs Overload

#### Abstraction
- Interface
- Abstract class


### SOLID Principles
#### Single Responsibility
**The Principle:** A class should have only one reason to change.

This means each class should have one, and only one, job or responsibility. If a class does too many things, it becomes tightly coupled and a change to one of its responsibilities could inadvertently break another.

#### Open / Close
**The Principle:** Software entities (classes, modules, functions) should be open for extension, but closed for modification.

This means you should be able to add new functionality to a class without changing its existing source code. This is typically achieved using interfaces, abstract classes, and polymorphism.

#### Liskov Substitution Principle
**The Principle:** Subtypes must be substitutable for their base types without altering the correctness of the program.

This means that a child class should be able to do everything its parent class can do, without any unexpected side effects. If a child class breaks the "contract" of its parent, it violates this principle.

#### Interface Segregation
**The Principle:** Clients should not be forced to depend on interfaces they do not use.

This means that large, "fat" interfaces should be split into smaller, more specific ones. This way, a class only needs to implement the methods that are relevant to its job.

#### Dependency Injection
**The Principle:** A. High-level modules should not depend on low-level modules. Both should depend on abstractions (e.g., interfaces). B. Abstractions should not depend on details. Details should depend on abstractions.

This principle is about decoupling your code. Instead of a high-level class directly creating and depending on a low-level class, both should depend on a shared interface. This allows you to easily swap out the low-level implementation without changing the high-level class.

---
## In-class Code
```java
package org.example;  
  
  
// factory design pattern  
// paypal, credit card -> payment methods -> pay API  
// expose checkout API for cross function team  
// double vs Double -> primitive vs referenced data type ->  8 primitive data type  
// List<Double> list = new ArraYList();  
// double d = 2.0; -> Double d1 = new Double (d); -> auto boxing  
// list.add(d);    -> list.add(d1);  
// double d = list.get(0);  
// List<double>  
  
  
// IllegalAccessException -> runtime exception vs compile time exception  
// extends RuntimeException vs extend Exception  
// Throwable  
// Exception vs Error  
  
  
  
interface Payment {  
    void pay(double money);  
}  
  
class PayPalPayment implements Payment{  
    public void pay(double money){  
        System.out.println("Pay pal");  
    }  
}  
  
class CreditCardPayment implements Payment{  
    public void pay(double money){  
        System.out.println("credit card payment");  
    }  
}  
  
class PaymentFactory {  
    public static Payment createPayment(String type)  {  
        switch(type.toLowerCase()) {  
            case "paypal": return new PayPalPayment();  
            case "creditcard": return new CreditCardPayment();  
            default:  
                try {  
                    throw new IllegalAccessException("unknown type" + type);  
                } catch (IllegalAccessException e) {  
                    System.out.println(e.getMessage());  
                    // logger.debug();  
                    // loger.info();                } finally {  
  
                }  
        }  
    }  
}  
  
public class PaymentService {  
    public void checkout(double amount, String paymentType) throws IllegalAccessException {  
        Payment payment = PaymentFactory.createPayment(paymentType);  
        payment.pay(amount );  
  
    }  
}
```

---
```java
package org.example.rothur;  
  
/**  
 * // status code ---> HTTP status 100, 200, 300, 400, 500 *                              HR, Sales, Account * Exception status code ----> 101         000 1  --> *                             DEparmetn * *                             HR -> CandadietNotExist 100 *                                   MissCellPhoneInfo 101 *                                   ContractUnSigned  202 *  Exception messages */public class App  
{  
    static class Singleton  
    {  
        private static Singleton instance;  
  
        private Singleton(){  
  
        }  
  
        public static Singleton getInstance(){  
            if(instance == null) {  
                instance = new Singleton();  
            }  
            return instance;  
        }  
    }  
  
    interface Payment {  
        void pay(double money);  
    }  
  
    class PaypalPayment implements Payment {  
        public void pay(double money) {  
            System.out.println("PaypalPayment: " + money);  
        }  
    }  
  
    class CreditCardPayment implements Payment {  
        public void pay(double money) {  
            System.out.println("CreditCardPayment: " + money);  
        }  
    }  
  
    class PaymentFactory {  
        public static Payment getPayment(String type) {  
            if (type.equalsIgnoreCase("paypal")) {  
                return new PaypalPayment();  
            } else if (type.equalsIgnoreCase("creditcard")) {  
                return new CreditCardPayment();  
            } else {  
                return null;  
            }  
        }  
    }  
}  
  
  
/**  
 *  list: *      - ArrayList *      - LinkedList *      - Vector + Stack * *  Queue: *      - PriorityQueue *          -> Comparator vs Comparable * *      - ArrayDeque vs LinkedList as Deque *      - BlockingQueue * *  Set: *      - Hashset -> Hash Collision -> *      - LinkedHashSet *      - TreeSet<Employee> ? Comparator(sorting age) vs Comparable(sorting ID)  
 * * *  Map:   ???? *      - HashMap -> HashTable (Deprecated) -> ConcurrentHashMap -> thread safety mechansim *      - LinkedHashMap *      - TreeMap<Key, Value> -> Sorting based on Key  
 * * *  https://github.com/WeiyuanW/learning_note/blob/main/day%201%20review%20note%20-%20Oct%2014.md * */  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
// 2.35 -> 0.35 ???? -> new BigDecimal("2.3") -> (0.000000001)  
// 10   -> 10 --> 4 -> (0, 1, 2, 3) && ???? (-2, -1, 0, 1)  
// 0000...0000
```
## Related