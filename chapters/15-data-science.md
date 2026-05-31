# Chapter 15 — Data Science
*The loop you no longer have to write.*

The spreadsheet is open on your screen. One hundred thousand rows. Twelve columns. Five years of sales data — every transaction your company logged. You need to know: which months had the best revenue? Where are the outliers? What's the trend?

In the old way, you would write a loop. Iterate through every row. Keep a running total. Compare values by hand. The code would be slow, and if you needed to do it again next week with fresh data, you'd rewrite the loop.

There is a different way. You open a library called pandas. You read the entire CSV into memory with one command. You group by month with one more line. You plot it with a third. The loop is gone. The manual comparison is gone. The computer does the arithmetic on a million rows and hands you a result in milliseconds.

Three libraries make this possible. **NumPy** provides fast arrays and vectorized arithmetic. **pandas** provides tables — structured data with column names, filtering, grouping, and cleaning. **matplotlib** draws the result. This chapter is not about machine learning. Machine learning comes downstream. This is the foundation: loading data, checking it, understanding what it says. This is where professionals spend eighty percent of their time.

---

## NumPy arrays: speed through uniformity

A Python list of a million numbers works — you can loop through it, append to it, check membership. But each element is its own Python object, separately allocated in memory, carrying type metadata and reference counts. Arithmetic on that list is arithmetic on a million tiny objects.

A NumPy *array* — formally an *ndarray*, for n-dimensional array — imposes a constraint: every element must be the same type. In exchange, all elements sit contiguous in memory, packed tight, and arithmetic operations run in compiled C code rather than Python loops. The tradeoff is inflexibility for speed. For a million numbers, it's the right trade.

```python
import numpy as np

temperatures = [32, 45, 68, 72, 81]
temps = np.array(temperatures)
print(temps)             # [32 45 68 72 81]
print(temps.dtype)       # int64
```

The critical insight is **vectorization**: an arithmetic operation applied to the array applies to every element simultaneously, without a Python loop:

```python
# List approach — explicit loop
temps_list = [32, 45, 68, 72, 81]
celsius_list = []
for t in temps_list:
    celsius_list.append((t - 32) * 5/9)

# NumPy approach — no loop
temps_array = np.array([32, 45, 68, 72, 81])
celsius_array = (temps_array - 32) * 5/9
print(celsius_array)
# [ 0.          7.22222222 20.         22.22222222 27.22222222]
```

The expression `(temps_array - 32) * 5/9` doesn't subtract 32 from one element and move on. It subtracts 32 from the entire array at once, in C, then multiplies the result by 5/9 at once. On a million-element array, this is hundreds of times faster than the Python loop.

**Broadcasting** extends this to operations between an array and a scalar — Python stretches the scalar to match the array's shape automatically:

```python
temps = np.array([32, 45, 68, 72, 81])
print(temps + 10)    # [42 55 78 82 91] — 10 added to every element
```

Indexing and slicing work exactly as with lists — zero-based, with negative indices counting from the end, and `[start:end]` slices excluding the end index:

```python
print(temps[0])      # 32
print(temps[-1])     # 81
print(temps[1:4])    # [45 68 72]
```

NumPy also provides aggregate methods directly on the array:

```python
print(temps.mean())   # 59.6
print(temps.max())    # 81
print(temps.min())    # 32
print(temps.sum())    # 298
```

The constraint is real. You cannot append to a NumPy array without creating a new one. You cannot mix types. For flexible collection and mutation, use Python lists. For arithmetic on fixed-size numeric data, use NumPy.

<!-- → [TABLE: NumPy creation functions — four rows: `np.array(list)` / creates array from Python list / preserves original types; `np.zeros(n)` / array of n zeros / dtype float64 by default; `np.ones(n)` / array of n ones / dtype float64 by default; `np.random.rand(n)` / n random floats in [0, 1) / useful for simulation.] -->

---

## pandas DataFrames: tabular data as a first-class citizen

NumPy is flat and homogeneous. Real data is structured and mixed: a date column, a string column for product names, an integer column for units sold, a float column for revenue, and some cells that are simply missing.

A pandas **DataFrame** is a table — rows and columns, like a spreadsheet or a database result. Each column is a **Series** (a one-dimensional array with an index), and different columns can have different types. The DataFrame tracks column names, manages alignment, and provides a vocabulary for the operations that data actually requires: filter, group, aggregate, reshape.

### Loading and inspecting

The most common entry point is `pd.read_csv()`:

```python
import pandas as pd

df = pd.read_csv('sales.csv')

print(df.head())         # First five rows
print(df.shape)          # (n_rows, n_columns) as a tuple
print(df.info())         # Column names, types, non-null counts
print(df.describe())     # Summary statistics for numeric columns
```

`.head()` is always the first thing you call on unfamiliar data — it tells you what you're working with before you do anything that might go wrong. `.info()` is second: it shows you whether you have missing values and whether the types are what you expected (dates read as strings, for instance, need conversion).

### Selecting and filtering

```python
# One column returns a Series
revenue = df['revenue']

# Multiple columns return a DataFrame
subset = df[['product', 'revenue']]

# Filter rows by condition
big_sales = df[df['revenue'] > 1000]

# Filter by two conditions — use & not 'and'
big_widgets = df[(df['revenue'] > 1000) & (df['product'] == 'Widget')]
```

The double square brackets for multiple columns — `df[['col1', 'col2']]` — is one of the syntax triples that every pandas user hits once. Single brackets return a Series; double brackets return a DataFrame. The condition inside `df[condition]` is a boolean Series the same length as the DataFrame; pandas keeps only the rows where the condition is `True`.

### Groupby: the most important operation

Groupby is the operation that turns raw rows into answers. You specify a column to group on, pick a column to aggregate, and choose an aggregation function:

```python
# Total revenue by month
monthly = df.groupby('month')['revenue'].sum()

# Average revenue by product
avg_by_product = df.groupby('product')['revenue'].mean()

# Count of transactions per salesperson
counts = df.groupby('salesperson').size()
```

`.groupby('month')` splits the DataFrame into groups — one group per unique month value. `['revenue']` selects the column to aggregate. `.sum()` adds up the values in each group. The result is a Series with group labels as the index.

This one operation — group, select, aggregate — handles most of the questions data analysts actually ask: total by category, average by region, count by period.

<!-- → [TABLE: Common aggregation functions after groupby — five rows: `.sum()` / total of values in group; `.mean()` / average; `.count()` / number of non-null values; `.size()` / number of rows including nulls; `.min()` / `.max()` / minimum/maximum.] -->

### Handling missing data

Real data has gaps. A sensor went offline for an hour. A customer skipped a required field. pandas represents missing values as `NaN` (Not a Number). Three operations handle the most common cases:

```python
# See where values are missing
print(df.isnull().sum())    # Count of nulls per column

# Drop rows with any missing value
df_clean = df.dropna()

# Fill nulls with a specific value or the column's mean
df['age'] = df['age'].fillna(df['age'].mean())
df['email'] = df['email'].fillna('no-email')
```

`.isnull()` returns a boolean DataFrame; `.sum()` on it gives counts per column. `.dropna()` returns a new DataFrame with null rows removed — it does not modify the original. `.fillna()` replaces nulls with the given value, which might be a constant, the column mean, the previous value, or whatever the domain calls for.

Missing data requires a decision: drop the row, fill it, or flag it. The right choice depends on why it's missing and what you're computing. If you drop every row with a missing email, you may be excluding a systematic group of customers who don't provide emails. If you fill every missing age with the mean, you're injecting noise into a distribution. These are not Python questions. They're questions about the data and the analysis.

---

## matplotlib: turning numbers into pictures

Numbers in a table are hard to reason about at scale. A line chart showing revenue climbing from January to June, then dropping in July, communicates in seconds what a column of twelve numbers doesn't. matplotlib is Python's standard plotting library — verbose compared to some alternatives, but universal and controllable.

### Line plot

```python
import matplotlib.pyplot as plt

months = ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun']
revenue = [15000, 18200, 21500, 20100, 25300, 28900]

plt.plot(months, revenue, marker='o', linestyle='-', color='blue', linewidth=2)
plt.title('Monthly Revenue')
plt.xlabel('Month')
plt.ylabel('Revenue ($)')
plt.grid(True)
plt.show()
```

`plt.plot(x, y)` draws a line connecting the points. `marker='o'` puts a circle at each point. `grid=True` draws reference lines. `plt.show()` renders the plot — in a regular Python script, execution pauses until you close the window; in a Jupyter notebook, the plot appears inline.

### Bar chart

```python
products = ['Widget A', 'Widget B', 'Gadget', 'Gizmo']
sales = [450, 320, 280, 190]

plt.bar(products, sales, color='green', alpha=0.7)
plt.title('Sales Count by Product')
plt.xlabel('Product')
plt.ylabel('Number of Sales')
plt.xticks(rotation=45)
plt.show()
```

`plt.bar(x, height)` draws vertical bars. `alpha=0.7` is opacity — 1.0 is fully opaque, 0.0 is invisible. `plt.xticks(rotation=45)` tilts the x-axis labels so they don't overlap when labels are long.

### Multiple plots

```python
fig, (ax1, ax2) = plt.subplots(1, 2, figsize=(12, 4))

ax1.plot(months, revenue, marker='o', color='blue')
ax1.set_title('Monthly Revenue')
ax1.set_ylabel('Revenue ($)')

ax2.bar(products, sales, color='green')
ax2.set_title('Sales by Product')
ax2.set_ylabel('Count')

plt.tight_layout()
plt.show()
```

`plt.subplots(1, 2)` creates one row of two plots. `figsize=(12, 4)` sets the overall size. The returned `ax1` and `ax2` are axes objects — you call `.plot()`, `.bar()`, `.set_title()` etc. directly on them. `plt.tight_layout()` adjusts spacing so labels don't overlap.

<!-- → [TABLE: Core matplotlib functions — six rows: `plt.plot(x, y)` / line chart; `plt.bar(x, height)` / vertical bar chart; `plt.barh(y, width)` / horizontal bar chart; `plt.scatter(x, y)` / scatter plot; `plt.hist(data)` / histogram; `plt.subplots(r, c)` / multiple plots in a grid.] -->

matplotlib does not produce data — it displays it. If you need the aggregated numbers (monthly totals, category means), compute them with pandas first, then pass the results to matplotlib. The analysis and the visualization are separate steps.

---

## The workflow, assembled

These three libraries are not independent tools. They form a workflow: load and clean with pandas, compute with NumPy or pandas, visualize with matplotlib. Here is the pattern that repeats in every real analysis:

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

# 1. Load
df = pd.read_csv('purchases.csv')
print(f"Loaded {df.shape[0]} rows, {df.shape[1]} columns")

# 2. Inspect
print(df.head())
print(df.isnull().sum())

# 3. Clean
df['date'] = pd.to_datetime(df['date'])
df = df.dropna(subset=['amount'])

# 4. Compute
avg_by_product = df.groupby('product')['amount'].mean()
avg_by_product = avg_by_product.sort_values(ascending=False)

# 5. Visualize
plt.figure(figsize=(10, 5))
plt.bar(avg_by_product.index, avg_by_product.values, color='steelblue')
plt.title('Average Spending by Product')
plt.xlabel('Product')
plt.ylabel('Average ($)')
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()

# 6. Report
print(f"\nTop product: {avg_by_product.index[0]}")
print(f"Average spend: ${avg_by_product.values[0]:.2f}")
```

Acquisition → inspection → cleaning → analysis → visualization → reporting. This is not Python-specific. It is the data analysis lifecycle. The libraries change; the workflow doesn't.

---

## Quick reference

| Function | What it does |
|---|---|
| `np.array(list)` | Create a NumPy array from a Python list |
| `np.zeros(n)` | Array of n zeros |
| `np.random.rand(n)` | Array of n random floats in [0, 1) |
| `pd.read_csv(filename)` | Load CSV into a DataFrame |
| `df.head(n)` | First n rows (default 5) |
| `df.shape` | (n_rows, n_columns) |
| `df.info()` | Column names, types, null counts |
| `df.describe()` | Summary statistics |
| `df['col']` | Select one column (Series) |
| `df[['c1', 'c2']]` | Select multiple columns (DataFrame) |
| `df[condition]` | Filter rows by boolean condition |
| `df.groupby('col')['col2'].sum()` | Group and aggregate |
| `df.isnull().sum()` | Count nulls per column |
| `df.dropna()` | Drop rows with any null |
| `df.fillna(value)` | Replace nulls with value |
| `plt.plot(x, y)` | Line plot |
| `plt.bar(x, y)` | Bar chart |
| `plt.title(s)` | Set title |
| `plt.show()` | Render the plot |

---

## LLM Exercise — Chapter 15: Data Science (Text Adventure Project) — FINAL CLOSER

**Project:** Text Adventure — final integration.
**What you're building this chapter:** playtest-data analysis with pandas/matplotlib + the compiled, polished game.
**Tool:** **Claude Code** + **Cowork** for final packaging.

---

**The Prompt:**

```
Chapter 15 — the closing chapter — of my Text Adventure project.
Chapter 15 covered: NumPy arrays; pandas DataFrames; basic
plotting with matplotlib; the data-analysis cycle.

Two parts.

PART A — Playtest Data Analysis.

1. **Add session logging.** When a game session ends (win or
   lose), append a row to `data/playtest_log.csv` with:
   - timestamp
   - player_name
   - rooms_visited (count)
   - items_collected (count)
   - enemies_defeated (count)
   - total_turns
   - outcome (win / lose / quit)
   - final_health, final_gold

2. **Play 10-20 sessions.** (You can play yourself + have 1-2
   classmates / friends play.) Each session appends a row.

3. **Analysis script — `analyze_playtests.py`.** Use pandas
   to load the CSV, compute:
   - Average turns per session.
   - Win rate.
   - Distribution of rooms visited (which rooms get visited
     most? least? — name reveals balance issues).
   - Distribution of enemies defeated.
   - Correlation between rooms_visited and outcome (do thorough
     explorers win more?).

4. **Matplotlib visualizations.** Produce 3-4 plots:
   - Histogram of total turns per session.
   - Bar chart of room-visit counts.
   - Win-rate over time (line plot — are later sessions higher
     win rates as players learn?).
   - Scatter plot of items_collected vs. outcome (color-coded).

   Save plots to `data/playtest_analysis.png`.

PART B — Final Game Integration.

5. **Write `README.md`** — the project's user-facing documentation:
   - One-line description (from the Vision Document).
   - How to play (installation, run command, basic commands).
   - The acceptance-test scenario from Ch 00, fully walked
     through.
   - Architecture: which files exist, what each does.
   - Credits and citations (any AI assistance acknowledged honestly).

6. **Final acceptance test.** Run the Ch 00 acceptance test
   scenario from end to end. Does it pass? Document any
   discrepancies.

7. **Final reflection (300-500 words):**
   - What 16 chapters taught you that the textbook alone
     wouldn't have.
   - The biggest bug you found and fixed.
   - The feature you cut for scope.
   - One thing you'd refactor if you had another semester.

End with: package the project for sharing. Verify someone with
Python 3 + pandas + matplotlib installed could clone the repo
and play the game.
```

---

**What this produces:** Playtest-data analysis (with charts) + a polished, documented, deployed game. The deliverable is real software the student keeps and can share.

**Connection to previous chapters:** Every prior chapter contributes — the final game uses every concept from variables to inheritance to file I/O to pandas.

**Preview of next chapter:** There is no next chapter — this is the close. What's next is your game: a real Python project you built, with mechanism arrows on the code, tests that verify, and analysis on real playtest data. Share it. Iterate. Build the next one.

---

## AI Wayback Machine

**John Tukey** invented exploratory data analysis — the framework underlying every modern Python data-science workflow.

**Run this:**

```
Who is John Tukey, and how does their work connect to data science we covered in this chapter? Keep it to three paragraphs. End with the single most surprising thing about their career or ideas.
```

→ Search **"John Tukey"** on Wikipedia.

**Now make the prompt better.** Try one of these:

- Ask it to apply John Tukey's ideas to a small Python program you'd write.
- Add a constraint: "Answer including criticisms or limits of John Tukey's framework."

What changes? What gets better? What gets worse?

---

## Exercises

**Warm-up**

1. Create a NumPy array from the list `[10, 20, 30, 40, 50]`. Divide every element by `3.28084` (feet to meters) in a single vectorized operation. Print the result and verify the first element is approximately 3.048.

2. Write code that reads a CSV file named `data.csv` into a pandas DataFrame. Use `.head()`, `.shape`, and `.info()` to inspect it. What are the column names and types?

3. Given a DataFrame with columns `['name', 'age', 'salary']`, write code to filter rows where `age > 30` and `salary < 100000`. Use a single boolean expression.

**Application**

4. You have a DataFrame with columns `['date', 'category', 'amount']`. Group by `category` and sum the amounts. Sort the result in descending order and print the top three categories.

5. A DataFrame has a column `'email'` with some null values. Write code that: (a) counts how many rows have a missing email, (b) creates a cleaned version with those rows dropped, (c) creates a filled version where missing emails are replaced with the string `'no-email'`.

6. Using matplotlib, create a line plot of the temperatures `[68, 72, 70, 75, 77]` over the days `['Mon', 'Tue', 'Wed', 'Thu', 'Fri']`. Add markers at each point, a title, axis labels, and a grid.

**Synthesis**

7. Load a CSV of sales data. (a) Display the first ten rows and data types. (b) Filter to sales where `amount > 500`. (c) Group by `region` and sum the amounts. (d) Create a bar chart of regional totals with the region with the highest total visually apparent.

8. Write a function `analyze_data(filename)` that: loads the CSV; identifies missing values in each column; drops rows with a missing `price` column; groups by `category` and computes the mean price; returns the result as a DataFrame. Test it on a sample CSV.

**Challenge**

9. You have a CSV with columns `date`, `product`, `units_sold`, `price_per_unit`. (a) Load the data and convert `date` to a datetime type. (b) Create a new column `revenue = units_sold * price_per_unit` — this is a vectorized operation on two columns. (c) Group by `product` and sum `revenue`. (d) Plot the result as a horizontal bar chart using `plt.barh()`. (e) Report the top-selling product and its total revenue.

10. Find a real dataset (Kaggle, data.gov, or your own data). Load it with pandas. Identify three columns of interest. Use `.isnull().sum()` and `.describe()` to characterize the data quality and distribution. Create two visualizations that answer a genuine question about the data. Write three to four sentences describing what you found — not what the code did, but what the data says.
