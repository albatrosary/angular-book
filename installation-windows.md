# Windows 10

Windows の場合、Webアプリケーション開発を行うためのツールがほとんどインストールされていません。Mac を利用するユーザより若干多くの手間を掛け環境を作る必要があります。次のものは私が Surface pro 4 を購入したときにはじめにセッティングしたツールです。

* git bash
* python 2系
* Ruby
* Visual Studio 2015 Community
* Windows 10 SDK
* nodist
* nodejs
* yo grunt-cli bower gulp live-server
* Visual Studio Code

順番にインストールしてください。尚、Windows 7 等はこれとは若干異なる手順が必要ですが、エンジニアですので Windows 10 を購入するか、Mac Book Pro \(でなくてもいいですが\) にするか、若しくは [Ubuntu](https://www.ubuntu.com/) を入れるかをすることを望みます。

### git bashのインストール

何はともあれ Windows マシンを利用するときに、必ず`git bash`はインストールします。

exe ファイルをダウンロードしてインストールしますが、設定が少しあります。重要なのが「Configuring the line ending conversions」で`Checkout as-is, commit Unix-style line endings`を選択してます。 参考までに、こちらでも同じexeが落ちてきます。

\[[http://git-scm.com/](http://git-scm.com/)\]

### python 2.7

python 2.7 をインストールします。npmライブラリの一部でpythonを利用していますので必須です（3系使わず2系）。

\[[https://www.python.org/downloads/](https://www.python.org/downloads/)\]

インストール出来たらPATHを追加しておきます。インストーラーなんだからやってくださいよと思うのだが自力で書き込みます。

### Ruby

SASS コンパイルとかで利用されるRuby（安定版）も入れます。三種類インストーラーありますが`RubyInstaller`を使ってます。

\[[http://rubyinstaller.org/](http://rubyinstaller.org/)\]

こちらはパスの設定はインストーラーからできる

### Visual Studio 2015 Community

\[[https://www.microsoft.com/ja-jp/dev/products/community.aspx:title](https://www.microsoft.com/ja-jp/dev/products/community.aspx:title)\]

オプションのインストールに関してはnode-gypがエラーになりますので対策はこちらを見て下さい

\[[https://github.com/nodejs/node-gyp](https://github.com/nodejs/node-gyp)\]

node-gypの条件はWindows10の場合は下記で「Visual C++ チェック忘れるな」ということのようです。

* Install the latest version of npm \(3.3.6 at the time of writing\)
* Install Python 2.7
* Install Visual Studio Community 2015 Edition. \(Custom Install, Select Visual C++ during the installation\)
* Set the environment variable GYP\_MSVS\_VERSION=2015
* Run the command prompt as Administrator
* $ npm install \(--msvs\_version=2015\) 
  &lt;
  -- Shouldn't be needed if you have set GYP\_MSVS\_VERSION env

### WIdows 10 SDK

\[[https://dev.windows.com/ja-jp/downloads/windows-10-sdk:title](https://dev.windows.com/ja-jp/downloads/windows-10-sdk:title)\]

### nodist

MAC で Node.js を入れるときに nodebrew を使ったように、Windows では nodist を使用します。インストーラーが用意されているのでダウンロードし実行します。

1. Download the installer here\(
   &lt;
   -githubを確認\)
2. Run the installer and follow the install wizard

設定は`set NODIST_X64=0`を`git bash`上で実行し、利用する node を登録します。確認のため`nodist -v`を叩くとバージョンが表示されます。現在は

```
$ nodist -v
0.7.2
```

と表示されます。

```
$ nodist + v6.7.0
$ nodist 6.7.0
```

これで利用可能になります。確認のためバージョンを見てみると

```
$ node -v
v6.7.0
$ npm -v
3.10.3
```

となります。
