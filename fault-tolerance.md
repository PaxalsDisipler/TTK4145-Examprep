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
* __Communication error__: Lost message -> resend
* __Software bugs__: Restart
* __Exceptions__: Abort/Retry

### Terminology
* **Fault**: the cause for an error. The actual bug, shortcoming, whatever that are the root cause for the problems.
  * **Transient faults**: fault that suddenly occurs, remain in the system for a while and then disappear again. Ex: faults due to external interference that come and go.
  * **Permanent faults**: faults that does not disappear if it first appears. Ex: software bugs, broken wire.
  * **Intermittent faults**: faults that occur from time to time. Ex: hardware that fail when it heats up.
* **Error**: the event that a fault actually causes the system to misbehave.
*  **Failure**: the deviation from the correct behavior the error causes.
```
┌───────────┐                 ┌────────────┐                 ┌────────────┐                 ┌───────────┐
│           │  activation     │            │  propagation    │            │  causation      │           │
│   fault   │────────────────▶│    error   │────────────────▶│  failure   │────────────────▶│   fault   │
│           │                 │            │                 │            │                 │           │
└───────────┘                 └────────────┘                 └────────────┘                 └───────────┘
```




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

We may also detect unexpected errors by the use of __acceptance tests__. We give demands to the corre state/result rather than testing on error situations. Lastly, we may have __static redundancy__. Given intermittent errors or independent systems, this catches errors that would otherwise be impossible.

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
