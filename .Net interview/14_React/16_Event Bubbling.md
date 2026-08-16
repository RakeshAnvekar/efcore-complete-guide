# React Event Bubbling - Interview Notes

## What is Event Bubbling?

Event Bubbling is a browser mechanism where an event starts from the element that triggered it and then moves upward through its parent elements.

```text
Child Element
      ↓
Parent Element
      ↓
Grandparent Element
      ↓
Document
```

The event "bubbles up" through the DOM tree.

---

# Example DOM Structure

```html
<div>
    <button>Click Me</button>
</div>
```

DOM Tree:

```text
DIV
 |
BUTTON
```

---

# React Example

```jsx
function App() {
  return (
    <div onClick={() => console.log("Div Clicked")}>
      <button onClick={() => console.log("Button Clicked")}>
        Click Me
      </button>
    </div>
  );
}
```

---

# What Happens?

User clicks:

```text
Button
```

Output:

```text
Button Clicked
Div Clicked
```

---

# Why?

Because the click event starts at:

```text
Button
```

and then bubbles up to:

```text
Div
```

Flow:

```text
Button Clicked
      ↓
Div Clicked
```

---

# Real-Life Example

Imagine:

```text
Grandfather
      |
Father
      |
Child
```

Child shouts:

```text
"I clicked!"
```

Father hears it.

```text
"I clicked!"
```

Grandfather hears it.

```text
"I clicked!"
```

The message travels upward.

This is Event Bubbling.

---

# Another Example

```jsx
<div onClick={() => console.log("Card Clicked")}>
  <button onClick={() => console.log("Edit Clicked")}>
    Edit
  </button>
</div>
```

User clicks:

```text
Edit Button
```

Output:

```text
Edit Clicked
Card Clicked
```

Both handlers execute because the event bubbles from button to div.

---

# Problem

Suppose:

```text
Card Click
    ↓
Open Product Details

Edit Click
    ↓
Open Edit Screen
```

When clicking Edit:

```text
Edit Screen Opens
Product Details Also Open
```

This is unwanted behavior caused by event bubbling.

---

# stopPropagation()

To stop bubbling:

```jsx
<button
  onClick={(e) => {
    e.stopPropagation();
    console.log("Edit Clicked");
  }}
>
  Edit
</button>
```

---

# Output

```text
Edit Clicked
```

Only the button event executes.

Parent event does not run.

---

# What Does stopPropagation() Do?

Without:

```text
Button
   ↓
Div
```

With:

```text
Button
   X
Div
```

The event stops at the button.

---

# Common Real-World Uses

### Modal Popup

```text
Overlay
   |
Modal
```

Prevent modal click from closing the popup.

---

### Dropdown Menu

```text
Dropdown
   |
Menu Item
```

Prevent parent dropdown event from firing.

---

### Cards With Buttons

```text
Product Card
      |
Edit Button
```

Prevent card click when clicking Edit.

---

# Interview Questions

## What is Event Bubbling?

Event Bubbling is a process where an event starts at the target element and propagates upward through its parent elements.

---

## What is the output?

```jsx
<div onClick={() => console.log("Parent")}>
  <button onClick={() => console.log("Child")}>
    Click
  </button>
</div>
```

Output:

```text
Child
Parent
```

---

## How do you stop Event Bubbling?

Use:

```jsx
e.stopPropagation();
```

---

## Why is Event Bubbling useful?

It allows parent elements to handle events triggered by child elements and forms the basis of event delegation.

---

# Quick Interview Answer

Event Bubbling is a browser event mechanism where an event starts from the element that triggered it and then propagates upward through its parent elements. In React, clicking a button inside a div triggers both the button's and div's click handlers unless `event.stopPropagation()` is used to stop the bubbling process.