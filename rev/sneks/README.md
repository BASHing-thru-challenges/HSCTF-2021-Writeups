# sneks (212 solves, 403 points)

## Description:
[https://www.youtube.com/watch?v=0arsPXEaIUY](https://www.youtube.com/watch?v=0arsPXEaIUY)

[sneks.pyc](sneks.pyc)

[output.txt](output.txt)

## Solution:
Like with scrambler, this challenge gave us a .pyc file and an output to reverse. I again used the website [decompiler.com](https://www.decompiler.com/) to decompile the .pyc file into the following python script.

```python3
# uncompyle6 version 3.7.4
# Python bytecode 3.8 (3413)
# Decompiled from: Python 2.7.17 (default, Sep 30 2020, 13:38:04) 
# [GCC 7.5.0]
# Warning: this version of Python has problems handling the Python 3 "byte" type in constants properly.

# Embedded file name: sneks.py
# Compiled at: 2021-05-19 21:21:59
# Size of source mod 2**32: 600 bytes
import sys

def f(n):
    if n == 0:
        return 0
    if n == 1 or n == 2:
        return 1
    x = f(n >> 1)
    y = f(n // 2 + 1)
    return g(x, y, not n & 1)


def e(b, j):
    return 5 * f(b) - 7 ** j


def d(v):
    return v << 1


def g(x, y, l):
    if l:
        return h(x, y)
    return x ** 2 + y ** 2


def h(x, y):
    return x * j(x, y)


def j(x, y):
    return 2 * y - x


def main():
    if len(sys.argv) != 2:
        print('Error!')
        sys.exit(1)
    inp = bytes(sys.argv[1], 'utf-8')
    a = []
    for i, c in enumerate(inp):
        a.append(e(c, i))
    else:
        for c in a:
            print((d(c)), end=' ')


if __name__ == '__main__':
    main()
```
There are a lot of functions that are pointless, so I ended up copying them into their calling location. The last thing the program does is shifted each number's bits to left by 1 place. So to reverse, we just shift the bits to the right by one place(suprisingly this doesn't work the other way). The e function is also easily reversible. The main problem is the f function, which is recursive. To bypass that, I bruteforced values to find flag.

**Script:**
```python3
def f(n):
    if n == 0:
        return 0
    if n == 1 or n == 2:
        return 1
    x = f(n >> 1)
    y = f(n // 2 + 1)
    return g(x, y, not n & 1)
def g(x, y, l):
    if l:
        return x * (2 * y - x)
    return x ** 2 + y ** 2

file = open('output.txt', 'r')
strNums = file.read().split(' ')[:-1]
nums = [int(i) >> 1 for i in strNums]
flag = ''
for i, c in enumerate(nums):
   c = (c + 7 ** i) // 5
   for x in range(0,256):
       if(f(x) == c):
          flag += chr(x)
print(flag)
```
## Flag:
`flag{s3qu3nc35_4nd_5um5}`
