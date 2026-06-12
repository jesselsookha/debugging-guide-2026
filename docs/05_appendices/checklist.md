# Appendix A · One-Page Debugging Checklist

---

## ◎ How to Use This Checklist

Work through this checklist **before making any change** and **before asking anyone for help** — human or AI.

It is organised in three levels. Start at the level that matches where you are. If you are stuck at the investigative level, confirm the foundational steps are already done — the cause of many investigation failures is a skipped foundational step.

!!! tip "Print this page"
    Keep a printed copy on your desk or workstation. The discipline of physically working through a checklist before reaching for a search engine or an AI tool is what separates systematic debugging from guesswork.

---

## ◉ ◈ ◆ Before Anything Else

These three steps apply at every level, every time.

- [ ] **STOP** — do not change any code yet
- [ ] **READ** the error message in full — every word of it
- [ ] **IDENTIFY** the error type: syntax / runtime / logical / semantic

If you cannot identify the error type, return to [Understanding Errors](../01_foundations/understanding_errors.md) before continuing.

---

## ◉ Foundational Checklist

*Use this when: you are new to debugging, or when the error is unfamiliar.*

**Locating the error:**

- [ ] What **file** does the error reference?
- [ ] What **line number** is reported?
- [ ] What **method or function** is named in the error?
- [ ] Have you navigated directly to that line — not scrolled — but clicked the link in the output panel?

**Understanding the error:**

- [ ] What does the error **message** say in plain language?
- [ ] Have you copied the exact error text — not paraphrased it?
- [ ] What was the program trying to do at the point it failed?

**Before making a change:**

- [ ] What did you **expect** to happen?
- [ ] What **actually** happened?
- [ ] What is **one specific change** you can make based on what you have read?
- [ ] Have you checked the line **before** the reported line as well? (Compilers report where they became confused — the mistake may be one line earlier)

**After making the change:**

- [ ] Run the program again. Is the original error gone?
- [ ] If a new error appeared — read it fully before doing anything else. Return to the top of this checklist.
- [ ] Can you explain **why** the fix worked?

!!! warning "Do not skip the last question"
    A fix you cannot explain is luck, not understanding. The next version of the same bug will find you unprepared. Take thirty seconds to articulate why the change resolved the error.

---

## ◈ Investigative Checklist

*Use this when: the error is indirect, the bug is not obvious, or the program runs but produces wrong output.*

**Confirm the foundational steps are done first.**

**Reproducing the bug:**

- [ ] Can you make the bug happen **every time**?
- [ ] Do you know the **exact inputs or conditions** that trigger it?
- [ ] Have you confirmed whether the bug occurs in **all contexts** or only specific ones (specific user, specific device, specific input value)?

**Reducing the problem:**

- [ ] Have you reduced the codebase to the **smallest section** where the bug still occurs?
- [ ] Have you tried **hard-coding** dynamic values to confirm whether the bug is in the logic or the data?
- [ ] Have you **commented out** half the suspect code to narrow the search (binary search approach)?

**Gathering evidence:**

- [ ] What does the **log output** show at the point of failure?
- [ ] Have you added **targeted log statements** to confirm execution flow and variable values?
- [ ] What is the **state of the key variables** at the point the error occurs?
- [ ] Is this the **root cause**, or is it a symptom of something that happened earlier?

**Forming a hypothesis:**

- [ ] What is your **specific hypothesis** about the cause? (Not "something is wrong" — a precise statement: "I believe X is null because Y was not initialised before Z was called")
- [ ] What **one change** would confirm or deny this hypothesis?
- [ ] Have you made only **one change at a time**?

**After the fix:**

- [ ] Can you explain **why it failed**?
- [ ] Can you explain **which assumption was wrong**?
- [ ] Can you explain **why the fix works**?
- [ ] Should this fix be accompanied by a **test** that would catch regression?

---

## ◆ Strategic Checklist

*Use this when: working in a team, in production systems, or on a fix that will affect other people's code.*

**Confirm the investigative checklist is complete first.**

**Team and communication:**

- [ ] Is this bug **already known** — has someone else reported or investigated it? (Check the issue tracker first)
- [ ] Who **owns** the code being changed? Have they been informed?
- [ ] Does your fix affect any **shared components** that other team members depend on?

**The fix itself:**

- [ ] Is your fix **small and focused** — one logical change, not a refactor?
- [ ] Does your fix address the **root cause** or only the symptom?
- [ ] Have you checked for **related locations** in the codebase where the same mistake might exist?
- [ ] Does your change maintain **existing behaviour** for all known inputs?

**Testing and verification:**

- [ ] Have you added or updated **unit tests** to cover the fixed behaviour?
- [ ] Have you added a **regression test** that would have caught this bug?
- [ ] Have you tested with **boundary inputs** — null, empty, maximum value, minimum value?
- [ ] Have you run the **full test suite** to confirm no regressions?

**Documentation:**

- [ ] Does your **commit message** describe the root cause and what was changed — not just "fixed bug"?
- [ ] Has the **issue tracker** been updated with the root cause, the fix, and how to verify?
- [ ] Should this be added to the **team knowledge base**?
- [ ] If this was a significant incident — does it warrant a **post-mortem**?

---

## ◎ The Help-Asking Checkpoint

Before asking anyone — a lecturer, a colleague, or an AI tool — confirm:

- [ ] I have read the full error message
- [ ] I know the error type
- [ ] I know the file and line number
- [ ] I have the relevant **code as text** (not a screenshot)
- [ ] I can state **expected vs. actual** behaviour precisely
- [ ] I have made at least **one deliberate attempt** at a fix
- [ ] I can describe **what I tried** and why it did not work

If all seven are true, you are ready to ask — and your question will receive a useful answer.

---

## ◎ The Quick Reference Card

```
┌──────────────────────────────────────────────────────────────────┐
│  DEBUGGING — QUICK REFERENCE                                     │
├──────────────────────────────────────────────────────────────────┤
│  STOP  →  READ  →  IDENTIFY  →  LOCATE  →  COMPARE  →  CHANGE   │
├──────────────────────────────────────────────────────────────────┤
│  ◉ FOUNDATIONAL:  File? Line? Message? Expected? One change?     │
│  ◈ INVESTIGATIVE: Reproduce? Reduce? Hypothesise? One change?    │
│  ◆ STRATEGIC:     Team aware? Tests updated? Fix documented?     │
├──────────────────────────────────────────────────────────────────┤
│  BEFORE ASKING:   Code (text) + Error + Context + Expected/Actual│
└──────────────────────────────────────────────────────────────────┘
```

---

*← Back to: [Appendices Overview](index.md)*

*Continue to: [Appendix B · Trace Tables →](trace_tables.md)*
