#### Atomic actions and forward error recovery
* Backward error recovery enables recovery from errors, but it is difficult/impossible to undo an operation performed in the environment in which the embedded system operates.
* We must consider forward error recovery and exception handling.
* When an error occurs, every task in an atomic action must participate in the recovery.
* The exception is raised in all active tasks in the action.
* The exception is `asynchronous`, since it originated in another task.

### Asynchronous Notification
Two basic models of asynchronous notification, resumption and termination.

#### Asynchronous transfer of control


##### Why avoid asynchronous transfer of control?
* Leaves us in an undefined state.
* ATC is okay if we have full control of the changed state and a lock manager which can unlock on behalf the killed thread.
