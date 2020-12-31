---
title: minMaxLocで最大と最小の位置を楽に取得
tags:  ["OpenCV", "minMaxLoc"]
categories: ["OpenCV", "画像処理"]
date: 2020-08-11 11:04:00 +0900
---
本記事はQrunchからの転載です。
___
行列の最大値、最小値はNumPyのmaxやmin、またそれらのインデックスはargmaxやargminを使えば取得できるのですが、OpenCVでは一発ですべて取得できます。


```Python
min_val, max_val, min_idx, max_idx = cv2.minMaxLoc(np.array([[1, 2, 3], 
                                                             [4, 5, 6]]))
print(min_val, max_val, min_idx, max_idx)
```

この出力は以下のとおりですが、それぞれ最小値、最大値、最小値の位置、最大値の位置をあらわします。位置は$(x,y)$をあらわしていますので、行列でいえば、（列、行）の順に格納されています。
```
(1.0, 6.0, (0, 0), (2, 1))
```

