# Handout 03: KPI Cards and Numeric Visuals

## Purpose

This handout covers the two visuals used to display single-number metrics in Power BI: the **Card** and the **KPI** visual. Card visuals are used for the four headline numbers on the Executive Dashboard and Customer Detail pages. KPI visuals add a target and a trend sparkline to provide context. The handout also walks through a defensive `HASONEVALUE` pattern for "Top customer" text cards.

---

## Prerequisites

- Handout 02 is complete; the Executive Dashboard has the logo, four KPI background rectangles, and the left navigation strip.
- The DAX measures from prior sections are available: **Total Revenue**, **Total Profit**, **Total Orders**, **Return Rate**, **Total Customers**, **Avg Revenue per Customer**, **Previous Month Revenue**, **Order Target**, **Revenue Target**, **Profit Target**.
- A `_Measures` table exists for organizing measures.

---

## Key Concepts

### Card vs. KPI Visual

| Visual | What it shows | When to use |
|---|---|---|
| **Card** | A single number (one measure) with optional category label | Headline KPIs, hero numbers |
| **Multi-row card** | Several measures stacked in one visual | Compact summaries |
| **KPI** | A value, a trend sparkline (over time), and a target with a checkmark/red indicator | Performance vs. goal context |

### Renaming Fields in a Visual

Renaming a field inside a visual changes only the label shown in that visual; the underlying measure name stays the same. Double-click the field in the **Build** pane (or the Values area) and type a new label such as `REVENUE`.

### Quick Format vs. Format Pane

The **Build** pane (right side, when a visual is selected) has a small format icon that exposes a quick set of common settings. The full **Format** pane provides every formatting option. Use the Format pane for anything beyond defaults.

### Color Palette Used in This Section

| Use | Color |
|---|---|
| Primary accent (callout values, color-scale max) | Bright teal — `#20E2D7` |
| Card and shape background | Medium-dark gray |
| Light text on dark background | White |
| Caution / negative trend | Red (used sparingly) |

### Display Units

KPI numbers are formatted with **display units** (e.g., Millions) so a value like `24,924,317.45` renders as `24.9M`. Display units and decimal places are controlled in **Callout value** in the Format pane.

---

## Exercise 1: Build the First Card — Total Revenue

**Goal:** Place a single, formatted card visual on top of the first KPI background rectangle.

1. On the **Insert** ribbon, expand the visuals gallery and select **Card** (the single-value card, not the multi-row card).
2. A blank card appears on the canvas. Drag it on top of the first KPI background rectangle.
3. In the **Build** pane on the right, click **Add data** under **Fields**.
4. Type or select **Total Revenue** to add it as the card value.
5. Double-click `Total Revenue` in the Fields list under the visual and rename the label to `REVENUE`.
6. Open the **Format** pane (the paint roller icon).
7. In **Callout value**:
   - **Font family:** Segoe UI
   - **Font style:** Bold
   - **Font size:** `38`
   - **Font color:** `#20E2D7`
   - **Display units:** Millions
   - **Value decimal places:** `1`
8. In **Category label**:
   - **Font size:** `9`
   - **Font color:** White
9. In **Effects**, turn off **Background** so the card is transparent and the dark-gray rectangle shows through.
10. Resize the card so it fits cleanly inside the KPI background.

**Expected result:** The first KPI tile shows something like `24.9M` in bright teal, with `REVENUE` in small white text, on top of the dark-gray rectangle.

---

## Exercise 2: Duplicate the Card for Profit, Orders, and Returns

**Goal:** Create three more cards using the same formatting.

1. With the Revenue card selected, press **Ctrl + C**, then **Ctrl + V** three times.
2. Drag each duplicate onto its own KPI background rectangle.
3. For the second card:
   - Remove `Total Revenue` from the field well.
   - Add **Total Profit**.
   - Rename the field label to `PROFIT`.
4. For the third card, add **Total Orders** and rename it `ORDERS`.
   - In **Callout value**, change **Display units** to `Auto` (or `Thousands`) and **Value decimal places** to `0`.
5. For the fourth card, add **Return Rate** and rename it `RETURN %`.
   - Confirm the measure formats as a percentage (e.g., `2.2%`).
6. Multi-select all four cards, then click **Format → Align → Align top** and **Distribute horizontally**.

**Expected result:** Four matching headline KPI cards stretching across the top strip of the Executive Dashboard.

---

## Exercise 3: Build a KPI Visual — Monthly Revenue vs. Previous Month

**Goal:** Add context to the headline numbers with a KPI visual that shows current value, a sparkline trend, and a target indicator.

1. In an empty area of the Executive Dashboard, insert a **KPI** visual (Insert ribbon → KPI).
2. In the **Build** pane:
   - **Value:** `Total Revenue`
   - **Trend axis:** `Start of Month` (from the Calendar table)
   - **Target:** `Previous Month Revenue`
3. The visual now shows the current month's revenue, a sparkline of monthly values, and a checkmark or red icon based on whether the current value is above or below the target.
4. In the **Format** pane:
   - **Title:** turn on; text `Monthly Revenue`; font Segoe UI, size `10`, centered.
   - **Callout value:** Segoe UI Bold, size `30`, **Display units** `Millions`, **Value decimal places** `2`.
   - **Trend axis:** **Direction** `High is good`; reduce **Transparency** of the area to about `10%` for a subtle background; choose neutral colors (avoid red unless trending poorly).
   - **Target label:** font size `9`; rename the target text from the default `Goal` to `Previous Month`.
5. Resize the visual to roughly `175 × 100` pixels.

**Expected result:** A compact KPI tile that displays current monthly revenue (e.g., `1.83M`), a small sparkline behind the number, a green checkmark if above last month's revenue, and the label `vs. Previous Month`.

---

## Exercise 4: Duplicate the KPI for Orders and Returns

**Goal:** Build two more KPI tiles for Monthly Orders and Monthly Returns using the same pattern.

1. Copy the Monthly Revenue KPI visual and paste it twice.
2. For the **Monthly Orders** KPI:
   - **Value:** `Total Orders`
   - **Trend axis:** `Start of Month`
   - **Target:** `Previous Month Orders`
   - **Title:** `Monthly Orders`
   - **Display units:** `Auto` or `Thousands`, decimals `0`.
   - **Trend axis direction:** `High is good`.
3. For the **Monthly Returns** KPI:
   - **Value:** `Total Returns`
   - **Trend axis:** `Start of Month`
   - **Target:** `Previous Month Returns`
   - **Title:** `Monthly Returns`
   - **Trend axis direction:** `Low is good` — for returns, fewer is better.
4. Multi-select all three KPIs and apply **Align top** and **Distribute horizontally**.

**Expected result:** Three KPI tiles arranged in a horizontal row, each showing a value, sparkline, and target indicator.

> **Common pitfall — colorblind accessibility:** Avoid pairing red and green for at-a-glance status indicators. Reserve red for genuinely concerning metrics; otherwise stick with neutral grays and a single accent color.

---

## Exercise 5: Customer Detail Cards (Total Customers and Revenue per Customer)

**Goal:** Create two headline cards on the Customer Detail page.

1. Switch to the **Customer Detail** page.
2. Insert a rounded rectangle (height `100`, width `225`, fill medium-dark gray, no border, 15% rounded corners) — or copy one from the Executive Dashboard.
3. Insert a **Card** visual on top of the rectangle.
4. Set **Fields** to **Total Customers**.
5. Rename the field label to `UNIQUE CUSTOMERS`.
6. Apply the same callout formatting as in Exercise 1 (Segoe UI Bold, size `38`, color `#20E2D7`, decimals `0`, transparent background, white category label at size `9`).
7. Copy the card and rectangle as a pair, paste them, and reposition.
8. In the duplicate card, replace the measure with **Avg Revenue per Customer** and rename the label `REVENUE PER CUSTOMER`. Set decimals to `0`.
9. Align the two cards horizontally.

**Expected result:** Two headline cards on Customer Detail: a count of unique customers and an average revenue per customer.

---

## Exercise 6: Top Customer Text Cards (with HASONEVALUE Safeguards)

**Goal:** Show the name, order count, and revenue of the single top customer by revenue, while protecting against ties.

### Why this needs HASONEVALUE

A naive Top 1 filter on `Full Name` can produce a tie: if two customers share the same name (or the same revenue under the active filters), the card silently aggregates both, producing misleading totals. `HASONEVALUE` returns `TRUE` only when the filter context contains exactly one value of the chosen column.

> **Analogy — the bouncer at a VIP table:** `HASONEVALUE` is a bouncer who only lets the card show a name when **exactly one** person is at the table. Two people show up? The bouncer hands back a placeholder (`Multiple Customers`) instead of mashing both names together. This prevents the card from quietly displaying a misleading aggregate.

### Step 1 — Create three customer-detail measures

In the `_Measures` table, create:

```DAX
Full Name (Customer Detail) =
IF(
    HASONEVALUE('Customer Lookup'[CustomerKey]),
    MAX('Customer Lookup'[Full Name]),
    "Multiple Customers"
)
```

```DAX
Total Orders (Customer Detail) =
IF(
    HASONEVALUE('Customer Lookup'[CustomerKey]),
    [Total Orders],
    "--"
)
```

```DAX
Total Revenue (Customer Detail) =
IF(
    HASONEVALUE('Customer Lookup'[CustomerKey]),
    [Total Revenue],
    "--"
)
```

> **Raw data note:** The raw `AdventureWorks Customer Lookup.csv` contains `FirstName` and `LastName` separately. The `Full Name` column is created earlier in Power Query as a concatenation. If `Full Name` does not exist in the model, build it before creating these measures.

### Step 2 — Build the three text cards

1. Insert a **Card** visual on the Customer Detail page.
2. Add `Full Name (Customer Detail)` to **Fields**.
3. In the **Filters** pane for that visual, drag in `Customer Lookup[CustomerKey]`.
4. Change the filter type to **Top N** → **Top** `1` → **By value** = `Total Revenue`.

   > Filter on **CustomerKey** (a unique identifier), not `Full Name`. This guarantees exactly one customer survives the filter even when names repeat.
5. Format the card: callout value Segoe UI Bold, size `28`, color white. Turn off the category label and place the card on a dark-gray rounded rectangle.
6. Copy the card twice. In the duplicates, replace the field with `Total Orders (Customer Detail)` and `Total Revenue (Customer Detail)`.
7. Above the three cards, add small text-box labels: `Top Customer by Revenue`, `Orders`, `Revenue`. Use Segoe UI, size `10`, transparent background.

### Step 3 — Test under filtered scenarios

1. Add a slicer for **Income Level**. Select different income levels and confirm the top customer name, orders, and revenue update consistently.
2. Apply a filter that produces a tie (for example, `Year = 2020` plus a narrow occupation slice). The cards should display `Multiple Customers` / `--` rather than confusingly aggregated totals.

**Expected result:** Three cards always agree with each other — when a single customer is identifiable, the name, order count, and revenue describe that one customer; when there is a tie, all three cards display the safe fallback values.

---

## Checkpoints

- The Executive Dashboard top strip shows four formatted KPI cards (Revenue, Profit, Orders, Return %).
- Three KPI visuals (Monthly Revenue, Monthly Orders, Monthly Returns) display value, sparkline, and target.
- The Customer Detail page shows two headline cards (Unique Customers, Revenue per Customer) and three top-customer text cards.
- Top-customer cards never produce misleading totals when ties occur in the data.

---

## Key Takeaways

- Use **Card** for plain headline numbers and **KPI** when the audience needs target context.
- Renaming a field inside a visual affects the label only — the measure name in the model is unchanged.
- Format one card or KPI fully, then copy and swap measures rather than reformatting from scratch.
- Wrap any "Top 1" text card in `HASONEVALUE` to guard against ties.
- Always filter on a guaranteed-unique key (such as `CustomerKey`) when ranking with Top N.
