### S - Single Responsibility

A class should only have one responsibility. Furthermore, it should only have one reason to change.

Benefits :

1. **Testing** – A class with one responsibility will have far fewer test cases.
2. **Lower coupling** – Less functionality in a single class will have fewer dependencies.
3. **Organization** – Smaller, well-organized classes are easier to search than monolithic ones.

### O - Open for extension / Closed for modification

classes should be open for extension but closed for modification. In doing so, we stop ourselves from modifying existing code and causing potential new bugs in an otherwise happy application.

Of course, the one exception to the rule is when fixing bugs in existing code.

Example we have a class guitar

```jsx
public class Guitar {

    private String make;
    private String model;
    private int volume;

    //Constructors, getters & setters
}
```

if we want to add a pattern flame to the guitar, then instead of just addidng an attribute flame to it we should extend it.

```jsx
public class SuperCoolGuitarWithFlames extends Guitar {

    private String flameColor;

    //constructor, getters + setters
}
```

### L - Liskov Substitution

If class _A_ is a subtype of class _B_, we should be able to replace _B_ with _A_ without disrupting the behavior of our program.

```jsx
One of the classic examples of this principle is a rectangle having four sides. 
A rectangle’s height can be any value and width can be any value. 
A square is a rectangle with equal width and height. 
So we can say that we can extend the properties of the rectangle class into square class. 
```

### I - Interface Segregation

Do not force any Class to implement an interface which is irrelevant to them larger interfaces should be split into smaller ones. By doing so, we can ensure that implementing classes only need to be concerned about the methods that are of interest to them.

### D - Dependency Inversion

The principle of dependency inversion refers to the decoupling of software modules. This way, instead of high-level modules depending on low-level modules, both will depend on abstractions.

- In simpler terms, the DIP suggests that classes should rely on abstractions (e.g., interfaces or abstract classes) rather than concrete implementations.
- This allows for more flexible and decoupled code, making it easier to change implementations without affecting other parts of the codebase.