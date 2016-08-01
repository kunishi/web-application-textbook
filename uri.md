# URI {#uri}

## 目的
Web上に置かれた情報に、プログラムから簡単にアクセスできるようにするには?
→Web上に置かれた情報各々に、プログラムで処理しやすい「名前」をつけよう

## リソース (Resource)
インターネット関連の国際標準規格を定める組織であるIETF（http://www.ietf.org/）の文書RFC3986には、リソース(資源)について次のような説明がある。

> This specification does not limit the scope of what might be a resource; rather, the term "resource" is used in a general sense for whatever might be identified by a URI.  Familiar examples include an electronic document, an image, a source of information with a consistent purpose (e.g., "today's weather report for Los Angeles"), a service (e.g., an HTTP-to-SMS gateway), and a collection of other resources.  A resource is not necessarily accessible via the Internet; e.g., human beings, corporations, and bound books in a library can also be resources.  Likewise, abstract concepts can be resources, such as the operators and operands of a mathematical equation, the types of a relationship (e.g., "parent" or "employee"), or numeric values (e.g., zero, one, and infinity).
> [この仕様はリソースであるかもしれないことに関する範囲を制限しません。 むしろ、「リソース」という用語は一般的な意味ではURIによって特定されることなら何でもに使用されます。 身近な例は一貫した目的(例えば、「ロサンゼルスのための今日の気象レポート」)、サービス(例えば、HTTPからSMSへのゲートウェイ)、および他のリソースの収集で電子化文書、イメージ、情報源を含んでいます。 リソースは必ずインターネットを通してアクセス可能であるというわけではありません。 また、例えば、図書館の人間、会社、および製本簿はリソースであるかもしれません。 同様に、抽象概念はリソースであるかもしれません、数学の方程式、関係(例えば、「親」か「従業員」)、または数値(例えば、ゼロ、1、および無限)のタイプのオペレータやオペランドなどのように。]

非常に抽象的な説明だが、「他と区別可能な、ひとまとまりの情報」と考えてよいだろう。この講義で扱うのは、Web上（あるいはインターネット上）に置かれたリソースである。

### リソースに関して留意すべき点
* 1つのリソースが複数の表現を持つことがある
  * 例：リソース「ロサンジェルスの今日の天気情報」は、HTMLページ、PDF、画像など、いろいろな表現で提供される可能性がある
* リソースは時間の経過に従って状態が変化する
  * 例：リソース「ロサンジェルスの今日の天気情報」は、朝には晴れだったが、昼には曇りになっているかもしれない

## URI (Uniform Resource Identifier)
* インターネット上のリソースの「名前」（識別子, identifier）
  * インターネット上のリソースのURIは、すべてインターネット上で一意に定まる。
  * 現在のインターネットでは、URL（Uniform Resource Locator）とほぼ同義であると考えて良い。
  * URIはWebで主に使われているが、Webに限った考え方ではない。Web以外の通信でも使うことができるし、パソコンやスマートフォンでアプリケーション内部から別のアプリケーションを起動する際にも用いられることがある。
* 例
  * http://www.ietf.org/rfc/rfc2396.txt
  * http://pubinfo.oka-pu.ac.jp/searchApp/viewSyllabus.php?id=1642
  * ftp://ftp.is.co.za/rfc/rfc1808.txt
  * mailto:John.Doe@example.com
  * tel:+1-816-555-1212

## URIの利用例
* ブラウザで表示するページの指定
* HTML中のリンクの指定（例: <a href=”http://www.example.com/”>example.com</a>）
* スマートフォンのアプリ内でURI “tel:+1-816-555-1212” にアクセスすると、電話アプリで +1-816-555-1212 に電話をかける。

## URIの構文
URIはいくつかの構成要素からなっている。詳細な構文はRFC3986に厳密に定められている。

* URIスキーム：URIが利用するプロトコルなど。
* ホスト名：その資源を提供するコンピュータ名。FQDNまたはIPアドレスである場合が多い。
* ユーザ情報：URIの指し示すリソースにアクセスするのにユーザ認証が必要となる場合、正当なユーザ名とパスワードをURIに含めることができる。
* ポート番号：URIにアクセスするためのTCPポート番号。
  * URIスキームで指定されたプロトコルのデフォルトポート番号の場合は省略可（例: HTTPなら80番の場合は省略可）
* パス：ホスト内での資源の識別子。最も簡単な場合、資源（ファイル）への絶対パスになる。
* クエリパラメータ：検索サービスに対する検索キーワードの指定など、クライアントからサーバに何らかのパラメータを渡したい場合に用いられる。
* URIフラグメント：リソース内部の、さらに細かい部分の指定に用いられる。

### 例
http://www.ietf.org/rfc/rfc2396.txt

* URIスキーム：http
* ホスト名：www.ietf.org
* パス：/rfc/rfc2396.txt

foo://bar:baz@example.com:8042/over/there?name=ferret&encoding=utf8#nose

* URIスキーム：foo
* ユーザ情報：bar:baz （ユーザID: bar, パスワード: baz）
* ホスト名： example.com
* ポート番号： 8042
* パス： /over/there
* クエリパラメータ： name=ferret&encoding=utf8
* URIフラグメント： nose

### URI構文に関する補足
* URIスキームとそれ以下の部分は “://” で区切られる。
* ユーザ情報中のユーザIDとパスワードは “:” で区切られる。
* ユーザ情報とホスト名は “@” で区切られる。
* ホスト名とポート番号は “:” で区切られる。
* ユーザ情報、ポート番号は省略可。
* パスとクエリパラメータは “?” で区切られる。
* クエリパラメータについて
  * 一つのパラメータは「パラメータ名=値」という形式
  * 複数のパラメータを並べる場合は “&” で区切る。
* URIフラグメントとその前の部分は “#” で区切られる。

## URIと文字
URIには、ASCII文字集合の一部の文字（アルファベット、数字、一部の記号）しか使えない。空白記号や日本語の文字などをURIに含めたい場合は、%エンコーディングという方式を用いて、URIに適した文字列に変換しなければならない。%エンコーディングの詳細はRFC3986に書かれているが、簡単に言うと「バイトごとに 0x○○ → %○○ と変換」という処理を行う。例を以下に示す。

* 空白記号（0x40）→ %40
* 「あ」という文字（UTF-8 では 0xE3 0x81 0x82 の3バイトで表現される）→ %E3%81%82

%エンコーディングを使わずに、直接URIの中でUnicode文字集合の文字を使えるように拡張した規格（Internationalized Resource Identifier, IRI）もあるが、まだ普及には至っていない。

## よりよいURI
Webでは、リソースを一意に指し示す識別子としてURIが使われ、外部からのリンクを含め、すべてのリソースへのアクセスはURIを使って行われる。ということは、ある情報をWebでリソースとして公開した後、そのリソースのURIを変更してしまうと、そのリソースはWeb上では「行方不明」になってしまうことになる。
つまり、究極的には、リソースに対するURIは未来永劫にわたって不変であることが望ましい。Web開発者は、リソースを公開する前に、そのURIの寿命が可能な限り長くなるようにURIを慎重に設計し、またURIの維持管理をすべきなのである。

参考ページ: [Cool URIs don’t change](http://www.w3.org/Provider/Style/URI.html.en) (日本語訳: [クールなURIは変わらない](http://www.kanzaki.com/docs/Style/URI))

### どういうときにURIが変わってしまうのか
ありがちな例をいくつか挙げてみよう。

* サーバ機のリプレースでWebサーバのホスト名が変わってしまった
  * http://server1.example.jp/ → http://server2.example.jp/
* Webサーバ上で、公開しているファイルの名前を変更した
  * http://example.jp/foo.html → http://example.jp/bar.html
* Javaで作られていたWebアプリケーションをRubyで再実装した
  * http://example.jp/servlet/LoginServlet → http://example.jp/login.rb

### よりよいURIを設計するには
* 実装依存の情報、時間が経てば変化する可能性のある情報を含めないようにする
  * http://example.jp/  (example.jpという組織がある限り不変)
    * サーバ管理技術で server1, server2 といったサーバ名はURIから隠蔽できる
  * http://example.jp/foo.html (ファイル名を変更してもURIを変えないようにできる)
  * http://example.jp/login (Webアプリケーションの実装言語によらず、ログイン機能があるかぎり不変)
  * http://example.jp/2014/10/16 (2014年10月16日に公開された情報。情報の公開日は時間がたっても不変)
  * http://example.jp/news/latest (最新のニュース)
  * http://twitter.com/kunishi/status/13211 (ユーザkunishiの、ID番号13211のツイート。kunishiがTwitterに登録していて、ツイートを消したりしない限り不変)
* できる限り、URIはリソースをシンプルに表現するように考える

### どうしてもURIを変更しなければならないときは
極力、HTTP リダイレクト（古いURIを新しいURIに転送するHTTPの仕組。後述）を使う。