<h1>reverse_cipher</h1>
Points: 400

<h1>Category</h1>
Reverse Engineering

<h1>Problem</h1>
<h3>We have recovered a binary and a text file. Can you reverse the flag.</h3>

<h1>Solution</h1>

Let's start and open binary in any disassembler in my case it would be IDA Freeware


![Alt text](https://i.imgur.com/pX3Z6ZY.png?raw=true "Title")


First of all we have 

```assembly
lea     rsi, modes      ; "r"
lea     rdi, filename   ; "flag.txt"
call    _fopen
```

and 

```assembly
lea     rsi, aA         ; "a"
lea     rdi, aRevThis   ; "rev_this"
call    _fopen
```


First file flag.txt is opened in read mode, and the second one in append mode.

```assembly
call    _fopen
mov     [rbp+revFilePointer], rax
cmp     [rbp+flagFilePointer], 0
jnz     short loc_11CE
```

```assembly
loc_11CE:
cmp     [rbp+revFilePointer], 0
jnz     short loc_11E1
```

In both cases we check that FILE is not null. I renamed that variables into more readable names revFilePointer and flagFilePointer.

![Alt text](https://i.imgur.com/5ozxWQL.png?raw=true "Title")

```assembly
loc_11E1:
mov     rdx, [rbp+flagFilePointer]
lea     rax, [rbp+ptr]
mov     rcx, rdx        ; stream
mov     edx, 1          ; n
mov     esi, 18h        ; size
mov     rdi, rax        ; ptr
call    _fread
mov     [rbp+var_24], eax
cmp     [rbp+var_24], 0
jg      short loc_1211
```

Let's look on C function fread:
```C
size_t fread(void *ptr, size_t size, size_t nitems, FILE *stream);
```

*ptr is pointer to char array
size_t is size of element array
nitems is amount of elements to read
stream is pointer to FILE

We can see that we are going to operate the flag.txt file.


![Alt text](https://i.imgur.com/XLCCn9m.png?raw=true "Title")


```assembly
loc_1211:
mov     [rbp+var_8], 0
jmp     short loc_123D

loc_123D:
cmp     [rbp+var_8], 7
jle     short loc_121A
```


```assembly
loc_121A:
mov     eax, [rbp+var_8]
cdqe
movzx   eax, [rbp+rax+ptr]
mov     [rbp+var_1], al
movsx   eax, [rbp+var_1]
mov     rdx, [rbp+revFilePointer]
mov     rsi, rdx        ; stream
mov     edi, eax        ; c
call    _fputc
add     [rbp+var_8], 1
```

We have loop there local integer is incremented, starting from 0 to 7. 

That function just write <b>PicoCTF</b> into our char array


```assembly
mov     [rbp+var_C], 8
jmp     short loc_128F

loc_128F:
cmp     [rbp+var_C], 16h
jle     short loc_124C
```



Next loop with increment local integer starting from 0x8 to 0x16

![Alt text](https://i.imgur.com/fqmSzcW.png?raw=true "Title")

```assembly
loc_124C:
mov     eax, [rbp+var_C]
cdqe
movzx   eax, [rbp+rax+ptr]
mov     [rbp+var_1], al
mov     eax, [rbp+var_C]
and     eax, 1
test    eax, eax
jnz     short loc_126F

movzx   eax, [rbp+var_1]
add     eax, 5
mov     [rbp+var_1], al
jmp     short loc_1279

loc_126F:
movzx   eax, [rbp+var_1]
sub     eax, 2
mov     [rbp+var_1], al

loc_1279:
movsx   eax, [rbp+var_1]
mov     rdx, [rbp+revFilePointer]
mov     rsi, rdx        ; stream
mov     edi, eax        ; c
call    _fputc
add     [rbp+var_C], 1
```

<b>loc_124C</b> take our local int - init value of this integer was <b>8</b> and do AND operation by 1.<br />
1.If the result of this operation is not zero we subtract from our char 2<br />
2.If the result of this operation is zero we add 5 to our char<br />

```assembly
movzx   eax, [rbp+var_39]
mov     [rbp+var_1], al
movsx   eax, [rbp+var_1]
mov     rdx, [rbp+revFilePointer]
mov     rsi, rdx        ; stream
mov     edi, eax        ; c
call    _fputc
mov     rax, [rbp+revFilePointer]
mov     rdi, rax        ; stream
call    _fclose
mov     rax, [rbp+flagFilePointer]
mov     rdi, rax        ; stream
call    _fclose
nop
leave
retn
main endp
```

We can skip last block of code, it's not important for us.

<b>We need copy flag.txt file and name it to rev_this</b>

In rev_this we have copied flag from flag.txt and enable our ./rev binary - Nothing happened? It's okay!
Look into your rev_this file - we got 2 flags
```
picoCTF{w1{1wq8/7376j.:}
picoCTF{|/�/|o=-<1<4o,?}
```
One is our default flag from flag.txt second is generated by our binary file.

We have a full analysis of this file, so let's write a program that will give us the correct flag based on the flag from the flag.txt file.


<b>loc_124C</b> take our local int - init value of this integer was <b>8</b> and do AND operation by 1.<br />
1.If the result of this operation is not zero we subtract from our char 2<br />
2.If the result of this operation is zero we add 5 to our char<br />




<b>Be careful! To encrypt we SUBSTRACT 2 and ADD 5 - To decrypt we need ADD 2 and SUBSTRACT 5. THE ACTION MUST BE OPPOSITE!</b>

( ͡° ͜ʖ ͡°)

```java
final char[] array = "w1{1wq8/7376j.:".toCharArray();
        final char[] decryptArray = new char[32];
        int local_14 = 0;
        for(int i=0; i<array.length; i++){
            if((local_14 & 1) == 0){
                char c = (char) array[i];
                char c2 = (char) (c - 5);
                decryptArray[i] = c2;
            }
            else{
                char c = (char) array[i];
                char c2 = (char) (c + 2);
                decryptArray[i] = c2;
            }
            local_14 = local_14 + 1;
        }
        String finalString = new String(decryptArray);
        System.out.println("picoCTF{" + finalString + "}");
        }
```        
        
Our flag: <b>picoCTF{r3v3rs312528e05}</b>






