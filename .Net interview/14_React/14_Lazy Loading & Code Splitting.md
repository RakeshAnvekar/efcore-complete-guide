# 🚀 React Module 12 – Lazy Loading & Code Splitting

> **Difficulty:** ⭐⭐⭐⭐☆
>
> **Interview Level:** Mid → Senior React Developer
>
> **Key Concept:** Lazy Loading loads React components **only when they are needed**, while Code Splitting divides a large JavaScript bundle into multiple smaller bundles. Together they significantly improve application performance.

---

# 📚 Table of Contents

- 🎯 What is Lazy Loading?
- 🎯 Why Do We Need Lazy Loading?
- 🔹 Without Lazy Loading
- 🔹 With Lazy Loading
- 🔹 React.lazy()
- 🔹 Suspense
- 🔹 Complete Example
- 🔹 What Happens Internally?
- 🔹 Real-Time Example
- 🔹 Code Splitting
- 🔹 Lazy Loading vs Code Splitting
- 📊 Bundle Comparison
- 📊 When to Use Lazy Loading
- ✅ Best Practices
- 🎤 Common Interview Questions
- 📌 Summary
- 🎯 Interview One-Liner

---

# 🎯 What is Lazy Loading?

Lazy Loading is a technique where **React loads components only when they are required** instead of downloading every component during the initial application load.

Instead of downloading

```
Login

Dashboard

Employees

Reports

Settings

Admin
```

React downloads

```
Login

↓

User Opens Dashboard

↓

Download Dashboard

↓

User Opens Employees

↓

Download Employees
```

Only required components are downloaded.

---

# 🎯 Why Do We Need Lazy Loading?

Imagine an Employee Management System.

Pages

```
Login

Dashboard

Employees

Reports

Projects

Settings

Admin

Profile
```

Without Lazy Loading

```
Application Starts

↓

Download Every Page

↓

8 MB Bundle

↓

Show Login
```

Even though the user only wants the Login page.

This increases

- Initial Load Time
- Bundle Size
- Memory Usage

---

# 🔹 Without Lazy Loading

```jsx
import Login from "./pages/Login";
import Dashboard from "./pages/Dashboard";
import Employees from "./pages/Employees";
import Reports from "./pages/Reports";
import Settings from "./pages/Settings";
import Admin from "./pages/Admin";
```

Execution

```
Application Starts

↓

Download Login

↓

Download Dashboard

↓

Download Employees

↓

Download Reports

↓

Download Settings

↓

Download Admin

↓

Render Login
```

Everything is downloaded immediately.

---

# 🔹 With Lazy Loading

```jsx
import { lazy } from "react";

const Login = lazy(() => import("./pages/Login"));

const Dashboard = lazy(() => import("./pages/Dashboard"));

const Employees = lazy(() => import("./pages/Employees"));

const Reports = lazy(() => import("./pages/Reports"));

const Settings = lazy(() => import("./pages/Settings"));

const Admin = lazy(() => import("./pages/Admin"));
```

Execution

```
Application Starts

↓

Download Login

↓

Render Login

↓

User Opens Dashboard

↓

Download Dashboard

↓

Render Dashboard
```

Only the requested page is downloaded.

---

# 🔹 React.lazy()

`React.lazy()` tells React to load a component only when it is first rendered.

Syntax

```jsx
const Dashboard = lazy(() => import("./Dashboard"));
```

Instead of importing immediately

```jsx
import Dashboard from "./Dashboard";
```

React downloads it later.

---

# 🔹 Suspense

While React downloads the component,

it displays a fallback UI.

```jsx
import { Suspense } from "react";

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

Downloading Dashboard

↓

Loading...

↓

Download Complete

↓

Dashboard Displayed
```

---

# 🔹 Complete Example

```jsx
import {

BrowserRouter,

Routes,

Route

}

from "react-router-dom";

import {

lazy,

Suspense

}

from "react";

const Login = lazy(() => import("./pages/Login"));

const Dashboard = lazy(() => import("./pages/Dashboard"));

const Employees = lazy(() => import("./pages/Employees"));

const Reports = lazy(() => import("./pages/Reports"));

function App(){

return(

<BrowserRouter>

<Suspense

fallback={<h2>Loading...</h2>}

>

<Routes>

<Route

path="/"

element={<Login/>}

/>

<Route

path="/dashboard"

element={<Dashboard/>}

/>

<Route

path="/employees"

element={<Employees/>}

/>

<Route

path="/reports"

element={<Reports/>}

/>

</Routes>

</Suspense>

</BrowserRouter>

);

}

export default App;
```

---

# 🔹 What Happens Internally?

Application Starts

```
User Opens Website

↓

Download Login.js

↓

Render Login
```

User opens Dashboard

```
Click Dashboard

↓

Dashboard Bundle Not Available

↓

Download Dashboard.js

↓

Render Dashboard
```

User opens Reports

```
Click Reports

↓

Download Reports.js

↓

Render Reports
```

React downloads only what is required.

---

# 🌍 Real-Time Example

## Employee Management System

Pages

```
Login

Dashboard

Employees

Reports

Projects

Admin
```

Without Lazy Loading

```
Application Starts

↓

Download All Pages

↓

8 MB Download

↓

Show Login
```

---

With Lazy Loading

```
Application Starts

↓

Download Login

↓

300 KB

↓

User Logs In

↓

Dashboard Downloaded

↓

600 KB

↓

User Opens Employees

↓

Employees Downloaded

↓

400 KB

↓

User Opens Reports

↓

Reports Downloaded

↓

700 KB
```

Only required pages are downloaded.

---

# 🌍 Banking Application Example

Suppose an Internet Banking application has

```
Home

Accounts

Transactions

Loans

Insurance

Credit Cards

Investments
```

Without Lazy Loading

```
Download Entire Banking Application

↓

20 MB
```

With Lazy Loading

```
Home Downloaded

↓

User Opens Loans

↓

Download Loan Module

↓

User Opens Insurance

↓

Download Insurance Module
```

Much faster startup.

---

# 🔹 Code Splitting

Code Splitting divides one large JavaScript bundle into multiple smaller bundles.

Without Code Splitting

```
bundle.js

20 MB

↓

Download Everything
```

With Code Splitting

```
Login.js

Dashboard.js

Employees.js

Reports.js

Settings.js
```

Each page becomes a separate bundle.

---

# 📊 Lazy Loading vs Code Splitting

| Lazy Loading | Code Splitting |
|--------------|----------------|
| Loads Components On Demand | Splits JavaScript into Multiple Bundles |
| Runtime Optimization | Build Time Optimization |
| Uses React.lazy() | Performed by Vite/Webpack |
| Improves Initial Load Time | Reduces Bundle Size |

---

# 📊 Bundle Comparison

## Without Lazy Loading

```
Application Starts

↓

Download

Login

Dashboard

Employees

Reports

Settings

Admin

↓

8 MB
```

---

## With Lazy Loading

```
Application Starts

↓

Login

300 KB

↓

Dashboard

600 KB

↓

Employees

400 KB

↓

Reports

700 KB
```

Downloads happen only when required.

---

# 📊 When Should We Use Lazy Loading?

| Use Lazy Loading | Avoid Lazy Loading |
|------------------|-------------------|
| Dashboard | Header |
| Reports | Footer |
| Admin | Navbar |
| Charts | Buttons |
| Analytics | Small Components |
| Settings | Login (Usually) |

---

# 🌍 Complete Flow

```
Application Starts

↓

Login Bundle Downloaded

↓

Login Success

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

↓

Admin Opened

↓

Admin Bundle Downloaded
```

---

# ❌ Common Mistakes

## Forgetting Suspense

```jsx
const Dashboard = lazy(() => import("./Dashboard"));
```

Without

```jsx
<Suspense>

</Suspense>
```

React throws an error.

---

## Lazy Loading Small Components

```jsx
Button

Header

Footer
```

No performance benefit.

---

## Lazy Loading Everything

Not every component needs lazy loading.

Focus on

- Pages
- Large Components
- Heavy Modules

---

# ✅ Best Practices

✔ Lazy load routes.

✔ Lazy load Dashboard pages.

✔ Lazy load Reports.

✔ Use Suspense.

✔ Split application into multiple bundles.

✔ Don't lazy load small reusable components.

---

# 🎤 Common Interview Questions

## What is Lazy Loading?

A technique that loads components only when they are required.

---

## Why do we use Lazy Loading?

To reduce the application's initial bundle size and improve startup performance.

---

## What is React.lazy()?

A React API used to lazy load components.

---

## Why do we need Suspense?

To display a fallback UI while a lazy-loaded component is being downloaded.

---

## What is Code Splitting?

Breaking a large JavaScript bundle into multiple smaller bundles.

---

## Difference between Lazy Loading and Code Splitting?

Code Splitting creates multiple bundles.

Lazy Loading decides when those bundles should be downloaded.

---

## Does Lazy Loading improve performance?

Yes.

It reduces initial load time and improves user experience, especially in large applications.

---

# 📌 Summary

| Scenario | Recommended |
|----------|-------------|
| Large Dashboard | Lazy Loading |
| Reports Module | Lazy Loading |
| Admin Module | Lazy Loading |
| Heavy Charts | Lazy Loading |
| Split JavaScript Files | Code Splitting |
| Show Loading UI | Suspense |
| Import On Demand | React.lazy() |

---

# 🎯 Interview One-Liner

> **Lazy Loading is a React performance optimization technique that loads components only when they are needed. It works together with Code Splitting, where the application is divided into smaller bundles, reducing the initial bundle size and improving application startup performance.**

---

# ⭐ Senior Interview Tip

One of the most common interview questions is:

## **Explain Lazy Loading with a real project example.**

### Answer

In an **Employee Management System**, users initially visit only the **Login** page. Without Lazy Loading, React downloads **Dashboard**, **Employees**, **Reports**, **Admin**, and **Settings** even though they are not immediately needed.

With **React.lazy()** and **Suspense**, only the **Login** page is downloaded first. After the user logs in and navigates to another page, React downloads only that page's bundle. This significantly reduces the initial bundle size, speeds up page load time, lowers memory usage, and provides a much better user experience.

**Easy Interview Rule**

- **Code Splitting** → Creates smaller JavaScript bundles.
- **Lazy Loading** → Loads those bundles only when they are needed.