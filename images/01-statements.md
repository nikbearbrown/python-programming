# Images — Chapter 1: Statements

Placeholder descriptors for figures referenced in the chapter.

## [FIGURE: Example uses of `print()`]

A table showing four variations of the `print()` function and their output:

1. `print("Today is Monday.")` and `print("I like string beans.")` → two lines of output
2. `print("Today", "is", "Monday")` and `print("Today", "is", "Monday", sep="...")` → showing default space separation vs custom separator
3. `print("Today is Monday, ", end="")` and `print("I like string beans.")` → showing how `end=""` suppresses the newline
4. `print("Today", "is", "Monday", sep="? ", end="!!")` and `print("I like string beans.")` → showing both `sep` and `end` together

**What students should notice:** The `sep` parameter changes what goes *between* values. The `end` parameter changes what goes *after* all the values. Understanding both is crucial for controlling output exactly.

## [FIGURE: Operator precedence table]

A vertical stack showing precedence from highest to lowest:

1. Parentheses `()`
2. Exponentiation `**`
3. Multiplication and division `*` and `/`
4. Addition and subtraction `+` and `-`

Each level should show an example. For example:
- Parentheses: `(2 + 3) * 4 = 20`
- Exponentiation: `2 ** 3 = 8`
- Multiplication: `2 * 3 = 6`
- Addition: `2 + 3 = 5`

**What students should notice:** Operations higher in the table happen first. When operators are at the same level, they go left to right (except exponentiation, which goes right to left).

## [FIGURE: Variable assignment as rebinding]

A visual showing a sequence of boxes:

1. **First box labeled `x`** with the value `5` inside.
2. **Arrow labeled `x = 10`** pointing to the same box.
3. **Second state of the box** now containing `10`.
4. **Text note:** "The name stays the same. The value changes. That's rebinding."

**What students should notice:** The variable name doesn't get replaced; what the name points to does. This is different from the mathematical idea of a variable as an unknown quantity.

## [FIGURE: String concatenation with spacing]

A diagram showing three examples:

1. `"5" + "3"` → `"53"` (not `8`)
2. `"hello"` + `" "` + `"world"` → `"hello world"`
3. `"Good"` + `"day"` → `"Goodday"` (missing space — a common mistake)

**What students should notice:** When strings join, there's no automatic space. You have to include it explicitly. Also, `+` with strings is *not* addition — it's joining.

## [FIGURE: Error traceback anatomy]

A sample error message with arrows pointing to each part:

```
Traceback (most recent call last):
  File "example.py", line 1
    print("Hello"
          ^
SyntaxError: '(' was never closed
```

Arrows pointing to:
- **Traceback:** "This is Python's report of the problem"
- **File and line number:** "Where to look in your code"
- **Caret `^`:** "Where Python found the problem"
- **Error type (SyntaxError):** "The category of mistake"
- **Error message:** "What went wrong"

**What students should notice:** A traceback is a roadmap. It tells you where to look and what kind of problem to look for. Read all five pieces before panicking.

## [FIGURE: Input, process, output cycle]

A three-box diagram with arrows:

```
[User Input] → [Program Processing] → [Output to Screen]
```

Examples inside each box:

- Input: `"98.6"` (temperature in Fahrenheit)
- Processing: `(98.6 - 32) * 5/9` (conversion formula)
- Output: `"98.6 F = 37.0 C"` (result)

**What students should notice:** Every program follows this pattern. Input gets you data. Processing transforms it. Output shows the result.

