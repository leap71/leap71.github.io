**[PicoGK.org](https://picogk.org)/[Labs](https://picogk.org/labs)**

# Experimenting with LLMs and Computational Engineering

**2024-03-31 By Lin Kayser**

## Introduction

![jvis](jvis.jpeg)

For the past week I have been trying to put to the test, some of the hypotheses I layed out in my article [Computational Engineering today, AI Engineering tomorrow, and when do we get J.A.R.V.I.S.?](https://jlk.ae/2023/03/29/computational-engineering-today-ai-engineering-tomorrow-and-when-do-we-get-j-a-r-v-i-s/) I published excactly a year ago and which I followed up in January with [On Tribal Knowledge, LLMs and Computational Knowledge in Engineering](https://jlk.ae/2024/01/01/on-tribal-knowledge-llms-and-computational-knowledge-in-engineering/).

In the article I describe how attempts to "somehow" train an AI to directly generate functional physical objects are likely futile. Showing a neural net tens of thousands of jet engines (even if you had access to detailed schematics), and then thinking it would invent a new one is wishful thinking. Engineering is based on physics, math, logic, hard computational knowledge. The "soft" world of machine intuition is simply not a good fit. You need a Computational Engineering Model based on ironclad scientific rules. Translating engineering knowledge to computer code is what we do at LEAP 71, so we are building the models in the fields we are working in. Now, the question is, if you have a Computational Model that creates a physical object (like we do), can you train on LLM on this code, and create a conversational interface to engineering? Essentially, can you create J.A.R.V.I.S. from Iron Man? 

From my initial tests, I can say, absolutely, but ... it's going to require effort!

In my experiments, which were as basic as you can imagine, I used OpenAI's public API to automatically send prompts to, essentially, ChatGPT, which include a code example, to give it context and then ask the LLM to invent something new, based on my natural language description. My app then automatically extracted the code the LLM gave me, ran it through the [Roslyn framework](https://en.wikipedia.org/wiki/Roslyn_(compiler)) which allows us to compile code from within a C# application. If errors were reported, the app flags them and automatically ask ChatGPT to fix them, until we get something that compiles. Now OpenAI's context window is not large enough to send the entire PicoGK source code, and megabytes of examples, let alone a comprehensive Computational Engineering Model, so a lot of it is hit or miss. But you can see where this could go, if you had a dedicated model trained on a large Computational Engineering code base. 

## Setup

There are a few components I had to write in order to run this experiment. First, I needed a way to communicate with the OpenAI API, which thanks to ChatGPT, is very simple to write. 

I will omit most of the code for brevity, but essentially I have a way to send a prompt to OpenAI's API, and get a PromptResponse object back. I store the responses, because I will send the historic prompts in follow up prompts, so the LLM has more context.

```c#
using System.Text;
using System.Text.Json;
using PicoGK;

namespace Leap71.AI
{
	public partial class ChatGPT
	{
		public class PromptResponse
		{
      //...
		}

		public ChatGPT(	... )
		{
      ...
		}

		public PromptResponse oGetChatResponseSynchronous(	string strPrompt,
																												int nMaxHistoricTokens = 2048) 
		{
			...
    }
    
    ...

		List<PromptResponse> m_oHistory = new();
	}
}

```

The second component I needed was a way to compile the code that was returned from the LLM and check it for errors. I am using the Roslyn framework for this. It is suprisingly complex to get all the dependencies right, but [fortunately Rick Strahl has some excellent code online that helps a lot.](https://github.com/RickStrahl/Westwind.Scripting/tree/master)

I omitted a lot of code, but you can see that this class can compile C# code, and throws an exception with any compilation errors it encounters.

```C#
public class CodeCompiler
    {
        public CodeCompiler(    string strCode,
                                string strNameSpaceAndClass,
                                string strMethodToInvoke)
        {
            AddNetCoreDefaultReferences();
            
          	...
           
            SyntaxTree oTree = CSharpSyntaxTree.ParseText(strCode);

            string strAssemblyName = Path.GetRandomFileName();

            CSharpCompilation oCompilation = CSharpCompilation.Create( ... )
                
            using (MemoryStream oStream = new())
            {
                EmitResult oEmitResult = oCompilation.Emit(oStream);

                if (!oEmitResult.Success)
                {
                    string strError = "Code compilation failed with the following errors\n";
                    foreach (Diagnostic oDiag in oEmitResult.Diagnostics)
                    {
                        strError += $"- {oDiag}\n";
                    }

                    throw new ArgumentException(strError);
                }

                ...

                MethodInfo? oMethod = oType.GetMethod(strMethodToInvoke);

                ...

                m_oMethod = oMethod;
            }
        }
        
        public void Task()
        {
            m_oMethod.Invoke(null, null);
        }

         ....
    }
```

Now, the last thing we need is a lot of glue code and the logic that actually sends the prompt and re-prompts if things fail.

```C#
public class OpenAIStuff
	{
		public static CodeCompiler oCreateCompiledCode(	string strPrompt,
																										string strReferenceCode,
																										string strDocumentationFile)
    {
			using StreamWriter oWriter = new StreamWriter(strDocumentationFile, false);
			
      if (oWriter is null)
      	throw new FileNotFoundException("Unable to create file " + strDocumentationFile);

			ChatGPT oGPT = new(@"
You are a C# developer creating computational engineering models that build complex geometry using code.
You use PicoGK, an open source library for computational geometry.",
								strApiKey);

			strPrompt = "**" + strPrompt + "**";

			strPrompt += @"

Please include all the code, including the class definition and the static Task function.
The function needs to be called 'Task', so don't call it anything else.
Please call the class 'AIExample'.

Do not add placeholder code. It needs to be functional.

Here's an example code to base your code on:

";
			strReferenceCode = CodeUtils.strAddCodeFences(strReferenceCode);

			strPrompt += strReferenceCode;

			int nTimeOut = 10;
			int nLoop = 0;

			....

			while (true)
			{
				nLoop++;

				if (nLoop > nTimeOut)
				{
					oWriter.WriteLine($"\n\n## Giving up");
					...
				}

				..
          
				var oResponse = oGPT.oGetChatResponseSynchronous(strPrompt, 10000);

				...

				var astrCode = CodeUtils.astrExtractCodeBlocks(oResponse.strResponse);

				if (astrCode.Count == 0)
				{
          // Did not send us a code block
					strPrompt = "Please provide me with a code block in your response, fenced off by ``` characters.";
					continue; // try again
				}

				if (astrCode.Count > 1)
				{
          // Sent us more than one code block
					strPrompt = "Please provide me with just one code block in your response, fenced off by ``` characters.";
					continue; // try again
				}

        // Compile the code block
				try
				{
					CodeCompiler oCompiler = new(astrCode[0], "PicoGKExamples.AIExample", "Task");

					oWriter.WriteLine($"\n\n## Success");
					oWriter.WriteLine($"\n\n**Created compilable code in {nLoop} Attempts**");

					return oCompiler;
				}

				catch (Exception e)
				{
          // Problem with compiling, ask LLM to fix
					strPrompt = "The compiler reported the following errors in your code, could you rewrite?\n";
					strPrompt += e.Message;

          // "Remind" LLM of the original code example to steer it back on track
					if (nLoop % 3 == 0)
					{
						strPrompt += "\n\nPlease reference the sample code again:\n\n";
						strPrompt += strReferenceCode;
					}
					
					// try again
				}
			}
		}
	}
```

Now, this will require a bit of explanation. I am taking the original prompt, and do some prompt engineering to make sure that the LLM doesn't do something too creative. I am also including some reference code to base its new code on, since OpenAI's API knows nothing about PicoGK.

When the responds comes back, I do some sanity check, to make sure we have exactly one code block, if not I re-prompt to get to where I want it to be.

If code compilation fails, I send back again, until I receive something compilable.

Once it's compilable, I am done with this stage. Now, whether the result is correct is something different altogether...

## Experiments

OK, let's have some fun with our first prompts. Here is my `Program.cs`. It uses the Boolean Showcase example from PicoGK as reference source code file and asks the LLM to create some objects in a specific constellation. The catch is, I am asking it to create two spheres and a box, but there is no example for an implicit function for a box, so it needs to invent it.

```
using PicoGKLab;
using Leap71.AI;
using PicoGK;

try
{
    string strMDFile = Path.Combine(    Utils.strDocumentsFolder(),
                                        Utils.strDateTimeFilename("ChatGPTCoding", ".md"));

    string strCodeExample = File.ReadAllText(   Path.Combine(   Utils.strProjectRootFolder(),
                                                "PicoGK/Examples/Ex_BooleanShowCase.cs"));


    CodeCompiler oCompiledCode = OpenAIStuff.oCreateCompiledCode(
        "Please create three objects, whose center point is in a equidistant triangle 23mm from each other. A 48mm cube and a 45mm and a 29mm sphere. Create a boolean intersection between the three objects and show (only) the result.",
        strCodeExample,
        strMDFile);
        
    PicoGK.Library.Go(0.8f, oCompiledCode.Task);
}

catch (Exception e)
{
    Console.Write(e.ToString());
}
```

So the prompt is:

**Please create three objects, whose center point is in a equidistant triangle 23mm from each other. A 48mm cube and a 45mm and a 29mm sphere. Create a boolean intersection between the three objects and show (only) the result.**

This prompt is automatically extended by my code to be the following:

**Please create three objects, whose center point is in a equidistant triangle 23mm from each other. A 48mm cube and a 45mm and a 29mm sphere. Create a boolean intersection between the three objects and show (only) the result.**

Please include all the code, including the class definition and the static Task function.
The function needs to be called 'Task', so don't call it anything else.
Please call the class 'AIExample'.

Do not add placeholder code. It needs to be functional.

Here's an example code to base your code on:

```csharp
....

using PicoGK;
using System.Numerics;

namespace PicoGKExamples
{
  ... etc
```

So, it includes the original prompt, additional general prompts, and the example we want it to base the source code on.

So, let the fun begin. I received a few good results, very often on the first go. In other cases, the LLM started to hallucinate wildly, inventing new functions and claiming with conviction, these were all part of the PicoGK API (which it has no idea about).

One interesting aspect is that once off track, it is hard to get it back. This prompted me to "remind" the LLM of the original source code with every third prompt. Without this reminder, it started inventing completely new things all the time.

Another observation points to the general issue, that LLMs have no concept of logic. We use [Hungarian Notation](https://en.wikipedia.org/wiki/Hungarian_notation) in PicoGK, to indicate the type of functions and variables. This is a bit out-of-fashion these days (but still as useful as in the 1990s). So, often the LLM generates function names that are wrong. The interface `IImplicit` requires you to implement a functions called `fSignedDistance` and if you don't the code doesn't compile. The LLM response often called the function `SignedDistance` instead, which caused compilation errors. No human programmer would ever make this mistake — but an LLM really has no clue what is doing logically, it just tries to predict the next token in a long string of context. 

It will be interesting to see how a two-sided artificial engineering brain which uses an LLM on one side to "predict code", and a Computational Model which forms the other side to make sure the logic is right, will have to be designed in practice.

Training the LLM directly on the Engineering Model will help rid it from much hallucinative dream potential. But it's also clear that we need strong logical safeguards against code that was generated — with conviction — and may lead to completely nonsensical machinery if executed.

Definitely a few fun tests. Here are some examples for your perusal:

- A frustrating example, where the LLM wasn't able to fix even very basic errors [ChatGPTCoding20240329_202128.md](ChatGPTCoding20240329_202128.md)
- Failure to understand the importance of exact names, but other than that, kudos for creating a new signed distance function for a cube: [ChatGPTCoding20240329_201957.md](ChatGPTCoding20240329_201957.md)
- And another example [ChatGPTCoding20240328_233907.md](ChatGPTCoding20240328_233907.md)

## Thoughts

This is a very initial example showcasing what is generally possible without much effort. The results are promising, but also show clearly that there is a fundamental "fuzziness" to the way LLMs work, which may not be problematic for language, but are definitely a problem for code that needs precise syntax and which requires clear logic to achieve the desired results.

The findings support the opinion I expressed in my articles on this subject. It will be fun to explore this further.

------

**[PicoGK.org](https://picogk.org)/[Labs](https://picogk.org/labs)**

------

*© 2024 by LEAP 71 — All Rights Reserved*

