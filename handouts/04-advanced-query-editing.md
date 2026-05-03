# Handout 04: Advanced Query Editing — Index Columns, Conditional Columns, Group By, Pivot/Unpivot, Merge, Append, and Data Source Settings

## Purpose

This handout covers the more advanced Power Query capabilities used to shape and combine data: conditional logic columns, index columns, aggregation with Group By, structural transformations with Pivot and Unpivot, combining tables with Merge and Append, the folder connector, and managing data source connections.

---

## Prerequisites

- The `Sales Data 2022` CSV has been loaded and renamed in Power Query Editor.
- `Customer Lookup`, `Product Lookup`, and `Calendar Lookup` tables are already loaded.
- The three sales CSV files (`2020`, `2021`, `2022`) are in a subfolder called `Sales Data` inside the `AdventureWorks Raw Data` folder.

> **Starting state of the Sales Data tables:**  
> `AdventureWorks Sales Data 2020/2021/2022.csv` each contain:  
> `OrderDate`, `StockDate`, `OrderNumber`, `ProductKey`, `CustomerKey`, `TerritoryKey`, `OrderLineItem`, `OrderQuantity`

---

## Part 1: Calculated Column Best Practices

Before diving into the tools, it is important to understand *where* to create calculated columns for the best performance.

### The Priority Order

| Priority | Location | Why |
|---|---|---|
| 1 (Best) | **Source system** (SQL, Excel, etc.) | Uses the source engine; no Power BI overhead |
| 2 | **Power Query Editor** | Included in Vertipaq's query plan for optimal compression |
| 3 | **Power BI front end (DAX)** | Not part of the query plan; can increase model size |
| 4 (Worst) | **Published report layer** | Furthest from the data; most overhead |

> **Why Power Query is better than DAX for static columns:** When you load data using Import mode, Power BI's internal engine (called **Vertipaq**) creates a query plan that determines the best way to compress and store the data in memory. Columns added in Power Query are part of that plan. Columns added later through DAX are not, which can disproportionately increase the size of the data model.
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

## Exercise 6: Load All Sales Data via the Folder Connector

**Goal:** Load all three sales data CSV files at once by connecting to the `Sales Data` folder.

> **Before starting:** Verify that the `Sales Data` folder inside `AdventureWorks Raw Data` contains only the three sales CSV files:
> - `AdventureWorks Sales Data 2020.csv`
> - `AdventureWorks Sales Data 2021.csv`
> - `AdventureWorks Sales Data 2022.csv`

### Steps

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
