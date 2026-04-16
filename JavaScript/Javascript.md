## Arrow Functions
basically instead of :
`double = function(n){return someOp(n)}` 
just do `double = (n) => someOp(n);`

- If no arguments:
`let sayHi = () => alert("Hello!");`

- anonymous functions 
```javascript
let age = prompt("What is your age?", 18);

let welcome = (age < 18) ?
  () => alert('Hello!') :
  () => alert("Greetings!");

welcome();
```

- multiline:
```javascript
let sum = (a, b) => {  // the curly brace opens a multiline function
  let result = a + b;
  return result; // if we use curly braces, then we need an explicit "return"
};

alert( sum(1, 2) ); // 3
```