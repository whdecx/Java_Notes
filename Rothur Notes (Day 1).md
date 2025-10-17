---
aliases: []
tags:
created: 2025-10-14
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
### Access modifier: 
public private  
default (same package) -> protected (default + parent/child access for subclass outside of the package)

### Templates
Class vs interface vs abstract class vs enum vs annotation(@interface)

interface vs abstract class
- can implement multiple interfaces
- extends only one
- is-a vs has-a relationship

### Static
- Scope: field, method, class, block
- Template -> access elements from template instead of instance
- Checks JVM knowledge:
    - stack, heap, PC, ==method area==, native method stack(C/C++ backdoor for os level api)
    - Static things is stored in the method area
- Static modified stuff is only loaded once
- Static class must reside inside another class as an inner class
    - loading template to method area -> you can have static class

### Final
- Scope: field, method, class
- field: the memory address or primary type cannot be modified after initialization (on creation)
- method: the method cannot be override
- class: **prevents inheritance:** A `final` class cannot be extended by any other class.

### Immutable class
- final class
- **private** final field
- getters only, no setter
    - Getters return defensive copies: deep copies or unmodifiable copies
    - **In the constructor:** When receiving a mutable object, create a new copy of it for your internal field. Do not store the reference to the original object that was passed in.

- final method: ==No need for this because it's already a final class==
- examples: String type

### Override v.s. Overload difference
- runtime v.s. compile time polymorphism
- in-between classes vs within same class
- The scope of overriden exception handling can be narrower but cannot be wider
- Overload: **You CANNOT overload a method by changing only its return type.**

| How to Differentiate Methods                      | Is it Overloading? | Example                                         |
| ------------------------------------------------- | ------------------ | ----------------------------------------------- |
| **Different Method Name**                         | No                 | `add()` vs. `sum()`                             |
| **Same Name, Different # of Params**              | **Yes**            | `add(int, int)` vs. `add(int, int, int)`        |
| **Same Name, Different Param Types**              | **Yes**            | `add(int, int)` vs. `add(double, double)`       |
| **Same Name, Different Param Order**              | **Yes**            | `format(String, int)` vs. `format(int, String)` |
| **Same Name, Same Params, Different Return Type** | **No (Error)**     | `int add(int,int)` vs. `double add(int,int)`    |
| **Same Name, Same Params, Different Param Names** | **No (Error)**     | `add(int a, int b)` vs. `add(int x, int y)`     |


### Factory pattern
You need the **Factory Design Pattern** to **decouple the code that _uses_ an object from the code that _creates_ the object.**

This separation makes your code dramatically cleaner, more flexible, and easier to maintain, especially as it grows in complexity.

### throws v.s. throw
The `throw` keyword is used **inside a method body** to immediately create and signal an exception. It performs the action of disrupting the normal flow of the program.

The `throws` keyword is used **in a method signature** to declare the exceptions that the method _might_ throw (but doesn't handle itself). It's a formal contract telling the caller what to expect.
- Let the caller of this method to handle the exception

#### when is finally not executed

| Scenario                           | Does `finally` Execute? | Why?                                                |
| ---------------------------------- | ----------------------- | --------------------------------------------------- |
| **Normal Execution**               | **Yes**                 | Standard program flow.                              |
| **Exception Thrown and Caught**    | **Yes**                 | Standard exception handling.                        |
| **`return` in `try`/`catch`**      | **Yes**                 | `finally` runs before the method exits.             |
| **`System.exit()` is called**      | **No**                  | The entire JVM is halted immediately.               |
| **Infinite loop in `try`/`catch`** | **No**                  | The execution point never reaches `finally`.        |
| **JVM crashes**                    | **No**                  | The execution environment is destroyed.             |
| **Code is in a Daemon thread**     | **No**                  | The JVM exits when all non-daemon threads are done. |


### Runtime v.s. Compile time exception

### Explain concepts
- when to use which
- Prove your experience instead recite facts

### Collection
![[c06899a3fcca6041984c77eeeac70649_MD5.png]]

### Map implementation

## Related