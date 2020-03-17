## はじめに
久しぶりに競技プログラミング欲が出たのでAtCoderやろーと思ったところ、便利なCLIツールが出ていたのでセットアップをしました。加えて暗黙知なところを少し書き残して置こうと思います。

## 使用ツール
[online-judge-tools](https://github.com/kmyk/online-judge-tools)
[atcoder-cli](https://github.com/Tatamo/atcoder-cli)

## ツールのインストール

[こちらの製作者様ページ](http://tatamo.81.la/blog/2018/12/07/atcoder-cli-installation-guide/)に丁寧に解説されています。

[@Adaachillさんの記事](https://qiita.com/Adaachill/items/3d4ddad56c5c2cc372cd)も非常に参考になります。

## やりたいこと
- デフォルトで指定したコンテストの問題すべてをダウンロードしておきたい（面倒なので）
- コンテスト用ディレクトリを作成したらテンプレートを用いたファイルの生成をしておいて欲しい
- なんらかのショートカットキーで実行ファイルを作って欲しい
- `oj t`でテストして欲しい

以上の欲求を解決していこうと思います。

### デフォルトで指定したコンテストの問題全てをダウンロードしておきたい。

[デフォルト動作の設定](http://tatamo.81.la/blog/2018/12/07/atcoder-cli-tutorial/)を見つつ設定していきます。
atcoder-cliのconfigで設定するだけでOKです。

1. `$ acc config default-task-choice all`
2. 終わり

### コンテスト用のディレクトリを作成したらテンプレートを用いたファイルの生成をしておいて欲しい

[テンプレートの設定](http://tatamo.81.la/blog/2018/12/07/atcoder-cli-tutorial/)を見つつ設定していきます。リンク元と内容一緒です。

1.``$ cd `acc config-dir` ``
2. cppという名前のディレクトリを作成します。
`$ mkdir cpp`
3. 雛形である`main.cpp`と`template.json`ファイルを作ります
例）`template.json`
```json
{
    "task":{
        "program":["main.cpp"],
        "submit":"main.cpp"
    }
}
```
例）`main.cpp`
```cpp
#include<bits/stdc++.h>
using namespace std;
int main() {

}
```

4. `$ acc templates`を使用して正常に読み込まれているか確認する。
5. デフォルトでテンプレートを使用するように変更する
`$ acc config default-template cpp`
6. 終わり

### ショートカットキーで実行ファイルを作って欲しい

`oj t`でテストしてもらう為には実行ファイルを作る必要があります。codeRunnerとか使ってもいいですが、ビルドタスクでショートカットキーを使い、実行ファイルを作っていきます。

[Visual Studio Codeを使ってC++でHello World](http://tatamo.81.la/blog/2018/12/07/atcoder-cli-tutorial/)

1. 上のリンクに書いてある通り手順をこなす。
>- ⌘+shift+Pでコマンドパレットを開く
>- Tasks: Configure Tasksを入力してEnter
>- テンプレートからtask.jsonを生成を選択
>- 最後にOthers を選択

2. 出現した`task.json`ファイルを編集
なお、ここではc++14を使う前提にしています。
設定値の詳細は上記のリンク先で確認してください。
```json
{
    // See https://go.microsoft.com/fwlink/?LinkId=733558
    // for the documentation about the tasks.json format
    "version": "2.0.0",
    "tasks": [
        {
            "label": "build main.cpp",
            "type": "shell",
            "command": "g++",
            "args": [
                "-O2", "-std=c++14", "-o", "${fileDirname}/a.out", "${file}",
            ],
            "group": {
                "kind": "build",
                "isDefault": true,
            }
        }
    ]
}
```
3. ショートカットキーを設定する
VScodeの[Code] -> [基本設定] -> [キーボードショートカット] -> [run build task]で検索 -> お好きなキー割り当てに変更
4. 実行ファイルを作りたいファイルと同じディレクトリでビルドタスクを実行する。
5. 終わり


### `oj t`でテストして欲しい

上の実行ファイルをビルドタスクで作るようにしたのであれば、configをいじるだけで終わりです。
1. ビルドタスクを作る
2. `$ acc config default-test-dirname-format test`をする
ojのテストディレクトリはtestで作成するため、そちらに合わせる。oj側を変えてもOK
3. `oj t`でテストする
4. 終わり

## まとめ

あとは通常の使用法通りに
1. `$ acc new <contest>`
2. `$ cd <contest>`
3. `main.cpp`にコードを書く
4. ビルドタスク実行
5. `oj t`でテスト
6. `acc s`で提出

といった流れで簡単にできます。シェルスクリプトも書く必要が無くなって、かなり便利ですね。
環境がノンストレスになったので精進に励みたいと思います。

## 宣伝

[AtCoderTracker](https://github.com/Javateaboy/tracker_status_atcoder)

AtCoderTrackerというAndroidアプリをFlutterで最近作りました。(審査中)

公開され次第リンクを載せるつもりですが、ぜひライバルのレートや問題の解き具合の進捗を確認したい場合は使ってみてください。