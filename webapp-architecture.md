# Webアプリケーションのアーキテクチャ

これまでの内容を基に、Webアプリケーションのアーキテクチャについて述べる。まずWebブラウザで動作するアプリケーションについて述べ、次にスマートフォンなどのアプリの構成についても簡単に述べる。

## Webブラウザで動作するアプリケーション

WebブラウザとWebサーバとで構成されるアプリケーションの場合、ソフトウェアが動作する環境は以下の2つがある。

* Webサーバ。任意のプログラミング言語によるソフトウェアが実行できる。開発効率を重視してRubyやPHP, Perl, Python, JavaScriptといったインタプリタ型言語でソフトウェアを実装することが多い。高性能を要求される場合はJavaやC#といったハイブリット型言語も用いられる。
* Webブラウザ。ブラウザにはJavaScriptインタプリタが内包されており、一定の条件下で、Webサーバから取得されたJavaScriptソフトウェアをブラウザ内で実行することが可能である。

したがってWebアプリケーションのアーキテクチャは、Webブラウザ内でJavaScriptプログラムを用いるかどうかによって大まかに2つに分けられる。概要を以下の図に示す。

<a title="By DanielSHaischt, via Wikimedia Commons [CC BY-SA 3.0 (http://creativecommons.org/licenses/by-sa/3.0)], via Wikimedia Commons" href="https://commons.wikimedia.org/wiki/File%3AAjax-vergleich-en.svg"><img width="512" alt="Ajax-vergleich-en" src="https://upload.wikimedia.org/wikipedia/commons/thumb/0/0b/Ajax-vergleich-en.svg/512px-Ajax-vergleich-en.svg.png"/></a>
(https://commons.wikimedia.org/wiki/File%3AAjax-vergleich-en.svg, By DanielSHaischt, via Wikimedia Commons [<a href="http://creativecommons.org/licenses/by-sa/3.0">CC BY-SA 3.0</a>], <a href="https://commons.wikimedia.org/wiki/File%3AAjax-vergleich-en.svg">via Wikimedia Commons</a>)

左側が古典的なWebアプリケーションのアーキテクチャであり、次の流れで処理が行われる。

1. WebブラウザからWebサーバ（URI）に対しHTTPリクエストが送られる
2. HTTPリクエストを受け取ったWebサーバは、URI、HTTPメソッドの種類などを基に適切なソフトウェアを選択し、処理を依頼する（**URIルーティング**）
3. 選択されたソフトウェアはデータベース問合せなどを実行し、レスポンスメッセージを組み立て、Webサーバに、Webブラウザへの送信を依頼
4. WebサーバからWebブラウザにレスポンスが送信される
5. Webブラウザはレスポンスを解析、HTMLなら必要なリソース（画像、CSS、JavaScriptなど）の取得をさらに行い、DOMを構築、レンダリングして表示
6. 場合によっては、Webブラウザ内でJavaScriptプログラムを実行して、ユーザからのアクション（マウスクリックなど）を監視し、アクションが発生するとJavaScript経由でDOMを更新して表示を変更

2から4の処理は、最も古くはCommon Gateway Interface(CGI, [RFC3875](https://tools.ietf.org/html/rfc3875)として国際標準となっている)を利用した極めて素朴な実現方法が主流であったが、Webアプリケーションの複雑化とともに抽象化が進み、現代のWebアプリケーションフレームワークでは多くの部分を自動的にやってくれるようになっている。

6のみ、簡単な例を示しておこう。メニュー項目にマウスを当てるとサブメニューが表示、外すとサブメニューが消えるという例である。ベースになるHTMLは次のようになっているとする。

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