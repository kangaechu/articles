---
title: "GCPをTerraformで管理する"
emoji: "✨"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["terraform", "gcp"]
published: true
---

GCPをTerraformで管理する際のディレクトリ構成です。

## 目的

プライベートでGCPを使っているのですが、Webコンソールでの管理に辛さを感じていました。
仕事でTerraformを使っていることもあり、できればTerraformで管理したいのです。
複数のプロジェクトをどのような構成で管理するかを調べたのですが、いい事例が見つからなかったので、
まずは自分の構成をたたき台にしてもらおうかなという気持ちです。

## 前提条件

- 個人開発用
- プロジェクトは10個程度
- プロジェクト内のリソースはGCSやAPIなど、1〜2個程度。かなり小規模
- Terraformで管理
- tfstateはGCSで管理
- GCPは初心者

## 構成

```
.
├── README.md
├── project_01
│   ├── config.tf # Terraformの設定
│   ├── google_project.tf
│   ├── google_service.tf
│   └── local.tf
├── project_02
├── project_03
└── project_tfstate
```

### プロジェクトごとにディレクトリを作成

プロジェクトごとにディレクトリ（project_xx）を作成した。プロジェクトごとにGitリポジトリを作成する方法もあるが、
1プロジェクトあたりのリソースが少ないため、リポジトリが増える方が煩雑になりそうなので1リポジトリにまとめた。

### tfstateは専用のプロジェクトに保管

tfstateは専用のプロジェクト（上記例ではproject_tfstate）に保管した。プロジェクト内にGCSのバケットを作成する方法もあるが、1プロジェクト内のリソースが少ないので、わざわざプロジェクトごとにtfstate用のバケットを用意する必要はないのではと思い、共用する方針とした。
CIの権限付与を考えるのであれば、プロジェクトごとにtfstateのバケットを作るのも良さそう。

### config.tfに設定をまとめる

config.tfはこんな感じ。

``hcl:config.tf
terraform {
  required_version = "1.1.7"

  required_providers {
    google = {
      source  = "hashicorp/google"
      version = "4.13.0"
    }
  }
  backend "gcs" {
    bucket = "project_tfstate"
    prefix = "project_01"
  }
}

provider "google" {
  project = local.project_id
  region  = "asia-northeast1"
}
```