# 02 · Investigation — The Investigative Debugger

---

## ◈ About This Section

This section is for programmers who can already write working code but who struggle when bugs are not obvious — when errors appear far from their cause, when projects span multiple files, or when a framework is involved.

The shift at this level is philosophical. Debugging is no longer about fixing what is visible. It is about **explaining behaviour you do not yet understand**.

---

## ◈ Who Should Read This Section

- Intermediate students comfortable with syntax but frustrated by complex bugs
- Anyone whose programs compile but behave unexpectedly
- Students using frameworks, libraries, or multi-file projects for the first time
- Anyone who has completed [01 · Foundations](../01_foundations/index.md)

---

## ◈ Pages in This Section

| Page | What It Covers |
|------|---------------|
| [The Investigative Mindset](investigative_mindset.md) | Debugging as investigation not reaction, evidence over intuition, the shift from fixing to understanding |
| [Core Investigation Skills](core_investigation_skills.md) | Reproducing bugs reliably, reducing the problem space, logging as evidence, symptoms vs root causes, forming hypotheses, explaining the fix |
| [Print and Log Debugging](print_log_debugging.md) | When and how to use print statements and logging strategically — not as spam, but as targeted evidence gathering |
| [Using a Debugger](using_a_debugger.md) | Breakpoints (line, conditional, method), step into / step over / step out, watch windows, variable inspection |
| [Stack Trace Analysis](stack_trace_analysis.md) | Reading a stack trace from top to bottom, distinguishing your code from framework code, using the trace as a map |
| [IDE-Specific Notes (Summary)](ide_specific_notes.md) | ⌨ Brief orientation to debugging features in Android Studio, VS Code, Visual Studio, NetBeans/IntelliJ (deep guides in Appendix G) |
| [Investigation Exercises](investigation_exercises.md) | ▣ Multi-file bugs, framework-related issues, logging exercises, stack trace interpretation |

---

## ◈ Assumed Prior Knowledge

Readers are expected to have completed or be familiar with [01 · Foundations](../01_foundations/index.md). This section does not re-teach error types or basic workflows.

---

## ◈ What This Section Does Not Cover

Debugging in production systems, team-based debugging, unit testing, and configuration errors belong in [03 · Strategy](../03_strategy/index.md).

---

**A bug you can reproduce is already half solved.**
