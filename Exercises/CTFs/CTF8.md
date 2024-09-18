# CTF 8

First,  in this CTF we explored the code provided in the file "index.php" and we saw how the query to login was made:

![Alt text](/images/ctf8-1.png)

After that, when we change the input to "admin'--" we have privileged access since after "--" all the remaining code is commented out, and the password check is ignored. 

![Alt text](/images/ctf8-1.1.png)

We logged in with username as "admin'--" and password as "--". 

![Alt text](/images/ctf8-2.png)

And as expected, we found the flag. 

![Alt text](/images/ctf8-3.png)


