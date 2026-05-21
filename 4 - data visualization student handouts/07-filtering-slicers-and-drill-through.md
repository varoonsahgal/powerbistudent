# Handout 07: Filtering, Slicers, Drill-Through, and Report Interactions

## Purpose

This handout covers all the ways users (and report authors) filter data: the Filters pane (visual / page / report scope), cross-filtering by clicking on a visual, slicer visuals, drill-through pages, and the **Edit interactions** controls that govern how visuals affect each other.

---

## Prerequisites

- The Executive Dashboard, Customer Detail, and Map pages contain at least one visual each.
- A `Continent` column exists on `Territory Lookup`, `Year` on `Calendar`, and `Income Level` and `Occupation` on `Customer Lookup`.

---

## Key Concepts

### The Three Filter Scopes

| Scope | Affects | Where to apply |
|---|---|---|
| **Visual-level** | Only the selected visual | Filters pane, with the visual selected |
| **Page-level** | Every visual on the current page | Filters pane, with no visual selected (or the page-filters area) |
| **Report-level** | Every visual on every page | Filters pane, top section |

### Filter Types

- **Basic filtering** — checkboxes for known values.
- **Advanced filtering** — text or numeric conditions (`contains`, `>`, `between`, etc.).
- **Top N** — show only the top *N* items by a specific metric.
- **Relative date** — `Last N days`, `This year`, etc.

### Cross-Filtering by Selection

Clicking a value in one visual filters or highlights other visuals on the page. **Ctrl + click** stacks selections across visuals. The exact behavior of each pair of visuals is configured under **Edit interactions**:

| Icon | Behavior |
|---|---|
| **Filter** | Other visual is fully filtered to match the selection |
| **Highlight** | Other visual highlights the matching segment without removing the rest |
| **None** | Selection has no effect on the other visual |

> **Analogy — a wired-up control room:** Every visual on a page is a piece of equipment in a control room. **Edit interactions** is the patch panel where you decide which switch controls which screen. By default, every switch is wired to every screen — which is rarely what you want. Spend a few minutes wiring deliberately and the dashboard will feel intentional rather than chaotic.

### Slicers vs. the Filters Pane

A **slicer** is an on-canvas visual that the end user can interact with directly. The **Filters pane** is functionally equivalent but lives off to the side and is sometimes hidden in the Power BI Service. For end-user-facing controls, prefer slicers.

### Drill-Through Pages

A drill-through page is a hidden detail page that filters automatically based on a value the user right-clicks on another page. Configure it once per detail page; users do not need a slicer to choose the row of interest.

> **Analogy — a product page on a shopping site:** Clicking a product card on a search results page jumps you to a detail page that already "knows" which product you picked. Drill-through works the same way: right-click a row, land on a detail page already filtered to that row.

---

## Exercise 1: Explore the Filters Pane

**Goal:** Practice applying filters at all three scopes.

1. On the **Executive Dashboard**, click an empty area of the canvas to deselect any visual.
2. Open the **Filters** pane on the right.
3. Drag `CategoryName` (Product Categories Lookup) into **Filters on this page**.
4. Use **Basic filtering** to deselect `Components`. Confirm that every visual on the page now excludes Components.
5. Drag `Year` into **Filters on all pages**. Set it to `2022`. Switch to other pages and confirm they are filtered too.
6. Remove both filters before continuing (hover the field name → click the eraser icon).

**Expected result:** Confidence applying and removing filters at page and report scope.

---

## Exercise 2: Build a Continent Slicer on the Map Page

**Goal:** Provide a tile-style slicer for filtering the map by continent.

1. Switch to the **Map** page.
2. On the **Insert** ribbon, choose **Slicer**.
3. In the **Build** pane, set **Field** to `Continent` (Territory Lookup).
4. With the slicer selected, open the **Format** pane → **Visual** tab.
5. Under **Slicer settings → Style**, choose **Tile**.
6. **Selection** options:
   - **Multi-select with Ctrl/Cmd:** off (so users can simply click between continents).
   - **Show "Select all" option:** on (adds an `All` button).
7. **Slicer header:** turn off (the field name is unnecessary if positioned cleanly).
8. **Values:** Segoe UI Bold, size `14`, dark text.
9. **Border / Padding:** add a `3` px white border for separation between tiles, and a light-gray background.
10. Resize the slicer to span horizontally near the top of the page.

**Expected result:** A horizontal row of continent buttons. Clicking a button filters every other visual on the page.

---

## Exercise 3: Add a Year Slicer to the Customer Detail Page

**Goal:** Let users filter the Customer Detail page by year.

1. Switch to the **Customer Detail** page.
2. Insert a **Slicer**.
3. **Field:** `Year` (Calendar Lookup).
4. Choose a style appropriate for the data: **Tile**, **Dropdown**, or **Between** (a numeric range slider) — Tile works well for three years (2020, 2021, 2022).
5. Position above the customer cards or beside the line chart.
6. Test: select a single year, then `Ctrl + click` (or rely on multi-select if enabled) to add a second year. Verify all visuals on the page respond.

**Expected result:** A working Year slicer that filters every Customer Detail visual.

---

## Exercise 4: Customize Report Interactions on the Customer Detail Page

**Goal:** Make cross-filtering behave intuitively for the user.

The default cross-filter behavior is rarely correct for every visual pair. Configure each interaction deliberately.

1. Switch to the **Customer Detail** page.
2. Select the line chart (Weekly Customers).
3. On the **Format** ribbon (visible while a visual is selected), click **Edit interactions**.
4. Notice that small icons appear on every other visual on the page.
5. Configure interactions originating from the line chart:
   - On both donut charts → set to **Filter** (selecting a week refilters the demographic mix to that week).
   - On the Top 100 Customers table → set to **None** (the table should remain a stable list).
   - On the headline KPI cards → set to **None** (KPI tiles should reflect the page-level filters only).
6. Select the Top 100 Customers table.
7. Configure interactions from the table:
   - On the line chart → **None** (selecting a customer should not redraw the line chart).
   - On the donut charts → **None**.
8. Select each donut chart and configure interactions:
   - Donuts back to the line chart → **None** (selecting an income level should not narrow the line chart).
   - Donuts to each other → **Filter** is acceptable, or **None** if cross-influence is undesired.
9. Click **Edit interactions** again on the ribbon to exit edit mode.

**Expected result:** Cross-filtering follows the intended user story — slicing time updates demographics, but selecting a single customer does not collapse other visuals.

---

## Exercise 5: Create a Drill-Through Page for Product Detail

**Goal:** Make the Product Detail page filter automatically when a user right-clicks a product elsewhere.

1. Switch to the **Product Detail** page.
2. Click an empty area of the canvas (no visual selected) to expose page-level format options.
3. Open the **Format** pane → **Page information**.
4. Set **Page type** to **Drill through**.
5. In the **Filters** pane, drag `ProductName` (Product Lookup) into the **Drill through** area.
6. A **back-arrow button** is generated automatically at the top-left of the page. Leave it in place.
7. Switch to the **Executive Dashboard**.
8. Right-click any value in the **Top 10 Products** matrix → choose **Drill through → Product Detail**.
9. Verify the Product Detail page opens already filtered to that product.
10. Click the back-arrow on Product Detail to return to the Executive Dashboard. (In Desktop, hold **Ctrl** while clicking the back button.)

> **UX tip:** Add a Card visual on Product Detail that displays the selected product's name (using `Full Name` of the product or a measure built on `SELECTEDVALUE(ProductName)`). Without a breadcrumb, users can lose track of which product they drilled into.

**Expected result:** Right-clicking any product in the Top 10 matrix navigates to a fully filtered Product Detail page.

---

## Exercise 6: Stack Filters via Cross-Selection

**Goal:** Demonstrate the effect of combining a slicer, a chart selection, and a filter pane filter.

1. On the **Customer Detail** page:
2. Select `2022` in the Year slicer.
3. Click a single bar of the Income Level donut (for example, `Average`).
4. In the Filters pane, set the Occupation filter to `Top 3` by Total Orders.
5. Observe the Top 100 Customers table — only customers in 2022 with average income working in one of the top three occupations remain.
6. Clear the chart selection by clicking the donut's selected segment again or clicking blank space.

**Expected result:** A clear understanding of how slicer, cross-filter selection, and filter-pane filters compose into a single combined filter context.

---

## Checkpoints

- The Map page has a Continent tile slicer that filters the map.
- The Customer Detail page has a Year slicer.
- Cross-filter behavior on the Customer Detail page matches the intended user experience (line chart filters demographics; table never redraws other visuals).
- Right-clicking a product in the Top 10 Products matrix opens the Product Detail page already filtered to that product.

---

## Key Takeaways

- Filters live at three scopes: report, page, visual. Pick the narrowest scope that achieves the goal.
- Slicers are user-facing; the Filters pane is author-facing.
- **Edit interactions** is one of the highest-impact features in Power BI — never assume the default cross-filter behavior is correct.
- Drill-through pages eliminate the need for "select a product" slicers and produce focused, single-record detail views.
- Always filter Top N on a unique key column.
