# ◆ Debugging in Systems

---

## ◆ The Shift From Code to System

At the investigative level, a bug can usually be traced to a specific line, a specific method, a specific file. The problem has a location. The fix has a location. The scope of the investigation is bounded.

At the strategic level, this is no longer reliable. A crash in the UI may originate from a decision made in the data layer. An incorrect value on screen may reflect a network response parsed three methods earlier. An application that behaves inconsistently may be responding to state that was set during a previous session, a previous screen, or a previous user interaction — none of which are visible at the point of the failure.

These are **system-level bugs**: failures that cannot be understood by examining a single component in isolation, because their cause and effect exist in different parts of the system.

Understanding them requires a different frame of reasoning:

> *"Which layer of this system is responsible for this behaviour?"*

---

## ◆ I. Framework-Driven Behaviour

Modern applications are built on frameworks — Android, React, Angular, ASP.NET, Spring — that control significant portions of the application's execution flow. The framework decides when lifecycle methods are called, when state is initialised, when views are rendered, and when resources are released.

A common source of strategic-level bugs is the programmer's assumption that their code controls execution, when in practice the framework does.

### Recognising Framework-Driven Bugs

The signs of a framework-driven bug:

- Code that appears correct in isolation but behaves unexpectedly in the application
- Behaviour that changes depending on how the screen was navigated to
- Methods that appear not to be called, or to be called in an unexpected order
- State that is present on first load but absent on subsequent loads

!!! example "Framework execution — Android Activity lifecycle"
    A student writes an Android app where a variable is initialised in `onCreate()`. The app crashes only when the device is rotated.

    The cause: Android destroys and recreates the Activity on rotation. `onCreate()` is called again — but the variable that was populated from user input (held in a field, not in a `Bundle` or `ViewModel`) was not preserved across the recreation. The code is correct. The assumption about when it runs is not.

    **Strategic lesson:** When working with a framework, know its lifecycle. Debug the execution sequence, not just the code.

### Debugging Framework Behaviour

I. **Read the framework's documentation for the lifecycle event you are working in.** Many framework bugs are caused by code placed in the wrong lifecycle method — not by logic errors in the code itself.

II. **Add entry logs to all lifecycle methods** to confirm the actual execution order:

=== "Kotlin / Android"
    ```kotlin
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        Log.d(TAG, "onCreate: savedInstanceState=${savedInstanceState != null}")
    }

    override fun onStart()  { super.onStart();  Log.d(TAG, "onStart") }
    override fun onResume() { super.onResume(); Log.d(TAG, "onResume") }
    override fun onPause()  { super.onPause();  Log.d(TAG, "onPause") }
    override fun onStop()   { super.onStop();   Log.d(TAG, "onStop") }
    override fun onDestroy(){ super.onDestroy();Log.d(TAG, "onDestroy") }
    ```

=== "C# / ASP.NET"
    ```csharp
    // Middleware pipeline — log each stage
    app.Use(async (context, next) =>
    {
        Console.WriteLine($"[Pipeline] Request start: {context.Request.Path}");
        await next(context);
        Console.WriteLine($"[Pipeline] Request end: {context.Response.StatusCode}");
    });
    ```

=== "JavaScript / React"
    ```typescript
    useEffect(() => {
        console.log('[useEffect] Component mounted or deps changed:', { userId, productId });
        return () => {
            console.log('[useEffect] Cleanup running');
        };
    }, [userId, productId]);
    ```

III. **Compare your assumed execution order with the actual logged order.** The first point of divergence is where to investigate.

---

## ◆ II. Lifecycle Issues

Lifecycle issues are a specific class of framework-driven bugs. They occur when code that depends on a resource, a value, or a state is executed at a point in the lifecycle when that resource, value, or state is not yet available — or is no longer valid.

### Common Lifecycle Bug Patterns

**Initialisation before the resource is ready:**
A view is accessed before the layout has been inflated. A database is queried before the connection is established. A network result is used before the response has returned.

**Stale state on re-entry:**
A screen loads correctly the first time. When navigated back to, it shows data from the previous visit because the data was loaded in a lifecycle method that does not fire on re-entry.

**Cleanup that runs too early or not at all:**
A resource (file handle, network connection, listener) is released before all operations that use it are complete — or is never released, causing a memory leak or an error on the next initialisation.

!!! warning "The two most common lifecycle mistakes"
    **Too early:** Calling a method on a view or object before it has been created. Common in Android when `findViewById()` is called before `setContentView()`, or in React when a `ref` is accessed before the component mounts.

    **Too late:** Holding a reference to a destroyed component. Common when a callback fires after the Activity or Fragment it references has been destroyed — the so-called "stale reference" bug.

### Debugging Lifecycle Issues

The investigative question is: **at the moment this code executes, what is guaranteed to exist?**

Work through the lifecycle sequence for the framework you are using and confirm:

- Was the resource initialised before this method runs?
- Is this method called every time the screen is shown, or only on the first creation?
- Is the cleanup method guaranteed to run before the next initialisation?

---

## ◆ III. State Management Failures

State management bugs are some of the most difficult to diagnose because the failure is not in the logic of any individual component — it is in how state flows between components, how it is updated, and how it is read.

### What State Management Bugs Look Like

- A screen shows the correct data on first load, then shows stale data after an update elsewhere in the application
- Two components that should be synchronised show different values
- An action in one part of the application unexpectedly affects the behaviour of a different, apparently unrelated part
- Data is lost on navigation, rotation, or backgrounding the application

### The Core Question

> *"Who owns this state? Who updates it? Who reads it? In what order do these things happen?"*

If no clear answer exists, the state management design itself is the source of the bug.

!!! example "State management — a common scenario"
    A mobile shopping app shows a cart item count in the navigation bar. When items are added from the product list, the count updates correctly. When items are removed from the cart screen, the count does not update until the app is restarted.

    **Diagnosis:** The cart screen updates the data layer directly but does not notify the UI component that owns the count display. The count display reads from a cached value that is not invalidated on removal.

    **Root cause:** Two components have independent copies of cart state. There is no single source of truth. The fix requires either a shared observable state (e.g., ViewModel with LiveData or StateFlow in Android) or an event system that notifies the count display when the underlying data changes.

### Debugging State Management

I. Map the data flow. Draw (or write down) which components read and write the piece of state that is behaving incorrectly.

II. Add logs at every read and write point. Confirm the sequence of operations.

III. Identify whether there is a single authoritative source for the state, or whether copies exist. Multiple copies without synchronisation are almost always the root cause.

---

## ◆ IV. Configuration, Environment, and Dependencies

A significant class of production bugs has no error in the code at all. The code is correct. The environment is wrong.

Configuration bugs include:

- API keys, database connection strings, or environment variables that are missing, wrong, or not loaded
- Feature flags that are enabled in development but disabled in production, or vice versa
- Dependency version mismatches between what the developer used locally and what is installed in the deployed environment
- Differences in operating system, file system, or locale between development and production

!!! warning "The classic symptom"
    *"It works on my machine."*

    When this is heard in a team context, the investigation should immediately focus on differences between the working environment and the failing environment — not on the code itself.

### Debugging Configuration and Environment Issues

**Step I — Isolate the environment difference.** What is different between the environment where it works and the environment where it fails?

**Step II — Check configuration values.** Log or print the actual value of every relevant configuration item at startup. Do not assume the configuration file was read correctly.

=== "Java / Spring Boot"
    ```java
    @Value("${api.base-url}")
    private String apiBaseUrl;

    @PostConstruct
    public void logConfig() {
        log.info("Config loaded: apiBaseUrl={}", apiBaseUrl);
        // If this prints 'null' or the placeholder, the property was not resolved
    }
    ```

=== "C# / .NET"
    ```csharp
    public Startup(IConfiguration configuration)
    {
        Console.WriteLine($"[Config] ConnectionString={configuration.GetConnectionString("Default")}");
        Console.WriteLine($"[Config] Environment={Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT")}");
    }
    ```

=== "JavaScript / Node.js"
    ```typescript
    console.log('[Config] API_URL:', process.env.API_URL);
    console.log('[Config] NODE_ENV:', process.env.NODE_ENV);
    // If undefined: the .env file was not loaded, or the variable name is wrong
    ```

**Step III — Check dependency versions.** Compare lock files (`package-lock.json`, `pom.xml`, `build.gradle`, `.csproj`) between the working and failing environments. A dependency update can introduce breaking changes without any code change.

**Step IV — Reproduce the failing environment locally if possible.** The most reliable way to debug an environment-specific issue is to reproduce the failing environment rather than guessing from the working one.

---

## ◆ V. Non-Crashing Failures

Not all bugs produce exceptions. Some of the most significant and damaging bugs produce no error at all — the program runs to completion, returns a result, and nobody knows it is wrong until a user notices, a test catches it, or the consequences compound over time.

### Categories of Non-Crashing Failures

**Wrong output:** The program produces a result that is logically incorrect but not an exception. Wrong calculations, incorrect sort orders, truncated strings, off-by-one errors in pagination.

**Performance failures:** The program is functionally correct but too slow to be usable. Queries that scan entire tables, algorithms with quadratic complexity on large inputs, memory allocations that are never released.

**User-reported failures:** Users report behaviour that developers cannot reproduce — because the failure depends on specific user data, specific device configurations, or specific usage sequences that are not exercised in standard testing.

**Silent data corruption:** The program writes incorrect data to a database, file, or API without failing. This class of bug is among the most serious because its effects can propagate before detection.

!!! danger "Silent failures are the most dangerous"
    A crash is visible immediately. A wrong calculation, a corrupted record, or a silently swallowed error may not be discovered until hours, days, or weeks later — by which time the impact has multiplied and the original cause is harder to trace.

    Defensive programming practices (assertions, input validation, structured logging of all write operations) exist specifically to make silent failures visible before their effects compound. See [Preventive Measures](preventive_measures.md).

### Debugging Non-Crashing Failures

**Start with the output.** What specifically is wrong? Quantify it. Is it always wrong by the same amount? Is it wrong only for certain inputs? Is the pattern consistent?

**Work backward from the wrong value.** If the wrong value is on the screen, where does it come from? Trace upstream through the data flow until you find where the value diverges from what it should be.

**Use test cases with known correct answers.** Write a test — even informally — that provides a specific input for which you can manually calculate the correct output. Verify that the program produces it. If it does not, you have a reproducible case to investigate. If it does, the failure is input-dependent and you need to identify what is different about the failing inputs.

---

*Continue to: [Professional Debugging Practices →](professional_practices.md)*

*← Back to: [Section Index](index.md)*

---

*[UI]: User Interface
*[API]: Application Programming Interface
*[TDD]: Test-Driven Development
*[IDE]: Integrated Development Environment
