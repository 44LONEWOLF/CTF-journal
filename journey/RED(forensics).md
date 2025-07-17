This one is cool. It teaches you how scripts or things like that hide in an innocous looking image.

I chose the "python scripting" way for this challenge, but you can also use an easy way. I'll show you both.

The first one is to download the file. and when you write the command ``` strings red.png ```  , you see something like a poem.

But if you look closely, the first letter of each row construct a word "CHECK LSB", LSB means Least Significant Bit.

Check this link to get basic understanding [ https://github.com/RenanTKN/pylsb/blob/master/README.md ]

After that you go to this link [ https://cyberchef.org/ ] -> go to search bar and write steganography -> drag the thing that say Extract LSB
and then on the right choose 'open file as input'(the icon with a box and right arrow) choose the file(red.png, on this case) -> now you will get an output that seems to repeat it self
after ==. Knowing base64 encrypthing pattern, we now write "from base64" on the search bar and drag it. And there is your flag.

Now, let's proceed to the scripting part. 

```

from PIL import Image       # Make sure you have imported the pillow module on your computer (Use pip install pillow, if you don't have it)
import base64

img = Image.open('red.png').convert("RGBA")     # This opens the image and convert it to Red, Green, Blue, Alpha mode
width, _ = img.size     # This get the width and height of the image but ignore the height as we only need the row i.e, width

bits = []

for x in range(width):
    r, g, b, a = img.getpixel((x, 0))       # getpixel() only take one argument so that we include x & 0 as a tuple 
    bits += [r&1, g&1, b&1, a&1]        #

binary = ''.join(str(bit) for bit in bits)    # Convert bits to binary string
chars = [chr(int(binary[i : i+8], 2)) for i in range(0, len(binary),8)]    # convert to ASCII
flag = base64.b64decode(''.join(chars)).decode()    # decode base64 to reveal message
print(flag)

```

CyberChef is a powerful tool for quick decoding, but scripting gives you full control.
