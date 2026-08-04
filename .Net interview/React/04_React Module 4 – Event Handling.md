# 🚀 React Module 4 – Event Handling

> **Difficulty:** ⭐⭐⭐⭐☆
>
> **Interview Level:** Beginner → Mid → Senior React Developer
>
> **Key Concept:** Event Handling in React allows components to respond to user interactions such as button clicks, typing, form submission, mouse events, and keyboard events. React uses **Synthetic Events** to provide a consistent event system across all browsers.

---

# 📚 Table of Contents

- 🎯 What is Event Handling?
- 🎯 Why Do We Need Event Handling?
- 🔹 React Events vs HTML Events
- 🔹 Handling Click Events
- 🔹 Passing Parameters to Events
- 🔹 Event Object
- 🔹 Synthetic Events
- 🔹 onChange Event
- 🔹 onSubmit Event
- 🔹 Preventing Default Behavior
- 🔹 Event Bubbling
- 🔹 Event Capturing
- 🔹 stopPropagation()
- 📊 Common React Events
- 📊 Event Bubbling vs Event Capturing
- 🎤 Common Interview Questions
- 📌 Summary
- 🎯 Interview One-Liner

---

# 🎯 What is Event Handling?

An **Event** is an action performed by the user.

Examples

- Clicking a Button
- Typing in a TextBox
- Submitting a Form
- Hovering the Mouse
- Pressing a Keyboard Key

React responds to these actions using **Event Handling**.

---

# 🎯 Why Do We Need Event Handling?

Imagine a Login Page.

```
Username

Password

Login Button
```

When the user clicks **Login**, something should happen.

```
User Clicks Login

↓

Validate Input

↓

Call Login API

↓

Display Result
```

Without Event Handling, the application cannot respond to user actions.

---

# 🔹 React Events vs HTML Events

## HTML

```html
<button onclick="showMessage()">
    Click
</button>
```

---

## React

```jsx
<button onClick={showMessage}>
    Click
</button>
```

Notice

- React uses **camelCase**
- Pass a function reference
- Do not pass a string

---

# 🔹 Handling Click Events

```jsx
function App() {

    function showMessage() {
        alert("Welcome");
    }

    return (
        <button onClick={showMessage}>
            Click Me
        </button>
    );
}
```

Execution Flow

```
User Click

↓

onClick

↓

showMessage()

↓

Alert Displayed
```

---

# 🔹 Inline Event Handler

Instead of creating a separate function

```jsx
<button
    onClick={() => alert("Hello")}
>
    Click
</button>
```

Useful for small logic.

Avoid complex logic inside JSX.

---

# 🔹 Passing Parameters

❌ Wrong

```jsx
<button
    onClick={showMessage("Rakesh")}
>
    Click
</button>
```

The function executes immediately while rendering.

---

✔ Correct

```jsx
<button
    onClick={() => showMessage("Rakesh")}
>
    Click
</button>
```

```jsx
function showMessage(name) {
    alert(name);
}
```

Execution

```
User Click

↓

Arrow Function

↓

showMessage("Rakesh")
```

---

# 🔹 Event Object

React automatically passes an Event Object.

```jsx
function handleClick(event) {

    console.log(event);

}

<button onClick={handleClick}>
    Click
</button>
```

Useful Properties

- event.target
- event.type
- event.currentTarget
- event.preventDefault()
- event.stopPropagation()

---

# 🔹 Synthetic Events

React wraps the browser's native event inside a **SyntheticEvent**.

```
Browser Event

↓

React

↓

Synthetic Event

↓

Application
```

Advantages

- Cross Browser Compatibility
- Consistent Behavior
- Better Performance

---

# 🔹 onChange Event

Triggered whenever the value of an input changes.

```jsx
const [name, setName] = useState("");

<input

value={name}

onChange={(e) =>
    setName(e.target.value)
}
/>
```

Execution Flow

```
User Types

↓

onChange

↓

State Updated

↓

Component Re-render

↓

UI Updated
```

---

# 🔹 onSubmit Event

Triggered when a form is submitted.

```jsx
function handleSubmit(e) {

    e.preventDefault();

    console.log("Submitted");

}

<form onSubmit={handleSubmit}>

    <button type="submit">

        Save

    </button>

</form>
```

---

# 🔹 Preventing Default Behavior

Normally

```
Submit Form

↓

Browser Refresh
```

React

```jsx
event.preventDefault();
```

Now

```
Submit Form

↓

preventDefault()

↓

Stay on Same Page

↓

Call API
```

---

# 🔹 Event Bubbling

Events travel from the child element to the parent.

Example

```jsx
<div onClick={() => console.log("Parent")}>

    <button
        onClick={() => console.log("Child")}
    >
        Click
    </button>

</div>
```

Output

```
Child

Parent
```

Flow

```
Button

↓

Div

↓

Body

↓

Document
```

---

# 🔹 stopPropagation()

Used to stop Event Bubbling.

```jsx
function handleChild(e) {

    e.stopPropagation();

    console.log("Child");

}
```

Now only

```
Child
```

executes.

---

# 🔹 Event Capturing

Capturing works opposite to Bubbling.

```
Document

↓

Body

↓

Parent

↓

Child
```

React supports capturing using

```jsx
onClickCapture
```

Example

```jsx
<div
    onClickCapture={() =>
        console.log("Parent")
    }
>

    <button
        onClick={() =>
            console.log("Child")
        }
    >
        Click
    </button>

</div>
```

Output

```
Parent

Child
```

---

# 📊 Common React Events

| Event | Purpose |
|---------|----------|
| onClick | Button Click |
| onChange | Input Change |
| onSubmit | Form Submit |
| onFocus | Input Focus |
| onBlur | Input Lost Focus |
| onKeyDown | Key Press |
| onKeyUp | Key Release |
| onMouseEnter | Mouse Enter |
| onMouseLeave | Mouse Leave |

---

# 📊 Event Bubbling vs Event Capturing

| Event Bubbling | Event Capturing |
|----------------|-----------------|
| Child → Parent | Parent → Child |
| Default Behavior | Explicitly Enabled |
| Uses onClick | Uses onClickCapture |

---

# 🌍 Real World Example

Employee Search

```jsx
<input

value={search}

onChange={(e) =>
    setSearch(e.target.value)
}
/>
```

Execution

```
User Types

↓

onChange

↓

Update State

↓

Employee List Filtered

↓

UI Updated
```

---

# ✅ Best Practices

✔ Use meaningful event handler names

✔ Prefer separate functions over large inline logic

✔ Use `preventDefault()` for forms

✔ Use `stopPropagation()` only when necessary

✔ Prefer Controlled Components for forms

---

# 🎤 Common Interview Questions

## What is Event Handling?

Handling user interactions like clicks, typing and form submission.

---

## Why does React use onClick instead of onclick?

React follows camelCase naming conventions for JSX.

---

## What is Synthetic Event?

A wrapper around the browser's native event providing cross-browser compatibility.

---

## Difference between onClick and onSubmit?

- onClick handles button clicks.
- onSubmit handles form submission.

---

## Why use preventDefault()?

To stop the browser's default action such as page refresh after form submission.

---

## What is Event Bubbling?

The event travels from the child element to its parent.

---

## What is Event Capturing?

The event travels from the parent element to the child before bubbling starts.

---

## What does stopPropagation() do?

Stops the event from propagating to parent elements.

---

# 📌 Summary

| Scenario | Recommended |
|----------|-------------|
| Button Click | onClick |
| Input Value Change | onChange |
| Form Submission | onSubmit |
| Prevent Page Refresh | preventDefault() |
| Stop Bubbling | stopPropagation() |
| Capture Event First | onClickCapture |
| Cross Browser Events | SyntheticEvent |

---

# 🎯 Interview One-Liner

> **React Event Handling allows components to respond to user interactions using Synthetic Events. React uses camelCase event names, supports event bubbling and capturing, and provides methods like `preventDefault()` and `stopPropagation()` to control event behavior.**

---

# ⭐ Senior Interview Tip

One of the most common interview questions is:

### **Why do we write `onClick={handleClick}` instead of `onClick={handleClick()}`?**

**Answer**

```jsx
onClick={handleClick}
```

passes a **function reference**.

The function executes **only when the button is clicked**.

```jsx
onClick={handleClick()}
```

calls the function **immediately during rendering**, which is usually incorrect.

---

# 🧠 Quick Revision

| Topic | Remember |
|--------|----------|
| Event Handling | Respond to User Actions |
| onClick | Button Click |
| onChange | Input Change |
| onSubmit | Form Submit |
| SyntheticEvent | React Event Wrapper |
| preventDefault() | Prevent Browser Default Action |
| stopPropagation() | Stop Event Bubbling |
| Event Bubbling | Child → Parent |
| Event Capturing | Parent → Child |