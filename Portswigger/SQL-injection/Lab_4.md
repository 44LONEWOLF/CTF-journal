* Title: SQL Injection UNION Attack: Retrieving Data From Other Tables

* Goal: Exploit UNION injection vulnerability to retrieve sensitive data(like username and password) from different table.

* Payload Used: ' UNION SELECT username, password, NULL FROM users--  (assuming there are three columns)

* Steps:

1. Determine the number and data type of the columns using ORDER BY & UNION SELECT(like we did in lab 3)

2. Submit the request and observe the response. The username and password should be displayed on the page.

* Explanation: UNION SELECT helps to combine results from two queries.

NULL is used to fill unused columns and avoid data type mismatch errors.

* Result: You successfully retrieved all usernames and passwords from ```users``` table.

* Takeaway: When query results are reflected on the response, UNION SELECT becomes a powerful tool for data extraction.
