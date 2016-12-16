次に、ルーターを階層化します。`app.routes`では`home`及び`pages`へのルーティングを設定し`pages`では`issue`、`top`、`wiki`のルーティングを設定します。



それでは始めます。`src/app` ディレクトリ以下の現状のファイル構成は次の通りです。

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
│   ├── home.component.css
│   ├── home.component.html
│   ├── home.component.spec.ts
│   └── home.component.ts
├── index.ts
├── issue
│   ├── issue.component.css
│   ├── issue.component.html
│   ├── issue.component.spec.ts
│   └── issue.component.ts
├── page-not-found
│   ├── page-not-found.component.css
│   ├── page-not-found.component.html
│   ├── page-not-found.component.spec.ts
│   └── page-not-found.component.ts
└── wiki
    ├── wiki.component.css
    ├── wiki.component.html
    ├── wiki.component.spec.ts
    └── wiki.component.ts

4 directories, 23 files
$ 
```

pages ディレクトリを作成し issue ディレクトリと wiki ディレクトリを pagesディレクトリへ移動させます。

```
$ mkdir pages
$ mv issue/ pages/
$ mv wiki/ pages/
```

これ以外に top.component と footer.component も合わせて作ります。top.component は url に pages が指定されたときに表示させる画面で footer.component は全画面のフッターを定義します。そして、ルーティングに合わせて`Module`も次のように階層化します。

```
$ ng g component footer
$ ng g module pages
$ ng g class pages/pages.routes
$ ng g module pages/issue
$ ng g module pages/wiki
$ ng g component pages/top
```

生成された `src/app` ディレクトリ以下のファイル構成は次の通りです。

```
$ tree
.
├── app.component.css
├── app.component.html
├── app.component.spec.ts
├── app.component.ts
├── app.module.ts
├── app.routes.ts
├── footer
│   ├── footer.component.css
│   ├── footer.component.html
│   ├── footer.component.spec.ts
│   └── footer.component.ts
├── home
│   ├── home.component.css
│   ├── home.component.html
│   ├── home.component.spec.ts
│   └── home.component.ts
├── index.ts
├── page-not-found
│   ├── page-not-found.component.css
│   ├── page-not-found.component.html
│   ├── page-not-found.component.spec.ts
│   └── page-not-found.component.ts
└── pages
    ├── issue
    │   ├── issue.component.css
    │   ├── issue.component.html
    │   ├── issue.component.spec.ts
    │   ├── issue.component.ts
    │   └── issue.module.ts
    ├── pages.component.css
    ├── pages.component.html
    ├── pages.component.spec.ts
    ├── pages.component.ts
    ├── pages.module.ts
    ├── pages.routes.ts
    ├── top
    │   ├── top.component.css
    │   ├── top.component.html
    │   ├── top.component.spec.ts
    │   └── top.component.ts
    └── wiki
        ├── wiki.component.css
        ├── wiki.component.html
        ├── wiki.component.spec.ts
        ├── wiki.component.ts
        └── wiki.module.ts

7 directories, 39 files
$ 
```

> ng g component と ng g module の違いは .module.ts ファイルがあるかどうかです。例えば  pages/issue を作成した場合
>
> ```
> $ ng g module pages/issue
> installing module
>   create src/app/pages/issue/issue.module.ts
> installing component
>   create src/app/pages/issue/issue.component.css
>   create src/app/pages/issue/issue.component.html
>   create src/app/pages/issue/issue.component.spec.ts
>   create src/app/pages/issue/issue.component.ts
> $
> ```

## 子ルータの設定

テンプレートが出来たので、中身を埋めていきます。はじめに子ルータの設定を行います。`pages.component.html`は

```
<h1>pages works!</h1>
<ul>
  <li><a routerLink="top">Top</a></li>
  <li><a routerLink="issue">Issue</a></li>
  <li><a routerLink="wiki">Wiki</a></li>
</ul>
<router-outlet></router-outlet>
```

`pages.routes.ts`は

```
import { ModuleWithProviders } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';

import { PagesComponent } from './pages.component';
import { IssueComponent } from './issue/issue.component';
import { TopComponent } from './top/top.component';
import { WikiComponent } from './wiki/wiki.component';

const pagesRoutes: Routes = [
  {
    path: 'pages',
    component: PagesComponent,
    children: [
      { path: '', redirectTo: 'top', pathMatch: 'full'},
      { path: 'top', component: TopComponent },
      { path: 'issue', component: IssueComponent },
      { path: 'wiki', component: WikiComponent }
    ]
  }
];

export const pagesRoutingProviders: any[] = [];

export const pagesRouting: ModuleWithProviders = RouterModule.forChild(pagesRoutes);
```

ここで`children`キーワードと`forChild`に注意してください。`pages.module.ts`は

```
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';

import { IssueModule } from './issue/issue.module';
import { WikiModule } from './wiki/wiki.module';

import { PagesComponent } from './pages.component';
import { TopComponent } from './top/top.component';

import { pagesRouting, pagesRoutingProviders }  from './pages.routes';

@NgModule({
  imports: [
    CommonModule,
    IssueModule,
    WikiModule,
    pagesRouting
  ],
  providers: [pagesRoutingProviders],
  declarations: [
    PagesComponent,
    TopComponent
  ]
})
export class PagesModule { }
```

Issue, wikiに関してはComponent単体では無くModuleにしていますのでimports句で`IssueModule`と`WikiModule`を設定しています。

## 親ルータの設定

app.componentを変更します。まずapp.component.htmlは

```
<h1>Issue Tracker</h1>
<ul>
  <li><a routerLink="home">Home</a></li>
  <li><a routerLink="pages">Pages</a></li>
</ul>
<router-outlet></router-outlet>
<app-footer></app-footer>
```

次にapp.routes.ts

```
import { ModuleWithProviders } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';

import { HomeComponent } from './home/home.component';
import { PageNotFoundComponent } from './page-not-found/page-not-found.component';

const appRoutes: Routes = [
  { path: '', redirectTo: 'home', pathMatch: 'full'},
  { path: 'home', component: HomeComponent },
  { path: '**', component: PageNotFoundComponent }
];

export const appRoutingProviders: any[] = [];

export const routing: ModuleWithProviders = RouterModule.forRoot(appRoutes);
```

続いてapp.module.ts

```
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { HttpModule } from '@angular/http';

import { PagesModule} from './pages/pages.module'

import { AppComponent } from './app.component';
import { HomeComponent } from './home/home.component';
import { PageNotFoundComponent } from './page-not-found/page-not-found.component';
import { FooterComponent } from './footer/footer.component';

import { routing, appRoutingProviders }  from './app.routes';

@NgModule({
  declarations: [
    AppComponent,
    HomeComponent,
    PageNotFoundComponent,
    FooterComponent
  ],
  imports: [
    BrowserModule,
    FormsModule,
    HttpModule,
    PagesModule,
    routing
  ],
  providers: [appRoutingProviders],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

`IssueComponent`、`WikiComponent`の代わりに`PagesModule`をimports句に設定しています。ng serveで簡易サーバを立ち上げるとネストされたルーティングが利用出来ていることの確認ができます。



ここまでで今回作成する簡単なサンプルWebアプリケーションの骨格は出来上がりました。

