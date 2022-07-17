---
title: YOLOv5モデルをONNXモデルにして使いたいけど後処理が面倒なとき
tags: ["YOLO", "YOLOv5", "ONNX", "ONNX Runtime", "Object Detection"] 
categories: ["機械学習"]
date: 2022-07-17
---

# 困ったこと
[YOLOv5](https://github.com/ultralytics/yolov5)は便利なライブラリですが、ONNXへモデルを変換したときにちょっと困ったことがあります。  
というのも、変換後のONNXモデルにはNMSなどの後処理が含まれていないため、後処理は別途用意する必要があります。  
公式ではPyTorchの関数を使ったNMSになっているため、そのまま後処理のコードをコピーしようとすれば実行環境上にONNX RuntimeとPyTorchの両方を用意しないといけません。でもせっかくONNXを使うなら、環境にPyTorchを入れたくないですよね。

# 解決方法
PyTorchを入れたくないけどどうしよう…と困っていたところ、こちらのプルリクを見つけました。  
https://github.com/ultralytics/yolov5/pull/7736  
どうやらNMSの処理がONNXモデルに含まれるような修正をおこなっているようです。

2022/07/17現在はまだmasterへはマージされていないのですが、[fork先のブランチ](https://github.com/triple-Mu/yolov5/tree/trtNMS)を試してみると、うまくいくことが確認できました。  
実際にONNXモデルへ変換をおこなうときにはexport.pyに"--nms"オプションをつければOKです。 
モデルの出力値の扱いは[こちら](https://github.com/triple-Mu/yolov5/blob/trtNMS/onnx_nms_ort.ipynb)を参考にすると分かるかと思います。
