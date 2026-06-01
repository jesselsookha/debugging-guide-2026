# ◉ Reading Error Messages

---

## ◉ The Message Is Not Noise

When a program fails to compile or crashes during execution, the development environment produces an error message. Many students dismiss this message within seconds — closing the window, clicking away, or simply not reading past the first unfamiliar word.

This is the single most common and most costly habit in beginner debugging.

An error message is a structured communication from the compiler or runtime. It contains specific, useful information about exactly what went wrong and where. Learning to read it is not optional — it is the foundation of every debugging technique that follows.

!!! tip "Read before you react"
    Before you change a single line of code, before you search online, before you ask anyone for help — read the entire error message. All of it. The information you need is almost always already there.

---

## ◉ I. The Anatomy of an Error Message

Most error messages, regardless of language or environment, follow a similar structure. Understanding the parts helps you know where to look first.

```
[Error Type]: [Description of what went wrong]
  at [File name], [Line number], [Method or context]
```

Some languages are more verbose than others. Some IDEs present this information differently. But the core components are consistent.

### The Four Components to Find

**I. Error Type**
The category or name of the error — `NullPointerException`, `SyntaxError`, `TypeError`, `CS1002`. This tells you the class of problem you are dealing with.

**II. Description**
A plain-language (or close to it) explanation of what the system encountered. This is the sentence to read carefully — it usually contains the specific value, variable name, or operation that caused the failure.

**III. File and Line Number**
Where in your code the error occurred or was first detected. This is your starting point for investigation.

**IV. Stack Trace**
A list of method calls that were active at the moment of failure, from the most recent call at the top to the original entry point at the bottom. Covered in detail below.

---

## ◉ II. Reading a Compile-Time Error Message

Compile-time errors are the most readable — the program has not even started, and the compiler is telling you directly what it found problematic.

=== "Java — missing semicolon"
    ```
    Main.java:5: error: ';' expected
            System.out.println("Hello")
                                       ^
    1 error
    ```

    Reading this:

    - **File:** `Main.java`
    - **Line:** `5`
    - **Error type:** `error: ';' expected`
    - **Pointer:** the `^` caret marks exactly where the compiler expected the semicolon
    - **Action:** go to line 5, add the missing `;`

=== "C# — missing semicolon"
    ```
    Program.cs(8,42): error CS1002: ; expected [MyProject]
    ```

    Reading this:

    - **File:** `Program.cs`
    - **Line:** `8`, **Column:** `42`
    - **Error code:** `CS1002`
    - **Description:** `; expected`
    - **Action:** go to line 8, column 42 in `Program.cs`

=== "Kotlin — type mismatch"
    ```
    error: type mismatch: inferred type is String but Int was expected
        val count: Int = "five"
                        ^
    ```

    Reading this:

    - **Error type:** `type mismatch`
    - **Detail:** the value `"five"` is a `String`, but the variable was declared as `Int`
    - **Pointer:** `^` marks the offending value
    - **Action:** either change the type annotation or change the value

=== "JavaScript — syntax error"
    ```
    SyntaxError: Unexpected token '}'
        at new Script (node:vm:100:7)
        ...
    ```

    Reading this:

    - **Error type:** `SyntaxError`
    - **Description:** `Unexpected token '}'` — the parser found a closing brace where it did not expect one
    - **Common cause:** a missing opening brace `{`, or an extra closing brace somewhere earlier

!!! warning "The reported line is where the compiler got confused — not always where you made the mistake"
    If the compiler reports an error on line 20, the actual mistake may be on line 18 or 19. Read the error message first, then check the reported line *and the lines immediately before it*.

---

## ◉ III. Reading a Runtime Error Message

Runtime errors are more complex because the program was running when the failure occurred. The error message includes not just a description but a **stack trace** — the sequence of method calls that were active at the moment of the crash.

### A Runtime Error in Java

```
Exception in thread "main" java.lang.NullPointerException: // (1)
Cannot invoke "String.length()" because "name" is null    // (2)
	at com.example.Main.printLength(Main.java:12)          // (3)
	at com.example.Main.main(Main.java:7)                  // (4)
```

1. **Exception type:** `java.lang.NullPointerException` — a null reference was accessed
2. **Description:** the exact method that was called on the null reference, and which variable was null
3. **Nearest call:** the method and line where the crash occurred — `Main.java` line 12, inside `printLength()`
4. **Caller:** the method that called the method that crashed — `main()` at line 7

!!! info "How to read a stack trace"
    Read from **top to bottom**. The top line is the closest to the crash. The lines below it are the chain of calls that led there.

    Your own code is the most important part. Framework code, library code, and JVM/CLR internals will also appear — you can generally skip past lines that reference packages you did not write.

### A Runtime Error in C#

```
Unhandled exception. System.NullReferenceException:        // (1)
Object reference not set to an instance of an object.      // (2)
   at MyApp.UserService.GetName() in UserService.cs:line 24 // (3)
   at MyApp.Program.Main() in Program.cs:line 10            // (4)
```

1. **Exception type:** `System.NullReferenceException`
2. **Standard description** — tells you a null object was accessed
3. **Your code:** `UserService.cs` line 24 is where it crashed
4. **Entry point:** `Program.cs` line 10 called the method that crashed

### A Runtime Error in Kotlin / Android (Logcat)

```
FATAL EXCEPTION: main
Process: com.example.myapp, PID: 12345
java.lang.NullPointerException: Attempt to invoke virtual method  // (1)
'java.lang.String android.content.Intent.getAction()'             // (2)
on a null object reference                                         // (3)
    at com.example.myapp.MainActivity.onCreate(MainActivity.kt:38) // (4)
    at android.app.Activity.performCreate(Activity.java:8051)       // (5)
```

1. **Exception type:** `NullPointerException`
2. **Method called on null:** `getAction()`
3. **Confirmation:** null object reference
4. **Your code:** `MainActivity.kt` line 38 — start your investigation here
5. **Framework code:** `android.app.Activity` — this is Android internal code, not yours

!!! tip "In Android Studio: focus on your package name"
    In a Logcat stack trace, look for lines containing your application's package name (e.g., `com.example.myapp`). Those are the lines in *your* code. Framework lines (beginning with `android.`, `java.`, `kotlin.`) are usually not where your mistake is — they just show the execution path.

---

## ◉ IV. Understanding Stack Traces

A stack trace records the **call stack** at the moment of failure — the sequence of method invocations that were active when the crash occurred. Understanding stack traces is one of the most valuable debugging skills available, and it is not difficult once you know how to read them.

### The Call Stack — a Simple Analogy

Imagine you are asked to do a task. While doing it, you need to do a smaller task. While doing that, something goes wrong. The stack trace records all the tasks that were in progress at the moment of failure — listed from most recent (closest to the crash) to original (entry point).

### What to Do with a Stack Trace

**Step I — Find the exception type and message.**
The first line always tells you *what* went wrong. Read it carefully.

**Step II — Find your code in the trace.**
Look for your own class names, package names, or file names. Ignore lines from standard libraries, frameworks, and the runtime itself — those are usually not where your mistake is.

**Step III — Go to that line.**
The line number next to your class name is where your code was executing at the point of failure. Open that file, go to that line, and read it carefully in context.

**Step IV — Read upward.**
The line above yours in the stack trace shows which method *called* the method that crashed. Sometimes the root cause is in the calling code, not in the crashed method itself.

!!! example "Stack trace walkthrough"
    Given this trace:
    ```
    java.lang.ArrayIndexOutOfBoundsException: Index 5 out of bounds for length 3
        at com.example.Calculator.getResult(Calculator.java:18)
        at com.example.Main.run(Main.java:42)
        at com.example.Main.main(Main.java:9)
    ```

    **Reading it:**

    - The crash is `ArrayIndexOutOfBoundsException` — index 5 was accessed on an array of length 3
    - Your code: `Calculator.java` line 18 is where the access happened
    - It was called from `Main.java` line 42, which was called from `main()` at line 9
    - **Start at `Calculator.java` line 18.** Find the array access. Check what value is being used as the index.

---

## ◉ V. Locating Errors in Your IDE

Each development environment presents error information differently. Knowing where to look is as important as knowing how to read what you find.

=== "⌨ Android Studio"
    - **Logcat** (bottom panel): shows runtime errors and crash reports when the app is running
    - **Build output** (Build tab): shows compile-time errors when you build the project
    - **Problems panel**: lists all detected errors with clickable links to the exact line
    - **Red underlines** in the editor: syntax and semantic errors detected in real time

=== "⌨ Visual Studio Code"
    - **Terminal** (bottom panel): shows compile errors for languages with build steps; shows runtime output
    - **Problems panel** (View → Problems): lists all detected errors and warnings with file and line
    - **Red underlines** in the editor: real-time syntax and type errors (language-dependent)
    - **Debug Console**: shows output when running in debug mode

=== "⌨ Visual Studio"
    - **Error List** panel: lists all errors, warnings, and messages after a build
    - **Output window**: shows full build output including detailed error messages
    - **Red squiggles** in the editor: immediate syntax and type errors
    - **Exception popup**: appears when a runtime exception is thrown during debugging

=== "⌨ IntelliJ / NetBeans"
    - **Run output** (bottom panel): shows console output and exception messages at runtime
    - **Event Log**: records build events and error summaries
    - **Red underlines**: real-time detection of syntax and type errors
    - **Problems view**: aggregated list of detected issues

!!! note "Not all errors appear in the same place"
    A program may compile without errors but fail at runtime — meaning the compile output window shows nothing, but the run output shows an exception. Always check **both** the build output and the runtime output before concluding there is no error.

---

## ◉ VI. A Practical Reading Checklist

When you encounter any error message, work through this sequence before doing anything else:

- [ ] What is the **error type**? (NullPointerException, SyntaxError, CS1002, etc.)
- [ ] What is the **description**? Read the plain-language part of the message.
- [ ] What **file and line number** is reported?
- [ ] Is there a **stack trace**? If so, which line of *your* code is closest to the top?
- [ ] What was the program trying to do at that line?
- [ ] Does the error description match what you see at that line?

If you can answer all six questions, you are ready to begin investigating the cause.

---

*Continue to: [The Basic Debugging Workflow →](basic_workflow.md)*

*← Back to: [Section Index](index.md)*

---

*[IDE]: Integrated Development Environment
*[JVM]: Java Virtual Machine
*[CLR]: Common Language Runtime
*[PID]: Process Identifier
