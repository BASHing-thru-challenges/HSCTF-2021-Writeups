# cyanocitta-cristata-cyanotephra-but- (94 solves, 464 points)

## Description:
Only the ciphertext has changed from the original challenge.

The Blue Jay (Cyanocitta cristata) is a passerine bird in the family Corvidae, native to North America. It is resident through most of eastern and central United States and southern Canada, although western populations may be migratory. It breeds in both deciduous and coniferous forests, and is common near and in residential areas. It is predominately blue with a white chest and underparts, and a blue crest. It has a black, U-shaped collar around its neck and a black border behind the crest. Sexes are similar in size and plumage, and plumage does not vary throughout the year. Four subspecies of the Blue Jay are recognized.

[output.txt](output.txt)

[cyanocitta-cristata-cyanotephra.sage](cyanocitta-cristata-cyanotephra.sage)

## Solution:
It's the same encryption as cyanocitta-cristata-cyanotephra but with different inputs, so I reused the same script. 

**My Script:**

```
import z3
import codecs
all = [(26, 66, 70314326037540683861066), (175, 242, 1467209789992686137450970), (216, 202, 1514632596049937965560228), (13, 227, 485439858137512552888191), (1, 114, 112952835698501736253972), (190, 122, 874047085530701865939630), (135, 12, 230058131262420942645110), (229, 220, 1743661951353629717753164), (193, 81, 704858158272534244116883)]
a, b = 886191939093, 589140258545
flag = 19440293474977244702108989804811578372332250
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
b2 = 14706786521482826438
b3 = 2369955372216026905
b4 = 4447776531968912934
b5 = 14360386757903922932
b1 = 8521180195499215015
b0 = 11227347570319680787
key = b0 * (a ** 2) + b1 * (b ** 2) + b2 * a * b + b3 * a + b4 * b + b5
print(key)
print(codecs.decode(hex(flag ^ key)[2:], "hex"))
```

## Flag:
`flag{d8smdsx01a0}`