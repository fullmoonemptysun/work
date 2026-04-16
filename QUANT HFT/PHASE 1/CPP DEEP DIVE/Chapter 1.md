# Structure of a program:
1. Types of statements in C++:
	- Declaration statements
	- Jump statements
	- Expression statements
	- Compound statements
	- Selection statements (conditionals)
	- Iteration statements (loops)
	- Try blocks

> [!ERROR] RULE
> Every C++ program must have a special function named **main** (all lower case letters).

2. Computers have an additional type of character, called a “control character”. These are characters that have special meaning to the computer system, but either aren’t intended to be displayed, or display as something other than a single visible symbol. Examples of well-known control characters include “escape” (which doesn’t display anything), “tab” (which displays as some number of spaces), and “backspace” (which erases the previous character).

>[!NOTE] Document Generation
>Documentation generation programs such as [Doxygen](https://www.doxygen.nl/) are designed to help generate and leverage documentation in various ways. Amongst other things, they can:
>- Help standardize the way your code is documented.
>- Generate diagrams, visualizations, and cross-links to make understanding the code structure easier.
>- Export your documentation to other formats (e.g. HTML) so it can be easily shared with others (e.g. other team members, or developers who are integrating whatever you are writing).
>
>You won’t need these while learning the language, but you may encounter them or find them useful in the future, especially in professional environments


# Intro to C++ objects and variables
1. In c++ An **object** represents a region of storage (typically RAM or a CPU register) that can hold a value. (not always object oriented programming related).
2. Key point here is that rather than say “go get the value stored in mailbox number 7532”, we can say, “go get the value stored by this object”

>[!NOTE] Key Insight
>An object is used to store a value in memory. A variable is an object that has a name (identifier).
>Naming our objects let us refer to those objects again later in the program.

3. This object does not include stored functions in c++.
>[!NOTE] Key insight
>The data type of an **object** must be known at compile-time (so the compiler knows how much memory that object requires).

**Multiple variable declarations**
```cpp
int a, int b; // wrong (compiler error)

int a, b; // correct


int a, double b; // wrong (compiler error)

int a; double b; // correct (but not recommended)

// correct and recommended (easier to read)
int a;
double b;

```

## Types of variable initialization in C++:
```cpp
int a;         // default-initialization (no initializer)

// Traditional initialization forms:
int b = 5;     // copy-initialization (initial value after equals sign)
int c ( 6 );   // direct-initialization (initial value in parenthesis)

// Modern initialization forms (preferred):
int d { 7 };   // direct-list-initialization (initial value in braces)
int e {};      // value-initialization (empty braces)
```

Section 14 covers the differences between these.

The 1st three are mostly same however one relevant difference is below:

### Default init
- In most cases, leaves the variable with unpredicted value, also known as *garbage value*.
- Will be discussed more later

### Copy init
- This was inherited from C 
- Copy-initialization had fallen out of favor in modern C++ due to being less efficient than other forms of initialization for some complex types. However, C++17 remedied the bulk of these issues, and copy-initialization is now finding new advocates. You will also find it used in older code (especially code ported from C), or by developers who simply think it looks more natural and is easier to read.

>[!NOTE]
>Copy-initialization is also used whenever values are implicitly copied, such as when passing arguments to a function by value, returning from a function by value, or catching exceptions by value.


### Direct init
Direct-initialization was initially introduced to allow for more efficient initialization of complex objects (those with class types, which we’ll cover in a future chapter). Just like copy-initialization, direct-initialization had fallen out of favor in modern C++, largely due to being superseded by direct-list-initialization. However, direct-list-initialization has a few quirks of its own, and so direct-initialization is once again finding use in certain cases.

>[!IMPORTANT] Initializations
>Prior to C++11, some types of initialization required using copy-initialization, and other types of initialization required using direct-initialization. Copy-initialization can be hard to differentiate from copy-assignment (because both use an `=`). And direct-initialization can be difficult to differentiate from function-related operations (because both use parentheses).
>List-initialization was introduced to provide a initialization syntax that works in almost all cases, behaves consistently, and has an unambiguous syntax that makes it easy to tell where we’re initializing an object.


### Direct List init

1. One benefit of direct list init is that it does not allow for narrowing conversions:

```cpp
int main()
{
    // An integer can only hold non-fractional values.
    // Initializing an int with fractional value 4.5 requires the compiler to convert 4.5 to a value an int can hold.
    // Such a conversion is a narrowing conversion, since the fractional part of the value will be lost.

    int w1 { 4.5 }; // compile error: list-init does not allow narrowing conversion

    int w2 = 4.5;   // compiles: w2 copy-initialized to value 4
    int w3 (4.5);   // compiles: w3 direct-initialized to value 4

    return 0;
}
```

above only applies to the initialization step.

we can do the following later and it will compiler:
`w1 = 4`.5;

This direct list is only an initialization method. Future assignments to this variable are done normally using `=` assignment operator.



### Value init/Zero init
1. Assigns the variable with value of 0 or whatever is closest to 0 for the respective type.
2. For **Class** types, this default value can be changed and can be anything.



- Ideally, we should try to use list init for everything. But it has some quirks that we will see future and so a mix of all these should be used once understood the exact nuances between them.

>[!IMPORTANT] Initialize variables 
>It is recommended that except required otherwise, we should always initialize a variable when we declare it.
>

- int a {0} vs. int a{}: when to use each:
	- When we need to use the value of a (0) we do the first. If we don't care about the value and only need a variable that we are going to put some value in then second.


## Multiple variable initialization (Not recommended)

```cpp
int a = 5, b = 6;          // copy-initialization
int c ( 7 ), d ( 8 );      // direct-initialization
int e { 9 }, f { 10 };     // direct-list-initialization
int i {}, j {};            // value-initialization
```


## What happens to a variable during compile time vs during runtime

At **compile-time** (when the program is being compiled), when encountering this statement, the compiler makes a note to itself that we want a variable with the name `x`, and that the variable has the data type `int` (more on data types in a moment). From that point forward (with some limitations that we’ll talk about in a future lesson), whenever we use the identifier `x` in our code, the compiler will know that we are referring to this variable


At **runtime** (when the program is loaded into memory and run), each object is given an actual storage location (such as RAM, or a CPU register) that it can use to store values. The process of reserving storage for an object’s use is called **allocation**. Once allocation has occurred, the object has been created and can be used.


## Unused initialized variables
1. Compilers will mostly give these as warnings (if Werror is on then this becomes an error).

2. Fix: either use the variable or just remove it/comment out

   But when neither is an option, say a list of math constants imported from a file, but not all being used then
3. We can use <code><strong>[[maybe_unused]]</strong></code> attribute before each of these variables (defined in C++17) like:
	```cpp
int main(){
	[[maybe_unused]] double pi {3.14};
	[[maybe_unused]] double g {9.8};
}
	
```

4. Tells the compiler not to complain if initialized variable is not used.
5. The compiler might also optimize these values out of the program so they'll have no performance impact.


# 1.5: Introduction to iostream: cout, cin and endl

1. The `cout` in `std::cout` is actually a variable.
2. `<<` is the *insertion* operator.
>[!IMPORTANT] Best practice
>Output a newline whenever a line of output is complete.



3. All data sent to `std::cout` is buffered, i.e, is not immediately sent to the console but is temporarily stored in a small region set aside in memory to collect such requests. 
4. Periodically, this buffer is **flushed** which means that all the data inside is transferred to its destination (in this case, the console.)
5. This has a ***Time critical meaning:*** if program crashes/aborts or is paused (for debugging?) before the flush happens, the data in there will not be displayed.

6. So why this buffer approach? why not straight up to `stdout` like C does?:
	  Writing data to a buffer is typically fast, whereas transferring a batch of data to an output device is comparatively slow. Buffering can significantly increase performance by batching multiple output requests together to minimize the number of times output has to be sent to the output device.

7. **\n vs. endl**:
	- `endl` does 2 things: it adds a newline to the output and flushes the buffer, which makes it slow. C++ output system periodically flushes the buffer anyway and this is more efficient too.
	- `\n` is just a newline character. 
	- When `\n` is outputted, the library outputting (iostream here) is responsible for translating this character into the newline sequence for the given OS.

8. `>>` is the *extraction operator*  used with `std::cin` to input data in a variable.
9. Interesting way of inputting to 2 vars at one time:
	```cpp
#include <iostream>

int main(){

	std::cout << "Enter 2 nums separated by a space";

  

	int x{};
	int y{};

	std::cin>>x>>y; // save the 1st num in x and the second in y??

	std::cout<<"The 2 nums are: " << x << " and " << y << '\n';

	return 0;


}
```

10. Just like output, input is also buffered.
11. This buffer lives inside `std::cin`. The enter key is also stored as a \n character.
12. `>>` removes characters from the front of this buffer and converts them in to a value that is  assigned to the associated variable. 

>[!Key Insight]
>`std::cin` is buffered because it allows us to separate the entering of input from the extract of input. We can enter input once and then perform multiple extraction requests on it.

## The basic extraction process
Here’s a simplified view of how operator `>>` works for input.

1. If `std::cin` is not in a good state (e.g. the prior extraction failed and `std::cin` has not yet been cleared), no extraction is attempted, and the extraction process aborts immediately.
2. Leading whitespace characters (spaces, tabs, and newlines at the front of the buffer) are discarded from the input buffer. This will discard an unextracted newline character remaining from a prior line of input.
3. If the input buffer is now empty, operator `>>` will wait for the user to enter more data. Any leading whitespace is discarded from the entered data.
4. operator `>>` then extracts as many consecutive characters as it can, until it encounters either a newline character (representing the end of the line of input) or a character that is not valid for the variable being extracted to.

The result of the extraction process is as follows:
- If the extraction aborted in step 1, then no extraction attempt occurred. Nothing else happens.
- If any characters were extracted in step 4 above, extraction is a success. The extracted characters are converted into a value that is then copy-assigned to the variable.
- If no characters could be extracted in step 4 above, extraction has failed. The object being extracted to is copy-assigned the value `0` (as of C++11), and any future extractions will immediately fail (until `std::cin` is cleared). (Inputting "hello when an int object is being expected")


# 1.6: Uninitialized variables and Undefined Behavior
1. If a variable is uninitialized, its value is whatever (garbage) value happens to be in the memory address for the variable.
2. Default init or basically just declaration should only be done selectively and intentionally for optimizations.

3. Undefined behavior or **UB** is the result of executing code whose behavior is not well-defined by the C++ language. In this case C++ doesn't have any rules determining what should happen.

4. Symptoms that there might be some **UB**:
	 - Your program produces different results every time it is run.
	- Your program consistently produces the same incorrect result.
	- Your program behaves inconsistently (sometimes produces the correct result, sometimes not).
	- Your program seems like it’s working but produces incorrect results later in the program.
	- Your program crashes, either immediately or later.
	- Your program works on some compilers but not others.
	- Your program works until you change some other seemingly unrelated code.

>[!ERROR] Eliminating Undefined behaviors
>Take care to avoid all situations that result in undefined behavior, such as using uninitialized variables.

5. Some compilers plus the stdlibs that come with them define how some aspect of the language will behave. This is ***implementation defined behavior***. C++ standard allows this in some cases so that the compiler may choose a behavior that is efficient for the platform.
6. ***Unspecified behavior*** is very much like implementation defined behavior except there isn't documentation for it and so is unspecified.

>[!IMPORTANT] Best practice
>Avoid implementation-defined and unspecified behavior whenever possible, as they may cause your program to malfunction on other implementations.


# 1.7: Keywords and naming identifiers
1. 92 keywords (C++23)
2. Identifier names that start with a capital letter are typically used for user-defined types (such as structs, classes, and enumerations, all of which we will cover later).
3. Try to avoid names that start with \_ . These names should be reserved for OS, compiler or library use.

>[!IMPORTANT] Best Practice
>When working in an existing program, use the conventions of that program (even if they don’t conform to modern best practices). Use modern best practices when you’re writing new programs.

# 1.8: Whitespace and basic formatting
1. **Whitespace** refers to characters used for formatting purposes. In C++, this is spaces, tabs, and newlines.
2. Preprocessor directives must be separated by newlines.
3. Quoted text separated by whitespaces will automatically be concatenated:
```cpp
std::cout << "Hello "
     "world!"; // prints "Hello world!"
```

4. Newlines are not allowed inside quoted text.
5. Statements may be split onto multiple lines.
6. Lines should be split after 80 chars (not required).

# 1.9 Intro to literals and operators
1. A **literal/literal constant** is a fixed value that has been inserted directly into source code.
2. Value of a literal is fixed and cannot be changed.

```cpp
#include <iostream>

int main()
{
    std::cout << 5 << '\n'; // print the value of a literal

    int x { 5 };
    std::cout << x << '\n'; // print the value of a variable
    return 0;
}
```
The difference between the two codes above is:
- 1st: We're printing the value 5 to console. When the compiler compiles this, the value 5 is compiled into the executable and can be used directly.
- 2nd: We create a variable and initializing it with value 5. The compiler will generate code that copies the literal value `5` into this variable's memory. Then when we print `x`, The compiler generates code to print the value in the memory location of x (which happens to have value 5).

>[!NOTE] Key insight
>Literals are values that are inserted directly into the source code. These values usually appear directly in the executable code (unless they are optimized out).
Objects and variables represent memory locations that hold values. These values can be fetched on demand.

3. Its common to see operators that are just one symbol, (+,-,/,) it is common to denote them as `operator+, operator-, etc.` **why?: Future lessons**.
4. `new, delete, throw, etc.` are operators that are not symbols but keywords.

5. There are only 1 **ternary**(conditional) and 1(throw) **nullary** operator in c++
6. In C++, the term “side effect” has a different meaning: it is an observable effect of an operator or function beyond producing a return value.

7. Operators that seem to not have a return value such as = and std::cout actually return the left operand. This is designed this way so they can be chained.

8. Non empty sequence of literals, variables, operators and function calls which calculates a value is called ***expression*** its ***execution*** is called *evaluation* and the resulting value is the ***result***

9. An expression's results in : a value | an object or a funtion | Nothing. (void)

10. Statements are used when we want the program to perform an action. Expressions are used when we want the program to calculate a value.



