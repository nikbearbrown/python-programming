# Chapter 10 — Dictionaries
*The data structure that maps a name to a fact in one lookup — and why that one property changes everything.*

Here is a program that finds a student's grade by scanning a list:

```python
names = ["Alice", "Bob", "Carol", "David"]
grades = [92, 88, 95, 85]

target = "Carol"
for i in range(len(names)):
    if names[i] == target:
        print(grades[i])
        break
```

It works. But if there are a million students, this program checks up to a million names before finding the right one. On average it checks half a million. That's a linear search — the time it takes grows with the size of the list.

Now here is the same lookup with a dictionary:

```python
grades = {"Alice": 92, "Bob": 88, "Carol": 95, "David": 85}
print(grades["Carol"])
```

One lookup. Whether there are four students or four million, the time is the same. Not approximately the same — the same. This is the property that makes dictionaries the most practical data structure in Python: they retrieve a value by its key in constant time, regardless of size.

The rest of this chapter is the mechanics of that fact: how to create dictionaries, how to access and modify them safely, and how to iterate over them. But the property worth remembering is the one above.

---

## The Structure: Keys Map to Values

A dictionary stores pairs. Each pair has a **key** — the thing you look up — and a **value** — the thing you find. Keys and values are separated by colons. Pairs are separated by commas. The whole thing lives in curly braces:

```python
phone_book = {
    "Alice Smith": "555-0147",
    "Bob Johnson": "555-0198",
    "Carol Davis": "555-0205"
}
```

The key is on the left. The value is on the right. To retrieve a value, you use the key in square brackets — just like indexing a list, but instead of a position you use a meaningful name:

```python
print(phone_book["Alice Smith"])   # 555-0147
```

If the key doesn't exist, Python raises a `KeyError`:

```python
print(phone_book["Zoe"])   # KeyError: 'Zoe'
```

The error message is precise. It tells you the exact key that wasn't found.

### The Rule About Keys

Keys must be **immutable** — they cannot change after they're created. Strings, numbers, tuples: all fine as keys. Lists and dictionaries: not allowed.

Why? Because Python finds values using a hash of the key — a mathematical fingerprint that maps the key to a storage location. If the key could change after it was stored, its fingerprint would change, and Python would never find it again. Immutable keys guarantee that the fingerprint stays stable.

```python
good = {"name": "Alice", 42: "the answer", (1, 2): "a tuple key"}

bad = {[1, 2]: "a list key"}   # TypeError: unhashable type: 'list'
```

The error "unhashable type" means the type can't be fingerprinted, so it can't be a key.

Values have no such restriction. They can be anything — strings, numbers, lists, other dictionaries.

<!-- → [TABLE: what can and cannot be dictionary keys — rows: string, integer, float, tuple, bool, None (can), list, dict, set (cannot) — column: type, hashable?, example as key, reason — student should have a quick reference for the immutability rule] -->

---

## Accessing Values Safely

The `[]` operator is direct but dangerous when a key might not exist. The safer tool is `.get()`. It takes two arguments: the key you're looking for, and a default value to return if the key isn't there:

```python
grades = {"Alice": 92, "Bob": 88, "Carol": 95}

print(grades.get("Alice", "Not found"))   # 92
print(grades.get("Zoe", "Not found"))     # Not found
```

If you omit the default, `.get()` returns `None` for missing keys rather than raising an error.

The choice between `[]` and `.get()` is a design decision. When you *know* a key should exist and want the program to fail loudly if it doesn't, use `[]` — the `KeyError` is your signal that something is wrong. When missing keys are expected and you have a sensible fallback, use `.get()` and name the fallback explicitly.

A word of caution: `if grades.get("Alice"):` looks like a safe membership check, but it's not. If Alice's grade is `0`, `.get()` returns `0`, which is falsy — and your condition silently evaluates to `False` even though Alice exists. Use `if "Alice" in grades:` to check membership, `.get()` to retrieve with a fallback.

---

## Three Views of a Dictionary

Python provides three ways to look at the contents of a dictionary:

```python
grades = {"Alice": 92, "Bob": 88, "Carol": 95}

print(grades.keys())     # dict_keys(['Alice', 'Bob', 'Carol'])
print(grades.values())   # dict_values([92, 88, 95])
print(grades.items())    # dict_items([('Alice', 92), ('Bob', 88), ('Carol', 95)])
```

Each returns a **view object** — not a list, but iterable. You can loop over them, check membership, or convert to a list if you need one.

The most useful for practical work is `.items()`. When you need both the key and the value in a loop, `.items()` gives you both as a tuple, which you can unpack directly:

```python
for student, grade in grades.items():
    print(f"{student}: {grade}")
```

Output:
```
Alice: 92
Bob: 88
Carol: 95
```

The tuple unpacking — `student, grade` — is the idiomatic way to iterate over a dictionary when you need both sides of each pair.

---

## Mutating a Dictionary

Dictionaries are mutable. You can add entries, update values, and delete entries after creation.

**Adding and updating** use the same syntax — assign a value to a key:

```python
grades["David"] = 85          # new entry
grades["Alice"] = 96          # update existing entry
```

Python doesn't distinguish between the two. If the key exists, the value is replaced. If it doesn't, the entry is created. This is clean but silent — it will overwrite without warning. If you want to add only when the key is new, check first:

```python
if "David" not in grades:
    grades["David"] = 85
```

**Deleting** uses either `del` or `.pop()`:

```python
del grades["Bob"]                         # removes Bob, raises KeyError if missing
removed = grades.pop("Carol")             # removes Carol, returns 95
safe = grades.pop("Zoe", "not found")     # returns "not found" instead of crashing
```

`.pop()` is the safer choice when the key might not exist, because it lets you provide a fallback. `del` is fine when you've already confirmed the key is there.

### The Counting Pattern

One of the most common dictionary operations is counting occurrences — building up a tally from a sequence. Here's the core pattern using `.get()`:

```python
sentence = "the quick brown fox jumps over the lazy dog the fox"
words = sentence.split()

word_count = {}
for word in words:
    word_count[word] = word_count.get(word, 0) + 1

print(word_count)
# {'the': 3, 'quick': 1, 'brown': 1, 'fox': 2, ...}
```

For each word: fetch its current count (defaulting to 0 if it's new), add 1, store it back. This idiom — `d[key] = d.get(key, 0) + 1` — appears constantly in Python. Internalize it.

<!-- → [FIGURE: step-by-step trace of the counting loop for "the fox the" — shows word_count dictionary state after each iteration: {}, {"the": 1}, {"the": 1, "fox": 1}, {"the": 2, "fox": 1} — student should see the dictionary growing as the loop runs] -->

---

## Dictionary Comprehensions

A **dictionary comprehension** builds a dictionary from an iterable in one expression:

```python
squares = {n: n**2 for n in range(1, 6)}
# {1: 1, 2: 4, 3: 9, 4: 16, 5: 25}
```

The syntax mirrors list comprehensions: an expression on each side of the colon, a `for` clause, and an optional `if` filter:

```python
even_squares = {n: n**2 for n in range(1, 10) if n % 2 == 0}
# {2: 4, 4: 16, 6: 36, 8: 64}
```

This is the same as the equivalent loop but in one line. Use comprehensions when you're transforming or filtering data into a new dictionary. Use loops when you have side effects — printing, writing to files, modifying existing data.

Comprehensions are also useful for inverting a dictionary (swapping keys and values):

```python
name_to_id = {"Alice": 1001, "Bob": 1002, "Carol": 1003}
id_to_name = {v: k for k, v in name_to_id.items()}
# {1001: 'Alice', 1002: 'Bob', 1003: 'Carol'}
```

This works as long as the values are unique (otherwise you lose information in the inversion) and immutable (since they become keys).

---

## Nested Dictionaries

Values can be anything — including other dictionaries. This creates **nested dictionaries**, which are the natural representation of structured data:

```python
inventory = {
    "Python Crash Course": {"quantity": 5, "price": 29.99},
    "Clean Code":          {"quantity": 2, "price": 35.00},
    "The Pragmatic Programmer": {"quantity": 0, "price": 42.50}
}
```

To access a nested value, chain the keys:

```python
print(inventory["Clean Code"]["price"])   # 35.0
```

The first `[]` retrieves the inner dictionary. The second `[]` retrieves the value from it. This chains to any depth, though dictionaries nested more than two or three levels deep become hard to read and are usually a sign that the data should be modeled differently.

Nested dictionaries are how JSON data is structured — the format that APIs and web services use to move data. When you call an API, you'll often get back a dictionary of dictionaries. Knowing how to navigate nested structures is practical from day one.

<!-- → [FIGURE: visual tree diagram of the nested inventory dictionary — inventory as root node, each book title as a child node, quantity and price as leaf nodes — student should see the nesting as a tree rather than imagining it as text] -->

---

## A Complete Example: Bookstore Inventory

Five operations — lookup, restock, sell, flag out-of-stock, report — all built on the three core patterns from this chapter:

```python
inventory = {
    "Python Crash Course":       5,
    "Automate the Boring Stuff": 3,
    "Clean Code":                2,
    "The Pragmatic Programmer":  0
}

def check_stock(title):
    return inventory.get(title, 0)

def restock(title, quantity):
    inventory[title] = inventory.get(title, 0) + quantity

def sell(title):
    count = inventory.get(title, 0)
    if count > 0:
        inventory[title] -= 1
        return True
    return False

def out_of_stock():
    return [title for title, count in inventory.items() if count == 0]

def report():
    for title, count in inventory.items():
        status = "OUT OF STOCK" if count == 0 else f"{count} copies"
        print(f"{title:<40} {status}")
```

```python
print(check_stock("Python Crash Course"))   # 5
print(check_stock("Not a Real Book"))       # 0 — safe default, no crash

restock("Python Crash Course", 3)
print(check_stock("Python Crash Course"))   # 8

sell("Python Crash Course")
print(check_stock("Python Crash Course"))   # 7

print(out_of_stock())   # ['The Pragmatic Programmer']
report()
```

Output:
```
Python Crash Course                      7 copies
Automate the Boring Stuff                3 copies
Clean Code                               2 copies
The Pragmatic Programmer                 OUT OF STOCK
```

Every function uses one of the patterns introduced in this chapter: `.get()` for safe access, assignment for mutation, `.items()` for iteration. The dictionary is the right tool here because every operation is a lookup by title — a meaningful key, not a position.

---

## What the Chapter Adds Up To

The central property is constant-time lookup. When you need to find something by a meaningful name rather than by its position in a sequence, a dictionary is almost always the right tool. The name of the student, the title of the book, the username in a system — these are all natural keys.

Three things that trip up beginners:

First, the `KeyError`. It comes from accessing a key that doesn't exist with `[]`. The fix is either `.get()` with a default, or an `in` check before the access. Use `[]` when you're confident; use `.get()` when you're not.

Second, the hashability rule. Lists can't be keys. If you need a composite key — something that bundles multiple values together — use a tuple. Tuples are immutable and hashable; lists are not.

Third, the silent overwrite. `d[key] = value` creates or updates without complaint. If you're not careful, you can overwrite existing data. Check with `in` before writing if you need to distinguish "new entry" from "update."

*What would change my mind:* The claim that dictionary lookups are constant-time holds in the average case but can degrade to linear in adversarial scenarios involving hash collisions. For most programs, this never matters. If you're building a security-sensitive system where an attacker could control dictionary keys, it's worth knowing that Python's hash randomization (enabled by default since Python 3.3) mitigates the worst cases.

*Still puzzling:* Why `.items()` returns tuples rather than some named structure — so `for student, grade in grades.items()` unpacks automatically — is a design choice that trades clarity for simplicity. Named tuples or dataclasses would be more self-documenting, but the tuple-unpacking idiom is now so idiomatic in Python that the tradeoff is probably right.

---

## Exercises

### Warm-up

**Exercise 10.1** *(Create and access.)*
Create a dictionary `fruit_colors` mapping three fruits to their colors. Retrieve one color using `[]` and one using `.get()`. What is the difference in behavior if the key doesn't exist?

**Exercise 10.2** *(Key validity.)*
Which of the following can be dictionary keys? Explain why or why not for each: `"name"`, `42`, `[1, 2, 3]`, `(1, 2)`, `{"a": 1}`.

**Exercise 10.3** *(Safe access.)*
Given `person = {"name": "Alice", "age": 30}`, retrieve the `"email"` key safely using `.get()` with a default of `"unknown"`. What would happen if you used `person["email"]` instead?

### Application

**Exercise 10.4** *(Mutation.)*
Start with an empty dictionary. Add three entries using `[]`. Update one value. Delete another using `.pop()`. Print the dictionary after each step.

**Exercise 10.5** *(Iteration.)*
Given `student_grades = {"Alice": 92, "Bob": 88, "Carol": 95}`, write a loop using `.items()` that prints `"Alice: 92"`, `"Bob: 88"`, `"Carol: 95"`.

**Exercise 10.6** *(Comprehension.)*
Write a dictionary comprehension that maps each word in `["python", "dictionary", "code"]` to its length.

### Synthesis

**Exercise 10.7** *(Word frequency.)*
Write a function that takes a string, splits it into words, and returns a dictionary mapping each word to its count. Test on `"the fox jumps over the fox"`. Expect `"the": 2, "fox": 2, ...`.

**Exercise 10.8** *(Nested dictionaries.)*
Create a nested dictionary representing an organization with two departments, each containing two employees and their roles. Write code to: print the role of one employee, add a new employee to one department, and print all employees in the other department.

**Exercise 10.9** *(Safe deletion.)*
Given a dictionary of product prices, print all items and their prices, remove the cheapest item using `.pop()` with a default, and print the updated dictionary.

### Challenge

**Exercise 10.10** *(Nested lookup.)*
A restaurant menu is a nested dictionary: `menu = {"appetizers": {"soup": 8, "salad": 10}, "mains": {"pasta": 15, "steak": 28}}`. Write a function that takes a category and a dish name and returns the price. Handle gracefully: what if the category doesn't exist? What if the dish doesn't exist in that category?

**Exercise 10.11** *(Score tracker.)*
Write a program that reads a series of name-score pairs, builds a dictionary, lets the user look up any student's score by name, handles missing names gracefully with `.get()`, and prints the class average when done.

---

## LLM Exercise — Chapter 10: Dictionaries (Text Adventure Project)

**Project:** Text Adventure.
**What you're building this chapter:** the world map as a dictionary of rooms — Python's most-used data structure carrying the game's structure.
**Tool:** **Claude Code**.

---

**The Prompt:**

```
Chapter 10 of my Text Adventure project. Chapter 10 covered:
key-value pairs; common operations (get, keys, values, items);
iteration patterns; dict comprehensions; KeyError; nested
dictionaries.

Refactor the world into dictionaries. Replace your parallel
lists from Ch 9.

1. **The world dictionary.** A nested structure:
   ```
   world = {
       "tavern": {
           "name": "The Sticky Mug Tavern",
           "description": "A warm room thick with woodsmoke...",
           "exits": {"north": "courtyard", "east": "kitchen"},
           "items": ["mug of ale", "torn map"],
           "enemies": [],
           "visited": False
       },
       "courtyard": {
           "name": "The Cobblestone Courtyard",
           "description": "Open sky overhead...",
           "exits": {"south": "tavern", "west": "alley"},
           "items": [],
           "enemies": ["pickpocket"],
           "visited": False
       },
       ...
   }
   ```

   Build out 10-15 rooms. Each room has: name, description,
   exits dict (direction → room_id), items list, enemies list,
   visited flag.

2. **The `describe_room(room_id)` function rewrite.** Use the
   new structure:
   - Get the room dict by id with `world.get(room_id)`. Handle
     missing rooms gracefully.
   - Print the name and description.
   - If items list is non-empty, print them.
   - If enemies list is non-empty, announce them.
   - Print the exits.

3. **The `go` command rewrite.** Look up the current room's exits
   dict. If the direction is a key, the value is the new room_id;
   move the player there. If not, "you can't go that way."

4. **The inventory dictionary refactor.** Make player_inventory
   a dict mapping `item_name → quantity`:
   ```
   player_inventory = {"healing potion": 2, "torch": 1}
   ```
   Update add/remove to update quantities, handle the 0-quantity
   case (remove the key).

5. **A dict comprehension somewhere.** Build a "visited rooms"
   summary using one.

6. **The `items()` iteration.** Use it to print the inventory:
   ```
   for item, qty in player_inventory.items():
       print(f"  {item}: {qty}")
   ```

In addition to the code, produce the prompt + tests. Tests:
- The describe_room function works for every room.
- The go command moves the player correctly.
- The inventory tracks quantities (taking 2 potions and using 1
  leaves 1).

End with: name one design decision where dict vs. list was a
hard call. What did you pick and why?
```

---

**What this produces:** A world represented as a nested dict + an inventory dict. Major architectural improvement.

**Connection to previous chapters:** Ch 9's parallel-list awkwardness gets resolved; the game structure is now Pythonic.

**Preview of next chapter:** Chapter 11 covers classes. You'll create Player, Enemy, and Item classes — turning the dict-based design into a proper OOP design.

---

## AI Wayback Machine

**Hans Peter Luhn** invented the hash function in 1953 for an IBM project — the technique that makes Python dictionaries fast.

**Run this:**

```
Who is Hans Peter Luhn, and how does their work connect to dictionaries we covered in this chapter? Keep it to three paragraphs. End with the single most surprising thing about their career or ideas.
```

→ Search **"Hans Peter Luhn"** on Wikipedia.

**Now make the prompt better.** Try one of these:

- Ask it to apply Hans Peter Luhn's ideas to a small Python program you'd write.
- Add a constraint: "Answer including criticisms or limits of Hans Peter Luhn's framework."

What changes? What gets better? What gets worse?
