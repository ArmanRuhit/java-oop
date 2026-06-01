# Patterns & Techniques — Java OOP (Chapters 6–9)

## Encapsulation (data + controlled interface)
**When to use**: any class whose internal data should not be tampered with from outside.
**How**: declare fields `private`; expose behavior through `public` methods (getters/setters or richer operations). The Stack class evolves from public `stck`/`tos` → `private`, accessible only via `push()`/`pop()`.
**Trade-offs**: slight verbosity; in exchange you can change internals freely without breaking callers. You *can* leave a field public when there's good reason, but rarely should.

## Two-step object creation
**When to use**: every time you instantiate.
**How**: `ClassName ref;` declares a reference; `ref = new ClassName(args);` allocates and binds. Often combined: `ClassName ref = new ClassName(args);`.
**Trade-offs**: separating the steps clarifies that the reference and the object are distinct (label vs thing).

## Constructor overloading
**When to use**: objects can be built from different data (no args, one value for all dims, full dims, or copied from another object).
**How**: define multiple constructors with different parameter lists; Java picks the one matching the `new` arguments. Include a copy constructor `Box(Box ob)`.
**Trade-offs**: more constructors to maintain; greatly improves call-site convenience. Norm for real-world classes.

## `this` to resolve instance-variable hiding
**When to use**: a parameter/local legitimately shares a field's name.
**How**: `this.field = field;` inside the constructor/method.
**Trade-offs**: improves naming clarity but can confuse; some teams avoid name collisions entirely. Matter of taste.

## `super()` constructor chaining
**When to use**: a subclass must initialize inherited (often `private`) superclass fields.
**How**: `super(args)` as the *first* statement of the subclass constructor; cascades up multilevel hierarchies. Each subclass passes required args "up the line."
**Trade-offs**: avoids duplicated initialization and lets the superclass keep fields private; constraint is it must come first.

## `super.member` to reach a hidden superclass member
**When to use**: subclass field/method hides a superclass one you still need.
**How**: `super.show()` or `super.i`.
**Trade-offs**: lets overriding method extend (not just replace) superclass behavior.

## Method overriding + dynamic dispatch (run-time polymorphism)
**When to use**: a general superclass operation needs type-specific behavior (`Figure.area()` → `Rectangle`/`Triangle`).
**How**: subclass defines a method with identical name+signature; call it through a superclass reference; the runtime object type selects the version.
**Trade-offs**: one clean interface across many types; the cost is late binding (resolved at run time). Mismatched signatures silently become overloads.

## Abstract class as a contract
**When to use**: a superclass defines structure but cannot meaningfully implement some method.
**How**: mark the method `abstract` (no body) and the class `abstract`; subclasses must override or themselves be abstract. Can't instantiate, but can hold references for polymorphism.
**Trade-offs**: guarantees subclasses implement required methods; forfeits direct instantiation.

## `static` for class-level state/behavior
**When to use**: data/behavior independent of any instance (counters, factory helpers, `main`).
**How**: `static` field shared by all instances; `static` method called as `Classname.method()`; `static {}` block for one-time load-time init.
**Trade-offs**: shared global-like state; static methods can't use `this`/`super` or touch instance members.

## Varargs for flexible arity
**When to use**: a method takes an unknown/variable number of same-typed args (printf-style).
**How**: `type ... name` as the last parameter; treat `name` as an array.
**Trade-offs**: cleaner than manual array packing; risk of overload ambiguity — keep the vararg last and singular, avoid ambiguous overloads.

## Recursion with a base case
**When to use**: problems naturally defined in terms of themselves (factorial, QuickSort, tree/AI algorithms).
**How**: method calls itself on a smaller input; an `if` base case forces return.
**Trade-offs**: clearer than iterative equivalents for some algorithms; slower (call overhead) and can overflow the stack if unbounded.

## `final` to lock behavior
**When to use**: constants; methods that must not be overridden; classes that must not be subclassed.
**How**: `final` on field/parameter/method/class.
**Trade-offs**: safety + possible inlining/early binding performance gains; reduces extensibility (can't be both `abstract` and `final`).

## Program to an interface (interface-reference dispatch)
**When to use**: multiple interchangeable implementations of one contract (`IntStack` → `FixedStack`/`DynStack`; storage as array/list/tree).
**How**: define an `interface`; have classes `implements` it; hold objects in an *interface-type* reference; calls resolve at run time. Caller needs no knowledge of the concrete class.
**Trade-offs**: strongest run-time polymorphism + lets unrelated classes share a contract and lets callees be written later; the reference sees only interface methods, not implementation extras.

## Package for namespace + visibility control
**When to use**: any real application; to let same-package classes share default-access members without exposing them.
**How**: `package pkg;` as the first statement; mirror the name in the directory tree; locate via current dir / `CLASSPATH` / `-classpath`; `import` for short names.
**Trade-offs**: avoids name collisions and gives fine-grained access (Table 9-1); requires filesystem layout to match and renaming a package means renaming its directory.

## Evolve an interface with a default method
**When to use**: add a method to a widely-implemented interface without breaking existing implementers, or make a method optional.
**How**: `default returnType m() { ... }` supplies a body used when an implementer doesn't override it. Real code should throw `UnsupportedOperationException` for unsupported optional ops.
**Trade-offs**: graceful evolution + optional behavior; gives *limited* multiple inheritance of behavior (not state) — beware default-method name conflicts (resolve: class wins > sub-interface wins > else error; or `Interface.super.method()`).

## Private interface method to share default-method code
**When to use**: two or more default methods in one interface need a common helper.
**How**: declare a `private` method (JDK 9) callable only by that interface's default/private methods.
**Trade-offs**: removes duplication without widening the public API; not inherited and unusable outside the interface — rarely needed.

## Interface vs abstract class (decision)
**When to use**: choosing the abstraction mechanism.
**How**: pick **interface** when unrelated classes must share a contract or a class needs multiple supertypes (multiple `implements`); pick **abstract class** when you need shared *state* (instance variables) or partial concrete implementation in one hierarchy.
**Trade-offs**: interfaces can't hold state and a class has only one superclass; abstract classes can't be multiply inherited.
