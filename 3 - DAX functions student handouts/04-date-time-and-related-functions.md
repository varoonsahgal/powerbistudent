# Handout 04: DAX Date, Time, and RELATED Functions

## Purpose

This handout covers two related groups of DAX functions:

1. **Date and time functions** — `TODAY`, `NOW`, `DAY`, `MONTH`, `YEAR`, `HOUR`, `MINUTE`, `SECOND`, `WEEKDAY`, `WEEKNUM`, `EOMONTH`, and `DATEDIFF`.
2. **The `RELATED` function**, which lets a calculated column on the **many** side of a relationship pull values from the **one** side — conceptually similar to `VLOOKUP` in Excel.

Together, these functions enable richer calendar tables and let you bring lookup-table attributes into fact tables for row-level calculations.

---

## Prerequisites

- The AdventureWorks model is loaded with all relationships. In particular, `Sales Data` (many) is related to `Product Lookup` (one) via `ProductKey`, and `Sales Data` is related to `Calendar Lookup` (one) via `OrderDate ↔ Date`.
- A `Calendar Lookup` table with at least a `Date` column. (`Calendar Lookup.csv` ships with just the `Date` column; other calendar attributes such as `Start of Week`, `MonthName`, etc. are added earlier in the course.)

---

## Part 1: Date and Time Functions

### Volatile Functions: TODAY and NOW

| Function | Returns | Argument |
|---|---|---|
| `TODAY()` | Current system date | None |
| `NOW()` | Current system date and time | None |

These are **volatile** — they update on every model refresh. Use them for "as of today" calculations such as customer age or days-since-order.

### Component Functions

Each of the following extracts one piece of a date or datetime value:

- `YEAR(Date)`, `MONTH(Date)`, `DAY(Date)` — date components.
- `HOUR(DateTime)`, `MINUTE(DateTime)`, `SECOND(DateTime)` — time components. The source column must include a time portion.

### WEEKDAY and WEEKNUM

`WEEKDAY(Date, [ReturnType])` returns the day of the week as a number. The optional `ReturnType` controls the numbering scheme:

| ReturnType | Numbering |
|---|---|
| `1` (default) | Sunday = 1 … Saturday = 7 |
| `2` | Monday = 1 … Sunday = 7 |
| `3` | Monday = 0 … Sunday = 6 |

`WEEKNUM(Date, [ReturnType])` returns the week number of the year, with similar return-type options for week-start day.

> **Match your calendar:** Always confirm your `WEEKDAY` numbering matches the convention used elsewhere in the model (for example, an existing `Start of Week` column). Mismatches produce subtle off-by-one bugs in weekend/weekday logic.

### EOMONTH

`EOMONTH(StartDate, Months)` returns the **last day of the month**, offset by the specified number of months from `StartDate`.

| `Months` value | Result |
|---|---|
| `0` | Last day of the current month |
| `-1` | Last day of the previous month |
| `1` | Last day of the next month |

### DATEDIFF

`DATEDIFF(Date1, Date2, Interval)` returns the difference between two dates in the specified `Interval` (`SECOND`, `MINUTE`, `HOUR`, `DAY`, `WEEK`, `MONTH`, `QUARTER`, `YEAR`).

> Many of these basic functions feel underwhelming on their own. Their real power emerges when nested inside larger calculations or measures.

---

## Exercise 1: Add a Day-of-Week Column to Calendar Lookup

**Goal:** Create a `Day of Week` calculated column on `Calendar Lookup` that returns `1`–`7`, with weeks starting on **Monday**, to align with the existing `Start of Week` convention used in the model.

### Steps

1. Switch to **Data** view and open the `Calendar Lookup` table.
2. Click **New column** on the **Table tools** ribbon.
3. Enter:

   ```DAX
   Day of Week =
   WEEKDAY ( 'Calendar Lookup'[Date], 2 )
   ```

4. Press **Enter**.

### Verify

- Find a row whose `Date` lands on a Monday — `Day of Week` should be `1`.
- A Sunday row should be `7`.
- Compare against the existing `Start of Week` column: rows in the same week should share the same `Start of Week` value, and the Monday of that week is where `Day of Week = 1`.

> **If you see Sunday = 1 instead:** You probably used the default return type (or `1`). The default is Sunday-first. Use return type `2` for Monday-first.

---

## Exercise 2: Add a Weekend / Weekday Flag

**Goal:** Add a `Weekend` calculated column on `Calendar Lookup` that returns `"Weekend"` for Saturday and Sunday, and `"Weekday"` otherwise.

### Step 1 — IF/OR Version

```DAX
Weekend =
IF (
    'Calendar Lookup'[Day of Week] = 6
        || 'Calendar Lookup'[Day of Week] = 7,
    "Weekend",
    "Weekday"
)
```

(With the Monday-first numbering from Exercise 1, Saturday = 6 and Sunday = 7.)

### Step 2 — Streamlined IN List Version

DAX supports an `IN` operator with a curly-brace list (a table constructor), which makes the same logic much cleaner:

```DAX
Weekend =
IF (
    'Calendar Lookup'[Day of Week] IN { 6, 7 },
    "Weekend",
    "Weekday"
)
```

`{ 6, 7 }` is a single-column table; `IN` checks whether the left-hand value is any of the values in the table. This pattern scales nicely whenever you need to check membership in a set.

### Verify

Filter the column for `Weekend = "Weekend"` and confirm the corresponding `Day of Week` is always 6 or 7. Filter for `Weekday` and confirm 1–5.

---

## Assignment 4: Extract Customer Birth Year

**Scenario (email from Dianne):** The team wants to look at sales patterns by customer age cohort. Add a column that captures only the year from each customer's birth date.

### Objective

Create a calculated column named **Birth Year** on `Customer Lookup` that extracts the year from `BirthDate`.

> **Raw data note:** `Customer Lookup.csv` contains `BirthDate` in `YYYY-MM-DD` format (sample: `1966-04-08`).

### Solution

```DAX
Birth Year = YEAR ( 'Customer Lookup'[BirthDate] )
```

### Verify

Spot-check several rows: a `BirthDate` of `1964-XX-XX` should yield `1964`. A `BirthDate` of `1963-XX-XX` should yield `1963`.

> **Why this matters:** Single-purpose extraction columns like `Birth Year`, `Order Year`, and `Order Quarter` are the building blocks for higher-level analyses — age cohorts, year-over-year comparisons, and time-based slicers.

---

## Part 2: The RELATED Function

### What RELATED Does

`RELATED(ColumnFromOneSideTable)` returns a value from the **one** side of a one-to-many relationship for the current row on the **many** side.

Think of it as a `VLOOKUP` that already knows the lookup key — because the relationship in your data model is the lookup key. You only need to specify the column you want pulled in.

> **Analogy — a phone book on your desk:** Each row of `Sales Data` knows a `ProductKey` — like having a contact's phone number. `RELATED('Product Lookup'[ProductPrice])` is like saying *"look up that number in the phone book and bring me back this person's address."* Power BI uses the active relationship as the phone book; you only have to name the column you want returned.

### Direction Matters

`RELATED` works **from the many side reaching across to the one side**. It will not work in the opposite direction — for that, you use `RELATEDTABLE` (out of scope for this handout).

> **Common mistake:** Trying to use `RELATED` on the lookup-table side. If you are sitting in `Product Lookup` (the *one* side) and try `RELATED('Sales Data'[…])`, DAX cannot return a single value because *one* product matches *many* sales rows. The function is designed only for many → one direction.

### When to Use RELATED

- In **calculated columns** that need to combine row-level data from two related tables (such as multiplying a unit price from `Product Lookup` by a quantity from `Sales Data`).
- Inside **iterator functions** like `SUMX`, `FILTER`, `MAXX`, where DAX has row context across the iteration.

### A Caution About Normalization

Adding a calculated column with `RELATED` duplicates lookup data into the fact table — which slightly violates the normalization principles you applied during data modeling. Use `RELATED` strategically, not as a default:

- For ad-hoc convenience, `RELATED` columns are fine.
- For production analytics, prefer to keep lookup data in lookup tables and use `RELATED` **inside an iterator measure** (for example, `SUMX('Sales Data', 'Sales Data'[OrderQuantity] * RELATED('Product Lookup'[ProductPrice]))`) instead of materializing a new column.

---

## Exercise 3: Bring Retail Price into Sales Data with RELATED

**Goal:** Add a `Retail Price` calculated column to `Sales Data` that pulls `ProductPrice` from `Product Lookup`.

> **Raw data note:** `Sales Data` (many) relates to `Product Lookup` (one) via `ProductKey`. `Product Lookup.csv` contains the column `ProductPrice` (sample value `34.99` for `ProductKey 214`).

### Steps

1. Confirm in **Model** view that an active relationship exists between `Sales Data[ProductKey]` and `Product Lookup[ProductKey]`.
2. Switch to **Data** view and select the `Sales Data` table.
3. Click **New column**.
4. Enter:

   ```DAX
   Retail Price = RELATED ( 'Product Lookup'[ProductPrice] )
   ```

5. Press **Enter**.

### Verify

1. Click the `Sales Data[ProductKey]` column header and sort ascending.
2. Find rows where `ProductKey = 214`. Their `Retail Price` should be `34.99`.
3. Open `Product Lookup` and confirm `ProductKey = 214` indeed has `ProductPrice = 34.99`.

> **What if RELATED is not available?** If `Product Lookup` does not appear in IntelliSense after you type `RELATED(`, the relationship between the two tables is missing or inactive. Return to **Model** view and verify the relationship before continuing.

---

## Exercise 4: Calculate Revenue with the New Column

**Goal:** Use the `Retail Price` column to derive a row-level `Revenue` column on `Sales Data`.

### Steps

1. In `Sales Data`, click **New column**.
2. Enter:

   ```DAX
   Revenue =
   'Sales Data'[Retail Price] * 'Sales Data'[OrderQuantity]
   ```

3. Press **Enter**.
4. Format the column as **Currency** with **2 decimal places** from the **Column tools** ribbon.

### Verify

Sort `OrderQuantity` descending. For a row with `OrderQuantity = 3` and `Retail Price = 21.98`, `Revenue` should be `65.94`.

> **Pro tip — measure version:** A more efficient, more "DAX-idiomatic" approach is to skip the calculated column entirely and write a measure:
>
> ```DAX
> Total Revenue =
> SUMX (
>     'Sales Data',
>     'Sales Data'[OrderQuantity] * RELATED ( 'Product Lookup'[ProductPrice] )
> )
> ```
>
> `SUMX` iterates over `Sales Data` row by row, and `RELATED` reaches into `Product Lookup` for each row's `ProductPrice`. No new columns are stored on disk — the calculation happens at query time.

---

## Checkpoint

- [ ] `Day of Week` column shows 1 for Mondays through 7 for Sundays.
- [ ] `Weekend` column flags Saturdays and Sundays correctly.
- [ ] `Birth Year` column matches the year portion of `BirthDate`.
- [ ] `Retail Price` and `Revenue` columns exist on `Sales Data` and produce sensible values.
- [ ] You can articulate the direction of `RELATED` (many → one) and at least one reason to prefer measure-based revenue over a calculated column.

---

## Key Takeaways

- Date component functions (`YEAR`, `MONTH`, `DAY`, `WEEKDAY`, `WEEKNUM`) are most useful as building blocks inside calendar tables and larger calculations.
- Confirm the **return type** for `WEEKDAY`/`WEEKNUM` matches your business calendar convention.
- The `IN { … }` operator is a clean way to test set membership and avoids long chains of `OR` conditions.
- `RELATED` works from the many side to the one side. It needs an active relationship to function.
- `RELATED` inside an iterator (`SUMX`, `FILTER`, `MAXX`) is the preferred pattern; calculated columns with `RELATED` should be used sparingly.
