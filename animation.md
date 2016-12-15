pages.componentのメニューにアニメーションを設定します。

準備としてpages.component.htmlを次のように書き換えます

```
<div>
  <ul>
    <li *ngFor="let menu of links; let i = index">
      <a routerLink="{{menu.url}}"
        [@menuState]="menu.state" (click)="onClick(i)">
        {{menu.name}}
      </a>
    </li>
  </ul>
</div>
<router-outlet></router-outlet>

```

pages.component.tsにアニメーションを定義します。アニメーションは@Componentのanimations句で実装します。

```
import { Component, OnInit, Input, trigger, state, style, transition, animate } from '@angular/core';

@Component({
  selector: 'app-pages',
  templateUrl: './pages.component.html',
  styleUrls: ['./pages.component.scss'],
  animations: [
    trigger('menuState', [
      state('inactive', style({
        transform: 'scale(1)'
      })),
      state('active', style({
        borderBottom: '2px solid #333333',
        transform: 'scale(1.2)'
      })),
      transition('inactive => active', animate('300ms ease-in')),
      transition('active => inactive', animate('300ms ease-out'))
    ])
  ]
})
export class PagesComponent implements OnInit {

  menuState: string;

  links = [{
    url: 'top',
    name: 'Top',
    state: 'active'
  }, {
    url: 'issue',
    name: 'Issue',
    state: 'inactive'
  }, {
    url: 'wiki',
    name: 'Wiki',
    state: 'inactive'
  }];

  constructor() {
    this.menuState = 'inactive';
  }

  ngOnInit() {
  }

  onClick(i: number) {
    this.links.forEach(
      (element, index, array) => element.state = (i === index) ? 'active' : 'inactive'
    );
  }
}

```



