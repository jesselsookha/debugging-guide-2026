# ◆ Professional Debugging Practices

---

## ◆ Debugging as a Professional Activity

In a solo or academic context, debugging is largely a private activity. You find the bug, fix it, and move on. Nobody needs to know exactly what the problem was, how long it took, or what you tried.

In a professional context, this changes completely.

Bugs in a team environment affect other people's work. A bug in a shared library may block three colleagues. A bug in a production system may affect real users. A fix that is not communicated may be accidentally reverted by the next person to touch the code. A root cause that is not documented will be encountered again by the next person, or by you six months from now.

Professional debugging practices exist to make debugging work **visible, communicable, reproducible, and preventable** — not just resolved.

---

## ◆ I. Debugging with Version Control

Version control is not just a backup system. It is a debugging tool.

### Finding When a Bug Was Introduced

The most powerful version control debugging technique is `git bisect` — a binary search through commit history to find the exact commit that introduced a bug.

**How it works:**

I. You know the bug exists now, and that it did not exist at some earlier point in history.

II. You tell git which commit is known-good and which is known-bad.

III. Git checks out the midpoint commit. You test whether the bug is present.

IV. You tell git whether this commit is good or bad. Git checks out the new midpoint.

V. Repeat until git identifies the exact commit that introduced the bug.

```bash
# Start bisect
git bisect start

# Tell git the current state is broken
git bisect bad

# Tell git a known-good commit (a tag, branch, or hash)
git bisect good v1.4.0

# Git checks out a midpoint commit
# Test your application — does the bug exist?

# If the bug is present at this commit:
git bisect bad

# If the bug is NOT present at this commit:
git bisect good

# Git narrows down and eventually reports:
# "abc1234 is the first bad commit"

# When done, return to the original branch
git bisect reset
```

!!! tip "Automate bisect with a test"
    If you can write a script or test that exits with code 0 when the bug is absent and non-zero when it is present, git bisect can run entirely automatically:

    ```bash
    git bisect start
    git bisect bad HEAD
    git bisect good v1.4.0
    git bisect run ./test_for_bug.sh
    ```

    Git runs through the binary search without manual intervention and reports the offending commit.

### Other Version Control Debugging Techniques

**`git log --all --grep="keyword"`** — search commit messages for references to a specific feature, fix, or file. Useful for finding when a specific change was made.

**`git blame filename`** — shows which commit last modified each line of a file. When you find a line that looks wrong, `git blame` tells you who changed it and when. That commit message often explains why.

**`git diff commit1..commit2`** — compares two commits or branches. When a bug exists in one branch but not another, diffing the branches reveals exactly what is different.

**`git log -p -- path/to/file`** — shows the full history of changes to a specific file, with diffs. Useful when a file's behaviour has changed but you are not sure when or why.

---

## ◆ II. Writing Effective Bug Reports

A bug report is a piece of technical communication. Its purpose is to give another person — a colleague, a reviewer, or your future self — enough information to understand the bug, reproduce it, and evaluate whether a fix is complete.

A bug report that cannot be reproduced from its description is not a useful bug report.

### The Components of a Good Bug Report

**I. Title**
A specific, searchable summary. Not *"app crashes"* but *"NullPointerException in ProductDetailFragment when navigating back from cart screen on Android 12"*.

**II. Environment**
- Platform, OS version, device or browser
- Application version or commit hash
- Any relevant configuration

**III. Steps to Reproduce**
The exact sequence of actions that produces the bug, numbered. Another developer should be able to follow these steps on a fresh installation and see the same behaviour.

**IV. Expected Behaviour**
What should happen.

**V. Actual Behaviour**
What actually happens. Include the exact error message, crash output, or wrong value.

**VI. Evidence**
Stack traces, log output, screenshots (of results, not of code), network request/response logs where relevant.

**VII. Severity and Impact**
How serious is this? Does it block core functionality? Affect all users or only some?

!!! example "Bug report — poor vs. effective"

    !!! failure "Poor bug report"
        **Title:** Cart is broken

        **Description:** When I try to buy something the app crashes

    !!! success "Effective bug report"
        **Title:** App crashes with NullPointerException when removing last item from cart on Android 13

        **Environment:** Android 13, Pixel 6 emulator, app version 2.3.1 (build 847)

        **Steps to reproduce:**
        1. Add a single item to the cart
        2. Open the cart screen
        3. Remove the item using the swipe gesture
        4. App crashes immediately

        **Expected:** Cart screen shows "Your cart is empty" message

        **Actual:** App crashes with the following exception:

        ```
        java.lang.NullPointerException: Attempt to invoke virtual method
        'int java.util.List.size()' on a null object reference
            at com.example.shop.CartFragment.updateEmptyState(CartFragment.kt:92)
            at com.example.shop.CartFragment.onItemRemoved(CartFragment.kt:67)
        ```

        **Severity:** High — crashes the app for all users who remove their last cart item

---

## ◆ III. Writing Post-Mortems

A post-mortem (also called a root cause analysis or incident report) is a document written after a significant bug has been resolved — especially one that affected users, caused data loss, or blocked a team.

Its purpose is not to assign blame. Its purpose is to **capture what happened, why it happened, and what will prevent it from happening again**.

### Structure of a Post-Mortem

**I. Summary**
One paragraph: what happened, when, who was affected, how it was resolved.

**II. Timeline**
A chronological log of events: when the bug was first observed, when investigation began, what was tried, when the fix was deployed.

**III. Root Cause**
A precise technical explanation of why the failure occurred. Not the symptom — the underlying cause.

**IV. Contributing Factors**
What conditions made this bug possible? Missing tests? An undocumented API behaviour? A configuration difference between environments?

**V. Impact**
Quantify the effect where possible: number of users affected, duration of the issue, data affected.

**VI. Resolution**
What fix was applied, and why it addresses the root cause.

**VII. Action Items**
Specific, assigned, trackable tasks to prevent recurrence. Not vague commitments — concrete changes: *"Add a unit test for CartFragment when the last item is removed"*, *"Document the assumption that cartItems will never be null after initialisation"*.

!!! note "Post-mortems are a learning practice, not a blame exercise"
    In professional engineering teams, post-mortems are treated as opportunities for systemic improvement. The goal is to identify how the process, design, or tooling failed — not which individual made a mistake. A culture that punishes mistakes produces teams that hide them. A culture that learns from them produces teams that improve.

---

## ◆ IV. Debugging in Teams

When debugging in a team context, the work changes character. You may be investigating code you did not write. You may need to communicate your findings to someone who was not present during the investigation. You may need to decide — as a team — which bugs to prioritise.

### Pair Debugging

Pair debugging is the deliberate practice of investigating a bug with a second person present — one person driving (navigating the code, running the debugger) and one person observing and asking questions.

The observer's role is crucial: they ask *why* questions. *"Why do you expect that variable to be non-null there?"* *"When is that method called exactly?"* The discipline of answering these questions aloud frequently surfaces the assumption that contains the bug — which is the same mechanism as rubber duck debugging, but with a human who can push back.

!!! tip "Ask for a second pair of eyes earlier, not later"
    Many programmers wait until they are completely stuck before asking for help. In a team context, a five-minute explanation of a problem to a colleague is often faster than two more hours of solo investigation. The act of preparing to explain the problem is itself valuable — it forces you to structure what you know and what you do not.

### Code Reviews as Debugging

A code review is not only a quality gate — it is a preventive debugging activity. A reviewer who reads code carefully before it is merged is performing the same reasoning as a debugger: asking whether the code does what it claims to do, whether edge cases are handled, and whether the assumptions embedded in the code are correct.

Effective code review from a debugging perspective asks:

- What happens when the input is null, empty, or at the boundary of a valid range?
- What happens if this network call fails?
- What happens if this method is called twice?
- Is this assumption documented? Should it be enforced with an assertion?

### Knowledge Bases and Internal Documentation

When a significant bug is resolved, the knowledge gained should not remain only in the memory of the person who fixed it. A brief entry in a team knowledge base — a wiki page, a Confluence article, a well-written commit message, or a comment in the code — preserves that knowledge for the next person who encounters the same issue.

This is not a new practice. Programmers in the 1980s kept printed binders of known issues and their solutions.[^1] StackOverflow became indispensable because programmers asked questions publicly and the answers accumulated into a searchable archive.[^2] The principle has not changed — only the medium.

[^1]: Discussed in the Brief History of Debugging — see [00 · Introduction](../00_introduction/brief_history.md).
[^2]: Stack Overflow launched in 2008 and rapidly became the primary community resource for programming questions. See https://stackoverflow.com

**What to document after a significant bug:**

- The symptoms that were observed
- The root cause (not just the fix)
- The fix that was applied and why it works
- Any conditions or inputs that trigger the issue
- Related issues or code areas to watch

!!! tip "Write documentation immediately after fixing"
    The knowledge of a bug is freshest immediately after it is resolved. A ten-minute documentation effort immediately after a fix preserves context that will be impossible to reconstruct six weeks later.

---

## ◆ V. Using Version Control for Safe Debugging

Beyond `git bisect`, good version control habits make debugging faster and safer:

**Work on a branch.** When investigating a significant bug, create a branch (`git checkout -b fix/issue-description`). This allows you to make exploratory changes, add temporary debug logs, and commit intermediate states without affecting the main codebase. When the fix is confirmed, the branch is merged cleanly.

**Commit frequently with meaningful messages.** Small commits with clear messages make it possible to identify which change introduced a regression. A commit that contains thirty unrelated changes is nearly impossible to bisect usefully.

**Use `git stash` for temporary changes.** If you need to switch context mid-investigation, `git stash` saves your uncommitted changes and restores a clean working directory. `git stash pop` restores them when you return.

**Revert, do not comment out.** If a change made a bug worse, `git revert` produces a clean reversal rather than leaving commented-out code in the file.

---

*Continue to: [Preventive Measures →](preventive_measures.md)*

*← Back to: [Section Index](index.md)*

---

*[TDD]: Test-Driven Development
*[IDE]: Integrated Development Environment
*[API]: Application Programming Interface
