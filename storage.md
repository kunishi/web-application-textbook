# HTTPクッキーと認証
ここまで述べたようにHTTPはステートレスなプロトコルであり、クライアント状態をサーバが保存することはない。しかし現実のアプリケーションでは、サービスへのログインなど、何らかのクライアント状態によってサーバの振る舞いを変えるニーズが存在する。

本章ではまずHTTPクッキーについて述べ、クライアント状態の典型例であるユーザ認証の実現方法をいくつか示す。

## HTTPクッキー
**HTTPクッキー**（HTTP cookie）[^1]は、Webサーバ上の情報をWebクライアントに保存しHTTPで利用するための汎用技術であり、これによりHTTPにおいて状態を実現することができる。1994年に、当時大きなシェアを持っていたWebブラウザNetscape Navigatorの開発元であるNetscape Communications社によって提案・実装された。その後IETFによる国際標準化が行われ、2011年に発行された[RFC 6265](https://tools.ietf.org/html/rfc6265)が最新版である。

[^1]: クッキーと略されることもある。

HTTPクッキーは、レスポンスメッセージにおける`Set-Cookie`ヘッダ、リクエストメッセージにおける`Cookie`ヘッダという2つのヘッダによって実現される。あるWebサーバから`Set-Cookie`ヘッダを付けてレスポンスメッセージを送信すると、以降、このレスポンスメッセージを受け取ったWebクライアントからそのWebサーバへリクエストメッセージを送信するときには`Cookie`ヘッダを付け、その中に、`Set-Cookie`ヘッダに含まれていた情報を含める。

例を示そう。Webブラウザから`http://example.jp/sample`にHTTPリクエストを送信したところ、そのレスポンスに次のヘッダが含まれていたとする。

```
Set-Cookie: SID=31d4d96e407aad42
```

このヘッダの中身`SID=31d4d96e407aad42`がHTTPクッキーであり、この例では名前`SID`の値が`31d4d96e407aad42`であることを示している。

このレスポンスメッセージを受け取ったWebブラウザは、以降、`http://example.jp/sample`にHTTPリクエストを送信する場合には次のヘッダを必ず付ける。

```
Cookie: SID=31d4d96e407aad42
```

HTTPクッキーには、有効期限、URIに関する有効範囲（ホスト名、パス）、セキュリティ上の制限（HTTPSでのみ有効、JavaScriptからのアクセス不可）などを付与することができる。次の例は、有効期限をグリニッジ標準時2021年6月9日10時18分14秒までとし、かつ`http://example.jp/`で始まるすべてのURIに対して同じHTTPクッキーを付与するという例である。

```
Set-Cookie: SID=31d4d96e407aad42; Path=/; Expires=Wed, 09 Jun 2021 10:18:14 GMT
```

なお、これらの制限は、リクエストメッセージ中の`Cookie`ヘッダには含まれない。

```
Cookie: SID=31d4d96e407aad42
```

HTTPクッキーを削除したい場合は、空のクッキーを送信したり、有効期限を過去に設定したクッキーを送信する。

```
Set-Cookie: SID=31d4d96e407aad42; Path=/; Expires=Sun, 06 Nov 1994 08:49:37 GMT
```

現実のWebクライアントではクッキーの数や容量に制限がある。RFC 6265では、Webクライアントを実装する場合には、次の条件を最低限守るべきだとしている。

* 一つのクッキー（名前と値のペア）の大きさが4096バイト以上扱えること
* 1ドメインあたりのクッキー数を50以上扱えること
* クッキー総数を3000以上扱えること

## HTTPで規定されている認証方式
HTTP/1.1には、**Basic認証**と**Digest認証**というユーザ認証方式が規定されている（[RFC 2617](https://tools.ietf.org/html/rfc2617), [RFC 7616](https://tools.ietf.org/html/rfc7616)）。これらはいずれもHTTPクッキーを使わない。

Basic認証とDigest認証では、いずれも、まず最初に認証なしのHTTPリクエストをサーバに送信し、ステータスコード`401 Unauthorized`のHTTPレスポンスを得る。そして、そのレスポンス中に含まれている情報を用いて認証付きのHTTPリクエストを送信する。

### Basic認証
Basic認証では、認証のためのユーザIDとパスワードをBase64エンコーディングした文字列をリクエストヘッダに含める。

例を示そう。まず認証なしのHTTPリクエストをサーバ`example.jp`に送信する。

```
GET / HTTP/1.1
Host: example.jp
```

すると`example.jp`は、このURIにアクセスするにはBasic認証が必要であるという情報を`WWW-Authentication`ヘッダにつけて、`401 Unauthorized`のHTTPレスポンスを返す。`realm`にはWebサーバ上でこの認証につけられた名前が記されている。

```
HTTP/1.1 401 Unauthorized
WWW-Authentication: Basic realm="WallyWorld"
```

このHTTPレスポンスを受け取ったWebクライアントは、ユーザに、認証に必要となるユーザ名とパスワードを入力するよう促す[^1]。

[^1]: 多くのWebブラウザでは、ポップアップウィンドウを表示して、その中にユーザ名とパスワードを入力させるようにしている。

ユーザ名とパスワードを受け取ると、Webクライアントはこれらを`:`で連結してBase64エンコードし、`Authorization`ヘッダに格納して再度HTTPリクエストを行う。以下の例では、`taro:pass`（ユーザ名`taro`、パスワード`pass`）をBase64エンコードした文字列`dGFybzpwYXNzCg==`が格納されている。

```
GET / HTTP/1.1
Host: example.jp
Authorization: Basic dGFybzpwYXNzCg==
```

このリクエストを受け取ったWebサーバは、`dGFybzpwYXNzCg==`をBase64デコードしてユーザ名とパスワードを取得し、ユーザ認証を行う。

多くのWebクライアントでは、ユーザによって入力されたユーザ名とパスワードを内部で保存しており、2回目以降のHTTPリクエストでは、ユーザからの入力なしに`Authorization`ヘッダを付与することが多い。

Basic認証では、誰でも容易に復号可能な状態で認証情報（ユーザ名とパスワード）がネットワーク上を流れるという問題点がある。したがって、TLSによる通信路の暗号化を併用することが必須となる。

### Digest認証
Digest認証では、ユーザ名やパスワードから一方向ハッシュ関数で計算された値（文字列）をHTTPリクエストに含めるため、Basic認証よりはセキュアな認証方式であると言える。

Digest認証でも、最初はまず認証なしのHTTPリクエストを送信し、`401 Unauthorized`のHTTPレスポンスを得る。例は次のようになる。

```
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Digest
   realm="http-auth@example.org",
   qop="auth, auth-int",
   algorithm=SHA-256,
   nonce="7ypf/xlj9XXwfDPEoM4URrv/xwf94BcCAzFZH4GiTo0v",
   opaque="FQhe/qaU925kfnzjCev0ciny7QMkPqMAFRtzCUYo5tdS"
WWW-Authenticate: Digest
   realm="http-auth@example.org",
   qop="auth, auth-int",
   algorithm=MD5,
   nonce="7ypf/xlj9XXwfDPEoM4URrv/xwf94BcCAzFZH4GiTo0v",
   opaque="FQhe/qaU925kfnzjCev0ciny7QMkPqMAFRtzCUYo5tdS"
```

`nonce`はタイムスタンプなどからサーバが生成する文字列であり、Webクライアントが認証情報を組み立てるときに用いられる。`opaque`もサーバが生成する文字列であるが、これはそのままHTTPリクエストに埋め込まれ、クライアントの同一性を保証する目的で用いられる。

Basic認証と同様に、これを受け取ると、Webクライアントはユーザにユーザ名とパスワードの入力を求め、それらを元に認証付きのHTTPリクエストを組み立て、サーバに送信する。この際、ユーザから入力されたユーザ名とパスワードのほか、`WWW-Authenticate`ヘッダに含まれていた`realm`と`nonce`、およびクライアントが生成する文字列`cnonce`などからハッシュ値を計算し、それを`response`に格納する。例は次のようになる。

```
Authorization: Digest username="Mufasa",
     realm="http-auth@example.org",
     uri="/dir/index.html",
     algorithm=MD5,
     nonce="7ypf/xlj9XXwfDPEoM4URrv/xwf94BcCAzFZH4GiTo0v",
     nc=00000001,
     cnonce="f2/wE4q74E6zIJEtWaHKaf5wv/H5QzzpXusqGemxURZJ",
     qop=auth,
     response="8ca523f5e9506fed4657c9700eebdbec",
     opaque="FQhe/qaU925kfnzjCev0ciny7QMkPqMAFRtzCUYo5tdS"
```

これを受け取ったサーバは、同様にハッシュ値を計算し、結果が`response`と一致していれば認証成功となる。

Digest認証の技術的基盤となっているのはMD5アルゴリズムの一方向性（データ$$D$$からMD5ハッシュ値$$H = md5(D)$$を計算したとき、$$H$$から$$D$$を求めることは事実上できない）であるが、この点に関する脆弱性が発見されており、`response`からユーザ名とパスワードを取得することがかなり容易に行えると考えられる。したがって、Digest認証もBasic認証と同様、通信路の暗号化が必須であると言える。

## HTTPクッキーを用いた認証
現代のWebアプリケーションで最もよく用いられている認証方式は、HTTPクッキーを用いるものである。

この認証方式では、通常、Webサーバが認証のためのフォームを含んだWebページ（ここでは`http://example.jp/login`としよう）を用意しておく。

``` html
<form action="/service" method="POST">
  <input type="text" id="username">
  <input type="password" id="password">
  <input type="submit" value="ログイン">
</form>
```

ここまで何度か見てきたように、このフォームにユーザがユーザ名とパスワードを入力してログインボタンをクリックすると、次のHTTPリクエストがサーバに送信される。

```
POST /service HTTP/1.1
Host: example.jp
Content-Type: application/x-www-form-urlencoded

username=taro&password=pass
```

WebサーバはHTTPリクエスト中のPOSTパラメータの値を元にユーザ認証を行う。認証が成功すると、Webサーバはこのログインに対して固有の文字列（**セッションID**）を生成し、これをHTTPクッキーとしてWebブラウザに返す。同時に、生成したセッションIDをユーザ名と紐づけてWebサーバ上でも保存しておく（セッションIDテーブル）。

```
Set-Cookie: SID=31d4d96e407aad42; Path=/
```

HTTPクッキーの機能通り、Webブラウザは以降の`example.jp`との接続においてHTTPクッキー`SID=31d4d96e407aad42`をHTTPリクエストに付与して送信する。

```
Cookie: SID=31d4d96e407aad42
```

Webサーバ`example.jp`では、このHTTPクッキーを基にセッションIDテーブルを検索し、どのユーザからのリクエストかを判断する。サービスからユーザをログアウトさせたい場合は、対応するセッションIDをセッションIDテーブルから削除するとともに、WebブラウザのHTTPクッキーを削除する。

この方法でも、最初にフォームからユーザ名とパスワードを送信するときには、リクエストボディ中に平文でパスワードが含まれている。したがってTLSによる通信路の暗号化が必須である。またなんらかの方法でセッションIDを第三者に奪取されると、第三者によるなりすまし攻撃を受ける可能性がある（[クロスサイト・リクエスト・フォージェリ](http://jvndb.jvn.jp/ja/cwe/CWE-352.html)、CSRF）。

このような弱点があるにも関わらず、Basic認証やDigest認証が使われず、HTTPクッキーを用いた認証が広く使われているのはなぜか。例えば[独立行政法人産業総合研究所情報セキュリティ研究センターのページ](https://www.rcis.aist.go.jp/special/MutualAuth/faq/detail-ja.html#formauth)には、次の考察結果を理由として挙げている。

> 1. ログアウト機能がないこと
> 2. ログインせずに同じページのコンテンツを閲覧するという使い方（ゲストアクセス）ができないこと
> 3. サーバ側からログイン状態を無効にする手段が存在しないこと
> 4. ホストをまたがったシングルサインオンが実現できないこと