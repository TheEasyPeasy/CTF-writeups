<h1>Calculator</h1>
Points: 50

<h1>Category</h1>
Reverse Engineering

<h1>Problem</h1>
<h3>I hid my flag in a calculator but I forgot what the magic number is... <br/>
 Author: Percival</h3>

<h1>Solution</h1>

In main function two calls caught my attention - readInput, at first during the static analysis I thought it was about arguments - nothing more wrong, it was about file names, which we'll get to in a moment <br />

We can see there that program read two input arguments<br/>

![Alt text](https://i.imgur.com/O6ikIyV.png "Title")

If we meet conditions and put in two arguments we will jump into our desired code snippet

![Alt text](https://i.imgur.com/BHp7sZw.png "Title")

First of all look at compare thing

```assembly
mov     eax, [ebp+var_C]
imul    eax, [ebp+var_18]
cmp     eax, 64
```

We know that ebp+var_c and ebp+var_18 is our value returned from readInput functions, because eax has been saved into ebp+var_C after call

```assembly
mov     eax, 4
shl     eax, 0
mov     ecx, [ebp+argv]
mov     edx, [ecx+eax]
push    edx             ; FileName
call    readInput
add     esp, 4
mov     [ebp+var_C], eax
```

So we know that our numbers multiplied by themselves must give us the number 64 <br/>

Let's look into readInput function <br/>

![Alt text](https://i.imgur.com/4t2LdU2.png "Title")

I just looked at it and figured it would just read the number from any file and since two numbers must give us 64 - I made the number.txt file, typed the number 8, fired the program and it worked fine. <br/>

./calculator.exe numbers.txt numbers.txt <br/>

Flag: <b>DawgCTF{c4LcU14T0r_64}</b>


