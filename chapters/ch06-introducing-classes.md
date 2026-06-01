# Chapter 6: Introducing Classes

## Core Idea
A class defines a new data type — a template that bundles **instance variables** (data) and **methods** (code). Objects are dynamically allocated instances of that template, accessed through references.

## Key Concepts
- **Class**: a logical construct/template that defines a new data type; has no physical reality on its own.
- **Object**: an instance of a class; occupies real memory. "Object" and "instance" are used interchangeably.
- **Instance variable**: data declared in a class; each object gets its *own copy*.
- **Method**: code that operates on the data; defines the interface to the data.
- **Member**: collective term for a class's variables and methods.
- **Reference**: a class-type variable holds the *address* of an object, not the object itself.
- **Constructor**: special method, same name as the class, no return type; initializes a new object.
- **`this`**: a reference to the object on which the method was invoked.
- **Garbage collection**: automatic reclamation of memory for objects with no remaining references.

## Mental Models
- **Two-step object creation**: declaring a reference (`Box b;`) does NOT create an object. `new` does the allocating; the reference just points at it. Think "label" (reference) vs "thing" (object).
- **Reference assignment copies the pointer, not the object**: `b2 = b1` makes both names point to the *same* object. (REMEMBER box in the book.)
- **A class is a "data engine"**: callers use it through its method controls without knowing the internal wiring — change the internals freely as long as the method interface holds.
- **Inside a method, instance variables are referred to directly** (no dot operator) because the method is always invoked relative to some object, so the object is already known.

## Anti-patterns
- **Exposing instance variables directly**: in well-designed classes, access data only through methods — you can change a method's behavior later, but not an exposed field's. (Stack's `stck` left public → open to "misuse or mischief.")
- **Assuming `b2 = b1` copies the object**: it copies only the reference; mutations through one alias affect the other.
- **Forgetting that primitives aren't objects**: `new` is for class objects; primitives (`int`, `char`) are plain variables for efficiency.

## Code Examples
```java
class Box {
  double width, height, depth;          // instance variables

  Box(double w, double h, double d) {   // parameterized constructor
    width = w; height = h; depth = d;
  }
  double volume() {                     // method — uses fields directly
    return width * height * depth;
  }
}
class BoxDemo {
  public static void main(String[] args) {
    Box mybox1 = new Box(10, 20, 15);   // new calls the constructor
    System.out.println("Volume is " + mybox1.volume());
  }
}
```
- **What it demonstrates**: class definition, constructor, method, `new`, and the dot operator on a reference.

```java
// 'this' resolves instance-variable hiding when a parameter shares a field's name
Box(double width, double height, double depth) {
  this.width  = width;    // this.field = parameter
  this.height = height;
  this.depth  = depth;
}
```
- **What it demonstrates**: `this` disambiguates a hidden instance variable from a local/parameter of the same name.

## Reference Tables
| Default value | Applies to |
|---|---|
| `0` | numeric primitive types |
| `null` | reference types |
| `false` | `boolean` |

(Used by the default constructor for non-initialized fields.)

## Key Takeaways
1. A class is a template (logical); an object is an instance (physical, in memory).
2. Object creation is two steps: declare a reference, then `new` to allocate.
3. Reference assignment copies the reference, never the object.
4. If you define *any* constructor, Java stops supplying the default constructor.
5. A constructor has no return type — its implicit return is the class type itself.
6. Encapsulate: hide data behind methods so internals can change without breaking callers (the Stack example).
7. You never `delete` in Java — garbage collection reclaims unreferenced objects automatically (and sporadically).

## Connects To
- **Ch 7**: access control (`private`/`public`) finally *enforces* the encapsulation this chapter motivates; method overloading lets one class have many constructors.
- **Ch 8**: inheritance reuses these class definitions; constructors chain via `super()`.
- **Encapsulation**: one of the OOP pillars introduced in Ch 2.
