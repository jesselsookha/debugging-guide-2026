# Debugging Guide 2026

[![Deploy to GitHub Pages](https://github.com/jesselsookha/debugging-guide-2026/actions/workflows/deploy.yml/badge.svg)](https://github.com/jesselsookha/debugging-guide-2026/actions/workflows/deploy.yml)
[![License: CC BY-NC-SA 4.0](https://img.shields.io/badge/License-CC%20BY--NC--SA%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by-nc-sa/4.0/)

A practical, progressive guide to debugging software — from first error message to professional practice.

Published at: **https://jesselsookha.github.io/debugging-guide-2026/**

---

## About This Guide

This guide teaches debugging as a **transferable skill**, not a collection of language-specific tricks. It is designed for:

- **High school students** (Grade 10–12) encountering programming errors for the first time
- **Undergraduate students** at any stage of their studies
- **Junior developers** entering the workplace and learning to work in a team context
- **Educators** seeking a structured resource to reference in lectures and practicals

The guide is organised by **debugging maturity**, not academic year or programming language.

---

## Symbol Key

| Symbol | Level | Description |
|--------|-------|-------------|
| `◉` | **Foundational** | New programmers learning to read errors and follow a basic workflow |
| `◈` | **Investigative** | Programmers who can write code but struggle with complex or indirect bugs |
| `◆` | **Strategic** | Advanced students, final-year projects, and junior developers |
| `◎` | **Cross-Cutting** | Concepts and techniques that apply to all levels |
| `▣` | **Exercise** | Practical activities to apply and test understanding |
| `⌨` | **Platform Note** | Environment-specific guidance (IDE, terminal, or platform) |

---

## Structure

```
debugging-guide-2026/
│
├── 00 · Introduction
│   ├── What is Debugging?
│   ├── The Debugging Mindset
│   ├── How to Use This Guide
│   └── A Brief History of Debugging
│
├── 01 · Foundations — The Foundational Debugger
│   ├── Understanding Errors
│   ├── Reading Error Messages
│   ├── The Basic Debugging Workflow
│   ├── Asking for Help (Human or AI)
│   └── Foundation Exercises
│
├── 02 · Investigation — The Investigative Debugger
│   ├── The Investigative Mindset
│   ├── Core Investigation Skills
│   ├── Print and Log Debugging
│   ├── Using a Debugger
│   ├── Stack Trace Analysis
│   ├── IDE-Specific Notes (Summary)
│   └── Investigation Exercises
│
├── 03 · Strategy — The Strategic Debugger
│   ├── Debugging in Systems
│   ├── Professional Debugging Practices
│   ├── Preventive Measures
│   └── Strategy Exercises
│
├── 04 · Tools, Techniques & Thinking  (Cross-Cutting Reference)
│   ├── Debugging Techniques (Quick Reference)
│   ├── Debugging Tools Overview
│   ├── Finding Help
│   └── Developing Debugging Skills Over Time
│
└── 05 · Appendices
    ├── A · One-Page Debugging Checklist
    ├── B · Trace Tables
    ├── C · Common Error Glossary
    ├── D · Debugging Anti-Patterns
    ├── E · Assessment Prompts
    ├── F · Lab Exercises by Level
    ├── G · Platform Notes
    │   ├── Android Studio / Logcat
    │   ├── VS Code / Terminal
    │   ├── Visual Studio / Watch Windows
    │   ├── NetBeans / IntelliJ
    │   └── Web / Browser DevTools
    └── H · Acknowledgments & References
```

---

## Building Locally

This guide is built with [MkDocs](https://www.mkdocs.org/) and the [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/) theme.

```bash
# Clone the repository
git clone https://github.com/jesselsookha/debugging-guide-2026.git
cd debugging-guide-2026

# Install dependencies
pip install -r requirements.txt

# Serve locally
mkdocs serve
```

Then open `http://127.0.0.1:8000` in your browser.

---

## Deployment

This guide deploys automatically to GitHub Pages on every push to `main`, using the GitHub Actions workflow in `.github/workflows/deploy.yml`.

To enable on a new repository:

1. Go to **Settings → Pages**
2. Under **Source**, select **GitHub Actions**
3. Push to `main` — the workflow handles the rest

---

## References

| Source | URL |
|--------|-----|
| freeCodeCamp — "How to Ask a Great (Technical) Question" | https://www.freecodecamp.org/news/how-to-ask-good-technical-questions/ |
| IBM Think — "What is Debugging?" | https://www.ibm.com/think/topics/debugging |
| AWS — "What is Debugging?" | https://aws.amazon.com/what-is/debugging/ |
| Upsun — "Essential Debugging Techniques for Developers" | https://upsun.com/blog/debugging-techniques-for-developers/ |
| SonarSource — "Debugging" | https://www.sonarsource.com/resources/library/debugging/ |
| Julia Evans (jvns.ca) — "A Way to Categorize Debugging Skills" | https://jvns.ca/blog/2022/08/30/a-way-to-categorize-debugging-skills/ |
| InfoWorld — "Learning and Improving Your Debugging Skills" | https://www.infoworld.com/article/2164328/learning-and-improving-your-debugging-skills.html |
| Stanford CS107 — "Debugging Guide" | https://web.stanford.edu/class/cs107/resources/debugging.html |
| Cornell CS1109 — "Lecture 10: Testing and Debugging" | https://www.cs.cornell.edu/courses/cs1109/2024su/lectures/lec10_testing_debugging.pdf |
| Princeton COS126 — "Debugging" (IntelliJ guide) | https://introcs.cs.princeton.edu/java/15inout/debugging.html |
| Stellenbosch CS144 — "Debugging Tips" | https://cs.sun.ac.za/courses/cs144/Debugging/ |
| Claude (Anthropic) — Content development assistance | https://claude.ai |

---

## License

This work is licensed under a [Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License](https://creativecommons.org/licenses/by-nc-sa/4.0/).

- **Share** — copy and redistribute the material in any medium or format
- **Adapt** — remix, transform, and build upon the material
- **Attribution** — give appropriate credit and indicate if changes were made
- **NonCommercial** — not for commercial use
- **ShareAlike** — distribute contributions under the same license

---

## Contributing

This guide is a living document. To contribute:

1. Fork the repository
2. Make your changes
3. Submit a pull request

Please ensure contributions align with the guide's tone, structure, and license.

---

**Debugging is not about fixing mistakes. It is about understanding systems.**

*— Debugging Guide 2026*
