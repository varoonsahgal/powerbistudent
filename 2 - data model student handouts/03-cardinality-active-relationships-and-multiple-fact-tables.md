# Handout 03: Cardinality, Active vs. Inactive Relationships, and Multiple Fact Tables

## Purpose

This handout covers three closely related modeling topics: **relationship cardinality** (the uniqueness of values on each side of a relationship), **active vs. inactive relationships** (when more than one possible relationship exists between two tables), and how to integrate a **second fact table** (`Returns Data`) into an existing model through shared dimension tables.

---

## Prerequisites

- Completion of Handout 02.
- The AdventureWorks model has a working star schema plus the snowflake extension for product tables.

---

## Part 1: Active vs. Inactive Relationships

### The Single-Active-Path Rule

Power BI allows **only one active relationship** between any two tables at a time. When two foreign-key candidates exist (for example, both `OrderDate` and `StockDate` could connect to `Calendar Lookup[Date]`), one becomes the active relationship and the other becomes inactive.

| State | Visual indicator |
|---|---|
| **Active** | Solid relationship line |
| **Inactive** | Dotted relationship line |

### Why Inactive Relationships Exist

- They preserve the option to switch quickly without recreating relationships.
- Advanced DAX functions like `USERELATIONSHIP()` can temporarily activate them inside a measure. (Covered later in the DAX section of the course.)

### Switching the Active Relationship

You **must deactivate the current active relationship before activating another**. Trying to activate two relationships to the same primary key at once produces an error.

---

## Exercise 1: Switch the Active Calendar Relationship

**Goal:** Add `StockDate` as a second relationship to `Calendar Lookup`, then practice switching which one is active.

### Steps

1. In **Model** view, drag `StockDate` from `Sales Data` onto `Date` in `Calendar Lookup`.
2. The new relationship appears as a **dotted line** (inactive) — Power BI automatically keeps `OrderDate` as the active relationship.
3. Double-click the new dotted line to open the Edit Relationship dialog.
4. Try checking **Make this relationship active**.

   **Expected result:** An error message:
   > *"You can't set this relationship to be active because it would create ambiguity between tables. To make this relationship active, deactivate one of the other relationships connecting these two tables."*

5. Click **Cancel**.
6. Double-click the **active** (solid line) `OrderDate` relationship.
7. Uncheck **Make this relationship active** and click **OK**.
8. Now both relationships are inactive. Double-click the `StockDate` relationship and check **Make this relationship active**.
9. To restore the original setup:
   - Select the active `StockDate` relationship.
   - In the **Properties** pane, toggle **Active** off and click **Apply changes**.
   - Select the inactive `OrderDate` relationship.
   - Toggle **Active** on and click **Apply changes**.

**Expected result:** The active calendar relationship is back on `OrderDate`. `StockDate` remains as a dotted-line inactive relationship.

> **Tip:** The Properties pane offers an alternative way to toggle active state — useful when both relationships should be left in place but their roles need to be swapped occasionally.

---

## Part 2: Cardinality

**Cardinality** describes how unique values are on each side of a relationship. There are three types.

### One-to-Many (the Default)

Most relationships in a healthy model are **one-to-many**:

- The dimension side has **one** instance of each key value.
- The fact side has **many** instances.

In Power BI, the cardinality is shown on the relationship line:

- `1` on the dimension side
- `*` (asterisk) on the fact side

This is the ideal default. Every relationship in the AdventureWorks model is one-to-many.

---

### One-to-One

Both sides have unique values for the matching key.

- A `Product Lookup` table and a separate `Price Lookup` table, each with a unique `ProductID` per row.
- Connecting them with `ProductID` produces a one-to-one relationship.

> **One-to-one is usually a code smell.** If two tables have a one-to-one relationship and both describe the same entity, they should generally be **merged** into a single normalized dimension table. Merging here is acceptable because there is no row duplication — every row remains unique and the table still describes a single concept.

---

### Many-to-Many

Both sides have duplicate values for the matching key.

Example: a `Product Lookup` table that accidentally has two rows with `ProductID = 4` (because they describe two different products that share an ID by mistake) joined to a `Sales` table with many transactions per product.

> **Many-to-many is usually a data integrity problem.** Even if Power BI allows the relationship, the analysis is meaningless: if `ProductID = 4` could refer to either "Washington Cream Soda" or "Washington Diet Cream Soda", a sale of `ProductID = 4` cannot be unambiguously tied to a single product.

> **Best practice:** Solve many-to-many at the source by deduplicating the dimension table or introducing a bridge table. Avoid the many-to-many cardinality option in Power BI unless you have a specific, well-understood reason.

---

## Exercise 2: Verify Cardinality of Existing Relationships

**Goal:** Confirm that every relationship in the AdventureWorks model is one-to-many.

### Method 1 — Visual Inspection

In **Model** view, check each relationship line:

- The **dimension side** should show `1`.
- The **fact side** should show `*`.

This includes:

- `Territory Lookup` → `Sales Data`
- `Customer Lookup` → `Sales Data`
- `Calendar Lookup` → `Sales Data`
- `Product Lookup` → `Sales Data`
- `Product Categories` → `Product Subcategories`
- `Product Subcategories` → `Product Lookup`

### Method 2 — Properties Pane

1. Click any relationship line.
2. In the **Properties** pane, view the **Cardinality** dropdown — it should display `Many to one (*:1)` or `One to many (1:*)` depending on which direction you read it from.

**Expected result:** Every relationship is `1:*` or `*:1`.

---

## Part 3: Multiple Fact Tables and Shared Dimensions

A real-world model often has more than one fact table. AdventureWorks has both `Sales Data` and `Returns Data`.

### Why You Cannot Connect Fact Tables Directly

`Sales Data` and `Returns Data` both have `ProductKey`, `TerritoryKey`, and date columns — but **all of these are foreign keys with duplicates** in both tables. Trying to relate them directly produces a many-to-many cardinality error.

### The Correct Pattern

Connect each fact table to **shared dimension tables**. Filters applied to a dimension table flow downstream to every related fact table simultaneously.

### Shared vs. Non-Shared Dimensions

A dimension is **shared** if it has relationships to multiple fact tables. The implications:

- Filtering on a **shared** dimension correctly affects values from all connected fact tables.
- Filtering on a dimension connected to only **one** fact table affects values from only that fact table. Other fact tables will display the same repeating grand total — the same telltale sign of a missing relationship.

In AdventureWorks:

| Dimension | Connects to Sales? | Connects to Returns? | Shared? |
|---|---|---|---|
| `Territory Lookup` | Yes | Yes | Shared |
| `Calendar Lookup` | Yes | Yes | Shared |
| `Product Lookup` | Yes | Yes | Shared |
| `Customer Lookup` | Yes | **No** (no `CustomerKey` in returns) | Sales only |

> **Raw data confirmation:** `AdventureWorks Returns Data.csv` contains `ReturnDate`, `TerritoryKey`, `ProductKey`, and `ReturnQuantity`. There is no customer key in returns. This is realistic — many real-world return systems track what was returned and where, but not who returned it.

---

## Exercise 3: Load and Connect the Returns Data Table

**Goal:** Add `Returns Data` to the model as a second fact table connected to all three shared dimensions.

### Step 1 — Load the Returns Data CSV

1. In Power BI, on the **Home** ribbon, click **Get data** > **Text/CSV**.
2. Browse to the `AdventureWorks Raw Data` folder and select `AdventureWorks Returns Data.csv`.
3. In the preview window, click **Transform Data**.
4. In Power Query Editor:
   - Confirm the first row is promoted as headers.
   - Confirm data types: `ReturnDate` = Date, `TerritoryKey` = Whole Number, `ProductKey` = Whole Number, `ReturnQuantity` = Whole Number.
   - Rename the query to `Returns Data`.
5. Click **Close & Apply**.

### Step 2 — Position the Table

1. Switch to **Model** view.
2. Drag the new `Returns Data` table to a position next to `Sales Data`, beneath the dimension tables.

### Step 3 — Try (and Fail) to Connect Sales to Returns Directly

1. Try dragging `ProductKey` from `Returns Data` onto `ProductKey` in `Sales Data`.
2. Power BI will warn that this would create a many-to-many cardinality.
3. Cancel the relationship — fact-to-fact connections are not the right pattern.

### Step 4 — Connect Returns to Each Shared Dimension

Create three relationships:

| Foreign key | Primary key | Dimension table |
|---|---|---|
| `Returns Data[ProductKey]` | `ProductKey` | `Product Lookup` |
| `Returns Data[ReturnDate]` | `Date` | `Calendar Lookup` |
| `Returns Data[TerritoryKey]` | `SalesTerritoryKey` | `Territory Lookup` |

**Expected result:** Three new one-to-many relationships connect `Returns Data` to its shared dimensions. No relationship exists between `Returns Data` and `Customer Lookup` because there is no shared key.

---

## Exercise 4: Validate Returns Through Shared and Non-Shared Dimensions

**Goal:** Confirm that filtering through shared dimensions works for both fact tables, and that filtering through customer (non-shared) only works for sales.

1. Switch to **Report** view and edit the matrix visual.
2. Add `ReturnQuantity` from `Returns Data` to the **Values** well alongside `OrderQuantity`.
3. Test each of the following fields one at a time in **Rows**:

   | Field | Source | Expected behavior |
   |---|---|---|
   | `Country` | `Territory Lookup` (shared) | Both quantities show correctly |
   | `ProductStyle` | `Product Lookup` (shared) | Both quantities show correctly |
   | `EducationLevel` | `Customer Lookup` (sales-only) | `OrderQuantity` shows correctly; `ReturnQuantity` shows the same repeating total on every row |

> **The repeating total again.** When `EducationLevel` is on rows, `Returns Data` has no awareness of customer education levels — there is no relationship — so the filter context cannot reach the returns table. The result is the classic missing-relationship signature.

---

## Checkpoint ✅

- [ ] `Returns Data` is loaded and positioned next to `Sales Data` in the model.
- [ ] Three relationships connect `Returns Data` to `Product Lookup`, `Calendar Lookup`, and `Territory Lookup`.
- [ ] No relationship exists between `Returns Data` and `Customer Lookup`.
- [ ] Every relationship in the model is one-to-many.
- [ ] You can articulate why `Returns Data` and `Sales Data` should not be connected directly.

---

## Key Takeaways

- Only **one active relationship** can exist between two tables at a time. Inactive relationships appear as dotted lines.
- You must deactivate an existing relationship before activating a competing one.
- **One-to-many** cardinality is the standard for a healthy model.
- **One-to-one** usually indicates two tables that should be merged.
- **Many-to-many** usually indicates a data integrity problem and should be solved at the source.
- Multiple fact tables connect to **shared dimension tables**, never directly to each other.
- A dimension that connects to only one fact table cannot filter the others — the missing-relationship signature (repeating totals) will appear.

---

## Raw Data Reference

| File | Columns used in this handout |
|---|---|
| `AdventureWorks Returns Data.csv` | `ReturnDate`, `TerritoryKey`, `ProductKey`, `ReturnQuantity` |
| `AdventureWorks Sales Data 2020/2021/2022.csv` | `OrderDate`, `StockDate`, `ProductKey`, `CustomerKey`, `TerritoryKey`, `OrderQuantity` |
