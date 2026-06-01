# ◎ How to Use This Guide

---

## ◎ This Is Not a Textbook

You do not need to read this guide from cover to cover. It is not a textbook and it does not build concepts in a strict linear sequence where every page depends on the one before it.

It is a **reference organised by debugging maturity** — a collection of progressively deeper content that you can enter at the level that matches where you are right now. The symbol key tells you what each piece of content is primarily aimed at. The navigation table below tells you where to start.

---

## ◎ Symbol Key

| Symbol | Level | Description |
|--------|-------|-------------|
| `◉` | **Foundational** | New programmers learning to read errors and follow a basic workflow |
| `◈` | **Investigative** | Programmers who can write code but struggle with complex or indirect bugs |
| `◆` | **Strategic** | Advanced students, final-year projects, and junior developers in the workplace |
| `◎` | **Cross-Cutting** | Concepts relevant to all levels — read these regardless of where you are |
| `▣` | **Exercise** | Practical activities — do not skip these |
| `⌨` | **Platform Note** | Environment-specific guidance for a specific IDE or tool |

These symbols appear at the top of each page and alongside individual sections within pages. They are signposts, not gatekeepers. If content at a higher level is relevant to you, read it.

---

## ◎ Suggested Starting Points by Audience

### I am a high school student (Grade 10–12)

Start with [01 · Foundations](../01_foundations/index.md), specifically [Understanding Errors](../01_foundations/understanding_errors.md) and [The Basic Debugging Workflow](../01_foundations/basic_workflow.md). The concepts you encounter there will serve you across every programming course you take from this point forward. When you encounter a specific error you do not recognise, the [Common Error Glossary](../05_appendices/error_glossary.md) in the appendices is a useful quick reference.

### I am a first-year undergraduate student

Begin with [00 · Introduction](index.md) to establish the mindset, then move through [01 · Foundations](../01_foundations/index.md) in order. Pay particular attention to [Reading Error Messages](../01_foundations/reading_error_messages.md) and [Asking for Help](../01_foundations/asking_for_help.md) — these two pages address the habits that cause the most difficulty in the first year of study.

### I am a second or third-year undergraduate student

If you are comfortable with error types and basic workflows but struggle when bugs are indirect, multi-file, or framework-related, start at [02 · Investigation](../02_investigation/index.md). If you are working on a capstone project or beginning workplace experience, [03 · Strategy](../03_strategy/index.md) addresses the level of complexity you are now operating at.

### I am a junior developer new to a team

[03 · Strategy](../03_strategy/index.md) is where you will find the most immediately relevant content — professional practices, debugging in systems, version control, bug reports, and preventive measures. [04 · Tools, Techniques & Thinking](../04_tools_techniques/index.md) offers the cross-cutting reference material that applies regardless of the stack you are working in.

### I am an educator

The [05 · Appendices](../05_appendices/index.md) contain material designed for direct classroom use: a one-page debugging checklist, trace table templates, assessment prompts, and lab exercises organised by level. The section introductions in [01 · Foundations](../01_foundations/index.md), [02 · Investigation](../02_investigation/index.md), and [03 · Strategy](../03_strategy/index.md) each state explicitly what prior knowledge is assumed, what the section covers, and what it does not cover — which may be useful for curriculum mapping.

### I am returning to this guide after a break

Use the navigation table on the [home page](../index.md). Find the section that matches what you are currently working on and start there.

---

## ◎ How the Sections Relate to Each Other

The five main sections of this guide map broadly to stages of debugging development:

**00 · Introduction** establishes the vocabulary and the mindset. It does not teach technique — it teaches the way of thinking that makes technique effective.

**01 · Foundations** is for programmers encountering errors for the first time, or for programmers who have been guessing rather than following a process. It covers error types, reading error messages, a repeatable six-step workflow, and how to ask for help.

**02 · Investigation** is for programmers who can write working code but who struggle when problems are not obvious. It introduces the tools and methods of systematic investigation — reproducibility, logging, the debugger, stack trace reading.

**03 · Strategy** is for programmers working in real systems — frameworks, teams, production environments. It covers the professional practices that go beyond finding and fixing individual bugs.

**04 · Tools, Techniques & Thinking** is a cross-cutting reference that does not fit neatly into the progression. Experienced programmers may consult it first. Beginners will find it most useful once they have context from the earlier sections.

**05 · Appendices** are practical reference material: checklists, glossaries, exercises, platform notes. These are designed to be printed, bookmarked, and used alongside the rest of the guide.

---

## ◎ On the Exercises

Every exercise section in this guide is marked `▣`. These are not optional extras. Debugging is a practical skill — it is not developed by reading about it. The exercises are the place where the concepts become habits.

If you are using this guide as part of a class, your lecturer may assign specific exercises. If you are using it independently, work through at least one exercise per section before moving on.

---

## ◎ On Platform-Specific Content

This guide is deliberately language-agnostic and IDE-agnostic in its main content. The principles of debugging apply whether you are working in Java with IntelliJ, in Kotlin with Android Studio, in C# with Visual Studio, or in JavaScript with VS Code.

Platform-specific guidance — how to read Logcat, how to set a breakpoint in Visual Studio, how to use browser DevTools — appears in two places: briefly in [IDE-Specific Notes](../02_investigation/ide_specific_notes.md) within the Investigation section, and in depth in [Appendix G · Platform Notes](../05_appendices/platform_android_studio.md).

If you are looking for specific guidance on your IDE, go directly to the relevant appendix page.

---

*Continue to: [A Brief History of Debugging →](brief_history.md)*
