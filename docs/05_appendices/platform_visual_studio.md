# Appendix G · Platform Notes — Visual Studio / Watch Windows

---

## ⌨ About This Page

This page is a deep reference for debugging in Visual Studio, with a focus on C# and .NET development. It expands on the brief orientation given in [IDE-Specific Notes](../02_investigation/ide_specific_notes.md).

Visual Studio (distinct from Visual Studio Code) is a full IDE primarily used for .NET development on Windows, with one of the most mature debugging toolsets available in any environment.

---

## ⌨ I. The Error List and Output Window

### Error List

**View → Error List** shows all compile-time errors, warnings, and messages after a build. Each entry is clickable and navigates directly to the source.

| Column | Meaning |
|--------|---------|
| Severity | Error, Warning, or Message |
| Code | The compiler error code, e.g., `CS1002`, `CS0103` |
| Description | Plain-language explanation |
| Project | Which project in the solution the issue is in |
| File / Line | Exact location |

!!! tip "Filter by severity"
    Large solutions can produce hundreds of warnings. Use the severity filter buttons at the top of the Error List to show only Errors when you need to focus on what is blocking the build.

### Output Window

**View → Output** shows detailed build output, including the full sequence of compilation steps, and — during debugging — diagnostic messages, loaded assemblies, and `Debug.WriteLine()` output.

```csharp
System.Diagnostics.Debug.WriteLine($"[OrderService] Processing order {order.Id}");
```

`Debug.WriteLine()` output appears in the **Output** window (Debug pane) only when running in Debug configuration — it is automatically excluded from Release builds.

---

## ⌨ II. The Exception Helper

When an unhandled exception is thrown during a debug session, Visual Studio automatically pauses execution at the line that threw it and displays the **Exception Helper** — a popup showing:

- The exception type
- The exception message
- A **View Details** option showing the full exception object, including `InnerException` if present
- Quick actions: **Continue**, **Break**, or add the exception type to **Exception Settings**

!!! tip "Read the Exception Helper before opening any other window"
    The Exception Helper gives you the exception type and message immediately, at the exact point of failure, with full variable context already available in the Locals window. This is almost always the fastest starting point for any runtime crash in Visual Studio.

### Exception Settings

**Debug → Windows → Exception Settings** (`Ctrl+Alt+E`) lets you configure the debugger to break **when an exception is thrown**, even if it would normally be caught further up the call stack. This is useful when an exception is being caught and handled silently somewhere, hiding the actual point of failure.

```csharp
try
{
    ProcessOrder(order);
}
catch (Exception)
{
    // Silently swallowed — Exception Settings will catch this
    // at the throw site instead of here
}
```

---

## ⌨ III. Setting Breakpoints

Click in the left margin next to a line number, or place the cursor on the line and press `F9`. A filled red circle appears.

### Conditional Breakpoints

Right-click the red circle → **Conditions...** A panel opens with options:

- **Conditional Expression** → **Is true**: pause when an expression evaluates to `true`, e.g., `order.Total > 1000`
- **Conditional Expression** → **Has changed**: pause when the value of an expression changes between hits
- **Hit Count**: pause after the breakpoint has been hit a specific number of times

### Tracepoints (Logpoints)

Right-click a breakpoint → **Actions...** → enter a message to print to the Output window, and optionally check **"Continue execution"** to log without pausing:

```
Order {order.Id} total is {order.Total}
```

This behaves like a `Console.WriteLine()` that can be added and removed without modifying source code.

---

## ⌨ IV. Debugging Windows and Stepping

### Key Windows

| Window | How to Open | Purpose |
|--------|-------------|---------|
| **Locals** | Debug → Windows → Locals | All local variables in the current method, automatically populated |
| **Watch** | Debug → Windows → Watch → Watch 1 | Custom expressions you add manually — persists across sessions |
| **Autos** | Debug → Windows → Autos | Variables used near the current line — automatically determined |
| **Call Stack** | Debug → Windows → Call Stack | The active call chain — double-click any frame to inspect that context |
| **Immediate** | Debug → Windows → Immediate | Type and execute expressions or statements interactively |
| **Threads** | Debug → Windows → Threads | All active threads — useful for concurrency bugs |

### The Watch Window in Detail

The Watch window is the direct equivalent of the trace table concept covered in [Appendix B](trace_tables.md) — a persistent list of expressions whose values update automatically at every step.

To add a watch: right-click any variable in the editor while debugging → **Add Watch** — or type directly into the Watch window:

```
order.Items.Count
order.Total > 0
order.Customer?.Name ?? "no customer"
```

Watches persist across debugging sessions (within the same solution), which is useful when returning to the same investigation across multiple runs.

### Stepping Controls

| Shortcut | Action |
|----------|--------|
| `F10` | Step Over |
| `F11` | Step Into |
| `Shift+F11` | Step Out |
| `F5` | Continue |
| `Ctrl+F10` | Run to Cursor |
| `Ctrl+Alt+E` | Exception Settings |
| `Ctrl+Shift+F9` | Delete all breakpoints |

### The Immediate Window

While paused, type any valid C# expression or statement into the Immediate window and press Enter:

```
> order.Total
85.50

> order.Items.Where(i => i.Price > 50).Count()
2

> order.Status = OrderStatus.Confirmed
```

The last example demonstrates that the Immediate window can also **modify** state — assigning a new value to a variable changes the running program's state for the rest of the session. Use this carefully, but it is a powerful way to test a fix's effect without restarting.

---

## ⌨ V. Edit and Continue

Visual Studio supports **Edit and Continue** — modifying source code while paused at a breakpoint, without restarting the debug session. For many simple changes (correcting a calculation, fixing a condition), the edit takes effect immediately and execution continues with the new code.

!!! note "Edit and Continue has limitations"
    Not all changes support Edit and Continue — adding new methods, changing method signatures, or modifying certain language constructs may require a restart. Visual Studio will indicate when a change cannot be applied without restarting.

---

## ⌨ VI. Common .NET Debugging Scenarios

### Scenario: NullReferenceException

```
System.NullReferenceException: 'Object reference not set to an instance of an object.'
```

C#'s message is famously unspecific compared to Java's equivalent. The Exception Helper's **View Details** often shows more — but the most reliable approach is to use the Locals window at the crash point to identify which object reference is `null`.

!!! tip "Nullable reference types"
    Enabling `<Nullable>enable</Nullable>` in your `.csproj` activates compile-time null checking. The compiler will warn you at build time about code paths where a reference type could be null but is used as if it cannot be — catching many `NullReferenceException` causes before the program ever runs.

### Scenario: LINQ "Sequence Contains No Elements"

```
System.InvalidOperationException: Sequence contains no elements
   at System.Linq.Enumerable.First[TSource](IEnumerable`1 source)
```

`.First()` throws when the sequence is empty. Use the Watch window to inspect the collection just before the `.First()` call — is it actually empty? If an empty result is valid, use `.FirstOrDefault()` and handle the `null`/default case explicitly.

### Scenario: Async/Await Deadlock

A common .NET-specific issue: calling `.Result` or `.Wait()` on a `Task` from a synchronous context can deadlock, especially in ASP.NET applications with a synchronization context.

```csharp
// Risky — can deadlock in some contexts
var result = SomeAsyncMethod().Result;

// Safer — await all the way up
var result = await SomeAsyncMethod();
```

If your application hangs (no exception, no output, just frozen) rather than crashing, suspect a deadlock. Use **Debug → Break All** while hung, then check the **Threads** and **Call Stack** windows — a thread blocked on `.Result` or `.Wait()` while another thread waits to resume on the same context is the signature of this bug.

---

*← Back to: [Appendix G · Platform Notes — VS Code / Terminal](platform_vscode.md)*

*Continue to: [Appendix G · Platform Notes — NetBeans / IntelliJ →](platform_netbeans_intellij.md)*

*← Back to: [Appendices Overview](index.md)*

---

*[IDE]: Integrated Development Environment
*[LINQ]: Language Integrated Query — a query syntax built into C#
*[ASP.NET]: Active Server Pages .NET — Microsoft's web application framework
