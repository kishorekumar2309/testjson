# 8. METHODS

### **ITEM 49: CHECK PARAMETERS FOR VALIDITY**

- If an invalid parameter value is passed to a method and the method checks its parameters before execution, it will fail quickly and cleanly with an appropriate exception
- failure to validate parameters, can result in a violation of *failure atomicity*
- public and protected methods:
  - use the Javadoc `@throws` tag to document the exception that will be thrown if a restriction on parameter values is violated (*IllegalArgumentException, IndexOutOfBoundsException, orNullPointerException*)
  - Parameter check facilities - java.util.Objects:
    - null check - **requireNonNull**
    - range-checking - **checkFromIndexSize, checkFromToIndex, checkIndex**
- nonpublic methods:
  - check their parameters using *assertions* - assertions throw `AssertionError` if they fail
  - they have no effect and essentially no cost unless you enable them, which you do by passing the `-ea` (or `-enableassertions`) flag to the `java` command
- a computation implicitly performs a required validity check but throws the wrong exception if the check fails - use the *exception translation* idiom
- ***Action:*** document the restrictions on parameters and enforce them with explicit checks at the beginning of the method body



### **ITEM 50: MAKE DEFENSIVE COPIES WHEN NEEDED**

- **You must program defensively, with the assumption that clients of your class will do their best to destroy its invariants**
- ***Date*** is obsolete and should no longer be used in new code,  use `Instant` (or `Local-DateTime` or `ZonedDateTime`)
- **it is essential to make a** ***defensive copy*** **of each mutable parameter to the constructor**
- **defensive copies are made** ***before*** **checking the validity of the parameters (Item 49), and the validity check is performed on the copies rather than on the originals** - protects *window of vulnerability* and avoids *time-of-check/time-of-use* or *TOCTOU* attack
- **do not use the** `clone` method to make a defensive copy of a parameter whose type is subclassable by untrusted parties
- accessors should **return defensive copies of mutable internal fields**
- consider making defensive copies in case of ***mutable*** fields
- If the cost of the copy would be prohibitive *and* the class trusts its clients not to modify the components inappropriately, then the defensive copy may be replaced by documentation outlining the client’s responsibility not to modify the affected components



### **ITEM 51: DESIGN METHOD SIGNATURES CAREFULLY**

- **Choose method names carefully** - Avoid long method names
  - primary goal should be to choose names that are understandable and consistent with other names in the same package
  - secondary goal should be to choose names consistent with the broader consensus, where it exists
- **Don’t go overboard in providing convenience methods** - Every method should “pull its weight.” Too many methods make a class difficult to learn, use, document, test, and maintain
- **Avoid long parameter lists** - Four parameters or fewer
- Parameter shortening techniques:
  - break the method up into multiple methods, each of which requires only a subset of the parameters
  - create *helper classes* to hold groups of parameters - static member classes
  - adapt the Builder pattern from object construction to method invocation
- **For parameter types, favor interfaces over classes** (Item 64)
- **Prefer two-element enum types to `boolean` parameters**