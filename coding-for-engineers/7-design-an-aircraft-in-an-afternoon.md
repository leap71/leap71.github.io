"Let's go design a jet plane this afternoon!"

My team stared at me bewildered. They all had secretly suspected that I was nuts — but until then it hadn't been that obvious. 

It took me a few minutes to get them back inside, after their hasty departure through the fire exit — who wants to be in a room with a CEO gone mad.

But then we got to work, and we did it. Afterwards, they all understood the power of Computational Engineering.

In conventional engineering, you really can't design *anything* in an afternoon. Because just collecting the data and writing the specification of what you want to build will take much longer.

You cannot go ahead and defer fundamental design decisions until later. Everything needs to be crystal clear, especially when multiple components need to interact. There is a huge amount of upfront work, before you can create the first virtual pen stroke. What size plane are we talking about? What's the operational ceiling? 1 aisle, 2 aisles? How many passengers? Range? What is the mass? Is it subsonic, supersonic, swept wing? What engines? — And a gazillion other things, that nobody could even think about capturing in a few hours, let alone a few days. Just writing the spec, making fundamental design decisions, etc. can take months or years. 

Any work on the actual geometry of the plane that started before this process had completed, would be a complete waste of time.

In software it used to be similar. In the *waterfall* paradigm, nothing could be done before the previous stage in the development had been completed. You had to write a detailed software spec, which could take forever, and everyone then implemented their part of the system. In the end, everything fit together. Because your spec had been perfect.

The world doesn't work that way. 

The best way to create an advanced system of reasonable complexity is through iteration. Software was stuck until we got rid of the waterfall model, and started to build complex systems through iteration. Remember that in the past every variable and function was global, and everyone needed to know how everything interacted? Waterfall was the only way to tame that madness. 

How do you iterate on a jet plane? You don't, really. 

That's why, in engineering, things take forever — and please never change a running system! Just modifying one aspect of a machine's design, usually triggers massive manual rework. Sounds a lot like old software with its horrible side effects!

If you have a complex system, a good approach is usually *divide et impera*, Divide and Conquer. 

What worked for the Romans, works for engineers. You split a plane into a lot of subcomponents, and just define the physical interfaces between them. Now everyone can work on their part, and, as long as it fits the agreed-upon interface, you are fine. 

But do this too early, and now your plane looks like a cobbled-together LEGO toy. And it certainly is not globally optimized. So, Divide and Conquer using CAD and physical interfaces is really not that great. To define good physical interfaces, you already have to have a very detailed design specification of the plane. So, you can't really iterate. But still, this is how we do it in CAD. We divide the plane into many subcomponents with well-defined physical interfaces. And we accept that the rigidity of this approach means that we will not get to an optimal design.

And that's it.

Again, in software it used to be similar — sure, you could split your code things into modules, but if every variable and every function needed to used in the right way, and a misstep would affect the stability of the entire code base, if every change to the code triggered changes and rewrites everywhere else — you were forced into a very rigid way of working. 

The same ridig way all engineers work today.

But let's go ahead and design a jet plane using Computational Engineering this afternoon, using the modern software concepts of information hiding and software interfaces.

"Guys", I said, "Let's forget about how an airplane looks. What principal components do you think it has?"

Very simple questions. And, in software, if you don't capture all answers in the right way, you can always go back later. Information Hiding to the rescue.

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
  
}

public class Tail
{
  
}

public class Engine
{
  
}
```

Note, there are millions of details missing. Well, we do not care. We have just defined that an Airplane has two wings, a tail and a fuselage. We have created the logical structure, and we can fill in the blanks later.

For example, it's probably a reasonable assumption (for now!), that the two wings are fundamentally the same, but one is mirrored compared to the other.

Now, we can debate how to specify this, maybe we should just used a `bool` value, saying it's mirrored. But that's geometric thinking. The wing should probably know, whether it's on the left or right side. So let's use an `enum`. An enum is a value that is restricted to certain options. In our case, the enum variable of the wing will have two options `Left` and `Right`;

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

So, we defined an enum type called `ESide`, which can either have the values `Left` or `Right`.  We have added a constructor to our class, so that we can specify it. Now everyone who wants to instantiate an object of class `Wing` needs to tell the wing object, on which side it is.

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

Actually, we already know that the `Wing` class we are creating here is merely a placeholder for something much more complex that will depend on the type of aircraft we are actually designing. 

But the power of inheritance and polymorphism allow us to ignore all of that. We can define the fundamental aircraft, without dealing with the complexity of all the decisions which will follow later on.

How does the aircraft look like at this point?

We don't know!

That's the point. A Computational Engineering Model, at it's core, first defines the logical structure of the class of objects it will create. All we know is the logical structure of the thing — geometry is irrelevant at this point.

I want to emphasize this: *Geometry is irrelevant at this point!*

In traditional engineering, geometry is the centerpiece of your work. In Computational Engineering, it's the last thing that you output, you defer the physical expression of your algorithm until later in your work.

At some point, you will create a member function that may look like this:

```c#
public class Airplane
{
	public Voxels voxCreateGeometry()
  {
    
  }
}
```

This will interact with all of the components of the aircraft to design the physical shape. How will it look like? Well, that depends on the interaction of all the parts. These "parts", are not physical parts. They are logical components. They can communicate with other parts. The `Fuselage` and the `Wing` can collaborate to find the perfect aerodynamic shape, while still making sure the right number of passengers fit into the specified number of seats. The `Engine` can communicate its structural needs to the `Wing`, making sure it arrives on an optimum attachment point and and mechanical interface to support all the loads. Once it's done that, it outputs the right geometry.

Now, there is *a lot* of information that needs to go back and forth. Building a complete Computational Model for an entire aircraft is a massive undertaking. But, frankly, it's not fundamentally harder than building a new aircraft with conventional means. If you create a computational model, however, every decision you made, every reason why a shape looks like the one it doesn, every aspect of your design, is captured in source code. You can run parameter sweeps, you can "debug", you can verify, you can falsify hypotheses you took during the design.

A Computational Engineering Model, is fundamentally a scientific approach to engineering.

