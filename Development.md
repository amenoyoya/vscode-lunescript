# Development for VSCode syntax highlight extension

## Environment

- Editor: VSCode
- Node.js: `16.10.0`
    - npm: `8.1.1`
        - yo: `4.3.0`
        - generator-code: `1.6.2`
        - vsce: `1.100.2`

### Setup
```bash
# install globally: yo, generator-code, vsce
$ npm i -g yo generator-code vsce
```

- **yo**:
    - Google製の総合開発ツールである Yeoman の一部 (yeoman-generator)
    - プロジェクトの雛形作成ツール
- **generator-code**:
    - VSCode extension 用の雛形作成ツール
    - yo に依存する
- **vsce**:
    - VSCode extension のパッケージングツール

***

## Development

### Creating a project
```bash
# create a project: `vscode-lunescript`
$ yo code

## What type of extension do you want to create? => `New Language Support`
## URL or file to import, or none for new: => `` (empty)
## What's the name of your extension? => `LuneScript Syntax`
## What's the identifier of your extension? => `vscode-lunescript`
## What's the description of your extension? => `Provides LuneScript syntax for Visual Studio Code`
## Enter the id of the language. => `lunescript`
## Enter the name of the language. => `LuneScript`
## Enter the file extensions of the language. => `.lns`
## Enter the root scope name of the grammar. => `source.lns`

# open the project by VSCode
$ code vscode-lunescript
```

※対話形式で設定した内容は後から `package.json` を書き直して修正可能

### Creating a syntax highlight
- `./syntaxes/{language_id}.tmLanguage.json` を編集してシンタックスハイライトを作成する
    - `name` の名前によって色が変わる
        - 参照: https://raw.githubusercontent.com/martinring/tmlanguage/master/tmlanguage.json
    - `match` もしくは `begin` - `end` に正規表現を定義し、マッチングパターンを設定する
    - 複数のマッチングパターンが必要な場合は `patterns` 配列に記述する
    - `repository` 配下に記述したマッチングパターンは `"include": "#pattern-name"` で流用することができる
- LuneScript は基本的な文法が Golang に近いため、公式の `go.tmLanguage.json` から改変しつつ作成
    - https://raw.githubusercontent.com/microsoft/vscode/main/extensions/go/syntaxes/go.tmLanguage.json

### Debugging
- プロジェクトを開いている VSCode で `F5` キーを押し、デバッグ用 VSCode を起動する
    - デバッグ用 VSCode には開発中の extension がインストールされた状態になっている
- デバッグ用 VSCode で対象言語のソースコードを開き、シンタックスハイライトを確認する
    - e.g. [sample.lns](./sample.lns)

### Creating snippets
- `package.json`: 以下のようにスニペットを読み込む設定を追加

```json:package.json
{
    // ...
    "contributes": {
        "languages": [
            // ...
        ],
        "grammars": [
            // ...
        ],
        // append the following settings
        "snippets": [
            {
                "language": "lunescript",
                "scopeName":"source.lns",
                "path": "./snippets/lunescript.snippet.json"
            }
        ]
    }
}
```

- `snipets/lunescript.snippet.json`: スニペット設定を記述する
    - 参考: [VSCodeのUser Snippetを活用しよう！](https://qiita.com/282Haniwa/items/82828c6a566e3e7e047d)

### Packaging the extension
- 事前準備:
    - `package.json`: `"publisher": "{your-name}"` を記述する
    - `README.md`: yo で作成したままの場合は適切な内容に書き換える

```bash
# create a package of this extension
$ vsce package

## => vacode-lunescript-0.0.1.vsix

# install extension from vsix
$ code --install-extension vscode-lunescript-0.0.1.vsix
```
