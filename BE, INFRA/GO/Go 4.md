## The error interface
Go programs express errors with `error` values. An Error is any type that implements the simple built-in [error interface](https://blog.golang.org/error-handling-and-go):

```go
type error interface {
    Error() string
}
```

When something can go wrong in a function, that function should return an `error` as its last return value. Any code that calls a function that can return an `error` should handle errors by testing whether the error is `nil`.

### Atoi

Let's look at how the [`strconv.Atoi`](https://pkg.go.dev/strconv#Atoi) function uses this pattern. The function signature of `Atoi` is:

```go
func Atoi(s string) (int, error)
```

This means `Atoi` takes a string argument and returns two values: an integer and an `error`. If the string can be successfully converted to an integer, `Atoi` returns the integer and a `nil` error. If the conversion fails, it returns zero and a non-nil error.

Here's how you would safely use `Atoi`:

```go
// Atoi converts a stringified number to an integer
i, err := strconv.Atoi("42b")
if err != nil {
    fmt.Println("couldn't convert:", err)
    // because "42b" isn't a valid integer, we print:
    // couldn't convert: strconv.Atoi: parsing "42b": invalid syntax
    // Note:
    // 'strconv.Atoi: parsing "42b": invalid syntax' is returned by the .Error() method
    return
}
// if we get here, then the
// variable i was converted successfully
```

A `nil` error denotes success; a non-nil error denotes failure.



## Creating errors
Since error is just an interface, we can create our own error types: 
```go
type userError struct {
    name string
}

func (e userError) Error() string {
    return fmt.Sprintf("%v has a problem with their account", e.name)
}
```

then we can return it like this:
```go
func (u user) signin() (int, error){
	if !login(user.username){
		return userError{name: user.username}
	
	}
	
}
```


[errors.New()](https://pkg.go.dev/errors#New) 
```go
var err error = errors.New("something went wrong")
```
returns error structs with the string message.




## Panic (basically, never use this)
There is another way to deal with errors in Go: the [`panic`](https://golang.org/ref/spec#Handling_panics) function. When a function calls `panic`, the program crashes and prints a stack trace.

As a general rule, _do not use panic!_

The `panic` function yeets control out of the current function and up the call stack until it reaches a function that [defers a `recover`](https://go.dev/blog/defer-panic-and-recover). If the panic isn't recovered in the same goroutine, the entire program crashes.

```go
func enrichUser(userID string) User {
    user, err := getUser(userID)
    if err != nil {
        panic(err)
    }
    return user
}

func main() {
    defer func() {
        if r := recover(); r != nil {
            fmt.Println("recovered from panic:", r)
        }
    }()

    // this panics, but the defer/recover block catches it
    // a truly astonishingly bad way to handle errors
    enrichUser("123")
}
```

Sometimes new Go developers look at `panic/recover`, and think, "This is like `try/catch`! I like this!" Don't be like them.


## Loops
- For loops:
```go
for i := 0; i < 10; i++ {
  fmt.Println(i)
}
// Prints 0 through 9
```

- Loops in Go can omit sections of a for loop. For example, the CONDITION (middle part) can be omitted which causes the loop to run forever.
```go
for INITIAL; ; AFTER {
  // do something forever
}
```
