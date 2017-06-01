### Synchronization primitives in Java
* __Synchronized methods__
  * Any method can be denoted `synchronized`, which means that it will run under mutual exclusion with every other synchronized method.

  ```java
  // Example
  public class SyncronizedCounter{
    private int c = 0;
    public synchronized void increment() { c++; }
    public synchronized void decrement() { c--; }
    public synchronized int  getValue()  { return c; }
  }
  ```
* `wait():` A call to wait will suspend the current thread. It will be resumed by a call to `notify()` or `notifyAll()`, or somebody calling `interrupt()` on the thread.
* `notify()` wakes an arbitrary thread blocked by this objects lock (although it in most cases be the thread with highest priority)
* `notifyAll()` wakes all threads blocked by this objects lock.


### Synchronization primitives in Ada
* __Protected Objects__ in Ada is somewhat analogous to the `synchronized` tag in Java, and ensure that there are **no (dangeorus) concurrent execution** of `entries`, `functions`* or `procedures` in the protected object. The way it differs from the `synchronized`-tag is that it apply to everything in the object, instead of one having to apply it to each method as shown in the Java example above.
* `functions` are per definition __read-only__ and can therefor be safely called concurrently with each other, but not with `procedures` or `entries`.
* `procedures` are free to make changes (__read-write__) to the protected object and must therefore run only under mutual exclusion.
* `entries` are like `procedures` but their execution is protected by a guard (boolean test formulated using the objects private variables). When the test fail, the entry is not callable and the caller will block until the guard become true. `entries` are the only _entities_ a task may export. This mean that other other task may call exported `entries`, and this way communicate with the other task, this is called **Rendezvous**. The `entry` is executed by __**the called task**__, and __**the calling task**__ is blocked until the call completes. 

<!--
Protected objects encapsulates data and allows only **mutually exclusive** access.
  * A module, private variables, functions, procedues and entries.
  * __Functions__ are read-only and have no side effects. They do not change the private variables of an object. They can therefore be called concurrently by several tasks, but not concurrently with procedures and entries.
  * __Procedues__ may have side effects, hence they can make changes to the state of the object. They have to run under mutual exclusion with other tasks.
  * __Entries__ Protected by a boolean guard. Looks like a procedure call, but when the guard evaluates to false, the calling task is suspended (blocked). Note that guards cannot test on entry *parameters*.
-->

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

##### Why is a system that guarantees that all deadlines are met often infeasible and/or too conservative?
  * Theoretical worst-case execution time often enormously large in part because of hardware effects.
  * Thread interaction may be so complicated that they are excluded from the scheduling proof.

* There are significantly more states in a sempahore-based shared variable system than in a message based system.

##### Why are message passing systems traditionally not seen as well suited for implementing systems with real-time demands?
  * Schedulability proofs are not well developed.
  * Real-time systems have traditionally been closer to hardware, maybe even without an operating system. Hence, the message passing infrastructure might be unavailable.
  * Shared variable based real-time systems often employ one thread per timing demand, and handle threads with priorities. In message based systems we need to create a lot more threads, i.e. for managing resources. This makes it more difficult to assign priorities to threads in any meaningful way.

##### How would you structure your system in order to assure that all real-time demands are met?
* Employ the __shared variable synchronization__ model. One thread per real time demand, assign priorities according to the deadlines (shortest deadline ==> highest priority). Keep track of resource usage for the different threads in order to comment on maximum blocking time.
* To show that all real-time demands are met we estimate the worst-case execution and blocking times and then go through a schedulability proof.


##### What do we gain by merging the failure modes?
* Simplification of the system, if we are handling the worst case errors anyway, maybe we can handle other errors in the same way.
* Error modes are a part of the module interface. Fewer error modes enhance modularity/maintenance by reducing the size of the interface.
* We handle unexpected errors, since the merging of failure modes also can encompass unknown error modes


##### Why do we avoid using `notify()` and rather use `notifyAll()` combined with while-loops around all the `wait()`'s in Java?
* If there are more reasons for waiting, you cannot easily know who is going to get awakened with the use of `notify()`. The system might misbehave if you awaken someone who's waiting for something other than what just got ready. From a module perspective, the use of `notify()` may demand knowledge of the usage patterns of the object -> not particularly good encapsulation!

##### Drawbacks of `notifyAll()`

* If we have a lot of readers and writers, waking all of them only to loop and go to sleep again is wasteful.


##### How do we make the readers/writers locks safe against starvation?
* Keep track of the order of requests. This leads to complete robustness against starvation of any writers. Alternatively we could assume that there are few writers, and hence give writers priority by keeping track of a waitingWriters variable and not letting any readers run while this is non-zero.
