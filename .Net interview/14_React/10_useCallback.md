# 🚀 React Module 7 – Part 5 – useCallback

> **Difficulty:** ⭐⭐⭐⭐⭐
>
> **Interview Level:** Mid → Senior React Developer
>
> **Key Concept:** `useCallback` is a React Hook that **memoizes (caches) a function**. It returns the **same function reference** between renders until one of its dependencies changes. It is primarily used to optimize performance when passing functions to child components.

---

# 📚 Table of Contents

- 🎯 What is useCallback?
- 🎯 Why Do We Need useCallback?
- 🔹 Function Recreation Problem
- 🔹 Syntax
- 🔹 How useCallback Works
- 🔹 Without useCallback
- 🔹 With useCallback
- 🔹 Dependency Array
- 🔹 useCallback with React.memo
- 🔹 Real World Example
- 🔹 useCallback vs useMemo
- 🔹 useCallback vs Normal Function
- 📊 Without useCallback vs With useCallback
- 📊 useCallback vs useMemo vs React.memo
- ✅ Best Practices
- 🎤 Common Interview Questions
- 📌 Summary
- 🎯 Interview One-Liner

---

# 🎯 What is useCallback?

`useCallback` is a **React Hook** that **memoizes a function**.

Instead of creating a new function every time a component re-renders, React returns the **same function reference** until one of its dependencies changes.

Think of `useCallback` as storing a function in memory.

```
Create Function

↓

Store Function

↓

Reuse Same Function

↓

No Recreation
```

---

# 🎯 Why Do We Need useCallback?

In JavaScript, **functions are objects**.

Every time a component renders,

a **new function object** is created.

Example

```jsx
function App() {

    function handleClick() {

        console.log("Clicked");

    }

    return (

        <button onClick={handleClick}>

            Click

        </button>

    );

}
```

Although the function body is identical,

React creates a new function every render.

```
Render 1

↓

handleClick()

↓

Memory Address 0x101

---------------------

Render 2

↓

handleClick()

↓

Memory Address 0x202

---------------------

Render 3

↓

handleClick()

↓

Memory Address 0x303
```

Every render produces a **different function reference**.

---

# 🔹 Why is this a Problem?

Suppose

```
Parent Component

↓

Child Component
```

Parent passes

```jsx
<Child onClick={handleClick} />
```

Execution

```
Parent Re-render

↓

New Function Created

↓

Child Props Changed

↓

Child Re-renders
```

Even though the logic never changed.

---

# React Solution

```
Create Function

↓

useCallback

↓

Store Function

↓

Reuse Same Function

↓

Child Doesn't Re-render
```

---

# 🔹 Syntax

```jsx
const memoizedFunction = useCallback(() => {

    // Function Body

}, [dependencies]);
```

Structure

```
useCallback(

Callback Function,

Dependency Array

)
```

---

# 🔹 How useCallback Works

Without useCallback

```
Render

↓

Create Function

↓

Render Again

↓

Create New Function

↓

Render Again

↓

Create New Function
```

---

With useCallback

```
Render

↓

Create Function

↓

Store Function

↓

Render Again

↓

Reuse Same Function

↓

No Recreation
```

---

# 🔹 Example Without useCallback

```jsx
function Parent() {

    const [count, setCount] = useState(0);

    function handleClick() {

        console.log("Clicked");

    }

    return (

        <>

            <Child onClick={handleClick} />

            <button

                onClick={() => setCount(count + 1)}

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

State Updated

↓

Parent Re-render

↓

New handleClick Function

↓

Child Re-render
```

---

# 🔹 Example With useCallback

```jsx
const handleClick = useCallback(() => {

    console.log("Clicked");

}, []);
```

Execution

```
Initial Render

↓

Function Created

↓

Stored

↓

Parent Re-render

↓

Reuse Same Function

↓

Child Doesn't Re-render
```

---

# 🔹 Dependency Array

```jsx
const handleClick = useCallback(() => {

    console.log(count);

}, [count]);
```

Execution

```
Initial Render

↓

Function Created

↓

count Changes

↓

Create New Function

↓

Store Again

↓

Reuse Until count Changes Again
```

The callback is recreated **only** when one of its dependencies changes.

---

# 🔹 useCallback with React.memo

This is the most common interview scenario.

## Child Component

```jsx
const Child = React.memo(({ onClick }) => {

    console.log("Child Render");

    return (

        <button onClick={onClick}>

            Click

        </button>

    );

});
```

---

## Parent Component

```jsx
const handleClick = useCallback(() => {

    console.log("Clicked");

}, []);
```

Execution

```
Parent Re-render

↓

Same Function Reference

↓

Props Unchanged

↓

React.memo

↓

Skip Child Render
```

Without useCallback

```
Parent Re-render

↓

New Function

↓

Props Changed

↓

Child Re-render
```

---

# 🌍 Real World Example

Employee Dashboard

```
Parent

↓

Employee List

↓

Employee Card

↓

Delete Button
```

Delete Function

```jsx
const deleteEmployee = useCallback((id) => {

    // Delete Employee

}, []);
```

Every Employee Card receives the **same function reference**, avoiding unnecessary re-renders.

---

# 🔹 useCallback vs useMemo

## useCallback

Returns a **Function**

```jsx
const handleClick = useCallback(() => {

    console.log("Clicked");

}, []);
```

---

## useMemo

Returns a **Value**

```jsx
const total = useMemo(() => {

    return calculateTotal();

}, []);
```

---

# 🔹 useCallback vs Normal Function

## Normal Function

```jsx
function save() {

}
```

Execution

```
Render

↓

Create New Function

↓

New Memory Reference
```

---

## useCallback

```jsx
const save = useCallback(() => {

}, []);
```

Execution

```
Render

↓

Reuse Existing Function

↓

Same Memory Reference
```

---

# 📊 Without useCallback vs With useCallback

| Without useCallback | With useCallback |
|---------------------|------------------|
| New Function Every Render | Same Function Reused |
| New Memory Reference | Cached Reference |
| Child May Re-render | Child Can Skip Rendering |
| Less Efficient | More Efficient |

---

# 📊 useCallback vs useMemo vs React.memo

| Feature | useCallback | useMemo | React.memo |
|----------|-------------|----------|------------|
| Returns | Function | Value | Component |
| Memoizes | Function | Value | Component |
| Prevents | Function Recreation | Value Recalculation | Component Re-render |
| Primary Use | Event Handlers | Expensive Calculations | Component Optimization |

---

# ❌ Common Mistakes

## Wrapping Every Function

```jsx
const hello = useCallback(() => {

    console.log("Hello");

}, []);
```

No performance benefit.

---

## Missing Dependencies

```jsx
const save = useCallback(() => {

    console.log(count);

}, []);
```

The callback always uses the initial value of `count`.

---

## Using useCallback Without Need

If the function

- is not passed to a child
- is not used in another hook

then `useCallback` usually provides no benefit.

---

# ✅ Best Practices

✔ Use `useCallback` when passing functions to `React.memo` components.

✔ Include every dependency used inside the callback.

✔ Do not wrap every function.

✔ Measure performance before optimizing.

✔ Keep dependency arrays accurate.

---

# 🎤 Common Interview Questions

## What is useCallback?

A Hook that memoizes a function.

---

## Why do we use useCallback?

To prevent unnecessary function recreation and reduce unnecessary child component re-renders.

---

## Does useCallback prevent component re-rendering?

No.

It only memoizes the function reference.

---

## Difference between useCallback and useMemo?

`useCallback` returns a cached function.

`useMemo` returns a cached value.

---

## Difference between useCallback and React.memo?

`useCallback` memoizes functions.

`React.memo` memoizes components.

---

## When should we use useCallback?

- Event Handlers
- Functions passed to Child Components
- Functions used inside Dependency Arrays
- Together with React.memo

---

## Should we wrap every function with useCallback?

No.

Only when it provides a measurable performance improvement.

---

# 📌 Summary

| Scenario | Recommended |
|----------|-------------|
| Pass Function to Child | useCallback |
| React.memo Child | useCallback |
| Cache Function | useCallback |
| Cache Expensive Value | useMemo |
| Prevent Child Re-render | React.memo + useCallback |
| Simple Local Function | Normal Function |

---

# 🎯 Interview One-Liner

> **`useCallback` is a React Hook that memoizes a function. It returns the same function reference between renders unless its dependencies change, helping reduce unnecessary child component re-renders when used with `React.memo`.**

---

# ⭐ Senior Interview Tip

One of the most frequently asked interview questions is:

## **Why do we use useCallback with React.memo?**

### Without useCallback

```
Parent Re-render

↓

New Function Created

↓

Child Props Changed

↓

React.memo Cannot Skip

↓

Child Re-renders
```

---

### With useCallback

```
Parent Re-render

↓

Same Function Reference

↓

Props Unchanged

↓

React.memo Detects No Change

↓

Child Render Skipped
```

Using **`React.memo` + `useCallback`** together is one of the most common React performance optimization techniques.

---

# 🧠 Interview Trick Question

## **Does useCallback make every React application faster?**

### Answer

**No.**

`useCallback` has its own overhead because React must store and compare the function reference.

Use it only when:

- The callback is passed to a memoized child component.
- The callback is used in another hook's dependency array.
- Performance profiling shows unnecessary re-renders caused by changing function references.

For simple components and small callbacks, a normal function is usually the better choice.