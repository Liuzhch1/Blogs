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

## Sort and Retrieve Data
**Objective:** Using `ORDER BY` clause of the `SELECT` command to sort the retrieved data as needed.

### Sort data
To sort the data retrieved by `SELECT`, use `ORDER BY` clause. `ORDER BY` clause takes one or more columns' name and sorts the output accordingly.
```SQL
SELECT prod_name
FROM products
ORDER BY prod_name;
```
**Meaning:** The retrieved data will order by `prod_name`.

> We can also use non-retrieved columns to sort data, it's totally legal:
```SQL
SELECT prod_name
FROM products
ORDER BY price;
```

### Sort by multiple columns
Sort by multiple columns, for example: order by last name first, then order by first name. To sort multiple columns, just specify column names separated name by commas.
```SQL
SELECT prod_id, prod_price, prod_name
FROM products
ORDER BY prod_price, prod_name;
```
**Meaning:** Order by `prod_price` and `prod_name`. First order by `prod_name`, then order by `prod_name`. So only to order `prod_name` if there are multiple product has the same price. If the price of all product differs from each other, it don't need to order by `prod_name`(or it's in order already).

### Specify sort direction
To sort the data in descending order, use `DESC` keyword.
```SQL
SELECT prod_id, prod_price, prod_name
FROM products
ORDER BY prod_price DESC;
```
**Meaning:** Sort items in `prod_price` descending order.

> `DESC` only applies to columns names that precede it. So we can put those names we want to sort in ascending order after `DESC`: `ORDER BY prod_name DESC, prod_name;`

> If want to descending multiple columns, must use `DESC` for every one of them: `ORDER BY prod_name DESC, prod_name DESC;`

> Usually, MySQL sorting ignores letter case. E.g. 'A'='a' in sorting.

#### use case
Using `ORDER BY` and `LIMIT` operation to find the most expensive product:
```SQL
SELECT prod_name
FROM products
ORDER BY prod_price DESC
LIMIT 1;
```
**Meaning:** Sort product in price descending order, and only show one item. So that item is the most expensive one.

> Must put `ORDER BY` after `FROM`, and put `LIMIT` after `ORDER BY`.

## Filter Data
**Objective:** Use `WHERE` clause of the `SELECT` command to specify search criteria.

### Using WHERE clause
Usually, we just need to retrieve part of data which meet certain condition. To specify search criteria, use `WHERE` clause.
```SQL
SELECT prod_name
FROM product
WHERE prod_price=2.5;
```
**Meaning:** Only retrieve items whose price is 2.5.

> Must place `ORDER BY` after `WHERE`.

### WHERE clause operator
There are various operator for filter data:
`=`, `!=`or`<>`, `<`, `<=`, `>`, `>=`, `BETWEEN ... AND ...`

Use `NULL` to filter no value: `WHERE prod_name IS NULL`.

> Filter will ignore `NULL` if the criteria is not `IS NULL`. Because database don't know if `NULL` data fit, so just ignore them.

