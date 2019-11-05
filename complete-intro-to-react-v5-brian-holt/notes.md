# Complete Intro to React - Brian Holt
## Intro

## Pure React
* React- dom library allows us to render react to the browser
* React native and React-dom share the react package, React package itself is actually quite small and just determines how to interact with react
* Module focuses on writing Pure React, using methods like `React.createElement()` to create elements to render to the browser (3 args: elem/component, attributes/props, children/text)
* Pros - Easy to build out UI. We can reuse and and nest component.
* Example of Pure React Components

## Tools
* Goes over tooling for project
* Setting up prettier (formating), eslint (code style - e.g. what methods are used? Unused variables )
* Mentions -D flag `npm install -D <package>` means this package you only need for your local environment. Use for packages you will not need to ship along with the rest of your project when in production  (e.g. prettier and eslint are appropriate packages for the `-D`)
* Mentions `package.lock` file - locks down the versions of your dependencies/packages. This can be used to ensure your project in production uses the same versions of things as your local version
* `npm ci` uses your package-lock file instead of your package.json to install dependencies
* uses Parcel to bundle our project
* `import { render } from "react-dom"` technically isn't destructuring, it's just how modules work - `react-dom` has a specific export called `render` and we just want to use that
* *tree-shaking*: a way to reduce the size of your project, it involves removing code across your modules, that won’t be used [Introduction to Tree Shaking - Better Programming - Medium](https://medium.com/better-programming/introduction-to-tree-shaking-e94e57db081e)
* *VS-Code trick* you quickly move a component to a new file, by highlighting the block of code, click on the light bulb that appears and clicking move to new file

## JSX
* allows us to write HTML markup, instead of creating our elements using `React.createElement()`
* When you have JSX, you need to import react, because under the hood JSX transpire to `React.createElement()`
* `npm install -D babel-eslint eslint-plugin-import eslint-plugin-jsx-a11y eslint-plugin-react` in “Configuring ESLint for React”
* Expressions (anything on the right side of an assignment) needs to be wrapped in curly braces in JSX

## Hooks
* Looked specifically at the `useState`  hook. 
* Functions that let us interact with the React state and lifecycle features
* Since components re-render every time it changes, these hooks shouldn’t be doing too much - they should focus on rendering (e.g. shouldn’t update functions, shouldn’t have too many side effects)
* they all start with `use`
* example:
```js
// useDropdown.js
import React, { useState } from "react";

// takes 3 arg a label, the default state and options (e.g. array of animals for the dropdown list)
const useDropdown = (label, defaultState, options) => {
  // initial state, defined by defaultState arg, generic updater
  const [state, setState] = useState(defaultState);
  // massage the label
  const id = `use-dropdown-${label.replace(" ", "").toLowerCase()}`;
  // return the jsx
  const Dropdown = () => (
    <label htmlFor={id}>
      {label}
      <select
        id={id}
        value={state}
        onChange={e => setState(e.target.value)}
        onBlur={e => setState(e.target.value)}
        disabled={options.length === 0}
      >
        <option>All</option>
        {options.map(item => (
          <option key={item} value={item}>
            {item}
          </option>
        ))}
      </select>
    </label>
  );
  // useDropdown returns an array of 3 things
  // state, the jsx/component, the updater (just in case)
  return [state, Dropdown, setState];
};

export default useDropdown;

```
* should not stick hooks in if statements, for loops, conditional, any unpredictable logic
* React keeps track of your pieces of state based on the order you call them
### Custom Hooks
* Creating custom hooks allows you to extract component logic into reusable functions
* Example of dropdown logic extracted into a custom hook:
```js
// Custom Hook (reusable dropdown)
import React, { useState } from "react";

const useDropdown = (label, defaultState, options) => {
  const [state, setState] = useState(defaultState);
  const id = `use-dropdown-${label.replace(" ", "").toLowerCase()}`;
  const Dropdown = () => (
    <label htmlFor={id}>
      {label}
      <select
        id={id}
        value={state}
        onChange={e => setState(e.target.value)}
        onBlur={e => setState(e.target.value)}
        disabled={options.length === 0}
      >
        <option>All</option>
        {options.map(item => (
          <option key={item} value={item}>
            {item}
          </option>
        ))}
      </select>
    </label>
  );
  return [state, Dropdown, setState];
};

export default useDropdown;

```

This is how it is referenced, via `<AnimalDropdown />` and `<BreedDropdown />` :

```js
// SearchParams.js
// parent component of instance of useDropdown hook
import React, { useState } from "react";
import { ANIMALS } from "@frontendmasters/pet";
// import custom hook
import useDropdown from "./useDropdown";

const SearchParams = () => {
  // useState creates a hook returns an array of two things
  // (which we've destructured)
  // - first is the current state, second is an updater
  const [location, setLocation] = useState("Seattle, WA");
  const [breeds, setBreeds] = useState([]);
  // Leftside of statement - useDropdown returns:
  // [state, Dropdown (component), setState]
  // Rightside of statement - passing label, defaultState and options
  const [animal, AnimalDropdown] = useDropdown("Animal", "dog", ANIMALS);
  const [breed, BreedDropdown] = useDropdown("Breed", "", breeds);

  return (
    <div className="search-params">
      <form>
        <label htmlFor="location">
          <input
            id="location"
            value={location}
            placeholder="Location"
            onChange={e => setLocation(e.target.value)}
          />
        </label>
        <AnimalDropdown />
        <BreedDropdown />
        <button>Submit</button>
      </form>
    </div>
  );
};

export default SearchParams;

```

## Effects
* replaces many of the lifecycle methods
* method that takes in a callback and an array of dependencies
* if you only want it run once, leave the array of dependencies empty
* Example of `useEffect`. In this example, we only want to make a call to the Pet API when the `animal` state changes:
```js
const SearchParams = () => {
  const [location, setLocation] = useState("Seattle, WA");
  const [breeds, setBreeds] = useState([]);
  const [animal, AnimalDropdown] = useDropdown("Animal", "dog", ANIMALS);
  const [breed, BreedDropdown, setBreed] = useDropdown("Breed", "", breeds);

  // useEffect initially runs AFTER the first render
  // second argument is an array of "dependencies"
  // useEffect will only run if one of these 3 things change,
  // without this, useEffect will constantly run (runs after each render by default)
  useEffect(() => {
    // we want to reset the breed and breeds state when it runs
    // *note about setBreed and setBreeds being part of the dependency array
    // including these two methods means useEffect will run if they get re-assigned
    // it's unlikely that they will be reassigned, however this is what the creators
    // of react wants us to do - if you do not include them, you will get an eslint react hook error/warning
    setBreeds([]);
    setBreed("");

    pet.breeds(animal).then(({ breeds }) => {
      const breedStrings = breeds.map(({ name }) => name);
      setBreeds(breedStrings);
    }, console.error);
  }, [animal, setBreed, setBreeds]);
  return (
    <div className="search-params">
      <form>
        <label htmlFor="location">
          <input
            id="location"
            value={location}
            placeholder="Location"
            onChange={e => setLocation(e.target.value)}
          />
        </label>
        <AnimalDropdown />
        <BreedDropdown />
        <button>Submit</button>
      </form>
    </div>
  );
};
```

## Dev Tools
## Async & Routing
## Class Components
## Error Boundaries
## Context
## Portals
## Wrapping Up