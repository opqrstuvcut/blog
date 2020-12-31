---
title: Matplotlibの凡例を外側に表示したい人へ
tags:  ["matplotlib", "Python"]
categories:  ["Python"]
showtoc: true
date: 2020-01-20T21:09:01+09:00
draft: false 
image: bc011ef843d7d97092a83521e65e6a15.png
---

本記事はQrunchからの転載です。
___

Matplotlibの凡例を外側に出したい人用に色々な例を書いておきます。

次のような凡例の位置をいじらずに表示した状態からいじっていきます。

```python
data = np.random.rand(10, 3)
labels = ["a", "b", "c"]

plt.plot(range(10), data, marker="o", linewidth=3)
plt.legend(labels)
plt.title("title")
plt.ylabel("y label")
plt.xlabel("x label")
plt.show()
```
![](1f998257aafd4af45a99802b5d2738ca.png)

### 右上に表示
凡例の枠の上部をグラフの枠の上部にあわせて、右上に表示するときは以下のようにします。
```python
plt.legend(labels, loc='upper left', bbox_to_anchor=(1, 1))
```
 ![](046032640d8b955c7fc8ea14004661cd.png)

### 右中央に表示
凡例の上下の位置をグラフと揃えて、右に表示するときは以下のようにします。
```python
plt.legend(labels, loc='center left', bbox_to_anchor=(1., .5))
```
![](b315ee00b7f3b400118ce55c857f86db.png)

### 上に表示
凡例の左右の位置をグラフと揃えて、上に表示するときは以下のようにします。
ncol=3とすることで横一列に3つ分のグラフの凡例を表示できます。
```python
plt.legend(labels, loc='lower center', bbox_to_anchor=(.5, 1.1), ncol=3)
```
![](5df64ab531c69bb196c09ded511a8b9e.png)

### 下に表示
凡例の左右の位置をグラフと揃えて、下に表示するときは以下のようにします。
```python
plt.legend(labels, loc='upper center', bbox_to_anchor=(.5, -.15), ncol=3)
```
![](b76f876a6c79eddc7ad208bc878baa88.png)

### 理屈
plt.legendの引数の**loc**に指定した凡例の箇所が**bbox_to_anchor**で指定した座標になるように位置が調整されます。ここで、座標はグラフの枠の左下が(0,0)で右上が(1,1)となります。
![](bc011ef843d7d97092a83521e65e6a15.png)

<u>例1</u>
 loc='upper left'、bbox_to_anchor=(1, 1)であるときには、凡例の枠の**左上**（locがupper leftなので）が(1,1)になるように凡例が配置されます。

<u>例2</u>
 loc='lower center'、bbox_to_anchor=(0.5, 1.1)であるときには、凡例の枠の**中央下**（locがlower centerなので）が(0.5,1.1)になるように凡例が配置されます。


