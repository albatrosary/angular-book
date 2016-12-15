サービスを作成します

```
$ ng g service pages/issue/issue
```

`issue.service.ts`は配列の登録を記述します。

具体的には

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

