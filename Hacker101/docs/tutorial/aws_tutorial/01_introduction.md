# クイズ(AWSCLI概要)

## Q0 方針
??? success
    ### 概要
    - 使用予定のものだけ書いていく
    - もしくは一度以上使ったコマンド


## Q1 AWS CLIについて知っていますか?

??? success
    ### AWS CLI(Command Line Interface)
    - AWSのサービスをコマンドラインから管理するためのツール

## Q2 awsコマンドのひな型を知っていますか?

??? success
    ### 概要

    ```bash
    aws [options] <command> <subcommand> [parameters]
    ```

    !!! warning
        - `[]`はオプションなので必ずしも指定する必要はない

## Q3 awsコマンドのグローバルオプションを知っていますか?

??? success
    ### `--debug`
    - デバッグログをオンにする

    ### `--endpoint-url`
    - コマンドのデフォルトURLを指定されたURLでオーバーライド

    ### `--no-verify-ssl`
    - AWSのサービスと通信する際、AWS CLIはSSLを使用
    - このデフォルトの動作(SSL証明書を検証する動作)をオーバーライドする

    ### `--no-paginate`
    - 自動ページ送りを無効

    ### `--output`
    - コマンド出力の書式設定

    ```text
    - json
    - text
    - table
    - yaml
    - yaml-stream
    から出力形式を選択可能
    ```

    - 例

    ```bash
    #outputの形式をテーブルへと変更している
    aws cognito-identity list-identity-pools --max-results 10 --output table > result.txt
    ------------------------------------------------------------------------------------------------------------
    |                                             ListIdentityPools                                            |
    +----------------------------------------------------------------------------------------------------------+
    ||                                              IdentityPools                                             ||
    |+------------------------------------------------------+-------------------------------------------------+|
    ||                    IdentityPoolId                    |                IdentityPoolName                 ||
    |+------------------------------------------------------+-------------------------------------------------+|
    ||  ap-northeast-1:hoge                                 |                 pool1                           ||
    ||  ap-northeast-1:fuga                                 |                 pool2                           ||
    ||  ap-northeast-1:foo                                  |                 pool3                           ||
    ||  ap-northeast-1:tekitou                              |                 pool4                           ||
    |+------------------------------------------------------+-------------------------------------------------+|
    ```

    ### `--query`
    - レスポンスのフィルタリングに使用する`JMESPath`クエリ

    ### `--profile`
    - 認証情報ファイルの特定のプロファイルを使用

    ### `--region`
    - 使用するリージョン。`config/env`の設定をオーバーライドする

    ### `--version`
    - ツールのバージョン表示

    ### `--color`
    - カラー出力の`on/off/auto`切り替え

    ```bash
    aws --version --color auto
    aws-cli/2.13.13 Python/3.11.4 Windows/10 exe/AMD64 prompt/off
    ```

    ### その他について

    ```bash
    --no-sign-request
      # 資格情報をロードしない
    --ca-bundle
      # CA証明書バンドル。config/env設定をオーバーライド
    --cli-read-timeout
      # 最大ソケット読み取り時間(second)
      # 0ならsocketはブロック
    --cli-connect-timeout
      # 最大ソケット接続時間(second)
      # 0ならsocketはブロック
    --cli-binary-format
      # バイナリBLOBに使用される書式設定スタイル
      # base64/ raw-in-base64-out(AWS CLI v1との互換性)
    --no-cli-pager
      #出力用のCLIページャーを無効
    --cli-auto-prompt
      #CLI入力パラメータの入力を自動的に要求
    --no-cli-auto-prompt
      #CLI入力パラメータの自動プロンプトを無効
    ```

## Q4 ARNの形式について知っていますか?

??? success
    ### 一般的な形式

    ```text
    雛形
    arn:partition:service:region:account-id:resource-id
    arn:partition:service:region:account-id:resource-type/resource-id
    arn:partition:service:region:account-id:resource-type:resource-id

    例)
    IAMユーザ
    arn:aws:iam::123456789012:user/hogehoge

    RDS
    arn:aws:rds:ap-northeast-1:12345678901:hogehoge
    ```

    ### 説明

    ```text
    partition
      aws: AWSリージョン
    
    service
      amplify: amplifyというサービス
    
    region
      ap-northeast-1: 東京リージョン
    
    account-id
      リソースを所有しており、ハイフンがないAWSアカウントのID
    
    resource-type
      リソースのタイプ
    
    resource-id
      リソース識別子（リソース名 or ID等）
      親リソースやバージョンなどの修飾子が含まれることも

    ```

## Q4 amplifyのアプリ一覧を返す方法を知っていますか？

??? success
    ### キーワード

    ```text
    ARN(Amazon Resource Name): 
      個々のAWSリソースに割り当てられた一意の識別子
      全AWSに渡るリソースを指定する必要がある場合、必要
      リソース名のフォーマットについては、後述
    ```
    ### 既存のAmplifyアプリのリストを返す

    ```json
    // aws amplify list-apps
    {
        "apps": [
            {
                "appId": "<アプリの一意のID>",
                "appArn": "<アプリのARN>",
                "name": "<アプリの名前>",

                /*
                tags
                  Amplifyアプリのタグ
                  key: value
                */
                "tags": {},

                "repository": "<リポジトリ名>",

                /*
                WEB:静的アプリ
                WEB_COMPUTE: SSRアプリの場合(Next12以降)
                WEB_DYNAMIC: (Next11以前)
                */
                "platform": "<アプリのプラットフォーム>",

                "createTime": "<アプリの作成時刻>",
                "updateTime": "<アプリの更新時刻>",

                /*
                iamServiceRoleArn
                  アプリのarnのIAMサービスロール
                */
                "iamServiceRoleArn": "arn:aws:iam::0123456789012:role/backend-hogehoge-role",

                /*
                environmentVariables
                  環境変数のキーと値
                  key: value
                */
                "environmentVariables": {
                    "NODE_VERSION": "20",
                },


                /*
                defaultDomain
                  アプリのデフォルトドメイン
                */
                "defaultDomain": "<appId>.amplifyapp.com",

                /*
                enableBranchAutoBuild
                  ブランチの自動構築が有効か
                */
                "enableBranchAutoBuild": false,

                /*
                enableBranchAutoDeletion
                  ブランチを削除すると、
                  Amplifyコンソールから
                  ブランチが自動的に切断されるか
                */
                "enableBranchAutoDeletion": true,

                /*
                enableBasicAuth
                  アプリのブランチに対する
                  基本認証が有効か
                */
                "enableBasicAuth": false,

                /*
                customRules
                  source: URL書き換え/リダイレクトのソース
                  target: 書き換えられるターゲット
                  status: 適用後のHTTPステータスコード

                */
                "customRules": [
                    //アプリのカスタムリダイレクトルールと
                    //書き換えルールについて
                    {
                        "source": "/<*>",
                        "target": "hogehoge.com/<*>",
                        "status": "200"
                    },
                ],

                /*
                productionBranch
                  実稼働ブランチに関する説明
                  lastDeployTime: 最後のデプロイ時間
                  status: 本番ブランチのステータス
                  branchName: ブランチ名
                */
                "productionBranch": {
                    "lastDeployTime": "2024-02-06T14:27:31.482000+09:00",
                    "status": "SUCCESSD",
                    "branchName": "main"
                },

                /*
                buildSpec
                  amplify.yml等の記載された、
                  ビルド時のアプリケーション構築の仕様
                */
                "buildSpec": "version: 1\nbackend:\n ...",

                /*
                customHeaders
                  カスタムHTTPヘッダがあれば記載
                */
                "customHeaders": "",

                /*
                enableAutoBranchCreation
                  自動ブランチ作成を有効にするか
                */
                "enableAutoBranchCreation": false,

                /*
                repositoryCloneMethod
                  Amplifyはこのパラメータを使用して、
                  Gitリポジトリへのアクセスに使用する
                  認証プロトコルを生成する
                SIGV4 CodeCommitリポジトリ
                SSH   GitLab or Bitbucketリポジトリ
                Token Githubリポジトリ
                */
                "repositoryCloneMethod": "SIGV4"
            }
        ]
    }

    ```

    ### 特定のアプリだけを返したい場合

    ```bash
    aws amplify get-app --app-id <value>

    # valueにはAmplifyアプリのIDを渡す
    ```

## Q5 amplifyのバックエンド環境を取得する方法を知っていますか?

??? success
    ### バックエンド環境を表示する

    ```bash
    aws amplify list-backend-environments 
    --app-id <value>
    ```

    ```json
    {
        "backendEnvironments": [
            {   
                /*
                backendEnvironmentArn
                  バックエンド環境のARN
                */
                "backendEnvironmentArn": "arn:aws:amplify:...",

                /*
                environmentName
                  バックエンド環境名
                */
                "environmentName": "dev",

                /*
                stackName
                  バックエンド環境の
                  AWS CloudFormationスタック名
                */
                "stackName": "amplify-hoge-dev-123456",

                /*
                deploymentArtifacts
                  デプロイメントアーティファクトの名前
                
                デプロイメントアーティファクト
                  特定のバックエンド環境のデプロイに
                  使用されるビルド済みのアプリやサービスの
                  バイナリやコードを指す。
                  通常は、開発者がローカルで作業したり
                  CI/CDパイプラインでビルドされたりした
                  コードやファイルの集合体

                  Amplifyはバックエンド環境をデプロイする際
                  特定のバックエンド環境に関連付けられた
                  デプロイメントアーティファクトを使用し、
                  アプリケーションのビルドやデプロイを行う
                */
                "deploymentArtifacts": "hoge-deployment",

                /*
                createTime
                  バックエンド環境の作成日時
                */
                "createTime": "2021-10-22T14:34:55.966000+09:00",

                /*
                updateTime
                  バックエンド環境の最終更新日時
                */
                "updateTime": "2021-10-22T14:34:55.966000+09:00"
            }
        ]
    }

    ```

    ### 特定アプリの特定のバックエンド環境を表示

    ```bash
    aws amplify get-backedn-enviroment 
    --app-id <value> 
    --environment-name <value>
    ```

## Q6 Amplifyアプリのブランチを取得する方法を知っていますか?

??? success
    ### Amplifyアプリのブランチをリストで表示

    ```text
    aws amplify list-branches 
    --app-id <value>
    ```

    ```json
    {
      "branches": [
        {
          /*
          branchArn
            ブランチのARN
          */
          "branchArn": "arn:aws:amplify:hoge",
          "branchName": "development",
          "tags": {},

          /*
          stage
            特定のブランチが属するステージ
            DEVELOPMENT: 開発
            PRODUCTION:本番ステージ
            EXPERIMENTAL: 実験的な
            BETA, PULL_REQUEST

            ※NONEになっている場合は、
            特定のステージに属していない
            ブランチを見つけ安くするために関連付けるべき
          */
          "stage": "DEVELOPMENT",

          /*
          displayname
            表示名。マネジメントコンソールではこちらが表示
            branchNameはプログラム以内やCLIでの識別に使用
          */
          "displayName": "development",
          
          /*
          enableNotification
            ブランチの通知を有効にするか
          */
          "enableNotification": false,

          "createTime": "2023-02-22T21:22:26.113000+09:00",
          "updateTime": "2024-02-26T11:02:07.742000+09:00",

          /*
          environmentVariables
            ブランチに固有の環境変数
          */
          "environmentVariables": {
            "NODE_VERSION": "20",
            "USER_BRANCH": "dev"
          },

          /*
          enableAutoBuild
            push時の自動ビルドを有効にするか
          */
          "enableAutoBuild": true,

          /*
          framework
            ブランチのフレームワークについて
          */
          "framework": "Next.js - SSR",

          /*
          activeJobId
            現在実行中のジョブのID
            デプロイに失敗した場合、この値は更新されない
            0パディングされるので、常に桁数は同じ
            アーティファクトの取得に使えそう
          */
          "activeJobId": "0000000268",

          /*
          totalNumberOfJobs
            現在実行中のジョブの個数
          */
          "totalNumberOfJobs": "0",

          /*
          enableBasicAuth
            ブランチに対する基本認証
            アプリ全体の値と異なる場合
            ブランチに設定した方が優先される
          */
          "enableBasicAuth": false,

          /*
          enablePerformanceMode
            ブランチのパフォーマンスモードが有効か
            [有効な場合：利点]
              ・Amplifyが最適化されたキャッシュ機能を提供
                ⇒アプリの読み込み速度が向上

              ・キャッシュされたデータを利用するので、
              　リクエストを処理するために必要なリソース減
              　⇒運用コストが減少する
            
            [有効な場合：欠点]
              ・キャッシュ機能のため、動的なコンテンツの
              　更新が即座に反映されない
              ・キャッシュされたデータが非常に機密性の
              　高い情報の場合、セキュリティ上の懸念がある
              ・ホスティングの構成やコードの変更が
              　導入されるまでに、最大10分

              ホスティングの構成
                ・インスタンスサイズやタイプ
                ・ロードバランサの設定
                ・キャッシュの設定
                ・CDNの設定
                ・SSL/TLS証明書の設定
              
              ホスティング
                ウェブサイトやアプリをインターネット上で
                公開するためのサーバ、インフラを提供する
                サービス
          */
          "enablePerformanceMode": false,

          /*
          ttl
            ・データやリソースがキャッシュされている機関
            ・以下の場合、5秒間はキャッシュされたデータが有効
            ・5秒以内なら、リクエストをキャッシュから返す
            ・キャッシュされたデータの更新頻度やリソースの更新頻度に基づいて調整するべき
          
          [短いTTL]
            ・迅速な更新が必要な場合
            ・リアルタイムな情報が提供される場合
          
          [長いTTL]
            ・リソースの更新が稀
            ・リクエストの負荷も軽減したい場合
          */
          "ttl": "5",

          /*
          ★enablePullRequestPreview
            ブランチのプルリクエストのプレビューを有効にする
            ※code-commitではまだ利用できない。2024/02現在
            ※「まだ」利用できないというメッセージは
            　4年以上表示されており、変更は望めない。
            　プレビュー機能は、非常に役に立つものであるため
            　code-commitからgithubへの移行理由になりうる。
            　なぜ、同じAWSのシステムで利用できないのか
            　本当にわからない
              [該当問題に関するissue](https://github.com/aws-amplify/amplify-hosting/issues/373)
            
            ※通常、Amplifyはプレビューを行う際、一時的な
            バックエンド環境を自動的にプロビジョニングする
            専用のバックエンド環境を指定する場合
            PullRequestEnvironmentNameを使用
              
          
          [Trueの場合]
            ※mainブランチのフラグがtrueであった場合
            dev --> mainへのプルリクエストを作成したとする
            
            ・変更内容が反映されたサイトが
              別のURLでプレビューされる
            ・既存の本番環境はそのまま維持されるため
            　本番環境に影響を与える可能性のある変更を
              安全にテストでき、
            　安全にレビューや確認を行うことが可能

            ・プルリクエストのマージ or
              クローズ時に環境は自動で削除される
              ⇒クリーンアップも自動で行われるので
              　テストのために別ブランチを立ち上げるなどの
              　不要なリソース消費は必要なくなる
          */
          "enablePullRequestPreview": false,


          "backendEnvironmentArn": "hoge-arn"
        }
      ]
    }
    ```

    ### 特定のブランチを表示

    ```text
    aws amplify get-branch 
    --app-id <value>
    --branch-name <value>
    ```

## Q7 Amplifyアプリに関連付けられたドメイン情報をリストで返す方法を知っていますか?

??? success
    ### ドメイン情報を表示

    ```bash
    aws amplify list-domain-associations 
    --app-id  <value>

    # カスタムドメインの設定やSSL証明書の有効性などが確認可能
    ```

    ```json
    {
      "domainAssociations": [
        {
          "domainAssociationArn": "arn:aws:amplify:ap-northeast-1:012345678901:apps/abcdefghijklm/domains/demodemo.fugafuga.com",
          "domainName": "demodemo.fugafuga.com",

          /*
          enableAutoSubDomain
            ブランチのサブドメインの自動作成を有効にするか
          */
          "enableAutoSubDomain": false,

          /*
          domainStatus
            ドメインの関連付けの現在のステータス
          */
          "domainStatus": "AVAILABLE",

          /*
          certificateVerificationDNSRecord
            証明書検証用のDNSレコード
          */
          "certificateVerificationDNSRecord": "hoge.com. CNAME hoge.aws.",

          /*
          subDomains
            subDmainSetting: サブドメインの設定について
              prefix: サブドメインのプレフィックス設定
              branchName: サブドメインのブランチ名
            verified: サブドメインが検証済みか
            dnsRecord: サブドメインのDNSレコード

            下記の場合、prefixがwwwでないと
            正常に動作しないと思われる(verified:falseなので)
          */
          "subDomains": [
            {
              "subDomainSetting": {
                "branchName": "dev"
              },
              "verified": false,
              "dnsRecord": " CNAME hoge.cloudfront.net"
            },
            {
              "subDomainSetting": {
                "prefix": "www",
                "branchName": "dev"
              },
              "verified": true,
              "dnsRecord": "www CNAME fuga.cloudfront.net"
            }
          ]
        }
      ]
    }
    ```

    ### 特定のドメイン情報だけ取得したい場合

    ```text
    aws amplify get-domain-association 
    --app-id <value>
    --domain-name <value>
    ```

## Q8 Amplifyアプリにおいて、ジョブ（ビルドやデプロイ等）の情報を取得したい場合の方法を知っていますか?

??? success
    ### 特定のブランチに紐づいたジョブ一覧を返す

    ```text
    aws amplify list-jobs
    --app-id <value>
    --branch-name <value>
    ```

    ```json
    {
      "jobSummaries": [
        {
          "jobArn": "arn:aws:amplify:.../jobs/0000000001",
          "jobId": "268",
          "commitId": "ハッシュ値",
          "commitMessage": "省略される",
          "commitTime": "2024-04-26T19:59:05.830000+09:00",
          "startTime": "2024-04-26T19:59:06.198000+09:00",
          /*
          status
            FAILED:失敗
            CANCELLED: キャンセル
            SUCCEED: 成功
          */
          "status": "FAILED",
          "endTime": "2024-04-26T20:00:07.616000+09:00"
        },
      ]
    }
    ```

    ### 特定のジョブを返す(さらに詳細情報が得られる)


## Q9 Codecommitを用いて特定のコミットに関する情報を返す方法を知っていますか?

??? success
    ### 特定のコミットに関する情報

    ```bash
    #Commit-idについてはジョブ一覧などから取得

    aws codecommit get-commit
    --repository-name <value>
    --commit-id <value>
    ```

    ```json
    {
      "commit": {
        "commitId": "commit-id",
        "treeId": "コミットのツリー情報",
        "parents": [
          "親コミットID"
        ],
        "message": "コミットメッセージ\n",
        "author": {
          "name": "コミットの作成者",
          "email": "hogehoge@gmail.com",
          "date": "1709018573 +0900"
        },
        "committer": {
          "name": "コミットした人",
          "email": "hogehoge@gmail.com",
          "date": "1709018573 +0900"
        },
        "additionalData": ""
      }
    }
    ```

## Q10 CodeCommitを用いて、Diffを取得することができますか?

??? success
    ### 差分(ファイル)取得

    ```bash
    aws codecommit get-differences
    --repository-name <value>
    [--before-commit-specifier] <commit-id>
    --after-commit-specifier <commit-id>fore
    ```

    ```json
    /*
    BLOBとは?
      Binary Large Object
      バイナリデータの塊
      通常のデータ型のフィールドには格納できない、
      画像や音声、動画、実行、圧縮ファイルなどの
      バイナリデータをレコードに格納するためのもの

      内部的にはテーブル本体とは別箇所に保存され
      テーブル上からデータを参照する
      そのため高速な検索などはできないことが多い
    */
    { 
      /*
      差分を取得する
      以下の場合、.gitignoreで変更があることが分かる
      */
      "differences": [
        {
          "beforeBlob": {
            "blobId": "BLOBのID",
            /*path: 関連ファイル名*/
            "path": ".gitignore",
            /*
            mode
              100644:読み取り/書き取り
              100755:読み取り/書き込み/実行
              160000:サブモジュール
              120000:シンボリックリンク
            */
            "mode": "100644"
          },
          "afterBlob": {
            "blobId": "BLOBのID",
            "path": ".gitignore",
            "mode": "100644"
          },

          /*
          changeType
            A:追加
            D:削除
            M:修正
          */
          "changeType": "M"
        }
      ]
    }
    ```

## Q11 CodeCommitとjq, base64 -d等を用いて、blobの中身を表示する方法を知っていますか?

??? success

    ### blobを入手

    ```bash
    aws codecommit get-blob 
    --repository-name <value>
    --blob-id <blobId>
    > result.json
    ```

    ```json
    {
      "content": "base64エンコードされたコンテンツ"
    }
    ```

    ### jqコマンド

    ```bash
    jq -r ".content" result.json | base64 -d
    #1. jqを用いて、JSONファイルから特定のキーの値を取得
    #2. base64でデコード
    ```

    - decodeした結果(.gitignoreだったので)
    ```txt
    # dependencies
    /node_modules
    /.pnp
    ...
    ```