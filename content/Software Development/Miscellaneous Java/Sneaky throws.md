In Java, the sn_eaky throw_ concept allows us to throw any checked exception without defining it explicitly in the method signature. This allows the omission of the _throws_ declaration, effectively imitating the characteristics of a runtime exception.

A problem with _sneaky throws_ is that you probably want to catch the exceptions eventually, but the Java compiler doesn’t allow you to catch sneakily thrown checked exceptions using exception handler for their particular exception type.

The _@SneakyThrows_ annotation from [Lombok](https://projectlombok.org/) allows you to throw checked exceptions without using the _throws_ declaration. This comes in handy when you need to raise an exception from a method within very restrictive interfaces like _Runnable._