# ReactJS
React documentation

# React.memo
###### App.js
```
//--App.js--
import './App.css';
import Button from './components/Button';
import { useState } from 'react';

function App() {
  const [counter, setCounter] = useState(0);
  return (
    <>
      <Button />
      <br />
      <h1>{counter}</h1>
      <br />
      <br />
      <button onClick={() => { setCounter(counter + 1) }}>Increment</button>
    </>
  );
}
export default App;
```
###### componenets/Button/index.js
```
//--components/Button/index.js--

function Button () {
    console.log("Button Component");
    return (
        <button>Click</button>
    )
}
export default Button;
```
But, if we don't want to render the button component every time. So, we use the *React Hook* **React.memo**. It memorises the component and do not let them to be rendered, every time when state changes but, only once at first time render.
Here, look the variation in the code of Button component.
to use **React.memo**, we have to first import. So, added the line

`import React from "react";`

Then, we use React.memo exactly where we are exporting the component like this

`export default React.memo(Button)`

See the complete code with changes.
```
//--components/Button/index.js--
import React from "react";
function Button () {
    console.log("Button Component");
    return (
        <button>Click</button>
    )
}
export default React.memo("Button");
```

Here is the glimpse of the working code.
![An example of React.memo (hook)](resources/React-memo.gif)


# useRef
### Example 1
using useRef hook get the value from input field.
###### App jsx
```
//--App.js--
import {useRef} from 'react';

function App() {
  const inputRef = useRef(null);

  return (
    <>
      <input ref={inputRef}/>
      <button onClick={()=>{console.log(inputRef.current.value)}}>Get input value</button>
    </>
  )
}

export default App;
```
![React Hook useRef to Get input field's value](resources/useRef_get_input_value.gif)

### Example 2
using useRef hook get a DOM element.

The previous code for counter, in which on every click on "Increment" button, the counter increments by 1 because of changing state using **useState** React hook.
Here I am using **useRef** to get the DOM element.

```
//--App.js--
import './App.css';

import {useState, useRef} from 'react';

function App() {
  const [counter, setCounter] = useState(0);
  const headingRef = useRef(null);

  return (
    <>
      <h1 ref={headingRef}>{counter}</h1>
      <br/>
      <button onClick = {()=>{setCounter(counter+1); console.log(headingRef.current)}}>Increment</button>

    </>
  );
}

export default App;

```
![React Hook useRef to get DOM element](resources/useRef_with_state_counter.gif)

# useCallback
You have seen the application of **React.memo** or simply **memo** that how it avoids unnecessary rendering by memorizing the rendered output. It improves the performance by memorizing the result and skip the last rendered result. Any component that put into **memo** method of **React** will not be re-rendered by with the re-rendering of main component of App because of **state change**. 
See below the example of **memo** usage again;

### Example 1: using memo avoid re-rendering
###### App.js
```
import {useState} from "react";
import Button from "./components/Button";
import Child from "./components/Child";


function App() {
  const [count, setCount] = useState(0);

  return(
    <div>
      <Child />
      <h1>{count}</h1>
      <button onClick={()=>setCount(oldValue => oldValue +1)}>Increment</button>
      <button disabled={count < 1} onClick={()=>setCount(oldValue => oldValue -1)}>Decrement</button>
    </div>
  )
}

export default App;
```
###### ./src/componenets/Button/index.js
```
import {memo} from "react";
function Child() {
    console.log("Child component");
    return(
        <div>
            <h1>Child component</h1>
        </div>
    )
}

export default memo(Child);
```
![React.memo to avoid re-rendering by memorizing](resources/memo_react_avoid_re-rendering.gif)

## BUT...
How long **memo** avoids the re-rendering of a component?
If a prop of a function or object is passed to the memorized component. Then it will **re-render** again with the re-rendering of the main component.

consider this code and its demo.
###### App.js
```
import {useState} from "react";
import Child from "./components/Child";

function App() {
  const [count, setCount] = useState(0);
  const getData = ()=> {
    return ["one", "two"];
  }
  return(
    <div>
      <Child getData={getData}/>
      <h1>{count}</h1>
      <button onClick={()=>setCount(oldValue => oldValue +1)}>Increment</button>
      <button disabled={count < 1} onClick={()=>setCount(oldValue => oldValue -1)}>Decrement</button>
    </div>
  )
}

export default App;
```
###### ./src/components/Child/index.js
```
import {memo} from "react";
function Child({getData}) {
    console.log(getData());
    console.log("Child component");
    return(
        <div>
            <h1>Child component</h1>
        </div>
    )
}
export default memo(Child);
```
![React.memo to avoid re-rendering fails](resources/memo_react_fails.gif)

<hr>
Done till here
<hr>

## useCallback

to tackle the above problem React has provided us a hook **useCallback()**.
It memorizes the functions/objects to be recreated because of App component rerending, Hence, the Child component (to which the function is passed as a prop) treats the functions as it was (nothing has been changes in the function nor it has been recreated) , and is not rerendered.

### syntax of useCallback

```
const memorizedFn = useCallback (function, [dependency array];
```

###### Example: avoid rerendering using useCallback hook
###### src/App.js
```
import {useState, useCallback} from "react";
import Child from "./components/Child";

function App() {
  const [count, setCount] = useState(0);
  const getData = useCallback(()=> {
    return ["one", "two"];
  }, []);
  return(
    <div>
      <Child getData={getData}/>
      <h1>{count}</h1>
      <button onClick={()=>setCount(oldValue => oldValue +1)}>Increment</button>
      <button disabled={count < 1} onClick={()=>setCount(oldValue => oldValue -1)}>Decrement</button>
    </div>
  )
}

export default App;

```

###### src/components/Child/index.js
```
import {memo} from "react";
function Child({getData}) {
    console.log(getData());
    console.log("Child component");
    return(
        <div>
            <h1>Child component</h1>
        </div>
    )
}

export default memo(Child);
```
![avoid_rerendering_by_function_recreation_using_useCallback](resources/avoid_rerendering_components_by_recreating_functions_using_useCallback.gif)


## useCallback with dependency

As you have understood the usage of **useCallback** hook in above example. The simple syntax of useCallback as

```
const memoizedCallback = useCallback(
  () => {
    // Function logic
  },
  [dependencies]
);
```

Here's how useCallback works and how to use it:

1. **The First Argument (Callback Function):**

The first argument to useCallback is the function that you want to memoize. This function will only be recreated if any of the dependencies in the dependency array change.

2. **The Second Argument (Dependencies):**

The second argument is an optional array of dependencies. If any of these dependencies change, the callback function is recreated. If the dependencies remain the same, the memoized function is returned.


###### src/App.js
```
import {useState, useCallback, useMemo} from "react";
import Child from "./components/Child";

function App() {
  const [count, setCount] = useState(0);
  const [show, setShow] = useState(false);


  const getData = useCallback(()=> {
    return ["one", "two"];
  }, [show]);
  return(
    <div>
      <Child getData={getData}/>
      <h1>{count}</h1>
      <button onClick={()=>setCount(oldValue => oldValue +1)}>Increment</button>
      <button disabled={count < 1} onClick={()=>setCount(oldValue => oldValue -1)}>Decrement</button>
      <br />
      <button onClick={()=> setShow (!show)}>{show ? "Hello" : "Hi"}</button>
    </div>
  )
}
```

###### src/components/Child/index.js
```
import {memo} from "react";
function Child({getData}) {
    console.log(getData());
    console.log("Child component");
    return(
        <div>
            <h1>Child component</h1>
        </div>
    )
}

export default memo(Child);
```


in this example:
- The `getData` function is memorized using `useCallback`.
- The `Child` component receives the `getData` function as a prop.
- the `count` state with the click on the **Increment** or **Decrement** button and thus the app re-renders but the `Child` component will not be re-rendered until yet. because it is memorized by the `useCallback` 
- The second argument of the `useCallback` hook is dependency, which depends on the **show** state.
- When the **show** state will be updated by toggle, the `App` component will be re-rendered and the Child componenet.

  `useCallback` is beneficial when passing functions down to child components, especially in scenarios where you want to avoid unnecessary re-renders of those child components due to the creation of new function references. It helps with optimizing the performance of your React application.

  ![useCallback hook depends on the change of show toggle](resources/useCallback_renders_with_show_toggle.gif)

# useMemo

  We have been used `React.memo` to memorize a **component** so to avoid re-rendering because of state updation.
  `useMemo` does the some work but for memorizing a **value**.
  Here is the syntax usage of `useMemo`

  **Syntax**
  ```
  const getValue = useMemo(calculateValue, dependencies)
  ```
  Similar to **useEffect**, **useCallback** and some other react hook `useMemo` has two parameters

  `calculateValue`: Any function that returns a pure value. The function should not accept any argument. React will call your function on initial render. On next renders, It will show the same value (because it will not be re-rendered) unless or until the dependencies are not changed.

  `dependencies`: It is array of values, on which the cashed value by **useMemo** depends. If there is a value, the cached value will only be changed by re-rendering with the change of the **value** in the dependencies. If the array is empty, the cached value will be the same.

  **Example**

```
import {useState, useMemo} from "react";

function App() {
  const [count, setCount] = useState(0);
  const getRandomValue =  () => {
    return Math.random()*10;
  }
    const getValue = useMemo(()=> getRandomValue(), []); 

  return (
    <div>
      <h1>{getValue}</h1>
      <h1>{count}</h1>
      <button onClick = {()=> setCount (oldValue => oldValue + 1)}> Inc</button>
      <button onClick = {()=> setCount (oldValue => oldValue - 1)}> Dec</button>
    </div>
  )
}
export default App;
```

**Lets break down the above code**

1. **State Management**
   - The component uses the `useState` hook to manage a state variable called `count` with an initial value of 0.

2. **Random Value Function**
   - There's a function called `getRandomValue` that returns a random value between 0 and 10. However, this function is not currently being used in the component render.

3. **`useMemo` for Memorization**
   - The `useMemo` hook is used to memorized the result of the `getRandomValue` function. It takes two arguments: a function to memorized the calculated value and an array of dependencies.
   - In this case, the function passed to `useMemo` is `getRandomValue`, and the dependency array `[]` indicates that the memorized value should be calculated only once ( on the initial render) because there are no dependencies.
   - The result of `useMemo` is stored in the variable `getValue`

4. **Rendering**
   - The component renders three elements.
   - An `<h1>` element displaying the memorized value obtained from `getValue`.
   - Another `<h1>` element displaying the current value of `count`.
   - Two buttons to inrement and decrement the `count` state.
  
   **overall**
   - The use of `useMemo` in this example might seem a bit redundant because `getRandomValue` doesn't have any dependencies. Memorization is more beneficial when dealing with expensive calculations or functions that depend on specific values, preventing unnecessary recalculations on every render. In this case, it's ensuring that the random value is only calculated once during the initial render.
   - **If you were to use `getRandomValue` directly without `useMemo`, it would be recalculated on every render, which might be unnecessary.**

  ![useMemo hook to memorize calculated value](resources/44.2_useMemo_memorized_value.gif)
