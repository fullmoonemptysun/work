1. Today, the most popular way to do transpiling is by using [Babel](https://babeljs.io/). Transpilation is automatically configured in React applications created with Vite. We will take a closer look at the configuration of the transpilation in [part 7](https://fullstackopen.com/en/part7) of this course.

>[!Success] Transpiling
>Transpiling refers to the process of converting source code written in one high-level programming language into another high-level programming language while preserving the original functionality. 
>- Used in this context to make newer javascript code backwards compatible. (turn newer features into older alternatives)

2. [Node.js](https://nodejs.org/en/) is a JavaScript runtime environment based on Google's [Chrome V8](https://developers.google.com/v8/) JavaScript engine and works practically anywhere - from servers to mobile phones. Let's practice writing some JavaScript using Node. The latest versions of Node already understand the latest versions of JavaScript, so the code does not need to be transpiled.

3. A javascript file can be run in node using `node fname.js`

- Variables
- Arrays
```js
//map function
const t = [1, 2, 3]

const m1 = t.map(value => value * 2)
console.log(m1)   // [2, 4, 6] is printed
const m2 = t.map(value => '<li>' + value + '</li>')
console.log(m2)  
// [ '<li>1</li>', '<li>2</li>', '<li>3</li>' ] is printed

//Destructuring assignment 
const t = [1, 2, 3, 4, 5]

const [first, second, ...rest] = t

console.log(first, second)  // 1 2 is printed
console.log(rest)          // [3, 4, 5] is printed





```



## Objects and `this`

1. Basically say we have an object like below:

```js
const dog = {name: "tom",
age: 3,
voice: "arf",
speak: function(){
	console.log(this.voice)
},

}

//then we do:
dog.speak() // will print "arf"

//but we can also have a reference to an object's function

const bark = dog.speak()

bark() // will print undefined or cause errors because the meaning of `this` which refers to the object the function/parameter belongs to is lost and is set to the....
```
...**Global Object.**

The following code also causes the same issue:
```js
const arto = {
  name: 'Arto Hellas',
  greet: function() {
    console.log('hello, my name is ' + this.name)
  },
}


setTimeout(arto.greet, 1000) //is called by the V8 engine and so the meaning of arto is lost. 


```

1. One way to solve this problem is through the `bind` function:
```js
setTimeout(arto.greet.bind(arto), 1000)
```
ensures this points to arto no matter where the function is being called from

---
# More on `this`

1. When used outside of any function, it refers to the global object, which is `window` in browsers and `global` within a node repo. it becomes `module.exports` for node modules.

2. When inside a function, `this` highly depends on how and where the function is called.

```js
function func(){
	"use strict"
	console.log(this==global);
}

func()
```
in strict mode the value of this is undefined, in normal mode, it is the global object. (only the function needs to be in strict mode, not the call side)

```js
function Person(firstName, lastName){
	this.firstName = firstName
	this.lastName = lastName
	}
	
const person = Person("Jane", "Doe"); //will assign firstName and lastName properties to the global object, this refers to it. IF in strict mode, cause an error because will try to access undefined's properties.

//To ensure the correct way of creating an object:
const person = new Person("Jane", "Doe"); //new must be used.
```

---

## Classes

1. Javascript does not have Classes in the same sense as Java or C# does but it does introduce an API that simulates class functionality. 

```js
class Person {
  constructor(name, age) {
    this.name = name
    this.age = age
  }
  greet() {
    console.log('hello, my name is ' + this.name)
  }
}

const adam = new Person('Adam Ondra', 29)
adam.greet()

const janja = new Person('Janja Garnbret', 23)
janja.greet()
```

2. The typeof of a class instance is also an Object.
3. Were heavily used in "old" React. But are now replaced with hooks.


## Misc. Javascript
1. Arrow functions are not semantically the same as doing it with the `function` keyword. We will see how they are different later.

>[!Important] ### [Inner functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Language_overview#inner_functions)
JavaScript function declarations are allowed inside other functions. **An important detail of nested functions in JavaScript is that they can access variables in their parent function's scope:**

2. 3 types of function calling conventions that enable asynchronous programming:
```js
	// Callback-based
fs.readFile(filename, (err, content) => {
  // This callback is invoked when the file is read, which could be after a while
  if (err) {
    throw err;
  }
  console.log(content);
});
// Code here will be executed while the file is waiting to be read

// Promise-based
fs.readFile(filename)
  .then((content) => {
    // What to do when the file is read
    console.log(content);
  })
  .catch((err) => {
    throw err;
  });
// Code here will be executed while the file is waiting to be read

// Async/await
async function readFile(filename) {
  const content = await fs.readFile(filename);
  console.log(content);
}
	```

>[!important] ### [Modules](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Language_overview#modules)
>JavaScript also specifies [Modules](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Language_overview#modules)a module system supported by most runtimes. A module is usually a file, identified by its file path or URL. You can use the [`import`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import) and [`export`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/export) statements to exchange data between modules:
>```js
import { foo } from "./foo.js";
>
// Unexported variables are local to the module
const b = 2;
>
export const a = 1; // remember this???
>```
>
>
>However, the JavaScript language doesn't offer standard library modules, all core functionalities are powered by global variables like [`Math`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math) and [`Intl`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl) instead. This is due to the long history of JavaScript lacking a module system, and the fact that opting into the module system involves some changes to the runtime setup.
>Different runtimes may use different module systems. For example, [Node.js](https://nodejs.org/en/) uses the package manager [npm](https://www.npmjs.com/) and is mostly file-system based, while [Deno](https://deno.com/) and browsers are fully URL-based and modules can be resolved from HTTP URLs.
>

3. Javascript is a language that focuses on the logic rather than the input output and so it always needs a runtime. Without a runtime we can't even know what it is doing.

4. A runtime, or a host, is something that feeds data to the JavaScript engine (the interpreter), provides extra global properties, and provides hooks for the engine to interact with the outside world. Module resolution, reading data, printing messages, sending network requests, etc. are all runtime-level operations.








