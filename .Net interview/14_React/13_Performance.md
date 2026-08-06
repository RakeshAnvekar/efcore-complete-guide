# 🚀 React Module 12 – Performance Optimization

> **Difficulty:** ⭐⭐⭐⭐☆
>
> **Interview Level:** Mid → Senior React Developer
>
> **Key Concept:** React Performance Optimization is the process of reducing unnecessary re-renders, avoiding expensive computations, and minimizing the application's bundle size to make React applications faster and more efficient.

---

# 📚 Table of Contents

- 🎯 Why React Performance Matters
- 🎯 Why Components Re-render
- 🔹 React.memo
- 🔹 useMemo
- 🔹 useCallback
- 🔹 Lazy Loading
- 🔹 Code Splitting
- 🌍 Real Project Example
- 📊 React.memo vs useMemo vs useCallback 
- 📊 Lazy Loading vs Code Splitting
- ✅ Best Practices
- 🎤 Common Interview Questions
- 📌 Summary
- 🎯 Interview One-Liner

---

# 🎯 Why React Performance Matters

Every time **State**, **Props**, or **Context** changes,

React re-renders components.

Example

```
App

↓

Dashboard

↓

Employee List

↓

Employee Card

↓

Employee Details
```

Suppose only the Counter changes.

```
Counter Updated

↓

Parent Re-renders

↓

Employee List Re-renders

↓

Employee Cards Re-render

↓

Employee Details Re-render
```

Many of these renders are unnecessary.

---

# 🎯 React Performance Optimization

React provides several optimization techniques.

```
Performance Optimization

↓

React.memo

↓

useMemo

↓

useCallback

↓

Lazy Loading

↓

Code Splitting
```

---

# 🔹 React.memo

## What is React.memo?

`React.memo` is a **Higher-Order Component (HOC)** that prevents unnecessary re-rendering of a component.

If the component receives the **same props**, React skips rendering it.

---

## Without React.memo

```jsx
function Child(){

    console.log("Child Render");

    return <h2>Child Component</h2>;

}

function Parent(){

    const [count,setCount]=useState(0);

    return(

        <>

            <Child/>

            <button

                onClick={()=>setCount(count+1)}

            >

                {count}

            </button>

        </>

    );

}
```

Execution

```
Button Click

↓

Parent Re-render

↓

Child Re-render
```

Even though Child doesn't use `count`.

---

## With React.memo

```jsx
const Child = React.memo(function Child(){

    console.log("Child Render");

    return <h2>Child Component</h2>;

});
```

Execution

```
Button Click

↓

Parent Re-render

↓

Props Changed?

↓

No

↓

Skip Child Render
```

---

## When Should You Use React.memo?

✔ Dashboard Widgets

✔ Employee Cards

✔ Product Cards

✔ Large Lists

✔ Reusable Components

---

# 🔹 useMemo

## What is useMemo?

`useMemo` caches the **result of an expensive calculation**.

Instead of recalculating every render,

React reuses the cached result.

---

Example

```jsx
const sortedEmployees = useMemo(() => {

    return [...employees].sort(

        (a,b)=>a.salary-b.salary

    );

},[employees]);
```

Execution

```
Render

↓

Dependencies Changed?

↓

Yes

↓

Calculate

↓

Store Result

↓

No

↓

Reuse Cached Result
```

---

## Real Use Cases

✔ Sorting

✔ Filtering

✔ Searching

✔ Report Generation

✔ Statistics

---

# 🔹 useCallback

## What is useCallback?

`useCallback` caches a **function**.

Instead of creating a new function every render,

React reuses the same function reference.

---

Without useCallback

```
Render

↓

Create New Function

↓

Child Props Changed

↓

Child Re-render
```

---

With useCallback

```jsx
const deleteEmployee = useCallback((id)=>{

    console.log(id);

},[]);
```

Execution

```
Render

↓

Reuse Same Function

↓

Child Props Unchanged

↓

Child Skips Rendering
```

---

## Best Use Cases

✔ Event Handlers

✔ React.memo Components

✔ Child Components

---

# 🔹 React.memo + useCallback

This is the most common optimization.

```
Parent Re-render

↓

useCallback

↓

Same Function Reference

↓

React.memo

↓

Skip Child Render
```

---

# 🔹 Lazy Loading

## What is Lazy Loading?

Lazy Loading loads components **only when they are needed**.

Instead of downloading every page initially,

React downloads pages on demand.

---

Without Lazy Loading

```
Home

Employees

Reports

Dashboard

Settings

↓

Downloaded Immediately
```

---

With Lazy Loading

```
Application Starts

↓

Home Downloaded

↓

User Opens Dashboard

↓

Dashboard Downloaded

↓

User Opens Reports

↓

Reports Downloaded
```

---

Example

```jsx
import {

    lazy,

    Suspense

} from "react";

const Dashboard = lazy(

    ()=>import("./Dashboard")

);
```

Render

```jsx
<Suspense

fallback={<h2>Loading...</h2>}

>

    <Dashboard/>

</Suspense>
```

Execution

```
User Opens Dashboard

↓

Download Dashboard Bundle

↓

Render Dashboard
```

---

## Benefits

✔ Faster Initial Load

✔ Smaller Bundle

✔ Better User Experience

---

# 🔹 Code Splitting

## What is Code Splitting?

Code Splitting divides one large JavaScript bundle into multiple smaller bundles.

Without Code Splitting

```
bundle.js

20 MB

↓

Download Everything
```

---

With Code Splitting

```
Home.js

↓

Dashboard.js

↓

Reports.js

↓

Employees.js
```

Only required bundles are downloaded.

---

Example

```jsx
const Employee = lazy(

()=>import("./Employee")

);
```

Each page becomes a separate bundle.

---

# 🌍 Real Project Example

Employee Management System

```
Application Starts

↓

Login Page Downloaded

↓

User Logs In

↓

Dashboard Bundle Downloaded

↓

Employee Page Opened

↓

Employee Bundle Downloaded

↓

Reports Opened

↓

Reports Bundle Downloaded
```

Only the required pages are downloaded.

---

# 📊 React.memo vs useMemo vs useCallback

| Feature | React.memo | useMemo | useCallback |
|----------|------------|----------|-------------|
| Memoizes | Component | Value | Function |
| Prevents | Re-render | Recalculation | Function Recreation |
| Returns | Component | Value | Function |
| Best Use | Child Components | Expensive Calculations | Event Handlers |

---

# 📊 Lazy Loading vs Code Splitting

| Lazy Loading | Code Splitting |
|--------------|----------------|
| Loads Components On Demand | Splits Bundle into Smaller Files |
| Improves Initial Load Time | Reduces Bundle Size |
| Uses React.lazy() | Supported by Build Tools (e.g., Vite/Webpack) |

---

# 🌍 Complete Performance Flow

```
User Click

↓

State Updated

↓

Parent Re-render

↓

React.memo

↓

Skip Child Render

↓

useMemo

↓

Reuse Calculation

↓

useCallback

↓

Reuse Function

↓

Lazy Loading

↓

Load Required Component

↓

Code Splitting

↓

Smaller JavaScript Bundle
```

---

# ❌ Common Mistakes

## Using React.memo Everywhere

Small components don't benefit much.

---

## Using useMemo for Simple Calculations

```jsx
const total = useMemo(() => a+b,[a,b]);
```

No performance improvement.

---

## Using useCallback for Every Function

Only use it when function references matter.

---

## Forgetting Dependency Arrays

```jsx
useMemo(()=>{

    return calculate();

},[]);
```

The value may become stale if dependencies are missing.

---

# ✅ Best Practices

✔ Use React.memo for reusable child components.

✔ Use useMemo for expensive calculations.

✔ Use useCallback for callbacks passed to memoized children.

✔ Lazy load large pages.

✔ Split application into smaller bundles.

✔ Keep state as local as possible.

✔ Use the React DevTools Profiler before optimizing.

---

# 🎤 Common Interview Questions

## What causes unnecessary re-renders?

State changes, prop changes, context changes, and new object/function references.

---

## What is React.memo?

A Higher-Order Component that skips rendering if props haven't changed.

---

## What is useMemo?

A Hook that caches expensive calculation results.

---

## What is useCallback?

A Hook that caches function references.

---

## Difference between useMemo and useCallback?

| useMemo | useCallback |
|----------|-------------|
| Caches Value | Caches Function |

---

## Difference between Lazy Loading and Code Splitting?

Code Splitting creates multiple bundles.

Lazy Loading loads those bundles only when required.

---

## Does React.memo prevent Parent Re-render?

No.

It only prevents the memoized child from re-rendering when its props remain unchanged.

---

# 📌 Summary

| Scenario | Recommended |
|----------|-------------|
| Prevent Child Re-render | React.memo |
| Cache Expensive Calculation | useMemo |
| Cache Event Handler | useCallback |
| Load Page On Demand | Lazy Loading |
| Reduce Bundle Size | Code Splitting |
| Large Dashboard | React.memo + useCallback |
| Sorting Large Data | useMemo |

---

# 🎯 Interview One-Liner

> **React Performance Optimization improves application speed by reducing unnecessary rendering and minimizing JavaScript bundle size. `React.memo` prevents unnecessary component re-renders, `useMemo` caches expensive calculations, `useCallback` caches function references, and `Lazy Loading` with `Code Splitting` reduces the application's initial load time.**

---

# ⭐ Senior Interview Tip

One of the most common interview questions is:

## **How would you optimize a slow React application?**

### Answer

1. Identify unnecessary re-renders using the **React DevTools Profiler**.
2. Wrap reusable child components with **React.memo**.
3. Cache expensive calculations using **useMemo**.
4. Cache callback functions passed to memoized children using **useCallback**.
5. Lazy load routes and large components with **React.lazy** and **Suspense**.
6. Enable **Code Splitting** so only required bundles are downloaded.
7. Use virtualization (e.g., `react-window`) for very large lists.
8. Keep state local where possible and avoid unnecessary updates.

Following these techniques significantly improves rendering performance, memory usage, and application startup time in large React applications.