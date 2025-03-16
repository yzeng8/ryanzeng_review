# Amazon Data Engineer Interview Tech-Wise Prep

## I. SQL Related Concepts

### A. Relational Database









### B. SQL Queries

#### 1.**Efficient Query Design**

- ##### **Proper JOIN strategies (Nested Loops, Hash Join, Merge Join)**
- ##### **Filtering early: WHERE vs. HAVING:**
  
  Filtering data early in the query helps improve performance by **reducing the number of rows processed** in subsequent steps.
  **Where clause**: filter before the any group or aggreagation
  **Having Clause**: Filter after 
  
- ##### **Column Indexing**

  ```sql
  CREATE INDEX idx_lastname ON employees(last_name);
  SELECT * FROM customers WHERE last_name = 'Smith';
  ```

  In SQL, Column Indexing is stored as B-trees(Binary trees) to speed up querying process. Lookup Time Speed from O(N) to O(logN).

  **Tradeoff**:

  | Tradeoff                    | Explanation                                                  |
  | --------------------------- | ------------------------------------------------------------ |
  | Faster Reads, Slower Writes | Content CellIndexes speed up `SELECT` queries but slow down `INSERT`, `UPDATE`, and `DELETE` operations because the index must be updated as well. |
  | Extra Storage Space         | Extra Disk Storage.                                          |

| ✅ **When to Use Column Indexes**            | ❌ **When Not to Use Indexes**                             |
| ------------------------------------------- | --------------------------------------------------------- |
| **Searching (`WHERE` conditions)**          | **Small tables (full scan is faster)**                    |
| **Speeding up `JOIN` operations**           | **High write/update/delete frequency**                    |
| **Optimizing `ORDER BY` and `GROUP BY`**    | **Low-cardinality columns (e.g., gender)**                |
| **Covering queries to avoid table lookups** | **Tables frequently modified (indexes slow down writes)** |



- ##### **Subqueries vs. Common Table Expressions (CTEs)**

  A **subquery** (also called a **nested query**) is a query inside another SQL query. 
  Common Table Expression - A **CTE** is a **temporary** named result set that can be used within a query

  **Use a Subquery when**:

  - It is **short and simple**.
  - It does **not need to be reused multiple times**.
  - You are working with **a small dataset**.

  **Use a CTE when**:

  - The result is **referenced multiple times** in the query.
  - You need **recursive logic**.
  - You want **better readability and maintainability**.

  | Feature               | Subqueries ❌           | CTEs ✅                           |
  | --------------------- | ---------------------- | -------------------------------- |
  | **Readability**       | Harder to read         | Easier to read                   |
  | **Reusability**       | Can't reuse subquery   | Can be referenced multiple times |
  | **Performance**       | May run multiple times | Optimized execution              |
  | **Recursive Queries** | ❌ Not possible         | ✅ Supported                      |

  ```SQL
  WITH AvgSalary AS (
      SELECT department_id, AVG(salary) AS avg_salary
      FROM employees
      GROUP BY department_id
  )
  SELECT e.name, e.salary
  FROM employees e
  JOIN AvgSalary a ON e.department_id = a.department_id
  WHERE e.salary > a.avg_salary; -- CTE Common Table Expressions Approach
  
  
  SELECT e.name, e.salary
  FROM employees e
  WHERE e.salary > (SELECT AVG(salary) FROM employees WHERE department_id = e.department_id);
  
  ```

  

- ##### Window functions and performance considerations

​	Window functions will not collapse the results as **GROUP BY** does, it will present the result for each row of data that selected.

| Feature                                | GROUP BY | Window Function  |
| -------------------------------------- | -------- | ---------------- |
| **Collapses Rows?**                    | ✅ Yes    | ❌ No             |
| **Retains Individual Rows?**           | ❌ No     | ✅ Yes            |
| **Adds Aggregated Value as a Column?** | ❌ No     | ✅ Yes            |
| **Uses PARTITION BY?**                 | ❌ No     | ✅ Yes (Optional) |

```sql
SELECT salesperson, SUM(amount) AS total_sales
FROM sales
GROUP BY salesperson; -- Group BY function that Collapsing the results.

SELECT sale_id, salesperson, amount,
       SUM(amount) OVER (PARTITION BY salesperson) AS total_sales
FROM sales;   -- Window Function that retaining the row results 
```



Some types of window functions:

* Aggregation Function -- Add the aggregation result as a new column

***\*SUM()\****, [AVG()](https://www.geeksforgeeks.org/sql-count-avg-and-sum/), ***\*COUNT()\****, ***\*MAX()\****, and ***\*MIN()\****.    

```sql
SUM() OVER (PARTITION BY KEYWORDS) AS SUM_RESULTS_COLUMN
```

* Rank function -- add ranking number as a new column
* Row number Function -- Assign each row with a new number 

```sql
SELECT Name, Department, Salary,
       ROW_NUMBER() OVER(PARTITION BY Department ORDER BY Salary DESC) AS emp_row_no
FROM employee;
```









## II. large scale data processing

## -  data structure optimization and scalability of algorithmslarge scale data processing, data structure optimization and scalability of algorithms