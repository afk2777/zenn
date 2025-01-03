---
published: true
topics:
  - LLM
  - ローカルLLM
  - macos
type: idea
emoji: 💻
title: 2025年さらに熱くなるであろうローカルLLMのためのm4mac購入検討
---

俺が好きなmacでローカルLLMを触れるってことと、来年さらにローカルLLMが熱くなる(はず)ということでm4チップが発売されたことを契機に購入を検討した。
＃現状、m1 mac book air 16GB所持で、小さめのモデルでも手元で動かすにはちょっと心もとない。

この記事は検討の過程を共有して、誰かの参考になればと思って公開する。

## 結論
色々悩んだが、整理してみると俺は**m4 mac book airのメモリ24GBを待つ**ことにした。(年末セールのm3 mac book airもちょっと候補。)

mac book proも悩んだが、持ち運びが多いので重さ、バカ高いPCを持ち運ぶの怖いという観点で諦めた。
一旦mac book airのみで推論ベースで遊んでみて、自分でもモデルを学習してみたいとかでてきたときにm4 mac miniのm4pro-48GBを追加導入しようかなと思う。

## ローカルLLMのためにmacを買うモチベを整理
### ローカルLLMの将来性、需要
- クラウドにデータを上げたくないなどのセキュリティに対する需要
- ドメイン特化LLM：特定の分野に特化し精度が高いモデルを作りたいという需要
	- モデルパラメータサイズが小さい事によるカスタマイズ性の高さを活かした取り組み
	- [ローカルLLMに関する松尾研究所の記事](https://zenn.dev/mkj/articles/c41d81f9f4ecd5)
	- [元松尾研究所今井さんの言葉-パーソナルAI](https://www.youtube.com/watch?v=n3S8eVHkzvY)

### macで動かす魅力
- macでLLMを動かすという面白さ、夢
- ユニファイドメモリによるgpuへのデカいメモリ割当
	- コンシューマ向けGPUは24GBが最大で動作可能なモデルが限られる
	- 次期モデル5090とかでも32GBとからしい。100万？とかの噂。
- 導入の手軽さ
	- gpuとか、箱とか準備したくない。デカい。
- macに最適化された機械学習フレームワークの登場と期待：MLX
	- ollamaに比べ、ローカルLLMの推論が速いらしい。
		- MLXに対応した形式のモデルを実行できる
			- ggufから変換が可能。
			- mlxのコミュニティで配られてる。
	- LoraなどのファインチューニングもMLXでできる。
	- ollamaもいずれmlxサポートしてくれる？の噂もあり

## メモリサイズをどうするか？
どの`パラメータサイズ`と`量子化サイズ`のローカルLLMを動かしたいかで決める。

俺は32Bの4bit量子化をターゲットにすることにした。
ので、メモリは24GBか32GBを選ぶことになる。

- パラメータサイズ32Bを選ぶ理由
	- qwen2.5-corderであるコーディング特化モデルが32Bで、今後ドメイン特化モデルが32Bクラスで展開されるのかなぁという予想。
	- 技術進歩で2025年に70Bレベルの精度を30Bレベルで出せるとかになってくれることを期待。

### ローカルLLMのパラメータサイズ x 量子化bit数に応じたメモリサイズ目安

| パラメータサイズ \ 量子化bit数  | Float16 (16bit) | 8bit量子化 | 4bit量子化 |
| ------------------- | --------------- | ------- | ------- |
| **7B (70億パラメータ)**   | 約16.8GB         | 約8.4GB  | 約4.2GB  |
| **14B (140億パラメータ)** | 約33.6GB         | 約16.8GB | 約8.4GB  |
| **32B (320億パラメータ)** | 約76.8GB         | 約38.4GB | 約19.2GB |
| **72B (720億パラメータ)** | 約172.8GB        | 約86.4GB | 約43.2GB |

[参考にしたメモリサイズの計算式](https://www.substratus.ai/blog/calculating-gpu-memory-for-llm/?utm_source=chatgpt.com)

- Loraとかで学習するなら上記の倍はあれば学習にも手を出せると思う。
- ちなみに、、
	- 正直、俺がターゲットにしているパラメータサイズ x 量子化サイズだとapple信者じゃでなければコンシューマ向けGPUを積んだPCを組むか買うかしたほうが良いとは思う。
	- なぜなら、コンシューマ向けGPUでも4090を選べばVRAMが24GBあり、32Bの4bit量子化までは動作させられるし圧倒的に速いし、macのユニファイドメモリのメリットが享受できないためである。

## 処理スピードをどこまでもとめるか？

### LLMの性能について
性能については**入力トークンを処理するスピード**と、**出力トークンを生成するスピード**の２つを考える必要がある。
- **入力トークンを処理するスピード(PP：プロンプト評価速度)**
	- gpuの性能 + gpuコア数が多いほど比例して早くなる
	- LLMに指示を入力してから、**最初の文字が出力されるまでの時間**に関わる
		- PPが速ければ最初の文字が出力されるまでの時間が短くなる
- **出力トークンの生成するスピード(TG：テキスト生成速度）**
	- 基本的にメモリ帯域幅が大きいと比例して速くなる
	- LLMが文字を**出力し始めてから、最後の文字を出力するまでの時間**に関わる
		- TGが速ければ文字が全部出力されるまでの時間が短くなる


### m4 mac book proの性能と価格の整理

| Chip   | メモリ  | 可モデル     | gpu Cores | BW [GB/s] | PP [t/s] | TG [t/s] | 価格       |
| ------ | ---- | -------- | --------- | --------- | -------- | -------- | -------- |
| M4     | 24GB | 32B-4bit | 10        | 120       | 221.29   | 24.11    | 308,800円 |
| M4     | 32GB | 32B-4bit | 10        | 120       | 221.29   | 24.11    | 338,800円 |
| M4 Pro | 24GB | 32B-4bit | 16        | 273       | 364.06   | 49.64    | 358,800円 |
| M4 Pro | 48GB | 32B-4bit | 16        | 273       | 364.06   | 49.64    | 418,800円 |
| M4 Pro | 24GB | 32B-4bit | 20        | 273       | 439.78   | 50.74    | 398,800円 |
| M4 Pro | 48GB | 72B-4bit | 20        | 273       | 439.78   | 50.74    | 418,800円 |
| M4 Max | 32GB | 32B-4bit | 32        | 410       | 752.91   | 70.37    | 528,000円 |
| M4 Max | 48GB | 32B-4bit | 40        | 546       | 885.68   | 83.00    | 603,800円 |

- mac miniはProチップまで選べてmac book proと同じ構成で-10万程度で手に入る。

## まとめ
一緒に来年macでローカルLLMしましょう。
正直m4 mac miniをもう買ってしまうかかなり悩んでる。

## 参考情報
 ### mac、ローカルLLMの購入に関する記事
https://zenn.dev/robustonian/articles/selection_of_gpus_for_local_llm
### M2 vs M4の動画
https://www.youtube.com/watch?v=bEsjaXWo9go&t=7s

| 項目                  | M2チップ      | M4チップ    |
| ------------------- | ---------- | -------- |
| 入力トークン              | 718トークン    | 718トークン  |
| **ファーストトークン応答時間**   | 約15.19秒    | 約7.4秒    |
| **全体プロセス時間**        | 約1分28秒     | 54秒      |
| **出力時間 トークン/秒**     | 約6.5トークン/秒 | 約9トークン/秒 |
| **プロンプト評価速度 (tps)** | 約47 tps    | 約97 tps  |
|                     |            |          |
### llama 7Bモデルの各Mチップ性能実測値まとめ
https://github.com/ggerganov/llama.cpp/discussions/4167
- macでは量子化するとスピードが若干落ちてる

### ↑をグラフにしてくれたやつ
https://llm-speed-viz.vercel.app

### mlx_lmについて
https://zenn.dev/michy/articles/041d0db2ae9895
https://qiita.com/asamiKA/items/3fdf89771084e3625643
