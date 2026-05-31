# Chapter 2 — Expressions
*The invisible ruler that decides what your code means.*

There's a line of Python you can type right now, before you've learned anything else about the language, and it will surprise you:

```python
3 + 4 * 5
```

If you read left to right — the way you read English, the way you scan a grocery list — you'd expect the answer to be 35. Three plus four is seven, seven times five is thirty-five. But Python returns 23. It didn't add first. It multiplied first, got 20, then added 3.

That's not a bug. That's a rule — one you already know from algebra, except you may not have realized you knew it. And that gap between what the line *looks like it does* and what it *actually does* is the central danger of expressions. Python won't warn you. There's no error message. Just 23 instead of 35, and if this is buried inside a larger calculation, you might not notice for a long time.

This chapter is about learning to see the invisible ruler that Python holds over every expression — the hierarchy of operations, the hidden conversions between number types, and the three different things "division" can mean. Once you can see that ruler, you can write expressions that do exactly what you intended. And when something goes wrong, you'll know where to look.

---

## What an Expression Is

An expression is a piece of code that evaluates to a single value. That's the whole definition, and it's shorter than it sounds. `7` is an expression — it evaluates to the integer seven. `7 * 4` is an expression — it evaluates to twenty-eight. `7 * (3 + 2)` is an expression — it evaluates to thirty-five. The word comes from Latin *expressus*: pressed out. An expression presses a value out of its components.

The components are **literals** — raw values you type directly, like `7` or `3.14` or `"hello"` — and **operators** — symbols that combine them, like `+`, `-`, `*`, `/`, `**`. When Python sees an expression, it evaluates it: follows its rules to produce the single value the expression represents.

What rules? That's the interesting part.

---

## The Hierarchy Python Follows

Python's operator precedence — the hierarchy that determines which operators act first — matches algebra. The same rules your math teacher taught you.

Parentheses win. Whatever is inside parentheses evaluates first, period. Then exponentiation: `**`. Then multiplication, division, floor division, and modulo — all at the same level, evaluated left to right. Then addition and subtraction, also left to right.

The standard mnemonic is PEMDAS, though Python's version includes a few operators your middle school teacher didn't mention.

So in `3 + 4 * 5`: multiplication is at a higher level than addition. Python evaluates `4 * 5` first, gets 20, then evaluates `3 + 20`, gets 23. The left-to-right reading order has nothing to do with it.

In `(3 + 4) * 5`: the parentheses force addition first. Python evaluates `3 + 4` first, gets 7, then evaluates `7 * 5`, gets 35. Parentheses are your tool for overriding the default order.

Here's a worked example worth tracing:

```python
2 + 3 * 4 - 5 ** 2
```

Step 1: Exponentiation first. `5 ** 2 = 25`. Expression becomes `2 + 3 * 4 - 25`.

Step 2: Multiplication next. `3 * 4 = 12`. Expression becomes `2 + 12 - 25`.

Step 3: Addition and subtraction, left to right. `2 + 12 = 14`. Then `14 - 25 = -11`.

Answer: `-11`. Check it in a Python shell. That's exactly what you'll get.

<!-- → [TABLE: Python operator precedence table — columns: precedence level (1–5), operator symbols, name, example, and result — student should see the full hierarchy from parentheses down to addition/subtraction, with exponentiation's right-to-left note] -->

There is one quirk worth naming: `**` is right-to-left, not left-to-right. That means `2 ** 3 ** 2` is `2 ** (3 ** 2)` — which is `2 ** 9 = 512` — not `(2 ** 3) ** 2` — which would be `8 ** 2 = 64`. In practice, when you see stacked exponents, just use parentheses to be explicit. The rule is rarely relevant, but it exists, and it will bite you if you don't know about it.

The deeper point about precedence is this: Python designed it to match algebra so that if you learned math, you already know the hierarchy. You don't have to memorize a new system. The problem is that the match is so clean that it becomes invisible. You stop seeing the rules because you assume they're obvious. They *are* obvious — until they're not, and then you've got a calculation that returned the wrong answer and you have no idea why.

The antidote is parentheses. Not because you need them for Python to evaluate correctly, but because they make the order explicit for the human reading the code — including you, six months from now.

---

## Types, and the Conversions That Happen Without Asking

There are three types you'll work with constantly: integers (`int`), floating-point numbers (`float`), and strings (`str`). They're categories. An integer is a whole number: `7`, `-3`, `0`. A float is a number with a decimal point: `7.0`, `-3.14`, `0.001`. A string is text: `"hello"`, `"7"`, `"3.14"`.

Note that `"7"` and `7` are different things. One is text. One is a number. You cannot do arithmetic with text, even if the text looks like a number.

### When Python Converts Automatically

When you mix an integer and a float in an arithmetic expression, Python converts the integer to a float:

```python
>>> 3 + 2.5
5.5
```

The `3` became `3.0`. The result is `5.5`, a float. Python did this without asking. Why? Because a float can represent everything an integer can, plus fractional parts. Promoting an integer to a float loses nothing. The reverse — demoting a float to an integer — would cut off the decimal, which might not be what you wanted. So Python promotes upward automatically.

This is called **implicit type conversion**: the type changes happen implicitly, as a side effect of the operation.

The interesting case is `/`, the division operator:

```python
>>> 10 / 2
5.0
```

Ten divided by two is five — no fraction, clean result. But Python still returns `5.0`, a float. This is intentional. The `/` operator in Python always returns a float, regardless of whether the result is a whole number. The designers made this choice because division in the mathematical sense produces a real number, and they wanted Python's `/` to match that meaning exactly.

<!-- → [CHART: side-by-side comparison showing int + int → int, int + float → float, float + float → float, int / int → float (highlight this one as the surprise) — student should see the type promotion rules at a glance] -->

If you're coming from older Python (or from languages like C or Java), this will feel wrong. In those systems, `10 / 2` returns `5`, an integer. Python 3 changed this deliberately. Knowing the history helps: the change was controversial, it broke a lot of old code, and it was made anyway because the old behavior was a source of subtle bugs.

### When Python Refuses

Python does not always convert automatically. When the conversion would be ambiguous or lossy in ways Python can't resolve, it raises a `TypeError` and stops:

```python
>>> "5" + 5
TypeError: can only concatenate str (not "int") to str
```

You asked Python to add a string and an integer. But what should that mean? Should it concatenate — turn `"5"` and `5` into `"55"`? Should it do arithmetic — turn `"5"` into `5` and compute `10`? Python doesn't guess. It refuses, and the error message tells you exactly what it expected.

This happens constantly with user input. The `input()` function always returns a string — always, without exception. Type `25` at a prompt, and what's stored in the variable is `"25"`, not `25`. If you then try to do arithmetic with it, Python will stop you.

```python
age = input("How old are you? ")
years_until_retirement = 65 - age   # TypeError
```

The fix is **explicit type conversion**: you tell Python exactly what the type should be.

```python
age = int(input("How old are you? "))
years_until_retirement = 65 - age   # works
```

`int()` converts its argument to an integer. `float()` converts to a float. `str()` converts to a string. These are not operators — they're functions that return a new value of the specified type.

Some conversions are lossy and Python will do them anyway if you ask explicitly:

```python
>>> int(5.9)
5
```

`int(5.9)` is `5`, not `6`. The fractional part is **truncated** — cut off — not rounded. If you want rounding, use `round()`. This is a common mistake: assuming `int()` rounds. It doesn't. It cuts.

<!-- → [TABLE: explicit conversion functions — int(), float(), str(), round() — columns: function, what it does, example input, example output, common mistake] -->

The design principle here is that Python is explicit by preference. When a conversion is unambiguous and lossless, it happens automatically (int to float). When it would require a choice — which of several possible meanings do you want? — Python makes you choose.

---

## Division: Three Operators for Three Different Meanings

Division is the place where most arithmetic surprises live.

The problem is that "division" doesn't mean one thing. It means at least three, depending on context:

- The **exact** answer, including the decimal. Seven divided by four is 1.75.
- The **quotient** — how many complete groups. Seven divided by four is 1, with something left over.
- The **remainder** — what's left over. Seven divided by four leaves a remainder of 3.

Python gives you a separate operator for each meaning.

`/` is **true division**. Always returns a float. Always gives the exact answer.

```python
>>> 7 / 4
1.75
```

`//` is **floor division**. Returns an integer (when both operands are integers). Gives the quotient, rounded down toward negative infinity.

```python
>>> 7 // 4
1
```

`%` is **modulo**. Returns the remainder after floor division.

```python
>>> 7 % 4
3
```

These last two — `//` and `%` — are a pair. They split a number completely. If you divide 7 by 4, you get 1 complete group of 4, and 3 left over. Together they satisfy: `n = (n // d) * d + (n % d)`. The quotient times the divisor, plus the remainder, gives back the original number. Check: `1 * 4 + 3 = 7`. ✓

<!-- → [INFOGRAPHIC: visual diagram showing 7 divided by 4 — a row of 7 blocks, grouped into one complete group of 4 (labeled "quotient = 1, from 7 // 4") and 3 leftover (labeled "remainder = 3, from 7 % 4") — student should see the geometric intuition for floor division and modulo as complementary operations] -->

This pair shows up everywhere in practical programming. A clock uses it: 193 minutes is `193 // 60 = 3` hours and `193 % 60 = 13` minutes. A unit converter uses it: 193 centimeters is `193 // 100 = 1` meter and `193 % 100 = 93` centimeters. Change-making uses it: $1.37 is `137 // 25 = 5` quarters and `137 % 25 = 12` cents remaining, then `12 // 10 = 1` dime and `12 % 10 = 2` cents remaining, and so on.

The key insight is that floor division and modulo answer different questions about the same operation. When you write `//`, your implicit question is "how many complete units?" When you write `%`, your implicit question is "what's left over?" If you find yourself using one without the other, that's often a signal to stop and think about whether you've answered both questions you actually needed to answer.

### A Worked Example: Clock Arithmetic

You leave home at 14:35 (2:35 PM). The drive is 347 minutes. When do you arrive?

```python
current_hour = 14
current_minute = 35
trip_minutes = 347

# Convert current time to total minutes since midnight
total_minutes = current_hour * 60 + current_minute

# Add trip time
arrival_minutes = total_minutes + trip_minutes

# Split back into hours and minutes
arrival_hour = arrival_minutes // 60
arrival_minute = arrival_minutes % 60

# Handle wraparound past midnight
arrival_hour = arrival_hour % 24

print(f"Arrival: {arrival_hour}:{arrival_minute:02d}")
```

Trace through it:
- `14 * 60 + 35 = 875` minutes since midnight.
- `875 + 347 = 1222` minutes since midnight.
- `1222 // 60 = 20`. `1222 % 60 = 22`.
- `20 % 24 = 20` (still same day; this line handles midnight wraparound).
- Output: `Arrival: 20:22`

Every step uses the same small set of tools: multiplication to convert units, `//` and `%` to split into hours and remainder, and modulo a second time to wrap around the 24-hour clock. The same pair of operators, doing the same job, nested inside each other.

<!-- → [FIGURE: step-by-step annotated trace of the clock calculation — each line of the program with its current variable values shown alongside — helps student see how data flows through the expressions] -->

---

## The Python Shell as a Laboratory

Everything in this chapter can be explored in a Python shell. A shell is a REPL: Read, Evaluate, Print, Loop. You type one expression, hit Enter, and see the result. Type another. See the result. The loop runs until you quit.

This is the most efficient learning tool available to you. When you're not sure whether `int(5.9)` truncates or rounds, type it and find out. When you want to test whether `2 ** 3 ** 2` is 512 or 64, type it. When you want to understand what `%` does with negative numbers (it does something that surprises most people), type `−7 % 4` and see `1`, not `-3`, and then think about why.

The shell rewards curiosity. You can break nothing. You can run nothing on a live system. Every hypothesis costs you two seconds to test.

The habit to build: any time you write an expression you're not certain about, test it in the shell first. Not because you're unsure of Python — because the cost of verifying is so low and the cost of being wrong in a larger program is so high.

---

## What the Chapter Adds Up To

Start with what you can see: an expression presses a value out of its components. The machinery underneath has three layers.

The first layer is **operator precedence**. Python evaluates higher-precedence operators before lower-precedence ones. The hierarchy matches algebra. Parentheses override everything. If in doubt, write the parentheses explicitly — they cost nothing and make the order visible.

The second layer is **types and conversion**. Python tracks whether a value is an integer, a float, or a string. When you mix types in arithmetic, Python converts automatically toward the more general type — integers become floats when necessary. When the conversion would be ambiguous, Python refuses with a TypeError. When you need to convert explicitly, use `int()`, `float()`, or `str()`. Remember that `int()` truncates, not rounds.

The third layer is **division's three meanings**. `/` gives you the exact answer as a float. `//` gives you the quotient as an integer. `%` gives you the remainder. The last two work as a pair — use them together to split a number into complete units and leftovers.

Behind all three layers is the same principle: Python does exactly what the rules say, not what you might have assumed. The gap between assumption and rule is where bugs live. Close that gap by understanding the rules well enough that nothing surprises you.

---

*What would change my mind:* If I found a case where Python's operator precedence diverges from mathematical convention in a way that affects everyday code, I'd revise the chapter's confidence that the hierarchy "just works" for programmers with algebra backgrounds. So far I haven't found one.

*Still puzzling:* Why the designers of Python 3 made `/` always return a float, even for `4 / 2 = 2.0`. The mathematical argument is clean — division produces a real number — but the pedagogical cost is real: students who learned elementary school division (quotient plus remainder) have to unlearn a default assumption before they can use `/` correctly. I don't yet have the most elegant way to frame that trade-off in a single sentence.

---

## Exercises

### Warm-up

**Exercise 2.1** *(Predict before you run.)*
Without using Python, evaluate each expression. Write out the steps using precedence rules.

(a) `2 + 3 * 4`
(b) `(2 + 3) * 4`
(c) `10 - 4 // 2`
(d) `2 ** 3 ** 2`

**Exercise 2.2** *(Type prediction.)*
For each expression, predict the type and value before checking in a shell.

(a) `int(5.9)`
(b) `float(5)`
(c) `str(5) + str(3)`
(d) `int("5") + int("3")`

**Exercise 2.3** *(Division operators.)*
Predict the result of each expression.

(a) `15 / 4`
(b) `15 // 4`
(c) `15 % 4`
(d) `15 // 4 * 4 + 15 % 4`

### Application

**Exercise 2.4** *(Unit conversion.)*
Write a program that reads a number of total seconds and converts it to hours, minutes, and seconds. Input `3661` should produce `1 hour 1 minute 1 second`. Use `//` for complete units and `%` for remainders.

**Exercise 2.5** *(Temperature conversion.)*
Write a program that reads a temperature in Fahrenheit and prints it in Celsius: `C = (F - 32) * 5/9`. Test with `32` (should give `0`), `212` (should give `100`), and `98.6` (should give approximately `37`).

**Exercise 2.6** *(Fix the type mismatch.)*
The following program has a type error. Identify where it occurs and fix it with explicit conversion.

```python
num_str = input("Enter a number: ")
result = num_str + 10
print(result)
```

### Synthesis

**Exercise 2.7** *(Bill splitting.)*
Write a program that reads a restaurant bill as a float and a number of diners as an integer, then prints each person's share to two decimal places. Test with $43.21 split among 3 people.

**Exercise 2.8** *(BMI calculator.)*
BMI is `weight_kg / (height_m ** 2)`. Write a program that reads weight in kilograms and height in meters and prints BMI to one decimal place. Test with 70 kg and 1.75 m (should give approximately 22.9). Pay attention to the parentheses.

### Challenge

**Exercise 2.9** *(Change-making.)*
Write a program that reads a dollar amount as a float and calculates the minimum number of coins (dollars, quarters, dimes, nickels, pennies) needed to make that amount. Convert to cents (integer) first, then work down through denominations with `//` and `%`. Test with $1.24.

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

## AI Wayback Machine

**Run this:**

```
Who is Alonzo Church, and how does their work connect to expressions we covered in this chapter? Keep it to three paragraphs. End with the single most surprising thing about their career or ideas.
```

→ Search **"Alonzo Church"** on Wikipedia.

**Now make the prompt better.** Try one of these:

- Ask it to apply Alonzo Church's ideas to a small Python program you'd write.
- Add a constraint: "Answer including criticisms or limits of Alonzo Church's framework."

What changes? What gets better? What gets worse?
