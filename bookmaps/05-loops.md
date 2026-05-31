# Bookmap — Chapter 5 (Loops)

Mining the OpenStax source modules for Chapter 5 (Loops) from *Introduction to Python Programming* — extracting reusable angles, structures, and gaps.

---

## Source material overview

Seven OpenStax modules:
1. **m00036** — Introduction, overview of loops in real-world contexts
2. **m00037** — While loop construct, Fibonacci example, counting, user input
3. **m00038** — For loop, range(), iteration over containers
4. **m00039** — Nested loops (while, for, mixed)
5. **m00040** — Break and continue statements
6. **m00041** — Loop-else construct
7. **m00042** — Chapter summary and reference table

Total source: ~3,400 words. Converted chapter: ~5,700 words.

---

## Module-by-module analysis

### m00036: Introduction

**What it teaches:** Loops are pervasive in real-world code. Phone idle detection, roster iteration. The concept is simple: *repeat code while a condition holds*.

**What worked:** Concrete examples (phone lock, roster). Terse definition.

**What was rewritten:** Expanded to cold open with parking meter and heartbeat — sensory, scene-based, memorable. The cold open did not exist in source; it was written to match the Attenborough voice anchor.

**What was omitted:** Vague motivational framing ("loops are important"). Replaced with honest stakes: scale-dependent consequences.

**Reusable idea:** *Open with a system in a steady state, repeating.* The parking meter cycles once per second; the heart cycles; the phone wakes to check. Repetition as rhythm, before introducing it as code structure.

---

### m00037: While loops

**What it teaches:** 
- While loop structure and control flow.
- Fibonacci sequence example (condition-driven loop).
- Counting up and down with step size.
- User input in a loop (sentinel value).
- Sum of odd numbers (accumulator).

**What worked:** 
- The Fibonacci example is concrete and non-trivial.
- The "counting with while loop" section is clear.
- The step-size concept (positive/negative) is well-motivated.

**What was rewritten:**
- Fibonacci example was removed (it's complex; a simpler count-to-zero example better introduces the structure).
- Replaced with bank balance example (more universally relatable, same structure).
- Added the infinite-loop trap explicitly, with cause and fix.
- Added off-by-one error as a named concept.

**What was omitted:**
- User-input sentinel loop (moves to later chapters where input validation is clearer).
- Sum-of-odd-numbers problem (folded into accumulator section with clearer examples).

**What was added:**
- Etymology of "iterate."
- Explicit initialization-condition-update template.
- Direct comparison: while vs. for, when to choose each.

**Reusable idea:** *Use a universally relatable problem (money, age, time) to anchor the loop structure.* Fibonacci is mathematically interesting but doesn't help students decide when to use while vs. for.

---

### m00038: For loops

**What it teaches:**
- For loop structure.
- Iteration over containers (strings, lists).
- range() function with one, two, three arguments.
- Comprehensive range() table.
- Comparison of for and while for the same task (multiples of 5).

**What worked:**
- The range() table is precise and complete.
- The side-by-side comparison (for vs. while for multiples of 5) is excellent.
- String and list iteration examples are simple and clear.

**What was rewritten:**
- The range() table was simplified (kept, but embedded in prose with examples rather than isolated).
- Added `enumerate()` as a named, common tool for "index + value."
- Expanded "when to choose for vs. while" into a full trade-off section.

**What was omitted:**
- "Counting spaces in a string" problem and other end-of-section exercises (moved to graduated exercises).
- "Even and odd sequences" problem (moved and generalized).

**What was added:**
- Etymology of "iterate."
- Explicit trade-off framing: flexibility vs. clarity.
- Code example showing both sides of the trade-off.

**Reusable idea:** *Use a table to anchor syntax, then show it doing real work in prose examples.* The range() table is reference material; the prose examples show when and why to use it.

---

### m00039: Nested loops

**What it teaches:**
- Nested while loops.
- Nested for loops.
- Mixed while-for nesting.
- Doctor's appointment scheduling example.
- Printing patterns (triangle of numbers, stars).

**What was rewritten:**
- The doctor's appointment example was kept but simplified.
- Pattern-printing examples were omitted (they are good exercises but not foundational concepts).

**What was added:**
- Minimal mention in the chapter (nested loops are a chapter-level concept, not primary to the core three concepts).
- Full worked example in pantry section.

**Decision:** Nested loops are important but secondary. The chapter focuses on single loops first. Nesting is mentioned, with examples available for students who want to go deeper.

**Reusable idea:** *Nested loops are a natural extension of the loop concept, not a new concept.* The mental model is: "outer loop runs N times; inner loop runs M times per outer iteration; total = N × M." This can be taught implicitly through exercises.

---

### m00040: Break and continue

**What it teaches:**
- Break statement: exit the loop immediately.
- Continue statement: skip to the next iteration.
- Infinite loops (necessary partner to break).
- Use cases for both.

**What worked:**
- The infinite-loop example clearly shows why break is needed.
- The distinction between break and continue is stark.

**What was rewritten:**
- The infinite loop example was kept but moved earlier in Concept 3 (where accumulator patterns live, alongside loop control).
- Added visual contrast (break → exit; continue → next iteration).

**What was omitted:**
- Some end-of-section exercises (moved to chapter exercises).

**What was added:**
- Explicit flowchart/mental model for each.
- When to use each (break for search/found; continue for filter/skip).

**Reusable idea:** *Teach break and continue together, as alternatives to complex boolean conditions.* A loop that says `if condition: break` is often clearer than a loop that guards its body with `while not condition:`.

---

### m00041: Loop-else

**What it teaches:**
- Loop-else statement: runs after loop if loop completes normally (not interrupted by break).
- Use case: detecting whether a search was found.

**What was omitted entirely from the chapter:**
- Loop-else is a Python-specific feature (not in most languages).
- It is syntactically valid but pedagogically optional.
- The same behavior can be achieved with a boolean flag (shown in the source as an alternative).
- The chapter already covers break. Else adds one more concept. Given scope and audience (CS-1, not Python specialists), it is better to defer until Chapter 6 (functions) or a later chapter.

**What was kept for reference:**
- The pantry section notes that loop-else exists.
- If Nik decides it is important, it can be added back with one full section; the infrastructure is there.

---

### m00042: Chapter summary and reference table

**What it teaches:**
- High-level summary of all loop concepts.
- Reference table of loop syntax and patterns.

**What worked:**
- The reference table is comprehensive and precise.

**What was rewritten:**
- The summary was regenerated from the rewritten chapter, condensing the three core concepts.
- The reference table was adapted (included in Chapter Summary section, styled as part of prose rather than isolated).

---

## Ideas harvest — angles and structures for reuse

### Pedagogical angles

1. **Repetition as rhythm:** The parking meter, the heartbeat, the phone waking. Loops are not abstract; they are how systems maintain steady state. Open with this.

2. **Condition-driven vs. sequence-driven:** The core distinction between while and for. Use this to help students choose. Organize the chapter around it.

3. **The accumulator as the loop's purpose:** Most loops build something (a sum, a count, a list). Put the accumulator pattern front-and-center, not as a special case. It is the ordinary case.

4. **The infinite loop as a teacher:** Students will write infinite loops. Use that as a teaching opportunity. Show what causes them and how to recognize them. Don't hide the danger; make it visible and teachable.

5. **Scale matters:** One iteration is fast; a million is visible. Infinite loops at scale crash hard. Talk about scale explicitly.

### Structural moves

1. **Cold open with a system in steady state:** Not an abstract definition, a scene.

2. **Specification before complications:** Teach the simple while loop, then the simple for loop, then the combinations. Build up.

3. **Side-by-side trade-offs:** When contrasting while and for, show the same task done both ways. Let the code speak.

4. **The three-part Claude pattern:** Every substantive code example has a prompt and tests. This is non-negotiable for a coding textbook.

5. **Graduated exercises:** Warm-up (trivial), application (grounded problem), synthesis (combine multiple concepts). The chapter's exercises follow this shape.

### Gaps and opportunities

1. **Loop efficiency:** The source does not discuss the performance implications of inefficient loops or accumulators. At scale, this matters. The chapter now addresses it.

2. **Loop-else:** A Python-specific feature. Omitted in the conversion to keep scope tight. Can be added back if scope expands.

3. **Multiple accumulators:** Sometimes you need to track multiple values (sum AND count, to compute average). The source touches this in the exercises but not in the prose. The chapter now shows this pattern explicitly.

4. **Testing loops:** How to verify a loop is correct without running it at full scale. The three-part pattern (code, prompt, tests) addresses this. Tests are the tool that catches off-by-one errors.

---

## What stayed true to the source

- The core concepts: while loop, for loop, range(), break, continue.
- The concrete examples: Fibonacci (adapted), doctor's appointments, Fizzbuzz candidate, prime sieve.
- The trade-offs: when to use while vs. for, why break and continue exist.
- The mistakes: infinite loops, off-by-one errors, uninitialized accumulators.

---

## What was added (not in source)

- Cold open (parking meter, heartbeat, phone).
- Etymology of key terms (loop, iterate, accumulator).
- Explicit initialization-condition-update template.
- Accumulator pattern as primary concept (not ancillary).
- Scale discussion (one to a million iterations).
- Three-part Claude pattern for every code example.
- Explicit trade-off framing (while vs. for).
- Graduated exercises (warm-up, application, synthesis).
- Integration section (how to think about a loop problem).

---

## Source-to-chapter mapping

| Source concept | Chapter location | Changes |
|---|---|---|
| Loop overview | Cold open + section 0.3 | Added scene; added stakes |
| While loop structure | Concept 1 | Simplified example (bank → Fibonacci); added template; added infinite-loop trap |
| Counting in while loop | Concept 1 | Kept; added step-size discussion |
| For loop structure | Concept 2 | Kept; added enumerate(); expanded trade-off |
| Range function | Concept 2 | Kept; simplified table; kept in prose |
| Nested loops | Mentioned in integration | Deferred; full examples in exercises |
| Break statement | Concept 3 | Kept; added flowchart mental model |
| Continue statement | Concept 3 | Kept; added flowchart mental model |
| Infinite loop | Concept 1 | Moved earlier; expanded treatment |
| Loop-else | Omitted | Python-specific; deferred to later course |
| Chapter summary | Chapter summary | Regenerated from rewritten chapter |
| Reference table | Chapter summary | Adapted and integrated |

---

## Recommendations for next iteration

1. **Add visual flowcharts:** Loop control flow (Figures 1, 4) would anchor understanding for visual learners.

2. **Expand accumulator section:** Add examples for average (sum + count), min/max tracking, and string building.

3. **Add section on nested loops:** If scope allows, expand Concept 2 to include a full nested-loop subsection with the doctor's appointment example.

4. **Loop-else revisit:** If the course is Python-heavy, add loop-else as a "Python bonus" section after break/continue.

5. **Interactive traces:** Tools that let students step through a loop iteration by iteration would catch off-by-one errors early.

6. **Performance benchmarking:** Show the time cost of an inefficient accumulator (e.g., string concatenation in a loop vs. list join) with real measurements.

---

## Conversion notes

- **Word count:** Source ~3,400 words → Chapter ~5,700 words. Expansion justified by: (a) added cold open and integration; (b) explicit trade-off framing; (c) three-part Claude pattern; (d) expanded error discussion.
  
- **Removed content:** Loop-else (deferred), some nested-loop examples (moved to exercises), some end-of-section exercises (consolidated into graduated exercises at chapter end).

- **Added content:** Etymology, cold open, explicit templates, scale discussion, integration section, error diagnosis, three-part Claude pattern infrastructure.

- **Voice anchor:** Matched to *Contemporary Mathematics* Chapter 1 (sets). Opening with a scene; naming concepts by their work, not their label; showing machinery; admitting uncertainty. The Attenborough × Feynman voice (v1.1) is active throughout.

---

*Bookmap created during Chapter 5 (Loops) conversion. Source material: OpenStax modules m00036–m00042.*
