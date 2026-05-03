# Handout 03: Date Tables, Calendar Tools, and Rolling Calendars

## Purpose

A dedicated date table is one of the most important components of any Power BI data model. This handout covers how to load the AdventureWorks calendar, use Power Query's date tools to build out a full calendar table, handle locale-related date errors, and create a dynamic rolling calendar using M code.

---

## Prerequisites

- Power BI Desktop is open with the `Customer Lookup` and `Product Lookup` tables already loaded.
- The `AdventureWorks Calendar Lookup.csv` file is available in the `AdventureWorks Raw Data` folder.

> **Starting state:** `AdventureWorks Calendar Lookup.csv` contains a single column named `Date` with daily date values from `2020-01-01` through `2022-06-30`.

---

## Part 1: Why a Date Table Matters

A date table gives you a single, clean column of dates that spans the full range of your data. By connecting this table to your sales and returns data through a date relationship, you can:

- Filter and slice reports by **day, week, month, quarter, or year**.
- Perform time-intelligence calculations (period-over-period comparisons, year-to-date totals).
- Keep your fact tables clean — the dates in your sales table are keys, not attributes.

The AdventureWorks calendar covers `2020-01-01` to `2022-06-30` — roughly two and a half years of data.

---

## Part 2: Date-Specific Tools in Power Query

Date tools appear in the **Add Column** ribbon when a date column is selected. The most important options are:

| Tool | What it does |
|---|---|
| **Age** | Calculates the difference between each date and today (`DateTime.LocalNow()`) |
| **Date Only** | Strips the time component from a date-time field |
| **Year / Quarter / Month / Week / Day** | Extracts individual date components as new columns |
| **Name of Day / Name of Month** | Extracts the day or month name as text |
| **Start of Week / Month / Quarter / Year** | Returns the first date of the containing period |
| **End of Week / Month / Quarter / Year** | Returns the last date of the containing period |
| **Earliest / Latest** | Returns the single earliest or latest date in the column *(Transform tab only)* |

> **Best practice:** Always create new date columns using the **Add Column** tab. The **Transform** tab would overwrite the original `Date` column. You almost always want to keep the original date and add the derived attributes alongside it.

> **Always start from the `Date` column.** If you accidentally have a derived column (such as `Start of Week`) selected when you add a new column, the new column will be based on that derived value instead of the original date. This produces incorrect results — for example, a "Start of Month" based on "Start of Week" will be off by days.

---

## Part 3: Exploring Earliest and Latest Dates

Before building out the calendar, it is helpful to confirm the date range of your data.

1. In Power Query Editor, select the `Calendar Lookup` table.
2. Select the `Date` column.
3. On the **Transform** ribbon, click **Date** > **Earliest**.
   - Result: `January 1, 2020`.
4. Delete the applied step.
5. On the **Transform** ribbon, click **Date** > **Latest**.
   - Result: `June 30, 2022`.
6. Delete the applied step to restore the full table.

---

## Exercise 1: Build Out the Calendar Lookup Table

**Goal:** Add the following columns to the `Calendar Lookup` table using the Add Column date tools.

| Column name | Tool to use |
|---|---|
| `Day Name` | Add Column > Date > Day > Name of Day |
| `Start of Week` | Add Column > Date > Week > Start of Week |
| `Start of Month` | Add Column > Date > Month > Start of Month |
| `Month Name` | Add Column > Date > Month > Name of Month |
| `Month Number` | Add Column > Date > Month > Month |
| `Start of Quarter` | Add Column > Date > Quarter > Start of Quarter |
| `Start of Year` | Add Column > Date > Year > Start of Year |
| `Year` | Add Column > Date > Year > Year |

### Steps

1. In Power Query Editor, select the `Calendar Lookup` table.
2. Click the `Date` column header to ensure it is selected.
3. On the **Add Column** ribbon, click **Date** > **Day** > **Name of Day**.
   - A new `Day Name` column is added on the right.
4. Click the `Date` column again to re-select it before each subsequent step.
5. Add each remaining column from the table above, always reselecting `Date` first.
6. Verify that `Start of Week` shows Sundays by default (for example, `2020-01-05` is a Sunday).

**Expected result:** The `Calendar Lookup` table should have 9 columns: `Date`, `Day Name`, `Start of Week`, `Start of Month`, `Month Name`, `Month Number`, `Start of Quarter`, `Start of Year`, and `Year`.

---

## Part 4: Customizing the Start of Week

By default, `Start of Week` uses **Sunday** as the first day. To change this:

1. In the Applied Steps pane, click the **gear icon** next to the `Inserted Start of Week` step.
2. In the formula window, find the `Date.StartOfWeek` function.
   - The function signature is: `Date.StartOfWeek([Date], optional firstDayOfWeek)`
   - By default, no second argument is provided (Sunday is the default).
3. To use **Monday** as the first day, add `Day.Monday` as the second argument:
   ```
   Date.StartOfWeek([Date], Day.Monday)
   ```
4. Click **OK**. The `Start of Week` column now shows Mondays.

> **Readable vs. numeric day codes:** You can also write `1` instead of `Day.Monday`, but `Day.Monday` is more readable to anyone reviewing the M code later.

---

## Checkpoint ✅

After completing Exercise 1, verify:

- The `Calendar Lookup` table has 9 columns.
- `Month Name` shows values like `January`, `February`, etc.
- `Month Number` shows `1` for January, `2` for February, etc.
- `Year` shows `2020`, `2021`, or `2022`.
- `Start of Week` shows the Monday of each week (if you completed Part 4).

---

## Part 5: Handling Date Locale Errors

### The Problem

When a CSV file was created using a **day/month/year** date format (common in the UK and many other countries) but is being opened on a machine set to **month/day/year** (the US default), Power BI will fail to parse dates after the 12th of any month.

**Why the 12th?** Dates like `01/05/2023` are ambiguous — they could mean January 5 (US) or May 1 (UK). But `13/01/2023` is unambiguous to a human: day 13 cannot be a month, so it must be January 13 in day/month/year format. Power BI, interpreting it as month/day/year, cannot parse month 13 and returns an error.

---

### The Fix: Change Data Type Using Locale

**Step 1 — Standardize the column to text first:**

If the column contains a mix of dates and numbers (which can happen with ambiguous date strings), change the column data type to **Text** first so all values are treated the same.

**Step 2 — Change type using locale:**

1. Left-click the data type icon in the column header.
2. Select **Using Locale...**.
3. Set **Data Type** to `Date`.
4. Set **Locale** to the locale of the **source file** — for example, `English (United Kingdom)` for a UK-format file.

   > **Important:** Use the locale of the file's origin, not your own locale. If the file came from a UK colleague, select `English (United Kingdom)`. If you select your own US locale, the dates will still be parsed incorrectly.

5. Click **OK**.
6. Verify that all dates now display in the expected format without errors.

> **This same process applies to currency columns** if decimal separators or thousands separators differ between locales (for example, some European countries use a comma as the decimal separator).

---

## Part 6: Creating a Rolling Calendar with M Code

### When to Use a Rolling Calendar

The `AdventureWorks Calendar Lookup.csv` is a fixed dataset covering a known date range. For live reports that refresh regularly, you want a calendar that **automatically extends itself** each day the query is refreshed. This is called a rolling calendar.

---

### How a Rolling Calendar Works

The rolling calendar:

1. Starts at a fixed date you define (for example, January 1, 2023).
2. Calculates today's date dynamically using `DateTime.LocalNow()`.
3. Generates a list of every date between those two points.
4. Converts that list to a table and formats it as a date column.
5. Each time the query is refreshed, the end date advances automatically.

---

## Exercise 2: Create a Rolling Calendar

**Goal:** Create a query that generates a daily date list from January 1, 2023, through today.

### Steps

**Step 1 — Create a blank query**

1. In Power Query Editor, click **New Source** > **Blank Query**.
2. In the **Queries** pane, rename the query to `Rolling Calendar`.

**Step 2 — Enter the start date literal**

1. Click in the formula bar.
2. Type the following and press **Enter**:
   ```
   = #date(2023, 1, 1)
   ```
   This creates a single date value: `January 1, 2023`.

**Step 3 — Add the date list formula**

1. Click the **fx** icon in the formula bar to add a new custom step.
2. Replace the default content with the following M code:
   ```
   = List.Dates(
       Source,
       Number.From(DateTime.LocalNow()) - Number.From(Source),
       #duration(1, 0, 0, 0)
   )
   ```
3. Press **Enter**.

   A list of dates from `2023-01-01` through today is generated.

> **What this formula does:**
> - `Source` = the start date literal you defined (`2023-01-01`).
> - `DateTime.LocalNow()` = today's date and time.
> - `Number.From(DateTime.LocalNow()) - Number.From(Source)` = the number of days between today and the start date.
> - `#duration(1, 0, 0, 0)` = an increment of exactly 1 day.
> - Together, this produces a list of every day from the start date through today.

> **M is case-sensitive.** `List.Dates` is correct; `list.dates` will throw an error. Always match the capitalization exactly.

**Step 4 — Convert the list to a table**

1. A **List Tools** ribbon appears. Click **To Table**.
2. Leave the delimiter settings at their defaults and click **OK**.
3. A single-column table is created.

**Step 5 — Rename the column and set the data type**

1. Double-click the column header and rename it to `Date`.
2. Click the data type icon in the header and select **Date**.

**Step 6 — Optionally add date attribute columns**

Use the same Add Column date tools from Exercise 1 to add `Year`, `Month Name`, `Start of Quarter`, etc.

**Step 7 — Save**

Click **Close & Apply**.

---

### Checkpoint ✅

After Exercise 2:

- The `Rolling Calendar` table starts on `2023-01-01`.
- The last date in the table matches today's date.
- The single column is named `Date` with a `Date` data type.
- If you refresh the query tomorrow, a new row for tomorrow's date will appear automatically.

> **Note for this course:** The AdventureWorks data only extends through June 2022. For the course exercises, use the `Calendar Lookup` table (loaded from the CSV) rather than the rolling calendar. The rolling calendar is a technique to keep in your toolkit for live reporting scenarios.

---

## Key Takeaways

- A date table is essential for time-based filtering and time-intelligence calculations.
- Always create new date-derived columns using **Add Column**, not **Transform**.
- Always have the original `Date` column selected before adding a new date attribute — not a derived column.
- `Start of Week` defaults to Sunday. Use `Day.Monday` as a second argument to `Date.StartOfWeek` to change it.
- When importing date files from other regions, use **Change Type > Using Locale** and specify the locale of the **source file**, not your own locale.
- A rolling calendar uses `DateTime.LocalNow()` and `List.Dates` to generate a self-updating date range in M code.
- M code is **case-sensitive** — function names must match exactly.
