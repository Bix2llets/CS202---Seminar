# Builder

## Problem

Suppose there is an generic object, which has a great diversity in concrete implementation, that is, the object has a great amount of attributes that need to be specified by programmer. For example, a generic class `Truck` can has many variation:

- Has engine
- is pulled by another truck
- Has driver's accommodation
- Has roof
- Has freezer
- Has back mounted equipment
- Has side mounted equipment
- Has trailer connector to pull other trucks
- Has spare wheels compartment
- etc

Each of the these attributes might be also an object, which further complexify the creation of `Truck`.

## The naive solution

To construct the concrete objects that can cover all of these variations, a solution is that we create a class for each of them. However, it is impractical since for each combination of attributes, we need a concrete class for them. Therefore, the number of classes that we need to declare increases exponentially.

Another solution is that we can let the constructor construct the object itself. However, to provide enough information to build up such object, the number of parameter needed is large, thus make the code less readable with long constructor call.
To shorten it, we can use the default parameters. However, default parameters only allow us to omit a continuous block of arguments at the end. Therefore, omitting some argument in the middle is not an option with this approach. Moreover, there are cases where some argument are rarely used throughout the development, thus it is questionable of if the corresponding parameter helps with construction of object.

An example for this is

```mermaid
classDiagram
class Truck{
    -hasEngine: bool
    -engine: Engine
    -pulledBy: Truck
    -hasDriverLivingArea: bool
    -hasRoof: bool
    -hasFreezer: bool
    -hasBackMountedEquipment: bool
    -hasSideMountedEquipment: bool
    -hasTrailerConnector: bool
    -pulling: Truck
    +Truck(hasEngine: bool, engine: Engine,
    pulledBy: Truck, hasDriverLivingArea: bool, 
    hasRoof: bool, hasFreezer: bool, 
    hasBackMountedEquipment: bool ,
    hasSideMountedEquipment: bool ,
    hasTrailerConnector: bool, pulling: Truck
    )
}
```

## The design pattern

This design pattern further extends the latter solution mentioned in the previous section. The construction of the generic class is controlled by middlemans, called the Builders.

The Builders provide an interface for programmer to build up complex objects with short and clean code. Typically, programmers interact with interfaces provided to create object steps by steps. In this way, programmers don't need to specify argument that is defaulted to values i.e. 0 for element that does not exist in the object or nullptr for pointer that is not going to be used.

Multiple builders also help with different implementation of building the object by controlling and encapsulating some argument of the original constructor. For example, in `BuilderEngineV8` class, the method `addEngine()` will make the truck has the V8 engine, but in the class `BuilderRocketEngine`, the method `addEngine()` will assemble the rocket engine to the truck.

The general structure of a Builder class is:

```mermaid
classDiagram
class Builder {
    building: ObjectType
    +reset()
    +getObject() ObjectType
    +addFeature1()
    +addFeature2()
    +addFeature3()
}
```

Builders' construction of objects can also be abstracted further with Directors. They are classes that provides methods of invoking a sequence of methods of a builder class and return the result object. Using such methods allows reuse of construction of a particular object throughout the program.

## Report content

1. A realworld problem (that needs your pattern to solve)
1. A naive solution (without your pattern)
1. Some problems occur with your naive solution
1. An introduction about your pattern
1. A general class diagram
1. A class diagram for your problem in step 1
1. The implementation of your pattern
1. Pros and cons of our pattern
1. Some other realworld problems, how the design patterns are applied in web dev, mobile dev
1. Quiz: 5-8 questions, ABCD choices, with small gifts (candies, cakes, hugs, kisses...) for correct answers

### 1. Real world problem

In a shooter game, there are various guns that belong to different types. Each of them can be customized with attachments. For simplicity, we assume every gun has the same set of possible attachments.

A gun can have attachments to be installed on. Some of them might have their own functions: the optical sight has toggleable lighting, the underbarrel utility can be used, etc. . Player can freely customize their weapon, hence there are a lot of variety of guns that we need to deal with.

### 2. Naive solution and their drawbacks

A naive solution for this problem is that we manage the attachment via attributes of the `Gun` class.

```mermaid
classDiagram
class Gun{
hasOpticalSight: bool
hasUnderbarrel: bool
hasStock: bool
hasExternalMagazine: bool
hasGrip: bool

opticalSight: OpticalSight
underbarrel: Underbarrel
stock: GunStock
externalMagazine: ExternalMagazine
grip: GunGrip

+Gun(hasOpticalSight, opticalSight, hasUnderbarrel, underbarrel,  hasStock, stock, hasExternalMagazine, externalMagazine, hasGrip, grip)  }
```

With this approach, it is easy to encapsulate all needed data into the `Gun` class.
However, the constructor of this class, if needed all information to initialize them, is too long for a clean and maintainable code.

For example, to create a gun with holographic sight and wooden stock, the following constructor is called
`Gun(true, holographicSight, false, nullptr, false, nullptr, true, woodenStock, false, nullptr, false, nullptr)`

As we can expect, there are too much parameter for the constructor. Moreover, it reduces its readability as reader won't know what `false` and `nullptr` stands for.

We can use the feature of default parameters to reduce the number of arguments needed to be provided, however this only allows us to omit arguments at the end of the constructor.

Another approach is that we use concrete classes to narrow down the amount of attribute used for tracking the attachments. With this approach, we need a unified interface to create them as they all are the derived classes of `Gun`. The more specific the concrete classes are, the more classes we need to create, and the longer their names are in order to describe which type of gun will be created with them. For example, the name for the class in the previous example will be `GunOpticalStock`. A name for the class of gun with all attachments possible can be `GunOpticalUnderbarrelStockExMagGrip`. Such classes' names are hard to read hence also reduce the maintainability of the program. Furthermore, the number of classes increases exponentially.

### The Builder design pattern

This pattern is about separation of construction of complex objects into sequential steps and manage them with middlemen, called the Builders. They serve as an abstraction layer, hiding the detailed construction of object while providing a unified interface for user to build up the desired object step by step. Moreover, the builders can also enforce some parameter during the construction of object, hence there might be many builders for one object.

To reuse a specific sequence of steps in building an object, we can also use another type of class, called the `Director`. The `Director` provide methods to invoke a sequence of construction steps in the `Builder` associated with it. It can also change the builder used in the sequences, provided that they share the same interface. Hence, the `Director` can also be used to construct different kind of classes if their `Builder` share the same base classes

```mermaid
classDiagram

class ExampleClass
class BuilderBase{

}
```
