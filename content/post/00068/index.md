---
title: NVIDIAのGPUのdriverの更新
tags: ["GPU", "NVIDIA"]
categories: ["GPU"]
date: 2021-12-11
---
# NVIDIAのGPUのdriver更新手順
色々手順はあると思いますが、1つのやり方のメモです。

1. 古いドライバを削除しておく
    * 公式からCUDA Toolkitをダウンロードしてインストールした場合は次で削除できるはず。
        ```
        $ cd /usr/local/cuda-x/bin 
        $ sudo cuda-uninstaller
        $ sudo nvidia-uninstall
        ```
    * もしapt-getを使って古いドライバを入れていたら次のコマンドで消え去るはず。nvidia containerが入っている場合はそれも消えるので、嫌な人は注意。
        ```
        sudo apt-get remove --purge nvidia\* libnvidia-\*
        ```
1. CUDA Toolkitのダウンロードとインストール（Installer Typeはrunfileが一番ラク）
    * CUDA 11.2ならここの手順に従うhttps://developer.nvidia.com/cuda-11.2.1-download-archive
    * 最新版のCUDAはここの手順に従うhttps://developer.nvidia.com/cuda-downloads
1. nvidia-smiコマンドを実行して動けばOK
