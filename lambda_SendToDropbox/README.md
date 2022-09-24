# send-to-dropbox

## 導入

### 1. リポジトリをクローン

```bash
git clone git@github.com:Nabby12/lambda_SendToDropbox.git
```

### 2. 必要ファイル準備

- 下記のサンプルに沿って"env.json"を作成（ローカル実行の際に環境変数等の設定で必要）
- "yourFunction"は"template.yaml"で設定している関数名と同一
- "enironment1"等は"template.yaml"内"Parameters"以下で設定している変数名と同一

```json
{
 "yourFunction": {
   "environment1": "value1",
   "environment2": "value2"
 } 
}
```

### 3. ビルド

```bash
sam build
```

- AWS SAM CLI 必須
    - 参考： [AWS SAM CLI のインストール - AWS Serverless Application Model](https://docs.aws.amazon.com/ja_jp/serverless-application-model/latest/developerguide/serverless-sam-cli-install.html)

### 4. ローカル実行

```bash
sam local invoke -n env.json -e event.json
```

- "-n" オプション（省略可）で環境変数を渡してローカル実行
- "-e" オプション（省略可）で指定のeventをlambda_handler関数に渡してローカル実行

## デプロイローカル実行

### 1. 初回 || 環境変数修正時

```bash
sam deploy --config-env dev -g / sam deploy --config-env prd -g
```

- "--config-env [環境名]" で環境ごとにデプロイ
  - 開発用 / 本番用など、別lambdaとしてデプロイ可能
- "-g" で各種オプションを設定してデプロイ
  - 初回デプロイ時（samconfig.tomlがない状態）は必須のオプション
  - 設定は "samconfig.toml"（初期値）でファイル生成され、保存される
    - 環境変数が記載されているため、env.jsonなどはgithubにあげない（.gitignoreに記載済み）

### 2. 2回目以降

```bash
sam deploy --config-env dev / sam deploy --config-env prd
```

- "samconfig.toml" で設定されている該当の環境名の値を使用してデプロイ実行

## デプロイ自動実行

- プルリクがマージされるとGithubActionsによりデプロイが実行される
- samconfig.tomlで指定していた環境変数等は、githubリポジトリのSecretsで設定する
  - 別途、.github/workflow/~.yml にて設定が必要
    - ~.ymlファイルは環境別に作成する
  - 複数環境を設定している場合、環境別にSecretsを設定する
