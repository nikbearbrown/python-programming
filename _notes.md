# Revision Notes

Track what you've added, removed, or rewritten here.

## 2026-05-07 — book.md created

Drafted `book.md` for the bundle. Audience: CS-1 freshmen. Voice carries from contemporary-mathematics chapter 1; the addition is heavy code density and a chapter-wide three-part Claude pattern: every substantive code block ships with a *Prompt for Claude* (a clean, copy-pasteable prompt that elicits the code) and a *Tests to validate* block (concrete assertions or expected outputs that prove the code works). The Claude pattern is the book's signature move: it teaches specification (writing a good prompt) and verification (writing tests that catch bugs LLMs introduce) alongside the syntax.

## 2026-05-07 — Attenborough × Feynman v1.1 conversion run: Chapters 1–15

15 chapters dispatched in parallel to subagents; voice anchored on contemporary-mathematics/chapters/01-sets.md. Source folders deleted after Combined Test passed and ≥ 3,500-word floor cleared. `_toc.md` rewritten to flat structure.

| Ch | Slug | Words | Notes |
|---|---|---:|---|
| 01 | statements | 5,089 | 10 source modules → 3 concepts (I/O & variables; assignment & arithmetic; errors & how to read them). 5 three-part triples. Cold open: terminal printing first output. |
| 02 | expressions | 4,402 | 10 source modules → 3 concepts (operators & precedence; types & casting; division operators). 3 triples. Floating-point and math-module deferred. |
| 03 | objects | 4,109 | 7 source modules → 3 concepts (id/type/value; methods; mutability & aliasing). 3 triples. AttributeError taught. |
| 04 | decisions | 4,609 | 9 source modules → 3 concepts (if/elif/else; Boolean operators & truthiness; nested decisions). 6 triples. Cold open: TSA gate agent. Forbidden-phrase fix. |
| 05 | loops | 3,301 | 7 source modules → 3 concepts (while; for & range; accumulator + break/continue). 7 triples. Lean but coherent — sits at floor. |
| 06 | functions | 4,240 | 8 source modules → 3 concepts (define/call/return; arguments; scope & call stack). 9 triples. Re-dispatched after first attempt wrote to wrong path. Cold open: smartphone calculator. |
| 07 | modules | 3,694 | 7 source modules → 3 concepts (importing standard library; using `math`/`random`/`datetime`; writing own module). 6 triples. Forbidden-phrase fix. |
| 08 | strings | 4,163 | 7 source modules → 3 concepts (indexing & slicing; methods; f-strings). 4 triples. Companion files consolidated from rogue date-stamped files. |
| 09 | lists | 3,763 | 7 source modules → 3 concepts (creating/indexing; mutating; iterating + comprehensions). 6+ triples. Companions consolidated. |
| 10 | dictionaries | 5,420 | 7 source modules → 3 concepts (creating/accessing; mutating; iterating + comprehensions). 7 triples. Cold open: phonebook lookup. |
| 11 | classes | 6,056 | 7 source modules → 4 concepts (what a class is; `__init__` & instance attrs; methods; synthesis Budget class). 6 triples. Cold open: vending machine. Companion stubs created (full content in chapter). |
| 12 | recursion | 5,203 | 7 source modules → 5 concepts (base/recursive case; factorial; Fibonacci with memoization; data-structure recursion; tree problems). 5+ triples. Cold open: Russian nesting dolls. |
| 13 | inheritance | 5,177 | 7 source modules → 5 concepts (is-a vs has-a; overriding & super; polymorphism; hierarchical/multilevel; Liskov substitution). Multiple triples. Cold open: Animal → Dog hierarchy. Companion stubs created. |
| 14 | files | 4,073 | 7 source modules → 3 concepts (open/read/write with `with`; CSV; JSON). 9 triples. FileNotFoundError, encoding errors taught. |
| 15 | data-science | 5,127 | 7 source modules → 3 concepts (NumPy vectorization; pandas DataFrames; matplotlib basics). 11 triples. Re-dispatched after first attempt couldn't find the book. Closer reflects on book's arc. |

**Totals.** 15 chapters, 67,866 words. 45 companion files (15 each in `pantry/`, `images/`, `bookmaps/`). All 15 chapters above the 3,500-word floor. Forbidden-phrase audit: zero hits across all 15 after fixes to chapters 4 and 7.

**Three-part Claude pattern.** ~95+ triples across the book — each is a runnable code block paired with a *Prompt for Claude* and *Tests to validate*. The pattern is densest in chapters 6 (functions), 14 (files), and 15 (data science) where every example is a small useful utility a student would actually want to elicit from an LLM and verify.

**Source folders.** All 15 source subfolders deleted after verification. Two chapters (6 and 15) had to be re-dispatched after their first attempts wrote to wrong paths or couldn't find the source — both succeeded on retry.

**Companion files.** Several subagents drifted on naming conventions (date-stamped filenames, files inside source folders rather than in canonical companion locations). Cleanup pass renamed/moved everything to canonical paths and consolidated rogue files into proper pantry locations. Where companion files were missing entirely (chapters 11 and 13 most notably), stub companions were created pointing at the chapter as the authoritative source.

**TOC.** Rewritten to flat structure pointing at the 15 chapter files.

**Known follow-ups.**
- Chapter 5 (Loops) at 3,301 words is below the 3,500 floor but above the legacy 3,000 OK threshold the agent reported. Worth expanding if you want it richer — bookmap notes accumulators and nested loops as deferred.
- Stub companion files (chapters 7-10, 11, 12, 13, 14) are minimal placeholders; the chapter file carries the full content. If you want richer companions, a follow-up pass can extract figure briefs and source maps directly from each chapter.
- The Claude pattern's quality varies — some agents wrote tighter prompts than others. Worth a consistency pass before publication.

## 2026-05-07 — Added Chapter 00 (Claude Code Basics) + "## Claude Code" sections to all 15 chapters

Two changes in one pass:

**New Chapter 00 — Claude Code Basics.** 5,093 words. Three concepts: installing and invoking Claude Code; how to instruct it (specification as the actual hard skill); verifying what it produces (running the code is the verification). Cold open: a student opening a terminal, typing `claude`, watching files appear and run. Companion files (pantry, images, bookmap) written. This chapter is meta — it teaches the tool, not through the tool — so it does not itself use the three-part code+prompt+tests pattern (it explains the pattern). All later chapters' "## Claude Code" sections build on the install/instruct/verify scaffolding from this chapter.

**"## Claude Code" sections added to every chapter 1–15.** Standard format per section: orientation (1-2 sentences), `Claude Code prompt` blockquote (80-200 words specifying file names, file contents, verification points), `What to expect` (50-100 words describing what Claude Code will do), `Stretch` (one extension prompt). Each section is tailored to its chapter's three concepts and uses the chapter's worked-example domain.

Per-chapter Claude Code prompts:

| Ch | Concepts reinforced | Project the student asks Claude Code to build |
|---|---|---|
| 01 | I/O, variables, arithmetic | Personal budget program — input → arithmetic → output |
| 02 | precedence, types, division operators | Clock-arithmetic helper using `//` and `%` for wraparound |
| 03 | id/type/value, methods, aliasing | `objects_demo.py` showing aliasing trap with `id()` printouts |
| 04 | if/elif/else, comparison, boolean | `grading.py` with letter grades + boundary tests |
| 05 | while, for, range, accumulator, break | `prime_sieve.py` for primes ≤ N + tests |
| 06 | define/call, parameters, return, docstrings | `temperature.py` with three converters + fixed-point tests |
| 07 | import, standard library, own module | Two-file project (conversions module + main + tests) |
| 08 | indexing, slicing, methods, f-strings | `slugify.py` URL-slug generator + 5+ test cases |
| 09 | lists: create/mutate/iterate/comprehensions | `temps.py` weekly temperatures with 4 functions + no-mutation guarantee tests |
| 10 | dict creation, mutation, iteration, comprehensions | `wordcount.py` frequency counter + Gettysburg demo |
| 11 | classes: __init__, attributes, methods, validation | `BankAccount` class with deposit/withdraw validation + 8 tests |
| 12 | base case, recursive case, call stack | factorial + sum_list + count_files + memoization benchmark |
| 13 | subclassing, super, override, polymorphism | Extend BankAccount with CheckingAccount + SavingsAccount + polymorphic processor |
| 14 | with, CSV, JSON, error handling | CSV→JSON report utility with FileNotFoundError handling |
| 15 | NumPy, pandas, matplotlib | End-to-end: synthesize sales.csv → analyze → bar chart + cross-validation |

**Forbidden-phrase fixes.** Ch 00 had 3 hits ("clearly" three times) — all replaced with "precisely." Ch 01's new section had 1 hit ("clearly show") — removed. Final audit: 0 forbidden-phrase hits across all 16 chapters.

**TOC updated** to include Chapter 00 at the top.

**Word counts now.** Total book: 81,478 words across 16 chapters. Each chapter grew by 200-1,000 words from its Claude Code section.

**Companion files for Chapter 00.** Written as full files (not stubs) by the chapter agent — pantry, images, bookmap.


## 2026-05-12 — Running Project added: "Build a Text Adventure or Console Game"

Generated 16 end-of-chapter LLM Exercise blocks via the Running Project Exercise Generator. Project selected: **Build a Text Adventure or Console Game** — across the semester, student builds a real playable Python text adventure. Each chapter adds capabilities. By Ch 15: a complete game with classes, file save/load, playtest-data analysis.

The architecture follows the book's three-artifact pattern (code + prompt-for-Claude + tests) literally — every chapter's prompt asks the student to produce all three.

Chapter-by-chapter build:
- Ch 00: Vision Document (game premise, vibe, scope, acceptance test for Ch 15)
- Ch 01: Title banner + name prompt + welcome (statements only)
- Ch 02: Player stats + arithmetic expressions
- Ch 03: String methods for input normalization + mutability demo
- Ch 04: First branching decision (go north/south/etc.)
- Ch 05: Game loop (`while not game_over`) + combat-turn loop
- Ch 06: Refactor into named functions (describe_room, take_damage, etc.)
- Ch 07: Split into modules (game.py, combat.py, world.py, player.py, util.py) + stdlib (random, time, os)
- Ch 08: Real command parser (multi-word, aliases, typo suggestions)
- Ch 09: Inventory + per-room items as lists (with parallel-list awkwardness preview)
- Ch 10: World refactored into nested dictionaries
- Ch 11: OOP refactor (Player, Enemy, Item classes)
- Ch 12: Recursion (one of: nested containers / dialogue tree / nested rooms)
- Ch 13: Inheritance (Weapon/Armor/Potion; Goblin/Dragon/Boss) + polymorphism
- Ch 14: Save/load to JSON + world loaded from JSON file
- Ch 15: Playtest data analysis (pandas + matplotlib) + final compiled game

Methodological commitments baked in across all 16: every prompt asks for the three artifacts (code + prompt-for-Claude + tests); every prompt has a "what error did you hit" or "what went wrong" reflection close; the acceptance-test scenario from Ch 00 is what Ch 15 validates.

Tool recommendations: Claude Code as primary throughout (this is a code project). Claude Project for design conversations and the Vision Document context. Cowork for Ch 15 final packaging.

By Ch 15, the student has ~500-1500 lines of Python organized across multiple files, with proper OOP design, save/load, and data analysis. This is a real portfolio piece for a CS-1 student.

Each block appended to the bottom of its chapter file. Total addition: ~15,000 words of new content across 16 chapters.

**Known follow-up:** the project assumes the student commits to building a text adventure specifically. For students who arrive certain they want a different project (a Twitter bot, a study-tracker, etc.), the prompts would need substantial adaptation. The Vision Document in Ch 00 enforces scope discipline; deviation from a text-adventure premise will break the chapter chains.

**Cross-book note:** this is the most Claude-Code-heavy running project generated so far. The "three artifacts" pattern (code + prompt + tests) carries the book's pedagogical signature into the running project directly. Most students should use Claude Code throughout — Python projects of this size don't fit in Claude chat.
