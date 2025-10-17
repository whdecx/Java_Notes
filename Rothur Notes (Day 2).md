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
![[c06899a3fcca6041984c77eeeac70649_MD5.png]]

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

## Related