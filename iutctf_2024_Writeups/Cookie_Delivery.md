---
created: 2024-04-26
---
## Description:
![[Desc]](/resources/Cookie-delivery-iutctf2024-problem-desc.jpg)
## File:
[chal](resources/cookie-delivery-iutctf2024-problem)
## Solve:
####  After using file command this was the output:
```
./chal: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=1b3b2ce343a45dffd44f5398a957b5232d1419be, for GNU/Linux 4.4.0, not stripped
```
So, it's a 64-bit ELF executable.

#### After using string command:
```
PTE1
u3UH
oY7H
B2 H
Y	T>H
Who are you?
Here's what my delivery boy has to say regarding your complains: %s
;*3$"
```
So, nothing useful in there.

#### Opening the executable with ghidra:
The content of the main function:
```c
undefined8 main(void)

{
  ssize_t sVar1;
  long in_FS_OFFSET;
  int local_60;
  byte local_51 [8];
  undefined local_49;
  undefined8 local_48;
  undefined8 local_40;
  undefined8 local_38;
  undefined8 local_30;
  undefined8 local_28;
  undefined8 local_20;
  undefined local_18;
  long local_10;
  
  local_10 = *(long *)(in_FS_OFFSET + 0x28);
  local_48 = 0x7516561a5600463b;
  local_40 = 0x2b196f5a52015f61;
  local_38 = 0x37596f1a01176c61;
  local_30 = 0x2032421b050d6c61;
  local_28 = 0x7332055d041f0362;
  local_20 = 0x3e54095906105164;
  local_18 = 0x52;
  puts("Who are you?");
  sVar1 = read(0,local_51,9);
  local_49 = 0;
  for (local_60 = 0; local_60 < 0x32; local_60 = local_60 + 1) {
    *(byte *)((long)&local_48 + (long)local_60) =
         *(byte *)((long)&local_48 + (long)local_60) ^ local_51[local_60 % ((int)sVar1 + -1)];
  }
  printf("Here\'s what my delivery boy has to say regarding your complains: %s\n",&local_48);
  if (local_10 != *(long *)(in_FS_OFFSET + 0x28)) {
                    /* WARNING: Subroutine does not return */
    __stack_chk_fail();
  }
  return 0;
}
```

After changing some variable names:
```c++
undefined8 main(void)

{
  ssize_t sVar1;
  long in_FS_OFFSET;
  int i;
  byte input [8];
  undefined local_49;
  undefined8 local_48;
  undefined8 local_40;
  undefined8 local_38;
  undefined8 local_30;
  undefined8 local_28;
  undefined8 local_20;
  undefined local_18;
  long local_10;
  
  local_10 = *(long *)(in_FS_OFFSET + 0x28);
  local_48 = 0x7516561a5600463b;
  local_40 = 0x2b196f5a52015f61;
  local_38 = 0x37596f1a01176c61;
  local_30 = 0x2032421b050d6c61;
  local_28 = 0x7332055d041f0362;
  local_20 = 0x3e54095906105164;
  local_18 = 0x52;
  puts("Who are you?");
  sVar1 = read(0,input,9);
  local_49 = 0;
  for (i = 0; i < 50; i = i + 1) {
    *(byte *)((long)&local_48 + (long)i) =
         *(byte *)((long)&local_48 + (long)i) ^ input[i % ((int)sVar1 + -1)];
  }
  printf("Here\'s what my delivery boy has to say regarding your complains: %s\n",&local_48);
  if (local_10 != *(long *)(in_FS_OFFSET + 0x28)) {
                    /* WARNING: Subroutine does not return */
    __stack_chk_fail();
  }
  return 0;
}
```

We can see that, the program taking input of upto nine characters. Though the input string is of 8 bytes. Considering taking a 8 character input. 
we are doing xor operation with the variable local_48. 

After looking at these variables we see that all of them are of 8 bytes length except for the first and the last one. So, 6 * 8 = 48 + 1 +1 = 50 which is equal to the number of times the loop runs. Thus it must be a char string of 50 characters. Thus changing the type of the variable to `char[50]` the variable changes to :
```c++
  local_49[1] = ';';
  local_49[2] = 'F';
  local_49[3] = '\0';
  local_49[4] = 'V';
  local_49[5] = '\x1a';
  local_49[6] = 'V';
  local_49[7] = '\x16';
  local_49[8] = 'u';
  local_49[9] = 'a';
  local_49[10] = '_';
  local_49[11] = '\x01';
  local_49[12] = 'R';
  local_49[13] = 'Z';
  local_49[14] = 'o';
  local_49[15] = '\x19';
  local_49[16] = '+';
  local_49[17] = 'a';
  local_49[18] = 'l';
  local_49[19] = '\x17';
  local_49[20] = '\x01';
  local_49[21] = '\x1a';
  local_49[22] = 'o';
  local_49[23] = 'Y';
  local_49[24] = '7';
  local_49[25] = 'a';
  local_49[26] = 'l';
  local_49[27] = '\r';
  local_49[28] = '\x05';
  local_49[29] = '\x1b';
  local_49[30] = 'B';
  local_49[31] = '2';
  local_49[32] = ' ';
  local_49[33] = 'b';
  local_49[34] = '\x03';
  local_49[35] = '\x1f';
  local_49[36] = '\x04';
  local_49[37] = ']';
  local_49[38] = '\x05';
  local_49[39] = '2';
  local_49[40] = 's';
  local_49[41] = 'd';
  local_49[42] = 'Q';
  local_49[43] = '\x10';
  local_49[44] = '\x06';
  local_49[45] = 'Y';
  local_49[46] = '\t';
  local_49[47] = 'T';
  local_49[48] = '>';
  local_49[49] = 'R';
```

And the loop changes to:
```c
  for (i = 0; i < 50; i = i + 1) {
    local_49[(long)i + 1] = local_49[(long)i + 1] ^ input[i % ((int)sVar1 + -1)];
  }
```
So, now it looks easy.
Here, we can see there is a xor operation happening with the local_49 variable and the input we are giving. The input is rotated from 0 to input_size to make the xor upto the last character. Thus the output might be the flag we are wanting. 

About xor operation we know that,
A^B = C then A^C = B

So, it the output is our flag, then if we input `iutctf{` as the input the output might be the input we want:
Thus running the program and giving input iutctf{ as the input:
```
Who are you?
iutctf{
Here's what my delivery boy has to say regarding your complains: R3t5n0m+b&<p^cga,Ck~r7FCedm(qQ*ys-j X)i
```
Again running the program and inserting `R3t5n0m` as input:
```
Who are you?
R3t5n0m
Here's what my delivery boy has to say regarding your complains: iutctf{'R+4<jKYy1w=jCT=hIqF
                                                                                            3rVnqT<B5-<:?R
```
Well, we got our first 7 characters, all we need is the 8th character.

We know that the last character of our flag is '}' So we do modulo `local_49[49] = 'R'` with `}` we get  `/`

So, after inserting `/` as the last character we get:
```
Who are you?
R3t5n0m/
Here's what my delivery boy has to say regarding your complains: iutctf{Z3lug4_t3_c4t_43_y0ur_00k135_\6bd3799
```
Not the right one. But we can get a hint about beluga cat doing something with cookies. But even trying with B or b the result was not the expected one.

So, time to do some bruteforce.
Here, I created a small python script with pwntools to  try with each of the ascii characters upto 127 and printing the output. Here, the log level is kept in `error` to minimize the output of the script:
```py
from pwn import *

context.log_level = 'error'

for i in range(10,128):
    print(chr(i))
    with process('./chal') as p:
        payload = 'R3t5n0m' + chr(i)
        p.sendline(payload)
        
        print(p.clean(1))
```
After running the program and analyzing the output we only get `}` as the last character for the input `C`.
```
C
b"Who are you?\nHere's what my delivery boy has to say regarding your complains: iutctf{63lug4_th3_c4t_4t3_y0ur_c00k135_06bd3799}\n"
```

Thus the flag is `iutctf{63lug4_th3_c4t_4t3_y0ur_c00k135_06bd3799}`

