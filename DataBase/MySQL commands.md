```sql
SELECT
	name, id,
	AVG(age) as avg_name
FROM user_profile
WHERE age BETWEEN 20 AND 30
GROUP BY school
LIMIT 2;
```


>Every SQL instruction should ends with `;`.
>Usually SQL keywords in upper case, columns and tables's name in lower case.
>You can divide one SQL instruction into multiple lines, it's more easier to read and debug.

## Databases and Tables
```SHOW DATABASES;```
	List a list of all databases.
```USE mydatabase;```
	Change to `mydatabase`.
```SHOW TABLES;```
	Show the list of tables inside a database which has been chosen in last command.
```SHOW COLUMNS FROM customers;``` or ```DESCRIBE customers;```
	Show the lists inside a table.


## Data Retrieval
**Objective:** Using `SELECT` command to retrieve one or more data columns from a table.

Using `SELECT` should give two piece of information: 1. what you want to choose. 2. where to choose from.
### Retrieve single column
```SQL
SELECT prod_name
FROM products;
```
**Meaning:** Using `SELECT` to choose a column called `product_name` from the table `product`.

> The order in which the data is returned has no special significance.

### Retrieve multiple columns
Column names must be separated by commas. **Attention**: there is no comma after last column name.
```SQL
SELECT prod_id, prod_name, prod_price
FROM products;
```
**Meaning:** Using `SELECT` to choose column `prod_id`, `prod_name` and `prod_price` from the table `products`.

### Retrieve all columns
In addition to specify the desired columns. We can retrieve all columns without listing them one by one. Using the asterisk`*` to get all columns.
```SQL
SELECT *
FROM products;
```
**Meaning:** Select all columns in the table.

> Use `*` only you do need it. Retrieve useless columns will degrades retrieving performance.

### Retrieve different rows
`SELECT` will return all matching rows. Using `DISTINCT` to return only rows of different value.
```SQL
SELECT DISTINC vend_id
FROM products;
```
**Meaning:** Only return rows with different `vend_id`.

> `DISTINC` applies to all columns, not just the column that precede it.
> TBD: what does this means?

### Limit results
Limit the number of result lines using `LIMIT` sentence.
```SQL
SELECT prod_name
FROM products
LIMIT 5;
```
**Meaning:** `LIMIT 5` means the returns no more than 5 rows.

```SQL
SELECT prod_name
FROM products
LIMIT 5,6;
```
**Meaning:** `LIMIT 5,6` means starting at line 5, return 6 lines. The first parameter means start position(0-indexed) and the second parameter means the number of rows to retrieve.

> If there are not enough rows, MySQL only return as many as rows as it can.

`LIMIT 5,6` == `LIMIT 5 OFFSET 6`.

### Fully qualified table names
We used only column name to refer them above. We can also use fully qualified names to refer them, which means use table name and column at the same time.
```SQL
SELECT products.prod_name
FROM products;
```
**Meaning:** Equal to [[MySQL commands#Retrieve single column|Retrieve single column]]. A fully qualified column name is specified here.

The table name is also can be qualified.
```SQL
SELECT products.prod_name
FROM mydatabase.products;
```
