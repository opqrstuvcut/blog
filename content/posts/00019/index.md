---
title: AWSのLambdaからPostgresを利用
tags:  ["AWS", "PostgreSQL", "Python", "Lambda"]
categories:  ["AWS"]
date: 2020-05-04 13:35:16 +0900
showtoc: true
---
AWSのLambda（Python）からPostgresを利用するためのライブラリの使い方のメモです。何もトラブルなく使えましたが、一応。
ライブラリのレポジトリは[こちら](https://github.com/jkehler/awslambda-psycopg2)です。

1. ライブラリのclone
```
git clone https://github.com/jkehler/awslambda-psycopg2.git
```

2. 適切な名前にリネーム
LambdaでPython3.6を利用する場合にはcloneしてきたレポジトリにある**psycopg2-3.6**を**psycopg2**にリネームします。あるいはPython3.7を利用する方は**psycopg2-3.7**を**psycopg2**にリネームします。

3. 適切な位置への配置  
psycopg2をLambdaにデプロイするコードと同じディレクトリに配置します。
例： lambda/hoge.pyというPythonスクリプトをデプロイする場合にはlambdaディレクトリ以下にpsycopg2を配置する。

4. Lambdaにデプロイする！
