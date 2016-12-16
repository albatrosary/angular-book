## tslint

tslintは既にangular-cliに定義されています。利用方法は

```
$ npm run lint
```

とすればTypeScriptのLintが実行されます。作成したtsファイルに対してlintを実行しプログラムを修正してください。

## sasslint

sasslintはangular-cliに登録されてませんのでインストールする必要があります。

```
$ npm install sass-lint --save-dev
```

sass-lint定義ファイル .sass-lint.yml を設定します。下記はサンプルとして提供されているものです。

```
#########################
## Sample Sass Lint File
#########################
# Linter Options
options:
  # Don't merge default rules
  merge-default-rules: false
  # Set the formatter to 'html'
  # formatter: html
  # Output file instead of logging results
  # output-file: './sass-lint.html'
  # Raise an error if more than 50 warnings are generated
  max-warnings: 50
# File Options
files:
  include: 'src/**/*.scss'
  ignore:
    - 'node_modules/**/*.s+(a|c)ss'
# Rule Configuration
rules:
  extends-before-mixins: 2
  extends-before-declarations: 2
  placeholder-in-extend: 2
  mixins-before-declarations:
    - 2
    -
      exclude:
        - breakpoint
        - mq

  no-warn: 1
  no-debug: 1
  no-ids: 2
  no-important: 2
  hex-notation:
    - 2
    -
      style: uppercase
  indentation:
    - 2
    -
      size: 2
  property-sort-order:
    - 1
    -
      order:
        - display
        - margin
      ignore-custom-properties: true
#  variable-for-property:
#    - 2
#    -
#      properties:
#        - margin
#        - content
```

sass-lintを実行させるためのスクリプトをpackage.jsonに定義します。

```
"scripts": {
    "start": "ng serve --proxy-config proxy.conf.json",
    "sasslint": "sass-lint -c .sass-lint.yml -q -v",
    "lint": "tslint \"src/**/*.ts\"",
    "test": "ng test",
    "pree2e": "webdriver-manager update",
    "e2e": "protractor"
  },
```

これでsass-lintが利用できます。実際に作成したプロジェクトに対してlintをしてください。



さてここまでのファイル構成は次のようになってます。

```
$ tree -L 1 -all
.
├── .editorconfig
├── .git
├── .gitignore
├── .sass-lint.yml
├── README.md
├── angular-cli.json
├── e2e
├── karma.conf.js
├── node_modules
├── package.json
├── protractor.conf.js
├── proxy.conf.json
├── server
├── src
└── tslint.json

7 directories, 10 files
$ 
```

src/app ディレクトリ配下は次の通りです。

```
$ tree
.
├── app.component.html
├── app.component.scss
├── app.component.spec.ts
├── app.component.ts
├── app.module.ts
├── app.routes.ts
├── footer
│   ├── footer.component.html
│   ├── footer.component.scss
│   ├── footer.component.spec.ts
│   └── footer.component.ts
├── home
│   ├── guards-home.service.spec.ts
│   ├── guards-home.service.ts
│   ├── home.component.html
│   ├── home.component.scss
│   ├── home.component.spec.ts
│   └── home.component.ts
├── index.ts
├── page-not-found
│   ├── page-not-found.component.html
│   ├── page-not-found.component.scss
│   ├── page-not-found.component.spec.ts
│   └── page-not-found.component.ts
├── pages
│   ├── guards-pages.service.spec.ts
│   ├── guards-pages.service.ts
│   ├── issue
│   │   ├── guards-issue.service.spec.ts
│   │   ├── guards-issue.service.ts
│   │   ├── issue-detail
│   │   │   ├── issue-detail.component.html
│   │   │   ├── issue-detail.component.scss
│   │   │   ├── issue-detail.component.spec.ts
│   │   │   └── issue-detail.component.ts
│   │   ├── issue-input
│   │   │   ├── issue-input.component.html
│   │   │   ├── issue-input.component.scss
│   │   │   ├── issue-input.component.spec.ts
│   │   │   └── issue-input.component.ts
│   │   ├── issue-list
│   │   │   ├── issue-list.component.html
│   │   │   ├── issue-list.component.scss
│   │   │   ├── issue-list.component.spec.ts
│   │   │   └── issue-list.component.ts
│   │   ├── issue-update
│   │   │   ├── issue-update.component.html
│   │   │   ├── issue-update.component.scss
│   │   │   ├── issue-update.component.spec.ts
│   │   │   └── issue-update.component.ts
│   │   ├── issue.component.html
│   │   ├── issue.component.scss
│   │   ├── issue.component.spec.ts
│   │   ├── issue.component.ts
│   │   ├── issue.module.ts
│   │   ├── issue.service.spec.ts
│   │   ├── issue.service.ts
│   │   └── issue.ts
│   ├── pages.component.html
│   ├── pages.component.scss
│   ├── pages.component.spec.ts
│   ├── pages.component.ts
│   ├── pages.module.ts
│   ├── pages.routes.ts
│   ├── top
│   │   ├── guards-top.service.spec.ts
│   │   ├── guards-top.service.ts
│   │   ├── top.component.html
│   │   ├── top.component.scss
│   │   ├── top.component.spec.ts
│   │   └── top.component.ts
│   └── wiki
│       ├── guards-wiki.service.spec.ts
│       ├── guards-wiki.service.ts
│       ├── markdown.pipe.spec.ts
│       ├── markdown.pipe.ts
│       ├── wiki.component.html
│       ├── wiki.component.scss
│       ├── wiki.component.spec.ts
│       ├── wiki.component.ts
│       └── wiki.module.ts
└── shared
    └── properties.scss

12 directories, 71 files
$ 
```



