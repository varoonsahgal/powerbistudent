# Handout 04: Line Charts, Area Charts, and Trend Analysis

## Purpose

This handout covers time-series visuals: line charts, area charts, trend lines, forecasts, custom tooltips, zoom sliders, and the date hierarchy with drill-up / drill-down. The Weekly Revenue chart on the Executive Dashboard and the Weekly Customers chart on the Customer Detail page are built here.

---

## Prerequisites

- Handouts 02 and 03 are complete; the Executive Dashboard has the logo, KPI cards, and KPI visuals in place.
- The Calendar table contains a configured **Date Hierarchy** (Year → Quarter → Month → Week → Date) and helper columns such as `Start of Week` and `Start of Month`.
- Measures **Total Revenue**, **Total Customers**, **Total Orders**, **Total Profit**, **Total Returns** are available.

---

## Key Concepts

### Why Line Charts for Time Series

Line charts are the strongest default for showing change over time. They reveal trend, seasonality, and outliers far better than bar charts when the X-axis is continuous time.

### On-Object Formatting

Most chart elements can be formatted directly on the canvas:

1. Double-click a visual to enter edit mode (a blue border appears).
2. Right-click an element (title, axis, line, label) and choose `Format <element>`.
3. The Format pane jumps directly to that element.

This is faster than scrolling through dozens of Format pane sections.

### Trend Lines

A trend line is an analytical overlay (linear by default) showing the general direction of the data. Other types include exponential, power, logarithmic, and polynomial. Keep the trend line subtle (high transparency) so it does not overwhelm the actual data.

### Forecasts

The forecast feature predicts future values based on historical data and works at whatever time granularity the X-axis currently shows.

- **Forecast length** — number of future periods to project.
- **Ignore last** — exclude the last *N* periods from the calculation. Use this when the most recent period is partial (for example, an in-progress week).
- **Confidence interval** — wider band = higher confidence.

> **Analogy — a weather forecast:** A forecast is a best-guess based on past patterns, not a guarantee. The **confidence band** is the meteorologist's "30%–70% chance of rain" — a wider band means more uncertainty. Always present forecasts as projections, never as facts.

### Date Hierarchy and Drill Modes

Dragging a **Date Hierarchy** (rather than a single date column) onto the X-axis enables drill-down. Drill icons in the upper-right of the chart:

| Icon | Action |
|---|---|
| Up arrow | Drill up one level |
| Down arrow (single) | Toggle drill mode — clicking a data point drills down to the next level for that point |
| Down arrow (double) | Drill down all data to the next level |
| Forked arrow | Expand the next level alongside the current one |

### Zoom Sliders

A zoom slider lets users focus on a sub-range of the X-axis without rebuilding the chart. Zoom can be enabled on the X-axis only, the Y-axis only, or both.

---

## Exercise 1: Build the Weekly Revenue Line Chart

**Goal:** Create a wide line chart on the Executive Dashboard showing revenue by week.

1. On the **Insert** ribbon, choose **Line chart**.
2. Drag and resize the chart so it sits below the headline KPI cards, taking up most of the page width.
3. In the **Build** pane:
   - **X-axis:** `Start of Week` (Calendar table)
   - **Y-axis:** `Total Revenue`
4. Confirm a continuous line appears with weeks across the X-axis and revenue on the Y-axis.

**Expected result:** A single-line chart showing weekly revenue across the full date range of the data.

---

## Exercise 2: Format the Line Chart

**Goal:** Apply the dashboard's visual style.

1. With the chart selected, open the **Format** pane → **Visual** tab.
2. **Title:**
   - Text: `Weekly Revenue`
   - Font: Segoe UI, size `10`, centered.
3. **X-axis** and **Y-axis:** font size `8` for axis values; remove redundant axis titles.
4. **Lines → Colors:** change the series color from the default blue to a medium-dark gray.
5. **Lines → Stroke width:** `2`.
6. **Data labels:** off (a continuous line with weekly granularity becomes cluttered with labels).
7. **Markers:** off (or leave default).
8. **Gridlines:** keep at default subtle gray.

**Expected result:** A clean, neutral, dark-gray line that fits the dashboard's visual identity.

---

## Exercise 3: Add a Custom Tooltip Style

**Goal:** Apply branded colors to the chart's hover tooltip.

1. With the chart selected, scroll the Format pane to **Tooltips**.
2. Set:
   - **Label color:** White
   - **Value color:** `#20E2D7` (bright teal)
   - **Drill text color:** White
   - **Background color:** Medium-dark gray, **Transparency** `10%`
3. Hover over the line on the canvas to verify the new tooltip styling.

**Expected result:** A dark, branded tooltip appears on hover, with white labels and teal values.

---

## Exercise 4: Add a Trend Line and a Zoom Slider

**Goal:** Layer a subtle linear trend line over the data and let users zoom into a date range.

1. Format pane → **Trend line** → toggle **On**.
   - **Style → Transparency:** `75%` (subtle).
   - **Color:** keep default or set to a medium gray.
2. Format pane → **Zoom slider** → toggle **On**.
3. Expand **Zoom slider** options and disable **Y-axis** so only the **X-axis** slider is shown.
4. On the canvas, drag the slider handles to focus on a sub-range, then double-click the slider to reset.

**Expected result:** A faint linear trend line crosses the chart, and a slider below the X-axis allows focusing on any time range.

---

## Exercise 5: Add a Forecast (with the Partial-Week Caveat)

**Goal:** Project future weeks while accounting for the incomplete final week of data.

1. Format pane → **Forecast** → toggle **On**.
2. Set:
   - **Forecast length:** `10` (projects 10 weeks ahead with weekly granularity).
   - **Ignore last:** `1` — this excludes the partial final week from the forecast calculation. Without this, the in-progress week's lower revenue distorts the projection.
   - **Confidence interval:** `95%`.
3. Style the forecast subtly:
   - **Forecast line transparency:** `90%`.
   - **Confidence band transparency:** very high so the band sits as a soft shadow.
4. Inspect the chart: a dotted (or shaded) projection extends past the last week, with a confidence band around it.

> **Why ignore the last period?** The AdventureWorks dataset ends mid-week (the last week contains only a few days). Including that partial week in the forecast biases the projection downward. Setting **Ignore last = 1** removes that distortion.

**Expected result:** A 10-week forecast is shown with a 95% confidence band, and the partial final week is excluded.

---

## Exercise 6: Convert the Line Chart to an Area Chart (Optional Demo)

**Goal:** Demonstrate fast visual-type switching.

1. With the chart selected, open the visual-type selector at the top of the **Build** pane.
2. Choose **Area chart**. The line chart converts in place.
3. In the Format pane → **Lines → Shading area**, set transparency to about `80%` so the fill is subtle.
4. Convert back to a line chart for the rest of the section.

**Expected result:** The same data is shown as a filled area chart; flipping back returns to the styled line chart.

---

## Exercise 7: Replace the X-Axis with the Date Hierarchy

**Goal:** Enable drill-down across years, quarters, months, weeks, and days.

1. In the **Build** pane, remove `Start of Week` from the X-axis.
2. From the **Data** pane, drag the **Date Hierarchy** (single object containing all five levels) into the X-axis well.
3. The chart now shows revenue by **Year** by default.
4. Use the drill icons in the chart's upper-right corner:
   - Click the double down-arrow to drill down to **Quarter**, then **Month**, then **Week**, then **Date**.
   - Click the up-arrow to roll back up.
   - Click the single down-arrow to enter drill-mode and click a specific data point (e.g., a particular month) to drill into that point only.
5. Decide on a default level appropriate for the dashboard (weekly is a common choice for the Executive view).

> **Common pitfall — partial weeks:** Drilling to the Week level surfaces partial weeks (the last week of a quarter or the in-progress current week). If this is confusing for the audience, remove the Week level from the hierarchy or use Year → Quarter → Month → Date instead.

**Expected result:** The line chart now supports interactive drill-up and drill-down across the date hierarchy.

---

## Exercise 8: Customer Detail — Weekly Customers Line Chart

**Goal:** Build a matching line chart on the Customer Detail page.

1. Switch to the **Customer Detail** page.
2. Insert a **Line chart**.
3. **X-axis:** `Start of Week`. **Y-axis:** `Total Customers`.
4. Apply the same formatting as the Weekly Revenue chart:
   - Title: `Weekly Customers`, Segoe UI size `10`, centered.
   - Line color: medium-dark gray, stroke width `2`.
   - Data labels off; markers off.
   - Tooltip: white labels, teal values, gray background at `10%` transparency.
5. Add a **Trend line** at `75%` transparency.
6. Add a **Zoom slider** on the **X-axis** only.
7. Save the file.

**Expected result:** A second weekly trend chart that is visually consistent with the Executive Dashboard.

---

## Exercise 9: Customer Detail — Switch to the Date Hierarchy

**Goal:** Provide drill capability on the customer trend chart.

1. With the Customer Detail line chart selected, remove `Start of Week` from the X-axis.
2. Drag the **Date Hierarchy** onto the X-axis.
3. Use the drill icons to confirm the chart can move between Year, Quarter, Month, Week, and Date.
4. Decide on a default starting level (commonly **Month** or **Week**) and leave the chart at that level.

**Expected result:** The Customer Detail trend chart supports the same drill behavior as the Executive Dashboard chart.

---

## Checkpoints

- The Weekly Revenue chart shows a clean dark-gray line with a custom tooltip, trend line, X-axis zoom slider, and a 10-week forecast that ignores the partial final week.
- The chart supports drill-down via the Date Hierarchy.
- The Customer Detail page contains a matching Weekly Customers chart.

---

## Key Takeaways

- Line charts are the default for time series; switch to area only when filling beneath the line genuinely helps the message.
- Use on-object formatting (right-click an element on the chart) to reach the right Format pane section quickly.
- Trend lines and forecasts are analytical overlays, not data — keep them subtle and explain them in tooltips or footnotes.
- Always set **Ignore last** when the most recent time period is incomplete; otherwise the forecast is biased.
- The **Date Hierarchy** turns a single line chart into an interactive multi-level explorer.
