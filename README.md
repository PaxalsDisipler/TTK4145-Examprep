# TTK4145-Examprep

## Transaction Fundamentals
### Locking
##### Pessimistic Concurrency Control
Most transaction systems employ _pessimistic concurrency control_ where data structures and/or other resources is accessed the transaction(-participant) lock that resource to prevent others from meddling with it.

###### Two-Phase Locking
In order to preserve the _atomicity_ property of the transaction locks must be acquired and released in two phases. In the _growing phase_ locks may only be acquired, basically accumulating more and more locks. When all the necessary locks are acquired, the transaction enter the shrinking phase, where it may only shed the locks it already have and cannot acquire new ones.

### Two-Phase Commit
_Two-Phase commit_ is a technique to ensure atomicity of transactions, more specifically a protocol that ensure that a transaction either succeed or fail without side-effects. This ensure that the system is in a well defined state regardless of the outcome of the transaction. If the transaction fail all attempted (or actually executed) changes to the system state are rolled back to the state of the system before the transaction began. This is pretty much a classic example of _backward error-recovery_, but may not be that useful in a embedded system setting, as it often is hard/impossible to undo real-life side-effects - pretty hard to _unlaunch_ a rocket...

###### Basic algorithm
* *Prepare to commit-phase*
  1. The transaction coordinator query all participants to check if they are ready/willing/able to commit.
  1. The participants prepare to commit (more on this further down)
  1. The participants answers the query from the coordinator with either "*yes, I can commit*" or "*no, abort!*"
* *Commit-phase*
  * If **all** participants have answered *yes*
    1. Coordinator ask all participants to actually do the commit
    * Participants do as they are told and release locks they may hold
    * Participants sends `ack` to the coordinator when they are done.
    * When all participants have acknowledged, the coordinator completes the transaction leaving the system in a
  * If **any** participant answers *no*
    1. The coordinator send `rollback` to all participants.
    * All participants undo any changes it may have made, and release any locks it may hold
    * Participants send `ack` to the coordinator indication that they have rolled back.
    * When all participants have acknowledged that they have rolled back, the coordinator closes the transaction leaving the state unchanged.

###### Optimizations


###### Interposition

### Transaction Manager

### Resource Manager

### Log

### Checkpoints

### Log Manager

### Lock Manager

### Optimistic Concurrency Control

### Heuristic Transactions

###
