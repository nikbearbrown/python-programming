# Chapter 15 — Data Science


## TL;DR

- The spreadsheet is open on your screen.
- You will practice Create NumPy arrays and understand when they are faster than Python lists; Load CSV data into pandas DataFrames and perform basic operations: selecting, filtering, grouping, handling missing values; Visualize data with line plots and bar charts using matplotlib.
- The chapter moves through Cold open, Learning objectives, Prerequisites, Why this chapter matters, and related ideas.

## Cold open

The spreadsheet is open on your screen. 100,000 rows. Twelve columns. Sales data from the last five years — every transaction your company logged. You click a cell. It highlights one number. You need to know: which months had the best revenue? Where are the outliers? What's the trend?

In the old world of programming, you would write a loop. Iterate through every row. Keep a running total. Compare values by hand. The code would be slow, the thinking would be manual, and if you needed to do it again next week with new data, you'd rewrite the same loop.

In the world we're about to enter — the one this chapter covers — you open a library called pandas. You read the CSV into a single command. You group by month with one more line. You plot it with a third. The loop is gone. The manual comparison is gone. The computer does the arithmetic once on a million rows and hands you a result in milliseconds.

This is what data science does: it takes the machinery of analysis — the sorting, filtering, calculating, drawing — and pulls it out of your code and into specialized tools. Python makes this possible because three libraries have become standard: **NumPy** for arrays and fast arithmetic, **pandas** for tabular data and cleanup, **matplotlib** for visualization. Learn these three, and you can work with real data. The data science lifecycle is something you'll encounter for the rest of your career, in fields as different as healthcare, business, politics, science.

This chapter is not machine learning. We're not building a model. This is the foundation underneath the foundation — the bread-and-butter work that comes before any model exists. This is where professionals spend 80% of their time: loading data, checking it, cleaning it, understanding what it's telling you.

### Learning objectives

By the end of this chapter you will be able to:

- **Create** NumPy arrays and understand when they are faster than Python lists.
- **Load** CSV data into pandas DataFrames and perform basic operations: selecting, filtering, grouping, handling missing values.
- **Visualize** data with line plots and bar charts using matplotlib.
- **Follow** the three-part Claude pattern (code, prompt, tests) as a learner and validator.

### Prerequisites

Chapters 1–14. You understand variables, loops, functions, dictionaries, lists, and how to read CSV files. You can write a function from scratch.

### Why this chapter matters

If you go into data science, data engineering, business analytics, economics, epidemiology, political science, or quantitative social science, you will open pandas on your first day. NumPy runs underneath half the scientific Python ecosystem. Matplotlib is how you show other people what your data says. This chapter closes out the Python sequence by showing you the real tools, the ones the world uses.

---

## Concept 1 — NumPy arrays: fast, flat arithmetic

Your Python list of 1,000,000 numbers is convenient. You can append to it. You can check membership with `in`. You can loop through it. But it's slow. Each element is its own tiny Python object, allocated separately in memory, with metadata attached.

A NumPy *array* — the abbreviation is *ndarray* (n-dimensional array) — throws away the flexibility. All elements must be the same type. All elements sit contiguous in memory, packed tight. In exchange, arithmetic on the whole array happens in compiled C code, not in Python loops.

Etymology: *array* comes from Old French, meaning *arrayed* — arranged, lined up in order.

### Creating an array

```python
import numpy as np

# Create an array from a Python list
temperatures = [32, 45, 68, 72, 81]
temps_array = np.array(temperatures)
print(temps_array)
# Output: [32 45 68 72 81]

# Create an array of zeros
zeros = np.zeros(5)
print(zeros)
# Output: [0. 0. 0. 0. 0.]

# Create an array of ones
ones = np.ones(3)
print(ones)
# Output: [1. 1. 1.]

# Create an array of random numbers
random_vals = np.random.rand(4)
print(random_vals)
# Output: [0.37... 0.81... 0.29... 0.95...]
```

> **Prompt for Claude.** "Write Python code using NumPy to create an array from the list [32, 45, 68, 72, 81]. Then create a second array of 5 zeros using `np.zeros()`. Print both."

> **Tests to validate.**
> - `temps_array.dtype` is `int64` or `int32` (integer type, not float)
> - `len(temps_array) == 5`
> - `temps_array[0] == 32`
> - `zeros.dtype` is `float64` (NumPy defaults to float for zeros)
> - `len(zeros) == 5`

### Vectorization: the speed trick

Here is the crucial insight. In a Python list, you would do this:

```python
temps_list = [32, 45, 68, 72, 81]
temps_celsius = []
for temp in temps_list:
    celsius = (temp - 32) * 5/9
    temps_celsius.append(celsius)
```

With a NumPy array, you do this:

```python
temps_array = np.array([32, 45, 68, 72, 81])
temps_celsius = (temps_array - 32) * 5/9
print(temps_celsius)
# Output: [0.         7.22222222 20.         22.22222222 27.22222222]
```

The arithmetic operator (`-`, `*`, `/`) applies to every element at once. This is called **vectorization** — the operation is not applied to each element separately, but to the entire vector (array) as a single unit. NumPy's vectorized operations are hundreds of times faster than Python loops on large arrays because they run in C, not Python.

Etymology: *vectorize* comes from Latin *vector*, meaning carrier. A vector carries a magnitude and direction; in programming, an array carries multiple values in one operation.

### Indexing and slicing

Arrays work like lists. You index from zero:

```python
temps = np.array([32, 45, 68, 72, 81])
print(temps[0])  # 32
print(temps[-1]) # 81
print(temps[1:4]) # [45 68 72] — slice from index 1 to 3 (4 excluded)
```

> **Prompt for Claude.** "Write Python code using NumPy to create an array [32, 45, 68, 72, 81]. Print the first element (index 0), the last element (using negative index -1), and slice from index 1 to 3 (inclusive)."

> **Tests to validate.**
> - `temps[0] == 32`
> - `temps[-1] == 81`
> - `len(temps[1:4]) == 3` and `temps[1:4][0] == 45`
> - Slicing does not modify the original array

### Broadcasting: extending operations across shapes

NumPy has a trick called **broadcasting**. If you have an array of shape (5,) and a scalar (single number), NumPy stretches the scalar to match the array's shape and applies the operation:

```python
temps = np.array([32, 45, 68, 72, 81])
offset = 10
result = temps + offset
print(result)
# Output: [42 55 78 82 91]
```

The single number 10 broadcasts to [10, 10, 10, 10, 10] invisibly, then adds element-wise.

> **Prompt for Claude.** "Write Python code using NumPy to create an array [32, 45, 68, 72, 81]. Add 10 to every element using broadcasting (no loop). Print the result."

> **Tests to validate.**
> - `result[0] == 42` (32 + 10)
> - `result[4] == 91` (81 + 10)
> - `len(result) == 5` (same shape as original)
> - Broadcasting does not modify the original array

### Common misconception

The trade-off is real. NumPy arrays are *not* flexible — you cannot append, insert, or change the dtype after creation without creating a new array. Python lists are more flexible. But when you're doing arithmetic on millions of rows, flexibility doesn't matter; speed does.

### Worked example — convert Fahrenheit to Celsius and find the mean

You have five temperature measurements in Fahrenheit and need to convert them to Celsius, then find the average.

```python
import numpy as np

# Measurements in Fahrenheit
fahrenheit = np.array([32, 45, 68, 72, 81])

# Convert to Celsius: (F - 32) * 5/9
celsius = (fahrenheit - 32) * 5/9

# Find the mean
mean_celsius = celsius.mean()

print(f"Celsius temps: {celsius}")
print(f"Mean: {mean_celsius:.2f}°C")
```

Output:
```
Celsius temps: [ 0.          7.22222222 20.         22.22222222 27.22222222]
Mean: 15.33°C
```

> **Prompt for Claude.** "Write Python code using NumPy to convert the Fahrenheit temperatures [32, 45, 68, 72, 81] to Celsius using the formula (F - 32) * 5/9. Then use `.mean()` to find the average temperature in Celsius. Print both the converted array and the mean, formatted to 2 decimal places."

> **Tests to validate.**
> - `celsius[0] == 0` (32°F is 0°C)
> - `celsius[2]` is approximately 20 (68°F is 20°C)
> - `mean_celsius` is approximately 15.33
> - `.mean()` returns a single NumPy scalar, not a list
> - The formula is correctly applied element-wise

---

## Concept 2 — pandas DataFrames: tabular data and groupby

NumPy arrays are fast and flat. But real data is messy and structured. You have columns: dates, product names, revenue amounts. Some rows have missing values. You need to select by condition ("show me only July"), group by category ("sum by month"), rename columns, handle missing data.

That's what pandas does. A **DataFrame** is a table — rows and columns, like Excel or a database table. Each column is a Series (a 1D array) and can have its own data type. You can group by a column, filter rows, rename columns, all with readable Python code.

Etymology: *DataFrame* comes from the name given to a structured collection of data in R, the statistical language. The frame is the structure holding the data.

### Loading and inspecting data

The most common operation is reading a CSV (comma-separated values) file:

```python
import pandas as pd

# Read a CSV file
df = pd.read_csv('sales.csv')

# Look at the first few rows
print(df.head())

# Get basic info: column names, types, missing-value counts
print(df.info())

# Get summary statistics: mean, min, max, quartiles
print(df.describe())
```

The `.head()` method shows the first five rows (or `n` rows if you pass a number). The `.info()` method tells you what data types each column has and whether any values are missing. The `.describe()` method computes mean, standard deviation, min, max, and quartiles for numeric columns.

### Selecting and filtering

Once the data is loaded, you select columns and rows by name or condition:

```python
df = pd.read_csv('sales.csv')

# Select one column (returns a Series)
revenue = df['revenue']

# Select multiple columns (returns a DataFrame)
subset = df[['product', 'revenue']]

# Filter rows: only entries where revenue > 1000
big_sales = df[df['revenue'] > 1000]

# Filter rows: only entries where product is 'Widget'
widgets = df[df['product'] == 'Widget']

# Combine conditions: revenue > 1000 AND product is 'Widget'
big_widgets = df[(df['revenue'] > 1000) & (df['product'] == 'Widget')]
```

Notice the double square brackets `df[['col1', 'col2']]` for multiple columns vs. single square brackets `df['col1']` for one. That syntax matters.

> **Prompt for Claude.** "Write Python code to load 'sales.csv' into a DataFrame. Select only the 'product' and 'revenue' columns into a new DataFrame called 'subset'. Then filter the original DataFrame to show only rows where revenue is greater than 1000."

> **Tests to validate.**
> - `isinstance(revenue, pd.Series)` (single column returns Series)
> - `isinstance(subset, pd.DataFrame)` (multiple columns return DataFrame)
> - `subset.shape[1] == 2` (exactly 2 columns)
> - `len(big_sales) < len(df)` (filtered has fewer rows)
> - `big_sales['revenue'].min() > 1000` (all filtered values meet condition)

### Groupby: the most important operation

You have sales data. You want to know: total revenue per month. Per product. Per salesperson. The `.groupby()` method does this:

```python
df = pd.read_csv('sales.csv')

# Group by month, sum the revenue for each month
monthly_revenue = df.groupby('month')['revenue'].sum()
print(monthly_revenue)
# Output:
# month
# January      15000
# February     18200
# March        21500
# dtype: int64

# Group by product, count how many sales of each
product_counts = df.groupby('product').size()

# Group by product, get the mean revenue per product
avg_by_product = df.groupby('product')['revenue'].mean()
```

`.groupby('column')` divides the DataFrame into groups based on the unique values in that column. Then you pick an aggregation: `.sum()`, `.mean()`, `.count()`, `.size()`, `.min()`, `.max()`. The result is a Series with the group labels as the index and the aggregated values as the data.

### Handling missing data

Real data has gaps. A customer didn't provide an email. A sensor went offline for a day. pandas uses `NaN` (Not a Number) to represent missing values:

```python
import pandas as pd
import numpy as np

# Create a DataFrame with some missing values
df = pd.DataFrame({
    'name': ['Alice', 'Bob', 'Charlie', 'Diana'],
    'age': [25, np.nan, 35, 22],
    'salary': [50000, 60000, np.nan, 55000]
})

# Identify missing values
print(df.isnull())
# Returns a Boolean DataFrame showing True where values are missing

# Drop rows with any missing value
df_clean = df.dropna()

# Fill missing values with the column mean
df['age'] = df['age'].fillna(df['age'].mean())
```

Common patterns:
- `.isnull()` returns True where data is missing.
- `.dropna()` removes rows containing any NaN.
- `.fillna(value)` replaces NaN with the given value (often the column mean or median).

> **Prompt for Claude.** "Write Python code to create a DataFrame with 4 rows: names [Alice, Bob, Charlie, Diana], ages [25, NaN, 35, 22], salaries [50000, 60000, NaN, 55000]. Use `.isnull()` to show where missing values are. Then use `.dropna()` to remove rows with any missing value."

> **Tests to validate.**
> - `.isnull().sum()` correctly identifies 2 missing values total
> - `df_clean = df.dropna()` has 2 rows (only Alice and Diana)
> - Original `df` still has 4 rows (dropna returns a copy, doesn't modify in place)
> - `df_clean['name'].tolist() == ['Alice', 'Diana']`

### Trade-offs: pandas richness vs. memory cost

NumPy arrays are lightweight. pandas DataFrames have overhead — column names, data types per column, indices, internal structure. A DataFrame uses more memory than a NumPy array of the same raw data. But the flexibility and readability usually win. For datasets that fit in memory (which is most of them in a classroom or small-to-medium business), pandas is the right choice.

### Worked example — load sales data, filter, group, and summarize

```python
import pandas as pd

# Load the data
df = pd.read_csv('sales.csv')

# Look at the first few rows
print("First 5 rows:")
print(df.head())

# How many rows and columns?
print(f"Shape: {df.shape}")  # (n_rows, n_columns)

# Which rows have revenue > 50000?
big_sales = df[df['revenue'] > 50000]
print(f"Sales > 50000: {len(big_sales)} rows")

# Sum revenue by month
monthly = df.groupby('month')['revenue'].sum()
print("\nMonthly revenue:")
print(monthly)

# What is the average sale amount per product?
avg_per_product = df.groupby('product')['revenue'].mean()
print("\nAverage revenue by product:")
print(avg_per_product)
```

> **Prompt for Claude.** "Write Python code to read 'sales.csv' into a pandas DataFrame. Display the first 5 rows with `.head()`. Filter the DataFrame to show only rows where the 'revenue' column is greater than 50000. Then group by 'month' and sum the 'revenue' column for each month. Print the results."

> **Tests to validate.**
> - `df` is a pandas DataFrame (type check with `isinstance(df, pd.DataFrame)`)
> - `len(df.head())` is at most 5
> - `big_sales['revenue'].min() > 50000` — all filtered rows meet the condition
> - `monthly` is a pandas Series (result of `.groupby().sum()`)
> - `monthly.sum()` equals `df[df['revenue'] > 50000]['revenue'].sum()` (sum of big sales only)

---

## Concept 3 — matplotlib: visualizing data

Numbers in a table are hard to understand. A thousand rows of revenue by month blurs into a number. But a line chart showing revenue rising from January to June, then dipping in July, tells the story in seconds.

**matplotlib** is Python's standard plotting library. It has a lot of options (and complexity), but the basics are simple: you provide x-values, y-values, and a plot type. It draws.

### Line plot: trends over time

```python
import matplotlib.pyplot as plt

# Data: months and revenue totals
months = ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun']
revenue = [15000, 18200, 21500, 20100, 25300, 28900]

# Create a line plot
plt.plot(months, revenue, marker='o', linestyle='-', color='blue', linewidth=2)

# Customize
plt.title('Monthly Revenue')
plt.xlabel('Month')
plt.ylabel('Revenue ($)')
plt.grid(True)

# Display
plt.show()
```

The `marker='o'` adds dots at each data point. `linestyle='-'` draws a solid line (other options: `'--'` for dashed, `':'` for dotted). `color='blue'` sets the color. `linewidth=2` makes the line thicker.

> **Prompt for Claude.** "Using matplotlib, create a line plot with months ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun'] on the x-axis and revenue [15000, 18200, 21500, 20100, 25300, 28900] on the y-axis. Add markers at each point (circles), a title 'Monthly Revenue', axis labels, and a grid."

> **Tests to validate.**
> - Plot has 6 data points (one per month)
> - Line connects all points in order
> - Title and axis labels are visible
> - Grid is displayed
> - Markers (circles) appear at each data point
> - Color is blue and linewidth is 2

### Bar chart: comparing categories

```python
import matplotlib.pyplot as plt

# Data: products and their sales counts
products = ['Widget A', 'Widget B', 'Gadget', 'Gizmo']
sales_count = [450, 320, 280, 190]

# Create a bar chart
plt.bar(products, sales_count, color='green', alpha=0.7)

# Customize
plt.title('Sales Count by Product')
plt.xlabel('Product')
plt.ylabel('Number of Sales')
plt.xticks(rotation=45)

# Display
plt.show()
```

`plt.bar()` creates vertical bars. `alpha=0.7` makes the bars slightly transparent (opacity from 0 to 1). `plt.xticks(rotation=45)` rotates the x-axis labels 45 degrees so they're easier to read.

> **Prompt for Claude.** "Using matplotlib, create a bar chart with products ['Widget A', 'Widget B', 'Gadget', 'Gizmo'] on the x-axis and sales counts [450, 320, 280, 190] on the y-axis. Use green bars with slight transparency (alpha=0.7), rotate the x-axis labels 45 degrees, and add a title 'Sales Count by Product' and axis labels."

> **Tests to validate.**
> - Bar chart displays 4 bars (one per product)
> - Bar heights match the sales counts (Widget A at 450, etc.)
> - Title is 'Sales Count by Product'
> - Bars are green with transparency
> - X-axis labels are rotated 45 degrees
> - Y-axis label is 'Number of Sales'

### A two-plot side-by-side example

```python
import matplotlib.pyplot as plt

# Data
months = ['Jan', 'Feb', 'Mar', 'Apr']
line_data = [100, 150, 120, 180]
bar_data = [20, 25, 18, 30]

# Create a figure with two subplots side by side
fig, (ax1, ax2) = plt.subplots(1, 2, figsize=(12, 4))

# Left plot: line
ax1.plot(months, line_data, marker='o', color='blue')
ax1.set_title('Trend Line')
ax1.set_ylabel('Value')

# Right plot: bar
ax2.bar(months, bar_data, color='red')
ax2.set_title('Comparison Bar')
ax2.set_ylabel('Count')

plt.tight_layout()
plt.show()
```

`plt.subplots(1, 2)` creates one row and two columns of plots. `figsize=(12, 4)` sets the overall width and height. `ax1` and `ax2` are the individual plot axes; you call `.plot()` or `.bar()` on them directly.

> **Prompt for Claude.** "Using matplotlib, create two plots side by side using `plt.subplots(1, 2)`: left plot is a line chart with months and values, right plot is a bar chart with months and counts. Add titles, labels, and make the figure size (12, 4). Use `plt.tight_layout()` before showing."

> **Tests to validate.**
> - `plt.subplots(1, 2)` returns figure and two axes objects
> - Left plot (ax1) displays a line with blue color and markers
> - Right plot (ax2) displays bars with red color
> - Each subplot has its own title and ylabel
> - Overall figure width is 12 and height is 4
> - Both plots render without overlap

### Trade-offs: matplotlib control vs. convenience

matplotlib is powerful — you can control nearly every pixel. But the learning curve is steep. For quick exploratory plots, libraries like Seaborn (built on matplotlib) or Plotly (interactive) are often faster to use. But matplotlib is the foundation, and it's ubiquitous in academic papers and reports.

### Common misconceptions

**`plt.show()` blocks the script.** Once you call `plt.show()`, the script pauses until you close the plot window. In Jupyter notebooks, this is not necessary — plots display automatically. In a regular Python script, `plt.show()` is required to see anything.

**Matplotlib does not return a DataFrame.** Plotting is for display, not for analysis. If you want the aggregated data (like monthly totals), use pandas. If you want to *see* the monthly totals, use matplotlib.

### Worked example — plot sales by month as a line and by product as a bar

```python
import pandas as pd
import matplotlib.pyplot as plt

# Create sample data (in practice, you'd read this from a CSV)
df = pd.DataFrame({
    'month': ['Jan', 'Jan', 'Jan', 'Feb', 'Feb', 'Feb'],
    'product': ['A', 'B', 'A', 'A', 'B', 'B'],
    'revenue': [5000, 3000, 4000, 6000, 3500, 4500]
})

# Monthly revenue (line plot)
monthly = df.groupby('month')['revenue'].sum()
plt.figure(figsize=(10, 4))

plt.subplot(1, 2, 1)
plt.plot(monthly.index, monthly.values, marker='o', color='blue', linewidth=2)
plt.title('Monthly Revenue')
plt.xlabel('Month')
plt.ylabel('Revenue ($)')
plt.grid(True)

# Product revenue (bar chart)
by_product = df.groupby('product')['revenue'].sum()

plt.subplot(1, 2, 2)
plt.bar(by_product.index, by_product.values, color='green', alpha=0.7)
plt.title('Revenue by Product')
plt.xlabel('Product')
plt.ylabel('Revenue ($)')

plt.tight_layout()
plt.show()
```

Output: Two side-by-side plots — a line showing revenue trend by month (left) and a bar chart showing total revenue by product (right).

> **Prompt for Claude.** "Using matplotlib, create two plots side by side: (1) a line plot of monthly revenue using x=[Jan, Feb, Mar] and y=[15000, 18000, 21000], with markers and a grid; (2) a bar chart of product counts using x=[Widget, Gadget] and y=[450, 320]. Use `plt.subplots(1, 2)` to create side-by-side plots. Label axes and add titles."

> **Tests to validate.**
> - Two plots are created (checked by `plt.subplots(1, 2)` returning two axes)
> - Line plot has `marker='o'` (or similar) and grid visible
> - Bar chart displays two bars with the correct heights
> - Both plots have titles and axis labels
> - `plt.show()` is called at the end (or in a Jupyter environment, plots render automatically)

---

## Integration — the data science workflow, in action

Let's tie the three concepts together: load data (pandas), process it (NumPy), visualize it (matplotlib). This is the data scientist's day.

**Scenario:** You have a CSV file of customer purchases. You need to:
1. Load it and check for missing data.
2. Filter to recent purchases (last 90 days).
3. Group by product and calculate average spending.
4. Plot the results.

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from datetime import datetime, timedelta

# Load data
df = pd.read_csv('purchases.csv')
print(f"Loaded {len(df)} rows")

# Inspect
print(df.head())
print(f"Missing values:\n{df.isnull().sum()}")

# Convert date column to datetime
df['date'] = pd.to_datetime(df['date'])

# Filter to last 90 days
cutoff = datetime.now() - timedelta(days=90)
df = df[df['date'] > cutoff]
print(f"After filtering: {len(df)} rows")

# Handle missing values: drop rows with null amount
df = df.dropna(subset=['amount'])

# Group by product, calculate mean spending
avg_by_product = df.groupby('product')['amount'].mean()

# Sort by spending (highest first)
avg_by_product = avg_by_product.sort_values(ascending=False)

# Plot
plt.figure(figsize=(10, 6))
plt.bar(avg_by_product.index, avg_by_product.values, color='steelblue')
plt.title('Average Customer Spending by Product (Last 90 Days)')
plt.xlabel('Product')
plt.ylabel('Average Spending ($)')
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()

# Summary
print(f"\nTop product by average spend: {avg_by_product.index[0]}")
print(f"Average: ${avg_by_product.values[0]:.2f}")
```

Notice the flow: load → inspect → filter → clean → aggregate → visualize → report. This pattern repeats in every real data analysis.

---

## Graduated exercises

### Warm-up

**Exercise 15.1** *(LO: NumPy arrays).* Create a NumPy array from the list `[10, 20, 30, 40, 50]`. Convert each value to meters by dividing by 3.28084 (feet to meters conversion). Print the result.

**Exercise 15.2** *(LO: pandas loading and inspection).* Write a Python snippet that reads a CSV file named `data.csv` into a pandas DataFrame. Use `.head()`, `.shape`, and `.info()` to inspect it. What are the column names and data types?

**Exercise 15.3** *(LO: filtering).* Given a DataFrame `df` with columns `['name', 'age', 'salary']`, write code to filter rows where `age > 30` and `salary < 100000`.

### Application

**Exercise 15.4** *(LO: groupby).* You have a DataFrame with columns `['date', 'category', 'amount']` representing daily expenses. Group by category and sum the amounts. Which category had the highest total?

**Exercise 15.5** *(LO: missing data).* A DataFrame has a column `'email'` with some null values. Write code to (a) count how many rows have missing email, (b) drop rows with missing email, (c) replace missing emails with the string `'no-email'`.

**Exercise 15.6** *(LO: matplotlib).* Using the sample data below, create a line plot showing the trend of temperature over five days. Add a title, axis labels, and a grid.

```python
days = ['Mon', 'Tue', 'Wed', 'Thu', 'Fri']
temps = [68, 72, 70, 75, 77]
```

### Synthesis

**Exercise 15.7** *(LO: integration).* Load a CSV of sales data. (a) Display the first 10 rows and data types. (b) Filter to sales where amount > 500. (c) Group by 'region' and sum the amounts. (d) Create a bar chart of regional totals.

**Exercise 15.8** *(LO: data pipeline).* Write a function `analyze_data(filename)` that: (1) loads the CSV, (2) identifies missing values in each column, (3) drops rows with missing 'price', (4) groups by 'category' and calculates the mean price per category, (5) returns the result as a DataFrame. Test it on a sample CSV.

### Challenge

**Exercise 15.9** *(LO: multi-step analysis).* You have a CSV with columns: 'date', 'product', 'units_sold', 'price_per_unit'. (a) Load the data and convert the 'date' column to a datetime type. (b) Create a new column 'revenue' = units_sold * price_per_unit. (c) Group by product and sum the revenue. (d) Plot the result as a horizontal bar chart (use `plt.barh()` instead of `plt.bar()`). (e) Report the top-selling product and its total revenue.

**Exercise 15.10** *(LO: open-ended).* Obtain a real dataset (e.g., Kaggle, data.gov, or your own). Load it with pandas. Identify three columns of interest. Use pandas to explore: What are the column types? Are there missing values? What is the distribution of values in each column? Create at least two visualizations (e.g., histogram of one column, bar chart of grouped data). Write a short summary (3–4 sentences) of what you learned.

---

## Claude Code — Build a sales analysis pipeline

This section walks you through a complete data analysis project: generating synthetic sales data, loading it, aggregating it, and visualizing it. You will practice NumPy vectorization, pandas groupby, and matplotlib in one workflow. The project lives in code, not in explanation — you ask Claude Code (or Claude in your terminal) to write it, then you run it and verify it works.

### Orientation

You are building a small data pipeline. The ingredients:

1. **Generate synthetic data.** A CSV file with 100 rows of sales data: month (1–12, repeated roughly 8 times), product (A, B, or C), and revenue (random 50–500).
2. **Load and aggregate.** Read the CSV with pandas, group by month and product, sum the revenue, and print a pivot table.
3. **Create a visualization.** Use matplotlib to plot monthly total revenue as a bar chart.
4. **Validate with tests.** Write assertions that check the aggregation is correct and that NumPy's vectorized sum matches pandas' sum.

The point is not to build something you'll use forever. The point is to see the three libraries (NumPy, pandas, matplotlib) working together on a problem that mirrors real work.

### What to expect

You will write (or ask Claude Code to write) three files:

- `sales.csv` — synthetic data, 100 rows.
- `analysis.py` — loads the CSV, groups by month and product, prints a pivot table, creates a chart.
- `test_analysis.py` — validates the aggregation with assertions, including a NumPy vectorized recomputation.

Then you will run `analysis.py` to see the pivot table and plot, and run `test_analysis.py` to confirm correctness.

### The prompt to Claude Code

Here is a complete prompt you could send to Claude Code (or Claude in your browser):

> *"I have three tasks. (1) Create a CSV file called `sales.csv` with 100 rows. Each row has: month (1–12, roughly 8 rows per month), product (one of A, B, C, distributed evenly), revenue (random integer 50–500). (2) Write `analysis.py` that loads `sales.csv` with pandas, prints the first 5 rows, then groups by month and product, sums revenue, and prints a pivot table (months as rows, products as columns). Then create a matplotlib bar chart showing total monthly revenue (summed across products), with months on the x-axis, revenue on the y-axis, and save the chart as `monthly.png`. (3) Write `test_analysis.py` that loads the same CSV, re-computes the monthly totals using NumPy's sum (vectorized, no loop), and asserts that the NumPy total equals the sum of all revenue rows. Also assert that the pivot table has 12 months and 3 products. Then run both scripts and confirm the output and that `monthly.png` was created."*

### Stretch

Once this pipeline works, try it on a dataset of your own choosing. Find a CSV online (Kaggle, data.gov, your company's data, a school project dataset). Replace `sales.csv` with your real data. Adapt the groupby and visualization to answer a question about *your* data — not this textbook's example. The machinery is the same. The question changes.

---

## Chapter summary

You have now worked with the three libraries that dominate Python data science.

**NumPy** gives you arrays and vectorized arithmetic. Instead of loops, you write one line of code that operates on a million numbers at once. The trade-off: arrays are less flexible than lists, but far faster.

**pandas** gives you DataFrames — tables with column names, mixed data types, and built-in operations for the messy work: reading CSVs, filtering rows, grouping by category, handling missing values. Most real data analysis is pandas work, not algebra.

**matplotlib** gives you plots. A line chart, a bar chart, a scatter plot. Numbers on a screen are hard to interpret; pictures are not. Matplotlib is the foundation of visualization in Python, though it takes practice to customize. Seaborn (built on matplotlib) and Plotly (interactive) are faster for quick exploratory plots, but matplotlib is the standard in academic papers and professional reports.

You have also followed the three-part Claude pattern throughout: each substantial example showed you the code, a prompt you could send to an LLM to elicit that code, and tests to verify it works. That pattern is how you remain in control when using an AI assistant. You specify precisely what you want, and you verify that what comes back is correct.

The data science lifecycle goes like this: **acquisition** (get the data, usually a CSV or database), **exploration** (load it, inspect it, understand its shape and quality), **cleaning** (handle missing values, fix inconsistencies, prepare it for analysis), **analysis** (group, filter, aggregate — ask questions of the data), **visualization** (show the results), **reporting** (communicate what you learned). This chapter covered exploration through visualization. Machine learning and predictive modeling sit downstream. You can't build a good model on data you don't understand.

This is also the last chapter in a fifteen-chapter sequence on Python. You now know:

- How to write statements, expressions, and control flow.
- How to organize code with functions and modules.
- How to work with strings, lists, dictionaries, and classes.
- How to think recursively and understand inheritance.
- How to read and write files.
- How to load and analyze data with the industry-standard tools.

You cannot do everything. Machine learning requires more. Web frameworks require more. But you can read data, clean it, understand it, and communicate what it says. That is a professional skill. You have it now.

### Functions reference

| Function | Use |
|---|---|
| `np.array(list)` | Create a NumPy array from a Python list. |
| `np.zeros(n)` | Create an array of n zeros. |
| `np.ones(n)` | Create an array of n ones. |
| `np.random.rand(n)` | Create an array of n random numbers in [0, 1). |
| `pd.read_csv(filename)` | Load a CSV file into a DataFrame. |
| `df.head(n)` | Display the first n rows (default 5). |
| `df.tail(n)` | Display the last n rows (default 5). |
| `df.shape` | Return (n_rows, n_columns). |
| `df.info()` | Display column names, types, and null counts. |
| `df.describe()` | Display summary statistics (mean, std, min, max, quartiles). |
| `df['col']` | Select a column (returns a Series). |
| `df[['col1', 'col2']]` | Select multiple columns (returns a DataFrame). |
| `df[condition]` | Filter rows by Boolean condition. |
| `df.groupby('col')['col2'].sum()` | Group by one column, aggregate another. |
| `df.isnull()` | Return Boolean DataFrame showing missing values. |
| `df.dropna()` | Drop rows with any missing value. |
| `df.fillna(value)` | Replace missing values with value. |
| `plt.plot(x, y)` | Create a line plot. |
| `plt.bar(x, y)` | Create a bar chart. |
| `plt.title(title)` | Set the plot title. |
| `plt.xlabel(label)` | Set the x-axis label. |
| `plt.ylabel(label)` | Set the y-axis label. |
| `plt.show()` | Display the plot. |

---

## Connections forward

If you move into data science or engineering, the next steps are:

- **Statistics** (Chapter 8 of the companion statistics textbook): probability distributions, hypothesis testing, confidence intervals.
- **Machine learning** (scikit-learn library): supervised learning (classification, regression), unsupervised learning (clustering).
- **SQL**: querying large databases when data doesn't fit in memory or come in a CSV.
- **Deep learning** (TensorFlow, PyTorch): neural networks for images, text, time series.

If you stay in software engineering, you will see pandas and NumPy in data pipelines, analytics dashboards, and backend services. You will recognize the patterns — groupby, filter, aggregate — as the vocabulary of moving data around.

If you go into science (biology, physics, economics, psychology), you will use these three libraries to analyze experiments, run simulations, visualize results, and communicate findings.

This chapter was your introduction to a domain. You won't master it in one reading. But you know the entry point now. You know where to open the browser and find the documentation when you need to do something new. And you know that the code you ask Claude to write can be tested, verified, and trusted because you understand the machinery underneath.

That trust — writing code with an LLM while keeping control — is the skill that matters most in the next decade. You have it.

---

## Still puzzling

How would you handle a dataset so large it doesn't fit in memory? (Answer: tools like Dask, Spark, or database queries that process in chunks.)

## What would change my mind

If you showed me a realistic dataset where pandas groupby is slower than a hand-written loop, I would revise the performance claims. (It would take a pathological case; vectorized NumPy is genuinely fast.)

## Tags

python; pandas; numpy; matplotlib; data-science; csv; vectorization; groupby; exploratory-data-analysis; visualization

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
```


---

##  AI Wayback Machine
**John Tukey** was invented exploratory data analysis — the framework underlying every modern Python data-science workflow.

**Run this:**

```
Who is John Tukey, and how does their work connect to data science we covered in this chapter? Keep it to three paragraphs. End with the single most surprising thing about their career or ideas.
```

→ Search **"John Tukey"** on Wikipedia.

**Now make the prompt better.** Try one of these:

- Ask it to apply John Tukey's ideas to a small Python program you'd write.
- Add a constraint: "Answer including criticisms or limits of John Tukey's framework."

What changes? What gets better? What gets worse?
