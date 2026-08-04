# Entity Framework Core – Module 1
# Introduction to Change Tracker

> **Course:** Entity Framework Core Complete Interview Master Roadmap

**Module:** 1 – Introduction to Change Tracker

**Difficulty:** ⭐⭐⭐☆☆

**Interview Importance:** ⭐⭐⭐⭐⭐

---

# Table of Contents

1. What is Entity Framework?
2. Why Do We Need Change Tracker?
3. What is Change Tracker?
4. Real-Time Example
5. Internal Architecture
6. Internal Working
7. Memory Diagram
8. DbContext Lifecycle
9. SaveChanges() Flow
10. Why Change Tracker is Required
11. Interview Questions
12. Summary
13. Key Takeaways

---

# What is Entity Framework?

Entity Framework Core (EF Core) is Microsoft's Object Relational Mapper (ORM).

It allows developers to work with database tables using C# objects instead of writing SQL manually.

Without Entity Framework

```csharp
SqlConnection connection = new SqlConnection(connectionString);

SqlCommand command = new SqlCommand(
    "SELECT * FROM Employees WHERE Id = 1",
    connection);

connection.Open();

SqlDataReader reader = command.ExecuteReader();
```

With Entity Framework

```csharp
var employee = context.Employees.First(e => e.Id == 1);
```

No SQL writing.

No manual object mapping.

Everything is handled automatically.

---

# Then Why Do We Need Change Tracker?

Suppose we load an employee.

```csharp
var employee = context.Employees.First(e => e.Id == 1);
```

Now change the salary.

```csharp
employee.Salary = 70000;
```

Finally

```csharp
context.SaveChanges();
```

Question

How does Entity Framework know

```
Salary

changed?
```

Did we tell EF?

No.

Entity Framework already knows because of

```
Change Tracker
```

---

# Definition

**Change Tracker** is a component inside `DbContext` that tracks every entity loaded or attached to it.

It monitors:

- New entities
- Modified entities
- Deleted entities
- Unchanged entities

When `SaveChanges()` is called,

Change Tracker tells Entity Framework what SQL statements should be generated.

---

# Responsibilities of Change Tracker

The Change Tracker is responsible for

✔ Tracking entities

✔ Detecting property changes

✔ Tracking entity state

✔ Comparing original values with current values

✔ Generating INSERT

✔ Generating UPDATE

✔ Generating DELETE

---

# Real-Time Example

Imagine a school teacher taking attendance.

Morning Attendance

```
Rakesh

Present

Raj

Present

Amit

Present
```

During the day

```
Raj

Leaves Early
```

At the end of the day

Teacher knows

```
Raj

Changed

↓

Present

↓

Left Early
```

Why?

Because the teacher was tracking students.

Entity Framework works the same way.

---

# Banking Example

Database

```
Customer

------------------------

Id

Name

Balance

1

Rakesh

50000
```

Application

```csharp
var customer =
context.Customers.First(c => c.Id == 1);
```

Deposit

```csharp
customer.Balance += 10000;
```

Question

How does EF know

```
Balance changed?
```

Answer

```
Change Tracker
```

---

# Internal Architecture

```
                     DbContext

                          │

          ┌───────────────┴────────────────┐

          ▼                                ▼

      DbSet<Employee>               Change Tracker

                                           │

                              Tracks Every Entity

                                           │

                               Entity State

                      Added

                      Modified

                      Deleted

                      Unchanged

                      Detached
```

---

# Internal Working

Suppose

```csharp
var employee =
context.Employees.First(e => e.Id == 1);
```

Step 1

Entity Framework executes SQL.

```sql
SELECT *

FROM Employees

WHERE Id = 1;
```

Database returns

```
Employee

Id = 1

Name = Rakesh

Salary = 50000
```

Step 2

Entity Framework creates

```
Employee Object
```

Step 3

Change Tracker starts tracking the object.

It stores

```
Original Values

Current Values

Entity State
```

---

# Memory Diagram

Before Loading

```
Database

↓

Employee Record
```

After Loading

```
STACK

employee

↓

Reference

----------------------------

HEAP

Employee Object

Id = 1

Name = Rakesh

Salary = 50000

----------------------------

Change Tracker

Original Values

Id = 1

Name = Rakesh

Salary = 50000

Current Values

Id = 1

Name = Rakesh

Salary = 50000

State

Unchanged
```

---

# Modify the Object

```csharp
employee.Salary = 70000;
```

Memory becomes

```
STACK

employee

↓

Reference

----------------------------

HEAP

Employee Object

Salary = 70000

----------------------------

Change Tracker

Original Salary

50000

Current Salary

70000

State

Modified
```

Notice

Only the object changes.

The Change Tracker also knows what changed.

---

# SaveChanges()

When we call

```csharp
context.SaveChanges();
```

Entity Framework asks

```
Change Tracker

↓

Which entities changed?
```

Change Tracker replies

```
Employee

↓

Modified
```

Entity Framework generates

```sql
UPDATE Employees

SET Salary = 70000

WHERE Id = 1;
```

---

# Why Does Change Tracker Store Original Values?

Suppose

Original

```
Salary = 50000
```

Current

```
Salary = 70000
```

Without storing the original value,

Entity Framework would not know whether anything changed.

It compares

```
Original Values

↓

Current Values
```

If they are different,

Entity State becomes

```
Modified
```

---

# DbContext Lifecycle

```
Application Starts

↓

Create DbContext

↓

Load Entity

↓

Start Tracking

↓

Modify Entity

↓

Change Tracker Detects Changes

↓

SaveChanges()

↓

Generate SQL

↓

Execute SQL

↓

Dispose DbContext
```

When DbContext is disposed,

all tracked entities are removed from memory.

---

# One DbContext = One Change Tracker

Every DbContext instance owns its own Change Tracker.

Example

```csharp
using var context1 = new AppDbContext();

using var context2 = new AppDbContext();
```

Memory

```
DbContext 1

↓

Change Tracker 1

-------------------------

DbContext 2

↓

Change Tracker 2
```

Tracking is never shared.

---

# What Happens Without Change Tracker?

Suppose

```csharp
employee.Salary = 70000;
```

Without Change Tracker,

Entity Framework would not know

- Which object changed
- Which object is new
- Which object was deleted
- Which SQL statement to execute

It would have to compare every object with the database every time.

That would be extremely slow.

---

# Advantages of Change Tracker

✔ Automatic SQL Generation

✔ Tracks Modified Properties

✔ Tracks Entity State

✔ Reduces Database Reads

✔ Makes CRUD Operations Easy

✔ Supports Optimistic Concurrency

---

# Interview Questions

## What is Change Tracker?

Change Tracker is a component inside Entity Framework Core that tracks entities associated with a DbContext.

It detects changes made to those entities and helps Entity Framework generate INSERT, UPDATE, and DELETE statements during SaveChanges().

---

## Where does Change Tracker live?

Inside the DbContext.

---

## Does every DbContext have a Change Tracker?

Yes.

Each DbContext instance has its own Change Tracker.

---

## When does Change Tracker start tracking?

When an entity is

- Loaded from the database
- Attached to DbContext

---

## What information does Change Tracker store?

- Original Values
- Current Values
- Entity State

---

## What happens when DbContext is disposed?

The Change Tracker is also disposed.

No entities remain tracked.

---

## Why is Change Tracker required?

Without it,

Entity Framework cannot determine

- What changed
- What SQL should be generated
- Which entities are new or deleted

---

# Summary

The Change Tracker is one of the core components of Entity Framework Core.

It automatically tracks every entity associated with a DbContext, records its original and current values, and determines whether the entity is Added, Modified, Deleted, Unchanged, or Detached.

When SaveChanges() is called, Entity Framework uses the Change Tracker to generate the appropriate SQL statements.

Without Change Tracker, Entity Framework would not know how to synchronize changes between your C# objects and the database.

---

# Key Takeaways

✔ Change Tracker is part of DbContext.

✔ It tracks entities loaded or attached to the DbContext.

✔ It stores Original Values and Current Values.

✔ It determines Entity State.

✔ It enables automatic INSERT, UPDATE, and DELETE generation.

✔ One DbContext has one Change Tracker.

✔ Tracking ends when DbContext is disposed.

✔ Understanding Change Tracker is fundamental for mastering Entity Framework Core.

---

# Entity Framework Core – Module 2
# Entity States (Most Important)

> **Course:** Entity Framework Core Complete Interview Master Roadmap

**Module:** 2 – Entity States

**Difficulty:** ⭐⭐⭐⭐☆

**Interview Importance:** ⭐⭐⭐⭐⭐

---

# Table of Contents

1. What is Entity State?
2. Why Do We Need Entity States?
3. Five Entity States
4. Detached State
5. Added State
6. Unchanged State
7. Modified State
8. Deleted State
9. Entity State Lifecycle
10. Internal Working
11. SQL Generated by Each State
12. Memory Diagrams
13. State Transition Diagram
14. Checking Entity State
15. Real-Time Banking Example
16. Interview Questions
17. Summary
18. Key Takeaways

---

# What is Entity State?

Every entity tracked by Entity Framework Core has a **State**.

The state tells Entity Framework what should happen when

```csharp
context.SaveChanges();
```

is executed.

For example

```
Employee

↓

Modified

↓

Generate UPDATE SQL
```

or

```
Employee

↓

Added

↓

Generate INSERT SQL
```

Without Entity State,

Entity Framework would not know

- Which object is new
- Which object changed
- Which object should be deleted

---

# Why Do We Need Entity States?

Suppose we load an employee.

```csharp
var employee = context.Employees.First(e => e.Id == 1);
```

Now modify

```csharp
employee.Salary = 70000;
```

Question

How does Entity Framework know

```
Salary changed?
```

Answer

Because Change Tracker changes the entity state from

```
Unchanged

↓

Modified
```

---

# Five Entity States

Entity Framework Core has five entity states.

```
Detached

Added

Unchanged

Modified

Deleted
```

Every tracked entity is always in one of these states.

---

# Entity State Transition Diagram

```
                     Database

                         │

                   Load Entity

                         │

                  Unchanged

         ┌──────────┼───────────┐

         ▼          ▼           ▼

    Modified     Deleted    Detached

         │

         ▼

    SaveChanges()

         │

         ▼

    Unchanged

------------------------------------------

new Entity

↓

Detached

↓

Add()

↓

Added

↓

SaveChanges()

↓

Unchanged
```

---

# State 1 – Detached

## Definition

Detached means

The entity exists in memory,

but Entity Framework is **NOT** tracking it.

---

## Example

```csharp
Employee employee = new Employee
{
    Name = "Rakesh",
    Salary = 50000
};
```

State

```
Detached
```

Why?

Because

```
new Employee()
```

creates only a C# object.

Entity Framework does not know about it.

---

## Memory Diagram

```
STACK

employee

↓

Reference

----------------------

HEAP

Employee Object

↓

No Change Tracker
```

---

## SaveChanges()

```csharp
context.SaveChanges();
```

Nothing happens.

No SQL is generated.

---

# State 2 – Added

Suppose

```csharp
context.Employees.Add(employee);
```

State becomes

```
Added
```

---

## Memory Diagram

```
DbContext

↓

Change Tracker

↓

Employee

State

Added
```

---

## SaveChanges()

```csharp
context.SaveChanges();
```

Entity Framework generates

```sql
INSERT INTO Employees(Name,Salary)

VALUES('Rakesh',50000)
```

After successful insert

State becomes

```
Unchanged
```

---

# State 3 – Unchanged

Suppose

```csharp
var employee =
context.Employees.First(e=>e.Id==1);
```

Immediately after loading

State

```
Unchanged
```

Why?

Because

Original Values

=

Current Values

---

## Memory

```
Original Salary

50000

Current Salary

50000

↓

Unchanged
```

Nothing has changed.

---

# State 4 – Modified

Suppose

```csharp
employee.Salary = 70000;
```

Now

Original

```
50000
```

Current

```
70000
```

Entity Framework compares

```
Original

↓

Current
```

They are different.

State becomes

```
Modified
```

---

## Memory Diagram

```
Original

Salary =50000

↓

Current

Salary =70000

↓

State

Modified
```

---

## SaveChanges()

```csharp
context.SaveChanges();
```

SQL

```sql
UPDATE Employees

SET Salary=70000

WHERE Id=1;
```

After SaveChanges

State

```
Unchanged
```

---

# State 5 – Deleted

Suppose

```csharp
context.Employees.Remove(employee);
```

State

```
Deleted
```

---

## SaveChanges()

```csharp
context.SaveChanges();
```

Entity Framework generates

```sql
DELETE

FROM Employees

WHERE Id=1;
```

After successful delete

State becomes

```
Detached
```

Because the entity no longer exists in the database.

---

# Complete Entity Lifecycle

## Step 1

```csharp
Employee employee = new Employee();
```

State

```
Detached
```

---

## Step 2

```csharp
context.Add(employee);
```

State

```
Added
```

---

## Step 3

```csharp
context.SaveChanges();
```

State

```
Unchanged
```

---

## Step 4

```csharp
employee.Salary = 70000;
```

State

```
Modified
```

---

## Step 5

```csharp
context.SaveChanges();
```

State

```
Unchanged
```

---

## Step 6

```csharp
context.Remove(employee);
```

State

```
Deleted
```

---

## Step 7

```csharp
context.SaveChanges();
```

State

```
Detached
```

---

# Complete State Flow

```
new Employee()

↓

Detached

↓

Add()

↓

Added

↓

SaveChanges()

↓

Unchanged

↓

Modify Property

↓

Modified

↓

SaveChanges()

↓

Unchanged

↓

Remove()

↓

Deleted

↓

SaveChanges()

↓

Detached
```

---

# SQL Generated by Each State

| Entity State | SQL Generated |
|---------------|---------------|
| Detached | Nothing |
| Added | INSERT |
| Unchanged | Nothing |
| Modified | UPDATE |
| Deleted | DELETE |

---

# Internal Working

Suppose

```csharp
var employee =
context.Employees.First(e=>e.Id==1);
```

Entity Framework stores

```
Original Values

↓

Employee

↓

Current Values

↓

State

Unchanged
```

Modify

```csharp
employee.Name = "Raj";
```

Entity Framework compares

```
Original Name

↓

Rakesh

Current Name

↓

Raj
```

Difference found.

State changes

```
Modified
```

SaveChanges()

↓

Generate UPDATE SQL

---

# Checking Entity State

```csharp
Console.WriteLine(
context.Entry(employee).State);
```

Output

```
Unchanged

Modified

Added

Deleted

Detached
```

Depending on the current state.

---

# Real-Time Banking Example

Customer Account

```
Balance

50000
```

Customer Loaded

```
Unchanged
```

Customer Deposits Money

```
Modified
```

New Customer Registered

```
Added
```

Customer Closes Account

```
Deleted
```

Customer Object Created Using

```csharp
new Customer()
```

State

```
Detached
```

---

# Memory Diagram

```
STACK

employee

↓

Reference

----------------------------

HEAP

Employee Object

Salary

70000

----------------------------

Change Tracker

Original

50000

Current

70000

State

Modified
```

---

# Interview Questions

## What is Entity State?

Entity State represents the current status of an entity inside the Change Tracker.

It determines what SQL Entity Framework will generate.

---

## Which state generates INSERT?

```
Added
```

---

## Which state generates UPDATE?

```
Modified
```

---

## Which state generates DELETE?

```
Deleted
```

---

## Which state is assigned immediately after loading an entity?

```
Unchanged
```

---

## What is Detached?

An entity exists in memory but is not being tracked by Entity Framework.

---

## What happens after SaveChanges()?

| Before SaveChanges | After SaveChanges |
|--------------------|-------------------|
| Added | Unchanged |
| Modified | Unchanged |
| Deleted | Detached |

---

## How can we check an entity's state?

```csharp
context.Entry(employee).State;
```

---

# Summary

Entity Framework Core uses Entity States to determine how each entity should be synchronized with the database.

Every tracked entity is always in one of five states:

- Detached
- Added
- Unchanged
- Modified
- Deleted

When `SaveChanges()` is executed, Entity Framework examines these states and generates the corresponding SQL statements.

Understanding Entity States is the foundation of understanding Change Tracking and how Entity Framework Core performs CRUD operations.

---

# Key Takeaways

✔ Every tracked entity has exactly one state.

✔ Detached → Not tracked.

✔ Added → INSERT.

✔ Unchanged → No SQL.

✔ Modified → UPDATE.

✔ Deleted → DELETE.

✔ After SaveChanges():

- Added → Unchanged
- Modified → Unchanged
- Deleted → Detached

✔ State determines which SQL Entity Framework generates.

✔ `context.Entry(entity).State` returns the current entity state.

---

# Entity Framework Core – Module 3
# Tracking vs No Tracking

> **Course:** Entity Framework Core Complete Interview Master Roadmap

**Module:** 3 – Tracking vs No Tracking

**Difficulty:** ⭐⭐⭐⭐☆

**Interview Importance:** ⭐⭐⭐⭐⭐

---

# Table of Contents

1. What is Tracking?
2. Why Tracking Exists
3. How Tracking Works
4. Tracking Query
5. No Tracking Query
6. AsNoTracking()
7. Tracking vs No Tracking
8. Memory Diagram
9. Performance Comparison
10. AsNoTrackingWithIdentityResolution()
11. Real-Time Example
12. Best Practices
13. Interview Questions
14. Summary
15. Key Takeaways

---

# What is Tracking?

Tracking means

**Entity Framework keeps monitoring every entity returned from the database.**

Whenever an entity is loaded,

Change Tracker starts tracking it.

Example

```csharp
var employee = context.Employees.First(e => e.Id == 1);
```

Immediately after loading,

Entity Framework stores

- Original Values
- Current Values
- Entity State

Inside Change Tracker.

---

# Internal Flow

```
Database

↓

Execute SQL

↓

Create Employee Object

↓

Store in Change Tracker

↓

Return Object
```

---

# Tracking Query

Example

```csharp
using var context = new AppDbContext();

var employee = context.Employees
                      .First(e => e.Id == 1);
```

Current State

```
Unchanged
```

Now

```csharp
employee.Salary = 80000;
```

State becomes

```
Modified
```

Now

```csharp
context.SaveChanges();
```

Entity Framework generates

```sql
UPDATE Employees

SET Salary = 80000

WHERE Id = 1;
```

No Update() method required.

---

# Memory Diagram

```
STACK

employee

↓

Reference

-----------------------

HEAP

Employee Object

-----------------------

Change Tracker

Original Salary

50000

Current Salary

80000

State

Modified
```

---

# Why Tracking Exists?

Suppose you load

```
100 Employees
```

Later

you modify only

```
Employee 25
```

Question

How does EF know only Employee 25 changed?

Answer

Change Tracker.

It monitors every tracked entity.

---

# No Tracking Query

Suppose

```csharp
var employee =
context.Employees
       .AsNoTracking()
       .First(e => e.Id == 1);
```

Now

Change Tracker does

NOT

track this object.

Memory

```
STACK

employee

↓

Reference

----------------------

HEAP

Employee Object

↓

No Change Tracker
```

---

# Modify Object

```csharp
employee.Salary = 80000;
```

Question

Will

```csharp
context.SaveChanges();
```

Update the database?

Answer

```
NO
```

Because EF never tracked the object.

No SQL is generated.

---

# Tracking vs No Tracking

## Tracking

```csharp
var employee =
context.Employees.First();
```

Entity State

```
Unchanged
```

Modify

```
Modified
```

SaveChanges()

```
UPDATE
```

---

## No Tracking

```csharp
var employee =
context.Employees
       .AsNoTracking()
       .First();
```

Modify

```csharp
employee.Name = "Raj";
```

SaveChanges()

```
Nothing
```

Because Entity Framework does not know about the change.

---

# Why is AsNoTracking Faster?

Suppose

```
10000 Employees
```

Tracking Query

EF stores

```
10000

Original Values

Current Values

Entity States
```

Huge memory.

No Tracking

```
Create Object

Return Object

Done.
```

Nothing stored.

Less Memory

Less CPU

Faster.

---

# Performance Comparison

| Feature | Tracking | AsNoTracking |
|----------|----------|--------------|
| Uses Change Tracker | ✔ | ❌ |
| Detects Changes | ✔ | ❌ |
| SaveChanges Works | ✔ | ❌ |
| Memory Usage | High | Low |
| Performance | Slower | Faster |

---

# Real-Time Example

Suppose

Bank Dashboard

```
Today's Transactions

50000 Rows
```

Users are only viewing.

No editing.

Best choice

```csharp
context.Transactions

.AsNoTracking()

.ToList();
```

Because

No updates.

No tracking required.

---

# Another Example

Employee Profile Screen

User opens profile

```
Edit Employee
```

Now user changes

```
Salary
```

Need Tracking

```csharp
context.Employees

.First(e=>e.Id==1);
```

Because

SaveChanges()

must generate UPDATE.

---

# AsNoTrackingWithIdentityResolution()

This is an advanced feature.

Suppose

```
Department

↓

Employees
```

Same Employee appears multiple times during query execution.

Without Identity Resolution

```
Employee A

Object 1

Employee A

Object 2

Employee A

Object 3
```

Multiple objects.

Memory increases.

Now

```csharp
.AsNoTrackingWithIdentityResolution()
```

EF creates

only

```
ONE

Employee Object
```

Multiple references point to it.

Memory decreases.

---

# Example

```csharp
var employees =
context.Employees

.AsNoTrackingWithIdentityResolution()

.ToList();
```

No tracking

but

duplicate objects are avoided.

---

# Which One Should We Use?

| Scenario | Recommended |
|-----------|-------------|
| Edit Screen | Tracking |
| View Screen | AsNoTracking |
| Reports | AsNoTracking |
| Dashboard | AsNoTracking |
| Export to Excel | AsNoTracking |
| Bulk Read | AsNoTracking |

---

# Best Practices

✔ Use Tracking only when updating data.

✔ Use AsNoTracking for read-only queries.

✔ Use AsNoTracking in APIs returning reports.

✔ Improves memory usage.

✔ Improves performance.

---

# Interview Questions

## What is Tracking?

Tracking means Entity Framework monitors an entity using Change Tracker.

---

## What is AsNoTracking?

It disables Change Tracker for the returned entities.

---

## Does SaveChanges() work after AsNoTracking?

```
No
```

Because EF is not tracking the entity.

---

## Why is AsNoTracking faster?

Because Entity Framework

does not store

- Original Values
- Current Values
- Entity States

inside Change Tracker.

---

## When should we use AsNoTracking?

Whenever data is read-only.

Examples

- Reports
- Dashboard
- Search Screen
- Export
- Read APIs

---

## What is AsNoTrackingWithIdentityResolution()?

It disables tracking but still ensures that duplicate entities are represented by a single object instance during query execution, reducing memory usage.

---

# Summary

Entity Framework Core provides two ways to retrieve data:

- **Tracking Queries**: Entities are monitored by the Change Tracker, allowing modifications to be detected and persisted with `SaveChanges()`.
- **No Tracking Queries**: Entities are returned without being tracked, improving performance and reducing memory usage for read-only scenarios.

Choosing the correct approach depends on whether the retrieved data will be modified or only displayed.

---

# Key Takeaways

✔ Tracking uses Change Tracker.

✔ Tracking enables SaveChanges().

✔ AsNoTracking disables Change Tracker.

✔ AsNoTracking improves performance.

✔ Use Tracking for updates.

✔ Use AsNoTracking for read-only operations.

✔ AsNoTrackingWithIdentityResolution reduces duplicate object creation without enabling tracking.

---

# Next Module

# Entity Framework Core – Module 4
# DetectChanges() – Internal Working

> **Course:** Entity Framework Core Complete Interview Master Roadmap

**Module:** 4 – DetectChanges()

**Difficulty:** ⭐⭐⭐⭐☆

**Interview Importance:** ⭐⭐⭐⭐⭐

---

# Table of Contents

1. What is DetectChanges()?
2. Why Do We Need DetectChanges()?
3. Internal Working
4. Automatic DetectChanges()
5. Manual DetectChanges()
6. AutoDetectChangesEnabled
7. Performance Impact
8. Real-Time Example
9. Best Practices
10. Interview Questions
11. Summary
12. Key Takeaways

---

# What is DetectChanges()?

DetectChanges() is a method used by Entity Framework Core to compare an entity's **Original Values** with its **Current Values**.

If any value has changed,

Entity Framework updates the entity state.

For example

```
Original Salary

50000

↓

Current Salary

70000

↓

Entity State

Modified
```

---

# Why Do We Need DetectChanges()?

Suppose

```csharp
var employee = context.Employees.First(e => e.Id == 1);
```

Initially

```
State

Unchanged
```

Now

```csharp
employee.Salary = 70000;
```

Question

How does EF know

Salary changed?

Answer

```
DetectChanges()
```

It compares

```
Original Values

↓

Current Values
```

---

# Internal Working

Suppose

Database

```
Employee

Id = 1

Salary = 50000
```

Load Employee

```csharp
var employee =
context.Employees.First(e => e.Id == 1);
```

Change Tracker stores

```
Original Salary

50000

Current Salary

50000

State

Unchanged
```

---

Modify

```csharp
employee.Salary = 70000;
```

Memory

```
Original Salary

50000

Current Salary

70000
```

Now

DetectChanges()

compares

```
50000

↓

70000
```

Difference found.

State becomes

```
Modified
```

---

# Memory Diagram

Before Modification

```
Change Tracker

---------------------

Original

Salary =50000

Current

Salary =50000

State

Unchanged
```

After Modification

```
Change Tracker

----------------------

Original

50000

Current

70000

State

Modified
```

---

# Automatic DetectChanges()

Normally,

you never call DetectChanges() yourself.

Entity Framework automatically calls it before

- SaveChanges()
- Entries()
- HasChanges()

Example

```csharp
employee.Name = "Raj";

context.SaveChanges();
```

Internally

```
SaveChanges()

↓

DetectChanges()

↓

Find Modified Entities

↓

Generate SQL

↓

Execute SQL
```

---

# Manual DetectChanges()

You can call it yourself.

```csharp
context.ChangeTracker.DetectChanges();
```

This forces EF to immediately check every tracked entity.

Example

```csharp
employee.Name = "Raj";

context.ChangeTracker.DetectChanges();

Console.WriteLine(
context.Entry(employee).State);
```

Output

```
Modified
```

---

# SaveChanges() Internal Flow

```
SaveChanges()

↓

DetectChanges()

↓

Find Added Entities

↓

Generate INSERT

↓

Find Modified Entities

↓

Generate UPDATE

↓

Find Deleted Entities

↓

Generate DELETE

↓

Execute SQL

↓

AcceptAllChanges()
```

---

# AutoDetectChangesEnabled

Entity Framework automatically calls DetectChanges().

Sometimes,

this becomes expensive.

Especially when

```
100000 Entities
```

are tracked.

You can disable automatic detection.

```csharp
context.ChangeTracker.AutoDetectChangesEnabled = false;
```

Now

Entity Framework will NOT automatically check changes.

You must call

```csharp
context.ChangeTracker.DetectChanges();
```

manually.

---

# Real-Time Example

Suppose

Importing

```
100000 Employees
```

```csharp
foreach(var emp in employees)
{
    context.Employees.Add(emp);
}

context.SaveChanges();
```

If DetectChanges()

runs after every Add(),

performance becomes slower.

Instead

```csharp
context.ChangeTracker.AutoDetectChangesEnabled = false;

foreach(var emp in employees)
{
    context.Employees.Add(emp);
}

context.ChangeTracker.DetectChanges();

context.SaveChanges();
```

Much faster.

---

# Performance Comparison

| Auto Detect | Manual Detect |
|--------------|--------------|
| Easy to use | Better for Bulk Operations |
| Slower for Large Data | Faster |
| Default Behavior | Advanced Usage |

---

# When Should We Disable AutoDetectChanges?

Use it only for

- Bulk Insert
- Bulk Update
- Bulk Import
- Data Migration

Never disable it in normal CRUD applications.

---

# Best Practices

✔ Leave AutoDetectChangesEnabled = true for normal applications.

✔ Disable it only during large batch operations.

✔ Always call DetectChanges() manually before SaveChanges() if automatic detection is disabled.

---

# Interview Questions

## What is DetectChanges()?

DetectChanges() compares the original and current values of tracked entities and updates their Entity State.

---

## When is DetectChanges() called automatically?

Before

- SaveChanges()
- Entries()
- HasChanges()

---

## Can we call DetectChanges() manually?

Yes.

```csharp
context.ChangeTracker.DetectChanges();
```

---

## What happens if AutoDetectChangesEnabled is false?

Entity Framework will not automatically detect modifications.

You must call DetectChanges() manually before SaveChanges().

---

## Why disable AutoDetectChanges?

To improve performance during bulk insert or bulk update operations.

---

# Summary

DetectChanges() is responsible for identifying modifications made to tracked entities.

It compares the original values with the current values and updates the entity state accordingly.

By default, Entity Framework Core calls DetectChanges() automatically before SaveChanges() and other operations.

For high-volume batch operations, automatic detection can be disabled to improve performance, but manual detection becomes the developer's responsibility.

---

# Key Takeaways

✔ DetectChanges() compares Original Values with Current Values.

✔ It updates the Entity State.

✔ SaveChanges() automatically calls DetectChanges().

✔ AutoDetectChangesEnabled is true by default.

✔ Disable AutoDetectChanges only for bulk operations.

✔ Call DetectChanges() manually if automatic detection is disabled.

---

# Internal Flow Diagram

```
Entity Loaded

↓

Original Values Stored

↓

Entity Modified

↓

DetectChanges()

↓

Entity State Updated

↓

SaveChanges()

↓

Generate SQL

↓

Execute SQL
```

---

# Entity Framework Core – Module 5
# SaveChanges() Internal Working

> **Course:** Entity Framework Core Complete Interview Master Roadmap

**Module:** 5 – SaveChanges() Internal Working

**Difficulty:** ⭐⭐⭐⭐⭐

**Interview Importance:** ⭐⭐⭐⭐⭐

---

# Table of Contents

1. What is SaveChanges()?
2. Why Do We Need SaveChanges()?
3. Internal Working
4. Complete SaveChanges() Pipeline
5. Transaction Handling
6. SQL Generation
7. AcceptAllChanges()
8. SaveChangesAsync()
9. Exception Handling
10. Performance Considerations
11. Interview Questions
12. Summary
13. Key Takeaways

---

# What is SaveChanges()?

`SaveChanges()` is the method that persists all pending changes made in the `DbContext` to the database.

Example

```csharp
var employee = context.Employees.First(e => e.Id == 1);

employee.Salary = 70000;

context.SaveChanges();
```

Without calling `SaveChanges()`, nothing is written to the database.

---

# Why Do We Need SaveChanges()?

Suppose

```csharp
employee.Salary = 70000;
```

The object changes only in memory.

Database still contains

```
Salary = 50000
```

Only after

```csharp
context.SaveChanges();
```

does the database become

```
Salary = 70000
```

---

# Internal Working

Suppose

```csharp
var employee = context.Employees.First(e => e.Id == 1);

employee.Salary = 70000;

context.SaveChanges();
```

Internally

```
SaveChanges()

↓

DetectChanges()

↓

Find Modified Entities

↓

Generate UPDATE SQL

↓

Open Database Transaction

↓

Execute SQL

↓

Commit Transaction

↓

AcceptAllChanges()
```

---

# Complete SaveChanges() Pipeline

```
Application

↓

DbContext

↓

SaveChanges()

↓

DetectChanges()

↓

Check Entity States

↓

Added?

↓

Generate INSERT

↓

Modified?

↓

Generate UPDATE

↓

Deleted?

↓

Generate DELETE

↓

Open Transaction

↓

Execute SQL

↓

Commit

↓

AcceptAllChanges()

↓

Return Number of Rows
```

---

# Example

Suppose

```csharp
context.Employees.Add(employee);

context.Departments.Remove(department);

employee.Salary = 80000;

context.SaveChanges();
```

Entity States

```
Employee

Added

----------------

Department

Deleted

----------------

Employee

Modified
```

Generated SQL

```sql
INSERT INTO Employees(...);

UPDATE Employees
SET Salary=80000
WHERE Id=1;

DELETE FROM Departments
WHERE Id=5;
```

All three statements execute in one transaction.

---

# Transaction Handling

By default,

`SaveChanges()` wraps all SQL statements in a database transaction.

```
INSERT

↓

UPDATE

↓

DELETE

↓

COMMIT
```

If one statement fails,

```
ROLLBACK
```

occurs automatically.

---

# Example

Suppose

```
INSERT Employee

↓

UPDATE Salary

↓

DELETE Department
```

If DELETE fails,

```
Rollback

↓

Nothing is saved.
```

This ensures database consistency.

---

# SQL Generation

Entity Framework generates SQL based on Entity States.

| Entity State | SQL |
|--------------|-----|
| Added | INSERT |
| Modified | UPDATE |
| Deleted | DELETE |
| Unchanged | Nothing |
| Detached | Nothing |

---

# AcceptAllChanges()

After successful execution,

Entity Framework calls

```csharp
AcceptAllChanges();
```

Internally it changes

```
Added

↓

Unchanged

Modified

↓

Unchanged

Deleted

↓

Detached
```

The Change Tracker is updated to reflect that all changes have been successfully saved.

---

# SaveChangesAsync()

Async version

```csharp
await context.SaveChangesAsync();
```

Everything works exactly the same,

except

database operations are asynchronous.

Pipeline

```
SaveChangesAsync()

↓

DetectChanges()

↓

Generate SQL

↓

Execute SQL Asynchronously

↓

Commit

↓

AcceptAllChanges()
```

---

# Exception Handling

Suppose

```csharp
try
{
    context.SaveChanges();
}
catch(Exception ex)
{
}
```

Possible exceptions

- DbUpdateException
- DbUpdateConcurrencyException
- SqlException (provider-specific)
- Validation exceptions (depending on configuration)

If an exception occurs,

the transaction is rolled back.

---

# Performance Tips

✔ Keep DbContext short-lived.

✔ Save multiple changes together instead of calling SaveChanges() repeatedly.

✔ Use SaveChangesAsync() in ASP.NET Core applications.

✔ Use bulk libraries for very large batch operations.

---

# Real-Time Banking Example

Transfer ₹1000

```
Debit Account A

↓

Credit Account B

↓

SaveChanges()
```

Both operations are committed together.

If one fails,

everything is rolled back.

This is why transactions are important.

---

# Interview Questions

## What does SaveChanges() do?

It detects changes, generates SQL, executes it inside a transaction, updates the Change Tracker, and returns the number of affected rows.

---

## Does SaveChanges() call DetectChanges()?

Yes.

By default,

`SaveChanges()` automatically calls `DetectChanges()` before generating SQL.

---

## Does SaveChanges() use a transaction?

Yes.

If multiple SQL statements are executed, they are wrapped in a transaction by default.

---

## What is AcceptAllChanges()?

It updates the Change Tracker after a successful save by changing entity states such as Added → Unchanged and Modified → Unchanged.

---

## Difference between SaveChanges() and SaveChangesAsync()?

| SaveChanges() | SaveChangesAsync() |
|---------------|--------------------|
| Synchronous | Asynchronous |
| Blocks the calling thread | Doesn't block while waiting for database I/O |
| Suitable for console or simple apps | Preferred in ASP.NET Core |

---

# Summary

`SaveChanges()` is the heart of Entity Framework Core persistence.

It performs change detection, generates SQL based on entity states, executes all statements within a transaction, updates the Change Tracker, and returns the number of affected rows.

Understanding this pipeline is essential for writing efficient EF Core applications and for succeeding in senior .NET interviews.

---

# Key Takeaways

✔ SaveChanges() persists tracked changes.

✔ DetectChanges() runs first.

✔ SQL is generated based on Entity State.

✔ Operations execute inside a transaction.

✔ Successful execution calls AcceptAllChanges().

✔ SaveChangesAsync() performs the same work asynchronously.

✔ SaveChanges() returns the number of affected rows.

---

# Internal Flow Diagram

```
Modify Entity

↓

SaveChanges()

↓

DetectChanges()

↓

Generate SQL

↓

Begin Transaction

↓

Execute SQL

↓

Commit

↓

AcceptAllChanges()

↓

Return Rows Affected
```

---

# Next Module

## Module 6 – ChangeTracker API

Topics Covered

- ChangeTracker.Entries()
- Entry()
- EntityEntry
- CurrentValues
- OriginalValues
- Property()
- Reload()
- GetDatabaseValues()
- EntityState Manipulation
- Real Interview Examples

