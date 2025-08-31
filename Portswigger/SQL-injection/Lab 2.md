Title: SQL Injection Vulnerability Allowing Login Bypass 

Goal: login as administrator without knowing the password 

Vulnerability Location: the login form accept user input(username and password) without sanitizing it first, which will be embedded in the SQL query.

Payload Used: ' OR 1=1--

Steps:

1. Go to the login page(My Account)

2. Enter administrator as a username.

3. Enter the payload as password and hit login. Voila - You're in.

Explanation: The query probably looks like this:

  SELECT * FROM users WHERE username = 'administrator' AND password = 'The_admin_password';

So when you feed it the payload it becomes something like:
     
  SELECT * FROM users WHERE username = 'administrator' AND password = '' OR 1=1--;

OR 1=1 always evaluate to true and you comment out the rest of logic trail with '--'

Result: You have successfully logged in as administrator without knowing the password.

