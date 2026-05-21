# Handout 05: Bar Charts, Donut Charts, and Categorical Visuals

## Purpose

This handout covers visuals used to compare categories and to show composition (parts of a whole): bar / column charts and donut / pie charts. The Orders by Category chart on the Executive Dashboard and the demographic donut charts (Income Level, Occupation) on the Customer Detail page are built here.

---

## Prerequisites

- Earlier handouts are complete; the Executive Dashboard and Customer Detail page have logo, KPI cards, and at least one line chart.
- The model contains **Total Orders** and the categorical fields `CategoryName` (Product Categories Lookup), `Income Level`, and `Occupation` (Customer Lookup).

> **Raw data note:** The raw `AdventureWorks Customer Lookup.csv` contains `AnnualIncome` and `Occupation` but no `Income Level` column. `Income Level` is created in Power Query by bucketing `AnnualIncome` into bands such as Low / Average / High / Very High. Confirm that column exists in the model before building the donut chart.

---

## Key Concepts

### Bar vs. Column vs. Pie vs. Donut

| Visual | Best for |
|---|---|
| **Column chart** (vertical bars) | Comparison across a small number of categories or over time |
| **Bar chart** (horizontal bars) | Comparison when category labels are long or there are many categories |
| **Pie / Donut** | Quick read of composition with **3–5 segments**; not ideal for precise comparison |
| **Stacked bar / column** | Composition combined with comparison |

Humans judge bar **length** more accurately than pie **angles**. Default to a bar chart unless the message is specifically about parts of a whole.

> **Analogy — ruler vs. clock face:** Comparing two bars is like comparing two pencils side by side — your eye reads length effortlessly. Comparing two pie slices is like comparing two clock hands at slightly different angles — doable but much harder. When precision matters, give your audience the ruler, not the clock.

### Top N Filtering

When a category dimension has many values (products, occupations), apply a **Top N** filter so the visual shows only the most relevant slices. This keeps composition charts readable.

### Visual-Type Conversion

Any chart can be converted to a different type via the visual-type selector at the top of the **Build** pane. All field assignments are preserved; only the rendering changes.

---

## Exercise 1: Build the Orders by Category Bar Chart

**Goal:** Add a horizontal bar chart on the Executive Dashboard comparing Total Orders across product categories.

1. On the **Insert** ribbon, choose **Bar chart** (clustered bar).
2. Drag and resize it to sit below the Weekly Revenue line chart.
3. In the **Build** pane:
   - **Y-axis:** `CategoryName` (Product Categories Lookup)
   - **X-axis:** `Total Orders`
4. The chart shows one bar per category (Accessories, Bikes, Clothing, Components — Components may have no sales).
5. Click the **Total Orders** column header (or in the visual's More options menu, choose **Sort axis → Total Orders → Descending**) to sort bars from largest to smallest.

**Expected result:** A horizontal bar chart sorted descending by Total Orders.

---

## Exercise 2: Format the Bar Chart

**Goal:** Apply the dashboard style.

1. In the **Format** pane → **Visual** tab:
   - **Title:** `Orders by Category`, Segoe UI, size `10`, centered.
   - **Legend:** off (the bar labels make a legend redundant).
   - **X-axis:** off (the data labels will show values).
   - **Y-axis:** keep on so category names appear.
   - **Bars → Colors:** medium-dark gray.
2. Turn on **Data labels**:
   - Position: outside end (or auto).
   - Decimal places: `0`.
   - Font size: `8`.
3. Use **on-object formatting** as an alternative: right-click any bar on the canvas → **Add data labels** → right-click a label → **Format data labels**.

**Expected result:** A clean gray bar chart with category names on the Y-axis and order counts shown at the end of each bar.

---

## Exercise 3: Convert the Bar Chart to a Donut (Demonstration)

**Goal:** Demonstrate fast visual-type switching, then return to the bar form.

1. With the chart selected, click the visual-type selector and choose **Donut chart**.
2. Note that bars convert into proportional slices but exact comparison becomes harder.
3. Convert back to **Bar chart** (Bars are the better fit for this comparison goal.)

**Expected result:** Confidence in switching between visual types without rebuilding field assignments.

---

## Exercise 4: Build the Income Level Donut on Customer Detail

**Goal:** Show the composition of Total Orders across customer income levels.

1. Switch to the **Customer Detail** page.
2. Insert a **Donut chart**.
3. In the **Build** pane:
   - **Legend:** `Income Level` (Customer Lookup)
   - **Values:** `Total Orders`
4. Format the donut:
   - **Title:** `Orders by Income Level`, Segoe UI, size `10`, centered.
   - **Legend:** off.
   - **Detail labels:** on; **Label contents:** `Category, total number`; **Decimal places:** `1`; **Font size:** `8`.
   - **Slices → Colors:** assign distinct colors using the dashboard palette:
     - **Average:** `#20E2D7` (bright teal)
     - **Low:** medium-dark gray
     - **High:** gold / amber accent
5. In the **Filters** pane (visual-level), filter `Income Level` to exclude `Very High` (or any segment that introduces a fourth color and clutters the donut).
   - Filter type: **Basic filtering**.
   - Uncheck `Very High` (and any blank or null entry).

**Expected result:** A three-segment donut showing the proportion of orders coming from Low, Average, and High income customers.

---

## Exercise 5: Build the Occupation Donut by Copy + Modify

**Goal:** Reuse the Income Level donut formatting for the Occupation donut.

1. Select the Income Level donut. Press **Ctrl + C**, then **Ctrl + V**.
2. Position the duplicate below or beside the original.
3. In the **Build** pane of the duplicate:
   - Remove `Income Level` from **Legend**.
   - Add `Occupation` (Customer Lookup) to **Legend**.
4. Update the title to `Orders by Occupation`.
5. In the **Filters** pane of the new visual:
   - **Remove the Income Level filter** that carried over from the original (it should no longer apply here).
   - On the `Occupation` field, change the filter type to **Top N** → **Show items: Top** `3` → **By value:** `Total Orders`. This keeps the donut to three readable slices.
6. Reassign slice colors so each occupation has a clear, accessible color.

**Expected result:** A second donut showing the top three occupations by Total Orders, formatted consistently with the Income Level donut.

> **Common pitfall — copied filters:** When a chart is duplicated, every filter on the original travels with it. Always inspect the **Filters** pane of the duplicate and remove any filter that no longer makes sense.

---

## Exercise 6 (Optional): Try a Top N Filter on Income Level

**Goal:** Compare Basic filtering with Top N filtering on the same field.

1. Select the Income Level donut.
2. In its **Filters** pane, switch the filter type from **Basic filtering** to **Top N**.
3. Configure **Show items: Top** `3` **By value: Total Orders**.
4. Compare the result with the Basic-filtering version. Decide which approach better matches the analytical message and revert as needed.

**Expected result:** Familiarity with both filter types; understanding when each is preferable (Basic to exclude known unwanted values, Top N to keep the chart focused on whatever is most material under any filter context).

---

## Checkpoints

- Executive Dashboard contains a sorted, formatted bar chart of Total Orders by Category.
- Customer Detail contains a clean Income Level donut and a Top-3 Occupation donut.
- All charts use the dashboard's typography (Segoe UI, size `10` titles), color palette, and gray neutrals.
- No filters from copied charts carry over inappropriately.

---

## Key Takeaways

- Default to bar / column charts for comparison; reserve donut charts for clear composition stories with very few segments.
- Data labels on bar charts often eliminate the need for an X-axis or legend.
- Top N filters are essential to keep charts with many categories readable.
- Always verify visual-level filters after copying a chart — duplicates inherit the filter state of the original.
- Visual-type switching in Power BI is non-destructive: try multiple representations of the same data quickly.
