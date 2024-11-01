# KMS Key とエイリアスのデプロイ

このプロジェクトは、特定のポリシーを設定したKMSキーとそのエイリアスを作成し、AWS SAM (Serverless Application Model) を使用してデプロイを管理します。

## プロジェクト構成

- **template.yaml**: KMSキーとエイリアスを定義するAWS CloudFormationテンプレート。
  - **MyKMSKey**: IAMユーザーやロールに対して、KMSキーの管理と利用の権限を設定します。
    - **Enable IAM User Permissions**: アカウントルートユーザーに全権限を付与。
    - **Allow access for Key Administrators**: `AdminRole` に対してキーの作成や削除、タグ付けなどの管理操作を許可。
    - **Allow use of the key**: `AdminRole` に対して、キーの暗号化・復号化やデータキーの生成などの利用操作を許可。
    - **Allow attachment of persistent resources**: `AdminRole` に対して、永続的なリソースのアタッチを許可。
    - **Allow Eventbridge**: `events.amazonaws.com` にデータキー生成と復号化の権限を付与。
  - **MyKMSKeyAlias**: 作成したKMSキーに `alias/key-sns` のエイリアスを付与。

- **samconfig.toml**: SAMデプロイメント用のパラメータを定義するファイル。
  - **stack_name**: デプロイするスタック名 (key-sns)
  - **region**: AWSリージョン (ap-northeast-1)
  - **s3_bucket**: SAMアーティファクトを保存するS3バケット (samdeploy-bucket)
  - **capabilities**: デプロイ時の能力 (CAPABILITY_NAMED_IAM)。名前付きIAMリソースを作成するために必要です。

## 出力

- **KMSKeyId**: 作成されたKMSキーのID。
- **KMSKeyAlias**: 作成されたKMSキーのエイリアス (`alias/key-sns`)。

## 事前準備

- **AWS CLI** および **AWS SAM CLI** がインストールされていること。
- **S3バケット**がSAMデプロイメントアーティファクト用に設定されていること。

## デプロイ手順

sam deploy --template-file template.yaml --config-file samconfig.toml --capabilities CAPABILITY_NAMED_IAM

