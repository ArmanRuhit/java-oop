# Glossary — Java OOP

> Schildt terms (Ch 6–9) below; Effective Java terms (Bloch, Items 10–25) at the end.

**Abstract class** — a class with one or more abstract methods; cannot be instantiated, but can be a reference type (Ch 8).
**Abstract method** — a method declared with no body (`abstract type name(params);`) that every concrete subclass must override; "subclasser responsibility" (Ch 8).
**Access modifier** — `private`, default, `protected`, `public`; controls who can access a member (Ch 7).
**Argument** — a value passed to a method at the call site (Ch 7).
**Arity** — number of arguments a method takes; a varargs method is "variable-arity" (Ch 7).
**Call-by-reference** — passing a reference so callee changes affect the caller's argument; Java simulates this for objects (Ch 7).
**Call-by-value** — passing a copy of the value; Java uses this for *all* arguments, including object references (Ch 7).
**Class** — a template defining a new data type from data + code; a logical construct (Ch 6).
**CLASSPATH** — env var (or `-classpath`) telling the runtime where to find packages; defaults to current dir (Ch 9).
**Command-line argument** — string passed to `main(String[] args)` after the program name (Ch 7).
**Constant interface** — interface holding only `static final` constants, implemented to import them; controversial technique (Ch 9).
**Constructor** — same-named, return-type-less method that initializes a new object (Ch 6).
**Default access** — no modifier; member visible to subclasses and other classes in the *same package* (Ch 9).
**Default constructor** — compiler-supplied no-arg constructor used only when no constructor is defined; sets fields to 0/null/false (Ch 6).
**Default method** — interface method with a body (JDK 8, keyword `default`); lets interfaces evolve without breaking implementers (Ch 9).
**Dot operator (`.`)** — links an object/class name to a member; formally a separator (Ch 6).
**Dynamic method dispatch** — run-time resolution of an overridden call based on the object's actual type; basis of run-time polymorphism (Ch 8).
**Early binding** — call resolved at compile time (e.g., `final` methods) (Ch 8).
**Encapsulation** — binding data with the code that manipulates it, plus access control (Ch 6–7).
**`extends`** — keyword that makes a class inherit a superclass (Ch 8).
**`final`** — constant field; un-overridable method; un-inheritable class (Ch 7–8).
**Fully qualified name** — full package path + class name, e.g. `java.util.Date` (Ch 9).
**Garbage collection** — automatic reclamation of unreferenced objects' memory (Ch 6).
**`implements`** — clause by which a class provides the methods required by one or more interfaces (Ch 9).
**`import`** — optional convenience to refer to a packaged class by short name; `java.lang.*` is implicit (Ch 9).
**Inheritance** — deriving a specialized subclass from a general superclass (Ch 8).
**Inner class** — a non-static nested class with direct access to all outer-class members (Ch 7).
**`interface`** — a contract of method signatures (implicitly `public abstract`) + constants (implicitly `public static final`); separates *what* from *how* (Ch 9).
**Interface reference** — a variable of interface type pointing to any implementing object; calls dispatch dynamically at run time (Ch 9).
**Instance** — an object; a concrete realization of a class (Ch 6).
**Instance variable** — per-object data field (Ch 6).
**Late binding** — call resolved at run time (normal overridable methods) (Ch 8).
**Member** — a class's variables and methods collectively (Ch 6).
**Method** — code that operates on a class's data; defines its interface (Ch 6).
**Method overloading** — multiple same-named methods with different parameter lists; compile-time polymorphism (Ch 7).
**Method overriding** — subclass method with identical name+signature replacing the superclass version (Ch 8).
**Multiple inheritance (of behavior)** — a class implementing several interfaces inherits each one's default methods; conflicts resolve by class>sub-interface>error rules (Ch 9). Java has no multiple inheritance of *state*.
**Nested (member) interface** — an interface declared inside a class/interface; can be `public`/`private`/`protected`; referenced as `Outer.NestedIF` (Ch 9).
**Nested class** — a class defined within another class (static or inner) (Ch 7).
**`new`** — operator that dynamically allocates an object and returns a reference (Ch 6).
**`Object`** — the universal superclass of every Java class (Ch 8).
**Object** — an instance of a class; has physical reality in memory (Ch 6).
**Package** — `package pkg;` namespace + visibility container; mirrors a filesystem directory hierarchy (Ch 9).
**Parameter** — a variable in a method definition that receives an argument (Ch 7).
**Partial implementation** — a class that doesn't implement all interface methods must be declared `abstract` (Ch 9).
**Private interface method** — JDK 9 helper callable only by default/private methods of the same interface; not inherited (Ch 9).
**Polymorphism** — "one interface, multiple methods"; compile-time (overloading) or run-time (overriding) (Ch 7–8).
**`private`** — accessible only within the same class (Ch 7).
**`protected`** — accessible within package and by subclasses (Ch 7–8).
**`public`** — accessible from any code (Ch 7).
**Recursion** — a method calling itself; needs a base case to terminate (Ch 7).
**Reference** — a variable holding an object's memory address, not the object itself (Ch 6).
**Run-time polymorphism** — selecting overridden behavior by object type at run time (Ch 8).
**`static`** — member belonging to the class, usable without an instance (Ch 7).
**Static block (`static {}`)** — runs once at class load to initialize static data (Ch 7).
**Static interface method** — JDK 8 method called as `Interface.method()`; needs no instance; not inherited (Ch 9).
**`String`** — immutable text object; Java's most-used class (Ch 7).
**Subclass** — the class that inherits (specialized) (Ch 8).
**`super`** — calls superclass constructor (`super(...)`) or accesses hidden superclass member (`super.x`) (Ch 8); `Interface.super.method()` selects an inherited default (Ch 9).
**Superclass** — the inherited (general) class (Ch 8).
**`this`** — reference to the current/invoking object; resolves instance-variable hiding (Ch 6).
**Type signature** — method name + parameter types; identity used for overriding (Ch 8).
**Varargs (`...`)** — variable-length argument list received as an array; must be the last, single such parameter (Ch 7).
**`var` (type inference)** — local variable type inferred from initializer; uses *declared* type, not runtime object type (Ch 7–8).

---

## Effective Java terms (Bloch, Items 10–25)

**Comparator construction methods** — fluent builders (`Comparator.comparingInt(...).thenComparingInt(...)`) for `compareTo`/`compare`; preferred over `<`/`>` (EJ Item 14).
**Conversion constructor / factory** — a copy constructor/factory whose argument is an interface, letting the client choose the copy's implementation type, e.g. `new TreeSet<>(s)` (EJ Item 13).
**Constant interface (antipattern)** — interface of only constants, implemented to import names; leaks implementation into the API — use a utility class/enum instead (EJ Item 22).
**Copy constructor / copy factory** — `Foo(Foo f)` / `static Foo newInstance(Foo f)`; the recommended alternative to `Cloneable`/`clone` (EJ Item 13).
**Decorator pattern** — a wrapper class that adds behavior to a wrapped instance (EJ Item 18).
**Defensive copy** — copying a client-supplied or returned mutable object so callers can't alter internal state (EJ Items 15, 17, 50).
**Forwarding method** — a method that delegates to the corresponding method on a contained (wrapped) instance; basis of composition (EJ Item 18).
**Functional approach** — operations return a new instance instead of mutating; method names are prepositions (`plus`) not verbs (`add`) (EJ Item 17).
**General contract** — the behavioral spec a method (`equals`, `hashCode`, `toString`, `compareTo`) must obey so dependent classes work (EJ Items 10–14).
**Immutable class** — instances cannot be modified after construction; the 5 rules: no mutators, no extension, all fields `final` + `private`, exclusive access to mutable components (EJ Item 17).
**Information hiding** — synonym for encapsulation: hide implementation, communicate via API (EJ Item 15).
**Mixin** — a type a class implements *in addition to* its primary type to declare optional behavior, e.g. `Comparable` (EJ Item 20).
**Natural ordering** — the ordering a class declares by implementing `Comparable.compareTo` (EJ Item 14).
**Significant field** — a field that participates in `equals` (and therefore must be in `hashCode`) (EJ Items 10–11).
**Simulated multiple inheritance** — implementing an interface by forwarding to a private inner class that extends its skeletal implementation (EJ Item 20).
**Skeletal implementation** — an abstract class (named `AbstractXxx`) implementing an interface's non-primitive methods atop its primitives; the Template Method pattern (EJ Item 20).
**Tagged class** — a class with a tag field + `switch` selecting flavor-specific behavior/fields; replace with a class hierarchy (EJ Item 23).
**Template Method pattern** — the pattern realized by a skeletal implementation (EJ Item 20).
**Value class** — a class representing a value (like `Integer`, `String`); the prime case for overriding `equals`/`hashCode`/`Comparable` (EJ Items 10, 14).
**Wrapper class** — a class that contains ("wraps") an instance of another and forwards to it; composition's robust alternative to subclassing (EJ Item 18).
