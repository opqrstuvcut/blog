---
title: 動画の書き込み
tags:  ["OpenCV", "動画"]
categories:  ["OpenCV", "画像処理"]
date: 2020-07-28 11:00:00 +0900
---
本記事はQrunchからの転載です。
___

# OpenCVでの動画の書き込み方

次のようにしてtest.mp4という名前の動画を作成します。

``` Python
fourcc = cv2.VideoWriter_fourcc("m", "p", "4", "v")
writer = cv2.VideoWriter("test.mp4",
                         fourcc,
                         30,
                         (1920, 1080))
print(writer.isOpened())
```

第二引数のfourccは動画のコーデックをあらわしており、mp4のときにはcv2.VideoWriter_fourccの引数には"m", "p", "4", "v"を指定します。他にもmpgで保存するときには"D", "I", "V", "X"を指定したりできます。拡張子に対応してどういうコーデックが指定できるかは、ググっていただくのが良いかと思います。
また、第三引数にFPSを第四引数に動画の横と縦の大きさを指定しています。
isOpenedメソッドにより動画を書き込むための準備ができているかを確認できます。FalseのときにはPCがコーデックに対応していなかったりで上手くいっていません。

実際に書き込みをおこなうときはwriteメソッドを使います。以下では3フレーム分書き込んでいます。

``` Python
writer.write(frame)
writer.write(frame) 
writer.write(frame)  
writer.release() # 書き込み後はreleaseする
```
