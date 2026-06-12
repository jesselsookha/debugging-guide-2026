# Appendix C · Common Error Glossary

---

## ◎ About This Glossary

This glossary provides plain-language definitions of the most common error types, error messages, and error-related terms encountered in software development. It is organised by category and is deliberately language-agnostic — where specific languages produce different names for the same concept, both are noted.

Use this glossary when you encounter an error name or message you do not recognise. Understanding what an error *means* is always the first step toward finding its cause.

!!! tip "Search before scrolling"
    If you are looking for a specific error name, use your browser's find function (`Ctrl+F` or `Cmd+F`) to locate it quickly.

---

## ◉ I. Syntax Errors

Syntax errors occur when the code violates the grammatical rules of the programming language. The program cannot be compiled or interpreted. They are always detected before execution.

| Error Message | What It Means | Common Cause |
|---------------|--------------|--------------|
| `unexpected token` | The parser encountered a symbol it did not expect at this position | Missing bracket, parenthesis, comma, or semicolon; a keyword used in the wrong context |
| `';' expected` / `expected ';'` | The language requires a semicolon to end a statement and one is missing | Missing `;` at the end of a Java or C# statement |
| `unclosed string literal` | A string was opened with a quote but never closed | Missing closing `"` or `'` |
| `unexpected end of input` / `unexpected end of file` | The file ended while the parser expected more code | An opening bracket `{`, `(`, or `[` was never closed |
| `cannot find symbol` | The compiler cannot find a variable, method, or class you referenced by name | Spelling error, wrong case, variable used outside its scope, or missing import |
| `'; expected'` (CS1002 in C#) | Semicolon missing at end of statement | Missing `;` |
| `illegal start of expression` | Java cannot parse the expression — it found something that cannot legally begin an expression | Missing closing brace `}` from a previous block, or incorrect placement of a keyword |
| `reached end of file while parsing` | Java's compiler ran out of code while expecting more | Unclosed `{` brace somewhere in the file |
| `indentation error` | Python requires consistent indentation; the indentation level is unexpected | Mixed tabs and spaces, or inconsistent indentation inside a block |
| `SyntaxError: invalid syntax` | Generic Python syntax error | Wide range of causes — missing colon `:` after `if`/`for`/`def`, mismatched brackets |

---

## ◉ II. Runtime Errors — Null and Reference

These errors occur when code attempts to use a reference that points to nothing.

| Error Name | Language(s) | What It Means |
|------------|-------------|--------------|
| `NullPointerException` | Java, Kotlin | A method was called, or a field was accessed, on an object reference that is `null` |
| `NullReferenceException` | C# | Same as above — a null reference was dereferenced |
| `TypeError: Cannot read properties of null` | JavaScript, TypeScript | A property was accessed on `null` |
| `TypeError: Cannot read properties of undefined` | JavaScript, TypeScript | A property was accessed on `undefined` — the variable was never assigned |
| `UninitializedPropertyAccessException` | Kotlin | A `lateinit var` was accessed before it was initialised |
| `AttributeError: 'NoneType' object has no attribute` | Python | A method or attribute was accessed on `None` |

!!! info "The null reference is the most common runtime error in object-oriented languages"
    Tony Hoare, who introduced the null reference in 1965, later called it his *"billion-dollar mistake"* — referring to the cumulative cost of null-reference bugs across the industry. Modern languages (Kotlin, Swift, TypeScript with strict null checks) attempt to make null-safety part of the type system to catch these at compile time rather than runtime.

---

## ◉ III. Runtime Errors — Collections and Arrays

These errors occur when code attempts to access a position in a collection that does not exist.

| Error Name | Language(s) | What It Means |
|------------|-------------|--------------|
| `ArrayIndexOutOfBoundsException` | Java, Kotlin | An array was accessed at an index beyond its valid range (0 to length-1) |
| `IndexOutOfRangeException` | C# | Same — an array or list was accessed at an invalid index |
| `IndexOutOfBoundsException` | Java (Lists), Kotlin | A `List` was accessed at an index that does not exist |
| `IndexError: list index out of range` | Python | A list was accessed at an index beyond its valid range |
| `ConcurrentModificationException` | Java | A collection was modified while being iterated with a `for-each` loop |

!!! warning "Off-by-one errors are the most common cause"
    The most frequent cause of index-out-of-bounds errors is an off-by-one mistake in a loop condition. A loop that uses `i <= array.length` instead of `i < array.length` will attempt to access index `array.length` on the final iteration — which does not exist. Always use `<` not `<=` when iterating to an array's length.

---

## ◉ IV. Runtime Errors — Type and Casting

These errors occur when the type of a value does not match what was expected.

| Error Name | Language(s) | What It Means |
|------------|-------------|--------------|
| `ClassCastException` | Java, Kotlin | An object was cast to a type it is not compatible with at runtime |
| `InvalidCastException` | C# | Same — an invalid type cast was attempted |
| `TypeError` | JavaScript, TypeScript, Python | An operation was performed on a value of the wrong type |
| `NumberFormatException` | Java, Kotlin | A string was passed to `parseInt()` or similar that does not contain a valid number (e.g., `"abc"`) |
| `FormatException` | C# | Same — `int.Parse()` received a string that is not a valid number |
| `ValueError` | Python | A function received an argument of the right type but an inappropriate value |

---

## ◉ V. Runtime Errors — Arithmetic

| Error Name | Language(s) | What It Means |
|------------|-------------|--------------|
| `ArithmeticException: / by zero` | Java, Kotlin | Integer division by zero was attempted |
| `DivideByZeroException` | C# | Same — integer division by zero |
| `ZeroDivisionError` | Python | Division by zero |
| `Infinity` / `NaN` | JavaScript, TypeScript | Floating-point division by zero produces `Infinity`; operations on `Infinity` or invalid operations produce `NaN` (Not a Number) — neither throws an exception, but both produce incorrect results |

!!! note "NaN and Infinity do not crash — they propagate"
    In JavaScript and TypeScript, `10 / 0` produces `Infinity` and `0 / 0` produces `NaN`. Neither throws an exception. Both propagate silently through calculations — meaning the wrong value can travel through many operations before the problem is noticed. Always validate divisors before dividing in JavaScript/TypeScript.

---

## ◉ VI. Runtime Errors — Stack and Memory

| Error Name | Language(s) | What It Means |
|------------|-------------|--------------|
| `StackOverflowError` | Java, Kotlin, C# | Infinite or excessively deep recursion exhausted the call stack |
| `RecursionError: maximum recursion depth exceeded` | Python | Same — Python has a configurable recursion limit (default 1000) |
| `OutOfMemoryError` / `OutOfMemoryException` | Java, Kotlin, C# | The application consumed all available heap memory — typically a memory leak or excessive object creation |
| `MemoryError` | Python | Same — Python ran out of memory |

---

## ◈ VII. Runtime Errors — Files and I/O

| Error Name | Language(s) | What It Means |
|------------|-------------|--------------|
| `FileNotFoundException` | Java, Kotlin, C# | The program tried to open a file that does not exist at the specified path |
| `FileNotFoundError` | Python | Same |
| `IOException` | Java, Kotlin | A general input/output operation failed — file read/write, network connection, etc. |
| `UnauthorizedAccessException` | C# | The program tried to read or write a file without the required permissions |
| `PermissionError` | Python | Same |

---

## ◈ VIII. Compilation and Build Errors

These errors occur during the build process, before the application runs.

| Error / Term | What It Means |
|-------------|--------------|
| `cannot resolve symbol` | The IDE cannot find the class, method, or variable referenced — typically a missing import or dependency |
| `incompatible types` | A value of one type was assigned or passed where a different type is required |
| `type mismatch` | Kotlin's equivalent — a value's inferred type does not match the declared type |
| `missing return statement` | A method declared to return a value does not have a `return` statement on all code paths |
| `unreachable statement` | Code appears after a `return`, `throw`, or `break` that prevents it from ever executing |
| `variable might not have been initialised` | Java detected a code path where a local variable could be used before being assigned |
| `ambiguous method call` | Two or more method overloads match the call equally well and the compiler cannot determine which to use |
| `Build failed` | One or more compilation errors prevent the project from being built — check the error list for the specific cause |

---

## ◈ IX. Android / Kotlin-Specific Errors

| Error Name | What It Means |
|------------|--------------|
| `FATAL EXCEPTION: main` | The main (UI) thread threw an uncaught exception — the app crashed. The exception type and stack trace follow. |
| `NetworkOnMainThreadException` | A network request was made on the main thread, which is not allowed in Android. Use a coroutine, background thread, or async operation. |
| `CalledFromWrongThreadException` | A UI element was accessed from a background thread. All UI updates must occur on the main thread. |
| `WindowManager$BadTokenException` | A dialog or window was shown after the Activity that owns it was destroyed. |
| `IllegalStateException: Fragment not attached` | A Fragment method was called after the Fragment was detached from its Activity — typically in an async callback. |
| `kotlin.UninitializedPropertyAccessException` | A `lateinit var` was accessed before `=` was called on it. |

---

## ◈ X. JavaScript / TypeScript-Specific Errors

| Error Name | What It Means |
|------------|--------------|
| `ReferenceError: X is not defined` | A variable or function named X was used but never declared in any accessible scope |
| `TypeError: X is not a function` | Code attempted to call something as a function, but the value at that name is not a function (possibly `undefined`, `null`, or an object) |
| `SyntaxError: Unexpected token '<'` | Often seen when a JavaScript file receives an HTML response (e.g., a 404 page) instead of valid JSON or JS |
| `Uncaught (in promise)` | An async operation threw an error that was not caught with `.catch()` or `try/catch` in an `async` function |
| `Cannot set properties of undefined` | A property was assigned on `undefined` — the object does not exist |
| `Maximum call stack size exceeded` | Infinite recursion — same as `StackOverflowError` in Java |
| `CORS error` | A network request was blocked because the server did not include the appropriate cross-origin headers. This is a browser security restriction, not a code error. |

---

## ◎ XI. Common Terms in Error Messages

| Term | Meaning |
|------|---------|
| `exception` | An object representing a runtime error — thrown when something goes wrong and caught (or not) by exception-handling code |
| `stack trace` | The list of method calls active at the time of an exception — read from top (closest to crash) to bottom (entry point) |
| `uncaught exception` | An exception that was thrown but not handled by any `catch` block — causes the program to terminate |
| `unhandled promise rejection` | An async operation failed and no `.catch()` or `async try/catch` was in place to handle the failure |
| `fatal` | An error so severe that the program cannot continue and must terminate immediately |
| `warning` | A non-fatal issue detected by the compiler, linter, or runtime — the program runs, but the warning indicates a potential problem |
| `deprecated` | A feature that still works but is scheduled for removal in a future version — replace it with the recommended alternative |
| `assertion failed` | An `assert` statement's condition evaluated to false — the program's own pre-condition check was violated |

---

*← Back to: [Appendix B · Trace Tables](trace_tables.md)*

*Continue to: [Appendix D · Debugging Anti-Patterns →](anti_patterns.md)*

*← Back to: [Appendices Overview](index.md)*

---

*[IDE]: Integrated Development Environment
*[NaN]: Not a Number — a special floating-point value produced by invalid arithmetic operations
*[CORS]: Cross-Origin Resource Sharing
*[JSON]: JavaScript Object Notation
*[UI]: User Interface
