# Chapter 1 — Statements

## Three possible titles

- **Your first line of code — and what happens when you run it**
- **Making Python do something: statements, output, and the sound of your instruction being obeyed**
- **From typing to seeing — the machinery of statements, variables, and arithmetic**

## TL;DR

A statement is a single instruction to the Python interpreter. The simplest statements — `print()` and `input()` — let you talk to a program and have it talk back. Everything that follows builds on this: storing values in variables, doing arithmetic, reading error messages.

---

## Cold open

You open a terminal for the first time, or an interactive Python window. Your fingers find the keyboard. You type:

```python
print("Hello, world!")
```

You press Enter.

The screen shows:

```
Hello, world!
```

That's the moment. That single line. You told the computer what to do, and it did it, and you saw the result. That moment is the entire point of this chapter. Everything we do for the next few weeks is building on that one fact: you can instruct a computer. The computer understands you. The conversation happens.

A **statement** — from Latin *status*, meaning a state — is a single instruction that tells Python to do something. Most Python programs are a list of statements, one after another. The computer runs them in order, top to bottom. `print()` is a statement. It tells Python: display this. Variables hold information. Assignment (`=`) puts information into a variable. Arithmetic (`+`, `-`, `*`, `/`) transforms information. Your job in this chapter is to learn to write these statements and to read the error messages when they go wrong.

### Learning objectives

By the end of this chapter you will be able to:

- **Write** output statements with `print()` and understand what `sep` and `end` control.
- **Read** input from a user with `input()` and store it in a variable.
- **Assign** values to variables and understand the rule that assignment is *binding*, not equation.
- **Perform** basic arithmetic on numbers — integers and floats — and understand operator precedence.
- **Concatenate** strings (stick them together) and find the length of a string.
- **Read** three kinds of error messages (`SyntaxError`, `NameError`, `IndentationError`) and know what to fix.
- **Write** programs that take input, do something to it, and produce output.

### Prerequisites

- Ability to open a terminal and run a Python interpreter or editor.
- High school algebra: plugging numbers into formulas, following order of operations.
- No prior programming.

### Why this chapter matters

Python is conversation. This chapter is how you start the conversation. Every line of code you write after this is a variation on these first statements. `print()` lets you see what your code did. `input()` lets the user tell your code what to do. Variables are how you remember things. Arithmetic is how you change things. Errors are how you learn what went wrong. These five tools are the foundation of everything else.

---

## Concept 1 — Talking to the program: output and input

When you run a Python program, the program often needs to communicate with you. It needs to show you results. It needs to ask you questions. That's what `print()` and `input()` do.

### The `print()` function — making the program speak

`print()` is a **function** — a named chunk of reusable code that does a task. When you write `print("Hello")`, you are *calling* the function, asking it to do its job. The word in the parentheses is the *argument* — the information you give the function to work with.

```python
print("Today is Monday.")
print("I like string beans.")
```

produces:

```
Today is Monday.
I like string beans.
```

Each `print()` statement outputs its argument to the screen and then moves to the next line. That movement to the next line is automatic — Python adds a newline character at the end of every `print()` statement unless you tell it not to.

You can print multiple values in one statement by separating them with commas:

```python
print("Team:", "Liverpool", "versus", "Chelsea")
```

produces:

```
Team: Liverpool versus Chelsea
```

By default, the values are separated by a single space. You can change this with the `sep` parameter — the word *sep* is short for separator:

```python
print("Team", "Liverpool", "versus", "Chelsea", sep=" ... ")
```

produces:

```
Team ... Liverpool ... versus ... Chelsea
```

You can also control what appears at the end of the output with the `end` parameter. By default it's a newline (the move to the next line). You can change it:

```python
print("First part: ", end="")
print("Second part.")
```

produces:

```
First part: Second part.
```

The `end=""` says "don't add a newline; instead, add nothing." This is useful when you want two `print()` statements to appear on the same line.

### The `input()` function — listening to the user

`input()` does the opposite of `print()`. It listens. It reads what the user types.

```python
name = input("What is your name? ")
```

This line does three things:
1. The text in the parentheses — `"What is your name? "` — is shown on the screen.
2. The program waits for the user to type something and press Enter.
3. Whatever the user typed is stored in the variable `name`.

A **variable** — a name that *varies* — is a place in the program's memory where a value lives. The name stays the same (`name`), but the value stored there can be different each time the program runs.

Once you have the input stored in a variable, you can use it again:

```python
name = input("What is your name? ")
print("Hello, " + name)
```

If the user types `"Alice"`, the program prints:

```
Hello, Alice
```

### Worked example: writing a greeting program

Write a program that asks the user for their name and age, then prints a personalized greeting.

```python
name = input("What is your name? ")
age = input("How old are you? ")
print("Hello, " + name + "! You are " + age + " years old.")
```

Test by running it with user input `Alice` and `25`:

```
What is your name? Alice
How old are you? 25
Hello, Alice! You are 25 years old.
```

> **Prompt for Claude.** "Write a Python program that asks the user for their name and age, then concatenates and prints a greeting message. The program should use `input()` twice, store the results in variables, and use string concatenation to build the message. Test with the inputs Alice and 25."

> **Tests to validate.**
> - Program prompts for name and age in the correct order
> - Program prints a message containing the name and age
> - Output is readable and contains the words "Hello" or a greeting
> - Program works with any name and any age value

### Common misconceptions

**`print()` is not the same as returning a value.** When a function *returns* a value, the value is available for further use in the code. When `print()` displays something, that's just for human eyes. The value doesn't get passed along to the next line. This will matter later when we write functions.

**The prompt in `input()` is optional but recommended.** You can write `input()` with nothing inside: `name = input()`. The program will still wait for input, but the user won't see a prompt telling them what to type. Always add a prompt so the user knows what's expected.

**`input()` always returns a string.** Even if the user types a number, like `25`, `input()` treats it as text. If you need an actual number, you have to convert it, which we'll cover in the next chapter.

---

## Concept 2 — Storing and changing values: variables and assignment

A variable is a name that points to a value stored in the computer's memory. The name stays the same; the value it points to can change. That's why it's called a *variable* — it *varies*.

### The assignment operator: `=` means binding, not equation

In mathematics, `=` means *equals*. In Python, `=` means something different. It's called the **assignment operator**. It says: "Take the value on the right side and store it in the variable on the left side."

```python
count = 0
count = 5
count = 10
```

After the first line, `count` holds `0`. After the second line, `count` holds `5` — the old value is gone. After the third line, `count` holds `10`. The name `count` never changed; what it points to changed.

This is the core trade-off in programming: **assignment as rebinding** versus the mathematical idea of *equation*. In Python, if I write:

```python
x = 5
x = x + 1
```

I'm not claiming that 5 equals 6. I'm saying: "Look at the current value of `x` (which is 5), add 1 to it (getting 6), and store the result back in `x`." Now `x` is 6. This looks odd at first. It stops looking odd once you understand that `=` doesn't mean "these two things are the same." It means "take the right side, evaluate it, put the result in the box on the left."

### Variable naming rules

Python requires that you follow certain rules when naming a variable:

- A variable name can contain letters, digits, and underscores (`_`).
- A variable name cannot start with a digit. `101class` is invalid; `class101` is fine.
- A variable name is case-sensitive. `Total` and `total` are different variables.
- Python has reserved words (keywords) that have special meaning and cannot be used as variable names. Examples: `if`, `for`, `while`, `return`, `class`, `def`.

Beyond the rules, there's a style guide. Python's official style guide recommends **snake_case**: all lowercase, with underscores between words. `first_name` is better than `firstname`. `total_price` is better than `totalPrice`. This makes code easier to read.

Choose descriptive names. A variable called `temperature` tells you what the value represents. A variable called `x` makes you guess. Professional programmers spend more time reading code than writing it, so readability matters.

```python
age = 25
user_email = "alice@example.com"
balance = 1500.50
product_count = 8
```

Each name describes what's stored in it.

### Worked example: converting currencies

Write a program that asks the user for an amount in US dollars and converts it to euros. (Use 1 USD = 0.92 EUR as the exchange rate.)

```python
dollars = input("Amount in USD: ")
dollars = float(dollars)
exchange_rate = 0.92
euros = dollars * exchange_rate
print(dollars, "USD =", euros, "EUR")
```

Test by running with input `100`:

```
Amount in USD: 100
100.0 USD = 92.0 EUR
```

> **Prompt for Claude.** "Write a Python program that takes a dollar amount from the user as input, converts it to euros using an exchange rate of 0.92, and prints the result. Use the variable names `dollars`, `exchange_rate`, and `euros`. Include the `float()` function to convert the input to a number."

> **Tests to validate.**
> - Program accepts user input for the dollar amount
> - Program correctly multiplies by 0.92
> - Program prints both the original and converted amounts
> - Test: 100 USD should give 92.0 EUR
> - Test: 50 USD should give 46.0 EUR

### Common misconceptions

**Variables don't have types; values do.** In Python, a variable is just a name. The value it holds has a type. You can rebind the same variable name to a value of a different type: `x = 5` (now `x` is an integer), then `x = "hello"` (now `x` is a string). The variable name doesn't care; it just holds whatever you put in it.

**Assignment is one-way.** `x = y` copies the value in `y` into `x`. It doesn't create a link. If `y` changes later, `x` stays the same.

---

## Concept 3 — Types and arithmetic: integers, floats, and operations

A **type** is a category of value. Python has many types, but we start with three: integers, floats, and strings.

An **integer** is a whole number: 5, -17, 0, 1000. An **integer** comes from the Latin *integer*, meaning "untouched" or "whole" — a number without a fractional part.

A **float** (short for floating-point) is a decimal number: 3.14, -2.5, 0.0, 1000.001. The term comes from the position of the decimal point — it *floats*. In the computer's memory, floats are stored differently than integers, which matters for precision (but we don't need to worry about that yet).

A **string** is text: `"Hello"`, `'Alice'`, `"123"`. Strings are enclosed in matching quotes.

```python
age = 25           # integer
price = 19.99      # float
name = "Bob"       # string
```

### Arithmetic operators and precedence

Python can do arithmetic. The basic operators are:

- `+` : addition
- `-` : subtraction
- `*` : multiplication
- `/` : division (always returns a float, even if the result is a whole number)
- `**` : exponentiation (power)

```python
print(10 + 3)       # 13
print(10 - 3)       # 7
print(10 * 3)       # 30
print(10 / 3)       # 3.3333...
print(10 ** 3)      # 1000 (10 to the power of 3)
```

When you have multiple operators in one expression, **operator precedence** determines the order they're evaluated. Just like in algebra, multiplication and division happen before addition and subtraction. Exponentiation happens before everything else. Parentheses override everything.

```python
print(2 + 3 * 4)        # 14 (not 20) — multiply 3 * 4 first
print((2 + 3) * 4)      # 20 (parentheses first)
print(2 ** 3 ** 2)      # 512 (right to left: 3 ** 2 = 9, then 2 ** 9 = 512)
```

The precedence order, from highest to lowest:

1. Parentheses `()`
2. Exponentiation `**`
3. Multiplication and division `*` and `/`
4. Addition and subtraction `+` and `-`

When operators are at the same level, Python evaluates left to right, except exponentiation, which goes right to left.

### Worked example: simple budget calculation

Write a program that asks the user for monthly income, housing cost, food cost, and transportation cost, then calculates savings (income minus all expenses).

```python
income = float(input("Monthly income: "))
housing = float(input("Housing cost: "))
food = float(input("Food cost: "))
transportation = float(input("Transportation cost: "))

total_expenses = housing + food + transportation
savings = income - total_expenses

print("Income:", income)
print("Expenses:", total_expenses)
print("Savings:", savings)
```

Test with input: income = 3000, housing = 1200, food = 400, transportation = 200:

```
Monthly income: 3000
Housing cost: 1200
Food cost: 400
Transportation cost: 200
Income: 3000.0
Expenses: 1800.0
Savings: 1200.0
```

> **Prompt for Claude.** "Write a Python program that calculates monthly savings. Ask the user for monthly income, housing cost, food cost, and transportation cost. Convert each input to a float. Calculate total expenses by adding the three costs. Calculate savings as income minus total expenses. Print the income, expenses, and savings amounts."

> **Tests to validate.**
> - Program reads all four inputs correctly
> - Program uses `float()` conversion on each input
> - Total expenses equals housing + food + transportation
> - Savings equals income - expenses
> - Test: income 3000, expenses 1800 should give savings 1200

### A scale shift: why precision matters

Right now, you're working with simple numbers in a program that runs on your screen. But Python powers applications that move money, control machinery, and guide medical devices. When you write `price * quantity`, that might be charging someone a credit card. When you write `altitude - descent_rate`, that might be landing an airplane. The difference between an integer and a float, or the difference between 3.14 and 3.14159, can matter.

In this book, we'll stay in small, safe examples. But as you grow, you'll learn about rounding errors, about when to use integers and when floats, about precision libraries for money. The habit starts here: *think about what your numbers mean*.

### Common misconceptions

**Division always returns a float.** Even if you divide two integers that divide evenly, like `10 / 2`, Python returns `5.0`, not `5`. If you want an integer result, you need the *floor division* operator `//`, which we'll see in a later chapter.

**Types matter for operations.** You can't add a string and an integer: `"5" + 5` is a `TypeError`. You can add two integers (`5 + 3 = 8`), two floats (`5.0 + 3.0 = 8.0`), or two strings (`"5" + "3" = "53"`), but not a mix.

---

## String operations: length and joining

Strings are values, and like integers and floats, they support operations. Two operations are especially useful: finding the length and joining (concatenating) two strings.

### Length: the `len()` function

`len()` tells you how many characters are in a string:

```python
print(len("hello"))           # 5
print(len("Hello, World!"))   # 13 (including the space and punctuation)
print(len(""))                # 0 (empty string)
```

This is useful when you're reading user input and want to check that they gave you something:

```python
name = input("Your name: ")
if len(name) == 0:
    print("You didn't enter anything.")
else:
    print("Hello, " + name)
```

(The `if` statement is coming in Chapter 4. For now, just notice that `len()` lets you check the length.)

### Concatenation: joining strings with `+`

You've already seen concatenation — using `+` to join strings:

```python
first = "Alice"
last = "Smith"
full_name = first + " " + last
print(full_name)             # Alice Smith
```

Notice the space in the middle: `" "`. Without it, you'd get `"AliceSmith"`, which looks wrong.

A key difference from arithmetic: when you concatenate strings, the `+` operator doesn't do addition. It joins them:

```python
"5" + "3"        # "53" (not 8)
5 + 3            # 8 (not "53")
"5" + 3          # TypeError — can't mix strings and numbers
```

This is the trade-off with operator overloading: the same symbol (`+`) does different things depending on the types. Addition for numbers. Concatenation for strings. It's convenient once you're used to it, but it's a source of errors when you're starting out.

### Worked example: building a welcome message

Write a program that asks for a user's first name, last name, and city, then prints a personalized welcome message.

```python
first = input("First name: ")
last = input("Last name: ")
city = input("City: ")

message = "Welcome, " + first + " " + last + ", from " + city + "!"
print(message)
```

Test with input: first = "Grace", last = "Hopper", city = "New Haven":

```
First name: Grace
Last name: Hopper
City: New Haven
Welcome, Grace Hopper, from New Haven!
```

> **Prompt for Claude.** "Write a Python program that asks the user for a first name, last name, and city. Concatenate these inputs into a welcome message using string concatenation with the + operator. The message should read 'Welcome, [first] [last], from [city]!' Print the message."

> **Tests to validate.**
> - Program reads three inputs in order
> - Program concatenates with spaces in the right places
> - Output includes the user's name and city
> - Test: Grace Hopper from New Haven produces expected output

---

## Integration: putting it together

By now you have the pieces: output, input, variables, arithmetic, and strings. A real program weaves these together.

Here's a program that takes a temperature in Fahrenheit and converts it to Celsius:

```python
fahrenheit = float(input("Temperature in Fahrenheit: "))
celsius = (fahrenheit - 32) * 5 / 9
print(fahrenheit, "F =", celsius, "C")
```

Test with input `98.6`:

```
Temperature in Fahrenheit: 98.6
98.6 F = 37.0 C
```

This program:
- Uses `input()` to listen to the user
- Uses `float()` to convert the string to a number
- Uses arithmetic with parentheses to follow order of operations
- Uses `print()` with multiple arguments to show the result

That's the conversation between you and the program. You give it data (`98.6`). It transforms the data (subtracts 32, multiplies by 5, divides by 9). It shows you the result.

Every program you write, no matter how large, is built from this pattern: input, process, output.

---

## Error messages: learning to read them

Making mistakes is the normal part of programming. Python tries to help by giving you error messages. Learning to read those messages is a crucial skill.

When Python encounters a problem, it prints a **traceback** — a report of what went wrong and where. Let's look at three common errors.

### SyntaxError: the punctuation mistake

A **SyntaxError** means you broke the rules of Python's grammar. The computer can't even *parse* your code (understand what you meant to write).

```python
print("Hello"
```

produces:

```
Traceback (most recent call last):
  File "example.py", line 1
    print("Hello"
          ^
SyntaxError: '(' was never closed
```

The caret (`^`) points to where Python got confused. In this case, you opened a parenthesis in `print(` but never closed it. Fix it by adding the closing parenthesis:

```python
print("Hello")
```

Other common syntax errors:
- Missing quotes: `print(hello)` instead of `print("hello")`
- Missing colon at the end of a line (you'll see this when we get to `if` statements)
- Using `==` (comparison) instead of `=` (assignment) when you meant to assign

### NameError: the undefined variable

A **NameError** means you used a variable name that doesn't exist.

```python
print(age)
```

produces:

```
Traceback (most recent call last):
  File "example.py", line 1, in <module>
    print(age)
NameError: name 'age' is not defined
```

You never assigned anything to `age`, so Python doesn't know what to do with it. Fix it by defining the variable first:

```python
age = 25
print(age)
```

Other common name errors:
- Misspelling a variable: `print(nme)` instead of `print(name)`
- Misspelling a function: `print("hello")` works, but `prnt("hello")` gives a NameError
- Forgetting to use quotes around a string: `name = Alice` instead of `name = "Alice"`

### IndentationError: the spacing mistake

An **IndentationError** means you have unwanted whitespace at the start of a line. This often happens when you're building longer programs with `if` statements and loops (coming later). For now, it's a warning: don't accidentally add spaces at the start of a line.

```python
    print("Hello")
```

(Notice the spaces before `print`.)

produces:

```
Traceback (most recent call last):
  File "example.py", line 1
    print("Hello")
IndentationError: unexpected indent
```

Fix it by removing the spaces:

```python
print("Hello")
```

### How to debug: reading the traceback

When you see an error:
1. **Read the line number.** The error report tells you which line of code has the problem.
2. **Read the error type.** `SyntaxError`, `NameError`, `IndentationError` — each tells you what category of problem to look for.
3. **Read the explanation.** Python usually tells you what it expected or what it found wrong.
4. **Look at the code.** Compare what you wrote to the rules. Nine times out of ten, you'll spot the mistake.

If the error message is confusing, Google it. Thousands of other programmers have had the same error. The Stack Overflow website is a goldmine of answers.

### Worked example: finding and fixing three errors

```python
name = input("Your name: ")
age = input("Your age: ")
print(name, "is", age, "years old")
```

This program has no errors — it works fine. But let's introduce three mistakes and fix them one by one:

**Mistake 1: Missing closing quote**
```python
name = input("Your name: )
age = input("Your age: ")
print(name, "is", age, "years old")
```

Error: `SyntaxError: EOL while scanning string literal`

Fix: Add the missing quote:
```python
name = input("Your name: ")
```

**Mistake 2: Misspelled variable**
```python
name = input("Your name: ")
age = input("Your age: ")
print(nme, "is", age, "years old")
```

Error: `NameError: name 'nme' is not defined`

Fix: Correct the spelling in the `print()` statement:
```python
print(name, "is", age, "years old")
```

**Mistake 3: Unexpected indentation**
```python
    name = input("Your name: ")
    age = input("Your age: ")
    print(name, "is", age, "years old")
```

Error: `IndentationError: unexpected indent`

Fix: Remove the leading spaces:
```python
name = input("Your name: ")
age = input("Your age: ")
print(name, "is", age, "years old")
```

> **Prompt for Claude.** "Write a simple Python program that asks for a user's name and age, stores them in variables, and prints a message combining them. Then create three versions of this program, each with one error: (1) a missing closing quote, (2) a misspelled variable name, (3) unexpected indentation. Write what error message each would produce."

> **Tests to validate.**
> - Correct version runs without error
> - Error version 1 produces `SyntaxError` with message about unterminated string
> - Error version 2 produces `NameError` with message about undefined variable
> - Error version 3 produces `IndentationError` with message about unexpected indent

---

## A brief history of Python: why this language?

In 1982, Guido van Rossum began working at a Dutch research institute called CWI. The team he joined was designing ABC, a language meant to be simple and friendly for teaching and prototyping. ABC worked well for beginners, but it lacked the features professionals needed for real work.

Years later, van Rossum moved to another team that needed to write programs for monitoring computers and analyzing data. The languages available in the 1980s were difficult and verbose for this kind of work. Van Rossum imagined a language that would be both simple (like ABC) and powerful (for professional use).

He started the project in his free time. He named it Python because he was a fan of the British comedy group Monty Python. (The connection to the snake came later.)

Over the next year, van Rossum and his colleagues used Python for real projects. They saw it working. Van Rossum decided to share it with the broader programming community. In 1991, Python 0.9.0 was released. He gave away the entire source code for free.

That choice — making Python simple, making it powerful, and giving it away — has shaped everything since. Today, Python is one of the most popular programming languages in the world. Companies like Google, Instagram, Pixar, NASA, and Apple use it. Millions of programmers use it. Hundreds of thousands of libraries have been written for it.

You can see Python's popularity in two ways:

**The TIOBE Index** ranks programming languages by search volume. Since 2001, TIOBE has tracked which languages are discussed most online. Python entered the top 10 in 2004 and stayed there. In October 2021, Python became the #1 language — the first time in 20 years that anything other than C or Java held the top spot.

**Stack Overflow** is a question-and-answer website for programmers. The number of Python questions asked per month has climbed steadily. In recent years, Python is the most asked-about language on the platform.

Why? Because van Rossum made the right choices: simplicity without sacrificing power, availability without gatekeeping, a language that works for teaching and for production. This chapter is your entry point to that community.

---

## Exercises

### Warm-up

1. Write a program that prints your name on one line and your age on the next line.

2. Write a program that asks the user "What is your favorite color?" and prints the response in a sentence: "Your favorite color is [color]."

3. Write a program that uses `print()` with the `sep` parameter to print three words separated by asterisks instead of spaces.

### Application

4. Write a program that asks for the user's first and last name, then prints the length of each name. Example:
   - Input: "Grace" and "Hopper"
   - Output: "Grace has 5 letters. Hopper has 6 letters."

5. Write a program that asks the user for the length and width of a rectangle (as floats), then calculates and prints the area.

6. Write a program that asks for a person's birth year, calculates their age (using the current year, 2024), and prints: "[Name] was born in [year] and is [age] years old."

### Synthesis

7. Write a program that simulates a simple currency exchange. Ask the user for an amount in USD, a country destination (as a string), and the exchange rate. Calculate the amount in the destination currency and print the result with the country name. (Example: "100 USD in Japan is 10,750 JPY at rate 107.50")

8. Write a program that calculates the total cost of a meal. Ask the user for:
   - The cost of the main dish
   - The cost of a beverage
   - The cost of a dessert
   - The tip percentage
   
   Calculate the subtotal, the tip amount, and the total. Print all three values.

### Challenge

9. Write a program that asks the user for a two-digit number (as input), then:
   - Stores it in a variable
   - Calculates the sum of the tens digit and the ones digit (without using loops or conditionals)
   - Prints the original number and the sum

   Example: Input 47 → output "The sum of the digits in 47 is 11."
   
   (Hint: Use integer division `//` and modulo `%`, which you haven't seen yet. Look them up or ask Claude.)

---

## Claude Code

This is a practical session you run in Claude Code after installing it (see Chapter 0). Open a new session in an empty directory, paste the prompt below into the chat, and watch Claude Code create files, run them, and verify that your code works as expected.

> **Claude Code prompt.**
>
> "Build a simple personal budget program. Create a file called `budget.py`. The program should:
> 
> 1. Ask the user for their name using `input()`.
> 2. Ask the user for their monthly income (a number).
> 3. Ask the user for three expenses: rent, groceries, and entertainment (all numbers).
> 4. Store all values in variables (use snake_case names).
> 5. Calculate total expenses by adding the three costs.
> 6. Calculate remaining money as income minus total expenses.
> 7. Print a summary that includes the user's name and shows their income, total expenses, and remaining money.
> 
> Use `float()` to convert the income and expense inputs to numbers so arithmetic works. Include at least one line that uses multiple arithmetic operations (like adding three expenses). Test the program by running it with the input: name = 'Alex', income = 2500, rent = 1000, groceries = 300, entertainment = 150. The output should show Alex has 1050 remaining."

**What to expect.** Claude Code will create `budget.py` with input prompts, variable assignments, arithmetic operations, and formatted output. It will run the program with your test input and show you the results. Watch for: the program asking for input in the right order, all four expenses correctly added together, and the remaining money calculated as 2500 - (1000 + 300 + 150) = 1050. This session reinforces input/output, variable naming, type conversion, and arithmetic with operator precedence.

**Stretch.** Paste this follow-up: "Add validation to the budget program. After reading the income, check that it's positive (> 0). If it's not, print an error message and exit. Do the same for expenses — each should be >= 0. Hint: You'll need `if` statements (from Chapter 4), but try writing the logic first and I'll help you add the syntax."

---

## Summary

A **statement** is an instruction to Python. `print()` displays output. `input()` reads what the user types. A **variable** stores a value and can vary what it holds. **Assignment** with `=` binds a value to a name — it's not an equation. **Types** include integers (whole numbers), floats (decimals), and strings (text). **Arithmetic operators** (`+`, `-`, `*`, `/`, `**`) transform numbers, and **precedence** determines the order they're evaluated. **String concatenation** with `+` joins strings. **Errors** (SyntaxError, NameError, IndentationError) are learning opportunities. Reading the traceback and matching it to the code gets you unstuck.

You now have the vocabulary and the tools to write programs that talk to the user. Everything that follows — decisions, loops, functions, data structures — is building on this foundation: you listen, you remember, you calculate, you speak.

---

## What would change my mind

If I discovered that the three-part Claude pattern (code + prompt + tests) created more confusion than clarity for CS-1 students, I would revise the pattern. The evidence would be student feedback showing they spent more time parsing the three parts than learning the concept, or that the prompts seemed to discourage their own problem-solving rather than scaffolding it.

---

## Still puzzling

I'm not yet fully satisfied with the treatment of why floats sometimes give slightly different results than expected (like `0.1 + 0.2` not equaling `0.3` exactly). That's a real issue students will hit, but it requires some understanding of binary representation that I haven't built yet. I flag it for a deeper dive in the types section of Chapter 2.

---

## Tags

`python` `statements` `io` `variables` `arithmetic` `strings` `errors` `guido-van-rossum` `syntax`

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

##  AI Wayback Machine
**Niklaus Wirth** was designed Pascal, Modula, and Oberon — and his clear principles for statement design influence Python's syntax.

**Run this:**

```
Who is Niklaus Wirth, and how does their work connect to Python statements we covered in this chapter? Keep it to three paragraphs. End with the single most surprising thing about their career or ideas.
```

→ Search **"Niklaus Wirth"** on Wikipedia.

**Now make the prompt better.** Try one of these:

- Ask it to apply Niklaus Wirth's ideas to a small Python program you'd write.
- Add a constraint: "Answer including criticisms or limits of Niklaus Wirth's framework."

What changes? What gets better? What gets worse?
