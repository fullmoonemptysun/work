>`cin.eof()` is a function that checks if the end of file at stdin has been reached
# Intro to functions
1. Basic function syntax:
   ```cpp
returnType functionName() // This is the function header (tells the compiler about the existence of the function)
{
    // This is the function body (tells the compiler what the function does)
}
```

>[!ERROR] NESTED FUNCTIONS:
> DO NOT EXIST IN C++ THEY ARE ILLEGAL.

# Function return values
1. When a function returns an expression, The return expression produces the value to be returned. The return value is a copy of that value. (RETURN BY VALUE).
2. `main` must return an int in c++
3. `main()` should not be called explicitly.
4. C allows the above and so some C compatible compilers will allow it too (gcc, looking at you)

>[!IMPORTANT] Is main REALLY the first thing that happens?
>no. there are other things that happen before.
>It is a common misconception that `main` is always the first function that executes.
>Global variables are initialized prior to the execution of `main`. If the initializer for such a variable invokes a function, then that function will execute prior to `main`. We discuss global variables in lesson [7.4 -- Introduction to global variables](https://www.learncpp.com/cpp-tutorial/introduction-to-global-variables/)

>[!IMPORTANT] Exit codes
>The C++ standard only defines the meaning of 3 status codes: `0`, `EXIT_SUCCESS`, and `EXIT_FAILURE`. `0` and `EXIT_SUCCESS` both mean the program executed successfully. `EXIT_FAILURE` means the program did not execute successfully.
>`EXIT_SUCCESS` and `EXIT_FAILURE` are preprocessor macros defined in the 
>```cpp
#include \<cstdlib> // for EXIT_SUCCESS and EXIT_FAILURE
int main()
>{
 >   return EXIT_SUCCESS;
>}
>```
>If you want to maximize portability, you should only use `0` or `EXIT_SUCCESS` to indicate a successful termination, or `EXIT_FAILURE` to indicate an unsuccessful termination.

