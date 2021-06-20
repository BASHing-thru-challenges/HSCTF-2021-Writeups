# queen-of-the-hill (514 solves, 221 points)

## Description
After finding a special key of the Hill, which contains a note to visit the Queen of the Hill, our brave Amanda begins her adventure to find the Queen of the Hill’s treasure. How shall she meet the Queen of the Hill? (a=0)

Cipher text: `rtca{vbuhp_kaiq_gfj_nx_rda_ujw}`

Encryption key:

16 25 8

14 19 5

15 17 3

## Solution
This seems like a pretty straightforward cipher, so I googled `queen of the hill cipher` given the challenge name, and found the Hill cipher. Using [this](https://www.dcode.fr/hill-cipher) website to decode it using a = 0 and the matrix values we are provided as a 3x3, we can get the flag.

## Flag
`flag{climb_your_way_to_the_top}`
