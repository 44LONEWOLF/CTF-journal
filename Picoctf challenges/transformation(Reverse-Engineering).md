As always, there are multiple options to do this. I spent good amount of time to understand this, 
write a python script that decode and show the flag, and look for another methods.  

You can do this using the easy way by just watching this video: [https://youtu.be/-apwZZ0TQ-k?si=3M_OMopXXHWxuoW8]

I chose to automate it using the following python script:

```

hexed_list = []
text_list = []

with open('enc', 'r') as f:
    file = f.read()
    for i in range(len(file)):
        code = hex(ord(file[i]))[2:]    # this convert each character to hex value and then exclude the '0x' part
        hexed_list.append(code)        # Add code to the hexed_list list

    for char in hexed_list:
        text = bytes.fromhex(char).decode('ascii')    # And this convert the hex values to ascii value 
        text_list.append(text)    # Addeach ascii value to text_list list

print(''.join(text_list))    # This print the list as a single string

```

This script should decrypt the enc file and print the flag
