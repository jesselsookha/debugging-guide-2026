# ◈ Print and Log Debugging

---

## ◈ The Oldest Tool That Still Works

Print debugging — inserting output statements to observe program state — is the first technique most programmers discover and, in the right circumstances, one of the most effective tools available. It requires no special setup, no debugger configuration, and works in every language and environment without exception.

Its simplicity is both its strength and its weakness. Used without strategy, print statements become noise — thousands of lines of output that are harder to read than the code itself. Used strategically, they answer specific questions about program state with precision and speed.

This page covers both sides: when to use print and log debugging, and how to use it well.

---

## ◈ I. Print Debugging vs. Structured Logging

Before discussing technique, it is worth distinguishing between two related but distinct practices.

**Print debugging** is temporary — statements inserted during investigation and removed once the bug is resolved. The goal is rapid, targeted visibility into a specific behaviour.

**Structured logging** is permanent — a deliberate record of application events written to a log system, with severity levels, timestamps, and context. It is part of the application's architecture, not a debugging shortcut.

This page focuses primarily on print debugging. Structured logging is covered in the context of professional practices in [03 · Strategy](../03_strategy/index.md).

!!! info "The line between them"
    In professional development, temporary debug prints should never be committed to version control. Structured log statements — using severity levels like `DEBUG`, `INFO`, `WARN`, `ERROR` — are deliberate, maintained, and appropriate to commit. The discipline is in knowing which you are writing and treating them accordingly.

---

## ◈ II. When Print Debugging Is the Right Tool

Print debugging is particularly well suited to:

- **Confirming execution flow** — verifying that a specific line or method was actually reached
- **Tracking variable values over time** — observing how a value changes across multiple iterations or calls
- **Debugging concurrent or async systems** — where a traditional debugger's pause-and-inspect model can alter the timing behaviour being investigated
- **Quick, isolated investigations** — when the question is simple enough that a debugger would be slower to set up than a print statement would be to write
- **Environments where a debugger is unavailable** — remote systems, embedded environments, or constrained runtime contexts

It is less well suited to:

- Complex multi-level call stacks (a debugger gives better visibility)
- Bugs that only occur under precise timing conditions (the act of logging can alter timing)
- Large codebases where the problem area is unknown (too much output is generated)

---

## ◈ III. Strategic vs. Noise Logging

The critical difference between effective and ineffective print debugging is *selectivity*.

### Noise logging — what to avoid

```java
// Noise: prints everything, answers nothing specific
System.out.println("here");
System.out.println("here2");
System.out.println("here3");
System.out.println(x);
System.out.println(y);
System.out.println("done");
```

This output is difficult to read, harder to interpret, and tells you almost nothing about why the bug is occurring. The labels `"here"`, `"here2"` are ambiguous — without seeing the code simultaneously, the output is meaningless.

### Strategic logging — what to do instead

A strategic log statement:

I. Has a label that identifies exactly where in the code it is
II. Prints a specific value or condition that you are investigating
III. Is placed at a point that answers a precise question
IV. Is removed or converted to a structured log once the investigation is complete

=== "Java"
    ```java
    // Question: Is processPayment() being called with the correct amount?
    System.out.println("[PaymentService.processPayment] amount=" + amount + ", currency=" + currency);

    // Question: Which branch is being taken?
    if (amount > LARGE_PAYMENT_THRESHOLD) {
        System.out.println("[PaymentService] Taking large-payment path");
        processLargePayment(amount);
    } else {
        System.out.println("[PaymentService] Taking standard path");
        processStandardPayment(amount);
    }

    // Question: What is the result before it is returned?
    double result = calculateFee(amount);
    System.out.println("[PaymentService.calculateFee] result=" + result);
    return result;
    ```

=== "C#"
    ```csharp
    // Question: What state is the order in when it reaches ProcessOrder?
    Console.WriteLine($"[OrderProcessor.ProcessOrder] OrderId={order.Id}, Status={order.Status}, ItemCount={order.Items.Count}");

    foreach (var item in order.Items)
    {
        // Question: Is each item being processed with the correct price?
        Console.WriteLine($"[OrderProcessor] Processing item: {item.Name}, Price={item.Price}, Qty={item.Quantity}");
        total += item.Price * item.Quantity;
        Console.WriteLine($"[OrderProcessor] Running total after {item.Name}: {total}");
    }
    ```

=== "Kotlin / Android"
    ```kotlin
    // Use Log levels in Android — not println
    private const val TAG = "CheckoutViewModel"

    fun submitOrder(cart: Cart) {
        Log.d(TAG, "submitOrder called: itemCount=${cart.items.size}, total=${cart.total}")

        if (cart.items.isEmpty()) {
            Log.w(TAG, "submitOrder: cart is empty — aborting")
            return
        }

        val result = paymentService.charge(cart.total)
        Log.d(TAG, "Payment result: success=${result.success}, transactionId=${result.transactionId}")
    }
    ```

=== "JavaScript / TypeScript"
    ```typescript
    // Use console.log with clear labels and structured data
    function processCart(cart: Cart): Invoice {
        console.log('[Cart.processCart] Starting:', { itemCount: cart.items.length, total: cart.total });

        const discountedTotal = applyDiscounts(cart);
        console.log('[Cart.processCart] After discounts:', { discountedTotal });

        const tax = calculateTax(discountedTotal);
        console.log('[Cart.processCart] Tax calculated:', { tax, finalTotal: discountedTotal + tax });

        return buildInvoice(discountedTotal, tax);
    }
    ```

---

## ◈ IV. Logging Patterns for Common Problems

### Tracing execution flow through a method

When you are unsure whether code is being reached:

```java
// Java — entry and exit logging
public String formatUsername(String raw) {
    System.out.println("[formatUsername] ENTRY: raw='" + raw + "'");

    if (raw == null || raw.isEmpty()) {
        System.out.println("[formatUsername] EXIT: returning empty (null or blank input)");
        return "";
    }

    String result = raw.trim().toLowerCase();
    System.out.println("[formatUsername] EXIT: result='" + result + "'");
    return result;
}
```

### Tracing values through a loop

When a loop produces the wrong result:

```csharp
// C# — log each iteration
double total = 0;
for (int i = 0; i < prices.Length; i++)
{
    Console.WriteLine($"[Loop i={i}] price={prices[i]}, total before={total}");
    total += prices[i];
    Console.WriteLine($"[Loop i={i}] total after={total}");
}
Console.WriteLine($"[Loop complete] Final total={total}");
```

### Confirming event or callback execution order

When working with event-driven or asynchronous code:

```kotlin
// Kotlin / Android — lifecycle and callback tracing
override fun onResume() {
    super.onResume()
    Log.d(TAG, "onResume called")
}

override fun onPause() {
    super.onPause()
    Log.d(TAG, "onPause called")
}

private val networkCallback = object : ConnectivityManager.NetworkCallback() {
    override fun onAvailable(network: Network) {
        Log.d(TAG, "Network available: $network")
    }

    override fun onLost(network: Network) {
        Log.d(TAG, "Network lost: $network")
    }
}
```

### Narrowing down in which call a value changes

```typescript
// TypeScript — before and after each transformation
let value = rawInput;
console.log('[transform] Start:', value);

value = sanitize(value);
console.log('[transform] After sanitize:', value);

value = validate(value);
console.log('[transform] After validate:', value);

value = format(value);
console.log('[transform] After format:', value);
```

---

## ◈ V. Using Log Levels Effectively

Most logging systems and frameworks support **log levels** — a severity hierarchy that lets you control how much output is produced without changing the code.

| Level | Purpose | When to use |
|-------|---------|-------------|
| `DEBUG` | Detailed diagnostic information | During investigation; disabled in production |
| `INFO` | Normal application events | Significant operations (user login, file saved) |
| `WARN` | Unexpected but recoverable situations | Fallbacks, retries, degraded behaviour |
| `ERROR` | Failures that require attention | Exceptions, failed operations, data loss |

=== "⌨ Android Studio (Logcat)"
    ```kotlin
    Log.v(TAG, "verbose — fine-grained detail")
    Log.d(TAG, "debug — investigation output")
    Log.i(TAG, "info — normal event")
    Log.w(TAG, "warn — unexpected but handled")
    Log.e(TAG, "error — something went wrong")
    ```
    Filter Logcat by tag or level to see only the output relevant to the current investigation.

=== "⌨ Java (SLF4J / Logback)"
    ```java
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;

    private static final Logger log = LoggerFactory.getLogger(OrderService.class);

    log.debug("Processing order: id={}, total={}", order.getId(), order.getTotal());
    log.info("Order completed: id={}", order.getId());
    log.warn("Payment retry attempted: attempt={}", retryCount);
    log.error("Payment failed: orderId={}, reason={}", order.getId(), e.getMessage(), e);
    ```

=== "⌨ C# (.NET ILogger)"
    ```csharp
    private readonly ILogger<OrderService> _logger;

    _logger.LogDebug("Processing order {OrderId} with total {Total}", order.Id, order.Total);
    _logger.LogInformation("Order {OrderId} completed", order.Id);
    _logger.LogWarning("Payment retry {Attempt} for order {OrderId}", retryCount, order.Id);
    _logger.LogError(ex, "Payment failed for order {OrderId}", order.Id);
    ```

---

## ◈ VI. Knowing When to Stop

Print debugging has diminishing returns once the output exceeds what you can comfortably read. If your debug output is more than a screenful per operation, it is time to switch to a debugger — which offers the same visibility into variable state without the noise.

!!! tip "The five-minute rule"
    If you have been adding print statements for more than five minutes without identifying the problem area, switch to the IDE debugger. Set a breakpoint at the entry point of the suspect code and step through it directly. The debugger gives you the same information as print statements — but interactively, one line at a time, without requiring you to predict in advance what to print.

---

*Continue to: [Using a Debugger →](using_a_debugger.md)*

*← Back to: [Section Index](index.md)*

---

*[IDE]: Integrated Development Environment
*[API]: Application Programming Interface
*[SLF4J]: Simple Logging Facade for Java
