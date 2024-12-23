---
title: RT-DERT v2のファインチューニング
tags: ["RT-DERT", "物体認識"] 
categories: ["機械学習"] 
date: 2024-12-23
image: feature.png
---
RT-DERT v2のファインチューニングをおこなったのでメモ。  
レポジトリは https://github.com/lyuwenyu/RT-DETR を参照してください。 また、本記事ではPyTorch版を前提としています。


## 手順
PyTorch版の[README](https://github.com/lyuwenyu/RT-DETR/tree/main/rtdetrv2_pytorch)を見てみるとそれほど記載がないですが、結構簡単にファインチューニングが可能になっています。  
おおまかに以下の手順になります。

1. 学習済みモデルのダウンロード
1. COCO形式のデータセットを準備
1. configを用意
1. 学習実行

### 学習済みモデルのダウンロード
[README](https://github.com/lyuwenyu/RT-DETR/tree/main/rtdetrv2_pytorch)の中から目的のサイズのモデルを選んでダウンロードさせていただきましょう。

### COCO形式のデータセットを準備
データについてはCOCO形式のデータセットを準備すればOKです。実行していないですが、Pascal VOC形式でも大丈夫なはずです。  
また、もし自作のデータセットならば、COCO形式のデータセットを学習と検証用データへ分割をしてjsonとして保存しておくところまではやっておく必要があります。

### configを用意
学習時のデータの設定やパラメータなどはすべてyml形式のファイルで管理されています。
以下を設定すればOKです。

* データセットの設定
    * configs/dataset/coco_detection.ymlをもとに作成する。
    * COCOデータセットとラベルを同じにしたくないため、remap_mscoco_categoryはFalseにする必要がある。
    * train_dataloaderとval_dataloaderのimg_folderとann_fileに自分が用意したデータセットのパスを記載する。 img_folderに画像が格納されているディレクトリを指定できるため、jsonファイルに記載している画像ファイルのパスはimg_folderを起点にする形でOK（よくある形式のやつですね）
* dataloaderの設定
    * configs/rtdetrv2/include/dataloader.yml をもとに作成する。
    * transforms部分にaugmentationを記述するので、不要なものを削除したり、逆に必要なものは追加する。ちなみに、複数の画像を合成するタイプのaugmentationが設定されていないが、著者曰く[mosaic augmentationでは良い結果が得られなかったとのこと](https://github.com/lyuwenyu/RT-DETR/issues/174)。ただし、関数自体は用意されていて、データセットによってはやはり効果がでる。もしmosaic augmentationを試す場合はtransforms.opsのRandomHorizontalFlipのあとに以下を追加すれば動く。
        ```
            - {type: Mosaic, size: [640, 640]}
        ```
* 基本となる設定
    * configs/rtdetrv2/以下のymlをもとに作成する。Sサイズのモデルならばrtdetrv2_r18vd_120e_coco.ymlを用いる。
    * __include__に自分で作成したymlを指定する。
    * epochもここで変更する。

ここまでやれば、とりあえずは学習が動くようになります。  細かい設定はコードを見たり挙動を確認しながら修正しましょう。

### 学習実行
READMEには複数GPUのケースが記載されていますが、もし1GPUならば以下のようにすれば良いです。
```bash
CUDA_VISIBLE_DEVICES=0 torchrun tools/train.py -c ./configs/rtdetrv2/rtdetrv2_r18vd_120e_coco_ft.yml -t ./models/rtdetrv2_r18vd_120e_coco_rerun_48.1.pth --use-amp --seed=0 --summary-dir=output/rtdetrv2_r18vd_120e_coco/xxx

```

学習後は学習済みモデルを用いて推論の実行と結果の保存ができます。用意されているスクリプトだと画像を1枚ずつ処理する必要がありますが、一気に推論できたほうが何かと便利なので、[こちら](https://github.com/opqrstuvcut/RT-DETR/blob/main/rtdetrv2_pytorch/references/deploy/rtdetrv2_torch.py)を用いて以下のようにしています。
```
python -m references.deploy.rtdetrv2_torch -c ./configs/rtdetrv2/rtdetrv2_r18vd_120e_coco_ft.yml -r ./output/rtdetrv2_r18vd_120e_coco/best.pth --im-dir=./dataset/validation_images/ -d cuda
```

onnxへの変換はREADMEに書かれているとおりなのですが、以下で動きます。
```bash
python tools/export_onnx.py -c ./configs/rtdetrv2/rtdetrv2_r18vd_120e_coco_ft.yml  -r ./output/rtdetrv2_r18vd_120e_coco/last.pth  --check
```

onnxへ変換したモデルを用いて[ここ](https://github.com/lyuwenyu/RT-DETR/blob/main/rtdetrv2_pytorch/references/deploy/rtdetrv2_onnxruntime.py)のように推論が可能です。
これをみていると、NMSが不要になったおかげでonnxの出力をほぼそのまま使えばよいという形になっており、シンプルかつTorchを入れたくないような場合の本番システムの構築が楽ですね（このコードだとtorchvisionを少し使っていますが、使わない形に置き換え可能ですね）。
