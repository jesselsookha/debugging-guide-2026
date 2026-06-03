# ◆ Preventive Measures

---

## ◆ The Best Bug Is One That Never Reaches Production

Everything in this guide so far has addressed bugs that already exist — how to read them, investigate them, and fix them. This page addresses a different question: how do you prevent them from occurring in the first place, or catch them as early as possible in the development process?

The principle from the InfoWorld debugging research[^1] applies directly here:

> *"Learn to fix bugs as early in the development process as you can."*

The later a bug is found, the more expensive it is to fix. A bug caught during development takes minutes. The same bug caught during code review takes an hour. The same bug reported by a user in production takes a day — and may have caused real harm in the meantime.

Preventive measures are the practices that shift bug discovery as early as possible in the development process.

[^1]: InfoWorld — "Learning and Improving Your Debugging Skills" — https://www.infoworld.com/article/2164328/learning-and-improving-your-debugging-skills.html

---

## ◆ I. Unit Testing

A unit test is a small, automated test that verifies a single piece of functionality in isolation. It runs a specific method or function with controlled inputs and asserts that the output matches what is expected.

The value of a unit test is not only that it catches a bug when the test is first written. It is that it continues to catch regressions — bugs that are accidentally reintroduced by future changes — every time the test suite is run.

### What a Unit Test Does

=== "Java (JUnit 5)"
    ```java
    import org.junit.jupiter.api.Test;
    import static org.junit.jupiter.api.Assertions.*;

    class DiscountServiceTest {

        @Test
        void premiumMemberReceivesTwentyPercentDiscount() {
            DiscountService service = new DiscountService();
            double result = service.applyDiscount("premium", 100.0);
            assertEquals(80.0, result, 0.001); // (1)
        }

        @Test
        void standardMemberReceivesNoDiscount() {
            DiscountService service = new DiscountService();
            double result = service.applyDiscount("standard", 100.0);
            assertEquals(100.0, result, 0.001);
        }

        @Test
        void nullMemberTypeReturnsOriginalPrice() {
            DiscountService service = new DiscountService();
            double result = service.applyDiscount(null, 100.0);
            assertEquals(100.0, result, 0.001); // (2)
        }
    }
    ```
    1. Tests the happy path — expected discount for a premium member.
    2. Tests a defensive case — what happens when input is null? This test forces the developer to handle it.

=== "C# (NUnit)"
    ```csharp
    using NUnit.Framework;

    [TestFixture]
    public class DiscountServiceTests
    {
        private DiscountService _service;

        [SetUp]
        public void SetUp() => _service = new DiscountService();

        [Test]
        public void PremiumMember_ReceivesTwentyPercentDiscount()
        {
            var result = _service.ApplyDiscount("premium", 100m);
            Assert.That(result, Is.EqualTo(80m));
        }

        [Test]
        public void NullMemberType_ReturnsOriginalPrice()
        {
            var result = _service.ApplyDiscount(null, 100m);
            Assert.That(result, Is.EqualTo(100m));
        }
    }
    ```

=== "JavaScript / TypeScript (Jest)"
    ```typescript
    import { applyDiscount } from './discountService';

    describe('applyDiscount', () => {

        it('applies 20% discount for premium members', () => {
            expect(applyDiscount('premium', 100)).toBe(80);
        });

        it('returns original price for standard members', () => {
            expect(applyDiscount('standard', 100)).toBe(100);
        });

        it('handles null member type without crashing', () => {
            expect(applyDiscount(null, 100)).toBe(100);
        });
    });
    ```

=== "Kotlin (JUnit 5)"
    ```kotlin
    import org.junit.jupiter.api.Test
    import org.junit.jupiter.api.Assertions.*

    class DiscountServiceTest {

        private val service = DiscountService()

        @Test
        fun `premium member receives 20 percent discount`() {
            val result = service.applyDiscount("premium", 100.0)
            assertEquals(80.0, result, 0.001)
        }

        @Test
        fun `null member type returns original price`() {
            val result = service.applyDiscount(null, 100.0)
            assertEquals(100.0, result, 0.001)
        }
    }
    ```

### What Unit Tests Reveal

Unit tests are most valuable for revealing edge cases — the inputs at the boundaries of valid ranges, the null or empty inputs, the combinations of conditions that normal use paths do not exercise.

!!! tip "Test the cases you do not expect, not only the ones you do"
    The test that catches bugs is rarely `applyDiscount("premium", 100)`. It is `applyDiscount(null, 100)`, `applyDiscount("", 0)`, `applyDiscount("PREMIUM", 100)` — the inputs that were not considered during development. Write at least one boundary case and one invalid input case for every method you test.

### Tests as Debugging Tools

When a bug is reported, writing a test that reproduces it before fixing it is valuable for two reasons:

I. It confirms you understand the bug — the test fails in the way the bug manifests.

II. When the fix is applied and the test passes, it provides ongoing protection against the same bug recurring.

This is the practice of **regression testing**: after fixing a bug, a test is added that would have caught it. Over time, the test suite grows to protect against every known failure mode.

---

## ◆ II. Test-Driven Development (TDD)

Test-driven development is a practice in which tests are written **before** the code they test. The cycle is:

**I. Red** — Write a test for a small piece of functionality. Run it. It fails (because the functionality does not exist yet).

**II. Green** — Write the minimum code necessary to make the test pass. Run it. It passes.

**III. Refactor** — Improve the structure of the code without changing its behaviour. The tests confirm behaviour is preserved.

```
┌──────────────────────────────────────────────────────────────────┐
│  THE TDD CYCLE                                                   │
│                                                                  │
│     ┌─────────┐                                                  │
│     │  Write  │                                                  │
│     │  test   │                                                  │
│     └────┬────┘                                                  │
│          │ test fails (RED)                                      │
│          ▼                                                       │
│     ┌─────────┐                                                  │
│     │  Write  │                                                  │
│     │  code   │                                                  │
│     └────┬────┘                                                  │
│          │ test passes (GREEN)                                   │
│          ▼                                                       │
│     ┌─────────┐                                                  │
│     │Refactor │──────────────────────────────────► repeat        │
│     │  code   │ tests still pass                                 │
│     └─────────┘                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### Why TDD Reduces Bugs

**It forces specification before implementation.** Writing a test first requires you to define exactly what the function should do, what it should receive, and what it should return — before writing any implementation code. This surfaces ambiguities and missing requirements before they become bugs.

**It produces a complete test suite automatically.** Because every piece of functionality has a test written for it from the start, TDD codebases tend to have high test coverage without additional effort.

**It makes debugging fast.** When a failing test appears, it is specific: this input produces this wrong output. The scope of investigation is immediately bounded.

!!! note "TDD is a design practice, not just a testing practice"
    The primary benefit of TDD is not the tests themselves — it is the thinking discipline it enforces. Writing a test before the code forces you to reason clearly about the interface, the inputs, the outputs, and the edge cases before writing a single line of implementation. Programmers who practise TDD consistently tend to write cleaner, more focused functions — because they have to think about the contract before they think about the implementation.

---

## ◆ III. Assertions and Defensive Programming

Assertions are checks embedded in code that verify that specific conditions are true at specific points in execution. If an assertion fails, the program stops immediately with a clear error message rather than continuing with incorrect state.

Defensive programming is the broader practice of writing code that fails loudly and early — rather than continuing silently with invalid state until a failure manifests somewhere distant from its cause.

### Assertions in Practice

=== "Java"
    ```java
    public double calculateTax(double amount) {
        assert amount >= 0 : "Tax calculation requires non-negative amount, got: " + amount;
        // If amount is negative, execution stops here with a clear message
        return amount * TAX_RATE;
    }
    ```
    Note: Java assertions must be enabled with `-ea` flag at runtime. In production code, use explicit checks instead.

=== "C# (Debug.Assert)"
    ```csharp
    public decimal CalculateTax(decimal amount)
    {
        System.Diagnostics.Debug.Assert(amount >= 0,
            $"CalculateTax: amount must be non-negative, got {amount}");
        return amount * TaxRate;
    }
    ```

=== "Kotlin (require / check)"
    ```kotlin
    fun calculateTax(amount: Double): Double {
        require(amount >= 0) { "Tax calculation requires non-negative amount, got: $amount" }
        return amount * TAX_RATE
    }

    fun processOrder(order: Order) {
        check(order.status == OrderStatus.PENDING) {
            "processOrder called on non-pending order: ${order.status}"
        }
        // ...
    }
    ```
    `require()` validates function arguments. `check()` validates object state. Both throw immediately with your message.

=== "JavaScript / TypeScript"
    ```typescript
    function calculateTax(amount: number): number {
        if (amount < 0) {
            throw new Error(`calculateTax: amount must be non-negative, got ${amount}`);
        }
        return amount * TAX_RATE;
    }
    ```

### What Defensive Programming Looks Like

```java
// Without defensive programming — silent failure
public void setUserAge(int age) {
    this.age = age; // Accepts -5, 999, anything
}

// With defensive programming — loud, early failure
public void setUserAge(int age) {
    if (age < 0 || age > 150) {
        throw new IllegalArgumentException(
            "Invalid age: " + age + ". Expected 0–150."
        );
    }
    this.age = age;
}
```

!!! tip "Validate at the boundary"
    Validate inputs at the point where they enter your system — from user input, from an API response, from a database — not deep inside your business logic. The boundary is where assumptions about data can be checked once, cleanly, rather than scattered throughout the codebase.

### The Null Object Pattern and Optional Types

A significant proportion of runtime errors are null reference exceptions. Languages have introduced various mechanisms to reduce them:

=== "Kotlin — null safety"
    ```kotlin
    // Kotlin's type system distinguishes nullable from non-nullable
    var name: String = "Alice"     // cannot be null
    var nickname: String? = null   // explicitly nullable

    // Safe call — only executes if nickname is non-null
    println(nickname?.length)

    // Elvis operator — provides a default
    val displayName = nickname ?: "Unknown"
    ```

=== "Java — Optional"
    ```java
    // Return Optional instead of null to force callers to handle absence
    public Optional<User> findById(String id) {
        User user = database.query(id);
        return Optional.ofNullable(user);
    }

    // Caller must handle both cases
    findById("123").ifPresent(user -> display(user));
    String name = findById("123").map(User::getName).orElse("Unknown");
    ```

=== "TypeScript — strict null checks"
    ```typescript
    // With "strictNullChecks": true in tsconfig.json
    function greet(name: string | null): string {
        if (name === null) {
            return "Hello, stranger";
        }
        return `Hello, ${name}`;
    }
    // TypeScript forces you to handle the null case before using name
    ```

---

## ◆ IV. Code Reviews as a Debugging Tool

A code review is a preventive debugging activity. A reviewer reading code before it is merged is asking exactly the debugging questions: does this code do what it claims? Are edge cases handled? Are the assumptions correct?

### What to Look for in a Review from a Debugging Perspective

**Unchecked inputs:** Are there places where null, empty, or out-of-range values would cause silent failures or crashes?

**Missing error handling:** Are exceptions caught meaningfully, or swallowed silently? Is a network failure handled, or does it leave the application in an indeterminate state?

**Implicit assumptions:** Is the code relying on state that is not guaranteed? Is a lifecycle assumption embedded without documentation?

**Tests:** Does the change include tests for the new behaviour? Does it include a regression test if it fixes a bug?

!!! quote "On reviewing other people's code"
    *"To improve your skills, debug other people's code rather than your own. It will be easier to see the faults in the other person's assumptions than it is to see your own."*[^2]

    The same principle applies in reverse: when your code is reviewed, the reviewer's questions about your assumptions are one of the most valuable forms of debugging feedback available.

[^2]: InfoWorld — "Learning and Improving Your Debugging Skills" — https://www.infoworld.com/article/2164328/learning-and-improving-your-debugging-skills.html

---

## ◆ V. The Cost of Finding Bugs Late

A useful frame for understanding why preventive measures are worth the investment:

| When the bug is found | Approximate relative cost |
|-----------------------|--------------------------|
| During development (by the programmer) | × 1 |
| During code review | × 5 |
| During testing (QA) | × 10 |
| After deployment (reported by a user) | × 50–100 |

These ratios are approximate and context-dependent, but the direction is consistent across all software engineering research: bugs found late cost significantly more than bugs found early — in time, in reputation, and sometimes in real harm to users or data.

Unit tests, TDD, assertions, and code reviews are investments in early detection. They have a cost at the time they are written. They pay dividends every time they catch something before it reaches production.

---

*Continue to: [Strategy Exercises →](strategy_exercises.md)*

*← Back to: [Section Index](index.md)*

---

*[TDD]: Test-Driven Development
*[QA]: Quality Assurance
*[API]: Application Programming Interface
