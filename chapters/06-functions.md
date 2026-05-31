# Chapter 6 — Functions

## Three suggested titles

- **Functions: Building blocks of reusable code**
- **Functions: From repetition to a single, named task**
- **Functions: The machinery of abstraction**

## TL;DR

A function is a named block of code you write once and call many times. Every button on your phone—calculator, camera, timer—is really a function waiting to be executed. Functions let you name complexity, specify inputs and outputs, and control where variables live in your program, which is where most bugs are caught.

---

## Opening: The smartphone calculator

Every modern pocket carries a calculator. Press the button labeled *÷* on a iPhone 15. That button is not running different code each time you press it. The division operation—subtract the exponent of the divisor from the numerator's exponent, normalize the result, handle zero—exists in one place. You press *÷*. iOS calls the function that does division. You press *÷* again three seconds later. The *same* function runs.

If Apple rewrote that entire division operation every time someone pressed that button, the app would be thousands of times larger. More important, if a bug were found—some edge case where dividing very large numbers produced the wrong answer—the engineer would have to fix it in hundreds of places.

Functions solve this. Write the code once. Give it a name. Call it by name whenever you need that task done. The principle applies whether you're building a calculator app or a spreadsheet or a program that simulates planetary motion.

A function is a named, reusable block of code that performs one task. This chapter teaches you how to write them, how to feed data into them, how to get results back out, and why the rules that govern where variables can be seen matter more than they seem to.

### Learning objectives

By the end of this chapter you will be able to:

- **Define and call** a function using the correct syntax.
- **Specify** parameters and arguments, and distinguish between positional, keyword, and default-value styles.
- **Design** functions that return values rather than only printing them.
- **Explain** what scope means, and debug programs where a variable is used outside its accessible region.
- **Trace** the call stack to understand the order in which functions execute and how control returns to the caller.
- **Apply** the three-part Claude pattern to verify that a function prompt produces correct code.

### Prerequisites

You've read Chapter 5 (Loops). You know what a for loop is, what `range()` does, what an accumulator is. You've used `print()` and know it's a function that takes arguments. You know the difference between a variable and a value, and you've seen what a `TypeError` looks like.

### Why this chapter matters

Every program of significant size is built from functions. Operating systems, web browsers, neural networks—all are assemblies of functions calling other functions in sequences. The student who does not understand scope will write programs that break in mysterious ways. The student who does understand scope can debug and refactor code with confidence. This chapter is where that discipline begins.

---

## Concept 1: Defining a function, calling it, documenting it

You are standing at a vending machine in a dorm hallway. You do not need to understand how the machine sorts change, counts bills, or triggers the motor that pushes the snack forward. You insert money. You press a button. Something happens. The machine returns your snack and your change. That is the interface.

A function works the same way. From the outside, you see the name and what it takes in and what it gives back. The implementation—the wiring, the machinery—is hidden. This is called *abstraction*. It is the single most important idea in programming.

### Defining a simple function

Here is a function that prints a welcome message:

```python
def welcome():
    """Print a greeting to the user."""
    print("Welcome to the program!")
```

Break this down sentence by sentence.

- `def` is the keyword that tells Python you are defining a function. It always comes first.
- `welcome()` is the function name (in snake_case—lowercase with underscores, never spaces). The parentheses are required, even when the function takes no input.
- `:` ends the definition line and signals that an indented block follows.
- The next line, indented, is the *docstring* — a description of what the function does, enclosed in triple quotes. This is not optional; write it every time. Docstrings are how future-you and other programmers know what the function is for.
- The last indented line is the function body — the code that runs when the function is called.

When you run this code, nothing happens yet. The function is defined but not called. Defining a function is like writing a recipe. Calling a function is like cooking that recipe.

### Calling a function

To call the function, you write its name followed by parentheses:

```python
welcome()
```

This line executes the function. The output is:

```
Welcome to the program!
```

You can call the same function as many times as you want:

```python
welcome()
welcome()
welcome()
```

Output:

```
Welcome to the program!
Welcome to the program!
Welcome to the program!
```

This is the entire point. Write the code once. Use it many times. If you find a bug in the message, fix it in one place.

### Etymology and notation

The word *function* comes from Latin *functio*, meaning "performance" or "execution." In mathematics, a function is a relationship: given an input, it always produces the same output. The *f* in *f(x) = 2x* is a function name. The *x* in parentheses is the input. In Python, we keep the same notation. `welcome()` is a function with no inputs. `fahrenheit_to_celsius(f)` is a function with one input.

### Worked example: Printing a BMI calculation header

Write a function that prints a header for a BMI (Body Mass Index) calculation program.

```python
def print_bmi_header():
    """Print the title and instructions for the BMI calculator."""
    print("=" * 30)
    print("BMI CALCULATOR")
    print("=" * 30)
    print("Enter your weight in kilograms and height in meters.")
```

Call the function:

```python
print_bmi_header()
```

Output:

```
==============================
BMI CALCULATOR
==============================
Enter your weight in kilograms and height in meters.
```

The function name is a verb phrase (`print_bmi_header`), not a noun. The docstring says what it does. The body uses only `print()` statements. Simple, clear, reusable.

> **Prompt for Claude.** "Write a Python function `print_bmi_header()` that prints a formatted header for a BMI calculator. Include a docstring. The header should be surrounded by a line of equal signs, have the title 'BMI CALCULATOR' centered or emphasized, and include a brief instruction about inputs."

> **Tests to validate.**
> - Call `print_bmi_header()` and verify output includes "BMI CALCULATOR".
> - Verify the output includes instructions mentioning kilograms and meters.
> - Verify there are no errors when the function is called.

### Common misconceptions

**The docstring is not a comment.** A comment is for you and other humans. A docstring is the official description of what the function does. It lives inside triple quotes and is part of the function's identity. Python's `help()` function reads docstrings. IDEs and documentation tools parse docstrings. Write them every time.

**Parentheses are not optional.** `welcome` is the function object (the thing itself). `welcome()` is a function call (the instruction to run it). Forgetting the parentheses is a common error and produces confusing results.

**A function must be defined before it is called.** If you write code that calls `welcome()` before you've written the `def welcome():` block, Python will raise a `NameError` before the function is ever executed.

---

## Concept 2: Parameters, arguments, and returning values

The vending machine accepts money and a button press. It returns a snack. A function should work the same way: accept inputs, process them, return an output. This is where functions become powerful.

### Parameters and arguments

A *parameter* is a variable in the function definition that represents an input. An *argument* is the actual value passed when the function is called.

Here is a function with one parameter:

```python
def fahrenheit_to_celsius(f):
    """Convert a Fahrenheit temperature to Celsius."""
    return (f - 32) * 5/9
```

- `f` is the parameter. It is defined in the function definition.
- When you call the function with `fahrenheit_to_celsius(32)`, the number `32` is the argument. It is assigned to the parameter `f`.

Here is the difference made concrete:

```python
temp = 98.6
result = fahrenheit_to_celsius(temp)
print(result)
```

Output:

```
37.0
```

The function takes the argument (`temp`, which holds `98.6`), assigns it to the parameter (`f`), runs the calculation, and returns the result.

### Return statements

A return statement ends the function and sends a value back to the code that called it. Notice the `fahrenheit_to_celsius` function above uses `return`. Many functions you've already used return values: `int("5")` returns the integer `5`. `round(3.7)` returns `4`. If a function does not have a `return` statement, it returns `None`, which means "no value."

Here is the same function written two ways:

```python
# Version 1: Returns a value
def fahrenheit_to_celsius(f):
    """Convert Fahrenheit to Celsius and return the result."""
    return (f - 32) * 5/9

result = fahrenheit_to_celsius(32)
print(f"32°F is {result}°C")
```

Output:

```
32°F is 0.0°C
```

```python
# Version 2: Prints instead of returning
def fahrenheit_to_celsius_v2(f):
    """Convert Fahrenheit to Celsius and print the result."""
    celsius = (f - 32) * 5/9
    print(f"{f}°F is {celsius}°C")

fahrenheit_to_celsius_v2(32)
```

Output:

```
32°F is 0.0°C
```

Both produce the same printed output, but they are different. In Version 1, the function returns a value that the caller can use—in another calculation, in a list, in a comparison. In Version 2, the function prints directly and returns `None`. The caller cannot capture or reuse the result. Version 1 is more flexible.

**Trade-off:** Returning values is more flexible than printing. Printing is sometimes clearer when the function's only purpose is to display information. In this book, we prefer returning values because they give the caller more options.

### Multiple parameters

A function can take multiple parameters. They are separated by commas in the definition. The arguments must match in number and type.

```python
def calculate_bmi(weight_kg, height_m):
    """Calculate BMI given weight in kg and height in meters."""
    bmi = weight_kg / (height_m ** 2)
    return bmi
```

Call it with two arguments, in order:

```python
my_bmi = calculate_bmi(68, 1.75)
print(f"BMI: {my_bmi:.1f}")
```

Output:

```
BMI: 22.2
```

If the number of arguments does not match the number of parameters, Python raises a `TypeError`:

```python
my_bmi = calculate_bmi(68)  # Missing the second argument
```

Error:

```
TypeError: calculate_bmi() missing 1 required positional argument: 'height_m'
```

The error message is exact: you provided one argument when two were required.

### Worked example: A tip calculator

Write a function that calculates the tip amount given a bill total and a tip percentage.

```python
def calculate_tip(bill_amount, tip_percent):
    """Calculate tip given bill amount and tip percentage (0-100)."""
    tip = bill_amount * (tip_percent / 100)
    return tip
```

Call the function:

```python
bill = 45.80
tip_rate = 20
tip_amount = calculate_tip(bill, tip_rate)
total = bill + tip_amount
print(f"Bill: ${bill:.2f}")
print(f"Tip ({tip_rate}%): ${tip_amount:.2f}")
print(f"Total: ${total:.2f}")
```

Output:

```
Bill: $45.80
Tip (20%): $9.16
Total: $54.96
```

> **Prompt for Claude.** "Write a Python function `calculate_tip(bill_amount, tip_percent)` that takes a bill amount in dollars and a tip percentage (where 100 = 100%) and returns the tip amount. Include a docstring. Test it with a $45.80 bill and 20% tip."

> **Tests to validate.**
> - `calculate_tip(45.80, 20) == 9.16` — basic calculation.
> - `calculate_tip(100, 15) == 15` — 15% of $100 is $15.
> - `calculate_tip(25.50, 18)` returns approximately 4.59.

### Common misconceptions

**Return is not print.** `return` sends a value back to the caller and stops the function. `print` displays text but does not return anything. If you return a value, the caller must do something with it—assign it to a variable, pass it to another function, or print it themselves.

**Parameters are local variables.** Once the function ends, the parameter variables disappear. You cannot access `f` or `height_m` outside their function.

---

## Concept 3: Scope and the call stack

Variables live in places. A variable defined outside all functions is *global*—it can be seen anywhere. A variable defined inside a function is *local*—it exists only inside that function. This is the most common source of confusion for new programmers.

### Global scope

A variable created outside a function has global scope:

```python
greeting = "Hello"

def print_greeting():
    """Print the greeting."""
    print(greeting)

print_greeting()
```

Output:

```
Hello
```

The variable `greeting` is global. The function `print_greeting()` can read it. The entire program can read it.

### Local scope

A variable created inside a function has local scope:

```python
def print_greeting():
    """Print a locally defined greeting."""
    greeting = "Hello"
    print(greeting)

print_greeting()
print(greeting)  # Error!
```

Output:

```
Hello
NameError: name 'greeting' is not defined
```

The variable `greeting` is local to `print_greeting()`. It exists only while the function runs. When the function ends, `greeting` is deleted from memory. Trying to access it outside the function raises a `NameError`.

### Why scope matters

Scope is a tool for controlling what code can see. If every variable were global, a large program would become chaos. A bug-fix in one function could break code in another function a thousand lines away. Limiting scope prevents that.

Here is an example where scope prevents a bug:

```python
total_sales = 0

def add_sale(amount):
    """Add a sale amount to the global total."""
    global total_sales
    total_sales = total_sales + amount

def format_currency(amount):
    """Return amount as a formatted currency string."""
    total_sales = 0  # This is a local variable, not the global one
    if amount < 0:
        total_sales = amount * -1  # Modifying the local copy
    return f"${total_sales:.2f}"

add_sale(100)
print(format_currency(50))
print(total_sales)
```

Output:

```
$50.00
100
```

The global `total_sales` is `100`. The function `format_currency()` defines its own local `total_sales`, which shadows (hides) the global one. Modifying the local variable does not affect the global one. This is usually good design—`format_currency()` should not have side effects on global state.

**Trade-off:** Global variables are easy to use but make programs hard to debug. Local variables are slightly more work (you must pass them as arguments) but make programs predictable. We prefer local scope because the extra clarity is worth the effort.

### The call stack

When one function calls another, Python keeps track of the order in which they were called. This is the *call stack*. Understanding the call stack helps you trace program execution by hand.

```python
def temperature_report(f):
    """Report temperature in both scales."""
    c = convert_to_celsius(f)  # Call the convert function
    print(f"{f}°F = {c:.1f}°C")

def convert_to_celsius(f):
    """Convert Fahrenheit to Celsius."""
    return (f - 32) * 5/9

temperature_report(98.6)
```

The execution order:

1. Main program calls `temperature_report(98.6)`.
2. `temperature_report` starts. It calls `convert_to_celsius(98.6)`.
3. `convert_to_celsius` executes and returns `37.0`.
4. Control returns to `temperature_report`, which prints the result.
5. Control returns to main program.

This is the call stack in action. The most recent call is at the top. When a function finishes, control pops back to wherever that function was called.

When you get a Python error with multiple lines of red text, you are looking at a stack trace — a record of which functions were active when the error occurred.

### Worked example: Debugging with the call stack

Write a function that calculates body mass index and another that validates the inputs:

```python
def validate_bmi_input(weight_kg, height_m):
    """Check that weight and height are positive numbers."""
    if weight_kg <= 0 or height_m <= 0:
        return False
    return True

def calculate_bmi(weight_kg, height_m):
    """Calculate BMI if inputs are valid."""
    if not validate_bmi_input(weight_kg, height_m):
        return None
    return weight_kg / (height_m ** 2)

# Test with valid input
bmi = calculate_bmi(70, 1.75)
print(f"BMI: {bmi:.1f}")

# Test with invalid input
bmi = calculate_bmi(-70, 1.75)
print(f"BMI: {bmi}")
```

Output:

```
BMI: 22.9
BMI: None
```

Call stack when `calculate_bmi(70, 1.75)` is called:

```
Stack (top to bottom):
1. validate_bmi_input(70, 1.75) — returns True
2. calculate_bmi(70, 1.75) — continues to return calculation
3. Main program — receives 22.86...
```

> **Prompt for Claude.** "Write a Python function `validate_bmi_input(weight_kg, height_m)` that returns `True` if both weight and height are positive numbers, and `False` otherwise. Also write a function `calculate_bmi(weight_kg, height_m)` that calls `validate_bmi_input()` and returns the BMI only if inputs are valid. Return `None` if inputs are invalid."

> **Tests to validate.**
> - `calculate_bmi(70, 1.75)` returns approximately 22.9.
> - `calculate_bmi(-70, 1.75)` returns `None`.
> - `calculate_bmi(70, 0)` returns `None`.
> - Direct call: `validate_bmi_input(70, 1.75)` returns `True`.

### Common misconceptions

**Local variables in different functions are not connected.** Two functions can both have a variable named `x`. They are completely separate. One function's `x` is not the other function's `x`.

**The `global` keyword is a warning flag, not a tool.** You can declare `global total_sales` to modify a global variable inside a function, but doing so makes the function harder to reason about. If you find yourself using `global`, ask whether that variable should be passed as a parameter instead.

---

## Integration and synthesis

Functions solve one central problem: repetition. Without functions, a program that needs to calculate temperature conversions at ten different points would contain the conversion formula ten times. If the formula is wrong—or if you discover a better formula—you must fix it ten times. This is error-prone.

With functions, the formula lives in one place. You name it. You call it. If you discover a bug, you fix it once.

But functions do more than eliminate repetition. They *name* complexity. When you write `fahrenheit_to_celsius(98.6)`, anyone reading your code sees immediately what calculation is happening. The name carries meaning. This is clarity.

They also *hide* complexity. The caller does not need to know how the conversion is done. They pass in Fahrenheit, get back Celsius. The mechanism is someone else's problem.

They enforce *modularity*. Each function should do one thing. If a function does too much, it becomes hard to test, hard to debug, hard to reuse. A good function is a named black box: clear input, clear output, clear contract.

They establish *scope*, which prevents variables from interfering with each other across a large program. This is where most bugs in large programs live—a variable modified in the wrong place, at the wrong time.

The three concepts you've learned:

1. **Defining and calling:** How to write a function and execute it by name.
2. **Parameters, arguments, returns:** How to feed data in and get results back out.
3. **Scope and the call stack:** Where variables live and in what order functions execute.

A real program combines all three. A function that calculates a tip (Concept 2) calls a helper function to validate the input (Concept 3 — controlling scope). The main program calls the tip calculator repeatedly, collecting results in a list. Each call is independent; local variables do not leak into the next call.

When you are reading someone else's code, the call stack is your map. Follow the execution line by line. When a function is called, jump to its definition. When a return statement is reached, jump back to the call site. This mechanical tracing is the foundation of debugging.

---

## Graduated exercises

### Warm-up

1. Write a function `print_name(name)` that takes a name as an argument and prints "Hello, [name]!" Call it three times with different names.

2. Write a function `square(x)` that takes a number and returns its square. Call it with 5 and assign the result to a variable. Print the variable.

3. Write a function `add(a, b)` that returns the sum of two numbers. Do not use `print` inside the function—use `return`.

### Application

4. Write a function `password_validator(password)` that returns `True` if the password is at least 8 characters long and contains at least one number, and `False` otherwise. Test it with at least three passwords.

5. Write a function `pizza_cost(diameter_inches, price_per_sq_inch)` that calculates the cost of a pizza given its diameter and the price per square inch. The area of a circle is π*r*². Return the total cost. Test it with a 12-inch pizza at $0.50 per square inch.

6. Write two functions: `read_temperature()` reads a Fahrenheit temperature from the user and returns it as a float. `convert_and_report(f)` takes Fahrenheit, converts it to Celsius using the formula from Concept 2, and prints both values. Call them together in a main program.

### Synthesis

7. Write a program with three functions:
   - `get_student_data()` reads a student's name, midterm score, and final score from the user. Returns all three as a tuple or separate variables (you choose).
   - `calculate_grade(midterm, final)` takes the two scores, calculates their average, and returns the average.
   - `print_transcript(name, average)` prints a report with the name and the average grade.
   
   Call these functions in order to build a complete grade report.

8. Write a function `list_average(numbers)` that takes a list of numbers and returns the average. Do not modify the list. Test it with at least two different lists. Then write a second function `grade_from_average(average)` that takes an average and returns a letter grade (A for 90+, B for 80-89, C for 70-79, D for 60-69, F for below 60). Use both functions to read test scores from the user, calculate the average, determine the grade, and print the result.

### Challenge

9. Write a function `fibonacci(n)` that returns the nth Fibonacci number, where the sequence is 0, 1, 1, 2, 3, 5, 8, ... Hint: This is easier with a loop and local variables than you might think. Test it with n = 0, 5, and 10.

10. Write a program that manages a simple bank account. Include:
    - A global variable `balance` initialized to 100.
    - A function `deposit(amount)` that adds to the global balance.
    - A function `withdraw(amount)` that subtracts from the global balance (only if funds are available).
    - A function `report_balance()` that returns the current balance.
    
    Call these functions in a loop that repeatedly asks the user what action to take. Use `global balance` in the functions that modify it.

---

## Claude Code

You have read about functions. You have written small ones. Now you will ask Claude Code to build a modest but complete project: three temperature conversion functions with docstrings, plus a test file that validates them against fixed points—the physical facts that every conversion formula must respect.

> **Claude Code prompt.**
>
> "Write a `temperature.py` file containing three functions: `f_to_c(f)` converts Fahrenheit to Celsius, `c_to_f(c)` converts Celsius to Fahrenheit, `c_to_k(c)` converts Celsius to Kelvin. Each function should include a docstring explaining what it does and what units it expects. Use standard conversion formulas.
> 
> Then write a `test_temperature.py` file with at least eight assertions using pytest that validate all three functions against known fixed points: water freezes at 32°F = 0°C, water boils at 212°F = 100°C, human body temperature is approximately 98.6°F = 37°C, the point where Celsius and Fahrenheit are numerically equal (-40°C = -40°F), and absolute zero is -273.15°C = 0K. Run the tests and report whether they pass."

**What to expect.** Claude Code will generate both files at once. It will choose appropriate formulas (9/5, 5/9, plus 32 for Fahrenheit conversions; plus 273.15 for Kelvin). The test file will use `pytest` assertions with reasonable tolerance for floating-point rounding. When you run `pytest test_temperature.py`, all tests should pass. The docstrings will be simple one-liners, not ornate.

**Stretch.** Ask Claude Code to add a fourth function `k_to_c(k)` that converts Kelvin to Celsius, then add four more test assertions validating it against the same fixed points. This tests whether you can extend existing code without breaking it.

---

## Chapter summary

A function is a named block of code you write once and call many times. Every function has a definition (using `def`), parameters and arguments (inputs), return values (outputs), and a scope (where variables live).

Defining a function is like writing a recipe. Calling it is like cooking. The function name should be a verb phrase describing what it does. Always include a docstring—the triple-quoted description that tells the next reader (or future-you) what the function does.

Parameters are the variables in the function definition. Arguments are the values passed when the function is called. Multiple parameters are separated by commas. The order matters unless you use keyword arguments.

Return statements send values back to the caller. A function without a return statement returns `None`. Returning a value is more flexible than printing because the caller can use the returned value in further calculations.

Scope determines where a variable can be accessed. Global variables (defined outside functions) can be seen anywhere. Local variables (defined inside functions) exist only while the function runs. This is a feature, not a bug—it prevents variables from interfering with each other.

The call stack tracks which functions are currently running and in what order. When you get an error with multiple lines of output, you are looking at a stack trace. Read it from bottom to top: the bottom is where the error occurred; the lines above show which functions were active.

When building a program of meaningful size, you will call functions from inside other functions. `temperature_report()` calls `convert_to_celsius()`. A main program calls several functions in sequence. Understanding the order of execution—the call stack—is how you debug.

The next chapter introduces modules, which are collections of functions someone else has already written. NumPy, pandas, matplotlib, requests—all are modules. You've already used `math.sqrt()`. Modules are functions organized into libraries.

---

## Connections forward

In Chapter 7 (Modules), you will use functions from the standard library: `math.sqrt()`, `random.choice()`, `datetime.now()`. These are functions someone else wrote and shared. You will learn how to import them and what each one does.

In Chapter 9 (Lists), you will iterate over lists with `for` loops and use functions like `sum()`, `len()`, `max()`, `min()`. These are built-in functions. They all follow the same pattern: name, parentheses, arguments, return value.

In Chapter 11 (Classes), you will see that a method is a function that lives inside a class. Instead of `fahrenheit_to_celsius(98.6)`, you might write `converter.fahrenheit_to_celsius(98.6)`. The principle is the same: you call a name, pass arguments, get a result back.

The discipline of writing good functions—one task per function, clear naming, clear parameters and returns, local scope where possible—is the foundation of every software engineer's work. Master it now, and refactoring, debugging, and collaboration become manageable. Skip it, and programs become fragile and hard to read.

---

## Tags

function, parameter, argument, return, scope, call-stack, docstring, reusability, abstraction, side-effects

## What would change my mind

If I discovered that students struggled more with the three-part Claude pattern when applied to functions than to earlier concepts (loops, decisions), I would simplify the prompts or provide more worked examples of the validation step.

## Still puzzling

Why students find local scope counterintuitive. I suspect it is because global variables feel simpler ("just use a variable everyone can see"), and the cost of that simplicity—bugs that are hard to trace—does not become apparent until the program is large. An earlier, forced experience with intentional scope violations might help.
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

**Connection to previous chapters:** Chs 1-5 produced working but cluttered code; Ch 6 organizes it.

**Preview of next chapter:** Chapter 7 covers modules. You'll split the game across files — `game.py`, `combat.py`, `world.py`, `player.py` — and use the standard library (`random`, `time`, `os`).


---

##  AI Wayback Machine
**Haskell Curry** was mathematician whose work on combinatorial logic prefigured functional programming — Haskell language is named after him.

**Run this:**

```
Who is Haskell Curry, and how does their work connect to functions we covered in this chapter? Keep it to three paragraphs. End with the single most surprising thing about their career or ideas.
```

→ Search **"Haskell Curry"** on Wikipedia.

**Now make the prompt better.** Try one of these:

- Ask it to apply Haskell Curry's ideas to a small Python program you'd write.
- Add a constraint: "Answer including criticisms or limits of Haskell Curry's framework."

What changes? What gets better? What gets worse?
