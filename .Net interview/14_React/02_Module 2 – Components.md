# 🚀 React Module 2 – Components

> **Difficulty:** ⭐⭐⭐⭐⭐
>
> **Interview Level:** Beginner → Mid → Senior React Developer
>
> **Key Concept:** Components are the building blocks of a React application. React applications are created by combining multiple reusable and independent components.

---

# 📚 Table of Contents

- 🎯 What is a Component?
- 🎯 Why Components?
- 🔹 Types of Components
- 🔹 Functional Components
- 🔹 Class Components
- 🔹 Props
- 🔹 Props vs State
- 🔹 Component Communication
- 🔹 Props Drilling
- 🔹 Component Composition
- 🔹 Component Lifecycle
- 🔹 Component Re-rendering
- 📊 Functional vs Class Components
- 📊 Props vs State
- 🎤 Common Interview Questions
- 📌 Summary
- 🎯 Interview One-Liner

---

# 🎯 What is a Component?

A **Component** is a reusable piece of UI.

Instead of building an entire page, React divides the UI into smaller reusable components.

Example

```
Application

│

├── Header

├── Navbar

├── Sidebar

├── Dashboard

│      │

│      ├── Employee Card

│      ├── Product Card

│      └── Customer Card

└── Footer
```

Each block is called a **Component**.

---

# 🎯 Why Components?

Without Components

```
One HTML File

↓

5000 Lines

↓

Hard to Maintain

↓

Hard to Debug
```

With Components

```
Application

↓

Header

↓

Sidebar

↓

Dashboard

↓

Footer
```

Each component is independent and reusable.

---

# ✅ Benefits of Components

- Reusable
- Independent
- Easy to Test
- Easy to Maintain
- Better Code Organization

---

# 🔹 Types of Components

React provides two types of components.

```
Components

│

├── Functional Component

└── Class Component
```

Modern React applications primarily use **Functional Components**.

---

# 🔹 Functional Component

A Functional Component is simply a JavaScript function that returns JSX.

## Example

```jsx
function Welcome() {
    return <h1>Hello React</h1>;
}
```

OR

```jsx
const Welcome = () => {
    return <h1>Hello React</h1>;
};
```

Usage

```jsx
<Welcome />
```

Output

```
Hello React
```

---

# Why Functional Components?

They are

- Easy to write
- Easy to understand
- Support Hooks
- Less Boilerplate
- Preferred in Modern React

---

# 🔹 Class Component

Before React Hooks, Class Components were used.

## Example

```jsx
import React, { Component } from "react";

class Welcome extends Component {

    render() {
        return <h1>Hello React</h1>;
    }

}

export default Welcome;
```

Usage

```jsx
<Welcome />
```

---

# Why are Class Components Less Popular?

They require

- Constructor
- render()
- this keyword
- Lifecycle Methods

More code compared to Functional Components.

---

# 📊 Functional vs Class Components

| Feature | Functional | Class |
|----------|------------|--------|
| Syntax | Simple | Complex |
| State | useState | this.state |
| Lifecycle | useEffect | Lifecycle Methods |
| Hooks | ✅ Yes | ❌ No |
| Boilerplate | Less | More |
| Preferred | ✅ Yes | ❌ No |

---

# 🔹 What are Props?

Props (Properties) are **read-only values** passed from a Parent Component to a Child Component.

Think of Props as **function parameters**.

Parent

```jsx
<Welcome name="Rakesh" />
```

Child

```jsx
function Welcome(props) {
    return <h1>Hello {props.name}</h1>;
}
```

Output

```
Hello Rakesh
```

---

# Destructuring Props

Instead of

```jsx
function Welcome(props) {
    return <h1>{props.name}</h1>;
}
```

Use

```jsx
function Welcome({ name }) {
    return <h1>{name}</h1>;
}
```

Cleaner and recommended.

---

# Can We Modify Props?

No.

Props are **Immutable**.

Wrong

```jsx
props.name = "John";
```

❌ Never modify Props.

---

# Component Communication

React follows **One-Way Data Flow**.

```
Parent Component

↓

Props

↓

Child Component
```

Child components receive data but should not modify it.

---

# 🔹 What is State?

State is data owned and managed by the component.

Unlike Props, State can change.

Example

```jsx
const [count, setCount] = useState(0);
```

Updating State

```jsx
setCount(count + 1);
```

React re-renders the component automatically.

---

# 📊 Props vs State

| Props | State |
|--------|-------|
| Passed by Parent | Managed by Component |
| Read-only | Mutable |
| External Data | Internal Data |
| Cannot Modify | Can Update |
| Parent Controls | Component Controls |

---

# 🔹 Props Drilling

Suppose

```
App

↓

Dashboard

↓

Employee

↓

EmployeeCard
```

Only EmployeeCard needs Employee Name.

But the data passes through every component.

```
App

↓

Dashboard

↓

Employee

↓

EmployeeCard
```

This is called **Props Drilling**.

Problem

- Too many intermediate components receive props.

Solution

- Context API
- Redux
- Zustand

---

# 🔹 Component Composition

React prefers **Composition** over Inheritance.

Example

```jsx
<Card>

    <Employee />

</Card>
```

Card becomes reusable.

Tomorrow it can display

- Employee
- Product
- Customer

Composition makes components flexible and reusable.

---

# 🔹 Component Lifecycle

Every component goes through three stages.

```
Mount (Component is created and added to the UI for the first time.)

↓

Update (Component already exists but data/state/props changed.)

↓

Unmount (Component removed from UI.)
```

### Functional Component

Handled using

```jsx
useEffect()
```

### Class Component

Uses

- constructor()
- render()
- componentDidMount()
- componentDidUpdate()
- componentWillUnmount()

---

# 🔹 Component Re-rendering

A component re-renders when

- State changes
- Props change
- Parent Component re-renders
- Context changes

Example

```jsx
const [count, setCount] = useState(0);

setCount(1);
```

Component re-renders automatically.

---

# 🌍 Real-World Example

```
Employee Portal

│

├── Header

├── Sidebar

├── Employee List

│      │

│      ├── Employee Card

│      ├── Employee Card

│      ├── Employee Card

└── Footer
```

Each section is developed as a separate component.

---

# ✅ Best Practices

✔ Keep components small.

✔ One responsibility per component.

✔ Prefer Functional Components.

✔ Use Props for communication.

✔ Use State only when necessary.

✔ Avoid unnecessary Props Drilling.

✔ Prefer Composition over Inheritance.

---

# 🎤 Common Interview Questions

## What is a Component?

A reusable piece of UI in React.

---

## Why do we use Components?

To build reusable, maintainable, and modular applications.

---

## Difference between Functional and Class Components?

Functional Components are JavaScript functions that support Hooks.

Class Components are ES6 classes using lifecycle methods.

---

## What are Props?

Props are read-only values passed from Parent to Child.

---

## Can Props be modified?

No.

Props are immutable.

---

## Difference between Props and State?

Props are passed by Parent.

State belongs to the component.

---

## What is Props Drilling?

Passing Props through multiple intermediate components to reach a deeply nested component.

---

## Why does React prefer Composition?

Because Composition makes components more reusable and flexible than inheritance.

---

## When does a Component Re-render?

- State changes
- Props change
- Parent re-renders
- Context changes

---

# 📌 Summary

| Scenario | Recommended |
|----------|-------------|
| Reusable UI | Component |
| Modern React Development | Functional Component |
| Legacy React | Class Component |
| Parent to Child Communication | Props |
| Component Internal Data | State |
| Deep Data Sharing | Context API |
| Reuse UI | Component Composition |
| Shared Layout | Composition |
| Large Application | Small Components |

---

# 🎯 Interview One-Liner

> **A React Component is a reusable, independent piece of UI. Modern React applications use Functional Components with Hooks. Data flows from Parent to Child through immutable Props, while mutable State is managed inside the component. React encourages Composition over Inheritance to build scalable and maintainable applications.**

---

# ⭐ Senior Interview Tip

If the interviewer asks:

### "Why are Functional Components preferred over Class Components?"

Answer:

- Less boilerplate code
- Easier to understand
- Supports Hooks
- Better readability
- Easier testing
- Recommended by the React team for modern development

**Today, almost all new React applications are built using Functional Components.**