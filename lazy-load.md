## Lazy Load

Lazy Load\(遅延ロード\)で最初に必要なリソースのみダウンロードし、後に必要なModuleをロードさせる機能です。今回はpages.moduleをLazy Loadします。

app.routes.ts を次のように変更します。pathとして`pages`を定義しloadChildren句を定義します。

```
import { ModuleWithProviders } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';

import { HomeComponent } from './home/home.component';
import { PageNotFoundComponent } from './page-not-found/page-not-found.component';

const appRoutes: Routes = [
  { path: '', redirectTo: 'home', pathMatch: 'full'},
  { path: 'home', component: HomeComponent, data: {title: 'home'} },
  { path: 'pages',
    loadChildren: './pages/pages.module#PagesModule'
  },
  { path: '**', component: PageNotFoundComponent, data: {title: 'Page Not Found'}  }
];

export const appRoutingProviders: any[] = [];

export const routing: ModuleWithProviders = RouterModule.forRoot(appRoutes);
```

pages.routes.ts ではpath: 'pages'を定義していましたが、app.routes.ts で既にpagesを記載してますのでここでは削除します。

```
import { ModuleWithProviders } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';

import { PagesComponent } from './pages.component';
import { TopComponent } from './top/top.component';
import { IssueComponent } from './issue/issue.component';
import { IssueUpdateComponent } from './issue/issue-update/issue-update.component';
import { WikiComponent } from './wiki/wiki.component';

const pagesRoutes: Routes = [
  { path: '',
    component: PagesComponent,
    children: [
      { path: '', redirectTo: 'top', pathMatch: 'full'},
      { path: 'top', component: TopComponent, data: {title: 'Top'} },
      { path: 'issue', component: IssueComponent, data: {title: 'Issue'} },
      { path: 'issue/update/:id', component: IssueUpdateComponent, data: {title: 'Issue Update'} },
      { path: 'wiki', component: WikiComponent, data: {title: 'Wiki'} }
    ]
  }
];

export const pagesRoutingProviders: any[] = [];

export const pagesRouting: ModuleWithProviders = RouterModule.forChild(pagesRoutes);
```

コンソールを見ると 0.chunk.js というファイルが現れます。これがLazy Loadされるファイルです。

```
$ npm start

> project-name@0.0.0 start /Users/albatrosary/Sandbox/start-angular2
> ng serve --proxy-config proxy.conf.json

** NG Live Development Server is running on http://localhost:4200. **
 10% building modules 2/2 modules 0 active[HPM] Proxy created: /api  ->  http://localhost:3000
Hash: cfcd02ebef4ad9736e41                                                               
Time: 10195ms
chunk    {0} 0.chunk.js, 0.bundle.map 263 kB {1} [rendered]
chunk    {1} main.bundle.js, main.bundle.map (main) 14 kB {3} [initial] [rendered]
chunk    {2} styles.bundle.js, styles.bundle.map (styles) 10.6 kB {4} [initial] [rendered]
chunk    {3} vendor.bundle.js, vendor.bundle.map (vendor) 2.27 MB [initial] [rendered]
chunk    {4} inline.bundle.js, inline.bundle.map (inline) 0 bytes [entry] [rendered]
webpack: bundle is now VALID.
```

## AoTビルド

AoTはAhead of Timeの略で事前ビルドです。angular-cliを使っている場合  
`ng build --aot true`

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



