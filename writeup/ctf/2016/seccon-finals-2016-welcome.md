---
layout: post
redirect_from:
  - /blog/2017/01/30/seccon-finals-2016-welcome/
date: "2017-01-30T03:49:21+09:00"
tags: [ "ctf", "writeup", "seccon", "rev" ]
---

# SECCON finals 2016: 六(6) welcome

<!-- {% raw %} -->

## problem

バイナリ`welcome`と以下の文字列が与えられる。

``` sh
ubuntu:~$ ./welcome SECCON{**************************}
LFU70EN3M7{WW{6LS}O0037I9VKUMSE{VI2C84ZO9UGW154QEYFLYGHN9C0VVR0V94PMD582Y{XTOIJ1
CXTM{CJX8UO8}EIO1{UKR0PSL{{TNHJDCH9GW}F50K}N93_H56E{5M6P45WDOBZDOVM055N6M{2EZD}4
L7{4U9V7NK3NV{}{NAAIE4Z5_}YK2TI0H7A5R6SR_2683W9}{NAQV2CPD86JQ3}4G18TPCSQT{5F6PQU
WZZ3OYFMSRLPJY1MSP3SO{B13MX6Y}NNEIF30INFV2DUAACZ0IGTW{PPGCIJQYUELA18FUEWLJO6XRNE
4RFRLNRI58MQDSG}C5GYZOO64322VTWIP81_8X7B7_TYTSE0O6AOTBIOPQQLTMKXV8TZFBRPBEXU5TVL
M0JAH1BI0OHP}QO3CQILQE1YORJXLSXJE4T60_AKCJH05CCFT8LTPV{C3PB8LN8O_R1BNS{WDC6}{IB}
3JQO6{}EG_}NP_QTTI070ZDUITGWY3A8VKDIH1AJSNYTX5AUOZ0S2N}Y}B18X2N_9GYFZH5YEW{2KE09
_C90O74HFSY9OZB4X2XCUGCRWDSFW7LDSAOYM5HOAP_HLZ1HHYQFV{QYTJNUWW37E1JPA3Q8DXP457EJ
4T2}BSV7RT{4JYMPCTJESJM6MP7I9{37LQ1G5ZZ_ETZRCR2TCDAIXWP_P3QT5026YMZCFSDRPT6N2P7}
OBDTJWF2SQKQOZ9OTFTX05NIL3MJY64EN_KTBDWUI8RJDO{UWTA}NGWZ8MS2UHC}_MP18DITIWEG0QXE
RNQ10E4C7HIVFM{EC9K1TG806NG}E494EP5L656ZN_QCZP9GT8F{S_V47G2YSYBEPJ}XL4C8CDXG24UR
F}FA_EA_HZKLQ0HJXMF_6XTNZJLAGJY6__5OASMJX4Z_WZVSKQS2ENZB_929TR0G2TTSLNSG0{6B64{}
9C9T0G4WK2OD06N{0AHI{Q8KII4Z7LOX5WIO2XWZYJNT_VBA1BF0UHE8BEPW4ZSGLY4_WSMVJGUHF82W
6SPSW5RJY9{11QBFZSOJ3JXSW2B3Q}3Z}H4W08AJNZK}DQ}}
```

## solution

### A

幅$32$で折り返すだけ。

```
LFU70EN3M7{WW{6L S }O0037I9VKUMSE{
VI2C84ZO9UGW154Q E YFLYGHN9C0VVR0V
94PMD582Y{XTOIJ1 C XTM{CJX8UO8}EIO
1{UKR0PSL{{TNHJD C H9GW}F50K}N93_H
56E{5M6P45WDOBZD O VM055N6M{2EZD}4
L7{4U9V7NK3NV{}{ N AAIE4Z5_}YK2TI0
H7A5R6SR_2683W9} { NAQV2CPD86JQ3}4
G18TPCSQT{5F6PQU W ZZ3OYFMSRLPJY1M
SP3SO{B13MX6Y}NN E IF30INFV2DUAACZ
0IGTW{PPGCIJQYUE L A18FUEWLJO6XRNE
4RFRLNRI58MQDSG} C 5GYZOO64322VTWI
P81_8X7B7_TYTSE0 O 6AOTBIOPQQLTMKX
V8TZFBRPBEXU5TVL M 0JAH1BI0OHP}QO3
CQILQE1YORJXLSXJ E 4T60_AKCJH05CCF
T8LTPV{C3PB8LN8O _ R1BNS{WDC6}{IB}
3JQO6{}EG_}NP_QT T I070ZDUITGWY3A8
VKDIH1AJSNYTX5AU O Z0S2N}Y}B18X2N_
9GYFZH5YEW{2KE09 _ C90O74HFSY9OZB4
X2XCUGCRWDSFW7LD S AOYM5HOAP_HLZ1H
HYQFV{QYTJNUWW37 E 1JPA3Q8DXP457EJ
4T2}BSV7RT{4JYMP C TJESJM6MP7I9{37
LQ1G5ZZ_ETZRCR2T C DAIXWP_P3QT5026
YMZCFSDRPT6N2P7} O BDTJWF2SQKQOZ9O
TFTX05NIL3MJY64E N _KTBDWUI8RJDO{U
WTA}NGWZ8MS2UHC} _ MP18DITIWEG0QXE
RNQ10E4C7HIVFM{E C 9K1TG806NG}E494
EP5L656ZN_QCZP9G T 8F{S_V47G2YSYBE
PJ}XL4C8CDXG24UR F }FA_EA_HZKLQ0HJ
XMF_6XTNZJLAGJY6 _ _5OASMJX4Z_WZVS
KQS2ENZB_929TR0G 2 TTSLNSG0{6B64{}
9C9T0G4WK2OD06N{ 0 AHI{Q8KII4Z7LOX
5WIO2XWZYJNT_VBA 1 BF0UHE8BEPW4ZSG
LY4_WSMVJGUHF82W 6 SPSW5RJY9{11QBF
ZSOJ3JXSW2B3Q}3Z } H4W08AJNZK}DQ}}
```

flag: `SECCON{WELCOME_TO_SECCON_CTF_2016}`

### B

`welcome`は`time(NULL)`して挙動を変えているが、連続して実行することでこの値を統一してやれば、ほとんどの部分で出力が一致する。
特に変化のある部分では入力がそのまま出力されている。
これに気付くだけで十分解ける。

``` python
#!/usr/bin/env python2
from pwn import * # https://pypi.python.org/pypi/pwntools
import argparse
parser = argparse.ArgumentParser()
parser.add_argument('--log-level', default='info')
parser.add_argument('--binary', default='./welcome')
args = parser.parse_args()
context.log_level = args.log_level

encrypted = '''\
LFU70EN3M7{WW{6LS}O0037I9VKUMSE{VI2C84ZO9UGW154QEYFLYGHN9C0VVR0V94PMD582Y{XTOIJ1\
CXTM{CJX8UO8}EIO1{UKR0PSL{{TNHJDCH9GW}F50K}N93_H56E{5M6P45WDOBZDOVM055N6M{2EZD}4\
L7{4U9V7NK3NV{}{NAAIE4Z5_}YK2TI0H7A5R6SR_2683W9}{NAQV2CPD86JQ3}4G18TPCSQT{5F6PQU\
WZZ3OYFMSRLPJY1MSP3SO{B13MX6Y}NNEIF30INFV2DUAACZ0IGTW{PPGCIJQYUELA18FUEWLJO6XRNE\
4RFRLNRI58MQDSG}C5GYZOO64322VTWIP81_8X7B7_TYTSE0O6AOTBIOPQQLTMKXV8TZFBRPBEXU5TVL\
M0JAH1BI0OHP}QO3CQILQE1YORJXLSXJE4T60_AKCJH05CCFT8LTPV{C3PB8LN8O_R1BNS{WDC6}{IB}\
3JQO6{}EG_}NP_QTTI070ZDUITGWY3A8VKDIH1AJSNYTX5AUOZ0S2N}Y}B18X2N_9GYFZH5YEW{2KE09\
_C90O74HFSY9OZB4X2XCUGCRWDSFW7LDSAOYM5HOAP_HLZ1HHYQFV{QYTJNUWW37E1JPA3Q8DXP457EJ\
4T2}BSV7RT{4JYMPCTJESJM6MP7I9{37LQ1G5ZZ_ETZRCR2TCDAIXWP_P3QT5026YMZCFSDRPT6N2P7}\
OBDTJWF2SQKQOZ9OTFTX05NIL3MJY64EN_KTBDWUI8RJDO{UWTA}NGWZ8MS2UHC}_MP18DITIWEG0QXE\
RNQ10E4C7HIVFM{EC9K1TG806NG}E494EP5L656ZN_QCZP9GT8F{S_V47G2YSYBEPJ}XL4C8CDXG24UR\
F}FA_EA_HZKLQ0HJXMF_6XTNZJLAGJY6__5OASMJX4Z_WZVSKQS2ENZB_929TR0G2TTSLNSG0{6B64{}\
9C9T0G4WK2OD06N{0AHI{Q8KII4Z7LOX5WIO2XWZYJNT_VBA1BF0UHE8BEPW4ZSGLY4_WSMVJGUHF82W\
6SPSW5RJY9{11QBFZSOJ3JXSW2B3Q}3Z}H4W08AJNZK}DQ}}'''
prefix = 'SECCON{'
masked = len('WELCOME_TO_SE*************')
suffix = '}'
alphabet =  '0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ_'

f = {}
for c in alphabet:
    s = 'WELCOME_TO_SE' + c
    s += 'A' * (masked - len(s))
    assert len(s) == masked
    with process([ args.binary, prefix + s + suffix ]) as p:
        f[c] = p.recvline().strip()
f['*'] = encrypted
for i in range(len(encrypted)):
    if f['0'][i] != f['A'][i]:
        for c in alphabet + '*':
            log.info('%c: %s', c, f[c][i])
```

<!-- {% endraw %} -->