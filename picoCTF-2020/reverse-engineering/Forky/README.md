<h1>Forky</h1>
Points: 500

<h1>Category</h1>
Reverse Engineering

<h1>Problem</h1>
<h3>In this program, identify the last integer value that is passed as parameter to the function doNothing().</h3>

<h1>Code</h1>

![Alt text](https://i.imgur.com/mQHwHgs.png?raw=true "Title")

<h1>Solution</h1>

```assembly
add     ebx, 1A57h
mov     [ebp+var_14], 3
mov     [ebp+var_10], 21h ; '!'
sub     esp, 8
push    0
push    0FFFFFFFFh
push    [ebp+var_10]
push    [ebp+var_14]
push    4
push    0
call    _mmap
```

Let's look on mmap function definition (https://man7.org/linux/man-pages/man2/mmap.2.html)

```C
void *mmap(void *addr, size_t length, int prot, int flags, int fd, off_t offset);
```

I wrote similar C code to our assembly and print that

```C
int *var;
var = mmap(0x0, 4, 3, 0x21, 0x0FFFFFFFF, 0);
printf("Value: %i\n", *var);
```

Execution of this code returned <b>0</b>

```assembly
add     esp, 20h
mov     [ebp+var_C], eax
mov     eax, [ebp+var_C]
mov     dword ptr [eax], 3B9ACA00h
```

var_C is our local int variable, in this code we just set 0x3B9ACA00 value.

```assembly
call    _fork
call    _fork
call    _fork
call    _fork
```

fork function in C (https://www.geeksforgeeks.org/fork-system-call/)

After read this article we know that our code above will execute 4^2(16) times

```assembly
mov     eax, [ebp+var_C]
mov     eax, [eax]
lea     edx, [eax+499602D2h]
mov     eax, [ebp+var_C]
mov     [eax], edx
mov     eax, [ebp+var_C]
mov     eax, [eax]
sub     esp, 0Ch
push    eax
call    doNothing
```

To our variable pointer we add 0x499602D2 address. So our solution in C code can be similar to this:

```C

#include <stdio.h>
#include <unistd.h>
#include <sys/mman.h>
int main(){

        int *var;
        var = mmap(0x0, 4, 3, 0x21, 0x0FFFFFFFF, 0);
        printf("Value %i\n", *var);
        *var = 1000000000;
        for(int i=0; i<16; i++){
                *var = *var + 0x499602d2;
        }
        printf("FLAG: %i\n", *var);
        return 0;
}
```


Output: <b>FLAG: -721750240</b>





