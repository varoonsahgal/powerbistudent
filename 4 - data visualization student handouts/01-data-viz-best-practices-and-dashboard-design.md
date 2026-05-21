# Handout 01: Data Visualization Best Practices and Dashboard Design

## Purpose

This handout introduces the conceptual foundations for designing effective Power BI reports and dashboards. Before opening Power BI and dropping visuals on a canvas, three guiding questions and a six-step design framework are used to plan a multi-page AdventureWorks dashboard.

---

## Prerequisites

- The AdventureWorks data model is complete (Power Query transformations, relationships, hierarchies, and DAX measures from prior sections are all in place).
- A blank or near-blank report file is available to begin building visuals.
- Familiarity with the AdventureWorks dataset (Sales, Returns, Calendar, Customer, Product, Product Categories, Product Subcategories, and Territory tables).

---

## Key Concepts

### The Three Key Data Visualization Questions

Before choosing any chart, work through these three questions:

1. **What type of data am I working with?**
   - Time series (date / week / month) → line and area charts
   - Geospatial (country, continent, region) → maps
   - Categorical (category, subcategory) → bar and column charts
   - Hierarchical → tree maps, matrices with drill-down
   - Less common: financial, text, funnel stages, survey responses

2. **What am I trying to communicate?**

| Goal | Common visuals |
|---|---|
| **Comparison** (across categories or over time) | Bar / column charts, clustered columns, line / area charts, heat maps, radar charts |
| **Composition** (parts of a whole) | Stacked bar / column, pie / donut, stacked area, waterfall, funnel, tree map, sunburst |
| **Distribution** (frequency of values) | Histograms, density plots, box-and-whisker plots |
| **Relationship** (correlation between variables) | Scatter plots, bubble charts, data tables, correlation matrices |

3. **Who is the audience?**

| Audience | Needs | Visual style |
|---|---|---|
| **Analyst** | Granular detail; root-cause analysis | Tables, combo charts, denser data |
| **Manager** | Summarized data with actionable insight | Mostly basic charts, with some detail to support recommendations |
| **Executive** | Crystal-clear KPIs at a glance | KPI cards, simple charts, minimal detail |

> **Bottom line:** Tried-and-true chart types — bar, column, line, histogram, scatter — are the right choice in roughly 90% of cases. Keep it simple.

### The Dashboard Design Framework (6 Steps)

A dashboard is an analytics tool that:

1. Consolidates data from multiple sources.
2. Tracks key metrics at a glance.
3. Facilitates data-driven storytelling and decision-making.

The six design steps:

1. **Define the purpose** — who is it designed for and what is it trying to communicate?
2. **Choose the right metrics.**
3. **Present the data effectively.**
4. **Eliminate clutter and noise.**
5. **Use layout to focus attention.**
6. **Wrap it all into a clear story.**

> *"Perfection is achieved not when there's nothing more to add, but when there's nothing left to take away."*
> Clarity and simplicity beat complexity in data visualization.

### Reading Patterns: F and Z

Western readers scan a screen in predictable patterns. Use this to your advantage when placing the most important content.

- **F-pattern:** Top-left → right; back, down → right; back, down → right.
- **Z-pattern:** Top-left → top-right; diagonally to bottom-left; bottom-left → bottom-right.

In both patterns, the **top strip** is the most valuable real estate. Place the company logo (for context) and the highest-level KPIs there. As the eye moves down, increase granularity.

> **Analogy — a newspaper front page:** A newspaper puts the masthead and the biggest headline at the top. Smaller headlines and supporting details run further down the page. A dashboard works the same way — the top strip is the front page, and detailed visuals are the inside columns.

---

## The AdventureWorks Project Brief

The dashboard must serve a **manager** audience and accomplish four goals:

1. Track high-level KPIs.
2. Compare regional performance.
3. Analyze product-level trends.
4. Identify high-value customers.

That is too much information for a single page. The dashboard will use **four pages**, each with a distinct purpose.

| Page | Purpose | Key visuals |
|---|---|---|
| **Executive Dashboard** | Top-line KPIs and trending | KPI cards, line chart with date hierarchy, bar chart by category, top-products matrix |
| **Map** | Regional / geospatial performance | Bubble map, continent slicer |
| **Product Detail** | Performance vs. targets and trends for a single product | Gauges (orders, revenue, profit), trend charts, returns chart |
| **Customer Detail** | Demographic composition and high-value customers | KPI cards, donut charts (income, occupation), customer trend, top-customer table |

---

## Exercise 1: Apply the Three Questions to AdventureWorks

**Goal:** Confirm the visual language of the upcoming dashboard before any visuals are built.

1. List the data types present in the AdventureWorks model:
   - Time series → `Calendar` table (Date, Start of Week, Start of Month, Year, etc.)
   - Geospatial → `Territory` (Region, Country, Continent)
   - Categorical → `Product Categories Lookup` (CategoryName), `Customer` (Occupation, Education Level)
   - Hierarchical → date hierarchy and category → subcategory → product hierarchy
2. Identify the primary communication goals: **comparison** (across categories and over time) and **composition** (customer demographics).
3. Confirm the audience: **managers**. Skew toward simple, standard charts but allow some detail to surface product- and customer-level insights.

**Expected result:** A short written summary that justifies why the dashboard will lean heavily on line charts, bar charts, donut charts, KPI cards, and a map — not scatter plots or histograms.

---

## Exercise 2: Sketch the Executive Dashboard Layout

**Goal:** Produce a low-fidelity sketch of the Executive Dashboard before opening Power BI.

1. On paper or in a whiteboarding tool, draw a page-shaped rectangle.
2. Reserve a thin **vertical strip on the left** for page-navigation icons.
3. Across the **top strip**, place:
   - The AdventureWorks logo on the far left.
   - Four big KPI cards: **Revenue**, **Profit**, **Orders**, **Return %**.
4. Below the top strip, place a **wide line chart** (weekly revenue trend with date hierarchy).
5. Beside or below the line chart, add three **monthly KPI cards** (revenue, orders, returns vs. previous month) for context.
6. In the lower portion of the page, place:
   - A **bar chart** breaking revenue down by product category.
   - A **matrix** showing the top 10 products by revenue with conditional formatting.
7. Note that granularity increases from top to bottom — high-level KPIs at the top, product-level detail at the bottom.

**Expected result:** A simple sketch that follows the F-pattern, places brand and KPIs in the most valuable real estate, and matches the four manager-level goals.

---

## Exercise 3: Sketch the Remaining Pages

**Goal:** Outline the Map, Product Detail, and Customer Detail pages.

1. **Map page**
   - Top: a slicer for **Continent** (and optionally Country).
   - Center: a bubble map showing **Total Orders** by Country.
2. **Product Detail page**
   - Three **gauge charts** comparing actual to target for Orders, Revenue, and Profit.
   - Trend visuals for revenue / profit and a column chart for returns.
3. **Customer Detail page**
   - KPI cards (Total Customers, Revenue per Customer).
   - Donut charts for **Income Level** and **Occupation** (composition).
   - A line chart showing customers over time.
   - A table listing the top 100 customers.
   - An **insight button** that uses a bookmark to highlight a specific finding (added later).

**Expected result:** A complete sketch package for all four pages that will guide every build step in the rest of the section.

---

## Checkpoints

- The sketch shows four distinct pages, each tied to a project goal.
- The Executive Dashboard places the logo and big KPI cards along the top.
- A consistent left-side navigation strip appears on every page.
- Visual choices match the three-question analysis (comparison + composition + manager audience).

---

## Key Takeaways

- **Plan before you build.** Answer the three questions — *what data, what message, who is the audience* — before opening Power BI.
- **Match the chart to the goal.** Comparison → bars and lines. Composition → stacked bars or donuts (sparingly). Distribution → histograms. Relationship → scatter plots.
- **Familiar beats fancy.** Bar, column, line, and KPI cards solve the vast majority of business questions. Reach for unusual visuals only when the message demands it.
- **One page, one purpose.** When a single page tries to do everything, it does nothing well. Split into focused pages and link them with navigation.
- **Top strip is prime real estate.** Place the brand and the most important KPIs where the eye lands first.
- **Sketch on paper first.** Five minutes of sketching saves an hour of dragging visuals around the canvas.

---

## Reference: AdventureWorks Tables Used in This Section

| Table | Source file | Purpose |
|---|---|---|
| Sales Data | `AdventureWorks Sales Data 2020.csv`, `…2021.csv`, `…2022.csv` | Fact table for orders / revenue |
| Returns Data | `AdventureWorks Returns Data.csv` | Fact table for returns |
| Calendar Lookup | `AdventureWorks Calendar Lookup.csv` | Time dimension and hierarchy |
| Customer Lookup | `AdventureWorks Customer Lookup.csv` | Customer demographics |
| Product Lookup | `AdventureWorks Product Lookup.csv` | Product attributes |
| Product Categories Lookup | `AdventureWorks Product Categories Lookup.csv` | Category labels |
| Product Subcategories Lookup | `AdventureWorks Product Subcategories Lookup.csv` | Subcategory labels |
| Territory Lookup | `AdventureWorks Territory Lookup.csv` | Region / Country / Continent |

> **Note:** Several columns referenced throughout this section (for example, `Full Name`, `Income Level`, `Start of Week`, `Start of Month`, `Year`) are **calculated** in Power Query or DAX during earlier sections. They are not columns in the raw CSV files. The raw `Customer Lookup` file uses `FirstName` / `LastName` and `AnnualIncome`; the raw `Calendar Lookup` file contains only a single `Date` column.
