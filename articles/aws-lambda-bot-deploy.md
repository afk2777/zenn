---
title: "【無料】AWS lambdaで仮想通貨MLbotデプロイ手順" # 記事のタイトル
emoji: "👾" # アイキャッチとして使われる絵文字（1文字だけ）
type: "idea" # tech: 技術記事 / idea: アイデア記事
topics: ["BTC", "bot", "python", "lambda", "AWS"] # タグ。["markdown", "rust", "aws"]のように指定する
published: false # 公開設定（falseにすると下書き）
---

### デプロイの流れ
リポジトリの作成
```shell
aws ecr create-repository --repository-name lambda-crypto-bot --image-scanning-configuration scanOnPush=true --region ap-northeast-1
```

localのlambda関数呼び出し
```shell
d run -it --rm -v (pwd):/var/task --entrypoint "python" crypto-bot ./lambda.py
```

awsイメージの削除
  ```shell
aws ecr batch-delete-image --repository-name lambda-crypto-bot --image-ids imageTag=latest
```


docker imageをpush
```shell
docker build -t crypto-bot:latest .
docker tag crypto-bot:latest 709884460913.dkr.ecr.ap-northeast-1.amazonaws.com/lambda-crypto-bot:latest
aws ecr get-login-password | docker login --username AWS --password-stdin 709884460913.dkr.ecr.ap-northeast-1.amazonaws.com
d push 709884460913.dkr.ecr.ap-northeast-1.amazonaws.com/lambda-crypto-bot:latest
```

aws image 確認
```shell
aws ecr list-images --repository-name lambda-crypto-bot
```

sls deploy
```shell
  sls deploy
```

sls remove
```shell
  sls remove
```

[AWS lambda](inkjkrop://note/0GkFHM_ka)

## 　用語メモ
統合証拠金　いろんな通貨を合算して信用証拠金にできる仕組み

## testnet-api
[Bybit API Docs](https://bybit-exchange.github.io/docs/spot/v1/#t-tradehistory)

## ccxt how to
[【Python】ccxtを活用した仮想通貨・ビットコイン取引の自動売買｜板情報・価格・注文・約定APIの利用](https://di-acc2.com/programming/python/4599/)

## pybotters
[Getting Started — pybotters  ドキュメント](https://pybotters.readthedocs.io/ja/latest/Home.html#rest-api)
[botterのためのasyncio](https://zenn.dev/mtkn1/articles/c61e77c1d221aa)

## ta-lib
conda install -c conda-forge ta-lib

[M1 Macのdocker環境にテクニカル指標計算ライブラリ「TA-Lib」をインストールする方法 - ギークなエンジニアを目指す男](https://www.takapy.work/entry/2021/12/19/163441)

curl "https://api-testnet.bybit.com/v2/private/wallet/balance?api_key=IXUqCfaE2NC02L4066&coin=BTC&timestamp=1658145905&sign=tcGKMxAm0xBuckbP2bHelXGrLQywY9UHxI1F"
curl "https://api-testnet.bybit.com/private/linear/position/list?api_key=IXUqCfaE2NC02L4066&symbol=BTCUSDT&timestamp=16581514505819&sign=tcGKMxAm0xBuckbP2bHelXGrLQywY9UHxI1F"


## 終わったタスクの置き場
* apiが正しく動くか確認 デリバティブ
  - [x] 全注文のキャンセル
  - [x] 指定数,インターバルのローソク足を取得
  - [x] 注文
    - [x] 信用買注文
    - [x] 信用売注文
  * [x] ポジション情報の取得
* bybit取引classにpybotterで実装する
  - [x] 全注文のキャンセル
  - [x] 指定数,インターバルのローソク足を取得
  - [x] 注文
  - [x] ポジション情報の取得
* デリバティブ番のモデルを作成する
  - [x] adamさんのipyenvをもってくる
  - [x] testネットのローソク速を2020から取得できるようにする自分のpybotterクラスを使って
  - [x] ipythonのグラフが文字化けする、sixelだ真とっ黒で表示されない問題応対
  * [x] デリバティブ番のモデルを作成する
- [x] richman_strategy.pyを今の取引モデル動作用に修正する
- [x] pyprojectにpybottersを追加して問題なくdockerbuildが行くか確認する
- [x] リッチマンストラテジーがうまく動作するか動かしてみる
- [x] lambda、ecs、ecr料金を調べる
(512 / 1024) *　24 *　60 *　31 / 5　*　10
[ざっくりAWS - Lambdaの料金を日本円で計算 ](https://aws-rough.cc/lambda/)
printぷんのログ見れる？
[AWS Lambdaのデバッグ方法 - Qiita](https://qiita.com/keitakn/items/02fcee5bbb037bbd2e41)
- [x] サーバーレス動作確認、デプロイ方法を整理以下を読む
[Deploy Lambda from container image with Serverless Framework | by Deepika Khalarka | AWS Tip](https://awstip.com/deploy-lambda-from-container-image-with-serverless-framework-98bdddc1f9db)
[[AWS] Serverless FrameworkでLambdaをArm64対応する](https://blog.katsubemakito.net/aws/lambda-with-serverless-framework-arm64)
[Serverless Framework Commands - AWS Lambda - Deploy Function](https://www.serverless.com/framework/docs/providers/aws/cli-reference/deploy-function)
[Serverless Framework: Plugins](https://www.serverless.com/plugins/serverless-package-python-functions)
- [x] serverless.ymlの時間契機を設定する
サーバーレスlambda関数コール契機設定
[Serverless Framework - AWS Lambda Guide - Serverless.yml Reference](https://www.serverless.com/framework/docs/providers/aws/guide/serverless.yml)
- [x] サーバーレスコンテナが正しく動作するか確認する
- [x] 以下エラーを回避する
環境変数をDockerfileに追加することで回避
/var/lang/lib/python3.8/site-packages/joblib/_multiprocessing_helpers.py:46: UserWarning: [Errno 38] Function not implemented.
joblib will operate in serial mode
warnings.warn('%s.  joblib will operate in serial mode' % (e,))
- [x] loading functionが毎回呼ばれる
1分周期で15分間隔以外は

[Bybit](https://testnet.bybit.com/app/user/security)
* [x] apiコードを再発呼する
[Bybit](https://testnet.bybit.com/app/user/api-management)
