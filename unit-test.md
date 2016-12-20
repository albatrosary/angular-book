これまでのアプリケーションから少し離れ Unit テスト用のコードを記述します。新しくプロジェクトを作成しましょう。作るアプリケーションは一覧にデータを表示する単純なものです。

```
$ ng new unit-test
```

プロジェクトが出来たらテスト用のアプリケーションを作成します。ジェネレータを使って必要なモジュールを生成しましょう

```
$ ng g component table
$ ng g service table/post
$ ng g class table/post
```

作成された src/app ディレクトリのファイル構成は次の通りです

```
$ tree
.
├── app.component.css
├── app.component.html
├── app.component.spec.ts
├── app.component.ts
├── app.module.ts
├── index.ts
└── table
    ├── post.service.spec.ts
    ├── post.service.ts
    ├── post.ts
    ├── table.component.css
    ├── table.component.html
    ├── table.component.spec.ts
    └── table.component.ts

1 directory, 13 files
$
```

## テスト用の簡単なアプリケーションを作る

TableComponent の table.component.html テンプレートは

```
<table>
  <thead>
    <tr>
      <th>Post Title</th>
      <th>Post Author</th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td>{{ post.title }}</td>
      <td>{{ post.author }}</td>
    </tr>
  </tbody>
</table>
```

table.component.ts は post をインターフェースに持つ単純なものです。

```
import { Component, OnInit, Input } from '@angular/core';

import { Post } from './post';

@Component({
  selector: 'app-table',
  templateUrl: './table.component.html',
  styleUrls: ['./table.component.css']
})
export class TableComponent implements OnInit {

  @Input()
  post: Post;

  constructor() { }

  ngOnInit() {
  }

}
```

ここで利用する Post は title と author を持ちコンストラクタでデータの受け取りが出来るクラスとします。

```
export class Post {

  public title: number;

  public author: string;

  constructor(post: any) {
    this.title = post.title;
    this.author = post.author;
  }
}
```

データを取得するためのサービス post.service.ts は

```
import { Injectable } from '@angular/core';
import { Http } from '@angular/http';

import 'rxjs/add/operator/toPromise';

import { Post } from './post';

@Injectable()
export class PostService {

  constructor(private http: Http) { }

  public getPost(): any {
    return this.http.get('/aseets/post.json')
      .toPromise()
      .then((res: any) => res.json())
      .catch((e: any) => console.error(e));
  }
}
```

データを作成します。 aseets ディレクトリに post.json ファイルを作成します。

```
{
  "title": "コレ1枚でわかる最新ITトレンド",
  "author": "やまだ"
}
```

あとは TableComponent を表示させるための AppComponent を定義するだけです。app.component.html は

```
<div *ngIf="isDataLoaded">
  <app-table
    [post]="post">
  </app-table>
</div>
```

app.component.ts は

```
import { Component } from '@angular/core';

import { Post } from './table/post';
import { PostService } from './table/post.service';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  public isDataLoaded: boolean = false;

  public post: Post;

  constructor(private postService: PostService) { }

  ngOnInit(): void {
    this.postService.getPost()
    .then((post: Post) => {
      this.post = new Post(post);
      this.isDataLoaded = true;
    });
  }
}
```

最後に app.module.ts を記載すれば終わりです。

```
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { HttpModule } from '@angular/http';

import { AppComponent } from './app.component';
import { TableComponent } from './table/table.component';

import { PostService } from './table/post.service';

@NgModule({
  declarations: [
    AppComponent,
    TableComponent
  ],
  imports: [
    BrowserModule,
    FormsModule,
    HttpModule
  ],
  providers: [ PostService ],
  bootstrap: [ AppComponent ]
})
export class AppModule { }
```

準備はここまでです。 `ng serve` を実行しプラウザで表示してください。単純な画面が表示されます。

### Post のテスト

post.ts は単純なクラスなのでテストもそれほど難しいものではありません。 angular-cli では class に対してテストコードは作りませんので自身で作成します。 post.spec.ts ファイルを作成しましょう

post.spec.ts の内容は次の通りです。テストデータを定義して expect による評価を行っています。

```
import { Post } from './post';

let testPost = {title: 'TestPost', author: 'Admin'};

describe('Post', () => {
  it('checks Post properties', () => {
    let post = new Post(testPost);
    expect(post instanceof Post).toBe(true);
    expect(post.title).toBe('TestPost');
    expect(post.author).toBe('Admin');
  });
});
```

### PostService のテスト

PostService は HTTP を使ってますので MockBackend と MockConnection を利用し擬似的に HTTP 通信を行います。

```
/* tslint:disable:no-unused-variable */

import {
  BaseRequestOptions,
  Response,
  ResponseOptions,
  Http,
  HttpModule
} from '@angular/http';
import {
  MockBackend,
  MockConnection
} from '@angular/http/testing';
import {
  inject,
  async,
  TestBed
} from '@angular/core/testing';

import { PostService } from './post.service';

describe('MockBackend: TestService', () => {
  beforeEach(() => {
    // Сделаем все нужные тестовые сервисы
    TestBed.configureTestingModule({
      providers: [
        PostService,
        MockBackend,
        BaseRequestOptions,
        {
          provide: Http,
          useFactory: (backend, options) => new Http(backend, options),
          deps: [MockBackend, BaseRequestOptions]
        }
      ],
      imports: [
        HttpModule
      ]
    });
  });

  it('returns the PostService',
    async(inject([PostService, MockBackend], (postService: PostService, backend: MockBackend) => {
      backend.connections
        .subscribe((connection: MockConnection) => {
          connection.mockRespond(
            new Response(
              new ResponseOptions({ body: '{"title": "TestPost", "author": "Admin"}' })
            )
          );
        });

      return postService.getPost()
        .then((data) => {
          expect(data).toEqual({'title': 'TestPost', 'author': 'Admin'});
        });
    }))
  );
});
```

次に PostService のモックを作成します。このモックは PostService を利用するコンポーネントのためのものです。モックファイルを post.service.mock.ts というファイルを作成します。

```
import { PostService } from './post.service';
import { Observable } from 'rxjs';

export class MockPostService extends PostService {
  public getPost(): any {
    return Observable
      .fromPromise(Promise.resolve({'title': 'TestPost', 'author': 'Admin'}))
      .toPromise();
  }
}
```

### TableComponent のテスト

TableComponent のテストでは TableComponent を呼び出すためのラッパークラスを定義しラッパークラスを生成することで TableComponent を呼び出しています。

```
/* tslint:disable:no-unused-variable */

import { Component } from '@angular/core';
import { TestBed, async } from '@angular/core/testing';

import { Post } from './post';
import { TableComponent } from './table.component';

import { PostService } from './post.service';
import { MockPostService } from './post.service.mock';

import { By } from '@angular/platform-browser';

@Component({
  selector  : 'test-cmp',
  template  : '<app-table [post]="postMock"></app-table>'
})
class TestCmpWrapper {
  public postMock;
}

describe('TableComponent', () => {
  beforeEach(() => {
    TestBed.configureTestingModule({
      declarations: [
        TestCmpWrapper,
        TableComponent
      ]
    });
  });

  describe('check rendering', () => {
    it('if component is rendered', async(() => {
      TestBed.compileComponents().then(() => {
        let fixture = TestBed.createComponent(TestCmpWrapper);
        let componentInstance = fixture.componentInstance;

        componentInstance.postMock = new Post({title: 'TestPost', author: 'Admin'});

        fixture.detectChanges();

        let td1 = fixture.debugElement.query(By.css('tr td:nth-child(1)')).nativeElement;
        expect(td1.innerText).toBe('TestPost');
        let td2 = fixture.debugElement.query(By.css('tr td:nth-child(2)')).nativeElement;
        expect(td2.innerText).toBe('Admin');
      });
    }));
  });
});
```

### AppComponent のテスト

最後に AppComponent をテストします。ここでは AppComponent を表示させるための TestCmpWrapper と TableComponent のラッパー TestTableCmpWrapper を定義しています。そして TableService のモックを利用しテストを行っています。

```
/* tslint:disable:no-unused-variable */

import { Component, Input } from '@angular/core';
import { TestBed, async } from '@angular/core/testing';
import { Post } from './table/post';
import { AppComponent } from './../app/app.component';
import { HttpModule } from '@angular/http';

import { By } from '@angular/platform-browser';

import { PostService } from './table/post.service';
import { MockPostService } from './table/post.service.mock';

@Component({
  selector  : 'test-app',
  template  : '<app-root></app-root>'
})
class TestCmpWrapper {}

@Component({
  selector  : 'app-table',
  template  : '<div>{{post}}</div>'
})
class TestTableCmpWrapper {
  @Input() public post: any;
}

describe('AppComponent', () => {
  beforeEach(() => {
    TestBed.configureTestingModule({
      declarations: [
        TestTableCmpWrapper,
        AppComponent,
        TestCmpWrapper
      ],
      providers: [
        {provide: PostService, useClass: MockPostService}
      ],
      imports: [
        HttpModule
      ]
    });
  });

  describe('check rendering', () => {
    it('if component is rendered', async(() => {
      TestBed.compileComponents().then(() => {
        let fixture = TestBed.createComponent(TestCmpWrapper);
        let componentInstance = fixture.componentInstance;

        fixture.detectChanges();

        let appRoot = fixture.debugElement.query(By.css('app-root')).nativeElement;

        fixture.autoDetectChanges();

        fixture.whenStable().then(() => {
          let appRootAfter = fixture.debugElement.query(By.css('app-root')).nativeElement;
        });
      });
    }));
  });
});
```

Anuglar Unit Test の基本的なところをまとめました。このテストコードは [github](https://github.com/albatrosary/angular-unit-test/) にあります。

