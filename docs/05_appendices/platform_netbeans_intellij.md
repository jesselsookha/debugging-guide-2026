# Appendix G · Platform Notes — NetBeans / IntelliJ IDEA

---

## ⌨ About This Page

This page is a deep reference for debugging in IntelliJ IDEA and NetBeans, with a focus on Java development. It expands on the brief orientation given in [IDE-Specific Notes](../02_investigation/ide_specific_notes.md).

IntelliJ IDEA and Android Studio share the same underlying platform (IntelliJ Platform), so much of the IntelliJ guidance here also applies directly to Android Studio's Java/Kotlin debugging — see [Appendix G · Android Studio](platform_android_studio.md) for Android-specific additions (Logcat, Layout Inspector).

---

## ⌨ I. IntelliJ IDEA

### The Run and Debug Output

**Run** output (bottom panel) shows console output — `System.out.println()`, exceptions, and program output — when running normally.

**Debug** output (bottom panel, when running in debug mode) shows the same console output **plus** the Debug tabs: Frames, Variables, Watches.

### Reading a Java Exception in the Console

```
Exception in thread "main" java.lang.ArithmeticException: / by zero
	at com.example.Calculator.divide(Calculator.java:14)
	at com.example.Main.main(Main.java:8)

Process finished with exit code 1
```

In IntelliJ, lines like `at com.example.Calculator.divide(Calculator.java:14)` are clickable hyperlinks (shown underlined and in a different colour). Click to navigate directly to that line.

### Setting Breakpoints

Click in the gutter to the left of the line number. A red circle appears.

**Starting a debug session:** Click the **debug icon** (bug-shaped) next to the run configuration dropdown, or right-click the file/class containing `main()` and select **Debug**.

### Conditional Breakpoints

Right-click the breakpoint circle → a small editing panel appears directly below the line:

- **Condition:** any valid Java/Kotlin boolean expression, e.g., `i == 99`, `user.getId().equals("usr_123")`
- **Log message to console**: print a custom message when the breakpoint is hit
- **Remove once hit**: automatically disable after the first trigger — useful for one-time investigation in a loop

---

## ⌨ II. The Debug Tool Window

When paused at a breakpoint, the **Debug** tool window opens with these tabs:

| Tab | Purpose |
|-----|---------|
| **Frames** | The call stack — click any frame to see that method's local variables |
| **Variables** | All variables in scope at the current frame — expandable for objects, arrays, collections |
| **Watches** | Custom expressions — click `+`, type any expression |
| **Console** | Standard output |

### Stepping Controls

| Shortcut | Action |
|----------|--------|
| `F8` | Step Over |
| `F7` | Step Into |
| `Alt+Shift+F7` | Force Step Into (steps into library/framework code, normally skipped) |
| `Shift+F8` | Step Out |
| `F9` | Resume Program |
| `Alt+F9` | Run to Cursor |
| `Alt+F8` | Evaluate Expression |
| `Ctrl+F8` | Toggle breakpoint |
| `Ctrl+Shift+F8` | View all breakpoints |

### Force Step Into

By default, Step Into skips over standard library code (`java.util.*`, `java.lang.*`) to avoid stepping through code you almost never need to inspect. **Force Step Into** (`Alt+Shift+F7`) overrides this — useful in the rare case where the bug genuinely is in how a library method behaves with your specific arguments.

### Inline Variable Values

IntelliJ displays the current value of each variable directly in the editor, as a greyed-out annotation at the end of the line, during a debug session:

```kotlin
val total = price * quantity   // total = 170
```

This updates live as you step, giving immediate visibility without checking the Variables panel for simple cases.

### Drop Frame

The **Drop Frame** button in the debug toolbar rewinds execution to the beginning of the currently selected method call — without restarting the program. If you stepped past the interesting part, Drop Frame lets you go back and step through it again with a different focus (e.g., using Step Into this time instead of Step Over).

---

## ⌨ III. Evaluate Expression

`Alt+F8` opens the **Evaluate Expression** dialog. Type any valid expression in the current language, evaluated in the context of the paused frame:

```java
order.getItems().size()
order.getTotal() > 100
items.stream().filter(i -> i.getPrice() > 50).count()
```

The **Evaluate** button shows the result immediately. The dialog can also execute statements — including ones that change object state — useful for testing a fix's effect interactively before applying it to the source.

---

## ⌨ IV. NetBeans

NetBeans uses similar concepts with different shortcuts and panel names.

### Setting Breakpoints

Click in the left margin next to the line number. A red square or stop-sign icon appears.

### Starting a Debug Session

**Debug → Debug Project** (`Ctrl+F5`), or right-click the project → **Debug**.

### Key Windows

| Window | How to Open | Purpose |
|--------|-------------|---------|
| **Variables** | Window → Debugging → Variables | All variables in scope at the current breakpoint |
| **Call Stack** | Window → Debugging → Call Stack | The active call chain |
| **Breakpoints** | Window → Debugging → Breakpoints | All breakpoints — manage conditions and enable/disable |
| **Output** | Window → Output | Console output and program messages |

### Stepping Controls

| Shortcut | Action |
|----------|--------|
| `F8` | Step Over |
| `F7` | Step Into |
| `Ctrl+F7` | Step Out |
| `Ctrl+F5` | Continue (Resume) |
| `Ctrl+F4` | Finish Debugger Session |

### Conditional Breakpoints in NetBeans

Right-click the breakpoint marker → **Breakpoint Properties** → check **Condition** and enter a boolean expression. Same concept as IntelliJ — pause only when the expression is true.

### Evaluating Expressions

While paused, **Window → Debugging → Evaluate Variables** opens a panel where expressions can be typed and evaluated in the current context.

---

## ⌨ V. Common Java Debugging Scenarios

### Scenario: ConcurrentModificationException

```
java.lang.ConcurrentModificationException
    at java.base/java.util.ArrayList$Itr.checkForComodification(ArrayList.java:1013)
    at java.base/java.util.ArrayList$Itr.next(ArrayList.java:967)
    at com.example.OrderService.removeExpiredItems(OrderService.java:22)
```

This occurs when a collection is modified (an item added or removed) while it is being iterated with a `for-each` loop.

```java
// Causes ConcurrentModificationException
for (Item item : items) {
    if (item.isExpired()) {
        items.remove(item);  // modifying during iteration
    }
}

// Fix: use an Iterator's remove() method
Iterator<Item> it = items.iterator();
while (it.hasNext()) {
    Item item = it.next();
    if (item.isExpired()) {
        it.remove();  // safe — the iterator knows about this removal
    }
}

// Or: collect items to remove, then remove after the loop
items.removeIf(Item::isExpired);
```

### Scenario: ClassCastException

```
java.lang.ClassCastException: class java.lang.String cannot be cast to class java.lang.Integer
    at com.example.DataProcessor.process(DataProcessor.java:31)
```

A value was cast to a type it does not actually have at runtime — common when working with raw types, `Object`, or deserialised data (e.g., from JSON or a generic collection without type safety).

**Investigative approach:** Set a breakpoint just before the cast. Use Evaluate Expression to check the actual runtime type: `value.getClass().getName()`. Compare with the type being cast to.

### Scenario: StackOverflowError from Recursion

```
Exception in thread "main" java.lang.StackOverflowError
    at com.example.TreeNode.countNodes(TreeNode.java:15)
    at com.example.TreeNode.countNodes(TreeNode.java:15)
    at com.example.TreeNode.countNodes(TreeNode.java:15)
    ... (repeats many times)
```

The same line repeating many times indicates infinite or excessively deep recursion. Set a breakpoint at the recursive call with a **hit count condition** (e.g., pause after 100 hits) to inspect the state at a deep point in the recursion — checking whether the base case condition is ever actually met.

```java
// Missing or incorrect base case
public int countNodes(TreeNode node) {
    return 1 + countNodes(node.left) + countNodes(node.right);
    // Missing: if (node == null) return 0;
}
```

---

*← Back to: [Appendix G · Platform Notes — Visual Studio / Watch Windows](platform_visual_studio.md)*

*Continue to: [Appendix G · Platform Notes — Web / Browser DevTools →](platform_browser_devtools.md)*

*← Back to: [Appendices Overview](index.md)*

---

*[IDE]: Integrated Development Environment
*[JSON]: JavaScript Object Notation
