This one is straight forward. You access the server using the address they give you (It looks like  nc verbal-sleep.picoctf.net......)

They will ask you to crack the code. 

There is one method I have tried. And that is scripting. I used python for that, by the way.

But before that, you need to know ways to determine which algorithm is used.

You can search that on Google or ask AI, but briefly:
md5 = has 32 characters

SHA1 = has 40 characters

SHA256 = has 64 characters

SHA512 = has 128 characters

bcrypt = starts with $2a$ or $2b$

scrypt = may use $scrypt$ or more elaborate encoding

NTLM = usually 32 characters but hex and looks like uppercase MD5

and now you download a rockyou.txt file from this link: [https://www.kaggle.com/datasets/wjburns/common-password-list-rockyoutxt]
Extract it and probably move it to Documents folder.

Once you identify the hash type and download and extract everything, Use the following code to crack the hash.

```
  
target_hash = "cd0894152aa5eec36ec79eb2bcb90ca40f056804530f40732b4957a496b23dc8"    # The hash you want to crack

with open("file_path", 'r', encoding = "latin-1") as file:
    for line in file:
        password = line.strip()
        hashed = hashlib.sha256(password.encode()).hexdigest()    # You change the sha256 with whatever the hashing algorithm is
        if hashed == target_hash:
            print(f"Password found: {password}")
            break
```

You run the script targeting each hash using the appropriate algorithm

And then you get your flag
