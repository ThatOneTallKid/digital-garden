In Java, daemon threads are low-priority threads that run in the background to perform tasks such as garbage collection or provide services to user threads. The life of a daemon thread depends on the mercy of user threads, meaning that when all user threads finish their execution, the Java Virtual Machine (JVM) automatically terminates the daemon thread.

To put it simply, daemon threads serve user threads by handling background tasks and have no role other than supporting the main execution.

Properties of Java Daemon Thread

No Preventing JVM Exit: Daemon threads cannot prevent the JVM from exiting when all user threads finish their execution. If all user threads complete their tasks, the JVM terminates itself, regardless of whether any daemon threads are running.

Automatic Termination: If the JVM detects a running daemon thread, it terminates the thread and subsequently shuts it down. The JVM does not check if the daemon thread is actively running; it terminates it regardless.

Low Priority: Daemon threads have the lowest priority among all threads in Java.

By default, the main thread is always a non-daemon thread. However, for all other threads, their daemon nature is inherited from their parent thread. If the parent thread is a daemon, the child thread is also a daemon, and if the parent thread is a non-daemon, the child thread is also a non-daemon.

methods:

void setDaemon(boolean status)

JavaScript

Copy

Caption

DaemonThread t1 = new DaemonThread("t1"); t1.setDaemon(true);

​

boolean isDaemon():

Exceptions in a Daemon thread

If you call the setDaemon() method after starting the thread, it would throw IllegalThreadStateException.