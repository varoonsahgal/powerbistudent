# Handout 01: Data Modeling Foundations

## Purpose

This handout introduces the foundational concepts behind every Power BI data model: what a data model actually is, why normalization matters, the difference between fact tables and dimension tables, and how primary and foreign keys form the basis of all table relationships.

---

## Prerequisites

- The AdventureWorks tables from Power Query are loaded into Power BI Desktop:
  - `Sales Data`, `Customer Lookup`, `Product Lookup`, `Product Subcategories`, `Product Categories`, `Territory Lookup`, `Calendar Lookup`.
- No relationships have been created yet.

---

## Part 1: What Is a Data Model?

A **data model** is a collection of tables connected by **relationships** built on shared columns (keys). Without relationships, tables behave as independent islands — even if they live in the same Power BI file.

### A Collection of Tables Is Not a Data Model

Imagine three tables — `Product Lookup`, `Sales Data`, and `Returns Data` — sitting next to each other with no connections. The `Sales Data` table has no awareness that the other two tables exist.

If you try to display `OrderQuantity` from `Sales Data` and break it down by `ProductName` from `Product Lookup`, you will see the **same grand total repeated on every row**. That repeating-total pattern is the classic visual signal of a missing or invalid relationship.

### A Data Model Is a Set of Connected Tables

When the same three tables are connected by relationships through shared keys, the same query produces accurate per-product values. This is the power of a relational data model: data from many sources can be analyzed in one place without physically merging tables or writing complex lookup formulas.

---

## Part 2: Visualizing the "No Model" Problem

This exercise demonstrates what happens when you analyze data across tables that have no relationships. You will return to this matrix throughout the section to confirm your relationships are working.

---

## Exercise 1: Confirm There Is No Model Yet

**Goal:** Verify that the loaded tables have no relationships and observe the resulting analysis problem.

1. Open the AdventureWorks Power BI file.
2. Click the **Model** view icon on the left navigation bar.
3. Confirm all seven tables are visible: `Sales Data`, `Customer Lookup`, `Product Lookup`, `Product Subcategories`, `Product Categories`, `Territory Lookup`, `Calendar Lookup`.
4. There should be **no relationship lines** connecting any of the tables.

**Expected result:** Seven independent tables with no connecting lines.

---

## Exercise 2: Create a Diagnostic Matrix Visual

**Goal:** Build a matrix visual that exposes the missing-relationship problem.

1. Switch to **Report** view.
2. On the **Insert** ribbon, click **Matrix**. (In older Power BI versions, find the matrix icon in the Visualizations pane on the right — it looks like a small table with blue shading.)
3. From the **Data** pane, expand `Sales Data` and drag `OrderQuantity` into the **Values** well.
   - The value automatically aggregates to a sum of `84,174`.
4. From the **Data** pane, expand `Sales Data` and drag `ProductKey` into the **Rows** well.
   - This works because `ProductKey` lives inside the same table as `OrderQuantity`.
5. Remove `ProductKey` from rows and instead drag `ProductName` from `Product Lookup` into **Rows**.

**Expected result:** Every row shows the same grand total (`84,174`) instead of a per-product breakdown — confirming there is no relationship between `Sales Data` and `Product Lookup`.

> **Why this matters:** Recognizing the repeating-total signature is one of the most useful diagnostic skills in Power BI. Whenever a visual shows the same total on every row, the first thing to check is whether a valid relationship exists between the tables involved.

---

## Part 3: Normalization

### Definition

**Normalization** is the process of organizing tables and columns in a relational database to **reduce redundancy** and **preserve data integrity**.

### Why Normalization Matters

A normalized model:

1. **Eliminates redundant data**, reducing table size and improving processing speed.
2. **Minimizes errors and anomalies** when inserting, updating, or deleting records.
3. **Simplifies queries** and structures the database for meaningful analysis.

### A Practical Mental Model

> In a normalized database, each table serves **one distinct and specific purpose** — and one purpose only.

For example:

- A **Product** table contains only product information.
- A **Sales** table contains only daily transaction records.
- A **Calendar** table contains only date attributes.
- A **Customer** table contains only customer attributes.

These are exactly the kinds of tables in the AdventureWorks model.

---

### The Cost of an Un-Normalized Table

Consider a single transaction table that mixes everything together:

| Date | ProductID | Quantity | Brand | ProductName | SKU | Weight |
|---|---|---|---|---|---|---|
| 2023-01-01 | 4 | 2 | Acme | Helmet | HLM-001 | 0.5 |
| 2023-01-01 | 4 | 1 | Acme | Helmet | HLM-001 | 0.5 |
| 2023-01-02 | 4 | 3 | Acme | Helmet | HLM-001 | 0.5 |

Every time product 4 is sold, the brand, name, SKU, and weight are duplicated. With 100 products selling 10,000 times a day, you would generate **roughly 1 million duplicate rows per day** of redundant product attributes.

### The Normalized Solution

Split that single table into two:

**Transactions table** (one row per sale):

| Date | ProductID | Quantity |
|---|---|---|

**Products table** (one row per unique product):

| ProductID | Brand | ProductName | SKU | Weight |
|---|---|---|---|---|

Then connect them through a **relationship** on `ProductID`. The same analysis is possible — but with no duplicated product attributes.

---

## Part 4: Fact Tables vs. Dimension Tables

A well-designed model contains two kinds of tables.

### Fact (Data) Tables

Fact tables contain the **numerical values** you want to analyze, count, or aggregate.

Examples: sales, orders, transactions, page views, returns.

### Dimension (Lookup) Tables

Dimension tables contain **descriptive attributes** — usually text — that you use to filter, group, or slice the fact-table values.

Examples: products, customers, dates, stores, territories.

> **Terminology:** "Fact" and "data" are used interchangeably. "Dimension" and "lookup" are also used interchangeably. This handout uses both pairings.

---

### How Fact and Dimension Tables Connect

Fact tables and dimension tables share **key columns**. The key column appears in both tables and provides the link between them.

| Fact table | Shared key | Dimension table |
|---|---|---|
| `Sales Data` | `ProductKey` | `Product Lookup` |
| `Sales Data` | `CustomerKey` | `Customer Lookup` |
| `Sales Data` | `OrderDate` | `Calendar Lookup` |
| `Sales Data` | `TerritoryKey` | `Territory Lookup` |

These shared columns are what allow filter context from a dimension table to reach the fact table.

```text
          Dimension tables (one row per entity, no duplicates)
   +------------------+   +------------------+   +------------------+
   | Product Lookup   |   | Customer Lookup  |   | Calendar Lookup  |
   | ProductKey  (PK) |   | CustomerKey (PK) |   | Date        (PK) |
   | ProductName      |   | FirstName        |   | Year, Month, ... |
   | ProductColor     |   | EducationLevel   |   |                  |
   +--------+---------+   +--------+---------+   +--------+---------+
            |                      |                      |
            | ProductKey           | CustomerKey          | OrderDate
            v                      v                      v
   +-------------------------------------------------------------+
   |                       Sales Data (fact)                     |
   |  ProductKey (FK)  CustomerKey (FK)  OrderDate (FK)  Qty ... |
   +-------------------------------------------------------------+
```

Dimension keys are unique (one row per product, customer, date). Fact keys repeat — every order line for the same product reuses the same `ProductKey`.

---

## Part 5: Primary Keys and Foreign Keys

While both kinds of tables contain "key" columns, the keys behave differently.

### Primary Keys (PKs)

A **primary key** uniquely identifies each row in its table.

- Appears in **dimension tables**.
- Contains **no duplicates**.
- Examples in AdventureWorks:
  - `Customer Lookup` → `CustomerKey`
  - `Product Lookup` → `ProductKey`
  - `Calendar Lookup` → `Date`
  - `Territory Lookup` → `SalesTerritoryKey`
  - `Product Categories` → `ProductCategoryKey`
  - `Product Subcategories` → `ProductSubcategoryKey`

### Foreign Keys (FKs)

A **foreign key** is the lookup-side reference inside a fact table.

- Appears in **fact tables**.
- Can contain **many duplicates** — every order for the same product repeats its `ProductKey`.
- Examples in AdventureWorks `Sales Data`:
  - `ProductKey`, `CustomerKey`, `TerritoryKey`, `OrderDate`, `StockDate`

### An Excel Analogy

If you have used `VLOOKUP`, `XLOOKUP`, or `INDEX/MATCH`:

- **Foreign keys** = the lookup values
- **Primary keys** = the lookup arrays
- **Relationships** = the lookup formula itself

A data model achieves the same lookup behavior, but built into the model instead of repeated thousands of times in formulas.

---

## Exercise 3: Identify and Mark Primary Keys in the Model

**Goal:** Mark the primary key in each dimension table so the Power BI model view displays a key icon next to it.

### Steps

1. Switch to **Model** view.
2. Zoom out to **80%** using the zoom control in the lower-right corner so all tables are visible.
3. Drag `Sales Data` to the bottom of the canvas, beneath the dimension tables. Arrange the dimension tables in a row above it.

   > **Why arrange this way?** Placing dimension tables on top creates a visual reminder that filter context flows **downward** from dimensions to facts. This will be covered in detail in a later handout.

4. For each dimension table below, click the table to select it, then in the **Properties** pane on the right, find **Key column** and choose the listed field:

   | Table | Primary Key |
   |---|---|
   | `Territory Lookup` | `SalesTerritoryKey` |
   | `Customer Lookup` | `CustomerKey` |
   | `Calendar Lookup` | `Date` |
   | `Product Lookup` | `ProductKey` |
   | `Product Categories` | `ProductCategoryKey` |
   | `Product Subcategories` | `ProductSubcategoryKey` |

5. Confirm that a small **key icon** (resembling an ID card) appears next to each marked field.

> **Tip:** If you are unsure which column uniquely identifies each record, switch to **Data** view to inspect the rows.

**Expected result:** Six dimension tables, each with one primary key visibly marked. The `Sales Data` table has **no primary key marked** — it contains only foreign keys.

---

### Why `Sales Data` Has No Primary Key

A fact table can have a primary key — for example, a `TransactionID` or `SalesID` that uniquely identifies each row. The AdventureWorks `Sales Data` table does not contain such a column. Multiple rows can share the same combination of `OrderDate`, `ProductKey`, `CustomerKey`, and `TerritoryKey`, so none of them serve as a unique identifier.

---

## Part 6: Why Not Just Merge Everything Into One Table?

A common instinct — especially for Excel users — is to merge all tables into a single wide table using `VLOOKUP` or Power Query merges. Technically, this is possible. In Power BI you could even use the `RELATED` DAX function to pull fields between tables.

It is also strongly discouraged.

### Problems with the "Frankentable" Approach

- **Massive data redundancy** — product attributes get duplicated for every transaction.
- **Significantly higher memory and processing requirements**.
- **Worse performance** as data volume grows.
- **Loss of normalization** — the table no longer serves a single purpose.

A relational model with multiple small, focused tables is dramatically more efficient than one giant merged table. Build the habit early.

---

## Checkpoint ✅

Before moving to the next handout, verify:

- [ ] You can describe what makes a collection of tables a data model.
- [ ] You can explain normalization in your own words.
- [ ] You can distinguish a fact table from a dimension table.
- [ ] You can explain the difference between primary and foreign keys.
- [ ] All six dimension tables in the AdventureWorks model show a key icon on their primary key column.
- [ ] `Sales Data` is positioned below the dimension tables.

---

## Key Takeaways

- A **data model** is a set of tables connected by relationships — not just a collection of tables.
- **Normalization** keeps each table focused on one purpose and removes redundant data.
- **Fact (data) tables** hold numbers to be aggregated. **Dimension (lookup) tables** hold attributes used for filtering and grouping.
- **Primary keys** are unique values in dimension tables. **Foreign keys** are repeating references in fact tables.
- A repeating grand total across rows of a visual is almost always a relationship problem.
- Merging everything into one table is *technically* possible but inefficient. A normalized relational model is always preferable.

---

## Raw Data Reference

This handout uses the AdventureWorks files validated against the actual CSVs:

| File | Primary key column |
|---|---|
| `AdventureWorks Customer Lookup.csv` | `CustomerKey` |
| `AdventureWorks Product Lookup.csv` | `ProductKey` |
| `AdventureWorks Product Categories Lookup.csv` | `ProductCategoryKey` |
| `AdventureWorks Product Subcategories Lookup.csv` | `ProductSubcategoryKey` |
| `AdventureWorks Territory Lookup.csv` | `SalesTerritoryKey` |
| `AdventureWorks Calendar Lookup.csv` | `Date` |
| `AdventureWorks Sales Data 2020/2021/2022.csv` | (no primary key — foreign keys only) |
