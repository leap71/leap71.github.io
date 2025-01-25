**[PicoGK.org](https://picogk.org)/coding for engineers**

![Coding for Engineers](assets/CodingforEngineers.jpg)

**[Table of contents](TOC.md)**

# Interfaces

We have covered a lot of ground by now. Time for a recap.

We started with the general concepts of computing, instructions and data. The introduction of object oriented programming married both together.

Classes encapsulate the data and operations we want to perform. When we create an instance of a class, using `new()`, we have an object. We think of an object of a certain type as an opaque black box and use *information hiding* to expose only the *minimum* properties and operations necessary to get the job done. If we exposed all the internals, chances are we would introduce unwanted interdependencies and side effects. That's why we hide as much as possible.

As a consequence, our objects, defined as C# classes, expose a well-defined, minimum footprint to the world.

Inheritance allows us to build a hierarchy of objects, through common ancestors. Each derived class inherits existing functionality and data, but introduces new traits. An `Airplane` shares much with a `Car`, but has many things that are specific to flying through the air. Even more generally, we can often treat it as a `Machine` or `Physicalobject`, and not deal with specifics. Therefore, it makes sense to build a common family tree that contains cars, planes, ships, and other things in a logical hierarchy of base classes.

In the world of object oriented programming, we can store an `Airplane` and a `Car` in the same variable of type `PhysicalObject`, as long as both classes are derived from that common base class. So, we can simplify the parts of our code which do not have to deal with the specifics of airplanes and cars, and just look at them more generally.

This concept of always moving to the simplest object and variable type necessary for functionality guides our thinking and, in practice, drastically simplifies our code.

The concept of *polymorphism* allows us to override the behavior of certain functions, when they are not appropriate for the derived class. So the function `PhysicalObject.PerformMaintenance()` can do entirely different things for a `Car` or an `Airplane`. But the outside interface, the name of the function and its signature, stays the same, and can be called when just dealing with a variable of type `PhysicalObject` generally.

If not for the concept of polymorphism, our code would have to constantly check what type of object we are dealing with, and call special functions for cars, airplanes and ships. Polymorphism does away with that and allows us to specify functions in the base class, that are *overridden* in the derived class.

And lastly, abstract functions and classes allow us to force the author of a child class to implement certain functions. This gives us a way to define a certain way of working in a parent class, without having to provide an actual implementation. The function `Move` in a `PhysicalObject` class, would certainly be abstract, as it has to be implemented very specifically for a `Car` and for an `Airplane`. A non-abstract function `PhysicalObject.Move()` would not know what to do, as it has no idea what "thing" it is actually dealing with, and how to move it.

## Has a..., is a..., behaves like a...

Sometimes we get a bit confused by all this, so let's discuss a way to think about classes.

Let's start with all the variables in a class, the *member variables*. Member variables are storage containers for data which belongs to an object. Slightly confusingly, there is no "data" in C# — only objects. So each member variable actually also contains an object. 

If you have a variable `float m_fVehicleMass`, then the variable contains a floating point number object with represents the mass of a vehicle. While we think of this as "data", because it's a number, it is still an object, and exposes functions on its own. 

This is true for all member variables.

So, for example, a `Car` might have a steering wheel, which is defined in a `SteeringWheel` class.

In your `Car` class definition it may look like this:

```c#
public class Car
{
    SteeringWheel m_oSteeringWheel = new();
}
```

Now your car *has a* steering wheel (stored in the member variable `m_oSteeringWheel`), and you can do something with it. 

The steering wheel *is a part* of the car. But, of course, you don't think of your car *being* a steering wheel. So, you should not consider deriving your car from the class `SteeringWheel` just to gain the functionality of steering. This may sound trivial, but it's actually a common mistake. People sometimes derive from a class that is not supposed to be an ancestor, when it should actually be a variable *contained* in the overarching class. 

The question to ask here, *does it have a ..*. , *or is it a ...*

You use inheritance, when you have a clear answer to the last question. This object *is a* ... , so therefore I am deriving from this base class. An `ElectricCar` *is a* `Car`, so the decision is clear. But an `ElectricCar` *has a* `SteeringWheel`.

Now, sometimes these answers are not so simple, and that leads us to the final essential concept we have to learn, in order to have all the basics in place for coding Computational Engineering Models.

Often you want to group objects by a certain type of behavior, by the common functionality they expose to the world, without forcing them into a linear class hierarchy. In this case you don't care about parent classes, overridden functions etc., you just want to know, whether you can interact with an object in a certain way, through a defined *interface*. When we expose functionality through an interface, we think of our object *behaving like a* "something".

## Interfaces

Interfaces define a group of abstract functions without implementation. By deriving your class from an interface, you are forced by the compiler to implement all of them in the interface. Let's do this:

``` c#
public interface IPoweredObject
{
    void TurnOn();
   
    void TurnOff();
}
```

So, we just defined an interface called `IPoweredObject`, which we can use whenever we have something that requires turning on and off. By convention, we use a capital `I` as prefix for the name of interfaces.

The interface defines two functions which anyone needs to implement who derives a class from the interface. You will note that this is very similar to an abstract class with abstract functions. In fact, if this were all there is to it, there'd be no advantage in using interfaces over abstract base classes.

But there is one important difference:

In C# classes *can only be derived from exactly one base class*. You have a clear family tree, that always leads back to *exactly one* common ancestor. So there is no way for a class to have two base classes. We cannot derive from both the base class `Car` and the base class `ElectricalAppliance`. We have to decide on exactly one ancestral class.

But a class can derive from *multiple interfaces*. So, an object can behave like a *flying thing*, a *movable object*, a *solid part*, a *refuelable machine*, a *mechanical device*, or whatever interface you choose for it — without forcing these functionalities into a class hierarchy.

Interfaces define a public *protocol* to interact with your object. Interfaces don't try to prescribe the family tree. Also, interfaces never contain variables, so they purely deal with functionality, without adding any weight to the class in terms of data members or internal functionality, constructors, etc.

In practice, you use interfaces more often these days rather than building complex class hierarchies. You can add a lightweight interface to any object, without affecting they way you implemented your family tree, so they are a good default choice.

To give you a concrete example. My partner, Josefine, was working on an igniter for a small rocket thruster. The igniter required a spark plug with an M8 thread. Now, we have a library for screws, including an M8 one. But, do you actually derive the spark plug from the M8 screw? A spark plug *is not* a screw, even though it behaves like one for some purposes. So the solution was not to derive the spark plug from the M8 screw, but actually have it implement an M8 interface. Now a spark plug can still be derived from, say, an `ElectricalDevice`, but also behave like an M8 screw when we need it to tell us the geometry of its thread, because it implements the interface.

Interfaces are a very powerful way to group functionality that you want to expose. 

In a way, coming from the engineering side, they are similar to connector types or protocols. So if your device has a [SATA interface](https://en.wikipedia.org/wiki/SATA) (a common interface for storage subsystems) for data retrieval/storage, you don't care whether it's a hard disk, an SSD, or some exotic other data vault. All you know is, you can rely on the SATA interface to expose certain functions, and you can use these functions to store and retrieve information from the device. The device itself can be treated as a black box.

In software, interfaces are exactly that: A contract with the user of your class, that certain functionality is available.

How do interact with a class that derives from an interface? It's very similar to the way we interact with base classes.

```c#
public interface IPoweredObject
{
    void TurnOn();
   
    void TurnOff();
}

public class Toaster : IPoweredObject
{
    public void TurnOn()
    {
        // Do something
    }
    
    public void TurnOff()
    {
        // Do something
    }
}

public class ElectricCar : Car, IPoweredObject
{
    // ... previous implementation
    
    public void TurnOn()
    {
        // Do something
    }
    
    public void TurnOff()
    {
        // Do something
    }
}
```

Now, I can interact with a `Toaster` and an `ElectricCar` in the same way, without trying to figure out, how I could shoehorn them into one common class hierarchy. The `ElectricCar` still has its original class hierarchy, being derived from `Car`, while the `Toaster` is completely independent of that. But they both expose functionality through the `IPoweredObject` interface, so I can work with them in the same way.

How do I interact with a class through the interface? Either by calling the functions directly, as usual. Or by assigning them to a variable of the interface type. This is quite analog to the way you work with base classes.

```c#
ElectricCar oCar = new();
Toaster oToaster = new();

oCar.TurnOn();
oToaster.TurnOn();

IPoweredObject xObject1 = oCar; 
IPoweredObject xObject2 = oToaster;

xObject1.TurnOff();
xObject2.TurnOff();
```

OK, what have we done here. First we created two objects, one `Car` and one `Toaster`.

We invoked the function to turn on the `Car` on and the `Toaster`. Nothing special here, these are regular member functions of the classes, as you would always have them. The compiler just forced us to implement them, because we said we support the `IPoweredObject` interface.

But then we assigned each object to a variable of type `IPoweredObject`. By convention, when we deal with a variable of an `interface` type, we use the prefix `x` (because `i` is already taken by the `integer` data type). You can call your variable whatever you want, of course, as this is just our own coding style.

`xObject1` and `xObject2` only know the object as a `IPoweredObject`. As a result, you can only call the functions implemented in that interface. You are interacting with the `ElectricCar` and the `Toaster` in exactly the same way, even though they are vastly different things. But if all you need is the exposed interface, that's fantastic, because, again, the less you know about the object you are interacting with, the less likely are you to build fragile interdependencies.

If all we want is make sure all the things around us are powered off, we will use the `IPoweredObject` interface to call the `TurnOff()` function and let the object itself figure out, what that means, because it knows best.

Interfaces are great to abstract the interaction with objects in a lightweight manner. While there is overlap with class inheritance, especially abstract base classes, the fact that you can slap an interface onto any object, regardless of where it is coming from, makes it a very powerful tool.

The way you think about interfaces is: *this class exposes a certain common functionality*.

## Summary

So, now we are done with most of the dry stuff. If you made it this far, you are in good shape. 

Let's summarize:

- We can use *inheritance* and *polymorphism* to build up a family tree of classes. When we do this, we think of our class as *being a class of the inherited type*, in other words, an `Aircraft` *is a* `MovingObject`, and a `Car` is also a `MovingObject`.
- When we add member variables to an class, we think of them as our object *having a* certain property. So, our `Aircraft` might *have* two `JetEngine` objects. It's important to remind ourselves not to confuse this with inheritance. We do not inherit our class from a `JetEngine`, just to gain access to propulsion, instead the `JetEngine` is contained in the class as member variable. It's easier to not get confused, when there is more than one, like in the case of having two engines. So when in doubt, just think of whether your object could, for example, have *two* steering wheels. While this may not make sense technically, it immediately clears up that a steering wheel *is a part of your object*, and not a base class.
- To bundle certain common functionality, without building complex interdependent abstract base classes, we can use *interfaces*. Interfaces conveniently package a certain set of functions, which have to be present in the class implementation. Afterwards, we can access our class through the interface. This is similar to abstract functions, but more flexible. 

And with this, we are done with the basics. Next we will build an aircraft.

---

Next: [**Design an aircraft in an afternoon**](7-design-an-aircraft-in-an-afternoon.md)

[Jump into the discussion here](https://github.com/leap71/PicoGK/discussions/categories/coding-for-computational-engineers)

[Table of contents](TOC.md)

---

**[PicoGK.org](https://picogk.org)/coding for engineers**

© 2024 by [Lin Kayser](https://www.linkedin.com/in/linkayser/) — All rights reserved.
