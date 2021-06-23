# aptenodytes-forsteri (597 solves, 183 points)
## Description
Here's a warmup cryptography challenge. Reverse the script, decrypt the output, submit the flag.

[aptenodytes-forsteri.py](https://hsctf.storage.googleapis.com/uploads/2bf18e2c8c9525f03b830291047fb00942b35d9b91feb2751680d97329aef749/aptenodytes-forsteri.py)
[output.txt](https://hsctf.storage.googleapis.com/uploads/30bab898df93535d5c5685a35810721f6472135d3d8cf62fe7e6f1e1b1b44646/output.txt)
## Solution
After looking at the initial python file and running some test values, I realised that there is another 1 to 1 relationship between plaintext and ciphertext.
This means I don't have to reverse the script at all. All I had to do was run every ascii keyboard character through the algorithm to create a lookup table, then match each
output to its input in the dictionary.

Here's the script to do so:
```python3

import time
from Crypto.Util.number import *
import linecache
keyText = "abcdefghijklmnopqrstuvwxyz1234567890!@#$%^&*()-=_+ABCDEFGHIJKLMNOPQRSTUVWXYZ[]{}`~'\"\\ /?.>,<"
key = {}
def encrypt():
    p = getPrime(1024)
    q = getPrime(1024)
    e = 2**16+1
    n=p*q
    ct=[]
    i = 0
    for ch in keyText:
        ct.append((ord(ch)^e)%n)
        key[ch] = str(ct[i])
        i += 1

encrypt()
plainText = list(key.keys())
cypherText = list(key.values())
data = linecache.getline('hoazin.txt', 4)
flag = data.split(', ')
flag[-1] = flag[-1].rstrip()
flagText = ""
for ct in flag:
    pos = cypherText.index(ct)
    flagText += plainText[pos]
print(flagText)

```
## Flag
``flag{tH1s_ic3_cr34m_i5_So_FroZ3n_i"M_pr3tTy_Sure_iT's_4ctua1ly_b3nDinG_mY_5p0On}``
