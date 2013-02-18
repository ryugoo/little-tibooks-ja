# 小さな Titanium Mobile の読み物 (Titanium 3.x 対応版)

このサイトは JavaScript を使って iOS や Android などに対応したネイティブアプリケーションを作ることができる、  
Appcelerator Titanium について紹介する非公式サイトです。

## Appcelerator Titanium

Appcelerator Titanium (タイタニウム) は JavaScript を使って iOS や Android に対応したネイティブアプリケーション、複数のプラットフォームに対応するモバイル Web アプリケーションやハイブリッドアプリケーションを構築することができる開発プラットホームです。シリコンバレーに拠点を置く [Appcelerator Inc](http://www.appcelerator.com/) が [GitHub 上でオープンソースソフトウェアとして開発](https://github.com/appcelerator/titanium_mobile)しています。

### 特徴と比較

Titanium の特徴は JavaScript による1つのコードで**ネイティブな**アプリケーションを構築できる点にあります。これは HTML や CSS を組み合わせて UI を作るタイプのモバイルアプリケーション開発プラットフォームとは異なります。 Titanium では JavaScript を使って全ての UI やロジックを書いていきます。そして UI は、アプリケーションが動作する OS が提供する標準的なものが提供されます。

> ##### UI
> ```
> // 背景色が白いウィンドウを作る
> var win = Ti.UI.createWindow({
>     backgroundColor: "#FFFFFF"
> });
> ```
> ##### ロジック
> ```
> // ウィンドウをクリックしたら Hello, world. と書かれたアラートを表示する
> win.addEventListener("click", function (e) {
>     alert("Hello, world.");
> });
> ```

### Write once, adapt anywhere

1つのコードで複数のプラットフォームに対応させるアプローチは Java で有名な「Write once, run anywhere」があります。 Titanium では同じようなアプローチを取っていますが「Write once, adapt anywhere」という、もう少し緩いものです。完全に1つのコードでマルチプラットフォームに対応させるのではなく、対応させたいプラットフォームごとにロジックを書き分けるアプローチです。

> ##### ロジックの書き分け
> ```
> var osname = Ti.Platform.osname;
> if (osname === "iphone") {
>     // iPhone または iPod touch
> } else if (osname === "android") {
>     // Android
> }
> ```

### 得意と苦手

ネイティブな UI を持つアプリケーションを構築できる Titanium は、 HTML や CSS で UI を作るタイプのアプリケーションよりも高いパフォーマンスを発揮します。使い勝手も一般的なアプリケーションと同じものを提供できますから、アプリケーションのユーザに新しく使い方を覚えてもらう負担が減るでしょう。

Titanium はウェブアプリケーションのフロントエンド構築に威力を発揮します。一般的な JavaScript のイベント駆動型のプログラミングをベースにしていますから、普段ウェブブラウザのための JavaScript を書いているような方でしたら容易に習得することができるでしょう。 XMLHttpRequest によく似た HTTP 通信の仕組みを使ったり、 Socket を利用することもできます。

> ##### HTTP 通信の例
> ```
> var http = Ti.Network.createHTTPClient();
> http.open("GET", "http://example.com/foobar.json");
> http.onload = function () {
>     var json = JSON.parse(http.responseText);
>     // 取得した JSON で行いたい処理
> };
> http.send(null);
> ```

一方で Titanium は非常に高いパフォーマンスを要求するアプリケーションの構築を苦手とします。 Titanium は JavaScript で書かれたコードを Objective-C や Java に変換しているわけではなく、アプリケーションに内蔵された JavaScript エンジンが JavaScript を処理しながら、アプリケーションを実行しています。 JavaScript の処理分、純粋なネイティブアプリケーションよりも性能は落ちます。

例えば、毎秒 30 フレームを安定して出したいゲームの構築や、非常に複雑な UI を持ったアプリケーション、 Instagram のように画像にエフェクトをかけるようなアプリケーションの構築には向いていません。このような問題に対処するためには、Titanium を使わず、純粋なネイティブアプリケーションとして開発を行うか、後述するモジュールを使う方法があります。

## Appcelerator Alloy

Appcelerator Alloy (アロイ) は Titanium 3.0 以降に提供されている **Titanium 用の公式 MVC フレームワーク**です。先述の通り、 Titanium は UI やロジックを全て JavaScript で書きます。初めのうちはコードの見通しも良く、コードの管理は容易ですが、 UI が複雑になってきたり、多くのロジックを組み込んでいるうちにコードの管理は難しくなってきます。

また、ユーザによって様々なベストプラクティスが持ち込まれた結果、ベストプラクティスが一貫しないという問題も抱えていました。そこで Appcelerator が出した解答が「Alloy」です。 Alloy ではユーザインターフェースを持つアプリケーションがよく使う MVC アーキテクチャに則ったアプリケーション開発を可能にします。 Alloy の規約に従った開発を行うことで、開発の管理を容易にします。

Alloy は Titanium のモジュールとして動作します。 Titanium 開発のための IDE である Titanium Studio をご利用の方であれば、 Titanium Studio 3 以降では新しいプロジェクトを作るときに Alloy を使ったプロジェクトを作成できます。また、 CLI を使っている方は Node.js のモジュールとして提供されていて、 npm を経由して Alloy をインストールすることが可能です。

```
$ sudo npm -g install alloy
```

### Alloy MVC

Alloy は MVC アーキテクチャに則ってアプリケーションを構築していくことができます。 Alloy を使うことで UI を XML + TSS (Titanium Style Sheet) で、ロジックとデータモデルを JavaScript で記述することができ、コードを役割に応じて簡単に分割できます。

* M (Model) : コントローラから受け取った情報の正しさを確かめたり、加工をしたり、保存したりする。
* V (View) : XML と TSS で構築された UI 。ユーザは UI を経由して Controller をに情報を渡す。
* C (Controller) : View を監視して入力された情報や操作を元に、適切な処理を走らせ、 View に結果を反映させる。

単にコードを分割するだけでなく、様々なアプリケーションでよく使う機能を「ウィジェット」という仕組みで分割して使い回したり、「テーマ」という仕組みで UI の装飾を簡単に切り替えたり、ビルトインのアニメーション機能を使うことができたりするなど、素直に Titanium を使ってアプリケーションを構築するよりも便利な仕組みが多く提供されています。

### Alloy の使用例