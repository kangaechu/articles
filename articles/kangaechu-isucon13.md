---
title: "チーム慎太郎でISUCON13に参加しました"
emoji: "💺"
type: "tech"
topics: ["ISUCON"]
published: false
---

[ISUCON12予選](https://zenn.dev/articles/kangaechu-isucon12-qualify)に引き続き、3度目の参加をしました。
Golangを選択し、結果はxxx位、xxxx点でした。


## チーム

チーム名は「慎太郎」。
メンバーは [@yktakaha4](https://github.com/yktakaha4)、[@matchasong](https://github.com/matchasong)、そして[@kangaechu](https://github.com/kangaechu)の3人で参加しました。

メンバーは前回と同様、前職の同僚です。

おおまかな役割として、以下の割り振りで行いました。

- @yktakaha4 : アプリ
- @kangaechu : インフラ
- @matchasong : ベンチマーク実行、インデックス付与

## 準備

事前に4回くらい集まり、[ISUCON11予選](https://github.com/isucon/isucon11-qualify)を解き直していました。
自分はあまり気持ちが乗っておらず、ようやくやる気になったのは前週くらいから。スタートが遅いです。

前回も役に立ったベンチマーク実行前のログ取得を自動的に行うスクリプト群を改善していました。
大きな変更はpprofを取得するようにしました。

![](https://storage.googleapis.com/zenn-user-upload/1a703c1306f8-20231126.png)
*pprof*

また、初期セットアップを早く終わらせることができるように修正しました。

ベンチ前後には以下のような処理を行っています。

- Nginx、MySQLログの削除
- git pull
- ビルド
- Nginx、MySQL、Redisの再起動
- pprofの実行
- ベンチマークの実行
- 実行後の点数をGitタグに追加
- 実行後のNgunx、MySQLスローログの解析結果作成
- Nginx、MySQLログをWebブラウザから見れるように、S3に保管
- スコアとログのURLをDiscordにPost

前回同様、当日のコミュニケーションと事後のログ取得に非常に便利でした。

![](https://storage.googleapis.com/zenn-user-upload/c595f592a6e0-20231126.png)
*Discordでの実行結果表示*


また、[GitHub Copilot](https://github.com/features/copilot)を準備したのも大きな変更点です。
ググらないとコードを書けないので、やりたいことをコメントに書けばそこそこのコードを書いてくれるCopilotは非常に便利でした。

## 当日の様子

当日のスコアの様子は以下の通りです。最終スコアは4,703点ですが、試合中の最高点は9,067点でした。

![](https://storage.googleapis.com/zenn-user-upload/00b224642d03-20231126.png)
*スコア*

10:00
10時の競技開始後、CloudFormationの起動をし、各サーバのセットアップを進めていました。
2人が当日マニュアルを読んでいるのを横目で見ながら作業を進めていたので、仕様を理解しきれていなかったかもしれません。
その後、2人がコードを読みながらコメントを追加していたので、セットアップの完了後に途中から参加しました。

10:50に初のベンチマークを流し、3,670点。

11:00
yktakaha4はコードの改善できそうなところにコメントを入れてくれ、matchasongはインデックスを見てくれました。

12:00
ごはんは今回はお弁当でした。寒かったのでスープを買って行ったが温まって良かった。
matchasongがインデックスを張ってくれて点数が7,021点に上昇。
自分はDBを2台目に分離し、点数が10,379点に上昇。

13:00
matchasongがインデックスを張ってくれて点数が12,018点に上昇。

自分はインフラの設定が終わったのでアイコンのハッシュ計算に手をつけてました。
リクエストも多い `GET /api/user/:username/icon` が毎回アイコンのハッシュ計算を行っているのを修正し、Nginxから画像を返せば点数があがりそうという見込みを立てました。
ただ、Nginxの設定がはまらずここから苦戦してたのでコードを修正すべきだったか。

14:00
yktakaha4がランキングのN+1を修正してくれて13,400点にアップ。

ここら辺で上位ランクに載ってて興奮（儚い夢でした）

![](https://storage.googleapis.com/zenn-user-upload/729b6e1161d8-20231126.png)
*上位ランク*

15:00
yktakaha4がアプリからIf-None-Matchを返す実装を入れてくれ、14717点に。

16:00
matchasongは `POST /api/livestream/:livestream_id/livecomment/:livecomment_id/report` のロックを修正するが点数が伸びず、revert。
自分はNginxを諦め、ハッシュ計算をDBに格納するところに着手。
しかし、格納したハッシュを返すところでエラーが発生。直せず。
yktakaha4は `POST /api/livestream/:livestream_id/moderate` のN+1を修正したがスコアが伸びず。

17:00
ログレベルをOFFに変更したり、MySQLのスローログ設定を元に戻したり。
17:30に最終スコアが14,717点出たのを確認し、そこで打ち止めとしました。

試合後はは美味しいジンギスカンを食べながらピエール瀧の体操42歳を見る楽しい会になりました。

![](https://storage.googleapis.com/zenn-user-upload/26fa00ef76df-20231126.jpg)
*ジンギスカンおいしい*

## 振り返り

- ○ : 前回より下がったが、そこそこの順位につけることができた
- ○ : 楽しく完走することができた
- ○ : 事前準備のおかげで前半はスムーズ
- ○ : matchasongの成長が楽しみ
- ○ : GitHub Copilot便利
- ○ : アプリ側のコードに手をつけることができた
- ✖︎ : 3台中2台しか使っていなかったのでその設定を先にすれば良かった
- ✖︎ : アプリ側の自分の修正がスコア改善につながらなかった
- ✖︎ : アプリのスコア改善が相変わらずyktakaha4頼み

楽しかったのでまた次回も参加したいです！

