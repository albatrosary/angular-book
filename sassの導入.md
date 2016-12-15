SASSを利用するための設定はとても簡単です。angular-cli.jsonを変更します。

```
{
  "project": {
    "version": "1.0.0-beta.22-1",
    "name": "project-name"
  },
  "apps": [
    {
      "root": "src",
      "outDir": "dist",
      "assets": [
        "assets",
        "favicon.ico"
      ],
      "index": "index.html",
      "main": "main.ts",
      "test": "test.ts",
      "tsconfig": "tsconfig.json",
      "prefix": "app",
      "mobile": false,
      "styles": [
        "styles.scss"
      ],
      "scripts": [],
      "environments": {
        "source": "environments/environment.ts",
        "dev": "environments/environment.ts",
        "prod": "environments/environment.prod.ts"
      }
    }
  ],
  "addons": [],
  "packages": [],
  "e2e": {
    "protractor": {
      "config": "./protractor.conf.js"
    }
  },
  "test": {
    "karma": {
      "config": "./karma.conf.js"
    }
  },
  "defaults": {
    "styleExt": "scss",
    "prefixInterfaces": false,
    "inline": {
      "style": false,
      "template": false
    },
    "spec": {
      "class": false,
      "component": true,
      "directive": true,
      "module": false,
      "pipe": true,
      "service": true
    }
  }
}
```

angular-cliのcssと書かれている部分をscssに変更するとng g componentコマンドを実行したときにcssではなくscssを出力します。またscssのビルドはすでに準備されているので拡張子を変更すれば特に設定は必要ありません。

### 共通SCSSの配置

assets/properties.scssに変数を定義します。ここではヘッダーとフッターのバックカラーを定義します。

```
$corporate-color: #2196F3;
$font-color: #FFFFFF;
```

### footer

footer.component.html

```
<footer>&copy; ashiras, inc.</footer>
```

footer.component.scss

```
@import './../../assets/properties';

footer {
  margin: 5px 0;
  padding: 5px 0;
  text-align: center;
  font-size: 0.8rem;
  background-color: $corporate-color;
  color: #FFFFFF;
}
```

f

