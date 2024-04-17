**[PicoGK.org](https://picogk.org)/coding for engineers**

![Coding for Engineers](assets/CodingforEngineers.jpg)

[0 — Preamble: My story](README.md)

[1 — Foreword](1-foreword.md)

[**2 — Fundamentals**](2-fundamentals.md)

[3 — Running Code](3-running-code.md)

# Fundamentals

In this chapter we will go on a whirlwind tour from the general concept of a computer to the paradigm of object oriented programming.

We will cover a lot of ground, but after reading this chapter carefully, you will have a solid foundation of modern software principles, that we will built upon in the following chapters.

So, what is a computer anyway?

## Data and Instructions

To understand coding, you need to understand a few basics about computers.

Modern computers, so-called [Turing machines](https://en.wikipedia.org/wiki/Turing_machine), have two fundamental building blocks: Instructions and data.

*Data* is the information that instructions work with; *instructions* processes input data, and generate output data as a result.

To store data, we use variables, to run instructions, we implement functions.

Let's see how that looks like in practice:

```c#
WriteLine("Hello World")
```

`WriteLine` is a function. It expects an argument (a string of characters, or simply `string`) and will output this `string` on your display, so you can read it. How it does that is conveniently hidden behind the function's façade — and often, we don't care how that function is actually implemented — we expect it to just work.

Now, we could store this `string` of characters, which spell out `"Hello World"` in a separate variable first. 

A variable is a place in the computer's memory to store information. 

A variable in C# and many other programming languages, has a *name* and a *type*. A variable's *type* defines what kind data you can store in it. A variable of type `string` can store strings and nothing else. If you want to store a number in a variable of type `string`, you explicitly have to convert it to a string of characters first. This is important to remember, because some modern programming languages, like Python, try to blur the differences between different kinds of data, which seems helpful at first, but is actually very dangerous. 

To come back to our example, to store data in a variable, we have to tell the computer first, that this variable exists, and what type it has.

```c#
string str;
str = "Hello World";
WriteLine(str);
```

So, in the first line, we announce to the world, that a variable of type `string` exists and that we want to call it `str`.

In the second line, we put the characters that form **Hello World** into the storage provided by `str`.

And in the last line, we run the instructions provided by the function `WriteLine` on the data inside the variable `str`.

Note, that in C#, every set of instructions is always delimited by the semicolon character `;`. In theory, this is all you need (most other whitespace is ignored), so you could write 

```c#
string str;str="Hello World";WriteLine(str);
```

and get the same result. But we want to write *readable* code, so we will spend our time to format our code nicely. 

*The nicer your code looks, the easier it is to understand.*

So let's recap this:

- Computers need instructions and data
- Instructions in C# are separated by semicolons
- Whitespace (tabs, space characters, paragraphs), are mostly ignored by C#, and are just there to make the code more readable for us humans
- Variables in C# must have a type
- Variables need to be declared before they are used
- What data you can store inside a variable depends on its type

Generally speaking, *C# doesn't automatically convert between different types*. 

So if you want to store a number in a string, you have to first convert the number to a string of characters. And if you think about it, this makes sense. A number is not a string of characters, and a string of characters is not a number. To convert a number to a `string`, you may want to choose how many decimal places you want to display. Or whether the number 1 is represented as `1.0`, `1.000`, `1`, `00001`or even `1,0` when you are in a European country (where, inexplicably, they use the comma as a decimal point).

On the flip side, if you have a `string`, which you *hope* contains a number — before you actually do math with the number, wouldn't it be reasonable to first see if you can actually extract the number from it? If a string, for example, contains the characters `1.000mm`, you'd have to make sure to strip away the unit (mm). Or, maybe you'd actually have to do some math, and multiply by a thousand if the unit was meters instead. What if your string contained `Hello World` instead of a number — how should that be handled? Is this an error? Or should we just treat it as zero?

C# avoids implicit conversions. Other programming languages, unfortunately, are "helpfully" converting stuff for you these days. So `"Hello World"` magically becomes `0`, and both `"100mm"` and `"5m"` are converted to `0` as well (which is the default value for "I have no clue what this value is supposed to be")... 

Implicit conversions are incredibly dangerous in any large scale software project.

That's why we chose C#, and easy-to-use strongly typed modern language, for the basis of [PicoGK](https://leap71.com/PicoGK/).

## Classes and Objects

Now, in the past, instructions and data were seen as separate things. 

Instructions were performed on data, but the instruction had no clue about where the data was coming from, and whether it actually made sense to perform the instruction on it. As long as the type of data was correct, say, a number, the instructions were performed. If you sent nonsense in, nonsense came out. This kind of programming is called *procedural programming* — and, unfortunately, it is still prevalent today. It's what you get when you copy and paste stuff linearly together, to hack together a "script" that performs a certain function. This spaghetti code, which is very common today, is not good software architecture.

In our real, physical world, we don't deal with abstract data and run mindless operations on it. In our real world, we deal with objects. These objects have properties, and they have ways we can interact with them. 

Imagine you have a car. It has certain properties. Some are static and never change, like the type of fuel it uses. Some vary, such as the speed or mileage driven. You have ways to interact with your car: you can press the accelerator, you can turn on the light. It would be downright silly, if we had an abstract instruction called `TurnOnLight` and we had to pass it a variable of the type `ElectricCurrent`, hoping nobody had mixed up the cables that lead to the light bulb.

Turning on the light, is something that makes only sense for that particular object (a car — it makes no sense for an elephant!). It only applies to that one instance, and it may be implemented differently in different types of cars — there is no universal way to do it, using universal instructions. The functionality, just like the properties, belong to the car I am driving right now, they are implemented for this particular make (or class) of car, and they may be different between various manufacturers (but the interface exposed to me as a driver, say, the brake pedal, is the same).

This insight led to a veritable revolution in computer programming in the late 80s and 90s, it led to the *object oriented programming* paradigm.

In object-oriented programming there are certain classes of objects, and you interact with them, by changing their properties, and invoking their methods. As a user of an instance of a class, you may invoke functions and check properties without needing to know exactly how they are implemented internally (just like I don't have to understand the electrical wiring of my car in order to switch on the lights). Everything is bundled together and the creator of the class of objects is responsible for making sure, that all functionality actually makes sense, and the state of the object is always valid.

In C# everything is an object (or at least a structure, we'll get to that in later chapters).

### The skeleton of a class

Let's imagine, we actually want to build a class that represents a certain type of car.

This is how we would start (the name of the class is a reference to the first electric car I bought in 2012 — a Tesla Roadster, sorry petrol heads):

```c#
class TeslaRoadster
{

}
```

This essentially tells the world, that there is now a new type in our software code, it's a certain class of cars, called `TeslaRoadster`. The curly braces `{ }` are always used in C# to show that the code block between them belongs together. Again, remember, that whitespace and paragraphs are (mostly) ignored in C#, so it's the same as if we had written

```c#
class TeslaRoadster {}
```

but, it wouldn't be very readable, because we need to add more to this code block.

So, let's imagine, we have certain data we want to store inside of our car object. We want this data to be associated with any car we create, and it should always travel with the car. So, let's try this.

The battery level comes to mind. The battery level is best stored as a floating point number. A floating point number contains numbers with decimal places, in C#, the corresponding types are `float` and `double`. `Double` is a high-precision floating point number, which is useful for complex calculations. We are fine with a single precision number.

Let's also give the car a name, just so we can differentiate different ones.

```c#
class TeslaRoadster
{
    float    m_fBatteryLevel;
    string   m_strName;
}
```

Now, let's look at this briefly. Why is there all this weird stuff, like `m_f` etc. before my nice variable name?

This is just convention, and maybe I am a bit old-fashioned about this. In my personal programming style, which I have used for 40 years and which has resulted in some very solid code, I have always prefixed the variable and function names with a type. In this case `f` tells me at a glance, that I am dealing with a `float` value here, and `str` tells me this is a string. The prefix `m_` tells me (just me, the computer doesn't care), that this is a member variable that belongs to my class. 

You will see a lot of code out there that doesn't follow this style (you can name variables however you want). In my experience I found it extremly hard to read someone else's code, unless they followed these kinds of conventions. Is the variable I am seeing a number, is it a list of things, is it a complex object? I (and all my teams) have been doing this forever, and it has always made things much easier. So there you go.

OK, now let's go back to our example. We have a class now, which describes a certain type of car. It has associated data, but it doesn't do anything. We may have to add functionality. Let's give our car a way to charge itself.

```c#
class TeslaRoadster
{
    void Charge()
    {
        m_fBatteryLevel = 1;
    }
    
    float    m_fBatteryLevel;
    string   m_strName;
}
```

Now, what have we done here? First of all, we have declared our first function! 

It starts with a terrible remnant of the old C programming days. What the hell is `void`? 

It essentially just means, that the function doesn't return a value (that the return value is "void"). Things like these are, why C# and C++ are sometimes considered "hard". But that's really all there is to it. Any function that doesn't return a value, has the return type `void`.

Now, you will see that our `Charge` function doesn't accept any parameters. It simply sets the battery level to 1, which, in our convention. Would be 100% charged.

So, let's sum this up. We now have a new type of object, an object of the class `TeslaRoadster`. It has two data items, two member variables, one to store the battery level, one to store the name of the car.

It has one function (member function, also called "method"), which allows us to charge the car.

### Constructors

Maybe you are asking yourself, but wait! We have all these properties, like the car name, and the battery level. But when I create an object of the type `TeslaRoadster`, what will be the initial value of these variables? 

Is the name just empty, or the battery level 0? Other programming languages would gladly allow you to leave all these things undefined, hoping that some kind of default behavior is suitable for your problem. 

Not C#.

In C# everything needs to be defined. You cannot have an uninitialized variable. There are two ways to do this, one very straighforward one, and one that brings us to the next step of our learning process.

Let's do the straighforward one first (leaving out the `Charge` function for brevity).

```c#
class TeslaRoadster
{
    float    m_fBatteryLevel  = 0;
    string   m_strName        = "";
}
```

So, when we declare a variable, we can give it a default value.  So we have told our computer what we expect these values to be whenever we create a new object of the class `TeslaRoadster`, all things are clear now.

But there's also a different way of doing things. 

Every class can have one or more *constructors*. A constructor is a special function, which has the same name as the class. This function will always run the first time an object of that class is created.

Let's implement the same functionality as above, but we will use a constructor instead.

```c#
class TeslaRoadster
{
    TeslaRoadster()
    {
        m_fBatteryLevel  = 0;
        m_strName        = "";
    }
    
    float    m_fBatteryLevel;
    string   m_strName;
}
```

So, you see, we have a constructor now. A constructor always has the name of the class and they never have return value, not even `void`.

The constructor is always run, when the object is being created, and it initializes the object's variables. 

So, in this second example, the functionality is exactly the same as before. So why bother? A constructor is a function, so you can do almost anything in there that you could do in a regular function. You can perform math, call other functions, etc.

But one of the most powerful things a constructor has, is the ability for you to pass parameters to it.

```c#
class TeslaRoadster
{
    TeslaRoadster(string strName)
    {
        m_fBatteryLevel  = 0;
        m_strName        = strName;
    }
    
    float    m_fBatteryLevel;
    string   m_strName;
}
```

Let's have a look at this. Now we are passing a string to the constructor of the object, which gives us the name of the car. In fact, we cannot have an object without a name anymore, because the constructor requires us to pass the name. This is a very powerful way to architect your code, because now, any information an object needs in order for it to be created, has to be passed to it.

Well, but not everyone likes to give their car a name (mine never had any), so how do we solve that?

We can create two constructors, one which requires the name, and one which doesn't.

```c#
class TeslaRoadster
{
    TeslaRoadster()
    {
        m_fBatteryLevel  = 0;
        m_strName        = "Poor anonymous car";
    }
    
    TeslaRoadster(string strName)
    {
        m_fBatteryLevel  = 0;
        m_strName        = strName;
    }
    
    float    m_fBatteryLevel;
    string   m_strName;
}
```

So now, we have two ways to create an object of type `TeslaRoadster` — one which allows us to give the car a name, and one which doesn't require it. Both result in a valid object, because the name is initialized properly.

Let's add one more constructor, to show you the power.

```c#
class TeslaRoadster
{
    TeslaRoadster()
    {
        m_fBatteryLevel  = 0;
        m_strName        = "Poor anonymous car";
    }
    
    TeslaRoadster(string strName)
    {
        m_fBatteryLevel  = 0;
        m_strName        = strName; 
    }
    
    TeslaRoadster(uint nPercentFull)
    {
        m_fBatteryLevel  = nPercentFull / 100.0f;
        m_strName        = "Poor anonymous car"; 
    }
    
    float    m_fBatteryLevel;
    string   m_strName;
}
```

You can see, that we can now give the car an initial battery level. Internally, the level is stored with a value from 0..1, which represents 0..100% charge. But we now gave us a way to pass the charge level as an unsigned integer value (`uint`). Integers are values without decimal places. The type `int` represents positive and negative integers, while `uint` represents only positive integers. Maybe you are wondering about the `100.0f` — this is how we tell C# that this is a single-precision floating point constant.

Now, you see a lot of repeated code in the constructors, and there are different ways to deal with it. For now, I will just introduce one more concept, which is useful for functions, including constructors, and then we move on.

### Default Parameters

In C#, you can (with a few restrictions), provide default values for parameters. If a default value is specified, you do not have to provide the parameter, when calling the function.

So, let's simplify our constructors using default parameters:

```c#
class TeslaRoadster
{
    TeslaRoadster(    string  strName         = "Poor anonymous car",
                      uint    nPercentFull    = 0)
    {
        m_fBatteryLevel  = nPercentFull / 100.0f;
        m_strName        = strName;
    }
    
    void Charge()
    {
        m_fBatteryLevel = 1;
    }
    
    float    m_fBatteryLevel;
    string   m_strName;
}
```

So, this allows us to optionally specify the charge level and the name of the car. It only works in sequence, though, so if you want to supply the charge level, you first have to give the car a name.

Now, we covered a lot of ground in this chapter, from the first Turing Machines to creating our first `class` definition. Let's introduce one more concept, that's essential in order for us to actually use the class we created, and then we go compiling.

### Visibility

By default, everything you create in C# has the visibility `private`. In other words, nobody outside the class can see what we created. 

This is called *Information Hiding* and is an important concept of object oriented programming. Before this paradigm, every aspect about everything a programmer created was always public. Information was stored in global variables, which anyone could read and manipulate. Everybody could invoke functions on any kind of data. This sounds very powerful — but it is incredibly dangerous! You might not know how the code was designed. If you modified a variable, maybe the app continued to work OK, but something was "off" somewhere, and you would not know about this, until it was too late. And if someone changed the implementation, a lot of things outside would also need to be adjusted. 

This interdependency of internal structures and the side effects they are causing is a software developer's definition of hell. 

In our class definition above, the member variables for the battery level and the name are currently not accessible outside the class. So why not make them public? We could eliminate the `Charge()` method, and just assign a new value whenever we charge the car. Wouldn't it be much easier than with all this private information hiding stuff?

Let's add one more property to our electric car, to illustrate why this is a bad idea.

```c#
class TeslaRoadster
{
    TeslaRoadster(  string  strName         = "Poor anonymous car",
                    uint    nPercentFull    = 0)
    {
        m_fBatteryLevel  = nPercentFull / 100.0f;
        m_strName        = strName;
    }
    
    void Charge()
    {
        m_fBatteryLevel      = 1;
        m_nChargingCycles    = m_nChargingCycles + 1;
    }
    
    float   m_fBatteryLevel;
    uint    m_nChargingCycles = 0;
    string  m_strName;
}
```

Imagine we wanted to count, how often our car was charged, as the battery cells degrade with each cycle. If we made all these properties directly writable from the outside, the user of the object would have to have an insider's knowledge about how our object works internally. Information hiding forces us to expose the mimimal interface to the outside world, shielding the implementation details from ignorant interference. The only way to change the charge level is to go through a defined interface, using the `Charge` method. The person who implemented the class knows what needs to be done to ensure our car is in a valid state. 

So, let's have a look at how we deliberately expose our class to the outside world.

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
        m_fBatteryLevel      = 1;
        m_nChargingCycles    = m_nChargingCycles + 1;
    }
    
    float   m_fBatteryLevel;
    uint    m_nChargingCycles = 0;
    string  m_strName;
}
```

So, first we added `public` before the class definition, making the class itself available to be used.

Then we made the constructor `public`, without this, the class cannot be instantiated, so we could actually not create an object of that class. While it may not make sense to you to have a public class that cannot be instantiated, there are actually use cases for that. But in our case, the class and its constructor are public.

Lastly, we made the `Charge()`function public, because otherwise we cannot charge our car.

Please note, that none of our properties are publicly available, so there is no way for us to understand the charge level, name, etc. of the car from the outside. We can change that by creating *access functions* for these. (C# has a mechanism for setter/getter functions, that I personally never use, because it interferes with our coding conventions, and I find them to hide what is actually happening, so I am intentionally not using those).

 So here is how I write it, and how I keep it consistent.

```c#
public class TeslaRoadster
{
    public TeslaRoadster(    string  strName         = "Poor anonymous car",
                             uint    nPercentFull    = 0)
    {
        m_fBatteryLevel  = nPercentFull / 100.0f;
        m_strName        = strName;
    }

    public void Charge()
    {
        m_fBatteryLevel      = 1;
        m_nChargingCycles    = m_nChargingCycles + 1;
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

So, let's look at these new public functions. The first one `strName` is simple enough. It just returns the name of the car. Note, that in the conventions I use, I indicate the return value (`str`) in the function name. Not many people use that convention, but again, it's a hard-earned lesson from many decades, that this makes things so much easier.

The next one `nBatteryLevelPercent` gives us an integer value, which indicates the percentage of the charge level of the battery. Note, that now from the outside (Information Hiding), we have no knowledge how the charge level is actually stored internally (in floating point values from 0..1). From outside the object, it looks like we always have integer values (from 0..100). One interesting thing I am doing here is using the `Convert` system functionality to properly convert the result of the floating point calculation to the `uint` value. It does proper rounding (and also throws an error if the value is negative).

The last function calculates a battery health value, based on some internal knowledge of charging cycles, typically expected lifetime, etc. The power of object oriented programming and information hiding means, the car itself "knows" these values, for a user of the car object, it is unimportant how we calculate them.

Keep your data `private` in almost all cases. There are very few instances where you want to expose the data directly, but these are rare.

If you don't expose your internal data, you can change every aspect of your class, without having to change the interface (imagine deciding to use an `uint` to store the charge level. We could make that change without modifying anything about how the object is used from the "outside" — the interface would stay the same).

## Summary

So, this was a whirlwind tour of going from the absolute basics of coding to the core building blocks of object oriented programming.

Remember the following:

- There's data and instructions
- Everything in C# is an object
- Objects encapsulate both data and instructions
- The data inside an object must always be initialized
- To initialize, we can either assign a value when we declare a variable, or use a constructor to do it
- Constructors are special functions that are called when an object is created
- Constructors can have parameters, that we can use to initialize the object properly
- By default, everything inside an object is invisible (`private`) — the concept of Information Hiding
- You should be deliberate about what you expose. The smallest interface is usually the best
- Data should be kept private in almost all cases, and exposed through access functions

------

Next: [**Running Code**](3-running-code.md)

------

**[PicoGK.org](https://picogk.org)/coding for engineers**
