Angularはコンポーネント単位でのScoped CSSであるため無駄な識別子を定義する必要が無く見通しの良いCSSを作ることができます。

SASSを利用するための設定はとても簡単です。angular-cli.jsonを変更します。

```
{
  "project": {
    "version": "1.0.0-beta.22-1",
    "name": "project-name"
  },
  "apps": [
    {
      "root": "src",
      "outDir": "dist",
      "assets": [
        "assets",
        "favicon.ico"
      ],
      "index": "index.html",
      "main": "main.ts",
      "test": "test.ts",
      "tsconfig": "tsconfig.json",
      "prefix": "app",
      "mobile": false,
      "styles": [
        "styles.scss"
      ],
      "scripts": [],
      "environments": {
        "source": "environments/environment.ts",
        "dev": "environments/environment.ts",
        "prod": "environments/environment.prod.ts"
      }
    }
  ],
  "addons": [],
  "packages": [],
  "e2e": {
    "protractor": {
      "config": "./protractor.conf.js"
    }
  },
  "test": {
    "karma": {
      "config": "./karma.conf.js"
    }
  },
  "defaults": {
    "styleExt": "scss",
    "prefixInterfaces": false,
    "inline": {
      "style": false,
      "template": false
    },
    "spec": {
      "class": false,
      "component": true,
      "directive": true,
      "module": false,
      "pipe": true,
      "service": true
    }
  }
}
```

angular-cliのcssと書かれている部分をscssに変更するとng g componentコマンドを実行したときにcssではなくscssを出力します。またscssのビルドはすでに準備されているので拡張子を変更すれば特に設定は必要ありません。

### 共通SCSSの配置

app/shared/properties.scssに変数を定義します。ここではヘッダーとフッターのバックカラーを定義します。

```
$corporate-color: #2196F3;
$font-color: #FFFFFF;
```

app/style.scssには画面共通で利用する設定をします。

```
/* You can add global styles to this file, and also import other style files */

body {
  margin: 0;
}

form {
  margin: 5px;
}

section {
  margin: 0 5px;
}

button {
  width: 100px;
  text-align: center;
  line-height: 32px;
  outline: none;
  background-color: #FFFFFF;
  border: 2px solid #333333;
  font-size: 1rem;
  box-sizing: border-box;
}

input, textarea {
  margin: 3px 0;
  padding: 7px 0;
  font-size: 1.2rem;
  border: 1px solid #333333;
  box-sizing: border-box;
  background-color: #FFFFFF;
}
```

各コンポーネントのSCSS及びHTMLは次のように変更します

### footer.component.html

```
<footer>&copy; ashiras, inc.</footer>
```

### footer.component.scss

```
@import '../shared/properties';

footer {
  margin: 5px 0;
  padding: 5px 0;
  text-align: center;
  font-size: 0.8rem;
  background-color: $corporate-color;
  color: $font-color;
}
```

### home.component.html

```
<section>
  <h1>Angular 2: The Future of JavaScript is Now</h1>
  <h2>ANGULAR CLI</h2>
  <p>Angular2をインストールする最良の方法は、Angular CLI Toolを使用することです。</p>
  <p>Angular CLIはAngular 2 Applicationの足場を生成するのに役立つだけでなく、開発者、生成、サービス、テスト、および管理に役立つ一連のコマンドが付属しています。 Angularを利用するする人々から聞いた主な不平の一つは、それがどこで始まり、どこで終わるのかを理解することが難しいことです。 Yeoman、Grunt、Gulp、そしてあらゆる種類のツールを使って真の発展を作り出していることです。 環境は、今やすべてがフレームワークコマンドラインインターフェイスによって提供されます。</p>

  <h2>ANGULAR UNIVERSAL</h2>
  <p>Angular2のアプリケーション向けのサーバーサイドレンダリングが存在します。Angular 1 では SEO に苦しめられましたが Universal を利用することでそれを回避できます そして、サイトのプレビュー、最適化された検索エンジン、そしてはるかに改善されたパフォーマンスを得ることができます。</p>

  <h2>ANGULAR MOBILE TOOL KIT</h2>
  <p>インストール可能なオフライン対応のモバイルネットワーク対応のAngular 2 Appsをすぐに作成するためのモバイルツールキットも提供しています。</p>

  <h2>ANGULAR AUGURY</h2>
  <p>Angular 2を使うと、デバッグがかなり難しいですが、Angular Augury Toolを利用することで回避できます。 これはChrome2プラグインです。 実際にコンポーネントツリーをグラフィック表現で管理しています。</p>

  <h2>ANGULAR MATERIAL 2</h2>
  <p>Angular Material 2コンポーネントではコミュニティに沿ってGoogle Developersチームの協力もあり 以前よりも強力なコンポーネントを作成しています。</p>

  <h2>ANGULAR FIREBASE 2</h2>
  <p>Angular Firebase 2というツールがあります。リアルタイムアプリケーション用に設計されているもので簡単に利用することができます。</p>

  <h2>ANGULAR 2 STYLE GUIDE</h2>
  <p>Angular2を開発するためのガイドラインを提示しています。</p>

  <h2>ANGULAR REFERENCES</h2>
  <p>Angular 2の最善の教科書は ANGULAR REFERENCES です。Angular 2のツールとチュートリアルに関する情報とリソースを見つけることができるセクションを提供しました。</p>

  <h2>NATIVE SCRIPT 2.0</h2>
  <p>Angular 2フレームワークを完全にサポートしたNative Script 2.0が存在します。WebビューでDOMをレンダリングせずにAngular 2を使ってネイティブアプリケーションを書くことができます。</p>

  <h2>CODELYZER</h2>
  <p>Angular 2アプリケーションのコードアナライザは、Angular 2スタイルガイドに従っているかどうかを確認します。</p>
</section>
```

### page-not-found.component.html

```
<p>404</p>
```

### page-not-found.component.scss

```
@import '../shared/properties';

p {
  display: flex;
  font-size: 10rem;
  justify-content: center;
  align-items: center;
  color: $corporate-color;
  background-image: url(./assets/images/angular.png);
  background-repeat: no-repeat;
  background-position: center center; 
}
```

angular.pngは [angular presskit](https://angular.io/presskit.html) からダウンロードできます。

### issue-input.component.scss

```
input, textarea {
  width: 100%;
}
```

### issue-list.component.scss

```
div {
  margin: 5px;
  padding: 3px;
  border: 1px solid #999999;
}
```

### issue-update.component.scss

```
input, textarea {
  width: 100%;
}

.id {
  border: 0px;
}
```

### issue.component.scss

```
:host {
  margin: 5px;
}
```

### top.component.html

```
<section>
  <h2>バグ管理システム</h2>

  <p>バグ管理システム、バグトラッキングシステム[1]とはプロジェクトのバグを登録し、修正状況を追跡するシステム。バグ管理システムの多くは、ウェブサーバ上で動作し、ウェブブラウザ経由でアクセスできるようになっている。バグ管理システムはソフトウェアを開発する上での必須のもの[要出典]になりつつある。</p>

  <h2>背景</h2>
  <p>近年、ソフトウェアの開発においてはバグの修正が重要な作業と考えられている。バグを漏らさず修正し、再発を防ぐには、バグの発見日時や発見者、再現方法、修正担当者、修正履歴、修正方法、重要度、テスト状況などの多くの情報を残し管理する必要がある。開発によっては数千という数のバグが発生し、また多数のテスト担当者や修正担当者が関わっていることを考慮すると、従来のファイルレベルの管理では追いつかなくなっている。このような背景から、バグを管理するソフトウェアであるバグ管理システムが生まれた。</p>

  <h2>基本的な機能</h2>
  <ul>
    <li>バグの集中管理 - バグの投稿〜完了までのバグ情報が集中管理される。ワークフローやバグの属性など詳細はバグ管理システムにより異なる。</li>
    <li>バグの検索 - 既存のバグが検索できる。キーワード検索やクエリ検索などがある。</li>
    <li>バグの履歴管理 - バグの対応状況を詳しく把握できる。</li>
    <li>電子メール通知機能 - バグが更新される際に修正内容がメールで通知される。</li>
  </ul>
</section>
```

### top.component.scss

```
ul {
  padding: 0 5px;
}

li {
  list-style: none;
}
```

### wiki.component.scss

```
:host {
  display: flex;
}

textarea {
  margin: 0 5px;
  width: 49%;
  height: 300px;
}

div {
  margin: 0 5px;
  width: 49%;
}
```

### pages.component.scss

```
ul {
  display: flex;
  padding: 0 3px;
  justify-content: center;
  align-items: center;
}

li {
  list-style: none;
  padding: 0 10px;
}

a {
  padding: 0 20px;
  font-size: 1.5rem;
  text-decoration: none;
  color: #888888;
}
```

### app.component.scss

```
@import './shared/properties';

ul {
  display: flex;
  margin: 24px 0;
  align-items: flex-end;
}

h1 {
  width: 100%
}

div {
  display: flex;
  padding: 0 3px;
  background-color: $corporate-color;
  color: $font-color;
}

li {
  list-style: none;
  padding: 0 5px;
}

a {
  text-decoration: none;
  color: $font-color;
}

```



