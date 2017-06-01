## Fault Tolerance
The basic idea with building a _fault tolerant_ system is to (ideally gracefully) handle both foreseen **and** unforeseen errors in the system. As the name hints at the unforeseen errors are the most challenging to handle.

* If faults occur we want to continue operation, albeit at a lower capacity.
* The system should behave as specified even in the presence of bugs.
* Testing can only show the presence of bugs, not the absence.
* Testing cannot ensure a fault-free system.
* Testing cannot find errors in the specification.
* Testing cannot find race conditions.

Testing is not good enough! We must handle all errors, also the unexpected ones.

#### How do we handle all the bugs still left in the system after testing?
* __Fault tolerance__.
* __Acceptance tests__ and __redundancy__.
* __Merging of failure modes__.
  * Simpler overall system.
  * Simpler error handling.
  * Better modules.
  * Handling of unknown errors.

Examples of merging of failure modes
* __Communication error__: Lost/corrupted/too late message -> resend
* __Software bugs__: Restart
* __Exceptions__: Abort/Retry

### Terminology
* **Fault**: the cause for an error. The actual bug, shortcoming, whatever that are the root cause for the problems.
  * **Transient faults**: fault that suddenly occurs, remain in the system for a while and then disappear again. Ex: faults due to external interference that come and go.
  * **Permanent faults**: faults that does not disappear if it first appears. Ex: software bugs, broken wire.
  * **Intermittent faults**: faults that occur from time to time. Ex: hardware that fail when it heats up.
* **Error**: the event that a fault actually causes the system to misbehave.
*  **Failure**: the deviation from the correct behavior the error causes.
* **Failure mode**: ways in which we can fail
```
┌───────────┐               ┌────────────┐               ┌────────────┐               ┌───────────┐
│           │  activation   │            │  propagation  │            │  causation    │           │
│   fault   │──────────────▶│    error   │──────────────▶│  failure   │──────────────▶│   fault   │
│           │               │            │               │            │               │           │
└───────────┘               └────────────┘               └────────────┘               └───────────┘
```

### Fault tolerance techniques
There are several techniques and tactics a programmer may choose in order to obtain some degree of fault tolerance in a system. All such techniques introduce some form of **redundancy** to the system in order both detect and recover from faults.

##### Process pair
One of the simplest forms of redundancy is the _process pair_. With this scheme redundancy is introduced to a component by duplication. The original component is more or less kept as before, but an extra "identical" component is added as a backup. The key idea is that as soon as the primary experience an error, the backup kicks in and take over the responsibilities of the primary.

While the key idea is simple enough, there are some points to consider with this setup:
* **The backup need some way of knowing when the primary malfunctions**. One way is for the primary to send **heartbeats** to the backup to signal that it's alive.
* **If the component have an internal state the backup need to know about this in order to seamlessly take over.** This may be solved by sending the complete state as part of the heartbeats that the primary sends, or if the state is too large for this to be feasible just send _deltas_ (changes to the state).
* **You  may want to implement the primary and backup differently to prevent both having the same fault.** If the backup is an identical clone of the primary, and not just a duplicate of the functionality you don't really do anything to mitigate faults stemming from software bugs, specification errors etc. You may also want to consider placing the backup in a different physical location and/or environment if this is possible.
* **A process pair may simplify and help both maintenance and future upgrades.** With a setup that provide one layer of redundancy provides a huge convenience if you want to repair or upgrade the component, as you simply can yank one out, swap it with a new one, and do the same thing with the other without ever having to shut down the system.
* **A process pair may provide provide excellent _availability_**. Ideally when an error have occurred in the primary, the backup steps right in minimizing any service downtime. 

##### N-version programming
Expanding on the key idea of _process pairs_, **N-version programming** can be a fruitful technique for components doing critical computations. As the name imply _**N**_ functionally equivalent programs do the same task, where _**N**_ is ideally greater than 2 and often an odd number to ensure a majority. All results are at the end of the computation compared, and a majority vote decide what result that gets passed on from the component.

```
Example: All should compute f(x)=y 
                                        cosmic ray                         
                                            ╱                              
                                           ╱                               
                                          ╱                                
                                         ╱                                 
                          ┌─────────────╳────────┐                         
                          │                      │                         
                 x        │                      │  z                      
            ┌────────────▶│      version 1       │────────────┐            
            │             │                      │            │            
            │             │                      │            │            
            │             └──────────────────────┘            │            
            │             ┌──────────────────────┐            Λ            
            │             │                      │           ╱ ╲           
   x        │    x        │                      │  y       ╱   ╲   y      
────────────┼───────────▶ │      version 2       │─────────▕     ▏────────▶
            │             │                      │          ╲   ╱          
            │             │                      │           ╲ ╱           
            │             └──────────────────────┘            V            
            │             ┌──────────────────────┐            │            
            │             │                      │            │            
            │    x        │                      │  y         │            
            └────────────▶│      version 3       │────────────┘            
                          │                      │                         
                          │                      │                         
                          └──────────────────────┘     
```

Again are the key conceptually pretty straight forward, but any implementation must be subject to several considerations:
* **Floating points introduce inaccuracies that make comparison less trivial.** If the output from each version is an integer any comparison is a piece of cake, but any floating point have a finite precision and direct comparison of two floats are generally a bad idea. Instead one must resort to comparing values to each other inexact with some threshold, introducing extra complexity (rarely a good thing...). Furthermore if the computation rely on some form of conditional program flow, a small difference in floating point-rounding in the different versions can make one version take a completely different execution path than the others and thus get a wildly different answer at the end.
* **The versions should fail completely independently - use different programming languages etc.** N-version programming hinges on the assumption that the problem can be fully specified and the different versions should therefore be possible to develop completely independently from each other using different development environments, languages etc. Ideally they should also be distributed across different hardware and placed in different physical environments to protect against physical faults.
* **With N-version programming you are basically doing N times the work at N times the expense**. This may seem obvious, but redundancy come at a cost, both in $$$ but also in increased complexity.



### Error detection
Two main classes
* __Environmental detection__
  * Errors detected in the environment in which the program executes. Include errors detected by hardware and the run time support system.

* __Application detection__
  * Errors detected by the application itself. Some error checks are
    * __Replication checks__
      * N-version programming can be used to provide error detection.
    * __Timing checks__
      * Watchdog timer process, if not reset within a certain period by a component, we assume that the component is in error. The software component must continually reset the timer to indicate that it is functioning correctly. Timing checks do not ensure that the component is functioning _correctly_, only that it is functioning on _time_.
    * __Reversal checks__
      * Given the output, calculate what the input should be, compare with actual input.
    * __Coding checks__
      * Test for corruption of data. I.e. checksums.
    * __Reasonableness checks__
      * Check that the state of data or value of an object is reasonable, based on its intended use.
    * __Structural checks__
      * Check the integrity of data objects such as lists or queues.
    * __Dynamic reasonableness checks__
      * Output emitted from some digital controller, usually some relationship between consecutive outputs.

We may also detect unexpected errors by the use of __acceptance tests__. We give demands to the correct state/result rather than testing on error situations. Lastly, we may have __static redundancy__. Given intermittent errors or independent systems, this catches errors that would otherwise be impossible.

### Damage confinement and assessment
We must assess the damage that may have occured. Damage confiement is about structuring the system in such a way as to minimize the effects of a faulty component. Two techniques, __modular decomposition__ and __atomic actions__. Interaction with components occurs through well-defined interfaces.

#### Modular decomposition
Break the system into modules. Each component represented by one or more modules.
Provides a _static_ structure where most of the structure is lost at runtime.
#### Atomic actions
A _dynamic_ structure. The activity of a component is said to be _atomic_ if there are no interactions between the activity and the system for the duration of the action. Appears indivisble and instantaneous to the rest of the system. Atomic actions are used to move the system from one consistent state to another and constrain the flow of information between components.

### Error recovery
Two approaches forward and backward error recovery.

#### Forward error recovery
Attempts to continue from an erroneous state by making selective corrections to the system state. Efficient, but system specific and is dependant on accurate predictions of the location and cause of errors.

#### Backward error recovery
Restore the system from the erroneous state to a safe previous state. We then execute an alternate section of the program. It is hoped that the alternative approach will not result in the same fault reaccuring. The point to which we restore is called a __recovery point__, and the act of establishing such points is called __checkpointing__. Necessary to save appropriate system state information at run-time.

The advantage of backward error recovery is that the erroneous state has been cleared and that it does not rely on finding the location and cause of the fault. However, it cannot undo any effects the fault may have had in the environment of the embedded system.
