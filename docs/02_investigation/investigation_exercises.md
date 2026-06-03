# ▣ Investigation Exercises

---

## ▣ How to Use These Exercises

These exercises are designed for programmers who can already write working code but need to develop the discipline of systematic investigation. They are not about identifying a single broken line — they are about **reasoning through behaviour**, reading output strategically, and using the tools introduced in this section.

Each exercise asks you to document your process, not just find the answer. The discipline of working through the steps matters as much as the result.

!!! note "Tools you will need"
    Unlike the Foundation Exercises, several of these exercises are best worked with an IDE open. You will be asked to use log statements, set breakpoints, and read stack traces. Have your development environment available for Sets B and C.

---

## ▣ Set A — Reading and Interpreting Stack Traces

For each stack trace below, answer the following questions without looking at any code:

I. What exception type was thrown, and what does it mean?
II. Which file and line in **your code** is closest to the crash?
III. What is the calling chain from the entry point to the crash?
IV. What would you examine first, and why?

---

### A1 — Java: A Chain of Calls

```
Exception in thread "main"
java.lang.ArrayIndexOutOfBoundsException: Index 3 out of bounds for length 3
    at com.example.grades.GradeCalculator.getGrade(GradeCalculator.java:31)
    at com.example.grades.ReportGenerator.buildReport(ReportGenerator.java:54)
    at com.example.grades.ReportGenerator.generateAll(ReportGenerator.java:22)
    at com.example.Main.main(Main.java:9)
```

??? question "Show analysis"
    **I. Exception type:** `ArrayIndexOutOfBoundsException` — an array was accessed at an index that does not exist. Index 3 was used on an array with length 3 (valid indices: 0, 1, 2).

    **II. Your code, closest to crash:** `GradeCalculator.java` line 31, method `getGrade()`.

    **III. Call chain:**
    ```
    main()                         ← entry point (Main.java:9)
      └── generateAll()            ← (ReportGenerator.java:22)
            └── buildReport()      ← (ReportGenerator.java:54)
                  └── getGrade()   ← crash here (GradeCalculator.java:31)
    ```

    **IV. Examine first:** Line 31 of `GradeCalculator.java`. Find the array access. The index being used is 3 on an array of length 3 — off by one. Check whether the loop or index variable controlling access uses `<= length` instead of `< length`.

---

### A2 — Kotlin / Android

```
FATAL EXCEPTION: main
Process: com.example.weatherapp, PID: 7291
java.lang.NullPointerException: Attempt to invoke virtual method
'void android.widget.ImageView.setImageResource(int)'
on a null object reference
    at com.example.weatherapp.ui.WeatherFragment.updateWeatherIcon(WeatherFragment.kt:74)
    at com.example.weatherapp.ui.WeatherFragment.onWeatherDataReceived(WeatherFragment.kt:51)
    at com.example.weatherapp.viewmodel.WeatherViewModel$fetchWeather$1.invoke(WeatherViewModel.kt:38)
    at android.os.Handler.dispatchMessage(Handler.java:106)
    at android.os.Looper.loop(Looper.java:223)
```

??? question "Show analysis"
    **I. Exception type:** `NullPointerException` — `setImageResource()` was called on an `ImageView` that is null. The view reference was not initialised.

    **II. Your code, closest to crash:** `WeatherFragment.kt` line 74, method `updateWeatherIcon()`.

    **III. Call chain (your code only):**
    ```
    WeatherViewModel.fetchWeather()     ← data callback (line 38)
      └── onWeatherDataReceived()       ← (WeatherFragment.kt:51)
            └── updateWeatherIcon()     ← crash here (WeatherFragment.kt:74)
    ```
    The `Handler` and `Looper` lines are Android framework code — not your code.

    **IV. Examine first:** Line 74 of `WeatherFragment.kt`. Find the `ImageView` whose `setImageResource()` is being called. It was declared but never assigned via `findViewById()` or view binding — or `updateWeatherIcon()` is being called before `onViewCreated()` has run, meaning the view hierarchy does not yet exist.

---

### A3 — C#: Multiple Layers

```
System.InvalidOperationException:
Sequence contains no elements
   at System.Linq.ThrowHelper.ThrowNoElementsException()
   at System.Linq.Enumerable.First[TSource](IEnumerable`1 source)
   at MyShop.Data.ProductRepository.GetFeaturedProduct(ProductRepository.cs:line 43)
   at MyShop.Services.HomePageService.BuildHomePage(HomePageService.cs:line 19)
   at MyShop.Controllers.HomeController.Index(HomeController.cs:line 12)
```

??? question "Show analysis"
    **I. Exception type:** `InvalidOperationException: Sequence contains no elements` — `.First()` was called on an empty collection. LINQ's `.First()` throws this exception when no elements match the query or the collection is empty. Use `.FirstOrDefault()` if an empty result is a valid state.

    **II. Your code, closest to crash:** `ProductRepository.cs` line 43, method `GetFeaturedProduct()`.

    **III. Call chain:**
    ```
    HomeController.Index()          ← entry (HomeController.cs:12)
      └── BuildHomePage()           ← (HomePageService.cs:19)
            └── GetFeaturedProduct() ← crash here (ProductRepository.cs:43)
    ```
    The `ThrowHelper` and `Enumerable` lines are .NET runtime code.

    **IV. Examine first:** Line 43 of `ProductRepository.cs`. Find the `.First()` call. The query that precedes it is returning zero results. Either no products in the database are marked as "featured", or the query condition is incorrect. Change `.First()` to `.FirstOrDefault()` and add a null check — then investigate why the query returns nothing.

---

## ▣ Set B — Applying Logging Strategically

These exercises ask you to **write log statements** to answer specific questions about program behaviour. For each scenario, write the log statements you would add, state what question each one answers, and describe what you would conclude if it produced no output at all.

---

### B1 — Java: The Invisible Discount

A discount method is called but the final price shown to the user never changes. The method is never observed to produce errors.

```java
public class PricingService {

    public double applyMemberDiscount(String memberId, double originalPrice) {
        MemberProfile profile = memberRepository.findById(memberId);

        if (profile != null && profile.isActiveMember()) {
            double discounted = originalPrice * (1.0 - profile.getDiscountRate());
            return discounted;
        }

        return originalPrice;
    }
}
```

**Task:** Write the log statements you would add to investigate why no discount is being applied. State the question each log answers.

??? question "Show suggested logs and reasoning"
    ```java
    public double applyMemberDiscount(String memberId, double originalPrice) {
        System.out.println("[applyMemberDiscount] ENTRY: memberId=" + memberId + ", originalPrice=" + originalPrice); // (1)

        MemberProfile profile = memberRepository.findById(memberId);
        System.out.println("[applyMemberDiscount] profile=" + (profile == null ? "NULL" : "found, active=" + profile.isActiveMember())); // (2)

        if (profile != null && profile.isActiveMember()) {
            double discounted = originalPrice * (1.0 - profile.getDiscountRate());
            System.out.println("[applyMemberDiscount] Discount applied: rate=" + profile.getDiscountRate() + ", result=" + discounted); // (3)
            return discounted;
        }

        System.out.println("[applyMemberDiscount] No discount applied — condition not met"); // (4)
        return originalPrice;
    }
    ```
    1. **Confirms the method is being called** with the expected values. If this does not appear in output, the method is not being reached at all — the bug is upstream.
    2. **Diagnoses the condition failure.** If `profile` is null, `findById()` is not finding the member. If `isActiveMember()` is false, the member exists but is not active.
    3. **Confirms the discount calculation.** If the condition is met, this verifies the rate and result. A `getDiscountRate()` of `0.0` would explain no visible change.
    4. **Confirms the fallback path was taken.** If this appears, the condition on line above evaluated to false.

    **If log 1 produces no output:** the method is never being called. The bug is in the calling code, not here.
    **If log 2 shows `profile=NULL`:** `findById()` is not finding the member — wrong ID format or missing database record.
    **If log 2 shows `active=false`:** the member is found but not active — check the `isActiveMember()` logic or the data.
    **If log 3 shows `rate=0.0`:** the discount rate is stored as zero — data problem, not a code logic problem.

---

### B2 — Kotlin: The Event That Never Fires

A button in an Android app is supposed to submit a form and navigate to a confirmation screen. Pressing the button does nothing — no navigation, no error.

```kotlin
class FormFragment : Fragment() {

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        setupSubmitButton()
    }

    private fun setupSubmitButton() {
        val submitButton = view?.findViewById<Button>(R.id.btn_submit)
        submitButton?.setOnClickListener {
            validateAndSubmit()
        }
    }

    private fun validateAndSubmit() {
        val nameField = view?.findViewById<EditText>(R.id.et_name)
        val name = nameField?.text?.toString()

        if (!name.isNullOrBlank()) {
            submitForm(name)
        }
    }

    private fun submitForm(name: String) {
        // Navigate to confirmation
        findNavController().navigate(R.id.action_form_to_confirmation)
    }
}
```

**Task:** Write the Logcat log statements you would add to trace why button presses produce no visible result. Include the tag and level.

??? question "Show suggested logs and reasoning"
    ```kotlin
    private const val TAG = "FormFragment"

    private fun setupSubmitButton() {
        val submitButton = view?.findViewById<Button>(R.id.btn_submit)
        Log.d(TAG, "setupSubmitButton: button=${if (submitButton == null) "NULL" else "found"}") // (1)
        submitButton?.setOnClickListener {
            Log.d(TAG, "Submit button clicked") // (2)
            validateAndSubmit()
        }
    }

    private fun validateAndSubmit() {
        val nameField = view?.findViewById<EditText>(R.id.et_name)
        val name = nameField?.text?.toString()
        Log.d(TAG, "validateAndSubmit: name='$name', isNullOrBlank=${name.isNullOrBlank()}") // (3)

        if (!name.isNullOrBlank()) {
            Log.d(TAG, "validateAndSubmit: validation passed, calling submitForm") // (4)
            submitForm(name)
        } else {
            Log.d(TAG, "validateAndSubmit: validation FAILED — name is null or blank") // (5)
        }
    }

    private fun submitForm(name: String) {
        Log.d(TAG, "submitForm: navigating with name='$name'") // (6)
        findNavController().navigate(R.id.action_form_to_confirmation)
    }
    ```

    **Investigation path:**

    - If **log 1** shows `button=NULL`: `R.id.btn_submit` does not match any view in the layout — wrong ID or wrong layout being inflated.
    - If **log 2** never appears despite pressing the button: the listener was not registered (button was null, so `?.setOnClickListener` was silently skipped).
    - If **log 3** shows `isNullOrBlank=true`: the name field is empty or the `EditText` was not found — the validation block never executes.
    - If **log 4** appears but navigation does not occur: the problem is in `findNavController()` — possibly the fragment is not inside a NavController-managed hierarchy.

---

## ▣ Set C — Multi-File and Indirect Bugs

These exercises present bugs that originate in one location and manifest in another. Apply the full investigative workflow: reproduce, reduce, hypothesise, test, explain.

---

### C1 — JavaScript / TypeScript: The Accumulating Error

A shopping cart function adds items correctly one at a time, but when items are added in a batch, the total is wrong. The bug is intermittent — it does not occur on every batch operation.

```typescript
class ShoppingCart {
    private items: CartItem[] = [];

    addItem(item: CartItem): void {
        this.items.push(item);
    }

    addItemsBatch(newItems: CartItem[]): void {
        newItems.forEach(item => this.addItem(item));
    }

    getTotal(): number {
        let total = 0;
        this.items.forEach(item => {
            total + item.price * item.quantity; // (1)
        });
        return total;
    }
}
```
1. Something is wrong here. Apply the workflow to find it.

**Observed behaviour:** `addItem()` works correctly. `addItemsBatch()` results in a total of `0` regardless of items added.

Apply the six-step workflow. Document each step.

??? question "Show workflow and resolution"
    **Step I — Read:** No error is thrown. Total always returns `0` after batch operations.

    **Step II — Identify:** Logical error — the program runs, but produces consistently wrong output (always `0`).

    **Step III — Locate:** The `getTotal()` method. The forEach loop runs without error, but the total does not accumulate.

    **Step IV — Compare:**
    Expected: sum of all `price × quantity` values.
    Actual: always `0`.

    A total of `0` suggests the accumulation never happens — the loop may not be executing, or the result is not being stored.

    **Step V — One hypothesis:** The expression `total + item.price * item.quantity` *calculates* a value but does not *assign* it back to `total`. The `+` operator without assignment is a no-op. The accumulation requires `+=`.

    **One change:**
    ```typescript
    total + item.price * item.quantity;   // before — calculates and discards
    total += item.price * item.quantity;  // after — accumulates correctly
    ```

    **Step VI — Test:** After the change, `getTotal()` returns the correct sum.

    **Explanation:** The `+` operator without assignment computes a value that is immediately discarded. `total` is never modified inside the loop, so it remains `0` throughout. This is a subtle semantic error — the expression is syntactically valid and produces no warning, but it does not have the intended effect. This pattern is particularly common in loops and is worth checking whenever an accumulator variable behaves unexpectedly.

---

### C2 — Java: The Wrong Object

A student records system stores students and retrieves them by ID. After adding several students, retrieval returns the wrong student for certain IDs.

```java
public class StudentRegistry {
    private List<Student> students = new ArrayList<>();

    public void addStudent(String id, String name, int grade) {
        Student s = new Student();
        s.setId(id);
        s.setName(name);
        s.setGrade(grade);
        students.add(s);
    }

    public Student findById(String targetId) {
        for (Student s : students) {
            if (s.getId() == targetId) {    // (1)
                return s;
            }
        }
        return null;
    }
}
```
1. A comparison is happening here. Is it the right kind?

**Observed behaviour:**
- `findById("S001")` works correctly when called immediately after `addStudent("S001", ...)`
- `findById("S001")` returns `null` when the ID comes from user input or is read from a file

Apply the investigative workflow. Identify the root cause and explain why the intermittent nature of the bug is itself a clue.

??? question "Show workflow and resolution"
    **Step I — Read:** No exception. Returns `null` inconsistently for IDs that are known to exist.

    **Step II — Identify:** Logical / semantic error — the comparison appears correct but behaves incorrectly under certain conditions.

    **Step III — Locate:** `findById()`, specifically the condition `s.getId() == targetId`.

    **Step IV — Compare:**
    Expected: returns the matching student.
    Actual: returns `null` when the ID originates from external input.

    The intermittent nature is the key clue. It works when the string literal used in `addStudent()` is compared — because Java may intern those literals, making `==` evaluate to `true`. It fails when the string comes from user input or file reading, because those produce new `String` objects that are not the same object in memory, even if they contain the same characters.

    **Step V — Hypothesis:** `==` compares object references in Java, not string content. Two `String` objects containing `"S001"` are not the same object unless interned. Use `.equals()` for content comparison.

    **One change:**
    ```java
    if (s.getId() == targetId)           // before — reference comparison
    if (s.getId().equals(targetId))      // after — content comparison
    ```

    **Step VI — Test:** `findById()` now returns the correct student regardless of where the ID string originated.

    **Explanation:** This is a classic Java semantic error. The `==` operator on objects compares memory addresses, not values. String literals in Java source code are pooled (interned) by the JVM, which is why `==` appears to work when comparing literals directly — they may reference the same pooled object. Strings from user input, file reading, or network data are new objects and are not pooled, so `==` reliably returns `false` even for identical content. This is why the bug was intermittent: it depended on the *origin* of the string, not its *value*.

---

### C3 — Kotlin / Android: The Lifecycle Trap

A product detail screen loads correctly the first time it is opened. When the user navigates back and reopens the same screen, it shows stale data from the previous visit.

```kotlin
class ProductDetailFragment : Fragment() {

    private var productId: String? = null
    private lateinit var viewModel: ProductViewModel

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        productId = arguments?.getString("productId")
        viewModel = ViewModelProvider(this).get(ProductViewModel::class.java)
        loadProduct()  // (1)
    }

    private fun loadProduct() {
        productId?.let {
            viewModel.loadProduct(it)
        }
    }

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        viewModel.product.observe(viewLifecycleOwner) { product ->
            displayProduct(product)
        }
    }
}
```
1. When in the lifecycle is `loadProduct()` being called?

**Observed behaviour:** First visit shows correct data. Back-navigation and return shows previous product data until the screen is dismissed and reopened from scratch.

Without running the code, reason through the lifecycle to identify why stale data persists.

??? question "Show reasoning and resolution"
    **Step I — Identify the pattern:** The bug is lifecycle-dependent — it only occurs on return navigation, not on first visit. This points to a state management or initialisation timing issue rather than a logic error in the data layer.

    **Step II — Trace the lifecycle:**
    When the user navigates *to* the screen: `onCreate()` is called → `productId` is read from arguments → `loadProduct()` fires → data loads correctly.

    When the user navigates *back* and returns: **`onCreate()` is NOT called again** for a Fragment that is on the back stack. The Fragment is resumed from the back stack — only `onStart()` and `onResume()` are called. The ViewModel retains its previous state.

    **Step III — Identify the root cause:**
    `loadProduct()` is called only in `onCreate()`. On return navigation, `onCreate()` does not run again. The ViewModel still holds the data from the previous product. The `observe()` call in `onViewCreated()` delivers the cached (stale) value immediately upon subscription.

    **Step IV — Fix:**
    Move `loadProduct()` to `onResume()` or `onViewCreated()` so it fires every time the screen becomes visible:

    ```kotlin
    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)

        viewModel.product.observe(viewLifecycleOwner) { product ->
            displayProduct(product)
        }

        loadProduct()  // moved here — fires on every view creation, including back-navigation return
    }
    ```

    **Explanation:** Android Fragment lifecycle is a common source of subtle bugs. `onCreate()` runs once per Fragment instance creation. `onViewCreated()` runs each time the Fragment's view is created — including after back-stack returns. Data loading that must be fresh on every visit should happen in `onViewCreated()` or `onResume()`, not in `onCreate()`. This class of bug — correct first behaviour, stale repeated behaviour — is almost always a lifecycle placement issue.

---

## ▣ Set D — Reflection and Self-Assessment

After completing the exercises above, answer the following questions. There are no correct answers — these are for your own development.

!!! question "Reflect on your investigation process"
    I. In Set A, did reading the stack trace give you enough information to form a hypothesis before seeing the answer? If not, what additional information would you have needed?

    II. In Set B, were there log positions you initially overlooked? What did adding those logs reveal that you had not anticipated?

    III. In Set C exercise 2, the bug was intermittent. Before reading the answer, what was your hypothesis for why the same code could produce different results on different runs?

    IV. In Set C exercise 3, no error message was produced. How did you approach reasoning about a bug with no crash, no log output, and no obvious error? What did the symptom pattern (works first time, fails on return) tell you before you examined any code?

    V. How has your approach to reading stack traces changed since working through this section? What do you look for first now that you might have overlooked before?

---

*You have completed 02 · Investigation.*

*Continue to: [03 · Strategy →](../03_strategy/index.md)*

*← Back to: [Section Index](index.md)*

---

*[IDE]: Integrated Development Environment
*[JVM]: Java Virtual Machine
*[LINQ]: Language Integrated Query — a query syntax built into C#
*[ViewModel]: An Android architecture component that holds UI-related data
