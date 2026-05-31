# Bookmap — Chapter 3 (Objects)

## Source materials and their contribution to the chapter

This chapter synthesizes material from OpenStax CS1 modules (m00020–m00026), a contemporary course on Python fundamentals. The original source is an OpenStax-style, lecture-adjacent treatment. The chapter reframes it in Attenborough × Feynman voice: opening in a puzzle (the dual nature of objects and identity), then unfolding the three layers (anatomy, methods, mutability) with the three-part Claude code pattern integrated throughout.

---

## Original source: m00020 (Chapter intro)

**What it teaches:** Objects are a central concept in Python. Variables refer to objects. Objects have properties (type, value).

**How the chapter uses it:** Compressed into the chapter opening and the "anatomy of an object" concept section. The original is thin on mechanism; the chapter expands by making identity (the rarely-discussed third property) the puzzle that draws the reader in.

---

## Original source: m00021 (String indexing and Unicode)

**What it teaches:** Strings are sequences. Characters have indexes (0-based, negative indexing). Unicode code points and `ord()`/`chr()`.

**How the chapter uses it:** Deferred to Chapter 8 (Strings). This chapter doesn't dig into string internals or Unicode. It mentions that strings are immutable, but doesn't explain why or demonstrate character encoding. The architecture of the book puts string manipulation in its dedicated chapter.

---

## Original source: m00022 (F-strings and formatting)

**What it teaches:** F-strings as a cleaner way to format output. Format specifiers for numbers (padding, decimal places, comma separators).

**How the chapter uses it:** Deferred to Chapter 8 (Strings). F-strings are a string feature, not an object-model feature. They appear in the chapter only in worked examples for readability (e.g., `f"Name: {cleaned}"`), not as something the student is learning from this chapter.

---

## Original source: m00023 (Object properties: identity, type, value)

**What it teaches:** Objects have identity (`id()`), type (`type()`), and value. Variables refer to objects (memory diagrams). The distinction between variables and objects.

**How the chapter uses it:** This is the backbone of Concept 1. The chapter keeps the three properties but reorders the opening to start with the puzzle (Why do two 5's have the same identity?) rather than announcing the framework. The memory diagram notation is simplified and integrated into the narrative.

---

## Original source: m00024 (Lists: creation, indexing, mutation)

**What it teaches:** Lists are created with `[]`. Individual elements are accessed by index. Lists are mutable. `len()` returns the length.

**How the chapter uses it:** Minimal treatment. Lists are mentioned in the context of mutability (the third concept) but not deeply explored. Indexing and `len()` are shown in the worked examples and exercises but not formally taught. Full list coverage is Chapters 9 (Lists), so this chapter treats lists as a concrete mutable type that illustrates the concept.

---

## Original source: m00025 (Tuples: creation, immutability)

**What it teaches:** Tuples are immutable sequences. How they differ from lists. The performance and safety benefits of immutability.

**How the chapter uses it:** Mentioned in the "Common misconceptions" section and the Chapter summary as a foil to lists — the immutable alternative. Not formally taught; that belongs in Chapter 9. The chapter just makes it clear that mutable vs. immutable is a design choice with trade-offs.

---

## Original source: m00026 (Chapter summary)

**What it teaches:** A reference table of key concepts and functions from the chapter.

**How the chapter uses it:** Replaced with a narrative Chapter summary that integrates the three concepts (anatomy, methods, mutability) into a coherent story. The reference table moves to the Pantry file for easy lookup.

---

## What the chapter adds

**Cold open:** The chapter begins with a puzzle (where does `.upper()` come from? why does `id(5)` return the same integer twice?) rather than announcing "This chapter is about objects." This is the Attenborough × Feynman move: start in wonder, not in taxonomy.

**The three-part Claude pattern:** Every substantive code example includes a prompt you could send to Claude and tests you could run to verify the result. This transforms each code snippet from "here's what works" into "here's how to elicit this from an LLM and verify it worked."

**Etymology and glossing:** Terms like "object," "method," and "mutable" are glossed on first use, connecting to their roots (Latin, Greek) and showing why the word choice matters. This addresses the Feynman principle: if you don't understand why something is named what it is, you don't fully understand it.

**Trade-offs named explicitly:** Each section calls out a trade-off (efficiency vs. safety for mutability; functional vs. object-oriented for method calling; caching for performance). This prepares students to make choices later in their programming.

**Scale shift:** The chapter mentions what happens at scale (millions of objects in long-running programs, garbage collection, memory pressure). This anchors the theoretical material in engineering reality.

**Error teaching:** At least one error (`AttributeError`) is shown with cause and fix, so students can recognize and resolve it.

---

## Ideas for extension

If this chapter were expanded to include more detail:

1. **The memory layout of objects.** CPython's object structure (`PyObject` in C), the reference count, the type pointer. Why identity is the memory address.

2. **Garbage collection in detail.** Reference counting vs. generational collection. What happens when no variable points to an object anymore.

3. **Special methods (dunder methods).** How `__str__`, `__repr__`, `__len__` work. Why some types have `len()` and others don't.

4. **Method resolution order (MRO).** When you call a method, how Python finds it. Inheritance and overriding (anticipating Chapter 13).

5. **Mutability in the wild.** Case studies: why lists are mutable (common operation is append), why strings are immutable (safety and performance), why tuples exist (defensive programming and hashing).

---

## Connections forward

- **Chapter 4 (Decisions):** Comparisons (`==` vs. `is`) are used heavily in conditionals. Understanding the distinction matters here.
- **Chapter 6 (Functions):** Functions take objects as arguments and return objects. The parameter/return mechanism depends on references.
- **Chapter 9 (Lists):** Deep dive into the mutable list. Exercises will build on the aliasing pitfall introduced here.
- **Chapter 10 (Dictionaries):** Keys must be immutable (hashable). This chapter lays the foundation for why tuples can be keys but lists cannot.
- **Chapter 11 (Classes):** The ultimate expression of objects: you design the type, the methods, the mutability. This chapter prepares students to think in those terms.

---

## Pedagogical rationale

The chapter front-loads the object model before diving into specific types (strings, lists, tuples). This is unconventional compared to the OpenStax source, which treats objects as an appendix to string manipulation. But it is necessary:

1. **Removes magical thinking.** Once students understand that `.upper()` is a method *attached to the string object*, not a free function, the rest of Python becomes less mysterious.

2. **Prepares for common pitfalls.** The aliasing trap (two variables sharing a list) will confuse students in Chapter 9 unless they've already seen the reference model.

3. **Unifies the language.** Functions, methods, operators, comparisons — they all make more sense once you understand that everything is an object with properties and operations.

---

## Differences from the source

| Aspect | Source (OpenStax) | Chapter 3 (This version) |
|--------|-------------------|-------------------------|
| **Opening** | Announces "This chapter covers objects, strings, and lists" | Opens with a puzzle: where did `.upper()` come from? |
| **Scope** | Covers strings, f-strings, lists, tuples in one chapter | Focuses on object model; defers string details and list operations to later chapters |
| **Mutability treatment** | Introduced casually, as a property of lists | Elevated to a major concept with trade-offs explicitly named |
| **Code examples** | Show what works; sometimes lack context | Accompanied by prompts and tests (Claude pattern) |
| **Aliasing** | Mentioned in exercises | Foregrounded as a central pitfall; illustrated with real examples |
| **Voice** | Expository; instructor explaining facts | Conversational; author and reader figuring out the architecture together |
| **Errors** | Not emphasized | `AttributeError` is taught as a recognizable sign |

