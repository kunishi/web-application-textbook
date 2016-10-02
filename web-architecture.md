# Webアーキテクチャ {#web-architecture}
この章の内容の多くは「Webを支える技術」（山本陽平著、技術評論社、ISBN978-4-7741-4204-3）に拠っている。

## アーキテクチャ（architecture）
- もともとは建築学の用語：建築の様式
- 転じて、コンピュータシステムの様式（構造・設計）を表す用語として使われるようになった
- IBMが大型計算機System/360で「コンピュータ・アーキテクチャ」という用語を使い始めたのが最初

## アーキテクチャスタイル（architecture style）
- アーキテクチャパターン(architecture pattern)ともいう
- 複数のアーキテクチャに共通する性質、様式、作法、流儀など
  - 多数のアーキテクチャ構築から得られた知見
- 代表的なソフトウェア・アーキテクチャスタイル
  - MVC (Model-View-Controller)
  - Layers
  - Pipes and Filters

## REST (Representational State Transfer)
- ネットワークシステムのアーキテクチャスタイルのひとつ
- 2000年にロイ・フィールディング（Roy Fielding)が、Webという極めて大規模なシステムがなぜ成立し、これほど成功したのかについて分析し、博士論文としてまとめた
  - [Architectural Styles and the Design of Network-based Software Architectures](http://www.ics.uci.edu/%7Efielding/pubs/dissertation/top.htm)
  - Roy Fielding：Apache httpdなどの実装に関わり、HTTP 1.0, HTTP 1.1の仕様策定にも関与
- 現代のWebの最重要アーキテクチャスタイル
- 以下の6つのアーキテクチャスタイルを合わせた複合アーキテクチャスタイル
  - クライアント・サーバ
  - ステートレス：アプリケーション状態をサーバで保存しない
  - キャッシュ：通信を通してクライアントが取得したデータを後で使い回す
  - 統一インタフェース：URIに対する操作を、統一された限定的なインタフェースで行う
    - HTTPの場合は、URIに対する操作は8つ（かなり少ない）
  - 階層化システム：システムを階層化しやすい
    - ロードバランサ、プロキシなど
  - コードオンデマンド：プログラムをサーバからダウンロードし、クライアント上で実行