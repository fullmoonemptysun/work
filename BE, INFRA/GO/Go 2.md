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
