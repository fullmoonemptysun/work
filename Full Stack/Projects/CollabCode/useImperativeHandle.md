- React hook, signature:
```jsx
import { useImperativeHandle } from 'react';  

  

function MyInput({ ref }) {  

useImperativeHandle(ref, () => {  

return {  

// ... your methods ...  

};  

}, []);  

// ...
```

inside the child component.

- The ref is defined in the parent component and basically is used to expose DOM elements/functions/properties to parent components.