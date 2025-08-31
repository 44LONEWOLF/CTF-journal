

This challenge teaches you how RSA works. 
There is a private key and a public key. The public key is used to encrypt the message and the private one is used to decrypt it.
And the public key consist N and e, N being the product of two prime numbers, p and q. and e being a public exponent. Modern RSA uses massive prime numbers to ensure security.
But in this challenge, if you look closer, N is even. And 2 is the only even prime number. so in this case, p=2.
That makes N easily breakable. I won't go deep into the math right now but if you are interested, check out this video: [ https://youtu.be/wcbH4t5SJpg?si=NgPdPrsidO48qXtk ]

Now let's use python to decrypt the message.

```

from Crypto.Util.number import long_to_bytes, inverse

N = 24913508843586413817902986190776789351432142035353979332472789703997247369358316240676153500744002598705324489853407907330099005970034173008745995676040206
p = 2    
q = N//p    # Make sure q is not a float number. Use //
e = 65537
cyphertext = 419865847666888356000840918463571895620053405607787776999315144077473239292185386820003761345688368874287460659154879642237962530629748882120673662526259

d = inverse(e, (p-1)*(q-1))

decrypted = pow(cyphertext, d, N)      # Where cyphertext is base, d is exponent and N is module(i.e, cyphertext^d mod(N), where mod means we are keeping only the remainder after dividing)

print(long_to_bytes(decrypted).decode())    # This will change the decrypted from massive number to ASCII

```

Personally I don't recommend using online decoders. You should understand what the algorithm is all about. But after you understand the logic, you can use this website [ https://www.dcode.fr/rsa-cipher ] to decrypt stuff.
In real-world scenarios, convenience matters — just make sure you understand the math first.
