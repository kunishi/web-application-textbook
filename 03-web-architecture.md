# Webアーキテクチャ {#web-architecture}

この章の内容の多くは「Webを支える技術」（山本陽平著、技術評論社、ISBN978-4-7741-4204-3）に拠っている。

Webがどういうシステムであるかは、アーキテクチャと扱うデータという2つの観点から考察する必要がある。

## リソース

Webで扱うデータは全てURI（URL）を持っている、という認識は多くの人が持っていることと思う。これは間違いではないのだが、のちに詳しく述べるように、URIで扱えるデータには、HTTP（またはHTTPS）以外でアクセスできるものも含まれる。

ここでは、Web上に置かれたデータのことをリソース\(resource\)と呼ぶことにする[^1]。

[^1]: [IETF](http://www.ietf.org)の文書RFC3986にあるとおり、本来、リソースとはURIで表現されるデータを全て表す用語である。したがって、HTTP\(またはHTTPS）以外でアクセスできるデータもリソースと呼ばれる。ただし、URIの用途の多くがWebであることを考慮し、説明を簡単にするため、リソースをHTTP（またはHTTPS）でアクセスできるデータと限定しておく。

### リソースに関して留意すべき点

* リソースは全てURIを持つ
  * リソースには、対応するURIで示される方法でアクセスすることができる

* 1つのリソースが複数の表現を持つことがある
  * 例：リソース「ロサンジェルスの今日の天気情報」は、HTMLページ、PDF、画像など、いろいろな表現で提供される可能性がある

* リソースは時間の経過に従って状態が変化する
  * 例：リソース「ロサンジェルスの今日の天気情報」は、朝には晴れだったが、昼には曇りになっているかもしれない


## アーキテクチャ（architecture）

* もともとは建築学の用語：建築の様式
* 転じて、コンピュータシステムの様式（構造・設計）を表す用語として使われるようになった
* IBMが大型計算機System\/360で「コンピュータ・アーキテクチャ」という用語を使い始めたのが最初

## アーキテクチャスタイル（architecture style）

* アーキテクチャパターン\(architecture pattern\)ともいう
* 複数のアーキテクチャに共通する性質、様式、作法、流儀など
  * 多数のアーキテクチャ構築から得られた知見

* 代表的なソフトウェア・アーキテクチャスタイル
  * MVC \(Model-View-Controller\)
  * Layers
  * Pipes and Filters


## REST \(Representational State Transfer\)

* ネットワークシステムのアーキテクチャスタイルのひとつ
* 2000年にロイ・フィールディング（Roy Fielding\)が、Webという極めて大規模なシステムがなぜ成立し、これほど成功したのかについて分析し、博士論文としてまとめた
  * [Architectural Styles and the Design of Network-based Software Architectures](http://www.ics.uci.edu/%7Efielding/pubs/dissertation/top.htm)
  * Roy Fielding：Apache httpdなどの実装に関わり、HTTP 1.0, HTTP 1.1の仕様策定にも関与

* 現代のWebの最重要アーキテクチャスタイル
* 以下の6つのアーキテクチャスタイルを合わせた複合アーキテクチャスタイル
  * クライアント・サーバ
  * ステートレス：アプリケーション状態をサーバで保存しない
  * キャッシュ：通信を通してクライアントが取得したデータを後で使い回す
  * 統一インタフェース：URIに対する操作を、統一された限定的なインタフェースで行う
    * HTTPの場合は、URIに対する操作は8つ（かなり少ない）
  * 階層化システム：システムを階層化しやすい
    * ロードバランサ、プロキシなど
  * コードオンデマンド：プログラムをサーバからダウンロードし、クライアント上で実行

### クライアント・サーバ

サービスを提供するソフトウェア（サーバ）と、サービスを受けるソフトウェア（クライアント）が通信するアーキテクチャスタイル。

* まずクライアントがサーバにリクエストを送信し、サーバがそれに対してレスポンスを返す
* 多くの場合、クライアントはサーバに対して計算資源に劣る
* 多くの場合、クライアントはサーバに対して非常に多い
* メリット：クライアントとサーバの役割分担によるシステム実装の単純化など
* デメリット：サーバには十分大きな計算資源を割り当てておく必要がある

### ステートレスサーバ

クライアントのアプリケーション状態をサーバで管理しない。結果として、クライアントは、サーバに対して自身のアプリケーション状態の情報を毎回全て送信する。

* 例：奇妙なハンバーガーショップ（「Webを支える技術」6.7節）
* メリット：サーバの実装の単純化、クライアントが増えても対応しやすい（スケーラビリティの向上）
* デメリット：毎回の通信量が増える

ステートレスでないアーキテクチャスタイル、すなわち、サーバがクライアントのアプリケーション状態を保存するアーキテクチャスタイルをステートフルサーバという。FTP（File Transfer Protocol、ファイル転送に広く用いられるプロトコル）やSMTP（Simple Mail Transfer Protocol、メール配送に用いられるプロトコル）など、アプリケーション層のプロトコルはほとんどがステートフルである。一方、HTTPは原則的にステートレスなプロコトルである。

理解を助けるために、[山本陽平氏による喩え話](http://yohei-y.blogspot.jp/2007/10/blog-post.html)を転載しよう。ハンバーガーショップでの客と店員のやりとりの例である。

ステートフルサーバとステートレスサーバはどう違うのか。まずは、ステートフルの例:

> 客: こんにちは

> 店員: いらっしゃいませ。○○バーガーへようこそ

> 客: ハンバーガーセットをお願いします

> 店員: サイドメニューは何になさいますか?

> 客: ポテトで

> 店員: ドリンクは何になさいますか?

> 客: ジンジャーエールで

> 店員: +50円でドリンクをLサイズにできますがいかがですか?

> 客: Mでいいです

> 店員: 以上でよろしいですか?

> 客: はい

店員: かしこまりました

これはいたって普通の会話に見える。では、ステートレスな場合はどうなのか。

> 客: こんにちは

> 店員: いらっしゃいませ。○○バーガーへようこそ

> 客: ハンバーガーセットをお願いします

> 店員: サイドメニューは何になさいますか?

> 客: ハンバーガーセットをポテトでお願いします

> 店員: ドリンクは何になさいますか?

> 客: ハンバーガーセットをポテトとジンジャーエールでお願いします

> 店員: +50円でドリンクをLサイズにできますがいかがですか?

> 客: ハンバーガーセットをポテトとジンジャーエール(M)でお願いします

> 店員: 以上でよろしいですか?

> 客: ハンバーガーセットをポテトとジンジャーエール(M)でお願いします。以上

> 店員: かしこまりました

これを見ると、以下のことが分かる。

* ステートフルなやりとりでは、店員（サーバ）が客（クライアント）のそれまでの注文を覚えている
* ステートレスなやりとりでは、客（クライアント）は毎回すべての注文を繰り返している

### キャッシュ

クライアントが一度取得したリソースを、後に同じリソースが必要になった時に使い回す。

* メリット：サーバ・クライアント間の通信を減らす（通信データ量の削減、通信時間の削減など）
* デメリット：キャッシュ管理を適切に行わないと、古いデータを使ってしまうことがある

### 統一インタフェース

リソースに対する操作を、統一した限定的なインタフェース（操作）で行う。

* HTTPの場合、リソースに対する操作は8種類（かなり少ない）
* メリット：実装が簡単になる
* デメリット：適切なインタフェースの設計が難しい

### 階層化システム

システム全体を階層化する。

* 例：サーバとクライアントの間に、サーバの負荷分散のためにロードバランサを挟む。あるいはアクセス制限のためにプロキシサーバを挟む
* ポイント：全て同一のプロトコルを使うことで、サーバ・クライアントともシステムの変更がほとんど不要
  * クライアントとロードバランサ、ロードバランサとサーバ、両方ともHTTPを用いる、など


### コードオンデマンド

プログラムコードをサーバからダウンロードし、クライアント上でそれを実行する。

* JavaScript, Flashなど
* メリット：クライアントの拡張が容易
* デメリット：システムが複雑化する、セキュリティ
