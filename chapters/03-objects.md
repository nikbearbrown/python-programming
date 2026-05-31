# Chapter 3 — Objects


## TL;DR

- You are at the Python prompt on a Tuesday afternoon, writing the kind of throwaway code that accumulates on every programmer's laptop.
- You will practice Distinguish between a variable, the object it refers to, and the identity of that object — and draw memory diagrams to show the difference; Predict the output of id(), type(), and len() functions on basic values; Call a method on an object and understand the difference between a method and a function.
- The chapter moves through Chapter opening, Learning objectives, Prerequisites, Why this chapter matters, and related ideas.

## Chapter opening

You are at the Python prompt on a Tuesday afternoon, writing the kind of throwaway code that accumulates on every programmer's laptop. You type `"hello".upper()` and get back `'HELLO'`. Then you stop and wonder: where did `.upper()` come from? The string `"hello"` is a value, not a recipe. It is not a machine. How does a value know how to do something?

A moment later, you type `id(5)` and get `140734888030976`. You type `id(5)` again in a fresh line, and you get the same number back. But you just created a new 5. Or did you? If they are different objects, why do they have the same identity?

Both questions are asking the same thing underneath: what exactly is a Python value, and how does Python know what to do with it?

This chapter answers that question. In Python, every value — every integer, string, list, anything you can assign to a variable — is an *object*. An object is not a blueprint or a template. It is a concrete thing that exists in the computer's memory right now, with an identity, a type, and a value. The object is also a package: it carries not just data but the operations that make sense for that data. Methods are those operations. When you write `"hello".upper()`, you are asking the string object to perform one of its methods. When you ask for the identity of an object, you are reading a label that Python attaches to everything it creates.

This is the architecture underneath Python. Once you see it, most of the language stops being magic.

### Learning objectives

By the end of this chapter you will be able to:

- **Distinguish** between a variable, the object it refers to, and the identity of that object — and draw memory diagrams to show the difference.
- **Predict** the output of `id()`, `type()`, and `len()` functions on basic values.
- **Call** a method on an object and understand the difference between a method and a function.
- **Explain** why two objects can be equal (`==`) but not identical (`is`), and use each comparison correctly.
- **Recognize** when a list or tuple is mutable or immutable, and anticipate aliasing pitfalls.
- **Identify** and fix an `AttributeError` when the wrong method is called on the wrong type.

### Prerequisites

- Chapter 1 (Statements): assignment, variables, the interactive prompt.
- Chapter 2 (Expressions): operators, type casting, basic arithmetic.
- Comfort with the REPL and running Python code.

### Why this chapter matters

Every value in Python is an object. This chapter is the foundation for understanding how Python really works underneath the syntax. Without it, methods look like magic, identity comparisons feel random, and the quirks of mutable data structures (lists) will surprise you later. With it, Python stops being a series of rules and starts being a coherent machine with consistent behavior.

---

## Concept 1 — The anatomy of an object

You already have three types of values in your arsenal: integers, floats, and strings. You've assigned them to variables, added them, printed them. Now you are going to look *at* them, not just use them.

In Python, an *object* is a piece of data that lives in the computer's memory. Unlike a number written on paper, which is just a symbol, a Python integer is a complete package. It has three properties.

**Identity.** Every object has an identity — a unique label that says "this is *this* object, not that object." In Python, this label is a number (the memory address where the object lives, though you usually don't need to know that). The built-in `id()` function gives you the identity:

```python
>>> id(5)
140734888030976
>>> id(5)
140734888030976
```

The identity never changes for the lifetime of the object. Once created, the label stays the same.

**Type.** Every object has a type — a class that determines what the object can do and what values it can hold. The built-in `type()` function tells you:

```python
>>> type(5)
<class 'int'>
>>> type(5.0)
<class 'float'>
>>> type("hello")
<class 'str'>
```

An integer object can be divided, multiplied, compared. A string object cannot. The type is what enforces that boundary.

**Value.** Every object has a value — the actual data. For a string, that is the sequence of characters. For an integer, that is the number. The value is what you see when you print or use the object:

```python
>>> print(5)
5
>>> print("hello")
hello
```

Here is where the puzzle about `id(5)` resolves. When you type `id(5)` twice, Python runs the same identity function twice, on the same value `5`. For small integers (−5 to 256), Python caches them for efficiency — it creates them once, then reuses them. So both calls return the same identity number. For larger integers, Python creates a fresh object each time:

```python
>>> id(257)
140734888031328
>>> id(257)
140734888031344
```

Two different objects. Same value, different identities.

This matters because Python has two ways to ask "is this the same?"

**`==` means equal — same value.**

```python
>>> 5 == 5
True
>>> "hello" == "hello"
True
>>> 5 == 5.0
True
```

Equal values return `True`, even if the objects are different.

**`is` means identical — same object.**

```python
>>> a = 5
>>> b = 5
>>> a == b
True
>>> a is b
True  # Python reused the cached integer
>>> c = 257
>>> d = 257
>>> c == d
True
>>> c is d
False  # Python created two different objects with the same value
```

Most of the time, you want `==`. You care whether the values are the same. The `is` comparison is a precision tool for specific situations (checking if a variable points to `None`, for example). The distinction becomes critical when you reach mutable objects like lists.

### Trade-off: precision vs. convenience

Python hides the machinery of objects from you most of the time. When you write `x = 5`, you don't think about identity and type. You just think about the value. This is good for readability. But it means that when you *do* need to understand what's happening underneath, you have to think in three layers at once: variable, object, identity. Memory diagrams help:

```
x ──→ 5 (identity: 140734888030976)
y ──→ 5 (same cached integer)
```

When you assign `y = x`, you are not copying the object. You are making `y` point to the same object that `x` points to. Python tracks both the variable name and the object. For small integers, the reuse is invisible. For mutable objects, it becomes a dangerous pitfall.

### Worked example — identity and equality

You are comparing two dates in a Python program. You store two representations of the same date:

```python
>>> date1 = "2025-05-07"
>>> date2 = "2025-05-07"
>>> date1 == date2
True
>>> date1 is date2
True
```

Both are true. The objects have the same value (equal), and Python's string interning caches them as the same object (identical). Now you make a date from user input:

```python
>>> date_input = input("Enter a date: ")
Enter a date: 2025-05-07
>>> date_input == date1
True
>>> date_input is date1
False
```

The value is the same (equal returns `True`). But the object is different (is returns `False`) because the input created a new string object. For comparing dates, `==` is correct. You care about the value, not the object identity.

### Common misconceptions

**Typing a value creates a new object every time.** Not always. Python reuses small integers and short strings (called *interning*). This is an optimization; you don't need to track when it happens. Use `==` for comparisons unless you have a specific reason to use `is`.

**Variables have types.** No. Objects have types. A variable is just a label that points to an object. The same variable can point to an integer one moment and a string the next:

```python
>>> x = 5
>>> type(x)
<class 'int'>
>>> x = "hello"
>>> type(x)
<class 'str'>
```

The variable `x` didn't change type. It switched which object it points to.

**Printing shows the identity.** No. Printing shows the value. To see the identity, use `id()`. To see the type, use `type()`. Each function asks a different question about the object.

---

## Concept 2 — Methods and what they do

You already know about functions. A function is a piece of code that takes input (arguments), does something, and produces output (a return value). You call it by name: `len("hello")` calls the `len` function and passes the string as an argument.

A *method*, from the Greek *methodos* meaning a way of doing, is a function that is attached to a particular object. You call it using dot notation: `"hello".upper()`. The string is not an argument; it is the *target*. The method operates on that object.

Look at the difference:

```python
# Function: len() is not attached to any object
len("hello")  # Call len, pass "hello" as argument

# Method: upper() is attached to the string object
"hello".upper()  # The string knows how to upper itself
```

The function lives in Python's namespace. The method lives inside the object. When you call a method, you are asking the object to perform an operation that makes sense for objects of its type.

Every type has its own methods. Strings have `.upper()`, `.lower()`, `.strip()` (removes whitespace from both ends), and many others. Integers do not have `.upper()` — that makes no sense for a number. If you try, you get an error:

```python
>>> 5.upper()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: 'int' object has no attribute 'upper'
```

The error name is `AttributeError`. Python is saying: "I looked inside the integer object for something called `upper`, and it's not there." This is not a type error (wrong type of input). It is an attribute error (the object doesn't have that attribute). When you see this error, the fix is usually to check your type. Are you calling `.upper()` on a string? If not, convert it first:

```python
>>> x = 5
>>> str(x).upper()  # Convert 5 to the string "5", then call .upper()
'5'
```

### The three-part Claude pattern for methods

Here is a real problem: you have a list of workout sessions, and each session is represented as a string like `"2025-05-07: 5 miles"`. You need to extract the date part from the first session.

```python
sessions = ["2025-05-07: 5 miles", "2025-05-08: 3 miles", "2025-05-09: 7 miles"]
date_str = sessions[0]
date_only = date_str.split(":")[0]
print(date_only)  # Output: 2025-05-07
```

> **Prompt for Claude.** "Write Python code to extract the date part (before the colon) from the string `'2025-05-07: 5 miles'` using the `.split()` method. Store the result in a variable and print it."

> **Tests to validate.**
> - `date_only == "2025-05-07"` — the date is correctly extracted
> - `type(date_only) == str` — the result is a string, not a list
> - Testing with a different date: `"2025-06-15: 10 miles".split(":")[0]` returns `"2025-06-15"`

The `.split()` method is a string method that breaks a string into pieces based on a delimiter (in this case, the colon). It returns a *list* of pieces. The `[0]` grabs the first piece — the part before the colon. You've now used two objects and three operations: a method (`.split()`), an index (`[0]`), and a function (`print()`). Each one is asking a different thing of the object it's attached to.

### Trade-off: object-oriented vs. functional

Python supports two styles. In the *functional* style, you pass objects to functions:

```python
len(my_string)  # Function takes object as input
```

In the *object-oriented* style, you call methods on objects:

```python
my_string.upper()  # Object carries the operation
```

Both are valid. Some types lean functional (you use `len()` more often than you'd use a `.length()` method), and some lean object-oriented (you use `.upper()` more often than a `upper(string)` function). Python is pragmatic: it uses whichever style fits the problem. You should too.

### Worked example — discovering methods

You are building a shopping app. You have a product name with extra whitespace: `"  Organic Mango Juice  "`. You want to strip the whitespace, convert to lowercase, and check the length.

```python
product = "  Organic Mango Juice  "
cleaned = product.strip().lower()
print(f"Name: {cleaned}, Length: {len(cleaned)}")
```

Output: `Name: organic mango juice, Length: 20`

> **Prompt for Claude.** "Write Python code to take the string `'  Organic Mango Juice  '`, remove whitespace from both ends using `.strip()`, convert to lowercase using `.lower()`, and print the cleaned name and its length."

> **Tests to validate.**
> - `cleaned == "organic mango juice"` — spaces removed and lowercase applied
> - `len(cleaned) == 20` — the length is correct
> - `cleaned.startswith("org")` returns `True` — the string starts with "org"

You can chain methods together: `.strip()` returns a new string, and you immediately call `.lower()` on that result. This is called *method chaining*. Python evaluates it left to right: first `.strip()`, then `.lower()`, then the `f-string` formatting.

### Common misconceptions

**Methods and functions are the same thing.** They are similar, but methods live inside objects. Functions live in the namespace. The notation is different: `len(x)` vs. `x.upper()`.

**Every type has the same methods.** No. Each type has its own set. Strings have `.upper()`. Lists have `.append()`. Integers have neither. The error message tells you which method the type doesn't have.

**Calling a method changes the object.** Sometimes. For immutable objects like strings, methods return a *new* object. For mutable objects like lists, methods often modify the object in place. You'll see the difference in the next section.

---

## Concept 3 — Mutability and the aliasing trap

You now know that a variable is a label pointing to an object. Most of the time, this doesn't matter. But when the object can be changed after it's created, this labeling becomes dangerous.

A *mutable* object, from the Latin *mutare* meaning to change, can be modified after creation. A *immutable* object cannot. Strings are immutable:

```python
>>> s = "hello"
>>> s[0] = "H"  # Try to change the first character
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'str' object does not support item assignment
```

Once created, the string stays the same. If you want a new string with a capital H, you have to create a new string:

```python
>>> s = "hello"
>>> s = "H" + s[1:]  # Create a new string
>>> s
"Hello"
```

Lists are mutable:

```python
>>> numbers = [1, 2, 3]
>>> numbers[0] = 99
>>> numbers
[99, 2, 3]
```

The same list object changed. No new object was created. This efficiency is good when you need it. But it creates a trap called *aliasing*: when two variables point to the same list, changing the list through one variable affects what you see through the other.

```python
>>> workout_1 = [3, 5, 7]  # Miles for three days
>>> workout_2 = workout_1  # workout_2 points to the same list
>>> workout_2[0] = 4  # Change it
>>> workout_1  # It changed here too!
[4, 5, 7]
```

This is not a bug. It is how Python works: `workout_2 = workout_1` does not copy the list. It makes `workout_2` point to the same object. When you modify the list through `workout_2`, you are modifying the object that `workout_1` also points to. Memory diagram:

```
workout_1 ──────┐
                ├──→ [3, 5, 7]  (identity: 140734888031424)
workout_2 ──────┘
```

When you change the first element to 4:

```
workout_1 ──────┐
                ├──→ [4, 5, 7]  (same object, changed)
workout_2 ──────┘
```

If you wanted a separate copy, you have to be explicit:

```python
>>> workout_1 = [3, 5, 7]
>>> workout_2 = workout_1.copy()  # Create a new list with the same elements
>>> workout_2[0] = 4
>>> workout_1
[3, 5, 7]  # Unchanged; workout_2 points to a different list
```

Now there are two objects:

```
workout_1 ──→ [3, 5, 7]  (identity: 140734888031424)
workout_2 ──→ [4, 5, 7]  (identity: 140734888031520)
```

### The three-part Claude pattern for list mutation

You are tracking a shopping cart. Items are added and removed as the user browses. You build a list of cart items and want to show what the cart contains at each step.

```python
cart = ["milk", "bread", "eggs"]
saved_cart = cart.copy()  # Save the current state
cart.append("cheese")
cart.remove("bread")

print(f"Original state: {saved_cart}")
print(f"Updated cart: {cart}")
```

Output:
```
Original state: ['milk', 'bread', 'eggs']
Updated cart: ['milk', 'eggs', 'cheese']
```

> **Prompt for Claude.** "Write Python code to create a list `cart` with items 'milk', 'bread', 'eggs'. Create a copy of the list called `saved_cart`. Then modify the original list by appending 'cheese' and removing 'bread'. Print both lists to show the difference."

> **Tests to validate.**
> - `saved_cart == ["milk", "bread", "eggs"]` — the copy is unchanged
> - `cart == ["milk", "eggs", "cheese"]` — the original is modified
> - `saved_cart is not cart` — they are different objects
> - `saved_cart == cart` evaluates to `False` — the lists are no longer equal

Without `.copy()`, both variables would point to the same list, and you'd lose the original state. This is the aliasing trap: it looks simple (`saved_cart = cart`) but creates shared references.

### Trade-off: efficiency vs. safety

Mutable objects are faster because you don't create copies. You modify in place. For a shopping cart with millions of items, this difference matters. But mutable objects are more dangerous because of aliasing — you can accidentally modify a shared list. Immutable objects are safer because you cannot accidentally change them, but every modification creates a new object, which uses more memory.

Python lets you choose: use lists when you need speed and control, use tuples (immutable sequences) when you need safety and you don't plan to modify the data. More on tuples in a moment.

### Worked example — the aliasing trap

You are managing a roster of athletes. You assign the roster to a draft list to prepare for trades:

```python
original_roster = ["Alice", "Bob", "Charlie"]
draft_roster = original_roster
draft_roster.append("Dana")

print(f"Original: {original_roster}")
print(f"Draft: {draft_roster}")
```

Output:
```
Original: ['Alice', 'Bob', 'Charlie', 'Dana']
Draft: ['Alice', 'Bob', 'Charlie', 'Dana']
```

You appended to the draft, but the original changed too. They point to the same list. The fix:

```python
original_roster = ["Alice", "Bob", "Charlie"]
draft_roster = original_roster.copy()
draft_roster.append("Dana")

print(f"Original: {original_roster}")
print(f"Draft: {draft_roster}")
```

Output:
```
Original: ['Alice', 'Bob', 'Charlie']
Draft: ['Alice', 'Bob', 'Charlie', 'Dana']
```

Now they are separate objects, and changes to one don't affect the other.

### Common misconceptions

**Assignment always copies.** No. For mutable objects, assignment creates a new reference to the same object. Use `.copy()` if you want a separate copy.

**Mutable is always better.** No. Mutability is an efficiency choice with a safety cost. Immutable objects are slower but safer. Use each for what it's designed for.

**If two lists are equal with `==`, they are the same object.** No. Two lists can have the same contents but be different objects. Use `is` to check if they are the same object, and `==` to check if they have the same contents:

```python
>>> a = [1, 2, 3]
>>> b = a.copy()
>>> a == b
True  # Same contents
>>> a is b
False  # Different objects
```

---

## Integration — putting it together

You've now seen three layers of Python's object architecture: the anatomy (id, type, value), the behavior (methods), and the danger (mutability). Let's put them in a real program.

You're building a simple task tracker. Users add tasks, mark them done, and see their progress.

```python
tasks = ["Write report", "Review feedback", "Submit draft"]
completed = []

# Someone works on the first task
current_task = tasks[0]
print(f"Working on: {current_task}")

# When done, move it to completed
tasks.remove(tasks[0])  # Remove from tasks
completed.append(current_task)  # Add to completed

print(f"Remaining: {tasks}")
print(f"Completed: {completed}")
print(f"Progress: {len(completed)}/{len(completed) + len(tasks)} done")
```

Output:
```
Working on: Write report
Remaining: ['Review feedback', 'Submit draft']
Completed: ['Write report']
Progress: 1/3 done
```

At each step, you're using the three-layer understanding. `tasks` and `completed` are variables pointing to list objects. Each list is mutable, so you can modify it in place. The `len()` function works on both lists. The `.append()` and `.remove()` methods modify the lists. The `[0]` index accesses individual elements. No magic.

---

## Graduated exercises

### Warm-up

1. **Object anatomy.** For the code below, write out the identity, type, and value of each object:
   ```python
   x = 42
   y = 3.14
   z = "python"
   ```
   Use `id()`, `type()`, and `print()` to verify your answers in the REPL.

2. **Method calling.** Write a Python snippet that takes the string `"python Programming"` and uses one method call to convert it to all lowercase. Print the result.

3. **Mutability check.** Create a list with three items. Modify one item using indexing. Then try to do the same with a string literal (not stored in a variable). What error do you get?

### Application

4. **Aliasing trap.** You have a list of favorite movies. You assign it to a new variable to prepare a "top 3" list. You modify the new list. What happens to the original? Write the code, run it, and explain the result. Then fix it using `.copy()`.

5. **Method chaining.** You have the string `"  HELLO WORLD  "`. Use two method calls (chained) to remove the whitespace and convert to lowercase. Print the result. What is the final value?

6. **Mixed types.** Create a list containing an integer, a float, a string, and another list as elements. Use indexing to extract each element. Print the type of each. Then modify one element and print the list again.

### Synthesis

7. **Task tracker.** Build a small program that:
   - Starts with a list of tasks: `["Email client", "Debug login", "Update docs"]`
   - Creates an empty `completed` list
   - Removes the first task from the original list and appends it to `completed`
   - Prints the state of both lists
   - Shows the progress: "1 of 3 tasks done"
   
   Run it, then modify it so that you track both lists without aliasing — that is, create an independent copy of the original for reference.

### Challenge

8. **Identity investigations.** Write a program that creates two lists with the same contents:
   ```python
   list_a = [1, 2, 3]
   list_b = [1, 2, 3]
   ```
   Check if `list_a == list_b` and if `list_a is list_b`. Then run:
   ```python
   list_c = list_a
   ```
   Check the same comparisons for `list_a` and `list_c`. Explain the results in terms of the three-layer object model (identity, type, value). Why is one pair identical and the other not?

---

## Claude Code

The three layers of objects — identity, type, and value — are easiest to understand when you can see them working. Here's a Claude Code session that builds a shopping list, aliases it, mutates it, and shows you exactly what changed and what stayed the same.

> **Claude Code prompt.**
>
> "Create a file `objects_demo.py` that demonstrates object identity and aliasing.
> 
> The program should:
> 1. Create a shopping list `original = ['milk', 'bread', 'eggs']`.
> 2. Alias it: `alias = original`.
> 3. Before mutation: print the id of both variables, the contents of both lists, and whether `alias is original` (should be `True`).
> 4. Mutate through the alias: `alias.append('cheese')`.
> 5. After mutation: print the id of both variables again, the contents of both lists, and confirm that `original` changed even though you only modified `alias`.
> 6. Now create a proper copy: `independent = original.copy()`.
> 7. Mutate the original through the copy: `independent.append('yogurt')` and `independent[0] = 'almond milk'`.
> 8. Show the state of all three lists and their ids.
> 9. Use comments to explain what each section is showing.
>
> Run the file and explain what the output tells you about how aliasing works. Why do `original` and `alias` have the same id, but `independent` has a different one?"

**What to expect.** You'll see output showing that when two variables point to the same list object (same id), changes through one are visible through the other. When you use `.copy()`, Python creates a new list object with a different identity, and changes to the copy don't affect the original. This is the core of the aliasing trap — Python doesn't copy by default; it creates references. The ids prove it.

**Stretch.** Modify the program to use a tuple instead of a list: `original_tuple = ('milk', 'bread', 'eggs')`. Try to alias it and mutate it the same way. What error do you get? Why? Write a short explanation in a comment.

---

## Chapter summary

An *object* is a concrete value in Python's memory, with three properties:
- **Identity:** A unique label (returned by `id()`), never changes.
- **Type:** The class that determines what the object can do (returned by `type()`).
- **Value:** The actual data the object holds (shown by `print()`).

A *variable* is a label that points to an object. Multiple variables can point to the same object (aliasing).

**Equality vs. identity:**
- `==` checks if two objects have the same value (equal).
- `is` checks if two variables point to the same object (identical).

A *method* is a function attached to an object. Call it with dot notation: `object.method()`. When you get an `AttributeError`, the object doesn't have that method — check the type.

A *mutable* object can be changed after creation (lists). An *immutable* object cannot (strings, integers). Mutability enables efficiency but creates the aliasing trap: two variables pointing to the same mutable object will both see any changes. Use `.copy()` to create a separate copy.

A *list* is a mutable sequence of elements. A *tuple* is an immutable sequence. Both support indexing and `len()`. Lists support `.append()`, `.remove()`, and `.copy()`. Tuples do not — they cannot be modified.

---

## What would change my mind

If a Python implementation (like PyPy or Jython) made garbage collection and reference counting so transparent that identity became unnecessary for learning, I would de-emphasize `is` and focus more on the type/value distinction. But CPython's memory model is the standard, and students will eventually run into performance issues where understanding identity matters.

## Still puzzling

I don't fully understand why Python chose to intern small integers (−5 to 256) but not small strings consistently. The engineering reason is cache efficiency, but there's a design choice there about what makes identity visible to programmers. This is worth coming back to once you've studied how Python allocates memory.

---

## Tags

objects, identity, methods, mutability, aliasing, lists, tuples, immutable, mutable, attribute-error, id-function, is-vs-equals, memory-diagrams, python-architecture
---

## LLM Exercise — Chapter 03: Objects (Text Adventure Project)

**Project:** Text Adventure.
**What you're building this chapter:** string-method usage for input normalization + a first introspection of mutability.
**Tool:** **Claude Code**.

---

**The Prompt:**

```
Chapter 3 of my Text Adventure project. Chapter 3 covered: every
value is an object; methods on values; mutability vs.
immutability; identity (`is`) vs. equality (`==`).

Add input normalization and demonstrate object behavior.

1. **Normalize the player name.** Use string methods:
   - `.strip()` to remove leading/trailing whitespace.
   - `.title()` to capitalize properly ("john" → "John").
   - Verify the empty-string case (after strip) still triggers
     the default-name behavior from Ch 1.

2. **Build a "command normalizer" function — wait, no functions
   yet.** Just write inline code that:
   - Asks the player for a command (e.g., "What do you want to
     do?")
   - Strips whitespace, lowercases the input.
   - Prints the normalized command back. (This sets up Ch 8
     where we'll do real command parsing.)

3. **Demo mutability.** Show the difference between mutable and
   immutable objects:
   - Create `original_inventory = ['rope', 'torch']`.
   - Create `copied_inventory = original_inventory`.
   - Mutate copied_inventory by `.append()`.
   - Print both. They're the same! Both have ['rope', 'torch',
     ...]. Show why with `id()` (they're the same object).
   - Now create `independent_inventory = original_inventory.copy()`.
   - Append to independent_inventory.
   - Print all three. The original is now changed too (because
     of the alias), but independent is its own list.

   This demo will save you debugging when classes arrive in Ch 11.

4. **The Python REPL practice.** Use Claude Code (or python -i
   game.py) to interactively:
   - Type `dir(player_name)` — see all methods.
   - Type `dir(player_health)` — fewer methods (int).
   - Type `dir(original_inventory)` — list methods.
   - Note the patterns. This is how to discover what a Python
     object can do.

In addition to the code, produce the prompt that elicited it
plus tests. Verify the mutability demo by running it and
predicting outputs first.

End with: name one method on a string OR a list that you didn't
know existed before this chapter. What does it do?
```

---

**What this produces:** Normalized input + a mutability demo. The "method I didn't know" closer trains the discipline of exploring objects.

**Connection to previous chapters:** Ch 2's stats are int objects; Ch 3 makes that explicit.

**Preview of next chapter:** Chapter 4 covers decisions. You'll add the first real branching — the player chooses which way to go (north/south/east/west) at the opening room.


---

##  AI Wayback Machine
**Alan Kay** was designed Smalltalk and coined "object-oriented programming" — the paradigm Python implements in its own way.

**Run this:**

```
Who is Alan Kay, and how does their work connect to objects we covered in this chapter? Keep it to three paragraphs. End with the single most surprising thing about their career or ideas.
```

→ Search **"Alan Kay"** on Wikipedia.

**Now make the prompt better.** Try one of these:

- Ask it to apply Alan Kay's ideas to a small Python program you'd write.
- Add a constraint: "Answer including criticisms or limits of Alan Kay's framework."

What changes? What gets better? What gets worse?
