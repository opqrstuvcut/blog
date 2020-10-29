---
title: Pythonのnamedtupleを使おう
tags:  ["Python", "namedtuple"]
categories:  ["Python"]
date: 2020-01-06 21:57:05 +0900
showtoc: true
---
Pythonのnamedtuple使ってますか？
案外使っていない方が多いので、ご紹介しておきます。

# namedtupleとは？
通常のタプルはインデックス指定でのみ要素を参照します。一方で、NamedTupleはタプルの各要素を**名前**によって参照できます。  
例えばpというnamedtupleの要素にnameというものがあれば、次のようにして参照できます。
```
name = p.name
```
他の部分はほとんど通常のタプルと同じと思って問題ありません。

# namedtupleを使うメリット
要素に名前がつけられるようになっただけですが、私が思うメリットは以下の通りです。
1. タプルのようなインデックスの指定では参照する要素を誤る可能性が出てきますが、名前で指定することで誤りを防ぐことができます。
1. タプルの各要素の意味がはっきりするのでコードの可読性がよくなります。
1. タプルを生成する箇所が複数あった場合に、要素の順番を誤ったり要素数を誤ったりすることがなくなります。

他にもいいところがあるかもしれませんね。

# namedtupleの使い方
## その1
使い方はそれほど難しくありません。以下のようにしてnamedtupleを定義できます。
```python
from collections import namedtuple
Person = namedtuple("Person", ["name", "age", "sex"])
```
上記により、Personのタプルが宣言できました。Personはnamedtupleの第二引数に指定されたnameとageとsexを要素にもつタプルです。ちなみに以下のようにリストではなく、スペース区切りの文字列で与えても同じ意味となります。
```python
Person = namedtuple("Person", "name age sex")
```
宣言したPersonというタプルを生成するには以下のようにします。
```python
p = Person("太郎", 10, "男")
```
このpの要素の参照は以下のようにしてできます。
```python
print(p.name, p.age, p.sex) # output: 太郎 10 男
```
簡単です！
## その2
（おそらく）Python3.6からは次のようにもnamedtupleが利用できます。
```python
from typing import NamedTuple

class Person(NamedTuple):
    name: str
    age: int
    sex: str

p = Person("太郎", 10, "男")
print(p.name, p.age, p.sex) # output: 太郎 10 男
```
個人的にはこの書き方のほうがぱっと見たときにわかりやすいような気がして好きです。
あと多分補完もこちらのほうが効きやすいのではと思っています。

# まとめ
コードの可読性があがり、間違いも減るので、namedtupleの利用をオススメします！
