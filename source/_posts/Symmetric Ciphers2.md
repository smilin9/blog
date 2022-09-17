---
title: Symmetric Ciphers2
date: 2022-06-27 14:00:00
tags: cryptohack
categories: 学习 
mathjax: true
---

# SYMMETRIC STARTER

## Modes of Operation Starter

### Description

The previous set of challenges showed how AES performs a keyed permutation on a block of data. In practice, we need to encrypt messages much longer than a single block. A *mode of operation* describes how to use a cipher like AES on longer messages.

All modes have serious weaknesses when used incorrectly. The challenges in this category take you to a different section of the website where you can interact with APIs and exploit those weaknesses. Get yourself acquainted with the interface and use it to take your next flag!

Play at http://aes.cryptohack.org/block_cipher_starter

###  Analyze

网站点出去是给了加密方式和过程，一开始以为是要看加密解密函数，但是发现他密文也没给，就很迷。去问了问书记 `@chal.route('/block_cipher_starter/decrypt/<ciphertext>/')` 是啥意思，发现 @ 这个东西表示网页路径，上面这个的网页路径就是 `/decrypt/<ciphertext>/`， 其中 <ciphertext> 是访问需要的参数，另一个访问不需要参数。就直接请求第二个，得到 `encrypt_flag()`：

```
{"ciphertext":"64cede86f533b58fe197a0c9aca6f8d5a7b4389c629cef1c9e90f4e5fe7629a4"}
```

然后将密文填进解密函数的参数值里面请求得到：

```
{"plaintext":"63727970746f7b626c30636b5f633170683372355f3472335f663435375f217d"}
```

明文丢进十六进制解码器得到 flag。

## Passwords as Keys

### Description

It is essential that keys in symmetric-key algorithms are random bytes, instead of passwords or other predictable data. The random bytes should be generated using a cryptographically-secure pseudorandom number generator (CSPRNG). If the keys are predictable in any way, then the security level of the cipher is reduced and it may be possible for an attacker who gets access to the ciphertext to decrypt it.

Just because a key looks like it is formed of random bytes, does not mean that it necessarily is. In this case the key has been derived from a simple password using a hashing function, which makes the ciphertext crackable.

For this challenge you may script your HTTP requests to the endpoints, or alternatively attack the ciphertext offline. Good luck!

Play at http://aes.cryptohack.org/passwords_as_keys

###  Analyze

一眼先请求 `encrypt_flag()`：

```
{"ciphertext":"c92b7734070205bdf6c0087a751466ec13ae15e6f1bcdd3f3a535ec0f4bbae66"}
```

把字典保存到本地，读到列表 words 里遍历，将其中的元素依次作为 key，创建 AES 对象 cipher，过滤出以 `crypto{` 开头的解密文本就是 flag。

本来是打算爆破然后和密文对比，发现会出好多奇奇怪怪的超时错误，遍历也有问题，9w 多行属实有点离谱。

### Code

```python
from Crypto.Cipher import AES
import hashlib

ciphertext = bytes.fromhex('c92b7734070205bdf6c0087a751466ec13ae15e6f1bcdd3f3a535ec0f4bbae66')

with open("./1.txt") as f:
    words = [w.strip() for w in f.readlines()]
for w in words:
    key = hashlib.md5(w.encode()).digest()
    cipher = AES.new(key, AES.MODE_ECB)
    decoded_text = cipher.decrypt(ciphertext)
    if decoded_text.startswith('crypto{'.encode()):
        print(decoded_text)
        exit
```

### SumUp

```python
from Crypto.Cipher import AES
cipher = AES.new(key, AES.MODE_ECB)
```

# BLOCK CIPHERS

## ECB CBC WTF

### Description

Here you can encrypt in CBC but only decrypt in ECB. That shouldn't be a weakness because they're different modes... right?

Play at http://aes.cryptohack.org/ecbcbcwtf

###  Analyze

第一眼无脑请求 `encrypt_flag()`，但偶然发现这个东西每次请求到的密文都不一样，顿悟：“喔这是分组密码”。。。

然后来看图

![QQ图片20220723214144](https://img.hawa130.com/QQ图片20220723214144.jpg)

获取到的密文长度是 32*3，由代码得出偏移量是前 32 位，把获取到的 ciphertext 分为三组先：

```
{"ciphertext":"734aaeb24447ac787dfb0cf09f7c3371246be4c9b9974bf6faad679a36dd42c8e529bb2b2cd1bba8afa460364d5d0ec8"}

734aaeb24447ac787dfb0cf09f7c3371  #iv
246be4c9b9974bf6faad679a36dd42c8  #第一组密文
e529bb2b2cd1bba8afa460364d5d0ec8  #第二组密文
```

先解第二组密文，把它填入解密函数的参数位置，得到异或后的密文 

```
{"plaintext":"7b5f92f988f314c7cdf246bb17fc63b5"}
```

这是第二组明文和第一组密文异或的结果，由于第一组密文已知，所以直接和它进行异或得到第二组明文 

```
5f34763031645f31375f21212121217d
```

同理，第一组密文解密之后得到 

```
{"plaintext":"1038d7c23028d74b1e9953c5ea1f5844"}
```

将其与偏移量进行异或得到第一组明文 

```
63727970746f7b3363625f3575636b35
```

拼接得到明文为

```
63727970746f7b3363625f3575636b355f34763031645f31375f21212121217d
```

hex 解码之后得到 flag。

## ECB Oracle

### Description

ECB is the most simple mode, with each plaintext block encrypted entirely independently. In this case, your input is prepended to the secret flag and encrypted and that's it. We don't even provide a decrypt function. Perhaps you don't need a padding oracle when you have an "ECB oracle"?

Play at http://aes.cryptohack.org/ecb_oracle

###  Analyze









### Code

```python

```

### SumUp

## Flipping Cookie

### Description



###  Analyze



### Code

```python
from Crypto.Cipher import AES
import requests
import json
import copy

r = requests.get('http://aes.cryptohack.org/flipping_cookie/get_cookie/')
cookie_payload = json.loads(r.text)["cookie"]

iv = cookie_payload[:32]
cookie = cookie_payload[32:]

iv_new = copy.deepcopy(iv)
xor_value = (0x12 << 72 | 0x13 << 64 | 0x19 << 56 | 0x16 << 48 | 0x5e << 40) & ((0xFFFFFFFFFF << 88) >> 48)

iv_new = int(iv_new, 16) ^ xor_value 

print(hex(iv_new))

get_param = 'http://aes.cryptohack.org/flipping_cookie/check_admin/' + cookie + '/' + str(hex(iv_new))[2:] + '/'
r2 = requests.get(get_param)

print(r2.text)
```

### SumUp

## Lazy CBC

### Description



###  Analyze



### Code

```python

```

### SumUp

## Triple DES

### Description



###  Analyze



### Code

```python

```

### SumUp
