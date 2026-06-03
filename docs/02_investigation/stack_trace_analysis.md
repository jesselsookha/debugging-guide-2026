# ◈ Stack Trace Analysis

---

## ◈ What a Stack Trace Is

A stack trace is an automatic record produced by the runtime when a program crashes. It captures the **call stack** at the exact moment of failure — the complete sequence of method calls that were active when the exception was thrown.

If an error message tells you *what* went wrong, the stack trace tells you *where* and *how the program got there*.

Reading a stack trace fluently is one of the highest-return skills in debugging. It transforms a crash from a mystery into a map.

---

## ◈ I. Understanding the Call Stack

To read a stack trace, you first need to understand what the call stack is.

As a program executes, every method call is added to the top of a structure called the call stack. When a method finishes, it is removed from the stack. When an exception is thrown and not caught, the runtime records the entire stack — every method that was in progress — before terminating.

!!! example "A simple call stack"
    ```
    main()
      └── loadUser()
            └── fetchFromDatabase()
                  └── parseResponse()   ← exception thrown here
    ```

    The stack trace for this scenario lists the methods from innermost (closest to the crash) to outermost (entry point):

    ```
    parseResponse()    ← top: where the exception was thrown
    fetchFromDatabase()
    loadUser()
    main()             ← bottom: where execution began
    ```

The top of the stack trace is always closest to the crash. The bottom is always the program's entry point.

---

## ◈ II. Reading a Stack Trace — Step by Step

### Java Example

```
Exception in thread "main"
java.lang.NullPointerException:                                          // (1)
Cannot invoke "String.length()" because "str" is null                    // (2)
    at com.example.util.StringUtils.getLength(StringUtils.java:23)       // (3)
    at com.example.service.UserService.validateName(UserService.java:45) // (4)
    at com.example.Main.main(Main.java:12)                               // (5)
```

1. **Exception type** — `NullPointerException`. A method was called on a null reference.
2. **Exception description** — the specific method called on null (`String.length()`) and which variable was null (`str`). This is the most information-dense line in the trace.
3. **Crash site** — `StringUtils.java` line 23, inside `getLength()`. This is where the exception was thrown.
4. **Caller** — `UserService.java` line 45, inside `validateName()`. This method called `getLength()`.
5. **Entry point** — `Main.java` line 12. This is where `validateName()` was ultimately called from.

**Where to start:** Line 3 — the crash site inside your own code. Open `StringUtils.java`, go to line 23, and find the call to `str.length()`. The variable `str` is null. Now trace back to understand why — who called `getLength()` with a null argument? That is line 45 of `UserService.java`.

---

### C# Example

```
System.NullReferenceException:                                                   // (1)
Object reference not set to an instance of an object.                            // (2)
   at MyApp.Data.UserRepository.GetDisplayName(UserRepository.cs:line 67)        // (3)
   at MyApp.Controllers.UserController.GetProfile(UserController.cs:line 34)     // (4)
   at MyApp.Controllers.UserController.Index(UserController.cs:line 18)          // (5)
```

1. **Exception type** — `NullReferenceException`
2. **Description** — standard C# message for null access; less specific than Java's
3. **Crash site** — `UserRepository.cs` line 67, method `GetDisplayName()`
4. **Caller** — `UserController.cs` line 34, method `GetProfile()`
5. **Caller's caller** — `UserController.cs` line 18, method `Index()`

**Where to start:** `UserRepository.cs` line 67. Find what null object is being accessed. Then check `UserController.cs` line 34 to see what was passed into `GetDisplayName()`.

---

### Kotlin / Android Logcat Example

```
FATAL EXCEPTION: main
Process: com.example.shopapp, PID: 14821
java.lang.NumberFormatException:                                                      // (1)
For input string: "abc"                                                               // (2)
    at java.lang.Integer.parseInt(Integer.java:652)                                   // (3) — runtime library
    at java.lang.Integer.parseInt(Integer.java:770)                                   // (3) — runtime library
    at com.example.shopapp.cart.CartFragment.updateQuantity(CartFragment.kt:88)       // (4) — YOUR code
    at com.example.shopapp.cart.CartFragment.onQuantityChanged(CartFragment.kt:62)    // (5) — YOUR code
    at android.view.View.performClick(View.java:7125)                                 // (6) — Android framework
    at android.widget.TextView.onEditorAction(TextView.java:1360)                     // (6) — Android framework
```

1. **Exception type** — `NumberFormatException`
2. **Description** — attempted to parse `"abc"` as an integer. This is the value that caused the failure.
3. **Runtime library frames** — `Integer.parseInt()` is internal Java code. This is where the exception was thrown mechanically, but it is not where the *mistake* is.
4. **Your code — crash context** — `CartFragment.kt` line 88, `updateQuantity()`. This is where `parseInt()` was called with the bad value.
5. **Your code — event origin** — `CartFragment.kt` line 62, `onQuantityChanged()`. This is what triggered `updateQuantity()`.
6. **Framework frames** — Android internal code that handles touch events. Not your code.

!!! tip "In Android traces: focus on your package name"
    In a Logcat stack trace, the lines you care about are those containing your application's package name (e.g., `com.example.shopapp`). Android framework lines (`android.view.*`, `android.widget.*`) and Java runtime lines (`java.lang.*`) show the execution path through the system, but the mistake is almost always in your code.

**Where to start:** `CartFragment.kt` line 88. Find the `parseInt()` call. Trace back to see where the value being parsed comes from — in this case, user input from a text field. The root cause is that user-typed text was passed directly to `parseInt()` without validation.

---

### JavaScript / TypeScript Example

```
TypeError: Cannot read properties of undefined (reading 'price')    // (1)
    at calculateTotal (cart.ts:34:32)                               // (2)
    at applyPromoCode (cart.ts:58:18)                               // (3)
    at checkout (checkout.ts:22:14)                                 // (4)
    at Object.<anonymous> (main.ts:7:1)                             // (5)
```

1. **Error type and description** — `undefined` was accessed as if it had a `price` property. Something expected to be an object is `undefined`.
2. **Crash site** — `cart.ts` line 34, column 32, function `calculateTotal()`
3. **Caller** — `cart.ts` line 58, `applyPromoCode()`
4. **Caller's caller** — `checkout.ts` line 22, `checkout()`
5. **Entry** — `main.ts` line 7

**Where to start:** `cart.ts` line 34, column 32. Find the `.price` property access. The object being accessed is `undefined`. Trace back to `applyPromoCode()` to see what it passed to `calculateTotal()`.

---

## ◈ III. Distinguishing Your Code from Framework Code

In any real application, a stack trace will contain a mixture of your code and code from frameworks, libraries, and the runtime. The mistake is almost always in your code — not in the framework.

**Your code** is identifiable by your package or namespace name:

| Language | Your code looks like |
|----------|---------------------|
| Java | `com.yourname.yourapp.*` |
| Kotlin / Android | `com.yourpackage.*` |
| C# | `YourNamespace.*` |
| JavaScript / TypeScript | Your file names (e.g., `cart.ts`, `userService.js`) |

**Framework and library code** looks like:

| Source | Example prefix |
|--------|---------------|
| Java runtime | `java.lang.*`, `java.util.*` |
| Android | `android.*`, `androidx.*` |
| .NET runtime | `System.*`, `Microsoft.*` |
| Node.js internals | `node:*`, `internal/*` |
| React / Angular | `react-dom`, `@angular/*` |

!!! note "Framework code is not where your mistake is"
    When a stack trace shows `java.lang.Integer.parseInt()` throwing an exception, the exception occurs inside library code — but the mistake is in *your* code that called `parseInt()` with an invalid argument. Always navigate to your own code first.

---

## ◈ IV. Using the Stack Trace as a Map

Once you understand the structure of a stack trace, it becomes more than a record of failure — it is a **map of the execution path** that led to the crash.

Each frame in the trace is a location you can navigate to. Each location shows you what was known and what was happening at that moment. Working through the frames from top to bottom lets you trace the flow of data through the system and identify where the incorrect value or unexpected state originated.

### A Worked Investigation

**Scenario:** A TypeScript e-commerce app throws `TypeError: Cannot read properties of undefined (reading 'price')` when a promo code is applied.

**Step I — Read the trace:**
```
TypeError: Cannot read properties of undefined (reading 'price')
    at calculateTotal (cart.ts:34)
    at applyPromoCode (cart.ts:58)
    at checkout (checkout.ts:22)
```

**Step II — Navigate to the crash site (`cart.ts:34`):**
```typescript
// cart.ts, line 34
function calculateTotal(items: CartItem[]): number {
    return items.reduce((sum, item) => sum + item.price * item.quantity, 0); // (1)
}
```
1. The `.price` access is here. `item` is undefined. One of the items in the array is `undefined`.

**Step III — Navigate to the caller (`cart.ts:58`):**
```typescript
// cart.ts, line 58
function applyPromoCode(code: string): void {
    const discountedItems = cart.items.map(item =>
        item.sku === promoItem ? applyDiscount(item) : item
    );
    const total = calculateTotal(discountedItems); // passes the mapped array
}
```
The mapped array is passed to `calculateTotal()`. If `applyDiscount()` returns `undefined` for a non-matching item, that `undefined` ends up in the array.

**Step IV — Check `applyDiscount()`:**
```typescript
function applyDiscount(item: CartItem): CartItem {
    // Bug: missing return statement for non-discountable items
    if (item.discountable) {
        return { ...item, price: item.price * 0.9 };
    }
    // Returns undefined implicitly when item is not discountable
}
```

**Root cause found:** `applyDiscount()` returns `undefined` for items that are not discountable. The fix: add a `return item;` for the non-discountable path.

The stack trace was the map. The investigation was the process of following it.

---

## ◈ V. Stack Traces in the IDE

You do not need to read stack traces in raw text. Every major IDE makes them navigable:

=== "⌨ IntelliJ / Android Studio"
    In the **Run** or **Debug** output panel, stack trace lines appear as blue hyperlinks. Click any line referencing your code to navigate directly to that file and line in the editor.

=== "⌨ Visual Studio"
    In the **Output** window and **Exception Helper** dialog, file references are clickable links. Double-clicking navigates to the source.

=== "⌨ VS Code"
    In the **Terminal** and **Debug Console**, file paths in stack traces are clickable. The editor navigates to the exact line.

=== "⌨ Android Studio Logcat"
    In the Logcat panel, lines containing your package name are shown as blue hyperlinks. Click to navigate directly to the source file.

---

*Continue to: [IDE-Specific Notes →](ide_specific_notes.md)*

*← Back to: [Section Index](index.md)*

---

*[IDE]: Integrated Development Environment
*[JVM]: Java Virtual Machine
*[PID]: Process Identifier
