---
title: magma-nvimで理想に近いVimでのJupyter環境を作る
tags: ["vim", "Python", "Jupyter", "magma", "jupytxt"] 
categories: ["vim","Python"] 
date: 2023-06-12
---

vimで開発しているとブラウザからJupyterを触るのが嫌になってきます。  
* 開発中にターミナルとブラウザへ移動が面倒
* vimで使っているformatterとかlinterをそのまま使いたい
* Jupyter上でもvimキーバインドを設定しているけど、ブラウザのキーバインドと被る
* vimからIPython上に実行結果を表示する方法もあるが、残念ながらめんどくさかったりで運用面があわない
* PyCharmやVSCodeを使えばいいじゃんという声が聞こえてきますが、とりあえずPyCharmは気になるところが多くて疲れました...


前々からvimで完結するようにしたいなぁと思っていたのですが、ようやくそれがある程度実現されてきたので紹介していきます。

## magma-nvim
[magma-nvim](https://github.com/dccsillag/magma-nvim)はJupyterカーネルに選択したコードの実行をさせて、結果をvim上に表示するためのプラグインです。  
上記のリンク先にデモ動画が載っていますが、自分が用意したものを載せておきます。
{{<gallery>}}
<video src="magma-sample.webm" autoplay loop></video>
{{</gallery>}}

これが本当に素晴らしくよくできています。magma-nvimの存在を知ったときはテンション爆あがりでした。  

が、自分の運用上はだいぶ困った点がありました。  

というのも、magma-nvimでは:MagmaInitで用意したJupyterのカーネルを選択することができますが、基本的にはホスト上にあるカーネルを選択することになります。  
自分の場合は、案件ごとにJupyter labのサーバーをDockerコンテナ内に立てるようにしていますので、ここが噛み合わないのです。  
Docker内のカーネルをホストのJupyterに追加できるらしい方法はいくつか試したのですが、うまくいかず…。

### カーネル選択の問題の解決
magma-nvimのコードを眺めていると、Jupyter Clientとかいう謎のライブラリを使ってカーネルを操作していることがわかりました。  
じゃあJupyter ClientでDockerコンテナ上で動いているカーネルを触りにいけばいいじゃないとなるのですが、無理っぽい雰囲気です。おそらく。正直このあたり何も知らないのでよく分かりませんが。

苦渋の選択ですが、サーバー上のJupyterカーネルを触るためのAPIが存在しますので、こちらを使ってうまく既存のmagma-nvimと連携させようという方向性になりました。  
非常に重い腰をあげて、それっぽく動くところまで実装したのがこちらになります。
[https://github.com/opqrstuvcut/magma-nvim](https://github.com/opqrstuvcut/magma-nvim)

これを使うと、
```
:MagmaInit http://localhost:8080/?token=5fe4e1d52e7b0fc72986a7683b8d7a71f804b92fee991b7e
```
みたいな感じでJupyterサーバーへのURLをMagmaInitに渡すことで、サーバー上のカーネルを実行できるようになります。  
自分の実装が悪いのか、セルの実行をしたときにもっさりしているような…？

ちなみにJupyterのAPIを扱う部分の実装には下記のブログ記事をかなり参考にさせていただきました。ありがとうございます。  
[https://ohke.hateblo.jp/entry/2019/05/25/180000](https://ohke.hateblo.jp/entry/2019/05/25/180000)

### magma-nvimを使う時の注意
* 最近はmagma-nvimの開発がおこなわれていないようです。とりあえずmagma-nvimを使ってみたい方は[https://github.com/WhiteBlackGoose/magma-nvim-goose](https://github.com/WhiteBlackGoose/magma-nvim-goose)を利用するとバグが直っていたり、機能が追加されていたりするので良いかと思います。
* 画像の表示がうまくいかないケースが報告されています。自分の環境でも画像が表示されたり、されなかったり不思議な現象がおきています。これはなんとかしたいですが、magma-nvimを使わない理由とまではいきません。

## 便利にするためのkeymap
Notebookを編集するときには[https://github.com/goerz/jupytext.vim](https://github.com/goerz/jupytext.vim)によって、いい感じにNotebookの内容をフォーマットして表示しています。  
これと以下のようなkermapを組み合わせてNotebookを編集しています。


### セルの実行
magma-nvimだと、例えばvisual modeで複数行を選択し、:MagmaEvaluateVisualを実行することでセルが定義されます（Notebook上のセルとは違う話です）。一度セルを定義すれば、その後はvisual modeで行選択をしなくてもセル単位で実行することができます。  

なのですが、やはりめんどうなのと、jupytextを使えば実際のNotebook上のセルを# %%で挟んで表示してくれますので、# %%で囲まれたコード単位で実行したくなります。  
これは次のMagmaEvaluateCellを定義して実現しています（luaやvimのapiの使い方のセオリーがわからないので変だったらすみません）。

```
function FindNextLineWithText(pattern)
    local currentLine = vim.fn.line('.')
    local totalLines = vim.fn.line('$')

    for line = currentLine + 1, totalLines do
        local lineText = vim.api.nvim_buf_get_lines(0, line - 1, line, false)[1]
        if lineText:find(pattern) then
            return line
        end
    end

    return totalLines
end

function FindPrevLineWithText(pattern, start_buffer)
    start_buffer = start_buffer or 0
    local currentLine = vim.fn.line('.')

    for line = currentLine + start_buffer , 1, -1 do
        local lineText = vim.api.nvim_buf_get_lines(0, line - 1, line, false)[1]
        if lineText:find(pattern) then
            return line
        end
    end

    return 0
end

function MagmaEvaluateCell()
    local pattern = "# %%"
    local startLine = FindPrevLineWithText(pattern)
    local endLine = FindNextLineWithText(pattern)

    vim.api.nvim_win_set_cursor(0, { startLine + 1, 0 })
    vim.api.nvim_feedkeys('V'..(endLine-1)..'gg', 'n', true)
    vim.api.nvim_feedkeys(vim.api.nvim_replace_termcodes(':<C-u>MagmaEvaluateVisual<CR>', true, true, true), 'n', true)
end

```
載せているデモ動画ではMagmaEvaluateCellを"mc"に設定しており、実行したいセル上にカーソルをもっていって利用しています。

### セルの新規作成
カーソルが存在するセルの下に新しいセルを作ります。
```
function AddNewCell()
    local pattern = "# %%"
    local endLine = FindNextLineWithText(pattern)
    local bufnr = vim.api.nvim_get_current_buf()
    vim.api.nvim_buf_set_lines(bufnr, endLine, endLine, false, {"", pattern})

    vim.api.nvim_win_set_cursor(0, { endLine + 1, 0 })
    vim.api.nvim_command("startinsert")
end
```

### セルの削除
とりあえずセルを簡単に削除するためのものです。
```
function DeleteCell()
    local pattern = "# %%"
    local startLine = FindPrevLineWithText(pattern)
    local endLine = FindNextLineWithText(pattern)
    vim.api.nvim_buf_set_lines(0, startLine, endLine, false, {})
end
```

### セルの移動
次のセルへの移動です。
```
function MoveNextCell()
    local pattern = "# %%"
    local endLine = FindNextLineWithText(pattern)
    vim.api.nvim_win_set_cursor(0, { endLine, 0 })
end

function MovePrevCell()
    local pattern = "# %%"
    local line = FindPrevLineWithText(pattern, -1)

    if line > 0 then
	vim.api.nvim_win_set_cursor(0, { line, 0 })
    end
end
```
