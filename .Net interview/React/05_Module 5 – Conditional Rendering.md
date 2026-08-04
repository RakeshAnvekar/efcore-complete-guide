# 🚀 React Module 5 – Conditional Rendering

> **Difficulty:** ⭐⭐⭐⭐☆
>
> **Interview Level:** Beginner → Mid → Senior React Developer
>
> **Key Concept:** Conditional Rendering allows React to display different UI based on conditions. Instead of showing the same UI all the time, React decides what to render depending on the application's state or data.

---

# 📚 Table of Contents

- 🎯 What is Conditional Rendering?
- 🎯 Why Do We Need Conditional Rendering?
- 🔹 Using if Statement
- 🔹 Using Ternary Operator
- 🔹 Using Logical AND (&&)
- 🔹 Using Logical OR (||)
- 🔹 Returning null
- 🔹 Conditional CSS
- 🔹 Conditional Rendering with Lists
- 📊 if vs Ternary vs &&
- 📊 || vs ??
- 🌍 Real World Examples
- ✅ Best Practices
- 🎤 Common Interview Questions
- 📌 Summary
- 🎯 Interview One-Liner

---

# 🎯 What is Conditional Rendering?

Conditional Rendering means displaying different UI based on a condition.

Instead of always showing the same component, React decides what to display depending on the application's state.

Think of it just like an **if statement** in C#.

```csharp
if(isLoggedIn)
{
    ShowDashboard();
}
else
{
    ShowLogin();
}
```

React

```jsx
isLoggedIn

?

<Dashboard />

:

<Login />
```

---

# 🎯 Why Do We Need Conditional Rendering?

Imagine an Employee Portal.

If the user is logged in

```
Dashboard
```

Otherwise

```
Login Screen
```

Without Conditional Rendering

```
Dashboard

Login

Both Visible
```

Not correct.

React decides which UI should be displayed.

---

# 🌍 Real World Examples

## Login

```
User Logged In

↓

Dashboard
```

Not Logged In

```
Login Page
```

---

## Shopping Cart

```
Cart Empty

↓

"No Items Found"
```

Cart Has Products

```
Product List
```

---

## Admin Panel

```
Is Admin

↓

Show Delete Button
```

Otherwise

```
Hide Delete Button
```

---

# 🔹 Using if Statement

The `if` statement is useful for complex conditions.

```jsx
function App() {

    const isLoggedIn = true;

    if (isLoggedIn) {

        return <Dashboard />;

    }

    return <Login />;

}
```

Execution Flow

```
Condition

↓

True

↓

Dashboard

False

↓

Login
```

---

# 🔹 Using Ternary Operator (? :)

The Ternary Operator is the most commonly used approach in React.

Syntax

```jsx
condition

?

<ComponentA />

:

<ComponentB />
```

Example

```jsx
function App() {

    const isLoggedIn = false;

    return (

        isLoggedIn

        ?

        <Dashboard />

        :

        <Login />

    );

}
```

Output

```
Login Component
```

---

## Button Example

```jsx
<button>

{

isAdmin

?

"Delete"

:

"View"

}

</button>
```

Output

```
Delete
```

or

```
View
```

---

# 🔹 Using Logical AND (&&)

The **&& operator** renders a component only if the condition is true.

Syntax

```jsx
condition && <Component />
```

Example

```jsx
function App() {

    const isAdmin = true;

    return (

        <>

            {

                isAdmin &&

                <button>

                    Delete

                </button>

            }

        </>

    );

}
```

Output

```
Delete Button
```

If

```
isAdmin = false
```

Nothing is rendered.

---

# 🔹 Using Logical OR (||)

The OR operator returns the first truthy value.

Example

```jsx
const name = "";

return (

<h1>

{name || "Guest"}

</h1>

);
```

Output

```
Guest
```

If

```jsx
const name = "Rakesh";
```

Output

```
Rakesh
```

---

# 🔹 Returning null

React allows components to return **null**.

Returning null means

```
Render Nothing
```

Example

```jsx
function Message({ show }) {

    if (!show)

        return null;

    return <h1>Hello</h1>;

}
```

If

```
show = false
```

Nothing appears on the screen.

---

# 🔹 Conditional CSS

React can apply different CSS classes based on a condition.

```jsx
<button

className={

isActive

?

"active"

:

"inactive"

}

>

Save

</button>
```

Output

```
Active Button
```

or

```
Inactive Button
```

---

# 🔹 Conditional Rendering with Lists

Suppose we have employees.

```jsx
employees.length > 0

?

employees.map(employee => (

<EmployeeCard key={employee.id} />

))

:

<p>No Employees Found</p>
```

Output

```
Employee List
```

or

```
No Employees Found
```

---

# 🔹 Nested Conditions

Sometimes multiple conditions exist.

```jsx
isLoading

?

<Loader />

:

isLoggedIn

?

<Dashboard />

:

<Login />
```

Execution

```
Loading?

↓

Yes

↓

Loader

↓

No

↓

Logged In?

↓

Dashboard

↓

Login
```

Avoid too many nested ternary operators because they reduce readability.

---

# 📊 if vs Ternary vs &&

| Method | Best Use |
|----------|----------|
| if | Complex Logic |
| Ternary (? :) | Two Possible Results |
| && | Show Component Only When True |
| null | Hide Component Completely |

---

# 📊 || vs ??

Many interviews ask this JavaScript question.

Using ||

```javascript
const name = "";

console.log(name || "Guest");
```

Output

```
Guest
```

Because an empty string is **Falsy**.

---

Using ??

```javascript
const name = "";

console.log(name ?? "Guest");
```

Output

```
""
```

Because `??` only checks for

- null
- undefined

---

# 🌍 Real World Example

Employee Dashboard

```
Application

↓

Loading?

↓

Yes

↓

Loader

↓

No

↓

Employees Available?

↓

Employee List

↓

Otherwise

↓

"No Employees Found"
```

---

# ✅ Best Practices

✔ Use **if** for complex logic.

✔ Use **Ternary Operator** when there are two outcomes.

✔ Use **&&** when displaying a component only if a condition is true.

✔ Return **null** when nothing should be rendered.

✔ Avoid deeply nested ternary operators.

✔ Keep JSX clean and readable.

---

# 🎤 Common Interview Questions

## What is Conditional Rendering?

Displaying different UI based on conditions.

---

## Which operator is most commonly used?

The Ternary Operator (`? :`).

---

## When should we use &&?

When we want to render a component only if the condition is true.

---

## What happens if a component returns null?

React renders nothing.

---

## Difference between && and Ternary?

**&&**

```
Condition True

↓

Render Component

Condition False

↓

Nothing
```

---

**Ternary**

```
Condition True

↓

Component A

Condition False

↓

Component B
```

---

## Difference between || and ??

`||`

Returns the first truthy value.

`??`

Returns the right value only if the left value is **null** or **undefined**.

---

# 📌 Summary

| Scenario | Recommended |
|----------|-------------|
| Complex Conditions | if |
| Two Outcomes | Ternary Operator |
| Show Component Only If True | && |
| Default Value | \|\| |
| Null or Undefined Default | ?? |
| Hide Component | return null |
| Dynamic CSS | Conditional className |
| Render List or Empty Message | Ternary |

---

# 🎯 Interview One-Liner

> **Conditional Rendering is the process of displaying different UI based on application state or conditions. React commonly uses `if`, the Ternary Operator (`? :`), Logical AND (`&&`), Logical OR (`||`), and `null` to decide what should be rendered.**

---

# ⭐ Senior Interview Tip

One of the most common React interview questions is:

### **Which should you use: `if`, `&&`, or the Ternary Operator?**

**Answer**

- ✅ Use **if** for complex conditions.
- ✅ Use the **Ternary Operator** when there are exactly two possible outcomes.
- ✅ Use **&&** when you want to display something only if a condition is true.
- ✅ Return **null** when the component should not render anything.

Choosing the appropriate approach makes your React components easier to read, maintain, and debug.