実際の開発ではクラウドなどの環境にデプロイする必要があります。私が好んで使っているのは

* [Microsoft Azure](https://azure.microsoft.com/ja-jp/)
* [Wercker CI](http://www.wercker.com/)
* [Github](https://github.com/)

開発したコードを Github で管理し、Wercker CI でユニットテスト等を行い成功時にビルドタスクを走らせ Microsoft Azure へデプロイするといった流れで利用してます。この３つは片手程のクリック数で構築できるためプロジェクト用の環境構築にはほとんど時間を要しません。


### Github でのリポジトリ作成

Github でリポジトリを作るのはそれほど難しいことではありません。 Githubアカウントを作成しリポジトリを生成するだけです。

### Microsoft Azure の設定

こちらもやはり Azure アカウントを作成します。その後、サブスクリプションを設定します。

> サブスクリプションとは、定期購読、購読料、加入契約、申込、応募、署名、などの意味を持つ英単語。ITの分野では、会員制サービスへの加入や、一定期間に定額でいくらでもデータを購入できる販売方式、特定の情報源からデータを定期的にダウンロードすることなどを指すことが多い。
>
> http://e-words.jp/w/サブスクリプション.html より

サブスクリプションの設定が終わったらインスタンスを生成します。インスタンスには「Web + モバイル」の中から「Web App」などをチョイスすると良いでしょう。５分程度の作業と数クリックでインスタンスが作成されます。

インスタンスが出来たら lftp の設定を行います。ftpサーバは既に決まったものを与えられていますのでログインユーザに対するパスワードを設定するのみです。

> LFTPは、UNIXおよびUnix系システム向けのコマンド行ファイル転送プログラム（FTPクライアント）の1つ。

非常に簡単ですが、Webサーバの構築ができます。

### Wercker CI の設定

Wercker CIもアカウント登録します。こちらは Githubアカウントを SSO\(シングルサインオン：Single Sign-On\)すると良いでしょう。Githubで作成したリポジトリを監視する設定を行います。

Wercker CI に実行させるタスクを定義します。wercker.yml というファイルをGithubリポジトリに配置します。内容は下記の通り。

    # This references the default nodejs container from
    # the Docker Hub: https://registry.hub.docker.com/_/node/
    # If you want Nodesource's container you would reference nodesource/node
    # Read more about containers on our dev center
    # http://devcenter.wercker.com/docs/containers/index.html
    box: node
    # This is the build pipeline. Pipelines are the core of wercker
    # Read more about pipelines on our dev center
    # http://devcenter.wercker.com/docs/pipelines/index.html

    # You can also use services such as databases. Read more on our dev center:
    # http://devcenter.wercker.com/docs/services/index.html
    # services:
        # - postgres
        # http://devcenter.wercker.com/docs/services/postgresql.html

        # - mongo
        # http://devcenter.wercker.com/docs/services/mongodb.html
    build:
      # The steps that will be executed on build
      # Steps make up the actions in your pipeline
      # Read more about steps on our dev center:
      # http://devcenter.wercker.com/docs/steps/index.html
      steps:
        # A step that executes `npm install` command
        - npm-install
        # A step that executes `npm test` command
        - npm-test

        # A custom script step, name value is used in the UI
        # and the code value contains the command that get executed
        - script:
            name: echo nodejs information
            code: |
              echo "node version $(node -v) running"
              echo "npm version $(npm -v) running"
    deploy:
      steps:
        - script:
            name: install angular-cli
            code: |
              npm install -g angular-cli
        - script:
            name: ng build
            code: |
              ng build --aot true
        - install-packages:
            packages: lftp
        - script:
            name: azure ftp deploy
            code: |
              lftp -u ${FTP_USERNAME},${FTP_PASSWORD} -e "mirror -Rev -X wercker.yml dist/. /site/wwwroot/." ${FTP_SERVER_URL}

${FTP\_USERNAME}、${FTP\_PASSWORD}、${FTP\_SERVER\_URL} は Wercker CI のパラメータとして Wercker CI に定義したものです。  
  
これで 開発コードをクライアントから Github へプッシュすると自動的に Wercker CI が実行され Azure へデプロイすることが出来ます。ものの数分でこうした一連の作業を完了させることが出来ますので、是非述べた構成で開発ライフサイクルを楽しんでみては如何でしょうか？

