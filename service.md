サービスを作成し DI\(Dependency injection\) を使ってコンポーネントで利用します。DI を行う際のキーワードは

* クラスの@Injectable宣言：サービス化
* コンポーネントの利用宣言：コンストラクターで宣言
* モジュールでのプロバイダ宣言

です。それぞれどのように DI 設定するのか見ていきます。

```
$ ng g service pages/issue/issue
```

`issue.service.ts`は配列の登録を記述します。具体的には

```
import { Injectable } from '@angular/core';

import { Issue } from './issue';

@Injectable()
export class IssueService {
  private issues: Issue[] = [];

  public delete(index: number): void {
    this.issues.splice(index, 1);
  }

  public add(issue: Issue): void {
    this.issues.push(issue);
  }

  public get list(): Issue[] {
    return this.issues;
  }
}
```

issue.component.tsは

```
import { Component, OnInit } from '@angular/core';
import { NgForm } from '@angular/forms';

import { Issue } from './issue';
import { IssueService } from './issue.service';

@Component({
  selector: 'app-issue',
  templateUrl: './issue.component.html',
  styleUrls: ['./issue.component.css']
})
export class IssueComponent implements OnInit {

  private issue: Issue;
  private issues: Issue[];

  constructor (
    private issueService: IssueService
  ) {}

  ngOnInit(): void {
    this.issue = new Issue;
    this.issues = this.issueService.list;
  }

  public onSubmit(form: NgForm): void {
    const issue = {
      title: form.value.title,
      desc: form.value.desc
    }

    this.issueService.add(issue);

    form.reset();
  }

  public onDelete(index: number): void {
    this.issueService.delete(index);
  }

}
```

issue.module.tsにIssueServiceを追加します

```
import { NgModule } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { CommonModule } from '@angular/common';

import { IssueComponent } from './issue.component';

import { IssueService } from './issue.service';

@NgModule({
  imports: [
    CommonModule,
    FormsModule
  ],
  declarations: [IssueComponent],
  providers: [
    IssueService
  ]
})
export class IssueModule { }
```

これでロジック部をComponentからServiceへ移動させることができました。

今回のようにあまり複雑でないようなものをサービス化する必要があるのか？という疑問もあるかもしれません。コンポーネント、特にtemplateUrl で定義されたテンプレートはUIの処理を定義し制御をそのTypeScriptファイルで実装します。言い換えると @Component で定義されたクラスは UI のためのものです。@Injectable は何かしらの処理を定義したもので UI とはあまり関係のないものを定義します。データストアを定義しそのハンドリングするメソッドを定義するのもいいですし、HTTPリクエストを処理するために定義するのも良いと思います。こうしてモジュール分割したものを接続する機能が DI だと考えれば良いかと思います。

> ReactなどでReduxなどのフレームワークがありますが、Angular でも [ngrx](https://github.com/ngrx) というものが存在し似たような構成が作れます。好みの問題もありますが ngrx を全面に採用する人もいますが私は利用しません。サンプルコードを書いてみると理解できますが Angular が提供している @Injectable が薄くなるのがもったいなく感じるためです。



