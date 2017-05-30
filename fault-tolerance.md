## Fault Tolerance
The basic idea with building a _fault tolerant_ system is to (ideally gracefully) handle both foreseen **and** unforeseen errors in the system. As the name hints at the unforeseen errors are the most challenging to handle.

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
We must assess the damage that may have occurred. Damage confinement is about structuring the system in such a way as to minimize the effects of a faulty component.
