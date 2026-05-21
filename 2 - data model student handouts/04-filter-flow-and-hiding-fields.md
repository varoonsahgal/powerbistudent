# Handout 04: Filter Flow, Bi-Directional Filtering, and Hiding Fields

## Purpose

This handout covers what is arguably the single most important concept in Power BI data modeling: **filter context and filter flow**. It explains how filter context travels through relationships, when bi-directional filtering helps and when it causes problems, and how to use the **Hide in report view** option to prevent invalid filter scenarios.

---

## Prerequisites

- Completion of Handout 03.
- The AdventureWorks model includes both `Sales Data` and `Returns Data` connected to their shared dimensions.

---

## Part 1: Filter Context and Filter Flow

### What Filter Context Is

When you put a field on a visual or a slicer, you create **filter context** — a set of filters that defines which rows of data should be considered. The matrix visual you have been building uses filter context every time a field is placed on **Rows**.

### How Filter Context Travels

In Power BI, every relationship line shows an **arrow** that points from the **dimension side** (the "1" side) to the **fact side** (the "many" side). Filter context follows the arrow.

> **Mental model:** Think of relationships as **filter wires**. They exist primarily to pass filter context from one table to another. By default, filter context flows **downstream** in the direction of the arrow and cannot flow upstream.

### Why Lookup Tables Are Placed Above the Fact Tables

Placing dimension tables visually above fact tables in the canvas is more than aesthetic. It creates an immediate visual reminder that **filter context flows downward** from dimensions to facts.

```text
   Dimension (the "1" side)               Dimension (the "1" side)
   +------------------+                   +------------------+
   | Territory Lookup |                   | Product Lookup   |
   +--------+---------+                   +--------+---------+
            |  filter context                      |
            |  flows DOWN the                      |
            v  arrow only                          v
   +-------------------------+         +-------------------------+
   |    Sales Data (fact)    |         |    Returns Data (fact)  |
   +-------------------------+         +-------------------------+
        the "many" side                       the "many" side
```

Filters placed on a dimension reach every fact table downstream. Filters placed *inside* a fact table are trapped — they cannot travel back upstream to the dimension and therefore cannot reach a sibling fact table.

---

## Part 2: A Worked Example

Consider a simplified three-table model:

- `Territory Lookup` (dimension)
- `Sales Data` (fact)
- `Returns Data` (fact)

Both fact tables are connected one-to-many to `Territory Lookup`.

### Filtering Correctly — Using the Primary Key

Place `TerritoryKey` from `Territory Lookup` on rows. Result:

- All 10 territories appear (the lookup contains them).
- `OrderQuantity` and `ReturnQuantity` both display correct values.

This works because `Territory Lookup` is **upstream** of both fact tables. Its filter context flows down both relationship lines.

### Filtering Incorrectly — Using a Foreign Key from a Fact Table

Place `TerritoryKey` from `Sales Data` on rows. Result:

- All 10 territories appear (sales had every territory).
- `OrderQuantity` is correct.
- `ReturnQuantity` shows the same repeating total on every row.

The reason: filter context generated inside `Sales Data` is **trapped in that table**. It cannot flow upstream to `Territory Lookup`, so it never reaches `Returns Data`.

### Filtering Incorrectly the Other Way

Place `TerritoryKey` from `Returns Data` on rows. Result:

- Only **8 territories** appear — territories 2 and 3 had zero returns and therefore do not exist in `Returns Data`.
- `ReturnQuantity` is correct.
- `OrderQuantity` shows the same repeating total on every row.

> **Two distinct problems are hidden in this scenario:** the repeating total on `OrderQuantity`, and the silent loss of two territories from the visual entirely. The second problem is harder to spot but equally serious.

---

## Exercise 1: Trace Filter Flow Yourself

**Goal:** See the trapped-filter-context problem firsthand.

### Steps

1. Open the AdventureWorks report and switch to **Report** view.
2. Edit the existing matrix visual:
   - Values: `OrderQuantity` (from `Sales Data`) and `ReturnQuantity` (from `Returns Data`).
   - Rows: `TerritoryKey` from **`Territory Lookup`**.
3. Confirm all 10 territories appear with correct values for both columns.
4. Remove `TerritoryKey` from rows.
5. Add `TerritoryKey` from **`Sales Data`** to rows.
6. Observe: `OrderQuantity` is correct; `ReturnQuantity` shows repeating totals.
7. Remove `TerritoryKey` from rows.
8. Add `TerritoryKey` from **`Returns Data`** to rows.
9. Observe: `ReturnQuantity` is correct; `OrderQuantity` shows repeating totals; territories 2 and 3 are missing.
10. Restore the visual to use `TerritoryKey` from `Territory Lookup` on rows.

> **Diagnostic rule:** Whenever a visual shows repeating grand totals, the first thing to check is **which table the row field came from**. The cure is almost always to use the field from the dimension/lookup table instead of from the fact table.

---

## Part 3: Bi-Directional Filtering

### What It Does

By default, every relationship has a **single** cross-filter direction (one-way). You can change this to **Both** to let filter context flow in either direction across that relationship — turning the one-way street into a two-way street.

### How to Change It

Either method works:

1. Double-click the relationship line, change **Cross filter direction** to **Both**, and click OK.
2. Click the relationship line, then change the **Cross filter direction** dropdown in the **Properties** pane to **Both** and click **Apply changes**.

The relationship line on the canvas updates to show a two-way arrow.

---

### When Bi-Directional Filtering Helps

If you set a bi-directional relationship between `Sales Data` and `Territory Lookup`, then placing `TerritoryKey` from `Sales Data` on rows now:

1. Sends filter context up to `Territory Lookup`.
2. From there, flows down to `Returns Data`.
3. Both quantities display correct values.

### When It Causes Hidden Problems

Putting `TerritoryKey` from `Returns Data` on rows in this same setup looks like it works — both columns show clean numbers. But territories 2 and 3 are still silently missing because they do not exist in the `Returns Data` table at all.

The visual looks correct but is **incomplete**. This is the danger of bi-directional filtering: it can hide errors instead of revealing them.

---

### The Ambiguity Problem

Adding multiple bi-directional filters can create **multiple conflicting filter paths** between two tables. Power BI calls this **ambiguity** and refuses to allow it.

Example with two two-way relationships:

- `Product Lookup` connects to `Returns Data`.
- `Returns Data` connects to `Territory Lookup` (bi-directional).
- `Sales Data` connects to `Territory Lookup` (bi-directional).
- An attempt to activate a second relationship between `Product Lookup` and `Sales Data` produces:

  > *"You can't create a direct active relationship between Product Lookup and Sales Data because that would introduce ambiguity between tables Product Lookup and Territory Lookup."*

A concrete way to see why this is a problem: imagine filtering on a single product (a water bottle). If that bottle was returned only in territories 1 and 2 but sold in territories 1, 2, 3, and 4, then `Territory Lookup` would receive **two conflicting filter contexts simultaneously** — one set saying "filter to territories 1 and 2", another saying "filter to territories 1, 2, 3, 4". Conflicting filters cannot be resolved.

---

## Pro Tip: Default to Simple Models

> **Always design models with one-way filters and one-to-many cardinality unless a more complex relationship is genuinely required.** The vast majority of models work perfectly with simple defaults. Bi-directional filtering and many-to-many cardinality are advanced tools that introduce risk and should only be used with full understanding of the consequences.

---

## Exercise 2: Demonstrate Bi-Directional Filtering and Ambiguity

**Goal:** Observe both the helpful and problematic effects of bi-directional filtering.

### Steps

1. In **Model** view, double-click the relationship between `Returns Data` and `Territory Lookup`.
2. Set **Cross filter direction** to **Both** and click **OK**. The arrow on the line now points both ways.
3. Switch to **Report** view. With `TerritoryKey` from `Returns Data` still on rows of the matrix, observe that the values now look clean (no repeating totals).
4. Recall that this view is still **missing territories 2 and 3** — the bi-directional filter masked the missing-relationship signature without solving the underlying problem.
5. Return to **Model** view.
6. Click the relationship between `Sales Data` and `Territory Lookup`.
7. In the **Properties** pane, set **Cross filter direction** to **Both** and click **Apply changes**.

   **Expected result:** An ambiguity error:
   > *"There are ambiguous paths between Territory Lookup and Product Lookup."*

8. Close the error.
9. Click the bi-directional relationship between `Returns Data` and `Territory Lookup`. In the Properties pane, set **Cross filter direction** back to **Single** and click **Apply changes**.

**Expected result:** All relationships are back to one-way. The model is restored.

---

## Part 4: Hiding Fields from Report View

### Why Hide Fields

The "wrong table" problem in the matrix happens because the same column name (`ProductKey`, `TerritoryKey`, etc.) exists in both fact and dimension tables. Hiding foreign-key fields in the **fact tables** prevents report authors from accidentally selecting them.

> **Hiding ≠ deleting.** Hidden fields remain available in the **Data** view and **Model** view. They are simply removed from the **Report** view's data pane.

### Methods to Hide

1. Right-click a field (or a whole table) in **Model** view or **Data** view → **Hide in report view**.
2. Hover over the field in **Model** view and click the **eye icon** to toggle visibility.
3. In the **Properties** pane, toggle the **Is hidden** option.

### Visual Indicator

Hidden fields display a slashed-eye icon next to their name in the Model view. They no longer appear in the Data pane on the Report tab.

---

## Exercise 3: Hide Foreign Keys in Both Fact Tables

**Goal:** Hide every foreign key in `Sales Data` and `Returns Data`, plus any chained foreign keys in the snowflake tables.

### Fact tables — hide every foreign key

In **Sales Data**, hide:

- `CustomerKey`
- `OrderDate`
- `ProductKey`
- `StockDate`
- `TerritoryKey`

In **Returns Data**, hide:

- `ProductKey`
- `ReturnDate`
- `TerritoryKey`

### Snowflake tables — hide chained foreign keys

In **Product Subcategories**, hide:

- `ProductCategoryKey`

In **Product Lookup**, hide:

- `ProductSubcategoryKey`

> **Why hide these too?** They are foreign keys in the snowflake chain — purely structural, not analytical. Removing them from the report view tidies the data pane.

### Steps

1. In **Model** view, hover over each field listed above and click the eye icon.
2. After hiding all fields, switch to **Report** view.
3. Check the data pane on the right.

**Expected result:** `Sales Data` displays only `OrderLineItem`, `OrderNumber`, and `OrderQuantity` (along with `Quantity Type` if you added it in the Power Query handout on Advanced Query Editing). `Returns Data` displays only `ReturnQuantity`. `ProductKey` is now only available from `Product Lookup`.

---

## Exercise 4 (Assignment): Diagnose Larry's Matrix

**Scenario:**

> *"Hey there. We've got another problem. Larry from sales just sent me this screenshot. I think he must have downloaded our BI model and messed with some relationships, because I know we had sales for product 338. Can you help diagnose what's going on and prevent him from doing this again? — Your boss."*

Larry's matrix shows `ProductKey` on rows and clean numbers in both `OrderQuantity` and `ReturnQuantity` columns — but **product 338 is missing entirely**, even though sales records exist for it.

### Objectives

1. Replicate Larry's matrix.
2. Identify which product `ProductKey 338` actually is.
3. Explain why product 338 is missing from Larry's view.
4. Hide all remaining foreign keys in `Returns Data` and revert any bi-directional filters to prevent the issue from recurring.

---

### Step 1 — Replicate the Matrix

1. In **Report** view, edit the matrix visual.
2. Remove the current rows field.
3. Add `ProductKey` from `Product Lookup` (the primary key version) to rows.

**Observation:** Numbers are accurate, and product 338 *does* appear in the list (it was sold 50 times and never returned). This is not yet Larry's view.

4. Remove `ProductKey` from rows.
5. Add `ProductKey` from `Returns Data` to rows.

**Observation:** Product 338 is now missing (it has no return records). But `OrderQuantity` is showing repeating totals — which Larry's screenshot did not show. So a relationship change must also have been made.

---

### Step 2 — Recreate the Bi-Directional Filter

1. In **Model** view, double-click the relationship between `Product Lookup` and `Returns Data`.
2. Set **Cross filter direction** to **Both** and click **OK**.
3. Switch to **Report** view.

**Expected result:** The matrix now matches Larry's screenshot — clean per-product numbers, but product 338 is silently missing.

### Why Product 338 Is Missing

The filter context starts in `Returns Data` (because the row field came from there). Bi-directional filtering passes that context up to `Product Lookup` and then down to `Sales Data`. Any product that has **no rows in `Returns Data`** (such as product 338) is filtered out by `Returns Data` before the filter even reaches `Sales Data`.

### Step 3 — Identify Product 338

1. Switch to **Data** view.
2. Open the `Product Lookup` table.
3. Scroll to `ProductKey = 338`.

**Expected result:** Product 338 is `Road-650 Black, 44`. Sample raw-data confirmation: this is one of the products in `AdventureWorks Product Lookup.csv`.

### Step 4 — Prevent the Issue From Recurring

1. In **Model** view, click the relationship between `Product Lookup` and `Returns Data`.
2. In the Properties pane, set **Cross filter direction** back to **Single** and click **Apply changes**.
3. Hide the foreign keys in `Returns Data`:
   - `ProductKey`
   - `ReturnDate`
   - `TerritoryKey`

   Click the eye icon next to each field.
4. Switch to **Report** view. The matrix returns to repeating totals (because the row field still references the now-hidden foreign key).
5. Remove the row field, then try to re-add `ProductKey` — it should only be selectable from `Product Lookup` because the `Returns Data` version is now hidden.
6. Add `ProductKey` from `Product Lookup`.

**Expected result:** The matrix shows accurate per-product values for both `OrderQuantity` and `ReturnQuantity`. Product 338 reappears (with 50 orders and 0 returns). The model is now safe from Larry's mistake.

---

## Checkpoint ✅

- [ ] You can describe how filter context flows along relationship arrows.
- [ ] You can explain why placing a foreign key from a fact table on rows produces wrong results.
- [ ] All foreign keys in `Sales Data` and `Returns Data` are hidden.
- [ ] All bi-directional filters have been removed.
- [ ] All chained foreign keys in `Product Lookup` and `Product Subcategories` are hidden.

---

## Key Takeaways

- **Filter context follows the arrow on a relationship line.** It cannot flow upstream by default.
- **Repeating grand totals** are a sign that filter context is trapped in a fact table.
- **Bi-directional filtering** allows context to flow both ways but introduces risks: silently missing rows and ambiguity errors.
- **Ambiguity** occurs when bi-directional filters create multiple conflicting paths between tables.
- The default best practice is **one-way filtering with one-to-many cardinality**.
- **Hide foreign keys in fact tables** so report authors can only filter through the proper dimension primary keys.

---

## Raw Data Reference

| File | Used for |
|---|---|
| `AdventureWorks Product Lookup.csv` | Confirmed `ProductKey 338` corresponds to a real product (`Road-650 Black, 44` in the source data) |
| `AdventureWorks Returns Data.csv` | Confirms that not every product has return rows — basis of the silent-missing-rows demonstration |
