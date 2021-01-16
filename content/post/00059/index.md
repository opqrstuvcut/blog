---
title: GCPのWorkload IdentityでGKEとGCPサービスとの連携を安全におこなう
tags: ["GCP", "GKE", "Workload Identity", "Kubernetes"]
categories: ["Kubernetes", "GCP"]
date: 2021-01-16
---

# Workload Identityについて

Compute Engineのホストからは何もせずにGCPのサービスにアクセス可能ですが、GKEを利用しているときに、クラスタ内のコンテナからStorageなどのサービスにどうやってアクセスするかという話がでてきます。

GCPのサービスアカウントのキーをコンテナ内に配置できればよいですが、それを実現すると今度はセキュリティ上の問題があらわれたりします。

こういった問題を解決してくれるのがWorkload Identityです。Workload  IdentityではKubernetesサービスアカウントというものとGCPのサービスアカウントの紐付けをおこない、KubernetesサービスアカウントでGCPのサービスを利用できるようにします。

手順は基本的には[公式](https://cloud.google.com/kubernetes-engine/docs/how-to/workload-identity?hl=ja)に従えばいいですが、ここでは簡素化したものを載せておきます。

# 手順

1. プロジェクト用のサービスアカウントを作成します。サービスアカウントに適切な権限を設定しておく必要あるので、注意しておこないます。下記で利用される"GSA"はこのサービスアカウントの名称をあらわしています。
2. GCPのコンソールかgcloudコマンドのいずれかでクラスターを作成します。このときworkload identityの有効化を必ずおこないます。
既存のクラスターに対しては次でWorkload Identityの有効化ができるようです（未確認）。

    ```bash
    $ gcloud container clusters update <クラスタ名> \
      --workload-pool=<プロジェクトID>.svc.id.goog
    ```

3. terminal上で、作成したクラスターを操作できるようにします。例えば次のようなコマンドを実行します。

    ```bash
    $ gcloud container clusters get-credentials <作成したクラスタ名> \
      --zone <asia-northeast1-aなどのzoneの指定>
    ```

4. Kubernetes Service Account（KSA）を作成します。

    ```bash
    $ kubectl create serviceaccount --namespace default \
      <KSAの名称（適当につける）>
    ```

    次のようなmanifestを作成し、podを作成しておくでもOKなはずです。

    ```yaml
    apiVersion: v1
    kind: ServiceAccount
    metadata:
      name: <KSAの名称>
    ```

5. GSAがSAと紐付けできるように権限を付与します。

    ```bash
    $ gcloud iam service-accounts add-iam-policy-binding \
      --role roles/iam.workloadIdentityUser \
      --member "serviceAccount:<プロジェクトID>.svc.id.goog[default/<KSAの名称>]" \
      <GSAの名称>@<プロジェクトID>.iam.gserviceaccount.com
    ```

6.  最後に次を実行。

    ```bash
    $ kubectl annotate serviceaccount \
      --namespace default \
      <KSAの名称> \
      iam.gke.io/gcp-service-account=<GSAの名称>@<プロジェクトID>.iam.gserviceaccount.com
    ```
