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

### **ITEM 28: PREFER LISTS TO ARRAYS**

### **ITEM 29: FAVOR GENERIC TYPES**

### **ITEM 30: FAVOR GENERIC METHODS**

### **ITEM 31: USE BOUNDED WILDCARDS TO INCREASE API FLEXIBILITY**

### **ITEM 32: COMBINE GENERICS AND VARARGS JUDICIOUSLY**

### **ITEM 33: CONSIDER TYPESAFE HETEROGENEOUS CONTAINERS**



