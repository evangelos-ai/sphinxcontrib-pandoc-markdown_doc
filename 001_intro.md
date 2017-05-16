# sphinxcontrib-pandoc-markdown

sphinxでマークダウンを扱う場合、`recommonmark`を使う方法があります。
`recommonmark`は拡張次第で数式を扱えたり、reStructuredTextを扱えたりするので非常に便利です。

しかし、以下のような欠点があります。

- CommonMarkが元なのでテーブル記法がない
- 拡張機能の設定方法が面倒

そこで`sphinxcontrib-pandoc-markdown`を作成しました。
`pandoc`を使ってマークダウンを変換するのでpandocマークダウンの拡張記法が使えます。

また、`rst`と`md`が混在できるので、設定を細かく指定したい文書は`rst`で、気軽に手早く書きたい場合は`md`で書くなど使い分けが可能になります。

## インストール

```
$ pip install sphinxcontrib-pandoc-markdown
```

### 依存ソフトウェア

- [pandoc](http://pandoc.org/)
- graphviz (グラフ機能を使う場合)
- PlantUML (UML機能を使う場合)
