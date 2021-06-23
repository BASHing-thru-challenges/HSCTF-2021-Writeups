# class-meets (116 solves, 454 points)
## Description
Thank goodness this school year is over... just kidding!

``nc class-meets.hsc.tf 1337``

[Class Meets.pdf](https://hsctf.storage.googleapis.com/uploads/7a23882e797264dfd47ba56bf0ee8474e4b0d1d7cc6ebb3824fe0712b077dc6e/Class%20Meets.pdf)
## Solution
This one felt easier concept-wise than the rest, yet it had less solves than [hopscotch](https://github.com/BASHing-thru-challenges/HSCTF-2021-Writeups/tree/main/algo/hopscotch),
[extended-fib-1](https://github.com/BASHing-thru-challenges/HSCTF-2021-Writeups/tree/main/algo/extended-fibonacci-sequence), 
[extended-fib-2](https://github.com/BASHing-thru-challenges/HSCTF-2021-Writeups/tree/main/algo/extended-fibonacci-sequence-2),
and [not-really-math](https://github.com/BASHing-thru-challenges/HSCTF-2021-Writeups/tree/main/algo/not-really-math).
I guess there were just too many loops and counters for most people. I mean seriously the code for this was just counter after counter keeping track of the different states
of the calendar. I used a python object for each student that could be updated when I wanted. I slowly built on my Student object model until all the counters and trackers
were in place. This is by no means the most efficient way to do this, but it works fine and still gets the flag.

Here's the final script:
```python3

from pwn import *

class Student:
    iter = 1
    onlineDayCount = 0
    inDayCount = 0
    inPerson = True

    def __init__(self, inDayCount, onlineDayCount):
        self.inDayCount = inDayCount
        self.onlineDayCount = onlineDayCount

    def getPlace(self):
        if self.inPerson:
            return "inPerson"
        return "online"

    def update(self):
        self.iter += 1
        if self.inPerson:
            if self.iter > self.inDayCount:
                self.iter = 1
                self.inPerson = False
        else:
            if self.iter > self.onlineDayCount:
                self.iter = 1
                self.inPerson = True

io = process(['nc', 'class-meets.hsc.tf', '1337'])
io.recvline()
while True:
    counting = False
    matches = 0
    day = 0
    month = 0
    weekday = 0
    date = ""
    while True:
        try:
            data = io.recvline().decode()
            print(data)
        except EOFError:
            print(data)
            quit()
        match = re.search("(M\d{1,2} D\d{1,2})\n", data)
        if match is not None:
            break
    start = match.group(1).strip()
    end = re.search("(M\d{1,2} D\d{1,2})\n", io.recvline().decode()).group(1).strip()
    student1stats = io.recvline().decode()
    student2stats = io.recvline().decode()
    student1 = Student(int(re.search("I(\d{1,2})", student1stats).group(1)), int(re.search("V(\d{1,2})", student1stats).group(1)))
    student2 = Student(int(re.search("I(\d{1,2})", student2stats).group(1)), int(re.search("V(\d{1,2})", student2stats).group(1)))
    print("Captured:")
    print("\t" + start)
    print("\t" + end)
    print("\t" + student1stats.strip())
    print("\t" + student2stats.strip())
    while date.strip() != end:
        if day > 29:
            day = 0
            month += 1
        date = f"M{month} D{day}"
        #print(date.strip(), start)
        if date.strip() == start:
            counting = True
            print("COUNTING NOW")
        if weekday == 5 or weekday == 6:
            print(date + ": Weekend")
            day += 1
            if weekday == 6:
                weekday = 0
            else:
                weekday += 1
        else:
            print(date + f": {student1.getPlace()}\t{student2.getPlace()}")
            if student1.getPlace() == student2.getPlace() and counting:
                matches += 1
            student1.update()
            student2.update()
            day += 1
            weekday += 1
    io.sendline(str(matches))
    print("[SEND]: " + str(matches))

```
## Flag
``flag{truly_4_m45t3r_4t_c00rd1n4t1n9_5ch3dul35}``
