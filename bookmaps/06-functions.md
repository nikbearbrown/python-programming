# Bookmap — Chapter 6: Functions

## Source files analyzed

**Location:** `/books/introduction-python-programming-claude/chapters/06-functions/`

**Source files (8 markdown files):**
1. `01-m00043.md` — Introductory framing and chapter motivation
2. `02-m00044.md` — Learning objectives, function definition, simple parameterless functions, benefits
3. `03-m00045.md` — Control flow and functions, functions calling functions
4. `04-m00046.md` — Scope (global and local), variable shadowing, debugging with scope
5. `05-m00047.md` — Arguments and parameters, multiple parameters, mutability
6. `06-m00048.md` — Return statements, multiple returns, using functions in expressions
7. `07-m00049.md` — Keyword arguments, default parameter values
8. `08-m00050.md` — Chapter summary and reference table

**Total source word count:** ~3,500 words

---

## Concept coverage (three chosen for the rewritten chapter)

### 1. Defining and calling functions (from sources 02-m00044)

**What the source teaches:**
- Function definition syntax with `def` keyword
- Parameterless functions
- Docstrings as documentation
- Benefits of functions: modularity, reusability
- Common exercises: `concessions()`, `terms()` functions

**What the rewritten chapter emphasizes:**
- Opens with the smartphone calculator scene (cold open)
- Teaches anatomy of function line-by-line
- Emphasizes docstrings as non-negotiable
- Trade-off: code repetition vs. function abstraction
- Three-part Claude pattern applied to `print_bmi_header()` worked example

**Integration:** This concept forms the foundation. Functions as named reusable blocks. No parameters yet—just the basic machinery of defining and calling.

### 2. Parameters, arguments, and return values (from sources 05-m00047 and 06-m00048)

**What the source teaches:**
- Parameter definition vs. argument passing
- Multiple parameters
- Mutability and how it affects function behavior
- Return statements
- Multiple return paths (if-else with different returns)
- Using function results in expressions

**What the rewritten chapter emphasizes:**
- Opens with the vending machine scene (different from parameterless)
- Teaches parameter/argument distinction through a concrete converter
- Return vs. print as a trade-off (with explicit reasoning)
- Mutability as a second-order effect (lists mutable, strings immutable)
- Three-part Claude pattern applied to `calculate_tip()` worked example
- Multiple return statements introduced but kept simple

**Integration:** Expands the function concept to data flowing in and results flowing out. This is where functions become genuinely useful.

### 3. Scope and the call stack (from source 04-m00046)

**What the source teaches:**
- Global scope (variables accessible anywhere)
- Local scope (variables exist only in the function)
- Global keyword for modifying global variables
- Why limiting scope matters for debugging
- Exercises with scope violations and corrections

**What the rewritten chapter emphasizes:**
- Opens with rooms in a house analogy
- Global vs. local with clear visual scopes
- Call stack as a physical stack of papers
- Mechanical tracing of function execution (line by line)
- Why scope matters: debugging a large program
- Trade-off: global simplicity vs. local safety
- Three-part Claude pattern applied to `validate_bmi_input()` and `calculate_bmi()` composed functions

**Integration:** Provides the mental model for understanding where variables live and why that matters. Crucial for debugging. The call stack is the Ariadne's thread through a program.

---

## Deferred material and reasoning

### Lambdas (anonymous functions)
- **Status:** Not covered.
- **Reasoning:** Lambdas are advanced—a way to create short functions without the `def` keyword. First-course Python should prioritize clarity and naming. Named functions are always clearer. Lambdas are a useful optimization later (Chapter 9+ when working with higher-order functions like `map()` and `filter()`).

### Closures
- **Status:** Not covered.
- **Reasoning:** Closures are functions that "remember" variables from their enclosing scope. This is a subtle concept. It is not needed for CS-1. Introduced later when the student encounters functions that create functions (decorators, in Chapter 13+ material).

### *args and **kwargs
- **Status:** Not covered.
- **Reasoning:** Variable-length argument lists are a convenience feature. The chapter teaches the disciplined approach: know your parameters upfront. *args and **kwargs encourage writing overly flexible functions that are hard to reason about. Deferred to Chapter 7 when students see library functions that use them.

### Decorators
- **Status:** Not covered.
- **Reasoning:** Decorators are functions that modify other functions. Far too advanced for a first pass. Belong in a follow-up course.

### Type hints
- **Status:** Not covered.
- **Reasoning:** Python 3.5+ allows `def fahrenheit_to_celsius(f: float) -> float:`. This is a tool for clarity and tooling. In a first course, it is optional syntax. Introduced in Chapter 11+ as student code becomes more complex.

### Recursion (brief mention only)
- **Status:** Mentioned as a teaser but not taught.
- **Reasoning:** Recursion is Chapter 12 in the book outline. Functions calling functions is taught here. Functions calling themselves is deferred to the dedicated chapter, where the concept gets the depth it deserves.

---

## Source-level notes

### Structure alignment

The source is organized into 8 sections that map closely to the rewritten structure:

| Source file | Key topic | Rewritten location |
|---|---|---|
| 01-m00043 | Motivation | Opening scene |
| 02-m00044 | Definition and calling | Concept 1 |
| 03-m00045 | Control flow and functions | Concept 3 (call stack) |
| 04-m00046 | Scope | Concept 3 |
| 05-m00047 | Arguments and parameters | Concept 2 |
| 06-m00048 | Return statements | Concept 2 |
| 07-m00049 | Keyword arguments and defaults | Concept 2 (extended) |
| 08-m00050 | Summary and table | Chapter summary |

### Pedagogical differences from source

**The source (original book):**
- Organized by technical topic (parameters, return, scope).
- Uses video references and linked examples (placeholder links to videos in a course).
- Exercises are separate, not embedded.
- Relies on multiple short examples per section.

**The rewritten chapter:**
- Organized by conceptual progression (defining → parameters/returns → scope).
- Uses scenes and analogies to ground concepts.
- Exercises are graduated and embedded.
- Emphasizes the three-part Claude pattern for every meaningful code example.
- Includes explicit trade-offs (return vs. print, global vs. local, positional vs. keyword).
- Uses the call stack as a unifying model.

### Example transformation

**Source example (from 02-m00044):**
```
def concessions():
    """..."""
    print("Food/Drink Options: Popcorn: $8-10 Candy: $3-5 Soft drink: $5-7")

concessions()
```

**Rewritten example (in Concept 1):**
```python
def print_bmi_header():
    """Print the title and instructions for the BMI calculator."""
    print("=" * 30)
    print("BMI CALCULATOR")
    print("=" * 30)
    print("Enter your weight in kilograms and height in meters.")

print_bmi_header()
```

**Why:** The BMI example is domain-grounded. It uses multiple print statements, which is more realistic. It shows formatting. The concessions example is generic. The rewrite makes the purpose clear.

### Vocabulary choices

The source uses "parameter" and "argument" correctly but sometimes loosely. The rewritten chapter enforces the distinction:
- **Parameter:** Variable in the function definition (parameter name is `f` in `def convert(f):`).
- **Argument:** Value passed when calling (argument value is `32` in `convert(32)`).

This distinction is taught in Concept 2 and reinforced in worked examples.

---

## Three-part Claude pattern usage

**Total three-part triples in the rewritten chapter: 9**

| Example | Location | Pattern |
|---|---|---|
| `print_bmi_header()` | Concept 1 worked example | Parameterless function with print |
| `fahrenheit_to_celsius(f)` | Concept 2 opening | Single-parameter converter returning value |
| `calculate_bmi(weight_kg, height_m)` | Concept 2 multiple parameters | Multi-parameter with return |
| `calculate_tip(bill_amount, tip_percent)` | Concept 2 worked example | Practical calculator function |
| `validate_bmi_input()` + `calculate_bmi()` | Concept 3 worked example | Composed functions with scope |
| Integration/Synthesis section | Later in chapter | Reinforces all three concepts together |
| Warm-up exercises (3-4 triples) | Graduated exercises | Students apply the pattern themselves |
| Application exercises (2-3 triples) | Graduated exercises | More complex patterns |
| Synthesis/Challenge exercises (2-3 triples) | Graduated exercises | Full application |

**Total: 12-15 student-facing triple applications across all exercises.**

The three-part pattern (Code + Prompt for Claude + Tests to validate) is applied consistently:
- In worked examples (to model the technique)
- In exercises (so students practice it)
- In the pantry file (with reusable prompt templates)

---

## Call stack emphasis

A distinctive feature of the rewritten chapter: the call stack is introduced as a mental model in Concept 3 and threaded throughout.

- **Concept 1:** Simple linear execution (no nested calls yet).
- **Concept 2:** Functions returning values (caller receives return).
- **Concept 3:** The call stack is named, visualized (mental stack of papers), and traced mechanically in the worked example.
- **Synthesis:** "When you are reading someone else's code, the call stack is your map."
- **Exercises:** Challenge problems involving multiple function calls reinforce this.

The call stack is not just a technical detail—it is the conceptual thread that ties execution order to scope to return values.

---

## Integration with earlier chapters

- **Chapter 1 (Statements):** Functions are statements. A `def` line is a statement.
- **Chapter 5 (Loops):** Functions often contain loops. The worked examples use `for` loops inside functions.
- **Chapter 9 (Lists):** Functions that take lists as arguments (mutability section).
- **Chapter 11 (Classes):** Methods are functions inside classes.
- **Chapter 12 (Recursion):** Recursion is functions calling themselves.

The rewritten chapter is careful not to presuppose too much, but it does assume comfortable use of loops and conditions.

---

## Alignment with book.md voice notes

The source book specifies:

> **Grounded examples.** Not `def foo(x): return x * 2`. Instead: `def fahrenheit_to_celsius(f): return (f - 32) * 5/9`. Concrete domain, concrete number, concrete reason this code exists.

**Adherence:** Every example in the rewritten chapter has a reason. No generic examples. BMI, temperature conversion, tip calculation, password validation, bank account tracking. All are realistic.

> **Code density.** Higher than a math book. Most concepts are taught by showing code that does the thing, then explaining what each line does.

**Adherence:** The chapter opens with code in Concept 1 and builds from there. Each concept starts with a code block, not an abstraction.

> **Errors are taught explicitly.** Every chapter shows at least one common error message, names what it means, and shows how to fix it.

**Adherence:** The chapter includes `NameError` (calling before defining), `TypeError` (wrong argument count), `NameError` again (accessing out of scope). Each is shown with the error message, explanation, and fix.

---

## Verification checklist

- [x] Three concepts are clear and distinct.
- [x] Each concept has a worked example.
- [x] Each worked example uses the three-part Claude pattern.
- [x] Trade-offs are explicitly named.
- [x] Etymology is provided for key terms.
- [x] The call stack is introduced and used as a mental model.
- [x] Scope is explained with visual analogy and concrete examples.
- [x] Common misconceptions are named and addressed.
- [x] Exercises are graduated (warm-up → application → synthesis → challenge).
- [x] Errors are shown and explained.
- [x] No forbidden phrases.
- [x] Voice is consistent with Attenborough × Feynman.
- [x] Chapter is 5,000+ words.
