# Chapter 7 Teaching Notes — Modules

## Learning pathway

This chapter marks a transition from "writing single-file programs" to "organizing code across multiple files." The shift is cognitive as much as technical: students move from thinking about their program as one monolithic script to thinking about it as a collection of collaborating modules.

The cold open (Lego bricks) anchors the abstract concept of modularity in something tangible — a physical analogy that sticks. The progression is deliberate:

1. **Concept 1** shows students that they can use pre-written code from the standard library without understanding its internals. This is motivating — suddenly they have access to functionality (square roots, randomness, dates) that would take hours to implement.

2. **Concept 2** shifts the perspective: now they write modules themselves. They see the workflow — create a `.py` file, define functions, import elsewhere. The trade-off between `import module` and `from module import function` is where they encounter their first real design decision.

3. **Concept 3** introduces the `if __name__ == "__main__"` idiom, which is not obviously necessary until you try to import a module that prints things on every import. The concrete problem creates the need for the solution.

The module ecosystem (standard library vs. PyPI) is introduced but not extensively explored — that's the work of later chapters when students need specific tools (NumPy, pandas, matplotlib).

## Common pitfalls and misconceptions

### 1. "I imported math, so why can't I call `sqrt()` directly?"

Students often write:

```python
import math
print(sqrt(16))  # NameError: name 'sqrt' is not defined
```

They expect `sqrt` to be available in their namespace. Explain:

- `import math` loads the module but does not bring individual functions into your namespace.
- You must use `math.sqrt()`, with the module name as a prefix.
- If they want to call `sqrt()` directly, they need `from math import sqrt`.

The namespace is the concept that unlocks this. Use the analogy: "If you import a toolbox, you don't get the hammer in your hand — you have the toolbox. To use the hammer, you reach into the toolbox (using the dot)."

### 2. Name collisions feel impossible to create by accident

Students who define a function `mean()` and then import a function also named `mean()` are surprised when their function shadows the imported one — with no error message.

```python
from statistics import mean

def mean(x):
    return sum(x) / len(x)

print(mean([1, 2, 3]))  # Calls the local version!
```

This is intentional Python design (last definition wins), not a bug. But it's dangerous because there's no warning. Reinforce:

- Always check the import list at the top of your file before naming functions.
- When in doubt, use `import module_name` and call `module_name.function()`. The redundancy is protection.

### 3. "What's the difference between a module and a package?"

This question often comes up. Short answer for Chapter 7: a module is a single `.py` file. A package is a folder of modules (a folder containing an `__init__.py` file). For this chapter, stick to modules. Packages are a Chapter 14+ topic.

### 4. Students try to import modules that don't exist

```python
import requests  # ModuleNotFoundError
```

`requests` is a real and popular module, but it's not in the standard library — it's on PyPI. Installing it requires `pip install requests`, which is beyond Chapter 7's scope. When this comes up, say: "Yes, `requests` is a great module, but it's not built in. You'd install it with `pip install requests`, which we'll show you when we need it."

### 5. The `if __name__ == "__main__"` idiom feels like magic

It is, at first. Walk through it explicitly:

- `__name__` is a special variable Python sets automatically.
- When you run a file directly (e.g., `python my_file.py`), Python sets `__name__` to the string `"__main__"`.
- When you import that file as a module, Python sets `__name__` to the module's name (e.g., `"areas"`).
- So `if __name__ == "__main__"` is a way of saying "run this code only if the user is running this file directly, not importing it."

Show this concretely:

```python
# File: mymodule.py
print(f"The value of __name__ is: {__name__}")

if __name__ == "__main__":
    print("This is the main module!")
else:
    print("This module was imported!")
```

Run it directly:
```
$ python mymodule.py
The value of __name__ is: __main__
This is the main module!
```

Then import it:
```
$ python
>>> import mymodule
The value of __name__ is: mymodule
This module was imported!
```

The output is tangible proof of how it works.

## Code examples — pedagogical notes

### The three-part Claude pattern in this chapter

The chapter includes six full code-prompt-test triples:

1. **Rolling dice (random module)** — Shows how to use randomness. Tests check that the result is in the right range and that seeding produces consistency.

2. **Temperature conversion module** — Shows how to write and import a custom module. Tests check accuracy and boundary cases.

3. **Age calculation (datetime module)** — Shows date arithmetic. Tests check off-by-one errors (born 20 years and 100 days ago is still 20 years old, not 21).

Each triple is:
- **Runnable:** Students can copy-paste the code and execute it without modification.
- **Real:** The examples solve actual problems, not toy problems. Temperature conversion is something a real program would do.
- **Testable:** The tests are concrete assertions, not vague expectations.

### Why these modules?

The chapter features `math`, `random`, and `datetime` because they are:

- **Essential.** Every programmer uses these or similar modules.
- **Safe.** They can be demonstrated in a shell without complex setup.
- **Varied.** One is constants and functions (math), one is randomness (random), one is objects and methods (datetime). Together they show modularity across different programming patterns.

`csv` and `json` are mentioned but not deeply explored — they require file I/O, which is Chapter 14.

## Assessment strategies

### Formative (in-chapter)

- **Exercises 7.1–7.3 (Warm-up):** Students should be able to use the standard library, write a simple module, and articulate the trade-off between import styles.
- **Exercises 7.4–7.6 (Application):** Students manage a practical design challenge (name collisions, side effects, date arithmetic). These reveal whether the concepts stuck.
- **Exercises 7.7–7.8 (Synthesis):** Students design a multi-function module and compare modules. These are the high bar — they require integration of concepts.

### Summative

By the end of this chapter, assess:

1. **Can the student import a function from the standard library and call it correctly?** (Use `math.sqrt()` correctly.)
2. **Can the student write a module and import it from another file?** (Create `greetings.py` with functions; import and use from `main.py`.)
3. **Can the student explain why `if __name__ == "__main__"` matters?** (Free-response: when would side effects be a problem?)
4. **Can the student navigate the trade-off between import styles?** (Write two versions of the same code and explain the pros/cons of each.)

## Common lesson adjustments

### For advanced students

Advanced students may ask:
- "How do I organize a large project with many modules?" → Introduce the concept of a package (folder of modules). Note that this is beyond Chapter 7 but they'll return to it in Chapter 14.
- "Can I reload a module if it changes?" → Brief mention of `importlib.reload()`, but don't dive deep.
- "What's the difference between importing at the module level vs. inside a function?" → Good question. Explain that importing at the top is standard (clearer dependencies), but importing inside a function is occasionally useful (e.g., to delay loading a large module until it's actually needed).

### For students struggling with the namespace concept

Some students will find the distinction between `import math` and `from math import sqrt` confusing. Use multiple analogies:

- "The module is the toolbox, the function is the tool. `import math` gives you the toolbox. `from math import sqrt` gives you just the tool."
- "The module name is like a last name. `math.sqrt()` says `sqrt()` from the math family. `sqrt()` alone assumes you already know which family."

Use visualization: draw a box labeled `math`, with `sqrt`, `pi`, `sin`, etc. inside. Then draw an arrow from `from math import sqrt` that extracts just that one function into the main namespace.

### For students who think all code must go in the module docstring

Some students interpret "docstrings document the module" as "all documentation must be in the docstring." Clarify:

- The docstring is a summary at the top of the file.
- Functions should also have docstrings.
- Comments (lines starting with `#`) document why, not what.
- The code itself should be clear enough to read.

A good module docstring is two sentences. The function docstrings are one sentence each. The code is self-explanatory.

## Extensions and connections

### Looking backward (prerequisites)

This chapter assumes:
- **Chapters 1–6:** Basic syntax, variables, functions, control flow. Students should be comfortable defining and calling functions.

### Looking forward (later chapters)

- **Chapter 8 (Strings):** Uses `datetime` module to work with date strings.
- **Chapter 9 (Lists):** Uses `random.choice()` and `random.shuffle()`.
- **Chapter 14 (Files):** Revisits the modular pattern; shows how to split file I/O across modules.
- **Chapter 15 (Data Science):** Imports NumPy and pandas, major third-party modules. The pattern from Chapter 7 scales directly.

### Real-world connection

Emphasize: "In your first job, you'll spend 80% of your time importing modules someone else wrote and 20% writing new code. Modules are how real programming works."

## Homework and extended practice

### Suggested homework

1. **Module design exercise:** Create a module that solves a real problem (e.g., `grade_calculator.py` with functions to compute letter grades, GPA, etc.). Import and use it from a main program.

2. **Explore the standard library:** Pick a module (e.g., `random`, `string`, `collections`). Spend 10 minutes reading its documentation. Write one program that uses three of its functions. Reflect: when would you use this module in a real program?

3. **Fix broken code:** Provide students with a program that has:
   - A name collision (they fix by renaming or using module notation).
   - Unwanted side effects on import (they fix with `if __name__ == "__main__"`).
   - A missing import (they add it).

### Extension activities

- **Build a library:** Create a module with 10 functions (e.g., geometry, statistics, text processing). Document it. Have a classmate import and use it.
- **Dependency graph:** List all the modules used in their code so far (in Chapters 1–7). How many are from the standard library? How many did they write?

## Common student questions and answers

**Q: Why does `import math` take so long?**
A: It doesn't, usually. If it seems slow, something else is happening. If Python is importing many large modules, that could add up. But `import math` is nearly instant.

**Q: Can I import a module twice?**
A: Technically yes, but Python only loads it once. The second `import` is a no-op.

**Q: What if I have a file named `math.py` in my project?**
A: Bad idea. Python will prefer your `math.py` over the standard library `math` module, which will break everything. Avoid naming your files after standard library modules.

**Q: Can I import only part of a module?**
A: Yes, with `from module import specific_function`. But if you need many functions, it's cleaner to `import module` and use dot notation.

**Q: Why is it `__name__` and not `_name_` or something?**
A: Convention. Double underscores (dunder) signal "I am a special Python variable, don't mess with me." It's a signal to the reader.

---

**Author:** Nik Bear Brown  
**Date:** 2025-05-07  
**Chapter:** 7 — Modules
