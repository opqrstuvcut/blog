---
title: 'GPUサーバーでのTensorFlow + uWSGIでFailed to get device properties, error code: 3'
tags:  ["TensorFlow", "uWSGI"]
categories:  ["uWSGI"]
date: 2020-11-08
showtoc: true
---

GPUサーバー上でTensorFlowを動かすアプリを作成し、nginxとの間にはuWSGIを挟む構成にしていたところ、次のエラーが出てしまいました。
```
Failed to get device properties, error code: 3
```
他の記事の引用になってしまいますが、エラーメッセージ自体をググっても解決できなかったので、メモ程度に載せておきます。

# 原因
確かとは断言できないのですが、次の記事にかかれていることが怪しいと推測しました。
[https://keng000.hatenablog.com/entry/2020/05/05/092425](https://keng000.hatenablog.com/entry/2020/05/05/092425)

つまり、マルチスレッドでのモデルの読み込み方が良くないのかと。
# 対処
記事に書かれている通り、uWSGIのiniファイルで次のように追記しました。
```
[uwsgi]
lazy-apps = true
```

とりあえずこれで解決しました。
