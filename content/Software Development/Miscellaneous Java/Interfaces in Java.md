An Interface acts like a contract for its implementing class.

**It specifies a behavior that its implementing classes must implement without dictating how.**

**Abstraction:** The _interface_ reveals only the essential information needed to invoke the method, whereas the complexities of the implementation remain concealed.

**Multiple Inheritance:** A class can implement several interfaces, thereby avoiding the diamond problem that can arise in languages that allow multiple inheritance from classes.

**Loose Coupling:** Interfaces provide a distinct separation between the functionality and the implementation details. It enables a class to alter its internal processes without affecting its users, as we define the method and the signature separately.

## @Interface Annotation

we use _@interface_ to declare an annotation type

**Annotations provide a way to add metadata to Java code elements like classes, methods, and fields.**

Let’s create an _@Review_ custom annotation, which we can use to track who reviewed a piece of code and when:

```java
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.METHOD})
public @interface Review {
    Stringreviewer();
    Stringdate()default "";
}Copy
```

Notice that when defining the _Review_ annotation using _@interface_, we define it as a regular _interface_. We have method names and return types that use primitive data types or even arrays. However, we cannot use complex objects for return types.

Also, we need to provide values for all the above-defined properties while using our _Review_ annotation. There is a way to define a default value at the time of declaration. We can use it if we don’t always need the value provided while using the annotation.

Also, one more thing to notice is the [_@Retention_](https://docs.oracle.com/javase/8/docs/api/index.html?java/lang/annotation/RetentionPolicy.html) and the [_@Target_](https://docs.oracle.com/javase/8/docs/api/java/lang/annotation/ElementType.html) annotations above the _@interface_ definition. The  _@Retention(RetentionPolicy.SOURCE)_ annotation makes our annotation available in the source code. The _@Target({ElementType.METHOD})_ annotation specifies that the _Review_ annotation will be applied only to the methods in a Java class.

Now, let’s use the _@Review_ annotation in the service method:

```java
@Review(reviewer = "Natasha", date = "2024-08-24")
public Stringservice() {
return "Some logic here";
}Copy
```