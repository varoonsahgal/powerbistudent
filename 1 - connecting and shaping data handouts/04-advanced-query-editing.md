# Handout 04: Advanced Query Editing — Index Columns, Conditional Columns, Group By, Pivot/Unpivot, Merge, Append, and Data Source Settings

## Purpose

This handout covers the more advanced Power Query capabilities used to shape and combine data: conditional logic columns, index columns, aggregation with Group By, structural transformations with Pivot and Unpivot, combining tables with Merge and Append, the folder connector, and managing data source connections.

---

## Prerequisites

- `Customer Lookup`, `Product Lookup`, and `Calendar Lookup` tables are already loaded in Power Query Editor (from Handouts 02 and 03).
- The three sales CSV files (`AdventureWorks Sales Data 2020.csv`, `2021.csv`, `2022.csv`) exist in a subfolder called `Sales Data` inside the `AdventureWorks Raw Data` folder.

> **Starting state of the Sales Data CSV files:**  
> `AdventureWorks Sales Data 2020/2021/2022.csv` each contain:  
> `OrderDate`, `StockDate`, `OrderNumber`, `ProductKey`, `CustomerKey`, `TerritoryKey`, `OrderLineItem`, `OrderQuantity`

---

## Part 0: Tidy Up the Queries Pane (Demo Queries Group)

Before we add new tables, organize the queries you no longer need to load (any **Disable Load** demo queries from earlier handouts — for example, the SQL connection demo, the web/Wikipedia demo, etc.) into a single group folder so the pane stays clean.

### Steps

1. In Power Query Editor, look at the **Queries** pane on the left.
2. Hold **Ctrl** and click each demo query that has its name in *italics* (these are the queries with **Enable Load** unchecked).
3. Right-click any of the selected queries and choose **Move To Group** > **New Group...**.
4. Name the group `Demo Queries` and click **OK**.
5. The selected queries are now collapsed under a `Demo Queries` folder. Click the arrow next to the folder name to collapse or expand it.

> **Why we do this:** It keeps your live, in-use queries clearly visible in the Queries pane and separates them from queries that are kept around for reference only.

---

## Part 0.5: Load the Sales Data 2022 Table

We will use the 2022 sales data for the index column, conditional column, and Group By exercises. Load it now.

### Steps

1. On the **Home** ribbon of Power Query Editor, click **New Source** > **Text/CSV**.
2. Browse to `AdventureWorks Raw Data` > `Sales Data` and select `AdventureWorks Sales Data 2022.csv`.
3. Click **Open**, then click **OK** in the preview window.
4. In the **Queries** pane, double-click the new query and rename it to `Sales Data 2022`.

   > **Naming note:** We deliberately do **not** add the `Lookup` suffix here. This is a **fact table** (containing transactions and keys that map to lookup tables), not a lookup table. We will discuss the difference in the data modeling section.

5. Verify column data types by clicking each header:

   | Column | Expected Data Type |
   |---|---|
   | `OrderDate` | Date |
   | `StockDate` | Date |
   | `OrderNumber` | Text |
   | `ProductKey` | Whole Number |
   | `CustomerKey` | Whole Number |
   | `TerritoryKey` | Whole Number |
   | `OrderLineItem` | Whole Number |
   | `OrderQuantity` | Whole Number |

6. Fix any data types that are wrong by clicking the type icon in the column header.

---

## Part 1: Calculated Column Best Practices

Before diving into the tools, it is important to understand *where* to create calculated columns for the best performance.

### The Priority Order

| Priority | Location | Why |
|---|---|---|
| 1 (Best) | **Source system** (SQL, Excel, etc.) | Uses the source engine; no Power BI overhead |
| 2 | **Power Query Editor** | Included in VertiPaq's query plan for optimal compression |
| 3 | **Power BI front end (DAX)** | Not part of the query plan; can increase model size |
| 4 (Worst) | **Published report layer** | Furthest from the data; most overhead |

> **Why Power Query is better than DAX for static columns:** When you load data using Import mode, Power BI's internal engine (called **VertiPaq**) creates a query plan that determines the best way to compress and store the data in memory. Columns added in Power Query are part of that plan. Columns added later through DAX are not, which can disproportionately increase the size of the data model.
>
> This is not a strict rule — DAX calculated columns are sometimes necessary. But when a column can be created in Power Query without significant complexity, prefer Power Query.

---

## Part 2: Index Columns

An **index column** is a sequential list of numbers (starting from 0, 1, or a custom value) that uniquely identifies each row. Index columns are most commonly used to create surrogate keys that can form relationships between tables.

### Adding an Index Column

1. Select the target table in Power Query Editor.
2. On the **Add Column** ribbon, click **Index Column**.
3. Choose:
   - **From 0** — starts at zero.
   - **From 1** — starts at one (most common).
   - **Custom** — specify a start value and increment.

### A Word of Caution About Applied Steps

If you add an index column, then move it, and then delete it, Power Query still records **all three steps** in the Applied Steps pane — even though the end result is the same as if you had never added the column at all.

Each time the data is refreshed, Power BI re-executes every applied step in sequence. Redundant steps waste processing time.

> **Best practice:** If you decide you do not need a column after experimenting, delete **all** the related applied steps (the Add step, any Reorder step, and the Remove step) by clicking the **X** next to each one in the Applied Steps pane.

---

## Part 3: Conditional Columns

A **conditional column** lets you define a new column based on if/then/else logic — similar to a nested `IF` statement in Excel.

### How Conditional Columns Work

When you define a conditional column, Power Query evaluates each row from top to bottom through your list of conditions. The first condition that evaluates to **true** determines the output value for that row. If no condition is met, the **Otherwise** (else) value is used.

---

## Exercise 1: Add a Quantity Type Column

**Goal:** Add a `Quantity Type` column to the `Sales Data 2022` table that labels each order as `Single Item` or `Multiple Items` based on `OrderQuantity`.

### Steps

1. In Power Query Editor, select the `Sales Data 2022` table.
2. On the **Add Column** ribbon, click **Conditional Column**.
3. In the dialog box:
   - **New column name:** `Quantity Type`
   - **Condition 1:**
     - Column: `OrderQuantity`
     - Operator: `equals`
     - Value: `1`
     - Output: `Single Item`
   - Click **Add Clause**.
   - **Condition 2:**
     - Column: `OrderQuantity`
     - Operator: `is greater than`
     - Value: `1`
     - Output: `Multiple Items`
   - **Otherwise:** `Other`
4. Click **OK**.

**Expected result:** The `Quantity Type` column shows `Single Item` for orders of 1 unit and `Multiple Items` for orders of 2 or more. The `Other` value acts as a safety catch — in this dataset it should not appear, but it protects against unexpected data in the future.

### Verifying the Results

1. Click the dropdown arrow on the `Quantity Type` column header.
2. If a banner reads "List may be incomplete," click **Load More** to force Power BI to scan the entire column.
3. Confirm only `Single Item` and `Multiple Items` appear.

---

## Part 4: Grouping and Aggregating Data

**Group By** lets you roll up a table from row-level detail to a higher-level summary. It is the Power Query equivalent of a pivot table or a `GROUP BY` clause in SQL.

> **Analogy:** Imagine your sales table has thousands of individual orders. Group By can compress that into a two-column summary: one column with each unique product key, one column with the total quantity sold for that product.

Any column not included in the Group By settings is removed from the output. You are intentionally trading detail for summary.

---

## Exercise 2: Group Sales Data by Product Key

**Goal:** Summarize the `Sales Data 2022` table to show total order quantity per product.

1. Select the `Sales Data 2022` table.
2. On the **Transform** ribbon, click **Group By**.
3. In the dialog:
   - Keep the **Basic** option selected.
   - **Group by column:** `ProductKey`
   - **New column name:** `Total Quantity`
   - **Operation:** `Sum`
   - **Column:** `OrderQuantity`
4. Click **OK**.

**Expected result:** A two-column table with `ProductKey` and `Total Quantity`. Sorting by `ProductKey` ascending shows each product key appearing exactly once.

5. Delete the **Grouped Rows** applied step to restore the original table.

---

## Exercise 3: Group by Multiple Columns (Advanced)

**Goal:** Summarize total quantity by every unique combination of `ProductKey` and `CustomerKey`.

1. On the **Transform** ribbon, click **Group By**.
2. Click **Advanced**.
3. Add two **Group By** columns: `ProductKey` and `CustomerKey`.
4. Set **New column name** to `Total Quantity`, **Operation** to `Sum`, **Column** to `OrderQuantity`.
5. Click **OK**.

**Expected result:** A three-column table. `ProductKey` now has multiple rows because the same product can be purchased by multiple different customers.

6. Delete the **Grouped Rows** and any **Sort** applied steps to restore the original table.

---

## Part 5: Pivoting and Unpivoting Tables

### The Concept

- **Pivot** = turn **row values** into **columns** (vertical → horizontal).
- **Unpivot** = turn **columns** into **rows** (horizontal → vertical).

> **Mental model:** Imagine the table is attached to a hinge at its top-left corner. Pivoting rotates the table up from vertical to horizontal. Unpivoting rotates it back down.

```text
Wide (pivoted)                          Tall (unpivoted)
+------+--------+--------+--------+      +------+--------+----------+
| Date | North  | Central| South  |  --> | Date | Region | Quantity |
+------+--------+--------+--------+      +------+--------+----------+
| 1/1  |  100   |   80   |   60   |      | 1/1  | North  |   100    |
| 1/2  |  120   |   90   |   70   |      | 1/1  | Central|    80    |
+------+--------+--------+--------+      | 1/1  | South  |    60    |
                                         | 1/2  | North  |   120    |
                                         | 1/2  | Central|    90    |
                                         | 1/2  | South  |    70    |
                                         +------+--------+----------+
```

The wide table is easy for humans to read. The tall table is what Power BI prefers for slicers, filters, and visuals.

**Transpose** is a similar but cruder option. It literally flips the entire table on its side without recognizing distinct values — it does not deduplicate. Avoid Transpose when your data has repeating values.

---

### When to Unpivot

Datasets shared by multiple people often arrive in a "wide" format with one column per category or region. This is fine for human reading but awkward for analysis. The unpivoted (tall) format, with one column for the category name and one column for the value, is far easier to filter, slice, and visualize in Power BI.

> **Raw data reference:** The file `Product Category Sales (Unpivot Demo).csv` is a good example. It has columns `Date`, `Product Category`, `North Region`, `Central Region`, and `South Region`. Filtering by region is impossible in this format. After unpivoting the three region columns, you get a four-column table: `Date`, `Product Category`, `Region`, `Quantity Sold` — which is far more flexible.

---

## Exercise 4: Unpivot Region Columns

**Goal:** Transform the wide product-category sales table into a tall, analysis-ready format.

1. Connect to `Product Category Sales (Unpivot Demo).csv` and load it into Power Query Editor.
2. Hold **Ctrl** and click to select the `North Region`, `Central Region`, and `South Region` columns.
3. On the **Transform** ribbon, click **Unpivot Columns** > **Unpivot Columns**.
4. Two new columns appear: `Attribute` and `Value`.
5. Rename `Attribute` to `Region`.
6. Rename `Value` to `Quantity Sold`.

**Expected result:** A four-column table. Each original row with three region values is now three rows — one per region.

### Repivoting (for reference)

To reverse the operation:

1. Select the `Region` and `Quantity Sold` columns.
2. On the **Transform** ribbon, click **Pivot Column**.
3. In the dialog, set **Values Column** to `Quantity Sold` and click **OK**.

The table returns to its original wide format.

---

## Part 6: Merging Queries

**Merge** joins two tables horizontally based on a common key column — similar to a VLOOKUP in Excel or a JOIN in SQL. The result adds columns from the second table to the first.

> **Merging makes tables wider (adds columns).**

### When to Merge (and When Not To)

Merging is sometimes the right tool — for example, when you need a single flat table for export. However, in Power BI it is almost always **better to define a relationship between tables** in the data model instead of merging them in Power Query. Merging duplicates data from the second table into every matching row of the first table, inflating model size unnecessarily.

### Join Types

The most common join type is **Left Outer**: keep all rows from the first table, and bring in matching rows from the second table. Unmatched rows from the first table retain null values in the new columns.

---

## Exercise 5: Merge Sales Data with Product Lookup (Demo)

**Goal:** Demonstrate how to pull `ProductName` and `ProductColor` from `Product Lookup` into `Sales Data 2022`. *This exercise is for learning purposes — delete the merge step afterward.*

1. In Power Query Editor, select `Sales Data 2022`.
2. On the **Home** ribbon, click **Merge Queries** (not "Merge Queries as New").
3. In the merge dialog:
   - The first table is already set to `Sales Data 2022`.
   - Click the `ProductKey` column in the first table.
   - From the dropdown, select `Product Lookup`.
   - Click the `ProductKey` column in the second table.
   - The status bar should read: *"The selection matches 29,481 of 29,481 rows from the first table."*
   - Join type: `Left Outer (all from first, matching from second)`.
4. Click **OK**.
5. A new column named `Product Lookup` appears with the value `Table` in each cell.
6. Click the **double-arrow expand icon** in the `Product Lookup` column header.
7. Uncheck **Select All**, then check only `ProductName` and `ProductColor`.
8. Uncheck **Use original column name as prefix** if you prefer cleaner names.
9. Click **OK**.
10. Scroll right to confirm `ProductName` and `ProductColor` values appear for each row.
11. **Delete the Merged Queries applied step** to revert to the original table.

> **Why we delete this step:** In a real data model, you would use a relationship between `Sales Data` and `Product Lookup` instead of merging. Table relationships avoid data duplication and are far more efficient.

---

## Part 7: Appending Queries

**Append** stacks two or more tables vertically — adding rows. This requires both tables to share the same column structure and data types.

> **Appending makes tables taller (adds rows).**

---

### The Folder Connector: A Better Way to Append

Manually appending three individual queries creates a dependency chain — you cannot delete the component tables because the appended table references them. The solution is to connect to the **folder** containing the files rather than to each file individually.

When you connect to a folder:

- Power BI automatically appends all files in the folder.
- If you add new files to the folder later, refreshing the query brings them in automatically.
- Only one query is created — no dependency chain.

---

## Exercise 6a: Manual Append — Load 2020 and 2021, Then Append All Three (Demo)

**Goal:** See firsthand why manually appending creates a dependency chain, before learning the folder connector approach. *This exercise is intentionally suboptimal — you will undo it in Exercise 6.*

### Step 1 — Load the 2020 and 2021 sales CSVs

1. In Power Query Editor, click **New Source** > **Text/CSV**.
2. Browse to `AdventureWorks Raw Data` > `Sales Data` and select `AdventureWorks Sales Data 2021.csv`.
3. Click **Open** > **OK**.
4. In the Queries pane, rename the new query to `Sales Data 2021`.
5. Verify all column data types match `Sales Data 2022` (see the Part 0.5 table above).
6. Repeat steps 1–5 for `AdventureWorks Sales Data 2020.csv` and rename it to `Sales Data 2020`.

### Step 2 — Align the schema by removing the `Quantity Type` column from 2022

The three tables must have **identical column structures** to append. The `Sales Data 2022` table currently has an extra `Quantity Type` column from Exercise 1. Remove it now.

1. Select the `Sales Data 2022` table.
2. In the **Applied Steps** pane, find the step that added the `Quantity Type` column (likely named `Added Conditional Column`).
3. Click the **X** next to that step to delete it.
4. Confirm `Sales Data 2022` now has the same 8 columns as `Sales Data 2020` and `Sales Data 2021`.

### Step 3 — Append as New

1. With any of the three sales tables selected, on the **Home** ribbon click **Append Queries** > **Append Queries as New**.
2. In the dialog, choose **Three or more tables**.
3. The currently selected table is already in the right-side list. From the **Available table(s)** list on the left, hold **Shift** and click `Sales Data 2020` and `Sales Data 2021` to select both, then click **Add >>**.
4. Confirm all three tables (`Sales Data 2020`, `Sales Data 2021`, `Sales Data 2022`) appear in the right-side list.
5. Click **OK**.
6. A new query named `Append1` is created. Rename it to `Sales Data 2020 through 2022`.

### Step 4 — Verify the append

1. Click the dropdown arrow on the `OrderDate` column header. If you see "List may be incomplete," click **Load More**.
2. Confirm dates from 2020, 2021, and 2022 are present.

### Step 5 — Observe the dependency issue

1. In the Queries pane, right-click `Sales Data 2020` and try **Delete**.
2. You will see a message like *"This query is being referenced by another query and cannot be deleted."* — Power BI cannot remove this table because the appended query depends on it.
3. Click **Cancel**.

> **Takeaway:** This is exactly the problem the folder connector solves. In Exercise 6, we will delete all four of these sales queries and replace them with a single, dependency-free query that points to the folder.

---

## Exercise 6: Replace the Manual Append with the Folder Connector

**Goal:** Delete the four sales queries created in Exercise 6a, then load all three sales CSV files at once by connecting to the `Sales Data` folder.

### Step 1 — Delete the manual queries

1. In the Queries pane, right-click `Sales Data 2020 through 2022` and select **Delete** > **Delete** (confirm).
2. Now that the appended query is gone, the three component tables can be deleted. Right-click each of `Sales Data 2020`, `Sales Data 2021`, and `Sales Data 2022` and select **Delete**.
3. Confirm only your lookup tables (`Customer Lookup`, `Product Lookup`, `Calendar Lookup`, etc.) remain in the Queries pane (plus the `Demo Queries` folder).

### Step 2 — Connect to the folder

> **Before continuing:** Verify that the `Sales Data` folder inside `AdventureWorks Raw Data` contains only the three sales CSV files:
> - `AdventureWorks Sales Data 2020.csv`
> - `AdventureWorks Sales Data 2021.csv`
> - `AdventureWorks Sales Data 2022.csv`

1. In Power Query Editor, click **New Source** > **More**.
2. In the Get Data window, select **Folder** and click **Connect**.
3. Click **Browse** and navigate to the `Sales Data` subfolder.
4. Click **OK** in the folder path dialog.
5. A preview window shows the file list (not the data rows). Click **Transform Data**.
6. The query editor opens showing file metadata columns (`Content`, `Name`, `Extension`, `Date modified`, etc.).
7. Click the **Combine Files** icon (two overlapping pages) in the `Content` column header.
8. In the **Combine Files** dialog, review the column preview (should match the sales data structure). Click **OK**.
9. Power BI generates several helper queries automatically. These appear in the Queries pane — collapse or ignore them.
10. In the resulting table, locate the `Source.Name` column (which shows the filename each row came from). Right-click it and select **Remove Column** — you do not need the filename in the final model.
11. Verify the `OrderDate` column contains values from 2020, 2021, and 2022.
12. The query is automatically named `Sales Data`. Leave this name as is.
13. Click **Close & Apply**.

**Expected result:** A single `Sales Data` table with all rows from 2020, 2021, and 2022 combined — approximately 56,000+ rows total.

### Checkpoint ✅

- The `Sales Data` table contains `OrderDate` values from `2020-01-01` through `2022-06-30`.
- There are no extra individual year tables visible in the data model.
- Refreshing the query would automatically include any new files dropped into the folder.

---

## Part 8: Managing Data Source Settings

### How Power BI Stores File Paths

When you connect to a local file, Power BI records the **exact file path and file name**. If you rename the file, move it to a different folder, or change folder permissions, the connection breaks and you will see an error when refreshing.

This is one of the most common issues encountered in Power BI, especially when sharing files between machines or reorganizing project folders.

---

### Finding and Fixing a Broken Connection

**Method 1 — Data Source Settings dialog:**

1. In Power Query Editor, click **File** > **Data Source Settings** (or find it on the **Home** ribbon).
2. The dialog lists every active connection in your file.
3. Select the broken connection (it may show a warning icon).
4. Click **Change Source**.
5. Browse to the new location of the file.
6. Click **OK** > **Close**.
7. Refresh the query to confirm the error is resolved.

**Method 2 — Gear icon in Applied Steps:**

1. In Power Query Editor, click on the broken query.
2. In the Applied Steps pane, click the **gear icon** next to the `Source` step.
3. The source dialog opens — browse to the updated file location.
4. Click **OK**.

Both methods produce the same result. Method 2 is faster when you already know which query is broken.

> **All applied steps are preserved** when you update a broken source path. No transformation work is lost — only the source file pointer is updated.

---

### Checkpoint ✅

- You can open **Data Source Settings** and see a list of all connections.
- You know how to use **Change Source** to point a query to a renamed or moved file.
- You understand that renaming a connected file breaks the connection in Power BI.

---

## Part 9: Organizing the Query Editor with Groups

As you add more queries, the Queries pane can become cluttered. Use **groups** to organize queries into folders.

1. Hold **Ctrl** and click to select the queries you want to group (for example, all disabled-load demo queries).
2. Right-click and select **Move to Group** > **New Group**.
3. Enter a name for the group (for example, `Demo Queries`).
4. Click **OK**.

The selected queries are now collapsed under a folder in the Queries pane. This keeps your working queries clearly visible.

---

## Key Takeaways

- **Add columns as close to the source as possible** for the best model performance.
- **Index columns** create unique row identifiers — delete any exploratory index steps if you decide not to use them.
- **Conditional columns** evaluate rows top-to-bottom; always include an **Otherwise** clause as a safety catch.
- **Group By** reduces a table to a summary by aggregating a numeric column by one or more grouping columns. Fields not included are dropped.
- **Unpivot** converts wide tables (columns as categories) into tall tables (categories as rows), which is the preferred format for Power BI analysis.
- **Merge** adds columns (horizontal join). **Append** adds rows (vertical stack).
- The **folder connector** is the recommended way to append multiple files — it creates one clean query, scales automatically as new files are added, and avoids dependency chains.
- Power BI stores **exact file paths**. Renaming or moving a connected file breaks the connection. Use **Data Source Settings** or the **Source step gear icon** to repair it.
- Applied steps are preserved when fixing broken sources — no transformation work is lost.
