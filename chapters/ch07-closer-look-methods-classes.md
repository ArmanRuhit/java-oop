# Chapter 7: A Closer Look at Methods and Classes

## Core Idea
Deepens classes with **overloading** (compile-time polymorphism), the real semantics of **argument passing**, **access control** that finally enforces encapsulation, plus `static`, `final`, nested classes, `String`, and `varargs`.

## Frameworks Introduced
- **Method overloading** — multiple methods, same name, *different parameter lists* (type and/or count).
  - When to use: closely related operations on different data (e.g., `Math.abs` for each numeric type).
  - How: Java picks the version by matching argument type/number; **return type alone cannot distinguish overloads**. If no exact match, Java tries automatic widening conversions (e.g., `int`→`double`).
- **Access control** — `private`, default, `protected`, `public` modifiers gate who can touch a member.
  - When to use: restrict data to method-only access; expose API as `public`, hide internals as `private`.

## Key Concepts
- **Parameter vs argument**: a *parameter* is the variable in the method definition; an *argument* is the value passed at the call site.
- **Call-by-value (primitives)**: a copy is passed; method changes don't affect the caller's variable.
- **Effective call-by-reference (objects)**: the *reference* is passed by value, but the copy points to the same object — so field mutations are visible to the caller.
- **`static`**: member belongs to the class, not any instance; accessible before any object exists, via `Classname.member`.
- **`final` (field)**: a constant; must be initialized at declaration or in a constructor; UPPERCASE by convention.
- **Nested class**: a class defined inside another; scope bounded by the encloser.
- **Inner class** (non-static nested): can access *all* members of the outer class directly.
- **Varargs (`...`)**: variable-length argument list, received as an array.

## Mental Models
- **Overloading = "one interface, multiple methods"** = compile-time polymorphism. The name is the *general action*; the compiler picks the specific version.
- **Java is always call-by-value** — even for objects. The subtlety: the *value* for an object is a reference, so the callee shares the object but can't repoint the caller's variable.
- **`static` = controlled global**: shared single copy of a variable / callable without an object, but namespaced under the class.
- **Inner class sees out, outer can't see in**: inner accesses outer's privates; outer cannot touch inner's members.

## Anti-patterns
- **Overloading unrelated operations** (e.g., `sqr` meaning both square and square-root): defeats overloading's purpose; only overload closely related behavior.
- **Relying on return type to overload**: illegal as a distinguishing factor — won't compile.
- **Varargs ambiguity**: overloads like `vaTest(int...)` and `vaTest(boolean...)` make `vaTest()` ambiguous; `vaTest(int...)` vs `vaTest(int, int...)` make `vaTest(1)` ambiguous. Fix by using distinct method names.
- **Varargs not last / more than one vararg**: `(int... v, boolean f)` and `(int... a, double... b)` are both illegal — the vararg must be the single, last parameter.

## Code Examples
```java
// Overloading: resolved by parameter type/number, NOT return type
void test() { }
void test(int a) { }
void test(int a, int b) { }
double test(double a) { return a * a; }   // int arg with no test(int) -> widens to this
```
- **What it demonstrates**: overload resolution and automatic widening when no exact match exists.

```java
static void vaTest(int ... v) {           // varargs: v is an int[]
  System.out.print("Num args: " + v.length + " -> ");
  for (int x : v) System.out.print(x + " ");
}
// calls: vaTest();  vaTest(1, 2, 3);  vaTest(88);   // 0..n args, auto-boxed into array
```
- **What it demonstrates**: `...` declares a variable-arity method; the param is used as an array; vararg must be last.

## Reference Tables
| Modifier | Same class | Same package | Subclass (other pkg) | World |
|---|:---:|:---:|:---:|:---:|
| `private` | ✓ | ✗ | ✗ | ✗ |
| default (none) | ✓ | ✓ | ✗ | ✗ |
| `protected` | ✓ | ✓ | ✓ | ✗ |
| `public` | ✓ | ✓ | ✓ | ✓ |

Static method restrictions: can only call other `static` methods, can only access `static` variables, and **cannot use `this` or `super`**.

## Key Takeaways
1. Overloading differs by parameter type/number only — never by return type.
2. Primitives pass by value; objects pass a reference *by value* (shared object, mutations visible).
3. `static` members exist without any instance; `main()` is static for exactly this reason.
4. `static {}` initialization block runs once, when the class is loaded.
5. `final` fields are constants; `final` parameters/locals can't be reassigned.
6. Arrays are objects — use `.length` (a field, not a method) for capacity.
7. `String` objects are *immutable*; use `StringBuffer`/`StringBuilder` to mutate.

## Connects To
- **Ch 6**: overloaded constructors generalize the single constructor introduced earlier.
- **Ch 8**: `final` gains two more meanings (prevent overriding, prevent inheritance); access control becomes critical with inheritance via `protected`.
- **Ch 9 (Packages)**: default access = "public within package"; full access table needs packages.
