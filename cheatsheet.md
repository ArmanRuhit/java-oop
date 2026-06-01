# Java OOP Cheatsheet (Chapters 6–9)

## Access modifiers (Table 9-1)
| Access from… | `private` | default | `protected` | `public` |
|---|:---:|:---:|:---:|:---:|
| Same class | ✓ | ✓ | ✓ | ✓ |
| Same-pkg subclass | ✗ | ✓ | ✓ | ✓ |
| Same-pkg non-subclass | ✗ | ✓ | ✓ | ✓ |
| Other-pkg subclass | ✗ | ✗ | ✓ | ✓ |
| Other-pkg non-subclass | ✗ | ✗ | ✗ | ✓ |

(Non-nested class: only `public` or default. A `public` class must be alone in a file named after it.)

## Overloading vs Overriding
| | Overloading | Overriding |
|---|---|---|
| Where | same class | subclass vs superclass |
| Rule | different param list | identical name + signature |
| Return type matters? | no (can't distinguish) | must be compatible |
| Bound | compile time (early) | run time (late) |
| Polymorphism | compile-time | run-time |

## Argument passing
- **Primitive** → call-by-value; caller's variable unchanged.
- **Object** → reference passed *by value*; shared object, field mutations visible; can't repoint caller's variable.

## `this` vs `super`
- `this` → current object; `this.x` resolves field hiding; `this(...)` calls another constructor in same class.
- `super.x` → superclass member hidden by subclass; `super(...)` calls superclass constructor (must be FIRST statement).

## Default field values (default constructor)
`0` numeric · `null` reference · `false` boolean

## `static` rules
- Exists without an instance; access via `Classname.member`.
- Static method: only calls static methods, only accesses static vars, **no `this`/`super`**.
- `static {}` block runs once at class load.

## `final` — three meanings
1. field → constant (init at declaration or in constructor)
2. method → can't be overridden (enables inlining/early binding)
3. class → can't be inherited (all methods implicitly final)
> Illegal: `abstract` + `final` together.

## Inheritance quick rules
- `class B extends A` — single inheritance only.
- Subclass can't access superclass `private` members.
- Superclass reference can point to subclass object; only reference-type members are visible.
- Constructors run superclass → subclass (order of derivation).
- Overridden call → resolved by **object's runtime type**, not reference type.

## Abstract class
- `abstract` method = no body, must be overridden.
- Class with any abstract method must be `abstract`; can't be instantiated; can be a reference type.

## Packages
- `package pkg;` = FIRST statement; dir tree must mirror `a.b.c` → `a/b/c`.
- Found via current dir / `CLASSPATH` / `-classpath` (path points *to* the package, not into it).
- `import pkg.*;` or `import pkg.Class;` — convenience only; `java.lang.*` auto-imported; fully-qualified names always work.

## Interfaces
- `interface I { void m(); }` → methods implicitly `public abstract`; vars implicitly `public static final`.
- `class C implements I1, I2` — many interfaces, one superclass. Implementing methods must be `public`, exact signature.
- Interface reference → dynamic dispatch at run time across unrelated classes (strongest run-time polymorphism).
- Don't implement all methods → class must be `abstract`.
- Interfaces extend interfaces (`extends`); implementer must satisfy the whole chain.
- JDK 8: `default` method (body, evolves interface) + `static` method (`I.m()`). JDK 9: `private` helper.
- Interfaces hold **no state** (no instance variables) — that's the class-vs-interface line.

## interface vs abstract class
- Need multiple supertypes / unrelated implementers → **interface**.
- Need shared state or partial concrete impl in one hierarchy → **abstract class**.

## Default-method conflict resolution
1. Class implementation wins over any interface default.
2. Two interfaces, same default, not overridden → **error**.
3. Sub-interface (`extends`) default beats super-interface default.
4. Explicit: `InterfaceName.super.method()`.

## Gotchas
- Reference assignment `b2 = b1` copies the reference, not the object.
- Return type alone cannot overload.
- Varargs `type...` must be the last & only vararg param; watch ambiguity.
- `var` infers the *declared/initializer* type, not the runtime subclass type.
- `String` is immutable → use `StringBuilder`/`StringBuffer` to mutate.
- Array size = `.length` (field, not method).
