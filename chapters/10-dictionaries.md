# Chapter 10 — Dictionaries


## TL;DR

- TL;DR: A dictionary is a container that maps immutable keys to values and retrieves them in constant time.
- You will practice Create a dictionary using curly braces or the dict() constructor; Access values by key using square brackets or the ; Mutate dictionaries by adding, updating, and deleting key-value pairs.
- The chapter moves through Suggested titles, Cold open, Learning objectives, Prerequisites, and related ideas.

## Suggested titles

1. The Phonebook Lookup: Why O(1) Matters
2. Dictionaries: The Data Structure That Powers the Real World
3. Keys and Values: Building Python's Most Practical Container

---

**TL;DR:** A dictionary is a container that maps immutable keys to values and retrieves them in constant time. It is Python's workhorse for real-world data. You'll learn to create dicts with `{}` or `dict()`, access values safely with `[]` or `.get()`, mutate them with assignment and `.update()`, and iterate over keys, values, or items. The rule that beats most dictionary bugs: use `.get(key, default)` instead of `dict[key]` when the key might not exist.

---

## Cold open

Picture a phone book on a shelf — leather-bound, pages yellowed, the names of a whole town compressed into thin paper. You need someone's number. You don't start at page one and read every entry. You turn to the section starting with the first letter of the last name. Then you scan for the name. Then you look to the right, and there: the number. One lookup, a few seconds. Constant time, regardless of whether the book has a thousand names or a million.

That is the entire logic of a dictionary in Python. The name — the last name, the key — tells you exactly where to find the value. No linear search. No scanning the whole structure. One lookup, and you're done. This speed matters. When Google's engineers store a billion web pages, they do not use lists and search them linearly. They use hash-based dictionaries that retrieve a page in one lookup, every single time.

In Python, a dictionary is written with curly braces. Keys and values are separated by colons. Multiple pairs are separated by commas. Here is what a small phone book looks like:

```python
phone_book = {
    "Alice Smith": "555-0147",
    "Bob Johnson": "555-0198",
    "Carol Davis": "555-0205"
}
```

The string on the left of each colon — `"Alice Smith"` — is the key. The string on the right — `"555-0147"` — is the value. To find Alice's number, you look up the key:

```python
print(phone_book["Alice Smith"])  # Prints: 555-0147
```

One lookup. Constant time. This chapter teaches you how dictionaries work, how to build them, how to add and remove entries, and why they are the most practical data structure in real-world Python.

### Learning objectives

By the end of this chapter you will be able to:

- **Create** a dictionary using curly braces or the `dict()` constructor.
- **Access** values by key using square brackets or the `.get()` method, and understand the difference in behavior when a key is missing.
- **Mutate** dictionaries by adding, updating, and deleting key-value pairs.
- **Iterate** over dictionary keys, values, or items, and use dictionary comprehensions to create dicts in a single expression.
- **Debug** the most common dictionary errors: `KeyError`, unhashable keys, and the mistake of treating a dict like a list.

### Prerequisites

Chapter 9 (Lists) — understanding indexing and mutation. Chapter 5 (Loops) and Chapter 4 (Decisions) — comfortable with `for` loops and `if` statements. A working grasp of what immutable means (if a value cannot be changed after creation, it is immutable).

### Why this chapter matters

Dictionaries are the data structure that bridges programming and the real world. Whenever you have a lookup table — student ID to grade, username to password, city to zip code, product name to inventory count — you are building a dictionary. JSON, the standard format for moving data across the internet, is a dictionary inside a dictionary inside a dictionary. Every API you call returns JSON. Every database query returns rows that are treated as dictionaries. The language you write to ask databases for information — SQL — is built on set operations and key-based lookups that dictionaries make visible and concrete.

If you master lists in Chapter 9 and dictionaries now, you have the two data structures that handle most of what Python is asked to do in the world.

---

## Concept 1 — Creating dictionaries and accessing their values

A dictionary — same root as the lexicographer's tool — is a structure that maps a *key* (the word you look up) to a *value* (the definition you find). In Python, a dictionary object can store any number of key-value pairs. Each key must be unique. Each key must be immutable — that is, you cannot change it after you create the dictionary. Values can be anything, including other dictionaries.

### The rule: keys must be immutable

Before you can write a single dictionary into code, you need to understand this one rule: **keys must be immutable**. Immutable means you cannot change the object after creation. Strings are immutable. Numbers are immutable. Tuples are immutable. Lists are mutable — you can change them after creation — so lists cannot be keys. Dictionaries are mutable, so dictionaries cannot be keys either.

Why? Because a dictionary uses a mathematical trick called *hashing* — from Old French *hacher*, meaning to chop. When you create a dictionary entry, Python computes a hash of the key: a fingerprint that maps the key to a storage location. If a key could change, its hash would change, and Python would never find it again. Immutable keys guarantee that the hash stays the same.

Here are the types you can use as keys:

- Strings: `"name"`, `"ID"`, `"timestamp"`
- Integers: `42`, `0`, `-5`
- Tuples: `(1, 2)`, `("first", "last")`
- Booleans: `True`, `False`
- None: `None` (the value that means "nothing")

Here are the types you cannot use as keys:

- Lists: `[1, 2, 3]` — mutable
- Dictionaries: `{"a": 1}` — mutable
- Sets: `{1, 2, 3}` — mutable

If you try, Python raises an error:

```python
bad_dict = {[1, 2]: "oops"}  # TypeError: unhashable type: 'list'
```

The error message says "unhashable" — it means the type cannot be hashed, so it cannot be a key.

### Creating a dictionary: curly braces

The simplest way to create a dictionary is with curly braces `{}`. Inside the braces, you list key-value pairs separated by colons, and separate pairs with commas.

```python
student_grades = {
    "Alice": 92,
    "Bob": 88,
    "Carol": 95,
    "David": 85
}
```

This creates a dictionary with four entries. The keys are strings. The values are integers. You can print it:

```python
print(student_grades)
# Prints: {'Alice': 92, 'Bob': 88, 'Carol': 95, 'David': 85}
```

Python also lets you create an empty dictionary and add entries later:

```python
empty_dict = {}
print(type(empty_dict))  # Prints: <class 'dict'>
```

### Accessing values: the `[]` operator

To get a value out of a dictionary, you use the key in square brackets, just like indexing a list. But instead of using a number, you use the key.

```python
print(student_grades["Alice"])  # Prints: 92
print(student_grades["Carol"])  # Prints: 95
```

If the key exists, you get the value. If the key does not exist, you get a `KeyError`:

```python
print(student_grades["Zoe"])  # KeyError: 'Zoe'
```

The error message tells you exactly what happened: the key `'Zoe'` is not in the dictionary.

### Accessing values safely: the `.get()` method

The `[]` operator is direct but dangerous. If the key might not exist, you crash with a `KeyError`. Python offers a gentler method: `.get()`. It takes two arguments: the key you're looking for, and a default value to return if the key is not there.

```python
print(student_grades.get("Alice", "No grade found"))  # Prints: 92
print(student_grades.get("Zoe", "No grade found"))  # Prints: No grade found
```

If you don't specify a default, `.get()` returns `None`:

```python
print(student_grades.get("Zoe"))  # Prints: None
```

This is the safer way. When you're not certain a key exists, use `.get()`. When you know it exists and want to fail loudly if it doesn't, use `[]`.

### Getting all keys, all values, or all pairs

Sometimes you want to see everything in the dictionary. Python provides three methods:

- `.keys()` — returns all the keys
- `.values()` — returns all the values
- `.items()` — returns all key-value pairs as tuples

```python
print(student_grades.keys())
# Prints: dict_keys(['Alice', 'Bob', 'Carol', 'David'])

print(student_grades.values())
# Prints: dict_values([92, 88, 95, 85])

print(student_grades.items())
# Prints: dict_items([('Alice', 92), ('Bob', 88), ('Carol', 95), ('David', 85)])
```

Notice the return types: `dict_keys`, `dict_values`, `dict_items`. These are special dictionary view objects. They are not lists, but they are iterable — you can loop over them. If you need an actual list, convert them:

```python
keys_as_list = list(student_grades.keys())
print(keys_as_list)  # Prints: ['Alice', 'Bob', 'Carol', 'David']
```

### Worked example — looking up a student's grade

Write a program that takes a student's name and returns their grade. If the student is not in the dictionary, return a message saying they were not found.

Given:

```python
student_grades = {"Alice": 92, "Bob": 88, "Carol": 95, "David": 85}
```

The solution uses `.get()` with a default message:

```python
def lookup_grade(name):
    """Look up a student's grade. Return a message if not found."""
    grade = student_grades.get(name, "Student not found")
    return grade

print(lookup_grade("Alice"))  # Prints: 92
print(lookup_grade("Zoe"))  # Prints: Student not found
```

Check: the function returns 92 for Alice (who is in the dict) and a message for Zoe (who is not). The `.get()` method prevents a crash.

General lesson: when a key might not exist, `.get()` is safer than `[]`. The trade-off is that you have to think ahead about what default value makes sense.

### Common misconceptions

**"I can use a list as a key if I convert it to a tuple first."** — False. You must use an immutable type as a key when you create the dictionary. A list is mutable, so it cannot be a key, ever.

**"Empty curly braces create an empty list."** — False. `{}` creates an empty dictionary. `[]` creates an empty list. The difference matters.

**"`.get()` is always safer than `[]`."** — Not quite. If you know a key should exist and the program is broken if it doesn't, use `[]` and let it crash loudly. Silence from `.get()` can hide bugs. Use `.get()` when missing keys are expected and handled.

---

## Concept 2 — Mutating dictionaries: adding, updating, and deleting

A dictionary is mutable — you can change it after creation. You can add new entries, update existing values, and delete entries entirely.

### Adding and updating with `[]`

To add a new entry, assign a value to a key that does not yet exist:

```python
student_grades = {"Alice": 92, "Bob": 88}
student_grades["Carol"] = 95
print(student_grades)
# Prints: {'Alice': 92, 'Bob': 88, 'Carol': 95}
```

To update an existing entry, assign a new value to an existing key:

```python
student_grades["Alice"] = 96
print(student_grades)
# Prints: {'Alice': 96, 'Bob': 88, 'Carol': 95}
```

Python does not care if the key already exists. If it does, the value is replaced. If it does not, the entry is created. This is clean and direct, but it silently overwrites. If you want to be certain you are adding something new and not replacing something old, you have to check first:

```python
if "David" not in student_grades:
    student_grades["David"] = 85
```

### Adding and updating with `.update()`

The `.update()` method adds or replaces multiple entries at once. It takes a dictionary as an argument:

```python
student_grades.update({"David": 85, "Eve": 91})
print(student_grades)
# Prints: {'Alice': 96, 'Bob': 88, 'Carol': 95, 'David': 85, 'Eve': 91}
```

This is cleaner when you have many entries to add at once.

### Deleting with `del`

To delete an entry, use the `del` keyword with the key:

```python
del student_grades["Bob"]
print(student_grades)
# Prints: {'Alice': 96, 'Carol': 95, 'David': 85, 'Eve': 91}
```

If the key does not exist, `del` raises a `KeyError`:

```python
del student_grades["Zoe"]  # KeyError: 'Zoe'
```

### Deleting safely with `.pop()`

The `.pop()` method removes an entry and returns its value:

```python
removed_grade = student_grades.pop("Alice")
print(removed_grade)  # Prints: 96
print(student_grades)
# Prints: {'Carol': 95, 'David': 85, 'Eve': 91}
```

Like `.get()`, `.pop()` can take a default value. If the key does not exist, it returns the default instead of raising an error:

```python
removed_grade = student_grades.pop("Zoe", "Key not found")
print(removed_grade)  # Prints: Key not found
```

This is the safe way to remove something when you are not certain it exists.

### Worked example — word frequency counter

Write a program that counts how many times each word appears in a sentence. Store the counts in a dictionary.

Given a sentence:

```python
sentence = "the quick brown fox jumps over the lazy dog the fox"
```

The solution splits the sentence into words, then uses a dictionary to count:

```python
def count_words(sentence):
    """Count the frequency of each word in a sentence."""
    words = sentence.lower().split()
    word_count = {}
    
    for word in words:
        if word in word_count:
            word_count[word] += 1
        else:
            word_count[word] = 1
    
    return word_count

result = count_words("the quick brown fox jumps over the lazy dog the fox")
print(result)
# Prints: {'the': 3, 'quick': 1, 'brown': 1, 'fox': 2, 'jumps': 1, 'over': 1, 'lazy': 1, 'dog': 1}
```

Check: "the" appears three times, "fox" appears twice, all others once. The counts are correct.

Alternative approach using `.get()`:

```python
def count_words_v2(sentence):
    """Count words using .get() to avoid the if/else."""
    words = sentence.lower().split()
    word_count = {}
    
    for word in words:
        word_count[word] = word_count.get(word, 0) + 1
    
    return word_count
```

This is shorter and cleaner. For each word, get its current count (defaulting to 0 if it doesn't exist) and add 1.

General lesson: dictionaries excel at counting and lookups. When you see a problem like "count occurrences" or "store related data by a key," reach for a dictionary.

### Common misconceptions

**"Dictionaries remember insertion order, so I can count on it."** — True in Python 3.7+, but it's an implementation detail, not guaranteed by the spec. Treat insertion order as a bonus, not a guarantee. If order matters, use other data structures.

**"I can use `.pop()` to delete without checking if the key exists."** — Yes, but only if you provide a default. Without a default, `.pop()` still raises a `KeyError` if the key is missing.

**"Assignment to a new key and assignment to an existing key are different operations."** — They're not. The `[]` operator does both: create or update, depending on whether the key exists.

---

## Concept 3 — Iterating and comprehending

Dictionaries are iterable. You can loop over them using `for` loops, and you can create them using comprehensions — a concise syntax that builds a dictionary from an iterable.

### Looping over keys

When you loop directly over a dictionary, you loop over its keys:

```python
student_grades = {"Alice": 92, "Bob": 88, "Carol": 95}

for student in student_grades:
    print(student)
# Prints:
# Alice
# Bob
# Carol
```

This is shorthand for:

```python
for student in student_grades.keys():
    print(student)
```

The two are equivalent.

### Looping over values

To loop over just the values, use `.values()`:

```python
for grade in student_grades.values():
    print(grade)
# Prints:
# 92
# 88
# 95
```

### Looping over key-value pairs

To loop over both keys and values together, use `.items()`. Each iteration gives you a tuple of `(key, value)`. You can unpack it directly:

```python
for student, grade in student_grades.items():
    print(f"{student}: {grade}")
# Prints:
# Alice: 92
# Bob: 88
# Carol: 95
```

The unpacking `student, grade` is the key. Without it, you'd have to write:

```python
for item in student_grades.items():
    print(item)
# Prints:
# ('Alice', 92)
# ('Bob', 88)
# ('Carol', 95)
```

### Dictionary comprehensions

A *comprehension* is a one-line way to build a data structure. A dictionary comprehension builds a dictionary from an iterable.

The syntax is:

```python
{key_expression: value_expression for item in iterable}
```

For example, create a dictionary of the first five positive integers and their squares:

```python
squares = {n: n**2 for n in range(1, 6)}
print(squares)
# Prints: {1: 1, 2: 4, 3: 9, 4: 16, 5: 25}
```

This is equivalent to the longer version:

```python
squares = {}
for n in range(1, 6):
    squares[n] = n**2
```

But the comprehension is one line and reads like a mathematical set.

You can add conditions:

```python
# Only odd numbers and their squares
odd_squares = {n: n**2 for n in range(1, 10) if n % 2 == 1}
print(odd_squares)
# Prints: {1: 1, 3: 9, 5: 25, 7: 49, 9: 81}
```

Comprehensions are not just shorter — they are idiomatic Python. When you see the need to build a dictionary from an iterable, reach for a comprehension.

### Worked example — mapping names to student ID numbers

You have a list of tuples, each containing a student ID and a name. Create a dictionary that maps names to IDs.

Given:

```python
students = [(1001, "Alice"), (1002, "Bob"), (1003, "Carol")]
```

The solution uses a dictionary comprehension to flip the tuple from `(ID, name)` to `name: ID`:

```python
def name_to_id(students):
    """Create a dictionary mapping names to student IDs."""
    return {name: student_id for student_id, name in students}

result = name_to_id([(1001, "Alice"), (1002, "Bob"), (1003, "Carol")])
print(result)
# Prints: {'Alice': 1001, 'Bob': 1002, 'Carol': 1003}
```

Check: Alice maps to 1001, Bob to 1002, Carol to 1003. The mapping is correct and can now be used for fast lookup.

General lesson: comprehensions excel when you're transforming or filtering data into a new structure. The syntax is dense, but it's worth learning because it appears everywhere in idiomatic Python.

### Common misconceptions

**"I have to use `.keys()` when I loop over a dictionary."** — No. Looping directly over a dictionary gives you the keys. `for k in d:` and `for k in d.keys():` are equivalent.

**"Comprehensions are just syntax sugar; they're not really faster."** — They are faster in practice. Comprehensions are optimized in Python's implementation and execute faster than the equivalent loop.

**"I can use a comprehension for anything I'd use a loop for."** — No. Comprehensions build a new data structure. If you're doing something with side effects (printing, writing to a file, modifying an existing dict), use a loop.

---

## Integration — the inventory system

Return to the cold open. You're managing a bookstore. Your inventory is a dictionary: book titles (strings, immutable) map to quantities (integers, mutable values). You need to:

1. Look up how many copies of a title you have.
2. Restock when a shipment arrives.
3. Sell books and decrement the count.
4. Flag books that are out of stock.
5. Prepare a report of all titles and their counts.

Here is the complete program:

```python
# Initialize inventory
inventory = {
    "Python Crash Course": 5,
    "Automate the Boring Stuff": 3,
    "Clean Code": 2,
    "The Pragmatic Programmer": 0
}

# 1. Look up a title
def check_stock(title):
    """Check how many copies we have. Default to 0 if not found."""
    return inventory.get(title, 0)

# 2. Restock when a shipment arrives
def restock(title, quantity):
    """Add new books to inventory or update existing count."""
    inventory[title] = inventory.get(title, 0) + quantity

# 3. Sell a book
def sell(title):
    """Remove one book from inventory if available. Return success/failure."""
    count = inventory.get(title, 0)
    if count > 0:
        inventory[title] -= 1
        return True
    return False

# 4. Check for out-of-stock titles
def out_of_stock():
    """Return a list of titles with zero copies."""
    return [title for title, count in inventory.items() if count == 0]

# 5. Report all inventory
def report():
    """Print a formatted inventory report."""
    for title, count in inventory.items():
        status = "OUT OF STOCK" if count == 0 else f"({count} copies)"
        print(f"{title}: {status}")

# Run the program
print(check_stock("Python Crash Course"))  # Prints: 5
print(check_stock("Not a Real Book"))  # Prints: 0

restock("Python Crash Course", 3)
print(check_stock("Python Crash Course"))  # Prints: 8

sell("Python Crash Course")
print(check_stock("Python Crash Course"))  # Prints: 7

print(out_of_stock())  # Prints: ['The Pragmatic Programmer']

report()
# Prints:
# Python Crash Course: (8 copies)
# Automate the Boring Stuff: (3 copies)
# Clean Code: (2 copies)
# The Pragmatic Programmer: OUT OF STOCK
```

Every operation in this program uses one of the three concepts from this chapter:

- Creating the dictionary with `{...}` syntax.
- Accessing values safely with `.get()` and with `[]`.
- Mutating the dictionary with assignment (`inventory[title] = ...`).
- Iterating with `.items()` and a comprehension for the out-of-stock list.

The dictionary is the right tool because lookups by title are constant-time, and the structure mirrors the real world: each book has a quantity.

---

## Exercises

### Warm-up

**Exercise 10.1** *(LO: create and access).* Create a dictionary `fruit_colors` with three fruits as keys and their colors as values (e.g., `{"apple": "red", "banana": "yellow", "grape": "purple"}`). Then print the color of the apple using both `[]` and `.get()`.

**Exercise 10.2** *(LO: understand keys).* Which of the following can be dictionary keys? For each, explain why or why not.
- (a) `"name"` (a string)
- (b) `42` (an integer)
- (c) `[1, 2, 3]` (a list)
- (d) `(1, 2)` (a tuple)
- (e) `{"a": 1}` (a dictionary)

**Exercise 10.3** *(LO: access safely).* You have a dictionary `person = {"name": "Alice", "age": 30}`. Write code using `.get()` to safely retrieve the value for key `"email"` with a default of `"unknown"`.

### Application

**Exercise 10.4** *(LO: mutate).* Start with an empty dictionary. Add three key-value pairs using square bracket assignment. Then update one value and delete another. Print the dictionary after each step.

**Exercise 10.5** *(LO: iterate).* Given `student_grades = {"Alice": 92, "Bob": 88, "Carol": 95}`, write a `for` loop that prints "Alice: 92", "Bob: 88", etc. (one line per student).

**Exercise 10.6** *(LO: comprehension).* Create a dictionary `word_lengths` that maps each word in the list `["python", "dictionary", "code"]` to its length. Use a dictionary comprehension.

### Synthesis

**Exercise 10.7** *(LO: synthesis — word frequency).* Write a function that takes a string, splits it into words, and returns a dictionary where each key is a word and each value is the number of times the word appears. Test it on the sentence "the fox jumps over the fox".

**Exercise 10.8** *(LO: synthesis — nested dict).* Create a dictionary representing a small organization:

```python
org = {
    "Engineering": {"Alice": "Senior Engineer", "Bob": "Junior Engineer"},
    "Sales": {"Carol": "Sales Lead", "David": "Sales Rep"}
}
```

Write code to:
- (a) Print the role of Alice.
- (b) Add a new person "Eve" as "Intern" in Engineering.
- (c) Print all people in the Sales department.

**Exercise 10.9** *(LO: synthesis — `.items()` and `.pop()`).* You have a dictionary of product prices: `{"apple": 0.50, "banana": 0.30, "orange": 0.60}`. Write code that:
- (a) Prints all items and their prices.
- (b) Removes the cheapest item (banana) using `.pop()`.
- (c) Prints the updated dictionary.

### Challenge

**Exercise 10.10** *(LO: open-ended).* A restaurant menu is stored as a nested dictionary: `menu = {"appetizers": {"soup": 8, "salad": 10}, "mains": {"pasta": 15, "steak": 28}}`. Write a function that takes a category and a dish name and returns the price. What happens if the category or dish does not exist? How would you handle that safely?

**Exercise 10.11** *(LO: synthesis + error handling).* Write a program that:
- Reads a list of names and test scores.
- Creates a dictionary mapping names to scores.
- Allows the user to look up a student's score by name using `.get()`.
- Handles the case where the student doesn't exist.
- Prints the average score across all students.

---

## Claude Code — building a word frequency counter

The patterns in this chapter condense into one concrete skill: *building a dictionary to count, lookup, or group data*. This section walks you through asking Claude to write a word frequency counter, then checking that it works.

### The problem

You're analyzing a speech. You want to know which words appear most often. The solution is a dictionary that maps each word to its count, plus a helper that returns the top *n* most frequent words.

Specification: `word_counts(text)` takes a string, returns a dictionary where keys are lowercase words (punctuation stripped) and values are their counts. `top_n(text, n)` takes the same text and returns the *n* most frequent words as a list of `(word, count)` tuples sorted in descending order by count.

### The code

```python
import string

def word_counts(text):
    """Count occurrences of each word in text (case-insensitive, no punctuation)."""
    # Remove punctuation and convert to lowercase
    cleaned = text.translate(str.maketrans('', '', string.punctuation)).lower()
    words = cleaned.split()
    
    # Count words using a dictionary
    counts = {}
    for word in words:
        counts[word] = counts.get(word, 0) + 1
    
    return counts

def top_n(text, n):
    """Return the n most frequent words as (word, count) tuples, sorted descending."""
    counts = word_counts(text)
    # Sort by count (second item of the tuple) in descending order
    sorted_words = sorted(counts.items(), key=lambda item: item[1], reverse=True)
    return sorted_words[:n]

# Test on the Gettysburg Address (opening lines)
speech = """
Four score and seven years ago our fathers brought forth on this continent,
a new nation, conceived in Liberty, and dedicated to the proposition that all men are created equal.
Now we are engaged in a great civil war, testing whether that nation, or any nation so conceived
and so dedicated, can long endure. We are met on a great battle-field of that war.
"""

print("Word counts:", word_counts(speech))
print("\nTop 5 most frequent words:")
for word, count in top_n(speech, 5):
    print(f"  {word}: {count}")
```

Output:
```
Word counts: {'four': 1, 'score': 1, 'and': 4, 'seven': 1, 'years': 1, ...}

Top 5 most frequent words:
  and: 4
  nation: 3
  a: 3
  that: 3
  are: 2
```

### Dissecting the code

**Line 3–5:** `word_counts()` uses `.translate()` and `str.punctuation` to remove all punctuation, then `.lower()` to standardize case.

**Line 6:** `.split()` with no argument splits on whitespace.

**Line 9–11:** The core counting pattern. For each word, fetch its current count (defaulting to 0 if the key doesn't exist) and add 1. This is the idiom: `dict[key] = dict.get(key, 0) + 1`.

**Line 16–17:** `top_n()` calls `word_counts()`, then uses `sorted()` with a `key` function. The `lambda item: item[1]` extracts the second element of each `(word, count)` tuple. `reverse=True` sorts descending (highest counts first).

**Line 18:** Return the first `n` items using slicing.

**Line 25–28:** Test on real text (the opening of the Gettysburg Address). Notice that "and" appears 4 times, "nation" and "a" and "that" each appear 3 times.

### Prompt for Claude

```
Write two Python functions for word frequency analysis:

1. word_counts(text) — takes a string, returns a dictionary mapping each word 
   (lowercased, no punctuation) to its count.

2. top_n(text, n) — takes a string and an integer n, returns a list of (word, count) 
   tuples for the n most frequent words, sorted descending by count.

Requirements:
- Remove punctuation before counting
- Case-insensitive
- Use .get() for safe dictionary access
- For top_n(), use sorted() with a lambda key function
- Include docstrings

Test on this text:
"the fox jumps over the lazy dog the fox"
```

### Tests to validate

1. **Basic word count:**
   ```python
   result = word_counts("hello world hello")
   assert result == {"hello": 2, "world": 1}
   ```

2. **Case insensitive:**
   ```python
   result = word_counts("Python python PYTHON")
   assert result == {"python": 3}
   ```

3. **Punctuation handling:**
   ```python
   result = word_counts("Hello, world! Hello.")
   assert "hello" in result
   assert result["hello"] == 2
   assert "," not in result  # punctuation removed
   ```

4. **Empty input:**
   ```python
   result = word_counts("")
   assert result == {}
   ```

5. **Top n functionality:**
   ```python
   counts = top_n("the fox jumps over the lazy dog the fox", 3)
   assert len(counts) == 3
   assert counts[0] == ("the", 3)  # "the" is most frequent
   assert counts[1][1] >= counts[2][1]  # descending order
   ```

6. **Top n with n > unique words:**
   ```python
   counts = top_n("hello world", 5)
   assert len(counts) == 2  # only 2 unique words
   ```

### What Claude Code teaches

This example reinforces four patterns you'll use constantly:

1. **Safe lookup with `.get()`**: `dict.get(key, 0)` is how you avoid `KeyError` when building a dictionary incrementally.

2. **Iteration over `.items()`**: `for word, count in counts.items()` unpacks the tuple so you have both the key and value available.

3. **Dictionary comprehension alternative**: You could also write:
   ```python
   # Equivalent to the loop in top_n:
   sorted_words = sorted(counts.items(), key=lambda item: item[1], reverse=True)
   ```
   This combines dictionary access (`.items()`), sorting with a custom key, and slicing — all things you now understand.

4. **Mutation as you go**: Notice that in `word_counts()`, you're mutating the `counts` dictionary on every iteration. The dictionary grows from empty to full. This is safe because you control it — no external code is modifying it at the same time.

### Why this matters

Word frequency is a real tool. Every search engine ranks pages partly on word frequency. Every text analysis system starts with counting. The code above is small enough to understand fully but large enough that you have to think about the pieces: splitting, case-handling, punctuation-stripping, dictionaries, sorting, slicing. And because you can ask Claude to write it, validate it, and modify it, you're learning what it means to *specify* a real requirement to a machine.

---

## Chapter summary

You can now do five things you could not do an hour ago.

You can create a dictionary using curly braces or the `dict()` function, and you understand the immutable-key rule that keeps the hash function stable.

You can retrieve values safely using either `[]` for direct access or `.get()` for access with a default, and you know when to use each.

You can mutate a dictionary by adding, updating, and deleting entries, using either assignment or the dedicated methods `.update()`, `.pop()`, and `del`.

You can loop over a dictionary in three ways — over keys, over values, or over key-value pairs using `.items()` — and you can write dictionary comprehensions to build dicts in one line.

And you have seen dictionaries solving real problems: word frequency counting, inventory management, and hierarchical data storage. You have the mental model that dictionaries are fast lookups, not ordered collections. (They preserve insertion order as of Python 3.7, but do not depend on it for your program's logic.)

The thing to watch for, going forward, is the **KeyError**. Almost every dictionary bug in beginner code comes from accessing a key that doesn't exist. Use `.get()` when the key might be missing. Use `[]` when you're confident it's there. Check with `in` before deleting:

```python
if title in inventory:
    del inventory[title]
```

What you should now be able to teach a friend who asks: why dictionaries are O(1) lookup, what immutable means and why it matters for keys, and when to reach for a dictionary instead of a list (when you're looking things up by a meaningful key, not a position).

---

## Connections forward

Chapter 11 (Classes) builds on dictionaries. Objects are, in one sense, dictionaries with methods attached. Every object has attributes — named slots that hold data — and attributes are stored and looked up using the same constant-time logic as dictionary keys.

Chapter 14 (Files) shows you that JSON, the most common format for moving data across the internet, is dictionaries all the way down. When you read data from an API, you get back nested dictionaries. When you write data to a file, you often convert it to JSON by dumping it as dictionaries.

And Chapter 15 (Data Science) leans on dictionaries heavily. pandas DataFrames are, at their core, dictionaries of columns. NumPy arrays support dictionary-like access when you use structured arrays. The indexing you'll learn there is fundamentally a fast lookup, which is what dictionaries are built to do.

The pattern from here forward: wherever you see lookup, think dictionary. Wherever you see key-value relationships in the world, think dictionary. The phone book, the organ register, the mail carrier's route, the API endpoint — all dictionaries under the hood.

---

## Three-part Claude code patterns

### Triple 1: Word frequency counter (for loops + dict mutation)

```python
def count_words(text):
    """Count how many times each word appears in a text."""
    words = text.lower().split()
    word_count = {}
    
    for word in words:
        if word in word_count:
            word_count[word] += 1
        else:
            word_count[word] = 1
    
    return word_count
```

> **Prompt for Claude.** "Write a Python function `count_words(text)` that takes a text string and returns a dictionary where each key is a word (lowercase) and each value is how many times it appears. Include a docstring. Hint: split the text into words, then loop through them, building up a dictionary."

> **Tests to validate.**
> - `count_words("hello world hello")` returns `{"hello": 2, "world": 1}`
> - `count_words("Python python PYTHON")` (case-insensitive) returns `{"python": 3}`
> - `count_words("")` returns `{}`
> - `len(count_words("one two three four five"))` equals `5` (five unique words)

---

### Triple 2: Safe grade lookup with `.get()` (access patterns)

```python
def student_grade_lookup(grades_dict, student_name, default="Not found"):
    """Look up a student's grade safely using .get()."""
    return grades_dict.get(student_name, default)

grades = {"Alice": 92, "Bob": 88, "Carol": 95}
print(student_grade_lookup(grades, "Alice"))  # Prints: 92
print(student_grade_lookup(grades, "Zoe"))  # Prints: Not found
```

> **Prompt for Claude.** "Write a Python function `student_grade_lookup(grades_dict, student_name, default='Not found')` that safely retrieves a student's grade from a dictionary. If the student is not in the dictionary, return the default value. Use the `.get()` method."

> **Tests to validate.**
> - `student_grade_lookup({"Alice": 92}, "Alice")` equals `92`
> - `student_grade_lookup({"Alice": 92}, "Bob")` equals `"Not found"`
> - `student_grade_lookup({"Alice": 92}, "Bob", "N/A")` equals `"N/A"`
> - Does NOT raise a `KeyError` for missing keys

---

### Triple 3: Dictionary comprehension for squared numbers (comprehensions)

```python
def squares_of_range(start, end):
    """Create a dictionary mapping numbers to their squares."""
    return {n: n**2 for n in range(start, end + 1)}

result = squares_of_range(1, 5)
print(result)  # Prints: {1: 1, 2: 4, 3: 9, 4: 16, 5: 25}
```

> **Prompt for Claude.** "Write a Python function `squares_of_range(start, end)` that returns a dictionary where each key is a number in the range [start, end] (inclusive) and each value is that number squared. Use a dictionary comprehension."

> **Tests to validate.**
> - `squares_of_range(1, 3)` equals `{1: 1, 2: 4, 3: 9}`
> - `squares_of_range(0, 0)` equals `{0: 0}`
> - `squares_of_range(5, 7)` equals `{5: 25, 6: 36, 7: 49}`
> - The function returns a dict, not a list

---

### Triple 4: Inventory restock with `.update()` (mutation)

```python
def restock_inventory(inventory, new_items):
    """Add or update multiple items in an inventory dictionary."""
    inventory.update(new_items)
    return inventory

stock = {"apples": 5, "bananas": 3}
stock = restock_inventory(stock, {"apples": 2, "oranges": 4})
print(stock)  # Prints: {'apples': 7, 'bananas': 3, 'oranges': 4}
```

> **Prompt for Claude.** "Write a Python function `restock_inventory(inventory, new_items)` that takes an inventory dictionary and a dictionary of new items. For items already in inventory, add the quantities; for new items, add them. Use the `.update()` method and ensure quantities add (don't replace). Include a docstring."

> **Tests to validate.**
> - `restock_inventory({"a": 5}, {"a": 3, "b": 2})` equals `{"a": 8, "b": 2}` (quantities add)
> - `restock_inventory({}, {"x": 1})` equals `{"x": 1}`
> - The function modifies and returns the original dict

---

### Triple 5: Conditional dictionary comprehension (comprehension + filtering)

```python
def even_squares(numbers):
    """Create a dict mapping only even numbers to their squares."""
    return {n: n**2 for n in numbers if n % 2 == 0}

result = even_squares([1, 2, 3, 4, 5, 6])
print(result)  # Prints: {2: 4, 4: 16, 6: 36}
```

> **Prompt for Claude.** "Write a Python function `even_squares(numbers)` that takes a list of numbers and returns a dictionary where keys are only the EVEN numbers and values are their squares. Use a dictionary comprehension with a condition. Include a docstring."

> **Tests to validate.**
> - `even_squares([1, 2, 3, 4, 5])` equals `{2: 4, 4: 16}`
> - `even_squares([1, 3, 5])` equals `{}` (no evens)
> - `even_squares([2, 4, 6])` equals `{2: 4, 4: 16, 6: 36}` (all evens)
> - Values are squared, not original

---

### Triple 6: Iterating `.items()` with unpacking (iteration)

```python
def salary_report(employee_salaries):
    """Print a formatted salary report from a dictionary."""
    for name, salary in employee_salaries.items():
        print(f"{name}: ${salary:,}")

salaries = {"Alice": 75000, "Bob": 65000, "Carol": 80000}
salary_report(salaries)
# Prints:
# Alice: $75,000
# Bob: $65,000
# Carol: $80,000
```

> **Prompt for Claude.** "Write a Python function `salary_report(employee_salaries)` that takes a dictionary of employee names and salaries and prints a formatted report. Each line should be `Name: $salary` with the salary formatted with commas. Use `.items()` and tuple unpacking in a `for` loop. Include a docstring."

> **Tests to validate.**
> - Function prints (not returns) the report
> - Format is exactly `Alice: $75,000` (with colon and dollar sign)
> - Works for any dictionary size
> - Salary is formatted with thousands separators

---

### Triple 7: Safe deletion with `.pop()` (error handling)

```python
def remove_student(grades, student_name):
    """Remove a student from the grades dictionary safely."""
    removed_grade = grades.pop(student_name, None)
    if removed_grade is not None:
        print(f"Removed {student_name} with grade {removed_grade}")
    else:
        print(f"{student_name} was not in the grades dictionary")
    return grades

grades = {"Alice": 92, "Bob": 88}
remove_student(grades, "Alice")  # Prints: Removed Alice with grade 92
remove_student(grades, "Zoe")  # Prints: Zoe was not in the grades dictionary
```

> **Prompt for Claude.** "Write a Python function `remove_student(grades, student_name)` that removes a student from the grades dictionary using `.pop()`. If the student exists, print a message with their grade. If they don't exist, print a message saying they weren't found. Return the updated dictionary. Include a docstring."

> **Tests to validate.**
> - `.pop()` is used (not `del`)
> - Function handles missing keys without crashing
> - Correct message printed for existing student
> - Correct message printed for non-existent student
> - Returns the modified dictionary

---

**What would change my mind:** If Python dictionaries were not O(1) lookup (they are due to hashing), or if immutable keys didn't matter because hashing didn't matter, this chapter's entire foundation would shift. It would be important to know that some languages use other data structures for key-value lookups that are O(log n) or O(n).

**Still puzzling:** Why does Python allow duplicate values in a dictionary (you can have two keys pointing to the same value) but not duplicate keys? The answer is that values are looked up by key, so uniqueness of values doesn't matter. But the moment two keys pointed to different values, which one would you get when you looked up that shared value? The design choice is: keys are unique, values are not. This is the right choice, but the asymmetry is worth naming.

---

## Tags

#dictionaries #hash-tables #key-value-pairs #python-data-structures #constant-time-lookup #iteration #comprehensions
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

##  AI Wayback Machine
**Hans Peter Luhn** was invented the hash function in 1953 for an IBM project — the technique that makes Python dictionaries fast.

**Run this:**

```
Who is Hans Peter Luhn, and how does their work connect to dictionaries we covered in this chapter? Keep it to three paragraphs. End with the single most surprising thing about their career or ideas.
```

→ Search **"Hans Peter Luhn"** on Wikipedia.

**Now make the prompt better.** Try one of these:

- Ask it to apply Hans Peter Luhn's ideas to a small Python program you'd write.
- Add a constraint: "Answer including criticisms or limits of Hans Peter Luhn's framework."

What changes? What gets better? What gets worse?
