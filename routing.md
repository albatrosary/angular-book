SPA\(Single-page Application\) の最大の特徴であるルーティングについて学びます。SPAのルーティングはURLに紐付いた画面を表示させる仕組みです。

## ルーティングの設定

はじめにルーティングに必要となる4つの画面を追加します。

```
$ ng g component home
$ ng g component issue
$ ng g component pageNotFound
$ ng g component wiki
```

`ng g component` コマンドを実行すると4つのファイルが出来上がります。具体的に `ng g component home` としたときは

```
$ ng g component home
installing component
  create src/app/home/home.component.css
  create src/app/home/home.component.html
  create src/app/home/home.component.spec.ts
  create src/app/home/home.component.ts
$
```

です。`home.component.css` は `home.component` に対するCSS定義を記述するものです。`home.component.html`は、HTMLテンプレートを記述するものです。`spec.ts` という拡張子のものがありますが、これはユニットテストを定義するものです。 `home.component.ts` は、TypeScriptで何かしらUIに関連する処理を記述するものです。

よく講演でコンポーネントの話をします。Webアプリケーション開発におけるコンポーネントは Web Components をベースにした考え方です。Web Components には次の4つの定義があります。

1. 
2. HTML Template
3. HTML Imports
4. Shadow DOM

これとは別にルーティング設定用のモジュールを作成します。

```
$ ng g class app.routes
```

`app.routes.ts` は

```
import { ModuleWithProviders } from'@angular/core';
import { Routes, RouterModule } from'@angular/router';

import { HomeComponent } from'./home/home.component';
import { IssueComponent } from'./issue/issue.component';
import { WikiComponent } from'./wiki/wiki.component';
import { PageNotFoundComponent } from'./page-not-found/page-not-found.component';

const appRoutes:Routes= [
  { path: '', redirectTo: 'home', pathMatch: 'full'},
  { path: 'home', component: HomeComponent },
  { path: 'issue', component: IssueComponent },
  { path: 'wiki', component: WikiComponent },
  { path: '**', component: PageNotFoundComponent }
];

export const appRoutingProviders: any[] = [];

export const routing:ModuleWithProviders = RouterModule.forRoot(appRoutes);
```

`app.module.ts` は`ng g component`コマンドで必要なファイルが追加されています。確認してください。

今回はルーティングの設定がありますのでルーティングをプロバイダー登録します。結果として次のようになります

```
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { HttpModule } from '@angular/http';

import { AppComponent } from './app.component';
import { HomeComponent } from './home/home.component';
import { IssueComponent } from './issue/issue.component';
import { PageNotFoundComponent } from './page-not-found/page-not-found.component';
import { WikiComponent } from './wiki/wiki.component';

import { routing, appRoutingProviders } from './app.routes';

@NgModule({
  declarations: [
    AppComponent,
    HomeComponent,
    IssueComponent,
    PageNotFoundComponent,
    WikiComponent
  ],
  imports: [
    BrowserModule,
    FormsModule,
    HttpModule,
    routing
  ],
  providers: [appRoutingProviders],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

`app.component.html`に aタグ を使って使って簡易メニューを作成します。

```
<h1>Issue Tracker</h1>
<ul>
  <li><a routerLink="home">Home</a></li>
  <li><a routerLink="issue">Issue</a></li>
  <li><a routerLink="wiki">Wiki</a></li>
</ul>
<router-outlet></router-outlet>
```

これでルーティングを定義することができました。実際に画面を動かしルーティングが出来ているか確認してください。

参考までに、ここまでのファイル構成を記載します。

```
$ tree 
.
├── app.component.css
├── app.component.html
├── app.component.spec.ts
├── app.component.ts
├── app.module.ts
├── app.routes.ts
├── home
│   ├── home.component.css
│   ├── home.component.html
│   ├── home.component.spec.ts
│   └── home.component.ts
├── index.ts
├── issue
│   ├── issue.component.css
│   ├── issue.component.html
│   ├── issue.component.spec.ts
│   └── issue.component.ts
├── page-not-found
│   ├── page-not-found.component.css
│   ├── page-not-found.component.html
│   ├── page-not-found.component.spec.ts
│   └── page-not-found.component.ts
└── wiki
    ├── wiki.component.css
    ├── wiki.component.html
    ├── wiki.component.spec.ts
    └── wiki.component.ts

4 directories, 23 files
$
```

ここまでの作業は非常に簡単だったと思います。特に angular-cli のお陰で、ややこしい環境設定は気にする必要無いというのは大きなメリットだと思います。

