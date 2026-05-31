# Figures — Chapter 6: Functions

## Figure 1: Function anatomy (diagram)

**Title:** Anatomy of a function definition

**Description:** A Python function definition with each part labeled.

```
def fahrenheit_to_celsius(f):           <- keyword, name, parameters
    """Convert Fahrenheit to Celsius."""  <- docstring
    return (f - 32) * 5/9                <- body, return statement
```

**What it shows:** The four components of a function definition: the `def` keyword, the function name, the parentheses with parameters, the colon, the indented docstring, and the indented body with the return statement.

**Why it matters:** New programmers often omit the docstring or forget the colon. This diagram makes all parts explicit.

---

## Figure 2: Call stack visualization (diagram)

**Title:** Call stack as execution moves through functions

**Description:** A timeline showing the call stack as a program executes.

```
main:
├─ temperature_report(98.6)
│  ├─ Stack: [main, temperature_report]
│  ├─ Calls convert_to_celsius(98.6)
│  │  ├─ Stack: [main, temperature_report, convert_to_celsius]
│  │  ├─ Returns 37.0
│  │  └─ Stack: [main, temperature_report]  <- popped off
│  ├─ Prints "98.6°F = 37.0°C"
│  └─ Stack: [main]  <- popped off
```

**What it shows:** As functions are called, they are pushed onto the stack. As they return, they are popped off. The currently executing function is always at the top.

**Why it matters:** Understanding the call stack is key to tracing execution and debugging errors. Students often struggle with "where am I in the program?" The stack makes this explicit.

---

## Figure 3: Scope diagram (diagram)

**Title:** Global and local scope in a program

**Description:** A visual representation of which variables are accessible where.

```
Global scope:
  greeting = "Hello"      <- accessible everywhere
  total_sales = 0         <- accessible everywhere
  
  function print_greeting():
      local scope:
        local_greeting = "Hi"  <- only here
        
  function calculate_tax(amount):
      local scope:
        tax_amount = amount * 0.08  <- only here
        total_sales = tax_amount    <- shadows global total_sales!
```

**What it shows:** Global variables are accessible from everywhere. Local variables are accessible only within their function. A local variable with the same name as a global variable shadows (hides) the global variable.

**Why it matters:** Scope confusion is the most common source of bugs for new programmers. This diagram makes it clear: inside a function, you see local variables first; if the name is not local, you can access global.

---

## Figure 4: Parameter vs. argument illustration (diagram)

**Title:** Parameters in definition vs. arguments in call

**Description:** Side-by-side comparison of definition and call.

```
Definition:                     Call:
def add(a, b):                  add(5, 3)
    ↑ parameters                    ↑ arguments
    return a + b
    
    When the function is called:
    a is assigned the value 5
    b is assigned the value 3
```

**What it shows:** Parameters are placeholders in the definition. Arguments are the actual values passed when the function is called. Parameter `a` receives argument `5`.

**Why it matters:** The terms "parameter" and "argument" are often confused. This diagram makes the distinction clear: one is in the definition, one is in the call.

---

## Figure 5: Return vs. print comparison (diagram)

**Title:** What happens when a function returns vs. prints

**Description:** Execution flow for two versions of the same function.

```
Version A: Print inside the function
def convert(f):
    c = (f - 32) * 5/9
    print(c)  <- output appears here
    
    convert(32)  <- call
    result = ???  <- nothing to assign

Version B: Return from the function
def convert(f):
    c = (f - 32) * 5/9
    return c  <- value goes back to caller
    
    result = convert(32)  <- call receives the value
    print(result)  <- output appears here, caller decides
```

**What it shows:** When a function prints, the output appears but nothing is returned to the caller. When a function returns, a value is sent back and the caller decides what to do with it.

**Why it matters:** This is a critical distinction. Students often print when they should return, limiting the usefulness of the function.

---

## Figure 6: Scope rules as a flowchart (diagram)

**Title:** How Python looks up a variable name

**Description:** Decision tree for variable lookup.

```
Looking up variable name 'x':

Is 'x' defined locally (in current function)?
  YES -> use the local version
  NO  -> Is 'x' defined globally?
          YES -> use the global version
          NO  -> NameError: name 'x' is not defined
```

**What it shows:** Python's variable lookup order: local first, then global, then error.

**Why it matters:** Explains why a local variable shadows a global, and why you get a `NameError` when a variable is not found.

---

## Figure 7: Multiple parameters example (code with annotation)

**Title:** How multiple parameters map to multiple arguments

**Description:** Detailed walkthrough of a function call with multiple arguments.

```python
def calculate_total_cost(item_price, quantity, tax_rate):
    """Calculate total cost including tax."""
    subtotal = item_price * quantity
    tax = subtotal * tax_rate
    return subtotal + tax

# Call the function:
total = calculate_total_cost(19.99, 3, 0.08)
                             ↑      ↑  ↑
                     item_price quantity tax_rate
                         ↓        ↓      ↓
                         19.99    3      0.08
```

**What it shows:** Each argument is matched to a parameter in order (position matters with positional arguments).

**Why it matters:** Illustrates positional argument mapping, which is the source of common bugs when parameter order is misunderstood.

---

## Figure 8: Keyword arguments flexibility (code comparison)

**Title:** Same call, three ways to write it

**Description:** Shows positional, keyword, and mixed argument styles.

```python
# All three calls do the same thing:

# 1. Positional (order matters)
calculate_total_cost(19.99, 3, 0.08)

# 2. Keyword (order does not matter)
calculate_total_cost(quantity=3, tax_rate=0.08, item_price=19.99)

# 3. Mixed (positional first, then keyword)
calculate_total_cost(19.99, quantity=3, tax_rate=0.08)

# This is clearer for the reader: "I know what each argument is"
```

**What it shows:** Keyword arguments allow any order and explicit naming.

**Why it matters:** Helps students understand that positional and keyword arguments are flexible tools, not rules.

---

## Figure 9: Function composition (flowchart)

**Title:** One function calling another

**Description:** Flow of execution and data as functions call each other.

```
main program
    │
    └─ calls process_temperature(32)
        │
        ├─ calls fahrenheit_to_celsius(32)
        │   └─ returns 0.0
        │
        ├─ prints "32°F = 0.0°C"
        │
        └─ returns to main
```

**What it shows:** How control flows and how data (the return value) flows back.

**Why it matters:** Helps students understand function composition and the call stack.

---

## Figure 10: Mutability and function arguments (before/after)

**Title:** What changes inside a function and what doesn't

**Description:** Mutable vs. immutable arguments.

```
Immutable (int):           Mutable (list):
def double(x):             def double_all(nums):
    x = x * 2   <- local    for i in range(len(nums)):
    return x        change      nums[i] = nums[i] * 2
                                return nums
n = 5
double(n)          <- n is still 5   mylist = [1, 2, 3]
                      (int is              double_all(mylist)
                       immutable)           <- mylist is [2, 4, 6]
                                              (list is mutable)
```

**What it shows:** Immutable objects (int, str) cannot be modified by a function unless the modification is returned and reassigned. Mutable objects (list, dict) can be modified inside a function.

**Why it matters:** This is confusing behavior that leads to bugs. Making it visual helps students understand.

---

## Figure 11: Default parameter values (syntax)

**Title:** How default values work

**Description:** Function definition with and without defaults, and how calls behave.

```python
def greet(name, greeting="Hello"):
         ↑   parameter 1    ↑ parameter 2 with default
         required           optional (can be omitted)

greet("Alice")             <- greeting defaults to "Hello"
greet("Bob", "Hi")         <- greeting is "Hi"
greet(greeting="Hey", name="Carol")  <- keyword args, any order
```

**What it shows:** Default values make parameters optional. If the caller does not provide the argument, the default is used.

**Why it matters:** Default values are a key tool for flexible function design.

---

## Figure 12: Error messages and the call stack (screenshot annotation)

**Title:** Reading a stack trace

**Description:** Real Python error output with annotations.

```
Traceback (most recent call last):
  File "program.py", line 10, in <module>
    temperature_report(32)
    ↑ where the error originated (line 10, main program)
  File "program.py", line 5, in temperature_report
    convert_to_celsius(32)
    ↑ function 1 called from line 5
  File "program.py", line 2, in convert_to_celsius
    return f / 0  <- division by zero
           ↑ the error occurred here
ZeroDivisionError: division by zero
```

**What it shows:** A stack trace reads bottom-to-top. The bottom shows where the error happened. The lines above show which functions were active.

**Why it matters:** Stack traces are intimidating for new programmers. Teaching students to read them bottom-to-top makes debugging tractable.

---

## Implementation notes for figures

- Figures 1, 3, 4, 5, 6, 9, 10, 11 should be simple ASCII diagrams or SVG boxes with clear labels.
- Figure 2 and 9 can be animated GIFs or static diagrams showing execution flow over time.
- Figure 12 can be a code snippet with overlaid annotations (the actual error message from running Python).
- All figures should use consistent colors: global scope in one color, local scope in another, function names in a third.
- Avoid 3D effects or unnecessary decoration. Clarity is the goal.
