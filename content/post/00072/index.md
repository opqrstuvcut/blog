---
title: スピアマンの順位相関係数の導出
tags: ["統計", "スピアマンの順位相関係数"] 
categories: ["統計"]
date: 2022-07-18
---

スピアマンの順位相関係数の導出のメモになります。

# 導出
$n$個のデータに対する2種類の値をそれぞれ$x_1,\cdots,x_n$と$y_1,\cdots,y_n$とします。  
そして、それらを何らかの方法で並べたときの順位をあらわす関数を$x_i$に対しては$R: \mathbb{R} \rightarrow \mathbb{N}$、$y_i$に対しては$S: \mathbb{R} \rightarrow \mathbb{N}$と定義します。なお、もしも同じ数が与えられたときは、適当に異なる順位をつけるとしておきます。$R$と$S$は順位をあらわす自然数に写す関数であるため全射です。  
また$R(x_1),\cdots,R(x_n)$の平均を$\bar{R}$、標準偏差を$\sigma_R$、$S(y_1),\cdots,S(y_n)$の平均を$\bar{S}$、標準偏差を$\sigma_S$とします。

いまやりたいことは$x_1,\cdots, x_n$と$y_1,\cdots, y_n$に対するスピアマンの順位相関係数を求めることです。
スピアマンの順位相関係数$r$は$R(x_1),\cdots, R(x_n)$と$S(y_1),\cdots, S(y_n)$に対するピアソンの相関係数になりますので、次のようにあらわされます。

$$
\begin{align*}
r &= \frac{\frac{1}{n}\sum_{i=1}^n (R(x_i) - \bar{R})(S(y_i) - \bar{S}) }{\sigma_R \sigma_S}.
\end{align*}
$$

上式は次のように整理できます。
$$
\begin{align*}
r &= \frac{\frac{1}{n}\sum_{i=1}^n (R(x_i)S(y_i) -\bar{S}R(x_i) -\bar{R}S(y_i) + \bar{R}\bar{S}) }{\sigma_R \sigma_S} \\\
&= \frac{\frac{1}{n}(\sum_{i=1}^n R(x_i)S(y_i)) -\bar{S}\bar{R} -\bar{R}\bar{S} + \bar{R}\bar{S} }{\sigma_R \sigma_S} \\\
&= \frac{\frac{1}{n}(\sum_{i=1}^n R(x_i)S(y_i)) -\bar{R}\bar{S}  }{\sigma_R \sigma_S}.
\end{align*}
$$

ここで、$d_i= R(x_i) - S(y_i)$とおくと、

$$
\begin{align*}
r &= \frac{\frac{1}{n}\left\\{\sum_{i=1}^n \frac{1}{2}(R(x_i)^2 -2 R(x_i)S(x_i) + S(y_i)^2 - d_i^2) + R(x_i)S(y_i)\right\\} -\bar{R}\bar{S}  }{\sigma_R \sigma_S}  \\\
&= \frac{\frac{1}{n}\left\\{\sum_{i=1}^n \frac{1}{2}(R(x_i)^2 + S(y_i)^2 - d_i^2)\right\\} -\bar{R}\bar{S}  }{\sigma_R \sigma_S} 
\end{align*}
$$
となります。

$R$と$S$の定義から、$\bar{R}=\bar{S}$と$\sigma_R = \sigma_S$が成り立つので、上式は以下のように変形できます。
$$
\begin{align*}
r &= \frac{\frac{1}{n}\left\\{\sum_{i=1}^n R(x_i)^2 - \frac{1}{2}d_i^2 \right\\} -\bar{R}^2  }{\sigma_R^2}  \\\
&= \frac{\frac{1}{n}\sum_{i=1}^n R(x_i)^2  -\bar{R}^2 - \frac{1}{2n} \sum_{i=1}^n d_i^2  }{\sigma_R^2}  \\\
&= \frac{\sigma_R^2 - \frac{1}{2n} \sum_{i=1}^n d_i^2  }{\sigma_R^2}  \\\
&= 1 - \frac{\sum_{i=1}^n d_i^2  }{2n\sigma_R^2}.
\end{align*}
$$

もう少し式を整理することができます。$\sigma_R^2$に着目するとこの部分は
$$
\begin{align*}
\sigma_R^2 &= \mathbb{E}[R(x)^2] - \bar{R}^2 \\\
&= \sum_{i=1}^n i^2 - \left(\sum_{i=1}^n i\right)^2 \\\
&= \frac{n^2-1}{12}
\end{align*}
$$
とわかります。このことから、次のスピアマンの順位相関係数が導かれます。
$$
\begin{align*}
r &= 1 - \frac{6\sum_{i=1}^n d_i^2  }{n^3 - n}.
\end{align*}
$$
