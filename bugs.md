既知のバグ
=========

### 図/画像が並列に並んでしまう

```viz
digraph graph_name {
  alpha;
  beta;
  alpha -> beta;
}
```

```viz
digraph graph_name {
  alpha;
  beta;
  alpha -> beta;
}
```

````
```viz
digraph graph_name {
  alpha;
  beta;
  alpha -> beta;
}
```
```viz
digraph graph_name {
  alpha;
  beta;
  alpha -> beta;
}
```
````

図を２つ並べると改行されない、これはバグよりも仕様である。

#### 解決案候補 A

このままの仕様

#### 解決案候補 B

コードブロックの仕様通りに改行させるが、中央寄せにする。


## 画像直後に改行を入れないとテキストが回り込みになる

![](imports/thunder.png)
テキストが回り込みになります。

改行を入れれば回り込みは解除される。

## 引用問題

pandocの変換にて、次のマークダウンは正確に変換されない。

````
```
コードブロック
```

> 引用文
````

```
コードブロック
```

> 引用文

コードブロックに引用文が統合されています、こちらはpandocの出力バグのようです。
pandoc2.0で修正予定。
