---
title: "M5Paper + PlatformIO + Arduino でHelloWorld"
emoji: "🗒"
type: "tech"
topics: ["M5Paper", "PlatformIO", "Arduino"]
published: true
---

先日ひょんなことで手に入れた[M5Paper](https://www.switch-science.com/catalog/6749/)。
e-inkで電池もよく持ちそうなので家の温度とかを表示したい。
開発環境を整えるだけでものすごい大変だったのでHelloWorldできるまでをまとめました。

## 環境

- Macbook Air (M1, 2020)
- macOS Big Sur 11.2.3
- Visual Studio Code 1.55.0 Darwin arm64
- M5Paper

## 手順

### ドライバのインストール

作成したプログラムをM5Paperに送るため、ドライバが必要になります。

[M5Stack](https://m5stack-store.myshopify.com/pages/download) のページに移動します。
CP2104 Driverの「Download」をクリックするとプラットフォームが選べるので、動かしている環境を選択し、ドライバをダウンロードします。

![](https://storage.googleapis.com/zenn-user-upload/nily67bcszp06up6ylvs4yslu27u)

ダウンロードしたドライバを実行し、インストールを実施します。

### PlatformIOのインストール

Arduinoの開発環境として、近年よく使われる[PlatformIO](https://platformio.org/)をインストールします。

公式サイトによると、PlatformIOは以下の特徴があります。

- クロスプラットフォーム
- 多くの異なるソフトウェア開発キット（SDK）またはフレームワークをサポート
- 高度なデバッグ（Debugging）
- 単体テスト（Debugging）
- 自動コード分析（静的コード分析）
- リモート管理（リモート開発）

PlatformIOはVisual Studio Codeのプラグインとして提供されています。

早速インストールしましょう。まずはVisual Codeを起動します。

![m5_01.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/36587/83d95a93-c9cc-6712-b9a1-f95e6e122377.png)


1. 左側四角のアイコン「Extensions」を選択
2. 検索バーから「platformio」で検索
3. 「PlatformIO IDE」を選択
4. 「Install」を選択

これでPlatformIO IDEのインストールは完了です。

### PlatformIO プロジェクトの作成

まずはPlatformIOのプロジェクトを作成します。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/36587/00d06c7c-3e10-23bd-689c-a605c641387a.png)

下にある家のアイコンを選択すると、PIO Homeの画面が開きます。Quick Accessの「New Project」を選択し、新しいプロジェクトを作成します。

Project Wizard が開くので、以下のように設定します。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/36587/98a00a6e-9386-7522-07e9-7b3b97db659b.png)

- Name: **（任意の名前）**
- Board: **M5Stack Fire**
- Framework: **Arduino**

問題なければ「Finish」を押します。

プロジェクトが作成され、`platformio.ini` の編集画面が表示されました。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/36587/53424336-a596-7c93-22eb-cb8c91536fea.png)


### ライブラリの追加

M5Paperの画面表示に必要なライブラリを追加します。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/36587/730444f8-4796-bff5-4c19-e0709131e8d0.png)

1. 下にある家のアイコンを選択し、PIO Homeを開く
2. 「Libraries」を選択し、ライブラリ画面を開く
3. Registryの検索バーから「M5EPD」で検索
4. 検索結果から「M5EPD」を選択

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/36587/71151f00-4a3d-9b3a-bc0d-742f89d4eae4.png)

M5EPDのライブラリの情報が表示されるので、「Add to Project」でプロジェクトに追加します。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/36587/00be6372-f5fe-99f1-df84-b56ceb80fa11.png)

Add project dependency画面が表示されるので、先ほど作成したプロジェクトを選択し、「Add」を選択します。

platform.ioのlib_depsに `m5stack/M5EPD` が追加されたことを確認します。

```ini:platformio.ini
; PlatformIO Project Configuration File
;
;   Build options: build flags, source filter
;   Upload options: custom upload port, speed and extra flags
;   Library options: dependencies, extra library storages
;   Advanced options: extra scripting
;
; Please visit documentation for the other options and examples
; https://docs.platformio.org/page/projectconf.html

[env:m5stack-fire]
platform = espressif32
board = m5stack-fire
framework = arduino
lib_deps = m5stack/M5EPD@^0.1.1
```

 `.pio/libdeps/m5stack-fire` 配下に M5EPDのディレクトリが追加されていました。

```shell-session
$ tree -d .pio -L 4
.pio
├── build
│   └── m5stack-fire
└── libdeps
    └── m5stack-fire
        └── M5EPD
            ├── examples
            ├── src
            └── tools
```

### ソースコードの修正

ひとまず、HelloWorldを表示できるコードをexampleからコピペします。
まずは、コピペ元のソースを開きます。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/36587/a12a8461-c24f-a89c-63f8-683a022f7c80.png)

1. 下にある家のアイコンを選択し、PIO Homeを開く
2. 「Libraries」を選択し、ライブラリ画面を開く
3. 「Installed」を選択肢、インストールしたライブラリ一蘭を表示
4. 一覧から「M5EPD」を選択

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/36587/a58e49be-bc31-45c5-6c6b-c35aada0155e.png)

Examplesタブにサンプルコードが表示されるので、検索バーから「HelloWorld」で検索し、ソースをコピーします。
コピーしたコードはsrc/main.cppに貼り付けます。

GUIの画面遷移が面倒だったら[GitHub](https://github.com/m5stack/M5EPD/blob/main/examples/Basics/HelloWorld/HelloWorld.ino)からコピペすれば良いでしょう。

```cpp:src/main.cpp
#include <M5EPD.h>

M5EPD_Canvas canvas(&M5.EPD);

void setup()
{
    M5.begin();
    M5.EPD.SetRotation(90);
    M5.EPD.Clear(true);
    M5.RTC.begin();
    canvas.createCanvas(540, 960);
    canvas.setTextSize(3);
    canvas.drawString("Hello World", 45, 350);
    canvas.pushCanvas(0,0,UPDATE_MODE_DU4);
}

void loop()
{

}
```

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/36587/eb7f04a5-b65d-751c-ac57-ecbbfe31ac87.png)

ライブラリが読み込まれなく、エラーとなっている場合はVisual Studio Codeを再起動します。（多分もっといい方法があると思うので、誰か教えてください）


### ビルド

コードも書いたのでようやくビルドです。

画面下のチェックボタンをクリックするとビルドが開始します。

![](https://storage.googleapis.com/zenn-user-upload/2tr0crnnfrh2196ownscgnqfmnn2)

`SUCCESS` と表示されたことを確認します。

### デプロイ

ようやくデプロイですね。

M5 PaperをUSBで接続し、画面下の→ボタンをクリックするとビルドが開始します。

![](https://storage.googleapis.com/zenn-user-upload/ddqs0z0c0tj5m1tde6ppg7e5gjmk)

`SUCCESS` と表示されたことを確認します。

M5 Paperに画像が表示されました！やったね！
![](https://storage.googleapis.com/zenn-user-upload/x2lpp01mulg9jsv23covba9ik76i)