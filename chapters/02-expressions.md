# Chapter 2 — Expressions

## Three title options

1. How expressions press out values — operators, types, and the silent work of arithmetic
2. Expressions: getting a calculator to do your thinking
3. The machinery of expressions — operators that work, types that change, and why `7 / 4` is not 1

---

## TL;DR

An expression is a piece of code that evaluates to a single value. Python expressions combine literals, variables, and operators following rules of precedence — which operators execute first — and type conversion — what happens when integers mix with floats, or strings mix with numbers. You'll learn to write expressions in a Python shell, recognize when type conversions help and when they hide bugs, and understand why division on a calculator is not the same division as in Python.

---

## Opening — the REPL and a puzzle

You sit down at a Python shell — a program that lets you type Python one line at a time and see the result immediately. REPL stands for "read-eval-print loop." Read your code. Evaluate it. Print the answer. Then loop back and let you try something else.

You type:

```python
2 ** 10
```

The interpreter evaluates it. You see:

```
1024
```

You type the same thing again. Same answer. You type a variation:

```python
2.0 ** 10
```

You see:

```
1024.0
```

A 2 just became a 2.0. The answer just became 1024.0 instead of 1024. Nothing about the calculation changed — two to the tenth power is still 1024 — but the *form* of the answer did. And you're wondering: why?

That question — what decides whether a Python answer looks like 1024 or 1024.0 — is the machinery of this chapter. Because once you understand that, you understand how to control what Python gives you back. And once you can control what Python gives you, you can write programs that don't surprise you.

### Learning objectives

By the end of this chapter you will be able to:

- **Evaluate** expressions involving operators, parentheses, and the rules of precedence, predicting the outcome before the interpreter runs.
- **Identify** when Python converts types automatically (implicitly) and when you need to convert manually (explicitly) using `int()`, `float()`, and `str()`.
- **Apply** the three division operators (`/`, `//`, `%`) to solve problems involving quotients, remainders, and unit conversion.
- **Predict** which data type emerges when integers mix with floats, when strings mix with numbers, and when operations overflow or lose precision.
- **Use** a Python shell interactively to experiment, correct mistakes with the arrow keys, and troubleshoot expressions before putting them in a program.

### Prerequisites

You are comfortable with high school algebra: plugging numbers into formulas, solving for a variable. You can open a terminal or Python shell on your computer. You understand from Chapter 1 that a variable holds a value, that `=` is assignment, and that `print()` puts text on the screen.

### Why this chapter matters

Every program you write will be built from expressions. A program reads in data (expressions), calculates (expressions), and outputs results (expressions). You cannot write even a simple calculator program without understanding how expressions evaluate. And you cannot debug when things go wrong without knowing why Python's arithmetic did not do what you expected. This chapter is the invisible foundation under everything that follows.

---

## Concept 1 — What an expression is, and how operators decide the outcome

### Cold open: the rule that wasn't there

You're in a spreadsheet. You have a column of prices. You have a column of tax rates. You write a formula in the total column:

```
price + tax * rate
```

The spreadsheet calculates. Your friend leans over and says, "Wait — did it multiply first or add first?" You're not sure you know. You calculated the same formula in your head a moment ago and got a different answer.

The spreadsheet didn't ask. It just calculated using a rule that has been agreed on since before you were born. Multiplication before addition. Parentheses before everything. That rule has a name: **operator precedence** — from Latin *operari*, to work, and *precedere*, to go before.

An **expression**, from Latin *expressus*, meaning pressed out, is a piece of code that, when evaluated, presses out a single value. The expression `7 * (3 + 2)` presses out the value 35. The expression `2 ** 10` presses out 1024.

### The operators: what they do

Python has operators for arithmetic:

| Operator | Name | Example | Result |
|---|---|---|---|
| `+` | Addition | `3 + 5` | `8` |
| `-` | Subtraction | `10 - 3` | `7` |
| `*` | Multiplication | `4 * 6` | `24` |
| `/` | Division (true) | `7 / 2` | `3.5` |
| `//` | Division (floor) | `7 // 2` | `3` |
| `%` | Modulo (remainder) | `7 % 2` | `1` |
| `**` | Exponentiation | `2 ** 3` | `8` |

Each operator does work: `+` adds, `-` subtracts, `*` multiplies. But `/` is the first surprise. In Python, `/` always gives you a float back, even when the division comes out even.

```python
>>> 10 / 2
5.0
```

That `5.0` — not `5` — is the clue that something changed types. We'll come back to that.

### Precedence: the invisible ruler

When you write an expression like `3 + 4 * 5`, Python doesn't evaluate left to right. It doesn't evaluate right to left either. It uses precedence: a hierarchy that says which operators eat first.

The hierarchy, from highest to lowest precedence:

1. Parentheses: `()`
2. Exponentiation: `**`
3. Multiplication, division (all kinds), modulo: `*`, `/`, `//`, `%`
4. Addition, subtraction: `+`, `-`

Operators with the same precedence evaluate left to right (except `**`, which is right to left, but that's a detail you rarely need).

So `3 + 4 * 5`:
- First: `4 * 5` evaluates to `20` (multiplication before addition)
- Then: `3 + 20` evaluates to `23`

If you wanted addition first, you'd write `(3 + 4) * 5`, which is `7 * 5 = 35`.

### The trade-off: implicit precedence versus explicit parens

Python's precedence rules match algebra. If you learned algebra in school, you already know them. That's a win. You don't have to learn a new hierarchy.

But the cost is this: if you don't remember the rule, the code silently does the wrong calculation. There's no error message. Just a wrong answer.

This is why good programmers use parentheses even when they're not required. `(3 + 4) * 5` takes one extra second to type but makes the intent visible. `3 + 4 * 5` makes you trust your memory.

### Worked example — predicting precedence

Evaluate `2 + 3 * 4 - 5 ** 2` without a calculator.

Step 1: Find the operator with highest precedence. Exponentiation goes first.
- `5 ** 2 = 25`
- Expression is now: `2 + 3 * 4 - 25`

Step 2: Next precedence level: multiplication.
- `3 * 4 = 12`
- Expression is now: `2 + 12 - 25`

Step 3: Addition and subtraction, left to right.
- `2 + 12 = 14`
- `14 - 25 = -11`

Answer: `-11`

Check with Python:

```python
>>> 2 + 3 * 4 - 5 ** 2
-11
```

✓

### Common misconceptions

**Left-to-right is not how it works.** Students often assume `3 + 4 * 5` means "add 3 and 4, then multiply by 5" because that's the order they read. It does not. Precedence decides, not reading order.

**Parentheses are not always about math.** When you write `print(x + y)`, the parentheses are syntax — they're how `print()` knows what to print. When you write `(x + y) * z`, the parentheses are arithmetic. Both use the same symbol. Don't confuse them.

**The rules are the same as algebra.** If you learned math, you already know Python's operator precedence. Trust it.

---

## Concept 2 — Types and the silent conversions that surprise you

### Cold open: a string that looked like a number

You write a program that asks someone for their age:

```python
age = input("How old are you? ")
```

You type `25`. The variable `age` now holds `"25"` — a string, because `input()` always reads as text.

You write the next line:

```python
years_until_retirement = 65 - age
```

You run it. You get an error:

```
TypeError: unsupported operand type(s) for -: 'int' and 'str'
```

What happened? `65` is an integer. `age` is a string that looks like a number but isn't. Python will not subtract a string from an integer. It won't guess. It stops and tells you the mistake.

A **type** is a category of value. Python has integers (`int`), floating-point numbers (`float`), strings (`str`), and others. Each type has rules about what you can do with it.

### Implicit type conversion: when Python helps

Sometimes Python converts types automatically. Add an integer and a float:

```python
>>> 3 + 2.5
5.5
```

The `3` is an integer. The `2.5` is a float. Python converts the `3` to `3.0` and adds them. The result is a float.

Why? Because a float is more precise than an integer. Integer arithmetic loses the fractional part. Float arithmetic keeps it. When you mix them, Python promotes the integer to a float so nothing is lost.

But this convenience has a cost: it's happening behind the scenes. You don't see the conversion. If you're not thinking about types, you might be surprised.

```python
>>> 1 / 2
0.5
>>> 1 // 2
0
```

Same numbers, different operators. `/` is true division and always returns a float. `//` is floor division and returns an integer. If you accidentally use `//` when you meant `/`, your answer will be silently rounded down.

### Explicit type conversion: when you take control

When implicit conversion doesn't happen (like integer minus string), you convert manually. Python gives you three functions:

- `int(x)` converts to an integer. If `x` is a float, the fractional part is cut off (not rounded).
- `float(x)` converts to a float. An integer `5` becomes `5.0`.
- `str(x)` converts to a string. An integer `42` becomes `"42"`.

Back to the age problem:

```python
age_str = input("How old are you? ")
age = int(age_str)
years_until_retirement = 65 - age
```

Now `age` is an integer. The subtraction works.

Or in one line:

```python
age = int(input("How old are you? "))
```

### The trade-off: convenience versus control

Implicit conversion is fast. You write less code. For common cases like mixing integers and floats, it does the right thing.

But implicit conversion is also silent. If you're not paying attention, the type changes happen without you noticing. And sometimes the "right thing" is not what you wanted. You meant true division but got floor division. You meant to concatenate strings but got a type error instead.

Explicit conversion is verbose. You write `int()` and `float()` and `str()` every time. But the code is honest. Anyone reading it sees exactly where a type change happens. And you can't be surprised by a conversion you wrote yourself.

### Worked example — reading and converting user input

You're writing a program that reads a person's height in centimeters and converts it to meters and remaining centimeters.

```python
height_cm = int(input("Height in centimeters: "))
meters = height_cm // 100
remaining_cm = height_cm % 100
print(f"{meters}m {remaining_cm}cm")
```

Input: `193`
Output: `1m 93cm`

Notice three things:
1. `input()` reads as a string. We immediately convert with `int()`.
2. `//` gives us the integer part (how many complete meters).
3. `%` gives us the remainder (leftover centimeters).

Together, these two operators — floor division and modulo — split one number into a quotient and a remainder.

### Common misconceptions

**int() rounds, but it doesn't.** `int(5.9)` is `5`, not `6`. The fractional part is cut off (truncated), not rounded. Use `round()` if you want rounding.

**String concatenation is not addition.** `"5" + 5` fails. You must convert explicitly: `"5" + str(5)` gives `"55"`, but `int("5") + 5` gives `10`. Different operations, different results.

**Type errors are not bugs; they're the system protecting you.** When Python says "can't add a string and an integer," it's not being difficult. It's refusing to guess what you meant.

---

## Concept 3 — Division: three operators, three different meanings

### Cold open: why 7 / 4 is not 1

In a calculus class in 1987, a student writes code on a computer.

```python
result = 7 / 4
```

She expects `1` — the integer answer, the quotient without the remainder.

She gets `1.75`.

"That's wrong," she says. "Seven divided by four is one."

The computer didn't make a mistake. She and the computer disagree about what "division" means. In her elementary school, division gave a quotient and a remainder. Seven divided by four is one with remainder three. In Python (and in algebra), division gives an exact answer in decimal form: 1.75.

Python offers three operators to cover all three meanings:

- `/` **true division**: gives the exact answer as a decimal. `7 / 4 = 1.75`.
- `//` **floor division**: gives the quotient, rounded down to the nearest integer. `7 // 4 = 1`.
- `%` **modulo**: gives the remainder. `7 % 4 = 3`.

They're often used in pairs. If you know how many complete groups fit in a total, that's floor division. If you know how much is left over, that's modulo.

### Integer division and the division pair

In many real programs, you don't care about the decimal answer. You care about "how many complete units?" and "how much is left?"

If you have 193 centimeters of height, how many meters is that? `193 // 100 = 1`. One complete meter.

How many centimeters left? `193 % 100 = 93`. Ninety-three centimeters remaining.

Together, floor division and modulo split a number: `193 = 1 * 100 + 93`.

In general: if `n = q * d + r` (where `q` is quotient, `r` is remainder, `d` is divisor), then:
- `q = n // d`
- `r = n % d`

### The trade-off: decimal precision versus integer simplicity

True division (`/`) is mathematically precise. It gives you the exact answer. For money calculations, measurements, and physics, precision matters.

But for counting — "how many complete groups?" — integer division is clearer. There's no decimal point to confuse things. And for a computer, integers are exact. Floats can have rounding errors (we'll meet those soon).

### Worked example — a clock calculation

You have 340 minutes of driving time ahead of you. You're leaving at 13:25 (1:25 PM). When do you arrive?

```python
current_hour = 13
current_minute = 25
trip_minutes = 340

# Convert current time to total minutes
total_minutes = current_hour * 60 + current_minute

# Add trip time
arrival_minutes = total_minutes + trip_minutes

# Convert back to hours and minutes
arrival_hour = arrival_minutes // 60
arrival_minute = arrival_minutes % 60

# Handle wraparound past midnight
arrival_hour = arrival_hour % 24

print(f"Arrival: {arrival_hour}:{arrival_minute:02d}")
```

Step by step:
- Current time: `13 * 60 + 25 = 805` minutes since midnight.
- After trip: `805 + 340 = 1145` minutes.
- Hours: `1145 // 60 = 19` (7 PM).
- Minutes: `1145 % 60 = 5`.
- Wraparound: `19 % 24 = 19` (still on the same day).
- Output: `Arrival: 19:05`

The modulo at the end handles the case where the arrival hour exceeds 23 (late evening or next day).

### Common misconceptions

**Division always gives a float in Python 3.** In older Python, `/` was floor division by default. Not anymore. `/` is always true division. This breaks old code. Use `//` if you want integer division.

**Modulo works with negative numbers, but not the way you think.** `7 % 4 = 3`, but `-7 % 4 = 1` (Python rounds toward negative infinity, not toward zero). For counting problems, this rarely matters. But don't be surprised if a calculation with negative numbers gives an unexpected remainder.

**The pair is always floor division and modulo.** If you use `//`, also use `%` at some point (or you should have used `/` instead). Mixing them tells you the calculation is complete.

---

## Integration — the shell, the operators, and the calculation

Return to the cold open: the Python shell. You're exploring how types work, and you've written several expressions.

```python
>>> 2 ** 10
1024
>>> 2.0 ** 10
1024.0
>>> 7 / 4
1.75
>>> 7 // 4
1
>>> 7 % 4
3
>>> "5" + 5
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: can only concatenate str (not "int") to str
>>> int("5") + 5
10
```

Now you understand the pattern. Each line demonstrates one principle:

1. **Precedence and exponentiation.** The `**` operator has the highest precedence. `2 ** 10` evaluates first.

2. **Type conversion through operators.** Using `/` instead of `//` converts the result to a float. The integer `2` became `2.0` in the exponent, so the result became `1024.0`.

3. **Division operators.** True division (`/`), floor division (`//`), and modulo (`%`) answer three different questions: "What's the exact answer?", "How many complete units?", and "What's left over?"

4. **Type errors as a feature.** You can't add a string and an integer. Python stops and tells you why. But once you convert the string to an integer, the addition works.

The shell is your laboratory. You type an expression, hit Enter, see what happens, and adjust. This is how you learn to predict what Python will do.

---

## Exercises

### Warm-up

**Exercise 2.1** *(Evaluate expressions using precedence.)*

Without using Python, evaluate each expression. Write the steps as if you were hand-calculating.

(a) `2 + 3 * 4`
(b) `(2 + 3) * 4`
(c) `10 - 4 // 2`
(d) `2 ** 3 ** 2`
(e) `(2 ** 3) ** 2`

**Exercise 2.2** *(Type conversions.)*

For each expression, predict the type and value before evaluating. Then check in a Python shell.

(a) `int(5.9)`
(b) `float(5)`
(c) `str(5) + str(3)`
(d) `int("5") + int("3")`

**Exercise 2.3** *(Division operators.)*

For each expression, predict the result.

(a) `15 / 4`
(b) `15 // 4`
(c) `15 % 4`
(d) `15 // 4 * 4 + 15 % 4`

### Application

**Exercise 2.4** *(Unit conversion with floor division and modulo.)*

Write a program that reads a number of total seconds and converts it to hours, minutes, and seconds. For example:

Input: `3661`
Output: `1 hour 1 minute 1 second`

Hint: Use `//` to get complete hours and minutes. Use `%` to get the remainder.

**Exercise 2.5** *(Temperature conversion.)*

Write a program that reads a temperature in Fahrenheit and prints it in Celsius using the formula: `C = (F - 32) * 5/9`.

Test your program with:
- `F = 32` (should give 0)
- `F = 212` (should give 100)
- `F = 98.6` (should give about 37)

**Exercise 2.6** *(Type consistency.)*

The following program has a type mismatch. Identify where the problem occurs and fix it using explicit type conversion.

```python
num_str = input("Enter a number: ")
result = num_str + 10
print(result)
```

### Synthesis

**Exercise 2.7** *(Expense splitting.)*

Write a program that reads a restaurant bill amount and splits it among a group. The program should:

1. Read the total bill (as a float)
2. Read the number of people (as an integer)
3. Calculate the amount per person
4. Print the result with two decimal places

Test with a bill of $43.21 split among 3 people. The result should be $14.40 per person (rounded).

**Exercise 2.8** *(Precedence in a real formula.)*

The body mass index (BMI) is calculated as: `weight_kg / (height_m ** 2)`.

Write a program that reads weight in kilograms and height in meters, calculates BMI, and prints the result rounded to one decimal place.

Test with:
- Weight = 70 kg, Height = 1.75 m (BMI should be about 22.9)

### Challenge

**Exercise 2.9** *(Change-making algorithm.)*

Write a program that reads a change amount in dollars (as a float) and calculates how many dollars, quarters (25¢), dimes (10¢), nickels (5¢), and pennies (1¢) to dispense.

Hint: Convert to cents (an integer) first, then use `//` and `%` repeatedly to count each denomination.

Test with: $1.24 should give 1 dollar, 0 quarters, 2 dimes, 0 nickels, 4 pennies.

**Exercise 2.10** *(Precedence challenge.)*

Evaluate without Python:

`3 + 4 * 2 ** 3 / 2 - 5`

Write out all the steps. Then verify with Python.

---

## Chapter summary

You now understand the hidden rules of expressions. Operator precedence decides which calculations happen first — and parentheses let you override that decision and make your intent visible.

You understand types: integers, floats, and strings. You know that Python converts types automatically sometimes (mixing integers and floats), and that sometimes it refuses (trying to subtract a string from an integer). And you know how to take control with explicit conversion: `int()`, `float()`, `str()`.

You understand division. True division `/` gives a decimal answer. Floor division `//` gives the integer quotient. Modulo `%` gives the remainder. Together, floor division and modulo split a number completely: if you divide 193 by 100, you get 1 complete hundred and 93 left over. That's what every clock, every unit converter, and every change-making algorithm is built on.

And you've learned the Python shell — the REPL, where you type one line at a time and see the result immediately. When you're not sure what an expression will do, the shell is your test bench.

What you should now be able to teach a friend: why `3 + 4 * 5` is not the same as `(3 + 4) * 5`, why `/` and `//` give different answers, and why Python refuses to add a string and an integer.

---

## Connections forward

Chapter 3 will introduce you to objects and methods — the idea that values in Python are not just bare numbers and strings, but things that know how to do work. An integer, for instance, has methods you can call on it. A string can be asked for its length.

Then in Chapter 4 you will learn about decisions: `if` statements that let your program choose different paths based on what's true and what's false. Those decisions depend on comparisons: is `x > 5`? Did the user type `"yes"`? And comparison expressions produce a Boolean value — `True` or `False`. The precedence rules you learned here extend to Boolean operators too.

The REPL you learned to use in this chapter will be your tool for exploring every concept ahead. When you get stuck, open the shell and try the thing. See what Python says.

---

## Claude Code

This section shows how to use Claude Code to explore and test the concepts from this chapter: how operators combine, how types convert, and how division operators split a number into quotient and remainder.

> **Claude Code prompt.**
>
> "I have 347 minutes of driving ahead of me. I'm leaving at 14:35 (2:35 PM). Write a Python function that calculates my arrival time on a 24-hour clock, handling wraparound past midnight. The function should take three parameters: `hour` (integer 0–23), `minute` (integer 0–59), and `minutes_to_add` (integer, any size). Return a tuple `(arrival_hour, arrival_minute)`. Use floor division (`//`) to extract hours from total minutes. Use modulo (`%`) to extract remaining minutes and to handle wraparound past midnight. Show the calculation steps so I can follow the operator precedence and type conversions. Test with my example (14:35 + 347 minutes) and three others: 23:50 + 20 minutes (wraps to next day), 12:00 + 240 minutes (exactly 4 hours), and 0:30 + 1410 minutes (wraps across midnight)."

**What to expect.** Claude Code will write a function that demonstrates three core chapter concepts working together: operator precedence (in the arithmetic expression `hour * 60 + minute + minutes_to_add`), type consistency (integers throughout), and the division pair (`//` and `%`) splitting one number into quotient and remainder. The function will handle the edge case where adding minutes crosses midnight by using modulo on the final hour. Each test case shows a different operator behavior — exact hour boundaries, wraparound, and large minute values — so you see the machinery at work.

**Stretch.** Ask Claude Code to write a program that reads a restaurant bill (float), splits it among a group (integer), and prints the per-person cost. Then ask it to compare the output of `/` (true division) versus `//` (floor division) and explain why you'd choose one or the other for money calculations. This reinforces type conversion, operator choice, and the trade-off between decimal precision and integer simplicity.

---

What would change my mind: If I discovered a case where Python's operator precedence differs from mathematical convention, or a case where implicit type conversion causes data loss in a common operation (beyond the documented float precision issues), I would revise the chapter's confidence in those mechanisms.

Still puzzling: Why Python chose to make `/` always return a float, even `4 / 2 = 2.0`. The design decision makes sense (true division, mathematical correctness), but it breaks the pattern students learned in elementary school and creates a minor cognitive friction. I haven't yet found the most elegant way to teach why this choice was made.

---

Tags: operators, precedence, type-conversion, expressions, division, modulo, Python-shell, REPL, casting
---

## LLM Exercise — Chapter 02: Expressions (Text Adventure Project)

**Project:** Text Adventure.
**What you're building this chapter:** the player's starting stats — health, gold, experience — with arithmetic operations.
**Tool:** **Claude Code**.

---

**The Prompt:**

```
Chapter 2 of my Text Adventure project. Chapter 2 covered:
operators (+, -, *, /, //, %, **); precedence; types (int, float,
str, bool); casting; TypeError.

Add player stats to the game.

1. **The stats.** After the welcome message, set the player's
   starting stats:
   - `player_health = 100` (int)
   - `player_max_health = 100` (int)
   - `player_gold = 50` (int)
   - `player_experience = 0` (int)
   - `player_level = 1` (int)
   - `player_attack_power = 10` (int)
   - `player_defense = 5` (int)

2. **Display the stats.** Print a "Player Stats" section showing
   each stat. Use f-strings (e.g., `f"Health: {player_health}/
   {player_max_health}"`).

3. **A practice arithmetic block.** Compute and print:
   - Health percentage: `(player_health / player_max_health) * 100`
   - Experience to next level: `100 * player_level - player_experience`
     (assume each level needs 100 × current_level XP)
   - "Damage per gold spent on potions" (10 health restored per
     5 gold) — just compute and print, not used yet.

4. **A casting demo.** Ask the player how many gold they want to
   keep for emergencies. The input is a string by default — cast
   to int. Handle the case where they type something that's not a
   number (just print a friendly message; we'll use try/except
   later when we know decisions).

In addition to the code, produce:
- **Prompt for Claude:** that you'd send to elicit this code.
- **Tests to validate:**
  1. Run the game. Do the stats print correctly?
  2. Are the arithmetic results what you predicted? (Compute by
     hand.)
  3. Type "abc" when asked for gold — does the program handle it
     gracefully?

Save to `game.py`. Note in a comment which expression you got
confused by during the build (this is the chapter's reflection
discipline).
```

---

**What this produces:** Player stats + an arithmetic demo. The "expression you got confused by" closer is the discipline.

**Connection to previous chapters:** Ch 1's variables and I/O extend to Ch 2's full expressions.

**Preview of next chapter:** Chapter 3 covers objects. You'll realize every stat IS an object and start calling methods on strings (`.upper()`, `.lower()`, `.strip()`) for the player-name normalization.


---

##  AI Wayback Machine

**Run this:**

```
Who is Alonzo Church, and how does their work connect to expressions we covered in this chapter? Keep it to three paragraphs. End with the single most surprising thing about their career or ideas.
```

→ Search **"Alonzo Church"** on Wikipedia.

**Now make the prompt better.** Try one of these:

- Ask it to apply Alonzo Church's ideas to a small Python program you'd write.
- Add a constraint: "Answer including criticisms or limits of Alonzo Church's framework."

What changes? What gets better? What gets worse?
