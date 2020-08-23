# Filtering

## Operators

- Comparison Operators:
  - =, !=, <>, <, >, LIKE, IN, BETWEEN
- Arithmetic Operators:
  - +, -, \*, /

## Condition Types

### Equality Conditions

```mysql
-- Equality
SELECT fname, lname
FROM person
WHERE gender = 'M';

-- Inequality
SELECT fname, lname
FROM person
WHERE gender <> 'F';			-- != also works for inequality

DELETE FROm account
WHERE status = 'CLOSED' AND YEAR(close_date) = 2002;
```

### Range Conditions

```mysql
SELECT emp_id, fname, lname, start_date
FROM employee
WHERE start_date < '2007-01-01' 
	AND start_date >= '2005-01-01';
```

#### Between Operator

```mysql
-- Equivalently
SELECT emp_id, fname, lname, start_date
FROM employee
WHERE start_date BETWEEN '2005-01-01' AND '2007-01-01';

SELECT account_id, product_cd, cust_id, avail_balance     
FROM account     
WHERE avail_balance BETWEEN 3000 AND 5000;
```

#### String Ranges

```mysql
SELECT fname, lname
FROM person
WHERE phone BETWEEN '500-000-0000' AND '999-999-9999';
```

### Membership Conditions

```mysql
SELECT account_id, product_cd, cust_id, avail_balance     
FROM account
WHERE product_cd = 'CHK' OR product_cd = 'SAV'
OR product_cd = 'CD' OR product_cd = 'MM';
-- Equivalently
SELECT account_id, product_cd, cust_id, avail_balance 
FROM account 
WHERE product_cd IN ('CHK','SAV','CD','MM');

SELECT account_id, product_cd, cust_id, avail_balance 
FROM account 
WHERE product_cd NOT IN ('CHK','SAV','CD','MM');
```

#### Using Subqueries

```mysql
-- Equivalently
SELECT account_id, product_cd, cust_id, avail_balance
FROM account
WHERE product_cd IN (SELECT product_cd FROM product
	WHERE product_type_cd = 'ACCOUNT');
```

### Matching Conditions

```mysql
-- All employee whose last name begins with 'T'
SELECT emp_id, fname, lname
FROM employee
WHERE LEFT(lname, 1) = 'T';
```

#### Wildcard Characters

| Wildcard Character | Matches                               |
| ------------------ | ------------------------------------- |
| _                  | Exactly one character                 |
| %                  | Any number of charaters (including 0) |

#### Sample Search Expressions

| Search Expression     | Interpretation                                            |
| --------------------- | --------------------------------------------------------- |
| F%                    | Strings beginning with *'F'*                              |
| %t                    | Strings ending with *'t'*                                 |
| %bas%                 | Strings containing the substring *'bas'*                  |
| _ _ t _               | Four-character strings with a *'t'* in the third position |
| _ _ _ - _ _ - _ _ _ _ | 11-char strings with dashes in the 4th and 7th positions  |

```mysql
SELECT lname
FROM employee
WHERE lname LIKE '_a%e%'
	AND fed_id LIKE '___-__-____';
```

### Using Regular Expressions

```mysql
SELECT emp_id, fname, lname
FROM employee
WHERE lname REGEXP '^[FG]';
```

**Oracle Database:** `regexp_like` function

**SQL Server:** allows regular expressions to be used with the `like` operator.













