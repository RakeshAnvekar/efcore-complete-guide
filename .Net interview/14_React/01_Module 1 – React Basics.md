# 🚀 React Module 1 – React Fundamentals

> **Difficulty:** ⭐⭐⭐☆☆
>
> **Interview Level:** Beginner → Mid → Senior React Developer
>
> **Key Concept:** React is a JavaScript library for building reusable, component-based user interfaces. It uses a **Virtual DOM** to efficiently update only the parts of the UI that change.

---

# 📚 Table of Contents

- 🎯 What is React?
- 🎯 Why was React Created?
- 🔹 What is SPA?
- 🔹 React Architecture
- 🔹 Components
- 🔹 What is DOM?
- 🔹 Real DOM
- 🔹 Virtual DOM
- 🔹 Rendering Process
- 🔹 Diffing Algorithm
- 🔹 Reconciliation
- 🔹 JSX
- 📊 Real DOM vs Virtual DOM
- 📊 React vs Angular vs Vue
- 🎤 Common Interview Questions
- 📌 Summary
- 🎯 Interview One-Liner

---

# 🎯 What is React?

React is an **Open Source JavaScript Library** developed by **Facebook (Meta)**.

It is used to build:

- Interactive User Interfaces
- Single Page Applications (SPA)
- Reusable Components

React follows a **Component-Based Architecture**, where the UI is divided into small reusable pieces.

---

# 🎯 Why was React Created?

Before React, developers mainly used **jQuery**.

Whenever data changed, developers manually updated the DOM.

```
Data Changes

↓

Find HTML Element

↓

Update HTML

↓

Browser Repaint
```

This became difficult for large applications.

React solves this by updating **only the changed UI**.

---

# Example

Suppose we have a page.

```
-----------------------

Header

-----------------------

Products

Laptop

Phone

TV

-----------------------

Footer

-----------------------
```

Now only "Phone" changes to "iPhone".

Without React

```
Entire Page

↓

Re-render

↓

Slow
```

With React

```
Header

(No Change)

↓

Phone

↓

iPhone

↓

Footer

(No Change)
```

Only the changed part is updated.

---

# 🔹 What is SPA?

SPA stands for **Single Page Application**.

A Single Page Application loads **one HTML page** and updates the UI dynamically without refreshing the entire page.

---

## Traditional Website

```
Home

↓

Server

↓

HTML

↓

Browser
```

Click About

```
Browser

↓

Server

↓

New HTML

↓

Entire Page Reload
```

---

## React SPA

```
Browser

↓

React

↓

Update Component

↓

No Full Refresh
```

Examples

- Gmail
- Facebook
- Netflix
- LinkedIn
- ChatGPT

---

# 🔹 React Architecture

React applications are built using **Components**.

```
App

│

├── Header

├── Sidebar

├── Product List

│      │

│      ├── Product Card

│      ├── Product Card

│      └── Product Card

└── Footer
```

Each component is independent and reusable.

---

# 🔹 What is a Component?

A Component is a reusable piece of UI.

Example

```jsx
function Header() {
    return <h1>Welcome</h1>;
}
```

Benefits

- Reusable
- Easy to Maintain
- Independent
- Testable

---

# 🔹 What is DOM?

DOM stands for **Document Object Model**.

The browser converts HTML into a tree structure.

Example

```html
<html>
   <body>
      <h1>Hello</h1>
   </body>
</html>
```

DOM Tree

```
HTML

│

BODY

│

H1

│

Hello
```

Every HTML element becomes an object.

---

# 🔹 Real DOM

The Real DOM is the actual DOM created and managed by the browser.

Whenever data changes, the browser updates the Real DOM.

Problem

```
1000 Products

↓

One Product Changed

↓

Browser Checks Large DOM

↓

Repaint

↓

Reflow
```

Updating the Real DOM is expensive.

---

# 🔹 Virtual DOM

The Virtual DOM is a lightweight JavaScript copy of the Real DOM.

```
Real DOM

↓

React

↓

Virtual DOM
```

React compares two Virtual DOM trees and updates only the changed nodes.

---

# Example

Old Virtual DOM

```
Laptop

Phone

TV
```

New Virtual DOM

```
Laptop

iPhone

TV
```

Only

```
Phone

↓

iPhone
```

is updated.

---

# 🔹 React Rendering Process

Whenever State or Props change

```
State Changes

↓

Component Re-renders

↓

New Virtual DOM

↓

Diffing

↓

Reconciliation

↓

Real DOM Updated
```

---

# 🔹 Diffing Algorithm

Diffing is the process of comparing the **Old Virtual DOM** with the **New Virtual DOM**.

Old

```
Laptop

Phone

TV
```

New

```
Laptop

iPhone

TV
```

React detects

```
Phone

↓

iPhone
```

Only this node is updated.

---

# 🔹 Reconciliation

Reconciliation is React's process of updating the Real DOM efficiently.

```
State Changes

↓

Virtual DOM Created

↓

Compare Old vs New

↓

Find Differences

↓

Update Real DOM
```

This makes React very fast.

---

# 🔹 Why React is Fast?

React is fast because it uses

- Virtual DOM
- Diffing Algorithm
- Reconciliation
- Component Reuse
- Efficient Rendering

Instead of updating the entire page, it updates only the changed elements.

---

# 🔹 JSX

JSX stands for **JavaScript XML**.

It allows us to write HTML-like syntax inside JavaScript.

Example

```jsx
const element = <h1>Hello React</h1>;
```

Internally

```javascript
const element = React.createElement(
    "h1",
    null,
    "Hello React"
);
```

JSX is converted into JavaScript before execution.

---

# JSX Rules

## One Parent Element

✅ Correct

```jsx
return (
    <div>
        <h1>Hello</h1>
        <p>World</p>
    </div>
);
```

❌ Wrong

```jsx
return (
    <h1>Hello</h1>
    <p>World</p>
);
```

Use a wrapper element or React Fragment (`<>...</>`).

---

# 📊 Real DOM vs Virtual DOM

| Feature | Real DOM | Virtual DOM |
|----------|----------|-------------|
| Exists In | Browser | Memory |
| Speed | Slower | Faster |
| Updates | Directly | Compares Before Updating |
| Performance | Lower | Higher |
| Rendering | Entire DOM May Be Updated | Only Changed Nodes |

---

# 📊 React vs Angular vs Vue

| Feature | React | Angular | Vue |
|----------|--------|----------|------|
| Type | Library | Framework | Framework |
| Developed By | Meta | Google | Evan You |
| Learning Curve | Easy | Hard | Easy |
| Virtual DOM | ✅ Yes | ❌ No | ✅ Yes |
| Language | JavaScript | TypeScript | JavaScript |

---

# 🎤 Common Interview Questions

## What is React?

A JavaScript library used to build reusable UI components.

---

## Why is React fast?

Because it uses the Virtual DOM, Diffing Algorithm, and Reconciliation to update only the changed UI.

---

## What is Virtual DOM?

A lightweight JavaScript representation of the Real DOM.

---

## What is JSX?

JSX is a syntax extension that allows writing HTML-like code inside JavaScript.

---

## What is Reconciliation?

The process React uses to compare Virtual DOM trees and efficiently update the Real DOM.

---

## What is Diffing?

The algorithm React uses to compare the old and new Virtual DOM.

---

## What is SPA?

A Single Page Application loads one HTML page and dynamically updates the UI without refreshing the browser.

---

# 📌 Summary

| Scenario | Recommended |
|----------|-------------|
| Build Dynamic UI | React |
| Reusable Components | React |
| Single Page Application | React |
| Efficient UI Updates | Virtual DOM |
| Compare UI Changes | Diffing |
| Update Browser Efficiently | Reconciliation |
| HTML Inside JavaScript | JSX |
| Fast Rendering | Virtual DOM |

---

# 🎯 Interview One-Liner

> **React is a JavaScript library for building component-based user interfaces. It uses a Virtual DOM, Diffing Algorithm, and Reconciliation process to efficiently update only the parts of the UI that change, making applications fast and responsive.**