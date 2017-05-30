### Threads
A thread is a sequence of instructions that execute serially. If we have multiple threads, the processor can work on one thread for a while, and then switch to another. The OS, specifically the scheduler, decides when each thread gets to run. The programmer has no control over this. For synchronization purposes there is no difference between the parallel and multithread models.

## Synchronization constraints
Requirements pertaining to the order of events. We use synchronization constraints to control concurrent access to shared variables.

### Mutual exclusion
Most common synchronization constraint. Task A and B are mutually exclusive if they cannot happen at the same time. Guarantees that only one thread can access a shared variable at a time. Mutual exclusion can also be implemented using message passing.

### Execution path
Order in which statements are executed.

### Critical region
Code guaranteed to run under __mutual exclusion__.

### Conditional critical region
Critical region with a guard. Must get under the mutual exclusion and pass the guard.

### Condition synchronization
Needed when a task wishes to perform some action that requires another task to be in some predefined state, or after having performed some action.


### Condition variables
A kind of synchronization primitive. `wait` always blocks. `signal` releases a blocked task, if any (otherwise it does nothing). Useful when you have a thread that needs to wait for a certain condition to be true. 

### Busy waiting
* Can implement synchronization by having tasks set and check shared variables acting as **flags**.
* Flag variables are called **spin locks**.
* This approach works well for **condition synchronization**, but no simple method for **mutual exclusion** exists.
* Busy wait algorithms are generally inefficient, as they involve tasks waiting and using up processing cycles without doing useful work.
* Busy waiting can create a **livelock**.

### Race condition
* Two or more threads can access shared data and they both try to access it at the same time.
* You don't know the order in which the threads will try to access the data.
* Thread scheduling algorithm may switch between threads at any time.
* The result of change in data is dependent on the thread scheduling algorithm.
  * Both threads 'racing' to change the data.
* To prevent race conditions
  * Put locks around the shared data to ensure only one thread can access it at the same time.


### Deadlock
Two processes are deadlocked if they are both holding a resource while waiting for a resource held by the other
