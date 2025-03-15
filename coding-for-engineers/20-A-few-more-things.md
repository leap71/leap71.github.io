**[PicoGK.org](https://picogk.org)/coding for engineers**

![Coding for Engineers](assets/CodingforEngineers.jpg)

**[Table of contents](TOC.md)**

# A few more things you should know about C#

After this rather intense dive into computational geometry, let’s explore some key aspects of C#, you should know. 

While I advocate for a very readable programming style, there are many ways to code in C# that can simplify your work — though their syntax might seem a bit daunting at first glance.

It's obviously up to you whether you use these concepts, but you should at least be familiar with them.

Let's start with something that you absolutely will use. Logical boolean operators.

## Boolean operators

Up until this chapter, we have been able to ignore complex `if` statements. This, by the way, is a sign of good object-oriented design. If you have to write many conditional clauses and switch between many different behaviors, chances are, your class architecture is not well-designed. 

Maybe instead of writing an `if` you should have split functionality into two different derived classes.

But of course, `if` is a constant companion of your life when you write code.

You already know the fundamental structure of an `if` clause:

``` c#
if (bSomethingTrue)
  DoSomething();
else
  DoSomethingElse();
```

Which is of course short for:

```c#
if (bSomethingTrue == true)
  DoSomething();
else
  DoSomethingElse();
```

With `==` being the *equality operator*. 

The equality operator allows you to check whether two values are the same, in this case, whether `bSomethingTrue` equals `true`.

What if you only wanted to do something if the expression is `false`?

You could write:

```c#
if (bSomethingTrue == false)
  DoSomethingElse();
```

and that would be entirely fine.

You could also use the *inequality operator*: `!=`.

```c#
if (bSomethingTrue != true)
  DoSomethingElse();
```

Which evaluates to `true` if `bSomethingTrue == false`.

But the more elegant way is to use the *NOT* operator, which inverts the expression. 

The *NOT* operator is simply an exclamation mark: `!`;

So you write:

```c#
if (!bSomethingTrue)
  DoSomethingElse();
```

Which reads as *If not SomethingTrue, then DoSomethingElse*.

Sometimes you may want to check for more than one condition. *If SomethingTrue AND SomeThingElseTrue, then...*.

In this case, you need an *AND* operator. The *AND* operator looks like this: `&&`

So you write:

```c#
if (bSomethingTrue && bSomethingElseTrue)
  DoSomething();
else
  DoSomethingElse();
```

You will only end up in `DoSomething()` if both conditions are `true`. 

If either one of the two expressions is `false`, you end up in `DoSomethingElse`.

What about if you only need one of these conditions to be true? Enter the *OR* operator: `||`.

```c#
if (bSomethingTrue || bSomethingElseTrue)
  DoSomething();
else
  DoSomethingElse();
```

You can obviously combine different operators:

```c#
if (bSomethingTrue && !bSomethingElseTrue)
  DoSomething();
else
  DoSomethingElse();
```

Here we require `bSomethingTrue` to be `true` and `bSomethingElseTrue` to be `false` ("not true");

You can go crazy, using brackets to combine expressions:

```c#
if (	(bSomethingTrue || !bSomethingElseTrue) &&
    	(bAnotherCondition && bAFourthCondition))
  DoSomething();
else
  DoSomethingElse();
```

But things can get hard to comprehend quickly. It is better to keep `if` clauses simple — they shouldn't require a PhD in boolean algebra. Stack multiple `if` clauses to avoid overly complex conditions, which, usually turn out to be the source of horrible bugs.

If you get confused, remember to read things out loud, substituting `!` for *NOT*, `&&` for *AND*, and `||` for *OR*.

## The ? operator in assignments

Very often, you will write code that looks like this:

```c#
if (bSomeCondition)
  iVariable = iSomething;
else
  iVariable = iSomethingElse;
```

That's all fine. But if you need to allocate the variable, you have to be careful — you cannot do:

```c#
if (bSomeCondition)
  int iVariable = iSomething;
else
  int iVariable = iSomethingElse;
```

Because now you declared two instances of `iVariable` which don't exist outside their `if`/`else` clauses.

So, you have to do something like this:

```c#
int iVariable;
if (bSomeCondition)
  iVariable = iSomething;
else
  iVariable = iSomethingElse;
```

Now, this is kind of wordy. Second, it violates the principle that a variable should always be initialized. If you make changes to the code later, you may forget that `iVariable` was not assigned a value at declaration, and end up with a problem.

You might be tempted to do something like that:

```c#
int iVariable = iSomething;
if (!bSomeCondition)
  iVariable = iSomethingElse;
```

But now you are unnecessarily assigning a value for the case that `bSomeCondition` is `false`.

This isn’t dramatic, but you can see why smart people thought there should be a more elegant way.

Enter the `?` operator.

What you can do is write:

```c#
int iVariable = bSomeCondition ? iSomething : iSomethingElse;
```

The `?` is the equivalent of an `if` and tests if the condition preceding the `?` is `true`. 

If it is, it will use the value before the `:` else, if the condition is `false`, it will use the value after the `:`.

It may look odd at first, but once you get used to it, I bet you’ll appreciate it.

It works in other contexts as well, such as:

```c#
int nCalculateNumber()
{
  return m_bInitialized ? m_nNumber : 0;
}
```

Which, as you might have guessed, returns `0` if the variable `m_bInitialized` is `false`;

The idea of an "unitialized value" brings us to the next concept.

## Nullable values

Other programming languages have the concept of *pointers* to variables. These pointers contain the address of a variable in memory. Since the variable doesn't contain the value directly, it needs a way to express that it is not pointing to anything. In that case, the pointer points to `null`, a special value that says, "I am not pointing anywhere."

C# for many good reasons doesn't use pointers. But it has the concept of a *nullable variable*. And it's useful for exactly the case when there is a possibility that your variable does not have a valid value yet. 

Note that this is not the same as an *uninitialized variable*. The C# compiler warns you if you don't initialize a variable, so it should not happen. 

A *nullable* variable is initialized to `null`, indicating it doesn't point to an object yet.

To declare a nullable variable, you write a `?` after the type:

```c#
Vector3? m_vecOrigin = null;
```

If you write the above, you have an initialized variable named `m_vecOrigin` that points to `null`.

To test for `null` you can do multiple things:

```c#
if (m_vecOrigin == null)
   ...
  
if (m_vecOrigin != null)
   ...
  
 if (m_vecOrigin is null)
   ...
   
 if (m_vecOrigin is not null)
   ...
```

Use this feature sparingly, as it can complicate your code. 

For example, instead of using a nullable object, you can assign an object that simply doesn't do anything, which might be a cleaner approach.

There is, however, one use case, where it comes in very handy: Default parameters in functions.

Let's say you want to create a constructor for an object and pass a default value for a `Vector3`. 

You might be tempted to write something like this:

```c#
public class SomeClass
{
  public SomeClass(Vector3 vecOrg = Vector3.Zero)
  {
    m_vecOrigin = vecOrg;
  }
  
  ...
}
```

And to your consternation, you will find that C# doesn't allow you to assign default values to variables that are not basic values (such as simple numbers).

You can, of course, solve this by not using a default parameter and by adding a second constructor:

```c#
public class SomeClass
{
    public SomeClass(Vector3 vecOrg)
    {
        m_vecOrigin = vecOrg;
    }

    public SomeClass()
    {
        m_vecOrigin = Vector3.Zero;
    }

    Vector3 m_vecOrigin;
}
```

But wouldn't it be nice to be able to use default values for non-value types?

You can, by using a nullable variable (which can have a value, the value `null`).

```c#
public class SomeClass
{
  public SomeClass(Vector3? vecOrg=null)
  {
    ...
  }
}
```

Note the `?` after `Vector3`, which makes the variable nullable.

That’s perfectly valid and actually a good use case. What does the function body look like?

```c#
public class SomeClass
{
  public SomeClass(Vector3? vecOrg=null)
  {
      if (vecOrg is null)
          m_vecOrigin = Vector3.Zero;
      else
          m_vecOrigin = vecOrg;
  }
}
```

Unfortunately, this, again, is quite wordy and not really easy to read. 

Furthermore, the C# compiler complains, since `Vector3` and `Vector3?` are actually not the same type (one is a nullable type, one is not). 

You can fix the error by assigning the `Value` property of the `Vector3` nullable object, which you can access like this:

```c#
public SomeClass(Vector3? vecOrg=null)
{
  if (vecO is null)
      m_vecOrigin = Vector3.Zero;
  else
      m_vecOrigin = vecOrg.Value;
}
```

Using our knowledge of the `?` operator, we can make it more concise:

```c#
public SomeClass(Vector3? vecOrg=null)
{
  m_vecOrigin = vecOrg is null ? Vector3.Zero : vecOrg.Value;
}
```

This is much shorter, and quite readable. Still it feels a bit messy with the `vec0.Value`.

Since this is used frequently, the authors of C# have found a way to make the construct even shorter:

```c#
public SomeClass(Vector3? vecOrg=null)
{
  m_vecOrigin = vecOrg ?? Vector3.Zero;
}
```

This expression assigns `vecO` if it is not `null`. If it is `null` the value after the `??` is used.

This is way cleaner, and you don't have to worry about the differences in the two types, the conversion happens behind the scenes.

Again, feel free to not use if you think it's too confusing. However, it indeed is a handy shorthand which you should be familiar with.

## The expression body operator =>

The expression body operator, which is written as `=>` is a way to shorten simple function bodies. Imagine you have a class that looks like this:

```c#
public class AClass
{
    public static int nNumber()
    {
        return 42;
    }

    public static int nAdd(int n1, int n2)
    {
        return n1 + n2;
    }
}
```

Using the expression body operator, you can shorten this to:

```c#
public class AClass
{
    public static int nNumber() => 42;

    public static int nAdd(int n1, int n2) => n1 + n2;
}
```

See how you can simply write the result that should be returned after `=>`, and you are done.

This, obviously, only makes sense for very short function bodies, so you will rarely see it used for complex logic.

One area where you will see it is in the declaration of *properties* of classes. 

Let's look at a class like this:

```c#
public class AClass
{
    public AClass(Vector3 vecValue)
    {
        m_vecValue = vecValue;
    }

    Vector3 m_vecValue;
}
```

Note that this class has a variable named `m_vecValue`. To the outside of the class, `m_vecValue` is not accessible, as by default it has `private` visibility.

Now, if we want to make it accessible to the outside world, we could do this:

```c#
public class AClass
{
    public AClass(Vector3 vecValue)
    {
        m_vecValue = vecValue;
    }

    public Vector3 vecVal()
    {
        return m_vecValue;
    }

    Vector3 m_vecValue;
}
```

And we can access the number by calling:

```c#
AClass oObj = new(Vector3.UnitX);
Vector3 vec = oObj.vecVal();
```

Using the expression body operator, we can somewhat simplify the access function by writing:

```c#
public class AClass
{
    public AClass(Vector3 vecValue)
    {
        m_vecValue = vecValue;
    }

    public Vector3 vecVal() => m_vecValue;

    Vector3 m_vecValue;
}
```

But there is another mechanism that C# gives us to access values of an object: *Properties*.

Properties are accessor functions that don't look like functions because they don't use `()`.

In other words:

```c#
AClass oObj = new(Vector3.UnitX);
Vector3 vec = oObj.vecVal;
```

Note the missing parentheses. We are now accessing `vecVal` like a public variable. But in the background, we are actually executing the accessor function. 

Let's see how this works:

```c#
public class AClass
{
    public AClass(Vector3 vecValue)
    {
        m_vecValue = vecValue;
    }

    public Vector3 vecVal => m_vecValue;

    Vector3 m_vecValue;
}
```

Note, all we had to do was remove the parentheses of the access function to make it a property.

You will see the `=>` operator used in properties a lot, as it removes a lot of tedious code. 

We will dive into properties, and when to use them, in the next chapter.

## Summary 

Some of the things written in this chapter may look complex, and this perceived complexity is one reason why coding has gained an aura of being accessible only to some strange breed of humans who communicate using obscure symbols. 

Early programming languages were quite wordy and literal, spelling out every step in long English words. Over time, people lost patience and invented shortcuts. Some of the clarity found in languages like BASIC or Pascal got lost along the way. In Pascal, you wrote `Begin` and `End` where you now write `{` and `}` in C#. While `Begin` and `End` are much clearer, they also require a lot more typing and result in more words on the screen, which can actually *obscure* the structure of your code.

Personally, I am always a bit torn between writing ultra-clear code that even the most basic coder can understand, and using some of the concepts I explained above, which — when used correctly — are more elegant but require a bit of advanced understanding. Finding the right balance is hard. If in doubt, always err on the side of readability. It makes your life much easier a few months down the road when you have to revisit your code.

It's easy to get carried away using every feature in a programming language, but the result might even stump boolean logic geniuses. The best code looks trivial and reads like a book. It also usually contains the fewest errors.

As always, the [code for this chapter is on GitHub](https://github.com/LinKayser/Coding4Engineers) (quite basic this time).

------

Next: **A few useful shortcuts in coding that you should know**

[Jump into the discussion here](https://github.com/leap71/PicoGK/discussions/categories/coding-for-computational-engineers)

[Table of contents](TOC.md)

------

**[PicoGK.org](https://picogk.org)/coding for engineers**

© 2024-2025 by [Lin Kayser](https://www.linkedin.com/in/linkayser/) — All rights reserved.