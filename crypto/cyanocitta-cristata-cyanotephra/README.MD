# cyanocitta-cristata-cyanotephra (100 solves, 461 points)

## Description:
The Blue Jay (Cyanocitta cristata) is a passerine bird in the family Corvidae, native to North America. It is resident through most of eastern and central United States and southern Canada, although western populations may be migratory. It breeds in both deciduous and coniferous forests, and is common near and in residential areas. It is predominately blue with a white chest and underparts, and a blue crest. It has a black, U-shaped collar around its neck and a black border behind the crest. Sexes are similar in size and plumage, and plumage does not vary throughout the year. Four subspecies of the Blue Jay are recognized.

[output.txt](output.txt)

[cyanocitta-cristata-cyanotephra.sage](cyanocitta-cristata-cyanotephra.sage)

## Solution:
The .sage file creates a function of x and y to encrypt the flag, and it also gives us 5 inputs and outputs of that function. We can use those outputs to reverse the function and find the coefficients. From there we can use a and b and the reversed function to decrypt the flag. I used z3 but in hindsight you just use linear algebra for a more efficient solution(this would have been helpful for a later challenge!)

**My Script:**

```
import z3
import codecs
all = [(102, 52, 196041229330569267220106), (132, 146, 410001940353094891409244), (18, 240, 103837469369268721370678), (249, 159, 1219095771628413625551254), (138, 178, 479023074104679528693764), (53, 109, 93693969877021049165894), (240, 91, 1025662638965841618153464), (80, 242, 287368655667312232381556), (89, 5, 125337978540015874431098)]
a, b = 966671014274, 431366307057
flag = 13852083614654026802100511377448284283243574446446059950439305716796054617705512658466627498983053935182292560386927855123180764079265426550919872138321924454925808615635580838623379370137115050380226108825021474681966792462276436879925108632413161056194128895629727670635129

xs = []
ys = []
vals = []
c0= z3.Int('c0')
c1= z3.Int('c1')
c2= z3.Int('c2')
c3= z3.Int('c3')
c4= z3.Int('c4')
c5= z3.Int('c5')
for x in all:
    xs.append(x[0])
    ys.append(x[1])
    vals.append(x[2])
s = z3.Solver()
for i in range(len(xs)):
    s.add(c0 * (xs[i] ** 2) + c1 * (ys[i] ** 2) + c2 * xs[i] * ys[i] + c3 * xs[i] + c4 * ys[i] + c5 == vals[i])
print(s.check())
print(s.model())
b2 = 5875327950550733875
b0 = 15323988390216276549
b4 = 14473158623602872631
b3 = 13889881931964042512
b1 = 1211184093130083857
b5 = 3300675726605068946
key = b0 * (a ** 2) + b1 * (b ** 2) + b2 * a * b + b3 * a + b4 * b + b5
print(key)
print(codecs.decode(hex(flag ^ key)[2:], "hex"))
```

## Flag:
`flag{:monkaSTEER::monkaSTEER::monkaSTEER::monkaSTEER::monkaSTEER::monkaSTEER::monkaSTEER::monkaSTEER::monkaSTEER:}`