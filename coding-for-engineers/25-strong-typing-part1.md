**[2PicoGK.org](https://picogk.org)/coding for engineers**

![Coding for Engineers](assets/CodingforEngineers.jpg)

**[Table of contents](TOC.md)**

# Strong typing (Part 1) — An angle is not just a `float` 

I hope you enjoyed the previous chapter, which introduced you to — or reminded you of — the principles underlying non-Cartesian coordinate systems. One key characteristic of `Polar`, `Cylindrical`, and `Spherical` coordinate systems is that they use angles to represent one or two of their coordinates.

We discussed why angles are most naturally represented in *radians*. Rather than dividing a circle into an arbitrary 360 degrees, radians describe an angle as the ratio between the length of an arc and the radius of the circle. On a unit circle, this is numerically equivalent to asking: how far did I travel around the circumference, counterclockwise?

We therefore standardized our coordinate types on storing angles as floating-point values expressed in radians.

However, reread the previous chapter while keeping in mind everything you know about object-oriented programming and the importance of strong typing. Do you not get the nagging feeling that something is not quite right?

It is a little careless to store an angle in a generic `float`, is it not? We are simply hoping that everyone will understand what that number represents.

Should we not make its meaning explicit? Should an angle not be its own type?

The argument against introducing special types for seemingly trivial quantities is often that they add little more than ceremony. You introduce a substantial amount of code for something that may not appear to justify it.

This becomes especially inconvenient when the type has to participate in mathematical formulas and calculations. Most mathematical functions expect a `float`, so you may end up constantly converting your custom type back into a more generic numerical value.

It is probably not worth it, right?

That was exactly my thinking when I wrote the original angle-related code in PicoGK.

But after writing the previous chapter, I began to dislike the situation even more. There had to be a better way.

So I got to work.

And yes, there is a better way. Here is the journey.

I will use this example to introduce a multi-chapter series on strong typing and the representation of physical quantities in code.

Because if using a generic `float` to represent an angle is questionable, using one to represent distances, pressures, durations, energies, and other physical quantities is even less defensible.

All code in LEAP 71’s Computational Engineering Models is based on strongly typed physical quantities. There is an excellent C# library called *UnitsNet*, which implements more units and quantities than you are ever likely to need. It also provides an `Angle` type, but it treats angles like physical quantities with units and introduces exactly the overhead and “wordiness” that we cannot have for a type we use constantly.

An angle is unusual. It is a dimensionless number, but clearly not semantically interchangeable with any other dimensionless number. A value representing an angle behaves differently, supports different operations, and carries information that the compiler should help us preserve.

This makes angles a particularly interesting case for strong typing.

Strictly speaking, `float` is already a statically typed C# type. What we are really talking about is stronger *domain typing*: using types that describe what a number means, rather than merely how it is stored.

But I am getting ahead of myself.

Let us do angles properly.

## A type for angles

Let us start by writing an `Angle` data type. A `readonly struct` makes the most sense:

```csharp
public readonly struct Angle
{
    public Angle(float fAngleInRad)
    {
        m_fAngleInRad = fAngleInRad;
    }

    public float fAngleInRad => m_fAngleInRad;

    readonly float m_fAngleInRad;
}
```

How would we use it?

```csharp
Angle angle = new(Rad.TwoPi / 2f);
float fSin = float.Sin(angle.fAngleInRad);
```

Two issues are immediately apparent.

Let us begin with the second one: `float.Sin(angle.fAngleInRad)` is rather wordy. That is exactly the problem we face when using strongly typed angles in formulas, and it is one reason we have continued to use `float` values everywhere.

Instead of making the code more readable, our new type has filled it with helper stuff.

The other issue is the constructor:

```csharp
Angle angle = new(Rad.TwoPi / 2f);
```

This is reasonably clear when we use our convenient `Rad.TwoPi` constant. But consider this:

```csharp
Angle angle = new(1f);
```

What does that number represent? Is it one radian or one degree?

Not great.

Let us try to fix that problem. We can make the constructor `private` and provide named factory methods that make the intended conversion explicit.

First, remove `public` from the constructor:

```csharp
Angle(float fAngleInRad)
{
    m_fAngleInRad = fAngleInRad;
}
```

You will notice that this no longer works:

```csharp
Angle angle = new(1f);
```

The constructor that accepts a numerical value is now inaccessible from outside the `Angle` type.

We can instead add this method:

```csharp
public static Angle oFromRad(float fAngleRad)
{
    return new Angle(fAngleRad);
}
```

This is a `static` method that creates a new `Angle` value from a floating-point value expressed in radians.

Because the method is defined inside the `Angle` type, it can access the type’s private constructor. It can therefore construct a new value even though external code cannot call the constructor directly.

This is a common and useful pattern when you want to control how values of a type are created.

We can easily add another factory method:

```csharp
public static Angle oFromDeg(float fAngleDeg)
{
    return new Angle(Rad.TwoPi * fAngleDeg / 360f);
}
```

Usage now becomes much clearer:

```csharp
Angle angle1 = Angle.oFromRad(Rad.TwoPi / 2f);
Angle angle2 = Angle.oFromDeg(180f);
```

However, we still have the other issue:

```csharp
float fSin = float.Sin(angle.fAngleInRad);
```

That is simply too wordy for everyday use.

Would it not be useful if an angle automatically converted to a `float` when passed to a function that requires one?

Then we could write:

```csharp
float fSin = float.Sin(angle);
```

We can do exactly that by implementing a conversion operator:

```csharp
public static implicit operator float(Angle angle)
{
    return angle.m_fAngleInRad;
}
```

The keyword `implicit` means that the compiler can perform this conversion automatically.

We could instead declare the conversion as `explicit`. In that case, we would have to write:

```csharp
float fSin = float.Sin((float)angle);
```

The `(float)` cast explicitly invokes the conversion operator.

For our use case, an implicit conversion is probably what we want because it reduces visual noise in mathematical formulas.

However, several problems remain.

This is acceptable:

```csharp
Angle angle = Angle.oFromRad(2f);
```

But it is still rather wordy. Can we do better?

At the same time, although the following code is elegant, it hides an important convention:

```csharp
float fSin = float.Sin(angle);
```

The call site no longer tells us whether the angle is converted into radians, degrees, or some other numerical representation. The type is simply called `Angle`.

Perhaps that is the problem.

## Implementing a `Rad` type

Should we not make it explicit that this type represents an angle in radians?

We can still create it from degrees or other representations, but, as we established in the previous chapter, radians are the natural representation for trigonometric mathematics and the representation expected by the standard mathematical functions.

Let us rename the type `Rad`.

The first thing we can do is make the constructor public again.

This is dangerous:

```csharp
Angle angle = new(1f);
```

The numerical convention is not apparent from the type.

This, on the other hand, is reasonably clear:

```csharp
Rad rAngle = new(1f);
```

The type name makes it explicit that the value represents radians.

We can still provide a convenient conversion from degrees:

```csharp
public static Rad rFromDeg(float fAngleDeg)
{
    return new Rad(Rad.TwoPi * fAngleDeg / 360f);
}
```

We can also retain the implicit conversion to `float`.

Converting a value of type `Rad` into a `float` has an unambiguous meaning: the resulting floating-point value is expressed in radians. Where the generic name `Angle` left room for uncertainty, `Rad` sits on solid ground.

Let us look at the complete type so far:

```csharp
public readonly struct Rad
{
    public const float TwoPi = float.Tau;

    public Rad(float fAngleInRad)
    {
        m_fRad = fAngleInRad;
    }

    public static Rad rFromDeg(float fAngleDeg)
    {
        return new Rad(TwoPi * fAngleDeg / 360f);
    }

    public float fRad => m_fRad;

    public static implicit operator float(Rad rAngle)
    {
        return rAngle.m_fRad;
    }

    readonly float m_fRad;
}
```

You will notice that I have also shortened some of the names. The meaning remains clear because the type itself now carries much of the information.

For convenience, we can also add:

```csharp
public float fDeg => m_fRad * 360f / TwoPi;
```

We no longer have to repeat the conversion formula whenever we want to display an angle in degrees:

```csharp
float fDegrees = rAngle.fDeg;
```

Very nice.

What about mathematical functions that return a `float` representing an angle?

```csharp
float fRad = float.Asin(fVal);
```

We could write:

```csharp
Rad rAngle = new(float.Asin(fVal));
```

But this is again somewhat wordy.

We could implement an implicit conversion from `float` to `Rad`, but that would largely defeat the purpose of introducing the type. Every arbitrary floating-point value could then silently become an angle.

What about an explicit conversion instead?

```csharp
public static explicit operator Rad(float fRad)
{
    return new Rad(fRad);
}
```

We can now write:

```csharp
Rad rAngle = (Rad)float.Asin(fVal);
```

This is concise while still forcing us to acknowledge that we are interpreting the numerical value as radians.

For functions that specifically return angles, we can make the intention even clearer by wrapping them in the `Rad` API:

```csharp
public static Rad rAsin(float fValue)
{
    return new Rad(float.Asin(fValue));
}
```

Usage then becomes:

```csharp
Rad rAngle = Rad.rAsin(fVal);
```

This keeps the result strongly typed and states exactly what operation is being performed.

There is still a great deal of functionality missing.

It makes sense to define mathematical operators. Multiplying a `Rad` value by a `float`, for example, produces a scaled angle:

```csharp
Rad rHalfAngle = rAngle * 0.5f;
```

Adding or subtracting two `Rad` values also makes sense:

```csharp
Rad rTotal = rAngle1 + rAngle2;
```

Dividing one `Rad` value by another produces a dimensionless ratio:

```csharp
float fRatio = rAngle1 / rAngle2;
```

Multiplying one angle by another, however, makes no sense in this API. What would the result represent? So, we do not implement a `Rad * Rad` operator.

This points us toward an important aspect of the exercise: we can deliberately choose which operations are valid.

Multiplying an angle by a scalar makes sense and produces another angle. Dividing one angle by another makes sense and produces a dimensionless ratio. Multiplying an angle by another angle is meaningless here — and likely a bug — so the compiler rejects it.

When using a generic `float`, every one of these operations is syntactically valid. A `float` does not know that it represents an angle and therefore cannot protect us from nonsensical combinations.

Our `Rad` type can.

PicoGK’s complete `Rad` implementation provides considerably more functionality. It supports comparisons, exact and approximate equality, periodic comparison, normalization, and the common trigonometric and inverse-trigonometric operations.

Its string representation displays the angle in degrees, which is particularly convenient when logging values or inspecting them during debugging.

You can examine the full implementation here:

https://github.com/leap71/PicoGK/blob/main/Numerics/Angles.cs

With all of this out of the way, we can now return to the Chapter 24 source code and see what must be adapted to use the updated coordinate types.

PicoGK 2.3 introduces a few breaking changes, but the required fixes are straightforward.

## Using Coordinates with `Rad`

In the `Polar` coordinate example from Chapter 24, we have the following code:

```csharp
float fPhiTarget = Rad.TwoPi * 2f / 3f; // Two-thirds around the circle

PolyLine oPoly = new();

int nSteps = 100;
float fStep = fPhiTarget / nSteps;

for (int n = 0; n < nSteps; n++)
{
    co.Phi = n * fStep;
    oPoly.nAddVertex(co.vecAsCartesian().vecAsVector3());
}

oPoly.AddArrow();
Library.oViewer().Add(oPoly);
```

This no longer compiles because `co.Phi` is now of type `Rad`.

We could fix it using an explicit cast:

```csharp
co.Phi = (Rad)(n * fStep);
```

But that would be inelegant.

If we look more closely, both `fPhiTarget` and `fStep` represent angles. They should therefore be declared as `Rad` values.

Let us rewrite the example properly:

```csharp
Rad rPhiTarget = Rad.Full * 2f / 3f; // Two-thirds around the circle

PolyLine oPoly = new();

int nSteps = 100;
Rad rStep = rPhiTarget / nSteps;

for (int n = 0; n < nSteps; n++)
{
    co.Phi = n * rStep;
    oPoly.nAddVertex(co.vecAsCartesian().vecAsVector3());
}

oPoly.AddArrow();
Library.oViewer().Add(oPoly);
```

Because of operator overloading, the code is now about as clean and obvious as it can be.

It is also immediately apparent that `rPhiTarget` and `rStep` represent angles rather than arbitrary numerical values.

The following code from `CylindricalSpiral` now clearly distinguishes between angular steps in `Phi` and linear steps along the `Z`axis:

```csharp
Cylindrical coZero = new();

Cylindrical co = new();
co.R = 10f;

int   nSteps = 40;
Rad   rStep  = Rad.Full / nSteps;
float fZStep = 20f / nSteps;

for (int n = 0; n < nSteps; n++)
{
    co.Phi   = n * rStep;
    coZero.Z = n * fZStep;
    co.Z     = coZero.Z;

    Library.oViewer().AddArrow(
        coZero.vecAsCartesian(),
        co.vecAsCartesian());
}
```

The type system now explicitly distinguishes the angular coordinate from the linear coordinate.

If you are more comfortable thinking in degrees, you can also use named constants:

```csharp
Rad rThetaTarget = Rad.Deg90;            // At the equator
Rad rPhiTarget   = Rad.Deg360 * 2f / 3f; // Two-thirds around the circle
```

The following example shows literal radian values being explicitly converted inside a loop:

```csharp
for (co.Theta = (Rad)0.05f;
     co.Theta < Rad.Deg180 * 0.7f;
     co.Theta += (Rad)0.2f)
{
    // ...
}
```

Finally, the new type removes an obvious source of misunderstanding.

Previously, we could write:

```csharp
Polar co = new(1f, 0f); // Radius 1, Phi 0
```

Notice how we felt compelled to add a comment explaining that the second value represents an angle?

With the new type, we can write:

```csharp
Polar co = new(1f, (Rad)0f);
```

Or, even better:

```csharp
Polar co = new(1f, Rad.Zero);
```

The meaning is now apparent from the code itself.

We can also no longer accidentally swap an ordinary scalar parameter with an angular parameter without receiving a compiler error. The constructor requires a `float` radius and a `Rad` angle, and those types are not implicitly interchangeable in both directions.

## Summary

Stronger domain typing is an important foundation for verifiable computational models.

It allows us to catch errors that might otherwise remain undetected for a long time. It can also make code more expressive by moving information out of comments and naming conventions and into the type system itself.

Historically, the trade-off was that every new type required us to write a substantial amount of supporting code. That code took time to produce and could itself contain errors.

The common decision was therefore: not worth it. Let us use a generic numerical type and move on — we have stuff to do!

The effort is clearly worthwhile for something as ubiquitous as an angle. But what about less common quantities? Should we not simply use a `float` and be done with it?

The economics have changed considerably with the arrival of AI systems that can generate, test, and review much of the repetitive implementation work. It is no longer prohibitively expensive to create a dedicated type, even when that type is conceptually little more than a carefully controlled wrapper around a floating-point value.

Over the past year, we have gradually introduced even stronger domain types for many of our variables.

Where we previously used a *UnitsNet* `Pressure` value — which is already considerably safer than a generic numerical type — we are now beginning to distinguish between types such as `StaticPressure` and `DynamicPressure`.

Both represent quantities with the physical dimension of pressure, but they do not mean the same thing in an engineering model.

Similarly, a value of type `ChamberPressure` is fundamentally a pressure, but it may carry constraints, assumptions, or operations specific to a rocket combustion chamber.

The next chapters will examine the relationship between physical units, semantic meaning, and strong domain types in greater depth.

As always, the [code for this chapter is on GitHub](https://github.com/LinKayser/Coding4Engineers).

------

Next: **Strong typing (Part 2)** — coming soon.

[Jump into the discussion here](https://github.com/leap71/PicoGK/discussions/categories/coding-for-computational-engineers)

[Table of contents](TOC.md)

------

**[PicoGK.org](https://picogk.org)/coding for engineers**

© 2024-2026 by [Lin Kayser](https://www.linkedin.com/in/linkayser/) — All rights reserved.