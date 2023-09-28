---
title: GPU周りがおかしくなったときのメモ（ubuntu）
tags: ["GPU", "CUDA", "NVIDIA"] 
categories: ["GPU"] 
date: 2023-07-06
---

いまだにちょくちょくGPU周りの設定がおかしくなることがあるのでメモ。たまに問題が起きる毎にメモが追加されます。

# ①nvidia-smiが遅い、ubuntuが起動しているのにGUIが何も表示されない
## 現象
* 何も画面に映らなくなる現象です。sshとかはいけます。
* nvidia-smiを実行すると普通は一瞬でGPUの状況が表示されますが、このケースでは1分以上かかったりします。

## 解決方法
cudaを入れ直して再起動したら直りました。原因はよくわかりません。  
aptでcudaを入れている場合は次のような感じです。
```
$ sudo apt remove cuda -y
$ sudo apt install cuda -y
$ sudo reboot now
```

# ②突然のNVIDIA-SMI has failed because it couldn't communicate with the NVIDIA driver. Make sure that the latest NVIDIA driver is installed and running.
## 現象
* PCを起動してnvidia-smiを実行すると、「NVIDIA-SMI has failed because it couldn't communicate with the NVIDIA driver. Make sure that the latest NVIDIA driver is installed and running.」が表示されます。
* もちろんDockerコンテナからのGPU利用もできません。

## 解決方法
試しにnvccを実行しようとしたところ、nvccが見つからないのでインストール。
```
$ sudo apt install nvidia-cuda-toolkit
 libnvidia-gl-535 : 破壊: libnvidia-gl-535:i386 (!= 535.86.10-0ubuntu1) しかし、535.104.05-0ubuntu0.22.04.4 はインストールされようとしています
 libnvidia-gl-535:i386 : 破壊: libnvidia-gl-535 (!= 535.104.05-0ubuntu0.22.04.4) しかし、535.86.10-0ubuntu1 はインストールされようとしています
 nvidia-cuda-toolkit : 依存: nvidia-profiler (= 11.5.114~11.5.1-1ubuntu1) しかし、インストールされようとしていません
                       依存: nvidia-cuda-dev (= 11.5.1-1ubuntu1) しかし、インストールされようとしていません
                       依存: nvidia-opencl-dev (= 11.5.1-1ubuntu1) しかし、インストールされようとしていません または
                               opencl-dev
                       推奨: nvidia-cuda-toolkit-doc (= 11.5.1-1ubuntu1) しかし、インストールされようとしていません
                       推奨: nvidia-cuda-gdb (= 11.5.114~11.5.1-1ubuntu1) しかし、インストールされようとしていません
                       推奨: nvidia-visual-profiler (= 11.5.114~11.5.1-1ubuntu1) しかし、インストールされようとしていません
                       推奨: nsight-compute (= 2021.3.1.4~11.5.1-1ubuntu1)
                       推奨: nsight-systems (= 2021.3.3.2~11.5.1-1ubuntu1)
 nvidia-dkms-535 : 依存: nvidia-kernel-common-535 (>= 535.104.05) しかし、535.86.10-0ubuntu1 はインストールされようとしています
 nvidia-driver-535 : 依存: libnvidia-gl-535 (= 535.104.05-0ubuntu0.22.04.4) しかし、535.86.10-0ubuntu1 はインストールされようとしています
                     依存: nvidia-kernel-common-535 (>= 535.104.05) しかし、535.86.10-0ubuntu1 はインストールされようとしています
E: 未解決の依存関係です。'apt --fix-broken install' を実行してみてください (または解法を明示してください)。
```

指摘どおりにapt --fix-broken installを実行する。
```
$ apt --fix-broken install
```

上記で今回は解決…。GPU周りは雰囲気で触っているのもあってこれで良いのか謎です。
