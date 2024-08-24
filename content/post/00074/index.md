---
title: docker composeでAWS ECSにデプロイするときのtips
tags: ["AWS"] 
categories: ["AWS", "ECS"] 
date: 2022-12-13
image: feature.png
---

docker composeを使ってAWSのECSにアプリをデプロイ可能ですが、もしかすると役立つものがあるかもしれないのでメモを残しておきます。
作業したのが半年前なので少し情報が古いかもしれないのです。

## AWSのサービスへのアクセス権限周り
ECSのタスクにAWSのサービスへのアクセス権限を与える場合はdocker-compose.ymlに次のように記述すれば良いです（下記はSQSのフルアクセスの例）。
```
service:
  api: 
    x-aws-policies:
      - "arn:aws:iam::aws:policy/AmazonSQSFullAccess"
```

## AWSのsecretの読み込み
secretとの連携はdocker-compose.ymlに次のように記述します。
```
secrets:
  sample_secret:
    name: "シークレットのARN"
    external: true
```
また、読み込ませたいコンテナにも設定を追加します。例えば次のようにします。
```
service:
  api: 
    secrets:
      - sample_secret
```
コンテナからは/run/secrets/sample_secretというパスを参照できるようになっており、この中身がsecretの値になります。

## GPUインスタンス
特に何も指定しない場合、FARGATE上にECSのタスクが展開されます。
GPUインスタンスを使いたい場合おそらく2つ選択肢があります。
* [https://www.docker.com/blog/deploy-gpu-accelerated-applications-on-amazon-ecs-with-docker-compose/](https://www.docker.com/blog/deploy-gpu-accelerated-applications-on-amazon-ecs-with-docker-compose/) を参考にgpuの記述をdocker-compose.ymlに追加
* docker compose convertを使ってCloudFormationテンプレートを出力し、それを編集してGPUインスタンスが使えるようにする。

自分は後者を選択しました。
というのも、複数のタスクを1つのGPUインスタンス上で実行したかったのですが、前者の方法だと1インスタンスにつき1タスクという制限がありました。
このため、後者を採用し、かつ次の変更を加えています。

### GPUインスタンスのruntimeの設定
1インスタンスに1タスクの問題は次のようにCloudFormationテンプレートのLaunchConfigurationのUserDataに処理を追加することで解決できます。
```
  LaunchConfiguration:
    Properties:
      IamInstanceProfile:
        Ref: EC2InstanceProfile
      ImageId: ...
      InstanceType: g4dn.xlarge
      SecurityGroups:
      - Ref: ...
      AssociatePublicIpAddress: true
      UserData:
        Fn::Base64: !Sub
          - |
            #!/bin/bash
            echo ECS_CLUSTER=${ClusterName} >> /etc/ecs/ecs.config
            (grep -q ^OPTIONS=\"--default-runtime /etc/sysconfig/docker && echo '/etc/sysconfig/docker needs no changes') || (sed -i 's/^OPTIONS="/OPTIONS="--default-runtime nvidia /' /etc/sysconfig/docker && echo '/etc/sysconfig/docker updated to have nvidia runtime as default' && systemctl restart docker && echo 'Restarted docker')
          - {
              ClusterName: SampleCluster
            }
      KeyName: ...
    Type: AWS::AutoScaling::LaunchConfiguration
```

この解決方法はどこかで見たものを利用したものですが、リンク先を失ってしまいました。


## ENI
複数のタスクを1インスタンスの上に立ち上げるとネットワークインターフェースの問題が発生してきます。  
docker compose convertで生成したCloudFormationテンプレートでは、awsvpcが設定されており、これだと1タスクが1つのネットワークインターフェースに対応するようです。  
ネットワークインターフェースはインスタンスによるのですが、例えばg4dnは3つですので、3タスクまでしかデプロイできないことになります。

### Trunking
前述の問題を解決したいときの1つの解決方法はTrunkingです。  
これはネットワークインターフェースをインスタンスに追加できます！  
ただ話はそんなに簡単ではなく、これが適用できるのは一部のインスタンスに限ります…。自分が使いたいg4dnは対象外でした。

### awsvpc -> bridge 
もう1つの解決方法はawsvpcを使うのを諦めてNetworkModeをbridgeにすることです。
NetworkModeを変更するにあたって、docker compose convertで生成されたCloudFormationテンプレートを色々と変更する必要がありますが、もはや仕方がないのでこれで解決しました。

## まとめ
docker composeコマンドでのdeployは非常に便利ですが、特殊なことは対応していないので結局CloudFormationテンプレートを修正していく必要がでてきます。
とはいえ、非常に便利な機能であることは間違いないですね。
