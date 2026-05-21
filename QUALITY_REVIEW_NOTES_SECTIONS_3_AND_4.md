# Quality Review Notes — Sections 3 (DAX) and 4 (Data Visualization)

This file collects unresolved concerns, possible inaccuracies, missing context, and improvement suggestions that were **not** applied directly to the handouts. The handouts themselves were edited only with safe, additive improvements (analogies, common-mistake notes, checkpoint prompts, and clearer key takeaways).

---

## Section 3 — DAX Functions

### 01 — Counting Functions
- **Possible inaccuracy:** Assignment 1 references measures named `Quantity Returned` and `Quantity Sold`, but `Quantity Returned` is not created in this handout. It may be defined earlier in the course; if not, learners will see a missing-measure error. Confirm and either add a brief setup step or cross-reference the originating handout.
- **Missing context:** The "Verify" step for `Total Orders` claims a value "close to 25,164." This is a specific number tied to one version of the AdventureWorks dataset. If the dataset has been re-exported or trimmed, learners will be confused when they see a different value. Consider rephrasing as "in the tens of thousands."
- **Suggested screenshot:** A side-by-side image of the four Card visuals (Total Returns, Total Orders, Returns COUNT, Returns DISTINCTCOUNT) would visually anchor Exercise 4's key insight.

### 02 — Logical Functions and SWITCH
- **Possible inaccuracy:** `Customer Lookup.csv` should be checked to confirm the exact `EducationLevel` text values. The handout lists `Bachelors`, `Partial College`, `High School`, `Partial High School`, and `Graduate Degree`. If any value uses different casing or spacing (e.g., `High School ` with trailing space), the `SWITCH` will silently miss those rows.
- **Missing context:** The `Parent` column built in Exercise 1 is later used in Assignment 2 (`Customer Priority`). A short reminder at the start of Assignment 2 — "this depends on the `Parent` column built in Exercise 1" — would help learners who skip ahead.
- **Suggested improvement (not applied to keep structure):** Adding a third bonus assignment using `IFERROR` would close the loop on the `IFERROR` concept introduced in Key Concepts but never used in an exercise.

### 03 — Text Functions
- **Possible inaccuracy / missing context:** The handout says `Calendar Lookup.csv` "contains only a `Date` column." Confirm against the actual file. If the raw file already includes a `MonthName` or similar column in the version learners have, the `FORMAT` workaround instructions are unnecessary.
- **Missing exercise:** `MID`, `RIGHT`, `LOWER`, and `SUBSTITUTE` appear in the function table but are never used in an exercise. A short demo (e.g., extracting the middle SKU segment with `MID` + two `SEARCH` calls) would round out the topic.
- **Suggested screenshot:** A cropped image of the `ProductSKU` column showing varying prefix lengths (`HL-`, `BK-`, `SO-`) would make the need for `SEARCH` more concrete.

### 04 — Date, Time, and RELATED
- **Possible inaccuracy:** The "Pro tip — measure version" of `Total Revenue` claims `SUMX` "happens at query time" with "no new columns stored on disk." This is correct in spirit, but for VertiPaq the trade-offs are nuanced (calculated columns compress; measures don't). If precision matters for an audience that may push back, consider softening the claim.
- **Missing context:** `RELATEDTABLE` is mentioned as the inverse of `RELATED` and dismissed as "out of scope." A one-sentence example ("e.g., `COUNTROWS(RELATEDTABLE('Sales Data'))` from a row of `Customer Lookup` to count that customer's orders") would prevent the function from feeling like a black box.
- **Suggested screenshot:** A Model view diagram highlighting the active `Sales Data[ProductKey] → Product Lookup[ProductKey]` relationship would make the "RELATED only works in this direction" point land.

### 05 — CALCULATE and Filter Context
- **Possible inaccuracy:** The "Worked Example with the AdventureWorks Data" cites Order `74144` with two specific line items and `ProductKey 574` / `220`. These keys must be verified against the current dataset; if they have shifted, learners doing the exercise will see different numbers and become confused.
- **Missing context:** Exercise 3 introduces `Weekend Orders` but does not show what happens when both a weekend filter and a row-level weekend slicer are applied to the same visual. Adding a brief note about this interaction (the `CALCULATE` filter overrides) would deepen the lesson.
- **Suggested diagram:** A simple flow diagram of "initial filter context → CALCULATE override → relationship pass-through → evaluate" would massively help visual learners understand the three-step process.
- **Best-practice note:** "Push calculations upstream" is good advice but lacks an explicit caution: pushing date logic to the source system makes Power BI time-intelligence functions less reusable. Consider a one-line caveat.

---

## Section 4 — Data Visualization

### 01 — Best Practices and Dashboard Design
- **Missing context:** The reading-pattern claim ("Western readers scan in F or Z patterns") is broadly true but has limited research backing for dashboards specifically. The advice is fine; just framing it as a heuristic rather than a law would be more accurate.
- **Suggested screenshots:** Mock-ups of the four-page sketch (Executive, Map, Product Detail, Customer Detail) would give learners a target to compare their own sketches against.

### 02 — Report Interface and Foundational Elements
- **Possible inaccuracy:** The handout says "Older Power BI versions (pre-April 2023): Visuals were inserted from the Visualizations pane rather than the Insert ribbon." Power BI Desktop **still** offers both the Insert ribbon and the Visualizations pane in current builds. Consider rephrasing to: "the Insert ribbon is the preferred location in newer versions, but the Visualizations pane still works."
- **Suggested screenshot:** An annotated image of the report-view sidebar with the five enabled panes labeled would make Exercise 1 self-explanatory.

### 03 — Cards and KPI Visuals
- **Possible inaccuracy:** The prerequisite list assumes measures named `Previous Month Revenue`, `Previous Month Orders`, `Previous Month Returns`, `Order Target`, `Revenue Target`, `Profit Target`, `Avg Revenue per Customer` already exist. None of these are built in section 3. They must be created in an earlier handout (or this one needs a Setup section). If learners are following only the published material, they will be blocked.
- **Missing context:** The "new card" vs. "old card" debate in Power BI Desktop is not mentioned. Recent versions ship a Preview "Card (new)" with different formatting paths. If the screenshots in the course were captured with the classic Card, learners on a fresh install may see a different UI.
- **Suggested improvement:** A mini "exit ticket" — drag the four headline cards into a slicer-filtered view and confirm they all update — would be a useful checkpoint.

### 04 — Line Charts and Trend Analysis
- **Possible inaccuracy:** "The forecast feature predicts future values based on historical data" is correct but does not mention that Power BI's forecast uses an exponential smoothing algorithm (ETS) and only works on continuous numeric axes. Learners with categorical axes will see the option grayed out and may struggle to diagnose why.
- **Missing exercise content:** The handout teaches the date hierarchy and drill modes but never demonstrates the **forked-arrow / "Expand all"** drill in a follow-up step. Worth a one-sentence example.
- **Suggested screenshot:** The four drill icons in the chart's upper-right corner are small and easy to miss — a zoomed annotated image would help.

### 05 — Bar Charts and Donut Charts
- **Possible inaccuracy:** Color assignments in the Income Level donut (teal / gray / amber) imply a specific brand palette. If the AdventureWorks course palette is documented elsewhere, link to it. If not, add a one-line note that any consistent palette is acceptable.
- **Missing context:** "Components may have no sales" — confirm whether the dataset actually contains `Components` rows. If not, the bar chart will only show three categories and the note is unnecessary.

### 06 — Tables, Matrices, and Conditional Formatting
- **Possible inaccuracy:** The "Apply to series" dropdown in Cell elements is the correct path for current Power BI versions, but earlier versions exposed conditional formatting under each individual value field's right-click menu. Mentioning both paths would future-proof the handout.
- **Suggested screenshot:** The `fx` button is small and easy to miss. An annotated screenshot showing where it sits within the Cell elements section would prevent search-and-rescue questions.

### 07 — Filtering, Slicers, Drill-Through, and Report Interactions
- **Possible inaccuracy:** "In Desktop, hold Ctrl while clicking the back button" — this used to be required for buttons too, but recent Desktop builds allow normal clicks for drill-through back buttons in some contexts. Verify against the current version.
- **Missing context:** Drill-through can be triggered from any visual that contains the drill-through field, not just the Top 10 Products matrix. Adding "from any visual that includes ProductName" would broaden the lesson.
- **Suggested checkpoint:** A short prompt — "Navigate to Product Detail by drilling through, then return to the Executive Dashboard. What state is the Executive Dashboard in?" — would reinforce the back-button behavior.

### 08 — Gauges, Maps, Bookmarks, and Buttons
- **Possible inaccuracy:** "Bing Maps services are available (typically the default in Power BI Desktop and Service)." Bing Maps in Power BI Service depends on **tenant settings**; some enterprise tenants disable it. The handout calls this out for Service but should also mention Azure Maps as the official replacement Microsoft is moving toward.
- **Possible inaccuracy:** The Gauge visual's "Trend axis" property — the handout says "leave blank — gauges in Power BI do not display a sparkline." Confirm this is still true. If the Build pane no longer shows a Trend axis well for Gauge, the instruction is misleading.
- **Missing context:** The reset button described in Exercise 8 ("Insert → Buttons → Reset") may not exist as a built-in option in all versions. If "Reset" is not a default button type, learners will be stuck. A blank button with a reset icon and a bookmark action achieves the same result and is more reliable.
- **Suggested screenshot:** Side-by-side images of the Gauge **Default** vs. **Below Target** state (with the conditional red callout) would land Exercise 4's payoff visually.

---

## Cross-Cutting Suggestions

- **Setup / Bootstrap handout:** A short "Section 3 / 4 Setup" file listing every measure and column the section assumes already exists (with the originating handout reference) would prevent the "missing measure" cascade that several exercises currently risk.
- **Version-tagging:** Power BI Desktop UI changes frequently. Consider adding a "Last verified against Power BI Desktop version: X" line at the top of each handout.
- **Color / theme reference:** A small palette reference (the bright teal `#20E2D7`, the dark gray, etc.) is currently scattered across handouts. Consolidating into a single `dashboard-style-guide.md` would reduce duplication and make rebrands trivial.
- **Glossary:** A one-page glossary of DAX terminology (filter context, row context, iterator, calculated column, measure, expression, table expression) would be a strong addition for learners who skim around the section out of order.
