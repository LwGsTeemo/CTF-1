### EasyCTF_2018: Adder
```
Category: Reverse Engineering Points: 80 Description:

This program adds numbers. Find the flag! adder

Write-up
This challenge is really simple, just load up the decompiler,

│           0x00400b9c      01d0           add eax, edx
│           0x00400b9e      3d39050000     cmp eax, 0x539              ; 1337
│       ┌─< 0x00400ba3      7527           jne 0x400bcc
Add 3 numbers!

# ./adder 
Enter three numbers!
0
1000
337
easyctf{y0u_added_thr33_nums!}
Therefore, the flag is easyctf{y0u_added_thr33_nums!}
```

### EasyCTF_2018: EzReverse
```
Category: Reverse Engineering Points: 140 Description:

Take a look at executable. Objdump the executable and read some assembly!

Write-up
This challenge was great fun and lots of experience was gained in the arts of radare2. In a nutshell, this program accepts 5 characters as an argument but if any arguments are wrong, it self-deletes. To get around this, I made a patched binary that would not do that :D.

Essentially, of the 5 characters, the fourth character is always k and the rest are all very strongly tied to one another. An excerpt is shown below,

│      │    0x0040090e      8b45ec         mov eax, dword [local_14h]
│      │    0x00400911      83f86f         cmp eax, 0x6f               ; 'o' ; 111
│      │┌─< 0x00400914      7551           jne 0x400967
│      ││   0x00400916      8b45e8         mov eax, dword [local_18h]
│      ││   0x00400919      8b55ec         mov edx, dword [local_14h]
│      ││   0x0040091c      83c20e         add edx, 0xe
│      ││   0x0040091f      39d0           cmp eax, edx
│     ┌───< 0x00400921      7544           jne 0x400967
│     │││   0x00400923      8b45e0         mov eax, dword [local_20h]
│     │││   0x00400926      8b55f0         mov edx, dword [local_10h]
│     │││   0x00400929      83ea0a         sub edx, 0xa
│     │││   0x0040092c      39d0           cmp eax, edx
│    ┌────< 0x0040092e      7537           jne 0x400967
│    ││││   0x00400930      8b45e4         mov eax, dword [local_1ch]
│    ││││   0x00400933      83f835         cmp eax, 0x35               ; '5' ; 53
│   ┌─────< 0x00400936      752f           jne 0x400967
│   │││││   0x00400938      8b45f0         mov eax, dword [local_10h]
│   │││││   0x0040093b      8b55ec         mov edx, dword [local_14h]
│   │││││   0x0040093e      83c203         add edx, 3
│   │││││   0x00400941      39d0           cmp eax, edx
│  ┌──────< 0x00400943      7522           jne 0x400967
│  ││││││   0x00400945      bf660a4000     mov edi, str.Now_here_is_your_flag: ; 0x400a66 ; "Now here is your flag: " ; const char * format
Additionally, there is tricky above that adds offsets to all integers, according to their placements,

│      │    0x00400881      c745e0010000.  mov dword [local_20h], 1
│      │    0x00400888      c745e4020000.  mov dword [local_1ch], 2
│      │    0x0040088f      c745e8030000.  mov dword [local_18h], 3
│      │    0x00400896      c745ec040000.  mov dword [local_14h], 4
│      │    0x0040089d      c745f0050000.  mov dword [local_10h], 5
As such, by running the command with our full argument, we get our flag,

# ./executable_patched g3zkm
Now here is your flag: 10453125111114
Therefore, the flag is 10453125111114.
```
