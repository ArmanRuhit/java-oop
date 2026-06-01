---
name: java-oop
description: "Knowledge base for Java OOP from two books. Use when learning or applying Java object-oriented programming — classes, objects, constructors, this, overloading, access control, static/final, inheritance, super, overriding, dynamic dispatch, abstract classes, interfaces, packages (Schildt) AND best practices: equals/hashCode/toString/clone/Comparable contracts, immutability, composition over inheritance, interfaces vs abstract classes, minimizing accessibility, nested classes (Bloch, Effective Java) — or referencing these concepts. Designed to grow with OOP material from other books."
allowed-tools:
  - Read
  - Grep
argument-hint: [topic, keyword, chapter (ch07), or EJ item (item18)]
---

# Java OOP — Multi-Book Knowledge Base
**Sources**: Herbert Schildt, *Java: The Complete Reference* (Ch 6–9, fundamentals) · Joshua Bloch, *Effective Java 3rd Ed.* (Ch 3–4, Items 10–25, best practices) | **Generated**: 2026-06-01

## How to Use This Skill

- **Without arguments** — load the core OOP frameworks below for reference.
- **With a topic** — ask about `overloading`, `inheritance`, `static`, `abstract`, `interfaces`, `packages`, `equals`, `hashCode`, `immutability`, `composition`, `nested classes`, etc.; I read the matching chapter file.
- **With a chapter/item** — ask for `ch06`–`ch09` (Schildt) or `ej-ch03`/`ej-ch04` / an item like `item18` (Bloch).
- **Browse** — ask "what chapters do you have?" for the index.

This skill spans two books: **Schildt = the mechanics** (how Java OOP works), **Bloch = the judgment** (how to use it well). When you ask about a topic not in the Core section, I read the relevant chapter file before answering.

---

## Core Frameworks & Mental Models

**Class vs Object (Ch 6).** A *class* is a logical template defining a new data type (fields + methods); an *object* is a physical instance in memory. Object creation is two steps: declare a reference, then `new` to allocate. A class variable holds a *reference* (address), never the object — so `b2 = b1` copies the reference, and both names mutate the same object.

**Encapsulation as a "data engine" (Ch 6–7).** Hide fields (`private`), expose behavior through methods. Callers operate the engine through its controls without knowing the internals — so you can change internals freely as long as the method interface holds. Access control (Ch 7) is what finally *enforces* this:
- `private` = same class only · default = same package · `protected` = package + subclasses · `public` = everywhere.

**Constructors & `this` (Ch 6).** A constructor shares the class name, has no return type, and initializes the object. Define any constructor and Java drops the default one. `this` references the invoking object and resolves instance-variable hiding (`this.width = width`).

**Polymorphism = "one interface, multiple methods".** Two forms:
- **Overloading (Ch 7, compile-time)** — same name, *different parameter lists*. Resolved by argument type/number; return type alone can't distinguish. If no exact match, Java widens (e.g., `int`→`double`).
- **Overriding + dynamic method dispatch (Ch 8, run-time)** — subclass method with *identical name + signature*; the call is resolved by the **object's runtime type, not the reference type**. This is run-time polymorphism, the engine of OOP design.

**Argument passing (Ch 7).** Java is *always* call-by-value. Primitives → a copy (caller unaffected). Objects → the *reference* is copied, so the object is shared and field mutations are visible, but you can't repoint the caller's variable.

**`static` and `final` (Ch 7–8).**
- `static` = belongs to the class, usable without an instance (`Classname.member`); static methods can't use `this`/`super` or touch instance members; `static {}` runs once at load.
- `final` has three meanings: constant field · un-overridable method (enables early binding/inlining) · un-inheritable class. `abstract` + `final` is illegal.

**Inheritance & `super` (Ch 8).** `class B extends A` — single inheritance only; subclass can't see superclass `private` members. `super(args)` calls the superclass constructor and **must be the first statement**; constructors execute superclass→subclass. `super.member` reaches a hidden superclass member. A superclass reference can point to a subclass object, but only reference-type members are visible.

**Abstraction (Ch 8).** An `abstract` method has no body and is "subclasser responsibility" — every concrete subclass must override it. A class with any abstract method must be `abstract`, can't be instantiated, but can serve as a reference type for polymorphism. Every class ultimately extends `Object`; override `toString()`/`equals()` to customize.

**Packages (Ch 9).** `package pkg;` (first statement) compartmentalizes the namespace *and* controls visibility; the package hierarchy mirrors the filesystem. Packages complete the access model — **default access = visible to subclasses + same-package classes** (see Table 9-1 in ch09). `import` is convenience only; `java.lang` is implicit; fully-qualified names always work.

**Interfaces (Ch 9).** `interface` fully separates *what* from *how*: method signatures only (implicitly `public abstract`), constants implicitly `public static final`. A class can `implements` *many* interfaces (vs one superclass) — Java's answer to multiple inheritance of **type/behavior, not state** (interfaces hold no instance variables). An **interface reference** dispatches dynamically at run time across *unrelated* classes — the strongest run-time polymorphism. JDK 8 added `default` (give a method a body to evolve interfaces without breaking implementers) + `static` methods; JDK 9 added `private` helpers. Default-method conflicts resolve: class wins > sub-interface wins > else error; `Interface.super.method()` selects explicitly.

**Use these when**: designing a class (start `private`, add methods) · choosing overload vs override (same class+different params = overload; subclass+same signature = override) · deciding `static` (no instance needed) · forcing subclass contracts (`abstract`) · locking a design (`final`) · choosing **interface vs abstract class** (need multiple inheritance or unrelated implementers → interface; need shared state → abstract class) · organizing code into **packages** for namespace + visibility control.

---

## Effective Java Best Practices (Bloch, Ch 3–4 · Items 10–25)

Where Schildt teaches *the mechanics*, Bloch teaches *the judgment* — when each feature helps and how it backfires.

**Object-method contracts are load-bearing (Items 10–14).** `equals`, `hashCode`, `toString`, `clone`, `compareTo` have **general contracts** that `HashMap`/`HashSet`/`TreeSet` and generic algorithms depend on:
- **`equals` (Item 10)** — override only for **value classes** with logical equality. Be reflexive/symmetric/transitive/consistent; never make it interoperate with other types (breaks symmetry). *No way to add a value component by subclassing and keep the contract* → use composition.
- **`hashCode` (Item 11)** — **always** override alongside `equals`; equal objects must hash equal. Recipe: `result = 31 * result + c` over significant fields (`31` = odd prime). `Objects.hash(...)` = concise but slower.
- **`toString` (Item 12)** — override in every instantiable class; return the interesting state, provide accessors for it.
- **`clone` (Item 13)** — `Cloneable` is broken; prefer a **copy constructor / copy factory**.
- **`Comparable` (Item 14)** — implement for value classes with a natural ordering; build comparators with `Comparator.comparing*`, never `<`/`>` or `a - b`.

**Minimize accessibility (Items 15–16).** Make every class/member as inaccessible as possible; default fields to **`private`**. Public classes expose **no mutable fields** — only `public static final` constants of immutables (never a `public static final` array). Use accessor methods.

**Minimize mutability (Item 17).** Prefer **immutable** classes — 5 rules: no mutators · prevent extension · all fields `final` · all fields `private` · exclusive access to mutable components. Use the **functional approach** (return new instances; method names are prepositions like `plus`). Immutable = simple, thread-safe, freely shareable, great map keys. Default stance: **every field `private final`** unless proven otherwise.

**Favor composition over inheritance (Items 18–19).** Implementation inheritance across packages is fragile (subclass binds to superclass internals; `InstrumentedHashSet` double-counts). Prefer a **wrapper class** (composition + forwarding = Decorator). Inherit only for a true **is-a** in code you control or that's *designed and documented for inheritance* — and such a class must document self-use and never call overridable methods from its constructor. Otherwise make the class `final`.

**Prefer interfaces to abstract classes (Items 20–22).** Interfaces define types, allow retrofitting, **mixins**, and nonhierarchical frameworks; pair with an abstract **skeletal implementation** (`AbstractList` = Template Method). `default` methods help at interface *creation*, not for safely evolving a released interface (Item 21). Use interfaces **only to define types** — never the **constant interface antipattern** (use a utility class/enum, Item 22).

**Structure (Items 23–25).** Replace **tagged classes** with class hierarchies. Make nested classes **`static`** unless they need the enclosing instance (nonstatic ones risk memory leaks). One top-level class per source file.

**Use these when**: writing `equals`/`hashCode` (always together, on significant fields) · deciding to subclass (prefer composition/wrapper unless true is-a) · choosing mutable vs immutable (default immutable) · exposing API surface (least accessible) · adding behavior to a type (interface + skeletal impl) · representing variants (hierarchy, not tag field).

---

## Chapter Index

### Schildt — *Java: The Complete Reference* (fundamentals)
| # | Title | Key Frameworks |
|---|-------|----------------|
| [ch06](chapters/ch06-introducing-classes.md) | Introducing Classes | class/object, references, constructors, `this`, garbage collection, encapsulation |
| [ch07](chapters/ch07-closer-look-methods-classes.md) | A Closer Look at Methods and Classes | overloading, argument passing, access control, `static`, `final`, nested/inner classes, `String`, varargs, recursion |
| [ch08](chapters/ch08-inheritance.md) | Inheritance | `extends`, `super`, overriding, dynamic method dispatch, run-time polymorphism, abstract classes, `final`, `Object` |
| [ch09](chapters/ch09-packages-interfaces.md) | Packages and Interfaces | `package`, `CLASSPATH`, `import`, access (Table 9-1), `interface`, `implements`, interface references, default/static/private methods |

### Bloch — *Effective Java 3rd Ed.* (best practices)
| # | Title | Items & Key Practices |
|---|-------|----------------|
| [ej-ch03](chapters/ej-ch03-methods-common-to-all-objects.md) | Methods Common to All Objects | Items 10–14: `equals`/`hashCode`/`toString`/`clone`/`Comparable` contracts, copy constructors, comparator construction methods |
| [ej-ch04](chapters/ej-ch04-classes-and-interfaces.md) | Classes and Interfaces | Items 15–25: minimize accessibility, accessor methods, immutability, composition over inheritance, design for inheritance, interfaces vs abstract classes, skeletal impl, tagged classes→hierarchies, static nested classes |

## Topic Index

- **abstract class / method** → ch08; vs interfaces, skeletal implementation → ej-ch04 (Item 20)
- **accessibility / minimize access** → ej-ch04 (Item 15); accessor methods vs public fields → ej-ch04 (Item 16)
- **access control / modifiers** (`private`/`protected`/`public`/default) → ch07; full package matrix (Table 9-1) → ch09; minimize → ej-ch04 (Item 15)
- **anonymous / local classes** → ej-ch04 (Item 24)
- **argument passing** (call-by-value/reference) → ch07
- **arrays (`.length`)** → ch07; never expose `public static final` array → ej-ch04 (Item 15)
- **class fundamentals** → ch06
- **class hierarchy vs tagged class** → ej-ch04 (Item 23)
- **`CLASSPATH` / finding packages** → ch09
- **`clone` / `Cloneable` / copy constructor** → ej-ch03 (Item 13)
- **command-line arguments** → ch07
- **`Comparable` / `compareTo` / `Comparator`** → ej-ch03 (Item 14)
- **composition over inheritance / wrapper / forwarding / Decorator** → ej-ch04 (Item 18)
- **constant interface (antipattern)** → ej-ch04 (Item 22); see also ch09
- **constructors** → ch06; overloaded → ch07; chaining via `super()` → ch08; must not call overridable methods → ej-ch04 (Item 19)
- **default / static / private interface methods** → ch09; designing interfaces for posterity → ej-ch04 (Item 21)
- **design/document for inheritance (or prohibit it)** → ej-ch04 (Item 19)
- **dynamic method dispatch** → ch08; via interface reference → ch09
- **encapsulation / information hiding** → ch06, ch07; minimize accessibility → ej-ch04 (Item 15)
- **`equals` (general contract)** → ej-ch03 (Item 10)
- **`extends` / inheritance** → ch08; interfaces extending interfaces → ch09; prefer composition → ej-ch04 (Item 18)
- **`final`** → ch07 (constant), ch08 (method/class); `private final` fields default → ej-ch04 (Items 15, 17)
- **functional approach (return new instance)** → ej-ch04 (Item 17)
- **garbage collection** → ch06; nonstatic inner class memory leak → ej-ch04 (Item 24)
- **`hashCode` (recipe, contract)** → ej-ch03 (Item 11)
- **`implements`** → ch09
- **immutability / immutable classes (5 rules)** → ej-ch04 (Item 17)
- **`import` / fully qualified names** → ch09; static import for constants → ej-ch04 (Item 22)
- **interface** (definition, references, vs abstract class) → ch09; **prefer interfaces to abstract classes** → ej-ch04 (Item 20); use only to define types → ej-ch04 (Item 22)
- **interface vs abstract class** → ch08, ch09, ej-ch04 (Item 20)
- **mixin** → ej-ch04 (Item 20)
- **multiple inheritance (of behavior, conflict rules)** → ch09; simulated (skeletal + forwarding) → ej-ch04 (Item 20)
- **nested / inner classes** → ch07; nested interfaces → ch09; static vs nonstatic member classes → ej-ch04 (Item 24)
- **`new` operator** → ch06
- **`Object` class** → ch08; overriding its methods correctly → ej-ch03 (Items 10–14)
- **overloading (methods/constructors)** → ch07
- **overriding (methods)** → ch08; Object methods → ej-ch03
- **packages** → ch09
- **polymorphism** → ch07 (compile-time), ch08 (run-time), ch09 (interface references)
- **recursion** → ch07
- **references / object variables** → ch06
- **skeletal implementation / Template Method** → ej-ch04 (Item 20)
- **single top-level class per file** → ej-ch04 (Item 25)
- **`static`** → ch07; static interface methods → ch09; static member classes → ej-ch04 (Item 24)
- **`String` (immutability)** → ch07; as immutable-class exemplar → ej-ch04 (Item 17)
- **`super`** → ch08; `Interface.super.method()` → ch09
- **tagged class → hierarchy** → ej-ch04 (Item 23)
- **`this`** → ch06
- **`toString` (override)** → ej-ch03 (Item 12)
- **type inference (`var`) with reference types** → ch07, ch08
- **value class** → ej-ch03 (Items 10, 14)
- **varargs (`...`)** → ch07
- **wrapper class (composition + forwarding)** → ej-ch04 (Item 18)

## Supporting Files

- [glossary.md](glossary.md) — all key terms with definitions (both books)
- [patterns.md](patterns.md) — techniques: encapsulation, constructor overloading, super-chaining, dispatch, abstract contracts, varargs, recursion + EJ patterns (equals/hashCode recipes, immutability, wrapper/forwarding, skeletal impl, copy constructor)
- [cheatsheet.md](cheatsheet.md) — access table, overload-vs-override, `this`/`super`, `static`/`final` rules, gotchas + EJ quick rules (Object-method contracts, immutability rules, composition-vs-inheritance)

---

## Scope & Limits

**Schildt (Ch 6–9):** OOP fundamentals — classes through packages & interfaces. Exceptions (Ch 10), generics (Ch 14), modules (Ch 16), and full library APIs are in chapters not included here.

**Bloch (Ch 3–4, Items 10–25):** best practices for object methods and class/interface design. The rest of *Effective Java* — creating/destroying objects (Items 1–9), generics (Ch 5), lambdas/streams (Ch 7), and more — is not yet included; this skill is designed to grow as those chapters are added. For hands-on coding, pair this with your project tooling and a JDK.
