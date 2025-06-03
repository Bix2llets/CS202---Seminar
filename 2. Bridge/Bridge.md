# Bridge

## Introduction

Suppose we are designing a renderer that can work across multipe drivers as well as multiple operating system with just an interface for user to call for.

## Naive solution

We implement concrete classes corresponding to each combination of platform and operating system. In each class, we implement the suitable rendering method.

The structure can be visualized in the following UML diagram

```mermaid
classDiagram

class RenderInterface {
    <<interface>>
    +render()
}

MacOpenGLLine     ..|> RenderInterface 
WindowOpenGLLine  ..|> RenderInterface 
LinuxOpenGLLine   ..|> RenderInterface 
RenderInterface     <|.. MacDirectXLine
RenderInterface     <|.. WindowDirectXLine
RenderInterface     <|.. LinuxDirectXLine
MacOpenGLPolygon     ..|> RenderInterface 
WindowOpenGLPolygon  ..|> RenderInterface 
LinuxOpenGLPolygon   ..|> RenderInterface 
RenderInterface     <|.. MacDirectXPolygon
RenderInterface     <|.. WindowDirectXPolygon
RenderInterface     <|.. LinuxDirectXPolygon
```

In reality, this solution is impractical when the number of drivers, platforms or shape rises as there are multiples classes for just one shape. Not only there are more work to cover such classes, the cost of maintaining and debugging them also skyrockets due to duplicated code and tight coupling of diffrent logics.

## The solution with Bridge Pattern

We see that the naive solution coupled the driver, the platform and the shape together. We can instead detach them and let them communicate with each other via designated interfaces.

The bridge pattern is designed to give another solution instead of coupling the abstraction and the implementation of an object. In the aforementioned design pattern, each of them are developed and functions independently, with the implementation designed for the abstraction in mind. This would significantly reduce the number of classes from $N \times M$ ($N$ implementations, $M$ abstractions) to $N + M$.  

```mermaid
classDiagram
class Abstraction {
    <<abstract>>
    - implement: Implementation
    + action1()*
    + action2()*
    + action3()*
} 

class ConcreteAbstraction1 {
    + action1()
    + action2()
    + action3()
}

class ConcreteAbstraction2 {
    + action1()
    + action2()
    + action3()
}
Abstraction <|-- ConcreteAbstraction1
Abstraction <|-- ConcreteAbstraction2
Abstraction --> Implementation: Bridge
class Implementation {
    <<interface>>
    + operation1()
    + operation2()
}

Implementation <|-- ConcreteImplementation1
Implementation <|-- ConcreteImplementation2

class ConcreteImplementation1 {
    + operation1()
    + operatoin2()
}

class ConcreteImplementation2 {
    + operation1()
    + operation2()
}
```

## Applying to the original problem

To solve the problem we have introduced in the first section, we separate the classes into shapes, operating systems and drivers specific classes.

```mermaid
classDiagram

class Shape {
    <<abstract>>
    - driver: Driver

    + render()*
}

class Driver {
    <<abstract>>
    = OS: System
     
    + sendDataToSystem()*
}

class System {
    <<interface>>
    + sendDataToGPU()*
}
Shape --> Driver: Bridge
Driver --> System: Bridge

Shape <|.. Line
Shape <|.. Polygon

Driver <|.. DirectX
Driver <|.. Vulkan
Driver <|.. OpenGL

System <|.. MacOS
System <|.. Window
System <|.. Linux

```

## Advantage and disadvantage of the pattern

### Advantage

- **Extensible and indepedent relation between abstraction and implementation**
The abstractions are loosely coupled to implementations instead of permanent binding. The implementation used for each abstraction is flexible and can be easily changed runtime-wise.
- **Separation of concern and responsibility**: Each part of the Bridge pattern has functionality and their range of responsibility smaller. This increase the maintainability of code as it can be easity tested. 

### Disadvantage

- **Not a universal solution**: This pattern might backfire when the implementations and abstractions has strict compatibility requirements. To solve this, we need overhead validation of compatibiity. Moreover, if the checking are not conducted fully, runtime error might happens.
- **Slightly worse performance**: The method relies on inheritance and composition, which display worse performance which might be inefficient during work with limited resources