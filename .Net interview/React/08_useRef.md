# 🚀 React Module 7 – Part 3 – useRef

> **Difficulty:** ⭐⭐⭐⭐☆
>
> **Interview Level:** Beginner → Mid → Senior React Developer
>
> **Key Concept:** `useRef` is a React Hook that stores a mutable value that persists across renders without causing the component to re-render. It is commonly used for **DOM manipulation**, **storing previous values**, **timer IDs**, and **mutable variables**.

---

# 📚 Table of Contents

- 🎯 What is useRef?
- 🎯 Why Do We Need useRef?
- 🔹 Syntax
- 🔹 How useRef Works
- 🔹 Accessing DOM Elements
- 🔹 Storing Mutable Values
- 🔹 Why useRef Doesn't Re-render
- 🔹 useRef vs useState
- 🔹 Storing Previous Values
- 🌍 Real World Examples
- 📊 useRef vs useState
- ✅ Best Practices
- 🎤 Common Interview Questions
- 📌 Summary
- 🎯 Interview One-Liner

---

# 🎯 What is useRef?

`useRef` is a **React Hook** that creates a **mutable reference object**.

Unlike **useState**, changing a `useRef` value **does not trigger a component re-render**.

Syntax

```jsx
const ref = useRef(initialValue);
```

React creates an object like this

```javascript
{
    current: initialValue
}
```

Example

```jsx
const inputRef = useRef(null);
```

---

# 🎯 Why Do We Need useRef?

Sometimes we need to store values **without re-rendering the component**.

Common Uses

- Focus an Input
- Access DOM Elements
- Store Timer IDs
- Store Previous Values
- Scroll to an Element
- Store Mutable Variables

---

# 🔹 Syntax

```jsx
const inputRef = useRef(null);
```

Attach it to a DOM element

```jsx
<input ref={inputRef} />
```

Access it

```jsx
inputRef.current.focus();
```

Execution

```
Component Render

↓

React Creates DOM

↓

inputRef.current

↓

Points to Input Element
```

---

# 🔹 How useRef Works

When React renders the component

```jsx
const inputRef = useRef(null);
```

React creates

```javascript
{
    current: null
}
```

After rendering

```jsx
<input ref={inputRef}/>
```

React updates

```javascript
{
    current: HTMLInputElement
}
```

Now

```jsx
inputRef.current.focus();
```

works.

---

# 🔹 Accessing DOM Elements

Example

```jsx
import { useRef } from "react";

function App() {

    const inputRef = useRef(null);

    function handleClick() {

        inputRef.current.focus();

    }

    return (

        <>

            <input ref={inputRef} />

            <button onClick={handleClick}>

                Focus Input

            </button>

        </>

    );

}
```

Execution

```
Click Button

↓

handleClick()

↓

inputRef.current.focus()

↓

Cursor Moves to Input
```

---

# 🔹 Storing Mutable Values

Unlike State, useRef stores values without re-rendering.

Example

```jsx
const countRef = useRef(0);

function increment() {

    countRef.current++;

    console.log(countRef.current);

}
```

Output

```
1

2

3

4
```

Notice

The component **does not re-render**.

---

# 🔹 Why useRef Doesn't Re-render

Suppose

```jsx
countRef.current++;
```

Execution

```
Mutable Value Changed

↓

React State

No Change

↓

No Re-render
```

React only re-renders when

- State Changes
- Props Change
- Context Changes

Updating `ref.current` is ignored by React's rendering process.

---

# 🔹 useRef vs useState

## useState

```jsx
const [count, setCount] = useState(0);

setCount(count + 1);
```

Execution

```
State Updated

↓

Component Re-render

↓

UI Updated
```

---

## useRef

```jsx
const countRef = useRef(0);

countRef.current++;
```

Execution

```
Value Updated

↓

No Re-render

↓

UI Does Not Change
```

---

# 📊 useRef vs useState

| Feature | useState | useRef |
|----------|----------|---------|
| Causes Re-render | ✅ Yes | ❌ No |
| Stores Value | ✅ Yes | ✅ Yes |
| Mutable | ❌ No | ✅ Yes |
| Updates UI | ✅ Yes | ❌ No |
| DOM Access | ❌ No | ✅ Yes |
| Timer IDs | ❌ No | ✅ Yes |
| Previous Values | ❌ Difficult | ✅ Easy |

---

# 🔹 Storing Previous Values

One of the most common interview questions.

```jsx
import { useEffect, useRef } from "react";

function Counter({ count }) {

    const previous = useRef();

    useEffect(() => {

        previous.current = count;

    }, [count]);

    return (

        <>

            <h2>

                Current : {count}

            </h2>

            <h2>

                Previous : {previous.current}

            </h2>

        </>

    );

}
```

Execution

```
Current = 1

Previous = undefined

↓

Current = 2

Previous = 1

↓

Current = 3

Previous = 2
```

---

# 🌍 Real World Examples

## Focus Input

```jsx
inputRef.current.focus();
```

---

## Scroll to an Element

```jsx
sectionRef.current.scrollIntoView();
```

---

## Store Timer ID

```jsx
const timerRef = useRef();

timerRef.current = setInterval(...);
```

Cleanup

```jsx
clearInterval(timerRef.current);
```

---

## Store Previous Value

```jsx
previous.current = value;
```

---

## Play Video

```jsx
videoRef.current.play();
```

---

## Pause Video

```jsx
videoRef.current.pause();
```

---

# 🌍 Real Project Example

Search Screen

```
Page Loads

↓

Cursor Automatically Focuses

↓

User Starts Typing
```

Implementation

```jsx
useEffect(() => {

    inputRef.current.focus();

}, []);
```

---

# ✅ Best Practices

✔ Use useRef for DOM manipulation.

✔ Use useRef for mutable values.

✔ Use useState for UI data.

✔ Use useRef for Timer IDs.

✔ Use useRef for Previous Values.

✔ Do not overuse refs for state management.

---

# 🎤 Common Interview Questions

## What is useRef?

A Hook that stores a mutable reference object that persists across renders.

---

## Does updating useRef trigger a re-render?

No.

Updating `ref.current` does not cause React to re-render.

---

## Why do we use useRef?

- DOM Manipulation
- Focus Input
- Store Timer IDs
- Store Previous Values
- Scroll Position

---

## Difference between useRef and useState?

useState updates the UI.

useRef stores values without updating the UI.

---

## Can useRef access DOM elements?

Yes.

That is one of its primary purposes.

---

## Can useRef store previous values?

Yes.

This is a very common interview use case.

---

## When should we use useRef instead of useState?

Use useRef when changing the value should **not** update the UI.

Use useState when changing the value **should** update the UI.

---

# 📌 Summary

| Scenario | Recommended |
|----------|-------------|
| Focus Input | useRef |
| Access DOM | useRef |
| Store Timer ID | useRef |
| Scroll to Element | useRef |
| Store Previous Value | useRef |
| UI Data | useState |
| Counter Display | useState |
| Mutable Variable | useRef |

---

# 🎯 Interview One-Liner

> **`useRef` is a React Hook that creates a mutable reference object whose value persists across renders without triggering a re-render. It is commonly used for DOM manipulation, storing timer IDs, tracking previous values, and maintaining mutable data that does not affect the UI.**

---

# ⭐ Senior Interview Tip

One of the most common interview questions is:

## **When should you use useRef instead of useState?**

### Answer

Use **useState** when the value affects the UI because React needs to re-render the component.

Use **useRef** when the value should persist across renders but **does not need to trigger a UI update**, such as:

- DOM Element References
- Timer IDs
- Previous Values
- Scroll Positions
- Third-Party Library Instances

Changing `ref.current` updates the value immediately, but React does not re-render because refs are outside React's state management system.