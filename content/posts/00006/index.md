---
title: 関数が上に凸であることの必要十分条件はヘッセ行列が半負定値の証明
tags:  ["数学", "ヘッセ行列", "負定値", "hessian", "凸"]
categories:  ["数学"]
date: 2020-03-11T00:08:01+09:00
showtoc: true
---

本記事はQrunchからの転載です。
___

関数が上に凸であることの必要十分条件はヘッセ行列が半負定値であることです。ネット上だと日本語でまとまっている文献があんまりないかもと思ったので、今回はこの証明をまとめます。
なお、関数が下に凸のときにはヘッセ行列は半正定値となります。上に凸の定義を使っているところを下に凸の定義に置き換え、正定値を負定値に置き換えれば、同じ議論が可能です。
また出てくる関数$f$は暗黙的に定義域で2階微分可能としています。

# 定義
## 関数が上に凸の定義
関数$f:\mathbb{R}^{n} \rightarrow \mathbb{R}$が上に凸とは任意の元$\mathbf{x}^{(1)}, \mathbf{x}^{(2)} \in \mathbb{R}^{n}$と任意の$t \in [0,1]$に対して以下が成り立つことを指します。
$$ f(t\mathbf{x}^{(2)} + (1 -t)\mathbf{x}^{(1)}) \geq tf(\mathbf{x}^{(2)}) + (1 -t) f(\mathbf{x}^{(1)}).$$

## ヘッセ行列の定義
関数$f:\mathbb{R}^{n} \rightarrow \mathbb{R}$のヘッセ行列$H$を以下のように定義します。
$$H_f = \nabla^2 f = \begin{pmatrix} \frac{\partial^2 f}{\partial x_1^2}   &\frac{\partial^2 f}{\partial x_1\partial x_2} & \dots & \frac{\partial^2 f}{\partial x_1\partial x_n} \cr
\frac{\partial^2 f}{\partial x_2\partial x_1} & \frac{\partial^2 f}{\partial x_2^2}   & \dots & \frac{\partial^2 f}{\partial x_2 \partial x_n} \cr
\vdots &  \vdots & \ddots  & \vdots \cr
\frac{\partial^2 f}{\partial x_n\partial x_1} & \frac{\partial^2 f}{\partial x_n \partial x_2}   & \dots & \frac{\partial^2 f}{ \partial x_n^2}
\end{pmatrix}.$$

## 行列の半負定値性の定義
ある対称行列$A \in \mathbb{R}^{n \times n}$に対して任意のベクトル$\mathbf{x} \in \mathbb{R}^n$が次を満たすとき、$A$を半負定値といいます。
$$ \mathbf{x}^T A \mathbf{x} \leq 0.$$

# 証明    
本題の証明に使うため、以下を先に証明しておきます。
## 凸関数の一次条件
<div style="padding: 10px; margin-bottom: 10px; border: 1px dashed #333333;">
関数$f:\mathbb{R}^{n} \rightarrow \mathbb{R}$が上に凸であるとき、任意の元$\mathbf{x}^{(1)}, \mathbf{x}^{(2)} \in \mathbb{R}^{n}$に対して以下が成り立ちます。
$$ f(\mathbf{x}^{(2)}) - f(\mathbf{x}^{(1)})\leq \nabla f(\mathbf{x}^{(1)})^T(\mathbf{x}^{(2)}- \mathbf{x}^{(1)}).$$
また、この逆も成り立ちます。</div>

<u>証明</u>
$(\Rightarrow)$
 上に凸であることの定義から、
$$ \begin{aligned} f(t\mathbf{x}^{(2)} + (1 -t)\mathbf{x}^{(1)}) \geq & tf(\mathbf{x}^{(2)}) + (1 -t) f(\mathbf{x}^{(1)}) \cr
f(\mathbf{x}^{(1)} + t(\mathbf{x}^{(2)} - \mathbf{x}^{(1)})) \geq  & f(\mathbf{x}^{(1)}) + t(f(\mathbf{x}^{(2)}) - f(\mathbf{x}^{(1)}))
 \cr
\frac{f(\mathbf{x}^{(1)} + t(\mathbf{x}^{(2)} - \mathbf{x}^{(1)})) - f(\mathbf{x}^{(1)}) }{t}
\geq & f(\mathbf{x}^{(2)}) - f(\mathbf{x}^{(1)})
\end{aligned} $$
となります。 ここで、$g(t) = f(\mathbf{x}^{(1)} + t(\mathbf{x}^{(2)} - \mathbf{x}^{(1)}))$とおくと、以下が成り立ちます。
$$
\begin{aligned}
\frac{g(t) - g(0)}{t}
\geq & f(\mathbf{x}^{(2)}) - f(\mathbf{x}^{(1)})
\end{aligned}.
$$
さらに$t \rightarrow 0$とすれば、以下のようになります。
$$
\begin{aligned}
g'(0)
\geq & f(\mathbf{x}^{(2)}) - f(\mathbf{x}^{(1)})
\end{aligned}.
$$
$g'(0)$がなんであるかというと、これは単純に計算すればよく、
$$g'(0) = \left.\frac{{\rm d}g}{{\rm d}t}\right|_{t=0} = \nabla f(\mathbf{x}^{(1)})^
T(\mathbf{x}^{(2)} - \mathbf{x}^{(1)})$$
となります。
以上から、
$$ f(\mathbf{x}^{(2)}) - f(\mathbf{x}^{(1)})\leq \nabla f(\mathbf{x}^{(1)})^T(\mathbf{x}^{(2)}- \mathbf{x}^{(1)})$$
が示されました。

$(\Leftarrow)$
適当な$0\leq t \leq 1$を用いて$\mathbf{z}=t\mathbf{x}^{(2)} + (1-t)\mathbf{x}^{(1)} $とおきます。仮定から以下が成り立ちます。
$$ \begin{aligned} f(\mathbf{x}^{(2)}) \leq &  f(\mathbf{z}) +  \nabla f(\mathbf{z})^T(\mathbf{x}^{(2)}- \mathbf{z}), \cr
f(\mathbf{x}^{(1)}) \leq &  f(\mathbf{z}) + \nabla f(\mathbf{z})^T(\mathbf{x}^{(1)}- \mathbf{z}).
\end{aligned}
$$
1つめの式に$t$を掛け、2つめの式に$1-t$を掛けて足し合わせることで以下のようになります。
$$ \begin{aligned}
tf(\mathbf{x}^{(2)}) + (1 -t )f(\mathbf{x}^{(1)}) \leq & tf(\mathbf{z})  + (1-t)f(\mathbf{z}) +  t\nabla f(\mathbf{z})^T(\mathbf{x}^{(2)}- \mathbf{z}) + (1-t) \nabla f(\mathbf{z})^T(\mathbf{x}^{(1)}- \mathbf{z}) \cr
= &  f(\mathbf{z}) +  \nabla f(\mathbf{z})^T(t(\mathbf{x}^{(2)}-\mathbf{z}) + (1-t) (\mathbf{x}^{(1)}- \mathbf{z})) \cr
= &  f(\mathbf{z}) +  \nabla f(\mathbf{z})^T(t\mathbf{x}^{(2)} + (1-t) \mathbf{x}^{(1)}-\mathbf{z}) \cr
= &  f(\mathbf{z})  \cr
= &  f(t\mathbf{x}^{(2)} + (1-t)\mathbf{x}^{(1)} ) . \cr
\end{aligned}
$$
以上から逆も証明できました。

この証明の参考：
http://mathgotchas.blogspot.com/2011/10/proof-for-first-order-condition-of.html

## テイラーの定理
本題の証明に用いるため、テイラーの定理の特別な場合を紹介しておきます。
テイラーの定理の証明はここではしません。また実際は成り立つための条件がありますが、ここでは以下のように利用できるとします。
<div style="padding: 10px; margin-bottom: 10px; border: 1px dashed #333333;">
$f(\mathbf{x} + \mathbf{y})$を以下のようにあらわせます。
$$ f(\mathbf{x} + \mathbf{y})  = f(\mathbf{x}) + \nabla f(\mathbf{x})^T  \mathbf{y} +  \frac{1}{2} \mathbf{y}^T  H_f(\mathbf{x}) \mathbf{y} + o(||\mathbf{y}||_2^2). $$
ここで$o(x^n)$は
$$
\lim_{x\rightarrow 0} \frac{o(x^n)}{x^n} = 0
$$
となります。
</div>
あるいは次の形式であらわすこともできます。
<div style="padding: 10px; margin-bottom: 10px; border: 1px dashed #333333;">
ある$0 < t < 1$を用いて$f(\mathbf{x} + \mathbf{y})$を以下のようにあらわせます。
$$ \begin{aligned} f(\mathbf{x} + \mathbf{y})  = &f(\mathbf{x}) + \nabla f(\mathbf{x})^T\mathbf{y}  +  \frac{1}{2} \mathbf{y}^T H_f(\mathbf{x} + t\mathbf{y}) \mathbf{y}. \end{aligned}$$
</div>

## 関数が上に凸であることの必要十分条件がヘッセ行列が半負定値であることの証明
本題の証明です。
<div style="padding: 10px; margin-bottom: 10px; border: 1px dashed #333333;">
関数$f:\mathbb{R}^{n} \rightarrow \mathbb{R}$が上に凸であることの必要十分条件は任意の$\mathbf{x}$においてヘッセ行列$H_f(\mathbf{x})$が半負定値であることです。</div>

<u>証明</u>
$(\Rightarrow)$
適当な元$\mathbf{x}, \mathbf{y} \in \mathbb{R}^{n}$を考えます。テイラーの定理より以下が成り立ちます。
$$ \begin{aligned} f(\mathbf{x} + \mathbf{y})  = &f(\mathbf{x}) + \nabla f(\mathbf{x})^T\mathbf{y}  + \frac{1}{2} \mathbf{y}^T H_f(\mathbf{x}) \mathbf{y} + o(||\mathbf{y}||^2_2).\tag{1} \end{aligned}$$
また、$f$が上に凸であるという仮定から、一次条件より
$$ f(\mathbf{x} + \mathbf{y})  \leq f(\mathbf{x}) + \nabla f(\mathbf{x})^T\mathbf{y}\tag{2}$$
が成り立ちます。式(1)と式(2)から、
$$ \frac{1}{2} \mathbf{y}^T H_f(\mathbf{x})\mathbf{y} +  o(||\mathbf{y}||^2_2) \leq 0. $$
$\mathbf{y} = \alpha \mathbf{z} , ||\mathbf{z}||_2 = 1 $とすると、

$$
\begin{aligned}
\frac{1}{2} \alpha^2 \mathbf{z}^T H_f(\mathbf{x})\mathbf{z} + o(\alpha^2)  \leq & 0 \cr
\frac{1}{2}  \mathbf{z}^T H_f(\mathbf{x})\mathbf{z} + \frac{o(\alpha^2)}{\alpha^2} \leq & 0 .
\end{aligned}
$$
このとき、$\alpha \rightarrow 0$とすると、$o(x^n)$の定義から以下が成り立ちます。
$$
\frac{1}{2}  \mathbf{z}^T H_f(\mathbf{x}) \mathbf{z} \leq 0.
$$
$\mathbf{y}$は任意のベクトルでしたので、$H_f(\mathbf{x})$は半負定値となります。
よって$f$が上に凸であるとき、ヘッセ行列$H_f(\mathbf{x})$は任意の$\mathbf{x}$で半負定値であることが示されました。

$(\Leftarrow)$
テイラーの定理より、ある$0 < t < 1$を用いて以下が成り立ちます。
$$ \begin{aligned} f(\mathbf{x} + \mathbf{y})  = &f(\mathbf{x}) + \nabla f(\mathbf{x})^T\mathbf{y} +  \frac{1}{2} \mathbf{y}^T H_f(\mathbf{x} + t\mathbf{y} ) \mathbf{y}.  \end{aligned}$$
ヘッセ行列$H_f(\mathbf{x} + t\mathbf{y} ) $が半負定値であるため、明らかに
$$ f(\mathbf{x} + \mathbf{y})  \leq f(\mathbf{x}) + \nabla f(\mathbf{x})^T\mathbf{y} $$
が成り立ちます。これは先程示した一次条件であらわれる式と同じです。
以上から、ヘッセ行列$H_f(\mathbf{x})$が任意の$\mathbf{x}$で半負定値であるとき、$f$が上に凸であることが示されました。

