## 1. はじめに

今回はブラウザ上で動作するしりとりアプリを作成します。  
フロントエンドは HTML / CSS / JavaScript、バックエンドは JavaScript を使って作成します。

## 2. Deno について

Deno は JavaScript、TypeScriptの実行環境です。  
今回は、Deno を使用してサーバーを立てます。

フロントエンドで使用されることが多い JavaScript を使用してサーバーの実装もできるのが便利です。  
JavaScript を使用するサーバーというと Node.js が有名ですが、この Node.js 開発者が反省点をふまえて開発したのが Deno です。

## 3. 開発準備

しりとりアプリを作っていくにあたっての下準備を行います。

### ■ Deno のインストール

公式サイトを参考に Deno をインストールします。  
Install Deno のセクションに書かれている通り、コマンドを実行するだけでOKです。  
https://docs.deno.com/runtime/manual

インストールができたら以下のコマンドを実行してみましょう。  
Welcome to Deno! と表示されるのを確認してください。  

```bash
deno run https://deno.land/std/examples/welcome.ts
```

### ■ Visual Studio Code のインストール

プログラムを書くエディタとして、今回は Visual Studio Code (VSCode) を使用します。  
公式サイトから該当するOSのインストーラをダウンロードしてインストールしましょう。  
https://code.visualstudio.com/download

### ■ ベースフォルダの作成

今回のプログラムを保存するフォルダを作成します。  
`deno-shiritori` という名前のフォルダを作成してください。

### ■ VSCode で開く

VSCode を起動して、Ctrl + O (Mac の場合は Cmd + O) を押し、先ほど作成した `deno-shiritori` フォルダを開きましょう。  
左側のエクスプローラーで「DENO-SHIRITORI (deno-shiritori) 」と表示されることを確認しましょう。

![open-deno-shiritori](./assets/02_open-deno-shiritori.png)

### ■ 拡張機能のインストール

続いて、Deno 関連のコードの補完のために拡張機能を入れていきます。
画面左上側のボタン群の1番下のボタンを押して「deno」と検索し、インストールしましょう。

![deno-extension](assets/03_deno-extension.png)

インストールが終わったらコマンドパレット( Ctrl(Cmd) + Shift + P )を開き、「Deno: Enable」を実行します。  
左側のエクスプローラーで、`.vscode/settings.json` が生成されればOKです。

![deno-enable](assets/04_deno-enable.png)