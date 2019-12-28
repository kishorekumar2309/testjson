# DESIGN PRINCIPLES

**GOAL**

Creation of mid-level software structures that

1. Tolerate change
2. Are easy to understand
3. Are the basis of components that can be used in many software systems

### SOLID PRINCIPLES

#### SRP: The Single Responsibility Principle 

***Definitions:***

- *A module should have one, and only one, reason to change*
- *A module should be responsible to one, and only one, user or stakeholder* 
- *A module should be responsible to one, and only one, actor*

module - source file

***Symptoms***:

1. Accidental Duplication
2. Merges

***Solutions***:

1. Separate the data from the functions
2. *Facade* pattern
3. Keep the most important business rules closer to the data: Have that method in data class and use the class as Facade for other methods

SRP at different levels:

- Module Level - functions and classes
- Components Level - Common Closure Principle
- Architectural Level -  Axis of Change responsible for the creation of Architectural Boundaries



#### OCP: The Open-Closed Principle

***Definitions:***

- *A software artifact should be open for extension but closed for modification.*

- The behavior of a software artifact ought to be extendible, without having to modify that artifact.

If component A should be protected from changes in component B, then component B should depend on component A

Higher-level components (central) in that hierarchy are protected from the changes made to lower-level components (peripheral)

***Directional Control:*** Interfaces are created to invert the dependency 

***Information Hiding:*** Transitive dependencies are a violation of the general principle that software entities should not depend on things they don’t directly use. Interfaces are created as protection from changes in the internals

***Goal***:

- make the system easy to extend without incurring a high impact of change
- partition the system into components, and arranging those components into a dependency hierarchy
- protects higher-level components from changes in lower-level components



#### LSP: The Liskov Substitution Principle

***Definitions:***

- If for each object o1 of type S there is an object o2 of type T such that for all programs P defined in terms of T, the behavior of P is unchanged when o1 is substituted for o2 then S is a subtype of T
- Interchangeable parts must adhere to a contract that allows those parts to be substituted one for another.

***Guiding The Use Of Inheritance***

***The Square/Rectangle Problem***: Violation example

The LSP should be extended to the level of architecture. 

A simple violation of substitutability, can cause a system’s architecture to be polluted with a significant amount of extra mechanisms.



**ISP: The Interface Segregation Principle**

Avoid depending on things that they don’t use.

Have individual interfaces for different purposes.

***Architecture***: it is harmful to depend on modules that contain more than you need



**DIP: The Dependency Inversion Principle**

***Definitions***

- source code dependencies refer only to abstractions, not to concretions
- code that implements high-level policy should not depend on the code that implements low-level details. Rather, details should depend on policies

***Stable Abstractions***

- interfaces are less volatile than implementations:
  - every change to an abstract interface corresponds to a change to its concrete implementations
  - conversely, changes to concrete implementations do not always, or even usually, require changes to the interfaces that they implement
- coding practices:
  - **Don’t refer to volatile concrete classes**
  - **Don’t derive from volatile concrete classes**
  - **Don’t override concrete functions**
  - **Never mention the name of anything concrete and volatile**

***Factories***: Use *Abstract Factory* for object creation

***Concreate Components:*** DIP violations cannot be entirely removed, but they can be gathered into a small number of concrete components and kept separate from the rest of the system