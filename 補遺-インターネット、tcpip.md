# 補遺: インターネット（the Internet）

* TCP/IP（またはUDP/IP）を標準プロトコルとする世界的規模のネットワーク
  * TCP: Transmission Control Protocol
  * UDP: User Datagram Protocol
  * IP: Internet Protocol

## 歴史

* 1962年、米・国防総省でインターネットの原型となる分散ネットワークの研究が開始される
* 1969年、インターネットの原型が誕生(ARPANET)
* 1970年代半ば、TCP/IPが開発される→UNIXに実装
* 1983年、軍事部門と研究ネットワークが分離される
* 1984年、日本でTCP\/IPベースのネットワークが誕生（JUNET）

## インターネットの通信プロトコル

以下の4層構造である。DARPAモデルと呼ばれ、[RFC1122](http://tools.ietf.org/html/rfc1122)で規定されている。

* アプリケーション層：具体的なインターネットアプリケーションの通信を実現する層（HTTP, SMTP, DNSなど）
* トランスポート層：データ転送（TCP, UDPなど）
* インターネット層：パケット配送、ルーティング（IP）
* リンク層：物理的なケーブルやネットワークアダプタ（イーサネット、WiFiなど）

<p><a href="https://commons.wikimedia.org/wiki/File:IP_stack_connections.svg#/media/File:IP_stack_connections.svg"><img src="https://upload.wikimedia.org/wikipedia/commons/thumb/c/c4/IP_stack_connections.svg/1200px-IP_stack_connections.svg.png" alt="IP stack connections.svg"></a><br>By <a href="https://en.wikipedia.org/wiki/User:Kbrose" class="extiw" title="en:User:Kbrose">en:User:Kbrose</a> - Prior Wikipedia artwork by en:User:Cburnett, <a href="http://creativecommons.org/licenses/by-sa/3.0/" title="Creative Commons Attribution-Share Alike 3.0">CC 表示-継承 3.0</a>, <a href="https://commons.wikimedia.org/w/index.php?curid=1831900">https://commons.wikimedia.org/w/index.php?curid=1831900</a></p>

([http://ja.wikipedia.org/wiki/%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB:IP_stack_connections.svg](http://ja.wikipedia.org/wiki/%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB:IP_stack_connections.svg))

## IPアドレス

IP上で、ネットワーク上の機器を識別するために使用する番号。下記は、現在広く用いられているIPv4のアドレス構成である。

<p><a href="https://commons.wikimedia.org/wiki/File:Ipv4_address_ja.svg#/media/File:Ipv4_address_ja.svg"><img src="https://upload.wikimedia.org/wikipedia/commons/thumb/2/29/Ipv4_address_ja.svg/1200px-Ipv4_address_ja.svg.png" alt="Ipv4 address ja.svg"></a><br>By Original: <a href="https://en.wikipedia.org/wiki/User:Indeterminate" class="extiw" title="en:User:Indeterminate">en:User:Indeterminate</a> Japanese translation: <a href="//commons.wikimedia.org/w/index.php?title=User:Lusheeta&amp;action=edit&amp;redlink=1" class="new" title="User:Lusheeta (page does not exist)">User:Lusheeta</a> - <a href="//commons.wikimedia.org/wiki/File:Ipv4_address.svg" title="File:Ipv4 address.svg">File:Ipv4_address.svg</a>, パブリック・ドメイン, <a href="https://commons.wikimedia.org/w/index.php?curid=7688865">https://commons.wikimedia.org/w/index.php?curid=7688865</a></p>

([http://ja.wikipedia.org/wiki/%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB:Ipv4_address_ja.svg](http://ja.wikipedia.org/wiki/%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB:Ipv4_address_ja.svg))

## FQDNとDNS

TCP/IP上の通信では、すべてIPアドレスを用いて通信相手を指定する。しかし一方で、IPアドレスは単なる数字の羅列であり、人間には極めて覚えにくい。そのため、人間に覚えやすい形式でインターネット上のホスト名を指定する方法として、FQDN（Fully Qualified Domain Name）が使われている。`google.com`, `ja.wikipedia.org`, `www.example.jp`などはFQDNの例である。

FQDNとIPアドレスの対応づけを管理するインターネットサービスがDNS（Domain Name Service）である。DNSサービスを提供するコンピュータ（DNSサーバ）に対して

* FQDNを検索すると、対応するIPアドレスを
* IPアドレスを検索すると、対応するFQDNを（もしあれば）

それぞれ返してくれる。ブラウザなどで `http://www.example.jp/` のようにFQDNを用いたURIを指定することができるのは、ソフトウェアの内部でDNSサービスを利用してIPアドレスを取得しているためである。DNSサービスの仕組みは、本講義の範疇を越えるので省略する。