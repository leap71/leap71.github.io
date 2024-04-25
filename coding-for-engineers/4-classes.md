**[PicoGK.org](https://picogk.org)/coding for engineers**

![Coding for Engineers](assets/CodingforEngineers.jpg)

[0 — Preamble: My story](README.md)

[1 — Foreword](1-foreword.md)

[2 — Fundamentals](2-fundamentals.md)

[3 — Running Code](3-running-code.md)

[**4 — Classes**](4-classes.md)

# Classes

Let's revisit the concept of classes we introduced in the first part of our series.

Here's our final class definition from before.

```c#
public class TeslaRoadster
{
    public TeslaRoadster(   string  strName         = "Poor anonymous car",
                            uint    nPercentFull    = 0)
    {
        m_fBatteryLevel  = nPercentFull / 100.0f;
        m_strName        = strName;
    }

    public void Charge()
    {
        m_fBatteryLevel     = 1;
        m_nChargingCycles   = m_nChargingCycles + 1;
    }

    public string strName()
    {
        return m_strName;
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
    string  m_strName;
}
```

Now, it's time to put it somewhere.

Let's create a new text file, and save it under the name `Cars.cs`.

Now, before we copy and paste our code there, we need to understand one more concept, the concept of *namespaces*.

## Namespaces

In the old days of programming, every symbol you defined was global. 

If I called a variable `fResult`and someone else called another variable the same, this would lead to all sorts of interesting problems. Organizing data in classes, and hiding information helped a lot, but still  you and I may both create a class called `Car` and the names would clash. So, people started adding project-specific prefixes and performed kinds of tricks, in order to prevent this — and soon functions started to look like your cat accidentally walked over your keyboard during coding. 

If you see some of these names, you know they come from a bygone era. Take a name like this one from the Apple API: `NSAttributedStringEnumerationOptions` — the NS prefix tells us, it has its origins in the [NeXTSTEP](https://en.wikipedia.org/wiki/NeXTSTEP) programming environment, which was introduced when Steve Jobs, freshly kicked out of Apple, started his a new company, called NeXT (which later provided the foundation for MacOS X, after Apple realized they made a mistake). The rest of the function name is very wordy, mostly because it needs to be unique in a programming framework with tens of thousands of symbols.

This is a long-winded way of saying, we needed a way to organize things without endless function and variable names. The solution are *namespaces*.

Namespaces are that exactly: a space, isolated from the rest of the world, where you can call things, whatever you want.

```c#
namespace PicoGK
{
    public class Voxels
    {
        
    }
}
```

Now, whenever we want to create a variable of type `Voxels`, we can 100% identify it as `PicoGK.Voxels`.

Now, how is this better than just calling it `PicoGK_Voxels` or `PKVoxels` or whatever in the first place?

Because of the `using` statement.

Since, very often, our code will use symbols from the same namespaces. We can write

```c#
using PicoGK;
```

At the top of our code, and now we can simply write `Voxels` instead of `PicoGK.Voxels`. Whenever things get ambiguous, the compiler will complain, and force you to uniquely identify your symbol with the right namespace prefix.

Namespaces can be nested, so, there is nothing that prevents you from doing something like that:

```c#
namespace Leap71
{
    namespace PicoGK
    {
        public class Voxels
        {
            
        }
    }
}
```

Now you have to write `Leap71.PicoGK.Voxel` without `using` statement. Or you add `using Leap71` and just write `PicoGK.Voxels`.

Or you combine it all and say:

```c#
using Leap71.PicoGK;
```

and you are back to just writing `Voxels`.

All classes in C# are organized using namespaces, even the built-in ones. Most of them use the `System` namespace. And because the `System` namespace is so omnipresent, the compiler assumes that you are always `using System`.

So, with that knowledge, let's complete our class definition and start using it.

## Class definition

Let's pick a namespace to use for our project, let's call it `CSForEngineers` and paste in our previous class definition.

```c#
namespace CSForEngineers
{
    public class TeslaRoadster
    {
        public TeslaRoadster(    string  strName      = "Poor anonymous car",
                                 uint    nPercentFull = 0)
        {
            m_fBatteryLevel  = nPercentFull / 100.0f;
            m_strName        = strName;
        }
    
        public void Charge()
        {
            m_fBatteryLevel     = 1;
            m_nChargingCycles   = m_nChargingCycles + 1;
        }
    
        public string strName()
        {
            return m_strName;
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
        string  m_strName;
    }
}
```

Now, it's time to instatiate our first object!

## Instantiating an object

Things sometimes get a bit confusing between the terms object and class, as they are sometimes used interchangeably. 

- An object is an instance of a class (something you created with `new` and assigned to a variable). 
- A class is a certain type of object. It's the type of your variable.

Let's do this:

Go to your Program.cs and create an instance of a class, which results in an object:

```c#
CSForEngineers.TeslaRoadster oLinsCar;
oLinsCar = new CSForEngineers.TeslaRoadster();
```

OK, what have we done here? We have declared a new variable of type `CSForEngineers.TeslaRoadster` and called the variable `oLinsCar`. So now we have a variable, a placeholder for a certain type of data.

Then we created a new object of the type `TeslaRoadster` and assigned it to the `oLinsCar` variable.

Now, before we continue, let's first simplify things a little. Let's add `using CSForEngineers` to the top of `Program.cs` as we will be spending all of our time in this namespace.

We can simplify our code now to:

```c#
using CSForEngineers;

TeslaRoadster oLinsCar;
oLinsCar = new TeslaRoadster();
```

And even further to:

```c#
TeslaRoadster oLinsCar = new();
```

So, we declare a new variable of type `TeslaRoadster`, call it `oLinsCar` and immediately assign a new object to it, that we create using the `new()` instruction. Since it is clear, which type we are creating, we do not have to repeat the name of the class again after new (but we could).

In C#, everything you create, is created using `new()`.

Now we have a brand new car, and we can do things with it, for example.

```c#
oLinsCar.Charge();
```

The `o` prepended to the object's name is simply a convention to tell us that this is a generic object (and not a `string`, a `float`, or whatever).

## Constructors

Remember what we said about what happens when a new object is created? The constructor is called to initialize all variables.

Let's see what happens when we output their contents.

```c#
using CSForEngineers;

TeslaRoadster oLinsCar = new();
Console.WriteLine(oLinsCar.strName());
```

The output will be `Poor anonymous car`, because, that's the default value for the name. We should change that.

Remember that constructors are just functions, and functions can have parameters. And we actually did add parameters to our constructor (but they had default values, so we did not need to provide them). 

So let's test this out:

```c#
using CSForEngineers;

TeslaRoadster oLinsCar = new("Lins's red Tesla");
Console.WriteLine(oLinsCar.strName());
```

In this example, we pass the first of the two optional parameters, the name of the car. 

And in fact, the name variable of our object is assigned accordingly.

Now, let's check the charge level and see if we can drive:

```c#
using CSForEngineers;

TeslaRoadster oLinsCar = new("Lin's red Tesla");
Console.WriteLine(oLinsCar.strName());
Console.WriteLine(oLinsCar.nBatteryLevelPercent().ToString());
```

Remember everything is an object in C#? `nBatteryLevelPercent` is an unsigned integer value. But this value object conveniently exposes a function to convert it to a `string`, called `ToString()`.

The charge level is `0` — that's not good. We should charge it before we drive!

```c#
using CSForEngineers;

TeslaRoadster oLinsCar = new("Lin's red Tesla");
Console.WriteLine(oLinsCar.strName());

oLinsCar.Charge();
Console.WriteLine(oLinsCar.nBatteryLevelPercent().ToString());
```

Now our charge level is `100`%, so we are ready to go driving.

We can create more cars for more people, and have a bit of fun with variables. This simple example illustrates how these objects are all of the same class, but they each have a life of their own, just like my car may be the same make and model of your car, but clearly we each have our own separate object.

```c#
using CSForEngineers;

TeslaRoadster oFredsCar = new(0, "Fred's Tesla");
Console.WriteLine(oFredsCar.strName());

TeslaRoadster oEmmasCar = new(0, "Emma's fun scooter");
Console.WriteLine(oEmmasCar.strName());

oFredsCar.Charge();
oEmmasCar.Charge();
Console.WriteLine(oFredsCar.nBatteryLevelPercent().ToString());
Console.WriteLine(oEmmasCar.nBatteryLevelPercent().ToString());
```

## Summary

While this may seem like a simple example, we have successfully demonstrated a number of important concepts.

First of all, everything in C# is an object. To avoid naming collisions, C# has the concept of (nested) *namespaces*. We have put our class definition in a namespace that makes sense for us, and by employing the `using` statement, we can avoid verbosity.

But actually, the most important thing we have demonstrated, is the power of object oriented programming to encapsulate both functions and data in one object. By hiding the actual implementation, we have decoupled the interface of the object from the internals. The percentage of our battery level is stored as a floating point value internally, but we interact with it through an integer percentage value. The interface to our car is deceptively simple, but the actual implementation behind this interface, can be quite elaborate.

Whenever we `Charge()` our car, not only is the battery value set to 100%, our battery also degrades a tiny little bit. You could imagine replacing the currently very simple formula for degradation with real-life data, and take complex interactions in cell chemistry into account. But the interface to the outside world would not change at all.

In this deceptively simple example, we have also shown, how quickly you can arrive at something useful, even if is just a few lines of code. In object oriented programming, you often start with a simple definition and the most minimal interface possible. 

A car is a complex object, but for the purpose of our example, the interface to it can be trivial. If we later add complexity, it doesn't change the simple interface we created for our basic purposes.

It is often useful to create different interfaces to objects for different purposes. Or treat entire types of objects in similar ways, because they have things in common.

Unless you need the complexity, you can treat, and implement, the object as simply as you can.

We will explore this concept in the next chapter on inheritance, and later with interfaces.

----

Next week: **Inheritance**

[Jump into the discussion here](https://github.com/leap71/PicoGK/discussions/categories/coding-for-computational-engineers)

---

**[PicoGK.org](https://picogk.org)/coding for engineers**
