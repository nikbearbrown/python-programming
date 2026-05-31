# Chapter 7 — Modules


## TL;DR

- A module, from Latin modulus meaning a small measure or unit — is a .
- You will practice Import modules from Python's standard library and call functions within them; Write a module (a ; Distinguish between import module and from module import function, and manage name collisions.
- The chapter moves through Cold open, Learning objectives, Prerequisites, Why this chapter matters, and related ideas.

## Cold open

You're at a Lego brick table. You have a pile of red bricks, a pile of blue bricks, a pile of wheels. You could build every wheel-and-bracket assembly by hand each time, stacking the same five bricks over and over. Or you could build one wheel assembly once, wrap it up, label it "*wheel module*", set it aside, and grab it whenever you need wheels. Same thing, every time, instantly pluggable. A module is that wrapped-up thing. A *.py* file containing functions and data you've written once, that you can grab from anywhere else in your project.

A *module*, from Latin *modulus* meaning a small measure or unit — is a *.py* file containing function definitions and other statements. The module's name is the filename without the *.py* extension. So `greetings.py` defines a module named `greetings`. When your program runs, Python loads that file's code into memory, and you can call the functions inside. Every program in this book is technically a module. But not every module is designed to be run as a standalone program — most are designed to be imported and used by other modules.

As programs grow, the need becomes clear. By Chapter 15 you'll be working with hundreds of lines of code, possibly distributed across multiple files. Organization matters. Python's standard library alone contains over 200 built-in modules. PyPI (the Python Package Index) hosts hundreds of thousands more, free and open source, written by programmers around the world. You can use them with one line: `import datetime`. That line connects you to decades of accumulated machinery for handling dates and times — machinery you don't have to write or test yourself.

This chapter teaches you three moves: importing from the standard library, writing and importing your own modules, and understanding the landscape of available tools.

### Learning objectives

By the end of this chapter you will be able to:

- **Import** modules from Python's standard library and call functions within them.
- **Write** a module (a *.py* file) containing function definitions, and **import** that module into other programs.
- **Distinguish** between `import module` and `from module import function`, and **manage** name collisions.
- **Explain** what `if __name__ == "__main__"` does and **use** it to prevent unwanted side effects.
- **Navigate** Python's standard library documentation and understand the difference between built-in modules and third-party packages from PyPI.

### Prerequisites

Comfort with function definitions and `def`. The ability to create and save a *.py* file on your computer.

### Why this chapter matters

Every non-trivial program is modular. As you grow as a programmer, you'll spend more time reading and importing other people's code than writing code from scratch. Understanding how modules work is how you learn to stand on the shoulders of giants rather than reinventing wheels — or wheel modules.

---

## Concept 1 — The standard library and the `import` statement

You're writing a program that needs to calculate the square root of a number. You could implement the algorithm yourself — Newton's method is elegant and not too complex. But why? Python ships with `math.sqrt()`. It's tested, it's fast, it's been maintained for decades. You use it with two lines:

```python
import math
print(math.sqrt(16))  # Prints 4.0
```

The `import` statement tells Python to load the module. Once loaded, you access functions and constants inside it using *dot notation*: `module_name.function_name`. So `math.pi` is the constant pi (approximately 3.14159) and `math.sqrt(x)` computes the square root.

A *namespace* — from *name* + *space*, the place where a module's names live — keeps functions from different modules from colliding. If your code defines a function `sqrt()` and the math module also defines `sqrt()`, the dot notation disambiguates: `math.sqrt()` is the one from the math module; `sqrt()` (no module prefix) is yours.

### Exploring the standard library

The standard library is Python's "batteries included" philosophy in action. Over 200 modules come with every Python installation, covering common tasks: working with dates and times, file I/O, random numbers, statistics, regular expressions, file compression, and much more. A few worth knowing early:

- **`math`** — square roots, logarithms, trigonometry, constants like pi and e.
- **`random`** — random number generation, shuffling lists, picking random items.
- **`datetime`** — working with dates and times in multiple formats.
- **`csv`** — reading and writing comma-separated-value data files.
- **`json`** — parsing and creating JSON (the standard interchange format for web APIs).

Each module is documented at https://docs.python.org. The documentation shows what functions are available, what arguments they take, and examples of use.

### Worked example — working with the random module

```python
import random

# Pick one random item from a list
colors = ['red', 'blue', 'green', 'yellow']
chosen = random.choice(colors)
print(f"Your favorite color is {chosen}")

# Generate a random integer from 1 to 100 (inclusive)
dice_roll = random.randint(1, 100)
print(f"You rolled: {dice_roll}")

# Shuffle a list in place
deck = list(range(1, 11))  # [1, 2, 3, ..., 10]
random.shuffle(deck)
print(f"Shuffled: {deck}")
```

> **Prompt for Claude.** "Write a Python function that simulates rolling two six-sided dice and returns the sum. Use the `random` module to generate each die roll. Include a docstring explaining what it does."

> **Tests to validate.**
> - Calling the function returns a number between 2 and 12 (inclusive).
> - Running it 10 times produces different results (with very high probability).
> - `random.seed(42)` before calling the function twice produces the same result both times (confirming the randomness is from the module, not elsewhere).

### Common misconceptions

**Importing is not the same as running.** `import math` loads the module but does not run any code in the module that's outside function definitions. `math` defines `sqrt` and `pi` but does not execute arbitrary statements. This is by design — it means importing a module is safe; the module author can't sneak surprise code into your program when you import.

**Every module attribute must be imported.** Some students think `import math` only imports `sqrt`, but it imports the entire namespace. You can then access `math.pi`, `math.cos()`, `math.factorial()`, and dozens of other functions and constants.

---

## Concept 2 — Writing and importing your own modules

The machinery inside your code grows. You write functions to calculate areas of geometric shapes. You write more functions to convert temperatures. You're 50 lines in and noticing a pattern: certain functions belong together. This is the moment to pull them out into a module.

A module is just a *.py* file with function definitions in it. Here's an `areas.py` file containing functions for calculating the areas of shapes:

```python
"""Functions for calculating geometric areas."""
import math

def square(side):
    """Calculate the area of a square."""
    return side ** 2

def rectangle(length, width):
    """Calculate the area of a rectangle."""
    return length * width

def circle(radius):
    """Calculate the area of a circle."""
    return math.pi * radius ** 2

def triangle(base, height):
    """Calculate the area of a triangle."""
    return 0.5 * base * height
```

Save this as `areas.py` in your project folder. Now in another file in the same folder, you can use these functions:

```python
import areas

print("A square with side 5 has area:", areas.square(5))
print("A circle with radius 3 has area:", areas.circle(3))
```

The module docstring — the string at the very top of the file before any code — documents the entire module. The function docstrings document individual functions. When you use `help(areas)` in a Python shell, Python displays both, automatically generated from the strings.

### Trade-off: `import module` vs `from module import function`

You have two ways to import. We've been using the first:

```python
import areas
print(areas.circle(5))
```

But you can also import *specific* functions:

```python
from areas import circle, square
print(circle(5))
print(square(5))
```

The trade-off is clarity versus convenience. `import areas` is more explicit — any reader can see that `areas.circle()` comes from the `areas` module. `from areas import circle` is shorter — you don't repeat the module name. But it's less obvious where `circle` came from unless you remember the import at the top.

A *name collision* occurs when the same name is defined in multiple places. If you write:

```python
from areas import circle

def circle(radius):
    return 3.14 * radius ** 2
```

Your function shadows the imported one. Python doesn't warn you — it just uses the most recent definition. This is dangerous because the reader of your code might not realize that `circle()` is your custom version, not the imported one.

To avoid this: when importing specific functions (using `from`), name them explicitly, keep the import list short, and choose function names that don't collide with your own code. When in doubt, use `import module` and access functions with dot notation — it's clearer.

### Worked example — a temperature conversion module

Create a file `conversions.py`:

```python
"""Functions to convert between temperature and distance units."""

def celsius_to_fahrenheit(c):
    """Convert Celsius to Fahrenheit."""
    return 9/5 * c + 32

def fahrenheit_to_celsius(f):
    """Convert Fahrenheit to Celsius."""
    return 5/9 * (f - 32)

def km_to_miles(km):
    """Convert kilometers to miles."""
    return km / 1.60934

def miles_to_km(mi):
    """Convert miles to kilometers."""
    return mi * 1.60934
```

Now use it in another file:

```python
import conversions

temp_c = 25
temp_f = conversions.celsius_to_fahrenheit(temp_c)
print(f"{temp_c}°C is {temp_f}°F")

distance_km = 100
distance_mi = conversions.km_to_miles(distance_km)
print(f"{distance_km} km is {distance_mi:.1f} miles")
```

> **Prompt for Claude.** "Write a Python module called `temperature.py` with a function `absolute_zero_in()` that takes a temperature scale (either 'celsius' or 'fahrenheit') and returns what absolute zero is in that scale. Absolute zero is -273.15 Celsius and -459.67 Fahrenheit. Include a docstring."

> **Tests to validate.**
> - `absolute_zero_in('celsius')` returns -273.15.
> - `absolute_zero_in('fahrenheit')` returns -459.67.
> - `absolute_zero_in('kelvin')` raises a ValueError with a message (or returns None, depending on design choice — specify).

### Common misconceptions

**Module names must match filenames.** If you create a file `conversion.py`, the module is `conversion`. If you later rename the file to `convert.py`, import statements must change to `import convert`. Python matches names exactly.

**You can't have circular imports easily.** If `module_a` imports from `module_b` and `module_b` imports from `module_a`, Python can get confused. This is rare in small programs but becomes a structural concern in large projects. The fix is usually to refactor: break the circular dependency or import inside a function instead of at the module level.

---

## Concept 3 — Side effects, `__name__`, and the module ecosystem

When Python imports a module, it runs every line of code in the file from top to bottom — except code inside function definitions. If your module's top level contains print statements, assignments, or anything else, that code runs as a side effect of importing. This can be unwanted.

Suppose you write a module to calculate areas:

```python
"""Calculate areas of shapes."""
import math

def circle(radius):
    """Area of a circle."""
    return math.pi * radius ** 2

print("The areas module is loaded!")
```

Every time someone imports this module, `print()` executes. This is clutter — the user didn't ask for output; they just wanted the `circle()` function. To prevent unwanted side effects, use a Python idiom:

```python
"""Calculate areas of shapes."""
import math

def circle(radius):
    """Area of a circle."""
    return math.pi * radius ** 2

if __name__ == "__main__":
    print("The areas module is loaded!")
```

The `__name__` variable (double underscore on each side — pronounced *dunder name*; *dunder* is short for *double underscore*) is a special variable Python sets automatically. When you run a file directly as a program, `__name__` is set to `"__main__"`. When you import the module, `__name__` is set to the module's name (e.g., `"areas"`). The `if` statement runs only when the file is executed as a program, not when it's imported.

This idiom is universal in Python. Almost every module includes it, with test code underneath:

```python
"""Calculate areas of shapes."""
import math

def circle(radius):
    """Area of a circle."""
    return math.pi * radius ** 2

if __name__ == "__main__":
    # Test code runs only when running this file directly
    print(f"Circle with radius 5: {circle(5)}")
    assert circle(5) == math.pi * 25
```

### The namespace of Python

Python comes with the standard library, built in and guaranteed to be available on any Python installation. Beyond that is PyPI — the Python Package Index (pronounced *pie pea eye*). PyPI is a public repository of modules and packages written by programmers worldwide. Most are free and open source. Many are incredibly useful.

Some examples from PyPI:

- **`numpy`** — fundamental numerical computing. Multidimensional arrays and fast linear algebra. Nearly every scientist in Python uses it.
- **`pandas`** — data manipulation and analysis. Tables, pivoting, filtering, grouping.
- **`requests`** — elegant HTTP library. Fetching data from web APIs in five lines instead of fifty.
- **`matplotlib`** — static and animated plots and visualizations.
- **`pygame`** — graphics and game development.

You install packages from PyPI using `pip` (the package installer for Python), a command-line tool that comes with Python:

```bash
pip install requests
```

After installation, you `import requests` just as you would any standard library module. One line in the terminal connects you to thousands of hours of open-source engineering.

This is the final trade-off: standard library modules are stable and guaranteed to be available everywhere; third-party packages are more specialized and powerful but introduce a dependency. Your code depends on that package being installed, in a compatible version, on the machine running your code. For a small program, this is fine. For code you're shipping to others, you'll want to manage dependencies carefully — a topic for later courses.

### Worked example — working with dates

```python
from datetime import date

# Create a date object
today = date.today()
print(f"Today is {today}")

# Create a specific date
birthday = date(2000, 1, 15)
print(f"Birthday: {birthday}")

# Calculate the difference
age_in_days = (today - birthday).days
years = age_in_days // 365
print(f"You are about {years} years old")
```

> **Prompt for Claude.** "Write a Python function that takes a birthday as a date object and returns the person's age in years. Use the `datetime` module. Don't use any external package — just the standard library. Include a docstring and handle the case where the birthday is in the future."

> **Tests to validate.**
> - A person born 20 years ago today is 20 years old.
> - A person born 20 years and 100 days ago is 20 years old (not 21, since their birthday hasn't happened this year).
> - A person born in the future returns 0 or raises a ValueError (designer's choice).
> - `assert` the result is an integer.

---

## Integration — modules in practice

Return to the cold open: you're building with Lego bricks. Once you wrap up wheel assemblies and set them aside labeled, you notice something powerful happens. You stop thinking about individual bricks. You think in terms of *modules*. "I need three wheel modules and two axles." The internal complexity of the wheel — which bricks are stacked in which order — disappears.

This is what the standard library gives you. You don't think about how `math.sqrt()` finds square roots. You use it and move on. You don't write a random-number generator; you `import random` and pick from a list.

And this is what you start doing when you write code for others (or for yourself three months from now). You extract clusters of related functions into modules. You document them with docstrings. You structure them so importing them doesn't execute tests or debug output. You're building wheels that someone — maybe you — will grab later and use without thinking about the internal machinery.

By Chapter 15, when you start working with data science libraries like NumPy and pandas, this modular thinking will be second nature. You'll import what you need, call the functions with the right arguments, and spend your energy on the logic of your program, not on reimplementing basic operations.

---

## Exercises

### Warm-up

**Exercise 7.1** *(LO: use standard library.)* Write a program that uses the `random` module to:
- Generate a random integer from 1 to 10.
- Pick a random color from a list of at least five colors.
- Shuffle a list of five numbers and print the result.

**Exercise 7.2** *(LO: write a module.)* Create a file `greetings.py` with two functions: `hello(name)` returns the string "Hello, [name]!" and `goodbye(name)` returns "Goodbye, [name]!". Write a separate program that imports `greetings` and uses both functions.

**Exercise 7.3** *(LO: distinguish import styles.)* Write two versions of the same program:
1. One that uses `import math` and calls `math.sqrt()`.
2. One that uses `from math import sqrt` and calls `sqrt()`.

Which one reads better to you? Why?

### Application

**Exercise 7.4** *(LO: manage name collisions.)* Create a module `stats.py` with a function `mean(numbers)` that calculates the average of a list. Then write a program that imports it. At the top of your program (after the import), define your own function named `mean()` that does something different. Call both and show that they produce different results.

**Exercise 7.5** *(LO: `if __name__ == "__main__"`.)* Create a module `areas.py` with functions to calculate the area of a square and a circle. At the bottom of the module, use `if __name__ == "__main__"` to test both functions when the file runs directly. Then import and use it from another program — verify that the test output does not appear.

**Exercise 7.6** *(LO: explore datetime.)* Write a program that:
- Gets today's date using `datetime.date.today()`.
- Calculates how many days until your next birthday.
- Prints the result in a readable format (e.g., "137 days until your birthday").

### Synthesis

**Exercise 7.7** *(LO: design a multi-function module.)* Design a module called `dice.py` with the following functions:
- `roll_die()` — returns a random integer from 1 to 6.
- `roll_dice(num_dice)` — rolls `num_dice` dice and returns a list of the results.
- `sum_dice(num_dice)` — rolls `num_dice` dice and returns the sum.

Include docstrings. At the bottom, use `if __name__ == "__main__"` to test all three functions.

**Exercise 7.8** *(LO: compare standard library modules.)* Write a program that demonstrates the use of three different standard library modules (choose from `math`, `random`, `datetime`, `csv`, or `json`). For each, show what problem it solves and give a concrete example. Reflect: why is the standard library valuable?

---

## Claude Code — Building modules hands-on

Why Claude Code matters here: You've read about modules. You've seen examples. But writing modules requires naming things correctly, handling multiple files, and debugging imports when they break silently. This is where a coding agent becomes valuable. Claude Code lets you scaffold two-file and three-file projects without fumbling the file creation, runs them for you, shows you what happens when side effects execute (and when they don't), and lets you test reproducibility with `random.seed()`. You get to see the machinery working, and you get to ask the agent to modify it without retyping.

### Orientation

By the end of this section, you'll have built and tested a complete two-file module project and a three-file test suite. You'll have watched imports execute and seen `if __name__ == "__main__"` prevent unwanted output. You'll have used `random.seed()` to prove reproducibility.

This is not lecture. You prompt. Claude Code builds. You read the output. You modify it. You test it. The thing you end up with is yours.

### Task 1 — Build a conversion module and use it

**Orientation.** You'll create two files: one that defines conversion functions, one that imports and uses them. Claude Code handles file creation and execution. You focus on understanding how the import works.

**Prompt for Claude Code.** "Create two Python files for me: `conversions.py` and `main.py`. In `conversions.py`, define these functions with docstrings:
- `mph_to_kph(mph)` — convert miles per hour to kilometers per hour (multiply by 1.60934)
- `lbs_to_kg(lbs)` — convert pounds to kilograms (multiply by 0.453592)
- `usd_to_eur(amount, rate)` — convert US dollars to euros using an exchange rate parameter

In `main.py`, import all three functions from `conversions` and call each one with sample input:
- 60 mph
- 150 lbs
- 100 USD at rate 0.92 EUR per USD

Print the results in readable format. Run the program and show me the output."

**What to expect.** Claude Code will create both files and run `main.py`. You'll see output like "60 mph = 96.56 kph". The key observation: the functions inside `conversions.py` are available to `main.py` because of the `import` statement. No side effects executed in `conversions.py` — it's pure definitions waiting to be called.

**Tests to validate.**
- Both files are created in the same directory.
- `main.py` runs without error.
- The output shows all three conversions calculated correctly.
- If you modify `main.py` to use `from conversions import mph_to_kph` instead of `import conversions`, it still works.

### Task 2 — Add `if __name__ == "__main__"` and see the difference

**Orientation.** Now you'll add a test block to `conversions.py` itself. When you run `conversions.py` directly, the test runs. When you import it into `main.py`, it doesn't. This is the side-effect prevention idiom in action.

**Prompt for Claude Code.** "Modify `conversions.py` to add this at the very bottom, after all the function definitions:

```python
if __name__ == '__main__':
    print('Running conversion tests...')
    print(f'60 mph = {mph_to_kph(60)} kph')
    print(f'150 lbs = {lbs_to_kg(150)} kg')
    print(f'100 USD = {usd_to_eur(100, 0.92)} EUR')
```

Now run `conversions.py` directly and show me the output. Then run `main.py` again and show me the output. Tell me what changed."

**What to expect.** When you run `conversions.py` directly, the test output appears: "Running conversion tests...". When you run `main.py`, it does not. The `if __name__ == "__main__"` block executed in the first case (where the module is the main program) but not in the second (where the module is imported). This is why modules can have test code without polluting programs that import them.

**Tests to validate.**
- Running `conversions.py` directly prints the test output.
- Running `main.py` does not print "Running conversion tests...".
- Both programs exit successfully.

### Task 3 — Write a test file with assertions and run pytest

**Orientation.** The gold standard for module testing is a separate file that imports your module and uses assertions. You'll create `test_conversions.py` and ask Claude Code to run it with pytest.

**Prompt for Claude Code.** "Create a file called `test_conversions.py` that imports the three functions from `conversions` and includes these assertions:

```python
from conversions import mph_to_kph, lbs_to_kg, usd_to_eur

def test_mph_to_kph():
    assert mph_to_kph(60) == 96.56040, 'Should convert 60 mph correctly'
    assert mph_to_kph(0) == 0, 'Should handle zero'

def test_lbs_to_kg():
    assert abs(lbs_to_kg(150) - 68.0388) < 0.001, 'Should convert 150 lbs within tolerance'
    assert lbs_to_kg(0) == 0, 'Should handle zero'

def test_usd_to_eur():
    assert usd_to_eur(100, 0.92) == 92.0, 'Should convert USD to EUR'
    assert usd_to_eur(50, 0.92) == 46.0, 'Should scale correctly'
```

Run these tests with pytest. Show me the output. If all pass, tell me how many assertions ran."

**What to expect.** pytest runs the three test functions and reports that all passed (or identifies which ones failed). The output shows you exactly which assertions held and which didn't. This is the pattern professionals use to catch regressions when they modify module functions.

**Tests to validate.**
- pytest runs without error.
- All test functions pass.
- pytest reports the correct number of assertions run (at least 6).

### Task 4 — Demonstrate random.seed() for reproducibility

**Orientation.** The `random` module produces different numbers each time you call it. But if you set the seed beforehand, you get the same sequence every time. This is how you debug randomness without making your code deterministic permanently.

**Prompt for Claude Code.** "Modify `main.py` to include this new section at the bottom:

```python
print()
print('--- Testing reproducibility with random.seed() ---')
import random

def roll_three_dice():
    return [random.randint(1, 6) for _ in range(3)]

# First run
random.seed(42)
print(f'First run with seed 42: {roll_three_dice()}')

# Second run with the same seed
random.seed(42)
print(f'Second run with seed 42: {roll_three_dice()}')

# Third run without resetting seed
print(f'Third run (new seed): {roll_three_dice()}')
```

Run the modified `main.py` and show me the output. Which rolls are the same? Which are different? Why?"

**What to expect.** The first and second runs (both with `random.seed(42)`) produce identical dice rolls. The third run produces different rolls because you didn't reset the seed. This shows that randomness is deterministic once you control the seed. This is powerful: you can test code that depends on randomness by seeding it, get the same output every time during development, then let it run unseeded in production.

**Tests to validate.**
- The first two rolls are identical.
- The third roll is different.
- The program runs without error.
- The rolls are all between 1 and 6 (valid die results).

### Stretch — design a project from scratch

If you've completed all four tasks, you're ready for a bigger challenge.

**Prompt for Claude Code.** "Create a three-file project: `dice.py`, `game.py`, and `test_game.py`.

`dice.py` should export:
- `roll_die(sides=6)` — return a random integer from 1 to sides
- `roll_dice(num_dice, sides=6)` — return a list of rolls
- `sum_dice(num_dice, sides=6)` — return the sum of rolls

Include a `if __name__ == '__main__'` block that demonstrates all three.

`game.py` should:
- Import the dice functions
- Simulate a simple game where two players each roll two six-sided dice
- The player with the higher sum wins
- Print who won and by how much
- Run 5 games with `random.seed(42)` so results are reproducible

`test_game.py` should:
- Import from both modules
- Write assertions that verify `roll_die()` returns values in range
- Write assertions that verify `sum_dice(2)` is between 2 and 12
- Run the game 5 times with the same seed and verify you get identical results

Run all three files and show me the output, including the test results."

**What you're testing here:**
- Can you structure code across multiple files?
- Do you understand that `random.seed()` controls randomness for the entire program, including imported modules?
- Can you write tests that import from modules and verify their behavior?
- Do you see how `if __name__ == "__main__"` lets each module demonstrate itself?

---

## Chapter summary

You can now do four things you could not do an hour ago.

You can use Python's standard library to solve common problems without writing the code from scratch. `import math` gives you square roots and logarithms. `import random` gives you randomness. `import datetime` gives you dates and times.

You can write your own modules — *.py* files with functions in them — and import them into other files in the same project. This lets you organize growing code into manageable, reusable pieces.

You can navigate the trade-off between `import module` (clear but verbose) and `from module import function` (short but requires care to avoid name collisions). You understand when each makes sense.

You understand that `if __name__ == "__main__"` is how Python modules separate code meant to be imported from code meant to be executed directly — a universal idiom that keeps importing safe.

And you now know that Python has a "batteries included" standard library of 200+ modules, and beyond that lies PyPI: hundreds of thousands of open-source packages, one `pip install` away.

The thing to watch for, going forward: as your programs grow and dependencies accumulate, keeping track of which modules you're using and which versions becomes important. For now, rely on the standard library when possible — it's stable and always available. When you move into data science or web development, you'll build on top of larger ecosystems like NumPy, pandas, Flask. The principle stays the same: modules are how Python grows beyond one file.

What you should now be able to teach a friend: how to import from the standard library, why you'd write a module, and what `if __name__ == "__main__"` does.

---

## Connections forward

Chapter 8 introduces strings — text in Python. You'll learn to manipulate them: slicing, concatenation, formatting, encoding. Several of the modules you've imported in this chapter — `datetime`, `csv`, `json` — work extensively with strings. You'll come back to them with string skills in hand.

Chapter 9 goes deeper with lists: indexing, slicing, iteration, and comprehensions. The `random` module's `random.choice()` and `random.shuffle()` will make sense in the context of list operations.

Later chapters will show you how to read and write files (Chapter 14), and then you'll use modules like `csv` and `json` to structure that file I/O in sophisticated ways. Modules are the thread that holds Python's standard library together.

---

## Research notes

**Concepts selected:** (1) importing standard library modules, emphasizing `math`, `random`, `datetime`; (2) writing and importing custom modules, the trade-off between import styles, and name collision management; (3) side effects, `__name__`, and the standard library vs. PyPI landscape.

**Primary sources:** OpenStax *Introduction to Python Programming* Chapter 7 (modules); Python official documentation (docs.python.org); PyPI homepage.

**Three-part Claude pattern:** Six code-prompt-test triples across the chapter. Examples: the `random` module example (random.choice, randint, shuffle), the custom `conversions` module, the `datetime` example. All runnable.

**Word count:** 5,250 words (target: 5,000–8,000).

**What changed from source:** Compressed seven OpenStax modules into three coherent concepts. Removed package installation detail (deferred to later chapters). Emphasized cold open (Lego brick analogy) and scene-driven teaching. Added explicit three-part prompts and tests for each major code example. Incorporated Attenborough × Feynman voice: conversational authority, specificity, trade-offs named, machinery visible.

**Still puzzling:** The best way to scaffold `pip install` for students in an introductory course — whether to show it here or defer it until students need a specific package from PyPI. Decided to mention it briefly but not show the mechanics; Chapter 15 (data science) will need it.

**What would change this reading:** If students come to this chapter with heavy prior experience in other languages (Java, C, C++), the module system might feel restrictive. Python's approach is more permissive than statically-typed languages, and that trade-off could be highlighted more. For now, pitched at first-time programmers.

---

**Tags:** modules, import, standard-library, namespace, side-effects, PyPI, code-organization

**Author:** Nik Bear Brown

**Voice anchor:** Contemporary Mathematics Chapter 1 (cold open, scene-driven, specification moves, trade-offs explicit)
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

##  AI Wayback Machine
**David Parnas** was wrote the foundational 1972 paper on modularity and information hiding — defining the principles modern Python modules implement.

**Run this:**

```
Who is David Parnas, and how does their work connect to modules we covered in this chapter? Keep it to three paragraphs. End with the single most surprising thing about their career or ideas.
```

→ Search **"David Parnas"** on Wikipedia.

**Now make the prompt better.** Try one of these:

- Ask it to apply David Parnas's ideas to a small Python program you'd write.
- Add a constraint: "Answer including criticisms or limits of David Parnas's framework."

What changes? What gets better? What gets worse?
