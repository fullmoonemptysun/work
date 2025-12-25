
## Destructuring
The following two component definitions are identical:

```jsx
const Hello = (props) => {
	const name = props.name
	const age = props.age
	const bornYear = ()=> ((new Date().getFullYear()) - age);
	return (
	<div>
		<p>
		Hello {props.name}, you are {age} years old
		</p>
		<p>So you were probably born in {bornYear()}</p>
	</div>
	)}



const Hello = ({name, age})=> {
	const bornYear = () => new Date().getFullYear() - age
	return (
	<div>
		<p>
		Hello {props.name}, you are {age} years old
		</p>
		<p>So you were probably born in {bornYear()}</p>
	</div>
	)
}
```
Instead of assigning the entire prop into a variable named props and then assigning its properties, we simply assign the property values to the variables directly by destructuring.


## Page Re-rendering

1. Another cool destructuring example:
```js
const App = (props) => {
  const {counter} = props
  return (
    <div>{counter}</div>
  )
}

export default App
```
takes counter property from props and assigns it to counter

2. We use the `.render()` function to re render if we want to update things on screen (re render, not reload):
```jsx
const root = ReactDOM.createRoot(document.getElementById('root'))

const refresh = () => {
	root.render(<App counter = {counter}/>)

}

setInterval(()=>{
refresh()
counter += 1},1000)
```

3. The **`setInterval()`** method of the [`Window`](https://developer.mozilla.org/en-US/docs/Web/API/Window) interface repeatedly calls a function or executes a code snippet, with a fixed time delay between each call.

4. This way of rerendering using the render function works but is not the recommended way.

## Stateful Component

1. In say the following snippet,
```jsx
import { sculptureList } from './data.js';

export default function Gallery() {
  let index = 0;

  function handleClick() {
    index = index + 1;
  }

  let sculpture = sculptureList[index];
  return (
    <>
      <button onClick={handleClick}>
        Next
      </button>
      <h2>
        <i>{sculpture.name} </i> 
        by {sculpture.artist}
      </h2>
      <h3>  
        ({index + 1} of {sculptureList.length})
      </h3>
      <img 
        src={sculpture.url} 
        alt={sculpture.alt}
      />
      <p>
        {sculpture.description}
      </p>
    </>
  );
}
```
Once the page is re rendered, the full component is rendered from scratch and so the internal variable change is not tracked. Basically nothing will change even after we re render.

2. Using state hooks:
```jsx
import {useState} from 'react'
const App = () => {
const [counter, setCounter] = useState(0)

setTimeout(
() => setCounter(counter + 1),
1000
)
return (
<div>{counter}</div>
)}
export default App
```
- we import useState function
- Then we use deconstruction style to get two variables, counter and setCounter (useState returns an array of 2. Takes in the initial state)
- The first variable is the state variable, meanwhile the second variable is a setter function for that state variable.
- The state of the component is changing
- Once the setter is called, the component gets re rendered and useState returns 1 this time.
- When setTimeout is called, it is called with 1 and then it calls setter with 1 + 1  = 2 and so on and so forth.


3. 