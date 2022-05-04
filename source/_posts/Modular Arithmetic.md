---
title: Modular Arithmetic
date: 2022-05-03 19:56:00
tags: cryptohack
mathjax: true
---

第二章也做完咯~

<!--more-->

看到这章的标题就感觉要寄。

进去一看果然，全是数学。。（数学废物笑不出来

做了近两天，我只能说数学博大精深，做数学题真的会谢。。

交完最后一题的 flag 觉得整个人都放松下来了，然后就感觉精疲力尽。

做的很艰难，尤其 python 用的还是那么不利索。

由于有些题的代码是通过推导之后直接通过数学定理化简，所以代码很简单。

# Modular Arithmetic

## Greatest Common Divisor

### Description

The Greatest Common Divisor (GCD), sometimes known as the highest common factor, is the largest number which divides two positive integers (a,b).

For `a = 12, b = 8` we can calculate the divisors of a: `{1,2,3,4,6,12}` and the divisors of b: `{1,2,4,8}`. Comparing these two, we see that `gcd(a,b) = 4`.

Now imagine we take `a = 11, b = 17`. Both `a` and `b` are prime numbers. As a prime number has only itself and `1` as divisors, `gcd(a,b) = 1`.

We say that for any two integers `a,b`, if `gcd(a,b) = 1` then `a` and `b` are coprime integers.

If `a` and `b` are prime, they are also coprime. If `a` is prime and `b < a` then `a` and `b` are coprime.

> Think about the case for `a` prime and `b > a`, why are these not necessarily coprime?

There are many tools to calculate the GCD of two integers, but for this task we recommend looking up [Euclid's Algorithm](https://en.wikipedia.org/wiki/Euclidean_algorithm).

Try coding it up; it's only a couple of lines. Use `a = 12, b = 8` to test it.

Now calculate `gcd(a,b)` for `a = 66528, b = 52920` and enter it below.

###  Analyze

就是算最大公约数啦，简单写个函数就好咯。

现在再看这题是真简单啊。

### Code

```python
def gcd(a: int, b: int) -> int:
    while b:
        a, b = b, a % b
    return a


a = int(input())
b = int(input())
print(gcd(a, b))
```

### SumUp

```python
# 欧几里得算法，是 C 语言做过的东西
# function： gcd(a, b)
def gcd(a: int, b: int) -> int:
    while b:
        a, b = b, a % b
    return a
```

## Extended GCD

### Description

Let `a` and `b` be positive integers.

The extended Euclidean algorithm is an efficient way to find integers `u,v` such that

```
a * u + b * v = gcd(a,b)
```

> Later, when we learn to decrypt RSA, we will need this algorithm to calculate the modular inverse of the public exponent.

Using the two primes `p = 26513, q = 32321`, find the integers `u,v` such that

```
p * u + q * v = gcd(p,q)
```

Enter whichever of `u` and `v` is the lower number as the flag.

> Knowing that `p,q` are prime, what would you expect `gcd(p,q)` to be? For more details on the extended Euclidean algorithm, check out [this page](http://www-math.ucdenver.edu/~wcherowi/courses/m5410/exeucalg.html).

###  Analyze

用到扩展的欧几里得算法，数学原理和辗转相除大同小异，简单代下数字手推一遍详细过程就知道怎么回事咯。

### Code

```python
def exgcd(a: int, b: int) -> (int, int, int):
    if a < b:
        t = a
        a = b
        b = t
    if b == 0:
        return a, 1, 0
    else:
        g, x, y = exgcd(b, a % b)
        return g, y, x - (a // b) * y


a = int(input())
b = int(input())
print(exgcd(a, b))
```

### SumUp

```python
# 扩展欧几里得，不限制输入的大小顺序
def exgcd(a: int, b: int) -> (int, int, int):
    if a < b:
        t = a
        a = b
        b = t
    if b == 0:
        return a, 1, 0
    else:
        g, x, y = exgcd(b, a % b)
        return g, y, x - (a // b) * y
# ax0 + by0 = gcd(a, b)
# 输入 a, b
# 输出 g, x0, y0
# 其中 g = gcd(a, b)
```

## Modular Arithmetic 1

### Description

Imagine you lean over and look at a cryptographer's notebook. You see some notes in the margin:

```
4 + 9 = 1
5 - 7 = 10
2 + 3 = 5
```

At first you might think they've gone mad. Maybe this is why there are so many data leaks nowadays you'd think, but this is nothing more than modular arithmetic modulo 12 (albeit with some sloppy notation).

You may not have been calling it modular arithmetic, but you've been doing these kinds of calculations since you learnt to tell the time (look again at those equations and think about adding hours).

Formally, "calculating time" is described by the theory of congruences. We say that two integers are congruent modulo m if `a ≡ b mod m`.

Another way of saying this, is that when we divide the integer `a` by `m`, the remainder is `b`. This tells you that if m divides a (this can be written as `m | a`) then `a ≡ 0 mod m`.

Calculate the following integers:

```
11 ≡ x mod 6
8146798528947 ≡ y mod 17
```

The solution is the smaller of the two integers.

###  Analyze

模运算来了，头大。后面全是和模运算有关的（准确来说这一整章都是

这道算简单，取余算一下 x 和 y，然后按要求输出小的。没啥好思考的捏。

### Code

```python
n = 8146798528947
print(min(n % 17, 11 % 6))
```

## Modular Arithmetic 2

### Description

We'll pick up from the last challenge and imagine we've picked a modulus `p`, and we will restrict ourselves to the case when `p` is prime.

The integers modulo `p` define a field, denoted `Fp`.

> If the modulus is not prime, the set of integers modulo `n` define a ring.

A finite field `Fp` is the set of integers `{0,1,...,p-1}`, and under both addition and multiplication there is an inverse element `b` for every element `a` in the set, such that `a + b = 0` and `a * b = 1`.

> Note that the identity element for addition and multiplication is different! This is because the identity when acted with the operator should do nothing: `a + 0 = a` and `a * 1 = a`.

Lets say we pick `p = 17`. Calculate `317 mod 17`. Now do the same but with `517 mod 17`.

What would you expect to get for `716 mod 17`? Try calculating that.

This interesting fact is known as Fermat's little theorem. We'll be needing this (and its generalisations) when we look at RSA cryptography.

Now take the prime `p = 65537`. Calculate `27324678765465536 mod 65537`.

Did you need a calculator?

###  Analyze

费马小定理，蛮有意思的一个公式。

$a^{p} \equiv a \mod p$，浅浅变形一下：$a^{p-1} \equiv 1 \mod p$

主要题目描述这个定理的时候略微有一点写复杂咯，不够优雅（ bushi。

这么推导出来其实没有写代码的必要。

### Code

```python
a = 273246787654
p = 65537
print(1 % p)

# a^(p-1) ≡ 1(mod p)
```

## Modular Inverting

### Description

As we've seen, we can work within a finite field `Fp`, adding and multiplying elements, and always obtain another element of the field.

For all elements `g` in the field, there exists a unique integer `d` such that `g * d ≡ 1 mod p`.

This is the multiplicative inverse of `g`.

**Example**: `7 * 8 = 56 ≡ 1 mod 11`

What is the inverse element: `3 * d ≡ 1 mod 13`?

> Think about the little theorem we just worked with. How does this help you find the inverse of an element?

###  Analyze

模逆运算，同余这块的知识。主要是欧拉定理和费马小定理的应用。

欧拉定理：$a^{φ(n)} \equiv 1 \mod n$

费马小定理：$a^{p-1} \equiv 1 \mod p$

对于这道题，有 $3 * d \equiv 1 \mod 13$

因为 gcd(3, 13) = 1，所以可以直接得$$d\ =\ 3^{φ(13)-1}\ *\ 1=3^{11} \mod 13$$ 

python 的 pow() 函数真的很好用！

### Code

```python
d = pow (3, 11, 13)
print(d)
```

### SumUp

$φ(n)$ 即 `1 ~ (n-1)` 范围内与 n 互素的数的个数，若 n 本身为素数，则 $φ(n)=n-1$ 。

## Quadratic Residues

### Description

We've looked at multiplication and division in modular arithmetic, but what does it mean to take the square root modulo an integer?

For the following discussion, let's work modulo `p = 29`. We can take the integer `a = 11` and calculate `a² = 5 mod 29`.

As `a = 11, a² = 5`, we say the square root of `5` is `11`.

This feels good, but now let's think about the square root of `18`. From the above, we know we need to find some integer `a` such that `a² = 18`

Your first idea might be to start with `a = 1` and loop to `a = p-1`. In this discussion `p` isn't too large and we can quickly look.

Have a go, try coding this and see what you find. If you've coded it right, you'll find that for all `a ∈ Fp*` you never find an `a` such that `a² = 18`.

What we are seeing, is that for the elements of `F*p`, not every element has a square root. In fact, what we find is that for roughly one half of the elements of `Fp*`, there is no square root.

> We say that an integer `x` is a *Quadratic Residue* if there exists an `a` such that `a² = x mod p`. If there is no such solution, then the integer is a *Quadratic Non-Residue*.

In other words, `x` is a quadratic residue when it is possible to take the square root of `x` modulo an integer `p`.

In the below list there are two non-quadratic residues and one quadratic residue.

Find the quadratic residue and then calculate its square root. Of the two possible roots, submit the smaller one as the flag.

> If `a² = x` then (-a)² = x. So if `x` is a quadratic residue in some finite field, then there are always two solutions for `a`.

```
p = 29
ints = [14, 6, 11]
```

###  Analyze

Legendre 符号（欧拉判别法）的内容，二次剩余。

定义：如果 $a^2 \equiv x \mod p$ 有解，则 x 是模 p 的二次剩余，否则称为二次非剩余,。

Given: p, x[]

Asked: 找出二次剩余，并求出平方根。

遍历 x in range(1, p)，计算 x 的平方模 p，并与 ints[] 中的元素进行比较，找出模 p 的二次剩余，此时的 x 即为该二次剩余的平方根。

公式和题里的 a 和 x 参数真的很容易弄混，理了半天才理清楚 。。

### Code

```python
p = 29
ints = [14, 6, 11]
flag = min(x for x in range(1, p) if pow(x, 2, p) in ints)
print(flag)
```

## Legendre Symbol

### Description

In Quadratic Residues we learnt what it means to take the square root modulo an integer. We also saw that taking a root isn't always possible.

In the previous case when `p = 29`, even the simplest method of calculating the square root was fast enough, but as `p` gets larger, this method becomes wildly unreasonable.

Lucky for us, we have a way to check whether an integer is a quadratic residue with a single calculation thanks to Legendre. In the following, we will assume we are working modulo a prime `p`.

Before looking at Legendre's symbol, let's take a brief detour to see an interesting property of quadratic (non-)residues.

```
Quadratic Residue * Quadratic Residue = Quadratic Residue
Quadratic Residue * Quadratic Non-residue = Quadratic Non-residue
Quadratic Non-residue * Quadratic Non-residue = Quadratic Residue
```

> Want an easy way to remember this? Replace "Quadratic Residue" with `+1` and "Quadratic Non-residue" with `-1`, all three results are the same!

So what's the trick? The [Legendre Symbol](https://en.wikipedia.org/wiki/Legendre_symbol) gives an efficient way to determine whether an integer is a quadratic residue modulo an odd prime `p`.

Legendre's Symbol: `(a / p) ≡ a(p-1)/2 mod p` obeys:

```
(a / p) = 1 if a is a quadratic residue and a ≢ 0 mod p
(a / p) = -1 if a is a quadratic non-residue mod p
(a / p) = 0 if a ≡ 0 mod p
```

Which means given any integer `a`, calculating `pow(a,(p-1)/2,p)` is enough to determine if `a` is a quadratic residue.

Now for the flag. Given the following 1024 bit prime and 10 integers, find the quadratic residue and then calculate its square root; the square root is your flag. Of the two possible roots, submit the larger one as your answer.

> So Legendre's symbol tells us which integer is a quadratic residue, but how do we find the square root?! The prime supplied obeys `p = 3 mod 4`, which allows us easily compute the square root. The answer is online, but you can figure it out yourself if you think about Fermat's little theorem.

###  Analyze

Legendre 符号的欧拉判别法。

$$\left(\frac{a}{p}\right)=\begin{cases}1, &a ≢ 0 \mod p\text{ 且 }a\text{ 是模 }p\text{ 的二次剩余} \\\ -1, &a ≢ 0 \mod p\text{ 且 }a\text{ 是模 }p\text{ 的二次非剩余} \\\ 0, &a ≡0 \mod p \end{cases}$$

欧拉判别法内容：$(a/p) \equiv a^{(p-1)/2} \mod p$。

遍历 ints 判断各自对应的 $(a/p)$ 是否等于 1，得到二次剩余 a 之后计算平方根 $a^{(p+1)/4} \mod p$ 。

### Code

```python
p = 101524035174539890485408575671085261788758965189060164484385690801466167356667036677932998889725476582421738788500738738503134356158197247473850273565349249573867251280253564698939768700489401960767007716413932851838937641880157263936985954881657889497583485535527613578457628399173971810541670838543309159139
ints = [25081841204695904475894082974192007718642931811040324543182130088804239047149283334700530600468528298920930150221871666297194395061462592781551275161695411167049544771049769000895119729307495913024360169904315078028798025169985966732789207320203861858234048872508633514498384390497048416012928086480326832803, 45471765180330439060504647480621449634904192839383897212809808339619841633826534856109999027962620381874878086991125854247108359699799913776917227058286090426484548349388138935504299609200377899052716663351188664096302672712078508601311725863678223874157861163196340391008634419348573975841578359355931590555, 17364140182001694956465593533200623738590196990236340894554145562517924989208719245429557645254953527658049246737589538280332010533027062477684237933221198639948938784244510469138826808187365678322547992099715229218615475923754896960363138890331502811292427146595752813297603265829581292183917027983351121325, 14388109104985808487337749876058284426747816961971581447380608277949200244660381570568531129775053684256071819837294436069133592772543582735985855506250660938574234958754211349215293281645205354069970790155237033436065434572020652955666855773232074749487007626050323967496732359278657193580493324467258802863, 4379499308310772821004090447650785095356643590411706358119239166662089428685562719233435615196994728767593223519226235062647670077854687031681041462632566890129595506430188602238753450337691441293042716909901692570971955078924699306873191983953501093343423248482960643055943413031768521782634679536276233318, 85256449776780591202928235662805033201684571648990042997557084658000067050672130152734911919581661523957075992761662315262685030115255938352540032297113615687815976039390537716707854569980516690246592112936796917504034711418465442893323439490171095447109457355598873230115172636184525449905022174536414781771, 50576597458517451578431293746926099486388286246142012476814190030935689430726042810458344828563913001012415702876199708216875020997112089693759638454900092580746638631062117961876611545851157613835724635005253792316142379239047654392970415343694657580353333217547079551304961116837545648785312490665576832987, 96868738830341112368094632337476840272563704408573054404213766500407517251810212494515862176356916912627172280446141202661640191237336568731069327906100896178776245311689857997012187599140875912026589672629935267844696976980890380730867520071059572350667913710344648377601017758188404474812654737363275994871, 4881261656846638800623549662943393234361061827128610120046315649707078244180313661063004390750821317096754282796876479695558644108492317407662131441224257537276274962372021273583478509416358764706098471849536036184924640593888902859441388472856822541452041181244337124767666161645827145408781917658423571721, 18237936726367556664171427575475596460727369368246286138804284742124256700367133250078608537129877968287885457417957868580553371999414227484737603688992620953200143688061024092623556471053006464123205133894607923801371986027458274343737860395496260538663183193877539815179246700525865152165600985105257601565]

res = [x for x in ints if pow(x, (p-1)//2, p) == 1][0]
print(pow(res, (p + 1)//4, p))
```

## Modular Square Root

### Description

In Legendre Symbol we introduced a fast way to determine whether a number is a square root modulo a prime. We can go further: there are algorithms for efficiently calculating such roots. The best one in practice is called Tonelli-Shanks, which gets its funny name from the fact that it was first described by an Italian in the 19th century and rediscovered independently by Daniel Shanks in the 1970s.

All primes that aren't 2 are of the form `p ≡ 1 mod 4` or `p ≡ 3 mod 4`, since all odd numbers obey these congruences. As the previous challenge hinted, in the `p ≡ 3 mod 4` case, a really simple formula for computing square roots can be [derived](https://crypto.stackexchange.com/a/20994) directly from Fermat's little theorem. That leaves us still with the `p ≡ 1 mod 4` case, so a more general algorithm is required.

In a congruence of the form `r² ≡ a mod p`, Tonelli-Shanks calculates `r`.

> Tonelli-Shanks doesn't work for composite (non-prime) moduli. Finding square roots modulo composites is computationally equivalent to integer factorization.

The main use-case for this algorithm is finding elliptic curve co-ordinates. Its operation is somewhat complex so we're not going to discuss the details, however, implementations are easy to find and Sage has one built-in.

Find the square root of `a` modulo the 2048-bit prime `p`. Give the smaller of the two roots as your answer.

###  Analyze

sympy 模块内有封装好的函数可以计算二次剩余。

函数 nthroot_mod(a, n, p) 用于求解于 $x^{n} \equiv a\ mod\ p$ 的同余式。

题目给了表达式 $r^2 \equiv a\ mod\ p$，求 r。

写了半天函数心态爆炸，然后搜了下 python 相关的库，几行就解决。

再次感叹，python 真是个好东西。

### Code

```python
import sympy
a = 8479994658316772151941616510097127087554541274812435112009425778595495359700244470400642403747058566807127814165396640215844192327900454116257979487432016769329970767046735091249898678088061634796559556704959846424131820416048436501387617211770124292793308079214153179977624440438616958575058361193975686620046439877308339989295604537867493683872778843921771307305602776398786978353866231661453376056771972069776398999013769588936194859344941268223184197231368887060609212875507518936172060702209557124430477137421847130682601666968691651447236917018634902407704797328509461854842432015009878011354022108661461024768
p = 30531851861994333252675935111487950694414332763909083514133769861350960895076504687261369815735742549428789138300843082086550059082835141454526618160634109969195486322015775943030060449557090064811940139431735209185996454739163555910726493597222646855506445602953689527405362207926990442391705014604777038685880527537489845359101552442292804398472642356609304810680731556542002301547846635101455995732584071355903010856718680732337369128498655255277003643669031694516851390505923416710601212618443109844041514942401969629158975457079026906304328749039997262960301209158175920051890620947063936347307238412281568760161
r = sympy.nthroot_mod(a, 2, p)
print(r)
```

### SumUp

$x^{n} \equiv a\ mod\ p$，求 x

```python
# function:
from sympy.ntheory.residue_ntheory import nthroot_mod
x = nthroot_mod(a, n, p)
```

## Chinese Remainder Theorem

### Description

The Chinese Remainder Theorem gives a unique solution to a set of linear congruences if their moduli are coprime.

This means, that given a set of arbitrary integers `ai`, and pairwise coprime integers `ni`, such that the following linear congruences hold:

> Note "pairwise coprime integers" means that if we have a set of integers `{n1, n2, ..., ni}`, all pairs of integers selected from the set are coprime: `gcd(ni, nj) = 1`.

```
x ≡ a1 mod n1
x ≡ a2 mod n2
...
x ≡ an mod nn
```

There is a unique solution `x ≡ a mod N` where `N = n1 * n2 * ... * nn`.

In cryptography, we commonly use the Chinese Remainder Theorem to help us reduce a problem of very large integers into a set of several, easier problems.

Given the following set of linear congruences:

```
x ≡ 2 mod 5
x ≡ 3 mod 11
x ≡ 5 mod 17
```

Find the integer `a` such that `x ≡ a mod 935`

> Starting with the congruence with the largest modulus, use that for `x ≡ a mod p` we can write `x = a + k*p` for arbitrary integer `k`.

###  Analyze

中国剩余定理，也叫孙子定理。主要针对同余方程组的求解问题。

YouTube 上找了个视频看了原理，跟着视频手算出来了结果，感觉跟着例题一步步算真的能加深理解诶，很容易就理解到了原理和用法。

![求解过程](https://img.hawa130.com/求解过程.jpg)

算法直接找了个板子，偷个懒捏。

### Code

```python
from functools import reduce


def chinese_remainder(n, a):
    sum = 0
    prod = reduce(lambda a, b: a * b, n)
    for n_i, a_i in zip(n, a):
        p = prod // n_i
        sum += a_i * mul_inv(p, n_i) * p
    return sum % prod


def mul_inv(a, b):
    b0 = b
    x0, x1 = 0, 1
    if b == 1:
        return 1
    while a > 1:
        q = a // b
        a, b = b, a % b
        x0, x1 = x1 - q * x0, x0
    if x1 < 0:
        x1 += b0
    return x1


n = [5, 11, 17]
a = [2, 3, 5]
print(chinese_remainder(n, a))
```

## Adrien's Signs

### Description

Adrien's been looking at ways to encrypt his messages with the help of symbols and minus signs. Can you find a way to recover the flag?

###  Analyze

python 学得不好，加密函数理解了好久。

解密倒还好，拿到密文直接正常取对数，取不到就负数取余把 n 还原回去再取对数。

不过代码得跑一会才能出结果，后面或许可以再看看有没有更优解。

（最后一行的代码简直写得太优雅咯

### Code

```python
from numpy import arange
from sympy import discrete_log

lis = [67594220461269, 501237540280788, 718316769824518, 296304224247167, 48290626940198, 30829701196032, 521453693392074, 840985324383794, 770420008897119, 745131486581197, 729163531979577, 334563813238599, 289746215495432, 538664937794468, 894085795317163, 983410189487558, 863330928724430, 996272871140947, 352175210511707, 306237700811584, 631393408838583, 589243747914057, 538776819034934, 365364592128161, 454970171810424, 986711310037393, 657756453404881, 388329936724352, 90991447679370, 714742162831112, 62293519842555, 653941126489711, 448552658212336, 970169071154259, 339472870407614, 406225588145372, 205721593331090, 926225022409823, 904451547059845, 789074084078342, 886420071481685, 796827329208633, 433047156347276, 21271315846750, 719248860593631, 534059295222748, 879864647580512, 918055794962142, 635545050939893, 319549343320339, 93008646178282, 926080110625306, 385476640825005, 483740420173050, 866208659796189, 883359067574584, 913405110264883, 898864873510337, 208598541987988, 23412800024088, 911541450703474, 57446699305445, 513296484586451, 180356843554043, 756391301483653, 823695939808936, 452898981558365, 383286682802447, 381394258915860, 385482809649632, 357950424436020, 212891024562585, 906036654538589, 706766032862393, 500658491083279, 134746243085697, 240386541491998, 850341345692155, 826490944132718, 329513332018620, 41046816597282, 396581286424992, 488863267297267, 92023040998362, 529684488438507, 925328511390026, 524897846090435, 413156582909097, 840524616502482, 325719016994120, 402494835113608, 145033960690364, 43932113323388, 683561775499473, 434510534220939, 92584300328516, 763767269974656, 289837041593468, 11468527450938, 628247946152943, 8844724571683, 813851806959975, 72001988637120, 875394575395153, 70667866716476, 75304931994100, 226809172374264, 767059176444181, 45462007920789, 472607315695803, 325973946551448, 64200767729194, 534886246409921, 950408390792175, 492288777130394, 226746605380806, 944479111810431, 776057001143579, 658971626589122, 231918349590349, 699710172246548, 122457405264610, 643115611310737, 999072890586878, 203230862786955, 348112034218733, 240143417330886, 927148962961842, 661569511006072, 190334725550806, 763365444730995, 516228913786395, 846501182194443, 741210200995504, 511935604454925, 687689993302203, 631038090127480, 961606522916414, 138550017953034, 932105540686829, 215285284639233, 772628158955819, 496858298527292, 730971468815108, 896733219370353, 967083685727881, 607660822695530, 650953466617730, 133773994258132, 623283311953090, 436380836970128, 237114930094468, 115451711811481, 674593269112948, 140400921371770, 659335660634071, 536749311958781, 854645598266824, 303305169095255, 91430489108219, 573739385205188, 400604977158702, 728593782212529, 807432219147040, 893541884126828, 183964371201281, 422680633277230, 218817645778789, 313025293025224, 657253930848472, 747562211812373, 83456701182914, 470417289614736, 641146659305859, 468130225316006, 46960547227850, 875638267674897, 662661765336441, 186533085001285, 743250648436106, 451414956181714, 527954145201673, 922589993405001, 242119479617901, 865476357142231, 988987578447349, 430198555146088, 477890180119931, 844464003254807, 503374203275928, 775374254241792, 346653210679737, 789242808338116, 48503976498612, 604300186163323, 475930096252359, 860836853339514, 994513691290102, 591343659366796, 944852018048514, 82396968629164, 152776642436549, 916070996204621, 305574094667054, 981194179562189, 126174175810273, 55636640522694, 44670495393401, 74724541586529, 988608465654705, 870533906709633, 374564052429787, 486493568142979, 469485372072295, 221153171135022, 289713227465073, 952450431038075, 107298466441025, 938262809228861, 253919870663003, 835790485199226, 655456538877798, 595464842927075, 191621819564547]
a = 288260533169915
p = 1007621497415251
decode = ''
for i in arange(len(lis)):
    cipher = lis[i]
    try:
        r = discrete_log(p, cipher, a)
        decode += '1'
    except:
        r = discrete_log(p, -cipher % p, a)
        decode += '0'
print(''.join([chr(int(decode[i:i+8], 2)) for i in range(0, len(decode), 8)]))
```

## Modular Binomials

### Description

Rearrange the following equations to get `p,q`

```
N = p*q
c1 = (2*p + 3*q)**e1 mod N
c2 = (5*p + 7*q)**e2 mod N
```

###  Analyze

给了五个参数，求两个未知数 p 和 q，一眼看到给了 p，q 的乘积 N，所以只要随便求出来其中一个然后除 N 就可以咯。

我是先求了 q，所以先把两个方程右边配成齐次的，然后把要消掉的 p 的系数配成一样的，q 的系数刚好是 15 和 14，相减就是 q 咯。

最后一道题倒是没啥技术含量，就嗯解方程组。

### Code

```python
import math
N = 14905562257842714057932724129575002825405393502650869767115942606408600343380327866258982402447992564988466588305174271674657844352454543958847568190372446723549627752274442789184236490768272313187410077124234699854724907039770193680822495470532218905083459730998003622926152590597710213127952141056029516116785229504645179830037937222022291571738973603920664929150436463632305664687903244972880062028301085749434688159905768052041207513149370212313943117665914802379158613359049957688563885391972151218676545972118494969247440489763431359679770422939441710783575668679693678435669541781490217731619224470152467768073
e1 = 12886657667389660800780796462970504910193928992888518978200029826975978624718627799215564700096007849924866627154987365059524315097631111242449314835868137
e2 = 12110586673991788415780355139635579057920926864887110308343229256046868242179445444897790171351302575188607117081580121488253540215781625598048021161675697
c1 = 14010729418703228234352465883041270611113735889838753433295478495763409056136734155612156934673988344882629541204985909650433819205298939877837314145082403528055884752079219150739849992921393509593620449489882380176216648401057401569934043087087362272303101549800941212057354903559653373299153430753882035233354304783275982332995766778499425529570008008029401325668301144188970480975565215953953985078281395545902102245755862663621187438677596628109967066418993851632543137353041712721919291521767262678140115188735994447949166616101182806820741928292882642234238450207472914232596747755261325098225968268926580993051
c2 = 14386997138637978860748278986945098648507142864584111124202580365103793165811666987664851210230009375267398957979494066880296418013345006977654742303441030008490816239306394492168516278328851513359596253775965916326353050138738183351643338294802012193721879700283088378587949921991198231956871429805847767716137817313612304833733918657887480468724409753522369325138502059408241232155633806496752350562284794715321835226991147547651155287812485862794935695241612676255374480132722940682140395725089329445356434489384831036205387293760789976615210310436732813848937666608611803196199865435145094486231635966885932646519


q = math.gcd(N, pow(c1, e2, N) * pow(5, e1 * e2, N) - pow(c2, e1, N) * pow(2, e1 * e2, N))
p = N // q
print(p)
print(q)
```
