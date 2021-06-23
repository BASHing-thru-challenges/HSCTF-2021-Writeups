# not-really-math (346 solves, 319 points)
## Description
Have a warmup algo challenge.

``nc not-really-math.hsc.tf 1337``

[not-really-math.pdf](https://hsctf.storage.googleapis.com/uploads/4ef4ab24e5d1c8d8fbf50e2fbf8e38319f57f33a91d24b636032e048de1caf3f/not-really-math.pdf)

## Solution
My first instinct was to create a script that would find the index of all the addition operations (The a's). It would then find the values on either side of the operation
and add them. It would do this for every a in the problem then multiply the remaining numbers. This works great for the test case in the pdf and other problems I could think of. This algorithm breaks down when addition opperations are chained like below:

``1m4a3a6a2m4a2m3``

My flawed algorithm would compute ``4 + 3``, then ``3 + 6``, and then ``6 + 2``. It always will repeat values when adding. After fiddling with ways to detect addition chaining and forming exceptions, the code got too complicated to fast. I had to rethink my approach.

I thought of a new method that would pull all groups of addition out as a substring. The algorithm would replace the a's with a "+" and use python's ``eval()`` function to compute the results. Results were stored in a list and later multiplied out. The final result was modded by 2<sup>32</sup> - 1 and sent to the nc server with pwntools.

Final script is below:
```python3

import re
from pwn import *

def multiplyList(list):
    total = 1
    if not list:
        return 1
    for num in list:
        total *= num
    return total

def find_char(char):
    match = char
    match_indexes = []
    for index, character in enumerate(problem):
        if character == match:
            match_indexes.append(index)
    return match_indexes

def getAddition():
    index = 0
    addition = []
    phrase = ""
    mLocation = find_char("m")
    if not mLocation:
        return -1
    else:
        for character in problem:
            if character == "m":
                break
            else:
                phrase += character
        addition.append(phrase)
        while index < len(mLocation):
            phrase = ""
            start = mLocation[index] + 1
            #print(start)
            try:
                end = mLocation[index + 1]
            except IndexError:
                for i in range(start, len(problem)):
                    phrase += problem[i]
                addition.append(phrase)
                break
            for i in range(start, end):
                phrase += problem[i]
            addition.append(phrase)
            index += 1
        return addition

io = process(['nc', 'not-really-math.hsc.tf', '1337'])
mod = 2**32 - 1
io.recvline()
while True:
    try:
        data = io.recvuntil(":").decode()
    except EOFError:
        print(io.recvline().decode())
        break
    #print(data)
    problem = re.search("([\dam]{4,})\n", data).group(0)
    print("[RECIEVE]: " + problem)
    final = 0
    addition = getAddition()
    #print(addition)
    if addition == -1:
        problem = problem.replace("a", "+")
        final = eval(problem)
    else:
        addition = [phrase.replace("a", "+") for phrase in addition]
        values = []
        edit = problem
        for phrase in addition:
            #print(phrase)
            values.append(eval(phrase))
            edit = edit.replace(phrase.replace("+", "a"), "", 1)
            #print(edit)
        edit = edit.replace("m", " ")
        mult = edit.split()
        #print(mult, values)
        mult = [int(i) for i in mult]
        #print(multiplyList(mult),multiplyList(values))
        final = multiplyList(mult) * multiplyList(values)
    print("[SEND]: " + str(final % mod))
    io.sendline(str(final % mod))
    
```
Running the code gives the flag!
## Flag
``flag{yknow_wh4t_3ls3_is_n0t_real1y_math?_c00l_m4th_games.com}``
