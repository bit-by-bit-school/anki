---
name: object oriented programming
---

# What is an abstract class?

An abstract class is a class that cannot be instantiated directly. It defines a mix of normal (implemented) methods and abstract methods that subclasses are required to implement.

# What is abstraction?

Hiding implementation complexity and exposing only the relevant parts of an object's behavior/interface, so a user of the class doesn't need to know how it works internally to use it.

# What is a class?

A class is a blueprint that describes the data (attributes) and behavior (methods) of a single entity or concept.

# What is an object?

An instance of a class — a concrete realization of the class's blueprint.

# What is object oriented programming?

A programming paradigm that structures code around objects — entities that bundle together data and the methods that operate on that data.

# What is encapsulation?

The bundling of data and the methods that act on it into a single unit, with control over which of that data is accessible from outside the class.

# What is inheritance?

Inheritance is the mechanism by which a child class acquires the data and methods of its parent class, enabling code reuse.

# What is multiple inheritance?

A class inherits from more than one parent class.

# What is the diamond problem?

An issue that can occur in multiple-inheritance: class D inherits from B and C, which both inherit from a common ancestor A. If B and C each override a method originally from A, it's unclear which version D should use.

# What is polymorphism?

In OOP, polymorphism is the ability for the same method call to behave differently depending on the runtime type of the object it's called on (e.g. shape.area() runs different code for a Circle vs. a Square).

