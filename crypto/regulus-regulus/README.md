# regulus-regulus (93 solves, 464 points)

## Description
Anhinga anhinga

`nc regulus-regulus.hsc.tf 1337`

## Solution

### Initial Analysis
When we connect to the challenge, we are given four options: `1. Key gen alg, 2. Public key, 3. Private key, 4. Decrypt`. Let's look at the algorithm first (slightly abridged).

```
flag = open('flag.txt','rb').read()
p,q = getPrime(1024),getPrime(1024)
e = 0x10001
n = p*q
m = random.randrange(0,n)
c = pow(m,e,n)
d = sympy.mod_inverse(e,(p-1)*(q-1))
def menu():
    elif choice=="4":
        d_ = int(input("What private key you like to decrypt the message with?\n : "))
        if d_%((p-1)*(q-1))==d:
            print("You are not allowed to use that private key.")
            menu()
        if (pow(c,d_,n)==m):
            print("Congrats! Here is your flag:")
            print(flag)
            exit()
        else:
            print("Sorry, that is incorrect.")
            menu()
    else:
        print("That is not a valid choice.")
        menu()
```
We mainly are focusing on the Decrypt portion, since 2 and 3 simply provide the public key, exponent, and private key (N, e, d). Now, decrypt asks us to provide a private key, and the "win" condition is be able to decrypt as you normally would with RSA; however, we're not able to use the private key provided due to the first if statement, which checks for that case. Thus, our objective is to create an equivalent private key that will pass that initial check. 

### p and q
To even generate an equivalent private key, we need to get the primes from the given N, e, and d. With a bit of research, we find this [script](https://gist.github.com/ddddavidee/b34c2b67757a54ce75cb), which works perfectly. My [edited version](RecoverPrimesEdit.py) is here which allows for input as well. Now, all there is to do is generate.

### New key
To wrap this up, we have to create a new key with these values. After some Googling, [this](https://crypto.stackexchange.com/questions/37467/do-equivalent-rsa-keys-exist) holds all the information we need; we don't want to create the same key of course within the normal range, but we can create "infinite" keys outside of it. This is done by adding `k * 𝜆(𝑛)` with d (our original private key), with k being any integer and 𝜆(𝑛) being Carmichael's totient. Thus, we can start off with d + 𝜆(𝑛) or d - 𝜆(𝑛). Now, all we have to do is calculate these values and plug them in; we could write a python script, but it was simple enough that I just used python straight from the terminal.

```
import math
N = 19316168030873260945275603782898337464542835781997293536834644543904701630686498645945181397403939706801778146895074523733326402797985969171203578567148386811169397210570865189040763019939755850442603591649636451732029816846773656995333769156014044122943361823169203440561463998364350771303082508415195956736787912315377041080664796940632124404342176482355390284400885746220351502363192224100650338975030440924085946282985354034340934798982642135681198940647313039088105058884256800900850701755704501841617590599646022649438168914338864831133623040905532759457170209522151552422304924939122179236853411601267117685489
e = 65537
d = 16884883869457975998793792659332590089090589686422340847737661422268528419347363083890131557661731509273544203721943777036403908685033190440207983413259636291534132282514972076075609684396733649086102118779077056423463450379449307443866532911328448963412718191643153130336834910635166806329416829303900788402294598220478266535268254865079046670642475226214357893226236240200514158089982579160987626097028379433794639451669125426824192869394654675771262867655872962250964411079862065145349944035047742005824718602621247747321626816856355843647545383654842528872661897605286371779827316304278993927199069038825273232033
p = 137627188406772166661633790490219897163626305912363544315728706216549580155185916743651066540798699726406263763724904676598284920987738790317861332434603872274863758755974881373115003273035904899419123612552084222717842423994457075837050641138048212782751810135933182038470104446009836850704525364081635247331
q = 140351396075768252051696541206604240911689771115377437467368943473619399188470134153646981351964095216800627817681768428092860601331073512134367238195202074105493523823629633474272507548704697559505152801575353145051958652467103731327692672624148860060683537227342987278567699479400353400552506758106234920219
carmichael = math.lcm((p-1), (q-1))
newd = d + carmichael
newdtwo = d - carmichael
```
Trying both, we get the flag after trying to decrypt. 

### Intended Solution
I'm not sure that this specific solution will always work, although I had no issues with it even after testing it on multiple cases. The intended solution was to use `d + φ(n)/2 and d - φ(n)/2 `, based of off [this post](https://www.reddit.com/r/crypto/comments/7xb1gl/someone_found_that_there_is_a_second_private_key/) which is interesting, as they used Euler's totient instead of Carmichael. I found that in nearly all cases I was able to get the flag with just `d + 𝜆(𝑛)` without even trying the - value. I believe this worked because the initial check looks to see if the submitted private key has a modulo of d after dividing by φ(n), where the person would try to generate a new private key with the standard `d + φ(n)`, but leaves it open for something like φ(n)/2 or in our case, Carmichael's. This also means we don't need to do d - 𝜆(𝑛), because this is only necessary if d + phi(n) is greater than n, thus failing the modulo check; however, even if our value of d + 𝜆(𝑛) is greater than n, it won't trigger that. Thus, this solution should theoretically work in every case.

## Flag
`flag{r3gulus_regu1us_regUlus_regulu5_regUlus_Regulus_reguLus_regulns_reGulus_r3gulus_regu|us}`

