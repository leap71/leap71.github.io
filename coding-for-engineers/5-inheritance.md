**[PicoGK.org](https://picogk.org)/coding for engineers**

![Coding for Engineers](assets/CodingforEngineers.jpg)

[0 — Preamble: My story](0-preamble.md)

[1 — Foreword](1-foreword.md)

[2 — Fundamentals](2-fundamentals.md)

[3 — Running Code](3-running-code.md)

[4 — Classes](4-classes.md)

[**5 — Inheritance**](5-inheritance.md)

# Inheritance

By now, we should be comfortable with the fundamental building blocks we use to create functionality in C#.

We have to identify the logical elements that make up our App. This is actually often simpler for engineering applications, as we are dealing with actual physical objects. In an abstract logical problem, you can easily get lost trying to figure out what constitutes a `class`,

I want to emphasize one important way to thinking that is essential for proper object-oriented programming:

Traditionally you would look at an operation that needs to be performed on some data, and treat the data as a passive entity. In a way, you think "I need to move the car to this position, so I will write a function to move the car from A to B, and pass the car as a parameter." 

The car in this way of thinking, is just a passive thing, and all the work is done by applying some process to it "from the outside".

In object-oriented programming, you keep the functionality close to the object. So the above function would change to "Car, move yourself to this position." Sometimes this may seem a bit counter-intuitive. Isn't the driver the one, who drives the car? Shouldn't there be a `Driver` object that interacts with the car in some standardized way? 

Absolutely, these are valid questions! 

And making the right choices is key to elegant software architecture. A good software architect will always try to minimize the information that needs to be exchanged in order to arrive at a goal. Information hiding, and limiting exposed functionality to the essentials is key to robust code.

Let's look at a powerful concept in our toolbox, which can help us on our quest to simplify our interactions with objects.

## Common ancestors

If you think about the world, the things that surround us have a lot in common. Almost everything is an object of some sort. And these objects have aspects they share. We expect, for example, all physical things to occupy a position in space, have a weight, etc.

So, in a way, it makes sense to group objects in a family tree — similar to what we find in biology. There are plants, there are animals, some animals are mammals, whereas others are fish, etc.

In object-oriented programming, we use *Inheritance* to build up a hierarchy of related objects. We can implement certain common functionality and properties in a *Base Class*. That functionality is then available in all classes that derive from it. 

To illustrate, let's go back to our car example.

Some of the things, like the name of our car, are not unique to the `TeslaRoadster` class. They could be moved to a general `Car` class. And basically everyhing in our current class, maybe with the exception of the implementation of a specific battery degradation formula, could be moved to an `ElectricCar` class that is derived from the more general `Car`. If we wanted to implement different types of cars, this would avoid a lot of repetition of code, and make it incredibly easy to create new classes. All we would have to do is implement the functionality that is special to the new descendant of the base class.

Let's try this.

Here is how our generalized `Car` class could look like:

```c#
public class Car
{   
    public Car(string strName = "Unnamed Car")
    {
        m_strName = strName;
    }

    public string strName()
    {
        return m_strName;
    }
    
    string m_strName;
}
```

So, we moved everything that is common to our generalized car to the base class. In our case this is just the name, but of course it could be whole lot of functionality that is common to driving and interacting with a car.

Now, let's derive our existing class from this ancestor.

We *derive* a class from another class, by adding the ancestral base class with `:` to the class definition.

`public class TeslaRoadster : Car`

This means that all functionality and all data that was created by the base class is now automatically available in the derived class.

In our constructor we have to make sure we call the constructor of the base class. We do this by adding `: base(...)` at the end of the constructor, as below. This passes the name of the car to the base class constructor. If we omit this, the base class constructor will still be called, but it will use its default value, which is not what we want.

```c#
public class TeslaRoadster : Car
{
    public TeslaRoadster(   string  strName         = "Unnamed Tesla Roadster",
                            uint    nPercentFull    = 0) 
        : base(strName)
    {
        m_fBatteryLevel    = nPercentFull / 100.0f;
    }

    public void Charge()
    {
        m_fBatteryLevel     = 1;
        m_nChargingCycles   = m_nChargingCycles + 1;
    }

    public uint nBatteryLevelPercent()
    {
        return Convert.ToUInt32(m_fBatteryLevel * 100.0f);
    }

    public uint nBatteryHealthPercent()
    {
        // Calculate an abstract "health percentage"
        float fHealth = 1.0f - (float) m_nChargingCycles / 10000.0f;
        return Convert.ToUInt32(fHealth * 100.0f);
    }

    float   m_fBatteryLevel;
    uint    m_nChargingCycles = 0;
}
```

So, our derived class no longer deals with the car's name. It *inherits* the functionality from the base class.

So, now we have a new way to think about our `TeslaRoadster` object. Yes, it's a `TeslaRoadster`, but it is also a `Car`. And when I don't need to deal with anything specific to a `TeslaRoadster`, I can just work with `Car` variables.

Let's say, I have a taxi company, and I have an inventory of cars. I could store them in variables of type `Car`, even though clearly, each could be a different model. But for the purpose of counting them, or even renting them out, it could be sufficient if I treat them all the same way, and interact with them using the functionality common to cars.

```c#
using SuperEngineering;

List<Car> aoMyCars = new(); // Create a new list of cars
aoMyCars.Add(new TeslaRoadster("Black Tesla Roadster"));
aoMyCars.Add(new TeslaRoadster("Blue Tesla Roadster"));
aoMyCars.Add(new TeslaRoadster("Green Tesla Roadster"));

foreach (Car oCar in aoMyCars)
{
    Console.WriteLine(oCar.strName());
}
```

Here we have added three cars to a `List` (a storage container for multiple objects). Even though we clearly created a certain type of car (`TeslaRoadsters`), we have added them all to a generic `List`, that holds objects of type `Car`. We have then used a `foreach` loop to output all the cars' names.

For the purpose of printing out the names, we really don't care what specific class of `Car` the `List` actually holds at this position.

## Polymorphism

I always hated that word. It makes something very simple sound complicated.

Polymorphism is the *ability of a derived class to override the functionality of the base class using the same function name*. Bear with me on that one.

Let's imagine we want to provide a common way expose functionality for all objects of a certain base type. But the actual *implementation* may differ. We could have a function `PrepareForDriving`, which in a gasoline car would call the `Refuel` and the `CheckForOilChange` function, but in an electric car, it would only call `Charge`. How can we get there?

We have to create a function in the base class that can be overridden by the derived class, to add specific functionality.

Let's do this, let's add this function to our `Car` class.

```c#
public virtual void PrepareForDriving() {}
```

Now, you remember from the past chapters, that whitespace is (largely) ignored by C#. Here we created an empty function declaration (there is nothing between the `{}`), in one line.

Note that we added the keyword `virtual` to the function definition. It tells the compiler, that derived classes can *override* this base function with new functionality.

Let's do this in the derived class. So in our `TeslaModel3` class, let's add

```c#
public override void PrepareForDriving()
{
    Charge();
}
```

So, instead of doing nothing, our `Charge` function is called, whenever we want our car to be prepared for driving.

So let's have a look how this works.

```c#
foreach (Car oCar in aoMyCars)
{
    oCar.PrepareForDriving();
}
```

So what do you expect will happen?

While we are dealing with a variable `oCar` of type `Car`, the object stored in it is actually a `TeslaRoadster`, which implements a different version of `PrepareForDriving` than our base class `Car`. Because `PrepareForDriving` is a `virtual` function, the function of the actual type, `TeslaRoadster.PrepareForDriving()`, is called, and *not the one in the base type*.

This is extremely powerful, because it allows us to create complex new types, that expose a simple common interface. A lot of good software architecture is based on parceling out functionality in a smart way, so you can implement it using a class hierarchy with virtual functions.

Now, in our example above, we supplied a default implementation for `PrepareForDriving`. So it's up to the derived class to implement the function or not. This could be a bit dangerous, because if the programmer, who implements the derived class forgets about this, nobody will notice (until a driver steps into the car and finds it not ready to drive).

We can force the programmer's hand by adding the keyword `abstract` to the base class implementation. When we do this, the derived class *must* implement the function, otherwise the compiler will throw an error. We *must* then also mark the entire class as `abstract` to indicate that it's now actually impossible to instantiate a `Car`. Because `Car` doesn't implement `PrepareForDriving`, it has now become an abstract class, that can only serve as the base for derived classes and can no longer stand on its own. 

This makes sense, since it's more of a category than an actual object. Just like you cannot have a `Fish`, it needs to be a `BlueFinTuna`, which is a proper fish in reality.

```c#
public abstract class Car
{   
    public Car(string strName = "Unnamed Car")
    {
        m_strName = strName;
    }

    public abstract void PrepareForDriving();

    public string strName()
    {
        return m_strName;
    }

    string m_strName;
}
```

Note that we also deleted the empty `{}` because `abstract` functions cannot have an implementation in C#.

Now every class derived from `Car` must implement `PrepareForDriving`, otherwise the compiler will complain.

## Summary 

We covered a lot of ground in this chapter. Class inheritance, polymorphism and abstract base classes, are very powerful concepts. 

While it can sound complex at times, it's important that none of this is complicated if you bring it back to the real world. Whenever you think of a class definition, you think "this is an [insert name]". So a "NEMA 17 stepper motor" *is a* "stepper motor", which in turn *is an* "electric motor", which in turn *is a* "motor", which in turn is a "physical object", which in turn *is a* "thing"... etc. 

Class inheritance is just a way for us to express these relationships, and create the object's family tree. 

Polymorphism, the implementation of virtual functions, is a powerful way to express how each of these objects in a family tree should behave individually. The base class can give a default implementation, that can later be overridden by a class higher in the hierarchy. This is a mighty concept, because all the remaining functionality can stay the same, and a derived class can just change one aspect of the object's behavior, by overriding a virtual function.

Abstract functions give us a way to describe a common interface, without needing to understand how the actual implementation looks like. We can say, every motor has `fTorque()`function that returns the current torque as a floating point value — but how this is implemented in an actual motor, we don't know yet. It may be dependent on many factors, and be completely different for different types of motors.

One important thing to always remember is, regardless of what variable you store an object in, when you call a virtual function, the function in the *derived class* is called, not the one that corresponds to the variable's type, which may be a *base type*.

- Classes can be derived from other classes, and form a class hiearchy
- Virtual functions can be overrideen in derived classes, changing the functionality
- Abstract functions *must* be overridden in derived classes, as there is no implementation in the base class
- A base class that defines an `abstract` function must be marked as `abstract`, as it cannot be instantiated (because its behavior would be undefined, since one or more functions are not implemented)

---

Next week: Polymorphism in action

[Jump into the discussion here](https://github.com/leap71/PicoGK/discussions/categories/coding-for-computational-engineers)

---

**[PicoGK.org](https://picogk.org)/coding for engineers**

