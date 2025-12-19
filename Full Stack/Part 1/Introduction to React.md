1. Easiest way to get started with react is by using a tool called **Vite**
	`npm create vite@latest`

![[Pasted image 20251219125830.png]]

2. The tool could have also installed the dependencies and started the app automatically if we had answered Yes to install with npm and start now question. But we do it manually here.
	`npm install`

3. The application can now be run as :
	`npm run dev`
	- by default, vite uses port 5173. If unavailable uses the next available port.

## Component
1. Source code is all in src.
2. The main files here are main.jsx and App.jsx
3. File App.jsx now defines a React component wit the name App:
```js
//App.jsx
const App = () => {
  return (
    <div>
      <p>Hello world</p>
    </div>
  )
}

export default App
```

```js
//main.jsx

import ReactDOM from 'react-dom/client';
import App from './App'

  

ReactDOM.createRoot(document.getElementById('root')).render(<App />)
```

4. The final line in main renders App component's content inside the 'root' element inside `index.html` that just sits outside of src: 

```html
<body>

<div id="root">
<!-- main.jsx renders App component here -->
</div>

<script type="module" src="/src/main.jsx"></script>

</body>
```

5. The component basically is a javascript function equivalent to:
	```js
const App = () => {
  return (
    <div>
      <p>Hello world</p>
    </div>
  )
}
	```
Functions name is App

6. Since this is just a normal js function, it can contain other stuff like:

```js
const App = () => {
  console.log("Hello from component")
  return (
    <div>
      <p>Hello world</p>
    </div>
  )
}
```

7. The first rule of frontend web development:
> 	_keep the console open all the time_

8. We can also put dynamic content inside the function like:
```js
const App = () => {
  const now = new Date()
  const a = 10
  const b = 20
  console.log(now, a+b)

  return (
    <div>
      <p>Hello world, it is {now.toString()}</p>
      <p>
        {a} plus {b} is {a + b}
      </p>
    </div>
  )
}
```

9. This line is imperative at the end of App.jsx, must not be removed.
```js
export default App
```

## JSX

1. It may look like react components are returning HTML markup. **This is not correct**

2. What they are returning is *Jsx*. Layout of React components is written using jsx. 

3. Although jsx looks like html, but under the hood it is being compiled directly into javascript and is not treated as html at all. 

After the above component is compiled it looks something like this:
```js
const App = () => {
  const now = new Date()
  const a = 10
  const b = 20
  return React.createElement(
    'div',
    null,
    React.createElement(
      'p', null, 'Hello world, it is ', now.toString()
    ),
    React.createElement(
      'p', null, a, ' plus ', b, ' is ', a + b
    )
  )
}
```

4. The compilation is done by Babel. Projects created with Vite are configured to compile automatically. (More on this part7)

5. It is possible to simply write react code in vanilla javascript like above. But is insane.

6. Every tag in JSX needs to be closed (xml like).


## Multiple Components
Let's add more components:
```jsx
const App = () => {

const now = new Date()

console.log(now)

return (
	<div>
		<Hello />
		<p>Hello world, it is {now.toDateString()}</p>
	</div>
)

}

const Hello = () => {
	return (
		<div>
		<h1>Greetings</h1>
		</div>
	)}
```
1. Here the Hello component is rendered before the hello world paragraph.

2. The philosophy of react is just this: Composing applications from many specialized reusable components.

3. A strong convention is that `App` is usually the root component. However this is not always true and will be explored in Part6

## Props: Passing data to components