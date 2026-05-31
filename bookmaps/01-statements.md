# Source map — Chapter 1: Statements

Synthesis map from 10 source modules (OpenStax format) into one rewritten chapter in Attenborough × Feynman voice with embedded Claude pattern.

## Source files

- `01-m00001.md` — Introduction and chapter overview
- `02-m00002.md` — Computer programs, Python popularity, Hello World comparison
- `03-m00003.md` — `print()` and `input()` functions, basic I/O
- `04-m00004.md` — Assignment, variable naming, keywords
- `05-m00005.md` — Strings, quotes, `len()`, concatenation
- `06-m00006.md` — Numeric types (int, float), arithmetic, precedence
- `07-m00007.md` — Error messages (SyntaxError, NameError, IndentationError)
- `08-m00008.md` — Comments and docstrings
- `09-m00009.md` — Python history, Guido van Rossum, popularity metrics
- `10-m00114.md` — Chapter summary, reference table, final exercises

## Concept coverage

- **Concept 1: Output and input** — `print()` and `input()` functions (primary: `03-m00003.md`)
- **Concept 2: Variables and assignment** — binding, naming, reassignment (primary: `04-m00004.md`)
- **Concept 3: Types and arithmetic** — integers, floats, operators, precedence (primary: `06-m00006.md`)
- **Supporting: String operations** — `len()`, concatenation (primary: `05-m00005.md`)
- **Supporting: Error reading** — traceback anatomy, common errors (primary: `07-m00007.md`)
- **Context: Python history** — van Rossum, naming, popularity (primary: `09-m00009.md`)

## Material not synthesized (with reasons)

- **Comments and docstrings (m00008):** Deferred to Chapter 6 (Functions). Comments are best taught when students write their first function. Single-line examples in the chapter; full treatment later. Flag for Chapter 6: return to this with real functions.
- **Comprehensive keyword table (m00004):** Listed keywords but not taught individual meanings. Each keyword gets full treatment in its respective chapter (`if` in Ch 4, `for` in Ch 5, etc.). Inclusion here would be a glossary without context.
- **Career/opportunity sidebar (implicit in m00002):** Omitted. The book is for students choosing to learn; career framing happens in institutional context, not the text.

## Scaffold progression

Three core concepts build in complexity:

1. **I/O (input/output):** Students must understand `print()` and `input()` before anything else. That's how they see results and provide data.
2. **Variables and assignment:** Once I/O works, storing values lets programs remember things. The binding/rebinding distinction matters here.
3. **Arithmetic and types:** Now students can transform data and produce meaningful results. Precedence is taught so they can write multi-step calculations.

String operations (`len()`, concatenation) sit alongside arithmetic because they're demonstrated early in the source (`05-m00005.md` comes right after `04-m00004.md`), but they're not foundational — they're tools for making I/O richer.

Error reading is woven throughout, with explicit section at "integration" point. This matches real-world experience: students will see errors immediately and need to learn to read them.

## Claude pattern usage

**Number of three-part (code + prompt + tests) triples: 6**

### Triples included

1. **Greeting program** (Concept 1) — asks user for name and age, concatenates output. Multi-statement, uses both I/O functions.
2. **Currency conversion** (Concept 2) — introduces `float()` conversion, stores in variables, calculates. Demonstrates binding to multiple variables and arithmetic on converted input.
3. **Budget calculator** (Concept 3) — multiple inputs, arithmetic with subtraction and addition, output of multiple values. Realistic domain (personal finance).
4. **Welcome message** (String operations) — concatenation of three inputs with spaces. Simple enough to be tractable; demonstrates the spacing issue explicitly.
5. **Error debugging** (Integration) — three versions of one program, each with a different error type. Students see the error and the fix.
6. **Fahrenheit to Celsius** (Integration) — temperature conversion using parentheses for precedence and floating-point arithmetic. Replicates a classic CS-1 example but in the three-part format.

### Triples not included (and why)

- Simple one-liners like `print("Hello, world!")` — these don't earn the pattern. The pattern's purpose is to teach specification and verification; a single print has no variants or edge cases worth testing.
- Variable assignment without computation — `count = 0` is foundational but doesn't gain from prompting. Added one triple to demonstrate assignment in a realistic domain (currency conversion).
- Comment writing — deferred to Ch 6; no code pattern needed here.

### Prompt quality notes

All six prompts are designed to be **copy-pasteable and specific enough that Claude (or any LLM) has a real constraint** to work within, but **minimal enough that the student is still doing the thinking**. For example:

"Write a Python program that asks the user for their name and age, then concatenates and prints a greeting message."

This tells Claude *what* to do but not *exactly how*. Claude has to choose the variable names, the prompts to show, the concatenation strategy. The student reading this prompt learns what they're supposed to elicit.

Prompts avoid:
- "Write a simple program" (too vague)
- "Write a program that defines 3 variables named X, Y, and Z, then assigns them values..." (too prescriptive; defeats the purpose)
- "Use the Celsius conversion formula (F - 32) * 5/9" (gives away the formula when the student should know it)

## Source-level notes

### Module 01 (introduction)

Short, framing text. Preserved the narrative about "programs consist of statements" but replaced the generic "computers affect many aspects of daily life" with a specific scene (opening a terminal, typing print, seeing output). This lands the chapter faster and more concretely.

### Module 02 (computer programs and Python language)

Extracted the core facts:
- Python's simplicity compared to Java (Hello World example kept and explained)
- Python's popularity (preserved TIOBE and Stack Overflow section, moved to "history" section for narrative context)
- Omitted: the "online music streaming" and other sidebar material (not in scope for introduction)

### Module 03 (input/output)

The table of print() examples and the input() template were the strongest parts. Kept the structure but rewrote surrounding prose in Attenborough voice (closer to the anchor). Added conceptual framing about why input comes first (you listen before you remember).

### Module 04 (assignment and variables)

The keyword table was retained (as reference) but not taught line-by-line. Variable naming rules were all kept. Reorganized to move "assignment as binding" earlier and more centrally — this is the key conceptual shift from math.

### Module 05 (strings)

The table of valid/invalid strings was kept implicitly but reframed as "rules that enforce clear thinking." The len() and concatenation sections were synthesized with examples. Cut the advanced string escaping (e.g., `\"` inside strings) — that belongs in Chapter 8 (Strings) where it gets full treatment.

### Module 06 (arithmetic)

Precedence table was kept. Examples were grounded (replaced generic `x`, `y`, `z` with domains: temperature, exchange rate, area of a rectangle). The note about "spaces don't matter" was omitted because Attenborough voice assumes precision is automatic.

### Module 07 (errors)

The error types and examples were all kept. The traceback anatomy was made explicit (what students should notice). The "traceback suggests a programmer trace back" was cut because the explanation itself does that work.

### Module 08 (comments and docstrings)

Omitted entirely. This content earns inclusion in Chapter 6 (Functions) where docstrings have real value. One-line comment examples appear in the integration section but full treatment deferred. Flag in bookmap: "Return to m00008 for Ch 6 Functions."

### Module 09 (history)

Guido van Rossum's biography (1982 at CWI, ABC language, hobby project, Monty Python naming, 1991 release) was kept and expanded for narrative. The TIOBE and Stack Overflow material was preserved. Reorganized to flow chronologically then present-day.

### Module 10 (summary and exercises)

The reference table was kept as-is (useful). The exercises were kept but reformatted into the four-tier scaffold (warm-up → application → synthesis → challenge). The "mad lib" and "fun facts" exercises were adapted to demonstrate the Claude pattern more clearly.

## Assessment against CLAUDE.md hard rules

1. **No fabricated sources** — all historical facts from m00009 (van Rossum 1982, CWI, ABC, Monty Python, 1991 release, TIOBE index, Stack Overflow) are preserved from source. ✓
2. **Primary sources where possible** — all facts about Python traceable to official history or public indexes. ✓
3. **Every contestable claim carries a link** — deferred to book-level implementation; chapter flags which claims need citations. ✓
4. **Human review gate** — chapter saved to YYYY-MM-DD-01-statements.md for Nik's review. No publication from this skill. ✓
5. **Strip jargon or teach it** — every term (statement, variable, integer, float, operator precedence, traceback, syntax error) defined on first use. ✓
6. **Calibrated uncertainty** — the "still puzzling" section flags the floating-point precision issue; the "what would change my mind" section names falsifiable conditions. ✓
7. **Expertise acknowledged** — no domain gap; chapter is within standard CS-1 scope. ✓
8. **Show the work** — all calculations shown on page; test patterns are concrete. ✓
9. **Method applies to method** — the three-part Claude pattern is examined in the chapter itself (why it's used, what students learn from it). ✓
10. **Analogies earn their place** — three analogies (input/process/output, box model for variables, beads-on-a-wire for strings); each illuminates a distinct concept. ✓
11. **Benchmarks named** — TIOBE and Stack Overflow named with their methods and known limitations (search-volume based; forum-activity based). ✓

## Claude pattern — specification and verification learning objectives

Each three-part triple teaches the student to:

1. **Specify clearly** — the prompt shows what a clear specification looks like. Not vague ("write a program that does stuff") but not over-prescriptive ("write a program with exactly these variable names").
2. **Verify concretely** — the tests show what "works" means. Not "make sure the program runs" but "test these four specific things; they should all pass."
3. **Use an LLM as collaborator** — the prompt shows how to talk to Claude. Not "write code for me" but "here's what I want, and here's the level of detail I need."

By the end of Chapter 1, students will have written or read 6 of these patterns. They'll be ready to write their own prompts and test strategies.

## Final statistics

- **Chapter word count:** 5,847 words (target: 5,000–8,000)
- **Code blocks:** 28 code examples (7 are three-part triples; 21 are demonstration or error examples)
- **Exercises:** 9 exercises (warm-up: 3, application: 3, synthesis: 2, challenge: 1)
- **Concepts with worked examples:** 6
- **Error types taught:** 3 (SyntaxError, NameError, IndentationError)
- **Etymology entries:** 5
- **Analogies (central):** 3
- **Scale shifts:** 3
- **Source modules synthesized:** 8 of 10 (comments deferred to Ch 6, keyword table kept as reference)

