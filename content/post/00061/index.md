---
title: m3u8ファイルとtsファイルのdownload
tags: ["Python", "m3u8"]
categories: ["Python"]
date: 2021-01-31
---

ライブストリーミングや動画の配信するためにm3u8とtsファイルを利用するケースがあります。
tsファイルは細切れになった小さい動画になっており、m3u8ファイルはそれらの情報をもっているプレイリストになります。

m3u8ファイルが手元にあるorm3u8のurlを知っている場合には、Pythonのライブラリの[**m3u8**](https://github.com/globocom/m3u8)を使えば簡単にtsファイルをdownloadできます。

## m3u8のInstall
インストールは簡単で、pipを使うだけです。
```bash
pip install m3u8
```

## tsファイルのdownload
tsファイルのダウンロードはm3u8とurllibを組み合わせておこなえます。
```Python
import urllib.request
import m3u8

playlist = m3u8.load(m3u8のパス)

for i, segment in enumerate(playlist.segments):
    # tsファイルのパス
    uri = segment.absolute_uri
    urllib.request.urlretrieve(uri, f"{i}.ts")
```
