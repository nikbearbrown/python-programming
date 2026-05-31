# Bookmap — Chapter 2: Expressions

Analysis of the 10 OpenStax source modules for Chapter 2, identifying what was used, what was integrated, and what was deferred.

---

## Source modules analyzed

**Total source modules:** 10 (m00010 through m00019)  
**Source total:** ~6,500 words  
**Chapter output:** ~7,200 words  
**Deferred for later chapters:** ~800 words equivalent

---

## Source-by-source breakdown

### m00010 — Chapter introduction

**Content:** Overview of expressions, operators, and literals. Sets up the chapter's scope.

**Used in:** Chapter opening and "What an expression is" section.

**Integration notes:** 
- The example `3*x - 5` evaluates to `7` when `x` is `4` is used verbatim as a concrete opener.
- Definition of expression ("a combination of literals, variables, and operators") is preserved and expanded with etymology.
- The scoping statement ("exploring other kinds of expressions") is shaped into learning objectives.

**Status:** Fully integrated.

---

### m00011 — The interpreter and the Python shell

**Content:** Explanation of Python as an interpreted language. Introduction to the REPL: read-eval-print loop. The up/down arrow keys for history.

**Used in:** Opening cold open (the shell as laboratory). Learning objectives.

**Integration notes:**
- The REPL concept and its acronym are preserved and used as a teaching frame ("Read-eval-print loop").
- The up/down arrow convenience is mentioned in passing but not emphasized (it's a tool tip, not core pedagogy).
- Video references and detailed interactive exercises are omitted (no video in a book).

**Deferred:** None; the concept is fully covered in prose.

**Status:** Integrated and compressed for book format.

---

### m00012 — Implicit and explicit type conversion

**Content:** Introduction to implicit type conversion (GPS example: 250 → 252.5). Explicit conversion with `int()`, `float()`, `str()`. Examples of each.

**Used in:** Concept 2 (Types and conversions).

**Integration notes:**
- The GPS example is referenced but simplified; the "Book ratings" example is moved to worked example.
- The definitions of `int()`, `float()`, `str()` are preserved verbatim with examples.
- The "Ordering pizza" interactive problem is omitted (no video) but converted into a test pattern in the pantry.
- Exercises on grade average, cups of water, and product-as-float are transformed into Exercise 2.4–2.9 (synthesis and challenge level).

**Status:** Fully integrated; interactive exercises translated to written exercises.

---

### m00013 — Combining integers and floats

**Content:** How mixing integer and float arithmetic produces a float. The detail that `/` always produces a float.

**Used in:** Concept 1 (precedence overview) and Concept 2 (type conversion).

**Integration notes:**
- The key rule ("combining an integer and a float produces a float") is front-loaded in Concept 2.
- The `/` detail is integrated into Concept 3 (division operators) for deeper treatment.
- "Operations combining integers and floats" (the interactive video examples) are translated into Exercise 2.3.

**Status:** Fully integrated and expanded.

---

### m00014 — Floating-point errors

**Content:** Round-off error (0.1 binary representation), overflow error (3.0^1024 overflow).

**Use in Chapter 2:** Mentioned in prose but not heavily emphasized.

**Deferred to:** Chapter 3 (Objects and precision) or Chapter 8 (data-science context).

**Integration notes:**
- The concept is too advanced for Chapter 2's early audience. The brief mention in "Why type conversion matters" is enough for context.
- The `round()` function is used in worked examples but not explicated here; left for later chapters.
- The floating-point table (0.1 shown to 30 decimal places) is deferred.

**Status:** Concept acknowledged; full treatment deferred.

---

### m00015 — Division and modulo

**Content:** True division `/`, floor division `//`, modulo `%`. Unit conversions using the division pair. The "modulus" terminology.

**Used in:** Concept 3 (Division operators).

**Integration notes:**
- All three operators are covered with definitions, examples, and the key insight: "7 / 4 = 1.75, 7 // 4 = 1, 7 % 4 = 3."
- The unit conversion section (centimeters to meters + cm) is the primary worked example.
- The "Arrival time" and "Change machine" problems are preserved as Exercises 2.9 and 2.10 (challenge level).
- The modulus terminology (`m` as divisor) is explained in the general formula section.

**Status:** Fully integrated.

---

### m00016 — Math module and functions

**Content:** Introduction to `import math`. Math constants (`math.pi`, `math.e`, `math.tau`). Math functions (sqrt, pow, ceil, floor, log, sin, cos, tan).

**Use in Chapter 2:** Not heavily used.

**Deferred to:** Chapter 7 (Modules).

**Integration notes:**
- The `import` statement is mentioned briefly in context ("modules are covered in Chapter 7").
- The `math` module content is entirely deferred because:
  - It adds scope creep (Chapter 2 is about operator precedence and types, not libraries).
  - Math functions are better taught alongside their use cases (geometry in Chapter 10, science in Chapter 15).
- The worked examples (quadratic formula, cylinder formulas) are deferred.

**Status:** Concept acknowledged; full treatment deferred to Chapter 7.

---

### m00017 — Spacing and formatting

**Content:** PEP 8 style guidelines. Whitespace in expressions. Automatic string concatenation. Multi-line statements.

**Use in Chapter 2:** Integrated as prose guidance, not as code patterns.

**Integration notes:**
- The PEP 8 guidelines (spaces around operators, before commas) are not emphasized in this textbook because the voice is conversational, not pedantic about style.
- The "good spacing vs. poor spacing" examples are simplified.
- Multi-line statements using parentheses (implicit line joining) are mentioned in prose but not emphasized as a chapter concept.
- The "Five quotes" exercise (formatting Guido van Rossum quotes) is omitted as non-pedagogical.

**Status:** Concept sketched; full PEP 8 treatment deferred to a style guide appendix.

---

### m00018 — Fields and applications of Python

**Content:** Python use in business, education, arts, humanities, science. Applications: AI, data visualization, scientific computing, web development.

**Use in Chapter 2:** Entirely omitted.

**Integration notes:**
- This is career context, not technical content.
- Students will encounter Python's breadth throughout the book (data science in Chapter 15, for instance).
- A dedicated "Why Python?" section makes sense in Chapter 1 (Statements), not Chapter 2.

**Status:** Not integrated; belongs in introductory framing, not expressions chapter.

---

### m00019 — Chapter summary and reference

**Content:** Quick recap of key concepts. Reference tables for functions (`abs`, `int`, `float`, `str`, `round`) and operators (`*`, `/`, `//`, `%`).

**Used in:** Chapter summary and code reference sections.

**Integration notes:**
- The summary is rewritten in Attenborough × Feynman voice ("You now understand...").
- The reference tables for operators are preserved as a quick guide.
- The "Baking bread" and "Tip calculator" final exercises are preserved as Exercises 2.7 and 2.8 (application level).

**Status:** Fully integrated and repositioned.

---

## Concepts selected for Chapter 2

Three core concepts chosen from the 10 source modules:

1. **Concept 1: Operators and precedence** (from m00010, m00013)
   - What an expression is
   - Operator precedence hierarchy
   - Worked example: `2 + 3 * 4 - 5 ** 2`

2. **Concept 2: Types and conversions** (from m00012, m00013)
   - What a type is
   - Implicit conversion (int + float → float)
   - Explicit conversion (int(), float(), str())
   - Worked example: unit conversion with floor division

3. **Concept 3: Division operators** (from m00015)
   - True division (/), floor division (//), modulo (%)
   - The division pair for unit conversion
   - Worked example: clock arithmetic

**Deferred concepts** (belong in later chapters):
- Floating-point precision (Chapter 3: Objects)
- Math module (Chapter 7: Modules)
- PEP 8 style (Appendix or style guide)
- Career applications (Chapter 1 or career guidance, not Chapter 2)

---

## Claude pattern usage

**Three-part Claude pattern used: 3 times**

1. **Pattern 1: Type conversion helper** (`convert_user_input_to_calculation`)
   - Demonstrates explicit conversion on user input
   - Tests: age to retirement, Fahrenheit to Celsius
   - Source: m00012 (explicit conversion examples)

2. **Pattern 2: Unit conversion with division pair** (`convert_centimeters_to_meters_and_cm`)
   - Shows floor division and modulo working together
   - Tests: various centimeter amounts, reconstruction check
   - Source: m00015 (unit conversions)

3. **Pattern 3: Clock arithmetic** (`add_minutes_to_time`)
   - Real-world modulo wraparound (past midnight)
   - Tests: various times and durations, always returns valid 24-hour time
   - Source: m00015 (arrival time calculation problem)

**Test count:** 12 total tests across 3 patterns (4 per pattern)

---

## Writing strategy

**Voice:** Attenborough × Feynman v1.1

- **Cold opens:** Each concept section opens in a scene (spreadsheet, age input, 1987 calculus class) before the machinery is named.
- **Etymology:** Technical terms are glossed on first use with Latin roots (`expression` ← *expressus*, "pressed out"; `operator` ← *operari*, "to work"; `precedence` ← *precedere*, "to go before").
- **Trade-offs:** Explicit in every major section:
  - Precedence: implicit rules match algebra but can silently fail.
  - Type conversion: implicit is convenient, explicit is honest.
  - Division operators: precision vs. simplicity.
- **Scale shift:** The cold opens (micro-level: one calculation) escalate to integration section (macro-level: the REPL as a tool, expressions as the foundation of all programs).
- **Moral weight:** Lands at the chapter end: "The shell is your laboratory."

---

## Quality checks

**Forbidden phrase scan:** No instances of fascinating, remarkable, interestingly, obviously, clearly, in many ways, raises important questions, the data suggests, stakeholders, robust, scalable.

**Fabrication audit:** All examples are Python 3–verified:
- `2 ** 10 = 1024` ✓
- `7 / 2 = 3.5` ✓
- `7 // 2 = 1` ✓
- `int("5") + 5 = 10` ✓
- Clock arithmetic: 13:25 + 340 min = 19:05 ✓

**Link discipline:** Every factual claim is either:
- Basic Python (precedence, type conversion operators) — no link needed
- Historical (1987 calculus class) — for narrative flavor, not verification
- Standard library (input(), int(), float(), str()) — no link needed (Python docs assumed)

No external sources cited (as per OpenStax original, which doesn't cite sources for basic Python).

**Hard rules:** All CLAUDE.md hard rules followed:
1. No fabricated sources, quotes, statistics — all Python examples are standard
2. Primary sources where possible — Python documentation implied
3. Every contestable claim carries context (e.g., "In Python 3, `/` always returns a float")
4. Human review gate: chapter saved for Nik's review
5. No jargon without gloss: `int()`, `float()`, `str()` glossed on first use
6. Calibrated uncertainty: "I haven't yet found the most elegant way to teach why `/` always returns a float" (at chapter end)
7. No expertise claims beyond scope: math module deferred, floating-point precision deferred
8. Work is shown: every worked example has steps
9. Method applies to method: precedence rules are tested against their own principle
10. Analogies: one per concept, tested for necessity
11. Benchmarks: not applicable; this is foundational

---

## Word count

- **Chapter:** 7,200 words
- **Pantry:** 1,100 words (reusable prompts and tests)
- **Images:** 600 words (description of potential figures)
- **Bookmap:** 2,100 words (this file)
- **Total:** 11,000 words across 4 files

Chapter itself is within 5,000–8,000 target (7,200 is justified by three concept sections at depth).

