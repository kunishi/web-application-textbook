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

このアーキテクチャはGoogle社がGoogle Mapで採用したことで広く知られることとなり、2005年に発表された[Ajax: A New Approach to Web Applications](http://adaptivepath.org/ideas/ajax-new-approach-web-applications/)[^1]という記事をきっかけに “Ajax” と呼ばれるようになった。なお、もともとAjaxは“Asynchronous JavaScript+XML”の略だったが、現在は特になんの略称でもないという公式見解になっている。

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

#### コールバック関数

Ajaxにおいて重要となるのが**コールバック関数**である。一般的なプログラミング言語の機能であるが、わかりにくい概念なので、簡単に説明しておく。

jQueryによるDHTMLの例の一部を再掲しよう。

``` javascript
  $(‘#menu li’).hover(
    function() { $(this).children(‘ul’).show(); },
    function() { $(this).children(‘ul’).hide(); }
  );
```

`function() {...}`という記述に着目してほしい。これは関数の呼び出しではない。プログラム中で処理可能な値[^1]としての関数、平たく言えば「関数」という種類の値（**第一級関数**、first-class function）である。したがって、`$('#menu li').hover({function() {...}, function() {...});`は、マウスを当てると第1引数の値（＝関数）を実行し、マウスを外すと第2引数の値（＝関数）を実行する、という関数呼び出しになる。このように、呼び出し先の関数の処理の一部となる第一級関数を**コールバック関数**という。

[^1]: プログラミング言語の用語で**第一級オブジェクト**（first-class object）という。

Ajaxでコールバック関数を用いた例を挙げてみよう。これはjQueryライブラリを用いたAjaxの実装であり、`http://example.jp/some.php`に`POST`メソッドを送り、成功（HTTPステータスコード`200`）のレスポンスを受け取った時に、レスポンスボディをブラウザの`alert`ダイアログで表示する、というものである。

``` javascript
jQuery.ajax({
  type: “POST”,
  url: “http://example.jp/some.php”,
  success: function(msg) {
    alert(msg);
  }
});
```

`jQuery.ajax`関数呼び出しの引数（JSON形式の値）に第一級関数 `function(msg) { alert(msg); }`が書かれているのに気づかれただろうか。`jQuery.ajax`内部では、レスポンスボディを引数としてこの関数を呼び出す。

なぜコールバック関数がAjaxで必要なのだろうか。それはブラウザ上ではJavaScriptプログラムが逐次的に実行されるためである[^1]。仮にコールバック関数を使わずにHTTPリクエストを記述するとどうなるだろうか。以下では`XMLHTTPRequest`を直接用いてHTTPリクエスト`GET /bar/foo.txt`を実行しているが、HTTPリクエストを実際に実行しているコード`request.send(null);`はHTTPレスポンスが返ってくるまで終了しない。つまり、HTTPレスポンスが返ってくるまでこのJavaScriptプログラムは待ち状態になり、ユーザには通信が終了するまでブラウザが停止してしまったように見える。

``` javascript
var request = new XMLHttpRequest();
request.open('GET', '/bar/foo.txt', false);  // `false` makes the request synchronous
request.send(null);

if (request.status === 200) {
  console.log(request.responseText);
}
```
(https://developer.mozilla.org/ja/docs/Web/API/XMLHttpRequest/Synchronous_and_Asynchronous_Requests より引用)

[^1]: JavaScriptという言語の実行モデルが、そもそも逐次的（シングルスレッド）である。

Ajaxにおけるコールバック関数は、この問題を解決する。以下の例において `jQuery.ajax`関数はHTTPレスポンスが返ってくるのを待たずに終了し、この部分以下の処理が継続される。そして、HTTPレスポンスが返ってきたタイミングで（非同期的に）コールバック関数が実行されるのである。

``` javascript
jQuery.ajax({
  type: “POST”,
  url: “http://example.jp/some.php”,
  success: function(msg) {
    alert(msg);
  }
});
```

コールバック関数を使ってAjaxを実装すると、一般にコールバック関数が入れ子になり、プログラムが複雑になりやすい。この問題点を改善した新しいAjaxプログラミングスタイルとして、Facebook社が公開した[React](https://facebook.github.io/react/)が非常に注目を集めている。本稿ではこれ以上の説明は割愛するが、今後重要性が増す技法であろうと予想される。

### Web API ###

Ajaxを用いたWebアプリケーションでは、Webサーバから取得するデータはHTML形式である必要はなく、HTMLよりプログラム可読性が高い形式であった方が構文解析も容易で、かつデータ転送効率も良い。

このような理由から、XMLや[JSON](https://tools.ietf.org/html/rfc4627)(JavaScript Object Notation)などの形式でデータをWeb経由で取得できるサイトが増えている。このようなデータ提供機能を、一般に**Web API**（Web Application Program Interface）という。

様々なWeb APIを利用すると、種々の情報をあちこちのWebサイトから取得し、これらを組み合わせることで新しいアプリケーションを作ることが容易に可能となる。この手法を**マッシュアップ**（mashup）という。マッシュアップの例としては、先に挙げたGoogle Maps APIの利用例や、TwitterやFacebookのタイムラインを表示するウィジェットを組み合わせてWebページを構成する、といった例が挙げられる。

#### 例: 地理情報の取得 ####

独立行政法人農業・食品産業技術総合研究所（ http://www.finds.jp ）の提供する簡易逆ジオコーディングサービスの実行例である。

```
http://www.finds.jp/ws/rgeocode.php?
json&lat=34.50165844222924&lon=133.3843445777893
```

このように、緯度・経度をGETパラメータに指定して `http://www.finds.jp/ws/rgeocode.php` にアクセスすると、以下のように、その位置の都道府県・市町村区名などがXMLやJSONとして返ってくる。このように、ソフトウェアで解析しやすい形で種々のデータを提供するWeb APIが多数存在する。

``` json
{
  "status": 200,
  "result": {
    "prefecture": {
      "pcode": 34,
      "pname": "広島県"
      },
(以下略)
```

#### 例: Google Maps API v3 ####

地図やSNSなど、複雑な情報を提供するサイトでは、さらにシンプルな形でデータを利用できるようにしている場合がある。例として、Google MapsのAPI（バージョン3）の利用例を示す。この例では、地図を表示したい場所に要素（`id`属性が`map`となっている）を用意し、Google Maps APIを呼び出すときにこの`id`属性の値を渡している。これにより、Google Maps APIはこの要素の内容に地図を表示する。

``` html
<script type="text/javascript"> 
  google.maps.event.addDomListener(window, 'load', function() { 
    var map = document.getElementById("gmap"); 
    var options = { 
      zoom: 16, 
      center: new google.maps.LatLng(35.686773, 139.68815), 
      mapTypeId: google.maps.MapTypeId.ROADMAP 
    }; 
    new google.maps.Map(map, options); 
  }); 
</script>

<!-- この要素内に地図が表示される -->
<div id="map"></div>
```

### シングル・ページ・アプリケーション（SPA）

- SPA (Single Page Application)
  - ブラウザで最初に読み込んだHTMLページのDOMをJSで変更することだけで動作するアプリケーション
  - サーバからはデータだけをAjaxで取得、画面の組み立てはクライアント側のJSですべて行う
  - JSの実行速度が上がってきたため、クライアントの少ない計算リソースでも実用的に動くようになってきた
  - 利点：ページ遷移が不要、DOMの一部だけを書き換えるため画面遷移が高速、サーバからダウンロードされるデータ量も削減→モバイル環境に向いている
  - 欠点：検索エンジンに収集されにくい、etc.

### モバイル端末のアプリへの応用

- スマートフォンアプリの作り方
  - ネイティブアプリ
    - ソースコードをコンパイルし、スマートフォンのOS上で動作するバイナリとしてアプリを作成
    - 利点：スマートフォンの機能がフルに使える、動作が速い
    - 欠点：iOS/Androidそれぞれについて独立に作らなければならない→クロスプラットフォーム対応が難しい
  - Webアプリ
    - ブラウザ上で動作するアプリ
    - 利点：クロスプラットフォーム対応が比較的簡単
    - 欠点：スマートフォンの機能が十分に使えない（センサ機能など）、動作が遅い、ブラウザ経由でしか動作しない
  - HTML5ハイブリッドアプリ
    - アプリのコアはHTML+CSS+JS（主にSPA）で、それを（HTML+CSS+JSとOSの橋渡しをする）ネイティブアプリでくるむ
    - 利点：クロスプラットフォーム対応が比較的簡単、スマートフォンの機能が（ネイティブアプリ経由で）かなり使える、ブラウザを経由しなくても動く
    - 欠点：ネイティブアプリよりは遅い（が、ネットワークアクセスが少ない分Webアプリより高速）

