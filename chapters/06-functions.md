# Chapter 6 — Functions
*Write it once, name it, call it forever — and why the rules about where variables live matter more than they seem.*

Here is a program fragment that calculates a tip three times:

```python
tip1 = 45.80 * (20 / 100)
tip2 = 32.00 * (20 / 100)
tip3 = 78.50 * (20 / 100)
```

Now imagine the tip rate changes from 20% to 18%. You change the number in three places. Imagine the formula is wrong — you divided when you should have multiplied. You fix it in three places. Imagine you're writing a restaurant billing system and the tip appears forty times. You touch forty lines, and you'd better not miss one.

This is the problem functions solve. Not the shallow problem of saving keystrokes — the deep problem of having one authoritative version of a piece of logic that you name, that you call, and that you fix in one place when it's wrong. The rest of the chapter is the mechanics of that idea.

---

## What a Function Is

A function is a named, reusable block of code. You define it once. You call it by name whenever you need that task performed. The name carries meaning — it tells every reader (including future you) what the block of code does, without them having to read the implementation.

The definition syntax has four parts, each required:

```python
def fahrenheit_to_celsius(f):
    """Convert a Fahrenheit temperature to Celsius."""
    return (f - 32) * 5/9
```

`def` is the keyword that signals a function definition. `fahrenheit_to_celsius` is the name — a verb phrase in snake_case, lowercase with underscores. `(f)` declares one parameter: a variable that will hold the input when the function is called. The colon ends the definition line and signals that an indented block follows. The triple-quoted string is the **docstring** — not a comment, but the official description of what the function does and what it expects. The last line is the body, which runs when the function is called.

Defining the function does nothing by itself. A function definition is a recipe. You have to cook it:

```python
result = fahrenheit_to_celsius(98.6)
print(result)   # 37.0
```

`fahrenheit_to_celsius(98.6)` is the **call**. The number `98.6` is the **argument** — the actual value passed in. Inside the function, it gets assigned to the parameter `f`. The function runs, computes `(98.6 - 32) * 5/9`, and **returns** `37.0` to the caller. The caller assigns that returned value to `result`.

<!-- → [FIGURE: annotated diagram of a function call — left side shows the call site with argument 98.6, arrow into function definition showing parameter f = 98.6, body executing, return value 37.0 traveling back to the call site — student should see the data flow both in and out] -->

The docstring matters. Python's `help()` function reads it. IDEs display it when you hover over a function name. Write one every time, even for simple functions. "Convert a Fahrenheit temperature to Celsius" is enough.

### Parentheses Are Syntax, Not Style

`fahrenheit_to_celsius` — without parentheses — is the function object itself, the thing sitting in memory. `fahrenheit_to_celsius(98.6)` — with parentheses and an argument — is the instruction to run it. Forgetting the parentheses is a common error and produces a confusing result: you get back the function object, not a number.

---

## Parameters, Arguments, and the Return Statement

The tip problem at the top of this chapter had three nearly identical lines because the formula was written out each time. A function solves this by accepting the varying parts as inputs:

```python
def calculate_tip(bill_amount, tip_percent):
    """Return the tip amount given a bill and a percentage (0–100)."""
    return bill_amount * (tip_percent / 100)
```

Now the three calls become:

```python
tip1 = calculate_tip(45.80, 20)
tip2 = calculate_tip(32.00, 20)
tip3 = calculate_tip(78.50, 20)
```

Change the formula in one place, and all three are fixed. The function has two **parameters** — `bill_amount` and `height_m` — each a local variable that exists only inside the function. The arguments you pass when calling the function are assigned to those parameters in order.

If you pass the wrong number of arguments, Python stops immediately with a `TypeError` that names the missing parameter:

```python
calculate_tip(45.80)
# TypeError: calculate_tip() missing 1 required positional argument: 'tip_percent'
```

The error is exact. It tells you what to fix.

### What `return` Does

`return` ends the function and sends a value back to the caller. Everything after a `return` statement in the same function is unreachable — Python will never execute it.

A function without a `return` statement returns `None`, which means "no value." This is fine when the function's sole purpose is to produce output (like a function that prints a header). It's a problem when you expect to use the result in a calculation.

The distinction is important enough to see side by side:

```python
# Version A: returns a value
def celsius_from_fahrenheit(f):
    return (f - 32) * 5/9

temp = celsius_from_fahrenheit(212)
print(f"Boiling point: {temp}°C")    # 100.0
doubled = celsius_from_fahrenheit(32) * 2   # can use in arithmetic
```

```python
# Version B: prints directly, returns None
def print_celsius(f):
    print(f"{f}°F = {(f - 32) * 5/9}°C")

result = print_celsius(212)
print(result)    # None — the function printed but returned nothing
```

Version A gives the caller options. Version B is a dead end. Prefer returning values; let the caller decide what to do with them.

### A Worked Example: BMI with Validation

Two functions that call each other. The first validates inputs. The second does the computation, but only if validation passes.

```python
def valid_bmi_inputs(weight_kg, height_m):
    """Return True if weight and height are both positive."""
    return weight_kg > 0 and height_m > 0

def calculate_bmi(weight_kg, height_m):
    """Return BMI, or None if inputs are invalid."""
    if not valid_bmi_inputs(weight_kg, height_m):
        return None
    return weight_kg / (height_m ** 2)
```

```python
print(calculate_bmi(70, 1.75))    # 22.857...
print(calculate_bmi(-70, 1.75))   # None
print(calculate_bmi(70, 0))       # None
```

`calculate_bmi` calls `valid_bmi_inputs` and makes a decision based on its return value. The validation logic lives in one place. If the rules change — say, height must be at least 0.5 meters — you fix `valid_bmi_inputs` once.

<!-- → [FIGURE: call-and-return diagram for calculate_bmi(70, 1.75) — shows calculate_bmi calling valid_bmi_inputs, return value True traveling back, calculate_bmi continuing to the arithmetic, final return value 22.86 traveling back to the caller — labels each arrow with the value being passed] -->

---

## Scope: Where Variables Live

Here is the most common mystery in early programs:

```python
def compute():
    result = 42
    return result

compute()
print(result)   # NameError: name 'result' is not defined
```

You computed something. You printed it. Why does Python say `result` doesn't exist?

Because `result` is **local** to `compute`. It was created inside the function. It exists only while the function runs. When the function returns, Python deletes `result` from memory. The line `print(result)` outside the function finds nothing.

This is scope. A variable created inside a function has **local scope** — it's visible only inside that function. A variable created outside all functions has **global scope** — it's visible everywhere.

```python
language = "Python"    # global

def greet():
    print(f"Welcome to {language}!")    # reads global variable — works

greet()    # Welcome to Python!
```

The function can *read* the global variable. But if it tries to *assign* to it without special syntax, Python creates a new local variable instead of modifying the global one:

```python
count = 0    # global

def increment():
    count = count + 1    # ERROR: UnboundLocalError

increment()
```

Python sees the assignment `count = ...` and decides `count` is a local variable inside `increment`. But then it tries to read `count` on the right side of the assignment — and the local `count` hasn't been assigned yet. That's the `UnboundLocalError`.

To actually modify a global variable inside a function, you declare it explicitly:

```python
count = 0

def increment():
    global count
    count = count + 1

increment()
increment()
print(count)    # 2
```

`global count` tells Python: "when I say `count` in this function, I mean the global one." Now the assignment modifies the global.

<!-- → [INFOGRAPHIC: two-region diagram — global scope box containing language = "Python" and count = 0; local scope box inside greet() containing no variables, with arrow showing read access to global language; local scope box inside increment() with its own count (showing the shadowing failure), then a separate version with global keyword and arrow showing write access to global count — student should see the asymmetry: reading global is automatic, writing requires explicit declaration] -->

### Why Scope Is a Feature, Not a Restriction

In a small program, global variables feel convenient. In a large program — or any program worked on by more than one person — they become a hazard. If a variable is global, any function anywhere can modify it. When something goes wrong, you have to check every function in the program to find what changed the value.

Local scope is a contract: a function's variables stay inside the function. The only things that cross the boundary are explicit: arguments going in, return values coming out. This makes functions predictable. You can reason about a function by reading just that function, without tracking down every place the global state might be modified.

The design preference that follows from this: pass state in as arguments, return new state as values. Avoid `global` declarations except where genuinely necessary (like tracking a program-wide total). The extra typing is cheap; the clarity is worth it.

### The Call Stack

When one function calls another, Python tracks the sequence in a **call stack** — a list of which functions are currently active, most recent on top.

```python
def temperature_report(f):
    c = fahrenheit_to_celsius(f)
    print(f"{f}°F = {c:.1f}°C")

def fahrenheit_to_celsius(f):
    return (f - 32) * 5/9

temperature_report(212)
```

Execution order:
1. Main program calls `temperature_report(212)`.
2. `temperature_report` starts, calls `fahrenheit_to_celsius(212)`.
3. `fahrenheit_to_celsius` executes, returns `100.0`.
4. Control returns to `temperature_report`, which now has `c = 100.0`.
5. `temperature_report` prints and returns.
6. Control returns to the main program.

When Python shows you a multi-line error traceback, you're reading the call stack at the moment the error occurred. The bottom line is where the error happened. The lines above it show which functions were active, in order from outermost to innermost. Read a traceback from bottom to top: find the error, then trace how you got there.

---

## What Functions Actually Do to a Program

Return to the tip problem. Three functions that clean up the calculation, validate inputs, and report results:

```python
def valid_tip_inputs(bill, percent):
    """Return True if bill is positive and percent is between 0 and 100."""
    return bill > 0 and 0 <= percent <= 100

def calculate_tip(bill, percent):
    """Return tip amount, or None if inputs are invalid."""
    if not valid_tip_inputs(bill, percent):
        return None
    return bill * (percent / 100)

def tip_report(bill, percent):
    """Print a formatted tip report."""
    tip = calculate_tip(bill, percent)
    if tip is None:
        print("Invalid inputs.")
        return
    print(f"Bill:  ${bill:.2f}")
    print(f"Tip:   ${tip:.2f}")
    print(f"Total: ${bill + tip:.2f}")

tip_report(45.80, 20)
```

Output:

```
Bill:  $45.80
Tip:   $9.16
Total: $54.96
```

Three functions. Each does one thing. `valid_tip_inputs` checks rules. `calculate_tip` does arithmetic. `tip_report` formats output. If the tip formula changes, one function changes. If the validation rules change, one function changes. If the output format changes, one function changes. Nothing else is touched.

This modularity — one task per function, clear inputs, clear outputs — is the discipline that makes large programs manageable. It's not about elegance. It's about the practical fact that you will need to change your code, and you need to be able to change it without breaking everything else.

*What would change my mind:* The preference for returning values over printing is a design heuristic, not a law. Functions whose only purpose is display — a `print_header()` function, a `display_menu()` function — are perfectly fine without a return value. What would change my recommendation is evidence that forcing return values on display functions makes programs clearer; so far the evidence runs the other direction.

*Still puzzling:* Why scope feels unnatural to beginners when it matches how most other bounded systems work — a variable inside a loop only exists in the loop, a variable inside an `if` block is visible after the block (in Python; not in other languages). The inconsistency may be the source of the confusion. Python's scope rules are not fully uniform, and the exceptions are where students get lost.

---

## Exercises

### Warm-up

**Exercise 6.1** *(Define and call.)*
Write a function `print_name(name)` that takes a name and prints `"Hello, [name]!"`. Call it three times with different names.

**Exercise 6.2** *(Return vs. print.)*
Write a function `square(x)` that returns the square of its argument. Do not use `print` inside the function. Assign the result to a variable, then print the variable. Explain in one sentence why this is different from a version that prints directly.

**Exercise 6.3** *(Spot the scope error.)*
The following program raises a `NameError`. Identify the line that causes it and explain why.

```python
def compute_area(radius):
    area = 3.14159 * radius ** 2
    return area

compute_area(5)
print(area)
```

### Application

**Exercise 6.4** *(Password validator.)*
Write a function `valid_password(password)` that returns `True` if the password is at least 8 characters long and contains at least one digit, and `False` otherwise. Test with at least three passwords, including edge cases.

**Exercise 6.5** *(Pizza cost.)*
Write a function `pizza_cost(diameter_inches, price_per_sq_inch)` that calculates the cost of a circular pizza. Area of a circle is `π × r²` (use `3.14159` or import `math.pi`). Return the total cost. Test with a 12-inch pizza at $0.50 per square inch.

**Exercise 6.6** *(Two-function pipeline.)*
Write `read_temperature()` that prompts the user for a Fahrenheit temperature and returns it as a float. Write `convert_and_report(f)` that converts to Celsius and prints both values. Call them in sequence in a main program.

### Synthesis

**Exercise 6.7** *(Grade report pipeline.)*
Write three functions that work in sequence:
- `get_student_data()` reads a student's name, midterm score, and final score from the user. Returns them.
- `calculate_average(midterm, final)` returns the average of the two scores.
- `print_transcript(name, average)` prints a formatted report with name and grade.

Call all three from a main block.

**Exercise 6.8** *(List average with grade.)*
Write `list_average(numbers)` that takes a list of numbers and returns their average without modifying the list. Write `letter_grade(average)` that returns a letter grade (A for 90+, B for 80–89, C for 70–79, D for 60–69, F below 60). Use both together to read a list of scores from the user, compute the average, and print the letter grade.

### Challenge

**Exercise 6.9** *(Fibonacci without recursion.)*
Write a function `fibonacci(n)` that returns the nth Fibonacci number (sequence: 0, 1, 1, 2, 3, 5, 8...). Use a loop and local variables, not recursion. Test with n = 0, 5, and 10.

**Exercise 6.10** *(Bank account with functions.)*
Write a program managing a simple bank account. Use a global variable `balance` initialized to 100. Write `deposit(amount)` that adds to the balance, `withdraw(amount)` that subtracts (only if funds are available), and `report_balance()` that returns the current balance. Use `global balance` in the functions that modify it. Then explain in a comment why using `global` here is a design smell — and what you'd do instead in a larger program.

---

## LLM Exercise — Chapter 06: Functions (Text Adventure Project)

**Project:** Text Adventure.
**What you're building this chapter:** the big refactor — extract named functions from the growing game.
**Tool:** **Claude Code**.

---

**The Prompt:**

```
Chapter 6 of my Text Adventure project. Chapter 6 covered: def;
parameters; return values; scope (local vs. global); default
arguments; functions as the unit of code reuse.

Refactor the growing game.py into named functions. Common ones:

1. **`describe_room(room_name)`** — prints the description of
   the named room. Currently you have inline `if/elif/else`
   blocks per room; this consolidates into a function.

2. **`get_command()`** — prompts the player, normalizes the
   input, returns the command string. Currently inline in the
   game loop.

3. **`take_damage(amount, defense)`** — computes damage after
   defense reduction. Returns the new health. Default
   defense = 0.

4. **`heal(amount)`** — returns the new health after healing,
   capped at max_health. Default amount = 10.

5. **`print_stats()`** — prints the player's current stats in
   a clean format. Currently inline in Ch 2.

6. **`combat(enemy_name, enemy_health, enemy_attack)`** — the
   full combat loop. Returns True if player won, False if
   player died.

Then replace the inline code in your game loop with calls to
these functions. The game should behave identically — the
refactor is for code quality, not behavior change.

In addition to the code, produce the prompt + tests. Tests now
matter more:
- Test each function in isolation. (`assert take_damage(50, 10)
  == 60` if starting health is 100.)
- Verify the integration test still works: play the game
  through, the behavior matches.

A subtle point: the chapter covered scope. Some of these
functions need to read or modify global state (player_health,
player_gold). The cleanest pattern: pass state in as arguments,
return new state. Avoid `global` declarations. Even if it
requires more typing in this chapter, it sets up Ch 11 (classes)
beautifully.

End with: name the function in your code with the WORST scope
hygiene (which uses globals it shouldn't). When classes arrive
in Ch 11, that function will become a method on a Player
class — explain why.
```

---

**What this produces:** A refactored game with named functions + tests for each.

**Connection to previous chapters:** Chs 1–5 produced working but cluttered code; Ch 6 organizes it.

**Preview of next chapter:** Chapter 7 covers modules. You'll split the game across files — `game.py`, `combat.py`, `world.py`, `player.py` — and use the standard library (`random`, `time`, `os`).

---

## AI Wayback Machine

**Haskell Curry** was a mathematician whose work on combinatorial logic prefigured functional programming — the Haskell language is named after him.

**Run this:**

```
Who is Haskell Curry, and how does their work connect to functions we covered in this chapter? Keep it to three paragraphs. End with the single most surprising thing about their career or ideas.
```

→ Search **"Haskell Curry"** on Wikipedia.

**Now make the prompt better.** Try one of these:

- Ask it to apply Haskell Curry's ideas to a small Python program you'd write.
- Add a constraint: "Answer including criticisms or limits of Haskell Curry's framework."

What changes? What gets better? What gets worse?
