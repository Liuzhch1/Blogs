```MySQL
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
```MySQL
SELECT prod_name
FROM products;
```
**Meaning:** Using `SELECT` to choose a column called `product_name` from the table `product`.

> The order in which the data is returned has no special significance.

### Retrieve multiple columns
Column names must be separated by commas. **Attention**: there is no comma after last column name.
```MySQL
SELECT prod_id, prod_name, prod_price
FROM products;
```
**Meaning:** Using `SELECT` to choose column `prod_id`, `prod_name` and `prod_price` from the table `products`.

### Retrieve all columns
In addition to specify the desired columns. We can retrieve all columns without listing them one by one. Using the asterisk`*` to get all columns.
```MySQL
SELECT *
FROM products;
```
**Meaning:** Select all columns in the table.

> Use `*` only you do need it. Retrieve useless columns will degrades retrieving performance.

### Retrieve different rows
`SELECT` will return all matching rows. Using `DISTINCT` to return only rows of different value.
```MySQL
SELECT DISTINC vend_id
FROM products;
```
**Meaning:** Only return rows with different `vend_id`.

> `DISTINC` applies to all columns, not just the column that precede it.
> TBD: what does this means?

### Limit results
Limit the number of result lines using `LIMIT` sentence.
```MySQL
SELECT prod_name
FROM products
LIMIT 5;
```
**Meaning:** `LIMIT 5` means the returns no more than 5 rows.

```MySQL
SELECT prod_name
FROM products
LIMIT 5,6;
```
**Meaning:** `LIMIT 5,6` means starting at line 5, return 6 lines. The first parameter means start position(0-indexed) and the second parameter means the number of rows to retrieve.

> If there are not enough rows, MySQL only return as many as rows as it can.

`LIMIT 5,6` == `LIMIT 5 OFFSET 6`.

### Fully qualified table names
We used only column name to refer them above. We can also use fully qualified names to refer them, which means use table name and column at the same time.
```MySQL
SELECT products.prod_name
FROM products;
```
**Meaning:** Equal to [[MySQL commands#Retrieve single column|Retrieve single column]]. A fully qualified column name is specified here.

The table name is also can be qualified.
```MySQL
SELECT products.prod_name
FROM mydatabase.products;
```

## Sort and Retrieve Data
**Objective:** Using `ORDER BY` clause of the `SELECT` command to sort the retrieved data as needed.

### Sort data
To sort the data retrieved by `SELECT`, use `ORDER BY` clause. `ORDER BY` clause takes one or more columns' name and sorts the output accordingly.
```MySQL
SELECT prod_name
FROM products
ORDER BY prod_name;
```
**Meaning:** The retrieved data will order by `prod_name`.

> We can also use non-retrieved columns to sort data, it's totally legal:
```MySQL
SELECT prod_name
FROM products
ORDER BY price;
```

### Sort by multiple columns
Sort by multiple columns, for example: order by last name first, then order by first name. To sort multiple columns, just specify column names separated name by commas.
```MySQL
SELECT prod_id, prod_price, prod_name
FROM products
ORDER BY prod_price, prod_name;
```
**Meaning:** Order by `prod_price` and `prod_name`. First order by `prod_name`, then order by `prod_name`. So only to order `prod_name` if there are multiple product has the same price. If the price of all product differs from each other, it don't need to order by `prod_name`(or it's in order already).

### Specify sort direction
To sort the data in descending order, use `DESC` keyword.
```MySQL
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
```MySQL
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
```MySQL
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

## Data Filtering
High level filter criteria with `WHERE`.  And `NOT` and `IN`.

### Combined WHERE clause
Use `AND` and `OR` operator to juxtapose multiple filter conditions.
```MySQL
...
WHERE vend_id=100 AND prod_price<=10;
```

Use `IN` for a filter range:
```MySQL
...
WHERE prod_price IN (10,20);
```

Use `NOT` to negate any condition following it.
```MySQL
...
WHERE prod_price NOT IN (20,100);
```

## Filter With Wildcards
Use `LIKE` operation to search.
> ***wildcard:*** A special character used to match a portion of a value
> ***search pattern:*** Search criteria consist of literals, wildcards or their combination.

To use wildcard, must use `LIKE` operation.
### Percent sign(`%`) wildcard
`%` indicate any character appear any times(0,1 or any more time).
```MySQL
...
WHERE prod_name LIKE "Jet%";
```
Filter any string start with `Jet`.
> Search is case sensitive. `"Jet%"` doesn't match `jetbrian`.

> `%` don't match `NULL`.

### Underline(`_`) wildcard
Similar to `%`, but `_` only match exactly one character.

> Wildcard is time time-consuming. Use them only when do needed.

## Search With Regular Expressions
**Objective:** Using `WHERE` with regular expressions to satisfy complex filter.

### Using MySQL regular expressions
> MySQL only support a small subset of regular expression.

#### basic regular expression
```MySQL
...
WHERE prod_name REGEXP '.000'
```
**Meaning:** `.` match any character. `'.000'` can match `1000`, `2000`, `a000` etc.

> **Important Difference:**
> `LIKE` match the whole line. If the matched text appears **in** the column value, `LIKE` won't return it.
> `REGEXP` match anything in the column value.

> **MySQL regular expression ignore case**. To be case sensitive, use `BINARY` keyword: `WHERE prod_name REGEXP BINARY 'JetBrain'`.

#### OR matching
Search one of two regular expression strings, using `|`.
```MySQL
...
WHERE prod_name REGEXP '1000|2000';
```
**Meaning:** It will match `'1000'` or `'2000'`. `|` is functionally similar to using `OR` statement.

#### match several characters
Using `[...]` to match those character want to match.
```MySQL
...
WHERE prod_name REGEXP '[123]Ton';
```
**Meaning:** It will match `1Ton`, `2Ton` and `3Ton`.

> Using `^` to negate anything after it: `'[^123]'` won't match `1` or `2` or `3`.

#### matching range
Using `-` to define a range.
```MySQL
...
WHERE prod_name REGEXP '[1-5]Ton';
```
**Meaning:** `'[1-5]'` will match number 1 to 5.

> [a-z] match all letters.

#### match special characters
Using `\\` to match any special characters like `[`, `.`, `-` etc.
```MySQL
WHERE vend_name REGEXP '\\.';
```
**Meaning:** Match column value which contains `.` This `\\` expression is called *escaping*.

Other special meaning escaping:
`\\n`: line feed
`\\r`: enter
`\\t`: tab
`\\f`: page change
`\\v`: longitudinal tab

#### match character class
`[:alnum:]`: any letter and number, equal to `[a-zA-Z0-9]`.
`[:alpha:]`: any letter, equal to `[a-zA-Z]`.
`[:blank:]`: space and tab.
`[:digit:]`: any number, equal to `[0-9]`.
`[:lower:]`: any lower case letters, equal to `[a-z]`.
`[:upper:]`: any upper case letters, equal to `[A-Z]`.
`[:space:]`: any white space character including space, equal to `[\\f\\n\\r\\t\\v]`.
`[:xdigit:]`: any hex number, equal to `[a-fA-F0-9]`.

#### match multiple instance
Those regular expression above only match a single occurrence. If want to match some string specific times, use following repeat match character.
`*`: 0 or more
`+`: 1 or multiple
`?`: 0 or 1
`{n}`: n times
`{n,}`: not less than n times
`{n,m}` n<times<m. Where m<=255.
```MySQL
...
WHERE prod_name REGEXP '[[:digit:]]{4}';
```
**Meaning:** Match any digit 4 times. `[:digit:]` is a set. So we need to put it in a `[]` and let `{4}` match it 4 times.

#### locator
Those regular expression above matching at any position in the string. To match specified location string. Use following locator.
`^`: start of text.
`$`: end of text.
`[[:<:]]`: start of a word.
`[[:>:]]`: end of a word
```MySQL
...
WHERE prod_name REGEXP '^[0-9]';
```
**Meaning:** Match any column value that start with a digit.

> Test your regular expression: `SELECT 'hello' REGEXP '[0-9]'`, which means match digit in string 'hello', it will return false.
> This `REGEXP` test only return 1 for success and 0 for failed.

## Create Calculated Field
**Objective:** Return the field in specified format. Not stored in the database.

### Concatenate field
Use `Concat()` to concatenate multiple column.
```MySQL
SELECT Concat(vend_name, '(', vend_country, ')')
...
```
**Meaning:** Concatenate `vend_name`, '(', `vend_country` and ')' together.
It will return something like: `Apple(USA)`, `XiaoMi(China)` etc.

#### remove space(` `)
Use `RTrim()` to remove space` ` at the right side of column value. `LTrim()` to remove space` ` at the left side of column value.
```MySQL
SELECT Concat(RTrim(vend_name), '(', RTrim(vend_country), ')')
...
```
**Meaning:** Remove right ` ` of `vend_name` and `vend_country`.

#### use alias
Use `AS` to given field a new name.
```MySQL
SELECT Concat(.......) AS vend_title
...
```
**Meaning:** After `Concat` operation, `AS` will name it as `vend_title`.

> Use `SELECT` to test:
> `SELECT 3*2` return 6.
> `SELECT Trim(' bag ')` return `'bag'`. 
> `SELECT Now()` return current date and time.

## Data Processing Functions
> Functions in SQL is not quilt portable. Which mean some function works in MySQL won't work on other `DBMS`. So write comments for function you used.

### Text processing function
Use `Upper()` function to turn all text to upper case.
```MySQL
SELECT Upper(vend_name) AS vend_name_Upper
...
```
**Meaning:** Select vend_name and turn all vend_name column value to upper case.

#### useful text processing func
`Left()`: return the character to the left of string.
`Right()`: return the character to the right of string.
`Length()`: return the length of a string.
`Lower()`: turn to lower case.
`Soundex()`: return SOUNDEX value.
> SOUNDEX value means return any string that sounds alike. Like `Lie` and `Lee`.

```MySQL
...
WHERE Soundex(cust_contact) = Soundex('Lie');
```
**Meaning:** Will return `Lee` because `Lee` and `Lie` sound alike.

### Date and time handler
`AddDate()`: Add a date: day or week, etc.
`AddTime(）`: Add a time: hour or min, etc.
`CurDate()`: Return current date.
`CurTime()`: Return current time.
`Date()`: Return date part of dateAndTime.
`DateDiff()`: Difference between two date.
`Date_Add()`: Any add.
`Date_Format()`: Formated date.
`Day()`: Day part of a date.
`Dayofweek() Hour()`: Corresponding day of the week.
`Minute()`: Minute part of the time.
`Month()`: Month part of the date.
`Now()`: Date and time.
`Second()`: Second part of time.
`Time()`: Time part of date.
`Year()`: Year part of date.

> **Important:** Date format must be `yyyy-mm-dd`.

```MySQL
...
WHERE Date(order_data) = '2022-5-14';
```

```MySQL
WHERE Date(order_date) BETWEEN '2022-02-01' AND '2022-02-28';
```

```MySQL
WHERE Year(order_date)=2022 AND Month(order_date)=2;
```

### Numerical processing func
`Abs()`
`Cos()`
`Exp()`
`Mod()`
`Pi()`
`Rand()`
`Sin()`
`Sqrt()`
`Tan()`

## Summary Data
MySQL can summary data to retrieve them. Like:
- Number of rows in the table (or number of rows that meet a condition)
- Sum of the row groups in the table.
- Find max, min, average, etc.
Those operation above need to summary data in the table. There are five *aggregate function* for summary. They are:
`AVG()`: return average value of a column
`COUNT()`: return the number of rows of a column
`MAX()`: return the max value of a column
`MIN()`: return the min value of a column
`SUM()`: return the sum of a column

#### AVG()
```MySQL
SELECT AVG(prod_price) AS avg_price
...
WHERE vend_name = 'Apple';
```
**Meaning:** Return the value of average of `prod_price` from `Apple`.

> `AVG()` ignore NULL value line.

#### COUNT()
```MySQL
SELECT COUNT(cust_email) AS num_cust
...
```
**Meaning:** Return the rows which `cust_email` is not NULL.

> `COUNT(*)` count the rows even which value is NULL.
> `COUNT(column)` count valued rows, ignore NULL value.

#### SUM()
```MySQL
SELECT SUM(item_price*quantity) AS total_price
FROM orderitems
WHERE order_num=20005;
```
**Meaning:** Return the sum of prices of all items in the order.

> `SUM()` ignore NULL value.

### Summary distinct data
Use `DISTINCT` to summary distinct data.
```MySQL
SELECT AVG(DISTINCT prod_price) AS avg_prive
...
```
**Meaning:** Return the average price of price different product.

## Grouping Data
Using `GROUP BY` and `HAVING` clause to group a subset of a table.

### Create group
```MySQL
SELECT vend_id, COUNT(*) AS num_prods
FROM products
GROUP BY vend_id;
```
**Meaning:** `GROUP BY` means calculate `num_prods` for each different `vend_id`.

> The column in after `GROUP BY` must be a column. It can not be a function result: `GROUP BY SUM(*)` is invalid. And can not be alias.

> `GROUP BY` must appear after `WHERE` clause, before `ORDER BY` clause.

### Filter grouping
```MySQL
...
GROUP BY cust_id
HAVING COUNT(*)>=2;
```
**Meaning:** `HAVING` filters those with `COUNT(*)>2`.

> `HAVING` and `WHERE` difference: `WHERE` filters before data grouping. `HAVING` filters after grouping. So `WHERE` excluded rows are not included in the grouping. This may change the calculated values. Affecting  the grouping filtered based on `HAVING`. Example as follows:

```MySQL
...
SELECT vend_id, COUNT(*) AS num_prods
FROM products
WHERE prod_price >= 10
GROUP BY vend_id
HAVING COUNT(*) >= 2;
```
**Meaning:** `WHERE` filter those `prod_price>=10` and **then** group by `vend_id`. The `HAVING` clause filters group count >=2. This means that if the prices of three products of a vendor are 8, 9 and 10. Product of price 8 and 9 will be filtered out by `WHERE`. Then there is only one line left after `GROUP`, which will be filtered out by `HAVING`. Because `HAVING` means the group's rows number must >=2.

### SELECT's clause order
```MySQL
SELECT
FROM
WHERE
GROUP BY
HAVING
ORDER BY
LIMIT
```

## Use Subquery
Subquery is the queries nested in other queries.
```MySQL
SELECT DISTINCT cust_id
FROM orders
WHERE order_num IN (SELECT order_num
					FROM orderitems
					WHERE prod_id='TNT2');
```
**Meaning:** Always look at the contents in parentheses first. It means choose `order_num` where the `prod_id` is "TNT2". Using this query's result, choose `cust_id` from `orders` table where the `order_num` in that query.

### Use subqueries as calculated fields
In addition to the nested queries above. Another way to use subqueries is to create calculated fields.

Now we need to display the total number of orders for each customer in the `customers` table. The order and corresponding customer ID stored in the `orders` table.
```MySQL
SELECT cust_name,
	   cust_state,
	   (SELECT COUNT(*)
	    FROM orders
	    WHERE orders.cust_id=customers.cust_id) AS orders
FROM customers
ORDER BY cust_name;
```
**Meaning:** The `orders` is a calculated fields, the content inside parentheses means select rows from `orders` table where the `cust_id` matches `cust_id` in the `customers` table. Which uses [[#Fully qualified table names]].

## Join Table
> **foreign key:** A column in a table, which contains the primary key value of another table and defines the relationship between two table.

### Create join
Just specify all the table to join and how they are related.
```MySQL
SELECT vend_name, prod_name, prod_price
FROM vendors, products
WHERE vendors.vend_id=products.vend_id
ORDER BY vend_name, prod_name;
```
**Meaning:** It's markable that `vend_name` and `prod_name` are not in the same table. `FROM` gives two table. They are the table to join. Using `WHERE` clause to join them. The `WHERE` clause indicates MySQL matching `vend_id` in table `vendors` and `vend_id` in table `products`.

#### `WHERE` clause importance
The relations between tables are constructed in operations. There doesn't exist anything instruct MySQL how to join tables, unless you command it.
Without `WHERE` clause in above example, MySQL will pair each row in first table with each row in  the second table, which is a **cartesian product**.

#### inner join
The join above is called `equijoin` which based on equality test between two tables. There is another way to specify.
```MySQL
SELECT vend_name, prod_name, prod_price
FROM vendors INNER JOIN products
ON vendors.vend_id=products.vend_id;
```
**Meaning:** The join condition is given by `ON` clause. The condition passed to `ON` is equal to `WHERE`.

> `WHERE` or `INNER JOIN...ON`?
> Prefer `INNER JOIN...ON`.

#### join multiple tables
There is no limit of number of tables can be joined.
```MySQL
...
FROM orderitems, products, vendors
WHERE products.vend_id=vendors.vend_id
AND orderitems.prod_id=products.prod_id
AND order_num=2004;
```
**Meaning:** Using `AND` in `WHERE` to join multiple tables.

## Create Advanced Join
### Use table alias
There are two reasons for SQL allows aliasing of table names.
- Shorten SQL statements
- Allowed to use the same table multiple times in a single select statement.
```MySQL
SELECT cust_name, cust_contact
FROM customers AS cst, orders AS od, orderitems AS odi
WHERE cst.cust_id=od.cust_id
	AND odi.order_num=od.order_num
	AND prod_id='TNT2';
```
**Meaning:** Shorten `customers` as `cst`. Ellipsis.

### Use different types of joins
#### self join
Find every product from a vendor which product a specific product:
```MySQL
SELECT p1.prod_id, p1.prod_name
FROM products AS p1, products AS p2
WHERE p1.vend_id=p2.vend_id
	AND p2.prod_id='TNT2';
```
**Meaning:** The two table required in this query are actually the same table. Do a self join to choose data we need. Use alias so that SQL know the difference between the two table.

#### natural join
Whenever we join tables, as least one column should appear in more than one table. The [[#inner join]] return all the data, so it the same column may appear multiple times. `natural join` excludes multiple occurrences, so that each column returns only once.
`natural join` should be done by ourselves.
```MySQL
SELECT c.*, o.order_num, o.order_date, oi.prod_id
FROM customers AS c, orders AS o, orderitems AS oi
WHERE c.cust_id=o.cust_id
	AND oi.order_num=o.order_num
	AND prod_id='FB';
```
**Meaning:** Only select different column from different tables.

#### outer join
Sometimes we want to associate two tables, if some rows in table one is not in table two, but we want to associate it. How to implement this? Likes following situation.
- Count the number of orders placed by each customer, including those that have not yet placed orders.
In the situation above, some customers may no appear in the customers table, but we want to includes it in the joined table.
```MySQL
SELECT customers.cust_id, orders.order_num
FROM customers LEFT OUTER JOIN orders
	ON customers.cust_id=orders.cust_id;
```
**Meaning:** `LEFT` means the joined table will include all rows in the left table. `RIGHT` will include the right tables' rows.

### Use joins with aggregation functions
```MySQL
SELECT customers.cust_name,
	   customers.cust_id,
	   COUNT(orders.order_num) AS num_ord
FROM customers LEFT OUTER JOIN orders
	ON customers.cust_id=orders.cust_id
GROUP BY customers.cust_id;
```
**Meaning:** Using left outer join to connect two tables and then group the rows by customers, count each customers `order_num` as `num_ord`.

## Combined Query
We can use `UNION` to execute multiple queries(`SELECT`), and return result as a single query result.
```MySQL
SELECT vend_id, prod_id, prod_price
FROM products
WHERE prod_price <=5
UNION
SELECT vend_id, prod_id, prod_price
FROM products
WHERE vend_id IN (1001,1002);
```
**Meaning:** The first three lines choose the rows where `prod_price<=5` and the last three lines choose the rows where `vend_id` is 1001 or 1002. Then `UNION` operator union the results of two queries(Duplicate lines appear only once).

### Rules of UNION
- Every query must contains the same columns, expressions or aggregate function(but different columns don't need to be listed as the same order).
- Column data types must be compatible: type do not have to be identical, but must be the types that the DBMS can be implicitly convert.
- Union automatically removes duplicate rows from the query result set. If do need show duplicate rows, use `UNION ALL`  instead.

### Sort combined query results
When using `UNION`, only one `ORDER BY` clause can be used and it must appear after the last select statement.
```MySQL
SELECT vend_id, prod_id, prod_price
FROM products
WHERE prod_price <= 5
UNION
SELECT vend_id, prod_id, prod_price
FROM products
WHERE vend_id IN (1001,1002)
ORDER BY vend_id, prod_price;
```
**Meaning:** Order the rows returned by union operation.

## Ch18 Full Text Search
> Not all engines support full text search. `MyISAM` supports, but `InnoDB` doesn't.

### Use full text search
In order to perform full text search, the searched columns must be indexed and re-indexed as the data changes. After properly designing the table columns, MySQL will automatically perform all indexing and re-indexing.
After indexing, using `Match()` and `Against()` with `SELECT` to search.

#### enable full text search when creating table
`CREATE TABLE` statement accepts a comma separated list of indexed columns.
```MySQL
CREATE TABLE productnotes
(
	note_id int NOT NULL AUTO_INCREMENT,
	prod_id char(10) NOT NULL,
	note_date datetime NOT NULL,
	note_text text NULL,
	PRIMARY KEY(note_id),
	FULLTEXT(note_text)
)ENGINE=MyISAM;
```
**Meaning:** Create a table that indexing column `note_text`.

After definition, MySQL will automatically maintains the index. Update the index when adding, deleting or updating.

> Don't use `FULLTEXT` when importing data. All data should imported first and then define `FULLTEXT`(and MySQL indexing). Otherwise it's takes many times to reindexing when importing data.

#### perform a full text search
Using `Match()` to specify the columns want to search and using `Against()` to specify the search expression.
```MySQL
SELECT note_text
FROM productnotes
WHERE Match(note_text) Against('rabbit');
```
**Meaning:** Search columns `note_text`'s value where contains `'rabbit'`.

> The result search by `Match()` and `Against()` has ranks. In the above example, if `rabbit` appeared in the 3-rd word in row1 and appeared in the 8-th word in row2, then row1 will returned above row2.
> Rank is calculated by MySQL according to the number of words in a row, the number of unique words and total number of words in the entire index and the number of rows containing the word.
> `LIKE` operation don't do ranking.

#### use query extensions
Above query do exactly search, while query extensions will return the row related to the result row(s).
- First do normal full text search, find all row matches.
- Then, MySQL check those rows and select some 'useful' words
- Finally, do full text search and return rows contains those 'useful' words (and the original word).
```MySQL
SELECT note_text
FROM productnotes
WHERE Match(note_text) Against('anvils' WITH QUERY EXPANSION);
```
**Meaning:** It will return more rows then `...Against('anvils');`.

#### boolean text search
`boolean mode` contains:
- words to match
- word to exclude(whenever a line contains this word, then won't return this line even it contains words to match)
- ranking tips(some words are more important than other word)
- expression group
```MySQL
SELECT note_text
FROM productnotes
WHERE Match(note_text) Against('heavy -rope*' IN BOOLEAN MODE);
```
**Meaning:** Match `'heavy'` but does not contain any word that begin with `rope`.

**Full text search Boolean operator:**
`+`: conclude and must exists
`-`: excludes and must not exists
`>`: conclude and increase rank
`<`: conclude and decrease rank
`()`: make phrases into subexpressions(allow those subexpressions to be included, excluded, etc)
`~`: cancel the sorting value of a word 
`*`: wildcard at ending
`""`: define a phrase(differ from single word, it match the whole phrase)
```MySQL
WHERE Match (note_text) Against('rabbit bait' IN BOOLEAN MODE)；
```
**Meaning:** contain `'rabbit'` or `'bait'`. At least one.

```MySQL
WHERE Match (note_text) Against('+rabbit +bait' IN BOOLEAN MODE)；
```
**Meaning:** contain both `'rabbit'` and `'bait'`.

```MySQL
WHERE Match (note_text) Against('"rabbit bait"' IN BOOLEAN MODE)
```
**Meaning:** contain the whole phrase `"rabbit bait"`.

```MySQL
WHERE Match (note_text) Against('>rabbit <carrot' IN BOOLEAN MODE)；
```
**Meaning:** increase the `'rabbit'` rank and decrease `'carrot'` rank.

```MySQL
WHERE Match (note_text) Against('+safe +(<combination)'IN BOOLEAN MODE)；
```
**Meaning:** search `'safe'` and `'combination'`, decrease the `'combination'`'s rank.

> There are some `stopword` in MySQL, which means they will be ignored when full text searching. It can be override.
> 
> If a word appear more than 50%, it will be ignored unless using `IN BOOLEAN MODE`. So in a two rows or one row table, full text search won't return results(reached 50% or not appear).
> 
> Ignore the single quote `'`, so `don't` indexed as `dont`
> 
> Language without separators(Chinese, Japanese, etc) cannot properly return the full text search result.

## Ch19 Insert Data
Using `INSERT` clause to insert data into table.

### Insert a complete row
```MySQL
INSERT INTO Customers
VALUES(NULL,
	   'Pep E. Lapew',
	   'Los Angles',
	   'USA',
	   NULL);
```
**Meaning:** Insert a new row with all column values specified. If some columns has no value, using `NULL`. The first column `cust_id` is `NULL` because that column will increase automatically by MySQL when inserting new row.

Above usage is easy but not suggested. The safer is:
```MySQL
INSERT INTO customers(
	cust_name,
	cust_city,
	cust_country,
	cust_email)
VALUES(
	'Pep E. Lapew',
	'Los Angles',
	'USA',
	NULL);
```
**Meaning:** Specify the each column's value precisely. **Advantage:** Even the table struct changes(column exchanges, etc.), this `INSERT` work well. And not all columns need to specify(so default to `NULL`).

> To omit some columns, those columns must allow `NULL` value. And give default value when definition.

`INSERT` is usually slow, to handle `SELECT` first, using `LOW_PRIORITY`
```MySQL
INSERT LOW PRIORITY INTO
```

### Insert multiple rows
We can use multiple `INSERT INTO`, each insert operation ends with a `;`. Or we can use a tuple in which contains multiple data sets as long as the column names and order are the same:
```MySQL
INSERT INTO customers(
	  cust_name,
	  cust_address,
	  cust_city,
	  cust_state,
	  cust_country)
VALUES(
	  'Pep E. LaPew',
	  '100 Main Street',
	  'Los Angeles',
	  'CA',
	  'USA'
),
	  (
	  'M. Martian',
	  '42 Galaxy Way',
	  'New York',
	  'NY',
	  'USA'
);
```
**Meaning:** Insert multiple rows with multiple data sets. Each set of values is enclosed by a pair of parentheses and separated by commas. Finally ends with a semicolon.

### Insert retrieved data
We can also insert data that is from `SELECT` operation.
```MySQL
INSERT INTO customers(
	cust_contact,
	cust_email,
	cust_name,
	cust_address,
	cust_city)
SELECT cust_contact,
	cust_email,
	cust_name,
	cust_address,
	cust_city
FROM custnew;
```
**Meaning:** We insert data that comes from `custnew` table.

> ATTENTION: when inserting from another table, if the primary key(eg. `cust_id`) is repeated, the subsequent insert operation will fail.
> Either ensure that the primary key value of the inserted row is not duplicate, or ignore it directly and let MySQL generate new values by itself. Like the example above.

## Ch 20 Update and Delete Data
Using `UPDATE` and `DELETE` clause to manipulate table data.

### Update data
`UPDATE` is formed with 3 parts:
- table to be updated
- column names and their new values
- Determine the filter criteria for the row to update(`WHERE`)
```MySQL
UPDATE customers
SET cust_email = 'elmer@fudd.com'
WHERE cust_id = 10005;
```
**Meaning:** set the `cust_email` value of `cust_id=10005` to `'elmer@fudd.com'` in the `customers` table.

To set more rows:
```MySQL
UPDATE customers
SET cust_name = 'The fudds',
	cust_email = 'elmer@fudd.com'
WHERE cust_id = 10005;
```

> We can also use subqueries to update more data.

> If `UPDATE` encounters an error, all `UPDATE` operation will be canceled and data will be restored to the original.
> We can use `IGNORE` to ignore error. Continue update. `UPDATE IGNORE customers...`

Set a column value to `NULL` to delete the value.
```MySQL
UPDATE customers
SET cust_email = NULL
WHERE cust_id = 10005;
```

### Delete data
Delete a row:
```MySQL
DELETE FROM customers
WHERE cust_id = 10006;
```
**Meaning:** Delete that row.
> Remember to use `WHERE` clause, otherwise all rows will be deleted.

If want to delete all rows from a table, use `TRUNCATE TABLE`, which is more fast.

#### guidelines for updates and deletions
1. Never use an `UPDATE` or `DELETE` clause without a `WHERE` clause unless you do want to make changes to all rows.
2. Ensure every table has a primary key.
3. Before using `UPDATE` or `DELETE`, using `SELECT` first so that you can ensure that the `WHERE` clause do what exactly what you want.

> ATTENTION! There is NO UNDO in MySQL! Use `DELETE` and `UPDATE`  carefully.

## Ch 21 Create and Manipulate Tables
### Table creation basis
To use `CREATE TABLE`, the following information must be given:
- The new table's name, after `CREATE TABLE`.
- Names and definitions of table columns, separated by commas.

```MySQL
CREATE TABLE customers
(
	cust_id int NOT NULL AUTO_INCREMENT,
	cust_name char(50) NOT NULL,
	cust_address char(50) NULL,
	PRIMARY KEY (cust_id)
) ENGINE = InnoDB;
```
**Meaning:** Create a new table named `customers` which has three columns, they are: `cust_id, cust_name, cust_address`. `cust_id` will self increases, and set it as the primary key.