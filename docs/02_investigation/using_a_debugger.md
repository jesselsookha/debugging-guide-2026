# ◈ Using a Debugger

---

## ◈ What a Debugger Is

A debugger is a tool that allows you to **pause a running program at any point and inspect its state** — the values of all variables, the sequence of method calls that led to the current position, and the exact line of code about to be executed.

Where print debugging requires you to predict what to observe before the program runs, a debugger lets you observe anything, interactively, as the program runs. It is a window into the live execution of your code.

Every major IDE includes a built-in debugger. Every programmer who writes code of any meaningful complexity should know how to use one.

!!! info "The relationship between print debugging and the debugger"
    Print debugging and the IDE debugger are complementary tools, not competing ones. Print debugging is faster to set up for simple, targeted questions. The debugger is more powerful for complex investigations where you need to observe multiple variables across many steps without predicting in advance what to print.

    The CS61B Berkeley guide[^1] puts it directly: *"You are free to debug at first with print statements and then use the debugger if you are still struggling to find the bug after five minutes."*

[^1]: CS61B Debugging Guide — https://www.learncs.site/docs/curriculum-resource/cs61b/cs61b_en/guides/debugging

---

## ◈ I. Breakpoints

A breakpoint is a marker placed on a specific line of code that tells the debugger: **pause execution here**. When the program reaches that line, it stops before executing the instruction, and control is handed to the programmer.

### Setting a Line Breakpoint

In every major IDE, you set a line breakpoint by clicking in the **margin to the left of the line number**. A red dot (or similar indicator) appears. When you run the program in debug mode, execution pauses at that line.

=== "⌨ IntelliJ / Android Studio"
    Click in the grey gutter to the left of the line number. A red circle appears. Run the program using **Run → Debug** (or `Shift+F9`) instead of the normal run.

=== "⌨ Visual Studio"
    Click in the grey margin to the left of the line number. A filled red circle appears. Run with **Debug → Start Debugging** (`F5`).

=== "⌨ VS Code"
    Click in the gutter to the left of the line number. A red dot appears. Run with **Run → Start Debugging** (`F5`). A `launch.json` configuration may be needed for some languages.

=== "⌨ NetBeans"
    Click in the left margin next to the line number. A red square appears. Run with **Debug → Debug Project** (`Ctrl+F5`).

### Conditional Breakpoints

A conditional breakpoint only pauses when a specified condition is true. This is essential when investigating bugs that only occur for certain values — for example, an error that only affects orders above a certain amount, or a crash that only occurs on the hundredth iteration of a loop.

=== "IntelliJ / Android Studio"
    Right-click the red breakpoint circle → **Edit Breakpoint** → enter a condition expression, e.g., `amount > 500.0` or `i == 99`.

=== "Visual Studio"
    Right-click the red circle → **Conditions** → enter the condition and select **Is true**.

=== "VS Code"
    Right-click the red dot → **Edit Breakpoint** → select **Expression** and enter the condition.

!!! tip "Conditional breakpoints save significant time"
    If a bug only occurs on the 1000th iteration of a loop, stepping through 999 iterations manually is not a debugging strategy — it is a waste of time. A conditional breakpoint pauses only when `i == 999`, placing you directly at the point of interest.

---

## ◈ II. Stepping Through Code

Once execution is paused at a breakpoint, you navigate through the program using step controls. Understanding the difference between the three primary step actions is essential.

### Step Over

**Executes the current line and moves to the next line in the same method.**

If the current line calls another method, Step Over executes that method completely and returns to the next line in the current method. You do not enter the called method.

**Use when:** You want to advance through the current method line by line without inspecting the implementation details of the methods it calls.

### Step Into

**Executes the current line and, if it calls a method, moves into the first line of that method.**

If the current line does not call a method, Step Into behaves identically to Step Over.

**Use when:** You want to investigate what happens inside a specific method call — particularly when you suspect the bug is inside the called method.

### Step Out

**Executes the remainder of the current method and returns to the line that called it.**

**Use when:** You stepped into a method and realised the bug is not there. Step Out brings you back to the caller without stepping through every remaining line of the current method.

### Run to Cursor / Continue

**Continue** runs the program until the next breakpoint is reached.

**Run to Cursor** runs the program until it reaches the line where your cursor is currently positioned — as if you had placed a temporary breakpoint there.

**Use Run to Cursor when:** You need to skip a large loop or sequence and jump to a specific point without setting a new breakpoint.

```
┌────────────────────────────────────────────────────────────────────┐
│  STEPPING QUICK REFERENCE                                          │
├──────────────────┬─────────────────────────────────────────────────┤
│  Step Over       │  Next line — stays in current method            │
│  Step Into       │  Goes inside the called method                  │
│  Step Out        │  Finishes current method, returns to caller     │
│  Continue        │  Runs until next breakpoint                     │
│  Run to Cursor   │  Runs to wherever your cursor is positioned     │
└──────────────────┴─────────────────────────────────────────────────┘
```

---

## ◈ III. Inspecting Variables

When execution is paused, the debugger shows you the current state of every variable in scope.

### The Variables / Locals Panel

All major IDEs display the current variable values in a dedicated panel — called **Variables**, **Locals**, **Debug View**, or similar. This panel shows:

- The name of each variable currently in scope
- Its current value
- Its type
- For objects: the ability to expand and inspect their fields

!!! example "Reading the Variables panel"
    If execution is paused inside a method `calculateTotal(Cart cart)`, the Variables panel shows:

    - `cart` → the `Cart` object (expandable to show `items`, `discount`, `total` fields)
    - Any local variables declared so far in the method
    - `this` → the current object instance (if inside an instance method)

    This gives you an immediate, complete picture of what the program knows at the exact moment it paused.

### Watch Expressions

A **watch** is a custom expression you add to the debugger that is evaluated continuously as you step through code. It can be a variable name, a method call, a comparison, or any valid expression in the language.

Watches are useful when you want to monitor a specific value that may not be directly visible in the Variables panel — for example, the result of a calculation, or whether two objects are equal.

=== "IntelliJ / Android Studio"
    In the **Debug** panel, find the **Variables** tab. Click the **+** button to add a watch expression. Enter any valid expression — e.g., `cart.items.size()`, `total > 100`, or `user != null`.

=== "Visual Studio"
    Open the **Watch** window via **Debug → Windows → Watch**. Type any expression in the **Name** column. The value is updated at every step.

=== "VS Code"
    In the **Run and Debug** panel, find the **WATCH** section. Click the **+** to add an expression.

### The Relationship Between Watch Windows and Trace Tables

Watch expressions in the debugger are the dynamic equivalent of a **trace table** — the paper-based technique introduced in [Appendix B](../05_appendices/trace_tables.md). Both serve the same purpose: recording the value of variables at each step of execution.

The difference is that a trace table is constructed manually on paper (useful for learning, for pseudocode analysis, or when a debugger is not available), while watch expressions in the debugger update automatically as the program runs.

!!! note "Trace tables as preparation"
    Working through a trace table on paper before using the debugger is a valuable exercise for complex logic. It forces you to reason about what the values *should* be at each step — and when you then run the debugger, any divergence between your expected trace and the actual values tells you exactly where the logic breaks.

---

## ◈ IV. Evaluating Expressions

Most debuggers allow you to evaluate arbitrary expressions in the context of the paused program — as if you had inserted a line of code at the current position.

This is useful for:

- Testing a hypothesis without modifying the source code
- Calling a method and observing the return value
- Checking whether two object references are equal
- Evaluating a calculation with the current variable values

=== "IntelliJ / Android Studio"
    **Alt+F8** opens the **Evaluate Expression** dialog. Enter any valid Kotlin or Java expression and click **Evaluate**.

=== "Visual Studio"
    In the **Immediate Window** (Debug → Windows → Immediate), type an expression and press Enter.

=== "VS Code"
    In the **Debug Console** (visible when in debug mode), type an expression and press Enter.

---

## ◈ V. The Call Stack Panel

When execution is paused, the debugger also shows the **call stack** — the sequence of method calls that led to the current position.

This is the live version of a stack trace. Each frame in the call stack represents one method that was active at the time of the pause. Clicking on a frame in the call stack panel navigates the Variables panel to show the state of that frame — the variables and values that existed when that method was executing.

!!! example "Navigating the call stack"
    Execution is paused inside `formatAddress()`. The call stack shows:

    ```
    formatAddress()          ← current frame
    buildUserProfile()       ← called formatAddress()
    onProfileLoaded()        ← called buildUserProfile()
    UserRepository.fetch()   ← called onProfileLoaded()
    ```

    Clicking on `buildUserProfile()` in the call stack navigates to that frame and shows its local variables — including the `User` object that was passed to `formatAddress()`. If the `User` object is in an unexpected state, this is where to look for why.

---

## ◈ VI. Drop Frame / Rewind

Some debuggers (notably IntelliJ and Android Studio) support **Drop Frame** — the ability to "rewind" the execution back to the start of the current method call.

This is useful when you stepped past the point of interest and want to observe it again without restarting the entire program.

!!! tip "Not all debuggers support Drop Frame"
    VS Code's support varies by language extension. Visual Studio supports it for managed code. When available, it is one of the most useful features in the debugger for iterative investigation.

---

*Continue to: [Stack Trace Analysis →](stack_trace_analysis.md)*

*← Back to: [Section Index](index.md)*

---

*[IDE]: Integrated Development Environment
*[JVM]: Java Virtual Machine
