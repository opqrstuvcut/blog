---
title: This version of ChromeDriver only supports Chrome version xxとなったとき
tags: ["ChromeDriver"]
categories: ["Chrome"]
date: 2021-01-31
---
Google Chromeのバージョンをあげたあと、[ImageDownloader](https://github.com/sczhengyabin/Image-Downloader)などを使っているときに次のようなエラーメッセージがでることがあります。
```
Message: session not created: This version of ChromeDriver only supports Chrome version 86
```

最後の86のところはChromeのバージョンによって変わります。

これはChromeDriverのバージョンとGoogle Chromeのバージョンが異なっていることによって起きるエラーです。
そのため、ChromeDriverのバージョンをあげればよいです。

例えば、Homebrewを使ってChromeDriverを入れている場合には次を実行します。
```
$ brew upgrade chromedriver
```
