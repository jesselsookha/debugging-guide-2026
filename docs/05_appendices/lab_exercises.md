# Appendix F · Lab Exercises by Level

---

## ◎ About This Appendix

These exercises are hands-on practicals — designed to be completed with a development environment open, not just read. Each exercise provides a broken or incomplete program, a clear objective, and a set of tasks that build progressively.

The exercises are grouped by level. They are designed to be completed in order within a level, but each can also be used independently.

!!! note "For educators"
    Each exercise specifies the language used in the provided code. Where a course uses a different language, the exercise scenario and tasks remain valid — substitute the provided language example with equivalent broken code in the course language. The bugs described are universal.

---

## ◉ Foundational Labs

*Objective: Apply the six-step workflow to identify and fix syntax, runtime, and logical errors.*

---

### ◉ Lab F1 — The Broken Grade Calculator (Java)

**Objective:** Find and fix three bugs — one of each type: syntax, runtime, and logical.

**Setup:** Create a file `GradeCalculator.java` with the following code exactly as written — do not fix anything before you begin.

```java
public class GradeCalculator {

    public static void main(String[] args) {
        int[] scores = {72, 85, 91, 60, 78}
        double average = calculateAverage(scores);
        String grade = getGrade(average);
        System.out.println("Average: " + average);
        System.out.println("Grade: " + grade);
    }

    public static double calculateAverage(int[] scores) {
        int total = 0;
        for (int i = 0; i <= scores.length; i++) {
            total += scores[i];
        }
        return total / scores.length;
    }

    public static String getGrade(double average) {
        if (average >= 75) {
            return "Distinction";
        } else if (average >= 50) {
            return "Pass";
        } else {
            return "Fail";
        }
    }
}
```

**Tasks:**

I. Attempt to compile the program. Record the exact error message produced.

II. Identify the **syntax error**. What does the error message tell you? Fix it and recompile.

III. Run the program. A runtime error now occurs. Record the full error message including the stack trace. Apply the six-step workflow:
   - What type of error is this?
   - Which file and line?
   - What was the program trying to do?
   - What is your hypothesis?
   - Make one change. Test.

IV. After fixing the runtime error, run the program again. The output is:
   ```
   Average: 77.0
   Grade: Distinction
   ```
   Manually calculate the correct average: (72 + 85 + 91 + 60 + 78) ÷ 5. Is `77.0` correct? If not, find and fix the **logical error**.

V. For each of the three bugs, write one sentence explaining: the root cause, and what it tells you to watch for in future code.

??? question "Show hints (not answers)"
    **Bug I hint:** Java requires every statement to end with a specific punctuation mark. Look at line 4.

    **Bug II hint:** The loop condition uses `<=`. How many valid indices does an array of length 5 have? What is the last valid index?

    **Bug III hint:** `total` is an `int`. `scores.length` is an `int`. What does integer division do with the remainder?

---

### ◉ Lab F2 — Reading the Room (Multi-Language)

**Objective:** Practice reading error messages across three different languages without running any code.

For each error message below, answer these questions on paper before opening any IDE:

I. What type of error is this?
II. What file and line is reported?
III. What does the error description mean in plain language?
IV. What would you look at first?

**Error 1 — C#:**
```
Program.cs(14,28): error CS0019:
Operator '>' cannot be applied to operands of type 'string' and 'int'
```

**Error 2 — Kotlin:**
```
error: unresolved reference: calcuateTotal
        val result = calcuateTotal(items)
                     ^
```

**Error 3 — TypeScript:**
```
src/cart.ts:31:18 - error TS2339:
Property 'unitPrice' does not exist on type 'CartItem'.
  31     total += item.unitPrice * item.quantity;
```

**Error 4 — JavaScript (runtime):**
```
TypeError: items.forEach is not a function
    at processCart (cart.js:18:10)
    at checkout (checkout.js:42:5)
```

After completing your written analysis, verify your reading by looking up any error codes or types you were unsure about in [Appendix C](error_glossary.md).

---

### ◉ Lab F3 — Trace Before You Run (Pseudocode / Java)

**Objective:** Build a trace table for a piece of code, predict the output, then verify by running.

**Part A — Trace this pseudocode for `n = 6`:**

```
count = 0
n = 6
while n > 1 
    if n MOD 2 = 0 then
        n = n / 2
    else
        n = n * 3 + 1
    endif
    count = count + 1
endwhile
output count
```

Create a trace table with columns: Step, `n`, `n > 1`, `n MOD 2 = 0`, `count`, Output.

**Part B — Predict:** What is the final value of `count`?

**Part C — Verify:** Implement this in Java (or the language of your choice) and run it with `n = 6`. Does the output match your trace?

**Part D — Extend:** Try `n = 27`. Count the steps. This sequence is known as the Collatz conjecture — no matter what positive integer you start with, the sequence always reaches 1. The number of steps is called the stopping time. What is the stopping time for `n = 27`?

---

## ◈ Investigative Labs

*Objective: Reproduce bugs systematically, use logging and the debugger, read stack traces, and distinguish root causes from symptoms.*

---

### ◈ Lab I1 — The Transaction Processor (Java)

**Objective:** Reproduce an intermittent bug, add targeted logging, and find the root cause.

**Setup:** Create and run the following Java program:

```java
import java.util.Arrays;
import java.util.List;

public class TransactionProcessor {

    public static void main(String[] args) {
        List<Integer> transactions = Arrays.asList(100, 250, 75, 400, 130);
        processAll(transactions);
    }

    public static void processAll(List<Integer> transactions) {
        int i = 0;
        while (i <= transactions.size()) {   // (1)
            process(transactions.get(i));
            i++;
        }
    }

    public static void process(int amount) {
        System.out.println("Processing: £" + amount);
    }
}
```
1. Look carefully at the loop condition.

**Tasks:**

I. Run the program. Record the complete error message and stack trace.

II. Apply the investigative checklist: Can you reproduce this reliably? (Yes — it crashes every time.) What does the stack trace tell you about the crash site?

III. Add log statements **before making any fix**:
   - Log the value of `i` at the start of each iteration
   - Log the result of `transactions.size()` once before the loop
   - Log a message confirming when the loop condition is checked
   
   Run again with the logs. What do the logs reveal?

IV. Form a hypothesis. Write it as one specific sentence.

V. Make one change that addresses the root cause. Test. Confirm the fix works for all five transactions.

VI. Write a unit test (JUnit 5) that would have caught this bug:
   ```java
   @Test
   void processAllHandlesAllTransactions() {
       // Write this test
   }
   ```

---

### ◈ Lab I2 — The Debugger Walkthrough (Kotlin / Android Studio)

**Objective:** Use the Android Studio debugger — breakpoints, stepping, variable inspection, and watch expressions — to investigate a bug without using any print statements.

**Setup:** In an Android Studio project, create a utility class:

```kotlin
object ScoreCalculator {

    fun calculateBonus(scores: List<Int>, threshold: Int): Int {
        var bonus = 0
        for (score in scores) {
            if (score > threshold) {
                bonus += score - threshold
            }
        }
        return bonus
    }

    fun applyMultiplier(value: Int, multiplier: Double): Int {
        return (value * multiplier).toInt()
    }

    fun getFinalScore(scores: List<Int>, threshold: Int, multiplier: Double): Int {
        val bonus = calculateBonus(scores, threshold)
        val final = applyMultiplier(bonus, multiplier)
        return final
    }
}
```

Call it with: `ScoreCalculator.getFinalScore(listOf(80, 45, 92, 60, 71), 65, 1.5)`

Expected result: `bonus` = (80-65) + (92-65) + (71-65) = 15 + 27 + 6 = 48. `final` = (48 × 1.5).toInt() = 72.

**Tasks:**

I. Set a **line breakpoint** at the first line of `calculateBonus()`. Run in debug mode.

II. When execution pauses, use the **Variables panel** to confirm the initial values of `scores`, `threshold`, and `bonus`.

III. Add a **watch expression** for `bonus` and another for `score - threshold`. Step through the loop using Step Over, observing both watch expressions update with each iteration.

IV. Add a **conditional breakpoint** on the `if (score > threshold)` line that only fires when `score > 80`. Resume and confirm it pauses only for score `92`.

V. Step into `applyMultiplier()` when it is called. In the Variables panel, confirm the values of `value` and `multiplier`.

VI. Evaluate the expression `value * multiplier` in the **Evaluate Expression** dialog (`Alt+F8`) before stepping further. What is the result? What does `.toInt()` do to it?

VII. Write a brief note: what did the debugger reveal that print statements would have been slower to show?

---

### ◈ Lab I3 — The Missing Data (TypeScript)

**Objective:** Investigate a bug where the program produces wrong output with no error message — a pure logical error.

**Setup:** Create `studentReport.ts`:

```typescript
interface Student {
    name: string;
    scores: number[];
}

function getAverage(scores: number[]): number {
    let total = 0;
    for (let i = 0; i < scores.length; i++) {
        total + scores[i];   // (1)
    }
    return total / scores.length;
}

function generateReport(students: Student[]): void {
    for (const student of students) {
        const avg = getAverage(student.scores);
        console.log(`${student.name}: ${avg.toFixed(1)}`);
    }
}

const cohort: Student[] = [
    { name: "Jennifer", scores: [72, 88, 91] },
    { name: "Jason",   scores: [65, 70, 80] },
    { name: "Jessica", scores: [95, 98, 92] }
];

generateReport(cohort);
```
1. Read this line carefully.

**Expected output:**
```
Jennifer: 83.7
Jason: 71.7
Jessica: 95.0
```

**Actual output:**
```
Jennifer: 0.0
Jason: 0.0
Jessica: 0.0
```

**Tasks:**

I. No error is thrown. The program runs to completion. What type of error is this?

II. Before changing anything, add log statements inside `getAverage()` to trace:
   - The value of `total` before the loop
   - The value of `scores[i]` on each iteration
   - The value of `total` after each iteration
   - The value of `total` before the return

   What does the log output tell you?

III. Form a hypothesis. Write it as a specific sentence.

IV. Make one change. Test. Confirm the fix produces correct output.

V. Explain why TypeScript did not produce a warning or error for this bug. What setting or tool could be configured to catch this class of mistake in future?

---

## ◆ Strategic Labs

*Objective: Debug in team contexts, work with configuration and environment, write professional documentation, and apply preventive practices.*

---

### ◆ Lab S1 — The Environment Bug (Multi-Language)

**Objective:** Debug a bug that exists in the environment, not in the code — and document the investigation professionally.

**Setup:** Create the following Node.js / TypeScript application:

```typescript
// config.ts
export const config = {
    apiKey: process.env.PAYMENT_API_KEY,
    baseUrl: process.env.PAYMENT_BASE_URL,
    timeout: parseInt(process.env.PAYMENT_TIMEOUT_MS ?? '5000')
};

// paymentService.ts
import { config } from './config';

export async function processPayment(amount: number): Promise<boolean> {
    console.log('[PaymentService] Config:', {
        apiKeyPresent: !!config.apiKey,
        baseUrl: config.baseUrl,
        timeout: config.timeout
    });

    if (!config.apiKey || !config.baseUrl) {
        throw new Error('Payment service configuration missing');
    }

    // Simulated payment call
    console.log(`[PaymentService] Processing £${amount} via ${config.baseUrl}`);
    return true;
}
```

**Part A — Without a `.env` file:**

I. Run the application without setting any environment variables. Record the exact error produced.

II. Add startup validation to `config.ts` that checks for required variables and exits with a helpful message if any are absent:
   ```typescript
   // Write this validation
   ```

**Part B — With an incomplete `.env` file:**

Create a `.env` file that sets `PAYMENT_API_KEY` but not `PAYMENT_BASE_URL`. Run again.

III. Does your validation catch this? If not, update it.

**Part C — Documentation:**

IV. Write a `README` section titled "Environment Configuration" that documents:
   - All required environment variables
   - What each one does
   - The consequence of each being absent
   - An example `.env.example` file

V. Write a two-item post-mortem action items list for a team that shipped this code to production without the validation in place.

---

### ◆ Lab S2 — Test-First Bug Fix (Java / Kotlin)

**Objective:** Practice the test-first debugging approach — write a failing test that reproduces the bug, then fix the code until the test passes.

**The bug:** A `PasswordValidator` class is supposed to return `true` only for passwords that are at least 8 characters, contain at least one digit, and contain at least one uppercase letter. It currently returns `true` for some passwords that should be rejected.

```java
public class PasswordValidator {

    public boolean isValid(String password) {
        if (password.length() < 8) {
            return false;
        }

        boolean hasDigit = false;
        boolean hasUpper = false;

        for (char c : password.toCharArray()) {
            if (Character.isDigit(c)) hasDigit = true;
            if (Character.isUpperCase(c)) hasUpper = true;
        }

        return hasDigit || hasUpper;   // (1)
    }
}
```
1. Read this return statement carefully.

**Tasks:**

I. Before running or changing anything, write four unit tests:
   - One that should pass (a valid password)
   - One that should fail for insufficient length
   - One that should fail for no digit
   - One that should fail for no uppercase letter

   Run the tests. Which ones pass and which fail? Does this match your expectation?

II. Identify the bug from the test results without further investigation. Write your hypothesis in one sentence.

III. Make one change to fix the bug. Re-run all four tests. All should now pass.

IV. Add two more tests for edge cases:
   - A password that is exactly 8 characters and meets all requirements (boundary case)
   - An empty string (defensive case)

V. Reflect: how did writing the tests first change your approach compared to reading the code and guessing?

---

### ◆ Lab S3 — The Team Debugging Simulation

**Objective:** Experience the full professional debugging workflow — from user report through investigation, fix, and documentation — in a simulated team context.

**Scenario:** You are a junior developer on a team maintaining a student management web application. On Monday morning, your team receives the following report from a lecturer:

> *"When I try to view a student's full academic record by clicking their name in the class list, I sometimes get a blank page instead of their details. It works for most students but not all. It's been happening since Friday's update."*

**Available resources:**

The Friday deployment changed the following:

```typescript
// Before Friday — worked correctly
async function getStudentRecord(studentId: string): Promise<Student> {
    const student = await studentRepository.findById(studentId);
    return student;
}

// After Friday — current version
async function getStudentRecord(studentId: string): Promise<Student> {
    const student = await studentRepository.findById(studentId);
    const enriched = await enrichmentService.addAcademicHistory(student);
    return enriched;
}
```

The `enrichmentService.addAcademicHistory()` method was added on Friday and calls an external academic records API.

**Tasks:**

I. **Triage:** Before touching any code, list the five questions you would ask to better understand the bug — questions that would help you reproduce it and narrow the cause.

II. **Hypothesise:** Based on the change made on Friday and the symptom (blank page for some students), write two plausible hypotheses for the root cause.

III. **Investigate:** Write the log statements you would add to `getStudentRecord()` to test your hypotheses. For each log, state what you are trying to confirm.

IV. **Document:** Write a formal bug report using the seven-component structure, based on what you know so far. Where you do not have information (e.g., the confirmed root cause), note it as "under investigation."

V. **Prevent:** After the bug is fixed, write two action items for the team's post-mortem that would prevent a similar issue in future deployments.

VI. **Reflect:** What aspects of this scenario would have been different if you were debugging alone, compared to debugging in a team context?

---

## ◎ Submission and Documentation Template

For each lab exercise completed, document your work using this structure. This template can be used for formal submission or personal learning records.

```
LAB EXERCISE: [Lab number and name]
DATE: [Date completed]
LANGUAGE / ENVIRONMENT: [Language and IDE used]

BUG I — [Type: syntax / runtime / logical / semantic]
  Observed: [What you saw — the error message or wrong output]
  Location: [File and line if applicable]
  Root cause: [Why it occurred]
  Fix applied: [The change made]
  Why it works: [Explanation of the fix]

BUG II — [if applicable, same structure]

WHAT I LEARNED:
  [One thing this exercise revealed about your debugging approach]
  [One thing you will do differently in future code or debugging sessions]
```

---

*← Back to: [Appendix E · Assessment Prompts](assessment_prompts.md)*

*Continue to: [Appendix H · Acknowledgments & References →](references.md)*

*← Back to: [Appendices Overview](index.md)*

---

*[IDE]: Integrated Development Environment
*[TDD]: Test-Driven Development
*[API]: Application Programming Interface
