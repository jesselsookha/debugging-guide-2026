# Appendix G · Platform Notes — VS Code / Terminal

---

## ⌨ About This Page

This page is a deep reference for debugging in Visual Studio Code, with a focus on JavaScript, TypeScript, and Node.js development — including React and React Native projects. It expands on the brief orientation given in [IDE-Specific Notes](../02_investigation/ide_specific_notes.md).

VS Code's debugging capabilities depend heavily on the language extensions installed and the `launch.json` configuration. This page covers the setup process as well as the debugging workflow itself.

---

## ⌨ I. The Terminal — Your First Source of Truth

For most JavaScript, TypeScript, and Node.js projects, the **Terminal** panel (`` Ctrl+` ``) is where the majority of error information appears — both during development (`npm run dev`, `npm start`) and when running scripts directly.

### Reading Terminal Output

When a Node.js application crashes, the terminal shows the error and stack trace directly:

```
TypeError: Cannot read properties of undefined (reading 'price')
    at calculateTotal (/Users/dev/project/src/cart.ts:34:32)
    at applyPromoCode (/Users/dev/project/src/cart.ts:58:18)
    at Object.<anonymous> (/Users/dev/project/src/main.ts:7:1)
```

Unlike Android's Logcat, Node.js stack traces show **full file paths**. In VS Code, these paths are clickable — `Ctrl+Click` (or `Cmd+Click` on macOS) navigates directly to the file and line.

### Common Terminal Patterns

**Build/compile errors (TypeScript):**
```
src/cart.ts:31:18 - error TS2339:
Property 'unitPrice' does not exist on type 'CartItem'.

31     total += item.unitPrice * item.quantity;
                     ~~~~~~~~~
```
TypeScript errors include the file, line, column, error code, description, and a visual indicator (`~~~~~`) pointing at the exact problematic token.

**Module resolution errors:**
```
Error: Cannot find module './utils/formatter'
Require stack:
- /Users/dev/project/src/index.js
```
This indicates a file path is incorrect, the file does not exist, or the file extension is missing in an import where it is required.

**Unhandled promise rejections:**
```
(node:18234) UnhandledPromiseRejectionWarning: Error: Network request failed
(node:18234) UnhandledPromiseRejectionWarning: Unhandled promise rejection.
This error originated either by throwing inside of an async function
without a catch block, or by rejecting a promise which was not handled
with .catch().
```
This means an `async` function threw an error that nothing caught. Find the `async` function in the stack trace and add a `try/catch` or `.catch()`.

---

## ⌨ II. The Problems Panel

**View → Problems** (or `Ctrl+Shift+M`) aggregates all errors and warnings detected by TypeScript, ESLint, and other configured tools — without running the code.

This is your **static analysis** view. Issues here are detected as you type, before you ever run the program. Red squiggly underlines in the editor correspond to entries here.

!!! tip "Fix Problems panel issues before running"
    A TypeScript type error or an ESLint error shown in the Problems panel often explains a runtime error before it occurs. If the Problems panel shows `Property 'X' does not exist on type 'Y'`, runtime code that relies on `X` will fail — fixing the type error first frequently prevents the runtime error entirely.

---

## ⌨ III. Setting Up the Debugger — `launch.json`

VS Code's interactive debugger requires a configuration file at `.vscode/launch.json`. If one does not exist, pressing `F5` prompts VS Code to create one, with options based on the detected project type.

### Example: Node.js Application

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "node",
      "request": "launch",
      "name": "Debug Node App",
      "skipFiles": ["<node_internals>/**"],
      "program": "${workspaceFolder}/src/index.ts",
      "preLaunchTask": "tsc: build - tsconfig.json",
      "outFiles": ["${workspaceFolder}/dist/**/*.js"]
    }
  ]
}
```

### Example: Attaching to a Running Process

For applications already running (e.g., a server started separately), use `"request": "attach"`:

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "node",
      "request": "attach",
      "name": "Attach to Process",
      "port": 9229,
      "restart": true,
      "skipFiles": ["<node_internals>/**"]
    }
  ]
}
```

The Node.js process must be started with `--inspect` (e.g., `node --inspect index.js`) for this to connect.

### Example: Chrome / Browser Debugging

For client-side JavaScript and React applications, the **Debugger for Chrome** (built into VS Code) attaches to a browser instance:

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "chrome",
      "request": "launch",
      "name": "Launch Chrome against localhost",
      "url": "http://localhost:3000",
      "webRoot": "${workspaceFolder}/src"
    }
  ]
}
```

---

## ⌨ IV. The Debug Panel and Stepping

Once a debug session starts (`F5`), the **Run and Debug** sidebar (`Ctrl+Shift+D`) shows:

| Panel | Purpose |
|-------|---------|
| **Variables** | Local, closure, and global scope variables at the current breakpoint |
| **Watch** | Custom expressions — click `+` to add |
| **Call Stack** | The active call chain — click any frame to inspect that context |
| **Breakpoints** | All breakpoints across the project — toggle individually |

### Stepping Controls

| Button | Shortcut | Action |
|--------|----------|--------|
| Continue | `F5` | Run to next breakpoint |
| Step Over | `F10` | Next line, stay in current function |
| Step Into | `F11` | Enter the called function |
| Step Out | `Shift+F11` | Finish current function, return to caller |
| Restart | `Ctrl+Shift+F5` | Restart the debug session |
| Stop | `Shift+F5` | End the debug session |

### Conditional Breakpoints

Right-click a breakpoint (red dot) → **Edit Breakpoint** → choose:

- **Expression** — pause only when a JavaScript/TypeScript expression evaluates to truthy, e.g., `item.price > 100`
- **Hit Count** — pause only after the breakpoint has been hit a specific number of times, e.g., `>= 5`
- **Log Message** — print a message to the Debug Console without pausing (equivalent to a logpoint)

### The Debug Console

While paused, the **Debug Console** (a tab alongside Terminal) accepts JavaScript/TypeScript expressions evaluated in the current scope:

```
> item.price
85
> item.price * item.quantity
170
> Object.keys(cart)
["items", "total", "discount"]
```

---

## ⌨ V. Debugging React Applications

React applications run in the browser, so debugging combines VS Code's editor with **Browser DevTools** (see [Appendix G · Browser DevTools](platform_browser_devtools.md)).

### React DevTools Extension

Install the **React Developer Tools** browser extension. This adds **Components** and **Profiler** tabs to browser DevTools, allowing you to:

- Inspect the component tree and the props/state of any component
- See which components re-render and why
- Edit state values live to test behaviour

### Common React Debugging Patterns

**Component not re-rendering when expected:**
```typescript
useEffect(() => {
    console.log('[Component] Re-rendered. Dependencies:', { userId, filter });
}, [userId, filter]);
```
If this log does not fire when you expect, the dependency array may be missing a value, or the value may not actually be changing (e.g., a new object reference created on every render that looks "different" but contains the same data).

**State not updating immediately:**
```typescript
const [count, setCount] = useState(0);

const handleClick = () => {
    setCount(count + 1);
    console.log(count); // Still shows the OLD value — state updates are asynchronous
};
```
React state updates are asynchronous and batched. To act on the new value, use a `useEffect` that depends on `count`, or use the functional update form `setCount(prev => prev + 1)`.

---

## ⌨ VI. Debugging React Native

React Native applications run on a device or emulator but execute JavaScript in a separate engine. Debugging requires connecting to that JavaScript context.

**Metro bundler terminal:** The terminal running `npx react-native start` shows JavaScript console output (`console.log`) and bundling errors.

**Device/emulator logs:** For native-level crashes (not JavaScript), use:
- Android: Logcat (see [Appendix G · Android Studio](platform_android_studio.md))
- iOS: Xcode console

**Remote JS debugging:** Shake the device (or `Cmd+D` / `Cmd+M` in the emulator) to open the developer menu, then select **Debug**. This connects the JavaScript context to Chrome DevTools or VS Code, enabling breakpoints in `.tsx`/`.jsx` files.

**Flipper:** A desktop debugging platform for React Native that provides network inspection, layout inspection, and crash reporting in one tool — particularly useful for bugs that involve the bridge between JavaScript and native code.

---

## ⌨ VII. Useful VS Code Extensions for Debugging

| Extension | Purpose |
|-----------|---------|
| **ESLint** | Real-time linting — surfaces potential bugs as you type |
| **Error Lens** | Shows error and warning text inline, next to the code, rather than only in the Problems panel |
| **GitLens** | Adds `git blame` annotations directly in the editor — see who last changed a line and when |
| **REST Client** | Send HTTP requests directly from `.http` files — useful for testing API endpoints during investigation |
| **Thunder Client** | A lightweight Postman alternative built into VS Code |

---

*← Back to: [Appendix G · Platform Notes — Android Studio / Logcat](platform_android_studio.md)*

*Continue to: [Appendix G · Platform Notes — Visual Studio / Watch Windows →](platform_visual_studio.md)*

*← Back to: [Appendices Overview](index.md)*

---

*[IDE]: Integrated Development Environment
*[ESLint]: A static analysis tool for JavaScript and TypeScript
*[DOM]: Document Object Model
*[API]: Application Programming Interface
*[HTTP]: Hypertext Transfer Protocol
