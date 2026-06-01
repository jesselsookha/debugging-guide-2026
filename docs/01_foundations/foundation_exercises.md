# ▣ Foundation Exercises

---

## ▣ How to Use These Exercises

These exercises are practical. Reading about debugging is useful. Doing it is where the skill is built.

Work through each exercise before checking the answer. If you get it wrong, do not simply read the correct answer and move on — trace back through the workflow and understand *why* your classification or reading was incorrect. That reasoning is the exercise.

!!! note "No IDE required for most exercises"
    The classification and error-reading exercises are designed to be completed on paper or in a plain text editor. You are practising the reading and reasoning skills, not the mechanical act of compiling code. Some exercises in Set C involve running code — those are marked accordingly.

---

## ▣ Set A — Classify the Error Type

For each code sample and its associated output, identify:

- The **error type** (syntax, runtime, logical, or semantic)
- A **one-sentence explanation** of what is wrong

---

### A1 — Java

```java
public class Main {
    public static void main(String[] args) {
        int x = 10
        int y = 20;
        System.out.println(x + y);
    }
}
```

**Output:**
```
Main.java:3: error: ';' expected
        int x = 10
                  ^
1 error
```

??? question "Show answer"
    **Error type:** Syntax error

    **Explanation:** A semicolon is missing at the end of `int x = 10`. Java requires every statement to end with `;`. The compiler detected the missing terminator at line 3 and reported the position with a `^` caret.

---

### A2 — C#

```csharp
using System;

class Program {
    static void Main() {
        int[] scores = { 85, 92, 78 };
        Console.WriteLine(scores[5]);
    }
}
```

**Output:**
```
Unhandled exception. System.IndexOutOfRangeException:
Index was outside the bounds of the array.
   at Program.Main() in Program.cs:line 6
```

??? question "Show answer"
    **Error type:** Runtime error

    **Explanation:** The array `scores` has three elements at indices 0, 1, and 2. Index 5 does not exist. The program compiled and started running, but crashed when it attempted to access a position beyond the array boundary. This is a classic `IndexOutOfRangeException`.

---

### A3 — Kotlin

```kotlin
fun main() {
    val numbers = listOf(10, 20, 30, 40, 50)
    var total = 0
    for (n in numbers) {
        total = n
    }
    println("Total: $total")
}
```

**Output:**
```
Total: 50
```

Expected output: `Total: 150`

??? question "Show answer"
    **Error type:** Logical error

    **Explanation:** The loop assigns `total = n` on each iteration instead of accumulating with `total += n`. The program runs without error, but produces the last value in the list (`50`) instead of the sum of all values (`150`). The correct line is `total += n`.

---

### A4 — JavaScript

```javascript
function isEven(n) {
    return n % 2 = 0;
}

console.log(isEven(4));
```

**Output:**
```
SyntaxError: Invalid left-hand side in assignment
```

??? question "Show answer"
    **Error type:** Syntax error (with a semantic intent behind it)

    **Explanation:** `=` is the assignment operator. `==` or `===` is the comparison operator. `n % 2 = 0` attempts to assign `0` to the result of `n % 2`, which is not a valid assignment target. The correct expression is `return n % 2 === 0;`.

---

### A5 — Java

```java
public class Greeting {
    public static void main(String[] args) {
        String name = null;
        if (name.equals("Alice")) {
            System.out.println("Hello, Alice!");
        }
    }
}
```

**Output:**
```
Exception in thread "main" java.lang.NullPointerException:
Cannot invoke "String.equals(Object)" because "name" is null
    at Greeting.main(Greeting.java:4)
```

??? question "Show answer"
    **Error type:** Runtime error

    **Explanation:** `name` is explicitly set to `null`, then `.equals()` is called on it. Calling any method on a null reference throws a `NullPointerException` at runtime. The program compiled successfully — the null assignment is syntactically valid. The fix is either to initialise `name` with a value, or to reverse the comparison: `"Alice".equals(name)`, which is null-safe in Java.

---

### A6 — TypeScript

```typescript
function celsiusToFahrenheit(c: number): number {
    return c * 9 / 5 + 32;
}

// Convert 100°C — expected result: 212°F
console.log(celsiusToFahrenheit(100)); // prints 212 ✓

// Convert 0°C — expected result: 32°F
console.log(celsiusToFahrenheit(0));   // prints 32 ✓

// Convert -40°C — expected result: -40°F
console.log(celsiusToFahrenheit(-40)); // prints -40 ✓
```

**Now consider this version:**

```typescript
function celsiusToFahrenheit(c: number): number {
    return c * (9 / 5 + 32); // (1)
}
```
1. Parentheses changed — does this affect the result?

??? question "Show answer"
    **Error type:** Logical / Semantic error

    **Explanation:** The parentheses change the order of operations. The original formula is `(c × 9 ÷ 5) + 32`. The modified version calculates `9 ÷ 5 + 32 = 33.8` first, then multiplies by `c`: `c × 33.8`. For `c = 100`, the result is `3380` instead of `212`. No syntax error, no crash — but the formula is now mathematically wrong. This illustrates how operator precedence errors produce plausible-looking code that is silently incorrect.

---

## ▣ Set B — Read the Error Message

For each error output below, answer the four reading questions without looking at any code.

**Reading questions:**
1. What is the error type?
2. What does the description tell you happened?
3. In which file and at which line did it occur?
4. What would you look at first?

---

### B1

```
error: cannot find symbol
        System.out.println(mesage);
                           ^
  symbol:   variable mesage
  location: class Main
Main.java:8: error
```

??? question "Show answer"
    1. **Error type:** Compile-time error — `cannot find symbol`
    2. **Description:** The variable `mesage` does not exist. The compiler cannot find any declaration with that name.
    3. **File and line:** `Main.java`, line 8
    4. **Look at first:** Line 8 in `Main.java`. The variable name is likely a spelling error — `mesage` instead of `message`. Check the declaration of the variable and compare spelling exactly.

---

### B2

```
Unhandled exception. System.DivideByZeroException:
Attempted to divide by zero.
   at Calculator.Divide(Calculator.cs:line 15)
   at Program.Main(Program.cs:line 7)
```

??? question "Show answer"
    1. **Error type:** Runtime error — `DivideByZeroException`
    2. **Description:** A division by zero was attempted. The divisor was `0` at the time of execution.
    3. **File and line:** Crash occurred in `Calculator.cs` at line 15. Called from `Program.cs` at line 7.
    4. **Look at first:** Line 15 in `Calculator.cs` — find the division operation. Then check how the divisor value arrives at that line. Either it was set to `0` directly, or it came from user input or a calculation that produced `0`. A guard condition (`if (divisor == 0)`) should be added.

---

### B3

```
FATAL EXCEPTION: main
Process: com.example.shoppingapp, PID: 9842
java.lang.NullPointerException: Attempt to invoke virtual method
'void android.widget.TextView.setText(java.lang.CharSequence)'
on a null object reference
    at com.example.shoppingapp.ProductActivity.displayProduct(ProductActivity.kt:63)
    at com.example.shoppingapp.ProductActivity.onCreate(ProductActivity.kt:31)
    at android.app.Activity.performCreate(Activity.java:8051)
```

??? question "Show answer"
    1. **Error type:** Runtime error — `NullPointerException`
    2. **Description:** `setText()` was called on a `TextView` that is null. The view reference was not initialised before use.
    3. **File and line (your code):** `ProductActivity.kt` line 63, inside `displayProduct()`. It was called from `onCreate()` at line 31.
    4. **Look at first:** Line 63 in `ProductActivity.kt` — find the `TextView` whose `setText()` is being called. Check how it was initialised. It was likely declared but never assigned via `findViewById()`, or `displayProduct()` was called before the layout was inflated.

---

## ▣ Set C — Apply the Workflow

These exercises ask you to apply the full six-step workflow to a broken program. For each one, document your process using the six steps as a structure.

---

### C1 — Java: The Wrong Average

```java
public class Average {
    public static void main(String[] args) {
        int[] scores = {70, 85, 90, 60, 75};
        int total = 0;

        for (int score : scores) {
            total = total + score;
        }

        int average = total / scores.length;
        System.out.println("Average: " + average);
    }
}
```

**Expected output:** `Average: 76`

**Actual output:** `Average: 76`

!!! question "Before you move on"
    Wait — the expected and actual output match. Does that mean there are no errors?

    Re-examine the expected output. The scores are 70, 85, 90, 60, 75. Their sum is 380. Divided by 5 that is 76. So `Average: 76` is correct.

    **But what if the scores were 70, 85, 90, 60, 76?** The sum would be 381. Divided by 5 that is 76.2. But integer division in Java truncates — the output would still be `Average: 76`.

    This is a hidden logical issue: integer division silently discards the decimal portion. The program appears correct for these inputs but produces inaccurate results for inputs where the average is not a whole number.

??? question "Show the fix and explanation"
    **Issue:** Integer division truncates. `381 / 5` in Java integer arithmetic = `76`, not `76.2`.

    **Fix:** Use floating-point division.
    ```java
    double average = (double) total / scores.length;
    System.out.printf("Average: %.1f%n", average);
    ```
    **Explanation:** Casting `total` to `double` before dividing forces floating-point arithmetic. The result is now accurate to decimal places.

    **Lesson:** Always test with inputs where the expected result is not a round number. Edge cases reveal assumptions.

---

### C2 — Kotlin: The Loop That Does Not Loop

```kotlin
fun main() {
    val items = listOf("apple", "banana", "cherry")
    var result = ""

    for (item in items) {
        result = item
    }

    println("Items: $result")
}
```

**Expected output:** `Items: apple, banana, cherry`

**Actual output:** `Items: cherry`

Apply the six steps. Identify the error type, locate the problem, and write the corrected version.

??? question "Show the workflow and fix"
    **Step I — Read:** No error message. The program runs but produces wrong output.

    **Step II — Identify:** Logical error — the output is the last item only.

    **Step III — Locate:** The `for` loop body — specifically the assignment `result = item`.

    **Step IV — Compare:** Expected all items concatenated. Actual is only the last item.

    **Step V — One change:** The loop replaces `result` each iteration instead of appending. Change `result = item` to build a concatenated string.

    **Step VI — Test:**
    ```kotlin
    fun main() {
        val items = listOf("apple", "banana", "cherry")
        val result = items.joinToString(", ")
        println("Items: $result")
    }
    ```
    Or with explicit loop:
    ```kotlin
    for ((index, item) in items.withIndex()) {
        result += if (index < items.size - 1) "$item, " else item
    }
    ```
    **Output:** `Items: apple, banana, cherry`

---

### C3 — JavaScript: The Function That Returns Nothing

```javascript
function multiply(a, b) {
    const product = a * b;
}

const result = multiply(6, 7);
console.log("Result: " + result);
```

**Expected output:** `Result: 42`

**Actual output:** `Result: undefined`

Apply the six steps. Identify the error type and correct it.

??? question "Show the workflow and fix"
    **Step I — Read:** No error thrown. Output is `undefined` instead of `42`.

    **Step II — Identify:** Logical error (or semantic error — the function does not do what was intended).

    **Step III — Locate:** The `multiply` function — specifically, the missing `return` statement.

    **Step IV — Compare:** Expected `42`. Actual `undefined`. In JavaScript, a function that does not explicitly return a value returns `undefined`.

    **Step V — One change:** Add `return product;` before the closing brace.

    **Step VI — Test:**
    ```javascript
    function multiply(a, b) {
        const product = a * b;
        return product;
    }

    const result = multiply(6, 7);
    console.log("Result: " + result); // Result: 42
    ```

---

## ▣ Reflection

After completing these exercises, consider:

!!! question "Reflect on your process"
    - Which error type was easiest to identify? Which was hardest?
    - Did you find yourself wanting to look at the answer before completing your own reading?
    - In Set C, did writing out the six steps change how you approached the problem compared to just reading the code?
    - What would you do differently the next time you encounter an error in your own code?

There are no correct answers to these reflection questions. They are for your own development.

---

*You have completed 01 · Foundations.*

*Continue to: [02 · Investigation →](../02_investigation/index.md)*

*← Back to: [Section Index](index.md)*
