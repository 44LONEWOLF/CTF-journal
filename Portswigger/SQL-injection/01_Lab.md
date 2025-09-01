Title: SQL Injection Vulnerability In WHERE clause Allowing Retrieval Of Hidden Data

The Goal: Bypass the visibility filters and reveal every item, including the ones that are not released yet.

Vulnerability Location: The URL bar.

Payload Used: ' OR 1=1--

Step:

1. Choose one category. I choose Gifts.

2. On the URL bar, add the payload so it'd look like this: ...Gifts' OR 1=1--

Explanation: What you are doing here is closing the category using ' and injecting an SQL command that returns true and reveal all the products because 1 is always equals to 1.

The "--" comment out the rest of the line in the database query.

* So the original version looks like:
  
SELECT * FROM products WHERE category = 'Gifts' AND released = 1;

* And after injection:
  
SELECT * FROM products WHERE category = 'Gifts' OR 1=1--' AND released = 1; (The 'AND released = 1 is no longer in the query)

Result: You get every item in the website, released or not.

Takeaway: Unsanitized input in SQL queries can expose all data. Always validate and use parameterized queries.
