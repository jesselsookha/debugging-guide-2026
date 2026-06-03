# ⌨ IDE-Specific Notes (Summary)

---

## ⌨ About This Page

This page provides a brief orientation to the debugging features of the four development environments most commonly used in the programmes this guide is written for: Android Studio, Visual Studio Code, Visual Studio, and IntelliJ IDEA / NetBeans.

The goal here is recognition — knowing which panel to open, which output to read, and where to find the debugger controls. Detailed, step-by-step walkthroughs for each platform are in [Appendix G · Platform Notes](../05_appendices/platform_android_studio.md).

!!! note "The principles are consistent across all environments"
    The debugging concepts covered in the previous pages — breakpoints, stepping, watch expressions, stack traces, log output — exist in every IDE listed here. The controls look different and have different names, but the underlying operations are identical. Learn the concept once. Apply it in any environment.

---

## ⌨ I. Android Studio

**Primary language:** Kotlin (and Java)

**Where to find runtime errors:** The **Logcat** panel (bottom of the screen, or **View → Tool Windows → Logcat**).

**Where to find build errors:** The **Build** output tab, or the **Problems** panel.

**How to start a debugging session:** Click the **bug icon** (🐛) instead of the run icon, or use **Run → Debug**. The app deploys to the emulator or connected device in debug mode.

**Key debugging panels:**

| Panel | Purpose |
|-------|---------|
| Logcat | Runtime output, crash reports, and your `Log.d()` / `Log.e()` statements |
| Variables | All variables in scope at the current breakpoint |
| Frames (Call Stack) | The sequence of method calls leading to the current position |
| Watches | Custom expressions you add to monitor throughout a session |
| Breakpoints | **Run → View Breakpoints** — manage all set breakpoints |

**Stepping controls (Debug toolbar):**

| Button | Action |
|--------|--------|
| ▶ Resume | Continue to next breakpoint |
| ↷ Step Over | Next line, stay in current method |
| ↓ Step Into | Enter the called method |
| ↑ Step Out | Finish current method, return to caller |
| ⟳ Evaluate Expression | `Alt+F8` — evaluate any expression in current context |

**Logcat filtering:** Filter by tag (your `TAG` constant), by log level (Debug / Info / Warn / Error), or by package name. This reduces noise dramatically when investigating a specific component.

!!! tip "Use Log.d() with a consistent tag"
    Always use `private const val TAG = "YourClassName"` and reference it in all your log calls. This makes filtering in Logcat precise — you see only the output from the class you are investigating.

---

## ⌨ II. Visual Studio Code

**Primary languages:** JavaScript, TypeScript, Python (and many others via extensions)

**Where to find runtime errors:** The **Terminal** panel (bottom). For web/Node.js apps, the terminal shows `console.log()` output and stack traces.

**Where to find compile/lint errors:** The **Problems** panel (**View → Problems**, or `Ctrl+Shift+M`). Errors and warnings from TypeScript, ESLint, and other tools appear here with clickable file links.

**How to start a debugging session:** Press **F5**, or go to **Run → Start Debugging**. VS Code requires a `launch.json` configuration for most projects. If one does not exist, VS Code will offer to create one.

**Key debugging panels (visible in the Run and Debug sidebar `Ctrl+Shift+D`):**

| Panel | Purpose |
|-------|---------|
| Variables | Local, closure, and global variables at the current position |
| Watch | Custom expressions to monitor |
| Call Stack | The sequence of calls leading to the current position |
| Breakpoints | All set breakpoints — toggle on/off here |
| Debug Console | Evaluate expressions interactively in the paused context |

**Stepping controls:**

| Button | Shortcut | Action |
|--------|----------|--------|
| Continue | `F5` | Run to next breakpoint |
| Step Over | `F10` | Next line, stay in current function |
| Step Into | `F11` | Enter the called function |
| Step Out | `Shift+F11` | Finish current function, return to caller |

!!! tip "Browser DevTools for web debugging"
    For browser-based JavaScript and TypeScript, the Chrome or Firefox DevTools debugger offers the same capabilities as VS Code — breakpoints, stepping, watch expressions — with the added benefit of inspecting the DOM and network requests simultaneously. See [Appendix G · Browser DevTools](../05_appendices/platform_browser_devtools.md) for details.

---

## ⌨ III. Visual Studio

**Primary language:** C# (and .NET languages)

**Where to find runtime errors:** The **Output** window (**View → Output**) shows detailed build and run output. When an exception is thrown during debugging, an **Exception Helper** popup appears at the crash site.

**Where to find build errors:** The **Error List** panel (**View → Error List**). Errors, warnings, and messages appear here after a build. Double-clicking navigates to the source line.

**How to start a debugging session:** Press **F5** or **Debug → Start Debugging**. The project builds and runs in debug mode.

**Key debugging windows:**

| Window | How to open | Purpose |
|--------|-------------|---------|
| Locals | Debug → Windows → Locals | All local variables in current method |
| Watch | Debug → Windows → Watch → Watch 1 | Custom expression monitoring |
| Call Stack | Debug → Windows → Call Stack | Stack of active method calls |
| Immediate | Debug → Windows → Immediate | Type and evaluate expressions interactively |
| Output | View → Output | Build output and runtime messages |
| Breakpoints | Debug → Windows → Breakpoints | Manage all breakpoints |

**Stepping controls:**

| Shortcut | Action |
|----------|--------|
| `F10` | Step Over |
| `F11` | Step Into |
| `Shift+F11` | Step Out |
| `F5` | Continue |
| `Ctrl+F10` | Run to Cursor |

!!! tip "The Exception Helper is your first stop"
    When an exception is thrown in debug mode, Visual Studio pauses automatically at the crash site and shows the **Exception Helper** — a popup that displays the exception type, message, and which variable caused the problem. Read this before opening any other panel.

---

## ⌨ IV. IntelliJ IDEA / NetBeans

**Primary language:** Java (IntelliJ also supports Kotlin)

**Where to find runtime errors:** The **Run** output panel (bottom) shows console output and exception messages. In debug mode, the **Debug** panel shows the live call stack and variables.

**Where to find build errors:** The **Build** output panel and the **Problems** view. Red squiggles in the editor mark real-time syntax and type errors.

**How to start a debugging session:** Click the **debug icon** next to the run configuration (the bug icon), or right-click the main class and select **Debug**. In NetBeans: **Debug → Debug Project** (`Ctrl+F5`).

**Key debugging panels (IntelliJ):**

| Panel | Purpose |
|-------|---------|
| Frames | The call stack — click any frame to inspect that context |
| Variables | All variables in scope at the current breakpoint |
| Watches | **+** to add a custom expression |
| Console | Standard output and `System.out.println()` output |
| Evaluate Expression | `Alt+F8` — evaluate any expression in the current context |

**Stepping controls (IntelliJ):**

| Shortcut | Action |
|----------|--------|
| `F8` | Step Over |
| `F7` | Step Into |
| `Shift+F8` | Step Out |
| `F9` | Resume (continue to next breakpoint) |
| `Alt+F9` | Run to Cursor |
| `Alt+F8` | Evaluate Expression |

**Drop Frame:** IntelliJ supports **Drop Frame** — rewinds execution to the start of the current method without restarting the program. Available from the debug toolbar. Useful when you stepped past the point of interest.

!!! tip "IntelliJ shows inline variable values"
    In debug mode, IntelliJ overlays the current value of every variable directly in the editor, next to the code. The most recently changed value is highlighted. This gives you immediate visual feedback about state without opening the Variables panel.

---

## ⌨ What Comes Next

This page gives you the orientation to find your way around each environment. For step-by-step walkthroughs — how to set up a debug configuration from scratch, how to filter Logcat effectively, how to use browser DevTools for network and DOM debugging — go to [Appendix G · Platform Notes](../05_appendices/platform_android_studio.md).

---

*Continue to: [Investigation Exercises →](investigation_exercises.md)*

*← Back to: [Section Index](index.md)*

---

*[IDE]: Integrated Development Environment
*[DOM]: Document Object Model
*[ESLint]: A static analysis tool for JavaScript and TypeScript
