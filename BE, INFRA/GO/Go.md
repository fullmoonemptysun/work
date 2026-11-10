![[Pasted image 20261103175316.png]]



A program in Go
```go
package main

import "fmt"

func main(){
	fmt.Println("Hello world")
}


//variable declarations (the sad way)
var varname int
varname = 2

//variable declaration, type inference and definition on the same line (the GOATed way)
varname := 2
myString := "HEHE"
aFloat := 3.141
aByte := 0xHE


//with := we can declare several variables on the same line (python style)
myString, myInt, myFloat := "Hehe", 3, 13.0


//constant variables like this
//They _cannot_ be more complex types like slices, maps and structs. Also they cannot use := declaration
const myStr = "hehe"

```

Some of Go's most common variable [types](https://go.dev/ref/spec#Types) are:

- `int`: a signed integer
- `bool`: a boolean value, either `true` or `false`
- `string`: a sequence of characters
- `float64`: a floating-point number
- `byte`: exactly what it sounds like: 8 bits of data

And to use them, their values are set like this:

```go
var health int
health = 100

var isAwesome bool
isAwesome = true

var greeting string
greeting = "Hello, world!"

var pi float64
pi = 3.14159

var data byte
data = 0xFF
```

- Go compiled code does not run faster than its C/C++ or Rust counterparts, however it compiles faster than them. And is faster than many other languages like Python, Ruby etc.

Integers, [uints](https://www.cs.utah.edu/~germain/PPS/Topics/unsigned_integer.html#:~:text=Unsigned%20Integers,negative%20\(zero%20or%20positive\).), [floats](https://techterms.com/definition/floatingpoint), and [complex](https://byjus.com/maths/complex-numbers/) numbers all have type sizes.

- **Signed integers** (no decimal)
```go
int  int8  int16  int32  int64
```

- **Unsigned integers** (non-negative numbers/no decimal)
```go
uint uint8 uint16 uint32 uint64 uintptr
```

- **Signed decimal numbers**
```go
float32 float64
```

- **Complex numbers** (a complex number has a real and imaginary part)
```go
complex64 complex128
```

```go
temperatureFloat := 88.26
temperatureInt := int(temperatureFloat)
```

Casting a float to an integer in this way [truncates](https://techterms.com/definition/truncate) the floating point portion.

## On where Go stands in the kingdom of languages

![[Pasted image 20261104190328.png]]
Go is a natively compiled language for sure, but it doesnt exactly sit with C or C++. It has a runtime, linked into the executable by the linker. This runtime is linked like the CRT (C runtime) however is more similar to the JVM runtime in terms of functionality. This runtime provides Go with some features similar to java, like automatic garbage collection (mark and sweep) and some other features related to Goroutines  and parallel programming. This means that it will not run as fast as Programs written in C/C++ and will be closer in speed with languages like Java. However the memory overhead will be less than java by far since it is still executing native machine code and not bytecode through JVM. 


## Const Variables
Constants must be known at compile time. They are _usually_ declared with a static value:

```go
const myInt = 15
```

However, constants _can be computed_ as long as the computation can happen at _compile time_.

For example, this is valid:

```go
const firstName = "Lane"
const lastName = "Wagner"
const fullName = firstName + " " + lastName
```

That said, you _cannot_ declare a constant that can only be computed at run-time like you can in JavaScript. This breaks:

```go
// the current time can only be known when the program is running
const currentTime = time.Now()
```

## String formatting
Go follows the [printf tradition](https://cplusplus.com/reference/cstdio/printf/) from the C language. In my opinion, string formatting/interpolation in Go is _less_ elegant than Python's f-strings, unfortunately.

- [fmt.Printf()](https://pkg.go.dev/fmt#Printf) - Prints a formatted string to [standard out](https://stackoverflow.com/questions/3385201/confused-about-stdin-stdout-and-stderr).
    
- [fmt.Sprintf()](https://pkg.go.dev/fmt#Sprintf) - Returns the formatted string
    

These following "formatting verbs" work with the formatting functions above:

## Default Representation

The `%v` variant prints any value in a default format. It can be used as a catchall.

```go
s := fmt.Sprintf("I am %v years old", 10)
// I am 10 years old

s := fmt.Sprintf("I am %v years old", "way too many")
// I am way too many years old
```

If you want to print in a more specific way, you can use the following formatting verbs:

## String

```go
s := fmt.Sprintf("I am %s years old", "way too many")
// I am way too many years old
```

## Integer

```go
s := fmt.Sprintf("I am %d years old", 10)
// I am 10 years old
```

## Float

```go
s := fmt.Sprintf("I am %f years old", 10.523)
// I am 10.523000 years old

// The ".2" rounds the number to 2 decimal places
s := fmt.Sprintf("I am %.2f years old", 10.523)
// I am 10.52 years old
```

`fmt` package's [docs](https://pkg.go.dev/fmt#hdr-Printing).

|Verb|Formats|
|---|---|
|`%v`|any value in its default format|
|`%s`|a string|
|`%d`|an integer in base 10|
|`%f`|a float, 6 decimal places by default|
|`%.2f`|a float rounded to 2 decimal places|
|`%t`|a boolean|
|`%q`|a string in double quotes|
|`%T`|the type of the value|
|`%%`|a literal percent sign|


## Runes and String encoding
In many programming languages (cough, C, cough), a "character" is a single byte. Using [ASCII](https://www.asciitable.com/) encoding, the standard for the C programming language, we can represent 128 characters with 7 bits. This is enough for the English alphabet, numbers, and some special characters.

In Go, strings are just sequences of bytes: they can hold arbitrary data. However, Go also has a special type, [`rune`](https://go.dev/blog/strings), which is an alias for `int32`. This means that a `rune` is a 32-bit integer, which is large enough to hold any [Unicode](https://home.unicode.org/) code point.

When you're working with strings, you need to be aware of the encoding (bytes -> representation). Go uses [UTF-8](https://en.wikipedia.org/wiki/UTF-8) encoding, which is a variable-length encoding.

### UTF-8 Text

Type text to see its code points and bytes. Joining adjacent emoji inserts a [zero width joiner](https://en.wikipedia.org/wiki/Zero-width_joiner) (U+200D).

There are 2 main takeaways:

1. When you need to work with individual characters in a string, you should use the `rune` type. It breaks strings up into their individual characters, which can be more than one byte long.
2. We can include a wide variety of Unicode characters in our strings, such as emojis and Chinese characters, and Go will handle them just fine.



