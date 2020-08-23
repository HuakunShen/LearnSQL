# Query

## Query Clauses

| Clause Name  | Purpose                                                      |
| ------------ | ------------------------------------------------------------ |
| **SELECT**   | Determines which columns to include in the query’s result set |
| **FROM**     | Identifies the tables from which to draw data and how the tables should be joined |
| **WHERE**    | Filters out unwanted data                                    |
| **GROUP BY** | Used to group rows together by common column values          |
| **HAVING**   | Filters out unwanted groups                                  |
| **ORDER BY** | Sorts the rows of the final result set by one or more columns |

## Order of Execution

| Order | Clause       |
| ----- | ------------ |
| 1     | **FROM**     |
| 2     | **WHERE**    |
| 3     | **GROUP BY** |
| 4     | **HAVING**   |
| 5     | **SELECT**   |
| 6     | **ORDER BY** |
| 7     | **LIMIT**    |

## Select Clause

> The select clause determines which of all possible columns should be included in the query’s result set. 
>
> One of the last clauses that the database server evaluates. 

### Things in Select Clause

- **Literals**, such as numbers or strings
- **Expressions**, such as `transaction.amount * −1`
- **Built-in function calls**, such as `ROUND(transaction.amount, 2)`
- **User-defined function calls**

```mysql
SELECT emp_id, 'ACTIVE', emp_id * 3.14, UPPER(lname)
FROM employee
```

If you only need to execute a built-in function or evaluate a simple expression, you can skip the from clause entirely.

```mysql
SELECT VERSION(), USER(), DATABASE();
```

## Column Aliases

> One can add *column alias* after each element of `select` clause. The original name may be complicated (like an expression) or ambiguous.

```mysql
SELECT emp_id,
	'ACTIVE' status,
	emp_id * 3.14 empid_x_pi,
	UPPER(lname) last_name_upper
FROM employee
```

### AS keyword

For better readability, use the `AS` keyword.

```mysql
SELECT emp_id,
	'ACTIVE' AS status,
	emp_id * 3.14 AS empid_x_pi,
	UPPER(lname) AS last_name_upper
FROM employee
```

## Removing Duplicates (DISTINCT)

```mysql
SELECT DISTINCT cusd_id
FROM account;
```

## FROM Clause

> The from clause defines the tables used by a query, along with the means of linking the tables together.

## Tables

### 3 types of tables

- Permanent tables (i.e., created using the create table statement)
- Temporary tables (i.e., rows returned by a subquery)
- Virtual tables (i.e., created using the create view statement)

### Subquery-generated tables

```mysql
SELECT e.fname, e.lname
FROM (SELECT fname, lname, title, start_date
			FROM employee) AS e;
```

### Views

```mysql
CREATE VIEW employee_vw AS
SELECT emp_id, fname, lnamem, YEAR(start_date) AS start_year
FROM employee;

SELECT emp_id, start_year
FROM employee_vw;
```

### Table Links & Aliases

```mysql
SELECT e.fname, e.lname, d.name AS dept_name
FROM employee AS e INNER JOIN department AS d
ON e.dept_id = d.dept_id;
```

## WHERE Clause

> The where clause is the mechanism for filtering out unwanted rows from your result set.

```mysql
-- AND
SELECT <cols>
FROM <table_name>
WHERE <col> = <value> AND <col2> = <value2>
			
-- OR
SELECT <cols>
FROM <table_name>
WHERE <col> = <value> OR <col2> = <value2>
```

## GROUP BY & HAVING

`Group By` is used to group data by column values.

When using `Group By`, `having` can be used to filter group data in the same way `where` caluse filters data.

```mysql
SELECT d.name, count(e.emp_id) num_employees     
FROM department d INNER JOIN employee e     
ON d.dept_id = e.dept_id     
GROUP BY d.name     
HAVING count(e.emp_id) > 2;
```

## ORDER BY Clause

> The `order by` clause is the mechanism for sorting your result set using either raw column data or expressions based on column data.

```mysql
SELECT open_emp_id, product_cd
FROM account
ORDER BY open_emp_id, product_cd;
```

### Ascending vs Descending Sort Order

When sorting, you have the option of specifying ascending or descending order via the asc and desc keywords. 

The default is ascending, so you will need to add the desc keyword, only if you want to use a descending sort.

```mysql
SELECT account_id, product_cd, open_date, avail_balance
FROM account
ORDER BY avail_balance DESC;
```

### Sorting via Expressions

This query uses the built-in function `right()` to extract the last three characters of the **fed_id** column and then sorts the rows based on this value.

```mysql
SELECT cust_id, cust_type_cd, city, state, fed_id
FROM customer
ORDER BY RIGHT(fed_id, 3);
```

### Sorting viaNumeric Placeholders

Reference the columns by their position in the `select` clause rather than by name.

Sort by the 2nd and 5th columns.

```mysql
SELECT emp_id, title, start_date, fname, lname
FROM employee
ORDER BY 2, 5;
```











