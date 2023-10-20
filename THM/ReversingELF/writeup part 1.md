# Reversing ELF
Room for beginner Reverse Engineering CTF players

https://tryhackme.com/room/reverselfiles

Author: mrpvr

## Task 1
I successfully retrieved the flag by downloading and executing the provided ELF binary file as part of the task

![Flag 1](/THM/ReversingELF/images/1.PNG)

Nonetheless, I plan to delve into the workings of the ELF binary using Ghidra for a better understanding.

### Ghidra
According to the information provided by the Ghidra decompiler, the binary program defines three variables: local_98, which is identified as a character array; local_78, labeled as an undefined array; and local_c, recognized as an unsigned integer.
```
char local_98 [32];
undefined4 local_78 [27];
uint local_c;
```

In the following lines of code, it appears that the program is populating the local_78 array with several hexadecimal values, as indicated below.

```
local_78[0] = 0x25
local_78[1] = 0x2b;
local_78[2] = 0x20;
local_78[3] = 0x26;
local_78[4] = 0x3a;
...
```

Subsequently, the program executed the memset() function on the local_98 array. However, due to my limited experience in reverse engineering, I was unfamiliar with the function's functionality. Therefore, I conducted further research to gain a better understanding of its purpose.

![Memset](/THM/ReversingELF/images/2.PNG)
Credits: https://www.tutorialspoint.com/c_standard_library/c_function_memset.htm

It appears that the function is filling the local_98 character array with the hexadecimal value 0x41, which corresponds to the character 'A,' for a total of 0x1b bytes, which is equivalent to 27 bytes.

In the following line of code, it appears that the program is executing a loop in which it adds the initial values set in the local_78 array to the new values (0x41) stored in the local_98 array.

Following the loop, the program simply outputs the final value of the local_98 array using the puts() function.

## Task 2
When I ran the program, it prompted for a password. However, upon entering 'password' as the password, the program responded with an "access denied" message

![Running Program](/THM/ReversingELF/images/3.PNG)

### Strings
By running the strings command, we observed several intriguing function names being called, along with some strings that appeared to be familiar from our previous analysis.

```
puts
printf
memset
...
Usage: %s password
super_secret_password
Access denied.
Access granted.
```

I have decided to give the password a try.

![Flag](/THM/ReversingELF/images/4.PNG)

Boom! We successfully obtained the flag. Nevertheless, I plan to utilize Ghidra to gain a deeper understanding of the program.

### Ghidra
In the program, it appears that the main function accepts two parameters. Based on my knowledge and research, these parameters likely correspond to command-line arguments.

The program first verifies whether the argument counter is equal to 2. If this condition is met, it proceeds to check if the provided argument matches "super_secret_password" using the strcmp function. In this context, strcmp returns 0 if the strings are equal and 1 otherwise.

If the strings are equal (resulting in a return value of 0), the program prints "Access Granted" and proceeds to call the giveFlag() function. Conversely, if the strings are not equal, the program prints "Access Denied."

I sought to comprehend the inner workings of the giveFlag() function but found it challenging. To gain clarity, I reached out to a more experienced friend for assistance. Their explanation shed light on the function:

The giveFlag() function involves two character pointers. One of these pointers points to the actual flag, which is stored in the .rodata section. The function then copies this flag to a character array. The subsequent steps of the function resemble those in task 1, where it uses the memset function and adds values to the final character array that will be displayed to the user.

## Task 3
When I ran the program, it prompted for a password. However, upon entering 'password' as the password, the program responded with an "Come on, even my aunt Mildred got this one!
" message

![Running Program](/THM/ReversingELF/images/5.PNG)

### Strings
By running the strings command, we observed several intriguing function names being called, along with some strings that appeared to be familiar from our previous analysis.

```
puts
strlen
malloc
...
Usage: %s PASSWORD
malloc failed
ZjByX3kwdXJfNWVjMG5kX2xlNTVvbl91bmJhc2U2NF80bGxfN2gzXzdoMW5nNQ==
Correct password!
Come on, even my aunt Mildred got this one!
```

It appears that the password is encoded in Base64 format. Consequently, I utilized CyberChef to decode the encoded text and then attempted to use the decoded text as the password.

![Flag](/THM/ReversingELF/images/6.PNG)

BOOM! We successfully obtained the flag once more. Nevertheless, my curiosity persists, and I remain eager to delve into the program's inner workings using Ghidra for a deeper understanding.

### Ghidra
I've encountered my first obstacle while attempting to analyze the code in Ghidra. Unfortunately, there is no debug information available, and the symbol tree is populated with functions labeled as "FUN_*," which makes the analysis process more challenging.

It indeed appears that FUN_080484f4 is very likely the main function, as it displays characteristics consistent with a main function. This includes references to argc and *argv, as well as the presence of messages that were encountered while attempting to analyze and run the program, which is indicative of the program's execution flow.

![Ghidra](/THM/ReversingELF/images/7.PNG)


