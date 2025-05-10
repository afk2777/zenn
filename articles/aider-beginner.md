---
published: true
topics:
  - AI
  - Aider
  - Neovim
  - Cursor
type: tech
emoji: 🤖
title: 微VimerによるターミナルAIコーディングツール「Aider」入門書
---
## 📘はじめに
aiderとはターミナル上で動作するAIエージェントコーディングツールで、競合はcursor、cline、claude code、codex等です。

aiderに入門したので、せっかくだから入門書を作成してみました。
使い始めたばかりで誤りもあるかもしれませんが、参考にしてください。

### 🤔入門した理由
cursorを使っているが、以下理由により乗り換えを検討するためです。
- 微vimerで**やっぱりターミナルから離れたくない**
	- claude codeも良さげだが、claudeにどっぷり浸かってしまう必要があり、API料金とかの兼ね合いで一旦見送った
	- あと多分、claude codeはある程度AIに委ねる印象があり、vibe codingむきなのかなぁとざっと思ってる。
- **aiderはMCPが現状未対応だけど開発中**で、もうちょい？で追加されそう
	https://github.com/Aider-AI/aider/pull/3672
- **AI webチャット(chatgpt等)との連携を効率化する機能がある**
	- コードの変更を考えさせるのをwebチャット側にやらせて、変更の作業はaiderにやらせるって思想で、その作業を効率化させる機能。
		- コードベース、aiderチャットに追加したコード、変更指示のプロンプトのコピペをりやすくすることで効率化する。
	- この機能が他のツールにはない(はず)で面白い機能だと思った。
	- **試してみた感じ結構良い感じに使えそうと感じている。**
- 知らないライブラリ、フレームワークについて自身が調査・学習したり、検討したりは(API料金を気にせず)chatgptを利用することが多いので、chatgpt plusに入っておきたい。が、cursorと両方契約はきつい。
	- 作業はコーディングより上記が多い
	- cursorは500回の制限があるので、この用途では利用しずらい。

**チュートリアルも作成してみた**ので、触ってみたい人はぜひ試してみてください。

## ✅ざっくりaiderの使い方3つのポイント

###  1️⃣ aiderは「スラッシュ`/`始まりのコマンド」を駆使して操作する
![利用例](/images/aider-beginner/IMG-20250502210045080.gif)

というわけで、以下コマンドはざっくり理解しておく。
よく利用しそうなコマンドは⭐️つけてます。
:::details コマンド一覧

|        | コマンド                    | 説明                                                               |
| ------ | ----------------------- | ---------------------------------------------------------------- |
|        | 【チャットモード】               | -                                                                |
| ⭐️     | `/ask <prompt>`         | コードベースに関する質問を行う（ファイルの編集なし）。                                      |
| ⭐️<br> | `/code <prompt>`        | コードの変更を依頼する。**公式推奨はask→code**で回す。(デフォルト)                         |
|        | `/architect <prompt>`   | 2つの異なるモデルを使用してアーキテクト(変更方法を思案し、提案)エディタ(ファイルへの編集)と段階を踏んでファイル編集する。  |
|        | 【コードベース】                | -                                                                |
| ⭐️<br> | `/ls`                   | コードベースに含まれている全てのファイルをリスト表示する。                                    |
|        | `/map`                  | 現在のリポジトリマップを表示する。                                                |
|        | `/map-refresh`          | リポジトリマップを強制的にリフレッシュする。                                           |
|        | 【チャット操作】                | -                                                                |
| ⭐️<br> | `/add <filepath>`       | ファイルをチャットに追加し、編集や詳細なレビューを可能にする。                                  |
|        | `/lint`                 | チャット内のファイルまたは未保存のファイルをリントして修正する。                                 |
| ⭐️<br> | `/read-only <filepath>` | 参照専用のファイルをチャットに追加するか、追加されたファイルを参照専用にする。コーディング規約を渡すときとかに使う。       |
| ⭐️<br> | `/drop`                 | チャットセッションからファイルを削除してコンテキストスペースを解放する。                             |
| ⭐️<br> | `/reset`                | すべてのファイルを削除し、チャット履歴をクリアする。                                       |
| ⭐️<br> | `/edit`                 | vimなどのエディタでプロンプトを書いてaiderに渡せる。                                   |
|        | `/multiline-mode`       | マルチラインモードを切り替える（EnterとMeta+Enterの動作を入れ替える）。                      |
| ⭐️<br> | `/paste`                | クリップボードから画像やテキストをチャットに貼り付ける。                                     |
|        | `/diff`                 | 最後のメッセージ以降の変更差分を表示する。                                            |
|        | `/web`                  | ウェブページをスクレイピングし、Markdownに変換してメッセージとして送信する。                       |
| ⭐️<br> | `/clear`                | チャット履歴をクリアする。                                                    |
|        | `/save`                 | 現在のチャットセッションのファイルを再構築できるようにコマンドをファイルに保存する。                       |
| ⭐️<br> | `/copy`                 | 最後のアシスタントメッセージをクリップボードにコピーする。                                    |
| ⭐️<br> | `/copy-context`         | 現在のチャットコンテキストをMarkdown形式でコピーする。                                  |
|        | 【コマンド】                  | -                                                                |
| <br>   | `/git`                  | Gitコマンドを実行する（出力はチャットに含まれない）。                                     |
|        | `/commit`               | チャット外で行った編集をリポジトリにコミットする。                                        |
|        | `/undo`                 | Aiderによって行われた最後のGitコミットを元に戻す。                                    |
| ⭐️<br> | `/run`                  | シェルコマンドを実行する。オプションでコマンドの出力結果をチャットに追加することもできる（`/run`ではなく、`!`でも化）。 |
|        | `/test`                 | シェルコマンドを実行し、非ゼロの終了コードの場合に出力をチャットに追加する。                           |
|        | 【設定系】                   | -                                                                |
|        | `/settings`             | 現在の設定を表示する。                                                      |
|        | `/context`              | まだよくわかってない。周囲のコードコンテキストを表示するコンテキストモードに入る。                        |
|        | `/model`                | メインモデルを新しいLLMに切り替える。                                             |
|        | `/models`               | 利用可能なモデルのリストを検索する。                                               |
|        | `/reasoning-effort`     | 推論努力レベルを設定する（値：数値またはモデルに応じてlow/medium/high）。                     |
|        | `/editor-model`         | エディターモデルを新しいLLMに切り替える。                                           |
|        | `/weak-model`           | 弱いモデルを新しいLLMに切り替える。                                              |
|        | `/think-tokens`         | 思考トークンの予算を設定する（例：8096、8k、10.5k、0.5Mなど）。                          |
|        | 【その他】                   |                                                                  |
|        | `/tokens`               | 現在のチャットコンテキストで使用されているトークン数を確認する。                                 |
|        | `/voice`                | 音声入力を録音して文字起こしする。                                                |
|        | `/help`                 | Aiderに関する質問を行う。                                                  |
|        | `/editor`               | `/edit`と一緒                                                       |
|        | `/chat-mode`            | 省略可能なため、使うことはない。新しいチャットモードに切り替える。                                |
|        | `/report`               | 問題を報告してGitHub Issueを開く。                                          |
|        | `/load`                 | ファイルからコマンドを読み込んで実行する。                                            |
|        | `/quit`                 | アプリケーションを終了する。                                                   |
| ⭐️     | `/exit`                 | アプリケーションを終了する。                                                   |
- 複数行のプロンプトを入力する4つの選択肢
	1. `{}`で囲う
	2.  `ESC-Enter`を入力して改行する
	3. `/multiline-mode`コマンドでEnterで改行、`ESC-Enter`でチャット送信に設定する
	4. `/edit`コマンドで別エディタを開いてプロンプトを作成して送信する
:::
### 2️⃣ 3つのチャットモードがある
1. ask
	- プロジェクトに関する質問など編集せずに指示したい場合のモード
2. code
	- ファイル編集など、変更を指示するモード。(デフォルトのモード)
3. architect
	- 2つの異なるモデルを使用して①アーキテクト(変更方法を思案し、提案)して、②エディタ(ファイルへの編集)と段階を踏んでファイル編集するモード
		- o3でアーキテクトして、gpt-4oでエディタするってことができる
	- まだ使ってない。金かかりそうだし。
	- [aider公式のリーダーボード](https://aider.chat/docs/leaderboards/)(どの用途でどのモデルが一番使われているかがわかる)によると、この**architectモードが一番使われている。**

- 補足：モードの詳細は以下。
	- [aider公式ドキュメントのチャットモードについて](https://aider.chat/docs/usage/modes.html)

### 3️⃣ aider公式推奨の開発進め方
- `ask`でコードの編集内容をAIと相談しながら確定させて、`code`で確定した内容を反映させる流れでやる。
- 編集が必要なファイルは必ずチャットに追加`/add <filename>`する必要がある(っぽい)。
	- 何もチャットにファイルを追加せずに編集を依頼すると追加を促される
	- ここが他ツールと違う特徴があるように感じた。
- 詳細はチュートリアル章でも触れるけど、ざっくりは以下の流れ
	1. `/ask`で編集したい機能を尋ねて、編集が必要なファイルを確認
		- `/ask XXXの機能はどのファイルでやってますか？`
	2.  `/add`コマンドで、1.で提示されたファイルをチャットに追加(1.の時点で追加を促されることもある)
		- `/add ZZZZZZ.js`
	3. `/ask`コマンドで、2.で追加したファイルについて機能追加コードを提示させる。
		- `/ask YYYの機能を追加するコードを提示してください。`
	4. `/code`編集させる(`/code`はデフォルトなので省略可能)
		- `先ほどの回答通り修正してください。`
### 補足：コードベース(repository-map)という概念について
- フォルダ構造やファイルを自動で解析し、プロジェクト全体の情報をAIのコンテキストとして活用
	- なので、**このディレクトリにこういうファイルがあるよとかは自分で渡さなくても自動である程度用意してくれている**
- デフォルトで1,000トークンに収まるように、重要な部分だけを取り出している
- 毎回LLMに渡される
- `/map`コマンドでどんな感じか確認できる。
- cursor等の他ツールにもこの概念はある
- こんな感じの情報だよ
	```text
	js/application.js
	js/bind_polyfill.js
	js/classlist_polyfill.js:
	⋮
	│  function DOMTokenList(el) {
	│    this.el = el;
	│    // The className needs to be trimmed and split on whitespace
	│    // to retrieve a list of classes.
	│    var classes = el.className.replace(/^\s+|\s+$/g, '').split(/\s+/);
	│    for (var i = 0; i < classes.length; i++) {
	│      push.call(this, classes[i]);
	│    }
	```

## 🛠️利用準備、設定について
### インストール、準備
基本は公式ドキュメントの手順を見てほしい。
以下はよく利用されるLLMプロバイダーopen router利用前提での例になっている。
現状open aiのAPIで利用してるが、open routerに乗り換え予定。

1. 任意LLMのAPIを利用するので、open routerなどを契約する
	-  無料のモデルもあります。

2. aiderをインストール
```shell
python -m pip install uv  # uvが入ってないなら
uv tool install --force --python python3.12 aider-chat@latest
```

3. apiキーを環境変数に設定する(`.env`も可)
```shell
export OPENROUTER_API_KEY=<key> 
```

4. (適当なプロジェクトのディレクトリ配下で)aiderを起動
```shell
aider --analytics-disable #aiderの分析情報収集を無効化。初回のみで良い。

# aider --model openrouter/<provider>/<model>
aider --model openrouter/openai/gpt-4o
```

- 補足：詳細は以下参照
	- [aider公式ドキュメント-aiderインストール手順](https://aider.chat/docs/install.html)
	- [aider公式ドキュメント-openrouterの設定手順詳細](https://aider.chat/docs/llms/openrouter.html)

### aiderの設定
`.aider.conf.yml`を作成して設定を記述する。
 カレントディレクトリ→リポジトリルート→ホームディレクトリとさかのぼって一番近いやつが採用される

- 重要そうな設定だけ載せておく
```yml
chat-language: japanese #aiderの言語指定
auto-commits: true  #コード編集後の自動コミットするかどうか
suggest-shell-commands: true  #コマンド実行時に許可を求めるかどうか
```

- その他の設定は以下を参照
	- [aider公式ドキュメント-設定ファイルについて](https://aider.chat/docs/config/aider_conf.html)

### コーディング規則の設定
以下2つの方法がある。

1. マークダウンに書いて参照用ファイルとして渡す。
	`/read-only markdown.md`

2. マークダウンに書いて、aider設定ファイル`.aider.conf.yml`に毎回読み込んで欲しいファイルを設定する
	- `*.md`が対応してるかは試さないとわからない
```yml
read:
  - CONVENTIONS.md
  - anotherfile.txt
  - thirdfile.py
```


## 🪄AI webチャット(chatgpt等)↔︎aiderの連携について

左：chatgpt、右：aider

![利用例](/images/aider-beginner/IMG-20250503120923193.gif)
### どういった機能？
- AI webチャット側(chatgpt等)でコードの機能追加方法を考えさせて、その結果をaiderに反映させることを効率化できる機能
- メリット
	- 機能追加案を無料or定額料金のAI webチャット側でやるので**API料金の節約**になる
		- 機能追加案などの難しいタスクはo3などの思考モデルに考えさせる
		- 変更の作業自体はdeepseekなどの安いモデルにやらせる
### やり方
1. aider     : 編集したいファイルを`add`で追加
2. aider     : `/copy-context <追加したい機能の指示>`を実行する
3. chatgpt : チャット欄にペーストする
	- 以下4つの情報がペーストされる
		- コードベース
		- addしたコード
		- aiderが付与するプロンプト
		- 2.で入力した<追加したい機能の指示>
4. chatgpt：chatgptの回答をコピー(すると、自動で回答がaiderチャットに貼り付けされる)
5. aider     : `/paste`を実行して、Enter

### 補足
- 指示の語尾に「日本語で回答してください。」ってつけるか、chatgptのシステムプロンプトで日本語を強制しないと回答が日本語にならないことがある。
- chatgpt + gpt-4oで試したところ、aiderの英語プロンプトと日本語が混じるせいかチャット回答がコード変更を提示してくれない時があった。思考モデルを利用すれば安定して回答してくれるので、**思考モデルの利用を推奨する。**
- aider起動時のオプションに`--copy-paste`を付与するとやり方の2.と5.の手順をaiderが自動でやってくれる。が、コピペ操作するたびに自動でaiderに貼り付けされる機能が意図せず発動するので、個人的には使いづらかった。
- aiderからchatgptに貼り付くプロンプトの日本語訳
```text
以下は私のGitリポジトリにあるファイルの概要です。  
これらのファイルには_変更を提案しないでください_。読み取り専用として扱ってください。  
編集が必要な場合は、_チャットに追加するように依頼してください_。

---
{codebase}
---

---
{/addしたコード}
---

変更を加えるためにファイルをどのように編集すればよいかだけを教えてください。
ファイル全体を返さないでください。  
必要な編集だけを示してください
```

## 🚀チュートリアル
適当なゲームアプリのリポジトリをクローンしてみて、aiderを使ってみよう。

1. OSSのゲームアプリリポジトリをcloneして、aiderを起動。
```shell
git clone https://github.com/gabrielecirulli/2048  
cd 2048  

aider
```

2. 始めて使うので、コードベースがどんな感じか見てみる。`/map`
```
> /map

Here are summaries of some files present in my git repository.
Do not propose changes to these files, treat them as *read-only*.
If you need to edit any of these files, ask me to *add them to the chat* first.
.gitignore

.jshintrc

CONTRIBUTING.md

LICENSE.txt

README.md

Rakefile

favicon.ico

index.html

js/animframe_polyfill.js:
⋮
│  if (!window.requestAnimationFrame) {
│    window.requestAnimationFrame = function (callback) {
│      var currTime = new Date().getTime();
│      var timeToCall = Math.max(0, 16 - (currTime - lastTime));
│      var id = window.setTimeout(function () {
│        callback(currTime + timeToCall);

--- 省略 ---
```

3. どんなプロジェクトか教えてもらう。`/ask`
```shell
> /ask このプロジェクトがどういうものか教えてください。

このプロジェクトは、2048というパズルゲームの実装のようです。JavaScriptを使用して、ゲームのロジックやUIの操作を行っています。以下に、プロジェクトの主要な要素をいくつか挙げます：

 1 ゲームロジック:
    • GameManagerクラスは、ゲームの主要なロジックを管理します。ゲームの開始、リスタート、タイルの移動、スコアの管理などを行います。
    • Gridクラスは、ゲームのグリッド（盤面）を管理し、タイルの配置や空きセルの管理を行います。
    • Tileクラスは、個々のタイルの位置や値を管理します。
 2 UI操作:
    • HTMLActuatorクラスは、HTMLを操作してゲームのUIを更新します。タイルの表示やスコアの更新、メッセージの表示などを行います。
    • KeyboardInputManagerクラスは、キーボード入力を管理し、ユーザーの操作をゲームに反映させます。
 3 データ保存:
    • LocalStorageManagerクラスは、ブラウザのローカルストレージを使用して、ゲームの状態やベストスコアを保存・取得します。
 4 ポリフィル:
    • animframe_polyfill.jsやclasslist_polyfill.jsなどのファイルは、古いブラウザでも新しい機能を使えるようにするためのポリフィルを提供しています。

このプロジェクトは、2048ゲームをブラウザ上で動作させるための完全な実装を含んでおり、ユーザーがキーボードで操作してタイルを動かし、同じ数字のタイルを合体させて2048のタイルを作ることを目指します。
```

4. 追加したい機能と編集するべきファイルについて確認。`/ask`
```shell
> /ask このゲームの採点方法を教えてください

ゲームの採点方法は、js/game_manager.jsファイル内で管理されています。

具体的には、GameManager.prototype.actuateメソッドでスコアが更新されます。
このメソッドでは、現在のスコアがベストスコアを超えている場合、storageManager.setBestScoreを使用してベストスコアを更新します。

また、ゲームが終了した場合にはゲームの状態をクリアし、終了していない場合には現在のゲーム状態を保存します。

スコア自体は、ゲーム内でタイルを動かしたり、合体させたりすることで増加します。
具体的なスコアの増加ロジックは、タイルの合体時に実行される処理の中に含まれていると考えられますが、詳細なロジックを確認するには、タイルの移動や合体に関するコードをさらに詳しく見る必要があります。
```

5. 機能を追加するファイルがわかったので、ファイルを追加 `/add`
```
> /add js/game_manager.js

Added js/game_manager.js to the chat
```

6. 追加したい機能の変更方法を尋ねる `/ask`
```
js/game_manager.js
> /ask 採点方法に多少のランダム性を加えることはできますか？例えば、10%の確率でユーザーに10倍のボーナスを与えるとか。変更箇所のみ提示してください。

はい、採点方法にランダム性を加えることは可能です。
例えば、タイルが合体したときに10%の確率でスコアに10倍のボーナスを与えるようにするには、GameManager.prototype.moveメソッド内でスコアを更新する部分にランダム性を追加します。

以下のように変更できます：

// Update the score
self.score += merged.value;

// Add random bonus
if (Math.random() < 0.1) { // 10% chance
  self.score += merged.value * 9; // Add 9 times the merged value as bonus
}

この変更により、タイルが合体するたびに10%の確率でスコアに10倍のボーナスが加算されます。
```

8. 変更方法が問題ないので変更を依頼する`/code`
	- デフォルトモードは`/code`なので省略化。
	- ファイル編集後は自動コミットされる。(設定でOFFにもできる)

```
js/game_manager.js
> その方法で良いので修正してください。

js/game_manager.js


<<<<<<< SEARCH
          // Update the score
          self.score += merged.value;
=======
          // Update the score
          self.score += merged.value;

          // Add random bonus
          if (Math.random() < 0.1) { // 10% chance
            self.score += merged.value * 9; // Add 9 times the merged value as bonus
          }
>>>>>>> REPLACE


           if (merged.value === 2048) self.won = true;
         } else {
```

9. 適宜以下を実施する
	- `/diff`を使って差分確認
	- `/clear`でこれまでのチャット履歴をクリアして、次の機能を作る
	- `/reset`でこれまでのチャット履歴、ファイルをクリアして、次の機能を作る

- 補足：その他の使い方
	- [aider公式ドキュメント-その他場面に応じたaiderの使い方例](https://aider.chat/examples/README.html)

### 補足
- AIへの指示でコマンド実行が必要と判断された場合は、コマンド実行も実施してくれる。この辺はcursorとかと一緒。(コマンド実行の際に許可を求められる。設定でOFFにもできる。)
```
> gitで一つ前にコミットした対応を元に戻してください。

gitで一つ前のコミットを元に戻すには、以下のコマンドを実行してください。


git revert HEAD


このコマンドは、最新のコミットを元に戻すための新しいコミットを作成します。これにより、履歴を保持しつつ変更を取り消すことができます。


Tokens: 6.7k sent, 106 received. Cost: $0.02 message, $0.02 session.

git revert HEAD
Run shell command? (Y)es/(N)o/(D)on't ask again [Yes]: y

Running git revert HEAD
[master 7f93f59] Revert "feat: add random bonus scoring with 10% chance on tile merge"
 1 file changed, 5 deletions(-)
Add command output to the chat? (Y)es/(N)o/(D)on't ask again [Yes]:
Added 96 lines of output to the chat.
You can use /undo to undo and discard each aider commit.
```

- chatgptとの連携で機能追加をする場合の流れ
	1. 機能を追加するファイルをチャットに追加 `/add`
	```
	> /add js/game_manager.js
	
	Added js/game_manager.js to the chat
	```
	2. webチャット貼り付け用に指示とチャットコンテキストをコピー `/copy-context`
	```
	> /copy-context スコアの採点方法を10%の確率でユーザーに10倍のボーナスを与える機能を追加してください。
	```
	 3. chatgptに貼り付けて、回答が完了したら回答をコピー(以下chatgptの回答)
	```
	思考時間: 数秒
	
	以下のように `js/game_manager.js` のスコア加算部分を差分パッチで修正してください。
	
	diff
	--- js/game_manager.js
	+++ js/game_manager.js
	@@ GameManager.prototype.move = function (direction) {
	-          // Update the score
	-          self.score += merged.value;
	+          // Update the score（10%の確率で10倍ボーナス）
	+          var bonusMultiplier = (Math.random() < 0.1) ? 10 : 1;
	+          self.score += merged.value * bonusMultiplier;
	+          if (bonusMultiplier > 1) {
	+            // ボーナス発生時のログ（必要に応じて UI 表示に変更可）
	+            console.log("★10×ボーナス獲得★");
	+          }
	
	```
	4. aiderに3.を貼り付けて、変更を反映させる

## 📝まとめ

![ベイダー](/images/aider-beginner/IMG-20250502211415009.png =300x)

最後まで読んでいただきありがとうございました。
需要がありそうなら、もう少し使ってみての感想みたいな記事も挙げてみようかなと思います。
