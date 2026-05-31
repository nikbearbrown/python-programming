# Introduction to Python Programming with Claude

## Title
Introduction to Python Programming with Claude

## Subtitle
Statements, expressions, objects, decisions, loops, functions, modules, strings, lists, dictionaries, classes, recursion, inheritance, files, and a first taste of data science — taught alongside how to ask an LLM for code and how to verify what it gives you.

## Author
Nik Bear Brown.

## Audience

This book is written for the undergraduate taking their first college programming course in Python — typically a freshman or sophomore in a CS, data-science, engineering, business analytics, or quantitative-social-science track. They have not programmed before, or have only fiddled with code in high school. They have been told (correctly) that AI code assistants will be part of their working life, and they are appropriately confused about what that means for learning to program.

Specifically:

- CS-100 and Engineering-100 students taking their required first programming course.
- Data-science freshmen who will spend the next four years writing Python.
- Business analytics, economics, and quantitative-social-science majors who need Python for their methods coursework.
- Adult learners returning to school for a working programming knowledge.
- Career changers who suspect that learning to code now is necessary and have heard that LLMs make it easier.

This is not an "AI replaces learning to code" book. It is the standard CS-1 Python sequence — statements, expressions, control flow, functions, data structures, classes, recursion, files, basic data science — written so that every code example doubles as a lesson in *how to elicit code from Claude* and *how to verify what comes back*. The student leaves the course able to write Python from scratch AND able to use an LLM as a competent collaborator.

## Scope

The fifteen chapters cover, in order:

1. **Statements** — input/output, variables, assignment, basic arithmetic, errors, a short history of Python.
2. **Expressions** — operators, precedence, types, casting, math operations.
3. **Objects** — values as objects, methods, mutability, identity.
4. **Decisions** — conditionals, comparison, Boolean operators, branching logic.
5. **Loops** — `while` and `for`, range, accumulator patterns, loop control.
6. **Functions** — definition, parameters, return, scope, default arguments.
7. **Modules** — `import`, the standard library, writing your own modules.
8. **Strings** — indexing, slicing, methods, formatting, encoding.
9. **Lists** — indexing, slicing, mutation, iteration, comprehensions.
10. **Dictionaries** — keys, values, iteration, the workhorse of Python.
11. **Classes** — attributes, methods, `__init__`, encapsulation.
12. **Recursion** — base case, recursive case, the call stack, tree problems.
13. **Inheritance** — extending classes, overriding, polymorphism, `super()`.
14. **Files** — reading, writing, context managers, paths, JSON and CSV.
15. **Data science** — a first pass at NumPy, pandas, and matplotlib.

Out of scope: web frameworks (Flask/Django), async, type-checking with mypy, packaging and pip, machine learning, deep learning. Those belong in the courses students will take if they decide they want more.

## Voice notes for this book

The Attenborough × Feynman v1.1 voice applies, with adjustments for a code-heavy book.

**Cold opens earn their place.** A chapter on functions opens with a calculator on a phone — every button is a function. A chapter on classes opens at a vending machine — same machine, many products, behavior controlled by one design. A chapter on recursion opens with a Russian doll. The scenes are short and sensory; the code arrives once the situation has earned attention.

**Code density.** Higher than a math book. Most concepts are taught by showing code that does the thing, then explaining what each line does. Every code block is runnable. Every code block has been mentally executed before it appears on the page. No pseudocode unless explicitly labeled as pseudocode.

**Notation taught.** Python syntax gets glossed on first use. The first time a student sees `for x in range(10):` they get told what `range` is, what the colon does, what the indentation enforces. Don't assume — *teach*.

**Grounded examples.** Not `def foo(x): return x * 2`. Instead: `def fahrenheit_to_celsius(f): return (f - 32) * 5/9`. Concrete domain, concrete number, concrete reason this code exists. When the source uses a generic example, replace with a domain example a student would recognize.

**Errors are taught explicitly.** Every chapter shows at least one common error message, names what it means, and shows how to fix it. `IndentationError`, `NameError`, `TypeError`, `IndexError`, `KeyError`, `AttributeError` — these are not edge cases, they are the daily reality of writing Python.

## The Claude integration — a chapter-wide pattern

This is what distinguishes this book. **For every substantive code example, the chapter produces three artifacts together:**

1. **The code itself**, in a runnable Python block.
2. **Prompt for Claude** — a clean, copy-pasteable prompt a student could send to Claude (or any LLM) to elicit the same code. Specific enough that the LLM has a real chance; minimal enough that the student is still doing the thinking. The student is being taught to *specify* what they want, which is the actual hard skill.
3. **Tests to validate** — 2–4 concrete tests that would prove the code works. Tests can be `assert` statements, expected `print` outputs, edge cases, type checks, or simple property checks. The student is being taught *how to verify* what an LLM produces, because the LLM will sometimes get it subtly wrong.

The visual format is consistent throughout the book:

````
```python
def fahrenheit_to_celsius(f):
    """Convert a Fahrenheit temperature to Celsius."""
    return (f - 32) * 5/9
```

> **Prompt for Claude.** "Write a Python function `fahrenheit_to_celsius(f)` that takes a Fahrenheit temperature and returns the Celsius equivalent. Include a docstring. Use the formula `(f - 32) * 5/9`."

> **Tests to validate.**
> - `fahrenheit_to_celsius(32) == 0` — water freezing
> - `fahrenheit_to_celsius(212) == 100` — water boiling at sea level
> - `fahrenheit_to_celsius(98.6)` returns approximately 37 — body temperature
> - `fahrenheit_to_celsius(-40) == -40` — the famous fixed point
````

Why this works pedagogically: writing a good prompt forces the student to specify the problem precisely, which is also what they would have to do to write the code from scratch. Writing tests forces the student to think about edge cases, which is also what they would have to do to debug code they wrote. The Claude framing turns a one-way "here is some code, memorize it" lesson into a three-way conversation between the student, the code, and the verification.

**Not every line needs a prompt.** Tiny snippets (a single `print("hello")`) don't earn the three-part treatment — they would clutter the page. Reach for the pattern when the code is doing real work: a function, a class, a non-trivial loop, a multi-step procedure. The chapter author exercises judgment.

**Tests don't need to be `pytest`-grade.** This is CS-1. A test can be a one-line `assert` or a documented expected output. Tests get more rigorous later in the book (Chapter 6 onwards, where functions have arguments).

## Prerequisites assumed

- High school algebra: solving for a variable, plugging into a formula.
- Basic comfort with a computer: opening a terminal, running a command, editing a text file.
- No prior programming. No prior LLM experience required.

## Author byline convention

Author is Nik Bear Brown. First-person voice when taking a position. "I've watched students freeze the first time they get a `NameError` — here's what helps..." rather than "It is often noted that..."

## Style file location

Voice ground truth: workshop-root `style/`, with per-book overrides in `books/introduction-python-programming-claude/style/` (currently empty). Where the per-book style is empty, agents anchor on `books/contemporary-mathematics/chapters/01-sets.md` (canonical voice anchor) — the math voice carries; the addition is heavy code density and the three-part Claude pattern.
