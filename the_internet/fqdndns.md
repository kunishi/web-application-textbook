## FQDNとDNS {#fqdn-dns}

TCP/IP上の通信では、すべてIPアドレスを用いて通信相手を指定する。しかし一方で、IPアドレスは単なる数字の羅列であり、人間には極めて覚えにくい。そのため、人間に覚えやすい形式でインターネット上のホスト名を指定する方法として、FQDN（Fully Qualified Domain Name）が使われている。google.com, ja.wikipedia.org, www.example.jpなどはFQDNの例である。

FQDNとIPアドレスの対応づけを管理するインターネットサービスがDNS（Domain Name Service）である。DNSサービスを提供するコンピュータ（DNSサーバ）に対して

*   FQDNを検索すると、対応するIPアドレスを
*   IPアドレスを検索すると、対応するFQDNを（もしあれば）

それぞれ返してくれる。ブラウザなどで http://www.example.jp/ のようにFQDNを用いたURIを指定することができるのは、ソフトウェアの内部でDNSサービスを利用してIPアドレスを取得しているためである。DNSサービスの仕組みは、本講義の範疇を越えるので省略する。