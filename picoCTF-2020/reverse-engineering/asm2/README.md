<h1>asm2</h1>
Points: 250

<h1>Category</h1>
Reverse Engineering

<h1>Problem</h1>
<h3>What does asm2(0xb,0x2e) return? Submit the flag as a hexadecimal value (starting with '0x'). NOTE: Your submission for this question will NOT be in the normal flag format. Source</h3>

<h1>Code</h1>

```assembly
asm2:
	<+0>:	push   ebp
	<+1>:	mov    ebp,esp
	<+3>:	sub    esp,0x10
	<+6>:	mov    eax,DWORD PTR [ebp+0xc]
	<+9>:	mov    DWORD PTR [ebp-0x4],eax
	<+12>:	mov    eax,DWORD PTR [ebp+0x8]
	<+15>:	mov    DWORD PTR [ebp-0x8],eax
	<+18>:	jmp    0x509 <asm2+28>
	<+20>:	add    DWORD PTR [ebp-0x4],0x1
	<+24>:	sub    DWORD PTR [ebp-0x8],0xffffff80
	<+28>:	cmp    DWORD PTR [ebp-0x8],0x63f3
	<+35>:	jle    0x501 <asm2+20>
	<+37>:	mov    eax,DWORD PTR [ebp-0x4]
	<+40>:	leave  
	<+41>:	ret    
```

<h1>Solution</h1>
We have two parameters in our function - 4 bytes. ebp + 0x4 is <b>RET</b>, ebp + 0x8 is first argument, and ebp + 0xC is second.<br/>
Let's name our variables <b>a</b> and <b>b</b><br/>

```C
void asm2(int a, int b);
```

First of all <b>b</b> is is moved into [ebp - 0x4] and <b>a</b> into [ebp - 0x8]<br/>
We have condition in <+28> if our <b>a</b> is less or equal <b>0x63f3</b> we add <b>0x1</b> to our <b>b</b> variable<br/>
and substract <b>0xffffff80</b> from our <b>a</b> variable

Finally our solution is after that operations. I had wrote simple C code to print our solution.

```C
void asm2(int a, int b){
    while(a <= 0x63f3){
        b = b + 0x1;
        a = a - 0xffffff80;
    }
    printf("Solution 0x%x\n", b);
}

int main() {
    asm2(0xb,0x2e);
    return 0;
}
```

<b>Solution 0xf6</b>
