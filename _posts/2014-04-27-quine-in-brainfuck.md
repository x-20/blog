---
category: blog
layout: post
date: 2014-04-27T18:00:42+09:00
tags: [ "quine", "brainfuck", "haskell", "esolang", "metaprogramming" ]
---

# brainfuckでquine書いた

-   haskellを使って書いた
-   [solorab/brainfuck-hs](https://github.com/kmyk/brainfuck-hs)
-   1763chars

``` brainfuck
>+++++>+++++>++>++>++>+>+++>+++>+++>++++>++++++>+++>++++++>++>++>++>++>+++++>+++
>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+
>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+++++++>++++>++++>++++>++++>++++>++
++>++++>++++>++++>++++>++++>++++>++++>++++>++++>++++>++++>++++>++++>++>+++++>+++
>+++++++>+++>+>++>++>++++>++++++>+++>+++++>++++>++++++>++>++>++++++>+++>+++>+++>
+++++>+++>++++++>++>+++++>+++>+++>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>
+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>++>++>++++>+++>+>++>+++++>++++>+++++>+++
+>+++++>++++>+++++>++++>+++++>++++>+++++>++++>+++++>++++>+++>++++>++>++++++>+++>
+++++>+++>+>+>+>++>+++++>++++>++++++>++++++>++>++++++>+++>+++++>+++>+>+>+>+>+>+>
+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>
+>+>+>+>++>+++++>++++>++++++>++++++>++>++++++>+++>+++++>+++>+>+>+>+>+>+>+>+>+>+>
+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>++>+
++++>++++>++++++>++++++>++>++++++>+++>+++++>+++>+>+>++>+++++>++++>++++++>++++++>
++>++++++>+++>+++++>+++>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>++>+++++>++++>++++++>+
+++++>++>++++++>+++>+++++>+++>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>+>++>+++++>+++
+>++++++>++++++>++>++++++>+++>+++++>+++>++>+++++>++++>++++++>++++++>+++>+++++++>
+++++>++++>++++++>++>++>++>++++++>+>+>+>+>+>+>+>+>+>+>+++++++
[[>>>+<<<-]<]>>>>
[<++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++.---------------
---->[<.<+>>-]<[-]>>]<<<
[<]>
[<<+++++++++++++++++++++++++++++++++++++++++++>>-<+>[-[-[-[-[-[-[-<->]<[<+++>[-]
]>]<[<++++++++++++++++++++++++++++++++++++++++++++++++++>[-]]>]<[<++++++++++++++
++++++++++++++++++++++++++++++++++>[-]]>]<[<++>[-]]>]<[<+++++++++++++++++>[-]]>]
<[<+++++++++++++++++++>[-]]>]<[<>[-]]<.[-]>>>]
++++++++++.
```

<!-- more -->

## 処理手順

### data読み込み

`><+-[].,`を`1..8`に適当に対応付け、1文字1byteで格納  
先頭byteを`0`とし番兵、data末尾で停止

### data移動

``` brainfuck
[[>>>+<<<-]<]>>>>
```

作業用空間の確保のため、全体を3byte右へずらしながら先頭へ  
番兵`0`でwhileを脱出し、data部分の先頭に移動し停止

### data部出力

``` brainfuck
[<++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++.---------------
---->[<.<+>>-]<[-]>>]<<<
```

`>`を出力し、`*ptr`を`+`にして準備  
`+`を出力しつつdataを次回利用のため後方に保存  
dataを舐め終わると、作業領域の分だけ多めに左へ戻る

### 先頭へ

``` brainfuck
[<]>
```

### program部出力

``` brainfuck
[<<+++++++++++++++++++++++++++++++++++++++++++>>-<+>[-[-[-[-[-[-[-<->]<[<+++>[-]
]>]<[<++++++++++++++++++++++++++++++++++++++++++++++++++>[-]]>]<[<++++++++++++++
++++++++++++++++++++++++++++++++++>[-]]>]<[<++>[-]]>]<[<+++++++++++++++++>[-]]>]
<[<+++++++++++++++++++>[-]]>]<[<>[-]]<.[-]>>>]
```

`><+-[].`を出力しながら右へ進む  
dataはもう使わないので破壊的に舐める  
`decr ; if (non-0) then { *recur* } else { write }`のようにし、1~8のswitch文のようなものを実現  
`+`の共通部分と`.`は共通化している

最も短縮の余地を残す部分である

### 改行出力

``` brainfuck
++++++++++.
```