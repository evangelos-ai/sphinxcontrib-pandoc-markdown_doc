mermaid.js
=========

```mermaid
graph TD;
    A-->B;
    A-->C;
    B-->D;
    C-->D;
```

````
```mermaid
graph TD;
    A-->B;
    A-->C;
    B-->D;
    C-->D;
```
````

```warning
mermaid.jsの現バージョン(7.0.0)ではCssの設定の関係で画面が半透明になる問題があります。

https://github.com/knsv/mermaid/issues/527
```

### Sphinxの設定

mermaid.jsを使用するには、Sphinx拡張の[sphinxcontrib-mermaid](https://github.com/mgaitan/sphinxcontrib-mermaid)をインストールする必要があります。

```
$ pip install sphinxcontrib-mermaid
```

また、pngやsvgなどの出力形式に対応したい場合は[mermaid-cli](https://www.npmjs.com/package/mermaid-cli)が必要です。

```
$ npm install -g mermaid-cli
```
