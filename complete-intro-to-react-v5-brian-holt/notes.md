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
## Hooks
## Effects
## Dev Tools
## Async & Routing
## Class Components
## Error Boundaries
## Context
## Portals
## Wrapping Up