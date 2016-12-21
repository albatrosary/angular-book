[yarn](https://yarnpkg.com/) は「Fast, reliable, and secure dependency management.」と書かれているように npm  より高速にモジュールをインストールできる仕組みです。yarn を利用する場合の angular-cli は --skipe-npm スイッチを使って実行し yarn コマンドを発行します。まず yarn のインストールには npm か brew を利用します。

```
$ npm install yarn -g
```

若しくは

```
$ brew update
$ brew install yarn
```

yarn のインストールが終わったら angular-cli を使ってパッケージをインストールします。このとき npm の実行はスキップさせます。 --skip-npm スイッチを利用します。

```
$ ng new yarn-app --skip-npm
installing ng2
  create .editorconfig
  create README.md
  create src/app/app.component.css
  create src/app/app.component.html
  create src/app/app.component.spec.ts
  create src/app/app.component.ts
  create src/app/app.module.ts
  create src/assets/.gitkeep
  create src/environments/environment.prod.ts
  create src/environments/environment.ts
  create src/favicon.ico
  create src/index.html
  create src/main.ts
  create src/polyfills.ts
  create src/styles.css
  create src/test.ts
  create src/tsconfig.json
  create angular-cli.json
  create e2e/app.e2e-spec.ts
  create e2e/app.po.ts
  create e2e/tsconfig.json
  create .gitignore
  create karma.conf.js
  create package.json
  create protractor.conf.js
  create tslint.json
Successfully initialized git.
$ 
```

npm install は実行されず必要なファイルのみ生成されますのでプロジェクトディレクトリに移り yarn を実行します。



```
$ cd yarn-app
$ yarn
yarn install v0.18.0
info No lockfile found.
[1/4] 🔍  Resolving packages...
warning protractor > jasmine > glob > minimatch@0.3.0: Please update to minimatch 3.0.2 or higher to avoid a RegExp DoS issue
warning angular-cli > remap-istanbul > istanbul > fileset > minimatch@2.0.10: Please update to minimatch 3.0.2 or higher to avoid a RegExp DoS issue
[2/4] 🚚  Fetching packages...
[3/4] 🔗  Linking dependencies...
warning Incorrect peer dependency "tslint@~4.0.0".
warning Incorrect peer dependency "@angular/compiler@<=2.3 >=2.2.0".
warning Incorrect peer dependency "@angular/core@<=2.3 >=2.2.0".
warning Unmet peer dependency "@angular/tsc-wrapped@^0.5.0".
warning Unmet peer dependency "reflect-metadata@^0.1.8".
[4/4] 📃  Building fresh packages...
success Saved lockfile.
✨  Done in 39.17s.
$
```

ng serve コマンドを実行し画面を表示します。angular-cli のバージョンによりうまくインストールされるものとされないものがあります。angular-cli@1.0.0-beta.24 ではうまくインストール出来ましたが angular-cli@1.0.0-beta.22 はうまくインストールされませんでした（正確にはビルドが 70% で固まります）。

```
$ ng version
angular-cli: 1.0.0-beta.24
node: 6.7.0
os: darwin x64
@angular/common: 2.4.0
@angular/compiler: 2.4.0
@angular/core: 2.4.0
@angular/forms: 2.4.0
@angular/http: 2.4.0
@angular/platform-browser: 2.4.0
@angular/platform-browser-dynamic: 2.4.0
@angular/router: 3.4.0
@angular/compiler-cli: 2.4.0
$ 
```

yarn でのインストールはかなり早いので安定し利用できるようになるのを待ちわびる限りです。

