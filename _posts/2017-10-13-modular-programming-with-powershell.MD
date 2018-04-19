---
layout: post
---
# Coupling and Cohesion

Modular programming divides the program into functions and groups them into modules. The best designs minimize **Coupling** between objects and maximize the **Cohesion** within objects. Simply put two objects are tightly coupled if there are many references between them. The two objects know a lot about each other. An object is cohesive if its components serve a single purpose. It is not a good idea to group things together just because they tend to happen at the same time.

These concepts go well with the idea of **Encapsulation**. With encapsulation we hide the details of an object from the outside. This allows us to enforce low coupling by narrowing the interface between the object and the rest of the program. The narrow interface also encourages cohesion because unrelated components will very likely need different information and so can be separated.

These concepts apply to at all scales in a program. A complicated function is often simplified when it is recognized that it is attempting to achieve more than one task simultaneously, and is broken up into more focused sub-tasks each in its own function.
