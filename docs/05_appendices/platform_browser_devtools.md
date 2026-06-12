# Appendix G · Platform Notes — Web / Browser DevTools

---

## ⌨ About This Page

This page is a deep reference for debugging web applications using browser developer tools — primarily Chrome DevTools, with notes on Firefox where it differs meaningfully. It expands on the brief orientation given in [IDE-Specific Notes](../02_investigation/ide_specific_notes.md).

Browser DevTools are relevant for any front-end JavaScript, TypeScript, HTML, or CSS work — including React and Angular applications running in the browser. For React Native, see the React Native section in [Appendix G · VS Code](platform_vscode.md).

**Opening DevTools:** `F12`, or `Ctrl+Shift+I` (`Cmd+Option+I` on macOS), or right-click any element → **Inspect**.

---

## ⌨ I. The Console Panel

The Console is where JavaScript errors, warnings, and `console.log()` output appear — the browser's equivalent of the terminal.

### Reading Console Errors

```
Uncaught TypeError: Cannot read properties of null (reading 'addEventListener')
    at main.js:12:23
```

Click the file reference (`main.js:12:23`) to jump directly to the **Sources** panel at that line.

### Console Methods Beyond `console.log()`

| Method | Purpose |
|--------|---------|
| `console.log()` | General output |
| `console.warn()` | Warning — yellow highlight, useful for non-fatal issues |
| `console.error()` | Error — red highlight, includes a stack trace automatically |
| `console.table(data)` | Renders arrays/objects as a formatted table — excellent for arrays of objects |
| `console.group()` / `console.groupEnd()` | Groups related log lines into a collapsible section |
| `console.assert(condition, message)` | Logs `message` only if `condition` is false |
| `console.time(label)` / `console.timeEnd(label)` | Measures elapsed time between two points |

```javascript
console.table(cart.items);
// Renders a table with columns: (index), name, price, quantity

console.group('Checkout process');
console.log('Step 1: validating cart');
console.log('Step 2: applying discounts');
console.groupEnd();

console.time('fetchProducts');
await fetchProducts();
console.timeEnd('fetchProducts');
// Output: fetchProducts: 342.18ms
```

### Filtering the Console

Use the filter box to search by text, or the level dropdown to show only **Errors**, **Warnings**, or specific log levels. The **Default levels** dropdown can hide verbose framework logs that clutter investigation.

### Preserving Log on Navigation

By default, the console clears when the page navigates or reloads. Check **"Preserve log"** (in Console settings, gear icon) to retain output across page reloads — essential when debugging a redirect, a form submission, or any flow that causes a navigation.

---

## ⌨ II. The Sources Panel — Breakpoints and Stepping

The **Sources** panel is the browser's interactive debugger — functionally equivalent to the debuggers in IntelliJ, Visual Studio, or VS Code.

### Setting Breakpoints

Open a file in the Sources panel (use `Ctrl+P` / `Cmd+P` to search for files by name), then click the line number to set a breakpoint.

### Conditional Breakpoints

Right-click a line number → **Add conditional breakpoint** → enter a JavaScript expression. Execution pauses only when the expression is truthy.

### Stepping Controls

| Icon / Shortcut | Action |
|-----------------|--------|
| `F8` / `Ctrl+\` | Resume / Pause |
| `F10` | Step Over next function call |
| `F11` | Step Into next function call |
| `Shift+F11` | Step Out of current function |
| `Ctrl+Click` on a frame in Call Stack | Navigate to that frame's context |

### The Scope Panel

While paused, the **Scope** panel (right sidebar) shows **Local**, **Closure**, and **Global** variables — equivalent to the Variables/Locals panels in other IDEs.

### Watch Expressions

The **Watch** panel (also right sidebar) lets you add custom expressions, evaluated continuously as you step:

```
cart.items.length
cart.total > 0
document.getElementById('checkout-btn').disabled
```

### Logpoints

Right-click a line number → **Add logpoint** → enter an expression to log to the console without pausing execution. This is the DevTools equivalent of a `console.log()` that can be added and removed without editing source files.

---

## ⌨ III. DOM Breakpoints

A capability unique to browser debugging: pausing execution when the **DOM itself** changes — not just when code executes a specific line.

Right-click any element in the **Elements** panel → **Break on** →

| Option | Pauses When |
|--------|-------------|
| **Subtree modifications** | A child element is added, removed, or modified |
| **Attribute modifications** | An attribute (class, style, data-*) on this element changes |
| **Node removal** | This specific element is removed from the DOM |

!!! example "When DOM breakpoints matter"
    A button that should be disabled during a loading state is sometimes clickable. Setting an **Attribute modifications** breakpoint on that button pauses execution at the exact line of JavaScript that changes its `disabled` attribute — showing you precisely when and why the attribute is being set incorrectly, without needing to search the codebase for every place that touches the button.

---

## ⌨ IV. The Network Panel

The **Network** panel captures every HTTP request the page makes — essential for debugging API calls, failed resource loads, and timing issues.

### Reading a Request

Click any request to see tabs:

| Tab | Shows |
|-----|-------|
| **Headers** | Request URL, method, status code, request and response headers |
| **Payload** / **Request** | The body sent with the request (for POST/PUT) |
| **Response** | The raw response body |
| **Preview** | A formatted view of the response (JSON tree, image, etc.) |
| **Timing** | A breakdown of where time was spent — DNS lookup, connection, waiting, downloading |

### Common Network-Related Bugs

**Status code 404:** The requested URL does not exist on the server. Check for typos in the URL, or confirm the API endpoint path matches what the backend actually exposes.

**Status code 401 / 403:** Authentication or authorization failure. Check the **Headers** tab — is an `Authorization` header present and correctly formatted? Has a token expired?

**Status code 500:** Server-side error. The response body (check **Response** tab) may contain a server-side stack trace or error message — this is where backend debugging begins.

**Status code 0 / "Failed" with no status:** Often a **CORS** error (see below), a network connectivity issue, or the request was blocked by a browser extension.

**Request never appears in the Network panel at all:** The JavaScript code that should trigger the request never ran. This is a code logic issue, not a network issue — go back to the Sources panel.

### CORS Errors

```
Access to fetch at 'https://api.example.com/products' from origin
'http://localhost:3000' has been blocked by CORS policy:
No 'Access-Control-Allow-Origin' header is present on the requested resource.
```

This is a **browser security restriction**, not a bug in your JavaScript code. The server at `api.example.com` must include an `Access-Control-Allow-Origin` header permitting requests from your origin. This cannot be fixed from the client side — it requires a server-side configuration change, or a development proxy to route requests through the same origin during local development.

### Filtering the Network Panel

Use the filter bar to show only specific types: `XHR` / `Fetch` (API calls), `JS`, `CSS`, `Img`, `Doc`. The **Preserve log** checkbox (same as Console) retains requests across page navigations.

---

## ⌨ V. The Elements Panel

The **Elements** panel shows the live DOM — not the original HTML source, but the current state after all JavaScript modifications.

### Inspecting Computed Styles

Select an element, then check the **Styles** tab (which CSS rules apply, in order of specificity) and the **Computed** tab (the final resolved value of every CSS property, after all rules are applied).

!!! tip "Computed styles reveal the actual problem"
    When an element's appearance does not match what the CSS file specifies, the **Computed** tab shows what is *actually* being applied — including styles from browser defaults, inherited values, and any rule with higher specificity that is overriding your intended style. The **Styles** tab shows which rule "won" with a strikethrough on overridden values.

### Editing Live

Both the DOM structure and CSS values can be edited directly in the Elements panel. Changes are temporary (lost on reload) but are the fastest way to test a CSS fix before editing the source file.

---

## ⌨ VI. The Application Panel

The **Application** panel (Chrome) / **Storage** panel (Firefox) inspects client-side storage:

| Storage Type | What It Holds |
|--------------|---------------|
| **Local Storage** | Persistent key-value data, survives browser restarts |
| **Session Storage** | Key-value data, cleared when the tab closes |
| **Cookies** | Small data pieces sent with every request to the same domain |
| **IndexedDB** | Structured client-side database for larger data |

**Common use:** A user reports that their saved preferences disappear. Check Local Storage directly — is the data actually being written? Is the key name consistent between the write and read operations? Is storage quota being exceeded (check the size)?

---

## ⌨ VII. Performance Panel

For performance issues — slow rendering, janky scrolling, slow page load — the **Performance** panel records a timeline of everything the browser does: JavaScript execution, rendering, layout, and painting.

**Recording a session:** Click record, perform the slow action, stop recording. The timeline shows where time was spent — a long yellow block indicates JavaScript execution; purple indicates rendering/layout; green indicates painting.

This is the browser equivalent of the profilers covered in [Debugging Tools Overview](../04_tools_techniques/tools_overview.md) — use it when the question is *"why is this slow?"* rather than *"why is this wrong?"*

---

## ⌨ VIII. Firefox DevTools Differences

Firefox DevTools (`F12`) offer equivalent panels with some notable additions:

- **Multi-line console editor**: the Console panel includes a built-in multi-line editor mode for writing longer test snippets
- **CSS Grid and Flexbox inspectors**: visual overlays specifically for debugging Grid and Flexbox layouts, more detailed than Chrome's equivalent
- **Accessibility panel**: inspects the accessibility tree directly — useful when debugging screen-reader or ARIA-related issues

The underlying concepts — Console, Debugger (Sources equivalent), Network, Inspector (Elements equivalent), Storage — map directly between the two browsers.

---

*← Back to: [Appendix G · Platform Notes — NetBeans / IntelliJ](platform_netbeans_intellij.md)*

*← Back to: [Appendices Overview](index.md)*

*You have reached the end of the appendices.*

*Return to: [Home](../index.md)*

---

*[DOM]: Document Object Model
*[HTTP]: Hypertext Transfer Protocol
*[CORS]: Cross-Origin Resource Sharing
*[CSS]: Cascading Style Sheets
*[ARIA]: Accessible Rich Internet Applications
*[API]: Application Programming Interface
*[JSON]: JavaScript Object Notation
