* Title: SQL Injection UNION Attack: Retrieving Multiple Values in A Single Column

* Goal: Retrieving multiple values(username and password) from a single column using UNION-based SQL injection attack.

* Payload Used: ' UNION SELECT username || '~' || password FROM users--

* Steps:

1. Confirm that there is only one column using ORDER BY or UNION SELECT NULL.

2. Inject the payload into the category search parameter and observe the result. Now the username and password appear on the page.

* Explanation: When the query returns only one column, you can't use UNION SELECT username, password directly.
Instead you concatenate both values using ```username || '~' || password``` or ```CONCAT(username, ':', password)``` (depending on the database. for example in MySQL, we use the CONCAT syntax).

The delimiters like : or ~ help separate the values visually.

* Result: You successfully retrieve the username and password from the users table, displayed as concatenated strings in the single output column.

* Takeaway: When limited to a single output column, string concatenation allows you to retrieve multiple values in one go.
