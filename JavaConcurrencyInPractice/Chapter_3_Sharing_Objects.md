# SHARING OBJECTS

Writing correct concurrent programs is primarily about ***managing access to shared, mutable state***

***Focus:*** Techniques for sharing and publishing objects so they can be safely accessed by multiple threads

2 important aspects:

1. Atomicity: `synchronized` blocks and methods can ensure that operations execute atomically
2. Visibility (memory visibility): when a thread modifies the state of an object, other threads can actually *see* the changes that were made

### 1. VISIBILITY

There is *no* guarantee that the reading thread will see a value written by another thread on a timely basis, or even at all. In order to ensure visibility of memory writes across threads, you must use synchronization

**Reordering**

- There is no guarantee that operations in one thread will be performed in the order given by the program, as long as the reordering is not detectable from within *that* thread—*even if the reordering is apparent to other threads*

- In the absence of synchronization, the Java Memory Model permits
  - compiler to reorder operations and cache values in registers
  - CPUs to reorder operations and cache values in processor-specific caches

**Stale Data**

- Without synchronization, *every time a variable is accessed*, it is possible to see a stale value for that variable
- *Stale data can cause serious and confusing failures such as unexpected exceptions, corrupted data structures, inaccurate computations, and infinite loops*

**Nonatomic 64-bit Operations**

- *out-of-thin-air safety*, is not guaranteed for 64-bit numeric variables (`double` and `long`) that are not declared `volatile`

**Locking and Visibility**

- When thread *A* executes a `synchronized` block, and subsequently thread *B* enters a `synchronized` block guarded by the same lock, the values of variables that were visible to *A* prior to releasing the lock are guaranteed to be visible to *B* upon acquiring the lock
- The reading and writing threads must synchronize on a common lock

**Volatile Variables**

- weaker form of synchronization, *volatile variables*, to ensure that updates to a variable are propagated predictably to other threads
- volatile variables are not cached in registers or in caches
- from a memory visibility perspective, writing a volatile variable is like exiting a `synchronized` block and reading a volatile variable is like entering a `synchronized` block
- ***Good use:*** ensuring the visibility of their own state, that of the object they refer to, or indicating that an important lifecycle event (such as initialization or shutdown) has occurred (e.g. checking a status flag to determine when to exit a loop) - ***completion, interruption, or status flag***
- ***Bas use:*** verifying correctness, volatile variables can only guarantee visibility and not atomicity
- criteria to be met for using volatile variables:
  - Writes to the variable do not depend on its current value, or you can ensure that only a single thread ever updates the value
  - The variable does not participate in invariants with other state variables
  - Locking is not required for any other reason while the variable is being accessed



