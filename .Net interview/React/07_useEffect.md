# 🚀 React Module 7 – Part 2 – useEffect

> **Difficulty:** ⭐⭐⭐⭐⭐
>
> **Interview Level:** Beginner → Mid → Senior React Developer
>
> **Key Concept:** `useEffect` is a React Hook used to perform **Side Effects** in Functional Components. It executes after the component renders and is commonly used for API calls, timers, event listeners, subscriptions, and DOM updates.

---

# 📚 Table of Contents

- 🎯 What is useEffect?
- 🎯 Why Do We Need useEffect?
- 🔹 What are Side Effects?
- 🔹 Syntax
- 🔹 Execution Flow
- 🔹 No Dependency Array
- 🔹 Empty Dependency Array (`[]`)
- 🔹 Dependency Array (`[dependency]`)
- 🔹 Multiple Dependencies
- 🔹 Cleanup Function
- 🔹 Component Lifecycle using useEffect
- 🔹 API Calls
- 🔹 Infinite Loop
- 📊 Dependency Array Comparison
- 🌍 Real World Example
- ✅ Best Practices
- 🎤 Common Interview Questions
- 📌 Summary
- 🎯 Interview One-Liner

---

# 🎯 What is useEffect?

`useEffect` is a **React Hook** used to perform **Side Effects** inside Functional Components.

A Side Effect is any operation that happens **outside the normal rendering process**.

Examples

- API Calls
- Database Calls
- Timers
- Event Listeners
- WebSocket Connections
- Updating Document Title
- Accessing Local Storage

---

# 🎯 Why Do We Need useEffect?

React Components should only focus on rendering UI.

Suppose we call an API directly inside the component.

```jsx
function Employee() {

    fetch("/api/employees");

    return <h1>Employees</h1>;

}
```

Problem

```
Component Render

↓

API Call

↓

Component Re-render

↓

API Call Again

↓

Multiple Requests
```

This causes unnecessary API calls.

React solves this using **useEffect**.

```jsx
useEffect(() => {

    fetch("/api/employees");

}, []);
```

Execution

```
Component Mounted

↓

API Called Once
```

---

# 🔹 What are Side Effects?

A Side Effect is any operation that affects something outside the component.

Examples

```
API Call

↓

Database Call

↓

Timer

↓

Event Listener

↓

Document Title

↓

Local Storage

↓

WebSocket
```

React recommends placing all side effects inside **useEffect**.

---

# 🔹 Syntax

```jsx
useEffect(() => {

    // Side Effect

}, [dependencies]);
```

Structure

```
useEffect(

Callback Function,

Dependency Array

)
```

---

# 🔹 Execution Flow

Whenever a component renders

```
Component Render

↓

React Updates DOM

↓

useEffect Executes
```

Important

`useEffect` always runs **after** the UI has been rendered.

---

# 🔹 No Dependency Array

```jsx
useEffect(() => {

    console.log("Executed");

});
```

Execution

```
Initial Render

↓

Runs

↓

State Changes

↓

Runs Again

↓

Props Change

↓

Runs Again
```

It runs after **every render**.

Usually **not recommended**.

---

# 💻 Example

```jsx
function Counter() {

    const [count, setCount] = useState(0);

    useEffect(() => {

        console.log("Component Rendered");

    });

    return (

        <button

            onClick={() => setCount(count + 1)}

        >

            {count}

        </button>

    );

}
```

Every click executes `useEffect`.

---

# 🔹 Empty Dependency Array ([])

```jsx
useEffect(() => {

    console.log("API Called");

}, []);
```

Execution

```
Component Mounted

↓

Runs Once

↓

Never Executes Again
```

Common Uses

- API Calls
- Initial Data Loading
- Initialization
- Event Registration

---

# 💻 Example

```jsx
useEffect(() => {

    fetchEmployees();

}, []);
```

API executes only once.

---

# 🔹 Dependency Array

```jsx
const [count, setCount] = useState(0);

useEffect(() => {

    console.log(count);

}, [count]);
```

Execution

```
Initial Render

↓

Runs

↓

count Changes

↓

Runs Again

↓

Other State Changes

↓

Does Not Execute
```

The effect runs only when **count** changes.

---

# 🔹 Multiple Dependencies

```jsx
useEffect(() => {

    console.log("Updated");

}, [count, name]);
```

Runs when

- count changes
- name changes

---

# 🔹 Cleanup Function

Some resources need cleanup.

Examples

- Timer
- Event Listener
- WebSocket
- Subscription

Syntax

```jsx
useEffect(() => {

    return () => {

        console.log("Cleanup");

    };

}, []);
```

Execution

```
Component Mounted

↓

Effect Executes

↓

Component Unmounted

↓

Cleanup Executes
```

---

# 💻 Timer Example

```jsx
useEffect(() => {

    const timer = setInterval(() => {

        console.log("Running");

    }, 1000);

    return () => {

        clearInterval(timer);

    };

}, []);
```

Without Cleanup

```
Timer Continues Running

↓

Memory Leak
```

---

# 💻 Event Listener Example

```jsx
useEffect(() => {

    window.addEventListener(

        "resize",

        handleResize

    );

    return () => {

        window.removeEventListener(

            "resize",

            handleResize

        );

    };

}, []);
```

Always remove event listeners.

---

# 🔹 Component Lifecycle using useEffect

## Class Component

```
constructor()

↓

render()

↓

componentDidMount()

↓

componentDidUpdate()

↓

componentWillUnmount()
```

---

## Functional Component

```
Render

↓

useEffect()

↓

Cleanup Function
```

`useEffect` replaces multiple lifecycle methods.

---

# 🔹 API Calls

Most common use case.

```jsx
useEffect(() => {

    async function loadEmployees() {

        const response = await fetch("/api/employees");

        const data = await response.json();

        console.log(data);

    }

    loadEmployees();

}, []);
```

Runs once when the component mounts.

---

# 🔹 Infinite Loop

❌ Wrong

```jsx
useEffect(() => {

    setCount(count + 1);

});
```

Execution

```
Render

↓

setCount()

↓

Render

↓

setCount()

↓

Infinite Loop
```

---

✔ Correct

```jsx
useEffect(() => {

    setCount(10);

}, []);
```

Runs only once.

---

# 📊 Dependency Array Comparison

| Dependency Array | Executes |
|------------------|----------|
| No Dependency Array | Every Render |
| [] | Only Once After Initial Render |
| [count] | Initial Render + Whenever count Changes |
| [count, name] | Initial Render + Whenever count or name Changes |

---

# 🌍 Real World Example

Employee Dashboard

```
Component Loads

↓

useEffect()

↓

Call Employee API

↓

Receive Data

↓

Update State

↓

Display Employees
```

---

# ✅ Best Practices

✔ Use `[]` for one-time initialization.

✔ Include every dependency used inside the effect.

✔ Clean up timers and event listeners.

✔ Avoid unnecessary effects.

✔ Keep effects focused on a single responsibility.

✔ Never update state repeatedly without proper dependencies.

---

# 🎤 Common Interview Questions

## What is useEffect?

A Hook used to perform Side Effects in Functional Components.

---

## What are Side Effects?

Operations like API calls, timers, event listeners, subscriptions, and DOM updates.

---

## When does useEffect execute?

After React renders the component and updates the DOM.

---

## Difference between

```jsx
useEffect(() => {})
```

and

```jsx
useEffect(() => {}, [])
```

Without dependency array

```
Runs After Every Render
```

With empty dependency array

```
Runs Only Once
```

---

## Why do we need a Cleanup Function?

To release resources like timers, event listeners, and subscriptions, preventing memory leaks.

---

## Can we call APIs inside useEffect?

Yes.

This is one of the most common use cases.

---

## Why does an Infinite Loop occur?

Because the effect updates state, which causes another render, triggering the effect again.

---

# 📌 Summary

| Scenario | Recommended |
|----------|-------------|
| API Call on Page Load | useEffect(..., []) |
| Execute After Every Render | useEffect(...) |
| Execute When State Changes | useEffect(..., [count]) |
| Execute When Multiple Values Change | useEffect(..., [count, name]) |
| Timer Cleanup | Cleanup Function |
| Event Listener Cleanup | Cleanup Function |
| Prevent Memory Leak | Cleanup Function |

---

# 🎯 Interview One-Liner

> **`useEffect` is a React Hook used to perform Side Effects in Functional Components. It executes after rendering, supports dependency-based execution, and provides a cleanup function for managing resources such as timers, event listeners, subscriptions, and API calls.**

---

# ⭐ Senior Interview Tip

One of the most frequently asked interview questions is:

## **What is the difference between these three useEffect examples?**

### 1️⃣ Runs after every render

```jsx
useEffect(() => {

    console.log("Runs Every Render");

});
```

---

### 2️⃣ Runs only once

```jsx
useEffect(() => {

    console.log("Runs Once");

}, []);
```

---

### 3️⃣ Runs when `count` changes

```jsx
useEffect(() => {

    console.log("Count Changed");

}, [count]);
```

### Answer

| Code | Behavior |
|------|----------|
| `useEffect(() => {})` | Runs after every render |
| `useEffect(() => {}, [])` | Runs only once after the initial render |
| `useEffect(() => {}, [count])` | Runs after the initial render and whenever `count` changes |

Understanding the dependency array is one of the most important aspects of using `useEffect` correctly in React.