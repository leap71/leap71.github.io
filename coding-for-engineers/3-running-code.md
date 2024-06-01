**[PicoGK.org](https://picogk.org)/coding for engineers**

![Coding for Engineers](assets/CodingforEngineers.jpg)

[0 — Preamble: My story](README.md)

[1 — Foreword](1-foreword.md)

[2 — Fundamentals](2-fundamentals.md)

[**3 — Running Code**](3-running-code.md)

[4 — Classes](4-classes.md)

[5 — Inheritance](5-inheritance.md)

[6 — Interfaces](6-interfaces.md)

[7 — Design an aircraft in an afternoon](7-design-an-aircraft-in-an-afternoon.md)

# Running code

The first computers were very bare bones — but they worked on the same principles as they do today. Computers run instructions (implemented in the hardware of the [Central Processing Unit](https://en.wikipedia.org/wiki/Central_processing_unit), CPU) on binary input data, (loaded from [Random Access Memory](https://en.wikipedia.org/wiki/Random-access_memory), RAM), to generate output data.

In the earliest days, you literally had to [hand-code using the instructions](https://en.wikipedia.org/wiki/Assembly_language) that the CPU supplied natively, which are extremely basic (but fast). Even today, some super-critical performance code is written that way.

But fortunately, we are many decades from of the beginnings of software, and today we use high-level languages to interact with the computer. In the end, however, what runs on your machine, are those low level commands that the processor understands.

To get from a high level language to machine-readable instructions, we use a *compiler*. It translates your code into an application, which can then be executed.

There are also other mechanisms. Instead of compiling, you could "interpret" the code as you go, translating it into machine commands, as it is executed. Interpreted languages, like Python, are popular, but they are orders of magnitude slower than compiled languages. It's pretty intuitive to grasp why: If you compile your code, the compiler can globally optimize your program, because it sees the entirety of your application. It can also spend longer amounts of time, looking for the optimum translation, because it doesn't matter whether it takes a little while. 

You compile once, and then your program runs at the native speed of the computer.

The disadvantage is, that your translated code is now specific to a processor and operating system. If you compiled for an Intel-based Windows system, the program doesn't run on a Mac, let alone an ARM-based Mac.

In order to get around the problem, people have introduced hybrid architectures. Microsoft's C# compiler translates your application into a [simple code that looks like machine-language](https://en.wikipedia.org/wiki/Bytecode), but is actually cross-platform. Because it is so similar to the instructions that will eventually run on your machine, and because the C# runtime [just-in-time compiles](https://en.wikipedia.org/wiki/Just-in-time_compilation) it to your target machine, when you launch the app, the speed is close to native code.

Native code is still faster, and that's why the [PicoGK Runtime](https://github.com/leap71/PicoGKRuntime), which is written in C++, is compiled for different archictures, and you need to [run the right installer to get PicoGK working on your system](https://github.com/leap71/PicoGK/releases).

The C# part of PicoGK is completely cross platform, and we try to implement as much as possible on that side, and only leave very performance-critical parts of the library to the compiled C++ code.

## Installing the IDE

In order to run your app, you first need to install a compiler on your system. Today, you usually use an Integrated Development Environment (IDE), which includes a code editor, compiler, and debugger. A debugger allows you to inspect your code while it is running, step through it one instruction at a time, and see the contents of your variables — very useful when you try to figure why stuff doesn't work the way you want.

We will be using [Visual Studio Code](https://code.visualstudio.com/), but everything explained here works similarly in other environments.

You can just install Visual Studio Code on your own, or [follow the instructions to install PicoGK](https://github.com/leap71/PicoGK/blob/main/Documentation), our open-source environment for Computational Engineering. While you don't need [PicoGK](https://picogk.org) for the initial code examples, everything you will learn later will be based on it.

Installing Visual Studio is probably the hardest thing you will do today. 

I am sorry.

Boy, I miss the days, when you switched on your computer, and you could just code...

But once you have it up and running, we can start with the creative part.

## Compiling your code

From the previous chapters we know that all code is encapsulated in functions. 

Now, there is one special function that is called when your application is launched. This function *is* your application. As long as you are inside that function, your app is alive. When it exits, your application terminates.

For the last couple of years this function was simply called `main()`, but recently, C# has simplified this a bit — so now the actual function is hidden.

[When you create a new "Console application" in C#](https://learn.microsoft.com/en-us/dotnet/core/tutorials/with-visual-studio-code), you will see a file called `Program.cs` — everything you put in this file is executed when the application launches, and once all the instructions are completed, your app terminates.

So, let's copy and paste the code we started with in the last chapter into `Program.cs`:

```c#
string str;
str = "Hello World";
WriteLine(str);
```

You will see that `WriteLine` is underlined with a wiggly red line, and when you try to compile, the compiler will complain with an error.

Why is that?

`WriteLine` is not defined anywhere, so the compiler doesn't know what it is supposed to do 

Remember that anything is a class in C#? So a function cannot exist without an associated class!

`WriteLine` is a function implemented in the system class `Console`. So, let's invoke it properly by adding the class name to it:

```c#
string str;
str = "Hello World";
Console.WriteLine(str);
```

If you compile the corrected code now, the compiler will not complain, and our application will run (very briefly, because it doesn't do much).

The debugger will show a lot of (really unnecessary) diagnostic messages in your output — but hidden in there, you will see a `Hello World.`

Congratulations, you have run your first app!

## Summary

Let's quickly summarize what we just did.

We created a new Console App project. This project has a single main function, implemented in `Program.cs`. For simplicity, C# treats everything in `Program.cs` as a single function. When your app launches, the function is run, when your function completes, the app terminates.

We copied the code from our first examples. We compiled it successfully, and then were able to display some text. 

Now, this sounds all a bit simple, but this is the foundation of every app on every platform — whether on your computer or on your mobile phone. 

All applications, including large ones, like a web browser or image editor, rely on this simple structure. 

Their main function is obviously much more complex, and creates windows for graphics and does many other things. 

To keep the program running for an extended period of time, the main function stays in a long loop, processing user input, until the user asks to terminate the application. When this happens, the app exits the loop, then it exits the main function — and the program terminates.

So now we have this out of our way, we can move on to the fun part.

-----

Next: [**Classes**](4-classes.md)

[Jump into the discussion here](https://github.com/leap71/PicoGK/discussions/categories/coding-for-computational-engineers)

------

**[PicoGK.org](https://picogk.org)/coding for engineers**
