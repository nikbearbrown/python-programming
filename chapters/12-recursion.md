# Chapter 12 — Recursion
*A function that calls itself — and the one rule that keeps it from running forever.*

Here is a function with no loop, no list, no accumulator:

```python
def factorial(n):
    if n == 0:
        return 1
    return n * factorial(n - 1)
```

It calls itself. `factorial(5)` calls `factorial(4)`, which calls `factorial(3)`, which calls `factorial(2)`, which calls `factorial(1)`, which calls `factorial(0)` — and `factorial(0)` returns `1`. Then `factorial(1)` returns `1 * 1 = 1`. Then `factorial(2)` returns `2 * 1 = 2`. Then `factorial(3)` returns `3 * 2 = 6`. Then `factorial(4)` returns `4 * 6 = 24`. Then `factorial(5)` returns `5 * 24 = 120`.

The answer builds on the way back out, not on the way in. That's what makes recursion feel strange the first time. You're not building up an accumulator in a loop — you're deferring the multiplication until the innermost call comes back with an answer, and then each outer layer completes its own multiplication as control returns to it.

This chapter explains how that works, when it's the right tool, and what happens when it goes wrong.

---

## The Two Parts Every Recursive Function Must Have

A recursive function calls itself. That means, left unchecked, it would call itself forever. What stops it is the **base case** — the condition under which the function does *not* call itself and instead returns a known answer.

Every recursive function has exactly two parts:

1. **Base case:** If the problem is small enough that I already know the answer, return it directly. No recursive call.
2. **Recursive case:** If the problem is larger, call myself with a smaller version of it, and return something built from that result.

The countdown example makes this concrete:

```python
def countdown(n):
    if n == 0:
        print("Blastoff!")
        return
    print(n)
    countdown(n - 1)
```

Base case: `n == 0` — print "Blastoff!" and stop. Recursive case: print `n`, then call `countdown(n - 1)`. The argument shrinks by one each time. Eventually it reaches zero, the base case fires, and the chain of calls unwinds.

Run `countdown(3)`:

```
3
2
1
Blastoff!
```

What's happening inside Python is a **call stack** — a record of every function call that's currently open and waiting for an inner call to finish.

When `countdown(3)` calls `countdown(2)`, Python suspends `countdown(3)` and starts `countdown(2)`. When `countdown(2)` calls `countdown(1)`, Python suspends `countdown(2)`. When `countdown(1)` calls `countdown(0)`, Python suspends `countdown(1)`. When `countdown(0)` returns, Python resumes `countdown(1)`, which then returns. Then `countdown(2)` resumes and returns. Then `countdown(3)` resumes and returns.

Four calls were open simultaneously. The stack is the mechanism that makes this possible: each suspended call remembers where it left off, and control returns to it when the inner call finishes.

<!-- → [FIGURE: vertical stack diagram for countdown(3) — four frames stacked top-to-bottom: countdown(3) at bottom (waiting), countdown(2) above it (waiting), countdown(1) above that (waiting), countdown(0) at top (returns first) — arrows showing "called by" downward and "returns to" upward, base case highlighted at top — student should see that the deepest call returns first and control unwinds back through the stack] -->

### What Happens Without a Base Case

Remove the base case from `countdown`:

```python
def bad_countdown(n):
    print(n)
    bad_countdown(n - 1)   # No stop condition
```

Python opens a new frame for every call. After about 1,000 calls, it runs out of stack space and stops with:

```
RecursionError: maximum recursion depth exceeded
```

This is Python's hard limit: approximately 1,000 active frames at once. It's not a number to work around — it's a signal that something is wrong with the algorithm. A missing base case is the most common cause.

Three questions to ask before running any recursive function:

1. Does a base case exist?
2. Is the base case reachable from the starting input?
3. Does every recursive call move toward the base case?

If any answer is no, the function will recurse until Python kills it.

---

## Factorial: The Canonical Example, Traced

The factorial of `n` — written `n!` — is the product of all positive integers from 1 to `n`. It has a natural recursive definition:

```
0! = 1              (by definition)
n! = n × (n−1)!     for n > 0
```

"The factorial of n is n times the factorial of n−1." The problem genuinely refers to a smaller version of itself. That's the signal that recursion fits.

```python
def factorial(n):
    """Return n! — the product of all integers from 1 to n."""
    if n < 0:
        raise ValueError("Factorial undefined for negative numbers.")
    if n == 0:
        return 1
    return n * factorial(n - 1)
```

Trace `factorial(4)` by expanding each call:

```
factorial(4)
= 4 × factorial(3)
= 4 × (3 × factorial(2))
= 4 × (3 × (2 × factorial(1)))
= 4 × (3 × (2 × (1 × factorial(0))))
= 4 × (3 × (2 × (1 × 1)))
= 4 × (3 × (2 × 1))
= 4 × (3 × 2)
= 4 × 6
= 24
```

The multiplications happen on the way *back* — as each call returns, its caller completes its multiplication. The innermost call (`factorial(0)`) is the first to return. The outermost call (`factorial(4)`) is the last.

<!-- → [FIGURE: tree diagram showing factorial(4) call chain — linear sequence of nested calls going down to factorial(0), then return values propagating back up with each multiplication shown at each level: 0→1, 1→1, 2→2, 3→6, 4→24 — student should see that the function descends first and the computation happens on the way back up] -->

The error guard for negative inputs matters. Without it, `factorial(-1)` calls `factorial(-2)`, which calls `factorial(-3)`, spiraling away from zero instead of toward it — never reaching the base case, eventually hitting the recursion limit.

---

## Fibonacci: When Naive Recursion Is Dangerously Slow

The Fibonacci sequence — 0, 1, 1, 2, 3, 5, 8, 13, ... — is defined by:

```
fib(0) = 0
fib(1) = 1
fib(n) = fib(n−1) + fib(n−2)   for n > 1
```

The recursive translation is direct:

```python
def fib(n):
    """Return the nth Fibonacci number."""
    if n == 0 or n == 1:
        return n
    return fib(n - 1) + fib(n - 2)
```

This is correct. It's also catastrophically slow for large `n`. To see why, expand `fib(5)` into its call tree:

```
                    fib(5)
                   /      \
              fib(4)        fib(3)
             /      \       /    \
        fib(3)   fib(2) fib(2) fib(1)
        /   \    /   \   /   \
    fib(2) fib(1) fib(1) fib(0) fib(1) fib(0)
    /   \
fib(1) fib(0)
```

`fib(3)` is computed twice. `fib(2)` is computed three times. `fib(1)` is computed five times. Each level of the tree roughly doubles the work. The number of calls to compute `fib(n)` grows as 2^n.

What this means in practice:

| n | Approximate calls |
|---|---|
| 10 | ~1,000 |
| 20 | ~1,000,000 |
| 30 | ~1,000,000,000 |
| 40 | ~1,000,000,000,000 |

At a billion operations per second, `fib(40)` takes about 1,000 seconds. Not a performance concern — an impossibility.

The fix is **memoization**: store results you've already computed so you don't compute them again.

```python
def fib_memo(n, memo=None):
    """Return the nth Fibonacci number using memoization."""
    if memo is None:
        memo = {}
    if n in memo:
        return memo[n]
    if n == 0 or n == 1:
        return n
    result = fib_memo(n - 1, memo) + fib_memo(n - 2, memo)
    memo[n] = result
    return result
```

With memoization, each value of `n` is computed exactly once. The number of calls is proportional to `n`, not to 2^n. `fib_memo(40)` is now instant.

<!-- → [CHART: comparison of calls required for naive fib vs memoized fib across n=5 to n=35 — two lines, one exponential (naive) and one linear (memoized) — y-axis is number of function calls, x-axis is n — student should see the exponential blowup visually and understand why memoization matters] -->

The lesson goes beyond Fibonacci: **a correct recursive algorithm can still be wrong because it's impossibly slow**. When a recursive function recomputes the same subproblems, memoization — or a completely different algorithm — is the right response.

Python's standard library provides a decorator that adds memoization automatically:

```python
from functools import lru_cache

@lru_cache(maxsize=None)
def fib_fast(n):
    """Return the nth Fibonacci number, with automatic memoization."""
    if n == 0 or n == 1:
        return n
    return fib_fast(n - 1) + fib_fast(n - 2)
```

`@lru_cache` intercepts every call to `fib_fast`, checks whether that input has been computed before, returns the cached result if so, and computes and stores it if not. The function itself is unchanged — only the calling mechanism is different.

---

## Recursion on Data: Summing a List

Lists have a naturally recursive structure. A non-empty list is a first element plus a shorter list. The sum of a list is the first element plus the sum of the rest.

```python
def sum_list(numbers):
    """Return the sum of all numbers in a list."""
    if len(numbers) == 0:
        return 0
    return numbers[0] + sum_list(numbers[1:])
```

Trace `sum_list([2, 3, 5])`:

```
sum_list([2, 3, 5])
= 2 + sum_list([3, 5])
= 2 + (3 + sum_list([5]))
= 2 + (3 + (5 + sum_list([])))
= 2 + (3 + (5 + 0))
= 10
```

The base case is the empty list, which sums to 0 by definition. Python has `sum()` built in and it's faster, but this example demonstrates the pattern: decompose the list into head and tail, process the head, recurse on the tail.

The same pattern works for searching, filtering, and building new structures. Anywhere you'd write a loop that processes a list element-by-element, there's an equivalent recursive formulation. The recursive version is often clearer when the operation itself has recursive structure — when what you do to element `n` depends on what you did to element `n-1` in a non-trivial way.

---

## When Recursion Is Not Just Clearer — It's the Only Tool

There's a category of problem where iteration doesn't just become less readable — it requires you to manually build and manage the same call stack that Python would build for you automatically. These are **tree-shaped problems**: problems where the structure branches and you don't know in advance how deep it goes.

A file system is a tree. A folder contains files and other folders. Those folders contain files and other folders. To count every file in a directory tree:

```python
import os

def count_files(path):
    """Count all files in path and every subdirectory."""
    if os.path.isfile(path):
        return 1
    if os.path.isdir(path):
        total = 0
        for item in os.listdir(path):
            total += count_files(os.path.join(path, item))
        return total
    return 0
```

The two base cases: a file returns 1. A path that doesn't exist returns 0. The recursive case: a directory sums the counts from all its contents, each of which might itself be a directory.

The depth of the tree is not known when you write the function. It's not known when you run it. Recursion handles arbitrary depth automatically. An iterative version would need a manually managed queue of "directories still to visit" — more code, more room for bugs, the same work.

This pattern — "if it's atomic, handle it; if it's composite, recurse on its parts" — appears throughout computer science:

- Parsing HTML (a page is made of elements, each of which contains elements)
- Evaluating arithmetic expressions (`2 + 3 * 4` is an addition of `2` and a multiplication)
- Processing JSON (objects contain other objects contain arrays contain objects)
- Any algorithm in the "divide and conquer" family: binary search, merge sort, quicksort

For all of these, recursion is the natural expression of the algorithm. Using iteration requires simulating the call stack manually, which is what recursion gives you for free.

<!-- → [FIGURE: tree diagram of a small directory structure — root folder with two subfolders, each containing files and possibly another subfolder — arrows showing the recursive calls: count_files called on root, then on each child, then on each grandchild — base cases (files) labeled with "return 1", interior nodes labeled with "sum of children" — student should see recursion traversing the tree depth-first] -->

---

## Recursion vs. Iteration: When to Use Which

Recursion is not a replacement for loops. It's a different tool for different problems.

Use **iteration** when:
- A loop naturally expresses the algorithm (sum a list, print 1 to 100, process each line of a file)
- The problem has a fixed depth you know in advance
- Memory is a concern and you want to avoid deep call stacks

Use **recursion** when:
- The problem definition is naturally recursive (factorial, Fibonacci, binary search)
- The data structure branches at unknown depth (trees, nested data, file systems)
- The recursive formulation is significantly clearer than the iterative equivalent

Choose based on clarity first. The performance difference between recursion and iteration is rarely relevant unless you're hitting the recursion depth limit or computing something like naive Fibonacci. When you force iteration onto a genuinely recursive problem, the code gets harder to read. When you force recursion onto a flat loop, you get unnecessary overhead and a risk of hitting the stack limit.

---

## Debugging Recursive Functions

When a recursive function misbehaves, add a depth parameter and print the call:

```python
def factorial_debug(n, depth=0):
    indent = "  " * depth
    print(f"{indent}factorial({n}) called")
    if n == 0:
        print(f"{indent}base case: returning 1")
        return 1
    result = n * factorial_debug(n - 1, depth + 1)
    print(f"{indent}returning {result}")
    return result
```

```
factorial(4) called
  factorial(3) called
    factorial(2) called
      factorial(1) called
        factorial(0) called
        base case: returning 1
      returning 1
    returning 2
  returning 6
returning 24
```

The indentation shows depth. You can see exactly where the base case fires and how values propagate back up. Add this pattern whenever a recursive function isn't doing what you expected — it makes the call stack visible.

*What would change my mind:* The chapter argues that recursion's primary use is on tree-shaped data where iteration would require a manually managed stack. If a general-purpose framework emerged that made explicit stack management as clean as recursive calls — with similar readability and less memory overhead — that would change the calculus. So far, recursion remains the cleaner expression for this class of problems.

*Still puzzling:* Python's default recursion limit of 1,000 feels arbitrary. It prevents certain naturally deep recursive algorithms (traversing large trees, computing Fibonacci naively for large `n`) without any graceful fallback — the program simply crashes. Other languages implement tail-call optimization, which turns certain recursive calls into loops automatically, removing the depth limit. Python deliberately does not, on grounds of readability (Guido van Rossum's reasoning: tracebacks become unintelligible when tail calls are eliminated). That tradeoff is defensible, but the specific number 1,000 remains a mystery to me.

---

## Exercises

### Warm-up

**Exercise 12.1** *(Base case identification.)*
For each function, identify the base case and the recursive case. Then trace the call for the given input.

(a) `countdown(3)` — from the chapter opening.
(b) `factorial(4)` — from the chapter.

**Exercise 12.2** *(Missing base case.)*
What happens when you call `bad_countdown(3)` where `bad_countdown` has no base case? What error message do you see? Why does Python stop at approximately 1,000 calls?

**Exercise 12.3** *(Trace by hand.)*
Trace `sum_list([1, 4, 2])` step by step, showing the call stack at each level and the value returned from each call.

### Application

**Exercise 12.4** *(Power.)*
Write a recursive function `power(base, exp)` that returns `base` raised to `exp`. Do not use Python's `**` operator. Base case: any number to the power 0 is 1. Recursive case: `base^exp = base × base^(exp−1)`. Test with `power(2, 5)` (should be 32) and `power(3, 0)` (should be 1).

**Exercise 12.5** *(String reversal.)*
Write a recursive function `reverse_string(s)` that returns `s` reversed. Base case: a string of length 0 or 1 is its own reverse. Recursive case: the reverse of a string is the reverse of all-but-the-first character, followed by the first character.

**Exercise 12.6** *(GCD.)*
Write a recursive function `gcd(a, b)` (greatest common divisor) using Euclid's algorithm: `gcd(a, b) = gcd(b, a % b)` when `b > 0`, and `gcd(a, 0) = a`. Test with `gcd(48, 18)` (should be 6) and `gcd(100, 75)` (should be 25).

### Synthesis

**Exercise 12.7** *(List length without `len`.)*
Write a recursive function `list_length(lst)` that returns the length of a list without using `len()`. Base case: empty list has length 0. Recursive case: one plus the length of the tail.

**Exercise 12.8** *(Palindrome check.)*
Write a recursive function `is_palindrome(s)` that returns `True` if `s` reads the same forwards and backwards. Base cases: strings of length 0 or 1 are palindromes. Recursive case: the first and last characters match, and the middle is a palindrome. Test with `"racecar"` (True), `"python"` (False), and `""` (True).

**Exercise 12.9** *(Flatten a nested list.)*
Write a recursive function `flatten(lst)` that takes a list that may contain nested lists and returns a single flat list. For each element: if it's a list, recurse into it; otherwise add it directly. Test with `flatten([1, [2, 3], [4, [5, 6]]])` — should return `[1, 2, 3, 4, 5, 6]`. Use `isinstance(item, list)` to check.

### Challenge

**Exercise 12.10** *(Binary search.)*
Write a recursive function `binary_search(sorted_list, target, low, high)` that searches for `target` in a sorted list. Return the index if found, `-1` if not. The algorithm: check the middle element; if it equals the target, return its index; if the target is smaller, recurse on the left half; if larger, recurse on the right half. The base case: `low > high` means the target isn't there.

**Exercise 12.11** *(Memoized Fibonacci.)*
Write `fib_fast(n)` using `@lru_cache` from `functools`. Then time `fib_fast(40)` and compare it to `fib(40)` from the chapter (be careful — naive `fib(40)` may take several minutes; add a comment warning about this rather than actually running it). Explain in a comment why the time difference is so dramatic.

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

## AI Wayback Machine

**Stephen Kleene** developed the theory of recursive functions in the 1930s — the mathematical foundation of recursion in programming.

**Run this:**

```
Who is Stephen Kleene, and how does their work connect to recursion we covered in this chapter? Keep it to three paragraphs. End with the single most surprising thing about their career or ideas.
```

→ Search **"Stephen Kleene"** on Wikipedia.

**Now make the prompt better.** Try one of these:

- Ask it to apply Stephen Kleene's ideas to a small Python program you'd write.
- Add a constraint: "Answer including criticisms or limits of Stephen Kleene's framework."

What changes? What gets better? What gets worse?
