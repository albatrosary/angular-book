Issue Tracker を作成します。Issue Tracker と言ってもどこにでもある Todo リストですが、Angular の特徴を活かし Component 間のやり取りの方法や Service の使い方など学びたいと思います。

## Issue Tracker の作成

issueを保存するための型を定義します

```
$ ng g class pages/issue/issue
```

内容は

```
export class Issue {
  title: string;
  desc: string;
}
```

__issue.component.ts__ は次のようになります。入力された値を配列で保持する機能と登録された情報を削除する機能を持ち合わせています。

```
import { Component, OnInit } from '@angular/core';
import { NgForm } from '@angular/forms';

import { Issue } from './issue';

@Component({
  selector: 'app-issue',
  templateUrl: './issue.component.html',
  styleUrls: ['./issue.component.css']
})
export class IssueComponent implements OnInit {

  issue: Issue;
  issues: Issue[];

  constructor () {}

  ngOnInit(): void {
    this.issue = new Issue;
    this.issues= [];
  }

  public onSubmit(form:NgForm): void {
    const issue = {
      title: form.value.title,
      desc: form.value.desc
    }

    this.issues.push(issue);

    form.reset();
  }

  public onDelete(index: number): void {
    this.issues.splice(index, 1);
  }

}
```

__issue.component.html__ は、入力部と Issue の一覧を表示する部分から成ります。

```
<h2>Issue</h2>
<form #f="ngForm" (ngSubmit)="onSubmit(f)" novalidate>
  <input name="title" ngModel required placeholder="title">
  <textarea name="desc" ngModel required placeholder="desc"></textarea>
  <button type=submit [disabled]="!f.form.valid">登録</button>
</form>
<div *ngFor="let issue of issues; let i = index">
  <p>{{issue.title}}</p>
  <pre>{{issue.desc}}</pre><div>{{i+1}}</div>
  <button (click)="onDelete(i)">削除</button>
</div>
```

簡単な Issue リストを作成することができました。ここで理解すべきことは [FORM](https://angular.io/docs/ts/latest/guide/forms.html) の使い方です。気がついたかもしれませんが、入力項目に値が入っていない場合、登録ボタンが押せなくなっています。これを応用すると、入力値のチェックやチェック後のメッセージ表示など様々なインタラクティブなことが実装できます。

Angular のバインディングは強力ですので UI を構築するときには欠かせないものです。

