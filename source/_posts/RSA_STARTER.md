---
title: RSA_STARTER
date: 2022-05-09 01:42:00
tags: cryptohack
mathjax: true
---

这个超快！

我看谁不是速通（bushi

<!--more-->

最后一块内容，没想到意外地顺利，舒服捏。

# RSA_STARTER

## RSA Starter 1   

### Description

All operations in RSA involve [modular exponentiation](https://en.wikipedia.org/wiki/Modular_exponentiation).

Modular exponentiation is an operation that is used extensively in cryptography and is normally written like: `2¹⁰ mod 17`

You can think of this as raising some number to a certain power (`2¹⁰ = 1024`), and then taking the remainder of the division by some other number (`1024 mod 17 = 4`). In Python there's a built-in operator for performing this operation: `pow(base, exponent, modulus)`

In RSA, modular exponentiation, together with the problem of prime factorisation, helps us to build a "[trapdoor function](https://en.wikipedia.org/wiki/Trapdoor_function)". This is a function that is easy to compute in one direction, but hard to do in reverse unless you have the right information. It allows us to encrypt a message, and only the person with the key can perform the inverse operation to decrypt it.

Find the solution to `101¹⁷ mod 22663`

###  Analyze

模幂运算？有种回到做数论题的错觉（出戏。

不过 pow() 是之前就用过的东西诶，突然亲切。

无脑填数就行。

### Code

```python
print(pow(101, 17, 22663))
```

## RSA Starter 2

### Description

RSA encryption is modular exponentiation of a message with an exponent `e` and a modulus `N` which is normally a product of two primes: `N = p * q`.

Together the exponent and modulus form an RSA "public key" `(N, e)`. The most common value for `e` is `0x10001` or `65537`.

"Encrypt" the number `12` using the exponent `e = 65537` and the primes `p = 17` and `q = 23`. What number do you get as the ciphertext?

###  Analyze

两个素数的乘积为模数。还是无脑填数。

### Code

```python
b = 12
e = 65537
p, q = 17, 23
N = p * q
print(pow(b, e, N))
```

## RSA Starter 3

### Description

RSA relies on the difficulty of the factorisation of the modulus `N`. If the primes can be found then we can calculate the [Euler totient](https://leimao.github.io/article/RSA-Algorithm/) of `N` and thus decrypt the ciphertext.

Given `N = p*q` and two primes:

```
p = 857504083339712752489993810777
q = 1029224947942998075080348647219
```

What is the totient of `N`?

###  Analyze

欧拉方程：$φ(mn) = φ(m)φ(n)$

因为 p，q 都是素数，所以 $φ(p) = p - 1$ ，$φ(q) = q - 1$。

在这道题里面是这样

$$\begin{align}φ(N) &= φ(p*q) \\\ &= φ(p)φ(q) \\\ &= (p-1)(q-1)\end{align}$$

### Code

```python
p = 857504083339712752489993810777
q = 1029224947942998075080348647219
N = (p - 1) * (q - 1)
print(N)
```

## RSA Starter 4

### Description

The private key `d` is used to decrypt ciphertexts created with the corresponding public key (it's also used to "sign" a message but we'll get to that later).

The private key is the secret piece of information or "trapdoor" which allows us to quickly invert the encryption function. If RSA is implemented well, if you do not have the private key the fastest way to decrypt the ciphertext is to first factorise the modulus.

In RSA the private key is the [modular multiplicative inverse](https://en.wikipedia.org/wiki/Modular_multiplicative_inverse) of the exponent `e` modulo the totient of `N`.

Given the two primes:

```
p = 857504083339712752489993810777
q = 1029224947942998075080348647219
```

and the exponent:

```
e = 65537
```

What is the private key `d`?

###  Analyze

对 e 求逆元。$d * e= 1 \mod N$。

第一次用这个库好像？

### Code

```python
import gmpy2

p = 857504083339712752489993810777
q = 1029224947942998075080348647219
e = 65537
N = (p - 1) * (q - 1)
d = gmpy2.invert(e, N)
print(d)
```

## RSA Starter 5

### Description

I've encrypted a secret number for your eyes only using your public key parameters:

```
N = 882564595536224140639625987659416029426239230804614613279163
e = 65537
```

Use the private key that you found for these parameters in the previous challenge to decrypt this ciphertext:

```
c = 77578995801157823671636298847186723593814843845525223303932
```

###  Analyze

和第一题没区别，无脑填数。

### Code

```python
N = 882564595536224140639625987659416029426239230804614613279163
c = 77578995801157823671636298847186723593814843845525223303932
d = 121832886702415731577073962957377780195510499965398469843281
flag = pow(c, d, N)
print(flag)
```

## RSA Starter 6

### Description

How can you ensure that the person receiving your message knows that you wrote it?

You've been asked out on a date, and you want to send a message telling them that you'd love to go, however a jealous lover isn't so happy about this.

When you send your message saying yes, your jealous lover intercepts the message and corrupts it so it now says no!

We can protect against these attacks by signing the message.

Imagine you write a message `M`. You encrypt this message with your **friend's public key**: `C = Mᵉ⁰ mod N₀`.

To sign this message, you calculate the hash of the message: `H(M)` and "encrypt" this with **your private key**: `S = H(M)ᵈ¹ mod N₁`.

> In real cryptosystems, it's [best practice to use separate keys](https://crypto.stackexchange.com/a/12138) for encrypting and signing messages.

Your friend can decrypt the message using **their private key**: `m = Cᵈ⁰ mod N₀`. Using your public key they calculate `s = Sᵉ¹ mod N₁`.

Now by computing `H(m)` and comparing it to `s`: `assert H(m) == s`, they can ensure that the message you sent them, is the message that they received!

Sign the flag `crypto{Immut4ble_m3ssag1ng}` using your private key and the SHA256 hash function.

###  Analyze

英文看得有点头大，得浅理一下逻辑。

>Alice 向 Bob 发送消息：
>
>1. 用 Bob 的公钥加密得到 C
>
>2. 签名
>
>   > 签名步骤
>   >
>   > 1. 求消息的哈希值 H
>   > 2. 用 Alice 的私钥“加密”哈希值得到 S
>
>Bob 接收 Alice 的消息：
>
>1. 用 Bob 的私钥解密得到 m
>2. 用 Alice 的公钥计算“解密”得到 s
>3. 计算消息的哈希值 H
>4. 比较 s 和 H，二者相等就能确定消息是真实的

签名是个蛮好玩的东西捏。

### Code

```python
from hashlib import sha256

N = 15216583654836731327639981224133918855895948374072384050848479908982286890731769486609085918857664046075375253168955058743185664390273058074450390236774324903305663479046566232967297765731625328029814055635316002591227570271271445226094919864475407884459980489638001092788574811554149774028950310695112688723853763743238753349782508121985338746755237819373178699343135091783992299561827389745132880022259873387524273298850340648779897909381979714026837172003953221052431217940632552930880000919436507245150726543040714721553361063311954285289857582079880295199632757829525723874753306371990452491305564061051059885803
d = 11175901210643014262548222473449533091378848269490518850474399681690547281665059317155831692300453197335735728459259392366823302405685389586883670043744683993709123180805154631088513521456979317628012721881537154107239389466063136007337120599915456659758559300673444689263854921332185562706707573660658164991098457874495054854491474065039621922972671588299315846306069845169959451250821044417886630346229021305410340100401530146135418806544340908355106582089082980533651095594192031411679866134256418292249592135441145384466261279428795408721990564658703903787956958168449841491667690491585550160457893350536334242689

flag = "crypto{Immut4ble_m3ssag1ng}"
flag = flag.encode()
hash = sha256(flag)
hash = hash.hexdigest()
hash = int(hash, 16)
c = pow(hash, d, N)
print(hex(c))
```
