# Chapter 7 — Modules
*How Python learned to stop reinventing the wheel.*

Picture a Lego table. You could build every wheel-and-bracket assembly by hand each time — stack the same five bricks in the same order, over and over, every time a project needs wheels. Or you could build one wheel assembly once, wrap it up, label it, set it aside, and grab it whenever you need it. Same result, every time, instantly pluggable. A module is that wrapped-up thing.

A *module* — from Latin *modulus*, a small measure or unit — is a `.py` file containing function definitions and other statements. The module's name is the filename without the `.py` extension. `areas.py` defines a module named `areas`. When your program imports it, Python loads that file's code into memory and makes its functions available under the module's name. Every Python program you've written so far is technically a module. Most modules, though, aren't designed to be run directly — they're designed to be imported and used by other code.

You already have reason to want this. The programs in earlier chapters are growing. Functions to convert temperatures sit next to functions that calculate areas sit next to functions that handle user input. They don't belong together. Separating them into files isn't tidiness — it's the beginning of a design principle that scales from a single `.py` file to projects with hundreds of thousands of lines.

And you are not writing alone. Python ships with over two hundred modules in its standard library, covering dates and times, random numbers, file formats, mathematics, and much more. The Python Package Index adds hundreds of thousands more, written by programmers worldwide. The question isn't whether you'll use other people's modules. It's how to do it cleanly.

---

## Importing from the standard library

You need the square root of a number. You could implement Newton's method — it's elegant, not too long. But `math.sqrt()` exists, has been tested for decades, and is one import away:

```python
import math
print(math.sqrt(16))    # 4.0
print(math.pi)          # 3.141592653589793
```

The `import` statement loads the module. Once loaded, you access everything inside it through dot notation: `math.sqrt()`, `math.pi`, `math.cos()`. The dot is the namespace separator — it says "look inside `math` for the thing named after the dot."

*Namespace*, from *name* + *space*, is the container where a module's names live. Namespaces prevent collisions. If your code defines a function `sqrt()` and `math` also defines `sqrt()`, they coexist peacefully — yours is just `sqrt()`, theirs is `math.sqrt()`. Without namespaces, every imported name would risk overwriting something you'd already defined.

A few standard library modules worth knowing early:

`math` gives you square roots, logarithms, trigonometry, and constants like `pi` and `e`. `random` gives you random number generation, list shuffling, and random selection. `datetime` handles dates, times, and the arithmetic between them. `csv` reads and writes comma-separated files. `json` parses and generates JSON, the standard format for web API data.

Each is documented at docs.python.org. The documentation shows what functions are available, what arguments they take, and examples. Getting comfortable reading module documentation is as important as knowing how to import.

Here's `random` in practice:

```python
import random

colors = ['red', 'blue', 'green', 'yellow']
chosen = random.choice(colors)
print(f"Chosen: {chosen}")

dice_roll = random.randint(1, 6)
print(f"Rolled: {dice_roll}")

deck = list(range(1, 11))
random.shuffle(deck)
print(f"Shuffled: {deck}")
```

`random.choice()` picks one element from a sequence. `random.randint(a, b)` returns a random integer between `a` and `b` inclusive — both endpoints are in range, unlike `range()`. `random.shuffle()` reorders a list in place.

Notice that `import random` gives you the entire module. You don't pick individual functions at this point — you get the namespace, and you access whatever you need through it.

<!-- → [TABLE: Standard library quick reference — four rows: Module / What it gives you / Example call / Returns. `math` / mathematical functions and constants / `math.sqrt(16)` / `4.0`; `random` / randomness / `random.randint(1, 6)` / integer 1–6; `datetime` / dates and times / `date.today()` / today's date; `csv` / CSV file I/O / `csv.reader(f)` / row iterator.] -->

---

## Writing your own modules

The same mechanism that lets you import `math` lets you import code you write. A module is just a `.py` file. If you save functions in `areas.py`, another program in the same folder can import them with `import areas`.

Here's `areas.py`:

```python
"""Functions for calculating geometric areas."""
import math

def square(side):
    """Area of a square."""
    return side ** 2

def rectangle(length, width):
    """Area of a rectangle."""
    return length * width

def circle(radius):
    """Area of a circle."""
    return math.pi * radius ** 2

def triangle(base, height):
    """Area of a triangle."""
    return 0.5 * base * height
```

The string at the very top is the *module docstring* — it documents the entire module. Function docstrings document individual functions. When you call `help(areas)` in a Python shell, Python shows both, automatically assembled from these strings.

Now in a separate file in the same folder:

```python
import areas

print("Square with side 5:", areas.square(5))
print("Circle with radius 3:", areas.circle(3))
```

The module name is the filename without `.py`. If you rename `areas.py` to `geometry.py`, the import must change to `import geometry`. Python matches exactly.

### Two import styles and when each makes sense

There's a second way to import — bring specific names directly into your namespace:

```python
from areas import circle, square

print(circle(5))    # No module prefix needed
print(square(5))
```

The trade-off is straightforward. `import areas` is more explicit — `areas.circle()` tells any reader exactly where `circle` came from. `from areas import circle` is shorter — you don't repeat the module name on every call. But it introduces a risk.

A *name collision* happens when the same name exists in two places. If you import `circle` from `areas` and then define your own `circle()` function, your definition silently overwrites the import. Python doesn't warn you:

```python
from areas import circle

def circle(radius):     # This shadows the imported circle
    return 3.14 * radius ** 2

circle(5)    # Calls your function, not areas.circle
```

The reader of this code — including you, three weeks from now — may not realize which `circle` is running. The fix: use `import areas` and dot notation when the source matters. Reserve `from module import name` for cases where the name is distinctive and the module is clear from context.

<!-- → [TABLE: Import style comparison — two rows: `import areas` / accesses as `areas.circle()` / collision-safe / verbose; `from areas import circle` / accesses as `circle()` / collision risk / concise. When to use each column: first for readability and safety; second when name is unambiguous and module is obvious.] -->

---

## Side effects and `__name__`

When Python imports a module, it executes every line of code in the file from top to bottom — except code inside function definitions, which is stored but not run. This means a module's top-level statements are *side effects* of importing.

Suppose `areas.py` had a print statement at the top level:

```python
"""Functions for calculating geometric areas."""
import math

def circle(radius):
    return math.pi * radius ** 2

print("The areas module has loaded!")    # Side effect
```

Every program that imports `areas` will see "The areas module has loaded!" whether they want to or not. This is intrusive. The fix is a Python idiom so universal it's essentially part of the language:

```python
if __name__ == "__main__":
    print("The areas module has loaded!")
```

The `__name__` variable — pronounced *dunder name*, where *dunder* is shorthand for *double underscore* — is set automatically by Python. When you run a file directly as a program, `__name__` is `"__main__"`. When a file is imported by another module, `__name__` is the module's name — `"areas"`, in this case.

The `if __name__ == "__main__"` block runs only in the first case. It never runs on import. This means you can put test code, demonstrations, or command-line interfaces under this guard without contaminating programs that import the module:

```python
"""Functions for calculating geometric areas."""
import math

def circle(radius):
    """Area of a circle."""
    return math.pi * radius ** 2

def square(side):
    """Area of a square."""
    return side ** 2

if __name__ == "__main__":
    # Only runs when this file is executed directly
    print(f"Circle with radius 5: {circle(5):.4f}")
    print(f"Square with side 4: {square(4)}")
    assert circle(5) == math.pi * 25
```

Run `areas.py` directly and the test block executes. Import `areas` from another file and it doesn't. This pattern appears in almost every well-written Python module.

<!-- → [INFOGRAPHIC: Two-panel diagram showing `__name__` behavior. Left panel: file is run directly → `__name__` equals `"__main__"` → `if __name__ == "__main__"` block executes. Right panel: file is imported → `__name__` equals `"areas"` → block does not execute. Arrow labels make the control flow concrete.] -->

---

## Beyond the standard library

The standard library covers common problems with stable, guaranteed code. It comes with every Python installation and requires no extra steps to use.

Beyond that is PyPI — the Python Package Index, pronounced *pie pea eye*. PyPI is a public repository of open-source packages written by programmers worldwide. Most are free. Some are foundational to entire fields.

NumPy provides multidimensional arrays and fast numerical operations — nearly every scientist working in Python uses it. Pandas adds spreadsheet-like data structures and analysis tools. Requests simplifies HTTP calls to web APIs from fifty lines to five. Matplotlib creates plots and charts. Pygame handles graphics and input for games.

You install packages from PyPI using `pip`, the package installer that ships with Python:

```bash
pip install requests
```

After that, `import requests` works exactly like importing any standard library module. One command connects you to thousands of hours of accumulated engineering.

The trade-off: standard library modules are stable, always available, and require no installation. Third-party packages are more specialized but introduce a *dependency* — your code requires that specific package to be installed, in a compatible version, on any machine that runs it. For personal projects, this is manageable. For code you're distributing, it becomes a real concern that later chapters address.

For now: default to the standard library when it does what you need. Reach for PyPI when the standard library doesn't cover it. Both are legitimate; the choice is about what your code will depend on.

---

## A working example: dates

`datetime` is one of the standard library's more complete modules — it handles the surprisingly complicated business of dates, times, time zones, and intervals. A taste:

```python
from datetime import date

today = date.today()
print(f"Today is {today}")

birthday = date(2000, 1, 15)
print(f"Birthday: {birthday}")

age_in_days = (today - birthday).days
years = age_in_days // 365
print(f"Approximately {years} years old")
```

Here `from datetime import date` imports just the `date` class from the `datetime` module. This is appropriate — `date` is a distinctive name unlikely to collide with anything else you'd define. Subtracting two `date` objects gives a `timedelta` representing the interval; `.days` extracts the number of days from it.

The module handles calendar arithmetic that's genuinely tricky to get right: leap years, month lengths, the varying number of days in different months. You don't want to write that from scratch. That's the point of the standard library — not that you *can't* write it, but that you *shouldn't have to*.

---

## LLM Exercise — Chapter 07: Modules (Text Adventure Project)

**Project:** Text Adventure.
**What you're building this chapter:** split the game across multiple files; use the standard library.
**Tool:** **Claude Code**.

---

**The Prompt:**

```
Chapter 7 of my Text Adventure project. Chapter 7 covered:
import; the standard library; writing your own modules; from X
import Y; __name__ == "__main__".

Split the growing game into modules. Suggested structure:

  /text-adventure/
    game.py        # main entry; the game loop
    combat.py      # combat() function and helpers
    world.py       # room descriptions and navigation
    player.py      # player-related functions (take_damage, heal,
                   #   print_stats)
    util.py        # input helpers, normalization
    /data/         # (still empty; populated in Ch 14)

Each module should:
- Have a docstring at the top stating what it does.
- Export only the functions other modules need (use `_` prefix for
  internal helpers if you like).
- Be importable: `python -c "import combat"` should run without
  errors.

Then in `game.py`, import what you need:
```python
from combat import combat
from world import describe_room
from player import take_damage, heal, print_stats
from util import get_command
```

Use 2-3 standard-library modules:

1. **`random`** — for combat dice rolls. Replace deterministic
   damage with `random.randint(min, max)`. Players love
   uncertainty.

2. **`time`** — for `time.sleep(0.5)` between game-state changes,
   giving text a chance to be read.

3. **`os`** — for `os.system('clear')` or `os.system('cls')` to
   clear the screen at chapter transitions (Linux/Mac uses
   'clear', Windows uses 'cls' — handle both).

The `if __name__ == "__main__":` block goes at the bottom of
`game.py`:
```python
if __name__ == "__main__":
    main_game_loop()
```

This means importing `game.py` doesn't auto-run the game — only
running it directly does.

In addition to the code, produce the prompt + tests. Tests:
- `import combat` works without errors.
- `python game.py` still plays the full game.
- Test combat with seeded random for reproducibility:
  `random.seed(42); ...` should produce the same outcome each
  run.

End with: explain why splitting into modules is better than one
big file. (Honest answer for small projects: it's overkill until
the file is >500 lines. But you're learning the discipline now.)
```

---

**What this produces:** A modular game with stdlib usage. Imports work correctly.

**Connection to previous chapters:** Ch 6's functions get organized; Ch 7's modules separate concerns.

**Preview of next chapter:** Chapter 8 covers strings. You'll build a real command parser — multi-word commands ("take rusty sword"), aliases ("n" for "north"), and command suggestions for typos.

---

## AI Wayback Machine

**David Parnas** wrote the foundational 1972 paper on modularity and information hiding — defining the principles modern Python modules implement.

**Run this:**

```
Who is David Parnas, and how does their work connect to modules we covered in this chapter? Keep it to three paragraphs. End with the single most surprising thing about their career or ideas.
```

→ Search **"David Parnas"** on Wikipedia.

**Now make the prompt better.** Try one of these:

- Ask it to apply David Parnas's ideas to a small Python program you'd write.
- Add a constraint: "Answer including criticisms or limits of David Parnas's framework."

What changes? What gets better? What gets worse?

---

## Exercises

**Warm-up**

1. Write a program that uses the `random` module to generate a random integer from 1 to 10, pick a random item from a list of at least five colors, and shuffle a list of five numbers. Print each result.

2. Create a file `greetings.py` with two functions: `hello(name)` returns the string `"Hello, [name]!"` and `goodbye(name)` returns `"Goodbye, [name]!"`. Write a separate program that imports `greetings` and uses both functions.

3. Write two versions of the same program: one using `import math` and calling `math.sqrt()`, one using `from math import sqrt` and calling `sqrt()` directly. Which reads more clearly to you, and why?

**Application**

4. Create a module `stats.py` with a function `mean(numbers)` that returns the average of a list. In a separate program, import it. Then, after the import, define your own function also named `mean()` that does something different. Call both and show they produce different results. What does this demonstrate about name collisions?

5. Create `areas.py` with functions for the area of a square and a circle. At the bottom, add a `if __name__ == "__main__"` block that tests both functions. Verify that when you import `areas` from another program, the test output does not appear.

6. Write a program that gets today's date from `datetime.date.today()`, calculates how many days until a fixed future date of your choice, and prints the result in a readable format.

**Synthesis**

7. Design a module called `dice.py` with three functions: `roll_die()` returns a random integer from 1 to 6; `roll_dice(num_dice)` returns a list of that many rolls; `sum_dice(num_dice)` returns their sum. Include docstrings. Add a `if __name__ == "__main__"` block that demonstrates all three. Import and use the module from a separate file.

8. Write a program that uses three different standard library modules. For each, show what problem it solves and demonstrate it with a concrete example. Then write a sentence or two: why is it better to use the standard library here than to write the functionality yourself?

**Challenge**

9. Write a module `conversions.py` with functions to convert miles to kilometers, pounds to kilograms, and Fahrenheit to Celsius. Write a separate test file that imports the module and uses `assert` statements to verify each function's output against a known correct value. Then deliberately break one function and run the tests — what error do you get, and what does it tell you about where the bug is?
