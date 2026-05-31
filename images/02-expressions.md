# Images — Chapter 2: Expressions

Reference guide for figures that could be illustrated in Chapter 2. No actual images are embedded (as per the mandate: `[FIGURE: ...]` notation in chapter); this file documents what visual aids would clarify the content.

---

## Visual aid candidates

### Figure 1: Operator precedence as a hierarchy

**Location:** Concept 1, after the precedence table.

**Purpose:** Show the four-level precedence stack visually. 

**Description:** A pyramid or stacked boxes showing:
- Level 1 (top, smallest): Parentheses `()`
- Level 2: Exponentiation `**`
- Level 3: Multiplication, division, modulo `*`, `/`, `//`, `%`
- Level 4 (bottom, largest): Addition, subtraction `+`, `-`

Operators higher in the stack "eat first" (evaluate first). This image anchors the abstract concept.

**Why visual helps:** Students often think left-to-right is the rule. A visual hierarchy makes it clear that precedence overrides reading order.

---

### Figure 2: Type conversion flow chart

**Location:** Concept 2, after "Explicit type conversion" section.

**Purpose:** Show what types can be converted to what, and what information is preserved or lost.

**Description:** Three starting nodes (int, float, str) with arrows showing conversions:
- int → float: always safe, adds `.0`
- float → int: truncates fractional part (with warning label)
- int → str: lossless, preserves value as text
- str → int: fails if string isn't a pure integer
- str → float: fails if string isn't a pure number
- float → str: converts decimal representation exactly

Color-code losses: red for truncation (float → int), green for lossless, yellow for possible errors.

**Why visual helps:** Students conflate "converting" with "rounding" or "preserving precision." A flow chart shows which conversions are safe and which lose information.

---

### Figure 3: The division pair — quotient and remainder

**Location:** Concept 3, after "Integer division and the division pair" section.

**Purpose:** Show how `//` and `%` split a number.

**Description:** A box diagram:
- Top: a number, say 193
- Arrow splits into two paths:
  - Left path: `193 // 100 = 1` (quotient)
  - Right path: `193 % 100 = 93` (remainder)
- Bottom: `1 × 100 + 93 = 193` (reconstruction)

Use color to link the quotient and remainder back to the original.

**Why visual helps:** Students see the two operators as independent. Showing them as two halves of one split makes their relationship clear.

---

### Figure 4: Python shell (REPL) screenshot annotated

**Location:** Opening section, "The REPL and a puzzle."

**Purpose:** Show what a real Python shell looks like with labels.

**Description:** A terminal screenshot showing:
```
>>> 2 ** 10
1024
>>> 2.0 ** 10
1024.0
>>> |
```

Labels:
- `>>>` prompt — where you type
- `2 ** 10` — expression you entered
- `1024` — result the interpreter printed
- `>>>` again — ready for next input (the "loop" back)

**Why visual helps:** Students may not have seen a Python shell before. Labeling the cycle (read → eval → print → loop) makes the acronym concrete.

---

### Figure 5: Type error breakdown

**Location:** Concept 2, "Cold open: a string that looked like a number."

**Purpose:** Visualize what happens when types collide.

**Description:** Two boxes side by side:
- Left: `65` (labeled: integer)
- Right: `"25"` (labeled: string)
- Arrow pointing to both: subtraction operator `-`
- Below: Red X with error message "TypeError: unsupported operand type(s) for -: 'int' and 'str'"
- Arrow down to: `age = int(input("..."))` with `"25"` becoming `25` (string → integer)
- Now: `65 - 25 = 40` (both integers, subtraction works)

**Why visual helps:** The error message is dense. A before/after visual shows what went wrong and how explicit conversion fixes it.

---

## Notes on illustration style

All figures should follow the Attenborough × Feynman aesthetic: **specificity over abstraction**. Use real examples (193 cm, 7 ÷ 4, the actual Python shell), not generic boxes labeled "type A" and "type B." Concrete numbers and actual code make the concept stick.

No figures should be decorative. Every figure answers a structural question the prose raises: How do operators stack? What information survives a conversion? How do quotient and remainder relate? These are the questions students have.

---

## Deferred visual aids

The following topics could use illustrations but are not critical for Chapter 2:

- **Floating-point representation in binary** — visual showing the conversion from decimal 0.1 to binary approximation. Deferred: belongs in Chapter 3 (Objects and precision).
- **Math module function taxonomy** — a table or diagram of which functions do what. Deferred: math module itself is Chapter 7.
- **Operator precedence tree for complex expression** — parsing tree for `3 + 4 * 2 ** 3 / 2 - 5`. Useful but overkill; prose explanation suffices.

