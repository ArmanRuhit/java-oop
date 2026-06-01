# Effective Java — Chapter 3: Methods Common to All Objects (Items 10–14)

> Source: *Effective Java, 3rd Edition* by Joshua Bloch. `Object`'s nonfinal methods (`equals`, `hashCode`, `toString`, `clone`) have **general contracts**; override them correctly or collection classes (`HashMap`, `HashSet`, `TreeSet`) break. `Comparable.compareTo` is covered here too because it has the same character.

## Core Idea
The Object-method contracts are load-bearing: other classes assume you obey them. Honor them or fail mysteriously.

## Items as Principles

### Item 10 — Obey the general contract when overriding `equals`
**Don't override at all** when: each instance is inherently unique (e.g. `Thread`); no need for logical equality; a superclass's `equals` already fits; or the class is private/package-private and `equals` is never called. **Do override** when a class has *logical equality* distinct from identity and no superclass already did — typically **value classes** (`Integer`, `String`), unless instance-controlled (enums).
The contract is an equivalence relation: **reflexive, symmetric, transitive, consistent**, and `x.equals(null)==false`.
- **Symmetry trap**: a `CaseInsensitiveString` that also equals `String` breaks symmetry. Don't make `equals` interoperate with other types.
- **Transitivity trap**: *there is no way to extend an instantiable class with a new value component and preserve the `equals` contract.* Favor **composition over inheritance** (Item 18) — give the subclass-would-be a private field + a view method.
- Recipe: `==` self-check (optimization) → `instanceof` type check → cast → compare each **significant** field. For `float`/`double` use `Float.compare`/`Double.compare`.
- Always override `hashCode` too (Item 11). Don't write `equals(MyType)` — that *overloads*, not overrides; use `@Override`.

### Item 11 — Always override `hashCode` when you override `equals`
Violating it breaks hash-based collections: equal objects **must** have equal hash codes. Bad: `return 42;` (legal, atrocious — all objects collide, hash tables degrade to linked lists, O(n²)).
**Recipe**: `int result = c` for the first significant field; for each remaining field compute `c` (`Type.hashCode(f)` for primitives, recursive `hashCode` for references, `Arrays.hashCode` for arrays) then `result = 31 * result + c`. `31` is an odd prime; order-dependence avoids anagram collisions. Exclude derived fields and any field not in `equals`.
- `Objects.hash(...)` is a one-liner but slower (varargs array + boxing) — fine when performance isn't critical.
- Cache the hash for immutable classes if computing it is costly (lazy init, thread-safe).
- Don't over-specify the returned value in docs — keeps future flexibility.

### Item 12 — Always override `toString`
`Object.toString` gives `ClassName@hex` — useless. A good `toString` makes classes pleasant to use and systems easier to debug (it's auto-invoked by `println`, string concat, `assert`, debuggers, logged messages). Return all interesting info, concisely. **Decide whether to specify the format**: specifying (with a matching static factory/constructor to parse back) gives a standard representation but locks you in forever; not specifying preserves flexibility. Either way, document the intent and **provide programmatic accessors** for the data — else you force clients to parse the string and turn the format into a de-facto API.

### Item 13 — Override `clone` judiciously
`Cloneable` is a broken design: it's a *mixin* that changes the behavior of `Object.clone` (protected, field-by-field copy) instead of declaring a method. Implementing it correctly requires an unenforceable, extralinguistic protocol. If you must: call `super.clone()`, exploit **covariant return types**, then **deep-copy mutable state** (recursively clone arrays/linked lists, or reinitialize). `clone` acts as a constructor — must not harm the original, must not invoke overridable methods, conflicts with `final` mutable fields. **Better alternative: a copy constructor or copy factory** (`public Yum(Yum y)` / `static Yum newInstance(Yum y)`) — no checked exceptions, no casts, no extralinguistic creation, and can take an interface type (conversion constructor, e.g. `new TreeSet<>(s)`). Arrays are the sole place `clone` is genuinely preferred.

### Item 14 — Consider implementing `Comparable`
Implementing `Comparable` gives a **natural ordering** and free interoperability with `Arrays.sort`, `TreeSet`/`TreeMap`, searching, and generic algorithms. Contract mirrors `equals`: anti-symmetric `sgn(x.compareTo(y)) == -sgn(y.compareTo(x))`, transitive, consistent; strongly recommended (not required) that `compareTo`-equality match `equals` (`BigDecimal` violates this — `HashSet` vs `TreeSet` give different results). Same extension caveat as `equals` — use composition, not subclassing, to add a value component.
- Compare most-significant field first, return on first nonzero.
- **Never use `<`/`>`** (verbose, error-prone) and **never use difference-based comparators** (`o1.hashCode() - o2.hashCode()` — integer overflow breaks transitivity). Use static `compare` methods or `Comparator` construction methods.

## Code Examples

```java
// Item 11 — typical hashCode recipe
@Override public int hashCode() {
    int result = Short.hashCode(areaCode);
    result = 31 * result + Short.hashCode(prefix);
    result = 31 * result + Short.hashCode(lineNum);
    return result;
}
```

```java
// Item 14 — Comparator construction methods (fluent, ~10% slower)
private static final Comparator<PhoneNumber> COMPARATOR =
    comparingInt((PhoneNumber pn) -> pn.areaCode)
        .thenComparingInt(pn -> pn.prefix)
        .thenComparingInt(pn -> pn.lineNum);
public int compareTo(PhoneNumber pn) { return COMPARATOR.compare(this, pn); }
```

## Anti-patterns
- **`equals` that interoperates with other types** → breaks symmetry.
- **Extending an instantiable class to add a value component** → can't preserve `equals`/`compareTo`. Use composition.
- **`hashCode` that returns a constant** or excludes significant fields → quadratic hash tables.
- **`return 42;`**, **`Cloneable` on classes designed for inheritance**, **difference-based comparators** (`a - b`).
- Overriding `equals` but not `hashCode` (or vice versa).

## Key Takeaways
1. The five Object-method contracts are obligations to *other* classes — break them and collections silently misbehave.
2. `equals` + `hashCode` travel together; both compare exactly the **significant fields**.
3. Prefer **copy constructors/factories** over `Cloneable`/`clone`.
4. Implement `Comparable` for any value class with a sensible ordering; build comparators with `Comparator.comparing*`, never `<`/`>` or `a-b`.
5. There is no safe way to add a value component by subclassing — reach for **composition** (Item 18).

## Connects To
- **Item 18 (Ch 4)**: composition over inheritance — the escape hatch for the `equals`/`compareTo` extension problem.
- **Item 17 (Ch 4)**: immutable classes naturally satisfy these contracts and can cache `hashCode`.
- **Schildt Ch 8**: `Object` class and method overriding — this chapter is the "do it correctly" layer on top.
