---
published: true
topics:
  - python
  - vim
  - neovim
  - JupyterLab
  - kaggle
type: tech
emoji: 📊
title: どうしてもneovimでjupyterlabしたいんや
---
## はじめに
はじめまして、makioです。
仮想通貨自動売買プログラムの開発をしています。

データ分析の勉強がてらkaggleに入門しようとブラウザやらvscodeでjupyterを操作していましたが、「vimでやりてぇー」という気持ちが抑えきれずneovim上でjuypterをやる方法を模索しました。

調べた結果、nevim上で以下のようなことができるようになりました。
- juypter notebook(\*\.ipynb)を開くと自動でpythonファイルとして開く。
- 保存時にipynbに編集内容を反映する。
- 「\#\%\%」で囲われた箇所を一つのセルとしてコードを実行できる。
- nvimのエディタ上にグラフ等の実行結果を表示できる
![利用時の様子](/images/nvim-jupyter/nvim-jupyter.gif)

### 実現した構成について
以下の構成で成功しています。
- m1 mac book air
- ターミナル：wezterm
	※kittyでも可です。
- nvimのプラグインツール：lazyvim
	https://www.lazyvim.org

## 導入手順
### 拡張機能以外の前準備
 1. weztermのkitty画像表示プロトコルの設定を有効にする
```lua
enable_kitty_graphics=true
```

2. 画像表示に必要なツールをインストール
```shell
# ターミナルで画像を表示するために必要がツール
brew install imagemagick
# 画像をnvimエディタ上で表示する拡張機能が使うlua外部プラグインツール
brew install luarocks
# 画像をnvimエディタ上で表示するための外部プラグインツールをインストールする
luarocks --local --lua-version=5.1 install magick
```

3. nvimがluaの外部プラグインを読み込む先のパスを設定
.config/nvim/lua/config/options.lua
＃lazyvimが起動時に読み込むファイルであればどこでも良いです。
```lua
-- Example for configuring Neovim to load user-installed installed Lua rocks:
package.path = package.path .. ";" .. vim.fn.expand("$HOME") .. "/.luarocks/share/lua/5.1/?/init.lua;"
package.path = package.path .. ";" .. vim.fn.expand("$HOME") .. "/.luarocks/share/lua/5.1/?.lua;"
```

4. python環境の準備
```shell
# 仮想環境を作成(pythonのversionは3.10以上)
pyenv local 3.10.11
python -m venv venv
source venv/bin/activate

# nvimからjupyterを利用するのに必要なpythonツールをインストール
pip install pynvim
pip install jupyterclient

# jupyterlabをインストール
pip install jupyterlab
```

:::message
poetryで作成した仮想環境に入るとなぜかnvim上で画像表示することができませんでした。
私は特にpoetryにこだわりはないので標準のvenvで構築しています。
:::

### 拡張機能の設定
#### 利用する拡張機能について
| 拡張機能 | 説明 |
| ---- | ---- |
| jupytext | \*\.ipyenvをpy形式に変換して開くために利用 |
| NotebookNavigator | 「\#\%\%」をセルとしてみなすために利用。セルブロック内のプログラムコードをmoltenに連携してくれる。 |
| molten-nvim | pythonプログラムを実行し、nvimエディタ上に結果を表示する。 |
| image.nvim | 画像をnvimエディタ上に表示するために利用。molten経由で利用される。 |

#### 設定ファイル
以下のファイルを追加してください。

https://github.com/afk2777/dotfiles/blob/master/.config/nvim/lua/plugins/jupyter.lua

## 使い方
1. 適当な\*\.ipyenvファイルをnvimで開く。
2. 以下ショートカットキーをセルブロック内で実行。
私の場合\<leader\>はスペースキーです。

| ショートカットキー | 説明 |
| ---- | ---- |
| \<leader\>rc | cellブロック内のプログラムの実行 |
| \<leader\>rm | cellブロック内のプログラムの実行して次のセルに移動 |

初回セル実行時は利用するpythonカーネルを聞かれるので選択してください。

![](/images/nvim-jupyter/python-kernel.png)


### 機能拡張について補足
今回、利用するのに最低限の機能を書いてますが、適宜拡張機能のページを見てショートカットキーに割り当てて拡張してみてください。

moltenでは設定を見直すことでセルの実行結果を仮想テキスト上に表示し続ける機能もあります。＃ただ、この機能は画像が乱れることがあるので私は利用していません。

## 最後に
vim初心者であるため導入するのにめちゃくちゃ時間がかかってしまいました。
こんなことにこんな時間かけてええんやろかと考えたりもしましたが本当にやってよかったです。
何を作るかも大事だけど、どう作るかにもこだわりたいですし。

## 参考記事
- weztermのkitty画像表示プロトコル対処
https://github.com/wez/wezterm/issues/986 
