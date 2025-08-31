* Title: SQL Injection UNION Attack: Determine The Number Of Columns Returned By The Query

* Goal: Determine the number of columns the original query returns using UNION SELECT injection 


* Vulnerability Location: The product category search parameter is vulnerable to UNION SELECT injection 

* Payload: ' ORDER BY n -- (n=1, 2, 3...)

And then: ' UNION SELECT NULL, NULL, NULL --

* Step:

1. Choose a category (such as Gifts) and add the first payload (' ORDER BY 1--) on the URL. Keep incrementing the number until you get an "Internal Server Error". If ORDER BY 3-- works but ORDER BY 4-- gives you error, that means it has 3 columns. And I'll assume it has 3 columns.

2. And now add the second payload (' UNION SELECT NULL, NULL, NULL--). If successful, the page loads without error.

* Explanation: The ORDER BY n payload helps us identify how many columns there are.

And the UNION SELECT NULL, NULL, NULL helps us to match the columns without having to face type compatibility error.

When you do UNION SELECT you must match the column number and data type correctly. If we mismatch the column type we get an error. But when using NULL, we don't need to worry about the data types. It matches with every data type.

* Result: You now discover the exact number of columns the query returns.

* Takeaway: Understanding the structure of the query is a key to successful SQL injection. ORDER BY and UNION SELECT NULL are really important tools for reconnaissance.
