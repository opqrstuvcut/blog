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
