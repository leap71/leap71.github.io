**[PicoGK.org](https://picogk.org)/coding for engineers**

![Coding for Engineers](assets/CodingforEngineers.jpg)

[**0 — Preamble: My story**](README.md)

[1 — Foreword](1-foreword.md)

[2 — Fundamentals](2-fundamentals.md)

[3 — Running Code](3-running-code.md)

## Preamble: My Story

I learnt coding in 1980, when I was 8 years old. Coding, programming, back then was simple. You just turned on the computer, and before the CRT screen of your TV set had warmed up to actually show anything, you could already type your first command. 

In fact, it was the only thing you could do with the thing.

Unfortunately, I did neither have a TV nor a computer. 

So I taught myself coding from a German computer magazine I bought at a newspaper stand. And I coded on a notebook — a paper notebook. It was so cool. When I was 10, I finally [got a programmable calculator, a TI-66](https://ja.wikipedia.org/wiki/TI-66#/media/%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB:TI-66.jpg). With it, I could start coding for real (really just steps of math, plus some control statements). [Inspired by my uncle's spacecraft ambitions](https://jlk.ae/2018/09/01/fly-rocket-fly-telling-the-story-of-uncle-lutz-attempt-to-conquer-space/), one of the first "apps" I wrote calculated the trajectory of a multi-stage rocket, using Tsiolkowsky's foundational equations (the math is actually not hard).

Finally, in 1985, I convinced my dad to buy a computer instead of a new typewriter. He spent what was then an enormous amount of money, especially for a struggling artist like he was, on a [state-of-the-art Amstrad CPC 664](https://en.wikipedia.org/wiki/Amstrad_CPC) personal computer (with whopping 64KB of memory).  

Then he got really angry with me: The computer couldn't do anything out-of-the-box. He spent all that money and now had less than a typewriter!

So in the first 20 minutes after unpacking the thing, I wrote a simple text editor. You could type one line, and after you hit Enter, it printed that line on the noisy 9-needle matrix printer, which quite falsely claimed to have "near letter quality". 

Now my dad had a typewriter on steroids (he could edit the line before it printed!). — And I could finally write computer programs for real.

Coding isn't hard. Programming languages have a few concepts that are fundamental to all or at least most of them. The syntax may vary, but the idea is the same. I went from programming in [BASIC](https://en.wikipedia.org/wiki/Locomotive_BASIC) and a bit of [Logo](https://simple.wikipedia.org/wiki/Logo_(programming_language)), to [Turbo Pascal 5.5](https://en.wikipedia.org/wiki/Turbo_Pascal), which introduced object-oriented programming, and then onwards to C++. 

I wrote C++ code most of my life. If you avoid a few traps and some of the ugliness that still stems from it "C" roots, it can be a very elegant language. But these days you frankly only need it for high-performance code, like the [PicoGK Runtime](https://github.com/leap71/PicoGKRuntime).

Today, modern programming languages, like C# or Java, keep the elegant aspects of C++ and eliminate the hard and ugly ones.

While you would think that the world of coding would have gotten easier over the past 40 years, unfortunately it hasn't. New programming languages are sprouting like weeds, and the simplicity of the early coding environments has been replaced by awkward, sometimes unnecessarily nerdy systems, that can be hard to navigate. Some of this stems from the rise of LINUX in the early 2000s, which swamped the world with hard-to-use command line tools, which, while flexible, appear like a nightmare flash from the past, for someone like me.

The hardest thing in the 1990s when installing Visual C++, was to insert the diskettes in the right order. Today, setting up Visual Studio requires a PhD in cryptography to decypher the error messages.

All this results in the unfortunate situation, where more and more people think coding is hard.

To make it less hard, well-meaning inventors of new programming languages like Python set out to eliminate weird things like curly braces or strong typing. Unfortunately, they are all solving the wrong issues. There is just very little well-written code out there, most of it unnecessarily obfuscates what it is doing. Many "elegant" ways of solving a problem are making it extremely hard to understand for a beginner what is actually happening. 

Actual elegant code is code where you understand at a glance what is going on.

So, coding has gotten harder. Most people don't code — they copy and paste stuff together until it works. That's why "tolerant" programming languages like Python have become so popular. You can actually write clean and elegant code in Python. It's just rarely done.

When my co-founder Michael and I had to decide on a programming language for Hyperganic, a company we started in 2015, we first looked into [Lua](https://www.lua.org/), a Pascal-like language that is very fast and robust. But it was lacking programming constructs like objects. [After Microsoft committed to open-sourcing C#](https://medium.com/microsoft-open-source-stories/how-microsoft-rewrote-its-c-compiler-in-c-and-made-it-open-source-4ebed5646f98#:~:text=In%20April%20of%202014%20at,under%20an%20Apache%202.0%20license.), it became a much better choice.

When Josefine and I decided on the foundation for [PicoGK](https://leap71.com/PicoGK/), C# was again the front runner. We looked into Python, because of its popularity — but its lack of multithreading, it's terrible performance, and the many issues in terms of being too tolerant, made us quickly discard it.

Why is being "tolerant" in programming a bad idea? A programming language should make you express your intent clearly and unambiguously. This is why it is a formal language with logical constructs. If something isn't clear, it should't work. That's the beauty of C#, it is actually very strict (stricter than C++), and this forces you to write good code.

Do we still need to write code in an age of ChatGPT?

Boy, yes we do.

It is exactly because of the fuzzyness of natural language that we need a way to rigorously encode logic. Just like you wouldn't try to describe physical formulas with Shakespearean language, you should not attempt to encode logic into an LLM. I spend a lot of time on this, because at LEAP 71 we want to eventually build a natural language interface to Computational Engineering. But natural language only works on a very fuzzy level, you need verifiable scientific code as the basis.

So, yes, you should learn how to code. 

Not only is it an important skill to have, it teaches you a way of thinking, computational reasoning, that will help you navigate our world in a structured way. I stopped coding in 2005, after IRIDAS, one of my startups, took off, and I had to focus on running the company. But being a coder made me a better CEO.

These days I am back to spending most of my days coding — and I enjoy it massively. It's one of the most engaging tasks I have ever done. In my 20s I coded for dozens of hours at a time, because I was [in a flow state](https://en.wikipedia.org/wiki/Mihaly_Csikszentmihalyi). In a world that is full of distractions, it's an amazing experience.

Writers will know this too. Writing and coding are some of the things you can do, that let you forget time and just create.

So, in the next couple of months, I will write, to teach you how to code.

**Welcome to coding for engineers.**

------

Next: **[Foreword](1-foreword.md)**

[Jump into the discussion here](https://github.com/leap71/PicoGK/discussions/categories/coding-for-computational-engineers)

------

**[PicoGK.org](https://picogk.org)/coding for engineers**
