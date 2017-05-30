# TTK4145-Examprep

## [Transaction Fundamentals](transaction-fundamentals.md)


## Atomic Actions

#### Atomic actions and forward error recovery
* Backward error recovery enables recovery from errors, but it is difficult/impossible to undo an operation performed in the environment in which the embedded system operates.
* We must consider forward error recovery and exception handling.
* When an error occurs, every task in an atomic action must participate in the recovery.
* The exception is raised in all active tasks in the action.
* The exception is `asynchronous`, since it originated in another task.

### Asynchronous Notification
Two basic models of asynchronous notification, resumption and termination.

#### Asynchronous transfer of control


##### Why avoid asynchronous transfer of control?
* Leaves us in an undefined state.
* ATC is okay if we have full control of the changed state and a lock manager which can unlock on behalf the killed thread.

## Fault Tolerance
* If faults occur we want to continue operation, albeit at a lower capacity.
* The system should behave as specified even in the presence of bugs.
* Testing can only show the presence of bugs, not the absence.
* Testing cannot ensure a fault-free system!


## Modelling of Concurrent Programs

#### Concurrency
* Two events are `concurrent` if we cannot tell by looking at the program which will happen first. Concurrent programs are non-deterministic, which makes the hard to debug.

#### Synchronization Constraints
Requirements pertaining to the order of events.

#### Semaphores
Benefits of semaphores
* Simplify the protocols for synchronization.
* Remove the need for busy-wait loops.

__Serialization__: A must happen before B. In sequential programs we know the order of events, in concurrent programs we do not. The use of `semaphores` for signaling can guarantee that a section of code in one thread runs before a section of code in another thread. This solves the serialization problem.

To say that a thread `blocks` itself:
* Notifies the scheduler that it cannot proceed. The scheduler will thus prevent the thread from running until an event occurs in which the thread becomes unblocked.

##### Consequences of the semaphore definition
There is generally no way of knowing before a thread decrements whether it will block or not. After a thread increments the semaphore and another thread gets woken up, both continue running concurrently. __No way of knowing which, if either, will continue immediately__. Moreover, when you signal a semaphore, you don't necessarily know whether another thread is waiting, the number of unblocked threads may be zero or one.

##### Advantages of semaphores
* They impose deliberate constraints which help programmers avoid errors.
* Solutions with sempahores are often clean and organized, which makes it easy to prove correctness.



##


### Race Condition
* Two or more threads can access shared data and they both try to access it at the same time.
* You don't know the order in which the threads will try to access the data.
* Thread scheduling algorithm may switch between threads at any time.
* The result of change in data is dependent on the thread scheduling algorithm.
  * Both threads 'racing' to change the data.
* To prevent race conditions
  * Put locks around the shared data to ensure only one thread can access it at the same time.



## Exam Questions

### Synchronization primitives in Java
* __Synchronized methods__
  * Any method can be denoted `synchronized`, which means that it will run under mutual exclusion with every other synchronized method.
* `wait():` A call to wait will suspend the current thread. It will be resumed by a call to `notify()` or `notifyAll()`, or somebody calling `interrupt()` on the thread.
* `notify()` wakes an arbitrary thread blocked by this objects lock.
* `notifyAll()` wakes all threads blocked by this objects lock.


### Synchronization primitives in Ada
#### __Protected Objects__
  * A module, private variables, functions, procedues and entries.
  * __Functions__ are read-only and have no side effects. They do not change the private variables of an object. They can therefore be called concurrently by several tasks, but not concurrently with procedures and entries.
  * __Procedues__ may have side effects, hence they can make changes to the state of the object. They have to run under mutual exclusion with other tasks.
  * __Entries__ Protected by a boolean guard. Looks like a procedure call, but when the guard evaluates to false, the calling task is suspended (blocked).


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
