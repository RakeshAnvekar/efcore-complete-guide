# .NET Finalizer, IDisposable, Dispose & GC.SuppressFinalize -- Interview Notes

> Author: ChatGPT\
> Purpose: Deep interview preparation

------------------------------------------------------------------------

# 1. Why do we need IDisposable if we already have a Garbage Collector?

The Garbage Collector (GC) **only frees managed memory**.

It **does not know how to release unmanaged resources**, such as:

-   File handles
-   Database connections
-   Network sockets
-   Printer handles
-   Native memory (`IntPtr`)
-   OS handles

Therefore we use **IDisposable** to release these resources immediately.

------------------------------------------------------------------------

# 2. Managed vs Unmanaged Resources

## Managed Resource

Objects created by .NET.

``` csharp
List<int> numbers = new();
string name = "Rakesh";
```

GC cleans them automatically.

## Unmanaged Resource

Created outside CLR.

Examples:

-   SQL Connection
-   File Handle
-   Socket
-   Native DLL memory

GC **cannot** clean them.

------------------------------------------------------------------------

# 3. Hotel Room Analogy

Imagine a hotel.

Room = Managed Memory

Room Key = Unmanaged Resource

When customer leaves:

Housekeeping cleans room.

But someone must collect the room key.

GC = Housekeeping

Finalizer / Dispose = Person collecting the key.

------------------------------------------------------------------------

# 4. Without Dispose and Without Finalizer

``` text
Create Object
      |
      v
Use Object
      |
      v
Object becomes unreachable
      |
      v
GC removes managed object
      |
      X
File Handle still open
```

Result:

Resource Leak

------------------------------------------------------------------------

# 5. Finalizer

Syntax

``` csharp
class FileManager
{
    ~FileManager()
    {
        Console.WriteLine("Cleaning unmanaged resources");
    }
}
```

A finalizer is a **backup cleanup mechanism**.

It is **NOT** called when object becomes unreachable.

Sequence:

``` text
Object created
      |
Lose all references
      |
Eligible for GC
      |
GC discovers object
      |
Move to Finalization Queue
      |
Finalizer Thread executes
      |
Next GC frees memory
```

Important:

-   Cannot predict when it runs.
-   Might never run before process exits.
-   Makes GC slower.

------------------------------------------------------------------------

# 6. IDisposable

``` csharp
public class Demo : IDisposable
{
    public void Dispose()
    {
        Console.WriteLine("Release unmanaged resources");
    }
}
```

Dispose provides **deterministic cleanup**.

Instead of waiting for GC,

you decide exactly when cleanup happens.

------------------------------------------------------------------------

# 7. Why Dispose is Better

Timeline

Without Dispose

``` text
10:00 Open SQL Connection

10:01 Finished work

10:15 GC runs

10:16 Finalizer closes connection
```

Connection remained open for 15 minutes.

With Dispose

``` text
10:00 Open SQL Connection

10:01 Dispose()

Connection closes immediately.
```

------------------------------------------------------------------------

# 8. Finalizer vs Dispose

  Feature         Finalizer              Dispose
  --------------- ---------------------- -----------------
  Called by       CLR Finalizer Thread   Developer
  Time            Unknown                Immediate
  Deterministic   No                     Yes
  Performance     Slow                   Fast
  Used for        Backup                 Primary cleanup

------------------------------------------------------------------------

# 9. Why Use Both?

Production pattern:

``` text
Developer calls Dispose()

        |
        v

Resource closed immediately

        |

GC.SuppressFinalize(this)

        |

CLR skips Finalizer
```

If developer forgets Dispose

``` text
Dispose not called

        |

GC runs

        |

Finalizer closes resource

        |

Memory removed
```

Finalizer is therefore a **safety net**.

------------------------------------------------------------------------

# 10. GC.SuppressFinalize()

``` csharp
public void Dispose()
{
    Dispose(true);

    GC.SuppressFinalize(this);
}
```

Meaning:

"I already cleaned everything.

Do NOT execute my finalizer."

Benefits

-   Faster GC
-   Object doesn't enter finalization process
-   Avoids duplicate cleanup

------------------------------------------------------------------------

# 11. Complete Dispose Pattern

``` csharp
using System;

public class ResourceHolder : IDisposable
{
    private bool _disposed;

    ~ResourceHolder()
    {
        Dispose(false);
    }

    public void Dispose()
    {
        Dispose(true);
        GC.SuppressFinalize(this);
    }

    protected virtual void Dispose(bool disposing)
    {
        if (_disposed)
            return;

        if (disposing)
        {
            // Dispose managed resources
        }

        // Release unmanaged resources

        _disposed = true;
    }
}
```

Explanation

Dispose(true)

-   Called by developer
-   Dispose managed resources
-   Dispose unmanaged resources

Dispose(false)

-   Called by Finalizer
-   Only release unmanaged resources

------------------------------------------------------------------------

# 12. Why bool disposing?

When Finalizer runs,

managed objects may already have been finalized.

Therefore

``` text
disposing == true

Safe to use managed objects

disposing == false

Do NOT access managed objects
```

------------------------------------------------------------------------

# 13. using Statement

``` csharp
using FileStream fs = File.OpenRead("test.txt");
```

Compiler converts to

``` csharp
FileStream fs = File.OpenRead("test.txt");

try
{
    // work
}
finally
{
    if (fs != null)
        fs.Dispose();
}
```

So using automatically calls Dispose.

------------------------------------------------------------------------

# 14. Interview Flow

Question:

Why IDisposable when GC exists?

Answer:

GC frees managed memory only.

It cannot release unmanaged resources like file handles or SQL
connections.

Dispose provides deterministic cleanup.

Finalizer is only a backup.

GC.SuppressFinalize prevents unnecessary finalization.

------------------------------------------------------------------------

# 15. Quick Comparison

  Scenario                  Result
  ------------------------- --------------------
  No Dispose No Finalizer   Resource leak
  Finalizer only            Cleanup eventually
  Dispose only              Immediate cleanup
  Dispose + Finalizer       Best Practice

------------------------------------------------------------------------

# 16. Hotel Analogy (Interview)

Hotel Room = Managed Memory

Room Key = Unmanaged Resource

GC cleans room.

Dispose returns key immediately.

Finalizer is hotel staff collecting forgotten keys later.

GC.SuppressFinalize means

"The guest already returned the key.

Don't send staff."

------------------------------------------------------------------------

# 17. Interview Questions

1.  What is IDisposable?
2.  What is a Finalizer?
3.  Difference between Dispose and Finalizer?
4.  Why is Finalizer slow?
5.  What is deterministic cleanup?
6.  Why GC cannot release SQL Connections?
7.  What is GC.SuppressFinalize()?
8.  Why Dispose(bool disposing)?
9.  What is using internally?
10. When should we write a Finalizer?

------------------------------------------------------------------------

# 18. Golden Rules

✅ Prefer using.

✅ Call Dispose as soon as resource is no longer needed.

✅ Finalizer is backup only.

✅ Call GC.SuppressFinalize after successful Dispose.

✅ Never rely on Finalizer for timely cleanup.

✅ Most modern classes use SafeHandle internally.
