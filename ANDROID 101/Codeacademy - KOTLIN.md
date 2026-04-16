
To manually compile a kotlin program:
In the bash terminal, we can enter the first command to compile our code and save it in a `.jar` file:

```
$ kotlinc HelloWorld.kt -include-runtime -d HelloWorld.jar
```

- `kotlinc` represents the compiler.
- `HelloWorld.kt` represents the source code file that we need to compile into Java bytecode.
- The `-include-runtime` option results in the `.jar` file being runnable by including the Kotlin runtime library within it.
- The `.jar` extension represents a file format that indicates many Java files zipped into one. When executed, Java can extract and use its information.
- The `-d` flag specifies the output path for the newly generated class files.

After we’ve hit enter, we can then run our program with the following command:

```
java -jar HelloWorld.jar
```

- The `java` command launches an application written in Java. It does this by starting the JVM, loading the `.class` file, and executing the code within the `main()` function.
- By specifying the `-jar` flag, we are instructing Java to execute a program within a `.jar` file.


# Variable Declarations
`var varName:type = value` normal variable declaration
`val varName:type = value` const variable declaration (immutable).

1. Semicolons are not important.
2. There is type inference. So type declarations are not important.

# String formatting (embedded variables)
`var varName:String = "world"`
`var varName:String = "Hello $varName"`

# User input
Take one line of input:
`var myName = readLine()`

# Some Builtin Properties/Functions
We will look mostly at properties/functions for the String type.
1. `.length` property returns the length parameter
2. .capitalize() returns a string with first letter capital

# Using Number variables
Most number types in kotlin inherit from the `Number` abstract class.

Number sizes in kotlin:

| **Name** | **Bit**(Size) |
| -------- | ------------- |
| Long     | 64            |
| Int      | 32            |
| Short    | 16            |
| Byte     | 8             |
| Double   | 64            |
| Float    | 32            |

There is a `Math.` library that can be used for many math functions.

' ' and " " are treated differently one is a char the other is a string.





