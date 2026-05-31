# Figures for Chapter 4 — Decisions

This file documents figures referenced in the chapter. In the final book, these will be replaced with rendered diagrams or screenshots.

## Figure 1: Truth table for and operator

**Location:** Concept 3, "The and operator"

**Description:** A 3×3 table showing all combinations of True/False for p, q, and p and q.

```
p     | q     | p and q
------+-------+--------
True  | True  | True
True  | False | False
False | True  | False
False | False | False
```

**Purpose:** Visual reference for the conjunction operator.

---

## Figure 2: Truth table for or operator

**Location:** Concept 3, "The or operator"

**Description:** A 3×3 table showing all combinations of True/False for p, q, and p or q.

```
p     | q     | p or q
------+-------+--------
True  | True  | True
True  | False | True
False | True  | True
False | False | False
```

**Purpose:** Visual reference for the disjunction operator.

---

## Figure 3: Truth table for not operator

**Location:** Concept 3, "The not operator"

**Description:** A 2×2 table showing p and not p.

```
p     | not p
------+-------
True  | False
False | True
```

**Purpose:** Visual reference for negation.

---

## Figure 4: if/elif/else flow diagram

**Location:** Concept 2, "The if-elif-else chain"

**Description:** A flowchart showing decision flow:
- Start → Condition 1? 
  - Yes → Execute Block 1 → End
  - No → Condition 2?
    - Yes → Execute Block 2 → End
    - No → Condition 3?
      - Yes → Execute Block 3 → End
      - No → Execute else Block → End

**Purpose:** Illustrate that exactly one path is taken.

---

## Figure 5: Operator precedence tree

**Location:** Concept 3, "Operator precedence and parentheses"

**Description:** A diagram showing the order in which operators are evaluated:
- Top: Parentheses
- Next: Exponentiation
- Next: *, /, //, %
- Next: +, -
- Next: <, <=, >, >=, ==, !=
- Next: not
- Next: and
- Bottom: or

**Purpose:** Clarify why `a or b and c` is parsed as `a or (b and c)`, not `(a or b) and c`.

---

## Figure 6: Short-circuit evaluation timeline

**Location:** Concept 3, "Short-circuit evaluation"

**Description:** Two parallel timelines showing evaluation:

**Scenario A: `count > 0 and 10 / count > 2` when count = 0**
```
Evaluate: count > 0
Result: False
Stop evaluation. Do not evaluate 10 / count (no division-by-zero error).
Final result: False
```

**Scenario B: `count > 0 and 10 / count > 2` when count = 5**
```
Evaluate: count > 0
Result: True
Continue. Evaluate 10 / count > 2
Result: 2 > 2 is False
Final result: False
```

**Purpose:** Show that short-circuit evaluation prevents errors and improves performance.

---

## Figure 7: Nested vs. chained conditions

**Location:** Concept 2, "Trade-off: chains vs. nesting"

**Description:** Side-by-side code comparison:

**Left (chained with and):**
```
if username == "admin" and password == "secret":
    print("Access granted")
else:
    print("Access denied")
```

**Right (nested):**
```
if username == "admin":
    if password == "secret":
        print("Access granted")
    else:
        print("Wrong password")
else:
    print("Unknown user")
```

**Purpose:** Show when chaining is shorter vs. when nesting provides better feedback.

---

## Figure 8: Thermostat hysteresis diagram

**Location:** Integration, "The thermostat, refined"

**Description:** A graph with temperature on the x-axis and furnace state (Heat/Off/Cool) on the y-axis.

- Setpoint = 20°C
- Hysteresis = 0.5°C
- Below 19.5°C: Heat
- 19.5°C to 20.5°C: Off
- Above 20.5°C: Cool

Shows the dead band that prevents rapid cycling.

**Purpose:** Illustrate a practical application of decision logic with hysteresis.

---

## Figure 9: Grading scale reference

**Location:** Concept 1, "Worked example — grading scale"

**Description:** A table showing score ranges and corresponding letter grades:

```
Score Range | Grade
90-100      | A
80-89       | B
70-79       | C
60-69       | D
0-59        | F
```

**Purpose:** Reference for the grading example and Exercise 4.4.

---

## Figure 10: ATM decision tree

**Location:** Concept 2, "Worked example — ATM withdrawal"

**Description:** A flowchart showing the decision sequence:

```
Start → Is account valid (7 digits)? 
  No → Print "Invalid account" → End
  Yes → Is amount positive?
    No → Print "Amount must be positive" → End
    Yes → Is balance sufficient?
      No → Print "Insufficient funds" → End
      Yes → Is amount <= daily limit?
        No → Print "Daily limit exceeded" → End
        Yes → Withdraw, update balance → Print success → End
```

**Purpose:** Show how cascading conditions prevent invalid operations.

---

## Rendering notes

- All tables should use a consistent style (lines, font, alignment).
- Flowchart boxes should be rectangular for actions, diamond for decisions.
- Color coding (optional): True in green, False in red, decisions in yellow.
- Diagrams should be simple and high-contrast for accessibility.
