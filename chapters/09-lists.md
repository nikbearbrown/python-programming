# Chapter 9 — Lists
*The data structure that actually does work.*

You are at a grocery store with a list written on a piece of paper. Milk, bread, eggs, cheese, tomatoes. As you move through the store you cross off each item — milk (done), bread (done), eggs (done). Halfway through you realize you forgot coffee, so you write it in at the bottom. At the register you regret the tomatoes — they looked better an hour ago — so you cross them off too.

This list is *mutable*: it changes as events unfold. It is *ordered*: milk comes before bread, and that matters because you don't want eggs crushed under the milk jug. Each item has a position. You can add, remove, and rearrange items on the fly.

This is what a Python list does. Unlike a string, which is locked the moment you create it, a list is a container you can open, modify, sort, filter, and rebuild. Lists are the workhorse of most programs that actually do something with data.

---

## Creating lists and reaching inside them

A *list* — from Old French *liste*, a strip or roll on which items are written — is Python's mutable, ordered sequence. You create one with square brackets:

```python
students = ["Alice", "Bob", "Charlie", "Diana"]
temperatures = [72, 68, 71, 75, 69]
mixed = [42, "hello", 3.14, True]
```

A list can hold any type, and the types don't need to match. Order is preserved: the first element stays first until you move it.

**Index** — from Latin *index*, a pointer or sign — is the position number of an element. Python uses zero-based indexing: the first element is at position 0, the second at position 1. This trips people up initially, but it becomes natural:

```python
students = ["Alice", "Bob", "Charlie", "Diana"]
print(students[0])    # Alice
print(students[1])    # Bob
print(students[3])    # Diana
```

Negative indices count backward from the end. `-1` is the last element, `-2` is second-to-last:

```python
print(students[-1])   # Diana
print(students[-2])   # Charlie
```

**Slicing** extracts a range. The syntax is `list[start:end]` — it includes `start` but excludes `end`:

```python
print(students[1:3])  # ['Bob', 'Charlie'] — indices 1 and 2, not 3
print(students[2:])   # ['Charlie', 'Diana'] — index 2 to the end
print(students[:2])   # ['Alice', 'Bob'] — from the start up to (not including) index 2
```

The exclusive end is consistent across Python — `range(5)` gives 0 through 4, not through 5. You learn the pattern once and it appears everywhere.

`len()` returns the number of elements:

```python
print(len(students))  # 4
```

The valid indices for a list of length 4 are 0, 1, 2, and 3. Index 4 doesn't exist. If you ask for it:

```python
print(students[4])    # IndexError: list index out of range
```

`IndexError` means you asked for a position the list doesn't have. When you see it, the first question to ask is whether your index is off by one.

<!-- → [INFOGRAPHIC: Visual of a four-element list showing both positive indices (0, 1, 2, 3 labeled below each element) and negative indices (-4, -3, -2, -1 labeled above). A slice arrow spanning indices 1 to 3 illustrates `[1:3]` including 1, 2 but stopping before 3. Makes zero-based indexing and slice exclusion concrete at a glance.] -->

---

## Mutating a list

Strings can't be changed after creation. Lists can. Python gives you several ways to modify a list in place.

`append(element)` adds an element to the end:

```python
students = ["Alice", "Bob"]
students.append("Charlie")
print(students)    # ['Alice', 'Bob', 'Charlie']
```

`remove(element)` removes the first occurrence of a value. If the value isn't there, Python raises a `ValueError`:

```python
students.remove("Bob")
print(students)    # ['Alice', 'Charlie']

students.remove("Diana")   # ValueError: list.remove(x): x not in list
```

`pop()` removes and returns the last element. Pass an index to remove at a specific position:

```python
students = ["Alice", "Bob", "Charlie"]
last = students.pop()
print(last)        # Charlie
print(students)    # ['Alice', 'Bob']

first = students.pop(0)
print(first)       # Alice
print(students)    # ['Bob']
```

`insert(index, element)` adds an element at a specific position, shifting everything else right:

```python
students = ["Alice", "Bob", "Diana"]
students.insert(2, "Charlie")
print(students)    # ['Alice', 'Bob', 'Charlie', 'Diana']
```

Index assignment replaces an element directly:

```python
students = ["Alice", "Bob", "Charlie"]
students[1] = "Beth"
print(students)    # ['Alice', 'Beth', 'Charlie']
```

### The aliasing trap, again

In Chapter 3, we saw that assignment doesn't copy an object — it creates a second name pointing to the same object. For lists, the consequence is sharp:

```python
original = [1, 2, 3]
alias = original
alias.append(4)
print(original)    # [1, 2, 3, 4] — the original changed too
```

Both names point to the same list. Modifying through either name modifies the shared object. If you need a genuinely separate copy:

```python
original = [1, 2, 3]
copy = original.copy()
copy.append(4)
print(original)    # [1, 2, 3] — unchanged
print(copy)        # [1, 2, 3, 4]
```

`.copy()` creates a new list with the same elements. Two objects, two identities. This pattern appears constantly — whenever you want to work on a modified version without disturbing the source.

<!-- → [TABLE: List mutation methods — five rows: Method / What it does / Raises error if. `append(x)` / adds x to end / never; `remove(x)` / removes first x / x not in list (ValueError); `pop(i)` / removes and returns element at i / index out of range (IndexError); `insert(i, x)` / inserts x before index i / never; `list[i] = x` / replaces element at i / index out of range (IndexError).] -->

---

## Iterating, sorting, and measuring

Once you have a list with data, the three basic operations are iterating (doing something with every element), sorting (putting elements in order), and measuring (finding aggregate values).

**Iteration** with a `for` loop:

```python
students = ["Alice", "Bob", "Charlie"]
for student in students:
    print(student)
```

This is element iteration — the loop variable takes on each value in turn. Use it by default. Reach for index iteration (`for i in range(len(students))`) only when the index itself matters:

```python
for i, student in enumerate(students):
    print(f"{i}: {student}")
# 0: Alice
# 1: Bob
# 2: Charlie
```

`enumerate()` gives you both index and value without the awkwardness of `range(len(...))`.

**Sorting** with `.sort()` rearranges the list in place:

```python
scores = [92, 45, 78, 56]
scores.sort()
print(scores)    # [45, 56, 78, 92]
```

In place means the original list changes. If you need the original unchanged, copy first, then sort the copy.

**Measuring** with built-in functions:

```python
scores = [92, 45, 78, 56, 88]
print(max(scores))   # 92
print(min(scores))   # 45
print(sum(scores))   # 359
print(sum(scores) / len(scores))  # 71.8
```

`sum()` requires numbers. `max()` and `min()` work on any type that supports comparison — including strings, where the ordering is alphabetical.

---

## List comprehensions

The accumulator pattern from Chapter 5 — start with an empty list, loop through values, append ones that qualify — comes up so often that Python has a one-line syntax for it.

The explicit version:

```python
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9]
evens = []
for n in numbers:
    if n % 2 == 0:
        evens.append(n)
```

The comprehension version:

```python
evens = [n for n in numbers if n % 2 == 0]
```

The general form is `[expression for variable in iterable if condition]`. Read it left to right: "give me `expression`, for each `variable` in `iterable`, but only if `condition` is true."

Transforming while filtering:

```python
grades = [85, 92, 68, 78, 55, 91, 62, 88]
passing = [g for g in grades if g >= 70]
print(passing)    # [85, 92, 78, 91, 88]
```

Building a transformed list without filtering:

```python
words = ["apple", "ant", "avocado"]
upper = [w.upper() for w in words]
print(upper)      # ['APPLE', 'ANT', 'AVOCADO']
```

<!-- → [TABLE: Comprehension pattern reference — three rows showing: goal / explicit loop version / comprehension version. Filter evens; Transform to uppercase; Filter and transform simultaneously. Side-by-side makes the equivalence concrete.] -->

Comprehensions are more concise, but the explicit loop is easier to debug when something goes wrong. A beginner should understand both — write the explicit version when you're uncertain what the output should be, switch to the comprehension once you're confident. They produce identical results.

---

## A real example: patient queue

The pieces fit together in a simple scenario. An ER waiting room tracks patients in order:

```python
queue = []
queue.append("Patient A")
queue.append("Patient B")
queue.append("Patient C")
print(f"Queue: {queue}")

first = queue.pop(0)
print(f"{first} is being treated. Remaining: {queue}")

queue.append("Patient D")
print(f"Queue: {queue}")

print("Still waiting:")
for patient in queue:
    print(f"  {patient}")
```

```
Queue: ['Patient A', 'Patient B', 'Patient C']
Patient A is being treated. Remaining: ['Patient B', 'Patient C']
Queue: ['Patient B', 'Patient C', 'Patient D']
Still waiting:
  Patient B
  Patient C
  Patient D
```

Build a list. Modify it as events unfold. Iterate to report. This is the pattern for most real list work — not elaborate sorting algorithms, but straightforward accumulation, mutation, and traversal.

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

## AI Wayback Machine

**John McCarthy** invented LISP in 1958 — the language built around lists, which influences how every modern language handles collections.

**Run this:**

```
Who is John McCarthy, and how does their work connect to lists we covered in this chapter? Keep it to three paragraphs. End with the single most surprising thing about their career or ideas.
```

→ Search **"John McCarthy"** on Wikipedia.

**Now make the prompt better.** Try one of these:

- Ask it to apply John McCarthy's ideas to a small Python program you'd write.
- Add a constraint: "Answer including criticisms or limits of John McCarthy's framework."

What changes? What gets better? What gets worse?

---

## Exercises

**Warm-up**

1. Create a list of five of your favorite foods. Print the first and last items using positive and negative indexing.

2. Create a list of the numbers 1 through 5. Use `append()` to add 6, then use `remove()` to remove 3. Print the final list.

3. Given `words = ["apple", "banana", "cherry"]`, extract and print the middle word using a slice. Then print the last two words using a negative-start slice.

**Application**

4. Write a program that creates a list of ten integers and uses a `for` loop to print each value multiplied by 2.

5. Write a program that creates a list of temperatures in Celsius. Find and print the highest, lowest, and average temperatures using `max()`, `min()`, and `sum()`.

6. Write a program that takes a list of words and uses a list comprehension to create a new list containing only words with more than three letters.

**Synthesis**

7. You have the grocery list `["milk", "bread", "eggs", "cheese", "butter"]`. Sort it alphabetically, remove "butter" (you already have it), and add "yogurt." Print the result. Then verify the original pre-sort order is gone — explain what happened and whether that's what you wanted.

8. Write a program that takes a list of test scores and prints: the highest score, the lowest score, the average, and a list of all scores above the average (using a comprehension). Make sure the above-average filter actually uses the computed average, not a hardcoded number.

**Synthesis / Challenge**

9. Write a program that creates two lists: team names and their scores. Use `zip()` to pair them, sort by score in descending order, and print a ranked table. Then use a comprehension to extract just the names of teams that scored above 100.

10. Write a function `cool_off(temps, drop)` that takes a list of temperatures and a number, and returns a *new list* with each temperature reduced by `drop` — without modifying the original. Test it by printing both the original and the cooled version, and verifying they differ only by the drop amount.
