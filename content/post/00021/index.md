---
title: docker-composeのbuildがはじまらないとき
tags:  ["docker-compose", "Docker"]
categories:  ["Docker"]
date: 2020-11-08
showtoc: true
---

最近では実験用の環境なんかもDockerコンテナ上に用意することも多いです。  
docker-composeも使うわけですが、たまにdocker-composeのbuildがいつになってもはじまらないことがあります。  
Building xxxがずっと表示されて、そこから進まないわけです。

以前も同じケースに出くわしたのにすぐ思い出せなかったので、備忘録的に残りしておきます（似た記事はネットにたくさんありますが）。

# docker-composeのbuildがはじまらない原因
<u>学習に使うデータのように重いファイルを置いてあるディレクトリでdocker-composeをおこなうのが原因です。</u>

なぜこれが原因でbuildがはじまらないかといえば、Docker Imageのbuildをするときに、Dockerfileがあるディレクトリ上のデータはすべてDockerのデーモンに渡されるためです。  
全部のファイルをデーモンに渡そうとするので、学習データなんかがDockerfileと同じディレクトリ上にあると、それらの重たいファイルも渡そうとしてしまい、いつになってもbuildが進まないわけですね。

# 対処方法
1. .dockerignoreにデーモンに渡してほしくないディレクトリ、ファイルを指定する
1. ファイルパスを工夫する（でもdockerignoreを使うのが一番いいんじゃないでしょうか）
