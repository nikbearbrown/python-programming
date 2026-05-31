# Images and Figures — Chapter 15 (Data Science)

## Figure 15.1: Array vs. List Memory Layout

**Brief:** Side-by-side diagram showing a Python list (separate objects scattered in memory, with pointers) vs. a NumPy array (contiguous memory, all elements packed together). Illustrates why arrays are faster.

**Caption:** A Python list stores references to objects scattered in memory. A NumPy array stores values contiguously, allowing vectorized operations in compiled code.

---

## Figure 15.2: Vectorization Example

**Brief:** Three code blocks arranged vertically:
1. Python loop (for temperature in temps: convert)
2. NumPy vectorization (one line: (temps - 32) * 5/9)
3. Timing comparison (loop: 0.5 seconds on 1M elements; NumPy: 0.005 seconds).

**Caption:** Vectorized NumPy operations are 100x faster than Python loops on large arrays because they run in compiled C code instead of Python bytecode.

---

## Figure 15.3: DataFrame Structure

**Brief:** A table (spreadsheet-like view) with:
- Column headers: 'name', 'age', 'salary'
- Three rows of data (Alice, 25, 50000 etc.)
- Column dtypes shown below headers (object, int64, float64)
- Index column on the left (0, 1, 2)

**Caption:** A pandas DataFrame is a table with labeled rows and columns. Each column can have its own data type. The index uniquely identifies each row.

---

## Figure 15.4: GroupBy Conceptual Diagram

**Brief:** Three steps illustrated:
1. Original DataFrame (rows: product A, B, A, C, B; revenue: 1000, 2000, 1500, 1200, 800).
2. "Group by product" — rows are partitioned into piles (A's together, B's together, C's together).
3. "Sum" — each pile is summed (A: 2500, B: 2800, C: 1200).

**Caption:** `groupby()` partitions the DataFrame by values in a column, then applies an aggregation (sum, mean, count, etc.) to each partition.

---

## Figure 15.5: Filtering with Boolean Indexing

**Brief:** A DataFrame shown in full, with a Boolean condition below each row (True/False), and a third DataFrame showing only the True rows.

**Caption:** Filtering with `df[df['column'] > threshold]` creates a Boolean array and keeps only rows where the condition is True.

---

## Figure 15.6: Line Plot Example

**Brief:** A line chart with x-axis labeled "Month" (Jan–Jun), y-axis labeled "Revenue ($)", data points connected by a line with markers (circles). Grid lines visible.

**Caption:** A line plot shows trends over time. Markers highlight individual data points; a grid makes values easy to read.

---

## Figure 15.7: Bar Chart Example

**Brief:** A vertical bar chart with x-axis labeled "Product" (Widget A, Widget B, Gadget, Gizmo), y-axis labeled "Sales Count", four bars of varying heights.

**Caption:** A bar chart compares values across categories. Height directly represents the value; bars are easy to compare visually.

---

## Figure 15.8: Missing Data in a DataFrame

**Brief:** A DataFrame with some cells showing `NaN` (highlighted in red/different color). Below: before and after dropna() and fillna().

**Caption:** Missing values are represented as `NaN`. You can drop rows containing NaN or fill them with a substitute value (often mean or median).

---

## Figure 15.9: Full Data Pipeline Workflow

**Brief:** A flowchart:
- Load CSV → Inspect (head, info) → Filter rows → Handle missing data → GroupBy + aggregate → Visualize → Report

Each step is a box with arrows showing the flow. Icons or small code snippets can decorate each box.

**Caption:** The data science workflow: load, inspect, clean, aggregate, visualize, report. This pattern repeats in every analysis.

---

## Figure 15.10: Subplots Side by Side

**Brief:** Two plots in one figure:
- Left: line plot of monthly revenue (Jan–Jun, increasing trend)
- Right: bar chart of product sales counts (Widget A, Widget B, Gadget, Gizmo)
- Both plots have titles, labels, and grid/colors.

**Caption:** `plt.subplots(1, 2)` creates one row and two columns of plots, allowing side-by-side comparison.

---

## Figure 15.11: Datetime Filtering Timeline

**Brief:** A timeline showing dates from 90 days ago to "today". Rows before the cutoff are grayed out; rows after are highlighted.

**Caption:** Filtering a DataFrame by date requires converting the date column to datetime type, then comparing against a cutoff date.

---

## Notes on figure placement in chapter

- **Figure 15.1** appears after "NumPy arrays: fast, flat arithmetic" introduction, before "Creating an array."
- **Figure 15.2** appears in the "Vectorization" section.
- **Figure 15.3** appears after the "Loading and inspecting data" section.
- **Figure 15.4** appears in the "Groupby" section.
- **Figure 15.5** appears in the "Selecting and filtering" section.
- **Figure 15.6 and 15.7** appear in the matplotlib sections for line plot and bar chart.
- **Figure 15.8** appears in the "Handling missing data" section.
- **Figure 15.9** appears in the "Integration" section.
- **Figure 15.10** appears in the worked example for matplotlib.
- **Figure 15.11** appears in the full pipeline worked example in Integration.

