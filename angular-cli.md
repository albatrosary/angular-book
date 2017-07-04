Angular アプリケーションの開発を進める上で最も簡単で効率的なツールは angular-cli を利用することです。 angular-cli は、開発に必要なライブラリをまとめていて、かつ、スカッフォールドも行えます。

## angular-cli のインストール

Node.js のインストールが済んでいれば、次に行うことは angular-cli を[インストールする](https://github.com/angular/angular-cli/blob/master/README.md)ことです。angular-cli は npm\(Node Package Manager\) を使ってインストールします。インストール完了後 `ng help` や `ng version` が実行できていれば無事インストールが完了しています。

> angular-cli のコマンドは ng を使います。ng コマンドには多くの機能が実装されてますので `ng help` で色々と確認すると良いでしょう。

```
$ npm install -g @angular/cli
$ ng version
    _                      _                 ____ _     ___
   / \   _ __   __ _ _   _| | __ _ _ __     / ___| |   |_ _|
  / △ \ | '_ \ / _` | | | | |/ _` | '__|   | |   | |    | |
 / ___ \| | | | (_| | |_| | | (_| | |      | |___| |___ | |
/_/   \_\_| |_|\__, |\__,_|_|\__,_|_|       \____|_____|___|
               |___/
@angular/cli: 1.2.0
node: 8.1.3
os: darwin x64
$ ng help
ng build <options...>
  Builds your app and places it into the output path (dist/ by default).
  aliases: b
  --target (String) (Default: development)
    aliases: -t <value>, -dev (--target=development), -prod (--target=production)
  --environment (String) (Default: )
    aliases: -e <value>
  --output-path (Path) (Default: null)
    aliases: -o <value>
  --watch (Boolean) (Default: false)
    aliases: -w
  --watcher (String)
  --suppress-sizes (Boolean) (Default: false)
  --base-href (String) (Default: null)
    aliases: -bh <value>
  --aot (Boolean) (Default: false)
  --sourcemap (Boolean) (Default: true)
    aliases: -sm
  --vendor-chunk (Boolean) (Default: true)
  --verbose (Boolean) (Default: false)
  --progress (Boolean) (Default: true)

・・・
```

もし既に古いバージョンの angular-cli がインストールされていた場合には更新を行います。

```
$ npm uninstall -g @angular/cli
$ npm cache clean
$ npm install -g @angular/cli@latest
```

尚、Angular CLI 1.0.0-beta.28 より以前のものを利用していた場合には、次のように削除します。

```
$ npm uninstall -g angular-cli
$ npm uninstall --save-dev angular-cli
```

angular-cli で私がよく利用するコマンドは

* ng new
* ng serve
* ng test
* ng e2e 
* ng g

です 。特に開発の初期では ng g はもっとも多く叩くコマンドで（ng g の g は generator の g）テンプレートを生成してくれます。  
angular-cli のコマンドは [github](https://github.com/angular/angular-cli) に詳細が書かれてますので一読されると良いと思います。

| Scaffold | Used |
| :--- | :--- |
| Component	| ng g component my-new-component	|
| Directive	| ng g directive my-new-directive	|
| Pipe | ng g pipe my-new-pipe |
| Service	| ng g service my-new-service	|
| Class	| ng g class my-new-class	|
| Guard	| ng g guard my-new-guard	|
| Interface	| ng g interface my-new-interface	|
| Enum | ng g enum my-new-enum |
| Module | ng g module my-module |

## angular-cli を使ったプロジェクトの生成

プロジェクトの生成には `ng new` を使います。このコマンドは必要なファイルを生成し、npm インストールを実行します。ここでは Hands-onプロジェクトを作成します。まずは cd ~ でホームディレクトリに移動しプロジェクトの生成を行います。

> 私はホームディレクトリ\(cd ~\) の直下に Sandbox というディレクトリを作って、そこでプロジェクト開発をしています。

```
$ cd ~
$ ng new Handson
$ cd Handson
$ ng serve
```

実際にプロジェクトを生成します。プロジェクトの作成には（ネットワークの状況にもよりますが）若干の時間を要します。下記のような`Installing packages for tooling via yarn.` というメッセージから中々応答が帰ってこないこともありますが辛抱強く待ちましょう。

```
$ ng new Handson
installing ng
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
  create src/tsconfig.app.json
  create src/tsconfig.spec.json
  create src/typings.d.ts
  create .angular-cli.json
  create e2e/app.e2e-spec.ts
  create e2e/app.po.ts
  create e2e/tsconfig.e2e.json
  create .gitignore
  create karma.conf.js
  create package.json
  create protractor.conf.js
  create tsconfig.json
  create tslint.json
Installing packages for tooling via yarn.
```

しばらくすると `Installed packages for tooling via yarn.`というメッセージが表示され無事プロジェクトが生成たれたことを示します。

```
$ ng new Handson
installing ng
  create .editorconfig
  create README.md

 ・・・

Installed packages for tooling via yarn.
Successfully initialized git.
Project 'Handson' successfully created.
$
```

プロジェクトの生成が終了したら、プロジェクトディレクトリに移動し簡易サーバを起動します。

```
$ cd Handson
$ ng serve
** NG Live Development Server is listening on localhost:4200, open your browser on http://localhost:4200 **
Hash: e82ae92c1a7c37970a7d                                                              
Time: 9304ms
chunk    {0} polyfills.bundle.js, polyfills.bundle.js.map (polyfills) 160 kB {4} [initial] [rendered]
chunk    {1} main.bundle.js, main.bundle.js.map (main) 5.28 kB {3} [initial] [rendered]
chunk    {2} styles.bundle.js, styles.bundle.js.map (styles) 10.5 kB {4} [initial] [rendered]
chunk    {3} vendor.bundle.js, vendor.bundle.js.map (vendor) 2.18 MB [initial] [rendered]
chunk    {4} inline.bundle.js, inline.bundle.js.map (inline) 0 bytes [entry] [rendered]
webpack: Compiled successfully.
```

はじめて見る方は、冒頭 `NG` と表示され何がだめなの？と思うかも知れませんがこれは ng コマンドという意味ですので勘違いしないようにしてください。

ブラウザを`http://localhost:4200/`で起動するようメッセージが表示されます。実際にブラウザを立ち上げアクセスしてみます。簡単なメッセージが表示されると思います。`ng serve`で起動した簡易サーバはライブリロードの機能が含まれているためTypeScriptファイルやHTMLファイルなどを更新した場合に自動的にブラウザが更新されます。

例えば`src/app`ディレクトリにある`app.component.ts`を更新してみます。

```
export class AppComponent {
  title = 'app';
}
```

を次のように変更してみます

```
export class AppComponent {
  title ='app sample';
}
```

TypeScriptがコンパイルされブラウザに変更した文字が表示されます。

アプリケーション開発では通常、ブラウザを起動したままTypeScriptファイルやHTMLファイル、CSSファイル\(SASSファイル\)を更新しながら進めていきます。

> 現時点では少し早いと思われる内容ですが、ng new で作成したコンポーネントの接頭詞はデフォルトでは __app__ と成っています。つまり Component に指定される selector が app-root で、以降 `ng new component` で生成されるコンポーネントには app が付きます。
>
> ```
> import { Component } from '@angular/core';
> 
> @Component({
>   selector: 'app-root',
>   templateUrl: './app.component.html',
>   styleUrls: ['./app.component.css']
> })
> export class AppComponent {
>   title = 'app';
> }
> ```
>
> 例えば `ng g component hoge` コマンドでコンポーネント生成すると
>
> ```
> import { Component, OnInit } from '@angular/core';
> 
> @Component({
>   selector: 'app-hoge',
>   templateUrl: './hoge.component.html',
>   styleUrls: ['./hoge.component.css']
> })
> export class HogeComponent implements OnInit {
> 
>   constructor() { }
> 
>   ngOnInit() {
>   }
> 
> }
> ```
>
> これを変更する場合には ng new --prefix とすると別の接頭詞になります。具体的に見てみましょう。`cd ../`として現在のプロジェクトディレクトリから外れ、新しいプロジェクトを作ります。
>
> ```
> $ ng new AngularTutorial --prefix ng
> ```
>
> とした場合に app.component.ts は
>
> ```
> import { Component } from '@angular/core';
> 
> @Component({
>   selector: 'ng-root',
>   templateUrl: './app.component.html',
>   styleUrls: ['./app.component.css']
> })
> export class AppComponent {
>   title = 'ng';
> }
> ```
>
> といった具合になります。但しディレクトリ名の app はそのままです。

## ユニットテスト

次に実際にはプロジェクトが完了したらユニットテストを実行します。 ユニットテストのコマンドは`ng test`で実行できます。

\[ctrl\]+\[c\]で`ng serve`コマンドを停止させ`ng test`コマンドを実行します。今`ng test`を実行すると次のようなエラー発行されます。

```
$ ng test
 10% building modules 1/1 modules 0 active05 07 2017 07:52:11.645:WARN [karma]: No captured browser, open http://localhost:9876/
05 07 2017 07:52:11.658:INFO [karma]: Karma v1.7.0 server started at http://0.0.0.0:9876/
05 07 2017 07:52:11.659:INFO [launcher]: Launching browser Chrome with unlimited concurrency
05 07 2017 07:52:11.666:INFO [launcher]: Starting browser Chrome
05 07 2017 07:52:19.339:WARN [karma]: No captured browser, open http://localhost:9876/  
05 07 2017 07:52:19.567:INFO [Chrome 59.0.3071 (Mac OS X 10.12.5)]: Connected on socket 5f6ppV2c5UgQHB9lAAAA with id 76162161
Chrome 59.0.3071 (Mac OS X 10.12.5) AppComponent should have as title 'app' FAILED
	Expected 'app sample' to equal 'app'.
	    at Object.<anonymous> (http://localhost:9876/_karma_webpack_/main.bundle.js:90:27)
	    at ZoneDelegate.webpackJsonp.../../../../zone.js/dist/zone.js.ZoneDelegate.invoke (http://localhost:9876/_karma_webpack_/polyfills.bundle.js:2801:26)
	    at AsyncTestZoneSpec.webpackJsonp.../../../../zone.js/dist/async-test.js.AsyncTestZoneSpec.onInvoke (http://localhost:9876/_karma_webpack_/vendor.bundle.js:2643:39)
	    at ProxyZoneSpec.webpackJsonp.../../../../zone.js/dist/proxy.js.ProxyZoneSpec.onInvoke (http://localhost:9876/_karma_webpack_/vendor.bundle.js:3406:39)
Chrome 59.0.3071 (Mac OS X 10.12.5): Executed 2 of 4 (1 FAILED) (0 secs / 0.196 secs)
Chrome 59.0.3071 (Mac OS X 10.12.5) AppComponent should have as title 'app' FAILED
	Expected 'app sample' to equal 'app'.
	    at Object.<anonymous> (http://localhost:9876/_karma_webpack_/main.bundle.js:90:27)
	    at ZoneDelegate.webpackJsonp.../../../../zone.js/dist/zone.js.ZoneDelegate.invoke (http://localhost:9876/_karma_webpack_/polyfills.bundle.js:2801:26)
	    at AsyncTestZoneSpec.webpackJsonp.../../../../zone.js/dist/async-test.js.AsyncTestZoneSpec.onInvoke (http://localhost:9876/_karma_webpack_/vendor.bundle.js:2643:39)
	    at ProxyZoneSpec.webpackJsonp.../../../../zone.js/dist/proxy.js.ProxyZoneSpec.onInvoke (http://localhost:9876/_karma_webpack_/veChrome 59.0.3071 (Mac OS X 10.12.5) AppComponent should render title in a h1 tag FAILED
	Expected '
	    Welcome to app sample!!
	  ' to contain 'Welcome to app!!'.
	    at Object.<anonymous> (http://localhost:9876/_karma_webpack_/main.bundle.js:96:58)
	    at ZoneDelegate.webpackJsonp.../../../../zone.js/dist/zone.js.ZoneDelegate.invoke (http://localhost:9876/_karma_webpack_/polyfills.bundle.js:2801:26)
	    at AsyncTestZoneSpec.webpackJsonp.../../../../zone.js/dist/async-test.js.AsyncTestZoneSpec.onInvoke (http://localhost:9876/_karma_webpack_/vendor.bundle.js:2643:39)
	    at ProxyZoneSpec.webpackJsonp.../../../../zone.js/dist/proxy.js.ProxyZoneSpec.onInvoke (http://localhost:9876/_karma_webpack_/vendor.bundle.js:3406:39)
Chrome 59.0.3071 (Mac OS X 10.12.5): Executed 3 of 4 (2 FAILED) (0 secs / 0.244 secs)
Chrome 59.0.3071 (Mac OS X 10.12.5) AppComponent should render title in a h1 tag FAILED
	Expected '
	    Welcome to app sample!!
	  ' to contain 'Welcome to app!!'.
	    at Object.<anonymous> (http://localhost:9876/_karma_webpack_/main.bundle.js:96:58)
	    at ZoneDelegate.webpackJsonp.../../../../zone.js/dist/zone.js.ZoneDelegate.invoke (http://localhost:9876/_karma_webpack_/polyfills.bundle.js:2801:26)
	    at AsyncTestZoneSpec.webpackJsonp.../../../../zone.js/dist/async-test.js.AsyncTestZoneSpec.onInvoke (http://localhost:9876/_karma_webpack_/vendor.bundle.js:2643:39)
	    at ProxyZoneSpec.webpackJsonp.../../../../zone.js/dist/proxy.js.ProxyZoneSpec.onInvoke (http://localhost:9876/_karma_webpack_/veChrome 59.0.3071 (Mac OS X 10.12.5): Executed 4 of 4 (2 FAILED) (0.335 secs / 0.278 secs)
```

先程`app.component.ts`を

```
export class AppComponent {
  title = 'app sample';
}
```

と変更したためにテストが通らなくなっています。テストコードを変更します。`app.component.spec.ts`を見て下さい。

```
・・・

it(`should have as title 'app'`, async(() => {
  const fixture = TestBed.createComponent(AppComponent);
  const app = fixture.debugElement.componentInstance;
  expect(app.title).toEqual('app');
}));

it('should render title in a h1 tag', async(() => {
  const fixture = TestBed.createComponent(AppComponent);
  fixture.detectChanges();
  const compiled = fixture.debugElement.nativeElement;
  expect(compiled.querySelector('h1').textContent).toContain('Welcome to app!!');
}));

・・・
```

ここに`app`という文字列と比較している部分あありますので、これを`app sample`に変更してください。


```
・・・

it(`should have as title 'app'`, async(() => {
  const fixture = TestBed.createComponent(AppComponent);
  const app = fixture.debugElement.componentInstance;
  expect(app.title).toEqual('app sample');
}));

it('should render title in a h1 tag', async(() => {
  const fixture = TestBed.createComponent(AppComponent);
  fixture.detectChanges();
  const compiled = fixture.debugElement.nativeElement;
  expect(compiled.querySelector('h1').textContent).toContain('Welcome to app sample!!');
}));

・・・
```

ライブリロードが実行されテストが再実行されます。結果次のようなメッセージあ表示されます。

```
Chrome 59.0.3071 (Mac OS X 10.12.5): Executed 4 of 4 SUCCESS (0.297 secs / 0.286 secs)
```

## e2eテスト

e2eテストは「End to Endテスト」と呼ばれブラウザを通してキーコード入力しクリックなどの動作テストをするためのものです。 先程と同様にコンソール画面を\[ctrl\]+\[c\]でプロセスを停止させe2eを実行します。

```
$ ng e2e
** NG Live Development Server is listening on localhost:49152, open your browser on http://localhost:49152 **
(node:14848) [DEP0022] DeprecationWarning: os.tmpDir() is deprecated. Use os.tmpdir() instead.
Hash: 2ceeea2902c0ef04e8bd
Time: 8703ms
chunk    {0} polyfills.bundle.js, polyfills.bundle.js.map (polyfills) 160 kB {4} [initial] [rendered]
chunk    {1} main.bundle.js, main.bundle.js.map (main) 6.93 kB {3} [initial] [rendered]
chunk    {2} styles.bundle.js, styles.bundle.js.map (styles) 10.5 kB {4} [initial] [rendered]
chunk    {3} vendor.bundle.js, vendor.bundle.js.map (vendor) 2.18 MB [initial] [rendered]
chunk    {4} inline.bundle.js, inline.bundle.js.map (inline) 0 bytes [entry] [rendered]
webpack: Compiled successfully.
[07:56:37] I/file_manager - creating folder /Users/albatrosary/Sandbox/Handson/node_modules/webdriver-manager/selenium
[07:56:38] I/update - chromedriver: unzipping chromedriver_2.30.zip
[07:56:38] I/update - chromedriver: setting permissions to 0755 for /Users/albatrosary/Sandbox/Handson/node_modules/webdriver-manager/selenium/chromedriver_2.30
[07:56:39] I/launcher - Running 1 instances of WebDriver
[07:56:39] I/direct - Using ChromeDriver directly...
Jasmine started

  handson App
    ✗ should display welcome message
      - Expected 'Welcome to app sample!!' to equal 'Welcome to app!!'.
          at Object.<anonymous> (/Users/albatrosary/Sandbox/Handson/e2e/app.e2e-spec.ts:12:37)
          at /Users/albatrosary/Sandbox/Handson/node_modules/jasminewd2/index.js:112:25
          at new ManagedPromise (/Users/albatrosary/Sandbox/Handson/node_modules/selenium-webdriver/lib/promise.js:1067:7)
          at ControlFlow.promise (/Users/albatrosary/Sandbox/Handson/node_modules/selenium-webdriver/lib/promise.js:2396:12)
          at schedulerExecute (/Users/albatrosary/Sandbox/Handson/node_modules/jasminewd2/index.js:95:18)
          at TaskQueue.execute_ (/Users/albatrosary/Sandbox/Handson/node_modules/selenium-webdriver/lib/promise.js:2970:14)
          at TaskQueue.executeNext_ (/Users/albatrosary/Sandbox/Handson/node_modules/selenium-webdriver/lib/promise.js:2953:27)
          at asyncRun (/Users/albatrosary/Sandbox/Handson/node_modules/selenium-webdriver/lib/promise.js:2860:25)
          at /Users/albatrosary/Sandbox/Handson/node_modules/selenium-webdriver/lib/promise.js:676:7
          at <anonymous>
          at process._tickCallback (internal/process/next_tick.js:169:7)

**************************************************
*                    Failures                    *
**************************************************

1) handson App should display welcome message
  - Expected 'Welcome to app sample!!' to equal 'Welcome to app!!'.

Executed 1 of 1 spec (1 FAILED) in 0.623 sec.
[07:56:41] I/launcher - 0 instance(s) of WebDriver still running
[07:56:41] I/launcher - chrome #01 failed 1 test(s)
[07:56:41] I/launcher - overall: 1 failed spec(s)
[07:56:41] E/launcher - Process exited with error code 1
$ 
```

ここでもユニットテストと同様の理由でエラーが発生します。e2eテストコードは「src」ディレクトリとは別の「e2e」ディレクトリに格納されています。 その中にある`app.e2e-spec.ts`というファイルを見てみます。

```
it('should display welcome message', () => {
  page.navigateTo();
  expect(page.getParagraphText()).toEqual('Welcome to app!!');
});
```

`Welcome to app!!`という文字と比較している部分がありますので先程と同様に`app sample!!`へ変更し、再度実行してみます。

```
it('should display message saying app works', () => {
  page.navigateTo();
  expect(page.getParagraphText()).toEqual('Welcome to app sample!!');
});
```

正常に実行されます。

```
$ ng e2e
** NG Live Development Server is listening on localhost:49152, open your browser on http://localhost:49152 **
(node:15085) [DEP0022] DeprecationWarning: os.tmpDir() is deprecated. Use os.tmpdir() instead.
Hash: 2ceeea2902c0ef04e8bd
Time: 9730ms
chunk    {0} polyfills.bundle.js, polyfills.bundle.js.map (polyfills) 160 kB {4} [initial] [rendered]
chunk    {1} main.bundle.js, main.bundle.js.map (main) 6.93 kB {3} [initial] [rendered]
chunk    {2} styles.bundle.js, styles.bundle.js.map (styles) 10.5 kB {4} [initial] [rendered]
chunk    {3} vendor.bundle.js, vendor.bundle.js.map (vendor) 2.18 MB [initial] [rendered]
chunk    {4} inline.bundle.js, inline.bundle.js.map (inline) 0 bytes [entry] [rendered]
webpack: Compiled successfully.
[08:00:42] I/update - chromedriver: file exists /Users/albatrosary/Sandbox/Handson/node_modules/webdriver-manager/selenium/chromedriver_2.30.zip
[08:00:42] I/update - chromedriver: unzipping chromedriver_2.30.zip
[08:00:43] I/update - chromedriver: setting permissions to 0755 for /Users/albatrosary/Sandbox/Handson/node_modules/webdriver-manager/selenium/chromedriver_2.30
[08:00:43] I/update - chromedriver: chromedriver_2.30 up to date
[08:00:43] I/launcher - Running 1 instances of WebDriver
[08:00:43] I/direct - Using ChromeDriver directly...
Jasmine started

  handson App
    ✓ should display welcome message

Executed 1 of 1 spec SUCCESS in 1 sec.
[08:00:46] I/launcher - 0 instance(s) of WebDriver still running
[08:00:46] I/launcher - chrome #01 passed
$ 
```

## ビルド

テストが完了したらリリースモジュールを作成します。リリースモジュールの作成には2種類あります。

* JiT\(Just in Time\)
* AoT\(Ahead of Time\)

AoTとJiTの違いは、タイミングとツーリングの問題です。AoTを使用すると、1組のライブラリを使用してビルド時にコンパイラが1回実行されます。JiTを使用すると、実行時に毎回異なるライブラリのセットを使用して各ユーザが実行します。

通常ビルドは次のように行います

```
$ ng build
```

「dist」ディレクトリにファイルが生成されます。

```
$ tree
.
├── favicon.ico
├── index.html
├── inline.js
├── inline.map
├── main.bundle.js
├── main.map
├── styles.bundle.js
└── styles.map

0 directories, 8 files
```

AoTビルドの場合は引数を付け加え行います。

```
$ ng build --aot true
```

同様の結果になりますが、ファイルサイズはいくらか小さくなっていて最適化されています。

```
$ tree
.
├── 0.map
├── favicon.ico
├── index.html
├── inline.js
├── inline.map
├── main.bundle.js
├── main.map
├── styles.bundle.js
└── styles.map

0 directories, 9 files
$
```

## 外部ファイルの登録

デモ作成用に幾つかファイルを追加します。詳細についてはハンズオンを進めながら行います。

```
$ npm install express body-parser cookie-parser --save-dev
$ npm install @types/express @types/body-parser @types/cookie-parser --save-dev
$ npm install angular2-express-engine angular2-platform-node angular2-universal angular2-universal-polyfills --save-dev
$ 
$ npm install marked --save
$ npm install @types/marked --save-dev
```

## 完成したコード

今回作成する簡単なWebアプリケーションの完成したコードは [github](https://github.com/albatrosary/start-angular) にあります。最終的な出来上がりを確認したい方はこちらを見て頂くと良いかと思います。

## まとめ

このように通常の開発では`ng serve`でアプリケーション開発を行い、`ng test`や`ng e2e`を使ってテストを行います。テストが完了すると`ng build`でリリースモジュールの作成をします。こうした手順の一部はCIで行うことで開発ライフサイクルの自動化をします。
