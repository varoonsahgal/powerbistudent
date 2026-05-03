# Handout 05: Query Refresh Management, Importing Excel Models, and Best Practices

## Purpose

This handout completes the connecting and shaping data section of the course. It covers three final topics: controlling which queries refresh when you click Refresh, importing a fully built Excel data model directly into Power BI, and a summary of best practices for connecting and shaping data.

---

## Prerequisites

- Power BI Desktop is open with the AdventureWorks tables already loaded (Sales Data, Customer Lookup, Product Lookup, Calendar Lookup, Territory Lookup, and subcategories).
- You have completed the Power Query work from the previous handouts.

> **Confirmed AdventureWorks table structure:**
> - `Sales Data`: `OrderDate`, `StockDate`, `OrderNumber`, `ProductKey`, `CustomerKey`, `TerritoryKey`, `OrderLineItem`, `OrderQuantity` — approximately 56,046 rows total across 2020–2022.
> - Lookup tables: `Customer Lookup`, `Product Lookup`, `Product Subcategories Lookup`, `Product Categories Lookup`, `Territory Lookup`, `Calendar Lookup`.

---

## Part 1: Managing Query Refresh

### How the Refresh Button Works by Default

In the main Power BI view, the **Refresh** button on the **Home** ribbon refreshes **every query** in the data model by default. When you click it, Power BI reloads every table — even tables whose source data never changes.

This is fine for small models, but as models grow, refreshing every table wastes time and resources.

---

### Excluding Queries from Report Refresh

Power Query provides a per-query setting called **Include in report refresh**. When this setting is turned off for a query, that query is skipped whenever Refresh is triggered from the Home tab.

**Where to find it:**

1. In Power Query Editor, locate the query in the **Queries** pane on the left.
2. Right-click the query name.
3. In the context menu, look for **Include in report refresh**.
   - A checkmark next to the option means it is currently enabled.
   - Clicking it toggles the setting off (no checkmark = excluded from refresh).

> **This setting is located just below Enable Load** in the right-click context menu.

---

### Which Tables Should Be Excluded?

A good rule of thumb: exclude any table whose source data does not change — or changes very rarely.

| Table type | Should it refresh? | Reasoning |
|---|---|---|
| Lookup tables (Product, Customer, Territory, Calendar) | No — exclude | These rarely change |
| Static reference tables | No — exclude | Source does not update |
| Sales or transaction data | Yes — include | This is the data that grows over time |
| Date tables loaded from CSV | No — exclude | The CSV is fixed |
| Rolling calendar (M code) | Consider including | It extends daily |

> **Analogy:** Refreshing all your lookup tables every time you want to see yesterday's sales numbers is like rebooting your entire computer every time you open a new browser tab. You want only the necessary work to happen.

---

## Exercise 1: Configure Selective Refresh for the AdventureWorks Model

**Goal:** Exclude all lookup tables and the rolling calendar from report refresh so that only the `Sales Data` table is updated when Refresh is triggered.

### Steps

1. In Power BI Desktop, click **Transform Data** to open Power Query Editor.
2. In the Queries pane, right-click `Territory Lookup`.
3. Click **Include in report refresh** to deselect it (the checkmark should disappear).
4. Repeat for each of the following queries:
   - `Product Lookup`
   - `Product Subcategories Lookup`
   - `Product Categories Lookup`
   - `Customer Lookup`
   - `Calendar Lookup`
   - `Rolling Calendar` (if present)
5. Leave `Sales Data` with **Include in report refresh** enabled (checkmark present).
6. Click **Close & Apply** to return to the main Power BI view.

### Verifying the Result

1. Back in the main Power BI view, click **Refresh** on the Home ribbon.
2. A progress indicator appears. Observe that **only the Sales Data table** is refreshing.
3. The refresh completes noticeably faster than when all tables were included.

**Expected result:** Only `Sales Data` refreshes. All lookup tables are skipped, and the data in those tables is unchanged.

---

### Checkpoint ✅

- All lookup tables have **Include in report refresh** deselected (no checkmark).
- `Sales Data` has **Include in report refresh** selected (checkmark present).
- Clicking **Refresh** from the Home tab refreshes only `Sales Data`.
- The refresh completes faster than a full-model refresh.

---

## Part 2: Importing an Excel Data Model into Power BI

### The Concept

Power BI provides a dedicated **Import** workflow — separate from **Get Data** — that can bring a fully built Excel data model directly into Power BI Desktop. This includes:

- Data source connections and file paths
- All applied Power Query transformation steps
- Relationships between tables
- Hierarchies and field formatting settings
- Calculated columns and DAX measures

This is not the same as using **Get Data > Excel**. The Import workflow reads the entire internal model from an Excel workbook and recreates it inside Power BI.

### What Does NOT Transfer

Excel workbooks built with Power Pivot often include pivot tables, conditional formatting, data bars, and KPI icon visualizations built on top of the data model. None of these transfer. The import captures the data model only — not the Excel sheet layouts or visualizations.

> **The equivalent of pivot tables in Power BI is the Matrix visual**, which is similar but not identical. You will rebuild the visualizations in Power BI after importing.

---

### When This Is Useful

If you or your team are more comfortable building data models in Excel (using Power Query, Power Pivot, and DAX), you can build the full model there and then import it into Power BI for the reporting and visualization phase. The end result — a clean data model with measures and relationships — is the same regardless of which environment it was built in.

---

## Exercise 2: Import an Excel Data Model

**Goal:** Import a fully built Excel data model (including queries, relationships, and DAX measures) into a new Power BI Desktop file.

> **Note:** This exercise is a demonstration. The Excel model referenced (`FoodMartDataModelCOMPLETE`) is from a separate course. No AdventureWorks raw data files are used here. Follow along conceptually or apply the same steps to any Excel workbook that contains a Power Pivot data model.

### Steps

1. Open a new, blank Power BI Desktop file.
2. Close the startup splash screen if it appears.
3. **Do not use Get Data.** Instead, go to **File** > **Import** > **Power Query, Power Pivot, Power View**.

   > **This is the key difference from Get Data.** The File > Import path reads the entire model structure, not just the data.

4. Browse to the Excel workbook file and select it.
5. A dialog appears:

   > *"We don't directly work with Excel workbook contents, but we know how to extract the useful content so that you can work with it in Power BI Desktop."*

   Click **Start**.

   > **Troubleshooting:** If the import returns a "Migration failed — file is already open" error, close the Excel file in Excel first, then click **Retry**.

6. A second dialog may appear asking how to handle cell-range tables (tables created directly in Excel cells, not in the data model). Choose **Copy Data** to bring the data into Power BI without keeping a live connection back to Excel.
7. Wait for the import to complete. A summary window lists everything that was migrated — tables, KPIs, measures, and connections.
8. Click **Close** on the summary window.

### Reviewing the Imported Model

After import:

- Go to the **Report view** to see all tables listed in the **Fields** pane.
- Go to the **Data view** to browse each table's contents.
- Go to the **Model view** to see all table relationships — the diagram will look messy immediately after import. You can reorganize it by dragging tables.
- Click into any measure in the Fields pane to confirm it has been imported with its original DAX formula intact.

**Expected result:** The imported model contains all tables, relationships, and DAX measures from the Excel workbook, ready for use in Power BI. Nothing needs to be recreated from scratch.

---

### Checkpoint ✅

- All tables from the Excel workbook appear in the Fields pane.
- The Model view shows the relationships between tables.
- DAX measures are visible in the Fields pane and contain their original formulas.
- No pivot table views or Excel sheet layouts are present — only the data model.

---

## Part 3: Best Practices for Connecting and Shaping Data

This section summarizes three key best practices that apply throughout the connecting and shaping data phase.

---

### Best Practice 1: Get Organized Before Loading Data

Define clear, intuitive table names before you start building. Renaming tables after you have built visuals, relationships, and DAX measures creates a ripple effect — every reference to the old table name must be found and updated.

Similarly, establish a stable folder structure for your data files before building your Power BI report. Because Power BI stores **exact file paths**, moving or renaming a connected file later means updating the source path in **Data Source Settings** for every affected query.

> **Practical habit:** Create a dedicated project folder with a `Data` subfolder before you connect anything. Name your files clearly from the start. This eliminates most data source path errors.

---

### Best Practice 2: Disable Report Refresh for Static Sources

There is no benefit to refreshing tables that never change. As shown in Part 1, use **Include in report refresh** to exclude lookup tables, calendar tables, and any other static sources from the refresh cycle.

Only the tables that actually receive new data — typically transaction or sales tables — should refresh. This keeps your refresh time proportional to the amount of data that actually changes.

---

### Best Practice 3: Only Load the Data You Need

More data does not mean better analysis. Loading unnecessary columns, overly granular rows, or data outside the relevant time window increases model size, slows queries, and uses more memory.

Before loading a table, ask:

- Do the visuals you plan to build actually need this column?
- Does the report need hourly data, or is daily sufficient?
- Does the report need row-level transaction detail, or will a pre-aggregated summary do?

> **Example:** If your report shows sales performance by month and by product category, you do not need the `StockDate` column, individual `OrderLineItem` numbers, or data from before the reporting period. Removing them in Power Query before loading reduces model size with no impact on the final report.

> Nothing in a data model is permanent. You can always add more data later. The cost of loading too little is a one-time query change. The cost of loading too much can be slow reports, failed refreshes, and high memory usage that compounds over time.

---

### Summary Table

| Best Practice | Quick Rule |
|---|---|
| **Organize before loading** | Set clear table names and a stable folder structure before connecting any data |
| **Selective refresh** | Exclude static lookup and calendar tables from report refresh |
| **Load only what you need** | Remove unneeded columns and rows in Power Query before Close & Apply |

---

## Key Takeaways

- The **Refresh** button on the Home ribbon refreshes every query by default. Use **Include in report refresh** (right-click in the Queries pane) to limit refreshes to only the tables that change.
- Excluding static tables from refresh reduces total refresh time with no impact on data accuracy.
- **File > Import > Power Query, Power Pivot, Power View** imports an entire Excel data model — including queries, relationships, and DAX measures — into a new Power BI file. This is fundamentally different from **Get Data > Excel**.
- Excel pivot tables, conditional formatting, and visual layouts do **not** transfer during the import. Only the data model structure transfers.
- Close the Excel workbook in Excel before importing it into Power BI, or the migration will fail.
- Get organized before loading: stable table names and a clear folder structure prevent most data source path errors.
- Only load the data the report actually needs — extra columns and rows inflate model size and slow performance.
- These three practices — organization, selective refresh, and minimal loading — together define a clean, efficient, maintainable Power BI data model.

---

*This handout completes the **Connecting and Shaping Data** section of the course. The next section covers creating table relationships and working in the Power BI data model view.*
