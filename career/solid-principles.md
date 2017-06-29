##SOLID Principles with ruby examples
**S.O.L.I.D** is an acronym for the first five object-oriented design(OOD) principles. 
These principles, when combined together, make it easy for a programmer to develop software that are easy to maintain and extend. They also make it easy for developers to avoid code smells, easily refactor code, and are also a part of the agile or adaptive software development.

* **S** - Single-responsiblity principle
* **O** - Open-closed principle
* **L** - Liskov substitution principle
* **I** - Interface segregation principle
* **D** - Dependency Inversion Principle

###Single responsibility principle - SRP
####_A class should have one and only one reason to change, meaning that a class should have only one job._

Probably the most well known principle, and one that you should try to adhere to most of the time. It helps keep classes and methods small and maintainable.

Every class should have a single responsibility, and that responsibility should be entirely encapsulated. All its services should be narrowly aligned with that responsibility, this embrace the high cohesion. 

However, the hard part about putting this principle into practice is figuring out just how wide to cast the ‘single responsibility’ net. Taken to the extreme, it actually lead to overly complex systems. There is such a thing as too much decomposition.

```ruby
[ADD CODE EXAMPLE]
```

###Open/closed principle - OCP
####_Software entities should be open for extension, but closed for modification. _
Classes or methods should be open for extension, but closed for modification. An entity can allow its behavior to be extended without modifying its source code. 

We should strive for modular designs that make it possible for us to change the behavior of the system without making modifications to the classes themselves

```ruby
[ADD CODE EXAMPLE]
```


###Liskov substitution principle - LSP
####_Objects in a program should be replaceable with instances of their subtypes without altering the correctness of that program._

The principle states that you should be able to replace any instances of a parent class with an instance of one of its children without creating any unexpected or incorrect behaviors.

```ruby
[ADD CODE EXAMPLE]
```


###Interface segregation principle - ISP 
####_Many client-specific interfaces are better than one general-purpose interface._

States that no client should be forced to depend on methods it does not use. ISP splits interfaces which are very large into smaller and more specific ones so that clients will only have to know about the methods that are of interest to them. Such shrunken interfaces are also called role interfaces. ISP is intended to keep a system decoupled and thus easier to refactor, change, and redeploy.

###Dependency inversion principle - DIP
####_Abstractions should not depend upon details. Details should depend upon abstractions._

Refers to a specific form of decoupling software modules. When following this principle, the conventional dependency relationships established from high-level, policy-setting modules to low-level, dependency modules are inverted (i.e. reversed), thus rendering high-level modules independent of the low-level module implementation details.


```ruby
[ADD CODE EXAMPLE]
```
