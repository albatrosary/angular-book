Router と Component には何かしらの生成、消滅などイベントが発生したときに実行されるイベントトリガーのような機能があります。

* Router Guards
* Component Lifecycle Hook
* Router Event

実際のアプリケーション開発ではよく使う（ページ遷移して良いのか良くないのかを判断するための処理を記載することは良くあります）ことがありますが、ここでは実験的にプログラムを記載してみます。

# Router Guards

ルーティングには [Routing Guards](https://angular.io/docs/ts/latest/guide/router.html) という機能があり、ページ遷移するときのイベント処理やセキュリティとして問題がある場合に遷移禁止等処理が行えるようになっています。具体的には5つの Guards があります。

| Guards | **内容** |
| :--- | :--- |
| CanActivate | ルートへのナビゲーション評価 |
| CanActivateChild | 小ルートへのナビゲーション評価 |
| CanDeactivate | 現在のルートからナビゲーションを評価 |
| Resolve | アクティブ化する前のプリフェッチ評価 |
| CanLoad | 非同期ルートの評価 |

について Guards を定義します。

```
$ ng g guard app
  create src/app/app.guard.spec.ts (340 bytes)
  create src/app/app.guard.ts (400 bytes)
$ 
```

生成されるファイル __app.quard.ts__ は

```
import { Injectable } from '@angular/core';
import { CanActivate, ActivatedRouteSnapshot, RouterStateSnapshot } from '@angular/router';
import { Observable } from 'rxjs/Observable';

@Injectable()
export class AppGuard implements CanActivate {
  canActivate(
    next: ActivatedRouteSnapshot,
    state: RouterStateSnapshot): Observable<boolean> | Promise<boolean> | boolean {
    return true;
  }
}
```

### HomeComponent に Routing Guards を定義

今回は処理をするというよりも、コンソールにログを出力することのみ記載しています。

```
import { Injectable } from '@angular/core';
import { CanActivate, ActivatedRouteSnapshot, RouterStateSnapshot } from '@angular/router';
import { Observable } from 'rxjs/Observable';

@Injectable()
export class AppGuard implements CanActivate {
  canActivate(
    next: ActivatedRouteSnapshot,
    state: RouterStateSnapshot): Observable<boolean> | Promise<boolean> | boolean {
      console.log('canActivate', 'next', next, 'state', state);
    return true;
  }
}
```

これを app-routing.module.ts に設定します。

```
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';

import { HomeComponent } from './home/home.component';
import { PageNotFoundComponent } from './page-not-found/page-not-found.component';

import { AppGuard } from './app.guard';

const routes: Routes = [
  { path: '', redirectTo: 'home', pathMatch: 'full'},
  { path: 'home', component: HomeComponent, canActivate: [AppGuard] },
  { path: 'pages', loadChildren: './pages/pages.module#PagesModule', canActivate: [AppGuard] },
  { path: '**', component: PageNotFoundComponent, canActivate: [AppGuard] }
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

次に app.module.ts にプロバイダ登録します。

```
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';
import { FormsModule }   from '@angular/forms';

import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';
import { HomeComponent } from './home/home.component';
import { PageNotFoundComponent } from './page-not-found/page-not-found.component';

import { AppGuard } from './app.guard';

@NgModule({
  declarations: [
    AppComponent,
    HomeComponent,
    PageNotFoundComponent
  ],
  imports: [
    BrowserModule,
    FormsModule,
    AppRoutingModule
  ],
  providers: [AppGuard],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

さて、ブラウザを起動しコンソールを開いて下さい。メニューをクリックするとログが表示されます。この後は、もし戻りが false だったらといった具合に各自で評価してください。

# Component Lifecycle Hooks

Component の Lifecycle Hooks については  [angular.io](https://angular.io/docs/ts/latest/guide/lifecycle-hooks.html) に詳しく書いています。実際にもう利用していて

```
ngOnInit() {
}
```

はそれにあたります。これ以外には

* ngOnChanges
* ngOnInit
* ngDoCheck
* ngAfterContentInit
* ngAfterContentChacked
* ngAfterViewInit
* ngAfterViewChacked
* ngOnDestroy

があります。 constructor も含め処理タイミングを確認すると良いでしょう。

> 注意事項として constructor に多くの処理を記述しないのがベターです。基本的に初期処理は ngOnInit に記載すべきです。

# Router Event

Router の状態に応じてイベントが取れます

| Router Event | Description |
|: --- |: --- |
| NavigationStart	| ナビゲーションの開始時に実行されるイベント |
| RoutesRecognized | ルータがURLを解析してルートが認識されたときに実行されるイベント |
| RouteConfigLoadStart | ルータ遅延がルート設定をロードする前に実行されるイベント。 |
| RouteConfigLoadEnd | ルートが遅延ロードされた後に実行されるイベント |
| NavigationEnd	| ナビゲーションが正常に終了したときに実行されるイベント |
| NavigationCancel | ナビゲーションがキャンセルされたときに実行されるイベント。これは、ナビゲーション中にルートガードがfalseを返すためです |
| NavigationError	| 予期しないエラーのためにナビゲーションが失敗したときに実行されるイベント |

例えば `app.component.ts` で利用する場合には次のようにします。

```
import { Component } from '@angular/core';
import { Router, ActivatedRoute, NavigationStart } from '@angular/router';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  title = 'app';
  constructor(private router: Router) {
    this.router.events
      .filter(e => e instanceof NavigationStart)     
      .pairwise()
      .subscribe((e) => { console.log(e); }); 
   }
}
```

RxJS のオペレータを有効にするため filter, pairwise を追加します

```
import { Component } from '@angular/core';

import { Router, ActivatedRoute, NavigationStart } from '@angular/router';

import 'rxjs/add/operator/filter';
import 'rxjs/add/operator/pairwise';

@Component({
  selector: 'ah-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.sass']
})
export class AppComponent {
  title = 'ah';
  constructor(private router: Router) {
    this.router.events
      .filter(e => e instanceof NavigationStart)     
      .pairwise()
      .subscribe((e) => { console.log(e); }); 
   }
}
```
