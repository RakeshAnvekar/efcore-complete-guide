# 🚀 React Module 7 – Part 6 – Context API

> **Difficulty:** ⭐⭐⭐⭐☆
>
> **Interview Level:** Beginner → Mid → Senior React Developer
>
> **Key Concept:** Context API is a built-in React feature that allows data to be shared across multiple components **without passing props manually through every level (Prop Drilling)**.

---

# 📚 Table of Contents

- 🎯 What is Context API?
- 🎯 Why Do We Need Context API?
- 🔹 What is Prop Drilling?
- 🔹 How Context API Works
- 🔹 createContext()
- 🔹 Provider
- 🔹 useContext()
- 🔹 Complete Example
- 🔹 Multiple Contexts
- 🌍 Real World Examples
- 📊 Context API vs Prop Drilling
- 📊 Context API vs Redux
- 📊 createContext vs Provider vs useContext
- ✅ Best Practices
- 🎤 Common Interview Questions
- 📌 Summary
- 🎯 Interview One-Liner

---

# 🎯 What is Context API?

**Context API** is a built-in React feature that allows data to be shared between components **without passing props through every intermediate component.**

It acts like a **shared data store** for a section of your application.

Common Examples

- Logged-in User
- Theme (Dark / Light)
- Language
- JWT Token
- Shopping Cart
- Application Settings

---

# 🎯 Why Do We Need Context API?

Suppose we have the following component hierarchy.

```
App

↓

Dashboard

↓

EmployeePage

↓

EmployeeCard

↓

EmployeeDetails
```

The logged-in user is available inside **App**.

```jsx
const user = {

    name: "John"

};
```

Only **EmployeeDetails** needs the user.

Without Context API

```
App

↓

Dashboard

↓

EmployeePage

↓

EmployeeCard

↓

EmployeeDetails
```

Every component receives

```jsx
user
```

even though only the last component uses it.

This is called **Prop Drilling**.

---

# 🔹 What is Prop Drilling?

Prop Drilling means passing props through multiple components just to reach a deeply nested child component.

Example

```jsx
<App user={user}>

↓

<Dashboard user={user}>

↓

<EmployeePage user={user}>

↓

<EmployeeCard user={user}>

↓

<EmployeeDetails user={user}/>
```

Problems

❌ Too many props

❌ Hard to maintain

❌ Tight coupling

❌ Difficult to understand

---

# React Solution

Instead of passing props

```
Context

↓

Provider

↓

Any Child Component

↓

Read Data Directly
```

No intermediate components are involved.

---

# 🔹 How Context API Works

Context API follows three simple steps.

```
Create Context

↓

Provider Stores Data

↓

Child Component Reads Data

↓

useContext()
```

Think of Context as a **shared storage**.

---

# 🔹 createContext()

The first step is creating a Context.

Create

```jsx
import { createContext } from "react";

const UserContext = createContext();

export default UserContext;
```

Execution

```
React

↓

Creates Context Object

↓

Ready to Store Shared Data
```

---

# 🔹 Provider

The Provider supplies data to all child components.

```jsx
<UserContext.Provider value={user}>

    <App/>

</UserContext.Provider>
```

Execution

```
Provider

↓

Stores Value

↓

Every Child Can Access It
```

Everything inside the Provider can read

```jsx
user
```

---

# 🔹 useContext()

The `useContext` Hook is used to consume the shared data.

```jsx
import { useContext } from "react";

import UserContext from "./UserContext";

function Profile(){

    const user = useContext(UserContext);

    return <h2>{user.name}</h2>;

}
```

Output

```
John
```

No props required.

---

# 🔹 Complete Example

## Step 1

### UserContext.js

```jsx
import { createContext } from "react";

const UserContext = createContext();

export default UserContext;
```

---

## Step 2

### App.jsx

```jsx
import UserContext from "./UserContext";

import Dashboard from "./Dashboard";

function App(){

    const user = {

        name: "John"

    };

    return(

        <UserContext.Provider value={user}>

            <Dashboard/>

        </UserContext.Provider>

    );

}

export default App;
```

---

## Step 3

### Dashboard.jsx

```jsx
import Employee from "./Employee";

function Dashboard(){

    return <Employee/>;

}

export default Dashboard;
```

Notice

No props are passed.

---

## Step 4

### Employee.jsx

```jsx
import Profile from "./Profile";

function Employee(){

    return <Profile/>;

}

export default Employee;
```

Again

No props.

---

## Step 5

### Profile.jsx

```jsx
import { useContext } from "react";

import UserContext from "./UserContext";

function Profile(){

    const user = useContext(UserContext);

    return(

        <h2>

            Welcome {user.name}

        </h2>

    );

}

export default Profile;
```

Execution

```
App

↓

Provider

↓

Dashboard

↓

Employee

↓

Profile

↓

useContext()

↓

John
```

No Prop Drilling.

---

# 🔹 Updating Context

Context values can also be updated.

```jsx
const [theme, setTheme] = useState("Light");

<ThemeContext.Provider

value={{

theme,

setTheme

}}

>
```

Child Component

```jsx
const {

theme,

setTheme

}

=

useContext(ThemeContext);

<button

onClick={() => setTheme("Dark")}

>

Dark Theme

</button>
```

Execution

```
Button Click

↓

setTheme()

↓

Provider Updates

↓

All Consumers Re-render
```

---

# 🔹 Multiple Contexts

Instead of one huge Context

❌ Bad

```
User

Theme

Language

Cart

Products

↓

One Context
```

Better

```
UserContext

ThemeContext

LanguageContext

CartContext
```

Each Context has a single responsibility.

---

# 🌍 Real World Example

Authentication

```
Login

↓

JWT Token

↓

UserContext

↓

Navbar

↓

Dashboard

↓

Profile

↓

Settings
```

Every component can access the logged-in user.

---

## Theme Switching

```
Dark Theme

↓

ThemeContext

↓

Navbar

↓

Sidebar

↓

Footer
```

Every component knows the current theme.

---

## Shopping Cart

```
Product

↓

CartContext

↓

Navbar

↓

Checkout

↓

Payment
```

Every page sees the same cart.

---

# 📊 Context API vs Prop Drilling

| Prop Drilling | Context API |
|---------------|-------------|
| Pass Props Manually | Shared Automatically |
| Difficult | Simple |
| Lots of Props | No Intermediate Props |
| Hard to Maintain | Easy to Maintain |

---

# 📊 Context API vs Redux

| Context API | Redux |
|--------------|--------|
| Built into React | External Library |
| Easy Setup | More Configuration |
| Authentication | Complex State |
| Theme | Large Applications |
| Language | Enterprise State Management |

---

# 📊 createContext vs Provider vs useContext

| Feature | Purpose |
|----------|----------|
| createContext() | Creates Context |
| Provider | Shares Data |
| useContext() | Reads Data |

---

# 🌍 Real Project Flow

```
Application Starts

↓

User Logs In

↓

JWT Token Received

↓

UserContext

↓

Navbar

↓

Dashboard

↓

Profile

↓

Settings
```

Every component receives the same data.

---

# ❌ Common Mistakes

## Using Context for Local State

```jsx
const [name,setName]=useState("");
```

No need for Context.

---

## Forgetting Provider

```jsx
const user = useContext(UserContext);
```

Without Provider

Context returns its default value.

---

## One Giant Context

Avoid storing everything in one Context.

Split it into multiple contexts.

---

# ✅ Best Practices

✔ Create separate contexts for User, Theme and Cart.

✔ Use Context only for shared global data.

✔ Avoid storing frequently changing local state.

✔ Wrap only the required components with Provider.

✔ Keep Context focused on one responsibility.

---

# 🎤 Common Interview Questions

## What is Context API?

A built-in React feature that shares data between components without Prop Drilling.

---

## What is Prop Drilling?

Passing props through multiple intermediate components that don't actually use them.

---

## What is createContext()?

It creates a Context object.

---

## What is Provider?

A component that stores and shares Context values.

---

## What is useContext()?

A Hook that reads values from the nearest Provider.

---

## Can Context values be updated?

Yes.

Usually by storing state inside the Provider and exposing both the value and setter.

---

## Difference between Context API and Redux?

Context API is ideal for simple shared state.

Redux is better for complex enterprise applications.

---

# 📌 Summary

| Scenario | Recommended |
|----------|-------------|
| Authentication | Context API |
| Logged-in User | Context API |
| Theme | Context API |
| Language | Context API |
| Shopping Cart (Small App) | Context API |
| Enterprise Global State | Redux |
| Avoid Prop Drilling | Context API |

---

# 🎯 Interview One-Liner

> **Context API is a built-in React feature that allows data to be shared across a component tree without manually passing props through every level. It solves the Prop Drilling problem using `createContext`, `Provider`, and the `useContext` Hook.**

---

# ⭐ Senior Interview Tip

One of the most common interview questions is:

## **Does Context API replace Redux?**

### Answer

**No.**

Use **Context API** for relatively simple shared state such as:

- Authentication
- Logged-in User
- Theme
- Language
- User Preferences

Use **Redux** when the application has:

- Complex state management
- Frequent updates
- Large teams
- Advanced debugging requirements
- Middleware support

For most React applications, **Context API is sufficient** for authentication, themes, and other shared application data.