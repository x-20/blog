---
layout: post
redirect_from:
  - /writeup/algo/atcoder/abc-089-b/
  - /blog/2018/03/08/abc-089-b/
date: "2018-03-08T12:03:40+09:00"
tags: [ "competitive", "writeup", "atcoder", "abc", "brainfuck" ]
"target_url": [ "https://beta.atcoder.jp/contests/abc089/tasks/abc089_b" ]
---

# AtCoder Beginner Contest 089: B - Hina Arare

## solution

入力中に`Y`が存在するかどうか判定。$O(N)$。

## implementation

brainfuck コメント削除すれば$158$byte

``` brainfuck
>+[-
    ->++++++++[<----------->-] 89
    >+<<
    [>>-<<[-]]
,+]
>+>[>
    -[+[>---<<]>+]>. 70
    >>+[+>+[<]>->]<.
    ++++++.
    ---.
    >>
]<[-
    -[>+<---]>-. 84
    >++++[<+++++>-]<.
    ++++++++++.
    -------------..
    >
]
```
