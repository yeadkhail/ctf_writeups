---
created: 2024-04-30
---
## Description:
![[Desc]](resources/iutctf2024_ctf_reversible_challenge_desc.png)
## File:
[chal](resources/iutctf2024_ctf_reversible_challengefile)

## Solve:

####  After using file command this was the output:
```
./chal: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=b0834f18c28b27ea201bf549358c188218b8420c, for GNU/Linux 4.4.0, not stripped
```
So, it's a 64-bit ELF executable and not stripped. Great!

#### After using string command:
```
PTE1
u3UH
u_cf140_H
cdb_t_l9H
t4y0}yu5H
'u5tk1{cH
1{cryi0
Flag: 
Wrong
Right
;*3$"
```
So, not that useful.
#### Opening the executable with ghidra:
This is the content of the main function:
```c

undefined8 main(void)

{
  int iVar1;
  ssize_t sVar2;
  long in_FS_OFFSET;
  undefined8 local_88;
  undefined8 local_80;
  undefined8 local_78;
  undefined5 local_70;
  undefined3 uStack_6b;
  undefined5 uStack_68;
  char local_58 [72];
  long local_10;
  
  local_10 = *(long *)(in_FS_OFFSET + 0x28);
  local_88 = 0x5f30343166635f75;
  local_80 = 0x396c5f745f626463;
  local_78 = 0x3575797d30793474;
  local_70 = 0x6b74357527;
  uStack_6b = 0x637b31;
  uStack_68 = 0x30697972;
  printf("Flag: ");
  fflush(stdout);
  sVar2 = read(0,local_58,0x40);
  iVar1 = (int)sVar2 + -1;
  local_58[iVar1] = '\0';
  enc(local_58,iVar1);
  iVar1 = strncmp(local_58,(char *)&local_88,0x24);
  if (iVar1 == 0) {
    printf("Right");
  }
  else {
    printf("Wrong");
  }
  if (local_10 != *(long *)(in_FS_OFFSET + 0x28)) {
                    /* WARNING: Subroutine does not return */
    __stack_chk_fail();
  }
  return 0;
}
```
After editing some names it becomes:
```c

undefined8 main(void)

{
  int iVar1;
  ssize_t sVar2;
  long in_FS_OFFSET;
  undefined8 scrambled;
  undefined8 local_80;
  undefined8 local_78;
  undefined5 local_70;
  undefined3 uStack_6b;
  undefined5 uStack_68;
  char input [72];
  long local_10;
  
  local_10 = *(long *)(in_FS_OFFSET + 0x28);
  scrambled = 0x5f30343166635f75;
  local_80 = 0x396c5f745f626463;
  local_78 = 0x3575797d30793474;
  local_70 = 0x6b74357527;
  uStack_6b = 0x637b31;
  uStack_68 = 0x30697972;
  printf("Flag: ");
  fflush(stdout);
  sVar2 = read(0,input,64);
  iVar1 = (int)sVar2 + -1;
  input[iVar1] = '\0';
  enc(input,iVar1);
  iVar1 = strncmp(input,(char *)&scrambled,36);
  if (iVar1 == 0) {
    printf("Right");
  }
  else {
    printf("Wrong");
  }
  if (local_10 != *(long *)(in_FS_OFFSET + 0x28)) {
                    /* WARNING: Subroutine does not return */
    __stack_chk_fail();
  }
  return 0;
}
```
Here, we can see a input is taken upto 64 characters and it is entered into the enc function and then it is compared with the scrambled.
After analyzing the scrambled, we see that, they are consists of 8 * 3 + 5 + 5 + 3 = 37 characters so which does makes sense with the strncmp length that we can see in the function. So, probably the flag is of 36 char in length.
Changing scrambled into `char[37]` we get:
```
  scrambled[0] = 'u';
  scrambled[1] = '_';
  scrambled[2] = 'c';
  scrambled[3] = 'f';
  scrambled[4] = '1';
  scrambled[5] = '4';
  scrambled[6] = '0';
  scrambled[7] = '_';
  scrambled[8] = 'c';
  scrambled[9] = 'd';
  scrambled[10] = 'b';
  scrambled[11] = '_';
  scrambled[12] = 't';
  scrambled[13] = '_';
  scrambled[14] = 'l';
  scrambled[15] = '9';
  scrambled[16] = 't';
  scrambled[17] = '4';
  scrambled[18] = 'y';
  scrambled[19] = '0';
  scrambled[20] = '}';
  scrambled[21] = 'y';
  scrambled[22] = 'u';
  scrambled[23] = '5';
  scrambled[24] = '\'';
  scrambled[25] = 'u';
  scrambled[26] = '5';
  scrambled[27] = 't';
  scrambled[28] = 'k';
  scrambled[29] = '1';
  scrambled[30] = '{';
  scrambled[31] = 'c';
  scrambled[32] = 'r';
  scrambled[33] = 'y';
  scrambled[34] = 'i';
  scrambled[35] = '0';
  scrambled[36] = '\0';
```
Now it looks easy!

Lets go to the enc function and change the name of the variables to an extent:
```c

void enc(char *str,int str_len)

{
  int iVar1;
  int i;
  
  for (i = 0; i < str_len; i = i + 1) {
    iVar1 = lucky(i);
    swap(str + i,str + iVar1 % str_len);
  }
  return;
}
```
here we can see it iterates upto the string length and makes a new integer from the iterator i using the lucky function and swaps the two characters.

Lets look at the lucky function then:
```c

int lucky(int len)

{
  int j;
  int m;
  int i;
  int k;
  
  j = 1;
  m = 0;
  do {
    if (len <= m) {
      return j;
    }
    k = 0;
    j = j + 1;
    for (i = 2; i <= j; i = i + 1) {
      if (j % i == 0) {
        k = k + 1;
      }
      if (1 < k) break;
    }
    if (k == 1) {
      m = m + 1;
    }
  } while( true );
}
```
Here, we can see that it creates a new integer from the given integer.

There can be multiple swaps of the same character with multiple value. So, instead  of making a reverse code for luck let's look at something. We see that there is not any randomness in the code. Therefore, for a given set of input of the same length it will always swap it to a fixed index. So, for any input of same input we will get same types of swaps which means the index remains the same. 

#### Reversing the string:
here we create a string of 36 characters: 
`ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghij`

then we run gdb and give it as input and we put breakpoint just before the string compare function or we can give it just after it returns from enc function.
After disassembling main we put a breakpoint just after it finishes enc
```asm
   0x000055555555531e <+175>:	mov    edx,DWORD PTR [rbp-0x84]
   0x0000555555555324 <+181>:	lea    rax,[rbp-0x50]
   0x0000555555555328 <+185>:	mov    esi,edx
   0x000055555555532a <+187>:	mov    rdi,rax
   0x000055555555532d <+190>:	call   0x555555555211 <enc>
   0x0000555555555332 <+195>:	lea    rcx,[rbp-0x80]
   0x0000555555555336 <+199>:	lea    rax,[rbp-0x50]
   0x000055555555533a <+203>:	mov    edx,0x24
   0x000055555555533f <+208>:	mov    rsi,rcx
   0x0000555555555342 <+211>:	mov    rdi,rax
   0x0000555555555345 <+214>:	call   0x555555555030 <strncmp@plt>

```
placing breakpoint
```
break *main+195
```
After some instructions in instruction main+199 we see that the input string's effective address is loaded into rax and so after checking rax we find the following scrambled input string.
`$rax   : 0x00007fffffffdda0  → "BaDFHcNWTXdQCLRfEYZbjMOgJSKIUhGiPVAe"`
Therefore our scrambled input string is:
`BaDFHcNWTXdQCLRfEYZbjMOgJSKIUhGiPVAe`

After comparing it with our input file by matching the index we get from
`ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghij` -> `BaDFHcNWTXdQCLRfEYZbjMOgJSKIUhGiPVAe`

and for our flag:
`u_cf140_cdb_t_l9t4y0}yu5'u5tk1{cryi0` -> `iutctf{1t'5_y0ur_lucky_d4y_04b0951c}`

Though I could make a script for that but I compared and placed it manually.

#### Flag:
Thus our flag is: `iutctf{1t'5_y0ur_lucky_d4y_04b0951c}`

