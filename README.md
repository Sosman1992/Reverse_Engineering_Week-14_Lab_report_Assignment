#  Control Flow Integrity Violation 


The Weeks Lab focus was on Writing a python script using `pwntools` that executes a given program named `pizza.c`, leaks the stack offset, and spawns a shell. Technique used is `buffer overflow`

---

After downloading the victim program: pizza to my desktop and executed it in my terminal with the command `./pizza`; I obtained the error output `bash: ./pizza: Permission denied`, hence, I added execute permissions to the pizza file with the command chmod +x pizza. Rerun the victim program named pizza and did not encountered error.

After running the victim program: pizza to see how it works as it can be shown from the image below:

![Screenshot from 2023-04-30 12-09-30](https://user-images.githubusercontent.com/66968869/235370146-73381ada-ad4d-47cd-9d69-4d2da068bf7f.png)

I then proceeded to Install pwntools by running `pip install pwntools` in my terminal window to install pwntools library for Python on my system.
