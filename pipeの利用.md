フィルターのような動きをするPipeという機能があります。 文字を大文字にする機能や、頭のみ大文字にする機能、検索フィルターなどを作るための機能です。

wiki画面を使ってPipeを実装します。

## Pipeの作成

Pipeを作成します。マークダウンで入力したものをHTMLへ変換するためののPipeを作成します。

```
$ ng g pipe pages/wiki/markdown.pipe
```

「app/pages/wiki」ディレクトリにある`markdown.pipe.ts`ファイルを開き次のように記述します

```
import { Pipe, PipeTransform } from'@angular/core';
import { DomSanitizer , SafeHtml } from'@angular/platform-browser';

import * as marked from 'marked';

@Pipe({
  name: 'markdown'
})
export class MarkdownPipe implements PipeTransform {

  constructor (private sanitizer: DomSanitizer) {}

  transform(value: any, args?: any):SafeHtml {
    if ( value===undefined||value===null ) return'';
    localStorage.setItem('amke', value);
    return this.sanitizer.bypassSecurityTrustHtml(marked(value));
  }
}
```

ここで`DomSanitizer`,`SafeHtml`は通常HTMLタグを記述した場合、サニタイジング処理がされるためそれをしないようにする処理のために記載しています。

## Pipeの利用

`app.module.ts`に`markdown.pipe`が追加されていることを確認します。

`wiki.component.html`を開き次のように記載します。

```
<h2>Wiki</h2>
<textarea [(ngModel)]="wiki"></textarea>
<div [innerHtml]="wiki | markdown"></div>
```

ここで`[innerHTML]`はHTMLバインディングするための記法です。

続いて`wiki.component.ts`ファイルを開き次のように記載します。

```
import { Component, OnInit } from'@angular/core';

@Component({
  selector: 'app-wiki',
  templateUrl: './wiki.component.html',
  styleUrls: ['./wiki.component.css']
})
export class WikiComponent implements OnInit {
  wiki:string;

  constructor() { }

  ngOnInit() {
    this.wiki=localStorage.getItem('amke');
  }

}
```

wiki.module.tsに必要なライブラリを追加します。

```
import { NgModule } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { CommonModule } from '@angular/common';

import { WikiComponent } from './wiki.component';

import { MarkdownPipe } from './markdown.pipe';

@NgModule({
  imports: [
    CommonModule,
    FormsModule
  ],
  declarations: [
    WikiComponent,
    MarkdownPipe
  ]
})
export class WikiModule { }

```

  
Angular2のバインディングの仕組みとPipeを利用することで簡単にマークダウンエディタを作成することができます。

