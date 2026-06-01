# Chapter 8: Inheritance

## Core Idea
Inheritance builds hierarchical classifications: a **subclass** `extends` a **superclass**, reusing its members and adding its own. Combined with **method overriding** + **dynamic method dispatch**, it delivers run-time polymorphism.

## Frameworks Introduced
- **Inheritance via `extends`** — `class B extends A` makes B a specialized A. Java allows only **single inheritance** (one superclass); hierarchies can be multilevel.
- **`super`** — two forms:
  1. `super(arg-list)` calls the immediate superclass constructor; **must be the first statement** in the subclass constructor.
  2. `super.member` accesses a superclass field/method hidden by the subclass.
- **Method overriding** — subclass method with *identical name AND type signature* replaces the superclass version when called on a subclass object. (Different signature = overloading, not overriding.)
- **Dynamic method dispatch** — overridden calls resolved at **run time** by the *object's actual type*, not the reference type. This is run-time polymorphism.
- **Abstract class / method** — `abstract` method has no body and *must* be overridden; a class with any abstract method must be `abstract` and cannot be instantiated.

## Key Concepts
- **Superclass/subclass**: inherited class vs the class doing the inheriting.
- **Superclass reference → subclass object**: legal and central to polymorphism; but only members known to the *reference type* are accessible.
- **Constructor execution order**: always superclass → subclass (order of derivation), with or without explicit `super()`.
- **`Object` class**: the universal superclass; every class is ultimately a subclass of `Object`.
- **Early vs late binding**: `final` calls resolve at compile time (early); normal overridable calls resolve at run time (late).

## Mental Models
- **"It's the object's type, not the reference's type"** — for overridden methods, the *runtime object* decides which version runs. (For field access and which members are *visible*, the reference type decides.)
- **Polymorphism = "one interface, multiple methods"** — superclass defines the general form (`area()`); each subclass supplies its specific implementation.
- **Abstract = "subclasser responsibility"** — the superclass declares *what* must exist; subclasses are forced to provide *how*.
- **Arguments travel "up the line"**: in a multilevel hierarchy, each `super()` calls the closest superclass constructor, cascading initialization from the top down.

## Anti-patterns
- **Re-initializing superclass fields in the subclass**: duplicates code and forces fields to be non-private. Use `super(...)` so the superclass keeps its data private.
- **Expecting overload when you meant override**: if signatures differ, you get overloading and no polymorphism — silent logic bug.
- **Declaring a class both `abstract` and `final`**: illegal — abstract needs subclasses, final forbids them.
- **Assuming `var` infers the runtime type**: type inference uses the *declared/initializer* (superclass) type, not the actual subclass object.

## Code Examples
```java
class Box {
  private double width, height, depth;
  Box(double w, double h, double d) { width=w; height=h; depth=d; }
  double volume() { return width*height*depth; }
}
class BoxWeight extends Box {
  double weight;
  BoxWeight(double w, double h, double d, double m) {
    super(w, h, d);        // call superclass constructor FIRST
    weight = m;            // initialize only what's new
  }
}
```
- **What it demonstrates**: `extends`, `super()` chaining, keeping superclass fields private.

```java
// Dynamic method dispatch: runtime object type chooses the method
A r;
r = new A(); r.callme();   // A's version
r = new B(); r.callme();   // B's version  (B extends A, overrides callme)
r = new C(); r.callme();   // C's version
```
- **What it demonstrates**: one superclass reference `r` invokes different overridden methods based on the actual object.

```java
abstract class Figure {
  double dim1, dim2;
  Figure(double a, double b) { dim1=a; dim2=b; }
  abstract double area();           // no body — subclasser responsibility
}
class Rectangle extends Figure {
  Rectangle(double a, double b) { super(a, b); }
  double area() { return dim1 * dim2; }   // must override
}
```
- **What it demonstrates**: abstract method forces every subclass to implement `area()`; `Figure` can't be instantiated but can hold references.

## Reference Tables
| `final` use | Effect |
|---|---|
| `final` field | named constant (Ch 7) |
| `final` method | cannot be overridden → enables early binding / inlining |
| `final` class | cannot be inherited (implicitly makes all methods final) |

Commonly used `Object` methods: `equals(Object)`, `toString()`, `hashCode()`, `getClass()`*, `clone()`, `finalize()`, `notify()`*, `notifyAll()`*, `wait()`* — (* are `final`, cannot be overridden). `toString()` is auto-called by `println()`.

## Key Takeaways
1. `extends` gives single inheritance; subclass can't access superclass `private` members (use `protected`/`super`).
2. `super(...)` must be the first statement; constructors run superclass-first.
3. Override = same name + same signature; otherwise it's an overload.
4. Dynamic method dispatch resolves overridden calls by the *runtime object type* — the engine of run-time polymorphism.
5. Abstract classes define structure without full implementation and can't be instantiated, but can serve as reference types.
6. `final` prevents overriding (method) or inheritance (class); enables compile-time (early) binding.
7. Every class ultimately extends `Object` — override `toString()`/`equals()` to customize behavior.

## Connects To
- **Ch 6**: extends the `Box` class; constructors now chain instead of re-initializing.
- **Ch 7**: `protected` access modifier and `final`'s first meaning come into play here; overriding vs overloading contrast.
- **Ch 9 (Interfaces)**: interfaces take abstraction further — full "one interface, multiple methods" without implementation/state.
