---
name: java-oop
description: "Knowledge base for Java OOP. Use when learning or applying Java object-oriented programming — classes, objects, constructors, this, method overloading, access control, static/final, inheritance, super, method overriding, dynamic method dispatch, abstract classes — or referencing these concepts. Currently sourced from \"Java: The Complete Reference\" by Herbert Schildt (Ch 6-8); designed to grow with OOP material from other books."
allowed-tools:
  - Read
  - Grep
argument-hint: [topic, keyword, or chapter number e.g. ch07]
---

# Java: The Complete Reference — OOP Core (Ch 6–9)
**Author**: Herbert Schildt | **Pages**: ~95 (4 chapters) | **Chapters**: 6–9 | **Generated**: 2026-06-01

## How to Use This Skill

- **Without arguments** — load the core OOP frameworks below for reference.
- **With a topic** — ask about `overloading`, `inheritance`, `static`, `abstract`, `interfaces`, `packages`, `default methods`, etc.; I read the matching chapter file.
- **With a chapter** — ask for `ch06`, `ch07`, `ch08`, or `ch09` to load that summary.
- **Browse** — ask "what chapters do you have?" for the index.

When you ask about a topic not in the Core section, I read the relevant chapter file before answering.

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

## Chapter Index

| # | Title | Key Frameworks |
|---|-------|----------------|
| [ch06](chapters/ch06-introducing-classes.md) | Introducing Classes | class/object, references, constructors, `this`, garbage collection, encapsulation |
| [ch07](chapters/ch07-closer-look-methods-classes.md) | A Closer Look at Methods and Classes | overloading, argument passing, access control, `static`, `final`, nested/inner classes, `String`, varargs, recursion |
| [ch08](chapters/ch08-inheritance.md) | Inheritance | `extends`, `super`, overriding, dynamic method dispatch, run-time polymorphism, abstract classes, `final`, `Object` |
| [ch09](chapters/ch09-packages-interfaces.md) | Packages and Interfaces | `package`, `CLASSPATH`, `import`, access (Table 9-1), `interface`, `implements`, interface references, default/static/private methods |

## Topic Index

- **abstract class / method** → ch08
- **access control / modifiers** (`private`/`protected`/`public`/default) → ch07; full package matrix (Table 9-1) → ch09
- **argument passing** (call-by-value/reference) → ch07
- **arrays (`.length`)** → ch07
- **class fundamentals** → ch06
- **`CLASSPATH` / finding packages** → ch09
- **command-line arguments** → ch07
- **constructors** → ch06; overloaded → ch07; chaining via `super()` → ch08
- **default / static / private interface methods** → ch09
- **dynamic method dispatch** → ch08; via interface reference → ch09
- **encapsulation** → ch06, ch07
- **`extends` / inheritance** → ch08; interfaces extending interfaces → ch09
- **`final`** → ch07 (constant), ch08 (method/class)
- **garbage collection** → ch06
- **`implements`** → ch09
- **`import` / fully qualified names** → ch09
- **interface** (definition, references, vs abstract class) → ch09
- **interface vs abstract class** → ch08, ch09
- **multiple inheritance (of behavior, conflict rules)** → ch09
- **nested / inner classes** → ch07; nested interfaces → ch09
- **`new` operator** → ch06
- **`Object` class** → ch08
- **overloading (methods/constructors)** → ch07
- **overriding (methods)** → ch08
- **packages** → ch09
- **polymorphism** → ch07 (compile-time), ch08 (run-time), ch09 (interface references)
- **recursion** → ch07
- **references / object variables** → ch06
- **`static`** → ch07; static interface methods → ch09
- **`String` (immutability)** → ch07
- **`super`** → ch08; `Interface.super.method()` → ch09
- **`this`** → ch06
- **type inference (`var`) with reference types** → ch07, ch08
- **varargs (`...`)** → ch07

## Supporting Files

- [glossary.md](glossary.md) — all key terms with definitions
- [patterns.md](patterns.md) — techniques: encapsulation, constructor overloading, super-chaining, dispatch, abstract contracts, varargs, recursion
- [cheatsheet.md](cheatsheet.md) — access table, overload-vs-override, `this`/`super`, `static`/`final` rules, gotchas

---

## Scope & Limits

Covers OOP fundamentals from Chapters 6–9 (classes through packages & interfaces). Exceptions (Ch 10), generics (Ch 14), modules (Ch 16), and the full `String`/library APIs live in later chapters not included here. For hands-on coding, pair this with your project tooling and a JDK.
