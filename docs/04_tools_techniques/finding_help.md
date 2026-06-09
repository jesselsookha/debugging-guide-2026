# ◎ Finding Help

---

## ◎ No Programmer Debugs Alone Forever

Every programmer reaches a point where the evidence they have gathered, the hypotheses they have formed, and the techniques they have applied are not sufficient to resolve the problem on their own. At that point, seeking help is not a failure of skill — it is the appropriate next step.

The question is not whether to seek help. It is how to seek it well, and where to look.

This page covers the major resources available to a working programmer, in the order a thoughtful investigator would typically consult them.

---

## ◎ I. Official Documentation

**What it is:** The authoritative, maintained reference for a language, framework, library, or platform — written and published by the people who built it.

**Why it matters:** Documentation describes how a system is *intended* to work. When code behaves unexpectedly, the documentation often reveals that the behaviour is by design — and describes the correct way to achieve the intended result. A forum post or AI suggestion that contradicts the official documentation should be treated with scepticism.

**Where to find it:**

| Language / Platform | Documentation |
|--------------------|--------------|
| Java | https://docs.oracle.com/en/java/ |
| Kotlin | https://kotlinlang.org/docs/ |
| C# / .NET | https://learn.microsoft.com/en-us/dotnet/ |
| JavaScript | https://developer.mozilla.org/en-US/docs/Web/JavaScript |
| TypeScript | https://www.typescriptlang.org/docs/ |
| Python | https://docs.python.org/ |
| Android | https://developer.android.com/docs |
| React | https://react.dev |
| Angular | https://angular.io/docs |

**How to use it effectively:**

I. Search for the specific class, method, or feature you are working with — not the general topic.

II. Read the description of the method's parameters, return value, and exceptions. Many bugs are caused by misunderstanding what a method returns, what it throws, or what it expects.

III. Read the **remarks** or **notes** sections. These often document non-obvious behaviours, edge cases, and common misuses.

IV. Check the **version** you are working with. Documentation for a newer version may not apply to an older one.

!!! tip "Documentation is the first stop, not the last resort"
    Many programmers consult forums and AI tools before reading the documentation — and find the answer there when the documentation would have been faster and more accurate. Develop the habit of checking the official reference first. It is not always the most readable source, but it is the most reliable.

---

## ◎ II. StackOverflow and Developer Forums

**What they are:** Community-maintained question-and-answer platforms where programmers post specific technical questions and others answer them.

**StackOverflow** (stackoverflow.com) is the largest and most widely used. Launched in 2008,[^1] it accumulated a searchable archive of millions of programming questions and their answers. For common bugs and widely used technologies, it remains one of the fastest paths to a relevant answer.

[^1]: Stack Overflow was founded by Joel Spolsky and Jeff Atwood and launched publicly in September 2008.

**Other relevant communities:**

| Platform | Character |
|----------|----------|
| **GitHub Issues** | Bug reports and feature requests for specific open-source projects — if a library is misbehaving, check its issue tracker before posting elsewhere |
| **Reddit** (`r/learnprogramming`, `r/androiddev`, `r/dotnet`, etc.) | More conversational; useful for broader questions and discussions |
| **Discord and Slack communities** | Real-time help for specific technologies; many major frameworks maintain official servers |
| **Official forums** | Many platforms (Android, Microsoft, JetBrains) maintain official developer forums with team members participating |

**How to use StackOverflow effectively:**

I. **Search before posting.** Copy your exact error message and search. Add the language and framework name. Most common bugs have already been asked and answered.

II. **Read the accepted answer and the comments.** The accepted answer is not always the best one. Upvoted alternatives and comments often contain important caveats.

III. **Check the date.** A highly upvoted answer from 2015 may be outdated for a 2024 version of a framework. Look for answers that reference the version you are using.

IV. **When posting:** Provide a [minimal reproducible example](techniques_reference.md#vi-reduction), the exact error message, what you have already tried, and what you expected to happen. Vague questions receive vague answers — or no answers.

!!! warning "The copy-paste trap"
    StackOverflow contains many correct answers. It also contains outdated answers, incomplete answers, and answers that work for a different problem than the one being described. A solution that works for one codebase may not work for yours if the underlying context is different. Always understand what a proposed fix does before applying it.

---

## ◎ III. Knowledge Bases and Internal Documentation

**What they are:** Maintained records of known issues, solutions, architectural decisions, and post-mortems within a team or organisation.

**Why they matter:** In a professional context, the most relevant answer to a specific bug may not be on StackOverflow — it may be in your team's own documentation, written by a colleague who solved the same problem six months ago.

**What a useful knowledge base contains:**

- Known bugs and their resolutions
- Configuration requirements and common configuration mistakes
- Architectural decisions and the reasoning behind them
- Post-mortems from past incidents
- Platform-specific setup guides and troubleshooting notes

**The practice of contribution:** A knowledge base is only useful if it is maintained. The discipline of adding a note after resolving a significant bug — even a brief entry — is what makes this resource valuable over time. This was true of programmers' printed binders in the 1980s.[^2] It is equally true of Confluence pages, Notion documents, and team wikis today.

[^2]: Discussed in [A Brief History of Debugging](../00_introduction/brief_history.md) — the era of manuals and paper documentation.

**When you fix a significant bug:**

- [ ] Can you find it in the existing knowledge base? If so, verify the entry is accurate and up to date.
- [ ] If not — should it be there? If the bug cost more than an hour of investigation, the answer is almost always yes.
- [ ] Write a brief entry: the symptom, the root cause, the fix, and any conditions that trigger it.

---

## ◎ IV. Using AI Tools Effectively

AI tools integrated into development workflows — GitHub Copilot, Gemini in Android Studio, Claude Code, and standalone chat interfaces — have become a practical resource for debugging. Used well, they are fast and genuinely useful. Used poorly, they are a mechanism for bypassing understanding.

This is not new. The same dynamic existed with StackOverflow: finding the right answer quickly is valuable. Copying a solution without understanding it creates future problems. The AI era has simply made the shortcut faster and more tempting.

### What AI Tools Do Well

- Explaining error messages in plain language
- Identifying common patterns associated with a specific error type
- Suggesting hypotheses to investigate
- Explaining what a specific piece of code does
- Generating test cases for a method
- Pointing toward relevant documentation

### What AI Tools Do Poorly

- Knowing the specific architecture of your codebase
- Understanding your environment's configuration
- Identifying bugs that depend on runtime state they cannot see
- Guaranteeing the correctness of their suggestions
- Replacing the reasoning process that builds debugging skill

### The Four-Component Standard

The same four components that make a good question to a human are required for an effective AI prompt:

**I. The relevant code** — pasted as text, not as a screenshot.

**II. The exact error message** — copied in full, including the stack trace.

**III. The context** — what the code is supposed to do, what environment it is running in, what you have already tried.

**IV. Expected vs. actual** — what you expected to happen and what actually happened.

=== "Ineffective AI prompt"
    ```
    why isnt this working
    [screenshot]
    ```
    The AI receives a low-resolution image and no context. Its response will be a generic guess.

=== "Marginally better but incomplete"
    ```
    getting a NullPointerException, here's the method:

    public String formatName(User user) {
        return user.getFirstName() + " " + user.getLastName();
    }
    ```
    The method is present but no error message, no stack trace, no context about when it fails.

=== "Effective AI prompt"
    ```
    I'm building an Android app in Kotlin. When the user opens their profile,
    the app crashes with:

    java.lang.NullPointerException: Attempt to invoke virtual method
    'java.lang.String com.example.app.model.User.getFirstName()'
    on a null object reference
        at com.example.app.ui.ProfileFragment.displayUser(ProfileFragment.kt:58)

    Here is the relevant code:

    fun displayUser(userId: String) {
        val user = userRepository.findById(userId)
        nameTextView.text = "${user.firstName} ${user.lastName}"
    }

    I expected findById() to always return a User object for a logged-in user.
    The crash only happens the first time the profile screen is opened after login.
    Could the issue be that the repository hasn't finished loading when this is called?
    ```
    The AI now has the exception type, the stack trace, the code, the expected behaviour, the actual behaviour, and a hypothesis to react to. The response will be specific and actionable.

### Verifying AI Suggestions

!!! warning "Treat AI suggestions as hypotheses, not answers"
    Before applying any suggested change from an AI tool:

    I. Read the explanation — not just the code block. Understand what the change does and why.

    II. Apply it as a single change (Step V of the foundational workflow).

    III. Test and observe the result.

    IV. If the suggestion resolves the bug — can you explain why? If not, ask a follow-up question. A fix you cannot explain is temporary luck.

    V. If the suggestion does not resolve the bug — what did you learn from the new state? Use that information in the next iteration.

### When AI Is Not the Right Tool

AI tools are not useful for:

- Bugs that depend on your specific codebase context they cannot see
- Environment and configuration issues requiring access to your system
- Architectural questions that require understanding your team's design decisions
- Validating whether a fix is appropriate for your specific project structure

For these, official documentation, a colleague with codebase knowledge, or a human mentor is more effective.

---

## ◎ V. When to Contact Official Support

Some bugs require escalation beyond community resources — to the platform provider, the framework maintainers, or a paid support channel.

**When community resources are not sufficient:**

- The bug appears to be in the platform or framework itself, not in your code
- You have searched documentation, StackOverflow, GitHub Issues, and found no relevant answer
- The bug is security-related and should be reported privately before public disclosure
- You are working in a licensed enterprise environment with a support contract

**How to report effectively:**

The same principles apply as for any technical question. A bug report to a framework maintainer on GitHub Issues should include: the version of the framework, the exact steps to reproduce, a minimal reproducible example, the expected behaviour, the actual behaviour, and relevant log output. Maintainers receive many reports — a well-structured report that can be acted on immediately is far more likely to receive a timely response than a vague description.

---

*Continue to: [Developing Debugging Skills Over Time →](developing_skills.md)*

*← Back to: [Section Index](index.md)*

---

*[IDE]: Integrated Development Environment
*[API]: Application Programming Interface
*[HTTP]: Hypertext Transfer Protocol
