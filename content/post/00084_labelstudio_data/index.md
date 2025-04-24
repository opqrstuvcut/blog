---
title: Label StudioのAPIを利用したデータ連携のメモ
tags: ["Label Studio", "アノテーション", "ディープラーニング", "物体検出", "Pre-Annotations"]
categories: ["ディープラーニング", "アノテーション"]
date: 2025-04-24
image: thm.png
---

Label StudioのAPIを利用するとき用のメモになります．下記で出てくる例は物体検出を例にしています．

## Taskの一覧の取得
```python
import os
import requests

LABEL_STUDIO_HOST = os.environ["LABEL_STUDIO_HOST"]
LABEL_STUDIO_PROJECT_ID = os.environ["LABEL_STUDIO_PROJECT_ID"]
LABEL_STUDIO_TOKEN = os.environ["LABEL_STUDIO_TOKEN"]

# page_sizeとpageによって取得できるタスクの範囲が変わるため、必要に応じて変更.
tasks = requests.get(
    f"{LABEL_STUDIO_HOST}/api/tasks/?project={LABEL_STUDIO_PROJECT_ID}&page_size=1000&page=1",
    headers={"Authorization": f"Token {LABEL_STUDIO_TOKEN}"},
).json()
```

## Taskの新規登録
```python
task_info = requests.post(
    f"{LABEL_STUDIO_HOST}/api/tasks/?project={LABEL_STUDIO_PROJECT_ID}",
    headers={"Authorization": f"Token {LABEL_STUDIO_TOKEN}"},
    json={
        "data": {"image": str(storage_file_path)}, # storage_file_pathはlocalならlocalのパス、Cloud上ならばCloud上のパス.
        "project": LABEL_STUDIO_PROJECT_ID,
        "file_upload": 1, # sync済みのdata source上のデータを読み込む場合は1
    },
).json()
```

## Pre-Annotationの登録
案件によっては、既存のモデルやシステムからラフなアノテーションが得られるときがあります．このときにはPre-Annotationを利用すると良いでしょう．

```python
# 登録するアノテーションの作成.
annotations = []
for i, ((left, top, right, bottom), label) in enumerate(zip(boxes, labels)):
    annotations.append(
        {
            "id": str(i),
            "type": "rectanglelabels",
            "from_name": "label", # このあたりの値もきっちり入れておかないと登録がうまくいかないため注意.
            "to_name": "image",
            "original_width": original_width,
            "original_height": original_height,
            "image_rotation": 0,
            "value": {
                "x": left / original_width * 100,
                "y": top / original_height * 100,
                "width": (right - left) / original_width * 100,
                "height": (bottom - top) / original_height * 100,
                "rotation": 0,
                "rectanglelabels": [label],
            },
        }
    )

res = requests.post(
    f"{LABEL_STUDIO_HOST}/api/predictions",
    headers={"Authorization": f"Token {LABEL_STUDIO_TOKEN}"},
    json={
        "task": task_info["id"],
        "model_version": "1",
        "result": boxes,
    },
)
```

## モデルの予測結果の登録
Label Studioにはモデルの予測結果を用いてアノテーションさせる機能がついています．これによって、モデルが解ける部分についてはアノテーションの手間が減るため、非常に作業が楽になります．

### APIサーバー
最初にアノテーションするデータを受け取り、それを推論するAPIサーバーを作成する必要があります．  
このための雛形が用意されているので、それを利用すると良いでしょう．  
https://github.com/HumanSignal/label-studio-ml-backend/tree/master/label_studio_ml/default_configs

雛形のなかで必ず変更する必要があるのがmodel.pyで、特にNewModelクラスのpredictメソッドを変更する必要があります．  
predictメソッドの中で入力データをLabel Studioから受け取り、モデルに推論して結果を変換する処理を書きましょう．

```python
def predict(self, tasks: List[Dict], context: Optional[Dict] = None, **kwargs) -> ModelResponse:
    preds = []
    for task in tasks:
        header = {
            "Authorization": "Token " + LABEL_STUDIO_TOKEN}
        image = Image.open(BytesIO(requests.get(
            LABEL_STUDIO_HOST + task['data']['image'], headers=header).content))
        image = np.array(image)
        result = detect(image) # ここでモデルの推論を実行.
        image_height, image_width = image.shape[:2]

        task_results = [
            {
                "type": "rectanglelabels",
                "value": {
                    "height": (bottom - top) / image_height * 100,
                    "width": (right - left) / image_width * 100,
                    "x": left / image_width * 100,
                    "y": top / image_height * 100,
                    "rotation": 0,
                    "rectanglelabels": [label],
                },
                "score": confidence,
                "original_width": image_width,
                "original_height": image_height,
                "image_rotation": 0,
                "id": str(i),
                "from_name": "label",
                "to_name": "image",
            }
            for i, (left, top, right, bottom, label, confidence) in enumerate(result)
        ]
        preds.append(
            {
                "result": task_results,
                "score": float(np.mean([box["confidence"] for box in result])),
            }
        )

    return preds
```

よしなに書き換えたらdocker-compose upしてAPIの準備はOKです．
なお、fitメソッドもNewModelクラスに存在しますが、訓練まで自動でおこないたい場合に実装すれば良いです．

### Label Studio側の設定
SettingsのModelからAPIサーバーのエンドポイントを設定します．特に難しいところはないと思います．

### 実行
ここまでできたら、あとはprojectのデータの一覧の画面のActionsからRetrieve Predictionsを実行するなどして、アノテーションをさせることができます．

## データのサンプリング
Label Studioの機能として、Uncertainty samplingが存在するのですが、これはEnterprise向けの機能となっています．  
無料版を使っている場合は妥協案として、別途Uncertainty sampling等を用いてデータを100件程度サンプリングし、それを登録してアノテーションをしていくのが良いでしょうか．

