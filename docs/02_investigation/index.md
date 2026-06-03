# 02 · Investigation — The Investigative Debugger

---

## ◈ What This Section Is About

At the foundational level, debugging is about reading. You learn to read error messages, identify error types, locate the source, and follow a repeatable workflow. That is the baseline.

This section is about what comes next — the shift that happens when those foundational skills are no longer enough.

As programs grow, bugs stop being obvious. Errors appear far from their cause. A crash in the UI originates from a problem in the data layer. A function produces the wrong value because of an assumption made two files away. A framework controls execution in ways the programmer cannot see directly. In these situations, reading the error message is just the beginning. The real work is **explaining behaviour you do not yet understand**.

This is investigative debugging. It is not a different kind of work from foundational debugging — it is the same process, applied with greater depth, greater precision, and a more deliberate use of the tools available.

!!! quote "The defining shift"
    *At the foundational level: "I fix errors."*
    *At the investigative level: "I explain behaviour."*

---

## ◈ Who Should Read This Section

Read this section if any of the following applies to you:

- You can write working programs but struggle when bugs are not obvious
- Your programs compile and run but produce results you cannot explain
- You work across multiple files, or with frameworks and libraries
- You have been debugging by commenting out code randomly and hoping for the best
- You completed [01 · Foundations](../01_foundations/index.md) and are ready for the next level

This is a capability-based level, not a year-based one. A confident first-year student working on a multi-file project may need this section. A third-year student who has been guessing their way through bugs may need to visit [01 · Foundations](../01_foundations/index.md) first.

---

## ◈ Pages in This Section

| Page | What It Covers |
|------|----------------|
| [The Investigative Mindset](investigative_mindset.md) | The philosophical shift from reaction to investigation — evidence over intuition, explaining behaviour not just fixing symptoms |
| [Core Investigation Skills](core_investigation_skills.md) | Reproducing bugs reliably, reducing the problem space, distinguishing symptoms from root causes, forming and testing hypotheses |
| [Print and Log Debugging](print_log_debugging.md) | Strategic use of print statements and logging — when to use them, what to log, and when to stop |
| [Using a Debugger](using_a_debugger.md) | Breakpoints, stepping (step into / step over / step out), watch windows, variable inspection — the full toolkit |
| [Stack Trace Analysis](stack_trace_analysis.md) | Reading stack traces with confidence — from top to bottom, finding your code, using the trace as a map |
| [IDE-Specific Notes (Summary)](ide_specific_notes.md) | ⌨ Brief orientation to debugging features in Android Studio, VS Code, Visual Studio, and IntelliJ/NetBeans |
| [Investigation Exercises](investigation_exercises.md) | ▣ Multi-file bugs, logging exercises, stack trace interpretation, framework-related scenarios |

---

## ◈ Assumed Prior Knowledge

Readers should be able to:

- Identify the four error types (syntax, runtime, logical, semantic)
- Read an error message and locate the file and line number
- Follow the six-step foundational workflow
- Use their IDE's output window, terminal, or Logcat panel

This section does not re-teach those skills. If any of the above is unfamiliar, start with [01 · Foundations](../01_foundations/index.md).

---

## ◈ What This Section Does Not Cover

Debugging in production systems, team-based debugging, version control, unit testing, and configuration errors belong in [03 · Strategy](../03_strategy/index.md). This section focuses on single-developer investigation of complex bugs within a local development environment.

---

*Continue to: [The Investigative Mindset →](investigative_mindset.md)*
