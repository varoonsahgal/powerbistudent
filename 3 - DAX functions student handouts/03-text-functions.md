# Handout 03: DAX Text Functions

## Purpose

This handout covers the DAX text-manipulation functions: `LEN`, `CONCATENATE` (and the `&` operator), `UPPER`, `LOWER`, `LEFT`, `MID`, `RIGHT`, `SUBSTITUTE`, and `SEARCH`. You will use these to build composite columns (full customer names, short month names, and SKU categories) on the AdventureWorks model.

---

## Prerequisites

- The AdventureWorks data model is loaded in Power BI Desktop with relationships established.
- You can create a new calculated column from **Data view > Table tools > New column**.
- You have completed the previous logical-functions handout.

---

## Key Concepts

### Text Functions at a Glance

| Function | Purpose | Syntax |
|---|---|---|
| `LEN` | Number of characters in a string | `LEN(Text)` |
| `CONCATENATE` | Join exactly **two** strings | `CONCATENATE(Text1, Text2)` |
| `&` (operator) | Join **any number** of strings | `Text1 & Text2 & Text3 & ...` |
| `UPPER` | Convert to uppercase | `UPPER(Text)` |
| `LOWER` | Convert to lowercase | `LOWER(Text)` |
| `LEFT` | First N characters | `LEFT(Text, [NumChars])` |
| `RIGHT` | Last N characters | `RIGHT(Text, [NumChars])` |
| `MID` | N characters starting at position P | `MID(Text, StartPosition, NumChars)` |
| `SUBSTITUTE` | Replace one substring with another | `SUBSTITUTE(Text, OldText, NewText, [InstanceNum])` |
| `SEARCH` | Position of a substring (case-insensitive) | `SEARCH(FindText, WithinText, [StartPosition], [NotFoundValue])` |

### CONCATENATE vs. the Ampersand

`CONCATENATE` accepts only two arguments. To join three or more strings with the function, you would have to nest multiple `CONCATENATE` calls — verbose and hard to read. The `&` operator is the practical choice and supports unlimited inputs.

> **Best practice:** Use `&` for string concatenation. Reserve `CONCATENATE` for cases where a function form is required (rare).

### LEFT, MID, RIGHT — Optional Argument Defaults

For `LEFT` and `RIGHT`, the `NumChars` argument is optional. If omitted, both default to **1**.

### SEARCH Returns a Position

`SEARCH` finds a substring inside another string and returns the **1-based starting position**. For example, `SEARCH("house", "doghouse")` returns `4`. Use the optional `NotFoundValue` argument to control what happens when the substring is missing — otherwise an error is raised.

> **Analogy — the "page number" of a word:** `SEARCH` is like asking "At what page number does this word first appear?" It does not give you the word; it gives you the **location**. You then hand that location to `LEFT`, `MID`, or `RIGHT` to actually grab the text you want.

> **Tip:** `SEARCH` is most useful when **nested inside** other text functions like `LEFT` or `MID`, so the position becomes input to a substring extraction.

---

## Exercise 1: Build a Customer Full Name Column with the Ampersand

**Goal:** Create a calculated column on `Customer Lookup` that concatenates `Prefix`, `FirstName`, and `LastName` into a single, readable string such as `MR. JON YANG`.

> **Raw data note:** `Customer Lookup.csv` contains the columns `Prefix`, `FirstName`, and `LastName`. Sample row: `MR.,JON,YANG`.

### Steps

1. Switch to **Data** view and select the `Customer Lookup` table.
2. On the **Table tools** ribbon, click **New column**.
3. In the formula bar, enter:

   ```DAX
   Customer Full Name CC =
       'Customer Lookup'[Prefix] & " "
           & 'Customer Lookup'[FirstName] & " "
           & 'Customer Lookup'[LastName]
   ```

4. Press **Enter** to commit.

### Why the Spaces?

Without `& " " &` between fields, the result would read `MR.JONYANG`. Each `" "` is a literal one-character string concatenated between the source columns.

### Verify

Look down the new column. Values should look like `MR. JON YANG`, `MS. EUGENE HUANG`, etc. — with single spaces between every component.

> **Why "CC" in the column name?** Adding a `CC` (calculated column) suffix is a useful naming convention when the same logical column may also exist as a Power Query–generated column. It avoids confusion about which version is being used.

---

## Exercise 2: Create a Short Month Name with LEFT

**Goal:** Add a `Month Short` column on `Calendar Lookup` that returns the first three characters of the month name (e.g., `Apr`, `Aug`, `Dec`).

> **Raw data note:** The raw `Calendar Lookup.csv` contains only a `Date` column. The `MonthName` column referenced here is created earlier in the course (either in Power Query or via `FORMAT([Date], "MMMM")`). If your file does not yet have one, derive it before continuing.

### Steps

1. With `Calendar Lookup` selected in **Data** view, click **New column**.
2. Enter:

   ```DAX
   Month Short = LEFT ( 'Calendar Lookup'[MonthName], 3 )
   ```

3. Press **Enter**.

### Verify

Sort by `Month Short` and confirm values such as `Jan`, `Feb`, `Mar`, … `Dec`. Each value should be exactly three characters long.

---

## Assignment 3: Capitalize Months and Extract SKU Categories

**Scenario (email from Dianne):**

- Ethan wants the month abbreviations to be **all caps** for readability.
- The product team needs an `SKU Category` column defined as **everything before the first hyphen** in `ProductSKU`.

### Objectives

1. Update the existing `Month Short` column on `Calendar Lookup` to also capitalize the result.
2. Create a new column `SKU Category` on `Product Lookup` containing all characters before the first hyphen of `ProductSKU`.

### Objective 1 — Wrap LEFT in UPPER

Edit the `Month Short` formula and wrap the existing `LEFT` call in `UPPER`:

```DAX
Month Short =
UPPER (
    LEFT ( 'Calendar Lookup'[MonthName], 3 )
)
```

### Verify

Values should now appear as `JAN`, `FEB`, `MAR`, … `DEC`.

> **Indentation tip:** Pressing **Tab** indents the selected lines and **Shift + Enter** adds a newline inside the formula bar. Indenting nested calls (the `LEFT` argument under `UPPER`) makes it visually obvious which arguments belong to which function.

### Objective 2 — Extract the SKU Category with LEFT + SEARCH

> **Raw data note:** `Product Lookup.csv` contains the `ProductSKU` column with sample values such as `HL-U509-R`, `BK-M82B-44`, `SO-B909-M`. The "category" prefix appears before the first hyphen.

The number of characters before the first hyphen varies, so a fixed number cannot be used. Instead, find the first hyphen with `SEARCH` and pass that position (minus 1) to `LEFT`:

```DAX
SKU Category =
LEFT (
    'Product Lookup'[ProductSKU],
    SEARCH ( "-", 'Product Lookup'[ProductSKU] ) - 1
)
```

### Why "minus 1"?

`SEARCH("-", "HL-U509-R")` returns `3` — the position **of** the hyphen. To get everything **before** the hyphen, subtract 1 to take the first 2 characters: `HL`.

### Build It Up Piece by Piece

If the formula above feels opaque, replicate the breakdown shown in the demo:

1. **`SEARCH ( "-", 'Product Lookup'[ProductSKU] )`** alone returns the position of the first hyphen (a small integer like `3` or `4`).
2. **`LEFT ( 'Product Lookup'[ProductSKU], SEARCH ( "-", 'Product Lookup'[ProductSKU] ) )`** without the `- 1` returns the prefix **including** the hyphen (e.g., `HL-`).
3. Adding `- 1` strips the hyphen, leaving just the category code (`HL`).

### Verify

- For `HL-U509-R` the column should show `HL`.
- For `BK-M82B-44` it should show `BK`.
- For `SO-B909-M` it should show `SO`.

> **Troubleshooting:** If a row throws an error, the SKU may not contain a hyphen. Wrap the formula in `IFERROR` or use the optional `NotFoundValue` argument of `SEARCH`:
>
> ```DAX
> SKU Category =
> IFERROR (
>     LEFT (
>         'Product Lookup'[ProductSKU],
>         SEARCH ( "-", 'Product Lookup'[ProductSKU] ) - 1
>     ),
>     'Product Lookup'[ProductSKU]
> )
> ```

---

## Checkpoint

- [ ] `Customer Full Name CC` reads cleanly with single spaces.
- [ ] `Month Short` displays three-letter, all-uppercase month abbreviations.
- [ ] `SKU Category` shows the prefix before the first hyphen for every row of `Product Lookup`.
- [ ] You can describe the difference between `CONCATENATE` and `&` and the difference between `LEFT` and `MID`.

> **Quick check:** If `SEARCH("@", "jane@acme.com")` returns `5`, what does `LEFT("jane@acme.com", SEARCH("@", "jane@acme.com") - 1)` return? Why the `- 1`?

---

## Key Takeaways

- The `&` operator is the practical, flexible way to concatenate text in DAX. Use `CONCATENATE` only when you specifically need a function.
- `LEFT`, `RIGHT`, and `MID` are most powerful when combined with `SEARCH` to extract dynamic substrings.
- `SEARCH` returns a *position* — almost always you will use it nested inside another text function rather than alone.
- Wrap nested calls (`UPPER ( LEFT ( … ) )`) and indent them. Readability now saves debugging time later.
- Always verify the output against the source column for several rows. Off-by-one errors with `LEFT`/`SEARCH` are easy to introduce.
