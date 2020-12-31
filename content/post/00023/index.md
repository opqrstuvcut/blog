---
title: TensorBoardのDocker Image
tags:  ["TensorFlow", "TensorBoard"]
categories:  ["TensorFlow"]
date: 2020-11-18
showtoc: true
---

たまにTensorBoardを使うときに、ホスト環境などにTensorBoardを入れるより、それ用のコンテナをたてたくなったので、そのメモです。

Docker Imageは以下のとおり。

```
FROM python:3.8
RUN pip install tensorflow
WORKDIR /logs
ENTRYPOINT ["tensorboard", "--logdir", "/logs", "--host", "0.0.0.0"]   
```

次のような感じでdocker buildとdocker runします。
```
$ docker build -t tensorboard .
$ docker run -it --rm -p 10000:6006 -v $PWD/logs:/logs tensorboard
```

-vに指定するhost側のlogのディレクトリのパスと-pに指定するportは適当に変更する。
