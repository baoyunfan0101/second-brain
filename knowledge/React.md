---
tags:
  - frontend
  - javascript
  - react
summary: components, hooks, state/props
use_cases:
  - ui
  - web
---

# React

## Overview

React is a UI library developed by Meta Platforms for building interactive user interfaces.

## Core Concepts

1. **Component**

UI is split into independent reusable pieces.

```jsx
function Button() {
  return <button>Click</button>;
}
```

Page = Component Tree

2. **Declarative**

Describe what UI should look like, not how to update DOM.

```jsx
// React
return <div>{count}</div>;

// Vanilla JS
document.getElementById("app").innerText = count;
```

3. **One-way Data Flow**

Data flows from parent to child.

```jsx
function Parent() {
  return <Child value={1} />;
}
```

## Core Mechanisms

1. **Virtual DOM**

- UI represented as JS objects
- Diff algorithm finds minimal changes
- Updates real DOM efficiently

2. **Hooks**

Used in function components to manage state and lifecycle.

**Common Hooks:**
- useState: state management
- useEffect: side effects
- useRef: DOM/variable reference
- useMemo: memoized value
- useCallback: memoized function

**Example:**
```jsx
import { useState } from "react";

function App() {
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount(count + 1)}>
      {count}
    </button>
  );
}
```

3. **Lifecycle (Function Component)**

```jsx
// run once
useEffect(() => {
  // mount

  return () => {
    // unmount
  };
}, []);

// run on every render
useEffect(() => {
  // executed after every render
});

// run when dependencies change
useEffect(() => {
  // executed when `count` changes
}, [count]);

// multiple dependencies
useEffect(() => {
  // executed when any dependency changes
}, [a, b]);

// cleanup before next run or unmount
useEffect(() => {
  // effect logic

  return () => {
    // cleanup (before next effect or unmount)
  };
}, [dep]);
```

## JSX

JS + HTML-like syntax

```jsx
const el = <h1>Hello</h1>;
```

**Equivalent:**
```js
React.createElement("h1", null, "Hello");
```

## State vs. Props

| Type  | Description              |
|-------|--------------------------|
| state | internal, mutable        |
| props | external, read-only      |

```jsx
import React, { useState } from "react";

// Child component (receives props)
function Counter({ value }) {
  return <h1>{value}</h1>;
}

// Parent component (manages state)
export default function App() {
  const [count, setCount] = useState(0); // state

  return (
    <>
      {/* pass state as props */}
      <Counter value={count} />

      <button onClick={() => setCount(count + 1)}>
        +1
      </button>
    </>
  );
}
```

## Minimal Example

**Function Component**

```jsx
import React, { useState } from "react";

export default function App() {
  const [count, setCount] = useState(0);

  return (
    <>
      <h1>{count}</h1>
      <button onClick={() => setCount(count + 1)}>
        +1
      </button>
    </>
  );
}
```

**Class Component**

```jsx
import React from "react";

export default class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0
    };
  }

  render() {
    return (
      <>
        <h1>{this.state.count}</h1>
        <button onClick={() => this.setState({ count: this.state.count + 1 })}>
          +1
        </button>
      </>
    );
  }
}
```