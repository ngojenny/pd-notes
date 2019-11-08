# Intermediate React - Brian Holt
## Basic Hooks
* `useState()` : makes component stateful. Returns an array of 2 items, piece of state  and an updater function. Hooks cannot be called in conditional or for loops as they are dependent on order.
* `useEffect()`: handles side effects. Not run immediately. It will run for the first time after the component renders. Second argument is an array of dependencies that will determine when it will run. You can leave the array blank to ensure `useEffect()` only runs once. However instead of leaving it blank, docs want use to put the updater in the array, it most likely will not change, so `useEffect()` will run once.
* `useContext()` : Alternative to prop drilling. Allows us to access state, that lives at the top level component, from a nested component. We create context using `createContext()`. `createContext()` passes an array that resembles a hook. To ensure our components can use the context. At the top level component, wrap child components in `<Context.Provider>`. We can read context using `useContext()`. See example:
```js
// see: https://codesandbox.io/s/github/btholt/react-hooks-examples/tree/master/
// Context.js
import React, { useState, useContext, createContext } from "react";

const UserContext = createContext([
  {
    firstName: "Bob",
    lastName: "Bobberson",
    suffix: 1,
    email: "bobbobberson@example.com"
  },
  obj => obj
]);

const LevelFive = () => {
  const [user, setUser] = useContext(UserContext);

  return (
    <div>
      <h5>{`${user.firstName} ${user.lastName} the ${user.suffix} born`}</h5>
      <button
        onClick={() => {
          setUser(Object.assign({}, user, { suffix: user.suffix + 1 }));
        }}
      >
        Increment
      </button>
    </div>
  );
};

const LevelFour = () => (
  <div>
    <h4>fourth level</h4>
    <LevelFive />
  </div>
);

const LevelThree = () => (
  <div>
    <h3>third level</h3>
    <LevelFour />
  </div>
);

const LevelTwo = () => (
  <div>
    <h2>second level</h2>
    <LevelThree />
  </div>
);

const ContextComponent = () => {
  const userState = useState({
    firstName: "James",
    lastName: "Jameson",
    suffix: 1,
    email: "jamesjameson@example.com"
  });

  return (
    <UserContext.Provider value={userState}>
      <h1>first level</h1>
      <LevelTwo />
    </UserContext.Provider>
  );
};

export default ContextComponent;

```
## Hooks In-Depth
* `useRef()`: allows you to hold onto DOM elements, timeouts or intervals. It returns an object with one property `.current`. `.current` is initialized with whatever argument that was passed. This object will persist for the entire lifetime of the component. We can also use this to access DOM elements. The value held in `.current` is mutable and doesn’t cause a re-render when it changes. We can use `useRef()` to access the DOM. From react docs: _If you pass a ref object to React with `<div ref={myRef} />`, React will set its current property to the corresponding DOM node whenever that node changes._
* `useReducer()`: like `useState`, this hook can interact with state. It accepts a reducer function `(state, action) => newState`, default State. It returns a current state and dispatch method. `useReducer()` is good for handling complex state logic that involves multiple sub-values or when the next state depends on the previous one
* `useMemo()`: used to optimize performance. Takes in a function and an array of dependencies. The function will only run when one of the dependencies change. In React, when the parent re-renders, its children will also re-render, which we want most of the time. However the child component has some non-performative methods, if we don’t want the children to re-render every time `useMemo()` can help with this. Might be an alternative to `componentShouldUpdate()`, see [useMemo Hook react tutorial - YouTube](https://www.youtube.com/watch?v=FSGQvH2kewE&vl=en) for example. * 
* `useCallback()`: similar to `useMemo()`, also used to optimized performance. Useful for when you need to prevent a function from being redefined every time. Examples shown uses `useCallback()` with `memo()`. [Kent Dodds says most of the time, you should not bother optimizing unnecessary re-renders.](https://kentcdodds.com/blog/usememo-and-usecallback). It is rare you will need this hook.
* `useLayoutEffect`: useful for when you want to measure something in the DOM. Similar to `useEffect()`, but will only run if the DOM has been manipulated. React docs recommend trying `useEffect()`, before turning to `useLayoutEffect`.
* `useImperativeHandle()`: Will mostly not use this - more for library authors

## CSS in JS
## Code Splitting
## Server Side Rendering
## TypeScript with React
## Redux
## Testing React