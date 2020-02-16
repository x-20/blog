---
category: blog
layout: post
date: 2016-03-21T21:44:30+09:00
tags: [ "esolang", "competitive", "golf", "brainfuck", "atcoder", "anagol", "hackerrank", "codeiq", "ideone" ]
---

# 各種サービスにおけるbrainfuck処理系について

サービスによって処理系の特性の差があるのでまとめておこうと思った。
どれも記事作製時の情報。c言語の規格がどうとか、処理系の話とバイナリの話は別だとか、そういう細かいことはあまり気にせず適当に読んでね。

## bf

-   <http://packages.ubuntu.com/search?keywords=bf>
-   [man](http://manpages.ubuntu.com/latest/man1/bf.1.html)
-   ubuntuのaptで入る処理系のひとつ
-   普通な処理系

``` sh
$ bf -h
bf - a Brainfuck interpreter       version 20041219
(C) 2003, 2004, Stephan Beyer, GPL, s-beyer@gmx.net

Usage information: 
        ./bf [-h] [options] inputfile

Available options:
        -c<num>   specify number of cells [9999]
        -i        show used code input (stderr)
        -n        translate input: 10 (\n) to 0
        -w        disallow decrementing 0 and incrementing 255
        -,<mode>  set input mode: 0-4 [0]

See the bf(1) manpage for more information.
Have fun!
```

-   c言語製
-   cellは8bit (`char`)
-   EOFは$-1$
-   wrap-aroundする
    -   opiton `-w`でdisallowできる
-   負番地の読み書きは不可
-   規定のcell数が$9999$と少ない
-   cheatは難しい
    -   中で命令を構造体にencodeして保持
    -   unmatchedな`]`は怒られない
        -   先頭付近に跳ぶっぽい変な挙動をする
        -   jumpが発生しないなら素通り
    -   unmatchedな`[`は怒られる
        -   `Error: Out of range! You wanted to '>' beyond the last cell.`という、なにか違うこと検出し停止する
        -   jumpが発生しないなら素通り
-   最適化はほぼない
    -   連続する`+-`や`<>`はある程度まとめられる

### 採用サービス

-   AtCoder
    -   <http://atcoder.jp/>
    -   競技プログラミングのサイト
    -   日本語
    -   `Ubuntu 14.04.4 LTS` `trusty`
    -   beginner contestのA問題B問題あたりはbrainfuck向けの問題が多い
    -   バイナリはubuntuのrepositoryから入手可能

-   HackerRank
    -   <https://www.hackerrank.com/>
    -   競技プログラミングのサイト
    -   `Ubuntu 14.04.4 LTS` `trusty`
    -   wrap-aroundがoption `-w`を使って禁止されている
    -   バイナリはubuntuのrepositoryから入手可能

## BFI

-   <http://esoteric.sange.fi/brainfuck/impl/interp/BFI.c>
-   すごくシンプルな処理系
-   cheatで遊べる

``` c
 * Program Name:  BFI
 * Version:       1.1
 * Date:          2003-04-29
 * Description:   Interpreter for the Brainfuck Programming Language
```

``` sh
$ bfi [<program file> ...]
```

-   c言語製
-   cellは32bit (`int`)
-   EOFは$-1$ (`EOF`)
-   wrap-aroundする
-   特に最適化はない
    -   負数に`[-]`するとTLEなので注意
-   負番地の読み書きは可能
-   cheat可能
    -   codeは`char p[]`として無加工で保持
    -   unmatchedな`]`は先頭に跳ぶ (anagol)
        -   例: `+++<---]>.>\x03ABC.>.`は`ABC`を出力
        -   code領域の範囲を越えて`[`を探しにいく
        -   `$esp`より低位な部分に`[`がひとつだけ置いてあるのが原因
            -   実行環境に強く依存する
            -   `.`により`putchar`を呼ぶと壊れて使えなくなる
            -   `,`の`getchar`なら壊れない
        -   jumpが発生しないなら素通り
        -   yukicoderでは使えないようだ
            -   単独の`.`や`.`を踏んだ程度ではだめ
    -   unmatchedな`[`はsegmentation fault
        -   jumpが発生しないなら素通り
    -   data領域の左側にcode領域がある (anagol, yukicoder)
        -   例: `+[<++ABC]:,:,:,`は`CBA`を出力
    -   提出したcodeの右端には$-1$ (code読み込みの際の`EOF`)
        -   例: `+[<+]<.<.<.ABC`は`CBA`を出力
    -   環境が分かっていれば[rop](https://en.wikipedia.org/wiki/Return-oriented_programming)も可能

### 採用サービス

-   Anarchy Golf
    -   <http://golf.shinh.org/>
    -   code golfのサイト
    -   通称 "golf場"
    -   cheatが可能
        -   むしろ推奨される
            -   普通に書いた際の記録の上書きされて消えるのを避けるためか、` (cheat)`とか名前に付けて示す人は多い
        -   例: `<+]<[.<-]"emspx!-pmmfH`で`Hello, world!`を出力 (22byte, <http://golf.shinh.org/p.rb?hello+world>)
        -   バイナリは入手可能
            -   [Performance checker](http://golf.shinh.org/check.rb)からbashで`base64 /golf/local/bf`
            -   libcも取れる
        -   pwnable
            -   <http://golf.shinh.org/reveal.rb?print+file/kimiyuki_1458840493>
                -   [How to Execute Bash on Brainfuck on Anarchy Golf - うさぎ小屋](http://kimiyuki.net/blog/2016/04/01/bash-on-brainfuck-on-anarchy-golf/)

-   Yukicoder
    -   <http://yukicoder.me/>
    -   ゆるふわな競技プログラミングのサイト
    -   cheatが可能
        -   推奨されるかどうかは知らない
        -   例: `-[<+]<[.<-]"emspX!pmmfH`で`Hello world!`を出力 (23byte, <http://yukicoder.me/submissions/85536>)
        -   バイナリは入手可能
            -   新規問題、#NNNNNを想定解として出力ケースを生成、を使う
                -   この上でdebugは可能だが、gdbで使えない機能がある
        -   64bitな都合上、pwnは厳しい
        -   <https://twitter.com/yukicoder/status/716281465684627458>

## bff

-   <https://github.com/apankrat/bff>
-   ideoneで採用されている処理系

``` sh
$ bff --help
bff: moderately-opimizing Brainfuck interpreter, 1.0.6, http://swapped.cc/bff
Usage: bff [<program file> [<input data file]]
```

-   c言語製
-   cellは32bit (`int`)
-   EOFは変
    -   `argv[2]`として渡されたファイルを読んでいるなら $0$ (`0`)
    -   標準入出力から読んでいるなら$-1$ (`EOF`)
-   wrap-aroundする
-   負番地も使用可能
-   最適化はすこし
    -   `[-]`や`[+]`の特殊化
        -   change logには1.0.6からとあるが実際は1.0.5の途中から
    -   連続する`+-`や`<>`はある程度まとめられる
    -   `[>+<-]`の最適化はない
        -   負数にやるとTLEなので注意
        -   "moderately-opimizing" とは
-   cheatはおそらく不可能
    -   unmatchedな`[` `]`は実行前にちゃんと検出
    -   負番地には対応
    -   中で命令を構造体にencodeして保持

### 採用サービス

-   ideone
    -   <http://ideone.com/>
    -   onlineでコードを実行できるサービス
    -   bff-1.0.5
        -   `[-]`や`[+]`の特殊化あり
    -   バイナリは入手できなさそう

-   CodeIQ
    -   <https://codeiq.jp/>
    -   >   実務スキル評価サービス
    -   日本語
    -   bff-1.0.3.1
        -   data領域の初期化忘れのbugが残っている
    -   裏側にideoneの企業版を利用
    -   バイナリは入手可能
        -   [実行くん](https://codeiq.jp/tools/sandbox/)からbashで`base64 /usr/bin/bff`

<!-- more -->

<!-- atcoder, bf, http://packages.ubuntu.com/trusty/amd64/bf/download, Sun Mar 27 04:44:02 JST 2016

H4sIAGvW8FYCA+xae3QT15mf0QOEAY14xrwHkIsNtmPxSAwYkIyMR0QQAqalaxxFlka2giw50siG
hKYmsgmzwsTNSbPdhmZTds9Jtic5S7ZZl7KpDTHFpGlSStqEvH3SkIww74cxT+33Xc3IYyfa3bN/
7R+Mj+fe73e/+73udx8zox+XOVdpaJpSLg21nEKq32gltFXGTfPTLIAVUyPgPoWaTA0DWq/is1LW
QWW/LFopDTKfFv518F+sSdHFGuugcqrMp5S0qtRT6ss6qPR+jxpUUhSb7oe2UoUyXPjIoJLVpsh1
2sH9NHK/bLlftsyvlD2yYT1D/NPJ/xWyfxWyX0ppl/nsKn6i/5TgJX1ny/hs66Byt8y3e0i/h6Df
MOp/fyl2rpf1ZYoLpaEGlco43BvwV9+38N6AtyDgD0a3Fmwtvq/gvoWFkVDhfGKTSeYtX7txcBxl
m8fLOYDtd2a/9vLC7v989BeF/ZGxh7vtu7eP3YttZvW40SzVRJs0iOXINuRN2z3Vt9FtWnwpPjWT
n78GAWO/A8/PgDPpkR98vUB9N/+5DHhtBvyJDHqfzMDvysCfkwG/nEHOjgz8X2TgX5+B/5cZ8OkZ
8J9kwCdlwOdliP/YDPybMuB1GfCjGfzdlYF/Twb+cAZcl0HOuxn4pQz4kgxx+DgD/7IMetdmkPNa
Bjk/yyDnAcDHUJMo1vLIoPVjlIznDsF9Ml40BKdg3YgE3MGa1FLhclSscXn5MF/jjwh8uGLNykAo
yFe4qwM85XLV1IWCrojgDgsuF+Va3eBaL/OtDLgjET6S6v6dnTc4t9S7/EG/kKrV8MIWfhvUQTN0
iPCCSxAUOt0oCFh3gaw6f9AXkptRCuEG0z1o9n2UzxeIRmrBwvqwPyj4XJ7aLRS/FZS5HA+66qOC
h/KF6vkgBVUwEl3wbEEml8/tD1Bh3h0IhDxURPD6g3APC6EA5eNDPsqjNIQD0Nvl4sPhYMgFkFvw
hxDwqRRC91BUoHxEXx1f56nfRvk8gVCExyboipq3RRpcEX9N0A0aQK8XZXtqw8RQ8JXo8tTVYwE9
QtgH3ZSjXucGA6EfxtO2tnw+Ve50lK50zS9cULgwXbcMVAdq8wsXkVVfQ9Z5TfpfA6u/ZtAfcunl
upbwDJPRYXJfhS+1/9BkTzDJeb2vukmPXAdopV1HvSm34yVM8o/AHaSTTmHP/eT5YXiCeEemx/v9
o3FH+kCmo5ORX5Pez/vvTeUv2jJONR+UeaCX9ynlylXhOSq8SIV/T4VnF6Xw4aq9lchX4Rq1fBWu
U8tX4erzUbEKV58PrCp8uArnVLhBha9T4SPU668Kz1Lhj6jwkep9UYWPUuH1Kny0Ct+qwo0qvEmF
M+p1XIWbVHibCh+jXu9UuHrde0mFj1fhr6jwCSp8vwqfqMIPqPB7VPghFZ6two+p8Ekq/LgKn6zC
T6rwKSq8R4WrD0eSCp+mwi+o8BkqvF+FD9pHLAP4LPW5UoXPVudV7IxB+rUGK0cMXQN4ctFTs1kq
mdMMd2aGFWpI4+mJSvQk4cp5HGmcGonjhA4jjVMicYjQjyKNx8XEfkJXI41TI/ESof8OaZwSiTZC
r0cap0KiidCrkUZzE/WELkUaUz/xCKGXII0pn1hH6PlIY6onrISeizSmeKKI0LOQxtROsISejDSm
dMJE6LFIYyonKEJnIY0pnLhwB2kN0ibiP6FvzAJ6DPGf0JeQHkv8J/RppMcR/wn9JdLjif+E/hjp
CcR/Qp9AeiLxn9B/QPoe4j+h30I6m/hP6INITyL+E/rfkZ5M/Cf0r5CeQvwn9D6kpxL/Cf1zpKcR
/wn9LNLTif9Ab4DEMEkTIX5c63azQZoKI8fFocaJRzmxS3oTRloaDrcO0xgr1YnjLhlATIrEsEnX
7ygkZoF0GkhZwudS1XBZnPTPtwGOHTFZDlV2MTMoy1mH+OeHOfFLLvbVhXUVju5DL023Ulz3YRMp
upvg+VdaAaKu+Nowd7m4/m2WpbiWQ4ImeZwkMTMDHt4OzIGE38gV4H7AxfqNnHiqUVPVdYDkPDBf
qerEpmQPM6OJrAesiv9wvxb4OfEt7vDpFRx9HH3+850o9D+Y6n8i3f9LuX/Tsh0zYR5Gx2yEntKT
EImqo/ogQPTlLpw/XGxZ8XRgEKakTRizsdMET3PAnzxelXgCuiQa4OYj9id6b0HVV8jMaCbzcYOl
7yBOtk7MTWk5BCDWnxT8vtiyaaBE0/CgbIfUBfFcloTaj6+IXU03Rx/AKcHYT/bpr0Cc6OgULr6o
E2pM+03pw1s4JMv2AylVQj+pAQDxZGWXr03aArTY1fs7We5vcSYeXAi3jjdgJDoPwU16GaZTYhva
3Mb8Rv9TEBOTtL5YP92Ao9CUXl++uAmyb9xM2TwaB6mta5Dc76fl/gmHGRgSD4DsAffbNog3JQm9
vqkV8mM3GWFK7KZGyOrEBUFiwNbOj9CiFlDSe7pTwvo2rH/e2Yf1OqyfUMXT9oMyS9LZWm6yfd/W
gYG1bXSIt20VDvHKRsz9XK6lLzot8SIGD8fDETtkWrP4yhrxGrNTBNAhvuc4/I2urHVt0tG6ll5D
n7c1raiMnrT0AZddvMqJ7zjFS43TE0eIBKqkSgjEfk87F/+Na11Jc+LHkdkwjbjDX+qc9GmmXVNS
Gf00dpQexBL+DGxYJfPYxNAou7h6lMqQpxuTZO7kVlbZNtuqbA/bXF2Kfy19wruxc3RvXI5zxx2M
Li6/nOg0G5yi3Zxth5pJ2gcJYo9DDTQ64wCjQg4AA6oEzai1d5dTfEsxIbEVh5xcqnh2jIM0h2hu
tFVABHecKWZR13aQF18WhNT3cnFdTi4xoNmcC42WQ9IqGBaYCg7x90zz57CGWM6WWfp6IZfJqsM6
xcRB3N3IAElvX08mbTSsPucd4hGH53NcR07dIAI4FLAYBDg8f+FaBTPL5b0PEqZRkhcYnOJ5qQRK
mHOvrPH0Aemgz0k/B3GOxeeYndiPE3shH7huu5kcrnoX2Zj2a50LwSNp33Wiwi6eY5r7wBCClonf
SGKqQcjlYl0wapcFi51pn92Zh81M+xnoIPlkls/KmpPRV7kYiKeRG7pDh+ifyBLYgZuK9ASyxisg
XOiOhmneD7rAPrDM1rkJOMA2cuBpnJtgaMwpMq62plvzmGZ8erbvYkH/UtkYpvk0YkMN+mt/yqCP
7OKdzuXoXlcKYZqfRX5AidOvyXwToVMKeUHhewqH0APGpKw93KPjtBowo0BYwOysg8yAeolwD9yX
My01QMesBjrhhgqug7FaICoIQeyP1erohF1FW4FeOECj/vmof4qsv2UTWW8glOPphJBeLyErpE3X
MITPkeTycgvGp7INUtrAtOBE5nYcwaRUzZaB/P0tptjBdpwpE0Fd50zU2Q2LU+Jfb6O+lBHPX5ON
uB+WokQ+7mhKypBdNiXlvbSU+7HTDpQyH1txHzz4b+nWKdjqwVYdLr5514gzzelzGSzu1im4qcyF
KTQOatJRYOnTF0BVJ2SVZJHdiLG/Rba9VByk9yGPej9ps/0gNRVhPSOzR17UcD7Gl30zGVbqbprz
JCXdNWUCwXLENG/HSXgCpsKalq+YlmPo1z7o7mtrup7PNH8zkHdlLWeFDUy7fh+IeoUWWEsSENha
BKCtzIvdrdtXzJOe7oNxbNc/iTwapl0bncy0H7fTp3fcwA2KeaYb5LWcYHadh3JVqy7XvktbBguX
vU+/GrrQwiSU2gdSpxOp/4RSSyQLSOU8+lmArfG856Tf4WiJW9zFPH0c47b4M+ZpnB82cNDh+dC5
+Esmhi/TyKqmhYxl2rPs8Ud19njYEF8/qvltphnPh336Dyfh9ljedL1QqGF2jgKs6Xol0/wJbu7X
q5iWLESWUkwzjauuB1bDD1PJjytpvBYE1hsSp+5g5mI+xhf5J+Eue12sMBt24UK77SpGA2owbwyg
JvqPOF3jiyzAJzmvkq04B+vHoZ54gUgibnRsYiFb8KgsXdXhGke8empuclC+HIHRPT+Q24nVV6A5
nd8dL6VlvA4yEp/dSc1H9KjlXszmvKSaH3LvSDbm3iqw6h+gJq2+ilF6PXtI7u3SJi5A9nacxIzG
47v0EMiHwU7MvaOyT1oKidbbmaY7slmFf6bMf+O2MoFG0soUKcUpYkWDfbcHn4cOOvBUJd7sMM20
krcIuJ98DT53WgGQtl+GeIofd7SpWo9h63qU+DC0xif03MNSHftVDC8DA/ObCX8EnEAdPdAI+DOA
V3a2zbJSCfPlVNzSs2ujeJuclSHPn4d+3fqdcGfmQAS79TvuwUezWM+N2DFNLEnv0gvYtqcJN6rU
SfhrX7fOXFB71G7WUNKuy7hHMc3Xod3eGk3aYndoZk8PULGzWkfrZrNhTWvgjClxikqtd9Il3P77
aWZnD45Fexld4meaT+AkfXov3Etgyl7B0gPns+Y/4H4SzzeTwA2K2v7LStRWoUTx9KCoPXtZiVru
JYzaryYOidpjl0nUnke8R4X/EDti0KTzFzG5x3U8PmOgdSm0dvxshvLsMFVDUalzcpfBubiXaT5A
1u55TT+iCphmI+Tmd9t+5ZJi+99f/LbtH1xSbA9cRNvPTRhi+39cIrZ/PGGI7b+4pNi+QLb9lQHb
0UbpCeDo6Ezbvxma0O4WDkyFQ4bj8NfaB+K6hZyofwyE4xOOMjfJ80BJUJha0si0PAqTpCmnElhu
k+dOJb+bRpcDpkucUebp6PuBphOfyjTX+iPNQTzaW/qkejBR1I+F9sTv0uuPqn0dtPtE/dXxwLBX
6R+f/u1gvnxRCeayC9+ePuJFJZgzLmAwfzl+SDBrL5Jgto4fEkzUnwrm1+chaHw6aHNwf7kEzxy2
DvJg/84dHOYSszQdMEtSuv9i+hwA539YiCaCaE68aDl08BiISx0LZwFTyydRTWVX5/uo4ynQIX4h
3bwwaL3gxM3mk5wYMPfAmuGE8oJTFMz9DvGqU/SaJZjBxVxL0iZ+IL4r6JtWUFGf7U16tpVyin+T
TCAxXix9dIEs0c+OY+H0f9gO3Q2WQ04x37xGBKEfHfwqbdIzwNlyNjqPi2+GY3U8YM52xr1wnIVa
rjMumIsgHYpTz6m2N5+DcPS+kLL96Dmw/by04oJ6KZbPNwHzSTS1h2st+IZhKWdrzkksAJfQFfBn
u7kf3TyOzjjzTtnE2w6xjzt8W+sQP5TGncON/qww1fIJjr9TvOEU++zieVty/Kd4ZuQW90ZPo8EG
NNiEBmejwSwaDFZvT1tNxuNyl/z8Lb82oikNJQjbXKlvCJ6hr+gHXvs7ggIfDkfrBd5bWFhIVZB3
+m6ZeijqF7DcGKx2gyAP72Wrw27PFl6IFFLhaqraV6i8wlde1s8rKFlemF9ZRSrKi/qS5ZuoulR1
flHRQst8y2LK1uD2B/AbBBuqx7f2kSXU2hDrD9ZHBdbnB7jG38AHUTdhEkJsrTvoTbOzORG2gIXW
LcFQY1AGC8GdBnfA72Xd4ZpoHR8UZLahcG4kL/Uh90HQFvKxYYgOP5P9YSjKNrohIl7UN2feHNbN
Fm1dtYqt3ibwhewGnmcLGtO6/sf+Ban+RUX/x/7L57DV/LZQEKhang24IwLr4QOBQiLH89/LUYkp
QTGBUCOR4vOHFTFZFSE2Ego08Pms2+uFah2POiOsWyCs1XyNPxj0B2vyWV8ozPJb3XX1AT6lr9oH
YXWzpWG3P+iLerbAwEEe1Yd5uLOpq4EPR1IDBete7so8FoZ+QT7eF+azGwS+HsaTLeW38eF8tnyd
M5+NFFQjZa2p21oY5IWsrI0Rdw3P4iemcB35srMk/Wp1BIxrZUFtFVspZ09VKnUwc7JA34gCT0kw
Wrcc7IjU8x6/bxsLZDUYB1FC76F7jrcq/Sp+RIFfNpuN1EKoohEInifk5eWMzE19KsrLGlEQVBgF
iDbML0HmWcJaitjczcE8DHoRMDYqjF5/BJO/kfXynjCP+QdBZYtYyGfoqoLmL1oE/fJL6kAxmB7h
BVk9AkvYooKFitVZmANkkHy5ljy2zh2sx1jhONWFwoOCVpjFuRugKRqcCc8J+PFqCRkTiD75lMWG
PJ5omPdSORH8Y3P9wdQEzInkswF/ECrePAwUPUW7dJf8vWB7Ipn8Fyx7k8k3YPO4dSaZ/CN+qzib
TH4F5atQ4jfeUbDMmaHcDGUJlK/C2r0byluwXX+KZyE4dtyCcio8RDhBzl4oA1COupFM/gVKJ5RX
kL6ZTE6F57W9UNq1A98a6MfXU/RWEz1l1HBDG536xoQvVY9LyST5IGQ0rTJmr2ZGNhqaqBWTl85d
YCav4cfLvynJBbvV34Gw72Z8hw/4i+SB3miKaTSBLKNh5Ui5fTt+94B28ouNzUbTBmh0jNxkNMhy
9+J3BWgvS/cHP6D6Bn7DOIsvTaBSajQ9o3EYs/doOSPbqisz5u7W241FO4fZjMWx4XbjI5pXs4zF
NmORzZhbamRLjdnQoxQVETud8jf0bIhruVaRV47ySlFeKcqzpeWVGy9YtT/VfKfEBxS/KlAe7N4r
yIO+0VQHfpXLH6VQnwDtxdD+V42ir3SwPofafu3nNKizf0vdgyOVOB8Becdg/LcPxPkxo2Hg9z5f
QXsuHPX9sn97UF8r6tutsxtzd+pXGotiw+xGq3YWeIZ6bCo9ZWk9+WDvIciHaFqP3chqAkbTSqMB
2zHviuBxaFG6vRo8t5H+mE+Yj+ug/XUEVseG7dSvadXu0TiN1t06TQhYV40kfM/hO3jgE8kPjXbr
dw5zGa2x4c9o9mhbdZqB/MB39ev6k0kNdfe6e9297l53r7vX3ev/83WcSf2u+L96t3rWtqEoqthE
yDRNBaVZWoILIVvUZslY3AyGLh06ZGnAlWM7cvAXshLipWgoGUoHTyE/IJiOmjJlDlm6FS9dSyil
0C4NneLKfudY1sUfgULfoKP7no7v+9J9T+a84F4mlj83Ac+p+6OeZ0HxqNk5XVb2ULsCHfFDoTum
hsVA+SNR/vumVx/oc6CX5l62oWeGeqyBXgbl/OD5gYpSc0RtzQPR7qHMG/oW7lm+4Yb7cGqjqN3x
U5lYfho26/2G313C/01PtScw1PM92PT7C/a2kfmv408du0zHGNcO8Ax4AewCr4DXQP2uwiXgKnAD
mAVuAUvAA+AR8BjYAZ4BL4Bd4BXwGqjj3MIScBW4AcwCt4Al4MHihH63mk5foWnnNUv9FaBZtbpX
tJ5vvljz7F1Yu7V9K79frhTWygVtYDl209GsQqvWbFUVeq4qwf8HMSMXlrnFit1/EHeNitd3WQ6v
XvEwvJZCIyyrF2zP1qyikyu5drWYcwpuZClGznZdu6UYvN/bcQfVsKvlndB13RtclBf1i/lm89/n
0x3ECs7r6FyHFnvPZZxhui80j9G5CbxvUm8t7MeC74HvIWNlBr+vPAk/1evkM/6cIOMp8udFvGJ6
hj5IiPh0iYyPCDw62m6IOJFFbEqIeNfQ4/FtUv+9RGwhn/Fk24jKR+svjndorxGraDNeBeA3tKj+
iTHt30OfJkR8TKfi8VH2H9vvCT7jrZ+Kx+d5rEGS/1YbOTMysj45WLAWZ4x/S/B98P0F0VFIpsB3
ko+44sPxijmez/Re8Lmeni7H15VJ9W+L9y8APwD/lSDI8TsR/Oj8kbI/zPDfEfw2+G3wPyem+w8w
Rkmxf+D5JPm8tM+hB06K/YVxS/4l6k++Cb55S/4njF1SrK88P0ZduS74nAdd4Z/68z9PpvsnfhF8
7m8YuMy56fyvsv3Q85rr4+errM935JFPnXx6PR4/J83/WN1HEvk/Z6wffwH1m+bU0DgAAA==

-->

<!-- anagol, BFI, /golf/local/bf, http://esoteric.sange.fi/brainfuck/impl/interp/BFI.c, Sun Mar 27 04:44:02 JST 2016

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
DCdhX9oe8SWTsJWzXcT9G9kYbm29HQAA

-->

<!-- yukicoder, BFI, /usr/bin/bf, http://esoteric.sange.fi/brainfuck/impl/interp/BFI.c, Mon Apr  4 15:23:20 JST 2016

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
GI4DRQ4gjQRFDiiMBUgOMIYGSA44gwdNDkBsDjhBDjBBDihCDiBCDhhCDhBCDggAFAAAAKwAAADI
/v//AgAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAMAZAAAAAAAAQBkAAAAAAAAAAAAAAAAAAAQAAAAAAAAABAAAAAAAAAAwAAAAAAAAA
yARAAAAAAAANAAAAAAAAALQJQAAAAAAAGQAAAAAAAAAQDmAAAAAAABsAAAAAAAAACAAAAAAAAAAa
AAAAAAAAABgOYAAAAAAAHAAAAAAAAAAIAAAAAAAAAPX+/28AAAAAmAJAAAAAAAAFAAAAAAAAAHgD
QAAAAAAABgAAAAAAAAC4AkAAAAAAAAoAAAAAAAAAXgAAAAAAAAALAAAAAAAAABgAAAAAAAAAFQAA
AAAAAAAAAAAAAAAAAAMAAAAAAAAAABBgAAAAAAACAAAAAAAAAKgAAAAAAAAAFAAAAAAAAAAHAAAA
AAAAABcAAAAAAAAAIARAAAAAAAAHAAAAAAAAAAgEQAAAAAAACAAAAAAAAAAYAAAAAAAAAAkAAAAA
AAAAGAAAAAAAAAD+//9vAAAAAOgDQAAAAAAA////bwAAAAABAAAAAAAAAPD//28AAAAA1gNAAAAA
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAACgOYAAAAAAA
AAAAAAAAAAAAAAAAAAAAAAYFQAAAAAAAFgVAAAAAAAAmBUAAAAAAADYFQAAAAAAARgVAAAAAAABW
BUAAAAAAAGYFQAAAAAAAAAAAAEdDQzogKEdOVSkgNC44LjUgMjAxNTA2MjMgKFJlZCBIYXQgNC44
LjUtNCkAAC5zeW10YWIALnN0cnRhYgAuc2hzdHJ0YWIALmludGVycAAubm90ZS5BQkktdGFnAC5u
b3RlLmdudS5idWlsZC1pZAAuZ251Lmhhc2gALmR5bnN5bQAuZHluc3RyAC5nbnUudmVyc2lvbgAu
Z251LnZlcnNpb25fcgAucmVsYS5keW4ALnJlbGEucGx0AC5pbml0AC50ZXh0AC5maW5pAC5yb2Rh
dGEALmVoX2ZyYW1lX2hkcgAuZWhfZnJhbWUALmluaXRfYXJyYXkALmZpbmlfYXJyYXkALmpjcgAu
ZHluYW1pYwAuZ290AC5nb3QucGx0AC5kYXRhAC5ic3MALmNvbW1lbnQAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABsAAAABAAAA
AgAAAAAAAAA4AkAAAAAAADgCAAAAAAAAHAAAAAAAAAAAAAAAAAAAAAEAAAAAAAAAAAAAAAAAAAAj
AAAABwAAAAIAAAAAAAAAVAJAAAAAAABUAgAAAAAAACAAAAAAAAAAAAAAAAAAAAAEAAAAAAAAAAAA
AAAAAAAAMQAAAAcAAAACAAAAAAAAAHQCQAAAAAAAdAIAAAAAAAAkAAAAAAAAAAAAAAAAAAAABAAA
AAAAAAAAAAAAAAAAAEQAAAD2//9vAgAAAAAAAACYAkAAAAAAAJgCAAAAAAAAHAAAAAAAAAAFAAAA
AAAAAAgAAAAAAAAAAAAAAAAAAABOAAAACwAAAAIAAAAAAAAAuAJAAAAAAAC4AgAAAAAAAMAAAAAA
AAAABgAAAAEAAAAIAAAAAAAAABgAAAAAAAAAVgAAAAMAAAACAAAAAAAAAHgDQAAAAAAAeAMAAAAA
AABeAAAAAAAAAAAAAAAAAAAAAQAAAAAAAAAAAAAAAAAAAF4AAAD///9vAgAAAAAAAADWA0AAAAAA
ANYDAAAAAAAAEAAAAAAAAAAFAAAAAAAAAAIAAAAAAAAAAgAAAAAAAABrAAAA/v//bwIAAAAAAAAA
6ANAAAAAAADoAwAAAAAAACAAAAAAAAAABgAAAAEAAAAIAAAAAAAAAAAAAAAAAAAAegAAAAQAAAAC
AAAAAAAAAAgEQAAAAAAACAQAAAAAAAAYAAAAAAAAAAUAAAAAAAAACAAAAAAAAAAYAAAAAAAAAIQA
AAAEAAAAAgAAAAAAAAAgBEAAAAAAACAEAAAAAAAAqAAAAAAAAAAFAAAADAAAAAgAAAAAAAAAGAAA
AAAAAACOAAAAAQAAAAYAAAAAAAAAyARAAAAAAADIBAAAAAAAABoAAAAAAAAAAAAAAAAAAAAEAAAA
AAAAAAAAAAAAAAAAiQAAAAEAAAAGAAAAAAAAAPAEQAAAAAAA8AQAAAAAAACAAAAAAAAAAAAAAAAA
AAAAEAAAAAAAAAAQAAAAAAAAAJQAAAABAAAABgAAAAAAAABwBUAAAAAAAHAFAAAAAAAARAQAAAAA
AAAAAAAAAAAAABAAAAAAAAAAAAAAAAAAAACaAAAAAQAAAAYAAAAAAAAAtAlAAAAAAAC0CQAAAAAA
AAkAAAAAAAAAAAAAAAAAAAAEAAAAAAAAAAAAAAAAAAAAoAAAAAEAAAACAAAAAAAAAMAJQAAAAAAA
wAkAAAAAAAASAAAAAAAAAAAAAAAAAAAACAAAAAAAAAAAAAAAAAAAAKgAAAABAAAAAgAAAAAAAADU
CUAAAAAAANQJAAAAAAAANAAAAAAAAAAAAAAAAAAAAAQAAAAAAAAAAAAAAAAAAAC2AAAAAQAAAAIA
AAAAAAAACApAAAAAAAAICgAAAAAAAPQAAAAAAAAAAAAAAAAAAAAIAAAAAAAAAAAAAAAAAAAAwAAA
AA4AAAADAAAAAAAAABAOYAAAAAAAEA4AAAAAAAAIAAAAAAAAAAAAAAAAAAAACAAAAAAAAAAAAAAA
AAAAAMwAAAAPAAAAAwAAAAAAAAAYDmAAAAAAABgOAAAAAAAACAAAAAAAAAAAAAAAAAAAAAgAAAAA
AAAAAAAAAAAAAADYAAAAAQAAAAMAAAAAAAAAIA5gAAAAAAAgDgAAAAAAAAgAAAAAAAAAAAAAAAAA
AAAIAAAAAAAAAAAAAAAAAAAA3QAAAAYAAAADAAAAAAAAACgOYAAAAAAAKA4AAAAAAADQAQAAAAAA
AAYAAAAAAAAACAAAAAAAAAAQAAAAAAAAAOYAAAABAAAAAwAAAAAAAAD4D2AAAAAAAPgPAAAAAAAA
CAAAAAAAAAAAAAAAAAAAAAgAAAAAAAAACAAAAAAAAADrAAAAAQAAAAMAAAAAAAAAABBgAAAAAAAA
EAAAAAAAAFAAAAAAAAAAAAAAAAAAAAAIAAAAAAAAAAgAAAAAAAAA9AAAAAEAAAADAAAAAAAAAFAQ
YAAAAAAAUBAAAAAAAAAEAAAAAAAAAAAAAAAAAAAABAAAAAAAAAAAAAAAAAAAAPoAAAAIAAAAAwAA
AAAAAABUEGAAAAAAAFQQAAAAAAAABAAAAAAAAAAAAAAAAAAAAAQAAAAAAAAAAAAAAAAAAAD/AAAA
AQAAADAAAAAAAAAAAAAAAAAAAABUEAAAAAAAACwAAAAAAAAAAAAAAAAAAAABAAAAAAAAAAEAAAAA
AAAAEQAAAAMAAAAAAAAAAAAAAAAAAAAAAAAAgBAAAAAAAAAIAQAAAAAAAAAAAAAAAAAAAQAAAAAA
AAAAAAAAAAAAAAEAAAACAAAAAAAAAAAAAAAAAAAAAAAAAAgZAAAAAAAAeAYAAAAAAAAdAAAALQAA
AAgAAAAAAAAAGAAAAAAAAAAJAAAAAwAAAAAAAAAAAAAAAAAAAAAAAACAHwAAAAAAAIoCAAAAAAAA
AAAAAAAAAAABAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAMAAQA4
AkAAAAAAAAAAAAAAAAAAAAAAAAMAAgBUAkAAAAAAAAAAAAAAAAAAAAAAAAMAAwB0AkAAAAAAAAAA
AAAAAAAAAAAAAAMABACYAkAAAAAAAAAAAAAAAAAAAAAAAAMABQC4AkAAAAAAAAAAAAAAAAAAAAAA
AAMABgB4A0AAAAAAAAAAAAAAAAAAAAAAAAMABwDWA0AAAAAAAAAAAAAAAAAAAAAAAAMACADoA0AA
AAAAAAAAAAAAAAAAAAAAAAMACQAIBEAAAAAAAAAAAAAAAAAAAAAAAAMACgAgBEAAAAAAAAAAAAAA
AAAAAAAAAAMACwDIBEAAAAAAAAAAAAAAAAAAAAAAAAMADADwBEAAAAAAAAAAAAAAAAAAAAAAAAMA
DQBwBUAAAAAAAAAAAAAAAAAAAAAAAAMADgC0CUAAAAAAAAAAAAAAAAAAAAAAAAMADwDACUAAAAAA
AAAAAAAAAAAAAAAAAAMAEADUCUAAAAAAAAAAAAAAAAAAAAAAAAMAEQAICkAAAAAAAAAAAAAAAAAA
AAAAAAMAEgAQDmAAAAAAAAAAAAAAAAAAAAAAAAMAEwAYDmAAAAAAAAAAAAAAAAAAAAAAAAMAFAAg
DmAAAAAAAAAAAAAAAAAAAAAAAAMAFQAoDmAAAAAAAAAAAAAAAAAAAAAAAAMAFgD4D2AAAAAAAAAA
AAAAAAAAAAAAAAMAFwAAEGAAAAAAAAAAAAAAAAAAAAAAAAMAGABQEGAAAAAAAAAAAAAAAAAAAAAA
AAMAGQBUEGAAAAAAAAAAAAAAAAAAAAAAAAMAGgAAAAAAAAAAAAAAAAAAAAAAAQAAAAQA8f8AAAAA
AAAAAAAAAAAAAAAADAAAAAEAFAAgDmAAAAAAAAAAAAAAAAAAGQAAAAIADQCgBUAAAAAAAAAAAAAA
AAAALgAAAAIADQDQBUAAAAAAAAAAAAAAAAAAQQAAAAIADQAQBkAAAAAAAAAAAAAAAAAAVwAAAAEA
GQBUEGAAAAAAAAEAAAAAAAAAZgAAAAEAEwAYDmAAAAAAAAAAAAAAAAAAjQAAAAIADQAwBkAAAAAA
AAAAAAAAAAAAmQAAAAEAEgAQDmAAAAAAAAAAAAAAAAAAuAAAAAQA8f8AAAAAAAAAAAAAAAAAAAAA
AQAAAAQA8f8AAAAAAAAAAAAAAAAAAAAAvgAAAAEAEQD4CkAAAAAAAAAAAAAAAAAAzAAAAAEAFAAg
DmAAAAAAAAAAAAAAAAAAAAAAAAQA8f8AAAAAAAAAAAAAAAAAAAAA2AAAAAAAEgAYDmAAAAAAAAAA
AAAAAAAA6QAAAAEAFQAoDmAAAAAAAAAAAAAAAAAA8gAAAAAAEgAQDmAAAAAAAAAAAAAAAAAABQEA
AAEAFwAAEGAAAAAAAAAAAAAAAAAAGwEAABIADQCwCUAAAAAAAAIAAAAAAAAAKwEAABIAAAAAAAAA
AAAAAAAAAAAAAAAAQAEAACAAAAAAAAAAAAAAAAAAAAAAAAAAXAEAACAAGABQEGAAAAAAAAAAAAAA
AAAAZwEAABAAGABUEGAAAAAAAAAAAAAAAAAAbgEAABIAAAAAAAAAAAAAAAAAAAAAAAAAggEAABIA
DgC0CUAAAAAAAAAAAAAAAAAAiAEAABIAAAAAAAAAAAAAAAAAAAAAAAAApwEAABAAGABQEGAAAAAA
AAAAAAAAAAAAtAEAABIAAAAAAAAAAAAAAAAAAAAAAAAAyQEAACAAAAAAAAAAAAAAAAAAAAAAAAAA
2AEAABECDwDICUAAAAAAAAAAAAAAAAAA5QEAABEADwDACUAAAAAAAAQAAAAAAAAA9AEAABIADQBA
CUAAAAAAAGUAAAAAAAAABAIAABIAAAAAAAAAAAAAAAAAAAAAAAAAGgIAABAAGQBYEGAAAAAAAAAA
AAAAAAAAHwIAABIADQBwBUAAAAAAAAAAAAAAAAAAJgIAABAAGQBUEGAAAAAAAAAAAAAAAAAAMgIA
ABIADQBgBkAAAAAAANoCAAAAAAAANwIAABIAAAAAAAAAAAAAAAAAAAAAAAAASgIAACAAAAAAAAAA
AAAAAAAAAAAAAAAAXgIAABECGABYEGAAAAAAAAAAAAAAAAAAagIAACAAAAAAAAAAAAAAAAAAAAAA
AAAAhAIAABIACwDIBEAAAAAAAAAAAAAAAAAAAGNydHN0dWZmLmMAX19KQ1JfTElTVF9fAGRlcmVn
aXN0ZXJfdG1fY2xvbmVzAHJlZ2lzdGVyX3RtX2Nsb25lcwBfX2RvX2dsb2JhbF9kdG9yc19hdXgA
Y29tcGxldGVkLjYzMzcAX19kb19nbG9iYWxfZHRvcnNfYXV4X2ZpbmlfYXJyYXlfZW50cnkAZnJh
bWVfZHVtbXkAX19mcmFtZV9kdW1teV9pbml0X2FycmF5X2VudHJ5AEJGSS5jAF9fRlJBTUVfRU5E
X18AX19KQ1JfRU5EX18AX19pbml0X2FycmF5X2VuZABfRFlOQU1JQwBfX2luaXRfYXJyYXlfc3Rh
cnQAX0dMT0JBTF9PRkZTRVRfVEFCTEVfAF9fbGliY19jc3VfZmluaQBwdXRjaGFyQEBHTElCQ18y
LjIuNQBfSVRNX2RlcmVnaXN0ZXJUTUNsb25lVGFibGUAZGF0YV9zdGFydABfZWRhdGEAZmNsb3Nl
QEBHTElCQ18yLjIuNQBfZmluaQBfX2xpYmNfc3RhcnRfbWFpbkBAR0xJQkNfMi4yLjUAX19kYXRh
X3N0YXJ0AGdldGNoYXJAQEdMSUJDXzIuMi41AF9fZ21vbl9zdGFydF9fAF9fZHNvX2hhbmRsZQBf
SU9fc3RkaW5fdXNlZABfX2xpYmNfY3N1X2luaXQAX0lPX2dldGNAQEdMSUJDXzIuMi41AF9lbmQA
X3N0YXJ0AF9fYnNzX3N0YXJ0AG1haW4AZm9wZW5AQEdMSUJDXzIuMi41AF9Kdl9SZWdpc3RlckNs
YXNzZXMAX19UTUNfRU5EX18AX0lUTV9yZWdpc3RlclRNQ2xvbmVUYWJsZQBfaW5pdAA=

-->

<!-- codeiq, bff-1.0.3.1, /usr/bin/bff, http://packages.ubuntu.com/trusty/amd64/bf/download, Sun Mar 27 04:44:02 JST 2016

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

-->

---

# 各種サービスにおけるbrainfuck処理系について

-   Sun Mar 27 04:44:02 JST 2016
    -   バイナリ取れるあたりのことを追記
-   Mon Apr  4 15:23:20 JST 2016
    -   yukicoderにbrainfuckが追加されたので追記
    -   ついでにanagolのunmatchedな`]`をきちんと調べた
-   Mon Dec  5 10:13:11 JST 2016
    -   bfのcell数が少ないことを明記