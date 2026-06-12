# Appendix G · Platform Notes — Android Studio / Logcat

---

## ⌨ About This Page

This page is a deep reference for debugging in Android Studio, with a focus on Kotlin development. It expands on the brief orientation given in [IDE-Specific Notes](../02_investigation/ide_specific_notes.md) with step-by-step setup instructions, detailed Logcat usage, and Android-specific debugging scenarios.

If you are new to Android Studio's debugger entirely, work through this page alongside [Using a Debugger](../02_investigation/using_a_debugger.md), which covers the underlying concepts in detail.

---

## ⌨ I. Logcat — Reading Runtime Output

Logcat is the primary window into what your running Android application is doing. Every crash, every `Log` statement, and every system event related to your app appears here.

### Opening Logcat

**View → Tool Windows → Logcat**, or click the **Logcat** tab at the bottom of the IDE.

### Anatomy of a Logcat Line

```
2026-06-11 09:42:17.331  8421-8421  MainActivity  com.example.app  D  onCreate: Activity started
└─────────┬─────────┘  └────┬───┘  └─────┬────┘  └──────┬──────┘  │  └────────┬────────┘
      Timestamp        PID-TID         Tag         Package      Level      Message
```

| Field | Meaning |
|-------|---------|
| Timestamp | When the log line was generated |
| PID-TID | Process ID and Thread ID |
| Tag | The string passed as the first argument to `Log.d(TAG, ...)` |
| Package | Your application's package name |
| Level | `V` (Verbose), `D` (Debug), `I` (Info), `W` (Warning), `E` (Error), `A` (Assert) |
| Message | The actual log content |

### Filtering Logcat

Logcat can produce thousands of lines per second from the system and other apps. Filtering is essential.

**By package name:** Use the package dropdown at the top of Logcat to show only your application's process.

**By log level:** Use the level dropdown to show only `Warn` and above, or `Error` and above, when you only care about problems.

**By tag:** Type your tag into the search box, e.g., `tag:MainActivity`. Combine with `package:mine` to scope to your app.

**By regex:** Logcat search supports regular expressions. To find all logs from a specific class and its nested classes: `MainActivity|MainActivity\$.*`

!!! tip "Use a consistent TAG constant"
    ```kotlin
    class ProfileFragment : Fragment() {
        companion object {
            private const val TAG = "ProfileFragment"
        }

        override fun onCreate(savedInstanceState: Bundle?) {
            super.onCreate(savedInstanceState)
            Log.d(TAG, "onCreate called")
        }
    }
    ```
    With a consistent `TAG` per class, filtering Logcat by `tag:ProfileFragment` shows you exactly the output from that component, with nothing else mixed in.

### Saving and Sharing Logcat Output

To save Logcat output for a bug report: click the **save icon** in the Logcat toolbar to export the current view to a `.txt` file. This is far more useful in a bug report than a screenshot — the text can be searched, copied, and read by AI tools.

---

## ⌨ II. Reading a Crash in Logcat

When an app crashes, Logcat shows a `FATAL EXCEPTION` block. Here is how to read it efficiently:

```
FATAL EXCEPTION: main
Process: com.example.shopapp, PID: 14821
java.lang.NullPointerException: Attempt to invoke virtual method
'void android.widget.TextView.setText(java.lang.CharSequence)'
on a null object reference
    at com.example.shopapp.ui.ProductFragment.displayProduct(ProductFragment.kt:58)
    at com.example.shopapp.ui.ProductFragment.onViewCreated(ProductFragment.kt:31)
    at androidx.fragment.app.Fragment.performViewCreated(Fragment.java:3112)
```

**Step-by-step reading:**

I. **`FATAL EXCEPTION: main`** — the crash happened on the main (UI) thread. The app will terminate.

II. **`Process: com.example.shopapp, PID: 14821`** — confirms which app crashed and its process ID. Useful when multiple apps are running on the emulator.

III. **Exception type and message** — `NullPointerException`, with the specific method (`setText`) and object type (`TextView`) that was null.

IV. **Stack trace — find your package name first.** `com.example.shopapp.ui.ProductFragment.displayProduct(ProductFragment.kt:58)` is your code. This is where to start.

V. **Lines starting with `androidx.` or `android.`** are framework code. They show *how* your code was called, but the mistake is not there.

!!! tip "Click the blue links"
    In Logcat, lines that reference your code appear as clickable blue links. Clicking one navigates directly to that file and line in the editor — no need to search manually.

---

## ⌨ III. Setting Up a Debug Session

### Starting in Debug Mode

Click the **bug icon** (🐛) in the toolbar instead of the green play button — or use **Run → Debug 'app'**. The app installs and launches on the emulator or connected device with the debugger attached.

### Setting Breakpoints

Click in the **gutter** (the grey area to the left of the line numbers) next to the line where you want execution to pause. A red circle appears.

### Conditional Breakpoints

Right-click the red breakpoint circle → a small panel appears with a **Condition** field. Enter any valid Kotlin boolean expression, e.g.:

```kotlin
position == 5
user?.id == "usr_4821"
items.size > 100
```

The breakpoint will only pause execution when this expression evaluates to `true`.

### Logpoints (Breakpoints That Don't Pause)

Right-click a breakpoint → check **"Suspend"** off, and instead enable **"Evaluate and log"**. Enter an expression to log. This behaves like a `Log.d()` statement that you can add and remove without editing the source code — useful for quick checks without modifying files.

---

## ⌨ IV. The Debug Panel

When execution pauses at a breakpoint, the **Debug** panel opens at the bottom of the IDE with several tabs:

| Tab | Purpose |
|-----|---------|
| **Frames** | The call stack — click any frame to inspect that context |
| **Variables** | All variables in scope, with current values; expandable for objects |
| **Watches** | Custom expressions you add — click `+` to add one |
| **Console** | Standard output — `println()` and uncaught output |

### Stepping Controls

| Icon / Shortcut | Action |
|-----------------|--------|
| `F8` | Step Over |
| `F7` | Step Into |
| `Shift+F8` | Step Out |
| `F9` | Resume Program |
| `Alt+F9` | Run to Cursor |
| `Alt+F8` | Evaluate Expression |
| `Ctrl+F8` | Toggle breakpoint at current line |

### Inline Variable Values

Android Studio (built on IntelliJ) displays variable values **inline in the editor** during a debug session — directly next to the line of code, in a coloured annotation. This gives immediate visual feedback without needing to check the Variables panel for simple cases.

### Drop Frame

If you stepped past the point you wanted to examine, the **Drop Frame** button (in the Debug toolbar) rewinds execution to the start of the current method call — without restarting the entire app. Available when debugging Kotlin and Java code.

---

## ⌨ V. Debugging Common Android Scenarios

### Scenario: NullPointerException on a View

```
java.lang.NullPointerException: Attempt to invoke virtual method
'void android.widget.TextView.setText(java.lang.CharSequence)'
on a null object reference
    at com.example.app.MainActivity.onCreate(MainActivity.kt:24)
```

**Likely causes, in order of likelihood:**

I. `findViewById()` was called before `setContentView()`.

II. The ID used in `findViewById()` does not exist in the layout file currently set — check that the correct layout is inflated.

III. With View Binding or Data Binding: the binding object is being accessed before `onCreateView()` has run, or after the view has been destroyed (`onDestroyView()`).

**Investigative approach:** Set a breakpoint at the line that crashes. When paused, check the Variables panel — is the view object `null`? Step back through `onCreate()` / `onViewCreated()` to confirm the order of initialisation.

---

### Scenario: App Crashes Only on Rotation

This is almost always a **lifecycle** issue — `onCreate()` runs again after rotation, and state held in instance variables (not saved/restored) is lost.

**Investigative approach:**

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    Log.d(TAG, "onCreate: savedInstanceState=${savedInstanceState != null}")
}

override fun onSaveInstanceState(outState: Bundle) {
    super.onSaveInstanceState(outState)
    Log.d(TAG, "onSaveInstanceState called")
    outState.putString("KEY_DATA", currentData)
}
```

Rotate the device or emulator (`Ctrl+F11` / `Ctrl+F12` in the emulator) and observe the Logcat output. Confirm whether `onSaveInstanceState()` is called before destruction, and whether `savedInstanceState` is non-null in the new `onCreate()`.

---

### Scenario: NetworkOnMainThreadException

```
android.os.NetworkOnMainThreadException
    at com.example.app.data.ApiClient.fetchData(ApiClient.kt:18)
```

Android prohibits network calls on the main (UI) thread to prevent the UI from freezing. The fix involves moving the network call to a background context — a coroutine with `Dispatchers.IO`, for example:

```kotlin
viewModelScope.launch(Dispatchers.IO) {
    val result = apiClient.fetchData()
    withContext(Dispatchers.Main) {
        updateUi(result)
    }
}
```

---

### Scenario: Fragment Not Attached / IllegalStateException

```
java.lang.IllegalStateException: Fragment ProfileFragment not attached to a context.
```

This occurs when a Fragment method that requires the Activity context is called after the Fragment has been detached — commonly inside an asynchronous callback that completes after the user has navigated away.

**Investigative approach:** Check whether the callback checks `isAdded` or uses `viewLifecycleOwner` for lifecycle-aware operations:

```kotlin
viewModel.data.observe(viewLifecycleOwner) { result ->
    if (isAdded) {
        updateUi(result)
    }
}
```

---

## ⌨ VI. The Layout Inspector

For UI bugs that are not crashes — a view is in the wrong position, the wrong size, or not visible — the **Layout Inspector** (**Tools → Layout Inspector**) shows a live, interactive 3D view of the rendered layout hierarchy, with the actual measured dimensions, padding, and properties of each view at runtime.

This is the appropriate tool when the question is *"why does this view look wrong?"* rather than *"why did the code crash?"*

---

## ⌨ VII. The Android Profiler

For performance issues — slow scrolling, high memory usage, battery drain — **View → Tool Windows → Profiler** opens real-time graphs of CPU, memory, network, and energy usage. See [Debugging Tools Overview](../04_tools_techniques/tools_overview.md) for when profiling is the appropriate approach.

---

*← Back to: [Appendix H · Acknowledgments & References](references.md)*

*Continue to: [Appendix G · Platform Notes — VS Code / Terminal →](platform_vscode.md)*

*← Back to: [Appendices Overview](index.md)*

---

*[IDE]: Integrated Development Environment
*[PID]: Process Identifier
*[TID]: Thread Identifier
*[UI]: User Interface
