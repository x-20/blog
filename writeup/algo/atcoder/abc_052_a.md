---
layout: post
redirect_from:
  - /writeup/algo/atcoder/abc-052-a/
  - /blog/2017/04/05/abc-052-a/
date: "2017-04-05T16:29:17+09:00"
tags: [ "competitive", "writeup", "atcoder", "abc" ]
"target_url": [ "https://beta.atcoder.jp/contests/abc052/tasks/abc052_a" ]
---

# AtCoder Beginner Contest 052: A - Two Rectangles

``` sml
fun solve a b c d =
  Int.max(a * b, c * d)

fun readInt() = TextIO.scanStream (Int.scan StringCvt.DEC) TextIO.stdIn
val SOME a = readInt()
val SOME b = readInt()
val SOME c = readInt()
val SOME d = readInt()
val () = print(Int.toString(solve a b c d) ^ "\n")
```
