# Webアプリケーションのアーキテクチャ

これまでの内容を基に、Webアプリケーションのアーキテクチャについて述べる。まずWebブラウザで動作するアプリケーションについて述べ、次にスマートフォンなどのアプリの構成についても簡単に述べる。

## Webブラウザで動作するアプリケーション

WebブラウザとWebサーバとで構成されるアプリケーションの場合、ソフトウェアが動作する環境は以下の2つがある。

* Webサーバ。任意のプログラミング言語によるソフトウェアが実行できる。開発効率を重視してRubyやPHP, Perl, Python, JavaScriptといったインタプリタ型言語でソフトウェアを実装することが多い。高性能を要求される場合はJavaやC#といったハイブリット型言語も用いられる。
* Webブラウザ。ブラウザにはJavaScriptインタプリタが内包されており、一定の条件下で、Webサーバから取得されたJavaScriptソフトウェアをブラウザ内で実行することが可能である。

したがってWebアプリケーションのアーキテクチャは、Webブラウザ内でJavaScriptプログラムを用いるかどうかによって大まかに2つに分けられる。概要を以下の図に示す。

<a title="By DanielSHaischt, via Wikimedia Commons [CC BY-SA 3.0 (http://creativecommons.org/licenses/by-sa/3.0)], via Wikimedia Commons" href="https://commons.wikimedia.org/wiki/File%3AAjax-vergleich-en.svg"><img width="512" alt="Ajax-vergleich-en" src="https://upload.wikimedia.org/wikipedia/commons/thumb/0/0b/Ajax-vergleich-en.svg/512px-Ajax-vergleich-en.svg.png"/></a>
(https://commons.wikimedia.org/wiki/File%3AAjax-vergleich-en.svg, By DanielSHaischt, via Wikimedia Commons [<a href="http://creativecommons.org/licenses/by-sa/3.0">CC BY-SA 3.0</a>], <a href="https://commons.wikimedia.org/wiki/File%3AAjax-vergleich-en.svg">via Wikimedia Commons</a>)

