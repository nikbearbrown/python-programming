# Chapter 14 — Files
*The membrane between your program and the world — and why close() is not optional.*

Here is a program that runs perfectly and loses all its output:

```python
outfile = open("results.txt", "w")
outfile.write("47,000 sensor readings processed.\n")
# program ends
```

The string was written. You can verify this — Python executed `write()` without error. But if you open `results.txt`, it may be empty. The data never reached the disk. It was sitting in a buffer in memory when the program ended, and when the program ended, the buffer was discarded.

The fix is one line:

```python
outfile = open("results.txt", "w")
outfile.write("47,000 sensor readings processed.\n")
outfile.close()
```

`close()` flushes the buffer — forces every byte in memory to actually land on disk — and releases the file connection. Without it, the file is a rumor, not a record.

Everything in this chapter follows from this one property of disk I/O: writing to disk is not instantaneous. The operating system batches writes for efficiency. Your code has to explicitly tell it when the batch is complete. Understanding why `close()` exists — and why you should almost always replace it with a `with` statement that closes automatically — is the thread running through the whole chapter.

---

## The Three-Step Pattern: Open, Use, Close

Every file operation in Python follows the same pattern:

1. **Open** the file with `open()`, which returns a file object.
2. **Use** the file object to read or write.
3. **Close** the file with `.close()`, which flushes and releases.

```python
infile = open("log.txt")        # open
contents = infile.read()         # use
infile.close()                   # close
print(contents)
```

The `open()` function takes a filename and returns a **file object** — a bridge between your Python program and the bytes on disk. By default it opens for reading. If the file doesn't exist, it raises `FileNotFoundError` immediately.

### Three Ways to Read

Once you have a file object, you have three reading options:

`read()` pulls the entire file into a single string:

```python
with open("poem.txt") as f:
    entire_text = f.read()
```

`readlines()` reads all lines into a list, each line including its trailing `\n`:

```python
with open("grades.txt") as f:
    lines = f.readlines()
# ['Alice,92\n', 'Bob,88\n', 'Carol,95\n']
```

Iterating over the file object directly reads one line at a time, which is the right choice for large files:

```python
with open("huge_log.txt") as f:
    for line in f:
        process(line.strip())
```

The `with` statement in all three examples is the context manager — more on that shortly. The `.strip()` call removes the trailing `\n` that `readlines()` and iteration include on every line.

<!-- → [TABLE: three reading methods compared — rows: read(), readlines(), iteration — columns: method, returns, best for, memory cost — student should see at a glance which to reach for based on file size and whether they need the whole thing at once] -->

**The size tradeoff.** `read()` is simple but loads the entire file into RAM. A 2 GB log file loaded with `read()` uses 2 GB of memory. Iterating line by line uses constant memory regardless of file size. For files under a few megabytes, use whichever is clearest. For larger files, always iterate.

---

## Writing: Modes and What They Destroy

To write, you pass a **mode** to `open()`:

```python
outfile = open("output.txt", "w")    # write mode
outfile.write("first line\n")
outfile.write("second line\n")
outfile.close()
```

Mode `"w"` is **write** — it creates the file if it doesn't exist and **erases it if it does**. This is not recoverable. The previous contents are gone the moment you open with `"w"`.

Mode `"a"` is **append** — it opens the file and positions the cursor at the end, leaving existing content intact:

```python
outfile = open("log.txt", "a")
outfile.write("New entry\n")
outfile.close()
```

Mode `"r"` is **read** (the default) — it opens for reading, raises `FileNotFoundError` if the file doesn't exist.

| Mode | If file exists | If file doesn't exist |
|------|---------------|----------------------|
| `"r"` | Open for reading | `FileNotFoundError` |
| `"w"` | **Erase it**, then write | Create it |
| `"a"` | Append to end | Create it |

The destructive behavior of `"w"` is the most common cause of accidental data loss in file I/O. Before you open anything with `"w"`, ask: do I actually want to erase whatever was there?

Two things `write()` does not do that beginners expect:
1. It does not add a newline. If you want line breaks, you write `"\n"` explicitly.
2. It does not write to disk immediately. The data goes into the buffer first.

---

## The Buffer and Why close() Is Not Optional

When you call `outfile.write("something")`, Python hands the string to the operating system's I/O buffer — temporary memory that collects writes before sending them to the physical disk. The OS does this for efficiency: disk writes are thousands of times slower than memory operations, so batching many small writes into one large disk operation is a significant performance win.

The consequence: your data may not be on disk until you call `close()`. If your program ends, the OS crashes, or the power goes out before `close()` runs, the buffer is discarded and the file is incomplete or empty.

`close()` issues two instructions to the OS: flush the buffer (write everything in memory to disk right now) and release the file descriptor (the system resource that represents the open connection). Both matter.

<!-- → [FIGURE: diagram showing the buffer between program memory and disk — write() puts data in buffer, close() flushes buffer to disk — two scenarios: program ends normally (close() called, buffer flushed, data safe) and program crashes (close() not reached, buffer discarded, data lost) — student should see why close() is required, not optional] -->

---

## The context manager: `with` Replaces Every `close()`

The problem with explicit `close()` calls is that they can be skipped. An exception between `open()` and `close()` will jump over the `close()` entirely, leaving the file open indefinitely.

The `with` statement solves this:

```python
with open("data.txt") as infile:
    contents = infile.read()
```

When the indented block ends — whether normally or because an exception fired — Python automatically closes the file. You never call `close()`. You cannot forget it. The file always closes.

This is **context management**: `open()` returns an object that knows how to set up (open the connection) and tear down (flush and close) the file access. The `with` statement calls the setup on entry and the teardown on exit, guaranteed.

In production code, always use `with`. The only time you'd manage `open()` and `close()` manually is when the file needs to stay open across multiple operations separated by other work — which is rare and usually a sign the design needs rethinking.

---

## File Paths

`open()` takes a filename. That filename is either a **relative path** (interpreted relative to the directory where your script is running) or an **absolute path** (starting from the root of the filesystem).

```python
open("data.txt")               # same folder as your script
open("inputs/data.txt")        # in a subfolder called inputs
open("../data.txt")            # one folder up
open("/home/alice/data.txt")   # absolute path, Mac/Linux
open("C:/Users/Alice/data.txt") # absolute path, Windows
```

On Windows, backslashes in paths need to be doubled (`"C:\\Users\\..."`) because `\` is an escape character in Python strings. Forward slashes work on all platforms and are the safer choice.

The `pathlib` module provides a cleaner path interface that handles the platform differences automatically:

```python
from pathlib import Path

data_path = Path("inputs") / "data.txt"   # builds the path correctly
with open(data_path) as f:
    contents = f.read()
```

`Path` objects work directly with `open()`, and the `/` operator builds paths in a platform-agnostic way.

---

## CSV: Parsing Tabular Data

CSV — comma-separated values — is the simplest widespread format for tabular data. One row per line. Fields separated by commas.

```
Name,Midterm,Final
Alice,85,92
Bob,78,88
Charlie,92,91
```

Python gives you two ways to parse CSV. The manual approach uses the string tools you already know:

```python
with open("exam_scores.csv") as f:
    lines = f.readlines()

header = lines[0]         # skip it
for line in lines[1:]:
    fields = line.strip().split(",")
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

The `csv` module handles edge cases the manual approach misses — fields containing commas inside quotes, varying whitespace, different line endings:

```python
import csv

with open("exam_scores.csv", newline="") as f:
    reader = csv.DictReader(f)
    for row in reader:
        name = row["Name"]
        average = (int(row["Midterm"]) + int(row["Final"])) / 2
        print(f"{name}: {average:.1f}")
```

`DictReader` uses the header row as keys, so each row is a dictionary. The name `row["Name"]` is clearer than `fields[0]`, and the module handles quoting and escaping automatically. For simple files with no embedded commas, manual splitting is fine. For anything that might come from a spreadsheet, use `csv.DictReader`.

<!-- → [TABLE: manual split vs csv module — rows: manual split, csv.reader, csv.DictReader — columns: how fields are accessed, handles quoted commas?, header handling, best for — student should see when to reach for each approach] -->

---

## Errors That Come From Files

File I/O fails in ways that memory operations do not. Three errors account for most failures:

**`FileNotFoundError`** — the file doesn't exist at the path you specified.

```python
try:
    with open("missing.txt") as f:
        contents = f.read()
except FileNotFoundError:
    print("File not found. Check the filename and path.")
```

**`ValueError`** — a field that should be a number isn't.

```python
try:
    score = int(fields[1])   # "absent" can't convert to int
except ValueError as e:
    print(f"Invalid score in row: {e}. Skipping.")
    continue
```

**`IndexError`** — a line has fewer fields than expected.

```python
try:
    name, midterm, final = fields[0], fields[1], fields[2]
except IndexError:
    print(f"Malformed row (too few fields): {line.strip()}")
    continue
```

The pattern for robust CSV parsing: wrap the file open in a `try`/`except FileNotFoundError`, and wrap the per-line field access in a `try`/`except (ValueError, IndexError)` inside the loop. This way a single bad row doesn't abort the entire file.

```python
try:
    with open("grades.csv") as f:
        lines = f.readlines()
except FileNotFoundError:
    print("grades.csv not found.")
    raise SystemExit(1)

for line in lines[1:]:   # skip header
    try:
        fields = line.strip().split(",")
        name = fields[0]
        score = int(fields[1])
        print(f"{name}: {score}")
    except (ValueError, IndexError) as e:
        print(f"Skipping malformed row: {line.strip()!r} — {e}")
```

<!-- → [FIGURE: flowchart of robust file parsing — outer try/except catches FileNotFoundError around the open(), inner try/except inside the loop catches ValueError and IndexError per row, with "continue" on error to process the rest of the file — student should see the two-level error handling structure] -->

---

## A Complete Example: Exam Score Report

Read a CSV, compute per-student averages, write a summary file.

```python
import csv

try:
    with open("exam_scores.csv", newline="") as infile:
        reader = csv.DictReader(infile)
        results = []
        for row in reader:
            try:
                name = row["Name"]
                average = (int(row["Midterm"]) + int(row["Final"])) / 2
                results.append((name, average))
            except (ValueError, KeyError) as e:
                print(f"Skipping row {row}: {e}")
except FileNotFoundError:
    print("exam_scores.csv not found.")
    raise SystemExit(1)

with open("report.txt", "w") as outfile:
    outfile.write("Exam Score Report\n")
    outfile.write("=" * 20 + "\n")
    for name, avg in results:
        outfile.write(f"{name}: {avg:.1f}\n")

print(f"Report written. {len(results)} students processed.")
```

Everything in this program is a pattern from this chapter:
- `with` to guarantee both files close
- `csv.DictReader` for clean header-based access
- Per-row error handling that skips bad rows without aborting
- `"w"` mode to create the output file
- Explicit `"\n"` in every `write()` call

*What would change my mind:* The chapter recommends `csv.DictReader` over manual splitting for anything that might have embedded commas. If a format is guaranteed to be simple — no quoted fields, no commas in values, machine-generated — manual splitting is fine and avoids the import. The decision is about what you can guarantee about the data source, which changes per project.

*Still puzzling:* The interaction between Python's buffer, the OS buffer, and the actual disk write involves at least two layers of buffering that `close()` flushes through. Why `flush()` exists as a separate method — flushing the Python buffer but not necessarily the OS buffer — and when you'd need `os.fsync()` to guarantee disk durability, are systems questions that Python's documentation treats lightly. For most programs, `close()` is enough. For financial or medical records, the deeper question matters.

---

## Exercises

### Warm-up

**Exercise 14.1** *(Open and read.)*
Write a program that opens a file named `greeting.txt` and prints its entire contents using `read()`. Use a `with` statement.

**Exercise 14.2** *(readlines.)*
Write a program that opens `greeting.txt` and uses `readlines()` to print the number of lines in the file.

**Exercise 14.3** *(Write mode.)*
Write a program that creates `myfile.txt` and writes three lines to it using write mode. After the `with` block closes, reopen the file and print its contents. Verify that running the program a second time doesn't append — it overwrites.

### Application

**Exercise 14.4** *(CSV parsing.)*
A file `students.csv` contains:
```
Name,Age,Grade
Alice,20,A
Bob,19,B
Charlie,21,A
```
Write a program that reads this file using `csv.DictReader` and prints each student's name and grade.

**Exercise 14.5** *(Error handling on open.)*
Write a program that prompts the user for a filename, then tries to open and read it. Catch `FileNotFoundError` and print a helpful message. Let the user try again until a valid filename is given (use a `while True` loop that `break`s on success).

**Exercise 14.6** *(Append vs. write.)*
Write a program that opens `log.txt` in write mode, writes `"Starting.\n"`, closes it via `with`, then opens the same file in append mode and writes `"Finished.\n"`. Finally, read and print the file. What do you see? Change the second open to `"w"` mode and observe what happens.

### Synthesis

**Exercise 14.7** *(CSV with error handling and `with`.)*
Write a program that uses `with` to open `grades.csv` (format: `Name,Score1,Score2`). Skip the header. For each row, parse `Name`, convert scores to integers, compute the average, and print `Name: average`. Catch `FileNotFoundError`, `ValueError`, and `IndexError` — each should produce a specific, helpful message.

**Exercise 14.8** *(Write structured output.)*
Write a program that opens `output.csv` in write mode, writes a header `"Product,Price,Quantity"`, then writes three product records. Close the file via `with`, reopen in read mode, and print all lines. Confirm that running the program twice produces the same output (not doubled records).

### Challenge

**Exercise 14.9** *(Grade report pipeline.)*
Write a two-pass program: first read `scores.csv` (format: `Name,Score`) and compute the class average; then write a `report.txt` where each line is `"Name: Score (above/below average)"` depending on whether the student's score is above or below the class average. Handle all three error types. Use `with` throughout.

**Exercise 14.10** *(Log rotation.)*
Write a program that maintains a log file with at most 10 lines. On each run, append one new timestamped entry. After appending, read all lines — if there are more than 10, write the most recent 10 back to the file (overwriting the rest). Use `from datetime import datetime` to generate timestamps.

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

## AI Wayback Machine

**Ken Thompson** co-designed Unix and its file system — defining how programs work with files in every modern OS.

**Run this:**

```
Who is Ken Thompson, and how does their work connect to files we covered in this chapter? Keep it to three paragraphs. End with the single most surprising thing about their career or ideas.
```

→ Search **"Ken Thompson"** on Wikipedia.

**Now make the prompt better.** Try one of these:

- Ask it to apply Ken Thompson's ideas to a small Python program you'd write.
- Add a constraint: "Answer including criticisms or limits of Ken Thompson's framework."

What changes? What gets better? What gets worse?
