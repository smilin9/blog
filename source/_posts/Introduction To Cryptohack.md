---
title: Introduction To Cryptohack
date: 2022-05-01 19:33:32
tags: cryptohack
categories: 学习
cover: https://img.hawa130.com/Introduction.jpg
mathjax: true
---

做完第一章咯~

<!--more-->

做了大半天才做完，主要是 python 语法什么的写起来比较手生，有 python 基础的话可能会做得更顺手一点。

这一章的知识都属于比较基础的，甚至大部分题几乎都是直接把 flag 怼脸上（笑

做完题之后犹豫了一下博客是单纯写解题相关还是顺便附上知识点总结，想起之前说的浅（shen）浅（shen）记录，所以还是打算都写写，不是什么坏事捏。

## Finding Flags

### Description

Each challenge is designed to help introduce you to a new piece of cryptography. Solving a challenge will require you to find a "flag".

These flags will usually be in the format`crypto{y0ur_f1rst_fl4g}`. The flag format helps you verify that you found the correct solution.

Try submitting this into the form below to solve your first challenge.

###  Analyze

简单说了下 flag 是啥，格式是 `crypto{y0ur_f1rst_fl4g}`。flag 已经给咯，直接填~

## Great Snakes

### Description

Modern cryptography involves code, and code involves coding. CryptoHack provides a good opportunity to sharpen your skills.

Of all modern programming languages, Python 3 stands out as ideal for quickly writing cryptographic scripts and attacks.

Run the attached Python script and it will output your flag.

###  Analyze

就直接在 python3 下运行 .py 文件就看到 `flag` 咯。

直接在 vscode 里跑的时候老是报错，发现第一行注释 `#!/usr/bin/env python3` 是给 Linux/macOS 环境准备的，Windows 直接删掉就好咯。

~~（孤儿 Windows 实锤）~~

## ASCII

### Description

ASCII is a 7-bit encoding standard which allows the representation of text using the integers 0-127.

Using the below integer array, convert the numbers to their corresponding ASCII characters to obtain a flag.

```
[99, 114, 121, 112, 116, 111, 123, 65, 83, 67, 73, 73, 95, 112, 114, 49, 110, 116, 52, 98, 108, 51, 125]
```

> In Python, the `chr()` function can be used to convert an ASCII ordinal number to a character (the `ord()` function does the opposite).

###  Analyze

介绍了下 ASCII 是啥，直接按给出的 `chr()` 函数转换一下输出 `flag`。

### Code

```python
ord = [99, 114, 121, 112, 116, 111, 123, 65, 83, 67, 73, 73, 95, 112, 114, 49, 110, 116,
       52, 98, 108, 51, 125]

print(''.join(chr(o) for o in ord))
```

### SumUp

```python
function:
chr()  # ASCII-->char
ord()  # char-->ASCII
```

## Hex

### Description

When we encrypt something the resulting ciphertext commonly has bytes which are not printable ASCII characters. If we want to share our encrypted data, it's common to encode it into something more user-friendly and portable across different systems.

Included below is a flag encoded as a hex string. Decode this back into bytes to get the flag.

```
63727970746f7b596f755f77696c6c5f62655f776f726b696e675f776974685f6865785f737472696e67735f615f6c6f747d
```

 In Python, the `bytes.fromhex()` function can be used to convert hex to bytes. The `.hex()` instance method can be called on byte strings to get the hex representation.

###  Analyze

十六进制来咯。

要求把给出的十六进制 decode 回 byte，用给出的函数直接 decode 输出。

### Code

```python
h = '63727970746f7b596f755f77696c6c5f62655f776f726b696e675f77697685f6865785f737472696e67\
35f615f6c6f747d'
print(bytes.fromhex(h))
```

### SumUp

```python
function:
bytes.fromhex()  # hex-->bytes
bytes.hex()  # bytes-->hex
```

## Base64

### Description

Another common encoding scheme is Base64, which allows us to represent binary data as an ASCII string using 64 characters. One character of a Base64 string encodes 6 bits, and so 4 characters of Base64 encode three 8-bit bytes.

Base64 is most commonly used online, so binary data such as images can be easily included into HTML or CSS files.

Take the below hex string, *decode* it into bytes and then *encode* it into Base64.

```
72bca9b68fc16ac7beeb8f849dca1d8a783e8acf9679bf9269f7bf
```

 In Python, after importing the base64 module with `import base64`, you can use the `base64.b64encode()` function. Remember to decode the hex first as the challenge description states.

###  Analyze

是 base64 捏，给的是十六进制字符串，先用 `bytes.fromhex` 函数 encode 得到 byte，然后用 base64 库里的 `base64.b64encode()` 函数编码。

记得先安 base64 库喔~

### Code

```python
import base64

h = '72bca9b68fc16ac7beeb8f849dca1d8a783e8acf9679bf9269f7bf'
decode = bytes.fromhex(h)  # hex --> bytes
print(base64.b64encode(decode))  # base64 编码
```

### SumUp

```python
import base64
base64.b64encode() # bytes-->base64Code
```

## Bytes and Big Integers

### Description

Cryptosystems like RSA works on numbers, but messages are made up of characters. How should we convert our messages into numbers so that mathematical operations can be applied?

The most common way is to take the ordinal bytes of the message, convert them into hexadecimal, and concatenate. This can be interpreted as a base-16 number, and also represented in base-10.

To illustrate:

```
message: HELLO
ascii bytes: [72, 69, 76, 76, 79]
hex bytes: [0x48, 0x45, 0x4c, 0x4c, 0x4f]
base-16: 0x48454c4c4f
base-10: 310400273487
```

> Python's PyCryptodome library implements this with the methods `Crypto.Util.number.bytes_to_long()` and `Crypto.Util.number.long_to_bytes()`. You may first have to install PyCryptodome and import it with `from Crypto.Util.number import *`. 

Convert the following integer back into a message:

```
11515195063862318899931685488813747395775516287289682636499965282714637259206269
```

###  Analyze

给了一个大整数，用函数 `long_to_bytes()` 转化下就解决咯。

### Code

```python
from Crypto.Util.number import *

lis = 11515195063862318899931685488813747395775516287289682636499965282714637259206269
print(long_to_bytes(lis))
```

### SumUp

```python
from Crypto.Util.number import *
Crypto.Util.number.bytes_to_long()
Crypto.Util.number.long_to_bytes()
```

虽然但是，我一定要吐槽 Crypto 库，这也太烦了！

安了之后还是不停报错说没有找到 Crypto 库，又是卸载重装又是重启。

试了一系列办法之后求助互联网，去 `.\python310\Lib\site-packages` 里面把 crypto 文件夹直接重命名为 Crypto，然后它就好了？代码就跑起来了！

装 py 库真的可以治疗低血压。。

## XOR Starter

### Description

XOR is a bitwise operator which returns 0 if the bits are the same, and 1 otherwise. In textbooks the XOR operator is denoted by ⊕, but in most challenges and programming languages you will see the caret `^` used instead.

|  A   |  B   | Output |
| :--: | :--: | :----: |
|  0   |  0   |   0    |
|  0   |  1   |   1    |
|  1   |  0   |   1    |
|  1   |  1   |   0    |

For longer binary numbers we XOR bit by bit: `0110 ^ 1010 = 1100`. We can XOR integers by first converting the integer from decimal to binary. We can XOR strings by first converting each character to the integer representing the Unicode character.

Given the string `"label"`, XOR each character with the integer `13`. Convert these integers back to a string and submit the flag as `crypto{new_string}`.

> The Python `pwntools` library has a convenient `xor()` function that can XOR together data of different types and lengths.

###  Analyze

给了字符串 `"label"`，每个字符和整数 13 异或得到 flag。

顺手下载 pwntools 和 pwn 库喔~

`pip install pwntools`

`pip install pwn`

### Code

```python
def XOR(str, key):
    res=''
    for s in str:
        res += chr(ord(s) ^ key)
    #print(o)
    return res

str = 'label'
flag = XOR(str,13)
print('crypto{%s}'% flag)
```

### SumUp

```python
from pwntools import xor
xor()  # 可以把不同长度和类型的数据进行异或，什么类型的数据都可以喔
```

## XOR Properties

### Description

In the last challenge, you saw how XOR worked at the level of bits. In this one, we're going to cover the properties of the XOR operation and then use them to undo a chain of operations that have encrypted a flag. Gaining an intuition for how this works will help greatly when you come to attacking real cryptosystems later, especially in the block ciphers category.

There are four main properties we should consider when we solve challenges using the XOR operator

```
Commutative: A ⊕ B = B ⊕ A
Associative: A ⊕ (B ⊕ C) = (A ⊕ B) ⊕ C
Identity: A ⊕ 0 = A
Self-Inverse: A ⊕ A = 0
```

Let's break this down. Commutative means that the order of the XOR operations is not important. Associative means that a chain of operations can be carried out without order (we do not need to worry about brackets). The identity is 0, so XOR with 0 "does nothing", and lastly something XOR'd with itself returns zero.

Let's try this out in action! Below is a series of outputs where three random keys have been XOR'd together and with the flag. Use the above properties to undo the encryption in the final line to obtain the flag.

```
KEY1 = a6c8b6733c9b22de7bc0253266a3867df55acde8635e19c73313
KEY2 ^ KEY1 = 37dcb292030faa90d07eec17e3b1c6d8daf94c35d4c9191a5e1e
KEY2 ^ KEY3 = c1545756687e7573db23aa1c3452a098b71a7fbf0fddddde5fc1
FLAG ^ KEY1 ^ KEY3 ^ KEY2 = 04ee9855208a2cd59091d04767ae47963170d1660df7f56f5faf
```

 Before you XOR these objects, be sure to decode from hex to bytes.

###  Analyze

异或的一些性质~

很简单的一点数学运算

$$\begin{align}&FK123⊕K1⊕K23 \\\ =&(F⊕K1⊕K2⊕K3)⊕K1⊕(K2⊕K3) \\\ =&(F⊕K2⊕K3)⊕0⊕(K2⊕K3) \\\ =&(F⊕K2⊕K3)⊕(K2⊕K3) \\\ =&F⊕0 \\\ =&F\end{align}$$

（其实只是想浅试一下数学公式的功能~

### Code

```python
from pwn import xor
K1 = 'a6c8b6733c9b22de7bc0253266a3867df55acde8635e19c73313'
K21 = '37dcb292030faa90d07eec17e3b1c6d8daf94c35d4c9191a5e1e'
K23 = 'c1545756687e7573db23aa1c3452a098b71a7fbf0fddddde5fc1'
FK132 = '04ee9855208a2cd59091d04767ae47963170d1660df7f56f5faf'
flag = xor(bytes.fromhex(FK132), bytes.fromhex(K1), bytes.fromhex(K23))
print(flag)
```

## Favourite byte

### Description

For the next few challenges, you'll use what you've just learned to solve some more XOR puzzles.

I've hidden some data using XOR with a single byte, but that byte is a secret. Don't forget to decode from hex first.

```
73626960647f6b206821204f21254f7d694f7624662065622127234f726927756d
```

###  Analyze

先从16进制 decode 喔

因为之和单个 byte 进行了异或，又知道 flag 的首字母是 `'c'`，所以可以用 decode 之后的第一位和 `'c'` 进行异或得到 secret。

之后把整个字符串和 secret 异或之后输出。

### Code

```python
def xor_byte(a: bytes, b: int):
    res = bytes([_a ^ b for _a in a])
    return res

lis = '73626960647f6b206821204f21254f7d694f7624662065622127234f726927756d'
decode = bytes.fromhex(lis)
secret = decode[0] ^ ord('c')
print(xor_byte(decode, secret))
```

## You either know, XOR you don't

### Description

I've encrypted the flag with my secret key, you'll never be able to guess it.

> Remember the flag format and how it might help you in this challenge!

```
0e0b213f26041e480b26217f27342e175d0e070a3c5b103e2526217f27342e175d0e077e263451150104
```

###  Analyze

第一步当然也是从十六进制 decode 咯。

和上一题的思路有点像捏，也可以利用 flag 的格式得到加密用到的 key。

只要把 `'c'` 换成 `'crypto{'`，然后加上一点点的猜想~~（自动补全）~~就可以咯。

### Code

```python
from pwn import xor


lis = '0e0b213f26041e480b26217f27342e175d0e070a3c5b103e2526217f27342e175d0e077e263451150\
104'
decode = bytes.fromhex(lis)
flag = 'crypto{'
# akey = xor(decode[:7], [ord(o) for o in flag])
# print(akey)
key = 'myXORkey'
print(xor(decode, key))

# akey = myXORke,猜想key = myXORkey
```

## 致谢~~（被迫的）~~

非常感谢 [hawa130](https://hawa130.com/) 对本文排版等问题的细心纠正。
