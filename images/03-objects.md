# Images and Figures — Chapter 3 (Objects)

*Descriptions, captions, and guidance for figures that should accompany the chapter.*

---

## Figure 1: Object anatomy (id, type, value)

**Purpose:** Show the three properties of a Python object side by side.

**Visual concept:**
A box labeled `5` (value) with annotations pointing to:
- A label on top: `identity: 140734888030976`
- A label on the right: `type: <class 'int'>`
- The box interior: `value: 5`

Similarly for `"hello"` (string) and `3.14` (float).

**Caption:** Each Python object has three properties. The identity is unique (the memory address). The type determines what operations the object supports. The value is the data itself.

---

## Figure 2: Variable as a reference (memory diagram)

**Purpose:** Show the distinction between a variable and the object it points to.

**Visual concept:**
Left side: Three variable names (`x`, `y`, `z`) stacked vertically. Arrows from each variable pointing right to boxes representing objects:
- `x` → box labeled `5` (int)
- `y` → box labeled `"hello"` (str)
- `z` → box labeled `[1, 2, 3]` (list)

Each box is labeled with its type and identity.

**Caption:** A variable is a label that points to an object. The variable holds the object's identity (memory address), not the object itself. Multiple variables can point to the same object.

---

## Figure 3: Aliasing — two variables, one list

**Purpose:** Show what happens when two variables point to the same mutable object.

**Visual concept:**
Two variables (`workout_1` and `workout_2`) with arrows converging to the same list box `[3, 5, 7]`.

Then, in a second panel, show the list after modification: `[4, 5, 7]`. Both variables still point to the same box, which now contains the modified list.

**Caption:** When you assign `workout_2 = workout_1`, both variables point to the same list object. Modifying the list through either variable affects what the other variable sees. This is the aliasing trap.

---

## Figure 4: Creating a copy with `.copy()`

**Purpose:** Contrast the aliasing case with the safe case using `.copy()`.

**Visual concept:**
Left panel (before `.copy()`): Both variables pointing to one list.
Right panel (after `.copy()`): Two separate list boxes with the same initial contents, each pointed to by a different variable. When one list is modified, the other remains unchanged.

**Caption:** Using `.copy()` creates a new list with the same elements. Now the two variables point to different objects. Changes to one list don't affect the other.

---

## Figure 5: Method calling vs. function calling

**Purpose:** Distinguish between `len(s)` (function) and `s.upper()` (method).

**Visual concept:**
Two side-by-side boxes:
- Left: A function icon or label with an arrow from it to an object, labeled `len("hello")`
- Right: An object with a method label inside or attached, `"hello".upper()`

Show that the function is separate from the object, while the method is part of the object.

**Caption:** A function is called independently and takes an object as input. A method is attached to an object and called using dot notation.

---

## Figure 6: Immutable vs. mutable

**Purpose:** Show what happens when you try to modify an immutable vs. a mutable object.

**Visual concept:**
Two columns:
- Left (string, immutable): `s = "hello"`. Try to assign `s[0] = "H"`. Show an error symbol and the message "TypeError: 'str' object does not support item assignment."
- Right (list, mutable): `lst = [1, 2, 3]`. Assign `lst[0] = 99`. Show the list changing to `[99, 2, 3]`.

**Caption:** Immutable objects like strings cannot be modified after creation. Mutable objects like lists can be changed in place.

---

## Figure 7: Integer caching — same value, same object (for small integers)

**Purpose:** Show why `id(5) == id(5)` but `id(257) != id(257)`.

**Visual concept:**
Two panels:
- Top panel: `5` and `5` both pointing to the same cached integer object.
- Bottom panel: Two `257` objects with different identities (not cached).

Show the identity numbers for each.

**Caption:** Python caches small integers (−5 to 256) for efficiency. Calling `id(5)` twice returns the same identity because it's the same cached object. Larger integers are created fresh each time.

---

## Figure 8: Equality vs. identity

**Purpose:** Illustrate `==` and `is` with concrete examples.

**Visual concept:**
A Venn diagram or comparison table:
- `==`: checks if two objects have the same value (equal). Example: `[1, 2] == [1, 2]` → True
- `is`: checks if two variables point to the same object (identical). Example: `a = [1, 2]; b = a; a is b` → True

Show two lists with the same contents but different identities to illustrate the difference.

**Caption:** `==` asks "do they have the same value?" `is` asks "are they the same object?" Use `==` for comparisons most of the time.

---

## Figure 9: Task tracker example — state progression

**Purpose:** Show how the object model applies to a real program.

**Visual concept:**
Three columns showing the state after each step:
1. Initial: `tasks = [...]`, `completed = []`
2. After removing first task and moving it: `tasks = [...]`, `completed = ["Write report"]`
3. Final progress: "1 of 3 tasks done"

Use memory diagrams to show which variables point to which objects.

**Caption:** A task tracker uses lists (mutable) and methods (`.append()`, `.remove()`) to track state. Each operation modifies the objects in place. The program counts elements with `len()` to show progress.

---

## Figure 10: Common error — AttributeError

**Purpose:** Show what an AttributeError looks like and what causes it.

**Visual concept:**
A code snippet:
```python
>>> x = 5
>>> x.upper()
```
Followed by the error traceback.

Below, show the fix: `str(x).upper()` → success.

**Caption:** If you call a method that doesn't exist on an object's type, you get an AttributeError. The fix is usually to convert to the right type first.

---

## Implementation notes

- Use consistent color coding: variables in one color, objects in another.
- Show identity numbers (memory addresses) consistently, even if they're synthetic examples.
- For all memory diagrams, use arrows pointing from variables to objects to reinforce the reference concept.
- Use boxes for objects, ovals or circles for variable names.
- In the aliasing figures, use the same box twice (or a visual indicator) to show both variables pointing to the same object.

