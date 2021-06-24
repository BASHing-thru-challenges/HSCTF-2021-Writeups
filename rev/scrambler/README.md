# scrambler (100 solves, 461 points)

## Description:
I wrote a program to print the flag, but the output is all scrambled!

[chall.pyc](chall.pyc)

[output.txt](output.txt)

## Solution:
This challenge gave us a .pyc file and an output to reverse. I used a website [decompiler.com](https://www.decompiler.com/) to decompile the .pyc file in a python script and got the following(the website uses uncompyle6)

```python3
# uncompyle6 version 3.7.4
# Python bytecode 3.8 (3413)
# Decompiled from: Python 2.7.17 (default, Sep 30 2020, 13:38:04) 
# [GCC 7.5.0]
# Warning: this version of Python has problems handling the Python 3 "byte" type in constants properly.

# Embedded file name: chall.py
# Compiled at: 2021-04-15 21:21:48
# Size of source mod 2**32: 860 bytes
import random, time
with open('flag.txt') as (f):
    flag = list(f.read())
if len(flag) % 2 == 1:
    flag.append(' ')
x = ['t', 'Y', 'w', 'V', '|', ']', 'u', 'X', '_', '0', 'P', 'k', 'h', 'D', 'A', '4', 'K', '5', 'z',
 'Z', 'G', '7', ';', 'S', ' ', '/', '6', '%', '}', '\\', ',', ':', '>', '#', 'a', '$', '3', '`',
 '+', 'R', 'b', 'H', 'd', 's', '1', 'J', 'L', 'v', '9', '2', 'o', 'M', '<', 'e', '(', 'x', '-',
 'B', 'm', "'", 'y', 'Q', '"', 'W', 'l', '.', 'i', 'O', '^', 'p', '8', 'f', 'F', 'C', '?', 'g',
 '@', 'j', '[', 'r', '!', '=', 'E', '~', '*', 'T', '{', ')', 'U', 'N', 'c', '&', 'n', 'q', 'I']
random.seed(int(time.time()))
for _ in range(20):
    for i in range(len(flag)):
        flag[i] = x[(ord(flag[i]) - 32)]

else:
    random.shuffle(flag)

for i in range(0, len(flag), 2):
    flag[i], flag[i + 1] = flag[(i + 1)], flag[i]
else:
    print(''.join(flag))
```
The first and last part of the script was quite easy to reverse by basic programmming skills, but the main problem I ran into was the `random.seed(int(time.time()))` which randomly shuffled the array based on the time the program executed. I tried the time given by the decompiler, but it didn't work... A hint from the challenge author told me I need to use a bit of bruteforce so I decided to check a day before and after the time given by the decompiler. The fact that it uses int to do time makes it possible. To do this I created a second script to run my solve script in Threads until I found the flag. My scripts are below.

**Solve Script**
```python3
import random
import sys
def decode(text, seed):
    for j in range(0, len(text), 2):
        text[j + 1], text[j],  = text[j], text[j+1]
    nums = []
    for z in range(0, len(text)):
        nums.append(z)
    random.seed(seed)
    random.shuffle(nums)
    #print(nums)
    unshuffled = [None] * len(text)
    for y in range(0, len(text)):
        unshuffled[nums[y]] = text[y]
    for _ in range(20):
        for i in range(len(unshuffled)):
            unshuffled[i] = chr(x.index(unshuffled[i]) + 32)
    return ''.join(unshuffled)
x = ['t', 'Y', 'w', 'V', '|', ']', 'u', 'X', '_', '0', 'P', 'k', 'h', 'D', 'A', '4', 'K', '5', 'z',
 'Z', 'G', '7', ';', 'S', ' ', '/', '6', '%', '}', '\\', ',', ':', '>', '#', 'a', '$', '3', '`',
 '+', 'R', 'b', 'H', 'd', 's', '1', 'J', 'L', 'v', '9', '2', 'o', 'M', '<', 'e', '(', 'x', '-',
 'B', 'm', "'", 'y', 'Q', '"', 'W', 'l', '.', 'i', 'O', '^', 'p', '8', 'f', 'F', 'C', '?', 'g',
 '@', 'j', '[', 'r', '!', '=', 'E', '~', '*', 'T', '{', ')', 'U', 'N', 'c', '&', 'n', 'q', 'I']
file = open('output.txt', 'r')
t = list(file.read().rstrip("\n"))
if __name__ == "__main__":
   print(decode(t, int(sys.argv[1])))
```

**Bruteforce Script**
```python3
import os
import threading
def thread(start, stop):
    print("start")
    for x in range(start, stop):
        output = os.popen("""python -u "c:\\Users\\lukegriffith\\vscode\\ctf\\solve.py" {i}""".format(i = x)).read()
        if 'flag{' in output:
            print(output)
            print(x)
            break
    print("stop")
#using converted times from decompiler, break the 48 hour segment into 24 parts
step = int((1618608108 - 1618435308) / 24) 
for y in range(24):
    t = threading.Thread(target=thread, args = (1618435308 + step * y, 1618435308 + step * (y + 1)))
    t.start()
```
After like a second, I got the following output with the flag.

```
asdfijoewiafj{opfw2eijafewpoi4jfepoijfweapoifejfpoijep2ofjpoeiwajfae}pox{cnkvo3ivnopifiopnqdfaisjiposdfajifoaiweifjeeeeeewpjwefoipwefjpewofijfepoiwefjpofeijefpwoijeoiejepooeiopew flag{71me5t4mp_fun} ijapdiofjaewp_iojnoewnvpoifpoie_wbpaoibjfpaoiwbfoboawebfbiefaowefbjopiaewfjefeb_anieaiebn_faoebf2a2222aniopni2poabn2fbwnifabwfebnibfaepaebfiabfine2a5ebonfifbw8aeniafbe9asd3npoinxclknvokinawp3oinoink2xclnopinevpaoiwenapoiwev41poiawevnpaowevnapwveovinklnzdvslkvnlknpq3pi
```
## Flag:
`flag{71me5t4mp_fun}`
