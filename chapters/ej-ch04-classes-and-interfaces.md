# Effective Java — Chapter 4: Classes and Interfaces (Items 15–25)

> Source: *Effective Java, 3rd Edition* by Joshua Bloch. Classes and interfaces are Java's basic units of abstraction; these items make them usable, robust, and flexible.

## Core Idea
Hide implementation aggressively, prefer immutability and composition, and use interfaces (not abstract classes) to define types.

## Items as Principles

### Item 15 — Minimize the accessibility of classes and members
Information hiding (encapsulation) decouples components so they can be built, tested, and changed in isolation. **Rule of thumb: make each class/member as inaccessible as possible.** Top-level classes: package-private unless they need to be public API. Members, in increasing access: `private` → package-private (default) → `protected` → `public`. Default to `private`; loosen only when a same-package class truly needs it. A `protected` member is a forever public commitment. You can't reduce access of an overriding method below the superclass's (Liskov). **Instance fields of public classes should rarely be public** — public mutable fields forfeit invariants and thread safety. Exception: `public static final` constants of primitives/immutables. **A nonzero-length array is mutable** — never expose a `public static final` array (security hole); return an unmodifiable list or a defensive `clone()`.

### Item 16 — In public classes, use accessor methods, not public fields
Public classes must **never expose mutable fields** — use private fields + getters/setters to preserve flexibility, invariants, and the ability to act on access. Exposing **immutable** fields is less harmful but still questionable. Package-private or private nested classes *may* expose fields if it cleanly describes the abstraction (less clutter, scope of change is confined). `java.awt.Point`/`Dimension` are cautionary tales.

### Item 17 — Minimize mutability
Immutable classes are easier to design/use, less error-prone, thread-safe, and freely shareable. **Five rules**: (1) no mutators; (2) prevent extension (make `final`, or private constructors + static factories); (3) all fields `final`; (4) all fields `private`; (5) exclusive access to mutable components (defensive copies). Use the **functional approach** — operations return a new instance (method names are prepositions like `plus`, not verbs). Benefits: simple (one state), thread-safe, share freely + share internals, great map keys/building blocks, free failure atomicity. Cost: a separate object per value (multistep ops create garbage — mitigate with package-private or public mutable companion, e.g. `String`→`StringBuilder`). **Default stance: make every field `private final`** unless there's a compelling reason; classes should be immutable unless there's good reason not to.

### Item 18 — Favor composition over inheritance
Implementation inheritance across package boundaries is fragile: a subclass depends on the superclass's implementation details, which can change between releases (the `InstrumentedHashSet` double-counts because `HashSet.addAll` self-calls `add`). Superclasses can also acquire new methods that bypass your overrides. **Solution: composition + forwarding** — give the new class a private field referencing an instance of the existing class, and forward each method (a **wrapper class** / **Decorator**). Robust, flexible (works with any `Set` impl), reusable forwarding class. Caveat: not for callback frameworks (the **SELF problem**). Use inheritance only for a genuine **is-a** subtype relationship in the same package or a class designed for extension; `Stack extends Vector` and `Properties extends Hashtable` are violations.

### Item 19 — Design and document for inheritance, or else prohibit it
A class designed for inheritance must **document its self-use** of overridable methods (the `@implSpec` Javadoc tag / "Implementation Requirements"), expose judiciously chosen `protected` hooks (test by writing ~3 subclasses, one by an outsider), and **constructors must not invoke overridable methods** (the override runs before subclass init — observes `null`/fails). Same restriction for `clone`/`readObject`. Designing for inheritance is a permanent commitment. **Otherwise prohibit subclassing**: make the class `final`, or give it only private/package-private constructors + static factories. Eliminate self-use mechanically by routing each overridable method through a private helper.

### Item 20 — Prefer interfaces to abstract classes
Interfaces allow multiple implementations without forcing a place in the hierarchy: existing classes can be **retrofitted**, they define **mixins** (`Comparable`), and they enable **nonhierarchical type frameworks** (avoid combinatorial explosion of 2ⁿ classes). Abstract classes can't — single inheritance constrains them. Provide `default` methods where one method has an obvious implementation atop others. Best of both worlds: an **abstract skeletal implementation** (`AbstractCollection`, `AbstractList`, `AbstractMap`) — the **Template Method** pattern: interface defines the type + default methods; the skeletal class implements the rest atop the primitives. A class that can't extend it can still implement the interface directly or forward to a private inner class extending the skeleton (**simulated multiple inheritance**).

### Item 21 — Design interfaces for posterity
`default` methods (Java 8) let you add methods to existing interfaces, but they're **injected without implementers' consent** and can't maintain every implementation's invariants (e.g. `removeIf` breaks Apache's `SynchronizedCollection` — no locking). Use them to provide standard implementations **at interface creation**, not to casually evolve a released interface. You still can't remove methods or change signatures. Test each new interface with ≥3 diverse implementations and multiple clients before release.

### Item 22 — Use interfaces only to define types
An interface should say what a client can *do* with instances. **The constant interface antipattern** (an interface of only `static final` constants, implemented to import names) leaks an implementation detail into the API and pollutes subclass namespaces. Export constants instead via the relevant class/interface, an `enum`, or a **noninstantiable utility class** (private constructor), using `static import` if heavily used.

### Item 23 — Prefer class hierarchies to tagged classes
A **tagged class** (a tag field + `switch` selecting flavor-specific fields/behavior) is verbose, error-prone, memory-wasteful, and can't be made `final`. Replace it with a **class hierarchy**: an abstract root with an abstract method per tag-dependent behavior, plus a concrete subclass per flavor. The compiler enforces completeness, fields are `final`, types reflect flavors, and the hierarchy models natural relationships (e.g. `Square extends Rectangle`).

### Item 24 — Favor static member classes over nonstatic
Four nested-class kinds: **static member**, **nonstatic member**, **anonymous**, **local** (last three are *inner classes*). A nonstatic member class holds a hidden reference to its enclosing instance — costs time/space and can cause **memory leaks** (the enclosing instance can't be GC'd). **If a member class doesn't need the enclosing instance, make it `static`.** Use nonstatic only for Adapters/views needing the outer instance. Anonymous classes: short (~≤10 lines), declared+instantiated at one point, no `instanceof`/name (largely superseded by lambdas, Item 42). Local classes: rare, like local variables with a name.

### Item 25 — Limit source files to a single top-level class
Multiple top-level classes in one file gives no benefit and risks **definition collisions whose resolution depends on compilation order** (`pancake` vs `potpie`). Put each top-level class in its own file; if subservient, use **static member classes** (Item 24) instead.

## Code Examples

```java
// Item 18 — composition + forwarding (wrapper / Decorator)
public class InstrumentedSet<E> extends ForwardingSet<E> {
    private int addCount = 0;
    public InstrumentedSet(Set<E> s) { super(s); }
    @Override public boolean add(E e) { addCount++; return super.add(e); }
    @Override public boolean addAll(Collection<? extends E> c) {
        addCount += c.size(); return super.addAll(c);
    }
    public int getAddCount() { return addCount; }
}
public class ForwardingSet<E> implements Set<E> {
    private final Set<E> s;                       // composition
    public ForwardingSet(Set<E> s) { this.s = s; }
    public boolean add(E e) { return s.add(e); }  // forwarding
    // ... forward every Set method to s ...
}
```

```java
// Item 23 — class hierarchy replaces a tagged class
abstract class Figure { abstract double area(); }
class Circle extends Figure {
    final double radius;
    Circle(double radius) { this.radius = radius; }
    @Override double area() { return Math.PI * (radius * radius); }
}
class Rectangle extends Figure {
    final double length, width;
    Rectangle(double l, double w) { length = l; width = w; }
    @Override double area() { return length * width; }
}
```

## Anti-patterns
- **Public mutable fields**; **`public static final` arrays** (security hole).
- **Cross-package implementation inheritance** of classes not designed for it → fragile.
- **Constructor (or `clone`/`readObject`) calling an overridable method**.
- **Constant interface** — use a utility class/enum instead.
- **Tagged classes** — use a hierarchy.
- **Nonstatic member class** where no enclosing reference is needed → wasted space + memory leak.
- **Multiple top-level classes per source file**.

## Key Takeaways
1. Make everything as inaccessible as possible; public classes expose only `public static final` immutable constants.
2. Default to **`private final`** fields and **immutable** classes (the five rules + functional methods).
3. **Compose, don't inherit** — wrapper classes are more robust and flexible than subclasses; inherit only for true is-a in a controlled/designed-for-extension setting.
4. Use **interfaces** to define types (mixins, nonhierarchical, retrofit) and pair them with **skeletal implementations**; design interfaces carefully — `default` methods don't make evolution safe.
5. Replace tagged classes with hierarchies; make nested classes `static` unless they need the enclosing instance; one top-level class per file.

## Connects To
- **Item 18 ↔ Items 10/14 (Ch 3)**: composition is the fix for the `equals`/`compareTo` extension problem.
- **Item 20/22**: interfaces vs abstract classes — extends Schildt **Ch 9** (Packages & Interfaces) with the "prefer interfaces" rationale.
- **Item 17**: immutability builds on `final` (Schildt **Ch 7–8**) and underpins thread safety and safe sharing.
- **Item 24**: nested/inner classes extend Schildt **Ch 7**'s nested-class basics with the static-vs-nonstatic decision.
