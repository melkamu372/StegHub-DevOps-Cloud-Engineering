# Self Study For LEMP Stack 
1. ## Make Yourself familiar with basic *SQL Syntax and most commonly used commands*

### What is SQL?
- SQL, which stands for Structured Query Language, is a programming language designed for managing data stored in relational databases. It Used  as a special tool for communicating with these databases.

### SQL allows us to do:
- **Interact with databases**: SQL provides commands to query *(search)*, *insert*, *update*, and *delete* data within a relational database.
- **Organize and manage data**: The structured nature of SQL helps keep data organized and facilitates retrieval of specific information.
- **Standardized language**: SQL is a widely recognized standard, allowing us to work with different relational database management systems (RDBMS) without needing to learn a new language for each one.


### What are SQL Statements?
SQL statements are instructions you give to a relational database management system (RDBMS) to perform various tasks. They act like commands that the database understands and executes.

### Types of SQL Statements

SQL statements can be categorized into four main types:

**Data Query Language (DQL)**:

- Used to retrieve data from the database.
- Most common DQL statement: `SELECT` (specifies what data you want and from which tables).

**Data Manipulation Language (DML)**:

- Used to modify data within the database.
- Common DML statements:
- `INSERT`: Adds new data.
- `UPDATE`: Modifies existing data.
- `DELETE`: Removes data.

**Data Definition Language (DDL)**:

- Used to define and manipulate the structure of the database itself.
- This includes creating tables, specifying data types for columns, and defining relationships between tables.
- Common DDL statements:
- `CREATE TABLE`: Creates a new table.
- `ALTER TABLE`: Modifies an existing table.
- `DROP TABLE`: Deletes a table.

**Data Control Language (DCL)**:

- Manages access privileges and user permissions within the database for data security.
- Common DCL statements:
- `GRANT`: Gives permissions to users.
- `REVOKE`: Takes away permissions from users.


### SQL Syntax:

**Comments:**

- Single-line comments: `-- This is a single-line comment`
- Multi-line comments: `/* This is a multi-line comment */`

**Statements Termination:**

- SQL statements are typically terminated with a semicolon (`;`).

**Case Sensitivity:**

- SQL is not case-sensitive for keywords, but it's case-sensitive for data.

**Whitespace:**

- Extra spaces and line breaks are generally ignored in SQL queries for readability.

### Basic SQL Syntax:

SQL statements follow a specific structure that the database understands. Here's a general breakdown:

```sql
SQL
KEYWORD [options] table_name [column_list]
WHERE condition
ORDER BY column_name [ASC | DESC]
LIMIT number;
```
- **KEYWORD**: This specifies the type of operation you want to perform. Examples include SELECT, INSERT, UPDATE, and DELETE.
- **[options]**: Optional options that can modify the base functionality of the keyword.
table_name: The name of the table you're working with.
- **[column_list]**: (For SELECT statements) Specifies the columns you want to retrieve data from. If omitted, all columns are returned.
- **WHERE condition**: (Optional) Used to filter data based on a specific criteria.
- **ORDER BY column_name [ASC | DESC]**: (Optional) Sorts the results based on a particular column in ascending (ASC) or descending (DESC) order.
- **LIMIT number**: (Optional) Limits the number of returned rows.

### Some of The Most Important SQL Commands with Explanation 
1. **SELECT**: 
- Used to retrieve data from one or more tables in a database.
- Syntax: 
```sql
SELECT column1, column2, ...
FROM table_name;
```

2. **UPDATE**: 
- Modifies existing data in a table.
- Syntax: 
```sql
UPDATE table_name
SET column1 = value1, column2 = value2, ...
WHERE condition;
```

3. **DELETE**: 
- Removes rows from a table based on a specified condition.
- Syntax: 
```sql
DELETE FROM table_name
WHERE condition;
```

4. **INSERT INTO**: 
- Adds new rows of data into a table.
- Syntax: 
```sql
INSERT INTO table_name (column1, column2, ...)
VALUES (value1, value2, ...);
```

5. **CREATE DATABASE**: 
- Creates a new database.
- Syntax: 
```sql
CREATE DATABASE database_name;
```

6. **ALTER DATABASE**: 
- Modifies the structure of an existing database.
- Syntax: 
```sql
ALTER DATABASE database_name
MODIFY NAME = new_database_name;
```

7. **CREATE TABLE**: 
- Creates a new table with specified columns and constraints.
- Syntax: 
```sql
CREATE TABLE table_name (
column1 datatype constraint,
column2 datatype constraint,
...
);
```

8. **ALTER TABLE**: 
- Modifies an existing table's structure.
- Syntax: 
```sql
ALTER TABLE table_name
ADD column_name datatype constraint;
```

9. **DROP TABLE**: 
- Deletes an entire table and its data from the database.
- Syntax: 
```sql
DROP TABLE table_name;
```

10. **CREATE INDEX**: 
- Creates an index on a table, which improves the speed of data retrieval operations.
- Syntax: 
```sql
CREATE INDEX index_name
ON table_name (column1, column2, ...);
```

11. **DROP INDEX**: 
- Deletes an existing index from a table.
- Syntax: 
```sql
DROP INDEX index_name;
```

---
2. ## Be Comfortable Using not only VIM, But also Nano editor as well, get to Know Basic Nano Commands 

### What is Nano mean?
Nano is a free and open-source text editor designed for Unix-like operating systems, including Linux and macOS. It's known for being user-friendly and a good option for beginners who find more powerful editors like Vim intimidating.

### Basic Nano Commands

1. **Opening a File**: 
- To open a file using Nano, simply type `nano` followed by the file name: 
```
nano filename
```

2. **Saving a File**: 
- To save a file in Nano, press `Ctrl + O`. Nano will prompt you to confirm the file name. Press `Enter` to confirm, then press `Ctrl + X` to exit Nano.

3. **Exiting Nano**: 
- To exit Nano, press `Ctrl + X`. If there are unsaved changes, Nano will prompt you to save the changes before exiting.

4. **Moving the Cursor**: 
- You can move the cursor using the arrow keys on your keyboard.

5. **Cut, Copy, and Paste**: 
- To cut a line, press `Ctrl + K`.
- To copy a line, press `Alt + 6` (hold down `Alt` and press `6`).
- To paste the cut or copied text, move the cursor to the desired location and press `Ctrl + U`.

6. **Search and Replace**: 
- To search for text in Nano, press `Ctrl + W`, then type the text you want to search for and press `Enter`.
- To replace text, press `Ctrl + \`, then type the text to search for, followed by the replacement text, and press `Enter`.

7. **Undo and Redo**: 
- To undo the last action, press `Ctrl + _` (underscore).
- To redo the last undone action, press `Alt + U`.

8. **Help**: 
- To access the Nano help menu, press `Ctrl + G`. This will display a list of Nano commands and shortcuts.



## References
1. [W3 SQL Tutorial](https://www.w3schools.com/sql/sql_syntax.asp)
2. [Tpoint Tech SQL Tutorial](https://www.javatpoint.com/sql-tutorial)
3. [Geeks for Geeks Nano Text Editor](https://www.geeksforgeeks.org/nano-text-editor-in-linux/)