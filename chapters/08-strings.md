# Chapter 8 — Strings
*A sequence of characters, an immutable fact, and the three operations that make text programmable.*

Your phone screen just lit up because someone typed your name. What happened between their keypress and your notification is a long chain of machinery, but near the beginning of it is something simple: the string `"Alex"` — or whatever your name is — was compared, character by character, against a list of names. Four comparisons. Four matches. Notification sent.

A string is a sequence of characters. That's the whole definition. But the word "sequence" is doing a lot of work there, because it means every character has a position, and positions can be used to extract pieces, to search, to test membership, to build new strings out of old ones. The machinery this chapter teaches is the foundation of almost everything a program does when it talks to the world: reading input, cleaning it, parsing it, formatting output.

---

## What a String Is, and Why It Can't Be Changed

A string is created by enclosing text in quotes:

```python
greeting = "hello"
name = 'Alice'
empty = ""
```

Single or double quotes — Python accepts both, and treats them identically. The empty string `""` is a valid string with length zero.

The first thing to know about strings is that they are **immutable**: once created, the characters inside cannot be changed. This is not a limitation you can work around — it's a design decision baked into Python. If you try to change a character:

```python
word = "hello"
word[0] = "H"   # TypeError: 'str' object does not support item assignment
```

Python stops immediately. The string is fixed.

Why? Because strings are often shared across a program. If one function could secretly modify a string that another function is using, the behavior of the second function would become unpredictable. Immutability is a guarantee: the string you received is the string you'll keep.

The consequence: every string operation that "changes" a string actually *builds a new string* and returns it. The original is untouched.

```python
word = "hello"
upper_word = word.upper()
print(upper_word)   # HELLO
print(word)         # hello — original unchanged
```

Assign the result back to the same variable if you want the variable to hold the new value:

```python
word = word.upper()   # word now holds "HELLO"
```

Keep this rule in mind throughout the chapter. It explains why every method call and every operation produces something new.

---

## Indexing: Getting One Character at a Time

A string is a sequence, so each character has a **position** — a number that identifies where it sits. Python counts from zero.

```python
s = "python"
#    012345
```

`s[0]` is `"p"`. `s[1]` is `"y"`. `s[5]` is `"n"`. For a string of length `n`, valid indices run from `0` to `n - 1`. Asking for `s[6]` on a six-character string raises an `IndexError` — there is no character at that position.

Python also counts backward from the end, using negative indices:

```python
s = "python"
print(s[-1])   # n — last character
print(s[-2])   # o
print(s[-6])   # p — same as s[0]
```

Index `-1` is always the last character. Index `-n` is always the first. Negative indices are shortcuts — `s[-1]` is exactly equivalent to `s[len(s) - 1]`, and Python converts them under the hood.

<!-- → [FIGURE: string "python" with both positive (0–5) and negative (−6 to −1) indices labeled below each character — student should see the two indexing systems as two views of the same positions, with first and last characters marked by both their indices] -->

The distinction between indexing and slicing matters here: **indexing a single out-of-range position raises an error; slicing a range that goes beyond the bounds does not.** Keep that asymmetry in mind.

---

## Slicing: Getting a Substring

Indexing gives you one character. Slicing gives you a contiguous piece — a substring — by specifying where to start and where to stop.

```python
s = "python"
print(s[0:2])   # py   — positions 0 and 1
print(s[2:5])   # tho  — positions 2, 3, 4
print(s[1:4])   # yth  — positions 1, 2, 3
```

The syntax is `s[start:stop]`. Characters from `start` up to — but *not including* — `stop`. The stop index is exclusive. This is the single most common source of off-by-one confusion in string work. `s[0:2]` gives two characters, not three. `s[2:5]` gives three characters, not four. The count is always `stop - start`.

Omit the start to begin from position 0. Omit the stop to run to the end:

```python
s = "python"
print(s[:3])    # pyt  — first three characters
print(s[3:])    # hon  — everything from position 3 onward
print(s[:])     # python — the whole string
```

Negative indices work in slices:

```python
s = "python"
print(s[-3:])   # hon  — last three characters
print(s[:-2])   # pyth — all but the last two
```

Slicing always produces a new string. `s[:]` — the full-string slice — creates a new string object with the same characters. The original is not modified.

<!-- → [FIGURE: string "python" with a highlighted region showing s[1:4] = "yth" — bracket notation above the string marking start=1 (inclusive) and stop=4 (exclusive), with an arrow to the result "yth" — student should see the stop-exclusive rule visually] -->

When a slice range goes beyond the string, Python returns whatever exists — no error:

```python
s = "hi"
print(s[0:100])   # hi — Python stops at the end, no complaint
```

This is different from indexing. `s[100]` raises an `IndexError`. `s[0:100]` returns the full string.

### Why the Stop Is Exclusive

The exclusive-stop convention looks arbitrary at first. But it has a clean consequence: `s[:n]` gives the first `n` characters, and `s[n:]` gives everything after them, and together they reconstruct the original string with no overlap and no gap:

```python
s = "python"
n = 3
print(s[:n] + s[n:])   # python — always equal to s, for any n
```

This makes slicing useful for splitting a string into a prefix and a suffix at any position.

---

## Methods: The Toolkit

Strings come with built-in methods — functions that belong to the string and operate on it. Because strings are immutable, every method returns a new string; the original is unchanged.

### Case Conversion

```python
text = "Hello, World!"
print(text.upper())   # HELLO, WORLD!
print(text.lower())   # hello, world!
print(text.title())   # Hello, World! (capitalize each word)
```

Case conversion matters when you want to compare strings without caring about capitalization:

```python
response = input("Continue? (yes/no): ")
if response.lower() == "yes":
    print("Continuing...")
```

This works whether the user types `"yes"`, `"YES"`, `"Yes"`, or `"yEs"`.

### Searching

`in` checks whether a substring exists anywhere in the string:

```python
text = "python programming"
print("prog" in text)    # True
print("java" in text)    # False
```

`find()` returns the index of the first occurrence, or `-1` if not found:

```python
text = "banana"
print(text.find("an"))    # 1  — first "an" starts at position 1
print(text.find("xyz"))   # -1 — not found
```

A warning: don't write `if text.find("x"):`. If `"x"` is at position 0, `find()` returns `0`, which is falsy — and your condition will incorrectly evaluate to `False`. Use `if "x" in text:` when you want a yes/no answer.

`count()` counts all non-overlapping occurrences:

```python
text = "banana"
print(text.count("a"))    # 3
print(text.count("an"))   # 2
```

### Cleaning and Transforming

`strip()` removes leading and trailing whitespace — spaces, tabs, newlines:

```python
raw = "  hello \n"
print(raw.strip())    # hello
```

Whitespace stripping is the first thing to do with any user input. Users accidentally add spaces. Files have trailing newlines. `strip()` handles both.

`replace()` builds a new string with substitutions:

```python
text = "I like apples"
print(text.replace("apples", "oranges"))   # I like oranges
print(text)                                # I like apples — unchanged
```

<!-- → [TABLE: string methods quick reference — rows: upper(), lower(), strip(), find(), count(), replace(), split(), join() — columns: method, what it does, example, return type — student should have a single-page reference for the full toolkit] -->

### Splitting and Joining

`split()` breaks a string into a list of substrings:

```python
text = "apple,banana,orange"
parts = text.split(",")
print(parts)    # ['apple', 'banana', 'orange']
```

Without an argument, `split()` splits on whitespace and collapses consecutive spaces:

```python
sentence = "one   two\tthree\nfour"
words = sentence.split()
print(words)    # ['one', 'two', 'three', 'four']
```

`join()` is the inverse — it combines a list of strings with a delimiter:

```python
fruits = ["apple", "banana", "orange"]
print(", ".join(fruits))    # apple, banana, orange
print(" — ".join(fruits))   # apple — banana — orange
```

The syntax is unusual: the delimiter goes *before* the method call, and the list goes inside the parentheses. Read it as "join this list using this delimiter." The design lets you reuse the same delimiter with any list.

`split()` and `join()` are inverses of each other. If you split a string and then join with the same delimiter, you recover the original:

```python
text = "a,b,c"
parts = text.split(",")
reconstructed = ",".join(parts)
print(reconstructed == text)    # True
```

---

## Formatting: Building Strings from Pieces

The most common string task is assembling output from variable data. Python provides two tools.

**`.format()`** uses `{}` as placeholders:

```python
name = "Alice"
age = 25
print("Hello, {}! You are {} years old.".format(name, age))
# Hello, Alice! You are 25 years old.
```

**F-strings** (Python 3.6+) put expressions directly inside the string, marked with a leading `f`:

```python
name = "Alice"
age = 25
print(f"Hello, {name}! You are {age} years old.")
# Hello, Alice! You are 25 years old.
```

Any Python expression works inside the braces:

```python
x, y = 10, 3
print(f"{x} divided by {y} is {x / y:.2f}")
# 10 divided by 3 is 3.33
```

The `:2f` after the expression is a **format specifier**: `.2f` means "float, two decimal places." Other common specifiers:

```python
price = 19.99567
print(f"${price:.2f}")      # $19.99  — two decimal places
print(f"{42:05d}")          # 00042   — integer, padded to 5 digits with zeros
print(f"{'hello':>10}")     #      hello — right-aligned in 10 characters
```

F-strings are cleaner and faster than `.format()`. Use them. The one situation where `.format()` is still useful: when the template string is stored separately from the data, such as in a configuration file or a translation system.

### A Worked Example: A Receipt

```python
def print_receipt(items, prices):
    """Print a formatted receipt with aligned columns."""
    print(f"{'Item':<20} {'Price':>8}")
    print("-" * 30)
    total = 0
    for item, price in zip(items, prices):
        print(f"{item:<20} ${price:>7.2f}")
        total += price
    print("-" * 30)
    print(f"{'Total':<20} ${total:>7.2f}")

print_receipt(
    ["Coffee", "Sandwich", "Cookie"],
    [3.50, 8.95, 1.75]
)
```

Output:

```
Item                    Price
------------------------------
Coffee              $   3.50
Sandwich            $   8.95
Cookie              $   1.75
------------------------------
Total               $  14.20
```

The `:<20` specifier left-aligns in a 20-character field. The `:>7.2f` specifier right-aligns a float in 7 characters with 2 decimal places. Column alignment is just arithmetic on string widths.

<!-- → [FIGURE: annotated f-string showing the format specifier breakdown — f"{price:>7.2f}" decomposed into: the expression (price), colon (start of specifier), > (right-align), 7 (field width), .2 (two decimal places), f (float type) — student should see each part of the specifier syntax labeled] -->

---

## What the Chapter Adds Up To

Strings are sequences. That single fact generates almost everything in this chapter.

Because they're sequences, characters have positions — which gives you indexing and slicing. Because they're sequences, you can iterate over them character by character, check membership with `in`, count occurrences with `count()`. Because they're sequences, `len()` gives their length.

Because they're immutable, every operation that "changes" a string returns a new one. This means you must always capture the return value if you want the result. `text.strip()` strips whitespace and returns the stripped string — but if you don't assign it, the original `text` is unchanged and you've done nothing.

The practical chain for almost any text-processing task:

1. Get input: `input()` or read from a file.
2. Clean it: `.strip()` to remove whitespace, `.lower()` to normalize case.
3. Parse it: `.split()` to break into pieces, indexing or slicing to extract fields.
4. Process it: search with `in` or `find()`, count with `count()`, transform with `replace()`.
5. Format output: f-strings with specifiers for alignment and precision.

Every program that talks to the outside world runs through some version of this chain.

*What would change my mind:* The exclusive-stop convention for slicing is the one place where the design of Python's strings creates consistent confusion. If evidence accumulated that teaching inclusive-stop first (and then correcting to exclusive) reduces errors, that would be worth trying. The current approach — state the rule, demonstrate the clean property it enables — is my best guess, not a certainty.

*Still puzzling:* Why `join()` lives on the delimiter rather than the list (`",".join(words)` instead of `words.join(",")`) is a consequence of the fact that `join()` works on any iterable, not just lists. The delimiter is always a string, so the method lives on the string type. The logic is sound but the syntax surprises beginners every time.

---

## Exercises

### Warm-up

**Exercise 8.1** *(Indexing and slicing.)*
Given `s = "python"`, predict the output of each expression before running it.

(a) `s[0]`
(b) `s[-1]`
(c) `s[1:4]`
(d) `s[:3] + s[3:]`
(e) `s[-3:]`

**Exercise 8.2** *(Immutability.)*
What does this code print? What error, if any, does it raise, and why?

```python
word = "hello"
word.upper()
print(word)
```

**Exercise 8.3** *(Method return values.)*
Fix the code in Exercise 8.2 so that `word` holds the uppercase version after the call.

### Application

**Exercise 8.4** *(Time parsing.)*
Write a program that asks the user for a time in the format `"hh:mm"` and prints just the minutes. Use slicing, not `find()`.

**Exercise 8.5** *(Word reversal.)*
Write a program that reads a sentence, splits it into words, reverses the order, and prints the words joined back with spaces.

**Exercise 8.6** *(Formatted price tag.)*
Write a program that asks for a product name and price, then prints a price tag with the name left-aligned in 20 characters and the price right-aligned to two decimal places.

### Synthesis

**Exercise 8.7** *(Palindrome check.)*
Write a program that reads a word and checks whether it's a palindrome (reads the same forwards and backwards). Use slicing to reverse the string. Handle case: `"Racecar"` should be recognized as a palindrome.

**Exercise 8.8** *(Text statistics.)*
Write a program that reads a paragraph. Count the number of sentences (endings: `.`, `?`, `!`), words, and vowels. Print the three counts in a formatted table.

**Exercise 8.9** *(Graceful list formatting.)*
Ask the user for names one at a time, stopping on a blank line. Then print `"Hello, Alice, Bob, and Carol!"` — with proper commas and `"and"` before the last name, regardless of how many names were entered.

### Challenge

**Exercise 8.10** *(Vowel removal.)*
Write a program that reads a sentence, removes all vowels, counts the remaining characters, and prints both the stripped text and what percentage of the original was vowels. Handle both upper and lowercase vowels.

**Exercise 8.11** *(Password validator.)*
Write a program that reads a password and checks: at least 8 characters, at least one digit, at least one uppercase letter, at least one lowercase letter. Print `"Valid"` or list each failing condition specifically.

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
- `parse_command("take rusty sword")` returns `("take", "rusty sword")`.
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

## AI Wayback Machine

**Joseph Marie Jacquard** invented the punched-card programmable loom in 1804 — the conceptual ancestor of string-and-instruction encoded computation.

**Run this:**

```
Who is Joseph Marie Jacquard, and how does their work connect to strings we covered in this chapter? Keep it to three paragraphs. End with the single most surprising thing about their career or ideas.
```

→ Search **"Joseph Marie Jacquard"** on Wikipedia.

**Now make the prompt better.** Try one of these:

- Ask it to apply Joseph Marie Jacquard's ideas to a small Python program you'd write.
- Add a constraint: "Answer including criticisms or limits of Joseph Marie Jacquard's framework."

What changes? What gets better? What gets worse?
