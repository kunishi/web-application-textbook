# Webアプリケーションの性能向上

- ボトルネック（bottleneck）
    - 性能向上を阻んでいるシステムの設計上の原因。多くの場合、システム全体から見ると小さな部分である。
- Webアプリケーションの性能（応答性）が悪い場合、ボトルネックがどこにあるか
    - クライアント（Webブラウザ）のパフォーマンス
        - HTMLレンダリング
        - JavaScriptの実行速度
    - 通信経路
        - 通信データ量
        - 通信回数
        - TCP通信回数
    - サーバの資源
        - CPU使用率
        - 同時接続数
        - メモリ使用率
        - ディスクIO
    - DBMSの性能
        - SQL問合せの実行速度
        - 非効率的なSQL問合せ
        - インデックス（索引）
        - WebサーバとRDBMS間の通信
    - 外部サービス（Webサービス）の応答性
    - ボトルネックの可能性が極めて多岐に渡るため、実際にどこがボトルネックかは計測してみないとわからない
- 代表的な対策
    - サーバの負荷分散
        - 同一構成のWebサーバを複数台用意しておいて、リクエストを分散する
        - DNSラウンドロビン: ホスト名に対して複数のIPアドレス、DNSの応答で複数のIPアドレスに分散
            - 簡単だが、DNSの仕組に依存→負荷分散が十分できないことも
            - サーバがダウンしているかどうかは考慮されない
        - ロードバランサ
            - クライアントから見ると1台のWebサーバ
            - 受け取ったリクエストを複数台のリアルサーバから1台を選択して中継
            - 正常に稼働しているサーバのみから選択
            - リクエストの中身は考慮しない
            - 専用ハードウェア、ソフトウェアの両方あり
        - リバースプロキシ
            - クライアントから見ると1台のWebサーバ
            - リクエストの中身を考慮して、適切なWebサーバにリクエストを転送
            - Webサーバからのレスポンスはリバースプロキシに返る。これをクライアントに転送
            - 主な用途例
                - リクエストURIのパスが `/image/*` だったら画像用のWebサーバに、そうでない場合はWebアプリケーションサーバに
                    - Webアプリケーションサーバは、多くの場合、アプリケーション高速化のため言語処理系をメモリ上に展開→1回のリクエストを処理するメモリ使用量が多い
    - 通信量の削減
        - キャッシュサーバ
            - クライアントから見ると1台のWebサーバ
            - リクエストをそのままWebサーバに転送
            - レスポンスをクライアントに送りつつ、キャッシュサーバ上に一時保存
            - 次にクライアントから同じURIへのリクエストがあったら、一時保存されたキャッシュデータをクライアントに返す
            - 考慮すべき点
                - キャッシュをどれだけ保存すべきか（期間、容量）
                - サーバでのデータの変更をどうキャッチするか
                - ステートフルな通信にはそもそも向かない（ログインしているクライアント vs. ログインしていないクライアント）
        - その他
            - 転送データのzip圧縮
                - テキストに対しては効果あり、圧縮済みバイナリデータにはあまり効果なし
            - 画像・動画etc.の圧縮改善
                - 圧縮率をあげる
                - より圧縮率の高いフォーマットを使う
    - ディスクIO、重複する計算の削減
        - メモリキャッシュ
            - アプリケーション内部で使用するデータをサーバ上のメモリ上にキャッシュ