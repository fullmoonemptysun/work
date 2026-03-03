
## JavaScript does not have classes? How does it do all the static functions and constructors then?

**Prototypes "Classes"**
**Dog is just a function. Functions are objects. Objects can have properties.**

```js
Dog = {
  [[Call]]: ...,        // makes it callable
  prototype: {          // auto-created, linked to all instances
    bark: fn,
    constructor: Dog
  },
  create: fn,           // just a property on Dog itself
  name: "Dog",
  length: 1
}
```

**`new Dog("Rex")` does two things:**

1. Creates a fresh object
2. Secretly links it to `Dog.prototype`

So `rex` can see `bark` (via chain), but not `create` (that's on `Dog`, not `Dog.prototype`).

**Prototype chain = delegation** When you access a property, JS looks on the object first, then walks up the chain. It doesn't find `bark` on `rex`, so it checks `Dog.prototype`. Found it.

**Classes are just syntax sugar** `static method` → stapled onto the constructor function itself `regular method` → stapled onto the constructor's prototype

**There are no classes.** Just objects, linked together, with properties pointing to functions.