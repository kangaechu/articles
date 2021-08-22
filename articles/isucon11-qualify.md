---
title: "ISUCON11に参加しました"
emoji: "💺"
type: "tech"
topics: ["ISUCON"]
published: true
---

はじめてISUCON11予選に参加したので、その記録です。
Golangを選択し、結果は130位、33,750点でした。

## チーム

チーム名は「慎太郎」。
メンバーは [@yktakaha4](https://github.com/yktakaha4)、[@matchasong](https://github.com/matchasong)、そして[@kangaechu](https://github.com/kangaechu)の3人で参加しました。

ISUCONに参加したかったものの、仲間がいなかったので参加申し込み前週に@yktakaha4を他チームからひきはがし、別の勉強会で自分たちのISUCON過去問を解く様子をzoomで見ていた@matchasongをひきずりこみました。

おおまかな役割として、以下の割り振りで行いました。

- @yktakaha4 : アプリ
- @kangaechu : インフラ
- @matchasong : ベンチマーク実行、全体的な管理

## 準備

事前に4回くらい集まり、[ISUCON10予選](https://github.com/isucon/isucon10-qualify)、[ISUCON9予選](https://github.com/isucon/isucon9-qualify)を解きました。
MySQLのインデックスの貼り方から始めるレベルなので、MySQL5系は降順のインデックス無視されるんだーとか学びながら順々に進めていきました。

これと並行し、ベンチマーク実行前のログ取得を自動的に行うスクリプト群を作成しました。Nginxのログ解析には[alp](https://github.com/tkuchiki/alp)、MySQLのスローログ解析には[pt-query-digest](https://www.percona.com/doc/percona-toolkit/LATEST/pt-query-digest.html)を使用し、ベンチマーク開始スクリプト内で

- Nginx、MySQLログの削除
- git pull
- ビルド
- Nginx、MySQLの再起動
- ベンチマークの実行
- 実行後の点数をGitタグに追加
- 実行後のNgunx、MySQLスローログの解析結果作成
- Nginx、MySQLログをWebブラウザから見れるように、公開ディレクトリに移動
- スコアとログのURLをDiscordにPost

する処理を実行するようにしました。
これにより間違いやすい作業を自動化でき、予選でも役に立ちました。

![](https://storage.googleapis.com/zenn-user-upload/c1ec1cc7ec2bd12f82b6fb9b.png)

## 当日の様子

当日のスコアの様子は以下の通りです。

![](https://storage.googleapis.com/zenn-user-upload/ed0d769883dd8b6c35b5080e.png)

10時の競技開始後、CloudFormationの起動をしながら、ドキュメントを音読しました。アプリケーションの起動後、実際に画面をさわりながら実行されるAPIのパス・リクエスト・レスポンスを眺めていきました。

11時くらいにだいたいの仕様を理解できたので、@matchasongとベンチマークを流せるよう準備を行いました。サーバのファイルをGit管理し、@yktakaha4にコードを読んでもらいました。また、/etcもGitの管理下に移動し、変更を管理するようにしました。

12時に初のベンチマークを流し、2,066点となりました。
ベンチマーク準備ではDBサーバがMariaDBのため、設定をどこに入れればいいかに時間がかかり、12時すぎくらいまで時間がかかってしまいました。
ごはんを食べ、小休憩。

13時から午後開始。@yktakaha4がインデックスが効きそうなところに貼ってくれたため、点数は17,252点と大幅アップ。また、 `dropProbablityStr` を環境変数化し、env.shから変更できるようにしてくれました。自分斬作業をしつつ、与えられた3台のサーバを

- Nginx + WebAPサーバ（Webを処理）
- WebAPサーバ (JSIのPOST /api/condition/:jia_isu_uuid)を処理
- DBサーバ

に分離するための準備をしていました。

14時台はMariaDBをWebAPサーバとは別サーバに移動し、24,794点と7,000点アップ。また、`POST /api/condition/:jia_isu_uuid` も分離しましたが、27,324と2,000点程度の増加にとどまりました。@yktakaha4はDBへのアクセスしてる部分をメモリにキャッシュ。@matchasongは`dropProbablityStr`の適正値を探してくれました。

15時台はMariaDBのバージョンを最新に更新。スコアが上がるかと思いましたが、変更前と比較し2,000程度の増加にとどまりました。@yktakaha4は文字列比較を正規表現に置き換え、ログレベルの変更を実施。また、ログレベルの変更により+2,000点。

16時台はassetsをNginxから配信。スコアはあまり変わらず。is_dirtyを列として持つ修正をしていたのを手伝ってましたが、マージまでは辿り着けず。この時点で50位台。

17時台は再起動試験して終わりとしようとしたが、ポータルが死亡。その隙に@yktakaha4がバルクインサートを更新。最高で35,578点となった状態でおしまい。

## 振り返り

- ○ : 3台構成やアセットのNginx配信など、ここら辺は最低限やりたいと思っていたことはできた
- ○ : 楽しく完走することができた
- ○ : 事前にベンチの面倒なものを自動化しておいたのはよかった
- ✖︎ : コスパの良い改善を考えず、割と思いついたところからやってしまった
- ✖︎ : とにかく時間が足りない。もっと手が早く動かないと話にならない
- ✖︎ : ログを落ち着いてみる時間がなかった。改善の手法が掴めていたかも

また次回参加したいです！

