---
categories:
  - CTF Writeup
  - Reversing
tags:
  - TryHackMe
---

## Crackme1
Let's start with a basic warmup, can you run the binary?

Make the permission of the binary to executable then run it. 
![](https://i.imgur.com/pb5Dtvg.png)
## Crackme2
Find the super-secret password! and use it to obtain the flag
1. What is the super secret password ?
   Opening up ghidra, we can see the check that must be satisfied to get the flag. 
   ![](https://i.imgur.com/EY65huw.png)
2. What is the flag?
   Using the new found password with the command `./crackme2 super_secret_password` we get the flag. 

## Crackme3
Use basic reverse engineering skills to obtain the flag

An easy way to solve this, its what I did, was to use `strings`. When your given a binary, one of the first commands to use and one of the basic reverse engineering skills is to use the `strings` command. Because there are times where the it outputs sensitive data like passwords. In this case when I ran strings on the binary I saw a base64 encoded value. 
![](https://i.imgur.com/vb4O8pR.png)

decoding this with the `base64 -d` command we get the flag. 
```bash
 echo -n "ZjByX3kwdXJfNWVjMG5kX2xlNTVvbl91bmJhc2U2NF80bGxfN2gzXzdoMW5nNQ==" | base64 -d
```

## Crackme4
Analyze and find the password for the binary?
What is the password?

I solved this using GDB. The first thing I did was disassembled the main function and there at offset `+62` relative to the main function I saw a `compare_pwd`
![](https://i.imgur.com/oeCnzBW.png)

Looking at the assembly code for the `compare_pwd` function I noticed the use of a string compare function.  Before that there was a `get_pwd` function which gets the password from the user, then I could infer that after getting the password, the program would then compare it to the correct password.
![](https://i.imgur.com/PG1akRj.png)

So the next step I did was to set a break point at `0xx00000000004006d5` so that I would see, at that point, the values of the registers. Then I ran the program with `test` as the argument. There I saw the password used. 
![](https://i.imgur.com/HGZUR7t.png)
There you can see that my argument `test` was being compared to the correct password `my_m0r3_secur3_pwd`

We can verify that by running it as the argument:
![](https://i.imgur.com/JT0P9D4.png)
## Crackme5
What will be the input of the file to get output `Good game`?

What is the input?

This is where I introduce a tool called `ltrace`. So `ltrace` is a Linux utility that traces the dynamic library calls a program makes during execution. It helps in debugging, reverse engineering, and understanding how a program interacts with shared libraries by showing function calls, arguments, and return values in real-time.

As you can see in the 2nd argument of the `strncmp` function is the password. 
![](https://i.imgur.com/PSZgAGO.png)

## Crackme6
Analyze the binary for the easy password

What is the password ?

So for I ran ghidra and followed the data process. First in the main function, the `compare_pwd` was callled. Then looking at the decompilation code for that function we see that `my_secure_test` function was called. In that function we can actually see the password. 
![](https://i.imgur.com/VCHGqYV.png)

We can verify this by passing it to the program. 
![](https://i.imgur.com/vU0SPDf.png)
## Crackme7
Analyze the binary to get the flag

What is the flag?

I used ghidra to look at the decompilation of the code. There you can see that if I input `31337` which is `0x7a69` in hex, it runs the giveFlag() function. 
![](https://i.imgur.com/Q8zQqjT.png)

Inputting 31337 will give us the flag. 
![](https://i.imgur.com/hrwz3y5.png)

## Crackme8
Analyze the binary and obtain the flag

What is the flag ?

This is similar to the previous question, the only difference is we now have to input a negative number to get the flag. 

![](https://i.imgur.com/PprQ48f.png)
