This challenge teaches you something about Metadata. Metadata is like an info card next to painting in museum.

It contains information regarding a file, such as: file name, file size, Attribution URL...

In the webshell, there is a tool called ExifTool, which gives us the metadata of a file.

Now, download the file -> unzip it using ``` unzip <filename> ``` -> You'll see a *jpg* file(you'll be inspecting the metadata of this file) -> 
run ``` exiftool <jpg_file_name> ``` -> You now have some metadata to work on.

Look at the Attribution URL, anything familiar? It's a base64 format. Decrypt it here [ https://cyberchef.org/ ] and there is your flag.

By the way, Attribution URL in Metadata refers to a web address included in a file’s metadata that credits the original source or author of the content.
