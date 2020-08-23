# Constraints

> Restriction placed on one or more columns of a table.

| Constraint Type           | Description                                                  |
| ------------------------- | ------------------------------------------------------------ |
| *Primary Key Constraints* | Identify the column or columns that guarantee uniqueness within a table |
| *Foreign Key Constraints* | Restrict one or more columns to contain only values found in another table’s pri- mary key columns, and may also restrict the allowable values in other tables if `update cascade` or `delete cascade` rules are established |
| *Unique Constraints*      | Restrict one or more columns to contain unique values within a table (primary key constraints are a special type of unique constraint) |
| *Check Constraints*       | Restrict the allowable values for a column                   |

If you want to use foreign key constraints with the MySQL server, you must use the InnoDB storage engine for your tables.

## Constraint Creation

### Add Constraints Along With Table Creation

```mysql
CREATE TABLE product
(
    product_cd      VARCHAR(10) NOT NULL,
    name            VARCHAR(50) NOT NULL,
    product_type_cd VARCHAR(10) NOT NULL,
    date_offered    DATE,
    date_retired    DATE,
    CONSTRAINT fk_product_type_cd FOREIGN KEY (product_type_cd) REFERENCES product_type (product_type_cd),
    CONSTRAINT pk_product PRIMARY KEY (product_cd)
);
```

### Add Constraints After Table Creation

```mysql
ALTER TABLE product
    ADD CONSTRAINT pk_product PRIMARY KEY (product_cd);

ALTER TABLE product
    ADD CONSTRAINT fk_product_type_cd FOREIGN KEY (product_type_cd) REFERENCES product_type (product_type_cd);
```

### Drop Constraints

```mysql
ALTER TABLE product DROP PRIMARY KEY;
ALTER TABLE product DROP FOREIGN KEY fk_product_type_cd;
```

## Constraints and Indexes

| Constraint Type         | MySQL                  | SQL Server              | Oracle Database                          |
| ----------------------- | ---------------------- | ----------------------- | ---------------------------------------- |
| Primary Key Constraints | Generates Unique Index | Generates Unique Index  | Uses Existing Index or Creates New Index |
| Foreign Key Constraints | Generates Index        | Does Not Generate Index | Does Not Generate Index                  |
| Unique Constraints      | Generates Unique Index | Generates Unique Index  | Uses Existing Index or Creates New Index |

Oracle Database use an existing index (if an appropriate one exists) to enforce primary key and unique constraints.

Although, neither Oracle Database nor SQL Server generates an index for a foreign key constraint, both servers' documentation advises that indexes be created for every foreign key.

## Cascading Constraints

`ON UPDATE CASCADE`: Referencing rows are updated in the child table when the referenced row is updated in the parent table which has a primary key.

`ON DELETE CASCADE` : A cascading delete removes rows from the child table when a row is deleted from the parent table according to the parent table's primary key and child table's foreign key.

```mysql
-- drop original foreign key constraint
ALTER TABLE product
DROP FOREIGN KEY fk_product_type_cd;
-- add another foreign key constraint with cascading update
ALTER TABLE product
    ADD CONSTRAINT fk_product_type_cd FOREIGN KEY (product_type_cd)
        REFERENCES product_type (product_type_cd)
        ON UPDATE CASCADE,
        ON DELETE CASCADE;
```



















