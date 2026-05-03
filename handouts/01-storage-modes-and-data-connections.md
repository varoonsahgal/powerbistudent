# Handout 01: Storage Modes and Data Connections

## Purpose

This handout covers how Power BI connects to data and the different storage modes available. It also walks through three types of data source connections: local CSV files, SQL databases, and web pages.

---

## Prerequisites

- Power BI Desktop is installed.
- The `AdventureWorks Raw Data` folder is available on your machine.

---

## Part 1: Storage and Connection Modes

### Key Concepts

Power BI supports four storage and connection modes. Choosing the right one depends on the size of your data, how frequently it updates, and whether company policy restricts importing data.

---

### Storage Mode Reference Table

| Mode | How it works | Best for |
|---|---|---|
| **Import** | Data is copied into Power BI's in-memory engine | Fast queries, datasets under ~1 GB, infrequently changing data |
| **DirectQuery** | Queries are sent live to the source system | Large datasets, frequently updated data, or when data cannot leave the source |
| **Composite** | Mixes Import and DirectQuery in the same model | Boosting performance by caching some tables while keeping others live |
| **Live Connection** | Connects to a pre-published Power BI dataset or Azure Analysis Services model | Shared datasets, multi-developer teams, or a central "single source of truth" |

---

### Import Mode (Used in this course)

Import mode is the default. When you load data using Import mode, Power BI copies the data into memory and queries run against a local cache. This produces the fastest query performance.

**When Import mode is the right choice:**

- The dataset will be under 1 GB after compression.
- The source data does not change frequently.
- You do not need to apply restrictions on Power Query transformations or DAX.

---

### DirectQuery Mode

DirectQuery does not copy data. Instead, every visualization generates a live query that is sent back to the source system on demand.

**When DirectQuery is the right choice:**

- The dataset is too large to load into memory.
- Reports must always reflect the most current data.
- Company policy prohibits copying data out of the original source.

> **Note:** DirectQuery has limitations on Power Query transformations and some DAX functions. Read the Microsoft documentation for your specific connector before using DirectQuery in production.

---

### Composite Models

A composite model combines Import and DirectQuery tables within the same report. You can, for example, import a small lookup table for fast filtering while keeping a large fact table in DirectQuery.

**Composite models are useful when you need to:**

- Boost performance by caching dimension tables.
- Combine a DirectQuery source with additional imported data.
- Build a single model from two or more DirectQuery sources.

---

### Live Connections

A live connection points to a dataset already published to Power BI Service or Azure Analysis Services. The data model is defined once and shared across multiple reports.

**Benefits of live connections:**

- One dataset serves as a central source of truth.
- Multiple team members can author separate reports from the same underlying data.
- Model development and visualization development can be split between different people.

---

### Checkpoint ✅

Before moving on, make sure you can answer:

- Which mode loads data into Power BI's memory?
- Which mode sends live queries to the source?
- What is the main difference between a Composite model and a Live connection?

---

## Part 2: Connecting to a SQL Database

### Overview

Power Query can connect to many relational database systems, including SQL Server, MySQL, PostgreSQL, Oracle, SAP, and Microsoft Access. Once you understand how to connect to one, the process is nearly identical for others.

### Steps

1. Open Power BI Desktop.
2. On the Home ribbon, click **Get Data** > **More**.
3. In the search box, type the name of your database (for example, `mysql`).
4. Select the connector and click **Connect**.
5. Enter the **Server** address and the **Database** name in the connection dialog.
6. Expand **Advanced Options** if you need to:
   - Set a command timeout.
   - Write a custom SQL statement to filter or shape data before it enters Power Query.
7. Click **OK**.
8. When prompted, enter your database credentials (username and password).
9. In the Navigator window, check the tables you want to load.
10. Click **Transform Data** to open them in Power Query Editor, or **Load** to load them directly.

### Disabling Load for Temporary Tables

If you connect to a database for exploration but do not need the data in your final model:

1. In the Power Query Editor **Queries** pane, right-click the table.
2. Uncheck **Enable Load**.
3. The table name will appear in italics, indicating it will not be loaded into the data model.
4. Click **Close & Apply**.

> **Why this matters:** Disabling load keeps a query available for reference or testing without bloating your data model.

---

## Part 3: Connecting to Web Data

### Overview

The Web connector in Power Query can import data from hosted files or scrape structured tables directly from a webpage.

### Steps

1. In Power Query Editor, click **New Source** > **Web**.
2. Paste the URL of the webpage into the **Basic** input field.
3. Click **OK**. Power BI will scan the page and detect structured tables.
4. In the Navigator window:
   - Click each detected table on the left to preview its contents.
   - Use the **Web View** tab to see what the page looks like and identify which table you want.
5. Select the table you want to import and click **Transform Data**.
6. In Power Query Editor, review and correct:
   - Data types for each column.
   - Column headers (rename as needed).
7. Rename the query to something meaningful (for example, `Largest Asset Management Firms`).

### Disabling Load for Demo or Exploratory Queries

Repeat the same **Disable Load** process described in Part 2 when you do not need the web table in your final model.

---

## Key Takeaways

- **Import mode** is the default and the fastest option for most smaller, infrequently changing datasets.
- **DirectQuery** is for large or frequently changing data but comes with limitations.
- **Composite models** let you mix storage modes within one report.
- **Live connections** enable shared, centralized datasets across a team.
- Power Query can connect to flat files, relational databases, web pages, folders, and many other source types using the same **Get Data** workflow.
- When connecting to local files, Power BI stores the exact file path. If the file is renamed or moved, you must update the connection manually via **Data Source Settings**.
- Use **Disable Load** to keep exploratory or demo queries in the editor without loading them into the data model.

---

## Exercise 1: Connect to a Local CSV File

**Goal:** Load the AdventureWorks Calendar Lookup file into Power Query.

1. Open Power BI Desktop.
2. Click **Get Data** > **Text/CSV**.
3. Browse to the `AdventureWorks Raw Data` folder and select `AdventureWorks Calendar Lookup.csv`.
4. Preview the data. You should see a single column named `Date`.
5. Click **Transform Data** to open Power Query Editor.
6. In the **Queries** pane on the left, double-click the query name and rename it to `Calendar Lookup`.
7. Verify the `Date` column has a **Date** data type (look for the calendar icon in the column header).

**Expected result:** A single-column table named `Calendar Lookup` with dates beginning on `2020-01-01`.

---

## Exercise 2: Explore Storage Mode Options (Conceptual)

**Goal:** Match each scenario to the correct storage mode.

| Scenario | Correct mode |
|---|---|
| A 500 MB product catalog that updates monthly | ? |
| A live sales dashboard pulling from a 50 GB data warehouse | ? |
| A shared enterprise dataset used by 10 report authors | ? |
| A model that caches product lookups but keeps orders live | ? |

**Answers:** Import · DirectQuery · Live Connection · Composite
