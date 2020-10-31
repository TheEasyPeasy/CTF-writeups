<h1>asm3</h1>
Points: 300

<h1>Category</h1>
Reverse Engineering

<h1>Problem</h1>
<h3>What does asm3(0xba6c5a02,0xd101e3dd,0xbb86a173) return? Submit the flag as a hexadecimal value (starting with '0x'). NOTE: Your submission for this question will NOT be in the normal flag format. Source</h3>

<h1>Code</h1>

```assembly
asm3:
	<+0>:	push   ebp
	<+1>:	mov    ebp,esp
	<+3>:	xor    eax,eax
	<+5>:	mov    ah,BYTE PTR [ebp+0xb]
	<+8>:	shl    ax,0x10
	<+12>:	sub    al,BYTE PTR [ebp+0xd]
	<+15>:	add    ah,BYTE PTR [ebp+0xc]
	<+18>:	xor    ax,WORD PTR [ebp+0x12]
	<+22>:	nop
	<+23>:	pop    ebp
	<+24>:	ret    
```

<h1>Solution</h1>
This time we compile our code and display the solution<br/>
Let's create our asm3.S file and write into it our assembly code<br/>

```assembly
.intel_syntax noprefix
.global asm3

asm3:
	push   ebp
	mov    ebp,esp
	xor    eax,eax
	mov    ah,BYTE PTR [ebp+0xb]
	shl    ax,0x10
	sub    al,BYTE PTR [ebp+0xd]
	add    ah,BYTE PTR [ebp+0xc]
	xor    ax,WORD PTR [ebp+0x12]
	nop
	pop    ebp
	ret
```

Now we need to create simple C program:

```C
#include <stdio.h>

int asm3(int, int, int);

int main(int argc, char* argv[])
{
    printf("0x%x\n", asm3(0xba6c5a02,0xd101e3dd,0xbb86a173));
    return 0;
}
```

Compile and run:

```bash
gcc -masm=intel -m32 -c asm.S -o asm.o
gcc -m32 -c main.c -o main.o
gcc -m32 asm.o main.o -o main
./main
```

Solution: <b>0x669b</b>
