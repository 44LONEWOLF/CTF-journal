This challenge teaches you about QR codes which can encode hidden texts.

As you probably know, QR codes can store URLs but they also can be used to store other things too.

And now you will get a flag out of a QR code. There are a couple of ways to do this.

1. You connect with the SSH. After that, it'll show you the image. You can screenshot it and then search it on Google image/lens.
And Voila, you get the flag.

2. You can also download the .zip file on your machine and extract QR image.

    - Go to this link: [ https://sourceforge.net/projects/zbar/ ] and download a ZBar bar code reader and install it.
     
    - After that, open **ZBar command prompt**
     
    - Run ``` zbarimg <the_QR_code_image_path> ```
     
    - And there shall be your flag.
