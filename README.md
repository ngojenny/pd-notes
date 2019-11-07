# Goals and Curriculum
## Goals
### General Goals
*November 1, 2019*
1) Learn new features of React (Hooks, Effects, Context API) and feel more confident putting them into practice
2) Have a foundational knowledge of testing
3) Be able to explain the difference between various types of testing (Static, Unit, Integration, Snapshot, etc)
4) Be more confident in more Advanced JavaScript concepts

### Specific Course Goals
*November 1, 2019*
1) [Complete Intro to React - Brian Holt](https://frontendmasters.com/courses/complete-react-v5/)
	- Be able to explain Hooks and provide example ✅
	- Be able to explain effects and provide example ✅
	- Be able to explain context and provide example ✅
	- Be able to explain portals and provide example ✅

**Key Learnings**
	- Hooks are functions that allow us to interact with state and lifecycle methods. This also means there will be less of a need to use class components. There are still a few things hooks cannot do at this moment (e.g. Error Boundaries). Brian goes over the state hook - `useState()`, used to set default state and update state. We can also make custom hooks, which allows us to extract component logic into reusable function. A an example of a potential custom hook would be a [dropdown](https://github.com/ngojenny/pd-notes/blob/master/complete-intro-to-react-v5-brian-holt/notes.md#hooks).
	- Effects replaces many of the lifecycle methods. The react docs say it lets us peform a side effect. The effect hook is `useEffect()`. This method takes a callback function and an array of dependencies that determines when the hook will run. To let it run only once we can leave the array blank. Another feature of `useEffect()` is any function returned, is the clean up function. The use case provided by the course is [calling an API when a piece of state changes](https://github.com/ngojenny/pd-notes/blob/master/complete-intro-to-react-v5-brian-holt/notes.md#effects).
	- Context is a React API that allows us to access global application state. It can be an alternative to prop drilling or Redux. We should only use context when we really need it - when a piece of state is need by many components (e.g. login info). [In the course Brian goes over how to use context to create a theming feature](https://github.com/ngojenny/pd-notes/blob/master/complete-intro-to-react-v5-brian-holt/notes.md#context).
	Portals allow us to allows us to render components outside the current component, while keeping its events and states in the current component. Example provided was creating a modal outside of the root div.


2) [Intermediate React - Brian Holt](https://frontendmasters.com/courses/intermediate-react-v2/)
	- Be able to explain the 3 basic hooks covered and determine the difference between them - `useState`, `useEffect`, `useContext`
	- Be able to explain code splitting
	- Be able to explain server-side rendering
	- Be able to add TypeScript to a project
	- Write a test for a component

3) [Deep JavaScript Foundations  - Kyle Simpson](https://frontendmasters.com/courses/deep-javascript-v3/)
	- Write reflection for Static Typing Module
	- Write reflection for Scope Module
	- Write reflection for Scope & Function Expressions Module
	- Write reflection for Advanced Scope Module
	- Write reflection for Closure Module
	- Write reflection for Objects Module
	- Write reflection for Prototype Module
4) [JavaScript Testing Practices and Principles - Kent C Dodds](https://frontendmasters.com/courses/testing-practices-principles/)
	- Be able to explain Static Testing
	- Be able to explain Unit Testing
	- Be able to explain Integration Testing
	- Write a list of Best practices learned from course