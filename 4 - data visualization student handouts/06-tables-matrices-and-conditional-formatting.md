# Handout 06: Tables, Matrices, and Conditional Formatting

## Purpose

This handout covers the **Table** and **Matrix** visuals and the **Cell elements** conditional formatting features (data bars, color scales, FX rules). The Top 10 Products matrix on the Executive Dashboard and the Top 100 Customers table on the Customer Detail page are built here.

---

## Prerequisites

- Earlier handouts complete; the dashboard has KPI cards, a line chart, a bar chart, and donut charts.
- The model contains **Total Orders**, **Total Revenue**, **Return Rate**, **Total Profit**, and the columns `ProductName`, `CategoryName`, `CustomerKey`, and `Full Name`.

---

## Key Concepts

### Table vs. Matrix

| Visual | Structure | Best for |
|---|---|---|
| **Table** | Flat list — fields become columns | Customer lists, product lists, transaction logs |
| **Matrix** | Pivot-style with **Rows**, **Columns**, and **Values** wells, supports drill | Hierarchical breakdowns (Category → Subcategory → Product) and cross-tabulations |

### Conditional Formatting via Cell Elements

The **Cell elements** section of the Format pane attaches conditional formatting to a single value column. The available formats are:

- **Background color** — solid color, color scale, or rules.
- **Font color** — same options as background.
- **Data bars** — proportional bars rendered inside the cell.
- **Icons** — checkmarks, arrows, traffic lights, etc.

The **fx** button (formatted as a small `fx` icon) opens the conditional-formatting dialog where the **Format style** can be set to **Gradient**, **Rules**, or **Field value**.

> **Analogy — a highlighter on a printout:** Conditional formatting is like running a highlighter across a printed table. **Data bars** are mini bar charts squeezed inside each cell ("how big?"). **Color scales** are heat maps ("how high relative to other rows?"). **Rules** act like a stoplight — green / yellow / red bands triggered when a value crosses a threshold.

### Top N on Tables

Tables and matrices can produce hundreds of rows by default. Apply a **Top N** filter on the row dimension (using a unique key column) to limit visible rows to the most important records.

---

## Exercise 1: Build the Top Products Matrix

**Goal:** Add a matrix on the Executive Dashboard showing the top products with order count, revenue, and return rate.

1. On the **Insert** ribbon, choose **Matrix**.
2. Drag and resize the matrix to sit below or beside the Orders by Category bar chart.
3. In the **Build** pane:
   - **Rows:** `ProductName`
   - **Values:** `Total Orders`, `Total Revenue`, `Return Rate` (in that order)
4. Rename each value's display label by double-clicking in the Build pane:
   - `Total Orders` → `Orders`
   - `Total Revenue` → `Revenue`
   - `Return Rate` → `Return %`
5. Sort the matrix by `Revenue` descending — click the **Revenue** column header on the visual until the down-arrow appears.

**Expected result:** A pivot-style matrix listing every product with three numeric columns.

---

## Exercise 2: Apply a Top 10 Filter

**Goal:** Limit the matrix to the ten products with the highest revenue.

1. With the matrix selected, open the **Filters** pane.
2. Drag `ProductKey` (Product Lookup) into the **Visual-level filters** area.
3. Change the filter type to **Top N**.
4. Configure:
   - **Show items: Top** `10`
   - **By value:** `Total Revenue`
5. Click **Apply filter**.

> **Why filter on `ProductKey` rather than `ProductName`?** `ProductKey` is guaranteed unique. If two products share a name, filtering on the name can collapse them into a single row or produce unstable Top N results.

**Expected result:** The matrix now shows exactly ten rows — the top ten products by revenue.

---

## Exercise 3: Format the Matrix

**Goal:** Apply the dashboard's clean, neutral style.

1. Open the **Format** pane → **Visual** tab.
2. **Style presets:** choose **None** as a clean starting point (banded rows can be added back later if desired).
3. **Title:** `Top 10 Products`, Segoe UI, size `10`, centered.
4. **Column headers:**
   - Font: Segoe UI Bold, size `10`.
   - Font color: dark gray.
   - Background: white or very light gray.
5. **Values:**
   - Font: Segoe UI, size `9` or `10`.
6. **Subtotals / Totals:** turn off **Column subtotals** and **Row subtotals** (a Top 10 list does not need a grand total).
7. **Grid:** keep horizontal grid lines subtle; turn off vertical grid lines for a cleaner look.

**Expected result:** A compact, readable Top 10 matrix that visually matches the rest of the dashboard.

---

## Exercise 4: Add Data Bars to the Orders Column

**Goal:** Render proportional bars inside each Orders cell.

1. With the matrix selected, in the **Format** pane → **Cell elements**.
2. Use the **Apply to series** dropdown to choose `Orders`.
3. Toggle **Data bars** on and click the **fx** icon.
4. In the conditional formatting dialog:
   - **Minimum:** Lowest value (auto).
   - **Maximum:** Highest value (auto).
   - **Positive bar color:** light gray.
   - **Negative bar color:** none required (no negative values expected).
   - **Show bar only:** off (display the value with the bar).
   - **Bar direction:** Left to right.
5. Click **OK**.

**Expected result:** Each Orders cell displays a numeric value with a horizontal gray bar whose length is proportional to that value.

---

## Exercise 5: Add a Color Scale to the Revenue Column

**Goal:** Tint Revenue cells from white (low) to teal (high) so the eye is drawn to top performers.

1. Format pane → **Cell elements** → **Apply to series:** `Revenue`.
2. Toggle **Background color** on and click the **fx** icon.
3. In the dialog:
   - **Format style:** Gradient.
   - **Minimum color:** white.
   - **Maximum color:** `#20E2D7`.
   - Leave **Diverging** off.
4. Click **OK**.

**Expected result:** Higher-revenue rows have a stronger teal background; lower-revenue rows fade to white.

---

## Exercise 6: Apply a Rules-Based Color Scale to Return %

**Goal:** Highlight high return rates with a warning color.

1. Format pane → **Cell elements** → **Apply to series:** `Return %`.
2. Toggle **Background color** on and click the **fx** icon.
3. Set **Format style** to **Rules**.
4. Enter rules (use percentages matching the data; tune as needed):
   - If value `>= 0` and `< 0.02` → light green (or white).
   - If value `>= 0.02` and `< 0.04` → soft yellow.
   - If value `>= 0.04` and `<= 1` → soft red.
5. Click **OK**.

**Expected result:** Return-rate cells are shaded by tier — neutral for healthy returns, yellow for caution, red for concerning levels.

---

## Exercise 7: Build the Top 100 Customers Table

**Goal:** Add a flat customer list on the Customer Detail page.

1. Switch to the **Customer Detail** page.
2. On the **Insert** ribbon, choose **Table**.
3. In the **Build** pane → **Columns**, add in order:
   - `CustomerKey`
   - `Full Name`
   - `Total Orders` (rename label to `Orders`)
   - `Total Revenue` (rename label to `Revenue`)
4. Sort the table descending by **Orders** — click the Orders column header until the descending indicator appears.
5. Apply a Top N filter:
   - In the **Filters** pane, drag `CustomerKey` into **Visual-level filters**.
   - Filter type **Top N**, **Show items: Top** `100`, **By value:** `Total Orders`.
6. Format the table:
   - **Title:** `Top 100 Customers`, Segoe UI, size `10`, centered.
   - **Style preset:** None.
   - **Column headers:** Segoe UI Bold, dark gray.
   - **Values:** Segoe UI, size `9` or `10`.
7. Apply conditional formatting:
   - **Orders → Data bars:** light gray.
   - **Revenue → Background color:** color scale, white to `#20E2D7`.

**Expected result:** A clean table listing the 100 highest-order customers, with data bars for Orders and a teal gradient for Revenue.

---

## Checkpoints

- The Executive Dashboard contains a Top 10 Products matrix with data bars, a color scale, and rules-based highlighting on Return %.
- The Customer Detail page contains a Top 100 Customers table sorted descending by Orders, with conditional formatting on both numeric columns.
- Both visuals use the dashboard's neutral typography and palette.

---

## Key Takeaways

- Use a **Table** for a flat list of records and a **Matrix** when rows have hierarchical structure or need cross-tabulation.
- Always filter Top N on a guaranteed-unique key column (`ProductKey`, `CustomerKey`) to avoid collisions.
- Conditional formatting transforms a wall of numbers into an at-a-glance signal: data bars for magnitude, color scales for ranking, rules for thresholds.
- Turn off totals on Top N visuals — a sum of the top 10 is rarely meaningful.
- Rename measure labels inside a visual (Orders, Revenue, Return %) to keep the visual narrow and easy to scan.
