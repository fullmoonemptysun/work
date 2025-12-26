
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


## Event Handling

A simple clicker application
```jsx
const App = () => {
  const [ counter, setCounter ] = useState(0)

  return (
    <div>
      <div>{counter}</div>
      <button onClick={() => setCounter(counter + 1)}>
        plus
      </button>
      <button onClick={() => setCounter(0)}>zero      </button>    </div>
  )
}
```

1. an event handler always needs to be a function or a function reference. it cannot be a function call, i.e, if a function is called upon and event, that function call should be made inside the event handler function. The function call cannot be the event handler function itself.


## Passing State - to child  components
1. *React components should be small and modular and reusable across the app and even different projects*. Let's refactor our application to follow this rule
`The official documentation says to lift the state up` which means to put the state in the top component and pass it down.

```jsx
const Display = (props) => {
return (
<div>{props.counter}</div>
)}


const App = () => {
  const [ counter, setCounter ] = useState(0)

  const increaseByOne = () => setCounter(counter + 1)
  const setToZero = () => setCounter(0)

  return (
    <div>
      <Display counter={counter}/>      <button onClick={increaseByOne}>
        plus
      </button>
      <button onClick={setToZero}> 
        zero
      </button>
    </div>
  )
}

```

2. When the buttons are clicked and `App` is rerendered, all its children are also re rendered.

Let's make the components more modular:
```jsx
const Button = (props) =>{
return (
<button onClick ={props.onClick}>
	{props.text}
</button>
)}
```
and finally it looks something like this:

```jsx
import {useState} from 'react'

const Display = ({counter}) => <div className="text-center">{counter}</div>;

const ButtonGroup = ({addButtonProps, resetButtonProps}) => {
    return (
        <div className=" flex gap-3 items-center justify-center">
        <Button  text={addButtonProps.text}  onClick = {addButtonProps.onClick}
        ></Button>

        <Button onClick = {resetButtonProps.onClick}text = {resetButtonProps.text}></Button>

        </div>
    )

}
    
const Button = ({onClick, text}) => {
    return (
        <button className="font-bold border rounded-lg px-6 p-1" onClick={onClick}>
            {text}
        </button>
    )
}

const App = () => {
    const [counter, setCounter] = useState(0)

    const handleClick = () => {
        console.log('clicked')
        setCounter(counter + 1);

    }

    const handleReset = () => {
        setCounter(0)
    }

    return (
        <>
        <Display counter = {counter}></Display>
    
        <ButtonGroup addButtonProps={{onClick: handleClick, text: "Add"}} resetButtonProps={{onClick: handleReset, text: "Reset"}}/>

        </>
        
    )
}

export default App

```

>[!Important] The main point
>- **Calling a function that changes the state causes the component to re-render.**
>- This change must be done through the setter function returned by `useState()` 
>- All the subcomponents get re-rendered as well.

## Refactoring the components

1. I already did this, basically destructuring and making components inline.

