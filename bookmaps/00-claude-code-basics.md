# Bookmap: Claude Code Basics

## Chapter-level explanation

**Chapter 0 — Claude Code Basics**

This chapter is the prerequisite for every "Claude Code" section in Chapters 1–15. It is not a Python concept chapter — it is a tool chapter. It teaches the *how* of using Claude Code (an LLM-powered command-line agent from Anthropic) to ask for code, watch it run, and verify it works.

The chapter opens mid-scene: a student opens a terminal, types `claude`, asks for a script, and watches the code appear and execute in real time. The cold open makes the tool feel immediate and tangible.

Three concepts follow:

1. **Installing and invoking.** How to get Claude Code on your machine (npm) and how to start a session. The trade-off between global install (convenient) and local install (controlled). A common pitfall (npm permissions) and two fixes.

2. **How to instruct effectively.** The core trade-off: verbose instructions take longer to type but reduce iteration; minimal instructions are quick but often require follow-up. The anatomy of a good instruction (what to create, what it should do, how to test, what to show). Named examples of vague vs. specific.

3. **Verifying what comes back.** An LLM can *describe* what code will do. Actual execution is verification. Three levels: code runs without error, output matches expectations, edge cases are handled. Reading tracebacks. Test-driven specification.

The three concepts build toward one loop: **specify → execute → verify**. Every subsequent chapter in the book uses this loop. By Chapter 15, it's automatic.

---

## Source-level notes

**This chapter has no source modules.** It is written from scratch, based on:
- Documented Claude Code features: npm installation, terminal invocation, file/bash/edit capabilities, permission prompts
- Anthropic's official documentation (claudecode.com, npm package registry)
- Pedagogical principles from Feynman (mechanism from first principles, admission of uncertainty) and Attenborough (scene-first, specific detail)

No copyrighted textbooks were mined for this chapter. The pedagogy is original to this workshop.

---

## Ideas harvest (teachable moments and angles for future work)

### On the tool itself

1. **The three-part Claude pattern applied to Claude Code itself.** The lesson of the book (code + prompt + test) is meta-relevant: every Claude Code session is itself a three-part artifact. This could be a connected essay on recursive pedagogy.

2. **The permission loop as a model of human-in-the-loop AI.** Claude Code asks before deleting. This is not friction; it's a model of how to integrate LLMs into human workflows. Future chapters might explore this more deeply.

3. **Specification as a transferable skill.** Writing a good prompt to Claude Code is the same skill as writing a good spec to a human collaborator or a downstream codebase. This chapter teaches it through code, but it transfers.

### On verification

4. **The mismatch between prediction and execution.** An LLM says "this will output X," but it outputs Y. This is the central moment where students learn that description is not truth. This tension could be the opening for a deeper essay on epistemic trust and testing.

5. **Three levels of verification.** The chapter names them: runs without error, output matches expectations, edge cases work. A future chapter might formalize this into a testing hierarchy.

### On the classroom experience

6. **Installation as a barrier to entry.** npm permissions, version conflicts, PATH issues — these are the first obstacles many students face. A booklet or skill specifically on "fixing npm and Node.js problems" could save hours of classroom time.

7. **The loop as confidence-building.** Students who run a Claude Code session and see their code work develop confidence fast. Future pedagogical research could explore whether Claude Code changes time-to-competence in CS-1.

### On the system design

8. **Claude Code as an onboarding tool.** Before teaching syntax, why not teach the tool? This chapter does it, but it could be even more prominent: "By the end of today, you'll have installed a coding assistant and used it to write real Python."

9. **The terminal as a first interface.** Many CS-1 students have never opened a terminal. Claude Code demands it. This chapter treats the terminal as a given, but it could be expanded to teach terminal basics first.

---

## Structural patterns used

1. **Cold open mid-scene.** Chapter opens with a student using Claude Code, not with a definition. Sensory detail: cursor, text streaming, file appearing.

2. **Trade-off naming.** Every concept section names the core trade-off explicitly. Global vs. local install. Verbose vs. minimal specification. Speed vs. verification.

3. **Etymology on first use.** Technical terms (npm, PATH, agent, REPL, traceback) are glossed in plain English on first mention.

4. **Worked examples with all four moves.** Instruction → response → verification → judgment. The student sees the full cycle.

5. **Common misconceptions by category.** Each section ends with a clear list of what students often get wrong.

6. **Integration section as a complete scenario.** Temperature converter: specify, run, verify, read the code. Full loop in action.

---

## Connections to Chapter 1 (Statements)

Chapter 1 opens with `print("Hello, world!")` — the canonical first statement. Chapter 0 teaches the *tool* that will produce and verify that statement. The flow is natural: "Here's the tool. Now let's use it to understand statements."

The "Claude Code" section of Chapter 1 could ask:

```
create a file called hello.py that contains print("Hello, world!").
run it and show me the output.
```

Then Chapter 1 explains what `print()` does, and why the output appears. Claude Code has already shown it works; now we understand why.

---

## Voice consistency check

This chapter uses:
- **Cold opens mid-scene.** ✓ Opens with terminal, cursor, typing, execution.
- **Etymology on technical terms.** ✓ npm, PATH, agent, REPL, traceback all glossed.
- **Trade-offs explicit.** ✓ Global/local, verbose/minimal, speed/verification all named.
- **First-person sparingly.** ✓ No "I" in this chapter; "you" when inviting the reader to try things; "we" when figuring something out together.
- **Specificity of reference.** ✓ Links to nodejs.org and github.com/nvm-sh/nvm. References specific npm and Claude Code features.
- **Grounded examples.** ✓ Real terminal output, real file names (hello.py, greet.py, temp_convert.py), real formulas (F to C conversion).
- **Mechanism from first principles.** ✓ Explains what npm is, what PATH is, how npm installs globally.
- **Named scale shifts.** ✓ One student's hello world → millions of developers using AI assistants. One script → production systems.
- **Forbidden phrases absent.** ✓ Checked for: fascinating, remarkable, obviously, clearly, in many ways, raises important questions, stakeholders, robust, scalable, "It is worth noting", "One could argue", "It seems as though", "Some would say", "revolutionary". None found.

---

## Assessment ideas for future

### What a student should be able to do after this chapter

- [ ] Install Claude Code and verify with `claude --version`
- [ ] Start a Claude Code session with `claude` and type an instruction
- [ ] Write a specific instruction with all four parts: what to create, what it should do, how to test, what to show
- [ ] Run code with Claude Code and verify the output matches expectations
- [ ] Catch a bug by examining output, then ask Claude Code to fix it
- [ ] Read a Python traceback and identify the line and error type
- [ ] Distinguish between "Claude Code says it will output X" and "Claude Code actually outputs X"

### Formative assessment (during chapter)

- After Concept 1: Can the student install Claude Code? Type `claude` and see the prompt?
- After Concept 2: Can the student write a specific instruction with all four parts?
- After Concept 3: Can the student run code, read the output, and spot a mismatch?

### Summative assessment (end of chapter)

- Exercise warm-up: Install and run. (Procedural.)
- Exercise application: Write a welcome program. (Specification and execution.)
- Exercise synthesis: Write a function with three test cases. (Full loop.)
- Exercise challenge: Write a function with error handling. (Edge cases and verification.)

---

