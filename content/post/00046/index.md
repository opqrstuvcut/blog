---
title: 動画の読みこみ
tags:  ["OpenCV", "動画", "読み込み"]
categories:  ["OpenCV", "画像処理"]
date: 2020-07-27 22:53:54 +0900
---
本記事はQrunchからの転載です。
___

今日はOpenCVでの動画の読み書きを扱います。

# 動画の読み込み

動画の読み込みは簡単です。

最初に次のように保存されている動画を開きます。

```Python
import cv2
v = cv2.VideoCapture("./ex.mp4")
```

カメラからフレームを取得する場合はデバイスのIDの指定すればよいです。
普通は次のように0を指定すればよいかと思います。

``` Python
v = cv2.VideoCapture(0)
```

フレームの読み込みは以下のようにします。

``` Python
ret, frame = v.read()
```

フレームが読み込めれば、retにはTrueが入ってきて、フレームが読み込めない状態になるとFalseが入ります。
これを利用すれば、次のようにしてフレームを次々に読み込めます（保存されているファイルを開いている場合には、動画の終わりまでが読み込まれます）。

``` Python
while True:
    ret, frame = v.read()
    if not ret:
        break
```

# プロパティの取得

動画のフレームの大きさ、FPS、フレーム数は以下のようにして取得できます。

``` Python
print(v.get(cv2.CAP_PROP_FRAME_WIDTH))
print(v.get(cv2.CAP_PROP_FRAME_HEIGHT)) 
print(v.get(cv2.CAP_PROP_FPS))  
print(v.get(cv2.CAP_PROP_FRAME_COUNT))  
```

取得できるプロパティは以下に一覧があります。
http://opencv.jp/opencv-2svn/cpp/highgui_reading_and_writing_images_and_video.html
