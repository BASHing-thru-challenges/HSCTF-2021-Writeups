# canis-lupus-familiaris-bernardus (112 solves, 456 points)

## Description:
Wait, why isn't it a species of bird this time?

```nc canis-lupus-familiaris-bernardus.hsc.tf 1337```

[canis-lupus-familiaris-bernardus.py](canis-lupus-familiaris-bernardus.py)

## Solution:
Looking at the script, we see that a valid peptide has to only have letters in "ABCDEFGHIKLMNPQRSTVWYZ" and the program will randomly give us invalid or valid peptides. But when the peptide is invalid, it asks us to change the iv to create a valid one through AES. We can do this through some xor manipulation. 

The final stage of AES-CBC is an xor between the final round and the iv. So, we can recover the previous stage by xoring the invalid peptide with the given iv. Then, we can xor  that with a valid peptide to get a valid iv. Repeating this 100 times, we will get the flag. Unfortunately, I could not fix EOF Errors coming from pwntools so I put it in a while true until I got the flag.

**My Script:**

```python3
from pwn import *

bad_chars = list("JOUX")
p = remote('canis-lupus-familiaris-bernardus.hsc.tf', 1337)
count = 0
while(True):
    try:
        p.recvuntil('V\n')
        last = True
        p.recvline() 
        while count < 100:
            print(count)
            resp = p.recvuntil('? ')
            if not last:
                resp = resp[26:]
            resp = resp[3:19]
            print(resp)
            validPep = resp.decode('UTF-8')
            good = True
            for char in bad_chars:
                if char in validPep:
                    good = False
                    validPep = validPep.replace(char, "A")
            if good:
                p.send('T\n')
                p.recvuntil('!\n')
            else:
                p.send('F\n')
                iv = p.recvuntil('e: ')
                iv = iv[24:56].decode('UTF-8') 
                hexed = "".join("{:02x}".format(c) for c in resp)
                hexed2 = "".join("{:02x}".format(c) for c in validPep.encode('UTF-8'))
                xor = hex((int(hexed,16) ^ int(iv,16)) ^ int(hexed2,16))
                p.send(xor[2:] + '\n')
            count = count + 1
            last = good
        print(p.recv())
        p.close()
        break
    except:
        print("EOF Error! Starting again")
        count = 0
        p.close()
        p = remote('canis-lupus-familiaris-bernardus.hsc.tf', 1337)
```

## Flag:
`flag{WATCHING_PPL_GET_PEPTIDED_IS_A_VALID_PEPTIDE}`
