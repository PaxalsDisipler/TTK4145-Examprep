###### Difference in design of message based interaction and shared variable synchronization
Shared variable synchronization focuses on



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
