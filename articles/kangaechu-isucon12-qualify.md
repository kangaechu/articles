---
title: "チーム慎太郎でISUCON12に参加しました"
emoji: "💺"
type: "tech"
topics: ["ISUCON"]
published: true
---

[ISUCON11予選](https://zenn.dev/articles/kangaechu-isucon11-qualify)に引き続き、2度目の参加をしました。
Golangを選択し、結果は209位、4,703点でした。


## チーム

チーム名は「慎太郎」。
メンバーは [@yktakaha4](https://github.com/yktakaha4)、[@matchasong](https://github.com/matchasong)、そして[@kangaechu](https://github.com/kangaechu)の3人で参加しました。

メンバーは前回と同様、前職の同僚です。

おおまかな役割として、以下の割り振りで行いました。

- @yktakaha4 : アプリ
- @kangaechu : インフラ
- @matchasong : ベンチマーク実行、インデックス付与

## 準備

事前に8回くらい集まり、[ISUCON11予選](https://github.com/isucon/isucon11-qualify)を解き直していました。
また、個人で[ISUCON10予選](https://github.com/isucon/isucon10-qualify)の問題を解いていました。

これと並行し、前回作成したベンチマーク実行前のログ取得を自動的に行うスクリプト群を改善していました。Nginxのログ解析には[alp](https://github.com/tkuchiki/alp)、MySQLのスローログ解析には[pt-query-digest](https://www.percona.com/doc/percona-toolkit/LATEST/pt-query-digest.html)、システムのモニタリングに[Netdata](https://www.netdata.cloud/)を使用しました。

- Nginx、MySQLログの削除
- git pull
- ビルド
- Nginx、MySQL、Redisの再起動
- ベンチマークの実行
- 実行後の点数をGitタグに追加
- 実行後のNgunx、MySQLスローログの解析結果作成
- Nginx、MySQLログをWebブラウザから見れるように、S3に保管
- スコアとログのURLをDiscordにPost

する処理を実行しています。
前回同様、当日のコミュニケーションと事後のログ取得に非常に便利でした。
![](https://storage.googleapis.com/zenn-user-upload/3c3054b4bd57-20220724.png)

また、アプリケーションサーバをまたいだKVSがあったらいいのではということでRedisを準備しました。
遅延処理的なものも使うかも、ということでバッチ処理的な機能も準備していました。

## 当日の様子

当日のスコアの様子は以下の通りです。最終スコアは4,703点ですが、試合中の最高点は9,067点でした。

![](https://storage.googleapis.com/zenn-user-upload/ffb00caf42d4-20220724.png)


セットアップ完了の様子。

![](https://storage.googleapis.com/zenn-user-upload/9e88b8e93e6f-20220727.jpg)

10時の競技開始後、CloudFormationの起動をし、各サーバのセットアップを進めていました。
2人が当日マニュアルを読んでいるのを横目で見ながら作業を進めていたので、仕様を理解しきれていなかったかもしれません。
その後、2人がコードを読みながらコメントを追加していたので、セットアップの完了後に途中から参加しました。

11:20に初のベンチマークを流し、2,827点。
ごはんを食べ、小休憩。

![](https://storage.googleapis.com/zenn-user-upload/f9fa052e9fc3-20220727.jpg)

午前中での自分の感想としてはこんな感じでした。

- SQLite3がやっかい
  - 知見がないためパフォーマンス改善の手法がわからない
  - インデックスを貼ってもパフォーマンス改善するのかよくわからない
  - MySQLに持って行った方が楽なのではないか
  - tmpfsに載せてディスクのI/Oを早くするくらいなのでは？
- MySQLのスロークエリを見たが、改善が効きそうなところがあまりなさそう
- コードは改善できそうなところがあるが、SQLite3のMySQL化で前提が変わりそう

そのため、まずはtmpfsに載せるのとSQLite3のMySQL化を試すことにしました。
@yktakaha4 に「MySQL化は罠ではないか」と言われたのですが、これが後で現実になるとは...

tmpfsへの変更は簡単にできたので12:48にスコア計測。2964とあまり上がらなかったのでひとまずリバート。自分はSQLite3のMySQL化を試すことに。スクリプトあるからいけるやろと思ってました。

14時台には @yktakaha4 がid生成を[xid](https://github.com/rs/xid)に変更。4017点と結構上がりました。tenantsAddHandlerにトランザクションを貼るようにしたがスコアはそこまで変わらず。 @matchasong もそれらしいところにインデックスを張ってくれましたがスコアが思うように上がらず。

15時台に自分がやっていたSQLiteのクエリをMySQLに変更できたのですが、POST /initialize時にサーバが死亡。慌てて2台目を使用するように変更しました。ここからCSVのインポートを試したりmysqldumpを試したりしたのですが、件数が多く、30秒に間に合わず苦労していました。

16時台は @yktakaha4 がplayersAddHandlerで変数をもとに値を埋める処理に書き換え4474点。自分はMySQL化を諦め始めていました。

17時台にもう一度tmpfsを試したところ、なんと9067点。今までの倍のスコアに沸き立ちましたがなぜか再起動するとOSが立ち上がらず色々調べている間に時間切れ。最高点は4782点でした。

## 振り返り

- ○ : 前回より下がったが、そこそこの順位につけることができた
- ○ : 楽しく完走することができた
- ○ : 事前準備のおかげで前半はスムーズ
- ✖︎ : コードに手をつけることができなかった。準備段階から会話に加わっておけばよかった
- ✖︎ : SQLite3のままいけばよかった。移行の諦めポイントを先に決めておけばよかった
- ✖︎ : tmpfs設定後のOS再起動失敗が謎。もう少し早く手をつけられればよかった。

楽しかったのでまた次回も参加したいです！

