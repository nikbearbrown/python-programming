# Chapter 1 — Statements
*The moment the computer does what you told it to.*

There is a particular thing that happens the first time a program runs correctly. You wrote something, you pressed a key, and the machine responded. Not with an error. With the answer you expected. The feeling is strange and specific — something between surprise and satisfaction, closer to surprise — because you realize the computer wasn't doing anything mysterious. It was doing exactly what you told it. The mystery was never the machine. The mystery was learning how to tell it.

That's what this chapter is about. Not Python as a language, not programming as a career, not the history of computing — just the basic act of giving a computer a clear instruction and watching it follow through. If you understand that act completely, everything else in this book is a variation on it.

---

## The simplest possible conversation

Type this:

```python
print("Hello, world!")
```

Press Enter.

The screen shows:

```
Hello, world!
```

That's a **statement** — a single instruction to the Python interpreter. `print()` is a *function*, a named chunk of code that does a specific job. The job here is: display the argument on the screen. The argument is `"Hello, world!"` — the text in the parentheses. Python runs the instruction, displays the text, and waits for the next instruction.

Most programs are a list of statements, one after another. Python runs them in order, top to bottom.

```python
print("Today is Monday.")
print("I like string beans.")
```

```
Today is Monday.
I like string beans.
```

Simple. But already there's something worth noticing. Each `print()` automatically adds a newline — the move to the next line — after its output. Python adds this for you because most of the time you want each piece of output on its own line. When you don't want a newline, you can suppress it:

```python
print("First part: ", end="")
print("Second part.")
```

```
First part: Second part.
```

The `end=""` says: instead of adding a newline at the end, add nothing. The second `print()` then continues on the same line. The named parameter `end` is called a *keyword argument* — it lets you change the default behavior of the function without rewriting the function itself.

There's another named parameter worth knowing: `sep`, short for separator. When you pass multiple values to `print()`, Python puts a space between them by default. `sep` lets you change that:

```python
print("Liverpool", "versus", "Chelsea", sep=" ... ")
```

```
Liverpool ... Chelsea
```

<!-- → [TABLE: Two-column table showing print() keyword arguments — `sep` (default: single space, purpose: separates multiple values) and `end` (default: newline character `\n`, purpose: controls what follows the output). Helps students see the two customization knobs without wading through function syntax.] -->

---

## Listening

`print()` speaks. `input()` listens.

```python
name = input("What is your name? ")
```

This does three things in sequence. First, it displays `"What is your name? "` on the screen. Second, it pauses — the program stops and waits while the user types. Third, when the user presses Enter, whatever they typed is captured and stored somewhere the program can find it. That somewhere is `name`.

`name` is a **variable** — a label attached to a value in the computer's memory. The label stays the same; the value it points to can change. This is where the word *variable* comes from: it varies.

Once the value is stored, you can use it:

```python
name = input("What is your name? ")
print("Hello, " + name)
```

If the user types `Alice`, the program prints `Hello, Alice`. The `+` between two strings joins them — this is *string concatenation*. It doesn't add in the arithmetic sense; it connects. `"Hello, " + "Alice"` produces `"Hello, Alice"`.

Notice the space after the comma inside the first string. Without it, `"Hello," + "Alice"` produces `"Hello,Alice"`. The concatenation operator joins exactly what you give it — it doesn't guess where spaces should go.

---

## What a variable actually is

The single most important thing to understand about variables is that assignment (`=`) is not equality. It is *binding*. The `=` sign in Python means: evaluate the right side, then attach that value to the name on the left.

```python
count = 0
count = 5
count = 10
```

After the first line, `count` holds `0`. After the second, it holds `5` — the `0` is gone. After the third, it holds `10`. The name `count` hasn't changed; what it points to has.

This becomes important when you write something like:

```python
x = 5
x = x + 1
```

This is not a mathematical statement. It is not claiming that 5 equals 6. It is an instruction: look at the current value of `x` (which is `5`), add `1` to it (getting `6`), then store that result back in `x`. Now `x` is `6`. The right side is evaluated first, completely, and only then assigned to the left side.

Variable names follow rules. They can contain letters, digits, and underscores, but they cannot start with a digit. They are case-sensitive — `Total` and `total` are different names. Python has reserved words (`if`, `for`, `while`, `return`, etc.) that cannot be used as variable names. Beyond the rules, there is style: Python programmers use *snake_case* — all lowercase, underscores between words. `first_name` rather than `firstname`, `total_price` rather than `totalPrice`. The style exists because code is read far more than it is written. A name that says what it holds is a favor to everyone who reads the code later, including yourself.

<!-- → [INFOGRAPHIC: Visual showing a variable as a labeled box in memory — the label (`name`) stays fixed on the outside, the value (`"Alice"` → `"Bob"` → `"Carol"`) changes inside. Arrows showing reassignment reinforce that `=` is binding, not equality.] -->

---

## Numbers and what you can do with them

Python works with two kinds of numbers. An **integer** is a whole number: `5`, `-17`, `0`, `1000`. A **float** (floating-point) is a decimal number: `3.14`, `-2.5`, `0.0`. The distinction matters because they behave differently in certain situations.

The arithmetic operators:

- `+` addition
- `-` subtraction
- `*` multiplication
- `/` division
- `**` exponentiation (power)

```python
print(10 + 3)    # 13
print(10 - 3)    # 7
print(10 * 3)    # 30
print(10 / 3)    # 3.3333...
print(10 ** 3)   # 1000
```

Division (`/`) always returns a float, even when the result is a whole number. `10 / 2` gives `5.0`, not `5`. This is a design choice Python made deliberately — it avoids the silent truncation that has caused real bugs in other languages.

When multiple operators appear in one expression, **operator precedence** determines the order they execute. The rules match algebra: exponentiation first, then multiplication and division, then addition and subtraction. Parentheses override everything.

```python
print(2 + 3 * 4)     # 14 — multiplication happens first
print((2 + 3) * 4)   # 20 — parentheses force addition first
```

When in doubt, use parentheses. They make your intent explicit and protect you from subtle precedence surprises.

<!-- → [TABLE: Operator precedence table — five rows (Parentheses, Exponentiation, Multiplication/Division, Addition/Subtraction) with example expression, result, and note on direction of evaluation. Students need this as a reference card before exercises.] -->

---

## The type problem

Here is something that trips people up. `input()` always returns a string — text — even when the user types a number.

```python
age = input("How old are you? ")
```

If the user types `25`, `age` holds the string `"25"`, not the integer `25`. They look the same when printed, but they behave differently when used in arithmetic:

```python
print(age + 1)    # TypeError: can only concatenate str (not "int") to str
```

Python won't add a string and an integer. It doesn't guess what you meant. To use the input as a number, you have to *convert* it:

```python
age = int(input("How old are you? "))
```

`int()` converts a string to an integer. `float()` converts to a floating-point number. These are called *type conversion functions*, and they're the bridge between the text that `input()` always gives you and the numbers that arithmetic requires.

The full pattern for a numeric input looks like this:

```python
temperature = float(input("Temperature in Fahrenheit: "))
celsius = (temperature - 32) * 5 / 9
print(temperature, "F =", celsius, "C")
```

Test with `98.6`:

```
Temperature in Fahrenheit: 98.6
98.6 F = 37.0 C
```

The parentheses around `(temperature - 32)` are essential. Without them, precedence would evaluate `32 * 5` first, giving the wrong answer. The formula `(F - 32) × 5/9` translates directly to code, parentheses and all.

---

## Strings beyond concatenation

Strings support more than concatenation. Two operations appear constantly.

`len()` gives you the number of characters in a string:

```python
print(len("hello"))          # 5
print(len("Hello, World!"))  # 13
print(len(""))               # 0
```

Spaces and punctuation count as characters. The empty string `""` has length zero.

Concatenation with `+` joins strings end to end:

```python
first = "Grace"
last = "Hopper"
full = first + " " + last
print(full)    # Grace Hopper
```

The `+` operator is said to be *overloaded* — it means different things depending on the types it operates on. For numbers, it adds. For strings, it concatenates. This is convenient but requires care: `"5" + "3"` gives `"53"`, not `8`. And `"5" + 3` gives a `TypeError` — Python won't add a string and a number even when the string contains digits.

<!-- → [TABLE: Operator behavior by type — rows showing `+` with int+int, float+float, str+str, str+int. Shows result for first three, TypeError for last. Makes the type enforcement concrete before students hit it on their own.] -->

---

## When things go wrong

Making mistakes is the normal part of programming. Python tries to help. When it encounters a problem, it prints a *traceback* — a report naming what went wrong and where.

Three errors show up constantly at this stage.

**SyntaxError** means Python can't parse what you wrote. The grammar is broken. Often a missing quote or parenthesis:

```python
print("Hello"
```

```
SyntaxError: '(' was never closed
```

The caret in the error message points to where Python got confused. Fix it by adding the closing parenthesis.

**NameError** means you used a name that doesn't exist:

```python
print(age)
```

```
NameError: name 'age' is not defined
```

You never assigned anything to `age`. Either you forgot the assignment, or you misspelled the variable name somewhere. These two causes account for almost every NameError.

**IndentationError** means you have unexpected whitespace at the start of a line. Python uses indentation to define structure, so stray spaces matter:

```python
    print("Hello")
```

```
IndentationError: unexpected indent
```

Remove the leading spaces. Indentation will matter when we get to `if` statements and loops — it will be *required* there, in specific amounts. But outside those structures, it should not exist.

Reading a traceback is a skill like any other. The line number tells you where. The error type tells you what category of problem to look for. The explanation tells you what Python found or expected. Most of the time, looking at that line of code after reading the error is enough to spot the fix.

---

## A brief history: why Python exists

In 1989, Guido van Rossum began writing Python over a Christmas holiday. He had spent years working on ABC, a language designed to be simple and teachable, but ABC lacked the extensibility professionals needed for real work. Van Rossum wanted both: a language you could teach a beginner in an afternoon and use to build serious software.

He named it Python after Monty Python's Flying Circus. He released the source code freely in 1991.

That combination — simplicity, power, open access — shaped everything that followed. Python is now one of the most widely used programming languages in the world, present in web services, scientific research, machine learning, and financial systems. The TIOBE index, which tracks programming language popularity by search volume, placed Python at #1 in October 2021 — the first time in twenty years that anything other than C or Java held the top position.

You are entering that conversation at the beginning. Everything you write for the rest of this book builds on the handful of ideas in this chapter.

---

## LLM Exercise — Chapter 01: Statements (Text Adventure Project)

**Project:** Text Adventure.
**What you're building this chapter:** the game's opening sequence — title banner, name prompt, personalized welcome. Your first runnable game code.
**Tool:** **Claude Code**.

---

**The Prompt:**

```
Chapter 1 of my Text Adventure project. The Vision Document is in
the project folder. Chapter 1 covered: variables and assignment;
basic I/O (print, input); arithmetic; error messages
(SyntaxError, NameError, IndentationError).

Build the game's opening. Three pieces.

1. **The title banner.** Print the game title with some ASCII
   flourish — at minimum, the title on its own line, perhaps
   with separator lines made of `=` or `*` characters. Print a
   one-line tagline. Then a blank line.

2. **The name prompt.** Use `input()` to ask the player's name.
   Store it in a variable called `player_name`. Handle the case
   where the player just hits Enter (an empty string) — assign
   a default name like "Adventurer."

3. **The personalized welcome.** Print 2-3 lines welcoming the
   player by name, hinting at the central conflict from the
   Vision Document, and prompting them to press Enter to begin.

Use `print()` and `input()` only. No functions yet (that's Ch 6).
No loops (Ch 5). No conditionals (Ch 4). Just statements.

In addition to the code, produce:
- **Prompt for Claude:** the prompt you'd send to Claude to elicit
  this code. Specific enough that the LLM has a chance; minimal
  enough that you're still doing the thinking.
- **Tests to validate:** 2-3 concrete tests:
  1. Run `python game.py` — does it print the banner without
     errors?
  2. Type a name when prompted — does the welcome message
     include the name?
  3. Just hit Enter when prompted — does the welcome message
     use the default name?

Save the code to `game.py`. Commit (mentally or with git) the
three artifacts together. This is the chapter's "three artifact"
pattern in action.

End with: name one error message you encountered while building
this. What did it mean? How did you fix it? This is the chapter's
practice in the daily reality of programming.
```

---

**What this produces:** A working opening sequence + the prompt + tests. The "what error did you hit" closer is the discipline this book trains.

**How to adapt this prompt:**

- *For your own project:* Adding decorative ASCII art is optional but adds character.
- *For ChatGPT / Gemini:* Works as written.
- *For Claude Code:* Native fit. Claude Code writes the file, runs it, sees the output.
- *For a Claude Project:* The Vision Document context informs what the tagline says.

**Connection to previous chapters:** Ch 00's Vision Document sets the game premise; Ch 1 produces the first runnable code.

**Preview of next chapter:** Chapter 2 covers expressions and types. You'll add the player's starting stats — health, gold, experience — using variables of the right types and arithmetic.

---

## AI Wayback Machine

**Niklaus Wirth** designed Pascal, Modula, and Oberon — and his clear principles for statement design influence Python's syntax.

**Run this:**

```
Who is Niklaus Wirth, and how does their work connect to Python statements we covered in this chapter? Keep it to three paragraphs. End with the single most surprising thing about their career or ideas.
```

→ Search **"Niklaus Wirth"** on Wikipedia.

**Now make the prompt better.** Try one of these:

- Ask it to apply Niklaus Wirth's ideas to a small Python program you'd write.
- Add a constraint: "Answer including criticisms or limits of Niklaus Wirth's framework."

What changes? What gets better? What gets worse?

---

## Exercises

**Warm-up**

1. Write a program that prints your name on one line and your age on the next.

2. Write a program that asks "What is your favorite color?" and prints the response in a sentence: "Your favorite color is [color]."

3. Write a program that uses `print()` with the `sep` parameter to print three words separated by asterisks instead of spaces.

**Application**

4. Write a program that asks for first and last name, then prints the length of each. Example: "Grace has 5 letters. Hopper has 6 letters."

5. Write a program that asks for the length and width of a rectangle (as floats) and prints the area.

6. Write a program that asks for a birth year, calculates the person's age using 2024 as the current year, and prints: "[Name] was born in [year] and is [age] years old."

**Synthesis**

7. Write a currency exchange program. Ask for an amount in USD, a destination country name, and an exchange rate. Print: "100 USD in Japan is 10750 JPY at rate 107.50." Build the output string with concatenation.

8. Write a meal cost calculator. Ask for the cost of a main dish, a beverage, and a dessert, and a tip percentage. Calculate and print the subtotal, tip amount, and total.

**Challenge**

9. Write a program that asks for a two-digit number, then calculates and prints the sum of its digits — without loops or conditionals. The integer division operator `//` and the modulo operator `%` (which gives the remainder after division) are what you need. Look them up or ask Claude. Example: input `47` → output "The sum of the digits in 47 is 11."
