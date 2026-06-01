# ◉ Asking for Help (Human or AI)

---

## ◉ Asking for Help Is Not a Weakness

Knowing when and how to ask for help is a professional skill. Every programmer — from student to senior engineer — reaches points where an outside perspective is the most efficient path forward.

The question is not *whether* to ask. It is *how* to ask.

A well-constructed question communicates that you have already engaged with the problem seriously. It gives the person or tool being asked exactly the information needed to help effectively. It almost always produces a useful response faster than a vague one.

A poorly constructed question does the opposite: it transfers the problem without providing the context needed to solve it, and it signals that the work of understanding the error has not yet been done.

!!! quote "The standard for asking"
    Before asking anyone — human or AI — for help, you should be able to answer: *What did I expect? What actually happened? What have I already tried?* If you cannot answer those three questions, you are not ready to ask yet.

---

## ◉ I. What Makes a Good Question

A good technical question contains four components. All four are required — not optional extras.

### Component I — The Code

The relevant section of code where the problem occurs. Not the entire project. Not a screenshot of the screen. The actual text of the code, copied and pasted.

Why text and not a screenshot? Because:

- Screenshots cannot be searched, copied, or run
- Text can be read by AI tools at full accuracy
- Syntax highlighting and analysis tools work on text
- The person helping can copy, modify, and test your code directly

!!! danger "Screenshots of code are not code"
    A screenshot of an error message is better than nothing. A screenshot of code is almost useless. Always provide the actual text.

### Component II — The Error Message

The complete, exact error message. Copy it from the output window, the terminal, the Logcat panel — wherever it appeared. Include:

- The full error type and description
- The stack trace if one was produced
- The file name and line number reported

Do not paraphrase the error. Do not summarise it. Copy the exact text.

### Component III — The Context

What were you trying to do? What does this code or function supposed to accomplish? What input were you using when the error occurred?

Context transforms an isolated code snippet into a solvable problem. Without it, the person helping is guessing at your intent.

### Component IV — Expected vs. Actual

State explicitly:
- What you expected the program to do
- What it actually did instead

This is the most frequently omitted component, and its absence is the most common reason a question fails to get a useful answer.

---

## ◉ II. Anatomy of a Good Question

Here is the same problem asked badly and then asked well.

!!! failure "A question that cannot be answered effectively"
    *"My app crashes when I press the button. Here's a screenshot. How do I fix it?"*

    Problems with this:
    - No code — cannot be analysed, copied, or run
    - No error text — the screenshot may not show the full message
    - No context — which button? What is the expected behaviour?
    - No expected vs. actual — "crashes" is not a description of expected behaviour

!!! success "The same question, asked effectively"
    *"I'm building an Android app in Kotlin. When the user presses the Submit button, the app crashes. I expected it to validate the input and navigate to the next screen.*

    *Here is the error from Logcat:*
    ```
    FATAL EXCEPTION: main
    java.lang.NullPointerException: Attempt to invoke virtual method
    'java.lang.String android.widget.EditText.getText()' on a null object reference
        at com.example.myapp.MainActivity.onSubmitClicked(MainActivity.kt:47)
    ```

    *Here is the relevant code:*
    ```kotlin
    fun onSubmitClicked(view: View) {
        val input = usernameField.text.toString()
        if (input.isNotEmpty()) {
            navigateToNextScreen()
        }
    }
    ```

    *`usernameField` is declared as a class property. I initialised it in `onCreate()` using `findViewById`. Could the issue be that `onCreate()` hasn't run by the time the button is pressed, or is `usernameField` not being found correctly?"*

    This question provides: the language and platform, expected behaviour, the full error, the relevant code, and a hypothesis to react to. It can be answered immediately and precisely.

---

## ◉ III. Asking a Human for Help

When asking a lecturer, teaching assistant, or colleague:

**Come with your work already done.** Have the workflow steps completed before you approach them. Know the error type. Know the line number. Know what you expected. Know what you tried. The conversation should be: *"I found X, I tried Y, I think Z might be the cause — am I reading this right?"* — not *"it's broken, can you fix it?"*

**Show your reasoning, not just your problem.** A good debugging conversation is a collaboration. Share your hypothesis. It gives the person helping you something concrete to confirm, refine, or redirect.

**Ask about your understanding, not just the answer.** *"Is my reading of this stack trace correct?"* is a more valuable question than *"what's wrong with my code?"* — because the first one builds skill.

!!! tip "The rubber duck method"
    Explain your problem out loud — to a classmate, to a colleague, or to an inanimate object on your desk. The act of articulating the problem in full sentences forces your brain to structure it, and frequently reveals the answer before you finish speaking. This technique has a name: rubber duck debugging. It works. Use it.

---

## ◉ IV. Using AI Tools Effectively

AI tools integrated into development environments — GitHub Copilot, Gemini in Android Studio, Claude, and others — can be genuinely useful debugging assistants. They can explain error messages, suggest likely causes, and propose fixes.

They can also produce plausible-sounding but incorrect responses, suggest fixes that solve the symptom while missing the root cause, and generate code that introduces new problems.

**The same four components apply when asking AI as when asking a human.** An AI tool given a screenshot and nothing else will attempt to help — but its response will be constrained by the incompleteness of what it was given. The more precisely you provide code, error, context, and expected vs. actual, the more precisely it can respond.

=== "Ineffective AI prompt"
    ```
    sort this out
    [screenshot attached]
    ```
    The AI receives a low-resolution image with no code text, no error text, and no context. Its response will be a generic guess.

=== "Marginally better but still incomplete"
    ```
    why is this wrong?

    val input = usernameField.text.toString()
    if (input.isNotEmpty()) {
        navigateToNextScreen()
    }
    ```
    Code is present, but no error message, no context, no expected vs. actual. The AI has to guess at the problem.

=== "Effective AI prompt"
    ```
    I'm developing an Android app in Kotlin. When the Submit button is pressed,
    the app crashes with this error:

    java.lang.NullPointerException: Attempt to invoke virtual method
    'java.lang.String android.widget.EditText.getText()' on a null object reference
        at com.example.myapp.MainActivity.onSubmitClicked(MainActivity.kt:47)

    Here is the relevant code:

    fun onSubmitClicked(view: View) {
        val input = usernameField.text.toString()
        if (input.isNotEmpty()) {
            navigateToNextScreen()
        }
    }

    usernameField is declared as a lateinit var and initialised in onCreate() using
    findViewById. I expected it to be available when the button is pressed.
    Could the issue be related to how or when it was initialised?
    ```
    The AI now has everything needed to give a precise, accurate response.

### Understanding AI Responses

!!! warning "Verify before you apply"
    AI suggestions should be treated as hypotheses, not answers. Before applying any suggested change:

    - Read the explanation, not just the code
    - Make sure you understand what the change does and why
    - Apply it as a single change and test (Step V of the workflow)
    - If you cannot explain the change, do not apply it yet — ask a follow-up question until you can

A fix you cannot explain is temporary luck. The next version of the same bug will find you unprepared.

### When AI Is Not the Right Tool

AI tools are not useful for:

- Problems that require access to your full project context (they only see what you provide)
- Environment configuration issues (they cannot see your system)
- Bugs that depend on runtime state at a specific moment
- Deciding *whether* a fix is architecturally correct for your project

For these, official documentation, platform forums, or a human colleague will be more effective.

---

## ◉ V. The Help-Asking Checklist

Before asking anyone or anything for help, confirm:

- [ ] I have read the full error message
- [ ] I know the error type and can name it
- [ ] I know the file and line number where the error occurred
- [ ] I can state clearly what I expected vs. what actually happened
- [ ] I have the relevant code ready as text (not a screenshot)
- [ ] I have already attempted at least one deliberate change based on my reading
- [ ] I can explain what I tried and why it did not resolve the problem

If all seven boxes are checked, you are ready to ask — and your question will be answered far more effectively.

---

*Continue to: [Foundation Exercises →](foundation_exercises.md)*

*← Back to: [Section Index](index.md)*

---

*[AI]: Artificial Intelligence
*[IDE]: Integrated Development Environment
