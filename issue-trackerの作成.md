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

`issue.component.ts`ファイルは次のようになります

```
import { Component, OnInit } from'@angular/core';
import { NgForm } from'@angular/forms';

import { Issue } from'./issue';

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

issue.component.html は

```
<h2>Issue</h2>
<form #f="ngForm" (ngSubmit)="onSubmit(f)" novalidate>
  <input name="title" ngModel required placeholder="title">
  <textarea name="desc" ngModel required placeholder="desc"></textarea>
  <button type=submit [disabled]="!f.form.valid">登録</button>
</form>
<div *ngFor="let issue of issues; let i = index">
  <div>{{i+1}}</div><button (click)="onDelete(i)">削除</button>
  <p>{{issue.title}}</p>
  <pre>{{issue.desc}}</pre>
</div>
```

issue.module.tsにも必要なライブラリを追加します

```
import { NgModule } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { CommonModule } from '@angular/common';
import { IssueComponent } from './issue.component';

@NgModule({
  imports: [
    CommonModule,
    FormsModule
  ],
  declarations: [IssueComponent]
})
export class IssueModule { }

```

簡単なIssueリストを作成することができました。

