# Structured Query Language and Relational Databases
## RBDMS Model
  * **Problems with file systems** - data redundancy, data inconsistency, 
  insecure data, unsharable and unstandardised data
  * **RDBMS Model**:
    * Data organised into tables called `relations`
    * Each row represents a `relationship`
    * Rows of relations called `tuples`
    * `Domain` is the pool of values from which values in a column are obtained
    * `Cardinality`: Number of tuples, `Degree`: Number of attributes
    * **KEYS**
      | Key | Use |
      |:---:|:---:|
      | Primary Key   | Set of one or more attributes that can be used to uniquely identify a tuple. If more than one attribute used, it is called `composite primary key`. |
      | Candidate Key | Attribute combinations that can serve as a primary key |
      | Alternate Key | Candidate key that is not the primary key |
      | Foreign Key   | Used to represent the relationship between two tables. Derived from the primary key of another table. |

      `Foreign Table`/`Detail Table`: Table in which FK column exists.<br>
      `Primary Table`/`Master Table`: Table to which FK attribute points to.

    * `Referential Integrity`: System of rules to ensure relationships in records are valid and 
    users don't accidentally change related data.
      * **Conditions**:
        * Matching field from primary table is the primary key and has a unique index.
        * Related fields have the same data type.
        * Both tables belong to the same database. 
      * **Rules**:
        * Cannot enter a value in FK field of foreign table that doesn't exist in primary key of 
        primary table. Use `NULL` to specify that the fields are unrelated.
        * Cannot delete a record from the primary table if matching records exist in foreign table.
        * Cannot change the PK in primary table if related records exist.

## SQL
* **Data Definition Language**
  * Commands to create and modify the database schemas, such as, creating, altering and dropping tables.
  * Schema stored in `data dictionary`.
  * Can be used to grant privileges, maintainence.
* **Data Manipulation Language**: Commands to insert, retrieve, modify and delete data stored in the database.

## SQL Queries
### DDL Queries
*
```sql
CREATE DATABASE <name>;
CREATE DATABASE IF NOT EXISTS <name>;
USE <database-name>;
SHOW TABLES;
SHOW DATABASES;
DESC <tablename>;
DROP DATABASE <name>;
```

#### `CREATE TABLE`
```sql
CREATE TABLE <name>
( attr1 <type>,
  ... );
```

To use the records of an existing table, 
```sql
CREATE TABLE pet_copy AS
(
    SELECT name, owner
    FROM pet
    WHERE sex = 'm'
);
```

##### `CONSTRAINTS`
```sql
UNIQUE 
DEFAULT '<default-val>'
CHECK (attr > 0) -- If it involves more than one column, it is specified at the end.
PRIMARY KEY
```

* FOREIGN KEY
  - `columnname datatype references tablename(columnname)
  ON DELETE CASCADE ON UPDATE CASCADE`
  <br> or at the end `FOREIGN KEY ... `.

* Custom constraint names can be given by
  ```sql
  CREATE TABLE ... (
    attr int CONSTRAINT PK_attr PRIMARY KEY,
    ...
    CONSTRAINT FK_attr2 FOREIGN KEY ...
  );
  ```
### DML Queries
#### SELECT 
* Basic select query
  ```sql
  SELECT attr1, attr3, attr2
  FROM table;
  ```
* Eliminate duplicates with `DISTINCT`
  ```sql
  SELECT DISTINCT species
  FROM pet;
  ```
* Scalar expressions
  ```sql
  SELECT attr1*100
  FROM table;
  ```
* Column Aliases
  ```sql
  SELECT attr AS "Event"
  FROM table;
  ```
* `IFNULL(<columnname>, <sub>)` can be used to replace NULL in attributes.
  ```sql
  SELECT name, birth, IFNULL(death, "alive") AS "Died On"
  FROM pet;
  ```
* We can put literal strings in the output by `SELECT attr1, ',', attr2 FROM table;`.

##### **`WHERE` clause**
  ```sql
  SELECT name, species, sex, owner
  FROM pet
  WHERE species = 'dog' AND sex = 'f';
  ```
  * When using `WHERE attr1 > 10`, the range is exclusive, use `>=` to make inclusive. 
  * `BETWEEN` clause can also be used. Here the ranges are inclusive. `NOT BETWEEN` is the opposite and is non-inclusive.
  * `||, &&, !` can also be used.
  * Conditions based on a list
    ```sql
    SELECT *
    FROM pet
    WHERE sex = 'f' AND species IN ('dog', 'cat', 'bird');
    ```
  * **Pattern matching**
    - `LIKE` used to match chars.
    - `%` matches substrings and `_` matches any character. So, `---%` is a string of atleast 3 characters.
    - The `ESCAPE` clause defines the escape character - `WHERE name LIKE '\%rd' ESCAPE '\';`.
    - `NOT LIKE` selects the records that do not match the substring.
  * NULL attributes can be found by ` attr IS (NOT) NULL`.
  * Operator Precedence:
    ```
    !<br>
    ^<br>
    *, /, %, MOD<br>
    -, +<br>
    <, ><br>
    &<br>
    |<br>
    ==, >=, >, <=..., IN BETWEEN, LIKE<br>
    NOT<br>
    &&, AND<br>
    XOR<br>
    ||, OR<br>
    ```
##### `ORDER BY`
```sql
SELECT ...
FROM ... 
WHERE ...
ORDER BY ...;
```
- `DESC` used for descending order, `ASC` vice-versa.

##### Functions (See TB at 556)

#### `INSERT`
- `INSERT INTO <tablename> [ <column list> ] VALUES ( <tuple>, <tuple>, ... );`
- To insert data from another table
  ```sql
  INSERT INTO pet_copy (...)
  SELECT ... FROM pet
  WHERE ...;
  ```
#### `UPDATE`
```sql
UPDATE <tablename>
SET attr = attr + 100 -- We can use scalar expressions
WHERE attr > 1000;
```

#### `DELETE`

```sql
DELETE FROM <tablename>
[ WHERE ... ];
```

#### `ALTER TABLE`
- **`ADD`**
  ```sql
  ALTER TABLE <tablename>
  ADD <attrname> <attr-type>
  [CONSTRAINTS ...];
  ```
- **`MODIFY`**
  - Specify only column-name and modified part. 
  - Modifying datastypes and sizes: `CHAR` -> `VARCHAR` and vice versa is possbile only when all tuples are 
  `NULL` for that attribute and we do not change the column size. We can always increase the size.
  - Constraints: **Only the `NOT NULL` constraint may be added using MODIFY**. To add other constraints use a different syntax.
  - Reordering columns:
    ```sql
    ALTER TABLE <tablename>
    MODIFY <columnname> <datatype> [ [FIRST|AFTER column] [CONSTRAINT ...] ];
    ```
- **`CHANGE`**
  - Change name of a column
  ```sql
  ALTER TABLE <tablename>
  CHANGE <old-columnname> <new-columnname> <new-definition>; -- Complete redefinition of column
  ```

- **`DROP`**
  - Drop components of a table
  ```sql
  ALTER TABLE <tablename>
  DROP PRIMARY KEY, DROP FOREGIN KEY FK_NAME, DROP COLUMN name;
  ```

#### MISC 
* To do calcs,
  ```sql
  SELECT 4*3
  FROM dual; -- Not compulsory
  ```
  `dual` is a dummy table provided by mysql.
* Get the current data - `select curdate();`.


<!--
vim: shiftwidth=2 colorcolumn=100 
-->
