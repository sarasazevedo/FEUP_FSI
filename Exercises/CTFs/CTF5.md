# CTF 5

##  Challenge 1 

To complete this challenge we have to follow the initial steps and then answer the questions:
1. Is there a file that is opened and read by the program?
2. Is there any way to control which file is opened?
3. Is there any buffer overflow? If so, what can you do?

First, we use the “checksec” program like is asked. 

![Alt text](/images/CTF5-1.png)

Then, to ask the questions above we analyzed the “main.c” file. 
1.	Yes. The file “mem.txt” is opened and read by the program.
2.	 Yes. To alter the content meme_file we just must change the content of that variable. 
3.	Yes. The variable “buffer” has length of 32 and the scanf will read 40, so we can overwrite. 

After that, we conclude that if we exceed the buffers capacity we overwrite in the meme_file and we can reach the flag. 

![Alt text](/images/CTF5-2.png)

## Challenge 2

To complete this challenge we have to follow the initial steps and then answer the questions:
1.	What changes were made?
2.	Do they completely mitigate the problem?
3.	Is it possible to overcome mitigation using a similar technique to the one used previously?

First, we use again the “checksec” program like is asked. 

![Alt text](/images/CTF5-3.png)

Like we did before, we started to analyze the “main.c” file. 

We tried to run the “nc ctf-fsi.fe.up.pt 4000” and used the “mem.txt” file to find the flag but  no result with that. 

![Alt text](/images/CTF5-4.png)

Them, we changed the “val” number to “0xfefc2324” in the main file and in the python program given in the challenge 1 we injected content to write 32 characters (in “r.sendline(…)”) followed by the new value of “val” and the name file that we wanted to read.  
After that, we executed again and found the flag. 

![Alt text](/images/CTF5-5.png)
