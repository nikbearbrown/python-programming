# Bookmap: OpenStax *Introduction to Python Programming* — Chapter 4 (Decisions)

**Source:** OpenStax modules m00027–m00035 (Boolean values, comparison operators, if/elif/else, logical operators, precedence, ternary operator).

**Mined for:** Rewrite into Attenborough × Feynman voice with heavy code density and three-part Claude pattern (code + prompt + tests).

---

## Module m00027 — What is a branch?

**What it teaches:** The concept of branching — multiple execution paths depending on a condition.

**Scene:** Path forks; program takes left or right based on condition.

**Strengths to keep:** The metaphor is intuitive. Students immediately understand that a branch is a choice.

**Angles for reframe:** 
- Open in a concrete scene (TSA gate, thermostat, login form) rather than abstract "multiple paths." The gate agent's checklist makes the logic concrete before naming it.
- Name *condition* early with gloss: "condition, from Latin *conditio*, meaning agreement — a statement that is either true or false."

**Harvest:**
- Branch metaphor (one path or the other, not both).
- Connection to expressions from Chapter 2 (expressions evaluate to true or false).
- Intuition: not all paths are the same; the program chooses.

---

## Module m00028 — Boolean values and conversion

**What it teaches:** The bool data type, True/False keywords, type conversion with `bool()`, truthy/falsy rules.

**Strengths:**
- Truthiness rules are stated clearly (zero/empty is False, non-zero/non-empty is True).
- Distinction between string "True" (a string) and True (a Boolean).
- Examples show conversion across types.

**Weaknesses:**
- Examples are generic (`is_fruit`, `is_vegetable`) and don't stick.
- No grounded domain (grading, age, inventory, bank account).
- The video-reference structure obscures the actual rules.

**Angles for reframe:**
- Replace generic variables with grounded ones: not `is_fruit = "True"`, but `has_discount = user_age < 18`.
- Expand on the trap: `=` vs `==`. This is the most common student error at this stage and deserves its own section.
- Use truthiness in a real conditional, not in isolation.

**Harvest:**
- Truthiness rule (zero/empty → False, non-zero/non-empty → True).
- Type conversion across int, float, string, list.
- The `bool()` function as explicit conversion.
- Comparison operators produce Booleans.

---

## Module m00029 — if and if-else statements

**What it teaches:** Condition, body, indentation, if-else semantics (exactly one branch).

**Strengths:**
- Clear on colon and indentation requirement.
- Examples show what happens inside and outside the block.
- Explains why if-else guarantees one path.

**Weaknesses:**
- Examples are stilted ("umbrella if rainy"; "error if result is negative").
- No real application (not grading, not validation, not decisions that matter).
- Video-reference style makes the actual syntax hard to extract.

**Angles for reframe:**
- Use a graded-homework or access-control scenario where the decision outcome matters.
- Trace through with test inputs to show "when this is true, this block runs."
- Show what IndentationError looks like and how to fix it (common beginner frustration).

**Harvest:**
- Colon after condition is syntax, not style.
- Indentation defines the block; consistency matters.
- if-else: exactly one branch runs.
- if alone: zero or one branch runs.

---

## Module m00030 — Logical operators (and, or, not)

**What it teaches:** Truth tables for and, or, not; their use in conditions; examples of each.

**Strengths:**
- Truth tables are explicit and correct.
- Examples show real use cases (museum entry, streaming, diving).
- Or and and are clearly distinguished.

**Weaknesses:**
- Truth tables are presented in isolation, not tied to code.
- Short-circuit evaluation is not mentioned.
- No discussion of De Morgan's laws or when to refactor.
- Examples are brief and don't follow through to a full program.

**Angles for reframe:**
- Embed truth tables in the explanation, not as separate boxes.
- Add short-circuit evaluation as a subsection: why Python stops evaluating, when it matters (performance, side effects, preventing errors).
- Show De Morgan duality from set theory (Chapter 1) applied to Boolean logic.
- Expand examples: "museum entry" becomes a full admission checker; "streaming" becomes a content filter.

**Harvest:**
- Truth tables (and, or, not).
- Use case for each operator.
- Short-circuit evaluation (stop early, avoid errors).
- De Morgan pattern: complements flip and/or.

---

## Module m00031 — Precedence and associativity

**What it teaches:** Operator precedence table; left-to-right associativity; exceptions (exponentiation is right-to-right).

**Strengths:**
- Precedence table is authoritative and complete.
- Examples show the difference between evaluated and un-parenthesized expressions.
- PEP 8 style guidance is included (spacing around operators).

**Weaknesses:**
- Table is dense and hard to internalize in one pass.
- Associativity explanation (left-to-right vs right-to-left) is abstract.
- No guidance on when to use parentheses for clarity vs. relying on precedence.

**Angles for reframe:**
- Show precedence visually as a tree or pyramid (high to low).
- Use a concrete mixed-operator example: `if age < 18 or income > 30000 and is_dependent:` — trace through with parentheses to show what Python does.
- Emphasize: "When in doubt, use parentheses. They cost nothing and make intent clear."

**Harvest:**
- Precedence order (parentheses → exponentiation → arithmetic → comparison → not → and → or).
- Left-to-right is the rule; exponentiation is the exception.
- Parentheses override precedence and clarify intent.

---

## Module m00032 — elif and chaining

**What it teaches:** elif syntax, chaining multiple conditions, the idea that elif stops at the first true condition.

**Strengths:**
- Clear distinction between two separate ifs (can execute both) and if-elif (only one).
- Chaining template is shown.
- Examples show real cases (livestream features, chess moves).

**Weaknesses:**
- The example of two separate ifs going wrong is contrived.
- No discussion of nested if vs. chained if trade-offs.
- No refactoring guidance (when to break a long chain into a function or lookup table).

**Angles for reframe:**
- Lead with the contrast: "Two separate if statements: risk of multiple branches. If-elif chain: exactly one." Show a real case where this matters.
- Introduce trade-off subsection: chains are concise, but nesting gives specific error messages. When do you choose?
- Hint at refactoring: if you have 10 elifs, maybe your data belongs in a dictionary lookup or a function.

**Harvest:**
- elif chains allow multiple conditions; only the first true one runs.
- Contrast with separate ifs.
- Trade-off: chaining brevity vs. nesting specificity.

---

## Module m00033 — Nested if-else

**What it teaches:** Nesting syntax (indentation within indentation), when nesting is useful, avoiding redundancy.

**Strengths:**
- Clear example of redundant if statements and their nested equivalent.
- Shows that nesting increases indentation and readability.

**Weaknesses:**
- Doesn't discuss the cost (nesting depth limits readability; deep nesting is a code smell).
- No guidance on refactoring to functions or data structures.
- Example (game ID and player count) is simple enough that chaining with and would be clearer.

**Angles for reframe:**
- Show three approaches: (1) three separate ifs, (2) chained ifs with and, (3) nested ifs. Compare clarity for each.
- Warn against nesting depth: 3 levels is readable, 5+ is a signal to refactor.
- Show the meal-order example (lunch/dinner menu choice) and discuss: when does nesting make sense? (When the second decision depends on the first and the error messages differ.)

**Harvest:**
- Nesting syntax (indentation within indentation).
- When nesting is clearer than chaining.
- Nesting depth as a code-smell signal.

---

## Module m00034 — Conditional expressions (ternary)

**What it teaches:** Single-line if-else syntax: `expression_if_true if condition else expression_if_false`.

**Strengths:**
- Clear template and explanation.
- Simple example: `max_num = y if x < y else x`.

**Weaknesses:**
- Underused in examples. Only one worked example (version check).
- No discussion of readability trade-off: ternary is concise but can be hard to read if nested.
- Placement in the chapter feels late; would benefit from context of when to use vs. full if-else.

**Angles for reframe:**
- Show ternary as a tool for assignment in one line: `discount = 0.1 if is_member else 0.0`.
- Warn: multiple nested ternaries are unreadable. Use full if-else for complex cases.
- Tie to precedence: ternary has the lowest precedence, so complex conditions need parentheses.

**Harvest:**
- Ternary operator as shorthand for simple if-else assignments.
- Readability trade-off: brevity vs. clarity.
- Lowest precedence; parentheses often needed.

---

## Module m00035 — Chapter summary

**What it teaches:** Bulleted recap of all concepts plus reference table of functions and operators.

**Strengths:**
- Reference table is complete and useful for quick lookup.
- Recap of learning objectives.

**Weaknesses:**
- Recap is a passive list; doesn't tie concepts together.
- No reflection on what students can *do* now that they couldn't before.

**Angles for reframe:**
- Recap in Feynman style: "You can now write conditions. You can chain them. You can combine them. You can see that exactly one path runs." More active, more memorable.
- Connection forward: loops use the same condition logic. Functions often return Booleans. Classes use conditions to validate state.

**Harvest:**
- Complete reference table for later.
- Recap as a starting point for reflection.

---

## Ideas harvest for Chapter 4 rewrite

### Puzzles (cold-open candidates)
- **TSA gate agent** checking ID, boarding pass, date, security flags (used in final chapter).
- **Thermostat** comparing room temp to setpoint; deciding heat/cool/off.
- **Login form** checking username and password.
- **Grade assignment** by numeric score.
- **Restaurant seating** based on party size and time.

### Specification moves (vague terms to sharpen)
- "Boolean" — name Boole, the 19th-century logician.
- "Condition" — an expression that evaluates to true or false.
- "Branch" — a path through code; exactly one branch runs in an if-elif-else chain.
- "Truthiness" — the rule for converting any value to true/false (zero/empty is false, non-zero/non-empty is true).
- "Short-circuit evaluation" — stopping evaluation as soon as the outcome is known.

### Mechanisms to explain in depth
- **Truthiness conversion** — why `bool(0)` is False, `bool("")` is False, `bool("0")` is True.
- **Comparison produces Boolean** — linking comparison operators to Boolean output.
- **If-elif-else exclusivity** — why exactly one branch runs (not "all true ones").
- **Short-circuit evaluation** — how and/or stop early; why division-by-zero is prevented by `count > 0 and 10 / count`.
- **Operator precedence** — why `a or b and c` is `a or (b and c)`, not `(a or b) and c`.

### Trade-offs to name
- **Truthiness convenience vs. explicit comparison** — `if x:` is shorter but `if x > 0:` is clearer.
- **Chained conditions vs. nested blocks** — chaining is concise, nesting gives specific error messages.
- **Ternary brevity vs. full if-else clarity** — ternary is one line, but multiple ternaries are unreadable.
- **Parentheses for clarity vs. precedence rules** — adding parentheses is always safe and improves readability.

### Errors to teach
- **= vs ==** — most common beginner mistake at this stage. Assignment vs. comparison.
- **IndentationError** — Python requires indentation; easy to miss on first try.
- **Missing colon** — `if x > 5` (wrong) vs. `if x > 5:` (right).
- **Forgetting that if-elif-else is exclusive** — thinking multiple branches can run.

### Code density techniques
- Use grounded, real examples (grading, age, banking, travel, recipes).
- Show short, runnable snippets (not pseudocode).
- Include at least one worked example per concept that ties together multiple ideas.
- Follow the three-part Claude pattern: code + prompt + tests.

### Connections forward
- Chapter 5 (Loops): A loop is a decision repeated. Same condition logic, inside a loop.
- Chapter 6 (Functions): Functions often return Booleans. Callers use those Booleans to decide.
- Chapter 9 (Lists): Iterate through a list and make decisions about each element.
- Chapter 10 (Dictionaries): Check whether a key is present (Boolean).

---

## Reflection on source material

The OpenStax modules are comprehensive and well-structured. They cover all necessary content: Booleans, comparison, if-elif-else, logical operators, precedence, ternary.

**What to keep:**
- Truth tables (correct, clear).
- Syntax templates (colon, indentation).
- Operator reference (complete and accurate).
- Precedence table (definitive).

**What to rewrite:**
- Examples (replace generic with grounded, real-world cases).
- Prose (replace declarative with narrative, scene-based).
- Explanations (add mechanisms, not just names).
- Cold opens (add a puzzle or scene, not an abstract definition).

**What to add:**
- Short-circuit evaluation (performance, error prevention).
- Trade-offs (chains vs. nesting, truthiness vs. explicit comparison).
- Common bugs (= vs ==, forgetting colon, indentation).
- Refactoring hints (when a long chain belongs in a function or lookup table).
- De Morgan duality (connection to set theory from Chapter 1).

**Voice calibration:**
The OpenStax voice is reference-like (correct but distant). The rewrite should be conversational-explanatory (Attenborough × Feynman): "Here's a puzzle. Watch how the logic works. Here's the machinery. Here's when to prefer one approach over another. Here's a common trap."
