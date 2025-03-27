**[PicoGK.org](https://picogk.org)/coding for engineers**

![Coding for Engineers](assets/CodingforEngineers.jpg)

**[Table of contents](TOC.md)**

# Let's talk about properties

Some of the syntactical shortcuts we learned in the previous chapter are already quite advanced. But you will encounter them frequently in other people's code, so you need to understand them.

They may also make your life easier when you're writing elegant and concise code of your own.

We touched on the subject of *properties* a bit in the last chapter. It's worth diving into it deeper.

## C# properties

To recap, properties are fundamentally just functions of a class, which allow you to get or set the value of a variable which is not visible to the outside.

Let's write out the functionality as traditional functions:

```c#
public class SomeClass
{
    public Vector3 vecOrigin()
    {
        return m_vecOrigin;
    }

    public void SetOrigin(Vector3 vec)
    {
        m_vecOrigin = vec;
    }

    // private variable
    Vector3 m_vecOrigin = Vector3.Zero;
}
```

So, we have a private variable `m_vecOrigin`, which is inaccessible to the outside world. To get access to it, you write an accessor function `vecOrigin`, and all it does is return the variable. Similarly, to set the variable, you write a function `SetOrigin`, which allows you to assign a new value to your internal variable.

Let’s first ask ourselves why we do it this way.

Could we not just expose `m_vecOrigin` as a public variable? 

The answer of course is, because it would violate the principle of *information hiding*. Access to the internal state of an object should always be through a minimal clearly-defined interface. 

What if you had to do something if the value of `m_vecOrigin` changed? If you expose the variable publicly, there's no way to detect the state change.

But if you have accessor functions, you can insert code that acts on the changes. For example:

```c#
public class SomeClass
{
    public Vector3 vecOrigin()
    {
        return m_vecOrigin;
    }

    public void SetOrigin(Vector3 vec)
    {
        if (vec != m_vecOrigin)
        {
            m_vecOrigin = vec;
            Recompute();
        }
    }

    void Recompute()
    {
        // ...
    }

    // private variable
    Vector3 m_vecOrigin = Vector3.Zero;
}
```

Now our `Set` function is more complex. If a different value is assigned, a function `Recompute` is called, which updates the state of your object, something we could not have done, had we exposed the variable directly.

Even if you think that will never happen, it's good practice to keep your internals internal — just in case things change later.

But writing `SetOrigin(vecNewValue)` looks a bit cumbersome. And calling a method of a class implies that some work is actually being done, which is not true for most fields that represent simple properties. And so, the authors of C# felt there should be a way to simplify this, which we touched on in the last chapter.

Now, when is it actually appropriate to expose a value as property? 

As a rule of thumb, a property should only be used if the implementation of the set/get function is actually trivial and doesn't involve work. So, if the `Recompute` function is *expensive* in terms of computing time required, `SetOrigin` is *not* a good candidate for a property. 

A regular function implies work, and if you actually do work, you should use a function.

If a *lot of work* is being done, you should also make that clear in the name of the function. In our case, a better name for the Set function could be:

```c#
public void SetOriginAndRecompute(Vector3 vec)
{
    if (vec != m_vecOrigin)
    {
        m_vecOrigin = vec;
        Recompute();
    }
}
```

A bit wordy, but now it’s entirely clear that this is a non-trivial function that may do a lot of work in the background.

But what if our function looked like this:

```c#
public void SetOrigin(Vector3 vec)
{
    if (vec != m_vecOrigin)
    {
        m_vecOrigin = vec;
        m_bRecomputeRequired = true;
    }
}
```

That's a very good candidate for a property. The above function sets the origin to a new value, and marks the object state as *dirty*, in other words, it makes sure that the object knows that something about its state has changed, and if there are dependent values that need to be recomputed, this can be done when necessary. 

How would we implement this class with properties?

In C#, the code structure of properties is a bit idiosyncratic, with magic variables appearing out of nowhere. But here it is.

If you only need a *Getter* function, then it is quite simple; we learned about it in the last chapter. You just write:

```c#
public Vector3 vecOrigin => m_vecOrigin;
```

What the above code actually stands for is the following code:

```c#
public Vector3 vecOrigin
{
  get
  {
      return m_vecOrigin;
  }
}
```

Let's dissect it: the following code declares the existence of a property.

```c#
public Vector3 vecOrigin
{
...
}
```

The special function name `get` declares the Getter function, the body then returns the contents of the variable `m_vecOrigin`.

Now, let's look at the *Setter* function. Correspondingly, it uses the built-in function name `set`.

```c#
public Vector3 vecOrigin
{
    get
    {
        return m_vecOrigin;
    }

    set
    {
        if (value != m_vecOrigin)
        {
            m_vecOrigin = value;
            m_bRecomputeRequired = true;
        }
    }    
}
```

As you can see, the function parameter `value` magically appears out of thin air and, unsurprisingly, represents the value that’s passed.

How do you actually use properties? That's the nice thing: you can use them like variables:

```c#
public static void DoSomething()
{
    SomeClass oClass = new();
    oClass.vecOrigin = Vector3.UnitX;

    Vector3 vecValue = oClass.vecOrigin;
    Console.WriteLine($"Value= {vecValue}");
}
```

So, the properties allow you to use variable-like syntax, which is very clean. In the background, however, functions are called, which ensure that the state of your object stays valid.

When you call `oClass.vecOrigin = Vector3.UnitX;`, in the background, C# calls the `set` function and passes `Vector3.UnitX` in the magic `value` variable. The function then executes like a regular function.

And here, of course, lies the danger in this approach.

You could be tempted to expose something that is really expensive to calculate as a property:

```c#
public class Universe
{
    public State oQuantumStateOfTheUniverse
    {
        // Calculation takes roughly 13.8 billion years, give or take
        get => oCalculateTheQuantumStateOfTheUniverse();  
    }
}
```

As we all know, this function will take at least 13.8 billion years to complete, so it's not a good candidate for a property.

Properties suggest that they are trivial to set and retrieve. 

One example in the built-in C# functionality is the `Count()` property of containers. As you know, there are arrays, which are containers for values (or objects) — for example:

```c#
float [] af = new float[100];
```

creates an array container which can hold 100 floating point values.

And the array object conveniently exposes a property, called `Length`, which tells you how many `float` objects it can hold.

```c#
Console.WriteLine(af.Length);
```

There are more complex containers, such as lists, which are more flexible. But these containers come at a cost. If you ask them for their capacity, they may have to run expensive calculations to find out. So, instead of exposing a `Length` property, which would surprise you if it took a long time to run, they instead expose a function, called `Count()`.

```c#
LinkedList<int> ai = new();
ai.AddLast(10);
Console.WriteLine(ai.Count());
```

This code creates a linked list of integers. A linked list allows you to insert values at any position you want. It dynamically grows and shrinks, as you add and remove items.

The `Count()` function allows you to find out how many objects are currently stored in the `LinkedList`, which, for this container, requires a transversal of all stored items, which can be non-trivial.

So `Count()`, as a consequence, is *not* implemented as a property. The invocation of `Count()` reminds you of the fact that there is a cost to running the function.

To illustrate this practically, consider this:

```c#
for (int n=0; n<ai.Count(); n++)
{
    // Do something
}
```

So, you are running this code for the number of objects in the container. But the `Count()` function is invoked every time you loop, which could be thousands of times. The fact that you are invoking a function reminds you that maybe you should do this instead:

```c#
int nListCount = ai.Count();
for (int n=0; n<nListCount; n++)
{
    // Do something
}
```

Now you call `Count()` exactly once, store the result in a variable, and compare against it, saving the compute time for counting objects.

But for the array, since it's a property, which indicates it's a trivial accessor function, it's completely fine to do this:

```c#
for (int n=0; n<af.Length; n++)
{
    // Do something
}
```

The `Length` property, if the author was diligent, is likely trivial to access in terms of computing cost, so another variable is unnecessary and would be quite wordy.

Lastly, I want to cover a shortcut, which I am personally not fond of — but you see it a lot.

```c#
public Vector3 vecOrigin {get;set;} = Vector3.Zero;
```

This creates, more or less, a public variable called `vecOrigin`, which is accessed through automatically-created getter/setter functions. If you see something like that, you very likely encountered a class, that was not thought out properly, in terms of external interface. Fundamentally, you have a variable that is readable/writeable from outside and inside through functions that do nothing but set or get the value. There may be reasons to do this (for example certain API requirements), but then, these reasons are often the same as the rare reasons for public variables.

These rare cases are simple container objects, such as the `Vector3` we used before. But then, a public variable for X,Y,Z is fine, so, why would you use `get`/`set`?

Whenever you are tempted to use this feature, ask yourself, what am I actually doing?

Next week we will talk about exactly these cases, we will talk about `struct` vs. `class` data types and values vs. references.

## Summary

This was a long-winded way of saying: Properties are a great way to expose access to trivial members of your class.

Properties simplify the syntax of access, and they communicate the fact that that access is inexpensive — think of the `Length` vs. `Count()` example.

Should you use them? I personally did not use them originally but have since grown quite fond of them, because of the fact that they allow me to differentiate between costly and trivial functions. 

I still make it a habit of marking computationally very expensive functions by naming them `CalculateBoundingBox()` vs. `GetBoundingBox()`, or `FindNearestPointOnSurface()` vs. `GetNearestPointOnSurface`. *Calculate* or *Find* evoke a sense of "oh, this might take a while", so the user of these functions will be aware that they should consider caching strategies, if frequent access is needed.  

But if access is cheap, a property called `oBoundingBox` makes it very clear: it's fine to use frequently with no penalty.

In the background, what actually happens is exactly the same: a function is called that does something. 

So it's really about what people call *syntactic sugar*:

`oObject.fHeight = 10f;` just looks a bit more elegant than `oObject.SetHeight(10f)` .

Make, however, sure that you never ever hide complex computation behind a property.

As always, the [code for this chapter is on GitHub](https://github.com/LinKayser/Coding4Engineers) (again quite basic this time).

------

Next: **Structs, classes, values, and references**

[Jump into the discussion here](https://github.com/leap71/PicoGK/discussions/categories/coding-for-computational-engineers)

[Table of contents](TOC.md)

------

**[PicoGK.org](https://picogk.org)/coding for engineers**

© 2024-2025 by [Lin Kayser](https://www.linkedin.com/in/linkayser/) — All rights reserved.