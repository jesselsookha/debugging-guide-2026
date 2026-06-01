# ◉ Understanding Errors

---

## ◉ What an Error Actually Is

An error is not the system telling you that you have failed. It is the system telling you, as precisely as it can, that it encountered something it could not process — and stopping rather than continuing to produce incorrect results.

That distinction matters. An error message is **information**. The programmer's first job is to read it.

Before any fix can be attempted, before any search is conducted, before any help is requested — the error must be understood. And the first step in understanding an error is knowing what *kind* of error it is, because different types of errors have different causes, appear at different times, and require different approaches to resolve.

There are four categories of error that every programmer will encounter, regardless of language or platform.

---

## ◉ I. Syntax Errors

### What They Are

A syntax error occurs when the code you have written violates the grammatical rules of the programming language. The program cannot be compiled or interpreted because the instruction is not a valid sentence in that language.

Think of it as a spelling or punctuation error in a document — except that while a human reader might infer your meaning despite a typo, a compiler or interpreter will not. It requires exact, correct syntax.

### When They Occur

Syntax errors are caught **before the program runs** — at compile time (in compiled languages like Java, C#, C++) or at parse time (in interpreted languages like JavaScript, Python). The program will not execute at all if syntax errors are present.

### What They Look Like

=== "Java"
    ```java
    public class Main {
        public static void main(String[] args) {
            System.out.println("Hello")  // (1)
        }
    }
    ```
    1. Missing semicolon `;` at the end of the statement. Java requires it.
    **Error:** `error: ';' expected`

=== "C#"
    ```csharp
    static void Main(string[] args)
    {
        Console.WriteLine("Hello")  // (1)
    }
    ```
    1. Missing semicolon `;`. C# requires it just as Java does.
    **Error:** `error CS1002: ; expected`

=== "Kotlin"
    ```kotlin
    fun main() {
        println("Hello"   // (1)
    }
    ```
    1. Missing closing parenthesis `)`. Kotlin's compiler will flag the mismatch.
    **Error:** `error: expecting ')'`

=== "JavaScript"
    ```javascript
    function greet() {
        console.log("Hello"   // (1)
    }
    ```
    1. Missing closing parenthesis `)`. JavaScript will catch this at parse time.
    **Error:** `SyntaxError: Unexpected token '}'`

=== "Pseudocode"
    ```
    START
        OUTPUT "Hello   ← missing END or closing delimiter
    ```
    Even in pseudocode on paper, an unclosed block is a syntax error.
    It signals that the structure of the algorithm is incomplete.

### ◉ Key Characteristics

!!! info "Syntax errors — what to know"
    - Caught before the program runs — nothing executes
    - Usually reported with a **line number** and a description of what was expected
    - Often caused by missing punctuation, unclosed brackets, or misspelled keywords
    - The reported line number is where the compiler *noticed* the problem — the actual mistake may be one line earlier

!!! warning "The line number is a clue, not a guarantee"
    Compilers report where they became confused, not always where the mistake is. A missing opening brace `{` on line 10 may produce a syntax error reported on line 15 — where the compiler expected to find something that would have been valid after a correctly opened block. Always check the line before the reported line.

---

## ◉ II. Runtime Errors

### What They Are

A runtime error occurs while the program is executing. The syntax was correct — the program compiled and started running — but at some point during execution, the program attempted an operation that was impossible or illegal: dividing by zero, accessing a null object, reading past the end of an array, or trying to open a file that does not exist.

When a runtime error occurs, the program **crashes** — it stops immediately and reports the error.

### When They Occur

Runtime errors occur **during execution** — you will not see them until the program runs and reaches the problematic instruction. The error may only occur under specific conditions: a particular input, a particular sequence of actions, or a particular state of the system.

### What They Look Like

=== "Java"
    ```java
    String name = null;
    System.out.println(name.length()); // (1)
    ```
    1. `name` is null. Calling `.length()` on a null reference causes a crash.
    **Error:** `Exception in thread "main" java.lang.NullPointerException`

=== "C#"
    ```csharp
    string name = null;
    Console.WriteLine(name.Length); // (1)
    ```
    1. Same problem — null reference access.
    **Error:** `System.NullReferenceException: Object reference not set to an instance of an object`

=== "Kotlin"
    ```kotlin
    val numbers = listOf(1, 2, 3)
    println(numbers[5]) // (1)
    ```
    1. Index 5 does not exist. The list has only indices 0, 1, 2.
    **Error:** `Exception in thread "main" java.lang.IndexOutOfBoundsException: Index 5 out of bounds for length 3`

=== "JavaScript"
    ```javascript
    let user = null;
    console.log(user.name); // (1)
    ```
    1. Accessing a property on `null`.
    **Error:** `TypeError: Cannot read properties of null (reading 'name')`

=== "TypeScript"
    ```typescript
    function divide(a: number, b: number): number {
        return a / b; // (1)
    }
    console.log(divide(10, 0));
    ```
    1. TypeScript does not prevent division by zero — the result is `Infinity`, not a crash in JavaScript/TypeScript. In other languages (Java, C#), integer division by zero is a runtime error.
    **Result:** `Infinity` — a logical problem, not always a crash.

=== "Pseudocode"
    ```
    total = 0
    count = 0
    average = total / count  // runtime error: division by zero
    OUTPUT average
    ```
    The algorithm is syntactically correct. The error only appears when `count` is zero at the point of execution.

### ◉ Key Characteristics

!!! info "Runtime errors — what to know"
    - Occur **during execution** — the program compiled successfully
    - Usually caused by unexpected input, uninitialised variables, invalid operations, or resource unavailability
    - Produce an **exception** or **error report** that includes the type of error and a stack trace
    - The stack trace shows exactly where in the program the crash occurred — and the path of calls that led there

!!! bug "Common runtime errors to recognise"
    | Error Name | Language(s) | Typical Cause |
    |------------|-------------|---------------|
    | `NullPointerException` | Java, Kotlin | Calling a method on a null object |
    | `NullReferenceException` | C# | Same — accessing a property of a null reference |
    | `IndexOutOfBoundsException` | Java, Kotlin | Accessing an array/list index that does not exist |
    | `IndexOutOfRangeException` | C# | Same in C# context |
    | `TypeError` | JavaScript, TypeScript | Operating on a value of the wrong type |
    | `ArrayIndexOutOfBoundsException` | Java | Accessing an array beyond its length |

---

## ◉ III. Logical Errors

### What They Are

A logical error is the most challenging type of error to find. The program compiles without error. It runs without crashing. But the output is **wrong**.

The program is doing exactly what it was told to do — the problem is that what it was told to do does not match what was intended. The error is in the logic of the algorithm itself.

### When They Occur

Logical errors have no fixed moment of discovery. They may be found immediately by the programmer, noticed by a user, caught by a test, or — in the worst case — not found at all until they cause a problem in production.

### What They Look Like

=== "Java"
    ```java
    // Calculate the average of two numbers
    int a = 10;
    int b = 20;
    int average = a + b / 2; // (1)
    System.out.println(average); // prints 20, expected 15
    ```
    1. Operator precedence error. Division happens before addition. The expression evaluates as `a + (b / 2)` = `10 + 10` = `20`. The correct expression is `(a + b) / 2`.

=== "C#"
    ```csharp
    // Check if a value is within range 1-10
    int value = 5;
    if (value > 1 && value > 10) // (1)
    {
        Console.WriteLine("In range");
    }
    ```
    1. Second condition should be `value < 10`, not `value > 10`. No error is thrown — the condition is simply never true for valid input.

=== "Kotlin"
    ```kotlin
    // Count down from 5 to 1
    for (i in 5..1) { // (1)
        println(i)
    }
    // prints nothing
    ```
    1. `5..1` is an empty range in Kotlin. Use `5 downTo 1` instead. The loop runs but iterates zero times — no crash, wrong behaviour.

=== "JavaScript"
    ```javascript
    // Check if user is an adult
    function isAdult(age) {
        return age > 18; // (1)
    }
    console.log(isAdult(18)); // false — should be true
    ```
    1. Should be `age >= 18`. An 18-year-old returns `false`. No error, wrong result.

=== "Pseudocode"
    ```
    total = 0
    for each score in scores 
        total = score        // assigns instead of accumulates
    endfor
    OUTPUT total / COUNT(scores)
    ```
    The structure is correct. The mistake is in line 3: `total = score` replaces the total each iteration instead of adding to it. The correct instruction is `total = total + score`.

### ◉ Key Characteristics

!!! warning "Logical errors — what to know"
    - No error message is produced — the program compiles and runs
    - Discovered only by comparing **expected output** with **actual output**
    - Require careful reasoning about what the code does versus what it was meant to do
    - Trace tables, test cases with known values, and stepping through code with a debugger are the primary tools for finding them

!!! tip "How to approach logical errors"
    Start with a known input for which you can manually calculate the correct output. Trace the program's execution step by step — either on paper using a trace table, or in the IDE's debugger — and compare what the program produces at each step with what you expect. The first point of divergence is where the logical error lives.

---

## ◉ IV. Semantic Errors

### What They Are

Semantic errors are related to logical errors but distinct. The code is syntactically valid, it runs without crashing, but the *meaning* of the instructions is wrong in context. The programmer wrote valid code that does something it was not intended to do — not because the logic was wrong, but because the statement itself was misused or misapplied.

The word "semantic" comes from the study of meaning. A semantic error is an error of meaning: valid syntax, valid logic structure, but the wrong operation for the intended purpose.

=== "Java"
    ```java
    // Intended: compare two strings
    String input = "yes";
    if (input == "yes") { // (1)
        System.out.println("Confirmed");
    }
    ```
    1. `==` compares object references in Java, not string content. Use `.equals()` for string comparison. This compiles, runs, and may sometimes print "Confirmed" — but for the wrong reason, and not reliably.

=== "C#"
    ```csharp
    // Intended: assign only if null
    string name = "Alice";
    name = name ?? "Default"; // This is correct C# usage
    // But consider:
    if (name = null) { } // (1)
    ```
    1. `name = null` is an assignment, not a comparison. C# will flag this as an error in a boolean context — other languages may not. The programmer meant `name == null`.

=== "Python"
    ```python
    # Intended: calculate x divided by 2π
    import math
    x = 10
    y = x / 2 * math.pi  # (1)
    ```
    1. Operator precedence: this evaluates as `(x / 2) * π` = `5π` ≈ 15.7. The intended expression `x / (2π)` should be written as `x / (2 * math.pi)` ≈ 1.59. No error — wrong meaning.

### ◉ Key Characteristics

!!! info "Semantic errors — what to know"
    - The code is valid and runs without crashing
    - The error is in what the statement *means*, not in its structure
    - Common in string comparisons, operator precedence, and type coercion contexts
    - Often subtle — the output may look plausible but be subtly incorrect

---

## ◉ Summary — The Four Error Types at a Glance

| Error Type | When Detected | Program Runs? | Primary Signal |
|------------|---------------|---------------|----------------|
| **Syntax** | Compile / parse time | No | Error in IDE / compiler output |
| **Runtime** | During execution | Starts, then crashes | Exception + stack trace |
| **Logical** | During testing / review | Yes — wrong output | Output does not match expectation |
| **Semantic** | During review | Yes — misleading output | Output looks plausible but is incorrect |

!!! question "Check your understanding"
    Before moving on, try to classify the following:

    I. A program produces the wrong total because a `+` was written instead of a `*`. *(Which error type?)*

    II. A program crashes when the user enters their name but works when they enter a number. *(Which error type?)*

    III. A program fails to compile because a closing brace `}` is missing. *(Which error type?)*

    IV. A program checks whether two objects are the same object in memory rather than whether they contain the same value. *(Which error type?)*

    Answers: I — Logical. II — Runtime. III — Syntax. IV — Semantic.

---

*Continue to: [Reading Error Messages →](reading_error_messages.md)*

*← Back to: [Section Index](index.md)*

---

*[IDE]: Integrated Development Environment
*[SDK]: Software Development Kit*
