> We don’t need to handle this manually, it is a part of JVM.

In java, garbage means unreferenced objects.

Garbage Collection is process of reclaiming the runtime unused memory automatically. In other words, it is a way to destroy the unused objects.

We use free() in C and delete() in C++.

## Advantages

- It makes java **memory efficient** because garbage collector removes the unreferenced objects from heap memory.
- It is **automatically done** by the garbage collector(a part of JVM) so we don't need to make extra efforts.

## How can a object be unreferenced ?

- By nulling the reference
- By assigning a reference to another
- By anonymous object etc.

### finalize() method

The finalize() method is invoked each time before the object is garbage collected. This method can be used to perform cleanup processing. This method is defined in Object class as:

```java
protected void finalize(){}
```

### gc() method

The gc() method is used to invoke the garbage collector to perform cleanup processing. The gc() is found in System and Runtime classes.

```java
public static void gc(){}
```

The garbage collector is the best example of the [[Daemon Thread]] as it is always running in the background.

### Garbage collection in VM

PS Scavenge 
- PS Scavenge is the Young generation collectors
- It is the parallel scavenge collector.
- PS Scavenge uses multiple threads in parallel for garbage collection.
- Vm parameter for enabling PS Scavenge 
```
-XX:+UseParallelGC
```

PS MarkSweep 
- PS MarkSweep is the old generation collector.
- PS MarkSweep is the parallel scavenge mark sweep collector.
- It uses the multiple threads in parallel for garbage collection.
- Vm parameter for enabling PS MarkSweep >

```
-XX:+UseParallelOldGC
```