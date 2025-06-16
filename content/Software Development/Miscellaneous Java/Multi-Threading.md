### Threads

Processes are heavy weight while threads are light weight.
Processes are allocated a separate memory area.

We can create Threads in java using two ways, namely :
1. Extending Thread Class
2. Implementing a Runnable interface

### Thread Functions

- join - waits for a thread to complete its execution
- priority - we can set priority, just a hint, not mandatory
- interrupt - interrupts a thread
- yield - switches the thread
- setDaemon - makes it a low priority background thread

## Executor

[_Executor_](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/Executor.html) is an interface that represents an object that executes provided tasks.**

_Executor_ does not strictly require the task execution to be asynchronous. In the simplest case, an executor can invoke the submitted task instantly in the invoking thread.

## Future

**_Future_ is used to represent the result of an asynchronous operation.** It comes with methods for checking if the asynchronous operation is completed or not, getting the computed result, etc.

What’s more, the _cancel(boolean mayInterruptIfRunning)_ API cancels the operation and releases the executing thread. If the value of _mayInterruptIfRunning_ is true, the thread executing the task will be terminated instantly.

## Multi-Threading with streams

Stream API also simplifies multithreading by providing the _parallelStream()_ method that runs operations over the stream’s elements in parallel mode.

The code below allows us to run method _doWork()_ in parallel for every element of the stream:

```java
list.parallelStream().forEach(element -> doWork(element));
```
