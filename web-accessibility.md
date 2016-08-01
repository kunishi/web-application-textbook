# Webアクセシビリティ

> The power of the Web is in its universality.
> Access by everyone regardless of disability is an essential aspect.
> Tim Berners-Lee, W3C Director and inventor of the World Wide Web
> http://www.w3.org/standards/webdesign/accessibility

- Webアクセシビリティとは
    - Web accessibility means that people with disabilities can use the Web (http://www.w3.org/WAI/intro/accessibility.php)
        - 障害を持つ人がWebを使えること
        - 使える：perceive(知覚する), understand(理解する), navigate(行ったり来たりする), interact(やり取りする) with the Web, contribute to the Web
        - 実在の視覚障害をもつ技術者の話：点字翻訳されたものでしか情報を得られなかったのが、コンピュータ＋音声読み上げ、さらにはWeb＋音声読み上げで情報が格段に得やすくなった
    - 高齢者にも役立つ
    - Webアクセシビリティの技術は、一般人が健康や環境etc.でWebの使い方を制限された場合にも役立つ
        - 画面が狭い、マウスが使えない、etc.
- 障害を持つ人がどのようにWebを使っているのか
    - http://www.w3.org/WAI/intro/people-use-web/storiesより
    - 視覚障害：画面の音声読み上げ＋キーボードによるWebブラウザ操作
    - 色覚障害：フォントサイズや色をブラウザ上でカスタマイズ
    - 聴覚障害：音声内容の代替（文字書き起こし、字幕、手話etc.）
    - 実演：MacOS Xでの画面読み上げ
- Webアクセシビリティの実現に向けて
    - Webアクセシビリティを意識してWebサイトやWebアプリケーションを構築する必要がある
        - ただし、多くの場合コストは大きくはない
    - 例：画像の代替テキスト
        - HTMLに画像をインラインで埋め込むマークアップ
        - <img src=“sample.jpg” width=“100” height=“100” alt=“サンプル画像”>
        - alt属性: 画像を表示する場合に代わりに表示するテキスト、読み上げソフトで画像の代わりに読み上げたり、点字翻訳ソフトで点字として表示されたりする
        - 機械的に決められない
            - 写真→<img src=“photo.jpg” alt="岡山県立大学の講堂の写真”>
            - アイコン→<img src=“icon.png” alt=“*”>
            - ロゴ→<img src=“logo.png” alt="岡山県立大学”>
            - 意味のない画像（スペースの調整用など）→<img src=“spacer.jpg” alt=“”>
    - 例：色の組み合わせ
        - 健常者の見え方と色覚異常を持つ人の見え方は違う。さらに個人差がある
            - http://www.kanzaki.com/docs/html/color-check
        - 色の明度や色差を元にコントラストが十分あるような配色を選ぶ
    - 例：リンクテキスト
        - どちらが適切?
            - `<a href=“http://example.com/entry”>入会フォーム</a>`
            - `入会は<a href=“http://example.com/entry”>こちら</a>から`
        - Webブラウザ＋音声読み上げの場合、ページ内のリンクだけをキーボード操作で行き来できる
            - その際、リンクテキストを読み上げる。前後は読み上げてくれない。
        - 後者だと、なんのリンクかわからない
    - 詳細は
        - WCAG (Web Content Accessibility Guideline) : http://www.w3.org/TR/WCAG20/
        - そのほか関連文書多数
- WebアプリケーションとWebアクセシビリティ
    - 音声読み上げ機能とWebブラウザとの連携方法
        - http://www.w3.org/TR/wai-aria-primer/accessibleelement.png
    - クライアントサイドのWebアプリケーションでは問題が複雑
        - 階層メニューなどのGUI部品をdiv要素などの汎用要素で組み立てることが多い→どういう役割を持ったGUI部品なのかが音声読み上げソフトなどにうまく伝わらない
        - JavaScriptによってDOMを操作→DOMの変更を音声読み上げソフトに伝える仕組みが必要
        - Webアプリケーション、ブラウザ、OS、読み上げソフトすべてが連携する必要がある
    - OSレベルでのアクセシビリティAPI
    - WAI-ARIA
        - GUI部品の役割など、アクセシビリティの確保に必要な情報をマークアップとして記述
        - Webアプリケーション（JavaScript）は、適宜アクセシビリティ関連のマークアップの（DOM中の）値を変更
        - ブラウザはこれを読み取り、アクセシビリティAPIを通して、音声読み上げソフトなどにDOMの変化などを伝達
        - 例: ポップアップメニュー
            - <div role=“menu” aria-haspopup=“true”>メニュー</div>