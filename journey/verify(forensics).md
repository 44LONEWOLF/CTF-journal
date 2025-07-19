For this challenge, first we connect with the SSH account, type "yes" and input the password they have provided.

After this, there are a couple of ways(that I've tried) to get the flag.

1. The first one is to use bash script as follow:

``` for item in $(find files -type f); do ./decrypt.sh "$item" | grep 'pico'; done ```

This loops through each file in files folder. And for each item it will run the script "./decrypt.sh"

You might have to scroll up a bit to find the flag but it gets the job done.

2. The second one goes like this:

        -   ``` sha256sum files/* | grep "467a10447deb3d4e17634cacc2a68ba6c2bb62a6637dad9145ea673bf0be5e02" ```
This gets each file in "files" folder, generate a SHA256 hash for each file, compare with the checksum and get the same one

        -   ``` ./decrypt.sh files/<the_file> ```
This decrypt the file and prints the flag. (<the_file> being the previous grep output)
