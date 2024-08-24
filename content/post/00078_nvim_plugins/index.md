---
title: おすすめの(Neo)Vimプラグイン
tags: ["NeoVim", "Vim"] 
categories: ["Vimプラグイン"] 
date: 2024-08-22
---

NeoVimで利用しているプラグインはたくさんあるのですが、個人的に激推なプラグインを紹介します。  
下記の設定例の記載が特にないプラグインはLazyでプラグインを読み込んでいるだけのものになります。

## vim-asterisk
https://github.com/haya14busa/vim-asterisk

アスタリスクを押すと、バッファ内のカーソル下の単語が検索できると思うのですが、通常だとヒットした単語にすぐに移動してしまいます。  
個人的には、カーソル下の単語と同じ単語あるんだっけ？と思ったときにもアスタリスクを利用するので、移動してほしくありませんでした。  
vim-asteriskを入れるとそういった問題が起きなくなります。

### 設定例
```lua
{
    "haya14busa/vim-asterisk",
    config = function()
        vim.api.nvim_set_keymap("", "*", "<Plug>(asterisk-z*)", {})
        vim.api.nvim_set_keymap("", "#", "<Plug>(asterisk-z#)", {})
        vim.api.nvim_set_keymap("", "g*", "<Plug>(asterisk-gz*)", {})
        vim.api.nvim_set_keymap("", "g#", "<Plug>(asterisk-gz#)", {})
    end,
}
```

## neoscroll.nvim
https://github.com/karb94/neoscroll.nvim  

C-uやC-dでカーソルを上下したときに、通常は一瞬でカーソルが移動後の表示に切り替わりますが、そうするとさっきまで見ていた行ってどこだっけ？となることがあります。  
neoscrollを使うと、カーソル移動したときにスクロールするような表示になるため、どれくらい移動したのかが視覚的にわかるので、この問題が解決されます。

{{< video src="neoscroll" >}}

### 設定例
```lua
{
    "karb94/neoscroll.nvim",
    config = function()
        require("neoscroll").setup({
            mappings = { "<C-u>", "<C-d>", "zt", "zz", "zb" },
        })

        local t = {}
        local time = "85"
        t["<C-u>"] = { "scroll", { "-vim.wo.scroll", "true", time } }
        t["<C-d>"] = { "scroll", { "vim.wo.scroll", "true", time } }
        t["zt"] = { "zt", { time } }
        t["zz"] = { "zz", { time } }
        t["zb"] = { "zb", { time } }

        require("neoscroll.config").set_mappings(t)
    end,
}
```


## nvim-bqf
https://github.com/kevinhwang91/nvim-bqf

QuickFixを利用しているときに、該当箇所周辺のプレビューウィンドウを表示できるようになります。  
これがないと、いちいちファイルを開いて中身を確認するという必要がでてくるため面倒です。

{{< video src="nvim-bqf" >}}

### 設定例
```lua
{
    "kevinhwang91/nvim-bqf",
    config = function()
        require("bqf").setup({})
    end,
    ft = "qf",
}
```

## vim-qfreplace
https://github.com/thinca/vim-qfreplace

QuickFixでの置換を楽にしてくれます。
自分はTelescopeで単語の検索 -> \<C-q>でQuickFixを開く -> :Qfreplace -> 置換を実行 という感じのフローを使います。

{{< video src="qfreplace" >}}
## registers.nvim
https://github.com/tversteeg/registers.nvim

レジスタの内容が見やすくなります。

{{< video src="registers" >}}

### 設定例
```lua
{
    "tversteeg/registers.nvim",
    cmd = "Registers",
    config = function()
        local registers = require("registers")
        registers.setup({
            window = {
                border = "rounded",
                transparency = 0,
            },
        })
    end,
    keys = {
        { '"', mode = { "n", "v" } },
        { "<C-R>", mode = "i" },
    },
    name = "registers",
}
```


## tiny-code-action.nvim
https://github.com/rachartier/tiny-code-action.nvim

CodeActionの変更内容が見やすくなります。
{{< video src="tiny-code-action" >}}

### 設定例
```lua
{
    "rachartier/tiny-code-action.nvim",
    dependencies = {
        { "nvim-lua/plenary.nvim" },
        { "nvim-telescope/telescope.nvim" },
    },
    event = "LspAttach",
    config = function()
        require("tiny-code-action").setup()
        vim.keymap.set("n", "<leader>ca", function()
            require("tiny-code-action").code_action()
        end, { noremap = true, silent = true })
    end,
}
```

## tiny-inline-diagnostic.nvim
https://github.com/rachartier/tiny-inline-diagnostic.nvim

Diagnosticが見やすくなります。
{{< video src="tiny-inline-diagnostic" >}}

### 設定例
```lua
{
    "rachartier/tiny-inline-diagnostic.nvim",
    event = "VeryLazy",
    config = function()
        require("tiny-inline-diagnostic").setup()
    end,
}
```

## project-cli-commands.nvim
https://github.com/dimaportenko/project-cli-commands.nvim

プロジェクトごとにCLIコマンドを登録しておいて、それを簡単に実行できるようにしてくれます。  
（自分の設定だと）\<leader>ccで登録しているコマンド一覧を選択するwindowが表示され、選ぶとそれが実行されます。  
登録しているコマンドは.nvim/config.jsonに保存する必要があるのですが、初回実行時はそのファイルを作成するか聞いてくれます。

{{< video src="cliproject" >}}

### 設定例
```lua
{
    "dimaportenko/project-cli-commands.nvim",

    dependencies = {
        "akinsho/toggleterm.nvim",
        "nvim-telescope/telescope.nvim",
    },

    -- optional keymap config
    config = function()
        local OpenActions = require("project_cli_commands.open_actions")
        local RunActions = require("project_cli_commands.actions")

        local config = {
            -- Key mappings bound inside the telescope window
            running_telescope_mapping = {
                ["<C-c>"] = RunActions.exit_terminal,
                ["<C-f>"] = RunActions.open_float,
                ["<C-v>"] = RunActions.open_vertical,
                ["<C-h>"] = RunActions.open_horizontal,
            },
            open_telescope_mapping = {
                { mode = "i", key = "<CR>", action = OpenActions.execute_script_vertical },
                { mode = "n", key = "<CR>", action = OpenActions.execute_script_vertical },
                { mode = "i", key = "<C-h>", action = OpenActions.execute_script },
                { mode = "n", key = "<C-h>", action = OpenActions.execute_script },
                { mode = "i", key = "<C-i>", action = OpenActions.execute_script_with_input },
                { mode = "n", key = "<C-i>", action = OpenActions.execute_script_with_input },
                { mode = "i", key = "<C-c>", action = OpenActions.copy_command_clipboard },
                { mode = "n", key = "<C-c>", action = OpenActions.copy_command_clipboard },
                { mode = "i", key = "<C-f>", action = OpenActions.execute_script_float },
                { mode = "n", key = "<C-f>", action = OpenActions.execute_script_float },
                { mode = "i", key = "<C-v>", action = OpenActions.execute_script_vertical },
                { mode = "n", key = "<C-v>", action = OpenActions.execute_script_vertical },
            },
        }

        require("project_cli_commands").setup(config)
    end,
}
```

## markview.nvim
https://github.com/OXY2DEV/markview.nvim

マークダウンの表示がNeoVim上で良い感じになるプラグインです。最高です。

{{< video src="markview" >}}

### 設定例
```lua 
{
    "OXY2DEV/markview.nvim",
    lazy = false, -- Recommended
    branch = "dev",
    -- ft = "markdown" -- If you decide to lazy-load anyway

    dependencies = {
        -- You will not need this if you installed the
        -- parsers manually
        -- Or if the parsers are in your $RUNTIMEPATH
        "nvim-treesitter/nvim-treesitter",

        "nvim-tree/nvim-web-devicons",
    },
    config = function()
        vim.cmd([[highlight MarkviewHeading1 guibg=#E67E80 guifg=white]])
        vim.cmd([[highlight MarkviewHeading2 guibg=#EB9C5F guifg=white]])
        vim.cmd([[highlight MarkviewHeading3 guibg=#83C092 guifg=white]])
        vim.cmd([[highlight MarkviewHeading4 guibg=#7FBBB3 guifg=white]])
        vim.cmd([[highlight MarkviewHeading5 guibg=#D3C6AA guifg=white]])
        vim.cmd([[highlight MarkviewHeading6 guibg=#384B55 guifg=white]])
        vim.cmd([[highlight MarkviewCode guibg=#1e2326]])
    end,
}
```


## nvim-cokeline
https://github.com/willothy/nvim-cokeline

バッファーラインのフレームワークです。  
元々は、bufferline.nvimやbarbar.nvimを使っていたのですが、なにかの挙動が想定通りいかず試しに切り替えてみようくらいのモチベーションでした。  
使ってみると、カスタマイズ性が高いですが、難しいところがあるわけではなく、また気になるような変な動作などもない個人的には理想のバッファーラインのプラグインです。

ここまでのデモ動画で表示されているバッファーラインに適用されています。

### 設定例
*色はカラースキームにあわせて設定が必要なはずです。
```lua
{
    "willothy/nvim-cokeline",
    dependencies = {
        "nvim-lua/plenary.nvim",
        "nvim-tree/nvim-web-devicons",
        "stevearc/resession.nvim",
    },
    config = function()
        local is_picking_focus = require("cokeline.mappings").is_picking_focus
        local is_picking_close = require("cokeline.mappings").is_picking_close
        local get_hex = require("cokeline.hlgroups").get_hl_attr

        local red = vim.g.terminal_color_1
        local yellow = vim.g.terminal_color_3
        require("cokeline").setup({
            default_hl = {
                fg = function(buffer)
                    return buffer.is_focused and get_hex("Normal", "fg") or get_hex("Comment", "fg")
                end,
                bg = function(buffer)
                    return buffer.is_focused and get_hex("DiffChange", "bg") or get_hex("ColorColumn", "bg")
                end,
            },
            components = {
                {
                    text = function(buffer)
                        return (buffer.index ~= 1) and "▏" or ""
                    end,
                },
                {
                    text = "  ",
                },
                {
                    text = function(buffer)
                        return buffer.is_focused and " " or ""
                    end,
                    fg = function(buffer)
                        if buffer.is_focused then
                            return "red"
                        end
                    end,
                },
                {
                    text = function(buffer)
                        return (is_picking_focus() or is_picking_close()) and buffer.pick_letter .. " "
                            or buffer.devicon.icon
                    end,
                    fg = function(buffer)
                        return (is_picking_focus() and yellow)
                            or (is_picking_close() and red)
                            or buffer.devicon.color
                    end,
                    italic = function()
                        return (is_picking_focus() or is_picking_close())
                    end,
                    bold = function()
                        return (is_picking_focus() or is_picking_close())
                    end,
                },
                {
                    text = function(buffer)
                        return buffer.index .. ": " .. buffer.filename
                    end,
                    bold = function(buffer)
                        return buffer.is_focused
                    end,
                    fg = function(buffer)
                        if buffer.is_modified then
                            return "blue"
                        else
                            return get_hex("Normal", "fg")
                        end
                    end,
                },
                {
                    text = function(buffer)
                        return (buffer.diagnostics.errors > 0) and "  " .. buffer.diagnostics.errors or ""
                    end,
                    fg = function(buffer)
                        return "red"
                    end,
                },
                {
                    text = function(buffer)
                        return (buffer.diagnostics.warnings > 0) and "  " .. buffer.diagnostics.warnings or ""
                    end,
                    fg = function(buffer)
                        return "yellow"
                    end,
                },
                {
                    text = function(buffer)
                        return (buffer.diagnostics.infos > 0) and "  " .. buffer.diagnostics.infos or ""
                    end,
                    fg = function(buffer)
                        return "blue"
                    end,
                },
                {
                    text = function(buffer)
                        return (buffer.diagnostics.hints > 0) and "  " .. buffer.diagnostics.hints or ""
                    end,
                    fg = function(buffer)
                        return "blue"
                    end,
                },
                {
                    text = " ",
                },
                {
                    text = "☓",
                    on_click = function(_, _, _, _, buffer)
                        buffer:delete()
                    end,
                },
                {
                    text = "  ",
                },
            },
            tabs = {
                placement = "right",
                components = {
                    {
                        text = function(tabpage)
                            return " " .. tabpage.number .. " |"
                        end,
                        fg = function(buffer)
                            return buffer.is_active and get_hex("Normal", "fg") or get_hex("Comment", "fg")
                        end,
                        bg = function(tabpage)
                            return tabpage.is_active and get_hex("StatusLine", "bg") or get_hex("ColorColumn", "bg")
                        end,
                    },
                },
            },
        })

        local map = vim.api.nvim_set_keymap
        for i = 1, 9 do
            map("n", ("<Leader>%s"):format(i), ("<Plug>(cokeline-focus-%s)"):format(i), { silent = true })
        end
        map("n", "<leader>bc", "<Plug>(cokeline-pick-close)", { silent = true })
        map("n", "<leader>bp", "<Plug>(cokeline-pick-focus)", { silent = true })
    end,
}

```

## chowcho.nvim
https://github.com/tkmpypy/chowcho.nvim

簡単にwindowを移動できるようにするためのプラグインです。これがあるとwindowの移動がかなり楽です。  
具体的には、このプラグインを使うと、例えば2つしかwindowがなければ\<C-w>\<C-w>でもう一方のwindowに移動するという設定ができます。
またwindowが多い場合でも、動的にwindowにキーが割り振られて移動しやすくなっています。

{{< video src="chowcho" >}}

### 設置例
```lua
{
    "tkmpypy/chowcho.nvim",
    config = function()
        require("chowcho").setup({
            icon_enabled = true,
            active_border_color = "#FF9E3B",
            border_style = "default",
            use_exclude_default = false,
            exclude = function(buf, win)
                local fname = vim.fn.expand("#" .. buf .. ":t")
                return fname == ""
            end,
            zindex = 10000,
        })

        chowcho = require("chowcho")

        local win_keymap_set = function(key, callback)
            vim.keymap.set({ "n", "t" }, "<C-w>" .. key, callback)
            vim.keymap.set({ "n", "t" }, "<C-w><C-" .. key .. ">", callback)
        end

        win_keymap_set("w", function()
            local wins = 0

            for i = 1, vim.fn.winnr("$") do
                local win_id = vim.fn.win_getid(i)
                local conf = vim.api.nvim_win_get_config(win_id)

                if conf.focusable then
                    wins = wins + 1

                    if wins > 2 then
                        chowcho.run()
                        return
                    end
                end
            end

            vim.api.nvim_command("wincmd w")
        end)
    end,
}
```

## lualine-time
https://github.com/archibate/lualine-time

lualineに時間が表示されるようになります。地味にかなり便利だと思っていますが、ターミナルで時間を表示させている人などは不要そうです。
{{< video src="lualine-time" >}}

## vim-arsync
https://github.com/KenN7/vim-arsync

リモートのサーバーへファイルを同期してくれます。  
ファイルを保存したときに自動で同期してくれたり、同期したくないファイルを選べたりと機能は充実しているかと思います。  

## molten-nvim
https://github.com/benlubas/molten-nvim

PythonのJupyter NotebookをNeoVim上から実行するときに使っています。現在はこれがないのは結構苦行と感じるレベルです。  
もともとはmagma.nvimというプラグインがあったのですが、これのforkになっていてそこから色々と機能が足されたりしています。

{{< video src="molten" >}}

### 設定例
これ用に色々とキーマップを設定しており、maでcellの追加、mdで削除、mcでセルを実行ができるようにしています。

```lua
{
    "benlubas/molten-nvim",
    dependencies = { "3rd/image.nvim" },
    build = ":UpdateRemotePlugins",
    init = function()
        vim.g.molten_image_provider = "image.nvim"
        vim.g.molten_split_direction = "right"
        vim.g.molten_output_win_border = "rounded" -- ':h nvim_open_win()' -> border option
        vim.g.molten_auto_open_output = false
        vim.g.molten_output_virt_lines = true
        vim.g.molten_virt_text_output = true
        vim.g.molten_use_border_highlights = true
        vim.g.molten_output_win_border = { "x", "━", "x", "┃" }
    end,
    config = function()
        vim.api.nvim_set_keymap("n", "ml", ":MoltenEvaluateLine<CR>", { silent = true })
        vim.api.nvim_set_keymap("n", "mr", ":MoltenReevaluateCell<CR>", { silent = true })
        vim.api.nvim_set_keymap("n", "ms", ":MoltenShowOutput<CR>", { silent = true })
        vim.api.nvim_set_keymap(
            "n",
            "me",
            ":MoltenShowOutput<CR>:noautocmd MoltenEnterOutput<CR>",
            { silent = true }
        )
        vim.api.nvim_set_keymap("n", "mh", ":MoltenHideOutput<CR>", { silent = true })

        local pattern = "```python"
        local end_pattern = "```"
        vim.api.nvim_set_keymap(
            "n",
            "mi",
            [[<Cmd>lua MoltenInitJupyterKernel()<CR>]],
            { noremap = true, silent = true }
        )

        function FindNextLineWithText(pattern)
            local currentLine = vim.fn.line(".")
            local totalLines = vim.fn.line("$")

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
            local currentLine = vim.fn.line(".")

            for line = currentLine + start_buffer, 1, -1 do
                local lineText = vim.api.nvim_buf_get_lines(0, line - 1, line, false)[1]
                if lineText:find(pattern) then
                    return line
                end
            end

            return 0
        end

        function MoltenEvaluateCell()
            local r, c = unpack(vim.api.nvim_win_get_cursor(0))

            vim.api.nvim_feedkeys(vim.api.nvim_replace_termcodes("zt", true, true, true), "n", true)
            vim.api.nvim_feedkeys(vim.api.nvim_replace_termcodes("5<C-y>", true, true, true), "n", true)

            local startLine = FindPrevLineWithText(pattern)
            local endLine = FindNextLineWithText(end_pattern)

            vim.opt.scrolloff = 1 -- scrolloffをもとに戻さないと正しく処理できないため、一旦1にする
            vim.api.nvim_win_set_cursor(0, { startLine + 1, 0 })
            vim.api.nvim_feedkeys("V", "n", true)
            vim.api.nvim_feedkeys((endLine - 1) .. "G", "n", true)

            vim.api.nvim_feedkeys(
                vim.api.nvim_replace_termcodes(":<C-u>MoltenEvaluateVisual<CR>", true, true, true),
                "n",
                true
            )

            vim.schedule(function()
                vim.api.nvim_win_set_cursor(0, { r, c })
                vim.opt.scrolloff = 10
            end)
        end
        vim.api.nvim_set_keymap("n", "mc", [[<Cmd>lua MoltenEvaluateCell()<CR>]], { noremap = true, silent = true })

        function DeleteCell()
            local startLine = FindPrevLineWithText(pattern)
            local endLine = FindNextLineWithText(end_pattern)
            vim.api.nvim_buf_set_lines(0, startLine - 1, endLine, false, {})
        end
        vim.api.nvim_set_keymap("n", "md", [[<Cmd>lua DeleteCell()<CR>]], { noremap = true, silent = true })

        function MoveNextCell()
            local startLine = FindNextLineWithText(pattern)
            vim.api.nvim_win_set_cursor(0, { startLine, 0 })
        end
        vim.api.nvim_set_keymap("n", "mn", [[<Cmd>lua MoveNextCell()<CR>]], { noremap = true, silent = true })

        function MovePrevCell()
            local line = FindPrevLineWithText(pattern, -1)

            if line > 0 then
                vim.api.nvim_win_set_cursor(0, { line, 0 })
            end
        end
        vim.api.nvim_set_keymap("n", "mp", [[<Cmd>lua MovePrevCell()<CR>]], { noremap = true, silent = true })

        function AddNewCell()
            local startLine = FindNextLineWithText(pattern)
            local bufnr = vim.api.nvim_get_current_buf()

            if startLine >= vim.api.nvim_buf_line_count(0) then
                startLine = startLine + 1
            end

            vim.api.nvim_buf_set_lines(bufnr, startLine - 1, startLine - 1, false, { "", pattern })
            vim.api.nvim_buf_set_lines(bufnr, startLine + 1, startLine + 1, true, { end_pattern, "" })
            vim.api.nvim_buf_set_lines(bufnr, startLine + 1, startLine + 1, false, { "" })

            vim.api.nvim_win_set_cursor(0, { startLine + 2, 0 })
            vim.api.nvim_command("startinsert")
        end
        vim.api.nvim_set_keymap("n", "ma", [[<Cmd>lua AddNewCell()<CR>]], { noremap = true, silent = true })
    end,
}
```
