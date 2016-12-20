e2e テストはシナリオを幾つか設定し画面からバックエンドまで一括してテストを行います。イメージとしては総合テストの自動化という感じでしょうか。テストの実施に関しては簡易サーバ及び Express サーバをそれぞれ別のコンソールで起動します。

```
$ npm start
```

Express は

```
$ node ./server/main.js
```

です。この状態で e2e テストも別のコンソールで実行します。

```
$ ng e2e
```

簡単なシナリオを考えます

1. 画面を起動する
2. 右上にある Pages をクリックする
3. 表示された Top 画面を評価する
4. Issueページに遷移する
5. Issueデータを登録し件数を数える。

## POファイルの作成

画面毎にPOファイルを作成します。

app.po.ts は

```
import { browser, element, by } from 'protractor';

export class ProjectNamePage {
  navigateTo() {
    return browser.get('/');
  }

  getParagraphText() {
    return element(by.css('app-root h1')).getText();
  }

  nextHome () {
     element(by.css('[href="/home"]')).click();
  }

  nextPages () {
     element(by.css('[href="/pages"]')).click();
  }
}

```

app.pages.po.ts は

```
import { browser, element, by } from 'protractor';

export class PagesPage {
  getParagraphText() {
    return element(by.css('h2')).getText();
  }

  getParagraphTexts(i: number) {
    return element.all(by.css('h2')).get(i).getText();
  }

  nextTopPage () {
     element(by.css('[href="/pages/top"]')).click();
  }

  nextIssuePage () {
     element(by.css('[href="/pages/issue"]')).click();
  }

  nextWikiPage () {
     element(by.css('[href="/pages/wiki"]')).click();
  }
}

```

app.issue.po.ts は

```
import { browser, element, by } from 'protractor';

export class IssuePage {
  getInput() {
    return element(by.css('form input'));
  }

  getTextArea() {
    return element(by.css('form textarea'));
  }

  getButton() {
    return element(by.css('form button'));
  }

  getListCount() {
    return element.all(by.css('app-issue-list div app-issue-detail')).count();
  }
}

```

## シナリオの実装

シナリオを app.e2e-spec.ts に記述します。

```
import { ProjectNamePage } from './app.po';
import { PagesPage } from './app.pages.po';
import { IssuePage } from './app.issue.po';

describe('project-name App', function() {
  let page: ProjectNamePage;
  let pages: PagesPage;
  let issue: IssuePage;

  beforeEach(() => {
    page = new ProjectNamePage();
    pages = new PagesPage();
    issue = new IssuePage();
  });

  it('should display message saying Issue Tracker', () => {
    page.navigateTo();
    expect(page.getParagraphText()).toEqual('Issue Tracker');
  });

  it('should display message saying Issue Tracker after Home-link Click', () => {
    page.nextHome();
    expect(page.getParagraphText()).toEqual('Issue Tracker');
  });

  it('should display message saying バグ管理システム, 背景, 基本的な機能', () => {
    page.nextPages();
    expect(pages.getParagraphText()).toEqual('バグ管理システム');
    expect(pages.getParagraphTexts(0)).toEqual('バグ管理システム');
    expect(pages.getParagraphTexts(1)).toEqual('背景');
    expect(pages.getParagraphTexts(2)).toEqual('基本的な機能');
  });

  it('Issue Count', () => {
    pages.nextIssuePage();
    
    issue.getInput().sendKeys('テスト');
    issue.getTextArea().sendKeys('これはテストです');
    issue.getButton().click();
    expect(issue.getListCount()).toEqual(3);
  });

});

```

とても簡単なシナリオですが、こうした要領で実装します。

