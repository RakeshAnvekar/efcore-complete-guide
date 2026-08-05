# 🚀 React Module 10 – React Router

> **Difficulty:** ⭐⭐⭐⭐⭐
>
> **Interview Level:** Beginner → Mid → Senior React Developer
>
> **Key Concept:** React Router is a client-side routing library that enables navigation between pages without refreshing the browser. It allows React applications to behave like Single Page Applications (SPA).

---

# 📚 Table of Contents

- 🎯 What is Routing?
- 🎯 Why Do We Need React Router?
- 🔹 SPA vs MPA
- 🔹 Installing React Router
- 🔹 BrowserRouter
- 🔹 Routes
- 🔹 Route
- 🔹 Link
- 🔹 NavLink
- 🔹 useNavigate()
- 🔹 useParams()
- 🔹 useLocation()
- 🔹 Query Parameters
- 🔹 Nested Routes
- 🔹 Protected Routes
- 🔹 Lazy Loading
- 🌍 Complete Project Structure
- 💻 Complete Routing Example
- 📊 Link vs NavLink
- 📊 BrowserRouter vs HashRouter
- 📊 Authentication vs Authorization
- ✅ Best Practices
- 🎤 Common Interview Questions
- 📌 Summary
- 🎯 Interview One-Liner

---

# 🎯 What is Routing?

Routing means displaying different pages based on the URL **without refreshing the browser**.

Example

```
/

/home

/employees

/reports

/settings
```

Each URL loads a different React Component.

---

# 🎯 Why Do We Need React Router?

Imagine an Employee Portal.

```
Dashboard

↓

Employees

↓

Projects

↓

Reports

↓

Settings
```

Without Routing

```
Everything

↓

One Component

↓

Very Difficult To Manage
```

React Router maps each URL to a separate component.

```
URL

↓

React Router

↓

Correct Component

↓

Display Page
```

---

# 📊 SPA vs MPA

## Multi Page Application

```
User Click

↓

Request Server

↓

Server Returns HTML

↓

Entire Page Reloads
```

Examples

- ASP.NET MVC
- PHP
- JSP

---

## Single Page Application

```
User Click

↓

React Router

↓

Component Changes

↓

No Page Refresh
```

Advantages

- Fast Navigation
- Better User Experience
- Less Server Load

---

# 🔹 Install

```bash
npm install react-router-dom
```

---

# 🔹 BrowserRouter

BrowserRouter enables routing.

```jsx
import { BrowserRouter } from "react-router-dom";

<BrowserRouter>

    <App />

</BrowserRouter>
```

Execution

```
Browser URL

↓

BrowserRouter

↓

Route Matching

↓

Render Component
```

---

# 🔹 Routes

Routes is the container for all routes.

```jsx
<Routes>

    <Route />

    <Route />

</Routes>
```

---

# 🔹 Route

Maps a URL to a Component.

```jsx
<Routes>

<Route

path="/"

element={<Home/>}

/>

<Route

path="/employees"

element={<Employees/>}

/>

</Routes>
```

Execution

```
URL

↓

/employees

↓

Employees Component
```

---

# 🔹 Link

Navigation without refreshing the page.

```jsx
<Link to="/employees">

Employees

</Link>
```

Instead of

```html
<a href="/employees">

Employees

</a>
```

Execution

```
Click

↓

React Router

↓

URL Updated

↓

Component Updated

↓

No Refresh
```

---

# 🔹 NavLink

Works like Link but automatically knows whether it is active.

```jsx
<NavLink to="/employees">

Employees

</NavLink>
```

Useful for

- Navigation Menu
- Sidebar
- Active Styling

---

# 🔹 useNavigate()

Navigate programmatically.

```jsx
const navigate = useNavigate();

function save(){

    navigate("/employees");

}
```

Execution

```
Save Success

↓

Navigate

↓

Employees Page
```

---

# 🔹 useParams()

Read values from URL.

Route

```jsx
<Route

path="/employees/:id"

element={<EmployeeDetails/>}

/>
```

URL

```
/employees/101
```

Read

```jsx
const { id } = useParams();
```

Output

```
101
```

---

# 🔹 useLocation()

Current URL information.

```jsx
const location = useLocation();

console.log(location.pathname);
```

Output

```
/employees
```

Useful for

- Breadcrumb
- Active Menu
- Analytics

---

# 🔹 Query Parameters

URL

```
/employees?page=2
```

```jsx
const location = useLocation();

const params =

new URLSearchParams(location.search);

const page = params.get("page");
```

Output

```
2
```

---

# 🔹 Nested Routes

```
Dashboard

↓

Employees

↓

Employee Details
```

```jsx
<Route

path="/employees"

element={<Employees/>}

>

<Route

path="details"

element={<Details/>}

/>

</Route>
```

Result

```
/employees/details
```

---

# 🚀 Protected Route

## What is Protected Route?

A Protected Route allows **only authenticated users** to access a page.

Without login

```
User

↓

/dashboard

↓

Redirect

↓

Login
```

After Login

```
User

↓

/dashboard

↓

Dashboard
```

---

# ProtectedRoute Component

```jsx
import { Navigate } from "react-router-dom";

function ProtectedRoute({ children }) {

    const token = localStorage.getItem("token");

    if (!token) {

        return <Navigate to="/login" />;

    }

    return children;

}

export default ProtectedRoute;
```

---

# Route Configuration

Instead of

```jsx
<Route

path="/dashboard"

element={<Dashboard/>}

/>
```

Use

```jsx
<Route

path="/dashboard"

element={

<ProtectedRoute>

<Dashboard/>

</ProtectedRoute>

}

/>
```

Execution

```
User

↓

Dashboard URL

↓

ProtectedRoute

↓

Token Exists?

↓

YES

↓

Dashboard

----------------

NO

↓

Navigate

↓

Login
```

---

# Login Example

```jsx
import { useNavigate } from "react-router-dom";

function Login(){

const navigate = useNavigate();

function login(){

localStorage.setItem(

"token",

"abc123"

);

navigate("/dashboard");

}

return(

<button

onClick={login}

>

Login

</button>

);

}
```

---

# Logout

```jsx
function logout(){

localStorage.removeItem("token");

}
```

Now

```
Dashboard

↓

Blocked
```

---

# 🌍 Complete Project Structure

```
src

│

├── App.jsx

├── main.jsx

│

├── pages

│   ├── Home.jsx

│   ├── About.jsx

│   ├── Login.jsx

│   ├── Dashboard.jsx

│   ├── Employees.jsx

│   ├── EmployeeDetails.jsx

│   └── NotFound.jsx

│

├── layout

│   └── Layout.jsx

│

├── components

│   ├── Navigation.jsx

│   └── ProtectedRoute.jsx

│

└── routes

    └── AppRoutes.jsx
```

---

# 📊 Link vs NavLink

| Link | NavLink |
|-------|----------|
| Navigation | Navigation + Active Styling |
| No Active State | Knows Current Route |
| Basic Navigation | Sidebar/Menu |

---

# 📊 BrowserRouter vs HashRouter

| BrowserRouter | HashRouter |
|---------------|------------|
| Clean URLs | Uses # |
| History API | Hash Based |
| Most Common | Legacy Apps |

Example

BrowserRouter

```
/employees
```

HashRouter

```
#/employees
```

---

# 📊 Authentication vs Authorization

| Authentication | Authorization |
|---------------|---------------|
| Login | Permission |
| Who are you? | What can you access? |
| JWT Token | Roles |

---

# 🌍 Real Project Flow

```
Application Starts

↓

BrowserRouter

↓

Routes

↓

URL Matching

↓

Protected Route?

↓

Yes

↓

Token?

↓

Yes

↓

Dashboard

----------------

No

↓

Login
```

---

# ✅ Best Practices

✔ Use BrowserRouter

✔ Use Link instead of `<a>`

✔ Use NavLink for Menu

✔ Protect Secure Pages

✔ Use Route Parameters

✔ Lazy Load Large Pages

✔ Store JWT Securely

✔ Validate Token on Server

---

# 🎤 Common Interview Questions

## What is React Router?

A client-side routing library for React applications.

---

## What is BrowserRouter?

A component that enables routing using the browser's history API.

---

## Difference between Link and NavLink?

Link performs navigation.

NavLink performs navigation and supports active styling.

---

## Difference between Link and `<a>`?

Link changes the route without refreshing the page.

`<a>` refreshes the entire page.

---

## What is useNavigate?

A Hook used for programmatic navigation.

---

## What is useParams?

Reads values from route parameters.

---

## What is useLocation?

Provides information about the current URL.

---

## What is a Protected Route?

A route that allows only authenticated users to access the page.

---

## Why do we use Protected Routes?

To prevent unauthorized users from accessing secure pages.

---

# 📌 Summary

| Scenario | Recommended |
|----------|-------------|
| Enable Routing | BrowserRouter |
| Route Container | Routes |
| URL Mapping | Route |
| Navigation | Link |
| Active Menu | NavLink |
| Navigate Programmatically | useNavigate |
| Route Parameter | useParams |
| Current URL | useLocation |
| Secure Page | ProtectedRoute |
| Lazy Load Page | React.lazy |

---

# 🎯 Interview One-Liner

> **React Router is a client-side routing library that enables navigation between components without reloading the browser. It provides BrowserRouter, Routes, Route, Link, NavLink, and routing hooks like useNavigate, useParams, and useLocation to build modern Single Page Applications. Protected Routes restrict access to authenticated users by checking authentication before rendering secure pages.**

---

# ⭐ Senior Interview Tip

One of the most common interview questions is:

## **Why isn't hiding the Dashboard menu enough?**

### Answer

Hiding the menu only removes the navigation link.

A user can still manually type

```
/dashboard
```

in the browser.

A **Protected Route** verifies authentication **before rendering** the component and redirects unauthenticated users to the Login page.

**Remember:** Protecting the UI is not enough. Your **ASP.NET Core APIs** must also validate the JWT using `[Authorize]` to ensure the backend remains secure.