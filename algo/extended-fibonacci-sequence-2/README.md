# extended-fibonacci-sequence-2 (186 solves / 417 points)
## Description
fibs r so much fun here's another one

``nc extended-fibonacci-sequence-2.hsc.tf 1337``

[Extended Fibonacci Sequence 2 ACTUALLY ACTUALLY ACTUALLY NEW.pdf](https://hsctf.storage.googleapis.com/uploads/e1e657e860490efcece6c5cd6efec35c90e4e1882f778675f1ea09274810b2e5/Extended%20Fibonacci%20Sequence%202%20ACTUALLY%20ACTUALLY%20ACTUALLY%20NEW.pdf)
## Solution
This challenge presented no difficulty as I used a practically carbon copy of my [extended-fibonacci-sequence](https://github.com/BASHing-thru-challenges/HSCTF-2021-Writeups/tree/main/algo/extended-fibonacci-sequence) script.
All I did was give ``fib(n)`` new base statements and change ``exFib(n)`` to add instead of concatenate. Once again I used pwntools for nc server interaction.

Final Script:
```python3

from pwn import *
import functools
import re
import time

@functools.lru_cache(None)
def fib(n):
    if n == 0:
        return 4
    elif n == 1:
        return 5
    return fib(n - 1) + fib(n - 2)

@functools.lru_cache(None)
def exFib2(n):
    if n <= 0:
        return 0 + fib(n)
    return exFib2(n - 1) + fib(n)

def addList(list):
    total = 0
    for num in list:
        total += num
    return total

io = process(['nc', 'extended-fibonacci-sequence-2.hsc.tf', '1337'])
io.recvline()
while True:
    while True:
        try:
            data = io.recvline().decode()
            print(data)
        except EOFError:
            print(data)
            quit()
        match = re.search("(\d{1,})\n", data)
        if match is not None:
            break
    n = int(match.group(0))
    print("[RECIEVE]: " + str(n))
    summ = []
    for i in range(0, n + 1):
        summ.append(exFib2(i))
    final = str(addList(summ))
    final = final[-10:]
    io.sendline(final.lstrip('0'))
    print("[SEND]: " + final)

```
Run the script... and boom, flag!
## Flag
``flag{i_n33d_a_fl4g._s0m3b0dy_pl3ase_giv3_m3_4_fl4g.}``
