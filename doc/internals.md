# Behind the scenes

If you are interested in the internal workings of PicoGK, here is some basic information.

## On the shoulders of giants

We are indebted to other Open-Source projects, without which writing PicoGK would have been an impossible task. Most notably we use the Academy Software Foundation's [OpenVDB library](https://www.openvdb.org/), which provides the majority of the functionality neccessary to build a robust geometry kernel. In many ways, PicoGK is a thin-yet-powerful layer of functionality on top of OpenVDB.

The PicoGK viewer relies on the [GLFW library](https://www.glfw.org/), for simple cross-platform OpenGL user interface functionality.

## PicoGK structure

PicoGK consists of two modules. 

- **PicoGK**, which is the C#-based project that you have currently open, and

- **PicoGKRuntime**, a C++-based runtime library, which is in a [separate GitHub project](https://github.com/leap71/PicoGKRuntime).

**PicoGK** provides an framework based on [Microsoft's open-source C# language](https://dotnet.microsoft.com/en-us/languages/csharp). It depends on PicoGKRuntime for the heavy lifting.

**[PicoGKRuntime](https://github.com/leap71/PicoGKRuntime)** exposes a minimal "extern C" interface, which can be implemented by other geometry kernels, including commercial ones. 

We encourage other software vendors to support the PicoGKRuntime API and release commercial alternatives to our open-source project. PicoGK was developed as a reliable common interface that you, the engineer, can always rely on as a free and universally available platform to fall back on when creating your own intellectual property.

**[PicoGK](https://github.com/leap71/PicoGK)**, the C# code, [usually provided as a Nuget package](https://www.nuget.org/packages/PicoGK), provides rich functionality based on the PicoGKRuntime interface. It makes it as simple as possible to build computational geometry.

## Compiling the PicoGK runtime

