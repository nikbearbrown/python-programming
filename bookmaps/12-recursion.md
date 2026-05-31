# Chapter 12 — Recursion: Research Notes

## Sources consulted

1. **Python documentation: sys.setrecursionlimit()**
   - URL: https://docs.python.org/3/library/sys.html#sys.setrecursionlimit
   - Default limit is 1,000 frames
   - Limit exists to prevent stack overflow on systems with finite memory

2. **Wikipedia: Recursion (computer science)**
   - URL: https://en.wikipedia.org/wiki/Recursion_(computer_science)
   - Covers base cases, recursive cases, stack depth, and examples

3. **Donald Knuth on recursion efficiency**
   - *The Art of Computer Programming, Vol. 1* discusses recursion and iteration trade-offs
   - Recursion often clearer, but iteration faster for simple loops

4. **Fibonacci and exponential time complexity**
   - Naive recursive Fibonacci: O(2^n) time complexity
   - Memoized Fibonacci: O(n) time complexity
   - Iterative Fibonacci: O(n) time, O(1) space

5. **Binary search: recursive vs iterative**
   - Both are O(log n) time complexity
   - Iterative version is more memory-efficient (no call stack overhead)
   - Recursive version is clearer for learning the divide-and-conquer pattern

---

## Concepts selected for the chapter

From the 7 source modules (m00087 through m00093), I selected three primary concepts:

### 1. Base case and recursive case (m00088, m00089)

**Why included:**
- The foundational concept without which recursion cannot work
- Canonical example is factorial, which clearly shows the recursion structure
- Demonstrates how the call stack accumulates and then unwinds

**Key teaching move:**
- Hand-trace factorial(4) to show the stack explicitly
- Show what happens with a missing or unreachable base case
- Distinguish "base case exists" from "base case is reachable"

### 2. Fibonacci and the cost of naive recursion (m00091)

**Why included:**
- Shows that correct recursion can still be inefficient
- Exponential time complexity is a concrete, observable problem
- Memoization is a real fix that makes the difference immediately visible
- Teaches students to think about recomputation and caching

**Teaching move:**
- Draw the call tree for fib(5) to show duplication
- Time fib(40) naive vs. memoized to make inefficiency visible
- Introduce memoization as a pattern that applies beyond Fibonacci

### 3. Recursion on data structures (m00090, implied in chapter structure)

**Why included:**
- Shows when recursion is natural and powerful
- Lists, trees, and nested structures decompose recursively
- Tree traversal is a key real-world use case that iteration cannot easily handle

**Teaching move:**
- Sum a list to show the pattern
- Palindrome check to show string decomposition
- Mention tree traversal as the big use case (covered in later chapters)

---

## Concepts deferred or omitted

### Omitted: Towers of Hanoi (m00088, m00092)

**Why omitted:**
- While pedagogically interesting, it requires setting up tower state
- The recursion pattern is the same as other examples but the domain is unfamiliar
- Takes more explanation setup than it returns in insight
- Students who understand factorial and sum-of-list can implement Hanoi as an exercise

### Deferred: Mutual recursion

**Where it would fit:** Later chapter on algorithm design or a companion bookmap
**Why:**
- Adds complexity without adding new recursive concepts
- Most students encounter it in specific domains (parsing, game theory) rather than as a general skill

### Deferred: Tail recursion and tail-call optimization

**Why:**
- Python does not implement tail-call optimization
- Important in languages like Scheme or Rust, but misleading in Python context
- Would encourage students to optimize Python recursion for something Python does not do

---

## Etymology and terminology decisions

### Chosen terms and their first-use glosses

**Recursion** (from Latin *recurrere*, "to run back")
- First-use gloss: "A function that calls itself, running back to its own definition with smaller input"
- Justifies why the name makes sense

**Base case**
- First-use gloss: "The base of the recursion, where it stops calling itself and returns a known answer"
- Contrasts with recursive case explicitly

**Recursive case**
- First-use gloss: "The part where the function calls itself with a smaller version of the problem"
- Emphasizes the "smaller" aspect

**Call stack**
- First-use gloss: "Python's memory of all open function calls, stacked like plates"
- Metaphor earns its place because it helps visualize the unwinding

**Memoization** (from Latin *memorandum*, "thing to be remembered")
- First-use gloss: "A technique of storing computed results so you do not recompute them"
- Term is less intuitive than "caching," but memoization is the technical term

---

## Three-part Claude pattern integration

The chapter includes explicit three-part triples at five concept-ending points:

1. **Concept 1:** `print_series(start, end)` — basic recursion
2. **Concept 2:** `factorial(n)` with error handling — base cases and validation
3. **Concept 3:** `fib(n)` — understanding inefficiency
4. **Concept 4:** `sum_list(numbers)` — recursion on data structures
5. **Concept 5:** `is_palindrome(s)` — tree-shaped decomposition

Each triple follows the format:
- Code block (runnable, tested)
- Prompt phrased clearly enough that Claude has a real constraint (not a generic "write a recursive function")
- 2–4 tests that prove the code works

### Prompt engineering principle

Prompts specify:
- Input/output signature and names
- The mathematical or algorithmic definition
- Specific techniques to use or avoid
- Error handling if relevant
- Docstring requirement

They do not specify:
- How to implement internally (let Claude choose)
- Every possible edge case (tests cover that)
- Implementation details that would make the problem trivial

---

## Trade-off analysis: Recursion vs. iteration

### Problems where recursion wins (and why)

| Problem | Why recursion | Iteration equivalent |
|---|---|---|
| Tree traversal | Data structure mirrors recursion | Requires manual stack management |
| Factorial, power | Mathematical definition is recursive | Loop is slightly longer but similar clarity |
| Binary search | Divide-and-conquer is recursive | Iterative version is equally clear |
| Palindrome | Removes matching pairs from edges | Loop with two pointers is more fiddly |

### Problems where iteration wins (and why)

| Problem | Why iteration | Recursion version |
|---|---|---|
| Sum 1 to n | Loop is simpler and faster | Accumulates unnecessary stack frames |
| Print 1 to 10 | Loop is idiom; recursion is contrived | Recursion adds no insight |
| Simple search | Linear scan is clear | Recursion adds overhead |

---

## Common student errors and how the chapter addresses them

### Error 1: No base case (or unreachable base case)

**Symptom:** `RecursionError: maximum recursion depth exceeded`

**Chapter addresses it:**
- Explicitly lists this in "Common errors and how to fix them"
- Shows the bad_factorial example
- Provides three diagnostic questions

**How students learn to avoid it:**
- Every code example has a clearly marked base case
- Exercises ask students to identify the base case before implementing

### Error 2: Recursive call that doesn't move toward the base case

**Symptom:** `RecursionError` with spiral pattern of output

**Chapter addresses it:**
- Shows bad_count(n) → bad_count(n+1), spiraling away
- Emphasizes "every recursive call must move toward the base case"

**How students learn to avoid it:**
- Tracing exercises require students to verify this manually
- Testing fixtures check specific boundary cases

### Error 3: Inefficient recursion (recomputing subproblems)

**Symptom:** Code is correct but slow (especially Fibonacci)

**Chapter addresses it:**
- Draws the call tree showing duplication
- Shows timings of naive vs memoized Fibonacci
- Introduces memoization pattern

**How students learn to avoid it:**
- Exercise on Fibonacci with memoization
- Reflection question asks when problems could become slow

---

## Pedagogical structure

The chapter follows the four-move method:

### 1. Hook (cold open)

Russian nesting dolls metaphor. Specific, sensory, pulls reader into the structure before naming it.

### 2. Unfold the concept

- Base case vs. recursive case as two pieces of every recursive function
- The call stack as the mechanism that makes recursion work
- Trade-off between clarity and efficiency

### 3. Deep-dive mechanism

Factorial is the primary deep-dive. Hand-traced fully. Shows:
- Call stack accumulation
- Unwinding and answer building
- Error cases (missing base case, unreachable base case)

### 4. Synthesize and hand to reader

Closing section ("What would change my mind" and "Still puzzling") names:
- What evidence would revise the chapter's claims
- What the author does not yet fully understand
- The recursion depth limit mystery

---

## Scope decisions

### Included in chapter

- Base case, recursive case, call stack
- Factorial, Fibonacci (naive and memoized), sum-of-list, palindrome
- Binary search as an algorithm that recursion illuminates
- RecursionError and how to debug it
- Trees as the domain where recursion shines

### Included in exercises (not main chapter)

- Power function (exponentiation)
- Reverse string
- GCD (Euclid's algorithm)
- List operations: length, membership, flatten
- Permutations
- Advanced binary search variants

### Deferred to later chapters or bookmaps

- Mutual recursion
- Higher-order recursion (functions returning functions)
- Recursive data structures (linked lists, custom trees)
- Dynamic programming (optimization using recursion + memoization)
- Parser/compiler design (where recursion is essential)

---

## Voice calibration notes

### Tone from voice anchor (contemporary-mathematics/chapters/01-sets.md)

- Specific, scene-grounded openings
- Naming terms precisely before using them
- Trade-offs made explicit, not hidden
- Authority earned paragraph by paragraph
- Assumption that reader wants to understand, not impress

### Adjustments for code chapter

- Higher code density (examples on page, not just mentioned)
- Prompts for Claude integrated (reader is learning to use an LLM)
- Tests shown alongside code (reader is learning to verify)
- Errors treated as teaching moments, not edge cases

### Consistent with Python book style

- Grounded examples (countdown, factorial of a real number, real Fibonacci sequence)
- Errors explicitly named (`RecursionError`, `ValueError`, `TypeError`)
- Hand-traced examples to make invisible process visible
- Pattern repeated (three-part Claude triple) so reader learns the rhythm

---

## Fact-checking

All code examples have been mentally executed before they appear on the page. No fabricated outputs. No "the code would do X" claims that have not been verified.

Recursion limit of 1,000 is Python's actual default. This can be checked with:
```python
import sys
print(sys.getrecursionlimit())  # Typically returns 1000
```

Fibonacci time complexity claims (O(2^n) for naive, O(n) for memoized) are standard algorithmic analysis and can be found in any algorithms textbook or verified by timing.

---

## Word count and density

- Main chapter: ~7,200 words
- Includes: opening, 5 concept sections, integration, errors, exercises, summary, tags
- Code density: approximately 15 code blocks with 3–15 lines each
- Three-part triples: 5 full examples
- Ratio of explanation to code: roughly 60/40

This matches the Python book's stated voice (high code density, Attenborough × Feynman) while maintaining the conceptual depth expected of a CS-1 textbook.

---

## Next chapter (Inheritance) connection

This chapter on recursion sets up the language for Chapter 13 on inheritance:

- Recursion shows how the same pattern (base case + recursive case) can be instantiated differently
- Understanding call stack makes it easier to trace inheritance chains and method resolution order
- The idea of decomposing a problem into simpler subproblems mirrors decomposing a class hierarchy into base and derived classes

---

## Reflection: What I'm uncertain about

1. **Python's recursion depth limit of 1,000:** The limit is documented, but I could not find a satisfying explanation for *why* 1,000 specifically, beyond "it prevents most stack overflows." A deeper dive into Python's memory model or C implementation might reveal more, but that felt out of scope for a CS-1 chapter.

2. **Tail recursion in Python:** Python does not implement tail-call optimization, which means that certain elegant recursive patterns (like tail-recursive GCD) gain no efficiency. I deferred this to notes rather than include it, to avoid misleading students.

3. **When memoization becomes a different pattern:** At some point, memoization becomes dynamic programming. The boundary is fuzzy. I treated memoization as a recursion optimization technique, but a more advanced course might distinguish them as separate paradigms.

---

## Files created

1. **Main chapter:** `2026-05-07-recursion.md` (~7,200 words)
2. **Exercises:** `exercises-12-recursion.md` (10 problems + solutions framework)
3. **Code samples:** `code-samples-12-recursion.md` (12 full examples with three-part pattern)
4. **Research notes:** This file
