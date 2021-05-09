<h1>Secret App</h1>
Points: 50

<h1>Category</h1>
Reverse Engineering

<h1>Problem</h1>
<h3>I hid my flag in a secret app but I forgot what my username and password are.<br/>
 Author: Percival</h3>

<h1>Solution</h1>

![Alt text](https://i.imgur.com/tgR5s6w.png "Title")

![Alt text](https://i.imgur.com/Hk055Ln.png "Title")


We can see that our input username after fgets call is saved into ebp+var_21C local var <br/>

```assembly
mov     esi, esp
push    eax             ; Stream
push    100h            ; MaxCount
lea     eax, [ebp+Buffer]
push    eax             ; Buffer
call    ds:fgets
add     esp, 0Ch
cmp     esi, esp
call    j___RTC_CheckEsp
mov     [ebp+var_21C], eax
cmp     [ebp+var_21C], 0
jnz     short loc_411A92
```

Then is compared in function what i named isNot_UsernameZero

```assembly
loc_411A92:
push    offset asc_417BC0 ; "\n"
call    print
add     esp, 4
mov     eax, [ebp+var_21C]
push    eax             ; Str1
call    j_isNot_UsernameZero
add     esp, 4
test    eax, eax
jnz     short loc_411AC9
```

It's just some wrapper fro strcmp function that compare our input username with "not_username"

![Alt text](https://i.imgur.com/xXfSqkK.png "Title")

The same situation is repeated for entering the password - checks if the password is "not_password"

![Alt text](https://i.imgur.com/vCoWnbz.png "Title")

As we can see that's all checks before decrypt function is called, we do not care about another things <br/>

If we enable our exe and input these two valid things we got the flag<br/>

Flag: <b>DawgCTF{4app_sup3r_53cret}</b>


