# Handout 02: Building Relationships and Model Schemas

## Purpose

This handout walks through the Model view in Power BI Desktop, the two methods for creating relationships, how to manage and edit relationships, and the difference between **star** and **snowflake** schemas. It concludes with a hands-on assignment that rebuilds the AdventureWorks model from scratch.

---

## Prerequisites

- Completion of Handout 01.
- Primary keys are marked on all six dimension tables.
- `Sales Data` is positioned below the dimension tables in the model canvas.

---

## Part 1: Tour of the Model View

The Model view is where most relationship work happens. Its main components are:

| Component | Purpose |
|---|---|
| **Menu ribbon** | Home and Help tabs |
| **Model canvas** | The main area where tables and relationship lines appear |
| **View options** (lower right) | Zoom in/out and reset model layout |
| **Properties pane** | Edit table names, descriptions, primary keys, and relationship settings |
| **Data pane** (right) | Lists every table and field in the model |
| **Model layout tabs** | Allow custom diagrams of subsets of the model (covered in Handout 05) |

---

## Part 2: Two Ways to Create Relationships

### Method 1 — Drag and Drop (Recommended)

The fastest and most intuitive method. Click and hold any key column in one table and drag it onto the matching key column in another table. The direction of the drag does not matter — Power BI determines the relationship correctly based on which side has unique values.

### Method 2 — Manage Relationships Dialog

Open the **Manage Relationships** dialog from the **Home** ribbon. Useful when:

- You are unfamiliar with the data and want to preview rows from each table.
- You want to view all existing relationships in one list.
- You need to edit cardinality, cross-filter direction, or active state for an existing relationship.

> **Auto-detect option:** The dialog also offers an **Auto-detect** button that asks Power BI to scan the model and create relationships automatically. It can be a useful shortcut, but it sometimes misses relationships and may make incorrect assumptions. Manual configuration is more reliable.

---

## Exercise 1: Create a Relationship Using the Manage Relationships Dialog

**Goal:** Connect `Customer Lookup` to `Sales Data` using the dialog method.

1. In **Model** view, on the **Home** ribbon, click **Manage Relationships**.
2. Click **New**.
3. In the top dropdown, select `Customer Lookup`.
4. In the bottom dropdown, select `Sales Data`.
5. Power BI automatically highlights `CustomerKey` in both tables.
6. Confirm the cardinality, cross-filter direction, and "Make this relationship active" indicators are populated. (Detailed coverage in Handout 03.)
7. Click **OK**, then **Close**.

**Expected result:** A new relationship line connects `Customer Lookup` to `Sales Data`. Hover over the line — the `CustomerKey` field is highlighted in both tables.

---

## Exercise 2: Create Relationships Using Drag and Drop

**Goal:** Connect the remaining core tables using the faster drag-and-drop method.

### Connect Territory to Sales Data

1. In `Sales Data`, click and hold the `TerritoryKey` field.
2. Drag it onto `SalesTerritoryKey` in `Territory Lookup`.
3. Release the mouse.

**Expected result:** A relationship line appears between the two tables.

### Connect Calendar to Sales Data

1. In `Calendar Lookup`, click and hold the `Date` field.
2. Drag it onto `OrderDate` in `Sales Data`.
3. Release the mouse.

> **Why OrderDate and not StockDate?** The `Sales Data` table contains two date fields: `OrderDate` and `StockDate`. There can be only one **active** relationship between two tables, so a choice must be made. The active relationship is set on `OrderDate` because most analysis is concerned with when an order was placed. (Active vs. inactive relationships are covered in Handout 03.)

### Connect Product Lookup to Sales Data

1. In `Sales Data`, click and hold `ProductKey`.
2. Drag it onto `ProductKey` in `Product Lookup`.

---

## Exercise 3: Verify the Relationships Work

**Goal:** Confirm the matrix from Handout 01 now produces correct values.

1. Switch to **Report** view.
2. The matrix visual built earlier still shows `OrderQuantity` and `ProductName` from `Product Lookup`.
3. With the relationship between `Sales Data` and `Product Lookup` now established, each row should show a unique per-product quantity instead of the repeating grand total.

**Expected result:** Per-product quantities replace the repeating `84,174` total. Any field from any related dimension table is now a valid filter.

---

## Part 3: Managing and Editing Existing Relationships

### Opening the Edit Relationship Dialog

There are three ways to edit an existing relationship:

1. **Manage Relationships** dialog → select the relationship → click **Edit**.
2. **Double-click** the relationship line in the model canvas.
3. Click the relationship line, then use the **Properties** pane to change individual settings.

### What You Can Change

- The primary or foreign key column on either side.
- Cardinality.
- Cross-filter direction.
- Whether the relationship is **active**.

### Deleting a Relationship

- Right-click the relationship line in the model canvas and select **Delete**, or
- Open **Manage Relationships**, select the relationship, and click **Delete**.

> **Tip:** Holding **Shift** in the Manage Relationships dialog allows you to select multiple relationships at once and delete them in one step — useful when starting over from a clean slate.

---

## Exercise 4: Edit a Relationship to Use a Different Foreign Key

**Goal:** Temporarily change the active calendar relationship from `OrderDate` to `StockDate`, then change it back.

1. Open **Manage Relationships**.
2. Select the relationship between `Sales Data` and `Calendar Lookup` and click **Edit**.
3. In the dialog, click the `StockDate` column in the `Sales Data` preview to use it as the foreign key.
4. Click **OK** and **Close**.
5. Hover over the relationship line in the canvas — the highlighted field should now be `StockDate`.
6. Double-click the line to reopen the Edit dialog and switch back to `OrderDate`.

**Expected result:** The active calendar relationship is restored to `OrderDate`.

---

## Exercise 5: Delete and Recreate a Relationship

**Goal:** Practice removing a relationship and rebuilding it.

1. Right-click the relationship line between `Customer Lookup` and `Sales Data`.
2. Select **Delete** and confirm.
3. Drag `CustomerKey` from `Sales Data` onto `CustomerKey` in `Customer Lookup` to recreate the relationship.

**Expected result:** The relationship is restored within seconds. This is the typical workflow when troubleshooting a model — deleting and recreating is fast and harmless.

---

## Part 4: Star and Snowflake Schemas

### Star Schema

A **star schema** is the simplest, most common data model layout. It is characterized by:

- One central **fact table**.
- Multiple **dimension tables**, each connected directly to the fact table.

Visually, the dimensions radiate out from the fact table like points of a star.

```text
                +------------------+
                | Calendar Lookup  |
                +--------+---------+
                         |
  +------------------+   |   +------------------+
  | Customer Lookup  |---+---| Territory Lookup |
  +------------------+   |   +------------------+
                         v
                  +-------------+
                  | Sales Data  |   <-- fact table at the centre
                  +------+------+
                         ^
                         |
                +------------------+
                | Product Lookup   |
                +------------------+
```

> **Note on layout:** A model is still a star schema regardless of where you place the tables on the canvas. The AdventureWorks model is a star schema even though the dimension tables sit *above* the fact table instead of *around* it.

### Snowflake Schema

A **snowflake schema** extends the star by adding **chained relationships** between dimension tables. A dimension can connect to a sub-dimension, which connects to another sub-dimension.

```text
   +---------------------+
   | Product Categories  |
   +----------+----------+
              |
              v
   +---------------------+
   | Product Subcategories|
   +----------+----------+
              |
              v
   +---------------------+
   | Product Lookup      |
   +----------+----------+
              |
              v
   +---------------------+
   | Sales Data (fact)   |
   +---------------------+
```

The AdventureWorks product hierarchy is a snowflake:

| Table | Foreign key | Connects to | Primary key |
|---|---|---|---|
| `Product Lookup` | `ProductSubcategoryKey` | `Product Subcategories` | `ProductSubcategoryKey` |
| `Product Subcategories` | `ProductCategoryKey` | `Product Categories` | `ProductCategoryKey` |

`Sales Data` connects to `Product Lookup`, which connects to `Product Subcategories`, which connects to `Product Categories`. The chain forms the snowflake.

> **When does a snowflake make sense?** When dimension tables themselves have their own descriptive lookup tables — typically because the source data was already normalized that way.

---

## Exercise 6 (Assignment): Rebuild the Model from a Clean Slate

**Scenario:**

> *"Hey there. Ethan shared the data model you've been working on, and we might have an issue. Last night I left my laptop open, and my cat Dennis somehow got his paws on our model. Now all the relationships are gone. Could you please rebuild the model, including all three product tables? I owe you one. — Dana"*

### Objectives

1. Delete all existing table relationships.
2. Create a **star schema** between `Sales Data`, `Calendar Lookup`, `Customer Lookup`, `Product Lookup`, and `Territory Lookup`.
3. Create a **snowflake schema** that connects all three product tables (`Product Lookup` → `Product Subcategories` → `Product Categories`).
4. Use a matrix visual to confirm `OrderQuantity` can be filtered by fields from every dimension table.

---

### Step 1 — Delete All Existing Relationships

1. Open **Manage Relationships**.
2. Click the first relationship in the list, then hold **Shift** and click the last one to select all.
3. Click **Delete** and confirm.

**Expected result:** No relationship lines remain in the model canvas.

---

### Step 2 — Build the Star Schema

Create the following four relationships using drag and drop:

| Fact column | Dimension column | Dimension table |
|---|---|---|
| `Sales Data[TerritoryKey]` | `SalesTerritoryKey` | `Territory Lookup` |
| `Sales Data[CustomerKey]` | `CustomerKey` | `Customer Lookup` |
| `Sales Data[OrderDate]` | `Date` | `Calendar Lookup` |
| `Sales Data[ProductKey]` | `ProductKey` | `Product Lookup` |

---

### Step 3 — Build the Snowflake Extension

The product hierarchy follows category → subcategory → product. Arrange the three product tables vertically to reflect this hierarchy, then create:

| Foreign key | Primary key | Direction |
|---|---|---|
| `Product Lookup[ProductSubcategoryKey]` | `Product Subcategories[ProductSubcategoryKey]` | one relationship |
| `Product Subcategories[ProductCategoryKey]` | `Product Categories[ProductCategoryKey]` | one relationship |

**Expected result:** A chain runs from `Sales Data` → `Product Lookup` → `Product Subcategories` → `Product Categories`.

---

### Step 4 — Validate Using the Matrix Visual

In **Report** view, edit the matrix visual to test fields from every dimension table. Place each field below in the **Rows** well one at a time:

| Field | Source table | Sample expected values |
|---|---|---|
| `Year` | `Calendar Lookup` | 2020, 2021, 2022 |
| `Occupation` | `Customer Lookup` | Clerical, Management, Manual, Professional, Skilled Manual |
| `CategoryName` | `Product Categories` | Accessories, Bikes, Clothing, Components |
| `ProductName` | `Product Lookup` | One row per product |
| `SubcategoryName` | `Product Subcategories` | Bike Racks, Bike Stands, Bottles and Cages, etc. |
| `Country` | `Territory Lookup` | Australia, Canada, France, Germany, United Kingdom, United States |

> **Raw data note:** `AdventureWorks Product Categories Lookup.csv` contains four categories: Bikes, Components, Clothing, Accessories. `AdventureWorks Territory Lookup.csv` includes the six countries listed above.

**Expected result:** Every field produces a unique per-row breakdown of `OrderQuantity` instead of a repeating grand total.

---

## Checkpoint ✅

- [ ] Four star-schema relationships connect `Sales Data` to its four dimensions.
- [ ] Two additional snowflake relationships chain through `Product Subcategories` and `Product Categories`.
- [ ] No `Customer` relationship exists for `Returns Data` (this is expected — there is no `CustomerKey` in returns; it will be loaded in Handout 03).
- [ ] The matrix visual produces accurate per-row values for fields from every dimension table.

---

## Key Takeaways

- The **drag-and-drop** method is the fastest way to create relationships; the **Manage Relationships dialog** is helpful for editing or working with unfamiliar data.
- Power BI **auto-detect** can be useful but sometimes misses relationships — manual configuration is more reliable.
- A **star schema** has one fact table connected directly to dimension tables.
- A **snowflake schema** adds chained relationships between dimensions (dimension → sub-dimension).
- Editing a relationship is as simple as double-clicking its line on the canvas.
- The position of tables on the canvas does not change the schema type — only the relationships do.

---

## Raw Data Reference

| File | Used for |
|---|---|
| `AdventureWorks Sales Data 2020/2021/2022.csv` | Fact table containing `ProductKey`, `CustomerKey`, `TerritoryKey`, `OrderDate`, `StockDate` |
| `AdventureWorks Product Lookup.csv` | Contains both `ProductKey` (PK) and `ProductSubcategoryKey` (FK) |
| `AdventureWorks Product Subcategories Lookup.csv` | Contains both `ProductSubcategoryKey` (PK) and `ProductCategoryKey` (FK) |
| `AdventureWorks Product Categories Lookup.csv` | Contains `ProductCategoryKey` (PK) and `CategoryName` |
| `AdventureWorks Territory Lookup.csv` | Contains `SalesTerritoryKey`, `Region`, `Country`, `Continent` |
| `AdventureWorks Calendar Lookup.csv` | Contains `Date` (PK) |
| `AdventureWorks Customer Lookup.csv` | Contains `CustomerKey` (PK) and demographic attributes |
