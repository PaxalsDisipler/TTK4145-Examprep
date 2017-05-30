<!---
### Schedulabilty proofs

### Response time analysis
-->
### Priority Inversion
Lets assume we have a system consisting of three processes with different priorities, `T3` with hightest priority and `T1` with lowest, and one shared resource `R1` protected by a lock.

![](inversion.png)

 `Priority inversion` is the following scenario (see figure):
 1. The scheduler see `T1` is the only one who wants to run, and thus let `T1` run.
 2. `T1` grab a hold of the lock for `R1`
 3. `T3` suddenly want to run, and having a higher priority the scheduler preempts `T1` and let `T3` run instead.
 4. `T2` want to run, but having a lower priority than `T3` it is put on hold.
 5. `T3` try to grab a hold of `R1`, but find that it's locked and blocks.
 6. The scheduler see that `T3` is blocked, and let `T2` run instead (as `T2` now is the highest priority non-blocked task that want to run).
 7. `T2` finishes, and the scheduler find that the only non-blocked task eligible for running is `T1`.
 8. `T1` gets to run, and release `R1` when it's done.
 9. The scheduler immediately see that `R1` is unlocked and let `T3` run.

 ##### So to recap what actually happened here:
 * `T2` finished first
 * `T1` finished second
 * `T3` finished last.

 **This clearly violate our predefined priority-ordering: `priority inversion`!**

 There are two variants of the priority inversion problem. **Bounded priority inversion** is simply the situation where a lower priority task gets to run because it hold a lock that a higher priority task have blocked on. This would equal the example above if you were to remove the `T2` task. The duration of the priority inversion would then be given by the time spent in the `critical section` of the low priority task. Bounded priority inversion is generally not a big deal as long as the low-priority task executes in a timely manner.

 **Unbounded priority inversion** may as the name implies last for an indefinite amount of time, and can completely starve the higher priority task, and is a far more severe situation. The example above illustrates this. If there were several intermediate priority tasks the scheduler might jump back and forth between these for any arbitrary amount of time, effectively completely killing the high priority task.

<!---
### Ceiling/inheritance protocols
* How it solve unbounded priority Inversion
* How ceiling protocol avoid deadlocks
-->
