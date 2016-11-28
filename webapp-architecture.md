# Webアプリケーションのアーキテクチャ

これまでの内容を基に、Webアプリケーションのアーキテクチャについて述べる。まずWebブラウザで動作するアプリケーションについて述べ、次にスマートフォンなどのアプリの構成についても簡単に述べる。

## Webブラウザで動作するアプリケーション

WebブラウザとWebサーバとで構成されるアプリケーションの場合、ソフトウェアが動作する環境は以下の2つがある。

* Webサーバ。任意のプログラミング言語によるソフトウェアが実行できる。開発効率を重視してRubyやPHP, Perl, Python, JavaScriptといったインタプリタ型言語でソフトウェアを実装することが多い。高性能を要求される場合はJavaやC#といったハイブリット型言語も用いられる。
* Webブラウザ。ブラウザにはJavaScriptインタプリタが内包されており、一定の条件下で、Webサーバから取得されたJavaScriptソフトウェアをブラウザ内で実行することが可能である。

したがってWebアプリケーションのアーキテクチャは、Webブラウザ内でJavaScriptプログラムを用いるかどうかによって大まかに2つに分けられる。概要を以下の図に示す。

<a title="By DanielSHaischt, via Wikimedia Commons [CC BY-SA 3.0 (http://creativecommons.org/licenses/by-sa/3.0)], via Wikimedia Commons" href="https://commons.wikimedia.org/wiki/File%3AAjax-vergleich-en.svg"><img width="512" alt="Ajax-vergleich-en" src="https://upload.wikimedia.org/wikipedia/commons/thumb/0/0b/Ajax-vergleich-en.svg/512px-Ajax-vergleich-en.svg.png"/></a>
(https://commons.wikimedia.org/wiki/File%3AAjax-vergleich-en.svg, By DanielSHaischt, via Wikimedia Commons [<a href="http://creativecommons.org/licenses/by-sa/3.0">CC BY-SA 3.0</a>], <a href="https://commons.wikimedia.org/wiki/File%3AAjax-vergleich-en.svg">via Wikimedia Commons</a>)

### 伝統的なWebアプリケーション

左側が伝統的なWebアプリケーションのアーキテクチャであり、次の流れで処理が行われる。

1. WebブラウザからWebサーバ（URI）に対しHTTPリクエストが送られる
2. HTTPリクエストを受け取ったWebサーバは、URI、HTTPメソッドの種類などを基に適切なソフトウェアを選択し、処理を依頼する（**URIルーティング**）
3. 選択されたソフトウェアはデータベース問合せなどを実行し、レスポンスメッセージを組み立て、Webサーバに、Webブラウザへの送信を依頼
4. WebサーバからWebブラウザにレスポンスが送信される
5. Webブラウザはレスポンスを解析、HTMLなら必要なリソース（画像、CSS、JavaScriptなど）の取得をさらに行い、DOMを構築、レンダリングして表示
6. 場合によっては、Webブラウザ内でJavaScriptプログラムを実行して、ユーザからのアクション（マウスクリックなど）を監視し、アクションが発生するとJavaScript経由でDOMを更新して表示を変更（ダイナミックHTML, DHTML）

これらの一連の処理を繰り返してWebページ間を遷移することで、アプリケーションが動作する。したがって、アプリケーションを設計する場合は、Webページ単位で設計を行い、それらをリンクやフォームでつないでいくという方針をとる。

2から4の処理は、最も古くはCommon Gateway Interface(CGI, [RFC3875](https://tools.ietf.org/html/rfc3875)として国際標準となっている)を利用した極めて素朴な実現方法が主流であったが、Webアプリケーションの複雑化とともに抽象化が進み、現代のWebアプリケーションフレームワークでは多くの部分を自動的にやってくれるようになっている。

6のみ簡単な例を示しておこう。メニュー項目にマウスを当てるとサブメニューが表示、外すとサブメニューが消えるという例である。ベースになるHTMLは次のようになっているとする。

``` html
<ul id=“menu”>
  <li>メニュー1
    <ul>
      <li>サブメニュー1</li>
      <li>サブメニュー2</li>
    </ul>
  </li>
  …
</ul>
```

このHTMLに対して、次のようなJavaScriptプログラムを実行する。これは[jQuery](https://jquery.com/)というJavaScriptライブラリを利用したプログラムであり、簡単にいうと、`id`属性の値が`menu`である要素の下にある`li`要素(`$('#menu li')`）に対し、マウスを当てると子要素の`ul`要素を表示(`show()`)、マウスを外すと子要素の`ul`要素を隠す(`hide()`)、というイベント監視関数を登録する、という処理を行っている。

``` javascript
$(document).ready(function() {
  $(‘#menu li’).hover(
    function() { $(this).children(‘ul’).show(); },
    function() { $(this).children(‘ul’).hide(); }
  );
});
```

### Ajax

1999年にMicrosoft社が公開したWebブラウザであるInternet Explorer 5において、ブラウザ内で実行されるプログラム中でHTTPリクエストを発行しレスポンスを受け取るAPIが初めて搭載された。Internet Explorer 5ではActiveXというMicrosoft独自技術向けのAPIであったが、程なく、他のブラウザでもこの機能をJavaScriptのAPIとして搭載するようになった。このAPIを、Internet Explorer 5での名称をそのまま用いて**XMLHTTPRequest**（**XHR**）と呼ぶ。

XHRを用いると、いったんWebブラウザ上でDOMを構築してページを表示したのち、JavaScriptプログラム中でHTTPリクエストを発行してWebサーバから必要なデータを取得し、これを用いてDOMを更新するというアーキテクチャを考えることができる（本節冒頭の図の右側）。伝統的なWebアプリケーションと比べ、ページ遷移とは非同期的にデータ取得およびページ更新を行うことができる点が大きく異なっている。さらに、このアーキテクチャはDOM, JavaScript, XHRだけで実現でき、特別な拡張機能を必要としないことも利点である。

このアーキテクチャはGoogle社がGoogle Mapで採用したことで広く知られることとなり、2005年に発表された[Ajax: A New Approach to Web Applications](http://adaptivepath.org/ideas/ajax-new-approach-web-applications/)[^1]という記事をきっかけに “Ajax” と呼ばれるようになった。

[^1]: 日本語訳は http://kentarok.org/translations/ajax など。

XHRを用いたJavaScriptプログラム例を以下に示す。実際には、Internet Explorer 5・6など古いブラウザに対応させるための条件も記述しなければならないが、ここでは簡単のため省略する。

``` javascript
// XMLHttpRequestオブジェクトの生成
var xhr = new XMLHttpRequest();

// XMLHttpRequestオブジェクトがデータを受け取ったときの処理
function processXhrChange() {
  if (xhr.readyState == 4) { // 通信終了
    if (xhr.status == 200) { // ステータスコード200
      // ここにDOM操作が入る
    }
  }
}

// XMLHttpRequestオブジェクトに上記の関数を登録
xhr.onreadystatechange = processXhrChange;

// XMLHttpRequestオブジェクトからGETリクエストを出す
xhr.open("GET", "http://example.jp/data.html");
xhr.send();
```