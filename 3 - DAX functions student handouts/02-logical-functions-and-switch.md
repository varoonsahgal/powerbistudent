# Handout 02: DAX Logical Functions and SWITCH

## Purpose

This handout covers the logical functions in DAX — `IF`, `IFERROR`, `AND`, `OR`, and `SWITCH` — along with the `&&` and `||` operators. It also explores the powerful `SWITCH(TRUE())` pattern and shows when each construct is the right tool for the job.

---

## Prerequisites

- The AdventureWorks data model is loaded with all relationships in place.
- You have completed the counting functions handout and understand the difference between calculated columns and measures.
- You know how to create a new calculated column from the **Data** view (or the **Modeling** ribbon).

---

## Key Concepts

### IF — The Foundation

`IF(logical_test, value_if_true, [value_if_false])`

- **logical_test** — any Boolean expression (text, numeric, or comparison) that evaluates to `TRUE` or `FALSE`. Examples: `Country = "USA"`, `Weekday = 1`, `RetailPrice > 10`.
- **value_if_true** — what to return when the test passes.
- **value_if_false** — optional; what to return when the test fails. If omitted, false rows return `BLANK`.

### IFERROR — Catching Calculation Errors

`IFERROR(expression, value_if_error)`

Evaluates `expression`. If it throws an error (e.g., divide by zero, type mismatch), it returns `value_if_error` instead.

> **Caution:** Errors are sometimes telling you something important. Wrapping every formula in `IFERROR` can hide real problems. Use it where errors are *expected* (such as a division that may legitimately have a zero denominator), not as a blanket safety net.

### AND, OR, &&, ||

| Function / Operator | Meaning | Number of conditions |
|---|---|---|
| `AND(a, b)` | Both `a` and `b` are true | Exactly 2 |
| `OR(a, b)` | At least one of `a`, `b` is true | Exactly 2 |
| `a && b && c` | Logical AND | Unlimited |
| `a \|\| b \|\| c` | Logical OR | Unlimited |

> **Tip:** Prefer `&&` and `||`. They support more than two conditions and read more naturally inside larger expressions.

### SWITCH — Replacing Nested IFs

`SWITCH(expression, value1, result1, value2, result2, ..., [else])`

`SWITCH` evaluates an expression once, then returns the result that matches the first equal value. It is purpose-built to replace long chains of nested `IF` statements when comparing one expression against many fixed values.

> **Critical limitation:** Plain `SWITCH` only supports **equality** matches. You cannot write `SWITCH(price, > 500, "High", > 100, "Mid", "Low")`. To use comparison operators, use the `SWITCH(TRUE(), …)` pattern (covered below).

### The SWITCH(TRUE()) Pattern

To use comparison operators (`>`, `<`, `>=`, etc.) with `SWITCH`, pass `TRUE()` as the expression and let each "value" become a Boolean test:

```DAX
SWITCH(
    TRUE(),
    [Product Price] > 500, "High",
    [Product Price] > 100, "Mid-range",
    "Low"
)
```

`SWITCH` evaluates each test in order and returns the result for the first one that equals `TRUE`. The trailing argument acts as the else clause.

> **Analogy — a checklist on a clipboard:** `SWITCH(TRUE(), …)` is like working down a checklist of yes/no questions in order. The first question that gets a "yes" wins, and the rest of the list is ignored. Because the checks happen top-to-bottom, **order matters** — a broader rule listed first will swallow narrower rules below it.

> **Common mistake — wrong order of conditions:** Listing `> 100` before `> 500` would label every product over $100 as `Mid-range` and the `High` bucket would never fire. Always list the **most restrictive** condition first when ranges overlap.

---

## Exercise 1: Build a Parent Calculated Column with IF

**Goal:** Add a calculated column to `Customer Lookup` that flags whether each customer is a parent (i.e., `TotalChildren` is greater than zero).

> **Raw data note:** `Customer Lookup.csv` contains a `TotalChildren` column with integer values starting at `0`. There is no existing `Parent` column — this exercise adds it.

### Steps

1. Switch to **Data** view (the table icon on the left navigation bar).
2. In the **Data** pane on the right, click the `Customer Lookup` table to open it.
3. On the **Table tools** ribbon, click **New column**.
4. In the formula bar, enter:

   ```DAX
   Parent =
   IF (
       'Customer Lookup'[TotalChildren] > 0,
       "Yes",
       "No"
   )
   ```

5. Press **Enter** to commit.

### Verify

- Filter the column header for `Parent = "Yes"`. Confirm those rows show `TotalChildren` values of 1 or more.
- Filter for `Parent = "No"`. Confirm those rows show `TotalChildren = 0`.

> **Why this matters:** This is row context at work. For each row, DAX looks up `TotalChildren` *on that same row* to evaluate the test. Calculated columns always have row context.

---

## Exercise 2: Replace Nested IFs with SWITCH for Month Number

**Goal:** Practice `SWITCH` by creating a `Month Number DAX` column on the `Calendar Lookup` table that maps month names (`January`, `February`, …) to month numbers (1, 2, …).

> **Raw data note:** The raw `Calendar Lookup.csv` contains only a `Date` column. The `MonthName` column referenced here is created in the prior Power Query / data-modeling section of the course. If your file does not yet have a `MonthName` (or `Month Name`) column, derive it first using `FORMAT([Date], "MMMM")` in a calculated column, or add it in Power Query.

### Step 1 — See Why Nested IFs Are Painful

In `Calendar Lookup`, click **New column** and try the nested `IF` approach for the first three months:

```DAX
Month Number IF =
IF ( 'Calendar Lookup'[MonthName] = "January", 1,
    IF ( 'Calendar Lookup'[MonthName] = "February", 2,
        IF ( 'Calendar Lookup'[MonthName] = "March", 3, BLANK() )
    )
)
```

Notice how parentheses pile up and readability degrades quickly. Imagine doing this for all 12 months.

### Step 2 — Rewrite with SWITCH

Add another new calculated column:

```DAX
Month Number DAX =
SWITCH (
    'Calendar Lookup'[MonthName],
    "January",   1,
    "February",  2,
    "March",     3,
    "April",     4,
    "May",       5,
    "June",      6,
    "July",      7,
    "August",    8,
    "September", 9,
    "October",   10,
    "November",  11,
    "December",  12,
    BLANK()
)
```

> **Productivity tip:** Hold **Alt + Shift + Down Arrow** in the formula bar to duplicate the current line. This is much faster than retyping each value/result pair.

### Watch Out for Type Mismatches

If you mix data types in the result slots (some quoted strings, some bare numbers), the column receives the **Variant** data type — DAX is essentially saying "I cannot decide." Keep all results in the same type. If you want them as text, quote every result. If you want them as integers, leave them all unquoted.

### Verify

- Sort by `MonthName` and confirm `January → 1`, `February → 2`, … `December → 12`.
- Delete the `Month Number IF` column once the comparison is understood.

---

## Exercise 3: Use SWITCH(TRUE()) for a Price Point Column

**Goal:** Bucket products into `High`, `Mid-range`, and `Low` price points using `SWITCH(TRUE())`.

> **Raw data note:** `Product Lookup.csv` contains a `ProductPrice` column. Sample values range from roughly `2` to `3578`.

### Steps

1. In **Data** view, open the `Product Lookup` table.
2. Click **New column** and enter:

   ```DAX
   Price Point =
   SWITCH (
       TRUE (),
       'Product Lookup'[ProductPrice] > 500, "High",
       'Product Lookup'[ProductPrice] > 100, "Mid-range",
       "Low"
   )
   ```

3. Press **Enter**.

### How It Works

- `SWITCH` evaluates each Boolean expression in order and returns the result for the first one that equals `TRUE()`.
- The trailing `"Low"` is the else clause — returned only when no earlier condition matches.
- Order matters. If the `> 100` test were listed before `> 500`, every product over `100` would be labeled `Mid-range` and `High` would never fire.

### Verify

Sort `Product Lookup` by `ProductPrice` descending. The most expensive bikes should be `High`, mid-priced items `Mid-range`, and cheap accessories `Low`.

---

## Assignment 2: Customer Segmentation Columns

**Scenario (email from Dianne):** The data science team needs three new calculated columns in `Customer Lookup` for an upcoming customer segmentation analysis.

### Objectives

1. **Customer Priority** — If the customer **is a parent** (`Parent = "Yes"`) **and** `AnnualIncome > 100,000`, return `"Priority"`. Otherwise return `"Standard"`.
2. **Income Level** — Bucket customers by `AnnualIncome`:
   - `>= 150,000` → `"Very High"`
   - `>= 100,000` → `"High"`
   - `>= 50,000` → `"Average"`
   - Otherwise → `"Low"`
3. **(Bonus) Education Category** — Use `SWITCH` on `EducationLevel` to group:
   - `"High School"` or `"Partial High School"` → `"High School"`
   - `"Bachelors"` or `"Partial College"` → `"Undergrad"`
   - `"Graduate Degree"` → `"Graduate"`

### Reference Solutions

```DAX
Customer Priority =
IF (
    'Customer Lookup'[Parent] = "Yes"
        && 'Customer Lookup'[AnnualIncome] > 100000,
    "Priority",
    "Standard"
)
```

```DAX
Income Level =
IF (
    'Customer Lookup'[AnnualIncome] >= 150000, "Very High",
    IF (
        'Customer Lookup'[AnnualIncome] >= 100000, "High",
        IF (
            'Customer Lookup'[AnnualIncome] >= 50000, "Average",
            "Low"
        )
    )
)
```

> **Alternative — `SWITCH(TRUE())` for Income Level:** This expresses the same logic in a flatter, more readable form.
>
> ```DAX
> Income Level =
> SWITCH (
>     TRUE (),
>     'Customer Lookup'[AnnualIncome] >= 150000, "Very High",
>     'Customer Lookup'[AnnualIncome] >= 100000, "High",
>     'Customer Lookup'[AnnualIncome] >= 50000,  "Average",
>     "Low"
> )
> ```

```DAX
Education Category =
SWITCH (
    'Customer Lookup'[EducationLevel],
    "High School",         "High School",
    "Partial High School", "High School",
    "Bachelors",           "Undergrad",
    "Partial College",     "Undergrad",
    "Graduate Degree",     "Graduate"
)
```

> **Spelling tip:** Education-level values must match the source data **exactly** — including case and spaces. If a row returns blank, double-check the spelling in `Customer Lookup[EducationLevel]` against your `SWITCH` list. The source values in `Customer Lookup.csv` include `Bachelors`, `Partial College`, `High School`, `Partial High School`, and `Graduate Degree`.

### Verify

- Filter `Customer Priority = "Priority"` and confirm every result has `Parent = "Yes"` and `AnnualIncome > 100000`.
- Filter `Income Level = "Low"` and confirm `AnnualIncome < 50000`.
- Filter `Education Category = "High School"` and confirm the source `EducationLevel` is either `High School` or `Partial High School`.

---

## Checkpoint

- [ ] You can recall the syntax of `IF`, `IFERROR`, `AND`, `OR`, and `SWITCH` without looking.
- [ ] You can explain why `&&` and `||` are usually preferred over `AND()` and `OR()`.
- [ ] You can articulate the one-sentence rule for when to use `SWITCH` vs. `SWITCH(TRUE())`.
- [ ] All three customer segmentation columns appear correctly in `Customer Lookup`.

> **Quick check:** Suppose you need to label rainfall as `Heavy` (> 50 mm), `Moderate` (> 10 mm), or `Light` (otherwise). Should you use plain `SWITCH` or `SWITCH(TRUE())`? Why? *(Hint: are your conditions equality matches or comparisons?)*

---

## Key Takeaways

- `IF` is the workhorse for two-way decisions. The moment you reach a third or fourth nested `IF`, switch to `SWITCH` or `SWITCH(TRUE())` for readability.
- Use **`SWITCH`** when comparing one expression to a list of **fixed values** (month names → month numbers).
- Use **`SWITCH(TRUE(), …)`** when each branch needs its own **Boolean test** (price > 500, age >= 18, country IN list).
- Prefer `&&` and `||` for multi-condition logic; reserve `AND()` and `OR()` for cases where you need a true two-argument function.
- Watch your data types — if `SWITCH` returns a mix of text and numbers, the column becomes the **Variant** type and may misbehave in measures and visuals.
- Use `IFERROR` deliberately for *expected* errors (such as a divide-by-zero). Wrapping every formula in `IFERROR` will hide bugs you actually need to see.
