# Debugging Guide 2026

[![Deploy to GitHub Pages](https://github.com/jesselsookha/debugging-guide-2026/actions/workflows/deploy.yml/badge.svg)](https://github.com/jesselsookha/debugging-guide-2026/actions/workflows/deploy.yml)
[![License: CC BY-NC-SA 4.0](https://img.shields.io/badge/License-CC%20BY--NC--SA%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by-nc-sa/4.0/)

A practical, progressive guide to debugging software — from first error message to professional practice.

Published at: **https://jesselsookha.github.io/debugging-guide-2026/**

---

## About This Guide

This guide teaches debugging as a **transferable skill**, not a collection of language-specific tricks. It is written for:

- **High school students** (Grade 10–12) encountering programming errors for the first time
- **Undergraduate students** at any stage of their studies
- **Junior developers** entering the workplace and learning to work in a team context
- **Educators** — teachers and lecturers — seeking a structured resource to reference in lectures, practicals, and assessment design

The guide is organised by **debugging maturity**, not academic year or programming language, so it can be used as a shared reference across a CS/IT department — from introductory programming through to capstone and workplace-readiness modules.

---

## A Note for Educators

If you teach programming — at secondary or tertiary level — this guide is intended to be used alongside your existing curriculum, not instead of it. The appendices in particular ([Assessment Prompts](https://jesselsookha.github.io/debugging-guide-2026/05_appendices/assessment_prompts/) and [Lab Exercises by Level](https://jesselsookha.github.io/debugging-guide-2026/05_appendices/lab_exercises/)) are designed to be lifted directly into practicals, tutorials, or formal assessment.

I would also welcome the opportunity to **run a guest lecture, workshop, or staff development session** based on this guide — for lecturers, teaching teams, or directly with students. If this is of interest, please reach out via the contact details on my [LinkedIn profile](https://www.linkedin.com/in/jesselsookha/) or open a discussion on this repository.

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

References are presented in Harvard style. Full citation details, including access dates and secondary sources, are available in [Appendix H · Acknowledgments & References](https://jesselsookha.github.io/debugging-guide-2026/05_appendices/references/).

### Educational and Conceptual Sources

AWS (Amazon Web Services) (n.d.) *What is debugging?* Available at: https://aws.amazon.com/what-is/debugging/ (Accessed: 2025).

Cornell University, Department of Computer Science (2024) *CS1109: Lecture 10 — Testing and debugging*. Available at: https://www.cs.cornell.edu/courses/cs1109/2024su/lectures/lec10_testing_debugging.pdf (Accessed: 2025).

Evans, J. (2022) *A way to categorize debugging skills*. Available at: https://jvns.ca/blog/2022/08/30/a-way-to-categorize-debugging-skills/ (Accessed: 2025).

freeCodeCamp (n.d.) *How to ask good technical questions*. Available at: https://www.freecodecamp.org/news/how-to-ask-good-technical-questions/ (Accessed: 2025).

IBM (n.d.) *What is debugging?* IBM Think. Available at: https://www.ibm.com/think/topics/debugging (Accessed: 2025).

InfoWorld (n.d.) *Learning and improving your debugging skills*. Available at: https://www.infoworld.com/article/2164328/learning-and-improving-your-debugging-skills.html (Accessed: 2025).

Princeton University, Department of Computer Science (n.d.) *Debugging* (COS126: Introduction to Computer Science). Available at: https://introcs.cs.princeton.edu/java/15inout/debugging.html (Accessed: 2025).

SonarSource (n.d.) *Debugging*. Available at: https://www.sonarsource.com/resources/library/debugging/ (Accessed: 2025).

Stanford University, Department of Computer Science (n.d.) *CS107 debugging guide*. Available at: https://web.stanford.edu/class/cs107/resources/debugging.html (Accessed: 2025).

Stellenbosch University, Department of Computer Science (n.d.) *CS144 debugging tips*. Available at: https://cs.sun.ac.za/courses/cs144/Debugging/ (Accessed: 2025).

University of California, Berkeley (n.d.) *CS61B debugging guide*. Available at: https://www.learncs.site/docs/curriculum-resource/cs61b/cs61b_en/guides/debugging (Accessed: 2025).

University of New South Wales, School of Computer Science and Engineering (n.d.) *Welcome to learning debugging*. Available at: https://cgi.cse.unsw.edu.au/~learn/debugging/ (Accessed: 2025).

University of Washington, Paul G. Allen School of Computer Science and Engineering (2021) *Debugging 101: Common compiler and runtime bugs* (CSE142, Spring 2021 lecture materials). Available at: https://courses.cs.washington.edu/courses/cse142/21sp/files/debugging.pdf (Accessed: 2025).

Upsun (n.d.) *Essential debugging techniques for developers: A complete guide*. Available at: https://upsun.com/blog/debugging-techniques-for-developers/ (Accessed: 2025).

### Referenced Within Sources

Agans, D.J. (2002) *Debugging: The 9 indispensable rules for finding even the most elusive software and hardware problems*. New York: AMACOM.

Ko, A.J. and Myers, B.A. — research on novice and expert debugging strategies, as discussed in Evans, J. (2022) *A way to categorize debugging skills*. Available at: https://jvns.ca/blog/2022/08/30/a-way-to-categorize-debugging-skills/ (Accessed: 2025).

### Historical Reference

Smithsonian National Museum of American History (n.d.) *Log book with computer bug*. Available at: https://americanhistory.si.edu/collections/object/nmah_334663 (Accessed: 2025).

### Tools, Platforms, and Products Referenced in This Guide

This guide refers extensively to the development environments, debugging tools, and platforms listed below. They are acknowledged here as the products discussed throughout the appendices and platform notes.

Google (n.d.) *Android Studio*. Available at: https://developer.android.com/studio (Accessed: 2025).

Google (n.d.) *Chrome DevTools*. Available at: https://developer.chrome.com/docs/devtools/ (Accessed: 2025).

JetBrains (n.d.) *IntelliJ IDEA*. Available at: https://www.jetbrains.com/idea/ (Accessed: 2025).

Microsoft (n.d.) *Visual Studio*. Available at: https://visualstudio.microsoft.com/ (Accessed: 2025).

Microsoft (n.d.) *Visual Studio Code*. Available at: https://code.visualstudio.com/ (Accessed: 2025).

Mozilla (n.d.) *Firefox DevTools*. Available at: https://firefox-source-docs.mozilla.org/devtools-user/ (Accessed: 2025).

Oracle (n.d.) *NetBeans*. Available at: https://netbeans.apache.org/ (Accessed: 2025).

Stack Overflow (n.d.) *Stack Overflow*. Available at: https://stackoverflow.com/ (Accessed: 2025).

### AI Assistance

Anthropic (2025) *Claude* [Large language model]. Available at: https://claude.ai (Accessed: 2025).

This guide was developed with the assistance of Claude (Anthropic), used for content drafting, structuring, code example generation, and editorial review across all sections. All AI-assisted content was reviewed, verified, and adapted by the author before inclusion — consistent with the guide's own principles on AI use, covered in [Asking for Help (Human or AI)](https://jesselsookha.github.io/debugging-guide-2026/01_foundations/asking_for_help/) and [Finding Help](https://jesselsookha.github.io/debugging-guide-2026/04_tools_techniques/finding_help/).

### Tooling and Publishing

MkDocs (n.d.) *MkDocs: Project documentation with Markdown*. Available at: https://www.mkdocs.org/ (Accessed: 2025).

Material for MkDocs (n.d.) *Material for MkDocs*. Available at: https://squidfunk.github.io/mkdocs-material/ (Accessed: 2025).

### How to Cite This Guide

Sookha, J. (2026) *Debugging Guide 2026*. Available at: https://jesselsookha.github.io/debugging-guide-2026/ (Accessed: [date]).

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

## Collaboration and Outreach

This guide grew out of classroom observation and conversations with students and lecturers — and it is intended to keep growing the same way. If you are:

- A **lecturer or teacher** who would like to adopt, adapt, or contribute to this guide for your own modules
- A **student or junior developer** who has found a gap, an unclear explanation, or a missing platform note
- Interested in a **guest lecture or workshop** on debugging pedagogy, AI-assisted learning, or this guide specifically

please get in touch via [LinkedIn](https://www.linkedin.com/in/jesselsookha/) or open an issue/discussion on the [GitHub repository](https://github.com/jesselsookha/debugging-guide-2026).

---

**Debugging is not about fixing mistakes. It is about understanding systems.**

*— Debugging Guide 2026*
