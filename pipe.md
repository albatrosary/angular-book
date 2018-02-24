フィルターのような動きをする Pipe という機能があります。 文字を大文字にする機能や、頭のみ大文字にする機能、検索フィルターなどを作るための機能です。

WikiComponent（マークダウンエディタ）を使って Pipe を実装します。

## Pipe の作成

Pipe を作成します。マークダウンで入力したものをHTMLへ変換するためののPipeを作成します。

```
$ ng g pipe pages/wiki/markdown
  create src/app/pages/wiki/markdown.pipe.spec.ts (195 bytes)
  create src/app/pages/wiki/markdown.pipe.ts (205 bytes)
  update src/app/pages/pages.module.ts (691 bytes)
$ 
```

「app/pages/wiki」ディレクトリにある`markdown.pipe.ts`ファイルを開き次のように記述します

```
import { Pipe, PipeTransform } from　'@angular/core';
import { DomSanitizer, SafeHtml } from　'@angular/platform-browser';

import * as marked from 'marked';

@Pipe({
  name: 'markdown'
})
export class MarkdownPipe implements PipeTransform {

  constructor (private sanitizer: DomSanitizer) {}

  transform(value: any, args?: any): SafeHtml {
    if ( value === undefined || value === null ) {
      return　'';
    }
    localStorage.setItem('amke', value);
    return this.sanitizer.bypassSecurityTrustHtml(marked(value));
  }
}
```

ここで`DomSanitizer`,`SafeHtml`は通常HTMLタグを記述した場合、サニタイジング処理がされるためそれをしないようにする処理のために記載しています。

## Pipe の利用

`pages.module.ts`に`markdown.pipe`が追加されていることを確認します。

```
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';

import { PagesRoutingModule } from './pages-routing.module';
import { PagesComponent } from './pages.component';
import { TopComponent } from './top/top.component';
import { IssueComponent } from './issue/issue.component';
import { WikiComponent } from './wiki/wiki.component';
import { MarkdownPipe } from './wiki/markdown.pipe';

@NgModule({
  imports: [
    CommonModule,
    PagesRoutingModule
  ],
  declarations: [PagesComponent, TopComponent, IssueComponent, WikiComponent, Markdown.PipePipe, MarkdownPipe]
})
export class PagesModule { }
```

`wiki.component.html`を開き次のように記載します。

```
<h2>Wiki</h2>
<textarea [(ngModel)]="wiki"></textarea>
<div [innerHtml]="wiki | markdown"></div>
```

ここで`[innerHTML]`はHTMLバインディングするための記法で div タグで囲まれた部分にバインディングされた値が挿入されます。

> \[\(\)\] は双方向バインディングを意味します。\[innerHTML\]='wiki' は、wiki が受け取った値を画面に表示する「方向」で、\(click\)='onClick\(\)'は画面が受け取ったイベントをonClick\(\)メソッド実行させる「方向」で実行されます。
>
> 比喩した表現ですが、\[innerHTML\]='wiki' を右から左という向き定義したとすると\(click\)='onClick\(\)'は左から右という方向になります。ここから\[\(\)\]が「双方向」である意味が理解できると思います。

続いて`wiki.component.ts`ファイルを開き次のように記載します。

```
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'ah-wiki',
  templateUrl: './wiki.component.html',
  styleUrls: ['./wiki.component.sass']
})
export class WikiComponent implements OnInit {
  
  wiki: string;

  constructor() { }

  ngOnInit() {
    this.wiki = localStorage.getItem('amke');
  }

}
```

`pages.module.ts` に必要なライブラリ（FormsModule）を追加します。

```
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { FormsModule } from '@angular/forms';

import { PagesRoutingModule } from './pages-routing.module';
import { PagesComponent } from './pages.component';
import { TopComponent } from './top/top.component';
import { IssueComponent } from './issue/issue.component';
import { WikiComponent } from './wiki/wiki.component';
import { MarkdownPipe } from './wiki/markdown.pipe';

@NgModule({
  imports: [
    CommonModule,
    FormsModule,
    PagesRoutingModule
  ],
  declarations: [PagesComponent, TopComponent, IssueComponent, WikiComponent, MarkdownPipe]
})
export class PagesModule { }
```

Angular のバインディングの仕組みとPipeを利用することで、数行のコードで簡単にマークダウンエディタを作成することができます。

### Web Storage API

よく使う [Web Storage](https://developer.mozilla.org/ja/docs/Web/API/Web_Storage_API) について簡単に説明します。Web Storage はキーバリューストアで

* localStorage
* sessionStorage

のふたつが存在します。何れもブラウザ内でデータを保持するための仕組みですが次のような特徴がありますのでアプリケーション開発ではそれぞれのメリットを理解し利用することをオススメします。

|  | 別タブでの共有 | データの有効期限 |
| :--- | :--- | :--- |
| localStorage | ◯ | ブラウザを閉じたり開いたりしてもデータを維持することができます |
| sessionStorage | ☓ | ページのセッション中でブラウザを閉じると消去されます |

データ容量は OS やブラウザで異なりますので[ストレージ容量を報告しているサイト](http://dev-test.nemikor.com/web-storage/support-test/)で確認すると良いでしょう。
