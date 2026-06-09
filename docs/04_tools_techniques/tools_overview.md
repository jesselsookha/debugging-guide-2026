# ◎ Debugging Tools Overview

---

## ◎ Knowing Which Tool to Reach For

A programmer who knows only one debugging tool will reach for it in every situation — whether it is appropriate or not. A programmer who knows the landscape of available tools can match the right tool to the problem at hand.

This page surveys the categories of tools available to a working developer. It is not exhaustive — the tooling ecosystem is large and changes over time — but it covers the categories that matter most in everyday development and the specific examples most relevant to the environments used in this guide.

!!! note "Tools support techniques — they do not replace thinking"
    No tool finds bugs on its own. A debugger that shows you the wrong value does not tell you why the value is wrong. A profiler that shows slow code does not tell you how to fix it. Tools make evidence visible. Reasoning turns evidence into understanding.

---

## ◎ I. IDE Debuggers

An integrated debugger is built into the development environment and operates directly on your source code. It is the primary debugging tool for the vast majority of development work.

All major IDEs provide the same core set of capabilities — the names and keyboard shortcuts differ, but the underlying operations are identical. See [IDE-Specific Notes](../02_investigation/ide_specific_notes.md) for the specific controls in each environment.

| Capability | What it does |
|------------|-------------|
| Line breakpoints | Pause execution at a specific line |
| Conditional breakpoints | Pause only when a condition is true |
| Method breakpoints | Pause at entry or exit of a specific method |
| Exception breakpoints | Pause when a specific exception type is thrown |
| Step over | Advance one line, stay in the current method |
| Step into | Enter the called method |
| Step out | Finish the current method, return to caller |
| Continue | Run to the next breakpoint |
| Run to cursor | Run to wherever the cursor is placed |
| Variable inspection | See all variables in scope at the current position |
| Watch expressions | Monitor custom expressions throughout a session |
| Call stack navigation | View and navigate the full execution path |
| Expression evaluation | Evaluate any expression in the current context |

**Primary use cases:** Single-developer investigation of bugs in local development. All levels of debugger — foundational through strategic.

---

## ◎ II. Standalone Debuggers

Standalone debuggers operate independently of an IDE. They are primarily used for lower-level debugging — systems programming, compiled binaries, and environments where an IDE is not available or appropriate.

| Tool | Language / Platform | Notes |
|------|--------------------|----|
| **GDB** (GNU Debugger) | C, C++, and other compiled languages | Command-line interface; widely available on Unix/Linux systems; supports remote debugging |
| **LLDB** | C, C++, Swift, Objective-C | Modern alternative to GDB; default debugger in Xcode; also available independently |
| **WinDbg** | Windows (kernel and user mode) | Used for advanced Windows debugging including crash dump analysis |
| **rr** | C, C++ on Linux | Time-travel debugging — records execution and allows replaying backward; powerful for intermittent bugs |

!!! info "When standalone debuggers are relevant"
    For most students and junior developers working in Java, Kotlin, C#, JavaScript, or TypeScript, an IDE debugger is sufficient. Standalone debuggers become relevant when working with C or C++ at a systems level, or when investigating crash dumps from production systems. They are worth being aware of — and the concepts (breakpoints, stepping, stack inspection) transfer directly from IDE debuggers.

---

## ◎ III. Logging Utilities

In production environments, an interactive debugger is rarely available. Logs are the primary — and sometimes only — evidence of what happened.

### Development Logging

During development, logging libraries provide structured output with severity levels, timestamps, and context. These are the tools that replace `System.out.println()` in production code.

| Platform | Library | Notes |
|----------|---------|-------|
| Java | SLF4J + Logback, Log4j2 | SLF4J provides a standard API; Logback and Log4j2 are implementations |
| C# / .NET | Microsoft.Extensions.Logging, NLog, Serilog | ILogger abstraction built into .NET; Serilog supports structured logging |
| Kotlin / Android | `android.util.Log` | Built in; filter by tag and level in Logcat |
| JavaScript / Node.js | Winston, Pino, Morgan (HTTP) | Winston is general-purpose; Pino prioritises performance |
| TypeScript | Winston, Pino (same as Node.js) | Type definitions available for both |
| Python | `logging` (standard library) | Built in; configurable levels, formatters, and handlers |

### Production Log Management

When applications are deployed, logs from multiple instances, services, and time periods must be collected, stored, and searched.

| Tool / Stack | Purpose |
|-------------|---------|
| **ELK Stack** (Elasticsearch, Logstash, Kibana) | Collect, transform, store, and visualise logs; widely used in enterprise environments |
| **Loki + Grafana** | Lightweight log aggregation designed for cloud-native environments |
| **Splunk** | Enterprise log analysis and monitoring; powerful search and alerting |
| **Datadog** | Full observability platform; logs, metrics, traces, and dashboards |
| **AWS CloudWatch** | Log management for applications running on AWS infrastructure |

!!! note "Production logging is not debugging logging"
    Debug logs are temporary — written during investigation and removed after. Production logs are permanent — structured, levelled, and maintained as part of the application's architecture. The distinction matters: debug output left in production code creates noise, potentially exposes sensitive information, and signals that investigation output was not cleaned up.

---

## ◎ IV. Profilers

A profiler measures the runtime behaviour of a program — how much time each method takes, how much memory is allocated, where CPU cycles are spent. Profilers are used when the bug is not a crash or wrong output, but **performance**: the program is correct but too slow, too memory-hungry, or too resource-intensive.

| Tool | Platform | What it measures |
|------|---------|-----------------|
| **Android Studio Profiler** | Android | CPU, memory, network, energy — all in one tool |
| **Visual Studio Diagnostic Tools** | C# / .NET | CPU usage, memory, and performance events |
| **IntelliJ IDEA Profiler** | Java / Kotlin (JVM) | CPU sampling, memory allocation, thread activity |
| **YourKit** | Java, .NET | Detailed CPU and memory profiling; commercial tool |
| **Chrome DevTools (Performance tab)** | JavaScript | JavaScript execution timeline, rendering, memory |
| **Node.js built-in profiler** | Node.js | CPU profiling via `--prof` flag |
| **Valgrind** | C, C++ | Memory error detection — leaks, invalid reads/writes |
| **Instruments** (Xcode) | iOS, macOS | CPU, memory, energy, and disk profiling |

**When to use a profiler:**

- The application is slower than expected for a given input size
- Memory usage grows over time without an obvious cause (suspected memory leak)
- A specific operation takes disproportionately long compared to others
- The application uses more battery or CPU than expected on mobile

!!! tip "Profile before optimising"
    The most common mistake in performance debugging is optimising the wrong thing. Without a profiler, it is very difficult to know where the actual bottleneck is — and programmers' intuition about which code is slow is frequently wrong. Profile first, then optimise the thing the profiler identifies.

---

## ◎ V. Static Analysis Tools

Static analysis examines source code without running it. It catches a specific class of bugs — those that can be identified by analysing the code's structure, types, and patterns — before any test or execution.

**What static analysis finds:**

- Potential null dereferences
- Unused variables, methods, or imports
- Unreachable code
- Common security vulnerabilities (injection patterns, hardcoded credentials)
- Type errors (in dynamically typed languages with type checking enabled)
- Complexity warnings (methods that are too long or too deeply nested)

| Tool | Language | Category |
|------|---------|---------|
| **Android Lint** | Kotlin / Java (Android) | IDE-integrated; runs automatically during build |
| **Detekt** | Kotlin | Static analysis and code style |
| **SpotBugs** | Java | Finds likely bugs by pattern matching |
| **SonarQube** | Multi-language | Enterprise-grade code quality platform |
| **ESLint** | JavaScript / TypeScript | Linting and style enforcement |
| **TypeScript compiler** (`tsc --strict`) | TypeScript | Type checking as static analysis |
| **Pylint / mypy** | Python | Linting and optional type checking |
| **Checkstyle** | Java | Style and formatting rules |

!!! tip "Integrate static analysis into your workflow"
    Static analysis is most valuable when it runs automatically — on every save, every build, or every pull request — rather than only when you remember to run it manually. Most IDE plugins for the tools above provide real-time feedback as you type.

---

## ◎ VI. Network and Protocol Tools

For applications that communicate over a network — web applications, mobile apps, APIs, microservices — bugs sometimes originate in the communication layer: the request was malformed, the response was not what was expected, or a connection timed out silently.

| Tool | Purpose |
|------|---------|
| **Browser DevTools (Network tab)** | Inspect HTTP requests and responses in real time; available in all major browsers |
| **Postman** | Send HTTP requests manually to test API endpoints independently of your application code |
| **curl** | Command-line HTTP client; useful for scripted API testing and CI environments |
| **Wireshark** | Capture and inspect raw network traffic at the packet level |
| **tcpdump** | Command-line packet capture on Unix/Linux systems |
| **Charles Proxy / mitmproxy** | HTTP/HTTPS proxy that captures and displays traffic from mobile apps and browsers |

**When network tools matter:**

- An API call returns an unexpected result and you cannot tell whether the request was sent correctly
- A mobile app behaves differently on a real device than on an emulator
- SSL/TLS errors occur and the certificate chain needs to be inspected
- A timeout or connection failure occurs and you need to know whether a request reached the server

---

## ◎ VII. Version Control as a Debugging Tool

Version control systems — primarily Git — are debugging tools in their own right. They provide a timestamped record of every change, making it possible to identify when a bug was introduced and what changed at that point.

The primary debugging use of Git is `git bisect`. Full coverage of this technique and other version-control debugging practices is in [Professional Debugging Practices](../03_strategy/professional_practices.md).

---

*Continue to: [Finding Help →](finding_help.md)*

*← Back to: [Section Index](index.md)*

---

*[IDE]: Integrated Development Environment
*[API]: Application Programming Interface
*[CPU]: Central Processing Unit
*[HTTP]: Hypertext Transfer Protocol
*[SSL]: Secure Sockets Layer
*[TLS]: Transport Layer Security
*[AWS]: Amazon Web Services
