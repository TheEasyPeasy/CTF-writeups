<h1>Who am I?</h1>
Points: 100

<h1>Category</h1>
Reverse Engineering

<h1>Problem</h1>
<h3>I hid my flag in this program but it seems to have forgot who it is?<br/>
 Author: Percival</h3>

<h1>Solution</h1>

The first association when I saw the code and hint from IDA about switch statements - control flow-flattening.
In a way, yes, but the switch was very simple, fortunately. <br/>

![Alt text](https://i.imgur.com/IzrTSRT.png "Title")


While browsing the code in general, I saw some external calli like calloc etc. But my attention was caught by "getpid" <br/>
<b>[ebp+pidNumber]</b> is my local var that i named before, when i saw that this function read PID number from arg[1], and then named function readPid

![Alt text](https://i.imgur.com/VqQ5d2q.png "Title")

I had assumptions that this would be the solution to the problem. Looking at other decryption functions etc. I didn't see any point in reversing it all

![Alt text](https://i.imgur.com/ae13yxi.png "Title") <br />

But problem is - <b>how can we know our PID number before process start?</b><br/>

We don't need, because this is compared in code - we can patch it. <br />

Look at this function (original)

![Alt text](https://i.imgur.com/tPczu9N.png "Title")

it compare our PID with input arg[1] if it's same just jump, we can circumvent it by change jump flow <br />

I will show you how to do it with IDA

![Alt text](https://i.imgur.com/W9IzzLh.png "Title")

And change
```assembly
jnz     short loc_411BC1
```
to

```assembly
jz     short loc_411BC1
```

so now if our PID is wrong, it will treat it as valid <br/>

When i was solving this task i did it in similar way - changed this block name to "validJump" and then

```assembly
jz     short loc_411BC1
```

changed to:

```assembly
jmp	   validJump
```

Finally, enable our exe with 123 argument it will give us flag<br/>

Flag: <b>DawgCTF{hU_4M-3y3?}</b>

**I honestly thought at the beginning that something was wrong with the flag by question mark, without checking it, I was rummaging around and reversing decryption functions: D**


