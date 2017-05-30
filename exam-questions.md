### Synchronization primitives in Java
* __Synchronized methods__
  * Any method can be denoted `synchronized`, which means that it will run under mutual exclusion with every other synchronized method.
* `wait():` A call to wait will suspend the current thread. It will be resumed by a call to `notify()` or `notifyAll()`, or somebody calling `interrupt()` on the thread.
* `notify()` wakes an arbitrary thread blocked by this objects lock.
* `notifyAll()` wakes all threads blocked by this objects lock.


### Synchronization primitives in Ada
#### __Protected Objects__
Protected objects encapsulates data and allows only **mutually exclusive** access.
  * A module, private variables, functions, procedues and entries.
  * __Functions__ are read-only and have no side effects. They do not change the private variables of an object. They can therefore be called concurrently by several tasks, but not concurrently with procedures and entries.
  * __Procedues__ may have side effects, hence they can make changes to the state of the object. They have to run under mutual exclusion with other tasks.
  * __Entries__ Protected by a boolean guard. Looks like a procedure call, but when the guard evaluates to false, the calling task is suspended (blocked). Note that guards cannot test on entry *parameters*.


### Deadlocks
* Deadlocks and race conditions can happen in a message passing system.
* **Detecting deadlocks**
  * Timeout/watchdog.
* **Handle deadlocks**
  * Break mutual exclusion
  * Preemption of resources. Aborting/restarting threads.
    * How do we preempt without leaving the system in an inconsistent state?
      * Structure the system functionality into atomic actions/transactions.

### Misc
* Transactions have only one error mode, abort. Aborting may not be an option when you have a deadline.
* Why is a system that guarantees that all deadlines are met often infeasible and/or too conservative?
  * Theoretical worst-case execution time often enormously large in part because of hardware effects.
  * Thread interaction may be so complicated that they are excluded from the scheduling proof.
