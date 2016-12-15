# 階層化されたルーティング

次に、ルーターを階層化します。`app.routes`では`home`及び`pages`へのルーティングを設定し`pages`では`issue`、`top`、`wiki`のルーティングを設定します。

```
root
 |- footer
 |- home
 |- page-not-found
 |- pages
   |- issue
   |- top
   |- wiki
```

ルーティングに合わせて`Module`も次のように階層化します。

```
app
 |- footer
 |- home
 |- page-not-found
 |- pages
 | |- issue
 | |  |- issue.module.ts
 | |- top
 | |- wiki
 | |- pages.module.ts
 | |- pages.routes.ts
 |- app.module.ts
 |- app.routes.ts
```

`angular-cli`のコマンドを利用して作成します。その前に`pages`ディレクトリを作成し`issue`、`wiki`のディレクトリをpages配下へ移動してください。

```
$ ng g component footer
$ ng g module pages
$ ng g class pages/pages.routes
$ ng g module pages/issue
$ ng g module pages/wiki
$ ng g component pages/top
```

## 子ルータの設定

はじめに子ルータの設定を行います。`pages.component.html`は

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

ここで`children`キーワードと`forChild`に注意してください。

`pages.module.ts`は

```
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';

import { IssueModule } from './issue/issue.module';

import { PagesComponent } from './pages.component';
import { TopComponent } from './top/top.component';
import { WikiComponent } from './wiki/wiki.component';

import { pagesRouting, pagesRoutingProviders }  from './pages.routes';

@NgModule({
  imports: [
    CommonModule,
    IssueModule,
    pagesRouting
  ],
  providers: [pagesRoutingProviders],
  declarations: [
    PagesComponent,
    TopComponent,
    WikiComponent
  ]
})
export class PagesModule { }
```

Issueに関してはComponent単体では無くModuleにしていますのでimports句で`IssueModule`を設定しています。

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

`IssueComponent`、`WikiComponent`の代わりに`PagesModule`をimports句に設定しています。

ng serveで簡易サーバを立ち上げるとネストされたルーティングが利用出来ていることの確認ができます。

