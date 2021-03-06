---
category: blog
layout: post
date: "2017-03-05T23:58:40+09:00"
tags: [ "ctf", "writeup", "seccon" ]
---

# サイバーコロッセオ x SECCON 2016: 感想

<http://2016.seccon.jp/news/#137>

[tsun](https://twitter.com/No___Op)さん,[hikalium](https://twitter.com/hikalium)さん,[make_now_just](https://twitter.com/hikalium)さんとのチームで参加した。$6$位。

## 問題

### 1

適当に自動化した。
提出する素数は`Crypto.Util.number.getPrime`をそのまま。
安全な素数だとかそういうのまでは手が回らなかった。

### 2

binary系の問題。`nc 10.100.2.1 11111`すると`/bin/ash`に繋がる。
`fork`が禁止されているが、`execve`を叩けばよい。

``` sh
$ nc
exec cat keyword1.txt
SECCON{connect_port_22222}
```

`keyword2.txt`, `keyword3.txt`については`setuid`されたバイナリがソースコード付きで置いてあり、脆弱性も攻撃方法も自明なので単にshellcodeを書いて投げるだけ。でも精進が足りないので少し手間取った。
`connect_port_22222`とあるが、見落としていても全て解ける。

defense keywordの提出までしたらかなりの得点源になった。

### 3

tsunさんとhikaliumさんが独立に$1$つ目を解いていた。
私はすこし触ってみたなのに気付かなかった。

### 4

適当にしてたら運良く$1$つ目まで解けた。

追記: md5して先頭が`fee1dead`になるようなファイルを作れという問題があった(想定解は`md5sum`という偽の実行ファイルを作ってPATHを通す)が、後から計算させたら`E9qKvk`と出た。$15$分程度だった。

### 5

はい。$3$つ目は分からず。

``` sh
$ cat keyword1.txt
$ nano keyword2.txt
```

### 6

見てない。

## 感想等

-   チームの$4$人全員バイナリ系の人間のはずなのにバイナリ問がほぼ存在しなかったのが敗因。でもまあ$6$位なので問題の苦手感の割には良い順位か
-   チームのひとりが体調不良で欠席になった。体調崩すとかはどうしようもないので仕方がないが、話をしたい人のひとりだったので残念
-   先日のSECCON finalsはKing of Hillと言いつつJeopardyの比重が重いように感じられ残念だったが、そうでなくなっていてよかった
-   個々の問題に関しては、すごく酷いということもないとはいえ特に良いということもない感じ
-   チームの人から、オンサイトのCTFは実質始めてだったけど楽しかった、みたいな感想が聞かれたのよかった
-   終了後、適当に集まって酒を飲んで楽しんだ。その後ホテルで某プロコン予選に出たが、酒パワーで通過できた
-   明日はチームのひとりとpwn会をすることになった

---

# サイバーコロッセオ x SECCON 2016: 感想

-   2017年  3月 11日 土曜日 20:40:09 JST
    -   md5のそれに関して追記
