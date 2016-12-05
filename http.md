# HTTP(Hypertext Transfer Protocol)

## プロトコルの概要

* HyperText = ハイパーテキスト（ハイパーメディアの前身となった考え方。複数のテキスト文書をリンクで結んだもの。Webが開発された当初は、Webページにはテキストしか使えなかったため、この言葉が使われたものと思われる）
* アプリケーション層のプロトコルの一つ。トランスポート層にはTCPを想定
* ポート番号のデフォルトは80
* 現在のHTTPでは、クライアントからのリクエストに対してサーバがレスポンスを返す
  * クライアントが通信の起点
  * 半二重通信
  * 1回のHTTP通信では1つのURIのデータしか取得することができない
  * 多数のデータを並行して取得するには、TCP接続を複数確立する必要がある
  * 注：次世代HTTPでは、この辺りが変更される可能性が大。詳細は後述。


### クライアント側の処理手順

1. （HTTP通信が始まる前に、TCP接続を確立しておく必要あり）
2. リクエストメッセージの構築
3. リクエストメッセージの送信
4. （サーバからレスポンスが返るまで待機）
5. レスポンスメッセージの受信
6. レスポンスメッセージの解析
7. クライアント側でのデータ処理

### サーバ側の処理手順

1. （HTTP通信が始まる前に、TCP接続を確立しておく必要あり）
2. （クライアントからのリクエストを待機）
3. リクエストメッセージの受信
4. リクエストメッセージの解析
5. 適切なアプリケーションプログラムへ処理を委譲
6. アプリケーショムから結果を取得
7. レスポンスメッセージの構築
8. レスポンスメッセージの送信

### HTTPの歴史

* HTTP/1.0以前…初期のWebでは、メッセージのヘッダがない、HTTPメソッドはGETのみ、など、非常に単純なプロトコルであった。
* HTTP/1.0：1996年に[RFC1945](https://tools.ietf.org/html/rfc1945)として制定。現在のHTTPの基本的な要素が取り込まれている。
* HTTP/1.1：1997年に[RFC2068](https://tools.ietf.org/html/rfc2068)として制定、1999年に[RFC2616](https://tools.ietf.org/html/rfc2616)として改訂。さらに2014年6月に[RFC7230](https://tools.ietf.org/html/rfc7230), [RFC7231](https://tools.ietf.org/html/rfc7231), [RFC7232](https://tools.ietf.org/html/rfc7232), [RFC7233](https://tools.ietf.org/html/rfc7233), [RFC7234](https://tools.ietf.org/html/rfc7234), [RFC7235](https://tools.ietf.org/html/rfc7235)の6分冊として改訂。現在のHTTPはほとんどすべて HTTP/1.1 と考えて良い。本講義の内容はこのバージョンに沿っている。
* HTTP/2：次世代HTTP。2015年5月に[RFC7540](https://tools.ietf.org/html/rfc7540)として国際標準規格となった。通信の高速化を目的として全二重通信が導入されており、その結果、サーバを起点とする通信も可能となっている。

## HTTPの基本思想

先に述べた通り、HTTPはステートレスサーバのアーキテクチャスタイルに従っており、原則としてサーバはクライアントのアプリケーション状態を保存しない。

ユーザ認証など、ステートレスにできない用途もあるが、極力アプリケーション状態の保存は最小に留める、という思想でアプリケーションが設計されることが多い。

論理的には、URIで指定された資源に対して操作(HTTPメソッド)を送り、サーバ側で対応する操作をした結果（成功・失敗、成功の場合は操作結果、失敗の場合はその原因など）をクライアントに返す。

## HTTPメソッド

* URIで指定した資源に対して行いたい処理を指定
* 主なHTTPメソッド
  * GET: 指定したURIの情報を取得する
    * 例：`GET http://blog.example.com/1` …URIで指定された記事の内容を取得する
  * POST: 指定したURIに対する子資源の作成など
    * 例：`POST http://blog.example.com/` … URI `http://blog.example.com/` に新しい投稿という子資源を作成する
  * PUT: 資源の更新
    * 例：`PUT http://blog.example.com/1` … URI `http://blog.example.com/1`  のデータを修正する
  * DELETE: 資源の削除
    * 例：`DELETE http://blog.example.com/1` …URI `http://blog.example.com/1` を削除する
* このように、HTTPメソッドの`GET`, `POST`, `PUT`, `DELETE`は、データに対する代表的な操作であるCRUD(create, read, update, delete)に対応している。
* そのほかのHTTPメソッド
  * `HEAD`, `OPTIONS`, `TRACE`, `CONNECT`, `PATCH`
  * `PATCH`は2010年に発行された[RFC5789](https://tools.ietf.org/html/rfc5789)で新しく追加されたHTTPメソッドである
* HTMLフォームから発行できるHTTPメソッドはGET, POSTのみ
  * これ以外のメソッドを発行する場合は何らかのプログラムを通さなければならない
  * PUT, DELETEをPOSTで代用することもある

## ステータスコード

リクエストメッセージに対応する処理をサーバ上でした結果（成功・失敗、失敗ならその原因など）をクライアントに通知する3桁の数字である。主なものを以下に示す。

* 1xx: 処理中…処理が継続していることを示す。
* クライアントはそのままリクエストを継続するなどの処理を行う
* 2xx: リクエスト成功…リクエストが成功したことを示す
  * `200 OK`: リクエスト成功。GETに対してリクエストが成功した場合は、レスポンスボディにその資源の内容が入る。
* 3xx: リダイレクト。他の資源への転送
  * クライアントはレスポンスヘッダ中のLocationヘッダを参照して、新しい資源に対するリクエストを行う
  * `301 Moved Permanently`: リソースの恒久的な移動
* 4xx: クライアントエラー…原因はクライアント側のリクエストにある
  * `404 Not Found`: リソースの不在
* 5xx: サーバエラー…原因はサーバ側にある
  * `500 Internal Server Error`: サーバ側に何らかの異常が起こっている

## HTTPメッセージ

クライアントからサーバに送られるデータをリクエストメッセージ、サーバからクライアントに送られるデータをレスポンスメッセージという。これらをまとめてHTTPメッセージという。

リクエストメッセージ、レスポンスメッセージともに、以下の2つの部分から構成される。

* ヘッダ: メッセージの付加情報。HTTPメソッドやステータスコードなどもこの部分に描かれる。
* ボディ: メッセージでやりとりされるデータ本体。

ヘッダとボディとの間には必ず1行の空行が置かれる。またボディは省略される（何もない）こともある。

使える文字にも制限がある。ヘッダ内ではASCII文字のみ、ボディ内ではテキストのみが許される。

### リクエストメッセージ

この例は、URI `http://www.example.com:8000/index.html` に `GET`メソッドを送信する、すなわち `http://www.example.com:8000/index.html` の内容を取得するときに送信されるリクエストメッセージである。

この場合、ボディは何もなく、ヘッダの後ろに1行空行があるという形式になる。

```
GET /index.html HTTP/1.1
Host: www.example.com:8000

```

* リクエストライン：メッセージの1行目。HTTPメソッド、リクエストURI、プロトコルバージョン
  * HTTPメソッド：リクエストURIで指定されたリソースに対して行いたい処理。上の例では`GET` （リソースの取得）が指定されている。
  * リクエストURI：上の例のようにパスだけが指定される場合、完全な形のURIが指定される場合、いずれもある。
  * プロトコルバージョン：上の例では `HTTP/1.1` （HTTP バージョン1.1）が指定されている。
  * リクエストヘッダ：リクエストラインに続き、2行目から置かれる。メッセージの付加情報を表す。各行ごと「ヘッダ名: 値」という形式になっている。
  * 上の例では、Hostヘッダに値 `www.example.com:8000` が指定されている。
* リクエストボディ：リクエストメッセージの本体。ない場合もある。リクエストヘッダとリクエストボディは空行1つで区切られる。
  * 上の例にはリクエストボディはない。

### レスポンスメッセージ

上に示したリクエストメッセージに対し、データ（URIで示されたHTMLページ）があった場合、次のようなレスポンスメッセージがサーバからクライアントに送信される。

```
HTTP/1.1 200 OK 
Content-Type: text/html; charset=utf-8

<html><body>I'm WEBrick.</body></html>
```

* ステータスライン：レスポンスメッセージの1行目。プロトコルバージョン、ステータスコード、テキストフレーズ
  * プロトコルバージョン：上の例では`HTTP/1.1`（HTTP バージョン1.1）が指定されている。
  * ステータスコード：リクエストの結果を表す数値コード。上の例では`200`（成功）が指定されている。ステータスコードについては後述。
  * テキストフレーズ：自然言語によるステータスコードの説明。上の例では`OK`が指定されている。
* レスポンスヘッダ：メッセージの付加情報。リクエストヘッダと同様「名前:値」という形をしている。
  * 上の例では Content-Typeヘッダに `text/html; charset=utf-8` という値が指定されている。
* レスポンスボディ：メッセージ本体。ない場合もある。レスポンスヘッダとレスポンスボディは空行1つで区切られる。
  * 上の例ではHTMLデータ「`<html><body>I'm WEBrick.</body></html>`」がレスポンスボディに置かれている。

### HTTPメッセージの代表的なヘッダ

どれも「名前: 値」という形式をしている。このような欄が必要に応じていくつもヘッダ内に置かれる。

* `Date: Tue, 06 Jul 2010 03:21:05 GMT` … メッセージを作成した日時
* `Host: www.example.com` … リクエストを送るサーバ名が `www.example.com` である
* `Content-Type: text/html; charset=utf-8` … メッセージのボディの内容を表す。この例ではHTML形式のテキスト（`text/html`）で、文字エンコーディングがUTF-8であることを表している。
* `Content-Length: 5538` … メッセージボディのバイト数が5538バイトである
* `Authorization: Basic dXNlcjpwYXNzd29yZA==` … 資源にパスワードによる認証が必要な場合、認証に必要なユーザ名とパスワードを送信する。この例では認証方式（Basic認証）とユーザ名、パスワード（`dXNlcjpwYXNzd29yZA==`の部分。ユーザ名とパスワードを[Base64エンコーディング](https://ja.wikipedia.org/wiki/Base64)と呼ばれる方式で符号化している。この例では `user:password` という文字列をエンコードしている）

## 例

### バイナリデータの送信

`http://example.com/photo.jpg` に対して`GET`メソッドを送る、という例を考える。直感的には、画像データ`photo.jpg`を取得するという例である。

ボディにはテキストしか使えないので、画像のようなバイナリデータをそのまま埋め込むことはできない。そこで、元のバイナリデータを[Base64エンコード](https://ja.wikipedia.org/wiki/Base64)によって符号化（encode）したテキストデータをボディに埋め込む。

```
HTTP/1.1 200 OK
Content-Type: image/jpeg

/9j/4AAQSkZJRgABAQAAAQABAAD/4RVORXhpZgAASUkqAAgAAAACADE
BAgAHAAAAJgAAAGmHBAABAAAALgAAAFgAAABQaWNhc2EAAAMAAJAHAA
QAAAAwMjIwAqAEAAEAAAD0AQAAA6AEAAEAAAD0AQAAAAAAAAYAAwEDA
AEAAAAGAAAAGgEFAAEAAACmAAAAGwEFAAEAAACuAAAAKAEDAAEAAAAC
AAAAAQIEAAEAAAC2AAAAAgIEAAEAAACQFAAAAAAAAEgAAAABAAAASAA
AAAEAAAD/2P/gABBKRklGAAEBAAABAAEAAP/bAEMABQMEBAQDBQQEBA
UFBQYHDAgHBwcHDwsLCQwRDxISEQ8RERMWHBcTFBoVEREYIRgaHR0fH
x8TFyIkIh4kHB4fHv/bAEMBBQUFBwYHDggIDh4UERQeHh4eHh4eHh4e
Hh4eHh4eHh4eHh4eHh4eHh4eHh4eHh4eHh4eHh4eHh4eHh4eHh4eHv/
AABEIAKAAoAMBIgACEQEDEQH/xAAcAAAABwEBAAAAAAAAAAAAAAABAg
MEBQYHAAj/xAA7EAABAwMCBAQEBQIGAQUAAAABAgMEAAUREiEGMUFRE
(以下略)
```

これを受け取ったクライアントは、ボディをBase64方式でデコードし、元の画像データを取り出す。

### 200以外のステータスコード

ステータスコードが200以外の場合、ボディには、処理結果の詳細情報が埋め込まれる。何も埋め込まれない、というケースは稀である。

例えば、`http://example.com/index.html` に対して `GET` メソッドを送ったところ、対応するリソースがなかった場合、次のようなレスポンスメッセージが返ってくる。

```
HTTP/1.1 404 Not Found
Content:Type: text/html

<html><body>該当するページが見つかりません</body></html>
```

### GETパラメータの扱い

検索サイトでは、検索キーワードなど種々の検索パラメータをGETパラメータとしてサーバに渡す場合が多い。このGETパラメータの受け渡しの例を示す。

例えば、`http://example.com/?q=foo` というURIに対して`GET`メソッドを送ることを考える。この場合`?`以降の`q=foo`がサーバに渡すパラメータであり、GETパラメータと呼ばれる。このとき、リクエストメッセージは次のようになる。

```
GET /?q=foo HTTP/1.1
Host: example.com

```

ただし、実装上、URIの長さには2,000文字などの上限が存在するので、上限を越えるような長いURIにアクセスする場合は、次に示すPOSTパラメータで代用することがある。

### POSTパラメータの扱い

GETパラメータ以外に、POSTメソッドを使ってサーバにパラメータを受け渡すことがある。ほとんどのHTMLフォームではこの方法が用いられている。

例えば、`http://example.com/login`に次のようなHTMLで表されるログインフォームがあったとしよう。

``` 
<form action="POST" target="/list">
  ユーザID: <input type="text" name="id">
  パスワード: <input type="password" name="pass">
  <input type="submit">
</form>
```

ブラウザで表示させると次のようになるだろう。
![](test.png)

ユーザIDの欄に `foo` を、パスワードの欄に `bar` をそれぞれ入力して「送信」ボタンをクリックしたとする。すると、次のように、URI `http://example.com/list` に`POST`メソッドが発行されることになる。

```
POST /list HTTP/1.1
Host: example.com
Content-Type: application/x-www-form-urlencoded

id=foo&pass=bar
```

このように`POST`メソッドでパラメータをサーバに渡すときは、パラメータがボディに埋め込まれる。このパラメータを**POSTパラメータ**という。

## HTTP/1.0からHTTP/1.1へ、そしてHTTP /2へ

### TCPコネクションの継続（Keep alive）

HTTPはTCP/IP上に実装されるアプリケーション層のプロトコルであるから、HTTPの通信を始める前にTCP/IP通信路を確立しなければならない。すなわち、いわゆるTCP/IPの[3ウェイ・ハンドシェイク](https://ja.wikipedia.org/wiki/3%E3%82%A6%E3%82%A7%E3%82%A4%E3%83%BB%E3%83%8F%E3%83%B3%E3%83%89%E3%82%B7%E3%82%A7%E3%82%A4%E3%82%AF)を行わなければならない。これは比較的時間がかかる処理である。

Webページは、ページの構造を記述するHTMLだけでなく、ページ中で使用されている画像、見た目（スタイル）を定義するCSS（Cascading Stylesheet)、当該Webページ上で動作するプログラムを記述するJavaScriptなど、多数のファイルから構成されており、通常、これらは全て別々のHTTP接続でサーバからクライアントに送られる（[http2 explained](https://www.gitbook.com/book/bagder/http2-explained/details)によれば、2015年現在、1ページを表示するのに平均100個のデータを必要としている）。したがって、これらのファイルを取得するたびにTCP/IPハンドシェイクを行うと、非常に時間がかかってしまうことは想像に難くない。

そこでHTTPには、複数のHTTP接続を1つのTCP/IPセッション上で行う、キープアライブ(Keep alive)という機能が含まれている。HTTP/1.1では、これがデフォルトの挙動になった。クライアント、サーバのいずれかから `Connection: close` をヘッダに入れて送信するまで、同一のTCP/IPセッションを使用する。

```
HTTP/1.1 200 Ok
Content-Type: text/html; charset=utf-8
Connection: close

<html><body>response</body></html>
```

### レイテンシの問題

Webの速度向上に関し、HTTP/1.1でも大きな問題が存在した。レイテンシ（データ転送要求を出してから結果が返って来るまでの遅延時間）である。HTTP/1.1では、1つのリクエストを出すと、そのレスポンスが返ってくるまで次のリクエストが出せない。すなわち、本質的にリクエスト・レスポンスが直列的に実行されることを想定している。従って、1ページを表示するのに必要なデータ数が増えれば増えるほど、ページ表示要求を出してから実際にページが表示されるまでの時間が長くなる。

この問題を解決するために、TCP/IP接続を複数並行して行う、複数の画像やCSS, JavaScriptを事前に1つにまとめてHTTPサーバ上に配備しリクエスト数を減らす（スプライティングなど）、データを複数のHTTPサーバに分散配備する（シャーディング）、などといった解決策が講じられてきた。しかし、問題の根本の原因がHTTPそのものの仕組みにあるため、これらはいずれも小手先の解決策でしかなかった。

### HTTP/2

HTTP/2は、HTTP/1.1との互換性を保ちつつ、上で述べたレイテンシの問題を解決する事を目的として検討されたプロトコルであり、2015年5月に[RFC7540](http://www.rfc-editor.org/rfc/rfc7540.txt)として国際標準化された。Googleが提唱した[SPDY](https://ja.wikipedia.org/wiki/SPDY)というプロトコルがベースになっている。

レイテンシの問題を解決するには、TCP/IP通信路の使用効率を向上させる必要がある。そこで**フレーム**と**ストリーム**という概念が導入された。

* フレーム：従来のHTTPメッセージよりも粒度の細かい、データの送受信単位。RFC7540には10種類のフレームが定義されている。例えば、従来のHTTPメッセージのヘッダは`HEADERS`フレーム、ボディは`DATA`フレームにそれぞれ対応する。なお、HTTP/1.1までと異なり、各フレームはバイナリフォーマットになっている。
* ストリーム：クライアントとサーバとの論理的な双方向データ送受信路。一つのHTTP/2接続中で複数のストリームを同時に設けることができる（多重化）。各ストリームは論理的には独立しており、双方向にフレーム列を送受信する。これにより、複数のTCP/IP通信路を使うことなく、複数のデータを並行して送受信することが可能となる。実装上は、複数のストリームのフレームが送信側でフレーム単位で混合されてから送信され、受信側では再びフレーム単位で分割してストリームごとに元に戻す、という処理が行われる。

これに付随して、次のような機能が用意されている。

* ヘッダー圧縮：HTTPはステートレスな通信であるため、ヘッダが冗長になり、ヘッダのデータ量が大きくなる。そこでヘッダに特化した圧縮方式である`HPACK`が用意されている([RFC7541](http://www.rfc-editor.org/rfc/rfc7541.txt))。
* サーバープッシュ：クライアントからのリクエストなしに、サーバからクライアントにデータを送信する機能。例えば、あるHTMLファイル`index.html`をブラウザで表示させるのに画像`logo.png`が必要だとわかっていた場合、`index.html`へのリクエストがクライアントから送られてきた時点で`logo.png`もプッシュし、クライアントがこれをキャッシュに入れておくと、レイテンシが改善する可能性がある。
* ストリームの優先度：クライアントからサーバに、どのストリームを優先して送信してほしいかを伝えることができる。フレームの一つである`PRIORITY`フレームを用いる。

### HTTP/1.1とHTTP/2との共存

HTTP/1.1がこれだけ普及している現在、短期間にHTTP/2への移行が進むとは到底考えられず、共存する期間が極めて長くなることが予想される。当面、インターネット上で実働するHTTPクライアント・サーバの圧倒的多数はHTTP/2をサポートしておらず、基本的にはHTTP/1.1が使われ、クライアント・サーバ双方がHTTP/2に対応している場合のみHTTP/2が使われる、といった状況になるであろう。

通信相手のクライアント・サーバがHTTP/2に対応しているか、通信が始まるまではわからないから、まずHTTP/1.1で通信を始め、HTTP/2で通信できるならHTTP/2に切り替える（プロトコルアップグレード）、というのが当面妥当な方法になると考えられる。

[RFC7540](https://tools.ietf.org/html/rfc7540)には、プロトコルアップグレードの方法が2つ示されている。HTTPSの場合は、TLSにもともと用意されているプロトコル調整の仕組みであるALPN(Application Layer Protocol Negotiation)を使う。一方HTTPの場合は、もともとHTTP/1.1に用意されていた（がほとんど使われていなかった）`Upgrade`ヘッダを使う。

以下、RFC7540に示されている、`Upgrade`ヘッダによるプロトコルアップグレードの例である。まずクライアントから次のように`Upgrade`ヘッダをつけたリクエストが送信される。ここで`HTTP2-Settings`ヘッダには、HTTP/2のフレームの一種である`SETTINGS`フレームのデータ本体（ペイロード、payload）がBase64エンコーディングした形でつけられる。

```
GET / HTTP/1.1
Host: server.example.com
Connection: Upgrade, HTTP2-Settings
Upgrade: h2c
HTTP2-Settings: <base64url encoding of HTTP/2 SETTINGS payload>

```

サーバがHTTP/2に対応している場合は、サーバからも`Upgrade`ヘッダのついたレスポンスが返り、以降はHTTP/2による通信が行われる。

```
HTTP/1.1 101 Switching Protocols
Connection: Upgrade
Upgrade: h2c

[ HTTP/2 connection ...
```

一方サーバがHTTP/2に対応していない場合は、通常のHTTP/1.1のレスポンスが返ってくる。この場合は以降の通信はHTTP/1.1で行われる。

```
HTTP/1.1 200 OK
Content-Length: 243
Content-Type: text/html

...
```

