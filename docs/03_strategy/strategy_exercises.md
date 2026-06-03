# ▣ Strategy Exercises

---

## ▣ How to Use These Exercises

These exercises are different from those in the previous sections. They are less about finding a specific broken line and more about reasoning at a system level — identifying which layer of a system is responsible for a failure, writing the documents that professional developers produce, and applying preventive practices before a bug occurs.

Some exercises ask you to write documents (bug reports, post-mortems). Take these seriously — the discipline of writing precisely about technical problems is as valuable as the technical investigation itself.

---

## ▣ Set A — System-Level Diagnosis

For each scenario, identify: the most likely layer of the system responsible for the failure, the investigative approach you would take, and the first concrete action.

---

### A1 — The Feature That Works in Development but Not in Production

A TypeScript / Node.js application sends email notifications when a user completes a purchase. During development and testing, notifications are sent correctly. After deployment to the production server, no notifications are sent — no errors are logged, and the purchase completes without any visible problem.

**Questions:**

I. The absence of errors is itself a clue. What does it suggest about where the failure is?

II. List three specific differences between a development environment and a production environment that could cause this behaviour.

III. What would you log at the entry point of the notification service to begin investigation?

IV. Write the first three items of a bug report for this issue.

??? question "Show analysis and guidance"
    **I. Absence of errors:**
    Errors thrown by the notification service are being silently swallowed — either caught without logging, or the notification call is wrapped in a try-catch that does nothing on failure. The notification code is running but failing silently. This is a silent failure pattern — the application continues normally because the failure is not propagated.

    **II. Likely environment differences:**
    - **API key / credentials:** The email service API key configured in the development `.env` file is not set in the production environment, so API calls fail with an authentication error that is silently caught.
    - **Network access:** The production server may have firewall rules that prevent outbound connections to the email service endpoint.
    - **Environment variable name:** A typo in the production environment variable name (`EMAIL_API_KEY` vs `EMAILAPIKEY`) causes the value to load as `undefined`, which is passed to the API client and fails silently.

    **III. Logging at entry point:**
    ```typescript
    async function sendPurchaseNotification(order: Order): Promise<void> {
        console.log('[Notifications] sendPurchaseNotification called:', {
            orderId: order.id,
            userEmail: order.userEmail,
            apiKeyPresent: !!process.env.EMAIL_API_KEY,
            apiKeyLength: process.env.EMAIL_API_KEY?.length ?? 0
        });
        try {
            await emailClient.send({ ... });
            console.log('[Notifications] Email sent successfully');
        } catch (error) {
            console.error('[Notifications] Email send FAILED:', error); // (1)
        }
    }
    ```
    1. The existing catch block was not logging the error. This single line is what would have revealed the failure immediately.

    **IV. Bug report opening:**

    **Title:** Purchase notifications not sent in production — no error logged (email API key not loaded)

    **Environment:** Production server (Ubuntu 22.04), Node.js 20.x, app version 3.1.2

    **Steps to reproduce:**
    1. Complete a purchase on the production environment
    2. Check notification logs — no entry appears
    3. Check user inbox — no notification received

    **Expected:** User receives email notification within 60 seconds of purchase completion.

    **Actual:** No email sent, no error logged. Purchase completes normally.

---

### A2 — The Intermittent State Bug

A React single-page application manages a shopping list. Users can add, remove, and reorder items. The list saves automatically. After several months in production, users begin reporting that occasionally their saved lists appear empty when they return to the application — despite having items the last time they visited.

The bug cannot be reproduced reliably in development. It has been reported by approximately 5% of users.

**Questions:**

I. The 5% occurrence rate is a clue. What kinds of conditions affect only some users?

II. What specific data would you want to collect from users who report the bug to narrow down the cause?

III. Is this an appropriate bug to debug reactively (investigate after it occurs) or preventively (add safeguards before it is fully understood)? Explain your reasoning.

IV. Write two specific action items that would appear in the post-mortem for this bug.

??? question "Show analysis and guidance"
    **I. What 5% occurrence suggests:**
    The bug is not universal — it is conditional on something that applies to some users but not others. Likely candidates:
    - Specific browser or browser version (a serialisation bug in an older browser)
    - Specific network conditions (the save request was made but not completed before the tab was closed)
    - Race condition between the autosave and navigation (saving is async — if the user navigates away before the save promise resolves, the write does not complete)
    - Storage quota exceeded for users with large lists (browser localStorage has a ~5MB limit — silent failure on write)

    **II. Data to collect from affected users:**
    - Browser name and version
    - Approximate number of items in their list before it disappeared
    - Whether they had multiple tabs open
    - Whether the app was used across multiple devices (sync conflict)
    - Whether they have any browser extensions that might interfere with storage
    - Time of last confirmed successful save vs. time of reported empty list

    **III. Reactive vs. preventive:**
    Both are warranted here. The bug is active in production affecting real users, so preventive safeguards should be applied immediately even without full diagnosis:
    - Add error logging to the save operation — catch and log any storage failure
    - Add a local backup (keep the previous save before overwriting)
    - Confirm writes with a readback — write the list, read it back, verify it matches

    Full investigation continues in parallel to identify the root cause. This is the professional approach: mitigate first, diagnose second.

    **IV. Post-mortem action items:**
    1. **Add error logging to localStorage write operations** — assigned to [developer], due [date]. Currently, write failures are silently swallowed. Any storage error must be logged with the list size and browser context.
    2. **Implement a write-verify pattern** — assigned to [developer], due [date]. After every autosave, read back the stored value and compare its item count to the in-memory list. If they differ, surface an error to the user rather than silently losing data.

---

## ▣ Set B — Writing Professional Documents

---

### B1 — Bug Report

The following scenario describes a bug. Write a complete, professional bug report using the structure from [Professional Debugging Practices](professional_practices.md).

**Scenario:**

You are working on an Android app built in Kotlin. The app has a profile screen where users can update their display name. After updating the name and pressing Save, the profile screen shows the new name correctly. However, when the user navigates to the Home screen and back to the Profile screen, the old name is displayed again. The updated name only appears after the app is fully restarted.

The following Logcat output was captured during the bug:

```
D/ProfileViewModel: loadProfile called: userId=usr_4821
D/ProfileRepository: fetchProfile: returning cached profile for usr_4821
D/ProfileFragment: displayProfile: name=Alice (old name shown)
```

The save operation completes with:

```
D/ProfileRepository: saveProfile: success, userId=usr_4821, newName=Alice Updated
```

??? question "Show a model bug report"
    **Title:** Profile screen shows stale display name after update — cache not invalidated on save

    **Environment:**
    - Android 13 (API 33), Pixel 6 emulator and OnePlus Nord physical device
    - App version 1.8.4 (build 312)
    - Kotlin 1.9, Jetpack Navigation 2.7

    **Steps to reproduce:**
    1. Log in with any user account
    2. Navigate to the Profile screen
    3. Tap Edit, change the display name to "Alice Updated", tap Save
    4. Observe: Profile screen now shows "Alice Updated" ✓
    5. Press Back to navigate to the Home screen
    6. Navigate back to the Profile screen
    7. Observe: Profile screen shows the old name "Alice" ✗
    8. Fully close and reopen the app
    9. Observe: Profile screen now shows "Alice Updated" ✓

    **Expected:** The Profile screen always shows the current display name, including after back-navigation.

    **Actual:** After navigating away and returning, the Profile screen displays the name that was current *before* the most recent save.

    **Evidence — Logcat:**
    ```
    D/ProfileRepository: saveProfile: success, userId=usr_4821, newName=Alice Updated
    D/ProfileRepository: fetchProfile: returning cached profile for usr_4821
    D/ProfileFragment: displayProfile: name=Alice  ← old name from cache
    ```
    The save succeeds but the in-memory cache is not updated. On return navigation, `loadProfile()` reads the stale cached value rather than the updated one.

    **Root cause (suspected):** `ProfileRepository` maintains an in-memory cache of profile objects. The `saveProfile()` method persists the change to the data source but does not update the cache. When `loadProfile()` is called on return navigation, it returns the cached (stale) profile.

    **Severity:** Medium — incorrect data displayed to users after every profile update, until app restart. No data loss occurs (saved value is correct in the data source).

    **Suggested fix:** After a successful save, invalidate or update the cache entry for the affected userId.

---

### B2 — Post-Mortem

Write a post-mortem for the following incident.

**Incident summary:**

On a Tuesday evening, a student team deployed version 2.0 of their capstone project — a web-based event management system built in TypeScript with a Node.js backend. At 21:47, a lecturer attempted to log in to assess the application and was shown a 500 Internal Server Error. All users were affected. The team was notified at 22:05 and began investigating. At 22:31, the issue was identified: the `.env` file containing the database connection string had not been included in the deployment because it was listed in `.gitignore`. The team manually created the `.env` file on the server with the correct values and restarted the application at 22:38. The application was fully restored. Total downtime: 51 minutes.

Write a post-mortem that covers all seven components from the Professional Practices page.

??? question "Show a model post-mortem"
    ---

    **POST-MORTEM: Production Outage — Missing Environment Configuration**
    *Capstone Project — Event Management System v2.0*
    *Date of incident: [Tuesday] | Written by: [Team Lead]*

    ---

    **I. Summary**

    At 21:47, version 2.0 of the Event Management System was deployed and immediately became unavailable to all users due to a missing database connection string. The application returned HTTP 500 errors on all routes. The outage lasted 51 minutes and was resolved at 22:38 by manually creating the missing `.env` file on the production server.

    ---

    **II. Timeline**

    | Time | Event |
    |------|-------|
    | 21:47 | v2.0 deployed to production server |
    | 21:47 | Application returns 500 errors on all routes |
    | 22:05 | Lecturer reports access failure; team notified |
    | 22:09 | Team begins investigation; server logs examined |
    | 22:31 | Root cause identified: `DATABASE_URL` environment variable not set |
    | 22:36 | `.env` file created on production server with correct values |
    | 22:38 | Application restarted; all routes responding correctly |
    | 22:40 | Lecturer confirms access restored |

    ---

    **III. Root Cause**

    The application requires a `DATABASE_URL` environment variable to establish a database connection. This value is stored in a `.env` file that is correctly excluded from version control via `.gitignore`. When the application was deployed, no procedure existed to provision the `.env` file on the production server. The database client threw an unhandled exception on startup when the variable was absent, causing all routes to return 500 errors.

    ---

    **IV. Contributing Factors**

    - No deployment checklist existed that included environment configuration verification
    - The application did not validate the presence of required environment variables at startup and log a clear error message
    - No staging environment existed on which the deployment could have been tested before reaching production
    - The team had not previously performed a full deployment from scratch — previous deployments updated an already-configured server

    ---

    **V. Impact**

    All users were unable to access the application for 51 minutes. One assessment attempt by a lecturer was blocked during this period.

    ---

    **VI. Resolution**

    The `.env` file was manually created on the production server with the correct `DATABASE_URL` and other required variables. The application was restarted and restored to full operation.

    ---

    **VII. Action Items**

    | Action | Owner | Due |
    |--------|-------|-----|
    | Create a deployment checklist that includes environment configuration verification as a required step | Team Lead | Before next deployment |
    | Add startup validation: check all required environment variables at application start and exit with a clear error message if any are missing | Backend Developer | This sprint |
    | Document all required environment variables and their expected format in the README | Team Lead | This week |

    ---

---

## ▣ Set C — Preventive Practice

---

### C1 — Writing Tests for an Existing Bug

The following Java method has a known bug: it throws an `ArithmeticException` when called with an empty list.

```java
public class GradeCalculator {

    public double calculateAverage(List<Integer> grades) {
        int total = 0;
        for (int grade : grades) {
            total += grade;
        }
        return (double) total / grades.size(); // (1)
    }
}
```
1. Division by zero when `grades` is empty.

**Task:**

I. Write a unit test (in the language of your choice) that reproduces the bug — the test should fail on the current implementation.

II. Fix the method.

III. Write two additional tests that verify the fixed behaviour for an empty list and a single-element list.

??? question "Show tests and fix"
    **I. Test that reproduces the bug (JUnit 5, Java):**
    ```java
    @Test
    void emptyListReturnsZeroNotException() {
        GradeCalculator calculator = new GradeCalculator();
        // This test fails on the current implementation with ArithmeticException
        double result = calculator.calculateAverage(List.of());
        assertEquals(0.0, result, 0.001);
    }
    ```

    **II. Fixed method:**
    ```java
    public double calculateAverage(List<Integer> grades) {
        if (grades == null || grades.isEmpty()) {
            return 0.0;
        }
        int total = 0;
        for (int grade : grades) {
            total += grade;
        }
        return (double) total / grades.size();
    }
    ```

    **III. Additional tests:**
    ```java
    @Test
    void emptyListReturnsZero() {
        GradeCalculator calculator = new GradeCalculator();
        assertEquals(0.0, calculator.calculateAverage(List.of()), 0.001);
    }

    @Test
    void singleElementListReturnsElement() {
        GradeCalculator calculator = new GradeCalculator();
        assertEquals(75.0, calculator.calculateAverage(List.of(75)), 0.001);
    }

    @Test
    void multipleGradesReturnCorrectAverage() {
        GradeCalculator calculator = new GradeCalculator();
        assertEquals(80.0, calculator.calculateAverage(List.of(70, 80, 90)), 0.001);
    }
    ```

---

## ▣ Reflection

!!! question "Reflect on your strategic thinking"
    I. In Set A exercise 1, the absence of error logs was the first clue. Before working through the answer, what did you think that absence meant? Did your interpretation change?

    II. In Set B exercise 1 (the bug report), how specific was your suspected root cause section? Could a developer who was not present during the investigation reproduce and fix the bug from your report alone?

    III. In Set B exercise 2 (the post-mortem), were the action items in your version specific and assignable — or were they vague commitments? What is the difference between *"improve deployment process"* and *"create a deployment checklist that includes environment configuration verification as a required step before next deployment"*?

    IV. Looking at your own current projects: are there areas where a missing test, a missing assertion, or a missing null check is a known risk? What would it take to address one of them this week?

---

*You have completed 03 · Strategy.*

*Continue to: [04 · Tools, Techniques & Thinking →](../04_tools_techniques/index.md)*

*← Back to: [Section Index](index.md)*

---

*[TDD]: Test-Driven Development
*[API]: Application Programming Interface
*[HTTP]: Hypertext Transfer Protocol
