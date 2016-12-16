## サーバの作成

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
  items.splice(id, 1);
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

## プロキシ設定

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

package.jsonにプロキシ設定された簡易サーバを立ち上げるようにscriptsを記述します。

```
"scripts": {
    "start": "ng serve --proxy-config proxy.conf.json",
    "lint": "tslint \"src/**/*.ts\"",
    "test": "ng test",
    "pree2e": "webdriver-manager update",
    "e2e": "protractor"
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

issue.service.ts はRxJSのPromiseを使って実装します。

    import { Injectable } from '@angular/core';
    import { Headers, Http } from '@angular/http';

    import { Observable } from 'rxjs';
    import 'rxjs/add/operator/toPromise';

    import { Issue } from './issue';

    @Injectable()
    export class IssueService {

      private headers = new Headers({'Content-Type': 'application/json'});

      private url = '/api/issues';

      private issues: Issue[] = [];

      constructor(private http: Http) { }

      public delete(index: number): Promise<Issue[]> {
        return this.http.delete(this.url + `/${index}`, { headers: this.headers })
          .toPromise()
          .then(() => this.issues.splice(index, 1))
          .catch(this.handleError);
      }

      public add(issue: Issue): void {
        this.http.post(this.url, JSON.stringify(issue), { headers: this.headers })
          .toPromise()
          .then(() => this.issues.push(issue))
          .catch(this.handleError);
      }

      public update(id: number, issue: Issue): void {
        let udata = {
          id: id,
          issue: JSON.stringify(issue)
        };

        this.http.put(this.url, udata, {headers: this.headers})
          .toPromise()
          .catch(this.handleError);
      }

      public allList(): Promise<Issue[]> {
        return this.http.get(this.url)
          .toPromise()
          .then(response => {
            this.issues = response.json();
            return this.issues;
          })
          .catch(this.handleError);
      }

      public getIssue(id: number): Promise<Issue> {
        return this.http.get(this.url + `/${id}`)
          .toPromise()
          .then(response => {
            this.issues = response.json();
            return this.issues;
          })
          .catch(this.handleError);
      }

      private handleError(error: any) {
        console.error('An error occurred', error);
        return Promise.reject(error.message || error);
      }
    }

## Componentの書き換え

issue-list.componentはPromise実装に伴い若干の処理を追加しています。

```
import { Component, OnInit } from '@angular/core';

import { Issue } from '../issue';
import { IssueService } from '../issue.service';

@Component({
  selector: 'app-issue-list',
  templateUrl: './issue-list.component.html',
  styleUrls: ['./issue-list.component.css']
})
export class IssueListComponent implements OnInit {

  private issues: Issue[];

  constructor (
    private issueService: IssueService
  ) {}

  ngOnInit() {
    this.issueService.allList()
      .then(response => this.issues = response)
      .catch(error => console.log(error));
  }

  public onDelete(index: number): void {
    this.issueService.delete(index)
      .catch(error => console.log(error));
  }

}
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
  selector: 'app-issue-update',
  templateUrl: './issue-update.component.html',
  styleUrls: ['./issue-update.component.css']
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

これでHTTPリクエストに対する簡単な処理が完了しました。

