# Webを支える技術 {#preliminaries}

ここでは、Webを理解する上で必要となる背景知識について述べる。

## Web

> The Web is intended to be an Internet-scale distributed hypermedia system - [Roy Fielding, “Architectural Styles and the Design of Network-based Software Architectures”](http://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm)

* インターネット上で提供されるサービスの一つ
  * リンクによって互いに関連付けられ、インターネット上に分散して配置された多数のページ（またはデータ）
  * サーバ・クライアント型のシステム
    * サーバ：ページやデータが配置されるコンピュータ
    * クライアント：ページやデータを取得するコンピュータ

### 歴史

Webの原型は、1965年にTed Nelsonによって提案されたハイパーテキスト（HyperText）という概念に遡ることができる。複数のテキスト文書をリンクで結びつけたデータ構造と、それを編集・閲覧するシステムであり、実装も行われた。また、この概念は後にApple社によりHyperCardというソフトウェアとして実現され、広く使われるようになった。ただし、これらは単一コンピュータ上にデータとシステムが集約されていることが前提であった。

1989年、欧州原子核共同研究機構（Conseil Européan pour la Recherche Nucléaire, CERN）のTim Berners-Leeが開発。目的は、各国の科学者間で情報を共有する手段であった。ここで、ハイパーテキストを分散ネットワークシステム上で実現する、というWebの基本的な概念が実現された。

![](/web/image03.png)

([http://commons.wikimedia.org/wiki/File%3AWorldWideWeb.1.png](http://commons.wikimedia.org/wiki/File%3AWorldWideWeb.1.pngより引用))

1993年、イリノイ大学米国立スーパーコンピュータ応用研究所（National Center for Supercomputing Application, NCSA）がブラウザMosaicを公開。本文中に画像を混在させることが可能で、現在のブラウザの原型である。

![](/web/image00.png)

(NCSA/University of Illinois, [http://www.ncsa.illinois.edu/News/Images/](http://www.ncsa.illinois.edu/News/Images/))

### Webの用途

* Webページ・Webサイト：ブラウザで閲覧
* ユーザインタフェース：プリンタやルータの設定画面など
* 汎用的なデータ通信手段：スマートフォンのアプリなど

### Webを支える技術

* HTTP, URI, HTML
* インターネットの諸技術（TCP/IP, DNS, …）
* ハイパーメディア（Hyper Media）
  * 画像、音声、映像、テキストなどをリンクで相互に結びつけて構成された情報媒体。リンクをたどることで、非線形的に情報を取得していくことが可能である。1965年にTed Nelsonによって提案された概念。
* 分散システム（Distributed System）
  * 複数のコンピュータを組み合わせて、処理を分散させる情報システム

その他、HTML以外のデータ記述形式、暗号化技術（公開鍵暗号）なども使われている。Webは、極めて多様な技術を集めて構築された情報システムであると言える。
