# Handout 08: Gauges, Maps, Bookmarks, and Buttons

## Purpose

This handout covers the remaining visuals and interactivity features needed to finish the AdventureWorks dashboard: **gauge charts** comparing actual to target on the Product Detail page, the **map** visual on the Map page, **bookmarks** that capture report state, and **buttons** that trigger bookmark navigation and reset filters.

---

## Prerequisites

- Earlier handouts complete; the Map, Product Detail, and Customer Detail pages exist with at least the page-navigation strip in place.
- Target measures from the DAX section are available: `Order Target`, `Revenue Target`, `Profit Target` (each defined as the previous month's value plus 10%).
- Bing Maps services are available (typically the default in Power BI Desktop and Service).

---

## Key Concepts

### Gauge Charts

A gauge visual is a half-circle "speedometer." It displays:

- A **value** (the metric being measured).
- A **target** (the comparison point).
- A **maximum** (where the arc fills to 100%).

Gauges work best when actual and target are similar in scale; a gauge is poor for highly variable metrics.

> **Analogy — a fuel gauge:** A gauge answers one question at a glance: *"Are we full, half, or empty relative to where we should be?"* It is excellent for progress-toward-target stories and a poor choice for showing trends or comparing many categories.

### Maps in Power BI

The standard **Map** visual uses Bing Maps to render bubble maps from a location field. Bubble size encodes a metric. Other map visuals include **Filled map**, **Shape map**, **Azure Map**, and **ArcGIS**.

> **Tenant requirement:** The Power BI Service admin must have **Bing Maps** enabled for the workspace. If maps render blank in the Service, this is the most common cause.

### Bookmarks

A **bookmark** captures the entire state of a report page: filter selections, slicer values, drill levels, and visual visibility. Bookmarks can be triggered by buttons or accessed from the Bookmarks pane.

> **Analogy — a save state in a video game:** A bookmark is a snapshot you can return to with one click. Filters applied, slicers set, drill levels chosen — all preserved exactly as captured. Pair bookmarks with buttons and the dashboard becomes a guided tour: click the button, jump to the saved view.

### Buttons and Action Types

Buttons can navigate, reset, or trigger bookmarks. Each button has independent format settings for **Default**, **On hover**, **On press**, and **Disabled** states, allowing rich visual feedback.

> **Desktop testing:** In Power BI Desktop, hold **Ctrl** while clicking a button to trigger its action. In the Power BI Service, a normal click is enough.

---

## Exercise 1: Build the First Gauge — Monthly Orders vs. Target

**Goal:** Add a gauge visual comparing Total Orders (current month) to Order Target on the Product Detail page.

1. Switch to the **Product Detail** page.
2. On the **Insert** ribbon, choose **Gauge** (the speedometer icon).
3. In the **Build** pane:
   - **Value:** `Total Orders`
   - **Trend axis:** *(leave blank — gauges in Power BI do not display a sparkline like KPI visuals)*

   > **Power BI gauge note:** Unlike the KPI visual, the gauge visual does not support a trend axis sparkline. If a sparkline is needed, swap the gauge for a KPI visual.
   - **Target value:** `Order Target`
   - **Maximum value:** `Order Target` (so the gauge reaches 100% when the target is met)
4. The gauge displays the actual orders for the active filter context, an indicator line at the target, and an arc fill that reaches the end at 100% of target.

**Expected result:** A gauge whose fill represents progress toward the order target.

---

## Exercise 2: Format the Gauge

**Goal:** Apply the dashboard's typography and color palette.

1. With the gauge selected, open the **Format** pane → **Visual** tab.
2. **Title:** `Orders vs. Target`, Segoe UI, size `10`, centered.
3. **Gauge axis → Fill color:** medium-dark gray.
4. **Callout value (the big number):** Segoe UI Bold, size `20`, dark gray, decimal places `0`.
5. **Target label:** Segoe UI, size `10`.
6. Resize to roughly `200 × 150` pixels.

**Expected result:** A neutrally colored gauge visually consistent with the rest of the dashboard.

---

## Exercise 3: Build Two More Gauges (Revenue and Profit)

**Goal:** Create matching gauges for Revenue and Profit performance.

1. Copy the Orders gauge twice and reposition the duplicates beside the original.
2. For the second gauge:
   - **Value:** `Total Revenue`
   - **Target value:** `Revenue Target`
   - **Maximum value:** `Revenue Target`
   - **Title:** `Revenue vs. Target`
   - **Display units:** `Millions`, decimals `2`.
3. For the third gauge:
   - **Value:** `Total Profit`
   - **Target value:** `Profit Target`
   - **Maximum value:** `Profit Target`
   - **Title:** `Profit vs. Target`
   - **Display units:** `Millions`, decimals `2`.
4. Multi-select all three gauges and apply **Format → Align top** and **Distribute horizontally**.

**Expected result:** A row of three gauges showing performance against orders, revenue, and profit targets.

---

## Exercise 4 (Optional): Conditionally Color the Gauge Callout Value

**Goal:** Turn the gauge callout red when a target is missed.

### Step 1 — Create gap measures

In the `_Measures` table:

```DAX
Order Target Gap = [Total Orders] - [Order Target]
Revenue Target Gap = [Total Revenue] - [Revenue Target]
Profit Target Gap = [Total Profit] - [Profit Target]
```

### Step 2 — Apply conditional formatting

1. Select the Orders gauge.
2. Format pane → **Callout value → Color → fx**.
3. **Format style:** Rules.
4. **What field should we base this on:** `Order Target Gap`.
5. Add rules:
   - If value `< 0` → red.
   - If value `>= 0` → dark gray.
6. Repeat for the Revenue and Profit gauges using the matching gap measures.

**Expected result:** Gauge callout values turn red whenever the actual value falls short of the target, providing an at-a-glance status without examining the arc.

---

## Exercise 5: Build the Map Visual

**Goal:** Add a bubble map of Total Orders by Country on the Map page.

1. Switch to the **Map** page.
2. On the **Insert** ribbon, choose **Map** (the basic bubble map).
3. In the **Build** pane:
   - **Location:** `Country` (Territory Lookup)
   - **Bubble size:** `Total Orders`
4. The map renders bubbles whose size reflects each country's order volume.

**Expected result:** A world map with proportional bubbles.

---

## Exercise 6: Format the Map

**Goal:** Apply branded styling and improve readability.

1. With the map selected, open the **Format** pane.
2. **Map style:** choose `Light` or `Road` (or `Dark` if matching a dark theme).
3. **Bubbles:**
   - **Color:** `#20E2D7`.
   - **Size:** adjust until the largest bubble is prominent without obscuring smaller bubbles.
   - **Transparency:** about `20%` so countries are partially visible beneath the bubble.
4. **Map controls:** enable **Auto zoom** so the map re-centers when filtered by the Continent slicer.
5. **Category labels:** Segoe UI, size `10`, with a semi-transparent white background for readability against the map.
6. **Tooltips:** white labels, teal values, dark-gray background at `10%` transparency (matching other visuals).
7. **Title:** off (the Map page header provides context).

**Expected result:** A clean bubble map that re-zooms when the Continent slicer is used and matches the dashboard's tooltip styling.

> **Geocoding note:** The raw `Territory Lookup` file contains `Country`, `Region`, and `Continent`. Bing Maps recognizes country names directly. If any country renders in the wrong location, set the **Data category** of the `Country` column to `Country/Region` in **Model view** and rebuild the map.

---

## Exercise 7: Create Two Bookmarks (Insight + Reset)

**Goal:** Capture an insightful filtered state and a clean unfiltered state on the Customer Detail page.

1. Switch to the **Customer Detail** page.
2. Apply filters that highlight an interesting finding — for example:
   - Year slicer = `2022`.
   - Occupation donut → click `Skilled Manual`.
3. Open the **Bookmarks** pane (View ribbon → Bookmarks).
4. Click **Add** to create a new bookmark.
5. Double-click the new bookmark and rename it `Customer Insight`.
6. Right-click the bookmark and confirm **Data**, **Display**, and **Current page** are checked. Uncheck **All visuals** if the bookmark should affect only specific visuals.
7. Clear all filters on the page (deselect the year, click the donut segment to deselect).
8. In the Bookmarks pane, click **Add** again. Rename the new bookmark `Customer Default`.

**Expected result:** Two named bookmarks captured. Clicking either bookmark in the Bookmarks pane returns the page to that exact state.

---

## Exercise 8: Add Buttons That Trigger the Bookmarks

**Goal:** Provide on-canvas controls for the insight and reset bookmarks.

### Insight button

1. With the Customer Detail page open, on the **Insert** ribbon choose **Buttons → Information**.
2. Drag and resize the button so it sits below the customer KPI cards.
3. With the button selected, in the **Format** pane → **Button**:
   - Enable **Text** and type a short insight, for example: `Among Skilled Manual workers in 2022, the top customer drove $4,683 in revenue.`
   - Set the text font to Segoe UI, italic, size `10`, dark gray.
   - Adjust **Padding** so the text does not overlap the icon.
4. Configure the **Action**:
   - Toggle **Action** on.
   - **Type:** `Bookmark`.
   - **Bookmark:** `Customer Insight`.
   - **Tooltip:** `Click to view the insight`.
5. Configure visual states (Format pane → **Button → Style → Apply settings to**):
   - **Default:** dark-gray fill, white icon and text.
   - **On hover:** teal `#20E2D7` fill, white icon and text.
   - **On press:** slightly darker teal, white icon and text.

### Reset button

1. Insert another button: **Insert → Buttons → Reset** (or use a generic blank button with a "reset" icon).
2. Position it near the insight button.
3. Configure the action:
   - **Type:** `Bookmark`.
   - **Bookmark:** `Customer Default`.
   - **Tooltip:** `Reset all filters`.
4. Apply the same hover and press styling as the insight button.

### Test

1. Hold **Ctrl** and click the insight button. The page returns to the captured filtered state.
2. Hold **Ctrl** and click the reset button. The page clears back to the default view.

**Expected result:** Two on-canvas buttons drive the user between the insight view and the default view. Hover feedback and tooltips make the controls discoverable.

---

## Checkpoints

- Three formatted gauge visuals exist on the Product Detail page (Orders, Revenue, Profit) with optional conditional coloring.
- The Map page contains a styled bubble map that responds to the Continent slicer.
- The Customer Detail page contains two bookmarks (`Customer Insight` and `Customer Default`) and two buttons that trigger them.
- Buttons display clear hover and press states.

---

## Key Takeaways

- Use **gauges** to communicate progress against a target; use **KPI visuals** when a sparkline trend is also needed.
- Maps require valid country / region data; set the **Data category** in Model view if Bing Maps misplaces locations.
- Bookmarks capture the entire state of a page — filters, drill levels, slicer selections, and visibility.
- Buttons with bookmark actions turn a static dashboard into a guided experience.
- Always design **Default**, **On hover**, and **On press** states on every interactive button so users see consistent feedback.
- In Power BI Desktop, hold **Ctrl** when clicking a button to trigger its action; in the Service, a normal click is enough.
