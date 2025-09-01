* Title: SQL injection attack, listing the database contents on non-Oracle databases

* Goal: Log in as the administrator user.

* Vulnerability Location: The **product category search parameter** is vulnerable to UNION SELECT injection.

* Payload Used:

 ```' ORDER BY 2 --``` ,  ```' UNION SELECT table_name, NULL FROM information_schema.tables --``` , 
 
 ```UNION SELECT column_name, NULL FROM information_schema.columns WHERE table_name='users_ncodug'--``` , 
 
 ```' UNION SELECT username_hsawsp, password_poblds FROM users_ncodug--```

* Steps:

  ### IMPORTANT NOTE: Table names(like users_ncodug) and column names (like username_hsawsp and password_poblds) in PortSwigger labs are randomized, so you must first discover them via information_schema.tables.

  1. First we determine the number of columns using  ```ORDER BY 2 --``` command. And here, I get 2 columns. 
 
  2. Now, we inject the second payload ```' UNION SELECT table_name, NULL FROM information_schema.tables --``` , which basically means "Get all the table names".
    Scan the page output for table names that resemble credentials, typically users_* (example: "users_ncodug")
 
  3. Then we inject the next payload ```UNION SELECT column_name, NULL FROM information_schema.columns WHERE table_name='users_ncodug'--``` .
    This payload queries the information_schema.columns table, a system-level metadata registry available in MySQL and MSSQL, to list all column names belonging to the users_ncodug table.

  4. Scan the page output for column names that resemble credentials, typically username_* and password_* (example: "username_hsawsp" & "password_poblds")
 
  5.  And last, inject the payload ```' UNION SELECT username_hsawsp, password_poblds FROM users_ncodug--```, which means "Extract the content of 'username_hsawsp' & 'password_poblds' columns from users_ncodug table"

* Result: You successfully enumerate the database structure and extract usernames and passwords from a hidden table using UNION-based SQL injection.

* Takeaway: When targeting non-Oracle databases, information_schema is your map. Use it to list tables, inspect columns, and extract data — all through UNION injection. Always match the column count and structure, and adapt to randomized table/column names.
