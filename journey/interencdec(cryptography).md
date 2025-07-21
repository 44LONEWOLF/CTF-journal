This challenge is about cryptography and it has layers of it.

First download the file and use ``` cat <filename> ``` to see it.

The output looks like base64. And when you decrypt it(you can use [ https://cyberchef.org/ ]), you’ll get another string that also looks like base64 — decrypt that too..

After that you'll get an output that has a typical picoCTF flag format(4 lower and 3 upper case letters, 
and after that wrapped in curly braces...) I personally decrypted it manually, you just go back 7 letters to find the original one(w=23 & p=16, 23-16=7)

If you prefer to use an online Caesar decoder(the name of the decryption technique is called Caesar decryption), you can use
[ https://cryptii.com/pipes/caesar-cipher ] as well.

Just like an onion, layers of encryption. 
