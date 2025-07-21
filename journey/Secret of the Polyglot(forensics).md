This challenge is about merging png with pdf, and it baffled me a lot. 

You may need to download the file so that you can open it in your local machine. When you open it, you will see the second half of the flag.
And you can find the first half by just changing the extension from *pdf* to *png*. When you open it, you'll see the *png* version of the file
which contains the first half of the flag.

If you run ``` file <filename.pdf> ```, you can see that it also contains a *PNG*. So, just to make things easier, 
run ``` cp <filename.pdf> <filename.png> ```

I tried to do all this just using the webshell and after lots of searching and youtube videos, I did something I had to do in the first place:
and that is "READING THE README.txt". You can read it by running ``` less ~/README.txt ```. And I found the following there: "- Exporting files from the webshell to the browser or vice versa
is possible using ``` sz <filename> / rz. ``` " . 

Since the first half flag is in the *png* file, run ``` sz <filename.png> ```. The flag is there. And now run ``` sz <filename.pdf> ```
to find the second half.

  
