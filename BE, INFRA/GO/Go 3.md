## Structs
We use [structs](https://go.dev/ref/spec#Struct_types) in Go to represent structured data. It's often convenient to group different types of variables together. For example, if we want to represent a car we could do the following:

```go
type car struct {
	brand      string
	model      string
	doors      int
	mileage    int
}
```

This creates a new struct type called `car`. All cars have a `brand`, `model`, `doors` and `mileage`.

To create a `car`, use a struct literal:

```go
myCar := car{
	brand:   "Toyota",
	model:   "Camry",
	doors:   4,
	mileage: 5000,
}
```

Structs in Go are often used to represent data that you might use a dictionary or object for in other languages.

Structs can be nested to represent more complex entities:

```go
type car struct {
  brand string
  model string
  doors int
  mileage int
  frontWheel wheel
  backWheel wheel
}

type wheel struct {
  radius int
  material string
}
```

The fields of a struct can be accessed using the dot `.` operator.

```go
myCar := car{}
myCar.frontWheel.radius = 5
```

We can also have anonymous struct but in general, _prefer named structs_. Named structs make it easier to read and understand your code, and they have the nice side-effect of being reusable. I sometimes use anonymous structs when I _know_ I won't ever need to use a struct again. For example, sometimes I'll use one to create the shape of some JSON data in HTTP handlers.

If a struct is only meant to be used once, then it makes sense to declare it in such a way that developers down the road won't be tempted to accidentally use it again.


## Embedded Structs and Nested Structs

Nested structs are basically what they seem, structs that are nested like this:
```go

type vehicle struct{
	name string,
	number int,
	vehicleWheel wheel, 
}

type wheel struct{
	material string,
	size float64,
}

myVehicle := vehicle{
	name: "bonnie",
	number: 2123,
	//vehicleWheel (named) instead of auto named from type (wheel)
	vehicleWheel: wheel{
		material: "carbon",
		size: 3.44,
	}
}

//just a nested struct
```

There is no object orientation in Go so we use **Embedded Structs** to do loose inheritance: 

```go
type vehicle struct{
	name string,
	number int,
	vehicleWheel wheel,
}

type wheel struct{
	material string,
	size float64,
}

type car struct{
	model string,
	vehicle,
}


myCar car := car{
	model: "ford",
	//this name has to be the same as the struct type name. Auto derived. The embedded struct is never explicitly named. The moment it is named, it becomes a nested struct and a parameter member of the parent struct. This is the difference between the two. The initiliazation looks pretty similar for both too. 
	vehicle: vehicle{
		//vehicle init
	}

}
```

vehicle here is not a name but the type itself. It means the vehicle struct is embedded and not nested. To access its access members, we can just do `myCar.membername` meanwhile, in nested structs we go through the access chain : `myCar.myVehicle.membername`


## Struct methods in Go

While Go is **not** object-oriented, it does support methods that can be defined on structs. Methods are just functions that have a receiver. A receiver is a special parameter that syntactically goes _before_ the name of the function.

```go
type rect struct {
  width int
  height int
}

// area has a receiver of (r rect)
// rect is the struct
// r is the placeholder
func (r rect) area() int {
  return r.width * r.height
}

var r = rect{
  width: 5,
  height: 10,
}

fmt.Println(r.area())
// prints 50
```



## Memory Layout in Structs
Similar to C/C++ struct fields are stored as contiguous entries inside memory. And also similar to C/C++, the Go compiler likes to keep things on certain boundaries, this can lead to unnecessary padding and additional memory usage (but optimizes speed).

The point is, in cases where memory occupancy is a bottleneck, it is good to think about the ordering of struct fields. Usually when we have the larger fields before and so on and so forth to avoid padding, its better. 

![[Pasted image 20261113001506.png]]
```go 
type social struct{
	reach uint16,
	numPosts uint8,
	numLikes uint8
}
```

Versus:

![[Pasted image 20261113001625.png]]
```go
type social struct{
	numPosts uint8,
	reach uint16,
	numLikes uint8,
}
```

- We can also use the [reflect package](https://pkg.go.dev/reflect) to debug the memory layout of a struct:
```go
typ := reflect.TypeOf(stats{})
fmt.Printf("Struct is %d bytes\n", typ.Size())
```

## Empty Struct
[Empty structs](https://dave.cheney.net/2014/03/25/the-empty-struct) are used in Go as a [unary](https://en.wikipedia.org/wiki/Unary_operation) value.

```go

// anonymous empty struct type
empty := struct{}{}

// named empty struct type
type emptyStruct struct{}
namedEmpty := emptyStruct{}
```

Later in this course, you'll see how and when they're used: it's surprisingly often! Mostly with maps and channels.


Empty structs are Go's smallest possible type: they take up **zero bytes of memory**.

Compare their size with the other types below:
![[Pasted image 20261113163951.png]]


## Interfaces
- Basically interfaces from OOP: [Interfaces](https://go.dev/tour/methods/9) allow you to focus on what a type does rather than how it's built. They can help you write more flexible and reusable code by defining behaviors (like methods) that different types can share. This makes it easy to swap out or update parts of your code without changing everything else.

Interfaces are just collections of method signatures. A type "implements" an interface if it has methods that match the interface's method signatures.

In the following example, a "shape" must be able to return its area and perimeter. Both `rect` and `circle` fulfill the interface.

```go
type shape interface {
  area() float64
  perimeter() float64
}

type rect struct {
    width, height float64
}
func (r rect) area() float64 {
    return r.width * r.height
}
func (r rect) perimeter() float64 {
    return 2*r.width + 2*r.height
}

type circle struct {
    radius float64
}
func (c circle) area() float64 {
    return math.Pi * c.radius * c.radius
}
func (c circle) perimeter() float64 {
    return 2 * math.Pi * c.radius
}
```

When a type implements an interface, it can then be used as that interface type.

```go
func printShapeData(s shape) {
	fmt.Printf("Area: %v - Perimeter: %v\n", s.area(), s.perimeter())
}
```

Because we say the input is of type `shape`, we know that any argument must implement the `.area()` and `.perimeter()` methods.

As an example, because [the empty interface](https://go.dev/tour/methods/14) doesn't require a type to have any methods at all, every type automatically implements the empty interface, written as:

```go
interface{}
```


Interfaces are implemented _implicitly_.
A type never declares that it implements a given interface. If an interface exists and a type has the proper methods defined, then the type automatically fulfills that interface.
A quick way of checking whether a struct implements an interface is to declare a function that takes an interface as an argument. If the function can take the struct as an argument, then the struct implements the interface.


```go
type Copier interface {
  Copy(sourceFile string, destinationFile string) (bytesCopied int)
}
```
This is much better. We can see what the expectations are now. The first parameter is the `sourceFile`, the second parameter is the `destinationFile`, and `bytesCopied`, an integer, is returned. It is not required to name the parameters inside the interface. However it is obviously great for clarity and readability.


## How to assert the actual underlying type from the interface

When working with interfaces in Go, every once-in-awhile you'll need access to the underlying type of an interface value. You can cast an interface to its underlying type using a [type assertion](https://go.dev/tour/methods/15). The example below shows how to safely access the `radius` field of `s` when it is not known that `s` is a `circle`:
```go
type shape interface {
	area() float64
}

type circle struct {
	radius float64
}

func (c circle) area() float64 {
	// ...
}

func printShapeInfo(s shape) {
	c, ok := s.(circle)
	if ok {
		radius := c.radius
		fmt.Printf("s is a circle, radius: %v\n", radius)
		return
	}
}
```

In `printShapeInfo`:
- We want to check if `s` is a `circle` in order to cast it into its underlying concrete type
- We know (from the function signature) that `s` is an instance of the `shape` interface, but we do not know if it's also a `circle`
- `c` is a new `circle` struct cast from `s`
- `ok` is `true` if `s` is indeed a `circle`, or `false` if `s` is NOT a `circle`

## Switch Case way to assert type 
A [type switch](https://go.dev/tour/methods/16) makes it easy to do several type assertions in a series. A type switch is similar to a regular switch statement, but the cases specify _types_ instead of _values_.
```go
func printNumericValue(num interface{}) {
	switch v := num.(type) {
	case int:
		fmt.Printf("%T\n", v)
	case string:
		fmt.Printf("%T\n", v)
	default:
		fmt.Printf("%T\n", v)
	}
}

//imp: here, v is not the type, but the object of that type itself.

func main() {
	printNumericValue(1)
	// prints "int"

	printNumericValue("1")
	// prints "string"

	printNumericValue(struct{}{})
	// prints "struct {}"
}
```
`fmt.Printf("%T\n", v)` prints the _type_ of a variable.


## A little bit of delegation and some examples:

Read this:
```go
//if we have something like this
type car interface{
	Color() string
	Speed() int
}

//it can be useful to have both of these:

/**
This is basically a joined interface. Whatever implements firetruck, now has to implement car and also the methods firetruck defines.
**/
type firetruck interface{
	car
}

/**
Here, the interface is embedded inside a struct instead of another interface. Here loggedVehicle can be a firetruck, or a sedan, or any other type that implements the car interface. 
**/
type loggedVehicle struct{
	car
}
// if we have, firetruck, sedan all having there own implementations of the method then this loggedVehicle struct can represent any of them, and we don't have to create multiple different loggedXXX versions for each vehicle type. 

```

## Final Notes on Interfaces
#### 1. Keep Interfaces Small
If there is only one piece of advice that you take away from this lesson, make it this: **keep interfaces small**! Interfaces are meant to define the minimal behavior necessary to accurately represent an idea or concept.
Here is an example from the standard HTTP package of a larger interface that's a good example of defining minimal behavior:

```go
type File interface {
    io.Closer
    io.Reader
    io.Seeker
    Readdir(count int) ([]os.FileInfo, error)
    Stat() (os.FileInfo, error)
}
```

Any type that satisfies the interface's behaviors can be considered by the HTTP package as a _File_. This is convenient because the HTTP package doesn't need to know if it's dealing with a file on disk, a network buffer, or a simple slice of bytes.

#### 2. Interfaces Should Have No Knowledge of Satisfying Types
An interface should define what is necessary for other types to classify as a member of that interface. They shouldn't be aware of any types that happen to satisfy the interface at design time.
For example, let's assume we are building an interface to describe the components necessary to define a car.

```go
type car interface {
	Color() string
	Speed() int
	IsFiretruck() bool
}
```

`Color()` and `Speed()` make perfect sense. They are methods confined to the scope of a car. `IsFiretruck()` is an anti-pattern. We are forcing all cars to declare whether or not they are firetrucks. In order for this pattern to make any amount of sense, we would need a whole list of possible subtypes. `IsPickup()`, `IsSedan()`, `IsTank()`... where does it end??

Instead, the developer should have relied on the native functionality of type assertion to derive the underlying type when given an instance of the car interface. Or, if a sub-interface is needed, it can be defined as:

```go
type firetruck interface {
	car
	HoseLength() int
}
```

Which inherits the required methods from `car` as an [embedded interface](https://gobyexample.com/struct-embedding) and adds one additional required method to make the `car` a `firetruck`.
#### 3. Interfaces Are Not Classes
- Interfaces are not classes, they are slimmer.
- Interfaces don't have constructors or destructors that require that data is created or destroyed.
- Interfaces aren't hierarchical by nature, though there is syntactic sugar to create interfaces that happen to be supersets of other interfaces.
- Interfaces define function signatures, but not underlying behavior. Making an interface often won't [DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself) up your code in regard to struct methods. For example, if five types satisfy the [`fmt.Stringer`](https://go.dev/tour/methods/17) interface, they all need their own version of the `String()` function.

---
