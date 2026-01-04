## **Console.log**

## VS Code snippets
1. Basically can create pieces of code that are commonly used and use them again. 

![[Pasted image 20260104074620.png]]

can create my own if needed.

---
## Rendering collections
1. we can generate dynamic list of li items like this :
```jsx
notes.map(note => <li>{note.content}</li>)
```

2. The result is an array of *li* elements which we can put inside `<ul>` tags:

```jsx
const App = (props) => {
  const { notes } = props

  return (
    <div>
      <h1>Notes</h1>
      <ul>
	      {notes.map(note => <li>{note.content}</li>)}</ul>    
    </div>
  )
}
```


## Key Attribute
