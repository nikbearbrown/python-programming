# Chapter 4 — Decisions
*The gate that decides which code runs — and why exactly one path executes.*

Here's a line of Python that looks completely harmless:

```python
if balance = 0:
    print("Account empty.")
```

Run it and you get a `SyntaxError`. The code won't even start. But if you change that one character — `=` to `==` — it runs perfectly. The first version tries to *assign* zero to `balance` inside a condition. The second *compares* balance to zero and asks whether the comparison is true.

Those two characters are responsible for more beginner bugs than almost anything else in the language. And they're the entry point to this chapter, because they reveal what a decision actually is: a test that produces either `True` or `False`, which then determines which code runs next. Once you understand that, the rest — `if`, `elif`, `else`, `and`, `or`, `not` — falls into place.

---

## What a Boolean Is

A Boolean is a data type with exactly two values: `True` and `False`. Named for George Boole, a 19th-century English logician who built the algebra of true and false, it is the atom of all decision-making in code. Every condition you write eventually presses down to one of these two values.

You can create one directly:

```python
is_raining = True
logged_in = False
```

Or you can create one by comparing values. Python has six comparison operators, each of which produces a Boolean:

```python
age = 25
print(age == 25)    # True  — equal to
print(age > 30)     # False — greater than
print(age != 25)    # False — not equal to
print(age >= 18)    # True  — greater than or equal
print(age < 18)     # False — less than
print(age <= 30)    # True  — less than or equal
```

The comparison happens, produces `True` or `False`, and that result is what the `if` statement receives.

<!-- → [TABLE: all six comparison operators — columns: operator, meaning, example expression, result — student should have a quick-reference for the complete set before seeing them in conditionals] -->

This is the mechanism behind the `=` vs `==` confusion. `=` assigns a value to a variable — it's a statement, not an expression. `==` compares two values and produces a Boolean — it's an expression that can live inside a condition. Mixing them up is natural because they look alike, but they do fundamentally different things.

### Truthiness: When Any Value Becomes a Boolean

You can also convert any value to a Boolean using `bool()`. The rule is simple: zero, empty, and nothing are `False`. Everything else is `True`.

```python
bool(0)       # False
bool(1)       # True
bool(-5)      # True
bool(0.0)     # False
bool("")      # False — empty string
bool("hello") # True
bool([])      # False — empty list
```

This matters because Python will silently apply this conversion when a non-Boolean appears in a condition. You can write `if name:` instead of `if name != ""`, and Python handles the conversion. The idiom is common enough that you'll see it everywhere in real code.

The risk is that it can obscure what you're actually checking. `if count:` is shorter than `if count > 0:`, but the longer form is explicit about the question being asked. When in doubt, be explicit. The extra characters cost nothing.

---

## The if Statement and Its Chain

An `if` statement has one job: run a block of code if a condition is `True`, skip it if not.

```python
temperature = int(input("Temperature in Celsius: "))

if temperature < 0:
    print("Below freezing.")
```

The colon after the condition and the indentation of the block are both syntax, not style. Python uses indentation to determine which statements belong to which block. Four spaces is the convention. Miss the colon or the indentation and Python raises a `SyntaxError` before running a single line.

Add an `else` and you cover the other branch:

```python
if temperature < 0:
    print("Below freezing. Stay warm.")
else:
    print("Above freezing.")
```

One path always executes. Exactly one. This guarantee is the structural property that makes conditionals useful — you know the code you didn't choose to run was skipped, not run accidentally.

Add `elif` when there are more than two cases:

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

<!-- → [INFOGRAPHIC: flowchart of the hour-based greeting chain — diamonds for each condition, arrows labeled True/False, exactly one terminal node reached per execution — student should see the single-path guarantee visually] -->

The chain evaluates top to bottom and stops at the first `True` condition. If `hour` is 10, the first check (`hour < 6`) is false, the second (`hour < 12`) is true, and we get "Good morning." The remaining `elif` and `else` are skipped entirely. The chain doesn't keep checking after it finds a match.

This is why the order matters. If you wrote `elif hour < 18:` before `elif hour < 12:`, every morning hour would fall into the afternoon bucket — `10 < 18` is true, and the chain would stop there before ever reaching the morning check. The hierarchy of conditions encodes logic that's easy to get wrong if you're not reading the chain as a sequence of filters.

### A Worked Example: The ATM

An ATM checks three rules before approving a withdrawal: the account must be valid, the amount must be positive, and the balance must cover the withdrawal.

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

Each `elif` checks one rule. The chain stops at the first failure, so the user gets exactly one error message. If all checks pass, the `else` executes the actual withdrawal. This pattern — validating each precondition in sequence before performing the operation — is the standard structure for any code that has to be careful about what it accepts.

<!-- → [FIGURE: annotated trace of the ATM code for two sample inputs: one that fails at "insufficient funds" and one that reaches "approved" — shows which lines execute and which are skipped for each case] -->

---

## Boolean Operators: Combining Conditions

Single conditions answer one question at a time. Most real decisions require several questions answered together. `and`, `or`, and `not` combine Booleans into compound conditions.

`and` requires both sides to be `True`:

```python
age = 25
has_license = True

if age >= 18 and has_license:
    print("You can drive.")
```

If either side is `False`, the whole expression is `False`. Both must be true.

`or` requires at least one side to be `True`:

```python
is_member = False
total_spent = 150

if is_member or total_spent >= 100:
    print("Discount applied.")
```

Only if both sides are `False` is the whole expression `False`.

`not` flips the Boolean:

```python
is_expired = True
if not is_expired:
    print("License is valid.")
```

<!-- → [TABLE: truth tables for and, or, not — columns: p, q, p and q, p or q, not p — standard two-row-per-case layout — student should have the complete logical reference] -->

### Short-Circuit Evaluation

Python evaluates `and` and `or` left to right and stops as soon as it knows the answer. For `and`: if the left side is `False`, the whole expression is `False` — Python never evaluates the right side. For `or`: if the left side is `True`, the whole expression is `True` — the right side is skipped.

This has a practical consequence:

```python
count = 0
if count > 0 and 10 / count > 2:
    print("OK")
```

When `count` is zero, `count > 0` is `False`. Python stops there and never evaluates `10 / count` — which would otherwise divide by zero and raise an error. The short-circuit is doing protective work. If you know the right-side condition is expensive or dangerous when the left side fails, put the left side first.

### Precedence and Parentheses

Comparison operators have higher precedence than Boolean operators, which usually works out correctly:

```python
if age >= 18 and height > 60:
# Evaluates as: (age >= 18) and (height > 60)
```

But when you mix `and` and `or`, the precedence rule is that `and` binds more tightly than `or`. This can surprise you:

```python
if age < 18 or age > 65 and income < 30000:
# Evaluates as: (age < 18) or ((age > 65) and (income < 30000))
```

That may be what you intended — "under 18, OR over 65 and low-income" — but it's not obvious from reading. Use parentheses to make the grouping explicit:

```python
if (age < 18) or (age > 65 and income < 30000):
```

The rule of thumb: whenever you mix `and` and `or` in the same condition, write the parentheses. The extra characters remove all ambiguity.

### A Worked Example: Clock Arithmetic with Decisions

Expand the clock calculation from Chapter 2. You have arrival time in total minutes. Now you want to determine the time-of-day period:

```python
arrival_minutes = 1222   # From Chapter 2: departure 14:35 + 347 minutes
arrival_hour = arrival_minutes // 60 % 24
arrival_minute = arrival_minutes % 60

if arrival_hour < 6 or arrival_hour >= 22:
    period = "night"
elif arrival_hour < 12:
    period = "morning"
elif arrival_hour < 18:
    period = "afternoon"
else:
    period = "evening"

print(f"Arrival: {arrival_hour}:{arrival_minute:02d} ({period})")
```

The `or` in the first condition captures the two disjoint "night" ranges — before 6 AM and after 10 PM — in a single check. The `elif` chain handles the remaining daylight hours in sequence.

---

## Nested vs. Chained Decisions

Two valid approaches handle multi-condition decisions differently. The choice between them is architectural.

**Chain with `and`:** Shorter, one pass through the conditions.

```python
if username == "admin" and password == "secret":
    print("Access granted.")
else:
    print("Access denied.")
```

**Nest the checks:** More code, but each failure path can give a specific message.

```python
if username == "admin":
    if password == "secret":
        print("Access granted.")
    else:
        print("Wrong password.")
else:
    print("Unknown user.")
```

The chain tells the user nothing about *why* they were denied. The nested version tells them whether the username or the password was wrong — which is useful for a legitimate user and a minor security concern (it tells an attacker which half they got right). The right choice depends on context.

A thermostat is a good example of where the distinction matters. A thermostat reads room temperature and setpoint, then decides whether to heat, cool, or do nothing. A naive version would check `room_temp < setpoint`. But real thermostats use *hysteresis* — a dead band that prevents the system from cycling on and off constantly:

```python
HYSTERESIS = 0.5

if room_temp < setpoint - HYSTERESIS:
    action = "Heat"
elif room_temp > setpoint + HYSTERESIS:
    action = "Cool"
else:
    action = "Off"
```

The `elif` chain is exactly right here: exactly one action per evaluation, with the middle range explicitly handled by `else`. A nested structure would be harder to read and no clearer in intent.

<!-- → [FIGURE: diagram showing the thermostat's three zones — below (setpoint − 0.5), above (setpoint + 0.5), and the dead band between them — labeled with Heat, Off, Cool respectively — student should see the hysteresis band as a spatial region, not just a pair of inequalities] -->

---

## What the Chapter Adds Up To

The machinery here is small: one new type (`bool`), one conversion rule (truthiness), six comparison operators, three Boolean operators, and three keywords (`if`, `elif`, `else`). Everything else is the application of these pieces.

The central structural property worth holding onto: a conditional chain guarantees exactly one path executes. Not zero. Not two. One — or zero if there's no `else` and all conditions are false. That guarantee is what makes decision-making code predictable.

The central mistake worth guarding against: `=` vs. `==`. Assignment does not compare. Comparison does not assign. In a condition, you want `==`. The way to avoid this mistake is to read `==` as a question — "is `age` equal to 25?" — and `=` as a statement — "set `age` to 25." They are different grammatical moves.

*What would change my mind:* If the thermostat example consistently confuses students who haven't encountered hysteresis before, I'd replace it with a simpler system — a price discount, a speed limit — that has the same structure without the domain knowledge requirement.

*Still puzzling:* The line between using truthiness (`if name:`) and being explicit (`if name != ""`) is a judgment call that experienced Python programmers debate in code review. The chapter notes that both appear in professional code and recommends clarity as the tiebreaker. But there's no rule that tells you which is clearer in a given context — only practice.

---

## Exercises

### Warm-up

**Exercise 4.1** *(Truthiness prediction.)*
For each expression, predict `True` or `False` without running code. Then verify in a shell.

```python
bool(0)
bool("")
bool([])
bool(3.14)
bool("0")
```

**Exercise 4.2** *(Comparison.)*
Write a snippet that reads two integers and prints which is larger, or "Equal" if they match.

**Exercise 4.3** *(The = vs == trap.)*
In one sentence, explain why `if x = 5:` is wrong and `if x == 5:` is right.

### Application

**Exercise 4.4** *(Grading chain.)*
Write a program that reads a test score (0–100) and prints the letter grade: A for 90+, B for 80–89, C for 70–79, D for 60–69, F below 60. Test the boundaries — 90 should give A, 89 should give B.

**Exercise 4.5** *(Login check.)*
Write a program that reads a username and password. Grant access only if username is `"admin"` and password is `"secret"`. Otherwise print a denial message.

**Exercise 4.6** *(Discount eligibility.)*
A customer gets a discount if they are under 18 OR a member. Read age and membership status (`"yes"` or `"no"`) and print whether the discount applies.

### Synthesis

**Exercise 4.7** *(Age validation.)*
Read a birth year. Assume the current year is 2026. Calculate age. Check: (1) age is between 0 and 150, and (2) the person is at least 18. Print a specific message for each failure case, or "You are of legal age" if both pass.

**Exercise 4.8** *(Multi-condition ATM.)*
Expand the ATM example. Check in order: valid 7-digit account ID, positive amount, sufficient balance, daily limit of $500. Print a specific error for the first failure, or "Approved. New balance: $X" on success.

**Exercise 4.9** *(Restaurant seating.)*
Read party size and hour of day (0–23). If party size is over 6 and the hour is between 12–13 or 18–20 (peak hours): "Wait 30 minutes." If party size is over 6 at any other time: "Wait 10 minutes." Otherwise: "Seat immediately." Use `and` / `or` as needed.

### Challenge

**Exercise 4.10** *(Leap year.)*
A year is a leap year if: divisible by 400, OR divisible by 4 AND not divisible by 100. Write a program that reads a year and determines whether it's a leap year. Test with 2000 (leap), 1900 (not), 2024 (leap), 2026 (not).

**Exercise 4.11** *(Traffic light state machine.)*
A traffic light cycles: red → green → yellow → red. Read the current color and print the next. Handle invalid input gracefully.

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

## AI Wayback Machine

**Edsger Dijkstra** argued in 1968 that `goto` considered harmful — and laid the groundwork for structured decision flow in modern programming.

![Edsger Dijkstra](../images/edsger-dijkstra-64z.png)

*Puppet Art by [Nik Bear Brown](https://www.nikbearbrown.com/).*

**Run this:**

```
Who is Edsger Dijkstra, and how does their work connect to decisions we covered in this chapter? Keep it to three paragraphs. End with the single most surprising thing about their career or ideas.
```

→ Search **"Edsger Dijkstra"** on Wikipedia.

**Now make the prompt better.** Try one of these:

- Ask it to apply Edsger Dijkstra's ideas to a small Python program you'd write.
- Add a constraint: "Answer including criticisms or limits of Edsger Dijkstra's framework."

What changes? What gets better? What gets worse?
