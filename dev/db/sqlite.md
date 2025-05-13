---
title: SQLite
---

`sqlite3 DatabaseName.db`

### Shell

* `.open`
  * `.open <path>` # opens a database
* `.help`
* `.databases`  # shows all databases in the current connection
* `.tables`     # display all tables in the current database
* `.schema`     # Show the CREATE statements.
* `.show`       # Show the current values for various settings
* `.quit`
* `.dump`       # export complete database in a text file
  * `testDB.db .dump > testDB.sql`  # converts the entire contents of testDB.db into SQLite statements dumping it into an ASCII text file
  * `testDB.db < testDB.sql`        # perform restoration from the generated testDB.sql





The database names *main* and *temp* are reserved for the primary database and database to hold temporary tables and other temporary data objects. Both of these database names exist for every database connection and should not be used for attachment, otherwise you will get the following warning message.

```sql
  SELECT
    <columns>
  FROM
    <table>
  WHERE
    <column_1> LIKE <pattern>;
```
#### NULL

* use NULL instead of using a special value to indicate that the information is missing
* NULL is special because you cannot compare it with another value (if the two pieces of information are unknown, you cannot compare them).
* NULL is even cannot be compared with itself; NULL is not equal to itself so NULL = NULL always results in false.
* When it comes to sorting, SQLite considers NULL to be smaller than any other value.

#### wildcards

SQLite provides two wildcards for constructing patterns. They are percent sign % and underscore _ :

* `%` matches any sequence of zero or more characters.
* `_` matches any single character.


DATA TYPE
------------------

You would use these data types while creating your tables. SQLite uses a more general dynamic type system. In SQLite, the datatype of a value is associated with the value itself, not with its container.

### Storage class
SQLite storage class is slightly more general than a datatype. The INTEGER storage class, for example, includes 6 different integer datatypes of different lengths.

* `NULL`    # The value is a NULL value.
* `INTEGER` # The value is a signed integer, stored in 1, 2, 3, 4, 6, or 8 bytes depending on the magnitude of the value.
* `REAL`    # The value is a floating point value, stored as an 8-byte IEEE floating point number.
* `TEXT`    # The value is a text string, stored using the database encoding (UTF-8, UTF-16BE or UTF-16LE)
* `BLOB`    # The value is a blob of data, stored exactly as it was input.

### Affinity Type
SQLite supports the concept of type affinity on columns. Any column can still store any type of data but the preferred storage class for a column is called its affinity. Each table column in an SQLite3 database is assigned one of the following type affinities −

* `TEXT`    # This column stores all data using storage classes NULL, TEXT or BLOB.
* `NUMERIC` # This column may contain values using all five storage classes.
* `INTEGER` # Behaves the same as a column with NUMERIC affinity, with an exception in a CAST expression.
* `REAL`    # Behaves like a column with NUMERIC affinity except that it forces integer values into floating point representation.
* `NONE`    # A column with affinity NONE does not prefer one storage class over another and no attempt is made to coerce data from one storage class into another.

### Affinity and Type Names
Following table lists down various data type names which can be used while creating SQLite3 tables with the corresponding applied affinity.

* **INTEGER**
  * INT
  * INTEGER
  * TINYINT
  * SMALLINT
  * MEDIUMINT
  * BIGINT
  * UNSIGNED
  * BIG INT
  * INT2
  * INT8
* **TEXT**
  * CHARACTER(20)
  * VARCHAR(255)
  * VARYING CHARACTER(255)
  * NCHAR(55)
  * NATIVE CHARACTER(70)
  * NVARCHAR(100)
  * TEXT
  * CLOB
* **NONE**
  * BLOB
  * no datatype specified
* **REAL**
  * REAL
  * DOUBLE
  * DOUBLE PRECISION
  * FLOAT
* **NUMERIC**
  * NUMERIC
  * DECIMAL(10,5)
  * BOOLEAN
  * DATE
  * DATETIME

### Boolean Datatype

SQLite does not have a separate Boolean storage class. Instead, Boolean values are stored as integers 0 (false) and 1 (true).

### Date and Time Datatype

SQLite does not have a separate storage class for storing dates and/or times, but SQLite is capable of storing dates and times as TEXT, REAL or INTEGER values.
* TEXT # A date in a format like "`YYYY-MM-DD HH:MM:SS.SSS`"
* REAL # The number of days since noon in Greenwich on November 24, 4714 B.C.
* INTEGER # The number of seconds since 1970-01-01 00:00:00 UTC

You can choose to store dates and times in any of these formats and freely convert between formats using the built-in date and time functions.


CREATE TABLE
---------------

Creating a basic table involves:
* naming the table
* defining its columns and each column's data type.

```sql
  CREATE TABLE dbname.tablename(
    column1 datatype PRIMARY KEY(one or more columns),
    column2 datatype,
    column3 datatype,
  );
```

`CREATE TABLE IF NOT EXISTS <table>` #

#### examples

```sql
  CREATE TABLE COMPANY(
    ID        INT       PRIMARY KEY     NOT NULL,
    NAME      TEXT                      NOT NULL,
    AGE       INT                       NOT NULL,
    ADDRESS   CHAR(50),
    SALARY    REAL
  );
```

ATTACH / DETACH
--------------------------------

`ATTACH DATABASE "<dbname>" AS "<alias>";`

When you have multiple databases available and you want to use any one of them at a time:  
ATTACH DATABASE is used to select a particular database.  
After this command, all SQLite statements will be executed under the attached database.

`DETACH DATABASE '<alias>';`

SELECT
-------------

```sql
SELECT <columns>
FROM <table>;
```

### clauses

* `ORDER BY`        # sort the result set
  * `ORDER BY <column_1> ASC, <column_2> DESC` # Default: ASC (von oben nach unten aufsteigend)
* `SELECT DISTINCT <columns>` # query unique rows in a table / remove duplicate rows in the result set.
* `WHERE`           # filter rows in the result set
  * `WHERE <condition>`
  * `WHERE column_1 = 100;`
  * `WHERE column_2 IN (1,2,3);`
  * `WHERE column_3 LIKE 'An%';`
  * `WHERE column_4 BETWEEN 10 AND 20;`
* `LIMIT <row_count> OFFSET <offset>`     # constrain the number of rows returned
  * `LIMIT 10`                            # get the first 10 rows in the tracks table
  
* `INNER JOIN` 
* `LEFT JOIN`       # query data from multiple tables using join.
* `GROUP BY`        # get the group rows into groups and apply aggregate function for each group.
* `HAVING`          # filter groups

#### examples

```sql
/* get the first 10 tracks */
SELECT trackid, trackname FROM tracks ORDER BY trackid ASC LIMIT 10;
/* get the last 10 tracks */
SELECT trackid, trackname FROM tracks ORDER BY trackid DESC LIMIT 10;
/* get the top 10 biggest tracks by size */
SELECT trackid,	trackname, bytes FROM	tracks ORDER BY	bytes DESC LIMIT 10;
```

### logical operators

* `ALL`     # returns 1 if all expressions are 1.
* `AND`	    # returns 1 if both expressions are 1, and 0 if one of the expressions is 0.
* `ANY`	    # returns 1 if any one of a set of comparisons is 1.
* `BETWEEN`	# returns 1 if a value is within a range.
* `EXISTS`	# returns 1 if a subquery contains any rows.
* `IN`	    # returns 1 if a value is in a list of values.
* `LIKE`    # returns 1 if a value matches a pattern
* `NOT`	    # reverses the value of other operators such as NOT EXISTS, NOT IN, NOT BETWEEN, etc.
* `OR`	    # returns true if either expression is 1

#### BETWEEN
`<test> BETWEEN <low> AND <high>`
* *low* and *high* is any valid expression that specify the low and high values of the range
* The BETWEEN operator is inclusive: returns true when *test* is 
  * less than or equal to *high* and 
  * greater than or equal to *low*


INSERT INTO
------------

`INSERT INTO <table> (column1, column2,..) VALUES (value1, value2,...);`

You don't need to specify the columns in the query if you are adding values for all the columns of the table.  
However, make sure the order of the values is in the same order as the columns in the table.



DROP
-----------

* used to remove a table definition and all associated data, indexes, triggers, constraints, and permission specifications for that table

`DROP TABLE <db>.<table>;`

DELETE
-----------

PRAGMA
-------------

`PRAGMA table_info(<table>);`