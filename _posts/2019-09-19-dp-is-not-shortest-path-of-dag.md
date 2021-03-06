---
category: blog
layout: post
redirect_from:
    - "/blog/2019/09/18/dp-is-not-shortest-path-of-dag/"
date: 2019-09-19T00:00:00+09:00
tags: [ "competitive", "dp", "dag" ]
---

# DPはDAG上の最短経路ではない

<small>
[DP](https://ja.wikipedia.org/wiki/動的計画法) の説明をするときに [DAG](https://ja.wikipedia.org/wiki/有向非巡回グラフ) という語を持ち出す競プロerは多いように思います。
しかしこれは不適切です。
この記事はこの状況をなんとかするために書かれました。<sup><a href="#1">1</a></sup>
</small>

<small>
想定読者: AtCoder 青以上
</small>

<br>

## 反例: $$G = (\mathbb{Z}, \lt)$$

DAG $$G = \mathbb{Z} = (\mathbb{Z}, \lt)$$ は主張「DP は DAG 上の最短経路である」の反例のひとつです。
ただし $$\mathbb{Z} = \lbrace \dots, -2, -1, 0, 1, 2, \dots \rbrace$$ は整数の全体からなる集合であり、$$\lt = \lbrace (x, y) \mid x \lt y \rbrace = \lbrace \dots, (-1, 0), (-1, 1), (-1, 2), \dots, (0, 1), (0, 2), (0, 3), \dots, (1, 2), (1, 3), (1, 4), \dots, \dots \rbrace$$ は整数上の通常の順序です。
図を書くと以下のようになります。

<object type="image/svg+xml" data="/blog/2019/09/19/dp-is-not-shortest-path-of-dag/int.svg"></object>

たとえば、整数から整数への関数 $$f : \mathbb{Z} \to \mathbb{Z}$$ であって $$f(x + 1) = 2 f(x)$$ という式で定義されるものを考えてみましょう。
この関数 $$f$$ は正しく定義されています。
関数 $$f$$ は DAG $$G$$ 上 (特にその部分グラフ $$G' = (\mathbb{Z}, R)$$ ただし $$xRy \iff y = x + 1$$ 上) の漸化式によって定義されています。
しかしこの関数の値を、DAG $$G$$ を使った動的計画法によって求めることはできません。
よって「DP は DAG 上の最短経路である」という説明は誤りです。

<br>
<br>
<br>
<hr>
<br>
<br>
<br>

## 反例に対してどのような修正がありうるか

有力だった DP の説明「DP は DAG 上の最短経路である」に対する反例 $$G = \mathbb{Z}$$ が見つかったとなれば、我々は代わりとなる新たな DP の説明を探さなければなりません。
これには主にふたつの選択肢があります。

1.  $$\mathbb{Z}$$ が無限個の要素を持つことを原因であるとし、「DP は有限の DAG 上の最短経路である」と修正すればよいとする選択肢。
1.  「DAG」という言葉を使うことをまったく諦め、別の説明を探すという選択肢。


## 「DP は有限の DAG 上の最短経路である」はなお不適切である

ここで別の DAG $$H = \mathbb{N} = (\mathbb{N}, \lt)$$ を考えてみましょう。
ただし $$\mathbb{N} = \lbrace 0, 1, 2, \dots \rbrace$$ は自然数の全体からなる集合であり、$$\lt = \lbrace (x, y) \mid x \lt y \rbrace = \lbrace (0, 1), (0, 2), (0, 3), \dots, (1, 2), (1, 3), (1, 4), \dots, \dots \rbrace$$ は自然数上の通常の順序です。
図を書くと以下のようになります。

<object type="image/svg+xml" data="/blog/2019/09/19/dp-is-not-shortest-path-of-dag/nat.svg"></object>

自然数から自然数への関数 $$g : \mathbb{N} \to \mathbb{N}$$ を $$g(0) = 1$$ かつ $$g(x + 1) = 2 g(x)$$ という式で定義します。
この関数 $$g$$ は DAG $$H$$ 上 (特にその部分グラフ $$H' = (\mathbb{N}, R)$$ ただし $$xRy \iff y = x + 1$$ 上) の漸化式によって定義されています。

関数 $$g$$ は関数 $$f$$ とほとんど同じ構成ですが、しかし DAG $$H$$ を使った動的計画法によって関数の値を求めることができます。
そして $$\mathbb{N}$$ は $$\mathbb{Z}$$ と同様に無限個の要素を持ちます。
つまり、単に「有限の」と付けるだけでは不適切だと言えるでしょう。

これに対する反論として「計算に必要な部分グラフだけを切り取れば有限だ」というものが考えられます。
つまり、実際に求める値は固定された $$a \in \mathbb{N}$$ に対しての $$g(a)$$ のみであり、その計算に必要であるのは $$a$$ 以下の自然数に対応する有限個の頂点のみである。
どんなグラフであっても不要な頂点を付け加えて無限グラフにすることはできるのだから、これは「DP は有限の DAG 上の最短経路である」の反例にはなっていない、というものです。

ところでここで、計算に真に無限の時間を使えた場合を想像してみましょう。
関数 $$g : \mathbb{N} \to \mathbb{N}$$ の無限個の値 $$g(0), g(1), g(2), \dots, g(n), \dots$$ は「無限の時間があれば、動的計画法ですべてを計算できる」と言ってよいでしょう。
しかし関数 $$f : \mathbb{Z} \to \mathbb{Z}$$ の無限個の値 $$\dots, f(-n), \dots, f(-2), f(-1), f(0), f(1), f(2), \dots, f(n), \dots$$ は「無限の時間があったとしても、動的計画法ではひとつも計算できない」ものです。
関数 $$g$$ の値のすべてを求めるのにはグラフ $$H = \mathbb{N}$$ の全体を使うことになるので「計算に必要な部分グラフだけを切り取れば有限だ」という主張は怪しいものになります。
もしこの $$G = \mathbb{Z}$$ と $$H = \mathbb{N}$$ の差を含めて説明できるような、より優れた DP の説明が存在するならば、 DAG を用いた説明は捨てられるべきです。
そしてそのような説明は実際に存在します。このため、やはり「DAG」という言葉を使う説明は不適切だと言えます。


## DAG ではなく「終端がある無限長の有向路が存在しないグラフ」、つまり整礎関係と言えばよい

DAG という説明が不適切であるならば、代わりとなる説明を探す必要があります。
これは $$G = \mathbb{Z}$$ の場合と $$H = \mathbb{N}$$ の場合の差を見れば解決します。
ある頂点 $v$ での値を考えたとき、 $v$ で終わるような無限長の有向路 $\dots \to v_3 \to v_2 \to v_1 \to v$ があれば無限後退に陥いってしまうため計算ができません。
そもそも計算を始めることすらできません。
一方でそのような無限長の有向路がなければ、有限の有向路たちの始点から順番に計算をしていくことで値が求まります。
入次数が無限である頂点があると有限の時間内には計算が終わらないでしょうが、無限の時間をかければ計算が可能なのでそのような場合は無視してもかまいません。
よって「終端がある無限長の有向路が存在しない」ことが条件として妥当です。

このような「終端がある無限長の有向路が存在しないグラフ」に対応する言葉として[整礎関係](https://ja.wikipedia.org/wiki/整礎関係)というものがあります。
ちなみに通常はこの整礎関係という語は主に順序を扱う文脈で登場するため「無限降下列 $\dots \lt v_3 \lt v_2 \lt v_1 \lt v$ が存在しない」という言葉で定義されます。
よってこれを使って「DP とは整礎関係に沿った計算である」という説明が適切です。


## そのような説明は何がうれしいのか: 帰納法との関係

グラフが有限の場合には、無限長の有向路は常に有向閉路です。
DAG とは有向非巡回グラフ、つまり「有向閉路を持たないグラフ」でした。
よって有限の場合には、整礎関係であることと DAG であることは一致します。
競プロで出現するのはたいてい有限の場合であり、有限の場合には DAG も整礎関係も一致するのであれば、「無限のグラフの場合に〜」という指摘は単なる揚げ足取りだとして無視してしまってはいけないのでしょうか？

もちろん無視することはできません。
動的計画法に関連するグラフが整礎関係であると認識することは、動的計画法と数学的帰納法の関連や、動的計画法と関数の再帰的定義の関係を明らかにします。
前者は正確には数学的帰納法ではなく、その一般化である[整礎帰納法](https://ja.wikipedia.org/wiki/数学的帰納法#整礎帰納法)と関連があります。
たとえば、帰納法においては「定理を強める」ことによってむしろ証明ができるようになることがありますが、動的計画法においても同様に「求める範囲を広げる」ことによって計算ができるようになることがある、という類似があります。
このような類似を把握しておくことは有益です。

<br>
<hr>
<br>

-   <a name="1">1</a>:
    <small>
    もちろん「最短路」という部分も同様に不適切ですが、「DAG」と比べて害も感染力も小さいため今回は無視します。
    ところで元々の主張 ([DPの話 - aizuzia](https://tayama-2.hatenadiary.org/entry/20111210/1323502092)) においては「たいていの DP は tropical semiring 的な枠組みの上に乗せられるものだよね」という面白いものであったようですが、伝言ゲームの果てに今では「根本から順番に計算するということ」ぐらいの理解をされているように見受けられます。この記事では後者の解釈を採用します。
    また、「初心者への説明のための方便として、不適切であることを注意した上で DAG という語を用いる」のであれば妥当であり、これに対する批判をするものでもありません。多くの競プロerはグラフにとてもよく慣れているためです。
    加えて、DP では処理できない部分を行列計算などで潰して DP に帰着させるような「非 DAG の DP」についても扱いません。
    </small>
