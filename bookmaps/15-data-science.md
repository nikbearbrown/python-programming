# Bookmap — Chapter 15 (Data Science)

## Source materials

Seven Markdown files from the original chapter outline:
1. **01-m00108.md** — Chapter introduction, data science definition, life cycle overview.
2. **02-m00109.md** — Learning objectives, data science life cycle stages, tools overview (Python, R, Jupyter, Google Colab, Kaggle, Excel/Sheets).
3. **03-m00110.md** — NumPy library (ndarray, creating arrays, operations: mathematical, array manipulation, linear algebra).
4. **04-m00111.md** — Pandas library (Series, DataFrame, data input/output from dict/list/array/CSV/Excel, DataFrame functions).
5. **05-m00112.md** — Exploratory Data Analysis (indexing with loc/iloc, slicing, filtering, handling missing data with isnull/dropna/fillna).
6. **06-m00113.md** — Matplotlib (visualization types: bar, line, scatter, histogram, box plot; plot methods; code examples for each).
7. **07-m00115.md** — Chapter summary, functions reference table.

Total source word count: ~5,500 words across seven files.

---

## Concept coverage

### Chosen for conversion

1. **NumPy arrays** (from 03-m00110):
   - What an ndarray is and why it's faster than a list.
   - Creating arrays (from list, zeros, ones, random).
   - Indexing and slicing.
   - Broadcasting.
   - Vectorization as the core insight.
   - Worked example: Fahrenheit to Celsius with mean.

2. **Pandas DataFrames** (from 04-m00111 and 05-m00112):
   - DataFrame as a table structure (rows, columns, dtypes).
   - Loading CSV with `pd.read_csv()`.
   - Inspection methods: head, tail, info, describe.
   - Selecting columns and rows.
   - **Filtering** with Boolean indexing.
   - **GroupBy** as the most important operation.
   - Handling missing data: isnull, dropna, fillna.
   - Worked example: load, filter, group, summarize.

3. **Matplotlib visualization** (from 06-m00113):
   - Why visualization matters.
   - Line plots (trends over time).
   - Bar charts (category comparison).
   - Subplots (side-by-side plots).
   - Customization (title, labels, colors, markers).
   - Worked example: dual plots (line + bar).

### Deferred material (with reasons)

- **Other visualization types** (scatter plot, histogram, box plot): The chapter focuses on line and bar charts because they're the most common for introductory data science. Scatter, histogram, and box plot are mentioned in the source but not deeply covered here because they require additional statistical knowledge (scatter → correlation, histogram → distribution, box plot → quartiles). A student who masters line and bar can easily extend to others.

- **Data science life cycle stages in detail** (acquisition, exploration, analysis, reporting): The chapter touches on these in the "Workflow" section but doesn't dedicate sections to each. The source spends more time on this framework; here, it's woven into the integration section as a practical pattern.

- **R, Jupyter, Google Colab, Kaggle Kernels, Excel**: The source devotes a section to tools. This chapter assumes students are working in a Python environment (Jupyter or script) and mentions Google Colab only in passing. The focus is on the libraries, not the platforms.

- **Advanced NumPy** (linear algebra, matrix operations, fancy indexing): Deferred because it requires linear algebra knowledge (eigenvalues, matrix inversion). Chapter 15 teaches vectorization and basic array operations; students ready for linear algebra will have the foundation to read the NumPy documentation.

- **Advanced pandas** (merging/joining DataFrames, pivot tables, time series): Deferred because the chapter is already dense. Merging and pivots are important but not covered in depth in the source materials either. The chapter teaches the foundation: load, filter, group, aggregate.

- **Machine learning** (scikit-learn, model training, cross-validation): Out of scope for this chapter and book. The source mentions "data analysis" and "predictive models" but this chapter focuses on exploration and visualization, not modeling.

- **Seaborn and Plotly**: The source mentions these as visualization alternatives. The chapter uses matplotlib only (as the foundational library) and notes that Seaborn and Plotly are faster for quick plots but less customizable.

---

## Source-level notes

### From 01-m00108 (Introduction)
- "Data science provides the ability to derive insights and make informed decisions from data." — This is the opening framing; chapter cold open builds on it.
- Diverse applications (healthcare, business, education, politics, environmental, social sciences). — Used to motivate the chapter.
- The chapter covers "a first taste of data science," not the full field. — This aligns with the chapter's focus: exploration and visualization, not modeling.

### From 02-m00109 (Learning objectives and tools)
- **Life cycle:** acquisition → exploration → analysis → reporting. — Embedded in the integration section.
- **Tools overview:** Python (NumPy, Pandas, Matplotlib, Seaborn, scikit-learn) is the primary tool. — Chapter focuses on NumPy, pandas, matplotlib.
- Learning objectives include: describe data science, identify life cycle stages, name tools, use Google Colab. — Chapter's objectives are more hands-on (create arrays, load data, visualize).

### From 03-m00110 (NumPy)
- ndarray stores homogeneous data (same type). — Emphasized in the chapter.
- Creation methods: from list, zeros, ones, random, from file. — All covered.
- Operations: mathematical (element-wise), array manipulation (reshape, transpose, concatenate), linear algebra. — Chapter covers mathematical and simple manipulation; linear algebra deferred.
- Source has heavy focus on operations; chapter prioritizes vectorization as the key insight.

### From 04-m00111 (Pandas)
- Name etymology: "panel data." — Included in etymologies.
- Series (1D) and DataFrame (2D) structures. — DataFrame emphasized; Series mentioned in groupby results.
- Data input/output: dict, list, array, CSV, Excel. — CSV emphasized; others mentioned.
- DataFrame functions: head, tail, info, describe, value_counts, unique. — All included in the chapter.
- Source has a long table of examples; chapter converts to prose + one worked example.

### From 05-m00112 (EDA)
- Indexing: loc (label-based) and iloc (integer-based). — Both mentioned; focus on filtering with conditions.
- Slicing: ranges, lists, Boolean conditions. — Boolean filtering emphasized.
- Missing data: isnull, dropna, fillna. — All included with a worked example.
- Source emphasizes ethical considerations (why is data missing? whose data?); chapter notes this but focuses on mechanics.

### From 06-m00113 (Matplotlib)
- Visualization types table. — Converted to text; line and bar plots detailed, others mentioned.
- Matplotlib functions: bar, plot, scatter, hist, boxplot. — plot and bar covered in detail; scatter, hist, boxplot deferred.
- Source provides code for each plot type; chapter provides detailed code for line and bar, plus a two-plot example.
- Integration with NumPy and pandas (e.g., passing groupby results to pyplot). — Shown in integration section.

### From 07-m00115 (Summary)
- Chapter summary is synthesized with the chapter's own integration; original summary is a bullet-point list, chapter's is narrative.
- Functions reference table is adapted and placed at chapter end.

---

## Claude triple count

The chapter contains **10 three-part Claude triples** (code, prompt, tests):

1. **NumPy array creation** — Create array from list, zeros, ones, random.
2. **Fahrenheit to Celsius vectorization** — Convert array, find mean.
3. **Pandas load, filter, group** — Read CSV, filter revenue > 50000, group by month.
4. **Matplotlib line plot** — Line plot with markers and grid.
5. **Matplotlib bar chart** — Bar chart with color and rotation.
6. **Matplotlib subplots** — Two plots side by side.
7. **Pandas missing data** — Create DataFrame with NaN, isnull, dropna, fillna.
8. **Pandas groupby summary** — Load, filter, group, aggregate multiple operations.
9. **matplotlib dual plot from data** — Load sample data, create line and bar side by side.
10. **Full pipeline integration** — Load, filter by date, handle missing, group, aggregate, visualize.

Each triple follows the format:
```
[code block]
> **Prompt for Claude.**
> **Tests to validate.**
```

---

## Deferred topics and reasoning

| Topic | Reason for deferral | Alternative |
|---|---|---|
| Scatter plot, histogram, box plot | Require statistical context (correlation, distribution, quartiles) | Line and bar plots suffice for intro; students can extend |
| Linear algebra (matrix ops, eigenvalues) | Requires advanced math prerequisites | Chapter teaches array fundamentals; NumPy docs cover the rest |
| Merging/joining DataFrames, pivot tables | Advanced pandas; source doesn't cover in depth | Foundation (load, filter, group) is sufficient for a first chapter |
| Time series analysis | Requires datetime knowledge beyond scope | One filtering-by-date example is included |
| Machine learning (scikit-learn) | Out of scope for "data science intro"; separate course | Chapter ends with "ML is downstream" note; points forward |
| Seaborn, Plotly | Alternatives to matplotlib; chapter focuses on foundation | Brief mention that faster tools exist but matplotlib is standard |
| SQL, big data (Spark, Dask) | Requires data-at-scale context | Chapter note: "datasets that fit in memory" assumption |

---

## Structural differences from source

| Aspect | Source | Chapter |
|---|---|---|
| **Organization** | Life cycle framework (acquisition → exploration → analysis → reporting) | Three-concept framework (NumPy → pandas → matplotlib) plus integration |
| **Depth** | Breadth (introduces many tools: R, Jupyter, Kaggle, Excel) | Depth (focuses on Python, three core libraries) |
| **Code examples** | Tables with many small examples | Fewer, longer examples; each with prompt and tests |
| **Learning objectives** | List form (describe, identify, name, use) | Narrative form (create, load, visualize) |
| **Visualization** | Five plot types tabulated | Two plot types (line, bar) in detail |
| **Missing data** | Three functions (isnull, dropna, fillna) with examples | Same three functions, emphasis on ethical considerations |
| **Cold open** | None (chapter starts with definition) | Scene-based (student at screen with CSV) |
| **Integration** | Mentioned in "programming practice" sections | Full worked example (load → filter → group → visualize → report) |

---

## Alignment with book's voice and three-part Claude pattern

The chapter maintains the book's **Attenborough × Feynman voice**: cold opens in scene (spreadsheet on screen, analyst needing answers), mechanism-first explanations (why NumPy is faster, how groupby partitions), trade-offs explicit (flexibility vs. speed, richness vs. memory), worked examples grounded in real domains (sales by month, customer spending by product).

Each substantial code example follows the **three-part Claude pattern**:
1. **Code block** — Runnable Python snippet.
2. **Prompt** — What a student could ask Claude for.
3. **Tests** — How to verify the code works.

This teaches students to: (a) specify what they want precisely, (b) verify that what comes back is correct, (c) remain in control when using an LLM.

---

## Notes for future chapters / cross-references

- **Chapter 8 (Strings)** deals with CSV file paths and formatting; Chapter 15 uses this knowledge.
- **Chapter 9 (Lists)** covers iteration and comprehensions; Chapter 15 contrasts Python loops with NumPy vectorization.
- **Chapter 10 (Dictionaries)** shows how to create DataFrames from dicts.
- **Chapter 14 (Files)** covers CSV reading without pandas; Chapter 15 shows the pandas upgrade.
- **Forward pointer:** Machine learning chapters (not in this book) will use pandas DataFrames and sklearn for modeling.

---

## Errata or uncertainties from source

- The source mentions Google Colaboratory extensively but provides no specific examples. Chapter mentions it for reference but doesn't require it (students can use any Jupyter or Python environment).
- The source references linked videos and Google Colab documents (marked with `[Google Colaboratory document](#)`); chapter assumes students read source files independently and provides self-contained examples.
- Source terminology: "stakeholders" (ch. 15 uses plain language instead per CLAUDE.md hard rules).

