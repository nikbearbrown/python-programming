# Chapter 3 — Objects
*Why every value in Python carries more than a value.*

You are at the Python prompt on a Tuesday afternoon. You type `"hello".upper()` and get back `'HELLO'`. You stop. A string is a piece of data — a sequence of characters. It is not a machine. It does not contain moving parts. So where did `.upper()` come from? How does a *value* know how to do something?

A moment later, you type `id(5)` and get back a large number — something like `140734888030976`. You type `id(5)` again, and you get the same number. You just created a new `5`. Or did you? Two fives. Same identity. That's either wrong or it means something.

Both puzzles have the same answer. In Python, every value is an *object*. Not a loose number or a string of text floating in memory — a complete package, with an identity, a type, and a value, plus the operations that make sense for that kind of thing. The string `"hello"` doesn't just hold characters; it holds the ability to transform itself. The number `5` isn't just `5`; it is a specific thing in memory that Python has catalogued and can point to. When you understand that, the surprises stop being surprises.

---

## Three things every object has

Every object in Python has three properties that never coexist in plain data.

**Identity** is the unique label that says: this is *this* object, not any other. Python assigns each object an identity when it creates it, and that identity stays constant for the lifetime of the object. The `id()` function exposes it:

```python
>>> id(5)
140734888030976
>>> id("hello")
140734888031008
```

**Type** is the class the object belongs to — the category that determines what the object can do and what it can hold. The `type()` function asks:

```python
>>> type(5)
<class 'int'>
>>> type(5.0)
<class 'float'>
>>> type("hello")
<class 'str'>
```

An integer knows how to be divided and multiplied. A string knows how to be searched and transformed. The type enforces which operations make sense.

**Value** is the actual data — the number, the sequence of characters, whatever concrete thing the object represents. This is what `print()` shows you.

Now the `id(5)` puzzle resolves. Python caches small integers — roughly −5 to 256 — as an optimization. It creates each one once, then reuses it. Type `id(5)` twice and you are pointing at the same cached object both times. The identity matches because there is only one `5` object in that range. Go outside the range:

```python
>>> id(257)
140734888031328
>>> id(257)
140734888031344
```

Two different numbers. Two different objects. Same value, different identities. Python created a fresh object each time.

<!-- → [INFOGRAPHIC: Side-by-side showing small integer caching (id(5) twice → same address) vs. large integer creation (id(257) twice → different addresses). Makes the optimization concrete without requiring knowledge of memory management.] -->

This distinction — same value, different identity — is why Python gives you two comparison operators.

`==` asks: do these objects have the same value?

```python
>>> 5 == 5
True
>>> "hello" == "hello"
True
>>> 5 == 5.0
True
```

`is` asks: are these the same object?

```python
>>> a = 5
>>> b = 5
>>> a is b
True    # Python reused the cached integer — same object
>>> c = 257
>>> d = 257
>>> c == d
True    # Same value
>>> c is d
False   # Different objects
```

Almost always, you want `==`. You care about value, not about which object Python happened to allocate. The one case where `is` matters in practice is checking for `None`:

```python
if result is None:
    ...
```

This is an idiom in Python, not an accident. `None` is a singleton — there is exactly one `None` object. `is None` is both correct and faster than `== None` for this check.

<!-- → [TABLE: Two-column comparison of `==` vs `is` — column headers: Operator, What it asks, When to use it, Example. Rows: `==` / same value? / comparing content of strings, lists, numbers / `"hello" == "hello"`; `is` / same object? / checking for None, debugging identity / `result is None`.] -->

---

## A variable is not an object

Variables point to objects. This sounds trivial but changes how you think about assignment.

When you write `x = 5`, Python creates an integer object with value `5`, and binds the name `x` to it. When you write `y = x`, Python does not copy the integer. It makes `y` point to the same object `x` already points to. Two labels, one object.

The labels have no type. The objects have types. The same label can point to different types at different moments:

```python
>>> x = 5
>>> type(x)
<class 'int'>
>>> x = "hello"
>>> type(x)
<class 'str'>
```

`x` didn't change type. It switched which object it references. The integer object still exists (for a moment, until nothing points to it and Python reclaims the memory), and the string object now has a label.

This architecture has a consequence that won't bite you yet but will later: when you say "variables have types," you are wrong in a way that will mislead you. Objects have types. Variables are just names.

---

## Methods: what the dot means

`len("hello")` is a function call. You pass the string as an argument. The function lives outside the object — in Python's namespace — and the string is its input.

`"hello".upper()` is a method call. The dot is the operator. The string to the left of the dot is not an argument; it is the *target*. The method lives *inside* the object. When you write `"hello".upper()`, you are asking this specific string object to perform one of its built-in operations.

Every type has its own set of methods. Strings have `.upper()`, `.lower()`, `.strip()`, `.split()`, and many others. Integers do not have `.upper()` — that makes no sense for a number. If you try it anyway:

```python
>>> 5.upper()
AttributeError: 'int' object has no attribute 'upper'
```

`AttributeError` means Python looked inside the integer object for something called `upper` and didn't find it. The fix is always to check your type. If you have an integer but need a string, convert it first: `str(5).upper()`.

Methods can be chained. `.strip()` returns a new string (whitespace removed). You can immediately call `.lower()` on that result:

```python
product = "  Organic Mango Juice  "
cleaned = product.strip().lower()
print(cleaned)          # organic mango juice
print(len(cleaned))     # 20
```

Python evaluates the chain left to right: `strip()` first, then `lower()` on the result. The original string `product` is unchanged — strings are immutable, so every string method returns a new string rather than modifying the original.

<!-- → [INFOGRAPHIC: Diagram of method chaining — `"  HELLO  "` → `.strip()` → `"HELLO"` → `.lower()` → `"hello"`. Each step shows the intermediate value, making the left-to-right evaluation visible.] -->

That word — *immutable* — leads to the most important idea in this chapter.

---

## Mutability and the aliasing trap

An *immutable* object cannot be changed after it is created. Strings, integers, and floats are immutable. If you try to modify a string in place:

```python
>>> s = "hello"
>>> s[0] = "H"
TypeError: 'str' object does not support item assignment
```

Python refuses. If you want `"Hello"`, you have to create a new string. Methods like `.upper()` and `.strip()` do this — they return a new string with the modifications. The original is untouched.

Lists are *mutable*. You can modify them after creation:

```python
>>> numbers = [1, 2, 3]
>>> numbers[0] = 99
>>> numbers
[99, 2, 3]
```

The same object changed. No new object was created. This efficiency is valuable, but it creates a trap.

Because assignment binds a label to an object — not a copy — two variables can point to the same list:

```python
>>> workout_1 = [3, 5, 7]
>>> workout_2 = workout_1
```

Now both names point to one object. When you modify the list through either name, you are modifying the shared object:

```python
>>> workout_2[0] = 4
>>> workout_1
[4, 5, 7]
```

You touched `workout_2`. `workout_1` changed. This is *aliasing* — two labels on the same object. It is not a bug in Python. It is exactly how Python works, and it will surprise you the first time you hit it.

<!-- → [INFOGRAPHIC: Memory diagram showing two-stage aliasing trap. Stage 1: `workout_1` and `workout_2` arrows both pointing to `[3, 5, 7]` (one box). Stage 2: same diagram after mutation — box now shows `[4, 5, 7]`, both arrows still pointing to it. Caption: "Two labels, one object. Changing through either label changes what both labels see."] -->

If you want a separate copy, you have to say so explicitly:

```python
>>> workout_1 = [3, 5, 7]
>>> workout_2 = workout_1.copy()
>>> workout_2[0] = 4
>>> workout_1
[3, 5, 7]    # Unchanged — workout_2 points to a different object now
```

`.copy()` creates a new list with the same elements. Two objects, two identities. Changes to one don't affect the other.

The pattern shows up everywhere. A roster for a sports team. A shopping cart in an e-commerce app. A list of tasks in a to-do system. Whenever you write `new_list = old_list` thinking you're saving a copy, you are creating an alias. The only way to get a true copy is `.copy()`.

<!-- → [TABLE: Comparison of assignment vs. copy — rows: Operation, Code, Creates new object?, Safe to mutate independently? — for `alias = original` (No, No) and `copy = original.copy()` (Yes, Yes).] -->

---

## Putting the three layers together

You now have the complete picture: identity (which object), type (what it can do), value (what it holds). A short program that builds on all three:

```python
tasks = ["Write report", "Review feedback", "Submit draft"]
completed = []

current_task = tasks[0]
tasks.remove(tasks[0])
completed.append(current_task)

print(f"Remaining: {tasks}")
print(f"Completed: {completed}")
print(f"Progress: {len(completed)}/{len(completed) + len(tasks)} done")
```

```
Remaining: ['Review feedback', 'Submit draft']
Completed: ['Write report']
Progress: 1/3 done
```

Every line uses the object model. `tasks` and `completed` are variables pointing to list objects. Lists are mutable, so `.remove()` and `.append()` modify them in place. `len()` is a function that asks any sequence object for its length. `tasks[0]` is indexing — reaching into a list object at a specific position.

No magic. The value exists in memory as an object. The name exists as a label. The type determines which operations apply. The identity stays constant. This is all there is.

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

## AI Wayback Machine

**Alan Kay** designed Smalltalk and coined "object-oriented programming" — the paradigm Python implements in its own way.

**Run this:**

```
Who is Alan Kay, and how does their work connect to objects we covered in this chapter? Keep it to three paragraphs. End with the single most surprising thing about their career or ideas.
```

→ Search **"Alan Kay"** on Wikipedia.

**Now make the prompt better.** Try one of these:

- Ask it to apply Alan Kay's ideas to a small Python program you'd write.
- Add a constraint: "Answer including criticisms or limits of Alan Kay's framework."

What changes? What gets better? What gets worse?

---

## Exercises

**Warm-up**

1. For each of these values, use `id()`, `type()`, and `print()` to inspect the object: `42`, `3.14`, `"python"`. Write down the identity, type, and value for each.

2. Take the string `"python Programming"` and use one method call to convert it to all lowercase. Print the result.

3. Create a list with three items and modify one item using indexing. Then try to do the same thing with a string literal — modify a single character by index. What error do you get?

**Application**

4. You have a list of favorite movies. Assign it to a new variable. Modify the new variable by appending a title. Print both variables. What happened? Now fix the code with `.copy()` and show the difference.

5. You have the string `"  HELLO WORLD  "`. Chain two method calls to remove the whitespace and convert to lowercase. Print the result and its length.

6. Create a list containing an integer, a float, a string, and another list as elements. Print the type of each element. Then change one element and print the list again.

**Synthesis**

7. Build a task tracker: start with a list of three tasks and an empty `completed` list. Move the first task from the task list to completed. Print both lists and a progress line showing "1 of 3 done." Then create an independent copy of the original task list before any moves, and show that the copy preserved the original state.

**Challenge**

8. Create two lists with identical contents: `list_a = [1, 2, 3]` and `list_b = [1, 2, 3]`. Check both `==` and `is`. Then assign `list_c = list_a` and run the same checks between `list_a` and `list_c`. All four comparisons should give a result; explain each in terms of identity, type, and value. Why do some match and others don't?
