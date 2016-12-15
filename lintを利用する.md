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

sasslint定義ファイル .sass-lint.yml を設定します。下記はサンプルとして提供されているものです。

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

sasslintを実行させるためのスクリプトをpackage.jsonに定義します。

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

これでsasslintが利用できます。実際に作成したプロジェクトに対してlintをしてください。

