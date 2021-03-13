---
title: 画像認識モデルの性能をあげるためのTips
tags: ["Deep Learning", "深層学習", "画像", "EfficientNet", "TensorFlow", "PyTorch"]
categories: ["Deep Learning", "深層学習"]
date: 2021-03-13
---

画像分類モデルを作っているときに予測精度をあげるのに役に立ったなぁという方法の一覧のメモです。
簡単にできるものから順に紹介しているつもりです。

# ConvolutionとFC層との橋渡しにはGlobal Averaging Poolingを使う
ネットにある転移学習の例をコピペすると、畳み込み層の出力を1次元にするところでFlattenを使ってたりします。
でも実はGlobal Averaging Poolingを使ったほうが精度が良くなるかもしれません。  
精度を改善するのもそうですが、モデルのパラメーターを大きく減らせることも非常に大きい恩恵だったりもします。

# EfficientNetはNoisy Student版を使う
転移学習に素のEfficientNetを利用している方は多いと思いますが、Noisy Stundent版の重みを用いて転移学習することでさらに性能があがるかもしれません。

TensorFlowであれば、こちらのレポジトリが利用可能です。
[https://github.com/qubvel/efficientnet](https://github.com/qubvel/efficientnet)

# Label Smoothingを使う
1か0かのハードラベルではなく、ソフトラベルを使って過学習を抑える方法です。
TensorFlowだと、簡単に使えます。
```Python
tf.keras.losses.categorical_crossentropy(y, y_hat, 
                                         label_smoothing=0.1)
```

# Learning Rate Schedulerを使う
学習率のスケジューラーを利用してみると、精度が良くなるかもしれません。  
例えば、lossが下がりきったタイミングで学習率を0.1倍にしてみると、lossが少し落ちたりします。  
性能が変わらないことも多いので、あまり期待しないほうが良いかも。

# RandAugmentを使う
RandAugmentは各画像に対して、ランダムにAugmentをいくつか利用する方法です。
RandAugmentのパラメーターはいくつのAugmentを利用するかと各Agumentでのパラメーターを制御するための値の2つのみになります。そのため、Augmentに関するパラメーターのグリッドサーチも一応可能です。  
"パラメーターがたったの2つでいいの！？"と普通の人は効果を疑うかと思いますが、実際に試してみるとかなりうまくいきました。

使うときには、imgaugなどのライブラリを利用すると良いかと思います。次のようにしてRandAugmentを利用可能です。
```Python
import imgaug.augmenters as iaa
images = iaa.RandAugment(n=2, m=30)(images=images)
```

[論文](https://arxiv.org/abs/1909.13719)によると、AutoAugmentよりも性能が良いという結果が出ています。


PyTorch用の実装もネットで適当に探せばすぐに見つかります。

# GridMaskを使う
GridMaskはAugmentの一つで、Cutoutと同じような種類のAugmentになります。
Cutoutと違い、Grid状のMaskをかける方法になります。  
問題によっては結構性能があがりました。  
実装はググると色々見つかります。
