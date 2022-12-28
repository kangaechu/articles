---
title: "IRKitを延命する"
emoji: "💓"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["irkit", "iot"]
published: false
---

2022年12月11日から使えなくなったIRKitを延命処置する方法です。

## 目的

IRKitは2014年に発売されてから8年が経過しました。
開発者から2022年11月末にIRKitのサーバが停止し、サービスを終了するとのお知らせがありました。
IRKitがサーバに接続できなくなると、ローカルのみの使用もできなくなるとのことです。
https://maaash.jp/2022/04/deprecating-irkit/

自宅では寒い日にリビングのエアコンをONにするために使われており、今年も使用する予定だったのでどうしようかと思ってました。
ただ、お知らせに修正したファームウェアを書き込むと継続して使用できるそうなので、作業の記録をまとめてみました。

:::message
以前は https://github.com/irkit/device/pull/6 を使用する方法としていましたが、追加の設定が必要だったため自分のリポジトリにforkしたものを作成しました。
:::

## 環境

- Macbook Air 2020 (Apple M1)
- macOS: Ventura 13.1

## 準備するもの

- IRKit
- USB Micro-Bケーブル

## カスタムファームウエアのインストール作業

### Arduinoのインストール

カスタムファームウェアをインストールするために、Arduinoの開発環境をインストールします。

```shell
$ brew install --cask arduino
```

Arduinoを起動し、エディタが起動すればOKです。
インストール時点でのArduinoのバージョンは`1.8.19`でした。

### Macと接続

USB Micro-Bケーブルを使用し、IRKitとMacを接続します。

接続するとキーボード設定アシスタントが立ち上がりますが、「終了」を選択します。

### IRKitのカスタムファームウェアのソースコードを取得

ターミナルから以下のコマンドを実行し、ソースコードを取得します。

```shell
git clone https://github.com/kangaechu/device
cd device
```

### Arduinoで開く

Finder、またはコマンドプロンプトからArduinoのプロジェクトファイルである `firmware/src/IRKit/IRKit.ino` を開きます。

コマンドプロンプトで開く場合は、以下のように実行します。

```shell
open firmware/src/IRKit/IRKit.ino
```

### Arduinoの設定

IRKitへの書き込みのため、以下の設定を行います。

- `ツール→ボード→Arduino Leonardo` を選択します。
- `ツール→シリアルポート→/dev/usbmodemxxxxx` (xxxxxは任意の数字)を選択します。

![`ツール→ボード→Arduino Leonardo` を選択](https://storage.googleapis.com/zenn-user-upload/67b31640e4e1-20221217.png)
![`ツール→シリアルポート→/dev/usbmodemxxxxx` (xxxxxは任意の数字)を選択](https://storage.googleapis.com/zenn-user-upload/c21e2322f607-20221217.png)

### コンパイル

エディタの左上にある✅ボタン（検証）を押し、プログラムをコンパイルします。
緑色の部分に「コンパイルが完了しました」と表示されれば成功です。
赤字のメッセージが出ますが問題ありません。

![エディタの左上にある✅ボタン（検証）を押し、プログラムをコンパイル](https://storage.googleapis.com/zenn-user-upload/56277ab8b6fb-20221217.png)

### IRKitへの書き込み

次にエディタの左上にある➡️ボタン（マイコンボードに書き込む）を押し、プログラムをIRKitに送信します。
緑色の部分に「ボードへの書き込みが完了しました」と表示されれば成功です。
赤字のメッセージが出ますが問題ありません。

![IRKitへの書き込み](https://storage.googleapis.com/zenn-user-upload/8641f7fadc8c-20221217.png)

書き込み後にLEDが赤→緑→水色→青→消灯すれば成功です。お疲れ様でした。

### Wi-Fi設定を書き込み

書き込み後にLEDが赤点滅している場合、IRKitのWi-Fi設定が削除されているため、以降の手順が必要となります。
サービス停止以前はWi-Fi設定をIRKitシンプルリモコンアプリで書き込むことができましたが、現時点ではエラーになります。

そのため、Wi-Fi設定を手動で書き込みます。

#### Wi-Fi設定から送信する文字列を作成

IRKitに書き込むWi-Fi設定は文字列をエンコードしたものなので、変換する必要があります。
手元で変換するのは難しいので、[IRKit Setup Guide](https://www.adriancourreges.com/projects/irkit-web-remote/live/public/setup.html) の力を借ります。

`4. Tell IRKit to join your home wifi network` のフォームにIRKitが接続したいSSIDとパスワードを入力します。

- 暗号化方式: WPA / WPA2 Encryption
- Device Key: （入力しない）

![](https://storage.googleapis.com/zenn-user-upload/3249a26608e0-20221217.png)

#### 作成した文字列を取得

メニューから表示→開発 / 管理→デベロッパーツール を開きます。

![](https://storage.googleapis.com/zenn-user-upload/bb6df90032df-20221217.png)

ネットワークを選択します。
Send to IRKitを押します。`An error occured while sending the request!` というメッセージが出ますが問題ありません。
デベロッパツールのwifiを右クリック→copy→cURLとしてコピーを選択します。

![](https://storage.googleapis.com/zenn-user-upload/06ad4ae72ca8-20221217.png)

#### MacをIRKitのWi-Fiに接続

IRKitのWi-Fiを設定するため、IRKit設定用のWi-Fiに接続します。
![](https://storage.googleapis.com/zenn-user-upload/2e969e7ef64e-20221217.png)

Mac右上のWi-Fiアイコンから、IRKitxxxx（xxxxは機種により異なる）を選択します。
パスワードは`XXXXXXXXXX`です。IRKitに同梱されたパスワードとは異なります。
![](https://storage.googleapis.com/zenn-user-upload/8eaa5a1d318b-20221217.png)

#### IRKitのWi-Fiを設定

先ほどコピーした文字列をテキストエディタに貼り付けします。

```shell
curl 'http://192.168.1.1/wifi' \
  -H 'Accept: */*' \
  -H 'Referer;' \
  -H 'Content-Type: application/x-www-form-urlencoded; charset=UTF-8' \
  --data-raw '8/4E414E4B412D4E4F2D53534944/70617373776F7264//2//////1A' \
  --compressed
```

上記のような形になっていますが、不要なヘッダやオプションを削除し、以下のように書き換えます。

```shell
curl -v -H 'X-Requested-With: curl' 'http://192.168.1.1/wifi' \
  --data-raw '8/4E414E4B412D4E4F2D53534944/70617373776F7264//2//////1A'
```

書き換えたらターミナルにコマンドを貼り付けて実行し、IRKitのWi-Fiを設定します。
書き込み後にLEDが赤→緑→水色→青→消灯すれば成功です。お疲れ様でした。

## まとめ

IRKitのインターネット機能がなくなったため、インターネットからIRKitを使用した家電の操作はできなくなりましたが、Raspberry Piなどを使用して同じWi-Fiの中から操作をすることは可能です。また、インターネットとうまく連携することも可能です。

