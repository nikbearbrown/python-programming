# Chapter 9 — Lists


## TL;DR

- You are at a grocery store with a shopping list written on a piece of paper.
- You will practice Create a list, access elements by index and slice, understand why Python indexes from 0; Mutate a list using append(), remove(), pop(), insert(), and index assignment; Iterate over a list with for loops and understand the difference between iterating elements and iterating indices.
- The chapter moves through Cold open, Learning objectives, Prerequisites, Why this chapter matters, and related ideas.

## Cold open

You are at a grocery store with a shopping list written on a piece of paper. The list reads: milk, bread, eggs, cheese, tomatoes. As you move through the store you cross off each item as you place it in your cart — milk (check), bread (check), eggs (check). Halfway through, you realize you forgot to add coffee to the list, so you write it in. At the register, you regret the tomatoes (they looked better an hour ago), so you cross that out. The list is *mutable* — it changes. It is *ordered* — milk comes before bread, and that matters because you don't want the eggs crushed under the milk. Each item has a position — the first item is milk, the third is eggs. You can add, remove, and rearrange items on the fly.

This is what a Python list does. Unlike a string (which is locked once created), a list is a container you can open, modify, sort, filter, and rebuild. A list is the workhorse data structure for most programs that actually do something with data.

### Learning objectives

By the end of this chapter you will be able to:

- **Create** a list, access elements by index and slice, understand why Python indexes from 0.
- **Mutate** a list using `append()`, `remove()`, `pop()`, `insert()`, and index assignment.
- **Iterate** over a list with `for` loops and understand the difference between iterating elements and iterating indices.
- **Analyze** lists using `max()`, `min()`, `sum()`, and understand when each is useful.
- **Transform** a list using `sort()`, `reverse()`, and `copy()`, and recognize when you need a copy vs. when aliasing matters.
- **Generate** new lists using list comprehensions and filtering, and understand the syntax from inside out.
- **Debug** the most common list errors: `IndexError`, type mismatches, and aliasing bugs.

### Prerequisites

Chapter 3 (Objects) introduced lists briefly and showed indexing. Chapter 5 (Loops) taught `for` loops. This chapter builds on both. If you've written `my_list = [1, 2, 3]` and printed `my_list[0]`, you're ready.

### Why this chapter matters

Every data science, analytics, or systems program spends most of its time working with lists. Databases return data as lists. File I/O reads lines into lists. Functions return multiple values as lists. The patterns you learn here—indexing, iteration, filtering—repeat in every language. Lists are also the stepping stone to dictionaries (Chapter 10), which are even more powerful for real-world data.

---

## Concept 1 — Creating lists, indexing, and the three-part Claude pattern

A **list**, from Old French *liste* meaning a strip or roll on which items are written, is Python's mutable, ordered sequence. You create one with square brackets and commas:

```python
students = ["Alice", "Bob", "Charlie", "Diana"]
temperatures = [72, 68, 71, 75, 69]
mixed = [42, "hello", 3.14, True]
```

A list can hold any type of element, and the types don't need to match. Order is preserved: the first element stays first until you move it.

**Index**, from Latin *index* meaning a pointer or sign, is the position number of an element. Python uses **zero-based indexing** — the first element is at index 0, the second at index 1, and so on. This trips every student up, but it pays off everywhere once you accept it:

```python
students = ["Alice", "Bob", "Charlie", "Diana"]
print(students[0])  # Alice (first element)
print(students[1])  # Bob
print(students[3])  # Diana (fourth element)
```

Negative indices count backward from the end. Index `-1` is the last element, `-2` is second-to-last:

```python
print(students[-1])  # Diana
print(students[-2])  # Charlie
```

**Slicing** extracts a range. The syntax is `list[start:end]`, and it includes `start` but excludes `end`:

```python
print(students[1:3])   # ['Bob', 'Charlie'] — indices 1 and 2, not 3
print(students[0:2])   # ['Alice', 'Bob']
print(students[2:])    # ['Charlie', 'Diana'] — from index 2 to the end
print(students[:2])    # ['Alice', 'Bob'] — from the start up to (not including) index 2
```

### The three-part pattern: code, prompt, tests

This book teaches you to use Claude as a thinking partner, not a shortcut. For every substantial piece of code, we show three things: the code itself, a clean prompt you could send to an LLM, and tests that prove the code works. You are learning to *specify problems precisely* and *verify solutions rigorously* — both are more valuable than memorizing syntax.

Here's the pattern in action. Suppose you want to write code that takes a class roster and prints the first three students.

```python
students = ["Alice", "Bob", "Charlie", "Diana", "Eve"]
first_three = students[0:3]
print(first_three)
```

> **Prompt for Claude.** "Write Python code that creates a list called `students` with five names: Alice, Bob, Charlie, Diana, and Eve. Extract the first three students into a new list called `first_three` using slicing, and print the result."

> **Tests to validate.**
> - `first_three == ["Alice", "Bob", "Charlie"]` — correct slice, correct order, correct count
> - `len(first_three) == 3` — we got exactly three students, not more or fewer
> - `first_three[0] == "Alice"` — the first element in the slice is Alice, not someone else

When you write the prompt, you're teaching yourself what the code needs to do. When you write the tests, you're catching bugs before they catch you. The code is the third thing because by then both the what and the how should be clear.

### Common pitfall: the off-by-one error

Index confusion causes most beginner mistakes. Remember: Python counts from 0, and slicing excludes the end index.

```python
roster = ["Alex", "Bailey", "Casey", "Dana"]
# NOT roster[0:4] even though there are 4 students — [0:4] means indices 0, 1, 2, 3.
# DO use roster[0:4] to get all four students, OR roster[0:3] if you want only the first three.
print(roster[0:4])  # ['Alex', 'Bailey', 'Casey', 'Dana'] — all four
print(roster[0:3])  # ['Alex', 'Bailey', 'Casey'] — first three
```

### Worked example — class roster extraction

Write code that extracts and prints the middle two students from a roster of four.

*Given:* `roster = ["Maya", "Jonas", "Priya", "Leo"]`. We want indices 1 and 2 (the second and third students, Jonas and Priya).

*Solution:*
```python
roster = ["Maya", "Jonas", "Priya", "Leo"]
middle_two = roster[1:3]
print(middle_two)
```

*Output:* `['Jonas', 'Priya']`

*Sanity check:* We used `[1:3]`, which includes index 1 and 2 (excludes 3). That's the second and third elements of a four-element list. Correct.

---

## Concept 2 — Mutating lists: adding, removing, and modifying

A string is **immutable** — once created, it cannot be changed. You can create a new string, but the original stays locked. A list is **mutable** — you can add, remove, and replace elements without creating a new list.

### The basic mutations

**`append(element)`** adds an element to the end:

```python
students = ["Alice", "Bob"]
students.append("Charlie")
print(students)  # ['Alice', 'Bob', 'Charlie']
```

**`remove(element)`** removes the first occurrence of an element by *value*, not by index. If the element is not in the list, Python raises a `ValueError`:

```python
students = ["Alice", "Bob", "Charlie"]
students.remove("Bob")
print(students)  # ['Alice', 'Charlie']
students.remove("Diana")  # ValueError: list.remove(x): x not in list
```

**`pop()`** removes and returns the last element. You can also pass an index to remove a specific position:

```python
students = ["Alice", "Bob", "Charlie"]
last = students.pop()
print(last)      # Charlie
print(students)  # ['Alice', 'Bob']
```

**`insert(index, element)`** adds an element at a specific index, shifting everything else to the right:

```python
students = ["Alice", "Bob", "Diana"]
students.insert(2, "Charlie")
print(students)  # ['Alice', 'Bob', 'Charlie', 'Diana']
```

**Index assignment** changes an element in place:

```python
students = ["Alice", "Bob", "Charlie"]
students[1] = "Beth"
print(students)  # ['Alice', 'Beth', 'Charlie']
```

### The gotcha: aliasing vs. copying

This is the most dangerous bug students encounter with lists. When you assign one list to another variable, you do not get a separate copy — you get an *alias*, a second name pointing to the same list in memory:

```python
original = [1, 2, 3]
alias = original
alias.append(4)
print(original)  # [1, 2, 3, 4] — the original changed too!
```

Both `original` and `alias` point to the same list. When you modify through `alias`, you modify the shared list. If you need a separate copy that can be modified without affecting the original, use the `.copy()` method:

```python
original = [1, 2, 3]
copy_list = original.copy()
copy_list.append(4)
print(original)   # [1, 2, 3] — unchanged
print(copy_list)  # [1, 2, 3, 4]
```

### The three-part pattern: a todo-list mutator

```python
todos = ["laundry", "groceries", "dishes"]
todos.append("study")
todos.remove("laundry")
todos[0] = "vacuum"
print(todos)
```

> **Prompt for Claude.** "Write Python code that creates a list called `todos` with three items: 'laundry', 'groceries', 'dishes'. Add 'study' to the end using append(). Remove 'laundry' using remove(). Replace the first remaining item (at index 0) with 'vacuum'. Print the final list."

> **Tests to validate.**
> - `len(todos) == 3` — we added one and removed one, so 3 total
> - `"study" in todos` — 'study' was added and never removed
> - `"laundry" not in todos` — 'laundry' was removed
> - `todos[0] == "vacuum"` — the first item was replaced

### Common pitfall: `IndexError`

```python
students = ["Alice", "Bob"]
print(students[5])  # IndexError: list index out of range
```

The list has only 2 elements (indices 0 and 1), so index 5 does not exist. Always check: are you asking for an index that is too large or too small? Use `len(list)` to check how many elements are in the list:

```python
students = ["Alice", "Bob"]
print(len(students))  # 2 — valid indices are 0 and 1
```

### Worked example — updating a temperature log

You are tracking daily temperatures. You have recorded [72, 68, 71, 75]. You realize day 2 was actually 70, not 68. You also want to add today's reading, 73, to the end.

*Solution:*
```python
temps = [72, 68, 71, 75]
temps[1] = 70  # Fix day 2
temps.append(73)  # Add today
print(temps)  # [72, 70, 71, 75, 73]
```

---

## Concept 3 — Iterating, sorting, and transforming lists

Once you have a list with data, you need to work with every element. The three tools are: iteration (process each element), sorting (put elements in order), and transformation (create a new list based on the old one).

### Iterating with `for` loops

The cleanest way to iterate a list is element iteration — the `for` loop variable becomes each element in turn:

```python
students = ["Alice", "Bob", "Charlie"]
for student in students:
    print(student)
```

Output:
```
Alice
Bob
Charlie
```

This is cleaner than index iteration (using `range(len(students))`), which you need only when you actually need the index number:

```python
students = ["Alice", "Bob", "Charlie"]
for i in range(len(students)):
    print(f"Position {i}: {students[i]}")
```

Output:
```
Position 0: Alice
Position 1: Bob
Position 2: Charlie
```

Use element iteration by default. Reach for index iteration only when the index itself matters.

### Sorting and reversing

**`sort()`** arranges elements in ascending order. For numbers, it's numeric order. For strings, it's alphabetical (dictionary order), with uppercase letters coming before lowercase:

```python
scores = [92, 45, 78, 56]
scores.sort()
print(scores)  # [45, 56, 78, 92]

words = ["zebra", "apple", "Zebra", "Apple"]
words.sort()
print(words)   # ['Apple', 'Zebra', 'apple', 'zebra']
```

Notice that `sort()` modifies the list in place — it does not create a new list. If you need the original unchanged, copy first:

```python
original = [92, 45, 78]
sorted_copy = original.copy()
sorted_copy.sort()
print(original)     # [92, 45, 78] — unchanged
print(sorted_copy)  # [45, 78, 92]
```

**`reverse()`** flips the order of elements:

```python
items = ["first", "second", "third"]
items.reverse()
print(items)  # ['third', 'second', 'first']
```

### Built-in functions: `max()`, `min()`, `sum()`

These three functions work on lists of comparable elements:

```python
scores = [92, 45, 78, 56, 88]
print(max(scores))   # 92
print(min(scores))   # 45
print(sum(scores))   # 359

cities = ["Boston", "Austin", "Denver"]
print(max(cities))   # Denver (alphabetically last)
print(min(cities))   # Austin (alphabetically first)
```

`sum()` works only on numbers. `max()` and `min()` work on any comparable type.

### The three-part pattern: processing a daily temperature log

```python
temps = [72, 68, 71, 75, 69]
print(f"High: {max(temps)}, Low: {min(temps)}, Average: {sum(temps) / len(temps)}")
```

> **Prompt for Claude.** "Write Python code that creates a list of temperatures: 72, 68, 71, 75, 69. Use max(), min(), and sum() to compute the highest, lowest, and average temperatures, and print them in a readable sentence."

> **Tests to validate.**
> - `max(temps) == 75` — the highest temperature is 75
> - `min(temps) == 68` — the lowest is 68
> - `sum(temps) == 355` — the sum is 355
> - `sum(temps) / len(temps) == 71.0` — the average is 71

### Worked example — sports league standings

You have a list of teams and their scores: `["Lakers", "Celtics", "Warriors", "Heat"]` and `[110, 102, 115, 98]`. You want to print the teams in order of highest to lowest score.

*Strategy:* Create a list of tuples pairing team names with scores, sort by score, and print. (We'll cover tuples properly in Chapter 10. For now, a tuple is an immutable list.)

*Solution:*
```python
teams = ["Lakers", "Celtics", "Warriors", "Heat"]
scores = [110, 102, 115, 98]
standings = list(zip(teams, scores))
standings.sort(key=lambda x: x[1], reverse=True)
for team, score in standings:
    print(f"{team}: {score}")
```

Output:
```
Warriors: 115
Lakers: 110
Celtics: 102
Heat: 98
```

(The `zip()` function pairs elements, `lambda x: x[1]` sorts by the second element, `reverse=True` puts highest first.)

---

## Concept 4 — List comprehensions and filtering

**Comprehension**, from Latin *com* (together) and *prehendere* (to grasp), means to hold together or to grasp as a unit. A **list comprehension** is Python's syntax for grasping a list together — building a new list from an old one in a single line.

### The basic pattern

```python
squares = [x * x for x in range(5)]
print(squares)  # [0, 1, 4, 9, 16]
```

This is equivalent to:

```python
squares = []
for x in range(5):
    squares.append(x * x)
print(squares)  # [0, 1, 4, 9, 16]
```

But it's more concise. The general form is:

```
[expression for variable in iterable]
```

Read it left to right:
- *expression*: what goes into the new list
- *variable*: the loop variable
- *iterable*: what you're iterating over

### Filtering with conditions

Add `if condition` to include only elements that pass the test:

```python
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9]
evens = [n for n in numbers if n % 2 == 0]
print(evens)  # [2, 4, 6, 8]
```

You can build on an existing list:

```python
words = ["cat", "catastrophe", "car", "cart", "dog"]
long_words = [w for w in words if len(w) > 3]
print(long_words)  # ['catastrophe', 'cart']
```

You can transform while filtering:

```python
words = ["apple", "ant", "avocado"]
a_words_upper = [w.upper() for w in words if w.startswith("a")]
print(a_words_upper)  # ['APPLE', 'ANT', 'AVOCADO']
```

### The three-part pattern: filtering a dataset

```python
data = [10, 25, 3, 42, 8, 55, 12, 99]
high_values = [x for x in data if x > 20]
print(high_values)
```

> **Prompt for Claude.** "Write Python code that creates a list of numbers: 10, 25, 3, 42, 8, 55, 12, 99. Use a list comprehension with an if condition to extract only values greater than 20, and store them in a variable called `high_values`. Print the result."

> **Tests to validate.**
> - `high_values == [25, 42, 55, 99]` — exactly the four values greater than 20
> - `len(high_values) == 4` — exactly four values pass the filter
> - All elements in `high_values` satisfy `x > 20` — no invalid elements slipped in
> - The order matches the original list — filtering preserves order

### Trade-off: comprehension vs. explicit loop

Comprehensions are elegant and Pythonic. Explicit loops are easier to debug. A beginner should prefer explicit loops until comprehensions are muscle memory:

```python
# Comprehension — concise, harder to debug
evens = [n for n in numbers if n % 2 == 0]

# Explicit loop — verbose, easier to debug
evens = []
for n in numbers:
    if n % 2 == 0:
        evens.append(n)
```

Both produce the same result. As you grow as a programmer, you'll reach for comprehensions more often. Start with what you understand.

### Worked example — filtering student grades

You have a list of student grades and want to extract passing grades (70 or above):

*Given:* `grades = [85, 92, 68, 78, 55, 91, 62, 88]`

*Solution (explicit loop):*
```python
grades = [85, 92, 68, 78, 55, 91, 62, 88]
passing = []
for grade in grades:
    if grade >= 70:
        passing.append(grade)
print(passing)  # [85, 92, 78, 91, 88]
```

*Solution (comprehension):*
```python
grades = [85, 92, 68, 78, 55, 91, 62, 88]
passing = [g for g in grades if g >= 70]
print(passing)  # [85, 92, 78, 91, 88]
```

Both work. The explicit version is easier to understand. The comprehension is more elegant once you've written a few.

---

## Nested lists (brief treatment)

Lists can contain other lists. This is useful for representing table-like data:

```python
matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]
print(matrix[0])      # [1, 2, 3] — first row
print(matrix[1][2])   # 6 — row 1, column 2
```

Iterate with nested loops:

```python
for row in matrix:
    for element in row:
        print(element, end=" ")
    print()
```

Output:
```
1 2 3 
4 5 6 
7 8 9 
```

Nested lists are practical but belong properly in Chapter 10 when we cover dictionaries (which are more natural for most real data). For now, know they exist and how to index them.

---

## Integration and synthesis

You now have the machinery. Let me show you how these pieces fit together in a real scenario.

Suppose you're managing an ER waiting room. Patients arrive, get triaged, wait, and are called in order. Your code tracks their order in the list:

```python
# Patients arrive
queue = []
queue.append("Patient A")
queue.append("Patient B")
queue.append("Patient C")
print(f"Queue: {queue}")

# Patient A is seen, removed from queue
first = queue.pop(0)  # Remove from front
print(f"{first} is being treated. Remaining: {queue}")

# New patient arrives
queue.append("Patient D")
print(f"Queue: {queue}")

# List everyone still waiting
print("Still waiting:")
for patient in queue:
    print(f"  {patient}")
```

Output:
```
Queue: ['Patient A', 'Patient B', 'Patient C']
Patient A is being treated. Remaining: ['Patient B', 'Patient C']
Queue: ['Patient B', 'Patient C', 'Patient D']
Still waiting:
  Patient B
  Patient C
  Patient D
```

This is the pattern. Build a list. Modify it as events happen. Iterate to process or report. Nothing else in this book will feel simpler or more powerful.

---

## Graduated exercises

### Warm-up

1. Create a list of five of your favorite foods. Print the first and last items using positive and negative indexing.
2. Create a list of numbers from 1 to 5. Use `append()` to add 6, then use `remove()` to remove 3. Print the final list.
3. Given `words = ["apple", "banana", "cherry"]`, extract and print the middle word using a slice.

### Application

4. Write a program that creates a list of ten integers. Use a `for` loop to print each value multiplied by 2.
5. Write a program that creates a list of temperatures (in Celsius). Find and print the highest, lowest, and average temperatures.
6. Write a program that takes a list of words and creates a new list with only words that have more than 3 letters, using a list comprehension.

### Synthesis

7. A grocery list is stored as `["milk", "bread", "eggs", "cheese", "butter"]`. You want to rearrange it in alphabetical order, remove "butter" (you already bought it), and add "yogurt". Write code to do this and print the result.
8. Write a program that reads a list of test scores and prints a summary: the highest score, the lowest score, the average, and how many scores are above the class average. Use `max()`, `min()`, `sum()`, and a list comprehension.

### Challenge

9. Write a program that creates two lists: one of team names and one of their scores. Combine them (using `zip()`), sort by score in descending order, and print a ranked table.
10. Given a list of numbers, use list comprehensions to create three new lists: one with only even numbers, one with only numbers greater than 50, and one with the squares of all numbers. Print all three.

---

## Claude Code — temperature analysis with pytest

### Orientation

The code-learning pattern runs throughout this book. You specify a problem precisely, you write a clean prompt for Claude or Claude Code, the tool produces code, you write tests to prove it works, and you run the tests yourself. This section walks through all five list concepts in one project.

Your task: analyze a week of daily high temperatures. You'll write four functions that exercise indexing, mutation, iteration, and comprehensions. You'll also confront the aliasing pitfall head-on and prove you've fixed it with tests.

### The setup

Create a file `temps.py` with this data structure:

```python
daily_highs = [72, 68, 75, 80, 85, 88, 91]
```

This is a week of temperatures (Monday through Sunday). Now you'll write four functions and test each one.

### What to expect

1. **`hottest_day_index(temps)`** — Return the index of the highest temperature, not the temperature itself. This exercises `max()` combined with indexing. (Hint: `list.index()` finds the index of an element by value.)

2. **`average(temps)`** — Return the average temperature as a float. This exercises `sum()`, `len()`, and arithmetic.

3. **`days_above(temps, threshold)`** — Return a *new list of indices* of days where the temperature exceeded the threshold, using a list comprehension. This is the key comprehension exercise.

4. **`cool_off(temps, drop)`** — Return a *new list* with each temperature reduced by `drop` degrees. Do not mutate the original. This forces you to confront aliasing and `.copy()`.

### The prompt

Here is a clean prompt you could send to Claude Code:

> "Create a file `temps.py` with the following:
> - A list `daily_highs = [72, 68, 75, 80, 85, 88, 91]` (one week of daily high temperatures)
> - A function `hottest_day_index(temps)` that returns the index of the highest temperature
> - A function `average(temps)` that returns the average temperature as a float
> - A function `days_above(temps, threshold)` that returns a list of indices where the temperature exceeds the threshold, using a list comprehension
> - A function `cool_off(temps, drop)` that returns a NEW list with each temperature reduced by drop degrees (without mutating the original)
> 
> Then create `test_temps.py` with pytest tests covering:
> - `hottest_day_index` returns 6 (index of 91)
> - `average` returns 82.71... (approximately)
> - `days_above(daily_highs, 80)` returns [3, 4, 5, 6] (indices where temp >= 80)
> - `cool_off(daily_highs, 5)` returns [67, 63, 70, 75, 80, 83, 86] but the original list remains unchanged
> 
> Run pytest and show me the output."

### The code (what Claude Code produces)

You'll get something like this:

```python
# temps.py
daily_highs = [72, 68, 75, 80, 85, 88, 91]

def hottest_day_index(temps):
    return temps.index(max(temps))

def average(temps):
    return sum(temps) / len(temps)

def days_above(temps, threshold):
    return [i for i, temp in enumerate(temps) if temp > threshold]

def cool_off(temps, drop):
    return [temp - drop for temp in temps]
```

Notice the key line: `cool_off` uses a list comprehension to build a new list, leaving the original untouched. If you had written `temps[i] -= drop` in a loop, you'd mutate the original. If you had written `new_temps = temps`, you'd create an alias. The comprehension avoids both traps.

Also notice `enumerate()` in `days_above`. The `enumerate()` function gives you both the index and the element in a single loop, which is cleaner than `range(len(temps))`.

### The tests (what you write)

```python
# test_temps.py
import pytest
from temps import hottest_day_index, average, days_above, cool_off, daily_highs

def test_hottest_day_index():
    assert hottest_day_index(daily_highs) == 6
    assert daily_highs[6] == 91

def test_average():
    result = average(daily_highs)
    assert abs(result - 82.714285) < 0.001

def test_days_above():
    result = days_above(daily_highs, 80)
    assert result == [3, 4, 5, 6]
    assert len(result) == 4

def test_cool_off_returns_new_list():
    original = daily_highs.copy()
    cooled = cool_off(daily_highs, 5)
    assert cooled == [67, 63, 70, 75, 80, 83, 86]
    assert daily_highs == original  # Original unchanged
    assert cooled is not daily_highs  # Different objects in memory
```

The last test is the crucial one: `cool_off` must return a new list and leave the original unchanged. The `is not` check confirms they are different objects. Run `pytest test_temps.py -v` and you'll see all four tests pass.

### The aliasing pitfall (in plain sight)

If `cool_off` had been written as:

```python
def cool_off(temps, drop):
    new_temps = temps  # WRONG: creates an alias
    for i in range(len(new_temps)):
        new_temps[i] -= drop
    return new_temps
```

Then `test_cool_off_returns_new_list` would fail on the line `assert daily_highs == original`, because `new_temps` and `daily_highs` point to the same list in memory. The "new" list would have modified the original. The correct fix is the list comprehension (or `.copy()` followed by mutation).

### Stretch

Once these four functions pass, extend the project:

- Write a function `warmest_week(temps_list_of_weeks)` that takes a list of lists (each inner list is one week of temperatures) and returns the index of the week with the highest average. This exercises nested lists and multiple iterations.

- Write a function `trend(temps)` that returns `"warming"` if each day is hotter than the previous, `"cooling"` if each day is cooler, and `"mixed"` otherwise. This exercises iteration with index comparisons.

- Write a function `normalize(temps, new_max)` that scales all temperatures so the maximum becomes `new_max`. For example, `normalize([72, 91], 100)` should return approximately `[79.1, 100]`. This exercises list comprehensions with arithmetic.

Test each extension function before moving on. By the time you've written these, you'll have muscle memory for lists.

---

## Chapter summary

A **list** is a mutable, ordered collection. You create it with square brackets: `[item1, item2, item3]`. Elements are accessed by **index** — starting from 0 for the first element, and you can use negative indices to count from the end. Slice with `list[start:end]` to extract ranges.

**Mutations** change a list in place: `append()` adds to the end, `remove()` removes by value, `pop()` removes from the end (or a specific index), `insert()` adds at a position, and index assignment `list[i] = value` replaces a specific element. Be careful: `alias = original` does not create a copy; both names point to the same list. Use `.copy()` for a separate list.

**Iteration** visits each element: `for item in list:` iterates by value, `for i in range(len(list)):` iterates by index. Use the first unless the index matters.

**Sorting** uses `list.sort()` (ascending order) or `list.reverse()` (flip order). Both modify the list in place.

**Functions** on lists: `max()` and `min()` find the largest and smallest, `sum()` adds all numbers, `len()` counts elements.

**List comprehensions** build new lists in one line: `[expression for var in iterable]`. Add an `if condition` to filter: `[x for x in list if condition]`.

**Errors**: `IndexError` means you asked for an index that does not exist. Check with `len()` first. `ValueError` means `remove()` could not find the value.

The core trade-off is **mutability vs. immutability**. Strings are immutable (locked once created), lists are mutable (open for change). This makes lists slower for some operations but far more flexible for real work.

### What would change my mind

If list performance became critical for your use case, you might reach for NumPy arrays (Chapter 15), which are faster for mathematical operations. But for most Python code, lists are the right tool.

### Still puzzling

I find list comprehensions feel magical until you've written your tenth one. Then the pattern clicks and becomes natural. If they don't click immediately, write explicit loops and revisit comprehensions in a week.

---

**Tags:** lists, mutability, iteration, Python data structures, indexing, comprehensions, Attenborough × Feynman, code-learning pattern

**Author:** Nik Bear Brown

**Date:** 2026-05-07

---

## Companion files structure

**filename:** `2026-05-07-lists-examples.py` — all code examples from the chapter, runnable
**filename:** `2026-05-07-lists-exercises.md` — expanded exercise set with solutions
**filename:** `2026-05-07-lists-errors.md` — common errors, diagnosis, and fixes
---

## LLM Exercise — Chapter 09: Lists (Text Adventure Project)

**Project:** Text Adventure.
**What you're building this chapter:** the inventory system + per-room items as Python lists.
**Tool:** **Claude Code**.

---

**The Prompt:**

```
Chapter 9 of my Text Adventure project. Chapter 9 covered: list
literals; indexing and slicing; mutation methods (append, extend,
pop, insert, remove); iteration; list comprehensions; in operator.

Build the inventory and per-room item systems.

1. **Player inventory.** A list called `player_inventory`.
   Operations:
   - `add_to_inventory(item)` — appends to the list.
   - `remove_from_inventory(item)` — uses `.remove()`. Handle the
     ValueError if item isn't there (with a friendly message).
   - `has_item(item)` — returns True if `item in inventory`.
   - `print_inventory()` — uses iteration to print each item;
     handles the empty-inventory case.

2. **Per-room items.** Each room has a list of items currently
   in it. Use a list of lists for now (or update your room
   structures from Ch 7):
   ```
   room_items = {
       "tavern": ["mug of ale", "torn map"],
       "dungeon": ["rusty sword", "skeleton key"],
       ...
   }
   ```
   Wait — that's a dictionary. Ch 10 covers those. For now, use
   a parallel list:
   ```
   room_names = ["tavern", "dungeon", "forest"]
   room_items = [["mug of ale", "torn map"], ["rusty sword"], []]
   ```
   It's clunky; Ch 10 fixes it.

3. **The "take" command.** When player types "take [item]":
   - Look up the items in the current room.
   - If [item] is in the list: remove from room, add to inventory,
     print confirmation.
   - If not: print "There's no [item] here."

4. **The "drop" command.** Reverse: remove from inventory, add to
   room items.

5. **A list comprehension somewhere.** Use one to:
   - Build a list of items the player can afford to buy.
   - Or build a list of room exits as uppercase letters for the
     prompt display.

6. **Iterate to print.** Use a `for` loop over the inventory to
   print it line by line.

In addition to the code, produce the prompt + tests. Tests:
- Player starts with empty inventory.
- "take torch" in a room with a torch: torch in inventory, not
  in room.
- "take dragon" in same room: error message, inventory unchanged.
- "drop torch" returns it to the room.

End with: at this point, you have parallel lists for room data
(names and items). What's awkward about parallel lists? (Hint:
they can get out of sync.) This sets up Ch 10's dictionaries.
```

---

**What this produces:** Working inventory system + take/drop commands.

**Connection to previous chapters:** Ch 8's parser + Ch 9's data structures = the inventory commands work end-to-end.

**Preview of next chapter:** Chapter 10 covers dictionaries. You'll refactor the parallel-list disaster into a clean dict-based world. Every room becomes a dict with name, description, exits, items.


---

##  AI Wayback Machine
**John McCarthy** was invented LISP in 1958 — the language built around lists, which influences how every modern language handles collections.

**Run this:**

```
Who is John McCarthy, and how does their work connect to lists we covered in this chapter? Keep it to three paragraphs. End with the single most surprising thing about their career or ideas.
```

→ Search **"John McCarthy"** on Wikipedia.

**Now make the prompt better.** Try one of these:

- Ask it to apply John McCarthy's ideas to a small Python program you'd write.
- Add a constraint: "Answer including criticisms or limits of John McCarthy's framework."

What changes? What gets better? What gets worse?
