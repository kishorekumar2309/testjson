# Thread Safety

Managing access to *state*, and in particular to *shared, mutable state*.

3 ways to ensure thread safety:

1. *Don't share* the state variable across threads
2. Make the state variable *immutable*
3. Use *synchronization* whenever accessing the state variable

### 1. WHAT IS THREAD SAFETY?

- Correctness means that a class *conforms to its specification*. A good specification defines *invariants* constraining an object's state and *postconditions* describing the effects of its operations
- A class is *thread-safe* if it behaves correctly when accessed from multiple threads, regardless of the scheduling or interleaving of the execution of those threads by the runtime environment, and with no additional synchronization or other coordination on the part of the calling code
- Stateless objects are always thread-safe

### 2. ATOMICITY

- **Race Condition**
  - check-then-act (lazy initialization: check whether an object is null and create only one)
  - read-modify-write (increment / decrement operator)
- **Race Conditions in Lazy Initialization**
- **Compound Actions**

### 3. LOCKING

- **Intrinsic Locks**
  - Every Java object can implicitly act as a lock for purposes of synchronization: these built-in locks are called *intrinsic locks* or *monitor locks*
  - The only way to acquire an intrinsic lock is to enter a synchronized block or method guarded by that lock
- **Reentrancy**
  - Locks are acquired on a per-thread rather than per-invocation basis

### 4. GUARDING STATE WITH LOCKS

- For each mutable state variable that may be accessed by more than one thread, *all* accesses to that variable must be performed with the *same* lock held
- Acquiring the lock associated with an object does *not* prevent other threads from accessing that object—the only thing that acquiring a lock prevents any other thread from doing is acquiring that same lock

### 5. LIVENESS AND PERFORMANCE

- Should be aware of what the code in the block is doing and how likely it is to take a long time to execute
- Holding a lock for a long time, either because you are doing something compute-intensive or because you execute a potentially blocking operation, introduces the risk of liveness or performance problems
- Avoid holding locks during lengthy computations or operations at risk of not completing quickly such as network or console I/O
