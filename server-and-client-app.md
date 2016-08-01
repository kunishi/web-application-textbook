# サーバサイドとクライアントサイドの連動

### 動機 ###
* HTTPはもともと同期的な通信
	* クライアントからリクエストを出すと、レスポンスが返ってくるまでクライアントの処理は停止する
* Webページは静的
	* ページの内容を変更するには? → 静的なページを2つ用意し、リンクやフォームなどを用いてページを遷移させる必要があった
	* DOM（Document Object Model）をプログラムで操作することで解決（前回の講義で解説済み）
* 例題
	* ページの一部にTwitterのタイムラインを表示する
		* タイムラインは時々刻々更新されていく
		* タイムラインの更新を時々刻々ページに反映させるには?
			* ブラウザがページを表示している裏でプログラムを動かし、タイムラインのデータを取得し、取得したデータでDOMの一部を置き換えれば良い
		* ブラウザ（クライアント）での表示とHTTPによるデータ取得が平行して行われる→ **非同期通信**

### Ajax (Asynchronous JavaScript And XML) ###

* ブラウザにあらかじめ用意されている標準機能だけでWebの非同期通信を実現する手法
	* DOM, JavaScript, XMLHttpRequest(XHR)のみ
	* プラグインなどの追加機能を必要としない
	* ブラウザ上で使われる技術
		* モバイルアプリではプログラミングの制限が少ないので、もっと自由に非同期通信ができる（のでAjaxは使われない）
* XMLHttpRequest
	* ブラウザに組み込まれたプログラミング言語(JavaScriptなど)でHTTP通信を行うためのAPI
	* Microsoftが提案・実装した技術
	* もともとはInternet Explorerでしか使えなかったが、その後ほかのブラウザでも実装が進み、標準で組み込まれるようになった
* 手順
	1. ブラウザ→サーバ：Webページのリクエスト
	2. サーバ→ブラウザ：Webページをレスポンス（Ajaxを利用するJavaScriptが埋め込まれている）
	3. ブラウザ：Webページの解析、表示、埋め込まれたJavaScriptプログラムを実行→XMLHttpRequestオブジェクトが生成
	4. XMLHttpRequestオブジェクト→サーバ：追加のデータ（HTML, XML, JSON, etc.）のリクエスト
	5. サーバ→XMLHttpRequestオブジェクト：追加のデータをレスポンス
	6. XMLHttpRequestオブジェクト：追加のデータを解析、これに基づきDOMを操作→ブラウザのページ表示が更新

### サンプルプログラム ###

実際に使う場合は、Internet Explorer 5・6など、古いブラウザに対応させるためのプログラムを書かなければならないが、ここでは簡単のため省略する。

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

#### Ajaxに存在する危険性 ####

* 通信中のURIが表示されない
* 通信が任意のタイミングで発生する
* 通信の有無を確認しにくい
* ユーザが気がつかない間に情報を盗まれる危険がある

### WebアプリケーションAPI ###

さまざまなサイトが、Webを通して外部からデータを利用できるような機能を提供している。このような機能を、一般にWeb API（Web Application Program Interface）という。Web APIには、内部でAjaxを使用しているものもある。

#### 例: 地理情報の取得 ####

独立行政法人農業・食品産業技術総合研究所（http://www.finds.jp）の提供する簡易逆ジオコーディングサービスの実行例である。

```
http://www.finds.jp/ws/rgeocode.php?
json&lat=34.50165844222924&lon=133.3843445777893
```

このように、緯度・経度をGETパラメータに指定して http://www.finds.jp/ws/rgeocode.php にアクセスすると、以下のように、その位置の都道府県・市町村区名などがXMLやJSONとして返ってくる。このように、ソフトウェアで解析しやすい形で種々のデータを提供するWeb APIが多数存在する。

```
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

地図やSNSなど、複雑な情報を提供するサイトでは、さらにシンプルな形でデータを利用できるようにしている場合がある。例として、Google MapsのAPI（バージョン3）の利用例を示す。

```
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

### マッシュアップ ###

様々なWeb APIを利用すると、種々の情報をあちこちのWebサイトから取得し、これらを組み合わせることで新しいアプリケーションを作ることが容易に可能となる。この手法を**マッシュアップ**（mashup）という。マッシュアップの例としては、先に挙げたGoogle Maps APIの利用例や、TwitterやFacebookのタイムラインを表示するウィジェットを組み合わせてWebページを構成する、といった例が挙げられる。


