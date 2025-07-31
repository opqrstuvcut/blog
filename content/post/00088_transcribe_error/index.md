---
title: OpenAIのPythonモジュール利用で「Unsupported file format」がでる
tags:
  [
    "OpenAI",
    "GPT",
    "Python"
  ]
categories: ["OpenAI", "Python"]
date: 2025-07-31
image: thm.png
---

下記のように、メモリ上の音声データをBytesIOに書き込み、OpenAIのAPIを実行しようとしました．

```python
import openai
client = openai.OpenAI()
f = io.BytesIO(voice)
f.seek(0)

transcription = client.audio.transcriptions.create(
    model="gpt-4o-mini-transcribe",
    file=f,
    language="ja",
    response_format="json",
)
```

この場合、下記のようなエラーが出てしまいます．
```
openai.BadRequestError: Error code: 400 - {'error': {'message': 'Unsupported file format', 'type': 'invalid_request_error', 'param': 'file', 'code': 'unsupported_value'}}
```

公式ドキュメントを見ていても解決しなかったのですが、GPTに問い詰めていると、下記の回答が得られました．
> OpenAI API (Whisper含む) は「ファイル名や拡張子」を見てファイル形式を判断していることがあります。
open("tmp.mp3", "rb")で渡す場合はname属性にファイル名（tmp.mp3）が付与されていますが、
io.BytesIOの場合はname属性がないため、「拡張子が分からず、形式不明」とみなされ、サポート外エラーになります。

これに従い、下記のようにf.nameの設定を追加したところ、うまくいくようになりました。
```python
import openai
client = openai.OpenAI()
f = io.BytesIO(voice)
f.name = "sample.mp3"
f.seek(0)

transcription = client.audio.transcriptions.create(
    model="gpt-4o-mini-transcribe",
    file=f,
    language="ja",
    response_format="json",
)
```
