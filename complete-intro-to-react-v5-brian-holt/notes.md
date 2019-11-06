# Complete Intro to React - Brian Holt
## Intro

## Pure React
* React-dom library allows us to render react to the browser
* React native and React-dom share the react package, React package itself is actually quite small and just determines how to interact with react
* Module focuses on writing Pure React, using methods like `React.createElement()` to create elements to render to the browser (3 args: elem/component, attributes/props, children/text)
* Pros - Easy to build out UI. We can reuse and and nest components.
* Example of Pure React Components:
```js
const App = () => {
  return React.createElement("div", {}, [
    React.createElement("h1", {}, "Adopt Me!"),
    React.createElement(Pet, {
      name: "Luna",
      animal: "Dog",
      breed: "Havenese"
    }),
    React.createElement(Pet, {
      name: "Pepper",
      animal: "Bird",
      breed: "Cockatiel"
    }),
    React.createElement(Pet, {
      name: "Doink",
      animal: "Cat",
      breed: "Mixed"
    })
  ]);
};

```

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
* When you have JSX, you need to import react, because under the hood JSX transpile to `React.createElement()`
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
### `NODE_ENV=development` vs `NODE_ENV=production`
* if using parcel, it sets it for us
* important to set your env variables appropriately because when developing we want all debugging tools ( e.g. warnings, messages), when our project is in production - we want a stripped down version to improved performance
### Strict Mode
* goes over `<React.StrictMode></React.StrictMode>` 
* when wrapped in this, react will give you additional warnings on deprecated react things

## Async & Routing
* Brian provides fallback API so you can work without internet or if the API used, has issues - see _Using the Fallback Mock API_
### Async
* See async example. In this example we want to get the pets that match the dropdown values and set our pets state with the matching lets pets:
```js
// in <SearchParams />
// async function will return a promise
// allows for await keyword to be used inside func
// will wait until the data is return, from their destructure animals off obj
async function requestPets() {
  const { animals } = await pet.animals({
    location,
    breed,
    type: animal
  });

  setPets(animals || []);
}
```

### Routing
* Uses Reach Router instead of React Router
* Reach Router is more accessible than React Router. React Router will rely more on devs to make it accessible
* See: [React Training: The Future of React Router and @reach/router](https://reacttraining.com/blog/reach-react-router-future/). For other reasons why you would choose Reach Router and vice versa.

## Class Components
* goes over class components
* shows how to configure babel to support classProperties e.g. (`state = {loading: true}`)
* introduces `static getDerivedStateFromProps` and uses it to massage media props (sets a default and update the child component’s photos state with large photos from media prop). See:

```js
import React from "react";

class Carousel extends React.Component {
  state = {
    photos: [],
    active: 0
  };

  static getDerivedStateFromProps({ media }) {
    let photos = ["http://placecorgi.com/600/500"];
    if (media.length) {
      photos = media.map(({ large }) => large);
    }

    return { photos };
  }

  render() {
    const { photos, active } = this.state;

    return (
      <div className="carousel">
        <img src={photos[active]} alt="animal" />
        <div className="carousel-smaller">
          {photos.map((photo, index) => (
            // eslint-disable-next-line
            <img
              key={photo}
              onClick={this.handleIndexClick}
              data-index={index}
              src={photo}
              className={index === active ? "active" : ""}
              alt="animal thumbnail"
            />
          ))}
        </div>
      </div>
    );
  }
}

export default Carousel;

```
## Error Boundaries
* Prevents entire UI from crashing when there is an error. It’s able to catch JavaScript errors in child component, log the error and display feedback to user.
* See [Error Boundaries – React](https://reactjs.org/docs/error-boundaries.html#introducing-error-boundaries)
* Example:
```js
// ErrorBoundary.js
import React, { Component } from "react";
import { Link, Redirect } from "@reach/router";

class ErrorBoundary extends Component {
  state = { hasError: false, redirect: false };

  // like a lifecycle method, will get called when there's an error
  // since it gets called - we know there was an error and set hasError to true
  static getDerivedStateFromError() {
    return { hasError: true };
  }
  componentDidCatch(error, info) {
    console.error("ErrorBoundary caught an error", error, info);
  }

  componentDidUpdate() {
    if (this.state.hasError) {
      setTimeout(() => this.setState({ redirect: true }), 5000);
    }
  }
  render() {
    if (this.state.redirect) {
      return <Redirect to="/" />;
    }
    if (this.state.hasError) {
      return (
        <h1>
          There was an error with this listing. <Link to="/"> Click here</Link>{" "}
          to go back to the home page or wait five seconds
        </h1>
      );
    }

    return this.props.children;
  }
}

export default ErrorBoundary;

```
* Cannot do error boundaries with hooks, must use class components (there is hook way of `static getDerivedStateFromError()`)
* In the example below we want to wrap Details in the error boundary in case there’s an error coming from the API. We can’t just wrap everything in `<ErrorBoundary></ErrorBoundary>` because it can only catch the error in child components, but not within the class itself. To fix this, he turns `<ErrorBoundary></ErrorBoundary>` into a higher order component above details. Not sure if this is the best way? See the way it is exported at the bottom:
```js
// Details.js
import React from "react";
import pet from "@frontendmasters/pet";
import Carousel from "./Carousel";
import ErrorBoundary from "./ErrorBoundary";

class Details extends React.Component {
  state = { loading: true };

  componentDidMount() {
    // uncomment next line to trigger <ErrorBoundary>
    // throw new Error("hi");
    pet.animal(this.props.id).then(({ animal }) => {
      this.setState({
        name: animal.name,
        animal: animal.type,
        location: `${animal.contact.address.city}, ${animal.contact.address.state}`,
        description: animal.description,
        media: animal.photos,
        breed: animal.breeds.primary,
        loading: false
      });
    }, console.error);
  }

  render() {
    if (this.state.loading) {
      return <h1>loading...</h1>;
    }
    const { animal, breed, location, description, name, media } = this.state;
    return (
      <div className="details">
        <Carousel media={media} />
        <div>
          <h1>{name}</h1>
          <h2>{`${animal} - ${breed} - ${location}`}</h2>
          <button>Adopt {name}</button>
          <p>{description}</p>
        </div>
      </div>
    );
  }
}

export default function DetailsWithErrorBoundary(props) {
  return (
    <ErrorBoundary>
      <Details {...props} />
    </ErrorBoundary>
  );
}

```
## Context
* React API, useful for when you need global application state (e.g user login info)
* Alternative to prop drilling/Redux
* Only use it when you have to
* See example:
```js
// ThemeContext.js
import { createContext } from "react";

// here we will stick a hook - but anything can go into createContext
// e.g. obj, func, string, etc
// hook syntax reminder: [state, updater] (here the updater doesn't do anything
// so it will be an empty function
const ThemeContext = createContext(["mediumaquamarine", () => {}]);

export default ThemeContext;

```
```js
// App.js
import React, { useState } from "react";
import { render } from "react-dom";
import { Router, Link } from "@reach/router";
import SearchParams from "./SearchParams";
import Details from "./Details";
import ThemeContext from "./ThemeContext";

const App = () => {
  // themeHook is referring to the whole array ["mediumaquamarine", () => {}]
  const themeHook = useState("darkblue");
  return (
    <ThemeContext.Provider value={themeHook}>
      <div>
        <header>
          <Link to="/">Adopt Me!</Link>
        </header>
        <Router>
          <SearchParams path="/" />
          <Details path="/details/:id" />
        </Router>
      </div>
    </ThemeContext.Provider>
  );
};

render(React.createElement(App), document.getElementById("root"));

```
```js
// SearchParams.js (hooks)
import React, { useState, useEffect, useContext } from "react";
import pet, { ANIMALS } from "@frontendmasters/pet";
import Results from "./Results";
// import custom hook
import useDropdown from "./useDropdown";
import ThemeContext from "./ThemeContext";

const SearchParams = () => {
  const [location, setLocation] = useState("Seattle, WA");
  const [breeds, setBreeds] = useState([]);
  const [animal, AnimalDropdown] = useDropdown("Animal", "dog", ANIMALS);
  const [breed, BreedDropdown, setBreed] = useDropdown("Breed", "", breeds);
  const [pets, setPets] = useState([]);
  const [theme] = useContext(ThemeContext);

  async function requestPets() {
    const { animals } = await pet.animals({
      location,
      breed,
      type: animal
    });

    setPets(animals || []);
  }

  useEffect(() => {
    setBreeds([]);
    setBreed("");

    pet.breeds(animal).then(({ breeds }) => {
      const breedStrings = breeds.map(({ name }) => name);
      setBreeds(breedStrings);
    }, console.error);
  }, [animal, setBreed, setBreeds]);
  return (
    <div className="search-params">
      <form
        onSubmit={e => {
          e.preventDefault();
          requestPets();
        }}
      >
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
        <button style={{ backgroundColor: theme }}>Submit</button>
      </form>
      <Results pets={pets} />
    </div>
  );
};

export default SearchParams;

```
```js
// Details.js (class)
...
import ThemeContext from "./ThemeContext";

class Details extends React.Component {
  state = { loading: true };

  componentDidMount() {
    ...
  }

  render() {
    if (this.state.loading) {
      return <h1>loading...</h1>;
    }
    const { animal, breed, location, description, name, media } = this.state;
    return (
      <div className="details">
        <Carousel media={media} />
        <div>
          <h1>{name}</h1>
          <h2>{`${animal} - ${breed} - ${location}`}</h2>
          // See below
          // uses Consumer
          // returns component (any function that returns JSX is component in React)
          <ThemeContext.Consumer>
            {themeHook => (
              <button style={{ backgroundColor: themeHook[0] }}>
                Adopt {name}
              </button>
            )}
          </ThemeContext.Consumer>
          <p>{description}</p>
        </div>
      </div>
    );
  }
}

export default function DetailsWithErrorBoundary(props) {
  return (
    <ErrorBoundary>
      <Details {...props} />
    </ErrorBoundary>
  );
}

```
* Example of updating and persisting data using context:
```js
// Search params
...
import ThemeContext from "./ThemeContext";

const SearchParams = () => {
  ...
  const [theme, setTheme] = useContext(ThemeContext);

  async function requestPets() {
    const { animals } = await pet.animals({
      location,
      breed,
      type: animal
    });

    setPets(animals || []);
  }

  useEffect(() => {
    setBreeds([]);
    setBreed("");

    pet.breeds(animal).then(({ breeds }) => {
      const breedStrings = breeds.map(({ name }) => name);
      setBreeds(breedStrings);
    }, console.error);
  }, [animal, setBreed, setBreeds]);
  return (
    <div className="search-params">
      <form
        onSubmit={e => {
          e.preventDefault();
          requestPets();
        }}
      >
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
        // See below, setTheme is added above
        <label htmlFor="theme">
          Theme
          <select
            value={theme}
            onChange={e => setTheme(e.target.value)}
            onBlur={e => setTheme(e.target.value)}
          >
            <option value="mediumaquamarine">mediumaquamarine</option>
            <option value="darkblue">darkblue</option>
            <option value="tomato">tomato</option>
          </select>
        </label>
        <button style={{ backgroundColor: theme }}>Submit</button>
      </form>
      <Results pets={pets} />
    </div>
  );
};

export default SearchParams;

```
* If we were just using a regular hook, once we navigate away from SearchParams, the component would unmount, destroying the hook and we would lose the current state


## Portals
## Wrapping Up