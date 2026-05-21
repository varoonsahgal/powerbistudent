# Handout 05: Model Layouts, Data Formats, Categories, and Hierarchies

## Purpose

This final data-modeling handout covers the remaining polish that turns a working model into a presentable one: **custom model layouts** for organizing larger models, **data formats** (how values appear in the front end), **data categories** (how Power BI interprets geospatial fields), and **hierarchies** (drill-down field groups). It closes with the section's recap of data modeling best practices.

---

## Prerequisites

- Completion of Handouts 01–04.
- The AdventureWorks model has both fact tables (`Sales Data`, `Returns Data`), the snowflake product chain, and all foreign keys hidden in fact tables.

---

## Part 1: Custom Model Layouts

### What They Are

**Model layout tabs** allow custom diagrams that show a subset of the full model. Layouts are organizational only — they do **not** duplicate tables. A change made to a table in any layout syncs across every layout.

Use layouts when a model becomes large enough that the full diagram is hard to read, or when different teams need to focus on different subsets of the model.

---

## Exercise 1: Create Sales-Only and Returns-Only Layouts

**Goal:** Add two custom layout tabs that focus on the sales and returns sub-models respectively.

### Step 1 — Add a New Layout

1. In **Model** view, locate the layout tabs at the bottom of the canvas. The default tab is **All Tables**.
2. Click the **+** sign to add a new tab.

### Step 2 — Build the Sales Layout the Manual Way

1. From the **Data** pane on the right, drag `Sales Data` onto the canvas of the new tab.
2. Drag `Customer Lookup` onto the canvas. The existing relationship line is preserved automatically.
3. Right-click `Customer Lookup` and select **Remove from diagram** (not **Delete from model** — that would delete the table everywhere).

### Step 3 — Use Add Related Tables

1. Right-click an empty area of the canvas.
2. Select **Add related tables**.
3. Power BI pulls in every table directly related to `Sales Data` automatically: `Product Lookup`, `Calendar Lookup`, `Customer Lookup`, `Territory Lookup`.
4. Double-click the layout tab name and rename it to `Sales Model`.

### Step 4 — Build the Returns Layout

1. Click **+** to add a third layout tab.
2. Drag `Returns Data` onto the canvas.
3. Right-click an empty area → **Add related tables**.
4. Only `Product Lookup`, `Territory Lookup`, and `Calendar Lookup` appear. (No customer relationship exists.)
5. Rename the tab to `Returns Model`.

### Step 5 — Verify Sync

1. Switch to the `Sales Model` layout.
2. Click `Sales Data`, then in the **Properties** pane, toggle visibility of `CustomerKey` to unhidden.
3. Switch to the **All Tables** layout. The change is reflected there too.
4. Restore the original hidden state.

### Step 6 — Clean Up (Optional)

For the AdventureWorks course project the additional layouts are not needed long-term:

1. Right-click the `Sales Model` and `Returns Model` tabs and delete them, or click the **X** on each tab.

**Expected result:** Only the **All Tables** layout remains. The model is unchanged.

---

## Part 2: Data Formats vs. Data Types

### The Distinction

| Concept | Where defined | What it controls |
|---|---|---|
| **Data type** | Power Query Editor | How data is **stored** (date, integer, text, decimal, etc.) |
| **Data format** | Power BI front end (Data view or Model view) | How data is **displayed** to report users |

The same value can have a single underlying type but multiple possible formats — for example, a date stored as `2022-06-30` can be displayed as `6/30/2022`, `Thursday, June 30, 2022`, or `Jun-22`.

### Where to Set Formats

- **Data view** → select a column → use the **Column tools** ribbon. (Most user-friendly.)
- **Model view** → select a field → use the **Properties** pane → **Format** dropdown.

---

## Exercise 2: Apply Short Date Format to All Date Columns

**Goal:** Replace verbose long-date displays with concise short dates throughout the model.

### Steps

1. Switch to **Data** view.
2. Open the `Calendar Lookup` table.
3. Select the `Date` column.
4. On the **Column tools** ribbon, in the **Format** dropdown, select **Short Date**.
5. Repeat for every date-typed column in `Calendar Lookup`:
   - `Start of Week`
   - `Start of Month`
   - `Start of Quarter`
   - `Start of Year`
6. Open `Sales Data` and apply Short Date format to:
   - `OrderDate`
   - `StockDate`
7. Open `Returns Data` and apply Short Date format to:
   - `ReturnDate`

> **Reminder:** This format change affects only the display. The underlying data is unchanged. Power BI shows a small popup confirming this when you change a format.

**Expected result:** All date columns display as `M/D/YYYY` rather than `Thursday, June 30, 2022`.

---

## Exercise 3: Adjust Currency Decimal Places

**Goal:** Limit currency columns to two decimal places.

### Steps

1. In **Data** view, open `Product Lookup`.
2. Scroll to the rightmost columns: `ProductCost`, `ProductPrice`, and `Discount Price` (if present from earlier work).
3. Select `ProductCost`.
4. On the **Column tools** ribbon, confirm the format is **Currency**.
5. Set the **Decimal places** value to **2**.
6. Repeat for `ProductPrice` and `Discount Price`.

**Expected result:** Currency columns now display values like `$13.09` instead of `$13.0863`.

> **Raw data note:** `ProductCost` values in `AdventureWorks Product Lookup.csv` are stored to four decimal places (e.g., `13.0863`). Reducing display precision does not affect the stored value or any underlying calculations.

---

## Part 3: Data Categories

### What They Are

Some fields have semantic meaning that Power BI can use intelligently — addresses, cities, countries, latitude/longitude, web URLs, image URLs, even barcodes. Marking a column with the correct **data category** unlocks behavior such as accurate auto-mapping in geospatial visuals.

### Where to Set the Category

- **Data view** → select column → **Column tools** → **Data category** dropdown.
- **Model view** → select field → Properties pane → expand **Advanced** → **Data category** dropdown.

---

## Exercise 4: Categorize Geospatial Fields in Territory Lookup

**Goal:** Tell Power BI that the `Country` and `Continent` columns contain geospatial data.

### Steps

1. Switch to **Data** view and open `Territory Lookup`.
2. Select the `Country` column.
3. On the **Column tools** ribbon, in the **Data category** dropdown, scroll to find **Country**.

   > **Watch out:** The first option in the alphabetical list is **County** (a sub-national region) — not what you want. Scroll past it to find **Country/Region** (sometimes labeled simply `Country`).

4. Select **Country**.
5. Select the `Continent` column.
6. In the **Data category** dropdown, choose **Continent**.
7. The `Region` column in this dataset uses custom region names ("Northwest", "Northeast", etc.) that are not standard Power BI categories. Leave this column with no data category.

### Verification

1. Return to **Model** view and select `Territory Lookup`.
2. The `Country` and `Continent` fields now display a small **globe icon** beside their names, confirming the geospatial categorization.

**Expected result:** Geospatial visuals (covered in later sections) will be able to map these fields automatically without prompting for additional disambiguation.

---

## Part 4: Hierarchies

### What They Are

A **hierarchy** is a group of related columns representing increasing levels of detail. Power BI treats a hierarchy as a single object that supports drill-up and drill-down behavior in visuals.

Common hierarchy patterns:

| Hierarchy | Levels |
|---|---|
| Geography | Continent → Country → Region |
| Date | Year → Quarter → Month → Week → Day |
| Product | Category → Subcategory → Product |

```text
   Territory Hierarchy
   ===================
     Continent             <-- top level (least detail)
       |
       +-- Country
              |
              +-- Region   <-- bottom level (most detail)

   Drill-down  ▼  goes deeper (more rows, more detail)
   Drill-up    ▲  goes higher (fewer rows, more summary)
```

### Where to Create Hierarchies

Anywhere the **Data pane** is visible — Model view, Data view, or Report view all work.

### How They Behave in Visuals

When a hierarchy is placed on a matrix or chart axis, the visual displays the top level first. Drill-down arrows appear on the visual header allowing you to move to deeper levels, drill back up, or expand the entire hierarchy as a nested view (similar to a pivot table).

---

## Exercise 5: Create a Territory Hierarchy

**Goal:** Build a `Territory Hierarchy` with three levels: `Continent` → `Country` → `Region`.

### Steps

1. In **Model** view, expand `Territory Lookup` in the **Data** pane.
2. Right-click the highest-level field (`Continent`) and select **Create hierarchy**.
3. A new hierarchy object appears under the table. Double-click and rename it to `Territory Hierarchy`.
4. Expand the hierarchy — it currently contains only `Continent`.
5. Add `Country` to the hierarchy. Two equivalent methods:
   - Right-click `Country` → **Add to hierarchy** → `Territory Hierarchy`.
   - Drag `Country` onto the hierarchy node.
6. Drag `Region` into the hierarchy as the third level.

> **Field instances:** Adding a field to a hierarchy creates an additional reference. The original field is still accessible separately for filters or other visuals.

### Verify in a Visual

1. Switch to **Report** view and edit the matrix visual.
2. Remove `ProductKey` from rows.
3. Drag `Territory Hierarchy` to **Rows**.
4. By default the matrix shows only `Continent`.
5. In the visual header, use the drill-down arrows to descend through `Country` and `Region`.
6. Use the expand-down arrow to display all levels nested in a single view.
7. Use the drill-up arrow to return.

**Expected result:** A single hierarchy field replaces three individual fields and provides interactive drill behavior.

---

## Exercise 6 (Assignment): Create a Date Hierarchy

**Scenario:**

> *"Hoping you can help with a quick request. Since we'll be doing a lot of time series analysis, Ethan asked us to add a date hierarchy to the model so that users can quickly view trends at any level of granularity (year, month, day, etc.) Please get that added before our afternoon call. Thanks! — Dana"*

### Objectives

1. Create a new hierarchy named `Date Hierarchy` containing the following fields, in this order:
   - `Start of Year`
   - `Start of Month`
   - `Start of Week`
   - `Date`
2. Add the hierarchy to the matrix visual on rows.
3. Practice drilling up and down between levels.

### Steps

1. In **Model** view, expand `Calendar Lookup` in the **Data** pane.
2. Right-click `Start of Year` and select **Create hierarchy**.
3. Double-click the new hierarchy and rename it to `Date Hierarchy`.
4. Add the remaining three fields by dragging them into the hierarchy in order:
   - `Start of Month`
   - `Start of Week`
   - `Date` (the primary key of the calendar table)
5. Switch to **Report** view and edit the matrix visual.
6. Remove `Territory Hierarchy` from rows.
7. Drag `Date Hierarchy` from `Calendar Lookup` to **Rows**.

### Verify

1. The matrix initially shows yearly totals.
2. Use the **drill-down** arrow to descend to month, then week, then day.
3. Use the **expand all** option to display every level simultaneously.
4. Use the **drill-up** arrow to return to higher levels.

**Expected result:** A reusable date hierarchy supports flexible time-series analysis. The same hierarchy will be useful in every later visual that involves dates.

---

## Part 5: Section Best Practices and Recap

### Four Principles for a Healthy Model

1. **Build a normalized model from the start.** Each table should serve a single, clear purpose. Use relationships rather than merges to combine information.
2. **Place dimension tables above fact tables in the model canvas.** This is a visual reminder that filter context flows downward.
3. **Avoid complex relationships unless necessary.** Default to one-to-many cardinality and one-way (single) cross-filter direction. Bi-directional filtering and many-to-many cardinality are advanced features that should only be used with deliberate intent.
4. **Hide foreign keys in fact tables from report view.** This forces report authors to filter using primary keys from dimension tables — preventing the most common source of subtle filter-context bugs.

---

## Final Checkpoint ✅

- [ ] All date and currency columns display in user-friendly formats.
- [ ] `Country` and `Continent` are categorized as geospatial fields.
- [ ] `Territory Hierarchy` (Continent → Country → Region) and `Date Hierarchy` (Year → Month → Week → Day) exist.
- [ ] All foreign keys in fact tables are hidden.
- [ ] All relationships use one-to-many cardinality and one-way cross-filter direction.
- [ ] You can describe each of the four best-practice principles in your own words.

---

## Key Takeaways

- **Model layouts** organize larger models without duplicating data — changes sync across all layouts.
- **Data types** affect storage; **data formats** affect display.
- **Data categories** unlock semantic features such as automatic geocoding for visuals.
- **Hierarchies** group related fields into a single drill-aware object — a polished alternative to pulling individual fields onto a visual.
- **One-way filters** and **one-to-many cardinality** should be the default. Hide fact-table foreign keys to enforce correct filter behavior.

---

## Raw Data Reference

| File | Used for |
|---|---|
| `AdventureWorks Calendar Lookup.csv` | Source of `Date` and the derived columns used in the date hierarchy |
| `AdventureWorks Territory Lookup.csv` | Source of `Continent`, `Country`, and `Region` for the territory hierarchy |
| `AdventureWorks Product Lookup.csv` | Source of `ProductCost` and `ProductPrice` decimal-precision examples |
