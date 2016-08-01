# HTML

- HTML（Hypertext Markup Language）
- Webページを記述するための書式（マークアップ言語）
  - マークアップ言語はプログラミング言語ではない
- プレインテキストに、タグと呼ばれる特別な記号を埋め込み、テキストの各部分の意味を明確にする
  - 例：`<title>〜</title>`…ページのタイトル、`<p>〜</p>`…段落
  - 開始タグ（`<html>`, `<title>`, `<head>`など）、終了タグ（`</html>`, `</title>`, `</head>`など）
  - ほとんどの場合、開始タグと終了タグは対にして用いる
  - 開始タグと終了タグで囲まれた部分：要素
  - 要素は入れ子にすることができる（例：`<html><head>〜</head><body><p>〜</p></body></html>`）
- 他ページへのリンク：`<a href="リンク先のURL">`リンクを張る文字列</a>
  - `href="..."`の部分を属性という。属性は要素の開始タグに付けることができる。hrefの部分を属性名、"..." の部分を属性値という。
- HTML文書
  - html要素のみからなる文書
  - html要素の中に他の要素が入れ子状に含まれる
  - HTML文書=html要素を根とする木
  - 1行目に、文書がHTMLで書かれていることを表す目印（`<!DOCTYPE html>`, DOCTYPE宣言）を置く
    - 以前は文書型宣言と呼ばれ、記法がもっと複雑であった。最近はこれだけでよい。
  - 一般的に、ページの外見（見た目）はHTML文書には記述されない
    - HTML文書をどのように表示するかはWebエージェント次第
    - ページの見た目を指定する方法：スタイルシート（CSSなど）…本講義では触れない

## HTMLで書かれたテキストファイル（HTML文書）の例

``` html
<!DOCTYPE html>
<html>
   <head>
	<title>はじめてのHTML文書</title>
   </head>
   <body>
	<p>Hello world!</p>
	<a href="http://www.oka-pu.ac.jp">岡山県立大学トップページ</a>
   </body>
</html>
```