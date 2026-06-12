# ◉ The Basic Debugging Workflow

---

## ◉ Why a Workflow Matters

Without a process, debugging is guessing. Guessing sometimes works — but it does not build skill, it does not scale to complex problems, and it produces no understanding of what actually went wrong or why the fix worked.

A workflow does the opposite. It is a **repeatable sequence of steps** that transforms an error from a crisis into a solvable problem. Each step narrows the space of possible causes. Each step produces information. By the time a deliberate change is made, it is based on evidence — not hope.

The workflow introduced here is foundational. It applies to every error type, every language, every environment. More advanced debugging techniques introduced later in this guide are extensions of this same process — not replacements for it.

!!! info "This workflow is a loop, not a line"
    The six steps are not a one-time checklist. After a change is made and tested, if the error persists or a new error appears, the process begins again from Step I — with the new information gathered from the previous pass. This is why it is represented as a cycle.

---

## ◉ The Six-Step Workflow

<div style="text-align: center; margin: 2rem 0;">
<svg viewBox="0 0 520 520" xmlns="http://www.w3.org/2000/svg" style="max-width: 520px; width: 100%; height: auto;" aria-label="The six-step debugging workflow shown as a circular cycle">

  <!-- Outer ring -->
  <circle cx="260" cy="260" r="230" fill="none" stroke="currentColor" stroke-width="1.5" stroke-dasharray="6 4" opacity="0.25"/>

  <!-- Centre label -->
  <circle cx="260" cy="260" r="72" fill="none" stroke="currentColor" stroke-width="1.5"/>
  <text x="260" y="252" text-anchor="middle" font-family="IBM Plex Mono, monospace" font-size="12" fill="currentColor" font-weight="700">DEBUGGING</text>
  <text x="260" y="270" text-anchor="middle" font-family="IBM Plex Mono, monospace" font-size="12" fill="currentColor" font-weight="700">WORKFLOW</text>
  <text x="260" y="288" text-anchor="middle" font-family="IBM Plex Mono, monospace" font-size="10" fill="currentColor" opacity="0.7">← repeat →</text>

  <!-- Step nodes — exact positions on circle radius 230, center 260,260 -->
  <!-- Step I   — top          (260, 30)   -->
  <!-- Step II  — top-right    (459.1, 145) -->
  <!-- Step III — bottom-right (459.1, 375) -->
  <!-- Step IV  — bottom       (260, 490)  -->
  <!-- Step V   — bottom-left  (60.9, 375) -->
  <!-- Step VI  — top-left     (60.9, 145) -->

  <!-- Connecting arcs (perfect circular arcs) -->
  <!-- I → II -->
  <path d="M 320 58 A 230 230 0 0 1 421 135" fill="none" stroke="currentColor" stroke-width="1.5" marker-end="url(#arrow)"/>
  <!-- II → III -->
  <path d="M 459 190 A 230 230 0 0 1 459 330" fill="none" stroke="currentColor" stroke-width="1.5" marker-end="url(#arrow)"/>
  <!-- III → IV -->
  <path d="M 421 385 A 230 230 0 0 1 320 462" fill="none" stroke="currentColor" stroke-width="1.5" marker-end="url(#arrow)"/>
  <!-- IV → V -->
  <path d="M 200 462 A 230 230 0 0 1 99 385" fill="none" stroke="currentColor" stroke-width="1.5" marker-end="url(#arrow)"/>
  <!-- V → VI -->
  <path d="M 61 330 A 230 230 0 0 1 61 190" fill="none" stroke="currentColor" stroke-width="1.5" marker-end="url(#arrow)"/>
  <!-- VI → I -->
  <path d="M 99 135 A 230 230 0 0 1 200 58" fill="none" stroke="currentColor" stroke-width="1.5" marker-end="url(#arrow)"/>

  <!-- Arrowhead marker -->
  <defs>
    <marker id="arrow" markerWidth="8" markerHeight="8" refX="6" refY="3" orient="auto">
      <path d="M0,0 L0,6 L8,3 z" fill="currentColor"/>
    </marker>
  </defs>

  <!-- Step I — top -->
  <circle cx="260" cy="30" r="36" fill="currentColor"/>
  <text x="260" y="25" text-anchor="middle" font-family="IBM Plex Mono, monospace" font-size="9" fill="white" font-weight="700">STEP I</text>
  <text x="260" y="38" text-anchor="middle" font-family="IBM Plex Mono, monospace" font-size="8.5" fill="white">STOP</text>
  <text x="260" y="49" text-anchor="middle" font-family="IBM Plex Mono, monospace" font-size="8.5" fill="white">&amp; READ</text>

  <!-- Step II — top-right -->
  <circle cx="459.1" cy="145" r="36" fill="currentColor"/>
  <text x="459.1" y="140" text-anchor="middle" font-family="IBM Plex Mono, monospace" font-size="9" fill="white" font-weight="700">STEP II</text>
  <text x="459.1" y="153" text-anchor="middle" font-family="IBM Plex Mono, monospace" font-size="8.5" fill="white">IDENTIFY</text>
  <text x="459.1" y="164" text-anchor="middle" font-family="IBM Plex Mono, monospace" font-size="8.5" fill="white">TYPE</text>

  <!-- Step III — bottom-right -->
  <circle cx="459.1" cy="375" r="36" fill="currentColor"/>
  <text x="459.1" y="370" text-anchor="middle" font-family="IBM Plex Mono, monospace" font-size="9" fill="white" font-weight="700">STEP III</text>
  <text x="459.1" y="383" text-anchor="middle" font-family="IBM Plex Mono, monospace" font-size="8.5" fill="white">LOCATE</text>
  <text x="459.1" y="394" text-anchor="middle" font-family="IBM Plex Mono, monospace" font-size="8.5" fill="white">SOURCE</text>

  <!-- Step IV — bottom -->
  <circle cx="260" cy="490" r="36" fill="currentColor"/>
  <text x="260" y="485" text-anchor="middle" font-family="IBM Plex Mono, monospace" font-size="9" fill="white" font-weight="700">STEP IV</text>
  <text x="260" y="498" text-anchor="middle" font-family="IBM Plex Mono, monospace" font-size="8.5" fill="white">EXPECTED</text>
  <text x="260" y="509" text-anchor="middle" font-family="IBM Plex Mono, monospace" font-size="8.5" fill="white">vs ACTUAL</text>

  <!-- Step V — bottom-left -->
  <circle cx="60.9" cy="375" r="36" fill="currentColor"/>
  <text x="60.9" y="370" text-anchor="middle" font-family="IBM Plex Mono, monospace" font-size="9" fill="white" font-weight="700">STEP V</text>
  <text x="60.9" y="383" text-anchor="middle" font-family="IBM Plex Mono, monospace" font-size="8.5" fill="white">ONE</text>
  <text x="60.9" y="394" text-anchor="middle" font-family="IBM Plex Mono, monospace" font-size="8.5" fill="white">CHANGE</text>

  <!-- Step VI — top-left -->
  <circle cx="60.9" cy="145" r="36" fill="currentColor"/>
  <text x="60.9" y="140" text-anchor="middle" font-family="IBM Plex Mono, monospace" font-size="9" fill="white" font-weight="700">STEP VI</text>
  <text x="60.9" y="153" text-anchor="middle" font-family="IBM Plex Mono, monospace" font-size="8.5" fill="white">TEST &amp;</text>
  <text x="60.9" y="164" text-anchor="middle" font-family="IBM Plex Mono, monospace" font-size="8.5" fill="white">REPEAT</text>

</svg>
</div>

---

## ◉ Step I — STOP and Read

The moment an error appears, the instinct is to act. Change something. Try something. Make it go away.

Resist that instinct.

**Stop. Read the error message in full.** This means: the error type, the description, the file name, the line number, and — if present — the stack trace. Do not close the error window. Do not start editing code. Read everything the system has given you.

This step costs less than thirty seconds. It frequently reveals the entire answer before any other step is needed.

!!! danger "The most expensive debugging mistake"
    Changing code before reading the error message is the single most common and most costly beginner habit. Every change made without reading the error first introduces the risk of masking the original problem, creating a new one, and losing the information that was already on the screen.

**Questions to answer in Step I:**

- What does the error message say, in plain language?
- What type of error is it? (Syntax, runtime, logical, semantic)
- What file and line number is reported?

---

## ◉ Step II — Identify the Error Type

Once you have read the message, classify the error. The type determines where to look and what kind of fix is likely needed.

| If the error is... | Then... |
|--------------------|---------|
| **Syntax** | The program did not compile — go to the reported line and correct the grammatical mistake |
| **Runtime** | The program crashed during execution — the stack trace shows you where |
| **Logical** | The program runs but produces wrong output — compare expected vs actual output |
| **Semantic** | The code means something different from what was intended — review the logic of the specific statement |

!!! question "Can't identify the type?"
    If you are unsure, ask: *Did the program compile?* If no → syntax error. *Did it crash while running?* If yes → runtime error. *Did it run to completion but produce wrong output?* → logical or semantic error. Return to [Understanding Errors](understanding_errors.md) if you need to review the distinctions.

---

## ◉ Step III — Locate the Source

Use the information from Steps I and II to find exactly where in your code the problem originates.

For **syntax errors**: go directly to the reported file and line. Read the line and the line before it.

For **runtime errors**: open the stack trace. Find the first line that references your own code — your class name, your file name. That is your starting point.

For **logical and semantic errors**: there is no error message to guide you. Start at the point where the output first diverges from what you expected.

!!! tip "Navigate directly — don't scroll"
    Every IDE has a way to jump directly to a specific line. In most environments, clicking on the file and line number in the error output navigates there instantly. Use it. Do not scroll manually through your code looking for the problem.

=== "⌨ Android Studio / IntelliJ"
    Click the blue file link in the Logcat or build output panel. The editor jumps directly to the line.

=== "⌨ Visual Studio"
    Double-click the error in the **Error List** panel. The editor navigates to the line.

=== "⌨ VS Code"
    Click the file path in the **Problems** panel or **Terminal** output. The editor jumps to the line.

=== "⌨ NetBeans"
    Click the error in the **Output** window. The editor highlights the line.

---

## ◉ Step IV — Expected vs. Actual

Before making any change, articulate precisely what you expected the program to do and what it actually did.

This sounds obvious. It is frequently skipped — and that skip is why many programmers make the wrong change.

Write it down if it helps. Saying it aloud helps even more. The act of articulating the gap often reveals the cause before any code is touched.

!!! example "Expected vs. Actual in practice"
    **Vague (not useful):** *"It doesn't work."*

    **Precise (useful):** *"I expected the method `calculateTotal()` to return `150` when called with the values `[50, 50, 50]`. Instead it returned `50`. The last value in the list is being returned instead of the sum."*

    The precise version already points at the cause: the accumulation logic is wrong, or the return statement is in the wrong place.

**Questions to answer in Step IV:**

- What did you *expect* the program to do at this point?
- What did it *actually* do?
- Where exactly does the behaviour diverge from the expectation?

---

## ◉ Step V — Make One Change

Form a hypothesis. Based on what you have read, identified, located, and compared — what do you think is wrong?

Make **one change** based on that hypothesis. One. Not two, not three.

!!! danger "Why one change at a time is non-negotiable"
    If you change three things and the error disappears, you do not know which change fixed it. If the error changes to a different error, you do not know which change caused the new problem. Making multiple simultaneous changes destroys the cause-and-effect relationship between your actions and their results.

    One change. Test. Observe. Repeat.

**Good hypothesis formation:**

=== "Java example"
    ```java
    // Error: NullPointerException on this line
    System.out.println(user.getName());
    ```
    **Hypothesis:** `user` is null at this point. It was not initialised before this call.

    **One change:** add a null check, or trace back to where `user` should have been initialised and fix that.

=== "C# example"
    ```csharp
    // Wrong output: total is always the last value, not the sum
    foreach (int score in scores)
    {
        total = score; // (1)
    }
    ```
    1. Hypothesis: `total` is being assigned instead of accumulated.

    **One change:** `total = score;` → `total += score;`

=== "Kotlin example"
    ```kotlin
    // Error: loop body never executes
    for (i in 10..1) {
        println(i)
    }
    ```
    **Hypothesis:** `10..1` is an empty range in Kotlin — it does not count down.

    **One change:** `10..1` → `10 downTo 1`

=== "JavaScript example"
    ```javascript
    // Wrong: function always returns undefined
    function getGreeting(name) {
        const message = "Hello, " + name;
    } // (1)
    ```
    1. Hypothesis: the function builds the message but never returns it.

    **One change:** add `return message;` before the closing brace.

---

## ◉ Step VI — Test and Repeat

Run the program. Observe the result.

**If the error is gone and the output is correct:** the fix worked. Before moving on — make sure you can explain *why* it worked. A fix you cannot explain is temporary luck, not understanding.

**If the error persists:** the hypothesis was wrong, or the fix was incomplete. The new error output is new evidence. Return to Step I with this additional information and work through the process again.

**If a different error appears:** the change resolved one problem but revealed or introduced another. That is progress. Start again from Step I with the new error.

!!! success "What a completed cycle looks like"
    I. Read: `NullPointerException` on `Main.java:24`, calling `user.getName()`

    II. Identified: runtime error — a null object was accessed

    III. Located: `Main.java` line 24, the `user` object

    IV. Expected `user` to be initialised in `setupUser()` — but `setupUser()` had not been called before line 24

    V. One change: moved the `setupUser()` call to before line 24

    VI. Test: program runs correctly. Fix explained: `user` was null because the initialisation method had not been called in the right sequence.

---

## ◉ The Workflow in Full — A Reference Card

```
┌──────────────────────────────────────────────────────────────────┐
│  THE SIX-STEP DEBUGGING WORKFLOW                                 │
├──────────────────────────────────────────────────────────────────┤
│  I.   STOP & READ    — Read the full error before touching code  │
│  II.  IDENTIFY       — What type of error is this?               │
│  III. LOCATE         — What file, line, and method?              │
│  IV.  COMPARE        — Expected output vs. actual output         │
│  V.   ONE CHANGE     — Form a hypothesis. Change one thing.      │
│  VI.  TEST & REPEAT  — Run. Observe. Return to I if needed.      │
└──────────────────────────────────────────────────────────────────┘
```

!!! note "Save this. Use it."
    A printable version of this checklist is available in [Appendix A](../05_appendices/checklist.md). Keep it visible while you work until the process becomes habit.

---

*Continue to: [Asking for Help (Human or AI) →](asking_for_help.md)*

*← Back to: [Section Index](index.md)*

---

*[IDE]: Integrated Development Environment
