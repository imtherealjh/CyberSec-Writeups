# Reversing ELF Part 2
Room for beginner Reverse Engineering CTF players

https://tryhackme.com/room/reverselfiles

Author: mrpvr

## Task 4
When I ran the program, it prompted for a password. However, upon entering 'password' as the password, the program responded with an "password "password" not ok" message

![Running Program](/THM/ReversingELF/images/8.PNG)

However, when I ran the "strings" command this time, it didn't yield the password. Consequently, I decided to utilize Ghidra to assist with this task.

### Ghidra
Upon analyzing the program with Ghidra, I observed that in the main function, it checks whether the number of parameters is equal to 2. If not, it executes the message: "Usage: %s password\nThis time the string is hidden, and we used strcmp."

When the parameters meet the specified condition, the compare_pwd function is executed. Within the compare_pwd function, I observed that several hexadecimal values are loaded into various variables. Before passing the memory address of the variable local_28 into the get_pwd function.

```
local_28 = 0x7b175614497b5d49;
local_20 = 0x547b175651474157;
local_18 = 0x4053;
local_16 = 0;
get_pwd(&local_28);
```

Inside the get_pwd function, there appears to be a loop that iterates through the memory address passed in and increments it by 1 with each iteration. During each iteration of the loop, the value at the memory address is XORed with 0x24.

```
void get_pwd(long param_1)
{
  int local_c;
  
  local_c = -1;
  while (local_c = local_c + 1, *(char *)(param_1 + local_c) != '\0') {
    *(byte *)(local_c + param_1) = *(byte *)(param_1 + local_c) ^ 0x24;
  }
  return;
}
```

At this juncture, I encountered difficulties due to my limited efficiency in comprehending the decompiled code produced by Ghidra. Consequently, I sought assistance from ChatGPT to gain insights into the functionality of each line of code.

I devoted a significant amount of time to fully comprehend the code's operations. The hexadecimal values configured within the compare_pwd function were utilized to perform XOR operations with 0x24, resulting in the generation of the ultimate password. Subsequently, I employed CyberChef to validate and confirm my understanding of the code's functionality.

![Flag Captured](/THM/ReversingELF/images/9.PNG)

Boom! We successfully obtained the correct password.

## Task 5
When I ran the program, it prompted for an input. However, upon entering 'password' as the password, the program responded with an "Always dig deeper" message.

![Program Ran](/THM/ReversingELF/images/10.PNG)

However, when I ran the "strings" command this time, it didn't yield the useful strings for the task. Consequently, I decided to utilize Ghidra to assist with this task.

### Ghidra
When I initially opened the program in Ghidra, I was confronted with an extensive list of hexadecimal values.

```
local_38 = 0x4f;
local_37 = 0x66;
local_36 = 100;
local_35 = 0x6c;
local_34 = 0x44;
...
local_20 = 0x74;
local_1f = 0x58;
local_1e = 0x74;
local_1d = 0x7a;
```

Subsequently, the memory address of the first variable (local_38) was passed into the strcmp_ function. In response, I decided to delve into the function to determine whether there were any crucial details that needed adjustment, similar to what I encountered in the previous task.

Within the function, I didn't come across any instances of dereferencing param_2, which represents the memory address of the local_38.

```
void strcmp_(char *param_1,char *param_2)
{
  size_t sVar1;
  int local_20;
  int local_1c;
  
  for (local_20 = 0; local_20 < 0x16; local_20 = local_20 + 1) {
  }
  local_1c = 0;
  while( true ) {
    sVar1 = strlen(param_1);
    if (sVar1 <= (ulong)(long)local_1c) break;
    param_1[local_1c] = (byte)key ^ param_1[local_1c];
    local_1c = local_1c + 1;
  }
  strncmp(param_1,param_2,0x1c);
  return;
}
```

Therefore, I opted to employ CyberChef to decode all the hexadecimal values present in the main function.

![Flag Captured](/THM/ReversingELF/images/11.PNG)

Boom! We successfully obtained the correct string.

## Task 6
When I ran the program, it prompted for an input. However, upon entering 'password' as the password, the program responded with an "password "password" not OK" message.


