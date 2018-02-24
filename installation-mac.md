# Mac

Mac の場合、git、RubyやPythonが既にインストール済みなので、特にインストールするツールはありません。好みのエディタを使って開発を進めれば良いです。

### nodebrewのインストール

nodebrew をインストールします。既に node がインストールされている場合には「奇麗に」削除しておきます。簡単には「奇麗に」削除できませんので google で色々と検索してください。

```
$ curl -L git.io/nodebrew | perl - setup
...
========================================
Add path:

export PATH=$HOME/.nodebrew/current/bin:$PATH
========================================
```

インストール完了したら path を設定します。vi 等で「~/.bash\_profile」ファイルを開き `Add path:` で指定されたパスを登録してください。登録後保存し環境を適用させます：

```
$ source ~/.bash_profile
```

nodeバージョンを調べます。一覧でバージョンが表示されます。

```
$ nodebrew ls-remote

...

v8.0.0    v8.1.0    v8.1.1    v8.1.2    v8.1.3    v8.1.4    v8.2.0    v8.2.1
v8.3.0    v8.4.0    v8.5.0    v8.6.0    v8.7.0    v8.8.0    v8.8.1    v8.9.0
v8.9.1    v8.9.2    v8.9.3    v8.9.4    

v9.0.0    v9.1.0    v9.2.0    v9.2.1    v9.3.0    v9.4.0    v9.5.0    v9.6.0
v9.6.1    

io@v1.0.0 io@v1.0.1 io@v1.0.2 io@v1.0.3 io@v1.0.4 io@v1.1.0 io@v1.2.0 io@v1.3.0
io@v1.4.1 io@v1.4.2 io@v1.4.3 io@v1.5.0 io@v1.5.1 io@v1.6.0 io@v1.6.1 io@v1.6.2
io@v1.6.3 io@v1.6.4 io@v1.7.1 io@v1.8.1 io@v1.8.2 io@v1.8.3 io@v1.8.4 

io@v2.0.0 io@v2.0.1 io@v2.0.2 io@v2.1.0 io@v2.2.0 io@v2.2.1 io@v2.3.0 io@v2.3.1
io@v2.3.2 io@v2.3.3 io@v2.3.4 io@v2.4.0 io@v2.5.0 

io@v3.0.0 io@v3.1.0 io@v3.2.0 io@v3.3.0 io@v3.3.1  
$ 
```

利用したいバージョンの node をインストールします。

```
$ nodebrew install-binary v8.9.4
```

使う node バージョンを指定します。

```
$ nodebrew use v8.9.4
use v8.9.4
$ node -v
v8.9.4
$ npm -v
5.0.3
$
```

これで node の準備が完了しました。Mac の場合  Ruby、Python などは既にインストールされているのでほとんど作業はありません。もし Xcode がインストールされていない場合は行って下さい。

> [Node.js リリース](https://github.com/nodejs/node/releases)はとても早く、皆さんが環境構築するときにはもっと新しいバージョンが出ているかもしれません。

### yarn のインストール

yarn は npm と同様のパッケージマネージャです。yarn のインストールは簡単で

```
$ brew install yarn
```

を実行すれば完了です。

yarn の詳細は [こちら](https://yarnpkg.com/lang/ja/docs/install/) を見て頂くとアップデート方法など詳細が理解できます。
