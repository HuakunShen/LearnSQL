# Views

> Views do not involve data storage. A view is a **select** statement with a name, the query is stored for others to use.

```mysql
CREATE VIEW customer_vw (cust_id, fed_id, cust_type_cd, address, city, state, zipcode) AS
SELECT cust_id,
       concat('' ends in '', substr(fed_id, 8, 4)) fed_id,
       cust_type_cd,
       address,
       city,
       state,
       postal_code
FROM customer;
```

Use `describe` to view columns info in a view.

```mysql
describe customer_vw;
```

## Why Use Views?

### Data Security

If a table is accessible to user, all rows and columns in it are accessible to user. If some data are secret, the best approach is to keep the table private and create views for the table, returning the appropriate data using the **where** clause to filter rows, and **select** to filter columns.

#### Virtual Private Database (VPD)

Oracle Database has another option for securing rows and columns of a table.

**VPD** allows attaching policies to tables, after which the server will modify a user's query as necessary to enforce the policies.

### Data Aggregation

Reporting applications generally require aggregated data, and views are a great way to make it appear as though data is being pre-aggregated and stored in the database.
Views give more flexibility. Views can be used as **API** or **Black Box**. The view name can stay the same, but the underlying logic of the view can be modified later without needing to change the code for querying the original view.

For Example, a view **total_balance_view** can be used for aggregating (summing) the balance of accounts. If later, a new table (**total_balance**) is created for performance improvement, the view's logic can be modified, without changing the way of querying **total_balance_view**.

```mysql
-- original view
CREATE total_balance_view
(cust_id, total_deposits, total_debt)
AS
SELECT cust_id, SUM(deposit), SUM(debt)
FROM account
GROUP BY deposit, debt;

-- modify view
CREATE OR REPLACE VIEW total_balance_view
(cust_id, total_deposits, total_debt)
AS
SELECT cust_id, total_deposits, total_debt
FROM total_balance;
```

**In summary, users will see a performance improvement without needing to modify their queries.**

### Hiding Complexity

> Shield end users from complexity.

For example, a view uses data from multiple tables using joins or subqueries. Users can use the view to avoid complexity.

### Joining Partitioned Data

Some database designs break large tables into multiple pieces in order to improve performance.

<details>
<summary>Example</summary>
For example, if the <strong>transaction</strong> table became large, the designers may decide to break it into two tables: <strong>transaction_current</strong>, which holds the latest six months’ of data, and <strong>transaction_historic</strong>, which holds all data up to six months ago. If a customer wants to see all the transactions for a particular account, you would need to query both tables. By creating a view that queries both tables and combines the results together (Using <strong>UNION</strong>), however, you can make it look like all transaction data is stored in a single table.
</details>
Using a view in this case is a good idea because it allows the designers to change the structure of the underlying data without the need to force all database users to modify their queries.

## Updatable Views

**MySQL**, **Oracle Database**, **SQL Server** all support modifyin data through views with the following restrictions:

- No aggregate functions are used (**max()**, **min()**, **avg()**, etc.).
- The view does not employ **group by** or **having** clauses.
- No subqueries exist in the **select** or **from** clause, and any subqueries in the **where** clause do not refer to tables in the **from** clause.
- The view does not utilize **union**, **union all**, or **distinct**.
- The **from** clause includes at least one table or updatable view.
- The **from** clause uses only inner joins if there is more than one table or view.

Cannot modify more than one base table through a join view. If a view joins 2 tables, the data of each table can be modified separately, but you cannot update multiple columns belonging to more than 1 table in the same query.

```mysql
CREATE VIEW join_view
(...)
AS
SELECT ...
FROM tableA INNER JOIN tableB ON tableA.id = tableB.id;

-- This Would Work
UPDATE join_view
SET tableA.col1 = ...
WHERE id = ...;

UPDATE join_view
SET tableB.col2 = ...
WHERE id = ...;


-- This Would Fail
UPDATE join_view
SET tableA.col1 = ..., tableB.col2 = ...
WHERE id = ...;
```

## instead-of triggers

Oracle Database and SQL Server also support updatable views with many restrictions just like MySQL.

With PL/SQL or Transact-SQL, you can use a feature called _instead-of trigger_, which allows you to essentially intercept `insert`, `update`, `delete` statements against a view, and write custom code to incorporate the changes. Without this type of feature, there are simply too many restrictions to make updating through views a feasible strategy for nontrivial applications.
