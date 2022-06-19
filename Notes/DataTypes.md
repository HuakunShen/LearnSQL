# Data Types

## Character Data

```sql
char(20)
varchar(20)
```

The maximum length for `char` is 255 bytes, `varchar` columns is 65,535 bytes.

For Oracle Database, use `varchar2` type when defining variable-length character columns.

### Character Sets

MySQL can store data using various character sets, both single- and multibyte. To view the supported character sets in your server, you can use the show command.

```sql
SHOW CHARACTER SET;
```

To choose a character set when defining a column:

```sql
varchar(20) character set utf8
```

With **MySQL**, you may set default character set for your entire database:

```sql
create database db_name character set utf8;
```

### Text Data

If you need to store data that might exceed the 64 KB limit for varchar columns, you will need to use one of the text types.

#### MySQL Text Types

| Text Type  | Maximum number of bytes |
| ---------- | ----------------------- |
| Tinytext   | 255                     |
| Text       | 65,535 (~64KB)          |
| Mediumtext | 16,777,215 (~16MB)      |
| Longtext   | 4,294,967,295 (~4GB)    |

#### Notes

- If data exceeds maximum size, the data will be truncated.
- Trailing spaces will not be removed.
- When using `text` columns for sorting or grouping, only the first 1024 bytes are used (limit may be increased).
- SQL Server has a single `text` type for large character data. DB2 and Oracle use a data type called `clob` (Character Large Object)

**Oracle Database:**

- 2000 bytes for `char`
- 4000 bytes for `varchar2`

**SQL Server:**

- 8000 bytes for both `char` and `varchar`

## Numeric Data

### MySQL Integer Types

| Type      | Signed Range                                            | Unsigned Range                  |
| --------- | ------------------------------------------------------- | ------------------------------- |
| Tinyint   | −128 to 127                                             | 0 to 255                        |
| Smallint  | −32,768 to 32,767                                       | 0 to 65,535                     |
| Mediumint | −8,388,608 to 8,388,607                                 | 0 to 16,777,215                 |
| Int       | −2,147,483,648 to 2,147,483,647                         | 0 to 4,294,967,295              |
| Bigint    | −9,223,372,036,854,775,808 to 9,223,372,036,854,775,807 | 0 to 18,446,744,073,709,551,615 |

### MySQL Floating-Point Types

| Type          | Numeric Range                                                |
| ------------- | ------------------------------------------------------------ |
| $Float(p,s)$  | −3.402823466E+38 to −1.175494351E-38 and 1.175494351E-38 to 3.402823466E+38 |
| $Double(p,s)$ | −1.7976931348623157E+308 to −2.2250738585072014E-308 and 2.2250738585072014E-308 to 1.7976931348623157E+308 |

you can specify a precision (the total number of allowable digits both to the left and to the right of the decimal point) and a scale (the number of allowable digits to the right of the decimal point), but they are not required.

`float(4,2)` will store a total of four digits, two to the left of the decimal and two to the right of the decimal. Such a column would handle the numbers 27.44 and 8.19 just fine, but the number 17.8675 would be roun- ded to 17.87, and attempting to store the number 178.375 in your float(4,2) column would generate an error.

## Temporal Data

### MySQL Temporal Types

| Type | Default Format | Allowable Values |
| ---- | -------------- | ---------------- |
| Date | YYYY-MM-DD | 1000-01-01 to 9999-12-31 |
| Datetime | YYYY-MM-DD HH:MI:SS | 1000-01-01 00:00:00 to 9999-12-31 23:59:59 |
| Timestamp | YYYY-MM-DD HH:MI:SS | 1970-01-01 00:00:00 to 2037-12-31 23:59:59 |
| Year | YYYY | 1901 to 2155 |
| Time | HHH:MI:SS | −838:59:59 to 838:59:59 |

If you wanted to insert the date March 23, 2005 into a date column using the default format **YYYY-MM-DD**, you would use the string *'2005-03-23'*

### Date Format Components

| Component | Definition | Range |
| --------- | ---------- | ----- |
| YYYY | Year, including century| 1000 to 9999 |
| MM |Month| 01 (January) to 12 (December) |
| DD| Day| 01 to 31 |
| HH| Hour| 00 to 23 |
| HHH| Hours (elapsed)| −838 to 838 |
| MI| Minute |00 to 59 |
| SS |Second |00 to 59|

### Date Conversions

The following would fail, since 'DEC-21-1980' violates default date format.

```sql
UPDATE person
SET birth_date = 'DEC-21-1980'
WHERE person_id = 1;
```

To convert string to date:

```sql
UPDATE person
SET birth_date = str_to_date('DEC-21-1980', '%b-%d-%Y')
WHERE person_id = 1;
```

| Placeholder | Description |
| ----------- | ----------- |
| %a | The short weekday name, such as Sun, Mon, ... |
| %b | The short month name, such as Jan, Feb, ... |
| %c | The numeric month (0..12) |
| %d | The numeric day of the month (00..31) |
| %f | The number of microseconds (000000..999999) |
| %H | The hour of the day, in 24-hour format (00..23) |
| %h | The hour of the day, in 12-hour format (01..12) |
| %i | The minutes within the hour (00..59) |
| %j | The day of year (001..366) |
| %M | The full month name (January..December) |
| %m | The numeric month |
| %p | AM or PM |
| %s | The number of seconds (00..59) |
| %W | The full weekday name (Sunday..Saturday) |
| %w | The numeric day of the week (0=Sunday..6=Saturday) |
| %Y | The four-digit year |

## NULL

### Scenarios

- Not Applicable: column not used
- Unknown Value: optional column
- Value Undefined

### Notes

- An expression can be **null**, but it can never equal **null**
- Two nulls are never equal to each other

```sql
SELECT emp_id, fname, lname, superior_emp_id
FROM employee
WHERE superior_emp_id IS NULL
	AND emp_id IS NOT NULL;
```













