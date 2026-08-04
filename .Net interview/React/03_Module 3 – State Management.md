# 🚀 React Module 3 – State Management (useState)

> **Difficulty:** ⭐⭐⭐⭐⭐
>
> **Interview Level:** Beginner → Mid → Senior React Developer
>
> **Key Concept:** State is mutable data managed by a React component. Whenever the state changes, React automatically re-renders the component and updates the UI efficiently using the Virtual DOM.

---

# 📚 Table of Contents

- 🎯 What is State?
- 🎯 Why Do We Need State?
- 🔹 What is useState()?
- 🔹 How useState() Works
- 🔹 State Update Process
- 🔹 Updating State
- 🔹 Functional Updates
- 🔹 State Batching
- 🔹 Updating Objects
- 🔹 Updating Arrays
- 🔹 Controlled Components
- 🔹 Uncontrolled Components
- 🔹 Lifting State Up
- 📊 Props vs State
- 📊 Controlled vs Uncontrolled Components
- 🎤 Common Interview Questions
- 📌 Summary
- 🎯 Interview One-Liner

---

# 🎯 What is State?

State is **mutable data** that belongs to a React component.

Whenever the state changes, React automatically **re-renders** the component and updates the UI.

Think of State as the **memory of a component**.

Example

```
Counter

Count = 0

↓

User Clicks Button

↓

Count = 1

↓

Count = 2
```

The component remembers the value because it is stored in **State**.

---

# 🎯 Why Do We Need State?

Suppose we use a normal variable.

```jsx
function Counter() {

    let count = 0;

    return (
        <>
            <h2>{count}</h2>

            <button onClick={() => count++}>
                Increment
            </button>
        </>
    );
}
```

Will the UI update?

❌ No.

Changing a normal JavaScript variable does **not** trigger a React re-render.

---

# React Solution

```jsx
const [count, setCount] = useState(0);
```

Now React knows

```
State Changed

↓

Component Re-render

↓

Virtual DOM Updated

↓

Real DOM Updated
```

---

# 🔹 What is useState()?

`useState()` is a **React Hook** that allows Functional Components to maintain state.

Syntax

```jsx
const [state, setState] = useState(initialValue);
```

Example

```jsx
const [count, setCount] = useState(0);
```

Where

- `count` → Current State
- `setCount()` → Updates State
- `0` → Initial Value

---

# 🔹 How useState() Works

Initially

```jsx
const [count, setCount] = useState(0);
```

React stores

```
count = 0
```

User clicks button

```jsx
setCount(1);
```

React performs

```
Old State

↓

0

↓

New State

↓

1

↓

Component Re-renders

↓

UI Updates
```

---

# 💻 Example

```jsx
import { useState } from "react";

function Counter() {

    const [count, setCount] = useState(0);

    return (
        <>
            <h2>{count}</h2>

            <button
                onClick={() => setCount(count + 1)}
            >
                Increment
            </button>
        </>
    );
}
```

Output

```
0

↓

1

↓

2

↓

3
```

---

# 🔹 State Update Process

```
User Click

↓

setState()

↓

State Updated

↓

Component Re-render

↓

Virtual DOM

↓

Diffing

↓

Reconciliation

↓

Real DOM Updated
```

---

# 🔹 Updating State

Never modify state directly.

❌ Wrong

```jsx
count = count + 1;
```

or

```jsx
state.name = "John";
```

✔ Correct

```jsx
setCount(count + 1);
```

Always use the setter function.

---

# 🔹 Functional Updates

Suppose

```jsx
setCount(count + 1);

setCount(count + 1);
```

Expected

```
0

↓

2
```

Actual

```
0

↓

1
```

Because both updates use the same previous value.

Correct

```jsx
setCount(prev => prev + 1);

setCount(prev => prev + 1);
```

Output

```
0

↓

2
```

Use functional updates whenever the next state depends on the previous state.

---

# 🔹 State Batching

React groups multiple state updates into a single render.

```jsx
setCount(c => c + 1);

setCount(c => c + 1);

setCount(c => c + 1);
```

Result

```
0

↓

3
```

Instead of

```
0

↓

1

↓

2

↓

3
```

This improves application performance.

---

# 🔹 Updating Objects

Initial State

```jsx
const [employee, setEmployee] = useState({

    name: "Rakesh",

    age: 30

});
```

❌ Wrong

```jsx
employee.age = 31;
```

✔ Correct

```jsx
setEmployee({

    ...employee,

    age: 31

});
```

Always create a new object.

---

# 🔹 Updating Arrays

Initial State

```jsx
const [employees, setEmployees] = useState([]);
```

❌ Wrong

```jsx
employees.push(newEmployee);
```

✔ Correct

```jsx
setEmployees([

    ...employees,

    newEmployee

]);
```

Never mutate arrays directly.

---

# 🔹 Controlled Components

A Controlled Component is managed by React State.

```jsx
const [name, setName] = useState("");

<input

value={name}

onChange={(e) => setName(e.target.value)}
/>
```

React is the source of truth.

---

# 🔹 Uncontrolled Components

The DOM manages the value.

```jsx
const inputRef = useRef();

<input ref={inputRef} />
```

React reads the value only when needed.

---

# 📊 Controlled vs Uncontrolled Components

| Controlled | Uncontrolled |
|------------|--------------|
| React Controls Input | DOM Controls Input |
| Uses State | Uses Ref |
| Easy Validation | Less Validation |
| Preferred | Rarely Used |

---

# 🔹 Lifting State Up

Suppose

```
Parent

│

├── Child A

└── Child B
```

Both children need the same data.

Instead of storing state inside Child A,

move the state to the Parent.

```
Parent

(State)

│

├── Child A

└── Child B
```

Parent passes the state to both children using Props.

---

# 📊 Props vs State

| Props | State |
|--------|-------|
| Read-only | Mutable |
| Passed by Parent | Managed by Component |
| External Data | Internal Data |
| Cannot Modify | Can Update |
| Parent Controls | Component Controls |

---

# 🌍 Real-World Example

Employee Search Screen

```
Employee Page

│

├── Search Box

├── Employee List

└── Employee Details
```

Search text is stored in State.

Whenever the user types

```
Input Changed

↓

State Updated

↓

Employee List Re-rendered
```

---

# ✅ Best Practices

✔ Keep State as small as possible.

✔ Never modify State directly.

✔ Use the setter function.

✔ Use functional updates when the new value depends on the previous value.

✔ Store only UI-related data in State.

✔ Lift State Up when multiple components need the same data.

---

# 🎤 Common Interview Questions

## What is State?

State is mutable data managed by a React component.

---

## What is useState?

A Hook that allows Functional Components to maintain state.

---

## Why can't we modify State directly?

Because React detects changes only when the setter function is used.

---

## Why use Functional Updates?

To ensure updates are based on the latest state value.

---

## What is State Batching?

React combines multiple state updates into a single render for better performance.

---

## What is Lifting State Up?

Moving state to the nearest common parent so multiple child components can share it.

---

## Difference between Props and State?

Props are passed by Parent.

State belongs to the component.

---

## Difference between Controlled and Uncontrolled Components?

Controlled Components use React State.

Uncontrolled Components use the DOM.

---

# 📌 Summary

| Scenario | Recommended |
|----------|-------------|
| Store Component Data | State |
| Update UI Dynamically | useState |
| Previous State Required | Functional Update |
| Multiple Updates | State Batching |
| Form Handling | Controlled Component |
| Quick DOM Access | Uncontrolled Component |
| Share State Between Children | Lift State Up |
| Parent → Child Data | Props |

---

# 🎯 Interview One-Liner

> **State is mutable data owned by a React component. The `useState` Hook allows Functional Components to manage state. Whenever state changes using the setter function, React automatically re-renders the component and updates only the changed parts of the UI using the Virtual DOM.**

---

# ⭐ Senior Interview Tip

One of the most common interview questions is:

### **"Why doesn't `count++` update the UI?"**

**Answer:**

`count++` only modifies a local JavaScript variable.

React does not track local variables.

React tracks only **State**.

When we call

```jsx
setCount(count + 1);
```

React schedules a re-render, creates a new Virtual DOM, compares it with the previous Virtual DOM, and updates only the changed UI efficiently.