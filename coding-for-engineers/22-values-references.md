**[PicoGK.org](https://picogk.org)/coding for engineers**

![Coding for Engineers](assets/CodingforEngineers.jpg)

**[Table of contents](TOC.md)**

# Structs, classes, and the secret life of references

In this chapter, we’ll dive into a topic that can be tricky to explain, but is usually straightforward in day-to-day coding.

I’ll explain the differences between the two kinds of types in C#: the ones known as *value types*, and the other kind — the ones we’ve been using all along — defined using the `class` keyword. We’ll see how these behave differently when passed to functions.

Let’s dive right in — and start by talking about *values*.

## Value types

We’ve used *value types* before — many of the simple built-in types are value types, such as `float`.

Value types are often small in terms of memory footprint. Their memory is usually allocated in a special area for local variables, called the *stack*, which involves very little overhead.

Classes, on the other hand, are usually more complex and are allocated in a separate part of memory, called the *heap*. The lifetime of such an object — the time it is actually used in your algorithm — can vary. To handle this, the C# runtime inserts logic to track the object’s usage and free its memory when it is no longer needed, through a process called *Garbage Collection* (GC).

Most of the time, all of this happens under the hood — but there are a few important behavioral differences you should be aware of.

## Copy vs. reference

We’ve talked about references before, but let’s refresh our memory (no pun intended).

Let’s look at this function:

```c#
static void DontChangeVariable(float f)
{
    f = 5;
}
```

What happens if I do this:

```c#
float fValue = 10;
DontChangeVariable(fValue);
Console.WriteLine(fValue);
```

If you guessed *nothing*, you’re right (OK, I gave away the result in the function name).

But why?

Because, as expected, the function received a *copy* of the variable’s value — not a reference to the original. Inside the function, you’re simply assigning the value `5` to this new local copy, which has no effect on the original variable outside the function.

In fact, the whole function doesn’t make much sense, because no one will ever see that we assigned that value.

But could we actually affect the original variable? Yes — by passing it *by reference* instead of *by value*.

You do this by adding the `ref` qualifier to the variable:

```c#
void ChangeVariable(ref float f)
{
    f = 5;
}
```

Now the function `ChangeVariable` receives a variable `f` that *points back* to the original variable. Modifying `f` directly changes the value of the original.

However, if you just try this:

```c#
float fValue = 10;
ChangeVariable(fValue);
```

The compiler will complain, telling you that the `ChangeVariable` function requires a reference, but you’re trying to pass by value. To make the example compile, you also need to add the `ref` qualifier to the function call:

```c#
float fValue = 10;
ChangeVariable(ref fValue);
Console.WriteLine(fValue);
```

So, what happens here? `fValue` holds the value `10`. You pass it by reference to the function, which means the function receives a reference to the original variable. The function then assigns a new value through that reference — so after the function exits, `fValue` contains the new value.

To sum up: there are two ways to pass a variable to a function. The default is *by value* — meaning the variable’s contents are copied into a new variable, and everything you do in the function operates on that copy.

The second way is to pass *by reference*, which passes the memory location of the original variable — so anything you do through that reference directly modifies the original.

Good to know, you might say — but that seems like an exotic feature, something that should rarely be used. Why even bother explaining it?

Because *pass by value* only applies to value types.

Objects defined as classes are always passed *by reference*.

## Reference types

The moment you declare a new type using `class`, you’re creating a *reference type*.

I suspect the main reason why the authors of C# felt the need to call it that was their discomfort with the fact that a `class`behaves so differently from a value. It makes a lot of sense once you think more deeply about it — but it’s still one of the choices I struggle with, because it’s not entirely obvious.

Now let’s modify our example. First, we’ll create a little container `class` that holds a `float` value. For simplicity, I’ve declared it as a public variable here (in the repository source code, it’s declared as a property):

```c#
class MyContainer
{
    public float fValue = 0;
}
```

And now, let’s modify the example:

```c#
MyContainer oContainer = new();
oContainer.fValue = 10;

DontChangeVariable(oContainer);
Console.WriteLine(oContainer.fValue);
```

And... well, sorry — the container’s value is actually modified, and it now holds `5`.

That’s because reference types — anything you declare with `class` — are *always* passed by reference.

And, unlike in some other programming languages, there’s no way to prevent a function from modifying the contents of a reference type (a class object) passed to it. If the C# authors had added a way to mark a function parameter as *readonly*, it would allow us to guarantee more robust code.

Again, this may sound like a big deal (and conceptually, I think it is!) — but in practice, it’s fairly irrelevant, fortunately.

## On a "need-to-know basis"

One of the reasons why this isn’t such a big deal is that if you use *information hiding* properly, you won’t expose things that shouldn’t be changed arbitrarily. If you carefully manage the state changes of your object, you simply won’t run into these issues.

If things get complex, and you’re worried that someone using your class might misunderstand its intent, I encourage you to think about exposing a minimal interface whenever possible.

Interfaces are a great way to minimize the chance that someone might use your class or function incorrectly.

Let’s look at this simple example, which introduces an `interface` with a subset of "safe" functionality:

```c#
interface IMyContainer
{
    float fValue { get; }
}
        
class MyContainer : IMyContainer
{
    ...
}
```

The interface `IMyContainer` exposes only the `fValue` property through a *getter*. There’s no way to set it.

If I now use the `interface` in a function instead of the `class` and try to do this:

```c#
static void CannotChangeReference(IMyContainer xCon)
{
    xCon.fValue = 5;
}
```

The compiler will complain (note the use of the `IMyContainer` interface vs. the `MyContainer` class).

This makes anyone who modifies the function stop and think about what they actually want to achieve — and often, the right solution is to make a copy:

```c#
static void CannotChangeReference(IMyContainer xCon)
{
    // This doesn't work because IMyContainer has no setter
    // xCon.fValue = 5;

    // Make a copy
    MyContainer oCon = new(xCon.fValue);
    oCon.fValue = 5;
}
```

Again, this all sounds more complex than it actually is — and most of the time, you can simply forget about it.

But there is something you *will* use sometimes: custom value types.

## Custom value types

Earlier, I gave examples of value types as *built-in* types, such as `float`, `int`, and similar.

But of course, you can create your own. Doing so is similar to defining a `class`, but you use the `struct` keyword instead:

```c#
public struct MyValue
{
    public float fValue
    {
        get => m_fValue;
        set
        {
            m_fValue = value;
        }
    }

    float m_fValue;
}
```

And because it’s a value type, this behaves exactly as we’d expect when we run our little test. Let’s implement our previous function again:

```c#
static void DontChangeVariable(MyValue oVal)
{
    oVal.fValue = 5;
}
```

And run it:

```c#
MyValue oValue = new();
oValue.fValue = 10;

DontChangeVariable(oValue);
Console.WriteLine(oValue.fValue);
```

Because it’s a value type, the contents of the `struct` are copied implicitly into the new variable. The result is that the value inside our original variable remains unchanged.

If we want to pass a reference and actually change the original variable, we can do that again:

```c#
static void ChangeVariable(ref MyValue oVal)
{
    oVal.fValue = 5;
}
```

Note the `ref` keyword.

Structs behave much like classes in many ways — but there are a few important differences:

- A `struct` always has an implicit default constructor that zero-initializes all its fields. This design exists for performance reasons — the idea is that a value type should be creatable without any constructor logic overhead.
- You cannot create abstract structs or use inheritance between structs — again, because they are simple value types.
- However, you *can* implement interfaces with a `struct`, and provide the required functions.
- You can mark an entire struct as `readonly`, which tells the compiler that the struct is immutable — allowing it to optimize away unnecessary copying.

## Summary

You can probably forget about this chapter for now — as long as you remember that classes are *Reference Types* that behave differently from *Value Types*. Instead of being copied, they always reference the original object.

If you need copies of objects, you have to explicitly create them with `new`. Most of the time, you don’t — and that’s exactly why the authors of C# designed it this way.

But with all of this behind us, let’s get back to engineering in the next chapters!

As always, the [code for this chapter is on GitHub](https://github.com/LinKayser/Coding4Engineers) (again quite basic this time).

------

Next: **Let's build a wing**

[Jump into the discussion here](https://github.com/leap71/PicoGK/discussions/categories/coding-for-computational-engineers)

[Table of contents](TOC.md)

------

**[PicoGK.org](https://picogk.org)/coding for engineers**

© 2024-2025 by [Lin Kayser](https://www.linkedin.com/in/linkayser/) — All rights reserved.