# Chapter 12 — Recursion

## Suggested titles

1. Recursion: A Function That Knows Itself
2. Running Back to the Definition: How Recursion Works
3. Smaller Versions of the Same Problem

## TL;DR

A recursive function is one that calls itself with a smaller version of the problem until it reaches a known answer (the base case). Recursion shines when a problem's structure mirrors itself — like a tree branching into smaller trees, or a problem shrinking by one unit at a time. You must always have a base case to stop the recursion, or your program will chase its tail forever and crash.

---

## Cold open

You pull open a set of Russian nesting dolls. The biggest doll sits in your hand. You open it. Inside is a slightly smaller doll. You open *that* one. Inside is a smaller doll still. You keep opening dolls — each one revealing the same shape, slightly scaled down — until you reach the tiny solid doll at the center. You cannot open it. That is your stopping point.

Recursion is like this. A function opens itself, finds a smaller version of the same problem inside, and hands that smaller problem back to itself to solve. When the problem gets small enough — small enough that you already know the answer — the function stops opening and returns the answer. Then each layer of the doll closes back up, building the final answer on the way out.

This is not a metaphor. The shape of recursion is the shape of the dolls. And it is one of the most powerful patterns in programming for problems that have this nested, self-similar structure.

### Learning objectives

By the end of this chapter you will be able to:

- **Explain** what a base case is and why every recursive function needs one.
- **Trace** a recursive function call by hand, drawing the stack of open function calls.
- **Implement** recursive functions for factorial, Fibonacci, and list-sum problems.
- **Distinguish** problems where recursion shines (tree-shaped data, problems that break into identical smaller subproblems) from problems where iteration is clearer.
- **Debug** a recursive function that is not terminating and understand `RecursionError`.

### Prerequisites

Chapter 6 (Functions) and Chapter 9 (Lists). You understand how function calls work and how to reason about them. You can read and modify code that uses loops and conditionals.

### Why this chapter matters

Recursion is not required for every program. But there are entire categories of problems — parsing nested data structures, searching trees, implementing algorithms like binary search, processing folder hierarchies — where recursion is either the clearest tool or the only tool. Later, when you encounter databases, compilers, or machine learning pipelines, you will find recursion inside. Understanding how it works now means you can read and troubleshoot that code when you need to.

---

## Concept 1 — The shape of recursion: base case and recursive case

Here is the simplest recursive function in the world:

```python
def countdown(n):
    """Count down from n to 1 and print each number."""
    if n == 0:
        print("Blastoff!")
        return
    else:
        print(n)
        countdown(n - 1)
```

This function calls itself. That is, the function `countdown` appears in its own definition.

The function works in two pieces:

1. **The base case:** `if n == 0:` — this is the "tiny solid doll" that cannot be opened further. When `n` reaches 0, the function prints "Blastoff!" and returns. It does *not* call itself. The base case is what stops the recursion.

2. **The recursive case:** `else: print(n); countdown(n - 1)` — this is where the function calls itself with a smaller problem. It prints the current number, then calls `countdown(n - 1)`. The argument is smaller: `n - 1` instead of `n`. Every recursive call must move toward the base case, or the function will loop forever.

Run the function:

```python
countdown(3)
```

This prints:

```
3
2
1
Blastoff!
```

What is happening inside Python? Let me show you by hand-tracing the call stack. When you call `countdown(3)`, Python does this:

1. **Frame 1:** `countdown(3)` is called. `n = 3`, which is not 0, so print 3. Then call `countdown(2)`.
2. **Frame 2:** `countdown(2)` is called. `n = 2`, which is not 0, so print 2. Then call `countdown(1)`.
3. **Frame 3:** `countdown(1)` is called. `n = 1`, which is not 0, so print 1. Then call `countdown(0)`.
4. **Frame 4:** `countdown(0)` is called. `n = 0`, so print "Blastoff!" and return. Frame 4 closes.
5. Execution returns to Frame 3. `countdown(0)` has returned, so Frame 3 closes.
6. Execution returns to Frame 2. `countdown(1)` has returned, so Frame 2 closes.
7. Execution returns to Frame 1. `countdown(2)` has returned, so Frame 1 closes.

Four calls to `countdown` were open at the same time. Python keeps track of them in something called the **call stack**. Each open function call is a "frame" on the stack. Frames open as you go deeper into recursion and close as you come back out.

This is the crux of recursion: Python has to remember every open call, waiting for the inner calls to finish, before the outer calls can close. This is powerful — it lets the function do work on the way *down* (like printing numbers) and on the way *back up* (like building an answer). But it also has a cost: the deeper the recursion, the more memory is used. Python has a limit called `sys.setrecursionlimit()` — by default, you can go about 1,000 deep before Python kills your program with a `RecursionError` saying the recursion limit was exceeded. More on that later.

> **Prompt for Claude.** "Write a recursive function `print_series(start, end)` that prints all integers from `start` down to `end` inclusive. Include a docstring. Handle the base case when `start < end` by printing nothing. Use recursion to move from one number to the next."

> **Tests to validate.**
> - `print_series(5, 3)` prints 5, 4, 3 (one number per line)
> - `print_series(1, 1)` prints 1 (the single number)
> - `print_series(3, 5)` prints nothing (base case: start is less than end)

### Trade-off: Recursion vs. iteration

You could write `countdown` without recursion:

```python
def countdown_iterative(n):
    """Count down from n to 1 and print each number (using a loop)."""
    for i in range(n, 0, -1):
        print(i)
    print("Blastoff!")
```

This is shorter, uses less memory, and runs faster. For problems that naturally fit a loop, iteration wins. Recursion is clearer when the problem itself is recursive — when the answer depends on solving a smaller version of the same problem, and that structure is the key insight. If you force iteration onto a naturally recursive problem, the code becomes harder to read, not easier.

The trade-off matters. Here is the principle:

- **Use recursion** when the problem definition is recursive (factorial, tree traversal, binary search on sorted data).
- **Use iteration** when a loop naturally expresses the algorithm (printing 1 to 100, summing an array with a single pass).
- **Choose based on clarity first, performance second** — for most programs, the difference in speed is negligible. Code that is easy to understand is code that works.

One more thing: when you use recursion, ask yourself these three questions before running the code:

1. **Does a base case exist?** Can I point to a condition under which the function stops calling itself?
2. **Is the base case reachable?** Does the recursive call actually move toward it?
3. **Is the problem naturally recursive, or am I forcing it?** If I have to think hard to justify using recursion, iteration might be clearer.

These three questions catch most recursion bugs before they happen.

---

## Concept 2 — Factorial: the canonical example

The factorial of a positive integer `n`, written `n!`, is the product of all positive integers from `n` down to 1.

```
5! = 5 × 4 × 3 × 2 × 1 = 120
3! = 3 × 2 × 1 = 6
1! = 1
```

Here is the key insight: you can define factorial *recursively*. The factorial of `n` is `n` times the factorial of `n - 1`:

```
n! = n × (n - 1)!
```

So `5! = 5 × 4!`, and `4! = 4 × 3!`, and so on, until you reach `1! = 1`. That final known value is your base case.

In Python:

```python
def factorial(n):
    """Return the factorial of n: n! = n × (n-1) × ... × 1."""
    if n == 0 or n == 1:
        return 1  # Base case: 0! = 1 and 1! = 1
    else:
        return n * factorial(n - 1)  # Recursive case
```

Let me trace `factorial(4)` by hand:

```
factorial(4)
= 4 × factorial(3)
= 4 × (3 × factorial(2))
= 4 × (3 × (2 × factorial(1)))
= 4 × (3 × (2 × 1))    # factorial(1) returns 1
= 4 × (3 × 2)
= 4 × 6
= 24
```

The call stack looks like this:

```
Frame 1: factorial(4) — waiting for factorial(3)
  Frame 2: factorial(3) — waiting for factorial(2)
    Frame 3: factorial(2) — waiting for factorial(1)
      Frame 4: factorial(1) — base case: return 1
    Frame 3: receives 1, computes 2 × 1 = 2, returns 2
  Frame 2: receives 2, computes 3 × 2 = 6, returns 6
Frame 1: receives 6, computes 4 × 6 = 24, returns 24
```

Notice the structure: each frame is *waiting* for the inner frame to finish before it can compute its own answer. This is the magic and the cost of recursion.

Now, what happens if you call `factorial(5)` and trace all five frames? Let me show you the actual Python behavior by running the code:

```python
print(factorial(5))  # Output: 120
print(factorial(0))  # Output: 1 (base case)
print(factorial(1))  # Output: 1 (base case)
```

The function works. But what if someone calls `factorial(-1)`? The function will never reach the base case. It will call `factorial(-2)`, then `factorial(-3)`, spiraling forever until Python stops it:

```python
factorial(-1)
# RecursionError: maximum recursion depth exceeded
```

This is the cost of missing or unreachable base cases. The function must have a base case, the base case must be reachable, and every recursive call must move toward it.

> **Prompt for Claude.** "Write a recursive function `factorial(n)` that computes `n!` (n factorial). Handle the base cases at `n == 0` and `n == 1` both returning 1. For `n < 0`, raise a `ValueError` with the message 'Factorial undefined for negative numbers'. Include a docstring."

> **Tests to validate.**
> - `factorial(0) == 1`
> - `factorial(5) == 120`
> - `factorial(1) == 1`
> - `factorial(-3)` raises `ValueError`

### Common mistake: Infinite recursion

Here is a broken version:

```python
def bad_factorial(n):
    return n * bad_factorial(n - 1)
```

This has no base case. It will call itself forever. The error you will see:

```
RecursionError: maximum recursion depth exceeded in comparison
```

Python interrupted the recursion at around frame 1,000 and stopped. If the base case is unreachable — for instance, if you have a base case `if n < 0` but the function starts with a positive `n` and only decreases — the same thing happens.

Always ask: does the base case exist? Is it reachable? Will the recursive call move toward it?

---

## Concept 3 — Fibonacci and the cost of naive recursion

The Fibonacci sequence is a series where each number is the sum of the two before it:

```
0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, ...
```

Here is the recursive definition:

```
fib(0) = 0
fib(1) = 1
fib(n) = fib(n - 1) + fib(n - 2)  for n > 1
```

In Python:

```python
def fib(n):
    """Return the nth Fibonacci number."""
    if n == 0 or n == 1:
        return n  # Base case
    else:
        return fib(n - 1) + fib(n - 2)  # Recursive case
```

Let me compute `fib(5)`:

```python
print(fib(5))  # Output: 5
```

This works. But watch what happens inside. Let me draw the call tree for `fib(5)`:

```
                    fib(5)
                   /      \
              fib(4)        fib(3)
             /      \       /      \
        fib(3)    fib(2)  fib(2)   fib(1)
        /    \    /    \  /    \
    fib(2) fib(1) fib(1) fib(0) fib(1) fib(0)
    /    \
fib(1) fib(0)
```

Notice: `fib(3)` is computed twice. `fib(2)` is computed three times. `fib(1)` is computed five times. As `n` grows, the wasted computation explodes. Computing `fib(40)` naively takes seconds. Computing `fib(50)` takes minutes.

Why is this happening? Each call to `fib(n)` spawns two calls: `fib(n-1)` and `fib(n-2)`. The number of calls grows exponentially — roughly 2^n calls to compute `fib(n)`. This is called the *exponential time complexity* of naive Fibonacci.

There is a fix: *memoization* — remembering the answers you have already computed so you do not recompute them. Store the results in a dictionary as you go:

```python
def fib_memo(n, memo=None):
    """Return the nth Fibonacci number using memoization."""
    if memo is None:
        memo = {}
    
    if n in memo:
        return memo[n]  # Already computed
    
    if n == 0 or n == 1:
        return n  # Base case
    else:
        result = fib_memo(n - 1, memo) + fib_memo(n - 2, memo)
        memo[n] = result  # Store for later
        return result
```

Now `fib_memo(40)` is instant. The lesson: naive recursion can be inefficient. When you use recursion, ask whether you are recomputing the same subproblems. If you are, memoization or a different algorithm may be needed.

### Why Fibonacci gets slow: exponential growth

Let me be concrete about the cost. When you call `fib(n)`, the naive implementation makes roughly 2^n calls. Here is what that means:

- `fib(10)`: about 2^10 = 1,024 calls
- `fib(20)`: about 2^20 = 1,048,576 calls
- `fib(30)`: about 2^30 = 1,073,741,824 calls
- `fib(40)`: about 2^40 = 1,099,511,627,776 calls

On a modern computer doing a billion operations per second, `fib(40)` takes about 1,000 seconds. That is 16 minutes. This is not a problem with Python; it is a problem with the algorithm. The same exponential explosion happens in any language.

Memoization fixes this by saying: once I have computed `fib(5)`, I never compute it again. With memoization, you compute each value once, and you have `n` values to compute. So the time becomes O(n) instead of O(2^n). The same `fib(40)` call now takes microseconds.

This is a profound lesson: **a correct algorithm can still be inefficient**. And conversely, **recognizing when a problem repeats itself can make the difference between impossible and instant**. Memoization is one technique. Dynamic programming is a whole field built on this idea.

> **Prompt for Claude.** "Write a function `fib(n)` that computes the nth Fibonacci number using the recursive definition `fib(0)=0, fib(1)=1, fib(n)=fib(n-1)+fib(n-2)`. Include a docstring. Write a comment explaining that this is slow (exponential time complexity) due to recomputation. Include a note: 'Don't call this with n > 35 — it will take several seconds.'"

> **Tests to validate.**
> - `fib(0) == 0`
> - `fib(1) == 1`
> - `fib(6) == 8`
> - `fib(10) == 55`

---

## Concept 4 — Recursion on data structures: sum of a list

Recursion shines when the data structure itself is recursive. A list is a good example. A non-empty list can be thought of as its first element plus the rest of the list:

```
[1, 2, 3, 4] = [1] + [2, 3, 4]
```

This naturally suggests a recursive definition of "sum the list":

```
sum([]) = 0                                    # Base case
sum([x] + rest) = x + sum(rest)  # Recursive case
```

In Python:

```python
def sum_list(numbers):
    """Return the sum of all numbers in a list."""
    if len(numbers) == 0:
        return 0  # Base case: empty list sums to 0
    else:
        first = numbers[0]
        rest = numbers[1:]
        return first + sum_list(rest)  # Recursive case
```

Let me trace `sum_list([2, 3, 5])`:

```
sum_list([2, 3, 5])
= 2 + sum_list([3, 5])
= 2 + (3 + sum_list([5]))
= 2 + (3 + (5 + sum_list([])))
= 2 + (3 + (5 + 0))     # sum_list([]) = 0
= 2 + (3 + 5)
= 2 + 8
= 10
```

Four frames on the call stack:

```
Frame 1: sum_list([2, 3, 5]) — waiting
  Frame 2: sum_list([3, 5]) — waiting
    Frame 3: sum_list([5]) — waiting
      Frame 4: sum_list([]) — base case: return 0
    Frame 3: receives 0, computes 5 + 0 = 5, returns 5
  Frame 2: receives 5, computes 3 + 5 = 8, returns 8
Frame 1: receives 8, computes 2 + 8 = 10, returns 10
```

Test it:

```python
print(sum_list([1, 2, 3, 4]))  # Output: 10
print(sum_list([]))             # Output: 0
print(sum_list([7]))            # Output: 7
```

This is clearer than writing a loop. The code mirrors the problem structure: a list is decomposed into its head and tail, the head is processed, and the tail is handed to the same function.

Python has a built-in `sum()` that is faster. But the pattern — decompose, process one piece, recurse on the rest — is one you will see everywhere: tree traversal, parsing, searching, building data structures.

> **Prompt for Claude.** "Write a recursive function `sum_list(numbers)` that takes a list of numbers and returns their sum. Handle the base case when the list is empty (return 0). Use list slicing to separate the first element from the rest, then recurse on the rest. Include a docstring."

> **Tests to validate.**
> - `sum_list([1, 2, 3]) == 6`
> - `sum_list([]) == 0`
> - `sum_list([10]) == 10`
> - `sum_list([-1, 2, -3, 4]) == 2`

---

## Concept 5 — Tree-shaped problems: when recursion is essential

A **tree** is a data structure that branches. Here is a simple tree of folder contents:

```
documents/
├── work/
│   ├── project_a/
│   │   ├── main.py
│   │   └── tests.py
│   └── notes.txt
└── personal/
    ├── recipes.txt
    └── photos/
        ├── beach.jpg
        └── mountains.jpg
```

If you want to count how many files are in the entire tree (including subfolders), you cannot write a simple loop. You do not know in advance how many levels deep the tree goes. This is where recursion is the natural tool.

Here is a function that counts files recursively:

```python
import os

def count_files(path):
    """Count the total number of files in path and all subfolders."""
    if os.path.isfile(path):
        # Base case: this is a file
        return 1
    elif os.path.isdir(path):
        # Recursive case: this is a folder
        total = 0
        for item in os.listdir(path):
            item_path = os.path.join(path, item)
            total += count_files(item_path)  # Recurse into subfolders
        return total
    else:
        # Base case: path does not exist
        return 0
```

The key insight: when you encounter a folder, you recursively count files in each item inside it. Each recursive call handles a smaller subtree. When you hit a file (not a folder), you stop and return 1. The total count is the sum of counts from all subtrees.

This pattern — "if it is atomic, handle it; if it is composite, recurse on the parts" — is the shape of recursion for tree-shaped data.

Notice something important: a loop cannot express this naturally. A loop can process one level of folders, but then you would need to manage a separate queue or stack of "folders still to process." Recursion lets Python manage that queue automatically through the call stack. This is where recursion stops being a stylistic choice and becomes the right tool.

### Trees in real systems

Trees are everywhere. Here are some examples you will encounter:

- **Folder hierarchies:** Every file system is a tree. The operating system itself uses recursion to search for files, compute disk usage, or delete an entire folder.
- **HTML/XML structure:** A web page is a tree. The browser uses recursion to parse HTML elements and their nested children.
- **JSON data:** Nested objects and arrays form a tree. Data processing code recursively traverses it.
- **Abstract syntax trees in compilers:** When a compiler parses your code, it builds a tree of expressions and statements. Evaluating that tree happens recursively.

When you understand recursion on trees, you understand a fundamental pattern in computer science.

### Parsing arithmetic expressions

Another example: parsing a simple arithmetic expression like `2 + 3 * 4`. The expression is a tree:

```
        +
       / \
      2   *
         / \
        3   4
```

A recursive parser would decompose the expression into its left and right operands, recursively evaluate each, then apply the operator. You cannot write this easily with a loop. The recursive structure of the expression demands recursive code.

For this chapter, you do not need to implement a full parser. But when you get to Chapter 13 or beyond, when you are working with real data structures, you will find yourself writing functions that look like `count_files`: checking if something is atomic or composite, and recursing on the parts.

> **Prompt for Claude.** "Write a recursive function `is_palindrome(s)` that checks whether a string `s` reads the same forwards and backwards (e.g., 'racecar', 'noon'). Handle the base cases: strings of length 0 or 1 are palindromes. For the recursive case, check if the first and last characters match, then recurse on the middle substring. Include a docstring."

> **Tests to validate.**
> - `is_palindrome('racecar')` is `True`
> - `is_palindrome('noon')` is `True`
> - `is_palindrome('python')` is `False`
> - `is_palindrome('')` is `True`
> - `is_palindrome('a')` is `True`

---

## Integration: Putting recursion to work

You now have the tools to recognize when a problem is recursive and to implement a solution. Let me show you how to put this together: a function that checks whether a list contains the same elements as another list, even if they are in a different order.

The idea: take the first element from the first list. Find how many times it appears in the second list. Then recurse on the rest of the first list, but *remove one instance* of that first element from the second list.

```python
def same_elements(list1, list2):
    """Return True if list1 and list2 contain the same elements (order doesn't matter)."""
    # Base case: both lists empty
    if len(list1) == 0:
        return len(list2) == 0
    
    # Recursive case: check if the first element of list1 is in list2
    first = list1[0]
    if first not in list2:
        return False  # Element not found, lists are different
    else:
        # Remove one instance of first from list2, then recurse
        list2_without_first = list2.copy()
        list2_without_first.remove(first)  # Remove one occurrence
        return same_elements(list1[1:], list2_without_first)
```

Test it:

```python
print(same_elements([1, 2, 3], [3, 1, 2]))      # True
print(same_elements([1, 1, 2], [1, 2, 1]))      # True
print(same_elements([1, 2, 3], [1, 2, 3, 3]))   # False
print(same_elements([], []))                     # True
```

This function has two base cases: when both lists are empty (they match) or when an element is missing (they do not match). Each recursive call moves closer to the base case by reducing both lists.

Why write this recursively? Because the problem structure is recursive. You are decomposing the problem into smaller versions of itself: "do list1 and list2 have the same elements" reduces to "does the first element of list1 appear in list2, *and* do the rest of list1 and the remainder of list2 have the same elements?" The recursion mirrors the problem shape.

An iterative version would require you to count occurrences of each element in both lists, then compare the counts:

```python
def same_elements_iterative(list1, list2):
    """Iterative version using counts."""
    from collections import Counter
    return Counter(list1) == Counter(list2)
```

This is actually shorter and more efficient. But notice what you lose: the recursive version makes the algorithm visible — you see how the problem shrinks with each call. The iterative version hides the work inside the `Counter` implementation. For teaching, the recursive version shows the thinking. For production code, the iterative version is better.

This is the art of choosing between recursion and iteration. Ask: what is clearer to *read*? What is clearer to *understand*? What will be clearer to *maintain* six months from now when you have forgotten the details?

### Summing recursively across a 2D structure

Here is one more example that shows recursion's power. Suppose you have a list of lists of numbers, and you want to sum all the numbers, no matter how deep the nesting:

```python
def sum_deeply(structure):
    """Sum all numbers in a (possibly nested) list structure."""
    total = 0
    for item in structure:
        if isinstance(item, list):
            # It's a nested list, recurse on it
            total += sum_deeply(item)
        else:
            # It's a number, add it
            total += item
    return total

# Test
print(sum_deeply([1, 2, [3, 4, [5, 6]]]))  # Output: 21 (1+2+3+4+5+6)
```

Notice that this function does not know in advance how deep the nesting goes. It does not need to. It simply asks: is this item a list? If yes, recurse. If no, add it. The recursion naturally handles arbitrary depth.

An iterative version of this would require you to manually manage a stack of lists to process — more bookkeeping, more room for bugs. Recursion lets Python manage the stack for you.

---

## Common errors and how to fix them

### RecursionError: maximum recursion depth exceeded

You will see this error when the base case is not reached within about 1,000 calls. Usually, this means:

1. **No base case:** The function never stops calling itself.
2. **Unreachable base case:** The base case exists, but the recursive call never gets there. For example:

```python
def bad_count(n):
    if n == 0:
        return "Done"
    else:
        return bad_count(n + 1)  # Moving away from 0, not toward it
```

3. **Very deep recursion:** The recursion is correct, but the problem is so deep that 1,000 frames is not enough. You can increase the limit:

```python
import sys
sys.setrecursionlimit(10000)  # Increase to 10,000 frames
```

But be warned: this uses memory. A better solution is often to rewrite the algorithm iteratively or use memoization.

### TypeError or other errors during recursion

When a recursive function crashes on a specific call, use print statements to trace the call stack:

```python
def factorial_debug(n, depth=0):
    indent = "  " * depth
    print(f"{indent}Calling factorial({n})")
    
    if n <= 1:
        print(f"{indent}Base case reached, returning 1")
        return 1
    else:
        result = n * factorial_debug(n - 1, depth + 1)
        print(f"{indent}Returning {result} for factorial({n})")
        return result

factorial_debug(4)
```

This prints:

```
Calling factorial(4)
  Calling factorial(3)
    Calling factorial(2)
      Calling factorial(1)
      Base case reached, returning 1
    Returning 2 for factorial(2)
  Returning 6 for factorial(3)
Returning 24 for factorial(4)
```

The indentation shows the depth of the call stack. You can see exactly where the base case is reached and how the return values propagate back up.

---

## Graduated exercises

**Warm-up: The base case**

1. Write a function `count_down_to_zero(n)` that prints all integers from `n` to 0, one per line. Use recursion. What is your base case? What is your recursive case?

2. Write a function `is_single_element(lst)` that returns `True` if `lst` contains exactly one element and `False` otherwise. Do not use `len()`. Hint: use recursion to strip away one element at a time.

**Application: Building solutions from smaller solutions**

3. Write a function `power(base, exp)` that computes `base^exp` (base to the power of exp) using recursion. For example, `power(2, 5)` should return 32. Do not use Python's `**` operator. Handle the base cases: any number to the power 0 is 1.

4. Write a function `reverse_string(s)` that reverses a string using recursion. For example, `reverse_string("hello")` returns `"olleh"`. Base case: a string of length 0 or 1 is its own reverse. Recursive case: the reverse of a string is the reverse of all-but-the-first-character, followed by the first character.

5. Write a function `gcd(a, b)` (greatest common divisor) using Euclid's algorithm: the GCD of `a` and `b` is the same as the GCD of `b` and `a % b`, until `b == 0`, at which point the GCD is `a`.

**Synthesis: Bringing recursion to bear on data structures**

6. Write a function `list_length(lst)` that returns the length of a list using recursion. Do not use `len()`. Base case: an empty list has length 0. Recursive case: a non-empty list has length 1 plus the length of the rest.

7. Write a function `contains(lst, value)` that returns `True` if `value` is in `lst` and `False` otherwise. Use recursion. Do not use the `in` operator.

8. Write a function `flatten(lst)` that takes a list that may contain nested lists and returns a single flat list. For example, `flatten([1, [2, 3], [4, [5, 6]]])` returns `[1, 2, 3, 4, 5, 6]`. Hint: check if each element is a list (use `isinstance(item, list)`). If it is, recurse on it. If not, add it to the result.

**Challenge: Problems whose very structure is recursive**

9. Write a function `permutations(s)` that generates all permutations of a string `s`. For example, `permutations("AB")` returns `["AB", "BA"]`. Hint: the permutations of a string are each character combined with the permutations of the rest.

10. Write a function `binary_search(sorted_list, target, low, high)` that searches for `target` in a sorted list using recursion. Return the index if found, or `-1` if not found. This is one of the few algorithms where recursion is nearly identical to iteration, but understanding it recursively helps you see the pattern of "divide and conquer."

---

## Claude Code — hands-on with recursion

The best way to understand recursion is to build it, run it, and watch the call stack in action. Here is a concrete workshop using Claude Code.

### The setup

We will create three functions:

1. **`factorial(n)`** — a simple recursive function with a clear base case and recursive case.
2. **`sum_list(lst)`** — recursion on a data structure, showing how to decompose a list into head and tail.
3. **`count_files(path)`** — recursion on a tree-shaped problem, proving that recursion is essential for directory traversal.

We will also write tests to validate each one, then add a fourth function `factorial_memo(n)` using `functools.lru_cache` to show how memoization transforms a slow recursive function into a fast one.

### Build the functions

Ask Claude Code to create a file `recursion_demo.py` with these three functions:

> **Prompt for Claude Code:**
> "Write a Python file called `recursion_demo.py` with three functions:
>
> 1. `factorial(n)` — returns `n!` (n factorial). Base case: `n == 0` or `n == 1` returns 1. Recursive case: `n * factorial(n - 1)`. Raise `ValueError('Factorial undefined for negative numbers')` if `n < 0`.
>
> 2. `sum_list(lst)` — returns the sum of all numbers in a list. Base case: empty list returns 0. Recursive case: separate the first element from the rest using list slicing, then return `first + sum_list(rest)`.
>
> 3. `count_files(path)` — recursively counts files in a directory and all subdirectories. Use `os.path.isfile(path)` to check if it's a file (base case, return 1), `os.path.isdir(path)` to check if it's a folder (recursive case, loop through `os.listdir(path)` and recurse on each item), and return 0 if the path does not exist.
>
> Include docstrings for each. Add a comment above `count_files` saying 'Warning: do not run on system directories — it can take a very long time.'"

Claude Code will generate the file. Keep it.

### Write tests

Now ask Claude Code to create `test_recursion.py` with tests that validate all three functions:

> **Prompt for Claude Code:**
> "Write a test file called `test_recursion.py` that uses pytest to test the functions in `recursion_demo.py`. Include:
>
> For `factorial`:
> - `factorial(0) == 1`
> - `factorial(5) == 120`
> - `factorial(1) == 1`
> - `factorial(-3)` raises `ValueError`
>
> For `sum_list`:
> - `sum_list([]) == 0`
> - `sum_list([1, 2, 3]) == 6`
> - `sum_list([-1, 2, -3, 4]) == 2`
> - `sum_list([10]) == 10`
>
> For `count_files`, create a temporary directory structure with 3 files in the root and a subdirectory with 2 more files (5 total). Test that `count_files(temp_directory) == 5`. Use pytest fixtures to set up and tear down the temporary directory."

Run the tests. All should pass.

### Watch the call stack

Now add a debugging version of `factorial` to `recursion_demo.py`:

> **Prompt for Claude Code:**
> "Add a function `factorial_debug(n, depth=0)` to `recursion_demo.py` that prints the call stack as it executes. Each print statement should be indented by `depth * 2` spaces. Print when entering (show the value of `n`), print when hitting the base case, and print the return value. Call this function with `factorial_debug(5)` at the bottom of the file (as a comment or demo, not automatic execution)."

Run `recursion_demo.py` and look at the output. You will see the call stack unwind.

### Add memoization

Now add one more function to `recursion_demo.py`:

> **Prompt for Claude Code:**
> "Add to `recursion_demo.py`:
>
> ```python
> from functools import lru_cache
>
> @lru_cache(maxsize=None)
> def factorial_memo(n):
>    \"\"\"Factorial using memoization via functools.lru_cache.\"\"\"
>    if n < 0:
>        raise ValueError('Factorial undefined for negative numbers')
>    if n == 0 or n == 1:
>        return 1
>    return n * factorial_memo(n - 1)
> ```
>
> Then add a benchmark script at the bottom that times `factorial_memo(30)` and compares it (in comments) to how slow naive recursion would be. Note that factorial doesn't have the exponential blowup problem that Fibonacci does, but this shows the pattern."

Run the function with `n=30`. It returns instantly. This is the power of memoization.

### The lesson

Three observations from this hands-on work:

1. **Base case and recursive case are non-negotiable.** Without a reachable base case, the function crashes with `RecursionError`. With one, the logic is clean and readable.

2. **The call stack is real and traceable.** You can print it, indent it, and watch how frames open and close. This is not magic — it is bookkeeping that Python does for you.

3. **Recursion can be slow, but memoization can fix it.** When a recursive function recomputes the same subproblems, memoization remembers the results. The `@lru_cache` decorator does this automatically. Fibonacci gets faster by a factor of millions; factorial is fast anyway, but the same technique applies.

When you write your own recursive functions in later chapters, come back to this workshop. Build them. Run the tests. Trace the stack. See where memoization would help.

---

## Chapter summary

Recursion is a function calling itself with a smaller version of the problem. Every recursive function has two parts:

- **Base case:** the condition under which the function stops calling itself and returns a known answer. Without a base case, the function loops forever.
- **Recursive case:** where the function calls itself with a smaller input, moving toward the base case.

Recursion is powerful for problems with recursive structure: problems that break naturally into smaller identical subproblems, or data structures that contain smaller versions of themselves (lists, trees, nested data). The call stack keeps track of all open function calls, so recursion uses more memory than iteration, but it often makes the code clearer.

Common mistakes:

- No base case or unreachable base case → `RecursionError`.
- Recursive call that moves away from the base case (e.g., `n + 1` instead of `n - 1`) → infinite recursion.
- Inefficient recursion that recomputes the same subproblems (like naive Fibonacci) → exponential time. Use memoization or a different algorithm.

Recursion is not the right tool for every job. Use it when the problem structure mirrors recursion. Use iteration when a loop is clearer. Use both in the same program when they each fit their part.

---

## What would change my mind

If I encountered a recursive algorithm that significantly outperformed an iterative version on the same problem (beyond the constant factors of function-call overhead), it would suggest that recursion carries a hidden advantage I have not yet understood — perhaps related to cache locality or memory layout. So far, I have not seen this.

## Still puzzling

I do not fully understand why Python chose a recursion depth limit of 1,000. This works for most student code and most real-world code, but it is arbitrary enough that I wonder whether a deeper limit (or a dynamic limit based on available memory) would have been better. The limit is documented, but the reasoning behind the specific number is not obvious to me.

---

## Tags

recursion, base-case, call-stack, tree-shaped-data, factorial, Fibonacci, binary-search, memoization, stack-overflow, Python

## Author

Nik Bear Brown
---

## LLM Exercise — Chapter 12: Recursion (Text Adventure Project)

**Project:** Text Adventure.
**What you're building this chapter:** recursive structures — containers in containers, dialogue trees, recursive search.
**Tool:** **Claude Code**.

---

**The Prompt:**

```
Chapter 12 of my Text Adventure project. Chapter 12 covered:
recursion; base case; recursive case; the call stack; tree
problems; RecursionError.

Add a recursive feature to the game. Pick one of these:

**Option A: Containers within containers.**

Some items can contain other items. A chest contains items. A
satchel contains items. The contents might themselves contain
other items (a chest with a locked box that contains a key).

1. **Container class.** Inherits from Item (preview of Ch 13):
   ```python
   class Container(Item):
       def __init__(self, name, description, contents=None):
           super().__init__(name, description)
           self.contents = contents or []

       def list_all_items_recursive(self, depth=0):
           # Print all items inside, including nested containers.
           # Base case: contents is empty.
           # Recursive case: for each item, if it's a Container,
           # recurse into it; otherwise just print it.
           ...

       def find_item_recursive(self, item_name):
           # Returns True if item_name is found anywhere in the
           # nested structure. False otherwise.
           ...
   ```

2. **Test cases:**
   - A satchel with a torch.
   - A chest with a satchel that has a torch.
   - A chest with a locked box (Container) that's locked
     (Container with a `locked` attribute; locked containers can't
     be opened).

**Option B: Dialogue tree for NPCs.**

NPCs have conversation trees. Each dialogue node has text and a
list of player-response options, each leading to another node.

1. **DialogueNode class.**
   ```python
   class DialogueNode:
       def __init__(self, text, responses=None):
           self.text = text
           # responses: list of (response_text, next_node) tuples
           self.responses = responses or []

       def converse(self, player):
           # Print the node's text.
           # If no responses: end conversation.
           # If responses: print them, get player choice, recurse
           # into the chosen response's next_node.
           ...
   ```

2. **Build a real conversation tree** with a shopkeeper or
   informant NPC. 3-5 levels deep.

**Option C: Recursive maze rendering.**

Some rooms have sub-rooms (a building has interior rooms). Use
recursion to print the full "you can see" description, recursively
going into sub-rooms that the player can see into.

Pick one option, implement it. In addition to the code, produce
the prompt + tests. Tests must verify:
- The base case terminates.
- The recursive case does what it claims.
- No RecursionError for normal-sized cases.

End with: identify the recursion depth limit Python imposes by
default (Hint: `sys.getrecursionlimit()`). For your game's worst-
case nesting depth, are you safe? Iteration would be safer for
deeply nested cases.
```

---

**What this produces:** A real recursive feature in the game. Recursion is one of programming's most counterintuitive concepts — having it tied to a game feature you can play makes it tactile.

**Connection to previous chapters:** Ch 11's classes get extended with recursive methods.

**Preview of next chapter:** Chapter 13 covers inheritance. You'll create Weapon, Armor, Potion as subclasses of Item; Goblin, Dragon as subclasses of Enemy. Polymorphism — different enemies attack differently.


---

##  AI Wayback Machine
**Stephen Kleene** was developed the theory of recursive functions in the 1930s — the mathematical foundation of recursion in programming.

**Run this:**

```
Who is Stephen Kleene, and how does their work connect to recursion we covered in this chapter? Keep it to three paragraphs. End with the single most surprising thing about their career or ideas.
```

→ Search **"Stephen Kleene"** on Wikipedia.

**Now make the prompt better.** Try one of these:

- Ask it to apply Stephen Kleene's ideas to a small Python program you'd write.
- Add a constraint: "Answer including criticisms or limits of Stephen Kleene's framework."

What changes? What gets better? What gets worse?
