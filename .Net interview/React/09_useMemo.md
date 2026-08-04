# 🚀 React Module 7 – Part 4 – useMemo

> **Difficulty:** ⭐⭐⭐⭐⭐
>
> **Interview Level:** Mid → Senior React Developer
>
> **Key Concept:** `useMemo` is a React Hook used to **memoize (cache) the result of an expensive calculation**. It recalculates the value only when its dependencies change, improving performance by avoiding unnecessary computations.

---

# 📚 Table of Contents

- 🎯 What is useMemo?
- 🎯 Why Do We Need useMemo?
- 🔹 What is Memoization?
- 🔹 Expensive Computation
- 🔹 Syntax
- 🔹 How useMemo Works
- 🔹 Without useMemo
- 🔹 With useMemo
- 🔹 Dependency Array
- 🔹 Real World Examples
- 🔹 useMemo vs useCallback
- 🔹 useMemo vs React.memo
- 📊 Without useMemo vs With useMemo
- 📊 useMemo vs useCallback vs React.memo
- ✅ Best Practices
- 🎤 Common Interview Questions
- 📌 Summary
- 🎯 Interview One-Liner

---

# 🎯 What is useMemo?

`useMemo` is a **React Hook** that **caches the result of an expensive calculation**.

Instead of executing the calculation on every render, React stores the previously calculated value and reuses it until one of its dependencies changes.

Think of `useMemo` as a **cache**.

```
Expensive Calculation

↓

Store Result

↓

Reuse Cached Result

↓

No Recalculation
```

---

# 🎯 Why Do We Need useMemo?

Imagine an Employee Dashboard.

The application contains **50,000 employees**.

Whenever the user types into a search box

```
Component Re-renders

↓

Filter Employees

↓

Sort Employees

↓

Calculate Total Salary

↓

Render UI
```

Even if the employee list has not changed, React repeats the expensive calculations.

This slows the application.

---

# React Solution

```
Component Render

↓

useMemo

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

# 🔹 What is Memoization?

**Memoization** means storing the result of a calculation so it can be reused later.

Instead of

```
Calculate

↓

Calculate Again

↓

Calculate Again
```

React performs

```
Calculate Once

↓

Cache Result

↓

Reuse Cached Result
```

---

# 🔹 What is an Expensive Computation?

An expensive computation is an operation that consumes noticeable CPU time.

Examples

- Sorting Thousands of Records
- Filtering Large Collections
- Complex Mathematical Calculations
- Aggregating Reports
- Parsing Large JSON Files
- Calculating Statistics

---

# 🔹 Syntax

```jsx
const memoizedValue = useMemo(() => {

    return expensiveCalculation();

}, [dependencies]);
```

Structure

```
useMemo(

Callback Function,

Dependency Array

)
```

---

# 🔹 How useMemo Works

Without useMemo

```
Component Render

↓

Calculate Value

↓

Component Re-render

↓

Calculate Again

↓

Component Re-render

↓

Calculate Again
```

---

With useMemo

```
Component Render

↓

Calculate Value

↓

Store Cached Result

↓

Component Re-render

↓

Reuse Cached Result

↓

No Calculation
```

---

# 🔹 Example Without useMemo

```jsx
function App() {

    const [count, setCount] = useState(0);

    function calculate() {

        console.log("Calculating...");

        return 1000000 * 1000000;

    }

    return (

        <>

            <h2>{calculate()}</h2>

            <button

                onClick={() => setCount(count + 1)}

            >

                {count}

            </button>

        </>

    );

}
```

Output

```
Calculating...

↓

Click Button

↓

Calculating Again

↓

Click Again

↓

Calculating Again
```

The expensive calculation runs after every render.

---

# 🔹 Example With useMemo

```jsx
function App() {

    const [count, setCount] = useState(0);

    const result = useMemo(() => {

        console.log("Calculating...");

        return 1000000 * 1000000;

    }, []);

    return (

        <>

            <h2>{result}</h2>

            <button

                onClick={() => setCount(count + 1)}

            >

                {count}

            </button>

        </>

    );

}
```

Output

```
Calculating...

↓

Click Button

↓

No Calculation

↓

Click Again

↓

No Calculation
```

The value is reused from memory.

---

# 🔹 Dependency Array

Suppose

```jsx
const total = useMemo(() => {

    return price * quantity;

}, [price, quantity]);
```

Execution

```
Initial Render

↓

Calculate

↓

price Changes

↓

Calculate Again

↓

quantity Changes

↓

Calculate Again

↓

Other State Changes

↓

Reuse Cached Result
```

Only dependency changes trigger recalculation.

---

# 🔹 Real World Example – Employee Search

```jsx
const filteredEmployees = useMemo(() => {

    return employees.filter(employee =>

        employee.name.includes(search)

    );

}, [employees, search]);
```

Filtering executes only when

- Employee List Changes
- Search Text Changes

Changing another state does not trigger filtering.

---

# 🔹 Real World Example – Sorting

```jsx
const sortedEmployees = useMemo(() => {

    return [...employees].sort(

        (a, b) =>

        a.salary - b.salary

    );

}, [employees]);
```

Sorting occurs only when the employee list changes.

---

# 🔹 useMemo vs useCallback

## useMemo

Returns a cached value.

```jsx
const total = useMemo(() => {

    return calculateTotal();

}, []);
```

---

## useCallback

Returns a cached function.

```jsx
const handleClick = useCallback(() => {

    console.log("Clicked");

}, []);
```

---

# 🔹 useMemo vs React.memo

## useMemo

Caches the result of a calculation.

```
Expensive Calculation

↓

Cache Result
```

---

## React.memo

Caches an entire component.

```
Parent Re-render

↓

Skip Child Rendering
```

---

# 📊 Without useMemo vs With useMemo

| Without useMemo | With useMemo |
|-----------------|--------------|
| Recalculates Every Render | Reuses Cached Value |
| Slower for Expensive Operations | Faster |
| No Memoization | Memoized Result |
| Higher CPU Usage | Lower CPU Usage |

---

# 📊 useMemo vs useCallback vs React.memo

| Feature | useMemo | useCallback | React.memo |
|----------|----------|-------------|------------|
| Returns | Value | Function | Component |
| Memoizes | Calculation Result | Function Reference | Component |
| Prevents | Recalculation | Function Recreation | Component Re-render |
| Primary Use | Performance Optimization | Stable Callback | Component Optimization |

---

# 🌍 Real Project Example

Employee Dashboard

```
Employee List

↓

Filter Employees

↓

Sort Employees

↓

Calculate Total Salary

↓

Display Report
```

Without `useMemo`, every button click recalculates everything.

With `useMemo`

```
Render

↓

Dependencies Changed?

↓

No

↓

Reuse Cached Result
```

The UI remains fast.

---

# ❌ Common Mistakes

## Using useMemo for Everything

```jsx
const total = useMemo(() => a + b, [a, b]);
```

Addition is inexpensive.

No performance benefit.

---

## Forgetting Dependencies

```jsx
useMemo(() => {

    return price * quantity;

}, []);
```

Now the value never updates.

---

## Memoizing Simple Values

```jsx
const message = useMemo(() => "Hello", []);
```

Completely unnecessary.

---

# ✅ Best Practices

✔ Use useMemo only for expensive calculations.

✔ Always include every dependency.

✔ Avoid unnecessary memoization.

✔ Measure performance before optimizing.

✔ Keep the dependency array accurate.

---

# 🎤 Common Interview Questions

## What is useMemo?

A Hook that memoizes the result of an expensive calculation.

---

## Why do we use useMemo?

To avoid recalculating expensive operations during every render.

---

## Does useMemo prevent re-rendering?

No.

It prevents recalculation, not rendering.

---

## When does useMemo recalculate?

Whenever one of its dependencies changes.

---

## Difference between useMemo and useCallback?

useMemo returns a cached value.

useCallback returns a cached function.

---

## Difference between useMemo and React.memo?

useMemo memoizes values.

React.memo memoizes components.

---

## Should we use useMemo everywhere?

No.

Only use it when performance benefits outweigh the memoization overhead.

---

# 📌 Summary

| Scenario | Recommended |
|----------|-------------|
| Expensive Calculation | useMemo |
| Filter Large Data | useMemo |
| Sort Large Data | useMemo |
| Aggregate Reports | useMemo |
| Simple Addition | ❌ No useMemo |
| Cache Function | useCallback |
| Prevent Child Re-render | React.memo |

---

# 🎯 Interview One-Liner

> **`useMemo` is a React Hook that memoizes the result of an expensive calculation. It recalculates the value only when its dependencies change, improving performance by avoiding unnecessary computations during component re-renders.**

---

# ⭐ Senior Interview Tip

One of the most frequently asked interview questions is:

## **Does useMemo prevent component re-rendering?**

### Answer

**No.**

`useMemo` **does not prevent the component from re-rendering**.

It only prevents **expensive calculations from running again**.

The component will still re-render whenever:

- State changes
- Props change
- Context changes

`useMemo` simply returns the previously cached value if its dependencies haven't changed.

---

# 🧠 Interview Trick Question

## **Should every calculation be wrapped inside useMemo?**

### Answer

**No.**

`useMemo` itself has a small performance cost.

Use it only when:

- The calculation is expensive.
- The value is reused across renders.
- Profiling shows a measurable performance improvement.

Using `useMemo` for simple operations like string concatenation or `a + b` usually provides no benefit and can make the code more complex.