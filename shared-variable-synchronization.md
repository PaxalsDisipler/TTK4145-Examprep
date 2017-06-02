### Threads
A thread is a sequence of instructions that execute serially. If we have multiple threads, the processor can work on one thread for a while, and then switch to another. The OS, specifically the scheduler, decides when each thread gets to run. The programmer has no control over this. For synchronization purposes there is no difference between the parallel and multithread models.

### Synchronization constraints
Requirements pertaining to the order of events. We use synchronization constraints to control concurrent access to shared variables.


### Resources
* Variables that must be protected from concurrent usage are grouped together into named regions and tagged as being resources.


### Mutual exclusion
Most common synchronization constraint. Task A and B are mutually exclusive if they cannot happen at the same time. Guarantees that only one thread can access a shared variable at a time. Mutual exclusion can also be implemented using message passing.

### Execution path
Order in which statements are executed.

### Critical region
Code guaranteed to run under __mutual exclusion__. Programming of a critical section as a critical region meets the requirements of mutual exclusion.

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
* Busy waiting can be desirable in cases where you wish to react as fast as possible changes in the blocking condition. Ie. in those (presumably) rare cases where waiting for the scheduler to handle the unblocking takes too long.
* Some locking-libraries/implementations also use **spin-locking** before a regular lock if the resource cannot be acquired in a reasonable amount of time. This is done to reduce overhead for context-switching in enviroments where locks usually are quickly obtained.

### Suspend and resume
* Motivated by the fact that busy-wait loops wastes processor time.
* Suspend the calling task if the condition for which it is waiting does not hold.
* Suffers from a race condition.
* __Solution__: Two-stage suspend.
* Low-level facility, which may be error-prone in use.


### Race condition
**Race conditions** refer behavior in a system where the result/effects/output are dependent on the sequence or timing of concurrently executing tasks. The most common version of this is the **data race condition**:
* Two or more threads can access shared data and they both try to access it at the same time.
* You don't know the order in which the threads will try to access the data.
* Thread scheduling algorithm may switch between threads at any time.
* The result of change in data is dependent on the thread scheduling algorithm.
  * Both threads 'racing' to change the data.
* To prevent race conditions
  * Put locks around the shared data to ensure only one thread can access it at the same time.

### Deadlock / Livelock
Two processes are **deadlocked**  if they are both holding a resource while waiting for a resource held by the other, and as a consequence neither can procced with its exceution. A **livelock** are similar to a deadlock, except that the states of the processes involved constantly change with respect to each other, but none progressing in its excecution.

_
A real-world example of a **livelock** occurs when two people meet in a narrow
corridor, and each tries to be polite by moving aside to let the other pass,
but they end up swaying from side to side without making any progress because
they both repeatedly move the same way at the same time.
_

### Starvation (indefinite postponement)
* A task wishes to gain access to a resource through a critical section, but is never allowed to do so because there are always other tasks gaining access before it. (Through unfair scheduling)

### Liveness
* If a task is free from livelocks, deadlocks and starvation it is said to possess liveness.
* Implies that if a task wishes to perform some task, it will eventually be allowed to do so. Particularly, if a task requests access to a critical section, it will gain access in finite time.


### Monitors
A **monitor** is a synchronization construct that allows threads to have both mutual exclusion and the ability to block for a certain condition to become true. Monitors also have a mechanism for signalling other threads that their condition has been met. A monitor consists of a mutex (lock) object and condition variables. A condition variable is basically a container of threads that are waiting for a certain condition. Monitors provide a mechanism for threads to temporarily give up exclusive access in order to wait for some condition to be met, before regaining exclusive access and resuming their task.
* Conditional critical regions can be dispersed throughout the program.
* Provides more structured control regions.
* Critical regions are written as procedures and encapsulated together into a single module called a monitor.
* Monitor provides mutual exclusion, but there is still a need for __condition synchronization__ within the monitor.
* __Condition variables__ alleviates the problem of condition synchronization.
* __Criticism__:
  * Monitors do not deal well with condition synchronizations, because they resort to low-level semaphore like primitives.
  * All the criticisms regarding semaphores apply equally to condition variables.
  * Internal structure of monitors may be difficult to understand because of condition variables.


### Barriers
* Mechanism that allows threads to be blocked until a number of them have arrived at the barrier.


#### Common error in synchronization code
* There is a danger of deadlock any time you wait for a semaphore while holding a mutex.
* Be suspicious of functions that locks a mutex, but have several `return`statement...Easy to forget to unlock the mutex at one or more exit points.


#### Lightswitch
* First thread into a section locks a semaphore (or queues), and the last one unlocks it. This pattern is called a lightswitch
```python
class Lightswitch:
  def __init__(self):
    self.counter = 0
    self.mutex = Semaphore(1)

    def lock(self, semaphore):
      self.mutex.wait()
        self.counter += 1
        if self.counter == 1:
          semaphore.wait()
      self.mutex.signal()

      def unlock(self, semaphore):
        self.mutex.wait()
          self.counter -= 1
          if self.counter == 0:
            semaphore.signal()
        self.mutex.signal()
```
