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
* Shows how to use the `emotion.js` library to add CSS in our JS
* Seems similar to styled-components. It keeps things modular. If we need to remove a component, the css would also be removed as well, we don’t have to figure out how to clean up the external css file

## Code Splitting
* defer the loading of pieces of code until it’s actually needed by the user/application
* Uses `React.lazy()` (takes a dynamic import), which returns a Promise (resolves to a module containing a React Component) and wraps the lazy component in `<React.Suspense>`
* Suspense will not render its children until `lazy()` resolves;
* Code Splitting is not worth doing unless you would be splitting out something that is over 30kb (compressed js)
*  A good use case may be code splitting child components that are importing large libraries  
```js
// In SearchParam.js
import _ from "lodash";
import moment from "moment";

// In App.js
const SearchParams = lazy(() => import("./Details");
```

## Server Side Rendering
* allows us to pre-render our App on the server, send the markup to the client, have JS load and React takes over once it is done loading
* server side rendering to node stream: uses `renderToNodeStream()` to send pieces of markup instead of entire payload

## TypeScript with React
* TypeScript checks for errors, related to data types, in your code
* Provides inline documentation as you type, e.g. which properties are avail in an obj, spelling, methods on type
* Configuring TypeScript
  1) `npm install -D typescript`: install TypeScript
  2) `npx tsc --init`: creates a tsconfig.json file
  3) in tsconfig.json, change target to `es2018`, uncomment `jsx` and set its value to `react`
  4) `npm install -D @types/react @types/react-dom @types/reach__router`: allows us to use typescript with react
* TypeScript will not check `.js` files by default
* Setting up tslint (tslint will eventually migrate over to eslint). Course shows how to take out eslint and replace it was tslint:
  1) `npm uninstall eslint babel-eslint eslint-config-prettier eslint-plugin-import eslint-plugin-jsx-a11y eslint-plugin-react eslint-plugin-react-hooks`
  2) `npm install -D tslint tslint-react tslint-config-prettier`
  3) `remove .eslintrc.json`
  4) in `package.json` change `scripts.lint`’s value to `tslint --project`
  5) create `tslint.json` and add:
```json
{
"extends":["tslint:recommended","tslint-react","tslint-config-prettier"],
  "rules":{
    "ordered-imports":false,
    "object-literal-sort-keys":false,
    "no-console":false,
    "jsx-no-lambda":false,
    "member-ordering":false
  }
}
```
6) install tslint extension for VScode

* you can hold `cmd` and click on methods to get tslint documentation on it
* Using TypeScript
  * renaming our component files to `.tsx`
  * example:
Modal.js
```js
import React, { useEffect, useRef } from "react";
import { createPortal } from "react-dom";

const modalRoot = document.getElementById("modal");

const Modal = ({ children }) => {
  const elRef = useRef(null);
  if (!elRef.current) {
    elRef.current = document.createElement("div");
  }

  useEffect(() => {
    modalRoot.appendChild(elRef.current);
    return () => modalRoot.removeChild(elRef.current);
  }, []);

  return createPortal(<div>{children}</div>, elRef.current);
};

export default Modal;

```
Modal.tsx
```js
import React, { useEffect, useRef, FunctionComponent } from "react";
import { createPortal } from "react-dom";

const modalRoot = document.getElementById("modal");

const Modal: FunctionComponent = ({ children }) => {
  const elRef = useRef(document.createElement("div"));

  useEffect(() => {
    if (!modalRoot) {
      return;
    }
    modalRoot.appendChild(elRef.current);
    return () => {
      modalRoot.removeChild(elRef.current);
    };
  }, []);

  return createPortal(<div>{children}</div>, elRef.current);
};

export default Modal;

```
ThemeContext.js
```js
import { createContext } from "react";

const ThemeContext = createContext(["green", () => {}]);

export default ThemeContext;

```
ThemeContext.tsx
```js
import { createContext } from "react";

// providing a type paramenter, returns nothing so we type void
// telling createContext, what we're going to give it - a string and function
const ThemeContext = createContext<[string, (theme: string) => void]>([
  "green",
  () => {}
]);

export default ThaemeContext;

```

Details.js
```js
class Details extends React.Component {
  state = { loading: true, showModal: false };
  componentDidMount() {
    pet
      .animal(this.props.id)
      .then(({ animal }) => {
        this.setState({
          name: animal.name,
          animal: animal.type,
          location: `${animal.contact.address.city}, ${
            animal.contact.address.state
          }`,
          description: animal.description,
          media: animal.photos,
          breed: animal.breeds.primary,
          url: animal.url,
          loading: false
        });
      })
      .catch(err => this.setState({ error: err }));
  }
  toggleModal = () => this.setState({ showModal: !this.state.showModal });
  adopt = () => navigate(this.state.url);
  render() {
    if (this.state.loading) {
      return <h1>loading … </h1>;
    }

    const {
      animal,
      breed,
      location,
      description,
      media,
      name,
      showModal
    } = this.state;

    return (
      <div className="details">
        <Carousel media={media} />
        <div>
          <h1>{name}</h1>
          <h2>{`${animal} — ${breed} — ${location}`}</h2>
          <ThemeContext.Consumer>
            {([theme]) => (
              <button
                style={{ backgroundColor: theme }}
                onClick={this.toggleModal}
              >
                Adopt {name}
              </button>
            )}
          </ThemeContext.Consumer>
          <p>{description}</p>
          {showModal ? (
            <Modal>
              <h1>Would you like to adopt {name}?</h1>
              <div className="buttons">
                <button onClick={this.adopt}>Yes</button>
                <button onClick={this.toggleModal}>No</button>
              </div>
            </Modal>
          ) : null}
        </div>
      </div>
    );
  }
}

export default function DetailsErrorBoundary(props) {
  return (
    <ErrorBoundary>
      <Details {...props} />
    </ErrorBoundary>
  );
}

```

Details.tsx
```js
class Details extends React.Component<RouteComponentProps<{ id: string }>> {
  public state = {
    loading: true,
    showModal: false,
    name: "",
    animal: "",
    location: "",
    description: "",
    media: [] as Photo[],
    url: "",
    breed: ""
  };
  public componentDidMount() {
    if (!this.props.id) {
      navigate("/");
      return;
    }
    pet
      .animal(+this.props.id)
      .then(({ animal }) => {
        this.setState({
          name: animal.name,
          animal: animal.type,
          location: `${animal.contact.address.city}, ${
            animal.contact.address.state
          }`,
          description: animal.description,
          media: animal.photos,
          breed: animal.breeds.primary,
          url: animal.url,
          loading: false
        });
      })
      .catch((err: Error) => this.setState({ error: err }));
  }
  public toggleModal = () =>
    this.setState({ showModal: !this.state.showModal });
  public adopt = () => navigate(this.state.url);
  public render() {
    if (this.state.loading) {
      return <h1>loading … </h1>;
    }

    const {
      animal,
      breed,
      location,
      description,
      media,
      name,
      showModal
    } = this.state;

    return (
      <div className="details">
        <Carousel media={media} />
        <div>
          <h1>{name}</h1>
          <h2>{`${animal} — ${breed} — ${location}`}</h2>
          <ThemeContext.Consumer>
            {([theme]) => (
              <button
                style={{ backgroundColor: theme }}
                onClick={this.toggleModal}
              >
                Adopt {name}
              </button>
            )}
          </ThemeContext.Consumer>
          <p>{description}</p>
          {showModal ? (
            <Modal>
              <h1>Would you like to adopt {name}?</h1>
              <div className="buttons">
                <button onClick={this.adopt}>Yes</button>
                <button onClick={this.toggleModal}>No</button>
              </div>
            </Modal>
          ) : null}
        </div>
      </div>
    );
  }
}

export default function DetailsErrorBoundary(
  props: RouteComponentProps<{ id: string }>
) {
  return (
    <ErrorBoundary>
      <Details {...props} />
    </ErrorBoundary>
  );
}

```
* For more examples see: [complete-intro-to-react-v5/src at typescript · btholt/complete-intro-to-react-v5 · GitHub](https://github.com/btholt/complete-intro-to-react-v5/tree/typescript/src)


## Redux
## Testing React