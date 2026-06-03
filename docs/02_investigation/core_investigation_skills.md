# ◈ Core Investigation Skills

---

## ◈ The Skills That Make Investigation Possible

The investigative mindset described in the previous page is the philosophical foundation. This page covers the practical skills that make it operational. These are the techniques that experienced programmers use daily — not because they are complicated, but because they are reliable.

Each skill builds on the last. Together they form a complete investigative toolkit that works across languages, frameworks, and environments.

---

## ◈ I. Reproducing Bugs Reliably

The first and most important skill in investigative debugging is the ability to make a bug happen on demand.

> **A bug you can reproduce is already half solved.**

If you cannot reliably reproduce a bug, you cannot:

- Verify that a fix actually resolves it
- Reason about what conditions trigger it
- Test whether the same problem exists in a different context
- Show the bug to someone else for help

**Making reproduction reliable:**

Start by documenting the exact steps that trigger the failure. Not a general description — the precise sequence of actions, inputs, and conditions. Ask:

- Does this happen every time, or only sometimes?
- Does it depend on specific input values?
- Does the sequence of actions matter?
- Does it happen on all devices or only some?
- Does it depend on application state (e.g., a user being logged in)?

!!! warning "Intermittent bugs — a special case"
    If a bug only occurs sometimes, your first goal is to make it consistent. Intermittent bugs are almost always caused by timing issues, uninitialised state, or race conditions. Add logging to capture what is different about the runs where the bug occurs versus the runs where it does not. The difference is the clue.

**Minimal reproduction:**

Once you can reproduce the bug reliably, try to reduce the reproduction to the smallest possible case. The Stanford CS107 debugging guide[^1] puts it well: you want *the smallest, simplest test case possible that still reproduces the bug*. The smaller the case, the fewer places the bug can hide, and the faster the investigation proceeds.

[^1]: Stanford CS107 Debugging Guide — https://web.stanford.edu/class/cs107/resources/debugging.html

---

## ◈ II. Reducing the Problem Space

Large codebases overwhelm reasoning. Investigating a bug across an entire application is like searching for a needle in a haystack by examining each piece of hay individually. Reduction is the technique of making the haystack smaller before the search begins.

### Binary Search for Bugs

The most systematic approach to reduction is binary search: divide the code in half, determine which half contains the problem, discard the other half, and repeat.

In practice this looks like:

I. Identify the range of code that could contain the bug (between program start and the crash, or between correct behaviour and incorrect behaviour)

II. Disable or comment out the code in the second half of that range

III. Test — does the bug still occur?

IV. If yes: the bug is in the first half. Narrow your search there.

V. If no: the bug is in the second half (the part you disabled). Re-enable it.

VI. Repeat until the problematic section is small enough to inspect directly.

=== "Java — binary search approach"
    ```java
    public void processOrder(Order order) {
        validateOrder(order);        // Does the bug appear after this?
        applyDiscounts(order);       // Comment this out to test
        calculateTax(order);         // Comment this out to test
        updateInventory(order);      // Comment this out to test
        generateInvoice(order);      // Comment this out to test
    }
    ```
    Start by commenting out the bottom half. If the bug disappears, it was in the removed code. If it persists, it is in the remaining code. Narrow down progressively.

=== "Kotlin — binary search approach"
    ```kotlin
    fun setupScreen() {
        initViews()           // Test 1: comment everything below this
        loadUserData()        // Test 2: comment everything below this
        applyTheme()          // Test 3: comment everything below this
        bindListeners()       // Test 4: comment everything below this
        refreshDisplay()
    }
    ```
    Each commented-out block removes a candidate. The block whose removal makes the bug disappear is the location to investigate.

### Other Reduction Techniques

**Hard-code dynamic values:** Replace values that come from user input, network calls, or databases with fixed constants. If the bug disappears, the problem is in how those values are being generated or passed. If it persists, the problem is in the logic that uses them.

**Remove UI layers:** If a bug occurs in the presentation layer but may originate in the data or business logic layer, test the underlying logic in isolation — without the UI. Call the method directly with known inputs.

**Simplify complex inputs:** If the bug occurs with a complex dataset, try to reproduce it with the simplest possible input that still triggers the failure.

!!! tip "Document what you remove"
    When reducing, keep a note of what you have disabled. It is easy to forget what state the code is in after several rounds of commenting out and re-enabling. Version control (`git stash` or a temporary branch) can help manage this without losing your changes.

---

## ◈ III. Logging as Strategic Evidence

Print and log statements are the oldest and most portable debugging technique. They work in every language, every environment, every platform — no debugger required.

The key distinction between strategic logging and noise is **intent**. Strategic logs answer specific questions. Noise logs print everything and produce output that takes longer to read than it does to generate.

### What to Log

The questions that strategic logs answer:

- **Did this code execute?** — a log at the start of a method confirms whether it was called at all
- **What was the value at this moment?** — log variables at the point of decision or assignment
- **In what order did events occur?** — log entry and exit of methods to trace execution sequence
- **What input arrived here?** — log parameters at the entry point of a method

=== "Java"
    ```java
    public double calculateDiscount(String userType, double price) {
        System.out.println("[DEBUG] calculateDiscount called: userType=" + userType + ", price=" + price); // (1)

        if (userType.equals("premium")) {
            double discount = price * 0.20;
            System.out.println("[DEBUG] Premium discount applied: " + discount); // (2)
            return price - discount;
        }

        System.out.println("[DEBUG] No discount applied"); // (3)
        return price;
    }
    ```
    1. Entry log — confirms the method was called and what values arrived
    2. Branch log — confirms which path was taken and the calculated value
    3. Default path log — confirms when no discount condition matched

=== "C#"
    ```csharp
    public decimal CalculateDiscount(string userType, decimal price)
    {
        Console.WriteLine($"[DEBUG] CalculateDiscount: userType={userType}, price={price}"); // (1)

        if (userType == "premium")
        {
            decimal discount = price * 0.20m;
            Console.WriteLine($"[DEBUG] Premium discount: {discount}"); // (2)
            return price - discount;
        }

        return price;
    }
    ```
    1. Entry and parameter log
    2. Computed value log — verifies the calculation before returning

=== "Kotlin / Android"
    ```kotlin
    fun calculateDiscount(userType: String, price: Double): Double {
        Log.d("DiscountCalc", "calculateDiscount: userType=$userType, price=$price") // (1)

        return if (userType == "premium") {
            val discount = price * 0.20
            Log.d("DiscountCalc", "Premium discount applied: $discount") // (2)
            price - discount
        } else {
            Log.d("DiscountCalc", "No discount") // (3)
            price
        }
    }
    ```
    1. Use `Log.d()` with a consistent tag — makes filtering in Logcat straightforward
    2. Log computed intermediate values
    3. Log all branches, not just the expected one

=== "JavaScript / TypeScript"
    ```typescript
    function calculateDiscount(userType: string, price: number): number {
        console.log(`[DEBUG] calculateDiscount: userType=${userType}, price=${price}`); // (1)

        if (userType === 'premium') {
            const discount = price * 0.20;
            console.log(`[DEBUG] Premium discount: ${discount}`); // (2)
            return price - discount;
        }

        console.log('[DEBUG] No discount applied'); // (3)
        return price;
    }
    ```
    1. Use template literals for readable output
    2. Log intermediate values before returning
    3. Log every branch for complete execution tracing

!!! danger "Remove debug logs before committing"
    Debug log statements are investigative tools, not permanent features. Remove them — or replace them with proper structured logging — before committing code. Debug output left in production code pollutes logs, can expose sensitive data, and signals to reviewers that the code was not cleaned up.

---

## ◈ IV. Symptoms vs. Root Causes

One of the most significant conceptual shifts in investigative debugging is learning to distinguish between a **symptom** and its **root cause**.

A symptom is what you observe — the crash, the wrong output, the missing data. A root cause is the reason it happened. Fixing a symptom without addressing the root cause produces a brittle fix: the bug appears to be resolved but resurfaces later, sometimes in a different form.

### Recognising the Difference

!!! example "Symptom vs. root cause — a chain"
    **Observed symptom:** The app crashes with a `NullPointerException` in the `displayUserProfile()` method.

    **First-level cause:** The `user` object is null at the point of the call.

    **Root cause:** The `fetchUser()` method returned early due to a network timeout, and the calling code did not check whether the result was null before proceeding.

    **Fixing the symptom** (adding a null check in `displayUserProfile()`) prevents the crash but does not fix the underlying problem — the user profile was never loaded.

    **Fixing the root cause** (handling the network failure in `fetchUser()` and communicating the failure state to the caller) resolves the actual problem.

**The key question to ask:**

> *"What had to be true for this symptom to occur?"*

Work backwards from the observation. Each answer reveals a deeper level of cause. Continue until you reach the point where the actual incorrect decision or missing step is located.

### Tracing Upstream

Most bugs originate upstream from where they are observed. A `NullPointerException` on line 87 was caused by a null assignment on line 34. A wrong calculation total in the UI was caused by incorrect accumulation in the data layer. An empty screen was caused by a failed API call that was silently swallowed.

!!! tip "Ask 'what happened before this?'"
    When you find the crash site, resist the urge to fix it immediately. Ask what had to be true immediately before that line executed for the crash to occur. Then ask what had to be true before *that*. Follow the chain upstream until you find the point where something was set incorrectly, missed, or mishandled.

---

## ◈ V. Forming and Testing Hypotheses

Investigation without hypotheses is aimless. A hypothesis gives each test a purpose: you are not adding a log statement to see what happens — you are adding it to confirm or deny a specific belief about what the program is doing.

### Forming a Hypothesis

A hypothesis is a specific, testable statement:

- *"The `userType` parameter arrives as null because the API response is not being parsed correctly."*
- *"The loop iterates one time too many because the condition uses `<=` instead of `<`."*
- *"The discount calculation fails for orders above £500 because the comparison uses the wrong data type."*

A hypothesis is not a guess. It is an inference from evidence already gathered. The more evidence you have, the better your hypothesis will be.

### Testing a Hypothesis

To test a hypothesis, make the one change most likely to confirm or deny it, and observe the result.

| If the result is... | Then... |
|---------------------|---------|
| Bug resolves | Hypothesis was correct — document the root cause and explain the fix |
| Bug persists unchanged | Hypothesis was wrong — gather more evidence and revise |
| Bug changes character | Partial hypothesis — you found *a* problem, but possibly not the only one |
| New bug appears | Your change introduced a side effect — revert and reconsider |

Every result produces information. None of them is wasted.

---

## ◈ VI. Explaining the Fix

After a bug is resolved, the investigation is not complete until the fix has been explained.

The explanation has three parts:

**I. Why did it fail?**
Describe the root cause specifically. Not *"there was a null pointer"* but *"the user object was null because fetchUser() returned early on a timeout, and the caller assumed it would always return a valid object."*

**II. Which assumption was wrong?**
Every bug reveals a mistaken assumption. Name it explicitly. *"I assumed that fetchUser() would always return a non-null user. That assumption was wrong."*

**III. Why does the fix work?**
Describe the mechanism of the fix. *"Adding a null check in the caller and falling back to a cached user when the network is unavailable ensures that displayUserProfile() always receives a valid object."*

!!! success "When explanation becomes habit"
    Programmers who explain their fixes consistently develop faster debugging instincts than those who do not. The act of articulating root causes builds a mental library of bug patterns — after encountering a null pointer from an unhandled API failure once and explaining it fully, the next occurrence of the same pattern is recognised immediately.

---

*Continue to: [Print and Log Debugging →](print_log_debugging.md)*

*← Back to: [Section Index](index.md)*

---

*[IDE]: Integrated Development Environment
*[API]: Application Programming Interface
*[UI]: User Interface
