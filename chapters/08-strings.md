# Chapter 8 — Strings

## Three possible titles

- **What a string is and what it becomes: indexing, slicing, methods, and the gap between intention and bytes**
- **Text as data: how Python treats strings, the operations that make them useful, and why strings are immutable**
- **From characters to messages: building, searching, formatting, and transforming text in Python**

## TL;DR

A string is a sequence of characters — the machinery for handling text. Python provides indexing (get one character by position), slicing (get a substring), methods (transform or search), and formatting (build strings from templates). The key trade-off: strings are immutable, so changes require building a new string instead of editing the old one in place.

---

## Cold open

You're in a group chat. Someone types your name. Your phone screen lights up. What just happened? Your name — call it the string `"Alex"` — was transmitted across networks as a sequence of bytes, matched character by character against the list of users in the group, found, and your phone received a notification. That entire process, underneath, is string operations. This chapter teaches you the tools. The machinery is simpler than the physics that carries it.

### Learning objectives

By the end of this chapter you will be able to:

- **Index** a string to retrieve individual characters by position, using both positive and negative indices.
- **Slice** a string to extract substrings, and understand that slicing is non-destructive.
- **Apply** string methods like `.upper()`, `.lower()`, `.split()`, `.join()`, `.find()`, `.count()` to transform and search.
- **Compare** strings using operators (`==`, `!=`, `<`, `>`, `in`) and understand that comparison uses character order.
- **Format** strings using `.format()` and f-strings to build output with variable data.
- **Explain** why strings are immutable and what that means for string manipulation.
- **Recognize** and fix common string errors: `IndexError`, `AttributeError`, `TypeError`.

### Prerequisites

- Chapter 1 (Statements) — understanding variables and `print()`.
- Chapter 3 (Objects) — comfort with the idea that values have types and methods.

### Why this chapter matters

Text is how humans and computers communicate. Every email, every search query, every message you send is a string. Every program that handles user input, reads from files, or formats output needs to manipulate strings. This chapter teaches you the fundamental operations: finding characters, extracting pieces, transforming case, finding substrings, building formatted messages. These operations are the foundation of almost everything you'll write that touches the outside world.

---

## Concept 1 — Indexing and slicing: getting at the characters

A string is a sequence. The computer needs to know where each character sits. That's indexing. And when you need a piece of the string — a word, a substring — that's slicing.

### Indexing: position matters

A **string** — from Latin *stringere*, to draw tight, to bind — is a sequence of characters held in order. Each character has a position. Python counts positions starting from 0. This is called **zero-indexing**, and it's standard in programming. The reason: when you think of a position as an *offset* from the beginning, the beginning is zero steps away.

```python
message = "python"
print(message[0]) # p
print(message[1]) # y
print(message[2]) # t
print(message[5]) # n
```

The first character is always at index 0. The second is at index 1. For a string of length `n`, the last character is at index `n - 1`. For `"python"`, length is 6, so the last character `"n"` is at index 5.

You can also count backward from the end. The last character is at index -1, the second-to-last at -2, and so on. This is useful when you want the last few characters without computing the string's length.

```python
message = "python"
print(message[-1]) # n (last character)
print(message[-2]) # o
print(message[-6]) # p (first character, since length is 6)
```

The rule: for a string of length `n`, index `k` and index `k - n` refer to the same character. So index 0 and index -6 both refer to the first character. Index 5 and index -1 both refer to the last.

What happens if you ask for an index that doesn't exist?

```python
message = "hi"
print(message[5]) # IndexError: string index out of range
```

The error message is clear: you asked for an index outside the valid range. A string of length `n` has valid indices from 0 to `n - 1`, or from `-n` to -1. Asking for index `n` or `10` will raise an `IndexError`. This is one of the most common errors when working with strings.

### Slicing: getting a substring

A **slice** — from Old English, meaning a thin flat piece cut from something larger — is a contiguous chunk of a sequence picked by start and stop position. Slicing is one of the most powerful tools in Python because it lets you extract substrings without loops.

```python
message = "python"
print(message[0:2]) # py
print(message[2:4]) # th
print(message[1:5]) # ytho
```

The syntax is `string[start:stop]`. It returns characters from position `start` up to (but not including) position `stop`. The stop index is *exclusive* — it's not included in the result. This is the most common point of confusion. Remember: `[start:stop]` gives you `stop - start` characters, starting from position `start`.

You can omit the start or stop to use defaults:

```python
message = "python"
print(message[:3]) # pyt (from position 0 up to 3)
print(message[3:]) # hon (from position 3 to the end)
print(message[:]) # python (the entire string)
```

Negative indices work in slices too:

```python
message = "python"
print(message[-3:]) # hon (last 3 characters)
print(message[:-2]) # pyth (all but the last 2)
print(message[-5:-2]) # yth (from the 5th-from-end to the 2nd-from-end)
```

A scale shift: what you're doing here — extracting a piece of a string by position — is the foundation of parsing. When a program reads a line like `"hh:mm:ss"` for time, or a fixed-format record from a file, slicing is how it breaks the line into fields.

What if you ask for a slice beyond the string's bounds? Python doesn't error. It just returns what's there.

```python
message = "hi"
print(message[0:10]) # hi (the entire string, since 10 is beyond the end)
print(message[-10:]) # hi (same; Python doesn't complain)
```

This is different from indexing. Indexing a single position that doesn't exist raises an error. Slicing a range that goes beyond the bounds doesn't.

### String immutability: a crucial property

Strings are **immutable** — they cannot be changed after creation. Once a string is made, its contents are fixed. This is a fundamental property, not a limitation you can work around.

```python
message = "hello"
message[0] = "H" # TypeError: 'str' object does not support item assignment
```

Why immutability? If strings could be modified in place, code that was using a string wouldn't know if another part of the program had secretly changed it. Immutability is a guarantee: the string you receive in a function is exactly the string you'll see when you use it.

To change a string, you build a new one:

```python
message = "hello"
message = "H" + message[1:] # Build a new string from parts
print(message) # Hello
```

Or more readably, using slicing and concatenation:

```python
original = "hello world"
modified = original[:5].upper() + original[5:] # "HELLO world"
```

This looks inefficient — creating a whole new string when you only wanted to change one character. And it is. But Python optimizes this when possible, and the trade-off (immutability for safety) is deliberate. You'll see mutable data structures (lists) in Chapter 9 that *do* allow in-place changes, and you'll understand when to use each.

### Worked example: extracting the first and last words from a sentence

Write a program that asks the user for a sentence, then prints the first and last words.

```python
sentence = input("Enter a sentence: ")
first_space = sentence.find(" ")
first_word = sentence[:first_space]
last_space = sentence.rfind(" ")
last_word = sentence[last_space + 1:]
print("First word:", first_word)
print("Last word:", last_word)
```

Test by running with input `"The quick brown fox"`:

```
Enter a sentence: The quick brown fox
First word: The
Last word: fox
```

> **Prompt for Claude.** "Write a Python program that takes a sentence as input, finds the first and last words, and prints them. Use the `find()` method to locate the first space, slice to extract characters before it for the first word. Use `rfind()` to locate the last space, slice to extract characters after it for the last word."

> **Tests to validate.**
> - Program reads a sentence from input
> - Program correctly identifies the first word
> - Program correctly identifies the last word
> - Test: "hello world" should give first word "hello", last word "world"
> - Test: "a b c d" should give first word "a", last word "d"

### Common misconceptions

**Slicing never raises an error.** Unlike indexing, asking for `message[0:100]` when the message is only 5 characters long returns the entire string, not an error. Python returns whatever exists in that range and stops.

**The stop index is exclusive, not inclusive.** This confuses many students. `message[0:3]` returns characters at indices 0, 1, and 2 — not including index 3. Once you accept this, the math becomes clean: a slice of length 3 starting at position 0 goes from 0 to 3.

**Slicing creates a new string.** When you write `new_message = message[0:5]`, Python builds a completely new string object. The original is unchanged. This is immutability in action — the slice is a new string, independent of the original.

**Negative indices are just shortcuts.** `message[-1]` is exactly equivalent to `message[len(message) - 1]`. The negative index is counted from the end, but under the hood, Python converts it.

---

## Concept 2 — Methods and searching: the toolkit for working with strings

Indexing and slicing get you characters and substrings. Methods transform strings and search within them. A **method** is a function that belongs to an object — in this case, a string. Strings have dozens of methods. Here are the ones you'll use most.

### Case conversion: `upper()` and `lower()`

These are the simplest string methods. They return a new string with all alphabetic characters converted to the opposite case. Non-alphabetic characters (numbers, punctuation, spaces) are unchanged.

```python
text = "Hello, World!"
print(text.upper()) # HELLO, WORLD!
print(text.lower()) # hello, world!
print(text) # Hello, World! (original unchanged)
```

Case conversion is useful when you want to compare strings ignoring case, or when you need to normalize input:

```python
user_input = input("Enter yes or no: ")
if user_input.lower() == "yes":
 print("You said yes.")
```

This code works whether the user types `"yes"`, `"YES"`, `"Yes"`, or `"yEs"`.

### Searching and testing: `in`, `find()`, and `count()`

The **`in` operator** checks if a substring exists. It returns `True` or `False`:

```python
text = "python programming"
print("prog" in text) # True
print("java" in text) # False
```

The **`find()` method** returns the index of the first occurrence, or -1 if not found:

```python
text = "banana"
print(text.find("an")) # 1 (first "an" starts at index 1)
print(text.find("na")) # 2
print(text.find("xyz")) # -1 (not found)
```

The **`count()` method** counts all occurrences:

```python
text = "banana"
print(text.count("a")) # 3
print(text.count("an")) # 2
print(text.count("xyz")) # 0
```

You can also iterate over characters using the `in` operator in a `for` loop:

```python
text = "abc"
for character in text:
 print(character)
```

produces:

```
a
b
c
```

Each iteration, the loop variable becomes the next character in the string.

### String modification: `replace()` and `strip()`

The **`replace()` method** builds a new string with substitutions made. The original is unchanged.

```python
text = "I like apples"
new_text = text.replace("apples", "oranges")
print(new_text) # I like oranges
print(text) # I like apples (original unchanged)
```

The **`strip()` method** removes leading and trailing whitespace (spaces, tabs, newlines):

```python
text = " hello \n"
print(text.strip()) # hello
print(text) # " hello \n" (original unchanged)
```

`strip()` is crucial for cleaning user input. Users often accidentally type spaces:

```python
name = input("Your name: ").strip()
print("Hello, " + name)
```

Related methods: `lstrip()` removes from the left only, `rstrip()` removes from the right only.

### Worked example: counting word frequencies

Write a program that reads a sentence, converts it to lowercase, splits it into words, and counts how many times each word appears. Print each unique word and its count.

```python
sentence = input("Enter a sentence: ").lower()
words = sentence.replace(".", "").replace(",", "").split()

unique_words = []
for word in words:
 if word not in unique_words:
 unique_words.append(word)

for word in unique_words:
 count = words.count(word)
 print(word + ": " + str(count))
```

Test by running with input `"the quick brown fox jumped over the lazy dog"`:

```
Enter a sentence: the quick brown fox jumped over the lazy dog
the: 1
quick: 1
brown: 1
fox: 1
jumped: 1
over: 1
lazy: 1
dog: 1
```

> **Prompt for Claude.** "Write a Python program that: takes a sentence as input, converts it to lowercase, removes punctuation (periods and commas) using `replace()`, splits it into words using `split()`, then prints each unique word and how many times it appears. Use a loop to check if a word is already in a list of unique words before adding it. Count occurrences using the `count()` method."

> **Tests to validate.**
> - Program reads and processes a sentence
> - Program converts to lowercase
> - Program removes punctuation before splitting
> - Program identifies each unique word exactly once
> - Word counts are correct
> - Test: "the dog and the cat" should give "the: 2", "dog: 1", "and: 1", "cat: 1"

### Common misconceptions

**Methods don't change the original string.** When you call `text.upper()`, it returns a new string. The variable `text` is unchanged unless you rebind it: `text = text.upper()`.

**The `in` operator checks for substrings, not just whole words.** `"cat" in "concatenate"` is `True`, even though "cat" is not a standalone word.

**`find()` returns an index, not a boolean.** It's tempting to use `if text.find("substring"):`, but this is wrong. If the substring is at index 0 (the first position), `find()` returns 0, which is falsy in Python. Use `if "substring" in text:` instead.

---

## Concept 3 — Formatting and building strings: templates and f-strings

Most programs need to build strings from pieces. A user enters their name, you want to greet them. Data comes back from a database, you want to format it nicely. Python provides two main tools: the `.format()` method and f-strings.

### String templates with `.format()`

The `.format()` method uses **replacement fields** — placeholders marked with `{}` — that get filled in with arguments:

```python
template = "Hello, {}! You are {} years old."
message = template.format("Alice", 25)
print(message) # Hello, Alice! You are 25 years old.
```

The placeholders are filled in order, left to right. You can also use named placeholders for clarity:

```python
template = "Hello, {name}! You are {age} years old."
message = template.format(name="Alice", age=25)
print(message) # Hello, Alice! You are 25 years old.
```

Named placeholders let you repeat values without duplicating arguments:

```python
template = "{name} is {age} years old. {name} lives in {city}."
message = template.format(name="Bob", age=30, city="Portland")
print(message) # Bob is 30 years old. Bob lives in Portland.
```

You can format numbers with a precision specifier. The syntax is `{:[width].[precision][type]}`:

```python
price = 19.99567
print("Price: ${:.2f}".format(price)) # Price: $19.99
print("{:05d}".format(42)) # 00042 (padded to 5 digits with zeros)
```

### F-strings: the modern approach

**F-strings** (formatted string literals), available since Python 3.6, are cleaner and faster. You prefix the string with `f` and put expressions directly inside `{}`:

```python
name = "Alice"
age = 25
message = f"Hello, {name}! You are {age} years old."
print(message) # Hello, Alice! You are 25 years old.
```

You can put any Python expression inside the `{}`:

```python
x = 10
y = 3
print(f"{x} + {y} = {x + y}") # 10 + 3 = 13
```

And you can use format specifiers the same way:

```python
price = 19.99567
print(f"Price: ${price:.2f}") # Price: $19.99
```

F-strings are faster and more readable. Use them when you can.

### Splitting and joining: `split()` and `join()`

The **`split()` method** breaks a string into a list of substrings:

```python
text = "apple,banana,orange"
fruits = text.split(",")
print(fruits) # ['apple', 'banana', 'orange']
```

If you don't give an argument, `split()` uses whitespace as the delimiter and automatically removes empty strings:

```python
text = "one two\tthree\nfour"
words = text.split()
print(words) # ['one', 'two', 'three', 'four']
```

The **`join()` method** does the opposite — it combines a list of strings into one, with a delimiter between them:

```python
fruits = ["apple", "banana", "orange"]
text = ", ".join(fruits)
print(text) # apple, banana, orange
```

The syntax is unusual at first: the delimiter comes before the method, and the list comes as an argument. Remember: "Take the list and join it with this delimiter." This design choice lets you reuse the same delimiter with different lists efficiently.

### Worked example: building a CSV line

Write a program that asks the user for first name, last name, and email, then outputs a line formatted for a CSV file (comma-separated values).

```python
first_name = input("First name: ").strip()
last_name = input("Last name: ").strip()
email = input("Email: ").strip()

csv_line = f"{first_name},{last_name},{email}"
print(csv_line)
```

Test by running with inputs `"Alice"`, `"Wonder"`, `"alice@example.com"`:

```
First name: Alice
Last name: Wonder
Email: alice@example.com
Alice,Wonder,alice@example.com
```

> **Prompt for Claude.** "Write a Python program that asks the user for a first name, last name, and email address. Use `.strip()` on each input to remove whitespace. Use an f-string to format these three values into a single CSV line separated by commas. Print the result."

> **Tests to validate.**
> - Program prompts for all three fields
> - Program strips whitespace from inputs
> - Program produces output in the format "first,last,email"
> - Test: "Alice", "Wonder", "alice@example.com" should produce "Alice,Wonder,alice@example.com"
> - Test: Extra spaces should be trimmed, not appear in output

### Common misconceptions

**F-strings evaluate expressions at definition time.** When you write `f"{x + 1}"`, the addition happens right then, and the result is embedded in the string. The result is a string literal.

**`join()` takes an iterable, not separate arguments.** `", ".join(["a", "b", "c"])` is correct. `", ".join("a", "b", "c")` is wrong and raises a `TypeError`.

**`split()` with no argument treats consecutive whitespace as one separator.** `"a b c".split()` returns `["a", "b", "c"]`, not `["a", "", "b", "", "c"]`. This is almost always what you want when parsing user input or file lines.

---

## Integration — from user to output

Let's build a small program that brings these pieces together: reading input, cleaning it, searching it, and formatting output.

A restaurant wants a program to parse a customer's order. The customer enters items separated by commas. The program should:
1. Read the order.
2. Clean up each item (remove leading/trailing spaces).
3. Count how many items.
4. Format a receipt showing each item and the total count.

```python
order_input = input("Enter items (comma-separated): ")
items = order_input.split(",")
clean_items = [item.strip() for item in items]

print("Receipt:")
for item in clean_items:
 print(f" - {item}")
print(f"Total items: {len(clean_items)}")
```

Test by running with input `"apple, banana , orange"`:

```
Enter items (comma-separated): apple, banana , orange
Receipt:
 - apple
 - banana
 - orange
Total items: 3
```

What's happening:
- `split(",")` breaks the input by commas without cleaning yet.
- The `for` loop with `.strip()` removes spaces from each item.
- F-strings format each line of output.
- `len()` counts the items.

Notice: `for item in items:` iterates over the list. Notice: `f"{item}"` embeds the value. Notice: `len(clean_items)` gets the count. All tools from this chapter, working together.

---

## Exercises

### Warm-up

**Exercise 8.1** *(LO: indexing and slicing).* Given the string `s = "python"`, what is the output of each?

(a) `print(s[0])`
(b) `print(s[-1])`
(c) `print(s[1:4])`
(d) `print(s[:3] + s[3:])`

**Exercise 8.2** *(LO: string comparison).* Evaluate each expression to `True` or `False`:

(a) `"apple" == "Apple"`
(b) `"cat" in "category"`
(c) `"dog" < "cat"`

**Exercise 8.3** *(LO: methods).* Given `s = "Hello, World!"`, what is the output of each?

(a) `print(s.lower())`
(b) `print(s.find("World"))`
(c) `print(s.count("l"))`

### Application

**Exercise 8.4** *(LO: slicing).* Write a program that asks the user for a time in the format `"hh:mm"` and prints just the minutes part. (Hint: use slicing, not `find()`.)

**Exercise 8.5** *(LO: split and join).* Write a program that asks the user for a sentence, splits it into words, reverses the order, and prints the words joined back with spaces. (Hint: lists have a `.reverse()` method, or you can use slicing with a step.)

**Exercise 8.6** *(LO: formatting).* Write a program that asks the user for a product name and price, then prints a formatted price tag with the name padded to 20 characters and the price formatted to two decimal places.

### Synthesis

**Exercise 8.7** *(LO: strings as sequences).* Write a program that reads a word from the user and checks if it's a palindrome (reads the same forwards and backwards). Hint: use slicing to reverse the string.

**Exercise 8.8** *(LO: counting and formatting).* Write a program that asks the user for a paragraph of text. Count the number of sentences (ending with `.`, `?`, or `!`). Count the number of words. Count the number of vowels. Print all three counts in a formatted table.

**Exercise 8.9** *(LO: string building).* Write a program that asks the user for a list of names (one per line). When the user enters a blank line, stop. Then print a greeting: "Hello, [name1], [name2], and [name3]!" — using proper commas and "and" before the last name.

### Challenge

**Exercise 8.10** *(LO: integration).* Write a program that asks the user to enter a sentence. Remove all vowels, count the remaining characters, and print the result. Also print what percentage of the original sentence was vowels.

**Exercise 8.11** *(LO: open-ended).* Write a program that asks the user for a password. Check that it has at least 8 characters, contains at least one digit, contains at least one uppercase letter, and contains at least one lowercase letter. Print "Valid" or "Invalid" with a message explaining what's missing.

---

## Claude Code — URL slug generator

### Orientation

This section brings together indexing, slicing, methods, and f-strings to solve a real problem: converting human-readable text into a URL-safe slug. Blog platforms do this constantly. You type "Hello, World! 2024" as a post title; the system converts it to "hello-world-2024" for the URL. The machinery is pure strings: case conversion, punctuation removal, whitespace collapsing, edge-case handling.

### The prompt

Open Claude Code and run this prompt to generate a working solution:

> I need a function `slugify(title)` that converts a blog post title into a URL slug.
> 
> Rules:
> - Convert to lowercase
> - Remove all non-alphanumeric characters except hyphens and spaces
> - Replace spaces with hyphens
> - Collapse consecutive hyphens into a single hyphen
> - Strip leading and trailing hyphens
> 
> Examples:
> - "Hello, World!" → "hello-world"
> - "Python 3.12 Release! " → "python-312-release"
> - " Multiple Spaces " → "multiple-spaces"
> - "---already-slugged---" → "already-slugged"
> - "" (empty string) → ""
> 
> Write the function, then write `test_slugify.py` with at least 5 test cases. Run pytest to validate.

### What to expect

Claude Code will produce two files: `slugify.py` containing the function, and `test_slugify.py` with test cases. The function uses string methods you've learned:

- `.lower()` for case conversion
- `.replace()` to substitute characters
- String slicing and conditionals to handle leading/trailing hyphens
- A loop (or list comprehension) to filter characters

The tests cover:
- Punctuation removal
- Multiple consecutive spaces becoming single hyphens
- Leading and trailing whitespace stripping before slugification
- Empty string edge case
- Unicode characters (emoji, accented letters)

Watch for the trade-off: how many passes through the string does the function make? Each `.replace()` call builds a new string. A more efficient approach would iterate once, character by character, building the result. This version prioritizes readability over performance — a common choice when data size is small.

### Stretch

Once the basic version works:

1. **Modify the function to preserve existing hyphens** — `"pre-made-slug"` should stay `"pre-made-slug"`, not become something else.

2. **Add a parameter for maximum slug length** — `slugify("Very Long Title", max_length=20)` should truncate intelligently, never breaking a word mid-hyphen.

3. **Handle Unicode properly** — use the `unicodedata` module to convert "Café" to "cafe" (stripping accents) instead of dropping the accented character. Compare the result to your emoji test case.

---

## Chapter summary

You now know how to work with text in Python. You can index and slice to get characters and substrings. You understand that strings are immutable — when you want to change a string, you build a new one. You know the most common string methods: `upper()`, `lower()`, `find()`, `count()`, `split()`, `join()`, `replace()`, `strip()`. You can format strings using `.format()` and f-strings. You can compare strings and search within them. You can iterate over characters and build output.

Strings are how your program communicates with the outside world. Every time your code reads user input, parses data from a file, or prints output, you're manipulating strings. The operations in this chapter are the foundation of almost everything you'll write that touches text.

The thing to remember: strings are sequences. Sequences have a first element, a last element, a length, and you can ask for any piece of them. That intuition — *sequence* — will reappear when we look at lists in Chapter 9 and dictionaries in Chapter 10. The tools change slightly, but the mental model stays the same.

What you should now be able to teach a friend who asks: how to get a character at a specific position, how to get a substring using start and stop, why you can't change a string in place and what you do instead, what the `in` operator does, how `split()` and `join()` work as inverses of each other, how to format a string with variable data.

---

## Connections forward

In Chapter 9, you'll meet **lists** — sequences like strings, but mutable. You can change a list after you create it. The methods will look similar (`count()`, `find()`), but lists can hold any type of value, not just characters.

In Chapter 10, you'll meet **dictionaries** — sequences of key-value pairs. You'll use string keys to look up values.

In Chapter 14, you'll read strings from files and write strings to files. Everything in this chapter about searching and parsing will become practical when you're processing real data.

And later, when you work with data (Chapter 15), you'll format and clean strings constantly. The discipline of using `.strip()`, checking with `in`, and building with `.format()` will be the foundation of data cleaning.

---

## What would change my mind

If a major Python version changed the semantics of string immutability, or if a practical use case emerged where mutability became necessary without building new objects, this chapter's framing would need revision. Currently, immutability is core to Python's design and has held for decades.

## Still puzzling

The reason `.join()` has its delimiter before the method call (instead of as the first argument inside the parentheses) is a quirk of Python's design. It's occasionally confusing, but it's the standard, and muscle memory develops quickly.

---

## Tags

strings, text, indexing, slicing, methods, formatting, immutability, search, python-fundamentals
---

## LLM Exercise — Chapter 08: Strings (Text Adventure Project)

**Project:** Text Adventure.
**What you're building this chapter:** a real command parser — handle multi-word commands, aliases, and gentle error messages.
**Tool:** **Claude Code**.

---

**The Prompt:**

```
Chapter 8 of my Text Adventure project. Chapter 8 covered:
indexing and slicing; common methods (split, join, strip,
replace, lower, upper); f-strings; encoding.

Build the command parser. The game now accepts commands like:
- "go north" or "n" or "north"
- "take rusty sword" or "get rusty sword" or "pickup sword"
- "look" or "examine room"
- "inventory" or "i" or "items"
- "fight goblin" or "attack goblin"
- "help"
- "quit" or "exit"

Build `parse_command(raw_input)` in `util.py`. It should:

1. **Normalize.** Lowercase, strip whitespace.

2. **Tokenize.** Split into words.

3. **Identify the verb.** Map aliases:
 - "go", "move", "walk", "head" → "go"
 - "take", "get", "pickup", "grab" → "take"
 - "look", "examine", "inspect" → "look"
 - "inventory", "i", "items", "bag" → "inventory"
 - "fight", "attack", "battle" → "fight"
 - "quit", "exit", "q", "bye" → "quit"
 - Single-letter shortcuts for directions: "n" → ("go", "north"),
 "s" → ("go", "south"), etc.

4. **Extract the object.** The rest of the words (joined with
 space). E.g., "take rusty sword" → verb="take",
 object="rusty sword".

5. **Return a tuple** (verb, object) where object may be an empty
 string for verb-only commands like "look" or "inventory".

6. **Handle typos gently.** If the verb isn't recognized, suggest
 the closest match (compare the first 3 letters). E.g., "tek"
 → "Did you mean 'take'?"

Add `format_room_description(room_name, items_in_room, exits)` —
a function that uses f-strings to build a rich multi-line
description with the room name, item list, and exit list. Use
`.title()` for room names; `.join(", ")` for item and exit lists.

In addition to the code, produce the prompt + tests. Tests:
- `parse_command("go north")` returns `("go", "north")`.
- `parse_command("n")` returns `("go", "north")`.
- `parse_command("take rusty sword")` returns `("take", "rusty
 sword")`.
- `parse_command("tek sword")` returns the typo suggestion.

End with: a real player will type "tale" instead of "take" once
in a while. What's the most graceful failure mode for your
parser? (Hint: never crash; always say something helpful.)
```

---

**What this produces:** A working command parser. The "graceful failure" closer is the UX discipline.

**Connection to previous chapters:** Ch 7's modules organized code; Ch 8 makes the input layer rich.

**Preview of next chapter:** Chapter 9 covers lists. You'll build the player's inventory and the per-room item lists as Python lists, with all the list operations (append, remove, "in" check).


---

##  AI Wayback Machine
**Joseph Marie Jacquard** was invented the punched-card programmable loom in 1804 — the conceptual ancestor of string-and-instruction encoded computation.

**Run this:**

```
Who is Joseph Marie Jacquard, and how does their work connect to strings we covered in this chapter? Keep it to three paragraphs. End with the single most surprising thing about their career or ideas.
```

→ Search **"Joseph Marie Jacquard"** on Wikipedia.

**Now make the prompt better.** Try one of these:

- Ask it to apply Joseph Marie Jacquard's ideas to a small Python program you'd write.
- Add a constraint: "Answer including criticisms or limits of Joseph Marie Jacquard's framework."

What changes? What gets better? What gets worse?
