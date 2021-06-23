# hopscotch (151 solves, 436 points)
## Description
Keith wants to play hopscotch, but in order to make things interesting, he decides to use a random number generator to decide the number of squares n to draw for a round of 
hopscotch. He then creates a hopscotch board on the floor by randomly creating a sequence of ones (one square) and twos (two squares) such that the sum of all the numbers in the
sequence is n. Given 1 <= n <= 1000, find the number of valid hopscotch boards (mod 10000) he can create.

Sample Input: 5 Sample Output: 8

``nc hopscotch.hsc.tf 1337``
## Solution
This one killed me. I **WAY** overthought this problem. I first had to lookup how tf a hopscotch board works because the description didn't make sense to me (bc im stupid or smth idk)

After figuring out what I was supposed to do, I handwrote the example problem representing boards as sequences of numbers either being 1 or 2.

Example below:
```
1 2 1 1 1 2 1 2
1 1 2 1 1 2 2 1
1 1 1 2 1 1 2 2
1 1 1 1 2
1
```
I did this for a few more numbers until I noticed some rules:

    1. For any number there will always be a base pattern of all 1's (pretty obvious)
    
    2. The next starting pattern could be found by adding in a 2, then filling the remaining space with 1's
    
    3. Each starting pattern could be rotated round to find more patterns
    
    4. The number of rotations for a starting pattern is equal to it's length
I though I had all the rules figured out to make my script, so I wrote python to match the behavoir explained above. It worked... kinda. As soon as I got to a number larger than 9,
I was getting incorrect board counts. This was because I forgot a key rule:

``2's DONT HAVE TO BE GROUPED!!!``

I messed around with determining how I could find the number of possible boards of a sequence of a given length and found myself drowning in messy and over-complicated code.
Then it hit me... PERMUTATIONS! What I was trying to find was a way to find the number of permutations there were for a sequence of 1's and 2's. This is an algorithm that has
already been made before, so why re-invent the wheel. I pulled out an old math textbook and found the formula for permutations of a multiset, my saving grace:

![image](https://user-images.githubusercontent.com/80281801/123070989-19d1d000-d3c9-11eb-8a64-af03b5247469.png)

I implemented the formula in python and got my final script:
```python3

from pwn import *
import math

def findBoards(n):
    total = 0
    max2 = n // 2
    for twoCount in range(max2 + 1):
        oneCount = n - 2 * twoCount
        length =  oneCount + twoCount
        perm = math.factorial(length) // (math.factorial(oneCount) * math.factorial(twoCount))
        total += perm
    return total

io = process(['nc', 'hopscotch.hsc.tf', '1337'])
io.recvline()
while True:
    try:
        data = io.recvuntil(':').decode()
        print(data)
    except EOFError:
        print(io.recvline().decode())
        quit()
    n = int(re.search("(\d{1,})\n", data).group(0))
    print("[RECIEVE]: " + str(n))
    answer = int(findBoards(n) % 10000)
    io.sendline(str(answer))
    print("[SEND]: " + str(answer))

```
## Flag
``flag{wh4t_d0_y0U_w4nt_th3_fla5_t0_b3?_'wHaTeVeR_yOu_wAnT'}``
