---
title: "CircleCI-AWSをOIDC認証で連携する際のTips"
emoji: "🚃"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Terraform", "CircleCI", "AWS"]
published: true
---

2022年3月26日に待望のCircleCI OIDCサポートがリリースされました。
https://circleci.com/docs/ja/2.0/openid-connect-tokens/

よーしブログ書くぞと思ったらクラメソさんが先に記事を投稿していたので、実際に自分が試したところをTipsとして記載します。
https://dev.classmethod.jp/articles/circleci-supported-oidc-so-i-tried-linking-it-with-aws/


## Terraformでの実装

### OIDC Provider

まずはOIDC Providerの定義から。

```hcl:oidc_providers.tf
resource "aws_iam_openid_connect_provider" "circleci" {
  url             = "https://oidc.circleci.com/org/${local.circleci_org_id}"
  client_id_list  = [local.circleci_org_id]
  thumbprint_list = [
    "9e99a48a9960b14926bb7f3b02e22da2b0ab7280",
  ]
}

locals {
  circleci_org_id = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

[aws_iam_openid_connect_provider](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/iam_openid_connect_provider) リソースで定義します。

URLとClient IDにはCircleCIのOrg IDが必要です。
CircleCIのOrganization Settings→Contextsで何らかのCOntextを作成するとOrganization IDが表示されます。
![](https://storage.googleapis.com/zenn-user-upload/67b9e902e508-20220329.png)

CircleCIプロバイダのサムプリントは `9e99a48a9960b14926bb7f3b02e22da2b0ab7280` です。
[aws_iam_openid_connect_providerのthumbprint_listの計算方法](https://qiita.com/minamijoyo/items/eac99e4b1ca0926c4310)で自分でも計算することができます。

### IAM Role

IAM Roleは基本的にGitHubリポジトリごとに作成します。

```hcl:oidc_terraform.tf
resource "aws_iam_role" "circleci" {
  name               = "circleci"
  description        = "circleci"
  assume_role_policy = data.aws_iam_policy_document.assume_circleci.json
}

data "aws_iam_policy_document" "assume_circleci" {
  statement {
    actions = [
      "sts:AssumeRoleWithWebIdentity",
    ]

    principals {
      type        = "Federated"
      identifiers = [aws_iam_openid_connect_provider.circleci.arn]
    }
}

# terraform planできるよう、ReadOnlyAccessを付与
resource "aws_iam_role_policy_attachment" "circleci" {
  role       = aws_iam_role.circleci.name
  policy_arn = "arn:aws:iam::aws:policy/ReadOnlyAccess"
}
```

## リポジトリの制限

上記の定義だとOrgが同じで、IAM Role ARNを知っていれば別のリポジトリからAWSの認証を使用できます。
怖いのでちゃんと制限をかけます。

`aws_iam_policy_document` データソースに制限を追加します。

```hcl:oidc_terraform.tf
data "aws_iam_policy_document" "assume_circleci" {
  statement {
    actions = [
      "sts:AssumeRoleWithWebIdentity",
    ]

    principals {
      type        = "Federated"
      identifiers = [aws_iam_openid_connect_provider.circleci.arn]
    }

    condition {
      test     = "StringLike"
      variable = "oidc.circleci.com/org/${local.circleci_org_id}:sub"
      values   = ["org/${local.circleci_org_id}/project/${local.circleci_oidc_project_id}/user/*"]
    }
  }
}

locals {
  circleci_org_id     = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
  circleci_project_id = "yyyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyyy"
}
```

`condition` で制限を追加しています。
指定可能なClaimsは [Format of the OpenID Connect ID token](https://circleci.com/docs/ja/2.0/openid-connect-tokens/#format-of-the-openid-connect-id-token) に記載があります。
ただ、使えたのはリポジトリごとに割り振られるプロジェクトIDが含まれるsubです。 `oidc.circleci.com/project-id`が使えないかなーと思ったのですがうまくいかず。[AWS ウェブ ID フェデレーションで利用可能なキー](https://docs.aws.amazon.com/ja_jp/IAM/latest/UserGuide/reference_policies_iam-condition-keys.html#condition-keys-wif)に書かれているキーしか使えないのですね。

プロジェクトIDを指定することにより、特定のCircleCIプロジェクト（GitHubリポジトリ）からのアクセスに制限できます。
GitHub Actionsのようにブランチ単位で制限ができると、開発環境のみデプロイを許可するIAM Roleと本番環境へのデプロイを許可するIAM Roleを分離できたりして便利なので、もう少し細かい設定ができるようになるといいですね。

プロジェクトIDはProject Settings→Overviewにあります。
![](https://storage.googleapis.com/zenn-user-upload/fe4d3b60eb43-20220329.png)

## CircleCi ステップ間でアクセスキーを渡す

ロールを使用する側では以下のようにconfig.ymlを作成します。

```yaml：.circleci/config.yml
version: 2.1

orbs:
  # See https://circleci.com/developer/ja/orbs/orb/circleci/aws-cli
  aws-cli: circleci/aws-cli@2.1.0

jobs:
  aws:
    machine:
      image: ubuntu-2004:current
    steps:
      - checkout
      - aws-cli/install
      - run: |
          aws_sts_credentials=$(aws sts assume-role-with-web-identity \
            --role-arn ${AWS_ROLE_ARN} \
            --web-identity-token ${CIRCLE_OIDC_TOKEN} \
            --role-session-name "circleci-oidc" \
            --duration-seconds 900 \
            --query "Credentials" \
            --output "json")
          echo export AWS_ACCESS_KEY_ID="$(echo $aws_sts_credentials | jq -r '.AccessKeyId')" >> $BASH_ENV
          echo export AWS_SECRET_ACCESS_KEY="$(echo $aws_sts_credentials | jq -r '.SecretAccessKey')" >> $BASH_ENV
          echo export AWS_SESSION_TOKEN="$(echo $aws_sts_credentials | jq -r '.SessionToken')" >> $BASH_ENV
          source $BASH_ENV
      - run: aws sts get-caller-identity
      - run:
          echo ${CIRCLE_OIDC_TOKEN:1}; # OIDC Tokenを取得

workflows:
  awscli:
    jobs:
      - aws:
          context:
            - oidc
```

事前にプロジェクトのEnvironmentで以下の環境変数を追加します。

- AWS_ROLE_ARN: 先ほど作成したIAM RoleのARN
- AWS_DEFAULT_REGION: AWSのリージョン

`aws sts assume-role-with-web-identity` で作成したアクセスキーは `$BASH_ENV` にリダイレクトすることにより、別ステップでも使用できます。
[環境変数の使用 ](https://circleci.com/docs/ja/2.0/env-vars/)
