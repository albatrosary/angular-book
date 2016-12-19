## Lazy Load

Lazy Load\(遅延ロード\)とは、最初に必要なリソースのみダウンロードし、後に必要な Module を（遅延）ロードさせる機能です。今回はPagesModule をLazy Loadします。

> サンプルでは、細かくなった Component を Module という単位でまとめましたが、Module を利用するメリットのひとつとしてこのLazy Load（遅延ロード）も上げられます。

`app.routes.ts` を次のように変更します。path として`pages`を定義し loadChildren と定義します。

```
import { ModuleWithProviders } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';

import { HomeComponent } from './home/home.component';
import { PageNotFoundComponent } from './page-not-found/page-not-found.component';

import { GuardsHomeService } from './home/guards-home.service';

const appRoutes: Routes = [
  { path: '', redirectTo: 'home', pathMatch: 'full'},
  {
    path: 'home',
    component: HomeComponent,
    canActivate: [GuardsHomeService],
    canActivateChild: [GuardsHomeService],
    canDeactivate: [GuardsHomeService],
    resolve: [GuardsHomeService],
    canLoad: [GuardsHomeService],
    data: {title: 'home'}
  },
  {
    path: 'pages',
    loadChildren: './pages/pages.module#PagesModule'
  },
  { path: '**', component: PageNotFoundComponent }
];

export const appRoutingProviders: any[] = [];

export const routing: ModuleWithProviders = RouterModule.forRoot(appRoutes);
```

`pages.routes.ts` では path: 'pages' を定義していましたが、`app.routes.ts` で既に `pages` を記載してますのでここでは削除します。

```
import { ModuleWithProviders } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';

import { PagesComponent } from './pages.component';
import { IssueComponent } from './issue/issue.component';
import { IssueUpdateComponent } from './issue/issue-update/issue-update.component';
import { TopComponent } from './top/top.component';
import { WikiComponent } from './wiki/wiki.component';

import { GuardsPagesService } from './guards-pages.service';
import { GuardsIssueService } from './issue/guards-issue.service';
import { GuardsTopService } from './top/guards-top.service';
import { GuardsWikiService } from './wiki/guards-wiki.service';

const pagesRoutes: Routes = [
  {
    path: '',
    component: PagesComponent,
    canActivate: [GuardsPagesService],
    canActivateChild: [GuardsPagesService],
    canDeactivate: [GuardsPagesService],
    resolve: [GuardsPagesService],
    canLoad: [GuardsPagesService],
    children: [
      { path: '', redirectTo: 'top', pathMatch: 'full' },
      {
        path: 'top',
        component: TopComponent,
        canActivate: [GuardsTopService],
        canActivateChild: [GuardsTopService],
        canDeactivate: [GuardsTopService],
        resolve: [GuardsTopService],
        canLoad: [GuardsTopService],
        data: { title: 'Top' }
      },
      {
        path: 'issue',
        component: IssueComponent,
        canActivate: [GuardsIssueService],
        canActivateChild: [GuardsIssueService],
        canDeactivate: [GuardsIssueService],
        resolve: [GuardsIssueService],
        canLoad: [GuardsIssueService],
        data: { title: 'Issue' }
      },
      {
        path: 'issue/update/:id',
        component: IssueUpdateComponent,
        data: { title: 'Issue Update' }
      },
      {
        path: 'wiki',
        component: WikiComponent,
        canActivate: [GuardsWikiService],
        canActivateChild: [GuardsWikiService],
        canDeactivate: [GuardsWikiService],
        resolve: [GuardsWikiService],
        canLoad: [GuardsWikiService],
        data: { title: 'Wiki' }
      }
    ]
  }
];

export const pagesRoutingProviders: any[] = [];

export const pagesRouting: ModuleWithProviders = RouterModule.forChild(pagesRoutes);
```

これに加え GuardsPagesService を設定する場所も変更します。 `app.module.ts` は

```
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { HttpModule } from '@angular/http';

import { AppComponent } from './app.component';
import { HomeComponent } from './home/home.component';
import { PageNotFoundComponent } from './page-not-found/page-not-found.component';
import { FooterComponent } from './footer/footer.component';

import { routing, appRoutingProviders } from './app.routes';

import { GuardsHomeService } from './home/guards-home.service';
import { GuardsPagesService } from './pages/guards-pages.service';

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
    routing
  ],
  providers: [
    appRoutingProviders,
    GuardsHomeService,
    GuardsPagesService
  ],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

`pages.module.ts` は

```
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';

import { IssueModule } from './issue/issue.module';
import { WikiModule } from './wiki/wiki.module';

import { PagesComponent } from './pages.component';
import { TopComponent } from './top/top.component';

import { pagesRouting, pagesRoutingProviders } from './pages.routes';

import { GuardsTopService } from './top/guards-top.service';
import { GuardsWikiService } from './wiki/guards-wiki.service';
import { GuardsIssueService } from './issue/guards-issue.service';

@NgModule({
  imports: [
    CommonModule,
    IssueModule,
    WikiModule,
    pagesRouting
  ],
  providers: [
    pagesRoutingProviders,
    GuardsTopService,
    GuardsWikiService,
    GuardsIssueService
  ],
  declarations: [
    PagesComponent,
    TopComponent
  ]
})
export class PagesModule { }
```

これで設定変更は完了しました。コンソールを見ると 0.chunk.js というファイルが現れますが Lazy Load されるファイルです。

```
$ npm start

> handson@0.0.0 start /Users/albatrosary/Sandbox/Handson
> ng serve --proxy-config proxy.conf.json

** NG Live Development Server is running on http://localhost:4200. **
 10% building modules 2/2 modules 0 active[HPM] Proxy created: /api  ->  http://localhost:3000
Hash: b580323ff06306113e1c                                                               
Time: 10745ms
chunk    {0} 0.chunk.js, 0.bundle.map 72.1 kB {1} [rendered]
chunk    {1} main.bundle.js, main.bundle.map (main) 18.1 kB {3} [initial] [rendered]
chunk    {2} styles.bundle.js, styles.bundle.map (styles) 10.6 kB {4} [initial] [rendered]
chunk    {3} vendor.bundle.js, vendor.bundle.map (vendor) 2.47 MB [initial] [rendered]
chunk    {4} inline.bundle.js, inline.bundle.map (inline) 0 bytes [entry] [rendered]
webpack: bundle is now VALID.
```

## AoTビルド

AoT は Ahead of Time の略で事前ビルドです。angular-cli を使っている場合  
`$ ng build --aot true`

というコマンドで実行可能です。通常のビルドとAoTビルドのサイズ比較をしてみます。

```
$ ng build
Hash: c9c9ff6f541f5e23481d                                                               
Time: 10208ms
chunk    {0} 0.chunk.js, 0.bundle.map 263 kB {1} [rendered]
chunk    {1} main.bundle.js, main.bundle.map (main) 14 kB {3} [initial] [rendered]
chunk    {2} styles.bundle.js, styles.bundle.map (styles) 10.6 kB {4} [initial] [rendered]
chunk    {3} vendor.bundle.js, vendor.bundle.map (vendor) 2.05 MB [initial] [rendered]
chunk    {4} inline.bundle.js, inline.bundle.map (inline) 0 bytes [entry] [rendered]
$
```

```
$ ng build --aot true
Hash: ced21f2f505db318372a                                                               
Time: 12961ms
chunk    {0} 0.chunk.js, 0.bundle.map 384 kB {1} [rendered]
chunk    {1} main.bundle.js, main.bundle.map (main) 82.7 kB {3} [initial] [rendered]
chunk    {2} styles.bundle.js, styles.bundle.map (styles) 10.6 kB {4} [initial] [rendered]
chunk    {3} vendor.bundle.js, vendor.bundle.map (vendor) 1.18 MB [initial] [rendered]
chunk    {4} inline.bundle.js, inline.bundle.map (inline) 0 bytes [entry] [rendered]
$
```

サイズ以外にも大きな違いがあり、HTML Templates は、通常のビルドの場合コンパイル後の JavaScript ファイルが

```
core_1.Component({
  selector: 'hero-search',
  template: "\n  <div id=\"search-component\">\n    <h4>Hero Search</h4>\n    <input #searchBox id=\"search-box\" (keyup)=\"search(searchBox.value)\" />\n    <div>\n      <div *ngFor=\"let hero of heroes | async\"\n          (click)=\"gotoDetail(hero)\" class=\"search-result\" >\n        <p class=\"ashiras\">{{hero.name}}</p>\n      </div>\n    </div>\n  </div>\n  ",
  styleUrls: ['hero-search.component.css'],
  providers: [hero_search_service_1.HeroSearchService]
}), 
```

であるのに対し、AoT の場合は完全な JavaScript に変換されます。

```
function (rootSelector) {
  this._el_0 = this.renderer.createElement(null, 'div', this.debug(0, 5, 6));
  this.renderer.setElementAttribute(this._el_0, 'class', 'search-result');
  this._text_1 = this.renderer.createText(this._el_0, '\n        ', this.debug(1, 6, 60));
  this._el_2 = this.renderer.createElement(this._el_0, 'p', this.debug(2, 7, 8));
  this.renderer.setElementAttribute(this._el_2, 'class', 'ashiras');
  this._text_3 = this.renderer.createText(this._el_2, '', this.debug(3, 7, 27));
  this._text_4 = this.renderer.createText(this._el_0, '\n      ', this.debug(4, 7, 44));
  var disposable_0 = this.renderer.listen(this._el_0, 'click', this.eventHandler(this._handle_click_0_0.bind(this)));
  this._expr_1 = import9.UNINITIALIZED;
  this.init([].concat([this._el_0]), [
    this._el_0,
    this._text_1,
    this._el_2,
    this._text_3,
    this._text_4
  ], [disposable_0], []);
  return null;
};
```

通常このコードはブラウザにロードしたときに Angular のビルドシステムが行う作業ですが、事前にこうしたコードを生成することでブラウザ上での実行速度を早めています。

