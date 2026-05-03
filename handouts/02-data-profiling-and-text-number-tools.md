# Handout 02: Data Profiling, QA, and Text/Number Tools in Power Query

## Purpose

This handout covers the data profiling tools built into Power Query, how to identify and fix data quality issues, and how to use text-specific and number-specific transformation tools. It also includes a hands-on assignment to extract domain names from an email column.

---

## Prerequisites

- Power BI Desktop is open with the AdventureWorks Customer Lookup CSV loaded.
- You have renamed the query to `Customer Lookup`.

> **Starting state:** The `AdventureWorks Customer Lookup.csv` file contains the following columns:  
> `CustomerKey`, `Prefix`, `FirstName`, `LastName`, `BirthDate`, `MaritalStatus`, `Gender`, `EmailAddress`, `AnnualIncome`, `TotalChildren`, `EducationLevel`, `Occupation`, `HomeOwner`

---

## Part 1: Data Profiling Tools

Power Query provides three visual profiling tools that help you understand the composition of your data before loading it into the model. All three are found on the **View** ribbon inside Power Query Editor.

---

### Column Quality

**Column Quality** shows the percentage of values in a column that are **Valid**, **Error**, or **Empty**.

- Hover over the quality bar for a column to see a contextual popup with counts and percentages.
- Click the ellipsis (`...`) on the quality bar for quick-action options: **Remove Errors**, **Remove Empty**, **Remove Duplicates**, and more.
- These contextual menus bring common cleanup actions directly to you without hunting through the ribbon.

> **Important:** By default, column profiling analyzes only the **first 1,000 rows**. If your table has more than 1,000 rows, the quality percentages can be misleading.
>
> The `Customer Lookup` table has over **18,000 rows**. To see accurate results, click the status bar text at the bottom of the editor that reads **"Column profiling based on top 1000 rows"** and change it to **"Column profiling based on entire data set"**.

---

### Column Distribution

**Column Distribution** shows a mini bar chart with the distribution of values in each column. It also displays the count of **distinct** values and **unique** values.

- **Distinct** = the number of different values that appear at least once.
- **Unique** = the number of values that appear exactly once (no repeats).

> **Example:** In the `FirstName` column, 666 distinct first names exist, and 89 of them appear only once. This means many customers share common first names. A "Remove Duplicates" suggestion from Power Query here would be wrong — duplicates in a name column are expected.

Power Query may suggest a "Remove duplicates" transformation automatically. Always evaluate whether the suggestion makes sense for your data before accepting it.

---

### Column Profile

**Column Profile** provides the most detailed view. It includes:

- **Column statistics** (count, distinct, unique, min, max, empty, errors, standard deviation, average)
- A **value distribution chart** on the right

> **Tip:** For a text column like `FirstName`, the min/max values are just the alphabetically first and last names — not very meaningful. For a numeric column like `AnnualIncome`, the min, max, average, and standard deviation are genuinely useful for understanding the data range.

---

## Part 2: Finding and Removing Errors

### Steps — Profiling the Entire Dataset

1. In Power Query Editor, open the `Customer Lookup` table.
2. On the **View** ribbon, check **Column Quality**.
3. At the bottom status bar, click **"Column profiling based on top 1000 rows"** and switch to **"Column profiling based on entire data set"**.
4. Look at the `CustomerKey` column. You should see several **Error** values appear.

### Understanding the Errors

To investigate what is causing the errors:

1. Right-click the `CustomerKey` column header.
2. Select **Keep Errors**. Power Query adds an applied step that filters the table to show only the error rows.
3. Click into one of the error cells to read the error message.
   - You will see a message like: *"We couldn't convert the value '30---' to number."*
   - Another row may contain a hyperlink or reference string that also cannot be converted to a whole number.
4. These are genuine data quality issues in the source CSV.
5. Delete the **Keep Errors** applied step to return to the full table.

### Removing Errors and Empty Rows

1. With the `CustomerKey` column selected, hover over the **Column Quality** bar.
2. Click the **Remove Errors** suggestion. A new **Removed Errors** step is added.
3. Look at the `CustomerKey` column again — some **Empty** values may remain.
4. Click **Remove Empty** from the contextual menu. A **Filtered Rows** step is added.
5. Scroll across the remaining columns. All columns should now show **100% Valid**.

> **What about the Prefix column?** You will still see ~130 empty values in the `Prefix` column after cleaning. This is expected — not every customer record has a prefix (Mr./Mrs.), so the empty values are legitimate and should be kept.

---

### Checkpoint ✅

After completing Part 2, verify:

- The `CustomerKey` column shows no errors or empty values.
- Empty values remain only in the `Prefix` column.
- The applied steps pane shows `Removed Errors` and `Filtered Rows` steps.

---

## Part 3: Text-Specific Tools

Text tools are found on the **Transform** ribbon (modifies the selected column in place) and the **Add Column** ribbon (creates a new column).

> **Critical distinction:**
> - **Transform tab** → modifies the existing column.
> - **Add Column tab** → creates a brand new column, leaving the original intact.
>
> This distinction applies to every tool that appears in both places. It is easy to accidentally use the wrong tab. If you do, simply delete the last applied step and try again.

---

### Available Text Tools

| Tool | What it does |
|---|---|
| **Split Column** | Splits a column on a delimiter or a number of characters |
| **Format** | Applies UPPERCASE, lowercase, or Capitalize Each Word (proper case) |
| **Trim** | Removes leading and trailing spaces |
| **Clean** | Removes leading/trailing spaces and non-printable characters |
| **Extract** | Returns a subset of characters — first N chars, last N chars, before/after/between delimiters |
| **Merge Columns** | Combines multiple columns into one with a separator |

> **Why Trim and Clean matter:** A trailing space is invisible to the human eye but Power BI treats `"Adventure Works"` and `"Adventure Works "` (with a trailing space) as completely different values. This causes broken filters, incorrect grouping, and mysterious duplicates. Run Trim or Clean proactively on any column you expect to use as a filter or for matching.

---

## Exercise 1: Apply Proper Case to Text Columns

**Goal:** Change `Prefix`, `FirstName`, and `LastName` columns from ALL CAPS to Title Case.

1. In the `Customer Lookup` table, select the `Prefix` column.
2. On the **Transform** ribbon, click **Format** > **Capitalize Each Word**.
3. Verify the column now shows `Mr.` instead of `MR.`
4. Hold **Shift** and click `LastName` to select both `FirstName` and `LastName` at the same time.
5. On the **Transform** ribbon, click **Format** > **Capitalize Each Word**.
6. Power Query combines the transformation into a single applied step (check the M code in the formula bar to confirm).

**Expected result:** `Prefix`, `FirstName`, and `LastName` are now in proper case.

---

## Exercise 2: Create a Full Name Column

**Goal:** Combine `Prefix`, `FirstName`, and `LastName` into a single `Full Name` column.

1. Click the `Prefix` column header.
2. Hold **Shift** and click `LastName` to select all three columns in order.

   > **Order matters.** Power Query will combine the columns in the order you selected them. Selecting `LastName` first would produce names like "Yang Jon Mr."

3. On the **Add Column** ribbon, click **Merge Columns**.
4. Set **Separator** to **Space**.
5. In the **New column name** field, type `Full Name`.
6. Click **OK**.
7. In the Applied Steps pane, right-click the new step and rename it to `Create Full Name Column`.
8. Scroll to the far right of the table to confirm the new `Full Name` column contains values like `Mr. Jon Yang`.

---

## Exercise 3: Extract Email Domains (Assignment)

**Scenario:** Your manager needs a new column in the Customer table that shows the email domain for each customer, so the team can understand where customers are coming from.

**Requirements:**

1. Duplicate the `EmailAddress` column and name it `Domain Name`.
2. From the `Domain Name` column, remove everything except the domain name itself (for example, `adventure-works.com` becomes `Adventure Works`).
3. Capitalize the domain name using proper case.
4. Save and apply.

### Step-by-Step Solution

**Step 1 — Duplicate the email column**

1. Right-click the `EmailAddress` column header.
2. Select **Duplicate Column**. A new column called `EmailAddress - Copy` is added.
3. Double-click the new column header and rename it to `Domain Name`.

**Step 2 — Extract the domain**

1. With `Domain Name` selected, go to the **Transform** ribbon.
2. Click **Extract** > **Text After Delimiter**.
3. In the **Delimiter** field, enter `@`.
4. Click **OK**. The column now contains values like `adventure-works.com`.

**Step 3 — Remove the `.com` suffix**

1. With `Domain Name` still selected, click **Extract** > **Text Before Delimiter**.
2. In the **Delimiter** field, enter `.` (a single period).
3. Click **OK**. The column now contains `adventure-works`.

**Step 4 — Replace the hyphen**

1. With `Domain Name` selected, on the **Transform** ribbon click **Replace Values**.
2. In **Value To Find**, enter `-` (a hyphen).
3. In **Replace With**, enter a single space (` `).
4. Click **OK**. The column now contains `adventure works`.

**Step 5 — Apply proper case**

1. With `Domain Name` selected, click **Format** > **Capitalize Each Word**.
2. The column now contains `Adventure Works`.

**Step 6 — Save and apply**

1. On the **Home** ribbon, click **Close & Apply**.

**Expected result:** A `Domain Name` column containing human-readable company names like `Adventure Works`.

> **Raw data note:** All email addresses in `AdventureWorks Customer Lookup.csv` use the `@adventure-works.com` domain, so the `Domain Name` column will show `Adventure Works` for every row after these steps. In a real-world dataset you would see a variety of domain names.

---

## Part 4: Number-Specific Tools

When you select a numeric column, the Transform ribbon replaces text tools with number-specific tools.

### Statistics Functions (Aggregators)

These return a single value for the entire column: **Sum, Min, Max, Median, Average, Standard Deviation, Count, Count Distinct**.

> **Important:** Because statistics functions collapse the entire table to one row, they are not useful for preparing data for the model. They are useful for **exploratory analysis** — answering quick questions before loading data.
>
> After using a statistics function, always **delete the applied step** to restore the full table.

**Example — Count distinct products:**

1. Select the `ProductName` column in the `Product Lookup` table.
2. On the **Transform** ribbon, click **Statistics** > **Count Distinct Values**.
3. The result is **293** — there are 293 unique products in the catalog.
4. Delete the applied step to return to the full table.

**Example — Average product price:**

1. Select the `ProductPrice` column.
2. Click **Statistics** > **Average**.
3. The average product price is approximately **$714.43**. This seems high because AdventureWorks sells bicycles and high-end accessories.
4. Delete the applied step.

---

### Standard, Scientific, and Trigonometry Operations

Unlike statistics functions, these tools operate **row by row**. Each value in the column is modified individually.

Common operations: **Add, Subtract, Multiply, Divide**, and more advanced options such as **Round, Absolute Value, Power, Log, Sine, Cosine, Tangent**.

---

### Info Functions

**Info** functions add a binary (true/false) flag to each row. Options include:

- **Is Even / Is Odd**
- **Is Positive / Is Negative**

---

## Exercise 4: Add a Discounted Price Column

**Goal:** Add a `Discount Price` column to the `Product Lookup` table that equals 90% of the `ProductPrice` value.

1. Select the `ProductPrice` column in the `Product Lookup` table.
2. On the **Add Column** ribbon (not Transform), click **Standard** > **Multiply**.
3. In the value field, enter `0.9`.
4. Click **OK**.
5. A new column is added at the far right. Double-click its header and rename it to `Discount Price`.
6. Notice that the new column inherits the **Fixed Decimal Number** (currency) data type from `ProductPrice`.
7. Click **Close & Apply**.

**Expected result:** A `Discount Price` column in the `Product Lookup` table showing 10%-off prices. For a product priced at `$34.99`, the discount price should be approximately `$31.49`.

> **Raw data reference:** `ProductPrice` column is in `AdventureWorks Product Lookup.csv`. Sample product: `Sport-100 Helmet, Red` — ProductKey `214`, ProductPrice `34.99`, so Discount Price ≈ `31.49`.

---

## Key Takeaways

- **Column Quality, Distribution, and Profile** are your first line of defense for understanding data before loading it into the model.
- Always **change column profiling to the entire dataset** when your table has more than 1,000 rows.
- **Keep Errors** is a powerful diagnostic tool — use it to isolate and investigate bad rows before cleaning them.
- The **Transform tab** modifies the selected column. The **Add Column tab** creates a new column. This is one of the most common sources of confusion in Power Query.
- The toolbar is **dynamic** — it changes based on what column type is selected.
- Use **Trim** and **Clean** proactively on any text column that will be used as a filter or for matching.
- **Statistics functions** are for exploration only — always delete the applied step afterward.
- When adding a calculated numeric column from **Add Column**, the new column inherits the data type of the source column.
