# Image Descriptions — Chapter 5 (Loops)

Descriptions of figures that would appear visually in the published chapter. Each description indicates placement and pedagogical purpose.

---

## Figure 1: While Loop Flowchart

**Placement:** After "The while loop: condition-controlled repetition" section heading.

**Description:**
A flowchart showing the while-loop control flow:
- Start node at top
- Diamond decision node labeled "condition?"
- If True (arrow labeled "yes"), flows down to a rounded rectangle labeled "Loop Body: Execute statements"
- From loop body, arrow curves back up to the condition node
- If False (arrow labeled "no"), flows down to End node
- Below flowchart: annotation labels for "initialization" (before entering diagram), "condition check" (at decision diamond), and "update/progress" (in loop body)

**Purpose:** Clarify the order of operations in a while loop. Show that the condition is checked before each execution.

---

## Figure 2: For Loop Over Sequence

**Placement:** After "Iterating with range()" section.

**Description:**
A side-by-side visualization:

Left side: "range(5) generates"
```
0  1  2  3  4
```
Arrows pointing down from each number.

Right side: "for i in range(5):" code box with loop body.
Below: execution table showing iteration 0 (i=0), iteration 1 (i=1), ..., iteration 4 (i=4).
Annotation: "Loop runs 5 times; i takes each value in sequence."

**Purpose:** Make concrete what `range()` generates and how many iterations the loop executes.

---

## Figure 3: Accumulator Pattern Animation

**Placement:** After "The accumulator pattern: building totals and counts" section.

**Description:**
A table showing step-by-step execution of the sum accumulator example:
```
numbers = [3, 7, 2, 9, 1]

Iteration | current num | total before | total after
---------+-------------+--------------+------------
    1    |      3      |       0      |      3
    2    |      7      |       3      |     10
    3    |      2      |      10      |     12
    4    |      9      |      12      |     21
    5    |      1      |      21      |     22
```
Annotation: "Each iteration, we add the current number to the running total."

**Purpose:** Show how accumulators evolve iteration by iteration. Demystify the pattern.

---

## Figure 4: Break vs. Continue

**Placement:** After "Loop control: break and continue" section.

**Description:**
Two parallel flowcharts, side by side:

Left (break):
- Loop body executes
- If condition met, box labeled "break → exit entire loop"
- Arrow points out and down to "next statement after loop"

Right (continue):
- Loop body executes
- If condition met, box labeled "continue → skip to next iteration"
- Arrow curves back to condition check

Annotation: "break: leave the loop entirely. continue: skip the rest of this iteration but keep looping."

**Purpose:** Contrast the two control statements visually. Show their different exit paths.

---

## Figure 5: While vs. For Trade-off

**Placement:** After "While vs. For: when to choose which" section.

**Description:**
A comparison table in the form of a balanced-scale visual:

Left side (While Loop):
- "Flexible"
- "Complex conditions"
- "Dynamic step size"
- "More lines of code"
- "(Must manage update yourself)"

vs.

Right side (For Loop):
- "Cleaner"
- "Sequence iteration"
- "Fixed or simple step"
- "Fewer lines"
- "(Python handles update)"

Visual: a simple balance scale with "complexity" on one side and "readability" on the other.

**Purpose:** Help students choose the right loop type. Show the trade-off clearly.

---

## Figure 6: Off-by-One Error Illustration

**Placement:** After "Common for-loop mistakes" section.

**Description:**
A number line from 0 to 10:
```
0   1   2   3   4   5   6   7   8   9   10
|---|---|---|---|---|---|---|---|---|---|
```

Annotation for `range(10)`:
- Bracket under 0–9: "range(10) includes these"
- 10 outside bracket: "does NOT include 10"

Text below: "Common mistake: Writing `range(10)` when you want 1 through 10. Correct: `range(1, 11)`."

**Purpose:** Visualize the off-by-one error in range. Anchor the exclusive upper bound in a concrete image.

---

## Figure 7: Nested Loop Execution Grid

**Placement:** After nesting example in Concept 3.

**Description:**
A grid showing nested loop execution:
```
Outer iteration | Inner values
      1         |  1 2 3 4
      2         |  1 2 3 4
      3         |  1 2 3 4
```

Code box above:
```python
for outer in range(1, 4):
    for inner in range(1, 5):
        print(outer, inner)
```

Annotation: "The inner loop runs completely for each outer iteration. Total: 3 × 4 = 12 executions."

**Purpose:** Show how nested loops interact. Make the execution count (outer × inner) visible.

---

## Figure 8: Infinite Loop Warning

**Placement:** After "The infinite loop" section.

**Description:**
A circular arrow diagram:
- Code snippet in center
- Circular arrow around it labeled "loops forever"
- Red "X" overlay
- Side-by-side: "The bug" (no counter update) vs. "The fix" (counter -= 1)

**Purpose:** Warn visually about infinite loops. Show the fix clearly.

---

## Figure 9: Scale Shift — One to a Million

**Placement:** After "Scale shift" section.

**Description:**
Three panels:

Panel 1: Single loop iteration icon (one checkbox marked, representing one row processed)

Panel 2: Ten loop iterations (ten checkboxes marked)

Panel 3: One million iterations represented as a massive stack or dense grid with notation "1,000,000 rows"

Caption below: "One iteration is fast. One million iterations is visible time. Infinite loops crash."

**Purpose:** Emphasize that loop efficiency matters at scale. Make the invisible loop count concrete.

---

## Figure 10: Loop Summary Table

**Placement:** At the end of Chapter Summary.

**Description:**
A reference table:
```
Loop Type | Use When | Example | Runs
----------|----------|---------|------
while     | Condition-driven | while balance < 2000 | Until condition is false
for       | Iterating sequence | for i in range(10) | Once per element
```

Additional rows for `break`, `continue`, `range()` variants, accumulator patterns.

**Purpose:** Provide a quick visual reference for loop constructs. Students can return to this table during problem-solving.

---

## Notes on Implementation

- Figures 1, 4, 8 can be created as SVG flowcharts or simple ASCII art diagrams in the markdown.
- Figures 2, 3, 5, 10 work well as tables or styled markdown.
- Figure 7 can be rendered as an animated GIF showing execution step by step, or as a static table.
- Figure 9 uses scale to communicate impact.
- All figures should use consistent color coding: green for "correct", red for "error/infinite", blue for "data flow".

All figures are pedagogically necessary — they do not decorate the text, they clarify it. The text can stand without them, but the text is stronger with them.
