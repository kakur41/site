---
title: 'TSGCTF2019振り返り'
date: 2023-10-28T07:40:03+09:00
draft: false
categories:
  - Crypto
tags:
  - TSGCTF
---

# TSGCTF2019
来週にTSGCTFがあるので問題を振り返る

## Crypto

### OPQRX
Rubyで書かれたRSA。\
GPTでpythonに変換させると下記のようになる。
```python
bits = 4096
P, Q = [getprime(bits) for _ in range(2)]

F = int(open('flag.txt', 'rb').read().hex(), 16)
X = P ^ Q
N = P * Q

if not F < N:
    raise ValueError("F must be < N")

E = 65537
C = pow(F, E, N)

with open('flag.enc', 'w') as f:
    f.write(f'X = {X}\nN = {N}\nE = {E}\nC = {C}\n')
```
普通のRSAと異なる点は$ X = P \oplus Q $が与えられることだ。
2つの数の掛け算の下位nビットは元の下位nビットのみに依存する。
したがって、下位から候補を枝刈り的に探索できる。(時間はかかる)
```python
def search(d, P, Q):
  print(d)
  if P*Q==N:
    D = pow(E, -1, (P-1)*(Q-1))
    print(long_to_bytes(pow(C, D, N)))
    exit(0)
  if d>4192:
    if P*Q==N:
      D = pow(E, -1, (P-1)*(Q-1))
      print(long_to_bytes(pow(C, D, N)))
      exit(0)
    return
  for i in range(2):
    P2 = P | ((i<<d) & (1<<d))
    Q2 = Q | ((X^(i<<d)) & (1<<d))
    m = (1 << (d+1)) - 1
    if ((P2*Q2) & m)==(N & m):
      search(d+1, P2, Q2)
search(0, 0, 0)
```
### OMEGA
アイゼンシュタイン環でのCRTを計算する問題。\
ただdivの中で浮動小数点が現れるので工夫が必要。\
またCRTの最後の和の部分で、i=1,4で同伴という概念により特定の単位を乗じる必要がある。

### Curve
パラメータ脆弱な楕円曲線上の離散対数問題。
以下は問題のpythonコード版(FLAG込み)
```python
from Crypto.Util.number import *
p = 2 ** 256 - 2 ** 32 - 313441
F = FiniteField(p)
E = EllipticCurve(F, [0, 7])
Gx = 0x79be667ef9dcbbac55a06295ce870b07029bfcdb2dce28d959f2815b16f81798
Gy = sqrt(Gx ** 3 + F(7))
print(Gy)
G = E(Gx, -Gy)
flag = bytes_to_long(b"TSGCTF{BewareOfTheSh@rpCurves!!}")
h = flag * G
assert h == E(0x56df2adff3c3749cc4c62c9e7da339dc02d157868a1d76f9d058d634d6a9525f, 0xc167d7eb600437e2d6ead69ebcf2b1b2f88939c0fafda0b19aa3db33f5024b43)
```
orderが因数分解できるので小さい問題に分けてCRTで合わせて解ける。\
しかし若干ビット数が足りないため調整が必要らしい。

### Millionaire Girl
パス
