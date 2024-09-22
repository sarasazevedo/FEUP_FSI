# Logbook 4

## Task 1

All the environment variables can be obtained using the printev or just env commands. Using the printenv PDW or env | grep PDW command only shows a few variables specific to that environment.

![Alt text](/Exercises/images/image.png)

## Task 2

First, we compile the myprintenv.c programme using the "gcc myprintenv.c" command. After that, we save the variables of the parent process and the child process in separate files and conclude that after making the difference between the two, the result is empty. 

![Alt text](/Exercises/images/image-1.png)

This means that all the variables from the parent environment are inherited by the child.

## Task 3

The program is compiled by using “gcc myenv.c” and after that we save the output as a file named null. Then we have to change the “execve()” in line 1 for “execve(“/usr/bin/env”,arg,environ)” and run again the program and save the output as a file named environ. To see the differences between both files we use the command diff. 

![Alt text](/Exercises/images/image-2.png)

We can conclude that in the null file no variables were passed unlike to the environ file.

## Task 4 

In this task we create, compile and run a file named system.c.
We saw that the system() function uses execl() to execute /bin/sh; execl() calls execve(), passing to it the environment variables array, as it says in the statement. 

![Alt text](/Exercises/images/image-3.png)

## Task 5 

We wrote the program given in step 1 that shows all the environment variables of the current process and set “root” as the owner of the program and then turned it into a Set-UID program using the commands given in step 2. Next, after running the program again, it was found that all the defined variables were in the list of the program's environment variables except LD_LIBRARY_PATH. This happens because LD_LIBRARY_PATH could allow a malicious implementation to be executed with root privileges.

![Alt text](/Exercises/images/image-4.png)

## Task 6

After creating a program with the code provided in the task and compiling it, changing the owner root and making it a Set-UID program, it was found that the function executed was the malicious one and not that of the operating system. In conclusion, programs that call the “system()”  function can have security vulnerabilities if they are not run properly, allowing an attacker to execute commands with root privileges.

![Alt text](/Exercises/images/image-5.png)

# CTF 4 - Linux Environment 

The objective of this week's ctf is to explore how environment variables affect the execution of programs on Linux systems. To do this, we had to connect to a server using the command nc ctf-fsi.fe.up.pt 4006. 
After that, we analyzed the different files in the home folder and in the main.c file we found something that could have the information we were looking for (flag.txt). 
We used the command “echo 'LD_PRELOAD=/tmp/lib PATH=/tmp' > env”to create a new file (env) with the content that we wanted. Then, we changed the directory to tmp with "cd /tmp" to perform the rest of the operations. We created an empty file (file.txt) and then we applied the command “chmod 777 file.txt” to allow any user of the system to access and modify it. 
Next, on the file called “lib.c” we had the content that let us access the content of the file “flag.txt”. 
We used the gcc command with “-fPIC” and “-g” to compile with debug information and, following that, the command “gcc -shared -o lib lib.o -lc” to create a shared library and, in the end, we define the permissions (read, write, execute) with “chmod 777 lib”. 

![Alt text](/Exercises/images/CTF4-1.png)

Finally, using the command “cat file.txt” we saw the file and found the flag. 

![Alt text](/Exercises/images/CTF4-2.png)



