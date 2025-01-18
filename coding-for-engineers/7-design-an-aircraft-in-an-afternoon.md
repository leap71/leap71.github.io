**[PicoGK.org](https://picogk.org)/coding for engineers**

![Coding for Engineers](assets/CodingforEngineers.jpg)

**[Table of contents](TOC.md)**

# Design an aircraft in an afternoon

Back in 2021, at my last company, [Hyperganic](https://jlk.ae/2022/05/11/we-just-built-the-worlds-largest-3d-printed-aerospike-rocket-engine/), I asked the team in my Singapore office to gather around.

"Let's design a jet plane this afternoon!"

My team stared at me bewildered. They all had secretly suspected I was nuts — but until then it hadn't been that obvious. 

It took me a few minutes to get them back inside, after their hasty departure through the fire exit — who wants to be in a room with a CEO gone mad...

But then we got to work, and we did it. Afterwards, they all understood the power of Computational Engineering.

In conventional engineering, you really can't design *anything* moderately complex in an afternoon. Because just collecting the data and writing the specification of what you want to build will take much longer.

You cannot go ahead and defer fundamental design decisions. Everything needs to be crystal clear, especially when multiple components have to interact. There is a large amount of upfront work before you can create the first virtual pen stroke. What size plane are we talking about? What's the operational ceiling? 1 aisle, 2 aisles? How many passengers? Range? What is the mass? Is it subsonic, supersonic, swept wing? What engines? — And a gazillion other things, that nobody could even think about capturing in a few hours, let alone a few days. Just writing the spec, making fundamental design decisions, etc. can take months or years. 

Working on the actual geometry of the plane before this process has completed, would be a complete waste of time.

In software it used to be similar. In the *waterfall* paradigm, nothing could be done before the previous stage in the development had been finished. You had to write a detailed software spec, which could take forever, and everyone then implemented their part of the system. In the end, everything fit together. 

Because, naturally, your spec had been perfect and you had thought of everything....

The world doesn't work that way. 

The best way to create an advanced system of reasonable complexity is through iteration. Think about the problem, yes, but then get started in a lightweight way, find what's missing, iterate.

## Innovation requires iteration

Software was stuck until we got rid of the waterfall model, and we started to build complex systems iteratively. Remember that in the past every variable and function was global, and the entire team needed to know how everything interacted? Waterfall was the only way to tame that madness. 

How do you iterate on a jet plane using conventional CAD? 

You can't. You need to pre-plan everything.

That's why, in engineering, things take forever — and please never change a running system! Just modifying one aspect of a machine's design, triggers massive manual rework. Sounds a lot like the old software paradigm with its horrible side effects!

If you have a complex system, a good approach is usually *divide et impera*, Divide and Conquer. 

What worked for the Romans, works for engineers. You split a machine into subcomponents, and define the *mechanical* connecting interfaces. Now everyone can work on their part, and, as long as it fits the agreed-upon connectors, you are fine. 

But do this too early, and now your plane looks like a cobbled-together LEGO toy. And it certainly is not globally optimized. So, Divide and Conquer using CAD and physical flanges and screws is really not that great. To define good mechanical interfaces, you already have to have a very detailed design specification of the plane. So, you can't really iterate. But still, this is how we do it in CAD. We divide the plane into many subcomponents with well-defined physical connection points. And we accept that the rigidity of this approach means that we will not get to an optimal design.

And that's it.

Again, in software it used to be similar — sure, you could split your code things into modules, but if every variable and every function had to be used in the right way, and a misstep would affect the stability of the entire code base, if every change to the code triggered rewrites in other places — you were forced into a very rigid way of working. 

The same rigid way all engineers work today.

## Designing an airplane using interfaces, placeholders, and stubs

But let's go ahead and design a jet plane using Computational Engineering this afternoon, using the modern software concepts of information hiding and software interfaces.

"Guys", I said, "Let's forget about how an airplane looks. What principal components do you think it has?"

Very simple questions. And, in software, if you don't capture all answers right away, you can always go back later. Information Hiding to the rescue. 

So, we came up with a very simple structure for an airplane.

We agreed, that an airplane, fundamentally has two wings, for simplicity's sake, every wing would have one engine, there is a fuselage, and a tail section, which seemed a bit complex, so we would deal with it later.

With that information, it isn't hard to come up with a class structure for an airplane:

```c#
public class Airplane
{
    Wing      m_oWingLeft   = new();
    Wing      m_oWingRight  = new();
    Fuselage  m_oFuselage   = new();
    Tail      m_oTail       = new(); 
}

public class Wing
{
    Engine m_oEngine = new();
}

public class Fuselage
{
     // We will figure it out
}

public class Tail
{
    // We will figure it out
}

public class Engine
{
    // Some complex stuff here
}
```

Note, there are millions of details missing. Well, we do not care! We have just defined that an airplane has two wings, a tail and a fuselage. We have created the logical structure, and we can fill in the blanks later.

If you don't know what information you need to be able to pass, you can at least agree on the fact *that information has to pass*. Which means you need to define an interface that you will use later to query for that information. 

If you don't know how a function will work, you can always put a stub in there, a placeholder function. If you don't yet have all the physical formulas that make up the thermal model of your engine, then just return some dummy data, and deal with it later.

If you are unsure about the exact details, you can still agree on the general structure of your class hierarchy, using the questions we defined in the last chapter (is a..., has a..., behaves like a ...)

For example, it's probably a reasonable assumption (for now!), that the two wings are fundamentally the same, but one is mirrored compared to the other.

Now, we can debate how to specify this, maybe we should have just used a `bool` value, saying it's mirrored. But that's geometric thinking. The wing should probably know, whether it's on the left or right side. So let's use an `enum`. An enum is a value that is restricted to certain options. In our case, the enum variable of the wing will have two options `Left` and `Right`;

```c#
public class Wing
{
    public enum ESide {Left, Right};
  
    public Wing(ESide eSide)
    {
        m_eSide = eSide;	
    }
  
    Engine  m_oEngine = new();
    ESide   m_eSide;
}
```

So, we defined an enum type called `ESide`, which can either have the values `Left` or `Right`.  We have added a constructor to our class, so that we can specify it. Since this is the only constructor, everyone who wants to instantiate an object of class `Wing` needs to tell the wing object, on which side it is.

And that's what we will do in our modified `Airplane` class.

```c#
public class Airplane
{
    Wing      m_oWingLeft   = new(Wing.ESide.Left);
    Wing      m_oWingRight  = new(Wing.ESide.Right);
    Fuselage  m_oFuselage   = new();
    Tail      m_oTail       = new(); 
}
```

Now, it's an interesting question, whether we are making the right assumption here. Are the left and right wings of an airplane really identical mirror images? Maybe they actually differ? Shouldn't we have separate classes for the two wings?

Very valid questions, but this really doesn't matter at this point! 

We can always derive a new class from `Wing` and call it `RightWing` and change everything that needs to be modified. 

Actually, we already know that the `Wing` class we are creating here is merely a placeholder for something much more complex that will depend on the type of aircraft we are designing. 

But the power of inheritance and polymorphism allows us to ignore all of that. We can define the fundamental aircraft, without dealing with the complexity of all the decisions which will follow later on. We can derive classes which are drop-in-replacements for the more generic classes.

How does the aircraft look like at this point?

We don't know!

That's the point. A Computational Engineering Model (CEM), at its core, first defines the logical structure of the class of objects it will create. Geometry is irrelevant at this point.

I want to emphasize this: *Geometry is irrelevant at this point!*

## Geometry is the last thing on your mind

In traditional engineering, CAD geometry is the centerpiece of your work. In Computational Engineering, it's the last thing that you output, you defer the physical expression of your algorithm until very late in your work. There are so many things to calculate, so much information that needs to flow back and forth, until, finally, you create geometry that expresses the insights you gained from your computations.

But, at some point, you will create a member function that may look like this:

```c#
public class Airplane
{
    public Shape oCreateResultingGeometry()
    {
        ....
    }
}
```

This function will interact with all of the components of the aircraft to design the physical shape. How will it look like? Well, that depends on the interaction of all the parts. These "parts", are not physical parts: they are logical components. They can communicate. The `Fuselage` and the `Wing` can collaborate to find the perfect aerodynamic shape (and maybe for one coherent whole, ignoring the fact that right now we treat them as separate logical parts), while still making sure the right number of passengers fit into the specified number of seats. The `Engine` can communicate its structural needs to the `Wing`, making sure it finds an optimum attachment point and mechanical interface to support all the loads. Once it's done that, it outputs the right geometry.

Now, there is *a lot* of information that needs to go back and forth. Building a complete Computational Model for an entire aircraft is a massive undertaking. But it's not fundamentally more massive than designing a new aircraft through conventional means. If you create a computational model, however, every decision you made, every reason why a shape looks like the one it does, every aspect of your design, is captured in source code. You can run parameter sweeps, you can "debug", you can verify, you can falsify the hypotheses you postulated during the design. And you can reuse it, dramatically cutting down the development time of the next iteration of your plane.

A Computational Engineering Model is a scientific approach to engineering. Science requires facts to be clearly and publicly stated. The source code of a Computational Engineering Model is such a statement. It describes the decision process, the assumptions, the used physical models, and the design logic it its entire detail. There is no room for obscurity.

## Building an airplane

So, how do we progress from here? Here's what I did with my team back then: We asked groups of two people each to take one component of the plane. One team took on the fuselage, one pair worked on the wings, others the engine, etc. I asked them to discuss what they would need to know, at minimum (information hiding) from the other teams. And then I asked them to encapsulate all of that in C# interfaces.

We used placeholder geometry as stand-ins for the components. The wings were just flat rectangles, the fuselage a tube, the engines smaller tubes. Using the information passed through the interfaces, we could start generating these placeholders in the general sizes and places. We discovered information which was missing, and added it to the relevant interfaces. The team working on the wings implemented the [NACA](https://en.wikipedia.org/wiki/NACA_airfoil#:~:text=The%20NACA%20four%2Ddigit%20wing,as%20percent%20of%20the%20chord.) profiles, and tried to figure out optimum shapes from the specifications, etc.

Increasingly, we started to see funny looking aircraft coming out of different parameter sets. Very simple children's toy versions of aircraft, but all generated by the same, increasingly sophisticated algorithm.

It was a fun exercise and taught many lessons. We decided to move it one step further.

From a previous project, we had a computational model of a high-bypass-ratio turboprop engine available. Couldn't we use that to replace the engine placeholders?

And of course we could. And there we had it, a children's toy airplane, with actual modern jet engines attached.

Unfortunately I don't have access to the pictures anymore, but it doesn't matter. Over the coming weeks, we are going to recreate it.

Because now that we know how to design an aircraft in a day, it's time to delve into [PicoGK](https://leap71.com/picogk/), LEAP 71's compact and robust open-source geometry kernel for engineering. It's time to generate shapes driven by algorithms.

## Summary

So, there is very little code in this chapter, and maybe you are disappointed, that the inaugural flight of our computational plane will take bit longer.

But I hope I made clear why we learned the things in the previous chapters, and how this approach differs fundamentally from traditional engineering.

In conventional engineering workflows, the reasoning, the thought process, the physical formulas, all these *are external to* the daily work of the engineer. The engineer's main labor is to visually create the object in CAD.

Computational engineers don't draw, they write down the logic, the physics, the heuristic models, etc. in code. They try to make the computational model consistent, something that is often surprisingly difficult to do. The flow of information, the abstract components, are more important than the shape of a part. Because the computational model is built on the entirety of the information, the resulting geometry can follow much more flexibly, than in objects that are built piecemeal from individual parts. A computational model has a general overview of the entire object.

- In Computational Engineering Models, we think logical and scientific first, and geometric second
- It's fine to put placeholder objects, functional "stubs", empty interfaces in place and fill in the details later
- It's fine to say: "I don't know yet, I'll figure this one out later"
- It's relatively easy to change things, so don't follow a waterfall model, where the initial spec needs to be perfect
- Class inheritance and polymorphism allow us to make substantial changes as we progress, by replacing a component with a derived class. So you don't have to worry about creating, for example, a `Wing` object that handles all kinds of wings. You can deal with a simple case first, and later derive a `SupersonicWing` later. You can go from a placeholder for an aircraft engine to a turboprop in a derived class. The interface stays the same.

Done with theory, let's see some visual output next week.

------

Next: [**First steps in PicoGK**](8-first-steps-in-picogk.md)

[Jump into the discussion here](https://github.com/leap71/PicoGK/discussions/categories/coding-for-computational-engineers)

[Table of contents](TOC.md)

------

**[PicoGK.org](https://picogk.org)/coding for engineers**

© 2024 by [Lin Kayser](https://www.linkedin.com/in/linkayser/) — All rights reserved.
