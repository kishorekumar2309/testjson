# Classes and Interfaces

**ITEM 15: Minimize the accessibility of classes and members**

- make each class or member as inaccessible as possible

- top-level (non-nested) classes and interfaces - *package-private* and *public*

- members (fields, methods, nested classes, and nested interfaces) - **access modifiers**

  - **private**: accessible only from the top-level class
  - **package-private**: accessible from any class in the package, aka *default* access, this is the access level you get if no access modifier is specified
    - *exception*: interface members are public by default
  - **protected**: accessible from subclasses of the class and from any class in the package
  - **public**: accessible from anywhere

  ![Access modifiers in java](https://www.sitesbay.com/java/images/basic-java/access-modifiers.png)

- private and package-private members are part of a class’s implementation and do not normally impact its exported API

  - *exception*: can, however, “leak” into the exported API if the class implements `Serializable`

- If a method overrides a superclass method, it cannot have a more restrictive access level in the subclass than in the superclass (LSP)

- For testing, it's acceptable to make a private member of a public class package-private in order to test it

- **Instance fields of public classes should rarely be public** - **classes with public mutable fields are not generally thread-safe**

  - *exception*: can expose constants via public static final fields, either primitive values or references to immutable objects

- a nonzero-length array is always mutable, so **it is wrong for a class to have a public static final array field, or an accessor that returns such a field**

- two additional implicit access levels as part of the *module system*, using *export declarations* in its *module declaration* (advisory)

  - allows you to share classes among packages within a module without making them visible to the entire world

    

**ITEM 16: In Public Classes, Use Accessor Methods, Not Public Fields**

- degenerate classes - serves no purpose other than to group instance fields

- use private fields and public *accessor methods* (getters) and, for mutable classes, *mutators* (setters)

- factors to be considered:

  - can we change the representation without changing the API ?
  - can we enforce invariants ?
  - can we take auxiliary action when a field is accessed ?

- public classes should never expose mutable fields

- less harmful - public classes to expose immutable fields

- sometimes desirable - package-private or private nested classes to expose fields, whether mutable or immutable

  

**ITEM 17: Minimize Mutability**

- immutable class - a class whose instances cannot be modified. **Classes should be immutable unless there’s a very good reason to make them mutable**

- **If a class cannot be made immutable, limit its mutability as much as possible**

- 5 rules to make a class immutable:

  1. **Don’t provide methods that modify the object’s state** (*mutators*)

  2. **Ensure that the class can’t be extended** (by making the class final or make all of its constructors private or package-private and add public static factories in place of the public constructors)

  3. **Make all fields final** - *exception*: nonfinal fields can be used to cache the results of expensive computations the first time they are needed (*lazy initialization*)

  4. **Make all fields private**

  5. **Ensure exclusive access to any mutable components** *(make defensive copies* in constructors, accessors, and `readObject` methods)

- **declare every field** `private final` **unless there’s a good reason to do otherwise**

-  *functional* approach - methods return the result of applying a function to their operand, without modifying it (method names should be *prepositions*)

- *procedural* or *imperative* approach - methods apply a procedure to their operand

- **Immutable objects are inherently thread-safe; they require no synchronization.**

- An immutable class can provide static factories that cache frequently requested instances to avoid creating new instances. 

- *Advantages* of using static factory:

  - reduces memory footprint and garbage collection costs
  - gives you the flexibility to add caching later, without modifying clients

- should not provide a `clone` method or *copy constructor*

- **The major disadvantage of immutable classes is that they require a separate object for each distinct value**

  

**ITEM 18: Favor Composition Over Inheritance**

- Code reuse through inheritance is safe

  - within a package
  - extending classes specifically designed and documented for extension

- **Unlike method invocation, inheritance violates encapsulation** - a subclass depends on the implementation details of its superclass for its proper function

- problems stem from overriding methods, adding new methods which has the same signature as the methods added to the super class in  the future (different return type - compiler error & same type - overriding)

- ***Solution:*** **composition** - forwarding methods in the new class

- Decorator pattern: wrapper classes. caveat: not suited for use in *callback frameworks*, wherein objects pass self-references to other objects for subsequent invocations. Because a wrapped object doesn’t know of its wrapper, it passes a reference to itself (`this`) and callbacks elude the wrapper. This is known as the *SELF problem*

  

**ITEM 19: Design And Document For Inheritance Or Else Prohibit It**

- **the class must document its** ***self-use*** **of overridable methods** - labeled “Implementation Requirements,” which is generated by the Javadoc tag `@implSpec`. eg. java.util.AbstractCollection - public boolean remove(Object o)
- ***only*** **way to test a class designed for inheritance is to write subclasses** (3)
- **Constructors must not invoke overridable methods,** directly or indirectly - as superclass constructor runs before the subclass constructor
- avoid Cloneable and Serializable interfaces in the super class - **neither** `clone` **nor** `readObject` **may invoke an overridable method, directly or indirectly**
- Serializable - `readResolve` or `writeReplace` method, you must make the `readResolve` or `writeReplace` method protected rather than private
- use case: abstract classes, including *skeletal implementations* of interfaces
- **prohibit subclassing in classes that are not designed and documented to be safely subclassed**. 2 ways to prohibit subclassing:
  1. declare the class final
  2. make all the constructors private or package-private and add public static factories in place of the constructors

**ITEM 20: Prefer Interfaces To Abstract Classes**

- **Existing classes can easily be retrofitted to implement a new interface**

- **Interfaces are ideal for defining mixins** - a *mixin* is a type that a class can implement in addition to its “primary type,” to declare that it provides some optional behavior

- **Interfaces allow for the construction of nonhierarchical type frameworks**

- **Interfaces enable safe, powerful functionality enhancements** -  *wrapper class*

- use default method - to provide implementation assistance and document them for inheritance using the `@implSpec` Javadoc tag

- interface default methods are not permitted to override `Object` methods such as `equals`, `hashCode`, and `toString`

- *Template Method* pattern - providing an abstract *skeletal implementation class* (`Abstract`*Interface*) to go with an interface

- *simulated multiple inheritance* - class implementing the interface can forward invocations of interface methods to a contained instance of a private inner class that extends the skeletal implementation

- Steps for writing a skeletal implementation (AbstractList):

  1. study the interface and decide which methods are the primitives (which the others can be implemented) - abstract methods in your skeletal implementation
  2. provide default methods in the interface for all of the methods that can be implemented directly atop the primitives
  3. write a class declared to implement the interface, with implementations of all of the remaining interface methods and object methods
  4. **good documentation is absolutely essential in a skeletal implementation**

- simple implementation (not an abstract class) - AbstractMap.SimpleEntry

  

**ITEM 21: Design Interfaces For Posterity**

- risks of interface default methods:
  - “injected” into existing implementations without the knowledge or consent of their implementors
  - **it is not always possible to write a default method that maintains all invariants of every conceivable implementation** (e.g. Collection - removeIf, which Apache `SynchronizedCollection` does not override)
  - **existing implementations of an interface may compile without error or warning but fail at runtime**
- critically important to test each new interface before release it (3 diverse implementations)



**ITEM 22: Use Interfaces Only To Define Types**

- the interface serves as a *type* that can be used to refer to instances of the class

- interface should say something about what a client can do with instances of the class 

- **poor use of interfaces**: *constant interface* - contains no methods; it consists solely of static final fields, each exporting a constant

  - alternatives: *enum type* or noninstantiable *utility class* 

  

**ITEM 23: Prefer Class Hierarchies To Tagged Classes**

- tagged class - class whose instances come in two or more flavors and contain a *tag* field indicating the flavor of the instance (e.g. circle or a rectangle)
- problems with tagged classes - cluttered with boilerplate - enum declarations, tag fields, switch statements and **tagged classes are verbose, error-prone, and inefficient**
- **tagged class is just a pallid imitation of a class hierarchy**. Steps to transform a tagged class into a class hierarchy:
  - define an abstract class containing: 
    - abstract method for each method in the tagged class whose behavior depends on the tag value
    - any methods whose behavior does not depend on the value of the tag
    - any data fields used by all the flavors
  - define a concrete subclass of the root class for each flavor of the original tagged class and in each subclass
    - include the data fields particular to its flavor
    - appropriate implementation of each abstract method in the root class



**ITEM 24: Favor Static Member Classes Over Nonstatic**

- *nested class* - a class defined within another class and should exist only to serve its enclosing class otherwise it should be a top-level class
- 4 kinds of nested classes and when to use them:
  1. *static member classes*:
     - simplest kind - declared inside another class and has access to all of the enclosing class’s members, even those declared private
     - access to static member of its enclosing class and obeys the same accessibility rules as other static members
     - common use: public helper class, useful only in conjunction with its outer class (e.g. enum describing the operations supported by a calculator)
  2. *nonstatic member classes*:
     - each instance of a nonstatic member class is implicitly associated with an *enclosing instance* of its containing class
     - can invoke methods on the enclosing instance or obtain a reference to the enclosing instance using the *qualified this* construct
     - impossible to create an instance of a nonstatic member class without an enclosing instance
     - common use: *Adapter* - allows an instance of the outer class to be viewed as an instance of some unrelated class (e.g. Map - keySet, entrySet)
  3. *anonymous classes*:
     - has no name and not a member of its enclosing class
     - limitations on the applicability of anonymous classes
       - can’t instantiate them except at the point they’re declared
       - can’t perform `instanceof` tests or do anything else that requires you to name the class
       - implementing a interface or extending a class is not supported
       - must be kept short—about ten lines or fewer—or readability will suffer
     - common use: implementation of static factory methods
     - lambdas are now preferred
  4. *local classes*
     - least frequently used
     - they have names and can be used repeatedly
     - like anonymous classes they
       - have enclosing instances only if they are defined in a nonstatic context
       - cannot contain static members
       - should be kept short so as not to harm readability
- ***always*** **put the** `static` **modifier in its declaration,** **for a member class that does not require access to an enclosing instance**. Omitting the modifier causes
  - each instance will have a hidden extraneous reference to its enclosing instance - storing this reference takes time and space
  - result in the enclosing instance being retained when it would otherwise be eligible for garbage collection - memory leak can be catastrophic



**ITEM 25: Limit Source Files To A Single Top-Level Class**

- split the top-level classes into separate source files
- alternative - use static member classes
- **Never put multiple top-level classes or interfaces in a single source file**

