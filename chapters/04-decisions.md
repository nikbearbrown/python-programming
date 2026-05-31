# Chapter 4 — Decisions

## TL;DR

A Boolean is a value that is either true or false. Python decides which branch of code to run by evaluating conditions — expressions that produce Booleans — then executing different blocks based on the result. Combine conditions with `and`, `or`, and `not` to make complex decisions; know when one chain beats nested if statements to keep code clear.

---

## Cold open — The gate agent

You are standing at an airport security checkpoint. A TSA agent checks your ID against the boarding pass. Four questions run in sequence:

1. Is the photo ID present and valid?
2. Is the boarding pass present and matches the ID?
3. Is today's date before the flight date?
4. Are no security flags raised on this passenger?

If all four are yes, the agent waves you through. If any is no, you stop. The agent is doing what every program does: evaluating conditions and deciding what code runs next.

In this chapter, you learn to write those decisions in Python. You'll learn how to evaluate a single condition, how to chain multiple conditions together, how to nest decisions inside each other, and when to refactor a tangle of decisions into something clearer. By the end, you'll see that the logic of human decision-making — if this, then that, else do something else — maps exactly onto the structure of code.

### Learning objectives

By the end of this chapter you will be able to:

- Distinguish Boolean values from other Python types and convert values to Booleans using truthiness rules.
- Use comparison operators to test relationships between values and produce Boolean results.
- Write `if`, `elif`, and `else` statements that execute different code paths based on conditions.
- Combine multiple conditions with `and`, `or`, and `not` to express complex decision logic.
- Recognize when nested decisions or refactored functions are clearer than long chains of conditions.
- Identify and fix common decision-logic errors: confusing `=` with `==`, misunderstanding operator precedence, and forgetting edge cases.

### Prerequisites

- Chapter 2 (Expressions): understanding of operators and how to read expressions left to right.
- Chapter 3 (Objects): comfort with different data types (int, float, string).
- A sense of how indentation works in Python (we will teach the syntax, but the idea is: indented code belongs to the block above it).

### Why this chapter matters

Every real program makes decisions. A banking system decides whether to approve a withdrawal. A video game decides whether a player's move is legal. A machine-learning model decides what to recommend. The gate agent's logic runs at scale — millions of decisions per second in a server — but the foundation is always the same: test a condition, take a path.

This chapter teaches you to think and write that way.

---

## Concept 1 — Boolean values and truthiness

A Boolean is a data type with exactly two values: `True` and `False`. Named for George Boole, a 19th-century English logician who built the algebra of true and false, Booleans are the atoms of decision-making. Every condition you write eventually becomes a Boolean.

### Creating and using Boolean values

The simplest way to create a Boolean is to write the literal:

```python
is_raining = True
is_frozen = False
logged_in = True
```

`True` and `False` are keywords in Python — capitalization is required. `true` and `false` will produce a `NameError`.

You can also create Booleans by converting other types using the `bool()` function. This is where it gets interesting. The conversion follows a single rule: **is the value "empty" or "zero" in some sense?** If yes, the Boolean is `False`. If no, it's `True`. This is called *truthiness*.

```python
# Rule: zero, empty, or None is False. Everything else is True.

bool(0)           # False
bool(1)           # True
bool(-5)          # True
bool(0.0)         # False
bool(3.14)        # True

bool("")          # False — empty string
bool("hello")     # True — non-empty string
bool([])          # False — empty list (we'll meet lists in Chapter 9)
bool([1, 2, 3])   # True — non-empty list
```

Truthiness matters because you can use a value directly in a condition without comparing it. Watch:

```python
name = input("Enter your name: ")
if name:                    # Truthy: non-empty string proceeds
    print(f"Hello, {name}")
else:
    print("Please enter a name.")

count = 0
if count:                   # Falsy: zero does not proceed
    print(f"You have {count} items.")
else:
    print("You have no items.")
```

> **Prompt for Claude.** "Write a Python snippet that uses the `bool()` function to convert an integer, a float, a string, and a list to Booleans. Include both truthy and falsy examples. Show the results with print statements."

> **Tests to validate.**
> - `bool(0) == False` and `bool(1) == True`
> - `bool("") == False` and `bool("text") == True`
> - The output shows all four conversions, one per line.

This is convenient, but it can hide bugs if you're not careful. The expression `if name:` is shorter than `if name != ""` but less explicit about what you're checking. Both are used in real Python code; clarity wins when in doubt.

### Comparison operators produce Booleans

The gate agent compared your ID to your boarding pass. In Python, comparison operators do that work. There are six:

| Operator | Meaning | Example |
|----------|---------|---------|
| `==` | Equal to | `age == 18` → `True` if age is 18 |
| `!=` | Not equal to | `status != "active"` → `True` if status is not "active" |
| `<` | Less than | `score < 100` → `True` if score is less than 100 |
| `<=` | Less than or equal | `attempts <= 3` → `True` if attempts is 3 or fewer |
| `>` | Greater than | `balance > 0` → `True` if balance is positive |
| `>=` | Greater than or equal | `age >= 18` → `True` if age is 18 or older |

Each produces a Boolean: `True` or `False`.

```python
age = 25
print(age == 25)    # True
print(age > 30)     # False
print(age != 25)    # False

score = 85
grade = score >= 90
print(grade)        # False (score is not A-level)

password = "secret123"
is_valid = password != ""
print(is_valid)     # True
```

> **Prompt for Claude.** "Write a Python function `check_grade(score)` that takes an integer score and returns a Boolean: `True` if the score is 80 or higher, `False` otherwise. Test it with three inputs: 75, 80, and 95."

> **Tests to validate.**
> - `check_grade(75) == False`
> - `check_grade(80) == True`
> - `check_grade(95) == True`

### The most common bug: = vs ==

Assignment (`=`) and comparison (`==`) look similar. They are not the same.

```python
# WRONG: uses = instead of ==
if age = 18:
    print("Adult")
```

This produces a `SyntaxError` in Python 3.8+. But in older code or in a slightly different context, it might run and create a subtle bug:

```python
# WRONG but runs (modifies the variable!)
age = 25
if (age = 30):          # This tries to ASSIGN 30 to age, not compare
    print("Age is 30")
```

The rule: `=` assigns a value to a variable. `==` compares two values and produces a Boolean. In a condition, you want `==`.

```python
# RIGHT
age = 25
if age == 30:
    print("Age is 30")
else:
    print("Age is not 30")
```

This is so common that linters (code checkers) flag it automatically.

### Worked example — grading scale

A school assigns letter grades based on score ranges:

- 90–100: A
- 80–89: B
- 70–79: C
- 60–69: D
- Below 60: F

Write a snippet that reads a score and prints the corresponding letter grade.

```python
score = int(input("Enter score: "))

if score >= 90:
    grade = "A"
elif score >= 80:
    grade = "B"
elif score >= 70:
    grade = "C"
elif score >= 60:
    grade = "D"
else:
    grade = "F"

print(f"Grade: {grade}")
```

*How it works:* The chain of comparisons stops at the first true condition. If score is 85, the first check `score >= 90` is false, so we move to `elif score >= 80`, which is true. The grade becomes "B" and we skip the rest.

*Test with 75:* First check false, second false, third true (75 >= 70). Grade is "C". Correct.

*Test with 55:* All comparisons false. The `else` catches it. Grade is "F". Correct.

> **Prompt for Claude.** "Write a Python program that reads an integer score and assigns a letter grade: A for 90+, B for 80-89, C for 70-79, D for 60-69, F below 60. Use if/elif/else. Test with scores 95, 85, 75, 65, and 55."

> **Tests to validate.**
> - Score 95 produces 'A', score 85 produces 'B', score 75 produces 'C'.
> - Score 65 produces 'D', score 55 produces 'F'.
> - Edge case: 90 produces 'A', 89 produces 'B'.

### Common misconceptions

**Truthiness is a convenience, not a substitute for clarity.** Yes, `if x:` works when x is a number or string. But `if x > 0:` or `if len(x) > 0:` is often clearer because it says what you're checking.

**Comparison chains work, but precedence matters.** `10 < score < 100` is valid Python and means "is score between 10 and 100?" But if you write it wrong — `score < 100 < 10` — it's always false because 100 is not less than 10. Read it left to right.

---

## Concept 2 — Conditionals: if, elif, else

An `if` statement runs a block of code only if a condition is true. An `elif` ("else if") checks a new condition if the prior one was false. An `else` runs if all prior conditions were false. Together, they enforce one cardinal rule: *exactly one branch executes*.

### The if statement

```python
age = int(input("Enter your age: "))

if age >= 18:
    print("You are an adult.")
    print("You can vote.")
```

If age is 18 or older, both print statements run. If age is less than 18, neither runs.

Notice the colon (`:`) after the condition and the **indentation** of the block. Indentation is syntax in Python, not just style. It tells Python which statements belong to the `if` block. By convention, four spaces per indentation level (never tabs in professional code).

If you forget the indentation or the colon, Python raises an error:

```python
# IndentationError
if age >= 18:
print("Adult")   # ERROR: not indented
```

### The if-else statement

An `else` block runs when the `if` condition is false:

```python
temperature = int(input("Temperature in Celsius: "))

if temperature < 0:
    print("Below freezing. Stay warm.")
else:
    print("Above freezing.")
```

One path always runs. Exactly one.

### The if-elif-else chain

When you have multiple conditions, `elif` chains them:

```python
hour = int(input("Hour (0-23): "))

if hour < 6:
    greeting = "Good night."
elif hour < 12:
    greeting = "Good morning."
elif hour < 18:
    greeting = "Good afternoon."
else:
    greeting = "Good evening."

print(greeting)
```

The chain evaluates top to bottom and stops at the first true condition. If hour is 10, the first check is false, the second is true, and we get "Good morning." The remaining elifs and the else are skipped.

*Why not three separate if statements?* Because separate ifs would all run if multiple conditions were true. If hour is 10, the first `if hour < 6:` is false, but the second `if hour < 12:` is true and the third `if hour < 18:` is also true. You'd get two greetings. The chain guarantees only one.

> **Prompt for Claude.** "Write a Python program that reads an integer hour (0-23) and prints a greeting based on time of day: 'Good night' (0-5), 'Good morning' (6-11), 'Good afternoon' (12-17), 'Good evening' (18-23). Use if/elif/else."

> **Tests to validate.**
> - Hour 3 produces 'Good night', hour 10 produces 'Good morning'.
> - Hour 14 produces 'Good afternoon', hour 22 produces 'Good evening'.
> - Boundary: hour 6 produces 'Good morning', hour 18 produces 'Good evening'.

### Trade-off: chains vs. nesting

Sometimes a condition depends on another condition. You can handle this two ways:

**Approach 1: Chain with and**

```python
username = input("Username: ")
password = input("Password: ")

if username == "admin" and password == "secret":
    print("Access granted.")
else:
    print("Access denied.")
```

**Approach 2: Nest the checks**

```python
username = input("Username: ")
password = input("Password: ")

if username == "admin":
    if password == "secret":
        print("Access granted.")
    else:
        print("Wrong password.")
else:
    print("Unknown user.")
```

The chain is shorter. The nested version gives more specific feedback. For a two-condition case, chain wins. For complex branching (different actions per game state and player count), nesting wins because you want to short-circuit early checks.

### Worked example — ATM withdrawal

An ATM checks three things: Is the account valid? Does the account have enough balance? Is the withdrawal amount valid (positive and not over the daily limit)?

```python
account = int(input("Account ID: "))
balance = int(input("Current balance: "))
amount = int(input("Withdrawal amount: "))

DAILY_LIMIT = 500

if account < 1000000 or account > 9999999:
    print("Invalid account number.")
elif amount <= 0:
    print("Amount must be positive.")
elif amount > balance:
    print("Insufficient funds.")
elif amount > DAILY_LIMIT:
    print(f"Daily limit is ${DAILY_LIMIT}.")
else:
    balance -= amount
    print(f"Withdrawal approved. New balance: ${balance}")
```

Each condition checks one rule. The chain stops at the first failure, ensuring exactly one error message or one success path.

> **Prompt for Claude.** "Write a Python program simulating an ATM. Read account ID (validate 7 digits), balance, and withdrawal amount. Check in order: valid account, positive amount, sufficient funds, daily limit ($500). Print appropriate message for each failure case or 'Approved' with new balance if all checks pass."

> **Tests to validate.**
> - Invalid account (too short) prints "Invalid account."
> - Amount <= 0 prints "Amount must be positive."
> - Amount > balance prints "Insufficient funds."
> - Amount > 500 prints "Daily limit."
> - Valid withdrawal prints "Approved" with correct new balance.

### Common misconceptions

**elif is not the same as else if:** Wait, it is. `elif` is just Python's syntax. But the key point: elif only runs if all prior conditions were false. If your first if was true, the elif is skipped entirely.

**Indentation is not optional:** Many beginners come from languages where indentation is style. In Python, it's syntax. Mismatched indentation is a syntax error.

---

## Concept 3 — Boolean operators: and, or, not

The gate agent's checks were connected by invisible ANDs: "Is the ID valid AND is the boarding pass present AND does today's date match AND are there no security flags?" All four had to be true.

Boolean operators (`and`, `or`, `not`) combine conditions. They produce Booleans and obey logical rules.

### The and operator

`and` returns `True` only if both conditions are true:

```python
age = int(input("Age: "))
has_license = input("Have driver's license? (yes/no): ") == "yes"

if age >= 18 and has_license:
    print("You can drive.")
else:
    print("You cannot drive.")
```

Test: age=25, has_license=True → both true → "You can drive."
Test: age=16, has_license=True → first false → "You cannot drive."

Truth table for `and`:

| p | q | p and q |
|---|---|---------|
| T | T | T |
| T | F | F |
| F | T | F |
| F | F | F |

### The or operator

`or` returns `True` if at least one condition is true:

```python
is_member = input("Are you a member? ") == "yes"
total_spent = int(input("Total spent today: "))

if is_member or total_spent >= 100:
    discount = 0.1
else:
    discount = 0.0

print(f"Discount: {discount * 100}%")
```

Test: is_member=True, total_spent=50 → at least one true → 10% discount.
Test: is_member=False, total_spent=150 → at least one true → 10% discount.
Test: is_member=False, total_spent=50 → both false → 0% discount.

Truth table for `or`:

| p | q | p or q |
|---|---|--------|
| T | T | T |
| T | F | T |
| F | T | T |
| F | F | F |

### The not operator

`not` flips the Boolean:

```python
is_expired = True
if not is_expired:
    print("License is valid.")
else:
    print("License expired.")
```

Truth table for `not`:

| p | not p |
|---|-------|
| T | F |
| F | T |

`not` is useful for readability. `if not is_empty:` is often clearer than `if len(x) > 0:`.

### Short-circuit evaluation

Python evaluates `and` and `or` from left to right and stops as soon as it knows the answer. This is called *short-circuit evaluation*.

For `and`: If the left side is false, the right side is not evaluated. Why? Because if the left is false, the whole expression is false regardless of the right.

```python
count = 0
if count > 0 and 10 / count > 2:
    print("OK")
```

The first condition `count > 0` is false. Python never evaluates `10 / count`, so no division-by-zero error. If count were 5, both conditions would be evaluated.

For `or`: If the left side is true, the right side is not evaluated.

```python
is_admin = True
if is_admin or expensive_permission_check():
    print("Access granted")
```

If is_admin is true, the expensive function is never called. This is a performance optimization that sometimes matters.

### Operator precedence and parentheses

Boolean operators have lower precedence than comparison operators. This usually works out right:

```python
if age >= 18 and height > 60:  # Evaluates as (age >= 18) and (height > 60)
```

But mixed conditions can surprise you. Always use parentheses for clarity:

```python
# Ambiguous without parentheses
if age < 18 or age > 65 and income < 30000:

# Clear with parentheses
if (age < 18) or (age > 65 and income < 30000):
```

(By precedence rules, the first means "under 18 OR (over 65 AND poor)", which is probably what you meant, but make it obvious.)

### Worked example — tax bracket calculator

A country has tax brackets. Income over a threshold is taxed at a higher rate. Write a program that reads income and marital status and prints the tax owed.

```python
income = int(input("Annual income: "))
status = input("Marital status (single/married): ")

SINGLE_STANDARD = 12000
MARRIED_STANDARD = 24000
SINGLE_RATE = 0.10
MARRIED_RATE = 0.08

if status == "single":
    if income <= SINGLE_STANDARD:
        tax = 0
    else:
        taxable = income - SINGLE_STANDARD
        tax = taxable * SINGLE_RATE
elif status == "married":
    if income <= MARRIED_STANDARD:
        tax = 0
    else:
        taxable = income - MARRIED_STANDARD
        tax = taxable * MARRIED_RATE
else:
    print("Unknown status.")
    tax = -1

if tax >= 0:
    print(f"Tax owed: ${tax:.2f}")
```

This uses nested conditions: first, determine marital status; then, within each status, check income against the threshold.

> **Prompt for Claude.** "Write a Python program that reads annual income and marital status ('single' or 'married'). Calculate tax: single filers deduct $12,000 and pay 10% on the remainder; married filers deduct $24,000 and pay 8%. Print tax owed to two decimal places. Use nested if statements."

> **Tests to validate.**
> - Single, income $15,000: taxable = $3,000, tax = $300.
> - Married, income $50,000: taxable = $26,000, tax = $2,080.
> - Single, income $12,000: no tax.
> - Married, income $24,000: no tax.

### Common misconceptions

**De Morgan's laws (from set theory, Chapter 1) apply here too.** `not (A and B)` is the same as `(not A) or (not B)`. `not (A or B)` is the same as `(not A) and (not B)`. If you find yourself writing `not (x > 5 and y < 10)`, rewrite it as `(x <= 5) or (y >= 10)` for clarity.

**Short-circuit evaluation is a feature, not a bug.** It's intentional and fast. But if your right-side condition has side effects (prints, modifies a variable), be aware it might not run.

---

## Integration — The thermostat, refined

Open the chapter again: a thermostat reads the room temperature and the setpoint (desired temperature) and decides whether to turn on the furnace or air conditioner.

```python
room_temp = float(input("Room temperature (°C): "))
setpoint = float(input("Desired temperature (°C): "))

HYSTERESIS = 0.5  # Prevent constant on-off cycling

if room_temp < setpoint - HYSTERESIS:
    action = "Heat"
elif room_temp > setpoint + HYSTERESIS:
    action = "Cool"
else:
    action = "Off"

print(f"Furnace: {action}")
```

The thermostat uses a decision chain with a clever detail: *hysteresis*. Instead of turning the heat on when room_temp equals setpoint and off when it overshoots by 0.001 degrees (which would cause rapid cycling), it creates a dead band. Heat turns on only when the room is 0.5 degrees below the setpoint and turns off only when it's 0.5 degrees above. This simple Boolean logic prevents wear on the equipment.

Now expand it. A smart thermostat also checks the time of day (occupants prefer cooler at night):

```python
room_temp = float(input("Room temperature (°C): "))
setpoint = float(input("Desired temperature (°C): "))
hour = int(input("Hour (0-23): "))

HYSTERESIS = 0.5
NIGHT_REDUCTION = 2  # Cooler at night

if hour >= 22 or hour < 6:  # Between 10 PM and 6 AM
    setpoint -= NIGHT_REDUCTION

if room_temp < setpoint - HYSTERESIS:
    action = "Heat"
elif room_temp > setpoint + HYSTERESIS:
    action = "Cool"
else:
    action = "Off"

print(f"Setpoint: {setpoint}°C, Furnace: {action}")
```

The logic is still simple: test the hour, adjust the setpoint, then apply the hysteresis decision. Each condition answers one question. The chain is clear.

This pattern — test, adjust, decide — is how real systems work, from smart homes to airplane autopilots.

---

## Exercises

### Warm-up

**Exercise 4.1** — *Truthiness.* For each of the following, predict the Boolean value without running code. Then run it and check.

```python
print(bool(0))
print(bool(""))
print(bool([]))
print(bool(3.14))
print(bool("0"))
```

**Exercise 4.2** — *Comparison.* Write a snippet that reads two integers, a and b, and prints which is larger (or if they're equal).

**Exercise 4.3** — *The = vs == trap.* Explain in one sentence why `if x = 5:` is wrong and `if x == 5:` is right.

### Application

**Exercise 4.4** — *Grading.* Write a program that reads a test score (0-100) and prints the letter grade:
- A: 90–100
- B: 80–89
- C: 70–79
- D: 60–69
- F: < 60

**Exercise 4.5** — *Login check.* Write a program that reads a username and password. Grant access only if username is "admin" and password is "secret". Otherwise, print an error.

**Exercise 4.6** — *Discount eligibility.* Write a program that reads age and membership status ("yes" or "no"). A customer gets a discount if they are under 18 OR a member. Print whether they get the discount.

### Synthesis

**Exercise 4.7** — *Age validation.* Write a program that reads a birth year (assume current year is 2026). Calculate age. Check: (1) age is between 0 and 150, (2) the person is at least 18. Print appropriate messages for each failure case and "You are of legal age" if both pass.

**Exercise 4.8** — *Multi-condition ATM.* Expand the ATM example. Read account ID, balance, and withdrawal amount. Check in order: valid account ID (8 digits), positive amount, sufficient balance, and daily limit ($500). Print a specific error message for the first failure, or "Approved. New balance: $X" on success.

**Exercise 4.9** — *Restaurant seating.* A restaurant seats customers based on party size and time. Read party size and hour of day (0-23).
- If party size > 6 and (it's 6-8 PM or 12-1 PM): "Wait 30 minutes, peak hours."
- Else if party size > 6: "Wait 10 minutes."
- Else: "Seat immediately."

Use nested if or and/or operators. Print the result.

### Challenge

**Exercise 4.10** — *Leap year.* A year is a leap year if: (1) divisible by 400, OR (2) divisible by 4 AND not divisible by 100. Write a program that reads a year and prints whether it's a leap year. Test with 2000 (leap), 1900 (not leap), 2024 (leap), 2026 (not leap).

**Exercise 4.11** — *State machine.* A traffic light cycles: red → green → yellow → red. Write a program that reads the current light color and prints the next. Use if/elif/else. What happens if the input is not a valid color? Handle it.

---

## Claude Code

The exercises above teach decision-making by hand. But in practice, you'll write decisions that live inside larger programs — functions that validate input, systems that grade assignments, services that process transactions. Claude Code is a way to write and test those decisions quickly, iteratively, without breaking your flow.

The prompt below asks Claude Code to build a grading system that reinforces three things you just learned: if/elif/else chains, comparison operators, and truthiness in combination with Boolean operators.

> **Claude Code prompt.**
>
> "Write a Python module `grading.py` with a function `letter_grade(score)` that converts a numeric score (0–100) to a letter grade with +/– modifiers. Rules: 93+ is A, 90–92 is A–, 87–89 is B+, 80–86 is B, 77–79 is B–, 73–76 is C+, 70–72 is C, 67–69 is C–, 60–66 is D, below 60 is F. Scores below 0 or above 100 are invalid.
>
> Then write a test file `test_grading.py` with pytest. Include 8 test cases: three boundary cases (89.999 should be B+, 90.0 should be A–, 90.001 should be A–), two invalid cases (–5 and 105), and three mid-range cases (75, 85, 95).
>
> Save both files and run `pytest test_grading.py -v` to show all tests passing."

**What to expect.** Claude Code will build a grading function using if/elif/else chains to test numeric boundaries. You'll see comparison operators in action (`score >= 93`, `score < 90`), the importance of order (a score of 90 must hit the A– case before it checks B+), and how pytest's test names make your intent clear. When you run the tests, each boundary case will pass, confirming that your logic handles the exact dividing lines correctly.

**Stretch.** Ask Claude Code to add a function `batch_grade(scores_list)` that accepts a list of scores and returns a list of letter grades. This reinforces truthiness (how to handle an empty list?), iteration (you'll loop), and conditional logic (each score needs the letter_grade function). Then add a test case that grades a list of ten random scores and prints a summary: "5 A's, 2 B's, 2 C's, 1 F".

---

## Chapter summary

You can now do five things you could not do before.

You can write conditions — expressions that evaluate to true or false — using comparison operators and Boolean values.

You can chain multiple conditions together with `and`, `or`, and `not` to express complex logic, and you understand that Python short-circuits, stopping as soon as it knows the answer.

You can write `if`, `elif`, and `else` blocks that take different paths through your code, and you know that exactly one path runs (or none, if there's no else).

You can identify and fix the most common beginner mistake: confusing assignment (`=`) with comparison (`==`).

You can read and refactor tangled decision logic, choosing between chaining conditions and nesting blocks based on clarity and the need to short-circuit or give specific error messages.

What you should now be able to teach a friend: why `if x:` works even when x is not explicitly a Boolean, how to read a chain of elif statements, and why `not (A and B)` is the same as `(not A) or (not B)`.

---

## Connections forward

Chapter 5 teaches loops: while and for. A loop is a decision repeated. The condition that decides whether to continue looping is a Boolean expression, built from the same operators you just learned.

Chapter 6 teaches functions. A function often returns a Boolean (is this valid? does this exist?), and the caller uses that Boolean to decide what to do next.

Later chapters use decisions everywhere: Chapter 9 (Lists) will iterate through a list and make decisions about each element. Chapter 10 (Dictionaries) will check whether a key is present. Chapter 11 (Classes) will use decisions to validate object state. By the end of the book, decisions are invisible — they're just the structure of clear thinking.

The gate agent is making millions of decisions per second. So is your code now.

---

## Research notes & metadata

**Sources consulted:**
- OpenStax *Introduction to Python Programming* modules m00027–m00035 (Boolean, comparison, if/elif/else, logical operators, precedence, ternary).
- Voice anchor: *Contemporary Mathematics* Chapter 1 (cold-open in scene, mechanism-first, trade-offs named, worked examples with check-against-intuition).
- Python 3.11 documentation: operator precedence, truthiness rules, style guide (PEP 8).

**Concept focus:**
1. Boolean values and truthiness (conversion rules, = vs ==).
2. Conditionals and chaining (if/elif/else, exactly-one-path guarantee, nesting vs chaining).
3. Logical operators and short-circuit evaluation (and, or, not, De Morgan implications).

**Mechanisms explained:**
- How comparison produces a Boolean.
- How truthiness converts any value to True/False.
- Why if/elif/else chains stop at the first true condition.
- Short-circuit evaluation and its performance implication.

**Claude patterns:** 6 substantive code triples (Boolean conversion, comparison grading, time-of-day greeting, ATM withdrawal, tax bracket, thermostat logic).

**Key trade-offs:**
- Truthiness convenience vs explicit comparison clarity.
- Chained conditions (short, runs once) vs nested blocks (specific error paths).
- Parentheses for machine readability vs precedence rules.

**What would change my mind:**
Evidence that students find the cold-open (TSA gate) confusing rather than clarifying would prompt a reframe. If a grounded example (tax brackets, ATM, thermostat) consistently misleads, it should be replaced with a simpler case.

**Still puzzling:**
The boundary between "use truthiness" and "be explicit" is context-dependent and not fully formalized. Code reviews often debate this. Chapter notes that both appear in real code and flag clarity as the tiebreaker, but this is an open judgment call, not a rule.

**Tags:** #decisions, #conditionals, #booleans, #control-flow, #python-fundamentals, #if-elif-else, #logical-operators, #short-circuit-evaluation, #beginner-errors, #code-clarity
---

## LLM Exercise — Chapter 04: Decisions (Text Adventure Project)

**Project:** Text Adventure.
**What you're building this chapter:** the first branching decision — the player chooses where to go.
**Tool:** **Claude Code**.

---

**The Prompt:**

```
Chapter 4 of my Text Adventure project. Chapter 4 covered: if /
elif / else; comparison operators (==, !=, <, >, <=, >=); Boolean
operators (and, or, not); truthiness; the dangling-else trap.

Add the first branching choice.

1. **The opening room.** Print a description: the player is in
   [a starting location appropriate to your game — a tavern, a
   spaceship docking bay, a foggy clearing, etc.]. Describe
   what's visible. List 3-4 directional choices ("You can go
   NORTH, SOUTH, EAST, or WEST.").

2. **The branching logic.** Get the player's input. Use if/elif/
   else to:
   - Normalize the input (lowercase, strip — from Ch 3).
   - Branch on the direction.
   - For each direction, print a short description of where
     they end up.
   - For invalid input, print a friendly "I don't understand"
     message.

3. **Add a stat-based check.** Within one of the directions,
   put a check: if the player has enough gold (say, > 25), they
   can pay a guard for safe passage; otherwise they have to
   sneak. Use `and` / `or` if you want to combine conditions
   (e.g., they need gold AND a torch — but they don't have a
   torch yet, so they must sneak).

4. **The "game over?" check.** Add a final block: if a specific
   choice leads to the player's death (the spider room collapses,
   the alien turret fires), set `game_over = True` and print
   a death message. Otherwise leave game_over = False. (The
   actual game loop comes in Ch 5.)

In addition to the code, produce the prompt + tests. Tests
should:
- Run the game with each direction; verify the right description
  prints.
- Run with invalid input; verify the error message.
- Run with sufficient and insufficient gold; verify the right
  branch.

End with: identify one decision in your game that has more than
one possible "right" answer. (E.g., either fighting OR sneaking
works.) Multiple-path design is what makes adventure games
replayable.
```

---

**What this produces:** The first branching decision in the game. Tests verify each branch.

**Connection to previous chapters:** Ch 1's input + Ch 2's variables + Ch 3's normalization + Ch 4's branching is the first real interactive code.

**Preview of next chapter:** Chapter 5 covers loops. You'll wrap the whole game in a `while not game_over` loop so the player can take multiple actions, not just one.


---

##  AI Wayback Machine
**Edsger Dijkstra** was argued in 1968 that "goto" considered harmful — and laid the groundwork for structured decision flow in modern programming.

**Run this:**

```
Who is Edsger Dijkstra, and how does their work connect to decisions we covered in this chapter? Keep it to three paragraphs. End with the single most surprising thing about their career or ideas.
```

→ Search **"Edsger Dijkstra"** on Wikipedia.

**Now make the prompt better.** Try one of these:

- Ask it to apply Edsger Dijkstra's ideas to a small Python program you'd write.
- Add a constraint: "Answer including criticisms or limits of Edsger Dijkstra's framework."

What changes? What gets better? What gets worse?
