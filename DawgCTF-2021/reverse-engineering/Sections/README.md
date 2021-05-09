<h1>Sections</h1>
Points: 75

<h1>Category</h1>
Reverse Engineering

<h1>Problem</h1>
<h3>I hid my flag in a... oh you get the point by now<br/>
 Author: Percival</h3>

<h1>Solution</h1>

If we look into main function we have only information that we need to XOR section data to get the flag. <br/>

![Alt text](https://i.imgur.com/eTd1ZXu.png "Title")


Let's look on section data in IDA Freeware (SHIFT + B7)

![Alt text](https://i.imgur.com/j5BpMS5.png "Title")

Double click and check out section data

![Alt text](https://i.imgur.com/53RZEhD.png "Title")


We can see .flag section, let's write simple program to XOR data.

```C
	puts("Start decrypting...");
	for (int j = 0; j < 128; j++)
	{
		unsigned char s[] = { 0x19, 0x0F, 0x1F, 0x3B, 0x2C, 0x3E, 0x3, 0x4D, 0x4B, 0x1B, 0x0C, 0x11, 0x17, 0x16
		, 0x0B, 0x59, 0x59 };
		int size = sizeof s / sizeof s[0];
		for (int i = 0; i < size; i++)
		{
			s[i] = s[i] ^ j;
		}
		printf("string %s\n", s);
	}
```

Our output has a lot of garbage strings, but inside them we could see our flag <br/>

Flag: <b>DawgCTF{53ctions!!}</b>


