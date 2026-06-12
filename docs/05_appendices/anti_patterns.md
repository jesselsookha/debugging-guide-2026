# Appendix D · Debugging Anti-Patterns

---

## ◎ What an Anti-Pattern Is

An anti-pattern is a response to a recurring problem that appears to be a solution but is not — or that actively makes things worse. In debugging, anti-patterns are the habits and behaviours that feel productive in the moment but consume time, obscure the real cause, and prevent learning.

Recognising anti-patterns in your own behaviour is as important as knowing the right techniques. The checklist in [Appendix A](checklist.md) is designed to interrupt anti-patterns before they take hold. This page names them, explains why they fail, and describes what to do instead.

!!! note "Anti-patterns are not character flaws"
    These patterns are common precisely because they feel natural under pressure. Frustration, time pressure, and unfamiliarity with a codebase all push programmers toward them. Recognising them is the first step to replacing them with better habits.

---

## ◉ I. Random Changes

**What it looks like:**
Changing code without forming a hypothesis — adjusting numbers, swapping conditions, adding or removing lines — and running the program to see if it works. Sometimes called *"trial and error"* when it is actually trial without understanding.

**Why it fails:**

- You cannot learn from a change you did not intend
- If the change appears to fix the bug, you do not know why — and the fix may be incomplete, may have introduced a new bug, or may only mask the symptom
- The longer this continues, the further the code drifts from its original state and the harder it becomes to reason about

!!! failure "Random changes in practice"
    ```java
    // Bug: NullPointerException on user.getName()
    // Random change attempt 1: add a try-catch and ignore
    try {
        label.setText(user.getName());
    } catch (Exception e) { }  // bug hidden, not fixed

    // Random change attempt 2: hard-code a string
    label.setText("Unknown");   // symptom removed, root cause untouched
    ```

**What to do instead:** Stop. Read the error message. Identify the type. Form a hypothesis — *"I believe `user` is null at this point because..."* — and make one change that directly tests that hypothesis.

---

## ◉ II. Multiple Changes at Once

**What it looks like:**
Making two, three, or more changes to the code before running it again to see whether the bug is resolved.

**Why it fails:**

- If the bug disappears, you do not know which change fixed it
- If a new bug appears, you do not know which of your changes caused it
- If the original bug persists, you cannot tell whether your changes were relevant or not
- The relationship between cause and effect is destroyed

!!! danger "The one-change rule is non-negotiable"
    This is not a preference or a guideline — it is a requirement for systematic debugging. Multiple simultaneous changes produce ambiguity. Ambiguity produces wasted time.

**What to do instead:** Form one hypothesis. Make one change. Run the program. Observe the result. Then decide what to do next.

---

## ◉ III. Ignoring the Error Message

**What it looks like:**
Closing the error window quickly, skimming the error without reading it carefully, or immediately searching for the error text online without first reading what it says in plain language.

**Why it fails:**

- The error message is the most information-dense piece of evidence available
- The file name, line number, error type, and description — all of which are in the error message — are the starting point for every debugging approach
- Searching online for an error you have not read carefully often produces the wrong results, because the search query is imprecise

**What to do instead:** Read the full error message before doing anything else. Read the type, the description, the file, and the line number. Ask: *"What is this error message telling me, in plain language?"* Then — and only then — decide what action to take.

!!! quote "The error message is a communication"
    The system is not producing error messages to frustrate you. It is telling you, as precisely as it can, what it found when it tried to execute your instructions. The message is the evidence. Read it.

---

## ◉ IV. Fixing the Symptom, Not the Root Cause

**What it looks like:**
Resolving the immediate visible failure without understanding or addressing why it occurred. The error disappears, but the underlying problem remains.

**Examples:**

- Adding a null check to prevent a crash, without investigating why the object is null
- Catching an exception and swallowing it silently, rather than handling the failure
- Hard-coding a value that should be calculated, to make the test pass

**Why it fails:**

- The bug will return — in the same place, in a different form, or under different conditions
- Symptom fixes accumulate technical debt — they make the codebase harder to understand and maintain
- The root cause remains in the system, potentially causing related failures elsewhere

!!! example "Symptom fix vs. root cause fix"
    **Bug:** `NullPointerException` when calling `user.getDisplayName()`

    **Symptom fix:**
    ```java
    if (user != null) {
        label.setText(user.getDisplayName());
    }
    // Crash prevented. But: why is user null? When does this happen?
    // The root cause is unknown and unaddressed.
    ```

    **Root cause investigation:** Traces back to `loadUser()`, which returns `null` when the network call fails. The root cause is missing error handling in `loadUser()`. The fix is to handle the network failure and communicate it to the calling code — not to silently suppress the consequence of the failure.

**What to do instead:** Ask *"What had to be true for this to occur?"* and trace backward until the actual source of the problem is found. Fix that.

---

## ◉ V. Skipping Reproduction

**What it looks like:**
Attempting to fix a bug before confirming that you can reliably make it happen on demand.

**Why it fails:**

- Without reliable reproduction, you cannot verify whether a fix actually resolves the bug
- You may make a change, observe that the bug does not appear, and conclude it is fixed — when in fact it still occurs under the same conditions as before

**What to do instead:** Before making any change, confirm: *"Can I make this bug happen every time I follow these specific steps?"* If the answer is no, make the reproduction reliable first. Document the exact inputs or conditions that trigger it.

---

## ◈ VI. Copy-Pasting Without Understanding

**What it looks like:**
Taking a code solution from StackOverflow, a forum, or an AI tool and applying it without understanding what it does or whether it is appropriate.

**Why it fails:**

- Code that solves a different problem in a different context may not solve your problem, or may introduce new problems in yours
- You introduce code you cannot debug — if it fails or produces unexpected behaviour, you have no basis for reasoning about why
- Accumulating ununderstood code creates a codebase that nobody can maintain with confidence

!!! warning "The AI-specific version of this pattern"
    AI tools produce plausible-sounding code quickly. This makes copy-paste without understanding particularly tempting when using AI assistance. The test: *"Can I explain what every line of this code does and why?"* If not, do not apply it until you can. Ask a follow-up question. Read the explanation. Then apply the change.

**What to do instead:** Before applying any suggested code — from any source — read it line by line and confirm you understand what each part does. If something is unclear, look it up or ask for an explanation before applying it.

---

## ◈ VII. Not Documenting What You Tried

**What it looks like:**
Investigating a bug for an hour, trying several approaches, and not keeping any record of what was attempted and what was observed.

**Why it fails:**

- You will repeat dead ends — returning to approaches you already eliminated, because you do not remember that you tried them
- When asking for help, you cannot describe what you have already done, which means helpers must start from the beginning
- Knowledge gained during investigation is lost when the session ends

**What to do instead:** Keep a brief running note during significant debugging sessions — even a text file or a piece of paper. Record: what you tried, what the result was, and what it tells you. This need not be elaborate. Three sentences per attempt is enough.

---

## ◈ VIII. Excessive AI Dependency

**What it looks like:**
Using an AI tool as the first (and only) response to every error — without reading the error message first, without forming a hypothesis first, without attempting any investigation first.

**Why it fails:**

- AI tools cannot see your full codebase, your environment, or the runtime state — their responses are constrained by what you provide
- Applying AI suggestions without evaluating them produces code you do not understand and cannot maintain
- Debugging skill is built through the act of investigation. Bypassing the investigation bypasses the learning.
- AI tools can be wrong — they can produce plausible but incorrect suggestions, especially for context-specific problems

!!! note "AI as a collaborator, not a replacement"
    AI tools are most effective when used by programmers who already understand the problem space — who can evaluate whether a suggestion makes sense, identify when it is wrong, and ask precise follow-up questions. They are least effective as a substitute for the thinking that builds that understanding.

**What to do instead:** Complete the foundational checklist before consulting any AI tool. Bring a precise question — code, error, context, expected vs. actual — rather than delegating the diagnosis. Verify every suggested change before applying it.

---

## ◈ IX. Panic Changes

**What it looks like:**
Making rapid, undirected changes under time pressure — when a deadline is approaching, when a supervisor is watching, or when frustration reaches a peak.

**Why it fails:**

- Speed without direction does not produce faster results — it produces more bugs, more confusion, and more lost time
- Changes made in panic are less likely to be documented, less likely to be understood, and more likely to introduce new problems
- Panic compounds: each new problem introduced under pressure adds to the urgency and makes calm reasoning harder

**What to do instead:** Slow down deliberately. Time spent reading and reasoning before making a change is almost always recovered in the fix itself. A bug understood in ten minutes is fixed in two. A bug addressed in panic for an hour is often still present at the end of that hour — with additional problems added.

---

## ◆ X. Refusing to Ask for Help (Pride)

**What it looks like:**
Spending significantly more time than necessary on a problem that a brief conversation would resolve, because asking feels like an admission of inadequacy.

**Why it fails:**

- Time is wasted — time that could have been spent productively
- The learning opportunity is missed — a colleague or mentor who explains the solution also explains the reasoning, which builds understanding that solo investigation does not always provide
- The team is slowed — if your stuck, the work is stuck

!!! tip "Knowing when to ask is a professional skill"
    There is no virtue in struggling alone past the point of productivity. The standard is not *"never ask for help"* — it is *"ask well, after engaging seriously with the problem yourself."* Come with evidence. Come with a hypothesis. Come with what you have already tried. That is not weakness — it is preparation.

**What to do instead:** Set a time limit for solo investigation — thirty minutes to an hour for a problem that genuinely resists progress. If the limit is reached without a clear path forward, prepare a structured question and ask.

---

## ◎ Quick Reference — Anti-Patterns at a Glance

| Anti-Pattern | The Signal | The Redirect |
|--------------|-----------|--------------|
| Random changes | *"Let me try changing this and see..."* | Form a hypothesis first |
| Multiple changes | *"I'll just fix these three things..."* | One change. Then test. |
| Ignoring the error | *"I know what the problem is..."* | Read the full message first |
| Symptom fixing | *"The crash is gone, it's fixed"* | Why was the object null? |
| Skipping reproduction | *"I'll just fix the code..."* | Can you make it happen reliably? |
| Copy-paste without understanding | *"This Stack Overflow answer looks right..."* | Can you explain every line? |
| No documentation | *(tries the same approach for the third time)* | Keep a running note |
| AI dependency | *"I'll just ask Copilot..."* | Read the error first |
| Panic changes | *(rapid, undirected edits)* | Stop. Breathe. Read. |
| Pride | *(third hour on the same problem)* | Prepare a question and ask |

---

*← Back to: [Appendix C · Common Error Glossary](error_glossary.md)*

*Continue to: [Appendix E · Assessment Prompts →](assessment_prompts.md)*

*← Back to: [Appendices Overview](index.md)*
