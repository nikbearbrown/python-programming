# Chapter 5 — Loops
*Repetition is the engine. A loop is how you install it.*

A parking meter is a loop. You have thirty minutes. The meter ticks. One second passes. Twenty-nine minutes and fifty-nine seconds remain. Another second passes. Twenty-nine minutes and fifty-eight. The meter checks its condition once per second: *Is time remaining?* If yes, the light stays green. If no, it flips red and an enforcement officer's app pings them three blocks away.

Your phone runs the same logic while it sits dark on the desk. *Is the screen being touched? Is motion detected? Are notifications pending?* No. No. No. Check again. A thousand times a second. Every idle device you own is executing a loop right now.

The heart is a loop. An electrical pulse fires, the chambers contract, the valves close, the rhythm waits. Then it fires again. Seventy times a minute, without instruction, without thought.

Repetition is not a special case in computation. It is the core of it. A loop is how you teach code to repeat — and once you understand loops, you understand how software handles anything at scale.

---

## The while loop: condition-controlled repetition

The simplest loop has the same structure as the parking meter: keep going *while* the condition is true, stop the moment it becomes false.

```python
while condition:
    # loop body
```

You read it aloud: "While the condition is true, execute the body. Check again. If still true, execute again. When false, exit."

Here is a concrete version. You have a savings account with $1,000. Each month you deposit $50. How many months until you reach $2,000?

```python
balance = 1000
monthly_deposit = 50
month = 0

while balance < 2000:
    balance += monthly_deposit
    month += 1

print(f"After {month} months, balance is ${balance}")
```

```
After 20 months, balance is $2000
```

Three things must coexist for a `while` loop to work correctly. First, the variables the condition depends on must be initialized before the loop begins — `balance` and `month` both start at known values. Second, the condition must be checkable at the top of each iteration — `balance < 2000` is evaluated before every pass. Third, something inside the loop body must eventually make the condition false — `balance += monthly_deposit` increases the balance toward the threshold.

Remove any of the three and you get an **infinite loop** — a loop whose condition never becomes false. The program runs until you force it to stop:

```python
# DANGEROUS
counter = 1
while counter > 0:
    print(counter)
    # counter is never updated — the condition stays true forever
```

The condition `counter > 0` starts true and never changes because nothing in the body touches `counter`. Python will print `1` until the heat death of the universe, or until you press Ctrl+C.

The fix is always the same: make sure the loop body pushes the condition toward false.

```python
counter = 1
while counter > 0:
    print(counter)
    counter -= 1
print("Done")
```

Now: counter is 1, print 1, decrement to 0. Check: `0 > 0` is false. Exit. Print "Done."

<!-- → [INFOGRAPHIC: Three-panel diagram of the while loop cycle — (1) Check condition → if false, exit; if true, continue; (2) Execute loop body; (3) Return to step 1. Arrows make the cycle explicit. Annotate which part of the savings example maps to each panel.] -->

The step size — how much the counter changes per iteration — determines the rhythm. Counting up by ones is the default. But you can count by twos, by tens, or downward:

```python
n = 10
while n > 0:
    print(n)
    n -= 2
# Output: 10 8 6 4 2
```

The most common mistake at this stage is the **off-by-one error**: writing `while n < 10` when you meant `while n <= 10`, and getting 9 iterations instead of 10. Always trace through the first and last iteration manually when the boundary matters.

---

## The for loop: iteration over sequences

The `while` loop is condition-controlled — it runs until something becomes false. The `for` loop is sequence-controlled — it runs exactly once for each element in a sequence. You don't manage a counter or a termination condition. You just say: "Do this for every item in that collection."

```python
for variable in sequence:
    # loop body
```

Python assigns the next element to the variable at the start of each iteration, executes the body, and moves on. When the sequence is exhausted, the loop exits.

The `range()` function generates integer sequences on demand — no list required:

```python
for i in range(5):
    print(i)
# Output: 0 1 2 3 4
```

`range(n)` generates integers from `0` to `n−1`. The end is *exclusive* — `range(5)` stops at 4, not 5. This surprises people once and then becomes second nature.

`range(start, end)` generates from `start` up to but not including `end`:

```python
for i in range(2, 6):
    print(i)
# Output: 2 3 4 5
```

`range(start, end, step)` adds a step size. The step can be negative:

```python
for i in range(10, 0, -1):
    print(i)
# Output: 10 9 8 7 6 5 4 3 2 1
```

The `for` loop works on any sequence, not just ranges. Strings are sequences of characters. Lists are sequences of objects:

```python
name = "Claude"
for letter in name:
    print(letter)
# Output: C l a u d e

students = ["Alice", "Bob", "Carol"]
for student in students:
    print(student)
# Output: Alice  Bob  Carol
```

When you need both the position and the value, `enumerate()` gives you both:

```python
students = ["Alice", "Bob", "Carol"]
for index, student in enumerate(students):
    print(f"{index}: {student}")
# Output:
# 0: Alice
# 1: Bob
# 2: Carol
```

<!-- → [TABLE: `range()` variants — three rows with columns: Call, Generates, Example output. Rows: `range(5)` / 0 to 4 / `0 1 2 3 4`; `range(2, 6)` / 2 to 5 / `2 3 4 5`; `range(0, 10, 2)` / 0 to 8 step 2 / `0 2 4 6 8`; `range(10, 0, -1)` / 10 down to 1 / `10 9 8 7 6 5 4 3 2 1`.] -->

**When to use `for` versus `while`:** if you have a sequence to iterate over, or you know the count in advance, use `for` — it's shorter and less prone to infinite loops. If you don't know how many times you'll loop (read until the user types "quit"; process until the file runs out), use `while`. The `for` loop is stricter, which makes it safer for the common case.

---

## The accumulator pattern

Most loops don't just print or examine data. They *accumulate* a result — a running total, a count of matching items, a filtered list. The pattern is always the same: start with an initial value, update it on every iteration.

**Summing a list:**

```python
numbers = [3, 7, 2, 9, 1]
total = 0

for num in numbers:
    total += num

print(f"Sum: {total}")
# Sum: 22
```

The accumulator `total` starts at zero. On each iteration, we add the current number to it. After the loop, it holds the complete sum. The initial value matters — for a sum it's zero, for a product it would be one, for building a list it's an empty list.

**Counting occurrences:**

```python
text = "hello world"
count = 0

for char in text:
    if char == 'l':
        count += 1

print(f"Letter 'l' appears {count} times")
# Letter 'l' appears 3 times
```

**Building a filtered list:**

```python
numbers = [3, 7, 2, 9, 1, 8, 4]
evens = []

for num in numbers:
    if num % 2 == 0:
        evens.append(num)

print(evens)
# [2, 8, 4]
```

The initial value of the accumulator is `[]` — an empty list. Each iteration appends a qualifying element. After the loop, the list holds exactly the values that passed the condition.

<!-- → [TABLE: Accumulator pattern variants — rows: Goal / Initial value / Update operation. Sum → 0 → `total += num`; Count → 0 → `count += 1`; Product → 1 → `product *= num`; Collect items → `[]` → `result.append(item)`. Makes the template explicit so students can adapt it.] -->

One iteration of this pattern processes one row in a spreadsheet, one vote in a tally, one transaction in a fraud check. Multiply by a million rows and the code doesn't change — only the time it takes. This is why testing on small data first is not optional. An infinite loop on a five-element list is an annoyance; on a million-row CSV, it's a crash.

---

## Loop control: break and continue

Sometimes the right move is to exit a loop early, or to skip one iteration without stopping entirely. Python gives you two control statements for this.

`break` exits the loop immediately, regardless of whether the condition is still true:

```python
for i in range(10):
    if i == 5:
        break
    print(i)
# Output: 0 1 2 3 4
```

The loop was set up to run 10 times. It ran 5 times and stopped when `i` reached 5. Everything after the `break` in that iteration was skipped, and the loop exited entirely.

`continue` skips the rest of the current iteration and moves immediately to the next one:

```python
for i in range(5):
    if i == 2:
        continue
    print(i)
# Output: 0 1 3 4
```

`2` is absent. When `i` was 2, `continue` jumped past the `print()` call and went straight to the next iteration. The loop kept running — it just skipped that step.

`break` is especially useful in a search: loop through candidates until you find what you're looking for, then stop. There's no reason to keep checking after the answer is found.

```python
numbers = [4, 7, 2, 11, 3, 16, 9]
target = None

for num in numbers:
    if num > 10:
        target = num
        break

if target is not None:
    print(f"First number over 10: {target}")
else:
    print("None found")
# First number over 10: 11
```

---

## Putting it together

FizzBuzz is an old programmer's interview exercise, famous for being simple and surprisingly clarifying:

Print the numbers from 1 to 30. For multiples of 3, print "Fizz" instead. For multiples of 5, print "Buzz". For multiples of both, print "FizzBuzz".

```python
for n in range(1, 31):
    if n % 3 == 0 and n % 5 == 0:
        print("FizzBuzz")
    elif n % 3 == 0:
        print("Fizz")
    elif n % 5 == 0:
        print("Buzz")
    else:
        print(n)
```

Every tool from this chapter appears: a `for` loop over a range, a condition checked on each iteration, branches that select the output. The order of the conditions matters — check the combined case first, then the individual cases. If you check `n % 3 == 0` before the combined case, "FizzBuzz" never prints.

This is the design discipline that loops require: not just "will this run?" but "will it run in the right order?"

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

## AI Wayback Machine

**John Backus** led the team that built FORTRAN in 1957 — bringing structured loops to programming for the first time.

**Run this:**

```
Who is John Backus, and how does their work connect to loops we covered in this chapter? Keep it to three paragraphs. End with the single most surprising thing about their career or ideas.
```

→ Search **"John Backus"** on Wikipedia.

**Now make the prompt better.** Try one of these:

- Ask it to apply John Backus's ideas to a small Python program you'd write.
- Add a constraint: "Answer including criticisms or limits of John Backus's framework."

What changes? What gets better? What gets worse?

---

## Exercises

**Warm-up**

1. Write a `while` loop that counts from 10 down to 1 and prints each number. Verify it prints exactly ten numbers.

2. Write a `for` loop using `range()` that prints every third number from 0 to 20 — that is, 0, 3, 6, 9, ..., 18.

3. Using a `for` loop and an accumulator, calculate the sum of the integers 1 through 100. (`range(1, 101)` gives you 1 through 100.)

**Application**

4. You have a list of daily temperatures: `[72, 68, 75, 71, 69, 74, 76]`. Calculate the average temperature using a `while` loop, an accumulator for the sum, and a counter for the number of elements.

5. Write a program that iterates through the string `"programming"` and counts how many vowels (a, e, i, o, u) appear. Use an accumulator and a `for` loop.

6. Given the list `[2, 3, 5, 7, 11, 12, 13]`, use a loop with `break` to find and print the first even number greater than 10. If no such number exists, print a message saying so.

**Synthesis**

7. **FizzBuzz.** Using a `for` loop, print numbers from 1 to 30. Replace multiples of 3 with "Fizz", multiples of 5 with "Buzz", and multiples of both with "FizzBuzz". Check the combined case first.

8. **Prime finder.** Write a program that finds all prime numbers less than 50. A prime is only divisible by 1 and itself. For each candidate, use a nested loop to check divisors and `break` as soon as one is found. Collect the primes in a list using the accumulator pattern.

**Challenge**

9. Without using Python's built-in `sum()`, write a program that computes the sum of all integers from 1 to N where N is provided by the user. Then compute the same sum using the closed-form formula N × (N + 1) / 2 and compare the two results. They should match. If they don't, you have a loop bug.
