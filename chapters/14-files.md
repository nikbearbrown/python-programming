# Chapter 14 — Files

## TL;DR

Files let your code talk to the disk — reading data that persists across sessions, writing results that outlive the program. The machinery is simple: `open()` to connect, read/write methods to move bytes, `close()` to flush the buffer. The complication is that disk I/O fails in ways memory operations do not. This chapter teaches you to write to the disk reliably and read from it carefully, handle the errors that come, and parse the two most common formats: plain text and CSV.

## Three title options

1. **Files and Persistence: How Code Reaches the Disk**
2. **Reading and Writing: The Bridge Between Memory and Storage**
3. **Files as Sequences: From Buffer to Disk, Error by Error**

---

## Cold open

You are exporting three months of sensor data from a research station — temperature, humidity, timestamps, 47,000 readings in all. The data lives in a database. Your job is to write it to a CSV file so an analyst in another city can graph it. You open the editor, type `outfile = open("sensor_readings.csv", "w")`, and your code begins to flow from the fast memory of your computer to the slow, permanent storage of the disk.

This is different from everything you have done so far.

When you print to the console, you see the result instantly. When you assign a variable, it sits in RAM. When you do arithmetic, the calculation happens and you use the result before you blink. But now you are handing your data to a file system. The file system has its own schedule. It buffers your writes. It schedules disk access. It makes promises about durability that you have to keep.

You finish writing 47,000 rows to the CSV. Your code ends. Your program exits. And nothing has touched the disk yet — your data is still in the buffer, waiting. That is why the next line of code is not `print()` but `outfile.close()`. The close tells the operating system: *write what you are holding to the disk right now, and make it stick*.

This chapter is about that boundary — the place where your program meets the filesystem, where RAM hands off to the permanent world. It teaches you how to read from files you have written, how to write to files you have created, how to handle the errors that come when files do not exist or have the wrong format, and how to parse CSV so you can turn spreadsheet data into Python structures your code can work with.

## Learning objectives

By the end of this chapter you will be able to:

- **Open** a file for reading or writing and understand what mode means.
- **Read** from a file using `read()`, `readline()`, and `readlines()`, and understand the difference.
- **Write** to a file using `write()`, understand buffering, and know why `close()` is required.
- **Specify** a file path on your operating system (Mac, Linux, Windows) correctly.
- **Parse** a CSV file into a Python list-of-lists data structure.
- **Handle** the common exceptions that come from file I/O: `FileNotFoundError`, `ValueError`, `IndexError`.
- **Use** the `with` statement (context manager) to guarantee that a file closes even when an exception fires.

## Prerequisites

- Loops (`for` and `while`).
- Lists and list indexing.
- String methods like `.strip()` and `.split()`.
- Exception handling with `try` and `except`.
- How your operating system organizes folders (file paths).

---

## Concept 1 — Opening, reading, closing: the three-step dance

A file is a sequence of bytes stored on your computer's disk, named and organized into folders. When you want to use that data, Python does not just hand it to you. You have to ask for it explicitly, using the `open()` function.

### What open() does

The `open()` function takes a filename (and optionally a mode) and returns a **file object** — a bridge between your Python program and the bytes on the disk.

```python
infile = open("sensor_data.txt")
```

This line tells Python: *find a file named sensor_data.txt in the same folder as my code, connect to it, prepare to read it*. The file object `infile` is now your interface to those bytes.

By default, `open()` opens a file for reading only. If the file does not exist, `open()` raises a `FileNotFoundError`.

### Reading the whole file: read()

The simplest way to read is to grab everything at once.

```python
infile = open("log.txt")
contents = infile.read()
print(contents)
infile.close()
```

The `read()` method reads the entire file and returns it as a single string. The string contains newline characters (`\n`) wherever the file had line breaks. Then you close the file to release the connection.

> **Prompt for Claude.** "Write a Python program that opens a file named 'quote.txt', reads its contents into a string, prints the string, and closes the file."

> **Tests to validate.**
> - If quote.txt contains "The truth is\nlike a lion;\nyou do not have to defend it.\n— it defends itself.", the output should print all three lines.
> - The program should not crash if run twice in a row.
> - After running, the file should close (test this by modifying the file and running again without restarting Python).

### Reading line by line: readline() and readlines()

Often you do not want the whole file at once. You want one line at a time, or all lines as a list.

`readline()` reads one line (from the current position to the next `\n`), returns it as a string (including the `\n`), and moves the cursor forward. Call it again and you get the next line. Useful for large files you want to process in chunks.

`readlines()` reads all lines and returns them as a list. Each element is a line, including the trailing `\n`.

```python
# Example: count lines in a file
infile = open("poem.txt")
lines = infile.readlines()
infile.close()
print(f"The file has {len(lines)} lines.")
```

One wrinkle: the newline character at the end of each line usually needs to go away. Use `.strip()` to remove leading and trailing whitespace, including the newline.

```python
infile = open("grades.txt")
lines = infile.readlines()
infile.close()

for line in lines:
    line = line.strip()  # Remove \n and any spaces
    print(f"Grade: {line}")
```

> **Prompt for Claude.** "Write a Python program that (1) opens 'numbers.txt', (2) uses readlines() to read all lines, (3) for each line, strip the newline character, convert to an integer, and add to a list, (4) compute the average of the list, and (5) close the file. Then print the average."

> **Tests to validate.**
> - If numbers.txt contains "5\n10\n15\n", the average should be 10.0.
> - If the file is empty, the program should handle division by zero (or test that this case is safe).
> - The program should work for files with leading/trailing spaces: "  5  \n10\n15\n".

### Trade-off: speed vs. memory

`read()` is fast and simple. It is also dangerous for gigabyte-sized files — you would load the entire gigabyte into RAM at once. `readline()` or reading in a loop is slower but uses constant memory. Choose based on file size.

```python
# For small files (< 10 MB)
contents = infile.read()

# For large files
for line in infile:
    process(line)
```

Notice the second example: you can iterate over a file object directly. Python treats a file as an iterable, yielding one line at a time.

### Closing a file

When you call `infile.close()`, you tell Python: *flush any pending reads, release the file handle, let the operating system know I am done*. Once closed, the file object is useless — calling `.read()` on it raises an error.

Close is not polite; it is required. A file left open consumes a system resource (a file descriptor). Open too many files and your program cannot open any more. On some systems, unsaved changes to a file may not hit the disk until the file is closed.

---

## Concept 2 — Writing and modes: creating and modifying files

If reading is about pulling data from the disk, writing is about pushing it there.

### Opening a file for writing: mode='w'

By default, `open()` opens a file in read mode (`'r'`). To write, pass a second argument:

```python
outfile = open("output.txt", 'w')
outfile.write("Hello, disk.")
outfile.close()
```

The mode `'w'` means *write*. Important: if the file already exists, `'w'` erases it completely and starts over. This is destructive. Use it only when you mean to overwrite.

The `write()` method takes a string and writes it to the file buffer. Note: `write()` does not add a newline. If you want line breaks, you must insert them explicitly using `\n`.

```python
outfile = open("output.txt", 'w')
outfile.write("Line 1\n")
outfile.write("Line 2\n")
outfile.write("Line 3")  # No newline at the end
outfile.close()
```

If you read output.txt, you get three lines. The third has no trailing newline.

### Appending: mode='a'

If you want to add to the end of an existing file without erasing it, use append mode:

```python
outfile = open("log.txt", 'a')
outfile.write("New entry\n")
outfile.close()
```

Mode `'a'` opens the file, moves the cursor to the end, and lets you `write()`. The file is not erased.

### The three modes summarized

| Mode | Meaning | If file exists | If file does not exist |
|------|---------|---|---|
| `'r'` | Read | Open it | Raise `FileNotFoundError` |
| `'w'` | Write (erase) | Erase it and start over | Create it |
| `'a'` | Append | Open at end | Create it |

### Buffering: why close() is mandatory

When you call `outfile.write("Hello")`, Python does not write immediately to the disk. It stores the data in a **buffer** — a piece of temporary memory. The operating system batches writes to the disk for efficiency.

When you call `outfile.close()`, the buffer is flushed: every byte sitting in memory is written to the disk and the connection closes.

If your program crashes or your computer loses power before `close()` executes, the data in the buffer is lost. The file on disk will be incomplete or not exist.

This is why you must **always close a file after writing**. No exceptions.

```python
# Careful: if the program crashes after write() but before close(),
# the file will be empty
outfile = open("important.txt", 'w')
outfile.write("Important data")
# ... CRASH HERE? Data lost.
outfile.close()
```

> **Prompt for Claude.** "Write a Python program that (1) opens 'favorite_quotes.txt' in write mode, (2) writes three separate lines to it (use a loop if you like), (3) closes the file, (4) opens it again in read mode, (5) reads and prints all lines with `.readlines()`, and (6) closes it again."

> **Tests to validate.**
> - After running, the file should exist and contain exactly three lines.
> - The second run should print the same three lines (confirming the file was not erased on the second open because we used read mode).
> - If you comment out `outfile.close()` in the write section, the file may be empty or incomplete when you try to read it (run the test to see this happen).

---

## Concept 3 — File paths, CSV parsing, and error handling

So far we have assumed files are in the same folder as our code. Real life is messier.

### File paths

A file lives in a folder. That folder lives in another folder. The chain of folders from the root of your disk to the file is the **path**.

On Mac and Linux, paths use forward slashes and start from the root `/`:

```python
infile = open("/users/alice/desktop/data.txt")
```

On Windows, paths use backslashes (or forward slashes in Python):

```python
# Backslashes (escape them with another backslash)
infile = open("c:\\users\\alice\\desktop\\data.txt")

# Forward slashes (cleaner)
infile = open("c:/users/alice/desktop/data.txt")
```

Relative paths (without a leading `/` or drive letter) are relative to the folder where your Python script lives:

```python
infile = open("data.txt")          # Same folder as script
infile = open("data/input.txt")    # In a subfolder
infile = open("../data/input.txt") # One folder up, then into data
```

### CSV: comma-separated values

CSV is a simple, widespread format for tabular data. Each line is a record. Fields within a line are separated by commas.

```
Title,Author,Pages
1984,George Orwell,268
Jane Eyre,Charlotte Bronte,532
Walden,Henry David Thoreau,156
```

Python does not parse CSV automatically. You have to read the file, split each line by commas, and build a list-of-lists.

```python
infile = open("books.csv")
rows = infile.readlines()
infile.close()

data = []
for row in rows:
    row = row.strip()  # Remove \n
    fields = row.split(",")  # Split by comma
    data.append(fields)

print(data)
# [['Title', 'Author', 'Pages'],
#  ['1984', 'George Orwell', '268'],
#  ['Jane Eyre', 'Charlotte Bronte', '532'],
#  ['Walden', 'Henry David Thoreau', '156']]
```

Notice that all fields are strings, even numbers. If you want to do arithmetic on the Pages column, you must cast to `int()`.

**Etymology note:** CSV stands for *comma-separated values*. The name is literal: commas separate the values. If a field itself contains a comma, you run into ambiguity. Real CSV parsers (like Python's `csv` module) handle this using quotation marks. For this chapter, assume no commas inside fields.

> **Prompt for Claude.** "Write a Python program that (1) opens 'student_grades.csv' (assumed to exist with lines like 'Alice,92,88,95'), (2) reads all lines, (3) for each line, strips the newline, splits by comma, extracts the first element (name) and the last three (three test scores), (4) computes the average of the three scores, (5) prints the name and average, and (6) closes the file. Example output: 'Alice: 91.67'."

> **Tests to validate.**
> - If the CSV has "Bob,75,80,85\n", the average should be 80.0.
> - The program should handle the header line gracefully (skip it or error checking).
> - Scores should be converted to `float` for averaging, not left as strings.

---

## Error handling: when files disappoint

File I/O is where Python's error handling becomes essential. Reading and writing can fail in ways memory operations cannot.

### FileNotFoundError

The most common error: you ask for a file that does not exist.

```python
infile = open("missing.txt")  # Raises FileNotFoundError
```

Wrap it in a try-except:

```python
try:
    infile = open("grades.txt")
    lines = infile.readlines()
    infile.close()
except FileNotFoundError:
    print("File not found. Please check the filename.")
```

### ValueError and IndexError during parsing

When you parse CSV or other structured data, the file format might be wrong. A line might be missing fields, or a field that should be a number might be text.

```python
# File contents:
# 5 apples
# 3 oranges
# broken line

infile = open("fruit.txt")
for line in infile:
    parts = line.strip().split()
    qty = int(parts[0])  # ValueError if parts[0] is not a number
    item = parts[1]      # IndexError if the line has no second word
    print(f"{qty} {item}")
infile.close()
```

When the program hits the line "broken line", it crashes. Handle it:

```python
try:
    infile = open("fruit.txt")
    for line in infile:
        parts = line.strip().split()
        qty = int(parts[0])
        item = parts[1]
        print(f"{qty} {item}")
    infile.close()
except (ValueError, IndexError) as error:
    print(f"Format error: {error}")
    infile.close()
except FileNotFoundError:
    print("File not found.")
```

---

## Integration: the context manager (with statement)

There is a gotcha in the code above. If `int(parts[0])` raises a `ValueError`, the `except` block prints a message. But `infile.close()` inside the `except` happens only for that specific error. If some other error fires, `close()` might not run.

The proper solution is the **context manager**, invoked with the `with` statement.

```python
with open("fruit.txt") as infile:
    for line in infile:
        parts = line.strip().split()
        qty = int(parts[0])
        item = parts[1]
        print(f"{qty} {item}")
```

The `with` statement automatically closes the file when the indented block ends, even if an exception fires. You do not need to call `close()` explicitly. If an exception is raised inside the block, the file closes before the exception propagates.

This is called **context management**. The `open()` function returns an object that manages the context of file access: it sets up the file on entry and tears it down on exit.

In production code, always use `with`. It is shorter and safer.

```python
# Before: manual close (error-prone)
infile = open("data.txt")
data = infile.read()
infile.close()

# After: with statement (safe)
with open("data.txt") as infile:
    data = infile.read()
```

> **Prompt for Claude.** "Write a Python program using a `with` statement to: (1) open 'numbers.txt' for reading, (2) read all lines, (3) for each line, strip whitespace and convert to int, (4) sum all the integers, and (5) print the sum. The program should NOT call close() explicitly."

> **Tests to validate.**
> - If numbers.txt contains "10\n20\n30\n", the sum should be 60.
> - If numbers.txt does not exist, a `FileNotFoundError` should be raised (and not caught in this version).
> - After the `with` block exits, the file should be closed (verify by trying to read from the file object afterward — it should raise an error).

---

## Worked example — processing student exam scores from CSV

A file `exam_scores.csv` contains:

```
Name,Midterm,Final
Alice,85,92
Bob,78,88
Charlie,92,91
```

Write a program that reads this file, computes the average of each student's two scores, and prints the name with the average.

```python
with open("exam_scores.csv") as infile:
    lines = infile.readlines()

# Skip the header
data_lines = lines[1:]

for line in data_lines:
    line = line.strip()
    fields = line.split(",")
    name = fields[0]
    midterm = int(fields[1])
    final = int(fields[2])
    average = (midterm + final) / 2
    print(f"{name}: {average:.1f}")
```

Output:

```
Alice: 88.5
Bob: 83.0
Charlie: 91.5
```

**Why this works:** 
1. `with open()` guarantees the file closes.
2. `readlines()` gives us all lines at once.
3. `lines[1:]` skips the header.
4. `split(",")` breaks each line into fields.
5. `int()` converts string scores to numbers.
6. The formula computes the average.
7. `:.1f` formats the output to one decimal place.

**What could go wrong:**
- If the file does not exist, a `FileNotFoundError` is raised before we ever parse.
- If a line has fewer than three fields, `fields[2]` raises an `IndexError`.
- If `fields[1]` is not a valid integer (e.g., "absent"), `int()` raises a `ValueError`.
- If the file is empty, `lines[1:]` is also empty, and the loop does not run.

To make this production-ready, you would wrap it in try-except:

```python
try:
    with open("exam_scores.csv") as infile:
        lines = infile.readlines()
    
    if len(lines) < 2:
        print("File has no data rows (only header or empty).")
    else:
        data_lines = lines[1:]
        for line in data_lines:
            line = line.strip()
            fields = line.split(",")
            name = fields[0]
            midterm = int(fields[1])
            final = int(fields[2])
            average = (midterm + final) / 2
            print(f"{name}: {average:.1f}")

except FileNotFoundError:
    print("exam_scores.csv not found.")
except ValueError as e:
    print(f"Invalid number in file: {e}")
except IndexError as e:
    print(f"Line format error (missing field): {e}")
```

---

## Claude Code — Building a small CLI utility with CSV and JSON

The patterns you have learned — `with`, CSV parsing, error handling — come together in a real task: reading student grades from a CSV file, analyzing them, and writing a summary report as JSON.

This is where Claude Code becomes a partner. You describe what you want, Claude Code builds it, and you test it. The conversation teaches you the machinery while the code teaches you the patterns.

### The task

You have a file `students.csv` with student names and test scores:

```
Name,Score
Alice,92
Bob,78
Charlie,85
Diana,91
```

Write a program `report.py` that:
1. Reads the CSV using the `csv` module (not manual string splitting).
2. Computes the average score and identifies the highest scorer.
3. Writes the summary to a JSON file `report.json`.
4. Handles the case where `students.csv` does not exist — a clear error message, not a stack trace.

Then write a test file `test_report.py` that creates a temporary CSV, runs the logic, parses the resulting JSON, and asserts the computations are correct.

### How to work with Claude Code

**Step 1: Ask Claude Code to scaffold the main program.**

Prompt: "Create a Python script `report.py` that reads a CSV file `students.csv` (with columns Name and Score), computes the average score and the name of the highest scorer, and writes a dictionary to `report.json` with keys 'average' and 'highest_name'. Use the `csv` module for parsing. If `students.csv` does not exist, print a helpful error message and exit gracefully."

Claude Code will write `report.py`. It will import `csv` and `json`, open the file with `with`, read the CSV rows, compute statistics, and write JSON using `json.dump()`. Look for the use of the `with` statement — that is the pattern in action.

**Step 2: Ask Claude Code to write the test.**

Prompt: "Write a test file `test_report.py` using pytest. Create a temporary `students.csv` with 5 test records, run the report logic, parse the output JSON, and assert that the average is correct and the highest-name is correct. Use the tmp_path fixture for the temporary file."

Claude Code will write `test_report.py`. Watch how the test uses `with` to open the temp CSV, and how `json.load()` parses the result.

**Step 3: Ask Claude Code to add error handling.**

Prompt: "Modify `report.py` to handle the case where `students.csv` does not exist. Show a friendly error message instead of a traceback. Also handle the case where a score is not a valid integer — print a message and skip that record."

Claude Code will add try-except blocks. You will see `FileNotFoundError` and `ValueError` caught by name.

**Step 4: Run the tests.**

Copy the files to your machine. Run `pytest test_report.py`. Watch it pass. Then delete `students.csv` and run the program again — the error message should appear, friendly and clear.

### What you learn by doing this

- `with` and `json.load()` / `json.dump()` are not isolated features. They work together in a real workflow.
- The `csv` module handles the splitting and quoting that manual `split(",")` cannot.
- Tests are not decoration. They are a way to verify that your error handling works and that you have not introduced a bug.
- A program that crashes with a stack trace is not production-ready. A program that prints a message and exits is.

### Why Claude Code here

You could write `report.py` by hand, line by line, using this chapter. But the point is not to test whether you can type. The point is to understand the pattern: open a file, read structured data, parse it into Python objects, compute with those objects, write the result to a new file, handle errors. Claude Code writes the scaffold fast. You read it, verify it uses the patterns you have learned, and understand how they compose into a real program.

---

## Common errors you will meet

**NameError: file not closed in your namespace.** You opened a file but never assigned it to a variable, so you cannot close it.

```python
# Wrong
open("data.txt").read()  # File never closes!

# Right
with open("data.txt") as f:
    data = f.read()
```

**FileNotFoundError: typo in filename or wrong path.** Check the filename and the working directory of your script.

**IndexError: accessing a field that does not exist.** Your CSV row has fewer fields than you expected. Use `print(fields)` to debug.

**ValueError: trying to convert a non-number string to int.** A field contains text, not a number. Check your assumptions about the file format.

**UnicodeDecodeError: the file is not plain text.** You tried to open a binary file (image, PDF, compiled code) as text. Files like these need special libraries.

---

## Chapter summary

You now understand the full cycle: opening a connection to a file, reading or writing data through that connection, handling errors when things go wrong, and closing the connection to commit your work to disk.

You know the three reading methods (`read()`, `readline()`, `readlines()`) and when to use each: `read()` for small files you want all at once, `readlines()` for small-to-medium files you want as a list of lines, and line-by-line iteration for huge files. You know the three modes (`'r'`, `'w'`, `'a'`) and what each does: read, write-and-erase, append. You know that `write()` does not add newlines — you must. You know that buffering means your data sits in memory until `close()` flushes it to disk.

You know CSV: a human-readable format where commas separate fields. You know how to parse it by reading lines, stripping whitespace, splitting by comma, and casting strings to numbers.

You know the errors that come from files: `FileNotFoundError` when the file is not there, `ValueError` and `IndexError` when the format is wrong. You know how to catch them with `try` and `except`. And you know the single best practice: use `with` to open files. It closes them automatically, even in the presence of exceptions.

The pattern you have learned here — open, read or write, close — appears in every language that touches the filesystem. Learn it once and you have it for life.

---

## What would change my mind

If Python added automatic resource cleanup without the need for `with` (or if every file were small enough to fit in memory at once), the pressure to use `with` would relax. As it stands, `with` is not a luxury — it is the difference between code that works reliably and code that leaks file handles.

---

## Still puzzling

The interaction between buffering, file descriptors, and the operating system deserves a deeper treatment than this chapter gives. Why does one `close()` guarantee data is on disk while another does not? When does the OS actually write? Those are systems questions, not Python questions, but they shape how safe your code is. For now, the rule is simple: close your files, and prefer `with`.

---

## Exercises

### Warm-up

**Exercise 14.1** *(LO: open and read)* Write a program that opens a file named `greeting.txt` and prints its entire contents using `read()`.

**Exercise 14.2** *(LO: readlines and iteration)* Write a program that opens `greeting.txt` and uses `readlines()` to print the number of lines in the file.

**Exercise 14.3** *(LO: write mode)* Write a program that creates a file named `myfile.txt` and writes three lines to it. After closing, reopen the file and verify the contents with `print()`.

### Application

**Exercise 14.4** *(LO: paths and CSV parsing)* You have a file `/tmp/students.csv` with contents:

```
Name,Age,Grade
Alice,20,A
Bob,19,B
Charlie,21,A
```

Write a program that reads this file (use the full path) and prints each student's name and grade.

**Exercise 14.5** *(LO: error handling)* Write a program that prompts the user for a filename, then tries to open and read it. If the file does not exist, catch the `FileNotFoundError` and print a helpful message. Allow the user to try again until a valid filename is given.

**Exercise 14.6** *(LO: append vs. write)* Write a program that opens `log.txt` in write mode, writes "Starting.\n", closes, then opens the same file in append mode and writes "Finished.\n". Finally, read the file and print its contents. What do you see?

### Synthesis

**Exercise 14.7** *(LO: CSV, errors, with statement)* Write a program that uses `with` to open `grades.csv` and processes it as follows: read all lines, skip the header, parse each line as `Name,Score1,Score2`, convert scores to integers, compute the average, and print `Name: Average`. Handle `FileNotFoundError`, `ValueError`, and `IndexError` gracefully.

**Exercise 14.8** *(LO: write structured data)* Write a program that opens `output.csv` in write mode, writes a header line `"Product,Price,Quantity"`, then writes three product records of your choice (as comma-separated strings). Close the file, reopen in read mode, and print all lines.

---

## Connections forward

Chapter 15 on data science will build on this foundation. You will learn pandas, a library that reads CSV (and other formats) into data structures optimized for analysis. When you load a CSV into a pandas DataFrame, the parsing you did by hand in this chapter happens under the hood. You will also meet NumPy arrays and matplotlib for visualization. All three libraries read from files and write to files. Knowing the mechanics of `open()`, `read()`, and `write()` will help you understand how they work and what can go wrong.

You will also encounter JSON, a structured format for storing nested data. Reading and writing JSON is similar to CSV — use `open()` and `with` — but the parsing is done by the `json` module rather than by hand. Once you understand the file I/O pattern, adding a new format is straightforward: open, parse, use.

---

## Tags

`file-io` `open-read-write` `csv-parsing` `error-handling` `context-managers` `buffering` `file-paths`

---

*Nik Bear Brown*
*Reading: Files are the membrane between your program and the world. Read carefully; write once.*
---

## LLM Exercise — Chapter 14: Files (Text Adventure Project)

**Project:** Text Adventure.
**What you're building this chapter:** save/load to JSON + load the world from an external JSON file.
**Tool:** **Claude Code**.

---

**The Prompt:**

```
Chapter 14 of my Text Adventure project. Chapter 14 covered:
reading and writing text files; open and context managers (with);
pathlib for cross-platform paths; JSON and CSV.

Add persistence. Two pieces.

1. **Save/Load Game State.**

   `save_game(player, world, save_path)`:
   - Build a save_data dict with player state (use vars(player)
     or a custom `to_dict()` method) + world state (which rooms
     are visited, what items are still where).
   - Write to JSON: `json.dump(save_data, f, indent=2)` inside a
     `with open(save_path, 'w') as f:` block.
   - Print confirmation.

   `load_game(save_path)`:
   - Read the JSON.
   - Reconstruct the Player object (Player.from_dict(data)).
   - Reconstruct the world state.
   - Return them.

   Add "save" and "load" commands to the game.

2. **Load the World From File.**

   Currently your world dict is hard-coded in Python. Move it to
   `data/world.json`:
   ```json
   {
     "tavern": {
       "name": "The Sticky Mug Tavern",
       "description": "...",
       "exits": {"north": "courtyard"},
       "items": ["mug of ale"]
     },
     ...
   }
   ```

   At game startup, load world from this file. The benefit:
   non-programmers can author content by editing the JSON; you
   don't need to redeploy.

3. **Pathlib usage.** Use `pathlib.Path` for cross-platform paths.
   `save_dir = Path("saves"); save_dir.mkdir(exist_ok=True)`.

4. **Multiple save slots.** Allow `save game1`, `load game1`,
   `save game2`, etc. List existing saves with a "saves"
   command.

5. **Error handling.** When loading a missing file: friendly
   message, don't crash. When the JSON is malformed: friendly
   message.

In addition to the code, produce the prompt + tests. Tests:
- Save and load a game; verify player.health, inventory, current
  room are preserved.
- Load a missing save: friendly error, no crash.
- Tamper with the JSON; load it; the error message is helpful.

End with: save files contain everything the player has done.
What user-data ethics consideration should you note? (Players
might share save files; the file shouldn't contain anything
private about them beyond what the game itself collects.)
```

---

**What this produces:** Save/load to JSON + world loaded from file. The game becomes persistent.

**Connection to previous chapters:** Ch 11's classes get `to_dict()` and `from_dict()` methods; Ch 10's world dict moves to disk.

**Preview of next chapter:** Chapter 15 — the closer. You'll add a playtest-data analysis. Track every game session's stats (rooms visited, items collected, time taken, win/lose) and produce a pandas/matplotlib analysis. Plus the final compiled game.


---

##  AI Wayback Machine
**Ken Thompson** was co-designed Unix and its file system — defining how programs work with files in every modern OS.

**Run this:**

```
Who is Ken Thompson, and how does their work connect to files we covered in this chapter? Keep it to three paragraphs. End with the single most surprising thing about their career or ideas.
```

→ Search **"Ken Thompson"** on Wikipedia.

**Now make the prompt better.** Try one of these:

- Ask it to apply Ken Thompson's ideas to a small Python program you'd write.
- Add a constraint: "Answer including criticisms or limits of Ken Thompson's framework."

What changes? What gets better? What gets worse?
