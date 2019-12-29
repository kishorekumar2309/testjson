# 5. Generics

Before generics,

- had to cast every object read from a collection and theses casts could fail at runtime if someone inserted object of the wrong type

After generics,

- compiler inserts casts automatically based on object type and reports *at compile time* if someone inserted object of the wrong type

***Terminologies***:

*Generic* class or interface: whose declaration has one or more *type parameters*

| **Term**                | **Example**                 | **Item**     |
| ----------------------- | --------------------------- | ------------ |
| Parameterized type      | `List<String>`              | Item 26      |
| Actual type parameter   | `String`                    | Item 26      |
| Generic type            | `List<E>`                   | Items 26, 29 |
| Formal type parameter   | `E`                         | Item 26      |
| Unbounded wildcard type | `List<?>`                   | Item 26      |
| Raw type                | `List`                      | Item 26      |
| Bounded type parameter  | `<E extends Number>`        | Item 29      |
| Recursive type bound    | `<T extends Comparable<T>>` | Item 30      |
| Bounded wildcard type   | `List<? extends Number>`    | Item 31      |
| Generic method          | `static List asList(E[] a)` | Item 30      |
| Type token              | `String.class`              | Item 33      |

### **ITEM 26: DON’T USE RAW TYPES**

- With raw types you can accidentally insert objects of the wrong type and the erroneous insertion compiles and runs without error
- *ClassCastException* will be thrown at the time of reading the object back in runtime
- **If you use raw types, you lose all the safety and expressiveness benefits of generics**
- *migration compatibility* *and interoperability with legacy code*, drove the decisions to support raw types and to implement generics using *erasure*
-  raw type `List` vs parameterized type `List<Object>`
  - you can pass a `List<String>` to a parameter of type `List`, you can’t pass it to a parameter of type `List<Object>`
  - sub-typing rules for generics, and `List<String>` is a subtype of the raw type `List`, but not of the parameterized type `List<Object>`
  - **you lose type safety if you use a raw type such as** `List`**, but not if you use a parameterized type such as** `List<Object>`
- use *unbounded wildcard types*, if you want to use a generic type but you don’t know or care what the actual type parameter is (Set<?>)
- exceptions to the rule that you should not use raw types
  - **must use raw types in class literals **(`List.class` is legal, but `List<String>.class` is not)
  - for `instanceof` operator, because generic type information is erased at runtime, it is illegal to use the `instanceof` operator on parameterized types other than unbounded wildcard types
- ***review***: `Set<Object>` is a parameterized type representing a set that can contain objects of any type, `Set<?>` is a wildcard type representing a set that can contain only objects of some unknown type, and `Set` is a raw type, which opts out of the generic type system. The first two are safe, and the last is not.

### **ITEM 27: ELIMINATE UNCHECKED WARNINGS**

Compiler warnings related to generics:

- unchecked cast warnings
- unchecked method invocation warnings
- unchecked parameterized vararg type warnings
- unchecked conversion warnings

***Actions:***

- **Eliminate every unchecked warning that you can** - It ensures that you won’t get a `ClassCastException` at runtime
- **If you can’t eliminate a warning, but you can prove that the code that provoked the warning is typesafe, then (and only then) suppress the warning with an** `@SuppressWarnings("unchecked")` **annotation**
- **Always use the** `SuppressWarnings` **annotation on the smallest scope possible** with comments



### **ITEM 28: PREFER LISTS TO ARRAYS**

***Arrays differ from generic types in two important ways:***

1. arrays are *covariant* (if `Sub` is a subtype of `Super`, then the array type `Sub[]` is a subtype of the array type `Super[]`), Generics, by contrast, are *invariant*.
2. arrays are *reified* (arrays know and enforce their element type at runtime), Generics, by contrast, are implemented by *erasure* (enforce their type constraints only at compile time and discard (or *erase*) their element type information at runtime)

***Arrays and generics do not mix well:***

- it is illegal to create an array of a generic type ( `new List<E>[]`), a parameterized type (`new List<String>[]`), or a type parameter (`new E[]`) 
- Types such as `E`, `List<E>`, and `List<String>` are technically known as *nonreifiable* types (runtime representation contains less information than its compile-time representation, because of erasure)
- the only parameterized types that are reifiable are unbounded wildcard types such as `List<?>` and `Map<?,?>`

Prefer using `List<E>`, even though you might sacrifice some conciseness or performance, but in exchange you get better type safety and interoperability



### **ITEM 29: FAVOR GENERIC TYPES**

Generifying a class: (class uses Object as type)

1. add one or more type parameters to its declaration
2. replace all the uses of the type `Object` with the appropriate type parameter and then try to compile

Generic types are safer and easier to use than types that require casts in client code



### **ITEM 30: FAVOR GENERIC METHODS**

- Static utility methods that operate on parameterized types are usually generic
- **The type parameter list, which declares the type parameters, goes between a method’s modifiers and its return type **(`public static <E> Set<E>(Set<E> s1, Set<E> s2)`)
- *generic singleton factory* - used for function objects (Collections.reverseOrder)



### **ITEM 31: USE BOUNDED WILDCARDS TO INCREASE API FLEXIBILITY**

- parameterized types are *invariant* - `List<String>` is not a subtype of `List<Object>`
- **For maximum flexibility, use wildcard types on input parameters that represent producers or consumers**
- If an input parameter is both a producer and a consumer, then wildcard types will do you no good
- **PECS stands for producer-**`extends`**, consumer-**`super`: if a parameterized type represents a `T` producer, use `<? extends T>`; if it represents a `T` consumer, use `<? super T>`
- **If the user of a class has to think about wildcard types, there is probably something wrong with its API**
- *explicit type argument*
- Comparables are always consumers, so **use** `Comparable<? extends T>` **in preference to** `Comparable<T>`**.** The same is true of comparators; therefore **use** `Comparator<? super T>` **in preference to** `Comparator<T>`
- As a rule, **if a type parameter appears only once in a method declaration, replace it with a wildcard**



### **ITEM 32: COMBINE GENERICS AND VARARGS JUDICIOUSLY**

- varargs -  allow clients to pass a variable number of arguments to a method (but it is a *leaky abstraction*: when you invoke a varargs method, an array is created to hold the varargs parameters; that array, which should be an implementation detail, is visible)

- *Heap pollution* occurs when a variable of a parameterized type refers to an object that is not of that type

- **it is unsafe to store a value in a generic varargs array parameter**

- **the** `SafeVarargs` **annotation constitutes a promise by the author of a method that it is typesafe**, **use** `@SafeVarargs` **on every method with a varargs parameter of a generic or parameterized type**

- `SafeVarargs` annotation is legal

  - only on methods that can’t be overridden
  - In Java 8, the annotation was legal only on static methods and final instance methods
  - In Java 9, it became legal on private instance methods as well

- **it is unsafe to give another method access to a generic varargs parameter array** with two exceptions

  - it is safe to pass the array to another varargs method that is correctly annotated with `@SafeVarargs`
  - it is safe to pass the array to a non-varargs method that merely computes some function of the contents of the array

- a generic varargs methods is safe if:

  1. it doesn’t store anything in the varargs parameter array, and

  2. it doesn’t make the array (or a clone) visible to untrusted code. If either of these prohibitions is violated, fix it

- Alternative: Use List instead of varargs



### **ITEM 33: CONSIDER TYPESAFE HETEROGENEOUS CONTAINERS**

- the normal use of generics, restricts you to a fixed number of type parameters per container
- you can get around this restriction by placing the type parameter on the key rather than the container
- type token - Class objects as keys for such typesafe heterogeneous containers
- example: DatabaseRow type representing a database row (the container), and a generic type Column as its key
- `cast` - class `Class` provides an instance method which
  - checks that its argument is an instance of the type represented by the `Class` object. 
  - if so, it returns the argument; otherwise it throws a `ClassCastException`
- `asSubclass` - class `Class` provides an instance method that casts safely (and dynamically)
  - casts the `Class` object on which it is called to represent a subclass of the class represented by its argument
  - if the cast succeeds, the method returns its argument; if it fails, it throws a `ClassCastException`