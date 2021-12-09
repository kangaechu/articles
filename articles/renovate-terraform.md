---
title: "Terraformのアップデートにちょっと便利なRenovate"
emoji: "✨"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["terraform", "renovate"]
published: false
---

Terraformのアップデートに[Renovate](https://docs.renovatebot.com/) を使うのもよかったよという話です。

## 目的

Terraformを運用する際、意外に手間がかかるのがTerraformやプロバイダのバージョンアップです。この1年でTerraformのバージョンは30、terraform-provider-awsのバージョンは54上がっていました。
一つ一つは大したことない作業でも、数が増えると辛いです。

この悩みを解消すべく現れたのが[minamijoyo/tfupdate](https://github.com/minamijoyo/tfupdate)です。tfupdateはコマンドライン、またはCIで実行でき、Terraformやプロバイダのバージョン制約を一度に更新できます。

また、一般的にライブラリのバージョンアップでよく使われるのは[Dependabot](https://docs.github.com/en/code-security/supply-chain-security/keeping-your-dependencies-updated-automatically/about-dependabot-version-updates)です。DependabotはRuby, Node.js, Java, Golangなどに加え、DockerやTerraformにも対応しています。定期的に更新したライブラリを調べ、バージョンアップ可能であればプルリクエストを作成してくれます。

そして今回紹介するのが[Renovate](https://docs.renovatebot.com/)です。Renovateはいわゆる「痒い所に手が届くDependabot」です。以前は有料でしたが、DependabotのGitHub統合のタイミングに合わせて無料になったようです。

Terraformのやプロバイダのアップデートでも「痒い所に手が届く」はずです。

## インストール

ひとまずインストールしてみましょう。
[Renovateのドキュメント](https://docs.renovatebot.com/getting-started/installing-onboarding/) にもある通り、GitHub Appでインストールできます。
https://github.com/apps/renovate を開きます。

![](https://storage.googleapis.com/zenn-user-upload/07c5da4fae2f-20211209.png)

右上の「Install」を押すとインストールできます。

次の画面では、どのOrganizationにインストールするかを選択します。

![](https://storage.googleapis.com/zenn-user-upload/fc043683de02-20211209.png)

次の画面では、どのリポジトリにインストールするかを選択します。
今回はTerraformを管理しているリポジトリを一つ選択しました。

![](https://storage.googleapis.com/zenn-user-upload/b4bd6229cf77-20211209.png)

インストール後、WhiteSource Renovate Dashboardを使用するため、ログインを要求されます。

![](https://storage.googleapis.com/zenn-user-upload/a4bb05da6013-20211209.png)

GitHub認証でログインします。

![](https://storage.googleapis.com/zenn-user-upload/64f1c26f93ca-20211209.png)

ログインすると、Renovate Dashboardが表示されますが、すぐには使わないためひとまず放置します。

![](https://storage.googleapis.com/zenn-user-upload/1db7c80672c7-20211209.png)

先ほど選択したTerraformを管理しているリポジトリを開きます。
「Dependency Dashboard」というタイトルのIssueが作成されました。
現在、Renovateによって以下の2つのアップデートのPull Requestが作成されたことがわかります。

- Update Terraform aws to v3.68.0
- Update dependency hashicorp/terraform to v1.1.0

また、チェックボックスを選択することによりretry/rebaseができます。

![](https://storage.googleapis.com/zenn-user-upload/cab8c1bb05c4-20211209.png)

「Update Terraform aws to v3.68.0」のPull Requestを開きました。
Release Notesなども表示されており、いわゆるDependabotでの更新と同じような機能がありますね。

![](https://storage.googleapis.com/zenn-user-upload/e50168d690ea-20211209.png)

インストールはこれで以上です。結構簡単だったのではないでしょうか。

## Renovateの便利なポイント

Renovateが他のTerraformアップデート方法と比べて便利な点を3つ挙げます。

### 1. GitHub Actions / CircleCIの知識なしに使用できる

tfupdateは便利なツールであり、[CircleCIのテンプレート](https://github.com/minamijoyo/tfupdate-circleci-example)も準備されていますが、ある程度のCIサービスの知識は必要となります。
Renovateであれば先ほどのインストール手順に従えば簡単に使用を開始することができます。

### 2. .terraform-versionも更新してくれる

[tfenv](https://github.com/tfutils/tfenv)を使用している場合、.terraform-versionでインストールするTerraformのバージョンを管理しているのではないでしょうか。Renovateであれば、.terraform-versionもあわせて更新してくれます。地味に便利！

![](https://storage.googleapis.com/zenn-user-upload/24521387013f-20211209.png)

### 3. .terraform.lock.hcl も全アーキテクチャ分更新してくれる

プロバイダやモジュールのバージョン管理に使用する.terraform.lock.hclですが、これを適切に管理するのは非常に難しいです。詳しくは [.terraform.lock.hcl完全に理解した](https://speakerdeck.com/minamijoyo/how-to-update-terraform-dot-lock-dot-hcl-efficiently)を参照いただくとわかりますが、h1はOS/アーキテクチャごとに異なり、正しく追加されていないとCIでエラーになったり思わぬところでgit diffが出たりと結構厄介です。
Renovateであれば、プロバイダのアップデート時に前アーキテクチャ分のh1を追加してくれます。Dependabotでは今のところ `linux_amd64` のみ追加する仕様となっています。
https://github.com/dependabot/dependabot-core/issues/4042

![](https://storage.googleapis.com/zenn-user-upload/97c13fb58fa0-20211209.png)

## まとめ

Renovateが「痒い所に手が届く」ところが理解していただけたかなと思います。
tfmigrate/Dependabotも良いツールですので、必要に応じて使い分けていただければと思います。
