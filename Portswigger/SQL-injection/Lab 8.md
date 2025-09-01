* Title: SQL injection attack, querying the database type and version on MySQL and Microsoft

* Goal: Display the database version string.

* Vulnerability Location: The **product category search parameter** is vulnerable to UNION SELECT injection.

* Payload Used:

 ```' ORDER BY 2 # ``` or ```' ORDER BY 2 --+``` , ```' UNION SELECT @@version, NULL #``` or ```' UNION SELECT @@version, NULL --+```

* Steps:

  1. First we determine the number of columns using the first payload ```ORDER BY 2 #``` command (But this works if you are using Burp Suite. If you are using the URL bar, use this one instead: ```' ORDER BY 2 --+```.)
    And here, I get 2 columns. How do I know? I get a ```200 OK```(a status code equivalent for success) until I try ```ORDER BY 3 #``` (or  ```' ORDER BY 2 --+```, if you are using the URL bar) which implies there are just 2 columns.
 
  2. Now, we inject the second payload  ```' UNION SELECT @@version, NULL #``` (or ```' UNION SELECT @@version, NULL --+```). And then, Boom! You successfully displayed the database version string.


* Explanation:

  Why are we using another version of payload when using URL bar? Because the URL bar always encodes our input(like = to %3d). 
  And the pound(#) sign is treated as a **fragment identifier**, which is never sent to the server. And that messes with our payload.
  So instead, we use ```--+```. The ```--``` comment out the rest of the line and the ```+``` ensures there's a space after the comment, which some parsers require.  

  As for the next payload, it really depends on the database type. For example, what we used earlier is used in **Microsoft SQL Server** and **MySQL**
  This is a [Cheat Sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet?form=MG0AV3&form=MG0AV3) for different types of databases.
  
  You should also note that for different databases there is different commenting style. Checkout the cheatsheat for more.

* Result: You successfully retrieve the database version string, confirming the backend technology (MySQL or MSSQL) and its specific version.

* Takeaway: Identifying the database type and version is a critical recon step. It helps tailor your payloads to the correct syntax and exploit paths.
  @@version is a reliable fingerprinting tool across major SQL engines.
