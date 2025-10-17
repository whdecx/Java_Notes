---
aliases: []
tags:
created: 2025-10-15
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
### Comparator v.s. Comparable

| **Feature**           | **Comparable**                                                                                                                                             | **Comparator**                                                                         |
| --------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------- |
| ==**Purpose**==       | ==Defines the **single, natural ordering** for a class that the class can compare object with itself implement by implement this interface on the class.== | ==Defines **multiple, custom orderings** for a class through a lambda expression.==    |
| **Package**           | `java.lang`                                                                                                                                                | `java.util`                                                                            |
| **Method Name**       | `compareTo(Object obj)`                                                                                                                                    | `compare(Object obj1, Object obj2)`                                                    |
| **Implementation**    | Implemented **inside the class** itself.                                                                                                                   | Implemented in a **separate class** or as a lambda.                                    |
| **# of Sort Orders**  | Provides only **one** way to sort.                                                                                                                         | Provides **many** different ways to sort.                                              |
| **Code Modification** | Requires you to be able to **modify the class** you are sorting.                                                                                           | **Does not** require modifying the class; can sort third-party classes.                |
| **Primary Use Case**  | Used by `Collections.sort(list)` and `Arrays.sort(array)`.                                                                                                 | Passed as an argument to `sort()` methods, e.g., `Collections.sort(list, comparator)`. |

### Collection
![image](Attachments/c06899a3fcca6041984c77eeeac70649_MD5.png)

#### Comparison among `Set` Implementations

| Feature                | `HashSet`                                | `LinkedHashSet`                              | `TreeSet`                                     |
| ---------------------- | ---------------------------------------- | -------------------------------------------- | --------------------------------------------- |
| **Ordering**           | **Unordered** (no guarantee)             | **Insertion Order** (predictable)            | **Sorted Order** (natural or by `Comparator`) |
| **Performance**        | **O(1)** for `add`, `remove`, `contains` | **O(1)** for `add`, `remove`, `contains`     | **O(log n)** for `add`, `remove`, `contains`  |
| **Internal Structure** | Hash Table                               | Hash Table + Doubly-Linked List              | Red-Black Tree                                |
| **`null` values?**     | Allows one `null`                        | Allows one `null`                            | Does not allow `null` (by default)            |
| **Best For**           | Maximum speed when order doesn't matter. | Uniqueness with predictable insertion order. | Uniqueness with automatic sorting.            |

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


### SOLID
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