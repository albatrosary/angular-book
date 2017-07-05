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

v7.6.0    

v7.7.0    v7.7.1    v7.7.2    v7.7.3    v7.7.4    

v7.8.0    

v7.9.0    

v7.10.0   

v8.0.0    

v8.1.0    v8.1.1    v8.1.2    v8.1.3    
$ 
```

利用したいバージョンの node をインストールします。

```
$ nodebrew install-binary v8.1.3
```

使う node バージョンを指定します。

```
$ nodebrew use v8.1.3
use v8.1.3
$ node -v
v8.1.3
$ npm -v
5.0.3
$
```

これで node の準備が完了しました。Mac の場合  Ruby、Python などは既にインストールされているのでほとんど作業はありません。もし Xcode がインストールされていない場合は行って下さい。

> [Node.js リリース](https://github.com/nodejs/node/releases)はとても早く、皆さんが環境構築するときにはもっと新しいバージョンが出ているかもしれません。

### yarn のインストール

yarn は npm と同様のパッケージマネージャです。yarn は早く、しっかりと、そして確実に行います。yarn のインストールは簡単で

```
$ brew install yarn
```

を実行すれば完了です。

