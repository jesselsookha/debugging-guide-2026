# Appendix B · Trace Tables

---

## ◎ What a Trace Table Is

A trace table is a paper-based technique for simulating the execution of a program step by step. You become the computer — reading each instruction in sequence, updating variable values as you go, evaluating conditions as they are encountered, and recording the state at every step.

It is the oldest form of debugging tool. It requires no software, no IDE, no device. It requires only the code, a piece of paper, and careful attention.

!!! quote "The trace table and the watch window"
    The Watch window in a modern IDE does exactly what a trace table does — it shows you the value of every variable at every step as the program executes. The difference is that the IDE does the execution. In a trace table, you do.

    Learning to build trace tables builds the mental skill that makes watch windows meaningful. A programmer who has never traced a loop on paper will not instinctively know which variable to watch in a debugger. One who has traced many loops knows exactly what to look for and where.

---

## ◎ I. Why Trace Tables Still Matter

In an era of powerful IDEs and interactive debuggers, trace tables might seem unnecessary. They are not.

**For beginners:** Trace tables teach mental execution — the ability to read code and visualise what the computer will do, step by step. This skill is foundational. Without it, a programmer cannot predict what their code will produce before running it, and cannot identify logical errors by reading alone.

**For all levels:** Complex loops, nested conditionals, and recursive algorithms are genuinely difficult to reason about in the abstract. A trace table makes the execution concrete and sequential — it externalises the reasoning that the mind struggles to hold all at once.

**In assessment contexts:** Many formal examinations and practical assessments require trace table completion on paper, where no IDE is available. The skill must be practised on paper to be reliable in that context.

**As preparation for using a debugger:** Building a trace table for a piece of code before running it in the debugger gives you a baseline — you know what the values *should* be at each step. When the debugger shows something different, you know exactly where to focus.

---

## ◎ II. How to Build a Trace Table

### The Structure

A trace table is a grid with:

- One column for the **step number** (sequential)
- One column for the **line number** being executed
- One column for each **variable** that changes during execution
- One column for each **condition** (loop test, if-statement) that affects flow
- An optional **output** column for anything printed or returned

### The Process

**Step I — Read the code once, completely.** Identify every variable. Identify every condition. Create your columns.

**Step II — Set all variables to their initial state.** If a variable is declared but not yet assigned, mark it as `?` or `undefined`.

**Step III — Execute line by line.** For each line:

- If it is an **assignment**, update the variable's column with the new value
- If it is a **condition**, evaluate it and record `true` or `false`
- If it is an **output statement**, record the value in the output column
- If it is a **loop**, trace each iteration as a separate row

**Step IV — Do not skip lines.** Every line executes in sequence. Include loop condition checks, including the final one that terminates the loop.

**Step V — When complete, read the output column.** Compare it with what you expected. Any discrepancy is the location of a logical error.

---

## ◎ III. Worked Example — A Simple Loop

### The Code

```
// Pseudocode
...
    total = 0
    i = 1
    while i <= 3
        total = total + i
        i = i + 1
    endwhile
    output total
...
```

### The Trace Table

| Step | Line | `total` | `i` | `i <= 3` | Output |
|------|------|---------|-----|----------|--------|
| 1 | 1 | 0 | ? | ? | — |
| 2 | 2 | 0 | 1 | ? | — |
| 3 | 3 (check) | 0 | 1 | true | — |
| 4 | 4 | 1 | 1 | true | — |
| 5 | 5 | 1 | 2 | true | — |
| 6 | 3 (check) | 1 | 2 | true | — |
| 7 | 4 | 3 | 2 | true | — |
| 8 | 5 | 3 | 3 | true | — |
| 9 | 3 (check) | 3 | 3 | true | — |
| 10 | 4 | 6 | 3 | true | — |
| 11 | 5 | 6 | 4 | true | — |
| 12 | 3 (check) | 6 | 4 | **false** | — |
| 13 | 7 | 6 | 4 | false | `6` |

**Reading the trace:** The loop runs three times (steps 3–5, 6–8, 9–11). On step 12, the condition `i <= 3` is false — `i` is now 4. The loop exits. The output is `6`, which is `1 + 2 + 3`. This is correct.

!!! note "Why include the condition check as its own row?"
    Every time a loop condition is evaluated — including the final evaluation that terminates the loop — it is a step. Including it makes clear how many times the condition was tested and when it first became false. Students who skip condition rows frequently miscount loop iterations.

---

## ◎ IV. Worked Example — A Conditional

### The Code

```java
// Java
int score = 72;
String grade;

if (score >= 80) {
    grade = "Distinction";
} else if (score >= 60) {
    grade = "Pass";
} else {
    grade = "Fail";
}

System.out.println(grade);
```

### The Trace Table

| Step | Line | `score` | `grade` | `score >= 80` | `score >= 60` | Output |
|------|------|---------|---------|---------------|---------------|--------|
| 1 | 1 | 72 | ? | ? | ? | — |
| 2 | 2 | 72 | ? | ? | ? | — |
| 3 | 4 (check) | 72 | ? | false | ? | — |
| 4 | 5 (check) | 72 | ? | false | true | — |
| 5 | 6 | 72 | "Pass" | false | true | — |
| 6 | 12 | 72 | "Pass" | false | true | `Pass` |

**Reading the trace:** `score >= 80` evaluates to false. `score >= 60` evaluates to true. The `else if` branch executes, assigning `"Pass"`. The output is `Pass`.

!!! tip "Only the branches that execute are traced"
    In a conditional structure, only the path actually taken is traced. The `else` block (lines 8–10) does not appear in this trace because it was not executed. This is correct — a trace table records what *happened*, not what *could have* happened.

---

## ◎ V. Worked Example — Finding a Logical Error

This is the most important use of a trace table in debugging: finding a bug in logic before, or instead of, running the code.

### The Code (with a bug)

```kotlin
// Kotlin — intended to find the maximum value in a list
fun findMax(numbers: List<Int>): Int {
    var max = 0               // Line 1
    for (n in numbers) {      // Line 2
        if (n > max) {        // Line 3
            max = n           // Line 4
        }
    }
    return max                // Line 5
}
```

**Input:** `numbers = [-5, -3, -8, -1]`

**Expected output:** `-1` (the largest value in the list)

### The Trace Table

| Step | Line | `max` | `n` | `n > max` | Return |
|------|------|-------|-----|-----------|--------|
| 1 | 1 | 0 | ? | ? | — |
| 2 | 2 (n = -5) | 0 | -5 | ? | — |
| 3 | 3 | 0 | -5 | false | — |
| 4 | 2 (n = -3) | 0 | -3 | ? | — |
| 5 | 3 | 0 | -3 | false | — |
| 6 | 2 (n = -8) | 0 | -8 | ? | — |
| 7 | 3 | 0 | -8 | false | — |
| 8 | 2 (n = -1) | 0 | -1 | ? | — |
| 9 | 3 | 0 | -1 | false | — |
| 10 | 5 | 0 | — | — | `0` |

**The trace reveals the bug:** The function returns `0` instead of `-1`. The condition `n > max` is never true because all values in the list are negative, and `max` was initialised to `0`. No value in the list is greater than `0`, so `max` never updates.

**The root cause:** Initialising `max` to `0` assumes the maximum is non-negative. For a list of all negative numbers, this assumption is wrong.

**The fix:** Initialise `max` to the first element of the list, or use `Int.MIN_VALUE`:

```kotlin
var max = numbers[0]     // correct: start with a real value from the list
// or
var max = Int.MIN_VALUE  // correct: smallest possible integer value
```

!!! success "What the trace table revealed"
    The trace table found a logical error without running the code. The bug was in the initialisation of `max`, not in the loop logic. A programmer who ran the code and saw `0` as the result might have suspected the loop — but the trace table shows that the loop is structurally correct. The error was set before the loop began.

---

## ◎ VI. Trace Tables for More Complex Code

### Nested Loops

For nested loops, trace the inner loop fully for each iteration of the outer loop. Use indentation or shading in your table to indicate which loop each row belongs to.

### Method Calls

When a method is called, note the call in the outer trace and then begin a new mini-trace for the called method with its own local variables. When the method returns, record the returned value in the outer trace and continue.

### Recursion

Trace recursive calls as nested traces — each recursive call begins a new section of the table with its own local scope. Record the return value of each call and carry it back to the level above.

!!! example "Trace table for a recursive method"
    For a recursive factorial function `factorial(3)`:

    | Call | `n` | `n <= 1` | Returns |
    |------|-----|----------|---------|
    | factorial(3) | 3 | false | 3 × factorial(2) |
    | factorial(2) | 2 | false | 2 × factorial(1) |
    | factorial(1) | 1 | true | 1 |
    | → factorial(2) resolves | | | 2 × 1 = 2 |
    | → factorial(3) resolves | | | 3 × 2 = 6 |

---

## ▣ VII. Trace Table Exercises

### Exercise 1 — Trace the Loop

Trace the following pseudocode for the input `n = 4`:

```
...
result = 1
i = 1
while i <= n 
    result = result * i
    i = i + 1
endwhile
output result
...
```

Create a trace table with columns: Step, Line, `result`, `i`, `i <= n`, Output.

??? question "Show completed trace and answer"
    | Step | Line | `result` | `i` | `i <= 4` | Output |
    |------|------|----------|-----|----------|--------|
    | 1 | 1 | 1 | ? | ? | — |
    | 2 | 2 | 1 | 1 | ? | — |
    | 3 | 3 (check) | 1 | 1 | true | — |
    | 4 | 4 | 1 | 1 | — | — |
    | 5 | 5 | 1 | 2 | — | — |
    | 6 | 3 (check) | 1 | 2 | true | — |
    | 7 | 4 | 2 | 2 | — | — |
    | 8 | 5 | 2 | 3 | — | — |
    | 9 | 3 (check) | 2 | 3 | true | — |
    | 10 | 4 | 6 | 3 | — | — |
    | 11 | 5 | 6 | 4 | — | — |
    | 12 | 3 (check) | 6 | 4 | true | — |
    | 13 | 4 | 24 | 4 | — | — |
    | 14 | 5 | 24 | 5 | — | — |
    | 15 | 3 (check) | 24 | 5 | false | — |
    | 16 | 7 | 24 | 5 | — | `24` |

    **Answer:** The output is `24`. This is `4!` (4 factorial = 4 × 3 × 2 × 1 = 24). The algorithm is a factorial calculator.

---

### Exercise 2 — Find the Bug

Trace the following Java code for `scores = [60, 85, 70]` and identify the logical error:

```java
int[] scores = {60, 85, 70};
int total = 0;

for (int i = 1; i <= scores.length; i++) {  // Line 4
    total = total + scores[i];               // Line 5
}

System.out.println("Total: " + total);
```

Create a trace table and identify both the logical error and the runtime error that will occur.

??? question "Show trace and analysis"
    | Step | Line | `total` | `i` | `i <= 3` | `scores[i]` | Output |
    |------|------|---------|-----|----------|-------------|--------|
    | 1 | 2 | 0 | ? | ? | — | — |
    | 2 | 4 (check) | 0 | 1 | true | — | — |
    | 3 | 5 | 85 | 1 | — | `scores[1]` = 85 | — |
    | 4 | 4 (check) | 85 | 2 | true | — | — |
    | 5 | 5 | 155 | 2 | — | `scores[2]` = 70 | — |
    | 6 | 4 (check) | 155 | 3 | true | — | — |
    | 7 | 5 | CRASH | 3 | — | `scores[3]` = ❌ | — |

    **Two errors found:**

    **I. Logical error:** The loop starts at `i = 1` (line 4), skipping `scores[0]` (value `60`). The first element is never added to `total`.

    **II. Runtime error:** When `i = 3`, `scores[3]` is accessed on an array of length 3 (valid indices: 0, 1, 2). This throws `ArrayIndexOutOfBoundsException`.

    **Fix:** Change `int i = 1` to `int i = 0` and `i <= scores.length` to `i < scores.length`.

---

### Exercise 3 — Write the Trace Table

Write (do not run) a trace table for the following Kotlin function when called with `x = 10`:

```kotlin
fun mystery(x: Int): Int {
    var result = 0
    var i = x
    while (i > 0) {
        result += i
        i -= 2
    }
    return result
}
```

What does this function compute? What is returned when `x = 10`?

??? question "Show trace and answer"
    | Step | `result` | `i` | `i > 0` | Return |
    |------|----------|-----|---------|--------|
    | Init | 0 | 10 | ? | — |
    | Check | 0 | 10 | true | — |
    | Body | 10 | 8 | — | — |
    | Check | 10 | 8 | true | — |
    | Body | 18 | 6 | — | — |
    | Check | 18 | 6 | true | — |
    | Body | 24 | 4 | — | — |
    | Check | 24 | 4 | true | — |
    | Body | 28 | 2 | — | — |
    | Check | 28 | 2 | true | — |
    | Body | 30 | 0 | — | — |
    | Check | 30 | 0 | false | — |
    | Return | 30 | 0 | — | `30` |

    **Answer:** The function sums all positive even numbers from `x` down to 2. For `x = 10`: `10 + 8 + 6 + 4 + 2 = 30`.

---

*← Back to: [Appendix A · Checklist](checklist.md)*

*Continue to: [Appendix C · Common Error Glossary →](error_glossary.md)*

*← Back to: [Appendices Overview](index.md)*

---

*[IDE]: Integrated Development Environment
