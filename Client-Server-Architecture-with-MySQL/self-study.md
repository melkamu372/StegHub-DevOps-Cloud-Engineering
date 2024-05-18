# Self Study
1. Read about ping and traceroute network diagnostic utilities. Be able to make sense out of the results of using these tools.

# Ping and Traceroute: Network Diagnostic Utilities

## Ping

**Ping** is a network utility used to test the reachability of a host on an IP network and to measure the round-trip time for messages sent from the originating host to a destination computer.

**How Ping Works:**
1. **ICMP Echo Request/Reply:** Ping sends Internet Control Message Protocol (ICMP) Echo Request messages to the target host and waits for an ICMP Echo Reply.
2. **Round-Trip Time (RTT):** It measures the time taken for the round-trip from the originating host to the destination and back.

**Interpreting Ping Results:**
- **Reply from [IP Address]:** Indicates the host is reachable.
- **Request timed out:** No response was received, possibly due to network issues or the host being down.
- **Packets sent/received/lost:** Helps determine packet loss.
- **RTT (ms):** Shows minimum, maximum, and average round-trip times, indicating latency.

**Example Ping Output:**



## Traceroute

**Traceroute** is a network diagnostic tool used to track the path that packets take from one IP address to another. It provides information on each hop the packet passes through to reach the destination.

**How Traceroute Works:**
1. **ICMP/TCP/UDP Probes:** Traceroute sends a sequence of packets with incrementally increasing Time-To-Live (TTL) values.
2. **TTL Exceeded:** Each router that forwards the packet decreases the TTL by one. When the TTL reaches zero, the router discards the packet and sends an ICMP "Time Exceeded" message back to the source.
3. **Tracing the Path:** By recording the source of each "Time Exceeded" message, Traceroute determines the path taken by the packets to the destination.

**Interpreting Traceroute Results:**
- **List of Hops:** Each line represents a hop from the source to the destination.
- **IP Address and Hostnames:** Shows the IP address and, if resolvable, the hostname of each hop.
- **RTT for Each Hop:** Provides round-trip times for each hop, often three times per hop.

**Example Traceroute Output:**


**Key Points:**
- **Hop Count:** The number of hops (intermediate routers) to reach the destination.
- **Latency per Hop:** Helps identify where delays are occurring in the path.
- **Unreachable Nodes:** If a hop consistently fails to respond, it might indicate a network problem at that hop.

> Understanding how to use and interpret the results from Ping and Traceroute,help diagnose and troubleshoot network issues effectively.


2. Refresh your knowledge of basic SQL commands, be able to perform simple SHOW, CREATE, DROP, SELECT and INSERT SQL queries.
SQL stands for Structured Query Language. SQL commands are the instructions used to communicate with a database to perform tasks, functions, and queries with data.

SQL commands can be used to search the database and to do other functions like creating tables, adding data to tables, modifying data, and dropping tables.

Here is a list of basic SQL commands (sometimes called clauses) you should know if you are going to work with SQL.
# Basic SQL Commands

## SHOW

The `SHOW` command is used to display database objects and their properties. It can show tables, databases, columns, and more.

**Examples:**
- **Show all databases:**
```
SHOW DATABASES;
```
- **Show all tables in the current database:**
```
SHOW TABLES;
```
- **Show columns of a specific table**:

```
SHOW COLUMNS FROM table_name;
```
### CREATE
The CREATE command is used to create databases, tables, views, and other database objects.

**Examples**:

- **Create a new database**:
```
CREATE DATABASE database_name;
```
- **Create a new table**:
```
CREATE TABLE table_name (
    column1_name data_type constraints,
    column2_name data_type constraints,
    ...
);
```
**Example**:
```
CREATE TABLE employees (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    position VARCHAR(50),
    salary DECIMAL(10, 2)
);
```
### DROP
The DROP command is used to delete databases, tables, or other database objects.

**Examples**:

- **Drop a database**:
```
DROP DATABASE database_name;
```
- **Drop a table**:
```
DROP TABLE table_name;
```

### SELECT
The SELECT command is used to query data from one or more tables.

**Examples**:

- *Select all columns from a table:*
```
SELECT * FROM table_name;
```
- *Select specific columns from a table*:
```
SELECT column1_name, column2_name FROM table_name;
```

- *Select with a condition*:
```
SELECT * FROM table_name WHERE condition;
```
Example:
```
SELECT name, position FROM employees WHERE salary > 50000;
```
### INSERT
The INSERT command is used to add new rows of data into a table.

**Examples**:

- Insert a single row:
```
INSERT INTO table_name (column1_name, column2_name, ...)
VALUES (value1, value2, ...);
```
**Example**:
```
INSERT INTO employees (name, position, salary)
VALUES ('John Doe', 'Manager', 75000);
```
- Insert multiple rows:

```
INSERT INTO table_name (column1_name, column2_name, ...)
VALUES 
(value1, value2, ...),
(value3, value4, ...),
...;
```

**Example**:
```
INSERT INTO employees (name, position, salary)
VALUES 
('Jane Smith', 'Developer', 60000),
('Mike Johnson', 'Analyst', 55000);
```

### Practical Example

**Let's combine these commands in a practical scenario**:

1. Show existing databases:

```
SHOW DATABASES;
```
2.  Create a new database:
```
CREATE DATABASE company;
```
3. Switch to the new database:
```
USE company;
```
4. Create a new table:
```
CREATE TABLE employees (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    position VARCHAR(50),
    salary DECIMAL(10, 2)
);
```
5. Insert data into the table:
```
INSERT INTO employees (name, position, salary)
VALUES ('Alice Brown', 'CEO', 120000);
```
6. Query the table:
```
SELECT * FROM employees;
```
7. Drop the table:
  ```
  DROP TABLE employees
  ```
## References
1. [Cloud Direct Using Traceroute, Ping, MTR, and PathPing](https://www.clouddirect.net/knowledge-base/KB0011455/using-traceroute-ping-mtr-and-pathping)
2. [Cisco Understand the Ping and Traceroute Commands](https://www.cisco.com/c/en/us/support/docs/ios-nx-os-software/ios-software-releases-121-mainline/12778-ping-traceroute.html)
3. [Basic SQL Commands - The List of Database Queries and Statements You Should Know](https://www.freecodecamp.org/news/basic-sql-commands/)