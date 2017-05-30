## Condition synchronization
Needed when a task wishes to perform some action that requires another task to be in some predefined state, or after having performed some action.

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
