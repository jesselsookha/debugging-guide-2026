# 03 · Strategy — The Strategic Debugger

---

## ◆ About This Section

This section is for advanced students, final-year project students, and junior developers who are working in real systems — where bugs do not come with a clear error message, where the environment itself can be the problem, and where fixing something quietly is not good enough.

At this level, debugging is a **professional activity**. It involves communication, documentation, prevention, and accountability.

---

## ◆ Who Should Read This Section

- Final-year undergraduate students working on capstone projects
- Students beginning internships or workplace experience
- Junior developers working in teams for the first time
- Anyone who has completed or is familiar with [02 · Investigation](../02_investigation/index.md)

---

## ◆ Pages in This Section

| Page | What It Covers |
|------|---------------|
| [Debugging in Systems](debugging_in_systems.md) | Framework-driven behaviour, lifecycle issues, state management failures, configuration and environment problems, non-crashing failures |
| [Professional Debugging Practices](professional_practices.md) | Debugging with version control (git bisect), writing bug reports and post-mortems, debugging in teams, knowledge bases and internal documentation |
| [Preventive Measures](preventive_measures.md) | Unit testing, test-driven development (TDD), assertions and defensive programming, code reviews as a debugging tool |
| [Strategy Exercises](strategy_exercises.md) | ▣ Capstone-style bug scenarios, configuration issue analysis, user-reported bug triage, post-mortem writing |

---

## ◆ Assumed Prior Knowledge

Readers are expected to be comfortable with [01 · Foundations](../01_foundations/index.md) and [02 · Investigation](../02_investigation/index.md). This section assumes you can read a stack trace, use a debugger, and reproduce a bug reliably.

---

## ◆ What This Section Does Not Cover

Specific language or framework documentation. This section is platform-agnostic. Platform-specific notes are in [Appendix G](../05_appendices/platform_android_studio.md).

---

**A fix that cannot be explained, reviewed, or documented is not production-ready.**
