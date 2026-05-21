# Handout 02: Report Interface and Foundational Elements

## Purpose

This handout walks through the Power BI report-view interface and shows how to build the structural foundation of the AdventureWorks dashboard: report pages, the AdventureWorks logo, KPI-card background shapes, and a left-side navigation strip. The Selection pane is also introduced for keeping a complex canvas organized.

---

## Prerequisites

- Handout 01 (the page sketches) is complete.
- The AdventureWorks `.pbix` file is open in Power BI Desktop.
- The course resource folder containing the `AdventureWorks logo.png` file (and other dashboard graphics) is available locally.

---

## Key Concepts

### The Report View

The bottom icon on the left navigation rail is **Report view** — this is where visuals are built. Most work in this section happens here, with occasional trips to **Model view** to verify relationships.

The two ribbon tabs that matter most for visual construction:

- **Insert** — visuals (including AI visuals), text boxes, buttons, shapes, and images.
- **View** — themes, page view, gridlines, snap-to-grid, lock objects, and which side panes are visible.

### Side Panes

From **View**, enable the panes that will be used throughout the section:

- **Filters**
- **Data**
- **Format**
- **Bookmarks**
- **Selection**

When all five are activated, Power BI displays a compact **vertical sidebar** on the right with one tab per pane. Clicking a tab shows or hides that pane without revisiting the View ribbon.

> **Older Power BI versions (pre-April 2023):** Visuals were inserted from the **Visualizations** pane rather than the Insert ribbon. The same options exist; only the location differs.

### The Page Tab Strip

The bottom-left corner of the report shows one tab per report page.

- **Double-click** a tab to rename it.
- Click the **+** button to add a page.
- Drag tabs to reorder them.

### The Selection Pane

The Selection pane lists every object on the active page. It is essential for:

- Renaming generic objects (e.g., `Shape`, `Image`) into descriptive names (`AW Logo`, `KPI Card Background 1`).
- Toggling visibility with the eye icon to find or temporarily hide overlapping objects.
- Grouping related objects so they can be selected, moved, and formatted together.

> **Analogy — layers in a design app:** Think of the Selection pane like the Layers panel in PowerPoint or Photoshop. Each visual, shape, or image lives on its own layer; you can rename, hide, lock, and reorder layers without disturbing the others.

### Object Layering (Z-order)

New objects appear in front of existing ones. Use **Format → Arrange → Bring forward / Send backward** (or right-click the object) to control which object sits on top.

---

## Exercise 1: Tour the Report-View Interface

**Goal:** Confirm the panes and ribbon tabs that will be used through the remainder of the course.

1. Open the AdventureWorks `.pbix` file.
2. Click the **Report view** icon (bar chart) on the far-left rail.
3. Delete any leftover slicers, matrices, or visuals from previous sections so the canvas is empty.
4. On the ribbon, click **View**.
5. In the **Show panes** group, enable each of the following so they appear in the right-side sidebar:
   - Filters
   - Data
   - Format
   - Bookmarks
   - Selection
6. Confirm a vertical sidebar of pane tabs is now visible on the right edge of the canvas.
7. Leave **Gridlines**, **Snap to grid**, and **Lock objects** unchecked for now.

**Expected result:** A clean canvas with the five side panes available as toggle tabs.

---

## Exercise 2: Create the Four Report Pages

**Goal:** Set up one page per dashboard view.

1. At the bottom of the report, double-click the existing page tab and rename it `Exec Dashboard`.
2. Click the **+** button at the right end of the tab strip to add a new page; rename it `Map`.
3. Add another page; rename it `Product Detail`.
4. Add another page; rename it `Customer Detail`.
5. Click back to the `Exec Dashboard` tab.

**Expected result:** Four named, empty report pages, with `Exec Dashboard` selected.

---

## Exercise 3: Insert the AdventureWorks Logo

**Goal:** Place the AdventureWorks logo in the upper-left corner of the Executive Dashboard.

1. On the **Insert** ribbon, click **Image**.
2. A placeholder rectangle appears on the canvas.

   > In Power BI versions before April 2023 a file dialog opens directly. Newer versions place a placeholder; the upload step lives in the Format pane.

3. With the placeholder selected, open the **Format** pane on the right.
4. Expand **Style**, then **Image**.
5. Choose **Upload image** and browse to the course resource folder.
6. Double-click `AdventureWorks logo.png`. The image loads into the placeholder.
7. Drag the image to the upper-left of the canvas and resize it as needed. Zoom out (bottom-right zoom controls) to confirm placement against the page edges.

**Expected result:** The AdventureWorks logo appears at the top-left of the Executive Dashboard.

---

## Exercise 4: Build the KPI Card Background Shapes

**Goal:** Create four matching dark-gray rounded rectangles that will sit behind the four KPI card values.

1. On the **Insert** ribbon, choose **Shapes → Rounded rectangle**.
2. With the new shape selected, right-click → **Format shape** to open the Format pane.
3. Expand **Shape** and adjust:
   - **Rounded corners:** `15%`
   - **Fill color:** medium-dark gray (the same gray will be reused throughout the dashboard)
   - **Border:** off
4. Expand **Size & position** and set:
   - **Height:** `100`
   - **Width:** `225`
5. Move the shape to the right of the logo, near the top of the canvas.
6. With the shape selected, press **Ctrl + C**, then **Ctrl + V** three times to create a total of four identical shapes.
7. Drag the four shapes into roughly evenly-spaced positions across the top strip.
8. Click an empty area of the canvas and use a marquee selection (or Ctrl+Click) to select all four rectangles.
9. On the contextual **Format** ribbon (visible while shapes are selected), click **Align → Distribute horizontally**, then **Align → Align top**.
10. Use the arrow keys to nudge spacing if needed; reapply **Distribute horizontally** after any adjustment.

**Expected result:** Four pixel-perfect, evenly-spaced dark-gray rounded rectangles aligned across the top of the page, ready to host KPI values.

> **Tip:** Power BI shows red dotted alignment guides while dragging shapes. Use them for quick visual alignment, then rely on the Distribute commands for exact spacing.

---

## Exercise 5: Build the Left-Side Navigation Strip

**Goal:** Add a vertical dark-gray bar on the left side of every page that will later host page-navigation buttons.

1. On the **Insert** ribbon, choose **Shapes → Rectangle** (the plain rectangle, not rounded).
2. In the **Format** pane:
   - **Fill color:** the same medium-dark gray used for the KPI backgrounds.
   - **Border:** off.
3. In **Size & position**:
   - **Width:** `45`
   - **Height:** stretch from top to bottom of the canvas.
4. Position the rectangle so it sits flush against the left edge of the page.
5. With the rectangle selected, press **Ctrl + C**.
6. Click each remaining page tab in turn (`Map`, `Product Detail`, `Customer Detail`) and press **Ctrl + V** to paste the bar onto each page.

**Expected result:** A consistent left-side navigation strip on all four report pages.

---

## Exercise 6: Organize Objects in the Selection Pane

**Goal:** Replace generic object names with descriptive labels and group related objects.

1. Open the **Selection** pane from the right-side sidebar.
2. Click the AdventureWorks logo on the canvas — its row highlights in the Selection pane.
3. Double-click its name and rename it `AW Logo`.
4. Rename the four rounded rectangles to `KPI BG 1` through `KPI BG 4`.
5. Rename the left-side rectangle to `Left Nav BG`.
6. In the Selection pane, **Shift + click** the four KPI background rows to multi-select.
7. Right-click the selection and choose **Group**.
8. Double-click the new group label and rename it `KPI Backgrounds`.
9. Click the eye icon next to any object or group to confirm it toggles visibility on the canvas, then turn visibility back on.

**Expected result:** The Selection pane lists clearly named objects, with the four KPI backgrounds collapsed inside a single `KPI Backgrounds` group.

> **Why this matters:** A finished dashboard page can contain dozens of overlapping objects. Without descriptive names, finding the correct shape later becomes very slow.

---

## Checkpoints

- The five side panes (Filters, Data, Format, Bookmarks, Selection) are accessible from the right sidebar.
- Four named report pages exist: `Exec Dashboard`, `Map`, `Product Detail`, `Customer Detail`.
- The Executive Dashboard contains the logo, four aligned KPI background rectangles, and a left-side navigation strip.
- The same left-side navigation strip is present on every other page.
- The Selection pane shows descriptive names and at least one logical group.

---

## Key Takeaways

- The **Insert** and **View** ribbons cover almost every interface action used in report building.
- Enabling all five side panes converts them into a vertical tab strip — a major time saver.
- Shapes (rounded rectangles, plain rectangles) are the building blocks of dashboard layout.
- Aligning and distributing multiple shapes once is faster than nudging each one by hand.
- Renaming and grouping objects in the Selection pane is essential for keeping a dense canvas manageable.
