1.  A program that can be easily transferred from one platform to another is said to be **portable**. The act of modifying a program so that it runs on a different platform is called **porting**.

2. C/C++ is "Trust The programmer"


## Compilation, object file, linking and libraries

1. After successful parsing and syntax/semantics checking, The **Compiler** starts converting code into machine instructions. 
2. These instructions are stored in an intermediate file called an ***Object File***.
3. This Object file contains other data that is required/useful for upcoming steps (linking and debugging).
4. After all `.cpp` files have been compiled, a **Linker** program kicks in. 
5. The Linker's job is to combine all of the object files and produce the desired output executable file. 

6. Linking steps:
	1. Read each object file and ensure it is valid
	2. Ensures all cross file dependencies are resolved. (Ex: If a file defines something and this something is used somewhere else, linker connects the two together).
	3. Linker also links one or more **Library** files (Pre-written code).
	4. Gives the final file (Could be an executable or a library, etc.).

### The Standard Library
1. C++ comes with it.
2. Is actually a collection of smaller libraries for many capabilities (Such as `iostream` for I/O, etc.)

### Building
1. All the above steps combined into giving an executable is called building.
2. For more complex projects, build tools(**make, build2**) are used to automate all this and some testing as well. 
> [!NOTE] Cache saves of object file
> When a code file is compiled, your IDE may cache (save) the resulting object file on disk. A **cache** (pronounced like “cash”) is storage location where frequently accessed data is stored for fast retrieval later. That way, if the program is compiled again in the future, any code file that hasn’t been modified doesn’t need to be recompiled -- the cached object file from last time can be reused

3. . Some Terms: 
	- **Build** compiles all _modified_ code files in the project or workspace/solution, and then links the object files into an executable. If no code files have been modified since the last build, this option does nothing.
	- **Clean** removes all cached objects and executables so the next time the project is built, all files will be recompiled and a new executable produced.
	- **Rebuild** does a “clean”, followed by a “build”.
	- **Compile** recompiles a single code file (regardless of whether it has been cached previously). This option does not invoke the linker or produce an executable.
	- **Run/start** executes the executable from a prior build. Some IDEs (e.g. Visual Studio) will invoke a “build” before doing a “run” to ensure you are running the latest version of your code. Otherwise (e.g. Code::Blocks) will just execute the prior executable.


## IDEs
1. For dev we need IDE. 
2. On VSCODE.

## Configuring The Compiler: Build Configurations
1. **Build Configuration or Build Target** is a collection of project settings that determines how the IDE will build the project like:
	1. what the executable will be named
	2. what directories the IDE will look in for the code and lib files
	3. whether to keep or strip out debugging information
	4. how much will the compiler optimize the program
	5. et cetera.

2. The **debug configuration** is designed to help you debug your program, and is generally the one you will use when writing your programs. This configuration turns off all optimizations, and includes debugging information, which makes your programs larger and slower, but much easier to debug. The debug configuration is usually selected as the active configuration by default. We’ll talk more about debugging techniques in a later lesson.

3. The **release configuration** is designed to be used when releasing your program to the public. This version is typically optimized for size and performance, and doesn’t contain the extra debugging information. Because the release configuration includes all optimizations, this mode is also useful for testing the performance of your code (which we’ll show you how to do later in the tutorial series).



## Configuring the Compiler: Compiler Extensions
1. Many times, some compilers will provide some extra features, optimizations, etc. That is not compliant with the C++ standard. These are called **compiler extensions.**
2. They are often enabled by default and should not be used unless required. (They are never neccessary).
3. Features defined by one compiler extension most likely ***will not*** be compatible with another compiler.

> [!Important] Best Practice!
> Remove/disable all compiler extensions.
> On GCC/Clang This is with argument :
> ```
> -pedantic-errors
> ```
> 	On VScode, edit the `task.json` file to include this option.
> 


## Configuring the compiler: Warning and Error Levels
>[!NOTE] Key Insight
>
>
>Compilers determine whether a **non-blocking** **diagnostic** issue is a warning or an error. While they usually align in their categorization, in some cases, compilers may not agree -- with one compiler emitting an error and another compiler emitting a warning for the same issue.
>
>
>

>[!IMPORTANT] Best Practice!
>Don’t let warnings pile up. Resolve them as you encounter them (as if they were errors). Otherwise a warning about a serious issue may be lost amongst warnings about non-serious issues

1. Level of warning can be increased to tell compiler to be more assertive and point it out the less obvious problems that may seem unimportant at one point

>[!IMPORTANT] Best Practice related to warnings, Treat them as errors!
>On VScode/Gcc:
>```"-Wall",
"-Weffc++",
"-Wextra",
"-Wconversion",
"-Wsign-conversion",
>```
>This enables many types of warnings that are disabled otherwise. 
>
>Also,
>`-Werror` : Treat all warnings as errors (Halt compilation).

2. Generally, compilers will default to one standard version of c++. **This may NOT be the best/latest version**








