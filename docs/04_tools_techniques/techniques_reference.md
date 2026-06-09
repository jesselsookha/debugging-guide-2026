# ◎ Debugging Techniques — Quick Reference

---

## ◎ About This Page

This page is a consolidated reference for the debugging techniques used by programmers at every level of experience. Some of these techniques have been introduced in earlier sections. Others appear here for the first time.

Each technique is presented with the same structure: what it is, when to use it, how it works, and where it falls short. The goal is not to prescribe a single approach — it is to ensure you know what options exist and when each is appropriate.

!!! info "Techniques vs. tools"
    A **technique** is a method of reasoning or an approach to investigation — it is independent of any specific platform or environment. A **tool** is a piece of software that supports one or more techniques. This page covers techniques. Tools are covered in [Debugging Tools Overview](tools_overview.md).

---

## ◎ I. Backtracking

**What it is:** Working backward from the point where a failure was observed to find where it originated.

**When to use:** When the crash site or incorrect output is visible, but the root cause is upstream — the bug is somewhere earlier in the execution path.

**How it works:**

I. Identify the exact point where incorrect behaviour was observed.

II. Ask: *"What had to be true immediately before this point for this to occur?"*

III. Move backward through the execution path, checking the state at each step.

IV. Continue until you find the point where the expected state diverged from the actual state.

**Example:** A `NullPointerException` on line 47. Backtracking asks: *"Where was this object supposed to be initialised?"* — and traces back to line 23, where a conditional return meant the initialisation was skipped.

**Limitations:** Becomes impractical in large, multi-file systems where the execution path spans many components. In those cases, combine backtracking with logging or the debugger to make the path visible.

---

## ◎ II. Binary Search / Divide and Conquer

**What it is:** Narrowing the search space by systematically halving it — testing midpoints to determine which half contains the problem, discarding the other, and repeating.

**When to use:**

- A bug exists somewhere within a range of code and the location is unknown
- A bug was introduced somewhere between a known-good commit and the current state (use `git bisect`)
- A bug occurs for some inputs but not others and the triggering condition is unknown

**How it works:**

I. Define the range — the span of code, commits, or inputs where the bug could exist.

II. Test the midpoint. Does the bug occur here?

III. If yes: the bug is in the first half. Narrow to that half.

IV. If no: the bug is in the second half. Narrow to that half.

V. Repeat until the range is small enough to inspect directly.

!!! tip "Binary search across commits — git bisect"
    `git bisect` automates this process across version history. Given a known-good commit and a known-bad commit, git checks out midpoints and asks you to test each one. After approximately log₂(n) steps, it identifies the exact commit that introduced the bug. See [Professional Debugging Practices](../03_strategy/professional_practices.md) for a full walkthrough.

**Limitations:** Requires the ability to test a specific state in isolation. Difficult to apply when side effects from one half of the code contaminate the test of the other.

---

## ◎ III. Print and Log Debugging

**What it is:** Inserting output statements at strategic points in the code to make the program's execution and state visible.

**When to use:**

- Quick confirmation that a method was called
- Tracking how a variable changes over multiple iterations
- Debugging in environments where an interactive debugger is not available
- Tracing execution order in asynchronous or event-driven code

**How it works:** A targeted log statement at the entry point of a method, at each branch, and at each significant state change provides a timeline of what happened. The output is read to find the first point where the expected behaviour diverges from the actual.

**The critical discipline:** Write logs that answer specific questions, not logs that print everything. See [Print and Log Debugging](../02_investigation/print_log_debugging.md) for the full technique.

!!! danger "Remove debug logs before committing"
    Debug print statements left in production code pollute logs, can expose sensitive data, and indicate that investigation output was not cleaned up. All debug logs should be removed — or converted to structured log levels — before code is committed.

**Limitations:** Produces noise if used without strategy. Can alter timing behaviour in concurrent systems. Does not allow interactive inspection.

---

## ◎ IV. Using an Interactive Debugger

**What it is:** Pausing program execution at specific points and inspecting the live state of the program — variable values, call stack, execution position — interactively.

**When to use:**

- When print debugging has not been sufficient to identify the cause
- When you need to observe state across multiple variables simultaneously
- When the problem requires stepping through conditional logic line by line
- When the bug involves object state that is difficult to surface via print statements

**Key capabilities:**

| Capability | What it provides |
|------------|-----------------|
| Line breakpoints | Pause at a specific line |
| Conditional breakpoints | Pause only when a condition is true |
| Step over | Advance one line, staying in the current method |
| Step into | Enter the called method on the current line |
| Step out | Finish the current method and return to the caller |
| Watch expressions | Monitor specific values as execution proceeds |
| Call stack navigation | See the full execution path and inspect any frame |
| Expression evaluation | Test hypotheses without modifying code |

**Limitations:** Requires setting up a debug configuration. Can be slower to use than print debugging for simple, targeted questions. Not available in all environments.

See [Using a Debugger](../02_investigation/using_a_debugger.md) for a full walkthrough of all capabilities.

---

## ◎ V. Rubber Duck Debugging

**What it is:** Explaining your code, your problem, and your reasoning in detail — aloud, in writing, or to an inanimate object — to force articulation of assumptions.

**When to use:** When you have been staring at the same code for too long. When something "should work" but does not and you cannot see why. When you are about to ask someone else for help.

**Why it works:** Explaining requires structure. To explain something in sequence, you must think through the sequence. The act of articulating *"and at this point, the variable should contain X because…"* frequently surfaces the precise moment where an assumption is wrong — before the explanation is even finished.

!!! tip "Rubber duck before you ask"
    Before asking a lecturer, a colleague, or an AI tool for help, explain the problem to yourself out loud first. The explanation that you would give them is often sufficient to reveal the answer. If it is not — if you get to the end of the explanation and the problem is still unclear — you now have a well-structured question rather than a vague one.

**Limitations:** Requires self-discipline and willingness to slow down. Does not help when the problem is genuinely beyond your current knowledge of the system. In those cases, a human rubber duck — a colleague who can ask questions — is more effective.

---

## ◎ VI. Reduction

**What it is:** Simplifying a broken program or scenario until only the minimum code necessary to reproduce the bug remains.

**When to use:** When the codebase is large and the bug's location is unknown. When you want to share a reproducible example with a colleague or post to a forum.

**How it works:**

I. Start with the full program that exhibits the bug.

II. Remove components, methods, or code blocks that are not essential to the reproduction.

III. Replace complex dependencies (databases, networks, APIs) with simple stubs or hard-coded values.

IV. Continue reducing until the smallest possible program that still exhibits the bug remains.

!!! info "The minimal reproducible example"
    The result of reduction is called a **minimal reproducible example** (MRE). It is the standard expected when asking for help on StackOverflow or GitHub Issues — not because the community is demanding, but because reducing to an MRE almost always surfaces the bug before the question is even posted. The discipline of reduction is itself a debugging technique.

**Limitations:** Time-consuming for complex systems. Not always possible when the bug depends on specific environmental conditions.

---

## ◎ VII. Static Analysis

**What it is:** Analysing source code without executing it, using automated tools to identify potential errors, code quality issues, security vulnerabilities, or style violations.

**When to use:** As a preventive measure, ideally integrated into the development workflow so analysis runs automatically on every change.

**What it finds:**

- Unused variables and unreachable code
- Potential null dereferences
- Type mismatches
- Common security vulnerabilities (SQL injection, XSS patterns)
- Style violations and complexity warnings

**Common static analysis tools:**

| Language | Tool |
|----------|------|
| Java | Checkstyle, SpotBugs, SonarQube |
| C# | Roslyn Analyzers, SonarQube |
| Kotlin | Android Lint, Detekt |
| JavaScript / TypeScript | ESLint, TSLint |
| Python | Pylint, Flake8, mypy |
| C / C++ | Clang-Tidy, Cppcheck |

**Limitations:** Catches a specific class of bugs — not all bugs. Produces false positives that must be evaluated. Does not catch runtime or logical errors that depend on execution state.

---

## ◎ VIII. Post-Mortem Debugging

**What it is:** Investigating a failure that has already occurred — after the crash, after the incident, after the system has stopped — by analysing the evidence it left behind.

**When to use:** When the failure cannot be reproduced interactively. When the crash occurred in production, on a user's device, or in a non-interactive environment. When a system has already restarted by the time investigation begins.

**What evidence is used:**

- Log files from before and during the failure
- Core dumps (a snapshot of the process's memory at the time of the crash)
- Stack traces from crash reports
- Monitoring metrics (CPU, memory, network) around the time of failure
- User reports describing what they were doing

**The investigative approach:**

I. Establish the timeline: when did normal behaviour end, and when did the failure occur?

II. Read available logs from the relevant time window — look for errors, warnings, or unexpected state changes.

III. Examine the stack trace (if available) to identify the crash site.

IV. Reconstruct the sequence of events by correlating multiple evidence sources.

V. Form and test hypotheses about the root cause — even if testing means deploying a fix and observing whether the failure recurs.

**Limitations:** You cannot pause and inspect state interactively. The quality of the investigation is bounded by the quality of the logging and monitoring in place before the failure. This is why logging and structured monitoring are preventive practices as much as investigative ones.

---

## ◎ IX. Remote Debugging

**What it is:** Debugging an application that is running in a different environment from your local machine — a remote server, a cloud instance, a connected device, or an emulator.

**When to use:** When a bug only occurs in a specific environment that cannot be replicated locally. When debugging a mobile application running on a physical device. When working with server-side code in a staging or production environment.

**How it works:**

Most major IDEs support attaching a debugger to a remote process. The application is started in debug mode with a specific port open, and the IDE connects to that port over the network. Once connected, the debugger operates identically to local debugging — breakpoints, stepping, watch expressions.

=== "⌨ Android Studio — physical device"
    Android Studio debugs on a connected physical device over USB (or wirelessly over the same network) transparently. Select the connected device as the deployment target and click the debug button.

=== "⌨ IntelliJ — remote JVM"
    Create a **Remote JVM Debug** run configuration. The target JVM is started with:
    ```
    -agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=*:5005
    ```
    IntelliJ connects to port 5005. Breakpoints and stepping work as normal.

=== "⌨ VS Code — remote development"
    VS Code's Remote Development extension connects directly to a remote machine via SSH. The full debugger is available as if the code were running locally.

**Limitations:** Requires network access and appropriate security configuration. Performance can be affected by network latency. Not available in all environments.

---

## ◎ Summary — Choosing the Right Technique

| Situation | Consider |
|-----------|----------|
| Quick question about a specific value | Print / log debugging |
| Complex state across many variables | Interactive debugger |
| Bug location is unknown, code range is large | Binary search / divide and conquer |
| Bug was introduced in a recent commit | `git bisect` |
| Stuck and cannot see the problem | Rubber duck debugging |
| Need a minimal example to share | Reduction |
| Catching bugs before they occur | Static analysis |
| Investigating a past failure | Post-mortem debugging |
| Bug only occurs on a remote system | Remote debugging |

---

*Continue to: [Debugging Tools Overview →](tools_overview.md)*

*← Back to: [Section Index](index.md)*

---

*[MRE]: Minimal Reproducible Example
*[IDE]: Integrated Development Environment
*[API]: Application Programming Interface
*[XSS]: Cross-Site Scripting
