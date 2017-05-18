# sphinxcontrib-pandoc-markdown

sphinxでマークダウンを扱う場合は`recommonmark`を使用する方法があります。
`recommonmark`は拡張により数式や`reStructuredText`を埋め込めるため非常に便利です。

しかし、以下のような欠点があります。

- CommonMarkが元なのでテーブル記法がない
- 拡張機能の設定方法が面倒

`sphinxcontrib-pandoc-markdown`は、`pandoc`を用いてマークダウンを変換します。
そのため、pandocマークダウンの拡張記法を使用することができます。

`recommonmark`と同様にソースパーサーですので、`rst`と`md`を混在することができるため、設定を細かく指定したい文書は`rst`で、気軽に手早く書きたい場合は`md`で書くなど使い分けが可能です。

## インストール

```console
$ pip install sphinxcontrib-pandoc-markdown
```

### 依存ソフトウェア

- [pandoc](http://pandoc.org/)
- [Graphviz](http://www.graphviz.org/) (グラフ機能を使う場合)
- [PlantUML](http://plantuml.com/) (UML機能を使う場合)
- [mermaid-cli](https://knsv.github.io/mermaid/) (mermaidでHTML以外のビルダーを使う場合)
