---
title: FastAPI + uvicornの構成のサーバーで時間経過でメモリ使用量が増えるとき
tags: ["AWS", "ECS", "FastAPI", "uvicorn", "Python"]
categories: ["AWS"]
date: 2022-03-20
image: memory_usage_ng.png
---

# 問題発生時の状況
AWSのECS上にFastAPI + uvicornの構成でのサーバーをたてました。内容的には普通のREST APIです。  
とりあえずは順調に動作していたのですが、たまにコンテナが再起動しているっぽいけどなんだろうと思って調べていたところ、メモリ使用量が次のようになっていました。  
{{< img800x src="memory_usage_ng.png" alt="メモリ使用量" >}}

時間経過でメモリ使用量が勝手に増えているような振る舞いです。 実装上はこんなことにならないはず…。


# 解決方法
調べてみると、同じようなissueが存在していました。
* https://github.com/tiangolo/fastapi/issues/1624
* https://github.com/encode/uvicorn/issues/1226

uvicorn側のissueをみると、uvicornのバージョンが0.17.0でこのような問題が起こるようです。  
利用しているバージョンがちょうど0.17.0でしたので、0.17.6へとバージョンしてみると、見事に解決しました！

バージョンアップ後のメモリ使用量が以下のグラフの右端の平らな部分です。時間経過でメモリ使用量が増えるようなことがなくなっています。  
{{< img800x src="memory_usage_improved.png" alt="メモリ使用量" >}}
