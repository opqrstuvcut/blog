---
title: AWSのDead Letter QueueのメッセージをもとのQueueに戻す
tags: ["AWS", "SQS", "DLQ"]
categories: ["AWS"]
date: 2021-06-05
---

AWSのSQSを使うときにちょっと困るのが、アプリの不具合等でDead Letter Queueに送られたメッセージをもとのQueueに戻したいケースです。
調べた感じでは、通常のAWS CLIでは簡単にはできなさそうです。

理想的には、送信元と送信先のQueueを指定さえすればメッセージを全部送れるような仕組みがあるといいなぁ…と思っていたところ、すばらしい実装を見つけました。
それが[SQS Message Mover](https://github.com/mercury2269/sqsmover)です。

使い方はとても簡単です。READMEに書いてあることをやれば簡単に動きます。
1. インストール

    macの方はbrewで入れても良いと思いますが、自分は次のコマンドで入れました（brew installより待たなくて済んだ説はあります）。
    ```bash
    $ curl https://raw.githubusercontent.com/mercury2269/sqsmover/master/install.sh | sudo sh
    ```
2. credentialsへのキーの指定

    これは~/.aws/credentialsにaccess keyとsecret keyを指定します（知っている方は多いかと思いますが）。
    ```
    [default]
    aws_access_key_id = <YOUR_ACCESS_KEY_ID>
    aws_secret_access_key = <YOUR_SECRET_ACCESS_KEY>
    ```
3. コマンドを実行

    次のように送りたいメッセージをもつQueueと送り先のQueueの名前を指定するだけです。
    ```
    $ sqsmover --source=<送り元のQueue名> --destination=<送り先のQueue名>
    ```

    次のような標準出力が確認できるかと思います。
    ```
    • Source queue URL: https://sqs.ap-northeast-1.amazonaws.com/xxx/queue_from
    • Destination queue URL: https://sqs.ap-northeast-1.amazonaws.com/xxx/queue_to
    • Approximate number of messages in the source queue: x
    • Starting to move messages...

                 |████████████████████████████████████████| 100%

    • Done. Moved x messages
    ```

とてもお手軽なので同じ状況の人にはおすすめです。
