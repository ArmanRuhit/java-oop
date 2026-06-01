# Chapter 9: Packages and Interfaces

## Core Idea
**Packages** compartmentalize the class namespace (naming + visibility control); **interfaces** fully separate *what* a class does from *how*, enabling a class to implement many interfaces and achieve run-time polymorphism across unrelated class hierarchies.

## Frameworks Introduced
- **Package** — `package pkg;` as the *first* statement in a file; classes belong to that package and must live in a matching directory hierarchy (`a.b.c` → `a/b/c`).
  - When to use: any real application (avoid the unnamed default package); to control cross-class/cross-package visibility.
  - How: declare `package`, mirror it in the filesystem, locate via current dir / `CLASSPATH` / `-classpath`.
- **Interface** — `interface Name { ... }` declares method signatures (no body, implicitly `public abstract`) and constants (implicitly `public static final`).
  - When to use: specify a contract that *unrelated* classes can fulfill; get multiple-interface implementation where single class inheritance can't.
  - How: `class C implements I1, I2 { ... }`; every required method must be `public` with an exactly matching signature.
- **Default method** (JDK 8) — `default` keyword gives an interface method a body.
  - When to use: evolve a widely-used interface without breaking implementers; make a method optional.

## Key Concepts
- **`import`**: convenience to use a class by short name; `import pkg.*;` or `import pkg.Class;`. `java.lang.*` is auto-imported. Always optional — you can use a fully qualified name instead.
- **Fully qualified name**: full package path + class (e.g., `java.util.Date`).
- **Interface reference**: a variable of interface type can point to any implementing object; calls dispatch dynamically at run time — knows only the interface's methods.
- **Partial implementation**: a class that doesn't implement all interface methods must be `abstract`.
- **Nested/member interface**: declared inside a class/interface; can be `public`/`private`/`protected`; referenced as `Outer.NestedIF`.
- **`static` interface method** (JDK 8): called as `Interface.method()`; not inherited by implementers or subinterfaces.
- **`private` interface method** (JDK 9): shared helper for default/private methods only; not inherited.

## Mental Models
- **Package = namespace + access boundary**: two mechanisms in one. Classes in a package can have "intimate knowledge of each other" via default access without exposing it to the world.
- **Interface decouples from the inheritance hierarchy**: because interfaces live in a separate hierarchy, classes unrelated by inheritance can share one interface — "this is where the real power of interfaces is realized."
- **Interface reference = the most powerful run-time polymorphism**: one `IntStack` reference drives `FixedStack` or `DynStack`, resolved at run time. The caller dispatches without knowing the callee.
- **Default methods give *limited* multiple inheritance of behavior** — but NOT of state (interfaces still can't hold instance variables). That's the defining class-vs-interface line.

## Anti-patterns
- **Using the default (unnamed) package** for real apps: fine for samples, inadequate otherwise.
- **Constant interface** (implementing an interface just to import `final static` constants): explicitly called "controversial," described "for completeness" only.
- **Forgetting `public` on an implementing method**: interface methods must be implemented as `public` or it won't compile.
- **Expecting multiple inheritance of state via default methods**: impossible — interfaces carry no instance variables.
- **Unresolved default-method conflict**: two interfaces with the same default method, not overridden by the class → compile-time error.

## Code Examples
```java
interface IntStack {
  void push(int item);
  int  pop();
  default void clear() {                 // JDK 8: evolve interface, don't break callers
    System.out.println("clear() not implemented.");
  }
}
class FixedStack implements IntStack {
  private int[] stck; private int tos;
  FixedStack(int size) { stck = new int[size]; tos = -1; }
  public void push(int item) { stck[++tos] = item; }   // must be public
  public int  pop()         { return stck[tos--]; }
}
// Dispatch through an interface reference — resolved at run time:
IntStack mystack = new FixedStack(5);   // or = new DynStack(5);
mystack.push(1);
```
- **What it demonstrates**: interface contract, `implements`, `public` methods, default method, and polymorphic interface-reference dispatch.

```java
interface Alpha { default void reset() {/*...*/} }
interface Beta  extends Alpha {
  default void reset() { Alpha.super.reset(); }   // explicitly call inherited default
}
```
- **What it demonstrates**: `Interface.super.method()` resolves a default-method conflict explicitly.

## Reference Tables
**Table 9-1 — Class Member Access**

| Access from… | `private` | default | `protected` | `public` |
|---|:---:|:---:|:---:|:---:|
| Same class | ✓ | ✓ | ✓ | ✓ |
| Same-package subclass | ✗ | ✓ | ✓ | ✓ |
| Same-package non-subclass | ✗ | ✓ | ✓ | ✓ |
| Different-package subclass | ✗ | ✗ | ✓ | ✓ |
| Different-package non-subclass | ✗ | ✗ | ✗ | ✓ |

(Non-nested classes have only two levels: `public` or default. A `public` class must be the only public class in its file, and the file must be named after it.)

**Default-method conflict resolution (multiple inheritance of behavior)**
1. Class implementation always wins over any interface default.
2. Two interfaces, same default, class doesn't override → **error**.
3. One interface `extends` another with a common default → the **sub-interface's** version wins.
4. Explicit selection: `InterfaceName.super.methodName()`.

## Key Takeaways
1. `package` is the first statement; the package hierarchy must mirror the directory structure.
2. Packages add the default access level: visible to subclasses *and* same-package classes.
3. `import` is pure convenience; `java.lang` is implicit; fully-qualified names always work.
4. A class can implement many interfaces but extend only one class — interfaces are Java's answer to multiple inheritance (of type/behavior, not state).
5. Interface references dispatch dynamically at run time — the strongest form of run-time polymorphism.
6. Interface variables are implicitly `public static final`; methods are implicitly `public abstract` (unless `default`/`static`/`private`).
7. JDK 8 added `default` + `static` interface methods; JDK 9 added `private` ones — but interfaces still hold no state.

## Connects To
- **Ch 7**: completes the access-control story — default access only fully makes sense with packages (Table 9-1).
- **Ch 8**: interfaces extend the abstract-class idea (`abstract` partial implementations) and the superclass-reference→subclass-object pattern becomes interface-reference→implementer.
- **Ch 10 (Exceptions)**: the note that `clear()` should throw `UnsupportedOperationException` instead of printing.
- **Ch 16 (Modules)**: packages become part of modules and the module path.
