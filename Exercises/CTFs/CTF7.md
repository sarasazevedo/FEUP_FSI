## CTF 7

## Challenge 1

We started by running the `checksec --file=program` in order to obtain more information about the program's protections. 

![Alt text](/Exercises/images/CTF7-1.png)

After analysing the source code, we can answer the following questions:

1. In which code line is the vulnerability found?
2. What does the vulnerability allow us to do?
3. Which functionality allows us to obtain the flag?

Answers:

1. The vulnerability is in line 25 of `main.c`, since it has a `scanf()` that scans the bytes of the variable `buffer`; however , the `load_flag()` function loads more bytes than what `buffer` contains.

2. It allows us to use a "format string attack" to usurp it's behavior, and access values we are not supposed to.

3. When the `load_flag` function is called, it reads the flag contained in the directory to another buffer that is a global variable and, therefore, is allocated in the Heap. By discovering the address of the function, it is possible to return the value of the string from the buffer.

To discover the functions address we'll use the gdb debugger:

```note
$ gdb program
$ p load_flag # 
```

The result was the return address "0x08049256", which is "\x60\xC0\x04\x08" in string format.

![Alt text](/Exercises/images/CTF7-2.png)

Then, we need to put the value of this register in the our "exploit_example.py". Since it is an integer, the execution stack reads it differently when compared to an array, as we concluded in the task of the previous week. So, we inverted the order of the bytes of the register 0x8049256 and inserted the register "\x60\xC0\x04\x08". Besides that, we needed to concatenate it to "-%s", so that we could read the content of the register itself. We executed the exploit file in local mode and we could access the content of "flag.txt".

![Alt text](/Exercises/images/CTF7-3.png)

![Alt text](/Exercises/images/CTF7-4.png)

This means we exploited the program successfully. So, we tried in the remote mode, by asserting the `LOCAL` variable as False and we obtained the flag value sucessfully.

![Alt text](/Exercises/images/CTF7-5.png)

## Challenge 2

We used checksec and we found the same restrictions from the first challenge.

![Alt text](/Exercises/images/CTF7-Challenge2-1.png)

After analysing the source code, we can answer the following questions:

1. In which line of code is the vulnerability at? And what does it allow us to do?
2. Is the flag loaded into memory? Or is there some functionality we can use to access it?
3. To unlock that functionality what do we have to do ?

Answers:

1. The vulnerability can be found in line 18 of the `main.c` file, this time, a bash script is launched if the value of key is 0xBEEF, or 48879 in decimal. This backdoor is sufficient to control the server and thus consult the content of the flag.txt file.

2. Yes, the key is loaded into the memory and we can access its value by using the command `p &key`.

3. To manipulate the value of key, which is a global variable and therefore is allocated in the Heap, we use the format string attack of the input through the option '%n'. Initially we need the value of the address of the variable key, and for that we use gdb:

```note
$ gdb program
$ p &key
```

The result was the return address 0x804b324 which is "\x08\x04\xB3\x24" in format string. 

![Alt text](/Exercises/images/CTF7-Challenge2-2.png)

We know how to write our string: but first, we have to calculate A. To do this we know 0xBEEF=48879. We also know that we have written 8 chars so far for our address, that left us with 48879-8=48871. This is the number we got to substitute 'A' with this value.

Now we run the exploit_example.py we got from the previous challenge and we can get the flag in the flag.txt:

![Alt text](/Exercises/images/CTF7-Challenge2-3.png)

![Alt text](/Exercises/images/CTF7-Challenge2-4.png)
