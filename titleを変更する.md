ルーティングに応じて、headerタグにあるtitleタグを変更する処理を追加します。app.component.tsを次のように変更します。

```
import { Component } from '@angular/core';
import { Title } from '@angular/platform-browser';
import { Router, ActivatedRoute, NavigationEnd } from '@angular/router';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.scss']
})
export class AppComponent {
  constructor (
    titleService: Title,
    router: Router,
    activatedRoute: ActivatedRoute
  ) {
    router.events.subscribe(event => {
      if (event instanceof NavigationEnd) {
        let title = this.getTitle(router.routerState, router.routerState.root).join('-');
        titleService.setTitle(title);
      }
    });
  }
  getTitle(state, parent) {
    let data = [];
    if (parent && parent.snapshot.data && parent.snapshot.data.title) {
      data.push(parent.snapshot.data.title);
    }

    if (state && parent) {
      data.push(... this.getTitle(state, state.firstChild(parent)));
    }
    return data;
  }
}
```

titleに表示する名称は

`data: {title: '表示タイトル'}`

としてルータに定義します。`app.routes.ts`の定義は

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
    data: { title: 'home' }
  },
  { path: '**', component: PageNotFoundComponent }
];

export const appRoutingProviders: any[] = [];

export const routing: ModuleWithProviders = RouterModule.forRoot(appRoutes);
```

pages.routes.tsの定義は

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
    path: 'pages',
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

ページを切り替えると定義したタイトルが表示されると思いますが如何でしょう？

