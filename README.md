hs2lazy -- Haskell to Lazy-K compiler
=====================================

## なにこれ

Haskell っぽい言語から [Lazy K](http://homepages.cwi.nl/~tromp/cl/lazy-k.html) へのコンパイラです。

## コンパイル

GHC が必要です。以下のコマンドでビルドできます。

    ghc -o hs2lazy --make Main.hs

## 使い方

    hs2lazy [source files]

ソースファイルは複数指定でき、連結した一つのファイルのように読み込まれます。コンパイル結果 (Lazy K のコード) は標準出力に出力します。
Prelude は自動ではロードされませんので、

    hs2lazy examples/Prelude.hs hogehoge.hs >hogehoge.lazy

のように最初に付属の examples/Prelude.hs を読み込ませると良いでしょう。

## プログラムの書き方

Lazy K では、プログラムは標準入力からの入力を数値の無限リスト (0-255 が文字を表し、256 以上が EOF を表す) として受け取ります。そして、プログラムを評価した結果を、同じ形式の数値の無限リストとみなして、標準出力に出力します。

hs2lazy のソース言語でも、同じような方式でプログラムを書きます。
Haskell では main の型は `IO ()` ですが、hs2lazy では

    main :: Stream -> Stream

となります。Stream 型は付属の Prelude.hs で

    data Stream = Cons Char Stream

と定義されています。入力は以下のような文字の無限ストリームになります。

    Cons 'h' $ Cons 'o' $ Cons 'g' $ Cons 'e' $ Cons eof $ Cons eof ...

`eof` は `(chr 256)` と定義されています。

ストリームと文字のリストとの相互変換を行う関数、`fromStream` と `toStream` を Prelude.hs に用意しています。例えば入力を文字単位で逆転して表示するプログラムは、以下のように書けます。

    main = toStream . reverse . fromStream

実は main は `Stream -> Stream` じゃなくてもコンパイルできてしまいます。気をつけましょう。

## ライセンス

型推論のコードは [Typing Haskell in Haskell](http://web.cecs.pdx.edu/~mpj/thih/) のコードをベースにしています。ライセンスは Lisence.thih を参照してください。
その他の部分はご自由にどうぞ。