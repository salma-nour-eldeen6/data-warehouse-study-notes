# Introduction

### Data → Information → Knowledge

**Data**:
Raw facts with no meaning yet.
(Example: numbers, text, logs)

**Information**:
Data after processing and organizing it.
It becomes meaningful and useful.

**Knowledge**:
Deeper understanding extracted from information.
It represents **hidden patterns and insights**.

![image.png](img/introduction/image.png)

<aside>

- **Knowledge = hidden patterns**
When you analyze information and discover patterns, you create knowledge.
- When you **share knowledge**, it becomes **information for others**
(because they receive it as something structured and understandable).
- You can **process information multiple times**,
and it can still remain **information** unless you discover new insights (knowledge).
</aside>

---


## **Database Server**

A **DB Server** is a server responsible for storing, managing, and retrieving data efficiently and securely. It **allows multiple users or applications to access the same data simultaneously**, while ensuring consistency, security, and integrity. It is the core of any system that relies on structured data.

**Main Functions:**

- Store data in **tables** in an organized way.
- Perform **CRUD operations**: Create, Read, Update, Delete.
- Manage **transactions** to ensure **data integrity**.
- Handle **multi-user access** and concurrency.
- Manage **backups** and **recovery**.

---

## **Index**

An **Index** is a **database object** that exists independently of the table data and **speeds up data retrieval** by providing a fast lookup mechanism, without scanning the entire table. It can be created or dropped without physically affecting the table.

**Types of Indexes:**

### **- B-Tree Index:**

Most common; good for exact matches or range queries.

- B-trees, short for *balanced trees*, are the most common type of database index. A **B-tree index** is an ordered list of values divided into ranges. By associating a key with a row or range of rows, B-trees provide excellent retrieval performance for a wide range of queries, including exact match and range searches.-
    
    ![image.png](img/introduction/image%201.png)
    
    ---
    

### **- Bitmap Index**

Used in OLAP for columns with low cardinality (like Gender or Status).

- A Bitmap Index is like a map of bits for each value in a column, which helps the database find rows faster without scanning the entire table
- In a **bitmap index**, the database stores a bitmap for each index key. In a conventional B-tree index, one index entry points to a single row.
    
    In a bitmap index, each index key stores pointers to multiple rows.
    
    Bitmap indexes are primarily designed for data warehousing or environments in which queries reference many columns in an ad hoc fashion. Situations that may call for a bitmap index include:
    
    - The indexed columns have low [cardinality](https://docs.oracle.com/en/database/oracle/oracle-database/19/cncpt/Chunk806120860.html#GUID-5CD22620-6D7A-40DC-BA09-EE3B5339C7F8), that is, the number of distinct values is small compared to the number of table rows.
    - The indexed table is either read-only or not subject to significant modification by DML statements.

For example, if you want to count all female customers, the database can check the bitmap for 'F' directly instead of scanning all rows.

Table 3-4 Sample Bitmap for One Column

| **Value** | **Row 1** | **Row 2** | **Row 3** | **Row 4** | **Row 5** | **Row 6** | **Row 7** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `M` | 1 | 0 | 1 | 1 | 1 | 0 | 0 |
| `F` | 0 | 1 | 0 | 0 | 0 | 1 | 1 |

A mapping function converts each bit in the bitmap to a rowid of the `customers` table. Each bit value depends on the values of the corresponding row in the table. 

For example, the bitmap for the `M` value contains a `1` as its first bit because the gender is `M` in the first row of the `customers` table. The bitmap `cust_gender='M'` has a `0` for the bits in rows 2, 6, and 7 because these rows do not contain `M` as their value.

---

able 3-5 Sample Bitmap for Two Columns

| **Value** | **Row 1** | **Row 2** | **Row 3** | **Row 4** | **Row 5** | **Row 6** | **Row 7** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `M` | 1 | 0 | 1 | 1 | 1 | 0 | 0 |
| `F` | 0 | 1 | 0 | 0 | 0 | 1 | 1 |
| `single` | 0 | 0 | 0 | 0 | 0 | 1 | 1 |
| `divorced` | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| `single` or `divorced`, and `F` | 0 | 0 | 0 | 0 | 0 | 1 | 1 |

Bitmap indexing efficiently merges indexes that correspond to several conditions in a `WHERE` clause. Rows that satisfy some, but not all, conditions are filtered out before the table itself is accessed. This technique improves response time, often dramatically.

---

- The **DB Server** creates and maintains the index.
- Every time data changes (Insert/Update/Delete), the server automatically updates the index.

**Source**

- https://docs.oracle.com/en/database/oracle/oracle-database/19/cncpt/indexes-and-index-organized-tables.html#GUID-B15C4817-7748-456D-9740-8B9628AF9F47

---

# **Views**

They evaluate the data in the tables underlying the view definition **at the time the view is queried**. It is a logical view of your tables, with no data stored anywhere else.

The upside of a view is that it will **always return the latest data to you**. The **downside of a view is that its performance** depends on how good a select statement the view is based on. If the select statement used by the view joins many tables, or uses joins based on non-indexed columns, the view could perform poorly.

# **Materialized views**

They are similar to regular views, in that they are a logical view of your data (based on a select statement), however, the **underlying query result set has been saved to a table**. The upside of this is that when you query a materialized view, **you are querying a table**, which may also be indexed.

In addition, because all the joins have been resolved at materialized view refresh time, you pay the price of the join once (or as often as you refresh your materialized view), rather than each time you select from the materialized view. In addition, with query rewrite enabled, Oracle can optimize a query that selects from the source of your materialized view in such a way that it instead reads from your materialized view. In situations where you create materialized views as forms of aggregate tables, or as copies of frequently executed queries, this can greatly speed up the response time of your end user application. The **downside though is that the data you get back from the materialized view is only as up to date as the last time the materialized view has been refreshed**.

---

![image.png](img/introduction/image%202.png)

Materialized views can be set to refresh manually, on a set schedule, or *based on the database detecting a change in data from one of the underlying tables*. Materialized views can be incrementally updated by combining them with materialized view logs, which **act as change data capture sources** on the underlying tables.

Materialized views are most often used in data warehousing / business intelligence applications where querying large fact tables with thousands of millions of rows would result in query response times that resulted in an unusable application.

https://stackoverflow.com/questions/93539/what-is-the-difference-between-views-and-materialized-views-in-oracle

---

## Different Information Worlds

An organization’s information usually exists in **two very different worlds**:

1. **Operational Systems (OLTP – Online Transaction Processing)**
2. **Data Warehouse (OLAP – Online Analytical Processing)**

They may store the same business data, but they serve completely different purposes.

<aside>

**Operational Systems – Where Data Is Created**

Operational systems are the systems that run the daily business.

Examples:

- Taking orders
- Registering customers
- Logging complaints
- Processing payments

These systems are built for:

- Speed
- Accuracy
- Handling one transaction at a time
- Supporting repetitive tasks

Users interact with **one record at a time**.

Think of a cashier scanning one product.

Or a support agent updating one complaint.

The goal here is **execution**, not analysis.

Technically:

- Highly normalized databases (often 3NF)
- Optimized for inserts, updates, deletes
- Short, fast transactions

This is the “engine room” of the company.

</aside>

<aside>

**Data Warehouse – Where Data Is Analyzed**

The data warehouse serves a completely different audience.

Its users:

- Analysts
- Executives
- Managers
- Data scientists

They don’t care about one transaction.

They ask questions like:

- How many orders were placed this week vs last week?
- Why did customer churn increase?
- Which product category is growing fastest?

These questions require:

- Scanning thousands or millions of rows
- Aggregating data
- Comparing across time
- Summarizing patterns

Users rarely retrieve a single row.

They retrieve **patterns across many rows**.

And their questions constantly change.

That’s a key point:

Operational tasks are repetitive.

Analytical questions are unpredictable.

The goal here is **understanding**, not execution.

</aside>

---

![image.png](img/introduction/image%203.png)

https://www.geeksforgeeks.org/dbms/difference-between-olap-and-oltp-in-dbms/

OLAP (Online Analytical Processing) and OLTP (Online Transaction Processing) are both integral parts of data management, but they have different functionalities.

- OLTP focuses on handling large numbers of transactional operations in real time, ensuring data consistency and reliability for daily business operations.
- OLAP is designed for complex queries and data analysis, enabling businesses to derive insights from vast datasets through multidimensional analysis.

<aside>

- **OLTP DBs** → normalize → good for transactions
- **OLAP / DWH** → denormalize → good for analysis and reporting
</aside>

## **What is Normalization?**

*Normalization* is the process of organizing a database to reduce redundancy and improve [data integrity](https://database.guide/what-is-data-integrity/).

https://database.guide/what-is-normalization/

![image.png](img/introduction/image%204.png)

## **Problems of Normalization in OLAP / DWH**

1. **Too many joins**
    - Queries need to combine many tables → **slower performance** for big datasets.
2. **Complex queries**
    - Harder to write and maintain SQL queries.
3. **Not ideal for reporting / analytics**
    - Analytical queries prefer **fewer tables**, so fewer joins = faster scans.

---

Even if both systems use the same business data, they differ in:

- User type
- Query behavior
- Performance requirements
- Data structure
- Frequency of change
    
    

---

**Operational systems:**

- Focus on current state
- Optimize for writes
- Handle small, fast transactions

**Data warehouses:**

- Focus on history
- Optimize for reads
- Handle large, complex queries

This is not just a hardware separation issue.

Moving an operational database to another server does not automatically transform it into a data warehouse.

<aside>

- OLTP = “doing the work” (write)
- OLAP = “analyzing the work” (read)
</aside>

If the structure remains transaction-oriented:

- Queries become complex
- Joins multiply
- Performance degrades
- Business users struggle

True analytical environments must be designed specifically for analytical needs.

---
