---
layout: post
category: technical
custom_js: mouse_coords
---

# Basic ReactJS Walkthrough

Here’s a **quick walkthrough of React** to get you started with the essentials of building React applications:

### 1. **What is React?**
React is a **JavaScript library** for building user interfaces, primarily focused on:
- **Component-based architecture**: UIs are built using independent, reusable pieces called components.
- **Declarative syntax**: React makes it easy to describe what your UI should look like based on your state and props.
- **Unidirectional data flow**: Data flows from parent to child components via props.

### 2. **Setting up a React Project**
You can create a React project easily using **Vite** or **Create React App** (CRA):

With Vite:
```bash
npm create vite@latest my-app --template react
cd my-app
npm install
npm run dev
```

With CRA:
```bash
npx create-react-app my-app
cd my-app
npm start
```

### 3. **Understanding JSX**
JSX is a syntax extension for JavaScript that looks similar to HTML but allows embedding JavaScript inside it.

**Example:**

```jsx
function Hello() {
  const name = 'World';
  return <h1>Hello, {name}!</h1>;
}
```

In the above example, the `{name}` is JavaScript inside JSX. This renders as:

```html
<h1>Hello, World!</h1>
```

### 4. **Components**
React is **component-based**, meaning everything in React is a component. A component can be a function or a class that returns JSX.

#### **Functional Components** (modern approach):
```jsx
function Greeting() {
  return <h1>Hello, React!</h1>;
}
```

#### **Class Components** (older approach):
```jsx
class Greeting extends React.Component {
  render() {
    return <h1>Hello, React!</h1>;
  }
}
```

#### Rendering a Component
To render a component, you use `ReactDOM.render()` or just return it in JSX.

```jsx
import React from 'react';
import ReactDOM from 'react-dom';
import Greeting from './Greeting';  // Import your component

ReactDOM.render(<Greeting />, document.getElementById('root'));
```

### 5. **Props (Properties)**
**Props** are inputs to components, used to pass data from parent to child components. They are read-only and help make components reusable.

**Example:**

```jsx
function Welcome(props) {
  return <h1>Hello, {props.name}!</h1>;
}

function App() {
  return <Welcome name="John" />;
}
```

This will render: `Hello, John!`

### 6. **State**
**State** is a way to store data that changes over time within a component. It allows React to track and re-render components when the state updates.

**Example Using `useState` Hook**:
```jsx
import { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);  // Declare state variable 'count'

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>Click me</button>
    </div>
  );
}
```

- `useState` returns a state variable and a function to update it.
- The component will re-render every time the state changes.

### 7. **Event Handling**
React handles events like clicks, form submissions, etc., using JavaScript functions. Events in React use camelCase instead of lowercase (e.g., `onClick` instead of `onclick`).

**Example of Event Handling:**

```jsx
function App() {
  function handleClick() {
    alert('Button clicked!');
  }

  return <button onClick={handleClick}>Click me</button>;
}
```

### 8. **Conditional Rendering**
You can conditionally render components or JSX based on a condition, usually using JavaScript expressions inside JSX.

**Example:**

```jsx
function UserGreeting() {
  return <h1>Welcome back!</h1>;
}

function GuestGreeting() {
  return <h1>Please sign up.</h1>;
}

function Greeting(props) {
  const isLoggedIn = props.isLoggedIn;
  return isLoggedIn ? <UserGreeting /> : <GuestGreeting />;
}
```

### 9. **Lists and Keys**
In React, you can render lists using `.map()` function. Each child component in a list should have a unique **key** prop to help React optimize rendering.

**Example:**

```jsx
function List() {
  const items = ['Item 1', 'Item 2', 'Item 3'];
  
  return (
    <ul>
      {items.map((item, index) => (
        <li key={index}>{item}</li>  // Always add a key
      ))}
    </ul>
  );
}
```

### 10. **Forms**
React controls forms using **controlled components**. The form input’s value is controlled via the state, and changes are handled with event handlers.

**Example:**

```jsx
import { useState } from 'react';

function Form() {
  const [input, setInput] = useState('');

  const handleChange = (event) => setInput(event.target.value);

  return (
    <form>
      <input type="text" value={input} onChange={handleChange} />
      <p>Your input: {input}</p>
    </form>
  );
}
```

### 11. **Hooks**
Hooks allow you to use state and other React features in functional components.

- **`useState`**: Manages state in a functional component.
- **`useEffect`**: Runs side effects (e.g., API calls, subscriptions).
- **`useContext`**: Allows sharing of data across components without prop drilling.

#### Example of `useEffect`:
```jsx
import { useState, useEffect } from 'react';

function Timer() {
  const [seconds, setSeconds] = useState(0);

  useEffect(() => {
    const interval = setInterval(() => {
      setSeconds(seconds => seconds + 1);
    }, 1000);
    
    return () => clearInterval(interval);  // Clean up
  }, []);  // Empty array means effect runs only once

  return <p>{seconds} seconds passed.</p>;
}
```

### 12. **React Router**
React Router is used to implement routing in your React applications.

1. Install it:
   ```bash
   npm install react-router-dom
   ```

2. Example of setting up routes:

```jsx
import { BrowserRouter as Router, Route, Routes, Link } from 'react-router-dom';

function Home() {
  return <h2>Home Page</h2>;
}

function About() {
  return <h2>About Page</h2>;
}

function App() {
  return (
    <Router>
      <nav>
        <Link to="/">Home</Link> | <Link to="/about">About</Link>
      </nav>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
      </Routes>
    </Router>
  );
}

export default App;
```

### 13. **Fetching Data (Using `useEffect`)**
Fetching data from APIs is usually done with `useEffect` and JavaScript’s `fetch()` function or a library like Axios.

```jsx
import { useState, useEffect } from 'react';

function DataFetching() {
  const [data, setData] = useState([]);

  useEffect(() => {
    fetch('https://jsonplaceholder.typicode.com/posts')
      .then(response => response.json())
      .then(data => setData(data));
  }, []);

  return (
    <ul>
      {data.map(item => (
        <li key={item.id}>{item.title}</li>
      ))}
    </ul>
  );
}
```

### 14. **React Dev Tools**
Use **React Developer Tools** in Chrome or Firefox to inspect the component tree and state of your React apps. This is a very useful debugging tool.

### 15. **State Management (Redux, Context API)**
For larger apps with complex state, consider using **Redux** or **Context API** for state management.

---

### Summary:
- React is component-based and uses JSX for templating.
- **Props** and **State** are core concepts.
- **Hooks** like `useState` and `useEffect` allow you to manage state and lifecycle events in functional components.
- Routing is handled by **React Router**.
- React’s declarative syntax and component-based architecture make it easy to build dynamic UIs.

Would you like to explore any specific area in more depth?