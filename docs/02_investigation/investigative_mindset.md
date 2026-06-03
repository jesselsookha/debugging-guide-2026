# ◈ The Investigative Mindset

---

## ◈ When Fixing Stops Working

In the early stages of programming, debugging has a satisfying simplicity:

```
Error appears → Read message → Fix line → Run again
```

This works well for isolated programs — short files, single responsibilities, inputs you control. The error message points directly at the problem. The fix is usually local. The feedback loop is fast.

As programs grow, this stops working.

Modern applications span multiple files and layers. They depend on frameworks that control execution flow invisibly. They react to user input, device state, network responses, and lifecycle events. When something breaks in this kind of system, the error message shows where the program *crashed* — not necessarily where the problem *originated*. The crash site and the bug site are often different places entirely.

At this point, the programmer faces a choice. They can keep trying to fix what is visible, changing things and hoping the problem resolves — an approach sometimes called *shotgun debugging* or *stare and hope*. Or they can change their approach entirely and treat the bug as something to be **investigated** rather than simply fixed.

!!! warning "Non-strategies — what not to do"
    The University of Washington's CS14 debugging curriculum identifies two non-strategies that students should explicitly avoid:

    **Shotgun debugging** — trying every possible thing you can think of, changing loop bounds, restructuring conditions, adding and removing code — without evidence of what is actually wrong. This leads to extra frustration, rarely finds the real bug, and becomes completely unmanageable as programs grow.

    **Stare and hope** — staring at your code and expecting your brain to find the bug by inspection alone. The computer is better at running the program than your brain is. Use it.

---

## ◈ Investigation vs. Reaction

The investigative mindset is the shift from *reacting* to bugs to *investigating* them.

A **reactive** debugger responds to symptoms. They see a crash and try to fix the crash site. They see wrong output and change the output line. They make changes until something works, often without fully understanding what changed or why the original code failed.

An **investigative** debugger treats a bug as a question: *Why did this system behave this way under these conditions?* They gather evidence before making any change. They form a hypothesis. They test it deliberately. They explain the fix before they commit it.

The difference is not intelligence or experience — it is method.

| Reactive approach | Investigative approach |
|-------------------|------------------------|
| Sees a crash, changes the crash site | Sees a crash, traces back to the origin |
| Makes multiple changes at once | Makes one change at a time |
| Hopes the bug is gone after a fix | Verifies the fix resolves the root cause |
| Cannot explain why the fix worked | Can explain every step of the diagnosis |
| Gets faster only by getting luckier | Gets faster by building a mental model |

---

## ◈ Evidence Over Intuition

Intuition is useful. It can point you toward the right area of a codebase. It can suggest which hypothesis to test first. But it is not a substitute for evidence.

Evidence in debugging comes from several sources:

**The error message and stack trace** — the system's own account of what went wrong and where.

**Log output** — a record of what the program actually did, in the order it did it, with the values it held at each point.

**The debugger** — a window into the running program: variable values, call stack, execution position, all visible in real time.

**Test cases** — specific inputs that reproduce the problem, allowing controlled experiments.

**The difference between expected and actual behaviour** — the most fundamental piece of evidence. Without articulating this precisely, no investigation can begin.

!!! tip "Start with what you know"
    Before opening a debugger or adding a log statement, write down what you know:

    - What did you expect to happen?
    - What actually happened?
    - Under what conditions does the problem occur?
    - When does it *not* occur?

    The answers to these four questions define the boundaries of your investigation. Evidence gathered inside those boundaries is useful. Evidence gathered outside them is noise.

---

## ◈ Unexpected Behaviour Is Data

One of the most useful mental shifts in investigative debugging is learning to treat unexpected behaviour as *data* rather than as an obstacle.

When a program produces output you did not expect, that unexpected output is telling you something specific about the state of the program at that moment. It is not random. It is not a mystery. It is a consequence of something that happened earlier in the execution — something you can trace back and identify if you approach it methodically.

!!! example "Treating output as data"
    A function that adds items to a shopping cart returns the correct total for the first two items, then returns wrong totals for all subsequent items.

    **Reactive response:** change the calculation logic and hope it improves.

    **Investigative response:** the pattern itself is data. The first two work. The third does not. Something changes between the second and third call. Is the accumulation logic correct? Is state being reset incorrectly? Is there an off-by-one in the loop? The pattern narrows the investigation before a single log statement is written.

---

## ◈ Fewer Changes, Clearer Learning

A principle that runs through every investigative technique in this section:

> **Make one change. Test. Observe. Then decide what to do next.**

This is not caution for its own sake. It is the scientific method applied to software.

When you change multiple things at once and the bug disappears, you do not know which change resolved it. When the bug changes character, you do not know which of your changes caused the new behaviour. The more changes you make simultaneously, the less information you gain from each test.

One change produces a clear result: this hypothesis was right, or it was wrong. Either outcome advances the investigation. Multiple simultaneous changes produce ambiguity.

!!! danger "The multiple-change trap"
    Changing many things at once is a reliable way to spend more time debugging, not less. It creates dependencies between changes, makes it impossible to roll back precisely, and prevents you from building the understanding that would make the next bug faster to resolve.

---

## ◈ Removing Code Is a Valid Action

Investigative debuggers frequently discover that the most effective path to a solution involves *deleting* code before rewriting it.

When a bug is embedded in a complex method, removing parts of that method and testing what remains is not failure — it is reduction. It narrows the problem space. If removing a section makes the bug disappear, that section is relevant. If the bug persists, that section is not the cause and the search continues elsewhere.

This is closely related to the binary search technique for bugs, covered in detail in [Core Investigation Skills](core_investigation_skills.md).

The principle: **complexity hides mistakes. Simplicity reveals them.**

---

## ◈ Explain Before You Move On

The final element of the investigative mindset, and the one most often skipped under time pressure:

> **A fix is incomplete until you can explain it.**

After resolving a bug, ask yourself three questions:

I. Why did this fail?
II. Which assumption was wrong?
III. Why does this fix work?

If you cannot answer all three, the fix may be correct — or it may be luck. The next time you encounter a related bug, you will have no transferable understanding to draw on. The time spent on explanation is never wasted. It is the mechanism by which bugs become learning.

!!! quote "On explanation"
    *"Explanation transforms bugs into knowledge."*

    *— from the old chat design brief for the Investigative Debugger stage*

---

*Continue to: [Core Investigation Skills →](core_investigation_skills.md)*

*← Back to: [Section Index](index.md)*
