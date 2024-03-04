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

プログラムを書くエディタとして、今回は Visual Studio Code (以下、VSCodeと表記) を使用します。  
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

## 4. HTTP サーバーで Hello, World!

しりとりアプリの前に、Hello, World! と表示させるだけのシンプルなHTTPサーバーを立ち上げます。

### ■ HTTPサーバーについて

HTTPサーバーとは、HTTP ( Hypertext Transfer Protocol ) というプロトコルでブラウザと通信を行うサーバーです。  
現在はこのHTTP通信を暗号化した HTTPS ( Hypertext Transfer Protocol Secure ) 呼ばれるプロトコルが使われることが多いです。

### ■ Hello, World! までの手順

[公式ドキュメント](https://docs.deno.com/runtime/tutorials/http_server)のプログラムを参考に作っていきます。  
はじめにVSCode上で、`server.js` というファイルを作成し下記のコードを書いて保存します。  
(保存は Ctrl(Cmd) + S)

![create-serverjs](assets/05_create-serverjs.png)

**server.js**

```js
const port = 8000;

const handler = () => {
  const body = 'Hello, World!';
  return new Response(body, { status: 200 });
};

console.log(`HTTP server running. Access it at: http://localhost:8000/`);
Deno.serve({ port }, handler);
```

続いて Ctrl(Cmd) + J でターミナルを開き、以下のコマンドからプログラムを実行します。

```bash
deno run --allow-net server.js
```

ターミナルに `Listening on http://localhost:8000/` と表示されたら、ブラウザで http://localhost:8000 にアクセスし、Hello, World! と表示されることを確認してください。

このプログラムは自動で終了しないので、ターミナルで Ctrl + C を押して終了させておきましょう。

### ★ Deno のパーミッションについて

Deno のプログラムではセキュリティの観点から、ファイル、ネットワーク、環境へのアクセスがデフォルトでは許可されません。  
公式ドキュメントでは「Deno is secure by default.」と書かれています。

これらを使用する場合は権限を許可する必要があり、上記の `--allow-net` はネットワークアクセスを許可します。  
その他にも `--allow-read` (ファイル読み取り)、`--allow-write` (ファイル書き込み)などがあります。

ちなみに `-A` と書くとすべての権限が許可されます。開発時はこちらを使用するのが便利です。

気になる方は公式ドキュメントの Permissions ページを読んでみてください。  
https://docs.deno.com/runtime/manual/basics/permissions

## 5. ファイルサーバー

ここからは、しりとりアプリを実装に入ります。  
ここでは、HTML や CSS ファイルを読み込んで表示できるようにします。

まず、`public` フォルダを作成し、その中に `index.html` ファイルを作成します。  
`index.html` には、以下のプログラムを書き込み保存してください。

**index.html**

```html
<!DOCTYPE html>
<html>

<head>
  <meta charset="utf-8">
</head>

<body>
  <h1>見出しです</h1>
</body>

</html>
```

このHTMLファイルを表示させるためには、`server.js` で下記のように変える必要があります。  
しかし、このように書く場合はCSSやJSファイル、別ページのHTMLファイルなど、ファイルを追加するたびにサーバーのコードを書き換えなければなりません。  

**server.js**

```js
const port = 8000;

const handler = async (_) => {
    return new Response(await Deno.readTextFile('./public/index.html'), {
        headers: { 'Content-Type': 'text/html; charset=utf-8' }
    })
};

console.log(`HTTP server running. Access it at: http://localhost:8000/`);
Deno.serve({ port }, handler);
```

これでは非効率的でめんどくさいので、 `public` フォルダの中のファイルを取得して返す、ファイルサーバーとして動作するようにします。  
`server.js` を下記のコードに置き換えてください。

**server.js**

```js
import { serveDir } from "https://deno.land/std@0.217.0/http/file_server.ts"

const port = 8000;

const handler = async (req) => {
    return serveDir(req, {
        fsRoot: 'public',
        urlRoot: '',
        showDirListing: true,
        enableCors: true,
    });
};

console.log(`HTTP server running. Access it at: http://localhost:8000/`);
Deno.serve({ port }, handler);
```

ここまで出来たら今回もターミナルからプログラムを実行します。    
先ほどとは少し違いますが、すべての権限を許可する `-A` と、プログラムに変更があった際に自動で更新してくれる `--watch` を指定しています。

```bash
deno run -A --watch server.js
```

これで `public` フォルダの中身が帰ってくるファイルサーバーを構築することができました。  
試しにCSSを追加してみましよう。`public` フォルダ内に `styles.css` を追加します。

**styles.css**

```css
body {
    background: skyblue;
}
```

`index.html` で `styles.css` を参照するようにします。

**index.html**

```html
<!DOCTYPE html>
<html>

<head>
  <meta charset="utf-8">
  <!-- 以下の link タグを追加してください -->
  <link rel="stylesheet" href="styles.css"> 
</head>

<body>
  <h1>見出しです</h1>
</body>

</html>

```

青い背景のページが表示されることを確認できればファイルサーバーの完成です。

![file-server-html](./assets/06_file-server.png)

## 6. HTTPメソッドについて

このあとはしりとりの最低限の機能として、サーバーでしりとりの前の単語を保持するようにして、ブラウザ(フロントエンド)からそれに繋がる次の単語を送って更新できるようにします。

実装の前に、HTTP通信(サーバーとブラウザ間の通信)に必要なHTTPメソッドについて説明します。  
HTTPで通信を行うときには、メソッドと呼ばれるアクションを指定する必要があります。  
メソッドについて、このあと使用する `GET` と `POST` について解説します。

### ■ GET

サーバからデータを取得する際に使用します。  
URLにクエリパラメータを含めることができます。  
**クエリパラメータは人の目に触れやすい場所に付与されるため、他人に見られて困る内容(個人情報等)はGETで送信してはいけない。**  

### ■ POST

サーバのデータを更新する時に使用します。  
データ自体はbodyに含めて通信します。  
また、その情報のことをペイロードといいます。

### ■ どちらを使うといいのか

以下のように覚えておいてください。

- 他人に見られると困る情報を扱う場合は `POST`
- それ以外、データの取得など何度呼び出されても同じ結果を返すものは `GET`
- ブラウザからデータを送信する場合は `POST`
- サーバーからデータを取得する場合は `GET`

---

例として、以下のような場合にはどちらのメソッドを使用するか考えてみましょう。

**Webショッピングサイトにおける商品検索**

ユーザーが検索バーに「青いシャツ」と入力し検索ボタンをクリックすると、検索内容をサーバーに送信し、サーバーから検索結果を取得します。

**オンラインフォームの回答データ送信**

ユーザーがオンラインでアンケートに回答すると、ユーザーがフォームに入力したデータは、安全にサーバーに送信される必要があります。

## 7. しりとりサーバー

それでは、しりとりの最低限の機能として、サーバーでしりとりの前の単語を保持するようにして、ブラウザからそれに繋がる次の単語を送って更新できるようにします。

### ■ 前の単語を保持する

まずは `server.js` で前の単語を記録する変数を用意して、それを取得するAPIを作成します。2箇所の追加ポイントを書き込んで保存しましょう。

**server.js**

```js
import { serveDir } from "https://deno.land/std@0.217.0/http/file_server.ts"

// 追加1: しりとりの前の単語を記録する変数、初期値は「しりとり」
let previousWord = 'しりとり';

const port = 8000;

const handler = async (req) => {
    const pathname = new URL(req.url).pathname;
    
    // 追加2: /shiritori にアクセスされたら前の単語を返す
    if (pathname === '/shiritori') {
        return new Response(previousWord);
    }

    return serveDir(req, {
        fsRoot: 'public',
        urlRoot: '',
        showDirListing: true,
        enableCors: true,
    });
};

console.log(`HTTP server running. Access it at: http://localhost:8000/`);
Deno.serve({ port }, handler);
```

サーバーを起動し、http://localhost:8000/shiritori にアクセスします。  
「しりとり」と返ってくることを確認しましょう。

### ■ ブラウザから送られてきた単語を記録する

続いて、ブラウザから送られてきた次の単語を記録できるようにします。  
ブラウザからは下記のようなJSON形式で送られてくることとしましょう。

```json
{
    "nextWord": "次の単語"
}
```

/shiritori にPOSTで送られてきたら、記録するように `server.js` を書き換えます。

**server.js**

```js
import { serveDir } from "https://deno.land/std@0.217.0/http/file_server.ts"

let previousWord = 'しりとり';

const port = 8000;

const handler = async (req) => {
    const pathname = new URL(req.url).pathname;
    
    // 追加1: 前の単語を記録するのはメソッドがGETのときなので条件を追加
    if (pathname === '/shiritori' && req.method === 'GET') {
        return new Response(previousWord);
    }

    // 追加2: POSTの場合は、次の単語を受け取って記録する
    if (pathname === '/shiritori' && req.method === 'POST') {
        // 送られてきた nextWord を取得する
        const requestJson = await req.json();
        const nextWord = requestJson.nextWord;

        previousWord = nextWord;
        return new Response(nextWord);
    }

    return serveDir(req, {
        fsRoot: 'public',
        urlRoot: '',
        showDirListing: true,
        enableCors: true,
    });
};

console.log(`HTTP server running. Access it at: http://localhost:8000/`);
Deno.serve({ port }, handler);
```

このように `req.method` を使うことで、同じ `/shiritori` というパスに対してHTTPメソッドを判別して処理を分けることができます。

このままだとブラウザ側(フロントエンド)に入力フォームがなく動作確認ができません。  
そこで、今度はフロントエンド側を作成していきます。

## 8. フロントエンドの調整

上記で作成した、しりとりサーバーのAPIを使えるようにしていきます。  
ブラウザ側では、HTTP通信をするのに [Fetch API](https://deno.land/api@v1.41.0?s=fetch) を使用します。

■ 前の単語を取得する

しりとりの前の単語を取得して表示できるように `index.html` を書き換えていきます。

Fetch API の使い方は簡単で、`fetch("{パス}")` と書くとAPIを呼び出すことができます。  
このとき、メソッドを指定しない場合は、GETになります。  
(POSTの呼び出し方はこのあと出てくるので割愛)

**index.html**

```html
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8">
    <link rel="stylesheet" href="styles.css">
</head>

<body>
    <!-- 変更1: 「見出しです」を「しりとり」に変更 -->
    <h1>しりとり</h1>

    <!-- 追加1: 前の単語を表示するためのpタグを追加 -->
    <p id="previousWord"></p>

    <!-- 追加2:  ページが読み込まれたら前の単語を取得する -->
    <script type="module">
        window.onload = async (event) => {
            const response = await fetch("/shiritori");
            const previousWord = await response.text();
            
            const para = document.querySelector("#previousWord");
            para.innerText = `前の単語：${previousWord}`;
        }
    </script>
</body>

</html>
```

画像のように「しりとり」と「前の単語：しりとり」と出てくることを確認しましょう。

![shiritori-1](./assets/07_shiritori-1.png)

### ■ 次の単語をサーバーへ送れるようにする

現状では次の単語を入力できないので、テキストフィールドと送信ボタンを追加します。  
また、送信ボタンを押したらテキストフィールドの入力内容が送信されるようにします。

以下のプログラムに書いてありますが、Fetch API を使って POST を使用する場合は、methodパラメータにPOSTと指定します。

**index.html**

```html
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8">
    <link rel="stylesheet" href="styles.css">
</head>

<body>
    <h1>しりとり</h1>

    <p id="previousWord"></p>

    <!-- 追加1: テキストフィールドと送信ボタンを追加 -->
    <input id="nextWordInput" type="text" />
    <button id="nextWordSendButton">送信</button>

    <script type="module">
        window.onload = async (event) => {
            const response = await fetch("/shiritori");
            const previousWord = await response.text();

            const para = document.querySelector("#previousWord");
            para.innerText = `前の単語：${previousWord}`;
        }

        // 追加2: 送信ボタンを押したら入力内容が送信されるようにする
        document.querySelector("#nextWordSendButton").onclick = async (event) => {
            const nextWord = document.querySelector("#nextWordInput").value;
            const response = await fetch("/shiritori", {
                method: "POST",
                headers: { "Content-Type": "application/json" },
                body: JSON.stringify({ nextWord })
            });
            const previousWord = await response.text();

            const para = document.querySelector("#previousWord");
            para.innerText = `前の単語：${previousWord}`;
        };

    </script>
</body>

</html>
```

## 9. 入力チェック

しりとりの最低限のルールとして、送られてきた単語が前の単語の最後の文字から始まっているかどうかと、「ん」で終わってないかを確認してエラーを返すようにします。  
index.html では、エラーが返されたらダイアログを表示するようにしましょう。

**server.js**

```js
import { serveDir } from "https://deno.land/std@0.217.0/http/file_server.ts"

let previousWord = 'しりとり';

const port = 8000;

const handler = async (req) => {
    const pathname = new URL(req.url).pathname;

    if (pathname === '/shiritori' && req.method === 'GET') {
        return new Response(previousWord);
    }

    if (pathname === '/shiritori' && req.method === 'POST') {
        const requestJson = await req.json();
        const nextWord = requestJson.nextWord;

        // 追加1: 前の単語の最後の文字から始まっているか
        if (
            nextWord.length > 0 &&
            previousWord.charAt(previousWord.length - 1) !== nextWord.charAt(0)
        ) {
            return new Response("前の単語に続いていません。", { status: 400 });
        }

        // 追加2: 「ん」で終わってないか
        if (nextWord.charAt(nextWord.length - 1) === "ん") {
            return new Response("「ん」で終わっています。", { status: 400 })
        }
        // 追加ここまで

        previousWord = nextWord;
        return new Response(nextWord);
    }

    return serveDir(req, {
        fsRoot: 'public',
        urlRoot: '',
        showDirListing: true,
        enableCors: true,
    });
};

console.log(`HTTP server running. Access it at: http://localhost:8000/`);
Deno.serve({ port }, handler);
```

**index.html**

```html
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8">
    <link rel="stylesheet" href="styles.css">
</head>

<body>
    <h1>しりとり</h1>

    <p id="previousWord"></p>

    <input id="nextWordInput" type="text" />
    <button id="nextWordSendButton">送信</button>

    <script type="module">
        window.onload = async (event) => {
            const response = await fetch("/shiritori");
            const previousWord = await response.text();

            const para = document.querySelector("#previousWord");
            para.innerText = `前の単語：${previousWord}`;
        }

        document.querySelector("#nextWordSendButton").onclick = async (event) => {
            const nextWord = document.querySelector("#nextWordInput").value;
            const response = await fetch("/shiritori", {
                method: "POST",
                headers: { "Content-Type": "application/json" },
                body: JSON.stringify({ nextWord })
            });

            // 追加1: エラーが返ってきたら、ダイアログを表示して後続の処理を中止
            if (response.status / 100 !== 2) {
                alert(await response.text());
                return;
            }

            const previousWord = await response.text();

            const para = document.querySelector("#previousWord");
            para.innerText = `前の単語：${previousWord}`;
        };

    </script>
</body>

</html>
```

## 10. さいごに

ここまでで最低限のしりとりが完成しました。  
このしりとりはこのあとにカスタマイズが待っています。

- 同じ単語を入力できないようにする
- 最初の単語がランダムに決まるようにする
- ひらがな以外を入力できないようにする
- しりとりの単語の履歴を表示する
- 最初からやり直せるようにリセット機能をつける
- 他のユーザーが単語を更新したら自動で自分のページの単語が切り替わるようにする
- 複数のユーザーで対戦できるようにする
- サーバーを JavaScript ではなく TypeScript で作成する
- CSS でオシャレな見た目にする

追加で機能を付け足して、Deno Deploy などを使用してオリジナルのしりとりを公開してみてください。