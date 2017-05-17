Sphinx独自拡張
=============

## reStructuredTextの埋め込み

````
``` eval_rst
* This is a bulleted list.
* It has two items, the second
  item uses two lines.
```
````

``` eval_rst
* This is a bulleted list.
* It has two items, the second
  item uses two lines.
```

## ノート、警告、参照

````
```note
This is note.
```

```warning
This is warning.
```

```seealso
This is seealso
```
````

```note
This is note.
```

```warning
This is warning.
```

```seealso
This is seealso
```


## TODO

````
```todo
This is todo.
```
````

```todo
This is todo.
```

#### TODOリスト

````
```todolist
```
````

```todolist
```

#### Sphinxの設定

TODOを使用するには`conf.py`に以下の記述を追加してください。

```python
extensions += ['sphinx.ext.todo']

todo_include_todos=True
```

## pull-quote、highlights

````
```pull-quote
This is pull-quote
```
````

```pull-quote
This is pull-quote
```

````
```highlights
This is highlights
```
````

```highlights
This is highlights
```

## epigraph
````
```epigraph
考えるな、感じろ ”Don't think. Feel!”

-- 映画「ドラゴン怒りの鉄拳」より
```
````

```epigraph
考えるな、感じろ ”Don't think. Feel!”

-- 映画「ドラゴン怒りの鉄拳」より
```
