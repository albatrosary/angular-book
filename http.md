今回作成する構成は MEAN スタック と呼ばれる構成の MongoDBを除いたものです。MEAN スタック とは

* [MongoDB](https://www.mongodb.com/)
* [Express](http://expressjs.com/)
* [Angular](https://angular.io/)
* [Node.js](https://nodejs.org/ja/)

で作られる Webアプリケーション のことです。2012年頃の話ですが、MongoDB コミュニティで入門者向けに LAMP\(Linux, Apache, MySQL, PHP\) のような構成で、より簡単なものとして考え出されました。

## HTTPサーバの作成

### 準備

Node.js に必要なライブラリを登録するため yarn コマンドでライブラリを取得します。

```
$ yarn add express body-parser http -D
yarn add v1.0.2
[1/4] 🔍  Resolving packages...
[2/4] 🚚  Fetching packages...
warning Pattern ["body-parser@^1.18.2"] is trying to unpack in the same destination "/Users/albatrosary/Library/Caches/Yarn/v1/npm-body-parser-1.18.2-87678a19d84b47d859b83199bd59bce222b10454" as pattern ["body-parser@1.18.2","body-parser@^1.16.1"]. This could result in a non deterministic behavior, skipping.
[3/4] 🔗  Linking dependencies...
warning "@angular-devkit/schematics@0.0.52" has incorrect peer dependency "@angular-devkit/core@0.0.29".
warning "@ngtools/webpack@1.9.8" has incorrect peer dependency "webpack@^2.2.0 || ^3.0.0".
warning "@schematics/angular@0.1.17" has incorrect peer dependency "@angular-devkit/core@0.0.29".
warning "@schematics/angular@0.1.17" has incorrect peer dependency "@angular-devkit/schematics@0.0.52".
warning "extract-text-webpack-plugin@3.0.2" has incorrect peer dependency "webpack@^3.1.0".
warning "file-loader@1.1.9" has incorrect peer dependency "webpack@^2.0.0 || ^3.0.0".
warning "html-webpack-plugin@2.30.1" has incorrect peer dependency "webpack@1 || ^2 || ^2.1.0-beta || ^2.2.0-rc || ^3".
warning "istanbul-instrumenter-loader@3.0.0" has incorrect peer dependency "webpack@^2.0.0 || ^3.0.0".
warning "less-loader@4.0.5" has incorrect peer dependency "less@^2.3.1".
warning "less-loader@4.0.5" has incorrect peer dependency "webpack@^2.0.0 || ^3.0.0".
warning "license-webpack-plugin@1.1.1" has incorrect peer dependency "webpack-sources@>=1.0.0".
warning "sass-loader@6.0.6" has incorrect peer dependency "node-sass@^4.0.0".
warning "sass-loader@6.0.6" has incorrect peer dependency "webpack@^2.0.0 || >= 3.0.0-rc.0 || ^3.0.0".
warning "stylus-loader@3.0.1" has incorrect peer dependency "stylus@>=0.52.4".
warning "uglifyjs-webpack-plugin@1.2.2" has incorrect peer dependency "webpack@^2.0.0 || ^3.0.0 || ^4.0.0".
warning "url-loader@0.6.2" has incorrect peer dependency "file-loader@*".
warning "webpack-dev-middleware@1.12.2" has incorrect peer dependency "webpack@^1.0.0 || ^2.0.0 || ^3.0.0".
warning "webpack-dev-server@2.11.1" has incorrect peer dependency "webpack@^2.2.0 || ^3.0.0".
warning "webpack-subresource-integrity@1.0.4" has incorrect peer dependency "webpack@^1.12.11 || ~2 || ~3".
warning "schema-utils@0.4.5" has incorrect peer dependency "webpack@^2.0.0 || ^3.0.0 || ^4.0.0".
warning "ajv-keywords@2.1.1" has incorrect peer dependency "ajv@^5.0.0".
warning "uglifyjs-webpack-plugin@0.4.6" has incorrect peer dependency "webpack@^1.9 || ^2 || ^2.1.0-beta || ^2.2.0-rc || ^3.0.0".
warning "ajv-keywords@3.1.0" has incorrect peer dependency "ajv@^6.0.0".
warning "ajv-keywords@1.5.1" has incorrect peer dependency "ajv@>=4.10.0".
[4/4] 📃  Building fresh packages...
success Saved lockfile.
success Saved 3 new dependencies.
├─ body-parser@1.18.2
├─ express@4.16.2
└─ http@0.0.0
✨  Done in 26.53s.
$ 
```

### アプリケーションサーバの作成

REST処理を行うためサーバを構築します。「server/main.js」として簡単なRESTサーバを作成します

```
'use strict';

const express = require('express');
const bodyParser = require('body-parser');
const app = express();
const server = require('http').createServer(app);
const port =  process.env.PORT || 3000;

app.use(bodyParser.json({limit: '50mb'}));
app.use(bodyParser.urlencoded({extended: true, limit: '50mb'}));

app.use(function(req, res, next) {
  res.header('Access-Control-Allow-Methods', 'GET, PUT, POST, DELETE, OPTION');
  res.header('Access-Control-Allow-Origin', '*');
  res.header('Access-Control-Allow-Headers', 'Origin, X-Requested-With, Content-Type, Accept');
  next();
});

// Start server
server.listen(port, process.env.OPENSHIFT_NODEJS_IP || process.env.IP || undefined, function() {
  console.log('Express server listening on %d, in %s mode', port, app.get('env'));
});

const items = require('./issues.json');

app.get('/api/issues', function(req, res) {
  res.status(200).json(items);
});

app.get('/api/issues/:id', function(req, res) {
  let id = req.params.id;
  res.status(200).json(items[id]);
});

app.post('/api/issues', function(req, res) {
  items.push(req.body);
  res.status(200).json();
});

app.put('/api/issues', function(req, res) {
  let id = req.body.id;
  let issue = req.body.issue;
  items[id] = JSON.parse(issue);
  res.status(200).json();
});

app.delete('/api/issues/:id', function(req, res) {
  let id = req.params.id;
  items.splice(Number(id), 1);
  res.status(200).json();
});

exports = module.exports = app;
```

テストデータ\(issues.json\)も同じディレクトリに作成します

```
[{
  "title": "テスト１",
  "desc": "これはテスト２"
},
{
  "title": "テスト２",
  "desc": "これはテスト２"
}]
```

コマンドライン

```
$ node ./server/main.js
Express server listening on 3000, in development mode
```

で起動することができます

### プロキシ設定

`ng serve`はプロキシを設定することができます。具体的には proxy.conf.json を プロジェクトの直下に配置し

```
$ ng serve --proxy-config proxy.conf.json
```

この`proxy.conf.json`は次のように定義できます。

```
{
  "/api": {
    "target": "http://localhost:3000",
    "secure": false
  }
}
```

package.json にプロキシ設定された簡易サーバを立ち上げるようにscriptsを記述します。

```
  "scripts": {
    "ng": "ng",
    "start": "ng serve --proxy-config proxy.conf.json",
    "build": "ng build --prod",
    "test": "ng test",
    "lint": "ng lint",
    "lint:sass": "./node_modules/sass-lint/bin/sass-lint.js -c sass-lint.yml -v -q",
    "e2e": "ng e2e"
  },
```

いままでは`ng serve`で起動してましたが、プロキシを利用するため`npm start`で起動します。

```
$ npm start

> handson@0.0.0 start /Users/albatrosary/Sandbox/Handson
> ng serve --proxy-config proxy.conf.json

** NG Live Development Server is running on http://localhost:4200. **
 10% building modules 2/2 modules 0 active[HPM] Proxy created: /api  ->  http://localhost:3000
Hash: 41ef7b51fe5f4eb2d5c7                                                               
Time: 11585ms
chunk    {0} main.bundle.js, main.bundle.map (main) 45.4 kB {2} [initial] [rendered]
chunk    {1} styles.bundle.js, styles.bundle.map (styles) 9.99 kB {3} [initial] [rendered]
chunk    {2} vendor.bundle.js, vendor.bundle.map (vendor) 2.5 MB [initial] [rendered]
chunk    {3} inline.bundle.js, inline.bundle.map (inline) 0 bytes [entry] [rendered]
webpack: bundle is now VALID.
```

サーバモジュールも入りファイルの構成が分かりづらいので一度整理します。 proxy.conf.json など適切な配置になってますか？

```
$ tree -L 2
.
├── README.md
├── angular-cli.json
├── e2e
│   ├── app.e2e-spec.ts
│   ├── app.po.ts
│   └── tsconfig.json
├── karma.conf.js
├── node_modules
├── package.json
├── protractor.conf.js
├── proxy.conf.json
├── server
│   ├── issues.json
│   └── main.js
├── src
│   ├── app
│   ├── assets
│   ├── environments
│   ├── favicon.ico
│   ├── index.html
│   ├── main.ts
│   ├── polyfills.ts
│   ├── styles.css
│   ├── test.ts
│   ├── tsconfig.json
│   └── typings.d.ts
└── tslint.json

892 directories, 20 files
$
```

## サービスの書き換え

issue.service.ts は RxJS の Promise 等を使って実装します。

```
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';

import { Observable } from 'rxjs/Observable';
import { catchError } from 'rxjs/operators';

import { Issue } from './issue';

@Injectable()
export class IssueService {

  private headers = new Headers({'Content-Type': 'application/json'});

  private url = '/api/issues';

  constructor(private http: HttpClient) { }

  public delete(index: number) {
    console.log(`${this.url}/${index}`);
    this.http.delete(`${this.url}/${index}`)
     .toPromise();
  }

  public add(issue: Issue): Observable<Issue> {
    return this.http.post<Issue>(this.url, JSON.stringify(issue))
      .pipe(catchError(this.handleError));
  }

  public update(id: number, issue: Issue): void {
    let udata = {
      id: id,
      issue: JSON.stringify(issue)
    };

    this.http.put(this.url, udata)
      .toPromise()
      .catch(this.handleError);
  }

  public get list(): Observable<Issue[]> {
    return this.http.get<Issue[]>(this.url)
      .pipe(catchError(this.handleError));
  }

  public getIssue(id: number): Observable<Issue> {
    return this.http.get<Issue>(this.url + `/${id}`)
      .pipe(catchError(this.handleError));
  }

  private handleError(error: any) {
    console.error('An error occurred', error);
    return Promise.reject(error.message || error);
  }

}
```
## IssueModule に HTTPモジュールの登録

HTTPモジュールを利用するため IssueModule に HttpClientModule を登録します。Angular 4 以前のバージョンでは HttpModule を利用してましたが、以降では HttpClientModule を使います。HttpClientModule を使うと HttpModule より簡素に記載することができます。

```
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { FormsModule } from '@angular/forms';
import { HttpClientModule } from '@angular/common/http';

import { IssueComponent } from './issue.component';

import { IssueService } from './issue.service';
import { IssueDetailComponent } from './issue-detail/issue-detail.component';
import { IssueInputComponent } from './issue-input/issue-input.component';
import { IssueListComponent } from './issue-list/issue-list.component';
import { IssueUpdateComponent } from './issue-update/issue-update.component';

@NgModule({
  imports: [
    CommonModule,
    FormsModule,
    HttpClientModule
  ],
  exports: [IssueComponent],
  declarations: [IssueComponent, IssueDetailComponent, IssueInputComponent, IssueListComponent, IssueUpdateComponent],
  providers: [IssueService]
})
export class IssueModule { }
```

## IssueListComponent の書き換え
 
IssueListComponent は 削除イベントを IssueComponent へ通知するように変更しています。

```
import { Component, OnInit, Input, Output, EventEmitter } from'@angular/core';

import { Issue } from'../issue';

@Component({
  selector: 'ah-issue-list',
  templateUrl: './issue-list.component.html',
  styleUrls: ['./issue-list.component.sass']
})
export class IssueListComponent implements OnInit {

  @Input() issues: Issue[];

  constructor (
  ) {}

  public ngOnInit () { }

  @Output('onDelete')
  private _onDelete = new EventEmitter<number>();
  public onDelete(index: number): void {
    this._onDelete.emit(index);
  }
  
}
```

## IssueUpdateComponent の変更

issue-update.component.html は

```
<form #f="ngForm" (ngSubmit)="onSubmit(f)" novalidate>
  <input class="id" name="id" [(ngModel)]="id" required placeholder="id">
  <input name="title" [(ngModel)]="title" required placeholder="title">
  <textarea name="desc" [(ngModel)]="desc" required placeholder="desc"></textarea>
  <button type=submit [disabled]="!f.form.valid">更新</button>
</form>
```

issue-update.component.ts は

```
import { Component, OnInit } from '@angular/core';
import { NgForm } from '@angular/forms';
import { Router, ActivatedRoute, Params } from '@angular/router';

import 'rxjs/add/operator/switchMap';

import { IssueService } from '../issue.service';
import { Issue } from '../issue';

@Component({
  selector: 'ah-issue-update',
  templateUrl: './issue-update.component.html',
  styleUrls: ['./issue-update.component.sass']
})
export class IssueUpdateComponent implements OnInit {


  id: number;

  title: string;

  desc: string;

  constructor(
    private router: Router,
    private route: ActivatedRoute,
    private issueService: IssueService
  ) {
  }

  ngOnInit() {
    this.route.params
      .switchMap((params: Params) => {
        this.id = +params['id'];
        return this.issueService.getIssue(this.id);
      })
      .subscribe(issue => {
        this.title = issue.title;
        this.desc = issue.desc;
      });
  }

  public onSubmit(form: NgForm): void {

    const issue = {
      title: form.value.title,
      desc: form.value.desc
    };

    this.issueService.update(form.value.id, issue);

    this.gotoIssue();
  }

  private gotoIssue() {
    this.router.navigate(['./pages/issue']);
  }
}
```

## IssueUpdateComponent のルータへの追加

pages-routing.module.ts に UpdateComponentの設定を追加します

```
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';

import { PagesComponent } from './pages.component';
import { TopComponent } from './top/top.component';
import { IssueComponent } from './issue/issue.component';
import { IssueUpdateComponent } from './issue/issue-update/issue-update.component';
import { WikiComponent } from './wiki/wiki.component';

const routes: Routes = [
  {
    path: '',
    component: PagesComponent,
    children: [
      { path: '', redirectTo: 'top', pathMatch: 'full'},
      { path: 'top', component: TopComponent },
      { path: 'issue', component: IssueComponent },
      { path: 'issue/update/:id', component: IssueUpdateComponent },
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

## IssueComponent の整理

IssueComponent で IssueService の管理をしています。データを受け渡すための設定を issue.component.html に記載します

```
<h2>Issue</h2>
<ah-issue-input
  (onSubmit)="onSubmit($event)">
></ah-issue-input>
<ah-issue-list
  [issues]="issues"
  (onDelete)="onDelete($event)">
></ah-issue-list>
```

issue.component.ts は各子コンポーネントへのデータの受け渡しと各子コンポーネントからの通知を取得します。

```
import { Component, OnInit } from '@angular/core';
import { NgForm } from '@angular/forms';

import { Issue } from'./issue';
import { IssueService } from'./issue.service';

@Component({
  selector: 'ah-issue',
  templateUrl: './issue.component.html',
  styleUrls: ['./issue.component.sass']
})
export class IssueComponent implements OnInit {

  issues: Issue[]

  constructor (
    private issueService: IssueService
  ) {}

  ngOnInit(): void {
    this.issueService.list
      .subscribe(response => this.issues = response)
  }
  
  onSubmit(issue: Issue) {
    this.issueService.add(issue);
    this.issues.push(issue);
  }

  public onDelete(index: number) {
    this.issueService.delete(index);
    this.issues.splice(index, 1);
  }
}
```


## IssueInputComponent のイベント通知


IssueComponent でサービスを管理していますので、IssueInputComponent から登録通知を行うよう変更します

```
import { Component, OnInit, Output, EventEmitter } from'@angular/core';
import { NgForm } from '@angular/forms';

import { Issue } from '../issue';

@Component({
  selector: 'ah-issue-input',
  templateUrl: './issue-input.component.html',
  styleUrls: ['./issue-input.component.sass']
})
export class IssueInputComponent implements OnInit {

  constructor() {
  }

  ngOnInit() {
  }

  @Output("onSubmit")
  private _onSubmit = new EventEmitter<Issue>();
  public onSubmit(form: NgForm): void {
    const issue = {
      title: form.value.title,
      desc: form.value.desc
    };
    this._onSubmit.emit(issue);
    form.reset();
  }

}
```

これでHTTPリクエストに対する簡単な処理が完了しました。

