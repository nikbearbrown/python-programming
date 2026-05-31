# Chapter 5 — Loops

## Three title options

1. Loops: How Programs Repeat
2. Iteration: Teaching Code to Do the Same Thing Over and Over
3. Repetition as the Engine of Computation

## TL;DR

A loop is a block of code that runs repeatedly until a condition becomes false. `while` loops let you repeat code as long as some condition is true; `for` loops let you iterate over sequences like ranges or lists. Between them, with accumulator patterns and loop control statements, you can solve most problems that require doing the same thing many times.

---

## Cold open

A parking meter in a city you don't live in is a loop. You have thirty minutes. The meter ticks. One second passes. Fifty-eight seconds remain. Another second passes. Fifty-seven. The meter checks its condition once per second: *Is time remaining?* If yes, the light stays green. If no, it flicks red and an enforcement officer's app pings them three blocks away.

Your phone checks the same loop while it sits dark on your desk. *Is the screen being touched?* No. *Is motion detected?* No. *Are notifications pending?* No. Check again. Again. Again. A thousand times a second, your phone's CPU is running code that looks, in the simplest form, like this:

```
while the screen is idle:
    keep the display off
    listen for input
```

The heart is a loop. It fires an electrical pulse, and the chambers contract. The pulse subsides. The valves close. The rhythm waits. Then it fires again. Seventy times a minute, in a healthy adult, without instruction, without thought.

Repetition is the engine of computation. A loop is how you teach code to repeat.

### Learning objectives

By the end of this chapter you will be able to:

- **Write** and debug `while` loops with explicit termination conditions.
- **Construct** `for` loops using `range()` and iteration over containers.
- **Apply** the accumulator pattern to build running totals, counts, and collections.
- **Use** `break` and `continue` to control loop execution.
- **Identify** infinite loops, off-by-one errors, and loop-related logical mistakes.
- **Choose** between `while` and `for` for different contexts and explain the trade-off.

### Prerequisites

From Chapter 3: understanding `True` and `False`, comparison operators (`==`, `<`, `>`). From Chapter 4: if-statements and indentation. Comfort with variables and basic arithmetic.

### Why this chapter matters

Loops are not a feature of Python. They are the core of programming. Every task that touches data — tallying votes, scanning a million-row CSV, checking thousands of transactions for fraud, rendering pixels on a screen — is a loop. Functions matter. Classes matter. But loops are the thing that makes code do *anything* at scale.

---

## Concept 1 — The while loop: condition-controlled repetition

A parking meter's logic is brutally simple: *while time remains, keep the light green*. The minute time reaches zero, the condition becomes false, and the loop exits.

In Python, a **while loop** repeats a block of code as long as a condition is true. The moment the condition becomes false, the loop stops and execution continues to the next statement.

Here is the structure:

```python
while condition:
    # loop body — statements that execute repeatedly
    # at least one statement here must eventually make the condition false
```

You read it aloud: "While the condition is true, execute the loop body. When the condition becomes false, exit the loop."

### A concrete example: monthly bank balance

Imagine you have a savings account with $1,000. Every month, you deposit $50. How many months until you have at least $2,000?

```python
balance = 1000
monthly_deposit = 50
month = 0

while balance < 2000:
    balance += monthly_deposit
    month += 1

print(f"After {month} months, balance is ${balance}")
```

> **Prompt for Claude.** "Write a Python program that starts with a bank balance of $1000 and adds $50 each month. Count how many months it takes to reach $2000. Use a while loop with condition `balance < 2000`. Print the result in a sentence."

> **Tests to validate.**
> - After running, the variable `month` should be 20 (since 1000 + 50*20 = 2000).
> - `balance` should equal exactly 2000.
> - The loop should not execute at all if `balance` starts at 2000 or higher.

Notice the three parts: (1) initialization before the loop — variables set to starting values; (2) the condition that gets checked before each iteration; (3) the statement inside the loop that changes the condition toward false. If you forget any of these, the loop will either not run at all, run forever, or never reach the target.

### The infinite loop: a trap and a lesson

An **infinite loop** is a loop whose condition never becomes false. The program will run forever (or until you force it to stop by pressing Ctrl+C).

```python
# DANGEROUS — DO NOT RUN
counter = 1
while counter > 0:
    print(counter)
    # BUG: counter is never updated, so the condition stays true forever
```

Why does this happen? The counter starts at 1, which is greater than 0. The condition is true, so we print. The counter stays 1. We check the condition again: 1 > 0 is still true. Print again. Forever.

The fix:

```python
counter = 1
while counter > 0:
    print(counter)
    counter -= 1  # This line actually changes the condition toward false

print("Done")
```

Now: counter is 1, print 1, decrement to 0. Check: 0 > 0 is false. Exit. Print "Done".

### Counting up and down: step sizes

The **step size** is how much the counter changes in each iteration. If you count up by 1 each time, the step is +1. If you count down by 2 each time, the step is −2.

```python
# Count up by 1s
n = 1
while n <= 5:
    print(n)
    n += 1
# Output: 1 2 3 4 5

# Count down by 2s
n = 10
while n > 0:
    print(n)
    n -= 2
# Output: 10 8 6 4 2
```

> **Prompt for Claude.** "Write a while loop that starts at 100 and counts down by 10 each iteration until the counter is less than or equal to 0. Print each value. Test it."

> **Tests to validate.**
> - The output should be: 100, 90, 80, 70, 60, 50, 40, 30, 20, 10.
> - The loop should terminate (not run forever).

### Common while-loop mistakes

**Off-by-one error:** You want to print 1 through 10, but you write `while n < 10:` instead of `while n <= 10:`. The loop prints 1 through 9 and stops. The off-by-one error is the most common loop bug in existence. Always double-check the boundary condition.

**Uninitialized variable:** You forget to initialize the counter before the loop starts. Python will throw a `NameError: name 'counter' is not defined`. This is also easy to catch — the error message tells you exactly what went wrong.

**Condition always false:** You write `while balance > 10000:` but balance starts at 500. The condition is already false, so the loop never runs. This is usually a logic error, not a syntax error. The program runs; it just doesn't do what you intended.

---

## Concept 2 — The for loop: iteration over sequences

A `while` loop is *condition-controlled*: you keep looping while some condition is true. A **for loop** is *sequence-controlled*: you loop once for each element in a sequence. Sequences can be lists, strings, or ranges.

Here is the structure:

```python
for variable in sequence:
    # loop body — executes once per element
```

You read it aloud: "For each element in the sequence, assign it to the variable, and execute the loop body."

### Iterating with range()

The **range()** function generates a sequence of integers. It is the workhorse of counted loops.

`range(n)` generates integers from 0 to n−1. (Note: the end is exclusive, the start is exclusive of nothing.)

```python
for i in range(5):
    print(i)
# Output: 0 1 2 3 4
```

`range(start, end)` generates integers from start to end−1.

```python
for i in range(2, 6):
    print(i)
# Output: 2 3 4 5
```

`range(start, end, step)` generates integers from start to end−1, stepping by step each time.

```python
for i in range(0, 10, 2):
    print(i)
# Output: 0 2 4 6 8

for i in range(10, 0, -1):
    print(i)
# Output: 10 9 8 7 6 5 4 3 2 1
```

> **Prompt for Claude.** "Write a for loop using range() that prints all multiples of 5 from 0 to 50 (inclusive). Use a step of 5. Test it."

> **Tests to validate.**
> - The output should be: 0, 5, 10, 15, 20, 25, 30, 35, 40, 45, 50.
> - The loop should run exactly 11 times.

### Iterating over strings and lists

A `for` loop doesn't require `range()`. It works over any sequence: a string, a list, or any iterable.

```python
name = "Claude"
for letter in name:
    print(letter)
# Output: C l a u d e

students = ["Alice", "Bob", "Carol"]
for student in students:
    print(student)
# Output: Alice Bob Carol
```

**Etymology:** *iterate*, from Latin *iterare*, meaning to do again — each pass through the loop is one iteration. The loop variable (here, `letter` or `student`) takes on a new value from the sequence each time.

When you need both the position (index) and the value, Python provides `enumerate()`:

```python
students = ["Alice", "Bob", "Carol"]
for index, student in enumerate(students):
    print(f"{index}: {student}")
# Output:
# 0: Alice
# 1: Bob
# 2: Carol
```

> **Prompt for Claude.** "Write a for loop that iterates over the list ['apple', 'banana', 'cherry'] using enumerate(). Print the index and the fruit name in the format '0: apple', etc."

> **Tests to validate.**
> - The output should show three lines with indices 0, 1, 2 and the corresponding fruit names.

### While vs. For: when to choose which

**Use `while` when:**
- The loop condition is complex and not a simple "iterate over a sequence."
- You don't know in advance how many iterations you need (e.g., "read until the user enters 'quit'").
- The step size or termination is dynamic and changes during the loop.

**Use `for` when:**
- You are iterating a known number of times (e.g., `range(100)`).
- You are iterating over the elements of a sequence (list, string, range).
- You want compact, readable code.

Here is the trade-off: `while` is more flexible but requires you to manage the condition and updates yourself. `for` is less flexible but cleaner when you are just iterating through a sequence.

```python
# While: counting to 10, the long way
i = 0
while i < 10:
    print(i)
    i += 1

# For: counting to 10, the clean way
for i in range(10):
    print(i)
```

The for-loop version is shorter and clearer. Use it.

### Common for-loop mistakes

**Off-by-one with range():** `range(5)` gives you 0, 1, 2, 3, 4 — five values, but they do not include 5. If you want 1 through 5, write `range(1, 6)`.

**Forgetting the colon:** Every loop (and every if-statement) ends with a colon. `for i in range(10)` will throw a `SyntaxError`.

**Modifying the sequence during iteration:** If you add or remove elements from a list while looping over it, you will skip elements or cause errors. Don't do this.

---

## Concept 3 — The accumulator pattern and loop control

Most loops do not just print or look at data. They *accumulate* a result: a running total, a count, a collection of filtered items. The **accumulator pattern** is the standard way to do this.

### The accumulator pattern: building totals and counts

An **accumulator** is a variable that starts with an initial value (usually 0 for a sum, 1 for a product, or an empty list for a collection) and gets updated on each iteration.

#### Summing values

```python
numbers = [3, 7, 2, 9, 1]
total = 0  # accumulator: start at 0

for num in numbers:
    total += num  # accumulator: add each number

print(f"Sum: {total}")
# Output: Sum: 22
```

> **Prompt for Claude.** "Write a Python program that takes a list of test scores and calculates the sum using an accumulator pattern. The list is `[85, 92, 78, 88, 95]`. Print the total."

> **Tests to validate.**
> - The sum should be 438.
> - The accumulator should start at 0 before the loop.

#### Counting occurrences

```python
text = "hello world"
count = 0  # accumulator: start at 0

for char in text:
    if char == 'l':
        count += 1

print(f"Letter 'l' appears {count} times")
# Output: Letter 'l' appears 3 times
```

> **Prompt for Claude.** "Write a program that counts how many times the letter 'e' appears in the sentence 'The quick brown fox jumps over the lazy dog'. Use an accumulator pattern."

> **Tests to validate.**
> - The count should be 3 (in "the" and "over").

#### Building a list

```python
# Filter even numbers
numbers = [3, 7, 2, 9, 1, 8, 4]
evens = []  # accumulator: start with empty list

for num in numbers:
    if num % 2 == 0:
        evens.append(num)

print(evens)
# Output: [2, 8, 4]
```

**Etymology:** *accumulator*, from Latin *accumulare*, meaning to heap up — the loop variable that accumulates a running total, count, or collection by adding to it on each iteration.

### Loop control: break and continue

Sometimes you want to exit a loop early or skip to the next iteration. Python provides two control statements.

**`break`:** Exits the loop immediately.

```python
user_string = "hello"
for letter in user_string:
    if letter == 'l':
        print(f"Found 'l' at position {letter}")
        break
    print(letter)

# Output:
# h
# e
# Found 'l' at position l
```

**`continue`:** Skips the rest of the current iteration and jumps to the next one.

```python
for i in range(5):
    if i == 2:
        continue
    print(i)

# Output:
# 0
# 1
# 3
# 4
```

> **Prompt for Claude.** "Write a program that iterates from 1 to 10 and prints each number, but skips the number 5 using a continue statement. Test it."

> **Tests to validate.**
> - The output should be 1, 2, 3, 4, 6, 7, 8, 9, 10 (5 is missing).
> - The loop should complete without errors.

### Scale shift: from one iteration to a million

One iteration of a loop might process one row in a CSV file, one vote in an election tally, one pixel on a screen. Multiply that iteration by a million rows, a million votes, a million pixels. The code does not change — only the number of repetitions. But the consequence of an infinite loop, an off-by-one error, or an inefficient accumulator becomes visible at scale.

A data scientist's loop that processes a million-row CSV takes minutes or hours to run. An infinite loop in that context is a crash, not just an annoyance. This is why "test on small data first" is not a suggestion — it is a discipline.

---

## Integration and synthesis

You now have three tools for repetition: `while` loops, `for` loops, and loop control. These are the foundational blocks of almost every program that does anything nontrivial.

Here is how to think about a problem:

1. **Identify the repetition:** What task needs to repeat? How many times? Is the number of repetitions known in advance?
2. **Choose the loop type:** If you know the count and have a sequence, use `for`. If you need to check a condition until it becomes false, use `while`.
3. **Initialize the accumulator:** If you are building a result, start with the right initial value (0 for sum, empty list for collection).
4. **Update the accumulator each iteration:** The loop body should change the result in the right direction.
5. **Check the termination:** Will the condition become false? Will the loop not run forever? Test on a small case first.

The most dangerous loop is one you don't test. The second most dangerous is one that seems to work but doesn't check the boundaries.

---

## Graduated exercises

### Warm-up

1. Write a `while` loop that counts from 10 down to 1 and prints each number. Verify it prints exactly ten numbers.

2. Write a `for` loop using `range()` that prints every third number from 0 to 20 (0, 3, 6, 9, ..., 18). Use step size 3.

3. Using a `for` loop and an accumulator, calculate the sum of the numbers 1 through 100. (Hint: `range(1, 101)` gives 1 through 100.)

### Application

4. Write a program that takes a list of daily temperatures: `[72, 68, 75, 71, 69, 74, 76]`. Calculate the average temperature using a `while` loop, an accumulator, and a counter.

5. Write a program that iterates through the string `"programming"` and counts how many vowels (a, e, i, o, u) appear. Use an accumulator and `for` loop.

6. Given a list of integers `[2, 3, 5, 7, 11, 12, 13]`, use a loop with a `break` statement to find and print the first even number greater than 10. If no such number exists, print a message.

### Synthesis

7. **FizzBuzz:** Write a program using a `for` loop that prints numbers from 1 to 30, with the following rules:
   - If the number is divisible by 3, print "Fizz".
   - If the number is divisible by 5, print "Buzz".
   - If the number is divisible by both 3 and 5, print "FizzBuzz".
   - Otherwise, print the number itself.

8. **Prime sieve:** Write a program that finds all prime numbers less than 50 using loops. A prime number is only divisible by 1 and itself. For each candidate number, loop through potential divisors and use `break` to exit early if a divisor is found. Use an outer loop to iterate through candidates and an inner loop to check divisibility.

---

## Claude Code

You now know `while`, `for`, accumulators, and loop control. This section brings them together into a single working program that you write *with* Claude Code, then test, then run.

> **Claude Code prompt.**
>
> "Build a Python script called `prime_sieve.py` that takes a number N from the user and prints all prime numbers from 2 up to and including N. A prime is only divisible by 1 and itself. Use a for loop to iterate through candidates from 2 to N (use range). For each candidate, use a nested while or for loop to check divisors starting from 2. Use break to exit early if you find a divisor. Use an accumulator list to collect the primes you find. Then write a separate script called `test_primes.py` that calls your sieve function and verifies it returns exactly the primes in the list [2, 3, 5, 7, 11, 13] and none of [4, 6, 8, 9] when N=20. Run the tests and show me the output."

**What to expect.** Claude Code will generate a working sieve that exercises `for` with `range`, a nested loop for divisor checking, `break` to skip composite numbers, and a list accumulator to collect results. You will see the script run live, then verify the test passes. This pattern—generate, test, verify—is how professional code gets written.

**Stretch.** Ask Claude Code: "How would you modify the sieve to count how many primes exist up to N without storing them? Use an accumulator counter instead of a list, and update it with `count += 1` instead of appending. Run it for N=100."

---

## Chapter summary

A loop is a block of code that repeats. Python provides two kinds:

- **`while` loop:** Repeats while a condition is true. You control initialization, condition, and updates.
- **`for` loop:** Repeats once for each element in a sequence. Cleaner and less error-prone when iterating a known number of times or over a sequence.

The **`range()` function** generates integer sequences. It is essential for counted loops.

The **accumulator pattern** builds results (sums, counts, lists) by starting with an initial value and updating it on each iteration.

**`break`** exits a loop early. **`continue`** skips the rest of the current iteration.

The most common loop errors are infinite loops (condition never becomes false), off-by-one errors (boundary condition too strict or too loose), and forgetting to initialize or update the accumulator. Test every loop on small data before running it at scale.

---

## Connections forward

- Chapter 6 (Functions): Functions often contain loops. A function that processes a list or counts occurrences is a function that loops.
- Chapter 9 (Lists): Loops are how you build, modify, and search lists. Most list operations are loops in disguise.
- Chapter 15 (Data Science): Every data-science operation — filtering, aggregating, transforming — is a loop over rows or columns.

---

## What would change my mind

If a student showed me a loop that does something I said was impossible with the given constructs, or if I misstated how `range()` handles boundaries or how `break` behaves in nested loops.

## Still puzzling

I have not yet seen a clean explanation for why Python's `range()` uses exclusive upper bounds (0 to n−1) rather than inclusive. It is mathematically consistent with half-open intervals [start, end), and there are good reasons for it, but it trips up students enough that I wish the language had gone the other way.

---

## Tags

loop, iteration, while, for, range, accumulator, break, continue, repetition, condition-controlled, sequence-controlled

## Author byline

Nik Bear Brown
---

## LLM Exercise — Chapter 05: Loops (Text Adventure Project)

**Project:** Text Adventure.
**What you're building this chapter:** the game loop — `while not game_over`. Plus a combat-turn loop.
**Tool:** **Claude Code**.

---

**The Prompt:**

```
Chapter 5 of my Text Adventure project. Chapter 5 covered: while
loops; for loops; range; accumulator patterns; break and continue;
infinite loops as a real risk.

Wrap the game in a real game loop and add combat turns.

1. **The game loop.** Wrap the existing branching code from Ch 4
   in `while not game_over:`. The loop should:
   - Print the current location description.
   - Prompt for a command.
   - Branch on the command.
   - Repeat until game_over becomes True.

   Add a "quit" command that sets game_over = True. Test that it
   works.

2. **A combat-turn loop.** When the player encounters an enemy
   (use a placeholder for now — make it a known location), enter
   a combat loop:
   ```
   while enemy_health > 0 and player_health > 0:
       print combat options ("attack", "defend", "run")
       get input
       if attack: enemy_health -= player_attack_power
       if defend: take less damage this turn
       if run: break out of combat (with chance of success)
       enemy attacks (subtract from player_health)
       print current health for both
   ```
   At end: print "you won" or "you died" based on which dropped
   to 0.

3. **An accumulator pattern.** Track turns_taken — increment by 1
   each iteration of the main game loop. At end of game, print
   "You finished in N turns." This is the accumulator pattern.

4. **Break and continue.** Use break in the combat loop (to flee).
   Use continue somewhere — maybe a stuck-in-mud check that skips
   the player's turn.

In addition to the code, produce the prompt + tests. Test:
- The main loop runs and exits cleanly on "quit."
- Combat runs to completion (player wins).
- Combat runs to completion (player dies → game over).
- The turn counter at end of game is correct.

End with: identify one place in your code where an infinite loop
would happen if you forgot to update the loop variable. Comment
in the code naming that risk.
```

---

**What this produces:** A working game loop + combat turn loop. The game now plays.

**Connection to previous chapters:** Ch 4's branching gets repeated; Ch 5's loops make the game playable.

**Preview of next chapter:** Chapter 6 covers functions. You'll refactor the growing code into named functions — `describe_room()`, `take_damage()`, `process_command()` — which is the first big code-quality leap.


---

##  AI Wayback Machine
**John Backus** was led the team that built FORTRAN in 1957 — bringing structured loops to programming for the first time.

**Run this:**

```
Who is John Backus, and how does their work connect to loops we covered in this chapter? Keep it to three paragraphs. End with the single most surprising thing about their career or ideas.
```

→ Search **"John Backus"** on Wikipedia.

**Now make the prompt better.** Try one of these:

- Ask it to apply John Backus's ideas to a small Python program you'd write.
- Add a constraint: "Answer including criticisms or limits of John Backus's framework."

What changes? What gets better? What gets worse?
