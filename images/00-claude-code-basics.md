# Images: Claude Code Basics

## Figure briefs for [FIGURE: ...] placeholders (none in this chapter)

This chapter contains no FIGURE placeholders. All explanations are text-based or code-based. If images are added in the future, they would serve these purposes:

### Potential figure 1: The Claude Code session flow

**Purpose:** Show the three-part loop visually.

**Content:**
- Left column: "Specify" — student types instruction at `>` prompt
- Middle column: "Execute" — Claude Code writes code, runs it
- Right column: "Verify" — output appears on screen, student checks it

**Label:** "The three-part Claude Code loop: specify, execute, verify."

---

### Potential figure 2: npm architecture

**Purpose:** Illustrate how npm installs Claude Code globally.

**Content:**
- npm registry (repository in the cloud)
- npm download process
- Global installation directory (e.g., `/usr/local/lib/node_modules`)
- PATH search (directories your shell searches)
- User typing `claude` in terminal, finding it in PATH

**Label:** "npm installs Claude Code globally: download → install → PATH search → terminal invocation."

---

### Potential figure 3: A sample traceback annotated

**Purpose:** Teach how to read Python error messages.

**Content:**
```
Traceback (most recent call last):
  File "script.py", line 3, in <module>
    result = 10 / denominator
             ^^^^^^^^^^^^^^^^
ZeroDivisionError: division by zero
```

With annotations:
- Arrow pointing to "line 3": "the line that failed"
- Arrow pointing to "ZeroDivisionError": "the type of error"
- Arrow pointing to the division: "the operation that caused it"

**Label:** "Reading a traceback: start at the error type, then trace back to the line and the operation."

---

### Potential figure 4: The specification trade-off

**Purpose:** Visual representation of the verbose vs. minimal trade-off.

**Content:**
- Left side: vague instruction ("write a function"), small arrow labeled "fast to type", large arrow labeled "slow to verify"
- Right side: specific instruction ("write a function that..."), large arrow labeled "slow to type", small arrow labeled "fast to verify"

**Label:** "The specification trade-off: more time typing → less time iterating."

---

## Terminal screenshot descriptions (for future reference)

If terminal screenshots are added:

### Screenshot 1: npm version check

```
$ npm --version
v20.11.0
```

Shows: npm is installed and working.

---

### Screenshot 2: Claude Code installation

```
$ npm install -g @anthropic-ai/claude-code
added 145 packages in 23s
```

Shows: Claude Code is installed globally.

---

### Screenshot 3: Claude Code session start

```
$ claude
Claude Code is ready.
>
```

Shows: Claude Code is running and waiting for input.

---

### Screenshot 4: A successful session

```
> create a file called hello.py that prints "hello" and run it
I'll create hello.py and run it.

[Creates file: hello.py]

print("hello")

[Running hello.py...]
hello
```

Shows: the full loop — instruction, file creation, execution, output.

---

## Diagram descriptions (for future reference)

### Diagram 1: The Claude Code architecture

**Components:**
- User (terminal)
- Claude Code agent (reads instruction, acts)
- File system (creates/edits files)
- Python interpreter (runs Python files)
- Output (shown in terminal)

**Flow:**
User → Claude Code → (creates file) → Python interpreter → Output → User

---

### Diagram 2: npm's role in the ecosystem

**Nodes:**
- npm registry (central repository)
- npm cli (command-line tool)
- Node.js (runtime)
- Claude Code (installed package)
- User's PATH (environment variable)
- Terminal (user interface)

**Connections:**
npm cli → npm registry (fetch) → Node.js installation → PATH setup → Terminal invocation

---

