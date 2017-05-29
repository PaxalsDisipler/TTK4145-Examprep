# TTK4145-Examprep

## Transaction Fundamentals
### Locking
Locking ensure that intermediate states does not propagate out of our transaction. There should not be any communication with anyone outside of the transaction.
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
    1. Participants do as they are told and release locks they may hold
    2. Participants sends `ack` to the coordinator when they are done.
    3. When all participants have acknowledged, the coordinator completes the transaction leaving the system in a
  * If **any** participant answers *no*
    1. The coordinator send `rollback` to all participants.
    1. All participants undo any changes it may have made, and release any locks it may hold
    2. Participants send `ack` to the coordinator indication that they have rolled back.
    3. When all participants have acknowledged that they have rolled back, the coordinator closes the transaction leaving the state unchanged.

###### Optimizations
* **Presumed abort**: the participant assume that the transaction will fail and do as little work as possible before it get confirmation from the coordinator that it should commit. If state changes are saved in incremental logs, actually writing these log-entries can wait until the transaction coordinator confirms that everyone should commit.  If the assumption of failure is correct the participant have now avoided doing work it otherwise would have to undo (delete/rollback etc), and if the assumption were wrong we still only do the work once.

* **One-Phase**: If there are only one participant in a transaction there's no need to wait for anyone. The coordinator simply ask the participant to commit right away.

* **Read-Only**: If a participant in a transaction only read stuff, it consequently does not make any state changes and does not need to bother itself with the result of the transaction and can simply ignore the _commit phase_

###### Interposition
Delegation of coordination responsibilities in a transaction is called _interposition_. Reasons for doing this include:
* Two or more resources are co-located far from the coordinator.
* Scoping - you don't want the coordinator to know about the implementation of all resources and "hide" this behind a interpositioned coordinator.
![](fig-interpositions.png)

### Transaction Manager
Responsibilities:
* Create Transactions
  * Generate transactions IDs `tid`
* Keeps track of participants
* Add participants when needed
* Decide *commit/abort*


### Resource Manager = "_transaction participant_"
Responsibilities:
* Holds locks for it's part of the transaction.
* Keep track of its own recovery points (if applicable)
* Participate in the _prepare-to-commit_ phase
* **Do the actual work**

### Log

### Checkpoints

### Log Manager

### Lock Manager

### Optimistic Concurrency Control

### Heuristic Transactions

###
