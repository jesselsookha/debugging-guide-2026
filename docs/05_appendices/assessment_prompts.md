# Appendix E · Assessment Prompts

---

## ◎ About This Appendix

This appendix contains structured assessment prompts for each level of debugging competence. They are intended for use in three contexts:

**For educators:** These prompts can be used as formal assessment tasks, practical examination questions, oral assessment prompts, or reflective journal entries. Each prompt is framed to assess reasoning and process — not just whether the correct answer was reached.

**For students in self-study:** Use these prompts to test your own understanding before formal assessment, or as reflection exercises at the end of each section of this guide.

**For junior developers:** Use the investigative and strategic prompts as a self-assessment of your current professional debugging practice.

!!! note "What these prompts assess"
    Debugging is a reasoning process, not a recall task. The prompts here are designed to assess *how* a programmer thinks about a problem — the evidence they gather, the hypotheses they form, the steps they take, and the explanations they produce. A correct answer with no reasoning demonstrated is not a complete response.

---

## ◎ Bloom's Taxonomy Alignment

The prompts are designed at multiple cognitive levels. Where relevant, the level is indicated.

| Level | Description | Prompt Type |
|-------|-------------|-------------|
| **Remember** | Recall terminology and definitions | Define, identify, name |
| **Understand** | Explain in own words | Describe, explain, classify |
| **Apply** | Use a process on a new problem | Trace, fix, write, follow |
| **Analyse** | Break down and examine components | Compare, distinguish, investigate |
| **Evaluate** | Judge the quality or appropriateness of an approach | Assess, justify, critique |
| **Create** | Produce something new | Design, write, construct |

The most valuable debugging prompts operate at **Apply**, **Analyse**, and **Evaluate** — not at Remember. A student who can define a `NullPointerException` but cannot explain what to do when they encounter one has not demonstrated debugging competence.

---

## ◉ I. Foundational Prompts

*Appropriate for: high school (Grade 10–12), first-year undergraduate students, and any programmer building foundational habits.*

---

### ◉ F1 — Error Classification (Apply)

The following error messages were produced by four different programs. For each one:

I. Identify the error type (syntax, runtime, logical, or semantic)
II. State the file and line number where the problem was detected
III. Write one sentence describing the most likely cause

**Error A:**
```
Main.java:7: error: ';' expected
        int total = 0
                     ^
```

**Error B:**
```
Exception in thread "main"
java.lang.ArrayIndexOutOfBoundsException: Index 4 out of bounds for length 4
    at com.example.Scores.getScore(Scores.java:22)
    at com.example.Main.main(Main.java:11)
```

**Error C:**
A program calculates the area of a rectangle. When given width = 5 and height = 4, it returns `18` instead of `20`.

**Error D:**
```java
String input = getUserInput();
if (input == "yes") {
    confirmAction();
}
```
The method `confirmAction()` is never called, even when the user types "yes".

---

### ◉ F2 — Reading an Error Message (Understand / Apply)

A student runs their Java program and sees the following output:

```
Exception in thread "main"
java.lang.NullPointerException: Cannot invoke "String.toUpperCase()"
because "username" is null
    at com.example.app.UserService.formatUsername(UserService.java:34)
    at com.example.app.Main.main(Main.java:12)
```

Answer the following questions:

I. What type of error is this?
II. What operation failed, and on which variable?
III. In which file and at which line did the crash occur?
IV. Which method called the method that crashed, and at which line?
V. What is one likely reason the variable `username` is null at this point?
VI. What would you check first in `UserService.java` line 34?

---

### ◉ F3 — Structured Help Request (Apply / Create)

A student has the following bug and wants to ask their lecturer for help:

```kotlin
fun main() {
    val numbers = listOf(10, 20, 30)
    var sum = 0
    for (i in 1..numbers.size) {
        sum += numbers[i]
    }
    println("Sum: $sum")
}
```

The program crashes with:
```
Exception in thread "main" java.lang.IndexOutOfBoundsException:
Index 3 out of bounds for length 3
```

Write a structured help request that the student should send — including all four required components: the code, the error, the context, and the expected vs. actual behaviour. Then identify the bug yourself and explain the fix.

---

### ◉ F4 — Trace Table (Apply)

Trace the following pseudocode for the input `n = 5` and complete a trace table with columns for Step, Line, `n`, `total`, `n > 0`, and Output.

```
total = 0
while n > 0 
    total = total + n
    n = n - 1
endwhile
output total
```

I. What is the final value of `total`?
II. How many times does the loop body execute?
III. What is the value of `n` when the loop terminates?

---

### ◉ F5 — Debugging Workflow (Apply / Evaluate)

A student encounters the following runtime error and immediately begins changing code at random. After twenty minutes, the error is different but still present.

Describe the **six-step debugging workflow** that the student should have followed from the moment the error first appeared. For each step, describe what specific action the student should have taken for this scenario.

```
Exception in thread "main"
java.lang.NullPointerException: Cannot invoke "String.length()" because
"str" is null
    at com.example.Validator.checkLength(Validator.java:18)
    at com.example.Main.processInput(Main.java:31)
    at com.example.Main.main(Main.java:9)
```

---

## ◈ II. Investigative Prompts

*Appropriate for: second and third-year undergraduate students, and programmers comfortable with basic debugging who are developing investigative discipline.*

---

### ◈ I1 — Reproducing an Intermittent Bug (Analyse / Evaluate)

A user reports: *"Sometimes when I submit the order form, nothing happens. But when I try again, it works."*

I. What information would you collect from the user to begin making the bug reproducible?
II. List four specific conditions or variables that intermittent bugs commonly depend on.
III. Describe the logging strategy you would implement to capture the state of the system at the moment the failure occurs — without being able to reproduce it interactively.
IV. At what point would you consider the bug "reliably reproduced"? What does that confirmation look like?

---

### ◈ I2 — Stack Trace Analysis (Apply / Analyse)

Examine the following stack trace from a Kotlin Android application:

```
FATAL EXCEPTION: main
Process: com.example.inventoryapp, PID: 18833
java.lang.NullPointerException: Attempt to invoke virtual method
'java.lang.String com.example.inventoryapp.model.Product.getName()'
on a null object reference
    at com.example.inventoryapp.ui.ProductListAdapter.onBindViewHolder(ProductListAdapter.kt:67)
    at androidx.recyclerview.widget.RecyclerView$Adapter.onBindViewHolder(RecyclerView.java:723)
    at com.example.inventoryapp.ui.InventoryFragment.setupRecyclerView(InventoryFragment.kt:44)
    at com.example.inventoryapp.ui.InventoryFragment.onViewCreated(InventoryFragment.kt:28)
```

Answer the following:

I. What exception type was thrown, and what does it mean in plain language?
II. Which line of **your code** is closest to the crash site?
III. Which framework/library lines can be ignored, and why?
IV. Describe the call chain from `onViewCreated()` to the crash site — in your own words.
V. The crash says `getName()` was called on a null `Product`. At which point in the call chain did a null `Product` enter the system? Where would you look to find out?
VI. Write a hypothesis for the root cause in one specific sentence.

---

### ◈ I3 — Hypothesis Formation (Analyse / Create)

A TypeScript function is supposed to return the most expensive item in a shopping cart. For most inputs it works correctly, but when two items have the same highest price, it always returns the wrong one.

```typescript
function getMostExpensive(items: CartItem[]): CartItem {
    let max = items[0];
    for (let i = 0; i < items.length; i++) {
        if (items[i].price > max.price) {
            max = items[i];
        }
    }
    return max;
}
```

I. Describe the specific input condition that triggers the bug.
II. Write three distinct hypotheses about what might be wrong.
III. For each hypothesis, describe the one change you would make to test it.
IV. Which hypothesis is most likely correct, based on reading the code? Explain your reasoning.
V. Write the corrected function and explain why the fix resolves the issue.

---

### ◈ I4 — Strategic Logging (Apply / Create)

The following C# method is supposed to apply discounts to an order. The discount is sometimes applied correctly and sometimes not — with no obvious pattern.

```csharp
public decimal ApplyDiscount(Order order, string promoCode)
{
    var promo = promoRepository.GetByCode(promoCode);

    if (promo != null && promo.IsActive && order.Total >= promo.MinimumOrderValue)
    {
        return order.Total * (1 - promo.DiscountRate);
    }

    return order.Total;
}
```

I. Write the log statements you would add to investigate why the discount is sometimes not applied. For each log statement, state the specific question it answers.
II. Describe what each possible log output would tell you about the cause.
III. If `promo` is found to be `null`, what would your next investigative step be?

---

### ◈ I5 — Root Cause vs. Symptom (Evaluate / Create)

A developer fixes a `NullPointerException` in a profile display method by adding a null check:

```java
// Before fix
public void displayProfile(User user) {
    nameLabel.setText(user.getName());
}

// After fix
public void displayProfile(User user) {
    if (user != null) {
        nameLabel.setText(user.getName());
    }
}
```

I. Is this a root cause fix or a symptom fix? Justify your answer.
II. What questions remain unanswered after this fix?
III. Describe the investigation you would conduct to find and fix the actual root cause.
IV. What risk does this "fix" introduce?

---

## ◆ III. Strategic Prompts

*Appropriate for: final-year undergraduate students, students beginning workplace experience, and junior developers working in team environments.*

---

### ◆ S1 — Production Bug Response (Evaluate / Create)

At 14:30 on a Tuesday, your team receives a report that the checkout process on your e-commerce application is failing for approximately 30% of users. Users report that clicking "Confirm Order" returns a 500 error.

I. Describe your immediate response — the first five actions you take in order.
II. What information do you gather before touching any code?
III. How do you communicate with your team and with any affected stakeholders during the investigation?
IV. Write the opening paragraph of the incident report you would file within two hours of resolution.
V. What preventive measures might have caught this bug before it reached production?

---

### ◆ S2 — Bug Report and Commit Message (Apply / Create)

You have investigated and resolved the following bug: In your Android application, when a user navigates to the Settings screen, changes their notification preferences, and presses Back, the Home screen still shows the old notification badge count. After investigation, you found that the `NotificationBadgeView` observes a `LiveData` object in `HomeViewModel`, but the `HomeViewModel` caches the badge count from app startup and does not observe the `NotificationRepository` for changes. The fix was to replace the cached integer with a `LiveData` derived directly from `NotificationRepository`.

Write:

I. A professional bug report using the standard seven-component structure
II. A git commit message that follows the convention: `fix: [short description]` followed by a body explaining the root cause and what was changed
III. An issue tracker update (two to three sentences) that closes the issue and links the commit

---

### ◆ S3 — Introducing a Debugging Practice to a Team (Evaluate / Create)

You join a small development team that does not currently write unit tests. During your first week, you observe two separate bugs that would have been caught by tests. You want to propose introducing unit testing to the team.

I. What arguments would you make for unit testing, specifically framed around the bugs you observed?
II. What resistance might you anticipate, and how would you address each concern?
III. Describe a gradual introduction strategy — how would you introduce unit testing without disrupting current workflow or requiring the team to write tests for all existing code immediately?
IV. How would you measure whether the introduction was successful after three months?

---

### ◆ S4 — Post-Mortem (Create)

Write a complete post-mortem for the following incident:

*A mobile banking application deployed an update that included a change to the interest calculation method for savings accounts. The change introduced an off-by-one error in the day-counting logic. As a result, interest was calculated incorrectly for 1,847 accounts over a period of eleven days before the error was detected during a routine audit. The total financial discrepancy was £3,240 — some accounts were overpaid and some underpaid. The fix required a hotfix deployment, a database correction script, and individual customer communications for affected accounts.*

Your post-mortem should include all seven components: summary, timeline (you may construct a plausible timeline), root cause, contributing factors, impact, resolution, and action items.

---

### ◆ S5 — Debugging Skill Self-Assessment (Evaluate)

Using the five categories of debugging knowledge from [Section 4.4](../04_tools_techniques/developing_skills.md) — System/Codebase, System/Domain, Tool/Procedural, Strategic, and Experiential — write a self-assessment of your current debugging competence.

For each category:

I. Rate your current level (beginning / developing / competent / proficient)
II. Provide one specific example of a situation where this knowledge helped you debug effectively — or where its absence slowed you down
III. Identify one specific action you will take in the next month to improve in this category

---

## ◎ Educator Notes

!!! info "Suggested use by year level"

    **High school (Grade 10–12):** F1, F2, F3, F4 are appropriate as practical assessment tasks or examination questions. F5 works well as a reflective writing task.

    **First-year undergraduate:** All foundational prompts (F1–F5), plus I1 and I2 as stretch tasks for stronger students.

    **Second-year undergraduate:** I1 through I5 as primary assessment material. F4 (trace tables) remains relevant as a foundational skill check.

    **Third-year and capstone:** S1 through S4 as project-based or portfolio assessment. S5 as a reflective component of a final project submission.

    **Junior developer self-assessment:** S5 is the most directly useful entry point. S1 and S4 reflect the professional realities of team debugging and incident management.

!!! tip "Oral assessment"
    Several of these prompts work particularly well as oral assessments — especially F5, I2, I5, and S1. Asking a student to explain their reasoning aloud while working through a stack trace or describing their response to a production incident reveals depth of understanding that written responses sometimes obscure.

---

*← Back to: [Appendix D · Debugging Anti-Patterns](anti_patterns.md)*

*Continue to: [Appendix F · Lab Exercises by Level →](lab_exercises.md)*

*← Back to: [Appendices Overview](index.md)*

---

*[TDD]: Test-Driven Development
*[IDE]: Integrated Development Environment
*[API]: Application Programming Interface
