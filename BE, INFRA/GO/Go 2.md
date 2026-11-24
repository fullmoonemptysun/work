## Code with conditionals and other stuff
```go


//conditionals
if height > 6 {
    fmt.Println("You are super tall!")
} else if height > 4 {
    fmt.Println("You are tall enough!")
} else {
    fmt.Println("You are not tall enough!")
}

//this is not legal in Go
else
{
}

// if conditional with initial statement
//For example, instead of writing:
length := getLength(email)
if length < 10 {
    fmt.Printf("Email must be at least 10 characters, is %d\n", length)
}

//We can do:
if length := getLength(email); length < 10 {
    fmt.Printf("Email must be at least 10 characters, is %d\n", length)
}



/


```


An `if` conditional can have an "initial" statement. The variable(s) created in the initial statement are _only_ defined within the scope of the `if`, `else if`, and `else` blocks.

```go
if INITIAL_STATEMENT; CONDITION {
}
```
why? to keep code cleaner and limit scope. 



## Switch Case
```go
func getCreator(os string) string {
    var creator string
    switch os {
    case "linux":
        creator = "Linus Torvalds"
    case "windows":
        creator = "Bill Gates"
    case "mac":
        creator = "A Steve"
    default:
        creator = "Unknown"
    }
    return creator
}
```

no `break` needed in each case block to avoid fallthrough (it is implicit in Go). But if fallthrough is what we want then:
```go
func getCreator(os string) string {
    var creator string
    switch os {
    case "linux":
        creator = "Linus Torvalds"
    case "windows":
        creator = "Bill Gates"

    // all three of these cases will set creator to "A Steve"
    case "macOS":
        fallthrough
    case "Mac OS X":
        fallthrough
    case "mac":
        creator = "A Steve"

    default:
        creator = "Unknown"
    }
    return creator
}
```

## Functions
- can take 0 or more arguments
A function declaration in Go:
```go
func sub(x int, y int) int {
    return x-y
}
```

When multiple arguments are of the same type, and are next to each other in the function signature, the type only needs to be declared after the last argument.

Here are some examples:

```go
func addToDatabase(hp, damage int) {
  // ...
}

func addToDatabase(hp, damage int, name string) {
  // ?
}

func addToDatabase(hp, damage int, name string, level int) {
  // ?
}
```


## Multiple values return and ignoring them

A function can return a value that the caller doesn't care about. We can explicitly ignore variables by using an underscore, or more precisely, the [blank identifier `_`](https://go.dev/doc/effective_go#blank).

For example:

```go
func getPoint() (x int, y int) {
    return 3, 4
}

// ignore y value
x, _ := getPoint()
```

Even though `getPoint()` returns two values, we can capture the first one and ignore the second. In Go, the blank identifier isn't just a convention; it's a real language feature that completely discards the value.

## Why Might You Ignore a Return Value?

Maybe a function called `getCircle` returns the center point and the radius, but you only need the radius for your calculation. In that case, you can ignore the center point variable.

The Go compiler will **return an error** if you have any unused variable declarations in your code, so you _need_ to ignore anything you don't intend to use.


## Named Return Values

Return values may be given names, and if they are, then they are treated the same as if they were new variables defined at the top of the function.

Named return values are best thought of as a way to document the purpose of the returned values.

According to the [tour of go](https://tour.golang.org/):

> A return statement without arguments returns the named return values. This is known as a "naked" return. Naked return statements should be used only in short functions. They can harm readability in longer functions.

Named return values are what enable naked returns. Use naked returns only in short functions where the purpose of the returned values is obvious.

```go
func getCoords() (x, y int) {
	// x and y are initialized with zero values

	return // automatically returns x and y
}
```

Is the same as:

```go
func getCoords() (int, int) {
	var x int
	var y int
	return x, y
}
```

In the first example, `x` and `y` are the return values. At the end of the function, we could simply write `return` to return the values of those two variables, rather than writing `return x,y`.


Even though a function has named return values, we can still explicitly return values if we want to.

```go
func getCoords() (x, y int) {
	return x, y // this is explicit
}
```

Using this explicit pattern we can even overwrite the return values:

```go
func getCoords() (x, y int) {
    return 5, 6 // this is explicit, x and y are NOT returned
}
```

Otherwise, if we want to return the values defined in the function signature we can just use a naked `return` (blank return):

```go
func getCoords() (x, y int) {
    return // implicitly returns x and y
}
```


## Lambda expressions, higher order functions, first class functions in Go

Go supports [first-class](https://developer.mozilla.org/en-US/docs/Glossary/First-class_Function) and higher-order functions, which are just fancy ways of saying "functions as values". Functions are just another type -- like `int`s and `string`s and `bool`s.

Let's assume we have two simple functions:

```go
func add(x, y int) int {
	return x + y
}

func mul(x, y int) int {
	return x * y
}
```

We can create a new `aggregate` function that accepts a function as its 4th argument:

```go
func aggregate(a, b, c int, arithmetic func(int, int) int) int {
  firstResult := arithmetic(a, b)
  secondResult := arithmetic(firstResult, c)
  return secondResult
}
```

It calls the given `arithmetic` function (which could be `add` or `mul`, or any other function that accepts two `int`s and returns an `int`) and applies it to three inputs instead of two. It can be used like this:

```go
func main() {
	sum := aggregate(2, 3, 4, add)
	// sum is 9
	product := aggregate(2, 3, 4, mul)
	// product is 24
}
```


## Anonymous functions (like js)
Anonymous functions are true to form in that they have _no name_. They're useful when defining a function that will only be used once or to create a quick [closure](https://en.wikipedia.org/wiki/Closure_\(computer_programming\)).

Let's say we have a function `conversions` that accepts another function, `converter` as input:

```go
func conversions(converter func(int) int, x, y, z int) (int, int, int) {
	convertedX := converter(x)
	convertedY := converter(y)
	convertedZ := converter(z)
	return convertedX, convertedY, convertedZ
}
```

We _could_ define a function normally and then pass it in by name... but it's usually easier to just define it anonymously:

```go
func double(a int) int {
    return a + a
}

func main() {
    // using a named function
	newX, newY, newZ := conversions(double, 1, 2, 3)
	// newX is 2, newY is 4, newZ is 6

    // using an anonymous function
	newX, newY, newZ = conversions(func(a int) int {
	    return a + a
	}, 1, 2, 3)
	// newX is 2, newY is 4, newZ is 6
}
```


## The `defer` keyword
The `defer` keyword is a fairly unique feature of Go. It allows a function to be executed automatically _just before_ its enclosing function returns. The deferred call's arguments are evaluated immediately, but the function call is not executed until the surrounding function returns.

Deferred functions are typically used to clean up resources that are no longer being used. Often to close database connections, file handlers and the like.

For example:

```go
func GetUsername(dstName, srcName string) (username string, err error) {
	// Open a connection to a database
	conn, _ := db.Open(srcName)

	// Close the connection *anywhere* the GetUsername function returns
	defer conn.Close()

	username, err = db.FetchUser()
	if err != nil {
		// The defer statement is auto-executed if we return here
		return "", err
	}

	// The defer statement is auto-executed if we return here
	return username, nil
}
```

In the above example, the `conn.Close()` function is not called here:

```go
defer conn.Close()
```

It's called:

```go
// here
return "", err
// or here
return username, nil
```

Depending on whether the `FetchUser` function errored. (We'll cover errors later).

Defer is a great way to **make sure** that something happens before a function exits, even if there are multiple return statements, a common occurrence in Go.

## Multiple Defers

The location of a `defer` statement inside a function matters. The deferred call is registered at the point where `defer` is executed, and it will run when the function returns. If you have multiple `defer` statements in a single function, they are executed in **last-in, first-out** order (the last deferred call runs first).

For example, you'd want to close a file before trying to remove it:

```go
func CreateTempFile() {
	f, _ := os.Create("temp-42.txt")
	defer os.Remove(f.Name()) // executed second
	defer f.Close()           // executed first

	fmt.Fprintln(f, "How many roads must a man walk down?")
}
```


## Scope in Go

Like C/C++, and Unlike Python, Go is _not_ function-scoped, it's [block-scoped](https://go.dev/ref/spec#Declarations_and_scope). Variables declared inside a block are only accessible within that block (and its nested blocks). There's also the package scope. We'll talk about packages later, but for now, you can think of it as the outermost, nearly global scope.

```go
package main

// scoped to the entire "main" package (basically global)
var age = 19

func sendEmail() {
    // scoped to the "sendEmail" function
    name := "Jon Snow"

    for i := 0; i < 5; i++ {
        // scoped to the "for" body
        email := "snow@winterfell.net"
    }
}
```

Blocks are defined by curly braces `{}`. New blocks are created for:

- Functions
- Loops
- If statements
- Switch statements
- Select statements
- Explicit blocks

## Closures
A [closure](https://en.wikipedia.org/wiki/Closure_\(computer_programming\)) is a function that references variables from outside its own function body. The function may access and _assign_ to the referenced variables.

In this example, the `concatter()` function returns a function that has reference to an _enclosed_ `doc` value. Each successive call to `harryPotterAggregator` mutates that same `doc` variable.

```go
func concatter() func(string) string {
	doc := ""
	return func(word string) string {
		doc += word + " "
		return doc
	}
}

func main() {
	harryPotterAggregator := concatter()
	harryPotterAggregator("Mr.")
	harryPotterAggregator("and")
	harryPotterAggregator("Mrs.")
	harryPotterAggregator("Dursley")
	harryPotterAggregator("of")
	harryPotterAggregator("number")
	harryPotterAggregator("four,")
	harryPotterAggregator("Privet")

	fmt.Println(harryPotterAggregator("Drive"))
	// Mr. and Mrs. Dursley of number four, Privet Drive
}
```

## Function currying
Function [currying](https://en.wikipedia.org/wiki/Currying) turns a function with multiple arguments into successive functions that each accept one argument.

For example, we can supply a shirt's unit price now and the quantity later:

```go
func main() {
	shirtCost := curriedCost(20)
	fmt.Println(shirtCost(3))
	// prints 60

	fmt.Println(shirtCost(5))
	// prints 100
}

func cost(unitPrice, quantity int) int {
	return unitPrice * quantity
}

func curriedCost(unitPrice int) func(int) int {
	return func(quantity int) int {
		return cost(unitPrice, quantity)
	}
}
```

`curriedCost(20)` returns a closure that remembers the unit price and waits for a quantity. Calling `shirtCost(3)` supplies that quantity and calls `cost(20, 3)`.

You can also call the returned function immediately:

```go
cost(20, 3)        // returns 60
curriedCost(20)(3) // returns 60
```
---
