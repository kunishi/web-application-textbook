# Webを支える技術 {#preliminaries}

ここでは、Webを理解する上で必要となる背景知識について述べる。

## Web

> The Web is intended to be an Internet-scale distributed hypermedia system - [Architectural Styles and the Design of Network-based Software Architectures](http://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm)

* インターネット上で提供されるサービスの一つ
  * リンクによって互いに関連付けられ、インターネット上に分散して配置された多数のページ（またはデータ）
  * サーバ・クライアント型のシステム
    * サーバ：ページやデータが配置されるコンピュータ
    * クライアント：ページやデータを取得するコンピュータ



### 歴史

Webの原型は、1965年にTed Nelsonによって提案されたハイパーテキスト（HyperText）という概念に遡ることができる。複数のテキスト文書をリンクで結びつけたデータ構造と、それを編集・閲覧するシステムであり、実装も行われた。また、この概念は後にApple社によりHyperCardというソフトウェアとして実現され、広く使われるようになった。ただし、これらは単一コンピュータ上にデータとシステムが集約されていることが前提であった。

1989年、欧州原子核共同研究機構（Conseil Européan pour la Recherche Nucléaire, CERN）のTim Berners-Leeが開発。目的は、各国の科学者間で情報を共有する手段であった。ここで、ハイパーテキストを分散ネットワークシステム上で実現する、というWebの基本的な概念が実現された。

![](/web/image03.png)

\([http:\/\/commons.wikimedia.org\/wiki\/File%3AWorldWideWeb.1.png](http://commons.wikimedia.org/wiki/File%3AWorldWideWeb.1.pngより引用)\)

1993年、イリノイ大学米国立スーパーコンピュータ応用研究所（National Center for Supercomputing Application, NCSA）がブラウザMosaicを公開。本文中に画像を混在させることが可能で、現在のブラウザの原型である。

![](/web/image00.png)

\(NCSA\/University of Illinois, [http:\/\/www.ncsa.illinois.edu\/News\/Images\/](http://www.ncsa.illinois.edu/News/Images/)\)

### Webの用途

* Webページ・Webサイト：ブラウザで閲覧
* ユーザインタフェース：プリンタやルータの設定画面など
* 汎用的なデータ通信手段：スマートフォンのアプリなど

### Webを支える技術

* HTTP, URI, HTML
* インターネットの諸技術（TCP\/IP, DNS, …）
* ハイパーメディア（Hyper Media）

  * 画像、音声、映像、テキストなどをリンクで相互に結びつけて構成された情報媒体。リンクをたどることで、非線形的に情報を取得していくことが可能である。1965年にTed Nelsonによって提案された概念。

* 分散システム（Distributed System）

  * 複数のコンピュータを組み合わせて、処理を分散させる情報システム


その他、HTML以外のデータ記述形式、暗号化技術（公開鍵暗号）なども使われている。Webは、極めて多様な技術を集めて構築された情報システムであると言える。

## インターネット（the Internet）

* TCP\/IP（またはUDP\/IP）を標準プロトコルとする世界的規模のネットワーク
  * TCP: Transmission Control Protocol
  * UDP: User Datagram Protocol
  * IP: Internet Protocol


### 歴史

* 1962年、米・国防総省でインターネットの原型となる分散ネットワークの研究が開始される
* 1969年、インターネットの原型が誕生\(ARPANET\)
* 1970年代半ば、TCP\/IPが開発される→UNIXに実装
* 1983年、軍事部門と研究ネットワークが分離される
* 1984年、日本でTCP\/IPベースのネットワークが誕生（JUNET）

### インターネットの通信プロトコル

以下の4層構造である。DARPAモデルと呼ばれ、[RFC1122](http://tools.ietf.org/html/rfc1122)で規定されている。

* アプリケーション層：具体的なインターネットアプリケーションの通信を実現する層（HTTP, SMTP, DNSなど）
* トランスポート層：データ転送（TCP, UDPなど）
* インターネット層：パケット配送、ルーティング（IP）
* リンク層：物理的なケーブルやネットワークアダプタ（イーサネット、WiFiなど）

<p><a href="https://commons.wikimedia.org/wiki/File:IP_stack_connections.svg#/media/File:IP_stack_connections.svg"><img src="https://upload.wikimedia.org/wikipedia/commons/thumb/c/c4/IP_stack_connections.svg/1200px-IP_stack_connections.svg.png" alt="IP stack connections.svg"></a><br>By <a href="https://en.wikipedia.org/wiki/User:Kbrose" class="extiw" title="en:User:Kbrose">en:User:Kbrose</a> - Prior Wikipedia artwork by en:User:Cburnett, <a href="http://creativecommons.org/licenses/by-sa/3.0/" title="Creative Commons Attribution-Share Alike 3.0">CC 表示-継承 3.0</a>, <a href="https://commons.wikimedia.org/w/index.php?curid=1831900">https://commons.wikimedia.org/w/index.php?curid=1831900</a></p>

\([http:\/\/ja.wikipedia.org\/wiki\/%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB:IP\_stack\_connections.svg](http://ja.wikipedia.org/wiki/%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB:IP_stack_connections.svg)\)

### IPアドレス

IP上で、ネットワーク上の機器を識別するために使用する番号。下記は、現在広く用いられているIPv4のアドレス構成である。

<p><a href="https://commons.wikimedia.org/wiki/File:Ipv4_address_ja.svg#/media/File:Ipv4_address_ja.svg"><img src="https://upload.wikimedia.org/wikipedia/commons/thumb/2/29/Ipv4_address_ja.svg/1200px-Ipv4_address_ja.svg.png" alt="Ipv4 address ja.svg"></a><br>By Original: <a href="https://en.wikipedia.org/wiki/User:Indeterminate" class="extiw" title="en:User:Indeterminate">en:User:Indeterminate</a> Japanese translation: <a href="//commons.wikimedia.org/w/index.php?title=User:Lusheeta&amp;action=edit&amp;redlink=1" class="new" title="User:Lusheeta (page does not exist)">User:Lusheeta</a> - <a href="//commons.wikimedia.org/wiki/File:Ipv4_address.svg" title="File:Ipv4 address.svg">File:Ipv4_address.svg</a>, パブリック・ドメイン, <a href="https://commons.wikimedia.org/w/index.php?curid=7688865">https://commons.wikimedia.org/w/index.php?curid=7688865</a></p>

\([http:\/\/ja.wikipedia.org\/wiki\/%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB:Ipv4\_address\_ja.svg](http://ja.wikipedia.org/wiki/%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB:Ipv4_address_ja.svg)\)

### FQDNとDNS

TCP\/IP上の通信では、すべてIPアドレスを用いて通信相手を指定する。しかし一方で、IPアドレスは単なる数字の羅列であり、人間には極めて覚えにくい。そのため、人間に覚えやすい形式でインターネット上のホスト名を指定する方法として、FQDN（Fully Qualified Domain Name）が使われている。google.com, ja.wikipedia.org, www.example.jpなどはFQDNの例である。

FQDNとIPアドレスの対応づけを管理するインターネットサービスがDNS（Domain Name Service）である。DNSサービスを提供するコンピュータ（DNSサーバ）に対して

* FQDNを検索すると、対応するIPアドレスを
* IPアドレスを検索すると、対応するFQDNを（もしあれば）

それぞれ返してくれる。ブラウザなどで http:\/\/www.example.jp\/ のようにFQDNを用いたURIを指定することができるのは、ソフトウェアの内部でDNSサービスを利用してIPアドレスを取得しているためである。DNSサービスの仕組みは、本講義の範疇を越えるので省略する。

## 文字の扱い

Webに関する技術を取り上げるとき、文字をコンピュータでどう表現するか、という問題があちこちに関わってくる。これは極めて複雑な話で、本格的に説明するとそれだけでかなりの時間を必要とする。ここでは、Webを理解する上で必要な、ごく基本的な知識のみ説明することとする。

### 文字集合

コンピュータで文字を扱う場合、どのような文字が扱えるのか、厳密に定めておく必要がある。これを文字集合（文字セット）という。通常、文字集合では、含まれる文字に整数を順に振っている。我々がよく目にする文字集合には次のようなものがある。

* ASCII…英大文字・小文字、数字、記号など
* ISO 8859…欧米やロシアなどで使われる文字を集めたもの
* JIS X 0208, JIS X 0213…日本語（漢字を含む）で使われる文字を集めたもの
* Unicode, ISO\/IEC 10646…世界で使われるすべての文字からなる文字集合。両者はほぼ同じものと考えてよい。

### 文字符号化方式

文字集合の次に決めなければならないのが、「文字集合に含まれる文字を、どのようにコンピュータ上で表現するか」である。通常、コンピュータ上では、文字を整数列で表現する。つまり、ある文字集合に含まれるすべての文字に対して、どのような整数列を対応付けるか、決めなければならない。これを文字符号化方式という。我々がよく目にする文字符号化方式には、次のようなものがある。

* ASCII … ASCII文字集合に対するもの
* ISO-8859-1, ISO-8859-2, … ISO 8859に対応するもの
* ISO-2022-JP … JIS X 0208に対応するもの。いわゆるJIS文字コード。
* EUC-JP … JIS X 0208 に対応するもの。いわゆるEUC文字コード。
* Shift\_JIS … JIS X 0208 に対応するもの。いわゆるシフトJIS文字コード。
* UTF-8, UTF-16 … いずれも Unicode に対応するもの。

### 文字コード

文字集合と文字符号化方式をひっくるめて、文字コードという言葉で表すことがある。厳密に定義された用語ではない。

### Webにおける文字コード

上記のように、歴史的経緯からさまざまな文字集合や文字符号化方式が乱立しているが、Webに関する限り、原則として「文字集合としてUnicode、文字符号化方式としてUTF-8を使う」と考えてよい。これ以外の選択肢は、よほどの理由がない限り選択してはいけない。

