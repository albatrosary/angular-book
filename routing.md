SPA\(Single-page Application\) の最大の特徴であるルーティングについて学びます。SPAのルーティングはURLに紐付いた画面を表示させる仕組みです。

> SPA は以前のWebアプリケーションと比べ、レスポンスが高速でUI/UXに優れています。

## ルーティングの設定

はじめにルーティングに必要となるいくつかの画面を追加します。top 画面、issue 画面、wiki 画面は pages 配下として定義しています。

```
$ ng g component home
$ ng g component pageNotFound
$ ng g module pages --routing
$ ng g component pages
$ ng g component pages/top
$ ng g component pages/issue
$ ng g component pages/wiki
```

`ng g component` コマンドを実行すると4つのファイルが出来上がり、ひとつのファイルが更新されます。具体的に `ng g component home` としたときは

```
$ ng g component pages/home
installing component
  create src/app/home/home.component.css
  create src/app/home/home.component.html
  create src/app/home/home.component.spec.ts
  create src/app/home/home.component.ts
  update src/app/app.module.ts
$
```

です。__home.component.css__ は `HomeComponent` に対するCSS定義を記述するものです。__home.component.html__ は、HTMLテンプレートを記述するものです。`spec.ts` という拡張子のものがありますが、これはユニットテストを定義するものです。 __home.component.ts__ は、TypeScriptで何かしらUIに関連する処理を記述するものです。

> よく講演でコンポーネントの話をしますが、Webアプリケーション開発におけるコンポーネントは Web Components をベースにした考え方です。Web Components には次の4つの定義があります。より詳細な説明は [MDN の Web Components](https://developer.mozilla.org/ja/docs/Web/Web_Components) を参照下さい
>
> 1. Custom Element
>
> 2. HTML Templates
>
> 3. HTML Imports
>
> 4. Shadow DOM
>
> Angular は、この Web Component に準じた構成を取っています。具体的には @Component の selector で宣言しているのが Custom Element です。templateUrl で HTML Templates と HTML Imports を実現させています。
>
> Shadow DOM ですが Angular では Scoped CSS を実装し実現させています。ブラウザを開きデベロッパーツールを開いて確認すると CSS が Scoped されているのが解ります。
>
> 各ブラウザの Shadow DOM 実装はばらつきがありますが Angular ではキーワード  `ViewEncapsulation.Native` をコンポーネントに定義することで Showdow DOM を有効にすることができます。Scoped CSS 同様デベロッパーツールを開き確認すると良いでしょう。
>
> ```
> import { Component, ViewEncapsulation } from '@angular/core';
>
> @Component({
>   selector: 'app-root',
>   templateUrl: './app.component.html',
>   styleUrls: ['./app.component.css'],
>   encapsulation: ViewEncapsulation.Native
> })
> export class AppComponent {
>   title = 'app sample';
> }
> ```

これとは別にルーティング設定用のモジュールを作成します。`app-routing.module.ts` は

```
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';

import { HomeComponent } from './home/home.component';
import { PageNotFoundComponent } from './page-not-found/page-not-found.component';

const routes: Routes = [
  { path: '', redirectTo: 'home', pathMatch: 'full'},
  { path: 'home', component: HomeComponent },
  { path: 'pages', loadChildren: './pages/pages.module#PagesModule' },
  { path: '**', component: PageNotFoundComponent }
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```
> プロジェクト生成時に `--routing` パラメーターを指定してない場合には `app-routing.module.ts`  が作られません。

ここで、`path: ''` はルートパス（ ://localhost:4200/ のような）で呼ばれたときに `/home` へリダイレクトするよう定義しています。リダイレクト先の `/home` ではコンポーネント HomeComponent を実行するよう定義されていますので、画面には HomeComponent で定義されたテンプレートが表示されます。

`path: '**'` は、ルーティング定義に無いパスが指定された場合（例えば、 ://localhost:4200/xxxxx ）にPageNotFoundComponent を表示するよう定義しています。404ページとしての定義です。

続いて、`app.module.ts` は`ng g component`コマンドで必要なファイルが追加されています。確認してください。

今回はルーティングの設定がありますのでルーティングをプロバイダー登録します。結果として次のようになります

```
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';

import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';
import { HomeComponent } from './home/home.component';
import { PageNotFoundComponent } from './page-not-found/page-not-found.component';

@NgModule({
  declarations: [
    AppComponent,
    HomeComponent,
    PageNotFoundComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

`app.component.html`に aタグ を使って使って簡易メニューを作成します。

```
<h1>Issue Tracker</h1>
<ul>
  <li><a routerLink="home">Home</a></li>
  <li><a routerLink="pages">Pages</a></li>
</ul>
<router-outlet></router-outlet>
```

## 子ルーティングの設定

pages 配下にあるページに対するルーティング設定を行います。`pages-routing.module.ts` は

```
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';

import { PagesComponent } from './pages.component';
import { TopComponent } from './top/top.component';
import { IssueComponent } from './issue/issue.component';
import { WikiComponent } from './wiki/wiki.component';

const routes: Routes = [
  {
    path: '',
    component: PagesComponent,
    children: [
      { path: '', redirectTo: 'top', pathMatch: 'full'},
      { path: 'top', component: TopComponent },
      { path: 'issue', component: IssueComponent },
      { path: 'wiki', component: WikiComponent }
    ]
  }
];

@NgModule({
  imports: [RouterModule.forChild(routes)],
  exports: [RouterModule]
})
export class PagesRoutingModule { }
```

__pages.module.ts__ は

```
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';

import { PagesRoutingModule } from './pages-routing.module';
import { PagesComponent } from './pages.component';
import { TopComponent } from './top/top.component';
import { IssueComponent } from './issue/issue.component';
import { WikiComponent } from './wiki/wiki.component';

@NgModule({
  imports: [
    CommonModule,
    PagesRoutingModule
  ],
  declarations: [PagesComponent, TopComponent, IssueComponent, WikiComponent]
})
export class PagesModule { }
```

最後に、`pages.component.html`に aタグ を使って使って簡易メニューを作成します。

```
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';

import { PagesComponent } from './pages.component';
import { TopComponent } from './top/top.component';
import { IssueComponent } from './issue/issue.component';
import { WikiComponent } from './wiki/wiki.component';

const routes: Routes = [
  {
    path: '',
    component: PagesComponent,
    children: [
      { path: '', redirectTo: 'top', pathMatch: 'full'},
      { path: 'top', component: TopComponent },
      { path: 'issue', component: IssueComponent },
      { path: 'wiki', component: WikiComponent }
    ]
  }
];

@NgModule({
  imports: [RouterModule.forChild(routes)],
  exports: [RouterModule]
})
export class PagesRoutingModule { }
```

これでルーティングを定義することができました。実際に画面を動かしルーティングが出来ているか確認してください。

参考までに、ここまでの `Handon/src/app` 内のファイル構成を記載します。

```
$ tree
.
├── app-routing.module.ts
├── app.component.css
├── app.component.html
├── app.component.spec.ts
├── app.component.ts
├── app.module.ts
├── home
│   ├── home.component.css
│   ├── home.component.html
│   ├── home.component.spec.ts
│   └── home.component.ts
├── page-not-found
│   ├── page-not-found.component.css
│   ├── page-not-found.component.html
│   ├── page-not-found.component.spec.ts
│   └── page-not-found.component.ts
└── pages
    ├── pages-routing.module.ts
    ├── pages.component.css
    ├── pages.component.html
    ├── pages.component.spec.ts
    ├── pages.component.ts
    ├── pages.module.ts
    ├── issue
    │   ├── issue.component.css
    │   ├── issue.component.html
    │   ├── issue.component.spec.ts
    │   └── issue.component.ts
    ├── top
    │   ├── top.component.css
    │   ├── top.component.html
    │   ├── top.component.spec.ts
    │   └── top.component.ts
    └── wiki
        ├── wiki.component.css
        ├── wiki.component.html
        ├── wiki.component.spec.ts
        └── wiki.component.ts

6 directories, 32 files
$ 
```

ここまでの作業は非常に簡単だったと思います。特に angular-cli のお陰で、ややこしい環境設定は気にする必要無いというのは大きなメリットだと思います。
